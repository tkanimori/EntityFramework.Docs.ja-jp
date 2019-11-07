---
title: リレーションシップ-EF Core
author: rowanmiller
ms.date: 10/27/2016
ms.assetid: 0ff736a3-f1b0-4b58-a49c-4a7094bd6935
uid: core/modeling/relationships
ms.openlocfilehash: 1e59ce9e19c12aa5564bc8467dcfcb3be8ee8996
ms.sourcegitcommit: 18ab4c349473d94b15b4ca977df12147db07b77f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2019
ms.locfileid: "73655669"
---
# <a name="relationships"></a>リレーションシップ

リレーションシップは、2つのエンティティが相互にどのように関連しているかを定義します。 リレーショナルデータベースでは、これは foreign key 制約によって表されます。

> [!NOTE]  
> この記事のほとんどのサンプルでは、一対多の関係を使用して概念を示しています。 一対一および多対多リレーションシップの例については、記事の最後にある「[その他のリレーションシップパターン](#other-relationship-patterns)」を参照してください。

## <a name="definition-of-terms"></a>用語の定義

リレーションシップの説明に使用される用語は多数あります。

* **依存エンティティ:** これは、外部キープロパティを含むエンティティです。 リレーションシップの "子" と呼ばれることもあります。

* **プリンシパルエンティティ:** これは、主キーと代替キーのプロパティを含むエンティティです。 リレーションシップの "親" と呼ばれることもあります。

* **外部キー:** エンティティが関連付けられているプリンシパルキープロパティの値を格納するために使用される、依存エンティティ内のプロパティ。

* **プリンシパルキー:** プリンシパルエンティティを一意に識別するプロパティ。 これは、主キーまたは代替キーの場合があります。

* **ナビゲーションプロパティ:** 関連エンティティへの参照を含む、プリンシパルエンティティまたは依存エンティティで定義されたプロパティ。

  * **コレクションナビゲーションプロパティ:** 関連する多くのエンティティへの参照を格納するナビゲーションプロパティ。

  * **参照ナビゲーションプロパティ:** 単一の関連エンティティへの参照を保持するナビゲーションプロパティ。

  * **逆ナビゲーションプロパティ:** この用語は、特定のナビゲーションプロパティを説明するときに、リレーションシップのもう一方の端のナビゲーションプロパティを参照します。

次のコードリストは、`Blog` との間の一対多リレーションシップを示してい `Post`

* `Post` が依存エンティティである

* `Blog` はプリンシパルエンティティです。

* `Post.BlogId` は外部キーです。

* `Blog.BlogId` がプリンシパルキー (この場合は、代替キーではなく主キー) です。

* `Post.Blog` は参照ナビゲーションプロパティです

* `Blog.Posts` はコレクションナビゲーションプロパティです。

* `Post.Blog` は `Blog.Posts` の逆ナビゲーションプロパティ (およびその逆) です。

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/Full.cs#Entities)]

## <a name="conventions"></a>規約

規則により、型で検出されたナビゲーションプロパティがある場合に、リレーションシップが作成されます。 プロパティは、参照先の型が現在のデータベースプロバイダーによってスカラー型としてマップされていない場合、ナビゲーションプロパティと見なされます。

> [!NOTE]  
> 規則によって検出されたリレーションシップは、常にプリンシパルエンティティの主キーを対象とします。 代替キーをターゲットにするには、Fluent API を使用して追加の構成を実行する必要があります。

### <a name="fully-defined-relationships"></a>完全に定義されたリレーションシップ

リレーションシップの最も一般的なパターンは、リレーションシップの両端と、依存エンティティクラスで定義されている外部キープロパティの両方でナビゲーションプロパティを定義することです。

* 2つの型の間にナビゲーションプロパティのペアが見つかった場合、それらは同じリレーションシップの逆ナビゲーションプロパティとして構成されます。

* 依存エンティティに `<primary key property name>`、`<navigation property name><primary key property name>`、または `<principal entity name><primary key property name>` という名前のプロパティが含まれている場合は、外部キーとして構成されます。

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/Full.cs?name=Entities&highlight=6,15,16)]

> [!WARNING]  
> 2つの型の間に複数のナビゲーションプロパティが定義されている場合 (つまり、互いをポイントするナビゲーションの複数の異なるペア)、規則によってリレーションシップは作成されず、手動でこれらのプロパティを構成して、ナビゲーションプロパティが上にあります。

### <a name="no-foreign-key-property"></a>外部キープロパティがありません

依存エンティティクラスで外部キープロパティを定義することをお勧めしますが、必須ではありません。 外部キープロパティが見つからない場合は、`<navigation property name><principal key property name>` という名前のシャドウ外部キープロパティが導入されます (詳細については、「[シャドウプロパティ](shadow-properties.md)」を参照してください)。

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/NoForeignKey.cs?name=Entities&highlight=6,15)]

### <a name="single-navigation-property"></a>単一ナビゲーションプロパティ

ナビゲーションプロパティを1つだけ含め (逆のナビゲーションも、外部キープロパティも含まない)、規約によってリレーションシップを定義するには十分です。 また、1つのナビゲーションプロパティと外部キープロパティを使用することもできます。

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/OneNavigation.cs?name=Entities&highlight=6)]

### <a name="cascade-delete"></a>連鎖削除

慣例により、cascade delete は必要なリレーションシップの場合は*cascade*に、オプションのリレーションシップの場合は*clientsetnull*に設定されます。 *Cascade*は、依存エンティティも削除されることを意味します。 *Clientsetnull*は、メモリに読み込まれていない依存エンティティは変更されず、手動で削除するか、有効なプリンシパルエンティティを指すように更新する必要があります。 メモリに読み込まれるエンティティの場合、EF Core は外部キーのプロパティを null に設定しようとします。

必須リレーションシップとオプションリレーションシップの違いについては、「[必須リレーションシップ」と「オプションのリレーションシップ](#required-and-optional-relationships)」セクションを参照してください。

さまざまな削除動作と規約で使用される既定値の詳細については、「 [Cascade delete](../saving/cascade-delete.md) 」を参照してください。

## <a name="data-annotations"></a>データの注釈

リレーションシップの構成に使用できるデータ注釈には、`[ForeignKey]` と `[InverseProperty]`の2つがあります。 これらは、`System.ComponentModel.DataAnnotations.Schema` 名前空間で使用できます。

### <a name="foreignkey"></a>[ForeignKey]

データ注釈を使用して、特定のリレーションシップの外部キープロパティとして使用するプロパティを構成できます。 これは通常、外部キープロパティが規則によって検出されない場合に実行されます。

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Relationships/ForeignKey.cs?highlight=30)]

> [!TIP]  
> `[ForeignKey]` 注釈は、リレーションシップのいずれかのナビゲーションプロパティに配置できます。 依存エンティティクラスのナビゲーションプロパティに移動する必要はありません。

### <a name="inverseproperty"></a>[InverseProperty]

データ注釈を使用すると、依存エンティティとプリンシパルエンティティのナビゲーションプロパティをどのように組み合わせるかを構成できます。 これは通常、2つのエンティティ型の間に複数のナビゲーションプロパティのペアがある場合に実行されます。

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Relationships/InverseProperty.cs?highlight=33,36)]

## <a name="fluent-api"></a>Fluent API

Fluent API でリレーションシップを構成するには、まず、リレーションシップを構成するナビゲーションプロパティを特定します。 `HasOne` または `HasMany` によって、構成を開始するエンティティ型のナビゲーションプロパティが識別されます。 次に、`WithOne` または `WithMany` への呼び出しを連結して、逆のナビゲーションを識別します。 `HasOne`/`WithOne` は参照ナビゲーションプロパティに使用され、`HasMany`/`WithMany` コレクションのナビゲーションプロパティに使用されます。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/NoForeignKey.cs?highlight=14-16)]

### <a name="single-navigation-property"></a>単一ナビゲーションプロパティ

ナビゲーションプロパティが1つしかない場合は、`WithOne` と `WithMany`のパラメーターなしのオーバーロードがあります。 これは、リレーションシップのもう一方の端には概念的に参照またはコレクションが存在するが、エンティティクラスにはナビゲーションプロパティが含まれていないことを示します。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/OneNavigation.cs?highlight=14-16)]

### <a name="foreign-key"></a>外部キー

Fluent API を使用して、特定のリレーションシップの外部キープロパティとして使用するプロパティを構成できます。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ForeignKey.cs?highlight=17)]

次のコードリストは、複合外部キーを構成する方法を示しています。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/CompositeForeignKey.cs?highlight=20)]

`HasForeignKey(...)` の文字列オーバーロードを使用して、shadow プロパティを外部キーとして構成できます (詳細については、「 [Shadow Properties](shadow-properties.md) 」を参照してください)。 次に示すように、外部キーとして使用する前に、シャドウプロパティをモデルに明示的に追加することをお勧めします。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ShadowForeignKey.cs#Sample)]

### <a name="without-navigation-property"></a>ナビゲーションプロパティなし

必ずしもナビゲーションプロパティを指定する必要はありません。 リレーションシップの一方の側に外部キーを指定するだけです。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/NoNavigation.cs?highlight=14-17)]

### <a name="principal-key"></a>プリンシパルキー

外部キーで主キー以外のプロパティを参照する場合は、Fluent API を使用してリレーションシップのプリンシパルキープロパティを構成できます。 プリンシパルキーとして構成したプロパティは、代替キーとして自動的に設定されます (詳細については、「[代替キー](alternate-keys.md) 」を参照してください)。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/PrincipalKey.cs?name=PrincipalKey&highlight=11)]

次のコードリストは、複合プリンシパルキーを構成する方法を示しています。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/CompositePrincipalKey.cs?name=Composite&highlight=11)]

> [!WARNING]  
> プリンシパルキープロパティを指定する順序は、外部キーに指定されている順序と一致している必要があります。

### <a name="required-and-optional-relationships"></a>必須およびオプションのリレーションシップ

Fluent API を使用して、リレーションシップが必須か省略可能かを構成できます。 これは最終的に、外部キープロパティが必須か省略可能かを制御します。 これは、シャドウ状態の外部キーを使用している場合に最も役立ちます。 エンティティクラスに外部キープロパティがある場合、リレーションシップの requiredness は、外部キープロパティが必須か省略可能かに基づいて決定されます (詳細については、[必須プロパティと省略可能なプロパティ](required-optional.md)を参照してください)。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/Required.cs?name=Required&highlight=11)]

### <a name="cascade-delete"></a>連鎖削除

Fluent API を使用して、特定のリレーションシップに対して連鎖削除動作を明示的に構成することができます。

各オプションの詳細については、「データの保存」セクションの「[連鎖削除](../saving/cascade-delete.md)」を参照してください。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/CascadeDelete.cs?name=CascadeDelete&highlight=11)]

## <a name="other-relationship-patterns"></a>その他のリレーションシップパターン

### <a name="one-to-one"></a>一対一

一対一のリレーションシップには、両側の参照ナビゲーションプロパティがあります。 これらは一対多のリレーションシップと同じ規則に従いますが、依存関係が各プリンシパルに関連付けられるように、外部キープロパティに一意のインデックスが導入されます。

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/OneToOne.cs?name=Property&highlight=6,15,16)]

> [!NOTE]  
> EF は、外部キープロパティを検出する機能に基づいて、依存するエンティティの1つを選択します。 依存関係として間違ったエンティティが選択されている場合は、Fluent API を使用してこれを修正できます。

Fluent API との関係を構成する場合は、`HasOne` メソッドと `WithOne` メソッドを使用します。

外部キーを構成するときに、依存エンティティ型を指定する必要があります。次の一覧で `HasForeignKey` に提供されているジェネリックパラメーターに注意してください。 一対多のリレーションシップでは、参照ナビゲーションを持つエンティティが依存しており、コレクションを持つエンティティがプリンシパルであることが明らかです。 ただし、これは一対一のリレーションシップではないため、明示的に定義する必要があります。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/OneToOne.cs?name=OneToOne&highlight=11)]

### <a name="many-to-many"></a>多対多

結合テーブルを表すエンティティクラスのない多対多リレーションシップは、まだサポートされていません。 ただし、結合テーブルのエンティティクラスを含め、2つの個別の一対多リレーションシップをマップすることで、多対多リレーションシップを表すことができます。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ManyToMany.cs?name=ManyToMany&highlight=11,12,13,14,16,17,18,19,39,40,41,42,43,44,45,46)]
