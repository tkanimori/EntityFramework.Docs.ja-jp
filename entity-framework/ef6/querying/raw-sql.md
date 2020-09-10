---
title: 生の SQL クエリ-EF6
description: Entity Framework 6 の生の SQL クエリ
author: divega
ms.date: 10/23/2016
ms.assetid: 9e1ee76e-2499-408c-81e8-9b6c5d1945a0
uid: ef6/querying/raw-sql
ms.openlocfilehash: 0db5069b23ff9aa65ced2dbe48694e4f20c98e51
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2020
ms.locfileid: "89620278"
---
# <a name="raw-sql-queries-ef6"></a>生の SQL クエリ (EF6)

Entity Framework を使用すると、エンティティクラスで LINQ を使用してクエリを実行できます。 ただし、生の SQL を使用してデータベースに対して直接クエリを実行することが必要になる場合があります。 これにはストアドプロシージャの呼び出しが含まれます。これは、ストアドプロシージャへのマッピングを現在サポートしていない Code First モデルに役立ちます。 このトピックで紹介するテクニックは、Code First および EF Designer で作成されたモデルに等しく使用できます。  

## <a name="writing-sql-queries-for-entities"></a>エンティティに対する SQL クエリの記述  

DbSet の SqlQuery メソッドを使用すると、エンティティインスタンスを返す生の SQL クエリを記述できます。 返されたオブジェクトは、LINQ クエリによって返された場合と同様に、コンテキストによって追跡されます。 次に例を示します。  

``` csharp  
using (var context = new BloggingContext())
{
    var blogs = context.Blogs.SqlQuery("SELECT * FROM dbo.Blogs").ToList();
}
```  

LINQ クエリの場合と同様に、クエリは結果が列挙されるまで実行されないことに注意してください。上記の例では、これは ToList の呼び出しを使用して行われます。  

生の SQL クエリが2つの理由で書き込まれるたびに、注意が必要です。 最初に、要求された型のエンティティのみが返されるようにクエリを記述する必要があります。 たとえば、継承などの機能を使用する場合、誤った CLR 型のエンティティを作成するクエリを記述するのは簡単です。  

次に、一部の種類の未加工の SQL クエリは、特に SQL インジェクション攻撃に関連する潜在的なセキュリティリスクを露呈します。 このような攻撃から保護するために、クエリでパラメーターを正しい方法で使用していることを確認してください。  

### <a name="loading-entities-from-stored-procedures"></a>ストアドプロシージャからのエンティティの読み込み  

DbSet SqlQuery を使用して、ストアドプロシージャの結果からエンティティを読み込むことができます。 たとえば、次のコードは dbo を呼び出します。データベース内の GetBlogs プロシージャ:  

``` csharp
using (var context = new BloggingContext())
{
    var blogs = context.Blogs.SqlQuery("dbo.GetBlogs").ToList();
}
```  

次の構文を使用して、ストアドプロシージャにパラメーターを渡すこともできます。  

``` csharp
using (var context = new BloggingContext())
{
    var blogId = 1;

    var blogs = context.Blogs.SqlQuery("dbo.GetBlogById @p0", blogId).Single();
}
```  

## <a name="writing-sql-queries-for-non-entity-types"></a>非エンティティ型に対する SQL クエリの記述  

プリミティブ型を含む任意の型のインスタンスを返す SQL クエリは、データベースクラスの SqlQuery メソッドを使用して作成できます。 次に例を示します。  

``` csharp
using (var context = new BloggingContext())
{
    var blogNames = context.Database.SqlQuery<string>(
                       "SELECT Name FROM dbo.Blogs").ToList();
}
```  

オブジェクトがエンティティ型のインスタンスであっても、データベースに対して SqlQuery から返された結果はコンテキストによって追跡されません。  

## <a name="sending-raw-commands-to-the-database"></a>生のコマンドをデータベースに送信する  

クエリ以外のコマンドは、データベースで ExecuteSqlCommand メソッドを使用してデータベースに送信できます。 次に例を示します。  

``` csharp
using (var context = new BloggingContext())
{
    context.Database.ExecuteSqlCommand(
        "UPDATE dbo.Blogs SET Name = 'Another Name' WHERE BlogId = 1");
}
```  

ExecuteSqlCommand を使用してデータベース内のデータに加えられた変更は、エンティティがデータベースから読み込まれるか、データベースから再読み込みされるまでコンテキストに対して不透明になります。  

### <a name="output-parameters"></a>出力パラメーター  

出力パラメーターが使用されている場合、結果が完全に読み取られるまで、値は使用できません。 これは、DbDataReader の基になる動作に起因します。詳細については、「 [DataReader を使用したデータの取得](https://go.microsoft.com/fwlink/?LinkID=398589) 」を参照してください。  
