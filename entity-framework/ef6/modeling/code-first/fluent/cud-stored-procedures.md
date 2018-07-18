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
# <a name="code-first-insert-update-and-delete-stored-procedures"></a>最初のコード挿入、更新、および Delete ストアド プロシージャ
> [!NOTE]
> **EF6 以降のみ** - このページで説明する機能、API などは、Entity Framework 6 で導入されました。 以前のバージョンを使用している場合、一部またはすべての情報は適用されません。  

既定では、Code First は構成すべてのエンティティの挿入を実行、更新、およびテーブルに直接アクセスを使用してコマンドを削除します。 EF6 で起動するストアド プロシージャを使用して、モデルの一部またはすべてのエンティティの Code First モデルを構成できます。  

## <a name="basic-entity-mapping"></a>基本的なエンティティのマッピング  

Insert ストアド プロシージャの使用を選択し、更新および Fluent API を使用して削除できます。  

``` csharp
modelBuilder
  .Entity<Blog>()
  .MapToStoredProcedures();
```  

これを行うには、いくつかの規則を使用して、データベースのストアド プロシージャの予想される形状を構築する Code First が発生します。  

- 3 つのストアド プロシージャの名前付き **\<type_name\>(_i)**、  **\<type_name\>更新 (_u)** と**\<種類名前\>削除 (_d)** (Blog_Insert、Blog_Update および Blog_Delete など)。  
- パラメーター名は、プロパティ名に対応します。  
  > [!NOTE]
  > 特定のプロパティの列の名前を変更する HasColumnName() または列の属性を使用する場合、この名前は、プロパティ名の代わりにパラメーターに使用されます。  
- **Insert ストアド プロシージャ**ストア生成としてマークされているものを除く、すべてのプロパティのパラメーターになります (identity または計算)。 ストアド プロシージャは、生成されたストアの各プロパティの列を含む結果セットを返す必要があります。  
- **更新ストアド プロシージャ**'Computed' のストア生成パターンが付いているものを除く、すべてのプロパティのパラメーターになります。 一部の同時実行トークンは、元の値のパラメーターが必要を参照してください、*同時実行制御トークン*詳細については後述します。 ストアド プロシージャは、計算の各プロパティの列を含む結果セットを返す必要があります。  
- **ストアド プロシージャを削除**エンティティ (または複数のパラメーター エンティティが複合キーを持つ場合) のキー値のパラメーターが必要です。 さらに、delete プロシージャも必要な独立アソシエーションの外部キーのパラメーター (エンティティで宣言されている、対応する外部キー プロパティがないリレーションシップ) 対象のテーブルです。 一部の同時実行トークンは、元の値のパラメーターが必要を参照してください、*同時実行制御トークン*詳細については後述します。  

例として、次のクラスの使用。  

``` csharp
public class Blog  
{  
  public int BlogId { get; set; }  
  public string Name { get; set; }  
  public string Url { get; set; }  
}
```  

格納されている既定のプロシージャは次のようになります。  

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

### <a name="overriding-the-defaults"></a>既定の設定をオーバーライドします。  

既定で構成された内容の一部またはすべてをオーバーライドすることができます。  

1 つまたは複数のストアド プロシージャの名前を変更することができます。 この例では、更新ストアド プロシージャのみを変更します。  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.HasName("modify_blog")));
```  

この例では、次の 3 つすべてのストアド プロシージャを変更します。  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.HasName("modify_blog"))  
     .Delete(d => d.HasName("delete_blog"))  
     .Insert(i => i.HasName("insert_blog")));
```  

これらの例で、呼び出しは、連結しますが、ラムダのブロックの構文を使用することもできます。  

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

この例では、BlogId プロパティ更新ストアド プロシージャのパラメーターを変更します。  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.Parameter(b => b.BlogId, "blog_id")));
```  

これらの呼び出しは、すべてのチェーンで構成可能なは。 次の 3 つすべてのストアド プロシージャとそのパラメーターの名前を変更する例を示します。  

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

データベースで生成された値を含む結果セット内の列の名前を変更することもできます。  

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

## <a name="relationships-without-a-foreign-key-in-the-class-independent-associations"></a>クラス (独立した関連付け) で外部キーのないリレーションシップ  

外部キー プロパティは、クラス定義に含めるときは、他のプロパティと同様に、対応するパラメーターを変更できます。 既定のパラメーター名は、リレーションシップ、クラスの外部キー プロパティが存在しますが、ときに **\<navigation_property_name\>_\<primary_key_name\>** します。  

たとえば、次のクラス定義は、挿入や投稿を更新するストアド プロシージャで想定されている Blog_BlogId パラメーターになります。  

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

### <a name="overriding-the-defaults"></a>既定の設定をオーバーライドします。  

パラメーターをメソッドに主キーのプロパティへのパスを指定することで、クラスに含まれていない外部キーのパラメーターを変更することができます。  

``` csharp
modelBuilder
  .Entity<Post>()  
  .MapToStoredProcedures(s =>  
    s.Insert(i => i.Parameter(p => p.Blog.BlogId, "blog_id")));
```  

ナビゲーション プロパティを依存エンティティ (つまり、されていない場合 Post.Blog プロパティが) Association メソッドを使用して、リレーションシップの他方の end を特定し、キーのプロパティのそれぞれに対応するパラメーターを構成できません。  

``` csharp
modelBuilder
  .Entity<Post>()  
  .MapToStoredProcedures(s =>  
    s.Insert(i => i.Navigation<Blog>(  
      b => b.Posts,  
      c => c.Parameter(b => b.BlogId, "blog_id"))));
```  

## <a name="concurrency-tokens"></a>同時実行制御トークン  

Update、delete ストアド プロシージャは、同時実行を処理する必要もあります。  

- エンティティに同時実行制御トークンが含まれている場合、ストアド プロシージャは必要に応じて更新/削除された行 (行処理されました) の数を返す出力パラメーターを持つことができます。 RowsAffectedParameter メソッドを使用して、そのようなパラメーターを構成する必要があります。  
既定では、EF は、影響を受けた行の数を決定するのに ExecuteNonQuery からの戻り値を使用します。 EF の観点から不適切になる ExecuteNonQuery の戻り値のある、ストアド プロシージャのロジックを実行する場合に役立ちます。 影響を受ける行の出力パラメーターを指定する実行の最後にします。  
- 各同時実行制御トークンがありますがという名前のパラメーターをする **\<property_name\>_Original** (Timestamp_Original など)。 これは、データベースから照会されたときに、値は – このプロパティの元の値渡されます。  
    - – タイムスタンプ – などのデータベースで計算される同時実行トークンは、元の値のパラメーターを必要のみがあります。  
    - 更新処理では、同時実行トークンとして設定されている非計算のプロパティに新しい値のパラメーターがあります。 これは、既に説明した新しい値の名前付け規則を使用します。 このようなトークンの例を使用する場合、ブログの URL、同時実行トークンとして、この (データベースでのみ更新されるタイムスタンプ トークン) とは異なり、コードで新しい値に更新できるため、新しい値が必要です。  

これは、例ではクラスし、タイムスタンプの同時実行トークンを使用してストアド プロシージャを更新します。  

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

これは、クラスし、非計算の同時実行トークンを使用してストアド プロシージャを更新します。  

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

### <a name="overriding-the-defaults"></a>既定の設定をオーバーライドします。  

必要に応じて処理行数パラメーターを導入することができます。  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.RowsAffectedParameter("rows_affected")));
```  

– 元の値のみが渡されるが – 計算データベース同時実行トークンの元の値パラメーターの名前を変更するのにだけのメカニズムの名前を変更する標準的なパラメーターを使用できます。  

``` csharp
modelBuilder  
  .Entity<Blog>()  
  .MapToStoredProcedures(s =>  
    s.Update(u => u.Parameter(b => b.Timestamp, "blog_timestamp")));
```  

– 場所両方元と新しい値が渡される – 非計算の同時実行トークンを使用すると、各パラメーターの名前を指定するパラメーターのオーバー ロードを使用できます。  

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

多対多の関係は、次の構文を使用したストアド プロシージャにマップすることができます。  

``` csharp
modelBuilder  
  .Entity<Post>()  
  .HasMany(p => p.Tags)  
  .WithMany(t => t.Posts)  
  .MapToStoredProcedures();
```  

その他の構成が指定されていない場合、既定では、次のストアド プロシージャの図形が使用されます。  

- 2 つのストアド プロシージャの名前付き **\<type_one\>\<type_two\>(_i)** と **\<type_one\>\<type_two\>削除 (_d)** (PostTag_Insert および PostTag_Delete など)。  
- パラメーターは、各型のキー値になります。 各パラメーターの名前**\<type_name\>_\<property_name\>** (Post_PostId および Tag_TagId など)。

次の例に示しますを挿入し、ストアド プロシージャを更新します。  

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

### <a name="overriding-the-defaults"></a>既定の設定をオーバーライドします。  

プロシージャとパラメーターの名前は、エンティティがストアド プロシージャと同様の方法で構成できます。  

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
