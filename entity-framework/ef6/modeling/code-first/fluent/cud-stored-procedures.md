---
title: ストアドプロシージャの挿入、更新、および削除の Code First-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 9a7ae7f9-4072-4843-877d-506dd7eef576
ms.openlocfilehash: bfc56671814aec1965ac054ff901297e5cdbbecb
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78415773"
---
# <a name="code-first-insert-update-and-delete-stored-procedures"></a><span data-ttu-id="c061b-102">ストアドプロシージャの挿入、更新、および削除の Code First</span><span class="sxs-lookup"><span data-stu-id="c061b-102">Code First Insert, Update, and Delete Stored Procedures</span></span>
> [!NOTE]
> <span data-ttu-id="c061b-103">**EF6 以降のみ** - このページで説明する機能、API などは、Entity Framework 6 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="c061b-103">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="c061b-104">以前のバージョンを使用している場合、一部またはすべての情報は適用されません。</span><span class="sxs-lookup"><span data-stu-id="c061b-104">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="c061b-105">既定では、Code First は、直接テーブルアクセスを使用して insert、update、および delete コマンドを実行するようにすべてのエンティティを構成します。</span><span class="sxs-lookup"><span data-stu-id="c061b-105">By default, Code First will configure all entities to perform insert, update and delete commands using direct table access.</span></span> <span data-ttu-id="c061b-106">EF6 以降では、モデル内の一部またはすべてのエンティティに対してストアドプロシージャを使用するように Code First モデルを構成できます。</span><span class="sxs-lookup"><span data-stu-id="c061b-106">Starting in EF6 you can configure your Code First model to use stored procedures for some or all entities in your model.</span></span>  

## <a name="basic-entity-mapping"></a><span data-ttu-id="c061b-107">基本エンティティのマッピング</span><span class="sxs-lookup"><span data-stu-id="c061b-107">Basic Entity Mapping</span></span>  

<span data-ttu-id="c061b-108">Fluent API を使用して、挿入、更新、削除のストアドプロシージャを使用することを選択できます。</span><span class="sxs-lookup"><span data-stu-id="c061b-108">You can opt into using stored procedures for insert, update and delete using the Fluent API.</span></span>  

``` csharp
modelBuilder
  .Entity<Blog>()
  .MapToStoredProcedures();
```  

<span data-ttu-id="c061b-109">この操作を行うと、Code First によって、データベース内のストアドプロシージャの想定される構造を構築するために何らかの規則が使用されます。</span><span class="sxs-lookup"><span data-stu-id="c061b-109">Doing this will cause Code First to use some conventions to build the expected shape of the stored procedures in the database.</span></span>  

- <span data-ttu-id="c061b-110">\<という名前の3つのストアドプロシージャ**type_name\>_Insert**、 **\<** type_name\>_Update **\<type_name\>** _Delete の Blog_Insert)。</span><span class="sxs-lookup"><span data-stu-id="c061b-110">Three stored procedures named **\<type_name\>_Insert**, **\<type_name\>_Update** and **\<type_name\>_Delete** (for example, Blog_Insert, Blog_Update and Blog_Delete).</span></span>  
- <span data-ttu-id="c061b-111">パラメーター名はプロパティ名に対応します。</span><span class="sxs-lookup"><span data-stu-id="c061b-111">Parameter names correspond to the property names.</span></span>  
  > [!NOTE]
  > <span data-ttu-id="c061b-112">HasColumnName () または Column 属性を使用して特定のプロパティの列の名前を変更する場合、この名前はプロパティ名の代わりにパラメーターに使用されます。</span><span class="sxs-lookup"><span data-stu-id="c061b-112">If you use HasColumnName() or the Column attribute to rename the column for a given property then this name is used for parameters instead of the property name.</span></span>  
- <span data-ttu-id="c061b-113">**Insert ストアドプロシージャ**には、store generated (id または計算済み) としてマークされているものを除き、すべてのプロパティのパラメーターがあります。</span><span class="sxs-lookup"><span data-stu-id="c061b-113">**The insert stored procedure** will have a parameter for every property, except for those marked as store generated (identity or computed).</span></span> <span data-ttu-id="c061b-114">ストアドプロシージャは、ストアが生成する各プロパティの列を含む結果セットを返す必要があります。</span><span class="sxs-lookup"><span data-stu-id="c061b-114">The stored procedure should return a result set with a column for each store generated property.</span></span>  
- <span data-ttu-id="c061b-115">**Update ストアドプロシージャ**には、ストアで生成されたパターン ' 計算済み ' でマークされているものを除き、すべてのプロパティのパラメーターがあります。</span><span class="sxs-lookup"><span data-stu-id="c061b-115">**The update stored procedure** will have a parameter for every property, except for those marked with a store generated pattern of 'Computed'.</span></span> <span data-ttu-id="c061b-116">一部の同時実行トークンには、元の値のパラメーターが必要です。詳細については、後述の「*同時実行トークン*」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="c061b-116">Some concurrency tokens require a parameter for the original value, see the *Concurrency Tokens* section below for details.</span></span> <span data-ttu-id="c061b-117">ストアドプロシージャは、計算されたプロパティごとに列を含む結果セットを返す必要があります。</span><span class="sxs-lookup"><span data-stu-id="c061b-117">The stored procedure should return a result set with a column for each computed property.</span></span>  
- <span data-ttu-id="c061b-118">**Delete ストアドプロシージャに**は、エンティティのキー値のパラメーター (または、エンティティに複合キーがある場合は複数のパラメーター) が必要です。</span><span class="sxs-lookup"><span data-stu-id="c061b-118">**The delete stored procedure** should have a parameter for the key value of the entity (or multiple parameters if the entity has a composite key).</span></span> <span data-ttu-id="c061b-119">さらに、delete プロシージャには、ターゲットテーブルのすべての独立した関連付け外部キー (エンティティで宣言された対応する外部キープロパティがないリレーションシップ) のパラメーターも含まれている必要があります。</span><span class="sxs-lookup"><span data-stu-id="c061b-119">Additionally, the delete procedure should also have parameters for any independent association foreign keys on the target table (relationships that do not have corresponding foreign key properties declared in the entity).</span></span> <span data-ttu-id="c061b-120">一部の同時実行トークンには、元の値のパラメーターが必要です。詳細については、後述の「*同時実行トークン*」セクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="c061b-120">Some concurrency tokens require a parameter for the original value, see the *Concurrency Tokens* section below for details.</span></span>  

<span data-ttu-id="c061b-121">例として、次のクラスを使用します。</span><span class="sxs-lookup"><span data-stu-id="c061b-121">Using the following class as an example:</span></span>  

``` csharp
public class Blog  
{  
  public int BlogId { get; set; }  
  public string Name { get; set; }  
  public string Url { get; set; }  
}
```  

<span data-ttu-id="c061b-122">既定のストアドプロシージャは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="c061b-122">The default stored procedures would be:</span></span>  

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

### <a name="overriding-the-defaults"></a><span data-ttu-id="c061b-123">既定値のオーバーライド</span><span class="sxs-lookup"><span data-stu-id="c061b-123">Overriding the Defaults</span></span>  

<span data-ttu-id="c061b-124">既定で構成されていたものの一部またはすべてを上書きできます。</span><span class="sxs-lookup"><span data-stu-id="c061b-124">You can override part or all of what was configured by default.</span></span>  

<span data-ttu-id="c061b-125">1つ以上のストアドプロシージャの名前を変更できます。</span><span class="sxs-lookup"><span data-stu-id="c061b-125">You can change the name of one or more stored procedures.</span></span> <span data-ttu-id="c061b-126">この例では、update ストアドプロシージャの名前を変更します。</span><span class="sxs-lookup"><span data-stu-id="c061b-126">This example renames the update stored procedure only.</span></span>  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.HasName("modify_blog")));
```  

<span data-ttu-id="c061b-127">この例では、3つのストアドプロシージャの名前をすべて変更します。</span><span class="sxs-lookup"><span data-stu-id="c061b-127">This example renames all three stored procedures.</span></span>  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.HasName("modify_blog"))  
     .Delete(d => d.HasName("delete_blog"))  
     .Insert(i => i.HasName("insert_blog")));
```  

<span data-ttu-id="c061b-128">これらの例では、呼び出しは連結されていますが、ラムダブロック構文を使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="c061b-128">In these examples the calls are chained together, but you can also use lambda block syntax.</span></span>  

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

<span data-ttu-id="c061b-129">次の例では、update ストアドプロシージャの "ブログ Id" プロパティのパラメーターの名前を変更します。</span><span class="sxs-lookup"><span data-stu-id="c061b-129">This example renames the parameter for the BlogId property on the update stored procedure.</span></span>  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.Parameter(b => b.BlogId, "blog_id")));
```  

<span data-ttu-id="c061b-130">これらの呼び出しはすべて chainable とコンポーザブルです。</span><span class="sxs-lookup"><span data-stu-id="c061b-130">These calls are all chainable and composable.</span></span> <span data-ttu-id="c061b-131">次に、3つのストアドプロシージャとそのパラメーターの名前を変更する例を示します。</span><span class="sxs-lookup"><span data-stu-id="c061b-131">Here is an example that renames all three stored procedures and their parameters.</span></span>  

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

<span data-ttu-id="c061b-132">また、データベースによって生成された値を含む結果セット内の列の名前を変更することもできます。</span><span class="sxs-lookup"><span data-stu-id="c061b-132">You can also change the name of the columns in the result set that contains database generated values.</span></span>  

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

## <a name="relationships-without-a-foreign-key-in-the-class-independent-associations"></a><span data-ttu-id="c061b-133">クラスに外部キーを持たないリレーションシップ (独立した関連付け)</span><span class="sxs-lookup"><span data-stu-id="c061b-133">Relationships Without a Foreign Key in the Class (Independent Associations)</span></span>  

<span data-ttu-id="c061b-134">外部キープロパティがクラス定義に含まれている場合、対応するパラメーターの名前を他のプロパティと同じ方法で変更できます。</span><span class="sxs-lookup"><span data-stu-id="c061b-134">When a foreign key property is included in the class definition, the corresponding parameter can be renamed in the same way as any other property.</span></span> <span data-ttu-id="c061b-135">クラスの外部キープロパティを使用せずにリレーションシップが存在する場合、既定のパラメーター名は **\>_\<primary_key_name\>navigation_property_name\<** ます。</span><span class="sxs-lookup"><span data-stu-id="c061b-135">When a relationship exists without a foreign key property in the class, the default parameter name is **\<navigation_property_name\>_\<primary_key_name\>**.</span></span>  

<span data-ttu-id="c061b-136">たとえば、次のクラス定義では、ストアドプロシージャ内で投稿を挿入および更新するために Blog_BlogId パラメーターが想定されています。</span><span class="sxs-lookup"><span data-stu-id="c061b-136">For example, the following class definitions would result in a Blog_BlogId parameter being expected in the stored procedures to insert and update Posts.</span></span>  

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

### <a name="overriding-the-defaults"></a><span data-ttu-id="c061b-137">既定値のオーバーライド</span><span class="sxs-lookup"><span data-stu-id="c061b-137">Overriding the Defaults</span></span>  

<span data-ttu-id="c061b-138">プライマリキープロパティへのパスをパラメーターメソッドに指定することで、クラスに含まれていない外部キーのパラメーターを変更できます。</span><span class="sxs-lookup"><span data-stu-id="c061b-138">You can change parameters for foreign keys that are not included in the class by supplying the path to the primary key property to the Parameter method.</span></span>  

``` csharp
modelBuilder
  .Entity<Post>()  
  .MapToStoredProcedures(s =>  
    s.Insert(i => i.Parameter(p => p.Blog.BlogId, "blog_id")));
```  

<span data-ttu-id="c061b-139">依存エンティティにナビゲーションプロパティがない場合 (つまり、</span><span class="sxs-lookup"><span data-stu-id="c061b-139">If you don’t have a navigation property on the dependent entity (i.e</span></span> <span data-ttu-id="c061b-140">投稿がありません)。その後、Association メソッドを使用してリレーションシップのもう一方の end を特定し、各キープロパティに対応するパラメーターを構成できます。</span><span class="sxs-lookup"><span data-stu-id="c061b-140">no Post.Blog property) then you can use the Association method to identify the other end of the relationship and then configure the parameters that correspond to each of the key property(s).</span></span>  

``` csharp
modelBuilder
  .Entity<Post>()  
  .MapToStoredProcedures(s =>  
    s.Insert(i => i.Navigation<Blog>(  
      b => b.Posts,  
      c => c.Parameter(b => b.BlogId, "blog_id"))));
```  

## <a name="concurrency-tokens"></a><span data-ttu-id="c061b-141">コンカレンシー トークン</span><span class="sxs-lookup"><span data-stu-id="c061b-141">Concurrency Tokens</span></span>  

<span data-ttu-id="c061b-142">ストアドプロシージャの更新および削除では、同時実行が必要になる場合もあります。</span><span class="sxs-lookup"><span data-stu-id="c061b-142">Update and delete stored procedures may also need to deal with concurrency:</span></span>  

- <span data-ttu-id="c061b-143">エンティティに同時実行トークンが含まれている場合、ストアドプロシージャには、更新または削除された行数 (影響を受ける行) を返す出力パラメーターをオプションで指定できます。</span><span class="sxs-lookup"><span data-stu-id="c061b-143">If the entity contains concurrency tokens, the stored procedure can optionally have an output parameter that returns the number of rows updated/deleted (rows affected).</span></span> <span data-ttu-id="c061b-144">このようなパラメーターは、RowsAffectedParameter メソッドを使用して構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c061b-144">Such a parameter must be configured using the RowsAffectedParameter method.</span></span>  
<span data-ttu-id="c061b-145">既定では、EF は ExecuteNonQuery からの戻り値を使用して、影響を受けた行の数を確認します。</span><span class="sxs-lookup"><span data-stu-id="c061b-145">By default EF uses the return value from ExecuteNonQuery to determine how many rows were affected.</span></span> <span data-ttu-id="c061b-146">影響を受ける行を指定すると、ストアドプロシージャで任意のロジックを実行して、実行の終了時に ExecuteNonQuery の戻り値が誤って (EF の観点から) 不正確になるような場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="c061b-146">Specifying a rows affected output parameter is useful if you perform any logic in your sproc that would result in the return value of ExecuteNonQuery being incorrect (from EF's perspective) at the end of execution.</span></span>  
- <span data-ttu-id="c061b-147">各同時実行トークンには、 **\<property_name\>_Original**という名前のパラメーターがあります (Timestamp_Original など)。</span><span class="sxs-lookup"><span data-stu-id="c061b-147">For each concurrency token there will be a parameter named **\<property_name\>_Original** (for example, Timestamp_Original ).</span></span> <span data-ttu-id="c061b-148">これには、このプロパティの元の値 (データベースからクエリを実行したときの値) が渡されます。</span><span class="sxs-lookup"><span data-stu-id="c061b-148">This will be passed the original value of this property – the value when queried from the database.</span></span>  
    - <span data-ttu-id="c061b-149">タイムスタンプなど、データベースによって計算される同時実行トークンには、元の値パラメーターのみが含まれます。</span><span class="sxs-lookup"><span data-stu-id="c061b-149">Concurrency tokens that are computed by the database – such as timestamps – will only have an original value parameter.</span></span>  
    - <span data-ttu-id="c061b-150">同時実行トークンとして設定された非計算プロパティには、更新プロシージャの新しい値のパラメーターも含まれます。</span><span class="sxs-lookup"><span data-stu-id="c061b-150">Non-computed properties that are set as concurrency tokens will also have a parameter for the new value in the update procedure.</span></span> <span data-ttu-id="c061b-151">これには、新しい値に既に説明した名前付け規則が使用されます。</span><span class="sxs-lookup"><span data-stu-id="c061b-151">This uses the naming conventions already discussed for new values.</span></span> <span data-ttu-id="c061b-152">このようなトークンの例としては、ブログの URL を同時実行トークンとして使用することが挙げられます。この新しい値は、コードによって新しい値に更新できるためです (データベースによってのみ更新されるタイムスタンプトークンとは異なります)。</span><span class="sxs-lookup"><span data-stu-id="c061b-152">An example of such a token would be using a Blog's URL as a concurrency token, the new value is required because this can be updated to a new value by your code (unlike a Timestamp token which is only updated by the database).</span></span>  

<span data-ttu-id="c061b-153">これは、クラスの例であり、timestamp 同時実行トークンを使用してストアドプロシージャを更新します。</span><span class="sxs-lookup"><span data-stu-id="c061b-153">This is an example class and update stored procedure with a timestamp concurrency token.</span></span>  

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

<span data-ttu-id="c061b-154">次に、クラスの例と、計算されない同時実行トークンを使用したストアドプロシージャの更新を示します。</span><span class="sxs-lookup"><span data-stu-id="c061b-154">Here is an example class and update stored procedure with non-computed concurrency token.</span></span>  

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

### <a name="overriding-the-defaults"></a><span data-ttu-id="c061b-155">既定値のオーバーライド</span><span class="sxs-lookup"><span data-stu-id="c061b-155">Overriding the Defaults</span></span>  

<span data-ttu-id="c061b-156">必要に応じて、影響を受ける行を指定することもできます。</span><span class="sxs-lookup"><span data-stu-id="c061b-156">You can optionally introduce a rows affected parameter.</span></span>  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.RowsAffectedParameter("rows_affected")));
```  

<span data-ttu-id="c061b-157">データベースで計算された同時実行トークンの場合–元の値のみが渡されます。標準パラメーターの名前変更メカニズムを使用して、元の値のパラメーターの名前を変更できます。</span><span class="sxs-lookup"><span data-stu-id="c061b-157">For database computed concurrency tokens – where only the original value is passed – you can just use the standard parameter renaming mechanism to rename the parameter for the original value.</span></span>  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.Parameter(b => b.Timestamp, "blog_timestamp")));
```  

<span data-ttu-id="c061b-158">計算されていない同時実行トークンの場合–元の値と新しい値の両方が渡されます。パラメーターのオーバーロードを使用すると、各パラメーターに名前を指定できます。</span><span class="sxs-lookup"><span data-stu-id="c061b-158">For non-computed concurrency tokens – where both the original and new value are passed – you can use an overload of Parameter that allows you to supply a name for each parameter.</span></span>  

``` csharp
modelBuilder
 .Entity<Blog>()
 .MapToStoredProcedures(s => s.Update(u => u.Parameter(b => b.Url, "blog_url", "blog_original_url")));
```  

## <a name="many-to-many-relationships"></a><span data-ttu-id="c061b-159">多対多リレーションシップ</span><span class="sxs-lookup"><span data-stu-id="c061b-159">Many to Many Relationships</span></span>  

<span data-ttu-id="c061b-160">このセクションの例として、次のクラスを使用します。</span><span class="sxs-lookup"><span data-stu-id="c061b-160">We’ll use the following classes as an example in this section.</span></span>  

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

<span data-ttu-id="c061b-161">多対多リレーションシップは、次の構文を使用してストアドプロシージャにマップできます。</span><span class="sxs-lookup"><span data-stu-id="c061b-161">Many to many relationships can be mapped to stored procedures with the following syntax.</span></span>  

``` csharp
modelBuilder  
  .Entity<Post>()  
  .HasMany(p => p.Tags)  
  .WithMany(t => t.Posts)  
  .MapToStoredProcedures();
```  

<span data-ttu-id="c061b-162">他の構成が指定されていない場合、既定では、次のストアドプロシージャ図形が使用されます。</span><span class="sxs-lookup"><span data-stu-id="c061b-162">If no other configuration is supplied then the following stored procedure shape is used by default.</span></span>  

- <span data-ttu-id="c061b-163">\<type_one という**名前の 2**つのストアドプロシージャ **\<type_two**\>_Insert\>\<type_one\>\<type_two\>_Delete PostTag_Insert します。</span><span class="sxs-lookup"><span data-stu-id="c061b-163">Two stored procedures named **\<type_one\>\<type_two\>_Insert** and **\<type_one\>\<type_two\>_Delete** (for example, PostTag_Insert and PostTag_Delete).</span></span>  
- <span data-ttu-id="c061b-164">パラメーターは、各型のキー値になります。</span><span class="sxs-lookup"><span data-stu-id="c061b-164">The parameters will be the key value(s) for each type.</span></span> <span data-ttu-id="c061b-165">Type_name\<れる各パラメーターの名前 **\>_\<property_name**\>(Post_PostId、Tag_TagId など)。</span><span class="sxs-lookup"><span data-stu-id="c061b-165">The name of each parameter being **\<type_name\>_\<property_name\>** (for example, Post_PostId and Tag_TagId).</span></span>

<span data-ttu-id="c061b-166">Insert および update ストアドプロシージャの例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="c061b-166">Here are example insert and update stored procedures.</span></span>  

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

### <a name="overriding-the-defaults"></a><span data-ttu-id="c061b-167">既定値のオーバーライド</span><span class="sxs-lookup"><span data-stu-id="c061b-167">Overriding the Defaults</span></span>  

<span data-ttu-id="c061b-168">プロシージャ名とパラメーター名は、エンティティストアドプロシージャと同様の方法で構成できます。</span><span class="sxs-lookup"><span data-stu-id="c061b-168">The procedure and parameter names can be configured in a similar way to entity stored procedures.</span></span>  

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
