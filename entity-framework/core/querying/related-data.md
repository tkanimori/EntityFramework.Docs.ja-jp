---
title: "読み込みに関連したデータの EF コア"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: f9fb64e2-6699-4d70-a773-592918c04c19
ms.technology: entity-framework-core
uid: core/querying/related-data
ms.openlocfilehash: cd26bd2e6f85083f73d97b1356d0ba38f53e0b8f
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2017
---
# <a name="loading-related-data"></a>関連データの読み込み

Entity Framework Core では、関連エンティティの読み込みをモデルで、ナビゲーション プロパティを使用することができます。 関連するデータの読み込みに使用される 3 つの一般的な O/RM パターンがあります。
* **一括読み込み**最初のクエリの一部として、データベースから、関連するデータが読み込まれたことを意味します。
* **明示的な読み込み**は後で、データベースから、関連するデータを明示的に読み込むことを意味します。
* **遅延読み込み**ナビゲーション プロパティにアクセスする場合、関連するデータが透過的にデータベースから読み込むことを意味します。 遅延読み込みは、まだ EF Core では可能ではありません。

> [!TIP]  
> この記事を表示する[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Querying)GitHub でします。

## <a name="eager-loading"></a>一括読み込み

使用することができます、`Include`クエリの結果に含まれる関連データを指定します。 次の例では、ブログの結果に返される必要があります、`Posts`プロパティとして、関連する投稿に設定されます。

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#SingleInclude)]

> [!TIP]  
> Entity Framework Core は自動的に修正をするナビゲーション プロパティをコンテキストのインスタンスに以前に読み込まれたその他のエンティティです。 場合でも、明示的には、ナビゲーション プロパティのデータを含まない、いくつかの場合は、プロパティを作成してか、以前に読み込まれたすべての関連するエンティティ。


単一のクエリでは、複数のリレーションシップから関連するデータを含めることができます。

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#MultipleIncludes)]

### <a name="including-multiple-levels"></a>複数のレベルを含む

使用して関連するデータの複数のレベルを含むへのリレーションシップを通じてドリルダウンすることができます、`ThenInclude`メソッドです。 次の例では、すべてのブログの関連する投稿を各投稿の作成者を読み込みます。

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#SingleThenInclude)]

複数の呼び出しをチェーンする`ThenInclude`関連データのレベルを含めてさらに続行します。

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#MultipleThenIncludes)]

これを同じクエリで複数のレベルと複数のルートから関連するデータを含めるにはすべてを組み合わせることができます。

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#IncludeTree)]

いずれかが含まれるエンティティの複数の関連エンティティを追加することがあります。 たとえば、クエリを実行するときに`Blog`が含まれています、`Posts`両方を格納して、`Author`と`Tags`の`Posts`です。 これを行うには、ルートからのパスを含めるそれぞれを指定する必要があります。 たとえば、 `Blog -> Posts -> Author` や `Blog -> Posts -> Tags`。 表示される冗長な結合、EF の統合はほとんどの場合、結合 SQL を生成するときにありません。

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#MultipleLeafIncludes)]

### <a name="ignored-includes"></a>無視が含まれています

不要になったクエリの開始とエンティティ型のインスタンスを返すように、クエリを変更する場合は、include 演算子は無視されます。

次の例では、include 演算子に基づいて、 `Blog`、ところが、`Select`演算子を使用して、匿名型を取得するクエリを変更します。 この場合、include 演算子がある影響しません。

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#IgnoredInclude)]

既定では、EF コアが、警告をログとは、演算子は無視されます。 参照してください[ログ](../miscellaneous/logging.md)ログ出力を表示する方法の詳細についてです。 スローするか、何もする include 演算子が無視される動作を変更できます。 では通常、コンテキストでのオプションを設定するときにこれは、 `DbContext.OnConfiguring`、または`Startup.cs`ASP.NET Core を使用している場合。

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/ThrowOnIgnoredInclude/BloggingContext.cs#OnConfiguring)]

## <a name="explicit-loading"></a>明示的読み込み

> [!NOTE]  
> この機能は、EF コア 1.1 で導入されました。

使用して、ナビゲーション プロパティを明示的に読み込むことができます、 `DbContext.Entry(...)` API です。

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#Eager)]

ナビゲーション プロパティは、関連エンティティを返す別のクエリを実行することによっても明示的に読み込むことができます。 変更の追跡が有効になっているかどうか、EF コアのすべてのエンティティは既に読み込まれているを参照する新たに読み込まれたエンティティのナビゲーション プロパティを設定を参照してくださいに既に読み込まれているエンティティのナビゲーション プロパティを設定が自動的にエンティティを読み込むときに、新たに読み込まれたエンティティです。

### <a name="querying-related-entities"></a>関連エンティティのクエリを実行します。

LINQ クエリを表すナビゲーション プロパティの内容を取得することもできます。

これにより、それらをメモリに読み込むことがなく、関連エンティティを aggregate 操作を実行しているなどの作業を行うことができます。

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#NavQueryAggregate)]

関連エンティティは、メモリに読み込まれるをフィルターすることもできます。

[!code-csharp[Main](../../../samples/core/Querying/Querying/RelatedData/Sample.cs#NavQueryFiltered)]

## <a name="lazy-loading"></a>遅延読み込み

遅延読み込みが EF のコアでまだサポートされていません。 表示することができます、[遅延読み込みバックログ項目に、](https://github.com/aspnet/EntityFramework/issues/3797)この機能を追跡するためにします。

## <a name="related-data-and-serialization"></a>関連データとシリアル化

EF コアは自動的に修正をナビゲーション プロパティ、しまうサイクルで、オブジェクト グラフ内ためです。 たとえば、ブログを読み込みが関連付けられて投稿投稿のコレクションを参照するブログ オブジェクトになります。 これらの投稿の各ブログへの参照になります。

一部のシリアル化フレームワークでは、このようなサイクルは許可されません。 たとえば、Json.NET であっても、循環参照が発生したときの場合、次の例外がスローされます。

> Newtonsoft.Json.JsonSerializationException: 自己のループを型 'MyApplication.Models.Blog' と 'ブログ' プロパティの検出を参照します。

ASP.NET Core を使用している場合は、オブジェクト グラフ内で見つかったサイクルを無視する Json.NET を構成できます。 これには、`ConfigureServices(...)`メソッド`Startup.cs`です。

``` csharp
public void ConfigureServices(IServiceCollection services)
{
    ...

    services.AddMvc()
        .AddJsonOptions(
            options => options.SerializerSettings.ReferenceLoopHandling = Newtonsoft.Json.ReferenceLoopHandling.Ignore
        );

    ...
}
```
