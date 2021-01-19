---
title: Entity Framework Core 5.0 の計画
description: Entity Framework Core 5.0 で計画されている機能
author: ajcvickers
ms.date: 08/22/2020
uid: core/what-is-new/ef-core-5.0/plan
ms.openlocfilehash: ba56a5f3c79dacc25b3402be114c57067da49433
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129045"
---
# <a name="plan-for-entity-framework-core-50"></a>Entity Framework Core 5.0 の計画

> [!IMPORTANT]
> EF Core 5.0 が[リリースされました](xref:core/what-is-new/index)。 このページは、プランの履歴レコードとして残ります。

[計画プロセス](xref:core/what-is-new/release-planning)で説明されているように、EF Core 5.0 リリースの暫定的な計画には関係者からの意見が取り込まれています。

> [!IMPORTANT]
> この計画はまだ進行中です。 ここで説明されている内容は確約されたものではありません。 この計画は、さらに学習して発展する出発点となります。 現在、5.0 に対して計画されていない機能が盛り込まれる可能性があります。 現在、5.0 に対して計画されている機能が除外される可能性があります。

## <a name="general-information"></a>一般情報

### <a name="version-number-and-release-date"></a>バージョン番号とリリース日

現在、EF Core 5.0 は、[.NET 5.0 と同時に](https://devblogs.microsoft.com/dotnet/introducing-net-5/)リリースされる予定です。 "5.0" というバージョンは、.NET 5.0 に合わせて選択されました。

### <a name="supported-platforms"></a>サポートされているプラットフォーム

EF Core 5.0 は、.NET 5.0 を含む任意の .NET Standard 2.1 プラットフォームで動作するように計画されています。 これは、より全般的な .NET 全体での [.NET Core へのプラットフォーム収束](https://devblogs.microsoft.com/dotnet/introducing-net-5/)の一部です。

EF Core 5.0 は、.NET Framework では実行されません。

### <a name="breaking-changes"></a>互換性に影響する変更

EF Core 5.0 には[破壊的変更](xref:core/what-is-new/ef-core-5.0/breaking-changes)がいくつか含まれていますが、EF Core 3.0 のときほど深刻なものは多くありません。 目標は、アプリケーションの大部分を中断せずに更新できるようにすることです。

データベース プロバイダーに対して、破壊的変更がいくつかあることが予想されます (特に TPT のサポートに関して)。 ただし、5.0 対応にプロバイダーを更新する作業は、3.0 の更新に要した時間より短いものと予想されます。

## <a name="themes"></a>テーマ

EF Core 5.0 への大規模な投資の基礎となる、いくつかの主要な領域やテーマが抽出されています。

## <a name="fully-transparent-many-to-many-mapping-by-convention"></a>慣例による完全に透過的な多対多マッピング

開発リーダー: @smitpatel、@AndriySvyryd、@lajones

追跡: [#10508](https://github.com/dotnet/efcore/issues/10508)

規模: L

状態: 完了

多対多は、GitHub バックログで[最も要求の多かった機能](https://github.com/dotnet/efcore/issues/1368)です (506 票)。

多対多リレーションシップのサポートは、次の 3 つの主要領域に分けることができます。

* スキップ ナビゲーションのプロパティ -- 次のテーマで取り上げます。
* property-bag エンティティ型。 これにより、標準の CLR 型 (例: `Dictionary`) をエンティティ インスタンスに使用できるようになり、エンティティ型ごとに明示的な CLR 型は不要になります。 追跡: [#9914](https://github.com/dotnet/efcore/issues/9914)
* 多対多リレーションシップを簡単に構成するための Sugar。

スキップ ナビゲーションのサポートに加えて、完全なエクスペリエンスが提供されるように、多対多のその他の領域を EF Core 5.0 に導入しました。

## <a name="many-to-many-navigation-properties-aka-skip-navigations"></a>多対多のナビゲーション プロパティ (別名 "スキップ ナビゲーション")

開発リーダー: @smitpatel、@AndriySvyryd

追跡: [#19003](https://github.com/dotnet/efcore/issues/19003)

規模: L

状態: 完了

最初のテーマで説明したように、多対多のサポートには複数の側面があります。
このテーマでは、特にスキップ ナビゲーションの使用を追跡します。
多対多のサポートを望んでいるユーザーにとって最も大きな阻害要因は、クエリなどのビジネス ロジックにおいて、結合テーブルを参照せずに、"自然な" リレーションシップを使用できないことであると考えています。
結合テーブルのエンティティ型がまだ存在する場合がありますが、ビジネス ロジックを妨げることはないはずです。

## <a name="table-per-type-tpt-inheritance-mapping"></a>Table-Per-Type (TPT) の継承のマッピング

開発リーダー: @AndriySvyryd、@smitpatel

追跡: [#2266](https://github.com/dotnet/efcore/issues/2266)

規模: XL

状態: 完了

TPT の作業が行われているのは、要求の多かった機能であることと (289 票、全体で 3 番目)、.NET 5 の全体的な計画の基本特性に適していると考えられる低レベルの変更が必要であるためです。 これにより、データベース プロバイダーに対して破壊的変更が行われることが予想されますが、3.0 で必要であった変更ほど大がかりにはならないはずです。

## <a name="filtered-include"></a>フィルター処理されたインクルード

開発リーダー: @maumar

追跡: [#1833](https://github.com/dotnet/efcore/issues/1833)

規模: M

状態: 完了

フィルター処理されたインクルードは、要求の多かった機能であり (376 票、全体で 2 番目)、作業量は多くなく、現在はモデル レベルのフィルターやさらに複雑なクエリを必要とする多くのシナリオの障害が取り除かれたり、実現がいっそう簡単になるものと考えられます。

## <a name="split-include"></a>インクルードの分割

開発リーダー: @smitpatel

追跡: [#20892](https://github.com/dotnet/efcore/issues/20892)

規模: L

状態: 完了

EF Core 3.0 では、特定の LINQ クエリに対して単一の SQL クエリを作成するための既定の動作が変更されました。
これにより、複数のコレクション向けのインクルードを使用するクエリのパフォーマンスが大幅に低下していました。

EF Core 5.0 では、新しい既定の動作を保持しています。
ただし、EF Core 5.0 では、単一のクエリによってパフォーマンスが低下しているコレクションのインクルードで複数クエリの生成が許可されます。

## <a name="required-one-to-one-dependents"></a>必須の一対一の依存関係

開発リーダー: @AndriySvyryd、@smitpatel

追跡: [#12100](https://github.com/dotnet/efcore/issues/12100)

規模: M

状態: 完了

EF Core 3.0 では、所有されている型を含むすべての依存関係は省略可能です (たとえば、Person.Address は null にすることができます)。 EF Core 5.0 では、必要に応じて依存関係を構成できます。

## <a name="rationalize-totable-toquery-toview-fromsql-etc"></a>ToTable、ToQuery、ToView、FromSql などの合理化

開発リーダー: @AndriySvyryd、@smitpatel

追跡: [#17270](https://github.com/dotnet/efcore/issues/17270)

規模: L

状態: 完了

以前のリリースでは、未加工の SQL、キーなしの型、および関連する領域のサポートを強化する作業が行われました。 ただし、すべてが連携して全体として機能するには、まだギャップと不整合の両方があります。 5\.0 での目標は、これらを修正し、異なる型のエンティティとそれに関連付けられたクエリおよびデータベース アイテムを定義、移行、使用するための優れたエクスペリエンスを作成することです。 これには、コンパイル済みクエリ API の更新が含まれる場合もあります。

現在の機能の一部は制限が緩すぎるために簡単に誤る可能性があるので、この項目ではアプリケーション レベルでいくつか破壊的変更が発生する可能性があることに注意してください。 このような機能の一部を遮断し、代わりの方法についてのガイダンスを提供することになると思われます。

## <a name="general-query-enhancements"></a>一般的なクエリの機能強化

開発リーダー: @smitpatel、@maumar

追跡: [5.0 のマイルストーンで `area-query` というラベルが付けられている問題](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-query+milestone%3A5.0.0+)

規模: XL

状態: 完了

クエリ変換コードは、EF Core 3.0 で大幅に書き換えられました。 一般に、このため、クエリ コードはより堅牢な状態になっています。 5\.0 では、TPT とスキップ ナビゲーション プロパティをサポートするために必要な部分を除いて、クエリに関する大きな変更が行われる予定はありません。 ただしそれでも、3.0 の見直しから残っているいくつかの技術的負債を修正するためにかなりの作業が必要です。 また、多くのバグの修正と、クエリ全体のエクスペリエンスをさらに向上させるための小さな機能拡張の実装も予定されています。

## <a name="migrations-and-deployment-experience"></a>移行と展開のエクスペリエンス

開発リーダー: @bricelam

追跡: [#19587](https://github.com/dotnet/efcore/issues/19587)

規模: L

状態: スコープ設定済み/完了

スコーピング:[移行バンドル機能](https://github.com/dotnet/efcore/issues/19693)は、EF Core 5.0 リリースの後まで延期されました。 ただし、その他いくつかの[移行に関連する特定目的の機能強化](https://github.com/dotnet/efcore/issues/19587#issuecomment-668794460)は、EF Core 5.0 に含まれます。

現在は、多くの開発者が、アプリケーションの起動時にデータベースを移行しています。 これは簡単ですが、次の理由で推奨されません。

* 複数のスレッド、プロセス、サーバーによって、データベースの移行が同時に試みられる可能性があります
* これが行われている間に、アプリケーションによって不整合な状態へのアクセスが試みられる可能性があります
* 通常、スキーマを変更するためのデータベース アクセス許可を、アプリケーションの実行に付与してはなりません
* 何か問題が発生した場合に、クリーンな状態に戻すことが困難です

展開時にデータベースを簡単に移行できる、より良いエクスペリエンスを提供したいと考えています。 これは次のようなものになるはずです。

* Linux、Mac、Windows で動作します
* コマンド ラインでの適切なエクスペリエンスです
* コンテナーでのシナリオをサポートします
* よく使用される実際の展開ツールとフローを使用します
* 少なくとも Visual Studio に統合されます

結果として、EF Core での多くの小規模な機能向上 (たとえば、SQLite での移行の向上など) と、EF だけではないエンドツーエンドのエクスペリエンスを向上させるための、他のチームによるガイダンスと長期的な共同作業になる可能性があります。

## <a name="ef-core-platforms-experience"></a>EF Core プラットフォームのエクスペリエンス

開発リーダー: @roji、@bricelam

追跡: [#19588](https://github.com/dotnet/efcore/issues/19588)

規模: L

状態: スコープ設定/完了

スコーピング:プラットフォームのガイダンスとサンプルは、Blazor、Xamarin、WinForms、および WPF 向けに公開されています。 Xamarin とその他の AOT またはリンカーの作業は、現在 EF Core 6.0 で予定されています。

従来の MVC に似た Web アプリケーションでの EF Core の使用に関しては適切なガイダンスがあります。 他のプラットフォームとアプリケーション モデルについてのガイダンスはないか、または古くなっています。 EF Core 5.0 では、以下のもので EF Core を使用するエクスペリエンスの調査、改善、文書化が計画されています。

* Blazor
* Xamarin (AOT とリンカーのストーリーの使用を含む)
* WinForms、WPF、WinUI、場合によっては他の UI frameworks

これは、EF Core での多くの小規模な機能向上と、EF だけではないエンドツーエンドのエクスペリエンスを向上させるための、他のチームによるガイダンスと長期的な共同作業になる可能性があります。

ここで検討されている具体的な領域は次のとおりです。

* 展開 (移行などの EF ツールの使用に関するエクスペリエンスを含む)
* アプリケーション モデル、(Xamarin と Blazor、場合によってはその他を含む)
* SQLite のエクスペリエンス (空間エクスペリエンスとテーブルの再構築を含む)
* AOT とリンクのエクスペリエンス
* 診断の統合 (パフォーマンス カウンターを含む)

## <a name="performance"></a>パフォーマンス

開発リーダー: @roji

追跡: [5.0 のマイルストーンで `area-perf` というラベルが付けられている問題](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+label%3Aarea-perf+milestone%3A5.0.0+)

規模: L

状態: スコープ設定済み/完了

スコーピング:Npgsql プロバイダーでのパフォーマンスの大幅な強化が完了しました。 現在、EF Core 6.0 でパフォーマンスに関するその他の作業が予定されています。

EF Core では、パフォーマンス ベンチマークのスイートの向上と、ランタイムに対する特定のパフォーマンスの向上が計画されています。 さらに、3.0 リリース サイクル中にプロトタイプ化された新しい ADO.NET バッチ API を完了する予定です。 また、ADO.NET レイヤーでは、Npgsql プロバイダーのさらなるパフォーマンス向上が計画されています。

この作業の一環として、ADO.NET および EF Core パフォーマンス カウンターや他の診断を必要に応じて追加することも計画されています。

## <a name="architecturalcontributor-documentation"></a>アーキテクチャおよび共同作成者向けドキュメント

ドキュメント作成リーダー: @ajcvickers

追跡: [#1920](https://github.com/dotnet/EntityFramework.Docs/issues/1920)

規模: L

状態: 切り取り

ここでのアイデアは、EF Core の内部で何が起こっているかを理解しやすくすることです。 これは、EF Core を使用するすべてのユーザーにとって便利なことですが、主な目的は、外部ユーザーが次のことを容易にできるようにすることです。

* EF Core コードに投稿する
* データベース プロバイダーを作成する
* 他の拡張機能を作成する

更新:残念ながら、この計画は壮大すぎました。
これが重要なことは変わりませんが、残念ながら EF Core 5.0 では実現できません。

## <a name="microsoftdatasqlite-documentation"></a>Microsoft.Data.Sqlite のドキュメント

ドキュメント作成リーダー: @bricelam

追跡: [#1675](https://github.com/dotnet/EntityFramework.Docs/issues/1675)

規模: M

状態: 完了しました。 新しいドキュメントは、[Microsoft docs サイトで公開](/dotnet/standard/data/sqlite/?tabs=netcore-cli)されています。

EF チームも、Microsoft.Data.Sqlite ADO.NET プロバイダーを所有しています。 5\.0 リリースの一環として、このプロバイダーの完全な文書化が予定されています。

## <a name="general-documentation"></a>一般的なドキュメント

ドキュメント作成リーダー: @ajcvickers

追跡: [5.0 マイルストーンでのドキュメント リポジトリの問題](https://github.com/dotnet/EntityFramework.Docs/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+)

規模: L

状態: 進行中

既に、3.0 および 3.1 リリースのドキュメントの更新が行われています。 また、以下の作業も行われています。

* 概要ドキュメントをいっそう理解しやすく実行しやすいものにするための見直し
* 検索を容易にして相互参照を追加するためのドキュメントの再編成
* 既存ドキュメントの詳細さと明快さの向上
* サンプルの更新とその他の例の追加

## <a name="fixing-bugs"></a>バグの修正

追跡: [5.0 のマイルストーンで `type-bug` というラベルが付けられている問題](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-bug+)

開発者: @roji、@maumar、@bricelam、@smitpatel、@AndriySvyryd、@ajcvickers

規模: L

状態: 進行中

この執筆時点では、5.0 リリースでの修正対象として 135 件のバグがトリアージされていますが (62 件は既に修正済み)、上記の「_一般的なクエリの機能強化_」セクションとかなり重複しています。

発生率 (マイルストーンで作業するようになった問題) は、3.0 リリースの全体を通して、1 か月あたり約 23 件の問題でした。 これらのすべてを 5.0 で修正する必要はありません。 大まかな見積もりとして、5.0 の時間枠では 150 件の問題をさらに修正することが計画されています。

## <a name="small-enhancements"></a>小規模な機能強化

追跡: [5.0 のマイルストーンで `type-enhancement` というラベルが付けられている問題](https://github.com/dotnet/efcore/issues?utf8=%E2%9C%93&q=is%3Aissue+milestone%3A5.0.0+label%3Atype-enhancement+)

開発者: @roji、@maumar、@bricelam、@smitpatel、@AndriySvyryd、@ajcvickers

規模: L

状態: 完了

これまでに説明した比較的大きな機能に加えて、5.0 では、"小さな傷" を修正するために多くの小規模な改善が予定されています。 これらの機能強化の多くは、上で説明した一般的なテーマにも含まれていることに注意してください。

## <a name="below-the-line"></a>対象外

追跡: [`consider-for-next-release` というラベルが付けられている問題](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+label%3Aconsider-for-next-release)

これらは、現時点では 5.0 リリースで予定されて **いない** バグ修正および機能強化ですが、上記の作業の進行状況によっては、努力目標として見られています。

また、計画を立てるときは常に、[最も投票が多かった問題](https://github.com/dotnet/efcore/issues?q=is%3Aissue+is%3Aopen+sort%3Areactions-%2B1-desc)が考慮されています。 これらの問題をリリースから切り捨てることは常に苦痛ですが、保有するリソースにとって現実的な計画を立てる必要があります。

## <a name="suggestions"></a>推奨事項

計画に関するフィードバックは重要です。 イシューの重要度を示す最善の方法は、GitHub でそのイシューに投票 (上向きの親指) することです。 このデータが、次のリリースの[計画プロセス](xref:core/what-is-new/release-planning)に取り込まれます。
