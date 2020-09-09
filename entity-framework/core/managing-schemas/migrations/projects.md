---
title: 個別の移行プロジェクトの使用-EF Core
description: Entity Framework Core でのデータベーススキーマの管理に個別の移行プロジェクトを使用する
author: bricelam
ms.author: bricelam
ms.date: 10/30/2017
uid: core/managing-schemas/migrations/projects
ms.openlocfilehash: a3f0ed96c6a8e3e8629d9a4bb1610fcbfe6ca043
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2020
ms.locfileid: "89617916"
---
# <a name="using-a-separate-migrations-project"></a>個別の移行プロジェクトの使用

移行は、を含むアセンブリとは別のアセンブリに格納することをお勧めし `DbContext` ます。 また、この方法を使用して、複数の移行のセットを維持できます。たとえば、1つは開発用、もう1つはリリース間のアップグレード用です。

目的

1. 新しいクラス ライブラリを作成します。

2. DbContext アセンブリへの参照を追加します。

3. 移行およびモデルスナップショットファイルをクラスライブラリに移動します。
   > [!TIP]
   > 既存の移行がない場合は、DbContext を含むプロジェクト内で1つを生成し、それを移動します。
   > 移行アセンブリに既存の移行が含まれていない場合、追加移行コマンドで DbContext を見つけることができないため、これが重要になります。

4. 移行アセンブリを構成します。

   ``` csharp
   options.UseSqlServer(
       connectionString,
       x => x.MigrationsAssembly("MyApp.Migrations"));
   ```

5. スタートアップアセンブリから移行アセンブリへの参照を追加します。
   * これによって循環依存関係が発生する場合は、クラスライブラリの出力パスを更新します。

     ``` xml
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

``` powershell
Add-Migration NewMigration -Project MyApp.Migrations
```

***
