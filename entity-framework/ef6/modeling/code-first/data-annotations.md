---
title: コードの最初のデータ注釈 - EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 80abefbd-23c9-4fce-9cd3-520e5df9856e
ms.openlocfilehash: e6b017306b4f66f5bac2a9964e11391da28ceb40
ms.sourcegitcommit: a013e243a14f384999ceccaf9c779b8c1ae3b936
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2019
ms.locfileid: "57463283"
---
# <a name="code-first-data-annotations"></a><span data-ttu-id="fc3d0-102">Code First のデータ注釈</span><span class="sxs-lookup"><span data-stu-id="fc3d0-102">Code First Data Annotations</span></span>
> [!NOTE]
> <span data-ttu-id="fc3d0-103">**EF4.1 以降のみ**-機能、Api、Entity Framework 4.1 で導入されたなどのこのページで説明します。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-103">**EF4.1 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 4.1.</span></span> <span data-ttu-id="fc3d0-104">以前のバージョンを使用している一部またはすべての情報は適用されません。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-104">If you are using an earlier version, some or all of this information does not apply.</span></span>

<span data-ttu-id="fc3d0-105">このページの内容を Julie Lerman によって書き込まれた最初の記事からの抜粋です (\<http://thedatafarm.com>)します。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-105">The content on this page is adapted from an article originally written by Julie Lerman (\<http://thedatafarm.com>).</span></span>

<span data-ttu-id="fc3d0-106">Entity Framework Code First EF が、クエリを実行するに依存するモデルを表す独自のドメイン クラスを使用することができます変更追跡、および関数を更新します。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-106">Entity Framework Code First allows you to use your own domain classes to represent the model that EF relies on to perform querying, change tracking, and updating functions.</span></span> <span data-ttu-id="fc3d0-107">コードが最初に '設定より規約です ' と呼ばれるプログラミング パターンを活用します。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-107">Code First leverages a programming pattern referred to as 'convention over configuration.'</span></span> <span data-ttu-id="fc3d0-108">コードはまず、クラスは Entity Framework の規則に従うし、その場合は、そのジョブを実行する方法を解明する自動的に。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-108">Code First will assume that your classes follow the conventions of Entity Framework, and in that case, will automatically work out how to perform it's job.</span></span> <span data-ttu-id="fc3d0-109">ただし、クラスは、これらの規則に従っていない、構成を必要な情報に EF を提供するクラスに追加する機能があります。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-109">However, if your classes do not follow those conventions, you have the ability to add configurations to your classes to provide EF with the requisite information.</span></span>

<span data-ttu-id="fc3d0-110">コードは 2 つの方法でこれらの構成をクラスに追加します。 まず、します。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-110">Code First gives you two ways to add these configurations to your classes.</span></span> <span data-ttu-id="fc3d0-111">1 つ DataAnnotations と呼ばれる単純な属性を使用して、2 つ目は Code First のコードで強制的に、構成を記述する方法を提供する Fluent API を使用しています。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-111">One is using simple attributes called DataAnnotations, and the second is using Code First’s Fluent API, which provides you with a way to describe configurations imperatively, in code.</span></span>

<span data-ttu-id="fc3d0-112">この記事では、System.ComponentModel.DataAnnotations 名前空間) の「DataAnnotations を使用して、最も一般的に必要な構成を強調表示、のクラスを構成する説明します。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-112">This article will focus on using DataAnnotations (in the System.ComponentModel.DataAnnotations namespace) to configure your classes – highlighting the most commonly needed configurations.</span></span> <span data-ttu-id="fc3d0-113">DataAnnotations がこれらのアプリケーションのクライアント側の検証のための同じ注釈を活用できる ASP.NET MVC などの .NET アプリケーションの番号で考えることもできます。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-113">DataAnnotations are also understood by a number of .NET applications, such as ASP.NET MVC which allows these applications to leverage the same annotations for client-side validations.</span></span>


## <a name="the-model"></a><span data-ttu-id="fc3d0-114">モデル</span><span class="sxs-lookup"><span data-stu-id="fc3d0-114">The model</span></span>

<span data-ttu-id="fc3d0-115">クラスの単純なペアを使って最初 DataAnnotations のコードを紹介します。ブログの投稿.</span><span class="sxs-lookup"><span data-stu-id="fc3d0-115">I’ll demonstrate Code First DataAnnotations with a simple pair of classes: Blog and Post.</span></span>

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

<span data-ttu-id="fc3d0-116">ブログや投稿クラスは簡単にコードの最初の規則に従うし、EF の互換性を有効にする調整は必要ありません。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-116">As they are, the Blog and Post classes conveniently follow code first convention and require no tweaks to enable EF compatability.</span></span> <span data-ttu-id="fc3d0-117">ただし、EF にクラスおよびマップ先のデータベースについての詳細を提供するのに、注釈を使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-117">However, you can also use the annotations to provide more information to EF about the classes and the database to which they map.</span></span>

 

## <a name="key"></a><span data-ttu-id="fc3d0-118">キー</span><span class="sxs-lookup"><span data-stu-id="fc3d0-118">Key</span></span>

<span data-ttu-id="fc3d0-119">Entity Framework は、エンティティの追跡に使用されるキーの値を持つすべてのエンティティに依存します。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-119">Entity Framework relies on every entity having a key value that is used for entity tracking.</span></span> <span data-ttu-id="fc3d0-120">Code First の 1 つの規則では、暗黙的なキー プロパティです。最初のコードは、"Id"、またはクラス名と"BlogId"など"Id", の組み合わせをという名前のプロパティを探します。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-120">One convention of Code First is implicit key properties; Code First will look for a property named “Id”, or a combination of class name and “Id”, such as “BlogId”.</span></span> <span data-ttu-id="fc3d0-121">このプロパティは、データベースの主キー列にマップされます。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-121">This property will map to a primary key column in the database.</span></span>

<span data-ttu-id="fc3d0-122">ブログと Post の両方のクラスでは、この規則に従います。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-122">The Blog and Post classes both follow this convention.</span></span> <span data-ttu-id="fc3d0-123">What-if でしたか。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-123">What if they didn’t?</span></span> <span data-ttu-id="fc3d0-124">ブログで名前を使用する場合*PrimaryTrackingKey*代わりに、あるいは*foo*でしょうか。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-124">What if Blog used the name *PrimaryTrackingKey* instead, or even *foo*?</span></span> <span data-ttu-id="fc3d0-125">コードが最初にこの規則に一致するプロパティでは見つからない場合は、キー プロパティに必要な Entity Framework の要件のため例外がスローされます。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-125">If code first does not find a property that matches this convention it will throw an exception because of Entity Framework’s requirement that you must have a key property.</span></span> <span data-ttu-id="fc3d0-126">キーの注釈を使用すると、EntityKey として使用するのにプロパティを指定します。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-126">You can use the key annotation to specify which property is to be used as the EntityKey.</span></span>

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

<span data-ttu-id="fc3d0-127">する場合はデータベースの生成機能は、最初にコードを使用して、ブログの表に、既定では、Id としても定義されている、PrimaryTrackingKey という名前の主キー列になります。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-127">If you are using code first’s database generation feature, the Blog table will have a primary key column named PrimaryTrackingKey, which is also defined as Identity by default.</span></span>

![主キー テーブルなブログ](~/ef6/media/jj591583-figure01.png)

### <a name="composite-keys"></a><span data-ttu-id="fc3d0-129">複合キー</span><span class="sxs-lookup"><span data-stu-id="fc3d0-129">Composite keys</span></span>

<span data-ttu-id="fc3d0-130">Entity Framework には、複合キーは、1 つ以上のプロパティで構成される主キーがサポートしています。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-130">Entity Framework supports composite keys - primary keys that consist of more than one property.</span></span> <span data-ttu-id="fc3d0-131">たとえば、プライマリ キーを持つ PassportNumber と IssuingCountry の組み合わせは、Passport クラスがある可能性があります。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-131">For example, you could have a Passport class whose primary key is a combination of PassportNumber and IssuingCountry.</span></span>

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

<span data-ttu-id="fc3d0-132">モデルに上記のクラスを使用しようとすることになります、 `InvalidOperationException`:</span><span class="sxs-lookup"><span data-stu-id="fc3d0-132">Attempting to use the above class in your EF model would result in an `InvalidOperationException`:</span></span>

<span data-ttu-id="fc3d0-133">*複合主キーの順序付けの種類 'Passport' を決定できません。複合主キーの順序を指定するのに、ColumnAttribute または HasKey メソッドを使用します。*</span><span class="sxs-lookup"><span data-stu-id="fc3d0-133">*Unable to determine composite primary key ordering for type 'Passport'. Use the ColumnAttribute or the HasKey method to specify an order for composite primary keys.*</span></span>

<span data-ttu-id="fc3d0-134">複合キーを使用するために Entity Framework はキー プロパティの順序を定義する必要があります。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-134">In order to use composite keys, Entity Framework requires you to define an order for the key properties.</span></span> <span data-ttu-id="fc3d0-135">これは、順序を指定する列の注釈を使用して行うことができます。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-135">You can do this by using the Column annotation to specify an order.</span></span>

>[!NOTE]
> <span data-ttu-id="fc3d0-136">順序の値は相対 (なくインデックス ベース) 任意の値を使用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-136">The order value is relative (rather than index based) so any values can be used.</span></span> <span data-ttu-id="fc3d0-137">たとえば、100 および 200 は 1 と 2 の代わりに許容になります。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-137">For example, 100 and 200 would be acceptable in place of 1 and 2.</span></span>

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

<span data-ttu-id="fc3d0-138">複合外部キーを持つエンティティがあれば、同じ列の対応するプライマリ キーのプロパティに使用する順序を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-138">If you have entities with composite foreign keys, then you must specify the same column ordering that you used for the corresponding primary key properties.</span></span>

<span data-ttu-id="fc3d0-139">同じである正確な値に割り当てられている必要があります、相対的な順序付けのみ外部キー プロパティ内で**順序**と一致する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-139">Only the relative ordering within the foreign key properties needs to be the same, the exact values assigned to **Order** do not need to match.</span></span> <span data-ttu-id="fc3d0-140">たとえば、次のクラスで 3 と 4 される可能性があります 1 と 2 の代わりにします。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-140">For example, in the following class, 3 and 4 could be used in place of 1 and 2.</span></span>

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

## <a name="required"></a><span data-ttu-id="fc3d0-141">必須</span><span class="sxs-lookup"><span data-stu-id="fc3d0-141">Required</span></span>

<span data-ttu-id="fc3d0-142">必要な注釈は、特定のプロパティが必要である EF に指示します。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-142">The Required annotation tells EF that a particular property is required.</span></span>

<span data-ttu-id="fc3d0-143">Title プロパティに必要な追加すると、プロパティは、これでデータを持つようにするための EF (および MVC) が実行されます。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-143">Adding Required to the Title property will force EF (and MVC) to ensure that the property has data in it.</span></span>

``` csharp
    [Required]
    public string Title { get; set; }
```

<span data-ttu-id="fc3d0-144">ない追加のないアプリケーションのコードやマークアップを変更、MVC アプリケーションのプロパティおよび注釈の名前を使用してメッセージの構築も動的に、クライアント側の検証は実行します。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-144">With no additional no code or markup changes in the application, an MVC application will perform client side validation, even dynamically building a message using the property and annotation names.</span></span>

![作成ページのタイトルが必要なエラー](~/ef6/media/jj591583-figure02.png)

<span data-ttu-id="fc3d0-146">必須の属性には、マッピングされたプロパティを null 非許容のことで生成されたデータベースも影響します。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-146">The Required attribute will also affect the generated database by making the mapped property non-nullable.</span></span> <span data-ttu-id="fc3d0-147">[タイトル] フィールドが"not null"に変更されていることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-147">Notice that the Title field has changed to “not null”.</span></span>

>[!NOTE]
> <span data-ttu-id="fc3d0-148">場合によっては、プロパティが必要な場合でも、null 非許容されるデータベース内の列の可能なないられます。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-148">In some cases it may not be possible for the column in the database to be non-nullable even though the property is required.</span></span> <span data-ttu-id="fc3d0-149">たとえば、TPH 継承の戦略のデータを複数の種類を使用してが格納されている場合、1 つのテーブル。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-149">For example, when using a TPH inheritance strategy data for multiple types is stored in a single table.</span></span> <span data-ttu-id="fc3d0-150">派生型に必要なプロパティが含まれている場合、列にできない null 非許容のこのプロパティは、階層内のすべての型であるためです。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-150">If a derived type includes a required property the column cannot be made non-nullable since not all types in the hierarchy will have this property.</span></span>

 

![テーブルなブログ](~/ef6/media/jj591583-figure03.png)

 

## <a name="maxlength-and-minlength"></a><span data-ttu-id="fc3d0-152">MaxLength、MinLength</span><span class="sxs-lookup"><span data-stu-id="fc3d0-152">MaxLength and MinLength</span></span>

<span data-ttu-id="fc3d0-153">MaxLength、MinLength 属性を必須にしたのと同様、追加のプロパティの検証を指定できます。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-153">The MaxLength and MinLength attributes allow you to specify additional property validations, just as you did with Required.</span></span>

<span data-ttu-id="fc3d0-154">長さの要件と BloggerName を次に示します。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-154">Here is the BloggerName with length requirements.</span></span> <span data-ttu-id="fc3d0-155">この例では、属性を結合する方法も示します。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-155">The example also demonstrates how to combine attributes.</span></span>

``` csharp
    [MaxLength(10),MinLength(5)]
    public string BloggerName { get; set; }
```

<span data-ttu-id="fc3d0-156">MaxLength 注釈は、プロパティの長さを 10 に設定して、データベースに影響します。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-156">The MaxLength annotation will impact the database by setting the property’s length to 10.</span></span>

![BloggerName 列の最大長を示すブログ表](~/ef6/media/jj591583-figure04.png)

<span data-ttu-id="fc3d0-158">クライアント側の注釈を MVC と EF 4.1 サーバー側の注釈は両方に従ってこの検証は、エラー メッセージを動的に再構築で行います。「BloggerName フィールドは、'10' の最大長の文字列または配列型をある必要があります」そのメッセージは、少し長くします。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-158">MVC client-side annotation and EF 4.1 server-side annotation will both honor this validation, again dynamically building an error message: “The field BloggerName must be a string or array type with a maximum length of '10'.” That message is a little long.</span></span> <span data-ttu-id="fc3d0-159">多くの注釈を使用して、エラー メッセージの属性を持つエラー メッセージを指定できます。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-159">Many annotations let you specify an error message with the ErrorMessage attribute.</span></span>

``` csharp
    [MaxLength(10, ErrorMessage="BloggerName must be 10 characters or less"),MinLength(5)]
    public string BloggerName { get; set; }
```

<span data-ttu-id="fc3d0-160">注釈が必要なエラー メッセージを指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-160">You can also specify ErrorMessage in the Required annotation.</span></span>

![カスタム エラー メッセージの作成 ページ](~/ef6/media/jj591583-figure05.png)

 

## <a name="notmapped"></a><span data-ttu-id="fc3d0-162">NotMapped</span><span class="sxs-lookup"><span data-stu-id="fc3d0-162">NotMapped</span></span>

<span data-ttu-id="fc3d0-163">コードの最初の規則は、サポートされているデータ型のすべてのプロパティが、データベースで表されていることを決定します。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-163">Code first convention dictates that every property that is of a supported data type is represented in the database.</span></span> <span data-ttu-id="fc3d0-164">これは、アプリケーション内のケースでは常にします。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-164">But this isn’t always the case in your applications.</span></span> <span data-ttu-id="fc3d0-165">たとえば、タイトルと BloggerName フィールドに基づくコードを作成するブログ クラスでプロパティがある可能性があります。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-165">For example you might have a property in the Blog class that creates a code based on the Title and BloggerName fields.</span></span> <span data-ttu-id="fc3d0-166">このプロパティは、動的に作成できるし、格納する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-166">That property can be created dynamically and does not need to be stored.</span></span> <span data-ttu-id="fc3d0-167">この BlogCode プロパティなど NotMapped 注釈を使用してデータベースにマップされていない任意のプロパティをマークすることができます。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-167">You can mark any properties that do not map to the database with the NotMapped annotation such as this BlogCode property.</span></span>

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

 

## <a name="complextype"></a><span data-ttu-id="fc3d0-168">ComplexType</span><span class="sxs-lookup"><span data-stu-id="fc3d0-168">ComplexType</span></span>

<span data-ttu-id="fc3d0-169">一連のクラス間で、ドメイン エンティティを記述し、レイヤーの完全なエンティティを記述するそれらのクラスに珍しくありません。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-169">It’s not uncommon to describe your domain entities across a set of classes and then layer those classes to describe a complete entity.</span></span> <span data-ttu-id="fc3d0-170">たとえば、モデルに BlogDetails と呼ばれるクラスを追加する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-170">For example, you may add a class called BlogDetails to your model.</span></span>

``` csharp
    public class BlogDetails
    {
        public DateTime? DateCreated { get; set; }

        [MaxLength(250)]
        public string Description { get; set; }
    }
```

<span data-ttu-id="fc3d0-171">BlogDetails にあらゆる種類のキー プロパティがないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-171">Notice that BlogDetails does not have any type of key property.</span></span> <span data-ttu-id="fc3d0-172">ドメイン駆動設計では、BlogDetails を値オブジェクトと呼びます。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-172">In domain driven design, BlogDetails is referred to as a value object.</span></span> <span data-ttu-id="fc3d0-173">Entity Framework は、複合型として値オブジェクトを参照します。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-173">Entity Framework refers to value objects as complex types.</span></span><span data-ttu-id="fc3d0-174">  独自の複合型を追跡することはできません。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-174">  Complex types cannot be tracked on their own.</span></span>

<span data-ttu-id="fc3d0-175">ただしブログ クラス、BlogDetails がブログ オブジェクトの一部として追跡のプロパティとして。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-175">However as a property in the Blog class, BlogDetails it will be tracked as part of a Blog object.</span></span> <span data-ttu-id="fc3d0-176">これを認識する最初のコードで、ComplexType として BlogDetails クラスをマークする必要があります。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-176">In order for code first to recognize this, you must mark the BlogDetails class as a ComplexType.</span></span>

``` csharp
    [ComplexType]
    public class BlogDetails
    {
        public DateTime? DateCreated { get; set; }

        [MaxLength(250)]
        public string Description { get; set; }
    }
```

<span data-ttu-id="fc3d0-177">今すぐそのブログ BlogDetails を表すブログ クラスでプロパティを追加できます。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-177">Now you can add a property in the Blog class to represent the BlogDetails for that blog.</span></span>

``` csharp
        public BlogDetails BlogDetail { get; set; }
```

<span data-ttu-id="fc3d0-178">データベースには、ブログの表に、その BlogDetail プロパティに含まれるプロパティを含む、ブログのプロパティのすべて含まれます。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-178">In the database, the Blog table will contain all of the properties of the blog including the properties contained in its BlogDetail property.</span></span> <span data-ttu-id="fc3d0-179">既定では、それぞれが付きます BlogDetail、複合型の名前。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-179">By default, each one is preceded with the name of the complex type, BlogDetail.</span></span>

![複合型を持つテーブルをブログ](~/ef6/media/jj591583-figure06.png)


## <a name="concurrencycheck"></a><span data-ttu-id="fc3d0-181">ConcurrencyCheck</span><span class="sxs-lookup"><span data-stu-id="fc3d0-181">ConcurrencyCheck</span></span>

<span data-ttu-id="fc3d0-182">ConcurrencyCheck 注釈の同時実行ユーザーが編集したり、エンティティを削除します。 データベースのチェックに使用する 1 つまたは複数のプロパティのフラグを設定することができます。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-182">The ConcurrencyCheck annotation allows you to flag one or more properties to be used for concurrency checking in the database when a user edits or deletes an entity.</span></span> <span data-ttu-id="fc3d0-183">EF Designer を使用していた場合、プロパティの ConcurrencyMode を Fixed に設定します。 これは。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-183">If you've been working with the EF Designer, this aligns with setting a property's ConcurrencyMode to Fixed.</span></span>

<span data-ttu-id="fc3d0-184">BloggerName プロパティに追加することによって ConcurrencyCheck のしくみを見てみましょう。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-184">Let’s see how ConcurrencyCheck works by adding it to the BloggerName property.</span></span>

``` csharp
    [ConcurrencyCheck, MaxLength(10, ErrorMessage="BloggerName must be 10 characters or less"),MinLength(5)]
    public string BloggerName { get; set; }
```

<span data-ttu-id="fc3d0-185">BloggerName フィールドで ConcurrencyCheck 注釈により、SaveChanges が呼び出されたときにそのプロパティの元の値が更新に使用されます。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-185">When SaveChanges is called, because of the ConcurrencyCheck annotation on the BloggerName field, the original value of that property will be used in the update.</span></span> <span data-ttu-id="fc3d0-186">コマンドが、キーの値だけでなく、BloggerName の元の値もフィルター処理して適切な行を検索しようとします。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-186">The command will attempt to locate the correct row by filtering not only on the key value but also on the original value of BloggerName.</span></span><span data-ttu-id="fc3d0-187">  ここでは、コマンドには、PrimaryTrackingKey を含む行は更新を表示、データベースに送信される UPDATE コマンドの重要な部分は 1、BloggerName"Julie"元の値をそのブログがデータベースから取得されたときにいたのです。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-187">  Here are the critical parts of the UPDATE command sent to the database, where you can see the command will update the row that has a PrimaryTrackingKey is 1 and a BloggerName of “Julie” which was the original value when that blog was retrieved from the database.</span></span>

``` SQL
    where (([PrimaryTrackingKey] = @4) and ([BloggerName] = @5))
    @4=1,@5=N'Julie'
```

<span data-ttu-id="fc3d0-188">そのブログのブログ作成者名が、その間にだれかが変更した場合は、この更新プログラムは失敗し、処理する必要がありますを DbUpdateConcurrencyException が表示されます。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-188">If someone has changed the blogger name for that blog in the meantime, this update will fail and you’ll get a DbUpdateConcurrencyException that you'll need to handle.</span></span>

 

## <a name="timestamp"></a><span data-ttu-id="fc3d0-189">タイムスタンプ</span><span class="sxs-lookup"><span data-stu-id="fc3d0-189">TimeStamp</span></span>

<span data-ttu-id="fc3d0-190">同時実行チェック用 rowversion またはタイムスタンプ フィールドを使用する方が一般的です。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-190">It's more common to use rowversion or timestamp fields for concurrency checking.</span></span> <span data-ttu-id="fc3d0-191">ConcurrencyCheck 注釈を使用するのではなく、プロパティの型がバイト配列である限り、特定のタイムスタンプの注釈を使用できます。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-191">But rather than using the ConcurrencyCheck annotation, you can use the more specific TimeStamp annotation as long as the type of the property is byte array.</span></span> <span data-ttu-id="fc3d0-192">コード最初はタイムスタンプのプロパティと同様に処理、ConcurrencyCheck プロパティが、コードを生成するデータベース フィールドを null 非許容は確認も。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-192">Code first will treat Timestamp properties the same as ConcurrencyCheck properties, but it will also ensure that the database field that code first generates is non-nullable.</span></span> <span data-ttu-id="fc3d0-193">タイムスタンプ プロパティを 1 つは、特定のクラスでのみができます。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-193">You can only have one timestamp property in a given class.</span></span>

<span data-ttu-id="fc3d0-194">ブログ クラスに次のプロパティを追加します。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-194">Adding the following property to the Blog class:</span></span>

``` csharp
    [Timestamp]
    public Byte[] TimeStamp { get; set; }
```

<span data-ttu-id="fc3d0-195">最初にデータベース テーブルで null 非許容の timestamp 列を作成するコードの結果。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-195">results in code first creating a non-nullable timestamp column in the database table.</span></span>

![タイムスタンプ列を含むブログ テーブル](~/ef6/media/jj591583-figure07.png)

 

## <a name="table-and-column"></a><span data-ttu-id="fc3d0-197">テーブルと列</span><span class="sxs-lookup"><span data-stu-id="fc3d0-197">Table and Column</span></span>

<span data-ttu-id="fc3d0-198">Code First のデータベースを作成することは、場合は、テーブルと列を作成しているの名前を変更することがあります。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-198">If you are letting Code First create the database, you may want to change the name of the tables and columns it is creating.</span></span> <span data-ttu-id="fc3d0-199">使用することできますも Code First 既存のデータベースでします。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-199">You can also use Code First with an existing database.</span></span> <span data-ttu-id="fc3d0-200">常にテーブルと、データベース内の列の名前をクラスと、ドメイン内のプロパティの名前が一致する場合。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-200">But it's not always the case that the names of the classes and properties in your domain match the names of the tables and columns in your database.</span></span>

<span data-ttu-id="fc3d0-201">筆者のクラスがブログをという名前し、慣例により、コードまず前提としていますこれは、ブログをという名前のテーブルにマップされます。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-201">My class is named Blog and by convention, code first presumes this will map to a table named Blogs.</span></span> <span data-ttu-id="fc3d0-202">そうでない場合は、テーブルの属性を持つテーブルの名前を指定できます。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-202">If that's not the case you can specify the name of the table with the Table attribute.</span></span> <span data-ttu-id="fc3d0-203">ここで、注釈はことを指定して、テーブル名 InternalBlogs。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-203">Here for example, the annotation is specifying that the table name is InternalBlogs.</span></span>

``` csharp
    [Table("InternalBlogs")]
    public class Blog
```

<span data-ttu-id="fc3d0-204">列の注釈は、マップされた列の属性を指定するときに複数のアデプトです。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-204">The Column annotation is a more adept in specifying the attributes of a mapped column.</span></span> <span data-ttu-id="fc3d0-205">名前、データ型またはテーブルの列が表示される順序もを伸ばすことができます。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-205">You can stipulate a name, data type or even the order in which a column appears in the table.</span></span> <span data-ttu-id="fc3d0-206">列の属性の例を示します。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-206">Here is an example of the Column attribute.</span></span>

``` csharp
    [Column("BlogDescription", TypeName="ntext")]
    public String Description {get;set;}
```

<span data-ttu-id="fc3d0-207">DataType DataAnnotation で列の TypeName 属性を混同しないでください。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-207">Don’t confuse Column’s TypeName attribute with the DataType DataAnnotation.</span></span> <span data-ttu-id="fc3d0-208">データ型は、注釈、UI の使用は、Code First によって無視されます。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-208">DataType is an annotation used for the UI and is ignored by Code First.</span></span>

<span data-ttu-id="fc3d0-209">再生成後に次の表に示します。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-209">Here is the table after it’s been regenerated.</span></span> <span data-ttu-id="fc3d0-210">テーブル名が InternalBlogs に変更し、複合型から列を説明 BlogDescription になります。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-210">The table name has changed to InternalBlogs and Description column from the complex type is now BlogDescription.</span></span> <span data-ttu-id="fc3d0-211">注釈の名前が指定されたため、コード最初は使用しません複合型の名前で列名を開始する規約。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-211">Because the name was specified in the annotation, code first will not use the convention of starting the column name with the name of the complex type.</span></span>

![ブログのテーブルと列の名前を変更](~/ef6/media/jj591583-figure08.png)

 

## <a name="databasegenerated"></a><span data-ttu-id="fc3d0-213">DatabaseGenerated</span><span class="sxs-lookup"><span data-stu-id="fc3d0-213">DatabaseGenerated</span></span>

<span data-ttu-id="fc3d0-214">重要なデータベース機能は、プロパティが計算する機能があります。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-214">An important database features is the ability to have computed properties.</span></span> <span data-ttu-id="fc3d0-215">Code First クラスにマッピングする場合は、計算列のテーブルが含まれている、それらの列を更新しようとする Entity Framework をしたくないです。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-215">If you're mapping your Code First classes to tables that contain computed columns, you don't want Entity Framework to try to update those columns.</span></span> <span data-ttu-id="fc3d0-216">ただし、EF を挿入または更新されたデータを使用した後、データベースからそれらの値を返すようにします。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-216">But you do want EF to return those values from the database after you've inserted or updated data.</span></span> <span data-ttu-id="fc3d0-217">DatabaseGenerated 注釈を使用すると、計算済みの列挙型と共に、クラスでこれらのプロパティのフラグを設定します。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-217">You can use the DatabaseGenerated annotation to flag those properties in your class along with the Computed enum.</span></span> <span data-ttu-id="fc3d0-218">他の列挙型は None と Id。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-218">Other enums are None and Identity.</span></span>

``` csharp
    [DatabaseGenerated(DatabaseGeneratedOption.Computed)]
    public DateTime DateCreated { get; set; }
```

<span data-ttu-id="fc3d0-219">それ以外の場合にのみ使用これコード最初ことはできません、計算列の数式を判断するため、既存のデータベースをポイントして、コードはまず、データベースを生成するときに、バイトまたはタイムスタンプ列で生成されたデータベースを使用できます。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-219">You can use database generated on byte or timestamp columns when code first is generating the database, otherwise you should only use this when pointing to existing databases because code first won't be able to determine the formula for the computed column.</span></span>

<span data-ttu-id="fc3d0-220">既定では、読むことを整数であるキー プロパティは、データベースの id キーなります。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-220">You read above that by default, a key property that is an integer will become an identity key in the database.</span></span> <span data-ttu-id="fc3d0-221">DatabaseGenerated を DatabaseGeneratedOption.Identity に設定すると同じメッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-221">That would be the same as setting DatabaseGenerated to DatabaseGeneratedOption.Identity.</span></span> <span data-ttu-id="fc3d0-222">Id キーを使用することをしない場合は、DatabaseGeneratedOption.None に値を設定できます。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-222">If you do not want it to be an identity key, you can set the value to DatabaseGeneratedOption.None.</span></span>

 

## <a name="index"></a><span data-ttu-id="fc3d0-223">インデックス</span><span class="sxs-lookup"><span data-stu-id="fc3d0-223">Index</span></span>

> [!NOTE]
> <span data-ttu-id="fc3d0-224">**EF6.1 以降のみ**-Entity Framework 6.1 で、インデックスの属性が導入されました。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-224">**EF6.1 Onwards Only** - The Index attribute was introduced in Entity Framework 6.1.</span></span> <span data-ttu-id="fc3d0-225">以前のバージョンを使用している場合、このセクションの情報は適用されません。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-225">If you are using an earlier version the information in this section does not apply.</span></span>

<span data-ttu-id="fc3d0-226">使用して 1 つまたは複数の列にインデックスを作成することができます、 **IndexAttribute**します。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-226">You can create an index on one or more columns using the **IndexAttribute**.</span></span> <span data-ttu-id="fc3d0-227">データベースの作成時に、データベースに対応するインデックスの作成に EF を原因またはスキャフォールディング、対応する属性を 1 つまたは複数のプロパティに追加**CreateIndex** Code First Migrations を使用している場合に呼び出します。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-227">Adding the attribute to one or more properties will cause EF to create the corresponding index in the database when it creates the database, or scaffold the corresponding **CreateIndex** calls if you are using Code First Migrations.</span></span>

<span data-ttu-id="fc3d0-228">たとえば、次のコードが作成されたインデックスになります。、**評価**の列、**投稿**データベース内のテーブル。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-228">For example, the following code will result in an index being created on the **Rating** column of the **Posts** table in the database.</span></span>

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

<span data-ttu-id="fc3d0-229">既定では、インデックスの名前は**IX\_&lt;プロパティ名&gt;** (IX\_上の例では評価)。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-229">By default, the index will be named **IX\_&lt;property name&gt;** (IX\_Rating in the above example).</span></span> <span data-ttu-id="fc3d0-230">ただし、インデックスの名前も指定できます。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-230">You can also specify a name for the index though.</span></span> <span data-ttu-id="fc3d0-231">次の例では、インデックスに名前を付けることを指定します**PostRatingIndex**します。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-231">The following example specifies that the index should be named **PostRatingIndex**.</span></span>

``` csharp
    [Index("PostRatingIndex")]
    public int Rating { get; set; }
```

<span data-ttu-id="fc3d0-232">既定では、インデックスが一意でない、使用することが、 **IsUnique**という名前のインデックスが一意であることを指定するパラメーター。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-232">By default, indexes are non-unique, but you can use the **IsUnique** named parameter to specify that an index should be unique.</span></span> <span data-ttu-id="fc3d0-233">次の例の一意のインデックスが導入されて、**ユーザー**のログイン名。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-233">The following example introduces a unique index on a **User**'s login name.</span></span>

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

### <a name="multiple-column-indexes"></a><span data-ttu-id="fc3d0-234">複数列のインデックス</span><span class="sxs-lookup"><span data-stu-id="fc3d0-234">Multiple-Column Indexes</span></span>

<span data-ttu-id="fc3d0-235">複数の列にまたがるインデックスは、指定されたテーブルの複数のインデックス注釈内で同じ名前を使用して指定されます。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-235">Indexes that span multiple columns are specified by using the same name in multiple Index annotations for a given table.</span></span> <span data-ttu-id="fc3d0-236">複数列のインデックスを作成するときに、インデックスの列の順序を指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-236">When you create multi-column indexes, you need to specify an order for the columns in the index.</span></span> <span data-ttu-id="fc3d0-237">次のコードが複数列インデックスを作成するなど、**評価**と**BlogId**と呼ばれる**IX\_BlogIdAndRating**します。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-237">For example, the following code creates a multi-column index on **Rating** and **BlogId** called **IX\_BlogIdAndRating**.</span></span> <span data-ttu-id="fc3d0-238">**BlogId**は、インデックスの最初の列と**評価**2 つ目は、します。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-238">**BlogId** is the first column in the index and **Rating** is the second.</span></span>

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

 

## <a name="relationship-attributes-inverseproperty-and-foreignkey"></a><span data-ttu-id="fc3d0-239">リレーションシップ属性:InverseProperty と不変</span><span class="sxs-lookup"><span data-stu-id="fc3d0-239">Relationship Attributes: InverseProperty and ForeignKey</span></span>

> [!NOTE]
> <span data-ttu-id="fc3d0-240">このページは、データ注釈を使用して Code First モデルでリレーションシップを設定する方法についての情報を提供します。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-240">This page provides information about setting up relationships in your Code First model using Data Annotations.</span></span> <span data-ttu-id="fc3d0-241">EF とアクセスし、リレーションシップを使用してデータを操作する方法に関係の詳細については、次を参照してください[リレーションシップとナビゲーション プロパティ](~/ef6/fundamentals/relationships.md)。 \*。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-241">For general information about relationships in EF and how to access and manipulate data using relationships, see [Relationships & Navigation Properties](~/ef6/fundamentals/relationships.md).\*</span></span>

<span data-ttu-id="fc3d0-242">コードの最初の規則が、モデル内の最も一般的なリレーションシップの処理が、場合によってはヘルプが必要な場所があります。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-242">Code first convention will take care of the most common relationships in your model, but there are some cases where it needs help.</span></span>

<span data-ttu-id="fc3d0-243">投稿との関係に問題を作成したブログ クラスのキー プロパティの名前を変更します。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-243">Changing the name of the key property in the Blog class created a problem with its relationship to Post.</span></span> 

<span data-ttu-id="fc3d0-244">データベースを生成するときに、コードはまず Post クラスで BlogId プロパティを表示しと一致する、クラス名と"Id"ブログ クラスへの外部キーとして規則によりを認識します。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-244">When generating the database, code first sees the BlogId property in the Post class and recognizes it, by the convention that it matches a class name plus “Id”, as a foreign key to the Blog class.</span></span> <span data-ttu-id="fc3d0-245">ブログのクラスで BlogId プロパティはありません。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-245">But there is no BlogId property in the blog class.</span></span> <span data-ttu-id="fc3d0-246">このソリューションは、投稿にナビゲーション プロパティを作成し、コードの最初の 2 つのクラス間のリレーションシップを構築する方法を理解する外部 DataAnnotation を使用して、Post.BlogId プロパティを使用して、内の制約を指定する方法についても、データベース。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-246">The solution for this is to create a navigation property in the Post and use the Foreign DataAnnotation to help code first understand how to build the relationship between the two classes —using the Post.BlogId property — as well as how to specify constraints in the database.</span></span>

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

<span data-ttu-id="fc3d0-247">データベース内の制約は、InternalBlogs.PrimaryTrackingKey と Posts.BlogId 間の関係を示しています。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-247">The constraint in the database shows a relationship between InternalBlogs.PrimaryTrackingKey and Posts.BlogId.</span></span> 

![InternalBlogs.PrimaryTrackingKey と Posts.BlogId 間のリレーションシップ](~/ef6/media/jj591583-figure09.png)

<span data-ttu-id="fc3d0-249">クラス間の複数のリレーションシップがある場合、InverseProperty が使用されます。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-249">The InverseProperty is used when you have multiple relationships between classes.</span></span>

<span data-ttu-id="fc3d0-250">ブログの投稿の作成者を追跡するのにすることがあります Post クラスで編集したユーザーとします。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-250">In the Post class, you may want to keep track of who wrote a blog post as well as who edited it.</span></span> <span data-ttu-id="fc3d0-251">ここでは、Post クラスの新しい 2 つのナビゲーション プロパティです。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-251">Here are two new navigation properties for the Post class.</span></span>

``` csharp
    public Person CreatedBy { get; set; }
    public Person UpdatedBy { get; set; }
```

<span data-ttu-id="fc3d0-252">また、これらのプロパティによって参照されている Person クラスに追加する必要もあります。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-252">You’ll also need to add in the Person class referenced by these properties.</span></span> <span data-ttu-id="fc3d0-253">Person クラスには、すべてのユーザーとすべての人が更新投稿の 1 つによって書き込まれた投稿の 1 つ、Post にナビゲーション プロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-253">The Person class has navigation properties back to the Post, one for all of the posts written by the person and one for all of the posts updated by that person.</span></span>

``` csharp
    public class Person
    {
            public int Id { get; set; }
            public string Name { get; set; }
            public List<Post> PostsWritten { get; set; }
            public List<Post> PostsUpdated { get; set; }
    }
```

<span data-ttu-id="fc3d0-254">最初にコードは、独自の 2 つのクラスのプロパティと一致することではありません。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-254">Code first is not able to match up the properties in the two classes on its own.</span></span> <span data-ttu-id="fc3d0-255">投稿のデータベース テーブルでは、CreatedBy 人および UpdatedBy 人のいずれか 1 つの外部キーが必要ですが、コードは最初に次の 4 つの外部キー プロパティを作成します。Person\_Id、Person\_Id1、CreatedBy\_Id と UpdatedBy\_id。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-255">The database table for Posts should have one foreign key for the CreatedBy person and one for the UpdatedBy person but code first will create four foreign key properties: Person\_Id, Person\_Id1, CreatedBy\_Id and UpdatedBy\_Id.</span></span>

![余分な外部キーを含むテーブルを投稿します。](~/ef6/media/jj591583-figure10.png)

<span data-ttu-id="fc3d0-257">これらの問題を解決するのにには、プロパティのアラインメントを指定するのに InverseProperty 注釈を使用できます。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-257">To fix these problems, you can use the InverseProperty annotation to specify the alignment of the properties.</span></span>

``` csharp
    [InverseProperty("CreatedBy")]
    public List<Post> PostsWritten { get; set; }

    [InverseProperty("UpdatedBy")]
    public List<Post> PostsUpdated { get; set; }
```

<span data-ttu-id="fc3d0-258">持参 PostsWritten プロパティは、これは、投稿の種類を指すことを知っている、ため Post.CreatedBy へのリレーションシップが構築されます。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-258">Because the PostsWritten property in Person knows that this refers to the Post type, it will build the relationship to Post.CreatedBy.</span></span> <span data-ttu-id="fc3d0-259">同様に、PostsUpdated は Post.UpdatedBy に接続されます。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-259">Similarly, PostsUpdated will be connected to Post.UpdatedBy.</span></span> <span data-ttu-id="fc3d0-260">コードはまず、余分な外部キーが作成されます。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-260">And code first will not create the extra foreign keys.</span></span>

![投稿の余分な外部キーを持たないテーブル](~/ef6/media/jj591583-figure11.png)

 

## <a name="summary"></a><span data-ttu-id="fc3d0-262">まとめ</span><span class="sxs-lookup"><span data-stu-id="fc3d0-262">Summary</span></span>

<span data-ttu-id="fc3d0-263">DataAnnotations だけでなく、コードの最初クラスでクライアントとサーバー側の検証の説明を入力しますを強化し、まずコードがその規則に基づいて、クラスに関することは想定をでも修正がもできます。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-263">DataAnnotations not only let you describe client and server side validation in your code first classes, but they also allow you to enhance and even correct the assumptions that code first will make about your classes based on its conventions.</span></span> <span data-ttu-id="fc3d0-264">DataAnnotations にないのみを改善するデータベース スキーマの生成が既存のデータベースに、コードの最初のクラスをマップすることもできます。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-264">With DataAnnotations you can not only drive database schema generation, but you can also map your code first classes to a pre-existing database.</span></span>

<span data-ttu-id="fc3d0-265">非常に柔軟なは、中には、DataAnnotations は、最も一般的に必要な構成の変更が、コードの最初のクラスで行うことができますを提供することに留意してください。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-265">While they are very flexible, keep in mind that DataAnnotations provide only the most commonly needed configuration changes you can make on your code first classes.</span></span> <span data-ttu-id="fc3d0-266">エッジ ケースの一部で、クラスを構成するには、Code First の Fluent API である代替の構成メカニズムになります。</span><span class="sxs-lookup"><span data-stu-id="fc3d0-266">To configure your classes for some of the edge cases, you should look to the alternate configuration mechanism, Code First’s Fluent API .</span></span>
