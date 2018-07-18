---
title: EF6 の DbSets を定義します。
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: 4528a509-ace7-4dfb-8065-1b833f5e03a0
caps.latest.revision: 3
ms.openlocfilehash: 8a495c6ce74d9a346a84b0e10fb28395f4dce07b
ms.sourcegitcommit: 00cb52625b57c1ea339ded1454179fe89b6bcfea
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2018
ms.locfileid: "39123269"
---
# <a name="defining-dbsets"></a>DbSets を定義します。
Code First ワークフローを開発する際に、データベースとのセッションを表し、モデル内の各型の DbSet を公開する DbContext 派生を定義します。 このトピックでは、DbSet プロパティを定義するさまざまな方法を説明します。  

## <a name="dbcontext-with-dbset-properties"></a>DbContext の DbSet プロパティ  

最初のコード例に示すように、一般的なケースは、モデルのエンティティ型のパブリックの自動 DbSet プロパティを DbContext です。 例えば:  

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
}
```  

Code First モードで使用する場合、エンティティ型だけから到達可能なその他の種類の構成として、ブログや投稿に設定されます。 さらに DbContext では、適切な DbSet のインスタンスを設定するには、各プロパティの setter は呼び出しが自動的に。  

## <a name="dbcontext-with-idbset-properties"></a>DbContext IDbSet プロパティを持つ  

ときに作成するモックまたは fakes、インターフェイスを使用して、セットのプロパティの宣言をより便利ななどの場合があります。 このような場合、IDbSet DbSet の代わりにインターフェイスを使用することができます。 例えば:  

``` csharp
public class BloggingContext : DbContext
{
    public IDbSet<Blog> Blogs { get; set; }
    public IDbSet<Post> Posts { get; set; }
}
```  

このコンテキストは、そのプロパティの設定の DbSet クラスを使用するコンテキストとまったく同じ方法で動作します。  

## <a name="dbcontext-with-read-only-set-properties"></a>プロパティを設定する読み取り専用の DbContext  

DbSet または IDbSet のプロパティのパブリック setter を公開したくない場合代わりに読み取り専用プロパティを作成し、自分で、セットのインスタンスを作成することができます。 例えば:  

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs
    {
        get { return Set<Blog>(); }
    }

    public DbSet<Post> Posts
    {
        get { return Set<Post>(); }
    }
}
```  

DbContext はそれが呼び出されるたびに、同じインスタンスがこれらの各プロパティが返されるように、Set メソッドから返された DbSet のインスタンスをキャッシュに注意してください。  

ここで、同じ方法での Code First のしくみのエンティティ型の検出ほどには、パブリック getter および setter プロパティは。  
