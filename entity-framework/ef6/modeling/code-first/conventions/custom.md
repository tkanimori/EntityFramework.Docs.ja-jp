---
title: カスタム Code First 規則-EF6
description: Entity Framework 6 のカスタム Code First 規則
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/code-first/conventions/custom
ms.openlocfilehash: 19f22d311438b8c7d3ac9d270017f3ea4907efda
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2020
ms.locfileid: "90074030"
---
# <a name="custom-code-first-conventions"></a>カスタム Code First 規則
> [!NOTE]
> **EF6 以降のみ** - このページで説明する機能、API などは、Entity Framework 6 で導入されました。 以前のバージョンを使用している場合、一部またはすべての情報は適用されません。

Code First を使用する場合、モデルは一連の規則を使用してクラスから計算されます。 既定の [Code First 規則](xref:ef6/modeling/code-first/conventions/built-in) では、エンティティの主キーになるプロパティ、エンティティがマップされるテーブルの名前、および10進数の列の有効桁数と小数点以下桁数が既定で決まります。

これらの既定の規則は、モデルに最適ではない場合があります。また、データ注釈または Fluent API を使用して多数の個々のエンティティを構成することによって回避する必要があります。 カスタム Code First 規則を使用すると、モデルの構成の既定値を提供する独自の規則を定義できます。 このチュートリアルでは、さまざまな種類のカスタム規則と、それぞれの作成方法について説明します。


## <a name="model-based-conventions"></a>モデルベースの規則

このページでは、カスタム規則の DbModelBuilder API について説明します。 ほとんどのカスタム規則を作成するには、この API が十分である必要があります。 ただし、作成された最終的なモデルを操作し、高度なシナリオに対応するために、モデルベースの規則を作成することもできます。 詳細については、「 [モデルベースの規則](xref:ef6/modeling/code-first/conventions/model)」を参照してください。

 

## <a name="our-model"></a>Microsoft のモデル

まず、この規則で使用できる単純なモデルを定義します。 次のクラスをプロジェクトに追加します。

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

Key という名前のプロパティをエンティティ型の主キーとして構成する規則を作成してみましょう。

規則はモデルビルダーで有効になっています。これには、コンテキストで OnModelCreating をオーバーライドすることによってアクセスできます。 ProductContext クラスを次のように更新します。

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

これで、モデルの名前付きキーのすべてのプロパティが、その一部であるすべてのエンティティの主キーとして構成されます。

また、構成するプロパティの種類に基づいてフィルター処理することにより、規則をより具体的にすることもできます。

``` csharp
    modelBuilder.Properties<int>()
                .Where(p => p.Name == "Key")
                .Configure(p => p.IsKey());
```

これにより、Key という名前のすべてのプロパティが、エンティティの主キーになるように構成されますが、整数の場合に限ります。

IsKey メソッドの興味深い機能は、付加的な機能です。 つまり、複数のプロパティで IsKey を呼び出すと、すべてが複合キーの一部になります。 1つの注意点として、キーに複数のプロパティを指定する場合は、それらのプロパティの順序も指定する必要があります。 これを行うには、次のように HasColumnOrder メソッドを呼び出します。

``` csharp
    modelBuilder.Properties<int>()
                .Where(x => x.Name == "Key")
                .Configure(x => x.IsKey().HasColumnOrder(1));

    modelBuilder.Properties()
                .Where(x => x.Name == "Name")
                .Configure(x => x.IsKey().HasColumnOrder(2));
```

このコードは、int キー列と文字列名列で構成される複合キーを持つように、モデル内の型を構成します。 デザイナーでモデルを表示すると、次のようになります。

![複合キー](~/ef6/media/compositekey.png)

プロパティ規則のもう1つの例として、[モデル] のすべての DateTime プロパティを、datetime ではなく SQL Server の datetime2 型にマップするように構成します。 これを実現するには、次のようにします。

``` csharp
    modelBuilder.Properties<DateTime>()
                .Configure(c => c.HasColumnType("datetime2"));
```

 

## <a name="convention-classes"></a>規則クラス

規則を定義するもう1つの方法は、規則クラスを使用して規則をカプセル化することです。 規則クラスを使用する場合は、system.object 名前空間の規則クラスから継承する型を作成します。 "。

規則クラスは、前に示した datetime2 規則を使用して作成できます。これを行うには、次の手順を実行します。

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

この規則を使用するように EF に指示するには、この規則を OnModelCreating の規則コレクションに追加します。チュートリアルに従っている場合は、次のようになります。

``` csharp
    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        modelBuilder.Properties<int>()
                    .Where(p => p.Name.EndsWith("Key"))
                    .Configure(p => p.IsKey());

        modelBuilder.Conventions.Add(new DateTime2Convention());
    }
```

ご覧のように、規則のインスタンスを規則のコレクションに追加します。 規則から継承すると、チームまたはプロジェクト間で規則をグループ化および共有するための便利な方法が提供されます。 たとえば、組織のすべてのプロジェクトで使用される規則の共通セットを含むクラスライブラリがあるとします。

 

## <a name="custom-attributes"></a>カスタム属性

規則のもう1つの優れた使用方法は、モデルを構成するときに新しい属性を使用できるようにすることです。 これを説明するために、文字列プロパティを非 Unicode としてマークするために使用できる属性を作成してみましょう。

``` csharp
    [AttributeUsage(AttributeTargets.Property, AllowMultiple = false)]
    public class NonUnicode : Attribute
    {
    }
```

次に、この属性をモデルに適用する規則を作成してみましょう。

``` csharp
    modelBuilder.Properties()
                .Where(x => x.GetCustomAttributes(false).OfType<NonUnicode>().Any())
                .Configure(c => c.IsUnicode(false));
```

この規則では、Unicode 以外の属性を任意の文字列プロパティに追加できます。これは、データベースの列が nvarchar ではなく varchar として格納されることを意味します。

この規則について注意する必要がある点は、Unicode 以外の属性を文字列プロパティ以外に配置すると、例外がスローされることです。 これは、文字列以外の型で IsUnicode を構成できないためです。 このような場合は、文字列ではないものを除外するように規則をより具体的に設定できます。

上記の規則はカスタム属性を定義する場合に機能しますが、属性クラスのプロパティを使用する場合は特に、使用した方が簡単な API もあります。

この例では、属性を更新して IsUnicode 属性に変更します。そのため、次のようになります。

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

これが完了したら、属性にブール値を設定して、プロパティが Unicode であるかどうかを規則に伝えることができます。 これは、次のように構成クラスの ClrProperty にアクセスすることによって、既に設定されている規則で行うことができます。

``` csharp
    modelBuilder.Properties()
                .Where(x => x.GetCustomAttributes(false).OfType<IsUnicode>().Any())
                .Configure(c => c.IsUnicode(c.ClrPropertyInfo.GetCustomAttribute<IsUnicode>().Unicode));
```

これは簡単ですが、規約 API の Having メソッドを使用することによって、これをより簡潔な方法で実現できます。 Having メソッドには、Func PropertyInfo, T 型のパラメーターがあります。このパラメーターは &lt; &gt; Where メソッドと同じ PropertyInfo を受け取りますが、オブジェクトを返すことが想定されています。 返されたオブジェクトが null の場合、プロパティは構成されません。つまり、Where と同じようにプロパティをフィルターで除外することができますが、返されたオブジェクトをキャプチャして、それを Configure メソッドに渡す点も異なります。 これは次のように機能します。

``` csharp
    modelBuilder.Properties()
                .Having(x =>x.GetCustomAttributes(false).OfType<IsUnicode>().FirstOrDefault())
                .Configure((config, att) => config.IsUnicode(att.Unicode));
```

カスタム属性は、Having メソッドを使用する唯一の理由ではありません。型またはプロパティを構成するときにフィルター処理を行う必要がある場合に便利です。

 

## <a name="configuring-types"></a>構成 (型を)

ここまでのすべての規則はプロパティに対して行われていましたが、モデルの型を構成するための規則 API にはもう1つの領域があります。 ここまでに示した規則に似ていますが、構成内のオプションは、プロパティレベルではなくエンティティになります。

では、型レベルの規則が非常に役に立ちます。これは、EF の既定とは異なる既存のスキーマにマップするか、別の名前付け規則を使用して新しいデータベースを作成するために、テーブルの名前付け規則を変更することです。 これを行うには、まず、モデル内の型の TypeInfo を受け入れ、その型のテーブル名を返すメソッドが必要です。

``` csharp
    private string GetTableName(Type type)
    {
        var result = Regex.Replace(type.Name, ".[A-Z]", m => m.Value[0] + "_" + m.Value[1]);

        return result.ToLower();
    }
```

このメソッドは、型を受け取り、キャメルケースの代わりに小文字とアンダースコアを使用する文字列を返します。 このモデルでは、ProductCategory クラスは Productcategory ではなく product category というテーブルにマップされ \_ ます。

このメソッドを用意したら、次のような規則でそれを呼び出すことができます。

``` csharp
    modelBuilder.Types()
                .Configure(c => c.ToTable(GetTableName(c.ClrType)));
```

この規則により、モデル内のすべての型が、GetTableName メソッドから返されたテーブル名にマップされるように構成されます。 この規則は、Fluent API を使用してモデル内の各エンティティに対して ToTable メソッドを呼び出すことと同じです。

この点に注意する必要があるのは、ToTable EF を呼び出すと、指定した文字列が正確なテーブル名として取得され、テーブル名を決定するときに通常実行する複数形化はありません。 このため、テーブル名は、製品カテゴリでは \_ なく製品カテゴリになり \_ ます。 この規則では、複数形化サービスの呼び出しを自分で行うことによって解決できます。

次のコードでは、EF6 に追加された [依存関係の解決](xref:ef6/fundamentals/configuring/dependency-resolution) 機能を使用して、EF によって使用された複数形化サービスを取得し、テーブル名を複数化します。

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
> GetService のジェネリックバージョンは、system.string 名前空間の拡張メソッドです。使用するには、使用するために、コンテキストに using ステートメントを追加する必要があります。

### <a name="totable-and-inheritance"></a>ToTable と継承

ToTable のもう1つの重要な側面は、特定のテーブルに型を明示的にマップした場合に、EF が使用するマッピング方法を変更できることです。 継承階層内のすべての型に対して ToTable を呼び出す場合は、上記のようにテーブルの名前として型名を渡すと、既定の階層構造 (TPH) のマッピング方法が、型ごとのテーブル (TPT) に変更されます。 これを説明する最善の方法は、具体的な例を whith ことです。

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

既定では、employee と manager の両方がデータベース内の同じテーブル (Employees) にマップされます。 テーブルには、各行に格納されているインスタンスの種類を示す識別子列を持つ従業員と管理者の両方が含まれます。 これは、階層の1つのテーブルがあるため、TPH のマッピングです。 ただし、両方の classe で ToTable を呼び出す場合、それぞれの型は独自のテーブルにマップされます。各型に独自のテーブルがあるため、TPT とも呼ばれます。

``` csharp
    modelBuilder.Types()
                .Configure(c=>c.ToTable(c.ClrType.Name));
```

上記のコードは、次のようなテーブル構造にマップされます。

![tpt の例](~/ef6/media/tptexample.jpg)

これを回避し、既定の TPH マッピングを維持するには、次の2つの方法があります。

1.  階層内の型ごとに同じテーブル名を使用して ToTable を呼び出します。
2.  階層の基本クラスでのみ ToTable を呼び出します。この例では employee です。

 

## <a name="execution-order"></a>実行順序

規則は、Fluent API と同じように、最後の優先の方法で動作します。 これは、同じプロパティの同じオプションを構成する2つの規則を記述した場合、最後の規則が優先されることを意味します。 たとえば、次のコードでは、すべての文字列の最大長が500に設定されていますが、その後、モデル内の Name という名前のすべてのプロパティが250の最大長を持つように構成されています。

``` csharp
    modelBuilder.Properties<string>()
                .Configure(c => c.HasMaxLength(500));

    modelBuilder.Properties<string>()
                .Where(x => x.Name == "Name")
                .Configure(c => c.HasMaxLength(250));
```

最大長を250に設定する規則は、すべての文字列を500に設定した後になります。そのため、モデル内の Name という名前のすべてのプロパティは、MaxLength が250になり、説明などの他の文字列は500になります。 この方法で規則を使用すると、モデル内の型またはプロパティに対して一般的な規則を提供し、異なるサブセットをオーバーライドできます。

特定のケースでは、Fluent API とデータ注釈を使用して規則をオーバーライドすることもできます。 上記の例では、Fluent API を使用してプロパティの最大長を設定していた場合、規則の前または後に配置することができます。これは、より具体的な Fluent API がより一般的な構成規則を優先するためです。

 

## <a name="built-in-conventions"></a>組み込み規則

カスタム規則は既定の Code First 規則によって影響を受ける可能性があるため、別の規則の前または後に実行する規則を追加すると便利な場合があります。 これを行うには、派生した DbContext で、規則のコレクションの AddBefore メソッドと Addbefore メソッドを使用します。 次のコードは、前に作成した規則クラスを追加して、組み込みのキー検出規則の前に実行されるようにします。

``` csharp
    modelBuilder.Conventions.AddBefore<IdKeyDiscoveryConvention>(new DateTime2Convention());
```

これは、組み込み規約の前または後に実行する必要がある規則を追加するときに最も使用されるものです。組み込みの規則の一覧については、「: system.string [名前空間](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx)」を参照してください。

また、モデルに適用しない規則を削除することもできます。 規則を削除するには、Remove メソッドを使用します。 PluralizingTableNameConvention を削除する例を次に示します。

``` csharp
    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        modelBuilder.Conventions.Remove<PluralizingTableNameConvention>();
    }
```
