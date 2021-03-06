---
title: サポートされている .NET 実装 - EF Core
description: Entity Framework Core の各バージョンでサポートされているプラットフォームに関する情報
author: bricelam
ms.date: 06/26/2020
uid: core/miscellaneous/platforms
ms.openlocfilehash: 6b888843d491805e735fd903253d7f10c028dd9f
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431246"
---
# <a name="net-implementations-supported-by-ef-core"></a>EF Core でサポートされている .NET 実装

私達は、最新の .NET 実装を使用するすべての開発者が EF Core を使用できることを希望しており、その目標に向けて取り組んでいます。 EF Core の .NET Core でのサポートは、自動テストされており、多くのアプリケーションで正常に使用できることがわかっていますが、Mono、Xamarin、UWP ではいくつか問題があります。

## <a name="overview"></a>概要

.NET 実装のガイダンスを下の表にまとめました。

| EF Core                       | 2.1 および 3.1 | 5.0             |
|:------------------------------|:------------|:----------------|
| .NET Standard                 | 2.0         | 2.1             |
| .NET Core                     | 2.0         | 3.0             |
| .NET Framework<sup>(1)</sup>  | 4.7.2       | (サポートされていません) |
| Mono                          | 5.4         | 6.4             |
| Xamarin.iOS<sup>(2)</sup>     | 10.14       | 12.16           |
| Xamarin.Mac<sup>(2)</sup>     | 3.8         | 5.16            |
| Xamarin.Android<sup>(2)</sup> | 8.0         | 10.0            |
| UWP<sup>(3)</sup>             | 10.0.16299  | TBD             |
| Unity<sup>(4)</sup>           | 2018.1      | TBD             |

<sup>(1)</sup> 下記の「[.NET Framework](#net-framework)」のセクションを参照してください。

<sup>(2)</sup> Xamarin には、EF Core 2 で開発された一部のアプリケーションが正しく動作しない問題と既知の制限があります。 回避策については、[アクティブな懸案事項](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+label%3Aarea-xamarin)の一覧を確認してください。

<sup>(3)</sup> EF Core 2.0.1 以降をお勧めします。 [.NET Core UWP 6.x パッケージ](https://www.nuget.org/packages/Microsoft.NETCore.UniversalWindowsPlatform/)をインストールします。 この記事の「[ユニバーサル Windows プラットフォーム](#universal-windows-platform)」のセクションを参照してください。

<sup>(4)</sup> Unity には、問題と既知の制限があります。 [アクティブな懸案事項](https://github.com/dotnet/efcore/issues?q=is%3Aopen+is%3Aissue+label%3Aarea-unity)の一覧を確認してください。

## <a name="net-framework"></a>.NET Framework

.NET Framework をターゲットとするアプリケーションを .NET Standard のライブラリと連携させるには、次のような変更が必要になる可能性があります。

プロジェクト ファイルを編集し、初期プロパティ グループに次のエントリが表示されるようにします。

```xml
<AutoGenerateBindingRedirects>true</AutoGenerateBindingRedirects>
```

テスト プロジェクトの場合、次のエントリも必要です。

```xml
<GenerateBindingRedirectsOutputType>true</GenerateBindingRedirectsOutputType>
```

それよりも古いバージョンの Visual Studio を使用する場合は、必ず [NuGet クライアントをバージョン 3.6.0 にアップグレードしてください](https://www.nuget.org/downloads)。これで .NET Standard 2.0 のライブラリと連携できるようになります。

また、可能であれば、NuGet の packages.config を PackageReference に移行することをお勧めします。 次のプロパティをプロジェクト ファイルに追加します。

```xml
<RestoreProjectStyle>PackageReference</RestoreProjectStyle>
```

## <a name="universal-windows-platform"></a>ユニバーサル Windows プラットフォーム

EF Core と .NET UWP の以前のバージョンには、多数の互換性の問題があり、特に .NET ネイティブ ツールチェーンでコンパイルされたアプリケーションの場合に顕著でした。 新しい .NET UWP バージョンでは、.NET Standard 2.0 のサポートが追加され、.NET Native 2.0 が含まれています。これにより、以前に報告されているほとんどの互換性の問題が解決されています。 EF Core 2.0.1 は、UWP でより完全にテストされていますが、テストは自動化されていません。

UWP で EF Core を使用する場合:

* クエリのパフォーマンスを最適化するには、LINQ クエリで匿名型を回避します。 UWP アプリケーションをアプリ ストアに配置するには、アプリケーションを .NET ネイティブでコンパイルする必要があります。 匿名型のクエリは、.NET ネイティブではパフォーマンスが低下します。

* `SaveChanges()` のパフォーマンスを最適化するには、[ChangeTrackingStrategy.ChangingAndChangedNotifications](/dotnet/api/microsoft.entityframeworkcore.changetrackingstrategy) を使用し、エンティティ型で [INotifyPropertyChanged](https://msdn.microsoft.com/library/system.componentmodel.inotifypropertychanged.aspx)、[INotifyPropertyChanging](https://msdn.microsoft.com/library/system.componentmodel.inotifypropertychanging.aspx)、[INotifyCollectionChanged](https://msdn.microsoft.com/library/system.collections.specialized.inotifycollectionchanged.aspx) を実装します。

## <a name="report-issues"></a>問題のレポート

予想どおりに機能しない組み合わせについては、[EF Core 問題追跡ツール](https://github.com/dotnet/efcore/issues/new)で新しい問題を登録することが推奨されています。 Xamarin 関連の問題については、 [Xamarin.Android](https://github.com/xamarin/xamarin-android/issues/new) または [Xamarin.iOS](https://github.com/xamarin/xamarin-macios/issues/new) の問題追跡ツールをご利用ください。
