---
title: リレーションシップ、ナビゲーションプロパティ、および外部キー-EF6
description: Entity Framework 6 のリレーションシップ、ナビゲーションプロパティ、および外部キー
author: divega
ms.date: 10/23/2016
ms.assetid: 8a21ae73-6d9b-4b50-838a-ec1fddffcf37
uid: ef6/fundamentals/relationships
ms.openlocfilehash: 63349d9a81065ea4e15a5f97ef5298cb3dc67339
ms.sourcegitcommit: 7c3939504bb9da3f46bea3443638b808c04227c2
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/09/2020
ms.locfileid: "89618390"
---
# <a name="relationships-navigation-properties-and-foreign-keys"></a>リレーションシップ、ナビゲーション プロパティ、および外部キー

この記事では、Entity Framework がエンティティ間のリレーションシップを管理する方法の概要について説明します。 また、リレーションシップをマップおよび操作する方法に関するガイダンスも示します。

## <a name="relationships-in-ef"></a>EF のリレーションシップ

リレーショナルデータベースでは、テーブル間のリレーションシップ (関連付けとも呼ばれます) が外部キーによって定義されます。 外部キー (FK) は、2 つのテーブルのデータ間にリンクを確立および設定するために使用される単一の列または複数の列の組み合わせです。 一般に、一対一、一対多、多対多のリレーションシップには、3種類あります。 一対多のリレーションシップでは、リレーションシップの多対多を表すテーブルに外部キーが定義されます。 多対多リレーションシップでは、3番目のテーブル (接合テーブルまたは結合テーブル) を定義する必要があります。主キーは、両方の関連テーブルの外部キーで構成されます。 一対一のリレーションシップでは、主キーはさらに外部キーとして機能し、どちらのテーブルにも個別の外部キー列はありません。

次の図は、一対多のリレーションシップに参加する2つのテーブルを示しています。 **Course**テーブルは、 **Department**テーブルにリンクする**DepartmentID**列が含まれているため、依存テーブルです。

![学科と講座のテーブル](~/ef6/media/database2.png)

Entity Framework では、アソシエーションまたはリレーションシップを通じて、エンティティを他のエンティティに関連付けることができます。 各リレーションシップには、エンティティ型を記述する2つの end と、そのリレーションシップの2つのエンティティの型の多重度 (1、0、または多) が含まれます。 リレーションシップは、リレーションシップのどちらの End がプリンシパル ロールでどちらの End が依存しているかを記した参照制約によって管理することができます。

ナビゲーションプロパティは、2つのエンティティ型間のアソシエーションをナビゲートする方法を提供します。 各オブジェクトは、参加する各リレーションシップに対してナビゲーション プロパティを持つことができます。 ナビゲーションプロパティを使用すると、リレーションシップを双方向に移動して管理し、参照オブジェクト (多重度が1または0または1のいずれか) またはコレクション (多重度が多の場合) を返すことができます。 一方向のナビゲーションを使用することもできます。この場合は、リレーションシップに参加し、両方に含まれていない型の1つのみにナビゲーションプロパティを定義します。

データベース内の外部キーにマップされるプロパティをモデルに含めることをお勧めします。 外部キー プロパティを含めることにより、依存オブジェクトの外部キーの値を変更してリレーションシップを作成または変更することができます。 このような種類のアソシエーションは外部キー アソシエーションと呼ばれます。 外部キーの使用は、切断されたエンティティを操作する場合にもさらに重要です。 これは、1対1または1対0の操作を行う場合に注意してください。1リレーションシップ、個別の外部キー列はありません。主キープロパティは外部キーとして機能し、常にモデルに含まれます。

外部キー列がモデルに含まれていない場合、関連情報は独立したオブジェクトとして管理されます。 リレーションシップは、外部キープロパティではなく、オブジェクト参照によって追跡されます。 この種類の関連付けは、独立した *関連付け*と呼ばれます。 *独立アソシエーション*を変更する最も一般的な方法は、アソシエーションに参加する各エンティティに対して生成されるナビゲーションプロパティを変更することです。

モデルでは 1 つまたは両方のアソシエーションを使用するよう選択することができます。 ただし、外部キーのみを含む結合テーブルによって接続される純粋多対多リレーションシップがある場合、EF は、このような多対多リレーションシップを管理するために独立した関連付けを使用します。   

次の図は、Entity Framework Designer で作成された概念モデルを示しています。 モデルには、一対多のリレーションシップに参加する2つのエンティティが含まれています。 両方のエンティティにナビゲーションプロパティがあります。 **Course** は依存エンティティであり、 **DepartmentID** 外部キープロパティが定義されています。

![ナビゲーションプロパティがある部門テーブルとコーステーブル](~/ef6/media/relationshipefdesigner.png)

次のコードスニペットは、Code First で作成されたのと同じモデルを示しています。

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

## <a name="configuring-or-mapping-relationships"></a>リレーションシップの構成またはマッピング

このページの残りの部分では、リレーションシップを使用してデータにアクセスし、操作する方法について説明します。 モデルでのリレーションシップの設定の詳細については、次のページを参照してください。

-   Code First でリレーションシップを構成するには、「 [データ注釈](xref:ef6/modeling/code-first/data-annotations) 」と「 [Fluent API –リレーションシップ](xref:ef6/modeling/code-first/fluent/relationships)」を参照してください。
-   Entity Framework Designer を使用してリレーションシップを構成するには、「 [EF Designer とのリレーションシップ](xref:ef6/modeling/designer/relationships)」を参照してください。

## <a name="creating-and-modifying-relationships"></a>リレーションシップの作成と変更

*外部キーの関連付け*では、リレーションシップを変更すると、状態がの依存オブジェクトの状態が `EntityState.Unchanged` に変わり `EntityState.Modified` ます。 独立したリレーションシップでは、リレーションシップを変更しても、依存オブジェクトの状態は更新されません。

次の例では、外部キープロパティとナビゲーションプロパティを使用して関連オブジェクトを関連付ける方法を示します。 外部キーの関連付けでは、いずれかの方法を使用して、リレーションシップを変更、作成、または変更できます。 独立アソシエーションでは、外部キー プロパティは使用できません。

- 次の例のように、外部キープロパティに新しい値を割り当てます。  
  ``` csharp
  course.DepartmentID = newCourse.DepartmentID;
  ```

- 次のコードでは、外部キーを **null**に設定してリレーションシップを削除します。 外部キープロパティは null 値が許容される必要があることに注意してください。  
  ``` csharp
  course.DepartmentID = null;
  ```

  >[!NOTE]
  > 参照が追加された状態 (この例では、course オブジェクト) である場合、SaveChanges が呼び出されるまで、参照ナビゲーションプロパティは新しいオブジェクトのキー値と同期されません。 同期が行われない理由は、追加されたオブジェクトが保存されるまでオブジェクト コンテキストに永久キーが含まれていないからです。 リレーションシップを設定した直後に新しいオブジェクトを完全に同期する必要がある場合は、次のいずれかの方法を使用します。 *

- 新しいオブジェクトをナビゲーション プロパティに割り当てる。 次のコードは、コースとの間のリレーションシップを作成し `department` ます。 オブジェクトがコンテキストにアタッチされている場合は、 `course` もコレクションに追加され、 `department.Courses` オブジェクトの対応する外部キープロパティ `course` は department のキープロパティ値に設定されます。  
  ``` csharp
  course.Department = department;
  ```

- リレーションシップを削除するには、ナビゲーションプロパティをに設定 `null` します。 .NET 4.0 に基づく Entity Framework を操作している場合は、関連 end を null に設定する前に読み込む必要があります。 次に例を示します。   
  ``` csharp
  context.Entry(course).Reference(c => c.Department).Load();
  course.Department = null;
  ```

  .NET 4.5 に基づく Entity Framework 5.0 以降では、関連 end を読み込まずに、リレーションシップを null に設定できます。 次のメソッドを使用して、現在の値を null に設定することもできます。   
  ``` csharp
  context.Entry(course).Reference(c => c.Department).CurrentValue = null;
  ```

- エンティティ コレクションのオブジェクトを削除または追加する。 たとえば、型のオブジェクトをコレクションに追加でき `Course` `department.Courses` ます。 この操作では、特定の **コース** と特定のの間のリレーションシップを作成し `department` ます。 オブジェクトがコンテキストにアタッチされている場合は、 **course** オブジェクトの department 参照と外部キープロパティが適切なに設定され `department` ます。  
  ``` csharp
  department.Courses.Add(newCourse);
  ```

- メソッドを使用して、 `ChangeRelationshipState` 2 つのエンティティオブジェクト間の指定したリレーションシップの状態を変更します。 このメソッドは、N 層アプリケーションと独立した *関連付け* (外部キーの関連付けでは使用できません) を操作する場合に最もよく使用されます。 また、このメソッドを使用するには、 `ObjectContext` 次の例に示すように、にドロップダウンする必要があります。  
次の例では、インストラクターとコースの間に多対多のリレーションシップがあります。 メソッドを呼び出し、 `ChangeRelationshipState` `EntityState.Added` パラメーターを渡すことにより、 `SchoolContext` 2 つのオブジェクトの間にリレーションシップが追加されたことを確認できます。
  ``` csharp

  ((IObjectContextAdapter)context).ObjectContext.
    ObjectStateManager.
    ChangeRelationshipState(course, instructor, c => c.Instructor, EntityState.Added);
  ```

  リレーションシップを追加するだけでなく、更新する場合は、新しいリレーションシップを追加した後で古いリレーションシップを削除する必要があることに注意してください。

  ``` csharp
  ((IObjectContextAdapter)context).ObjectContext.
    ObjectStateManager.
    ChangeRelationshipState(course, oldInstructor, c => c.Instructor, EntityState.Deleted);
  ```

## <a name="synchronizing-the-changes-between-the-foreign-keys-and-navigation-properties"></a>外部キーとナビゲーションプロパティの間の変更の同期

上記のいずれかの方法を使用してコンテキストにアタッチされているオブジェクトのリレーションシップを変更する場合、Entity Framework は外部キー、参照、およびコレクションを同期させておく必要があります。Entity Framework は、プロキシを持つ POCO エンティティについて、この同期 (リレーションシップの修正とも呼ばれます) を自動的に管理します。 詳細については、「 [プロキシの使用](xref:ef6/fundamentals/proxies)」を参照してください。

プロキシなしで POCO エンティティを使用している場合は、コンテキスト内の関連オブジェクトを同期するために、"検出された **変更** " メソッドが呼び出されるようにする必要があります。 次の Api では、検出された **変更** の呼び出しが自動的にトリガーされることに注意してください。

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
-   に対する LINQ クエリの実行 `DbSet`

## <a name="loading-related-objects"></a>関連オブジェクトの読み込み

Entity Framework では、通常、ナビゲーションプロパティを使用して、定義されたアソシエーションによって返されるエンティティに関連するエンティティを読み込みます。 詳しくは、「[関連オブジェクトの読み込み](xref:ef6/querying/related-data)」をご覧ください。

> [!NOTE]
> 外部キー アソシエーションで依存オブジェクトの関連 End を読み込むと、現在メモリ内にある依存の外部キー値に基づいて関連オブジェクトが読み込まれます。

``` csharp
    // Get the course where currently DepartmentID = 2.
    Course course = context.Courses.First(c => c.DepartmentID == 2);

    // Use DepartmentID foreign key property
    // to change the association.
    course.DepartmentID = 3;

    // Load the related Department where DepartmentID = 3
    context.Entry(course).Reference(c => c.Department).Load();
```

独立アソシエーションの場合、依存オブジェクトの関連 End は現在データベース内にある外部キー値に基づいてクエリが行われます。 ただし、リレーションシップが変更され、依存オブジェクトの reference プロパティがオブジェクトコンテキストに読み込まれた別のプリンシパルオブジェクトを指している場合、Entity Framework は、クライアントで定義されているようにリレーションシップを作成しようとします。

## <a name="managing-concurrency"></a>コンカレンシーを管理する

外部キーと独立アソシエーションの両方で、同時実行チェックは、モデルで定義されているエンティティキーおよびその他のエンティティプロパティに基づいて行われます。 EF デザイナーを使用してモデルを作成する場合は、属性を fixed に設定して、 `ConcurrencyMode` プロパティの同時実行性をチェックするように指定します。 **fixed** Code First を使用してモデルを定義する場合は、 `ConcurrencyCheck` 同時実行をチェックするプロパティに注釈を使用します。 Code First を操作するときに、注釈を使用して `TimeStamp` 、プロパティの同時実行性をチェックするように指定することもできます。 指定されたクラスで使用できるタイムスタンププロパティは1つだけです。 Code First は、このプロパティをデータベースの null 値が許容されないフィールドにマップします。

同時実行のチェックと解決に参加するエンティティを操作する場合は、常に外部キーの関連付けを使用することをお勧めします。

詳細については、「 [同時実行の競合の処理](xref:ef6/saving/concurrency)」を参照してください。

## <a name="working-with-overlapping-keys"></a>重複するキーの操作

重複するキーは、キーの一部のプロパティがエンティティの別のキーの一部でもある複合キーです。 独立アソシエーションで重複するキーを持つことはできません。 重複するキーを持つ外部キー アソシエーションを変更する場合は、オブジェクト参照を使用するよりも外部キー値を変更することをお勧めします。
