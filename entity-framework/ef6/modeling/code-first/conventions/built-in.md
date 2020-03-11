---
title: Code First 規則-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: bc644573-c2b2-4ed7-8745-3c37c41058ad
ms.openlocfilehash: 4d03a32db5d84eb37c22617a95005b272172a65d
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78415947"
---
# <a name="code-first-conventions"></a><span data-ttu-id="658fe-102">Code First 規則</span><span class="sxs-lookup"><span data-stu-id="658fe-102">Code First Conventions</span></span>
<span data-ttu-id="658fe-103">Code First では、または Visual Basic .NET クラスC#を使用してモデルを記述できます。</span><span class="sxs-lookup"><span data-stu-id="658fe-103">Code First enables you to describe a model by using C# or Visual Basic .NET classes.</span></span> <span data-ttu-id="658fe-104">モデルの基本図形は、規約を使用して検出されます。</span><span class="sxs-lookup"><span data-stu-id="658fe-104">The basic shape of the model is detected by using conventions.</span></span> <span data-ttu-id="658fe-105">規則は、Code First を操作するときに、クラス定義に基づいて概念モデルを自動的に構成するために使用される規則のセットです。</span><span class="sxs-lookup"><span data-stu-id="658fe-105">Conventions are sets of rules that are used to automatically configure a conceptual model based on class definitions when working with Code First.</span></span> <span data-ttu-id="658fe-106">規則は、system.string 名前空間で定義されています。</span><span class="sxs-lookup"><span data-stu-id="658fe-106">The conventions are defined in the System.Data.Entity.ModelConfiguration.Conventions namespace.</span></span>  

<span data-ttu-id="658fe-107">データ注釈または fluent API を使用して、モデルをさらに構成できます。</span><span class="sxs-lookup"><span data-stu-id="658fe-107">You can further configure your model by using data annotations or the fluent API.</span></span> <span data-ttu-id="658fe-108">優先順位は、fluent API の後にデータ注釈と規則を使用して構成されます。</span><span class="sxs-lookup"><span data-stu-id="658fe-108">Precedence is given to configuration through the fluent API followed by data annotations and then conventions.</span></span> <span data-ttu-id="658fe-109">詳細については、「[データ注釈](~/ef6/modeling/code-first/data-annotations.md)」、「 [Fluent api-リレーションシップ](~/ef6/modeling/code-first/fluent/relationships.md)」、「 [fluent api 型 & プロパティ](~/ef6/modeling/code-first/fluent/types-and-properties.md)」、および「 [VB.NET を使用した fluent api](~/ef6/modeling/code-first/fluent/vb.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="658fe-109">For more information see [Data Annotations](~/ef6/modeling/code-first/data-annotations.md), [Fluent API - Relationships](~/ef6/modeling/code-first/fluent/relationships.md), [Fluent API - Types & Properties](~/ef6/modeling/code-first/fluent/types-and-properties.md) and [Fluent API with VB.NET](~/ef6/modeling/code-first/fluent/vb.md).</span></span>  

<span data-ttu-id="658fe-110">Code First の規則の詳細な一覧については、 [API のドキュメント](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="658fe-110">A detailed list of Code First conventions is available in the [API Documentation](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx).</span></span> <span data-ttu-id="658fe-111">このトピックでは、Code First によって使用される規則の概要について説明します。</span><span class="sxs-lookup"><span data-stu-id="658fe-111">This topic provides an overview of the conventions used by Code First.</span></span>  

## <a name="type-discovery"></a><span data-ttu-id="658fe-112">種類の検出</span><span class="sxs-lookup"><span data-stu-id="658fe-112">Type Discovery</span></span>  

<span data-ttu-id="658fe-113">Code First 開発を使用する場合は、通常、概念 (ドメイン) モデルを定義する .NET Framework クラスを記述することから始めます。</span><span class="sxs-lookup"><span data-stu-id="658fe-113">When using Code First development you usually begin by writing .NET Framework classes that define your conceptual (domain) model.</span></span> <span data-ttu-id="658fe-114">クラスを定義するだけでなく、モデルに含める型を**Dbcontext**に知らせる必要もあります。</span><span class="sxs-lookup"><span data-stu-id="658fe-114">In addition to defining the classes, you also need to let **DbContext** know which types you want to include in the model.</span></span> <span data-ttu-id="658fe-115">これを行うには、 **dbcontext**から派生したコンテキストクラスを定義し、モデルに含める型の**dbcontext**プロパティを公開します。</span><span class="sxs-lookup"><span data-stu-id="658fe-115">To do this, you define a context class that derives from **DbContext** and exposes **DbSet** properties for the types that you want to be part of the model.</span></span> <span data-ttu-id="658fe-116">Code First にはこれらの型が含まれ、参照される型が別のアセンブリで定義されている場合でも、参照される型がすべて取得されます。</span><span class="sxs-lookup"><span data-stu-id="658fe-116">Code First will include these types and also will pull in any referenced types, even if the referenced types are defined in a different assembly.</span></span>  

<span data-ttu-id="658fe-117">型が継承階層に参加している場合は、基本クラスの**Dbset**プロパティを定義するだけで十分です。基底クラスと同じアセンブリ内にある場合は、派生型が自動的に含まれます。</span><span class="sxs-lookup"><span data-stu-id="658fe-117">If your types participate in an inheritance hierarchy, it is enough to define a **DbSet** property for the base class, and the derived types will be automatically included, if they are in the same assembly as the base class.</span></span>  

<span data-ttu-id="658fe-118">次の例では、 **SchoolEntities** **クラス (department**) で定義されている**dbset**プロパティは1つだけです。</span><span class="sxs-lookup"><span data-stu-id="658fe-118">In the following example, there is only one **DbSet** property defined on the **SchoolEntities** class (**Departments**).</span></span> <span data-ttu-id="658fe-119">Code First は、このプロパティを使用して、参照されるすべての型を検出し、プルします。</span><span class="sxs-lookup"><span data-stu-id="658fe-119">Code First uses this property to discover and pull in any referenced types.</span></span>  

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

<span data-ttu-id="658fe-120">モデルから型を除外する場合は、 **Notmapped**属性または**dbmodelbuilder を使用します。** fluent API を無視します。</span><span class="sxs-lookup"><span data-stu-id="658fe-120">If you want to exclude a type from the model, use the **NotMapped** attribute or the **DbModelBuilder.Ignore** fluent API.</span></span>  

```  csharp
modelBuilder.Ignore<Department>();
```  

## <a name="primary-key-convention"></a><span data-ttu-id="658fe-121">主キーの規則</span><span class="sxs-lookup"><span data-stu-id="658fe-121">Primary Key Convention</span></span>  

<span data-ttu-id="658fe-122">クラスのプロパティの名前が "ID" である場合 (大文字と小文字は区別されません)、またはクラス名の後に "ID" が続く場合、Code First は、プロパティが主キーであると推測します。</span><span class="sxs-lookup"><span data-stu-id="658fe-122">Code First infers that a property is a primary key if a property on a class is named “ID” (not case sensitive), or the class name followed by "ID".</span></span> <span data-ttu-id="658fe-123">主キープロパティの型が数値または GUID の場合は、id 列として構成されます。</span><span class="sxs-lookup"><span data-stu-id="658fe-123">If the type of the primary key property is numeric or GUID it will be configured as an identity column.</span></span>  

``` csharp
public class Department
{
    // Primary key
    public int DepartmentID { get; set; }

    . . .  

}
```  

## <a name="relationship-convention"></a><span data-ttu-id="658fe-124">リレーションシップ規則</span><span class="sxs-lookup"><span data-stu-id="658fe-124">Relationship Convention</span></span>  

<span data-ttu-id="658fe-125">Entity Framework では、ナビゲーションプロパティを使用して、2つのエンティティ型間のリレーションシップをナビゲートすることができます。</span><span class="sxs-lookup"><span data-stu-id="658fe-125">In Entity Framework, navigation properties provide a way to navigate a relationship between two entity types.</span></span> <span data-ttu-id="658fe-126">各オブジェクトは、参加する各リレーションシップに対してナビゲーション プロパティを持つことができます。</span><span class="sxs-lookup"><span data-stu-id="658fe-126">Every object can have a navigation property for every relationship in which it participates.</span></span> <span data-ttu-id="658fe-127">ナビゲーションプロパティを使用すると、リレーションシップを双方向に移動して管理し、参照オブジェクト (多重度が1または0または1のいずれか) またはコレクション (多重度が多の場合) を返すことができます。</span><span class="sxs-lookup"><span data-stu-id="658fe-127">Navigation properties allow you to navigate and manage relationships in both directions, returning either a reference object (if the multiplicity is either one or zero-or-one) or a collection (if the multiplicity is many).</span></span> <span data-ttu-id="658fe-128">Code First は、型に定義されているナビゲーションプロパティに基づいてリレーションシップを推測します。</span><span class="sxs-lookup"><span data-stu-id="658fe-128">Code First infers relationships based on the navigation properties defined on your types.</span></span>  

<span data-ttu-id="658fe-129">ナビゲーションプロパティに加えて、依存オブジェクトを表す型に外部キープロパティを含めることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="658fe-129">In addition to navigation properties, we recommend that you include foreign key properties on the types that represent dependent objects.</span></span> <span data-ttu-id="658fe-130">プリンシパルプライマリキープロパティと同じデータ型で、次のいずれかの形式の名前を持つプロパティは、リレーションシップの外部キーを表します。 '\<ナビゲーションプロパティ名\>\<プリンシパル主キープロパティ名\>'、'\<プリンシパルクラス名\>\<主キープロパティ名\>'、または '\<プリンシパルプライマリキープロパティ名\>'。</span><span class="sxs-lookup"><span data-stu-id="658fe-130">Any property with the same data type as the principal primary key property and with a name that follows one of the following formats represents a foreign key for the relationship: '\<navigation property name\>\<principal primary key property name\>', '\<principal class name\>\<primary key property name\>', or '\<principal primary key property name\>'.</span></span> <span data-ttu-id="658fe-131">複数の一致が見つかった場合は、上記の順序で優先順位が付けられます。</span><span class="sxs-lookup"><span data-stu-id="658fe-131">If multiple matches are found then precedence is given in the order listed above.</span></span> <span data-ttu-id="658fe-132">外部キーの検出では、大文字と小文字は区別されません。</span><span class="sxs-lookup"><span data-stu-id="658fe-132">Foreign key detection is not case sensitive.</span></span> <span data-ttu-id="658fe-133">外部キープロパティが検出されると、Code First 外部キーの null 値の許容に基づいて、リレーションシップの多重度が推測されます。</span><span class="sxs-lookup"><span data-stu-id="658fe-133">When a foreign key property is detected, Code First infers the multiplicity of the relationship based on the nullability of the foreign key.</span></span> <span data-ttu-id="658fe-134">プロパティが null 値を許容する場合、リレーションシップはオプションとして登録されます。それ以外の場合、リレーションシップは必須として登録されます。</span><span class="sxs-lookup"><span data-stu-id="658fe-134">If the property is nullable then the relationship is registered as optional; otherwise the relationship is registered as required.</span></span>  

<span data-ttu-id="658fe-135">依存エンティティの外部キーが null 値を許容しない場合、Code First はリレーションシップに対して連鎖削除を設定します。</span><span class="sxs-lookup"><span data-stu-id="658fe-135">If a foreign key on the dependent entity is not nullable, then Code First sets cascade delete on the relationship.</span></span> <span data-ttu-id="658fe-136">依存エンティティの外部キーが null 許容である場合、Code First はリレーションシップに対して連鎖削除を設定しません。また、プリンシパルが削除されると、外部キーは null に設定されます。</span><span class="sxs-lookup"><span data-stu-id="658fe-136">If a foreign key on the dependent entity is nullable, Code First does not set cascade delete on the relationship, and when the principal is deleted the foreign key will be set to null.</span></span> <span data-ttu-id="658fe-137">規則によって検出された多重度と連鎖削除の動作は、fluent API を使用してオーバーライドできます。</span><span class="sxs-lookup"><span data-stu-id="658fe-137">The multiplicity and cascade delete behavior detected by convention can be overridden by using the fluent API.</span></span>  

<span data-ttu-id="658fe-138">次の例では、ナビゲーションプロパティと外部キーを使用して、Department クラスと Course クラス間のリレーションシップを定義しています。</span><span class="sxs-lookup"><span data-stu-id="658fe-138">In the following example the navigation properties and a foreign key are used to define the relationship between the Department and Course classes.</span></span>  

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
> <span data-ttu-id="658fe-139">同じ型の間に複数のリレーションシップがある場合 (たとえば **、person クラス**に**ReviewedBooks**および**authoredbooks**ナビゲーションプロパティが含まれていて、 **book** **クラスに** **作成者**と**レビュー担当者**のナビゲーションプロパティが含まれている場合)、データ注釈または fluent API を使用してリレーションシップを手動で構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="658fe-139">If you have multiple relationships between the same types (for example, suppose you define the **Person** and **Book** classes, where the **Person** class contains the **ReviewedBooks** and **AuthoredBooks** navigation properties and the **Book** class contains the **Author** and **Reviewer** navigation properties) you need to manually configure the relationships by using Data Annotations or the fluent API.</span></span> <span data-ttu-id="658fe-140">詳細については、「[データ注釈-リレーションシップ](~/ef6/modeling/code-first/data-annotations.md)」と「 [Fluent API-リレーションシップ](~/ef6/modeling/code-first/fluent/relationships.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="658fe-140">For more information, see [Data Annotations - Relationships](~/ef6/modeling/code-first/data-annotations.md) and [Fluent API - Relationships](~/ef6/modeling/code-first/fluent/relationships.md).</span></span>  

## <a name="complex-types-convention"></a><span data-ttu-id="658fe-141">複合型の規則</span><span class="sxs-lookup"><span data-stu-id="658fe-141">Complex Types Convention</span></span>  

<span data-ttu-id="658fe-142">主キーを推論できず、データ注釈または fluent API によって登録される主キーがないクラス定義を Code First が検出すると、その型は複合型として自動的に登録されます。</span><span class="sxs-lookup"><span data-stu-id="658fe-142">When Code First discovers a class definition where a primary key cannot be inferred, and no primary key is registered through data annotations or the fluent API, then the type is automatically registered as a complex type.</span></span> <span data-ttu-id="658fe-143">複合型の検出では、型がエンティティ型を参照するプロパティを持たず、別の型のコレクションプロパティから参照されていないことも必要です。</span><span class="sxs-lookup"><span data-stu-id="658fe-143">Complex type detection also requires that the type does not have properties that reference entity types and is not referenced from a collection property on another type.</span></span> <span data-ttu-id="658fe-144">次のクラス定義を指定すると Code First 主キーがないため、**詳細**が複合型であると推論されます。</span><span class="sxs-lookup"><span data-stu-id="658fe-144">Given the following class definitions Code First would infer that **Details** is a complex type because it has no primary key.</span></span>  

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

## <a name="connection-string-convention"></a><span data-ttu-id="658fe-145">接続文字列の表記規則</span><span class="sxs-lookup"><span data-stu-id="658fe-145">Connection String Convention</span></span>  

<span data-ttu-id="658fe-146">使用する接続を検出するために DbContext が使用する規則については、「[接続とモデル](~/ef6/fundamentals/configuring/connection-strings.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="658fe-146">To learn about the conventions that DbContext uses to discover the connection to use see [Connections and Models](~/ef6/fundamentals/configuring/connection-strings.md).</span></span>  

## <a name="removing-conventions"></a><span data-ttu-id="658fe-147">規則の削除</span><span class="sxs-lookup"><span data-stu-id="658fe-147">Removing Conventions</span></span>  

<span data-ttu-id="658fe-148">System.string 名前空間で定義されている任意の規則を削除できます。</span><span class="sxs-lookup"><span data-stu-id="658fe-148">You can remove any of the conventions defined in the System.Data.Entity.ModelConfiguration.Conventions namespace.</span></span> <span data-ttu-id="658fe-149">次の例では、 **PluralizingTableNameConvention**を削除します。</span><span class="sxs-lookup"><span data-stu-id="658fe-149">The following example removes **PluralizingTableNameConvention**.</span></span>  

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

## <a name="custom-conventions"></a><span data-ttu-id="658fe-150">カスタム規約</span><span class="sxs-lookup"><span data-stu-id="658fe-150">Custom Conventions</span></span>  

<span data-ttu-id="658fe-151">カスタム規則は EF6 以降でサポートされています。</span><span class="sxs-lookup"><span data-stu-id="658fe-151">Custom conventions are supported in EF6 onwards.</span></span> <span data-ttu-id="658fe-152">詳細については、「[カスタム Code First 規則](~/ef6/modeling/code-first/conventions/custom.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="658fe-152">For more information see [Custom Code First Conventions](~/ef6/modeling/code-first/conventions/custom.md).</span></span>
