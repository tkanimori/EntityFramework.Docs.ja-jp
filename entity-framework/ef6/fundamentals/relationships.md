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
# <a name="relationships-navigation-properties-and-foreign-keys"></a>リレーションシップ、ナビゲーション プロパティ、および外部キー

この記事では、エンティティ フレームワークがエンティティ間の関係を管理する方法の概要を示します。 また、リレーションシップのマッピング方法と操作方法に関するガイダンスも示します。

## <a name="relationships-in-ef"></a>EF のリレーションシップ

リレーショナル・データベースでは、テーブル間の関係 (関連とも呼ばれる) は、外部キーを通じて定義されます。 外部キー (FK) は、2 つのテーブルのデータ間にリンクを確立および設定するために使用される単一の列または複数の列の組み合わせです。 一般的には、1 対 1、1 対多、および多対多の 3 種類のリレーションシップがあります。 一対多リレーションシップでは、外部キーはリレーションシップの多端を表すテーブルに定義されます。 多対多リレーションシップでは、3 つ目のテーブル (ジャンクションテーブルまたは結合テーブルと呼ばれます) を定義し、その主キーは両方の関連テーブルの外部キーで構成されます。 一対一リレーションシップでは、主キーは外部キーとしてさらに機能し、どちらのテーブルにも別の外部キー列はありません。

次の図は、一対多リレーションシップに参加する 2 つのテーブルを示しています。 **コース**テーブルには、部門テーブルにリンクする**部門ID**列が含まれているため、**依存**テーブルです。

![部門とコーステーブル](~/ef6/media/database2.png)

エンティティ フレームワークでは、エンティティは、関連付けまたは関連付けを介して他のエンティティに関連付けることができます。 各リレーションシップには、エンティティ型とそのリレーションシップの 2 つのエンティティの型 (1、0 または 1、または多) の多重度を記述する 2 つの端が含まれています。 リレーションシップは、リレーションシップのどちらの End がプリンシパル ロールでどちらの End が依存しているかを記した参照制約によって管理することができます。

ナビゲーション プロパティは、2 つのエンティティ型間の関連付けをナビゲートする方法を提供します。 各オブジェクトは、参加する各リレーションシップに対してナビゲーション プロパティを持つことができます。 ナビゲーション プロパティを使用すると、参照オブジェクト (多重度が 1 または 0 または 1 の場合) またはコレクション (多重度が多い場合) を返して、両方向のリレーションシップをナビゲートおよび管理できます。 一方向ナビゲーションを使用することもできますが、その場合は、リレーションシップに参加する 1 つの型にのみナビゲーション プロパティを定義し、両方に定義することはできません。

データベースの外部キーにマップするプロパティをモデルに含めるのが推奨されます。 外部キー プロパティを含めることにより、依存オブジェクトの外部キーの値を変更してリレーションシップを作成または変更することができます。 このような種類のアソシエーションは外部キー アソシエーションと呼ばれます。 外部キーの使用は、切断されたエンティティを操作する場合にはさらに重要です。 1 対 1 または 1 対 0 で作業する場合に注意してください。1 つのリレーションシップ、個別の外部キー列がない場合、主キー プロパティは外部キーとして機能し、常にモデルに含まれます。

外部キー列がモデルに含まれていない場合、関連付け情報は独立したオブジェクトとして管理されます。 リレーションシップは、外部キー プロパティではなく、オブジェクト参照を通じて追跡されます。 このタイプの関連付けを、*独立アソシエーション*と呼ばれます。 *独立した関連付*けを変更する最も一般的な方法は、関連付けに参加する各エンティティに対して生成されるナビゲーション プロパティを変更することです。

モデルでは 1 つまたは両方のアソシエーションを使用するよう選択することができます。 ただし、外部キーのみを含む結合テーブルによって接続される純粋な多対多リレーションシップがある場合、EF は、このような多対多リレーションシップを管理するために独立した関連付けを使用します。   

次の図は、Entity Framework デザイナーで作成された概念モデルを示しています。 モデルには、一対多の関係に参加する 2 つのエンティティが含まれています。 両方のエンティティにはナビゲーション プロパティがあります。 **コース**は依存エンティティであり、部門**ID**外部キー プロパティが定義されています。

![ナビゲーション プロパティを持つ部門テーブルとコース テーブル](~/ef6/media/relationshipefdesigner.png)

次のコード スニペットは、Code First で作成されたモデルと同じモデルを示しています。

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

## <a name="configuring-or-mapping-relationships"></a>関係の構成またはマッピング

このページの残りの部分では、リレーションシップを使用してデータにアクセスして操作する方法について説明します。 モデルでのリレーションシップの設定については、次のページを参照してください。

-   コードファーストで関係を設定するには、「[データ注釈](~/ef6/modeling/code-first/data-annotations.md)と[Fluent API – リレーションシップ](~/ef6/modeling/code-first/fluent/relationships.md)」を参照してください。
-   エンティティ フレームワーク デザイナーを使用してリレーションシップを構成するには[、「EF デザイナーとの関係」](~/ef6/modeling/designer/relationships.md)を参照してください。

## <a name="creating-and-modifying-relationships"></a>リレーションシップの作成と変更

*外部キーアソシエーション*では、リレーションシップを変更すると、状態を持つ依存オブジェクトの状態が`EntityState.Unchanged`に変更`EntityState.Modified`されます。 独立した関係では、関係を変更しても、依存オブジェクトの状態は更新されません。

次の例は、外部キー プロパティとナビゲーション プロパティを使用して関連オブジェクトを関連付ける方法を示しています。 外部キーの関連付けを使用すると、リレーションシップの変更、作成、または変更を行うことができます。 独立アソシエーションでは、外部キー プロパティは使用できません。

- 次の例のように、外部キー プロパティに新しい値を割り当てます。  
  ``` csharp
  course.DepartmentID = newCourse.DepartmentID;
  ```

- 次のコードは、外部キーを**null**に設定してリレーションシップを削除します。 外部キー プロパティは null 許容である必要があります。  
  ``` csharp
  course.DepartmentID = null;
  ```

  >[!NOTE]
  > 参照が追加された状態 (この例ではコース オブジェクト) の場合、参照ナビゲーション プロパティは SaveChanges が呼び出されるまで、新しいオブジェクトのキー値と同期されません。 同期が行われない理由は、追加されたオブジェクトが保存されるまでオブジェクト コンテキストに永久キーが含まれていないからです。 リレーションシップを設定した直後に新しいオブジェクトを完全に同期する必要がある場合は、次のいずれかの方法を使用します。

- 新しいオブジェクトをナビゲーション プロパティに割り当てる。 次のコードは、コースと`department`. オブジェクトがコンテキストにアタッチされている場合`course`は、 も`department.Courses`コレクションに追加され、`course`オブジェクトの対応する外部キー プロパティは部門のキー プロパティ値に設定されます。  
  ``` csharp
  course.Department = department;
  ```

- リレーションシップを削除するには、ナビゲーション プロパティを`null`に設定します。 .NET 4.0 に基づく Entity Framework を使用している場合は、関連する末尾を読み込んでから null に設定する必要があります。 次に例を示します。   
  ``` csharp
  context.Entry(course).Reference(c => c.Department).Load();
  course.Department = null;
  ```

  .NET 4.5 に基づく Entity Framework 5.0 以降では、関連する End を読み込まずにリレーションシップを null に設定できます。 次の方法を使用して、現在の値を null に設定することもできます。   
  ``` csharp
  context.Entry(course).Reference(c => c.Department).CurrentValue = null;
  ```

- エンティティ コレクションのオブジェクトを削除または追加する。 たとえば、型`Course`のオブジェクトをコレクションに`department.Courses`追加できます。 この操作により、特定の**コース**と の間に`department`関係が作成されます。 オブジェクトがコンテキストにアタッチされている場合、**コース**オブジェクトの部門参照と外部キープロパティが適切な`department`に設定されます。  
  ``` csharp
  department.Courses.Add(newCourse);
  ```

- `ChangeRelationshipState` 2 つのエンティティ オブジェクト間の指定されたリレーションシップの状態を変更するメソッドを使用します。 この方法は、N 層アプリケーションおよび*独立した関連付け*(外部キーの関連付けでは使用できません) を使用する場合に最も一般的に使用されます。 また、このメソッドを使用するには、次の例`ObjectContext`に示すように、 にドロップダウンする必要があります。  
次の例では、インストラクタとコースの間に多対多の関係があります。 メソッドを`ChangeRelationshipState`呼び出して`EntityState.Added`パラメータを渡すと`SchoolContext`、2 つのオブジェクト間にリレーションシップが追加されたことを知ることができます。
  ``` csharp

  ((IObjectContextAdapter)context).ObjectContext.
    ObjectStateManager.
    ChangeRelationshipState(course, instructor, c => c.Instructor, EntityState.Added);
  ```

  リレーションシップを更新する場合 (単に追加するだけではなく)、新しいリレーションシップを追加した後で古いリレーションシップを削除する必要があります。

  ``` csharp
  ((IObjectContextAdapter)context).ObjectContext.
    ObjectStateManager.
    ChangeRelationshipState(course, oldInstructor, c => c.Instructor, EntityState.Deleted);
  ```

## <a name="synchronizing-the-changes-between-the-foreign-keys-and-navigation-properties"></a>外部キーとナビゲーション プロパティの間の変更の同期

上記のいずれかのメソッドを使用してコンテキストにアタッチされたオブジェクトのリレーションシップを変更する場合、Entity Framework は、外部キー、参照、およびコレクションを同期する必要があります。Entity Framework は、プロキシを持つ POCO エンティティのこの同期 (関連解決とも呼ばれます) を自動的に管理します。 詳細については、「[プロキシの操作](~/ef6/fundamentals/proxies.md)」を参照してください。

プロキシを使用せずに POCO エンティティを使用している場合は、コンテキスト内の関連オブジェクトを同期するために**DetectChanges**メソッドが呼び出されていることを確認する必要があります。 次の API は、自動的に**DetectChanges**呼び出しをトリガーします。

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
-   に対する LINQ クエリの実行`DbSet`

## <a name="loading-related-objects"></a>関連オブジェクトの読み込み

Entity Framework では、一般的に、定義された関連付けによって返されるエンティティに関連するエンティティを読み込むためにナビゲーション プロパティを使用します。 詳細については、「[関連オブジェクトのロード](~/ef6/querying/related-data.md)」を参照してください。

> [!NOTE]
> 外部キー アソシエーションで依存オブジェクトの関連 End を読み込むと、現在メモリ内にある依存の外部キー値に基づいて関連オブジェクトが読み込まれます。

``` csharp
    // Get the course where currently DepartmentID = 2.
    Course course2 = context.Courses.First(c => c.DepartmentID == 2);

    // Use DepartmentID foreign key property
    // to change the association.
    course2.DepartmentID = 3;

    // Load the related Department where DepartmentID = 3
    context.Entry(course).Reference(c => c.Department).Load();
```

独立アソシエーションの場合、依存オブジェクトの関連 End は現在データベース内にある外部キー値に基づいてクエリが行われます。 ただし、リレーションシップが変更され、依存オブジェクトの参照プロパティが、オブジェクト コンテキストに読み込まれる別のプリンシパル オブジェクトを指している場合、Entity Framework はクライアントで定義されている関連付けを作成しようとします。

## <a name="managing-concurrency"></a>コンカレンシーを管理する

外部キーと独立した関連付けの両方で、同時実行チェックは、モデルで定義されているエンティティ キーおよびその他のエンティティ プロパティに基づきます。 EF Designer を使用してモデルを作成する場合`ConcurrencyMode`は、属性を**fixed**に設定して、プロパティの同時実行性をチェックするように指定します。 Code First を使用してモデルを定義する`ConcurrencyCheck`場合は、同時実行性をチェックするプロパティに対して注釈を使用します。 Code First を使用する場合は、`TimeStamp`アノテーションを使用して、プロパティの同時実行性をチェックするように指定することもできます。 指定したクラスに含めることができるタイムスタンプ プロパティは 1 つだけです。 コード First は、このプロパティをデータベース内の null 非許容フィールドにマップします。

同時実行のチェックと解決に参加するエンティティを操作するときは、常に外部キーの関連付けを使用することをお勧めします。

詳細については、「[同時実行の競合の処理](~/ef6/saving/concurrency.md)」を参照してください。

## <a name="working-with-overlapping-keys"></a>重なり合うキーの操作

重複するキーは、キーの一部のプロパティがエンティティの別のキーの一部でもある複合キーです。 独立アソシエーションで重複するキーを持つことはできません。 重複するキーを持つ外部キー アソシエーションを変更する場合は、オブジェクト参照を使用するよりも外部キー値を変更することをお勧めします。
