---
title: Fluent API - リレーションシップ - EF6
author: divega
ms.date: 2016-10-23
ms.assetid: fd73b4f8-16d5-40f1-9640-885ceafe67a1
ms.openlocfilehash: e13a1370f46362e0f2ca3743ec5b063ce6f87cbe
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42994763"
---
# <a name="fluent-api---relationships"></a>Fluent API - リレーションシップ
> [!NOTE]
> このページは、Code First fluent API を使用してモデル内のリレーションシップの設定に関する情報を提供します。 EF とアクセスし、リレーションシップを使用してデータを操作する方法に関係の詳細については、次を参照してください。[リレーションシップとナビゲーション プロパティ](~/ef6/fundamentals/relationships.md)します。  

Code First を使用する場合は、ドメインの CLR クラスを定義することで、モデルを定義します。 既定では、Entity Framework は、クラスをデータベース スキーマにマップするのに Code First の規則を使用します。 ほとんどの場合、外部キーと、クラスを定義するナビゲーション プロパティに基づいて、テーブル間のリレーションシップを設定するコードの最初に依存することができますコードの最初の名前付け規則を使用する場合。 クラスを定義するときに、規則に従わない、または規則の動作方法を変更する場合、fluent API や data annotations を使用するには Code First は、テーブル間のリレーションシップをマップできるように、クラスを構成します。  

## <a name="introduction"></a>はじめに  

Fluent API を使用したリレーションシップを構成するときに、EntityTypeConfiguration インスタンスを開始し、HasRequired、HasOptional、または多くのメソッドを使用して、このエンティティが参加するリレーションシップの種類を指定します。 HasRequired と HasOptional のメソッドは、参照ナビゲーション プロパティを表すラムダ式を受け取ります。 HasMany メソッドは、コレクション ナビゲーション プロパティを表すラムダ式を受け取ります。 WithRequired、ジオメトリマネジャ、および WithMany メソッドを使用して、逆のナビゲーション プロパティを設定できます。 これらのメソッドでは、引数を受け取らないし、一方向のナビゲーションでカーディナリティを指定するために使用するオーバー ロードがあります。  

外部キー プロパティは、HasForeignKey メソッドを使用して構成できます。 このメソッドは、外部キーとして使用するプロパティを表すラムダ式を受け取ります。  

## <a name="configuring-a-required-to-optional-relationship-one-tozero-or-one"></a>必須-を-省略可能なリレーションシップ (1-0-または-1) を構成します。  

次の例では、0 または 1 に 1 つのリレーションシップを構成します。 OfficeAssignment では、プロパティの名前が HasKey メソッドは、主キーを構成するために使用する規則に従っていないためには、プライマリ キーと外部キー、InstructorID プロパティがあります。  

``` csharp
// Configure the primary key for the OfficeAssignment
modelBuilder.Entity<OfficeAssignment>()
    .HasKey(t => t.InstructorID);

// Map one-to-zero or one relationship
modelBuilder.Entity<OfficeAssignment>()
    .HasRequired(t => t.Instructor)
    .WithOptional(t => t.OfficeAssignment);
```  

## <a name="configuring-a-relationship-where-both-ends-are-required-one-to-one"></a>両方の End は (1 対 1) 必須リレーションシップを構成します。  

ほとんどの場合、依存している種類と、リレーションシップのプリンシパルには、Entity Framework は推測できます。 ただし、両方が、リレーションシップの end が必要なまたは両方の側は省略可能な Entity Framework とは、依存とプリンシパルに識別できません。 リレーションシップの両端が必要なときに、HasRequired メソッドの後、WithRequiredPrincipal または WithRequiredDependent を使用します。 リレーションシップの両端が省略可能な場合は、WithOptionalPrincipal または WithOptionalDependent を HasOptional メソッドの後に使用します。  

``` csharp
// Configure the primary key for the OfficeAssignment
modelBuilder.Entity<OfficeAssignment>()
    .HasKey(t => t.InstructorID);

modelBuilder.Entity<Instructor>()
    .HasRequired(t => t.OfficeAssignment)
    .WithRequiredPrincipal(t => t.Instructor);
```  

## <a name="configuring-a-many-to-many-relationship"></a>多対多リレーションシップを構成します。  

次のコードは、コースと Instructor 型の間の多対多リレーションシップを構成します。 次の例では、既定の Code First 規約は、結合テーブルの作成に使用されます。 その結果、CourseInstructor テーブルが Course_CourseID および Instructor_InstructorID 列で作成されます。  

``` csharp
modelBuilder.Entity<Course>()
    .HasMany(t => t.Instructors)
    .WithMany(t => t.Courses)
```  

テーブルの結合テーブルの名前と列の名前を指定する場合は、マップのメソッドを使用して追加の構成を行う必要があります。 次のコードでは、CourseID と InstructorID 列を含む CourseInstructor テーブルを生成します。  

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

## <a name="configuring-a-relationship-with-one-navigation-property"></a>1 つのナビゲーション プロパティを持つリレーションシップを構成します。  

一方向の (という単一方向も) リレーションシップがリレーションシップの end の 1 つのみで、両方ではなくナビゲーション プロパティを定義する場合。 慣例により、Code First は常に解釈として一対多、一方向の関係。 たとえば、Instructor と OfficeAssignment、場所にあるナビゲーション プロパティは Instructor 型のみの間の一対一のリレーションシップの場合は、fluent API を使用してこの関係を構成する必要があります。  

``` csharp
// Configure the primary Key for the OfficeAssignment
modelBuilder.Entity<OfficeAssignment>()
    .HasKey(t => t.InstructorID);

modelBuilder.Entity<Instructor>()
    .HasRequired(t => t.OfficeAssignment)
    .WithRequiredPrincipal();
```  

## <a name="enabling-cascade-delete"></a>連鎖削除を有効にします。  

WillCascadeOnDelete メソッドを使用して、リレーションシップで連鎖削除を構成できます。 場合は、依存エンティティの外部キーが許容されませんし、Code First 連鎖削除の設定の関係します。 依存エンティティの外部キー値が許容、Code First は連鎖削除、リレーションシップに、プリンシパルが削除されたときに、外部キーが設定されます場合、を null にします。  

使用してこれらの連鎖削除規則を削除することができます。  

modelBuilder.Conventions.Remove\<OneToManyCascadeDeleteConvention\>)  
modelBuilder.Conventions.Remove\<ManyToManyCascadeDeleteConvention\>)  

次のコードでは、必要になるリレーションシップを構成し、連鎖削除を無効にします。  

``` csharp
modelBuilder.Entity<Course>()
    .HasRequired(t => t.Department)
    .WithMany(t => t.Courses)
    .HasForeignKey(d => d.DepartmentID)
    .WillCascadeOnDelete(false);
```  

## <a name="configuring-a-composite-foreign-key"></a>複合外部キーの構成  

部門型の主キーが DepartmentID と名前のプロパティ、構成されている場合の部門とコース タイプの外部キーの主キーをよう構成は。  

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

## <a name="renaming-a-foreign-key-that-is-not-defined-in-the-model"></a>モデルで定義されていない外部キーの名前を変更します。  

を、CLR 型での外部キーの定義が、データベースが必要な名前を指定しないように選択する場合は、次の操作を行います。  

``` csharp
modelBuilder.Entity<Course>()
    .HasRequired(c => c.Department)
    .WithMany(t => t.Courses)
    .Map(m => m.MapKey("ChangedDepartmentID"));
```  

## <a name="configuring-a-foreign-key-name-that-does-not-follow-the-code-first-convention"></a>コードの最初の規則に従っていない外部キー名を構成します。  

コースのクラスの外部キー プロパティが SomeDepartmentID DepartmentID ではなく、呼び出された場合は、SomeDepartmentID 外部キーにすることを指定するには、次を実行する必要があります。  

``` csharp
modelBuilder.Entity<Course>()
         .HasRequired(c => c.Department)
         .WithMany(d => d.Courses)
         .HasForeignKey(c => c.SomeDepartmentID);
```  

## <a name="model-used-in-samples"></a>サンプルで使用されるモデル  

このページのサンプルについては、次のコードの最初のモデルが使用されます。  

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
