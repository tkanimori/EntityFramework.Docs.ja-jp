---
title: EF6 から EF Core への移植 - コードベース モデルの移植 - EF
description: Entity Framework 6 つのコードベース モデル アプリケーションを Entity Framework Core に移植する方法に関する固有の情報
author: rowanmiller
ms.date: 10/27/2016
uid: efcore-and-ef6/porting/port-code
ms.openlocfilehash: a5bbdc2ee95ea6bea96e24bee4588b524e0ffc58
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073579"
---
# <a name="porting-an-ef6-code-based-model-to-ef-core"></a>EF Core への EF6 コードベース モデルの移植

すべての注意事項を読み、移植の準備ができたら、次のガイドラインを参考にして作業を開始してください。

## <a name="install-ef-core-nuget-packages"></a>EF Core の NuGet パッケージをインストールする

EF Core を使用するには、使用するデータベース プロバイダーに対して NuGet パッケージをインストールします。 たとえば、SQL Server を対象とする場合は、`Microsoft.EntityFrameworkCore.SqlServer` をインストールします。 詳細については、「[データベース プロバイダー](xref:core/providers/index)」を参照してください。

移行の使用を計画している場合は、`Microsoft.EntityFrameworkCore.Tools` パッケージもインストールする必要があります。

EF6 NuGet パッケージ (EntityFramework) はインストールされたままにしておくことをお勧めします。これは EF Core と EF6 を同じアプリケーションで並列で使用できるようにするためです。 ただし、アプリケーションのどの領域でも EF6 を使用するつもりがない場合は、パッケージをアンインストールすることで、注意が必要なコード上にコンパイル エラーを表示することができます。

## <a name="swap-namespaces"></a>名前空間をスワップする

EF6 で使用する API のほとんどは、`System.Data.Entity` 名前空間 (および関連するサブ名前空間) にあります。 最初のコード変更では、`Microsoft.EntityFrameworkCore` 名前空間にスワップします。 通常は、派生コンテキスト コード ファイルから開始し、そこで作業を行い、コンパイル エラーが発生したら対処します。

## <a name="context-configuration-connection-etc"></a>コンテキストの構成 (接続など)

[EF Core がアプリケーションで動作するかどうかの確認](xref:efcore-and-ef6/porting/index)の記事で説明しているように、EF Core には、接続先のデータベースを検出する特別な方法はあまりありません。 派生コンテキストで `OnConfiguring` メソッドをオーバーライドし、API 固有のデータベース プロバイダーを使用してデータベースへの接続を設定する必要があります。

ほとんどの EF6 アプリケーションでは、接続文字列がアプリケーションの `App/Web.config` ファイルに格納されます。 EF Core では、`ConfigurationManager` API を使用してこの接続文字列を読み取ります。 この API を使用できるようにするには、`System.Configuration` フレームワーク アセンブリに参照を追加しなければならない場合があります。

``` csharp
public class BloggingContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
      optionsBuilder.UseSqlServer(ConfigurationManager.ConnectionStrings["BloggingDatabase"].ConnectionString);
    }
}
```

## <a name="update-your-code"></a>コードを更新する

この時点では、コンパイル エラーに対処し、コードを見直して、動作変更の影響があるかどうかを確認します。

## <a name="existing-migrations"></a>既存の移行

既存の EF6 移行を EF Core に移植する方法は実際にはありません。

可能であれば、EF6 からの以前の移行がすべてデータベースに適用されていることを前提に、EF Core を使用してその時点からスキーマの移行を開始することをお勧めします。 これを行うには、モデルが EF Core に移植された後に `Add-Migration` コマンドを使用して移行を追加します。 次に、スキャフォールディング移行の `Up` と `Down` のメソッドからすべてのコードを削除します。 その後の移行では、最初の移行がスキャフォールディングされたときにモデルと比較されます。

## <a name="test-the-port"></a>移植をテストする

アプリケーションでコンパイルされるからといって、EF Core に正常に移植されるわけではありません。 アプリケーションのすべての領域をテストして、どの動作変更もアプリケーションに悪影響を与えないことを確認する必要があります。
