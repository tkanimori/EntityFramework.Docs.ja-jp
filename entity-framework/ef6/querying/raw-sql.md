---
title: 生 SQL クエリ - EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 9e1ee76e-2499-408c-81e8-9b6c5d1945a0
ms.openlocfilehash: 6b00648939ccedffeed09b4e1d6e8d70fa262a36
ms.sourcegitcommit: 2b787009fd5be5627f1189ee396e708cd130e07b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2018
ms.locfileid: "45490585"
---
# <a name="raw-sql-queries"></a>生 SQL クエリ
Entity Framework エンティティ クラスで LINQ を使用してクエリを実行することができます。 ただし、データベースに対して直接 SQL そのものを使用してクエリを実行する時間があります。 これは、Code First モデルの現在のストアド プロシージャへのマッピングをサポートしていないことができますが、ストアド プロシージャの呼び出しが含まれます。 このトピックで紹介するテクニックは、Code First および EF Designer で作成されたモデルに等しく使用できます。  

## <a name="writing-sql-queries-for-entities"></a>エンティティの SQL クエリの記述  

DbSet に SqlQuery メソッドは、エンティティのインスタンスを返す書き込まれる生 SQL クエリを使用します。 れる LINQ クエリが返されたかどうかと同様、返されたオブジェクトがコンテキストによって追跡されます。 例えば:  

``` csharp  
using (var context = new BloggingContext())
{
    var blogs = context.Blogs.SqlQuery("SELECT * FROM dbo.Blogs").ToList();
}
```  

LINQ クエリと同様、クエリが実行されない結果が列挙されるまでに注意してください: ToList への呼び出しで上記の例で実行されます。  

生 SQL クエリが 2 つの理由に書き込まれるたびに、十分に注意してください。 最初に、実際には、要求された型のエンティティのみが返されることを確認するクエリを記述する必要があります。 たとえば、継承などの機能を使用する場合が正しくない CLR 型のエンティティを作成するクエリを記述する簡単です。  

次に、生の SQL クエリの種類によっては、SQL インジェクション攻撃の特にの潜在的なセキュリティ リスクを公開します。 このような攻撃から保護するための適切な方法で、クエリでパラメーターを使用することを確認します。  

### <a name="loading-entities-from-stored-procedures"></a>ストアド プロシージャからのエンティティの読み込み  

DbSet.SqlQuery を使用して、ストアド プロシージャの結果からエンティティを読み込むことができます。 たとえば、次のコードは、dbo を呼び出します。データベースの GetBlogs プロシージャ:  

``` csharp
using (var context = new BloggingContext())
{
    var blogs = context.Blogs.SqlQuery("dbo.GetBlogs").ToList();
}
```  

次の構文を使用してストアド プロシージャにパラメーターを渡すこともできます。  

``` csharp
using (var context = new BloggingContext())
{
    var blogId = 1;

    var blogs = context.Blogs.SqlQuery("dbo.GetBlogById @p0", blogId).Single();
}
```  

## <a name="writing-sql-queries-for-non-entity-types"></a>エンティティ型以外の SQL クエリの作成  

データベース クラスの SqlQuery メソッドを使用して、プリミティブ型を含む、任意の型のインスタンスを返す SQL クエリを作成できます。 例えば:  

``` csharp
using (var context = new BloggingContext())
{
    var blogNames = context.Database.SqlQuery<string>(
                       "SELECT Name FROM dbo.Blogs").ToList();
}
```  

オブジェクトのエンティティ型のインスタンスである場合でも、データベースで SqlQuery から返される結果は、コンテキストによって追跡ことはありません。  

## <a name="sending-raw-commands-to-the-database"></a>生のコマンドをデータベースに送信します。  

非クエリ コマンドは、データベースで ExecuteSqlCommand メソッドを使用してデータベースに送信できます。 例えば:  

``` csharp
using (var context = new BloggingContext())
{
    context.Database.ExecuteSqlCommand(
        "UPDATE dbo.Blogs SET Name = 'Another Name' WHERE BlogId = 1");
}
```  

エンティティが読み込まれたまたは、データベースから再読み込みされるまで ExecuteSqlCommand を使用して、データベース内のデータに加えられた変更は、コンテキストに不透明なことに注意してください。  

### <a name="output-parameters"></a>出力パラメーター  

出力パラメーターを使用している場合は、結果が完全に読み取られるまでに、その値は使用できません。 これは、DbDataReader の基になる動作によるものを参照してください[DataReader によるデータの取得](http://go.microsoft.com/fwlink/?LinkID=398589)の詳細。  
