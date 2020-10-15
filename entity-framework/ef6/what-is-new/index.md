---
title: 新機能 - EF6
description: Entity Framework 6 の新機能
author: ajcvickers
ms.date: 09/12/2019
uid: ef6/what-is-new/index
ms.openlocfilehash: 3c588475268ea063433f55fdb2f994d70f8c51ae
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92064303"
---
# <a name="whats-new-in-ef6"></a>EF6 の新機能

最新の機能と最高の信頼性を得るには、最新リリース バージョンの Entity Framework を使用することを強くお勧めします。
ただし、前のバージョンを使用しなければならないことや、新しい改善点を最新のプレリリースで試さなければならないこともあります。
特定のバージョンの EF をインストールする場合は、「[Get Entity Framework](xref:ef6/fundamentals/install)」(Entity Framework の取得) を参照してください。

## <a name="ef-640"></a>EF 6.4.0

EF 6.4.0 ランタイムは、NuGet で 2019 年 12 月にリリースされました。 EF 6.4 の主な目的は、EF 6.3 で提供された機能とシナリオをポーランド語にすることです。 Github にある[重要な修正の一覧](https://github.com/dotnet/ef6/milestone/14?closed=1)をご覧ください。

## <a name="ef-630"></a>EF 6.3.0

EF 6.3.0 ランタイムは、NuGet で 2019 年 9 月にリリースされました。 このリリースの主な目的は、EF 6 を使用する既存のアプリケーションを .NET Core 3.0 に円滑に移行することです。 コミュニティによって、いくつかのバグ修正と機能強化も提供されています。 詳細については、各 [6.3.0](https://github.com/aspnet/EntityFramework6/milestones?state=closed) マイルストーンで解決されたイシューを参照してください。 次に、重要なものをいくつか紹介します。

- .NET Core 3.0 のサポート
  - EntityFramework パッケージでは、.NET Framework 4.x に加え .NET Standard 2.1 をターゲットとするようになりました。
  - これは、EF 6.3 がクロスプラットフォームで、Linux や macOS などの Windows 以外のオペレーティングシステムでサポートされることを意味します。
  - 移行コマンドは、アウト プロセスの実行と SDK 形式のプロジェクトで動作するように書き替えられています。
- SQL Server HierarchyId のサポート。
- Roslyn と NuGet PackageReference との互換性の向上。
- アセンブリからの移行を有効化、追加、スクリプト作成、および適用する `ef6.exe` ユーティリティが追加されました。 これにより `migrate.exe` が置き換えられます。

### <a name="ef-designer-support"></a>EF デザイナーのサポート

現在、.NET Core または .NET Standard のプロジェクトでも、SDK スタイルの .NET Framework プロジェクトでも、EF デザイナーを直接使用することはサポートされていません。 

この制限を回避するには、エンティティと DbContext の EDMX ファイルと生成されたクラスを、リンク ファイルとして、同じソリューション内の .NET Core 3.0 または .NET Standard 2.1 プロジェクトに追加します。

そのリンク ファイルは、プロジェクト ファイル内で次のようになります。

``` csproj 
<ItemGroup>
  <EntityDeploy Include="..\EdmxDesignHost\Entities.edmx" Link="Model\Entities.edmx" />
  <Compile Include="..\EdmxDesignHost\Entities.Context.cs" Link="Model\Entities.Context.cs" />
  <Compile Include="..\EdmxDesignHost\Thing.cs" Link="Model\Thing.cs" />
  <Compile Include="..\EdmxDesignHost\Person.cs" Link="Model\Person.cs" />
</ItemGroup>
```

EDMX ファイルが EntityDeploy ビルド アクションにリンクされていることに注意してください。 これは特別な MSBuild タスク (EF 6.3 パッケージに含まれるようになりました) で、EF モデルを埋め込みリソースとして対象のアセンブリに追加する処理 (または、EDMX の [メタデータ成果物の処理] 設定に基づいて、これをファイルとして出力フォルダーにコピーする処理) を担当します。 これを設定する方法について詳しくは、[EDMX .NET Core サンプル](https://aka.ms/EdmxDotNetCoreSample)に関するページをご覧ください。

警告: "実際の" .edmx ファイルを定義している古いスタイル (つまり、非 SDK スタイル) の .NET Framework プロジェクトは必ず、.sln ファイル内のリンクを定義するプロジェクトの_前_にくるようにしてください。 そうしないと、デザイナーで .edmx ファイルを開いたとき、次のエラー メッセージが表示されます: "現在、プロジェクトに対して指定されているターゲット フレームワークでは Entity Framework が使用できません。 プロジェクトのターゲット フレームワークを変更することも、XmlEditor でモデルを編集することもできます"。

## <a name="past-releases"></a>以前のリリース

[以前のリリース](xref:ef6/what-is-new/past-releases)のページには、EF の過去のすべてのバージョンのアーカイブと、各リリースで導入された主要機能が含まれています。
