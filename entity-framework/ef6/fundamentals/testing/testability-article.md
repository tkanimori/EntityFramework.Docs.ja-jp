---
title: テストの容易性とエンティティ フレームワーク 4.0 - EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 9430e2ab-261c-4e8e-8545-2ebc52d7a247
ms.openlocfilehash: 96b6b2791f12b7d60a233f7e6dc77e5a8579fb66
ms.sourcegitcommit: 144edccf9b29a7ffad119c235ac9808ec1a46193
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "81434314"
---
# <a name="testability-and-entity-framework-40"></a>テストの容易性とエンティティ フレームワーク 4.0
スコット・アレン

公開: 2010 年 5 月

## <a name="introduction"></a>はじめに

このホワイト ペーパーでは、エンティティ フレームワーク 4.0 と Visual Studio 2010 ADO.NETを使用してテスト可能なコードを記述する方法について説明し、デモンストレーションします。 このホワイト ペーパーでは、テスト駆動型設計 (TDD) や動作駆動型設計 (BDD) など、特定のテスト方法論に焦点を当てていません。 代わりに、このペーパーでは、ADO.NET Entity Framework を使用するコードを記述する方法に焦点を当てますが、分離して自動化された方法でテストするのは簡単です。 データ アクセス シナリオでのテストを容易にする一般的なデザイン パターンを見て、フレームワークを使用する際にそれらのパターンを適用する方法を確認します。 また、フレームワークの特定の機能を見て、これらの機能がテスト可能なコードでどのように機能するかを確認します。

## <a name="what-is-testable-code"></a>テスト可能なコードとは

自動単体テストを使用してソフトウェアを検証する機能には、多くの望ましい利点があります。 優れたテストは、アプリケーションのソフトウェア欠陥の数を減らし、アプリケーションの品質を向上させることは誰もが知っていますが、単体テストを実施するだけではバグを見つけるだけではありません。

優れた単体テスト スイートを使用すると、開発チームは時間を節約し、作成するソフトウェアを制御できます。 チームは、既存のコードの変更、リファクタリング、再設計、およびソフトウェアの再構築を行って新しい要件を満たし、新しいコンポーネントをアプリケーションに追加し、テストスイートがアプリケーションの動作を検証できることを知ることができます。 単体テストは、変更を促進し、複雑さが増すに応じてソフトウェアの保守性を維持するための、迅速なフィードバック サイクルの一部です。

ただし、単体テストには価格が付属しています。 チームは、単体テストを作成および管理するために時間を投資する必要があります。 これらのテストを作成するために必要な労力は、基礎となるソフトウェアの**テスト可能性**に直接関係しています。 ソフトウェアのテストはどの程度簡単ですか? テストの容易性を考慮してソフトウェアを設計するチームは、テスト不可能なソフトウェアを使用するチームよりも迅速に効果的なテストを作成します。

マイクロソフトでは、テストの可能性を考慮して、ADO.NET エンティティ フレームワーク 4.0 (EF4) を設計しました。 これは、開発者がフレームワーク コード自体に対して単体テストを記述することを意味するものではありません。 代わりに、EF4 のテスト可能性の目標を使用すると、フレームワークの上に構築されたテスト可能なコードを簡単に作成できます。 具体的な例を見る前に、テスト可能なコードの品質を理解する価値があります。

### <a name="the-qualities-of-testable-code"></a>テスト可能なコードの品質

テストが簡単なコードでは、少なくとも 2 つの特性が表示されます。 まず、テスト可能なコードは**簡単に観察できます**。 いくつかの入力セットを考えると、コードの出力を簡単に観察できるはずです。 たとえば、次のメソッドのテストは、メソッドが計算の結果を直接返すため簡単です。

``` csharp
    public int Add(int x, int y) {
        return x + y;
    }
```

メソッドが計算された値をネットワーク ソケット、データベース テーブル、または次のコードのようなファイルに書き込む場合、メソッドのテストは困難です。 テストでは、値を取得するために追加の作業を実行する必要があります。

``` csharp
    public void AddAndSaveToFile(int x, int y) {
         var results = string.Format("The answer is {0}", x + y);
         File.WriteAllText("results.txt", results);
    }
```

第二に、テスト可能なコードは**分離**しやすいです。 テスト可能なコードの不適切な例として、次の擬似コードを使用してみましょう。

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

この方法は観察が容易で、保険契約を通過し、戻り値が期待される結果と一致することを確認できます。 ただし、メソッドをテストするには、正しいスキーマを使用してデータベースをインストールし、メソッドが電子メールを送信しようとする場合に備えて SMTP サーバーを構成する必要があります。

単体テストでは、メソッド内の計算ロジックを検証する必要がありますが、電子メール サーバーがオフラインであるか、データベース サーバーが移動したためにテストが失敗する可能性があります。 これらのエラーは、テストで検証する動作とは無関係です。 動作を特定するのは困難です。

テスト可能なコードを記述しようとするソフトウェア開発者は、多くの場合、記述するコード内の懸念事項の分離を維持しようと努力します。 上記の方法は、ビジネスの計算に焦点を当て、データベースと電子メールの実装の詳細を他のコンポーネントに委任する必要があります。 ロバート・C・マーティンはこれを単一責任原則と呼んでいます。 オブジェクトは、ポリシーの値の計算のように、単一の狭い責任をカプセル化する必要があります。 その他のデータベースと通知作業はすべて、他のオブジェクトの責任である必要があります。 この方法で記述されたコードは、単一のタスクに焦点を当てているため、分離が容易です。

NET には、単一責任の原則に従い、分離を実現するために必要な抽象化があります。 インターフェイス定義を使用して、具体的な型の代わりにインターフェイスの抽象化を使用するようにコードを強制できます。 このホワイト ペーパーの後半では、上記の例のような方法が、データベースと話すように*見える*インターフェイスで動作する方法について説明します。 ただし、テスト時には、データベースとの間でやり取りせず、メモリ内にデータを保持するダミー実装を置き換えることができます。 このダミー実装では、データ アクセス コードまたはデータベース構成の関連のない問題からコードを分離します。

分離には、さらに利点があります。 最後の方法でのビジネス計算は、実行に数ミリ秒しかかかりませんが、コードがネットワークを飛び回り、さまざまなサーバーと通信する場合、テスト自体が数秒間実行される可能性があります。 単体テストは、小規模な変更を容易にするために高速に実行する必要があります。 単体テストも繰り返し可能で、テストとは無関係のコンポーネントに問題があるため、失敗しないようにする必要があります。 コードを簡単に観察して分離するコードを記述すると、開発者はコードのテストを簡単に記述でき、テストの実行を待つ時間が短くなり、さらに重要なことに、存在しないバグの追跡に費やす時間が減ります。

テストの利点を理解し、テスト可能なコードが示す品質を理解できることを願っています。 EF4 と連携してデータをデータベースに保存するコードの記述方法を取り上げるのですが、まずはデータ アクセスのテスト可能な設計について議論することに焦点を絞ります。

## <a name="design-patterns-for-data-persistence"></a>データ永続性のためのデザイン パターン

以前に提示された悪い例の両方があまりにも多くの責任を持っていました。 最初の悪い例では、計算を実行*し、* ファイルに書き込む必要がありました。 2 番目の悪い例では、データベースからデータを読み取*り、* ビジネス計算を実行*し、* 電子メールを送信する必要がありました。 懸念を分離し、他のコンポーネントに責任を委任する小さなメソッドを設計することで、テスト可能なコードの作成に大きく前進します。 目標は、小規模で集中的な抽象化からアクションを作成することによって機能を構築することです。

データの永続化に関しては、私たちが探している小さくて焦点を絞った抽象化は、設計パターンとして文書化されているために一般的です。 マーティン・ファウラーの著書『エンタープライズ・アプリケーション・アーキテクチャーのパターン』は、これらのパターンを印刷物で記述した最初の作品でした。 これらのパターンの簡単な説明を次のセクションで説明してから、これらのADO.NET Entity Framework がこれらのパターンを実装して動作する方法を示します。

### <a name="the-repository-pattern"></a>リポジトリ パターン

ファウラー氏は、リポジトリは「ドメインオブジェクトにアクセスするためのコレクションのようなインターフェイスを使用して、ドメインとデータマッピング層の間を仲介する」と述べています。 リポジトリ パターンの目的は、コードをデータ アクセスの minutiae から分離することであり、以前に見た分離はテスト容易性に必要な特性です。

分離の鍵は、リポジトリがコレクションのようなインターフェイスを使用してオブジェクトを公開する方法です。 リポジトリを使用するために記述するロジックには、リポジトリが要求したオブジェクトをどのように具体化するのかわかりません。 リポジトリはデータベースとの間でやり取りする場合も、メモリ内コレクションからオブジェクトを返すだけの場合もあります。 コードで知っておくべきことは、リポジトリがコレクションを保持するように見え、コレクションからオブジェクトを取得、追加、および削除できることです。

既存の .NET アプリケーションでは、具象リポジトリは、次のような汎用インターフェイスから継承することがよくあります。

``` csharp
    public interface IRepository<T> {       
        IEnumerable<T> FindAll();
        IEnumerable<T> FindBy(Expression<Func\<T, bool>> predicate);
        T FindById(int id);
        void Add(T newEntity);
        void Remove(T entity);
    }
```

EF4 の実装を提供する場合は、インターフェイス定義にいくつか変更を加えますが、基本的な概念は変わりません。 コードでは、このインターフェイスを実装する具象リポジトリを使用して、主キー値でエンティティを取得したり、述語の評価に基づいてエンティティのコレクションを取得したり、使用可能なすべてのエンティティを単純に取得したりできます。 このコードでは、リポジトリ インターフェイスを使用してエンティティを追加および削除することもできます。

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

コードはインターフェイス (従業員の IRepository) を使用しているため、インターフェイスのさまざまな実装をコードに提供できます。 1 つの実装は、EF4 によってバックアップされ、オブジェクトを Microsoft SQL Server データベースに永続化する実装である場合があります。 別の実装 (テスト中に使用する実装) は、インメモリの Employee オブジェクトのリストによってバックアップされる可能性があります。 インターフェイスは、コードの分離を実現するのに役立ちます。

IRepository&lt;T&gt;インターフェイスは、保存操作を公開しないことに注意してください。 既存のオブジェクトを更新する方法 保存操作を含む IRepository 定義に遭遇する可能性があり、これらのリポジトリの実装では、オブジェクトをデータベースに直ちに永続化する必要があります。 しかし、多くのアプリケーションでは、オブジェクトを個別に保持したくありません。 代わりに、オブジェクトを、おそらく異なるリポジトリから実現し、ビジネス アクティビティの一部としてそれらのオブジェクトを変更し、すべてのオブジェクトを単一のアトミック操作の一部として永続化します。 幸いにも、この種の動作を許可するパターンがあります。

### <a name="the-unit-of-work-pattern"></a>作業単位パターン

ファウラー氏は、作業単位は「ビジネス・トランザクションの影響を受けるオブジェクトのリストを維持し、変更の書き出しと同時実行の問題の解決を調整する」と述べています。 リポジトリから作成したオブジェクトの変更を追跡し、変更をコミットするように指示したときにオブジェクトに加えた変更を保持するのは作業単位の責任です。 また、すべてのリポジトリに追加した新しいオブジェクトをデータベースに挿入し、削除を管理することも、作業単位の責任です。

ADO.NET DataSet で作業を行ったことがあれば、作業単位パターンに慣れているはずです。 ADO.NET DataSet は、DataRow オブジェクトの更新、削除、挿入を追跡する機能を備えており、TableAdapter の助けを借りてデータベースに対するすべての変更を調整することができました。 ただし、DataSet オブジェクトは、基になるデータベースの非接続されたサブセットをモデル化します。 作業単位パターンは同じ動作を示しますが、データ アクセス コードから分離され、データベースを認識していないビジネス オブジェクトおよびドメイン オブジェクトで動作します。

.NET コードで作業単位をモデル化する抽象化は、次のようになります。

``` csharp
    public interface IUnitOfWork {
        IRepository<Employee> Employees { get; }
        IRepository<Order> Orders { get; }
        IRepository<Customer> Customers { get; }
        void Commit();
    }
```

作業単位からリポジトリ参照を公開することにより、単一の作業単位オブジェクトが、業務トランザクション中に実体化されたすべてのエンティティを追跡する機能を持つことを保証できます。 実際の作業単位の Commit メソッドの実装は、メモリ内の変更をデータベースと調整するためにすべてのマジックが発生する場所です。 

IUnitOfWork 参照を指定すると、コードは 1 つ以上のリポジトリから取得したビジネス オブジェクトを変更し、アトミック コミット操作を使用してすべての変更を保存できます。

``` csharp
    var firstEmployee = unitofWork.Employees.FindById(1);
    var firstCustomer = unitofWork.Customers.FindById(1);
    firstEmployee.Name = "Alex";
    firstCustomer.Name = "Christopher";
    unitofWork.Commit();
```

### <a name="the-lazy-load-pattern"></a>レイジー ロード パターン

ファウラーは、名前の遅延ロードを使用して、「必要なデータをすべて含むが、それを取得する方法を知っているオブジェクト」を記述します。 透過的な遅延読み込みは、テスト可能なビジネス コードを記述し、リレーショナル データベースを操作するときに重要な機能です。 例として、次のコードを考えてみましょう。

``` csharp
    var employee = repository.FindById(id);
    // ... and later ...
    foreach(var timeCard in employee.TimeCards) {
        // .. manipulate the timeCard
    }
```

タイムカード コレクションはどのように設定されますか? 2つの答えがあります。 1 つの回答は、従業員リポジトリが、従業員を取得するように求められた場合、従業員の関連付けられたタイム カード情報と共に、両方の従業員を取得するクエリを発行することです。 リレーショナル データベースでは、通常、このクエリに JOIN 句が必要であり、アプリケーションのニーズよりも多くの情報を取得する可能性があります。 アプリケーションが TimeCards プロパティに触れる必要がな場合はどうでしょうか?

2 つ目の答えは、TimeCards プロパティを "オンデマンド" で読み込むことです。 この遅延読み込みは、タイム カード情報を取得するための特別な API を呼び出さないため、ビジネス ロジックに対して暗黙的かつ透過的です。 このコードでは、必要に応じてタイムカード情報が存在することを前提としています。 一般的にメソッド呼び出しの実行時のインターセプトを伴う遅延読み込みに関するいくつかの魔法があります。 インターセプト コードは、ビジネス ロジックをビジネス ロジックに自由に残しながら、データベースとの通信とタイム カード情報の取得を担当します。 この遅延ロードマジックにより、ビジネスコードはデータ取得操作から分離され、よりテスト可能なコードが得られます。

遅延読み込みの欠点は *、アプリケーションが*タイム カード情報を必要とする場合に、コードが追加のクエリを実行することです。 これは多くのアプリケーションにとって問題ではありませんが、パフォーマンスに敏感なアプリケーションやアプリケーションが多数の従業員オブジェクトをループし、ループの各反復 (N+1 クエリの問題と呼ばれる問題) のたびにタイム カードを取得するクエリを実行する場合、遅延読み込みはドラッグです。 このようなシナリオでは、アプリケーションは、可能な限り最も効率的な方法でタイム カード情報を熱心に読み込みたい場合があります。

さいわい、次のセクションに進んでこれらのパターンを実装する場合、EF4 が暗黙の遅延読み込みと効率的な一括読み込みの両方をサポートする方法を見ていきます。

## <a name="implementing-patterns-with-the-entity-framework"></a>エンティティ フレームワークを使用したパターンの実装

最後のセクションで説明したすべての設計パターンは、EF4 で簡単に実装できるということです。 デモンストレーションを行うために、シンプルなASP.NET MVC アプリケーションを使用して、Employees と関連付けられたタイム カード情報を編集および表示します。 まず、次の "プレーンな古い CLR オブジェクト" (POKO) を使用します。 

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

これらのクラス定義は、EF4 のさまざまなアプローチや機能を検討するに伴って若干変更されますが、その目的は、これらのクラスを可能な限り永続化無知 (PI) として維持することです。 PI オブジェクトは、保持している状態がデータベース内に格納されている場合でも、*どのように*、または、データベース内に配置されている*のかを*知りません。 PI と POCO は、テスト可能なソフトウェアと共に手をつないで行きます。 POCO アプローチを使用するオブジェクトは、データベースがなくても動作できるため、制約が少なく、柔軟性が高く、テストが容易です。

POC を配置すると、Visual Studio でエンティティ データ モデル (EDM) を作成できます (図 1 参照)。 EDM を使用してエンティティのコードを生成しません。 代わりに、私たちは愛情を込めて手作りするエンティティを使用したいと考えています。 EDM を使用してデータベース スキーマを生成し、EF4 がオブジェクトをデータベースにマップするために必要なメタデータを提供します。

![ef test_01](~/ef6/media/eftest-01.jpg)

**図 1**

注: 最初に EDM モデルを開発する場合は、EDM からクリーンな POCO コードを生成することができます。 これは、データ プログラミング チームが提供する Visual Studio 2010 拡張機能を使用して行うことができます。 拡張機能をダウンロードするには、Visual Studio の [ツール] メニューから拡張機能マネージャーを起動し、テンプレートのオンライン ギャラリーで "POCO" を検索します (図 2 参照)。 EF にはいくつかの POCO テンプレートがあります。 テンプレートの使用方法の詳細については、「[チュートリアル: エンティティ フレームワークの POCO テンプレート](https://docs.microsoft.com/archive/blogs/adonet/walkthrough-poco-template-for-the-entity-framework)」を参照してください。

![ef test_02](~/ef6/media/eftest-02.png)

**図 2**

この POCO の開始点から、テスト可能なコードに対する 2 つの異なるアプローチを探ります。 最初のアプローチでは、作業単位とリポジトリを実装するために Entity Framework API からの抽象化を活用するため、EF アプローチを呼び出します。 2 番目のアプローチでは、独自のカスタム リポジトリの抽象化を作成し、各アプローチの長所と短所を確認します。 まず、EF アプローチを見ていきます。  

### <a name="an-ef-centric-implementation"></a>EF セントリック実装

mvc プロジェクトのASP.NETから次のコントローラー アクションを検討してください。 このアクションは、Employee オブジェクトを取得し、結果を返して従業員の詳細ビューを表示します。

``` csharp
    public ViewResult Details(int id) {
        var employee = _unitOfWork.Employees
                                  .Single(e => e.Id == id);
        return View(employee);
    }
```

コードはテスト可能ですか? アクションの動作を検証するには、少なくとも 2 つのテストがあります。 まず、アクションが正しいビューを返すことを確認します 。 また、アクションが正しい従業員を取得することを確認するテストを記述し、データベースを照会するコードを実行せずにこれを行いたいと思います。 テスト対象のコードを分離する必要があることに注意してください。 分離により、データ アクセス コードまたはデータベース構成のバグが原因でテストが失敗しないようにします。 テストが失敗した場合、コントローラロジックにバグがあり、下位レベルのシステムコンポーネントにはバグがないことがわかります。

分離を実現するには、リポジトリや作業単位に関して先ほど説明したインターフェイスのような抽象化が必要になります。 リポジトリ パターンは、ドメイン オブジェクトとデータ マッピング レイヤの間を仲介するように設計されています。 このシナリオでは、EF4*は*データ マッピング レイヤーであり、既に (System.Data.Objects&lt;&gt;名前空間から) IObjectSet T という名前のリポジトリのような抽象化を提供しています。 インターフェイス定義は次のようになります。

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

IObjectSet&lt;&gt; T は、オブジェクトのコレクションに似ており (IEnumerable&lt;T&gt;を使用)、シミュレートされたコレクションからオブジェクトを追加および削除するメソッドを提供するため、リポジトリの要件を満たしています。 アタッチ と デタッチ メソッドは、EF4 API の追加機能を公開します。 IObjectSet&lt;T&gt;をリポジトリのインターフェイスとして使用するには、リポジトリをバインドするための作業抽象化単位が必要です。

``` csharp
    public interface IUnitOfWork {
        IObjectSet<Employee> Employees { get; }
        IObjectSet<TimeCard> TimeCards { get; }
        void Commit();
    }
```

このインターフェイスの具体的な実装の 1 つは SQL Server と話し合い、EF4 から ObjectContext クラスを使用して簡単に作成できます。 ObjectContext クラスは、EF4 API の実際の作業単位です。

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

オブジェクトを&lt;作成するのと&gt;同じくらい簡単に、オブジェクトのオブジェクトセットのメソッドを呼び出します。 舞台裏では、フレームワークは EDM で提供されたメタデータを使用して、具体的な ObjectSet&lt;&gt;T を生成します。 クライアント コードでのテストの容易性を&lt;維持&gt;するために IObjectSet T インターフェイスを返すことに固執します。

この具体的な実装は実稼働環境で役立ちますが、テストを容易にするために IUnitOfWork の抽象化を使用する方法に焦点を当てる必要があります。

### <a name="the-test-doubles"></a>テストダブルス

コントローラーアクションを分離するには、実際の作業単位 (ObjectContext によってバックアップ) とテストの二重または「偽の」作業単位 (インメモリ操作の実行) を切り替える機能が必要になります。 この種類の切り替えを実行する一般的な方法は、MVC コントローラーが作業単位をインスタンス化するのではなく、作業単位をコンストラクター パラメーターとしてコントローラーに渡すことです。

``` csharp
    class EmployeeController : Controller {
      publicEmployeeController(IUnitOfWork unitOfWork)  {
          _unitOfWork = unitOfWork;
      }
      ...
    }
```

上記のコードは、依存関係の挿入の例です。 コントローラーが依存関係 (作業単位) を作成することは許可しませんが、その依存関係をコントローラーに挿入します。 MVC プロジェクトでは、カスタム コント ローラー ファクトリを、依存関係の挿入を自動化する制御 (IoC) コンテナーの反転と組み合わせて使用する一般的です。 これらのトピックは、この記事の範囲外ですが、この記事の最後に記載されている参考資料を参照して、詳細を参照できます。

テストに使用できる偽の作業単位の実装は、次のようになります。

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

偽の作業単位がコミットされたプロパティを公開していることに注目してください。 テストを容易にする偽のクラスに機能を追加すると便利な場合があります。 この場合、Commited プロパティをチェックすることによって、コードが作業単位をコミットしているかどうかは簡単に確認できます。

また、従業員オブジェクトとタイムカードオブジェクトをメモリ&lt;に&gt;保持するには、偽のIObjectSet Tが必要になります。 ジェネリックを使用して単一の実装を提供できます。

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

このテストは、その作業のほとんどを基になる HashSet&lt;T&gt;オブジェクトに委任します。 IObjectSet&lt;T&gt;には T をクラス (参照型) として適用するジェネリック制約が必要であり、また&lt;IQueryable T&gt;を実装するように強制されることに注意してください。 標準 LINQ 演算子 AsQueryable を使用して、メモリ内&lt;コレクション&gt;を IQueryable T として表示するのは簡単です。

### <a name="the-tests"></a>テスト

従来の単体テストでは、1 つのテスト クラスを使用して、すべてのアクションのすべてのテストを 1 つの MVC コントローラーに保持します。 これらのテスト、またはあらゆる種類の単体テストを、私たちが構築したメモリ内の偽物を使用して書くことができます。 ただし、この記事では、モノリシック テスト クラスアプローチを避け、代わりに特定の機能に焦点を当てるためにテストをグループ化します。たとえば、"新しい従業員の作成" はテスト対象の機能である場合があるため、1 つのテスト クラスを使用して、新しい従業員の作成を担当する単一のコントローラー アクションを検証します。

これらの細かいテストクラスすべてに必要な一般的なセットアップコードがいくつかあります。 たとえば、常にインメモリリポジトリと偽の作業単位を作成する必要があります。 また、偽の作業単位が挿入された従業員コントローラのインスタンスも必要です。 この共通セットアップ コードは、基本クラスを使用してテスト クラス間で共有します。

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

基本クラスで使用する "オブジェクトマザー" は、テストデータを作成するための一般的なパターンの 1 つです。 オブジェクトの母には、複数のテスト器具で使用するためにテストエンティティをインスタンス化するファクトリメソッドが含まれています。

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

いくつかのテスト備品の基本クラスとして EmployeeControllerTestBase を使用できます (図 3 を参照)。 各テストフィクスチャは、特定のコントローラーアクションをテストします。 たとえば、あるテストフィクスチャは、HTTP GET 要求 (従業員を作成するためのビューを表示する) で使用される Create アクションのテストに焦点を当て、別の備品は HTTP POST 要求で使用される Create アクション (ユーザーが送信した情報を取得して従業員を作成する) に焦点を当てます。 各派生クラスは、特定のコンテキストで必要なセットアップと、その特定のテスト コンテキストの結果を検証するために必要なアサーションを提供する場合にのみ責任を負います。

![ef test_03](~/ef6/media/eftest-03.png)

**図 3**

ここで示す命名規則とテストスタイルは、テスト可能なコードには必要ではなく、1 つのアプローチにすぎません。 図 4 は、Visual Studio 2010 のジェット ブレイン Resharper テスト ランナー プラグインで実行されているテストを示しています。

![ef test_04](~/ef6/media/eftest-04.png)

**図 4**

共有セットアップ コードを処理する基本クラスを使用すると、各コントローラー アクションの単体テストは小さく、書き込みが簡単です。 テストは(メモリ内操作を実行しているので)迅速に実行され、無関係なインフラストラクチャまたは環境上の懸念(テスト中のユニットを分離したため)のために失敗すべきではありません。

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

これらのテストでは、基本クラスは、セットアップ作業のほとんどを行います。 基本クラスのコンストラクターは、メモリ内リポジトリ、フェイク作業単位、および EmployeeController クラスのインスタンスを作成します。 テスト クラスはこの基本クラスから派生し、Create メソッドのテストの詳細に焦点を当てています。 この場合、詳細は、単体テストの手順で示される「配置、行動、およびアサート」の手順に煮詰まります。

-   受信データをシミュレートする newEmployee オブジェクトを作成します。
-   従業員のコント ローラーの作成アクションを呼び出し、newEmployee を渡します。
-   [作成] アクションによって期待される結果が生成されていることを確認します (従業員はリポジトリに表示されます)。

私たちが構築したものは、EmployeeControllerアクションのいずれかをテストすることができます。 たとえば、Employee コントローラのインデックス アクションのテストを記述する場合、テストベース クラスから継承して、テストに対して同じ基本設定を確立できます。 再び、基本クラスは、メモリ内リポジトリ、偽の作業単位、および EmployeeController のインスタンスを作成します。 インデックス アクションのテストは、インデックス アクションの呼び出しと、アクションが返すモデルの品質のテストだけに集中する必要があります。

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

メモリ内の偽物で作成しているテストは、ソフトウェアの*状態*をテストする方向に向けられている. たとえば、Create アクションをテストする場合、作成アクションの実行後にリポジトリの状態を検査する必要があります 。

``` csharp
    [TestMethod]
    public void ShouldAddNewEmployeeToRepository() {
        _controller.Create(_newEmployee);
        Assert.IsTrue(_repository.Contains(_newEmployee));
    }
```

後で、相互作用ベースのテストについて説明します。 相互作用ベースのテストでは、テスト対象のコードがオブジェクトの適切なメソッドを呼び出し、正しいパラメータを渡したかどうかを尋ねます。 ここでは、別のデザイン パターンであるレイジー ロードをカバーに移動します。

## <a name="eager-loading-and-lazy-loading"></a>一括読み込みと遅延読み込み

ASP.NET MVC Web アプリケーションのある時点で、従業員の情報を表示し、従業員の関連付けられたタイム カードを含めることができます。 たとえば、従業員の名前とシステム内のタイム カードの合計数を示すタイム カードの概要表示があります。 この機能を実装するには、いくつかの方法があります。

### <a name="projection"></a>射影

サマリーを作成する簡単な方法の 1 つは、ビューに表示する情報専用のモデルを構築することです。 このシナリオでは、モデルは次のようになります。

``` csharp
    public class EmployeeSummaryViewModel {
        public string Name { get; set; }
        public int TotalTimeCards { get; set; }
    }
```

EmployeeSummaryViewModel はエンティティではなく、データベースに保持する必要があるものではありません。 厳密に型指定された方法でビューにデータをシャッフルするには、このクラスを使用します。 ビュー モデルには動作 (メソッドなし) が含まれるので、ビュー モデルはデータ転送オブジェクト (DTO) と似ています。 プロパティは、移動する必要があるデータを保持します。 LINQ の標準投影演算子である Select 演算子を使用して、このビュー モデルを簡単にインスタンス化できます。

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

上記のコードには2つの注目すべき機能があります。 まず、 – コードは、観察と分離が容易であるため、テストが容易です。 Select演算子は、実際の作業単位に対して行うのと同様に、メモリ内の偽物に対しても同様に機能します。

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

2 つ目の注目すべき機能は、EF4 が従業員とタイム カードの情報を組み立てる効率的な単一のクエリを生成する方法です。 特別な API を使用せずに、従業員情報とタイム カード情報を同じオブジェクトに読み込んでいます。 このコードは、単に、メモリ内のデータ ソースとリモート データ ソースに対して動作する標準の LINQ 演算子を使用して必要な情報を表現しただけです。 EF4 は、LINQ クエリと C\#コンパイラによって生成された式ツリーを、単一の効率的な T-SQL クエリに変換できました。

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

ビュー モデルや DTO オブジェクトを操作するのではなく、実際のエンティティで作業する必要がある場合もあります。 従業員*と*従業員のタイムカードが必要だとわかったら、関連データを控えめで効率的な方法で熱心に読み込むことができます。

### <a name="explicit-eager-loading"></a>明示的な一括読み込み

関連エンティティ情報を熱心に読み込みたい場合は、ビジネス ロジック (またはこのシナリオではコントローラ アクション ロジック) がリポジトリに対する要望を表現するためのメカニズムが必要です。 EF4 ObjectQuery&lt;&gt; T クラスは、クエリ中に取得する関連オブジェクトを指定する Include メソッドを定義します。 &lt;EF4 ObjectContext は、ObjectQuery&gt; &lt;T&gt;から継承する具体的なオブジェクト セット T クラスを介してエンティティを公開します。コントローラーアクションで ObjectSet&lt;&gt; T 参照を使用している場合は、次のコードを記述して、各従業員にタイム カード情報の一括読み込みを指定できます。

``` csharp
    _employees.Include("TimeCards")
              .Where(e => e.HireDate.Year > 2009);
```

しかし、コードをテスト可能にするため、実際の作業単位の外部から&lt;ObjectSet&gt; T を公開することはしません。 代わりに、偽装しやすい IObjectSet&lt;&gt; T インターフェイスに依存していますが、IObjectSet&lt;T&gt;は Include メソッドを定義しません。 LINQ の長所は、独自の Include 演算子を作成できることです。

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

この&lt;Include 演算子は、IObjectSet&gt; &lt;T&gt;ではなく IQueryable T の拡張メソッドとして定義されています。 これにより、IQueryable&lt;T 、IObjectSet T、&gt;オブジェクトクエリ&lt;&gt;&lt;T 、およびオブジェクトセット&gt;&lt;T&gt;など、より幅広い型でメソッドを使用できます。 基礎となるシーケンスが本物の EF4 ObjectQuery&lt;T&gt;ではない場合、害はなく、Include 演算子は no-op です。 基になるシーケンス*が*ObjectQuery&lt;T&gt; (または ObjectQuery&lt;&gt;T から派生) の場合、EF4 は追加データの要件を確認し、適切な SQL クエリを作成します。

この新しいオペレータを配置すると、リポジトリからタイムカード情報の一括読み込みを明示的に要求できます。

``` csharp
    public ViewResult Index() {
        var model = _unitOfWork.Employees
                               .Include("TimeCards")
                               .OrderBy(e => e.HireDate);
        return View(model);
    }
```

実際の ObjectContext に対して実行すると、コードは次の単一のクエリを生成します。 このクエリは、データベースから十分な情報を収集して、従業員オブジェクトを具体化し、TimeCards プロパティを完全に設定します。

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

優れたニュースは、アクション メソッド内のコードが完全にテスト可能なままです。 私たちは、Include演算子をサポートするために私たちの偽物のための追加機能を提供する必要はありません。 悪いニュースは、永続化を無知に保ちたいというコードの中で Include 演算子を使用しなければならなかったことです。 これは、テスト可能なコードを構築するときに評価する必要があるトレードオフのタイプの典型的な例です。 パフォーマンスの目標を達成するために、リポジトリの抽象化の外部に永続性の懸念を漏らす必要がある場合があります。

一括読み込みの代わりに、遅延読み込みがあります。 遅延読み込みは、関連データの要件を明示的に通知するためにビジネス コードを必要*としないことを*意味します。 代わりに、アプリケーションでエンティティを使用し、追加のデータが必要な場合は、必要に応じて、Entity Framework がデータを読み込みます。

### <a name="lazy-loading"></a>遅延読み込み

ビジネス ロジックに必要なデータがわからないシナリオは、簡単に想像できます。 ロジックには従業員オブジェクトが必要な場合もありますが、これらのパスの一部が従業員からのタイム カード情報を必要とし、一部が必要でない場合は、異なる実行パスに分岐する場合があります。 このようなシナリオは、データが必要に応じて魔法のように表示されるため、暗黙的な遅延読み込みに最適です。

遅延読み込み (遅延読み込みとも呼ばれます) は、エンティティ オブジェクトにいくつかの要件を設定します。 真の永続性の無知を持つ POPO は、永続化層からの要件に直面しませんが、真の永続性の無知を達成することは事実上不可能です。代わりに、相対的な度数で永続性の無知を測定します。 永続化指向の基本クラスから継承する必要がある場合や、POTO で遅延読み込みを実現するために特殊なコレクションを使用する必要がある場合は、残念です。 幸いなことに、EF4 は、あまり侵入性の低いソリューションを持っています。

### <a name="virtually-undetectable"></a>事実上検出不可能

POCO オブジェクトを使用する場合、EF4 はエンティティの実行時プロキシを動的に生成できます。 これらのプロキシは、具体化された POKO を目に見えない形でラップし、追加の処理を実行する各プロパティの取得および設定操作をインターセプトして追加のサービスを提供します。 そのようなサービスの 1 つは、私たちが探している遅延読み込み機能です。 もう 1 つのサービスは、プログラムがエンティティのプロパティ値を変更するタイミングを記録できる効率的な変更追跡メカニズムです。 変更の一覧は、SaveChanges メソッドの実行中に、UPDATE コマンドを使用して変更されたエンティティを保持するために ObjectContext によって使用されます。

ただし、これらのプロキシが機能するには、プロパティの取得とエンティティの設定操作にフックする方法が必要であり、プロキシは仮想メンバーをオーバーライドすることによってこの目標を達成します。 したがって、暗黙の遅延読み込みと効率的な変更追跡を行いたい場合は、POCO クラス定義に戻り、プロパティを仮想としてマークする必要があります。

``` csharp
    public class Employee {
        public virtual int Id { get; set; }
        public virtual string Name { get; set; }
        public virtual DateTime HireDate { get; set; }
        public virtual ICollection<TimeCard> TimeCards { get; set; }
    }
```

従業員エンティティは主に永続性が無知であるといえます。 唯一の要件は仮想メンバーを使用する必要があり、これはコードのテストの容易性に影響を与えません。 特別な基本クラスから派生したり、遅延読み込みに特化した特別なコレクションを使用したりする必要はありません。 コードが示すように、ICollection&lt;T&gt;を実装するクラスは、関連するエンティティを保持するために使用できます。

また、作業単位内で行う必要がある小さな変更も 1 つあります。 ObjectContext オブジェクトを直接操作する場合、遅延読み込みは既定で*オフ*になっています。 遅延読み込みを有効にする ContextOptions プロパティに設定できるプロパティがあり、どこでも遅延読み込みを有効にする場合は、このプロパティを実際の作業単位内に設定できます。

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

暗黙的な遅延読み込みが有効になっていると、アプリケーション コードは従業員と従業員の関連付けられたタイム カードを使用しながら、EF が余分なデータを読み込むために必要な作業を意識しなくなります。

``` csharp
    var employee = _unitOfWork.Employees
                              .Single(e => e.Id == id);
    foreach (var card in employee.TimeCards) {
        // ...
    }
```

遅延読み込みにより、アプリケーション コードの記述が簡単になり、プロキシ マジックを使用すると、コードは完全にテスト可能です。 作業単位のメモリ内フェイクは、テスト中に必要に応じて、関連データを持つ偽のエンティティをプリロードできます。

この時点で、IObjectSet&lt;T&gt;を使用してリポジトリを構築することから注意を向け、抽象化を見て永続化フレームワークのすべての兆候を隠します。

## <a name="custom-repositories"></a>カスタム リポジトリ

この記事で作業単位のデザイン パターンを最初に説明した時点で、作業単位の外観に関するサンプル コードをいくつか提供しました。 この元のアイデアを、作業してきた従業員と従業員のタイム カード シナリオを使用して再提示しましょう。

``` csharp
    public interface IUnitOfWork {
        IRepository<Employee> Employees { get; }
        IRepository<TimeCard> TimeCards { get;  }
        void Commit();
    }
```

この作業単位と最後のセクションで作成した作業単位の主な違いは、この作業単位が EF4 フレームワークからの抽象化を使用しない方法です (IObjectSet&lt;T&gt;はありません)。 IObjectSet&lt;&gt; T はリポジトリ インターフェイスとして機能しますが、公開する API はアプリケーションのニーズに完全に合わない可能性があります。 この今後のアプローチでは、カスタム IRepository T&lt;&gt;抽象化を使用してリポジトリを表します。

テスト駆動型設計、行動駆動型設計、およびドメイン駆動型の手法設計に従う開発者の多くは、&lt;いくつかの&gt;理由から IRepository T アプローチを採用しています。 最初に、IRepository&lt;&gt; T インターフェイスは"破損防止" 層を表します。 ドメイン駆動型デザインの書籍で Eric Evans が説明したように、破損防止レイヤーは、永続 API のように、インフラストラクチャ API からドメイン コードを遠ざけます。 次に、開発者は、(テストの作成中に検出された) アプリケーションの正確なニーズを満たすメソッドをリポジトリに組み込むことができます。 たとえば、ID 値を使用して 1 つのエンティティを検索する必要が頻繁に発生するため、FindById メソッドをリポジトリ インターフェイスに追加できます。IRepository&lt;T&gt;の定義は次のようになります。

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

エンティティ コレクションを公開する IQueryable&lt;T&gt;インターフェイスの使用に戻ります。 IQueryable&lt;&gt; T を使用すると、LINQ 式ツリーを EF4 プロバイダーにフローし、プロバイダーにクエリの全体的なビューを提供できます。 2 つ目のオプションは&lt;IEnumerable&gt;T を返す方法で、EF4 LINQ プロバイダーはリポジトリ内に構築された式のみを表示します。 リポジトリーの外部で行われたグループ化、順序付け、およびプロジェクションは、データベースに送信される SQL コマンドに構成されず、パフォーマンスが低下する可能性があります。 一方、IEnumerable&lt;T&gt;の結果のみを返すリポジトリは、新しい SQL コマンドで驚くことはありません。 どちらの方法も機能し、両方のアプローチはテスト可能なままです。

ジェネリックと EF4 ObjectContext API を使用して&lt;&gt; IRepository T インターフェイスの単一の実装を提供するのは簡単です。

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

IRepository&lt;T&gt;アプローチでは、クライアントがエンティティにアクセスするためのメソッドを呼び出す必要があるため、クエリを制御する追加の方法が提供されます。 メソッド内では、追加のチェックと LINQ 演算子を提供して、アプリケーションの制約を適用できます。 インターフェイスには、ジェネリック型パラメーターに 2 つの制約があることに注意してください。 1 つ目の制約は ObjectSet&lt;T&gt;で必要とされるクラスの制約であり、2 つ目の制約は、アプリケーション用に作成された抽象化である IEntity をエンティティに強制的に実装します。 IEntity インターフェイスは、エンティティに読み取り可能な Id プロパティを強制し、FindById メソッドでこのプロパティを使用できます。 IEntity は次のコードで定義されています。

``` csharp
    public interface IEntity {
        int Id { get; }
    }
```

IEntity は、このインターフェイスを実装するためにエンティティが必要であるため、永続化の無知の小さな違反と見なされる可能性があります。 永続性の無知はトレードオフに関するもので、FindById 機能の多くはインターフェイスによって課される制約を上回ることを覚えておいてください。 このインターフェイスは、テストの容易性に影響を与える必要はありません。

ライブ IRepository&lt;T&gt;をインスタンス化するには、EF4 ObjectContext が必要であるため、具体的な作業単位の実装では、インスタンス化を管理する必要があります。

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

### <a name="using-the-custom-repository"></a>カスタム リポジトリの使用

カスタム リポジトリの使用は、IObjectSet&lt;T&gt;に基づくリポジトリを使用する場合と大きな違いはありません。 LINQ 演算子をプロパティに直接適用する代わりに、まずリポジトリのメソッドを呼び出して IQueryable&lt;T&gt;参照を取得する必要があります。

``` csharp
    public ViewResult Index() {
        var model = _repository.FindAll()
                               .Include("TimeCards")
                               .OrderBy(e => e.HireDate);
        return View(model);
    }
```

以前に実装したカスタムの Include 演算子は、変更せずに動作します。 リポジトリの FindById メソッドは、1 つのエンティティを取得しようとするアクションから重複したロジックを削除します。

``` csharp
    public ViewResult Details(int id) {
        var model = _repository.FindById(id);
        return View(model);
    }
```

調査した 2 つのアプローチのテスト可能性に大きな違いはありません。 前の&lt;セクションで行ったように、HashSet&gt; &lt;Employee&gt;の支援を受けた具体的なクラスを構築することで、IRepository Tの偽の実装を提供することができました。 ただし、開発者によっては、偽物を作成する代わりにモック オブジェクトとモック オブジェクト フレームワークを使用することを好む開発者もいます。 モックを使用して実装をテストし、モックとフェイクの違いについては次のセクションで説明します。

### <a name="testing-with-mocks"></a>モックを使用したテスト

マーティン・ファウラーが「テスト・ダブル」と呼ぶものを構築するには、さまざまなアプローチがあります。 テストダブル(映画スタントダブルなど)は、テスト中に実際のプロダクションオブジェクトのために「立つ」ために構築するオブジェクトです。 作成したメモリ内リポジトリは、SQL Server との間でやり取りするリポジトリのテストダブルです。 単体テスト中にこれらのテストダブルを使用してコードを分離し、テストを高速に実行する方法を見てきました。

私たちが構築したテストダブルは、実際の作業実装を持っています。 シーンの背後には、それぞれがオブジェクトの具体的なコレクションを格納し、テスト中にリポジトリを操作する際に、このコレクションに対してオブジェクトを追加および削除します。 実際のコードと実際の実装で、テストをこのようにビルドしたい開発者もいます。これらのテストダブルは、私たちが*偽物と*呼ぶものです. 彼らは作業実装を持っていますが、実稼働環境では十分ではありません。 偽のリポジトリは実際にはデータベースに書き込みません。 偽の SMTP サーバーは、実際にはネットワーク経由で電子メール メッセージを送信しません。

### <a name="mocks-versus-fakes"></a>モック対フェイク

*モック*と呼ばれる別のタイプのテストダブルがあります。 偽物は動作する実装を持っていますが、モックは実装なしで来ます。 モック オブジェクト フレームワークの助けを借りて、実行時にこれらのモック オブジェクトを構築し、テストダブルとして使用します。 このセクションでは、オープンソースのモックフレームワーク Moq を使用します。 Moq を使用して従業員リポジトリのテストダブルを動的に作成する簡単な例を次に示します。

``` csharp
    Mock<IRepository<Employee>> mock =
        new Mock<IRepository<Employee>>();
    IRepository<Employee> repository = mock.Object;
    repository.Add(new Employee());
    var employee = repository.FindById(1);
```

Moq に IRepository&lt;従業員&gt;の実装を依頼し、動的に 1 つを構築します。 モック&lt;T&gt;オブジェクトの Object プロパティ&lt;にアクセスすることで、IRepository Employee&gt;を実装するオブジェクトにアクセスできます。 コントローラに渡すことができるのはこの内部オブジェクトであり、これがテストダブルか実際のリポジトリかは分かりません。 実際の実装を使用してオブジェクトのメソッドを呼び出すのと同じように、オブジェクトのメソッドを呼び出すことができます。

Add メソッドを呼び出すときにモック リポジトリが何をするのか疑問に思う必要があります。 モック オブジェクトの背後には実装がないため、Add は何もしません。 私たちが書いた偽物のように、舞台裏に具体的なコレクションがないので、従業員は捨てられます。 FindById の戻り値についてはどうでしょうか。 この場合、モックオブジェクトは、それができる唯一の操作を行います。 参照型 (Employee) を返すため、戻り値は null 値です。

モックは価値のない音かもしれません。しかし、私たちが話していないモックの機能が2つあります。 まず、Moq フレームワークはモック オブジェクトで行われたすべての呼び出しを記録します。 コードの後半で、誰かが Add メソッドを呼び出したか、または FindById メソッドを呼び出した場合は Moq に問い合わせることができます。 この「ブラックボックス」の記録機能をテストで使用する方法については、後で説明します。

2番目の大きな特徴は、モックを使用して *、期待*したモックオブジェクトをプログラムする方法です。 期待は、モックオブジェクトに特定の相互作用にどのように応答するかを伝えます。 たとえば、期待をモックにプログラミングし、誰かが FindById を呼び出したときに従業員オブジェクトを返すように指示できます。 Moq フレームワークは、セットアップ API とラムダ式を使用して、これらの期待値をプログラミングします。

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

このサンプルでは、Moq にリポジトリを動的に構築するよう依頼し、期待してリポジトリをプログラムします。 期待値は、値 5 を渡す FindById メソッドを呼び出したときに、Id 値が 5 の新しい従業員オブジェクトを返すようにモック オブジェクトに指示します。 このテストは成功し、IRepository&lt;T&gt;を偽るための完全な実装を構築する必要はありませんでした。

先ほど書いたテストを再検討し、偽物の代わりにモックを使用するように再作業してみましょう。 以前と同様に、基本クラスを使用して、すべてのコントローラーのテストに必要な共通のインフラストラクチャを設定します。

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

セットアップ コードはほとんど同じです。 偽物を使用する代わりに、モックオブジェクトを構築するためにMoqを使用します。 基本クラスは、コードが Employees プロパティを呼び出すときにモック リポジトリを返すように、作業のモック単位を配置します。 モックセットアップの残りの部分は、各特定のシナリオ専用のテスト備品内で行われます。 たとえば、インデックス アクションのテストフィクスチャは、アクションがモック リポジトリの FindAll メソッドを呼び出したときに、従業員のリストを返すようにモック リポジトリを設定します。

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

期待を除いて、私たちのテストは以前のテストに似ています。 しかし、モックフレームワークの記録能力を使用して、異なる角度からテストにアプローチすることができます。 この新しい視点は次のセクションで見ていきます。

### <a name="state-versus-interaction-testing"></a>状態と相互作用テスト

モック オブジェクトを使用してソフトウェアをテストするために使用できるさまざまな手法があります。 1 つのアプローチは、状態ベースのテストを使用することです。 状態ベースのテストでは、ソフトウェアの状態に関するアサーションが行われます。 最後のテストでは、コント ローラーのアクション メソッドを呼び出し、ビルドする必要があるモデルに関するアサーションを行いました。 テスト状態の他の例を次に示します。

-   Create の実行後に、リポジトリに新しい従業員オブジェクトが含まれていることを確認します。
-   Index の実行後に、モデルがすべての従業員のリストを保持しているかどうかを確認します。
-   Delete の実行後に、リポジトリに特定の従業員が含まれていないかどうかを確認します。

モック オブジェクトで見るもう 1 つの方法は、相互作用を検証*することです*。 状態ベースのテストではオブジェクトの状態に関するアサーションが行われますが、相互作用ベースのテストでは、オブジェクトの相互作用に関するアサーションが行われます。 次に例を示します。

-   Create の実行時に、コントローラーがリポジトリの Add メソッドを呼び出すかどうかを確認します。
-   コントローラーが、Index の実行時にリポジトリの FindAll メソッドを呼び出すかどうかを確認します。
-   コントローラーが作業単位の Commit メソッドを呼び出して、Edit の実行時に変更を保存することを確認します。

インタラクションテストでは、コレクション内を突いてカウントを検証していないため、多くの場合、必要なテスト データが少なくなります。 たとえば、Details アクションが正しい値を持つリポジトリの FindById メソッドを呼び出す場合、アクションは正しく動作している可能性があります。 FindById から返すテスト データを設定しなくても、この動作を検証できます。

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

上記のテストフィクスチャで必要なセットアップは、基本クラスによって提供されるセットアップのみです。 コントローラアクションを呼び出すと、モックリポジトリとの対話が記録されます。 Moq の検証 API を使用して、コントローラが適切な ID 値で FindById を呼び出したかどうか Moq に問い合わせることができます。 コントローラーがメソッドを呼び出さなかった場合、または予期しないパラメーター値を指定してメソッドを呼び出した場合、Verify メソッドは例外をスローし、テストは失敗します。

Create アクションが現在の作業単位でコミットを呼び出すことを確認する別の例を次に示します。

``` csharp
    [TestMethod]
    public void ShouldCommitUnitOfWork() {
        _controller.Create(_newEmployee);
        _unitOfWork.Verify(u => u.Commit());
    }
```

相互作用テストの危険性の1つは、相互作用を過剰に指定する傾向にあります。 モック オブジェクトがモック オブジェクトとのすべての対話を記録および検証する機能は、テストがすべての相互作用を検証しようとする必要があることを意味しません。 インタラクションの中には実装の詳細なものもありますが、現在のテストを満たすために*必要な*インタラクションのみを検証する必要があります。

モックまたは偽物の間の選択は、主にあなたがテストしているシステムとあなたの個人的な(またはチーム)の好みに依存します。 モック オブジェクトを使用すると、テストの倍数を実装するために必要なコードの量を大幅に削減できますが、誰もが期待するプログラミングと対話の検証に慣れたわけではありません。

## <a name="conclusions"></a>まとめ

このペーパーでは、データの永続化にADO.NET Entity Framework を使用しながらテスト可能なコードを作成するいくつかの方法を示しました。 IObjectSet&lt;T のような組み込みの抽象化&gt;を利用したり、IRepository&lt;T&gt;のような独自の抽象化を作成したりできます。どちらの場合も、ADO.NET Entity Framework 4.0 の POCO サポートにより、これらの抽象化のコンシューマーは、常に無知で高度にテスト可能です。 暗黙の遅延読み込みなどの追加の EF4 機能を使用すると、リレーショナル データ ストアの詳細を気にせずにビジネスおよびアプリケーションのサービス コードを動作させることができます。 最後に、作成する抽象化は単体テストの内部で簡単にモックまたは偽りで、高速な実行、高度に隔離された、信頼性の高いテストを実現するためにこれらのテストダブルを使用できます。

### <a name="additional-resources"></a>その他のリソース

-   ロバート・C・マーティン「[単一責任原則](https://www.objectmentor.com/resources/articles/srp.pdf)」
-   マーティン・ファウラー、*エンタープライズアプリケーションアーキテクチャのパターンからパターンの*[カタログ](https://www.martinfowler.com/eaaCatalog/index.html)
-   グリフィン・カプリオ「[依存性注入](https://msdn.microsoft.com/magazine/cc163739.aspx)」
-   データ プログラミング ブログ[「 チュートリアル : Entity Framework 4.0 を使用したテスト駆動型開発](https://docs.microsoft.com/archive/blogs/adonet/walkthrough-test-driven-development-with-the-entity-framework-4-0)」
-   データ プログラミング ブログ"[エンティティ フレームワーク 4.0 を使用したリポジトリおよび作業単位パターンの使用](https://docs.microsoft.com/archive/blogs/adonet/using-repository-and-unit-of-work-patterns-with-entity-framework-4-0)"
-   アーロン・ジェンセン「[機械仕様の紹介](http://codebetter.com/blogs/aaron.jensen/archive/2008/05/08/introducing-machine-specifications-or-mspec-for-short.aspx)」
-   エリック・リー [「MSTestとBDD」](https://saintgimp.org/2009/01/20/bdd-with-mstest/)
-   エリック・エヴァンス「[ドメイン駆動型デザイン](https://books.google.com/books?id=7dlaMs0SECsC&printsec=frontcover&dq=evans%20domain%20driven%20design&hl=en&ei=cHztS6C8KIaglAfA_dS1CA&sa=X&oi=book_result&ct=result&resnum=1&ved=0CCoQ6AEwAA)」
-   マーティン・ファウラー「[モックはスタブではない](https://martinfowler.com/articles/mocksArentStubs.html)」
-   マーティン・ファウラー「[テストダブル](https://martinfowler.com/bliki/TestDouble.html)」
-   [Moq](https://code.google.com/p/moq/)

### <a name="biography"></a>伝記

スコット・アレンは、Pluralsightの技術スタッフの一員であり、OdeToCode.comの創設者です。 15年間の商用ソフトウェア開発の中で、Scottは8ビットの組み込みデバイスから拡張性の高いASP.NETWebアプリケーションまで、あらゆるソリューションに取り組んできました。 スコットはOdeToCodeのブログやツイッター(.)で[https://twitter.com/OdeToCode](https://twitter.com/OdeToCode)アクセスできます。
