---
title: Fluent API-プロパティと型の構成とマッピング-EF6
description: Fluent API-Entity Framework 6 でのプロパティと型の構成とマッピング
author: divega
ms.date: 10/23/2016
ms.assetid: 648ed274-c501-4630-88e0-d728ab5c4057
uid: ef6/modeling/code-first/fluent/types-and-properties
ms.openlocfilehash: d49da416b2df5828fce5618cc196e22dfbc2bd34
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618054"
---
# <a name="fluent-api---configuring-and-mapping-properties-and-types"></a><span data-ttu-id="414d4-103">Fluent API-プロパティと型の構成とマッピング</span><span class="sxs-lookup"><span data-stu-id="414d4-103">Fluent API - Configuring and Mapping Properties and Types</span></span>
<span data-ttu-id="414d4-104">Entity Framework を使用する場合 Code First 既定の動作では、EF に組み込まれている一連の規則を使用して、POCO クラスをテーブルにマップします。</span><span class="sxs-lookup"><span data-stu-id="414d4-104">When working with Entity Framework Code First the default behavior is to map your POCO classes to tables using a set of conventions baked into EF.</span></span> <span data-ttu-id="414d4-105">ただし、これらの規則に従うことができない場合や、規則に従っていないエンティティにエンティティをマップする必要がある場合があります。</span><span class="sxs-lookup"><span data-stu-id="414d4-105">Sometimes, however, you cannot or do not want to follow those conventions and need to map entities to something other than what the conventions dictate.</span></span>  

<span data-ttu-id="414d4-106">規則以外のものを使用するように EF を構成するには、次の2つの主な方法があります。つまり、 [注釈](xref:ef6/modeling/code-first/data-annotations) または EFs fluent API です。</span><span class="sxs-lookup"><span data-stu-id="414d4-106">There are two main ways you can configure EF to use something other than conventions, namely [annotations](xref:ef6/modeling/code-first/data-annotations) or EFs fluent API.</span></span> <span data-ttu-id="414d4-107">注釈には fluent API の機能のサブセットのみが含まれているので、注釈を使用して実現できないマッピングシナリオがあります。</span><span class="sxs-lookup"><span data-stu-id="414d4-107">The annotations only cover a subset of the fluent API functionality, so there are mapping scenarios that cannot be achieved using annotations.</span></span> <span data-ttu-id="414d4-108">この記事は、fluent API を使用してプロパティを構成する方法を示すことを目的としています。</span><span class="sxs-lookup"><span data-stu-id="414d4-108">This article is designed to demonstrate how to use the fluent API to configure properties.</span></span>  

<span data-ttu-id="414d4-109">Code first fluent API は、派生した[Dbcontext](https://msdn.microsoft.com/library/system.data.entity.dbcontext.aspx)で[onmodelcreating](https://msdn.microsoft.com/library/system.data.entity.dbcontext.onmodelcreating.aspx)メソッドをオーバーライドすることによって最も一般的にアクセスされます。</span><span class="sxs-lookup"><span data-stu-id="414d4-109">The code first fluent API is most commonly accessed by overriding the [OnModelCreating](https://msdn.microsoft.com/library/system.data.entity.dbcontext.onmodelcreating.aspx) method on your derived [DbContext](https://msdn.microsoft.com/library/system.data.entity.dbcontext.aspx).</span></span> <span data-ttu-id="414d4-110">次のサンプルは、fluent api を使用してさまざまなタスクを実行する方法を示しています。また、モデルに合わせてコードをコピーしてカスタマイズすることもできます。そのまま使用できるモデルを確認する場合は、この記事の最後に記載します。</span><span class="sxs-lookup"><span data-stu-id="414d4-110">The following samples are designed to show how to do various tasks with the fluent api and allow you to copy the code out and customize it to suit your model, if you wish to see the model that they can be used with as-is then it is provided at the end of this article.</span></span>  

## <a name="model-wide-settings"></a><span data-ttu-id="414d4-111">モデル全体の設定</span><span class="sxs-lookup"><span data-stu-id="414d4-111">Model-Wide Settings</span></span>  

### <a name="default-schema-ef6-onwards"></a><span data-ttu-id="414d4-112">既定のスキーマ (EF6 以降)</span><span class="sxs-lookup"><span data-stu-id="414d4-112">Default Schema (EF6 onwards)</span></span>  

<span data-ttu-id="414d4-113">EF6 以降では、DbModelBuilder で HasDefaultSchema メソッドを使用して、すべてのテーブル、ストアドプロシージャなどに使用するデータベーススキーマを指定できます。この既定の設定は、別のスキーマを明示的に構成するすべてのオブジェクトに対してオーバーライドされます。</span><span class="sxs-lookup"><span data-stu-id="414d4-113">Starting with EF6 you can use the HasDefaultSchema method on DbModelBuilder to specify the database schema to use for all tables, stored procedures, etc. This default setting will be overridden for any objects that you explicitly configure a different schema for.</span></span>  

``` csharp
modelBuilder.HasDefaultSchema("sales");
```  

### <a name="custom-conventions-ef6-onwards"></a><span data-ttu-id="414d4-114">カスタム規則 (EF6 以降)</span><span class="sxs-lookup"><span data-stu-id="414d4-114">Custom Conventions (EF6 onwards)</span></span>  

<span data-ttu-id="414d4-115">EF6 以降では、独自の規則を作成して、Code First に含まれる規則を補うことができます。</span><span class="sxs-lookup"><span data-stu-id="414d4-115">Starting with EF6 you can create your own conventions to supplement the ones included in Code First.</span></span> <span data-ttu-id="414d4-116">詳細については、「 [カスタム Code First 規則](xref:ef6/modeling/code-first/conventions/custom)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="414d4-116">For more details, see [Custom Code First Conventions](xref:ef6/modeling/code-first/conventions/custom).</span></span>  

## <a name="property-mapping"></a><span data-ttu-id="414d4-117">プロパティ マッピング</span><span class="sxs-lookup"><span data-stu-id="414d4-117">Property Mapping</span></span>  

<span data-ttu-id="414d4-118">[プロパティ](https://msdn.microsoft.com/library/system.data.entity.infrastructure.dbentityentry.property.aspx)メソッドは、エンティティ型または複合型に属する各プロパティの属性を構成するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="414d4-118">The [Property](https://msdn.microsoft.com/library/system.data.entity.infrastructure.dbentityentry.property.aspx) method is used to configure attributes for each property belonging to an entity or complex type.</span></span> <span data-ttu-id="414d4-119">プロパティメソッドは、指定されたプロパティの構成オブジェクトを取得するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="414d4-119">The Property method is used to obtain a configuration object for a given property.</span></span> <span data-ttu-id="414d4-120">構成オブジェクトのオプションは、構成されている型に固有です。IsUnicode は、文字列プロパティでのみ使用できます。たとえば、のようになります。</span><span class="sxs-lookup"><span data-stu-id="414d4-120">The options on the configuration object are specific to the type being configured; IsUnicode is available only on string properties for example.</span></span>  

### <a name="configuring-a-primary-key"></a><span data-ttu-id="414d4-121">主キーの構成</span><span class="sxs-lookup"><span data-stu-id="414d4-121">Configuring a Primary Key</span></span>  

<span data-ttu-id="414d4-122">主キーの Entity Framework 規則は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="414d4-122">The Entity Framework convention for primary keys is:</span></span>  

1. <span data-ttu-id="414d4-123">クラスは、名前が "ID" または "Id" であるプロパティを定義します。</span><span class="sxs-lookup"><span data-stu-id="414d4-123">Your class defines a property whose name is “ID” or “Id”</span></span>  
2. <span data-ttu-id="414d4-124">または、クラス名の後に "ID" または "Id" が続きます。</span><span class="sxs-lookup"><span data-stu-id="414d4-124">or a class name followed by “ID” or “Id”</span></span>  

<span data-ttu-id="414d4-125">プロパティを主キーとして明示的に設定するには、HasKey メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="414d4-125">To explicitly set a property to be a primary key, you can use the HasKey method.</span></span> <span data-ttu-id="414d4-126">次の例では、HasKey メソッドを使用して、OfficeAssignment 型の InstructorID 主キーを構成しています。</span><span class="sxs-lookup"><span data-stu-id="414d4-126">In the following example, the HasKey method is used to configure the InstructorID primary key on the OfficeAssignment type.</span></span>  

``` csharp
modelBuilder.Entity<OfficeAssignment>().HasKey(t => t.InstructorID);
```  

### <a name="configuring-a-composite-primary-key"></a><span data-ttu-id="414d4-127">複合主キーの構成</span><span class="sxs-lookup"><span data-stu-id="414d4-127">Configuring a Composite Primary Key</span></span>  

<span data-ttu-id="414d4-128">次の例では、DepartmentID プロパティと Name プロパティを、Department 型の複合主キーになるように構成します。</span><span class="sxs-lookup"><span data-stu-id="414d4-128">The following example configures the DepartmentID and Name properties to be the composite primary key of the Department type.</span></span>  

``` csharp
modelBuilder.Entity<Department>().HasKey(t => new { t.DepartmentID, t.Name });
```  

### <a name="switching-off-identity-for-numeric-primary-keys"></a><span data-ttu-id="414d4-129">数値主キーの Id をオフにする</span><span class="sxs-lookup"><span data-stu-id="414d4-129">Switching off Identity for Numeric Primary Keys</span></span>  

<span data-ttu-id="414d4-130">次の例では、DepartmentID プロパティを System.componentmodel に設定して、値がデータベースによって生成されないことを示します。</span><span class="sxs-lookup"><span data-stu-id="414d4-130">The following example sets the DepartmentID property to System.ComponentModel.DataAnnotations.DatabaseGeneratedOption.None to indicate that the value will not be generated by the database.</span></span>  

``` csharp
modelBuilder.Entity<Department>().Property(t => t.DepartmentID)
    .HasDatabaseGeneratedOption(DatabaseGeneratedOption.None);
```  

### <a name="specifying-the-maximum-length-on-a-property"></a><span data-ttu-id="414d4-131">プロパティの最大長の指定</span><span class="sxs-lookup"><span data-stu-id="414d4-131">Specifying the Maximum Length on a Property</span></span>  

<span data-ttu-id="414d4-132">次の例では、Name プロパティは50文字以内である必要があります。</span><span class="sxs-lookup"><span data-stu-id="414d4-132">In the following example, the Name property should be no longer than 50 characters.</span></span> <span data-ttu-id="414d4-133">50文字を超える値を指定すると、 [Dbentityvalidationexception](https://msdn.microsoft.com/library/system.data.entity.validation.dbentityvalidationexception.aspx) 例外が発生します。</span><span class="sxs-lookup"><span data-stu-id="414d4-133">If you make the value longer than 50 characters, you will get a [DbEntityValidationException](https://msdn.microsoft.com/library/system.data.entity.validation.dbentityvalidationexception.aspx) exception.</span></span> <span data-ttu-id="414d4-134">Code First がこのモデルからデータベースを作成する場合は、Name 列の最大長も50文字に設定します。</span><span class="sxs-lookup"><span data-stu-id="414d4-134">If Code First creates a database from this model it will also set the maximum length of the Name column to 50 characters.</span></span>  

``` csharp
modelBuilder.Entity<Department>().Property(t => t.Name).HasMaxLength(50);
```  

### <a name="configuring-the-property-to-be-required"></a><span data-ttu-id="414d4-135">必須のプロパティを構成しています</span><span class="sxs-lookup"><span data-stu-id="414d4-135">Configuring the Property to be Required</span></span>  

<span data-ttu-id="414d4-136">次の例では、Name プロパティが必要です。</span><span class="sxs-lookup"><span data-stu-id="414d4-136">In the following example, the Name property is required.</span></span> <span data-ttu-id="414d4-137">名前を指定しない場合は、DbEntityValidationException 例外が発生します。</span><span class="sxs-lookup"><span data-stu-id="414d4-137">If you do not specify the Name, you will get a DbEntityValidationException exception.</span></span> <span data-ttu-id="414d4-138">Code First がこのモデルからデータベースを作成する場合、通常、このプロパティを格納するために使用される列は null 非許容になります。</span><span class="sxs-lookup"><span data-stu-id="414d4-138">If Code First creates a database from this model then the column used to store this property will usually be non-nullable.</span></span>  

> [!NOTE]
> <span data-ttu-id="414d4-139">場合によっては、プロパティが必要な場合でも、データベース内の列を null 非許容にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="414d4-139">In some cases it may not be possible for the column in the database to be non-nullable even though the property is required.</span></span> <span data-ttu-id="414d4-140">たとえば、複数の型に対して TPH 継承戦略データを使用すると、1つのテーブルに格納されます。</span><span class="sxs-lookup"><span data-stu-id="414d4-140">For example, when using a TPH inheritance strategy data for multiple types is stored in a single table.</span></span> <span data-ttu-id="414d4-141">派生型に必須のプロパティが含まれている場合、階層内のすべての型がこのプロパティを持つわけではないため、列を null 非許容にすることはできません。</span><span class="sxs-lookup"><span data-stu-id="414d4-141">If a derived type includes a required property the column cannot be made non-nullable since not all types in the hierarchy will have this property.</span></span>  

``` csharp
modelBuilder.Entity<Department>().Property(t => t.Name).IsRequired();
```  

### <a name="configuring-an-index-on-one-or-more-properties"></a><span data-ttu-id="414d4-142">1つまたは複数のプロパティのインデックスの構成</span><span class="sxs-lookup"><span data-stu-id="414d4-142">Configuring an Index on one or more properties</span></span>  

> [!NOTE]
> <span data-ttu-id="414d4-143">**Ef 6.1 以降のみ** -Index 属性が Entity Framework 6.1 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="414d4-143">**EF6.1 Onwards Only** - The Index attribute was introduced in Entity Framework 6.1.</span></span> <span data-ttu-id="414d4-144">以前のバージョンを使用している場合、このセクションの情報は適用されません。</span><span class="sxs-lookup"><span data-stu-id="414d4-144">If you are using an earlier version the information in this section does not apply.</span></span>  

<span data-ttu-id="414d4-145">インデックスの作成は、Fluent API ではネイティブでサポートされていませんが、Fluent API を使用して **Indexattribute** のサポートを利用できます。</span><span class="sxs-lookup"><span data-stu-id="414d4-145">Creating indexes isn't natively supported by the Fluent API, but you can make use of the support for **IndexAttribute** via the Fluent API.</span></span> <span data-ttu-id="414d4-146">インデックスの属性は、モデルの注釈をモデルに含めることによって処理され、その後、パイプラインの後の方でデータベースのインデックスに変換されます。</span><span class="sxs-lookup"><span data-stu-id="414d4-146">Index attributes are processed by including a model annotation on the model that is then turned into an Index in the database later in the pipeline.</span></span> <span data-ttu-id="414d4-147">Fluent API を使用して、これらの同じ注釈を手動で追加できます。</span><span class="sxs-lookup"><span data-stu-id="414d4-147">You can manually add these same annotations using the Fluent API.</span></span>  

<span data-ttu-id="414d4-148">これを行う最も簡単な方法は、新しいインデックスのすべての設定を含む **Indexattribute** のインスタンスを作成することです。</span><span class="sxs-lookup"><span data-stu-id="414d4-148">The easiest way to do this is to create an instance of **IndexAttribute** that contains all the settings for the new index.</span></span> <span data-ttu-id="414d4-149">その後、 **Indexannotation** のインスタンスを作成できます。このインスタンスは、ef モデルに格納できるモデル注釈に **indexannotation** 設定を変換する ef 固有の型です。</span><span class="sxs-lookup"><span data-stu-id="414d4-149">You can then create an instance of **IndexAnnotation** which is an EF specific type that will convert the **IndexAttribute** settings into a model annotation that can be stored on the EF model.</span></span> <span data-ttu-id="414d4-150">これらは、Fluent API の **Hascolumnannotation** メソッドに渡すことができ、注釈の名前の **インデックス** を指定します。</span><span class="sxs-lookup"><span data-stu-id="414d4-150">These can then be passed to the **HasColumnAnnotation** method on the Fluent API, specifying the name **Index** for the annotation.</span></span>  

``` csharp
modelBuilder
    .Entity<Department>()
    .Property(t => t.Name)
    .HasColumnAnnotation("Index", new IndexAnnotation(new IndexAttribute()));
```  

<span data-ttu-id="414d4-151">**Indexattribute**で使用できる設定の完全な一覧については、「 [Code First データ注釈](xref:ef6/modeling/code-first/data-annotations)」の「 *Index* 」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="414d4-151">For a complete list of the settings available in **IndexAttribute**, see the *Index* section of [Code First Data Annotations](xref:ef6/modeling/code-first/data-annotations).</span></span> <span data-ttu-id="414d4-152">これには、インデックス名のカスタマイズ、一意のインデックスの作成、複数列のインデックスの作成が含まれます。</span><span class="sxs-lookup"><span data-stu-id="414d4-152">This includes customizing the index name, creating unique indexes, and creating multi-column indexes.</span></span>  

<span data-ttu-id="414d4-153">Indexattribute のコンストラクターに**Indexattribute**の配列を渡すことにより、1つのプロパティに対し**IndexAnnotation**て複数のインデックス注釈を指定できます。</span><span class="sxs-lookup"><span data-stu-id="414d4-153">You can specify multiple index annotations on a single property by passing an array of **IndexAttribute** to the constructor of **IndexAnnotation**.</span></span>  

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

### <a name="specifying-not-to-map-a-clr-property-to-a-column-in-the-database"></a><span data-ttu-id="414d4-154">CLR プロパティをデータベースの列にマップしないように指定する</span><span class="sxs-lookup"><span data-stu-id="414d4-154">Specifying Not to Map a CLR Property to a Column in the Database</span></span>  

<span data-ttu-id="414d4-155">次の例は、CLR 型のプロパティがデータベースの列にマップされないように指定する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="414d4-155">The following example shows how to specify that a property on a CLR type is not mapped to a column in the database.</span></span>  

``` csharp
modelBuilder.Entity<Department>().Ignore(t => t.Budget);
```  

### <a name="mapping-a-clr-property-to-a-specific-column-in-the-database"></a><span data-ttu-id="414d4-156">CLR プロパティをデータベース内の特定の列にマップする</span><span class="sxs-lookup"><span data-stu-id="414d4-156">Mapping a CLR Property to a Specific Column in the Database</span></span>  

<span data-ttu-id="414d4-157">次の例では、CLR プロパティの名前を DepartmentName データベース列にマップします。</span><span class="sxs-lookup"><span data-stu-id="414d4-157">The following example maps the Name CLR property to the DepartmentName database column.</span></span>  

``` csharp
modelBuilder.Entity<Department>()
    .Property(t => t.Name)
    .HasColumnName("DepartmentName");
```  

### <a name="renaming-a-foreign-key-that-is-not-defined-in-the-model"></a><span data-ttu-id="414d4-158">モデルで定義されていない外部キーの名前変更</span><span class="sxs-lookup"><span data-stu-id="414d4-158">Renaming a Foreign Key That Is Not Defined in the Model</span></span>  

<span data-ttu-id="414d4-159">CLR 型で外部キーを定義しない場合に、データベースに含める名前を指定する場合は、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="414d4-159">If you choose not to define a foreign key on a CLR type, but want to specify what name it should have in the database, do the following:</span></span>  

``` csharp
modelBuilder.Entity<Course>()
    .HasRequired(c => c.Department)
    .WithMany(t => t.Courses)
    .Map(m => m.MapKey("ChangedDepartmentID"));
```  

### <a name="configuring-whether-a-string-property-supports-unicode-content"></a><span data-ttu-id="414d4-160">文字列プロパティが Unicode コンテンツをサポートするかどうかの構成</span><span class="sxs-lookup"><span data-stu-id="414d4-160">Configuring whether a String Property Supports Unicode Content</span></span>  

<span data-ttu-id="414d4-161">既定では、文字列は Unicode (SQL Server の nvarchar) です。</span><span class="sxs-lookup"><span data-stu-id="414d4-161">By default strings are Unicode (nvarchar in SQL Server).</span></span> <span data-ttu-id="414d4-162">IsUnicode メソッドを使用して、文字列を varchar 型にする必要があることを指定できます。</span><span class="sxs-lookup"><span data-stu-id="414d4-162">You can use the IsUnicode method to specify that a string should be of varchar type.</span></span>  

``` csharp
modelBuilder.Entity<Department>()
    .Property(t => t.Name)
    .IsUnicode(false);
```  

### <a name="configuring-the-data-type-of-a-database-column"></a><span data-ttu-id="414d4-163">データベース列のデータ型の構成</span><span class="sxs-lookup"><span data-stu-id="414d4-163">Configuring the Data Type of a Database Column</span></span>  

<span data-ttu-id="414d4-164">[HasColumnType](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.configuration.stringcolumnconfiguration.hascolumntype.aspx)メソッドを使用すると、同じ基本型の異なる表現へのマッピングが可能になります。</span><span class="sxs-lookup"><span data-stu-id="414d4-164">The [HasColumnType](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.configuration.stringcolumnconfiguration.hascolumntype.aspx) method enables mapping to different representations of the same basic type.</span></span> <span data-ttu-id="414d4-165">この方法を使用しても、実行時にデータの変換を実行することはできません。</span><span class="sxs-lookup"><span data-stu-id="414d4-165">Using this method does not enable you to perform any conversion of the data at run time.</span></span> <span data-ttu-id="414d4-166">列を varchar に設定する場合は、データベースに依存しないため、IsUnicode を使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="414d4-166">Note that IsUnicode is the preferred way of setting columns to varchar, as it is database agnostic.</span></span>  

``` csharp
modelBuilder.Entity<Department>()   
    .Property(p => p.Name)   
    .HasColumnType("varchar");
```  

### <a name="configuring-properties-on-a-complex-type"></a><span data-ttu-id="414d4-167">複合型のプロパティの構成</span><span class="sxs-lookup"><span data-stu-id="414d4-167">Configuring Properties on a Complex Type</span></span>  

<span data-ttu-id="414d4-168">複合型のスカラープロパティを構成するには、2つの方法があります。</span><span class="sxs-lookup"><span data-stu-id="414d4-168">There are two ways to configure scalar properties on a complex type.</span></span>  

<span data-ttu-id="414d4-169">ComplexTypeConfiguration プロパティを呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="414d4-169">You can call Property on ComplexTypeConfiguration.</span></span>  

``` csharp
modelBuilder.ComplexType<Details>()
    .Property(t => t.Location)
    .HasMaxLength(20);
```  

<span data-ttu-id="414d4-170">ドット表記を使用して、複合型のプロパティにアクセスすることもできます。</span><span class="sxs-lookup"><span data-stu-id="414d4-170">You can also use the dot notation to access a property of a complex type.</span></span>  

``` csharp
modelBuilder.Entity<OnsiteCourse>()
    .Property(t => t.Details.Location)
    .HasMaxLength(20);
```  

### <a name="configuring-a-property-to-be-used-as-an-optimistic-concurrency-token"></a><span data-ttu-id="414d4-171">オプティミスティック同時実行トークンとして使用するプロパティの構成</span><span class="sxs-lookup"><span data-stu-id="414d4-171">Configuring a Property to Be Used as an Optimistic Concurrency Token</span></span>  

<span data-ttu-id="414d4-172">エンティティのプロパティが同時実行トークンを表すように指定するには、ConcurrencyCheck 属性または IsConcurrencyToken メソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="414d4-172">To specify that a property in an entity represents a concurrency token, you can use either the ConcurrencyCheck attribute or the IsConcurrencyToken method.</span></span>  

``` csharp
modelBuilder.Entity<OfficeAssignment>()
    .Property(t => t.Timestamp)
    .IsConcurrencyToken();
```  

<span data-ttu-id="414d4-173">また、IsRowVersion メソッドを使用して、データベースの行バージョンとしてプロパティを構成することもできます。</span><span class="sxs-lookup"><span data-stu-id="414d4-173">You can also use the IsRowVersion method to configure the property to be a row version in the database.</span></span> <span data-ttu-id="414d4-174">プロパティを行バージョンに設定すると、オプティミスティック同時実行トークンとして自動的に構成されます。</span><span class="sxs-lookup"><span data-stu-id="414d4-174">Setting the property to be a row version automatically configures it to be an optimistic concurrency token.</span></span>  

``` csharp
modelBuilder.Entity<OfficeAssignment>()
    .Property(t => t.Timestamp)
    .IsRowVersion();
```  

## <a name="type-mapping"></a><span data-ttu-id="414d4-175">型マッピング</span><span class="sxs-lookup"><span data-stu-id="414d4-175">Type Mapping</span></span>  

### <a name="specifying-that-a-class-is-a-complex-type"></a><span data-ttu-id="414d4-176">クラスが複合型であることを指定する</span><span class="sxs-lookup"><span data-stu-id="414d4-176">Specifying That a Class Is a Complex Type</span></span>  

<span data-ttu-id="414d4-177">慣例により、主キーが指定されていない型は複合型として扱われます。</span><span class="sxs-lookup"><span data-stu-id="414d4-177">By convention, a type that has no primary key specified is treated as a complex type.</span></span> <span data-ttu-id="414d4-178">Code First が複合型を検出しないシナリオがいくつかあります (たとえば、ID というプロパティがあり、それが主キーであることを意味していない場合など)。</span><span class="sxs-lookup"><span data-stu-id="414d4-178">There are some scenarios where Code First will not detect a complex type (for example, if you do have a property called ID, but you do not mean for it to be a primary key).</span></span> <span data-ttu-id="414d4-179">このような場合は、fluent API を使用して、型が複合型であることを明示的に指定します。</span><span class="sxs-lookup"><span data-stu-id="414d4-179">In such cases, you would use the fluent API to explicitly specify that a type is a complex type.</span></span>  

``` csharp
modelBuilder.ComplexType<Details>();
```  

### <a name="specifying-not-to-map-a-clr-entity-type-to-a-table-in-the-database"></a><span data-ttu-id="414d4-180">CLR エンティティ型をデータベース内のテーブルにマップしないように指定する</span><span class="sxs-lookup"><span data-stu-id="414d4-180">Specifying Not to Map a CLR Entity Type to a Table in the Database</span></span>  

<span data-ttu-id="414d4-181">次の例では、CLR 型を、データベース内のテーブルにマップされないものから除外する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="414d4-181">The following example shows how to exclude a CLR type from being mapped to a table in the database.</span></span>  

``` csharp
modelBuilder.Ignore<OnlineCourse>();
```  

### <a name="mapping-an-entity-type-to-a-specific-table-in-the-database"></a><span data-ttu-id="414d4-182">データベース内の特定のテーブルへのエンティティ型のマッピング</span><span class="sxs-lookup"><span data-stu-id="414d4-182">Mapping an Entity Type to a Specific Table in the Database</span></span>  

<span data-ttu-id="414d4-183">Department のすべてのプロパティは、t_ Department という名前のテーブルの列にマップされます。</span><span class="sxs-lookup"><span data-stu-id="414d4-183">All properties of Department will be mapped to columns in a table called t_ Department.</span></span>  

``` csharp
modelBuilder.Entity<Department>()  
    .ToTable("t_Department");
```  

<span data-ttu-id="414d4-184">次のように、スキーマ名を指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="414d4-184">You can also specify the schema name like this:</span></span>  

``` csharp
modelBuilder.Entity<Department>()  
    .ToTable("t_Department", "school");
```  

### <a name="mapping-the-table-per-hierarchy-tph-inheritance"></a><span data-ttu-id="414d4-185">階層ごとのテーブル (TPH) の継承のマッピング</span><span class="sxs-lookup"><span data-stu-id="414d4-185">Mapping the Table-Per-Hierarchy (TPH) Inheritance</span></span>  

<span data-ttu-id="414d4-186">TPH マッピングシナリオでは、継承階層内のすべての型が1つのテーブルにマップされます。</span><span class="sxs-lookup"><span data-stu-id="414d4-186">In the TPH mapping scenario, all types in an inheritance hierarchy are mapped to a single table.</span></span> <span data-ttu-id="414d4-187">識別子列は、各行の種類を識別するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="414d4-187">A discriminator column is used to identify the type of each row.</span></span> <span data-ttu-id="414d4-188">Code First でモデルを作成する場合、TPH は、継承階層に参加する型の既定の方法です。</span><span class="sxs-lookup"><span data-stu-id="414d4-188">When creating your model with Code First, TPH is the default strategy for the types that participate in the inheritance hierarchy.</span></span> <span data-ttu-id="414d4-189">既定では、識別子の列が "識別子" という名前のテーブルに追加され、階層内の各型の CLR 型名が識別子の値に使用されます。</span><span class="sxs-lookup"><span data-stu-id="414d4-189">By default, the discriminator column is added to the table with the name “Discriminator” and the CLR type name of each type in the hierarchy is used for the discriminator values.</span></span> <span data-ttu-id="414d4-190">既定の動作を変更するには、fluent API を使用します。</span><span class="sxs-lookup"><span data-stu-id="414d4-190">You can modify the default behavior by using the fluent API.</span></span>  

``` csharp
modelBuilder.Entity<Course>()  
    .Map<Course>(m => m.Requires("Type").HasValue("Course"))  
    .Map<OnsiteCourse>(m => m.Requires("Type").HasValue("OnsiteCourse"));
```  

### <a name="mapping-the-table-per-type-tpt-inheritance"></a><span data-ttu-id="414d4-191">テーブル型 (TPT) の継承のマッピング</span><span class="sxs-lookup"><span data-stu-id="414d4-191">Mapping the Table-Per-Type (TPT) Inheritance</span></span>  

<span data-ttu-id="414d4-192">TPT マッピングシナリオでは、すべての型が個々のテーブルにマップされます。</span><span class="sxs-lookup"><span data-stu-id="414d4-192">In the TPT mapping scenario, all types are mapped to individual tables.</span></span> <span data-ttu-id="414d4-193">基本データ型または派生型のみに属するプロパティは、その型にマップされたテーブルに格納されます。</span><span class="sxs-lookup"><span data-stu-id="414d4-193">Properties that belong solely to a base type or derived type are stored in a table that maps to that type.</span></span> <span data-ttu-id="414d4-194">派生型にマップされるテーブルには、派生テーブルとベーステーブルを結合する外部キーも格納されます。</span><span class="sxs-lookup"><span data-stu-id="414d4-194">Tables that map to derived types also store a foreign key that joins the derived table with the base table.</span></span>  

``` csharp
modelBuilder.Entity<Course>().ToTable("Course");  
modelBuilder.Entity<OnsiteCourse>().ToTable("OnsiteCourse");
```  

### <a name="mapping-the-table-per-concrete-class-tpc-inheritance"></a><span data-ttu-id="414d4-195">テーブル単位のクラス (TPC) の継承のマッピング</span><span class="sxs-lookup"><span data-stu-id="414d4-195">Mapping the Table-Per-Concrete Class (TPC) Inheritance</span></span>  

<span data-ttu-id="414d4-196">TPC マッピングシナリオでは、階層内のすべての非抽象型が個々のテーブルにマップされます。</span><span class="sxs-lookup"><span data-stu-id="414d4-196">In the TPC mapping scenario, all non-abstract types in the hierarchy are mapped to individual tables.</span></span> <span data-ttu-id="414d4-197">派生クラスにマップされるテーブルには、データベースの基本クラスにマップされるテーブルとのリレーションシップがありません。</span><span class="sxs-lookup"><span data-stu-id="414d4-197">The tables that map to the derived classes have no relationship to the table that maps to the base class in the database.</span></span> <span data-ttu-id="414d4-198">継承されたプロパティを含む、クラスのすべてのプロパティは、対応するテーブルの列にマップされます。</span><span class="sxs-lookup"><span data-stu-id="414d4-198">All properties of a class, including inherited properties, are mapped to columns of the corresponding table.</span></span>  

<span data-ttu-id="414d4-199">MapInheritedProperties メソッドを呼び出して、それぞれの派生型を構成します。</span><span class="sxs-lookup"><span data-stu-id="414d4-199">Call the MapInheritedProperties method to configure each derived type.</span></span> <span data-ttu-id="414d4-200">MapInheritedProperties は、基本クラスから継承されたすべてのプロパティを、派生クラスのテーブルの新しい列に再マップします。</span><span class="sxs-lookup"><span data-stu-id="414d4-200">MapInheritedProperties remaps all properties that were inherited from the base class to new columns in the table for the derived class.</span></span>  

> [!NOTE]
> <span data-ttu-id="414d4-201">TPC 継承階層に参加しているテーブルでは主キーが共有されないため、同じ identity シードを使用してデータベースが生成された値がある場合は、サブクラスにマップされているテーブルに挿入すると、重複するエンティティキーが存在することに注意してください。</span><span class="sxs-lookup"><span data-stu-id="414d4-201">Note that because the tables participating in TPC inheritance hierarchy do not share a primary key there will be duplicate entity keys when inserting in tables that are mapped to subclasses if you have database generated values with the same identity seed.</span></span> <span data-ttu-id="414d4-202">この問題を解決するには、各テーブルに対して異なる初期シード値を指定するか、プライマリキープロパティで id をオフにします。</span><span class="sxs-lookup"><span data-stu-id="414d4-202">To solve this problem you can either specify a different initial seed value for each table or switch off identity on the primary key property.</span></span> <span data-ttu-id="414d4-203">Id は、Code First を操作する場合の整数キープロパティの既定値です。</span><span class="sxs-lookup"><span data-stu-id="414d4-203">Identity is the default value for integer key properties when working with Code First.</span></span>  

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

### <a name="mapping-properties-of-an-entity-type-to-multiple-tables-in-the-database-entity-splitting"></a><span data-ttu-id="414d4-204">エンティティ型のプロパティをデータベース内の複数のテーブルにマップする (エンティティ分割)</span><span class="sxs-lookup"><span data-stu-id="414d4-204">Mapping Properties of an Entity Type to Multiple Tables in the Database (Entity Splitting)</span></span>  

<span data-ttu-id="414d4-205">エンティティ分割を使用すると、エンティティ型のプロパティを複数のテーブルに分散させることができます。</span><span class="sxs-lookup"><span data-stu-id="414d4-205">Entity splitting allows the properties of an entity type to be spread across multiple tables.</span></span> <span data-ttu-id="414d4-206">次の例では、Department エンティティは Department と DepartmentDetails の2つのテーブルに分割されています。</span><span class="sxs-lookup"><span data-stu-id="414d4-206">In the following example, the Department entity is split into two tables: Department and DepartmentDetails.</span></span> <span data-ttu-id="414d4-207">エンティティ分割では、マップメソッドの複数の呼び出しを使用して、プロパティのサブセットを特定のテーブルにマップします。</span><span class="sxs-lookup"><span data-stu-id="414d4-207">Entity splitting uses multiple calls to the Map method to map a subset of properties to a specific table.</span></span>  

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

### <a name="mapping-multiple-entity-types-to-one-table-in-the-database-table-splitting"></a><span data-ttu-id="414d4-208">データベース内の1つのテーブルへの複数のエンティティ型のマッピング (テーブル分割)</span><span class="sxs-lookup"><span data-stu-id="414d4-208">Mapping Multiple Entity Types to One Table in the Database (Table Splitting)</span></span>  

<span data-ttu-id="414d4-209">次の例では、主キーを共有する2つのエンティティ型を1つのテーブルにマップします。</span><span class="sxs-lookup"><span data-stu-id="414d4-209">The following example maps two entity types that share a primary key to one table.</span></span>  

``` csharp
modelBuilder.Entity<OfficeAssignment>()
    .HasKey(t => t.InstructorID);

modelBuilder.Entity<Instructor>()
    .HasRequired(t => t.OfficeAssignment)
    .WithRequiredPrincipal(t => t.Instructor);

modelBuilder.Entity<Instructor>().ToTable("Instructor");

modelBuilder.Entity<OfficeAssignment>().ToTable("Instructor");
```  

### <a name="mapping-an-entity-type-to-insertupdatedelete-stored-procedures-ef6-onwards"></a><span data-ttu-id="414d4-210">ストアドプロシージャの挿入/更新/削除へのエンティティ型のマッピング (EF6 以降)</span><span class="sxs-lookup"><span data-stu-id="414d4-210">Mapping an Entity Type to Insert/Update/Delete Stored Procedures (EF6 onwards)</span></span>  

<span data-ttu-id="414d4-211">EF6 以降では、insert update および delete のストアドプロシージャを使用するようにエンティティをマップできます。</span><span class="sxs-lookup"><span data-stu-id="414d4-211">Starting with EF6 you can map an entity to use stored procedures for insert update and delete.</span></span> <span data-ttu-id="414d4-212">詳細については、「 [ストアドプロシージャの挿入/更新/削除 Code First](xref:ef6/modeling/code-first/fluent/cud-stored-procedures)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="414d4-212">For more details see, [Code First Insert/Update/Delete Stored Procedures](xref:ef6/modeling/code-first/fluent/cud-stored-procedures).</span></span>  

## <a name="model-used-in-samples"></a><span data-ttu-id="414d4-213">サンプルで使用されるモデル</span><span class="sxs-lookup"><span data-stu-id="414d4-213">Model Used in Samples</span></span>  

<span data-ttu-id="414d4-214">このページのサンプルには、次の Code First モデルが使用されています。</span><span class="sxs-lookup"><span data-stu-id="414d4-214">The following Code First model is used for the samples on this page.</span></span>  

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
