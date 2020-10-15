---
title: Fluent API-プロパティと型の構成とマッピング-EF6
description: Fluent API-Entity Framework 6 でのプロパティと型の構成とマッピング
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/code-first/fluent/types-and-properties
ms.openlocfilehash: 821672bcb797314c96189443ace7f875a79c8582
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065148"
---
# <a name="fluent-api---configuring-and-mapping-properties-and-types"></a>Fluent API-プロパティと型の構成とマッピング
Entity Framework を使用する場合 Code First 既定の動作では、EF に組み込まれている一連の規則を使用して、POCO クラスをテーブルにマップします。 ただし、これらの規則に従うことができない場合や、規則に従っていないエンティティにエンティティをマップする必要がある場合があります。  

規則以外のものを使用するように EF を構成するには、次の2つの主な方法があります。つまり、 [注釈](xref:ef6/modeling/code-first/data-annotations) または EFs fluent API です。 注釈には fluent API の機能のサブセットのみが含まれているので、注釈を使用して実現できないマッピングシナリオがあります。 この記事は、fluent API を使用してプロパティを構成する方法を示すことを目的としています。  

Code first fluent API は、派生した[Dbcontext](https://msdn.microsoft.com/library/system.data.entity.dbcontext.aspx)で[onmodelcreating](https://msdn.microsoft.com/library/system.data.entity.dbcontext.onmodelcreating.aspx)メソッドをオーバーライドすることによって最も一般的にアクセスされます。 次のサンプルは、fluent api を使用してさまざまなタスクを実行する方法を示しています。また、モデルに合わせてコードをコピーしてカスタマイズすることもできます。そのまま使用できるモデルを確認する場合は、この記事の最後に記載します。  

## <a name="model-wide-settings"></a>Model-Wide の設定  

### <a name="default-schema-ef6-onwards"></a>既定のスキーマ (EF6 以降)  

EF6 以降では、DbModelBuilder で HasDefaultSchema メソッドを使用して、すべてのテーブル、ストアドプロシージャなどに使用するデータベーススキーマを指定できます。この既定の設定は、別のスキーマを明示的に構成するすべてのオブジェクトに対してオーバーライドされます。  

``` csharp
modelBuilder.HasDefaultSchema("sales");
```  

### <a name="custom-conventions-ef6-onwards"></a>カスタム規則 (EF6 以降)  

EF6 以降では、独自の規則を作成して、Code First に含まれる規則を補うことができます。 詳細については、「 [カスタム Code First 規則](xref:ef6/modeling/code-first/conventions/custom)」を参照してください。  

## <a name="property-mapping"></a>プロパティ マッピング  

[プロパティ](https://msdn.microsoft.com/library/system.data.entity.infrastructure.dbentityentry.property.aspx)メソッドは、エンティティ型または複合型に属する各プロパティの属性を構成するために使用されます。 プロパティメソッドは、指定されたプロパティの構成オブジェクトを取得するために使用されます。 構成オブジェクトのオプションは、構成されている型に固有です。IsUnicode は、文字列プロパティでのみ使用できます。たとえば、のようになります。  

### <a name="configuring-a-primary-key"></a>主キーの構成  

主キーの Entity Framework 規則は次のとおりです。  

1. クラスは、名前が "ID" または "Id" であるプロパティを定義します。  
2. または、クラス名の後に "ID" または "Id" が続きます。  

プロパティを主キーとして明示的に設定するには、HasKey メソッドを使用します。 次の例では、HasKey メソッドを使用して、OfficeAssignment 型の InstructorID 主キーを構成しています。  

``` csharp
modelBuilder.Entity<OfficeAssignment>().HasKey(t => t.InstructorID);
```  

### <a name="configuring-a-composite-primary-key"></a>複合主キーの構成  

次の例では、DepartmentID プロパティと Name プロパティを、Department 型の複合主キーになるように構成します。  

``` csharp
modelBuilder.Entity<Department>().HasKey(t => new { t.DepartmentID, t.Name });
```  

### <a name="switching-off-identity-for-numeric-primary-keys"></a>数値主キーの Id をオフにする  

次の例では、DepartmentID プロパティを System.componentmodel に設定して、値がデータベースによって生成されないことを示します。  

``` csharp
modelBuilder.Entity<Department>().Property(t => t.DepartmentID)
    .HasDatabaseGeneratedOption(DatabaseGeneratedOption.None);
```  

### <a name="specifying-the-maximum-length-on-a-property"></a>プロパティの最大長の指定  

次の例では、Name プロパティは50文字以内である必要があります。 50文字を超える値を指定すると、 [Dbentityvalidationexception](https://msdn.microsoft.com/library/system.data.entity.validation.dbentityvalidationexception.aspx) 例外が発生します。 Code First がこのモデルからデータベースを作成する場合は、Name 列の最大長も50文字に設定します。  

``` csharp
modelBuilder.Entity<Department>().Property(t => t.Name).HasMaxLength(50);
```  

### <a name="configuring-the-property-to-be-required"></a>必須のプロパティを構成しています  

次の例では、Name プロパティが必要です。 名前を指定しない場合は、DbEntityValidationException 例外が発生します。 Code First がこのモデルからデータベースを作成する場合、通常、このプロパティを格納するために使用される列は null 非許容になります。  

> [!NOTE]
> 場合によっては、プロパティが必要な場合でも、データベース内の列を null 非許容にすることはできません。 たとえば、複数の型に対して TPH 継承戦略データを使用すると、1つのテーブルに格納されます。 派生型に必須のプロパティが含まれている場合、階層内のすべての型がこのプロパティを持つわけではないため、列を null 非許容にすることはできません。  

``` csharp
modelBuilder.Entity<Department>().Property(t => t.Name).IsRequired();
```  

### <a name="configuring-an-index-on-one-or-more-properties"></a>1つまたは複数のプロパティのインデックスの構成  

> [!NOTE]
> **Ef 6.1 以降のみ** -Index 属性が Entity Framework 6.1 で導入されました。 以前のバージョンを使用している場合、このセクションの情報は適用されません。  

インデックスの作成は、Fluent API ではネイティブでサポートされていませんが、Fluent API を使用して **Indexattribute** のサポートを利用できます。 インデックスの属性は、モデルの注釈をモデルに含めることによって処理され、その後、パイプラインの後の方でデータベースのインデックスに変換されます。 Fluent API を使用して、これらの同じ注釈を手動で追加できます。  

これを行う最も簡単な方法は、新しいインデックスのすべての設定を含む **Indexattribute** のインスタンスを作成することです。 その後、 **Indexannotation** のインスタンスを作成できます。このインスタンスは、ef モデルに格納できるモデル注釈に **indexannotation** 設定を変換する ef 固有の型です。 これらは、Fluent API の **Hascolumnannotation** メソッドに渡すことができ、注釈の名前の **インデックス** を指定します。  

``` csharp
modelBuilder
    .Entity<Department>()
    .Property(t => t.Name)
    .HasColumnAnnotation("Index", new IndexAnnotation(new IndexAttribute()));
```  

**Indexattribute**で使用できる設定の完全な一覧については、「 [Code First データ注釈](xref:ef6/modeling/code-first/data-annotations)」の「 *Index* 」セクションを参照してください。 これには、インデックス名のカスタマイズ、一意のインデックスの作成、複数列のインデックスの作成が含まれます。  

Indexattribute のコンストラクターに**Indexattribute**の配列を渡すことにより、1つのプロパティに対し**IndexAnnotation**て複数のインデックス注釈を指定できます。  

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

### <a name="specifying-not-to-map-a-clr-property-to-a-column-in-the-database"></a>CLR プロパティをデータベースの列にマップしないように指定する  

次の例は、CLR 型のプロパティがデータベースの列にマップされないように指定する方法を示しています。  

``` csharp
modelBuilder.Entity<Department>().Ignore(t => t.Budget);
```  

### <a name="mapping-a-clr-property-to-a-specific-column-in-the-database"></a>CLR プロパティをデータベース内の特定の列にマップする  

次の例では、CLR プロパティの名前を DepartmentName データベース列にマップします。  

``` csharp
modelBuilder.Entity<Department>()
    .Property(t => t.Name)
    .HasColumnName("DepartmentName");
```  

### <a name="renaming-a-foreign-key-that-is-not-defined-in-the-model"></a>モデルで定義されていない外部キーの名前変更  

CLR 型で外部キーを定義しない場合に、データベースに含める名前を指定する場合は、次の手順を実行します。  

``` csharp
modelBuilder.Entity<Course>()
    .HasRequired(c => c.Department)
    .WithMany(t => t.Courses)
    .Map(m => m.MapKey("ChangedDepartmentID"));
```  

### <a name="configuring-whether-a-string-property-supports-unicode-content"></a>文字列プロパティが Unicode コンテンツをサポートするかどうかの構成  

既定では、文字列は Unicode (SQL Server の nvarchar) です。 IsUnicode メソッドを使用して、文字列を varchar 型にする必要があることを指定できます。  

``` csharp
modelBuilder.Entity<Department>()
    .Property(t => t.Name)
    .IsUnicode(false);
```  

### <a name="configuring-the-data-type-of-a-database-column"></a>データベース列のデータ型の構成  

[HasColumnType](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.configuration.stringcolumnconfiguration.hascolumntype.aspx)メソッドを使用すると、同じ基本型の異なる表現へのマッピングが可能になります。 この方法を使用しても、実行時にデータの変換を実行することはできません。 列を varchar に設定する場合は、データベースに依存しないため、IsUnicode を使用することをお勧めします。  

``` csharp
modelBuilder.Entity<Department>()   
    .Property(p => p.Name)   
    .HasColumnType("varchar");
```  

### <a name="configuring-properties-on-a-complex-type"></a>複合型のプロパティの構成  

複合型のスカラープロパティを構成するには、2つの方法があります。  

ComplexTypeConfiguration プロパティを呼び出すことができます。  

``` csharp
modelBuilder.ComplexType<Details>()
    .Property(t => t.Location)
    .HasMaxLength(20);
```  

ドット表記を使用して、複合型のプロパティにアクセスすることもできます。  

``` csharp
modelBuilder.Entity<OnsiteCourse>()
    .Property(t => t.Details.Location)
    .HasMaxLength(20);
```  

### <a name="configuring-a-property-to-be-used-as-an-optimistic-concurrency-token"></a>オプティミスティック同時実行トークンとして使用するプロパティの構成  

エンティティのプロパティが同時実行トークンを表すように指定するには、ConcurrencyCheck 属性または IsConcurrencyToken メソッドを使用します。  

``` csharp
modelBuilder.Entity<OfficeAssignment>()
    .Property(t => t.Timestamp)
    .IsConcurrencyToken();
```  

また、IsRowVersion メソッドを使用して、データベースの行バージョンとしてプロパティを構成することもできます。 プロパティを行バージョンに設定すると、オプティミスティック同時実行トークンとして自動的に構成されます。  

``` csharp
modelBuilder.Entity<OfficeAssignment>()
    .Property(t => t.Timestamp)
    .IsRowVersion();
```  

## <a name="type-mapping"></a>型マッピング  

### <a name="specifying-that-a-class-is-a-complex-type"></a>クラスが複合型であることを指定する  

慣例により、主キーが指定されていない型は複合型として扱われます。 Code First が複合型を検出しないシナリオがいくつかあります (たとえば、ID というプロパティがあり、それが主キーであることを意味していない場合など)。 このような場合は、fluent API を使用して、型が複合型であることを明示的に指定します。  

``` csharp
modelBuilder.ComplexType<Details>();
```  

### <a name="specifying-not-to-map-a-clr-entity-type-to-a-table-in-the-database"></a>CLR エンティティ型をデータベース内のテーブルにマップしないように指定する  

次の例では、CLR 型を、データベース内のテーブルにマップされないものから除外する方法を示します。  

``` csharp
modelBuilder.Ignore<OnlineCourse>();
```  

### <a name="mapping-an-entity-type-to-a-specific-table-in-the-database"></a>データベース内の特定のテーブルへのエンティティ型のマッピング  

Department のすべてのプロパティは、t_ Department という名前のテーブルの列にマップされます。  

``` csharp
modelBuilder.Entity<Department>()  
    .ToTable("t_Department");
```  

次のように、スキーマ名を指定することもできます。  

``` csharp
modelBuilder.Entity<Department>()  
    .ToTable("t_Department", "school");
```  

### <a name="mapping-the-table-per-hierarchy-tph-inheritance"></a>階層ごとのテーブル (TPH) の継承のマッピング  

TPH マッピングシナリオでは、継承階層内のすべての型が1つのテーブルにマップされます。 識別子列は、各行の種類を識別するために使用されます。 Code First でモデルを作成する場合、TPH は、継承階層に参加する型の既定の方法です。 既定では、識別子の列が "識別子" という名前のテーブルに追加され、階層内の各型の CLR 型名が識別子の値に使用されます。 既定の動作を変更するには、fluent API を使用します。  

``` csharp
modelBuilder.Entity<Course>()  
    .Map<Course>(m => m.Requires("Type").HasValue("Course"))  
    .Map<OnsiteCourse>(m => m.Requires("Type").HasValue("OnsiteCourse"));
```  

### <a name="mapping-the-table-per-type-tpt-inheritance"></a>テーブル型 (TPT) の継承のマッピング  

TPT マッピングシナリオでは、すべての型が個々のテーブルにマップされます。 基本データ型または派生型のみに属するプロパティは、その型にマップされたテーブルに格納されます。 派生型にマップされるテーブルには、派生テーブルとベーステーブルを結合する外部キーも格納されます。  

``` csharp
modelBuilder.Entity<Course>().ToTable("Course");  
modelBuilder.Entity<OnsiteCourse>().ToTable("OnsiteCourse");
```  

### <a name="mapping-the-table-per-concrete-class-tpc-inheritance"></a>テーブル単位のクラス (TPC) の継承のマッピング  

TPC マッピングシナリオでは、階層内のすべての非抽象型が個々のテーブルにマップされます。 派生クラスにマップされるテーブルには、データベースの基本クラスにマップされるテーブルとのリレーションシップがありません。 継承されたプロパティを含む、クラスのすべてのプロパティは、対応するテーブルの列にマップされます。  

MapInheritedProperties メソッドを呼び出して、それぞれの派生型を構成します。 MapInheritedProperties は、基本クラスから継承されたすべてのプロパティを、派生クラスのテーブルの新しい列に再マップします。  

> [!NOTE]
> TPC 継承階層に参加しているテーブルでは主キーが共有されないため、同じ identity シードを使用してデータベースが生成された値がある場合は、サブクラスにマップされているテーブルに挿入すると、重複するエンティティキーが存在することに注意してください。 この問題を解決するには、各テーブルに対して異なる初期シード値を指定するか、プライマリキープロパティで id をオフにします。 Id は、Code First を操作する場合の整数キープロパティの既定値です。  

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

### <a name="mapping-properties-of-an-entity-type-to-multiple-tables-in-the-database-entity-splitting"></a>エンティティ型のプロパティをデータベース内の複数のテーブルにマップする (エンティティ分割)  

エンティティ分割を使用すると、エンティティ型のプロパティを複数のテーブルに分散させることができます。 次の例では、Department エンティティは Department と DepartmentDetails の2つのテーブルに分割されています。 エンティティ分割では、マップメソッドの複数の呼び出しを使用して、プロパティのサブセットを特定のテーブルにマップします。  

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

### <a name="mapping-multiple-entity-types-to-one-table-in-the-database-table-splitting"></a>データベース内の1つのテーブルへの複数のエンティティ型のマッピング (テーブル分割)  

次の例では、主キーを共有する2つのエンティティ型を1つのテーブルにマップします。  

``` csharp
modelBuilder.Entity<OfficeAssignment>()
    .HasKey(t => t.InstructorID);

modelBuilder.Entity<Instructor>()
    .HasRequired(t => t.OfficeAssignment)
    .WithRequiredPrincipal(t => t.Instructor);

modelBuilder.Entity<Instructor>().ToTable("Instructor");

modelBuilder.Entity<OfficeAssignment>().ToTable("Instructor");
```  

### <a name="mapping-an-entity-type-to-insertupdatedelete-stored-procedures-ef6-onwards"></a>ストアドプロシージャの挿入/更新/削除へのエンティティ型のマッピング (EF6 以降)  

EF6 以降では、insert update および delete のストアドプロシージャを使用するようにエンティティをマップできます。 詳細については、「 [ストアドプロシージャの挿入/更新/削除 Code First](xref:ef6/modeling/code-first/fluent/cud-stored-procedures)」を参照してください。  

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
