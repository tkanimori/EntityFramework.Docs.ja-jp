---
title: プロキシ - EF6 の操作
author: divega
ms.date: 10/23/2016
ms.assetid: 869ee4dc-06f1-471d-8e0e-0a1a2bc59c30
ms.openlocfilehash: 8f7d2e8b41ece28efe8d1df3b0679e6e4510d64a
ms.sourcegitcommit: 2b787009fd5be5627f1189ee396e708cd130e07b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2018
ms.locfileid: "45489818"
---
# <a name="working-with-proxies"></a>プロキシの操作
POCO エンティティ型のインスタンスを作成するときに Entity Framework は多くの場合、動的に生成されたエンティティのプロキシとして機能する派生型のインスタンスを作成します。 このプロキシは、プロパティにアクセスするときにアクションを自動的に実行するためのフックを挿入するエンティティの一部の仮想プロパティをオーバーライドします。 たとえば、このメカニズムは、リレーションシップの遅延読み込みをサポートに使用されます。 このトピックで紹介するテクニックは、Code First および EF Designer で作成されたモデルに等しく使用できます。  

## <a name="disabling-proxy-creation"></a>プロキシの作成を無効にします。  

Entity Framework がプロキシのインスタンスを作成するを防ぐために便利な場合があります。 たとえば、プロキシではないインスタンスをシリアル化はプロキシ インスタンスをシリアル化するよりもはるかに簡単にします。 ProxyCreationEnabled フラグをオフにすると、プロキシの作成をオフにすることができます。 これを行うことが 1 つの場所のコンテキストのコンス トラクター内です。 例えば:  

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

EF によって型プロキシは作成されないことに注意してください。 何を行うプロキシを使用する必要がある場合。 つまり、型はシールされてや仮想プロパティを持っていないをすることでプロキシも回避できます。  

## <a name="explicitly-creating-an-instance-of-a-proxy"></a>プロキシのインスタンスを明示的に作成します。  

新しい演算子を使用してエンティティのインスタンスを作成する場合、プロキシ インスタンスは作成されません。 問題ないことが考えられますが、(たとえば、遅延読み込みまたはプロキシ変更の追跡が動作するよう) プロキシ インスタンスを作成する必要がある場合、行うことができます、DbSet の Create メソッドを使用します。 例えば:  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Create();
}
```  

ジェネリック バージョンを作成するは、派生エンティティ型のインスタンスを作成する場合に使用できます。 例えば:  

``` csharp
using (var context = new BloggingContext())
{
    var admin = context.Users.Create<Administrator>();
}
```  

Create メソッドを追加または作成されたエンティティをコンテキストにアタッチはいないことに注意してください。  

エントリのエンティティのプロキシ型を作成する場合、Create メソッドは、エンティティ型自体のインスタンスが作成だけはありません値それとは何も実行するため。 たとえば、エンティティ型がシールされている場合や作成は、仮想プロパティを持たない場合は、エンティティ型のインスタンスを作成します。  

## <a name="getting-the-actual-entity-type-from-a-proxy-type"></a>プロキシの種類からの実際のエンティティ型の取得  

プロキシ型は、このようなものを検索する名前を持ちます。  

System.Data.Entity.DynamicProxies.Blog_5E43C6C196972BF0754973E48C9C941092D86818CD94005E9A759B70BF6E48E6  

このプロキシ型が ObjectContext から GetObjectType メソッドを使用してエンティティ型が表示されます。 例えば:  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    var entityType = ObjectContext.GetObjectType(blog.GetType());
}
```  

GetObjectType に渡される型の場合は、プロキシ型のエンティティ型ではないエンティティ型のインスタンスが返されます。 つまり、実際のエンティティ型を取得するか、型がプロキシ型の場合に表示するその他のチェックなしにこのメソッドを常に使用することができます。  
