---
title: リレーションシップ、ナビゲーション プロパティ、および外部キー - EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 8a21ae73-6d9b-4b50-838a-ec1fddffcf37
ms.openlocfilehash: 76169f25d01bed81ad0748c9e85a67bd112a6e65
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434327"
---
# <a name="relationships-navigation-properties-and-foreign-keys"></a><span data-ttu-id="d7338-102">リレーションシップ、ナビゲーション プロパティ、および外部キー</span><span class="sxs-lookup"><span data-stu-id="d7338-102">Relationships, navigation properties, and foreign keys</span></span>

<span data-ttu-id="d7338-103">この記事では、エンティティ フレームワークがエンティティ間の関係を管理する方法の概要を示します。</span><span class="sxs-lookup"><span data-stu-id="d7338-103">This article gives an overview of how Entity Framework manages relationships between entities.</span></span> <span data-ttu-id="d7338-104">また、リレーションシップのマッピング方法と操作方法に関するガイダンスも示します。</span><span class="sxs-lookup"><span data-stu-id="d7338-104">It also gives some guidance on how to map and manipulate relationships.</span></span>

## <a name="relationships-in-ef"></a><span data-ttu-id="d7338-105">EF のリレーションシップ</span><span class="sxs-lookup"><span data-stu-id="d7338-105">Relationships in EF</span></span>

<span data-ttu-id="d7338-106">リレーショナル・データベースでは、テーブル間の関係 (関連とも呼ばれる) は、外部キーを通じて定義されます。</span><span class="sxs-lookup"><span data-stu-id="d7338-106">In relational databases, relationships (also called associations) between tables are defined through foreign keys.</span></span> <span data-ttu-id="d7338-107">外部キー (FK) は、2 つのテーブルのデータ間にリンクを確立および設定するために使用される単一の列または複数の列の組み合わせです。</span><span class="sxs-lookup"><span data-stu-id="d7338-107">A foreign key (FK) is a column or combination of columns that is used to establish and enforce a link between the data in two tables.</span></span> <span data-ttu-id="d7338-108">一般的には、1 対 1、1 対多、および多対多の 3 種類のリレーションシップがあります。</span><span class="sxs-lookup"><span data-stu-id="d7338-108">There are generally three types of relationships: one-to-one, one-to-many, and many-to-many.</span></span> <span data-ttu-id="d7338-109">一対多リレーションシップでは、外部キーはリレーションシップの多端を表すテーブルに定義されます。</span><span class="sxs-lookup"><span data-stu-id="d7338-109">In a one-to-many relationship, the foreign key is defined on the table that represents the many end of the relationship.</span></span> <span data-ttu-id="d7338-110">多対多リレーションシップでは、3 つ目のテーブル (ジャンクションテーブルまたは結合テーブルと呼ばれます) を定義し、その主キーは両方の関連テーブルの外部キーで構成されます。</span><span class="sxs-lookup"><span data-stu-id="d7338-110">The many-to-many relationship involves defining a third table (called a junction or join table), whose primary key is composed of the foreign keys from both related tables.</span></span> <span data-ttu-id="d7338-111">一対一リレーションシップでは、主キーは外部キーとしてさらに機能し、どちらのテーブルにも別の外部キー列はありません。</span><span class="sxs-lookup"><span data-stu-id="d7338-111">In a one-to-one relationship, the primary key acts additionally as a foreign key and there is no separate foreign key column for either table.</span></span>

<span data-ttu-id="d7338-112">次の図は、一対多リレーションシップに参加する 2 つのテーブルを示しています。</span><span class="sxs-lookup"><span data-stu-id="d7338-112">The following image shows two tables that participate in one-to-many relationship.</span></span> <span data-ttu-id="d7338-113">**コース**テーブルには、部門テーブルにリンクする**部門ID**列が含まれているため、**依存**テーブルです。</span><span class="sxs-lookup"><span data-stu-id="d7338-113">The **Course** table is the dependent table because it contains the **DepartmentID** column that links it to the **Department** table.</span></span>

![部門とコーステーブル](~/ef6/media/database2.png)

<span data-ttu-id="d7338-115">エンティティ フレームワークでは、エンティティは、関連付けまたは関連付けを介して他のエンティティに関連付けることができます。</span><span class="sxs-lookup"><span data-stu-id="d7338-115">In Entity Framework, an entity can be related to other entities through an association or relationship.</span></span> <span data-ttu-id="d7338-116">各リレーションシップには、エンティティ型とそのリレーションシップの 2 つのエンティティの型 (1、0 または 1、または多) の多重度を記述する 2 つの端が含まれています。</span><span class="sxs-lookup"><span data-stu-id="d7338-116">Each relationship contains two ends that describe the entity type and the multiplicity of the type (one, zero-or-one, or many) for the two entities in that relationship.</span></span> <span data-ttu-id="d7338-117">リレーションシップは、リレーションシップのどちらの End がプリンシパル ロールでどちらの End が依存しているかを記した参照制約によって管理することができます。</span><span class="sxs-lookup"><span data-stu-id="d7338-117">The relationship may be governed by a referential constraint, which describes which end in the relationship is a principal role and which is a dependent role.</span></span>

<span data-ttu-id="d7338-118">ナビゲーション プロパティは、2 つのエンティティ型間の関連付けをナビゲートする方法を提供します。</span><span class="sxs-lookup"><span data-stu-id="d7338-118">Navigation properties provide a way to navigate an association between two entity types.</span></span> <span data-ttu-id="d7338-119">各オブジェクトは、参加する各リレーションシップに対してナビゲーション プロパティを持つことができます。</span><span class="sxs-lookup"><span data-stu-id="d7338-119">Every object can have a navigation property for every relationship in which it participates.</span></span> <span data-ttu-id="d7338-120">ナビゲーション プロパティを使用すると、参照オブジェクト (多重度が 1 または 0 または 1 の場合) またはコレクション (多重度が多い場合) を返して、両方向のリレーションシップをナビゲートおよび管理できます。</span><span class="sxs-lookup"><span data-stu-id="d7338-120">Navigation properties allow you to navigate and manage relationships in both directions, returning either a reference object (if the multiplicity is either one or zero-or-one) or a collection (if the multiplicity is many).</span></span> <span data-ttu-id="d7338-121">一方向ナビゲーションを使用することもできますが、その場合は、リレーションシップに参加する 1 つの型にのみナビゲーション プロパティを定義し、両方に定義することはできません。</span><span class="sxs-lookup"><span data-stu-id="d7338-121">You may also choose to have one-way navigation, in which case you define the navigation property on only one of the types that participates in the relationship and not on both.</span></span>

<span data-ttu-id="d7338-122">データベースの外部キーにマップするプロパティをモデルに含めるのが推奨されます。</span><span class="sxs-lookup"><span data-stu-id="d7338-122">It is recommended to include properties in the model that map to foreign keys in the database.</span></span> <span data-ttu-id="d7338-123">外部キー プロパティを含めることにより、依存オブジェクトの外部キーの値を変更してリレーションシップを作成または変更することができます。</span><span class="sxs-lookup"><span data-stu-id="d7338-123">With foreign key properties included, you can create or change a relationship by modifying the foreign key value on a dependent object.</span></span> <span data-ttu-id="d7338-124">このような種類のアソシエーションは外部キー アソシエーションと呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="d7338-124">This kind of association is called a foreign key association.</span></span> <span data-ttu-id="d7338-125">外部キーの使用は、切断されたエンティティを操作する場合にはさらに重要です。</span><span class="sxs-lookup"><span data-stu-id="d7338-125">Using foreign keys is even more essential when working with disconnected entities.</span></span> <span data-ttu-id="d7338-126">1 対 1 または 1 対 0 で作業する場合に注意してください。1 つのリレーションシップ、個別の外部キー列がない場合、主キー プロパティは外部キーとして機能し、常にモデルに含まれます。</span><span class="sxs-lookup"><span data-stu-id="d7338-126">Note, that when working with 1-to-1 or 1-to-0..1 relationships, there is no separate foreign key column, the primary key property acts as the foreign key and is always included in the model.</span></span>

<span data-ttu-id="d7338-127">外部キー列がモデルに含まれていない場合、関連付け情報は独立したオブジェクトとして管理されます。</span><span class="sxs-lookup"><span data-stu-id="d7338-127">When foreign key columns are not included in the model, the association information is managed as an independent object.</span></span> <span data-ttu-id="d7338-128">リレーションシップは、外部キー プロパティではなく、オブジェクト参照を通じて追跡されます。</span><span class="sxs-lookup"><span data-stu-id="d7338-128">Relationships are tracked through object references instead of foreign key properties.</span></span> <span data-ttu-id="d7338-129">このタイプの関連付けを、*独立アソシエーション*と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="d7338-129">This type of association is called an *independent association*.</span></span> <span data-ttu-id="d7338-130">*独立した関連付*けを変更する最も一般的な方法は、関連付けに参加する各エンティティに対して生成されるナビゲーション プロパティを変更することです。</span><span class="sxs-lookup"><span data-stu-id="d7338-130">The most common way to modify an *independent association* is to modify the navigation properties that are generated for each entity that participates in the association.</span></span>

<span data-ttu-id="d7338-131">モデルでは 1 つまたは両方のアソシエーションを使用するよう選択することができます。</span><span class="sxs-lookup"><span data-stu-id="d7338-131">You can choose to use one or both types of associations in your model.</span></span> <span data-ttu-id="d7338-132">ただし、外部キーのみを含む結合テーブルによって接続される純粋な多対多リレーションシップがある場合、EF は、このような多対多リレーションシップを管理するために独立した関連付けを使用します。</span><span class="sxs-lookup"><span data-stu-id="d7338-132">However, if you have a pure many-to-many relationship that is connected by a join table that contains only foreign keys, the EF will use an independent association to manage such many-to-many relationship.</span></span>   

<span data-ttu-id="d7338-133">次の図は、Entity Framework デザイナーで作成された概念モデルを示しています。</span><span class="sxs-lookup"><span data-stu-id="d7338-133">The following image shows a conceptual model that was created with the Entity Framework Designer.</span></span> <span data-ttu-id="d7338-134">モデルには、一対多の関係に参加する 2 つのエンティティが含まれています。</span><span class="sxs-lookup"><span data-stu-id="d7338-134">The model contains two entities that participate in one-to-many relationship.</span></span> <span data-ttu-id="d7338-135">両方のエンティティにはナビゲーション プロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="d7338-135">Both entities have navigation properties.</span></span> <span data-ttu-id="d7338-136">**コース**は依存エンティティであり、部門**ID**外部キー プロパティが定義されています。</span><span class="sxs-lookup"><span data-stu-id="d7338-136">**Course** is the dependent entity and has the **DepartmentID** foreign key property defined.</span></span>

![ナビゲーション プロパティを持つ部門テーブルとコース テーブル](~/ef6/media/relationshipefdesigner.png)

<span data-ttu-id="d7338-138">次のコード スニペットは、Code First で作成されたモデルと同じモデルを示しています。</span><span class="sxs-lookup"><span data-stu-id="d7338-138">The following code snippet shows the same model that was created with Code First.</span></span>

``` csharp
public class Course
{
  public int CourseID { get; set; }
  public string Title { get; set; }
  public int Credits { get; set; }
  public int DepartmentID { get; set; }
  public virtual Department Department { get; set; }
}

public class Department
{
   public Department()
   {
     this.Courses = new HashSet<Course>();
   }  
   public int DepartmentID { get; set; }
   public string Name { get; set; }
   public decimal Budget { get; set; }
   public DateTime StartDate { get; set; }
   public int? Administrator {get ; set; }
   public virtual ICollection<Course> Courses { get; set; }
}
```

## <a name="configuring-or-mapping-relationships"></a><span data-ttu-id="d7338-139">関係の構成またはマッピング</span><span class="sxs-lookup"><span data-stu-id="d7338-139">Configuring or mapping relationships</span></span>

<span data-ttu-id="d7338-140">このページの残りの部分では、リレーションシップを使用してデータにアクセスして操作する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="d7338-140">The rest of this page covers how to access and manipulate data using relationships.</span></span> <span data-ttu-id="d7338-141">モデルでのリレーションシップの設定については、次のページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="d7338-141">For information on setting up relationships in your model, see the following pages.</span></span>

-   <span data-ttu-id="d7338-142">コードファーストで関係を設定するには、「[データ注釈](~/ef6/modeling/code-first/data-annotations.md)と[Fluent API – リレーションシップ](~/ef6/modeling/code-first/fluent/relationships.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d7338-142">To configure relationships in Code First, see [Data Annotations](~/ef6/modeling/code-first/data-annotations.md) and [Fluent API – Relationships](~/ef6/modeling/code-first/fluent/relationships.md).</span></span>
-   <span data-ttu-id="d7338-143">エンティティ フレームワーク デザイナーを使用してリレーションシップを構成するには[、「EF デザイナーとの関係」](~/ef6/modeling/designer/relationships.md)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d7338-143">To configure relationships using the Entity Framework Designer, see [Relationships with the EF Designer](~/ef6/modeling/designer/relationships.md).</span></span>

## <a name="creating-and-modifying-relationships"></a><span data-ttu-id="d7338-144">リレーションシップの作成と変更</span><span class="sxs-lookup"><span data-stu-id="d7338-144">Creating and modifying relationships</span></span>

<span data-ttu-id="d7338-145">*外部キーアソシエーション*では、リレーションシップを変更すると、状態を持つ依存オブジェクトの状態が`EntityState.Unchanged`に変更`EntityState.Modified`されます。</span><span class="sxs-lookup"><span data-stu-id="d7338-145">In a *foreign key association*, when you change the relationship, the state of a dependent object with an `EntityState.Unchanged` state changes to `EntityState.Modified`.</span></span> <span data-ttu-id="d7338-146">独立した関係では、関係を変更しても、依存オブジェクトの状態は更新されません。</span><span class="sxs-lookup"><span data-stu-id="d7338-146">In an independent relationship, changing the relationship does not update the state of the dependent object.</span></span>

<span data-ttu-id="d7338-147">次の例は、外部キー プロパティとナビゲーション プロパティを使用して関連オブジェクトを関連付ける方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="d7338-147">The following examples show how to use the foreign key properties and navigation properties to associate the related objects.</span></span> <span data-ttu-id="d7338-148">外部キーの関連付けを使用すると、リレーションシップの変更、作成、または変更を行うことができます。</span><span class="sxs-lookup"><span data-stu-id="d7338-148">With foreign key associations, you can use either method to change, create, or modify relationships.</span></span> <span data-ttu-id="d7338-149">独立アソシエーションでは、外部キー プロパティは使用できません。</span><span class="sxs-lookup"><span data-stu-id="d7338-149">With independent associations, you cannot use the foreign key property.</span></span>

- <span data-ttu-id="d7338-150">次の例のように、外部キー プロパティに新しい値を割り当てます。</span><span class="sxs-lookup"><span data-stu-id="d7338-150">By assigning a new value to a foreign key property, as in the following example.</span></span>  
  ``` csharp
  course.DepartmentID = newCourse.DepartmentID;
  ```

- <span data-ttu-id="d7338-151">次のコードは、外部キーを**null**に設定してリレーションシップを削除します。</span><span class="sxs-lookup"><span data-stu-id="d7338-151">The following code removes a relationship by setting the foreign key to **null**.</span></span> <span data-ttu-id="d7338-152">外部キー プロパティは null 許容である必要があります。</span><span class="sxs-lookup"><span data-stu-id="d7338-152">Note, that the foreign key property must be nullable.</span></span>  
  ``` csharp
  course.DepartmentID = null;
  ```

  >[!NOTE]
  > <span data-ttu-id="d7338-153">参照が追加された状態 (この例ではコース オブジェクト) の場合、参照ナビゲーション プロパティは SaveChanges が呼び出されるまで、新しいオブジェクトのキー値と同期されません。</span><span class="sxs-lookup"><span data-stu-id="d7338-153">If the reference is in the added state (in this example, the course object), the reference navigation property will not be synchronized with the key values of a new object until SaveChanges is called.</span></span> <span data-ttu-id="d7338-154">同期が行われない理由は、追加されたオブジェクトが保存されるまでオブジェクト コンテキストに永久キーが含まれていないからです。</span><span class="sxs-lookup"><span data-stu-id="d7338-154">Synchronization does not occur because the object context does not contain permanent keys for added objects until they are saved.</span></span> <span data-ttu-id="d7338-155">リレーションシップを設定した直後に新しいオブジェクトを完全に同期する必要がある場合は、次のいずれかの方法を使用します。</span><span class="sxs-lookup"><span data-stu-id="d7338-155">If you must have new objects fully synchronized as soon as you set the relationship, use one of the following methods.\*</span></span>

- <span data-ttu-id="d7338-156">新しいオブジェクトをナビゲーション プロパティに割り当てる。</span><span class="sxs-lookup"><span data-stu-id="d7338-156">By assigning a new object to a navigation property.</span></span> <span data-ttu-id="d7338-157">次のコードは、コースと`department`.</span><span class="sxs-lookup"><span data-stu-id="d7338-157">The following code creates a relationship between a course and a `department`.</span></span> <span data-ttu-id="d7338-158">オブジェクトがコンテキストにアタッチされている場合`course`は、 も`department.Courses`コレクションに追加され、`course`オブジェクトの対応する外部キー プロパティは部門のキー プロパティ値に設定されます。</span><span class="sxs-lookup"><span data-stu-id="d7338-158">If the objects are attached to the context, the `course` is also added to the `department.Courses` collection, and the corresponding foreign key property on the `course` object is set to the key property value of the department.</span></span>  
  ``` csharp
  course.Department = department;
  ```

- <span data-ttu-id="d7338-159">リレーションシップを削除するには、ナビゲーション プロパティを`null`に設定します。</span><span class="sxs-lookup"><span data-stu-id="d7338-159">To delete the relationship, set the navigation property to `null`.</span></span> <span data-ttu-id="d7338-160">.NET 4.0 に基づく Entity Framework を使用している場合は、関連する末尾を読み込んでから null に設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d7338-160">If you are working with Entity Framework that is based on .NET 4.0, then the related end needs to be loaded before you set it to null.</span></span> <span data-ttu-id="d7338-161">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="d7338-161">For example:</span></span>   
  ``` csharp
  context.Entry(course).Reference(c => c.Department).Load();
  course.Department = null;
  ```

  <span data-ttu-id="d7338-162">.NET 4.5 に基づく Entity Framework 5.0 以降では、関連する End を読み込まずにリレーションシップを null に設定できます。</span><span class="sxs-lookup"><span data-stu-id="d7338-162">Starting with Entity Framework 5.0, that is based on .NET 4.5, you can set the relationship to null without loading the related end.</span></span> <span data-ttu-id="d7338-163">次の方法を使用して、現在の値を null に設定することもできます。</span><span class="sxs-lookup"><span data-stu-id="d7338-163">You can also set the current value to null using the following method.</span></span>   
  ``` csharp
  context.Entry(course).Reference(c => c.Department).CurrentValue = null;
  ```

- <span data-ttu-id="d7338-164">エンティティ コレクションのオブジェクトを削除または追加する。</span><span class="sxs-lookup"><span data-stu-id="d7338-164">By deleting or adding an object in an entity collection.</span></span> <span data-ttu-id="d7338-165">たとえば、型`Course`のオブジェクトをコレクションに`department.Courses`追加できます。</span><span class="sxs-lookup"><span data-stu-id="d7338-165">For example, you can add an object of type `Course` to the `department.Courses` collection.</span></span> <span data-ttu-id="d7338-166">この操作により、特定の**コース**と の間に`department`関係が作成されます。</span><span class="sxs-lookup"><span data-stu-id="d7338-166">This operation creates a relationship between a particular **course** and a particular `department`.</span></span> <span data-ttu-id="d7338-167">オブジェクトがコンテキストにアタッチされている場合、**コース**オブジェクトの部門参照と外部キープロパティが適切な`department`に設定されます。</span><span class="sxs-lookup"><span data-stu-id="d7338-167">If the objects are attached to the context, the department reference and the foreign key property on the **course** object will be set to the appropriate `department`.</span></span>  
  ``` csharp
  department.Courses.Add(newCourse);
  ```

- <span data-ttu-id="d7338-168">`ChangeRelationshipState` 2 つのエンティティ オブジェクト間の指定されたリレーションシップの状態を変更するメソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="d7338-168">By using the `ChangeRelationshipState` method to change the state of the specified relationship between two entity objects.</span></span> <span data-ttu-id="d7338-169">この方法は、N 層アプリケーションおよび*独立した関連付け*(外部キーの関連付けでは使用できません) を使用する場合に最も一般的に使用されます。</span><span class="sxs-lookup"><span data-stu-id="d7338-169">This method is most commonly used when working with N-Tier applications and an *independent association* (it cannot be used with a foreign key association).</span></span> <span data-ttu-id="d7338-170">また、このメソッドを使用するには、次の例`ObjectContext`に示すように、 にドロップダウンする必要があります。</span><span class="sxs-lookup"><span data-stu-id="d7338-170">Also, to use this method you must drop down to `ObjectContext`, as shown in the example below.</span></span>  
<span data-ttu-id="d7338-171">次の例では、インストラクタとコースの間に多対多の関係があります。</span><span class="sxs-lookup"><span data-stu-id="d7338-171">In the following example, there is a many-to-many relationship between Instructors and Courses.</span></span> <span data-ttu-id="d7338-172">メソッドを`ChangeRelationshipState`呼び出して`EntityState.Added`パラメータを渡すと`SchoolContext`、2 つのオブジェクト間にリレーションシップが追加されたことを知ることができます。</span><span class="sxs-lookup"><span data-stu-id="d7338-172">Calling the `ChangeRelationshipState` method and passing the `EntityState.Added` parameter, lets the `SchoolContext` know that a relationship has been added between the two objects:</span></span>
  ``` csharp

  ((IObjectContextAdapter)context).ObjectContext.
    ObjectStateManager.
    ChangeRelationshipState(course, instructor, c => c.Instructor, EntityState.Added);
  ```

  <span data-ttu-id="d7338-173">リレーションシップを更新する場合 (単に追加するだけではなく)、新しいリレーションシップを追加した後で古いリレーションシップを削除する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d7338-173">Note that if you are updating (not just adding) a relationship, you must delete the old relationship after adding the new one:</span></span>

  ``` csharp
  ((IObjectContextAdapter)context).ObjectContext.
    ObjectStateManager.
    ChangeRelationshipState(course, oldInstructor, c => c.Instructor, EntityState.Deleted);
  ```

## <a name="synchronizing-the-changes-between-the-foreign-keys-and-navigation-properties"></a><span data-ttu-id="d7338-174">外部キーとナビゲーション プロパティの間の変更の同期</span><span class="sxs-lookup"><span data-stu-id="d7338-174">Synchronizing the changes between the foreign keys and navigation properties</span></span>

<span data-ttu-id="d7338-175">上記のいずれかのメソッドを使用してコンテキストにアタッチされたオブジェクトのリレーションシップを変更する場合、Entity Framework は、外部キー、参照、およびコレクションを同期する必要があります。Entity Framework は、プロキシを持つ POCO エンティティのこの同期 (関連解決とも呼ばれます) を自動的に管理します。</span><span class="sxs-lookup"><span data-stu-id="d7338-175">When you change the relationship of the objects attached to the context by using one of the methods described above, Entity Framework needs to keep foreign keys, references, and collections in sync. Entity Framework automatically manages this synchronization (also known as relationship fix-up) for the POCO entities with proxies.</span></span> <span data-ttu-id="d7338-176">詳細については、「[プロキシの操作](~/ef6/fundamentals/proxies.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d7338-176">For more information, see [Working with Proxies](~/ef6/fundamentals/proxies.md).</span></span>

<span data-ttu-id="d7338-177">プロキシを使用せずに POCO エンティティを使用している場合は、コンテキスト内の関連オブジェクトを同期するために**DetectChanges**メソッドが呼び出されていることを確認する必要があります。</span><span class="sxs-lookup"><span data-stu-id="d7338-177">If you are using POCO entities without proxies, you must make sure that the **DetectChanges** method is called to synchronize the related objects in the context.</span></span> <span data-ttu-id="d7338-178">次の API は、自動的に**DetectChanges**呼び出しをトリガーします。</span><span class="sxs-lookup"><span data-stu-id="d7338-178">Note that the following APIs automatically trigger a **DetectChanges** call.</span></span>

-   `DbSet.Add`
-   `DbSet.AddRange`
-   `DbSet.Remove`
-   `DbSet.RemoveRange`
-   `DbSet.Find`
-   `DbSet.Local`
-   `DbContext.SaveChanges`
-   `DbSet.Attach`
-   `DbContext.GetValidationErrors`
-   `DbContext.Entry`
-   `DbChangeTracker.Entries`
-   <span data-ttu-id="d7338-179">に対する LINQ クエリの実行`DbSet`</span><span class="sxs-lookup"><span data-stu-id="d7338-179">Executing a LINQ query against a `DbSet`</span></span>

## <a name="loading-related-objects"></a><span data-ttu-id="d7338-180">関連オブジェクトの読み込み</span><span class="sxs-lookup"><span data-stu-id="d7338-180">Loading related objects</span></span>

<span data-ttu-id="d7338-181">Entity Framework では、一般的に、定義された関連付けによって返されるエンティティに関連するエンティティを読み込むためにナビゲーション プロパティを使用します。</span><span class="sxs-lookup"><span data-stu-id="d7338-181">In Entity Framework you commonly use navigation properties to load entities that are related to the returned entity by the defined association.</span></span> <span data-ttu-id="d7338-182">詳細については、「[関連オブジェクトのロード](~/ef6/querying/related-data.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d7338-182">For more information, see [Loading Related Objects](~/ef6/querying/related-data.md).</span></span>

> [!NOTE]
> <span data-ttu-id="d7338-183">外部キー アソシエーションで依存オブジェクトの関連 End を読み込むと、現在メモリ内にある依存の外部キー値に基づいて関連オブジェクトが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="d7338-183">In a foreign key association, when you load a related end of a dependent object, the related object will be loaded based on the foreign key value of the dependent that is currently in memory:</span></span>

``` csharp
    // Get the course where currently DepartmentID = 2.
    Course course2 = context.Courses.First(c => c.DepartmentID == 2);

    // Use DepartmentID foreign key property
    // to change the association.
    course2.DepartmentID = 3;

    // Load the related Department where DepartmentID = 3
    context.Entry(course).Reference(c => c.Department).Load();
```

<span data-ttu-id="d7338-184">独立アソシエーションの場合、依存オブジェクトの関連 End は現在データベース内にある外部キー値に基づいてクエリが行われます。</span><span class="sxs-lookup"><span data-stu-id="d7338-184">In an independent association, the related end of a dependent object is queried based on the foreign key value that is currently in the database.</span></span> <span data-ttu-id="d7338-185">ただし、リレーションシップが変更され、依存オブジェクトの参照プロパティが、オブジェクト コンテキストに読み込まれる別のプリンシパル オブジェクトを指している場合、Entity Framework はクライアントで定義されている関連付けを作成しようとします。</span><span class="sxs-lookup"><span data-stu-id="d7338-185">However, if the relationship was modified, and the reference property on the dependent object points to a different principal object that is loaded in the object context, Entity Framework will try to create a relationship as it is defined on the client.</span></span>

## <a name="managing-concurrency"></a><span data-ttu-id="d7338-186">コンカレンシーを管理する</span><span class="sxs-lookup"><span data-stu-id="d7338-186">Managing concurrency</span></span>

<span data-ttu-id="d7338-187">外部キーと独立した関連付けの両方で、同時実行チェックは、モデルで定義されているエンティティ キーおよびその他のエンティティ プロパティに基づきます。</span><span class="sxs-lookup"><span data-stu-id="d7338-187">In both foreign key and independent associations, concurrency checks are based on the entity keys and other entity properties that are defined in the model.</span></span> <span data-ttu-id="d7338-188">EF Designer を使用してモデルを作成する場合`ConcurrencyMode`は、属性を**fixed**に設定して、プロパティの同時実行性をチェックするように指定します。</span><span class="sxs-lookup"><span data-stu-id="d7338-188">When using the EF Designer to create a model, set the `ConcurrencyMode` attribute to **fixed** to specify that the property should be checked for concurrency.</span></span> <span data-ttu-id="d7338-189">Code First を使用してモデルを定義する`ConcurrencyCheck`場合は、同時実行性をチェックするプロパティに対して注釈を使用します。</span><span class="sxs-lookup"><span data-stu-id="d7338-189">When using Code First to define a model, use the `ConcurrencyCheck` annotation on properties that you want to be checked for concurrency.</span></span> <span data-ttu-id="d7338-190">Code First を使用する場合は、`TimeStamp`アノテーションを使用して、プロパティの同時実行性をチェックするように指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="d7338-190">When working with Code First you can also use the `TimeStamp` annotation to specify that the property should be checked for concurrency.</span></span> <span data-ttu-id="d7338-191">指定したクラスに含めることができるタイムスタンプ プロパティは 1 つだけです。</span><span class="sxs-lookup"><span data-stu-id="d7338-191">You can have only one timestamp property in a given class.</span></span> <span data-ttu-id="d7338-192">コード First は、このプロパティをデータベース内の null 非許容フィールドにマップします。</span><span class="sxs-lookup"><span data-stu-id="d7338-192">Code First maps this property to a non-nullable field in the database.</span></span>

<span data-ttu-id="d7338-193">同時実行のチェックと解決に参加するエンティティを操作するときは、常に外部キーの関連付けを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="d7338-193">We recommend that you always use the foreign key association when working with entities that participate in concurrency checking and resolution.</span></span>

<span data-ttu-id="d7338-194">詳細については、「[同時実行の競合の処理](~/ef6/saving/concurrency.md)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="d7338-194">For more information, see [Handling Concurrency Conflicts](~/ef6/saving/concurrency.md).</span></span>

## <a name="working-with-overlapping-keys"></a><span data-ttu-id="d7338-195">重なり合うキーの操作</span><span class="sxs-lookup"><span data-stu-id="d7338-195">Working with overlapping Keys</span></span>

<span data-ttu-id="d7338-196">重複するキーは、キーの一部のプロパティがエンティティの別のキーの一部でもある複合キーです。</span><span class="sxs-lookup"><span data-stu-id="d7338-196">Overlapping keys are composite keys where some properties in the key are also part of another key in the entity.</span></span> <span data-ttu-id="d7338-197">独立アソシエーションで重複するキーを持つことはできません。</span><span class="sxs-lookup"><span data-stu-id="d7338-197">You cannot have an overlapping key in an independent association.</span></span> <span data-ttu-id="d7338-198">重複するキーを持つ外部キー アソシエーションを変更する場合は、オブジェクト参照を使用するよりも外部キー値を変更することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="d7338-198">To change a foreign key association that includes overlapping keys, we recommend that you modify the foreign key values instead of using the object references.</span></span>
