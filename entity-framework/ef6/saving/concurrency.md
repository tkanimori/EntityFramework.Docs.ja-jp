---
title: 同時実行の競合の EF6 の処理
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: 2318e4d3-f561-4720-bbc3-921556806476
caps.latest.revision: 3
ms.openlocfilehash: b8608dbb4cadd60ff4ff4984583f8a9d843b3949
ms.sourcegitcommit: f05e7b62584cf228f17390bb086a61d505712e1b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2018
ms.locfileid: "39121835"
---
# <a name="handling-concurrency-conflicts"></a>同時実行の競合の処理
オプティミスティック同時実行は共有的では、エンティティをエンティティからデータが変更されていないことを期待してデータベースに保存しようとしてが読み込まれました。 わかった場合、例外がスローされ、再度保存する前に、競合を解決する必要がありますし、データが変更されました。 このトピックでは、Entity Framework では、このような例外を処理する方法について説明します。 このトピックで紹介するテクニックは、Code First および EF Designer で作成されたモデルに等しく使用できます。  

この投稿はオプティミスティック同時実行制御の詳細については、適切な場所ではありません。 以下のセクションでは、同時実行の解像度の知識を前提としていて、一般的なタスクのパターンを表示します。  

これらのパターンの多くで説明したトピックの使用[プロパティの値を操作](~/ef6/saving/change-tracking/property-values.md)します。  

使用している独立した関連付け (場所、外部キーにマップされていないエンティティのプロパティ) と、同時実行の問題を解決するは外部キー アソシエーションを使用する場合よりもはるかに難しくなります。 そのため、アプリケーションで同時実行の解決を行う場合に、常に、エンティティに外部キーをマップすることをお勧めします。 次の例では、外部キー アソシエーションを使用していることを前提としています。  

外部キー アソシエーションを使用するエンティティを保存しようとしているときに、オプティミスティック同時実行例外が検出されたときに、DbUpdateConcurrencyException が SaveChanges によってスローされます。  

## <a name="resolving-optimistic-concurrency-exceptions-with-reload-database-wins"></a>再読み込み (データベース wins) とオプティミスティック同時実行例外を解決します。  

データベースの値で、エンティティの現在の値を上書きする Reload メソッドを使用できます。 エンティティは通常に渡され、戻るなんらかの形でユーザーと、もう一度その変更を加えるし、再度保存しようとする必要があります。 例えば:  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    blog.Name = "The New ADO.NET Blog";

    bool saveFailed;
    do
    {
        saveFailed = false;

        try
        {
            context.SaveChanges();
        }
        catch (DbUpdateConcurrencyException ex)
        {
            saveFailed = true;

            // Update the values of the entity that failed to save from the store
            ex.Entries.Single().Reload();
        }

    } while (saveFailed);
}
```  

同時実行例外をシミュレートするためには、SaveChanges の呼び出しにブレークポイントを設定し、SQL Management Studio などの他のツールを使用してデータベースに保存されているエンティティを変更します。 SaveChanges SqlCommand を直接使用してデータベースを更新する前に行を挿入することもできます。 例えば:  

``` csharp
context.Database.SqlCommand(
    "UPDATE dbo.Blogs SET Name = 'Another Name' WHERE BlogId = 1");
```  

DbUpdateConcurrencyException のエントリ メソッドでは、更新に失敗したエンティティの対象の DbEntityEntry インスタンスを返します。 (現在常に返します同時実行の問題の 1 つの値。 これは値を返す複数の一般的な更新プログラムの例外の。)これらの各呼び出しが再読み込みやデータベースから再読み込みする必要があるすべてのエンティティのエントリを取得する代わりに適さない場合があります。  

## <a name="resolving-optimistic-concurrency-exceptions-as-client-wins"></a>クライアント側に合わせると、オプティミスティック同時実行例外を解決します。  

再読み込みを使用する上記の例が、データベースの wins とも呼ばれます。 または store wins エンティティ内の値がデータベースからの値によって上書きされるためです。 逆を行うし、エンティティの現在の値で、データベース内の値を上書きするたい場合があります。 これは、クライアント側に合わせるとも呼ばれますが、データベースの現在の値を取得して、エンティティの元の値として設定して実行できます。 (を参照してください[プロパティの値を操作](~/ef6/saving/change-tracking/property-values.md)現在と元の値の詳細について)。例えば:  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    blog.Name = "The New ADO.NET Blog";

    bool saveFailed;
    do
    {
        saveFailed = false;
        try
        {
            context.SaveChanges();
        }
        catch (DbUpdateConcurrencyException ex)
        {
            saveFailed = true;

            // Update original values from the database
            var entry = ex.Entries.Single();
            entry.OriginalValues.SetValues(entry.GetDatabaseValues());
        }

    } while (saveFailed);
}
```  

## <a name="custom-resolution-of-optimistic-concurrency-exceptions"></a>オプティミスティック同時実行例外のカスタム解決  

場合によって、エンティティの現在の値と、データベースの現在の値を組み合わせるしたい場合があります。 これには、カスタム ロジックやユーザー操作がいくつかは、通常必要があります。 たとえば、フォームを現在の値は、データベース内の値を格納しているユーザーに表示可能性があり、既定値に解決される値の設定。 ユーザーは必要に応じて、解決された値を編集し、データベースに保存されるこれらの解決された値になります。 これ行うエンティティのエントリで CurrentValues とではから返される DbPropertyValues オブジェクトを使用します。 例えば:  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    blog.Name = "The New ADO.NET Blog";

    bool saveFailed;
    do
    {
        saveFailed = false;
        try
        {
            context.SaveChanges();
        }
        catch (DbUpdateConcurrencyException ex)
        {
            saveFailed = true;

            // Get the current entity values and the values in the database
            var entry = ex.Entries.Single();
            var currentValues = entry.CurrentValues;
            var databaseValues = entry.GetDatabaseValues();

            // Choose an initial set of resolved values. In this case we
            // make the default be the values currently in the database.
            var resolvedValues = databaseValues.Clone();

            // Have the user choose what the resolved values should be
            HaveUserResolveConcurrency(currentValues, databaseValues, resolvedValues);

            // Update the original values with the database values and
            // the current values with whatever the user choose.
            entry.OriginalValues.SetValues(databaseValues);
            entry.CurrentValues.SetValues(resolvedValues);
        }
    } while (saveFailed);
}

public void HaveUserResolveConcurrency(DbPropertyValues currentValues,
                                       DbPropertyValues databaseValues,
                                       DbPropertyValues resolvedValues)
{
    // Show the current, database, and resolved values to the user and have
    // them edit the resolved values to get the correct resolution.
}
```  

## <a name="custom-resolution-of-optimistic-concurrency-exceptions-using-objects"></a>オブジェクトを使用してオプティミスティック同時実行例外のカスタム解決  

上記のコードでは、現在を受け渡す、データベース、および解決される値の DbPropertyValues インスタンスを使用します。 このエンティティ型のインスタンスを使いやすい場合があります。 これ行う DbPropertyValues の ToObject や SetValues メソッドを使用します。 例えば:  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    blog.Name = "The New ADO.NET Blog";

    bool saveFailed;
    do
    {
        saveFailed = false;
        try
        {
            context.SaveChanges();
        }
        catch (DbUpdateConcurrencyException ex)
        {
            saveFailed = true;

            // Get the current entity values and the values in the database
            // as instances of the entity type
            var entry = ex.Entries.Single();
            var databaseValues = entry.GetDatabaseValues();
            var databaseValuesAsBlog = (Blog)databaseValues.ToObject();

            // Choose an initial set of resolved values. In this case we
            // make the default be the values currently in the database.
            var resolvedValuesAsBlog = (Blog)databaseValues.ToObject();

            // Have the user choose what the resolved values should be
            HaveUserResolveConcurrency((Blog)entry.Entity,
                                       databaseValuesAsBlog,
                                       resolvedValuesAsBlog);

            // Update the original values with the database values and
            // the current values with whatever the user choose.
            entry.OriginalValues.SetValues(databaseValues);
            entry.CurrentValues.SetValues(resolvedValuesAsBlog);
        }

    } while (saveFailed);
}

public void HaveUserResolveConcurrency(Blog entity,
                                       Blog databaseValues,
                                       Blog resolvedValues)
{
    // Show the current, database, and resolved values to the user and have
    // them update the resolved values to get the correct resolution.
}
```  
