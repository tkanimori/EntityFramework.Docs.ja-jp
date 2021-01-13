---
title: ログとインターセプトの概要 - EF Core
description: EF Core のログ、イベント、インターセプター、診断の概要
author: ajcvickers
ms.date: 10/01/2020
uid: core/logging-events-diagnostics/index
ms.openlocfilehash: 5ddbffc8d39e97065f2e06af14443c62b4a9465d
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129227"
---
# <a name="overview-of-logging-and-interception"></a>ログとインターセプトの概要

Entity Framework Core (EF Core) には、ログの生成、イベントへの応答、および診断の取得を行うためのメカニズムがいくつか含まれています。 これらはそれぞれ異なる状況に合わせて調整されており、複数のメカニズムが動作する可能性がある場合でも、タスクに最適なメカニズムを選択することが重要です。 たとえば、データベース インターセプターを使用して SQL をログに記録することもできますが、これはログに合わせて調整されたメカニズムの方がより適切に処理されます。 このページでは、これらの各メカニズムの概要を示し、それぞれを使用する状況について説明します。

## <a name="quick-reference"></a>クイック リファレンス

以下の表に、ここで説明するメカニズムの違いに関するクイック リファレンスを示します。

| メカニズム |  Async | Scope | 登録済み | 使用目的
|:----------|--------|-------|------------|-------------
| シンプルなログ | いいえ | コンテキストごと | コンテキスト構成 | 開発時のログ
| Microsoft.Extensions.Logging | いいえ | コンテキストごと* | D.I. またはコンテキスト構成 | 本番環境のログ
| イベント | いいえ | コンテキストごと | 時間指定なし | EF イベントへの応答
| インターセプター | Yes | コンテキストごと | コンテキスト構成 | EF 操作の取り扱い
| 診断リスナー | いいえ | プロセス | グローバル | アプリケーション診断

*通常、`Microsoft.Extensions.Logging` は依存関係の挿入を使用してアプリケーションごとに構成されますが、EF レベルでは、必要に応じて異なるロガーを使用して各コンテキストを構成 _できます_。

## <a name="simple-logging"></a>シンプルなログ

> [!NOTE]
> この機能は EF Core 5.0 で導入されました。

[DbContext インスタンスの構成](xref:core/dbcontext-configuration/index)時に <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.LogTo%2A> を使用すると、任意の種類のアプリケーションから EF Core ログにアクセスできます。 この構成は、通常、<xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType> のオーバーライドで行われます。 次に例を示します。

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(Console.WriteLine);
-->
[!code-csharp[LogToConsole](../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToConsole)]

この概念は、EF6 の <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> に似ています。

詳細については、「[シンプルなログ](xref:core/logging-events-diagnostics/simple-logging)」を参照してください。

## <a name="microsoftextensionslogging"></a>Microsoft.Extensions.Logging

[Microsoft.Extensions.Logging](/dotnet/core/extensions/logging) は、多くの一般的なログ システム用のプラグイン プロバイダーを備えた拡張可能なログ メカニズムです。 EF Core は `Microsoft.Extensions.Logging` と完全に統合されており、ASP.NET Core アプリケーションでは既定でこの形式のログが使用されます。

詳細については、[EF Core での Microsoft.Extensions.Logging の使用](xref:core/logging-events-diagnostics/extensions-logging)に関する記事をご覧ください。

## <a name="events"></a>イベント

> [!NOTE]
> EF Core 5.0 では、追加のイベントが導入されました。

EF Core では、EF Core コードで特定の処理が発生したときにコールバックとして機能する [.NET イベント](/dotnet/standard/events/)が公開されます。 イベントはインターセプターよりシンプルであり、より柔軟な登録が可能です。 ただし、これらは同期のみであるため、非ブロッキングの非同期 I/O を実行することはできません。

イベントは DbContext インスタンスごとに登録され、この登録はいつでも行うことができます。 プロセス内のすべての DbContext インスタンスで同じ情報を取得するには、[診断リスナー](xref:core/logging-events-diagnostics/diagnostic-listeners)を使用します。

詳細については、「[EF Core での .NET イベント](xref:core/logging-events-diagnostics/events)」を参照してください。

## <a name="interception"></a>interception

> [!NOTE]
> この機能は EF Core 3.0 で導入されました。 EF Core 5.0 では、追加のインターセプターが導入されました。

EF Core インターセプターを使用すると、EF Core 操作をインターセプト、変更、抑制できます。 これには、コマンド実行などの低レベルのデータベース操作と SaveChanges の呼び出しなどの上位レベルの操作が含まれます。

インターセプターは、インターセプトしている操作を変更または抑制できる点がログや診断と異なります。 ログ記録には、[シンプルなログ](xref:core/logging-events-diagnostics/simple-logging)または [Microsoft.Extensions.Logging](xref:core/logging-events-diagnostics/extensions-logging) の使用をお勧めします。

インターセプターは DbContext インスタンスごとに登録され、この登録はいつでも行うことができます。 プロセス内のすべての DbContext インスタンスで同じ情報を取得するには、[診断リスナー](xref:core/logging-events-diagnostics/diagnostic-listeners)を使用します。

詳細については、[インターセプト](xref:core/logging-events-diagnostics/interceptors)に関する記事をご覧ください。

## <a name="diagnostic-listeners"></a>診断リスナー

診断リスナーを使用すると、現在の .NET プロセスで発生する任意の EF Core イベントをリッスンできます。

診断リスナーは、単一の DbContext インスタンスからイベントを取得するのには適していません。 EF Core インターセプターは、コンテキストごとの登録によって同じイベントへのアクセスを可能にします。

診断リスナーは、ログ記録向けに設計されていません。 ログ記録には、[シンプルなログ](xref:core/logging-events-diagnostics/simple-logging)または [Microsoft.Extensions.Logging](xref:core/logging-events-diagnostics/extensions-logging) の使用をお勧めします。

詳細については、「[EF Core での診断リスナーの使用](xref:core/logging-events-diagnostics/diagnostic-listeners)」を参照してください。
