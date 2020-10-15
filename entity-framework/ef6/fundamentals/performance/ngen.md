---
title: NGen を使用した起動時のパフォーマンスの向上-EF6
description: Entity Framework 6 での NGen による起動時のパフォーマンスの向上
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/fundamentals/performance/ngen
ms.openlocfilehash: a58ffc1a4da1cdb0ae12366ab1ec0139141b3a7f
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065421"
---
# <a name="improving-startup-performance-with-ngen"></a>NGen を使用した起動時のパフォーマンスの向上
> [!NOTE]
> **EF6 以降のみ** - このページで説明する機能、API などは、Entity Framework 6 で導入されました。 以前のバージョンを使用している場合、一部またはすべての情報は適用されません。  

.NET Framework は、アプリケーションの起動を高速化する手段として、マネージアプリケーションとライブラリのネイティブイメージの生成をサポートしています。また、場合によっては、使用するメモリが少なくなります。 ネイティブイメージは、マネージコードアセンブリをアプリケーションの実行前にネイティブコンピューター命令を含むファイルに変換することによって作成されます。これにより、.NET JIT (Just-in-time) コンパイラは、アプリケーションランタイムでネイティブ命令を生成する必要がなくなります。  

Version 6 より前のバージョンでは、EF ランタイムのコアライブラリは .NET Framework に含まれており、ネイティブイメージは自動的に生成されました。 バージョン6以降では、EF ランタイム全体が EntityFramework NuGet パッケージに結合されています。 同様の結果を得るには、NGen.exe コマンドラインツールを使用してネイティブイメージを生成する必要があります。  

経験上の観察は、EF ランタイムアセンブリのネイティブイメージが、アプリケーションの起動時間の 1 ~ 3 秒間を切ることができることを示しています。  

## <a name="how-to-use-ngenexe"></a>NGen.exe の使用方法  

NGen.exe ツールの最も基本的な機能は、アセンブリとそのすべての直接の依存関係のネイティブイメージを "インストール" (つまり、ディスクに保存) することです。 これを実現する方法を次に示します。  

1. 管理者としてコマンド プロンプト ウィンドウを開きます。
2. 現在の作業ディレクトリを、ネイティブイメージを生成するアセンブリの場所に変更します。

   ``` console
   cd <*Assemblies location*>  
   ```

3. オペレーティングシステムとアプリケーションの構成によっては、32ビットアーキテクチャ、64ビットアーキテクチャ、またはその両方のネイティブイメージを生成することが必要になる場合があります。

   32ビットの場合は、次のように実行します。

   ``` console
   %WINDIR%\Microsoft.NET\Framework\v4.0.30319\ngen install <Assembly name>  
   ```

   64ビットの場合は、次のように実行します。
  
   ``` console
   %WINDIR%\Microsoft.NET\Framework64\v4.0.30319\ngen install <Assembly name>  
   ```

> [!TIP]
> 間違ったアーキテクチャのネイティブイメージを生成するのは非常に一般的な間違いです。 確信が持てない場合は、コンピューターにインストールされているオペレーティングシステムに適用されるすべてのアーキテクチャのネイティブイメージを簡単に生成できます。  

また NGen.exe は、インストールされているネイティブイメージのアンインストールと表示、複数のイメージの生成のキューなどの他の機能もサポートします。使用法の詳細については、 [NGen.exe のドキュメント](https://msdn.microsoft.com/library/6t9t5wcf.aspx)を参照してください。  

## <a name="when-to-use-ngenexe"></a>NGen.exe を使用する場合  

EF バージョン6以降に基づくアプリケーションでネイティブイメージを生成するアセンブリを決定する際には、次のオプションを考慮する必要があります。  

- **主 ef ランタイムアセンブリ EntityFramework.dll**: 一般的な ef ベースのアプリケーションは、起動時またはデータベースへの最初のアクセス時にこのアセンブリから大量のコードを実行します。 そのため、このアセンブリのネイティブイメージを作成すると、起動時のパフォーマンスが最大になります。  
- **アプリケーションによって使用される EF プロバイダーアセンブリ**: 起動時間は、これらのネイティブイメージを生成する場合と若干同じ効果があります。 たとえば、アプリケーションで SQL Server に EF プロバイダーを使用する場合は、EntityFramework.SqlServer.dll のネイティブイメージを生成する必要があります。  
- **アプリケーションのアセンブリとその他の依存関係**: [NGen.exe のドキュメント](https://msdn.microsoft.com/library/6t9t5wcf.aspx) では、ネイティブイメージを生成するアセンブリを選択するための一般的な条件と、セキュリティ上のネイティブイメージの影響、"ハードバインディング" などの高度なオプション、デバッグとプロファイルのシナリオでのネイティブイメージの使用などのシナリオなどについて説明します。  

> [!TIP]
> 起動時のパフォーマンスとアプリケーションの全体的なパフォーマンスの両方でネイティブイメージを使用した場合の影響を慎重に測定し、実際の要件と比較してください。 一般にネイティブイメージは起動時のパフォーマンスの向上に役立ちますが、場合によってはメモリ使用量が少なくなることがありますが、一部のシナリオでは同等のメリットが得られません。 たとえば、安定した状態で実行した場合 (つまり、アプリケーションによって使用されているすべてのメソッドが少なくとも1回呼び出された場合)、JIT コンパイラによって生成されるコードはネイティブイメージよりも若干優れたパフォーマンスをもたらします。  

## <a name="using-ngenexe-in-a-development-machine"></a>開発用コンピューターでの NGen.exe の使用  

開発中は、.NET JIT コンパイラによって、頻繁に変更されるコードに対する全体的なトレードオフが最適になります。 EF runtime アセンブリなど、コンパイルされた依存関係のネイティブイメージを生成すると、各実行の開始時に数秒を減らすことで、開発とテストの時間を短縮できます。  

EF ランタイムアセンブリを見つけるための最適な場所は、ソリューションの NuGet パッケージの場所です。 たとえば、SQL Server で EF 6.0.2 を使用し、.NET 4.5 以降を対象とするアプリケーションの場合は、コマンドプロンプトウィンドウで次のように入力できます (管理者として開くことを忘れないでください)。  

```console
cd <Solution directory>\packages\EntityFramework.6.0.2\lib\net45
%WINDIR%\Microsoft.NET\Framework\v4.0.30319\ngen install EntityFramework.SqlServer.dll
%WINDIR%\Microsoft.NET\Framework64\v4.0.30319\ngen install EntityFramework.SqlServer.dll
```  

> [!NOTE]
> これにより、SQL Server 用の EF プロバイダーのネイティブイメージをインストールするときに、メインの EF runtime アセンブリのネイティブイメージも既定でインストールされるという事実を活用できます。 これは、EntityFramework.dll が同じディレクトリにある EntityFramework.SqlServer.dll アセンブリの直接の依存関係であることを NGen.exe が検出できるために機能します。  

## <a name="creating-native-images-during-setup"></a>セットアップ時のネイティブイメージの作成  

WiX Toolkit は、この [ハウツーガイド](https://wixtoolset.org/documentation/manual/v3/howtos/files_and_registry/ngen_managed_assemblies.html)で説明されているように、セットアップ時にマネージアセンブリのネイティブイメージの生成をキューによってサポートします。 別の方法として、NGen.exe コマンドを実行するカスタムセットアップタスクを作成することもできます。  

## <a name="verifying-that-native-images-are-being-used-for-ef"></a>EF でネイティブイメージが使用されていることを確認しています  

特定のアプリケーションがネイティブアセンブリを使用していることを確認するには、拡張子が ".ni.dll" または ".ni.exe" の読み込み済みアセンブリを探します。 たとえば、EF のメインランタイムアセンブリのネイティブイメージは EntityFramework.ni.dll として呼び出されます。 プロセスの .NET アセンブリを確認する簡単な方法は、 [プロセスエクスプローラー](https://technet.microsoft.com/sysinternals/bb896653)を使用することです。  

## <a name="other-things-to-be-aware-of"></a>その他の注意点  

**アセンブリのネイティブイメージの作成は、アセンブリを[GAC (グローバルアセンブリキャッシュ)](https://msdn.microsoft.com/library/yf1d93sz.aspx)に登録する場合と混同しないようにしてください**。 NGen.exe では、GAC にないアセンブリのイメージを作成できます。実際には、特定のバージョンの EF を使用する複数のアプリケーションが同じネイティブイメージを共有できます。 Windows 8 では、GAC に配置されたアセンブリのネイティブイメージを自動的に作成できますが、EF ランタイムはアプリケーションと共に配置するように最適化されています。また、アセンブリの解決に悪影響を及ぼし、アプリケーションを他の側面でサービスするため、GAC に登録することはお勧めしません。  
