---
title: エンティティの明示的な追跡-EF Core
description: 追加、アタッチ、更新、および削除を使用して、DbContext でエンティティを明示的に追跡する
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/explicit-tracking
ms.openlocfilehash: 28a6ec3e3c25dad70882b8681f78744a5979efe6
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129621"
---
# <a name="explicitly-tracking-entities"></a>エンティティの明示的な追跡

各 <xref:Microsoft.EntityFrameworkCore.DbContext> インスタンスは、エンティティに加えられた変更を追跡します。 これらの追跡対象エンティティは、が呼び出されると、データベースへの変更を <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> 行います。

Entity Framework Core (EF Core) の変更の追跡は、同じ <xref:Microsoft.EntityFrameworkCore.DbContext> インスタンスを使用してエンティティのクエリを実行し、を呼び出すことによって更新する場合に最適 <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> です。 これは、EF Core によってクエリされたエンティティの状態が自動的に追跡され、SaveChanges が呼び出されたときにこれらのエンティティに加えられた変更が検出されるためです。 この方法については、 [EF Core の Change Tracking](xref:core/change-tracking/index)に記載されています。

> [!TIP]
> このドキュメントでは、EF Core の変更の追跡のエンティティの状態と基本について理解していることを前提としています。 これらのトピックの詳細については、 [EF Core の Change Tracking](xref:core/change-tracking/index) を参照してください。

> [!TIP]
> [GitHub からサンプルコードをダウンロード](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/ChangeTrackingInEFCore)することで、このドキュメントのすべてのコードを実行し、デバッグすることができます。

> [!TIP]
> わかりやすくするために、このドキュメントではを使用し、などの非同期的なメソッドを参照して <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChangesAsync%2A> います。 特に明記されていない限り、非同期メソッドの呼び出しと待機は置き換えることができます。

## <a name="introduction"></a>はじめに

エンティティは、 <xref:Microsoft.EntityFrameworkCore.DbContext> コンテキストがそれらのエンティティを追跡するように明示的に "アタッチ" できます。 これは主に次の場合に役立ちます。

1. データベースに挿入される新しいエンティティを作成しています。
2. 以前に _別_ の dbcontext インスタンスによってクエリされた切断されたエンティティを再アタッチしています。

これらのうちの1つは、ほとんどのアプリケーションで必要となるもので、メソッドによって処理されるプライマリです <xref:Microsoft.EntityFrameworkCore.DbContext.Add%2A?displayProperty=nameWithType> 。

2つ目は、エンティティ **_が追跡されていないときに_** エンティティまたはリレーションシップを変更するアプリケーションによってのみ必要です。 たとえば、web アプリケーションは、ユーザーが変更を行ってエンティティを返送する web クライアントにエンティティを送信する場合があります。 これらのエンティティは、最初に DbContext からクエリを行った後に "切断" と呼ばれますが、クライアントに送信されると、そのコンテキストから切断されます。

Web アプリケーションは、これらのエンティティを再アタッチして再度追跡し、データベースに対する適切な更新を可能にするために加えられた変更を示す必要があり <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> ます。 これは主に、メソッドとメソッドによって処理され <xref:Microsoft.EntityFrameworkCore.DbContext.Attach%2A?displayProperty=nameWithType> <xref:Microsoft.EntityFrameworkCore.DbContext.Update%2A?displayProperty=nameWithType> ます。

> [!TIP]
> 通常は、クエリの対象となったのと _同じ DbContext インスタンス_ にエンティティをアタッチする必要はありません。 定期的に追跡しないクエリを実行し、返されたエンティティを同じコンテキストにアタッチすることは避けてください。 これは、追跡クエリを使用した場合よりも遅くなります。また、シャドウプロパティの値がないなどの問題が発生し、適切に機能しなくなる可能性があります。

### <a name="generated-verses-explicit-key-values"></a>生成されたキー値

既定では、整数と GUID の [キープロパティ](xref:core/modeling/keys) は、 [自動的に生成されたキー値](xref:core/modeling/generated-properties)を使用するように構成されています。 **変更の追跡には大きな利点があります。未設定のキー値は、エンティティが "新規" であることを示し** ます。 "New" によって、データベースにまだ挿入されていないことを意味します。

次のセクションでは、2つのモデルを使用します。 最初のは、生成されたキー値を使用し **ない** ように構成されています。

<!--
    public class Blog
    {
        [DatabaseGenerated(DatabaseGeneratedOption.None)]
        public int Id { get; set; }

        public string Name { get; set; }

        public IList<Post> Posts { get; } = new List<Post>();
    }

    public class Post
    {
        [DatabaseGenerated(DatabaseGeneratedOption.None)]
        public int Id { get; set; }

        public string Title { get; set; }
        public string Content { get; set; }

        public int? BlogId { get; set; }
        public Blog Blog { get; set; }
    }
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Model)]

生成されていない (明示的に設定された) キー値は、すべて明示的で簡単に実行できるため、各例で最初に示します。 次に、生成されたキー値を使用する例を示します。

<!--
public class Blog
{
    public int Id { get; set; }

    public string Name { get; set; }

    public ICollection<Post> Posts { get; } = new List<Post>();
}

public class Post
{
    public int Id { get; set; }

    public string Title { get; set; }
    public string Content { get; set; }

    public int? BlogId { get; set; }
    public Blog Blog { get; set; }
}
-->
[!code-csharp[Model](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Model)]

ここでは、生成されたキー値を使用することが [単純な整数キーの既定](xref:core/modeling/generated-properties)値であるため、このモデルのキープロパティには追加の構成は必要ありません。

## <a name="inserting-new-entities"></a>新しいエンティティの挿入

### <a name="explicit-key-values"></a>明示的なキー値

エンティティは、 `Added` によって挿入される状態で追跡される必要があり <xref:Microsoft.EntityFrameworkCore.DbContext.SaveChanges%2A> ます。 エンティティは、通常、、、、、またはの同等のメソッドのいずれかを呼び出すことによって、追加された状態に <xref:Microsoft.EntityFrameworkCore.DbContext.Add%2A?displayProperty=nameWithType> <xref:Microsoft.EntityFrameworkCore.DbContext.AddRange%2A?displayProperty=nameWithType> <xref:Microsoft.EntityFrameworkCore.DbContext.AddAsync%2A?displayProperty=nameWithType> <xref:Microsoft.EntityFrameworkCore.DbContext.AddRangeAsync%2A?displayProperty=nameWithType> <xref:Microsoft.EntityFrameworkCore.DbSet%601> なります。

> [!TIP]
> これらのメソッドはすべて、変更追跡のコンテキストで同じ方法で動作します。 詳細については、「 [その他の Change Tracking 機能](xref:core/change-tracking/miscellaneous) 」を参照してください。

たとえば、新しいブログの追跡を開始するには、次のようにします。

<!--
            context.Add(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                });
-->
[!code-csharp[Inserting_new_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Inserting_new_entities_1)]

この呼び出しの後にある [change tracker デバッグビュー](xref:core/change-tracking/debug-views) を調べると、コンテキストが状態の新しいエンティティを追跡していることが示され `Added` ます。

```output
Blog {Id: 1} Added
  Id: 1 PK
  Name: '.NET Blog'
  Posts: []
```

ただし、Add メソッドは、個々のエンティティに対してのみ機能します。 実際には、 _関連エンティティのグラフ全体の_ 追跡が開始され、すべての `Added` 状態になります。 たとえば、新しいブログと関連する新しい投稿を挿入するには、次のように入力します。

<!--
            context.Add(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                    Posts =
                    {
                        new Post
                        {
                            Id = 1,
                            Title = "Announcing the Release of EF Core 5.0",
                            Content = "Announcing the release of EF Core 5.0, a full featured cross-platform..."
                        },
                        new Post
                        {
                            Id = 2,
                            Title = "Announcing F# 5",
                            Content = "F# 5 is the latest version of F#, the functional programming language..."
                        }
                    }
                });
-->
[!code-csharp[Inserting_new_entities_2](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Inserting_new_entities_2)]

コンテキストは、これらのすべてのエンティティを次のように追跡するようになりました `Added` 。

```output
Blog {Id: 1} Added
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}]
Post {Id: 1} Added
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Added
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

上記の例では、キープロパティに明示的な値が設定されていることに注意 `Id` してください。 これは、キー値が自動的に生成されるのではなく、明示的に設定されたキー値を使用するようにモデルが構成されているためです。 生成されたキーを使用しない場合は、を呼び出す _前に_ 、キープロパティを明示的に設定する必要があり `Add` ます。 これらのキー値は、SaveChanges が呼び出されたときに挿入されます。 たとえば、SQLite を使用する場合は、次のようになります。

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='1' (DbType = String), @p1='.NET Blog' (Size = 9)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Blogs" ("Id", "Name")
VALUES (@p0, @p1);

-- Executed DbCommand (0ms) [Parameters=[@p2='1' (DbType = String), @p3='1' (DbType = String), @p4='Announcing the release of EF Core 5.0, a full featured cross-platform...' (Size = 72), @p5='Announcing the Release of EF Core 5.0' (Size = 37)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Posts" ("Id", "BlogId", "Content", "Title")
VALUES (@p2, @p3, @p4, @p5);

-- Executed DbCommand (0ms) [Parameters=[@p0='2' (DbType = String), @p1='1' (DbType = String), @p2='F# 5 is the latest version of F#, the functional programming language...' (Size = 72), @p3='Announcing F# 5' (Size = 15)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Posts" ("Id", "BlogId", "Content", "Title")
VALUES (@p0, @p1, @p2, @p3);
```

これらのエンティティは、 `Unchanged` データベース内に存在するようになったため、SaveChanges の完了後の状態で追跡されます。

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

### <a name="generated-key-values"></a>生成されたキー値

前述のように、既定では、[自動的に生成されたキー値](xref:core/modeling/generated-properties)を使用するように整数と GUID の[キープロパティ](xref:core/modeling/keys)が構成されています。 これは、アプリケーションが _キー値を明示的に設定してはいけない_ ことを意味します。 たとえば、新しいブログを挿入し、生成されたキー値ですべてを投稿するには、次のようにします。

<!--
            context.Add(
                new Blog
                {
                    Name = ".NET Blog",
                    Posts =
                    {
                        new Post
                        {
                            Title = "Announcing the Release of EF Core 5.0",
                            Content = "Announcing the release of EF Core 5.0, a full featured cross-platform..."
                        },
                        new Post
                        {
                            Title = "Announcing F# 5",
                            Content = "F# 5 is the latest version of F#, the functional programming language..."
                        }
                    }
                });
-->
[!code-csharp[Inserting_new_entities_3](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Inserting_new_entities_3)]

明示的なキー値と同様に、コンテキストはすべてのエンティティを次のように追跡するようになりました `Added` 。

```output
Blog {Id: -2147482644} Added
  Id: -2147482644 PK Temporary
  Name: '.NET Blog'
  Posts: [{Id: -2147482637}, {Id: -2147482636}]
Post {Id: -2147482637} Added
  Id: -2147482637 PK Temporary
  BlogId: -2147482644 FK Temporary
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: -2147482644}
Post {Id: -2147482636} Added
  Id: -2147482636 PK Temporary
  BlogId: -2147482644 FK Temporary
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: -2147482644}
```

この場合、エンティティごとに [一時的なキー値](xref:core/change-tracking/miscellaneous#temporary-values) が生成されていることに注意してください。 これらの値は SaveChanges が呼び出されるまで EF Core によって使用されます。この時点で、実際のキー値がデータベースから読み取られます。 たとえば、SQLite を使用する場合は、次のようになります。

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='.NET Blog' (Size = 9)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Blogs" ("Name")
VALUES (@p0);
SELECT "Id"
FROM "Blogs"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();

-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String), @p2='Announcing the release of EF Core 5.0, a full featured cross-platform...' (Size = 72), @p3='Announcing the Release of EF Core 5.0' (Size = 37)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Posts" ("BlogId", "Content", "Title")
VALUES (@p1, @p2, @p3);
SELECT "Id"
FROM "Posts"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();

-- Executed DbCommand (0ms) [Parameters=[@p0='1' (DbType = String), @p1='F# 5 is the latest version of F#, the functional programming language...' (Size = 72), @p2='Announcing F# 5' (Size = 15)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Posts" ("BlogId", "Content", "Title")
VALUES (@p0, @p1, @p2);
SELECT "Id"
FROM "Posts"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();
```

SaveChanges が完了すると、すべてのエンティティが実際のキー値で更新され、データベースの状態と一致した状態で追跡され `Unchanged` ます。

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

これは、明示的なキー値を使用した前の例とまったく同じ終了状態です。

> [!TIP]
> 生成されたキー値を使用している場合でも、明示的なキー値を設定できます。 EF Core は、このキー値を使用してを挿入しようとします。 Id 列を含む SQL Server を含む一部のデータベース構成では、このような挿入がサポートされていないため、がスローされます。

## <a name="attaching-existing-entities"></a>既存のエンティティのアタッチ

### <a name="explicit-key-values"></a>明示的なキー値

クエリから返されるエンティティは、状態で追跡され `Unchanged` ます。 `Unchanged`この状態は、エンティティがクエリされてから変更されていないことを意味します。 接続されていないエンティティは、HTTP 要求内の web クライアントから返されることがありますが、、 <xref:Microsoft.EntityFrameworkCore.DbContext.Attach%2A?displayProperty=nameWithType> <xref:Microsoft.EntityFrameworkCore.DbContext.AttachRange%2A?displayProperty=nameWithType> 、またはの同等のメソッドを使用して、この状態にすることができ <xref:Microsoft.EntityFrameworkCore.DbSet%601> ます。 たとえば、既存のブログの追跡を開始するには、次のようにします。

<!--
            context.Attach(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                });
-->
[!code-csharp[Attaching_existing_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Attaching_existing_entities_1)]

> [!NOTE]
> 次の例では、簡略化のためにを使用してエンティティを明示的に作成してい `new` ます。 通常、エンティティインスタンスは別のソースから取得されます。たとえば、クライアントから逆シリアル化されたり、HTTP Post のデータから作成されたりすることがあります。

この呼び出しの後にある [change tracker デバッグビュー](xref:core/change-tracking/debug-views) を調べると、エンティティが状態で追跡されていることがわかり `Unchanged` ます。

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: []
```

と同様に `Add` 、は、 `Attach` 接続されているエンティティのグラフ全体を状態に設定し `Unchanged` ます。 たとえば、既存のブログと関連する既存の投稿を添付するには、次のようにします。

<!--
            context.Attach(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                    Posts =
                    {
                        new Post
                        {
                            Id = 1,
                            Title = "Announcing the Release of EF Core 5.0",
                            Content = "Announcing the release of EF Core 5.0, a full featured cross-platform..."
                        },
                        new Post
                        {
                            Id = 2,
                            Title = "Announcing F# 5",
                            Content = "F# 5 is the latest version of F#, the functional programming language..."
                        }
                    }
                });
-->
[!code-csharp[Attaching_existing_entities_2](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Attaching_existing_entities_2)]

コンテキストは、これらのすべてのエンティティを次のように追跡するようになりました `Unchanged` 。

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

この時点で SaveChanges を呼び出すと、効果はありません。 すべてのエンティティはとしてマークさ `Unchanged` れるため、データベース内で更新するものはありません。

### <a name="generated-key-values"></a>生成されたキー値

前述のように、既定では、[自動的に生成されたキー値](xref:core/modeling/generated-properties)を使用するように整数と GUID の[キープロパティ](xref:core/modeling/keys)が構成されています。 これは、接続されていないエンティティを使用する場合の大きな利点です。未設定のキー値は、エンティティがまだデータベースに挿入されていないことを示します。 これにより、変更トラッカーは新しいエンティティを自動的に検出し、状態にすることができ `Added` ます。 たとえば、次のブログと投稿のグラフを添付することを検討してください。

```c#
            context.Attach(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                    Posts =
                    {
                        new Post
                        {
                            Id = 1,
                            Title = "Announcing the Release of EF Core 5.0",
                            Content = "Announcing the release of EF Core 5.0, a full featured cross-platform..."
                        },
                        new Post
                        {
                            Id = 2,
                            Title = "Announcing F# 5",
                            Content = "F# 5 is the latest version of F#, the functional programming language..."
                        },
                        new Post
                        {
                            Title = "Announcing .NET 5.0",
                            Content = ".NET 5.0 includes many enhancements, including single file applications, more..."
                        },
                    }
                });
```

<!--
            context.Attach(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                    Posts =
                    {
                        new Post
                        {
                            Id = 1,
                            Title = "Announcing the Release of EF Core 5.0",
                            Content = "Announcing the release of EF Core 5.0, a full featured cross-platform..."
                        },
                        new Post
                        {
                            Id = 2,
                            Title = "Announcing F# 5",
                            Content = "F# 5 is the latest version of F#, the functional programming language..."
                        },
                        new Post
                        {
                            Title = "Announcing .NET 5.0",
                            Content = ".NET 5.0 includes many enhancements, including single file applications, more..."
                        },
                    }
                });
-->
[!code-csharp[Attaching_existing_entities_3](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Attaching_existing_entities_3)]

ブログのキー値は1であり、データベースに既に存在していることを示しています。 2つのポストにはキー値も設定されていますが、3番目の投稿はありません。 EF Core には、このキー値が0として表示されます。これは、整数の CLR 既定値です。 この結果、次のように新しいエンティティがとしてマーク EF Core され `Added` `Unchanged` ます。

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}, {Id: -2147482636}]
Post {Id: -2147482636} Added
  Id: -2147482636 PK Temporary
  BlogId: 1 FK
  Content: '.NET 5.0 includes many enhancements, including single file a...'
  Title: 'Announcing .NET 5.0'
  Blog: {Id: 1}
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
```

この時点で SaveChanges を呼び出すと、エンティティに対して何も実行されません `Unchanged` が、新しいエンティティがデータベースに挿入されます。 たとえば、SQLite を使用する場合は、次のようになります。

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='1' (DbType = String), @p1='.NET 5.0 includes many enhancements, including single file applications, more...' (Size = 80), @p2='Announcing .NET 5.0' (Size = 19)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Posts" ("BlogId", "Content", "Title")
VALUES (@p0, @p1, @p2);
SELECT "Id"
FROM "Posts"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();
```

ここで重要な点は、生成されたキー EF Core 値を使用して、切断された **グラフ内の既存のエンティティを自動的に区別** することができることです。 簡単に言うと、生成されたキーを使用すると、エンティティにキー値が設定されていない場合、EF Core は常にエンティティを挿入します。

## <a name="updating-existing-entities"></a>既存のエンティティの更新

### <a name="explicit-key-values"></a>明示的なキー値

<xref:Microsoft.EntityFrameworkCore.DbContext.Update%2A?displayProperty=nameWithType>、 <xref:Microsoft.EntityFrameworkCore.DbContext.UpdateRange%2A?displayProperty=nameWithType> 、およびの同等のメソッドは、上で説明した <xref:Microsoft.EntityFrameworkCore.DbSet%601> メソッドとまったく同じように動作しますが、 `Attach` エンティティは状態ではなくに配置される点が異なり `Modfied` `Unchanged` ます。 たとえば、既存のブログの追跡を開始するには、次のようにし `Modified` ます。

<!--
            context.Update(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                });
-->
[!code-csharp[Updating_existing_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Updating_existing_entities_1)]

この呼び出しの後にある [change tracker デバッグビュー](xref:core/change-tracking/debug-views) を調べると、コンテキストが状態でこのエンティティを追跡していることが示され `Modified` ます。

```output
Blog {Id: 1} Modified
  Id: 1 PK
  Name: '.NET Blog' Modified
  Posts: []
```

やと同様 `Add` に `Attach` 、 `Update` 関連エンティティの _グラフ全体_ をとして実際にマークし `Modified` ます。 たとえば、既存のブログと関連する既存の投稿を次のように添付し `Modified` ます。

<!--
            context.Update(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                    Posts =
                    {
                        new Post
                        {
                            Id = 1,
                            Title = "Announcing the Release of EF Core 5.0",
                            Content = "Announcing the release of EF Core 5.0, a full featured cross-platform..."
                        },
                        new Post
                        {
                            Id = 2,
                            Title = "Announcing F# 5",
                            Content = "F# 5 is the latest version of F#, the functional programming language..."
                        }
                    }
                });
-->
[!code-csharp[Updating_existing_entities_2](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Updating_existing_entities_2)]

コンテキストは、これらのすべてのエンティティを次のように追跡するようになりました `Modified` 。

```output
Blog {Id: 1} Modified
  Id: 1 PK
  Name: '.NET Blog' Modified
  Posts: [{Id: 1}, {Id: 2}]
Post {Id: 1} Modified
  Id: 1 PK
  BlogId: 1 FK Modified Originally <null>
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...' Modified
  Title: 'Announcing the Release of EF Core 5.0' Modified
  Blog: {Id: 1}
Post {Id: 2} Modified
  Id: 2 PK
  BlogId: 1 FK Modified Originally <null>
  Content: 'F# 5 is the latest version of F#, the functional programming...' Modified
  Title: 'Announcing F# 5' Modified
  Blog: {Id: 1}
```

この時点で SaveChanges を呼び出すと、これらすべてのエンティティの更新がデータベースに送信されます。 たとえば、SQLite を使用する場合は、次のようになります。

```sql
-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String), @p0='.NET Blog' (Size = 9)], CommandType='Text', CommandTimeout='30']
UPDATE "Blogs" SET "Name" = @p0
WHERE "Id" = @p1;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p3='1' (DbType = String), @p0='1' (DbType = String), @p1='Announcing the release of EF Core 5.0, a full featured cross-platform...' (Size = 72), @p2='Announcing the Release of EF Core 5.0' (Size = 37)], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "BlogId" = @p0, "Content" = @p1, "Title" = @p2
WHERE "Id" = @p3;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p3='2' (DbType = String), @p0='1' (DbType = String), @p1='F# 5 is the latest version of F#, the functional programming language...' (Size = 72), @p2='Announcing F# 5' (Size = 15)], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "BlogId" = @p0, "Content" = @p1, "Title" = @p2
WHERE "Id" = @p3;
SELECT changes();
```

### <a name="generated-key-values"></a>生成されたキー値

と同様に `Attach` 、生成されたキー値の主な利点は `Update` 次のとおりです。未設定のキー値は、エンティティが新規で、データベースにまだ挿入されていないことを示します。 と同様に `Attach` 、これにより、DbContext は新しいエンティティを自動的に検出し、状態にすることができ `Added` ます。 たとえば、 `Update` ブログと投稿の次のグラフを使用してを呼び出すことを検討してください。

<!--
            context.Update(
                new Blog
                {
                    Id = 1,
                    Name = ".NET Blog",
                    Posts =
                    {
                        new Post
                        {
                            Id = 1,
                            Title = "Announcing the Release of EF Core 5.0",
                            Content = "Announcing the release of EF Core 5.0, a full featured cross-platform..."
                        },
                        new Post
                        {
                            Id = 2,
                            Title = "Announcing F# 5",
                            Content = "F# 5 is the latest version of F#, the functional programming language..."
                        }
                    }
                });
-->
[!code-csharp[Updating_existing_entities_3](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Updating_existing_entities_3)]

例と同様に `Attach` 、キー値のない post は new として検出され、状態に設定され `Added` ます。 その他のエンティティは次のようにマークされ `Modified` ます。

```output
Blog {Id: 1} Modified
  Id: 1 PK
  Name: '.NET Blog' Modified
  Posts: [{Id: 1}, {Id: 2}, {Id: -2147482633}]
Post {Id: -2147482633} Added
  Id: -2147482633 PK Temporary
  BlogId: 1 FK
  Content: '.NET 5.0 includes many enhancements, including single file a...'
  Title: 'Announcing .NET 5.0'
  Blog: {Id: 1}
Post {Id: 1} Modified
  Id: 1 PK
  BlogId: 1 FK Modified Originally <null>
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...' Modified
  Title: 'Announcing the Release of EF Core 5.0' Modified
  Blog: {Id: 1}
Post {Id: 2} Modified
  Id: 2 PK
  BlogId: 1 FK Modified Originally <null>
  Content: 'F# 5 is the latest version of F#, the functional programming...' Modified
  Title: 'Announcing F# 5' Modified
  Blog: {Id: 1}
```

この時点でを呼び出すと、 `SaveChanges` 新しいエンティティが挿入されている間に、既存のすべてのエンティティの更新がデータベースに送信されます。 たとえば、SQLite を使用する場合は、次のようになります。

```sql
-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String), @p0='.NET Blog' (Size = 9)], CommandType='Text', CommandTimeout='30']
UPDATE "Blogs" SET "Name" = @p0
WHERE "Id" = @p1;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p3='1' (DbType = String), @p0='1' (DbType = String), @p1='Announcing the release of EF Core 5.0, a full featured cross-platform...' (Size = 72), @p2='Announcing the Release of EF Core 5.0' (Size = 37)], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "BlogId" = @p0, "Content" = @p1, "Title" = @p2
WHERE "Id" = @p3;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p3='2' (DbType = String), @p0='1' (DbType = String), @p1='F# 5 is the latest version of F#, the functional programming language...' (Size = 72), @p2='Announcing F# 5' (Size = 15)], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "BlogId" = @p0, "Content" = @p1, "Title" = @p2
WHERE "Id" = @p3;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p0='1' (DbType = String), @p1='.NET 5.0 includes many enhancements, including single file applications, more...' (Size = 80), @p2='Announcing .NET 5.0' (Size = 19)], CommandType='Text', CommandTimeout='30']
INSERT INTO "Posts" ("BlogId", "Content", "Title")
VALUES (@p0, @p1, @p2);
SELECT "Id"
FROM "Posts"
WHERE changes() = 1 AND "rowid" = last_insert_rowid();
```

これは、切断されたグラフから更新と挿入を生成する非常に簡単な方法です。 ただし、一部のプロパティ値が変更されていない場合でも、追跡されるすべてのエンティティのすべてのプロパティについて、更新または挿入がデータベースに送信されます。 怖いすぎないようにしてください。小さいグラフを持つ多くのアプリケーションでは、これは簡単で実用的な方法で更新プログラムを生成できます。 ただし、他の複雑なパターンでは、「 [EF Core の Id 解決](xref:core/change-tracking/identity-resolution)」で説明されているように、より効率的な更新が発生する可能性があります。

## <a name="deleting-existing-entities"></a>既存のエンティティの削除

SaveChanges によって削除されるエンティティについては、状態で追跡する必要があり `Deleted` ます。 エンティティは、通常、、 `Deleted` 、またはの同等のメソッドのいずれかを呼び出すことによって、状態に <xref:Microsoft.EntityFrameworkCore.DbContext.Remove%2A?displayProperty=nameWithType> <xref:Microsoft.EntityFrameworkCore.DbContext.RemoveRange%2A?displayProperty=nameWithType> <xref:Microsoft.EntityFrameworkCore.DbSet%601> なります。 たとえば、既存の投稿を次のようにマークし `Deleted` ます。

<!--
            context.Remove(
                new Post
                {
                    Id = 2
                });
-->
[!code-csharp[Deleting_existing_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Deleting_existing_entities_1)]

この呼び出しの後にある [change tracker デバッグビュー](xref:core/change-tracking/debug-views) を調べると、コンテキストが状態のエンティティを追跡していることが示され `Deleted` ます。

```output
Post {Id: 2} Deleted
  Id: 2 PK
  BlogId: <null> FK
  Content: <null>
  Title: <null>
  Blog: <null>
```

このエンティティは、SaveChanges が呼び出されたときに削除されます。 たとえば、SQLite を使用する場合は、次のようになります。

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='2' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Posts"
WHERE "Id" = @p0;
SELECT changes();
```

SaveChanges が完了した後、削除されたエンティティはデータベースに存在しなくなったため、DbContext からデタッチされます。 このため、追跡されているエンティティがないため、デバッグビューは空です。

## <a name="deleting-dependentchild-entities"></a>依存/子エンティティの削除

グラフからの依存/子エンティティの削除は、プリンシパル/親エンティティを削除するよりも簡単です。 詳細については、次のセクションと「 [外部キーとナビゲーションの変更](xref:core/change-tracking/relationship-changes) 」を参照してください。

で作成されたエンティティに対してを呼び出すことは通常あり `Remove` `new` ません。 さらに、やとは異なり、 `Add` `Attach` `Update` `Remove` またはの状態で既に追跡されていないエンティティに対してを呼び出すことは珍しく `Unchanged` ありませ `Modified` ん。 代わりに、通常は、1つのエンティティまたは関連エンティティのグラフを追跡し、 `Remove` 削除する必要があるエンティティに対してを呼び出します。 通常、追跡対象のエンティティのグラフは次のいずれかによって作成されます。

1. エンティティに対するクエリの実行
2. 前の `Attach` `Update` セクションで説明したように、接続されていないエンティティのグラフでメソッドまたはメソッドを使用します。

たとえば、前のセクションのコードでは、クライアントから投稿を取得し、次のような処理を実行する可能性が高くなります。

<!--
            context.Attach(post);
            context.Remove(post);
-->
[!code-csharp[Deleting_dependent_child_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Deleting_dependent_child_entities_1)]

これは、前の例とまったく同じように動作します。これは、追跡されていないエンティティに対してを呼び出すと、 `Remove` 最初にアタッチされてからとしてマークが付けられるため `Deleted` です。

より現実的な例では、エンティティのグラフが最初にアタッチされ、それらのエンティティの一部が削除済みとしてマークされます。 例:

<!--
            // Attach a blog and associated posts
            context.Attach(blog);

            // Mark one post as Deleted
            context.Remove(blog.Posts[1]);
-->
[!code-csharp[Deleting_dependent_child_entities_2](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Deleting_dependent_child_entities_2)]

が呼び出されたエンティティを除き、すべてのエンティティはとしてマークされ `Unchanged` `Remove` ます。

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Deleted
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

このエンティティは、SaveChanges が呼び出されたときに削除されます。 たとえば、SQLite を使用する場合は、次のようになります。

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='2' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Posts"
WHERE "Id" = @p0;
SELECT changes();
```

SaveChanges が完了した後、削除されたエンティティはデータベースに存在しなくなったため、DbContext からデタッチされます。 その他のエンティティの `Unchanged` 状態は次のとおりです。

```output
Blog {Id: 1} Unchanged
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}]
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
```

## <a name="deleting-principalparent-entities"></a>プリンシパル/親エンティティの削除

2つのエンティティ型を接続する各リレーションシップには、プリンシパルまたは親 end、および依存または子 end があります。 依存/子エンティティは、外部キープロパティを持つエンティティです。 一対多のリレーションシップでは、プリンシパル/親は "一" 側にあり、依存/子は "多" 側にあります。 詳細については、「 [リレーションシップ](xref:core/modeling/relationships) 」を参照してください。

上記の例では、投稿を削除しようとしています。これは、ブログ内の依存/子エンティティです。一対多のリレーションシップを投稿しています。 依存/子エンティティを削除しても、他のエンティティには影響がないため、この方法は比較的簡単です。 一方、プリンシパル/親エンティティを削除すると、依存/子エンティティにも影響を与える必要があります。 そうしないと、外部キーの値は、存在しなくなった主キーの値を参照したままになります。 これは無効なモデル状態であるため、ほとんどのデータベースで参照制約エラーが発生します。

この無効なモデルの状態は、次の2つの方法で処理できます。

1. FK 値を null に設定しています。 これは、依存/子がプリンシパル/親に関連付けられなくなったことを示します。 これは、外部キーを null 許容にする必要があるオプションのリレーションシップの既定値です。 FK を null に設定することは、必要なリレーションシップに対しては無効です。通常、外部キーは null 非許容です。
2. 依存/子を削除しています。 これは必須のリレーションシップの既定値であり、オプションのリレーションシップに対しても有効です。

変更の追跡とリレーションシップの詳細については、「 [外部キーとナビゲーションの変更](xref:core/change-tracking/relationship-changes) 」を参照してください。

### <a name="optional-relationships"></a>省略可能なリレーションシップ

`Post.BlogId`外部キープロパティは、使用しているモデルで null 値が許容されます。 つまり、リレーションシップは省略可能であるため、EF Core の既定の動作では、 `BlogId` ブログが削除されるときに、外部キープロパティが null に設定されます。 例:

<!--
            // Attach a blog and associated posts
            context.Attach(blog);

            // Mark one post as Deleted
            context.Remove(blog.Posts[1]);
-->
[!code-csharp[Deleting_principal_parent_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysSamples.cs?name=Deleting_principal_parent_entities_1)]

の呼び出しの後にある [change tracker デバッグビュー](xref:core/change-tracking/debug-views) を調べると、 `Remove` 予想どおり、ブログが次のようにマークされていることがわかり `Deleted` ます。

```output
Blog {Id: 1} Deleted
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}]
Post {Id: 1} Modified
  Id: 1 PK
  BlogId: <null> FK Modified Originally 1
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: <null>
Post {Id: 2} Modified
  Id: 2 PK
  BlogId: <null> FK Modified Originally 1
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: <null>
```

興味深いことに、すべての関連する投稿はとしてマークされました `Modified` 。 これは、各エンティティの外部キープロパティが null に設定されているためです。 SaveChanges を呼び出すと、データベース内の各投稿の外部キー値が null に更新されてから、ブログが削除されます。

```sql
-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String), @p0=NULL], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "BlogId" = @p0
WHERE "Id" = @p1;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p1='2' (DbType = String), @p0=NULL], CommandType='Text', CommandTimeout='30']
UPDATE "Posts" SET "BlogId" = @p0
WHERE "Id" = @p1;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p2='1' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Blogs"
WHERE "Id" = @p2;
SELECT changes();
```

SaveChanges が完了した後、削除されたエンティティはデータベースに存在しなくなったため、DbContext からデタッチされます。 他のエンティティは `Unchanged` 、データベースの状態と一致する null 外部キー値を使用してとしてマークされるようになりました。

```output
Post {Id: 1} Unchanged
  Id: 1 PK
  BlogId: <null> FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: <null>
Post {Id: 2} Unchanged
  Id: 2 PK
  BlogId: <null> FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: <null>
```

### <a name="required-relationships"></a>必須リレーションシップ

`Post.BlogId`外部キープロパティが null 非許容の場合、ブログと投稿の関係が "必須" になります。 このような場合、既定では、プリンシパル/親が削除されると、EF Core によって依存/子エンティティが削除されます。 たとえば、前の例のように、関連する投稿を含むブログを削除します。

<!--
            // Attach a blog and associated posts
            context.Attach(blog);

            // Mark one post as Deleted
            context.Remove(blog.Posts[1]);
-->
[!code-csharp[Deleting_principal_parent_entities_1](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/ExplicitKeysRequiredSamples.cs?name=Deleting_principal_parent_entities_1)]

の呼び出しの後にある [change tracker デバッグビュー](xref:core/change-tracking/debug-views) を調べると、 `Remove` 予想どおり、ブログは次のようにマークされ `Deleted` ます。

```output
Blog {Id: 1} Deleted
  Id: 1 PK
  Name: '.NET Blog'
  Posts: [{Id: 1}, {Id: 2}]
Post {Id: 1} Deleted
  Id: 1 PK
  BlogId: 1 FK
  Content: 'Announcing the release of EF Core 5.0, a full featured cross...'
  Title: 'Announcing the Release of EF Core 5.0'
  Blog: {Id: 1}
Post {Id: 2} Deleted
  Id: 2 PK
  BlogId: 1 FK
  Content: 'F# 5 is the latest version of F#, the functional programming...'
  Title: 'Announcing F# 5'
  Blog: {Id: 1}
```

ここで興味深いのは、関連するすべての投稿もとしてマークされていることです `Deleted` 。 SaveChanges を呼び出すと、ブログと関連するすべての投稿がデータベースから削除されます。

```sql
-- Executed DbCommand (0ms) [Parameters=[@p0='1' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Posts"
WHERE "Id" = @p0;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p0='2' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Posts"
WHERE "Id" = @p0;
SELECT changes();

-- Executed DbCommand (0ms) [Parameters=[@p1='1' (DbType = String)], CommandType='Text', CommandTimeout='30']
DELETE FROM "Blogs"
WHERE "Id" = @p1;
```

SaveChanges が完了した後、削除されたすべてのエンティティは、データベースに存在しなくなったため、DbContext からデタッチされます。 そのため、デバッグビューからの出力は空です。

> [!NOTE]
> このドキュメントでは、EF Core でのリレーションシップの操作に関する表面についてのみ説明します。 リレーションシップのモデリングの詳細および[外部キーとナビゲーションの変更](xref:core/change-tracking/relationship-changes)の詳細については、「[リレーションシップ](xref:core/modeling/relationships)」を参照してください。 SaveChanges を呼び出す場合の依存/子エンティティの更新/削除の詳細については、「

## <a name="custom-tracking-with-trackgraph"></a>TrackGraph を使用したカスタム追跡

<xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.TrackGraph%2A?displayProperty=nameWithType> は `Add` と似 `Attach` て `Update` いますが、追跡する前にすべてのエンティティインスタンスに対してコールバックを生成する点が異なります。 これにより、グラフ内の個々のエンティティを追跡する方法を決定するときに、カスタムロジックを使用できます。

たとえば、生成されたキー値を持つエンティティを追跡するときに使用 EF Core ルールを考えてみます。 kye 値がゼロの場合、エンティティは新しいので、挿入する必要があります。 このルールを拡張して、キー値が負であるかどうかを示し、エンティティを削除する必要があります。 これにより、切断されたグラフのエンティティの主キー値を変更して、削除されたエンティティをマークすることができます。

<!--
            blog.Posts.Add(
                new Post
                {
                    Title = "Announcing .NET 5.0",
                    Content = ".NET 5.0 includes many enhancements, including single file applications, more..."
                }
            );

            var toDelete = blog.Posts.Single(e => e.Title == "Announcing F# 5");
            toDelete.Id = -toDelete.Id;
-->
[!code-csharp[Custom_tracking_with_TrackGraph_1a](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Custom_tracking_with_TrackGraph_1a)]

この切断されたグラフは、TrackGraph を使用して追跡できます。

<!--
        public static void UpdateBlog(Blog blog)
        {
            using var context = new BlogsContext();

            context.ChangeTracker.TrackGraph(
                blog, node =>
                    {
                        var propertyEntry = node.Entry.Property("Id");
                        var keyValue = (int)propertyEntry.CurrentValue;

                        if (keyValue == 0)
                        {
                            node.Entry.State = EntityState.Added;
                        }
                        else if (keyValue < 0)
                        {
                            propertyEntry.CurrentValue = -keyValue;
                            node.Entry.State = EntityState.Deleted;
                        }
                        else
                        {
                            node.Entry.State = EntityState.Modified;
                        }

                        Console.WriteLine($"Tracking {node.Entry.Metadata.DisplayName()} with key value {keyValue} as {node.Entry.State}");

                    });

            context.SaveChanges();
        }
-->
[!code-csharp[Custom_tracking_with_TrackGraph_1b](../../../samples/core/ChangeTracking/ChangeTrackingInEFCore/GeneratedKeysSamples.cs?name=Custom_tracking_with_TrackGraph_1b)]

上記のコードでは、グラフ内の各エンティティについて、 _エンティティを追跡する前に_ 主キーの値を確認します。 値が設定されていない (ゼロ) キー値の場合、コードは通常実行する EF Core を行います。 つまり、キーが設定されていない場合、エンティティはとマークされ `Added` ます。 キーが設定されていて、値が負でない場合、エンティティはとマークされ `Modified` ます。 ただし、負ではないキー値が見つかった場合は、その実際の負でない値が復元され、エンティティはとして追跡され `Deleted` ます。

このコードを実行した場合の出力は次のとおりです。

```output
Tracking Blog with key value 1 as Modified
Tracking Post with key value 1 as Modified
Tracking Post with key value -2 as Deleted
Tracking Post with key value 0 as Added
```

> [!NOTE]
> わかりやすくするために、このコードでは、各エンティティにという整数の主キープロパティがあることを前提としてい `Id` ます。 これは、抽象基本クラスまたはインターフェイスに体系化ことができます。 または、 <xref:Microsoft.EntityFrameworkCore.Metadata.IEntityType> このコードがあらゆる種類のエンティティで動作するように、メタデータから主キーのプロパティを取得することもできます。

TrackGraph には2つのオーバーロードがあります。 上で使用した単純なオーバーロードでは、EF Core は、グラフの走査をいつ停止するかを決定します。 具体的には、エンティティが既に追跡されている場合、またはコールバックがエンティティの追跡を開始しない場合、特定のエンティティからの新しい関連エンティティへのアクセスを停止します。

高度なオーバーロードには、 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.TrackGraph%60%601(System.Object,%60%600,System.Func{Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntryGraphNode{%60%600},System.Boolean})?displayProperty=nameWithType> ブール値を返すコールバックがあります。 コールバックが false を返す場合、グラフトラバーサルは停止します。それ以外の場合は、続行されます。 このオーバーロードを使用する場合は、無限ループを避けるために注意する必要があります。

高度なオーバーロードでは、TrackGraph に状態を指定することもできます。この状態は、各コールバックに渡されます。
