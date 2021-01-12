---
title: 追跡対象のエンティティへのアクセス-EF Core
description: EntityEntry、DbContext. Entries、および Dbcontext. Local を使用した、追跡されたエンティティへのアクセス
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/entity-entries
ms.openlocfilehash: f385016aba61535f33e34c622dd43ce6dc823fc5
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129613"
---
# <a name="accessing-tracked-entities"></a>追跡対象のエンティティへのアクセス

によって追跡されるエンティティにアクセスするには、次の4つの主要な Api があり <xref:Microsoft.EntityFrameworkCore.DbContext> ます。

- <xref:System.Data.Entity.DbContext.Entry%2A?displayProperty=nameWithType><xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601>指定されたエンティティインスタンスのインスタンスを返します。
- <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries%2A?displayProperty=nameWithType><xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601>すべての追跡対象エンティティ、または特定の型のすべての追跡対象エンティティのインスタンスを返します。
- <xref:Microsoft.EntityFrameworkCore.DbContext.Find%2A?displayProperty=nameWithType>、、、およびでは、 <xref:Microsoft.EntityFrameworkCore.DbContext.FindAsync%2A?displayProperty=nameWithType> <xref:Microsoft.EntityFrameworkCore.DbSet%601.Find%2A?displayProperty=nameWithType> <xref:Microsoft.EntityFrameworkCore.DbSet%601.FindAsync%2A?displayProperty=nameWithType> 主キーによって1つのエンティティが検索されます。まず、追跡対象のエンティティを探し、必要に応じてデータベースに対してクエリを実行します。
- <xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> DbSet によって表されるエンティティ型のエンティティの実際のエンティティ (EntityEntry インスタンスではない) を返します。

これらの各トピックについては、以下のセクションで詳しく説明します。

> [!TIP]
> このドキュメントでは、EF Core の変更の追跡のエンティティの状態と基本について理解していることを前提としています。 これらのトピックの詳細については、 [EF Core の Change Tracking](xref:core/change-tracking/index) を参照してください。

> [!TIP]
> [GitHub からサンプルコードをダウンロード](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/AccessingTrackedEntities)することで、このドキュメントのすべてのコードを実行し、デバッグすることができます。

## <a name="using-dbcontextentry-and-entityentry-instances"></a>DbContext および EntityEntry インスタンスの使用

追跡対象の各エンティティについて、Entity Framework Core (EF Core) は次のことを追跡します。

- エンティティの全体的な状態。 これは、、、、またはのいずれかです。 `Unchanged` `Modified` 詳細については、EF Core の `Added` Change Tracking を参照し `Deleted` てください。 [](xref:core/change-tracking/index)
- 追跡対象のエンティティ間のリレーションシップ。 たとえば、投稿が属しているブログです。
- プロパティの "現在の値"。
- プロパティの "元の値"。この情報を取得できます。 元の値は、エンティティがデータベースから照会されたときに存在していたプロパティ値です。
- クエリ後にどのプロパティ値が変更されたか。
- 値が [一時的](xref:core/change-tracking/miscellaneous#temporary-values)であるかどうかなど、プロパティ値に関するその他の情報。

エンティティインスタンスをに渡すと <xref:System.Data.Entity.DbContext.Entry%2A?displayProperty=nameWithType> 、によっ <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> て、指定されたエンティティのこの情報にアクセスできるようになります。 例:

<!--
        using var context = new BlogsContext();

        var blog = context.Blogs.Single(e => e.Id == 1);
        var entityEntry = context.Entry(blog);

-->
[!code-csharp[Using_DbContext_Entry_and_EntityEntry_instances_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_DbContext_Entry_and_EntityEntry_instances_1)]

次のセクションでは、EntityEntry を使用してエンティティの状態にアクセスして操作する方法と、エンティティのプロパティとナビゲーションの状態について説明します。

### <a name="working-with-the-entity"></a>エンティティの操作

の最も一般的な使用方法 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> は、エンティティの現在のにアクセスすることです <xref:Microsoft.EntityFrameworkCore.EntityState> 。 例:

<!--
        var currentState = context.Entry(blog).State;
        if (currentState == EntityState.Unchanged)
        {
            context.Entry(blog).State = EntityState.Modified;
        }
-->
[!code-csharp[Work_with_the_entity_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_the_entity_1)]

Entry メソッドは、まだ追跡されていないエンティティに対しても使用できます。 これ _は、エンティティの追跡を開始しません_。エンティティの状態は依然と `Detatched` してです。 ただし、返された EntityEntry を使用してエンティティの状態を変更することができます。その時点で、エンティティは特定の状態で追跡されます。 たとえば、次のコードでは、ブログインスタンスの追跡が開始され `Added` ます。

<!--
        var newBlog = new Blog();
        Debug.Assert(context.Entry(newBlog).State == EntityState.Detached);

        context.Entry(newBlog).State = EntityState.Added;
        Debug.Assert(context.Entry(newBlog).State == EntityState.Added);
-->
[!code-csharp[Work_with_the_entity_2](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_the_entity_2)]

> [!TIP]
> EF6 とは異なり、個々のエンティティの状態を設定しても、接続されているすべてのエンティティが追跡されるわけではありません。 これにより `Add` 、 `Attach` `Update` エンティティのグラフ全体に作用する、、またはを呼び出すよりも低いレベルの操作に状態が設定されます。

次の表は、EntityEntry を使用してエンティティ全体を操作する方法をまとめたものです。

| EntityEntry メンバー                                                                                         | 説明
|:-----------------------------------------------------------------------------------------------------------|----------------------
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.State?displayProperty=nameWithType>         | エンティティのを取得または設定し <xref:Microsoft.EntityFrameworkCore.EntityState> ます。
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Entity?displayProperty=nameWithType>        | エンティティインスタンスを取得します。
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Context?displayProperty=nameWithType>       | <xref:Microsoft.EntityFrameworkCore.DbContext>このエンティティを追跡している。
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Metadata?displayProperty=nameWithType>      | <xref:Microsoft.EntityFrameworkCore.Metadata.IEntityType> エンティティの型のメタデータ。
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.IsKeySet?displayProperty=nameWithType>      | エンティティのキー値が設定されているかどうか。
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Reload?displayProperty=nameWithType>        | データベースから読み取った値を使用してプロパティ値を上書きします。
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.DetectChanges?displayProperty=nameWithType> | このエンティティの変更のみを強制的に検出します。「 [変更の検出と通知」を](xref:core/change-tracking/change-detection)参照してください。

### <a name="working-with-a-single-property"></a>1つのプロパティの操作

のいくつかのオーバーロードでは <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Property%2A?displayProperty=nameWithType> 、エンティティの個々のプロパティに関する情報へのアクセスが許可されます。 たとえば、厳密に型指定された、fluent に似た API を使用すると、次のようになります。

<!--
            PropertyEntry<Blog, string> propertyEntry = context.Entry(blog).Property(e => e.Name);
-->
[!code-csharp[Work_with_a_single_property_1a](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_property_1a)]

代わりに、プロパティ名を文字列として渡すことができます。 例:

<!--
            PropertyEntry<Blog, string> propertyEntry = context.Entry(blog).Property<string>("Name");
-->
[!code-csharp[Work_with_a_single_property_1b](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_property_1b)]

返されたを <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry%602> 使用して、プロパティに関する情報にアクセスできます。 たとえば、このエンティティのプロパティの現在の値を取得して設定するために使用できます。

<!--
            string currentValue = context.Entry(blog).Property(e => e.Name).CurrentValue;
            context.Entry(blog).Property(e => e.Name).CurrentValue = "1unicorn2";
-->
[!code-csharp[Work_with_a_single_property_1d](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_property_1d)]

上記のいずれのプロパティメソッドも、厳密に型指定されたジェネリックインスタンスを返し <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry%602> ます。 このジェネリック型を使用することをお勧めします。これは、 [値型をボックス](/dotnet/csharp/programming-guide/types/boxing-and-unboxing)化せずにプロパティ値にアクセスできるようにするためです。 ただし、エンティティまたはプロパティの型がコンパイル時にわからない場合は、代わりに非ジェネリックを <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry> 取得できます。

<!--
            var propertyEntry = context.Entry(blog).Property("Name");
-->
[!code-csharp[Work_with_a_single_property_1c](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_property_1c)]

これにより、ボックス化された値の型を犠牲にして、型に関係なく、任意のプロパティのプロパティ情報にアクセスできるようになります。 例:

<!--
            object blog = context.Blogs.Single(e => e.Id == 1);

            object currentValue = context.Entry(blog).Property("Name").CurrentValue;
            context.Entry(blog).Property("Name").CurrentValue = "1unicorn2";
-->
[!code-csharp[Work_with_a_single_property_1e](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_property_1e)]

次の表は、PropertyEntry によって公開されるプロパティ情報をまとめたものです。

| PropertyEntry メンバー                               | 説明
|:-------------------------------------------------|----------------------
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry%602.CurrentValue?displayProperty=nameWithType>  | プロパティの現在の値を取得および設定します。
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry%602.OriginalValue?displayProperty=nameWithType> | プロパティの元の値を取得または設定します (使用可能な場合)。
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry%602.EntityEntry?displayProperty=nameWithType>   | エンティティのへの後方参照 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> 。
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.Metadata?displayProperty=nameWithType>          | <xref:Microsoft.EntityFrameworkCore.Metadata.IProperty> プロパティのメタデータ。
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsModified?displayProperty=nameWithType>        | このプロパティが変更済みとしてマークされているかどうかを示し、この状態を変更できるようにします。
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsTemporary?displayProperty=nameWithType>       | このプロパティが [一時](xref:core/change-tracking/miscellaneous#temporary-values#temporary-values)としてマークされているかどうかを示し、この状態を変更できるようにします。

注:

- プロパティの元の値は、エンティティがデータベースから照会されたときのプロパティの値です。 ただし、またはなど、エンティティが切断され、別の DbContext に明示的にアタッチされている場合、元の値は使用できません `Attach` `Update` 。 この場合、返される元の値は、現在の値と同じになります。
- <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> 変更済みとしてマークされているプロパティのみを更新します。 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsModified>指定したプロパティ値を EF Core に強制的に更新する場合は true に設定し、EF Core によってプロパティ値が更新されないようにする場合は false に設定します。
- [一時的な値](xref:core/change-tracking/miscellaneous) は、通常 EF Core [値ジェネレーター](xref:core/modeling/generated-properties)によって生成されます。 プロパティの現在の値を設定すると、一時的な値が指定された値に置き換えられ、プロパティが一時的ではないとマークされます。 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry.IsTemporary>明示的に設定された後でも強制的に値を一時的にする場合は true に設定します。

### <a name="working-with-a-single-navigation"></a>単一のナビゲーションの操作

、、およびのいくつかのオーバーロードは <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Reference%2A?displayProperty=nameWithType> 、 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Collection%2A?displayProperty=nameWithType> 個々の <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Navigation%2A?displayProperty=nameWithType> ナビゲーションに関する情報へのアクセスを許可します。

1つの関連エンティティへの参照ナビゲーションには、メソッドを使用してアクセス <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Reference%2A> します。 参照ナビゲーションは、一対多のリレーションシップの "一" の側、および一対一のリレーションシップの両側を指します。 例:

<!--
        ReferenceEntry<Post, Blog> referenceEntry1 = context.Entry(post).Reference(e => e.Blog);
        ReferenceEntry<Post, Blog> referenceEntry2 = context.Entry(post).Reference<Blog>("Blog");
        ReferenceEntry referenceEntry3 = context.Entry(post).Reference("Blog");
-->
[!code-csharp[Work_with_a_single_navigation_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_navigation_1)]

ナビゲーションは、一対多および多対多のリレーションシップの "多" の側で使用される場合に、関連エンティティのコレクションにすることもできます。 メソッドは、 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601.Collection%2A> コレクションのナビゲーションにアクセスするために使用されます。 例:

<!--
        CollectionEntry<Blog, Post> collectionEntry1 = context.Entry(blog).Collection(e => e.Posts);
        CollectionEntry<Blog, Post> collectionEntry2 = context.Entry(blog).Collection<Post>("Posts");
        CollectionEntry collectionEntry3 = context.Entry(blog).Collection("Posts");
-->
[!code-csharp[Work_with_a_single_navigation_2a](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_navigation_2a)]

一部の操作は、すべてのナビゲーションで共通です。 これらのアクセスには、メソッドを使用して参照とコレクションの両方のナビゲーションを行うことができ <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Navigation%2A?displayProperty=nameWithType> ます。 すべてのナビゲーションにアクセスするときに使用できるのは非ジェネリックアクセスのみであることに注意してください。 例:

<!--
        NavigationEntry navigationEntry = context.Entry(blog).Navigation("Posts");
-->
[!code-csharp[Work_with_a_single_navigation_2b](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_a_single_navigation_2b)]

次の表は、、、およびの使用方法をまとめたもの <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ReferenceEntry%602> <xref:Microsoft.EntityFrameworkCore.ChangeTracking.CollectionEntry%602> <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry> です。

| NavigationEntry メンバー                                                                                    | 説明
|:----------------------------------------------------------------------------------------------------------|----------------------
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.MemberEntry.CurrentValue?displayProperty=nameWithType> | ナビゲーションの現在の値を取得および設定します。 これはコレクションナビゲーションのコレクション全体です。
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry.Metadata?displayProperty=nameWithType> | <xref:Microsoft.EntityFrameworkCore.Metadata.INavigationBase> ナビゲーションのメタデータ。
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry.IsLoaded?displayProperty=nameWithType> | 関連するエンティティまたはコレクションがデータベースから完全に読み込まれているかどうかを示す値を取得または設定します。
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry.Load?displayProperty=nameWithType>     | データベースから関連エンティティまたはコレクションを読み込みます。「 [関連データの明示的な読み込み」を](xref:core/querying/related-data/explicit)参照してください。
| <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry.Query?displayProperty=nameWithType>    | クエリ EF Core は、を使用して、さらに構成できるとしてこのナビゲーションを読み込みます `IQueryable` 。「 [関連データの明示的な読み込み](xref:core/querying/related-data/explicit)」を参照してください。

### <a name="working-with-all-properties-of-an-entity"></a>エンティティのすべてのプロパティを操作する

<xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Properties?displayProperty=nameWithType><xref:System.Collections.Generic.IEnumerable%601> <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyEntry> エンティティのすべてのプロパティについて、のを返します。 これは、エンティティのすべてのプロパティに対してアクションを実行するために使用できます。 たとえば、任意の DateTime プロパティをに設定するには、次のようにし `DateTime.Now` ます。

<!--
        foreach (var propertyEntry in context.Entry(blog).Properties)
        {
            if (propertyEntry.Metadata.ClrType == typeof(DateTime))
            {
                propertyEntry.CurrentValue = DateTime.Now;
            }
        }
-->
[!code-csharp[Work_with_all_properties_of_an_entity_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_properties_of_an_entity_1)]

さらに、EntityEntry には、すべてのプロパティ値を同時に取得および設定するためのメソッドがいくつか含まれています。 これらのメソッドは、 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyValues> プロパティとその値のコレクションを表すクラスを使用します。 PropertyValues は、現在または元の値、または現在データベースに格納されている値に対して取得できます。 例:

<!--
        var currentValues = context.Entry(blog).CurrentValues;
        var originalValues = context.Entry(blog).OriginalValues;
        var databaseValues = context.Entry(blog).GetDatabaseValues();
-->
[!code-csharp[Work_with_all_properties_of_an_entity_2a](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_properties_of_an_entity_2a)]

これらの PropertyValues オブジェクトは、独自にはあまり役に立ちません。 ただし、エンティティを操作するときに必要な一般的な操作を実行するために組み合わせることができます。 これは、データ転送オブジェクトを操作する場合や、 [オプティミスティック同時実行の競合](xref:core/saving/concurrency)を解決する場合に便利です。 次のセクションでは、いくつかの例を示します。

#### <a name="setting-current-or-original-values-from-an-entity-or-dto"></a>エンティティまたは DTO からの現在または元の値の設定

エンティティの現在の値または元の値は、別のオブジェクトから値をコピーすることによって更新できます。 たとえば、 `BlogDto` エンティティ型と同じプロパティを持つデータ転送オブジェクト (DTO) について考えてみます。

<!--
public class BlogDto
{
    public int Id { get; set; }
    public string Name { get; set; }
}
-->
[!code-csharp[BlogDto](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=BlogDto)]

これは、を使用して、追跡対象エンティティの現在の値を設定するために使用でき <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyValues.SetValues%2A?displayProperty=nameWithType> ます。

<!--
        var blogDto = new BlogDto { Id = 1, Name = "1unicorn2" };

        context.Entry(blog).CurrentValues.SetValues(blogDto);
-->
[!code-csharp[Work_with_all_properties_of_an_entity_2b](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_properties_of_an_entity_2b)]

この手法は、サービス呼び出しまたは n 層アプリケーションのクライアントから取得した値を使用してエンティティを更新するときに使用されることがあります。 使用されるオブジェクトは、エンティティと同じ型である必要はありません。ただし、エンティティの名前と名前が一致するプロパティがある場合に限ります。 上の例では、DTO のインスタンスを使用して、 `BlogDto` 追跡対象のエンティティの現在の値を設定してい `Blog` ます。

プロパティは、現在の値と異なる値が設定されている場合にのみ、変更済みとしてマークされることに注意してください。

#### <a name="setting-current-or-original-values-from-a-dictionary"></a>ディクショナリからの現在または元の値の設定

前の例では、エンティティまたは DTO インスタンスから値を設定しています。 プロパティ値がディクショナリ内の名前と値のペアとして格納されている場合も、同じ動作が使用できます。 例:

<!--
        var blogDictionary = new Dictionary<string, object>
        {
            ["Id"] = 1,
            ["Name"] = "1unicorn2"
        };

        context.Entry(blog).CurrentValues.SetValues(blogDictionary);
-->
[!code-csharp[Work_with_all_properties_of_an_entity_2d](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_properties_of_an_entity_2d)]

#### <a name="setting-current-or-original-values-from-the-database"></a>データベースからの現在の値または元の値の設定

またはを呼び出し、返されたオブジェクトを使用して現在の値または元の <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.GetDatabaseValues> <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.GetDatabaseValuesAsync%2A> 値を設定することにより、エンティティの現在の値または元の値をデータベースの最新の値で更新できます。 例:

<!--
        var databaseValues = context.Entry(blog).GetDatabaseValues();
        context.Entry(blog).CurrentValues.SetValues(databaseValues);
        context.Entry(blog).OriginalValues.SetValues(databaseValues);
-->
[!code-csharp[Work_with_all_properties_of_an_entity_2c](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_properties_of_an_entity_2c)]

#### <a name="creating-a-cloned-object-containing-current-original-or-database-values"></a>現在、元、またはデータベースの値を含む複製されたオブジェクトの作成

CurrentValues、OriginalValues、または GetDatabaseValues から返された PropertyValues オブジェクトを使用すると、を使用してエンティティの複製を作成でき <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyValues.ToObject?displayProperty=nameWithType> ます。 例:

<!--
var clonedBlog = context.Entry(blog).GetDatabaseValues().ToObject();
-->
[!code-csharp[Work_with_all_properties_of_an_entity_2e](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_properties_of_an_entity_2e)]

は、 `ToObject` DbContext によって追跡されない新しいインスタンスを返すことに注意してください。 返されたオブジェクトには、他のエンティティに設定されたリレーションシップもありません。

複製されたオブジェクトは、特に特定の型のオブジェクトにデータをバインドする場合に、データベースに対する同時更新に関連する問題を解決するのに役立ちます。 詳細については、「 [オプティミスティック同時実行制御](xref:core/saving/concurrency) 」を参照してください。

### <a name="working-with-all-navigations-of-an-entity"></a>エンティティのすべてのナビゲーションを操作する

<xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Navigations?displayProperty=nameWithType><xref:System.Collections.Generic.IEnumerable%601> <xref:Microsoft.EntityFrameworkCore.ChangeTracking.NavigationEntry> エンティティのすべてのナビゲーションについて、のを返します。 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.References?displayProperty=nameWithType> とは同じことを <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Collections?displayProperty=nameWithType> 行いますが、それぞれ参照またはコレクションのナビゲーションに限定されています。 これは、エンティティのすべてのナビゲーションに対してアクションを実行するために使用できます。 たとえば、すべての関連エンティティの読み込みを強制的に実行するには、次のようにします。

<!--
        foreach (var navigationEntry in context.Entry(blog).Navigations)
        {
            navigationEntry.Load();
        }
-->
[!code-csharp[Work_with_all_navigations_of_an_entity_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_navigations_of_an_entity_1)]

### <a name="working-with-all-members-of-an-entity"></a>エンティティのすべてのメンバーの操作

標準プロパティとナビゲーションプロパティの状態と動作は異なります。 そのため、上のセクションで説明したように、ナビゲーションと非ナビゲーションを個別に処理することが一般的です。 ただし、通常のプロパティとナビゲーションのどちらであるかに関係なく、エンティティのメンバーに対して何らかの処理を実行すると便利な場合があります。 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Member%2A?displayProperty=nameWithType> および <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Members?displayProperty=nameWithType> は、この目的のために用意されています。 例:

<!--
        foreach (var memberEntry in context.Entry(blog).Members)
        {
            Console.WriteLine(
                $"Member {memberEntry.Metadata.Name} is of type {memberEntry.Metadata.ClrType.ShortDisplayName()} and has value {memberEntry.CurrentValue}");
        }
-->
[!code-csharp[Work_with_all_members_of_an_entity_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Work_with_all_members_of_an_entity_1)]

このサンプルのブログでこのコードを実行すると、次の出力が生成されます。

```output
Member Id is of type int and has value 1
Member Name is of type string and has value .NET Blog
Member Posts is of type IList<Post> and has value System.Collections.Generic.List`1[Post]
```

> [!TIP]
> [ [トラッカーの変更] デバッグビュー](xref:core/change-tracking/debug-views) には、次のような情報が表示されます。 変更トラッカー全体の [デバッグ] ビューは、 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.DebugView?displayProperty=nameWithType> 追跡対象の各エンティティの個々のから生成されます。

## <a name="find-and-findasync"></a>Find と FindAsync

<xref:Microsoft.EntityFrameworkCore.DbContext.Find%2A?displayProperty=nameWithType>、 <xref:Microsoft.EntityFrameworkCore.DbContext.FindAsync%2A?displayProperty=nameWithType> 、 <xref:Microsoft.EntityFrameworkCore.DbSet%601.Find%2A?displayProperty=nameWithType> 、および <xref:Microsoft.EntityFrameworkCore.DbSet%601.FindAsync%2A?displayProperty=nameWithType> は、主キーがわかっている場合に、1つのエンティティを効率的に検索できるように設計されています。 最初にエンティティが追跡されているかどうかを検索し、存在する場合はそのエンティティを直ちに返します。 データベースクエリが行われるのは、エンティティがローカルで追跡されていない場合のみです。 たとえば、同じエンティティに対して Find を2回呼び出すコードを考えてみます。

<!--
        using var context = new BlogsContext();

        Console.WriteLine("First call to Find...");
        var blog1 = context.Blogs.Find(1);

        Console.WriteLine($"...found blog {blog1.Name}");

        Console.WriteLine();
        Console.WriteLine("Second call to Find...");
        var blog2 = context.Blogs.Find(1);
        Debug.Assert(blog1 == blog2);

        Console.WriteLine("...returned the same instance without executing a query.");
-->
[!code-csharp[Find_and_FindAsync_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Find_and_FindAsync_1)]

SQLite を使用した場合のこのコード (EF Core のログ記録を含む) の出力は次のとおりです。

```output
First call to Find...
info: 12/29/2020 07:45:53.682 RelationalEventId.CommandExecuted[20101] (Microsoft.EntityFrameworkCore.Database.Command)
      Executed DbCommand (1ms) [Parameters=[@__p_0='1' (DbType = String)], CommandType='Text', CommandTimeout='30']
      SELECT "b"."Id", "b"."Name"
      FROM "Blogs" AS "b"
      WHERE "b"."Id" = @__p_0
      LIMIT 1
...found blog .NET Blog

Second call to Find...
...returned the same instance without executing a query.
```

最初の呼び出しではエンティティがローカルに見つからないことに注意してください。そのため、データベースクエリを実行します。 逆に、2回目の呼び出しは、既に追跡されているため、データベースに対してクエリを実行せずに同じインスタンスを返します。

指定されたキーを持つエンティティがローカルで追跡されず、データベースに存在しない場合、Find は null を返します。

### <a name="composite-keys"></a>複合キー

Find は、複合キーと共に使用することもできます。 たとえば、 `OrderLine` 注文 ID と製品 ID で構成される複合キーを持つエンティティについて考えてみます。

<!--
public class OrderLine
{
    public int OrderId { get; set; }
    public int ProductId { get; set; }

    //...
}
-->
[!code-csharp[OrderLine](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=OrderLine)]

<xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating%2A?displayProperty=nameWithType>キー部分 _とその順序_ を定義するには、複合キーをで構成する必要があります。 例:

<!--
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder
            .Entity<OrderLine>()
            .HasKey(e => new { e.OrderId, e.ProductId });
    }
-->
[!code-csharp[OnModelCreating](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=OnModelCreating)]

`OrderId`はキーの最初の部分であり、 `ProductId` キーの2番目の部分であることに注意してください。 検索するキー値を渡すときには、この順序を使用する必要があります。 例:

<!--
        var orderline = context.OrderLines.Find(orderId, productId);
-->
[!code-csharp[Find_and_FindAsync_2](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Find_and_FindAsync_2)]

## <a name="using-changetrackerentries-to-access-all-tracked-entities"></a>ChangeTracker を使用して、すべての追跡対象エンティティにアクセスする

これまでは、一度に1つのにしかアクセス <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry> できませんでした。 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries?displayProperty=nameWithType> DbContext によって現在追跡されているすべてのエンティティの EntityEntry を返します。 例:

<!--
        using var context = new BlogsContext();
        var blogs = context.Blogs.Include(e => e.Posts).ToList();

        foreach (var entityEntry in context.ChangeTracker.Entries())
        {
            Console.WriteLine($"Found {entityEntry.Metadata.Name} entity with ID {entityEntry.Property("Id").CurrentValue}");
        }
-->
[!code-csharp[Using_ChangeTracker_Entries_to_access_all_tracked_entities_1a](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_ChangeTracker_Entries_to_access_all_tracked_entities_1a)]

このコードは、次の出力を生成します。

```output
Found Blog entity with ID 1
Found Post entity with ID 1
Found Post entity with ID 2
```

ブログと投稿の両方のエントリが返されることに注意してください。 代わりに、ジェネリックオーバーロードを使用して、結果を特定のエンティティ型にフィルター処理でき <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries%60%601?displayProperty=nameWithType> ます。

<!--
        foreach (var entityEntry in context.ChangeTracker.Entries<Post>())
        {
            Console.WriteLine(
                $"Found {entityEntry.Metadata.Name} entity with ID {entityEntry.Property(e => e.Id).CurrentValue}");
        }
-->
[!code-csharp[Using_ChangeTracker_Entries_to_access_all_tracked_entities_1b](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_ChangeTracker_Entries_to_access_all_tracked_entities_1b)]

このコードからの出力は、投稿だけが返されることを示しています。

```output
Found Post entity with ID 1
Found Post entity with ID 2
```

また、ジェネリックオーバーロードを使用すると、ジェネリックインスタンスが返さ <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry%601> れます。 これにより、この例のプロパティへの fluent 形式のアクセスが可能に `Id` なります。

フィルター処理に使用されるジェネリック型は、マップされたエンティティ型である必要はありません。代わりに、マップされていない基本型またはインターフェイスを使用できます。 たとえば、モデル内のすべてのエンティティ型で、キープロパティを定義するインターフェイスが実装されているとします。

<!--
public interface IEntityWithKey
{
    int Id { get; set; }
}
-->
[!code-csharp[IEntityWithKey](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=IEntityWithKey)]

その後、このインターフェイスを使用して、厳密に型指定された方法で、任意の追跡対象エンティティのキーを操作できます。 例:

<!--
        foreach (var entityEntry in context.ChangeTracker.Entries<IEntityWithKey>())
        {
            Console.WriteLine(
                $"Found {entityEntry.Metadata.Name} entity with ID {entityEntry.Property(e => e.Id).CurrentValue}");
        }
-->
[!code-csharp[Using_ChangeTracker_Entries_to_access_all_tracked_entities_1c](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_ChangeTracker_Entries_to_access_all_tracked_entities_1c)]

## <a name="using-dbsetlocal-to-query-tracked-entities"></a>DbSet. ローカルを使用した追跡対象エンティティの照会

EF Core のクエリは常にデータベースで実行され、データベースに保存されているエンティティのみが返されます。 <xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> ローカルの追跡対象エンティティの DbContext に対してクエリを実行するための機構を提供します。

`DbSet.Local`は、追跡されたエンティティにクエリを実行するために使用されるため、通常は、エンティティを DbContext に読み込み、読み込まれたエンティティを操作します。 これはデータバインディングに特に当てはまりますが、他の状況でも役立つことがあります。 たとえば、次のコードでは、最初にすべてのブログと投稿に対してデータベースにクエリを行います。 <xref:Microsoft.EntityFrameworkCore.EntityFrameworkQueryableExtensions.Load%2A>拡張メソッドは、アプリケーションに直接返されることなく、コンテキストによって追跡された結果を使用して、このクエリを実行するために使用されます。 (または類似点を使用する `ToList` と同じ効果がありますが、ここでは必要のない、返されるリストを作成するオーバーヘッドがあります)。次に、を使用して、 `DbSet.Local` ローカルで追跡されているエンティティにアクセスします。

<!--
        using var context = new BlogsContext();

        context.Blogs.Include(e => e.Posts).Load();

        foreach (var blog in context.Blogs.Local)
        {
            Console.WriteLine($"Blog: {blog.Name}");
        }

        foreach (var post in context.Posts.Local)
        {
            Console.WriteLine($"Post: {post.Title}");
        }
-->
[!code-csharp[Using_DbSet_Local_to_query_tracked_entities_1](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_DbSet_Local_to_query_tracked_entities_1)]

とは異なり、 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.Entries?displayProperty=nameWithType> は `DbSet.Local` エンティティのインスタンスを直接返します。 もちろん、を呼び出して、返されたエンティティに対して EntityEntry を常に取得でき <xref:System.Data.Entity.DbContext.Entry%2A?displayProperty=nameWithType> ます。

### <a name="the-local-view"></a>ローカルビュー

<xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> ローカルで追跡されているエンティティのうち、現在のエンティティを反映するビューを返し <xref:Microsoft.EntityFrameworkCore.EntityState> ます。 具体的には、次のことを意味します。

- `Added` エンティティが含まれます。 `Added`エンティティはデータベースにまだ存在していないため、データベースクエリによって返されることはないため、通常の EF Core クエリではこれは当てはまりません。
- `Deleted` エンティティは除外されます。 `Deleted`エンティティはデータベースにまだ存在して _いるため、_ データベースクエリによって返されるので、通常の EF Core クエリの場合は、このようになります。

これは、エンティティ `DbSet.Local` グラフの現在の概念状態を反映するデータを表示することを意味し `Added` ます。エンティティとエンティティは除外され `Deleted` ます。 これは、SaveChanges が呼び出された後に、どのデータベースの状態が予想されるかと一致します。

これは、通常、データバインディングに最適なビューです。これは、アプリケーションによって行われた変更に基づいてデータを理解する際にユーザーに表示されるためです。

次のコードは、1つの投稿をとしてマークした後、 `Deleted` 新しい投稿を追加し、としてマークする方法を示してい `Added` ます。

<!--
        using var context = new BlogsContext();

        var posts = context.Posts.Include(e => e.Blog).ToList();

        Console.WriteLine("Local view after loading posts:");

        foreach (var post in context.Posts.Local)
        {
            Console.WriteLine($"  Post: {post.Title}");
        }

        context.Remove(posts[1]);

        context.Add(new Post
        {
            Title = "What’s next for System.Text.Json?",
            Content = ".NET 5.0 was released recently and has come with many...",
            Blog = posts[0].Blog
        });

        Console.WriteLine("Local view after adding and deleting posts:");

        foreach (var post in context.Posts.Local)
        {
            Console.WriteLine($"  Post: {post.Title}");
        }
-->
[!code-csharp[Using_DbSet_Local_to_query_tracked_entities_2](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_DbSet_Local_to_query_tracked_entities_2)]

このコードからの出力は次のとおりです。

```output
Local view after loading posts:
  Post: Announcing the Release of EF Core 5.0
  Post: Announcing F# 5
  Post: Announcing .NET 5.0
Local view after adding and deleting posts:
  Post: What’s next for System.Text.Json?
  Post: Announcing the Release of EF Core 5.0
  Post: Announcing .NET 5.0
```

削除された投稿がローカルビューから削除され、追加された投稿が含まれていることに注意してください。

### <a name="using-local-to-add-and-remove-entities"></a>ローカルを使用したエンティティの追加と削除

<xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> によって <xref:Microsoft.EntityFrameworkCore.ChangeTracking.LocalView%601> のインスタンスが返されます。 これは、 <xref:System.Collections.Generic.ICollection%601> エンティティがコレクションから追加および削除されたときに、通知を生成して応答するの実装です。 (これはと同じ概念です <xref:System.Collections.ObjectModel.ObservableCollection%601> が、独立したコレクションとしてではなく、既存の EF Core 変更追跡エントリに対する射影として実装されます)。

ローカルビューの通知は、dbcontext の変更の追跡にフックされます。これにより、ローカルビューは DbContext と同期したままになります。 具体的な内容は次のとおりです。

- 新しいエンティティを追加する `DbSet.Local` と、DbContext (通常は状態) によって追跡され `Added` ます。 (エンティティに既に生成されたキー値がある場合は、代わりにとして追跡され `Unchanged` ます)。
- からエンティティを削除 `DbSet.Local` すると、そのエンティティはとマークさ `Deleted` れます。
- DbContext によって追跡されるエンティティは、自動的にコレクションに表示され `DbSet.Local` ます。 たとえば、より多くのエンティティを取り込むためにクエリを実行すると、ローカルビューが自動的に更新されます。
- としてマークされているエンティティ `Deleted` は、自動的にローカルコレクションから削除されます。

これは、コレクションに対してを追加および削除するだけで、追跡されたエンティティを操作するためにローカルビューを使用できることを意味します。 たとえば、では、前の例のコードを変更して、ローカルコレクションから投稿を追加および削除できます。

<!--
        using var context = new BlogsContext();

        var posts = context.Posts.Include(e => e.Blog).ToList();

        Console.WriteLine("Local view after loading posts:");

        foreach (var post in context.Posts.Local)
        {
            Console.WriteLine($"  Post: {post.Title}");
        }

        context.Posts.Local.Remove(posts[1]);

        context.Posts.Local.Add(new Post
        {
            Title = "What’s next for System.Text.Json?",
            Content = ".NET 5.0 was released recently and has come with many...",
            Blog = posts[0].Blog
        });

        Console.WriteLine("Local view after adding and deleting posts:");

        foreach (var post in context.Posts.Local)
        {
            Console.WriteLine($"  Post: {post.Title}");
        }
-->
[!code-csharp[Using_DbSet_Local_to_query_tracked_entities_3](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_DbSet_Local_to_query_tracked_entities_3)]

ローカルビューに加えられた変更は DbContext と同期されるため、出力は前の例から変更されていません。

### <a name="using-the-local-view-for-windows-forms-or-wpf-data-binding"></a>Windows フォームまたは WPF データバインディングのためのローカルビューの使用

<xref:Microsoft.EntityFrameworkCore.DbSet%601.Local?displayProperty=nameWithType> EF Core エンティティへのデータバインディングの基礎を形成します。 ただし、Windows フォームと WPF は、想定される特定の種類の通知コレクションと共に使用すると最適に動作します。 ローカルビューでは、次の特定のコレクション型の作成がサポートされています。

- <xref:Microsoft.EntityFrameworkCore.ChangeTracking.LocalView%601.ToObservableCollection?displayProperty=nameWithType><xref:System.Collections.ObjectModel.ObservableCollection%601>WPF データバインディングのを返します。
- <xref:Microsoft.EntityFrameworkCore.ChangeTracking.LocalView%601.ToBindingList?displayProperty=nameWithType><xref:System.ComponentModel.BindingList%601>Windows フォームデータバインディングのを返します。

例:

<!--
        ObservableCollection<Post> observableCollection = context.Posts.Local.ToObservableCollection();
        BindingList<Post> bindingList = context.Posts.Local.ToBindingList();
-->
[!code-csharp[Using_DbSet_Local_to_query_tracked_entities_4](../../../samples/core/ChangeTracking/AccessingTrackedEntities/Samples.cs?name=Using_DbSet_Local_to_query_tracked_entities_4)]

EF Core による WPF のデータバインディングの詳細については、「 [wpf の概要](xref:core/get-started/wpf) 」を参照してください。

> [!TIP]
> 特定の DbSet インスタンスのローカルビューは、最初にアクセスされてからキャッシュされるときに、遅延して作成されます。 LocalView の作成自体は高速で、大量のメモリは使用されません。 ただし、検出された [変更](xref:core/change-tracking/change-detection)は、大量のエンティティの処理速度が低下する可能性があります。 およびによって作成されたコレクション `ToObservableCollection` `ToBindingList` も、遅延してキャッシュされます。 これらの方法では、新しいコレクションが作成されます。これにより、何千ものエンティティが関係しているときに、処理速度が遅く、大量のメモリを使用する可能性があります。
