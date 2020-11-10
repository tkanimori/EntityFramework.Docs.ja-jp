---
title: 個別の移行プロジェクトの使用-EF Core
description: Entity Framework Core でのデータベーススキーマの管理に個別の移行プロジェクトを使用する
author: bricelam
ms.date: 11/06/2020
uid: core/managing-schemas/migrations/projects
ms.openlocfilehash: 2d1c093e0bb307584e2bf19cb93deec98aa10692
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2020
ms.locfileid: "94429807"
---
# <a name="using-a-separate-migrations-project"></a>個別の移行プロジェクトの使用

移行は、を含むプロジェクトとは別のプロジェクトに保存することができ `DbContext` ます。 また、この方法を使用して、複数の移行のセットを維持できます。たとえば、1つは開発用、もう1つはリリース間のアップグレード用です。

> [!TIP]
> この記事の[サンプルは GitHub](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/Schemas/ThreeProjectMigrations) で確認できます。

## <a name="steps"></a>手順

1. 新しいクラス ライブラリを作成します。

2. DbContext プロジェクトへの参照を追加します。

3. 移行およびモデルスナップショットファイルをクラスライブラリに移動します。
   > [!TIP]
   > 既存の移行がない場合は、DbContext を含むプロジェクト内で1つを生成し、それを移動します。
   > これは、移行プロジェクトに既存の移行が含まれていない場合に、Add-Migration コマンドで DbContext を見つけることができないため、重要です。

4. 移行アセンブリを構成します。

   [!code-csharp[](../../../../samples/core/Schemas/ThreeProjectMigrations/WebApplication1/Startup.cs#snippet_MigrationsAssembly)]

5. [ **スタートアップ** プロジェクトから移行プロジェクトへの参照を追加します。

   ```xml
   <ItemGroup>
     <ProjectReference Include="..\WebApplication1.Migrations\WebApplication1.Migrations.csproj">
   </ItemGroup>
   ```

   これによって循環依存関係が発生した場合は、代わりに **移行** プロジェクトの基本出力パスを更新できます。

   ```xml
   <PropertyGroup>
     <BaseOutputPath>..\WebApplication1\bin\</BaseOutputPath>
   </PropertyGroup>
   ```

すべてを正しく行った場合は、新しい移行をプロジェクトに追加できるようになります。

## <a name="net-core-cli"></a>[.NET Core CLI](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add NewMigration --project WebApplication1.Migrations
```

## <a name="visual-studio"></a>[Visual Studio](#tab/vs)

```powershell
Add-Migration NewMigration -Project WebApplication1.Migrations
```

***
