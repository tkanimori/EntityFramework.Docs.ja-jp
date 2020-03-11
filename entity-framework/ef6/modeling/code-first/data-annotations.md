---
title: データ注釈の Code First-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 80abefbd-23c9-4fce-9cd3-520e5df9856e
ms.openlocfilehash: 9fac2a90c46d78ff5fd632800cc0050276467773
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78415857"
---
# <a name="code-first-data-annotations"></a>データ注釈の Code First
> [!NOTE]
> **Ef 4.1 以降のみ**-このページで説明した機能、api などは、Entity Framework 4.1 で導入されました。 以前のバージョンを使用している場合は、この情報の一部またはすべてが適用されません。

このページの内容は、最初にジュリー Lerman (\<http://thedatafarm.com>)によって作成された記事から適用されます。

Entity Framework Code First を使用すると、独自のドメインクラスを使用して、EF がクエリ、変更の追跡、および更新機能を実行するために依存するモデルを表すことができます。 Code First は、「構成よりも規約」と呼ばれるプログラミングパターンを活用します。 Code First は、クラスが Entity Framework の規則に従うことを前提としています。その場合、そのジョブの実行方法は自動的に機能します。 ただし、クラスがこれらの規則に従っていない場合は、クラスに構成を追加して、必要な情報を EF に提供することができます。

Code First には、これらの構成をクラスに追加する2つの方法が用意されています。 1つは DataAnnotations という単純な属性を使用し、2番目の属性は Code First の Fluent API を使用します。これにより、コードで構成を強制的に記述することができます。

この記事では、(System.componentmodel 名前空間内の) DataAnnotations を使用してクラスを構成する方法に焦点を当て、最も一般的に必要な構成を強調表示します。 DataAnnotations は、ASP.NET MVC など、多くの .NET アプリケーションによっても認識されます。これにより、これらのアプリケーションは、クライアント側の検証に同じ注釈を利用できます。


## <a name="the-model"></a>モデル

ここでは、単純なクラスのペア (ブログと Post) で Code First DataAnnotations について説明します。

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

ブログと Post クラスは、code first 規約に従うことができ、EF との互換性を有効にするための調整は必要ありません。 ただし、注釈を使用して、クラスおよびマップ先のデータベースに関する詳細情報を EF に提供することもできます。

 

## <a name="key"></a>Key

Entity Framework は、エンティティ追跡に使用されるキー値を持つすべてのエンティティに依存します。 Code First の規則の1つは、暗黙的なキープロパティです。Code First は、"Id" という名前のプロパティ、またはクラス名と "Id" の組み合わせ ("ブログ Id" など) を検索します。 このプロパティは、データベースの主キー列にマップされます。

ブログと Post クラスは両方ともこの規則に従います。 失敗した場合はどうすればよいでしょうか。 代わりに、" *Primarytrackingkey* " という名前が使用されている場合はどうし*ますか。* Code first がこの規則に一致するプロパティを見つけられない場合は、キープロパティを持っている必要がある Entity Framework の要件があるため、例外がスローされます。 キー注釈を使用して、EntityKey として使用するプロパティを指定できます。

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

Code first のデータベース生成機能を使用している場合、ブログテーブルには、既定で Id としても定義されている、PrimaryTrackingKey という主キー列があります。

![主キーを持つブログテーブル](~/ef6/media/jj591583-figure01.png)

### <a name="composite-keys"></a>複合キー

Entity Framework は、複数のプロパティで構成される複合キー (主キー) をサポートしています。 たとえば、PassportNumber とを組み合わせた主キーを持つ Passport クラスがあるとします。

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

EF モデルで上記のクラスを使用しようとすると、`InvalidOperationException`が発生します。

*' Passport ' 型の複合主キーの順序を決定できません。複合主キーの順序を指定するには、ColumnAttribute または HasKey メソッドを使用します。*

複合キーを使用するには、Entity Framework で、キープロパティの順序を定義する必要があります。 これを行うには、列注釈を使用して順序を指定します。

>[!NOTE]
> 順序の値は (インデックスベースではなく) 相対であるため、任意の値を使用できます。 たとえば、100と200は、1と2の代わりに許容されます。

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

複合外部キーを持つエンティティがある場合は、対応する主キープロパティに使用したものと同じ列の順序を指定する必要があります。

外部キープロパティ内の相対順序のみが同じである必要があります。**順序**に割り当てられた正確な値を一致させる必要はありません。 たとえば、次のクラスでは、1と2の代わりに3と4を使用できます。

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

必須の注釈は、特定のプロパティが必要であることを EF に指示します。

Title プロパティに必要なを追加すると、EF (および MVC) によって、プロパティにデータが含まれるようになります。

``` csharp
    [Required]
    public string Title { get; set; }
```

アプリケーションで追加のコードやマークアップの変更を加えなくても、MVC アプリケーションは、プロパティと注釈の名前を使用してメッセージを動的に構築しながら、クライアント側の検証を実行します。

![タイトルを含むページの作成が必要なエラー](~/ef6/media/jj591583-figure02.png)

また、必要な属性は、マップされたプロパティを null 非許容にすることで、生成されるデータベースにも影響します。 Title フィールドが "not null" に変更されていることに注意してください。

>[!NOTE]
> 場合によっては、プロパティが必要な場合でも、データベース内の列を null 非許容にすることはできません。 たとえば、複数の型に対して TPH 継承戦略データを使用すると、1つのテーブルに格納されます。 派生型に必須のプロパティが含まれている場合、階層内のすべての型がこのプロパティを持つわけではないため、列を null 非許容にすることはできません。

 

![ブログの表](~/ef6/media/jj591583-figure03.png)

 

## <a name="maxlength-and-minlength"></a>MaxLength および MinLength

MaxLength 属性および MinLength 属性を使用すると、必要な場合と同様に、追加のプロパティ検証を指定できます。

次に示すのは、ブログ Ger名と長さの要件です。 この例では、属性を結合する方法も示しています。

``` csharp
    [MaxLength(10),MinLength(5)]
    public string BloggerName { get; set; }
```

MaxLength 注釈は、プロパティの長さを10に設定することによってデータベースに影響を与えます。

![ブログテーブルで、ブログ Gername 列の最大長が示されています](~/ef6/media/jj591583-figure04.png)

MVC クライアント側注釈と EF 4.1 サーバー側注釈は、両方ともこの検証を行い、"フィールドのブログ Gername は、最大長が ' 10 ' の文字列または配列型である必要があります" というエラーメッセージを動的に構築します。このメッセージは少し長くなります。 多くの注釈を使用すると、ErrorMessage 属性を使用してエラーメッセージを指定できます。

``` csharp
    [MaxLength(10, ErrorMessage="BloggerName must be 10 characters or less"),MinLength(5)]
    public string BloggerName { get; set; }
```

また、必要な注釈に ErrorMessage を指定することもできます。

![カスタムエラーメッセージを含むページの作成](~/ef6/media/jj591583-figure05.png)

 

## <a name="notmapped"></a>NotMapped

Code first の規則では、サポートされるデータ型のすべてのプロパティがデータベースで表現されます。 ただし、これはアプリケーションでは常に当てはまりません。 たとえば、Title および Blog Gername フィールドに基づいてコードを作成する、ブログクラスのプロパティがあるとします。 このプロパティは動的に作成することができ、保存する必要はありません。 このブログコードプロパティなど、NotMapped 注釈を使用して、データベースにマップされていないプロパティをマークできます。

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

 

## <a name="complextype"></a>ComplexType 要素

クラスのセット全体でドメインエンティティを記述し、そのクラスをレイヤー化して完全なエンティティを記述することは珍しくありません。 たとえば、ブログの詳細というクラスをモデルに追加することができます。

``` csharp
    public class BlogDetails
    {
        public DateTime? DateCreated { get; set; }

        [MaxLength(250)]
        public string Description { get; set; }
    }
```

ブログの詳細には、どのような種類のキープロパティもありません。 ドメイン駆動設計では、ブログの詳細を値オブジェクトと呼びます。 Entity Framework は、値オブジェクトを複合型として参照します。  複合型は独自に追跡できません。

ただし、ブログクラスのプロパティとして、Blog の詳細については、ブログオブジェクトの一部として追跡されます。 Code first がこれを認識できるようにするには、このクラスを ComplexType としてマークする必要があります。

``` csharp
    [ComplexType]
    public class BlogDetails
    {
        public DateTime? DateCreated { get; set; }

        [MaxLength(250)]
        public string Description { get; set; }
    }
```

ブログクラスにプロパティを追加して、ブログのブログの詳細を表すことができるようになりました。

``` csharp
        public BlogDetails BlogDetail { get; set; }
```

データベースでは、ブログテーブルには、blog 詳細プロパティに含まれるプロパティを含む、ブログのすべてのプロパティが含まれます。 既定では、各の前に複合型の名前 (「ブログ詳細」) が付いています。

![複合型を含むブログテーブル](~/ef6/media/jj591583-figure06.png)


## <a name="concurrencycheck"></a>ConcurrencyCheck

ConcurrencyCheck 注釈を使用すると、ユーザーがエンティティを編集または削除したときに、データベースの同時実行チェックに使用される1つ以上のプロパティにフラグを設定できます。 EF デザイナーを使用している場合は、プロパティの ConcurrencyMode を Fixed に設定することになります。

ConcurrencyCheck がどのように動作するかを、"ブログ Gername" プロパティに追加してみましょう。

``` csharp
    [ConcurrencyCheck, MaxLength(10, ErrorMessage="BloggerName must be 10 characters or less"),MinLength(5)]
    public string BloggerName { get; set; }
```

SaveChanges が呼び出されると、"ブログ Gername" フィールドの ConcurrencyCheck 注釈によって、そのプロパティの元の値が更新で使用されます。 コマンドは、キー値だけでなく、ブログ Gername の元の値にもフィルターを適用して、正しい行の検索を試みます。  データベースに送信される UPDATE コマンドの重要な部分を次に示します。このコマンドは、PrimaryTrackingKey が1で、blog Gername が "ジュリー" である行を更新します。これは、ブログがデータベースから取得されたときの元の値です。

``` SQL
    where (([PrimaryTrackingKey] = @4) and ([BloggerName] = @5))
    @4=1,@5=N'Julie'
```

その間に他のユーザーがそのブログの blogger 名を変更した場合、この更新は失敗し、処理する必要がある DbUpdateConcurrencyException が得られます。

 

## <a name="timestamp"></a>TimeStamp

同時実行チェックには、rowversion フィールドまたは timestamp フィールドを使用するのが一般的です。 ただし、ConcurrencyCheck 注釈を使用するのではなく、プロパティの型がバイト配列である限り、より具体的なタイムスタンプ注釈を使用できます。 Code first では、タイムスタンププロパティは ConcurrencyCheck プロパティと同じように扱われますが、code first によって生成されるデータベースフィールドが null 非許容であることも保証されます。 指定されたクラスに設定できるタイムスタンププロパティは1つだけです。

次のプロパティをブログクラスに追加します。

``` csharp
    [Timestamp]
    public Byte[] TimeStamp { get; set; }
```

コードでは、最初にデータベーステーブルに null 値を許容しないタイムスタンプ列を作成します。

![タイムスタンプ列が含まれるブログテーブル](~/ef6/media/jj591583-figure07.png)

 

## <a name="table-and-column"></a>テーブルと列

データベース Code First 作成する場合は、作成するテーブルと列の名前を変更することもできます。 また、既存のデータベースで Code First を使用することもできます。 ただし、ドメイン内のクラスとプロパティの名前がデータベース内のテーブルと列の名前と一致するとは限りません。

クラスの名前は Blog であり、規約により、code first は、これがブログという名前のテーブルにマップされることを前提としています。 そうでない場合は、table 属性を使用してテーブルの名前を指定できます。 ここで、注釈はテーブル名が InternalBlogs であることを示しています。

``` csharp
    [Table("InternalBlogs")]
    public class Blog
```

列注釈は、マップされた列の属性を指定する際に、より使い慣れになります。 名前、データ型、またはテーブル内で列が表示される順序を指定できます。 列属性の例を次に示します。

``` csharp
    [Column("BlogDescription", TypeName="ntext")]
    public String Description {get;set;}
```

列の TypeName 属性を DataType DataAnnotation と混同しないでください。 DataType は UI に使用される注釈であり、Code First によって無視されます。

再生成された後のテーブルを次に示します。 テーブル名が InternalBlogs に変更され、[複合型からの説明] 列が [ブログの説明] に変わりました。 名前が注釈に指定されているため、code first では、複合型の名前を使用して列名を開始する規則が使用されません。

![ブログのテーブルと列の名前を変更](~/ef6/media/jj591583-figure08.png)

 

## <a name="databasegenerated"></a>DatabaseGenerated

重要なデータベース機能は、計算されたプロパティを持つことができる機能です。 Code First クラスを計算列を含むテーブルにマップしている場合は、Entity Framework でそれらの列を更新しないようにしてください。 ただし、データを挿入または更新した後、EF によってそれらの値がデータベースから返されるようにする必要があります。 DatabaseGenerated 注釈を使用して、計算された列挙型と共にクラス内のこれらのプロパティにフラグを付けることができます。 その他の列挙型は None と Identity です。

``` csharp
    [DatabaseGenerated(DatabaseGeneratedOption.Computed)]
    public DateTime DateCreated { get; set; }
```

Code first でデータベースを生成しているときに、バイト列またはタイムスタンプ列に対して生成されたデータベースを使用できます。それ以外の場合は、既存のデータベースをポイントするときにのみ使用してください。これは、code first では計算列の数式を判別できないためです。

前に読みました。既定では、整数であるキープロパティはデータベースの id キーになります。 これは、DatabaseGenerated. Id に対して DatabaseGenerated を設定した場合と同じです。 Id キーを使用しない場合は、値を DatabaseGeneratedOption に設定できます。

 

## <a name="index"></a>インデックス

> [!NOTE]
> **Ef 6.1 以降のみ**-Index 属性が Entity Framework 6.1 で導入されました。 以前のバージョンを使用している場合、このセクションの情報は適用されません。

1つ以上の列にインデックスを作成するには、 **Indexattribute**を使用します。 属性を1つ以上のプロパティに追加すると、データベースの作成時に EF によってデータベース内の対応するインデックスが作成されるか、Code First Migrations を使用している場合は、対応する**Createindex**の呼び出しがスキャフォールディングます。

たとえば、次のコードでは、データベースの **[投稿]** テーブルの **[評価]** 列にインデックスが作成されます。

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

既定では、インデックスの名前は**ix\_&lt;プロパティ名&gt;** (上記の例では Ix\_評価) になります。 ただし、インデックスの名前を指定することもできます。 次の例では、インデックスを**Postr/index**という名前にする必要があることを指定します。

``` csharp
    [Index("PostRatingIndex")]
    public int Rating { get; set; }
```

既定では、インデックスは一意ではありませんが、 **isunique**名前付きパラメーターを使用して、インデックスが一意であることを指定できます。 次の例では、**ユーザー**のログイン名に対して一意のインデックスが導入されています。

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

複数の列にまたがるインデックスは、指定されたテーブルの複数のインデックス注釈で同じ名前を使用して指定されます。 複数列のインデックスを作成する場合は、インデックス内の列の順序を指定する必要があります。 たとえば、次のコードでは、**評価**に対して複数列のインデックスを作成し、IX という名前のブログ**id**を**IX\_ブログ idandレーティング**として作成しています。 **ブログ id**はインデックスの最初の列で、**評価**は2番目の列です。

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

 

## <a name="relationship-attributes-inverseproperty-and-foreignkey"></a>リレーションシップ属性: InverseProperty と ForeignKey

> [!NOTE]
> このページでは、データ注釈を使用して Code First モデル内のリレーションシップを設定する方法について説明します。 EF のリレーションシップに関する一般的な情報と、リレーションシップを使用してデータにアクセスして操作する方法については、「[リレーションシップ & ナビゲーションプロパティ](~/ef6/fundamentals/relationships.md)」を参照してください。

Code first 規則は、モデル内の最も一般的な関係を処理しますが、サポートが必要な場合もあります。

ブログクラスのキープロパティの名前を変更すると、Post との関係に問題が発生しました。 

データベースを生成するとき、code first は Post クラスで Blog Id プロパティを確認し、クラス名に "Id" が付いているという規則により、ブログクラスの外部キーとして認識します。 ただし、ブログクラスには、blog Id プロパティはありません。 これを解決するには、Post でナビゲーションプロパティを作成し、外部 DataAnnotation を使用して、2つのクラス間のリレーションシップを作成する方法 ("Post." ブログ Id プロパティを使用) と、「」で制約を指定する方法について説明します。データベース.

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

データベース内の制約によって、InternalBlogs. PrimaryTrackingKey と Post. Blog Id の間のリレーションシップが示されます。 

![InternalBlogs. PrimaryTrackingKey と Post. blog Id のリレーションシップ](~/ef6/media/jj591583-figure09.png)

InverseProperty は、クラス間に複数のリレーションシップがある場合に使用されます。

Post クラスでは、だれがブログ投稿を作成したか、およびだれが編集したかを追跡することができます。 Post クラスの2つの新しいナビゲーションプロパティを次に示します。

``` csharp
    public Person CreatedBy { get; set; }
    public Person UpdatedBy { get; set; }
```

また、これらのプロパティによって参照される Person クラスにを追加する必要があります。 Person クラスは、ナビゲーションプロパティを投稿に戻します。1つはユーザーが作成したすべての投稿、もう1つはその人物によって更新されたすべての投稿を対象とします。

``` csharp
    public class Person
    {
            public int Id { get; set; }
            public string Name { get; set; }
            public List<Post> PostsWritten { get; set; }
            public List<Post> PostsUpdated { get; set; }
    }
```

Code first では、2つのクラスのプロパティを独自に一致させることはできません。 投稿のデータベーステーブルには、CreatedBy person の外部キーと Updatedby person の外部キーが1つずつ含まれている必要がありますが、code first では、Person\_Id、Person\_Id1、CreatedBy\_Id、および UpdatedBy\_Id という4つの外部キープロパティが作成されます。

![余分な外部キーを含む post テーブル](~/ef6/media/jj591583-figure10.png)

これらの問題を修正するには、InverseProperty 注釈を使用して、プロパティの配置を指定します。

``` csharp
    [InverseProperty("CreatedBy")]
    public List<Post> PostsWritten { get; set; }

    [InverseProperty("UpdatedBy")]
    public List<Post> PostsUpdated { get; set; }
```

Person の PostsWritten プロパティは、これが Post 型を参照することを認識しているため、CreatedBy へのリレーションシップを構築します。 同様に、PostsUpdated Post に接続されます。 また、code first では余分な外部キーは作成されません。

![余分な外部キーを持たない投稿テーブル](~/ef6/media/jj591583-figure11.png)

 

## <a name="summary"></a>まとめ

DataAnnotations では、クライアントとサーバー側の検証をコードの最初のクラスで記述できるだけでなく、規則に基づいて、コードがクラスに対して最初に実行する想定を強化し、修正することもできます。 DataAnnotations を使用すると、データベーススキーマの生成だけでなく、コードの最初のクラスを既存のデータベースにマップすることもできます。

非常に柔軟性がありますが、DataAnnotations は、コードの最初のクラスに対して実行できる最も一般的な構成の変更のみを提供することに注意してください。 一部のエッジケースでクラスを構成するには、代替の構成メカニズムである Code First の Fluent API を参照する必要があります。
