---
title: カスタム コードの最初規則 - EF6
author: divega
ms.date: 2016-10-23
ms.assetid: dd2bdbd9-ae9e-470a-aeb8-d0ba160499b7
ms.openlocfilehash: a0e8080037cf86640275f498ed159c847ff5c057
ms.sourcegitcommit: 0d36e8ff0892b7f034b765b15e041f375f88579a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2018
ms.locfileid: "44251064"
---
# <a name="custom-code-first-conventions"></a>カスタム コードの最初の規則
> [!NOTE]
> **EF6 以降のみ** - このページで説明する機能、API などは、Entity Framework 6 で導入されました。 以前のバージョンを使用している場合、一部またはすべての情報は適用されません。

Code First を使用する場合、モデルは、一連の規則を使用して、クラスから計算されます。 既定の[コードの最初の規則](~/ef6/modeling/code-first/conventions/built-in.md)プロパティがエンティティの主キー、エンティティのマッピング テーブルと 10 進数の列が既定ではどのような有効桁数と小数点の名前になりますこのようなことを確認します。

場合によってこれらの既定の規則が、モデルの理想的なと、データ注釈または Fluent API を使用して多くの個々 のエンティティを構成することによって、回避する必要があります。 カスタム コード最初の規則を使用して、モデルの構成の既定値を提供する独自の規則を定義できます。 このチュートリアルでは、カスタムの規則およびそれらを作成する方法のさまざまな種類について説明します。


## <a name="model-based-conventions"></a>モデルに基づく規則

このページでは、カスタムの規則の DbModelBuilder API について説明します。 この API は、ほとんどのカスタム規則を作成するための十分な必要があります。 ただし、モデルに基づく規則 - 作成した後、最終的なモデルを操作する規則を作成する - 高度なシナリオを処理する機能も。 詳細については、次を参照してください。[モデルに基づく規則](~/ef6/modeling/code-first/conventions/model.md)します。

 

## <a name="our-model"></a>私たちのモデル

規約を使用できる単純なモデルを定義してみましょう。 次のクラスをプロジェクトに追加します。

``` csharp
    using System;
    using System.Collections.Generic;
    using System.Data.Entity;
    using System.Linq;

    public class ProductContext : DbContext
    {
        static ProductContext()
        {
            Database.SetInitializer(new DropCreateDatabaseIfModelChanges<ProductContext>());
        }

        public DbSet<Product> Products { get; set; }
    }

    public class Product
    {
        public int Key { get; set; }
        public string Name { get; set; }
        public decimal? Price { get; set; }
        public DateTime? ReleaseDate { get; set; }
        public ProductCategory Category { get; set; }
    }

    public class ProductCategory
    {
        public int Key { get; set; }
        public string Name { get; set; }
        public List<Product> Products { get; set; }
    }
```

 

## <a name="introducing-custom-conventions"></a>カスタム規則の概要

名前付きエンティティの種類の主キーのキーのプロパティを構成する規則を記述してみましょう。

コンテキストで OnModelCreating オーバーライドすることでアクセスできるモデル ビルダーでは、規則が有効にします。 ProductContext クラスを次のように更新するには。

``` csharp
    public class ProductContext : DbContext
    {
        static ProductContext()
        {
            Database.SetInitializer(new DropCreateDatabaseIfModelChanges<ProductContext>());
        }

        public DbSet<Product> Products { get; set; }

        protected override void OnModelCreating(DbModelBuilder modelBuilder)
        {
            modelBuilder.Properties()
                        .Where(p => p.Name == "Key")
                        .Configure(p => p.IsKey());
        }
    }
```

ここで、名前付きキー、モデル内の任意のプロパティになりますが部分的にどのようなエンティティの主キーとして構成されています。

できるようにも紹介規約より具体的フィルタ処理を構成するプロパティの型。

``` csharp
    modelBuilder.Properties<int>()
                .Where(p => p.Name == "Key")
                .Configure(p => p.IsKey());
```

これは、そのエンティティのキーが整数である場合にのみに、プライマリ キーと呼ばれるすべてのプロパティで構成されます。

IsKey メソッドの面白い機能がある加法です。 つまり複数のプロパティで IsKey を呼び出すし、すべてになる複合キーの一部です。 この 1 つ注意点は、キーの複数のプロパティを指定するときにこれらのプロパティの順序を指定もする必要があります。 HasColumnOrder の次のようなメソッドを呼び出すことによって、これを行うことができます。

``` csharp
    modelBuilder.Properties<int>()
                .Where(x => x.Name == "Key")
                .Configure(x => x.IsKey().HasColumnOrder(1));

    modelBuilder.Properties()
                .Where(x => x.Name == "Name")
                .Configure(x => x.IsKey().HasColumnOrder(2));
```

このコードは int 型のキー列と文字列の列で構成される複合キーにするには、このモデルの種類を構成します。 場合は、モデル デザイナーの表示次のようになります。

![複合キー](~/ef6/media/compositekey.png)

規則のプロパティの別の例では、datetime ではなく、SQL server datetime2 型にマップする、モデルのすべての DateTime プロパティを構成します。 次のようにこれを実現できます。

``` csharp
    modelBuilder.Properties<DateTime>()
                .Configure(c => c.HasColumnType("datetime2"));
```

 

## <a name="convention-classes"></a>規則クラス

規則を定義する別の方法では、規則クラスを使用して、規則をカプセル化します。 規則クラスを使用する場合を System.Data.Entity.ModelConfiguration.Conventions 名前空間で規則クラスから継承する型を作成します。

次の手順で先ほどお見せした datetime2 規則と規則クラスを作成できます。

``` csharp
    public class DateTime2Convention : Convention
    {
        public DateTime2Convention()
        {
            this.Properties<DateTime>()
                .Configure(c => c.HasColumnType("datetime2"));        
        }
    }
```

この規則を使用する EF に指示するには、OnModelCreating チュートリアルと共に従っている場合は、次のように次の規則コレクションに追加します。

``` csharp
    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        modelBuilder.Properties<int>()
                    .Where(p => p.Name.EndsWith("Key"))
                    .Configure(p => p.IsKey());

        modelBuilder.Conventions.Add(new DateTime2Convention());
    }
```

ご覧のとおり、規則のインスタンスを規則コレクションに追加します。 規則から継承するには、グループ化して、チームまたはプロジェクト全体で規則を共有の便利な方法が提供します。 組織のすべてのプロジェクトを使用する規則の共通セットを使用したクラス ライブラリなどがあることができます。

 

## <a name="custom-attributes"></a>カスタム属性

規則のもう 1 つの優れた使用モデルを構成するときに使用される新しい属性を有効にすることです。 これを説明するには、文字列プロパティを Unicode 以外としてマークするために使用できる属性を作成しましょう。

``` csharp
    [AttributeUsage(AttributeTargets.Property, AllowMultiple = false)]
    public class NonUnicode : Attribute
    {
    }
```

次に、私たちのモデルにこの属性を適用する規則を作成してみましょう。

``` csharp
    modelBuilder.Properties()
                .Where(x => x.GetCustomAttributes(false).OfType<NonUnicode>().Any())
                .Configure(c => c.IsUnicode(false));
```

この規則で、データベース内の列には、文字列プロパティのいずれかに NonUnicode 属性が nvarchar ではなく varchar として格納されるを追加できます。

この規則について注意が必要ですをされている文字列のプロパティ以外のものでは例外をスローし、NonUnicode 属性を配置する場合。 これは、文字列以外の任意の型で IsUnicode を構成することはできませんので。 この場合、し、行うことができます、規則具体的に文字列のないものはすべてを除外できるようにします。

カスタム属性を定義するために、上記の規則が動作しますが、別の API を使用する属性クラスのプロパティを使用する場合に特ににはるかに簡単にできます。

この例で、属性を更新し、IsUnicode 属性を次のように変更するつもりは。

``` csharp
    [AttributeUsage(AttributeTargets.Property, AllowMultiple = false)]
    internal class IsUnicode : Attribute
    {
        public bool Unicode { get; set; }

        public IsUnicode(bool isUnicode)
        {
            Unicode = isUnicode;
        }
    }
```

このしたら、規約、プロパティは Unicode を指定するかどうかを指示する、属性にブール値を設定できます。 既にこのような構成クラスの ClrProperty にアクセスすることがある規則で実行できます。

``` csharp
    modelBuilder.Properties()
                .Where(x => x.GetCustomAttributes(false).OfType<IsUnicode>().Any())
                .Configure(c => c.IsUnicode(c.ClrPropertyInfo.GetCustomAttribute<IsUnicode>().Unicode));
```

これは非常に簡単ですが、Having を使用してこれを実現するためのより簡潔な方法がある規則の API のメソッド。 Func を入力メソッドのパラメーターには Having&lt;PropertyInfo、T&gt; Where と同じ PropertyInfo を受け入れるメソッド、オブジェクトを取得する必要があります。 プロパティが構成されていないに返されるオブジェクトが null の場合は、Where と同じようにプロパティをフィルターすることができることができますが異なる場合も、返されたオブジェクトをキャプチャし、Configure メソッドに渡すことで。 これは、次のように動作します。

``` csharp
    modelBuilder.Properties()
                .Having(x =>x.GetCustomAttributes(false).OfType<IsUnicode>().FirstOrDefault())
                .Configure((config, att) => config.IsUnicode(att.Unicode));
```

カスタム属性は、Having を使用する唯一の理由ではないメソッド、便利だと、型またはプロパティを構成するときにフィルター処理をすることを判断する必要がある任意の場所。

 

## <a name="configuring-types"></a>種類の構成

これまでに、プロパティについては、すべての規約されているが、モデルの種類を構成するための API 規約の別の領域があります。 これまで行ってきた規則に似ていますが、内側の構成オプションがレベルでプロパティの代わりに、エンティティになります。

テーブル名前付け規則、既存の EF 既定とは異なるスキーマにマップするか、別の名前付け規則で新しいデータベースを作成する型レベルの規則は本当に役に立つことの 1 つは変更します。 これを行うことが、モデル内の型の TypeInfo をそのまま使用でき、その型のテーブル名にする必要がありますを返すメソッドがまず必要。

``` csharp
    private string GetTableName(Type type)
    {
        var result = Regex.Replace(type.Name, ".[A-Z]", m => m.Value[0] + "_" + m.Value[1]);

        return result.ToLower();
    }
```

このメソッドは、型を受け取り、キャメル ケースではなくアンダー スコアと小文字を使用する文字列を返します。 ProductCategory クラスを product テーブルにマップすることを意味これモデル\_ProductCategories ではなくカテゴリ。

そのメソッドを取得したら、次のように、規則で呼び出すできます。

``` csharp
    modelBuilder.Types()
                .Configure(c => c.ToTable(GetTableName(c.ClrType)));
```

この規則は、GetTableName メソッドから返されるテーブル名にマップするには、このモデルですべての種類を構成します。 この規則は、Fluent API を使用して、モデル内の各エンティティの ToTable メソッドの呼び出しと同等です。

これについて注意が必要ですは、呼び出すときに ToTable EF はテーブル名を決定するときに通常行うようの複数形化せず、正確なテーブル名として指定する文字列を使用するはことです。 これは、規則内のテーブル名が製品である理由\_製品ではなくカテゴリ\_カテゴリ。 解決できる規則では自分たちの複数形化サービスへの呼び出しを行います。

次のコードでは、使用、[依存関係の解決](~/ef6/fundamentals/configuring/dependency-resolution.md)EF で使用する複数形化サービスを取得するように EF6 で追加された機能、テーブル名を複数形にするとします。

``` csharp
    private string GetTableName(Type type)
    {
        var pluralizationService = DbConfiguration.DependencyResolver.GetService<IPluralizationService>();

        var result = pluralizationService.Pluralize(type.Name);

        result = Regex.Replace(result, ".[A-Z]", m => m.Value[0] + "_" + m.Value[1]);

        return result.ToLower();
    }
```

> [!NOTE]
> GetService のジェネリック バージョン System.Data.Entity.Infrastructure.DependencyResolution 名前空間での拡張メソッドを使用して、追加する必要がありますを使用するには、コンテキストにステートメント。

### <a name="totable-and-inheritance"></a>ToTable と継承

ToTable のもう 1 つの重要な側面されている場合に、特定のテーブル型を明示的にマップし、EF を使用するマッピング方法を変更することができます。 ToTable 継承階層内のすべての型に対して呼び出すことが場合、上記のテーブルの名前として、型名を渡すことは変更する既定 - Table-per-hierarchy (TPH) のマッピング方法 Table-type ごと (TPT)。 これを記述する最善の方法では、それぞれ具体的な例です。

``` csharp
    public class Employee
    {
        public int Id { get; set; }
        public string Name { get; set; }
    }

    public class Manager : Employee
    {
        public string SectionManaged { get; set; }
    }
```

既定では、従業員とマネージャーの両方がデータベース内の同じテーブル (従業員) にマップされます。 テーブルには、従業員とマネージャー インスタンスの種類が各行に格納されているかを示す識別子列の両方が含まれます。 これは、階層の 1 つのテーブルがある TPH マッピングです。 ただし、両方 classe で ToTable を呼び出す場合、各型は代わりにするテーブルにマップ独自、TPT ため、種類ごとに独自のテーブルとも呼ばれますの。

``` csharp
    modelBuilder.Types()
                .Configure(c=>c.ToTable(c.ClrType.Name));
```

上記のコードは、次のようなテーブル構造へマップされます。

![tpt の例](~/ef6/media/tptexample.jpg)

この問題を回避し、いくつかの方法で、既定の TPH マッピングを維持できます。

1.  階層内の各型の同じテーブル名を持つ ToTable を呼び出します。
2.  従業員がこの例では、階層の基本クラスでのみ、ToTable を呼び出します。

 

## <a name="execution-order"></a>実行順序

規則は、Fluent API と同じです。 最後の wins 方法で動作します。 つまり、同じプロパティの同じオプションを構成する 2 つの規則、wins を実行する最後の 1 つを記述する場合は、です。 例として、次のコードですべての文字列の最大長は 500 に設定が、250 の最大長を使用してモデルの名前と呼ばれるすべてのプロパティを構成します。

``` csharp
    modelBuilder.Properties<string>()
                .Configure(c => c.HasMaxLength(500));

    modelBuilder.Properties<string>()
                .Where(x => x.Name == "Name")
                .Configure(c => c.HasMaxLength(250));
```

最大長は 250 に設定する規約では、すべての文字列を 500 に設定した後であるため、モデル名と呼ばれるすべてのプロパティが、最大長 250 の説明など、他の任意の文字列の中に、500 になります。 型またはモデルと、オーバーライドでプロパティの一般的な規則を提供できることを意味する規則を使用して、この方法では、さまざまなサブセットのことです。

データ注釈と Fluent API は、特定のケースで、規則を上書きすることも使用できます。 上記の例でプロパティの最大長を設定するのには、Fluent API を使用した場合が置くことが、規則の前後にためより具体的に Fluent API 経由での一般的な構成規則が優先されます。

 

## <a name="built-in-conventions"></a>組み込みの規約

カスタムの規則は、既定の Code First 規約によって受ける可能性があります、ために、別の規則の前後に実行するために規則を追加すると便利ができます。 これを行うには、派生、DbContext で規則のコレクションのよび AddBefore と addafter ですメソッドを使用することができます。 次のコードは、構築する前に、実行するために作成した規則クラスを追加、キーの検出規則にします。

``` csharp
    modelBuilder.Conventions.AddBefore<IdKeyDiscoveryConvention>(new DateTime2Convention());
```

これの最も多く使用する組み込みの規則の前後に実行する必要がある規則を追加するときに、組み込みの規則の一覧はこちら: [System.Data.Entity.ModelConfiguration.Conventions Namespace](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx).

モデルに適用しないようにする規則を削除することもできます。 規則を削除するには、Remove メソッドを使用します。 次に、PluralizingTableNameConvention を削除する例を示します。

``` csharp
    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        modelBuilder.Conventions.Remove<PluralizingTableNameConvention>();
    }
```
