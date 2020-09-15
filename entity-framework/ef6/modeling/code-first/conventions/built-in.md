---
title: Code First 規則-EF6
description: Entity Framework 6 の Code First 規則
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/code-first/conventions/built-in
ms.openlocfilehash: 71e865d674d6bef7aaf65d95abe1b1add96d890f
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2020
ms.locfileid: "90072605"
---
# <a name="code-first-conventions"></a>Code First 規則
Code First を使用すると、C# または Visual Basic .NET クラスを使用してモデルを記述できます。 モデルの基本図形は、規約を使用して検出されます。 規則は、Code First を操作するときに、クラス定義に基づいて概念モデルを自動的に構成するために使用される規則のセットです。 規則は、system.string 名前空間で定義されています。  

データ注釈または fluent API を使用して、モデルをさらに構成できます。 優先順位は、fluent API の後にデータ注釈と規則を使用して構成されます。 詳細については、「 [データ注釈](xref:ef6/modeling/code-first/data-annotations)」、「 [Fluent api-リレーションシップ](xref:ef6/modeling/code-first/fluent/relationships)」、「 [fluent api 型 & プロパティ](xref:ef6/modeling/code-first/fluent/types-and-properties) 」、および「 [VB.NET を使用した fluent api](xref:ef6/modeling/code-first/fluent/vb)」を参照してください。  

Code First の規則の詳細な一覧については、 [API のドキュメント](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx)を参照してください。 このトピックでは、Code First によって使用される規則の概要について説明します。  

## <a name="type-discovery"></a>種類の検出  

Code First 開発を使用する場合は、通常、概念 (ドメイン) モデルを定義する .NET Framework クラスを記述することから始めます。 クラスを定義するだけでなく、モデルに含める型を **Dbcontext** に知らせる必要もあります。 これを行うには、 **dbcontext** から派生したコンテキストクラスを定義し、モデルに含める型の **dbcontext** プロパティを公開します。 Code First にはこれらの型が含まれ、参照される型が別のアセンブリで定義されている場合でも、参照される型がすべて取得されます。  

型が継承階層に参加している場合は、基本クラスの **Dbset** プロパティを定義するだけで十分です。基底クラスと同じアセンブリ内にある場合は、派生型が自動的に含まれます。  

次の例では、 **SchoolEntities** **クラス (department**) で定義されている**dbset**プロパティは1つだけです。 Code First は、このプロパティを使用して、参照されるすべての型を検出し、プルします。  

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

モデルから型を除外する場合は、 **Notmapped** 属性または **dbmodelbuilder を使用します。** fluent API を無視します。  

```  csharp
modelBuilder.Ignore<Department>();
```  

## <a name="primary-key-convention"></a>主キーの規則  

クラスのプロパティの名前が "ID" である場合 (大文字と小文字は区別されません)、またはクラス名の後に "ID" が続く場合、Code First は、プロパティが主キーであると推測します。 主キープロパティの型が数値または GUID の場合は、id 列として構成されます。  

``` csharp
public class Department
{
    // Primary key
    public int DepartmentID { get; set; }

    . . .  

}
```  

## <a name="relationship-convention"></a>リレーションシップ規則  

Entity Framework では、ナビゲーション プロパティによって、2 つのエンティティ型間のリレーションシップをナビゲートする手段が提供されます。 各オブジェクトは、参加する各リレーションシップに対してナビゲーション プロパティを持つことができます。 ナビゲーションプロパティを使用すると、リレーションシップを双方向に移動して管理し、参照オブジェクト (多重度が1または0または1のいずれか) またはコレクション (多重度が多の場合) を返すことができます。 Code First は、型に定義されているナビゲーションプロパティに基づいてリレーションシップを推測します。  

ナビゲーションプロパティに加えて、依存オブジェクトを表す型に外部キープロパティを含めることをお勧めします。 プリンシパルプライマリキープロパティと同じデータ型で、次のいずれかの形式の名前を持つプロパティは、リレーションシップの外部キー (' \<navigation property name\> \<principal primary key property name\> '、' \<principal class name\> \<primary key property name\> '、または ' ') を表し \<principal primary key property name\> ます。 複数の一致が見つかった場合は、上記の順序で優先順位が付けられます。 外部キーの検出では、大文字と小文字は区別されません。 外部キープロパティが検出されると、Code First 外部キーの null 値の許容に基づいて、リレーションシップの多重度が推測されます。 プロパティが null 値を許容する場合、リレーションシップはオプションとして登録されます。それ以外の場合、リレーションシップは必須として登録されます。  

依存エンティティの外部キーが null 値を許容しない場合、Code First はリレーションシップに対して連鎖削除を設定します。 依存エンティティの外部キーが null 許容である場合、Code First はリレーションシップに対して連鎖削除を設定しません。また、プリンシパルが削除されると、外部キーは null に設定されます。 規則によって検出された多重度と連鎖削除の動作は、fluent API を使用してオーバーライドできます。  

次の例では、ナビゲーションプロパティと外部キーを使用して、Department クラスと Course クラス間のリレーションシップを定義しています。  

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
> 同じ型の間に複数のリレーションシップがある場合 (たとえば **、person クラス**に**ReviewedBooks**および**authoredbooks**ナビゲーションプロパティ**Person**が含まれていて、 **book** **クラスに****作成者**と**レビュー担当者**のナビゲーションプロパティが含まれている場合)、データ注釈または fluent API を使用してリレーションシップを手動で構成する必要があります。 詳細については、「 [データ注釈-リレーションシップ](xref:ef6/modeling/code-first/data-annotations) 」と「 [Fluent API-リレーションシップ](xref:ef6/modeling/code-first/fluent/relationships)」を参照してください。  

## <a name="complex-types-convention"></a>複合型の規則  

主キーを推論できず、データ注釈または fluent API によって登録される主キーがないクラス定義を Code First が検出すると、その型は複合型として自動的に登録されます。 複合型の検出では、型がエンティティ型を参照するプロパティを持たず、別の型のコレクションプロパティから参照されていないことも必要です。 次のクラス定義を指定すると Code First 主キーがないため、 **詳細** が複合型であると推論されます。  

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

## <a name="connection-string-convention"></a>接続文字列の表記規則  

使用する接続を検出するために DbContext が使用する規則については、「 [接続とモデル](xref:ef6/fundamentals/configuring/connection-strings)」を参照してください。  

## <a name="removing-conventions"></a>規則の削除  

System.string 名前空間で定義されている任意の規則を削除できます。 次の例では、 **PluralizingTableNameConvention**を削除します。  

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

## <a name="custom-conventions"></a>カスタム規約  

カスタム規則は EF6 以降でサポートされています。 詳細については、「 [カスタム Code First 規則](xref:ef6/modeling/code-first/conventions/custom)」を参照してください。
