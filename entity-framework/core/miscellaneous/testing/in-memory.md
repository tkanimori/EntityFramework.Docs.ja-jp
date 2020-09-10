---
title: EF インメモリデータベースでのテスト-EF Core
description: EF のメモリ内データベースを使用して Entity Framework Core アプリケーションをテストする
author: ajcvickers
ms.date: 10/27/2016
uid: core/miscellaneous/testing/in-memory
ms.openlocfilehash: 353f67fb0e78fefa74fc77d302e505bacb692ed4
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2020
ms.locfileid: "89619395"
---
# <a name="testing-with-the-ef-in-memory-database"></a>EF インメモリデータベースを使用したテスト

> [!WARNING]
> EF のメモリ内データベースは、多くの場合、リレーショナルデータベースとは動作が異なります。
> 「 [EF Core を使用するコードのテスト](xref:core/miscellaneous/testing/index)」で説明されているように、関連する問題とトレードオフについて十分に理解した後は、EF のメモリ内データベースのみを使用してください。  

> [!TIP]
> SQLite はリレーショナルプロバイダーであり、メモリ内のデータベースを使用することもできます。
> 一般的なリレーショナルデータベースの動作をより厳密に一致させるためにテストに使用することを検討してください。
> これについては、 [「SQLite を使用した EF Core アプリケーションのテスト](xref:core/miscellaneous/testing/sqlite)」で説明されています。   

このページの情報は、他の場所にあります。
* EF インメモリデータベースでのテストに関する一般的な情報については、「 [EF Core を使用したコードのテスト](xref:core/miscellaneous/testing/index) 」を参照してください。
* EF のメモリ内データベースを使用するサンプルについては、 [EF Core を使用するアプリケーションをテストする方法を示すサンプル](xref:core/miscellaneous/testing/testing-sample) を参照してください。
* EF インメモリデータベースに関する一般的な情報について [は、「ef インメモリデータベースプロバイダー](xref:core/providers/in-memory/index) 」を参照してください。
