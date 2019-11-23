---
title: テストの容易性と Entity Framework 4.0-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 9430e2ab-261c-4e8e-8545-2ebc52d7a247
ms.openlocfilehash: 28ec5446ce9faf98fb8fff141832236d70b29daf
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2019
ms.locfileid: "72181587"
---
# <a name="testability-and-entity-framework-40"></a>テストの容易性と Entity Framework 4.0
Scott Allen

公開: 2010 年 5 月

## <a name="introduction"></a>はじめに

このホワイトペーパーでは、ADO.NET Entity Framework 4.0 と Visual Studio 2010 を使用して、テスト可能なコードを記述する方法について説明します。 このホワイトペーパーでは、テスト駆動設計 (TDD) や動作駆動設計 (BDD) など、特定のテスト方法に焦点を当てません。 代わりに、このホワイトペーパーでは、ADO.NET Entity Framework を使用するコードを記述する方法について重点的に説明します。また、簡単に分離してテストすることができます。 ここでは、データアクセスのシナリオでのテストを容易にする共通のデザインパターンについて説明し、フレームワークの使用時にこれらのパターンを適用する方法を見ていきます。 また、これらの機能がテスト可能なコードでどのように動作するかを確認するために、フレームワークの特定の機能についても説明します。

## <a name="what-is-testable-code"></a>テスト可能なコードとは

自動単体テストを使用してソフトウェアの一部を検証する機能には、多くの望ましい利点があります。 優れたテストはアプリケーションのソフトウェア障害の数を減らし、アプリケーションの品質を向上させることができますが、単体テストはバグの発見だけではありません。

優れた単体テストスイートを使用すると、開発チームは時間を節約し、作成したソフトウェアの制御を維持できます。 チームは、既存のコードに変更を加えたり、新しい要件を満たすようにソフトウェアをリファクター、再設計、再構築したり、テストスイートでアプリケーションの動作を検証できることを確認しながら、アプリケーションに新しいコンポーネントを追加したりできます。 単体テストは、変更を容易にし、複雑さが増すにつれてソフトウェアの保守性を維持するための簡単なフィードバックサイクルの一部です。

ただし、単体テストには料金が伴います。 チームは、単体テストの作成と管理に時間を費やす必要があります。 これらのテストを作成するために必要な作業量は、基になるソフトウェアのテストの**容易**性に直接関係しています。 ソフトウェアはどれほど簡単にテストできますか。 テストの容易性を考慮してソフトウェアを設計するチームは、テスト不可能なソフトウェアを扱うチームよりも効率的なテストを作成します。

Microsoft は、ADO.NET Entity Framework 4.0 (EF4) を、テストの容易性を考慮して設計しました。 これは、開発者がフレームワークコード自体に対して単体テストを記述することを意味するわけではありません。 代わりに、EF4 のテストの容易性の目標によって、フレームワークの上に構築されるテスト可能なコードを簡単に作成できます。 具体的な例を確認する前に、テスト可能なコードの品質について理解しておくことをお勧めします。

### <a name="the-qualities-of-testable-code"></a>テスト可能なコードの品質

テストが容易なコードでは、常に少なくとも2つの特徴が表示されます。 まず、テスト可能なコードは簡単に確認**できます。** 入力のセットを指定すると、コードの出力を簡単に確認できるようになります。 たとえば、メソッドが計算の結果を直接返すため、次のメソッドを簡単にテストできます。

``` csharp
    public int Add(int x, int y) {
        return x + y;
    }
```

メソッドが計算された値をネットワークソケット、データベーステーブル、または次のコードのようなファイルに書き込む場合、メソッドのテストは困難です。 テストでは、値を取得するために追加の作業を実行する必要があります。

``` csharp
    public void AddAndSaveToFile(int x, int y) {
         var results = string.Format("The answer is {0}", x + y);
         File.WriteAllText("results.txt", results);
    }
```

2番目に、テスト可能なコードは簡単に**分離**できます。 テスト可能なコードの不適切な例として、次の擬似コードを使用してみましょう。

``` csharp
    public int ComputePolicyValue(InsurancePolicy policy) {
        using (var connection = new SqlConnection("dbConnection"))
        using (var command = new SqlCommand(query, connection)) {

            // business calculations omitted ...               

            if (totalValue > notificationThreshold) {
                var message = new MailMessage();
                message.Subject = "Warning!";
                var client = new SmtpClient();
                client.Send(message);
            }
        }
        return totalValue;
    }
```

このメソッドは簡単に観察できます。保険ポリシーを渡して、戻り値が予期される結果と一致することを確認することができます。 ただし、メソッドをテストするには、適切なスキーマを使用してデータベースをインストールし、メソッドが電子メールを送信しようとしたときに SMTP サーバーを構成する必要があります。

単体テストでは、メソッド内の計算ロジックだけを検証する必要がありますが、電子メールサーバーがオフラインになっているか、データベースサーバーが移動したことが原因で、テストが失敗する可能性があります。 これらのエラーはどちらも、テストで検証しようとしている動作とは関係がありません。 この動作を分離することは困難です。

テスト可能なコードを記述しようとしているソフトウェア開発者は、多くの場合、記述するコードにおける懸念事項の分離を維持することに努めています。 上記の方法では、ビジネスの計算に焦点を当て、データベースおよび電子メールの実装の詳細を他のコンポーネントに委任する必要があります。 Robert Martin は、これを単一責任の原則と呼びます。 オブジェクトは、ポリシーの値の計算など、1つの限定された責任をカプセル化する必要があります。 他のすべてのデータベースと通知の作業は、他のオブジェクトの役割である必要があります。 この方法で記述されたコードは、単一のタスクに重点があるため、分離しやすくなります。

.NET では、単一責任の原則に従って分離を実現するために必要な抽象化が用意されています。 インターフェイス定義を使用して、具象型ではなくインターフェイスの抽象化を使用するようにコードを強制できます。 この記事の後半では、上記の不適切な例のようなメソッドが、データベースと通信するように*見える*インターフェイスを使用する方法について説明します。 ただし、テスト時には、データベースとは通信しないダミーの実装に置き換えることができますが、データはメモリに保持されます。 このダミー実装は、データアクセスコードまたはデータベース構成の関連のない問題からコードを分離します。

分離には他にも利点があります。 最後のメソッドのビジネスの計算は、実行に数ミリ秒しかかかりませんが、テスト自体は、ネットワーク上でコードがホップし、さまざまなサーバーと通信するため、数秒間実行される可能性があります。 小さな変更を容易にするために、単体テストが高速に実行される必要があります。 テストに関連付けられていないコンポーネントに問題があるため、単体テストも反復可能で、失敗することはありません。 見やすく、分離するためのコードを記述すると、開発者はコードのテストを作成しやすくなり、テストの実行を待機する時間が短縮されます。さらに重要なのは、存在しないバグを追跡する時間を短縮することです。

テストの利点を評価し、テスト可能なコードが示す品質を理解することができます。 この記事では、EF4 と連携してデータをデータベースに保存するコードを記述する方法について説明しますが、このコードでは、データアクセスのテスト可能な設計について説明します。

## <a name="design-patterns-for-data-persistence"></a>データ永続化の設計パターン

前述の不適切な例には、どちらもあまり多くの責任がありません。 最初の不適切な例では、計算を実行し*て*ファイルに書き込む必要がありました。 2番目の不適切な例では、データベースからデータを読み取り *、* ビジネスの計算を実行し*て*電子メールを送信する必要がありました。 関心を分離し、他のコンポーネントに責任を委任する小さなメソッドを設計することにより、テスト可能なコードを記述することに大きな進化を加えます。 目標は、小規模で焦点を絞った抽象化からアクションを作成することによって機能を構築することです。

データの永続化に関しては、探している小さな抽象化された抽象化が、設計パターンとしてドキュメントに記載されていることがよくあります。 エンタープライズアプリケーションアーキテクチャの Martin Fowler の書籍パターンは、これらのパターンを印刷で記述するための最初の作業でした。 これらの ADO.NET Entity Framework がこれらのパターンをどのように実装して使用するかを説明する前に、これらのパターンについて簡単に説明します。

### <a name="the-repository-pattern"></a>リポジトリ パターン

Fowler は、ドメインオブジェクトにアクセスするためのコレクションに似たインターフェイスを使用して、ドメインとデータマッピングのレイヤーを仲介することを意味します。 リポジトリパターンの目的は、データアクセスの minutiae からコードを分離することです。これまでの分離は、テストの容易性のために必要な特徴です。

分離の鍵となるのは、リポジトリがコレクションに似たインターフェイスを使用してオブジェクトを公開する方法です。 リポジトリを使用するために記述するロジックには、要求したオブジェクトをリポジトリが具体化する方法はありません。 リポジトリはデータベースと通信する場合もあれば、メモリ内コレクションからオブジェクトを返す場合もあります。 すべてのコードは、コレクションを維持するためにリポジトリが表示されること、およびコレクションからオブジェクトを取得、追加、および削除できることを認識している必要があります。

既存の .NET アプリケーションでは、多くの場合、具象リポジトリは次のようなジェネリックインターフェイスから継承されます。

``` csharp
    public interface IRepository<T> {       
        IEnumerable<T> FindAll();
        IEnumerable<T> FindBy(Expression<Func\<T, bool>> predicate);
        T FindById(int id);
        void Add(T newEntity);
        void Remove(T entity);
    }
```

EF4 の実装を提供するときに、インターフェイス定義にいくつかの変更を行いますが、基本的な概念は変わりません。 コードでは、このインターフェイスを実装する具象リポジトリを使用して、その主キー値によってエンティティを取得したり、述語の評価に基づいてエンティティのコレクションを取得したり、使用可能なすべてのエンティティを取得したりすることができます。 このコードでは、リポジトリインターフェイスを使用してエンティティを追加および削除することもできます。

Employee オブジェクトの IRepository を指定すると、コードは次の操作を実行できます。

``` csharp
    var employeesNamedScott =
        repository
            .FindBy(e => e.Name == "Scott")
            .OrderBy(e => e.HireDate);
    var firstEmployee = repository.FindById(1);
    var newEmployee = new Employee() {/*... */};
    repository.Add(newEmployee);
```

コードはインターフェイス (Employee の IRepository) を使用しているため、インターフェイスのさまざまな実装でコードを提供できます。 1つの実装は、EF4 によってサポートされ、オブジェクトを Microsoft SQL Server データベースに保持する実装である場合があります。 (テスト中に使用する) 別の実装は、Employee オブジェクトのメモリ内リストによってサポートされる場合があります。 インターフェイスは、コード内の分離を実現するのに役立ちます。

IRepository&lt;T&gt; インターフェイスでは、保存操作が公開されないことに注意してください。 既存のオブジェクトを更新するにはどうすればよいですか。 保存操作を含む IRepository 定義を経由することもあります。これらのリポジトリの実装では、オブジェクトをすぐにデータベースに保持する必要があります。 ただし、多くのアプリケーションでは、オブジェクトを個別に永続化する必要はありません。 代わりに、さまざまなリポジトリからオブジェクトを有効にし、ビジネスアクティビティの一部としてそれらのオブジェクトを変更し、1つのアトミックな操作の一部としてすべてのオブジェクトを永続化する必要があります。 幸いにも、この種類の動作を許可するパターンがあります。

### <a name="the-unit-of-work-pattern"></a>作業単位パターン

Fowler は、1つの作業単位が "ビジネストランザクションの影響を受けるオブジェクトの一覧を保持し、変更と同時実行に関する問題の解決を調整する" ということを示しています。 リポジトリから有効なオブジェクトに対する変更を追跡し、変更をコミットする作業単位を指定するときに、オブジェクトに加えられたすべての変更を保持するのは、作業単位の役割です。 また、すべてのリポジトリに追加した新しいオブジェクトを取得し、そのオブジェクトをデータベースに挿入して、削除を実行する作業単位も必要です。

ADO.NET データセットを操作したことがある場合は、作業単位パターンについて既に理解しておく必要があります。 ADO.NET データセットには、DataRow オブジェクトの更新、削除、および挿入を追跡する機能があり、(TableAdapter を使用して) データベースへのすべての変更が調整される可能性がありました。 ただし、DataSet オブジェクトは、基になるデータベースの切断されたサブセットをモデル化します。 作業単位パターンは同じ動作をしますが、データアクセスコードから分離され、データベースを認識しないビジネスオブジェクトとドメインオブジェクトで動作します。

.NET コードの作業単位をモデル化するための抽象化は、次のようになります。

``` csharp
    public interface IUnitOfWork {
        IRepository<Employee> Employees { get; }
        IRepository<Order> Orders { get; }
        IRepository<Customer> Customers { get; }
        void Commit();
    }
```

作業単位からリポジトリ参照を公開することにより、1つの作業単位オブジェクトが、ビジネストランザクション中に具体化されたすべてのエンティティを追跡できるようになります。 実際の作業単位に対して Commit メソッドを実装することは、すべてのマジックがデータベースとのメモリ内の変更を調整するために行われる場所です。 

IUnitOfWork 参照を指定すると、コードは1つ以上のリポジトリから取得したビジネスオブジェクトに変更を加え、アトミックコミット操作を使用してすべての変更を保存できます。

``` csharp
    var firstEmployee = unitofWork.Employees.FindById(1);
    var firstCustomer = unitofWork.Customers.FindById(1);
    firstEmployee.Name = "Alex";
    firstCustomer.Name = "Christopher";
    unitofWork.Commit();
```

### <a name="the-lazy-load-pattern"></a>遅延読み込みパターン

Fowler は、"遅延読み込み" という名前を使用して、"必要なすべてのデータを含まないが、取得方法を知っている" オブジェクトを記述します。 透過的な遅延読み込みは、テスト可能なビジネスコードを記述し、リレーショナルデータベースを操作するときに必要となる重要な機能です。 例として、次のコードについて考えてみます。

``` csharp
    var employee = repository.FindById(id);
    // ... and later ...
    foreach(var timeCard in employee.TimeCards) {
        // .. manipulate the timeCard
    }
```

タイムカードコレクションはどのように設定されますか。 2つの回答が得られます。 回答として、従業員リポジトリは従業員をフェッチするように求められた場合に、従業員に関連付けられたタイムカード情報と共に両方の従業員を取得するクエリを発行します。 リレーショナルデータベースでは、通常、JOIN 句を使用するクエリが必要であり、アプリケーションの必要以上に多くの情報を取得することがあります。 アプリケーションがタイムカードプロパティに触れる必要がない場合はどうすればよいでしょうか。

2番目の答えは、タイムカードプロパティを "オンデマンド" で読み込むことです。 この遅延読み込みは、タイムカード情報を取得するための特別な Api をコードが呼び出さないため、暗黙的で、ビジネスロジックに対して透過的です。 このコードでは、必要に応じてタイムカード情報が存在することを前提としています。 遅延読み込みには、通常、メソッド呼び出しの実行時のインターセプトに関連するマジックがいくつかあります。 インターセプトコードは、ビジネスロジックをビジネスロジックとして解放したまま、データベースと通信し、タイムカード情報を取得する役割を担います。 この遅延読み込みマジックにより、ビジネスコードはデータ取得操作から分離され、テストが容易なコードになります。

遅延読み込みの欠点は、アプリケーションがタイムカード情報を必要とする場合、*コードが追加*のクエリを実行することです。 これは多くのアプリケーションでは問題ではありませんが、パフォーマンスに依存するアプリケーションやアプリケーションでは、多数の従業員オブジェクトをループし、ループの各反復処理中にクエリを実行してタイムカードを取得します (問題は N + 1 と呼ばれることがよくあります)。クエリの問題)、遅延読み込みはドラッグです。 このようなシナリオでは、アプリケーションは、最も効率的な方法でタイムカード情報の読み込みを集中的することができます。

幸いにも、次のセクションに進み、これらのパターンを実装すると、EF4 が暗黙的な遅延読み込みと効率的な一括読み込みの両方をサポートしていることがわかります。

## <a name="implementing-patterns-with-the-entity-framework"></a>Entity Framework を使用したパターンの実装

最後のセクションで説明したすべてのデザインパターンは、EF4 で実装するのが簡単です。 例として、単純な ASP.NET MVC アプリケーションを使用して、従業員とそれに関連付けられているタイムカード情報を編集し、表示します。 まず、次の "plain old CLR objects" (POCOs) を使用します。 

``` csharp
    public class Employee {
        public int Id { get; set; }
        public string Name { get; set; }
        public DateTime HireDate { get; set; }
        public ICollection<TimeCard> TimeCards { get; set; }
    }

    public class TimeCard {
        public int Id { get; set; }
        public int Hours { get; set; }
        public DateTime EffectiveDate { get; set; }
    }
```

これらのクラス定義は、EF4 のさまざまなアプローチと機能を調べると若干変わりますが、その目的は、これらのクラスを可能な限り永続化非依存 (PI) として保持することです。 PI オブジェクトは、その状態がデータベース内に存在するか*どう*か*を把握し*ていません。 PI と POCOs は、テスト可能なソフトウェアで手に入ります。 POCO アプローチを使用するオブジェクトは、データベースが存在しなくても動作するため、制約が少なく、柔軟性が高く、テストが簡単です。

POCOs が配置されたら、Visual Studio で Entity Data Model (EDM) を作成できます (図1を参照)。 EDM を使用して、エンティティのコードを生成することはありません。 代わりに、lovingly によって構築されたエンティティを手作業で使用したいと考えています。 EDM を使用してデータベーススキーマを生成するだけで、オブジェクトをデータベースにマップするために必要なメタデータ EF4 が提供されます。

![ef test_01](~/ef6/media/eftest-01.jpg)

**図1**

注: EDM モデルを最初に開発する場合は、EDM から POCO のクリーンなコードを生成できます。 これを行うには、データプログラミングチームによって提供される Visual Studio 2010 拡張機能を使用します。 拡張機能をダウンロードするには、Visual Studio の [ツール] メニューから拡張機能マネージャーを起動し、"POCO" のテンプレートのオンラインギャラリーを検索します (図2を参照)。 EF には、いくつかの POCO テンプレートが用意されています。 テンプレートの使用方法の詳細については、「[チュートリアル: POCO template for the Entity Framework](https://blogs.msdn.com/adonet/pages/walkthrough-poco-template-for-the-entity-framework.aspx)」を参照してください。

![ef test_02](~/ef6/media/eftest-02.png)

**図2**

この POCO の出発点から、テスト可能なコードに対する2つの異なるアプローチについて説明します。 最初の方法では、EF アプローチを呼び出します。これは、Entity Framework API の抽象化を利用して作業単位とリポジトリを実装するためです。 2番目の方法では、独自のカスタムリポジトリの抽象化を作成し、それぞれの方法の長所と短所を確認します。 まず、EF のアプローチについて説明します。  

### <a name="an-ef-centric-implementation"></a>EF 中心の実装

ASP.NET MVC プロジェクトの次のコントローラーアクションを考えてみましょう。 アクションは Employee オブジェクトを取得し、結果を返して従業員の詳細ビューを表示します。

``` csharp
    public ViewResult Details(int id) {
        var employee = _unitOfWork.Employees
                                  .Single(e => e.Id == id);
        return View(employee);
    }
```

コードのテストが可能かどうか。 アクションの動作を確認するために必要なテストが少なくとも2つあります。 まず、アクションが適切なビュー (簡単なテスト) を返すことを確認したいと考えています。 また、アクションによって適切な従業員が取得されることを確認するテストを作成し、データベースを照会するコードを実行せずにこれを実行したいと考えています。 テスト対象のコードを分離する必要があることに注意してください。 分離により、データアクセスコードまたはデータベース構成のバグが原因でテストが失敗しないようにすることができます。 テストが失敗した場合は、下位レベルのシステムコンポーネントではなく、コントローラーロジックにバグがあることがわかります。

分離を実現するには、リポジトリと作業単位に対して前に説明したインターフェイスのような抽象化が必要です。 リポジトリパターンは、ドメインオブジェクトとデータマッピングレイヤー間で仲介するように設計されています。 このシナリオでは、EF4 はデータマッピング層で*あり*、IObjectSet&lt;t&gt; (system.string 名前空間から) というリポジトリに似た抽象化を既に提供しています。 インターフェイス定義は次のようになります。

``` csharp
    public interface IObjectSet<TEntity> :
                     IQueryable<TEntity>,
                     IEnumerable<TEntity>,
                     IQueryable,
                     IEnumerable
                     where TEntity : class
    {
        void AddObject(TEntity entity);
        void Attach(TEntity entity);
        void DeleteObject(TEntity entity);
        void Detach(TEntity entity);
    }
```

IObjectSet&lt;T&gt; は、(IEnumerable&lt;T&gt;を介して) オブジェクトのコレクションに似ており、シミュレートされたコレクションにオブジェクトを追加および削除するメソッドを提供するため、リポジトリの要件を満たしています。 Attach メソッドと Detach メソッドは、EF4 API の追加機能を公開します。 リポジトリのインターフェイスとして IObjectSet&lt;T&gt; を使用するには、リポジトリをバインドするために作業単位の抽象化が必要です。

``` csharp
    public interface IUnitOfWork {
        IObjectSet<Employee> Employees { get; }
        IObjectSet<TimeCard> TimeCards { get; }
        void Commit();
    }
```

このインターフェイスの具象実装の1つは、SQL Server と対話し、EF4 の ObjectContext クラスを使用して簡単に作成できます。 ObjectContext クラスは、EF4 API の実際の作業単位です。

``` csharp
    public class SqlUnitOfWork : IUnitOfWork {
        public SqlUnitOfWork() {
            var connectionString =
                ConfigurationManager
                    .ConnectionStrings[ConnectionStringName]
                    .ConnectionString;
            _context = new ObjectContext(connectionString);
        }

        public IObjectSet<Employee> Employees {
            get { return _context.CreateObjectSet<Employee>(); }
        }

        public IObjectSet<TimeCard> TimeCards {
            get { return _context.CreateObjectSet<TimeCard>(); }
        }

        public void Commit() {
            _context.SaveChanges();
        }

        readonly ObjectContext _context;
        const string ConnectionStringName = "EmployeeDataModelContainer";
    }
```

IObjectSet&lt;T&gt; を生活に取り込むことは、ObjectContext オブジェクトの受け取る createobjectset<tentity> メソッドを呼び出すのと同じように簡単です。 背後では、フレームワークは EDM で提供されたメタデータを使用して、具象 ObjectSet&lt;T&gt;を生成します。 クライアントコードでのテストの容易性を維持するために、IObjectSet&lt;T&gt; インターフェイスを返すことをお勧めします。

この具象実装は実稼働環境で役立ちますが、IUnitOfWork 抽象化を使用してテストを容易にする方法に焦点を当てる必要があります。

### <a name="the-test-doubles"></a>テストの倍精度浮動小数点数

コントローラーアクションを分離するには、(ObjectContext によって支えられた) 実際の作業単位と、テストの double または "偽の" 作業単位 (インメモリ操作の実行) を切り替える機能が必要です。 この種の切り替えを実行する一般的な方法は、MVC コントローラーが作業単位をインスタンス化せずに、代わりに、コンストラクターパラメーターとしてコントローラーに作業単位を渡すことです。

``` csharp
    class EmployeeController : Controller {
      publicEmployeeController(IUnitOfWork unitOfWork)  {
          _unitOfWork = unitOfWork;
      }
      ...
    }
```

上記のコードは、依存関係の挿入の例です。 コントローラーに依存関係 (作業単位) を作成することは許可されていませんが、コントローラーに依存関係が挿入されます。 MVC プロジェクトでは、カスタムコントローラーファクトリとコントロールの反転 (IoC) コンテナーを組み合わせて使用して、依存関係の挿入を自動化するのが一般的です。 これらのトピックはこの記事の範囲を超えていますが、詳細については、この記事の最後に記載されている参照情報を参照してください。

テストに使用できる仮の作業単位実装は、次のようになります。

``` csharp
    public class InMemoryUnitOfWork : IUnitOfWork {
        public InMemoryUnitOfWork() {
            Committed = false;
        }
        public IObjectSet<Employee> Employees {
            get;
            set;
        }

        public IObjectSet<TimeCard> TimeCards {
            get;
            set;
        }

        public bool Committed { get; set; }
        public void Commit() {
            Committed = true;
        }
    }
```

仮の作業単位は、コミットされたプロパティを公開することに注意してください。 テストを容易にする偽クラスに機能を追加すると便利な場合があります。 この場合、コミットされたプロパティをチェックすることで、コードが作業単位をコミットするかどうかを簡単に確認できます。

また、従業員とタイムカードオブジェクトをメモリに保持するために、偽の IObjectSet&lt;T&gt; も必要になります。 ジェネリックを使用して1つの実装を提供できます。

``` csharp
    public class InMemoryObjectSet<T> : IObjectSet<T> where T : class
        public InMemoryObjectSet()
            : this(Enumerable.Empty<T>()) {
        }
        public InMemoryObjectSet(IEnumerable<T> entities) {
            _set = new HashSet<T>();
            foreach (var entity in entities) {
                _set.Add(entity);
            }
            _queryableSet = _set.AsQueryable();
        }
        public void AddObject(T entity) {
            _set.Add(entity);
        }
        public void Attach(T entity) {
            _set.Add(entity);
        }
        public void DeleteObject(T entity) {
            _set.Remove(entity);
        }
        public void Detach(T entity) {
            _set.Remove(entity);
        }
        public Type ElementType {
            get { return _queryableSet.ElementType; }
        }
        public Expression Expression {
            get { return _queryableSet.Expression; }
        }
        public IQueryProvider Provider {
            get { return _queryableSet.Provider; }
        }
        public IEnumerator<T> GetEnumerator() {
            return _set.GetEnumerator();
        }
        IEnumerator IEnumerable.GetEnumerator() {
            return GetEnumerator();
        }

        readonly HashSet<T> _set;
        readonly IQueryable<T> _queryableSet;
    }
```

このテストでは、ほとんどの作業を、基になる HashSet&lt;T&gt; オブジェクトにダブルデリゲートします。 IObjectSet&lt;T&gt; には、クラス (参照型) として T を適用する汎用的な制約が必要です。また、IQueryable&lt;T&gt;も実装する必要があります。 標準的な LINQ 演算子 AsQueryable を使用して、メモリ内コレクションを IQueryable&lt;T&gt; として表示するのは簡単です。

### <a name="the-tests"></a>テスト

従来の単体テストでは、単一のテストクラスを使用して、1つの MVC コントローラー内のすべてのアクションに対するすべてのテストを保持します。 これらのテスト、または任意の種類の単体テストを、作成したメモリ内のフェイクを使用して記述できます。 ただし、この記事では、モノリシックテストクラスのアプローチを避け、代わりに特定の機能に重点を置いてテストをグループ化します。  たとえば、"新しい従業員の作成" はテストする機能である可能性があるため、1つのテストクラスを使用して、新しい従業員を作成する1つのコントローラーアクションを確認します。

これらの細かいテストクラスには、いくつかの一般的なセットアップコードが必要です。 たとえば、常にメモリ内リポジトリと偽の作業単位を作成する必要があります。 また、作業単位が挿入された従業員コントローラーのインスタンスも必要です。 基本クラスを使用して、この共通セットアップコードをテストクラス間で共有します。

``` csharp
    public class EmployeeControllerTestBase {
        public EmployeeControllerTestBase() {
            _employeeData = EmployeeObjectMother.CreateEmployees()
                                                .ToList();
            _repository = new InMemoryObjectSet<Employee>(_employeeData);
            _unitOfWork = new InMemoryUnitOfWork();
            _unitOfWork.Employees = _repository;
            _controller = new EmployeeController(_unitOfWork);
        }

        protected IList<Employee> _employeeData;
        protected EmployeeController _controller;
        protected InMemoryObjectSet<Employee> _repository;
        protected InMemoryUnitOfWork _unitOfWork;
    }
```

基底クラスで使用される "オブジェクト母" は、テストデータを作成するための一般的なパターンの1つです。 オブジェクト母親には、複数のテストフィクスチャで使用するテストエンティティをインスタンス化するファクトリメソッドが含まれています。

``` csharp
    public static class EmployeeObjectMother {
        public static IEnumerable<Employee> CreateEmployees() {
            yield return new Employee() {
                Id = 1, Name = "Scott", HireDate=new DateTime(2002, 1, 1)
            };
            yield return new Employee() {
                Id = 2, Name = "Poonam", HireDate=new DateTime(2001, 1, 1)
            };
            yield return new Employee() {
                Id = 3, Name = "Simon", HireDate=new DateTime(2008, 1, 1)
            };
        }
        // ... more fake data for different scenarios
    }
```

EmployeeControllerTestBase は、多数のテストフィクスチャの基底クラスとして使用できます (図3を参照)。 各テストフィクスチャは、特定のコントローラーアクションをテストします。 たとえば、あるテストフィクスチャは、(従業員を作成するためのビューを表示するために) HTTP GET 要求中に使用される作成アクションのテストに重点を置いています。また、別のフィクスチャは、HTTP POST 要求で使用される作成アクション (ユーザーが従業員を作成します)。 各派生クラスは、特定のコンテキストで必要なセットアップだけを担当し、特定のテストコンテキストの結果を検証するために必要なアサーションを提供します。

![ef test_03](~/ef6/media/eftest-03.png)

**図3**

ここに示されている名前付け規則とテストスタイルは、テスト可能なコードには必要ありません。これは1つの方法にすぎません。 図4は、Visual Studio 2010 用の Jet 頭脳のテストランナープラグインで実行されるテストを示しています。

![ef test_04](~/ef6/media/eftest-04.png)

**図4**

共有セットアップコードを処理する基底クラスを使用すると、各コントローラーアクションの単体テストは小さく、簡単に記述できます。 テストは (メモリ内の操作を実行しているため) すぐに実行されます。また、関連のないインフラストラクチャや環境の問題 (テスト対象の単位が分離されているため) が原因で失敗することはありません。

``` csharp
    [TestClass]
    public class EmployeeControllerCreateActionPostTests
               : EmployeeControllerTestBase {
        [TestMethod]
        public void ShouldAddNewEmployeeToRepository() {
            _controller.Create(_newEmployee);
            Assert.IsTrue(_repository.Contains(_newEmployee));
        }
        [TestMethod]
        public void ShouldCommitUnitOfWork() {
            _controller.Create(_newEmployee);
            Assert.IsTrue(_unitOfWork.Committed);
        }
        // ... more tests

        Employee _newEmployee = new Employee() {
            Name = "NEW EMPLOYEE",
            HireDate = new System.DateTime(2010, 1, 1)
        };
    }
```

これらのテストでは、基本クラスによってほとんどのセットアップ作業が実行されます。 基底クラスのコンストラクターは、メモリ内のリポジトリ、仮の作業単位、および EmployeeController クラスのインスタンスを作成することに注意してください。 このテストクラスは、この基本クラスから派生し、Create メソッドのテストの詳細に焦点を当てています。 この例では、単体テストの手順に表示される "整列、操作、およびアサート" の手順について詳しく説明しています。

-   受信データをシミュレートする newEmployee オブジェクトを作成します。
-   EmployeeController の Create アクションを呼び出し、newEmployee を渡します。
-   [作成] アクションによって、予想される結果が生成されることを確認します (従業員がリポジトリに表示されます)。

私たちが構築したことで、EmployeeController アクションをテストできます。 たとえば、Employee コントローラーの Index アクションのテストを作成するときに、テスト基本クラスから継承して、テストの基本設定を同じにすることができます。 ここでも、基底クラスによって、インメモリリポジトリ、仮の作業単位、および EmployeeController のインスタンスが作成されます。 インデックスアクションのテストでは、インデックスアクションの呼び出しと、アクションによって返されるモデルの品質のテストにのみ焦点を当てる必要があります。

``` csharp
    [TestClass]
    public class EmployeeControllerIndexActionTests
               : EmployeeControllerTestBase {
        [TestMethod]
        public void ShouldBuildModelWithAllEmployees() {
            var result = _controller.Index();
            var model = result.ViewData.Model
                          as IEnumerable<Employee>;
            Assert.IsTrue(model.Count() == _employeeData.Count);
        }
        [TestMethod]
        public void ShouldOrderModelByHiredateAscending() {
            var result = _controller.Index();
            var model = result.ViewData.Model
                         as IEnumerable<Employee>;
            Assert.IsTrue(model.SequenceEqual(
                           _employeeData.OrderBy(e => e.HireDate)));
        }
        // ...
    }
```

インメモリのフェイクで作成しているテストは、ソフトウェアの*状態*のテストに向いています。 たとえば、作成アクションをテストするときに、作成アクションの実行後にリポジトリの状態を検査する必要があります。リポジトリは新しい従業員を保持しますか。

``` csharp
    [TestMethod]
    public void ShouldAddNewEmployeeToRepository() {
        _controller.Create(_newEmployee);
        Assert.IsTrue(_repository.Contains(_newEmployee));
    }
```

後で、相互作用ベースのテストについて説明します。 相互作用ベースのテストでは、テスト対象のコードがオブジェクトで適切なメソッドを呼び出し、正しいパラメーターを渡されたかどうかを確認します。 ここでは、もう1つの設計パターン (遅延読み込み) について説明します。

## <a name="eager-loading-and-lazy-loading"></a>一括読み込みと遅延読み込み

ASP.NET MVC web アプリケーションのある時点で、従業員の情報を表示し、従業員に関連付けられたタイムカードを含めることをお勧めします。 たとえば、従業員の名前とシステム内のタイムカードの合計数を示すタイムカードの概要が表示される場合があります。 この機能を実装するには、いくつかの方法があります。

### <a name="projection"></a>射影

概要を作成する簡単な方法の1つは、ビューに表示する情報専用のモデルを構築することです。 このシナリオでは、モデルは次のようになります。

``` csharp
    public class EmployeeSummaryViewModel {
        public string Name { get; set; }
        public int TotalTimeCards { get; set; }
    }
```

EmployeeSummaryViewModel はエンティティではなく、データベースに保持するものではないことに注意してください。 このクラスは、厳密に型指定された方法でデータをビューにシャッフルする場合にのみ使用します。 ビューモデルはデータ転送オブジェクト (DTO) に似ています。これは、[動作なし] (メソッドなし) のプロパティのみが含まれているためです。 プロパティは、移動する必要があるデータを保持します。 LINQ の標準プロジェクション演算子 (Select 演算子) を使用して、このビューモデルのインスタンス化を簡単に行うことができます。

``` csharp
    public ViewResult Summary(int id) {
        var model = _unitOfWork.Employees
                               .Where(e => e.Id == id)
                               .Select(e => new EmployeeSummaryViewModel
                                  {
                                    Name = e.Name,
                                    TotalTimeCards = e.TimeCards.Count()
                                  })
                               .Single();
        return View(model);
    }
```

上記のコードには、次の2つの注目すべき機能があります。 最初の方法では、コードを簡単に確認し、分離することができるため、コードを簡単にテストできます。 Select 演算子は、実際の作業単位に対して実行されるメモリ内のフェイクに対しても同様に機能します。

``` csharp
    [TestClass]
    public class EmployeeControllerSummaryActionTests
               : EmployeeControllerTestBase {
        [TestMethod]
        public void ShouldBuildModelWithCorrectEmployeeSummary() {
            var id = 1;
            var result = _controller.Summary(id);
            var model = result.ViewData.Model as EmployeeSummaryViewModel;
            Assert.IsTrue(model.TotalTimeCards == 3);
        }
        // ...
    }
```

2番目の注目すべき機能は、EF4 が1つの効率的なクエリを生成して従業員とタイムカード情報をまとめて組み立てることができるようにする方法です。 特別な Api を使用せずに、従業員情報とタイムカード情報を同じオブジェクトに読み込んでいます。 このコードでは、インメモリデータソースとリモートデータソースに対して機能する標準の LINQ 演算子を使用して、必要な情報だけを表現しています。 EF4 は、LINQ クエリおよび C\# コンパイラによって生成された式ツリーを、効率的な単一の T-sql クエリに変換できました。

``` SQL
    SELECT
    [Limit1].[Id] AS [Id],
    [Limit1].[Name] AS [Name],
    [Limit1].[C1] AS [C1]
    FROM (SELECT TOP (2)
      [Project1].[Id] AS [Id],
      [Project1].[Name] AS [Name],
      [Project1].[C1] AS [C1]
      FROM (SELECT
        [Extent1].[Id] AS [Id],
        [Extent1].[Name] AS [Name],
        (SELECT COUNT(1) AS [A1]
         FROM [dbo].[TimeCards] AS [Extent2]
         WHERE [Extent1].[Id] =
               [Extent2].[EmployeeTimeCard_TimeCard_Id]) AS [C1]
              FROM [dbo].[Employees] AS [Extent1]
               WHERE [Extent1].[Id] = @p__linq__0
         )  AS [Project1]
    )  AS [Limit1]
```

ビューモデルまたは DTO オブジェクトではなく、実際のエンティティを使用しない場合もあります。 従業員*と*従業員のタイムカードが必要であることがわかったら、関連データを控えめで効率的な方法で集中的に読み込むことができます。

### <a name="explicit-eager-loading"></a>明示的な一括読み込み

関連エンティティ情報の読み込みを集中的する場合は、ビジネスロジック (またはこのシナリオではコントローラーアクションロジック) 用のメカニズムを使用して、リポジトリに対する希望を表現する必要があります。 EF4 ObjectQuery&lt;T&gt; クラスは、クエリの実行中に取得する関連オブジェクトを指定する Include メソッドを定義します。 EF4 ObjectContext は、ObjectQuery&lt;T&gt;から継承する具象 ObjectSet&lt;T&gt; クラスを介してエンティティを公開していることに注意してください。  ObjectSet&lt;&gt; T を使用している場合は、次のコードを記述して、各従業員のタイムカード情報の一括読み込みを指定することができます。

``` csharp
    _employees.Include("TimeCards")
              .Where(e => e.HireDate.Year > 2009);
```

ただし、コードをテストできるようにするために、実際の作業単位クラスの外部から ObjectSet&lt;T&gt; を公開しているわけではありません。 代わりに、IObjectSet&lt;T&gt; インターフェイスを使用します。これは簡単に偽装できますが、IObjectSet&lt;T&gt; は Include メソッドを定義していません。 LINQ の利点は、独自の Include 演算子を作成できることです。

``` csharp
    public static class QueryableExtensions {
        public static IQueryable<T> Include<T>
                (this IQueryable<T> sequence, string path) {
            var objectQuery = sequence as ObjectQuery<T>;
            if(objectQuery != null)
            {
                return objectQuery.Include(path);
            }
            return sequence;
        }
    }
```

この Include 演算子は、IObjectSet&lt;T&gt;ではなく、IQueryable&lt;T&gt; の拡張メソッドとして定義されていることに注意してください。 これにより、IQueryable&lt;T&gt;、IObjectSet&lt;T&gt;、ObjectQuery&lt;T&gt;、ObjectSet&lt;T&gt;など、さまざまな種類の型でメソッドを使用できるようになります。 基になるシーケンスが正規の EF4 ObjectQuery&lt;T&gt;ではない場合、問題は発生せず、Include 操作は実行されません。 基になるシーケンス*が*ObjectQuery&lt;t&gt; (または ObjectQuery&lt;t&gt;から派生) である場合、EF4 には追加データの要件が表示され、適切な SQL クエリが生成されます。

この新しいオペレーターを使用して、リポジトリからのタイムカード情報の一括読み込みを明示的に要求できます。

``` csharp
    public ViewResult Index() {
        var model = _unitOfWork.Employees
                               .Include("TimeCards")
                               .OrderBy(e => e.HireDate);
        return View(model);
    }
```

実際の ObjectContext に対して実行すると、コードは次の1つのクエリを生成します。 このクエリでは、従業員オブジェクトを具体化し、それらのタイムカードプロパティを完全に設定するために、データベースから十分な情報が1つのトリップで収集されます。

``` SQL
    SELECT
    [Project1].[Id] AS [Id],
    [Project1].[Name] AS [Name],
    [Project1].[HireDate] AS [HireDate],
    [Project1].[C1] AS [C1],
    [Project1].[Id1] AS [Id1],
    [Project1].[Hours] AS [Hours],
    [Project1].[EffectiveDate] AS [EffectiveDate],
    [Project1].[EmployeeTimeCard_TimeCard_Id] AS [EmployeeTimeCard_TimeCard_Id]
    FROM ( SELECT
         [Extent1].[Id] AS [Id],
         [Extent1].[Name] AS [Name],
         [Extent1].[HireDate] AS [HireDate],
         [Extent2].[Id] AS [Id1],
         [Extent2].[Hours] AS [Hours],
         [Extent2].[EffectiveDate] AS [EffectiveDate],
         [Extent2].[EmployeeTimeCard_TimeCard_Id] AS
                    [EmployeeTimeCard_TimeCard_Id],
         CASE WHEN ([Extent2].[Id] IS NULL) THEN CAST(NULL AS int)
         ELSE 1 END AS [C1]
         FROM  [dbo].[Employees] AS [Extent1]
         LEFT OUTER JOIN [dbo].[TimeCards] AS [Extent2] ON [Extent1].[Id] = [Extent2].[EmployeeTimeCard_TimeCard_Id]
    )  AS [Project1]
    ORDER BY [Project1].[HireDate] ASC,
             [Project1].[Id] ASC, [Project1].[C1] ASC
```

優れたニュースは、アクションメソッド内のコードが完全にテスト可能な状態であることです。 Include 演算子をサポートするために、フェイクの追加機能を提供する必要はありません。 悪い知らせは、永続化を意識せずに、コード内で Include 演算子を使用する必要があるということです。 これは、テスト可能なコードを作成するときに評価する必要があるトレードオフの例です。 リポジトリの抽象化の外部で、パフォーマンスの目標を達成するために、永続化に関する問題が発生する場合があります。

一括読み込みの代わりに、遅延読み込みがあります。 遅延読み込みは、関連付けられたデータの要件を明示的に通知するためにビジネスコードを必要とし*ない*ことを意味します。 代わりに、アプリケーションでエンティティを使用します。追加データが必要な場合は Entity Framework によってデータが要求時に読み込まれます。

### <a name="lazy-loading"></a>遅延読み込み

ビジネスロジックが必要とするデータがわからないシナリオを簡単に想像できます。 このロジックには employee オブジェクトが必要であることがわかっているかもしれませんが、これらのパスの中には従業員からのタイムカード情報を必要とするものもあれば、異なる実行パスに分岐する場合もあります。 このようなシナリオは、データが必要に応じて表示されるため、暗黙的な遅延読み込みに最適です。

遅延読み込みとも呼ばれる遅延読み込みでは、エンティティオブジェクトにいくつかの要件があります。 真の永続性無視を持つ POCOs は、永続化層の要件を満たしていませんが、実際の永続性無視は実際には実現できません。  代わりに、永続性の無視を相対的度単位で測定します。 永続指向の基底クラスから継承する必要がある場合、または特殊なコレクションを使用して POCOs で遅延読み込みを行う必要がある場合は、不幸になります。 幸いなことに、EF4 には、あまり邪魔にならないソリューションがあります。

### <a name="virtually-undetectable"></a>事実上検出不可能

POCO オブジェクトを使用する場合、EF4 はエンティティのランタイムプロキシを動的に生成できます。 これらのプロキシは、具体化された POCOs を非表示にして、追加の作業を実行するために各プロパティの get および set 操作をインターセプトすることによって追加のサービスを提供します このようなサービスの1つは、お探しの遅延読み込み機能です。 別のサービスは、プログラムがエンティティのプロパティ値を変更したときに記録できる効率的な変更追跡メカニズムです。 変更の一覧は、SaveChanges メソッドで、更新コマンドを使用して変更されたエンティティを永続化するために使用されます。

ただし、これらのプロキシを機能させるには、エンティティに対してプロパティの get 操作と set 操作をフックする方法が必要です。プロキシは、仮想メンバーをオーバーライドすることによってこの目標を達成します。 したがって、暗黙的な遅延読み込みと効率的な変更の追跡を行う場合は、POCO クラス定義に戻り、プロパティを virtual としてマークする必要があります。

``` csharp
    public class Employee {
        public virtual int Id { get; set; }
        public virtual string Name { get; set; }
        public virtual DateTime HireDate { get; set; }
        public virtual ICollection<TimeCard> TimeCards { get; set; }
    }
```

Employee エンティティは、ほとんどの場合、永続化に依存しないと言ってもかまいません。 唯一の要件は、仮想メンバーを使用することです。これは、コードのテストの容易性には影響しません。 特別な基底クラスから派生する必要はなく、遅延読み込み専用の特別なコレクションを使用することもできます。 コードに示すように、ICollection&lt;T&gt; を実装するクラスは、関連エンティティを保持するために使用できます。

また、作業単位内で行う必要がある軽微な変更も1つあります。 既定では、ObjectContext オブジェクトを直接操作する場合、遅延読み込みは*無効*になっています。 遅延読み込みを有効にするために ContextOptions プロパティに設定できるプロパティがあります。このプロパティは、どこでも遅延読み込みを有効にする必要がある場合に、実際の作業単位内で設定できます。

``` csharp
    public class SqlUnitOfWork : IUnitOfWork {
         public SqlUnitOfWork() {
             // ...
             _context = new ObjectContext(connectionString);
             _context.ContextOptions.LazyLoadingEnabled = true;
         }    
         // ...
     }
```

暗黙の遅延読み込みが有効になっている場合、アプリケーションコードは従業員と従業員の関連付けられたタイムカードを使用できますが、EF が余分なデータを読み込むために必要な作業を意識意識する必要はありません。

``` csharp
    var employee = _unitOfWork.Employees
                              .Single(e => e.Id == id);
    foreach (var card in employee.TimeCards) {
        // ...
    }
```

遅延読み込みでは、アプリケーションコードの記述が容易になります。また、プロキシマジックを使用すると、コードは完全にテスト可能な状態のままになります。 作業単位のメモリ内フェイクでは、テスト中に必要に応じて、関連するデータを使用して偽エンティティを事前に読み込むことができます。

この時点で、IObjectSet&lt;T&gt; を使用してリポジトリを構築し、抽象化を調べて永続化フレームワークのすべての署名を非表示にします。

## <a name="custom-repositories"></a>カスタムリポジトリ

この記事では、最初に作業単位のデザインパターンを提示したときに、作業単位がどのようなものかを示すサンプルコードを提供しました。 作業中の従業員と従業員のタイムカードシナリオを使用して、この元のアイデアを再提示してみましょう。

``` csharp
    public interface IUnitOfWork {
        IRepository<Employee> Employees { get; }
        IRepository<TimeCard> TimeCards { get;  }
        void Commit();
    }
```

この作業単位と最後のセクションで作成した作業単位の主な違いは、この作業単位が EF4 framework の抽象化を使用しないことです (IObjectSet&lt;T&gt;はありません)。 IObjectSet&lt;T&gt; はリポジトリインターフェイスとして適切に機能しますが、公開される API はアプリケーションのニーズと完全に一致しない可能性があります。 この今後のアプローチでは、カスタム IRepository&lt;T&gt; 抽象化を使用してリポジトリを表現します。

テスト駆動設計、行動駆動設計、およびドメイン主導型の手法の設計に従う多くの開発者は、いくつかの理由により、IRepository&lt;T&gt; アプローチを採用しています。 まず、IRepository&lt;T&gt; インターフェイスは "破損の防止" レイヤーを表します。 ドメイン駆動設計ブックの Eric Evans の説明に従って、破損対策レイヤーは、永続化 API などのインフラストラクチャ Api からドメインコードを分離します。 2つ目の方法として、開発者は、(テストの作成時に検出された) アプリケーションの正確なニーズを満たすメソッドをリポジトリに組み込むことができます。 たとえば、多くの場合、ID 値を使用して1つのエンティティを検索する必要があるため、FindById メソッドをリポジトリインターフェイスに追加できます。  IRepository&lt;T&gt; 定義は次のようになります。

``` csharp
    public interface IRepository<T>
                    where T : class, IEntity {
        IQueryable<T> FindAll();
        IQueryable<T> FindWhere(Expression<Func\<T, bool>> predicate);
        T FindById(int id);       
        void Add(T newEntity);
        void Remove(T entity);
    }
```

ここでは、IQueryable&lt;T&gt; インターフェイスを使用してエンティティコレクションを公開する方法について説明します。 IQueryable&lt;T&gt; を使用すると、LINQ 式ツリーを EF4 プロバイダーにフローし、プロバイダーにクエリの全体像を与えることができます。 2つ目の方法は、IEnumerable&lt;T&gt;を返すことです。これは、EF4 LINQ プロバイダーによって、リポジトリ内に構築された式のみが表示されることを意味します。 リポジトリの外部で行われたグループ化、順序付け、および射影は、データベースに送信される SQL コマンドには構成されないため、パフォーマンスが低下する可能性があります。 一方、リポジトリは IEnumerable&lt;&gt; T だけを返すので、新しい SQL コマンドを使用しても驚くことはありません。 どちらの方法も機能し、どちらの方法もテスト可能な状態を維持します。

ジェネリックと EF4 ObjectContext API を使用して、IRepository&lt;T&gt; インターフェイスの単一の実装を簡単に提供できます。

``` csharp
    public class SqlRepository<T> : IRepository<T>
                                    where T : class, IEntity {
        public SqlRepository(ObjectContext context) {
            _objectSet = context.CreateObjectSet<T>();
        }
        public IQueryable<T> FindAll() {
            return _objectSet;
        }
        public IQueryable<T> FindWhere(
                               Expression<Func\<T, bool>> predicate) {
            return _objectSet.Where(predicate);
        }
        public T FindById(int id) {
            return _objectSet.Single(o => o.Id == id);
        }
        public void Add(T newEntity) {
            _objectSet.AddObject(newEntity);
        }
        public void Remove(T entity) {
            _objectSet.DeleteObject(entity);
        }
        protected ObjectSet<T> _objectSet;
    }
```

IRepository&lt;T&gt; アプローチでは、クライアントがエンティティにアクセスするためにメソッドを呼び出す必要があるため、クエリをさらに制御できます。 メソッド内では、アプリケーションの制約を適用するための追加チェックと LINQ 演算子を提供できます。 インターフェイスには、ジェネリック型パラメーターに対する2つの制約があることに注意してください。 1つ目の制約は、ObjectSet&lt;T&gt;によって要求される及びクラスであり、2つ目の制約は、エンティティに IEntity (アプリケーション用に作成された抽象化) を実装するように強制します。 IEntity インターフェイスでは、エンティティに読み取り可能な Id プロパティが強制的に設定されます。その後、FindById メソッドでこのプロパティを使用できます。 IEntity は、次のコードで定義されています。

``` csharp
    public interface IEntity {
        int Id { get; }
    }
```

IEntity は、このインターフェイスを実装するためにエンティティが必要であるため、永続性の無視の小さな違反と見なすことができます。 永続化の無視はトレードオフに関するものです。多くの場合、FindById の機能は、インターフェイスによって課される制約を上回ることになります。 インターフェイスは、テストの容易性には影響しません。

ライブ IRepository&lt;T&gt; をインスタンス化するには EF4 ObjectContext が必要であるため、具体的な作業単位の実装ではインスタンス化を管理する必要があります。

``` csharp
    public class SqlUnitOfWork : IUnitOfWork {
        public SqlUnitOfWork() {
            var connectionString =
                ConfigurationManager
                    .ConnectionStrings[ConnectionStringName]
                    .ConnectionString;

            _context = new ObjectContext(connectionString);
            _context.ContextOptions.LazyLoadingEnabled = true;
        }

        public IRepository<Employee> Employees {
            get {
                if (_employees == null) {
                    _employees = new SqlRepository<Employee>(_context);
                }
                return _employees;
            }
        }

        public IRepository<TimeCard> TimeCards {
            get {
                if (_timeCards == null) {
                    _timeCards = new SqlRepository<TimeCard>(_context);
                }
                return _timeCards;
            }
        }

        public void Commit() {
            _context.SaveChanges();
        }

        SqlRepository<Employee> _employees = null;
        SqlRepository<TimeCard> _timeCards = null;
        readonly ObjectContext _context;
        const string ConnectionStringName = "EmployeeDataModelContainer";
    }
```

### <a name="using-the-custom-repository"></a>カスタムリポジトリの使用

カスタムリポジトリの使用は、IObjectSet&lt;T&gt;に基づくリポジトリの使用とは大きく異なります。 LINQ 演算子を直接プロパティに適用するのではなく、まず、リポジトリのメソッドを呼び出して、IQueryable&lt;T&gt; 参照を取得する必要があります。

``` csharp
    public ViewResult Index() {
        var model = _repository.FindAll()
                               .Include("TimeCards")
                               .OrderBy(e => e.HireDate);
        return View(model);
    }
```

前に実装したカスタムの Include 演算子が変更なしで動作することに注意してください。 リポジトリの FindById メソッドは、1つのエンティティを取得しようとしているアクションから重複したロジックを削除します。

``` csharp
    public ViewResult Details(int id) {
        var model = _repository.FindById(id);
        return View(model);
    }
```

調査した2つのアプローチのテストの容易性に大きな違いはありません。 前のセクションで行ったのと同じように、HashSet&lt;Employee&gt; によって支えられた具象クラスを構築することにより、IRepository&lt;T&gt; の偽の実装を提供できます。 ただし、一部の開発者は、フェイクを構築するのではなく、モックオブジェクトとモックオブジェクトフレームワークを使用することを希望しています。 モックを使用して実装をテストし、次のセクションでモックとフェイクの違いについて説明します。

### <a name="testing-with-mocks"></a>モックを使用したテスト

Martin Fowler が "test double" を呼び出す対象を構築するには、さまざまな方法があります。 テスト用の double (movie 代役 double など) は、テスト中に実際の実稼働オブジェクト用に "スタンドイン" するために構築するオブジェクトです。 作成したインメモリリポジトリは、SQL Server と通信するリポジトリのテスト double です。 単体テストでこれらのテストの double を使用して、コードを分離し、テストを高速に実行する方法について説明しました。

ビルドしたテストの倍精度浮動小数点数の実装は、実際に動作しています。 各シーンの背後では、オブジェクトの具象コレクションが格納され、テスト中にリポジトリを操作するときに、このコレクションのオブジェクトが追加および削除されます。 実際のコードと実用的な実装を使用して、この方法でテストを作成する開発者もいます。  これらのテスト2つは、*フェイク*と呼ばれるものです。 実装は動作していますが、実稼働環境での使用には十分ではありません。 仮のリポジトリは実際にはデータベースに書き込みを行いません。 偽の SMTP サーバーは、実際にはネットワーク経由で電子メールメッセージを送信しません。

### <a name="mocks-versus-fakes"></a>モックとフェイク

*モック*と呼ばれる別の種類のテスト double があります。 フェイクの実装は正常に実行されていますが、モックには実装がありません。 モックオブジェクトフレームワークを使用すると、これらのモックオブジェクトを実行時に構築し、テスト代替として使用できます。 このセクションでは、オープンソースのモックフレームワーク Moq を使用します。 次に、Moq を使用して、従業員リポジトリのテスト double を動的に作成する簡単な例を示します。

``` csharp
    Mock<IRepository<Employee>> mock =
        new Mock<IRepository<Employee>>();
    IRepository<Employee> repository = mock.Object;
    repository.Add(new Employee());
    var employee = repository.FindById(1);
```

IRepository に対して Moq に&lt;従業員&gt; の実装を要求し、それを動的に構築します。 モック&lt;T&gt; オブジェクトのオブジェクトプロパティにアクセスすることによって、IRepository&lt;Employee&gt; を実装するオブジェクトを取得できます。 これは、コントローラーに渡すことができる内部オブジェクトであり、テスト用の double または実際のリポジトリであるかどうかはわかりません。 実際の実装でオブジェクトに対してメソッドを呼び出すのと同じように、オブジェクトでメソッドを呼び出すことができます。

Add メソッドを呼び出すと、モックリポジトリによって実行される内容を確認する必要があります。 モックオブジェクトの背後に実装が存在しないため、Add は何も行いません。 作成したフェイクとは別の具象コレクションではなく、従業員は破棄されます。 FindById の戻り値について この場合、モックオブジェクトは、既定値を返すことができる処理だけを行います。 参照型 (Employee) を返すため、戻り値は null 値になります。

モックは、意味がない可能性があります。ただし、まだ説明していないモックには2つの機能があります。 まず、Moq フレームワークは、モックオブジェクトに対して行われたすべての呼び出しを記録します。 コードの後半で、Add メソッドを呼び出した場合、またはだれかが FindById メソッドを呼び出した場合は、Moq に要求できます。 この "ブラックボックス" の記録機能をテストで使用する方法については、後で説明します。

2つ目の優れた機能は、Moq を使用して、*予測*されるモックオブジェクトをプログラミングする方法です。 予測は、特定の相互作用に対する応答方法をモックオブジェクトに伝えます。 たとえば、モックに対する予測をプログラミングして、だれかが FindById を呼び出したときに employee オブジェクトを返すように指示することができます。 Moq フレームワークは、セットアップ API とラムダ式を使用して、これらの予測をプログラミングします。

``` csharp
    [TestMethod]
    public void MockSample() {
        Mock<IRepository<Employee>> mock =
            new Mock<IRepository<Employee>>();
        mock.Setup(m => m.FindById(5))
            .Returns(new Employee {Id = 5});
        IRepository<Employee> repository = mock.Object;
        var employee = repository.FindById(5);
        Assert.IsTrue(employee.Id == 5);
    }
```

このサンプルでは、Moq にリポジトリを動的に構築するように指示し、その後、予測を使用してリポジトリをプログラミングします。 この予測では、FindById メソッドを呼び出したときに5の値が渡されたときに、モックオブジェクトが Id 値5の新しい employee オブジェクトを返すように指示します。 このテストは成功するため、完全な実装を作成する必要はありませんでした。&lt;T&gt;をフェイクにします。

先ほど作成したテストにもう一度戻って、フェイクではなくモックを使用するように書き直してみましょう。 前と同様に、基本クラスを使用して、コントローラーのすべてのテストに必要なインフラストラクチャの共通部分を設定します。

``` csharp
    public class EmployeeControllerTestBase {
        public EmployeeControllerTestBase() {
            _employeeData = EmployeeObjectMother.CreateEmployees()
                                                .AsQueryable();
            _repository = new Mock<IRepository<Employee>>();
            _unitOfWork = new Mock<IUnitOfWork>();
            _unitOfWork.Setup(u => u.Employees)
                       .Returns(_repository.Object);
            _controller = new EmployeeController(_unitOfWork.Object);
        }

        protected IQueryable<Employee> _employeeData;
        protected Mock<IUnitOfWork> _unitOfWork;
        protected EmployeeController _controller;
        protected Mock<IRepository<Employee>> _repository;
    }
```

セットアップコードはほとんど同じです。 フェイクを使用する代わりに、Moq を使用してモックオブジェクトを構築します。 基本クラスは、コードが Employees プロパティを呼び出すときに、モックリポジトリを返すようにモックの作業単位を配置します。 モックセットアップの残りの部分は、特定のシナリオごとに専用のテストフィクスチャ内で実行されます。 たとえば、Index アクションのテストフィクスチャは、アクションがモックリポジトリの FindAll メソッドを呼び出したときに、従業員の一覧を返すようにモックリポジトリを設定します。

``` csharp
    [TestClass]
    public class EmployeeControllerIndexActionTests
               : EmployeeControllerTestBase {
        public EmployeeControllerIndexActionTests() {
            _repository.Setup(r => r.FindAll())
                        .Returns(_employeeData);
        }
        // .. tests
        [TestMethod]
        public void ShouldBuildModelWithAllEmployees() {
            var result = _controller.Index();
            var model = result.ViewData.Model
                          as IEnumerable<Employee>;
            Assert.IsTrue(model.Count() == _employeeData.Count());
        }
        // .. and more tests
    }
```

期待を除けば、テストは前に行ったテストに似ています。 ただし、モックフレームワークの記録機能を使用すると、さまざまな角度からテストにアプローチできます。 次のセクションでは、この新しいパースペクティブについて説明します。

### <a name="state-versus-interaction-testing"></a>状態と相互作用テスト

モックオブジェクトを使用してソフトウェアをテストするには、さまざまな方法があります。 1つの方法は、これまでこのホワイトペーパーで行った状態ベースのテストを使用することです。 状態ベースのテストによって、ソフトウェアの状態に関するアサーションが作成されます。 前回のテストでは、コントローラーでアクションメソッドを呼び出し、ビルドする必要のあるモデルに関するアサーションを作成しました。 テストの状態のその他の例を次に示します。

-   作成を実行した後に、リポジトリに新しい employee オブジェクトが含まれていることを確認します。
-   インデックスが実行された後に、モデルがすべての従業員の一覧を保持していることを確認します。
-   削除の実行後に、リポジトリに特定の従業員が含まれていないことを確認します。

モックオブジェクトのもう1つの方法は、*相互作用*を検証することです。 状態ベースのテストでは、オブジェクトの状態に関するアサーションが発生しますが、相互作用ベースのテストでは、オブジェクトがどのように対話するかについてのアサーションを行います 例 :

-   Create の実行時に、コントローラーがリポジトリの Add メソッドを呼び出していることを確認します。
-   インデックスの実行時に、コントローラーによってリポジトリの FindAll メソッドが呼び出されることを確認します。
-   編集の実行時に変更を保存するために、コントローラーが作業単位のコミット方法を呼び出していることを確認します。

相互作用テストでは、コレクション内の調査やカウントの検証が行われないため、テストデータの量が少なくなることがよくあります。 たとえば、Details アクションが、正しい値を持つリポジトリの FindById メソッドを呼び出すことがわかっている場合、アクションは正しく動作していると考えられます。 FindById から返されるテストデータを設定しなくても、この動作を確認できます。

``` csharp
    [TestClass]
    public class EmployeeControllerDetailsActionTests
               : EmployeeControllerTestBase {
         // ...
        [TestMethod]
        public void ShouldInvokeRepositoryToFindEmployee() {
            var result = _controller.Details(_detailsId);
            _repository.Verify(r => r.FindById(_detailsId));
        }
        int _detailsId = 1;
    }
```

上記のテストフィクスチャで必要なセットアップは、基本クラスによって提供されるセットアップだけです。 コントローラーアクションを呼び出すと、Moq はモックリポジトリとのやり取りを記録します。 Moq の Verify API を使用して、コントローラーが適切な ID 値で FindById を呼び出したかどうかを確認できます。 コントローラーがメソッドを呼び出さなかった場合、または予期しないパラメーター値を使用してメソッドを呼び出した場合は、Verify メソッドによって例外がスローされ、テストは失敗します。

次に、作成アクションによって現在の作業単位に対して Commit が呼び出されることを確認する別の例を示します。

``` csharp
    [TestMethod]
    public void ShouldCommitUnitOfWork() {
        _controller.Create(_newEmployee);
        _unitOfWork.Verify(u => u.Commit());
    }
```

相互作用テストの1つの危険性は、相互作用を指定する傾向です。 モックオブジェクトとのすべての相互作用を記録および検証する機能は、テストですべての相互作用を検証する必要があるという意味ではありません。 一部の対話は実装の詳細であり、現在のテストを満たすために*必要な*相互作用だけを検証する必要があります。

モックまたはフェイクの選択は、テストするシステムと、個人 (またはチーム) の設定に大きく左右されます。 モックオブジェクトを使用すると、テストの double を実装するために必要なコードの量を大幅に削減できますが、すべてのユーザーがプログラミングの期待を理解し、相互作用を確認することはできません。

## <a name="conclusions"></a>まとめ

このホワイトペーパーでは、データの永続化に ADO.NET Entity Framework を使用しながら、テスト可能なコードを作成するためのいくつかの方法を説明しました。 IObjectSet&lt;T&gt;などの組み込みの抽象化を活用したり、IRepository&lt;T&gt;のような独自の抽象化を作成したりすることができます。  どちらの場合も、ADO.NET Entity Framework 4.0 での POCO のサポートにより、これらの抽象化のコンシューマーは、永続的ではありませんが、高度なテストが可能になります。 暗黙の遅延読み込みなどの追加の EF4 機能を使用すると、リレーショナルデータストアの詳細を気にせずに、ビジネスおよびアプリケーションのサービスコードを動作させることができます。 最後に、作成した抽象化は単体テスト内で簡単にモックまたはフェイクにすることができます。また、これらのテスト double を使用して、高速な実行、高度に分離された信頼性の高いテストを実現できます。

### <a name="additional-resources"></a>その他のリソース

-   Robert Martin "[単一責任の原則](https://www.objectmentor.com/resources/articles/srp.pdf)"
-   Martin Fowler、*エンタープライズアプリケーションアーキテクチャのパターン*[のパターンのカタログ](https://www.martinfowler.com/eaaCatalog/index.html)
-   Griffin Caprio、「[依存関係の注入](https://msdn.microsoft.com/magazine/cc163739.aspx)」
-   データプログラミングに関するブログ「[チュートリアル: Entity Framework 4.0 を使用したテスト駆動開発](https://blogs.msdn.com/adonet/pages/walkthrough-test-driven-development-with-the-entity-framework-4-0.aspx)」をご覧ください。
-   データプログラミングのブログ「 [Entity Framework 4.0 でのリポジトリと作業単位のパターンの使用](https://blogs.msdn.com/adonet/archive/2009/06/16/using-repository-and-unit-of-work-patterns-with-entity-framework-4-0.aspx)」
-   Aaron Jensen、「[マシン仕様の紹介](http://codebetter.com/blogs/aaron.jensen/archive/2008/05/08/introducing-machine-specifications-or-mspec-for-short.aspx)」
-   Eric Lee、" [BDD With MSTest](https://blogs.msdn.com/elee/archive/2009/01/20/bdd-with-mstest.aspx)"
-   Eric Evans ([ドメイン駆動設計](https://books.google.com/books?id=7dlaMs0SECsC&printsec=frontcover&dq=evans%20domain%20driven%20design&hl=en&ei=cHztS6C8KIaglAfA_dS1CA&sa=X&oi=book_result&ct=result&resnum=1&ved=0CCoQ6AEwAA))
-   Martin Fowler、"[モックはスタブ](https://martinfowler.com/articles/mocksArentStubs.html)ではない"
-   Martin Fowler、" [Test Double](https://martinfowler.com/bliki/TestDouble.html)"
-   [Moq](https://code.google.com/p/moq/)

### <a name="biography"></a>略歴

Scott Allen は、Pluralsight のテクニカルスタッフのメンバーであり、OdeToCode.com の創設者です。 15年間の商用ソフトウェア開発において、8ビット embedded デバイスから拡張性の高い ASP.NET web アプリケーションまで、すべてのソリューションについて Scott が取り組んでいます。 OdeToCode、または[https://twitter.com/OdeToCode](https://twitter.com/OdeToCode)の Twitter で Scott に連絡できます。
