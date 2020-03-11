---
title: カスタム Code First 規則-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: dd2bdbd9-ae9e-470a-aeb8-d0ba160499b7
ms.openlocfilehash: cfd7f7cad532dca5227793c04d7d91e977ea5e4e
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78415893"
---
# <a name="custom-code-first-conventions"></a><span data-ttu-id="8c1de-102">カスタム Code First 規則</span><span class="sxs-lookup"><span data-stu-id="8c1de-102">Custom Code First Conventions</span></span>
> [!NOTE]
> <span data-ttu-id="8c1de-103">**EF6 以降のみ** - このページで説明する機能、API などは、Entity Framework 6 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="8c1de-103">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="8c1de-104">以前のバージョンを使用している場合、一部またはすべての情報は適用されません。</span><span class="sxs-lookup"><span data-stu-id="8c1de-104">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="8c1de-105">Code First を使用する場合、モデルは一連の規則を使用してクラスから計算されます。</span><span class="sxs-lookup"><span data-stu-id="8c1de-105">When using Code First your model is calculated from your classes using a set of conventions.</span></span> <span data-ttu-id="8c1de-106">既定の[Code First 規則](~/ef6/modeling/code-first/conventions/built-in.md)では、エンティティの主キーになるプロパティ、エンティティがマップされるテーブルの名前、および10進数の列の有効桁数と小数点以下桁数が既定で決まります。</span><span class="sxs-lookup"><span data-stu-id="8c1de-106">The default [Code First Conventions](~/ef6/modeling/code-first/conventions/built-in.md) determine things like which property becomes the primary key of an entity, the name of the table an entity maps to, and what precision and scale a decimal column has by default.</span></span>

<span data-ttu-id="8c1de-107">これらの既定の規則は、モデルに最適ではない場合があります。また、データ注釈または Fluent API を使用して多数の個々のエンティティを構成することによって回避する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8c1de-107">Sometimes these default conventions are not ideal for your model, and you have to work around them by configuring many individual entities using Data Annotations or the Fluent API.</span></span> <span data-ttu-id="8c1de-108">カスタム Code First 規則を使用すると、モデルの構成の既定値を提供する独自の規則を定義できます。</span><span class="sxs-lookup"><span data-stu-id="8c1de-108">Custom Code First Conventions let you define your own conventions that provide configuration defaults for your model.</span></span> <span data-ttu-id="8c1de-109">このチュートリアルでは、さまざまな種類のカスタム規則と、それぞれの作成方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="8c1de-109">In this walkthrough, we will explore the different types of custom conventions and how to create each of them.</span></span>


## <a name="model-based-conventions"></a><span data-ttu-id="8c1de-110">モデルベースの規則</span><span class="sxs-lookup"><span data-stu-id="8c1de-110">Model-Based Conventions</span></span>

<span data-ttu-id="8c1de-111">このページでは、カスタム規則の DbModelBuilder API について説明します。</span><span class="sxs-lookup"><span data-stu-id="8c1de-111">This page covers the DbModelBuilder API for custom conventions.</span></span> <span data-ttu-id="8c1de-112">ほとんどのカスタム規則を作成するには、この API が十分である必要があります。</span><span class="sxs-lookup"><span data-stu-id="8c1de-112">This API should be sufficient for authoring most custom conventions.</span></span> <span data-ttu-id="8c1de-113">ただし、作成された最終的なモデルを操作し、高度なシナリオに対応するために、モデルベースの規則を作成することもできます。</span><span class="sxs-lookup"><span data-stu-id="8c1de-113">However, there is also the ability to author model-based conventions - conventions that manipulate the final model once it is created - to handle advanced scenarios.</span></span> <span data-ttu-id="8c1de-114">詳細については、「[モデルベースの規則](~/ef6/modeling/code-first/conventions/model.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8c1de-114">For more information, see [Model-Based Conventions](~/ef6/modeling/code-first/conventions/model.md).</span></span>

 

## <a name="our-model"></a><span data-ttu-id="8c1de-115">Microsoft のモデル</span><span class="sxs-lookup"><span data-stu-id="8c1de-115">Our Model</span></span>

<span data-ttu-id="8c1de-116">まず、この規則で使用できる単純なモデルを定義します。</span><span class="sxs-lookup"><span data-stu-id="8c1de-116">Let's start by defining a simple model that we can use with our conventions.</span></span> <span data-ttu-id="8c1de-117">次のクラスをプロジェクトに追加します。</span><span class="sxs-lookup"><span data-stu-id="8c1de-117">Add the following classes to your project.</span></span>

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

 

## <a name="introducing-custom-conventions"></a><span data-ttu-id="8c1de-118">カスタム規則の概要</span><span class="sxs-lookup"><span data-stu-id="8c1de-118">Introducing Custom Conventions</span></span>

<span data-ttu-id="8c1de-119">Key という名前のプロパティをエンティティ型の主キーとして構成する規則を作成してみましょう。</span><span class="sxs-lookup"><span data-stu-id="8c1de-119">Let’s write a convention that configures any property named Key to be the primary key for its entity type.</span></span>

<span data-ttu-id="8c1de-120">規則はモデルビルダーで有効になっています。これには、コンテキストで OnModelCreating をオーバーライドすることによってアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="8c1de-120">Conventions are enabled on the model builder, which can be accessed by overriding OnModelCreating in the context.</span></span> <span data-ttu-id="8c1de-121">ProductContext クラスを次のように更新します。</span><span class="sxs-lookup"><span data-stu-id="8c1de-121">Update the ProductContext class as follows:</span></span>

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

<span data-ttu-id="8c1de-122">これで、モデルの名前付きキーのすべてのプロパティが、その一部であるすべてのエンティティの主キーとして構成されます。</span><span class="sxs-lookup"><span data-stu-id="8c1de-122">Now, any property in our model named Key will be configured as the primary key of whatever entity its part of.</span></span>

<span data-ttu-id="8c1de-123">また、構成するプロパティの種類に基づいてフィルター処理することにより、規則をより具体的にすることもできます。</span><span class="sxs-lookup"><span data-stu-id="8c1de-123">We could also make our conventions more specific by filtering on the type of property that we are going to configure:</span></span>

``` csharp
    modelBuilder.Properties<int>()
                .Where(p => p.Name == "Key")
                .Configure(p => p.IsKey());
```

<span data-ttu-id="8c1de-124">これにより、Key という名前のすべてのプロパティが、エンティティの主キーになるように構成されますが、整数の場合に限ります。</span><span class="sxs-lookup"><span data-stu-id="8c1de-124">This will configure all properties called Key to be the primary key of their entity, but only if they are an integer.</span></span>

<span data-ttu-id="8c1de-125">IsKey メソッドの興味深い機能は、付加的な機能です。</span><span class="sxs-lookup"><span data-stu-id="8c1de-125">An interesting feature of the IsKey method is that it is additive.</span></span> <span data-ttu-id="8c1de-126">つまり、複数のプロパティで IsKey を呼び出すと、すべてが複合キーの一部になります。</span><span class="sxs-lookup"><span data-stu-id="8c1de-126">Which means that if you call IsKey on multiple properties and they will all become part of a composite key.</span></span> <span data-ttu-id="8c1de-127">1つの注意点として、キーに複数のプロパティを指定する場合は、それらのプロパティの順序も指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8c1de-127">The one caveat for this is that when you specify multiple properties for a key you must also specify an order for those properties.</span></span> <span data-ttu-id="8c1de-128">これを行うには、次のように HasColumnOrder メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="8c1de-128">You can do this by calling the HasColumnOrder method like below:</span></span>

``` csharp
    modelBuilder.Properties<int>()
                .Where(x => x.Name == "Key")
                .Configure(x => x.IsKey().HasColumnOrder(1));

    modelBuilder.Properties()
                .Where(x => x.Name == "Name")
                .Configure(x => x.IsKey().HasColumnOrder(2));
```

<span data-ttu-id="8c1de-129">このコードは、int キー列と文字列名列で構成される複合キーを持つように、モデル内の型を構成します。</span><span class="sxs-lookup"><span data-stu-id="8c1de-129">This code will configure the types in our model to have a composite key consisting of the int Key column and the string Name column.</span></span> <span data-ttu-id="8c1de-130">デザイナーでモデルを表示すると、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="8c1de-130">If we view the model in the designer it would look like this:</span></span>

![複合キー](~/ef6/media/compositekey.png)

<span data-ttu-id="8c1de-132">プロパティ規則のもう1つの例として、[モデル] のすべての DateTime プロパティを、datetime ではなく SQL Server の datetime2 型にマップするように構成します。</span><span class="sxs-lookup"><span data-stu-id="8c1de-132">Another example of property conventions is to configure all DateTime properties in my model to map to the datetime2 type in SQL Server instead of datetime.</span></span> <span data-ttu-id="8c1de-133">これを実現するには、次のようにします。</span><span class="sxs-lookup"><span data-stu-id="8c1de-133">You can achieve this with the following:</span></span>

``` csharp
    modelBuilder.Properties<DateTime>()
                .Configure(c => c.HasColumnType("datetime2"));
```

 

## <a name="convention-classes"></a><span data-ttu-id="8c1de-134">規則クラス</span><span class="sxs-lookup"><span data-stu-id="8c1de-134">Convention Classes</span></span>

<span data-ttu-id="8c1de-135">規則を定義するもう1つの方法は、規則クラスを使用して規則をカプセル化することです。</span><span class="sxs-lookup"><span data-stu-id="8c1de-135">Another way of defining conventions is to use a Convention Class to encapsulate your convention.</span></span> <span data-ttu-id="8c1de-136">規則クラスを使用する場合は、system.object 名前空間の規則クラスから継承する型を作成します。 "。</span><span class="sxs-lookup"><span data-stu-id="8c1de-136">When using a Convention Class then you create a type that inherits from the Convention class in the System.Data.Entity.ModelConfiguration.Conventions namespace.</span></span>

<span data-ttu-id="8c1de-137">規則クラスは、前に示した datetime2 規則を使用して作成できます。これを行うには、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="8c1de-137">We can create a Convention Class with the datetime2 convention that we showed earlier by doing the following:</span></span>

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

<span data-ttu-id="8c1de-138">この規則を使用するように EF に指示するには、この規則を OnModelCreating の規則コレクションに追加します。チュートリアルに従っている場合は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="8c1de-138">To tell EF to use this convention you add it to the Conventions collection in OnModelCreating, which if you’ve been following along with the walkthrough will look like this:</span></span>

``` csharp
    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        modelBuilder.Properties<int>()
                    .Where(p => p.Name.EndsWith("Key"))
                    .Configure(p => p.IsKey());

        modelBuilder.Conventions.Add(new DateTime2Convention());
    }
```

<span data-ttu-id="8c1de-139">ご覧のように、規則のインスタンスを規則のコレクションに追加します。</span><span class="sxs-lookup"><span data-stu-id="8c1de-139">As you can see we add an instance of our convention to the conventions collection.</span></span> <span data-ttu-id="8c1de-140">規則から継承すると、チームまたはプロジェクト間で規則をグループ化および共有するための便利な方法が提供されます。</span><span class="sxs-lookup"><span data-stu-id="8c1de-140">Inheriting from Convention provides a convenient way of grouping and sharing conventions across teams or projects.</span></span> <span data-ttu-id="8c1de-141">たとえば、組織のすべてのプロジェクトで使用される規則の共通セットを含むクラスライブラリがあるとします。</span><span class="sxs-lookup"><span data-stu-id="8c1de-141">You could, for example, have a class library with a common set of conventions that all of your organizations projects use.</span></span>

 

## <a name="custom-attributes"></a><span data-ttu-id="8c1de-142">カスタム属性</span><span class="sxs-lookup"><span data-stu-id="8c1de-142">Custom Attributes</span></span>

<span data-ttu-id="8c1de-143">規則のもう1つの優れた使用方法は、モデルを構成するときに新しい属性を使用できるようにすることです。</span><span class="sxs-lookup"><span data-stu-id="8c1de-143">Another great use of conventions is to enable new attributes to be used when configuring a model.</span></span> <span data-ttu-id="8c1de-144">これを説明するために、文字列プロパティを非 Unicode としてマークするために使用できる属性を作成してみましょう。</span><span class="sxs-lookup"><span data-stu-id="8c1de-144">To illustrate this, let’s create an attribute that we can use to mark String properties as non-Unicode.</span></span>

``` csharp
    [AttributeUsage(AttributeTargets.Property, AllowMultiple = false)]
    public class NonUnicode : Attribute
    {
    }
```

<span data-ttu-id="8c1de-145">次に、この属性をモデルに適用する規則を作成してみましょう。</span><span class="sxs-lookup"><span data-stu-id="8c1de-145">Now, let’s create a convention to apply this attribute to our model:</span></span>

``` csharp
    modelBuilder.Properties()
                .Where(x => x.GetCustomAttributes(false).OfType<NonUnicode>().Any())
                .Configure(c => c.IsUnicode(false));
```

<span data-ttu-id="8c1de-146">この規則では、Unicode 以外の属性を任意の文字列プロパティに追加できます。これは、データベースの列が nvarchar ではなく varchar として格納されることを意味します。</span><span class="sxs-lookup"><span data-stu-id="8c1de-146">With this convention we can add the NonUnicode attribute to any of our string properties, which means the column in the database will be stored as varchar instead of nvarchar.</span></span>

<span data-ttu-id="8c1de-147">この規則について注意する必要がある点は、Unicode 以外の属性を文字列プロパティ以外に配置すると、例外がスローされることです。</span><span class="sxs-lookup"><span data-stu-id="8c1de-147">One thing to note about this convention is that if you put the NonUnicode attribute on anything other than a string property then it will throw an exception.</span></span> <span data-ttu-id="8c1de-148">これは、文字列以外の型で IsUnicode を構成できないためです。</span><span class="sxs-lookup"><span data-stu-id="8c1de-148">It does this because you cannot configure IsUnicode on any type other than a string.</span></span> <span data-ttu-id="8c1de-149">このような場合は、文字列ではないものを除外するように規則をより具体的に設定できます。</span><span class="sxs-lookup"><span data-stu-id="8c1de-149">If this happens, then you can make your convention more specific, so that it filters out anything that isn’t a string.</span></span>

<span data-ttu-id="8c1de-150">上記の規則はカスタム属性を定義する場合に機能しますが、属性クラスのプロパティを使用する場合は特に、使用した方が簡単な API もあります。</span><span class="sxs-lookup"><span data-stu-id="8c1de-150">While the above convention works for defining custom attributes there is another API that can be much easier to use, especially when you want to use properties from the attribute class.</span></span>

<span data-ttu-id="8c1de-151">この例では、属性を更新して IsUnicode 属性に変更します。そのため、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="8c1de-151">For this example we are going to update our attribute and change it to an IsUnicode attribute, so it looks like this:</span></span>

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

<span data-ttu-id="8c1de-152">これが完了したら、属性にブール値を設定して、プロパティが Unicode であるかどうかを規則に伝えることができます。</span><span class="sxs-lookup"><span data-stu-id="8c1de-152">Once we have this, we can set a bool on our attribute to tell the convention whether or not a property should be Unicode.</span></span> <span data-ttu-id="8c1de-153">これは、次のように構成クラスの ClrProperty にアクセスすることによって、既に設定されている規則で行うことができます。</span><span class="sxs-lookup"><span data-stu-id="8c1de-153">We could do this in the convention we have already by accessing the ClrProperty of the configuration class like this:</span></span>

``` csharp
    modelBuilder.Properties()
                .Where(x => x.GetCustomAttributes(false).OfType<IsUnicode>().Any())
                .Configure(c => c.IsUnicode(c.ClrPropertyInfo.GetCustomAttribute<IsUnicode>().Unicode));
```

<span data-ttu-id="8c1de-154">これは簡単ですが、規約 API の Having メソッドを使用することによって、これをより簡潔な方法で実現できます。</span><span class="sxs-lookup"><span data-stu-id="8c1de-154">This is easy enough, but there is a more succinct way of achieving this by using the Having method of the conventions API.</span></span> <span data-ttu-id="8c1de-155">Having メソッドには、Func&lt;PropertyInfo、T&gt; 型のパラメーターがあります。このパラメーターは Where メソッドと同じですが、オブジェクトを返すことが想定されています。</span><span class="sxs-lookup"><span data-stu-id="8c1de-155">The Having method has a parameter of type Func&lt;PropertyInfo, T&gt; which accepts the PropertyInfo the same as the Where method, but is expected to return an object.</span></span> <span data-ttu-id="8c1de-156">返されたオブジェクトが null の場合、プロパティは構成されません。つまり、Where と同じようにプロパティをフィルターで除外することができますが、返されたオブジェクトをキャプチャして、それを Configure メソッドに渡す点も異なります。</span><span class="sxs-lookup"><span data-stu-id="8c1de-156">If the returned object is null then the property will not be configured, which means you can filter out properties with it just like Where, but it is different in that it will also capture the returned object and pass it to the Configure method.</span></span> <span data-ttu-id="8c1de-157">これは次のように機能します。</span><span class="sxs-lookup"><span data-stu-id="8c1de-157">This works like the following:</span></span>

``` csharp
    modelBuilder.Properties()
                .Having(x =>x.GetCustomAttributes(false).OfType<IsUnicode>().FirstOrDefault())
                .Configure((config, att) => config.IsUnicode(att.Unicode));
```

<span data-ttu-id="8c1de-158">カスタム属性は、Having メソッドを使用する唯一の理由ではありません。型またはプロパティを構成するときにフィルター処理を行う必要がある場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="8c1de-158">Custom attributes are not the only reason to use the Having method, it is useful anywhere that you need to reason about something that you are filtering on when configuring your types or properties.</span></span>

 

## <a name="configuring-types"></a><span data-ttu-id="8c1de-159">構成 (型を)</span><span class="sxs-lookup"><span data-stu-id="8c1de-159">Configuring Types</span></span>

<span data-ttu-id="8c1de-160">ここまでのすべての規則はプロパティに対して行われていましたが、モデルの型を構成するための規則 API にはもう1つの領域があります。</span><span class="sxs-lookup"><span data-stu-id="8c1de-160">So far all of our conventions have been for properties, but there is another area of the conventions API for configuring the types in your model.</span></span> <span data-ttu-id="8c1de-161">ここまでに示した規則に似ていますが、構成内のオプションは、プロパティレベルではなくエンティティになります。</span><span class="sxs-lookup"><span data-stu-id="8c1de-161">The experience is similar to the conventions we have seen so far, but the options inside configure will be at the entity instead of property level.</span></span>

<span data-ttu-id="8c1de-162">では、型レベルの規則が非常に役に立ちます。これは、EF の既定とは異なる既存のスキーマにマップするか、別の名前付け規則を使用して新しいデータベースを作成するために、テーブルの名前付け規則を変更することです。</span><span class="sxs-lookup"><span data-stu-id="8c1de-162">One of the things that Type level conventions can be really useful for is changing the table naming convention, either to map to an existing schema that differs from the EF default or to create a new database with a different naming convention.</span></span> <span data-ttu-id="8c1de-163">これを行うには、まず、モデル内の型の TypeInfo を受け入れ、その型のテーブル名を返すメソッドが必要です。</span><span class="sxs-lookup"><span data-stu-id="8c1de-163">To do this we first need a method that can accept the TypeInfo for a type in our model and return what the table name for that type should be:</span></span>

``` csharp
    private string GetTableName(Type type)
    {
        var result = Regex.Replace(type.Name, ".[A-Z]", m => m.Value[0] + "_" + m.Value[1]);

        return result.ToLower();
    }
```

<span data-ttu-id="8c1de-164">このメソッドは、型を受け取り、キャメルケースの代わりに小文字とアンダースコアを使用する文字列を返します。</span><span class="sxs-lookup"><span data-stu-id="8c1de-164">This method takes a type and returns a string that uses lower case with underscores instead of CamelCase.</span></span> <span data-ttu-id="8c1de-165">このモデルでは、ProductCategory クラスは Productcategory ではなく product\_category という名前のテーブルにマップされます。</span><span class="sxs-lookup"><span data-stu-id="8c1de-165">In our model this means that the ProductCategory class will be mapped to a table called product\_category instead of ProductCategories.</span></span>

<span data-ttu-id="8c1de-166">このメソッドを用意したら、次のような規則でそれを呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="8c1de-166">Once we have that method we can call it in a convention like this:</span></span>

``` csharp
    modelBuilder.Types()
                .Configure(c => c.ToTable(GetTableName(c.ClrType)));
```

<span data-ttu-id="8c1de-167">この規則により、モデル内のすべての型が、GetTableName メソッドから返されたテーブル名にマップされるように構成されます。</span><span class="sxs-lookup"><span data-stu-id="8c1de-167">This convention configures every type in our model to map to the table name that is returned from our GetTableName method.</span></span> <span data-ttu-id="8c1de-168">この規則は、Fluent API を使用してモデル内の各エンティティに対して ToTable メソッドを呼び出すことと同じです。</span><span class="sxs-lookup"><span data-stu-id="8c1de-168">This convention is the equivalent to calling the ToTable method for each entity in the model using the Fluent API.</span></span>

<span data-ttu-id="8c1de-169">この点に注意する必要があるのは、ToTable EF を呼び出すと、指定した文字列が正確なテーブル名として取得され、テーブル名を決定するときに通常実行する複数形化はありません。</span><span class="sxs-lookup"><span data-stu-id="8c1de-169">One thing to note about this is that when you call ToTable EF will take the string that you provide as the exact table name, without any of the pluralization that it would normally do when determining table names.</span></span> <span data-ttu-id="8c1de-170">このため、テーブル名は製品\_カテゴリではなく product\_category になります。</span><span class="sxs-lookup"><span data-stu-id="8c1de-170">This is why the table name from our convention is product\_category instead of product\_categories.</span></span> <span data-ttu-id="8c1de-171">この規則では、複数形化サービスの呼び出しを自分で行うことによって解決できます。</span><span class="sxs-lookup"><span data-stu-id="8c1de-171">We can resolve that in our convention by making a call to the pluralization service ourselves.</span></span>

<span data-ttu-id="8c1de-172">次のコードでは、EF6 に追加された[依存関係の解決](~/ef6/fundamentals/configuring/dependency-resolution.md)機能を使用して、EF によって使用された複数形化サービスを取得し、テーブル名を複数化します。</span><span class="sxs-lookup"><span data-stu-id="8c1de-172">In the following code we will use the [Dependency Resolution](~/ef6/fundamentals/configuring/dependency-resolution.md) feature added in EF6 to retrieve the pluralization service that EF would have used and pluralize our table name.</span></span>

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
> <span data-ttu-id="8c1de-173">GetService のジェネリックバージョンは、system.string 名前空間の拡張メソッドです。使用するには、使用するために、コンテキストに using ステートメントを追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8c1de-173">The generic version of GetService is an extension method in the System.Data.Entity.Infrastructure.DependencyResolution namespace, you will need to add a using statement to your context in order to use it.</span></span>

### <a name="totable-and-inheritance"></a><span data-ttu-id="8c1de-174">ToTable と継承</span><span class="sxs-lookup"><span data-stu-id="8c1de-174">ToTable and Inheritance</span></span>

<span data-ttu-id="8c1de-175">ToTable のもう1つの重要な側面は、特定のテーブルに型を明示的にマップした場合に、EF が使用するマッピング方法を変更できることです。</span><span class="sxs-lookup"><span data-stu-id="8c1de-175">Another important aspect of ToTable is that if you explicitly map a type to a given table, then you can alter the mapping strategy that EF will use.</span></span> <span data-ttu-id="8c1de-176">継承階層内のすべての型に対して ToTable を呼び出す場合は、上記のようにテーブルの名前として型名を渡すと、既定の階層構造 (TPH) のマッピング方法が、型ごとのテーブル (TPT) に変更されます。</span><span class="sxs-lookup"><span data-stu-id="8c1de-176">If you call ToTable for every type in an inheritance hierarchy, passing the type name as the name of the table like we did above, then you will change the default Table-Per-Hierarchy (TPH) mapping strategy to Table-Per-Type (TPT).</span></span> <span data-ttu-id="8c1de-177">これを説明する最善の方法は、具体的な例を whith ことです。</span><span class="sxs-lookup"><span data-stu-id="8c1de-177">The best way to describe this is whith a concrete example:</span></span>

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

<span data-ttu-id="8c1de-178">既定では、employee と manager の両方がデータベース内の同じテーブル (Employees) にマップされます。</span><span class="sxs-lookup"><span data-stu-id="8c1de-178">By default both employee and manager are mapped to the same table (Employees) in the database.</span></span> <span data-ttu-id="8c1de-179">テーブルには、各行に格納されているインスタンスの種類を示す識別子列を持つ従業員と管理者の両方が含まれます。</span><span class="sxs-lookup"><span data-stu-id="8c1de-179">The table will contain both employees and managers with a discriminator column that will tell you what type of instance is stored in each row.</span></span> <span data-ttu-id="8c1de-180">これは、階層の1つのテーブルがあるため、TPH のマッピングです。</span><span class="sxs-lookup"><span data-stu-id="8c1de-180">This is TPH mapping as there is a single table for the hierarchy.</span></span> <span data-ttu-id="8c1de-181">ただし、両方の classe で ToTable を呼び出す場合、それぞれの型は独自のテーブルにマップされます。各型に独自のテーブルがあるため、TPT とも呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="8c1de-181">However, if you call ToTable on both classe then each type will instead be mapped to its own table, also known as TPT since each type has its own table.</span></span>

``` csharp
    modelBuilder.Types()
                .Configure(c=>c.ToTable(c.ClrType.Name));
```

<span data-ttu-id="8c1de-182">上記のコードは、次のようなテーブル構造にマップされます。</span><span class="sxs-lookup"><span data-stu-id="8c1de-182">The code above will map to a table structure that looks like the following:</span></span>

![tpt の例](~/ef6/media/tptexample.jpg)

<span data-ttu-id="8c1de-184">これを回避し、既定の TPH マッピングを維持するには、次の2つの方法があります。</span><span class="sxs-lookup"><span data-stu-id="8c1de-184">You can avoid this, and maintain the default TPH mapping, in a couple ways:</span></span>

1.  <span data-ttu-id="8c1de-185">階層内の型ごとに同じテーブル名を使用して ToTable を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="8c1de-185">Call ToTable with the same table name for each type in the hierarchy.</span></span>
2.  <span data-ttu-id="8c1de-186">階層の基本クラスでのみ ToTable を呼び出します。この例では employee です。</span><span class="sxs-lookup"><span data-stu-id="8c1de-186">Call ToTable only on the base class of the hierarchy, in our example that would be employee.</span></span>

 

## <a name="execution-order"></a><span data-ttu-id="8c1de-187">実行順序</span><span class="sxs-lookup"><span data-stu-id="8c1de-187">Execution Order</span></span>

<span data-ttu-id="8c1de-188">規則は、Fluent API と同じように、最後の優先の方法で動作します。</span><span class="sxs-lookup"><span data-stu-id="8c1de-188">Conventions operate in a last wins manner, the same as the Fluent API.</span></span> <span data-ttu-id="8c1de-189">これは、同じプロパティの同じオプションを構成する2つの規則を記述した場合、最後の規則が優先されることを意味します。</span><span class="sxs-lookup"><span data-stu-id="8c1de-189">What this means is that if you write two conventions that configure the same option of the same property, then the last one to execute wins.</span></span> <span data-ttu-id="8c1de-190">たとえば、次のコードでは、すべての文字列の最大長が500に設定されていますが、その後、モデル内の Name という名前のすべてのプロパティが250の最大長を持つように構成されています。</span><span class="sxs-lookup"><span data-stu-id="8c1de-190">As an example, in the code below the max length of all strings is set to 500 but we then configure all properties called Name in the model to have a max length of 250.</span></span>

``` csharp
    modelBuilder.Properties<string>()
                .Configure(c => c.HasMaxLength(500));

    modelBuilder.Properties<string>()
                .Where(x => x.Name == "Name")
                .Configure(c => c.HasMaxLength(250));
```

<span data-ttu-id="8c1de-191">最大長を250に設定する規則は、すべての文字列を500に設定した後になります。そのため、モデル内の Name という名前のすべてのプロパティは、MaxLength が250になり、説明などの他の文字列は500になります。</span><span class="sxs-lookup"><span data-stu-id="8c1de-191">Because the convention to set max length to 250 is after the one that sets all strings to 500, all the properties called Name in our model will have a MaxLength of 250 while any other strings, such as descriptions, would be 500.</span></span> <span data-ttu-id="8c1de-192">この方法で規則を使用すると、モデル内の型またはプロパティに対して一般的な規則を提供し、異なるサブセットをオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="8c1de-192">Using conventions in this way means that you can provide a general convention for types or properties in your model and then overide them for subsets that are different.</span></span>

<span data-ttu-id="8c1de-193">特定のケースでは、Fluent API とデータ注釈を使用して規則をオーバーライドすることもできます。</span><span class="sxs-lookup"><span data-stu-id="8c1de-193">The Fluent API and Data Annotations can also be used to override a convention in specific cases.</span></span> <span data-ttu-id="8c1de-194">上記の例では、Fluent API を使用してプロパティの最大長を設定していた場合、規則の前または後に配置することができます。これは、より具体的な Fluent API がより一般的な構成規則を優先するためです。</span><span class="sxs-lookup"><span data-stu-id="8c1de-194">In our example above if we had used the Fluent API to set the max length of a property then we could have put it before or after the convention, because the more specific Fluent API will win over the more general Configuration Convention.</span></span>

 

## <a name="built-in-conventions"></a><span data-ttu-id="8c1de-195">組み込み規則</span><span class="sxs-lookup"><span data-stu-id="8c1de-195">Built-in Conventions</span></span>

<span data-ttu-id="8c1de-196">カスタム規則は既定の Code First 規則によって影響を受ける可能性があるため、別の規則の前または後に実行する規則を追加すると便利な場合があります。</span><span class="sxs-lookup"><span data-stu-id="8c1de-196">Because custom conventions could be affected by the default Code First conventions, it can be useful to add conventions to run before or after another convention.</span></span> <span data-ttu-id="8c1de-197">これを行うには、派生した DbContext で、規則のコレクションの AddBefore メソッドと Addbefore メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="8c1de-197">To do this you can use the AddBefore and AddAfter methods of the Conventions collection on your derived DbContext.</span></span> <span data-ttu-id="8c1de-198">次のコードは、前に作成した規則クラスを追加して、組み込みのキー検出規則の前に実行されるようにします。</span><span class="sxs-lookup"><span data-stu-id="8c1de-198">The following code would add the convention class we created earlier so that it will run before the built in key discovery convention.</span></span>

``` csharp
    modelBuilder.Conventions.AddBefore<IdKeyDiscoveryConvention>(new DateTime2Convention());
```

<span data-ttu-id="8c1de-199">これは、組み込み規約の前または後に実行する必要がある規則を追加するときに最も使用されるものです。組み込みの規則の一覧については、「: system.string[名前空間](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8c1de-199">This is going to be of the most use when adding conventions that need to run before or after the built in conventions, a list of the built in conventions can be found here: [System.Data.Entity.ModelConfiguration.Conventions Namespace](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx).</span></span>

<span data-ttu-id="8c1de-200">また、モデルに適用しない規則を削除することもできます。</span><span class="sxs-lookup"><span data-stu-id="8c1de-200">You can also remove conventions that you do not want applied to your model.</span></span> <span data-ttu-id="8c1de-201">規則を削除するには、Remove メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="8c1de-201">To remove a convention, use the Remove method.</span></span> <span data-ttu-id="8c1de-202">PluralizingTableNameConvention を削除する例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="8c1de-202">Here is an example of removing the PluralizingTableNameConvention.</span></span>

``` csharp
    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        modelBuilder.Conventions.Remove<PluralizingTableNameConvention>();
    }
```
