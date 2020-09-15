---
title: 事前に生成されたマッピングビュー-EF6
description: Entity Framework 6 で事前に生成されたマッピングビュー
author: divega
ms.date: 10/23/2016
uid: ef6/fundamentals/performance/pre-generated-views
ms.openlocfilehash: b69c1eea1e219d76a8de1b2cf3b88de2cf74c8f0
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2020
ms.locfileid: "90073553"
---
# <a name="pre-generated-mapping-views"></a>事前に生成されたマッピングビュー
Entity Framework がクエリを実行したり、データソースへの変更を保存したりする前に、データベースにアクセスするための一連のマッピングビューを生成する必要があります。 これらのマッピングビューは、データベースを抽象的な方法で表す一連の Entity SQL ステートメントであり、アプリケーションドメインごとにキャッシュされるメタデータの一部です。 同じアプリケーションドメイン内に同じコンテキストの複数のインスタンスを作成すると、キャッシュされたメタデータからのマッピングビューが再生成されるのではなく再利用されます。 マッピングビューの生成は最初のクエリの実行にかかる全体的なコストの重要な部分であるため、Entity Framework を使用すると、マッピングビューを事前に生成し、コンパイル済みのプロジェクトに含めることができます。詳細については、「  [パフォーマンスに関する考慮事項 (Entity Framework)](xref:ef6/fundamentals/performance/perf-whitepaper)」を参照してください。

## <a name="generating-mapping-views-with-the-ef-power-tools-community-edition"></a>EF Power Tools Community Edition を使用したマッピングビューの生成

ビューを事前に生成する最も簡単な方法は、 [EF Power Tools Community エディション](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition)を使用することです。 パワーツールをインストールすると、次のように、ビューを生成するメニューオプションが表示されます。

-   **Code First**モデルの場合は、dbcontext クラスを含むコードファイルを右クリックします。
-   **EF デザイナー**モデルの場合は、EDMX ファイルを右クリックします。

![ビューの生成](~/ef6/media/generateviews.png)

プロセスが完了すると、次のようなクラスが生成されます。

![生成されたビュー](~/ef6/media/generatedviews.png)

アプリケーションを実行すると、EF はこのクラスを使用して必要に応じてビューを読み込みます。 モデルが変更され、このクラスを再生成していない場合、EF は例外をスローします。

## <a name="generating-mapping-views-from-code---ef6-onwards"></a>コード EF6 からのマッピングビューの生成

ビューを生成するもう1つの方法は、EF に用意されている Api を使用することです。 この方法を使用する場合は、必要に応じてビューを自由にシリアル化できますが、自分でビューを読み込む必要もあります。

> [!NOTE]
> **EF6 以降のみ** -このセクションに示されている api は Entity Framework 6 で導入されました。 以前のバージョンを使用している場合、この情報は適用されません。

### <a name="generating-views"></a>ビューの生成

ビューを生成するための Api は、StorageMappingItemCollection クラスに表示されます。 ObjectContext の MetadataWorkspace を使用して、コンテキストの StorageMappingCollection を取得できます。 新しい DbContext API を使用している場合は、次のような IObjectContextAdapter を使用してこれにアクセスできます。このコードでは、dbContext という名前の派生 DbContext のインスタンスを使用しています。

``` csharp
    var objectContext = ((IObjectContextAdapter) dbContext).ObjectContext;
    var  mappingCollection = (StorageMappingItemCollection)objectContext.MetadataWorkspace
                                                                        .GetItemCollection(DataSpace.CSSpace);
```

StorageMappingItemCollection を取得すると、GenerateViews と ComputeMappingHashValue メソッドにアクセスできるようになります。

``` csharp
    public Dictionary\<EntitySetBase, DbMappingView> GenerateViews(IList<EdmSchemaError> errors)
    public string ComputeMappingHashValue()
```

最初のメソッドは、コンテナーマッピング内の各ビューのエントリを含むディクショナリを作成します。 2番目のメソッドは、1つのコンテナーマッピングのハッシュ値を計算し、ビューが事前に生成された後にモデルが変更されていないことを検証するために実行時に使用されます。 複数のコンテナーマッピングが関係する複雑なシナリオでは、2つのメソッドのオーバーライドが提供されます。

ビューを生成するときに、GenerateViews メソッドを呼び出し、結果として得られた EntitySetBase および DbMappingView を記述します。 また、ComputeMappingHashValue メソッドによって生成されたハッシュも格納する必要があります。

### <a name="loading-views"></a>ビューの読み込み

GenerateViews メソッドによって生成されたビューを読み込むには、DbMappingViewCache 抽象クラスを継承するクラスを EF に提供します。 DbMappingViewCache は、実装する必要がある2つのメソッドを指定します。

``` csharp
    public abstract string MappingHashValue { get; }
    public abstract DbMappingView GetView(EntitySetBase extent);
```

MappingHashValue プロパティは、ComputeMappingHashValue メソッドによって生成されたハッシュを返す必要があります。 EF がビューを要求するときに、最初に生成され、このプロパティによって返されるハッシュとモデルのハッシュ値を比較します。 一致しない場合、EF は EntityCommandCompilationException 例外をスローします。

GetView メソッドは EntitySetBase を受け入れ、GenerateViews メソッドによって生成されたディクショナリ内の指定された EntitySetBase に関連付けられていた EntitySql を含む DbMappingVIew を返す必要があります。 EF が不要なビューを要求した場合、GetView は null を返す必要があります。

次に示すのは、前述のようにパワーツールで生成された DbMappingViewCache からの抽出です。ここでは、必要な EntitySql を格納および取得するための1つの方法を示しています。

``` csharp
    public override string MappingHashValue
    {
        get { return "a0b843f03dd29abee99789e190a6fb70ce8e93dc97945d437d9a58fb8e2afd2e"; }
    }

    public override DbMappingView GetView(EntitySetBase extent)
    {
        if (extent == null)
        {
            throw new ArgumentNullException("extent");
        }

        var extentName = extent.EntityContainer.Name + "." + extent.Name;

        if (extentName == "BlogContext.Blogs")
        {
            return GetView2();
        }

        if (extentName == "BlogContext.Posts")
        {
            return GetView3();
        }

        return null;
    }

    private static DbMappingView GetView2()
    {
        return new DbMappingView(@"
            SELECT VALUE -- Constructing Blogs
            [BlogApp.Models.Blog](T1.Blog_BlogId, T1.Blog_Test, T1.Blog_title, T1.Blog_Active, T1.Blog_SomeDecimal)
            FROM (
            SELECT
                T.BlogId AS Blog_BlogId,
                T.Test AS Blog_Test,
                T.title AS Blog_title,
                T.Active AS Blog_Active,
                T.SomeDecimal AS Blog_SomeDecimal,
                True AS _from0
            FROM CodeFirstDatabase.Blog AS T
            ) AS T1");
    }
```

EF で DbMappingViewCache を使用するには、DbMappingViewCacheTypeAttribute を使用して、作成されたコンテキストを指定します。 次のコードでは、ブログコンテキストを MyMappingViewCache クラスに関連付けます。

``` csharp
    [assembly: DbMappingViewCacheType(typeof(BlogContext), typeof(MyMappingViewCache))]
```

より複雑なシナリオでは、マッピングビューのキャッシュファクトリを指定することによって、マッピングビューのキャッシュインスタンスを指定できます。 これは、抽象クラスの system.object を実装することによって行うことができます。。 使用されるマッピングビューのキャッシュファクトリのインスタンスは、StorageMappingItemCollection. MappingViewCacheFactoryproperty を使用して取得または設定できます。
