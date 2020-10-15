---
title: 個別の移行プロジェクトの使用-EF Core
description: Entity Framework Core でのデータベーススキーマの管理に個別の移行プロジェクトを使用する
author: bricelam
ms.date: 10/30/2017
uid: core/managing-schemas/migrations/projects
ms.openlocfilehash: 6f28027c320f0d1c13d13bef7d8227b2bb68df91
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062491"
---
# <a name="using-a-separate-migrations-project"></a>個別の移行プロジェクトの使用

移行は、を含むアセンブリとは別のアセンブリに格納することをお勧めし `DbContext` ます。 また、この方法を使用して、複数の移行のセットを維持できます。たとえば、1つは開発用、もう1つはリリース間のアップグレード用です。

目的

1. 新しいクラス ライブラリを作成します。

2. DbContext アセンブリへの参照を追加します。

3. 移行およびモデルスナップショットファイルをクラスライブラリに移動します。
   > [!TIP]
   > 既存の移行がない場合は、DbContext を含むプロジェクト内で1つを生成し、それを移動します。
   > 移行アセンブリに既存の移行が含まれていない場合、Add-Migration コマンドで DbContext を見つけることができないため、これは重要です。

4. 移行アセンブリを構成します。

   ```csharp
   options.UseSqlServer(
       connectionString,
       x => x.MigrationsAssembly("MyApp.Migrations"));
   ```

5. スタートアップアセンブリから移行アセンブリへの参照を追加します。
   * これによって循環依存関係が発生する場合は、クラスライブラリの出力パスを更新します。

     ```xml
     <PropertyGroup>
       <OutputPath>..\MyStartupProject\bin\$(Configuration)\</OutputPath>
     </PropertyGroup>
     ```

すべてを正しく行った場合は、新しい移行をプロジェクトに追加できるようになります。

## <a name="net-core-cli"></a>[.NET Core CLI](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add NewMigration --project MyApp.Migrations
```

## <a name="visual-studio"></a>[Visual Studio](#tab/vs)

```powershell
Add-Migration NewMigration -Project MyApp.Migrations
```

***
