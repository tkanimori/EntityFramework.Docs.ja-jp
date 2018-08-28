---
title: カスタム コードの最初規則 - EF6
author: divega
ms.date: 2016-10-23
ms.assetid: dd2bdbd9-ae9e-470a-aeb8-d0ba160499b7
ms.openlocfilehash: 79450790c6d3c8ce7fad209e3946e81d3fad4b75
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42995829"
---
# <a name="custom-code-first-conventions"></a><span data-ttu-id="7ff6d-102">カスタム コードの最初の規則</span><span class="sxs-lookup"><span data-stu-id="7ff6d-102">Custom Code First Conventions</span></span>
> [!NOTE]
> <span data-ttu-id="7ff6d-103">**EF6 以降のみ** - このページで説明する機能、API などは、Entity Framework 6 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-103">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="7ff6d-104">以前のバージョンを使用している場合、一部またはすべての情報は適用されません。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-104">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="7ff6d-105">Code First を使用する場合、モデルは、一連の規則を使用して、クラスから計算されます。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-105">When using Code First your model is calculated from your classes using a set of conventions.</span></span> <span data-ttu-id="7ff6d-106">既定の[コードの最初の規則](~/ef6/modeling/code-first/conventions/built-in.md)プロパティがエンティティの主キー、エンティティのマッピング テーブルと 10 進数の列が既定ではどのような有効桁数と小数点の名前になりますこのようなことを確認します。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-106">The default [Code First Conventions](~/ef6/modeling/code-first/conventions/built-in.md) determine things like which property becomes the primary key of an entity, the name of the table an entity maps to, and what precision and scale a decimal column has by default.</span></span>

<span data-ttu-id="7ff6d-107">場合によってこれらの既定の規則が、モデルの理想的なと、データ注釈または Fluent API を使用して多くの個々 のエンティティを構成することによって、回避する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-107">Sometimes these default conventions are not ideal for your model, and you have to work around them by configuring many individual entities using Data Annotations or the Fluent API.</span></span> <span data-ttu-id="7ff6d-108">カスタム コード最初の規則を使用して、モデルの構成の既定値を提供する独自の規則を定義できます。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-108">Custom Code First Conventions let you define your own conventions that provide configuration defaults for your model.</span></span> <span data-ttu-id="7ff6d-109">このチュートリアルでは、カスタムの規則およびそれらを作成する方法のさまざまな種類について説明します。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-109">In this walkthrough, we will explore the different types of custom conventions and how to create each of them.</span></span>


## <a name="model-based-conventions"></a><span data-ttu-id="7ff6d-110">モデルに基づく規則</span><span class="sxs-lookup"><span data-stu-id="7ff6d-110">Model-Based Conventions</span></span>

<span data-ttu-id="7ff6d-111">このページでは、カスタムの規則の DbModelBuilder API について説明します。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-111">This page covers the DbModelBuilder API for custom conventions.</span></span> <span data-ttu-id="7ff6d-112">この API は、ほとんどのカスタム規則を作成するための十分な必要があります。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-112">This API should be sufficient for authoring most custom conventions.</span></span> <span data-ttu-id="7ff6d-113">ただし、モデルに基づく規則 - 作成した後、最終的なモデルを操作する規則を作成する - 高度なシナリオを処理する機能も。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-113">However, there is also the ability to author model-based conventions - conventions that manipulate the final model once it is created - to handle advanced scenarios.</span></span> <span data-ttu-id="7ff6d-114">詳細については、次を参照してください。[モデルに基づく規則](~/ef6/modeling/code-first/conventions/model.md)します。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-114">For more information, see [Model-Based Conventions](~/ef6/modeling/code-first/conventions/model.md).</span></span>

 

## <a name="our-model"></a><span data-ttu-id="7ff6d-115">私たちのモデル</span><span class="sxs-lookup"><span data-stu-id="7ff6d-115">Our Model</span></span>

<span data-ttu-id="7ff6d-116">規約を使用できる単純なモデルを定義してみましょう。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-116">Let's start by defining a simple model that we can use with our conventions.</span></span> <span data-ttu-id="7ff6d-117">次のクラスをプロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-117">Add the following classes to your project.</span></span>

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

 

## <a name="introducing-custom-conventions"></a><span data-ttu-id="7ff6d-118">カスタム規則の概要</span><span class="sxs-lookup"><span data-stu-id="7ff6d-118">Introducing Custom Conventions</span></span>

<span data-ttu-id="7ff6d-119">名前付きエンティティの種類の主キーのキーのプロパティを構成する規則を記述してみましょう。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-119">Let’s write a convention that configures any property named Key to be the primary key for its entity type.</span></span>

<span data-ttu-id="7ff6d-120">コンテキストで OnModelCreating オーバーライドすることでアクセスできるモデル ビルダーでは、規則が有効にします。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-120">Conventions are enabled on the model builder, which can be accessed by overriding OnModelCreating in the context.</span></span> <span data-ttu-id="7ff6d-121">ProductContext クラスを次のように更新するには。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-121">Update the ProductContext class as follows:</span></span>

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

<span data-ttu-id="7ff6d-122">ここで、名前付きキー、モデル内の任意のプロパティになりますが部分的にどのようなエンティティの主キーとして構成されています。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-122">Now, any property in our model named Key will be configured as the primary key of whatever entity its part of.</span></span>

<span data-ttu-id="7ff6d-123">できるようにも紹介規約より具体的フィルタ処理を構成するプロパティの型。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-123">We could also make our conventions more specific by filtering on the type of property that we are going to configure:</span></span>

``` csharp
    modelBuilder.Properties<int>()
                .Where(p => p.Name == "Key")
                .Configure(p => p.IsKey());
```

<span data-ttu-id="7ff6d-124">これは、そのエンティティのキーが整数である場合にのみに、プライマリ キーと呼ばれるすべてのプロパティで構成されます。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-124">This will configure all properties called Key to be the primary key of their entity, but only if they are an integer.</span></span>

<span data-ttu-id="7ff6d-125">IsKey メソッドの面白い機能がある加法です。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-125">An interesting feature of the IsKey method is that it is additive.</span></span> <span data-ttu-id="7ff6d-126">つまり複数のプロパティで IsKey を呼び出すし、すべてになる複合キーの一部です。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-126">Which means that if you call IsKey on multiple properties and they will all become part of a composite key.</span></span> <span data-ttu-id="7ff6d-127">この 1 つ注意点は、キーの複数のプロパティを指定するときにこれらのプロパティの順序を指定もする必要があります。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-127">The one caveat for this is that when you specify multiple properties for a key you must also specify an order for those properties.</span></span> <span data-ttu-id="7ff6d-128">HasColumnOrder の次のようなメソッドを呼び出すことによって、これを行うことができます。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-128">You can do this by calling the HasColumnOrder method like below:</span></span>

``` csharp
    modelBuilder.Properties<int>()
                .Where(x => x.Name == "Key")
                .Configure(x => x.IsKey().HasColumnOrder(1));

    modelBuilder.Properties()
                .Where(x => x.Name == "Name")
                .Configure(x => x.IsKey().HasColumnOrder(2));
```

<span data-ttu-id="7ff6d-129">このコードは int 型のキー列と文字列の列で構成される複合キーにするには、このモデルの種類を構成します。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-129">This code will configure the types in our model to have a composite key consisting of the int Key column and the string Name column.</span></span> <span data-ttu-id="7ff6d-130">場合は、モデル デザイナーの表示次のようになります。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-130">If we view the model in the designer it would look like this:</span></span>

![compositeKey](~/ef6/media/compositekey.png)

<span data-ttu-id="7ff6d-132">規則のプロパティの別の例では、datetime ではなく、SQL server datetime2 型にマップする、モデルのすべての DateTime プロパティを構成します。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-132">Another example of property conventions is to configure all DateTime properties in my model to map to the datetime2 type in SQL Server instead of datetime.</span></span> <span data-ttu-id="7ff6d-133">次のようにこれを実現できます。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-133">You can achieve this with the following:</span></span>

``` csharp
    modelBuilder.Properties<DateTime>()
                .Configure(c => c.HasColumnType("datetime2"));
```

 

## <a name="convention-classes"></a><span data-ttu-id="7ff6d-134">規則クラス</span><span class="sxs-lookup"><span data-stu-id="7ff6d-134">Convention Classes</span></span>

<span data-ttu-id="7ff6d-135">規則を定義する別の方法では、規則クラスを使用して、規則をカプセル化します。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-135">Another way of defining conventions is to use a Convention Class to encapsulate your convention.</span></span> <span data-ttu-id="7ff6d-136">規則クラスを使用する場合を System.Data.Entity.ModelConfiguration.Conventions 名前空間で規則クラスから継承する型を作成します。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-136">When using a Convention Class then you create a type that inherits from the Convention class in the System.Data.Entity.ModelConfiguration.Conventions namespace.</span></span>

<span data-ttu-id="7ff6d-137">次の手順で先ほどお見せした datetime2 規則と規則クラスを作成できます。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-137">We can create a Convention Class with the datetime2 convention that we showed earlier by doing the following:</span></span>

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

<span data-ttu-id="7ff6d-138">この規則を使用する EF に指示するには、OnModelCreating チュートリアルと共に従っている場合は、次のように次の規則コレクションに追加します。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-138">To tell EF to use this convention you add it to the Conventions collection in OnModelCreating, which if you’ve been following along with the walkthrough will look like this:</span></span>

``` csharp
    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        modelBuilder.Properties<int>()
                    .Where(p => p.Name.EndsWith("Key"))
                    .Configure(p => p.IsKey());

        modelBuilder.Conventions.Add(new DateTime2Convention());
    }
```

<span data-ttu-id="7ff6d-139">ご覧のとおり、規則のインスタンスを規則コレクションに追加します。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-139">As you can see we add an instance of our convention to the conventions collection.</span></span> <span data-ttu-id="7ff6d-140">規則から継承するには、グループ化して、チームまたはプロジェクト全体で規則を共有の便利な方法が提供します。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-140">Inheriting from Convention provides a convenient way of grouping and sharing conventions across teams or projects.</span></span> <span data-ttu-id="7ff6d-141">組織のすべてのプロジェクトを使用する規則の共通セットを使用したクラス ライブラリなどがあることができます。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-141">You could, for example, have a class library with a common set of conventions that all of your organizations projects use.</span></span>

 

## <a name="custom-attributes"></a><span data-ttu-id="7ff6d-142">カスタム属性</span><span class="sxs-lookup"><span data-stu-id="7ff6d-142">Custom Attributes</span></span>

<span data-ttu-id="7ff6d-143">規則のもう 1 つの優れた使用モデルを構成するときに使用される新しい属性を有効にすることです。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-143">Another great use of conventions is to enable new attributes to be used when configuring a model.</span></span> <span data-ttu-id="7ff6d-144">これを説明するには、文字列プロパティを Unicode 以外としてマークするために使用できる属性を作成しましょう。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-144">To illustrate this, let’s create an attribute that we can use to mark String properties as non-Unicode.</span></span>

``` csharp
    [AttributeUsage(AttributeTargets.Property, AllowMultiple = false)]
    public class NonUnicode : Attribute
    {
    }
```

<span data-ttu-id="7ff6d-145">次に、私たちのモデルにこの属性を適用する規則を作成してみましょう。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-145">Now, let’s create a convention to apply this attribute to our model:</span></span>

``` csharp
    modelBuilder.Properties()
                .Where(x => x.GetCustomAttributes(false).OfType<NonUnicode>().Any())
                .Configure(c => c.IsUnicode(false));
```

<span data-ttu-id="7ff6d-146">この規則で、データベース内の列には、文字列プロパティのいずれかに NonUnicode 属性が nvarchar ではなく varchar として格納されるを追加できます。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-146">With this convention we can add the NonUnicode attribute to any of our string properties, which means the column in the database will be stored as varchar instead of nvarchar.</span></span>

<span data-ttu-id="7ff6d-147">この規則について注意が必要ですをされている文字列のプロパティ以外のものでは例外をスローし、NonUnicode 属性を配置する場合。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-147">One thing to note about this convention is that if you put the NonUnicode attribute on anything other than a string property then it will throw an exception.</span></span> <span data-ttu-id="7ff6d-148">これは、文字列以外の任意の型で IsUnicode を構成することはできませんので。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-148">It does this because you cannot configure IsUnicode on any type other than a string.</span></span> <span data-ttu-id="7ff6d-149">この場合、し、行うことができます、規則具体的に文字列のないものはすべてを除外できるようにします。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-149">If this happens, then you can make your convention more specific, so that it filters out anything that isn’t a string.</span></span>

<span data-ttu-id="7ff6d-150">カスタム属性を定義するために、上記の規則が動作しますが、別の API を使用する属性クラスのプロパティを使用する場合に特ににはるかに簡単にできます。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-150">While the above convention works for defining custom attributes there is another API that can be much easier to use, especially when you want to use properties from the attribute class.</span></span>

<span data-ttu-id="7ff6d-151">この例で、属性を更新し、IsUnicode 属性を次のように変更するつもりは。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-151">For this example we are going to update our attribute and change it to an IsUnicode attribute, so it looks like this:</span></span>

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

<span data-ttu-id="7ff6d-152">このしたら、規約、プロパティは Unicode を指定するかどうかを指示する、属性にブール値を設定できます。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-152">Once we have this, we can set a bool on our attribute to tell the convention whether or not a property should be Unicode.</span></span> <span data-ttu-id="7ff6d-153">既にこのような構成クラスの ClrProperty にアクセスすることがある規則で実行できます。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-153">We could do this in the convention we have already by accessing the ClrProperty of the configuration class like this:</span></span>

``` csharp
    modelBuilder.Properties()
                .Where(x => x.GetCustomAttributes(false).OfType<IsUnicode>().Any())
                .Configure(c => c.IsUnicode(c.ClrPropertyInfo.GetCustomAttribute<IsUnicode>().Unicode));
```

<span data-ttu-id="7ff6d-154">これは非常に簡単ですが、Having を使用してこれを実現するためのより簡潔な方法がある規則の API のメソッド。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-154">This is easy enough, but there is a more succinct way of achieving this by using the Having method of the conventions API.</span></span> <span data-ttu-id="7ff6d-155">Func を入力メソッドのパラメーターには Having&lt;PropertyInfo、T&gt; Where と同じ PropertyInfo を受け入れるメソッド、オブジェクトを取得する必要があります。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-155">The Having method has a parameter of type Func&lt;PropertyInfo, T&gt; which accepts the PropertyInfo the same as the Where method, but is expected to return an object.</span></span> <span data-ttu-id="7ff6d-156">プロパティが構成されていないに返されるオブジェクトが null の場合は、Where と同じようにプロパティをフィルターすることができることができますが異なる場合も、返されたオブジェクトをキャプチャし、Configure メソッドに渡すことで。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-156">If the returned object is null then the property will not be configured, which means you can filter out properties with it just like Where, but it is different in that it will also capture the returned object and pass it to the Configure method.</span></span> <span data-ttu-id="7ff6d-157">これは、次のように動作します。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-157">This works like the following:</span></span>

``` csharp
    modelBuilder.Properties()
                .Having(x =>x.GetCustomAttributes(false).OfType<IsUnicode>().FirstOrDefault())
                .Configure((config, att) => config.IsUnicode(att.Unicode));
```

<span data-ttu-id="7ff6d-158">カスタム属性は、Having を使用する唯一の理由ではないメソッド、便利だと、型またはプロパティを構成するときにフィルター処理をすることを判断する必要がある任意の場所。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-158">Custom attributes are not the only reason to use the Having method, it is useful anywhere that you need to reason about something that you are filtering on when configuring your types or properties.</span></span>

 

## <a name="configuring-types"></a><span data-ttu-id="7ff6d-159">種類の構成</span><span class="sxs-lookup"><span data-stu-id="7ff6d-159">Configuring Types</span></span>

<span data-ttu-id="7ff6d-160">これまでに、プロパティについては、すべての規約されているが、モデルの種類を構成するための API 規約の別の領域があります。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-160">So far all of our conventions have been for properties, but there is another area of the conventions API for configuring the types in your model.</span></span> <span data-ttu-id="7ff6d-161">これまで行ってきた規則に似ていますが、内側の構成オプションがレベルでプロパティの代わりに、エンティティになります。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-161">The experience is similar to the conventions we have seen so far, but the options inside configure will be at the entity instead of property level.</span></span>

<span data-ttu-id="7ff6d-162">テーブル名前付け規則、既存の EF 既定とは異なるスキーマにマップするか、別の名前付け規則で新しいデータベースを作成する型レベルの規則は本当に役に立つことの 1 つは変更します。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-162">One of the things that Type level conventions can be really useful for is changing the table naming convention, either to map to an existing schema that differs from the EF default or to create a new database with a different naming convention.</span></span> <span data-ttu-id="7ff6d-163">これを行うことが、モデル内の型の TypeInfo をそのまま使用でき、その型のテーブル名にする必要がありますを返すメソッドがまず必要。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-163">To do this we first need a method that can accept the TypeInfo for a type in our model and return what the table name for that type should be:</span></span>

``` csharp
    private string GetTableName(Type type)
    {
        var result = Regex.Replace(type.Name, ".[A-Z]", m => m.Value[0] + "_" + m.Value[1]);

        return result.ToLower();
    }
```

<span data-ttu-id="7ff6d-164">このメソッドは、型を受け取り、キャメル ケースではなくアンダー スコアと小文字を使用する文字列を返します。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-164">This method takes a type and returns a string that uses lower case with underscores instead of CamelCase.</span></span> <span data-ttu-id="7ff6d-165">ProductCategory クラスを product テーブルにマップすることを意味これモデル\_ProductCategories ではなくカテゴリ。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-165">In our model this means that the ProductCategory class will be mapped to a table called product\_category instead of ProductCategories.</span></span>

<span data-ttu-id="7ff6d-166">そのメソッドを取得したら、次のように、規則で呼び出すできます。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-166">Once we have that method we can call it in a convention like this:</span></span>

``` csharp
    modelBuilder.Types()
                .Configure(c => c.ToTable(GetTableName(c.ClrType)));
```

<span data-ttu-id="7ff6d-167">この規則は、GetTableName メソッドから返されるテーブル名にマップするには、このモデルですべての種類を構成します。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-167">This convention configures every type in our model to map to the table name that is returned from our GetTableName method.</span></span> <span data-ttu-id="7ff6d-168">この規則は、Fluent API を使用して、モデル内の各エンティティの ToTable メソッドの呼び出しと同等です。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-168">This convention is the equivalent to calling the ToTable method for each entity in the model using the Fluent API.</span></span>

<span data-ttu-id="7ff6d-169">これについて注意が必要ですは、呼び出すときに ToTable EF はテーブル名を決定するときに通常行うようの複数形化せず、正確なテーブル名として指定する文字列を使用するはことです。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-169">One thing to note about this is that when you call ToTable EF will take the string that you provide as the exact table name, without any of the pluralization that it would normally do when determining table names.</span></span> <span data-ttu-id="7ff6d-170">これは、規則内のテーブル名が製品である理由\_製品ではなくカテゴリ\_カテゴリ。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-170">This is why the table name from our convention is product\_category instead of product\_categories.</span></span> <span data-ttu-id="7ff6d-171">解決できる規則では自分たちの複数形化サービスへの呼び出しを行います。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-171">We can resolve that in our convention by making a call to the pluralization service ourselves.</span></span>

<span data-ttu-id="7ff6d-172">次のコードでは、使用、[依存関係の解決](~/ef6/fundamentals/configuring/dependency-resolution.md)EF で使用する複数形化サービスを取得するように EF6 で追加された機能、テーブル名を複数形にするとします。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-172">In the following code we will use the [Dependency Resolution](~/ef6/fundamentals/configuring/dependency-resolution.md) feature added in EF6 to retrieve the pluralization service that EF would have used and pluralize our table name.</span></span>

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
> <span data-ttu-id="7ff6d-173">GetService のジェネリック バージョン System.Data.Entity.Infrastructure.DependencyResolution 名前空間での拡張メソッドを使用して、追加する必要がありますを使用するには、コンテキストにステートメント。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-173">The generic version of GetService is an extension method in the System.Data.Entity.Infrastructure.DependencyResolution namespace, you will need to add a using statement to your context in order to use it.</span></span>

### <a name="totable-and-inheritance"></a><span data-ttu-id="7ff6d-174">ToTable と継承</span><span class="sxs-lookup"><span data-stu-id="7ff6d-174">ToTable and Inheritance</span></span>

<span data-ttu-id="7ff6d-175">ToTable のもう 1 つの重要な側面されている場合に、特定のテーブル型を明示的にマップし、EF を使用するマッピング方法を変更することができます。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-175">Another important aspect of ToTable is that if you explicitly map a type to a given table, then you can alter the mapping strategy that EF will use.</span></span> <span data-ttu-id="7ff6d-176">ToTable 継承階層内のすべての型に対して呼び出すことが場合、上記のテーブルの名前として、型名を渡すことは変更する既定 - Table-per-hierarchy (TPH) のマッピング方法 Table-type ごと (TPT)。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-176">If you call ToTable for every type in an inheritance hierarchy, passing the type name as the name of the table like we did above, then you will change the default Table-Per-Hierarchy (TPH) mapping strategy to Table-Per-Type (TPT).</span></span> <span data-ttu-id="7ff6d-177">これを記述する最善の方法では、それぞれ具体的な例です。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-177">The best way to describe this is whith a concrete example:</span></span>

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

<span data-ttu-id="7ff6d-178">既定では、従業員とマネージャーの両方がデータベース内の同じテーブル (従業員) にマップされます。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-178">By default both employee and manager are mapped to the same table (Employees) in the database.</span></span> <span data-ttu-id="7ff6d-179">テーブルには、従業員とマネージャー インスタンスの種類が各行に格納されているかを示す識別子列の両方が含まれます。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-179">The table will contain both employees and managers with a discriminator column that will tell you what type of instance is stored in each row.</span></span> <span data-ttu-id="7ff6d-180">これは、階層の 1 つのテーブルがある TPH マッピングです。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-180">This is TPH mapping as there is a single table for the hierarchy.</span></span> <span data-ttu-id="7ff6d-181">ただし、両方 classe で ToTable を呼び出す場合、各型は代わりにするテーブルにマップ独自、TPT ため、種類ごとに独自のテーブルとも呼ばれますの。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-181">However, if you call ToTable on both classe then each type will instead be mapped to its own table, also known as TPT since each type has its own table.</span></span>

``` csharp
    modelBuilder.Types()
                .Configure(c=>c.ToTable(c.ClrType.Name));
```

<span data-ttu-id="7ff6d-182">上記のコードは、次のようなテーブル構造へマップされます。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-182">The code above will map to a table structure that looks like the following:</span></span>

![tptExample](~/ef6/media/tptexample.jpg)

<span data-ttu-id="7ff6d-184">この問題を回避し、いくつかの方法で、既定の TPH マッピングを維持できます。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-184">You can avoid this, and maintain the default TPH mapping, in a couple ways:</span></span>

1.  <span data-ttu-id="7ff6d-185">階層内の各型の同じテーブル名を持つ ToTable を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-185">Call ToTable with the same table name for each type in the hierarchy.</span></span>
2.  <span data-ttu-id="7ff6d-186">従業員がこの例では、階層の基本クラスでのみ、ToTable を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-186">Call ToTable only on the base class of the hierarchy, in our example that would be employee.</span></span>

 

## <a name="execution-order"></a><span data-ttu-id="7ff6d-187">実行順序</span><span class="sxs-lookup"><span data-stu-id="7ff6d-187">Execution Order</span></span>

<span data-ttu-id="7ff6d-188">規則は、Fluent API と同じです。 最後の wins 方法で動作します。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-188">Conventions operate in a last wins manner, the same as the Fluent API.</span></span> <span data-ttu-id="7ff6d-189">つまり、同じプロパティの同じオプションを構成する 2 つの規則、wins を実行する最後の 1 つを記述する場合は、です。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-189">What this means is that if you write two conventions that configure the same option of the same property, then the last one to execute wins.</span></span> <span data-ttu-id="7ff6d-190">例として、次のコードですべての文字列の最大長は 500 に設定が、250 の最大長を使用してモデルの名前と呼ばれるすべてのプロパティを構成します。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-190">As an example, in the code below the max length of all strings is set to 500 but we then configure all properties called Name in the model to have a max length of 250.</span></span>

``` csharp
    modelBuilder.Properties<string>()
                .Configure(c => c.HasMaxLength(500));

    modelBuilder.Properties<string>()
                .Where(x => x.Name == "Name")
                .Configure(c => c.HasMaxLength(250));
```

<span data-ttu-id="7ff6d-191">最大長は 250 に設定する規約では、すべての文字列を 500 に設定した後であるため、モデル名と呼ばれるすべてのプロパティが、最大長 250 の説明など、他の任意の文字列の中に、500 になります。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-191">Because the convention to set max length to 250 is after the one that sets all strings to 500, all the properties called Name in our model will have a MaxLength of 250 while any other strings, such as descriptions, would be 500.</span></span> <span data-ttu-id="7ff6d-192">型またはモデルと、オーバーライドでプロパティの一般的な規則を提供できることを意味する規則を使用して、この方法では、さまざまなサブセットのことです。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-192">Using conventions in this way means that you can provide a general convention for types or properties in your model and then overide them for subsets that are different.</span></span>

<span data-ttu-id="7ff6d-193">データ注釈と Fluent API は、特定のケースで、規則を上書きすることも使用できます。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-193">The Fluent API and Data Annotations can also be used to override a convention in specific cases.</span></span> <span data-ttu-id="7ff6d-194">上記の例でプロパティの最大長を設定するのには、Fluent API を使用した場合が置くことが、規則の前後にためより具体的に Fluent API 経由での一般的な構成規則が優先されます。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-194">In our example above if we had used the Fluent API to set the max length of a property then we could have put it before or after the convention, because the more specific Fluent API will win over the more general Configuration Convention.</span></span>

 

## <a name="built-in-conventions"></a><span data-ttu-id="7ff6d-195">組み込みの規約</span><span class="sxs-lookup"><span data-stu-id="7ff6d-195">Built-in Conventions</span></span>

<span data-ttu-id="7ff6d-196">カスタムの規則は、既定の Code First 規約によって受ける可能性があります、ために、別の規則の前後に実行するために規則を追加すると便利ができます。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-196">Because custom conventions could be affected by the default Code First conventions, it can be useful to add conventions to run before or after another convention.</span></span> <span data-ttu-id="7ff6d-197">これを行うには、派生、DbContext で規則のコレクションのよび AddBefore と addafter ですメソッドを使用することができます。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-197">To do this you can use the AddBefore and AddAfter methods of the Conventions collection on your derived DbContext.</span></span> <span data-ttu-id="7ff6d-198">次のコードは、構築する前に、実行するために作成した規則クラスを追加、キーの検出規則にします。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-198">The following code would add the convention class we created earlier so that it will run before the built in key discovery convention.</span></span>

``` csharp
    modelBuilder.Conventions.AddBefore<IdKeyDiscoveryConvention>(new DateTime2Convention());
```

<span data-ttu-id="7ff6d-199">これの最も多く使用する組み込みの規則の前後に実行する必要がある規則を追加するときに、組み込みの規則の一覧はこちら: [System.Data.Entity.ModelConfiguration.Conventions Namespace](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx).</span><span class="sxs-lookup"><span data-stu-id="7ff6d-199">This is going to be of the most use when adding conventions that need to run before or after the built in conventions, a list of the built in conventions can be found here: [System.Data.Entity.ModelConfiguration.Conventions Namespace](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx).</span></span>

<span data-ttu-id="7ff6d-200">モデルに適用しないようにする規則を削除することもできます。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-200">You can also remove conventions that you do not want applied to your model.</span></span> <span data-ttu-id="7ff6d-201">規則を削除するには、Remove メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-201">To remove a convention, use the Remove method.</span></span> <span data-ttu-id="7ff6d-202">次に、PluralizingTableNameConvention を削除する例を示します。</span><span class="sxs-lookup"><span data-stu-id="7ff6d-202">Here is an example of removing the PluralizingTableNameConvention.</span></span>

``` csharp
    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        modelBuilder.Conventions.Remove<PluralizingTableNameConvention>();
    }
```
