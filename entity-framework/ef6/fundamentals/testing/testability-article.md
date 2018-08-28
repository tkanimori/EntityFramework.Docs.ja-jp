---
title: テストの容易性と Entity Framework 4.0
author: divega
ms.date: 2016-10-23
ms.assetid: 9430e2ab-261c-4e8e-8545-2ebc52d7a247
ms.openlocfilehash: 17a9f09022531a81042979464de05fbbd2570759
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42995230"
---
# <a name="testability-and-entity-framework-40"></a>テストの容易性と Entity Framework 4.0
Scott Allen

公開日: 2010 年 5 月

## <a name="introduction"></a>はじめに

このホワイト ペーパーでは、について説明し、ADO.NET Entity Framework 4.0 と Visual Studio 2010 でのテストが容易なコードを記述する方法を示します。 このホワイト ペーパーは、テスト駆動型設計 (TDD) や動作駆動型設計 (BDD) などの特定のテスト手法に重点を試みません。 代わりにこのホワイト ペーパーは、ADO.NET Entity Framework を使用してまだを特定し、自動でテストしやすいコードを記述する方法について学びます。 データ アクセス シナリオのテストを容易になり、フレームワークを使用するときに、これらのパターンを適用する方法について一般的な設計パターンに注目します。 これらの機能をテストしやすいコードに機能させる方法を確認するフレームワークの特定の機能についても確認します。

## <a name="what-is-testable-code"></a>テストが容易なコードとは何ですか。

自動化された単体テストを使用してソフトウェアを確認する機能では、多くの望ましいメリットは提供します。 すべてのユーザーには、適切なテストがアプリケーションと増加インプレース単体テストを導入することで、アプリケーションの品質がバグを発見してだけ自体よりもはるかにソフトウェアの不具合の数を減らすことが認識しています。

適切な単体テスト スイートには、時間を節約し、制御が作成したソフトウェアを開発チームができます。 チームは、既存のコード、リファクタリングの再設計に変更を加えることができ、新しい要件を満たし、テスト スイートを把握しながら、アプリケーションに新しいコンポーネントを追加するソフトウェアを再構築は、アプリケーションの動作を確認できます。 単体テストは、変更を容易になり、複雑さの増大に応じて、ソフトウェアの保守容易性を維持する簡単なフィードバック サイクルの一部です。

ユニット テストには、価格、ただしします。 チームを作成して単体テストを管理する時間を費やす必要があります。 これらのテストを作成するために必要な作業量に直接関連する、 **testability**の基になるソフトウェアです。 テストするソフトウェアを簡単にですか。 ソフトウェアのテストの容易性を考慮して設計チームは、されていないテスト可能なソフトウェアを使用するチームよりも高速効果的なテストを作成します。

Microsoft では、テストの容易性を考慮して、ADO.NET Entity Framework 4.0 (EF4) が設計されています。 これは開発者が単体テスト フレームワーク コード自体を記述するという意味しません。 代わりに、EF4 のテストの容易性の目標を使用すると、簡単にビルド フレームワークの上にテストしやすいコードを作成できます。 具体的な例を見ると、前に、テストしやすいコードの品質を理解することをお勧めします。

### <a name="the-qualities-of-testable-code"></a>テスト可能なコードの品質評価

コードを簡単にテストできますが、少なくとも 2 つの特徴を示す常にします。 まず、テスト可能なコードが簡単に**観察**します。 入力のいくつかのセットを指定するには、ことが容易に、コードの出力を確認できます。 たとえば、次のメソッドのテストは簡単、メソッドが直接計算の結果を返すためです。

``` csharp
    public int Add(int x, int y) {
        return x + y;
    }
```

メソッドをテストすることは、ネットワーク ソケット、データベース テーブル、または次のコードのようなファイルに、計算された値を書き込む場合は困難です。 テストは、値を取得する追加の作業を実行する必要があります。

``` csharp
    public void AddAndSaveToFile(int x, int y) {
         var results = string.Format("The answer is {0}", x + y);
         File.WriteAllText("results.txt", results);
    }
```

次に、テストしやすいコードが簡単に**分離**します。 次の擬似コードを使用してテストしやすいコードの悪い例としてしましょう。

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

メソッドが容易に確認できます: 保険ポリシーで合格し、戻り値が予期される結果と一致することを確認します。 ただし、方法をテストする適切なスキーマにインストールされているデータベースがあり、メソッドが、電子メールを送信しようとしています。 場合に、SMTP サーバーを構成する必要があります。

単体テストは、メソッド内で計算ロジックの検証にだけ関心がありますが、電子メール サーバーがオフライン、またはデータベース サーバーに移動しているため、テストが失敗します。 このようなエラーの両方は、テストしたいことを確認の動作に関連しません。 動作は、特定が困難です。

多くの場合、テストしやすいコードを記述するソフトウェア開発者は、書き込むことが、コード内では関心の分離の維持に努めています。 上記のメソッドは、ビジネスの計算に集中し、他のコンポーネントにデータベース、および電子メールの実装の詳細を委任する必要があります。 Robert C. Martin はこれ単一責任の原則を呼び出します。 オブジェクトは、ポリシーの値を計算するように、1 つ、幅が狭い責任をカプセル化する必要があります。 データベースと通知の他のすべての作業を他のオブジェクトの責任となります。 このような方法で記述されたコードは、1 つのタスクにフォーカスがあるため、分離に簡単です。

.NET では、単一責任の原則に従い、分離を実現する必要があります、抽象化があります。 インターフェイス定義を使用してを具象型ではなく、インターフェイスの抽象化を使用するコードを強制できます。 このホワイト ペーパーの後半で見て上で示した悪い例を使用できるようにメソッドをインターフェイスをどのように*検索*データベースに連絡があるようにします。 ただし、テスト時には、データベースと通信しませんが、代わりにデータをメモリに保持するダミーの実装を置き換えるできます。 このダミー実装は、データ アクセス コードまたはデータベースの構成関連のない問題が原因でコードが分離されます。

その他の分離の利点があります。 最後のメソッドでのビジネスの計算を実行するには数ミリ秒かかるだけが、テスト自体は、さまざまなサーバーをネットワークとプレゼンテーションを囲むコード ホップとしての数秒間の実行可能性があります。 単体テストは、小さな変更を容易に高速に実行する必要があります。 単体テストも繰り返し使用できるし、テストとは無関係なコンポーネントの問題があるため、失敗する必要があります。 コードのテストの記述を簡単に時間がある開発者が容易に確認し、分離コードの意味を記述、テストを実行するを待つ時間が短縮しより重要なは、存在しないバグを追跡する時間が短縮します。

うまくいけばテストのメリットを評価し、テストしやすいコードを示す特性を理解できます。 監視可能な簡単に分離すると、残りの中に、データベースにデータを保存する EF4 で動作するコードを記述する方法について説明しようとしていますが、まずデータ アクセスのテストが容易な設計の説明について見てを絞り込みます。

## <a name="design-patterns-for-data-persistence"></a>データの永続性の設計パターン

不良の前に示した例の両方には、多くの責任が必要があります。 無効な最初の例は、計算を実行する必要がある*と*ファイルに書き込みます。 不適切な 2 番目の例は、データベースからデータを読み取る必要がある*と*ビジネス計算を実行する*と*電子メールを送信します。 懸念事項を分離し、他のコンポーネントに責任を委任する方法を小さくデザインしてテストしやすいコードを記述する方向に大きなメリットをもたらすことになります。 目標は、小規模およびフォーカスのある抽象化からアクションを作成することによって機能を構築します。

データ永続化する小さなと探しているフォーカスがあるの抽象化は一般的なので、設計パターンとして文書化されてしました。 Martin Fowler の書籍のエンタープライズ アプリケーション アーキテクチャのパターンでは、印刷でこれらのパターンを記述する最初の作業をしました。 これらの ADO.NET Entity Framework を実装およびこれらのパターンと連携する方法を紹介する前にように次のセクションでは、これらのパターンの簡単な説明を提供します。

### <a name="the-repository-pattern"></a>リポジトリ パターン

Fowler は、リポジトリ「間を仲介のドメインとデータ コレクションのようなインターフェイスを使用して、ドメイン オブジェクトにアクセスするためのマッピング レイヤー」を述べています。 リポジトリ パターンの目的は、データのアクセスの些細からコードを分離して、分離以前説明したように、テストの容易性に必要な特徴です。

分離、重要では、リポジトリがコレクションのようなインターフェイスを使用してオブジェクトを公開する方法です。 リポジトリには考え方がない方法を使用して書き込むロジック、リポジトリは要求したオブジェクトを具体化します。 リポジトリが、データベースと通信またはだけメモリ内コレクションからオブジェクトを返すこと可能性があります。 コードを知る必要があります。 すべては、コレクションを維持するために、リポジトリが表示されます、取得、追加、およびコレクションからオブジェクトを削除できます。

既存の .NET アプリケーションで具体的なリポジトリは、多くの場合、次のようなジェネリック インターフェイスから継承します。

``` csharp
    public interface IRepository<T> {       
        IEnumerable<T> FindAll();
        IEnumerable<T> FindBy(Expression<Func\<T, bool>> predicate);
        T FindById(int id);
        void Add(T newEntity);
        void Remove(T entity);
    }
```

EF4 の実装を提供しますが、基本的な概念は変わりませんインターフェイス定義にいくつかの変更を行ったします。 コードでは、このインターフェイスを実装する具体的なリポジトリを使用して、述語の評価に基づいてエンティティのコレクションを取得する、主キーの値でエンティティを取得したり、単に使用できるすべてのエンティティを取得することができます。 コードを追加し、リポジトリ インターフェイスを使用してエンティティの削除もできます。

IRepository の Employee オブジェクトを指定するには、コードは、次の操作を実行できます。

``` csharp
    var employeesNamedScott =
        repository
            .FindBy(e => e.Name == "Scott")
            .OrderBy(e => e.HireDate);
    var firstEmployee = repository.FindById(1);
    var newEmployee = new Employee() {/*... */};
    repository.Add(newEmployee);
```

インターフェイス (IRepository の従業員) コードを使用しているため、コード、インターフェイスのさまざまな実装を提供できます。 1 つの実装には、EF4 と永続化するオブジェクト、Microsoft SQL Server データベースに基づく実装可能性があります。 (1 つのテスト中に使用して)、さまざまな実装は、メモリ内の従業員の一覧のオブジェクトによってバックアップ可能性があります。 インターフェイスは、コードでの分離を実現するために役立ちます。

IRepository に注意してください&lt;T&gt;インターフェイスは、保存操作を公開しません。 既存のオブジェクトを更新する方法 IRepository 定義が含まれて、保存操作が遭遇する可能性があり、これらのリポジトリの実装が直ちにデータベースにオブジェクトを保存する必要があります。 ただし、多くのアプリケーションでオブジェクトを個別に保存するはありません。 代わりに、オブジェクトをライフ、おそらく、別のリポジトリから、ビジネス アクティビティの一部としてこれらのオブジェクトを変更し、1 つのアトミック操作の一部としてすべてのオブジェクトを保持します。 さいわい、この種類の動作を許可するためのパターンがあります。

### <a name="the-unit-of-work-pattern"></a>Unit of Work パターン

Fowler は、作業単位が「の管理オブジェクトの一覧がビジネス トランザクションによって影響を受けるし、変更の書き込みと同時実行の問題の解決を調整」を述べています。 リポジトリからを実現し、お知らせ、unit of work、変更をコミットするときに、オブジェクトになりました変更を保存、オブジェクトに対する変更を追跡する作業の単位の役目です。 新しいオブジェクトのすべてのリポジトリに追加したし、データベース、およびも管理の削除に、オブジェクトを挿入する作業の単位の責任です。

ADO.NET データセットでこれまでの作業を実行した場合を既に理解しておく、unit of work パターン。 ADO.NET データセット、更新、削除、および DataRow オブジェクトの挿入を追跡する機能がありでした (TableAdapter のヘルプ) の調整、すべての変更をデータベースにします。 ただし、DataSet オブジェクト モデルの基になるデータベースのサブセットを切断します。 Unit of work パターンは、ビジネス オブジェクトやデータ アクセス コードから分離されており、データベースを認識しないドメイン オブジェクトが、同じ動作を示します。

.NET コードでの作業単位をモデル化する抽象化は、次のようになります。

``` csharp
    public interface IUnitOfWork {
        IRepository<Employee> Employees { get; }
        IRepository<Order> Orders { get; }
        IRepository<Customer> Customers { get; }
        void Commit();
    }
```

作業の 1 つの単位を確認できる作業単位からのリポジトリの参照を公開することでは、オブジェクトは、ビジネス トランザクション中に具体化されたすべてのエンティティを追跡する機能を持ちます。 実際の作業単位の Commit メソッドの実装をデータベースにメモリ内の変更を調整するのには、すべてのマジックが行われる場所です。 

IUnitOfWork の参照を指定すると、コードは 1 つまたは複数のリポジトリから取得されたビジネス オブジェクトに変更を加えるし、アトミックなコミット操作を使用してすべての変更を保存します。

``` csharp
    var firstEmployee = unitofWork.Employees.FindById(1);
    var firstCustomer = unitofWork.Customers.FindById(1);
    firstEmployee.Name = "Alex";
    firstCustomer.Name = "Christopher";
    unitofWork.Commit();
```

### <a name="the-lazy-load-pattern"></a>遅延読み込みのパターン

Fowler は、名前の遅延読み込みを使用して、「オブジェクトのすべてのデータが含まれていないをする必要がありますがそれを取得する方法を知っている」について説明します。 透過的な遅延読み込みするときに重要な機能は、テスト可能なビジネスのコードを記述し、リレーショナル データベースを使用します。 たとえば、次のコードを検討してください。

``` csharp
    var employee = repository.FindById(id);
    // ... and later ...
    foreach(var timeCard in employee.TimeCards) {
        // .. manipulate the timeCard
    }
```

タイムカードのコレクションの設定方法 2 つの解答があります。 1 つの答えは、従業員をフェッチするように求められたら、従業員のリポジトリが両方と関連付けられているタイム カードについては、従業員の従業員を取得するクエリを発行します。 一般に結合句を使用したクエリが要求され、アプリケーションより多くの情報の取得中に発生する可能性があります、リレーショナル データベースで必要があります。 場合、アプリケーション タイムカードを実際のプロパティをタッチする必要はありませんか。

2 番目の応答では、"オンデマンドで"タイムカードを実際のプロパティを読み込めません。 この遅延読み込みでは、暗黙の型とビジネス ロジックに透過的なコードがタイム カード情報を取得する特殊な Api を呼び出さないためには。 コードでは、タイム カード情報が必要なときに前提としています。 メソッドの呼び出しの実行時のインターセプトは一般に、遅延読み込みを伴う不思議な力です。 インターセプトのコードは、データベースと通信し、ビジネス ロジックを自由にビジネス ロジックはそのままタイム カード情報を取得する責任を負います。 この遅延読み込みマジックではデータ取得操作とより多くのテストが容易なコードの結果から自体を分離するビジネス コード。

アプリケーションを遅延読み込みの欠点は*は*コードでは、追加のクエリを実行します。 タイム カード情報が必要です。 そうでは問題になるパフォーマンス重視のアプリケーションまたは (問題 N + 1 と呼ばれます、ループの各反復中にタイム カードを取得するアプリケーションをさまざまな従業員のオブジェクトをループ処理し、クエリを実行するが、多くのアプリケーションクエリの問題の場合)、遅延読み込みは、ドラッグします。 これらのシナリオで、アプリケーションが集中的に可能な最も効率的な方法でタイム カード情報を読み込むしようとする可能性があります。

さいわい、どのように EF4 が両方の暗黙的な遅延読み込みをサポートし、効率的な一括で読み込むように、次のセクションに移動し、これらのパターンを実装しましたが表示されます。

## <a name="implementing-patterns-with-the-entity-framework"></a>Entity Framework でのパターンを実装します。

良い知らせは、最後のセクションで説明した設計パターンのすべてが EF4 で実装が簡単であります。 単純な ASP.NET MVC アプリケーションを使用して編集し、従業員とその関連タイム カード情報を表示するかを示します。 次"plain old CLR object"を使用することから始めます (Poco)。 

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

これらのクラス定義は、さまざまなアプローチと EF4 の機能について検証が永続性として依存 (PI) 可能な限りこれらのクラスを保持する目的は、若干変更されます。 PI オブジェクトを知らない*方法*、あるいは*場合*を保持している状態が、データベース内に存在します。 PI と Poco がテスト可能なソフトウェアが連携します。 POCO アプローチを使用して、オブジェクトは、小さい制約付きより柔軟に簡単に存在するデータベースを使用せずに操作できるようにするためのテストです。

インプレース Poco Visual Studio で Entity Data Model (EDM) を作成できます (図 1 参照)。 エンティティのコードを生成するのには、EDM を使用しません。 代わりに、手動で作成一目みてエンティティを使用します。 データベース スキーマを生成し、EF4 をデータベースにオブジェクトをマップする必要があるメタデータを提供する、EDM にのみ使用します。

![eftest_01](~/ef6/media/eftest-01.jpg)

**図 1**

注: まず EDM モデルを開発する場合は、クリーン、EDM から POCO コードを生成すること データ プログラマビリティ チームによって提供される Visual Studio 2010 拡張機能で、これを行うことができます。 拡張機能をダウンロードするには、Visual Studio の [ツール] メニューから拡張機能マネージャーを起動し、"POCO"(図を参照して 2) のテンプレートのオンライン ギャラリーを検索します。 POCO テンプレートがいくつかは EF 使用できます。 テンプレートの使用に関する詳細については、次を参照してください。"[チュートリアル: Entity Framework 用のテンプレート POCO](http://blogs.msdn.com/adonet/pages/walkthrough-poco-template-for-the-entity-framework.aspx)"。

![eftest_02](~/ef6/media/eftest-02.png)

**図 2**

この POCO の開始点からは 2 つの異なる方法をについて説明をテストしやすいコードです。 最初のアプローチは呼び出す作業とリポジトリの単位を実装するために Entity Framework API から抽象化を利用するため、EF のアプローチです。 2 番目の方法は、独自のカスタム リポジトリの抽象化を作成し、長所と短所のそれぞれの方法を参照してください。 EF のアプローチを探索から始めます。  

### <a name="an-ef-centric-implementation"></a>EF の中心とした実装

ASP.NET MVC プロジェクトから次のコント ローラー アクションを検討してください。 アクションは、Employee オブジェクトを取得し、従業員の詳細なビューを表示する結果を返します。

``` csharp
    public ViewResult Details(int id) {
        var employee = _unitOfWork.Employees
                                  .Single(e => e.Id == id);
        return View(employee);
    }
```

テストが容易なコードですか。 アクションの動作を確認する必要がありますが、少なくとも 2 つのテストがあります。 最初に、アクションを返します、正しいビュー – 簡単にテストすることを確認してみましょう。 アクションが正しい従業員を取得したいと思いますデータベース クエリを実行するコードの実行なしで行うことを確認するテストを記述することもなります。 テスト対象のコードを分離することを覚えておいてください。 データ アクセス コードまたはデータベースの構成でのバグにより、テストが失敗しない分離が保証されます。 テストが失敗した場合、およびいくつかの下位レベルのシステム コンポーネントではなく、コント ローラー ロジックの不具合があるがわかります。

分離を実現するために必要があります表示されるインターフェイスのようないくつかの抽象化前のリポジトリと作業単位です。 リポジトリ パターンがドメイン オブジェクトとデータ マッピング レイヤーの間を仲介するように設計してください。 このシナリオの EF4 で*は*データ マッピング レイヤー、および IObjectSet という名前のリポジトリのような抽象化で提供されている&lt;T&gt; (System.Data.Objects 名前空間) から。 インターフェイス定義は、次のようになります。

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

IObjectSet&lt;T&gt;オブジェクトのコレクションに似ているために、リポジトリの要件を満たしている (IEnumerable を使用して&lt;T&gt;) を追加し、シミュレートされたコレクションからオブジェクトを削除するメソッドを提供します。 アタッチとデタッチの方法では、EF4 API の追加機能を公開します。 IObjectSet を使用する&lt;T&gt;リポジトリのインターフェイスとして作業の抽象化の単位にリポジトリをまとめてバインドする必要があります。

``` csharp
    public interface IUnitOfWork {
        IObjectSet<Employee> Employees { get; }
        IObjectSet<TimeCard> TimeCards { get; }
        void Commit();
    }
```

このインターフェイスの 1 つの具象実装では、SQL Server と対話して、簡単に EF4 から ObjectContext クラスを使用して作成できます。 ObjectContext クラスは、EF4 API での作業の実際の単位です。

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

取り込む、IObjectSet&lt;T&gt;ライフを ObjectContext オブジェクトの CreateObjectSet メソッドを呼び出すだけです。 バック グラウンドで、フレームワークは、メタデータを使用具象 ObjectSet を生成するために、EDM に用意されています&lt;T&gt;します。 ここで、IObjectSet を返すことで引き続き使用します&lt;T&gt;インターフェイスのクライアント コードでテストの容易性を保持するのに役立つためです。

この具体的な実装は、運用環境で便利ですが、IUnitOfWork の抽象化を使用して、テストを容易にする方法に注目する必要があります。

### <a name="the-test-doubles"></a>テスト代替

コント ローラー アクションを分離するには、実際の作業単位 (ObjectContext でサポート) と作業 (メモリ内の操作を実行する) のテスト ダブルか「偽」ユニット間で切り替えることができる必要があります。 この種類の切り替えを実行する一般的なアプローチでは、インスタンスの作業単位が代わりに、コンス トラクター パラメーターとしてコント ローラーには、パスの作業単位、MVC コント ローラーを追加できません。

``` csharp
    class EmployeeController : Controller {
      publicEmployeeController(IUnitOfWork unitOfWork)  {
          _unitOfWork = unitOfWork;
      }
      ...
    }
```

上記のコードは、依存関係の挿入の例です。 コント ローラー (作業単位) の依存関係の作成しますが、コント ローラーに、依存関係を挿入することはできません。 MVC プロジェクトで、制御の反転 (IoC) コンテナーを組み合わせてカスタム コント ローラー ファクトリを使用して、依存関係の挿入を自動化する一般的です。 これらのトピックは、この記事の範囲を超えていますが、次のこの記事の最後に参照よりで読み取ることができます。

テストに使用できる作業の実装の偽の単位は、次のようになります。

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

コミットのプロパティを公開する偽 unit of work に注意してください。 機能テストを簡素化するクラスをフェイクに追加する便利な場合があります。 ここでは、容易にコードがコミットされたプロパティをチェックしての作業単位をコミットするかどうかを確認できます。

フェイク IObjectSet 必要がありますも&lt;T&gt;従業員やタイムカードのオブジェクトをメモリに保持します。 ジェネリックを使用して単一の実装を提供できます。

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

このテスト代替はほとんどの基になる、HashSet に作業を委任&lt;T&gt;オブジェクト。 注その IObjectSet&lt;T&gt;クラス (参照型) として T を適用するジェネリック制約を必要とし、強制的に IQueryable を実装することにも&lt;T&gt;します。 IQueryable として表示される、メモリ内コレクションを作成する簡単&lt;T&gt; AsQueryable、標準的な LINQ 演算子を使用します。

### <a name="the-tests"></a>テスト

従来の単体テストでは、1 つのテスト クラスを 1 つの MVC コント ローラーのすべての操作のすべてのテストを保持するために使用します。 記述できます、これらのテストまたは単体テストでは、任意の型のメモリを使用して fakes を作成しました。 ただし、回避はこの記事で、モノリシック テスト クラス アプローチし、特定の機能に集中するテストをグループ代わりにします。  たとえば、「新しい従業員の作成」ようにをテストする 1 つのテスト クラスを使用して、新しい従業員の作成を担当する 1 つのコント ローラー アクションを確認する機能があります。

きめ細かな設定のテスト クラスをすべての必要ないくつかの一般的なセットアップ コードがあります。 たとえば、常に必要があります、メモリ内リポジトリおよびフェイク作業単位を作成します。 フェイク unit of work の挿入と従業員のコント ローラーのインスタンスも必要があります。 基本クラスを使用して、テスト クラスにまたがるこの共通のセットアップ コードをご説明します。

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

基底クラスで使用して「オブジェクト母」は、テスト データを作成するための 1 つの一般的なパターンです。 オブジェクトの母なるには、複数のテスト フィクスチャの間で使用するためのテスト エンティティのインスタンスを作成するファクトリ メソッドが含まれています。

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

さまざまなテスト フィクスチャが (図 3 参照)、EmployeeControllerTestBase 基底クラスとして使用できます。 各テスト フィクスチャは特定のコント ローラー アクションをテストします。 たとえば、(従業員を作成するためのビューを表示) する HTTP GET 要求、中に使用する作成操作のテストに 1 つのテスト フィクスチャ、重点的し、さまざまなフィクスチャ、重点的に HTTP POST 要求で使用されている作成アクション (によって送信された情報を取得する、ユーザーの従業員を作成する)。 各派生クラスは、特定のコンテキストで、その特定のテストのコンテキストの結果を確認するために必要なアサーションを提供するために必要なセットアップを担当します。

![eftest_03](~/ef6/media/eftest-03.png)

**図 3**

ここでは名前付け規則とテストのスタイルがテストしやすいコードの必要はありません – アプローチの 1 つだけです。 図 4 は、Jet の頭脳の Resharper で実行されているテストでは、Visual Studio 2010 のランナーのプラグインをテストします。

![eftest_04](~/ef6/media/eftest-04.png)

**図 4**

共有のセットアップ コードを処理する基本クラスを各コント ローラー アクションの単体テストは、小さく、簡単に記述できます。 テストでは、すばやく (からのメモリ内の操作を実行しました) 実行され、(ため、テスト対象の単位を分離していること) 関連のないインフラストラクチャや環境の懸念事項により失敗することはできません。

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

これらのテストのセットアップ作業のほとんどは、基本クラス。 基底クラスのコンス トラクターは、メモリ内リポジトリ、作業、フェイク単位および EmployeeController クラスのインスタンスを作成します。 注意してください。 テスト クラスでは、この基本クラスから派生し、テストを作成する方法の詳細に重点を置いています。 この場合、詳細は単体テストの手順で表示されます「整列、動作、およびアサート」の手順に要約します。

-   受信データをシミュレートするために newEmployee オブジェクトを作成します。
-   EmployeeController の作成アクションを呼び出すし、newEmployee を渡します。
-   作成アクション (リポジトリで、従業員が表示されます)、期待される結果を生成することを確認します。

構築した EmployeeController アクションのいずれかをテストできます。 たとえば、従業員のコント ローラーの Index アクションのテストを記述するとき、テストのために同じ基本設定を確立するために、テストの基本クラスから継承できます。 もう一度、基底クラスでは、メモリ内リポジトリ、偽の unit of work、および EmployeeController のインスタンスは作成します。 インデックス アクションの呼び出しに注目するだけで、インデックス アクションのテストにし、テスト アクション、モデルの品質を返します。

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

ここではメモリ内の fakes を作成、テストがテスト指向です、*状態*ソフトウェア。 たとえば、– 作成アクションの実行後に、リポジトリの状態を検査する作成アクションをテストするときに、リポジトリ保持して、新しい従業員でしょうか。

``` csharp
    [TestMethod]
    public void ShouldAddNewEmployeeToRepository() {
        _controller.Create(_newEmployee);
        Assert.IsTrue(_repository.Contains(_newEmployee));
    }
```

後でベースの相互作用のテストに説明します。 テスト対象のコードが、オブジェクトに対する適切なメソッドを呼び出すし、適切なパラメーターを渡されたかどうか、ベースの相互作用のテストが求められます。 ここで進みますカバーのもう 1 つの設計パターン: 遅延読み込み。

## <a name="eager-loading-and-lazy-loading"></a>一括読み込みと遅延読み込み

ある時点で ASP.NET MVC web アプリケーションは従業員の情報を表示し、従業員を含めることがあるには、タイム カードが関連付けられています。 たとえば、システムで、従業員の名前およびタイム カードの合計数を示すタイム カードの概要の表示があります。 この機能を実装するためのいくつかの方法があります。

### <a name="projection"></a>射影

概要を作成する 1 つの簡単なアプローチでは、専用のビューに表示する情報をモデルを作成します。 このシナリオでは、モデルは、次のようになります。

``` csharp
    public class EmployeeSummaryViewModel {
        public string Name { get; set; }
        public int TotalTimeCards { get; set; }
    }
```

EmployeeSummaryViewModel はエンティティではありません –、つまりは何かのデータベースに保持することに注意してください。 厳密に型指定された方法で、ビューにデータをシャッフルするこのクラスを使用するつもりはのみです。 ビュー モデルではない動作 (メソッドなし) – プロパティのみが含まれているために、データ転送オブジェクト (DTO)。 プロパティでは、移動する必要があります。 データを保持します。 LINQ の標準的な射影演算子 – Select 演算子を使用して、このビュー モデルをインスタンス化を簡単になります。

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

上記のコードの 2 つの注目すべき機能があります。 まず – コードは、簡単に観察し、分離がまだ簡単なことをテストできます。 Select 演算子と同様、メモリ内の fakes に対しては同様に機能に対する実際の作業単位です。

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

2 番目の注目すべき機能は、コードにより、EF4 従業員とタイム カード情報を一緒にアセンブルする 1 つで効率的なクエリを生成する方法です。 任意の特殊な Api を使用せず、同じオブジェクトに従業員情報とタイム カード情報を読み込むしました。 コードでは、メモリ内データ ソースとリモート データ ソースに対して動作する標準的な LINQ 演算子を使用して必要な情報だけで表されます。 EF4 が LINQ クエリと C で生成された式ツリーに変換できた\#コンパイラに 1 つで効率的な T-SQL クエリ。

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
         )  AS [Project1]
    )  AS [Limit1]
```

ビュー モデルまたは DTO のオブジェクトではなく、実際のエンティティを操作したくとそれ以外の場合があります。 従業員必要がありますがわかっています*と*従業員のタイム カードでは、集中的に、控えめな効率的な方法で、関連するデータを読み込むことができます。

### <a name="explicit-eager-loading"></a>明示的な一括読み込み

いくつかのメカニズムが必要なビジネス ロジックの (または、このシナリオでは、コント ローラー アクション ロジックで) 関連するエンティティ情報を集中的にロードするときに、リポジトリの希望を表現します。 EF4 ObjectQuery&lt;T&gt;クラスは、クエリ中に取得する関連オブジェクトを指定する Include メソッドを定義します。 EF4 ObjectContext は、具体的な ObjectSet を介してエンティティを公開します。 注意してください。&lt;T&gt; ObjectQuery から継承されるクラス&lt;T&gt;します。  ObjectSet を使用していた場合&lt;T&gt;次のコードを記述できます、コント ローラー アクション内の参照の各従業員のタイム カード情報を一括で読み込みを指定します。

``` csharp
    _employees.Include("TimeCards")
              .Where(e => e.HireDate.Year > 2009);
```

ただし、テスト可能なコードを保持しようとしていますのでされることはありません ObjectSet&lt;T&gt;から外部作業クラスの実際の単位。 代わりに、我々 が依存、IObjectSet&lt;T&gt;偽より簡単なインターフェイスが IObjectSet&lt;T&gt; Include メソッドを一切定義しません。 LINQ の優れた点は、独自の Include 演算子を作成することです。

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

IQueryable 拡張メソッドとしてこの Include 演算子が定義されて&lt;T&gt; IObjectSet ではなく&lt;T&gt;します。 これにより、幅広い範囲の IQueryable を含む、型のメソッドを使用して&lt;T&gt;、IObjectSet&lt;T&gt;、ObjectQuery&lt;T&gt;、および ObjectSet&lt;T&gt;します。 基になるシーケンスが正規の EF4 ObjectQuery イベント&lt;T&gt;Include 演算子は no-op、弊害はありません。 シーケンス、基になる場合*は*ObjectQuery&lt;T&gt; (または ObjectQuery から派生した&lt;T&gt;)、EF4、要件に追加のデータを参照してください、適切な SQL を作成し、クエリ。

配置でこの新しい演算子を持つリポジトリからタイム カード情報の一括読み込みを明示的に要求できます。

``` csharp
    public ViewResult Index() {
        var model = _unitOfWork.Employees
                               .Include("TimeCards")
                               .OrderBy(e => e.HireDate);
        return View(model);
    }
```

実際の ObjectContext に対して実行すると、コードには、次の 1 つのクエリが生成されます。 クエリでは、employee オブジェクトを具体化して、完全、タイムカードを実際のプロパティを設定する 1 回のトリップで、データベースから十分な情報を収集します。

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
         FROM  [dbo].[Employees] AS [Extent1]
         LEFT OUTER JOIN [dbo].[TimeCards] AS [Extent2] ON [Extent1].[Id] = [Extent2].[EmployeeTimeCard_TimeCard_Id]
    )  AS [Project1]
    ORDER BY [Project1].[HireDate] ASC,
             [Project1].[Id] ASC, [Project1].[C1] ASC
```

すばらしいは、アクション メソッド内のコードには引き続き完全にテストします。 Include 演算子をサポートするために、fakes の追加機能を提供する必要はありません。 悪い面は依存しない永続性を維持したいコード内で Include 演算子を使用する必要があります。 これは、テスト可能なコードをビルドするときに評価する必要がありますのトレードオフの種類の典型的な例です。 パフォーマンス目標を達成するリポジトリの抽象化の外部の永続化懸念事項のリークをできるようにする必要がある場合もあります。

一括読み込みを使用しない場合は、遅延読み込みです。 遅延読み込みは*いない*ビジネスのコードに明示的に関連付けられているデータの要件をご案内が必要です。 アプリケーションで、エンティティを使用し、追加のデータに Entity Framework が必要な代わりに、オンデマンドでデータが読み込まれます。

### <a name="lazy-loading"></a>遅延読み込み

ビジネス ロジックの一部のデータが必要があります、わからないシナリオを考えてみましょうに簡単です。 ロジックには、employee オブジェクトが必要がありますが、タイム カードについては、従業員からそれらのパスのいくつか必要し、しない、さまざまな実行パスに分岐します可能性がありますがわかっています可能性があります。 このようなシナリオでは、暗黙的な遅延読み込みデータは魔法のように必要な場合に表示されるために最適です。

遅延読み込み、遅延とも呼ばれますが、エンティティ オブジェクトのいくつかの要件を配置している読み込み中はします。 True の永続化非依存で Poco の要件、永続化レイヤーから直面しないが true の永続性の無視が実現するために実質的に不可能ではありません。  代わりに相対度数で永続化非依存を測定します。 できなくなる幸運指向の永続化の基本クラスを継承または Poco の遅延読み込みを実現するために特殊なコレクションを使用する必要がある場合。 さいわい、EF4 では、さほどソリューションがあります。

### <a name="virtually-undetectable"></a>ほとんどの検出

POCO オブジェクトを使用する場合、EF4 はエンティティのランタイム プロキシを動的に生成できます。 これらのプロキシが目に見えない形具体化された Poco をラップし各プロパティをインターセプトすることでその他のサービスの取得を提供および追加の作業を実行する操作を設定します。 このような 1 つのサービスは、探している遅延読み込み機能です。 別のサービスは、効率的な変更の追跡、プログラムには、エンティティのプロパティ値が変更されたときを記録するメカニズムです。 変更の一覧は、更新コマンドを使用して、変更されたエンティティを保持する、SaveChanges メソッドの中に、ObjectContext によって使用されます。

ただし、するのには、これらのプロキシのプロパティの取得にフックする方法を必要なプロキシと、エンティティに対するセット操作では、この目標を達成仮想メンバーをオーバーライドします。 そのため、暗黙の遅延読み込みと効率的な変更追跡が必要な場合、POCO クラス定義に戻り、仮想プロパティを設定する必要があります。

``` csharp
    public class Employee {
        public virtual int Id { get; set; }
        public virtual string Name { get; set; }
        public virtual DateTime HireDate { get; set; }
        public virtual ICollection<TimeCard> TimeCards { get; set; }
    }
```

引き続き Employee エンティティが永続化をほとんど依存と言えます。 唯一の要件は、仮想メンバーを使用して、コードのテストの容易性に影響はありません。 任意の特殊な基本クラスから派生または遅延読み込みに専用の特別なコレクションを使用しても不要です。 コードは、例、ICollection を実装するクラスと&lt;T&gt;は関連エンティティを保持するために使用できます。

1 つの小さな変更が、作業単位内にする必要がありますもあります。 遅延読み込みは*オフ*既定を ObjectContext オブジェクトを直接使用する場合。 設定できる ContextOptions プロパティで有効にする遅延読み込み、プロパティがありますし、すべての場所で遅延読み込みを有効にする場合、実際の作業単位内でこのプロパティを設定することができます。

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

暗黙的な遅延読み込みが有効になっているには、従業員のでは、さいわいにも、EF に余分なデータの読み込みに必要な作業を認識しないままタイム カードが関連付けられているし、アプリケーション コードは、従業員を使用できます。

``` csharp
    var employee = _unitOfWork.Employees
                              .Single(e => e.Id == id);
    foreach (var card in employee.TimeCards) {
        // ...
    }
```

遅延読み込みは、アプリケーション コードを簡単に記述、およびプロキシ マジック コードは引き続き完全にテストします。 Unit of work のメモリ内の fakes に偽のエンティティに関連付けられているデータ、テスト中に必要なときにプリロードするだけです。

この時点で払う IObjectSet を使用してリポジトリの構築から有効にします&lt;T&gt;し、永続化フレームワークのすべての記号を非表示に抽象化を確認します。

## <a name="custom-repositories"></a>カスタム リポジトリ

この記事ではまずの unit of work 設計パターンを表示とすると、unit of work のようにいくつかのサンプル コードが用意されていますが。 従業員とで操作している従業員タイム カードのシナリオを使用してこの元の意図を再表示してみましょう。

``` csharp
    public interface IUnitOfWork {
        IRepository<Employee> Employees { get; }
        IRepository<TimeCard> TimeCards { get;  }
        void Commit();
    }
```

この作業単位と最後のセクションで作成した作業の単位の主な違いはどのように作業単位が、EF4 framework から抽象化を使用しません (IObjectSet がない&lt;T&gt;)。 IObjectSet&lt;T&gt;リポジトリ インターフェイスが公開 API としても動作は、アプリケーションのニーズを持つ完全に一致可能性があります。 今後この方法でカスタム IRepository を使用してリポジトリを表す&lt;T&gt;抽象化します。

テスト駆動型設計、動作駆動型設計、およびドメイン主導型の方法論のデザインをフォローしている多くの開発者が、IRepository を好む&lt;T&gt;アプローチをいくつかの理由。 最初は、IRepository、&lt;T&gt;インターフェイスは、「腐敗」レイヤーを表します。 ドメイン駆動設計の著書で Eric Evans が説明されていると破損対策レイヤーを永続化 API などのインフラストラクチャは、Api から、ドメイン コードを保持します。 次に、開発者は、(テストの書き込み中に検出) と、アプリケーションの正確なニーズを満たすリポジトリにメソッドを構築できます。 たとえば、リポジトリ インターフェイスを FindById メソッドを追加できるように、ID 値を使用して 1 つのエンティティを検索します必要があります頻繁に。  この IRepository&lt;T&gt;定義は、次のようになります。

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

IQueryable を使用するバックアップをドロップに注意してください。&lt;T&gt;エンティティ コレクションを公開するインターフェイス。 IQueryable&lt;T&gt; EF4 プロバイダーに送信し、プロバイダーのクエリの全体像を提供する LINQ 式ツリーを使用します。 IEnumerable を返すを 2 つ目の方法もあります&lt;T&gt;、つまり、EF4 LINQ プロバイダーでは、リポジトリ内で作成された式のみ表示されます。 グループ化、並べ替え、およびリポジトリの外部で実行するプロジェクションは、パフォーマンスが低下することができるデータベースに送信する SQL コマンドに構成されません。 その一方で、IEnumerable のみを返すリポジトリ&lt;T&gt;結果は決して驚くことで新しい SQL コマンドを使用します。 両方のアプローチが動作し、両方の方法がテスト可能なままです。

IRepository の 1 つの実装を提供する簡単な&lt;T&gt;インターフェイスのジェネリックと EF4 ObjectContext API を使用します。

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

IRepository&lt;T&gt;アプローチにより、クエリをいくつか追加の制御、クライアントがエンティティを取得するメソッドを呼び出す必要があるためです。 メソッド内で追加のチェックとアプリケーションの制約を適用する LINQ 演算子が実現します。 インターフェイスが 2 つの制約のジェネリック型パラメーターに注意してください。 最初の制約は、ObjectSet によって必要なクラスの短所汚染&lt;T&gt;、し、2 つ目の制約が IEntity – アプリケーションの作成の抽象型を実装するために、エンティティを強制します。 IEntity インターフェイス Id の読み取り可能なプロパティがエンティティを強制して、FindById メソッドでこのプロパティを使用することができます。 次のコードでは、IEntity が定義されます。

``` csharp
    public interface IEntity {
        int Id { get; }
    }
```

エンティティがこのインターフェイスを実装するために必要なためににより、IEntity に永続化非依存の小規模な違反を考慮でした。 永続化非依存では、トレードオフについて説明し、FindById 機能が、インターフェイスによって課される制約を上回る多くに注意してください。 インターフェイスには、テストの容易性に影響はありません。

ライブ IRepository をインスタンス化する&lt;T&gt; EF4 ObjectContext を必要なため、具体的な実装の作業単位がインスタンス化を管理する必要があります。

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

### <a name="using-the-custom-repository"></a>カスタム リポジトリを使用します。

IObjectSet に基づいてリポジトリを使用して、大幅に異なりますが、カスタム リポジトリを使用して&lt;T&gt;します。 LINQ 演算子を適用するプロパティに直接、代わりに最初にする必要がありますいずれかに IQueryable を取得するリポジトリのメソッドを呼び出す&lt;T&gt;参照。

``` csharp
    public ViewResult Index() {
        var model = _repository.FindAll()
                               .Include("TimeCards")
                               .OrderBy(e => e.HireDate);
        return View(model);
    }
```

変更せずに動作が以前に実装したカスタムの Include 演算子に注意してください。 リポジトリの FindById メソッドでは、1 つのエンティティを取得しようとしています。 アクションから複製されたロジックを削除します。

``` csharp
    public ViewResult Details(int id) {
        var model = _repository.FindById(id);
        return View(model);
    }
```

2 つの方法について説明しましたのテストの容易性に大きな違いはありません。 IRepository の偽の実装を提供できる&lt;T&gt; HashSet に支え具象クラスを作成することにより&lt;従業員&gt;- 最後のセクションで行ったものと同じようにします。 ただし、モック オブジェクトを使用して、モック オブジェクト フレームワーク fakes を構築する代わりを好む開発者もいます。 モックを使用して、今回の実装をテストし、次のセクションでは、モックとフェイクの間の違いについて説明することについて説明します。

### <a name="testing-with-mocks"></a>モックを使用したテスト

「テスト代替」どのような Martin Fowler の呼び出しを構築するためのさまざまな方法はあります。 (二重ムービー スタント) のような二重のテストは、テスト中に運用環境のオブジェクトに""実際のビルドするオブジェクトです。 作成したメモリ内リポジトリは、SQL Server と対話するリポジトリのテスト代替です。 単体テスト中にこれらのテスト代替を使用してコードを分離し、高速に実行されるテストを保持する方法を説明しました。

作成したテスト代替では、実際、作業の実装があります。 オブジェクトの具体的なコレクションを格納するバック グラウンドでそれぞれし、追加し、テスト中に、リポジトリの操作オブジェクトをこのコレクションから削除します。 一部の開発者、テスト代替の実際のコードと作業の実装でこの方法を構築したいです。  これらのテスト代替はいわゆる*fakes*します。 作業の実装があるが、運用環境用に十分な実際はありません。 偽のリポジトリはデータベースに実際に作成できません。 偽の SMTP サーバーでは、ネットワーク経由で電子メール メッセージを送信しない実際にします。

### <a name="mocks-versus-fakes"></a>フェイクとモック

別の種類のテストと呼ばれる倍精度浮動小数点がある、*モック*します。 Fakes には、作業の実装がありますが、モック実装のが付属しません。 モック オブジェクト フレームワークを利用して実行時にこれらのモック オブジェクトを構築し、テスト代替として使用します。 このセクションで使用する、オープン ソースのフレームワーク Moq モックを作成します。 Moq を使用して、従業員のリポジトリの二重テストを動的に作成する簡単な例を次に示します。

``` csharp
    Mock<IRepository<Employee>> mock =
        new Mock<IRepository<Employee>>();
    IRepository<Employee> repository = mock.Object;
    repository.Add(new Employee());
    var employee = repository.FindById(1);
```

Moq 要求、IRepository&lt;従業員&gt;実装および、ビルドのいずれかに動的にします。 IRepository を実装するオブジェクトを取得できます&lt;従業員&gt;のモック オブジェクトのプロパティにアクセスする&lt;T&gt;オブジェクト。 これは、コント ローラーに渡すことができますこの内部オブジェクトと、テスト代替または実際のリポジトリの場合は、わかりません。 オブジェクトのメソッドは、実際の実装を持つオブジェクトでメソッドを呼び出すことと同様呼び出すことができます。

モック リポジトリが Add メソッドを呼び出しているときに疑問する必要があります。 モック オブジェクトの内部の実装がないため、追加が何も行われません。 従業員は破棄されますのでご説明したとおり、fakes にあったように、バック グラウンドでの具体的なコレクションはありません。 FindById の戻り値について説明します。 ここで、モック オブジェクトは、既定値が返されますが、そのですだけが。 参照型 (従業員) を返すことがあるため、戻り値、null 値です。

モック思えますが、役に立ちません。ただし、について説明していないのでモックの 2 つの多くの機能があります。 まず、Moq フレームワークは、モック オブジェクトで行われたすべての呼び出しを記録します。 後半のコードには、Moq を依頼して、だれでも、Add メソッドが呼び出される場合、または FindById メソッドを呼び出したすべてのユーザーの場合できます。 この「ブラック_ボックス」記録機能を使用して、テストでは後でどのように表示されます。

2 番目の優れた機能は、Moq を使用して使用して、モック オブジェクトのプログラミング方法*期待*します。 予測は、モック オブジェクトに、特定の操作に応答する方法を指示します。 たとえば、予測をモックにプログラムをだれかが FindById を呼び出すときに、employee オブジェクトを返すことを確認できます。 Moq フレームワークは、このような期待をプログラムするのにセットアップ API とラムダ式を使用します。

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

このサンプルでは、リポジトリを動的に構築 Moq させていただき、という前提でリポジトリをプログラムします。 想定されるは、だれかが 5 の値を渡す FindById メソッドを呼び出すときに、5 の Id 値を持つ新しい employee オブジェクトを返すモック オブジェクトを指示します。 このテストが成功して、偽の IRepository に完全な実装を構築する必要はなく&lt;T&gt;します。

みましょう以前作成したテストを再確認し、fakes ではなく、モックを使用するように再作成します。 同じように、前に使用します基底クラスのすべてのコント ローラーのテストに必要なインフラストラクチャの一般的な情報を設定します。

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

このセットアップ コードはほとんど同じです。 Fakes を使用せずに Moq モック オブジェクトの構築に使用します。 基本クラスは、コードが従業員のプロパティを呼び出すと、モック リポジトリを返すモック unit of work を配置します。 モック セットアップの残りの部分は、特定のシナリオごとに専用のテスト フィクスチャ内で実行されます。 など、インデックス アクション用のテスト フィクスチャはアクションは、モック リポジトリの FindAll メソッドを呼び出すときに従業員の一覧を返すモック リポジトリをセットアップします。

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

、期待値を除くテストでは、ように、テストする前にありました。 ただし、モック フレームワークの記録機能を異なる角度からテストをアプローチことができます。 次のセクションで新しい視点に立つことについて説明します。

### <a name="state-versus-interaction-testing"></a>相互作用のテストと状態

ソフトウェアとモック オブジェクトをテストに使用できるさまざまな方法はあります。 1 つは、状態を使用するベースのテスト、これは、何がこのホワイト ペーパーでここまでの処理です。 状態ベースのソフトウェアの状態の詳細についてはアサーションをテストします。 最後のテストは、コント ローラーのアクション メソッドを呼び出すし、それを構築する必要があります、モデルに関するアサーションを作成します。 テストの状態の他のいくつかの例を次に示します。

-   リポジトリが作成の実行後に、新しい従業員オブジェクトを含むことを確認します。
-   モデルは、インデックスの実行後に、すべての従業員の一覧を保持していることを確認します。
-   削除の実行後に、リポジトリは、特定の従業員を含まないことを確認します。

モック オブジェクトのもう 1 つの方法は、確認する*の相互作用*します。 状態ベースのオブジェクトの状態に関するテストとアサーション、相互作用ベースのオブジェクトを操作する方法の詳細についてはアサーションをテストします。 例えば:

-   作成を実行するときに、コント ローラーがリポジトリの Add メソッドを呼び出すことを確認します。
-   インデックスを実行するときに、コント ローラーがリポジトリの FindAll メソッドを呼び出すことを確認します。
-   コント ローラーが編集を実行するときに、変更を保存する作業の Commit メソッドの単位を呼び出すことを確認します。

コレクション内に設定するときとカウントを確認することはためテストの低いデータを必要な相互作用をテスト多くの場合。 など、Details アクションが有効な値の指定のリポジトリの FindById メソッドを呼び出すことがわかっている場合、アクションがおそらく正しく動作します。 FindById から返されるすべてのテスト データをセットアップすることがなくこの動作を確認できます。

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

上記のテスト フィクスチャで必要な唯一のセットアップでは、基本クラスによって提供されるセットアップです。 コント ローラー アクションを呼び出すことと Moq モック リポジトリとの対話が記録されます。 確認 API の Moq を使用して、質問 Moq、コント ローラーが、適切な ID 値を持つ FindById を呼び出されます。 コント ローラーが、メソッドを呼び出しませんでした予期しないパラメーターの値を持つメソッドを呼び出した場合、検証メソッドは例外をスローし、テストは失敗します。

作成アクションは、現在の作業ユニットでコミットを呼び出すことを確認するもう 1 つの例を次に示します。

``` csharp
    [TestMethod]
    public void ShouldCommitUnitOfWork() {
        _controller.Create(_newEmployee);
        _unitOfWork.Verify(u => u.Commit());
    }
```

相互作用のテストに 1 つの危険性は、上の相互作用を指定する傾向です。 記録して、テストといって、モック オブジェクトに対するすべての操作を確認するには、モック オブジェクトの機能は、すべての操作を確認しますしようとする必要があります。 いくつかの通信には、実装の詳細との相互作用を確認する必要がありますのみ*必要*を現在のテストを満たすためにします。

モックやフェイクの間で choice がシステムをテストして、個人に大きく左右 (またはチーム) 設定します。 モック オブジェクトは、テストの代替を実装する必要があるコードの量を大幅に短縮できますが、誰もが快適なは、期待のプログラミングとの相互作用を確認しています。

## <a name="conclusions"></a>まとめ

このホワイト ペーパーでは、データの永続性、ADO.NET Entity Framework を使用中にテストしやすいコードを作成するいくつかの方法を説明してきました。 IObjectSet などの組み込みの抽象化を利用できます&lt;T&gt;、IRepository のように、独自の抽象化を作成または&lt;T&gt;します。  どちらの場合で、ADO.NET Entity Framework 4.0 で POCO サポートでは、依存しやすいに永続化するこれらの抽象化のコンシューマーができます。 暗黙的な遅延読み込みにより、ビジネスおよびアプリケーションのサービスと同様に、追加の EF4 機能は、リレーショナル データ ストアの詳細について心配することがなく動作するコードです。 最後に、作成抽象化は簡単にモックやフェイク単体テストでは、内部でと、これらの高度に分離された高速の実行を実現するために、テスト代替と信頼性の高いテストを使用します。

### <a name="additional-resources"></a>その他のリソース

-   Robert C. Martin、"[単一責任の原則](http://www.objectmentor.com/resources/articles/srp.pdf)"
-   Martin Fowler、[パターンのカタログ](http://www.martinfowler.com/eaaCatalog/index.html)から*エンタープライズ アプリケーション アーキテクチャのパターン*
-   Griffin の Caprio"[依存関係の注入](https://msdn.microsoft.com/magazine/cc163739.aspx)"
-   データ プログラマビリティ ブログでは、"[チュートリアル: Entity Framework 4.0 によるテスト駆動開発](http://blogs.msdn.com/adonet/pages/walkthrough-test-driven-development-with-the-entity-framework-4-0.aspx)"。
-   データ プログラマビリティ ブログでは、" [with Entity Framework 4.0 を使用してリポジトリと Unit of Work パターン](http://blogs.msdn.com/adonet/archive/2009/06/16/using-repository-and-unit-of-work-patterns-with-entity-framework-4-0.aspx)"
-   Dave Astels、" [BDD 入門](http://blog.daveastels.com/files/BDD_Intro.pdf)"
-   Aaron Jensen、"[マシンの仕様を導入](http://codebetter.com/blogs/aaron.jensen/archive/2008/05/08/introducing-machine-specifications-or-mspec-for-short.aspx)"
-   Eric Lee、" [MSTest を使用した BDD](http://blogs.msdn.com/elee/archive/2009/01/20/bdd-with-mstest.aspx)"
-   Eric evans 著、" [Domain Driven Design](http://books.google.com/books?id=7dlaMs0SECsC&printsec=frontcover&dq=evans%20domain%20driven%20design&hl=en&ei=cHztS6C8KIaglAfA_dS1CA&sa=X&oi=book_result&ct=result&resnum=1&ved=0CCoQ6AEwAA)"
-   Martin fowler 氏は、"[モックとスタブ](http://martinfowler.com/articles/mocksArentStubs.html)"
-   Martin fowler 氏は、"[テスト ダブル](http://martinfowler.com/bliki/TestDouble.html)"
-   Jeremy Miller"[状態相互作用のテストと](http://codebetter.com/blogs/jeremy.miller/articles/129544.aspx)"
-   [Moq](http://code.google.com/p/moq/)

### <a name="biography"></a>略歴

Scott Allen は、Pluralsight の技術スタッフのメンバーと OdeToCode.com の創設者です。 商用ソフトウェア開発の 15 年間は、ASP.NET web アプリケーションを非常にスケーラブルな 8 ビットの組み込みデバイスからすべてのソリューションで Scott、努めてきました。 Scott は、OdeToCode、彼のブログや Twitter は、上に到達できる[ http://twitter.com/OdeToCode](http://twitter.com/OdeToCode)します。
