---
title: アセンブリを NGen - EF6 で起動時のパフォーマンスを向上させる
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: dc6110a0-80a0-4370-8190-cea942841cee
caps.latest.revision: 4
ms.openlocfilehash: cffd2deea3148a16ed704d1e5e7b365eda06f72b
ms.sourcegitcommit: bdd06c9a591ba5e6d6a3ec046c80de98f598f3f3
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2018
ms.locfileid: "39122739"
---
# <a name="improving-startup-performance-with-ngen"></a>アセンブリを NGen で起動時のパフォーマンスを向上させる
> [!NOTE]
> **EF6 以降のみ** - このページで説明する機能、API などは、Entity Framework 6 で導入されました。 以前のバージョンを使用している場合、一部またはすべての情報は適用されません。  

アプリケーションを迅速に起動する方法として、または場合によってはでライブラリを使用して、メモリが少ないを .NET Framework は、マネージ アプリケーションのネイティブ イメージの生成をサポートします。 ネイティブ イメージが軽減は、.NET JIT (ジャスト イン タイム) コンパイラでネイティブ命令を生成することをマネージ コード アセンブリをアプリケーションが実行される前に、ネイティブ機械語命令を含むファイルに変換することによって作成されます。アプリケーション ランタイム。  

前のバージョン 6、EF のランタイムの core ライブラリ、.NET Framework の一部であったし、それらのネイティブ イメージが自動的に生成されました。 バージョン 6 以降で、全体の EF ランタイムは、EntityFramework NuGet パッケージに統合されています。 ネイティブ イメージが今すぐに生成、NGen.exe コマンド ライン ツールを使用して同様の結果を取得します。  

経験の観測値では、EF のランタイム アセンブリのネイティブ イメージは、アプリケーションの起動時間の 1 ~ 3 秒の間で減らすことができますを表示します。  

## <a name="how-to-use-ngenexe"></a>NGen.exe を使用する方法  

NGen.exe ツールの最も基本的な機能は、「インストール」する (つまりを作成し、ディスクに永続化) アセンブリとその直接の依存関係のネイティブ イメージ。 次を実現方法を示します。  

1. 管理者としてコマンド プロンプト ウィンドウを開きます  
2. ネイティブ イメージを生成するアセンブリの場所を現在の作業ディレクトリを変更します。  

  ``` console
    cd <*Assemblies location*>  
  ```
3. オペレーティング システムとアプリケーションの構成によっては、32 ビット アーキテクチャでは、64 ビット アーキテクチャ用、または両方のネイティブ イメージを生成する必要があります。  

    32 ビットの実行します。  
  ``` console
    %WINDIR%\Microsoft.NET\Framework\v4.0.30319\ngen install <Assembly name>  
  ```
    64 ビットの実行します。
  ``` console
    %WINDIR%\Microsoft.NET\Framework64\v4.0.30319\ngen install <Assembly name>  
  ```

> [!TIP]
> 非常によくある間違いは、正しくないアーキテクチャのネイティブ イメージを生成します。 ときに不明な単に、マシンにインストールされているオペレーティング システムに適用されるすべてのアーキテクチャ用のネイティブ イメージを生成することができます。  

NGen.exe をアンインストールして、複数のイメージ生成のキュー、インストールされたネイティブ イメージを表示するなどの他の関数もサポートします。使用状況の詳細については、読み取り、 [NGen.exe ドキュメント](https://msdn.microsoft.com/library/6t9t5wcf.aspx)します。  

## <a name="when-to-use-ngenexe"></a>NGen.exe を使用する場合  

EF 6 以降のバージョンに基づくアプリケーションでのネイティブ イメージを生成するアセンブリを決定する際に、次のオプションを検討してください。  

- **メインの EF ランタイム アセンブリとなる EntityFramework.dll**: 一般的な EF ベースのアプリケーションをデータベースに起動時に、またはその最初のアクセスは、このアセンブリから大量のコードを実行します。 そのため、このアセンブリのネイティブ イメージを作成すると、起動時のパフォーマンスの向上が生成されます。  
- **アプリケーションで使用される任意の EF プロバイダー アセンブリ**: 起動時メリットも若干これらのネイティブ イメージを生成します。 たとえば、アプリケーション EF provider for SQL Server を使用するは場合は、EntityFramework.SqlServer.dll のネイティブ イメージを生成します。  
- **アプリケーションのアセンブリとその他の依存関係**: [NGen.exe ドキュメント](https://msdn.microsoft.com/library/6t9t5wcf.aspx)のネイティブ イメージと、セキュリティ上のネイティブ イメージの影響を生成するアセンブリを選択するための一般的な条件について説明します「ハード バインディング」などのオプションを高度なデバッグとプロファイリング シナリオなどでのネイティブ イメージを使用してなどのシナリオ。  

> [!TIP]
> 慎重に起動時のパフォーマンスと、アプリケーションの全体的なパフォーマンスの両方でネイティブ イメージを使用しての影響を測定して実際の要件と比較して確認します。 ネイティブ イメージはスタートアップ パフォーマンスを向上させ、場合によっては、メモリ使用量を減らす際に役立つ一般的に、すべてのシナリオを均等に利用できます。 たとえば、安定状態での実行時に (つまり、アプリケーションで使用されているすべてのメソッドが少なくとも 1 回呼び出された) と、JIT コンパイラによって生成されたコードこと実際に得ネイティブ イメージよりも若干優れたパフォーマンス。  

## <a name="using-ngenexe-in-a-development-machine"></a>開発用コンピューターで NGen.exe を使用します。  

.NET JIT の開発時にコンパイラが頻繁に変更するコードの最適な全体的なトレードオフが提供されます。 EF のランタイム アセンブリなどのコンパイル済みの依存関係のネイティブ イメージを生成すると、開発とテストを実行するたびの先頭に数秒を切り抜くを高速化できます。  

EF のランタイム アセンブリを検索する点としては、ソリューションの NuGet パッケージの場所です。 たとえば、SQL Server での EF 6.0.2 を使用して、.NET 4.5 以降を対象とするアプリケーションのことができますを入力、次コマンド プロンプト ウィンドウで (管理者として開きます。 これを思い出してください)。  

``` console
cd <Solution directory>\packages\EntityFramework.6.0.2\lib\net45
%WINDIR%\Microsoft.NET\Framework\v4.0.30319\ngen install EntityFramework.SqlServer.dll
%WINDIR%\Microsoft.NET\Framework64\v4.0.30319\ngen install EntityFramework.SqlServer.dll
```  

> [!NOTE]
> これは、EF provider for SQL Server のネイティブ イメージをインストールすることも既定でインストールされますネイティブ イメージ、メインの EF ランタイム アセンブリを利用します。 これは、NGen.exe が EntityFramework.dll が同じディレクトリにある EntityFramework.SqlServer.dll アセンブリの直接的な依存関係を検出できるために機能します。  

## <a name="creating-native-images-during-setup"></a>セットアップ時にネイティブ イメージの作成  

WiX ツールキットがこれで説明するようキューのセットアップ中に、マネージ アセンブリのネイティブ イメージの生成をサポート[ハウツー ガイド](http://wixtoolset.org/documentation/manual/v3/howtos/files_and_registry/ngen_managed_assemblies.html)します。 別の方法では、NGen.exe コマンドを実行するカスタム セットアップ タスクを作成します。  

## <a name="verifying-that-native-images-are-being-used-for-ef"></a>EF のネイティブ イメージが使用されていることを確認します。  

特定のアプリケーションが、拡張子が読み込まれたアセンブリを探すことによって、ネイティブ アセンブリを使用していることを確認できます". ni.dll「または」. ni.exe"。 たとえば、EF のメインのランタイム アセンブリのネイティブ イメージの名前は、EntityFramework.ni.dll には。 プロセスの読み込み済みの .NET アセンブリを検査する簡単な方法は、使用することです。 [Process Explorer](https://technet.microsoft.com/sysinternals/bb896653)します。  

## <a name="other-things-to-be-aware-of"></a>注意すべきこと  

**アセンブリのネイティブ イメージを作成する必要がありますと混同しないでアセンブリの登録、 [GAC (グローバル アセンブリ キャッシュ)](https://msdn.microsoft.com/library/yf1d93sz.aspx)** します。 NGen.exe は、GAC に含まれていないアセンブリのイメージを作成して、実際には、特定のバージョンの EF を使用する複数のアプリケーションは、同じネイティブ イメージを共有できます。 Windows 8 では、GAC に配置するアセンブリのネイティブ イメージを自動的に作成できます、中に、アプリケーションと共に展開する EF ランタイムが最適化され、アセンブリの解決に悪影響を及ぼしますこれとは、GAC に登録しないで、その他の側面の間で、アプリケーションにサービスを提供します。  
