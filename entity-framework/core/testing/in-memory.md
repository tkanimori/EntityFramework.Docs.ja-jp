---
title: EF In-Memory データベース EF Core を使用したテスト
description: EF のメモリ内データベースを使用して Entity Framework Core アプリケーションをテストする
author: ajcvickers
ms.date: 10/27/2016
uid: core/testing/in-memory
ms.openlocfilehash: db91570dc9d5a4b95d513df509867e9bca406356
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2020
ms.locfileid: "94431241"
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
