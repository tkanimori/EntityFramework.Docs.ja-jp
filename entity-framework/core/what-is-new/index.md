---
title: EF Core のリリースと計画
description: 現在の EF Core のリリースと、今後のリリースのスケジュール/計画の詳細
author: ajcvickers
ms.date: 01/12/2021
uid: core/what-is-new/index
ms.openlocfilehash: 18d8055840b5a38dc62d20e7e18c440b7360c9e4
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128330"
---
# <a name="ef-core-releases-and-planning"></a>EF Core のリリースと計画

## <a name="stable-releases"></a>安定版リリース

| リリース | [対象とする Framework] | サポート期限 | リンク
|:--------|------------------|-----------------|------
| [EF Core 5.0](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore) | .NET Standard 2.1 | 2022 年 2 月中旬 | [お知らせ](https://devblogs.microsoft.com/dotnet/announcing-the-release-of-ef-core-5-0/) / [破壊的変更](xref:core/what-is-new/ef-core-5.0/breaking-changes)
| [EF Core 3.1](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/3.1.10) | .NET Standard 2.0 | 2022 年 12 月 3 日 (LTS) | [お知らせ](https://devblogs.microsoft.com/dotnet/announcing-entity-framework-core-3-1-and-entity-framework-6-4/)
| ~~[EF Core 3.0](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/3.0.3)~~ | .NET Standard 2.1 | 2020 年 3 月 3 日に期限切れ | [お知らせ](https://devblogs.microsoft.com/dotnet/announcing-ef-core-3-0-and-ef-6-3-general-availability/) / [破壊的変更](xref:core/what-is-new/ef-core-3.x/breaking-changes)
| ~~[EF Core 2.2](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/2.2.6)~~ | .NET Standard 2.0 | 2019 年 12 月 23 日に期限切れ | [お知らせ](https://devblogs.microsoft.com/dotnet/announcing-entity-framework-core-2-2/)
| [EF Core 2.1](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/2.1.14) | .NET Standard 2.0 | 2021 年 8 月 21 日 (LTS) | [お知らせ](https://devblogs.microsoft.com/dotnet/announcing-entity-framework-core-2-1/)
| ~~[EF Core 2.0](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/2.0.3)~~ | .NET Standard 2.0 | 2018 年 10 月 1 日に期限切れ | [お知らせ](https://devblogs.microsoft.com/dotnet/announcing-entity-framework-core-2-0/)
| ~~[EF Core 1.1](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/1.1.6)~~ | .NET Standard 1.3 | 2019 年 6 月 27 日に期限切れ | [お知らせ](https://devblogs.microsoft.com/dotnet/announcing-entity-framework-core-1-1/)
| ~~[EF Core 1.0](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/1.0.6)~~ | .NET Standard 1.3 | 2019 年 6 月 27 日に期限切れ | [お知らせ](https://devblogs.microsoft.com/dotnet/entity-framework-core-1-0-0-available/)

各 EF Core リリースでサポートされている特定のプラットフォームの詳細については、「[サポートされているプラットフォーム](xref:core/miscellaneous/platforms)」を参照してください。

サポートの有効期限と長期的なサポート (LTS) のリリースについては、[.NET のサポート ポリシー](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)を参照してください。

## <a name="guidance-on-updating-to-new-releases"></a>新しいリリースへの更新に関するガイダンス

* サポートされているリリースでは、セキュリティおよびその他の重要なバグが修正されています。 常に、特定のリリースの最新のパッチを使用してください。 たとえば、EF Core 2.1 では、使用可能な最高の "x" に対して 2.1. x を使用します。
* メジャー バージョンの更新 (たとえば、EF Core 2 から EF Core 3) には、多くの場合、破壊的変更が含まれています。 メジャー バージョン間で更新を行う場合は、完全なテストを行うことをお勧めします。 破壊的変更に対処するためのガイダンスについては、上の破壊的変更に関するリンクをご覧ください。
* マイナー バージョンの更新には、通常、破壊的変更は含まれていません。 ただし、新しい機能によって回帰が生じる可能性があるため、やはり完全なテストを行うことをお勧めします。

## <a name="release-planning-and-schedules"></a>リリース計画とスケジュール

EF Core のリリースは、[.NET Core の出荷スケジュール](https://github.com/dotnet/core/blob/master/roadmap.md)に合わせて行われます。

通常、修正プログラムのリリースは月単位で出荷されますが、長いリード タイムがあります。
Microsoft では、これを改善するために取り組んでいます。

各リリースで出荷される内容の決定方法の詳細については、「[リリースの計画プロセス](xref:core/what-is-new/release-planning)」を参照してください。
通常、次のメジャー リリースまたはマイナー リリースよりもさらに詳細な計画を行うことはありません。

## <a name="ef-core-60"></a>EF Core 6.0

次に予定されている安定版リリースは、**2021 年 11 月** にスケジュールされている **EF Core 6.0** です。

[EF Core 6.0 の大まかな計画](xref:core/what-is-new/ef-core-6.0/plan)は、ドキュメントに記載されている[リリース計画プロセス](xref:core/what-is-new/release-planning)に従って作成されています。

計画に関するフィードバックは重要です。
イシューの重要度を示す最善の方法は、GitHub でそのイシューに投票 (上向きの親指 👍) することです。
このデータが、次のリリースの計画プロセスに取り込まれます。

### <a name="get-it-now"></a>今すぐ入手する

EF Core 6.0 のパッケージは **今すぐ**、

* [デイリー ビルド](https://github.com/dotnet/aspnetcore/blob/master/docs/DailyBuilds.md)として入手できます。
  * すべての最新の機能とバグの修正。 一般に非常に安定しています。75,000 回以上のテストが各ビルドに対して実行されます。

また、進行状況に応じて、頻繁なプレビューが NuGet にプッシュされます。 デイリー ビルドに対してプレビューの遅れはありますが、対応する ASP.NET Core および .NET Core のプレビューで機能することがテストされます。

デイリー ビルドのプレビューを使用することは、イシューを発見し、できるだけ早くフィードバックを提供するための優れた方法です。
フィードバックが早く得られるほど、次の公式リリースまでに対処できる可能性が高くなります。
