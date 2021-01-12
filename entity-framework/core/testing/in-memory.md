---
title: EF In-Memory データベース EF Core を使用したテスト
description: EF のメモリ内データベースを使用して Entity Framework Core アプリケーションをテストする
author: ajcvickers
ms.date: 10/27/2016
uid: core/testing/in-memory
ms.openlocfilehash: 78dcac3d0fd69110986c99a097a864104caa1951
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2021
ms.locfileid: "98128811"
---
# <a name="testing-with-the-ef-in-memory-database"></a>EF In-Memory データベースを使用したテスト

> [!WARNING]
> EF のメモリ内データベースは、多くの場合、リレーショナルデータベースとは動作が異なります。
> 「 [EF Core を使用するコードのテスト](xref:core/testing/index)」で説明されているように、関連する問題とトレードオフについて十分に理解した後は、EF のメモリ内データベースのみを使用してください。

> [!TIP]
> SQLite はリレーショナルプロバイダーであり、メモリ内のデータベースを使用することもできます。
> 一般的なリレーショナルデータベースの動作をより厳密に一致させるためにテストに使用することを検討してください。
> これについては、 [「SQLite を使用した EF Core アプリケーションのテスト](xref:core/testing/sqlite)」で説明されています。

このページの情報は、他の場所にあります。

* EF インメモリデータベースでのテストに関する一般的な情報については、「 [EF Core を使用したコードのテスト](xref:core/testing/index) 」を参照してください。
* EF のメモリ内データベースを使用するサンプルについては、 [EF Core を使用するアプリケーションをテストする方法を示すサンプル](xref:core/testing/testing-sample) を参照してください。
* EF インメモリデータベースに関する一般的な情報について [は、「ef インメモリデータベースプロバイダー](xref:core/providers/in-memory/index) 」を参照してください。
