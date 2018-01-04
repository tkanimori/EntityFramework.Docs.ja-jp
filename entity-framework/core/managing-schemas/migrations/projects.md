---
title: "複数のプロジェクトの EF コアでの移行"
author: bricelam
ms.author: bricelam
ms.date: 10/30/2017
ms.technology: entity-framework-core
ms.openlocfilehash: 3684e86cce0005056380d89604d038c734054d14
ms.sourcegitcommit: ced2637bf8cc5964c6daa6c7fcfce501bf9ef6e8
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/22/2017
---
<a name="using-a-separate-project"></a>別のプロジェクトを使用します。
========================
1 つ含まれているよりも別のアセンブリで使用して移行を格納することがあります、`DbContext`です。 また、リリースごとにアップグレードとこの戦略などの移行には、複数のセットを維持するために、開発用の 1 つを使用することができます。

目的

1. 新しいクラス ライブラリを作成します。

2. DbContext アセンブリへの参照を追加します。

3. クラス ライブラリに移行およびモデルのスナップショット ファイルを移動します。
   * いずれかを追加していない場合は、DbContext プロジェクトに追加しに移動します。

4. 移行アセンブリを構成します。

   ``` csharp
   options.UseSqlServer(
       connectionString,
       x => x.MigrationsAssembly("MyApp.Migrations"));
   ```

5. スタートアップ アセンブリから、移行アセンブリへの参照を追加します。
   * これにより、循環する依存関係場合、は、クラス ライブラリの出力パスを更新します。

     ``` xml
     <PropertyGroup>
       <OutputPath>..\MyStarupProject\bin\$(Configuration)\</OutputPath>
     </PropertyGroup>
     ```

削除できた場合すべて正しくは、新しい移行プロジェクトに追加することができます。

``` powershell
Add-Migration NewMigration -Project MyApp.Migrations
```
``` Console
dotnet ef migrations add NewMigration --project MyApp.Migrations
```
