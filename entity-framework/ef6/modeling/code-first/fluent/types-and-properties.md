---
title: Fluent API 構成およびプロパティと型のマッピングで EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 648ed274-c501-4630-88e0-d728ab5c4057
ms.openlocfilehash: 7371cc99142ccf8fc6bea237d7d58d1e67fcecec
ms.sourcegitcommit: 75f8a179ac9a70ad390fc7ab2a6c5e714e701b8b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/27/2018
ms.locfileid: "52339804"
---
# <a name="fluent-api---configuring-and-mapping-properties-and-types"></a>Fluent API を構成して、プロパティと型のマッピング
Entity Framework Code First を使用する場合、既定の動作は、POCO クラスを EF に組み込まれた規則のセットを使用してテーブルにマップするのには。 場合によっては、ただし、することはできません、またはこれらの規則に従うし、規則ではどのような以外にエンティティをマップする必要がありますしたくないです。  

Namely の表記規則以外のものを使用する EF を構成する 2 つの主な方法はあります[注釈](~/ef6/modeling/code-first/data-annotations.md)または EFs fluent API。 注釈では、注釈の使用を実現できないマッピング シナリオがあるため、fluent API の機能のサブセットがのみについて説明します。 この記事では、fluent API を使用してプロパティを構成する方法を示すために設計されています。  

Code first fluent API がオーバーライドすることによってアクセスが最もよく、 [OnModelCreating](https://msdn.microsoft.com/library/system.data.entity.dbcontext.onmodelcreating.aspx)メソッドを派生[DbContext](https://msdn.microsoft.com/library/system.data.entity.dbcontext.aspx)します。 次のサンプルは fluent api を使用したさまざまなタスクを実行し、すると、コードをコピーしとで使用できるモデルを表示する場合、モデルに合わせてカスタマイズする方法について説明するように設計-がこの記事の最後に提供されます。  

## <a name="model-wide-settings"></a>モデル全体の設定  

### <a name="default-schema-ef6-onwards"></a>既定のスキーマ (EF6 以降)  

EF6 で始まることができます HasDefaultSchema メソッドを使用 DbModelBuilder のすべてのテーブル、ストアド プロシージャなどを使用するのにデータベース スキーマを指定します。この既定の設定は、別のスキーマを明示的に構成するすべてのオブジェクトのオーバーライドされます。  

``` csharp
modelBuilder.HasDefaultSchema("sales");
```  

### <a name="custom-conventions-ef6-onwards"></a>カスタムの規則 (EF6 以降)  

EF6 Code First に入っているものを補完する、独自の規則を作成することで開始しています。 詳細については、[カスタム コードの最初の規則](~/ef6/modeling/code-first/conventions/custom.md)を参照してください。  

## <a name="property-mapping"></a>プロパティのマッピング  

[プロパティ](https://msdn.microsoft.com/library/system.data.entity.infrastructure.dbentityentry.property.aspx)メソッドは、エンティティまたは複合型に属している各プロパティの属性の構成に使用されます。 プロパティ メソッドを使用して、特定のプロパティの構成オブジェクトを取得できます。 構成オブジェクトのオプションが構成されている種類に固有IsUnicode はなどの文字列プロパティでのみ使用できます。  

### <a name="configuring-a-primary-key"></a>主キーを構成します。  

主キーの Entity Framework の規則は次のとおりです。  

1. クラスは、名前が"ID"または"Id"プロパティを定義します。  
2. または、クラス名の後に"ID"または"Id"  

主キーのプロパティを明示的に設定するには、HasKey メソッドを使用することができます。 次の例では、HasKey メソッドを使用して、OfficeAssignment で InstructorID 主キーを構成します。  

``` csharp
modelBuilder.Entity<OfficeAssignment>().HasKey(t => t.InstructorID);
```  

### <a name="configuring-a-composite-primary-key"></a>複合主キーを構成します。  

次の例では、部門の型の複合主キー プロパティ DepartmentID と Name プロパティを構成します。  

``` csharp
modelBuilder.Entity<Department>().HasKey(t => new { t.DepartmentID, t.Name });
```  

### <a name="switching-off-identity-for-numeric-primary-keys"></a>数値の主キーの Id をオフの切り替え  

次の例を示す値をデータベースによって生成されませんが System.ComponentModel.DataAnnotations.DatabaseGeneratedOption.None に DepartmentID プロパティを設定します。  

``` csharp
modelBuilder.Entity<Department>().Property(t => t.DepartmentID)
    .HasDatabaseGeneratedOption(DatabaseGeneratedOption.None);
```  

### <a name="specifying-the-maximum-length-on-a-property"></a>プロパティの最大長を指定します。  

次の例では、Name プロパティは 50 文字以内にあります。 50 文字より長い値を行った場合、 [DbEntityValidationException](https://msdn.microsoft.com/library/system.data.entity.validation.dbentityvalidationexception.aspx)例外。 Code First にこのモデルからデータベースを作成する場合 50 文字に、[名前] 列の最大長も設定されます。  

``` csharp
modelBuilder.Entity<Department>().Property(t => t.Name).HasMaxLength(50);
```  

### <a name="configuring-the-property-to-be-required"></a>必要になるプロパティを構成します。  

次の例では、Name プロパティが必要です。 名前を指定しない場合は、DbEntityValidationException 例外が表示されます。 このモデルからデータベースを作成します Code First 場合は、このプロパティの格納に使用される列が null 非許容に通常は。  

> [!NOTE]
> 場合によっては、プロパティが必要な場合でも、null 非許容されるデータベース内の列の可能なないられます。 たとえば、TPH 継承の戦略のデータを複数の種類を使用してが格納されている場合、1 つのテーブル。 派生型に必要なプロパティが含まれている場合、列にできない null 非許容のこのプロパティは、階層内のすべての型であるためです。  

``` csharp
modelBuilder.Entity<Department>().Property(t => t.Name).IsRequired();
```  

### <a name="configuring-an-index-on-one-or-more-properties"></a>1 つまたは複数のプロパティのインデックスを構成します。  

> [!NOTE]
> **EF6.1 以降のみ**-Entity Framework 6.1 で、インデックスの属性が導入されました。 以前のバージョンを使用している場合、このセクションの情報は適用されません。  

加えることが、Fluent API でネイティブでサポートされていないインデックスの作成用のサポートを使用して、 **IndexAttribute** Fluent API を使用して。 インデックスの属性は、パイプラインの後半で、データベース内のインデックスに変換し、モデルのモデル注釈を含めることによって処理されます。 Fluent API を使用して注釈を追加これらと同じ手動でできます。  

これを行う最も簡単な方法は、のインスタンスを作成する**IndexAttribute**新しいインデックスのすべての設定を格納しています。 インスタンスを作成し、 **IndexAnnotation**は EF 特定の型に変換されますが、 **IndexAttribute** EF モデルに格納できるモデル注釈を設定します。 これらに渡すことができます、 **HasColumnAnnotation**メソッド名を指定する、Fluent API を**インデックス**の注釈。  

``` csharp
modelBuilder
    .Entity<Department>()
    .Property(t => t.Name)
    .HasColumnAnnotation("Index", new IndexAnnotation(new IndexAttribute()));
```  

使用できる設定の完全な一覧については**IndexAttribute**を参照してください、*インデックス*の[Code First のデータ注釈](~/ef6/modeling/code-first/data-annotations.md)します。 これには、インデックス名をカスタマイズする、一意のインデックスの作成、および複数列インデックスの作成が含まれます。  

配列を渡すことによって、1 つのプロパティで複数のインデックス注釈を指定できます**IndexAttribute**のコンストラクターに**IndexAnnotation**します。  

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

### <a name="specifying-not-to-map-a-clr-property-to-a-column-in-the-database"></a>CLR プロパティをデータベース内の列にマップしないように指定  

次の例では、CLR 型のプロパティが、データベース内の列にマップされていないことを指定する方法を示します。  

``` csharp
modelBuilder.Entity<Department>().Ignore(t => t.Budget);
```  

### <a name="mapping-a-clr-property-to-a-specific-column-in-the-database"></a>CLR プロパティをデータベースの特定の列にマップします。  

次の例では、DepartmentName データベース列に名前の CLR プロパティがマップされます。  

``` csharp
modelBuilder.Entity<Department>()
    .Property(t => t.Name)
    .HasColumnName("DepartmentName");
```  

### <a name="renaming-a-foreign-key-that-is-not-defined-in-the-model"></a>モデルで定義されていない外部キーの名前を変更します。  

CLR 型での外部キーの定義が、データベースが必要な名前を指定しないように選択する場合は、次の操作を行います。  

``` csharp
modelBuilder.Entity<Course>()
    .HasRequired(c => c.Department)
    .WithMany(t => t.Courses)
    .Map(m => m.MapKey("ChangedDepartmentID"));
```  

### <a name="configuring-whether-a-string-property-supports-unicode-content"></a>文字列プロパティは、Unicode コンテンツをサポートしているかどうかを構成します。  

既定では、文字列は Unicode (SQL Server での nvarchar) されます。 IsUnicode メソッドを使用して、varchar 型の文字列があることを指定することができます。  

``` csharp
modelBuilder.Entity<Department>()
    .Property(t => t.Name)
    .IsUnicode(false);
```  

### <a name="configuring-the-data-type-of-a-database-column"></a>データベース列のデータ型を構成します。  

[HasColumnType](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.configuration.stringcolumnconfiguration.hascolumntype.aspx)メソッドは、同じ基本型のさまざまな表現へのマッピングを使用できます。 このメソッドを使用しての場合、実行時に、データの変換を実行できるようにはありません。 データベースに依存しない IsUnicode varchar 列の設定の推奨される方法は、することに注意してください。  

``` csharp
modelBuilder.Entity<Department>()   
    .Property(p => p.Name)   
    .HasColumnType("varchar");
```  

### <a name="configuring-properties-on-a-complex-type"></a>複合型のプロパティの構成  

複合型のスカラー プロパティを構成する 2 つの方法はあります。  

ComplexTypeConfiguration でプロパティを呼び出すことができます。  

``` csharp
modelBuilder.ComplexType<Details>()
    .Property(t => t.Location)
    .HasMaxLength(20);
```  

複合型のプロパティにアクセスするのに、ドット表記を使用することもできます。  

``` csharp
modelBuilder.Entity<OnsiteCourse>()
    .Property(t => t.Details.Location)
    .HasMaxLength(20);
```  

### <a name="configuring-a-property-to-be-used-as-an-optimistic-concurrency-token"></a>オプティミスティック同時実行トークンとして使用するプロパティを構成します。  

エンティティのプロパティが同時実行トークンを表すことを指定するには、ConcurrencyCheck 属性または IsConcurrencyToken メソッドのいずれかを使用できます。  

``` csharp
modelBuilder.Entity<OfficeAssignment>()
    .Property(t => t.Timestamp)
    .IsConcurrencyToken();
```  

データベース内の行バージョンであるプロパティを構成するのに IsRowVersion メソッドを使用することもできます。 オプティミスティック同時実行制御トークンは、行バージョンが自動的に構成するプロパティを設定します。  

``` csharp
modelBuilder.Entity<OfficeAssignment>()
    .Property(t => t.Timestamp)
    .IsRowVersion();
```  

## <a name="type-mapping"></a>型のマッピング  

### <a name="specifying-that-a-class-is-a-complex-type"></a>クラスが複合型を指定します。  

慣例により、指定したプライマリ キーを持たない型は複合型として扱われます。 一部のシナリオで Code First は検出されません複合型 (たとえば場合、ID という名前のプロパティがする主キーにするのには、必ずしも) があります。 このような場合、型が複合型であるかを明示的に指定する fluent API を使用するとします。  

``` csharp
modelBuilder.ComplexType<Details>();
```  

### <a name="specifying-not-to-map-a-clr-entity-type-to-a-table-in-the-database"></a>CLR のエンティティ型をデータベース内のテーブルにマップしないように指定  

次の例では、データベース内のテーブルにマップされているから CLR 型を除外する方法を示します。  

``` csharp
modelBuilder.Ignore<OnlineCourse>();
```  

### <a name="mapping-an-entity-type-to-a-specific-table-in-the-database"></a>データベース内の特定のテーブルにエンティティ型のマッピング  

部門のすべてのプロパティは、t _ 部門をという名前のテーブル内の列にマップされます。  

``` csharp
modelBuilder.Entity<Department>()  
    .ToTable("t_Department");
```  

このようなスキーマ名を指定することもできます。  

``` csharp
modelBuilder.Entity<Department>()  
    .ToTable("t_Department", "school");
```  

### <a name="mapping-the-table-per-hierarchy-tph-inheritance"></a>Table-per-hierarchy (TPH) 継承のマッピング  

TPH マッピング シナリオでは、継承階層内のすべての型が 1 つのテーブルにマップされます。 識別子列を使用して、各行の種類を識別します。 Code First によるモデルを作成するときに、継承階層に参加している型の既定の戦略は TPH です。 既定では、「識別子」という名前のテーブルに識別子列が追加して、階層内の各型の CLR 型名は識別子の値を使用します。 既定の動作を変更するには、fluent API を使用します。  

``` csharp
modelBuilder.Entity<Course>()  
    .Map<Course>(m => m.Requires("Type").HasValue("Course"))  
    .Map<OnsiteCourse>(m => m.Requires("Type").HasValue("OnsiteCourse"));
```  

### <a name="mapping-the-table-per-type-tpt-inheritance"></a>テーブルあたり型 (TPT) 継承のマッピング  

TPT マッピング シナリオでは、すべての種類は個々 のテーブルにマップされます。 基本データ型または派生型のみに属するプロパティは、その型にマップされたテーブルに格納されます。 派生型にマップされるテーブルでは、派生テーブル、ベース テーブルと結合する外部キーも格納されます。  

``` csharp
modelBuilder.Entity<Course>().ToTable("Course");  
modelBuilder.Entity<OnsiteCourse>().ToTable("OnsiteCourse");
```  

### <a name="mapping-the-table-per-concrete-class-tpc-inheritance"></a>テーブル-コンクリートあたり Class (TPC) 継承のマッピング  

TPC マッピング シナリオでは、階層内のすべての非抽象型は個々 のテーブルにマップされます。 派生クラスにマップされるテーブル、データベース内の基本クラスにマップされるテーブルに関係のあるありません。 継承されたプロパティを含むクラスのすべてのプロパティは、対応するテーブルの列にマップされます。  

それぞれの派生型を構成する MapInheritedProperties メソッドを呼び出します。 MapInheritedProperties は、テーブルには、派生クラスの新しい列に、基本クラスから継承されたすべてのプロパティを再マップします。  

> [!NOTE]
> TPC 継承階層に参加しているテーブルを共有しないため、主キーがありますされる重複したエンティティ キーの同じ identity シードがデータベースで生成された値がある場合は、サブクラスにマップされているテーブルに挿入するときに注意してください。 この問題を解決するには、各テーブルの別の最初のシード値を指定するか、主キー プロパティに、id が無効です。 Identity は、Code First を使用する場合の整数キー プロパティの既定値です。  

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

### <a name="mapping-properties-of-an-entity-type-to-multiple-tables-in-the-database-entity-splitting"></a>エンティティ型のプロパティを (エンティティ分割) データベースの複数のテーブルにマップします。  

エンティティ分割には、複数のテーブルに分散し、エンティティ型のプロパティことができます。 次の例では、Department エンティティが 2 つのテーブルに分割されます。 部門と DepartmentDetails します。 エンティティ分割プロパティのサブセットを特定のテーブルにマップするのにマップのメソッドに複数の呼び出しを使用します。  

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

### <a name="mapping-multiple-entity-types-to-one-table-in-the-database-table-splitting"></a>データベース (テーブル分割) の 1 つのテーブルに複数のエンティティ型のマッピング  

次の例では、1 つのテーブルに主キーを共有する 2 つのエンティティ型をマップします。  

``` csharp
modelBuilder.Entity<OfficeAssignment>()
    .HasKey(t => t.InstructorID);

modelBuilder.Entity<Instructor>()
    .HasRequired(t => t.OfficeAssignment)
    .WithRequiredPrincipal(t => t.Instructor);

modelBuilder.Entity<Instructor>().ToTable("Instructor");

modelBuilder.Entity<OfficeAssignment>().ToTable("Instructor");
```  

### <a name="mapping-an-entity-type-to-insertupdatedelete-stored-procedures-ef6-onwards"></a>ストアド プロシージャの挿入/更新/削除 (EF6 以降) へのエンティティ型のマッピング  

EF6 で始まる挿入更新にストアド プロシージャを使用し、削除するエンティティにマップできます。 詳細についてを参照してください、[コード最初挿入/更新/削除のストアド プロシージャ](~/ef6/modeling/code-first/fluent/cud-stored-procedures.md)します。  

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
