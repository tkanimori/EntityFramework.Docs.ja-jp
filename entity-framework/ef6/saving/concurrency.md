---
title: 同時実行の競合の処理-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 2318e4d3-f561-4720-bbc3-921556806476
ms.openlocfilehash: 4d29fd7a4d9b6003f71bc8411cea2d863a4c5429
ms.sourcegitcommit: d85263b5d5d665dbaf94de8832e2917bce048b34
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2020
ms.locfileid: "86451243"
---
# <a name="handling-concurrency-conflicts-ef6"></a>同時実行の競合の処理 (EF6)

オプティミスティック同時実行制御では、エンティティが読み込まれてからデータが変更されていないことを期待して、エンティティをデータベースに保存しようとしています。 データが変更されたことが判明した場合は、例外がスローされるので、再度保存する前に競合を解決する必要があります。 このトピックでは、Entity Framework でこのような例外を処理する方法について説明します。 このトピックで紹介するテクニックは、Code First および EF Designer で作成されたモデルに等しく使用できます。  

この投稿は、オプティミスティック同時実行制御を完全に説明するための適切な場所ではありません。 以下のセクションでは、同時実行の解決についての知識を前提とし、一般的なタスクのパターンを示します。  

これらのパターンの多くは、「[プロパティ値の操作](~/ef6/saving/change-tracking/property-values.md)」で説明されているトピックを使用します。  

独立した関連付け (外部キーがエンティティのプロパティにマップされていない場合) を使用する場合の同時実行の問題の解決は、外部キーの関連付けを使用する場合よりもはるかに困難です。 したがって、アプリケーションで同時実行の解決を行う場合は、常に外部キーをエンティティにマップすることをお勧めします。 以下のすべての例では、外部キーの関連付けを使用していることを前提としています。  

外部キーの関連付けを使用するエンティティを保存しようとしているときに、オプティミスティック同時実行制御の例外が検出されると、DbUpdateConcurrencyException が SaveChanges によってスローされます。  

## <a name="resolving-optimistic-concurrency-exceptions-with-reload-database-wins"></a>再読み込みによるオプティミスティック同時実行例外の解決 (データベースの優先)  

再読み込みメソッドを使用して、エンティティの現在の値をデータベース内の現在の値で上書きすることができます。 エンティティは通常、何らかの形でユーザーに戻され、再度変更を加えて再保存する必要があります。 次に例を示します。  

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

同時実行例外をシミュレートするには、SaveChanges 呼び出しにブレークポイントを設定し、SQL Server Management Studio などの別のツールを使用してデータベースに保存されているエンティティを変更する方法が適しています。 SaveChanges の前に行を挿入して、SqlCommand を使用してデータベースを直接更新することもできます。 次に例を示します。  

``` csharp
context.Database.SqlCommand(
    "UPDATE dbo.Blogs SET Name = 'Another Name' WHERE BlogId = 1");
```  

DbUpdateConcurrencyException の Entries メソッドは、更新に失敗したエンティティの DbEntityEntry インスタンスを返します。 (現在、このプロパティは、同時実行の問題に対して常に単一の値を返します。 一般的な更新例外に対して複数の値が返される場合があります)。場合によっては、データベースからの再読み込みが必要になる可能性のあるすべてのエンティティのエントリを取得し、それぞれに対して再読み込みを呼び出すこともできます。  

## <a name="resolving-optimistic-concurrency-exceptions-as-client-wins"></a>クライアント優先としてのオプティミスティック同時実行例外の解決  

再読み込みを使用する上記の例は、データベースの値によってエンティティ内の値が上書きされるため、データベースの優先またはストアの優先と呼ばれることがあります。 場合によっては、逆の処理を行い、現在エンティティ内にある値を使用してデータベースの値を上書きすることが必要になることがあります。 これは、クライアント優先とも呼ばれ、現在のデータベース値を取得し、エンティティの元の値として設定することによって実行できます。 (現在の値と元の値の詳細については、「[プロパティ値の使用](~/ef6/saving/change-tracking/property-values.md)」を参照してください)。例えば：  

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

場合によっては、現在データベース内にある値を、現在エンティティ内にある値と組み合わせる必要があります。 これには通常、カスタムロジックまたはユーザーの操作が必要です。 たとえば、現在の値、データベース内の値、および解決済みの値の既定のセットを含むフォームをユーザーに提示することができます。 その後、ユーザーは必要に応じて解決済みの値を編集し、データベースに保存される解決済みの値になります。 これは、エンティティのエントリの CurrentValues および GetDatabaseValues から返された DbPropertyValues オブジェクトを使用して行うことができます。 次に例を示します。  

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

## <a name="custom-resolution-of-optimistic-concurrency-exceptions-using-objects"></a>オブジェクトを使用したオプティミスティック同時実行例外のカスタム解決  

上記のコードでは、DbPropertyValues インスタンスを使用して、現在、データベース、および解決済みの値を渡しています。 このために、エンティティ型のインスタンスを使用する方が簡単な場合があります。 これは、DbPropertyValues の ToObject および SetValues メソッドを使用して行うことができます。 次に例を示します。  

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
