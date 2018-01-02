---
title: "サポートされている .NET 実装 - EF Core"
author: rowanmiller
ms.author: divega
ms.date: 08/30/2017
ms.technology: entity-framework-core
uid: core/platforms/index
ms.openlocfilehash: 6b6ea9ca833810169d0d850f3bea8776b761c007
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2017
---
# <a name="net-implementations-supported-by-ef-core"></a>EF Core でサポートされている .NET 実装

Microsoft は .NET コードを記述できるあらゆる場所で EF Core を使えるようにすることを目指しており、その目標に向かって現在も取り組んでいます。 EF Core の導入を目指している各 .NET 実装のガイダンスを下の表にまとめました。

EF Core 2.0 の対象は [.NET Standard 2.0](https://docs.microsoft.com/dotnet/standard/net-standard) です。そのため、それをサポートする .NET 実装が必要です。

| .NET 実装 | Status | 1.x で必須 | 2.x で必須
|-|-|-|-
| **.NET Core** ([ASP.NET Core](../get-started/aspnetcore/index.md)、[コンソール](../get-started/netcore/index.md)など) | **完全サポート/推奨:** 自動化されたテストを適用済み。多くのアプリケーションで問題なく動作することが確認済み。 | [.NET Core SDK 1.x](https://www.microsoft.com/net/core/) | [.NET Core SDK 2.x](https://www.microsoft.com/net/core/)
| **.NET Framework** (WinForms、WPF、ASP.NET、[コンソール](../get-started/full-dotnet/index.md)など) | **完全サポート/推奨:** 自動化されたテストを適用済み。多くのアプリケーションで問題なく動作することが確認済み。 EF 6 はこのプラットフォームでも利用可能 (「[Compare EF Core & EF6](../../efcore-and-ef6/index.md)」 (EF Core と EF6 を比較する) を参照し、適切なテクノロジを選択してください)。 | .NET Framework 4.5.1 | .NET Framework 4.6.1
| **Mono & Xamarin** | **進行中 – 問題に遭遇する可能性あり:** EF Core チームと顧客が一部のテストを実施済み。 早い段階で導入したユーザーからは成功例も報告されていますが、[問題も見つかっており](https://github.com/aspnet/entityframework/issues?q=is%3Aopen+is%3Aissue+label%3Aarea-xamarin)、テストが続けられればそれ以外の問題も見つかる可能性があります。 具体的には、Xamarin.iOS に制限があります。その制限により、EF Core 2.0 で開発された一部のアプリケーションが正しく動作しない可能性があります。 | Mono 4.6 <br/> Xamarin.iOS 10 <br/> Xamarin.Mac 3 <br/> Xamarin.Android 7 | Mono 5.4 <br/> Xamarin.iOS 10.14 <br/> Xamarin.Mac 3.8 <br/> Xamarin.Android 7.5
| [**ユニバーサル Windows プラットフォーム**](../get-started/uwp/index.md) | **進行中 – 問題に遭遇する可能性あり:** EF Core チームと顧客が一部のテストを実施済み。 .NET Native ツールチェーンでコンパイルしたときの[問題](https://github.com/aspnet/entityframework/issues?utf8=%E2%9C%93&q=is%3Aopen%20is%3Aissue%20label%3Aarea-uwp%20)が報告されています。このツールチェーンはリリース ビルド中に一般的に使用され、Windows Store に展開するときの必須です (.NET Native を使用していない場合や単に実験を行う場合は、問題の多くは影響を与えません)。 | [最新 .NET UWP 5 パッケージ](https://www.nuget.org/packages/Microsoft.NETCore.UniversalWindowsPlatform/5.4.1) | [最新 .NET UWP 6 パッケージ](https://www.nuget.org/packages/Microsoft.NETCore.UniversalWindowsPlatform/) <sup>(1)</sup>

<sup>(1)</sup> このバージョンの .NET UWP は .NET Standard 2.0 のサポートを追加し、.NET Native 2.0 を含んでいます。.NET Native 2.0 は以前に報告された互換性問題の多くを解消しますが、テストの結果、EF Core 2.0 に[いくつかの問題が残っている](https://github.com/aspnet/EntityFrameworkCore/issues?q=is%3Aopen+is%3Aissue+milestone%3A2.0.1+label%3Aarea-uwp)ことが判明しました。それについては、今後のパッチ リリースで対処する予定です。

予想どおりに機能しない組み合わせについては、[EF Core 問題追跡ツール](https://github.com/aspnet/entityframeworkcore/issues/new)で新しい問題を登録することが推奨されています。Xamarin 関連の問題については、[Xamarin 問題追跡ツール](https://bugzilla.xamarin.com/newbug)をご利用ください。
