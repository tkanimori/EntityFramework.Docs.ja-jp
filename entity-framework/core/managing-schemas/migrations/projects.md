---
title: 複数のプロジェクトの EF Core での移行
author: bricelam
ms.author: bricelam
ms.date: 10/30/2017
uid: core/managing-schemas/migrations/projects
ms.openlocfilehash: 30a6afad1488e74ce2585be3d780186311379a97
ms.sourcegitcommit: ad1bdea58ed35d0f19791044efe9f72f94189c18
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/28/2018
ms.locfileid: "47447145"
---
<a name="using-a-separate-project"></a>別のプロジェクトを使用します。
========================
1 つを含む別のアセンブリで、移行を格納することも、`DbContext`します。 リリースごとにアップグレードとこの戦略など、移行の複数のセットを維持するために、開発用に 1 つを使用することもできます。

目的

1. 新しいクラス ライブラリを作成します。

2. DbContext アセンブリへの参照を追加します。

3. クラス ライブラリに移行し、モデルのスナップショット ファイルを移動します。
   > [!TIP]
   > 既存の移行がない場合は、DbContext を含むプロジェクトを生成し、移動します。 移行アセンブリに既存の移行が含まれていない場合、Add-migration コマンドことはできません、DbContext を検索するため重要です。

4. 移行アセンブリを構成します。

   ``` csharp
   options.UseSqlServer(
       connectionString,
       x => x.MigrationsAssembly("MyApp.Migrations"));
   ```

5. スタートアップ アセンブリから、移行のアセンブリへの参照を追加します。
   * これにより、循環依存関係場合は、クラス ライブラリの出力パスを更新します。

     ``` xml
     <PropertyGroup>
       <OutputPath>..\MyStartupProject\bin\$(Configuration)\</OutputPath>
     </PropertyGroup>
     ```

すべてのプロセスが正しく、プロジェクトに新しい移行を追加できる必要があります。

``` powershell
Add-Migration NewMigration -Project MyApp.Migrations
```
``` Console
dotnet ef migrations add NewMigration --project MyApp.Migrations
```
