---
title: シャドウとインデクサーのプロパティ-EF Core
description: Entity Framework Core モデルでの shadow および indexer プロパティの構成
author: AndriySvyryd
ms.date: 10/09/2020
uid: core/modeling/shadow-properties
ms.openlocfilehash: f03dc000bb111253ae74c05a668703f2e6237a57
ms.sourcegitcommit: f3512e3a98e685a3ba409c1d0157ce85cc390cf4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2020
ms.locfileid: "94430418"
---
# <a name="shadow-and-indexer-properties"></a>Shadow および Indexer プロパティ

Shadow プロパティは、.NET エンティティクラスで定義されていないが、EF Core モデルでそのエンティティ型に対して定義されているプロパティです。 これらのプロパティの値と状態は、単に変更トラッカーに保持されます。 シャドウプロパティは、マップされたエンティティ型では公開できないデータがデータベースに存在する場合に便利です。

## <a name="foreign-key-shadow-properties"></a>外部キーのシャドウのプロパティ

シャドウプロパティは、外部キープロパティで最もよく使用されます。この場合、2つのエンティティ間のリレーションシップはデータベース内の外部キー値によって表されますが、リレーションシップはエンティティ型間のナビゲーションプロパティを使用してエンティティ型で管理されます。 慣例により、EF はリレーションシップが検出されたときに、外部キープロパティが依存エンティティクラスに見つからない場合に shadow プロパティを導入します。

このプロパティにはという名前が付けられ `<navigation property name><principal key property name>` ます (これは、プリンシパルエンティティを指し示す依存エンティティのナビゲーションで、名前付けに使用されます)。 プリンシパルキープロパティ名にナビゲーションプロパティの名前が含まれている場合、その名前はだけになり `<principal key property name>` ます。 依存エンティティにナビゲーションプロパティがない場合は、その代わりにプリンシパルの型名が使用されます。

たとえば、次のコードリストでは、 `BlogId` shadow プロパティがエンティティに導入され `Post` ます。

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/ShadowForeignKey.cs?name=Conventions&highlight=21-23)]

## <a name="configuring-shadow-properties"></a>シャドウプロパティの構成

Fluent API を使用してシャドウプロパティを構成できます。 の文字列オーバーロードを呼び出した後は、 `Property` 他のプロパティに対して行う任意の構成呼び出しを連鎖させることができます。 次の例では、 `Blog` にという名前の CLR プロパティがないため、 `LastUpdated` shadow プロパティが作成されています。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/ShadowProperty.cs?name=ShadowProperty&highlight=8)]

メソッドに指定された名前が `Property` 既存のプロパティの名前 (shadow プロパティまたは entity クラスで定義されているプロパティ) と一致する場合、コードは新しい shadow プロパティを導入するのではなく、既存のプロパティを構成します。

## <a name="accessing-shadow-properties"></a>シャドウプロパティへのアクセス

シャドウプロパティの値は、API を使用して取得および変更でき `ChangeTracker` ます。

```csharp
context.Entry(myBlog).Property("LastUpdated").CurrentValue = DateTime.Now;
```

シャドウプロパティは、静的メソッドを使用して LINQ クエリで参照でき `EF.Property` ます。

```csharp
var blogs = context.Blogs
    .OrderBy(b => EF.Property<DateTime>(b, "LastUpdated"));
```

返されるエンティティは変更トラッカーによって追跡されないため、no tracking クエリの後にシャドウプロパティにアクセスすることはできません。

## <a name="property-bag-entity-types"></a>プロパティバッグのエンティティ型

> [!NOTE]
> プロパティバッグエンティティ型のサポートは EF Core 5.0 で追加されました。

インデクサープロパティのみを含むエンティティ型は、プロパティバッグエンティティ型と呼ばれます。 これらのエンティティ型には、シャドウプロパティはありません。 現時点 `Dictionary<string, object>` では、プロパティバッグエンティティ型としてのみサポートされています。 これは、一意の名前を持つ共有エンティティ型として構成する必要があり、対応する `DbSet` プロパティは呼び出しを使用して実装する必要があることを意味し `Set` ます。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/SharedType.cs?name=SharedType&highlight=3,7)]
