---
title: EF6 から EF Core への移植 - コード ベースのモデルの移植
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 2dce1a50-7d84-4856-abf6-2763dd9be99d
uid: efcore-and-ef6/porting/port-code
ms.openlocfilehash: 2484b681d71ae8711b1b3a59bc274a0b2e403294
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42997050"
---
# <a name="porting-an-ef6-code-based-model-to-ef-core"></a>EF Core に EF6 コードに基づくモデルの移植

すべての警告を読んだし、ポートに準備ができたら、し、ここではいくつかのガイドラインを参照してください。

## <a name="install-ef-core-nuget-packages"></a>EF Core NuGet パッケージをインストールします。

EF Core を使用するには、使用するデータベース プロバイダーの NuGet パッケージをインストールします。 たとえば、SQL Server を対象とするときにインストール`Microsoft.EntityFrameworkCore.SqlServer`します。 参照してください[データベース プロバイダー](../../core/providers/index.md)詳細についてはします。

移行を使用する予定のかどうかは、インストールする必要があります、`Microsoft.EntityFrameworkCore.Tools`パッケージ。

これを EF6 の NuGet パッケージ (EntityFramework) をインストールすると、そのままに EF Core と EF6 が同じアプリケーションで並列で使用できます。 ただし、アプリケーションの領域で EF6 を使用する場合は、パッケージをアンインストールし、役立つの注意が必要なコードのコンパイル エラーが発生します。

## <a name="swap-namespaces"></a>名前空間をスワップします。

EF6 で使用するほとんどの Api は、`System.Data.Entity`名前空間 (および関連するサブ名前空間)。 最初のコード変更をスワップするには、`Microsoft.EntityFrameworkCore`名前空間。 通常、派生コンテキストのコード ファイルを起動し、作業するそこが発生したコンパイル エラーに対処します。

## <a name="context-configuration-connection-etc"></a>コンテキストの構成 (接続など)

」の説明に従って[ように EF Core は作業アプリケーションの](ensure-requirements.md)、EF Core が少ないマジックの周囲に接続するデータベースを検出します。 オーバーライドする必要があります、`OnConfiguring`メソッドを派生コンテキストと、データベースへの接続をセットアップするには、データベース プロバイダーの特定の API を使用します。

ほとんどの EF6 アプリケーションでは、アプリケーションの接続文字列を格納`App/Web.config`ファイル。 EF Core でのこの接続文字列を使用して読み取りを`ConfigurationManager`API。 参照を追加する必要があります、`System.Configuration`この API を使用できるフレームワーク アセンブリ。

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

この時点では、コンパイル エラーをアドレス指定と確認の動作の変更とするかどうかは影響を確認するコードの問題になります。

## <a name="existing-migrations"></a>既存の移行

既存の EF6 の移行を EF Core に移植可能な方法が本当にありません。

可能であれば、EF6 からすべての以前の移行をデータベースに適用されているし、開始からスキーマを移行するポイントの EF Core を使用しが想定することをお勧めします。 これを行うには、使用して、`Add-Migration`モデルが EF Core に移植したら、移行を追加するコマンド。 すべてのコードを削除し、`Up`と`Down`スキャフォールディングの移行の方法です。 次の移行は、その最初の移行がスキャフォールディングされるときに、モデルに比較されます。

## <a name="test-the-port"></a>ポートをテストします。

アプリケーションをコンパイルするためにだけしないわけでは EF Core に移植が正常にします。 None、動作の変更と、アプリケーションが悪影響を受けることを確認するアプリケーションのすべての領域をテストする必要があります。
