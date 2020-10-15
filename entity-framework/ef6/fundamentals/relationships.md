---
title: リレーションシップ、ナビゲーションプロパティ、および外部キー-EF6
description: Entity Framework 6 のリレーションシップ、ナビゲーションプロパティ、および外部キー
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/fundamentals/relationships
ms.openlocfilehash: 956a24051c89d410e052ab02f073e693e1934a74
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92062972"
---
# <a name="relationships-navigation-properties-and-foreign-keys"></a><span data-ttu-id="e776e-103">リレーションシップ、ナビゲーション プロパティ、および外部キー</span><span class="sxs-lookup"><span data-stu-id="e776e-103">Relationships, navigation properties, and foreign keys</span></span>

<span data-ttu-id="e776e-104">この記事では、Entity Framework がエンティティ間のリレーションシップを管理する方法の概要について説明します。</span><span class="sxs-lookup"><span data-stu-id="e776e-104">This article gives an overview of how Entity Framework manages relationships between entities.</span></span> <span data-ttu-id="e776e-105">また、リレーションシップをマップおよび操作する方法に関するガイダンスも示します。</span><span class="sxs-lookup"><span data-stu-id="e776e-105">It also gives some guidance on how to map and manipulate relationships.</span></span>

## <a name="relationships-in-ef"></a><span data-ttu-id="e776e-106">EF のリレーションシップ</span><span class="sxs-lookup"><span data-stu-id="e776e-106">Relationships in EF</span></span>

<span data-ttu-id="e776e-107">リレーショナルデータベースでは、テーブル間のリレーションシップ (関連付けとも呼ばれます) が外部キーによって定義されます。</span><span class="sxs-lookup"><span data-stu-id="e776e-107">In relational databases, relationships (also called associations) between tables are defined through foreign keys.</span></span> <span data-ttu-id="e776e-108">外部キー (FK) は、2 つのテーブルのデータ間にリンクを確立および設定するために使用される単一の列または複数の列の組み合わせです。</span><span class="sxs-lookup"><span data-stu-id="e776e-108">A foreign key (FK) is a column or combination of columns that is used to establish and enforce a link between the data in two tables.</span></span> <span data-ttu-id="e776e-109">一般に、一対一、一対多、多対多のリレーションシップには、3種類あります。</span><span class="sxs-lookup"><span data-stu-id="e776e-109">There are generally three types of relationships: one-to-one, one-to-many, and many-to-many.</span></span> <span data-ttu-id="e776e-110">一対多のリレーションシップでは、リレーションシップの多対多を表すテーブルに外部キーが定義されます。</span><span class="sxs-lookup"><span data-stu-id="e776e-110">In a one-to-many relationship, the foreign key is defined on the table that represents the many end of the relationship.</span></span> <span data-ttu-id="e776e-111">多対多リレーションシップでは、3番目のテーブル (接合テーブルまたは結合テーブル) を定義する必要があります。主キーは、両方の関連テーブルの外部キーで構成されます。</span><span class="sxs-lookup"><span data-stu-id="e776e-111">The many-to-many relationship involves defining a third table (called a junction or join table), whose primary key is composed of the foreign keys from both related tables.</span></span> <span data-ttu-id="e776e-112">一対一のリレーションシップでは、主キーはさらに外部キーとして機能し、どちらのテーブルにも個別の外部キー列はありません。</span><span class="sxs-lookup"><span data-stu-id="e776e-112">In a one-to-one relationship, the primary key acts additionally as a foreign key and there is no separate foreign key column for either table.</span></span>

<span data-ttu-id="e776e-113">次の図は、一対多のリレーションシップに参加する2つのテーブルを示しています。</span><span class="sxs-lookup"><span data-stu-id="e776e-113">The following image shows two tables that participate in one-to-many relationship.</span></span> <span data-ttu-id="e776e-114">**Course**テーブルは、 **Department**テーブルにリンクする**DepartmentID**列が含まれているため、依存テーブルです。</span><span class="sxs-lookup"><span data-stu-id="e776e-114">The **Course** table is the dependent table because it contains the **DepartmentID** column that links it to the **Department** table.</span></span>

![学科と講座のテーブル](~/ef6/media/database2.png)

<span data-ttu-id="e776e-116">Entity Framework では、アソシエーションまたはリレーションシップを通じて、エンティティを他のエンティティに関連付けることができます。</span><span class="sxs-lookup"><span data-stu-id="e776e-116">In Entity Framework, an entity can be related to other entities through an association or relationship.</span></span> <span data-ttu-id="e776e-117">各リレーションシップには、エンティティ型を記述する2つの end と、そのリレーションシップの2つのエンティティの型の多重度 (1、0、または多) が含まれます。</span><span class="sxs-lookup"><span data-stu-id="e776e-117">Each relationship contains two ends that describe the entity type and the multiplicity of the type (one, zero-or-one, or many) for the two entities in that relationship.</span></span> <span data-ttu-id="e776e-118">リレーションシップは、リレーションシップのどちらの End がプリンシパル ロールでどちらの End が依存しているかを記した参照制約によって管理することができます。</span><span class="sxs-lookup"><span data-stu-id="e776e-118">The relationship may be governed by a referential constraint, which describes which end in the relationship is a principal role and which is a dependent role.</span></span>

<span data-ttu-id="e776e-119">ナビゲーションプロパティは、2つのエンティティ型間のアソシエーションをナビゲートする方法を提供します。</span><span class="sxs-lookup"><span data-stu-id="e776e-119">Navigation properties provide a way to navigate an association between two entity types.</span></span> <span data-ttu-id="e776e-120">各オブジェクトは、参加する各リレーションシップに対してナビゲーション プロパティを持つことができます。</span><span class="sxs-lookup"><span data-stu-id="e776e-120">Every object can have a navigation property for every relationship in which it participates.</span></span> <span data-ttu-id="e776e-121">ナビゲーションプロパティを使用すると、リレーションシップを双方向に移動して管理し、参照オブジェクト (多重度が1または0または1のいずれか) またはコレクション (多重度が多の場合) を返すことができます。</span><span class="sxs-lookup"><span data-stu-id="e776e-121">Navigation properties allow you to navigate and manage relationships in both directions, returning either a reference object (if the multiplicity is either one or zero-or-one) or a collection (if the multiplicity is many).</span></span> <span data-ttu-id="e776e-122">一方向のナビゲーションを使用することもできます。この場合は、リレーションシップに参加し、両方に含まれていない型の1つのみにナビゲーションプロパティを定義します。</span><span class="sxs-lookup"><span data-stu-id="e776e-122">You may also choose to have one-way navigation, in which case you define the navigation property on only one of the types that participates in the relationship and not on both.</span></span>

<span data-ttu-id="e776e-123">データベース内の外部キーにマップされるプロパティをモデルに含めることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="e776e-123">It is recommended to include properties in the model that map to foreign keys in the database.</span></span> <span data-ttu-id="e776e-124">外部キー プロパティを含めることにより、依存オブジェクトの外部キーの値を変更してリレーションシップを作成または変更することができます。</span><span class="sxs-lookup"><span data-stu-id="e776e-124">With foreign key properties included, you can create or change a relationship by modifying the foreign key value on a dependent object.</span></span> <span data-ttu-id="e776e-125">このような種類のアソシエーションは外部キー アソシエーションと呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="e776e-125">This kind of association is called a foreign key association.</span></span> <span data-ttu-id="e776e-126">外部キーの使用は、切断されたエンティティを操作する場合にもさらに重要です。</span><span class="sxs-lookup"><span data-stu-id="e776e-126">Using foreign keys is even more essential when working with disconnected entities.</span></span> <span data-ttu-id="e776e-127">これは、1対1または1対0の操作を行う場合に注意してください。1リレーションシップ、個別の外部キー列はありません。主キープロパティは外部キーとして機能し、常にモデルに含まれます。</span><span class="sxs-lookup"><span data-stu-id="e776e-127">Note, that when working with 1-to-1 or 1-to-0..1 relationships, there is no separate foreign key column, the primary key property acts as the foreign key and is always included in the model.</span></span>

<span data-ttu-id="e776e-128">外部キー列がモデルに含まれていない場合、関連情報は独立したオブジェクトとして管理されます。</span><span class="sxs-lookup"><span data-stu-id="e776e-128">When foreign key columns are not included in the model, the association information is managed as an independent object.</span></span> <span data-ttu-id="e776e-129">リレーションシップは、外部キープロパティではなく、オブジェクト参照によって追跡されます。</span><span class="sxs-lookup"><span data-stu-id="e776e-129">Relationships are tracked through object references instead of foreign key properties.</span></span> <span data-ttu-id="e776e-130">この種類の関連付けは、独立した *関連付け*と呼ばれます。</span><span class="sxs-lookup"><span data-stu-id="e776e-130">This type of association is called an *independent association*.</span></span> <span data-ttu-id="e776e-131">*独立アソシエーション*を変更する最も一般的な方法は、アソシエーションに参加する各エンティティに対して生成されるナビゲーションプロパティを変更することです。</span><span class="sxs-lookup"><span data-stu-id="e776e-131">The most common way to modify an *independent association* is to modify the navigation properties that are generated for each entity that participates in the association.</span></span>

<span data-ttu-id="e776e-132">モデルでは 1 つまたは両方のアソシエーションを使用するよう選択することができます。</span><span class="sxs-lookup"><span data-stu-id="e776e-132">You can choose to use one or both types of associations in your model.</span></span> <span data-ttu-id="e776e-133">ただし、外部キーのみを含む結合テーブルによって接続される純粋多対多リレーションシップがある場合、EF は、このような多対多リレーションシップを管理するために独立した関連付けを使用します。</span><span class="sxs-lookup"><span data-stu-id="e776e-133">However, if you have a pure many-to-many relationship that is connected by a join table that contains only foreign keys, the EF will use an independent association to manage such many-to-many relationship.</span></span>   

<span data-ttu-id="e776e-134">次の図は、Entity Framework Designer で作成された概念モデルを示しています。</span><span class="sxs-lookup"><span data-stu-id="e776e-134">The following image shows a conceptual model that was created with the Entity Framework Designer.</span></span> <span data-ttu-id="e776e-135">モデルには、一対多のリレーションシップに参加する2つのエンティティが含まれています。</span><span class="sxs-lookup"><span data-stu-id="e776e-135">The model contains two entities that participate in one-to-many relationship.</span></span> <span data-ttu-id="e776e-136">両方のエンティティにナビゲーションプロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="e776e-136">Both entities have navigation properties.</span></span> <span data-ttu-id="e776e-137">**Course** は依存エンティティであり、 **DepartmentID** 外部キープロパティが定義されています。</span><span class="sxs-lookup"><span data-stu-id="e776e-137">**Course** is the dependent entity and has the **DepartmentID** foreign key property defined.</span></span>

![ナビゲーションプロパティがある部門テーブルとコーステーブル](~/ef6/media/relationshipefdesigner.png)

<span data-ttu-id="e776e-139">次のコードスニペットは、Code First で作成されたのと同じモデルを示しています。</span><span class="sxs-lookup"><span data-stu-id="e776e-139">The following code snippet shows the same model that was created with Code First.</span></span>

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

## <a name="configuring-or-mapping-relationships"></a><span data-ttu-id="e776e-140">リレーションシップの構成またはマッピング</span><span class="sxs-lookup"><span data-stu-id="e776e-140">Configuring or mapping relationships</span></span>

<span data-ttu-id="e776e-141">このページの残りの部分では、リレーションシップを使用してデータにアクセスし、操作する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="e776e-141">The rest of this page covers how to access and manipulate data using relationships.</span></span> <span data-ttu-id="e776e-142">モデルでのリレーションシップの設定の詳細については、次のページを参照してください。</span><span class="sxs-lookup"><span data-stu-id="e776e-142">For information on setting up relationships in your model, see the following pages.</span></span>

-   <span data-ttu-id="e776e-143">Code First でリレーションシップを構成するには、「 [データ注釈](xref:ef6/modeling/code-first/data-annotations) 」と「 [Fluent API –リレーションシップ](xref:ef6/modeling/code-first/fluent/relationships)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e776e-143">To configure relationships in Code First, see [Data Annotations](xref:ef6/modeling/code-first/data-annotations) and [Fluent API – Relationships](xref:ef6/modeling/code-first/fluent/relationships).</span></span>
-   <span data-ttu-id="e776e-144">Entity Framework Designer を使用してリレーションシップを構成するには、「 [EF Designer とのリレーションシップ](xref:ef6/modeling/designer/relationships)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e776e-144">To configure relationships using the Entity Framework Designer, see [Relationships with the EF Designer](xref:ef6/modeling/designer/relationships).</span></span>

## <a name="creating-and-modifying-relationships"></a><span data-ttu-id="e776e-145">リレーションシップの作成と変更</span><span class="sxs-lookup"><span data-stu-id="e776e-145">Creating and modifying relationships</span></span>

<span data-ttu-id="e776e-146">*外部キーの関連付け*では、リレーションシップを変更すると、状態がの依存オブジェクトの状態が `EntityState.Unchanged` に変わり `EntityState.Modified` ます。</span><span class="sxs-lookup"><span data-stu-id="e776e-146">In a *foreign key association*, when you change the relationship, the state of a dependent object with an `EntityState.Unchanged` state changes to `EntityState.Modified`.</span></span> <span data-ttu-id="e776e-147">独立したリレーションシップでは、リレーションシップを変更しても、依存オブジェクトの状態は更新されません。</span><span class="sxs-lookup"><span data-stu-id="e776e-147">In an independent relationship, changing the relationship does not update the state of the dependent object.</span></span>

<span data-ttu-id="e776e-148">次の例では、外部キープロパティとナビゲーションプロパティを使用して関連オブジェクトを関連付ける方法を示します。</span><span class="sxs-lookup"><span data-stu-id="e776e-148">The following examples show how to use the foreign key properties and navigation properties to associate the related objects.</span></span> <span data-ttu-id="e776e-149">外部キーの関連付けでは、いずれかの方法を使用して、リレーションシップを変更、作成、または変更できます。</span><span class="sxs-lookup"><span data-stu-id="e776e-149">With foreign key associations, you can use either method to change, create, or modify relationships.</span></span> <span data-ttu-id="e776e-150">独立アソシエーションでは、外部キー プロパティは使用できません。</span><span class="sxs-lookup"><span data-stu-id="e776e-150">With independent associations, you cannot use the foreign key property.</span></span>

- <span data-ttu-id="e776e-151">次の例のように、外部キープロパティに新しい値を割り当てます。</span><span class="sxs-lookup"><span data-stu-id="e776e-151">By assigning a new value to a foreign key property, as in the following example.</span></span>  
  ``` csharp
  course.DepartmentID = newCourse.DepartmentID;
  ```

- <span data-ttu-id="e776e-152">次のコードでは、外部キーを **null**に設定してリレーションシップを削除します。</span><span class="sxs-lookup"><span data-stu-id="e776e-152">The following code removes a relationship by setting the foreign key to **null**.</span></span> <span data-ttu-id="e776e-153">外部キープロパティは null 値が許容される必要があることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="e776e-153">Note, that the foreign key property must be nullable.</span></span>  
  ``` csharp
  course.DepartmentID = null;
  ```

  >[!NOTE]
  > <span data-ttu-id="e776e-154">参照が追加された状態 (この例では、course オブジェクト) である場合、SaveChanges が呼び出されるまで、参照ナビゲーションプロパティは新しいオブジェクトのキー値と同期されません。</span><span class="sxs-lookup"><span data-stu-id="e776e-154">If the reference is in the added state (in this example, the course object), the reference navigation property will not be synchronized with the key values of a new object until SaveChanges is called.</span></span> <span data-ttu-id="e776e-155">同期が行われない理由は、追加されたオブジェクトが保存されるまでオブジェクト コンテキストに永久キーが含まれていないからです。</span><span class="sxs-lookup"><span data-stu-id="e776e-155">Synchronization does not occur because the object context does not contain permanent keys for added objects until they are saved.</span></span> <span data-ttu-id="e776e-156">リレーションシップを設定した直後に新しいオブジェクトを完全に同期する必要がある場合は、次のいずれかの方法を使用します。 \*</span><span class="sxs-lookup"><span data-stu-id="e776e-156">If you must have new objects fully synchronized as soon as you set the relationship, use one of the following methods.\*</span></span>

- <span data-ttu-id="e776e-157">新しいオブジェクトをナビゲーション プロパティに割り当てる。</span><span class="sxs-lookup"><span data-stu-id="e776e-157">By assigning a new object to a navigation property.</span></span> <span data-ttu-id="e776e-158">次のコードは、コースとの間のリレーションシップを作成し `department` ます。</span><span class="sxs-lookup"><span data-stu-id="e776e-158">The following code creates a relationship between a course and a `department`.</span></span> <span data-ttu-id="e776e-159">オブジェクトがコンテキストにアタッチされている場合は、 `course` もコレクションに追加され、 `department.Courses` オブジェクトの対応する外部キープロパティ `course` は department のキープロパティ値に設定されます。</span><span class="sxs-lookup"><span data-stu-id="e776e-159">If the objects are attached to the context, the `course` is also added to the `department.Courses` collection, and the corresponding foreign key property on the `course` object is set to the key property value of the department.</span></span>  
  ``` csharp
  course.Department = department;
  ```

- <span data-ttu-id="e776e-160">リレーションシップを削除するには、ナビゲーションプロパティをに設定 `null` します。</span><span class="sxs-lookup"><span data-stu-id="e776e-160">To delete the relationship, set the navigation property to `null`.</span></span> <span data-ttu-id="e776e-161">.NET 4.0 に基づく Entity Framework を操作している場合は、関連 end を null に設定する前に読み込む必要があります。</span><span class="sxs-lookup"><span data-stu-id="e776e-161">If you are working with Entity Framework that is based on .NET 4.0, then the related end needs to be loaded before you set it to null.</span></span> <span data-ttu-id="e776e-162">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="e776e-162">For example:</span></span>   
  ``` csharp
  context.Entry(course).Reference(c => c.Department).Load();
  course.Department = null;
  ```

  <span data-ttu-id="e776e-163">.NET 4.5 に基づく Entity Framework 5.0 以降では、関連 end を読み込まずに、リレーションシップを null に設定できます。</span><span class="sxs-lookup"><span data-stu-id="e776e-163">Starting with Entity Framework 5.0, that is based on .NET 4.5, you can set the relationship to null without loading the related end.</span></span> <span data-ttu-id="e776e-164">次のメソッドを使用して、現在の値を null に設定することもできます。</span><span class="sxs-lookup"><span data-stu-id="e776e-164">You can also set the current value to null using the following method.</span></span>   
  ``` csharp
  context.Entry(course).Reference(c => c.Department).CurrentValue = null;
  ```

- <span data-ttu-id="e776e-165">エンティティ コレクションのオブジェクトを削除または追加する。</span><span class="sxs-lookup"><span data-stu-id="e776e-165">By deleting or adding an object in an entity collection.</span></span> <span data-ttu-id="e776e-166">たとえば、型のオブジェクトをコレクションに追加でき `Course` `department.Courses` ます。</span><span class="sxs-lookup"><span data-stu-id="e776e-166">For example, you can add an object of type `Course` to the `department.Courses` collection.</span></span> <span data-ttu-id="e776e-167">この操作では、特定の **コース** と特定のの間のリレーションシップを作成し `department` ます。</span><span class="sxs-lookup"><span data-stu-id="e776e-167">This operation creates a relationship between a particular **course** and a particular `department`.</span></span> <span data-ttu-id="e776e-168">オブジェクトがコンテキストにアタッチされている場合は、 **course** オブジェクトの department 参照と外部キープロパティが適切なに設定され `department` ます。</span><span class="sxs-lookup"><span data-stu-id="e776e-168">If the objects are attached to the context, the department reference and the foreign key property on the **course** object will be set to the appropriate `department`.</span></span>  
  ``` csharp
  department.Courses.Add(newCourse);
  ```

- <span data-ttu-id="e776e-169">メソッドを使用して、 `ChangeRelationshipState` 2 つのエンティティオブジェクト間の指定したリレーションシップの状態を変更します。</span><span class="sxs-lookup"><span data-stu-id="e776e-169">By using the `ChangeRelationshipState` method to change the state of the specified relationship between two entity objects.</span></span> <span data-ttu-id="e776e-170">このメソッドは、N 層アプリケーションと独立した *関連付け* (外部キーの関連付けでは使用できません) を操作する場合に最もよく使用されます。</span><span class="sxs-lookup"><span data-stu-id="e776e-170">This method is most commonly used when working with N-Tier applications and an *independent association* (it cannot be used with a foreign key association).</span></span> <span data-ttu-id="e776e-171">また、このメソッドを使用するには、 `ObjectContext` 次の例に示すように、にドロップダウンする必要があります。</span><span class="sxs-lookup"><span data-stu-id="e776e-171">Also, to use this method you must drop down to `ObjectContext`, as shown in the example below.</span></span>  
<span data-ttu-id="e776e-172">次の例では、インストラクターとコースの間に多対多のリレーションシップがあります。</span><span class="sxs-lookup"><span data-stu-id="e776e-172">In the following example, there is a many-to-many relationship between Instructors and Courses.</span></span> <span data-ttu-id="e776e-173">メソッドを呼び出し、 `ChangeRelationshipState` `EntityState.Added` パラメーターを渡すことにより、 `SchoolContext` 2 つのオブジェクトの間にリレーションシップが追加されたことを確認できます。</span><span class="sxs-lookup"><span data-stu-id="e776e-173">Calling the `ChangeRelationshipState` method and passing the `EntityState.Added` parameter, lets the `SchoolContext` know that a relationship has been added between the two objects:</span></span>
  ``` csharp

  ((IObjectContextAdapter)context).ObjectContext.
    ObjectStateManager.
    ChangeRelationshipState(course, instructor, c => c.Instructor, EntityState.Added);
  ```

  <span data-ttu-id="e776e-174">リレーションシップを追加するだけでなく、更新する場合は、新しいリレーションシップを追加した後で古いリレーションシップを削除する必要があることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="e776e-174">Note that if you are updating (not just adding) a relationship, you must delete the old relationship after adding the new one:</span></span>

  ``` csharp
  ((IObjectContextAdapter)context).ObjectContext.
    ObjectStateManager.
    ChangeRelationshipState(course, oldInstructor, c => c.Instructor, EntityState.Deleted);
  ```

## <a name="synchronizing-the-changes-between-the-foreign-keys-and-navigation-properties"></a><span data-ttu-id="e776e-175">外部キーとナビゲーションプロパティの間の変更の同期</span><span class="sxs-lookup"><span data-stu-id="e776e-175">Synchronizing the changes between the foreign keys and navigation properties</span></span>

<span data-ttu-id="e776e-176">上記のいずれかの方法を使用してコンテキストにアタッチされているオブジェクトのリレーションシップを変更する場合、Entity Framework は外部キー、参照、およびコレクションを同期させておく必要があります。Entity Framework は、プロキシを持つ POCO エンティティについて、この同期 (リレーションシップの修正とも呼ばれます) を自動的に管理します。</span><span class="sxs-lookup"><span data-stu-id="e776e-176">When you change the relationship of the objects attached to the context by using one of the methods described above, Entity Framework needs to keep foreign keys, references, and collections in sync. Entity Framework automatically manages this synchronization (also known as relationship fix-up) for the POCO entities with proxies.</span></span> <span data-ttu-id="e776e-177">詳細については、「 [プロキシの使用](xref:ef6/fundamentals/proxies)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e776e-177">For more information, see [Working with Proxies](xref:ef6/fundamentals/proxies).</span></span>

<span data-ttu-id="e776e-178">プロキシなしで POCO エンティティを使用している場合は、コンテキスト内の関連オブジェクトを同期するために、"検出された **変更** " メソッドが呼び出されるようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="e776e-178">If you are using POCO entities without proxies, you must make sure that the **DetectChanges** method is called to synchronize the related objects in the context.</span></span> <span data-ttu-id="e776e-179">次の Api では、検出された **変更** の呼び出しが自動的にトリガーされることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="e776e-179">Note that the following APIs automatically trigger a **DetectChanges** call.</span></span>

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
-   <span data-ttu-id="e776e-180">に対する LINQ クエリの実行 `DbSet`</span><span class="sxs-lookup"><span data-stu-id="e776e-180">Executing a LINQ query against a `DbSet`</span></span>

## <a name="loading-related-objects"></a><span data-ttu-id="e776e-181">関連オブジェクトの読み込み</span><span class="sxs-lookup"><span data-stu-id="e776e-181">Loading related objects</span></span>

<span data-ttu-id="e776e-182">Entity Framework では、通常、ナビゲーションプロパティを使用して、定義されたアソシエーションによって返されるエンティティに関連するエンティティを読み込みます。</span><span class="sxs-lookup"><span data-stu-id="e776e-182">In Entity Framework you commonly use navigation properties to load entities that are related to the returned entity by the defined association.</span></span> <span data-ttu-id="e776e-183">詳しくは、「[関連オブジェクトの読み込み](xref:ef6/querying/related-data)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="e776e-183">For more information, see [Loading Related Objects](xref:ef6/querying/related-data).</span></span>

> [!NOTE]
> <span data-ttu-id="e776e-184">外部キー アソシエーションで依存オブジェクトの関連 End を読み込むと、現在メモリ内にある依存の外部キー値に基づいて関連オブジェクトが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="e776e-184">In a foreign key association, when you load a related end of a dependent object, the related object will be loaded based on the foreign key value of the dependent that is currently in memory:</span></span>

``` csharp
    // Get the course where currently DepartmentID = 2.
    Course course = context.Courses.First(c => c.DepartmentID == 2);

    // Use DepartmentID foreign key property
    // to change the association.
    course.DepartmentID = 3;

    // Load the related Department where DepartmentID = 3
    context.Entry(course).Reference(c => c.Department).Load();
```

<span data-ttu-id="e776e-185">独立アソシエーションの場合、依存オブジェクトの関連 End は現在データベース内にある外部キー値に基づいてクエリが行われます。</span><span class="sxs-lookup"><span data-stu-id="e776e-185">In an independent association, the related end of a dependent object is queried based on the foreign key value that is currently in the database.</span></span> <span data-ttu-id="e776e-186">ただし、リレーションシップが変更され、依存オブジェクトの reference プロパティがオブジェクトコンテキストに読み込まれた別のプリンシパルオブジェクトを指している場合、Entity Framework は、クライアントで定義されているようにリレーションシップを作成しようとします。</span><span class="sxs-lookup"><span data-stu-id="e776e-186">However, if the relationship was modified, and the reference property on the dependent object points to a different principal object that is loaded in the object context, Entity Framework will try to create a relationship as it is defined on the client.</span></span>

## <a name="managing-concurrency"></a><span data-ttu-id="e776e-187">コンカレンシーを管理する</span><span class="sxs-lookup"><span data-stu-id="e776e-187">Managing concurrency</span></span>

<span data-ttu-id="e776e-188">外部キーと独立アソシエーションの両方で、同時実行チェックは、モデルで定義されているエンティティキーおよびその他のエンティティプロパティに基づいて行われます。</span><span class="sxs-lookup"><span data-stu-id="e776e-188">In both foreign key and independent associations, concurrency checks are based on the entity keys and other entity properties that are defined in the model.</span></span> <span data-ttu-id="e776e-189">EF デザイナーを使用してモデルを作成する場合は、属性を fixed に設定して、 `ConcurrencyMode` プロパティの同時実行性をチェックするように指定します。 **fixed**</span><span class="sxs-lookup"><span data-stu-id="e776e-189">When using the EF Designer to create a model, set the `ConcurrencyMode` attribute to **fixed** to specify that the property should be checked for concurrency.</span></span> <span data-ttu-id="e776e-190">Code First を使用してモデルを定義する場合は、 `ConcurrencyCheck` 同時実行をチェックするプロパティに注釈を使用します。</span><span class="sxs-lookup"><span data-stu-id="e776e-190">When using Code First to define a model, use the `ConcurrencyCheck` annotation on properties that you want to be checked for concurrency.</span></span> <span data-ttu-id="e776e-191">Code First を操作するときに、注釈を使用して `TimeStamp` 、プロパティの同時実行性をチェックするように指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="e776e-191">When working with Code First you can also use the `TimeStamp` annotation to specify that the property should be checked for concurrency.</span></span> <span data-ttu-id="e776e-192">指定されたクラスで使用できるタイムスタンププロパティは1つだけです。</span><span class="sxs-lookup"><span data-stu-id="e776e-192">You can have only one timestamp property in a given class.</span></span> <span data-ttu-id="e776e-193">Code First は、このプロパティをデータベースの null 値が許容されないフィールドにマップします。</span><span class="sxs-lookup"><span data-stu-id="e776e-193">Code First maps this property to a non-nullable field in the database.</span></span>

<span data-ttu-id="e776e-194">同時実行のチェックと解決に参加するエンティティを操作する場合は、常に外部キーの関連付けを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="e776e-194">We recommend that you always use the foreign key association when working with entities that participate in concurrency checking and resolution.</span></span>

<span data-ttu-id="e776e-195">詳細については、「 [同時実行の競合の処理](xref:ef6/saving/concurrency)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e776e-195">For more information, see [Handling Concurrency Conflicts](xref:ef6/saving/concurrency).</span></span>

## <a name="working-with-overlapping-keys"></a><span data-ttu-id="e776e-196">重複するキーの操作</span><span class="sxs-lookup"><span data-stu-id="e776e-196">Working with overlapping Keys</span></span>

<span data-ttu-id="e776e-197">重複するキーは、キーの一部のプロパティがエンティティの別のキーの一部でもある複合キーです。</span><span class="sxs-lookup"><span data-stu-id="e776e-197">Overlapping keys are composite keys where some properties in the key are also part of another key in the entity.</span></span> <span data-ttu-id="e776e-198">独立アソシエーションで重複するキーを持つことはできません。</span><span class="sxs-lookup"><span data-stu-id="e776e-198">You cannot have an overlapping key in an independent association.</span></span> <span data-ttu-id="e776e-199">重複するキーを持つ外部キー アソシエーションを変更する場合は、オブジェクト参照を使用するよりも外部キー値を変更することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="e776e-199">To change a foreign key association that includes overlapping keys, we recommend that you modify the foreign key values instead of using the object references.</span></span>
