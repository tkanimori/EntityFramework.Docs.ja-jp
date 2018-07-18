---
title: Fluent API - リレーションシップ - EF6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: fd73b4f8-16d5-40f1-9640-885ceafe67a1
caps.latest.revision: 3
ms.openlocfilehash: 7437b395fbed07dcb8c93cd8418317611e14a60b
ms.sourcegitcommit: f05e7b62584cf228f17390bb086a61d505712e1b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2018
ms.locfileid: "39122259"
---
# <a name="fluent-api---relationships"></a><span data-ttu-id="d6259-102">Fluent API - リレーションシップ</span><span class="sxs-lookup"><span data-stu-id="d6259-102">Fluent API - Relationships</span></span>
> [!NOTE]
> <span data-ttu-id="d6259-103">このページは、Code First fluent API を使用してモデル内のリレーションシップの設定に関する情報を提供します。</span><span class="sxs-lookup"><span data-stu-id="d6259-103">This page provides information about setting up relationships in your Code First model using the fluent API.</span></span> <span data-ttu-id="d6259-104">EF とアクセスし、リレーションシップを使用してデータを操作する方法に関係の詳細については、次を参照してください。[リレーションシップとナビゲーション プロパティ](~/ef6/fundamentals/relationships.md)します。</span><span class="sxs-lookup"><span data-stu-id="d6259-104">For general information about relationships in EF and how to access and manipulate data using relationships, see [Relationships & Navigation Properties](~/ef6/fundamentals/relationships.md).</span></span>  

<span data-ttu-id="d6259-105">Code First を使用する場合は、ドメインの CLR クラスを定義することで、モデルを定義します。</span><span class="sxs-lookup"><span data-stu-id="d6259-105">When working with Code First, you define your model by defining your domain CLR classes.</span></span> <span data-ttu-id="d6259-106">既定では、Entity Framework は、クラスをデータベース スキーマにマップするのに Code First の規則を使用します。</span><span class="sxs-lookup"><span data-stu-id="d6259-106">By default, Entity Framework uses the Code First conventions to map your classes to the database schema.</span></span> <span data-ttu-id="d6259-107">ほとんどの場合、外部キーと、クラスを定義するナビゲーション プロパティに基づいて、テーブル間のリレーションシップを設定するコードの最初に依存することができますコードの最初の名前付け規則を使用する場合。</span><span class="sxs-lookup"><span data-stu-id="d6259-107">If you use the Code First naming conventions, in most cases you can rely on Code First to set up relationships between your tables based on the foreign keys and navigation properties that you define on the classes.</span></span> <span data-ttu-id="d6259-108">クラスを定義するときに、規則に従わない、または規則の動作方法を変更する場合、fluent API や data annotations を使用するには Code First は、テーブル間のリレーションシップをマップできるように、クラスを構成します。</span><span class="sxs-lookup"><span data-stu-id="d6259-108">If you do not follow the conventions when defining your classes, or if you want to change the way the conventions work, you can use the fluent API or data annotations to configure your classes so Code First can map the relationships between your tables.</span></span>  

## <a name="introduction"></a><span data-ttu-id="d6259-109">はじめに</span><span class="sxs-lookup"><span data-stu-id="d6259-109">Introduction</span></span>  

<span data-ttu-id="d6259-110">Fluent API を使用したリレーションシップを構成するときに、EntityTypeConfiguration インスタンスを開始し、HasRequired、HasOptional、または多くのメソッドを使用して、このエンティティが参加するリレーションシップの種類を指定します。</span><span class="sxs-lookup"><span data-stu-id="d6259-110">When configuring a relationship with the fluent API, you start with the EntityTypeConfiguration instance and then use the HasRequired, HasOptional, or HasMany method to specify the type of relationship this entity participates in.</span></span> <span data-ttu-id="d6259-111">HasRequired と HasOptional のメソッドは、参照ナビゲーション プロパティを表すラムダ式を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="d6259-111">The HasRequired and HasOptional methods take a lambda expression that represents a reference navigation property.</span></span> <span data-ttu-id="d6259-112">HasMany メソッドは、コレクション ナビゲーション プロパティを表すラムダ式を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="d6259-112">The HasMany method takes a lambda expression that represents a collection navigation property.</span></span> <span data-ttu-id="d6259-113">WithRequired、ジオメトリマネジャ、および WithMany メソッドを使用して、逆のナビゲーション プロパティを設定できます。</span><span class="sxs-lookup"><span data-stu-id="d6259-113">You can then configure an inverse navigation property by using the WithRequired, WithOptional, and WithMany methods.</span></span> <span data-ttu-id="d6259-114">これらのメソッドでは、引数を受け取らないし、一方向のナビゲーションでカーディナリティを指定するために使用するオーバー ロードがあります。</span><span class="sxs-lookup"><span data-stu-id="d6259-114">These methods have overloads that do not take arguments and can be used to specify cardinality with unidirectional navigations.</span></span>  

<span data-ttu-id="d6259-115">外部キー プロパティは、HasForeignKey メソッドを使用して構成できます。</span><span class="sxs-lookup"><span data-stu-id="d6259-115">You can then configure foreign key properties by using the HasForeignKey method.</span></span> <span data-ttu-id="d6259-116">このメソッドは、外部キーとして使用するプロパティを表すラムダ式を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="d6259-116">This method takes a lambda expression that represents the property to be used as the foreign key.</span></span>  

## <a name="configuring-a-required-to-optional-relationship-one-tozero-or-one"></a><span data-ttu-id="d6259-117">必須-を-省略可能なリレーションシップ (1-0-または-1) を構成します。</span><span class="sxs-lookup"><span data-stu-id="d6259-117">Configuring a Required-to-Optional Relationship (One-to–Zero-or-One)</span></span>  

<span data-ttu-id="d6259-118">次の例では、0 または 1 に 1 つのリレーションシップを構成します。</span><span class="sxs-lookup"><span data-stu-id="d6259-118">The following example configures a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="d6259-119">OfficeAssignment では、プロパティの名前が HasKey メソッドは、主キーを構成するために使用する規則に従っていないためには、プライマリ キーと外部キー、InstructorID プロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="d6259-119">The OfficeAssignment has the InstructorID property that is a primary key and a foreign key, because the name of the property does not follow the convention the HasKey method is used to configure the primary key.</span></span>  

``` csharp
// Configure the primary key for the OfficeAssignment
modelBuilder.Entity<OfficeAssignment>()
    .HasKey(t => t.InstructorID);

// Map one-to-zero or one relationship
modelBuilder.Entity<OfficeAssignment>()
    .HasRequired(t => t.Instructor)
    .WithOptional(t => t.OfficeAssignment);
```  

## <a name="configuring-a-relationship-where-both-ends-are-required-one-to-one"></a><span data-ttu-id="d6259-120">両方の End は (1 対 1) 必須リレーションシップを構成します。</span><span class="sxs-lookup"><span data-stu-id="d6259-120">Configuring a Relationship Where Both Ends Are Required (One-to-One)</span></span>  

<span data-ttu-id="d6259-121">ほとんどの場合、依存している種類と、リレーションシップのプリンシパルには、Entity Framework は推測できます。</span><span class="sxs-lookup"><span data-stu-id="d6259-121">In most cases Entity Framework can infer which type is the dependent and which is the principal in a relationship.</span></span> <span data-ttu-id="d6259-122">ただし、両方が、リレーションシップの end が必要なまたは両方の側は省略可能な Entity Framework とは、依存とプリンシパルに識別できません。</span><span class="sxs-lookup"><span data-stu-id="d6259-122">However, when both ends of the relationship are required or both sides are optional Entity Framework cannot identify the dependent and principal.</span></span> <span data-ttu-id="d6259-123">リレーションシップの両端が必要なときに、HasRequired メソッドの後、WithRequiredPrincipal または WithRequiredDependent を使用します。</span><span class="sxs-lookup"><span data-stu-id="d6259-123">When both ends of the relationship are required, use WithRequiredPrincipal or WithRequiredDependent after the HasRequired method.</span></span> <span data-ttu-id="d6259-124">リレーションシップの両端が省略可能な場合は、WithOptionalPrincipal または WithOptionalDependent を HasOptional メソッドの後に使用します。</span><span class="sxs-lookup"><span data-stu-id="d6259-124">When both ends of the relationship are optional, use WithOptionalPrincipal or WithOptionalDependent after the HasOptional method.</span></span>  

``` csharp
// Configure the primary key for the OfficeAssignment
modelBuilder.Entity<OfficeAssignment>()
    .HasKey(t => t.InstructorID);

modelBuilder.Entity<Instructor>()
    .HasRequired(t => t.OfficeAssignment)
    .WithRequiredPrincipal(t => t.Instructor);
```  

## <a name="configuring-a-many-to-many-relationship"></a><span data-ttu-id="d6259-125">多対多リレーションシップを構成します。</span><span class="sxs-lookup"><span data-stu-id="d6259-125">Configuring a Many-to-Many Relationship</span></span>  

<span data-ttu-id="d6259-126">次のコードは、コースと Instructor 型の間の多対多リレーションシップを構成します。</span><span class="sxs-lookup"><span data-stu-id="d6259-126">The following code configures a many-to-many relationship between the Course and Instructor types.</span></span> <span data-ttu-id="d6259-127">次の例では、既定の Code First 規約は、結合テーブルの作成に使用されます。</span><span class="sxs-lookup"><span data-stu-id="d6259-127">In the following example, the default Code First conventions are used to create a join table.</span></span> <span data-ttu-id="d6259-128">その結果、CourseInstructor テーブルが Course_CourseID および Instructor_InstructorID 列で作成されます。</span><span class="sxs-lookup"><span data-stu-id="d6259-128">As a result the CourseInstructor table is created with Course_CourseID and Instructor_InstructorID columns.</span></span>  

``` csharp
modelBuilder.Entity<Course>()
    .HasMany(t => t.Instructors)
    .WithMany(t => t.Courses)
```  

<span data-ttu-id="d6259-129">テーブルの結合テーブルの名前と列の名前を指定する場合は、マップのメソッドを使用して追加の構成を行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="d6259-129">If you want to specify the join table name and the names of the columns in the table you need to do additional configuration by using the Map method.</span></span> <span data-ttu-id="d6259-130">次のコードでは、CourseID と InstructorID 列を含む CourseInstructor テーブルを生成します。</span><span class="sxs-lookup"><span data-stu-id="d6259-130">The following code generates the CourseInstructor table with CourseID and InstructorID columns.</span></span>  

``` csharp
modelBuilder.Entity<Course>()
    .HasMany(t => t.Instructors)
    .WithMany(t => t.Courses)
    .Map(m =>
    {
        m.ToTable("CourseInstructor");
        m.MapLeftKey("CourseID");
        m.MapRightKey("InstructorID");
    });
```  

## <a name="configuring-a-relationship-with-one-navigation-property"></a><span data-ttu-id="d6259-131">1 つのナビゲーション プロパティを持つリレーションシップを構成します。</span><span class="sxs-lookup"><span data-stu-id="d6259-131">Configuring a Relationship with One Navigation Property</span></span>  

<span data-ttu-id="d6259-132">一方向の (という単一方向も) リレーションシップがリレーションシップの end の 1 つのみで、両方ではなくナビゲーション プロパティを定義する場合。</span><span class="sxs-lookup"><span data-stu-id="d6259-132">A one-directional (also called unidirectional) relationship is when a navigation property is defined on only one of the relationship ends and not on both.</span></span> <span data-ttu-id="d6259-133">慣例により、Code First は常に解釈として一対多、一方向の関係。</span><span class="sxs-lookup"><span data-stu-id="d6259-133">By convention, Code First always interprets a unidirectional relationship as one-to-many.</span></span> <span data-ttu-id="d6259-134">たとえば、Instructor と OfficeAssignment、場所にあるナビゲーション プロパティは Instructor 型のみの間の一対一のリレーションシップの場合は、fluent API を使用してこの関係を構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d6259-134">For example, if you want a one-to-one relationship between Instructor and OfficeAssignment, where you have a navigation property on only the Instructor type, you need to use the fluent API to configure this relationship.</span></span>  

``` csharp
// Configure the primary Key for the OfficeAssignment
modelBuilder.Entity<OfficeAssignment>()
    .HasKey(t => t.InstructorID);

modelBuilder.Entity<Instructor>()
    .HasRequired(t => t.OfficeAssignment)
    .WithRequiredPrincipal();
```  

## <a name="enabling-cascade-delete"></a><span data-ttu-id="d6259-135">連鎖削除を有効にします。</span><span class="sxs-lookup"><span data-stu-id="d6259-135">Enabling Cascade Delete</span></span>  

<span data-ttu-id="d6259-136">WillCascadeOnDelete メソッドを使用して、リレーションシップで連鎖削除を構成できます。</span><span class="sxs-lookup"><span data-stu-id="d6259-136">You can configure cascade delete on a relationship by using the WillCascadeOnDelete method.</span></span> <span data-ttu-id="d6259-137">場合は、依存エンティティの外部キーが許容されませんし、Code First 連鎖削除の設定の関係します。</span><span class="sxs-lookup"><span data-stu-id="d6259-137">If a foreign key on the dependent entity is not nullable, then Code First sets cascade delete on the relationship.</span></span> <span data-ttu-id="d6259-138">依存エンティティの外部キー値が許容、Code First は連鎖削除、リレーションシップに、プリンシパルが削除されたときに、外部キーが設定されます場合、を null にします。</span><span class="sxs-lookup"><span data-stu-id="d6259-138">If a foreign key on the dependent entity is nullable, Code First does not set cascade delete on the relationship, and when the principal is deleted the foreign key will be set to null.</span></span>  

<span data-ttu-id="d6259-139">使用してこれらの連鎖削除規則を削除することができます。</span><span class="sxs-lookup"><span data-stu-id="d6259-139">You can remove these cascade delete conventions by using:</span></span>  

<span data-ttu-id="d6259-140">modelBuilder.Conventions.Remove\<OneToManyCascadeDeleteConvention\>)</span><span class="sxs-lookup"><span data-stu-id="d6259-140">modelBuilder.Conventions.Remove\<OneToManyCascadeDeleteConvention\>()</span></span>  
<span data-ttu-id="d6259-141">modelBuilder.Conventions.Remove\<ManyToManyCascadeDeleteConvention\>)</span><span class="sxs-lookup"><span data-stu-id="d6259-141">modelBuilder.Conventions.Remove\<ManyToManyCascadeDeleteConvention\>()</span></span>  

<span data-ttu-id="d6259-142">次のコードでは、必要になるリレーションシップを構成し、連鎖削除を無効にします。</span><span class="sxs-lookup"><span data-stu-id="d6259-142">The following code configures the relationship to be required and then disables cascade delete.</span></span>  

``` csharp
modelBuilder.Entity<Course>()
    .HasRequired(t => t.Department)
    .WithMany(t => t.Courses)
    .HasForeignKey(d => d.DepartmentID)
    .WillCascadeOnDelete(false);
```  

## <a name="configuring-a-composite-foreign-key"></a><span data-ttu-id="d6259-143">複合外部キーの構成</span><span class="sxs-lookup"><span data-stu-id="d6259-143">Configuring a Composite Foreign Key</span></span>  

<span data-ttu-id="d6259-144">部門型の主キーが DepartmentID と名前のプロパティ、構成されている場合の部門とコース タイプの外部キーの主キーをよう構成は。</span><span class="sxs-lookup"><span data-stu-id="d6259-144">If the primary key on the Department type consisted of DepartmentID and Name properties, you would configure the primary key for the Department and the foreign key on the Course types as follows:</span></span>  

``` csharp
// Composite primary key
modelBuilder.Entity<Department>()
.HasKey(d => new { d.DepartmentID, d.Name });

// Composite foreign key
modelBuilder.Entity<Course>()  
    .HasRequired(c => c.Department)  
    .WithMany(d => d.Courses)
    .HasForeignKey(d => new { d.DepartmentID, d.DepartmentName });
```  

## <a name="renaming-a-foreign-key-that-is-not-defined-in-the-model"></a><span data-ttu-id="d6259-145">モデルで定義されていない外部キーの名前を変更します。</span><span class="sxs-lookup"><span data-stu-id="d6259-145">Renaming a Foreign Key That Is Not Defined in the Model</span></span>  

<span data-ttu-id="d6259-146">を、CLR 型での外部キーの定義が、データベースが必要な名前を指定しないように選択する場合は、次の操作を行います。</span><span class="sxs-lookup"><span data-stu-id="d6259-146">If you choose not to define a foreign key on the CLR type, but want to specify what name it should have in the database, do the following:</span></span>  

``` csharp
modelBuilder.Entity<Course>()
    .HasRequired(c => c.Department)
    .WithMany(t => t.Courses)
    .Map(m => m.MapKey("ChangedDepartmentID"));
```  

## <a name="configuring-a-foreign-key-name-that-does-not-follow-the-code-first-convention"></a><span data-ttu-id="d6259-147">コードの最初の規則に従っていない外部キー名を構成します。</span><span class="sxs-lookup"><span data-stu-id="d6259-147">Configuring a Foreign Key Name That Does Not Follow the Code First Convention</span></span>  

<span data-ttu-id="d6259-148">コースのクラスの外部キー プロパティが SomeDepartmentID DepartmentID ではなく、呼び出された場合は、SomeDepartmentID 外部キーにすることを指定するには、次を実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d6259-148">If the foreign key property on the Course class was called SomeDepartmentID instead of DepartmentID, you would need to do the following to specify that you want SomeDepartmentID to be the foreign key:</span></span>  

``` csharp
modelBuilder.Entity<Course>()
         .HasRequired(c => c.Department)
         .WithMany(d => d.Courses)
         .HasForeignKey(c => c.SomeDepartmentID);
```  

## <a name="model-used-in-samples"></a><span data-ttu-id="d6259-149">サンプルで使用されるモデル</span><span class="sxs-lookup"><span data-stu-id="d6259-149">Model Used in Samples</span></span>  

<span data-ttu-id="d6259-150">このページのサンプルについては、次のコードの最初のモデルが使用されます。</span><span class="sxs-lookup"><span data-stu-id="d6259-150">The following Code First model is used for the samples on this page.</span></span>  

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
