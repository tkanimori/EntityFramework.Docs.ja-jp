---
title: リレーションシップ-EF Core
description: Entity Framework Core を使用するときにエンティティ型間のリレーションシップを構成する方法
author: AndriySvyryd
ms.date: 10/01/2020
uid: core/modeling/relationships
ms.openlocfilehash: c92fa1904436ec43ff8918354a99440079e2a96a
ms.sourcegitcommit: 42bbf7f68e92c364c5fff63092d3eb02229f568d
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2020
ms.locfileid: "94503216"
---
# <a name="relationships"></a>リレーションシップ

リレーションシップは、2つのエンティティが相互にどのように関連しているかを定義します。 リレーショナルデータベースでは、これは foreign key 制約によって表されます。

> [!NOTE]  
> この記事のほとんどのサンプルでは、一対多の関係を使用して概念を示しています。 一対一および多対多リレーションシップの例については、記事の最後にある「 [その他のリレーションシップパターン](#other-relationship-patterns) 」を参照してください。

## <a name="definition-of-terms"></a>用語の定義

リレーションシップの説明に使用される用語は多数あります。

* **依存エンティティ:** これは、外部キープロパティを含むエンティティです。 リレーションシップの "子" と呼ばれることもあります。

* **プリンシパルエンティティ:** これは、主キーと代替キーのプロパティを含むエンティティです。 リレーションシップの "親" と呼ばれることもあります。

* **プリンシパルキー:** プリンシパルエンティティを一意に識別するプロパティです。 これは、主キーまたは代替キーの場合があります。

* **外部キー:** 関連エンティティのプリンシパルキー値を格納するために使用される、依存エンティティ内のプロパティ。

* **ナビゲーションプロパティ:** 関連エンティティを参照するプリンシパルエンティティまたは依存エンティティで定義されたプロパティ。

  * **コレクションナビゲーションプロパティ:** 関連する多くのエンティティへの参照を格納するナビゲーションプロパティ。

  * **参照ナビゲーションプロパティ:** 単一の関連エンティティへの参照を保持するナビゲーションプロパティ。

  * **逆ナビゲーションプロパティ:** この用語は、特定のナビゲーションプロパティを説明するときに、リレーションシップのもう一方の端のナビゲーションプロパティを参照します。
  
* **自己参照リレーションシップ:** 依存エンティティ型とプリンシパルエンティティ型が同じリレーションシップ。

次のコードは、との間の一対多リレーションシップを示しています。 `Blog``Post`

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/Full.cs#Full)]

* `Post` は依存エンティティです。

* `Blog` プリンシパルエンティティ

* `Blog.BlogId` は主キー (この場合は、代替キーではなく主キー) です。

* `Post.BlogId` は外部キーです。

* `Post.Blog` 参照ナビゲーションプロパティです

* `Blog.Posts` コレクションナビゲーションプロパティです

* `Post.Blog` はの逆ナビゲーションプロパティです `Blog.Posts` (逆も同様)。

## <a name="conventions"></a>規約

既定では、型に対してナビゲーションプロパティが検出されると、リレーションシップが作成されます。 プロパティは、参照先の型が現在のデータベースプロバイダーによってスカラー型としてマップされていない場合、ナビゲーションプロパティと見なされます。

> [!NOTE]  
> 規則によって検出されたリレーションシップは、常にプリンシパルエンティティの主キーを対象とします。 代替キーをターゲットにするには、Fluent API を使用して追加の構成を実行する必要があります。

### <a name="fully-defined-relationships"></a>完全に定義されたリレーションシップ

リレーションシップの最も一般的なパターンは、リレーションシップの両端と、依存エンティティクラスで定義されている外部キープロパティの両方でナビゲーションプロパティを定義することです。

* 2つの型の間にナビゲーションプロパティのペアが見つかった場合、それらは同じリレーションシップの逆ナビゲーションプロパティとして構成されます。

* 依存エンティティに、これらのパターンのいずれかに一致する名前を持つプロパティが含まれている場合は、外部キーとして構成されます。
  * `<navigation property name><principal key property name>`
  * `<navigation property name>Id`
  * `<principal entity name><principal key property name>`
  * `<principal entity name>Id`

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/Full.cs?name=Full&highlight=6,15-16)]

この例では、強調表示されたプロパティを使用してリレーションシップを構成します。

> [!NOTE]
> プロパティが主キーであるか、またはプリンシパルキーと互換性のない型である場合、外部キーとして構成されません。

> [!NOTE]
> 3.0 より EF Core 前では、プリンシパルキープロパティとまったく同じ名前のプロパティが、[外部キーとも一致](https://github.com/dotnet/efcore/issues/13274)していました。

### <a name="no-foreign-key-property"></a>外部キープロパティがありません

依存エンティティクラスで外部キープロパティを定義することをお勧めしますが、必須ではありません。 外部キープロパティが見つからない場合は、という名前の [シャドウ外部キープロパティ](xref:core/modeling/shadow-properties) が導入され `<navigation property name><principal key property name>` `<principal entity name><principal key property name>` ます。依存する型にナビゲーションが存在しない場合はになります。

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/NoForeignKey.cs?name=NoForeignKey&highlight=6,15)]

この例では、シャドウ外部キーは、 `BlogId` ナビゲーション名の前に冗長があるためです。

> [!NOTE]
> 同じ名前のプロパティが既に存在する場合は、シャドウプロパティ名の後に数字が付加されます。

### <a name="single-navigation-property"></a>単一ナビゲーションプロパティ

ナビゲーションプロパティを1つだけ含め (逆のナビゲーションも、外部キープロパティも含まない)、規約によってリレーションシップを定義するには十分です。 また、1つのナビゲーションプロパティと外部キープロパティを使用することもできます。

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/OneNavigation.cs?name=OneNavigation&highlight=6)]

### <a name="limitations"></a>制限事項

2つの型の間に複数のナビゲーションプロパティが定義されている場合 (つまり、互いをポイントするナビゲーションの1対のペアだけを超えている場合)、ナビゲーションプロパティによって表されるリレーションシップがあいまいになります。 あいまいさを解決するには、手動で構成する必要があります。

### <a name="cascade-delete"></a>連鎖削除

慣例により、cascade delete は必要なリレーションシップの場合は *cascade* に、オプションのリレーションシップの場合は *clientsetnull* に設定されます。 *Cascade* は、依存エンティティも削除されることを意味します。 *Clientsetnull* は、メモリに読み込まれていない依存エンティティは変更されず、手動で削除するか、有効なプリンシパルエンティティを指すように更新する必要があります。 メモリに読み込まれるエンティティの場合、EF Core は外部キーのプロパティを null に設定しようとします。

必須リレーションシップとオプションリレーションシップの違いについては、「 [必須リレーションシップ」と「オプションのリレーションシップ](#required-and-optional-relationships) 」セクションを参照してください。

さまざまな削除動作と規約で使用される既定値の詳細については、「 [Cascade delete](xref:core/saving/cascade-delete) 」を参照してください。

## <a name="manual-configuration"></a>手動構成

### <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

Fluent API でリレーションシップを構成するには、まず、リレーションシップを構成するナビゲーションプロパティを特定します。 `HasOne` またはは、 `HasMany` 構成を開始するエンティティ型のナビゲーションプロパティを識別します。 次に、またはへの呼び出しを連結し `WithOne` `WithMany` て、逆のナビゲーションを識別します。 `HasOne`/`WithOne`は、参照ナビゲーションプロパティに使用され、 `HasMany` / `WithMany` コレクションナビゲーションプロパティに使用されます。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/NoForeignKey.cs?name=NoForeignKey&highlight=8-10)]

### <a name="data-annotations"></a>[データの注釈](#tab/data-annotations)

データ注釈を使用すると、依存エンティティとプリンシパルエンティティのナビゲーションプロパティをどのように組み合わせるかを構成できます。 これは通常、2つのエンティティ型の間に複数のナビゲーションプロパティのペアがある場合に実行されます。

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Relationships/InverseProperty.cs?name=InverseProperty&highlight=20,23)]

> [!NOTE]
> 依存エンティティのプロパティに対してのみ [必須] を使用すると、リレーションシップの requiredness に影響を与えることができます。 [必須] プリンシパルエンティティからのナビゲーションは通常は無視されますが、エンティティが依存するエンティティになる場合があります。

> [!NOTE]
> データ注釈 `[ForeignKey]` と `[InverseProperty]` は、名前空間で使用でき `System.ComponentModel.DataAnnotations.Schema` ます。 `[Required]` は、名前空間で使用でき `System.ComponentModel.DataAnnotations` ます。

---

### <a name="single-navigation-property"></a>単一ナビゲーションプロパティ

ナビゲーションプロパティが1つしかない場合は、とのパラメーターなしのオーバーロードがあり `WithOne` `WithMany` ます。 これは、リレーションシップのもう一方の端には概念的に参照またはコレクションが存在するが、エンティティクラスにはナビゲーションプロパティが含まれていないことを示します。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/OneNavigation.cs?name=OneNavigation&highlight=8-10)]

---

### <a name="configuring-navigation-properties"></a>ナビゲーションプロパティの構成

> [!NOTE]
> この機能は、EF Core 5.0 で追加されました。

ナビゲーションプロパティが作成された後で、さらに構成する必要がある場合があります。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/NavigationConfiguration.cs?name=NavigationConfiguration&highlight=7-9)]

> [!NOTE]
> この呼び出しを使用してナビゲーションプロパティを作成することはできません。 これは、リレーションシップの定義または規則によって以前に作成されたナビゲーションプロパティの構成にのみ使用されます。

### <a name="foreign-key"></a>外部キー

#### <a name="fluent-api-simple-key"></a>[Fluent API (単純キー)](#tab/fluent-api-simple-key)

Fluent API を使用して、特定のリレーションシップの外部キープロパティとして使用するプロパティを構成できます。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ForeignKey.cs?name=ForeignKey&highlight=11)]

#### <a name="fluent-api-composite-key"></a>[Fluent API (複合キー)](#tab/fluent-api-composite-key)

Fluent API を使用して、特定のリレーションシップの複合外部キープロパティとして使用するプロパティを構成できます。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/CompositeForeignKey.cs?name=CompositeForeignKey&highlight=13)]

#### <a name="data-annotations-simple-key"></a>[データ注釈 (単純キー)](#tab/data-annotations-simple-key)

データ注釈を使用して、特定のリレーションシップの外部キープロパティとして使用するプロパティを構成できます。 これは通常、外部キープロパティが規則によって検出されない場合に実行されます。

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/Relationships/ForeignKey.cs?name=ForeignKey&highlight=17)]

> [!TIP]  
> 注釈は、 `[ForeignKey]` リレーションシップのいずれかのナビゲーションプロパティに配置できます。 依存エンティティクラスのナビゲーションプロパティに移動する必要はありません。

> [!NOTE]
> ナビゲーションプロパティでを使用して指定されたプロパティは、 `[ForeignKey]` 依存する型に存在する必要はありません。 この場合、指定した名前を使用して、シャドウ外部キーが作成されます。

---

#### <a name="shadow-foreign-key"></a>シャドウ外部キー

の文字列オーバーロードを使用し `HasForeignKey(...)` て、shadow プロパティを外部キーとして構成できます (詳細については、「 [shadow Properties](xref:core/modeling/shadow-properties) 」を参照してください)。 次に示すように、外部キーとして使用する前に、シャドウプロパティをモデルに明示的に追加することをお勧めします。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ShadowForeignKey.cs?name=ShadowForeignKey&highlight=10,16)]

#### <a name="foreign-key-constraint-name"></a>外部キー制約名

慣例により、リレーショナルデータベースを対象とする場合、外部キー制約には FK という名前が付けられ \_ \<dependent type name> \_ \<principal type name> \_ \<foreign key property name> ます。 複合外部キーの場合、は、 \<foreign key property name> 外部キープロパティ名のアンダースコアで区切られたリストになります。

制約名は次のように構成することもできます。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ConstraintName.cs?name=ConstraintName&highlight=6-7)]

### <a name="without-navigation-property"></a>ナビゲーションプロパティなし

必ずしもナビゲーションプロパティを指定する必要はありません。 リレーションシップの一方の側に外部キーを指定するだけです。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/NoNavigation.cs?name=NoNavigation&highlight=8-11)]

### <a name="principal-key"></a>プリンシパルキー

外部キーで主キー以外のプロパティを参照する場合は、Fluent API を使用してリレーションシップのプリンシパルキープロパティを構成できます。 プリンシパルキーとして構成したプロパティは、自動的に [代替キー](xref:core/modeling/keys#alternate-keys)として設定されます。

#### <a name="simple-key"></a>[単純キー](#tab/simple-key)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/PrincipalKey.cs?name=PrincipalKey&highlight=11)]

#### <a name="composite-key"></a>[複合キー](#tab/composite-key)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/CompositePrincipalKey.cs?name=CompositePrincipalKey&highlight=11)]

> [!WARNING]  
> プリンシパルキープロパティを指定する順序は、外部キーに指定されている順序と一致している必要があります。

---

### <a name="required-and-optional-relationships"></a>必須およびオプションのリレーションシップ

Fluent API を使用して、リレーションシップが必須か省略可能かを構成できます。 これは最終的に、外部キープロパティが必須か省略可能かを制御します。 これは、シャドウ状態の外部キーを使用している場合に最も役立ちます。 エンティティクラスに外部キープロパティがある場合、リレーションシップの requiredness は、外部キープロパティが必須か省略可能かに基づいて決定されます (詳細については、 [必須プロパティと省略可能なプロパティ](xref:core/modeling/entity-properties#required-and-optional-properties) を参照してください)。

外部キープロパティは依存エンティティ型にあるため、必要に応じて構成する場合は、すべての依存エンティティに対応するプリンシパルエンティティが必要であることを意味します。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/Required.cs?name=Required&highlight=6)]

> [!NOTE]
> を呼び出す `IsRequired(false)` と、特に構成されていない限り、外部キープロパティも省略可能になります。

### <a name="cascade-delete"></a>連鎖削除

Fluent API を使用して、特定のリレーションシップに対して連鎖削除動作を明示的に構成することができます。

各オプションの詳細については、「 [連鎖削除](xref:core/saving/cascade-delete) 」を参照してください。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/CascadeDelete.cs?name=CascadeDelete&highlight=6)]

## <a name="other-relationship-patterns"></a>その他のリレーションシップパターン

### <a name="one-to-one"></a>一対一

一対一のリレーションシップには、両側の参照ナビゲーションプロパティがあります。 これらは一対多のリレーションシップと同じ規則に従いますが、依存関係が各プリンシパルに関連付けられるように、外部キープロパティに一意のインデックスが導入されます。

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/Relationships/OneToOne.cs?name=OneToOne&highlight=6,15-16)]

> [!NOTE]  
> EF は、外部キープロパティを検出する機能に基づいて、依存するエンティティの1つを選択します。 依存関係として間違ったエンティティが選択されている場合は、Fluent API を使用してこれを修正できます。

Fluent API との関係を構成する場合は、 `HasOne` メソッドとメソッドを使用し `WithOne` ます。

外部キーを構成するときは、依存エンティティ型を指定する必要があります。以下の一覧で、に提供されているジェネリックパラメーターに注意して `HasForeignKey` ください。 一対多のリレーションシップでは、参照ナビゲーションを持つエンティティが依存しており、コレクションを持つエンティティがプリンシパルであることが明らかです。 ただし、これは一対一のリレーションシップではないため、明示的に定義する必要があります。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/OneToOne.cs?name=OneToOne&highlight=11)]

既定では、依存側は省略可能と見なされますが、必要に応じて構成できます。 ただし、依存エンティティが指定されているかどうかは EF によって検証されません。そのため、この構成では、データベースマッピングで適用が許可されている場合にのみ、違いがあります。 この一般的なシナリオは、既定でテーブル分割を使用する参照所有型です。

[!code-csharp[Main](../../../samples/core/Modeling/OwnedEntities/OwnedEntityContext.cs?name=Required&highlight=11-12)]

この構成では、に対応する列は、 `ShippingAddress` データベースで null 非許容としてマークされます。

> [!NOTE]
> [Null 非許容の参照型](/dotnet/csharp/nullable-references)を使用している場合は、を呼び出す `IsRequired` 必要はありません。

> [!NOTE]
> EF Core 5.0 に、依存関係が必要かどうかを構成する機能が追加されました。

### <a name="many-to-many"></a>多対多

多対多リレーションシップでは、両側にコレクションナビゲーションプロパティが必要です。 これらは、他の種類のリレーションシップと同様に規則によって検出されます。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ManyToManyShared.cs?name=ManyToManyShared)]

このリレーションシップをデータベースで実装する方法は、との外部キーを含む結合テーブルによって行われ `Post` `Tag` ます。 たとえば、上記のモデルのリレーショナルデータベースに EF が作成されます。

```sql
CREATE TABLE [Posts] (
    [PostId] int NOT NULL IDENTITY,
    [Title] nvarchar(max) NULL,
    [Content] nvarchar(max) NULL,
    CONSTRAINT [PK_Posts] PRIMARY KEY ([PostId])
);

CREATE TABLE [Tags] (
    [TagId] nvarchar(450) NOT NULL,
    CONSTRAINT [PK_Tags] PRIMARY KEY ([TagId])
);

CREATE TABLE [PostTag] (
    [PostsId] int NOT NULL,
    [TagsId] nvarchar(450) NOT NULL,
    CONSTRAINT [PK_PostTag] PRIMARY KEY ([PostsId], [TagsId]),
    CONSTRAINT [FK_PostTag_Posts_PostsId] FOREIGN KEY ([PostsId]) REFERENCES [Posts] ([PostId]) ON DELETE CASCADE,
    CONSTRAINT [FK_PostTag_Tags_TagsId] FOREIGN KEY ([TagsId]) REFERENCES [Tags] ([TagId]) ON DELETE CASCADE
);
```

内部的には、EF は、結合エンティティ型と呼ばれる結合テーブルを表すエンティティ型を作成します。 このに使用できる特定の CLR 型がないため、 `Dictionary<string, object>` が使用されます。 複数の多対多リレーションシップがモデルに存在する可能性があるため、結合エンティティ型には一意の名前を指定する必要があります (この場合は) `PostTag` 。 これを可能にする機能は、共有型のエンティティ型と呼ばれます。

多対多のナビゲーションは、結合エンティティ型を効果的にスキップするため、"スキップナビゲーション" と呼ばれます。 一括構成を使用している場合は、すべてのスキップナビゲーションをから取得でき `GetSkipNavigations` ます。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ManyToManyShared.cs?name=Metadata)]

結合エンティティ型に構成を適用するのは一般的です。 この操作は、を使用して実行でき `UsingEntity` ます。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ManyToManyShared.cs?name=SharedConfiguration)]

[モデルシードデータ](xref:core/modeling/data-seeding) は、匿名型を使用して、結合エンティティ型に指定できます。 モデルデバッグビューを調べて、規則によって作成されたプロパティ名を確認できます。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ManyToManyShared.cs?name=Seeding)]

結合エンティティ型には追加のデータを格納できますが、そのためにはオーダーメイド CLR 型を作成することをお勧めします。 カスタム結合エンティティ型を使用してリレーションシップを構成する場合は、両方の外部キーを明示的に指定する必要があります。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ManyToManyPayload.cs?name=ManyToManyPayload)]

> [!NOTE]
> EF Core 5.0 では、多対多リレーションシップを構成する機能が追加されました。以前のバージョンでは、次の方法を使用します。

結合エンティティ型を追加し、2つの個別の一対多リレーションシップをマッピングするだけで、多対多リレーションシップを表すこともできます。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/Relationships/ManyToMany.cs?name=ManyToMany&highlight=11-14,16-19,39-46)]

> [!NOTE]
> データベースからの多対多リレーションシップのスキャフォールディングのサポートはまだ追加されていません。 「 [問題の追跡](https://github.com/dotnet/efcore/issues/22475)」を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [EF Core コミュニティのスタンドアップセッション](https://www.youtube.com/watch?v=W1sxepfIMRM&list=PLdo4fOcmZ0oX-DBuRG4u58ZTAJgBAeQ-t&index=32)。多対多とインフラストラクチャの中核的土台を深く掘り下げています。
