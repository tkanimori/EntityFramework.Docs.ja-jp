---
title: リレーションシップ、ナビゲーション プロパティ、および外部キーは、EF6
author: divega
ms.date: 2016-10-23
ms.assetid: 8a21ae73-6d9b-4b50-838a-ec1fddffcf37
ms.openlocfilehash: a1653afd609280ab572ef88a9fcf8a6275b79fd6
ms.sourcegitcommit: a81aed575372637997b18a0f9466d8fefb33350a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/06/2018
ms.locfileid: "43821401"
---
# <a name="relationships-navigation-properties-and-foreign-keys"></a><span data-ttu-id="49397-102">リレーションシップ、ナビゲーション プロパティ、および外部キー</span><span class="sxs-lookup"><span data-stu-id="49397-102">Relationships, navigation properties and foreign keys</span></span>
<span data-ttu-id="49397-103">このトピックでは、Entity Framework でのエンティティ間のリレーションシップの管理方法の概要を示します。</span><span class="sxs-lookup"><span data-stu-id="49397-103">This topic gives an overview of how Entity Framework manages relationships between entities.</span></span> <span data-ttu-id="49397-104">また、マップし、のリレーションシップを操作する方法のいくつかのガイダンスを提供します。</span><span class="sxs-lookup"><span data-stu-id="49397-104">It also gives some guidance on how to map and manipulate relationships.</span></span>

## <a name="relationships-in-ef"></a><span data-ttu-id="49397-105">EF でのリレーションシップ</span><span class="sxs-lookup"><span data-stu-id="49397-105">Relationships in EF</span></span>

<span data-ttu-id="49397-106">リレーショナル データベース、テーブル間のリレーションシップ (アソシエーションとも呼ばれます) は、外部キーを通じて定義されます。</span><span class="sxs-lookup"><span data-stu-id="49397-106">In relational databases, relationships (also called associations) between tables are defined through foreign keys.</span></span> <span data-ttu-id="49397-107">外部キー (FK) は、列または列の組み合わせを確立し、2 つのテーブル内のデータ間のリンクを強制するために使用します。</span><span class="sxs-lookup"><span data-stu-id="49397-107">A foreign key (FK) is a column or combination of columns that is used to establish and enforce a link between the data in two tables.</span></span> <span data-ttu-id="49397-108">一般に、次の 3 つの種類のリレーションシップ。 一対一、一対多および多対多です。</span><span class="sxs-lookup"><span data-stu-id="49397-108">There are generally three types of relationships: one-to-one, one-to-many, and many-to-many.</span></span> <span data-ttu-id="49397-109">一対多リレーションシップでは、外部キーが多のリレーションシップの側を表すテーブルで定義されます。</span><span class="sxs-lookup"><span data-stu-id="49397-109">In a one-to-many relationship, the foreign key is defined on the table that represents the many end of the relationship.</span></span> <span data-ttu-id="49397-110">多対多リレーションシップでは、両方の関連テーブルから外部キーは、主キーで構成されます (結合または結合テーブルといいます)、3 番目テーブルを定義する必要があります。</span><span class="sxs-lookup"><span data-stu-id="49397-110">The many-to-many relationship involves defining a third table (called a junction or join table), whose primary key is composed of the foreign keys from both related tables.</span></span> <span data-ttu-id="49397-111">一対一のリレーションシップの主キーがさらに外部キーとして機能し、いずれかのテーブルの別の外部キー列はありません。</span><span class="sxs-lookup"><span data-stu-id="49397-111">In a one-to-one relationship, the primary key acts additionally as a foreign key and there is no separate foreign key column for either table.</span></span>

<span data-ttu-id="49397-112">次の図では、一対多のリレーションシップに参加している 2 つのテーブルを示します。</span><span class="sxs-lookup"><span data-stu-id="49397-112">The following image shows two tables that participate in one-to-many relationship.</span></span> <span data-ttu-id="49397-113">**コース**を含んでいるために、テーブルが依存するテーブル、 **DepartmentID**列にリンクする、**部門**テーブル。</span><span class="sxs-lookup"><span data-stu-id="49397-113">The **Course** table is the dependent table because it contains the **DepartmentID** column that links it to the **Department** table.</span></span>

![Database2](~/ef6/media/database2.png)

<span data-ttu-id="49397-115">Entity Framework では、エンティティはアソシエーションまたはリレーションシップを使用して他のエンティティに関連付けることができます。</span><span class="sxs-lookup"><span data-stu-id="49397-115">In Entity Framework, an entity can be related to other entities through an association or relationship.</span></span> <span data-ttu-id="49397-116">各リレーションシップには、エンティティ型との関係では、2 つのエンティティの種類 (1 つ、0 または 1、または多く) の多重度を示す 2 つの end が含まれています。</span><span class="sxs-lookup"><span data-stu-id="49397-116">Each relationship contains two ends that describe the entity type and the multiplicity of the type (one, zero-or-one, or many) for the two entities in that relationship.</span></span> <span data-ttu-id="49397-117">リレーションシップが終了するはプリンシパルの役割について説明しますが、参照に関する制約は、リレーションシップを準拠場合があります依存ロールであるとします。</span><span class="sxs-lookup"><span data-stu-id="49397-117">The relationship may be governed by a referential constraint, which describes which end in the relationship is a principal role and which is a dependent role.</span></span>

<span data-ttu-id="49397-118">ナビゲーション プロパティは、2 つのエンティティ型間のアソシエーションを移動する方法を提供します。</span><span class="sxs-lookup"><span data-stu-id="49397-118">Navigation properties provide a way to navigate an association between two entity types.</span></span> <span data-ttu-id="49397-119">各オブジェクトは、参加する各リレーションシップに対してナビゲーション プロパティを持つことができます。</span><span class="sxs-lookup"><span data-stu-id="49397-119">Every object can have a navigation property for every relationship in which it participates.</span></span> <span data-ttu-id="49397-120">ナビゲーション プロパティを使用するに移動し、参照オブジェクトを返す双方向のリレーションシップを管理できます (多重度がいずれかである場合または 0 または 1) または (多重度が多くの場合) のコレクション。</span><span class="sxs-lookup"><span data-stu-id="49397-120">Navigation properties allow you to navigate and manage relationships in both directions, returning either a reference object (if the multiplicity is either one or zero-or-one) or a collection (if the multiplicity is many).</span></span> <span data-ttu-id="49397-121">できますが一方向のナビゲーションで、リレーションシップの種類の 1 つだけにし、両方ではなくナビゲーション プロパティを定義する場合。</span><span class="sxs-lookup"><span data-stu-id="49397-121">You may also choose to have one-way navigation, in which case you define the navigation property on only one of the types that participates in the relationship and not on both.</span></span>

<span data-ttu-id="49397-122">データベースの外部キーにマップされるモデルにプロパティを含めることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="49397-122">It is recommended to include properties in the model that map to foreign keys in the database.</span></span> <span data-ttu-id="49397-123">外部キー プロパティを含めることにより、依存オブジェクトの外部キーの値を変更してリレーションシップを作成または変更することができます。</span><span class="sxs-lookup"><span data-stu-id="49397-123">With foreign key properties included, you can create or change a relationship by modifying the foreign key value on a dependent object.</span></span> <span data-ttu-id="49397-124">このような種類のアソシエーションは外部キー アソシエーションと呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="49397-124">This kind of association is called a foreign key association.</span></span> <span data-ttu-id="49397-125">切り離されたエンティティを使用する場合は、外部キーを使用するとよりも重要なです。</span><span class="sxs-lookup"><span data-stu-id="49397-125">Using foreign keys is even more essential when working with disconnected entities.</span></span> <span data-ttu-id="49397-126">なお、時に 1 対 1 または 1 対 0 の操作.1 のリレーションシップでは、別の外部キー列は、主キー プロパティが、外部キーとして機能し、モデルに含まれる常にします。</span><span class="sxs-lookup"><span data-stu-id="49397-126">Note, that when working with 1-to-1 or 1-to-0..1 relationships, there is no separate foreign key column, the primary key property acts as the foreign key and is always included in the model.</span></span>

<span data-ttu-id="49397-127">モデルでは、外部キー列が含まれていない、関連付け情報は、独立したオブジェクトとして管理されます。</span><span class="sxs-lookup"><span data-stu-id="49397-127">When foreign key columns are not included in the model, the association information is managed as an independent object.</span></span> <span data-ttu-id="49397-128">リレーションシップは、外部キー プロパティではなくオブジェクト参照によって追跡されます。</span><span class="sxs-lookup"><span data-stu-id="49397-128">Relationships are tracked through object references instead of foreign key properties.</span></span> <span data-ttu-id="49397-129">アソシエーションのこの型が呼び出される、*独立アソシエーション*します。</span><span class="sxs-lookup"><span data-stu-id="49397-129">This type of association is called an *independent association*.</span></span> <span data-ttu-id="49397-130">変更する最も一般的な方法、*独立アソシエーション*は、アソシエーションに参加する各エンティティに対して生成されるナビゲーション プロパティを変更します。</span><span class="sxs-lookup"><span data-stu-id="49397-130">The most common way to modify an *independent association* is to modify the navigation properties that are generated for each entity that participates in the association.</span></span>

<span data-ttu-id="49397-131">モデルでは 1 つまたは両方のアソシエーションを使用するよう選択することができます。</span><span class="sxs-lookup"><span data-stu-id="49397-131">You can choose to use one or both types of associations in your model.</span></span> <span data-ttu-id="49397-132">ただし、外部キーのみを含む結合テーブルで接続する純粋な多対多リレーションシップがあれば、EF は独立アソシエーションをこのような多対多リレーションシップの管理に使用します。</span><span class="sxs-lookup"><span data-stu-id="49397-132">However, if you have a pure many-to-many relationship that is connected by a join table that contains only foreign keys, the EF will use an independent association to manage such many-to-many relationship.</span></span>   

<span data-ttu-id="49397-133">次の図は、Entity Framework デザイナーで作成された概念モデルを示しています。</span><span class="sxs-lookup"><span data-stu-id="49397-133">The following image shows a conceptual model that was created with the Entity Framework Designer.</span></span> <span data-ttu-id="49397-134">モデルには、一対多のリレーションシップに参加している 2 つのエンティティが含まれています。</span><span class="sxs-lookup"><span data-stu-id="49397-134">The model contains two entities that participate in one-to-many relationship.</span></span> <span data-ttu-id="49397-135">両方のエンティティには、ナビゲーション プロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="49397-135">Both entities have navigation properties.</span></span> <span data-ttu-id="49397-136">**コース**は depend エンティティであり、 **DepartmentID**外部キー プロパティを定義します。</span><span class="sxs-lookup"><span data-stu-id="49397-136">**Course** is the depend entity and has the **DepartmentID** foreign key property defined.</span></span>

![RelationshipEFDesigner](~/ef6/media/relationshipefdesigner.png)

<span data-ttu-id="49397-138">次のコード スニペットでは、Code First で作成された同じモデルを示します。</span><span class="sxs-lookup"><span data-stu-id="49397-138">The following code snippet shows the same model that was created with Code First.</span></span>

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
     this.Course = new HashSet<Course>();
   }  
   public int DepartmentID { get; set; }
   public string Name { get; set; }
   public decimal Budget { get; set; }
   public DateTime StartDate { get; set; }
   public int? Administrator {get ; set; }
   public virtual ICollection<Course> Courses { get; set; }
}
```

## <a name="configuring-or-mapping-relationships"></a><span data-ttu-id="49397-139">構成またはマッピングのリレーションシップ</span><span class="sxs-lookup"><span data-stu-id="49397-139">Configuring or mapping relationships</span></span>

<span data-ttu-id="49397-140">このページの残りの部分では、アクセスし、リレーションシップを使用してデータを操作する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="49397-140">The rest of this page covers how to access and manipulate data using relationships.</span></span> <span data-ttu-id="49397-141">モデル内のリレーションシップをセットアップする方法については、次のページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="49397-141">For information on setting up relationships in your model, see the following pages.</span></span>

-   <span data-ttu-id="49397-142">Code First でのリレーションシップを構成するを参照してください。[データ注釈](~/ef6/modeling/code-first/data-annotations.md)と[Fluent API – リレーションシップ](~/ef6/modeling/code-first/fluent/relationships.md)します。</span><span class="sxs-lookup"><span data-stu-id="49397-142">To configure relationships in Code First, see [Data Annotations](~/ef6/modeling/code-first/data-annotations.md) and [Fluent API – Relationships](~/ef6/modeling/code-first/fluent/relationships.md).</span></span>
-   <span data-ttu-id="49397-143">Entity Framework デザイナーを使用してリレーションシップを構成するを参照してください。 [EF Designer でリレーションシップ](~/ef6/modeling/designer/relationships.md)します。</span><span class="sxs-lookup"><span data-stu-id="49397-143">To configure relationships using the Entity Framework Designer, see [Relationships with the EF Designer](~/ef6/modeling/designer/relationships.md).</span></span>

## <a name="creating-and-modifying-relationships"></a><span data-ttu-id="49397-144">作成して、リレーションシップの変更</span><span class="sxs-lookup"><span data-stu-id="49397-144">Creating and modifying relationships</span></span>

<span data-ttu-id="49397-145">*外部キー アソシエーション*、リレーションシップを使用して、依存オブジェクトの状態を変更するときに、`EntityState.Unchanged`状態に変わります`EntityState.Modified`。</span><span class="sxs-lookup"><span data-stu-id="49397-145">In a *foreign key association*, when you change the relationship, the state of a dependent object with an `EntityState.Unchanged` state changes to `EntityState.Modified`.</span></span> <span data-ttu-id="49397-146">独立リレーションシップでは、リレーションシップの変更は、依存オブジェクトの状態は更新できません。</span><span class="sxs-lookup"><span data-stu-id="49397-146">In an independent relationship, changing the relationship does not update the state of the dependent object.</span></span>

<span data-ttu-id="49397-147">次の例では、外部キー プロパティとナビゲーション プロパティを使用して、関連するオブジェクトを関連付ける方法を示します。</span><span class="sxs-lookup"><span data-stu-id="49397-147">The following examples show how to use the foreign key properties and navigation properties to associate the related objects.</span></span> <span data-ttu-id="49397-148">外部キー アソシエーションでは、変更、作成、またはリレーションシップを変更するいずれかの方法を使用できます。</span><span class="sxs-lookup"><span data-stu-id="49397-148">With foreign key associations, you can use either method to change, create, or modify relationships.</span></span> <span data-ttu-id="49397-149">独立アソシエーションでは、外部キー プロパティは使用できません。</span><span class="sxs-lookup"><span data-stu-id="49397-149">With independent associations, you cannot use the foreign key property.</span></span>

- <span data-ttu-id="49397-150">で、次の例のように、外部キーのプロパティに新しい値を代入します。</span><span class="sxs-lookup"><span data-stu-id="49397-150">By assigning a new value to a foreign key property, as in the following example.</span></span>  
  ``` csharp
  course.DepartmentID = newCourse.DepartmentID;
  ```

- <span data-ttu-id="49397-151">次のコードでは、リレーションシップを削除外部キーを設定して**null**します。</span><span class="sxs-lookup"><span data-stu-id="49397-151">The following code removes a relationship by setting the foreign key to **null**.</span></span> <span data-ttu-id="49397-152">外部キー プロパティを null 許容型でなければならないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="49397-152">Note, that the foreign key property must be nullable.</span></span>  
  ``` csharp
  course.DepartmentID = null;
  ```

  >[!NOTE]
  > <span data-ttu-id="49397-153">参照が (この例では、コース オブジェクト) で、追加済み状態にある場合は、参照ナビゲーション プロパティは同期されません新しいオブジェクトのキー値を持つ SaveChanges が呼び出されるまで。</span><span class="sxs-lookup"><span data-stu-id="49397-153">If the reference is in the added state (in this example, the course object), the reference navigation property will not be synchronized with the key values of a new object until SaveChanges is called.</span></span> <span data-ttu-id="49397-154">同期が行われない理由は、追加されたオブジェクトが保存されるまでオブジェクト コンテキストに永久キーが含まれていないからです。</span><span class="sxs-lookup"><span data-stu-id="49397-154">Synchronization does not occur because the object context does not contain permanent keys for added objects until they are saved.</span></span> <span data-ttu-id="49397-155">新しいオブジェクトのリレーションシップを設定するとすぐに完全に同期が必要な場合は次の methods.\* のいずれかを使用します。</span><span class="sxs-lookup"><span data-stu-id="49397-155">If you must have new objects fully synchronized as soon as you set the relationship, use one of the following methods.\*</span></span>

- <span data-ttu-id="49397-156">新しいオブジェクトをナビゲーション プロパティに割り当てる。</span><span class="sxs-lookup"><span data-stu-id="49397-156">By assigning a new object to a navigation property.</span></span> <span data-ttu-id="49397-157">次のコードは、コースの間のリレーションシップを作成し、`department`します。</span><span class="sxs-lookup"><span data-stu-id="49397-157">The following code creates a relationship between a course and a `department`.</span></span> <span data-ttu-id="49397-158">オブジェクトがコンテキストにアタッチされている場合、`course`にも追加、`department.Courses`のコレクション、および、対応する外部キー プロパティ、`course`オブジェクトが、部門のキー プロパティの値に設定します。</span><span class="sxs-lookup"><span data-stu-id="49397-158">If the objects are attached to the context, the `course` is also added to the `department.Courses` collection, and the corresponding foreign key property on the `course` object is set to the key property value of the department.</span></span>  
  ``` csharp
  course.Department = department;
  ```

- <span data-ttu-id="49397-159">リレーションシップを削除するにナビゲーション プロパティを設定します。`null`します。</span><span class="sxs-lookup"><span data-stu-id="49397-159">To delete the relationship, set the navigation property to `null`.</span></span> <span data-ttu-id="49397-160">.NET 4.0 に基づいている Entity Framework を使用する場合は、関連 end を null に設定する前に読み込まれる必要があります。</span><span class="sxs-lookup"><span data-stu-id="49397-160">If you are working with Entity Framework that is based on .NET 4.0, then the related end needs to be loaded before you set it to null.</span></span> <span data-ttu-id="49397-161">例えば:</span><span class="sxs-lookup"><span data-stu-id="49397-161">For example:</span></span>   
  ``` csharp
  context.Entry(course).Reference(c => c.Department).Load();
  course.Department = null;
  ```

  <span data-ttu-id="49397-162">以降、.NET 4.5 に基づく、Entity Framework 5.0 で設定できますリレーションシップを null に関連 end を読み込まずに。</span><span class="sxs-lookup"><span data-stu-id="49397-162">Starting with Entity Framework 5.0, that is based on .NET 4.5, you can set the relationship to null without loading the related end.</span></span> <span data-ttu-id="49397-163">次のメソッドを使用して null には、現在の値を設定することもできます。</span><span class="sxs-lookup"><span data-stu-id="49397-163">You can also set the current value to null using the following method.</span></span>   
  ``` csharp
  context.Entry(course).Reference(c => c.Department).CurrentValue = null;
  ```

- <span data-ttu-id="49397-164">エンティティ コレクションのオブジェクトを削除または追加する。</span><span class="sxs-lookup"><span data-stu-id="49397-164">By deleting or adding an object in an entity collection.</span></span> <span data-ttu-id="49397-165">たとえば、型のオブジェクトを追加することができます`Course`を`department.Courses`コレクション。</span><span class="sxs-lookup"><span data-stu-id="49397-165">For example, you can add an object of type `Course` to the `department.Courses` collection.</span></span> <span data-ttu-id="49397-166">この操作は、特定の関係を作成します。**コース**と特定`department`します。</span><span class="sxs-lookup"><span data-stu-id="49397-166">This operation creates a relationship between a particular **course** and a particular `department`.</span></span> <span data-ttu-id="49397-167">オブジェクトがコンテキスト、部門の参照および外部キー プロパティにアタッチされている場合、**コース**を適切なオブジェクトが設定される`department`します。</span><span class="sxs-lookup"><span data-stu-id="49397-167">If the objects are attached to the context, the department reference and the foreign key property on the **course** object will be set to the appropriate `department`.</span></span>  
  ``` csharp
  department.Courses.Add(newCourse);
  ```

- <span data-ttu-id="49397-168">使用して、`ChangeRelationshipState`メソッドを 2 つのエンティティ オブジェクト間の指定されたリレーションシップの状態を変更します。</span><span class="sxs-lookup"><span data-stu-id="49397-168">By using the `ChangeRelationshipState` method to change the state of the specified relationship between two entity objects.</span></span> <span data-ttu-id="49397-169">N 層アプリケーションを使用する場合、このメソッドは最もよく使用して、*独立アソシエーション*(外部キー アソシエーションで、使用できません)。</span><span class="sxs-lookup"><span data-stu-id="49397-169">This method is most commonly used when working with N-Tier applications and an *independent association* (it cannot be used with a foreign key association).</span></span> <span data-ttu-id="49397-170">また、このメソッドを使用する必要がありますドロップダウンを`ObjectContext`次の例のようにします。</span><span class="sxs-lookup"><span data-stu-id="49397-170">Also, to use this method you must drop down to `ObjectContext`, as shown in the example below.</span></span>  
<span data-ttu-id="49397-171">次の例では、インストラクターとコースの多対多リレーションシップです。</span><span class="sxs-lookup"><span data-stu-id="49397-171">In the following example, there is a many-to-many relationship between Instructors and Courses.</span></span> <span data-ttu-id="49397-172">呼び出す、`ChangeRelationshipState`メソッドと受け渡し、`EntityState.Added`パラメーター、ことができます、 `SchoolContext` 2 つのオブジェクト間の関係が追加されたことを知る。</span><span class="sxs-lookup"><span data-stu-id="49397-172">Calling the `ChangeRelationshipState` method and passing the `EntityState.Added` parameter, lets the `SchoolContext` know that a relationship has been added between the two objects:</span></span>
  ``` csharp

  ((IObjectContextAdapter)context).ObjectContext.
    ObjectStateManager.
    ChangeRelationshipState(course, instructor, c => c.Instructor, EntityState.Added);
  ```

  <span data-ttu-id="49397-173">(追加するだけでなく) 更新する場合は注意してリレーションシップを新しく追加した後、古いリレーションシップを削除する必要があります。</span><span class="sxs-lookup"><span data-stu-id="49397-173">Note that if you are updating (not just adding) a relationship, you must delete the old relationship after adding the new one:</span></span>

  ``` csharp
  ((IObjectContextAdapter)context).ObjectContext.
    ObjectStateManager.
    ChangeRelationshipState(course, oldInstructor, c => c.Instructor, EntityState.Deleted);
  ```

## <a name="synchronizing-the-changes-between-the-foreign-keys-and-navigation-properties"></a><span data-ttu-id="49397-174">外部キーとナビゲーション プロパティの変更の同期</span><span class="sxs-lookup"><span data-stu-id="49397-174">Synchronizing the changes between the foreign keys and navigation properties</span></span>

<span data-ttu-id="49397-175">上記の方法のいずれかを使用して、コンテキストにアタッチされているオブジェクトのリレーションシップを変更すると、Entity Framework は、外部キー、参照、およびコレクションの同期を保つ必要があります。Entity Framework は、プロキシを持つ POCO エンティティを自動的にこの同期 (とも呼ばれるリレーションシップ フィックス アップ) を管理します。</span><span class="sxs-lookup"><span data-stu-id="49397-175">When you change the relationship of the objects attached to the context by using one of the methods described above, Entity Framework needs to keep foreign keys, references, and collections in sync. Entity Framework automatically manages this synchronization (also known as relationship fix-up) for the POCO entities with proxies.</span></span> <span data-ttu-id="49397-176">詳細については、次を参照してください。 [Proxies の操作](~/ef6/fundamentals/proxies.md)します。</span><span class="sxs-lookup"><span data-stu-id="49397-176">For more information, see [Working with Proxies](~/ef6/fundamentals/proxies.md).</span></span>

<span data-ttu-id="49397-177">プロキシなしの POCO エンティティを使用している場合は必ず必要がありますを**DetectChanges**コンテキストで関連オブジェクトを同期するメソッドが呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="49397-177">If you are using POCO entities without proxies, you must make sure that the **DetectChanges** method is called to synchronize the related objects in the context.</span></span> <span data-ttu-id="49397-178">次の Api が自動的にトリガーに注意してください、 **DetectChanges**呼び出します。</span><span class="sxs-lookup"><span data-stu-id="49397-178">Note, that the following APIs automatically trigger a **DetectChanges** call.</span></span>

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
-   <span data-ttu-id="49397-179">に対してクエリを LINQ を実行します。 `DbSet`</span><span class="sxs-lookup"><span data-stu-id="49397-179">Executing a LINQ query against a `DbSet`</span></span>

## <a name="loading-related-objects"></a><span data-ttu-id="49397-180">関連オブジェクトの読み込み</span><span class="sxs-lookup"><span data-stu-id="49397-180">Loading related objects</span></span>

<span data-ttu-id="49397-181">最もよく使用する Entity Framework では、ナビゲーション プロパティを使用して、定義されたアソシエーションで返されるエンティティに関連するエンティティを読み込みます。</span><span class="sxs-lookup"><span data-stu-id="49397-181">In Entity Framework you use most commonly use the navigation properties to load entities that are related to the returned entity by the defined association.</span></span> <span data-ttu-id="49397-182">詳細については、次を参照してください。[関連オブジェクトの読み込み](~/ef6/querying/related-data.md)します。</span><span class="sxs-lookup"><span data-stu-id="49397-182">For more information, see [Loading Related Objects](~/ef6/querying/related-data.md).</span></span>

> [!NOTE]
> <span data-ttu-id="49397-183">外部キー アソシエーションで依存オブジェクトの関連 End を読み込むと、現在メモリ内にある依存の外部キー値に基づいて関連オブジェクトが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="49397-183">In a foreign key association, when you load a related end of a dependent object, the related object will be loaded based on the foreign key value of the dependent that is currently in memory:</span></span>

``` csharp
    // Get the course where currently DepartmentID = 1.
    Course course2 = context.Courses.First(c=>c.DepartmentID == 2);

    // Use DepartmentID foreign key property
    // to change the association.
    course2.DepartmentID = 3;

    // Load the related Department where DepartmentID = 3
    context.Entry(course).Reference(c => c.Department).Load();
```

<span data-ttu-id="49397-184">独立アソシエーションの場合、依存オブジェクトの関連 End は現在データベース内にある外部キー値に基づいてクエリが行われます。</span><span class="sxs-lookup"><span data-stu-id="49397-184">In an independent association, the related end of a dependent object is queried based on the foreign key value that is currently in the database.</span></span> <span data-ttu-id="49397-185">ただし、リレーションシップが変更されると、Entity Framework、オブジェクト コンテキストに読み込まれる別のプリンシパル オブジェクトに依存するオブジェクトのポイント参照プロパティはしようとするととしてのリレーションシップを作成、クライアントで定義されます。</span><span class="sxs-lookup"><span data-stu-id="49397-185">However, if the relationship was modified, and the reference property on the dependent object points to a different principal object that is loaded in the object context, Entity Framework will try to create a relationship as it is defined on the client.</span></span>

## <a name="managing-concurrency"></a><span data-ttu-id="49397-186">同時実行の管理</span><span class="sxs-lookup"><span data-stu-id="49397-186">Managing concurrency</span></span>

<span data-ttu-id="49397-187">外部キーと独立アソシエーションの両方では、同時実行制御チェックは、エンティティ キーと、モデルで定義されている他のエンティティ プロパティに基づいています。</span><span class="sxs-lookup"><span data-stu-id="49397-187">In both foreign key and independent associations, concurrency checks are based on the entity keys and other entity properties that are defined in the model.</span></span> <span data-ttu-id="49397-188">EF デザイナーを使用して、モデルを作成、設定、`ConcurrencyMode`属性を**固定**同時実行のプロパティをチェックすることを指定します。</span><span class="sxs-lookup"><span data-stu-id="49397-188">When using the EF Designer to create a model, set the `ConcurrencyMode` attribute to **fixed** to specify that the property should be checked for concurrency.</span></span> <span data-ttu-id="49397-189">Code First を使用してモデルを定義する場合は、使用、`ConcurrencyCheck`同時実行チェックにするプロパティの注釈。</span><span class="sxs-lookup"><span data-stu-id="49397-189">When using Code First to define a model, use the `ConcurrencyCheck` annotation on properties that you want to be checked for concurrency.</span></span> <span data-ttu-id="49397-190">Code First の使用時に使用することも、`TimeStamp`同時実行のプロパティをチェックすることを指定する注釈。</span><span class="sxs-lookup"><span data-stu-id="49397-190">When working with Code First you can also use the `TimeStamp` annotation to specify that the property should be checked for concurrency.</span></span> <span data-ttu-id="49397-191">特定のクラスでタイムスタンプ プロパティを 1 つだけことができます。</span><span class="sxs-lookup"><span data-stu-id="49397-191">You can have only one timestamp property in a given class.</span></span> <span data-ttu-id="49397-192">コードは、まず、データベースの null 非許容のフィールドにこのプロパティをマップします。</span><span class="sxs-lookup"><span data-stu-id="49397-192">Code First maps this property to a non-nullable field in the database.</span></span>

<span data-ttu-id="49397-193">同時実行チェックと解決に参加するエンティティを使用する場合、外部キー アソシエーションを常に使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="49397-193">We recommend that you always use the foreign key association when working with entities that participate in concurrency checking and resolution.</span></span>

<span data-ttu-id="49397-194">詳細については、次を参照してください。[同時実行競合の処理](~/ef6/saving/concurrency.md)します。</span><span class="sxs-lookup"><span data-stu-id="49397-194">For more information, see [Handling Concurrency Conflicts](~/ef6/saving/concurrency.md).</span></span>

## <a name="working-with-overlapping-keys"></a><span data-ttu-id="49397-195">重複したキーの使用</span><span class="sxs-lookup"><span data-stu-id="49397-195">Working with overlapping Keys</span></span>

<span data-ttu-id="49397-196">重複するキーは、キーの一部のプロパティがエンティティの別のキーの一部でもある複合キーです。</span><span class="sxs-lookup"><span data-stu-id="49397-196">Overlapping keys are composite keys where some properties in the key are also part of another key in the entity.</span></span> <span data-ttu-id="49397-197">独立アソシエーションで重複するキーを持つことはできません。</span><span class="sxs-lookup"><span data-stu-id="49397-197">You cannot have an overlapping key in an independent association.</span></span> <span data-ttu-id="49397-198">重複するキーを持つ外部キー アソシエーションを変更する場合は、オブジェクト参照を使用するよりも外部キー値を変更することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="49397-198">To change a foreign key association that includes overlapping keys, we recommend that you modify the foreign key values instead of using the object references.</span></span>
