---
title: 移行の概要 - EF Core
description: Entity Framework Core で移行を使用してデータベース スキーマを管理する方法の概要
author: bricelam
ms.date: 10/28/2020
uid: core/managing-schemas/migrations/index
ms.openlocfilehash: b9547298714af59453aeae6d05742a03c067708b
ms.sourcegitcommit: 788a56c2248523967b846bcca0e98c2ed7ef0d6b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/20/2020
ms.locfileid: "95003396"
---
# <a name="migrations-overview"></a>移行の概要

実プロジェクトでは、機能が実装されると、データ モデルが変更されます。新しいエンティティまたはプロパティが追加および削除されると、アプリケーションと一致させるために、データベース スキーマを変更しなければならなくなります。 EF Core の移行機能では、データベースの既存のデータを維持しながら、アプリケーションのデータ モデルとデータベース スキーマを同期した状態で、データベース スキーマを増分的に更新することができます。

移行は、大まかに次のように機能します。

* データ モデルの変更が採用されると、データベース スキーマを一致させておくために、必要な更新が記述された対応する移行を、開発者は EF Core ツールを使用して追加します。EF Core は、現在のモデルを古いモデルのスナップショットと比較して相違点を特定し、移行ソース ファイルを生成します。このファイルは、他のソース ファイルと同様に、ご自分のプロジェクトのソース管理で追跡できます。
* 新しい移行が生成されると、それをさまざまな方法でデータベースに適用できます。 EF Core では、適用されたすべての移行を特別な履歴テーブルに記録します。これにより、どの移行が適用されたか、またはされていないかを知ることができます。

このページの残りの部分では、移行手順を初心者向けに詳細に説明します。 さらなる詳細については、このセクションの他のページを参照してください。

## <a name="getting-started"></a>作業の開始

次の単純なモデルが含まれる、あなたの初めての EF Core アプリケーションが完成したとします。

```csharp
public class Blog
{
    public int Id { get; set; }
    public string Name { get; set; }
}
```

開発時に、[Create API と Drop API](xref:core/managing-schemas/ensure-created) を使用して、すばやく反復処理を行い、ご自分のモデルに必要に応じた変更を加えたとします。しかし、今度ご自分のアプリケーションが運用環境に移行することになったので、データベース全体を削除せず、スキーマを安全に発展させる方法が必要です。

### <a name="install-the-tools"></a>ツールのインストール

まず、[EF Core のコマンドライン ツール](xref:core/cli/index)をインストールする必要があります。

* 一般的に、どのプラットフォームでも動作する [.NET Core CLI ツール](xref:core/cli/dotnet)の使用が推奨されます。
* Visual Studio 内で作業するのに慣れている場合や、EF6 での移行経験がある場合は、[パッケージ マネージャー コンソール ツール](xref:core/cli/powershell)を使用することもできます。

### <a name="create-your-first-migration"></a>ご自分の最初の移行を作成する

これで、ご自分の最初の移行を追加する準備ができました。 EF Core に **InitialCreate** という名前の移行を作成するように指示します。

#### <a name="net-core-cli"></a>[.NET Core CLI](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add InitialCreate
```

#### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

```powershell
Add-Migration InitialCreate
```

**_

EF Core により、ご自分のプロジェクトに _ *Migrations** というディレクトリが作成され、いくつかのファイルが生成されます。 EF Core で生成された内容を確認し、場合によっては修正することが推奨されますが、ここではそれは省きます。

### <a name="create-your-database-and-schema"></a>ご自分のデータベースとスキーマを作成する

この時点で、EF にご自分のデータベースを作成させ、移行からご自分のスキーマを作成できます。 これは、次のように行います。

#### <a name="net-core-cli"></a>[.NET Core CLI](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef database update
```

#### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

```powershell
Update-Database
```

**_

これで完了です。SQL を 1 行も記述せずに、ご自分のアプリケーションをご自分の新しいデータベースで実行する準備ができました。 この方法での移行の適用は、ローカルでの開発には適していますが、運用環境には適していません。詳細については、[移行の適用](xref:core/managing-schemas/migrations/applying)に関するページを参照してください。

### <a name="evolving-your-model"></a>ご自分のモデルを発展させる

数日後、ご自分のブログに作成のタイムスタンプを追加するように求められました。 ご自分のアプリケーションに必要な変更を行ったので、現在、ご自分のモデルは次のようになっています。

```csharp
public class Blog
{
    public int Id { get; set; }
    public string Name { get; set; }
    public DateTime CreatedTimestamp { get; set; }
}
```

現在、ご自分のモデルとご自分の実稼働データベースは一致しなくなっています。ご自分のデータベース スキーマに新しい列を追加する必要があります。 このために、新しい移行を作成します。

#### <a name="net-core-cli"></a>[.NET Core CLI](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef migrations add AddBlogCreatedTimestamp
```

#### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

```powershell
Add-Migration AddBlogCreatedTimestamp
```

_*_

移行には、後でプロジェクト履歴がわかりやすいように、わかりやすい名前が付いていることに着目してください。

これはプロジェクトの最初の移行ではないため、EF Core では、列の追加前に、ご自分の更新されたモデルを古いモデルのスナップショットと比較します。このモデルのスナップショットとは、移行を追加したときに EF Core によって生成されるファイルの 1 つであり、ソース管理にチェックインされます。 その比較に基づき、EF Core は列が追加されたことを検出し、適切な移行を追加します。

これで、以前と同じようにご自分の移行を適用できるようになりました。

<!--markdownlint-disable MD024-->

#### <a name="net-core-cli"></a>[.NET Core CLI](#tab/dotnet-core-cli)

```dotnetcli
dotnet ef database update
```

#### <a name="visual-studio"></a>[Visual Studio](#tab/vs)

```powershell
Update-Database
```

<!--markdownlint-enable MD024-->

_*_

今回は、データベースが既に存在していることが EF によって検出されていることに着目してください。 また、上記の最初の移行が適用されたとき、その事実がご自分のデータベースの特別な移行履歴テーブルに記録されました。これにより、EF は新しい移行のみを自動的に適用します。

### <a name="excluding-parts-of-your-model"></a>モデルの一部を除外する

> [!NOTE]
> この機能は EF Core 5.0 で導入されました。

別の DbContext の型を参照する必要がある場合があります。 これにより、移行の競合が発生する可能性があります。 これを回避するには、いずれかの DbContext の移行から型を除外します。

[!code-csharp[](../../../../samples/core/Modeling/FluentAPI/TableExcludeFromMigrations.cs#TableExcludeFromMigrations)]

### <a name="next-steps"></a>次の手順

上では、移行について簡単に説明しました。 [移行の管理](xref:core/managing-schemas/migrations/managing)および[それの適用](xref:core/managing-schemas/migrations/applying)などについては、その他のドキュメント ページを参照してください。 [.NET Core CLI ツール リファレンス](xref:core/cli/index)にも、さまざまなコマンドに関する有用な情報があります。

## <a name="additional-resources"></a>その他のリソース

EF Core 5.0 の新しい移行機能について詳しく調べる [EF Core コミュニティ スタンドアップ セッション](https://www.youtube.com/watch?v=mSsGERmrhnE&list=PLdo4fOcmZ0oX-DBuRG4u58ZTAJgBAeQ-t&index=20)。
