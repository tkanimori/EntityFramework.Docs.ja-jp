---
title: Fluent API-リレーションシップ-EF6
description: Fluent API-Entity Framework 6 のリレーションシップ
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/code-first/fluent/relationships
ms.openlocfilehash: 7c62dfd788afda41f4c940836152b9114aa545e1
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065161"
---
# <a name="fluent-api---relationships"></a>Fluent API-リレーションシップ
> [!NOTE]
> このページでは、fluent API を使用して Code First モデル内のリレーションシップを設定する方法について説明します。 EF のリレーションシップに関する一般的な情報と、リレーションシップを使用してデータにアクセスして操作する方法については、「 [リレーションシップ & ナビゲーションプロパティ](xref:ef6/fundamentals/relationships)」を参照してください。  

Code First を使用する場合は、ドメイン CLR クラスを定義してモデルを定義します。 既定では、Entity Framework は Code First 規約を使用して、クラスをデータベーススキーマにマップします。 Code First 名前付け規則を使用する場合、ほとんどの場合、Code First に依存して、クラスで定義する外部キーとナビゲーションプロパティに基づいてテーブル間のリレーションシップを設定できます。 クラスの定義時に規則に従っていない場合、または規則の動作を変更する場合は、fluent API またはデータ注釈を使用してクラスを構成し、Code First がテーブル間のリレーションシップをマップできるようにすることができます。  

## <a name="introduction"></a>はじめに  

Fluent API とのリレーションシップを構成する場合は、まず EntityTypeConfiguration インスタンスを作成し、次に HasRequired、Hasrequired、または Hasrequired メソッドを使用して、このエンティティが参加するリレーションシップの種類を指定します。 HasRequired メソッドと Hasrequired メソッドは、参照ナビゲーションプロパティを表すラムダ式を受け取ります。 HasMany メソッドは、コレクションナビゲーションプロパティを表すラムダ式を受け取ります。 次に、WithRequired、Withrequired、および Withrequired メソッドを使用して、逆ナビゲーションプロパティを構成できます。 これらのメソッドには、引数を受け取らないオーバーロードがあり、一方向のナビゲーションでカーディナリティを指定するために使用できます。  

その後、HasForeignKey メソッドを使用して、外部キープロパティを構成できます。 このメソッドは、外部キーとして使用されるプロパティを表すラムダ式を受け取ります。  

## <a name="configuring-a-required-to-optional-relationship-one-tozero-or-one"></a>必須からオプションのリレーションシップを構成する (1 対0または1つ)  

次の例では、一対ゼロまたは一対一のリレーションシップを構成します。 OfficeAssignment には、主キーと外部キーの InstructorID プロパティがあります。これは、プロパティの名前が規則に従っていないためです。これは、プライマリキーの構成に HasKey メソッドが使用されるためです。  

``` csharp
// Configure the primary key for the OfficeAssignment
modelBuilder.Entity<OfficeAssignment>()
    .HasKey(t => t.InstructorID);

// Map one-to-zero or one relationship
modelBuilder.Entity<OfficeAssignment>()
    .HasRequired(t => t.Instructor)
    .WithOptional(t => t.OfficeAssignment);
```  

## <a name="configuring-a-relationship-where-both-ends-are-required-one-to-one"></a>両端が必要なリレーションシップを構成する (1 対 1)  

ほとんどの場合 Entity Framework は、依存している型と、リレーションシップのプリンシパルである型を推論できます。 ただし、リレーションシップの両端が必要である場合、または両方の側が省略可能な場合 Entity Framework は依存関係とプリンシパルを識別できません。 リレーションシップの両方の end が必要な場合は、HasRequired メソッドの後に WithRequiredPrincipal または Withrequiredprincipal を使用します。 リレーションシップの両方の end が省略可能な場合は、HasOptional メソッドの後に、Withoptional Principal または Withoptional を使用します。  

``` csharp
// Configure the primary key for the OfficeAssignment
modelBuilder.Entity<OfficeAssignment>()
    .HasKey(t => t.InstructorID);

modelBuilder.Entity<Instructor>()
    .HasRequired(t => t.OfficeAssignment)
    .WithRequiredPrincipal(t => t.Instructor);
```  

## <a name="configuring-a-many-to-many-relationship"></a>多対多リレーションシップの構成  

次のコードでは、コースとインストラクターの型の間に多対多のリレーションシップを構成しています。 次の例では、既定の Code First 規約を使用して、結合テーブルを作成します。 その結果、Course_CourseID と Instructor_InstructorID 列を含む CourseInstructor テーブルが作成されます。  

``` csharp
modelBuilder.Entity<Course>()
    .HasMany(t => t.Instructors)
    .WithMany(t => t.Courses)
```  

テーブル内の結合テーブル名と列名を指定する場合は、Map メソッドを使用して追加の構成を行う必要があります。 次のコードでは、CourseID 列と InstructorID 列を含む CourseInstructor テーブルが生成されます。  

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

## <a name="configuring-a-relationship-with-one-navigation-property"></a>1つのナビゲーションプロパティを使用したリレーションシップの構成  

一方向 (一方向とも呼ばれます) のリレーションシップは、ナビゲーションプロパティがいずれかのリレーションシップの両端でのみ定義されていて、両方ではない場合です。 慣例により、Code First は常に一対多として一方向の関係を解釈します。 たとえば、インストラクターと OfficeAssignment の間に1対1のリレーションシップが必要な場合に、インストラクターの種類に対してのみナビゲーションプロパティがある場合は、fluent API を使用してこのリレーションシップを構成する必要があります。  

``` csharp
// Configure the primary Key for the OfficeAssignment
modelBuilder.Entity<OfficeAssignment>()
    .HasKey(t => t.InstructorID);

modelBuilder.Entity<Instructor>()
    .HasRequired(t => t.OfficeAssignment)
    .WithRequiredPrincipal();
```  

## <a name="enabling-cascade-delete"></a>連鎖削除の有効化  

WillCascadeOnDelete メソッドを使用して、リレーションシップに対して連鎖削除を構成できます。 依存エンティティの外部キーが null 値を許容しない場合、Code First はリレーションシップに対して連鎖削除を設定します。 依存エンティティの外部キーが null 許容である場合、Code First はリレーションシップに対して連鎖削除を設定しません。また、プリンシパルが削除されると、外部キーは null に設定されます。  

これらの連鎖削除規則は、次を使用して削除できます。  

modelBuilder. 規則。削除 \<OneToManyCascadeDeleteConvention\> ()  
modelBuilder. 規則。削除 \<ManyToManyCascadeDeleteConvention\> ()  

次のコードでは、必要となるリレーションシップを構成してから、cascade delete を無効にしています。  

``` csharp
modelBuilder.Entity<Course>()
    .HasRequired(t => t.Department)
    .WithMany(t => t.Courses)
    .HasForeignKey(d => d.DepartmentID)
    .WillCascadeOnDelete(false);
```  

## <a name="configuring-a-composite-foreign-key"></a>複合外部キーの構成  

Department 型の主キーが DepartmentID および Name プロパティで構成されている場合は、次のように、学科の主キーとコースの種類の外部キーを構成します。  

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

## <a name="renaming-a-foreign-key-that-is-not-defined-in-the-model"></a>モデルで定義されていない外部キーの名前変更  

CLR 型で外部キーを定義しないことを選択したが、データベースに必要な名前を指定する場合は、次の手順を実行します。  

``` csharp
modelBuilder.Entity<Course>()
    .HasRequired(c => c.Department)
    .WithMany(t => t.Courses)
    .Map(m => m.MapKey("ChangedDepartmentID"));
```  

## <a name="configuring-a-foreign-key-name-that-does-not-follow-the-code-first-convention"></a>Code First 規約に従っていない外部キー名の構成  

Course クラスの外部キープロパティが DepartmentID ではなく SomeDepartmentID と呼ばれていた場合は、次の手順を実行して、SomeDepartmentID を外部キーにするように指定する必要があります。  

``` csharp
modelBuilder.Entity<Course>()
         .HasRequired(c => c.Department)
         .WithMany(d => d.Courses)
         .HasForeignKey(c => c.SomeDepartmentID);
```  

## <a name="model-used-in-samples"></a>サンプルで使用されるモデル  

このページのサンプルには、次の Code First モデルが使用されています。  

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
