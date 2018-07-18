---
title: 最初のコード挿入、更新、および Delete ストアド プロシージャは、EF6
author: divega
ms.date: 2016-10-23
ms.prod: entity-framework
ms.author: divega
ms.manager: avickers
ms.technology: entity-framework-6
ms.topic: article
ms.assetid: 9a7ae7f9-4072-4843-877d-506dd7eef576
caps.latest.revision: 3
ms.openlocfilehash: 1f100ed888abd98df83c80d0de2086cfb1ba7b4f
ms.sourcegitcommit: bdd06c9a591ba5e6d6a3ec046c80de98f598f3f3
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2018
ms.locfileid: "39122749"
---
# <a name="code-first-insert-update-and-delete-stored-procedures"></a><span data-ttu-id="9fb91-102">最初のコード挿入、更新、および Delete ストアド プロシージャ</span><span class="sxs-lookup"><span data-stu-id="9fb91-102">Code First Insert, Update, and Delete Stored Procedures</span></span>
> [!NOTE]
> <span data-ttu-id="9fb91-103">**EF6 以降のみ** - このページで説明する機能、API などは、Entity Framework 6 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="9fb91-103">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="9fb91-104">以前のバージョンを使用している場合、一部またはすべての情報は適用されません。</span><span class="sxs-lookup"><span data-stu-id="9fb91-104">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="9fb91-105">既定では、Code First は構成すべてのエンティティの挿入を実行、更新、およびテーブルに直接アクセスを使用してコマンドを削除します。</span><span class="sxs-lookup"><span data-stu-id="9fb91-105">By default, Code First will configure all entities to perform insert, update and delete commands using direct table access.</span></span> <span data-ttu-id="9fb91-106">EF6 で起動するストアド プロシージャを使用して、モデルの一部またはすべてのエンティティの Code First モデルを構成できます。</span><span class="sxs-lookup"><span data-stu-id="9fb91-106">Starting in EF6 you can configure your Code First model to use stored procedures for some or all entities in your model.</span></span>  

## <a name="basic-entity-mapping"></a><span data-ttu-id="9fb91-107">基本的なエンティティのマッピング</span><span class="sxs-lookup"><span data-stu-id="9fb91-107">Basic Entity Mapping</span></span>  

<span data-ttu-id="9fb91-108">Insert ストアド プロシージャの使用を選択し、更新および Fluent API を使用して削除できます。</span><span class="sxs-lookup"><span data-stu-id="9fb91-108">You can opt into using stored procedures for insert, update and delete using the Fluent API.</span></span>  

``` csharp
modelBuilder
  .Entity<Blog>()
  .MapToStoredProcedures();
```  

<span data-ttu-id="9fb91-109">これを行うには、いくつかの規則を使用して、データベースのストアド プロシージャの予想される形状を構築する Code First が発生します。</span><span class="sxs-lookup"><span data-stu-id="9fb91-109">Doing this will cause Code First to use some conventions to build the expected shape of the stored procedures in the database.</span></span>  

- <span data-ttu-id="9fb91-110">3 つのストアド プロシージャの名前付き **\<type_name\>(_i)**、  **\<type_name\>更新 (_u)** と**\<種類名前\>削除 (_d)** (Blog_Insert、Blog_Update および Blog_Delete など)。</span><span class="sxs-lookup"><span data-stu-id="9fb91-110">Three stored procedures named **\<type_name\>_Insert**, **\<type_name\>_Update** and **\<type_name\>_Delete** (for example, Blog_Insert, Blog_Update and Blog_Delete).</span></span>  
- <span data-ttu-id="9fb91-111">パラメーター名は、プロパティ名に対応します。</span><span class="sxs-lookup"><span data-stu-id="9fb91-111">Parameter names correspond to the property names.</span></span>  
  > [!NOTE]
  > <span data-ttu-id="9fb91-112">特定のプロパティの列の名前を変更する HasColumnName() または列の属性を使用する場合、この名前は、プロパティ名の代わりにパラメーターに使用されます。</span><span class="sxs-lookup"><span data-stu-id="9fb91-112">If you use HasColumnName() or the Column attribute to rename the column for a given property then this name is used for parameters instead of the property name.</span></span>  
- <span data-ttu-id="9fb91-113">**Insert ストアド プロシージャ**ストア生成としてマークされているものを除く、すべてのプロパティのパラメーターになります (identity または計算)。</span><span class="sxs-lookup"><span data-stu-id="9fb91-113">**The insert stored procedure** will have a parameter for every property, except for those marked as store generated (identity or computed).</span></span> <span data-ttu-id="9fb91-114">ストアド プロシージャは、生成されたストアの各プロパティの列を含む結果セットを返す必要があります。</span><span class="sxs-lookup"><span data-stu-id="9fb91-114">The stored procedure should return a result set with a column for each store generated property.</span></span>  
- <span data-ttu-id="9fb91-115">**更新ストアド プロシージャ**'Computed' のストア生成パターンが付いているものを除く、すべてのプロパティのパラメーターになります。</span><span class="sxs-lookup"><span data-stu-id="9fb91-115">**The update stored procedure** will have a parameter for every property, except for those marked with a store generated pattern of 'Computed'.</span></span> <span data-ttu-id="9fb91-116">一部の同時実行トークンは、元の値のパラメーターが必要を参照してください、*同時実行制御トークン*詳細については後述します。</span><span class="sxs-lookup"><span data-stu-id="9fb91-116">Some concurrency tokens require a parameter for the original value, see the *Concurrency Tokens* section below for details.</span></span> <span data-ttu-id="9fb91-117">ストアド プロシージャは、計算の各プロパティの列を含む結果セットを返す必要があります。</span><span class="sxs-lookup"><span data-stu-id="9fb91-117">The stored procedure should return a result set with a column for each computed property.</span></span>  
- <span data-ttu-id="9fb91-118">**ストアド プロシージャを削除**エンティティ (または複数のパラメーター エンティティが複合キーを持つ場合) のキー値のパラメーターが必要です。</span><span class="sxs-lookup"><span data-stu-id="9fb91-118">**The delete stored procedure** should have a parameter for the key value of the entity (or multiple parameters if the entity has a composite key).</span></span> <span data-ttu-id="9fb91-119">さらに、delete プロシージャも必要な独立アソシエーションの外部キーのパラメーター (エンティティで宣言されている、対応する外部キー プロパティがないリレーションシップ) 対象のテーブルです。</span><span class="sxs-lookup"><span data-stu-id="9fb91-119">Additionally, the delete procedure should also have parameters for any independent association foreign keys on the target table (relationships that do not have corresponding foreign key properties declared in the entity).</span></span> <span data-ttu-id="9fb91-120">一部の同時実行トークンは、元の値のパラメーターが必要を参照してください、*同時実行制御トークン*詳細については後述します。</span><span class="sxs-lookup"><span data-stu-id="9fb91-120">Some concurrency tokens require a parameter for the original value, see the *Concurrency Tokens* section below for details.</span></span>  

<span data-ttu-id="9fb91-121">例として、次のクラスの使用。</span><span class="sxs-lookup"><span data-stu-id="9fb91-121">Using the following class as an example:</span></span>  

``` csharp
public class Blog  
{  
  public int BlogId { get; set; }  
  public string Name { get; set; }  
  public string Url { get; set; }  
}
```  

<span data-ttu-id="9fb91-122">格納されている既定のプロシージャは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="9fb91-122">The default stored procedures would be:</span></span>  

``` SQL
CREATE PROCEDURE [dbo].[Blog_Insert]  
  @Name nvarchar(max),  
  @Url nvarchar(max)  
AS  
BEGIN
  INSERT INTO [dbo].[Blogs] ([Name], [Url])
  VALUES (@Name, @Url)

  SELECT SCOPE_IDENTITY() AS BlogId
END
CREATE PROCEDURE [dbo].[Blog_Update]  
  @BlogId int,  
  @Name nvarchar(max),  
  @Url nvarchar(max)  
AS  
  UPDATE [dbo].[Blogs]
  SET [Name] = @Name, [Url] = @Url     
  WHERE BlogId = @BlogId;
CREATE PROCEDURE [dbo].[Blog_Delete]  
  @BlogId int  
AS  
  DELETE FROM [dbo].[Blogs]
  WHERE BlogId = @BlogId
```  

### <a name="overriding-the-defaults"></a><span data-ttu-id="9fb91-123">既定の設定をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="9fb91-123">Overriding the Defaults</span></span>  

<span data-ttu-id="9fb91-124">既定で構成された内容の一部またはすべてをオーバーライドすることができます。</span><span class="sxs-lookup"><span data-stu-id="9fb91-124">You can override part or all of what was configured by default.</span></span>  

<span data-ttu-id="9fb91-125">1 つまたは複数のストアド プロシージャの名前を変更することができます。</span><span class="sxs-lookup"><span data-stu-id="9fb91-125">You can change the name of one or more stored procedures.</span></span> <span data-ttu-id="9fb91-126">この例では、更新ストアド プロシージャのみを変更します。</span><span class="sxs-lookup"><span data-stu-id="9fb91-126">This example renames the update stored procedure only.</span></span>  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.HasName("modify_blog")));
```  

<span data-ttu-id="9fb91-127">この例では、次の 3 つすべてのストアド プロシージャを変更します。</span><span class="sxs-lookup"><span data-stu-id="9fb91-127">This example renames all three stored procedures.</span></span>  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.HasName("modify_blog"))  
     .Delete(d => d.HasName("delete_blog"))  
     .Insert(i => i.HasName("insert_blog")));
```  

<span data-ttu-id="9fb91-128">これらの例で、呼び出しは、連結しますが、ラムダのブロックの構文を使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="9fb91-128">In these examples the calls are chained together, but you can also use lambda block syntax.</span></span>  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    {  
      s.Update(u => u.HasName("modify_blog"));  
      s.Delete(d => d.HasName("delete_blog"));  
      s.Insert(i => i.HasName("insert_blog"));  
    });
```  

<span data-ttu-id="9fb91-129">この例では、BlogId プロパティ更新ストアド プロシージャのパラメーターを変更します。</span><span class="sxs-lookup"><span data-stu-id="9fb91-129">This example renames the parameter for the BlogId property on the update stored procedure.</span></span>  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.Parameter(b => b.BlogId, "blog_id")));
```  

<span data-ttu-id="9fb91-130">これらの呼び出しは、すべてのチェーンで構成可能なは。</span><span class="sxs-lookup"><span data-stu-id="9fb91-130">These calls are all chainable and composable.</span></span> <span data-ttu-id="9fb91-131">次の 3 つすべてのストアド プロシージャとそのパラメーターの名前を変更する例を示します。</span><span class="sxs-lookup"><span data-stu-id="9fb91-131">Here is an example that renames all three stored procedures and their parameters.</span></span>  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.HasName("modify_blog")  
                   .Parameter(b => b.BlogId, "blog_id")  
                   .Parameter(b => b.Name, "blog_name")  
                   .Parameter(b => b.Url, "blog_url"))  
     .Delete(d => d.HasName("delete_blog")  
                   .Parameter(b => b.BlogId, "blog_id"))  
     .Insert(i => i.HasName("insert_blog")  
                   .Parameter(b => b.Name, "blog_name")  
                   .Parameter(b => b.Url, "blog_url")));
```  

<span data-ttu-id="9fb91-132">データベースで生成された値を含む結果セット内の列の名前を変更することもできます。</span><span class="sxs-lookup"><span data-stu-id="9fb91-132">You can also change the name of the columns in the result set that contains database generated values.</span></span>  

``` csharp
modelBuilder
  .Entity<Blog>()
  .MapToStoredProcedures(s =>
    s.Insert(i => i.Result(b => b.BlogId, "generated_blog_identity")));
```

``` SQL
CREATE PROCEDURE [dbo].[Blog_Insert]  
  @Name nvarchar(max),  
  @Url nvarchar(max)  
AS  
BEGIN
  INSERT INTO [dbo].[Blogs] ([Name], [Url])
  VALUES (@Name, @Url)

  SELECT SCOPE_IDENTITY() AS generated_blog_id
END
```  

## <a name="relationships-without-a-foreign-key-in-the-class-independent-associations"></a><span data-ttu-id="9fb91-133">クラス (独立した関連付け) で外部キーのないリレーションシップ</span><span class="sxs-lookup"><span data-stu-id="9fb91-133">Relationships Without a Foreign Key in the Class (Independent Associations)</span></span>  

<span data-ttu-id="9fb91-134">外部キー プロパティは、クラス定義に含めるときは、他のプロパティと同様に、対応するパラメーターを変更できます。</span><span class="sxs-lookup"><span data-stu-id="9fb91-134">When a foreign key property is included in the class definition, the corresponding parameter can be renamed in the same way as any other property.</span></span> <span data-ttu-id="9fb91-135">既定のパラメーター名は、リレーションシップ、クラスの外部キー プロパティが存在しますが、ときに **\<navigation_property_name\>_\<primary_key_name\>** します。</span><span class="sxs-lookup"><span data-stu-id="9fb91-135">When a relationship exists without a foreign key property in the class, the default parameter name is **\<navigation_property_name\>_\<primary_key_name\>**.</span></span>  

<span data-ttu-id="9fb91-136">たとえば、次のクラス定義は、挿入や投稿を更新するストアド プロシージャで想定されている Blog_BlogId パラメーターになります。</span><span class="sxs-lookup"><span data-stu-id="9fb91-136">For example, the following class definitions would result in a Blog_BlogId parameter being expected in the stored procedures to insert and update Posts.</span></span>  

``` csharp
public class Blog  
{  
  public int BlogId { get; set; }  
  public string Name { get; set; }  
  public string Url { get; set; }

  public List<Post> Posts { get; set; }  
}  

public class Post  
{  
  public int PostId { get; set; }  
  public string Title { get; set; }  
  public string Content { get; set; }  

  public Blog Blog { get; set; }  
}
```  

### <a name="overriding-the-defaults"></a><span data-ttu-id="9fb91-137">既定の設定をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="9fb91-137">Overriding the Defaults</span></span>  

<span data-ttu-id="9fb91-138">パラメーターをメソッドに主キーのプロパティへのパスを指定することで、クラスに含まれていない外部キーのパラメーターを変更することができます。</span><span class="sxs-lookup"><span data-stu-id="9fb91-138">You can change parameters for foreign keys that are not included in the class by supplying the path to the primary key property to the Parameter method.</span></span>  

``` csharp
modelBuilder
  .Entity<Post>()  
  .MapToStoredProcedures(s =>  
    s.Insert(i => i.Parameter(p => p.Blog.BlogId, "blog_id")));
```  

<span data-ttu-id="9fb91-139">ナビゲーション プロパティを依存エンティティ (つまり、されていない場合</span><span class="sxs-lookup"><span data-stu-id="9fb91-139">If you don’t have a navigation property on the dependent entity (i.e</span></span> <span data-ttu-id="9fb91-140">Post.Blog プロパティが) Association メソッドを使用して、リレーションシップの他方の end を特定し、キーのプロパティのそれぞれに対応するパラメーターを構成できません。</span><span class="sxs-lookup"><span data-stu-id="9fb91-140">no Post.Blog property) then you can use the Association method to identify the other end of the relationship and then configure the parameters that correspond to each of the key property(s).</span></span>  

``` csharp
modelBuilder
  .Entity<Post>()  
  .MapToStoredProcedures(s =>  
    s.Insert(i => i.Navigation<Blog>(  
      b => b.Posts,  
      c => c.Parameter(b => b.BlogId, "blog_id"))));
```  

## <a name="concurrency-tokens"></a><span data-ttu-id="9fb91-141">同時実行制御トークン</span><span class="sxs-lookup"><span data-stu-id="9fb91-141">Concurrency Tokens</span></span>  

<span data-ttu-id="9fb91-142">Update、delete ストアド プロシージャは、同時実行を処理する必要もあります。</span><span class="sxs-lookup"><span data-stu-id="9fb91-142">Update and delete stored procedures may also need to deal with concurrency:</span></span>  

- <span data-ttu-id="9fb91-143">エンティティに同時実行制御トークンが含まれている場合、ストアド プロシージャは必要に応じて更新/削除された行 (行処理されました) の数を返す出力パラメーターを持つことができます。</span><span class="sxs-lookup"><span data-stu-id="9fb91-143">If the entity contains concurrency tokens, the stored procedure can optionally have an output parameter that returns the number of rows updated/deleted (rows affected).</span></span> <span data-ttu-id="9fb91-144">RowsAffectedParameter メソッドを使用して、そのようなパラメーターを構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="9fb91-144">Such a parameter must be configured using the RowsAffectedParameter method.</span></span>  
<span data-ttu-id="9fb91-145">既定では、EF は、影響を受けた行の数を決定するのに ExecuteNonQuery からの戻り値を使用します。</span><span class="sxs-lookup"><span data-stu-id="9fb91-145">By default EF uses the return value from ExecuteNonQuery to determine how many rows were affected.</span></span> <span data-ttu-id="9fb91-146">EF の観点から不適切になる ExecuteNonQuery の戻り値のある、ストアド プロシージャのロジックを実行する場合に役立ちます。 影響を受ける行の出力パラメーターを指定する実行の最後にします。</span><span class="sxs-lookup"><span data-stu-id="9fb91-146">Specifying a rows affected output parameter is useful if you perform any logic in your sproc that would result in the return value of ExecuteNonQuery being incorrect (from EF's perspective) at the end of execution.</span></span>  
- <span data-ttu-id="9fb91-147">各同時実行制御トークンがありますがという名前のパラメーターをする **\<property_name\>_Original** (Timestamp_Original など)。</span><span class="sxs-lookup"><span data-stu-id="9fb91-147">For each concurrency token there will be a parameter named **\<property_name\>_Original** (for example, Timestamp_Original ).</span></span> <span data-ttu-id="9fb91-148">これは、データベースから照会されたときに、値は – このプロパティの元の値渡されます。</span><span class="sxs-lookup"><span data-stu-id="9fb91-148">This will be passed the original value of this property – the value when queried from the database.</span></span>  
    - <span data-ttu-id="9fb91-149">– タイムスタンプ – などのデータベースで計算される同時実行トークンは、元の値のパラメーターを必要のみがあります。</span><span class="sxs-lookup"><span data-stu-id="9fb91-149">Concurrency tokens that are computed by the database – such as timestamps – will only have an original value parameter.</span></span>  
    - <span data-ttu-id="9fb91-150">更新処理では、同時実行トークンとして設定されている非計算のプロパティに新しい値のパラメーターがあります。</span><span class="sxs-lookup"><span data-stu-id="9fb91-150">Non-computed properties that are set as concurrency tokens will also have a parameter for the new value in the update procedure.</span></span> <span data-ttu-id="9fb91-151">これは、既に説明した新しい値の名前付け規則を使用します。</span><span class="sxs-lookup"><span data-stu-id="9fb91-151">This uses the naming conventions already discussed for new values.</span></span> <span data-ttu-id="9fb91-152">このようなトークンの例を使用する場合、ブログの URL、同時実行トークンとして、この (データベースでのみ更新されるタイムスタンプ トークン) とは異なり、コードで新しい値に更新できるため、新しい値が必要です。</span><span class="sxs-lookup"><span data-stu-id="9fb91-152">An example of such a token would be using a Blog's URL as a concurrency token, the new value is required because this can be updated to a new value by your code (unlike a Timestamp token which is only updated by the database).</span></span>  

<span data-ttu-id="9fb91-153">これは、例ではクラスし、タイムスタンプの同時実行トークンを使用してストアド プロシージャを更新します。</span><span class="sxs-lookup"><span data-stu-id="9fb91-153">This is an example class and update stored procedure with a timestamp concurrency token.</span></span>  

``` csharp
public class Blog  
{  
  public int BlogId { get; set; }  
  public string Name { get; set; }  
  public string Url { get; set; }  
  [Timestamp]
  public byte[] Timestamp { get; set; }
}
```

``` SQL
CREATE PROCEDURE [dbo].[Blog_Update]  
  @BlogId int,  
  @Name nvarchar(max),  
  @Url nvarchar(max),
  @Timestamp_Original rowversion  
AS  
  UPDATE [dbo].[Blogs]
  SET [Name] = @Name, [Url] = @Url     
  WHERE BlogId = @BlogId AND [Timestamp] = @Timestamp_Original
```  

<span data-ttu-id="9fb91-154">これは、クラスし、非計算の同時実行トークンを使用してストアド プロシージャを更新します。</span><span class="sxs-lookup"><span data-stu-id="9fb91-154">Here is an example class and update stored procedure with non-computed concurrency token.</span></span>  

``` csharp
public class Blog  
{  
  public int BlogId { get; set; }  
  public string Name { get; set; }  
  [ConcurrencyCheck]
  public string Url { get; set; }  
}
```

``` SQL
CREATE PROCEDURE [dbo].[Blog_Update]  
  @BlogId int,  
  @Name nvarchar(max),  
  @Url nvarchar(max),
  @Url_Original nvarchar(max),
AS  
  UPDATE [dbo].[Blogs]
  SET [Name] = @Name, [Url] = @Url     
  WHERE BlogId = @BlogId AND [Url] = @Url_Original
```  

### <a name="overriding-the-defaults"></a><span data-ttu-id="9fb91-155">既定の設定をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="9fb91-155">Overriding the Defaults</span></span>  

<span data-ttu-id="9fb91-156">必要に応じて処理行数パラメーターを導入することができます。</span><span class="sxs-lookup"><span data-stu-id="9fb91-156">You can optionally introduce a rows affected parameter.</span></span>  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.RowsAffectedParameter("rows_affected")));
```  

<span data-ttu-id="9fb91-157">– 元の値のみが渡されるが – 計算データベース同時実行トークンの元の値パラメーターの名前を変更するのにだけのメカニズムの名前を変更する標準的なパラメーターを使用できます。</span><span class="sxs-lookup"><span data-stu-id="9fb91-157">For database computed concurrency tokens – where only the original value is passed – you can just use the standard parameter renaming mechanism to rename the parameter for the original value.</span></span>  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.Parameter(b => b.Timestamp, "blog_timestamp")));
```  

<span data-ttu-id="9fb91-158">– 場所両方元と新しい値が渡される – 非計算の同時実行トークンを使用すると、各パラメーターの名前を指定するパラメーターのオーバー ロードを使用できます。</span><span class="sxs-lookup"><span data-stu-id="9fb91-158">For non-computed concurrency tokens – where both the original and new value are passed – you can use an overload of Parameter that allows you to supply a name for each parameter.</span></span>  

``` csharp
modelBuilder
 .Entity<Blog>()
 .MapToStoredProcedures(s => s.Update(u => u.Parameter(b => b.Url, "blog_url", "blog_original_url")));
```  

## <a name="many-to-many-relationships"></a><span data-ttu-id="9fb91-159">多対多リレーションシップ</span><span class="sxs-lookup"><span data-stu-id="9fb91-159">Many to Many Relationships</span></span>  

<span data-ttu-id="9fb91-160">このセクションの例として、次のクラスを使用します。</span><span class="sxs-lookup"><span data-stu-id="9fb91-160">We’ll use the following classes as an example in this section.</span></span>  

``` csharp
public class Post  
{  
  public int PostId { get; set; }  
  public string Title { get; set; }  
  public string Content { get; set; }  

  public List<Tag> Tags { get; set; }  
}  

public class Tag  
{  
  public int TagId { get; set; }  
  public string TagName { get; set; }  

  public List<Post> Posts { get; set; }  
}
```  

<span data-ttu-id="9fb91-161">多対多の関係は、次の構文を使用したストアド プロシージャにマップすることができます。</span><span class="sxs-lookup"><span data-stu-id="9fb91-161">Many to many relationships can be mapped to stored procedures with the following syntax.</span></span>  

``` csharp
modelBuilder  
  .Entity<Post>()  
  .HasMany(p => p.Tags)  
  .WithMany(t => t.Posts)  
  .MapToStoredProcedures();
```  

<span data-ttu-id="9fb91-162">その他の構成が指定されていない場合、既定では、次のストアド プロシージャの図形が使用されます。</span><span class="sxs-lookup"><span data-stu-id="9fb91-162">If no other configuration is supplied then the following stored procedure shape is used by default.</span></span>  

- <span data-ttu-id="9fb91-163">2 つのストアド プロシージャの名前付き **\<type_one\>\<type_two\>(_i)** と **\<type_one\>\<type_two\>削除 (_d)** (PostTag_Insert および PostTag_Delete など)。</span><span class="sxs-lookup"><span data-stu-id="9fb91-163">Two stored procedures named **\<type_one\>\<type_two\>_Insert** and **\<type_one\>\<type_two\>_Delete** (for example, PostTag_Insert and PostTag_Delete).</span></span>  
- <span data-ttu-id="9fb91-164">パラメーターは、各型のキー値になります。</span><span class="sxs-lookup"><span data-stu-id="9fb91-164">The parameters will be the key value(s) for each type.</span></span> <span data-ttu-id="9fb91-165">各パラメーターの名前**\<type_name\>_\<property_name\>** (Post_PostId および Tag_TagId など)。</span><span class="sxs-lookup"><span data-stu-id="9fb91-165">The name of each parameter being **\<type_name\>_\<property_name\>** (for example, Post_PostId and Tag_TagId).</span></span>

<span data-ttu-id="9fb91-166">次の例に示しますを挿入し、ストアド プロシージャを更新します。</span><span class="sxs-lookup"><span data-stu-id="9fb91-166">Here are example insert and update stored procedures.</span></span>  

``` SQL
CREATE PROCEDURE [dbo].[PostTag_Insert]  
  @Post_PostId int,  
  @Tag_TagId int  
AS  
  INSERT INTO [dbo].[Post_Tags] (Post_PostId, Tag_TagId)   
  VALUES (@Post_PostId, @Tag_TagId)
CREATE PROCEDURE [dbo].[PostTag_Delete]  
  @Post_PostId int,  
  @Tag_TagId int  
AS  
  DELETE FROM [dbo].[Post_Tags]    
  WHERE Post_PostId = @Post_PostId AND Tag_TagId = @Tag_TagId
```  

### <a name="overriding-the-defaults"></a><span data-ttu-id="9fb91-167">既定の設定をオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="9fb91-167">Overriding the Defaults</span></span>  

<span data-ttu-id="9fb91-168">プロシージャとパラメーターの名前は、エンティティがストアド プロシージャと同様の方法で構成できます。</span><span class="sxs-lookup"><span data-stu-id="9fb91-168">The procedure and parameter names can be configured in a similar way to entity stored procedures.</span></span>  

``` csharp
modelBuilder  
  .Entity<Post>()  
  .HasMany(p => p.Tags)  
  .WithMany(t => t.Posts)  
  .MapToStoredProcedures(s =>  
    s.Insert(i => i.HasName("add_post_tag")  
                   .LeftKeyParameter(p => p.PostId, "post_id")  
                   .RightKeyParameter(t => t.TagId, "tag_id"))  
     .Delete(d => d.HasName("remove_post_tag")  
                   .LeftKeyParameter(p => p.PostId, "post_id")  
                   .RightKeyParameter(t => t.TagId, "tag_id")));
```  
