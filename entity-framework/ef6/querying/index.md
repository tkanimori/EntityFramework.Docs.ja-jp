---
title: エンティティのクエリおよび検索 - EF6
author: divega
ms.date: 2016-10-23
ms.assetid: 65bb3db2-2226-44af-8864-caa575cf1b46
ms.openlocfilehash: 19e70bc5bcfdd0c81186c6139661395ebb1ee61f
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42993282"
---
# <a name="querying-and-finding-entities"></a>エンティティのクエリおよび検索
このトピックでは、Entity Framework を使用した、LINQ および Find メソッドを含むデータのさまざまなクエリ方法について説明します。 このトピックで紹介するテクニックは、Code First および EF Designer で作成されたモデルに等しく使用できます。  

## <a name="finding-entities-using-a-query"></a>クエリを使用したエンティティの検索  

DbSet および IDbSet には、IQueryable が実装されているので、データベースに対して LINQ クエリを記述する開始点として使用できます。 ここは LINQ に関して詳細に説明する場所ではありませんが、簡単な例を次にいくつか挙げます。  

``` csharp
using (var context = new BloggingContext())
{
    // Query for all blogs with names starting with B
    var blogs = from b in context.Blogs
                   where b.Name.StartsWith("B")
                   select b;

    // Query for the Blog named ADO.NET Blog
    var blog = context.Blogs
                    .Where(b => b.Name == "ADO.NET Blog")
                    .FirstOrDefault();
}
```  

なお、DbSet および IDbSet は、常にデータベースに対してクエリが作成し、返されたエンティティがコンテキストに既に含まれる場合でも、常にデータベースに対してラウンドトリップを行います。 クエリは次のときにデータベースに対して実行されます。  

- **foreach** (C#) または **For Each** (Visual Basic) ステートメントによって列挙された場合。  
- [ToArray](https://msdn.microsoft.com/library/bb298736)、[ToDictionary](https://msdn.microsoft.com/library/system.linq.enumerable.todictionary)、または [ToList](https://msdn.microsoft.com/library/bb342261) などのコレクション操作によって列挙された場合。  
- [First](https://msdn.microsoft.com/library/bb291976) や [Any](https://msdn.microsoft.com/library/bb337697) などの LINQ 演算子がクエリの最も外側で指定された場合。  
- DbSet の [Load](https://msdn.microsoft.com/library/system.data.entity.dbextensions.load) 拡張メソッド、[DbEntityEntry.Reload](https://msdn.microsoft.com/library/system.data.entity.infrastructure.dbentityentry.reload.aspx)、および Database.ExecuteSqlCommand メソッドが呼び出された場合。  

データベースから結果が返されると、コンテキストに存在しないオブジェクトはコンテキストにアタッチされます。 オブジェクトが既にコンテキストに存在する場合、既存のオブジェクトが返されます (エントリに格納されているオブジェクトのプロパティの現在の値と元の値が、データ ソースの値で上書きされることは**ありません**)。  

データベースにはまだ保存されていないコンテキストに追加されたエンティティは、クエリの実行では結果セットの一部として返されません。 コンテキスト内のデータを取得するには、「[ローカル データ](~/ef6/querying/local-data.md)」を参照してください。  

クエリによってデータベースから行が返されない場合、結果は **null** ではなく空のコレクションになります。  

## <a name="finding-entities-using-primary-keys"></a>主キーを使用したエンティティの検索  

DbSet の Find メソッドは、主キーの値を使用してコンテキストで追跡されているエンティティを検索します。 コンテキストにエンティティがない場合、クエリはデータベースに送られ、そこからエンティティが検索されます。 コンテキストまたはデータベース内にそのエンティティが見つからない場合は null が返されます。  

Find は、次の 2 つの重要な点でクエリを使用する場合とは異なります。  

- データベースに対するラウンドトリップは、特定のキーを持つエンティティがコンテキストにない場合のみ実行されます。  
- Find は、追加された状態のエンティティを返します。 つまり、Find はコンテキストに追加されているけれども、データベースにまだ保存されていないエンティティを返します。  
### <a name="finding-an-entity-by-primary-key"></a>主キーを使用したエンティティの検索  

次のコードでは、Find の用途をいくつか示しています。  

``` csharp
using (var context = new BloggingContext())
{
    // Will hit the database
    var blog = context.Blogs.Find(3);

    // Will return the same instance without hitting the database
    var blogAgain = context.Blogs.Find(3);

    context.Blogs.Add(new Blog { Id = -1 });

    // Will find the new blog even though it does not exist in the database
    var newBlog = context.Blogs.Find(-1);

    // Will find a User which has a string primary key
    var user = context.Users.Find("johndoe1987");
}
```  

### <a name="finding-an-entity-by-composite-primary-key"></a>複合主キーを使用したエンティティの検索  

Entity Framework では、エンティティに複合キー (2 つ以上のプロパティで構成されているキー) が許可されています。 たとえば、特定のブログのユーザー設定を表す BlogSettings エンティティがあるとします。 ユーザーは、1 つのブログで BlogSettings を 1 つしか持つことができないので、BlogSettings の主キーを BlogId と Username の組み合わせにすることができます。 次のコードでは、BlogId = 3 と Username = "johndoe1987" の BlogSettings の検索を試行します。  

``` csharp  
using (var context = new BloggingContext())
{
    var settings = context.BlogSettings.Find(3, "johndoe1987");
}
```  

なお、複合キーがある場合、ColumnAttribute または fluent API を使用して、複合キーのプロパティの順序を指定する必要があります。 Find に対する呼び出しでは、そのキーを形成する値を指定するときに、この順序を使用する必要があります。  
