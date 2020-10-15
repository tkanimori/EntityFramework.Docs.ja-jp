---
title: ストアドプロシージャの挿入、更新、および削除の Code First-EF6
description: Entity Framework 6 でのストアドプロシージャの挿入、更新、および削除の Code First
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/code-first/fluent/cud-stored-procedures
ms.openlocfilehash: 749bcc9faaf92ae6555c37facb8de31d877d1260
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92065187"
---
# <a name="code-first-insert-update-and-delete-stored-procedures"></a><span data-ttu-id="a28f3-103">ストアドプロシージャの挿入、更新、および削除の Code First</span><span class="sxs-lookup"><span data-stu-id="a28f3-103">Code First Insert, Update, and Delete Stored Procedures</span></span>
> [!NOTE]
> <span data-ttu-id="a28f3-104">**EF6 以降のみ** - このページで説明する機能、API などは、Entity Framework 6 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="a28f3-104">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="a28f3-105">以前のバージョンを使用している場合、一部またはすべての情報は適用されません。</span><span class="sxs-lookup"><span data-stu-id="a28f3-105">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="a28f3-106">既定では、Code First は、直接テーブルアクセスを使用して insert、update、および delete コマンドを実行するようにすべてのエンティティを構成します。</span><span class="sxs-lookup"><span data-stu-id="a28f3-106">By default, Code First will configure all entities to perform insert, update and delete commands using direct table access.</span></span> <span data-ttu-id="a28f3-107">EF6 以降では、モデル内の一部またはすべてのエンティティに対してストアドプロシージャを使用するように Code First モデルを構成できます。</span><span class="sxs-lookup"><span data-stu-id="a28f3-107">Starting in EF6 you can configure your Code First model to use stored procedures for some or all entities in your model.</span></span>  

## <a name="basic-entity-mapping"></a><span data-ttu-id="a28f3-108">基本エンティティのマッピング</span><span class="sxs-lookup"><span data-stu-id="a28f3-108">Basic Entity Mapping</span></span>  

<span data-ttu-id="a28f3-109">Fluent API を使用して、挿入、更新、削除のストアドプロシージャを使用することを選択できます。</span><span class="sxs-lookup"><span data-stu-id="a28f3-109">You can opt into using stored procedures for insert, update and delete using the Fluent API.</span></span>  

``` csharp
modelBuilder
  .Entity<Blog>()
  .MapToStoredProcedures();
```  

<span data-ttu-id="a28f3-110">この操作を行うと、Code First によって、データベース内のストアドプロシージャの想定される構造を構築するために何らかの規則が使用されます。</span><span class="sxs-lookup"><span data-stu-id="a28f3-110">Doing this will cause Code First to use some conventions to build the expected shape of the stored procedures in the database.</span></span>  

- <span data-ttu-id="a28f3-111">\*\* \<type_name\> _Insert**、 \*\* \<type_name\> _Update** 、 \*\* \<type_name\> _Delete\*\*という名前の3つのストアドプロシージャ (たとえば、Blog_Insert、Blog_Update、Blog_Delete)。</span><span class="sxs-lookup"><span data-stu-id="a28f3-111">Three stored procedures named **\<type_name\>_Insert**, **\<type_name\>_Update** and **\<type_name\>_Delete** (for example, Blog_Insert, Blog_Update and Blog_Delete).</span></span>  
- <span data-ttu-id="a28f3-112">パラメーター名はプロパティ名に対応します。</span><span class="sxs-lookup"><span data-stu-id="a28f3-112">Parameter names correspond to the property names.</span></span>  
  > [!NOTE]
  > <span data-ttu-id="a28f3-113">HasColumnName () または Column 属性を使用して特定のプロパティの列の名前を変更する場合、この名前はプロパティ名の代わりにパラメーターに使用されます。</span><span class="sxs-lookup"><span data-stu-id="a28f3-113">If you use HasColumnName() or the Column attribute to rename the column for a given property then this name is used for parameters instead of the property name.</span></span>  
- <span data-ttu-id="a28f3-114">**Insert ストアドプロシージャ** には、store generated (id または計算済み) としてマークされているものを除き、すべてのプロパティのパラメーターがあります。</span><span class="sxs-lookup"><span data-stu-id="a28f3-114">**The insert stored procedure** will have a parameter for every property, except for those marked as store generated (identity or computed).</span></span> <span data-ttu-id="a28f3-115">ストアドプロシージャは、ストアが生成する各プロパティの列を含む結果セットを返す必要があります。</span><span class="sxs-lookup"><span data-stu-id="a28f3-115">The stored procedure should return a result set with a column for each store generated property.</span></span>  
- <span data-ttu-id="a28f3-116">**Update ストアドプロシージャ** には、ストアで生成されたパターン ' 計算済み ' でマークされているものを除き、すべてのプロパティのパラメーターがあります。</span><span class="sxs-lookup"><span data-stu-id="a28f3-116">**The update stored procedure** will have a parameter for every property, except for those marked with a store generated pattern of 'Computed'.</span></span> <span data-ttu-id="a28f3-117">一部の同時実行トークンには、元の値のパラメーターが必要です。詳細については、後述の「 *同時実行トークン* 」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="a28f3-117">Some concurrency tokens require a parameter for the original value, see the *Concurrency Tokens* section below for details.</span></span> <span data-ttu-id="a28f3-118">ストアドプロシージャは、計算されたプロパティごとに列を含む結果セットを返す必要があります。</span><span class="sxs-lookup"><span data-stu-id="a28f3-118">The stored procedure should return a result set with a column for each computed property.</span></span>  
- <span data-ttu-id="a28f3-119">**Delete ストアドプロシージャに** は、エンティティのキー値のパラメーター (または、エンティティに複合キーがある場合は複数のパラメーター) が必要です。</span><span class="sxs-lookup"><span data-stu-id="a28f3-119">**The delete stored procedure** should have a parameter for the key value of the entity (or multiple parameters if the entity has a composite key).</span></span> <span data-ttu-id="a28f3-120">さらに、delete プロシージャには、ターゲットテーブルのすべての独立した関連付け外部キー (エンティティで宣言された対応する外部キープロパティがないリレーションシップ) のパラメーターも含まれている必要があります。</span><span class="sxs-lookup"><span data-stu-id="a28f3-120">Additionally, the delete procedure should also have parameters for any independent association foreign keys on the target table (relationships that do not have corresponding foreign key properties declared in the entity).</span></span> <span data-ttu-id="a28f3-121">一部の同時実行トークンには、元の値のパラメーターが必要です。詳細については、後述の「 *同時実行トークン* 」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="a28f3-121">Some concurrency tokens require a parameter for the original value, see the *Concurrency Tokens* section below for details.</span></span>  

<span data-ttu-id="a28f3-122">例として、次のクラスを使用します。</span><span class="sxs-lookup"><span data-stu-id="a28f3-122">Using the following class as an example:</span></span>  

``` csharp
public class Blog  
{  
  public int BlogId { get; set; }  
  public string Name { get; set; }  
  public string Url { get; set; }  
}
```  

<span data-ttu-id="a28f3-123">既定のストアドプロシージャは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="a28f3-123">The default stored procedures would be:</span></span>  

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

### <a name="overriding-the-defaults"></a><span data-ttu-id="a28f3-124">既定値のオーバーライド</span><span class="sxs-lookup"><span data-stu-id="a28f3-124">Overriding the Defaults</span></span>  

<span data-ttu-id="a28f3-125">既定で構成されていたものの一部またはすべてを上書きできます。</span><span class="sxs-lookup"><span data-stu-id="a28f3-125">You can override part or all of what was configured by default.</span></span>  

<span data-ttu-id="a28f3-126">1つ以上のストアドプロシージャの名前を変更できます。</span><span class="sxs-lookup"><span data-stu-id="a28f3-126">You can change the name of one or more stored procedures.</span></span> <span data-ttu-id="a28f3-127">この例では、update ストアドプロシージャの名前を変更します。</span><span class="sxs-lookup"><span data-stu-id="a28f3-127">This example renames the update stored procedure only.</span></span>  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.HasName("modify_blog")));
```  

<span data-ttu-id="a28f3-128">この例では、3つのストアドプロシージャの名前をすべて変更します。</span><span class="sxs-lookup"><span data-stu-id="a28f3-128">This example renames all three stored procedures.</span></span>  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.HasName("modify_blog"))  
     .Delete(d => d.HasName("delete_blog"))  
     .Insert(i => i.HasName("insert_blog")));
```  

<span data-ttu-id="a28f3-129">これらの例では、呼び出しは連結されていますが、ラムダブロック構文を使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="a28f3-129">In these examples the calls are chained together, but you can also use lambda block syntax.</span></span>  

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

<span data-ttu-id="a28f3-130">次の例では、update ストアドプロシージャの "ブログ Id" プロパティのパラメーターの名前を変更します。</span><span class="sxs-lookup"><span data-stu-id="a28f3-130">This example renames the parameter for the BlogId property on the update stored procedure.</span></span>  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.Parameter(b => b.BlogId, "blog_id")));
```  

<span data-ttu-id="a28f3-131">これらの呼び出しはすべて chainable とコンポーザブルです。</span><span class="sxs-lookup"><span data-stu-id="a28f3-131">These calls are all chainable and composable.</span></span> <span data-ttu-id="a28f3-132">次に、3つのストアドプロシージャとそのパラメーターの名前を変更する例を示します。</span><span class="sxs-lookup"><span data-stu-id="a28f3-132">Here is an example that renames all three stored procedures and their parameters.</span></span>  

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

<span data-ttu-id="a28f3-133">また、データベースによって生成された値を含む結果セット内の列の名前を変更することもできます。</span><span class="sxs-lookup"><span data-stu-id="a28f3-133">You can also change the name of the columns in the result set that contains database generated values.</span></span>  

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

## <a name="relationships-without-a-foreign-key-in-the-class-independent-associations"></a><span data-ttu-id="a28f3-134">クラスに外部キーを持たないリレーションシップ (独立した関連付け)</span><span class="sxs-lookup"><span data-stu-id="a28f3-134">Relationships Without a Foreign Key in the Class (Independent Associations)</span></span>  

<span data-ttu-id="a28f3-135">外部キープロパティがクラス定義に含まれている場合、対応するパラメーターの名前を他のプロパティと同じ方法で変更できます。</span><span class="sxs-lookup"><span data-stu-id="a28f3-135">When a foreign key property is included in the class definition, the corresponding parameter can be renamed in the same way as any other property.</span></span> <span data-ttu-id="a28f3-136">クラスの外部キープロパティを使用せずにリレーションシップが存在する場合、既定のパラメーター名は\*\* \<navigation_property_name\> _ \<primary_key_name\> \*\*です。</span><span class="sxs-lookup"><span data-stu-id="a28f3-136">When a relationship exists without a foreign key property in the class, the default parameter name is **\<navigation_property_name\>_\<primary_key_name\>**.</span></span>  

<span data-ttu-id="a28f3-137">たとえば、次のクラス定義では、ストアドプロシージャ内で投稿を挿入および更新するために Blog_BlogId パラメーターが想定されています。</span><span class="sxs-lookup"><span data-stu-id="a28f3-137">For example, the following class definitions would result in a Blog_BlogId parameter being expected in the stored procedures to insert and update Posts.</span></span>  

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

### <a name="overriding-the-defaults"></a><span data-ttu-id="a28f3-138">既定値のオーバーライド</span><span class="sxs-lookup"><span data-stu-id="a28f3-138">Overriding the Defaults</span></span>  

<span data-ttu-id="a28f3-139">プライマリキープロパティへのパスをパラメーターメソッドに指定することで、クラスに含まれていない外部キーのパラメーターを変更できます。</span><span class="sxs-lookup"><span data-stu-id="a28f3-139">You can change parameters for foreign keys that are not included in the class by supplying the path to the primary key property to the Parameter method.</span></span>  

``` csharp
modelBuilder
  .Entity<Post>()  
  .MapToStoredProcedures(s =>  
    s.Insert(i => i.Parameter(p => p.Blog.BlogId, "blog_id")));
```  

<span data-ttu-id="a28f3-140">依存エンティティにナビゲーションプロパティがない場合 (つまり、</span><span class="sxs-lookup"><span data-stu-id="a28f3-140">If you don’t have a navigation property on the dependent entity (i.e</span></span> <span data-ttu-id="a28f3-141">投稿がありません)。その後、Association メソッドを使用してリレーションシップのもう一方の end を特定し、各キープロパティに対応するパラメーターを構成できます。</span><span class="sxs-lookup"><span data-stu-id="a28f3-141">no Post.Blog property) then you can use the Association method to identify the other end of the relationship and then configure the parameters that correspond to each of the key property(s).</span></span>  

``` csharp
modelBuilder
  .Entity<Post>()  
  .MapToStoredProcedures(s =>  
    s.Insert(i => i.Navigation<Blog>(  
      b => b.Posts,  
      c => c.Parameter(b => b.BlogId, "blog_id"))));
```  

## <a name="concurrency-tokens"></a><span data-ttu-id="a28f3-142">コンカレンシー トークン</span><span class="sxs-lookup"><span data-stu-id="a28f3-142">Concurrency Tokens</span></span>  

<span data-ttu-id="a28f3-143">ストアドプロシージャの更新および削除では、同時実行が必要になる場合もあります。</span><span class="sxs-lookup"><span data-stu-id="a28f3-143">Update and delete stored procedures may also need to deal with concurrency:</span></span>  

- <span data-ttu-id="a28f3-144">エンティティに同時実行トークンが含まれている場合、ストアドプロシージャには、更新または削除された行数 (影響を受ける行) を返す出力パラメーターをオプションで指定できます。</span><span class="sxs-lookup"><span data-stu-id="a28f3-144">If the entity contains concurrency tokens, the stored procedure can optionally have an output parameter that returns the number of rows updated/deleted (rows affected).</span></span> <span data-ttu-id="a28f3-145">このようなパラメーターは、RowsAffectedParameter メソッドを使用して構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="a28f3-145">Such a parameter must be configured using the RowsAffectedParameter method.</span></span>  
<span data-ttu-id="a28f3-146">既定では、EF は ExecuteNonQuery からの戻り値を使用して、影響を受けた行の数を確認します。</span><span class="sxs-lookup"><span data-stu-id="a28f3-146">By default EF uses the return value from ExecuteNonQuery to determine how many rows were affected.</span></span> <span data-ttu-id="a28f3-147">影響を受ける行を指定すると、ストアドプロシージャで任意のロジックを実行して、実行の終了時に ExecuteNonQuery の戻り値が誤って (EF の観点から) 不正確になるような場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="a28f3-147">Specifying a rows affected output parameter is useful if you perform any logic in your sproc that would result in the return value of ExecuteNonQuery being incorrect (from EF's perspective) at the end of execution.</span></span>  
- <span data-ttu-id="a28f3-148">同時実行トークンごとに、 \*\* \<property_name\> _Original\*\*という名前のパラメーターがあります (Timestamp_Original など)。</span><span class="sxs-lookup"><span data-stu-id="a28f3-148">For each concurrency token there will be a parameter named **\<property_name\>_Original** (for example, Timestamp_Original ).</span></span> <span data-ttu-id="a28f3-149">これには、このプロパティの元の値 (データベースからクエリを実行したときの値) が渡されます。</span><span class="sxs-lookup"><span data-stu-id="a28f3-149">This will be passed the original value of this property – the value when queried from the database.</span></span>  
    - <span data-ttu-id="a28f3-150">タイムスタンプなど、データベースによって計算される同時実行トークンには、元の値パラメーターのみが含まれます。</span><span class="sxs-lookup"><span data-stu-id="a28f3-150">Concurrency tokens that are computed by the database – such as timestamps – will only have an original value parameter.</span></span>  
    - <span data-ttu-id="a28f3-151">同時実行トークンとして設定された非計算プロパティには、更新プロシージャの新しい値のパラメーターも含まれます。</span><span class="sxs-lookup"><span data-stu-id="a28f3-151">Non-computed properties that are set as concurrency tokens will also have a parameter for the new value in the update procedure.</span></span> <span data-ttu-id="a28f3-152">これには、新しい値に既に説明した名前付け規則が使用されます。</span><span class="sxs-lookup"><span data-stu-id="a28f3-152">This uses the naming conventions already discussed for new values.</span></span> <span data-ttu-id="a28f3-153">このようなトークンの例としては、ブログの URL を同時実行トークンとして使用することが挙げられます。この新しい値は、コードによって新しい値に更新できるためです (データベースによってのみ更新されるタイムスタンプトークンとは異なります)。</span><span class="sxs-lookup"><span data-stu-id="a28f3-153">An example of such a token would be using a Blog's URL as a concurrency token, the new value is required because this can be updated to a new value by your code (unlike a Timestamp token which is only updated by the database).</span></span>  

<span data-ttu-id="a28f3-154">これは、クラスの例であり、timestamp 同時実行トークンを使用してストアドプロシージャを更新します。</span><span class="sxs-lookup"><span data-stu-id="a28f3-154">This is an example class and update stored procedure with a timestamp concurrency token.</span></span>  

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

<span data-ttu-id="a28f3-155">次に、クラスの例と、計算されない同時実行トークンを使用したストアドプロシージャの更新を示します。</span><span class="sxs-lookup"><span data-stu-id="a28f3-155">Here is an example class and update stored procedure with non-computed concurrency token.</span></span>  

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

### <a name="overriding-the-defaults"></a><span data-ttu-id="a28f3-156">既定値のオーバーライド</span><span class="sxs-lookup"><span data-stu-id="a28f3-156">Overriding the Defaults</span></span>  

<span data-ttu-id="a28f3-157">必要に応じて、影響を受ける行を指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="a28f3-157">You can optionally introduce a rows affected parameter.</span></span>  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.RowsAffectedParameter("rows_affected")));
```  

<span data-ttu-id="a28f3-158">データベースで計算された同時実行トークンの場合–元の値のみが渡されます。標準パラメーターの名前変更メカニズムを使用して、元の値のパラメーターの名前を変更できます。</span><span class="sxs-lookup"><span data-stu-id="a28f3-158">For database computed concurrency tokens – where only the original value is passed – you can just use the standard parameter renaming mechanism to rename the parameter for the original value.</span></span>  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.Parameter(b => b.Timestamp, "blog_timestamp")));
```  

<span data-ttu-id="a28f3-159">計算されていない同時実行トークンの場合–元の値と新しい値の両方が渡されます。パラメーターのオーバーロードを使用すると、各パラメーターに名前を指定できます。</span><span class="sxs-lookup"><span data-stu-id="a28f3-159">For non-computed concurrency tokens – where both the original and new value are passed – you can use an overload of Parameter that allows you to supply a name for each parameter.</span></span>  

``` csharp
modelBuilder
 .Entity<Blog>()
 .MapToStoredProcedures(s => s.Update(u => u.Parameter(b => b.Url, "blog_url", "blog_original_url")));
```  

## <a name="many-to-many-relationships"></a><span data-ttu-id="a28f3-160">多対多リレーションシップ</span><span class="sxs-lookup"><span data-stu-id="a28f3-160">Many to Many Relationships</span></span>  

<span data-ttu-id="a28f3-161">このセクションの例として、次のクラスを使用します。</span><span class="sxs-lookup"><span data-stu-id="a28f3-161">We’ll use the following classes as an example in this section.</span></span>  

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

<span data-ttu-id="a28f3-162">多対多リレーションシップは、次の構文を使用してストアドプロシージャにマップできます。</span><span class="sxs-lookup"><span data-stu-id="a28f3-162">Many to many relationships can be mapped to stored procedures with the following syntax.</span></span>  

``` csharp
modelBuilder  
  .Entity<Post>()  
  .HasMany(p => p.Tags)  
  .WithMany(t => t.Posts)  
  .MapToStoredProcedures();
```  

<span data-ttu-id="a28f3-163">他の構成が指定されていない場合、既定では、次のストアドプロシージャ図形が使用されます。</span><span class="sxs-lookup"><span data-stu-id="a28f3-163">If no other configuration is supplied then the following stored procedure shape is used by default.</span></span>  

- <span data-ttu-id="a28f3-164">\*\* \<type_one\> \<type_two\> _Insert**と** \<type_one\> \<type_two\> _Delete\*\*という名前の2つのストアドプロシージャ (たとえば、PostTag_Insert と PostTag_Delete)。</span><span class="sxs-lookup"><span data-stu-id="a28f3-164">Two stored procedures named **\<type_one\>\<type_two\>_Insert** and **\<type_one\>\<type_two\>_Delete** (for example, PostTag_Insert and PostTag_Delete).</span></span>  
- <span data-ttu-id="a28f3-165">パラメーターは、各型のキー値になります。</span><span class="sxs-lookup"><span data-stu-id="a28f3-165">The parameters will be the key value(s) for each type.</span></span> <span data-ttu-id="a28f3-166">各パラメーターの名前は\*\* \<type_name\> _ \<property_name\> \*\*です (たとえば、Post_PostId と Tag_TagId)。</span><span class="sxs-lookup"><span data-stu-id="a28f3-166">The name of each parameter being **\<type_name\>_\<property_name\>** (for example, Post_PostId and Tag_TagId).</span></span>

<span data-ttu-id="a28f3-167">Insert および update ストアドプロシージャの例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="a28f3-167">Here are example insert and update stored procedures.</span></span>  

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

### <a name="overriding-the-defaults"></a><span data-ttu-id="a28f3-168">既定値のオーバーライド</span><span class="sxs-lookup"><span data-stu-id="a28f3-168">Overriding the Defaults</span></span>  

<span data-ttu-id="a28f3-169">プロシージャ名とパラメーター名は、エンティティストアドプロシージャと同様の方法で構成できます。</span><span class="sxs-lookup"><span data-stu-id="a28f3-169">The procedure and parameter names can be configured in a similar way to entity stored procedures.</span></span>  

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
