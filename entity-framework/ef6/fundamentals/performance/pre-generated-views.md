---
title: 事前に生成されたマッピング ビュー - EF6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: 917ba9c8-6ddf-4631-ab8c-c4fb378c2fcd
caps.latest.revision: 3
ms.openlocfilehash: 9e74176d02afc424118219eec8e016843333cbb8
ms.sourcegitcommit: 390f3a37bc55105ed7cc5b0e0925b7f9c9e80ba6
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2018
ms.locfileid: "39122588"
---
# <a name="pre-generated-mapping-views"></a>事前に生成されたマッピング ビュー
Entity Framework では、クエリを実行したり、データ ソースに変更を保存することが、前に、一連のデータベースにアクセスするマッピング ビューが生成する必要があります。 これらのマッピング ビューを抽象的な方法でデータベースを表す Entity SQL ステートメントのセットし、アプリケーション ドメインごとにキャッシュされるメタデータの一部であります。 同じアプリケーション ドメインで同じコンテキストの複数のインスタンスを作成する場合は、再生成するのではなく、キャッシュされたメタデータからマッピング ビューは再利用します。 マップ ビューの生成は、最初のクエリの実行における全体的なコストのかなりの部分であるため、Entity Framework では、マッピング ビューを事前に生成およびコンパイル済みのプロジェクトに追加することができます。 詳細については、次を参照してください。[パフォーマンスに関する考慮事項 (Entity Framework)](~/ef6/fundamentals/performance/perf-whitepaper.md)します。

## <a name="generating-mapping-views-with-the-ef-power-tools"></a>EF Power Tools でのビューのマッピングを生成します。

ビューを事前に生成する最も簡単な方法は使用する、 [EF Power Tools](http://visualstudiogallery.msdn.microsoft.com/72a60b14-1581-4b9b-89f2-846072eff19d)します。 Power ツールをインストールしたら次に示すようにビューの生成のメニュー オプションを必要があります。

-   **Code First** DbContext クラスを含むコード ファイルにモデルを右クリックします。
-   **EF Designer**モデルは、EDMX ファイルを右クリックします。

![generateViews](~/ef6/media/generateviews.png)

生成された次のようなクラスが、プロセスが完了すると

![generatedViews](~/ef6/media/generatedviews.png)

これを実行すると、アプリケーションの EF は必要に応じてビューの読み込みにこのクラスを使用します。 モデルの変更と再生成しないこのクラスの場合、EF によって例外がスローされます。

## <a name="generating-mapping-views-from-code---ef6-onwards"></a>コードの EF6 以降からビューのマッピングを生成します。

ビューを生成するその他の方法は、EF を提供する Api を使用することです。 このメソッドを使用する場合が、自分でビューの読み込みにも必要ですが、ビューをシリアル化する自由があります。

> [!NOTE]
> **EF6 以降のみ**-このセクションに示すように、Api は、Entity Framework 6 で導入されました。 以前のバージョンを使用している場合、この情報は適用されません。

### <a name="generating-views"></a>ビューの生成

ビューを生成する Api は、System.Data.Entity.Core.Mapping.StorageMappingItemCollection クラスです。 ObjectContext の MetadataWorkspace を使用して、コンテキストに対して、StorageMappingCollection を取得できます。 使用してこれにアクセスできるように、新しい DbContext API を使用している場合は、次のような IObjectContextAdapter、このコードであるという dbContext 派生、DbContext のインスタンス。

``` csharp
    var objectContext = ((IObjectContextAdapter) dbContext).ObjectContext;
    var  mappingCollection = (StorageMappingItemCollection)objectContext.MetadataWorkspace
                                                                        .GetItemCollection(DataSpace.CSSpace);
```

StorageMappingItemCollection したら GenerateViews と ComputeMappingHashValue メソッドへのアクセスを取得できます。

``` csharp
    public Dictionary\<EntitySetBase, DbMappingView> GenerateViews(IList<EdmSchemaError> errors)
    public string ComputeMappingHashValue()
```

最初のメソッドでは、コンテナーのマッピングでは、各ビューのエントリを使って、ディクショナリを作成します。 2 番目のメソッドでは、1 つのコンテナーのマッピングのハッシュ値を計算し、実行時の検証に使用、モデルが、ビューが事前に生成した後に変更していないこと。 2 つのメソッドのオーバーライドは、複数のコンテナーのマッピングに関連する複雑なシナリオで提供されます。

ビューを生成するときに GenerateViews メソッドを呼び出すし、結果として得られる EntitySetBase と DbMappingView を記述します。 また、ComputeMappingHashValue メソッドによって生成されたハッシュを格納する必要があります。

### <a name="loading-views"></a>ビューの読み込み

GenerateViews メソッドによって生成されるビューを読み込むためには、DbMappingViewCache 抽象クラスから継承するクラスを使用して EF を行うことができます。 DbMappingViewCache は、2 つのメソッドを実装する必要がありますを指定します。

``` csharp
    public abstract string MappingHashValue { get; }
    public abstract DbMappingView GetView(EntitySetBase extent);
```

MappingHashValue プロパティには、ComputeMappingHashValue メソッドによって生成されたハッシュを返す必要があります。 EF がビューの確認にはまず生成し、モデルのハッシュ値をこのプロパティによって返されるハッシュと比較します。 これらが一致しない場合は、EF は EntityCommandCompilationException 例外をスローします。

GetView メソッドで、EntitySetBase はそのまま使用し、GenerateViews メソッドによって生成されたディクショナリで指定された EntitySetBase に関連付けられていた解析対象の EntitySql が生成する対象を含む DbMappingVIew を返す必要があります。 EF を求める場合ことがない、GetView ビューは null を返します。

格納および取得のために必要な解析対象の EntitySql 1 つの方法がわかりますが、上記とパワー ツールで生成される DbMappingViewCache からの抜粋を次に示します。

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

EF に使用しているには、追加する、DbMappingViewCache は用に作成されたコンテキストの指定、DbMappingViewCacheTypeAttribute を使用します。 次のコードで MyMappingViewCache クラスを使用して、BlogContext を関連付けます。

``` csharp
    [assembly: DbMappingViewCacheType(typeof(BlogContext), typeof(MyMappingViewCache))]
```

複雑なシナリオは、マッピング ビュー キャッシュ ファクトリを指定することによってマッピング キャッシュ インスタンスの表示を提供することができます。 これは、抽象クラス System.Data.Entity.Infrastructure.MappingViews.DbMappingViewCacheFactory を実装することで実行できます。 使用されるマッピング ビュー キャッシュ ファクトリのインスタンスを取得または、StorageMappingItemCollection.MappingViewCacheFactoryproperty を使用して設定できます。
