---
title: プロキシの使用-EF6
description: Entity Framework 6 でのプロキシの使用
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/fundamentals/proxies
ms.openlocfilehash: de44e891c074a8811e466d040f6451bea0f950f4
ms.sourcegitcommit: 4860d036ea0fb392c28799907bcc924c987d2d7b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/17/2020
ms.locfileid: "97635849"
---
# <a name="working-with-proxies"></a>プロキシの使用
POCO エンティティ型のインスタンスを作成する場合、Entity Framework は、多くの場合、エンティティのプロキシとして機能する動的に生成された派生型のインスタンスを作成します。 このプロキシは、プロパティにアクセスしたときにアクションを自動的に実行するフックを挿入するために、エンティティの一部の仮想プロパティをオーバーライドします。 たとえば、このメカニズムは、リレーションシップの遅延読み込みをサポートするために使用されます。 このトピックで紹介するテクニックは、Code First および EF Designer で作成されたモデルに等しく使用できます。  

## <a name="disabling-proxy-creation"></a>プロキシ作成の無効化  

Entity Framework がプロキシインスタンスを作成しないようにすると便利な場合があります。 たとえば、プロキシインスタンスをシリアル化するよりも、プロキシ以外のインスタンスをシリアル化する方がはるかに簡単です。 プロキシの作成は、フラグをオフにすることによって無効にすることができ `ProxyCreationEnabled` ます。 これを実行できる1つの方法は、コンテキストのコンストラクターです。 次に例を示します。  

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

New 演算子を使用してエンティティのインスタンスを作成した場合、プロキシインスタンスは作成されません。 これは問題になるとは限りませんが、プロキシインスタンスを作成する必要がある場合 (たとえば、遅延読み込みやプロキシの変更の追跡が機能するようにする場合) は、のメソッドを使用してこれを行うことができ `Create` `DbSet` ます。 次に例を示します。  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Create();
}
```  

派生エンティティ型のインスタンスを作成する場合は、ジェネリックバージョンのを `Create` 使用できます。 次に例を示します。  

``` csharp
using (var context = new BloggingContext())
{
    var admin = context.Users.Create<Administrator>();
}
```  

メソッドでは、 `Create` 作成されたエンティティをコンテキストに追加したり、アタッチしたりすることはありません。  

このメソッドは、エンティティ型自体のインスタンスを作成するだけであることに注意してください `Create` 。エンティティのプロキシ型を作成すると、何も実行されないため、値がないことに注意してください。 たとえば、エンティティ型が sealed であるか、またはに仮想プロパティがない場合、 `Create` はエンティティ型のインスタンスを作成するだけです。  

## <a name="getting-the-actual-entity-type-from-a-proxy-type"></a>プロキシ型からの実際のエンティティ型の取得  

プロキシ型には、次のような名前が付いています。  

```
System.Data.Entity.DynamicProxies.Blog_5E43C6C196972BF0754973E48C9C941092D86818CD94005E9A759B70BF6E48E6
```

このプロキシ型のエンティティ型は、のメソッドを使用して見つけることができ `GetObjectType` `ObjectContext` ます。 次に例を示します。  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    var entityType = ObjectContext.GetObjectType(blog.GetType());
}
```  

に渡される型 `GetObjectType` が、プロキシ型ではないエンティティ型のインスタンスである場合、エンティティの型は引き続き返されることに注意してください。 つまり、このメソッドを使用して、型がプロキシ型であるかどうかを確認する他のチェックを行わずに、実際のエンティティ型を取得することができます。  
