---
title: プロキシの使用-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 869ee4dc-06f1-471d-8e0e-0a1a2bc59c30
ms.openlocfilehash: 8f7d2e8b41ece28efe8d1df3b0679e6e4510d64a
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78415989"
---
# <a name="working-with-proxies"></a>プロキシの使用
POCO エンティティ型のインスタンスを作成する場合、Entity Framework は、多くの場合、エンティティのプロキシとして機能する動的に生成された派生型のインスタンスを作成します。 このプロキシは、プロパティにアクセスしたときにアクションを自動的に実行するフックを挿入するために、エンティティの一部の仮想プロパティをオーバーライドします。 たとえば、このメカニズムは、リレーションシップの遅延読み込みをサポートするために使用されます。 このトピックで紹介するテクニックは、Code First および EF Designer で作成されたモデルに等しく使用できます。  

## <a name="disabling-proxy-creation"></a>プロキシ作成の無効化  

Entity Framework がプロキシインスタンスを作成しないようにすると便利な場合があります。 たとえば、プロキシインスタンスをシリアル化するよりも、プロキシ以外のインスタンスをシリアル化する方がはるかに簡単です。 プロキシの作成は、Proxyの有効化フラグをオフにすることによって無効にすることができます。 これを実行できる1つの方法は、コンテキストのコンストラクターです。 次に例を示します。  

``` csharp
public class BloggingContext : DbContext
{
    public BloggingContext()
    {
        this.Configuration.ProxyCreationEnabled = false;
    }  

    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
}
```  

EF では、プロキシが何も実行しない型に対してプロキシを作成しないことに注意してください。 これは、シールされた型または仮想プロパティを持たない型を使用してプロキシを回避することもできます。  

## <a name="explicitly-creating-an-instance-of-a-proxy"></a>プロキシのインスタンスを明示的に作成する  

New 演算子を使用してエンティティのインスタンスを作成した場合、プロキシインスタンスは作成されません。 これは問題になることはありませんが、プロキシインスタンスを作成する必要がある場合 (たとえば、遅延読み込みやプロキシ変更の追跡が機能するようにする場合)、DbSet の Create メソッドを使用して実行できます。 次に例を示します。  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Create();
}
```  

派生エンティティ型のインスタンスを作成する場合は、ジェネリックバージョンの Create を使用できます。 次に例を示します。  

``` csharp
using (var context = new BloggingContext())
{
    var admin = context.Users.Create<Administrator>();
}
```  

Create メソッドでは、作成されたエンティティがコンテキストに追加またはアタッチされないことに注意してください。  

Create メソッドは、エンティティ型自体のインスタンスを作成するだけであることに注意してください。エンティティのプロキシ型を作成すると、何も実行されないため、値がないことに注意してください。 たとえば、エンティティ型が sealed であるか、またはに仮想プロパティがない場合、Create はエンティティ型のインスタンスを作成するだけです。  

## <a name="getting-the-actual-entity-type-from-a-proxy-type"></a>プロキシ型からの実際のエンティティ型の取得  

プロキシ型には、次のような名前が付いています。  

System.object.... Blog_5E43C6C196972BF0754973E48C9C941092D86818CD94005E9A759B70BF6E48E6  

このプロキシ型のエンティティ型は、ObjectContext の GetObjectType メソッドを使用して見つけることができます。 次に例を示します。  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    var entityType = ObjectContext.GetObjectType(blog.GetType());
}
```  

GetObjectType に渡される型が、プロキシ型ではないエンティティ型のインスタンスである場合、エンティティの型は引き続き返されることに注意してください。 つまり、このメソッドを使用して、型がプロキシ型であるかどうかを確認する他のチェックを行わずに、実際のエンティティ型を取得することができます。  
