---
title: データ注釈の Code First-EF6
description: Entity Framework 6 のデータ注釈の Code First
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/code-first/data-annotations
ms.openlocfilehash: dd91ddf674f2235190e50beb847e569a898ad59b
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2020
ms.locfileid: "90074065"
---
# <a name="code-first-data-annotations"></a><span data-ttu-id="cf0dd-103">Code First Data Annotations (Code First のデータ注釈)</span><span class="sxs-lookup"><span data-stu-id="cf0dd-103">Code First Data Annotations</span></span>
> [!NOTE]
> <span data-ttu-id="cf0dd-104">**Ef 4.1 以降のみ** -このページで説明した機能、api などは、Entity Framework 4.1 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-104">**EF4.1 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 4.1.</span></span> <span data-ttu-id="cf0dd-105">以前のバージョンを使用している場合は、この情報の一部またはすべてが適用されません。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-105">If you are using an earlier version, some or all of this information does not apply.</span></span>

<span data-ttu-id="cf0dd-106">このページの内容は、最初にジュリー Lerman () によって作成された記事から適用され \<http://thedatafarm.com> ます。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-106">The content on this page is adapted from an article originally written by Julie Lerman (\<http://thedatafarm.com>).</span></span>

<span data-ttu-id="cf0dd-107">Entity Framework Code First を使用すると、独自のドメインクラスを使用して、EF がクエリ、変更の追跡、および更新機能を実行するために依存するモデルを表すことができます。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-107">Entity Framework Code First allows you to use your own domain classes to represent the model that EF relies on to perform querying, change tracking, and updating functions.</span></span> <span data-ttu-id="cf0dd-108">Code First は、「構成よりも規約」と呼ばれるプログラミングパターンを活用します。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-108">Code First leverages a programming pattern referred to as 'convention over configuration.'</span></span> <span data-ttu-id="cf0dd-109">Code First は、クラスが Entity Framework の規則に従うことを前提としています。その場合、そのジョブの実行方法は自動的に機能します。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-109">Code First will assume that your classes follow the conventions of Entity Framework, and in that case, will automatically work out how to perform its job.</span></span> <span data-ttu-id="cf0dd-110">ただし、クラスがこれらの規則に従っていない場合は、クラスに構成を追加して、必要な情報を EF に提供することができます。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-110">However, if your classes do not follow those conventions, you have the ability to add configurations to your classes to provide EF with the requisite information.</span></span>

<span data-ttu-id="cf0dd-111">Code First には、これらの構成をクラスに追加する2つの方法が用意されています。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-111">Code First gives you two ways to add these configurations to your classes.</span></span> <span data-ttu-id="cf0dd-112">1つは DataAnnotations という単純な属性を使用し、2番目の属性は Code First の Fluent API を使用します。これにより、コードで構成を強制的に記述することができます。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-112">One is using simple attributes called DataAnnotations, and the second is using Code First’s Fluent API, which provides you with a way to describe configurations imperatively, in code.</span></span>

<span data-ttu-id="cf0dd-113">この記事では、(System.componentmodel 名前空間内の) DataAnnotations を使用してクラスを構成する方法に焦点を当て、最も一般的に必要な構成を強調表示します。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-113">This article will focus on using DataAnnotations (in the System.ComponentModel.DataAnnotations namespace) to configure your classes – highlighting the most commonly needed configurations.</span></span> <span data-ttu-id="cf0dd-114">DataAnnotations は、ASP.NET MVC など、多くの .NET アプリケーションによっても認識されます。これにより、これらのアプリケーションは、クライアント側の検証に同じ注釈を利用できます。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-114">DataAnnotations are also understood by a number of .NET applications, such as ASP.NET MVC which allows these applications to leverage the same annotations for client-side validations.</span></span>


## <a name="the-model"></a><span data-ttu-id="cf0dd-115">Model</span><span class="sxs-lookup"><span data-stu-id="cf0dd-115">The model</span></span>

<span data-ttu-id="cf0dd-116">ここでは、単純なクラスのペア (ブログと Post) で Code First DataAnnotations について説明します。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-116">I’ll demonstrate Code First DataAnnotations with a simple pair of classes: Blog and Post.</span></span>

``` csharp
    public class Blog
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public string BloggerName { get; set;}
        public virtual ICollection<Post> Posts { get; set; }
    }

    public class Post
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public DateTime DateCreated { get; set; }
        public string Content { get; set; }
        public int BlogId { get; set; }
        public ICollection<Comment> Comments { get; set; }
    }
```

<span data-ttu-id="cf0dd-117">ブログと Post クラスは、code first 規約に従うことができ、EF との互換性を有効にするための調整は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-117">As they are, the Blog and Post classes conveniently follow code first convention and require no tweaks to enable EF compatability.</span></span> <span data-ttu-id="cf0dd-118">ただし、注釈を使用して、クラスおよびマップ先のデータベースに関する詳細情報を EF に提供することもできます。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-118">However, you can also use the annotations to provide more information to EF about the classes and the database to which they map.</span></span>

 

## <a name="key"></a><span data-ttu-id="cf0dd-119">キー</span><span class="sxs-lookup"><span data-stu-id="cf0dd-119">Key</span></span>

<span data-ttu-id="cf0dd-120">Entity Framework は、エンティティ追跡に使用されるキー値を持つすべてのエンティティに依存します。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-120">Entity Framework relies on every entity having a key value that is used for entity tracking.</span></span> <span data-ttu-id="cf0dd-121">Code First の規則の1つは、暗黙的なキープロパティです。Code First は、"Id" という名前のプロパティ、またはクラス名と "Id" の組み合わせ ("ブログ Id" など) を検索します。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-121">One convention of Code First is implicit key properties; Code First will look for a property named “Id”, or a combination of class name and “Id”, such as “BlogId”.</span></span> <span data-ttu-id="cf0dd-122">このプロパティは、データベースの主キー列にマップされます。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-122">This property will map to a primary key column in the database.</span></span>

<span data-ttu-id="cf0dd-123">ブログと Post クラスは両方ともこの規則に従います。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-123">The Blog and Post classes both follow this convention.</span></span> <span data-ttu-id="cf0dd-124">失敗した場合はどうすればよいでしょうか。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-124">What if they didn’t?</span></span> <span data-ttu-id="cf0dd-125">代わりに、" *Primarytrackingkey* " という名前が使用されている場合はどうし*ますか。*</span><span class="sxs-lookup"><span data-stu-id="cf0dd-125">What if Blog used the name *PrimaryTrackingKey* instead, or even *foo*?</span></span> <span data-ttu-id="cf0dd-126">Code first がこの規則に一致するプロパティを見つけられない場合は、キープロパティを持っている必要がある Entity Framework の要件があるため、例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-126">If code first does not find a property that matches this convention it will throw an exception because of Entity Framework’s requirement that you must have a key property.</span></span> <span data-ttu-id="cf0dd-127">キー注釈を使用して、EntityKey として使用するプロパティを指定できます。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-127">You can use the key annotation to specify which property is to be used as the EntityKey.</span></span>

``` csharp
    public class Blog
    {
        [Key]
        public int PrimaryTrackingKey { get; set; }
        public string Title { get; set; }
        public string BloggerName { get; set;}
        public virtual ICollection<Post> Posts { get; set; }
    }
```

<span data-ttu-id="cf0dd-128">Code first のデータベース生成機能を使用している場合、ブログテーブルには、既定で Id としても定義されている、PrimaryTrackingKey という主キー列があります。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-128">If you are using code first’s database generation feature, the Blog table will have a primary key column named PrimaryTrackingKey, which is also defined as Identity by default.</span></span>

![主キーを持つブログテーブル](~/ef6/media/jj591583-figure01.png)

### <a name="composite-keys"></a><span data-ttu-id="cf0dd-130">複合キー</span><span class="sxs-lookup"><span data-stu-id="cf0dd-130">Composite keys</span></span>

<span data-ttu-id="cf0dd-131">Entity Framework は、複数のプロパティで構成される複合キー (主キー) をサポートしています。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-131">Entity Framework supports composite keys - primary keys that consist of more than one property.</span></span> <span data-ttu-id="cf0dd-132">たとえば、PassportNumber とを組み合わせた主キーを持つ Passport クラスがあるとします。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-132">For example, you could have a Passport class whose primary key is a combination of PassportNumber and IssuingCountry.</span></span>

``` csharp
    public class Passport
    {
        [Key]
        public int PassportNumber { get; set; }
        [Key]
        public string IssuingCountry { get; set; }
        public DateTime Issued { get; set; }
        public DateTime Expires { get; set; }
    }
```

<span data-ttu-id="cf0dd-133">EF モデルで上記のクラスを使用しようとすると、次のようになり `InvalidOperationException` ます。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-133">Attempting to use the above class in your EF model would result in an `InvalidOperationException`:</span></span>

<span data-ttu-id="cf0dd-134">*' Passport ' 型の複合主キーの順序を決定できません。複合主キーの順序を指定するには、ColumnAttribute または HasKey メソッドを使用します。*</span><span class="sxs-lookup"><span data-stu-id="cf0dd-134">*Unable to determine composite primary key ordering for type 'Passport'. Use the ColumnAttribute or the HasKey method to specify an order for composite primary keys.*</span></span>

<span data-ttu-id="cf0dd-135">複合キーを使用するには、Entity Framework で、キープロパティの順序を定義する必要があります。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-135">In order to use composite keys, Entity Framework requires you to define an order for the key properties.</span></span> <span data-ttu-id="cf0dd-136">これを行うには、列注釈を使用して順序を指定します。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-136">You can do this by using the Column annotation to specify an order.</span></span>

>[!NOTE]
> <span data-ttu-id="cf0dd-137">順序の値は (インデックスベースではなく) 相対であるため、任意の値を使用できます。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-137">The order value is relative (rather than index based) so any values can be used.</span></span> <span data-ttu-id="cf0dd-138">たとえば、100と200は、1と2の代わりに許容されます。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-138">For example, 100 and 200 would be acceptable in place of 1 and 2.</span></span>

``` csharp
    public class Passport
    {
        [Key]
        [Column(Order=1)]
        public int PassportNumber { get; set; }
        [Key]
        [Column(Order = 2)]
        public string IssuingCountry { get; set; }
        public DateTime Issued { get; set; }
        public DateTime Expires { get; set; }
    }
```

<span data-ttu-id="cf0dd-139">複合外部キーを持つエンティティがある場合は、対応する主キープロパティに使用したものと同じ列の順序を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-139">If you have entities with composite foreign keys, then you must specify the same column ordering that you used for the corresponding primary key properties.</span></span>

<span data-ttu-id="cf0dd-140">外部キープロパティ内の相対順序のみが同じである必要があります。 **順序** に割り当てられた正確な値を一致させる必要はありません。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-140">Only the relative ordering within the foreign key properties needs to be the same, the exact values assigned to **Order** do not need to match.</span></span> <span data-ttu-id="cf0dd-141">たとえば、次のクラスでは、1と2の代わりに3と4を使用できます。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-141">For example, in the following class, 3 and 4 could be used in place of 1 and 2.</span></span>

``` csharp
    public class PassportStamp
    {
        [Key]
        public int StampId { get; set; }
        public DateTime Stamped { get; set; }
        public string StampingCountry { get; set; }

        [ForeignKey("Passport")]
        [Column(Order = 1)]
        public int PassportNumber { get; set; }

        [ForeignKey("Passport")]
        [Column(Order = 2)]
        public string IssuingCountry { get; set; }

        public Passport Passport { get; set; }
    }
```

## <a name="required"></a><span data-ttu-id="cf0dd-142">必須</span><span class="sxs-lookup"><span data-stu-id="cf0dd-142">Required</span></span>

<span data-ttu-id="cf0dd-143">注釈は、 `Required` 特定のプロパティが必要であることを EF に指示します。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-143">The `Required` annotation tells EF that a particular property is required.</span></span>

<span data-ttu-id="cf0dd-144">Title プロパティに必要なを追加すると、EF (および MVC) によって、プロパティにデータが含まれるようになります。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-144">Adding Required to the Title property will force EF (and MVC) to ensure that the property has data in it.</span></span>

``` csharp
    [Required]
    public string Title { get; set; }
```

<span data-ttu-id="cf0dd-145">アプリケーションで追加のコードやマークアップの変更を加えなくても、MVC アプリケーションは、プロパティと注釈の名前を使用してメッセージを動的に構築しながら、クライアント側の検証を実行します。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-145">With no additional code or markup changes in the application, an MVC application will perform client side validation, even dynamically building a message using the property and annotation names.</span></span>

![タイトルを含むページの作成が必要なエラー](~/ef6/media/jj591583-figure02.png)

<span data-ttu-id="cf0dd-147">また、必要な属性は、マップされたプロパティを null 非許容にすることで、生成されるデータベースにも影響します。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-147">The Required attribute will also affect the generated database by making the mapped property non-nullable.</span></span> <span data-ttu-id="cf0dd-148">Title フィールドが "not null" に変更されていることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-148">Notice that the Title field has changed to “not null”.</span></span>

>[!NOTE]
> <span data-ttu-id="cf0dd-149">場合によっては、プロパティが必要な場合でも、データベース内の列を null 非許容にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-149">In some cases it may not be possible for the column in the database to be non-nullable even though the property is required.</span></span> <span data-ttu-id="cf0dd-150">たとえば、複数の型に対して TPH 継承戦略データを使用すると、1つのテーブルに格納されます。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-150">For example, when using a TPH inheritance strategy data for multiple types is stored in a single table.</span></span> <span data-ttu-id="cf0dd-151">派生型に必須のプロパティが含まれている場合、階層内のすべての型がこのプロパティを持つわけではないため、列を null 非許容にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-151">If a derived type includes a required property the column cannot be made non-nullable since not all types in the hierarchy will have this property.</span></span>

 

![ブログの表](~/ef6/media/jj591583-figure03.png)

 

## <a name="maxlength-and-minlength"></a><span data-ttu-id="cf0dd-153">MaxLength および MinLength</span><span class="sxs-lookup"><span data-stu-id="cf0dd-153">MaxLength and MinLength</span></span>

<span data-ttu-id="cf0dd-154">属性と属性を使用すると、 `MaxLength` `MinLength` の場合と同様に、追加のプロパティ検証を指定でき `Required` ます。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-154">The `MaxLength` and `MinLength` attributes allow you to specify additional property validations, just as you did with `Required`.</span></span>

<span data-ttu-id="cf0dd-155">次に示すのは、ブログ Ger名と長さの要件です。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-155">Here is the BloggerName with length requirements.</span></span> <span data-ttu-id="cf0dd-156">この例では、属性を結合する方法も示しています。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-156">The example also demonstrates how to combine attributes.</span></span>

``` csharp
    [MaxLength(10),MinLength(5)]
    public string BloggerName { get; set; }
```

<span data-ttu-id="cf0dd-157">MaxLength 注釈は、プロパティの長さを10に設定することによってデータベースに影響を与えます。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-157">The MaxLength annotation will impact the database by setting the property’s length to 10.</span></span>

![ブログテーブルで、ブログ Gername 列の最大長が示されています](~/ef6/media/jj591583-figure04.png)

<span data-ttu-id="cf0dd-159">MVC クライアント側注釈と EF 4.1 サーバー側注釈は、両方ともこの検証を行い、"フィールドのブログ Gername は、最大長が ' 10 ' の文字列または配列型である必要があります" というエラーメッセージを動的に構築します。このメッセージは少し長くなります。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-159">MVC client-side annotation and EF 4.1 server-side annotation will both honor this validation, again dynamically building an error message: “The field BloggerName must be a string or array type with a maximum length of '10'.” That message is a little long.</span></span> <span data-ttu-id="cf0dd-160">多くの注釈を使用すると、ErrorMessage 属性を使用してエラーメッセージを指定できます。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-160">Many annotations let you specify an error message with the ErrorMessage attribute.</span></span>

``` csharp
    [MaxLength(10, ErrorMessage="BloggerName must be 10 characters or less"),MinLength(5)]
    public string BloggerName { get; set; }
```

<span data-ttu-id="cf0dd-161">また、必要な注釈に ErrorMessage を指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-161">You can also specify ErrorMessage in the Required annotation.</span></span>

![カスタムエラーメッセージを含むページの作成](~/ef6/media/jj591583-figure05.png)

 

## <a name="notmapped"></a><span data-ttu-id="cf0dd-163">NotMapped</span><span class="sxs-lookup"><span data-stu-id="cf0dd-163">NotMapped</span></span>

<span data-ttu-id="cf0dd-164">Code first の規則では、サポートされるデータ型のすべてのプロパティがデータベースで表現されます。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-164">Code first convention dictates that every property that is of a supported data type is represented in the database.</span></span> <span data-ttu-id="cf0dd-165">ただし、これはアプリケーションでは常に当てはまりません。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-165">But this isn’t always the case in your applications.</span></span> <span data-ttu-id="cf0dd-166">たとえば、Title および Blog Gername フィールドに基づいてコードを作成する、ブログクラスのプロパティがあるとします。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-166">For example you might have a property in the Blog class that creates a code based on the Title and BloggerName fields.</span></span> <span data-ttu-id="cf0dd-167">このプロパティは動的に作成することができ、保存する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-167">That property can be created dynamically and does not need to be stored.</span></span> <span data-ttu-id="cf0dd-168">このブログコードプロパティなど、NotMapped 注釈を使用して、データベースにマップされていないプロパティをマークできます。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-168">You can mark any properties that do not map to the database with the NotMapped annotation such as this BlogCode property.</span></span>

``` csharp
    [NotMapped]
    public string BlogCode
    {
        get
        {
            return Title.Substring(0, 1) + ":" + BloggerName.Substring(0, 1);
        }
    }
```

 

## <a name="complextype"></a><span data-ttu-id="cf0dd-169">ComplexType</span><span class="sxs-lookup"><span data-stu-id="cf0dd-169">ComplexType</span></span>

<span data-ttu-id="cf0dd-170">クラスのセット全体でドメインエンティティを記述し、そのクラスをレイヤー化して完全なエンティティを記述することは珍しくありません。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-170">It’s not uncommon to describe your domain entities across a set of classes and then layer those classes to describe a complete entity.</span></span> <span data-ttu-id="cf0dd-171">たとえば、ブログの詳細というクラスをモデルに追加することができます。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-171">For example, you may add a class called BlogDetails to your model.</span></span>

``` csharp
    public class BlogDetails
    {
        public DateTime? DateCreated { get; set; }

        [MaxLength(250)]
        public string Description { get; set; }
    }
```

<span data-ttu-id="cf0dd-172">に `BlogDetails` は、キープロパティの型がないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-172">Notice that `BlogDetails` does not have any type of key property.</span></span> <span data-ttu-id="cf0dd-173">ドメイン駆動設計で `BlogDetails` は、は値オブジェクトと呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-173">In domain driven design, `BlogDetails` is referred to as a value object.</span></span> <span data-ttu-id="cf0dd-174">Entity Framework は、値オブジェクトを複合型として参照します。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-174">Entity Framework refers to value objects as complex types.</span></span><span data-ttu-id="cf0dd-175">複合型は独自に追跡できません。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-175">  Complex types cannot be tracked on their own.</span></span>

<span data-ttu-id="cf0dd-176">ただし、クラスのプロパティとして `Blog` 、 `BlogDetails` はオブジェクトの一部として追跡され `Blog` ます。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-176">However as a property in the `Blog` class, `BlogDetails` will be tracked as part of a `Blog` object.</span></span> <span data-ttu-id="cf0dd-177">Code first でこれを認識させるには、クラスをとしてマークする必要があり `BlogDetails` `ComplexType` ます。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-177">In order for code first to recognize this, you must mark the `BlogDetails` class as a `ComplexType`.</span></span>

``` csharp
    [ComplexType]
    public class BlogDetails
    {
        public DateTime? DateCreated { get; set; }

        [MaxLength(250)]
        public string Description { get; set; }
    }
```

<span data-ttu-id="cf0dd-178">クラスにプロパティを追加し `Blog` て、そのブログのを表すことができるようになりました `BlogDetails` 。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-178">Now you can add a property in the `Blog` class to represent the `BlogDetails` for that blog.</span></span>

``` csharp
        public BlogDetails BlogDetail { get; set; }
```

<span data-ttu-id="cf0dd-179">データベースでは、この `Blog` テーブルには、プロパティに含まれるプロパティを含む、ブログのすべてのプロパティが含まれ `BlogDetail` ます。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-179">In the database, the `Blog` table will contain all of the properties of the blog including the properties contained in its `BlogDetail` property.</span></span> <span data-ttu-id="cf0dd-180">既定では、それぞれの前に複合型の名前 "ブログの詳細" が付いています。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-180">By default, each one is preceded with the name of the complex type, "BlogDetail".</span></span>

![複合型を含むブログテーブル](~/ef6/media/jj591583-figure06.png)


## <a name="concurrencycheck"></a><span data-ttu-id="cf0dd-182">ConcurrencyCheck</span><span class="sxs-lookup"><span data-stu-id="cf0dd-182">ConcurrencyCheck</span></span>

<span data-ttu-id="cf0dd-183">注釈を使用すると、 `ConcurrencyCheck` ユーザーがエンティティを編集または削除したときに、データベースの同時実行チェックに使用される1つ以上のプロパティにフラグを設定できます。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-183">The `ConcurrencyCheck` annotation allows you to flag one or more properties to be used for concurrency checking in the database when a user edits or deletes an entity.</span></span> <span data-ttu-id="cf0dd-184">EF デザイナーを使用している場合は、プロパティをに設定することに `ConcurrencyMode` `Fixed` なります。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-184">If you've been working with the EF Designer, this aligns with setting a property's `ConcurrencyMode` to `Fixed`.</span></span>

<span data-ttu-id="cf0dd-185">`ConcurrencyCheck`これをプロパティに追加して、の動作を確認してみましょう `BloggerName` 。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-185">Let’s see how `ConcurrencyCheck` works by adding it to the `BloggerName` property.</span></span>

``` csharp
    [ConcurrencyCheck, MaxLength(10, ErrorMessage="BloggerName must be 10 characters or less"),MinLength(5)]
    public string BloggerName { get; set; }
```

<span data-ttu-id="cf0dd-186">が呼び出されると、フィールドの注釈によって、 `SaveChanges` `ConcurrencyCheck` `BloggerName` そのプロパティの元の値が更新で使用されます。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-186">When `SaveChanges` is called, because of the `ConcurrencyCheck` annotation on the `BloggerName` field, the original value of that property will be used in the update.</span></span> <span data-ttu-id="cf0dd-187">コマンドは、キー値だけでなく、の元の値にもフィルターを適用して、正しい行の検索を試み `BloggerName` ます。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-187">The command will attempt to locate the correct row by filtering not only on the key value but also on the original value of `BloggerName`.</span></span><span data-ttu-id="cf0dd-188">ここでは、データベースに送信される UPDATE コマンドの重要な部分を示します。このコマンドは、が1で、が "ジュリー" である行を更新し `PrimaryTrackingKey` `BloggerName` ます。これは、そのブログがデータベースから取得されたときの元の値です。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-188">  Here are the critical parts of the UPDATE command sent to the database, where you can see the command will update the row that has a `PrimaryTrackingKey` is 1 and a `BloggerName` of “Julie” which was the original value when that blog was retrieved from the database.</span></span>

``` SQL
    where (([PrimaryTrackingKey] = @4) and ([BloggerName] = @5))
    @4=1,@5=N'Julie'
```

<span data-ttu-id="cf0dd-189">その間に他のユーザーがそのブログの blogger 名を変更した場合、この更新は失敗し、処理する必要がある **DbUpdateConcurrencyException** が得られます。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-189">If someone has changed the blogger name for that blog in the meantime, this update will fail and you’ll get a **DbUpdateConcurrencyException** that you'll need to handle.</span></span>

 

## <a name="timestamp"></a><span data-ttu-id="cf0dd-190">TimeStamp</span><span class="sxs-lookup"><span data-stu-id="cf0dd-190">TimeStamp</span></span>

<span data-ttu-id="cf0dd-191">同時実行チェックには、rowversion フィールドまたは timestamp フィールドを使用するのが一般的です。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-191">It's more common to use rowversion or timestamp fields for concurrency checking.</span></span> <span data-ttu-id="cf0dd-192">ただし、注釈を使用するのではなく、 `ConcurrencyCheck` `TimeStamp` プロパティの型がバイト配列である限り、より具体的な注釈を使用できます。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-192">But rather than using the `ConcurrencyCheck` annotation, you can use the more specific `TimeStamp` annotation as long as the type of the property is byte array.</span></span> <span data-ttu-id="cf0dd-193">Code first ではプロパティはプロパティと同じように扱われ `Timestamp` `ConcurrencyCheck` ますが、code first によって生成されるデータベースフィールドが null 非許容であることも保証されます。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-193">Code first will treat `Timestamp` properties the same as `ConcurrencyCheck` properties, but it will also ensure that the database field that code first generates is non-nullable.</span></span> <span data-ttu-id="cf0dd-194">指定されたクラスに設定できるタイムスタンププロパティは1つだけです。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-194">You can only have one timestamp property in a given class.</span></span>

<span data-ttu-id="cf0dd-195">次のプロパティをブログクラスに追加します。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-195">Adding the following property to the Blog class:</span></span>

``` csharp
    [Timestamp]
    public Byte[] TimeStamp { get; set; }
```

<span data-ttu-id="cf0dd-196">コードでは、最初にデータベーステーブルに null 値を許容しないタイムスタンプ列を作成します。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-196">results in code first creating a non-nullable timestamp column in the database table.</span></span>

![タイムスタンプ列が含まれるブログテーブル](~/ef6/media/jj591583-figure07.png)

 

## <a name="table-and-column"></a><span data-ttu-id="cf0dd-198">テーブルと列</span><span class="sxs-lookup"><span data-stu-id="cf0dd-198">Table and Column</span></span>

<span data-ttu-id="cf0dd-199">データベース Code First 作成する場合は、作成するテーブルと列の名前を変更することもできます。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-199">If you are letting Code First create the database, you may want to change the name of the tables and columns it is creating.</span></span> <span data-ttu-id="cf0dd-200">また、既存のデータベースで Code First を使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-200">You can also use Code First with an existing database.</span></span> <span data-ttu-id="cf0dd-201">ただし、ドメイン内のクラスとプロパティの名前がデータベース内のテーブルと列の名前と一致するとは限りません。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-201">But it's not always the case that the names of the classes and properties in your domain match the names of the tables and columns in your database.</span></span>

<span data-ttu-id="cf0dd-202">クラスにはという名前が付けられ `Blog` 、規約により、code first はという名前のテーブルにマップされることを前提として `Blogs` います。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-202">My class is named `Blog` and by convention, code first presumes this will map to a table named `Blogs`.</span></span> <span data-ttu-id="cf0dd-203">そうでない場合は、属性を使用してテーブルの名前を指定でき `Table` ます。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-203">If that's not the case you can specify the name of the table with the `Table` attribute.</span></span> <span data-ttu-id="cf0dd-204">ここで、注釈はテーブル名が **Internalblogs**であることを示しています。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-204">Here for example, the annotation is specifying that the table name is **InternalBlogs**.</span></span>

``` csharp
    [Table("InternalBlogs")]
    public class Blog
```

<span data-ttu-id="cf0dd-205">`Column`注釈は、マップされた列の属性を指定するときに、より使い慣れになります。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-205">The `Column` annotation is a more adept in specifying the attributes of a mapped column.</span></span> <span data-ttu-id="cf0dd-206">名前、データ型、またはテーブル内で列が表示される順序を指定できます。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-206">You can stipulate a name, data type or even the order in which a column appears in the table.</span></span> <span data-ttu-id="cf0dd-207">属性の例を次に示し `Column` ます。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-207">Here is an example of the `Column` attribute.</span></span>

``` csharp
    [Column("BlogDescription", TypeName="ntext")]
    public String Description {get;set;}
```

<span data-ttu-id="cf0dd-208">列の `TypeName` 属性を DataType DataAnnotation と混同しないでください。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-208">Don’t confuse Column’s `TypeName` attribute with the DataType DataAnnotation.</span></span> <span data-ttu-id="cf0dd-209">DataType は UI に使用される注釈であり、Code First によって無視されます。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-209">DataType is an annotation used for the UI and is ignored by Code First.</span></span>

<span data-ttu-id="cf0dd-210">再生成された後のテーブルを次に示します。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-210">Here is the table after it’s been regenerated.</span></span> <span data-ttu-id="cf0dd-211">テーブル名が **Internalblogs** に変更され、 `Description` 複合型の列がになりました `BlogDescription` 。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-211">The table name has changed to **InternalBlogs** and `Description` column from the complex type is now `BlogDescription`.</span></span> <span data-ttu-id="cf0dd-212">名前が注釈に指定されているため、code first では、複合型の名前を使用して列名を開始する規則が使用されません。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-212">Because the name was specified in the annotation, code first will not use the convention of starting the column name with the name of the complex type.</span></span>

![ブログのテーブルと列の名前を変更](~/ef6/media/jj591583-figure08.png)

 

## <a name="databasegenerated"></a><span data-ttu-id="cf0dd-214">DatabaseGenerated</span><span class="sxs-lookup"><span data-stu-id="cf0dd-214">DatabaseGenerated</span></span>

<span data-ttu-id="cf0dd-215">重要なデータベース機能は、計算されたプロパティを持つことができる機能です。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-215">An important database features is the ability to have computed properties.</span></span> <span data-ttu-id="cf0dd-216">Code First クラスを計算列を含むテーブルにマップしている場合は、Entity Framework でそれらの列を更新しないようにしてください。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-216">If you're mapping your Code First classes to tables that contain computed columns, you don't want Entity Framework to try to update those columns.</span></span> <span data-ttu-id="cf0dd-217">ただし、データを挿入または更新した後、EF によってそれらの値がデータベースから返されるようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-217">But you do want EF to return those values from the database after you've inserted or updated data.</span></span> <span data-ttu-id="cf0dd-218">注釈を使用して、 `DatabaseGenerated` 列挙型と共にクラス内のこれらのプロパティにフラグを付けることができ `Computed` ます。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-218">You can use the `DatabaseGenerated` annotation to flag those properties in your class along with the `Computed` enum.</span></span> <span data-ttu-id="cf0dd-219">その他の列挙型は `None` と `Identity` です。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-219">Other enums are `None` and `Identity`.</span></span>

``` csharp
    [DatabaseGenerated(DatabaseGeneratedOption.Computed)]
    public DateTime DateCreated { get; set; }
```

<span data-ttu-id="cf0dd-220">Code first でデータベースを生成しているときに、バイト列またはタイムスタンプ列に対して生成されたデータベースを使用できます。それ以外の場合は、既存のデータベースをポイントするときにのみ使用してください。これは、code first では計算列の数式を判別できないためです。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-220">You can use database generated on byte or timestamp columns when code first is generating the database, otherwise you should only use this when pointing to existing databases because code first won't be able to determine the formula for the computed column.</span></span>

<span data-ttu-id="cf0dd-221">前に読みました。既定では、整数であるキープロパティはデータベースの id キーになります。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-221">You read above that by default, a key property that is an integer will become an identity key in the database.</span></span> <span data-ttu-id="cf0dd-222">これは、をに設定することと同じ `DatabaseGenerated` `DatabaseGeneratedOption.Identity` です。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-222">That would be the same as setting `DatabaseGenerated` to `DatabaseGeneratedOption.Identity`.</span></span> <span data-ttu-id="cf0dd-223">Id キーにしない場合は、値をに設定 `DatabaseGeneratedOption.None` します。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-223">If you do not want it to be an identity key, you can set the value to `DatabaseGeneratedOption.None`.</span></span>

 

## <a name="index"></a><span data-ttu-id="cf0dd-224">インデックス</span><span class="sxs-lookup"><span data-stu-id="cf0dd-224">Index</span></span>

> [!NOTE]
> <span data-ttu-id="cf0dd-225">**Ef 6.1 以降のみ** - `Index` 属性は Entity Framework 6.1 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-225">**EF6.1 Onwards Only** - The `Index` attribute was introduced in Entity Framework 6.1.</span></span> <span data-ttu-id="cf0dd-226">以前のバージョンを使用している場合、このセクションの情報は適用されません。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-226">If you are using an earlier version the information in this section does not apply.</span></span>

<span data-ttu-id="cf0dd-227">1つ以上の列にインデックスを作成するには、 **Indexattribute**を使用します。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-227">You can create an index on one or more columns using the **IndexAttribute**.</span></span> <span data-ttu-id="cf0dd-228">属性を1つ以上のプロパティに追加すると、データベースの作成時に EF によってデータベース内の対応するインデックスが作成されるか、Code First Migrations を使用している場合は、対応する **Createindex** の呼び出しがスキャフォールディングます。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-228">Adding the attribute to one or more properties will cause EF to create the corresponding index in the database when it creates the database, or scaffold the corresponding **CreateIndex** calls if you are using Code First Migrations.</span></span>

<span data-ttu-id="cf0dd-229">たとえば、次のコードでは、 `Rating` データベース内のテーブルの列にインデックスが作成され `Posts` ます。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-229">For example, the following code will result in an index being created on the `Rating` column of the `Posts` table in the database.</span></span>

``` csharp
    public class Post
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }
        [Index]
        public int Rating { get; set; }
        public int BlogId { get; set; }
    }
```

<span data-ttu-id="cf0dd-230">既定では、インデックスは\*\*ix \_ &lt; プロパティ名 &gt; \*\* ( \_ 上記の例では ix 評価) という名前になります。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-230">By default, the index will be named **IX\_&lt;property name&gt;** (IX\_Rating in the above example).</span></span> <span data-ttu-id="cf0dd-231">ただし、インデックスの名前を指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-231">You can also specify a name for the index though.</span></span> <span data-ttu-id="cf0dd-232">次の例では、インデックスの名前をにする必要があることを指定し `PostRatingIndex` ます。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-232">The following example specifies that the index should be named `PostRatingIndex`.</span></span>

``` csharp
    [Index("PostRatingIndex")]
    public int Rating { get; set; }
```

<span data-ttu-id="cf0dd-233">既定では、インデックスは一意ではありませんが、名前付きパラメーターを使用して、インデックスが一意である `IsUnique` ことを指定できます。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-233">By default, indexes are non-unique, but you can use the `IsUnique` named parameter to specify that an index should be unique.</span></span> <span data-ttu-id="cf0dd-234">次の例では、のログイン名に対して一意のインデックスを作成し `User` ます。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-234">The following example introduces a unique index on a `User`'s login name.</span></span>

``` csharp
    public class User
    {
        public int UserId { get; set; }

        [Index(IsUnique = true)]
        [StringLength(200)]
        public string Username { get; set; }

        public string DisplayName { get; set; }
    }
```

### <a name="multiple-column-indexes"></a><span data-ttu-id="cf0dd-235">複数列のインデックス</span><span class="sxs-lookup"><span data-stu-id="cf0dd-235">Multiple-Column Indexes</span></span>

<span data-ttu-id="cf0dd-236">複数の列にまたがるインデックスは、指定されたテーブルの複数のインデックス注釈で同じ名前を使用して指定されます。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-236">Indexes that span multiple columns are specified by using the same name in multiple Index annotations for a given table.</span></span> <span data-ttu-id="cf0dd-237">複数列のインデックスを作成する場合は、インデックス内の列の順序を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-237">When you create multi-column indexes, you need to specify an order for the columns in the index.</span></span> <span data-ttu-id="cf0dd-238">たとえば、次のコードでは、に複数列のインデックスを作成 `Rating` し、 `BlogId` **IX \_ ブログ idand格付け**という名前を付けています。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-238">For example, the following code creates a multi-column index on `Rating` and `BlogId` called **IX\_BlogIdAndRating**.</span></span> <span data-ttu-id="cf0dd-239">`BlogId` はインデックスの最初の列で、 `Rating` は2番目の列です。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-239">`BlogId` is the first column in the index and `Rating` is the second.</span></span>

``` csharp
    public class Post
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }
        [Index("IX_BlogIdAndRating", 2)]
        public int Rating { get; set; }
        [Index("IX_BlogIdAndRating", 1)]
        public int BlogId { get; set; }
    }
```

 

## <a name="relationship-attributes-inverseproperty-and-foreignkey"></a><span data-ttu-id="cf0dd-240">リレーションシップ属性: InverseProperty と ForeignKey</span><span class="sxs-lookup"><span data-stu-id="cf0dd-240">Relationship Attributes: InverseProperty and ForeignKey</span></span>

> [!NOTE]
> <span data-ttu-id="cf0dd-241">このページでは、データ注釈を使用して Code First モデル内のリレーションシップを設定する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-241">This page provides information about setting up relationships in your Code First model using Data Annotations.</span></span> <span data-ttu-id="cf0dd-242">EF のリレーションシップに関する一般的な情報と、リレーションシップを使用してデータにアクセスして操作する方法については、「 [リレーションシップ & ナビゲーションプロパティ](xref:ef6/fundamentals/relationships)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-242">For general information about relationships in EF and how to access and manipulate data using relationships, see [Relationships & Navigation Properties](xref:ef6/fundamentals/relationships).\*</span></span>

<span data-ttu-id="cf0dd-243">Code first 規則は、モデル内の最も一般的な関係を処理しますが、サポートが必要な場合もあります。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-243">Code first convention will take care of the most common relationships in your model, but there are some cases where it needs help.</span></span>

<span data-ttu-id="cf0dd-244">クラスのキープロパティの名前を変更すると、との `Blog` 関係に問題が発生しました `Post` 。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-244">Changing the name of the key property in the `Blog` class created a problem with its relationship to `Post`.</span></span> 

<span data-ttu-id="cf0dd-245">データベースを生成するとき、code first `BlogId` は Post クラスのプロパティを確認し、クラス名と **Id**が一致するという規則によって、クラスに対する外部キーとして認識し `Blog` ます。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-245">When generating the database, code first sees the `BlogId` property in the Post class and recognizes it, by the convention that it matches a class name plus **Id**, as a foreign key to the `Blog` class.</span></span> <span data-ttu-id="cf0dd-246">ただし `BlogId` 、ブログクラスにはプロパティがありません。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-246">But there is no `BlogId` property in the blog class.</span></span> <span data-ttu-id="cf0dd-247">これを解決するには、でナビゲーションプロパティを作成 `Post` し、dataannotation を使用して、 `ForeignKey` (プロパティを使用して) 2 つのクラス間のリレーションシップを構築する方法 `Post.BlogId` と、データベースで制約を指定する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-247">The solution for this is to create a navigation property in the `Post` and use the `ForeignKey` DataAnnotation to help code first understand how to build the relationship between the two classes (using the `Post.BlogId` property) as well as how to specify constraints in the database.</span></span>

``` csharp
    public class Post
    {
            public int Id { get; set; }
            public string Title { get; set; }
            public DateTime DateCreated { get; set; }
            public string Content { get; set; }
            public int BlogId { get; set; }
            [ForeignKey("BlogId")]
            public Blog Blog { get; set; }
            public ICollection<Comment> Comments { get; set; }
    }
```

<span data-ttu-id="cf0dd-248">データベース内の制約によって、との間のリレーションシップが表示され `InternalBlogs.PrimaryTrackingKey` `Posts.BlogId` ます。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-248">The constraint in the database shows a relationship between `InternalBlogs.PrimaryTrackingKey` and `Posts.BlogId`.</span></span> 

![InternalBlogs. PrimaryTrackingKey と Post. blog Id のリレーションシップ](~/ef6/media/jj591583-figure09.png)

<span data-ttu-id="cf0dd-250">は、 `InverseProperty` クラス間に複数のリレーションシップがある場合に使用されます。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-250">The `InverseProperty` is used when you have multiple relationships between classes.</span></span>

<span data-ttu-id="cf0dd-251">クラスでは、だれがブログ投稿を作成したか、およびだれが編集したかを `Post` 追跡することができます。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-251">In the `Post` class, you may want to keep track of who wrote a blog post as well as who edited it.</span></span> <span data-ttu-id="cf0dd-252">Post クラスの2つの新しいナビゲーションプロパティを次に示します。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-252">Here are two new navigation properties for the Post class.</span></span>

``` csharp
    public Person CreatedBy { get; set; }
    public Person UpdatedBy { get; set; }
```

<span data-ttu-id="cf0dd-253">また、 `Person` これらのプロパティで参照されるクラスを追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-253">You’ll also need to add in the `Person` class referenced by these properties.</span></span> <span data-ttu-id="cf0dd-254">`Person`クラスは、ナビゲーションプロパティをに戻し `Post` ます。1つはユーザーが作成したすべての投稿、もう1つはそのユーザーが更新したすべての投稿を対象とします。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-254">The `Person` class has navigation properties back to the `Post`, one for all of the posts written by the person and one for all of the posts updated by that person.</span></span>

``` csharp
    public class Person
    {
            public int Id { get; set; }
            public string Name { get; set; }
            public List<Post> PostsWritten { get; set; }
            public List<Post> PostsUpdated { get; set; }
    }
```

<span data-ttu-id="cf0dd-255">Code first では、2つのクラスのプロパティを独自に一致させることはできません。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-255">Code first is not able to match up the properties in the two classes on its own.</span></span> <span data-ttu-id="cf0dd-256">のデータベーステーブルには、person の `Posts` 外部キーが1つずつ含まれている必要がありますが、code first では、person `CreatedBy` `UpdatedBy` \*\* \_ id\*\*、 **Person \_ Id1**、 **CreatedBy \_ id** 、および **updatedby \_ id**という4つの外部キープロパティが作成されます。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-256">The database table for `Posts` should have one foreign key for the `CreatedBy` person and one for the `UpdatedBy` person but code first will create four foreign key properties: **Person\_Id**, **Person\_Id1**, **CreatedBy\_Id** and **UpdatedBy\_Id**.</span></span>

![余分な外部キーを含む post テーブル](~/ef6/media/jj591583-figure10.png)

<span data-ttu-id="cf0dd-258">これらの問題を修正するには、注釈を使用して `InverseProperty` プロパティの配置を指定します。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-258">To fix these problems, you can use the `InverseProperty` annotation to specify the alignment of the properties.</span></span>

``` csharp
    [InverseProperty("CreatedBy")]
    public List<Post> PostsWritten { get; set; }

    [InverseProperty("UpdatedBy")]
    public List<Post> PostsUpdated { get; set; }
```

<span data-ttu-id="cf0dd-259">`PostsWritten`Person のプロパティは、このが型を参照していることを認識しているので、 `Post` へのリレーションシップを構築し `Post.CreatedBy` ます。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-259">Because the `PostsWritten` property in Person knows that this refers to the `Post` type, it will build the relationship to `Post.CreatedBy`.</span></span> <span data-ttu-id="cf0dd-260">同様 `PostsUpdated` に、はに接続され `Post.UpdatedBy` ます。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-260">Similarly, `PostsUpdated` will be connected to `Post.UpdatedBy`.</span></span> <span data-ttu-id="cf0dd-261">また、code first では余分な外部キーは作成されません。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-261">And code first will not create the extra foreign keys.</span></span>

![余分な外部キーを持たない投稿テーブル](~/ef6/media/jj591583-figure11.png)

 

## <a name="summary"></a><span data-ttu-id="cf0dd-263">まとめ</span><span class="sxs-lookup"><span data-stu-id="cf0dd-263">Summary</span></span>

<span data-ttu-id="cf0dd-264">DataAnnotations では、クライアントとサーバー側の検証をコードの最初のクラスで記述できるだけでなく、規則に基づいて、コードがクラスに対して最初に実行する想定を強化し、修正することもできます。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-264">DataAnnotations not only let you describe client and server side validation in your code first classes, but they also allow you to enhance and even correct the assumptions that code first will make about your classes based on its conventions.</span></span> <span data-ttu-id="cf0dd-265">DataAnnotations を使用すると、データベーススキーマの生成だけでなく、コードの最初のクラスを既存のデータベースにマップすることもできます。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-265">With DataAnnotations you can not only drive database schema generation, but you can also map your code first classes to a pre-existing database.</span></span>

<span data-ttu-id="cf0dd-266">非常に柔軟性がありますが、DataAnnotations は、コードの最初のクラスに対して実行できる最も一般的な構成の変更のみを提供することに注意してください。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-266">While they are very flexible, keep in mind that DataAnnotations provide only the most commonly needed configuration changes you can make on your code first classes.</span></span> <span data-ttu-id="cf0dd-267">一部のエッジケースでクラスを構成するには、代替の構成メカニズムである Code First の Fluent API を参照する必要があります。</span><span class="sxs-lookup"><span data-stu-id="cf0dd-267">To configure your classes for some of the edge cases, you should look to the alternate configuration mechanism, Code First’s Fluent API .</span></span>
