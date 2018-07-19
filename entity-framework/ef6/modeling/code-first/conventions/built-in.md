---
title: コード規則の最初の EF6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: bc644573-c2b2-4ed7-8745-3c37c41058ad
caps.latest.revision: 4
ms.openlocfilehash: b124a034ba900780cc4d7e1354408cd3995e874e
ms.sourcegitcommit: f05e7b62584cf228f17390bb086a61d505712e1b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2018
ms.locfileid: "39121875"
---
# <a name="code-first-conventions"></a><span data-ttu-id="bd259-102">コードの最初の規則</span><span class="sxs-lookup"><span data-stu-id="bd259-102">Code First Conventions</span></span>
<span data-ttu-id="bd259-103">まず、コードを使用すると、c# または Visual Basic .NET のクラスを使用してモデルを記述できます。</span><span class="sxs-lookup"><span data-stu-id="bd259-103">Code First enables you to describe a model by using C# or Visual Basic .NET classes.</span></span> <span data-ttu-id="bd259-104">規則を使用して、モデルの基本的な形状が検出されます。</span><span class="sxs-lookup"><span data-stu-id="bd259-104">The basic shape of the model is detected by using conventions.</span></span> <span data-ttu-id="bd259-105">規則は、Code First を使用する場合は、クラス定義に基づく概念モデルを自動的に構成するために使用するルールのセットです。</span><span class="sxs-lookup"><span data-stu-id="bd259-105">Conventions are sets of rules that are used to automatically configure a conceptual model based on class definitions when working with Code First.</span></span> <span data-ttu-id="bd259-106">規則は、System.Data.Entity.ModelConfiguration.Conventions 名前空間で定義されます。</span><span class="sxs-lookup"><span data-stu-id="bd259-106">The conventions are defined in the System.Data.Entity.ModelConfiguration.Conventions namespace.</span></span>  

<span data-ttu-id="bd259-107">さらに、データ注釈または fluent API を使用して、モデルを構成できます。</span><span class="sxs-lookup"><span data-stu-id="bd259-107">You can further configure your model by using data annotations or the fluent API.</span></span> <span data-ttu-id="bd259-108">優先順位は、データ注釈と規則が続く、fluent API を使用した構成に付与されます。</span><span class="sxs-lookup"><span data-stu-id="bd259-108">Precedence is given to configuration through the fluent API followed by data annotations and then conventions.</span></span> <span data-ttu-id="bd259-109">詳細については、次を参照してください[データ注釈](~/ef6/modeling/code-first/data-annotations.md)、 [Fluent API - リレーションシップ](~/ef6/modeling/code-first/fluent/relationships.md)、 [Fluent API の種類とプロパティ](~/ef6/modeling/code-first/fluent/types-and-properties.md)と[VB.NETにFluentAPI](~/ef6/modeling/code-first/fluent/vb.md)。</span><span class="sxs-lookup"><span data-stu-id="bd259-109">For more information see [Data Annotations](~/ef6/modeling/code-first/data-annotations.md), [Fluent API - Relationships](~/ef6/modeling/code-first/fluent/relationships.md), [Fluent API - Types & Properties](~/ef6/modeling/code-first/fluent/types-and-properties.md) and [Fluent API with VB.NET](~/ef6/modeling/code-first/fluent/vb.md).</span></span>  

<span data-ttu-id="bd259-110">Code First 規約の詳細な一覧が表示されます、 [API ドキュメント](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx)します。</span><span class="sxs-lookup"><span data-stu-id="bd259-110">A detailed list of Code First conventions is available in the [API Documentation](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx).</span></span> <span data-ttu-id="bd259-111">このトピックでは、Code First によって使用される規則の概要を示します。</span><span class="sxs-lookup"><span data-stu-id="bd259-111">This topic provides an overview of the conventions used by Code First.</span></span>  

## <a name="type-discovery"></a><span data-ttu-id="bd259-112">型の探索</span><span class="sxs-lookup"><span data-stu-id="bd259-112">Type Discovery</span></span>  

<span data-ttu-id="bd259-113">Code First の開発を使用する場合に、(ドメイン) の概念モデルを定義する .NET Framework クラスを記述することで、通常で開始します。</span><span class="sxs-lookup"><span data-stu-id="bd259-113">When using Code First development you usually begin by writing .NET Framework classes that define your conceptual (domain) model.</span></span> <span data-ttu-id="bd259-114">クラスを定義するだけでなくも設定する必要が**DbContext**モデルに追加する型も把握しています。</span><span class="sxs-lookup"><span data-stu-id="bd259-114">In addition to defining the classes, you also need to let **DbContext** know which types you want to include in the model.</span></span> <span data-ttu-id="bd259-115">派生したコンテキスト クラスを定義するには、 **DbContext**公開**DbSet**モデルの一部に型のプロパティ。</span><span class="sxs-lookup"><span data-stu-id="bd259-115">To do this, you define a context class that derives from **DbContext** and exposes **DbSet** properties for the types that you want to be part of the model.</span></span> <span data-ttu-id="bd259-116">コードでは、まずこれらの型が含まれますされ、参照先の型が別のアセンブリで定義されている場合でもが、参照先の種類でもプルされます。</span><span class="sxs-lookup"><span data-stu-id="bd259-116">Code First will include these types and also will pull in any referenced types, even if the referenced types are defined in a different assembly.</span></span>  

<span data-ttu-id="bd259-117">型は、継承階層に参加して、定義するのに十分な処理は、 **DbSet**基底クラスと同じアセンブリ内にある場合、基底クラスと派生型のプロパティは自動的に含まれるになります。</span><span class="sxs-lookup"><span data-stu-id="bd259-117">If your types participate in an inheritance hierarchy, it is enough to define a **DbSet** property for the base class, and the derived types will be automatically included, if they are in the same assembly as the base class.</span></span>  

<span data-ttu-id="bd259-118">次の例では、1 つしかない**DbSet**プロパティで定義されている、 **SchoolEntities**クラス (**部門**)。</span><span class="sxs-lookup"><span data-stu-id="bd259-118">In the following example, there is only one **DbSet** property defined on the **SchoolEntities** class (**Departments**).</span></span> <span data-ttu-id="bd259-119">コードは、まず探索し、参照先の種類でプルこのプロパティを使用します。</span><span class="sxs-lookup"><span data-stu-id="bd259-119">Code First uses this property to discover and pull in any referenced types.</span></span>  

``` csharp
public class SchoolEntities : DbContext
{
    public DbSet<Department> Departments { get; set; }
}

public class Department
{
    // Primary key
    public int DepartmentID { get; set; }
    public string Name { get; set; }

    // Navigation property
    public virtual ICollection<Course> Courses { get; set; }
}

public class Course
{
    // Primary key
    public int CourseID { get; set; }

    public string Title { get; set; }
    public int Credits { get; set; }

    // Foreign key
    public int DepartmentID { get; set; }

    // Navigation properties
    public virtual Department Department { get; set; }
}

public partial class OnlineCourse : Course
{
    public string URL { get; set; }
}

public partial class OnsiteCourse : Course
{
    public string Location { get; set; }
    public string Days { get; set; }
    public System.DateTime Time { get; set; }
}
```  

<span data-ttu-id="bd259-120">モデルから型を除外する場合を使用して、 **NotMapped**属性または**DbModelBuilder.Ignore** fluent API。</span><span class="sxs-lookup"><span data-stu-id="bd259-120">If you want to exclude a type from the model, use the **NotMapped** attribute or the **DbModelBuilder.Ignore** fluent API.</span></span>  

```  csharp
modelBuilder.Ignore<Department>();
```  

## <a name="primary-key-convention"></a><span data-ttu-id="bd259-121">プライマリ キーの規則</span><span class="sxs-lookup"><span data-stu-id="bd259-121">Primary Key Convention</span></span>  

<span data-ttu-id="bd259-122">コードでは、プロパティは、(いない大文字小文字を区別)、"ID"という名前のクラスのプロパティはまたはクラス名の"ID"が続く場合、主キーを最初は推測します。</span><span class="sxs-lookup"><span data-stu-id="bd259-122">Code First infers that a property is a primary key if a property on a class is named “ID” (not case sensitive), or the class name followed by "ID".</span></span> <span data-ttu-id="bd259-123">主キー プロパティの型が数値か、または GUID がある場合、id 列として構成します。</span><span class="sxs-lookup"><span data-stu-id="bd259-123">If the type of the primary key property is numeric or GUID it will be configured as an identity column.</span></span>  

``` csharp
public class Department
{
    // Primary key
    public int DepartmentID { get; set; }

    . . .  

}
```  

## <a name="relationship-convention"></a><span data-ttu-id="bd259-124">リレーションシップの規則</span><span class="sxs-lookup"><span data-stu-id="bd259-124">Relationship Convention</span></span>  

<span data-ttu-id="bd259-125">Entity Framework では、ナビゲーション プロパティは、2 つのエンティティ型間のリレーションシップを移動する方法を提供します。</span><span class="sxs-lookup"><span data-stu-id="bd259-125">In Entity Framework, navigation properties provide a way to navigate a relationship between two entity types.</span></span> <span data-ttu-id="bd259-126">各オブジェクトは、参加する各リレーションシップに対してナビゲーション プロパティを持つことができます。</span><span class="sxs-lookup"><span data-stu-id="bd259-126">Every object can have a navigation property for every relationship in which it participates.</span></span> <span data-ttu-id="bd259-127">ナビゲーション プロパティを使用するに移動し、参照オブジェクトを返す双方向のリレーションシップを管理できます (多重度がいずれかである場合または 0 または 1) または (多重度が多くの場合) のコレクション。</span><span class="sxs-lookup"><span data-stu-id="bd259-127">Navigation properties allow you to navigate and manage relationships in both directions, returning either a reference object (if the multiplicity is either one or zero-or-one) or a collection (if the multiplicity is many).</span></span> <span data-ttu-id="bd259-128">コードは、まず、型に定義されているナビゲーション プロパティに基づくリレーションシップを推測します。</span><span class="sxs-lookup"><span data-stu-id="bd259-128">Code First infers relationships based on the navigation properties defined on your types.</span></span>  

<span data-ttu-id="bd259-129">ナビゲーションのプロパティだけでなく、外部キー プロパティを依存オブジェクトを表す型を含めることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="bd259-129">In addition to navigation properties, we recommend that you include foreign key properties on the types that represent dependent objects.</span></span> <span data-ttu-id="bd259-130">任意のプロパティは、プリンシパルのプライマリ キー プロパティと同じデータ型と、次の形式のいずれかに続く名前は、リレーションシップの外部キーを表します: '\<ナビゲーション プロパティの名前\>\<プリンシパル主キー プロパティ名\>'、'\<プリンシパル クラス名\>\<主キー プロパティ名\>'、または '\<プリンシパルのプライマリ キー プロパティ名\>'。</span><span class="sxs-lookup"><span data-stu-id="bd259-130">Any property with the same data type as the principal primary key property and with a name that follows one of the following formats represents a foreign key for the relationship: '\<navigation property name\>\<principal primary key property name\>', '\<principal class name\>\<primary key property name\>', or '\<principal primary key property name\>'.</span></span> <span data-ttu-id="bd259-131">複数の一致が見つかった場合は、上に示した順序で優先順位が指定します。</span><span class="sxs-lookup"><span data-stu-id="bd259-131">If multiple matches are found then precedence is given in the order listed above.</span></span> <span data-ttu-id="bd259-132">外部キーの検出は大文字小文字が区別されません。</span><span class="sxs-lookup"><span data-stu-id="bd259-132">Foreign key detection is not case sensitive.</span></span> <span data-ttu-id="bd259-133">外部キー プロパティが検出されると、Code First は推測外部キーの null 値許容属性に基づくリレーションシップの多重度。</span><span class="sxs-lookup"><span data-stu-id="bd259-133">When a foreign key property is detected, Code First infers the multiplicity of the relationship based on the nullability of the foreign key.</span></span> <span data-ttu-id="bd259-134">プロパティが null 許容の場合、リレーションシップが省略可能です。 として登録します。それ以外の場合、リレーションシップが登録されている必須とします。</span><span class="sxs-lookup"><span data-stu-id="bd259-134">If the property is nullable then the relationship is registered as optional; otherwise the relationship is registered as required.</span></span>  

<span data-ttu-id="bd259-135">場合は、依存エンティティの外部キーが許容されませんし、Code First 連鎖削除の設定の関係します。</span><span class="sxs-lookup"><span data-stu-id="bd259-135">If a foreign key on the dependent entity is not nullable, then Code First sets cascade delete on the relationship.</span></span> <span data-ttu-id="bd259-136">依存エンティティの外部キー値が許容、Code First は連鎖削除、リレーションシップに、プリンシパルが削除されたときに、外部キーが設定されます場合、を null にします。</span><span class="sxs-lookup"><span data-stu-id="bd259-136">If a foreign key on the dependent entity is nullable, Code First does not set cascade delete on the relationship, and when the principal is deleted the foreign key will be set to null.</span></span> <span data-ttu-id="bd259-137">多重度とカスケード削除の動作がによって検出された規則は、fluent API を使用してオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="bd259-137">The multiplicity and cascade delete behavior detected by convention can be overridden by using the fluent API.</span></span>  

<span data-ttu-id="bd259-138">次の例では、部門とコースのクラス間のリレーションシップを定義するナビゲーション プロパティと外部キーが使用されます。</span><span class="sxs-lookup"><span data-stu-id="bd259-138">In the following example the navigation properties and a foreign key are used to define the relationship between the Department and Course classes.</span></span>  

``` csharp
public class Department
{
    // Primary key
    public int DepartmentID { get; set; }
    public string Name { get; set; }

    // Navigation property
    public virtual ICollection<Course> Courses { get; set; }
}

public class Course
{
    // Primary key
    public int CourseID { get; set; }

    public string Title { get; set; }
    public int Credits { get; set; }

    // Foreign key
    public int DepartmentID { get; set; }

    // Navigation properties
    public virtual Department Department { get; set; }
}
```  

> [!NOTE]
> <span data-ttu-id="bd259-139">同じ型の間で複数のリレーションシップがある場合 (たとえば、定義する、 **Person**と**帳**クラス、場所、 **Person**クラスには、が含まれています**ReviewedBooks**と**AuthoredBooks**ナビゲーション プロパティ、および**帳**クラスが含まれています、**作成者**と**レビュー担当者**ナビゲーション プロパティ) のデータ注釈または fluent API を使用して、リレーションシップを手動で構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="bd259-139">If you have multiple relationships between the same types (for example, suppose you define the **Person** and **Book** classes, where the **Person** class contains the **ReviewedBooks** and **AuthoredBooks** navigation properties and the **Book** class contains the **Author** and **Reviewer** navigation properties) you need to manually configure the relationships by using Data Annotations or the fluent API.</span></span> <span data-ttu-id="bd259-140">詳細については、次を参照してください。[データ注釈 - リレーションシップ](~/ef6/modeling/code-first/data-annotations.md)と[Fluent API - リレーションシップ](~/ef6/modeling/code-first/fluent/relationships.md)します。</span><span class="sxs-lookup"><span data-stu-id="bd259-140">For more information, see [Data Annotations - Relationships](~/ef6/modeling/code-first/data-annotations.md) and [Fluent API - Relationships](~/ef6/modeling/code-first/fluent/relationships.md).</span></span>  

## <a name="complex-types-convention"></a><span data-ttu-id="bd259-141">複合型の規則</span><span class="sxs-lookup"><span data-stu-id="bd259-141">Complex Types Convention</span></span>  

<span data-ttu-id="bd259-142">Code First が検出、クラス定義を主キーを推論することはできません、およびデータ注釈または fluent API で主キーが登録されていない型は自動的に複合型として登録します。</span><span class="sxs-lookup"><span data-stu-id="bd259-142">When Code First discovers a class definition where a primary key cannot be inferred, and no primary key is registered through data annotations or the fluent API, then the type is automatically registered as a complex type.</span></span> <span data-ttu-id="bd259-143">複合型の検出では、型をエンティティ型を参照し、別の型のコレクション プロパティから参照されていないプロパティにないことも必要です。</span><span class="sxs-lookup"><span data-stu-id="bd259-143">Complex type detection also requires that the type does not have properties that reference entity types and is not referenced from a collection property on another type.</span></span> <span data-ttu-id="bd259-144">次のクラス定義を指定した Code First は推論を**詳細**複合型は、主キーがあるないためです。</span><span class="sxs-lookup"><span data-stu-id="bd259-144">Given the following class definitions Code First would infer that **Details** is a complex type because it has no primary key.</span></span>  

``` csharp
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
```  

## <a name="connection-string-convention"></a><span data-ttu-id="bd259-145">接続文字列の規則</span><span class="sxs-lookup"><span data-stu-id="bd259-145">Connection String Convention</span></span>  

<span data-ttu-id="bd259-146">DbContext は参照を使用する接続の検出を使用している表記規則について[接続とモデル](~/ef6/fundamentals/configuring/connection-strings.md)します。</span><span class="sxs-lookup"><span data-stu-id="bd259-146">To learn about the conventions that DbContext uses to discover the connection to use see [Connections and Models](~/ef6/fundamentals/configuring/connection-strings.md).</span></span>  

## <a name="removing-conventions"></a><span data-ttu-id="bd259-147">規則を削除します。</span><span class="sxs-lookup"><span data-stu-id="bd259-147">Removing Conventions</span></span>  

<span data-ttu-id="bd259-148">System.Data.Entity.ModelConfiguration.Conventions 名前空間で定義されている規則を削除することができます。</span><span class="sxs-lookup"><span data-stu-id="bd259-148">You can remove any of the conventions defined in the System.Data.Entity.ModelConfiguration.Conventions namespace.</span></span> <span data-ttu-id="bd259-149">次の例では、削除**PluralizingTableNameConvention**します。</span><span class="sxs-lookup"><span data-stu-id="bd259-149">The following example removes **PluralizingTableNameConvention**.</span></span>  

``` csharp
public class SchoolEntities : DbContext
{
     . . .

    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        // Configure Code First to ignore PluralizingTableName convention
        // If you keep this convention, the generated tables  
        // will have pluralized names.
        modelBuilder.Conventions.Remove<PluralizingTableNameConvention>();
    }
}
```  

## <a name="custom-conventions"></a><span data-ttu-id="bd259-150">カスタムの規則</span><span class="sxs-lookup"><span data-stu-id="bd259-150">Custom Conventions</span></span>  

<span data-ttu-id="bd259-151">カスタムの規則は、以降の EF6 でサポートされます。</span><span class="sxs-lookup"><span data-stu-id="bd259-151">Custom conventions are supported in EF6 onwards.</span></span> <span data-ttu-id="bd259-152">詳細については、次を参照してください。[カスタム コードの最初の規則](~/ef6/modeling/code-first/conventions/custom.md)します。</span><span class="sxs-lookup"><span data-stu-id="bd259-152">For more information see [Custom Code First Conventions](~/ef6/modeling/code-first/conventions/custom.md).</span></span>
