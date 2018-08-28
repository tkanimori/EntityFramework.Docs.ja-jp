---
title: コードの最初のデータ注釈 - EF6
author: divega
ms.date: 2016-10-23
ms.assetid: 80abefbd-23c9-4fce-9cd3-520e5df9856e
ms.openlocfilehash: 0ab66afa3babafe657b3ddb32c02c3fba0ae310e
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42994587"
---
# <a name="code-first-data-annotations"></a>Code First のデータ注釈
> [!NOTE]
> **EF4.1 以降のみ**-機能、Api、Entity Framework 4.1 で導入されたなどのこのページで説明します。 以前のバージョンを使用している場合、一部またはすべての情報は適用されません。

このページのコンテンツの出典元し、Julie Lerman が最初に書き込まれた情報の記事 (\<http://thedatafarm.com>)します。

Entity Framework Code First EF が、クエリを実行に依存するモデルを表す独自のドメイン クラスを使用できるように追跡と変更関数を更新しています。 コードは、最初の構成に規則と呼ばれるプログラミング パターンを活用します。 場合は、そのコードはまず、クラスが EF で使用される規則に従うこと。 その場合は、EF は、ジョブを実行する必要がある詳細を使用することになります。 ただし、クラスは、これらの規則に従っていない、構成に必要な情報に EF を提供するクラスを追加する機能があります。

コードは 2 つの方法でこれらの構成をクラスに追加します。 まず、します。 DataAnnotations と呼ばれる単純な属性を使用して 1 つは、もう一方は Fluent API 構成を強制的に、コードで記述する方法を提供するには最初にコードを使用します。

この記事では、System.ComponentModel.DataAnnotations 名前空間) の「DataAnnotations を使用して、最も一般的に必要な構成を強調表示、のクラスを構成する説明します。 DataAnnotations がこれらのアプリケーションのクライアント側の検証のための同じ注釈を活用できる ASP.NET MVC などの .NET アプリケーションの番号で考えることもできます。


## <a name="the-model"></a>モデル

紹介しますコード クラスの単純なペアの最初の DataAnnotations: ブログや投稿します。

``` csharp
    public class Blog
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public string BloggerName { get; set;}
        public virtual ICollection<Post> Posts { get; set; }
    }

    public class Post
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public DateTime DateCreated { get; set; }
        public string Content { get; set; }
        public int BlogId { get; set; }
        public ICollection<Comment> Comments { get; set; }
    }
```

ブログや投稿クラスは簡単にコードの最初の規則に従うし、EF とその操作を調整する必要はありません。 ただし、EF に、クラスとがマップされるデータベースについての詳細を提供する、注釈を使用することもできます。

 

## <a name="key"></a>キー

Entity Framework は、エンティティの追跡に使用するキー値を持つすべてのエンティティに依存します。 コードは、最初に依存している表記規則の 1 つは、どのプロパティは、コードの最初のクラスの各キーを意味する方法です。 その規則は、"Id"または"BlogId"など"Id", とクラスの名前とを組み合わせたものをという名前のプロパティを検索します。 プロパティは、データベースの主キー列にマップされます。

ブログと Post の両方のクラスでは、この規則に従います。 しかししていない場合でしょうか。 ブログで名前を使用する場合*PrimaryTrackingKey*代わりに、またはでも*foo*でしょうか。 コードが最初にこの規則に一致するプロパティでは見つからない場合は、キー プロパティに必要な Entity Framework の要件のため例外がスローされます。 キーの注釈を使用すると、EntityKey として使用するのにプロパティを指定します。

``` csharp
    public class Blog
    {
        [Key]
        public int PrimaryTrackingKey { get; set; }
        public string Title { get; set; }
        public string BloggerName { get; set;}
        public virtual ICollection<Post> Posts { get; set; }
    }
```

する場合はデータベースの生成機能は、最初にコードを使用して、ブログの表に、既定では、Id としても定義されている PrimaryTrackingKey という名前の主キー列になります。

![jj591583_figure01](~/ef6/media/jj591583-figure01.png)

### <a name="composite-keys"></a>複合キー

Entity Framework には、複合キーは、1 つ以上のプロパティで構成される主キーがサポートしています。 たとえばのプライマリ キーを持つ PassportNumber と IssuingCountry の組み合わせは、Passport クラスがある可能性があります。

``` csharp
    public class Passport
    {
        [Key]
        public int PassportNumber { get; set; }
        [Key]
        public string IssuingCountry { get; set; }
        public DateTime Issued { get; set; }
        public DateTime Expires { get; set; }
    }
```

InvalidOperationExceptions という、; を取得するモデルに上記のクラスを使用した場合

*複合主キーの順序付けの種類 'Passport' を決定できません。複合主キーの順序を指定するのに、ColumnAttribute または HasKey メソッドを使用します。*

複合キーがある場合は、Entity Framework では、キーのプロパティの順序を定義する必要があります。 順序を指定する列の注釈を使用してこれが実行できます。

>[!NOTE]
> 順序の値は相対 (なくインデックス ベース) 任意の値を使用できるようにします。 たとえば、100 および 200 は 1 と 2 の代わりに許容になります。

``` csharp
    public class Passport
    {
        [Key]
        [Column(Order=1)]
        public int PassportNumber { get; set; }
        [Key]
        [Column(Order = 2)]
        public string IssuingCountry { get; set; }
        public DateTime Issued { get; set; }
        public DateTime Expires { get; set; }
    }
```

複合外部キーを持つエンティティがある場合は、同じ列の対応するプライマリ キーのプロパティに使用する順序を指定する必要があります。

同じである正確な値に割り当てられている必要があります、相対的な順序付けのみ外部キー プロパティ内で**順序**と一致する必要はありません。 たとえば、次のクラスで 3 と 4 される可能性があります 1 と 2 の代わりにします。

``` csharp
    public class PassportStamp
    {
        [Key]
        public int StampId { get; set; }
        public DateTime Stamped { get; set; }
        public string StampingCountry { get; set; }

        [ForeignKey("Passport")]
        [Column(Order = 1)]
        public int PassportNumber { get; set; }

        [ForeignKey("Passport")]
        [Column(Order = 2)]
        public string IssuingCountry { get; set; }

        public Passport Passport { get; set; }
    }
```

## <a name="required"></a>必須

必要な注釈は、特定のプロパティが必要である EF に指示します。

Title プロパティに必要な追加すると、プロパティは、これでデータを持つようにするための EF (および MVC) が実行されます。

``` csharp
    [Required]
    public string Title { get; set; }
```

ない追加のないアプリケーションのコードやマークアップを変更、MVC アプリケーションのプロパティおよび注釈の名前を使用してメッセージの構築も動的に、クライアント側の検証は実行します。

![jj591583_figure02](~/ef6/media/jj591583-figure02.png)

必須の属性には、マッピングされたプロパティを null 非許容のことで生成されたデータベースも影響します。 [タイトル] フィールドが"not null"に変更されていることに注意してください。

>[!NOTE]
> 場合によっては、プロパティが必要な場合でも、null 非許容されるデータベース内の列の可能なないられます。 たとえば、TPH 継承の戦略のデータを複数の種類を使用してが格納されている場合、1 つのテーブル。 派生型に必要なプロパティが含まれている場合、列にできない null 非許容のこのプロパティは、階層内のすべての型であるためです。

 

![jj591583_figure03](~/ef6/media/jj591583-figure03.png)

 

## <a name="maxlength-and-minlength"></a>MaxLength、MinLength

MaxLength、MinLength 属性を必須にしたのと同様、追加のプロパティの検証を指定できます。

長さの要件と BloggerName を次に示します。 この例では、属性を結合する方法も示します。

``` csharp
    [MaxLength(10),MinLength(5)]
    public string BloggerName { get; set; }
```

MaxLength 注釈は、プロパティの長さを 10 に設定して、データベースに影響します。

![jj591583_figure04](~/ef6/media/jj591583-figure04.png)

クライアント側の注釈を MVC と EF 4.1 サーバー側の注釈は両方に従ってこの検証でエラー メッセージを動的にもう一度ビルド:「BloggerName フィールドは、'10' の最大長の文字列または配列型をある必要があります」そのメッセージは、少し長くします。 多くの注釈を使用して、エラー メッセージの属性を持つエラー メッセージを指定できます。

``` csharp
    [MaxLength(10, ErrorMessage="BloggerName must be 10 characters or less"),MinLength(5)]
    public string BloggerName { get; set; }
```

注釈が必要なエラー メッセージを指定することもできます。

![jj591583_figure05](~/ef6/media/jj591583-figure05.png)

 

## <a name="notmapped"></a>NotMapped

コードの最初の規則は、サポートされているデータ型のすべてのプロパティが、データベースで表されていることを決定します。 これは、アプリケーション内のケースでは常にします。 たとえば、タイトルと BloggerName フィールドに基づくコードを作成するブログ クラスでプロパティがある可能性があります。 このプロパティは、動的に作成できるし、格納する必要はありません。 この BlogCode プロパティなど NotMapped 注釈を使用してデータベースにマップされていない任意のプロパティをマークすることができます。

``` csharp
    [NotMapped]
    public string BlogCode
    {
        get
        {
            return Title.Substring(0, 1) + ":" + BloggerName.Substring(0, 1);
        }
    }
```

 

## <a name="complextype"></a>ComplexType

一連のクラス間で、ドメイン エンティティを記述し、レイヤーの完全なエンティティを記述するそれらのクラスに珍しくありません。 たとえば、モデルに BlogDetails と呼ばれるクラスを追加する可能性があります。

``` csharp
    public class BlogDetails
    {
        public DateTime? DateCreated { get; set; }

        [MaxLength(250)]
        public string Description { get; set; }
    }
```

BlogDetails にあらゆる種類のキー プロパティがないことに注意してください。 ドメイン駆動設計では、BlogDetails を値オブジェクトと呼びます。 Entity Framework は、複合型として値オブジェクトを参照します。  独自の複合型を追跡することはできません。

ただしブログ クラス、BlogDetails がブログ オブジェクトの一部として追跡のプロパティとして。 これを認識する最初のコードで、ComplexType として BlogDetails クラスをマークする必要があります。

``` csharp
    [ComplexType]
    public class BlogDetails
    {
        public DateTime? DateCreated { get; set; }

        [MaxLength(250)]
        public string Description { get; set; }
    }
```

今すぐそのブログ BlogDetails を表すブログ クラスでプロパティを追加できます。

``` csharp
        public BlogDetails BlogDetail { get; set; }
```

データベースには、ブログの表に、その BlogDetail プロパティに含まれるプロパティを含む、ブログのプロパティのすべて含まれます。 既定では、それぞれが付きます BlogDetail、複合型の名前。

![jj591583_figure06](~/ef6/media/jj591583-figure06.png)

もう 1 つの興味深いことには、DateCreated プロパティ、クラス内の null 非許容の日時として定義されますが、関連するデータベース フィールドを null 値を許容します。 データベース スキーマに影響する場合は、必要な注釈を使用する必要があります。

 

## <a name="concurrencycheck"></a>ConcurrencyCheck

ConcurrencyCheck 注釈の同時実行ユーザーが編集したり、エンティティを削除します。 データベースのチェックに使用する 1 つまたは複数のプロパティのフラグを設定することができます。 EF Designer を使用していた場合、プロパティの ConcurrencyMode を Fixed に設定します。 これは。

BloggerName プロパティに追加することによって ConcurrencyCheck のしくみを見てみましょう。

``` csharp
    [ConcurrencyCheck, MaxLength(10, ErrorMessage="BloggerName must be 10 characters or less"),MinLength(5)]
    public string BloggerName { get; set; }
```

BloggerName フィールドで ConcurrencyCheck 注釈により、SaveChanges が呼び出されたときにそのプロパティの元の値が更新に使用されます。 コマンドが、キーの値だけでなく、BloggerName の元の値もフィルター処理して適切な行を検索しようとします。  ここでは、コマンドには、PrimaryTrackingKey を含む行は更新を表示、データベースに送信される UPDATE コマンドの重要な部分は 1、BloggerName"Julie"元の値をそのブログがデータベースから取得されたときにいたのです。

``` SQL
    where (([PrimaryTrackingKey] = @4) and ([BloggerName] = @5))
    @4=1,@5=N'Julie'
```

そのブログのブログ作成者名が、その間にだれかが変更した場合は、この更新プログラムは失敗し、処理する必要がありますを DbUpdateConcurrencyException が表示されます。

 

## <a name="timestamp"></a>タイムスタンプ

同時実行チェック用 rowversion またはタイムスタンプ フィールドを使用する方が一般的です。 ConcurrencyCheck 注釈を使用するのではなく、プロパティの型がバイト配列である限り、特定のタイムスタンプの注釈を使用できます。 コード最初はタイムスタンプのプロパティと同様に処理、ConcurrencyCheck プロパティが、コードを生成するデータベース フィールドを null 非許容は確認も。 タイムスタンプ プロパティを 1 つは、特定のクラスでのみができます。

ブログ クラスに次のプロパティを追加します。

``` csharp
    [Timestamp]
    public Byte[] TimeStamp { get; set; }
```

最初にデータベース テーブルで null 非許容の timestamp 列を作成するコードの結果。

![jj591583_figure07](~/ef6/media/jj591583-figure07.png)

 

## <a name="table-and-column"></a>テーブルと列

Code First のデータベースを作成することは、場合は、テーブルと列を作成しているの名前を変更することがあります。 使用することできますも Code First 既存のデータベースでします。 常にテーブルと、データベース内の列の名前をクラスと、ドメイン内のプロパティの名前が一致する場合。

筆者のクラスがブログをという名前し、慣例により、コードまず前提としていますこれは、ブログをという名前のテーブルにマップされます。 そうでない場合は、テーブルの属性を持つテーブルの名前を指定できます。 ここで、注釈はことを指定して、テーブル名 InternalBlogs。

``` csharp
    [Table("InternalBlogs")]
    public class Blog
```

列の注釈は、マップされた列の属性を指定するときに複数のアデプトです。 名前、データ型またはテーブルの列が表示される順序もを伸ばすことができます。 列の属性の例を示します。

``` csharp
    [Column(“BlogDescription", TypeName="ntext")]
    public String Description {get;set;}
```

DataType DataAnnotation で列の TypeName 属性を混同しないでください。 データ型は、注釈、UI の使用は、Code First によって無視されます。

再生成後に次の表に示します。 テーブル名が InternalBlogs に変更し、複合型から列を説明 BlogDescription になります。 注釈の名前が指定されたため、コード最初は使用しません複合型の名前で列名を開始する規約。

![jj591583_figure08](~/ef6/media/jj591583-figure08.png)

 

## <a name="databasegenerated"></a>DatabaseGenerated

重要なデータベース機能は、プロパティが計算する機能があります。 Code First クラスにマッピングする場合は、計算列のテーブルが含まれている、それらの列を更新しようとする Entity Framework をしたくないです。 ただし、EF を挿入または更新されたデータを使用した後、データベースからそれらの値を返すようにします。 DatabaseGenerated 注釈を使用すると、計算済みの列挙型と共に、クラスでこれらのプロパティのフラグを設定します。 他の列挙型は None と Id。

``` csharp
    [DatabaseGenerated(DatabaseGenerationOption.Computed)]
    public DateTime DateCreated { get; set; }
```

それ以外の場合にのみ使用これコード最初ことはできません、計算列の数式を判断するため、既存のデータベースをポイントして、コードはまず、データベースを生成するときに、バイトまたはタイムスタンプ列で生成されたデータベースを使用できます。

既定では、読むことを整数であるキー プロパティは、データベースの id キーなります。 DatabaseGenerated を DatabaseGenerationOption.Identity に設定すると同じメッセージが表示されます。 Id キーを使用することをしない場合は、DatabaseGenerationOption.None に値を設定できます。

 

## <a name="index"></a>インデックス

> [!NOTE]
> **EF6.1 以降のみ**-Entity Framework 6.1 で、インデックスの属性が導入されました。 以前のバージョンを使用している場合、このセクションの情報は適用されません。

使用して 1 つまたは複数の列にインデックスを作成することができます、 **IndexAttribute**します。 データベースの作成時に、データベースに対応するインデックスの作成に EF を原因またはスキャフォールディング、対応する属性を 1 つまたは複数のプロパティに追加**CreateIndex** Code First Migrations を使用している場合に呼び出します。

たとえば、次のコードが作成されたインデックスになります。、**評価**の列、**投稿**データベース内のテーブル。

``` csharp
    public class Post
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }
        [Index]
        public int Rating { get; set; }
        public int BlogId { get; set; }
    }
```

既定では、インデックスの名前は**IX\_&lt;プロパティ名&gt;** (IX\_上の例では評価)。 ただし、インデックスの名前も指定できます。 次の例では、インデックスに名前を付けることを指定します**PostRatingIndex**します。

``` csharp
    [Index("PostRatingIndex")]
    public int Rating { get; set; }
```

既定では、インデックスが一意でない、使用することが、 **IsUnique**という名前のインデックスが一意であることを指定するパラメーター。 次の例の一意のインデックスが導入されて、**ユーザー**のログイン名。

``` csharp
    public class User
    {
        public int UserId { get; set; }

        [Index(IsUnique = true)]
        [StringLength(200)]
        public string Username { get; set; }

        public string DisplayName { get; set; }
    }
```

### <a name="multiple-column-indexes"></a>複数列のインデックス

複数の列にまたがるインデックスは、指定されたテーブルの複数のインデックス注釈内で同じ名前を使用して指定されます。 複数列のインデックスを作成するときに、インデックスの列の順序を指定する必要があります。 次のコードが複数列インデックスを作成するなど、**評価**と**BlogId**と呼ばれる**IX\_BlogAndRating**します。 **BlogId**は、インデックスの最初の列と**評価**2 つ目は、します。

``` csharp
    public class Post
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public string Content { get; set; }
        [Index("IX_BlogIdAndRating", 2)]
        public int Rating { get; set; }
        [Index("IX_BlogIdAndRating", 1)]
        public int BlogId { get; set; }
    }
```

 

## <a name="relationship-attributes-inverseproperty-and-foreignkey"></a>リレーションシップは、次の属性: InverseProperty と不変

> [!NOTE]
> このページは、データ注釈を使用して Code First モデルでリレーションシップを設定する方法についての情報を提供します。 EF とアクセスし、リレーションシップを使用してデータを操作する方法に関係の詳細については、次を参照してください[リレーションシップとナビゲーション プロパティ](~/ef6/fundamentals/relationships.md)。 *。

コードの最初の規則が、モデル内の最も一般的なリレーションシップの処理が、場合によってはヘルプが必要な場所があります。

投稿との関係に問題を作成したブログ クラスのキー プロパティの名前を変更します。 

データベースを生成するときに、コードはまず Post クラスで BlogId プロパティを表示しと一致する、クラス名と"Id"ブログ クラスへの外部キーとして規則によりを認識します。 ブログのクラスで BlogId プロパティはありません。 このソリューションは、投稿にナビゲーション プロパティを作成し、コードの最初の 2 つのクラス間のリレーションシップを構築する方法を理解する外部 DataAnnotation を使用して、Post.BlogId プロパティを使用して、内の制約を指定する方法についても、データベース。

``` csharp
    public class Post
    {
            public int Id { get; set; }
            public string Title { get; set; }
            public DateTime DateCreated { get; set; }
            public string Content { get; set; }
            public int BlogId { get; set; }
            [ForeignKey("BlogId")]
            public Blog Blog { get; set; }
            public ICollection<Comment> Comments { get; set; }
    }
```

データベース内の制約は、InternalBlogs.PrimaryTrackingKey と Posts.BlogId 間の関係を示しています。 

![jj591583_figure09](~/ef6/media/jj591583-figure09.png)

クラス間の複数のリレーションシップがある場合、InverseProperty が使用されます。

ブログの投稿の作成者を追跡するのにすることがあります Post クラスで編集したユーザーとします。 ここでは、Post クラスの新しい 2 つのナビゲーション プロパティです。

``` csharp
    public Person CreatedBy { get; set; }
    public Person UpdatedBy { get; set; }
```

また、これらのプロパティによって参照されている Person クラスに追加する必要もあります。 Person クラスには、すべてのユーザーとすべての人が更新投稿の 1 つによって書き込まれた投稿の 1 つ、Post にナビゲーション プロパティがあります。

``` csharp
    public class Person
    {
            public int Id { get; set; }
            public string Name { get; set; }
            public List<Post> PostsWritten { get; set; }
            public List<Post> PostsUpdated { get; set; }
    }
```

最初にコードは、独自の 2 つのクラスのプロパティと一致することではありません。 投稿のデータベース テーブルでは、CreatedBy 人の 1 つの外部キーが必要し、UpdatedBy 人がコードの最初として 4 つは外部キー プロパティを作成します: Person\_Id、Person\_Id1、CreatedBy\_Id とUpdatedBy\_id。

![jj591583_figure10](~/ef6/media/jj591583-figure10.png)

これらの問題を解決するのにには、プロパティのアラインメントを指定するのに InverseProperty 注釈を使用できます。

``` csharp
    [InverseProperty("CreatedBy")]
    public List<Post> PostsWritten { get; set; }

    [InverseProperty("UpdatedBy")]
    public List<Post> PostsUpdated { get; set; }
```

持参 PostsWritten プロパティは、これは、投稿の種類を指すことを知っている、ため Post.CreatedBy へのリレーションシップが構築されます。 同様に、PostsUpdated は Post.UpdatedBy に接続されます。 コードはまず、余分な外部キーが作成されます。

![jj591583_figure11](~/ef6/media/jj591583-figure11.png)

 

## <a name="summary"></a>まとめ

DataAnnotations だけでなく、コードの最初クラスでクライアントとサーバー側の検証の説明を入力しますを強化し、まずコードがその規則に基づいて、クラスに関することは想定をでも修正がもできます。 DataAnnotations にないのみを改善するデータベース スキーマの生成が既存のデータベースに、コードの最初のクラスをマップすることもできます。

非常に柔軟なは、中には、DataAnnotations は、最も一般的に必要な構成の変更が、コードの最初のクラスで行うことができますを提供することに留意してください。 エッジ ケースの一部で、クラスを構成するには、Code First の Fluent API である代替の構成メカニズムになります。
