---
title: コードの最初のデータ注釈 - EF6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: 80abefbd-23c9-4fce-9cd3-520e5df9856e
caps.latest.revision: 3
ms.openlocfilehash: 91d1d8c2608df8f7b38e70b565a4225cf10ae21f
ms.sourcegitcommit: bdd06c9a591ba5e6d6a3ec046c80de98f598f3f3
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2018
ms.locfileid: "39122802"
---
# <a name="code-first-data-annotations"></a><span data-ttu-id="f1fdf-102">Code First のデータ注釈</span><span class="sxs-lookup"><span data-stu-id="f1fdf-102">Code First Data Annotations</span></span>
> [!NOTE]
> <span data-ttu-id="f1fdf-103">**EF4.1 以降のみ**-機能、Api、Entity Framework 4.1 で導入されたなどのこのページで説明します。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-103">**EF4.1 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 4.1.</span></span> <span data-ttu-id="f1fdf-104">以前のバージョンを使用している場合、一部またはすべての情報は適用されません。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-104">If you are using an earlier version, some or all of the information does not apply.</span></span>

<span data-ttu-id="f1fdf-105">このページのコンテンツの出典元し、Julie Lerman が最初に書き込まれた情報の記事 (\<http://thedatafarm.com>)します。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-105">The content on this page is adapted from and article originally written by Julie Lerman (\<http://thedatafarm.com>).</span></span>

<span data-ttu-id="f1fdf-106">Entity Framework Code First EF が、クエリを実行に依存するモデルを表す独自のドメイン クラスを使用できるように追跡と変更関数を更新しています。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-106">Entity Framework Code First allows you to use your own domain classes to represent the model which EF relies on to perform querying, change tracking and updating functions.</span></span> <span data-ttu-id="f1fdf-107">コードは、最初の構成に規則と呼ばれるプログラミング パターンを活用します。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-107">Code first leverages a programming pattern referred to as convention over configuration.</span></span> <span data-ttu-id="f1fdf-108">場合は、そのコードはまず、クラスが EF で使用される規則に従うこと。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-108">What this means is that code first will assume that your classes follow the conventions that EF uses.</span></span> <span data-ttu-id="f1fdf-109">その場合は、EF は、ジョブを実行する必要がある詳細を使用することになります。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-109">In that case, EF will be able to work out the details it needs to do its job.</span></span> <span data-ttu-id="f1fdf-110">ただし、クラスは、これらの規則に従っていない、構成に必要な情報に EF を提供するクラスを追加する機能があります。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-110">However, if your classes do not follow those conventions, you have the ability to add configurations to your classes to provide EF with the information it needs.</span></span>

<span data-ttu-id="f1fdf-111">コードは 2 つの方法でこれらの構成をクラスに追加します。 まず、します。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-111">Code first gives you two ways to add these configurations to your classes.</span></span> <span data-ttu-id="f1fdf-112">DataAnnotations と呼ばれる単純な属性を使用して 1 つは、もう一方は Fluent API 構成を強制的に、コードで記述する方法を提供するには最初にコードを使用します。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-112">One is using simple attributes called DataAnnotations and the other is using code first’s Fluent API, which provides you with a way to describe configurations imperatively, in code.</span></span>

<span data-ttu-id="f1fdf-113">この記事では、System.ComponentModel.DataAnnotations 名前空間) の「DataAnnotations を使用して、最も一般的に必要な構成を強調表示、のクラスを構成する説明します。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-113">This article will focus on using DataAnnotations (in the System.ComponentModel.DataAnnotations namespace) to configure your classes – highlighting the most commonly needed configurations.</span></span> <span data-ttu-id="f1fdf-114">DataAnnotations がこれらのアプリケーションのクライアント側の検証のための同じ注釈を活用できる ASP.NET MVC などの .NET アプリケーションの番号で考えることもできます。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-114">DataAnnotations are also understood by a number of .NET applications, such as ASP.NET MVC which allows these applications to leverage the same annotations for client-side validations.</span></span>


## <a name="the-model"></a><span data-ttu-id="f1fdf-115">モデル</span><span class="sxs-lookup"><span data-stu-id="f1fdf-115">The model</span></span>

<span data-ttu-id="f1fdf-116">紹介しますコード クラスの単純なペアの最初の DataAnnotations: ブログや投稿します。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-116">I’ll demonstrate code first DataAnnotations with a simple pair of classes: Blog and Post.</span></span>

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

<span data-ttu-id="f1fdf-117">ブログや投稿クラスは簡単にコードの最初の規則に従うし、EF とその操作を調整する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-117">As they are, the Blog and Post classes conveniently follow code first convention and required no tweaks to help EF work with them.</span></span> <span data-ttu-id="f1fdf-118">ただし、EF に、クラスとがマップされるデータベースについての詳細を提供する、注釈を使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-118">But you can also use the annotations to provide more information to EF about the classes and the database that they map to.</span></span>

 

## <a name="key"></a><span data-ttu-id="f1fdf-119">キー</span><span class="sxs-lookup"><span data-stu-id="f1fdf-119">Key</span></span>

<span data-ttu-id="f1fdf-120">Entity Framework は、エンティティの追跡に使用するキー値を持つすべてのエンティティに依存します。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-120">Entity Framework relies on every entity having a key value that it uses for tracking entities.</span></span> <span data-ttu-id="f1fdf-121">コードは、最初に依存している表記規則の 1 つは、どのプロパティは、コードの最初のクラスの各キーを意味する方法です。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-121">One of the conventions that code first depends on is how it implies which property is the key in each of the code first classes.</span></span> <span data-ttu-id="f1fdf-122">その規則は、"Id"または"BlogId"など"Id", とクラスの名前とを組み合わせたものをという名前のプロパティを検索します。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-122">That convention is to look for a property named “Id” or one that combines the class name and “Id”, such as “BlogId”.</span></span> <span data-ttu-id="f1fdf-123">プロパティは、データベースの主キー列にマップされます。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-123">The property will map to a primary key column in the database.</span></span>

<span data-ttu-id="f1fdf-124">ブログと Post の両方のクラスでは、この規則に従います。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-124">The Blog and Post classes both follow this convention.</span></span> <span data-ttu-id="f1fdf-125">しかししていない場合でしょうか。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-125">But what if they didn’t?</span></span> <span data-ttu-id="f1fdf-126">ブログで名前を使用する場合*PrimaryTrackingKey*代わりに、またはでも*foo*でしょうか。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-126">What if Blog used the name *PrimaryTrackingKey* instead or even *foo*?</span></span> <span data-ttu-id="f1fdf-127">コードが最初にこの規則に一致するプロパティでは見つからない場合は、キー プロパティに必要な Entity Framework の要件のため例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-127">If code first does not find a property that matches this convention it will throw an exception because of Entity Framework’s requirement that you must have a key property.</span></span> <span data-ttu-id="f1fdf-128">キーの注釈を使用すると、EntityKey として使用するのにプロパティを指定します。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-128">You can use the key annotation to specify which property is to be used as the EntityKey.</span></span>

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

<span data-ttu-id="f1fdf-129">する場合はデータベースの生成機能は、最初にコードを使用して、ブログの表に、既定では、Id としても定義されている PrimaryTrackingKey という名前の主キー列になります。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-129">If you are using code first’s database generation feature, the Blog table will have a primary key column named PrimaryTrackingKey which is also defined as Identity by default.</span></span>

![jj591583_figure01](~/ef6/media/jj591583-figure01.png)

### <a name="composite-keys"></a><span data-ttu-id="f1fdf-131">複合キー</span><span class="sxs-lookup"><span data-stu-id="f1fdf-131">Composite keys</span></span>

<span data-ttu-id="f1fdf-132">Entity Framework には、複合キーは、1 つ以上のプロパティで構成される主キーがサポートしています。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-132">Entity Framework supports composite keys - primary keys that consist of more than one property.</span></span> <span data-ttu-id="f1fdf-133">たとえばのプライマリ キーを持つ PassportNumber と IssuingCountry の組み合わせは、Passport クラスがある可能性があります。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-133">For example, your could have a Passport class whose primary key is a combination of PassportNumber and IssuingCountry.</span></span>

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

<span data-ttu-id="f1fdf-134">InvalidOperationExceptions という、; を取得するモデルに上記のクラスを使用した場合</span><span class="sxs-lookup"><span data-stu-id="f1fdf-134">If you were to try and use the above class in your EF model you wuld get an InvalidOperationExceptions stating;</span></span>

<span data-ttu-id="f1fdf-135">*複合主キーの順序付けの種類 'Passport' を決定できません。複合主キーの順序を指定するのに、ColumnAttribute または HasKey メソッドを使用します。*</span><span class="sxs-lookup"><span data-stu-id="f1fdf-135">*Unable to determine composite primary key ordering for type 'Passport'. Use the ColumnAttribute or the HasKey method to specify an order for composite primary keys.*</span></span>

<span data-ttu-id="f1fdf-136">複合キーがある場合は、Entity Framework では、キーのプロパティの順序を定義する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-136">When you have composite keys, Entity Framework requires you to define an order of the key properties.</span></span> <span data-ttu-id="f1fdf-137">順序を指定する列の注釈を使用してこれが実行できます。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-137">You can do this using the Column annotation to specify an order.</span></span>

>[!NOTE]
> <span data-ttu-id="f1fdf-138">順序の値は相対 (なくインデックス ベース) 任意の値を使用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-138">The order value is relative (rather than index based) so any values can be used.</span></span> <span data-ttu-id="f1fdf-139">たとえば、100 および 200 は 1 と 2 の代わりに許容になります。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-139">For example, 100 and 200 would be acceptable in place of 1 and 2.</span></span>

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

<span data-ttu-id="f1fdf-140">複合外部キーを持つエンティティがある場合は、同じ列の対応するプライマリ キーのプロパティに使用する順序を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-140">If you have entities with composite foreign keys then you must specify the same column ordering that you used for the corresponding primary key properties.</span></span>

<span data-ttu-id="f1fdf-141">同じである正確な値に割り当てられている必要があります、相対的な順序付けのみ外部キー プロパティ内で**順序**と一致する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-141">Only the relative ordering within the foreign key properties needs to be the same, the exact values assigned to **Order** do not need to match.</span></span> <span data-ttu-id="f1fdf-142">たとえば、次のクラスで 3 と 4 される可能性があります 1 と 2 の代わりにします。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-142">For example, in the following class, 3 and 4 could be used in place of 1 and 2.</span></span>

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

## <a name="required"></a><span data-ttu-id="f1fdf-143">必須</span><span class="sxs-lookup"><span data-stu-id="f1fdf-143">Required</span></span>

<span data-ttu-id="f1fdf-144">必要な注釈は、特定のプロパティが必要である EF に指示します。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-144">The Required annotation tells EF that a particular property is required.</span></span>

<span data-ttu-id="f1fdf-145">Title プロパティに必要な追加すると、プロパティは、これでデータを持つようにするための EF (および MVC) が実行されます。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-145">Adding Required to the Title property will force EF (and MVC) to ensure that the property has data in it.</span></span>

``` csharp
    [Required]
    public string Title { get; set; }
```

<span data-ttu-id="f1fdf-146">ない追加のないアプリケーションのコードやマークアップを変更、MVC アプリケーションのプロパティおよび注釈の名前を使用してメッセージの構築も動的に、クライアント側の検証は実行します。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-146">With no additional no code or markup changes in the application, an MVC application will perform client side validation, even dynamically building a message using the property and annotation names.</span></span>

![jj591583_figure02](~/ef6/media/jj591583-figure02.png)

<span data-ttu-id="f1fdf-148">必須の属性には、マッピングされたプロパティを null 非許容のことで生成されたデータベースも影響します。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-148">The Required attribute will also affect the generated database by making the mapped property non-nullable.</span></span> <span data-ttu-id="f1fdf-149">[タイトル] フィールドが"not null"に変更されていることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-149">Notice that the Title field has changed to “not null”.</span></span>

>[!NOTE]
> <span data-ttu-id="f1fdf-150">場合によっては、プロパティが必要な場合でも、null 非許容されるデータベース内の列の可能なないられます。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-150">In some cases it may not be possible for the column in the database to be non-nullable even though the property is required.</span></span> <span data-ttu-id="f1fdf-151">たとえば、TPH 継承の戦略のデータを複数の種類を使用してが格納されている場合、1 つのテーブル。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-151">For example, when using a TPH inheritance strategy data for multiple types is stored in a single table.</span></span> <span data-ttu-id="f1fdf-152">派生型に必要なプロパティが含まれている場合、列にできない null 非許容のこのプロパティは、階層内のすべての型であるためです。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-152">If a derived type includes a required property the column cannot be made non-nullable since not all types in the hierarchy will have this property.</span></span>

 

![jj591583_figure03](~/ef6/media/jj591583-figure03.png)

 

## <a name="maxlength-and-minlength"></a><span data-ttu-id="f1fdf-154">MaxLength、MinLength</span><span class="sxs-lookup"><span data-stu-id="f1fdf-154">MaxLength and MinLength</span></span>

<span data-ttu-id="f1fdf-155">MaxLength、MinLength 属性を必須にしたのと同様、追加のプロパティの検証を指定できます。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-155">The MaxLength and MinLength attributes allow you to specify additional property validations, just as you did with Required.</span></span>

<span data-ttu-id="f1fdf-156">長さの要件と BloggerName を次に示します。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-156">Here is the BloggerName with length requirements.</span></span> <span data-ttu-id="f1fdf-157">この例では、属性を結合する方法も示します。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-157">The example also demonstrates how to combine attributes.</span></span>

``` csharp
    [MaxLength(10),MinLength(5)]
    public string BloggerName { get; set; }
```

<span data-ttu-id="f1fdf-158">MaxLength 注釈は、プロパティの長さを 10 に設定して、データベースに影響します。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-158">The MaxLength annotation will impact the database by setting the property’s length to 10.</span></span>

![jj591583_figure04](~/ef6/media/jj591583-figure04.png)

<span data-ttu-id="f1fdf-160">クライアント側の注釈を MVC と EF 4.1 サーバー側の注釈は両方に従ってこの検証でエラー メッセージを動的にもう一度ビルド:「BloggerName フィールドは、'10' の最大長の文字列または配列型をある必要があります」そのメッセージは、少し長くします。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-160">MVC client-side annotation and EF 4.1 server-side annotation will both honor this validation, again dynamically building an error message: “The field BloggerName must be a string or array type with a maximum length of '10'.” That message is a little long.</span></span> <span data-ttu-id="f1fdf-161">多くの注釈を使用して、エラー メッセージの属性を持つエラー メッセージを指定できます。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-161">Many annotations let you specify an error message with the ErrorMessage attribute.</span></span>

``` csharp
    [MaxLength(10, ErrorMessage="BloggerName must be 10 characters or less"),MinLength(5)]
    public string BloggerName { get; set; }
```

<span data-ttu-id="f1fdf-162">注釈が必要なエラー メッセージを指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-162">You can also specify ErrorMessage in the Required annotation.</span></span>

![jj591583_figure05](~/ef6/media/jj591583-figure05.png)

 

## <a name="notmapped"></a><span data-ttu-id="f1fdf-164">NotMapped</span><span class="sxs-lookup"><span data-stu-id="f1fdf-164">NotMapped</span></span>

<span data-ttu-id="f1fdf-165">コードの最初の規則は、サポートされているデータ型のすべてのプロパティが、データベースで表されていることを決定します。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-165">Code first convention dictates that every property that is of a supported data type is represented in the database.</span></span> <span data-ttu-id="f1fdf-166">これは、アプリケーション内のケースでは常にします。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-166">But this isn’t always the case in your applications.</span></span> <span data-ttu-id="f1fdf-167">たとえば、タイトルと BloggerName フィールドに基づくコードを作成するブログ クラスでプロパティがある可能性があります。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-167">For example you might have a property in the Blog class that creates a code based on the Title and BloggerName fields.</span></span> <span data-ttu-id="f1fdf-168">このプロパティは、動的に作成できるし、格納する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-168">That property can be created dynamically and does not need to be stored.</span></span> <span data-ttu-id="f1fdf-169">この BlogCode プロパティなど NotMapped 注釈を使用してデータベースにマップされていない任意のプロパティをマークすることができます。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-169">You can mark any properties that do not map to the database with the NotMapped annotation such as this BlogCode property.</span></span>

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

 

## <a name="complextype"></a><span data-ttu-id="f1fdf-170">ComplexType</span><span class="sxs-lookup"><span data-stu-id="f1fdf-170">ComplexType</span></span>

<span data-ttu-id="f1fdf-171">一連のクラス間で、ドメイン エンティティを記述し、レイヤーの完全なエンティティを記述するそれらのクラスに珍しくありません。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-171">It’s not uncommon to describe your domain entities across a set of classes and then layer those classes to describe a complete entity.</span></span> <span data-ttu-id="f1fdf-172">たとえば、モデルに BlogDetails と呼ばれるクラスを追加する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-172">For example, you may add a class called BlogDetails to your model.</span></span>

``` csharp
    public class BlogDetails
    {
        public DateTime? DateCreated { get; set; }

        [MaxLength(250)]
        public string Description { get; set; }
    }
```

<span data-ttu-id="f1fdf-173">BlogDetails にあらゆる種類のキー プロパティがないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-173">Notice that BlogDetails does not have any type of key property.</span></span> <span data-ttu-id="f1fdf-174">ドメイン駆動設計では、BlogDetails を値オブジェクトと呼びます。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-174">In domain driven design, BlogDetails is referred to as a value object.</span></span> <span data-ttu-id="f1fdf-175">Entity Framework は、複合型として値オブジェクトを参照します。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-175">Entity Framework refers to value objects as complex types.</span></span>  <span data-ttu-id="f1fdf-176">独自の複合型を追跡することはできません。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-176">Complex types cannot be tracked on their own.</span></span>

<span data-ttu-id="f1fdf-177">ただしブログ クラス、BlogDetails がブログ オブジェクトの一部として追跡のプロパティとして。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-177">However as a property in the Blog class, BlogDetails it will be tracked as part of a Blog object.</span></span> <span data-ttu-id="f1fdf-178">これを認識する最初のコードで、ComplexType として BlogDetails クラスをマークする必要があります。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-178">In order for code first to recognize this, you must mark the BlogDetails class as a ComplexType.</span></span>

``` csharp
    [ComplexType]
    public class BlogDetails
    {
        public DateTime? DateCreated { get; set; }

        [MaxLength(250)]
        public string Description { get; set; }
    }
```

<span data-ttu-id="f1fdf-179">今すぐそのブログ BlogDetails を表すブログ クラスでプロパティを追加できます。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-179">Now you can add a property in the Blog class to represent the BlogDetails for that blog.</span></span>

``` csharp
        public BlogDetails BlogDetail { get; set; }
```

<span data-ttu-id="f1fdf-180">データベースには、ブログの表に、その BlogDetail プロパティに含まれるプロパティを含む、ブログのプロパティのすべて含まれます。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-180">In the database, the Blog table will contain all of the properties of the blog including the properties contained in its BlogDetail property.</span></span> <span data-ttu-id="f1fdf-181">既定では、それぞれが付きます BlogDetail、複合型の名前。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-181">By default, each one is preceded with the name of the complex type, BlogDetail.</span></span>

![jj591583_figure06](~/ef6/media/jj591583-figure06.png)

<span data-ttu-id="f1fdf-183">もう 1 つの興味深いことには、DateCreated プロパティ、クラス内の null 非許容の日時として定義されますが、関連するデータベース フィールドを null 値を許容します。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-183">Another interesting note is that although the DateCreated property was defined as a non-nullable DateTime in the class, the relevant database field is nullable.</span></span> <span data-ttu-id="f1fdf-184">データベース スキーマに影響する場合は、必要な注釈を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-184">You must use the Required annotation if you wish to affect the database schema.</span></span>

 

## <a name="concurrencycheck"></a><span data-ttu-id="f1fdf-185">ConcurrencyCheck</span><span class="sxs-lookup"><span data-stu-id="f1fdf-185">ConcurrencyCheck</span></span>

<span data-ttu-id="f1fdf-186">ConcurrencyCheck 注釈の同時実行ユーザーが編集したり、エンティティを削除します。 データベースのチェックに使用する 1 つまたは複数のプロパティのフラグを設定することができます。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-186">The ConcurrencyCheck annotation allows you to flag one or more properties to be used for concurrency checking in the database when a user edits or deletes an entity.</span></span> <span data-ttu-id="f1fdf-187">EF Designer を使用していた場合、プロパティの ConcurrencyMode を Fixed に設定します。 これは。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-187">If you've been working with the EF Designer, this aligns with setting a property's ConcurrencyMode to Fixed.</span></span>

<span data-ttu-id="f1fdf-188">BloggerName プロパティに追加することによって ConcurrencyCheck のしくみを見てみましょう。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-188">Let’s see how ConcurrencyCheck works by adding it to the BloggerName property.</span></span>

``` csharp
    [ConcurrencyCheck, MaxLength(10, ErrorMessage="BloggerName must be 10 characters or less"),MinLength(5)]
    public string BloggerName { get; set; }
```

<span data-ttu-id="f1fdf-189">BloggerName フィールドで ConcurrencyCheck 注釈により、SaveChanges が呼び出されたときにそのプロパティの元の値が更新に使用されます。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-189">When SaveChanges is called, because of the ConcurrencyCheck annotation on the BloggerName field, the original value of that property will be used in the update.</span></span> <span data-ttu-id="f1fdf-190">コマンドが、キーの値だけでなく、BloggerName の元の値もフィルター処理して適切な行を検索しようとします。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-190">The command will attempt to locate the correct row by filtering not only on the key value but also on the original value of BloggerName.</span></span>  <span data-ttu-id="f1fdf-191">ここでは、コマンドには、PrimaryTrackingKey を含む行は更新を表示、データベースに送信される UPDATE コマンドの重要な部分は 1、BloggerName"Julie"元の値をそのブログがデータベースから取得されたときにいたのです。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-191">Here are the critical parts of the UPDATE command sent to the database, where you can see the command will update the row that has a PrimaryTrackingKey is 1 and a BloggerName of “Julie” which was the original value when that blog was retrieved from the database.</span></span>

``` SQL
    where (([PrimaryTrackingKey] = @4) and ([BloggerName] = @5))
    @4=1,@5=N'Julie'
```

<span data-ttu-id="f1fdf-192">そのブログのブログ作成者名が、その間にだれかが変更した場合は、この更新プログラムは失敗し、処理する必要がありますを DbUpdateConcurrencyException が表示されます。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-192">If someone has changed the blogger name for that blog in the meantime, this update will fail and you’ll get a DbUpdateConcurrencyException that you'll need to handle.</span></span>

 

## <a name="timestamp"></a><span data-ttu-id="f1fdf-193">タイムスタンプ</span><span class="sxs-lookup"><span data-stu-id="f1fdf-193">TimeStamp</span></span>

<span data-ttu-id="f1fdf-194">同時実行チェック用 rowversion またはタイムスタンプ フィールドを使用する方が一般的です。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-194">It's more common to use rowversion or timestamp fields for concurrency checking.</span></span> <span data-ttu-id="f1fdf-195">ConcurrencyCheck 注釈を使用するのではなく、プロパティの型がバイト配列である限り、特定のタイムスタンプの注釈を使用できます。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-195">But rather than using the ConcurrencyCheck annotation, you can use the more specific TimeStamp annotation as long as the type of the property is byte array.</span></span> <span data-ttu-id="f1fdf-196">コード最初はタイムスタンプのプロパティと同様に処理、ConcurrencyCheck プロパティが、コードを生成するデータベース フィールドを null 非許容は確認も。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-196">Code first will treat Timestamp properties the same as ConcurrencyCheck properties, but it will also ensure that the database field that code first generates is non-nullable.</span></span> <span data-ttu-id="f1fdf-197">タイムスタンプ プロパティを 1 つは、特定のクラスでのみができます。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-197">You can only have one timestamp property in a given class.</span></span>

<span data-ttu-id="f1fdf-198">ブログ クラスに次のプロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-198">Adding the following property to the Blog class:</span></span>

``` csharp
    [Timestamp]
    public Byte[] TimeStamp { get; set; }
```

<span data-ttu-id="f1fdf-199">最初にデータベース テーブルで null 非許容の timestamp 列を作成するコードの結果。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-199">results in code first creating a non-nullable timestamp column in the database table.</span></span>

![jj591583_figure07](~/ef6/media/jj591583-figure07.png)

 

## <a name="table-and-column"></a><span data-ttu-id="f1fdf-201">テーブルと列</span><span class="sxs-lookup"><span data-stu-id="f1fdf-201">Table and Column</span></span>

<span data-ttu-id="f1fdf-202">Code First のデータベースを作成することは、場合は、テーブルと列を作成しているの名前を変更することがあります。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-202">If you are letting Code First create the database, you may want to change the name of the tables and columns it is creating.</span></span> <span data-ttu-id="f1fdf-203">使用することできますも Code First 既存のデータベースでします。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-203">You can also use Code First with an existing database.</span></span> <span data-ttu-id="f1fdf-204">常にテーブルと、データベース内の列の名前をクラスと、ドメイン内のプロパティの名前が一致する場合。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-204">But it's not always the case that the names of the classes and properties in your domain match the names of the tables and columns in your database.</span></span>

<span data-ttu-id="f1fdf-205">筆者のクラスがブログをという名前し、慣例により、コードまず前提としていますこれは、ブログをという名前のテーブルにマップされます。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-205">My class is named Blog and by convention, code first presumes this will map to a table named Blogs.</span></span> <span data-ttu-id="f1fdf-206">そうでない場合は、テーブルの属性を持つテーブルの名前を指定できます。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-206">If that's not the case you can specify the name of the table with the Table attribute.</span></span> <span data-ttu-id="f1fdf-207">ここで、注釈はことを指定して、テーブル名 InternalBlogs。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-207">Here for example, the annotation is specifying that the table name is InternalBlogs.</span></span>

``` csharp
    [Table("InternalBlogs")]
    public class Blog
```

<span data-ttu-id="f1fdf-208">列の注釈は、マップされた列の属性を指定するときに複数のアデプトです。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-208">The Column annotation is a more adept in specifying the attributes of a mapped column.</span></span> <span data-ttu-id="f1fdf-209">名前、データ型またはテーブルの列が表示される順序もを伸ばすことができます。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-209">You can stipulate a name, data type or even the order in which a column appears in the table.</span></span> <span data-ttu-id="f1fdf-210">列の属性の例を示します。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-210">Here is an example of the Column attribute.</span></span>

``` csharp
    [Column(“BlogDescription", TypeName="ntext")]
    public String Description {get;set;}
```

<span data-ttu-id="f1fdf-211">DataType DataAnnotation で列の TypeName 属性を混同しないでください。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-211">Don’t confuse Column’s TypeName attribute with the DataType DataAnnotation.</span></span> <span data-ttu-id="f1fdf-212">データ型は、注釈、UI の使用は、Code First によって無視されます。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-212">DataType is an annotation used for the UI and is ignored by Code First.</span></span>

<span data-ttu-id="f1fdf-213">再生成後に次の表に示します。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-213">Here is the table after it’s been regenerated.</span></span> <span data-ttu-id="f1fdf-214">テーブル名が InternalBlogs に変更し、複合型から列を説明 BlogDescription になります。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-214">The table name has changed to InternalBlogs and Description column from the complex type is now BlogDescription.</span></span> <span data-ttu-id="f1fdf-215">注釈の名前が指定されたため、コード最初は使用しません複合型の名前で列名を開始する規約。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-215">Because the name was specified in the annotation, code first will not use the convention of starting the column name with the name of the complex type.</span></span>

![jj591583_figure08](~/ef6/media/jj591583-figure08.png)

 

## <a name="databasegenerated"></a><span data-ttu-id="f1fdf-217">DatabaseGenerated</span><span class="sxs-lookup"><span data-stu-id="f1fdf-217">DatabaseGenerated</span></span>

<span data-ttu-id="f1fdf-218">重要なデータベース機能は、プロパティが計算する機能があります。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-218">An important database features is the ability to have computed properties.</span></span> <span data-ttu-id="f1fdf-219">Code First クラスにマッピングする場合は、計算列のテーブルが含まれている、それらの列を更新しようとする Entity Framework をしたくないです。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-219">If you're mapping your Code First classes to tables that contain computed columns, you don't want Entity Framework to try to update those columns.</span></span> <span data-ttu-id="f1fdf-220">ただし、EF を挿入または更新されたデータを使用した後、データベースからそれらの値を返すようにします。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-220">But you do want EF to return those values from the database after you've inserted or updated data.</span></span> <span data-ttu-id="f1fdf-221">DatabaseGenerated 注釈を使用すると、計算済みの列挙型と共に、クラスでこれらのプロパティのフラグを設定します。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-221">You can use the DatabaseGenerated annotation to flag those properties in your class along with the Computed enum.</span></span> <span data-ttu-id="f1fdf-222">他の列挙型は None と Id。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-222">Other enums are None and Identity.</span></span>

``` csharp
    [DatabaseGenerated(DatabaseGenerationOption.Computed)]
    public DateTime DateCreated { get; set; }
```

<span data-ttu-id="f1fdf-223">それ以外の場合にのみ使用これコード最初ことはできません、計算列の数式を判断するため、既存のデータベースをポイントして、コードはまず、データベースを生成するときに、バイトまたはタイムスタンプ列で生成されたデータベースを使用できます。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-223">You can use database generated on byte or timestamp columns when code first is generating the database, otherwise you should only use this when pointing to existing databases because code first won't be able to determine the formula for the computed column.</span></span>

<span data-ttu-id="f1fdf-224">既定では、読むことを整数であるキー プロパティは、データベースの id キーなります。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-224">You read above that by default, a key property that is an integer will become an identity key in the database.</span></span> <span data-ttu-id="f1fdf-225">DatabaseGenerated を DatabaseGenerationOption.Identity に設定すると同じメッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-225">That would be the same as setting DatabaseGenerated to DatabaseGenerationOption.Identity.</span></span> <span data-ttu-id="f1fdf-226">Id キーを使用することをしない場合は、DatabaseGenerationOption.None に値を設定できます。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-226">If you do not want it to be an identity key, you can set the value to DatabaseGenerationOption.None.</span></span>

 

## <a name="index"></a><span data-ttu-id="f1fdf-227">インデックス</span><span class="sxs-lookup"><span data-stu-id="f1fdf-227">Index</span></span>

> [!NOTE]
> <span data-ttu-id="f1fdf-228">**EF6.1 以降のみ**-Entity Framework 6.1 で、インデックスの属性が導入されました。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-228">**EF6.1 Onwards Only** - The Index attribute was introduced in Entity Framework 6.1.</span></span> <span data-ttu-id="f1fdf-229">以前のバージョンを使用している場合、このセクションの情報は適用されません。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-229">If you are using an earlier version the information in this section does not apply.</span></span>

<span data-ttu-id="f1fdf-230">使用して 1 つまたは複数の列にインデックスを作成することができます、 **IndexAttribute**します。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-230">You can create an index on one or more columns using the **IndexAttribute**.</span></span> <span data-ttu-id="f1fdf-231">データベースの作成時に、データベースに対応するインデックスの作成に EF を原因またはスキャフォールディング、対応する属性を 1 つまたは複数のプロパティに追加**CreateIndex** Code First Migrations を使用している場合に呼び出します。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-231">Adding the attribute to one or more properties will cause EF to create the corresponding index in the database when it creates the database, or scaffold the corresponding **CreateIndex** calls if you are using Code First Migrations.</span></span>

<span data-ttu-id="f1fdf-232">たとえば、次のコードが作成されたインデックスになります。、**評価**の列、**投稿**データベース内のテーブル。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-232">For example, the following code will result in an index being created on the **Rating** column of the **Posts** table in the database.</span></span>

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

<span data-ttu-id="f1fdf-233">既定では、インデックスの名前は**IX\_&lt;プロパティ名&gt;** (IX\_上の例では評価)。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-233">By default, the index will be named **IX\_&lt;property name&gt;** (IX\_Rating in the above example).</span></span> <span data-ttu-id="f1fdf-234">ただし、インデックスの名前も指定できます。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-234">You can also specify a name for the index though.</span></span> <span data-ttu-id="f1fdf-235">次の例では、インデックスに名前を付けることを指定します**PostRatingIndex**します。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-235">The following example specifies that the index should be named **PostRatingIndex**.</span></span>

``` csharp
    [Index("PostRatingIndex")]
    public int Rating { get; set; }
```

<span data-ttu-id="f1fdf-236">既定では、インデックスが一意でない、使用することが、 **IsUnique**という名前のインデックスが一意であることを指定するパラメーター。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-236">By default, indexes are non-unique, but you can use the **IsUnique** named parameter to specify that an index should be unique.</span></span> <span data-ttu-id="f1fdf-237">次の例の一意のインデックスが導入されて、**ユーザー**のログイン名。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-237">The following example introduces a unique index on a **User**'s login name.</span></span>

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

### <a name="multiple-column-indexes"></a><span data-ttu-id="f1fdf-238">複数列のインデックス</span><span class="sxs-lookup"><span data-stu-id="f1fdf-238">Multiple-Column Indexes</span></span>

<span data-ttu-id="f1fdf-239">複数の列にまたがるインデックスは、指定されたテーブルの複数のインデックス注釈内で同じ名前を使用して指定されます。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-239">Indexes that span multiple columns are specified by using the same name in multiple Index annotations for a given table.</span></span> <span data-ttu-id="f1fdf-240">複数列のインデックスを作成するときに、インデックスの列の順序を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-240">When you create multi-column indexes, you need to specify an order for the columns in the index.</span></span> <span data-ttu-id="f1fdf-241">次のコードが複数列インデックスを作成するなど、**評価**と**BlogId**と呼ばれる**IX\_BlogAndRating**します。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-241">For example, the following code creates a multi-column index on **Rating** and **BlogId** called **IX\_BlogAndRating**.</span></span> <span data-ttu-id="f1fdf-242">**BlogId**は、インデックスの最初の列と**評価**2 つ目は、します。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-242">**BlogId** is the first column in the index and **Rating** is the second.</span></span>

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

 

## <a name="relationship-attributes-inverseproperty-and-foreignkey"></a><span data-ttu-id="f1fdf-243">リレーションシップは、次の属性: InverseProperty と不変</span><span class="sxs-lookup"><span data-stu-id="f1fdf-243">Relationship Attributes: InverseProperty and ForeignKey</span></span>

> [!NOTE]
> <span data-ttu-id="f1fdf-244">このページは、データ注釈を使用して Code First モデルでリレーションシップを設定する方法についての情報を提供します。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-244">This page provides information about setting up relationships in your Code First model using Data Annotations.</span></span> <span data-ttu-id="f1fdf-245">EF とアクセスし、リレーションシップを使用してデータを操作する方法に関係の詳細については、次を参照してください[リレーションシップとナビゲーション プロパティ](~/ef6/fundamentals/relationships.md)。 \*。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-245">For general information about relationships in EF and how to access and manipulate data using relationships, see [Relationships & Navigation Properties](~/ef6/fundamentals/relationships.md).\*</span></span>

<span data-ttu-id="f1fdf-246">コードの最初の規則が、モデル内の最も一般的なリレーションシップの処理が、場合によってはヘルプが必要な場所があります。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-246">Code first convention will take care of the most common relationships in your model, but there are some cases where it needs help.</span></span>

<span data-ttu-id="f1fdf-247">投稿との関係に問題を作成したブログ クラスのキー プロパティの名前を変更します。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-247">Changing the name of the key property in the Blog class created a problem with its relationship to Post.</span></span> 

<span data-ttu-id="f1fdf-248">データベースを生成するときに、コードはまず Post クラスで BlogId プロパティを表示しと一致する、クラス名と"Id"ブログ クラスへの外部キーとして規則によりを認識します。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-248">When generating the database, code first sees the BlogId property in the Post class and recognizes it, by the convention that it matches a class name plus “Id”, as a foreign key to the Blog class.</span></span> <span data-ttu-id="f1fdf-249">ブログのクラスで BlogId プロパティはありません。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-249">But there is no BlogId property in the blog class.</span></span> <span data-ttu-id="f1fdf-250">このソリューションは、投稿にナビゲーション プロパティを作成し、コードの最初の 2 つのクラス間のリレーションシップを構築する方法を理解する外部 DataAnnotation を使用して、Post.BlogId プロパティを使用して、内の制約を指定する方法についても、データベース。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-250">The solution for this is to create a navigation property in the Post and use the Foreign DataAnnotation to help code first understand how to build the relationship between the two classes —using the Post.BlogId property — as well as how to specify constraints in the database.</span></span>

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

<span data-ttu-id="f1fdf-251">データベース内の制約は、InternalBlogs.PrimaryTrackingKey と Posts.BlogId 間の関係を示しています。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-251">The constraint in the database shows a relationship between InternalBlogs.PrimaryTrackingKey and Posts.BlogId.</span></span> 

![jj591583_figure09](~/ef6/media/jj591583-figure09.png)

<span data-ttu-id="f1fdf-253">クラス間の複数のリレーションシップがある場合、InverseProperty が使用されます。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-253">The InverseProperty is used when you have multiple relationships between classes.</span></span>

<span data-ttu-id="f1fdf-254">ブログの投稿の作成者を追跡するのにすることがあります Post クラスで編集したユーザーとします。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-254">In the Post class, you may want to keep track of who wrote a blog post as well as who edited it.</span></span> <span data-ttu-id="f1fdf-255">ここでは、Post クラスの新しい 2 つのナビゲーション プロパティです。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-255">Here are two new navigation properties for the Post class.</span></span>

``` csharp
    public Person CreatedBy { get; set; }
    public Person UpdatedBy { get; set; }
```

<span data-ttu-id="f1fdf-256">また、これらのプロパティによって参照されている Person クラスに追加する必要もあります。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-256">You’ll also need to add in the Person class referenced by these properties.</span></span> <span data-ttu-id="f1fdf-257">Person クラスには、すべてのユーザーとすべての人が更新投稿の 1 つによって書き込まれた投稿の 1 つ、Post にナビゲーション プロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-257">The Person class has navigation properties back to the Post, one for all of the posts written by the person and one for all of the posts updated by that person.</span></span>

``` csharp
    public class Person
    {
            public int Id { get; set; }
            public string Name { get; set; }
            public List<Post> PostsWritten { get; set; }
            public List<Post> PostsUpdated { get; set; }
    }
```

<span data-ttu-id="f1fdf-258">最初にコードは、独自の 2 つのクラスのプロパティと一致することではありません。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-258">Code first is not able to match up the properties in the two classes on its own.</span></span> <span data-ttu-id="f1fdf-259">投稿のデータベース テーブルでは、CreatedBy 人の 1 つの外部キーが必要し、UpdatedBy 人がコードの最初として 4 つは外部キー プロパティを作成します: Person\_Id、Person\_Id1、CreatedBy\_Id とUpdatedBy\_id。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-259">The database table for Posts should have one foreign key for the CreatedBy person and one for the UpdatedBy person but code first will create four will foreign key properties: Person\_Id, Person\_Id1, CreatedBy\_Id and UpdatedBy\_Id.</span></span>

![jj591583_figure10](~/ef6/media/jj591583-figure10.png)

<span data-ttu-id="f1fdf-261">これらの問題を解決するのにには、プロパティのアラインメントを指定するのに InverseProperty 注釈を使用できます。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-261">To fix these problems, you can use the InverseProperty annotation to specify the alignment of the properties.</span></span>

``` csharp
    [InverseProperty("CreatedBy")]
    public List<Post> PostsWritten { get; set; }

    [InverseProperty("UpdatedBy")]
    public List<Post> PostsUpdated { get; set; }
```

<span data-ttu-id="f1fdf-262">持参 PostsWritten プロパティは、これは、投稿の種類を指すことを知っている、ため Post.CreatedBy へのリレーションシップが構築されます。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-262">Because the PostsWritten property in Person knows that this refers to the Post type, it will build the relationship to Post.CreatedBy.</span></span> <span data-ttu-id="f1fdf-263">同様に、PostsUpdated は Post.UpdatedBy に接続されます。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-263">Similarly, PostsUpdated will be connected to Post.UpdatedBy.</span></span> <span data-ttu-id="f1fdf-264">コードはまず、余分な外部キーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-264">And code first will not create the extra foreign keys.</span></span>

![jj591583_figure11](~/ef6/media/jj591583-figure11.png)

 

## <a name="summary"></a><span data-ttu-id="f1fdf-266">まとめ</span><span class="sxs-lookup"><span data-stu-id="f1fdf-266">Summary</span></span>

<span data-ttu-id="f1fdf-267">DataAnnotations だけでなく、コードの最初クラスでクライアントとサーバー側の検証の説明を入力しますを強化し、まずコードがその規則に基づいて、クラスに関することは想定をでも修正がもできます。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-267">DataAnnotations not only let you describe client and server side validation in your code first classes, but they also allow you to enhance and even correct the assumptions that code first will make about your classes based on its conventions.</span></span> <span data-ttu-id="f1fdf-268">DataAnnotations にないのみを改善するデータベース スキーマの生成が既存のデータベースに、コードの最初のクラスをマップすることもできます。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-268">With DataAnnotations you can not only drive database schema generation, but you can also map your code first classes to a pre-existing database.</span></span>

<span data-ttu-id="f1fdf-269">非常に柔軟なは、中には、DataAnnotations は、最も一般的に必要な構成の変更が、コードの最初のクラスで行うことができますを提供することに留意してください。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-269">While they are very flexible, keep in mind that DataAnnotations provide only the most commonly needed configuration changes you can make on your code first classes.</span></span> <span data-ttu-id="f1fdf-270">エッジ ケースの一部で、クラスを構成するには、Code First の Fluent API である代替の構成メカニズムになります。</span><span class="sxs-lookup"><span data-stu-id="f1fdf-270">To configure your classes for some of the edge cases, you should look to the alternate configuration mechanism, Code First’s Fluent API .</span></span>
