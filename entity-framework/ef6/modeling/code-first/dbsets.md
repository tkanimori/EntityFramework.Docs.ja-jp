---
title: 定義 (DbSets を)-EF6
description: Entity Framework 6 での DbSets の定義
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/code-first/dbsets
ms.openlocfilehash: dc5ad7f8b4ba32454c702f354b37223007e856e3
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065226"
---
# <a name="defining-dbsets"></a>定義 (DbSets を)
Code First ワークフローを使用して開発する場合は、データベースとのセッションを表す派生 DbContext を定義し、モデルの型ごとに Dbcontext を公開します。 このトピックでは、DbSet プロパティを定義するさまざまな方法について説明します。  

## <a name="dbcontext-with-dbset-properties"></a>DbContext と Dbcontext プロパティ  

Code First 例に示す一般的なケースは、モデルのエンティティ型に対して、パブリックな自動 Dbcontext プロパティを持つ DbContext を使用することです。 次に例を示します。  

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
}
```  

Code First モードで使用すると、ブログや投稿をエンティティ型として構成できるだけでなく、その他の型を構成することができます。 さらに、DbContext は、これらの各プロパティの setter を自動的に呼び出して、適切な Dbcontext のインスタンスを設定します。  

## <a name="dbcontext-with-idbset-properties"></a>IDbSet プロパティを持つ DbContext  

モックの作成時やフェイクの作成時など、インターフェイスを使用して set プロパティを宣言すると便利な場合があります。 このような場合は、DbSet の代わりに IDbSet インターフェイスを使用できます。 次に例を示します。  

``` csharp
public class BloggingContext : DbContext
{
    public IDbSet<Blog> Blogs { get; set; }
    public IDbSet<Post> Posts { get; set; }
}
```  

このコンテキストは、その設定プロパティに DbSet クラスを使用するコンテキストとまったく同じ方法で動作します。  

## <a name="dbcontext-with-read-only-set-properties"></a>読み取り専用の設定プロパティを持つ DbContext  

DbSet または IDbSet プロパティのパブリック setter を公開したくない場合は、代わりに読み取り専用プロパティを作成し、自分で set インスタンスを作成できます。 次に例を示します。  

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

DbContext は、Set メソッドから返された Dbcontext のインスタンスをキャッシュして、これらの各プロパティが呼び出されるたびに同じインスタンスを返すようにします。  

Code First のエンティティ型の検出は、パブリックな getter と setter を持つプロパティの場合と同じように動作します。  
