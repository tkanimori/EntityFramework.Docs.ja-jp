---
title: EF6 から EF Core - コードに基づくモデルを移植への移植
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 2dce1a50-7d84-4856-abf6-2763dd9be99d
uid: efcore-and-ef6/porting/port-code
ms.openlocfilehash: a0fa4f9a7028f56666fb993185cb03eddb9a2cd1
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2017
ms.locfileid: "26052952"
---
# <a name="porting-an-ef6-code-based-model-to-ef-core"></a>EF core EF6 コードに基づくモデルの移植

すべての警告を読んだし、ポートする準備ができたら、し、ここでは作業を開始するためのいくつかのガイドライン。

## <a name="install-ef-core-nuget-packages"></a>EF Core NuGet パッケージをインストールします。

EF コアを使用するのには、使用するデータベース プロバイダーの NuGet パッケージをインストールします。 たとえば、SQL Server を対象とする場合にインストールする場合`Microsoft.EntityFrameworkCore.SqlServer`です。 参照してください[データベース プロバイダー](../../core/providers/index.md)詳細についてはします。

移行を使用する予定のかどうかは、インストールすることも必要があります、`Microsoft.EntityFrameworkCore.Tools`パッケージです。

これは正常に EF6 NuGet パッケージ (EntityFramework) をインストールすると、そのまま EF コアおよび EF6 が同じアプリケーションに並列で使用できます。 ただし、アプリケーションのすべての領域で EF6 を使用する場合は、パッケージをアンインストールに役立つの注意が必要なコードのコンパイル エラーが発生します。

## <a name="swap-namespaces"></a>名前空間をスワップします。

EF6 で使用するほとんどの Api は、`System.Data.Entity`名前空間 (および関連するサブ名前空間)。 最初のコード変更をスワップするには、`Microsoft.EntityFrameworkCore`名前空間。 通常、派生コンテキスト コード ファイルで開始し、作業そこから発生すると、コンパイル エラーを修正とします。

## <a name="context-configuration-connection-etc"></a>コンテキストの構成 (接続などです。)

」の説明に従って[を確認してください EF コアは作業アプリケーションの](ensure-requirements.md)、EF コアが少ないマジック周囲への接続にデータベースを検出します。 オーバーライドする必要があります、`OnConfiguring`メソッドを派生のコンテキストと、データベースへの接続をセットアップするには、データベース プロバイダーの特定の API を使用します。

ほとんどの EF6 アプリケーションは、アプリケーションでは、接続文字列を格納`App/Web.config`ファイル。 EF コアで読むこの接続文字列を使用して、 `ConfigurationManager` API です。 参照を追加する必要があります、`System.Configuration`フレームワーク アセンブリをこの API を使用することです。

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

## <a name="update-your-code"></a>コードを更新します

この時点でのコンパイル エラーを修正するかどうか、動作は変更に影響するを参照してください。 コードを確認することを勧めします。

## <a name="existing-migrations"></a>既存の移行

方法はありません実際に可能なポートの EF コアへの既存の EF6 移行します。

可能であれば、EF6 から以前のすべての移行は、データベースに適用されているし、スキーマを移行することから開始ポイント EF コアを使用したと仮定することをお勧めします。 これを行うには、使用すると、 `Add-Migration` EF コアにモデルが移植された後に、移行を追加するコマンド。 すべてのコードを削除すると、`Up`と`Down`スキャフォールディングの移行のメソッドです。 次の移行は、その最初の移行がスキャフォールディングされたときにモデルに比較されます。

## <a name="test-the-port"></a>ポートをテストします。

アプリケーションをコンパイルするためにだけ限りません EF コアが正常に移植します。 動作の変更のいずれもと、アプリケーションが悪影響を受けることを確認するには、アプリケーションのすべての領域をテストする必要があります。
