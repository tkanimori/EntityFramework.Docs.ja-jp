---
title: DbContext プーリング
description: Entity Framework Core での DbContext プーリング
author: rick-anderson
ms.author: riande
ms.date: 9/19/2020
uid: core/miscellaneous/context-pooling
ms.openlocfilehash: 8638c838511be85bd994751b9911b107974dfe2f
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92061958"
---
# <a name="dbcontext-pooling"></a>DbContext プール

`AddDbContextPool` インスタンスのプールを有効に `DbContext` します。 コンテキストプーリングは、要求ごとに新しいインスタンスを作成するのではなく、コンテキストインスタンスを再利用することで、web サーバーなどの大規模なシナリオでスループットを向上させることができます。

EF Core を使用する ASP.NET Core アプリの一般的なパターンには、カスタム <xref:Microsoft.EntityFrameworkCore.DbContext> 型を [依存関係挿入](/aspnet/core/fundamentals/dependency-injection) コンテナーに登録し、コントローラーまたは Razor Pages のコンストラクターパラメーターを使用してその型のインスタンスを取得する作業が含まれます。 コンストラクターインジェクションを使用して、要求ごとに新しいコンテキストインスタンスが作成されます。

<xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextPool%2A> 再利用可能なコンテキストインスタンスのプールを有効にします。 コンテキストプーリングを使用するには、 `AddDbContextPool` サービスの登録時にではなく、メソッドを使用し `AddDbContext` ます。

```csharp
services.AddDbContextPool<BloggingContext>(
    options => options.UseSqlServer(connectionString));
```

を使用すると、コンテキストインスタンスが要求されたときに `AddDbContextPool` 、EF は、プール内に使用可能なインスタンスがあるかどうかを最初に確認します。 要求処理が終わると、インスタンス上のあらゆる状態がリセットされ、インスタンス自体はプールに戻ります。

これは、ADO.NET プロバイダーでの接続プールの動作と概念的に似ており、コンテキストインスタンスの初期化にかかるコストを節約できるという利点があります。

`poolSize`のパラメーターは、 <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContextPool%2A> プールに保持されているインスタンスの最大数を設定します。 を `poolSize` 超えた場合、新しいコンテキストインスタンスはキャッシュされず、EF は必要に応じてインスタンスを作成する非プール動作にフォールバックします。

## <a name="limitations"></a>制限事項

コンテキストの初期化が大幅なコストであることを示すために、アプリのプロファイリングとテストを行う必要があります。

`AddDbContextPool` では、コンテキストのメソッドで実行できる内容にいくつかの制限があり `OnConfiguring` ます。

> [!WARNING]
> 状態を維持するアプリでコンテキストプーリングを使用することは避けてください。 たとえば、要求間で共有しないようなコンテキストのプライベートフィールドです。 EF Core は、コンテキストインスタンスをプールに追加する前に、認識している状態のみをリセットします。

コンテキストプーリングは、要求間で同じコンテキストインスタンスを再利用することによって機能します。 これは、インスタンス自体を使用して、永続化できるように、 [シングルトン](/aspnet/core/fundamentals/dependency-injection#service-lifetimes) として効果的に登録されることを意味します。

コンテキストプーリングは、解決されたサービスを含むコンテキスト構成が要求間で固定されているシナリオを対象としています。 [スコープ](/aspnet/core/fundamentals/dependency-injection#service-lifetimes)が指定されたサービスが必要な場合、または構成を変更する必要がある場合は、プーリングを使用しないでください。 プールからのパフォーマンスの向上は通常、高度に最適化されたシナリオ以外ではごくわずかです。
