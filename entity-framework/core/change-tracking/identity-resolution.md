---
title: Id 解決-EF Core
description: 主キー値を使用して複数のエンティティインスタンスを1つのインスタンスに解決する
author: ajcvickers
ms.date: 12/30/2020
uid: core/change-tracking/identity-resolution
ms.openlocfilehash: f94b61371dcead27853799719dabc7849500d466
ms.sourcegitcommit: 032a1767d7a6e42052a005f660b80372c6521e7e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2021
ms.locfileid: "98129636"
---
# <a name="identity-resolution-in-ef-core"></a>EF Core での id 解決

は、 <xref:Microsoft.EntityFrameworkCore.DbContext> 特定の主キー値を持つ1つのエンティティインスタンスだけを追跡できます。 これは、同じキー値を持つエンティティの複数のインスタンスを1つのインスタンスに解決する必要があることを意味します。 これは "id 解決" と呼ばれます。 Id 解決によって Entity Framework Core (EF Core) は、エンティティのリレーションシップやプロパティ値に関するあいまいさのない一貫したグラフを追跡します。

> [!TIP]
> このドキュメントでは、EF Core の変更の追跡のエンティティの状態と基本について理解していることを前提としています。 これらのトピックの詳細については、 [EF Core の Change Tracking](xref:core/change-tracking/index) を参照してください。

> [!TIP]
> [GitHub からサンプルコードをダウンロード](https://github.com/dotnet/EntityFramework.Docs/tree/master/samples/core/ChangeTracking/IdentityResolutionInEFCore)することで、このドキュメントのすべてのコードを実行し、デバッグすることができます。

## <a name="introduction"></a>はじめに

次のコードでは、エンティティに対してクエリを実行し、同じ主キー値を持つ別のインスタンスをアタッチしようとしています。

<!--
            using var context = new BlogsContext();

            var blogA = context.Blogs.Single(e => e.Id == 1);
            var blogB = new Blog { Id = 1, Name = ".NET Blog (All new!)" };

            try
            {
                context.Update(blogB); // This will throw
            }
            catch (Exception e)
            {
                Console.WriteLine(e);
            }
-->
[!code-csharp[Identity_Resolution_in_EF_Core_1](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Identity_Resolution_in_EF_Core_1)]

このコードを実行すると、次の例外が発生します。

> InvalidOperationException: キー値 ' {Id: 1} ' を持つ別のインスタンスが既に追跡されているため、エンティティ型 ' Blog ' のインスタンスを追跡できません。 既存のエンティティをアタッチする場合は、特定のキー値を持つ1つのエンティティインスタンスのみがアタッチされていることを確認します。

次の理由により、EF Core には1つのインスタンスが必要です。

- プロパティ値は、複数のインスタンス間で異なる場合があります。 データベースを更新するときは、使用するプロパティ値を EF Core が認識している必要があります。
- 他のエンティティとのリレーションシップは、複数のインスタンス間で異なる場合があります。 たとえば、"ブログ a" は、"ブログ b" とは異なる投稿のコレクションに関連付けられている場合があります。

上記の例外は、通常、次のような状況で発生します。

- エンティティを更新しようとしたとき
- シリアル化されたエンティティのグラフを追跡しようとすると
- 自動的に生成されないキー値の設定に失敗した場合
- 複数の作業単位に対して DbContext インスタンスを再利用する場合

これらの各状況については、次のセクションで説明します。

## <a name="updating-an-entity"></a>エンティティの更新

エンティティを新しい値で更新する方法はいくつかあります。これについては [Change Tracking 「EF Core」](xref:core/change-tracking/index) と「 [明示的なエンティティの追跡](xref:core/change-tracking/explicit-tracking)」を確認してください。 これらのアプローチについては、id 解決のコンテキストで以下に説明します。 注意すべき重要な点は、各方法では、クエリを使用するか、またはのいずれかの呼び出しを使用し `Update` `Attach` ますが、 **_両方_** を使用しないことです。

### <a name="call-update"></a>Update の呼び出し

多くの場合、更新するエンティティは、SaveChanges に使用する DbContext に対するクエリからは取得されません。 たとえば、web アプリケーションで、POST 要求の情報からエンティティインスタンスを作成できます。 これを処理する最も簡単な方法は、またはを使用することです <xref:Microsoft.EntityFrameworkCore.DbContext.Update%2A?displayProperty=nameWithType> <xref:Microsoft.EntityFrameworkCore.DbSet%601.Update%2A?displayProperty=nameWithType> 。 例:

<!--
    public static void UpdateFromHttpPost1(Blog blog)
    {
        using var context = new BlogsContext();

        context.Update(blog);

        context.SaveChanges();
    }
-->
[!code-csharp[Updating_an_entity_1](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Updating_an_entity_1)]

この場合、次のようになります。

- エンティティのインスタンスは1つだけ作成されます。
- エンティティインスタンスは、更新の実行の一環としてデータベースからは照会され **ません** 。
- 実際に変更されたかどうかに関係なく、すべてのプロパティ値がデータベースで更新されます。
- 1つのデータベースラウンドトリップが行われます。

### <a name="query-then-apply-changes"></a>クエリを実行し、変更を適用する

通常、POST 要求の情報からエンティティが作成されるときに、どのプロパティ値が実際に変更されたかは不明です。 多くの場合、前の例で行ったように、データベース内のすべての値を更新するだけで十分です。 ただし、アプリケーションが多数のエンティティを処理していて、それらの少数の部分だけが実際の変更を行っている場合は、送信される更新プログラムを制限すると便利な場合があります。 これを実現するには、クエリを実行して、データベース内に現在存在しているエンティティを追跡し、それらの追跡対象エンティティに変更を適用します。 例:

<!--
    public static void UpdateFromHttpPost2(Blog blog)
    {
        using var context = new BlogsContext();

        var trackedBlog = context.Blogs.Find(blog.Id);

        trackedBlog.Name = blog.Name;
        trackedBlog.Summary = blog.Summary;

        context.SaveChanges();
    }
-->
[!code-csharp[Updating_an_entity_2](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Updating_an_entity_2)]

この場合、次のようになります。

- エンティティの1つのインスタンスのみが追跡されます。クエリによってデータベースから返されるものです `Find` 。
- `Update`、 `Attach` 、などが使用され **ていません** 。
- 実際に変更されたプロパティ値だけがデータベースで更新されます。
- 2つのデータベースのラウンドトリップが行われます。

EF Core には、このようなプロパティ値を転送するためのヘルパーがいくつかあります。 たとえば、 <xref:Microsoft.EntityFrameworkCore.ChangeTracking.PropertyValues.SetValues%2A?displayProperty=nameWithType> は、指定されたオブジェクトからすべての値をコピーし、追跡対象のオブジェクトに設定します。

<!--
    public static void UpdateFromHttpPost3(Blog blog)
    {
        using var context = new BlogsContext();

        var trackedBlog = context.Blogs.Find(blog.Id);

        context.Entry(trackedBlog).CurrentValues.SetValues(blog);

        context.SaveChanges();
    }
-->
[!code-csharp[Updating_an_entity_3](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Updating_an_entity_3)]

`SetValues` では、エンティティ型のプロパティと一致するプロパティ名を持つデータ転送オブジェクト (Dto) を含む、さまざまなオブジェクトの種類を使用できます。 例:

<!--
    public static void UpdateFromHttpPost4(BlogDto dto)
    {
        using var context = new BlogsContext();

        var trackedBlog = context.Blogs.Find(dto.Id);

        context.Entry(trackedBlog).CurrentValues.SetValues(dto);

        context.SaveChanges();
    }
-->
[!code-csharp[Updating_an_entity_4](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Updating_an_entity_4)]

または、プロパティ値の名前と値のエントリを含むディクショナリ。

<!--
    public static void UpdateFromHttpPost5(Dictionary<string, object> propertyValues)
    {
        using var context = new BlogsContext();

        var trackedBlog = context.Blogs.Find(propertyValues["Id"]);

        context.Entry(trackedBlog).CurrentValues.SetValues(propertyValues);

        context.SaveChanges();
    }
-->
[!code-csharp[Updating_an_entity_5](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Updating_an_entity_5)]

このようなプロパティ値の使用方法の詳細については、「追跡対象の [エンティティへのアクセス](xref:core/change-tracking/entity-entries) 」を参照してください。

### <a name="use-original-values"></a>元の値を使用する

ここまでの手順では、更新を行う前にクエリを実行したか、変更されたかどうかに関係なくすべてのプロパティ値を更新しました。 更新の一部としてクエリを実行せずに変更された値のみを更新するには、変更されたプロパティ値に関する特定の情報が必要です。 この情報を取得する一般的な方法は、HTTP Post または同様に、現在の値と元の値の両方を返すことです。 例:

<!--
    public static void UpdateFromHttpPost6(Blog blog, Dictionary<string, object> originalValues)
    {
        using var context = new BlogsContext();

        context.Attach(blog);
        context.Entry(blog).OriginalValues.SetValues(originalValues);

        context.SaveChanges();
    }
-->
[!code-csharp[Updating_an_entity_6](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Updating_an_entity_6)]

このコードでは、変更された値を持つエンティティが最初にアタッチされます。 これにより、EF Core は状態のエンティティを追跡します。 `Unchanged` つまり、プロパティ値が変更済みとしてマークされていません。 次に、元の値のディクショナリがこの追跡対象のエンティティに適用されます。 これにより、現在の値と元の値が変更されたプロパティとしてマークが付けられます。 現在の値と元の値が同じプロパティは、変更済みとしてマークされません。

この場合、次のようになります。

- アタッチを使用して、エンティティの1つのインスタンスのみが追跡されます。
- エンティティインスタンスは、更新の実行の一環としてデータベースからは照会され **ません** 。
- 元の値を適用することで、実際に変更されたプロパティ値だけがデータベースで更新されるようになります。
- 1つのデータベースラウンドトリップが行われます。

前のセクションの例と同様に、元の値をディクショナリとして渡す必要はありません。エンティティインスタンスまたは DTO も機能します。

> [!TIP]
> このアプローチには魅力的な特性がありますが、エンティティの元の値を web クライアントとの間で送信する必要があります。 この追加の複雑さにメリットがあるかどうかを慎重に検討してください。多くのアプリケーションでは、より簡単な方法の1つがより実用的です。

## <a name="attaching-a-serialized-graph"></a>シリアル化されたグラフのアタッチ

EF Core は、「 [外部キーと](xref:core/change-tracking/relationship-changes)ナビゲーションの変更」で説明されているように、外部キーおよびナビゲーションプロパティを介して接続されたエンティティのグラフを操作します。 これらのグラフが、たとえば JSON ファイルからを使用して EF Core 以外で作成されている場合は、同じエンティティの複数のインスタンスを持つことができます。 これらの重複は、グラフを追跡する前に1つのインスタンスに解決する必要があります。

### <a name="graphs-with-no-duplicates"></a>重複しないグラフ

さらに先に進む前に、次の点を認識しておくことが重要です。

- 多くの場合、シリアライザーには、グラフ内のループや重複するインスタンスを処理するオプションがあります。
- グラフルートとして使用されるオブジェクトを選択すると、多くの場合、重複を減らしたり削除したりするのに役立ちます。

可能な場合は、シリアル化オプションを使用して、重複しないルートを選択します。 たとえば、次のコードでは、 [Json.NET](https://www.nuget.org/packages/Newtonsoft.Json/) を使用して、ブログのリストをそれぞれに関連付けられた投稿と共にシリアル化しています。

<!--
            using var context = new BlogsContext();

            var blogs = context.Blogs.Include(e => e.Posts).ToList();

            var serialized = JsonConvert.SerializeObject(
                blogs,
                new JsonSerializerSettings
                {
                    ReferenceLoopHandling = ReferenceLoopHandling.Ignore,
                    Formatting = Formatting.Indented
                });

            Console.WriteLine(serialized);
-->
[!code-csharp[Attaching_a_serialized_graph_1a](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/SerializedGraphExamples.cs?name=Attaching_a_serialized_graph_1a)]

このコードから生成される JSON は次のとおりです。

```json
[
  {
    "Id": 1,
    "Name": ".NET Blog",
    "Summary": "Posts about .NET",
    "Posts": [
      {
        "Id": 1,
        "Title": "Announcing the Release of EF Core 5.0",
        "Content": "Announcing the release of EF Core 5.0, a full featured cross-platform...",
        "BlogId": 1
      },
      {
        "Id": 2,
        "Title": "Announcing F# 5",
        "Content": "F# 5 is the latest version of F#, the functional programming language...",
        "BlogId": 1
      }
    ]
  },
  {
    "Id": 2,
    "Name": "Visual Studio Blog",
    "Summary": "Posts about Visual Studio",
    "Posts": [
      {
        "Id": 3,
        "Title": "Disassembly improvements for optimized managed debugging",
        "Content": "If you are focused on squeezing out the last bits of performance for your .NET service or...",
        "BlogId": 2
      },
      {
        "Id": 4,
        "Title": "Database Profiling with Visual Studio",
        "Content": "Examine when database queries were executed and measure how long the take using...",
        "BlogId": 2
      }
    ]
  }
]
```

JSON には、重複するブログや投稿がないことに注意してください。 これは、への単純な呼び出しに `Update` よって、データベース内のこれらのエンティティが更新されることを意味します。

<!--
        public static void UpdateBlogsFromJson(string json)
        {
            using var context = new BlogsContext();

            var blogs = JsonConvert.DeserializeObject<List<Blog>>(json);

            foreach (var blog in blogs)
            {
                context.Update(blog);
            }

            context.SaveChanges();
        }
-->
[!code-csharp[Attaching_a_serialized_graph_1b](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/SerializedGraphExamples.cs?name=Attaching_a_serialized_graph_1b)]

### <a name="handling-duplicates"></a>重複の処理

前の例のコードでは、各ブログに関連する投稿をシリアル化しています。 これが、関連付けられているブログと共に各投稿をシリアル化するように変更された場合は、シリアル化された JSON に重複が導入されます。 例:

<!--
            using var context = new BlogsContext();

            var posts = context.Posts.Include(e => e.Blog).ToList();

            var serialized = JsonConvert.SerializeObject(
                posts,
                new JsonSerializerSettings
                {
                    ReferenceLoopHandling = ReferenceLoopHandling.Ignore,
                    Formatting = Formatting.Indented
                });

            Console.WriteLine(serialized);
-->
[!code-csharp[Attaching_a_serialized_graph_2](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/SerializedGraphExamples.cs?name=Attaching_a_serialized_graph_2)]

シリアル化された JSON は次のようになります。

```json
[
  {
    "Id": 1,
    "Title": "Announcing the Release of EF Core 5.0",
    "Content": "Announcing the release of EF Core 5.0, a full featured cross-platform...",
    "BlogId": 1,
    "Blog": {
      "Id": 1,
      "Name": ".NET Blog",
      "Summary": "Posts about .NET",
      "Posts": [
        {
          "Id": 2,
          "Title": "Announcing F# 5",
          "Content": "F# 5 is the latest version of F#, the functional programming language...",
          "BlogId": 1
        }
      ]
    }
  },
  {
    "Id": 2,
    "Title": "Announcing F# 5",
    "Content": "F# 5 is the latest version of F#, the functional programming language...",
    "BlogId": 1,
    "Blog": {
      "Id": 1,
      "Name": ".NET Blog",
      "Summary": "Posts about .NET",
      "Posts": [
        {
          "Id": 1,
          "Title": "Announcing the Release of EF Core 5.0",
          "Content": "Announcing the release of EF Core 5.0, a full featured cross-platform...",
          "BlogId": 1
        }
      ]
    }
  },
  {
    "Id": 3,
    "Title": "Disassembly improvements for optimized managed debugging",
    "Content": "If you are focused on squeezing out the last bits of performance for your .NET service or...",
    "BlogId": 2,
    "Blog": {
      "Id": 2,
      "Name": "Visual Studio Blog",
      "Summary": "Posts about Visual Studio",
      "Posts": [
        {
          "Id": 4,
          "Title": "Database Profiling with Visual Studio",
          "Content": "Examine when database queries were executed and measure how long the take using...",
          "BlogId": 2
        }
      ]
    }
  },
  {
    "Id": 4,
    "Title": "Database Profiling with Visual Studio",
    "Content": "Examine when database queries were executed and measure how long the take using...",
    "BlogId": 2,
    "Blog": {
      "Id": 2,
      "Name": "Visual Studio Blog",
      "Summary": "Posts about Visual Studio",
      "Posts": [
        {
          "Id": 3,
          "Title": "Disassembly improvements for optimized managed debugging",
          "Content": "If you are focused on squeezing out the last bits of performance for your .NET service or...",
          "BlogId": 2
        }
      ]
    }
  }
]
```

グラフに、同じキー値を持つ複数のブログインスタンスと、同じキー値を持つ複数の Post インスタンスが含まれるようになったことに注意してください。 前の例のようにこのグラフを追跡しようとすると、がスローされます。

> InvalidOperationException: キー値 ' {Id: 2} ' を持つ別のインスタンスが既に追跡されているため、エンティティ型 ' Post ' のインスタンスを追跡できません。 既存のエンティティをアタッチする場合は、特定のキー値を持つ1つのエンティティインスタンスのみがアタッチされていることを確認します。

これを解決するには、次の2つの方法があります。

- 参照を保持する JSON シリアル化オプションを使用する
- グラフの追跡中に id 解決を実行する

#### <a name="preserve-references"></a>参照を保持する

Json.NET は、 `PreserveReferencesHandling` これを処理するオプションを提供します。 例:

<!--
            var serialized = JsonConvert.SerializeObject(
                posts,
                new JsonSerializerSettings
                {
                    PreserveReferencesHandling = PreserveReferencesHandling.All,
                    Formatting = Formatting.Indented
                });
-->
[!code-csharp[Attaching_a_serialized_graph_3](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/SerializedGraphExamples.cs?name=Attaching_a_serialized_graph_3)]

結果の JSON は次のようになります。

```json
{
  "$id": "1",
  "$values": [
    {
      "$id": "2",
      "Id": 1,
      "Title": "Announcing the Release of EF Core 5.0",
      "Content": "Announcing the release of EF Core 5.0, a full featured cross-platform...",
      "BlogId": 1,
      "Blog": {
        "$id": "3",
        "Id": 1,
        "Name": ".NET Blog",
        "Summary": "Posts about .NET",
        "Posts": [
          {
            "$ref": "2"
          },
          {
            "$id": "4",
            "Id": 2,
            "Title": "Announcing F# 5",
            "Content": "F# 5 is the latest version of F#, the functional programming language...",
            "BlogId": 1,
            "Blog": {
              "$ref": "3"
            }
          }
        ]
      }
    },
    {
      "$ref": "4"
    },
    {
      "$id": "5",
      "Id": 3,
      "Title": "Disassembly improvements for optimized managed debugging",
      "Content": "If you are focused on squeezing out the last bits of performance for your .NET service or...",
      "BlogId": 2,
      "Blog": {
        "$id": "6",
        "Id": 2,
        "Name": "Visual Studio Blog",
        "Summary": "Posts about Visual Studio",
        "Posts": [
          {
            "$ref": "5"
          },
          {
            "$id": "7",
            "Id": 4,
            "Title": "Database Profiling with Visual Studio",
            "Content": "Examine when database queries were executed and measure how long the take using...",
            "BlogId": 2,
            "Blog": {
              "$ref": "6"
            }
          }
        ]
      }
    },
    {
      "$ref": "7"
    }
  ]
}
```

この JSON では `"$ref": "5"` 、グラフ内の既存のインスタンスを参照するのような参照で重複部分が置換されていることに注意してください。 このグラフは、上に示すように、の単純な呼び出しを使用して追跡でき `Update` ます。

<xref:System.Text.Json>.Net 基底クラスライブラリ (BCL) でのサポートには同様のオプションがあるため、同じ結果が得られます。 例:

<!--
            var serialized = System.Text.Json.JsonSerializer.Serialize(posts, new System.Text.Json.JsonSerializerOptions
            {
                ReferenceHandler = ReferenceHandler.Preserve,
                WriteIndented = true
            });
-->
[!code-csharp[Attaching_a_serialized_graph_4](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/SerializedGraphExamples.cs?name=Attaching_a_serialized_graph_4)]

#### <a name="resolve-duplicates"></a>重複の解決

シリアル化プロセスで重複を排除できない場合は、を使用してその <xref:Microsoft.EntityFrameworkCore.ChangeTracking.ChangeTracker.TrackGraph%2A?displayProperty=nameWithType> 処理方法を提供します。 TrackGraph はと同様に動作 `Add` `Attach` し `Update` ますが、追跡する前にすべてのエンティティインスタンスに対してコールバックが生成される点が異なります。 このコールバックを使用して、エンティティを追跡するか無視することができます。 例:

<!--
        public static void UpdatePostsFromJsonWithIdentityResolution(string json)
        {
            using var context = new BlogsContext();

            var posts = JsonConvert.DeserializeObject<List<Post>>(json);

            foreach (var post in posts)
            {
                context.ChangeTracker.TrackGraph(
                    post, node =>
                        {
                            var keyValue = node.Entry.Property("Id").CurrentValue;
                            var entityType = node.Entry.Metadata;

                            var existingEntity = node.Entry.Context.ChangeTracker.Entries()
                                .FirstOrDefault(
                                    e => Equals(e.Metadata, entityType)
                                         && Equals(e.Property("Id").CurrentValue, keyValue));

                            if (existingEntity == null)
                            {
                                Console.WriteLine($"Tracking {entityType} entity with key value {keyValue}");

                                node.Entry.State = EntityState.Modified;
                            }
                            else
                            {
                                Console.WriteLine($"Discarding duplicate {entityType} entity with key value {keyValue}");
                            }
                        });
            }

            context.SaveChanges();
        }
-->
[!code-csharp[Attaching_a_serialized_graph_5](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/SerializedGraphExamples.cs?name=Attaching_a_serialized_graph_5)]

グラフ内の各エンティティに対して、次のコードが使用されます。

- エンティティのエンティティ型とキー値を検索する
- 変更トラッカーでこのキーを使用してエンティティを参照します
  - エンティティが見つかった場合は、エンティティが重複しているため、それ以上のアクションは実行されません。
  - エンティティが見つからない場合は、状態をに設定して追跡します。 `Modified`

このコードを実行した場合の出力は次のとおりです。

```output
Tracking EntityType: Post entity with key value 1
Tracking EntityType: Blog entity with key value 1
Tracking EntityType: Post entity with key value 2
Discarding duplicate EntityType: Post entity with key value 2
Tracking EntityType: Post entity with key value 3
Tracking EntityType: Blog entity with key value 2
Tracking EntityType: Post entity with key value 4
Discarding duplicate EntityType: Post entity with key value 4
```

> [!IMPORTANT]
> このコード **は、すべての重複が同一であることを前提として** います。 これにより、他のものを破棄しながら、任意の複製を追跡することが安全です。 重複が異なる場合は、コードで使用するものを決定する方法、およびプロパティとナビゲーション値を組み合わせて使用する方法を決定する必要があります。

> [!NOTE]
> わかりやすくするために、このコードでは、各エンティティにという主キープロパティがあることを前提としてい `Id` ます。 これは、抽象基本クラスまたはインターフェイスに体系化ことができます。 または、 <xref:Microsoft.EntityFrameworkCore.Metadata.IEntityType> このコードがあらゆる種類のエンティティで動作するように、メタデータから主キーのプロパティを取得することもできます。

## <a name="failing-to-set-key-values"></a>キー値の設定に失敗する

エンティティ型は、多くの場合、 [自動的に生成されたキー値](xref:core/modeling/generated-properties)を使用するように構成されます。 これは、非複合キーの整数および GUID プロパティの既定値です。 ただし、エンティティ型が自動生成されたキー値を使用するように構成されていない場合は、エンティティを追跡する前に明示的なキー値を設定する必要があります。 たとえば、次のエンティティ型を使用します。

<!--
    public class Pet
    {
        [DatabaseGenerated(DatabaseGeneratedOption.None)]
        public int Id { get; set; }

        public string Name { get; set; }
    }
-->
[!code-csharp[Pet](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Pet)]

キー値を設定せずに2つの新しいエンティティインスタンスを追跡するコードを考えてみます。

<!--
            using var context = new BlogsContext();

            context.Add(new Pet { Name = "Smokey" });

            try
            {
                context.Add(new Pet { Name = "Clippy" }); // This will throw
            }
            catch (Exception e)
            {
                Console.WriteLine(e);
            }
-->
[!code-csharp[Failing_to_set_key_values_1](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=Failing_to_set_key_values_1)]

このコードでは、次のことがスローされます。

> InvalidOperationException: キー値 ' {Id: 0} ' を持つ別のインスタンスが既に追跡されているため、エンティティ型 ' Pet ' のインスタンスを追跡できません。 既存のエンティティをアタッチする場合は、特定のキー値を持つ1つのエンティティインスタンスのみがアタッチされていることを確認します。

この問題を解決するには、キー値を明示的に設定するか、生成されたキー値を使用するようにキープロパティを構成します。 詳細については、「 [生成される値](xref:core/modeling/generated-properties) 」を参照してください。

## <a name="overusing-a-single-dbcontext-instance"></a>単一の DbContext インスタンスの乱用

<xref:Microsoft.EntityFrameworkCore.DbContext> は、 [Dbcontext の初期化と構成](xref:core/dbcontext-configuration/index)に関するページで説明されているように、有効期間の短い作業単位を表すように設計されており、 [EF Core の Change Tracking](xref:core/change-tracking/index)で詳しく説明しています。 このガイダンスに従っていないと、同じエンティティの複数のインスタンスを追跡しようとした場合に、簡単に実行できるようになります。 一般的な例を次に示します。

- 同じ DbContext インスタンスを使用して、テスト状態を設定し、テストを実行します。 これにより、多くの場合、DbContext はテストセットアップから1つのエンティティインスタンスを追跡し、次にテストで新しいインスタンスを適切にアタッチしようとします。 代わりに、別の DbContext インスタンスを使用して、テストの状態とテストコードを適切に設定します。
- リポジトリまたは同様のコードでの共有 DbContext インスタンスの使用。 代わりに、各作業単位に対して1つの DbContext インスタンスを使用していることを確認してください。

## <a name="identity-resolution-and-queries"></a>Id の解決とクエリ

Id 解決は、エンティティがクエリから追跡されるときに自動的に行われます。 これは、特定のキー値を持つエンティティインスタンスが既に追跡されている場合に、新しいインスタンスを作成する代わりに、この既存の追跡対象インスタンスが使用されることを意味します。 これには重要な結果があります。データベースでデータが変更された場合、クエリの結果には反映されません。 これは、「 [Dbcontext の初期化と構成](xref:core/dbcontext-configuration/index)」で説明されているように、作業単位ごとに新しい dbcontext インスタンスを使用し、 [EF Core の Change Tracking](xref:core/change-tracking/index)で詳細を使用する理由です。

> [!IMPORTANT]
> EF Core は常にデータベースに対して DbSet に対して LINQ クエリを実行し、データベースの内容に基づいてのみ結果を返すことを理解しておくことが重要です。 ただし、追跡クエリの場合、返されたエンティティが既に追跡されている場合は、データベース内のデータからインスタンスを作成する代わりに、追跡対象のインスタンスが使用されます。

<xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.Reload> またはは、追跡対象の <xref:Microsoft.EntityFrameworkCore.ChangeTracking.EntityEntry.GetDatabaseValues> エンティティをデータベースの最新データで更新する必要がある場合に使用できます。 詳細については、「 [追跡対象エンティティへのアクセス](xref:core/change-tracking/entity-entries) 」を参照してください。

クエリの追跡とは異なり、追跡しないクエリでは、id 解決は実行されません。 これは、前に説明した JSON シリアル化の場合と同じように、追跡なしのクエリが重複を返すことができることを意味します。 これは、クエリ結果をシリアル化してクライアントに送信する場合、通常は問題ではありません。

> [!TIP]
> 定期的に追跡しないクエリを実行し、返されたエンティティを同じコンテキストにアタッチすることは避けてください。 これは、追跡クエリを使用するよりも処理速度が遅く、困難になることがあります。

追跡なしのクエリは、クエリから多数のエンティティをストリーミングする場合のパフォーマンスに影響するため、id 解決を実行しません。 これは、id 解決によって返される各インスタンスを追跡し、後で複製を作成するのではなく使用できるようにするためです。

EF Core 5.0 以降では、を使用して、追跡なしのクエリで id 解決を強制的に実行でき <xref:Microsoft.EntityFrameworkCore.EntityFrameworkQueryableExtensions.AsNoTrackingWithIdentityResolution%60%601(System.Linq.IQueryable{%60%600})> ます。 次に、返されたインスタンス (通常の方法では追跡せずに) を追跡し、クエリ結果に重複が作成されないようにします。

## <a name="overriding-object-equality"></a>オーバーライド (オブジェクトの等価性を)

EF Core は、エンティティインスタンスを比較するときに [参照の等価性](/dotnet/csharp/programming-guide/statements-expressions-operators/equality-comparisons) を使用します。 これは、エンティティ型がオーバーライドする場合や、オブジェクトの等価性を変更する場合にも当てはまり <xref:System.Object.Equals(System.Object)?displayProperty=nameWithType> ます。 ただし、等価性のオーバーライドが EF Core の動作に影響を与える可能性があります。コレクションのナビゲーションでは、参照の等価性ではなくオーバーライドされた等値が使用され、そのために複数のインスタンスが同じものとしてレポートされます。

このため、エンティティの等価性を上書きしないようにすることをお勧めします。 使用する場合は、参照の等価性を強制するコレクションナビゲーションを作成してください。 たとえば、参照の等価性を使用する等値比較子を作成します。

<!--
    public sealed class ReferenceEqualityComparer : IEqualityComparer<object>
    {
        private ReferenceEqualityComparer()
        {
        }

        public static ReferenceEqualityComparer Instance { get; } = new ReferenceEqualityComparer();

        bool IEqualityComparer<object>.Equals(object x, object y) => x == y;

        int IEqualityComparer<object>.GetHashCode(object obj) => RuntimeHelpers.GetHashCode(obj);
    }
-->
[!code-csharp[ReferenceEqualityComparer](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/ReferenceEqualityComparer.cs?name=ReferenceEqualityComparer)]

(.NET 5 以降では、これはとして BCL に含まれてい <xref:System.Collections.Generic.ReferenceEqualityComparer> ます)。

この比較子は、コレクションナビゲーションを作成するときに使用できます。 例:

<!--
        public ICollection<Order> Orders { get; set; }
            = new HashSet<Order>(ReferenceEqualityComparer.Instance);
-->
[!code-csharp[OrdersCollection](../../../samples/core/ChangeTracking/IdentityResolutionInEFCore/IdentityResolutionSamples.cs?name=OrdersCollection)]

### <a name="comparing-key-properties"></a>キープロパティの比較

等価比較に加えて、キー値も順序付けする必要があります。 これは、SaveChanges の1回の呼び出しで複数のエンティティを更新するときに、デッドロックを回避するために重要です。 プライマリ、代替、または外部キーのプロパティ、および一意のインデックスに使用されるすべての型は、およびを実装する必要があり <xref:System.IComparable%601> <xref:System.IEquatable%601> ます。 通常、キーとして使用される型 (int、Guid、文字列など) は、これらのインターフェイスを既にサポートしています。 カスタムキーの種類では、これらのインターフェイスを追加できます。
