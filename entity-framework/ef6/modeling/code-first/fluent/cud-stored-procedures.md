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
# <a name="code-first-insert-update-and-delete-stored-procedures"></a>ストアドプロシージャの挿入、更新、および削除の Code First
> [!NOTE]
> **EF6 以降のみ** - このページで説明する機能、API などは、Entity Framework 6 で導入されました。 以前のバージョンを使用している場合、一部またはすべての情報は適用されません。  

既定では、Code First は、直接テーブルアクセスを使用して insert、update、および delete コマンドを実行するようにすべてのエンティティを構成します。 EF6 以降では、モデル内の一部またはすべてのエンティティに対してストアドプロシージャを使用するように Code First モデルを構成できます。  

## <a name="basic-entity-mapping"></a>基本エンティティのマッピング  

Fluent API を使用して、挿入、更新、削除のストアドプロシージャを使用することを選択できます。  

``` csharp
modelBuilder
  .Entity<Blog>()
  .MapToStoredProcedures();
```  

この操作を行うと、Code First によって、データベース内のストアドプロシージャの想定される構造を構築するために何らかの規則が使用されます。  

- ** \<type_name\> _Insert**、 ** \<type_name\> _Update** 、 ** \<type_name\> _Delete**という名前の3つのストアドプロシージャ (たとえば、Blog_Insert、Blog_Update、Blog_Delete)。  
- パラメーター名はプロパティ名に対応します。  
  > [!NOTE]
  > HasColumnName () または Column 属性を使用して特定のプロパティの列の名前を変更する場合、この名前はプロパティ名の代わりにパラメーターに使用されます。  
- **Insert ストアドプロシージャ** には、store generated (id または計算済み) としてマークされているものを除き、すべてのプロパティのパラメーターがあります。 ストアドプロシージャは、ストアが生成する各プロパティの列を含む結果セットを返す必要があります。  
- **Update ストアドプロシージャ** には、ストアで生成されたパターン ' 計算済み ' でマークされているものを除き、すべてのプロパティのパラメーターがあります。 一部の同時実行トークンには、元の値のパラメーターが必要です。詳細については、後述の「 *同時実行トークン* 」セクションを参照してください。 ストアドプロシージャは、計算されたプロパティごとに列を含む結果セットを返す必要があります。  
- **Delete ストアドプロシージャに** は、エンティティのキー値のパラメーター (または、エンティティに複合キーがある場合は複数のパラメーター) が必要です。 さらに、delete プロシージャには、ターゲットテーブルのすべての独立した関連付け外部キー (エンティティで宣言された対応する外部キープロパティがないリレーションシップ) のパラメーターも含まれている必要があります。 一部の同時実行トークンには、元の値のパラメーターが必要です。詳細については、後述の「 *同時実行トークン* 」セクションを参照してください。  

例として、次のクラスを使用します。  

``` csharp
public class Blog  
{  
  public int BlogId { get; set; }  
  public string Name { get; set; }  
  public string Url { get; set; }  
}
```  

既定のストアドプロシージャは次のようになります。  

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

### <a name="overriding-the-defaults"></a>既定値のオーバーライド  

既定で構成されていたものの一部またはすべてを上書きできます。  

1つ以上のストアドプロシージャの名前を変更できます。 この例では、update ストアドプロシージャの名前を変更します。  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.HasName("modify_blog")));
```  

この例では、3つのストアドプロシージャの名前をすべて変更します。  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.HasName("modify_blog"))  
     .Delete(d => d.HasName("delete_blog"))  
     .Insert(i => i.HasName("insert_blog")));
```  

これらの例では、呼び出しは連結されていますが、ラムダブロック構文を使用することもできます。  

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

次の例では、update ストアドプロシージャの "ブログ Id" プロパティのパラメーターの名前を変更します。  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.Parameter(b => b.BlogId, "blog_id")));
```  

これらの呼び出しはすべて chainable とコンポーザブルです。 次に、3つのストアドプロシージャとそのパラメーターの名前を変更する例を示します。  

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

また、データベースによって生成された値を含む結果セット内の列の名前を変更することもできます。  

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

## <a name="relationships-without-a-foreign-key-in-the-class-independent-associations"></a>クラスに外部キーを持たないリレーションシップ (独立した関連付け)  

外部キープロパティがクラス定義に含まれている場合、対応するパラメーターの名前を他のプロパティと同じ方法で変更できます。 クラスの外部キープロパティを使用せずにリレーションシップが存在する場合、既定のパラメーター名は** \<navigation_property_name\> _ \<primary_key_name\> **です。  

たとえば、次のクラス定義では、ストアドプロシージャ内で投稿を挿入および更新するために Blog_BlogId パラメーターが想定されています。  

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

### <a name="overriding-the-defaults"></a>既定値のオーバーライド  

プライマリキープロパティへのパスをパラメーターメソッドに指定することで、クラスに含まれていない外部キーのパラメーターを変更できます。  

``` csharp
modelBuilder
  .Entity<Post>()  
  .MapToStoredProcedures(s =>  
    s.Insert(i => i.Parameter(p => p.Blog.BlogId, "blog_id")));
```  

依存エンティティにナビゲーションプロパティがない場合 (つまり、 投稿がありません)。その後、Association メソッドを使用してリレーションシップのもう一方の end を特定し、各キープロパティに対応するパラメーターを構成できます。  

``` csharp
modelBuilder
  .Entity<Post>()  
  .MapToStoredProcedures(s =>  
    s.Insert(i => i.Navigation<Blog>(  
      b => b.Posts,  
      c => c.Parameter(b => b.BlogId, "blog_id"))));
```  

## <a name="concurrency-tokens"></a>コンカレンシー トークン  

ストアドプロシージャの更新および削除では、同時実行が必要になる場合もあります。  

- エンティティに同時実行トークンが含まれている場合、ストアドプロシージャには、更新または削除された行数 (影響を受ける行) を返す出力パラメーターをオプションで指定できます。 このようなパラメーターは、RowsAffectedParameter メソッドを使用して構成する必要があります。  
既定では、EF は ExecuteNonQuery からの戻り値を使用して、影響を受けた行の数を確認します。 影響を受ける行を指定すると、ストアドプロシージャで任意のロジックを実行して、実行の終了時に ExecuteNonQuery の戻り値が誤って (EF の観点から) 不正確になるような場合に便利です。  
- 同時実行トークンごとに、 ** \<property_name\> _Original**という名前のパラメーターがあります (Timestamp_Original など)。 これには、このプロパティの元の値 (データベースからクエリを実行したときの値) が渡されます。  
    - タイムスタンプなど、データベースによって計算される同時実行トークンには、元の値パラメーターのみが含まれます。  
    - 同時実行トークンとして設定された非計算プロパティには、更新プロシージャの新しい値のパラメーターも含まれます。 これには、新しい値に既に説明した名前付け規則が使用されます。 このようなトークンの例としては、ブログの URL を同時実行トークンとして使用することが挙げられます。この新しい値は、コードによって新しい値に更新できるためです (データベースによってのみ更新されるタイムスタンプトークンとは異なります)。  

これは、クラスの例であり、timestamp 同時実行トークンを使用してストアドプロシージャを更新します。  

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

次に、クラスの例と、計算されない同時実行トークンを使用したストアドプロシージャの更新を示します。  

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

### <a name="overriding-the-defaults"></a>既定値のオーバーライド  

必要に応じて、影響を受ける行を指定することもできます。  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.RowsAffectedParameter("rows_affected")));
```  

データベースで計算された同時実行トークンの場合–元の値のみが渡されます。標準パラメーターの名前変更メカニズムを使用して、元の値のパラメーターの名前を変更できます。  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.Parameter(b => b.Timestamp, "blog_timestamp")));
```  

計算されていない同時実行トークンの場合–元の値と新しい値の両方が渡されます。パラメーターのオーバーロードを使用すると、各パラメーターに名前を指定できます。  

``` csharp
modelBuilder
 .Entity<Blog>()
 .MapToStoredProcedures(s => s.Update(u => u.Parameter(b => b.Url, "blog_url", "blog_original_url")));
```  

## <a name="many-to-many-relationships"></a>多対多リレーションシップ  

このセクションの例として、次のクラスを使用します。  

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

多対多リレーションシップは、次の構文を使用してストアドプロシージャにマップできます。  

``` csharp
modelBuilder  
  .Entity<Post>()  
  .HasMany(p => p.Tags)  
  .WithMany(t => t.Posts)  
  .MapToStoredProcedures();
```  

他の構成が指定されていない場合、既定では、次のストアドプロシージャ図形が使用されます。  

- ** \<type_one\> \<type_two\> _Insert**と** \<type_one\> \<type_two\> _Delete**という名前の2つのストアドプロシージャ (たとえば、PostTag_Insert と PostTag_Delete)。  
- パラメーターは、各型のキー値になります。 各パラメーターの名前は** \<type_name\> _ \<property_name\> **です (たとえば、Post_PostId と Tag_TagId)。

Insert および update ストアドプロシージャの例を次に示します。  

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

### <a name="overriding-the-defaults"></a>既定値のオーバーライド  

プロシージャ名とパラメーター名は、エンティティストアドプロシージャと同様の方法で構成できます。  

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
