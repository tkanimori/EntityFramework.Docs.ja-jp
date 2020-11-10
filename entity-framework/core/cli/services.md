---
title: デザイン時サービス-EF Core
description: Entity Framework Core デザイン時サービスに関する情報
author: bricelam
ms.date: 10/22/2020
uid: core/cli/services
ms.openlocfilehash: 07fc8a39fb106f357e135a17f6867ab338621910
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431313"
---
# <a name="design-time-services"></a>デザイン時サービス

ツールで使用される一部のサービスは、デザイン時にのみ使用されます。 これらのサービスは EF Core のランタイムサービスとは別に管理され、アプリと共にデプロイされないようにします。 これらのサービスの1つ (たとえば、移行ファイルを生成するサービス) をオーバーライドするには、の実装を `IDesignTimeServices` スタートアッププロジェクトに追加します。

[!code-csharp[Main](../../../samples/core/Miscellaneous/CommandLine/DesignTimeServices.cs#DesignTimeServices)]

## <a name="referencing-microsoftentityframeworkcoredesign"></a>参照 (Microsoft EntityFrameworkCore を)

DevelopmentDependency は、パッケージの1つです。 これは、依存関係が他のプロジェクトに推移的にフローせず、既定でその型を参照できないことを意味します。

その型を参照し、デザイン時サービスをオーバーライドするには、プロジェクトファイル内の PackageReference 項目のメタデータを更新します。

```xml
<PackageReference Include="Microsoft.EntityFrameworkCore.Design" Version="3.1.9">
  <PrivateAssets>all</PrivateAssets>
  <!-- Remove IncludeAssets to allow compiling against the assembly -->
  <!--<IncludeAssets>runtime; build; native; contentfiles; analyzers; buildtransitive</IncludeAssets>-->
</PackageReference>
```

パッケージが PackageReference を使用して推移的に参照されている場合は、明示的なをパッケージに追加し、そのメタデータを変更する必要があります。

## <a name="list-of-services"></a>サービスのリスト

デザイン時サービスの一覧を次に示します。

サービス                                                                              | 説明
------------------------------------------------------------------------------------ | -----------
<xref:Microsoft.EntityFrameworkCore.Design.IAnnotationCodeGenerator>                 | 対応するモデル注釈のコードを生成します。
<xref:Microsoft.EntityFrameworkCore.Design.ICSharpHelper>                            | C# コードの生成に役立ちます。
<xref:Microsoft.EntityFrameworkCore.Design.IPluralizer>                              | 複数化と単数化の単語。
<xref:Microsoft.EntityFrameworkCore.Migrations.Design.IMigrationsCodeGenerator>      | 移行のコードを生成します。
<xref:Microsoft.EntityFrameworkCore.Migrations.Design.IMigrationsScaffolder>         | 移行ファイルを管理するためのメインクラス。
<xref:Microsoft.EntityFrameworkCore.Scaffolding.IDatabaseModelFactory>               | データベースからデータベースモデルを作成します。
<xref:Microsoft.EntityFrameworkCore.Scaffolding.IModelCodeGenerator>                 | モデルのコードを生成します。
<xref:Microsoft.EntityFrameworkCore.Scaffolding.IProviderConfigurationCodeGenerator> | コードの構成を生成します。
<xref:Microsoft.EntityFrameworkCore.Scaffolding.IReverseEngineerScaffolder>          | リバースエンジニアリングされたモデルをスキャフォールディングするためのメインクラス。

## <a name="using-services"></a>サービスの使用

これらのサービスは、独自のツールを作成する場合にも役立ちます。 たとえば、デザイン時ワークフローの一部を自動化する場合などです。

AddEntityFrameworkDesignTimeServices および AddDbContextDesignTimeServices 拡張メソッドを使用して、これらのサービスを含むサービスプロバイダーを作成できます。

[!code-csharp[](../../../samples/core/Miscellaneous/CommandLine/CustomTools.cs#CustomTools)]
