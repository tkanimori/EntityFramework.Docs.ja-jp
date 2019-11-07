---
title: シャドウのプロパティ-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 75369266-d2b9-4416-b118-ed238f81f599
uid: core/modeling/shadow-properties
ms.openlocfilehash: ab57358dd247e32c4ca0f57d07b4cb98f2b85d29
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655963"
---
# <a name="shadow-properties"></a>シャドウ プロパティ

Shadow プロパティは、.NET エンティティクラスで定義されていないが、EF Core モデルでそのエンティティ型に対して定義されているプロパティです。 これらのプロパティの値と状態は、単に変更トラッカーに保持されます。

シャドウプロパティは、マップされたエンティティ型では公開できないデータがデータベースに存在する場合に便利です。 2つのエンティティ間のリレーションシップはデータベース内の外部キー値によって表されますが、リレーションシップはエンティティ型間のナビゲーションプロパティを使用してエンティティ型で管理される外部キープロパティで最もよく使用されます。

シャドウプロパティの値は、`ChangeTracker` API を使用して取得および変更できます。

``` csharp
context.Entry(myBlog).Property("LastUpdated").CurrentValue = DateTime.Now;
```

シャドウプロパティは、`EF.Property` の静的メソッドを使用して LINQ クエリで参照できます。

``` csharp
var blogs = context.Blogs
    .OrderBy(b => EF.Property<DateTime>(b, "LastUpdated"));
```

## <a name="conventions"></a>規約

リレーションシップが検出されたが、依存エンティティクラスで外部キープロパティが見つからない場合は、規則によってシャドウプロパティを作成できます。 この場合、shadow 外部キープロパティが導入されます。 Shadow 外部キーのプロパティには `<navigation property name><principal key property name>` という名前が付けられます (プリンシパルエンティティを指す依存エンティティのナビゲーションは、名前付けに使用されます)。 プリンシパルキープロパティ名にナビゲーションプロパティの名前が含まれている場合、その名前は `<principal key property name>`のみになります。 依存エンティティにナビゲーションプロパティがない場合は、その代わりにプリンシパルの型名が使用されます。

たとえば、次のコードリストでは、`Post` エンティティに `BlogId` shadow プロパティが導入されます。

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/ShadowForeignKey.cs?name=Conventions)]

## <a name="data-annotations"></a>データの注釈

シャドウプロパティをデータ注釈と共に作成することはできません。

## <a name="fluent-api"></a>Fluent API

Fluent API を使用してシャドウプロパティを構成できます。 `Property` の文字列オーバーロードを呼び出した後は、他のプロパティに対して行う任意の構成呼び出しを連鎖させることができます。

`Property` メソッドに指定された名前が既存のプロパティの名前 (shadow プロパティまたはエンティティクラスで定義されているプロパティ) と一致する場合、コードは新しい shadow プロパティを導入するのではなく、既存のプロパティを構成します。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ShadowProperty.cs?name=ShadowProperty&highlight=8)]
