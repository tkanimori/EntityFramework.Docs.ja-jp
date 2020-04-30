---
title: EF インメモリデータベースでのテスト-EF Core
author: ajcvickers
description: EF のメモリ内データベースを使用して EF Core アプリケーションをテストする
ms.date: 10/27/2016
uid: core/miscellaneous/testing/in-memory
ms.openlocfilehash: f31b3bdedb8c339dbb6baa9d7f2031d023d5757f
ms.sourcegitcommit: 79e460f76b6664e1da5886d102bd97f651d2ffff
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/29/2020
ms.locfileid: "82538350"
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
* EF インメモリデータベースでのテストに関する一般的な情報については、「 [EF Core を使用したコードのテスト](xref:core/miscellaneous/testing/index)」を参照してください。
* EF のメモリ内データベースを使用するサンプルについては、 [EF Core を使用するアプリケーションをテストする方法を示すサンプル](xref:core/miscellaneous/testing/testing-sample)を参照してください。
* EF インメモリデータベースに関する一般的な情報について[は、「ef インメモリデータベースプロバイダー](xref:core/providers/in-memory/index) 」を参照してください。
