---
title: EF Core のリリースと計画
description: 現在の EF Core のリリースと、今後のリリースのスケジュール/計画の詳細
author: ajcvickers
ms.date: 06/11/2020
uid: core/what-is-new/index
ms.openlocfilehash: 7d4f37c227feec0fad9f828999680f834ddd2d4e
ms.sourcegitcommit: ebfd3382fc583bc90f0da58e63d6e3382b30aa22
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/25/2020
ms.locfileid: "85370241"
---
# <a name="ef-core-releases-and-planning"></a>EF Core のリリースと計画

## <a name="stable-releases"></a>安定版リリース

| リリース | [対象とする Framework] | サポート期限 | リンク
|:--------|------------------|-----------------|------
| [EF Core 3.1](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore) | .NET Standard 2.0 | 2022 年 12 月 3 日 (LTS) | [お知らせ](https://devblogs.microsoft.com/dotnet/announcing-entity-framework-core-3-1-and-entity-framework-6-4/)
| ~~[EF Core 3.0](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/3.0.3)~~ | .NET Standard 2.1 | 2020 年 3 月 3 日に期限切れ | [お知らせ](https://devblogs.microsoft.com/dotnet/announcing-ef-core-3-0-and-ef-6-3-general-availability/) / [破壊的変更](ef-core-3.0/breaking-changes.md)
| ~~[EF Core 2.2](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/2.2.6)~~ | .NET Standard 2.0 | 2019 年 12 月 23 日に期限切れ | [お知らせ](https://devblogs.microsoft.com/dotnet/announcing-entity-framework-core-2-2/)
| [EF Core 2.1](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/2.1.14) | .NET Standard 2.0 | 2021 年 8 月 21 日 (LTS) | [お知らせ](https://devblogs.microsoft.com/dotnet/announcing-entity-framework-core-2-1/)
| ~~[EF Core 2.0](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/2.0.3)~~ | .NET Standard 2.0 | 2018 年 10 月 1 日に期限切れ | [お知らせ](https://devblogs.microsoft.com/dotnet/announcing-entity-framework-core-2-0/)
| ~~[EF Core 1.1](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/1.1.6)~~ | .NET Standard 1.3 | 2019 年 6 月 27 日に期限切れ | [お知らせ](https://devblogs.microsoft.com/dotnet/announcing-entity-framework-core-1-1/)
| ~~[EF Core 1.0](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/1.0.6)~~ | .NET Standard 1.3 | 2019 年 6 月 27 日に期限切れ | [お知らせ](https://devblogs.microsoft.com/dotnet/entity-framework-core-1-0-0-available/)

各 EF Core リリースでサポートされている特定のプラットフォームの詳細については、「[サポートされているプラットフォーム](../platforms/index.md)」を参照してください。

サポートの有効期限と長期的なサポート (LTS) のリリースについては、[.NET のサポート ポリシー](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)を参照してください。

## <a name="guidance-on-updating-to-new-releases"></a>新しいリリースへの更新に関するガイダンス

* サポートされているリリースでは、セキュリティおよびその他の重要なバグが修正されています。 常に、特定のリリースの最新のパッチを使用してください。 たとえば、EF Core 2.1 の場合は、2.1.14 を使用します。
* メジャー バージョンの更新 (たとえば、EF Core 2 から EF Core 3) には、多くの場合、破壊的変更が含まれています。 メジャー バージョン間で更新を行う場合は、完全なテストを行うことをお勧めします。 破壊的変更に対処するためのガイダンスについては、上の破壊的変更に関するリンクをご覧ください。
* マイナー バージョンの更新には、通常、破壊的変更は含まれていません。 ただし、新しい機能によって回帰が生じる可能性があるため、やはり完全なテストを行うことをお勧めします。

## <a name="release-planning-and-schedules"></a>リリース計画とスケジュール

EF Core のリリースは、[.NET Core の出荷スケジュール](https://github.com/dotnet/core/blob/master/roadmap.md)に合わせて行われます。

通常、修正プログラムのリリースは月単位で出荷されますが、長いリード タイムがあります。
Microsoft では、これを改善するために取り組んでいます。

各リリースで出荷される内容の決定方法の詳細については、「[リリースの計画プロセス](release-planning.md)」を参照してください。
通常、次のメジャー リリースまたはマイナー リリースよりもさらに詳細な計画を行うことはありません。

## <a name="ef-core-50"></a>EF Core 5.0

次に予定されている安定版リリースは、2020 年 11 月にスケジュールされている **EF Core 5.0** です。

[EF Core 5.0 の大まかな計画](xref:core/what-is-new/ef-core-5.0/plan)は、[リリース計画プロセス](release-planning.md)のドキュメントに従って作成されています。

計画に関するフィードバックは重要です。
イシューの重要度を示す最善の方法は、GitHub でそのイシューに投票 (上向きの親指 👍) することです。
このデータが、次のリリースの計画プロセスに取り込まれます。

### <a name="get-it-now"></a>今すぐ入手しましょう

EF Core 5.0 のパッケージは**今すぐ**、

* [デイリー ビルド](https://github.com/dotnet/aspnetcore/blob/master/docs/DailyBuilds.md)として入手できます。
  * すべての最新の機能とバグの修正。 一般に非常に安定しています。57,000 以上のテストが各ビルドに対して実行されます。
* [NuGet でのプレビュー](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore)
  * デイリー ビルドに対して遅れはありますが、対応する ASP.NET Core および .NET Core のプレビューで機能することがテストされます。

デイリー ビルドのプレビューを使用することは、イシューを発見し、できるだけ早くフィードバックを提供するための優れた方法です。
フィードバックが早く得られるほど、次の公式リリースまでに対処できる可能性が高くなります。
