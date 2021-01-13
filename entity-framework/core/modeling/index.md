---
title: モデルの作成と構成 - EF Core
description: Entity Framework Core を使用したモデルの作成と構成の概要
author: AndriySvyryd
ms.date: 10/13/2020
uid: core/modeling/index
ms.openlocfilehash: 709e2bde60c8e2c31f0a39390624c5d31a9cfa08
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129201"
---
# <a name="creating-and-configuring-a-model"></a>モデルの作成と構成

Entity Framework では、一連の規則を利用し、エンティティ クラスの形状に基づいてモデルがビルドされます。 規則にあるものを捕捉する/オーバーライドする目的で、追加構成を指定できます。

この記事では、あらゆるデータ ストアをターゲットにするモデルに適用できる構成、あらゆるリレーショナル データベースをターゲットにするときに適用できる構成について取り上げます。 プロバイダーも、特定のデータ ストアに固有の構成を有効にできます。 プロバイダー固有の構成については、「[データベース プロバイダー](xref:core/providers/index)」セクションを参照してください。

> [!TIP]
> この記事の[サンプル](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples)は GitHub で確認できます。

## <a name="use-fluent-api-to-configure-a-model"></a>fluent API を使用してモデルを構成する

派生コンテキストで `OnModelCreating` メソッドをオーバーライドし、`ModelBuilder API` を使用してモデルを構成できます。 これは最も強力な構成方法であり、エンティティ クラスを変更しなくても構成を指定できます。 Fluent API 構成には一番上の優先度が与えられ、規則やデータ注釈をオーバーライドします。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Required.cs?highlight=12-14)]

### <a name="grouping-configuration"></a>構成のグループ化

<xref:System.Data.Entity.DbContext.OnModelCreating%2A> メソッドのサイズを小さくするため、エンティティ型のすべての構成を、<xref:Microsoft.EntityFrameworkCore.IEntityTypeConfiguration%601> を実装する個別のクラスに抽出できます。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/EntityTypeConfiguration.cs?Name=IEntityTypeConfiguration)]

その後、`OnModelCreating` から `Configure` メソッドを呼び出すだけです。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/EntityTypeConfiguration.cs?Name=ApplyIEntityTypeConfiguration)]

特定のアセンブリ内の `IEntityTypeConfiguration` を実装する型で指定されたすべての構成を適用できます。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/EntityTypeConfiguration.cs?Name=ApplyConfigurationsFromAssembly)]

> [!NOTE]
> 構成が適用される順序は定義されていないため、このメソッドは順序が重要でない場合にのみ使用する必要があります。

## <a name="use-data-annotations-to-configure-a-model"></a>データ注釈を使用してモデルを構成する

クラスやプロパティに属性 (データ注釈と呼ばれています) を適用することもできます。 データ注釈は規則をオーバーライドしますが、Fluent API 構成で上書きされます。

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Required.cs?highlight=15)]
