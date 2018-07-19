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
# <a name="code-first-conventions"></a>コードの最初の規則
まず、コードを使用すると、c# または Visual Basic .NET のクラスを使用してモデルを記述できます。 規則を使用して、モデルの基本的な形状が検出されます。 規則は、Code First を使用する場合は、クラス定義に基づく概念モデルを自動的に構成するために使用するルールのセットです。 規則は、System.Data.Entity.ModelConfiguration.Conventions 名前空間で定義されます。  

さらに、データ注釈または fluent API を使用して、モデルを構成できます。 優先順位は、データ注釈と規則が続く、fluent API を使用した構成に付与されます。 詳細については、次を参照してください[データ注釈](~/ef6/modeling/code-first/data-annotations.md)、 [Fluent API - リレーションシップ](~/ef6/modeling/code-first/fluent/relationships.md)、 [Fluent API の種類とプロパティ](~/ef6/modeling/code-first/fluent/types-and-properties.md)と[VB.NETにFluentAPI](~/ef6/modeling/code-first/fluent/vb.md)。  

Code First 規約の詳細な一覧が表示されます、 [API ドキュメント](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx)します。 このトピックでは、Code First によって使用される規則の概要を示します。  

## <a name="type-discovery"></a>型の探索  

Code First の開発を使用する場合に、(ドメイン) の概念モデルを定義する .NET Framework クラスを記述することで、通常で開始します。 クラスを定義するだけでなくも設定する必要が**DbContext**モデルに追加する型も把握しています。 派生したコンテキスト クラスを定義するには、 **DbContext**公開**DbSet**モデルの一部に型のプロパティ。 コードでは、まずこれらの型が含まれますされ、参照先の型が別のアセンブリで定義されている場合でもが、参照先の種類でもプルされます。  

型は、継承階層に参加して、定義するのに十分な処理は、 **DbSet**基底クラスと同じアセンブリ内にある場合、基底クラスと派生型のプロパティは自動的に含まれるになります。  

次の例では、1 つしかない**DbSet**プロパティで定義されている、 **SchoolEntities**クラス (**部門**)。 コードは、まず探索し、参照先の種類でプルこのプロパティを使用します。  

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

モデルから型を除外する場合を使用して、 **NotMapped**属性または**DbModelBuilder.Ignore** fluent API。  

```  csharp
modelBuilder.Ignore<Department>();
```  

## <a name="primary-key-convention"></a>プライマリ キーの規則  

コードでは、プロパティは、(いない大文字小文字を区別)、"ID"という名前のクラスのプロパティはまたはクラス名の"ID"が続く場合、主キーを最初は推測します。 主キー プロパティの型が数値か、または GUID がある場合、id 列として構成します。  

``` csharp
public class Department
{
    // Primary key
    public int DepartmentID { get; set; }

    . . .  

}
```  

## <a name="relationship-convention"></a>リレーションシップの規則  

Entity Framework では、ナビゲーション プロパティは、2 つのエンティティ型間のリレーションシップを移動する方法を提供します。 各オブジェクトは、参加する各リレーションシップに対してナビゲーション プロパティを持つことができます。 ナビゲーション プロパティを使用するに移動し、参照オブジェクトを返す双方向のリレーションシップを管理できます (多重度がいずれかである場合または 0 または 1) または (多重度が多くの場合) のコレクション。 コードは、まず、型に定義されているナビゲーション プロパティに基づくリレーションシップを推測します。  

ナビゲーションのプロパティだけでなく、外部キー プロパティを依存オブジェクトを表す型を含めることをお勧めします。 任意のプロパティは、プリンシパルのプライマリ キー プロパティと同じデータ型と、次の形式のいずれかに続く名前は、リレーションシップの外部キーを表します: '\<ナビゲーション プロパティの名前\>\<プリンシパル主キー プロパティ名\>'、'\<プリンシパル クラス名\>\<主キー プロパティ名\>'、または '\<プリンシパルのプライマリ キー プロパティ名\>'。 複数の一致が見つかった場合は、上に示した順序で優先順位が指定します。 外部キーの検出は大文字小文字が区別されません。 外部キー プロパティが検出されると、Code First は推測外部キーの null 値許容属性に基づくリレーションシップの多重度。 プロパティが null 許容の場合、リレーションシップが省略可能です。 として登録します。それ以外の場合、リレーションシップが登録されている必須とします。  

場合は、依存エンティティの外部キーが許容されませんし、Code First 連鎖削除の設定の関係します。 依存エンティティの外部キー値が許容、Code First は連鎖削除、リレーションシップに、プリンシパルが削除されたときに、外部キーが設定されます場合、を null にします。 多重度とカスケード削除の動作がによって検出された規則は、fluent API を使用してオーバーライドできます。  

次の例では、部門とコースのクラス間のリレーションシップを定義するナビゲーション プロパティと外部キーが使用されます。  

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
> 同じ型の間で複数のリレーションシップがある場合 (たとえば、定義する、 **Person**と**帳**クラス、場所、 **Person**クラスには、が含まれています**ReviewedBooks**と**AuthoredBooks**ナビゲーション プロパティ、および**帳**クラスが含まれています、**作成者**と**レビュー担当者**ナビゲーション プロパティ) のデータ注釈または fluent API を使用して、リレーションシップを手動で構成する必要があります。 詳細については、次を参照してください。[データ注釈 - リレーションシップ](~/ef6/modeling/code-first/data-annotations.md)と[Fluent API - リレーションシップ](~/ef6/modeling/code-first/fluent/relationships.md)します。  

## <a name="complex-types-convention"></a>複合型の規則  

Code First が検出、クラス定義を主キーを推論することはできません、およびデータ注釈または fluent API で主キーが登録されていない型は自動的に複合型として登録します。 複合型の検出では、型をエンティティ型を参照し、別の型のコレクション プロパティから参照されていないプロパティにないことも必要です。 次のクラス定義を指定した Code First は推論を**詳細**複合型は、主キーがあるないためです。  

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

## <a name="connection-string-convention"></a>接続文字列の規則  

DbContext は参照を使用する接続の検出を使用している表記規則について[接続とモデル](~/ef6/fundamentals/configuring/connection-strings.md)します。  

## <a name="removing-conventions"></a>規則を削除します。  

System.Data.Entity.ModelConfiguration.Conventions 名前空間で定義されている規則を削除することができます。 次の例では、削除**PluralizingTableNameConvention**します。  

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

## <a name="custom-conventions"></a>カスタムの規則  

カスタムの規則は、以降の EF6 でサポートされます。 詳細については、次を参照してください。[カスタム コードの最初の規則](~/ef6/modeling/code-first/conventions/custom.md)します。
