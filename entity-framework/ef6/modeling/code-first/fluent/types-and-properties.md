---
title: Fluent API 構成およびプロパティと型のマッピングで EF6
author: divega
ms.date: 2016-10-23
ms.assetid: 648ed274-c501-4630-88e0-d728ab5c4057
ms.openlocfilehash: e65a3f4721e5c28de63d143e1143f3584e145477
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42996988"
---
# <a name="fluent-api---configuring-and-mapping-properties-and-types"></a><span data-ttu-id="24841-102">Fluent API を構成して、プロパティと型のマッピング</span><span class="sxs-lookup"><span data-stu-id="24841-102">Fluent API - Configuring and Mapping Properties and Types</span></span>
<span data-ttu-id="24841-103">Entity Framework Code First を使用する場合、既定の動作は、POCO クラスを EF に組み込まれた規則のセットを使用してテーブルにマップするのには。</span><span class="sxs-lookup"><span data-stu-id="24841-103">When working with Entity Framework Code First the default behavior is to map your POCO classes to tables using a set of conventions baked into EF.</span></span> <span data-ttu-id="24841-104">場合によっては、ただし、することはできません、またはこれらの規則に従うし、規則ではどのような以外にエンティティをマップする必要がありますしたくないです。</span><span class="sxs-lookup"><span data-stu-id="24841-104">Sometimes, however, you cannot or do not want to follow those conventions and need to map entities to something other than what the conventions dictate.</span></span>  

<span data-ttu-id="24841-105">Namely の表記規則以外のものを使用する EF を構成する 2 つの主な方法はあります[注釈](~/ef6/modeling/code-first/data-annotations.md)または EFs fluent API。</span><span class="sxs-lookup"><span data-stu-id="24841-105">There are two main ways you can configure EF to use something other than conventions, namely [annotations](~/ef6/modeling/code-first/data-annotations.md) or EFs fluent API.</span></span> <span data-ttu-id="24841-106">注釈では、注釈の使用を実現できないマッピング シナリオがあるため、fluent API の機能のサブセットがのみについて説明します。</span><span class="sxs-lookup"><span data-stu-id="24841-106">The annotations only cover a subset of the fluent API functionality, so there are mapping scenarios that cannot be achieved using annotations.</span></span> <span data-ttu-id="24841-107">この記事では、fluent API を使用してプロパティを構成する方法を示すために設計されています。</span><span class="sxs-lookup"><span data-stu-id="24841-107">This article is designed to demonstrate how to use the fluent API to configure properties.</span></span>  

<span data-ttu-id="24841-108">Code first fluent API がオーバーライドすることによってアクセスが最もよく、 [OnModelCreating](https://msdn.microsoft.com/library/system.data.entity.dbcontext.onmodelcreating.aspx)メソッドを派生[DbContext](https://msdn.microsoft.com/library/system.data.entity.dbcontext.aspx)します。</span><span class="sxs-lookup"><span data-stu-id="24841-108">The code first fluent API is most commonly accessed by overriding the [OnModelCreating](https://msdn.microsoft.com/library/system.data.entity.dbcontext.onmodelcreating.aspx) method on your derived [DbContext](https://msdn.microsoft.com/library/system.data.entity.dbcontext.aspx).</span></span> <span data-ttu-id="24841-109">次のサンプルは fluent api を使用したさまざまなタスクを実行し、すると、コードをコピーしとで使用できるモデルを表示する場合、モデルに合わせてカスタマイズする方法について説明するように設計-がこの記事の最後に提供されます。</span><span class="sxs-lookup"><span data-stu-id="24841-109">The following samples are designed to show how to do various tasks with the fluent api and allow you to copy the code out and customize it to suit your model, if you wish to see the model that they can be used with as-is then it is provided at the end of this article.</span></span>  

## <a name="model-wide-settings"></a><span data-ttu-id="24841-110">モデル全体の設定</span><span class="sxs-lookup"><span data-stu-id="24841-110">Model-Wide Settings</span></span>  

### <a name="default-schema-ef6-onwards"></a><span data-ttu-id="24841-111">既定のスキーマ (EF6 以降)</span><span class="sxs-lookup"><span data-stu-id="24841-111">Default Schema (EF6 onwards)</span></span>  

<span data-ttu-id="24841-112">EF6 で始まることができます HasDefaultSchema メソッドを使用 DbModelBuilder のすべてのテーブル、ストアド プロシージャなどを使用するのにデータベース スキーマを指定します。この既定の設定は、別のスキーマを明示的に構成するすべてのオブジェクトのオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="24841-112">Starting with EF6 you can use the HasDefaultSchema method on DbModelBuilder to specify the database schema to use for all tables, stored procedures, etc. This default setting will be overridden for any objects that you explicitly configure a different schema for.</span></span>  

``` csharp
modelBuilder.HasDefaultSchema(“sales”);
```  

### <a name="custom-conventions-ef6-onwards"></a><span data-ttu-id="24841-113">カスタムの規則 (EF6 以降)</span><span class="sxs-lookup"><span data-stu-id="24841-113">Custom Conventions (EF6 onwards)</span></span>  

<span data-ttu-id="24841-114">EF6 Code First に入っているものを補完する、独自の規則を作成することで開始しています。</span><span class="sxs-lookup"><span data-stu-id="24841-114">Starting with EF6 you can create your own conventions to supplement the ones included in Code First.</span></span> <span data-ttu-id="24841-115">詳細については、次を参照してください。[カスタム コードの最初の規則](~/ef6/modeling/code-first/conventions/custom.md)します。</span><span class="sxs-lookup"><span data-stu-id="24841-115">For more details, see [Custom Code First Conventions](~/ef6/modeling/code-first/conventions/custom.md).</span></span>  

## <a name="property-mapping"></a><span data-ttu-id="24841-116">プロパティのマッピング</span><span class="sxs-lookup"><span data-stu-id="24841-116">Property Mapping</span></span>  

<span data-ttu-id="24841-117">[プロパティ](https://msdn.microsoft.com/library/system.data.entity.infrastructure.dbentityentry.property.aspx)メソッドは、エンティティまたは複合型に属している各プロパティの属性の構成に使用されます。</span><span class="sxs-lookup"><span data-stu-id="24841-117">The [Property](https://msdn.microsoft.com/library/system.data.entity.infrastructure.dbentityentry.property.aspx) method is used to configure attributes for each property belonging to an entity or complex type.</span></span> <span data-ttu-id="24841-118">プロパティ メソッドを使用して、特定のプロパティの構成オブジェクトを取得できます。</span><span class="sxs-lookup"><span data-stu-id="24841-118">The Property method is used to obtain a configuration object for a given property.</span></span> <span data-ttu-id="24841-119">構成オブジェクトのオプションが構成されている種類に固有IsUnicode はなどの文字列プロパティでのみ使用できます。</span><span class="sxs-lookup"><span data-stu-id="24841-119">The options on the configuration object are specific to the type being configured; IsUnicode is available only on string properties for example.</span></span>  

### <a name="configuring-a-primary-key"></a><span data-ttu-id="24841-120">主キーを構成します。</span><span class="sxs-lookup"><span data-stu-id="24841-120">Configuring a Primary Key</span></span>  

<span data-ttu-id="24841-121">主キーの Entity Framework の規則は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="24841-121">The Entity Framework convention for primary keys is:</span></span>  

1. <span data-ttu-id="24841-122">クラスは、名前が"ID"または"Id"プロパティを定義します。</span><span class="sxs-lookup"><span data-stu-id="24841-122">Your class defines a property whose name is “ID” or “Id”</span></span>  
2. <span data-ttu-id="24841-123">または、クラス名の後に"ID"または"Id"</span><span class="sxs-lookup"><span data-stu-id="24841-123">or a class name followed by “ID” or “Id”</span></span>  

<span data-ttu-id="24841-124">主キーのプロパティを明示的に設定するには、HasKey メソッドを使用することができます。</span><span class="sxs-lookup"><span data-stu-id="24841-124">To explicitly set a property to be a primary key, you can use the HasKey method.</span></span> <span data-ttu-id="24841-125">次の例では、HasKey メソッドを使用して、OfficeAssignment で InstructorID 主キーを構成します。</span><span class="sxs-lookup"><span data-stu-id="24841-125">In the following example, the HasKey method is used to configure the InstructorID primary key on the OfficeAssignment type.</span></span>  

``` csharp
modelBuilder.Entity<OfficeAssignment>().HasKey(t => t.InstructorID);
```  

### <a name="configuring-a-composite-primary-key"></a><span data-ttu-id="24841-126">複合主キーを構成します。</span><span class="sxs-lookup"><span data-stu-id="24841-126">Configuring a Composite Primary Key</span></span>  

<span data-ttu-id="24841-127">次の例では、部門の型の複合主キー プロパティ DepartmentID と Name プロパティを構成します。</span><span class="sxs-lookup"><span data-stu-id="24841-127">The following example configures the DepartmentID and Name properties to be the composite primary key of the Department type.</span></span>  

``` csharp
modelBuilder.Entity<Department>().HasKey(t => new { t.DepartmentID, t.Name });
```  

### <a name="switching-off-identity-for-numeric-primary-keys"></a><span data-ttu-id="24841-128">数値の主キーの Id をオフの切り替え</span><span class="sxs-lookup"><span data-stu-id="24841-128">Switching off Identity for Numeric Primary Keys</span></span>  

<span data-ttu-id="24841-129">次の例を示す値をデータベースによって生成されませんが System.ComponentModel.DataAnnotations.DatabaseGeneratedOption.None に DepartmentID プロパティを設定します。</span><span class="sxs-lookup"><span data-stu-id="24841-129">The following example sets the DepartmentID property to System.ComponentModel.DataAnnotations.DatabaseGeneratedOption.None to indicate that the value will not be generated by the database.</span></span>  

``` csharp
modelBuilder.Entity<Department>().Property(t => t.DepartmentID)
    .HasDatabaseGeneratedOption(DatabaseGeneratedOption.None);
```  

### <a name="specifying-the-maximum-length-on-a-property"></a><span data-ttu-id="24841-130">プロパティの最大長を指定します。</span><span class="sxs-lookup"><span data-stu-id="24841-130">Specifying the Maximum Length on a Property</span></span>  

<span data-ttu-id="24841-131">次の例では、Name プロパティは 50 文字以内にあります。</span><span class="sxs-lookup"><span data-stu-id="24841-131">In the following example, the Name property should be no longer than 50 characters.</span></span> <span data-ttu-id="24841-132">50 文字より長い値を行った場合、 [DbEntityValidationException](https://msdn.microsoft.com/library/system.data.entity.validation.dbentityvalidationexception.aspx)例外。</span><span class="sxs-lookup"><span data-stu-id="24841-132">If you make the value longer than 50 characters, you will get a [DbEntityValidationException](https://msdn.microsoft.com/library/system.data.entity.validation.dbentityvalidationexception.aspx) exception.</span></span> <span data-ttu-id="24841-133">Code First にこのモデルからデータベースを作成する場合 50 文字に、[名前] 列の最大長も設定されます。</span><span class="sxs-lookup"><span data-stu-id="24841-133">If Code First creates a database from this model it will also set the maximum length of the Name column to 50 characters.</span></span>  

``` csharp
modelBuilder.Entity<Department>().Property(t => t.Name).HasMaxLength(50);
```  

### <a name="configuring-the-property-to-be-required"></a><span data-ttu-id="24841-134">必要になるプロパティを構成します。</span><span class="sxs-lookup"><span data-stu-id="24841-134">Configuring the Property to be Required</span></span>  

<span data-ttu-id="24841-135">次の例では、Name プロパティが必要です。</span><span class="sxs-lookup"><span data-stu-id="24841-135">In the following example, the Name property is required.</span></span> <span data-ttu-id="24841-136">名前を指定しない場合は、DbEntityValidationException 例外が表示されます。</span><span class="sxs-lookup"><span data-stu-id="24841-136">If you do not specify the Name, you will get a DbEntityValidationException exception.</span></span> <span data-ttu-id="24841-137">このモデルからデータベースを作成します Code First 場合は、このプロパティの格納に使用される列が null 非許容に通常は。</span><span class="sxs-lookup"><span data-stu-id="24841-137">If Code First creates a database from this model then the column used to store this property will usually be non-nullable.</span></span>  

> [!NOTE]
> <span data-ttu-id="24841-138">場合によっては、プロパティが必要な場合でも、null 非許容されるデータベース内の列の可能なないられます。</span><span class="sxs-lookup"><span data-stu-id="24841-138">In some cases it may not be possible for the column in the database to be non-nullable even though the property is required.</span></span> <span data-ttu-id="24841-139">たとえば、TPH 継承の戦略のデータを複数の種類を使用してが格納されている場合、1 つのテーブル。</span><span class="sxs-lookup"><span data-stu-id="24841-139">For example, when using a TPH inheritance strategy data for multiple types is stored in a single table.</span></span> <span data-ttu-id="24841-140">派生型に必要なプロパティが含まれている場合、列にできない null 非許容のこのプロパティは、階層内のすべての型であるためです。</span><span class="sxs-lookup"><span data-stu-id="24841-140">If a derived type includes a required property the column cannot be made non-nullable since not all types in the hierarchy will have this property.</span></span>  

``` csharp
modelBuilder.Entity<Department>().Property(t => t.Name).IsRequired();
```  

### <a name="configuring-an-index-on-one-or-more-properties"></a><span data-ttu-id="24841-141">1 つまたは複数のプロパティのインデックスを構成します。</span><span class="sxs-lookup"><span data-stu-id="24841-141">Configuring an Index on one or more properties</span></span>  

> [!NOTE]
> <span data-ttu-id="24841-142">**EF6.1 以降のみ**-Entity Framework 6.1 で、インデックスの属性が導入されました。</span><span class="sxs-lookup"><span data-stu-id="24841-142">**EF6.1 Onwards Only** - The Index attribute was introduced in Entity Framework 6.1.</span></span> <span data-ttu-id="24841-143">以前のバージョンを使用している場合、このセクションの情報は適用されません。</span><span class="sxs-lookup"><span data-stu-id="24841-143">If you are using an earlier version the information in this section does not apply.</span></span>  

<span data-ttu-id="24841-144">加えることが、Fluent API でネイティブでサポートされていないインデックスの作成用のサポートを使用して、 **IndexAttribute** Fluent API を使用して。</span><span class="sxs-lookup"><span data-stu-id="24841-144">Creating indexes isn't natively supported by the Fluent API, but you can make use of the support for **IndexAttribute** via the Fluent API.</span></span> <span data-ttu-id="24841-145">インデックスの属性は、パイプラインの後半で、データベース内のインデックスに変換し、モデルのモデル注釈を含めることによって処理されます。</span><span class="sxs-lookup"><span data-stu-id="24841-145">Index attributes are processed by including a model annotation on the model that is then turned into an Index in the database later in the pipeline.</span></span> <span data-ttu-id="24841-146">Fluent API を使用して注釈を追加これらと同じ手動でできます。</span><span class="sxs-lookup"><span data-stu-id="24841-146">You can manually add these same annotations using the Fluent API.</span></span>  

<span data-ttu-id="24841-147">これを行う最も簡単な方法は、のインスタンスを作成する**IndexAttribute**新しいインデックスのすべての設定を格納しています。</span><span class="sxs-lookup"><span data-stu-id="24841-147">The easiest way to do this is to create an instance of **IndexAttribute** that contains all the settings for the new index.</span></span> <span data-ttu-id="24841-148">インスタンスを作成し、 **IndexAnnotation**は EF 特定の型に変換されますが、 **IndexAttribute** EF モデルに格納できるモデル注釈を設定します。</span><span class="sxs-lookup"><span data-stu-id="24841-148">You can then create an instance of **IndexAnnotation** which is an EF specific type that will convert the **IndexAttribute** settings into a model annotation that can be stored on the EF model.</span></span> <span data-ttu-id="24841-149">これらに渡すことができます、 **HasColumnAnnotation**メソッド名を指定する、Fluent API を**インデックス**の注釈。</span><span class="sxs-lookup"><span data-stu-id="24841-149">These can then be passed to the **HasColumnAnnotation** method on the Fluent API, specifying the name **Index** for the annotation.</span></span>  

``` csharp
modelBuilder
    .Entity<Department>()
    .Property(t => t.Name)
    .HasColumnAnnotation("Index", new IndexAnnotation(new IndexAttribute()));
```  

<span data-ttu-id="24841-150">使用できる設定の完全な一覧については**IndexAttribute**を参照してください、*インデックス*の[Code First のデータ注釈](~/ef6/modeling/code-first/data-annotations.md)します。</span><span class="sxs-lookup"><span data-stu-id="24841-150">For a complete list of the settings available in **IndexAttribute**, see the *Index* section of [Code First Data Annotations](~/ef6/modeling/code-first/data-annotations.md).</span></span> <span data-ttu-id="24841-151">これには、インデックス名をカスタマイズする、一意のインデックスの作成、および複数列インデックスの作成が含まれます。</span><span class="sxs-lookup"><span data-stu-id="24841-151">This includes customizing the index name, creating unique indexes, and creating multi-column indexes.</span></span>  

<span data-ttu-id="24841-152">配列を渡すことによって、1 つのプロパティで複数のインデックス注釈を指定できます**IndexAttribute**のコンス トラクターに**IndexAnnotation**します。</span><span class="sxs-lookup"><span data-stu-id="24841-152">You can specify multiple index annotations on a single property by passing an array of **IndexAttribute** to the constructor of **IndexAnnotation**.</span></span>  

``` csharp
modelBuilder
    .Entity<Department>()
    .Property(t => t.Name)
    .HasColumnAnnotation(
        "Index",  
        new IndexAnnotation(new[]
            {
                new IndexAttribute("Index1"),
                new IndexAttribute("Index2") { IsUnique = true }
            })));
```  

### <a name="specifying-not-to-map-a-clr-property-to-a-column-in-the-database"></a><span data-ttu-id="24841-153">CLR プロパティをデータベース内の列にマップしないように指定</span><span class="sxs-lookup"><span data-stu-id="24841-153">Specifying Not to Map a CLR Property to a Column in the Database</span></span>  

<span data-ttu-id="24841-154">次の例では、CLR 型のプロパティが、データベース内の列にマップされていないことを指定する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="24841-154">The following example shows how to specify that a property on a CLR type is not mapped to a column in the database.</span></span>  

``` csharp
modelBuilder.Entity<Department>().Ignore(t => t.Budget);
```  

### <a name="mapping-a-clr-property-to-a-specific-column-in-the-database"></a><span data-ttu-id="24841-155">CLR プロパティをデータベースの特定の列にマップします。</span><span class="sxs-lookup"><span data-stu-id="24841-155">Mapping a CLR Property to a Specific Column in the Database</span></span>  

<span data-ttu-id="24841-156">次の例では、DepartmentName データベース列に名前の CLR プロパティがマップされます。</span><span class="sxs-lookup"><span data-stu-id="24841-156">The following example maps the Name CLR property to the DepartmentName database column.</span></span>  

``` csharp
modelBuilder.Entity<Department>()
    .Property(t => t.Name)
    .HasColumnName("DepartmentName");
```  

### <a name="renaming-a-foreign-key-that-is-not-defined-in-the-model"></a><span data-ttu-id="24841-157">モデルで定義されていない外部キーの名前を変更します。</span><span class="sxs-lookup"><span data-stu-id="24841-157">Renaming a Foreign Key That Is Not Defined in the Model</span></span>  

<span data-ttu-id="24841-158">CLR 型での外部キーの定義が、データベースが必要な名前を指定しないように選択する場合は、次の操作を行います。</span><span class="sxs-lookup"><span data-stu-id="24841-158">If you choose not to define a foreign key on a CLR type, but want to specify what name it should have in the database, do the following:</span></span>  

``` csharp
modelBuilder.Entity<Course>()
    .HasRequired(c => c.Department)
    .WithMany(t => t.Courses)
    .Map(m => m.MapKey("ChangedDepartmentID"));
```  

### <a name="configuring-whether-a-string-property-supports-unicode-content"></a><span data-ttu-id="24841-159">文字列プロパティは、Unicode コンテンツをサポートしているかどうかを構成します。</span><span class="sxs-lookup"><span data-stu-id="24841-159">Configuring whether a String Property Supports Unicode Content</span></span>  

<span data-ttu-id="24841-160">既定では、文字列は Unicode (SQL Server での nvarchar) されます。</span><span class="sxs-lookup"><span data-stu-id="24841-160">By default strings are Unicode (nvarchar in SQL Server).</span></span> <span data-ttu-id="24841-161">IsUnicode メソッドを使用して、varchar 型の文字列があることを指定することができます。</span><span class="sxs-lookup"><span data-stu-id="24841-161">You can use the IsUnicode method to specify that a string should be of varchar type.</span></span>  

``` csharp
modelBuilder.Entity<Department>()
    .Property(t => t.Name)
    .IsUnicode(false);
```  

### <a name="configuring-the-data-type-of-a-database-column"></a><span data-ttu-id="24841-162">データベース列のデータ型を構成します。</span><span class="sxs-lookup"><span data-stu-id="24841-162">Configuring the Data Type of a Database Column</span></span>  

<span data-ttu-id="24841-163">[HasColumnType](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.configuration.stringcolumnconfiguration.hascolumntype.aspx)メソッドは、同じ基本型のさまざまな表現へのマッピングを使用できます。</span><span class="sxs-lookup"><span data-stu-id="24841-163">The [HasColumnType](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.configuration.stringcolumnconfiguration.hascolumntype.aspx) method enables mapping to different representations of the same basic type.</span></span> <span data-ttu-id="24841-164">このメソッドを使用しての場合、実行時に、データの変換を実行できるようにはありません。</span><span class="sxs-lookup"><span data-stu-id="24841-164">Using this method does not enable you to perform any conversion of the data at run time.</span></span> <span data-ttu-id="24841-165">データベースに依存しない IsUnicode varchar 列の設定の推奨される方法は、することに注意してください。</span><span class="sxs-lookup"><span data-stu-id="24841-165">Note that IsUnicode is the preferred way of setting columns to varchar, as it is database agnostic.</span></span>  

``` csharp
modelBuilder.Entity<Department>()   
    .Property(p => p.Name)   
    .HasColumnType("varchar");
```  

### <a name="configuring-properties-on-a-complex-type"></a><span data-ttu-id="24841-166">複合型のプロパティの構成</span><span class="sxs-lookup"><span data-stu-id="24841-166">Configuring Properties on a Complex Type</span></span>  

<span data-ttu-id="24841-167">複合型のスカラー プロパティを構成する 2 つの方法はあります。</span><span class="sxs-lookup"><span data-stu-id="24841-167">There are two ways to configure scalar properties on a complex type.</span></span>  

<span data-ttu-id="24841-168">ComplexTypeConfiguration でプロパティを呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="24841-168">You can call Property on ComplexTypeConfiguration.</span></span>  

``` csharp
modelBuilder.ComplexType<Details>()
    .Property(t => t.Location)
    .HasMaxLength(20);
```  

<span data-ttu-id="24841-169">複合型のプロパティにアクセスするのに、ドット表記を使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="24841-169">You can also use the dot notation to access a property of a complex type.</span></span>  

``` csharp
modelBuilder.Entity<OnsiteCourse>()
    .Property(t => t.Details.Location)
    .HasMaxLength(20);
```  

### <a name="configuring-a-property-to-be-used-as-an-optimistic-concurrency-token"></a><span data-ttu-id="24841-170">オプティミスティック同時実行トークンとして使用するプロパティを構成します。</span><span class="sxs-lookup"><span data-stu-id="24841-170">Configuring a Property to Be Used as an Optimistic Concurrency Token</span></span>  

<span data-ttu-id="24841-171">エンティティのプロパティが同時実行トークンを表すことを指定するには、ConcurrencyCheck 属性または IsConcurrencyToken メソッドのいずれかを使用できます。</span><span class="sxs-lookup"><span data-stu-id="24841-171">To specify that a property in an entity represents a concurrency token, you can use either the ConcurrencyCheck attribute or the IsConcurrencyToken method.</span></span>  

``` csharp
modelBuilder.Entity<OfficeAssignment>()
    .Property(t => t.Timestamp)
    .IsConcurrencyToken();
```  

<span data-ttu-id="24841-172">データベース内の行バージョンであるプロパティを構成するのに IsRowVersion メソッドを使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="24841-172">You can also use the IsRowVersion method to configure the property to be a row version in the database.</span></span> <span data-ttu-id="24841-173">オプティミスティック同時実行制御トークンは、行バージョンが自動的に構成するプロパティを設定します。</span><span class="sxs-lookup"><span data-stu-id="24841-173">Setting the property to be a row version automatically configures it to be an optimistic concurrency token.</span></span>  

``` csharp
modelBuilder.Entity<OfficeAssignment>()
    .Property(t => t.Timestamp)
    .IsRowVersion();
```  

## <a name="type-mapping"></a><span data-ttu-id="24841-174">型のマッピング</span><span class="sxs-lookup"><span data-stu-id="24841-174">Type Mapping</span></span>  

### <a name="specifying-that-a-class-is-a-complex-type"></a><span data-ttu-id="24841-175">クラスが複合型を指定します。</span><span class="sxs-lookup"><span data-stu-id="24841-175">Specifying That a Class Is a Complex Type</span></span>  

<span data-ttu-id="24841-176">慣例により、指定したプライマリ キーを持たない型は複合型として扱われます。</span><span class="sxs-lookup"><span data-stu-id="24841-176">By convention, a type that has no primary key specified is treated as a complex type.</span></span> <span data-ttu-id="24841-177">一部のシナリオで Code First は検出されません複合型 (たとえば場合、ID という名前のプロパティがする主キーにするのには、必ずしも) があります。</span><span class="sxs-lookup"><span data-stu-id="24841-177">There are some scenarios where Code First will not detect a complex type (for example, if you do have a property called ID, but you do not mean for it to be a primary key).</span></span> <span data-ttu-id="24841-178">このような場合、型が複合型であるかを明示的に指定する fluent API を使用するとします。</span><span class="sxs-lookup"><span data-stu-id="24841-178">In such cases, you would use the fluent API to explicitly specify that a type is a complex type.</span></span>  

``` csharp
modelBuilder.ComplexType<Details>();
```  

### <a name="specifying-not-to-map-a-clr-entity-type-to-a-table-in-the-database"></a><span data-ttu-id="24841-179">CLR のエンティティ型をデータベース内のテーブルにマップしないように指定</span><span class="sxs-lookup"><span data-stu-id="24841-179">Specifying Not to Map a CLR Entity Type to a Table in the Database</span></span>  

<span data-ttu-id="24841-180">次の例では、データベース内のテーブルにマップされているから CLR 型を除外する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="24841-180">The following example shows how to exclude a CLR type from being mapped to a table in the database.</span></span>  

``` csharp
modelBuilder.Ignore<OnlineCourse>();
```  

### <a name="mapping-an-entity-type-to-a-specific-table-in-the-database"></a><span data-ttu-id="24841-181">データベース内の特定のテーブルにエンティティ型のマッピング</span><span class="sxs-lookup"><span data-stu-id="24841-181">Mapping an Entity Type to a Specific Table in the Database</span></span>  

<span data-ttu-id="24841-182">部門のすべてのプロパティは、t _ 部門をという名前のテーブル内の列にマップされます。</span><span class="sxs-lookup"><span data-stu-id="24841-182">All properties of Department will be mapped to columns in a table called t_ Department.</span></span>  

``` csharp
modelBuilder.Entity<Department>()  
    .ToTable("t_Department");
```  

<span data-ttu-id="24841-183">このようなスキーマ名を指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="24841-183">You can also specify the schema name like this:</span></span>  

``` csharp
modelBuilder.Entity<Department>()  
    .ToTable("t_Department", "school");
```  

### <a name="mapping-the-table-per-hierarchy-tph-inheritance"></a><span data-ttu-id="24841-184">Table-per-hierarchy (TPH) 継承のマッピング</span><span class="sxs-lookup"><span data-stu-id="24841-184">Mapping the Table-Per-Hierarchy (TPH) Inheritance</span></span>  

<span data-ttu-id="24841-185">TPH マッピング シナリオでは、継承階層内のすべての型が 1 つのテーブルにマップされます。</span><span class="sxs-lookup"><span data-stu-id="24841-185">In the TPH mapping scenario, all types in an inheritance hierarchy are mapped to a single table.</span></span> <span data-ttu-id="24841-186">識別子列を使用して、各行の種類を識別します。</span><span class="sxs-lookup"><span data-stu-id="24841-186">A discriminator column is used to identify the type of each row.</span></span> <span data-ttu-id="24841-187">Code First によるモデルを作成するときに、継承階層に参加している型の既定の戦略は TPH です。</span><span class="sxs-lookup"><span data-stu-id="24841-187">When creating your model with Code First, TPH is the default strategy for the types that participate in the inheritance hierarchy.</span></span> <span data-ttu-id="24841-188">既定では、「識別子」という名前のテーブルに識別子列が追加して、階層内の各型の CLR 型名は識別子の値を使用します。</span><span class="sxs-lookup"><span data-stu-id="24841-188">By default, the discriminator column is added to the table with the name “Discriminator” and the CLR type name of each type in the hierarchy is used for the discriminator values.</span></span> <span data-ttu-id="24841-189">既定の動作を変更するには、fluent API を使用します。</span><span class="sxs-lookup"><span data-stu-id="24841-189">You can modify the default behavior by using the fluent API.</span></span>  

``` csharp
modelBuilder.Entity<Course>()  
    .Map<Course>(m => m.Requires("Type").HasValue("Course"))  
    .Map<OnsiteCourse>(m => m.Requires("Type").HasValue("OnsiteCourse"));
```  

### <a name="mapping-the-table-per-type-tpt-inheritance"></a><span data-ttu-id="24841-190">テーブルあたり型 (TPT) 継承のマッピング</span><span class="sxs-lookup"><span data-stu-id="24841-190">Mapping the Table-Per-Type (TPT) Inheritance</span></span>  

<span data-ttu-id="24841-191">TPT マッピング シナリオでは、すべての種類は個々 のテーブルにマップされます。</span><span class="sxs-lookup"><span data-stu-id="24841-191">In the TPT mapping scenario, all types are mapped to individual tables.</span></span> <span data-ttu-id="24841-192">基本データ型または派生型のみに属するプロパティは、その型にマップされたテーブルに格納されます。</span><span class="sxs-lookup"><span data-stu-id="24841-192">Properties that belong solely to a base type or derived type are stored in a table that maps to that type.</span></span> <span data-ttu-id="24841-193">派生型にマップされるテーブルでは、派生テーブル、ベース テーブルと結合する外部キーも格納されます。</span><span class="sxs-lookup"><span data-stu-id="24841-193">Tables that map to derived types also store a foreign key that joins the derived table with the base table.</span></span>  

``` csharp
modelBuilder.Entity<Course>().ToTable("Course");  
modelBuilder.Entity<OnsiteCourse>().ToTable("OnsiteCourse");
```  

### <a name="mapping-the-table-per-concrete-class-tpc-inheritance"></a><span data-ttu-id="24841-194">テーブル-コンクリートあたり Class (TPC) 継承のマッピング</span><span class="sxs-lookup"><span data-stu-id="24841-194">Mapping the Table-Per-Concrete Class (TPC) Inheritance</span></span>  

<span data-ttu-id="24841-195">TPC マッピング シナリオでは、階層内のすべての非抽象型は個々 のテーブルにマップされます。</span><span class="sxs-lookup"><span data-stu-id="24841-195">In the TPC mapping scenario, all non-abstract types in the hierarchy are mapped to individual tables.</span></span> <span data-ttu-id="24841-196">派生クラスにマップされるテーブル、データベース内の基本クラスにマップされるテーブルに関係のあるありません。</span><span class="sxs-lookup"><span data-stu-id="24841-196">The tables that map to the derived classes have no relationship to the table that maps to the base class in the database.</span></span> <span data-ttu-id="24841-197">継承されたプロパティを含むクラスのすべてのプロパティは、対応するテーブルの列にマップされます。</span><span class="sxs-lookup"><span data-stu-id="24841-197">All properties of a class, including inherited properties, are mapped to columns of the corresponding table.</span></span>  

<span data-ttu-id="24841-198">それぞれの派生型を構成する MapInheritedProperties メソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="24841-198">Call the MapInheritedProperties method to configure each derived type.</span></span> <span data-ttu-id="24841-199">MapInheritedProperties は、テーブルには、派生クラスの新しい列に、基本クラスから継承されたすべてのプロパティを再マップします。</span><span class="sxs-lookup"><span data-stu-id="24841-199">MapInheritedProperties remaps all properties that were inherited from the base class to new columns in the table for the derived class.</span></span>  

> [!NOTE]
> <span data-ttu-id="24841-200">TPC 継承階層に参加しているテーブルを共有しないため、主キーがありますされる重複したエンティティ キーの同じ identity シードがデータベースで生成された値がある場合は、サブクラスにマップされているテーブルに挿入するときに注意してください。</span><span class="sxs-lookup"><span data-stu-id="24841-200">Note that because the tables participating in TPC inheritance hierarchy do not share a primary key there will be duplicate entity keys when inserting in tables that are mapped to subclasses if you have database generated values with the same identity seed.</span></span> <span data-ttu-id="24841-201">この問題を解決するには、各テーブルの別の最初のシード値を指定するか、主キー プロパティに、id が無効です。</span><span class="sxs-lookup"><span data-stu-id="24841-201">To solve this problem you can either specify a different initial seed value for each table or switch off identity on the primary key property.</span></span> <span data-ttu-id="24841-202">Identity は、Code First を使用する場合の整数キー プロパティの既定値です。</span><span class="sxs-lookup"><span data-stu-id="24841-202">Identity is the default value for integer key properties when working with Code First.</span></span>  

``` csharp
modelBuilder.Entity<Course>()
    .Property(c => c.CourseID)
    .HasDatabaseGeneratedOption(DatabaseGeneratedOption.None);

modelBuilder.Entity<OnsiteCourse>().Map(m =>
{
    m.MapInheritedProperties();
    m.ToTable("OnsiteCourse");
});

modelBuilder.Entity<OnlineCourse>().Map(m =>
{
    m.MapInheritedProperties();
    m.ToTable("OnlineCourse");
});
```  

### <a name="mapping-properties-of-an-entity-type-to-multiple-tables-in-the-database-entity-splitting"></a><span data-ttu-id="24841-203">エンティティ型のプロパティを (エンティティ分割) データベースの複数のテーブルにマップします。</span><span class="sxs-lookup"><span data-stu-id="24841-203">Mapping Properties of an Entity Type to Multiple Tables in the Database (Entity Splitting)</span></span>  

<span data-ttu-id="24841-204">エンティティ分割には、複数のテーブルに分散し、エンティティ型のプロパティことができます。</span><span class="sxs-lookup"><span data-stu-id="24841-204">Entity splitting allows the properties of an entity type to be spread across multiple tables.</span></span> <span data-ttu-id="24841-205">次の例では、Department エンティティが 2 つのテーブルに分割されます。 部門と DepartmentDetails します。</span><span class="sxs-lookup"><span data-stu-id="24841-205">In the following example, the Department entity is split into two tables: Department and DepartmentDetails.</span></span> <span data-ttu-id="24841-206">エンティティ分割プロパティのサブセットを特定のテーブルにマップするのにマップのメソッドに複数の呼び出しを使用します。</span><span class="sxs-lookup"><span data-stu-id="24841-206">Entity splitting uses multiple calls to the Map method to map a subset of properties to a specific table.</span></span>  

``` csharp
modelBuilder.Entity<Department>()
    .Map(m =>
    {
        m.Properties(t => new { t.DepartmentID, t.Name });
        m.ToTable("Department");
    })
    .Map(m =>
    {
        m.Properties(t => new { t.DepartmentID, t.Administrator, t.StartDate, t.Budget });
        m.ToTable("DepartmentDetails");
    });
```  

### <a name="mapping-multiple-entity-types-to-one-table-in-the-database-table-splitting"></a><span data-ttu-id="24841-207">データベース (テーブル分割) の 1 つのテーブルに複数のエンティティ型のマッピング</span><span class="sxs-lookup"><span data-stu-id="24841-207">Mapping Multiple Entity Types to One Table in the Database (Table Splitting)</span></span>  

<span data-ttu-id="24841-208">次の例では、1 つのテーブルに主キーを共有する 2 つのエンティティ型をマップします。</span><span class="sxs-lookup"><span data-stu-id="24841-208">The following example maps two entity types that share a primary key to one table.</span></span>  

``` csharp
modelBuilder.Entity<OfficeAssignment>()
    .HasKey(t => t.InstructorID);

modelBuilder.Entity<Instructor>()
    .HasRequired(t => t.OfficeAssignment)
    .WithRequiredPrincipal(t => t.Instructor);

modelBuilder.Entity<Instructor>().ToTable("Instructor");

modelBuilder.Entity<OfficeAssignment>().ToTable("Instructor");
```  

### <a name="mapping-an-entity-type-to-insertupdatedelete-stored-procedures-ef6-onwards"></a><span data-ttu-id="24841-209">ストアド プロシージャの挿入/更新/削除 (EF6 以降) へのエンティティ型のマッピング</span><span class="sxs-lookup"><span data-stu-id="24841-209">Mapping an Entity Type to Insert/Update/Delete Stored Procedures (EF6 onwards)</span></span>  

<span data-ttu-id="24841-210">EF6 で始まる挿入更新にストアド プロシージャを使用し、削除するエンティティにマップできます。</span><span class="sxs-lookup"><span data-stu-id="24841-210">Starting with EF6 you can map an entity to use stored procedures for insert update and delete.</span></span> <span data-ttu-id="24841-211">詳細についてを参照してください、[コード最初挿入/更新/削除のストアド プロシージャ](~/ef6/modeling/code-first/fluent/cud-stored-procedures.md)します。</span><span class="sxs-lookup"><span data-stu-id="24841-211">For more details see, [Code First Insert/Update/Delete Stored Procedures](~/ef6/modeling/code-first/fluent/cud-stored-procedures.md).</span></span>  

## <a name="model-used-in-samples"></a><span data-ttu-id="24841-212">サンプルで使用されるモデル</span><span class="sxs-lookup"><span data-stu-id="24841-212">Model Used in Samples</span></span>  

<span data-ttu-id="24841-213">このページのサンプルについては、次のコードの最初のモデルが使用されます。</span><span class="sxs-lookup"><span data-stu-id="24841-213">The following Code First model is used for the samples on this page.</span></span>  

``` csharp
using System.Data.Entity;
using System.Data.Entity.ModelConfiguration.Conventions;
// add a reference to System.ComponentModel.DataAnnotations DLL
using System.ComponentModel.DataAnnotations;
using System.Collections.Generic;
using System;

public class SchoolEntities : DbContext
{
    public DbSet<Course> Courses { get; set; }
    public DbSet<Department> Departments { get; set; }
    public DbSet<Instructor> Instructors { get; set; }
    public DbSet<OfficeAssignment> OfficeAssignments { get; set; }

    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        // Configure Code First to ignore PluralizingTableName convention
        // If you keep this convention then the generated tables will have pluralized names.
        modelBuilder.Conventions.Remove<PluralizingTableNameConvention>();
    }
}

public class Department
{
    public Department()
    {
        this.Courses = new HashSet<Course>();
    }
    // Primary key
    public int DepartmentID { get; set; }
    public string Name { get; set; }
    public decimal Budget { get; set; }
    public System.DateTime StartDate { get; set; }
    public int? Administrator { get; set; }

    // Navigation property
    public virtual ICollection<Course> Courses { get; private set; }
}

public class Course
{
    public Course()
    {
        this.Instructors = new HashSet<Instructor>();
    }
    // Primary key
    public int CourseID { get; set; }

    public string Title { get; set; }
    public int Credits { get; set; }

    // Foreign key
    public int DepartmentID { get; set; }

    // Navigation properties
    public virtual Department Department { get; set; }
    public virtual ICollection<Instructor> Instructors { get; private set; }
}

public partial class OnlineCourse : Course
{
    public string URL { get; set; }
}

public partial class OnsiteCourse : Course
{
    public OnsiteCourse()
    {
        Details = new Details();
    }

    public Details Details { get; set; }
}

public class Details
{
    public System.DateTime Time { get; set; }
    public string Location { get; set; }
    public string Days { get; set; }
}

public class Instructor
{
    public Instructor()
    {
        this.Courses = new List<Course>();
    }

    // Primary key
    public int InstructorID { get; set; }
    public string LastName { get; set; }
    public string FirstName { get; set; }
    public System.DateTime HireDate { get; set; }

    // Navigation properties
    public virtual ICollection<Course> Courses { get; private set; }
}

public class OfficeAssignment
{
    // Specifying InstructorID as a primary
    [Key()]
    public Int32 InstructorID { get; set; }

    public string Location { get; set; }

    // When Entity Framework sees Timestamp attribute
    // it configures ConcurrencyCheck and DatabaseGeneratedPattern=Computed.
    [Timestamp]
    public Byte[] Timestamp { get; set; }

    // Navigation property
    public virtual Instructor Instructor { get; set; }
}
```  
