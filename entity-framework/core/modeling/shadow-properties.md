---
title: シャドウのプロパティ-EF Core
author: AndriySvyryd
ms.date: 01/03/2020
ms.assetid: 75369266-d2b9-4416-b118-ed238f81f599
uid: core/modeling/shadow-properties
ms.openlocfilehash: 229cfd83f75b01dff9ac9ad30ee55c7cc727c19e
ms.sourcegitcommit: 4e86f01740e407ff25e704a11b1f7d7e66bfb2a6
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/09/2020
ms.locfileid: "75781184"
---
# <a name="shadow-properties"></a>シャドウ プロパティ

Shadow プロパティは、.NET エンティティクラスで定義されていないが、EF Core モデルでそのエンティティ型に対して定義されているプロパティです。 これらのプロパティの値と状態は、単に変更トラッカーに保持されます。 シャドウプロパティは、マップされたエンティティ型では公開できないデータがデータベースに存在する場合に便利です。

## <a name="foreign-key-shadow-properties"></a>外部キーのシャドウのプロパティ

シャドウプロパティは、外部キープロパティで最もよく使用されます。この場合、2つのエンティティ間のリレーションシップはデータベース内の外部キー値によって表されますが、リレーションシップはエンティティ間のナビゲーションプロパティを使用してエンティティ型で管理されます。な. 慣例により、EF はリレーションシップが検出されたときに、外部キープロパティが依存エンティティクラスに見つからない場合に shadow プロパティを導入します。

このプロパティには `<navigation property name><principal key property name>` という名前が付けられます (プリンシパルエンティティを指す依存エンティティのナビゲーションが名前付けに使用されます)。 プリンシパルキープロパティ名にナビゲーションプロパティの名前が含まれている場合、その名前は `<principal key property name>`のみになります。 依存エンティティにナビゲーションプロパティがない場合は、その代わりにプリンシパルの型名が使用されます。

たとえば、次のコードリストでは、`Post` エンティティに `BlogId` shadow プロパティが導入されます。

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/ShadowForeignKey.cs?name=Conventions&highlight=21-23)]

## <a name="configuring-shadow-properties"></a>シャドウプロパティの構成

Fluent API を使用してシャドウプロパティを構成できます。 `Property`の文字列オーバーロードを呼び出した後は、他のプロパティに対して行う任意の構成呼び出しを連鎖させることができます。 次の例では、`Blog` に `LastUpdated`という名前の CLR プロパティがないため、shadow プロパティが作成されます。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ShadowProperty.cs?name=ShadowProperty&highlight=8)]

`Property` メソッドに指定された名前が既存のプロパティの名前 (shadow プロパティまたはエンティティクラスで定義されているプロパティ) と一致する場合、コードは新しい shadow プロパティを導入するのではなく、既存のプロパティを構成します。

## <a name="accessing-shadow-properties"></a>シャドウプロパティへのアクセス

Shadow プロパティ値は、`ChangeTracker` API を使用して取得および変更できます。

``` csharp
context.Entry(myBlog).Property("LastUpdated").CurrentValue = DateTime.Now;
```

シャドウプロパティは、`EF.Property` の静的メソッドを使用して LINQ クエリで参照できます。

``` csharp
var blogs = context.Blogs
    .OrderBy(b => EF.Property<DateTime>(b, "LastUpdated"));
```
