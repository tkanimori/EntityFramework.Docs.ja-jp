---
title: Fluent API-リレーションシップ-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: fd73b4f8-16d5-40f1-9640-885ceafe67a1
ms.openlocfilehash: 05f282c02699f8bf3c71197ac5e01000f1855917
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78415761"
---
# <a name="fluent-api---relationships"></a><span data-ttu-id="ca501-102">Fluent API-リレーションシップ</span><span class="sxs-lookup"><span data-stu-id="ca501-102">Fluent API - Relationships</span></span>
> [!NOTE]
> <span data-ttu-id="ca501-103">このページでは、fluent API を使用して Code First モデル内のリレーションシップを設定する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="ca501-103">This page provides information about setting up relationships in your Code First model using the fluent API.</span></span> <span data-ttu-id="ca501-104">EF のリレーションシップに関する一般的な情報と、リレーションシップを使用してデータにアクセスして操作する方法については、「[リレーションシップ & ナビゲーションプロパティ](~/ef6/fundamentals/relationships.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="ca501-104">For general information about relationships in EF and how to access and manipulate data using relationships, see [Relationships & Navigation Properties](~/ef6/fundamentals/relationships.md).</span></span>  

<span data-ttu-id="ca501-105">Code First を使用する場合は、ドメイン CLR クラスを定義してモデルを定義します。</span><span class="sxs-lookup"><span data-stu-id="ca501-105">When working with Code First, you define your model by defining your domain CLR classes.</span></span> <span data-ttu-id="ca501-106">既定では、Entity Framework は Code First 規約を使用して、クラスをデータベーススキーマにマップします。</span><span class="sxs-lookup"><span data-stu-id="ca501-106">By default, Entity Framework uses the Code First conventions to map your classes to the database schema.</span></span> <span data-ttu-id="ca501-107">Code First 名前付け規則を使用する場合、ほとんどの場合、Code First に依存して、クラスで定義する外部キーとナビゲーションプロパティに基づいてテーブル間のリレーションシップを設定できます。</span><span class="sxs-lookup"><span data-stu-id="ca501-107">If you use the Code First naming conventions, in most cases you can rely on Code First to set up relationships between your tables based on the foreign keys and navigation properties that you define on the classes.</span></span> <span data-ttu-id="ca501-108">クラスの定義時に規則に従っていない場合、または規則の動作を変更する場合は、fluent API またはデータ注釈を使用してクラスを構成し、Code First がテーブル間のリレーションシップをマップできるようにすることができます。</span><span class="sxs-lookup"><span data-stu-id="ca501-108">If you do not follow the conventions when defining your classes, or if you want to change the way the conventions work, you can use the fluent API or data annotations to configure your classes so Code First can map the relationships between your tables.</span></span>  

## <a name="introduction"></a><span data-ttu-id="ca501-109">はじめに</span><span class="sxs-lookup"><span data-stu-id="ca501-109">Introduction</span></span>  

<span data-ttu-id="ca501-110">Fluent API とのリレーションシップを構成する場合は、まず EntityTypeConfiguration インスタンスを作成し、次に HasRequired、Hasrequired、または Hasrequired メソッドを使用して、このエンティティが参加するリレーションシップの種類を指定します。</span><span class="sxs-lookup"><span data-stu-id="ca501-110">When configuring a relationship with the fluent API, you start with the EntityTypeConfiguration instance and then use the HasRequired, HasOptional, or HasMany method to specify the type of relationship this entity participates in.</span></span> <span data-ttu-id="ca501-111">HasRequired メソッドと Hasrequired メソッドは、参照ナビゲーションプロパティを表すラムダ式を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="ca501-111">The HasRequired and HasOptional methods take a lambda expression that represents a reference navigation property.</span></span> <span data-ttu-id="ca501-112">HasMany メソッドは、コレクションナビゲーションプロパティを表すラムダ式を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="ca501-112">The HasMany method takes a lambda expression that represents a collection navigation property.</span></span> <span data-ttu-id="ca501-113">次に、WithRequired、Withrequired、および Withrequired メソッドを使用して、逆ナビゲーションプロパティを構成できます。</span><span class="sxs-lookup"><span data-stu-id="ca501-113">You can then configure an inverse navigation property by using the WithRequired, WithOptional, and WithMany methods.</span></span> <span data-ttu-id="ca501-114">これらのメソッドには、引数を受け取らないオーバーロードがあり、一方向のナビゲーションでカーディナリティを指定するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="ca501-114">These methods have overloads that do not take arguments and can be used to specify cardinality with unidirectional navigations.</span></span>  

<span data-ttu-id="ca501-115">その後、HasForeignKey メソッドを使用して、外部キープロパティを構成できます。</span><span class="sxs-lookup"><span data-stu-id="ca501-115">You can then configure foreign key properties by using the HasForeignKey method.</span></span> <span data-ttu-id="ca501-116">このメソッドは、外部キーとして使用されるプロパティを表すラムダ式を受け取ります。</span><span class="sxs-lookup"><span data-stu-id="ca501-116">This method takes a lambda expression that represents the property to be used as the foreign key.</span></span>  

## <a name="configuring-a-required-to-optional-relationship-one-tozero-or-one"></a><span data-ttu-id="ca501-117">必須からオプションのリレーションシップを構成する (1 対0または1つ)</span><span class="sxs-lookup"><span data-stu-id="ca501-117">Configuring a Required-to-Optional Relationship (One-to–Zero-or-One)</span></span>  

<span data-ttu-id="ca501-118">次の例では、一対ゼロまたは一対一のリレーションシップを構成します。</span><span class="sxs-lookup"><span data-stu-id="ca501-118">The following example configures a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="ca501-119">OfficeAssignment には、主キーと外部キーの InstructorID プロパティがあります。これは、プロパティの名前が規則に従っていないためです。これは、プライマリキーの構成に HasKey メソッドが使用されるためです。</span><span class="sxs-lookup"><span data-stu-id="ca501-119">The OfficeAssignment has the InstructorID property that is a primary key and a foreign key, because the name of the property does not follow the convention the HasKey method is used to configure the primary key.</span></span>  

``` csharp
// Configure the primary key for the OfficeAssignment
modelBuilder.Entity<OfficeAssignment>()
    .HasKey(t => t.InstructorID);

// Map one-to-zero or one relationship
modelBuilder.Entity<OfficeAssignment>()
    .HasRequired(t => t.Instructor)
    .WithOptional(t => t.OfficeAssignment);
```  

## <a name="configuring-a-relationship-where-both-ends-are-required-one-to-one"></a><span data-ttu-id="ca501-120">両端が必要なリレーションシップを構成する (1 対 1)</span><span class="sxs-lookup"><span data-stu-id="ca501-120">Configuring a Relationship Where Both Ends Are Required (One-to-One)</span></span>  

<span data-ttu-id="ca501-121">ほとんどの場合 Entity Framework は、依存している型と、リレーションシップのプリンシパルである型を推論できます。</span><span class="sxs-lookup"><span data-stu-id="ca501-121">In most cases Entity Framework can infer which type is the dependent and which is the principal in a relationship.</span></span> <span data-ttu-id="ca501-122">ただし、リレーションシップの両端が必要である場合、または両方の側が省略可能な場合 Entity Framework は依存関係とプリンシパルを識別できません。</span><span class="sxs-lookup"><span data-stu-id="ca501-122">However, when both ends of the relationship are required or both sides are optional Entity Framework cannot identify the dependent and principal.</span></span> <span data-ttu-id="ca501-123">リレーションシップの両方の end が必要な場合は、HasRequired メソッドの後に WithRequiredPrincipal または Withrequiredprincipal を使用します。</span><span class="sxs-lookup"><span data-stu-id="ca501-123">When both ends of the relationship are required, use WithRequiredPrincipal or WithRequiredDependent after the HasRequired method.</span></span> <span data-ttu-id="ca501-124">リレーションシップの両方の end が省略可能な場合は、HasOptional メソッドの後に、Withoptional Principal または Withoptional を使用します。</span><span class="sxs-lookup"><span data-stu-id="ca501-124">When both ends of the relationship are optional, use WithOptionalPrincipal or WithOptionalDependent after the HasOptional method.</span></span>  

``` csharp
// Configure the primary key for the OfficeAssignment
modelBuilder.Entity<OfficeAssignment>()
    .HasKey(t => t.InstructorID);

modelBuilder.Entity<Instructor>()
    .HasRequired(t => t.OfficeAssignment)
    .WithRequiredPrincipal(t => t.Instructor);
```  

## <a name="configuring-a-many-to-many-relationship"></a><span data-ttu-id="ca501-125">多対多リレーションシップの構成</span><span class="sxs-lookup"><span data-stu-id="ca501-125">Configuring a Many-to-Many Relationship</span></span>  

<span data-ttu-id="ca501-126">次のコードでは、コースとインストラクターの型の間に多対多のリレーションシップを構成しています。</span><span class="sxs-lookup"><span data-stu-id="ca501-126">The following code configures a many-to-many relationship between the Course and Instructor types.</span></span> <span data-ttu-id="ca501-127">次の例では、既定の Code First 規約を使用して、結合テーブルを作成します。</span><span class="sxs-lookup"><span data-stu-id="ca501-127">In the following example, the default Code First conventions are used to create a join table.</span></span> <span data-ttu-id="ca501-128">その結果、Course_CourseID と Instructor_InstructorID 列を含む CourseInstructor テーブルが作成されます。</span><span class="sxs-lookup"><span data-stu-id="ca501-128">As a result the CourseInstructor table is created with Course_CourseID and Instructor_InstructorID columns.</span></span>  

``` csharp
modelBuilder.Entity<Course>()
    .HasMany(t => t.Instructors)
    .WithMany(t => t.Courses)
```  

<span data-ttu-id="ca501-129">テーブル内の結合テーブル名と列名を指定する場合は、Map メソッドを使用して追加の構成を行う必要があります。</span><span class="sxs-lookup"><span data-stu-id="ca501-129">If you want to specify the join table name and the names of the columns in the table you need to do additional configuration by using the Map method.</span></span> <span data-ttu-id="ca501-130">次のコードでは、CourseID 列と InstructorID 列を含む CourseInstructor テーブルが生成されます。</span><span class="sxs-lookup"><span data-stu-id="ca501-130">The following code generates the CourseInstructor table with CourseID and InstructorID columns.</span></span>  

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

## <a name="configuring-a-relationship-with-one-navigation-property"></a><span data-ttu-id="ca501-131">1つのナビゲーションプロパティを使用したリレーションシップの構成</span><span class="sxs-lookup"><span data-stu-id="ca501-131">Configuring a Relationship with One Navigation Property</span></span>  

<span data-ttu-id="ca501-132">一方向 (一方向とも呼ばれます) のリレーションシップは、ナビゲーションプロパティがいずれかのリレーションシップの両端でのみ定義されていて、両方ではない場合です。</span><span class="sxs-lookup"><span data-stu-id="ca501-132">A one-directional (also called unidirectional) relationship is when a navigation property is defined on only one of the relationship ends and not on both.</span></span> <span data-ttu-id="ca501-133">慣例により、Code First は常に一対多として一方向の関係を解釈します。</span><span class="sxs-lookup"><span data-stu-id="ca501-133">By convention, Code First always interprets a unidirectional relationship as one-to-many.</span></span> <span data-ttu-id="ca501-134">たとえば、インストラクターと OfficeAssignment の間に1対1のリレーションシップが必要な場合に、インストラクターの種類に対してのみナビゲーションプロパティがある場合は、fluent API を使用してこのリレーションシップを構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ca501-134">For example, if you want a one-to-one relationship between Instructor and OfficeAssignment, where you have a navigation property on only the Instructor type, you need to use the fluent API to configure this relationship.</span></span>  

``` csharp
// Configure the primary Key for the OfficeAssignment
modelBuilder.Entity<OfficeAssignment>()
    .HasKey(t => t.InstructorID);

modelBuilder.Entity<Instructor>()
    .HasRequired(t => t.OfficeAssignment)
    .WithRequiredPrincipal();
```  

## <a name="enabling-cascade-delete"></a><span data-ttu-id="ca501-135">連鎖削除の有効化</span><span class="sxs-lookup"><span data-stu-id="ca501-135">Enabling Cascade Delete</span></span>  

<span data-ttu-id="ca501-136">WillCascadeOnDelete メソッドを使用して、リレーションシップに対して連鎖削除を構成できます。</span><span class="sxs-lookup"><span data-stu-id="ca501-136">You can configure cascade delete on a relationship by using the WillCascadeOnDelete method.</span></span> <span data-ttu-id="ca501-137">依存エンティティの外部キーが null 値を許容しない場合、Code First はリレーションシップに対して連鎖削除を設定します。</span><span class="sxs-lookup"><span data-stu-id="ca501-137">If a foreign key on the dependent entity is not nullable, then Code First sets cascade delete on the relationship.</span></span> <span data-ttu-id="ca501-138">依存エンティティの外部キーが null 許容である場合、Code First はリレーションシップに対して連鎖削除を設定しません。また、プリンシパルが削除されると、外部キーは null に設定されます。</span><span class="sxs-lookup"><span data-stu-id="ca501-138">If a foreign key on the dependent entity is nullable, Code First does not set cascade delete on the relationship, and when the principal is deleted the foreign key will be set to null.</span></span>  

<span data-ttu-id="ca501-139">これらの連鎖削除規則は、次を使用して削除できます。</span><span class="sxs-lookup"><span data-stu-id="ca501-139">You can remove these cascade delete conventions by using:</span></span>  

<span data-ttu-id="ca501-140">modelBuilder. 規則\<OneToManyCascadeDeleteConvention\>() を削除します。</span><span class="sxs-lookup"><span data-stu-id="ca501-140">modelBuilder.Conventions.Remove\<OneToManyCascadeDeleteConvention\>()</span></span>  
<span data-ttu-id="ca501-141">modelBuilder. 規則\<ManyToManyCascadeDeleteConvention\>() を削除します。</span><span class="sxs-lookup"><span data-stu-id="ca501-141">modelBuilder.Conventions.Remove\<ManyToManyCascadeDeleteConvention\>()</span></span>  

<span data-ttu-id="ca501-142">次のコードでは、必要となるリレーションシップを構成してから、cascade delete を無効にしています。</span><span class="sxs-lookup"><span data-stu-id="ca501-142">The following code configures the relationship to be required and then disables cascade delete.</span></span>  

``` csharp
modelBuilder.Entity<Course>()
    .HasRequired(t => t.Department)
    .WithMany(t => t.Courses)
    .HasForeignKey(d => d.DepartmentID)
    .WillCascadeOnDelete(false);
```  

## <a name="configuring-a-composite-foreign-key"></a><span data-ttu-id="ca501-143">複合外部キーの構成</span><span class="sxs-lookup"><span data-stu-id="ca501-143">Configuring a Composite Foreign Key</span></span>  

<span data-ttu-id="ca501-144">Department 型の主キーが DepartmentID および Name プロパティで構成されている場合は、次のように、学科の主キーとコースの種類の外部キーを構成します。</span><span class="sxs-lookup"><span data-stu-id="ca501-144">If the primary key on the Department type consisted of DepartmentID and Name properties, you would configure the primary key for the Department and the foreign key on the Course types as follows:</span></span>  

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

## <a name="renaming-a-foreign-key-that-is-not-defined-in-the-model"></a><span data-ttu-id="ca501-145">モデルで定義されていない外部キーの名前変更</span><span class="sxs-lookup"><span data-stu-id="ca501-145">Renaming a Foreign Key That Is Not Defined in the Model</span></span>  

<span data-ttu-id="ca501-146">CLR 型で外部キーを定義しないことを選択したが、データベースに必要な名前を指定する場合は、次の手順を実行します。</span><span class="sxs-lookup"><span data-stu-id="ca501-146">If you choose not to define a foreign key on the CLR type, but want to specify what name it should have in the database, do the following:</span></span>  

``` csharp
modelBuilder.Entity<Course>()
    .HasRequired(c => c.Department)
    .WithMany(t => t.Courses)
    .Map(m => m.MapKey("ChangedDepartmentID"));
```  

## <a name="configuring-a-foreign-key-name-that-does-not-follow-the-code-first-convention"></a><span data-ttu-id="ca501-147">Code First 規約に従っていない外部キー名の構成</span><span class="sxs-lookup"><span data-stu-id="ca501-147">Configuring a Foreign Key Name That Does Not Follow the Code First Convention</span></span>  

<span data-ttu-id="ca501-148">Course クラスの外部キープロパティが DepartmentID ではなく SomeDepartmentID と呼ばれていた場合は、次の手順を実行して、SomeDepartmentID を外部キーにするように指定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ca501-148">If the foreign key property on the Course class was called SomeDepartmentID instead of DepartmentID, you would need to do the following to specify that you want SomeDepartmentID to be the foreign key:</span></span>  

``` csharp
modelBuilder.Entity<Course>()
         .HasRequired(c => c.Department)
         .WithMany(d => d.Courses)
         .HasForeignKey(c => c.SomeDepartmentID);
```  

## <a name="model-used-in-samples"></a><span data-ttu-id="ca501-149">サンプルで使用されるモデル</span><span class="sxs-lookup"><span data-stu-id="ca501-149">Model Used in Samples</span></span>  

<span data-ttu-id="ca501-150">このページのサンプルには、次の Code First モデルが使用されています。</span><span class="sxs-lookup"><span data-stu-id="ca501-150">The following Code First model is used for the samples on this page.</span></span>  

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
