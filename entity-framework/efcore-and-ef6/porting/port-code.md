---
title: EF6 から EF Core に移植してコードベースのモデルを移植する-EF
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 2dce1a50-7d84-4856-abf6-2763dd9be99d
uid: efcore-and-ef6/porting/port-code
ms.openlocfilehash: 0a99eac2091c07d8bcf7d4e5e4bdc2afcaeee810
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2019
ms.locfileid: "72181215"
---
# <a name="porting-an-ef6-code-based-model-to-ef-core"></a>EF6 コードベースモデルを EF Core に移植する

すべての注意事項を読んだ後に移植する準備ができたら、次のガイドラインを参考にしてください。

## <a name="install-ef-core-nuget-packages"></a>NuGet パッケージのインストール EF Core

EF Core を使用するには、使用するデータベースプロバイダーの NuGet パッケージをインストールします。 たとえば、SQL Server を対象とする場合は、`Microsoft.EntityFrameworkCore.SqlServer`をインストールします。 詳細については、「[データベースプロバイダー](../../core/providers/index.md) 」を参照してください。

移行の使用を計画している場合は、`Microsoft.EntityFrameworkCore.Tools` パッケージもインストールする必要があります。

EF6 NuGet パッケージ (EntityFramework) はインストールされたままにしておくことをお勧めします。これは EF Core と EF6 を同じアプリケーションでサイドバイサイドで使用できるようにするためです。 ただし、アプリケーションの任意の領域で EF6 を使用するつもりがない場合は、パッケージをアンインストールすることで、注意が必要なコードの一部でコンパイルエラーが発生することがあります。

## <a name="swap-namespaces"></a>名前空間の入れ替え

EF6 で使用する Api のほとんどは、`System.Data.Entity` 名前空間 (および関連するサブ名前空間) にあります。 最初のコード変更では、`Microsoft.EntityFrameworkCore` 名前空間にスワップします。 通常は、派生コンテキストコードファイルから開始し、そこから作業を行い、コンパイルエラーが発生したときに対処します。

## <a name="context-configuration-connection-etc"></a>コンテキストの構成 (接続など)

「アプリケーションで[EF Core が機能することを確認](ensure-requirements.md)する」で説明されているように、EF Core は、接続先のデータベースを検出するマジックがあまりありません。 派生コンテキストで `OnConfiguring` メソッドをオーバーライドし、データベースプロバイダー固有の API を使用してデータベースへの接続を設定する必要があります。

ほとんどの EF6 アプリケーションでは、接続文字列はアプリケーション `App/Web.config` ファイルに格納されます。 EF Core では、`ConfigurationManager` API を使用してこの接続文字列を読み取ります。 この API を使用できるようにするには、`System.Configuration` framework アセンブリに参照を追加する必要がある場合があります。

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

この時点で、コンパイルエラーに対処し、コードを見直して、動作の変更が影響を受けるかどうかを確認します。

## <a name="existing-migrations"></a>既存の移行

既存の EF6 移行を EF Core に移植する方法は実際にはありません。

可能であれば、EF6 からの以前の移行がすべてデータベースに適用されていることを前提にしてから、EF Core を使用してその時点からスキーマの移行を開始することをお勧めします。 これを行うには、モデルが EF Core に移植された後に、`Add-Migration` コマンドを使用して移行を追加します。 次に、スキャフォールディング移行の `Up` と `Down` メソッドからすべてのコードを削除します。 その後の移行では、最初の移行がスキャフォールディングされたときにモデルと比較されます。

## <a name="test-the-port"></a>ポートをテストする

アプリケーションがコンパイルされるので、は EF Core に正常に移植されるという意味ではありません。 アプリケーションのすべての領域をテストして、動作の変更がアプリケーションに悪影響を与えないことを確認する必要があります。
