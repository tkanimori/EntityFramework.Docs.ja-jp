---
title: リレーションシップ、ナビゲーション プロパティ、および外部キーは、EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 8a21ae73-6d9b-4b50-838a-ec1fddffcf37
ms.openlocfilehash: 46c2d11b5704ec7ae82a423ae042b87f5efe436f
ms.sourcegitcommit: 8b42045cd21f80f425a92f5e4e9dd4972a31720b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2018
ms.locfileid: "49315660"
---
# <a name="relationships-navigation-properties-and-foreign-keys"></a>リレーションシップ、ナビゲーション プロパティ、および外部キー
このトピックでは、Entity Framework でのエンティティ間のリレーションシップの管理方法の概要を示します。 また、マップし、のリレーションシップを操作する方法のいくつかのガイダンスを提供します。

## <a name="relationships-in-ef"></a>EF でのリレーションシップ

リレーショナル データベース、テーブル間のリレーションシップ (アソシエーションとも呼ばれます) は、外部キーを通じて定義されます。 外部キー (FK) は、列または列の組み合わせを確立し、2 つのテーブル内のデータ間のリンクを強制するために使用します。 一般に、次の 3 つの種類のリレーションシップ。 一対一、一対多および多対多です。 一対多リレーションシップでは、外部キーが多のリレーションシップの側を表すテーブルで定義されます。 多対多リレーションシップでは、両方の関連テーブルから外部キーは、主キーで構成されます (結合または結合テーブルといいます)、3 番目テーブルを定義する必要があります。 一対一のリレーションシップの主キーがさらに外部キーとして機能し、いずれかのテーブルの別の外部キー列はありません。

次の図では、一対多のリレーションシップに参加している 2 つのテーブルを示します。 **コース**を含んでいるために、テーブルが依存するテーブル、 **DepartmentID**列にリンクする、**部門**テーブル。

![部門と Course テーブル](~/ef6/media/database2.png)

Entity Framework では、エンティティはアソシエーションまたはリレーションシップを使用して他のエンティティに関連付けることができます。 各リレーションシップには、エンティティ型との関係では、2 つのエンティティの種類 (1 つ、0 または 1、または多く) の多重度を示す 2 つの end が含まれています。 リレーションシップが終了するはプリンシパルの役割について説明しますが、参照に関する制約は、リレーションシップを準拠場合があります依存ロールであるとします。

ナビゲーション プロパティは、2 つのエンティティ型間のアソシエーションを移動する方法を提供します。 各オブジェクトは、参加する各リレーションシップに対してナビゲーション プロパティを持つことができます。 ナビゲーション プロパティを使用するに移動し、参照オブジェクトを返す双方向のリレーションシップを管理できます (多重度がいずれかである場合または 0 または 1) または (多重度が多くの場合) のコレクション。 できますが一方向のナビゲーションで、リレーションシップの種類の 1 つだけにし、両方ではなくナビゲーション プロパティを定義する場合。

データベースの外部キーにマップされるモデルにプロパティを含めることをお勧めします。 外部キー プロパティを含めることにより、依存オブジェクトの外部キーの値を変更してリレーションシップを作成または変更することができます。 このような種類のアソシエーションは外部キー アソシエーションと呼ばれます。 切り離されたエンティティを使用する場合は、外部キーを使用するとよりも重要なです。 なお、時に 1 対 1 または 1 対 0 の操作.1 のリレーションシップでは、別の外部キー列は、主キー プロパティが、外部キーとして機能し、モデルに含まれる常にします。

モデルでは、外部キー列が含まれていない、関連付け情報は、独立したオブジェクトとして管理されます。 リレーションシップは、外部キー プロパティではなくオブジェクト参照によって追跡されます。 アソシエーションのこの型が呼び出される、*独立アソシエーション*します。 変更する最も一般的な方法、*独立アソシエーション*は、アソシエーションに参加する各エンティティに対して生成されるナビゲーション プロパティを変更します。

モデルでは 1 つまたは両方のアソシエーションを使用するよう選択することができます。 ただし、外部キーのみを含む結合テーブルで接続する純粋な多対多リレーションシップがあれば、EF は独立アソシエーションをこのような多対多リレーションシップの管理に使用します。   

次の図は、Entity Framework デザイナーで作成された概念モデルを示しています。 モデルには、一対多のリレーションシップに参加している 2 つのエンティティが含まれています。 両方のエンティティには、ナビゲーション プロパティがあります。 **コース**は depend エンティティであり、 **DepartmentID**外部キー プロパティを定義します。

![ナビゲーション プロパティを持つテーブルを部門とコース](~/ef6/media/relationshipefdesigner.png)

次のコード スニペットでは、Code First で作成された同じモデルを示します。

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

## <a name="configuring-or-mapping-relationships"></a>構成またはマッピングのリレーションシップ

このページの残りの部分では、アクセスし、リレーションシップを使用してデータを操作する方法について説明します。 モデル内のリレーションシップをセットアップする方法については、次のページを参照してください。

-   Code First でのリレーションシップを構成するを参照してください。[データ注釈](~/ef6/modeling/code-first/data-annotations.md)と[Fluent API – リレーションシップ](~/ef6/modeling/code-first/fluent/relationships.md)します。
-   Entity Framework デザイナーを使用してリレーションシップを構成するを参照してください。 [EF Designer でリレーションシップ](~/ef6/modeling/designer/relationships.md)します。

## <a name="creating-and-modifying-relationships"></a>作成して、リレーションシップの変更

*外部キー アソシエーション*、リレーションシップを使用して、依存オブジェクトの状態を変更するときに、`EntityState.Unchanged`状態に変わります`EntityState.Modified`。 独立リレーションシップでは、リレーションシップの変更は、依存オブジェクトの状態は更新できません。

次の例では、外部キー プロパティとナビゲーション プロパティを使用して、関連するオブジェクトを関連付ける方法を示します。 外部キー アソシエーションでは、変更、作成、またはリレーションシップを変更するいずれかの方法を使用できます。 独立アソシエーションでは、外部キー プロパティは使用できません。

- で、次の例のように、外部キーのプロパティに新しい値を代入します。  
  ``` csharp
  course.DepartmentID = newCourse.DepartmentID;
  ```

- 次のコードでは、リレーションシップを削除外部キーを設定して**null**します。 外部キー プロパティを null 許容型でなければならないことに注意してください。  
  ``` csharp
  course.DepartmentID = null;
  ```

  >[!NOTE]
  > 参照が (この例では、コース オブジェクト) で、追加済み状態にある場合は、参照ナビゲーション プロパティは同期されません新しいオブジェクトのキー値を持つ SaveChanges が呼び出されるまで。 同期が行われない理由は、追加されたオブジェクトが保存されるまでオブジェクト コンテキストに永久キーが含まれていないからです。 新しいオブジェクトのリレーションシップを設定するとすぐに完全に同期が必要な場合は次の methods.* のいずれかを使用します。

- 新しいオブジェクトをナビゲーション プロパティに割り当てる。 次のコードは、コースの間のリレーションシップを作成し、`department`します。 オブジェクトがコンテキストにアタッチされている場合、`course`にも追加、`department.Courses`のコレクション、および、対応する外部キー プロパティ、`course`オブジェクトが、部門のキー プロパティの値に設定します。  
  ``` csharp
  course.Department = department;
  ```

- リレーションシップを削除するにナビゲーション プロパティを設定します。`null`します。 .NET 4.0 に基づいている Entity Framework を使用する場合は、関連 end を null に設定する前に読み込まれる必要があります。 例えば:   
  ``` csharp
  context.Entry(course).Reference(c => c.Department).Load();
  course.Department = null;
  ```

  以降、.NET 4.5 に基づく、Entity Framework 5.0 で設定できますリレーションシップを null に関連 end を読み込まずに。 次のメソッドを使用して null には、現在の値を設定することもできます。   
  ``` csharp
  context.Entry(course).Reference(c => c.Department).CurrentValue = null;
  ```

- エンティティ コレクションのオブジェクトを削除または追加する。 たとえば、型のオブジェクトを追加することができます`Course`を`department.Courses`コレクション。 この操作は、特定の関係を作成します。**コース**と特定`department`します。 オブジェクトがコンテキスト、部門の参照および外部キー プロパティにアタッチされている場合、**コース**を適切なオブジェクトが設定される`department`します。  
  ``` csharp
  department.Courses.Add(newCourse);
  ```

- 使用して、`ChangeRelationshipState`メソッドを 2 つのエンティティ オブジェクト間の指定されたリレーションシップの状態を変更します。 N 層アプリケーションを使用する場合、このメソッドは最もよく使用して、*独立アソシエーション*(外部キー アソシエーションで、使用できません)。 また、このメソッドを使用する必要がありますドロップダウンを`ObjectContext`次の例のようにします。  
次の例では、インストラクターとコースの多対多リレーションシップです。 呼び出す、`ChangeRelationshipState`メソッドと受け渡し、`EntityState.Added`パラメーター、ことができます、 `SchoolContext` 2 つのオブジェクト間の関係が追加されたことを知る。
  ``` csharp

  ((IObjectContextAdapter)context).ObjectContext.
    ObjectStateManager.
    ChangeRelationshipState(course, instructor, c => c.Instructor, EntityState.Added);
  ```

  (追加するだけでなく) 更新する場合は注意してリレーションシップを新しく追加した後、古いリレーションシップを削除する必要があります。

  ``` csharp
  ((IObjectContextAdapter)context).ObjectContext.
    ObjectStateManager.
    ChangeRelationshipState(course, oldInstructor, c => c.Instructor, EntityState.Deleted);
  ```

## <a name="synchronizing-the-changes-between-the-foreign-keys-and-navigation-properties"></a>外部キーとナビゲーション プロパティの変更の同期

上記の方法のいずれかを使用して、コンテキストにアタッチされているオブジェクトのリレーションシップを変更すると、Entity Framework は、外部キー、参照、およびコレクションの同期を保つ必要があります。Entity Framework は、プロキシを持つ POCO エンティティを自動的にこの同期 (とも呼ばれるリレーションシップ フィックス アップ) を管理します。 詳細については、次を参照してください。 [Proxies の操作](~/ef6/fundamentals/proxies.md)します。

プロキシなしの POCO エンティティを使用している場合は必ず必要がありますを**DetectChanges**コンテキストで関連オブジェクトを同期するメソッドが呼び出されます。 次の Api が自動的にトリガーに注意してください、 **DetectChanges**呼び出します。

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
-   に対してクエリを LINQ を実行します。 `DbSet`

## <a name="loading-related-objects"></a>関連オブジェクトの読み込み

最もよく使用する Entity Framework では、ナビゲーション プロパティを使用して、定義されたアソシエーションで返されるエンティティに関連するエンティティを読み込みます。 詳細については、次を参照してください。[関連オブジェクトの読み込み](~/ef6/querying/related-data.md)します。

> [!NOTE]
> 外部キー アソシエーションで依存オブジェクトの関連 End を読み込むと、現在メモリ内にある依存の外部キー値に基づいて関連オブジェクトが読み込まれます。

``` csharp
    // Get the course where currently DepartmentID = 2.
    Course course2 = context.Courses.First(c=>c.DepartmentID == 2);

    // Use DepartmentID foreign key property
    // to change the association.
    course2.DepartmentID = 3;

    // Load the related Department where DepartmentID = 3
    context.Entry(course).Reference(c => c.Department).Load();
```

独立アソシエーションの場合、依存オブジェクトの関連 End は現在データベース内にある外部キー値に基づいてクエリが行われます。 ただし、リレーションシップが変更されると、Entity Framework、オブジェクト コンテキストに読み込まれる別のプリンシパル オブジェクトに依存するオブジェクトのポイント参照プロパティはしようとするととしてのリレーションシップを作成、クライアントで定義されます。

## <a name="managing-concurrency"></a>コンカレンシーの管理

外部キーと独立アソシエーションの両方では、同時実行制御チェックは、エンティティ キーと、モデルで定義されている他のエンティティ プロパティに基づいています。 EF デザイナーを使用して、モデルを作成、設定、`ConcurrencyMode`属性を**固定**同時実行のプロパティをチェックすることを指定します。 Code First を使用してモデルを定義する場合は、使用、`ConcurrencyCheck`同時実行チェックにするプロパティの注釈。 Code First の使用時に使用することも、`TimeStamp`同時実行のプロパティをチェックすることを指定する注釈。 特定のクラスでタイムスタンプ プロパティを 1 つだけことができます。 コードは、まず、データベースの null 非許容のフィールドにこのプロパティをマップします。

同時実行チェックと解決に参加するエンティティを使用する場合、外部キー アソシエーションを常に使用することをお勧めします。

詳細については、次を参照してください。[同時実行競合の処理](~/ef6/saving/concurrency.md)します。

## <a name="working-with-overlapping-keys"></a>重複したキーの使用

重複するキーは、キーの一部のプロパティがエンティティの別のキーの一部でもある複合キーです。 独立アソシエーションで重複するキーを持つことはできません。 重複するキーを持つ外部キー アソシエーションを変更する場合は、オブジェクト参照を使用するよりも外部キー値を変更することをお勧めします。
