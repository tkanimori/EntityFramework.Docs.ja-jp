---
title: Entity Framework Core 6.0 の計画
description: EF Core 6.0 に対して計画されているテーマと機能
author: ajcvickers
ms.date: 01/12/2021
uid: core/what-is-new/ef-core-6.0/plan
ms.openlocfilehash: 612461bc6ad30778baa5c6d10dda5cabac91dcb2
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129551"
---
# <a name="plan-for-entity-framework-core-60"></a>Entity Framework Core 6.0 の計画

[計画プロセス](xref:core/what-is-new/release-planning)で説明されているように、Entity Framework Core (EF Core) 6.0 リリースの計画には関係者からの意見が取り入れられています。

これまでのリリースとは異なり、この計画では、6.0 リリースのすべての作業を対象にすることは予定されていません。 代わりに、このリリースで投資する場所と方法を示しますが、リリースの作業を行いながらフィードバックを集めて学習し、柔軟に新しい作業の範囲を調整したり作業に取り入れたりします。

> [!IMPORTANT]
> このプランは確約ではありません。 さらに学習して進化する出発点となります。 現在、6.0 で計画されていない機能が盛り込まれる可能性があります。 現在、6.0 で計画されている機能が除外される可能性があります。

## <a name="general-information"></a>一般情報

### <a name="version-number-and-release-date"></a>バージョン番号とリリース日

EF Core 6.0 は、5.0 EF Core に続く次のリリースであり、現時点では、2021 年 11 月に .NET 6 と同時にリリースされる予定です。

### <a name="supported-platforms"></a>[サポートされているプラットフォーム]

現在、EF Core 6.0 の対象は .NET 5 です。 これは、リリースが近くなった時点で .NET 6 に更新される可能性があります。 EF Core 6.0 では、.NET Standard のどのバージョンも対象になっていません。詳細については、「[.NET Standard の将来](https://devblogs.microsoft.com/dotnet/the-future-of-net-standard/)」を参照してください。

EF Core 6.0 は、.NET Framework では実行されません。

EF Core 6.0 は、[長期的なサポート (LTS) リリース](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)として .NET 6 と連携します。

### <a name="breaking-changes"></a>重大な変更

EF Core と .NET プラットフォームの両方の機能強化が継続的に行われているため、EF Core 6.0 に含まれる[破壊的変更](xref:core/what-is-new/ef-core-6.0/breaking-changes)の数は多くありません。 目標は、アプリケーションの大部分を中断せずに更新できるようにすることです。

## <a name="themes"></a>テーマ

EF Core 6.0 では、次の領域の基礎を形成するために大きな投資が注ぎ込まれます。

## <a name="highly-requested-features"></a>要求の多かった機能

これまでと同じように、[計画プロセス](xref:core/what-is-new/release-planning)の基になっているものは主に、[GitHub でのフィーチャーへの投票 (👍)](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc) です。 EF Core 6.0 では、次のような要求の多かったフィーチャーについての作業が行われる予定です。

### <a name="sql-server-temporal-tables"></a>SQL Server のテンポラル テーブル

追跡: [#4693](https://github.com/dotnet/efcore/issues/4693)

状態: 開始前

規模: 大

テンポラル テーブルを使用すると、通常のテーブルのように格納された最新のデータのみに対してではなく、テーブルに格納された _任意の時点_ のデータに対するクエリの実行をサポートします。 EF Core 6.0 の場合、Migrations によってテンポラル テーブルを作成できるだけでなく、LINQ クエリを使用してデータにアクセスすることもできます。

この作業の初期のスコープは、[イシューで説明](https://github.com/dotnet/efcore/issues/4693#issuecomment-625048974)されています。 リリース時のフィードバックに基づいて、サポートが追加される可能性があります。

### <a name="json-columns"></a>JSON 列

追跡: [#4021](https://github.com/dotnet/efcore/issues/4021)

状態: 開始前

規模: Medium

この機能により、任意のデータベース プロバイダーによって実装できる JSON サポートのための一般的なメカニズムとパターンが導入されます。 コミュニティと協力して、[Npgsql](https://github.com/npgsql/efcore.pg) および [Pomelo MySQL](https://github.com/PomeloFoundation/Pomelo.EntityFrameworkCore.MySql) の既存の実装を調整し、SQL Server と SQLite のサポートも追加します。

### <a name="columnattributeorder"></a>ColumnAttribute.Order

追跡: [#10059](https://github.com/dotnet/efcore/issues/10059)

状態: 開始前

規模: 小

この機能を使用すると、Migrations または `EnsureCreated` で **テーブルを作成する** ときに、列を任意の順序にすることができます。 既存のテーブルの列の順序を変更するには、テーブルを再構築する必要があることに注意してください。この機能のサポートは、EF Core のリリースでは予定されていません。

## <a name="performance"></a>パフォーマンス

EF Core は一般に EF6 より高速ですが、パフォーマンスの大幅な向上が可能な領域がまだあります。 これらの領域のいくつかには EF Core 6.0 で取り組むことが計画されていますが、パフォーマンスのインフラストラクチャとテストも改善されています。

このテーマに関しては反復的な調査が何回も行われる予定であり、それによりリソースを集中させる場所が明らかになります。 最初は次の作業が計画されています。

### <a name="performance-infrastructure-and-new-tests"></a>パフォーマンス インフラストラクチャと新しいテスト

状態: 開始前

規模: Medium

EF Core のコードベースには、毎日実行されるパフォーマンス ベンチマークのセットが既に含まれています。 6\.0 では、これらのテストのためのインフラストラクチャを強化し、新しいテストを追加することが計画されています。 また、メインラインのパフォーマンス シナリオのプロファイリングを行い、簡単に実行できるものを見つけて解決します。

### <a name="compiled-models"></a>コンパイル済みモデル

追跡: [#1906](https://github.com/dotnet/efcore/issues/1906)

状態: 開始前

規模: 特大

コンパイル済みモデルを使用すると、EF モデルのコンパイル済み形式を生成できます。 これにより、起動パフォーマンスが向上すると共に、モデルにアクセスするときのパフォーマンスが一般に向上します。

### <a name="techempower-fortunes"></a>TechEmpower の Fortunes

追跡: [#23611](https://github.com/dotnet/efcore/issues/23611)

状態: 開始前

規模: 特大

何年も、業界標準の [TechEmpower ベンチマーク](https://www.techempower.com/benchmarks/)を .NET 上で PostgreSQL データベースに対して実行してきました。 [Fortunes ベンチマーク](https://www.techempower.com/benchmarks/#section=data-r19&hw=ph&test=fortune)は、EF のシナリオに特に関連しています。 このベンチマークには、次のようなさまざまなバリエーションがあります。

- ADO.NET を直接使用する実装。 これは、ここで示す 3 つのものの中で最も速い実装です。
- [Dapper](https://www.nuget.org/packages/Dapper/) を使用する実装。 これは ADO.NET を直接使用するより遅くなりますが、それでも高速です。
- EF Core を使用する実装。 現在、これは 3 つの中で最も遅い実装です。

EF Core 6.0 の目標は、EF Core のパフォーマンスを TechEmpower Fortunes ベンチマークでの Dapper のパフォーマンスと匹敵するものにすることです。 (これは簡単なことではありませんが、できるだけ近付けるように最善を尽くします)。

### <a name="linkeraot"></a>リンカー/AOT

追跡: [#10963](https://github.com/dotnet/efcore/issues/10963)

状態: 開始前

規模: Medium

EF Core では、大量のランタイム コードの生成が実行されます。 これは、Xamarin や Blazor のようなリンカー ツリーのシェイキングに依存するアプリ モデルや、iOS のような動的コンパイルを実行できないプラットフォームでは課題になっています。 EF Core 6.0 の一部としてこの領域の調査を続け、可能な限り改善目標を達成します。 ただし、6.0 の期間内にギャップが完全に埋まることは期待していません。

## <a name="migrations-and-deployment"></a>移行と展開

[EF Core 5.0 で行われた調査](xref:core/what-is-new/ef-core-5.0/plan#migrations-and-deployment-experience)から継続して、移行の管理とデータベースの展開に関するサポートを強化することを計画しています。 これに関しては、2 つの主要な領域があります。

### <a name="migrations-bundles"></a>移行バンドル

追跡: [#19693](https://github.com/dotnet/efcore/issues/19693)

状態: 開始前

規模: Medium

移行バンドルは、運用データベースに移行を適用する自己完結型の実行可能ファイルです。 その動作は `dotnet ef database update` と同じですが、必要なものがすべて 1 つの実行可能ファイルに含まれているため、SSH、Docker、PowerShell の展開がはるかに簡単になるはずです。

### <a name="managing-migrations"></a>移行の管理

追跡: [#22945](https://github.com/dotnet/efcore/issues/22945)

状態: 開始前

規模: 大

1 つのアプリケーションに対して作成される移行の数が増加して負担になる可能性があります。 また、これらの移行は、必要ない場合でも、アプリケーションと共に展開されることがよくあります。 EF Core 6.0 では、ツールおよびプロジェクトとアセンブリの管理の向上により、これを改善する予定です。 対応を予定している特定の 2 つのイシューは、[多くの移行を 1 つにまとめる](https://github.com/dotnet/efcore/issues/2174)ことと、[クリーンなモデルのスナップショットを再生成する](https://github.com/dotnet/efcore/issues/18557)ことです。

## <a name="improve-existing-features-and-fix-bugs"></a>既存のフィーチャーの改善とバグの修正

[6.0.0 のマイルストーンに割り当てられているイシューまたはバグ](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A6.0.0)は、現在、このリリースで計画されています。 これには、多くの小さな機能強化とバグ修正が含まれます。

### <a name="ef6-query-parity"></a>EF6 のクエリのパリティ

追跡: ["ef6-parity" というラベルが付けられていて 6.0 のマイルストーンに含まれるイシュー](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+label%3Aef6-parity+milestone%3A6.0.0)

状態: 開始前

規模: 大

EF Core 5.0 の場合、EF6 でサポートされていないパターンに加えて、EF6 によってサポートされているほとんどのクエリ パターンがサポートされています。 EF Core 6.0 の場合は、ギャップを埋め、サポートされる EF Core クエリを、サポートされている EF6 クエリの真のスーパーセットにすることが計画されています。 これはギャップの調査によって進められますが、[後に FirstOrDefault が続く GroupBy を変換する](https://github.com/dotnet/efcore/issues/12088)のような GroupBy のイシューや、[プリミティブ](https://github.com/dotnet/efcore/issues/11624)型および[マップされていない](https://github.com/dotnet/efcore/issues/10753)型に対する生の SQL クエリが既に含まれています。  

### <a name="value-objects"></a>値オブジェクト

追跡: [#9906](https://github.com/dotnet/efcore/issues/9906)

状態: 開始前

規模: Medium

以前は、[集計のサポート](https://www.martinfowler.com/bliki/DDD_Aggregate.html)を目的として、エンティティを所有するのはチーム ビューであり、[値オブジェクト](https://www.martinfowler.com/bliki/ValueObject.html)に対する妥当な近似でもありました。 経験上、そうではないことがわかりました。 そのため、EF Core 6.0 では、ドメイン駆動設計での値オブジェクトのニーズに焦点を当てて、より優れたエクスペリエンスの導入が計画されています。 この方法は、所有エンティティではなく値コンバーターに基づいています。

この作業の最初の対象は、[複数の列にマップする値コンバーター](https://github.com/dotnet/efcore/issues/13947)を可能にすることです。 リリース時のフィードバックに基づいて、サポートが追加される可能性があります。

### <a name="cosmos-database-provider"></a>Cosmos データベース プロバイダー

追跡: ["area-cosmos" というラベルが付けられていて 6.0 のマイルストーンに含まれるイシュー](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A6.0.0+label%3Aarea-cosmos)

状態: 開始前

規模: 大

EF Core 6.0 で Cosmos プロバイダーに対して行う機能強化に関するフィードバックの収集が積極的に行われています。 情報が増えたらこのドキュメントを更新します。 現時点では、必要な Cosmos のフィーチャーに投票 (👍) してください。

### <a name="expose-model-building-conventions-to-applications"></a>モデル構築規則をアプリケーションに公開する

追跡: [#214](https://github.com/dotnet/efcore/issues/214)

状態: 開始前

規模: 中

EF Core では、.NET 型からモデルを構築するために規則のセットが使用されます。 これらの規則は、現在、データベース プロバイダーによって制御されています。 EF Core 6.0 では、アプリケーションでこれらの規則に接続して変更できるようにする予定です。

### <a name="zero-bug-balance-zbb"></a>残バグ ゼロ (ZBB)

追跡: [6.0 のマイルストーンで `type-bug` というラベルが付けられているイシュー](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A6.0.0+label%3Atype-bug+)

状態: 進行中

規模: 大

EF Core 6.0 の期間中に、すべての未解決バグを修正することを計画しています。 いくつかの留意事項:

- これは、特に [type-bug](https://github.com/dotnet/efcore/issues?q=is%3Aissue+label%3Atype-bug) というラベルが付いているイシューに当てはまります。
- バグを適切に修正するために設計の変更や新しい機能が必要になる場合など、例外があります。 そのようなイシューは、`blocked` というラベルでマークされます。
- GA/RTM リリースに近付いたら一般に行われるように、必要な場合はリスクに基づいてバグをそのままにします。

### <a name="miscellaneous-features"></a>その他の機能

追跡: [6.0 のマイルストーンで `type-enhancement` というラベルが付けられているイシュー](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A6.0.0+label%3Atype-enhancement)

状態: 進行中

規模: 大

EF 6.0 では他に次のようなフィーチャーが計画されていますが、これらに限定されません。

- [非ナビゲーション コレクションに対するクエリを分割する](https://github.com/dotnet/efcore/issues/21234)
- [リバース エンジニアリングで単純な結合テーブルを検出し、多対多リレーションシップを作成する](https://github.com/dotnet/efcore/issues/22475)
- [SQLite と SQL Server での完全な自由テキスト検索を完成させる](https://github.com/dotnet/efcore/issues/4823)
- [SQL Server の空間インデックス](https://github.com/dotnet/efcore/issues/12538)
- [モデル内の特定の型のプロパティに既定の変換を指定するためのメカニズムと API](https://github.com/dotnet/efcore/issues/10784)
- [ADO.NET から新しいバッチ API を使用する](https://github.com/dotnet/efcore/issues/18990)

## <a name="net-integration"></a>.NET 統合

EF Core チームは、関連性はあるが独立した複数のテクノロジについての作業も行っています。 具体的には、以下の作業が予定されています。

### <a name="enhancements-to-systemdata"></a>System.Data の機能強化

追跡: [6.0 のマイルストーンで `area-System.Data` というラベルが付けられている dotnet\runtime リポジトリのイシュー](https://github.com/dotnet/runtime/issues?q=is%3Aopen+is%3Aissue+label%3Aarea-System.Data+milestone%3A6.0.0)

状態: 開始前

規模: 大

この作業には次のものが含まれます。

- 新しい[バッチ処理 API](https://github.com/dotnet/runtime/issues/28633) の実装。
- ADO.NET の理解と進化のための、他の .NET チームやコミュニティとの継続的な連携。
- [System.Data.* コンポーネントでのトレースのために DiagnosticSource を標準化する](https://github.com/dotnet/runtime/issues/22336)。

### <a name="enhancements-to-microsoftdatasqlite"></a>Microsoft.Data.Sqlite の機能強化

追跡: [6.0 のマイルストーンで `type-enhancement` および `area-adonet-sqlite` というラベルが付けられているイシュー](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+milestone%3A6.0.0+label%3Atype-enhancement+label%3Aarea-adonet-sqlite)

状態: 進行中

規模: Medium

パフォーマンスのための[接続プール](https://github.com/dotnet/efcore/issues/13837)や[準備されたステートメント](https://github.com/dotnet/efcore/issues/14044)など、Microsoft.Data.Sqlite に関していくつかの小さな改良が計画されています。

### <a name="nullable-reference-types"></a>null 許容参照型

追跡: [#14150](https://github.com/dotnet/efcore/issues/14150)

状態: 進行中

規模: 大

[null 許容参照型](/dotnet/csharp/nullable-references)を使用するために EF Core コードに注釈を付けます。

## <a name="experiments-and-investigations"></a>実験と調査

EF チームは、EF Core 6.0 の期間中に、2 つの領域での実験と調査に時間を費やすことを計画しています。 これは学習プロセスであるため、6.0 リリースでは具体的な成果物は計画されていません。

### <a name="sqlservercore"></a>SqlServer.Core

追跡: [.NET Data Lab リポジトリで](https://github.com/dotnet/datalab/)

状態: 開始前

規模: 継続

[Microsoft.Data.SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient/) は、SQL Server 用の完全な機能を備えた ADO.NET データベース プロバイダーです。 .NET Core と .NET Framework の両方で、広範な SQL Server 機能がサポートされています。 しかし、その動作間での多くの複雑なやり取りが含まれる、大規模で古いコードベースでもあります。 そのため、新しい .NET Core フィーチャーを使用することにより得られる可能性がある利点を調査するのが難しくなっています。 したがって、.NET 用の高パフォーマンス SQL Server ドライバーの可能性を判断するための実験を、コミュニティと協力して開始しています。

> [!IMPORTANT]
> Microsoft.Data.SqlClient への取り組みは変わりません。 EF Core であってもなくても、引き続き SQL Server と SQL Azure の両方に接続するための推奨される方法です。 新しく導入される SQL Server のフィーチャーは引き続きサポートされます。

### <a name="graphql"></a>GraphQL

状態: 開始前

規模: 継続

[GraphQL](https://graphql.org/) は、過去数年間にさまざまなプラットフォームで広く使用されるようになっています。 これに関する調査を行い、.NET でのエクスペリエンスを向上させる方法を見つけることを計画しています。 これには、既存のエコシステムを理解してサポートするためのコミュニティとの連携が含まれます。 また、既存の作業への貢献の形式や、Microsoft スタックでの無償要素の開発での、Microsoft による具体的な取り組みも含まれる場合があります。

### <a name="dataverse-formerly-common-data-services"></a>DataVerse (旧称 Common Data Services)

状態: 開始前

規模: 継続

[DataVerse](/powerapps/maker/data-platform/data-platform-intro) は、ビジネス アプリケーションを短期間で開発できるように設計された列ベースのデータ ストアです。 バイナリ オブジェクト (BLOB) などの複雑なデータ型が自動的に処理され、組織や連絡先などの組み込みのエンティティとリレーションシップが用意されています。 SDK は存在しますが、EF Core プロバイダーを利用することで、開発者には高度な LINQ クエリの使用、作業単位の利用、一貫性のあるデータ アクセス API などのメリットがあります。 チームは、DataVerse に接続する .NET 開発者エクスペリエンスを向上させるためのさまざまな方法を検討します。

## <a name="below-the-cut-line"></a>対象外

追跡: [`consider-for-current-release` というラベルが付けられている問題](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+label%3Aconsider-for-current-release)

これらは、現時点では 6.0 リリースで予定されて **いない** バグ修正および機能強化ですが、リリース全体を通したフィードバックと上記の作業の進行状況に基づいて検討されます。 これらのイシューは、EF Core 6.0 に取り込まれ、自動的に次のリリースの候補になる可能性があります。

また、計画を立てるときは常に、[最も投票が多かった問題](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc)が考慮されています。 これらの問題をリリースから切り捨てることは常に苦痛ですが、保有するリソースにとって現実的な計画を立てる必要があります。 必要なフィーチャーに投票 (👍) したことを確認してください。

## <a name="suggestions"></a>推奨事項

計画に関するフィードバックは重要です。 イシューの重要度を示す最善の方法は、GitHub でそのイシューに投票 (👍) することです。 このデータが、次のリリースの[計画プロセス](xref:core/what-is-new/release-planning)に取り込まれます。
