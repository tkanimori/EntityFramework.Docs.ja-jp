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
# <a name="testability-and-entity-framework-40"></a><span data-ttu-id="e45d5-102">テストの容易性とエンティティ フレームワーク 4.0</span><span class="sxs-lookup"><span data-stu-id="e45d5-102">Testability and Entity Framework 4.0</span></span>
<span data-ttu-id="e45d5-103">スコット・アレン</span><span class="sxs-lookup"><span data-stu-id="e45d5-103">Scott Allen</span></span>

<span data-ttu-id="e45d5-104">公開: 2010 年 5 月</span><span class="sxs-lookup"><span data-stu-id="e45d5-104">Published: May 2010</span></span>

## <a name="introduction"></a><span data-ttu-id="e45d5-105">はじめに</span><span class="sxs-lookup"><span data-stu-id="e45d5-105">Introduction</span></span>

<span data-ttu-id="e45d5-106">このホワイト ペーパーでは、エンティティ フレームワーク 4.0 と Visual Studio 2010 ADO.NETを使用してテスト可能なコードを記述する方法について説明し、デモンストレーションします。</span><span class="sxs-lookup"><span data-stu-id="e45d5-106">This white paper describes and demonstrates how to write testable code with the ADO.NET Entity Framework 4.0 and Visual Studio 2010.</span></span> <span data-ttu-id="e45d5-107">このホワイト ペーパーでは、テスト駆動型設計 (TDD) や動作駆動型設計 (BDD) など、特定のテスト方法論に焦点を当てていません。</span><span class="sxs-lookup"><span data-stu-id="e45d5-107">This paper does not try to focus on a specific testing methodology, like test-driven design (TDD) or behavior-driven design (BDD).</span></span> <span data-ttu-id="e45d5-108">代わりに、このペーパーでは、ADO.NET Entity Framework を使用するコードを記述する方法に焦点を当てますが、分離して自動化された方法でテストするのは簡単です。</span><span class="sxs-lookup"><span data-stu-id="e45d5-108">Instead this paper will focus on how to write code that uses the ADO.NET Entity Framework yet remains easy to isolate and test in an automated fashion.</span></span> <span data-ttu-id="e45d5-109">データ アクセス シナリオでのテストを容易にする一般的なデザイン パターンを見て、フレームワークを使用する際にそれらのパターンを適用する方法を確認します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-109">We’ll look at common design patterns that facilitate testing in data access scenarios and see how to apply those patterns when using the framework.</span></span> <span data-ttu-id="e45d5-110">また、フレームワークの特定の機能を見て、これらの機能がテスト可能なコードでどのように機能するかを確認します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-110">We’ll also look at specific features of the framework to see how those features can work in testable code.</span></span>

## <a name="what-is-testable-code"></a><span data-ttu-id="e45d5-111">テスト可能なコードとは</span><span class="sxs-lookup"><span data-stu-id="e45d5-111">What Is Testable Code?</span></span>

<span data-ttu-id="e45d5-112">自動単体テストを使用してソフトウェアを検証する機能には、多くの望ましい利点があります。</span><span class="sxs-lookup"><span data-stu-id="e45d5-112">The ability to verify a piece of software using automated unit tests offers many desirable benefits.</span></span> <span data-ttu-id="e45d5-113">優れたテストは、アプリケーションのソフトウェア欠陥の数を減らし、アプリケーションの品質を向上させることは誰もが知っていますが、単体テストを実施するだけではバグを見つけるだけではありません。</span><span class="sxs-lookup"><span data-stu-id="e45d5-113">Everyone knows that good tests will reduce the number of software defects in an application and increase the application’s quality - but having unit tests in place goes far beyond just finding bugs.</span></span>

<span data-ttu-id="e45d5-114">優れた単体テスト スイートを使用すると、開発チームは時間を節約し、作成するソフトウェアを制御できます。</span><span class="sxs-lookup"><span data-stu-id="e45d5-114">A good unit test suite allows a development team to save time and remain in control of the software they create.</span></span> <span data-ttu-id="e45d5-115">チームは、既存のコードの変更、リファクタリング、再設計、およびソフトウェアの再構築を行って新しい要件を満たし、新しいコンポーネントをアプリケーションに追加し、テストスイートがアプリケーションの動作を検証できることを知ることができます。</span><span class="sxs-lookup"><span data-stu-id="e45d5-115">A team can make changes to existing code, refactor, redesign, and restructure software to meet new requirements, and add new components into an application all while knowing the test suite can verify the application’s behavior.</span></span> <span data-ttu-id="e45d5-116">単体テストは、変更を促進し、複雑さが増すに応じてソフトウェアの保守性を維持するための、迅速なフィードバック サイクルの一部です。</span><span class="sxs-lookup"><span data-stu-id="e45d5-116">Unit tests are part of a quick feedback cycle to facilitate change and preserve the maintainability of software as complexity increases.</span></span>

<span data-ttu-id="e45d5-117">ただし、単体テストには価格が付属しています。</span><span class="sxs-lookup"><span data-stu-id="e45d5-117">Unit testing comes with a price, however.</span></span> <span data-ttu-id="e45d5-118">チームは、単体テストを作成および管理するために時間を投資する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e45d5-118">A team has to invest the time to create and maintain unit tests.</span></span> <span data-ttu-id="e45d5-119">これらのテストを作成するために必要な労力は、基礎となるソフトウェアの**テスト可能性**に直接関係しています。</span><span class="sxs-lookup"><span data-stu-id="e45d5-119">The amount of effort required to create these tests is directly related to the **testability** of the underlying software.</span></span> <span data-ttu-id="e45d5-120">ソフトウェアのテストはどの程度簡単ですか?</span><span class="sxs-lookup"><span data-stu-id="e45d5-120">How easy is the software to test?</span></span> <span data-ttu-id="e45d5-121">テストの容易性を考慮してソフトウェアを設計するチームは、テスト不可能なソフトウェアを使用するチームよりも迅速に効果的なテストを作成します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-121">A team designing software with testability in mind will create effective tests faster than the team working with un-testable software.</span></span>

<span data-ttu-id="e45d5-122">マイクロソフトでは、テストの可能性を考慮して、ADO.NET エンティティ フレームワーク 4.0 (EF4) を設計しました。</span><span class="sxs-lookup"><span data-stu-id="e45d5-122">Microsoft designed the ADO.NET Entity Framework 4.0 (EF4) with testability in mind.</span></span> <span data-ttu-id="e45d5-123">これは、開発者がフレームワーク コード自体に対して単体テストを記述することを意味するものではありません。</span><span class="sxs-lookup"><span data-stu-id="e45d5-123">This doesn’t mean developers will be writing unit tests against framework code itself.</span></span> <span data-ttu-id="e45d5-124">代わりに、EF4 のテスト可能性の目標を使用すると、フレームワークの上に構築されたテスト可能なコードを簡単に作成できます。</span><span class="sxs-lookup"><span data-stu-id="e45d5-124">Instead, the testability goals for EF4 make it easy to create testable code that builds on top of the framework.</span></span> <span data-ttu-id="e45d5-125">具体的な例を見る前に、テスト可能なコードの品質を理解する価値があります。</span><span class="sxs-lookup"><span data-stu-id="e45d5-125">Before we look at specific examples, it’s worthwhile to understand the qualities of testable code.</span></span>

### <a name="the-qualities-of-testable-code"></a><span data-ttu-id="e45d5-126">テスト可能なコードの品質</span><span class="sxs-lookup"><span data-stu-id="e45d5-126">The Qualities of Testable Code</span></span>

<span data-ttu-id="e45d5-127">テストが簡単なコードでは、少なくとも 2 つの特性が表示されます。</span><span class="sxs-lookup"><span data-stu-id="e45d5-127">Code that is easy to test will always exhibit at least two traits.</span></span> <span data-ttu-id="e45d5-128">まず、テスト可能なコードは**簡単に観察できます**。</span><span class="sxs-lookup"><span data-stu-id="e45d5-128">First, testable code is easy to **observe**.</span></span> <span data-ttu-id="e45d5-129">いくつかの入力セットを考えると、コードの出力を簡単に観察できるはずです。</span><span class="sxs-lookup"><span data-stu-id="e45d5-129">Given some set of inputs, it should be easy to observe the output of the code.</span></span> <span data-ttu-id="e45d5-130">たとえば、次のメソッドのテストは、メソッドが計算の結果を直接返すため簡単です。</span><span class="sxs-lookup"><span data-stu-id="e45d5-130">For example, testing the following method is easy because the method directly returns the result of a calculation.</span></span>

``` csharp
    public int Add(int x, int y) {
        return x + y;
    }
```

<span data-ttu-id="e45d5-131">メソッドが計算された値をネットワーク ソケット、データベース テーブル、または次のコードのようなファイルに書き込む場合、メソッドのテストは困難です。</span><span class="sxs-lookup"><span data-stu-id="e45d5-131">Testing a method is difficult if the method writes the computed value into a network socket, a database table, or a file like the following code.</span></span> <span data-ttu-id="e45d5-132">テストでは、値を取得するために追加の作業を実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e45d5-132">The test has to perform additional work to retrieve the value.</span></span>

``` csharp
    public void AddAndSaveToFile(int x, int y) {
         var results = string.Format("The answer is {0}", x + y);
         File.WriteAllText("results.txt", results);
    }
```

<span data-ttu-id="e45d5-133">第二に、テスト可能なコードは**分離**しやすいです。</span><span class="sxs-lookup"><span data-stu-id="e45d5-133">Secondly, testable code is easy to **isolate**.</span></span> <span data-ttu-id="e45d5-134">テスト可能なコードの不適切な例として、次の擬似コードを使用してみましょう。</span><span class="sxs-lookup"><span data-stu-id="e45d5-134">Let’s use the following pseudo-code as a bad example of testable code.</span></span>

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

<span data-ttu-id="e45d5-135">この方法は観察が容易で、保険契約を通過し、戻り値が期待される結果と一致することを確認できます。</span><span class="sxs-lookup"><span data-stu-id="e45d5-135">The method is easy to observe – we can pass in an insurance policy and verify the return value matches an expected result.</span></span> <span data-ttu-id="e45d5-136">ただし、メソッドをテストするには、正しいスキーマを使用してデータベースをインストールし、メソッドが電子メールを送信しようとする場合に備えて SMTP サーバーを構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e45d5-136">However, to test the method we’ll need to have a database installed with the correct schema, and configure the SMTP server in case the method tries to send an email.</span></span>

<span data-ttu-id="e45d5-137">単体テストでは、メソッド内の計算ロジックを検証する必要がありますが、電子メール サーバーがオフラインであるか、データベース サーバーが移動したためにテストが失敗する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="e45d5-137">The unit test only wants to verify the calculation logic inside the method, but the test might fail because the email server is offline, or because the database server moved.</span></span> <span data-ttu-id="e45d5-138">これらのエラーは、テストで検証する動作とは無関係です。</span><span class="sxs-lookup"><span data-stu-id="e45d5-138">Both of these failures are unrelated to the behavior the test wants to verify.</span></span> <span data-ttu-id="e45d5-139">動作を特定するのは困難です。</span><span class="sxs-lookup"><span data-stu-id="e45d5-139">The behavior is difficult to isolate.</span></span>

<span data-ttu-id="e45d5-140">テスト可能なコードを記述しようとするソフトウェア開発者は、多くの場合、記述するコード内の懸念事項の分離を維持しようと努力します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-140">Software developers who strive to write testable code often strive to maintain a separation of concerns in the code they write.</span></span> <span data-ttu-id="e45d5-141">上記の方法は、ビジネスの計算に焦点を当て、データベースと電子メールの実装の詳細を他のコンポーネントに委任する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e45d5-141">The above method should focus on the business calculations and delegate the database and email implementation details to other components.</span></span> <span data-ttu-id="e45d5-142">ロバート・C・マーティンはこれを単一責任原則と呼んでいます。</span><span class="sxs-lookup"><span data-stu-id="e45d5-142">Robert C. Martin calls this the Single Responsibility Principle.</span></span> <span data-ttu-id="e45d5-143">オブジェクトは、ポリシーの値の計算のように、単一の狭い責任をカプセル化する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e45d5-143">An object should encapsulate a single, narrow responsibility, like calculating the value of a policy.</span></span> <span data-ttu-id="e45d5-144">その他のデータベースと通知作業はすべて、他のオブジェクトの責任である必要があります。</span><span class="sxs-lookup"><span data-stu-id="e45d5-144">All other database and notification work should be the responsibility of some other object.</span></span> <span data-ttu-id="e45d5-145">この方法で記述されたコードは、単一のタスクに焦点を当てているため、分離が容易です。</span><span class="sxs-lookup"><span data-stu-id="e45d5-145">Code written in this fashion is easier to isolate because it is focused on a single task.</span></span>

<span data-ttu-id="e45d5-146">NET には、単一責任の原則に従い、分離を実現するために必要な抽象化があります。</span><span class="sxs-lookup"><span data-stu-id="e45d5-146">In .NET we have the abstractions we need to follow the Single Responsibility Principle and achieve isolation.</span></span> <span data-ttu-id="e45d5-147">インターフェイス定義を使用して、具体的な型の代わりにインターフェイスの抽象化を使用するようにコードを強制できます。</span><span class="sxs-lookup"><span data-stu-id="e45d5-147">We can use interface definitions and force the code to use the interface abstraction instead of a concrete type.</span></span> <span data-ttu-id="e45d5-148">このホワイト ペーパーの後半では、上記の例のような方法が、データベースと話すように*見える*インターフェイスで動作する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-148">Later in this paper we’ll see how a method like the bad example presented above can work with interfaces that *look* like they will talk to the database.</span></span> <span data-ttu-id="e45d5-149">ただし、テスト時には、データベースとの間でやり取りせず、メモリ内にデータを保持するダミー実装を置き換えることができます。</span><span class="sxs-lookup"><span data-stu-id="e45d5-149">At test time, however, we can substitute a dummy implementation that doesn’t talk to the database but instead holds data in memory.</span></span> <span data-ttu-id="e45d5-150">このダミー実装では、データ アクセス コードまたはデータベース構成の関連のない問題からコードを分離します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-150">This dummy implementation will isolate the code from unrelated problems in the data access code or database configuration.</span></span>

<span data-ttu-id="e45d5-151">分離には、さらに利点があります。</span><span class="sxs-lookup"><span data-stu-id="e45d5-151">There are additional benefits to isolation.</span></span> <span data-ttu-id="e45d5-152">最後の方法でのビジネス計算は、実行に数ミリ秒しかかかりませんが、コードがネットワークを飛び回り、さまざまなサーバーと通信する場合、テスト自体が数秒間実行される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="e45d5-152">The business calculation in the last method should only take a few milliseconds to execute, but the test itself might run for several seconds as the code hops around the network and talks to various servers.</span></span> <span data-ttu-id="e45d5-153">単体テストは、小規模な変更を容易にするために高速に実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e45d5-153">Unit tests should run fast to facilitate small changes.</span></span> <span data-ttu-id="e45d5-154">単体テストも繰り返し可能で、テストとは無関係のコンポーネントに問題があるため、失敗しないようにする必要があります。</span><span class="sxs-lookup"><span data-stu-id="e45d5-154">Unit tests should also be repeatable and not fail because a component unrelated to the test has a problem.</span></span> <span data-ttu-id="e45d5-155">コードを簡単に観察して分離するコードを記述すると、開発者はコードのテストを簡単に記述でき、テストの実行を待つ時間が短くなり、さらに重要なことに、存在しないバグの追跡に費やす時間が減ります。</span><span class="sxs-lookup"><span data-stu-id="e45d5-155">Writing code that is easy to observe and to isolate means developers will have an easier time writing tests for the code, spend less time waiting for tests to execute, and more importantly, spend less time tracking down bugs that do not exist.</span></span>

<span data-ttu-id="e45d5-156">テストの利点を理解し、テスト可能なコードが示す品質を理解できることを願っています。</span><span class="sxs-lookup"><span data-stu-id="e45d5-156">Hopefully you can appreciate the benefits of testing and understand the qualities that testable code exhibits.</span></span> <span data-ttu-id="e45d5-157">EF4 と連携してデータをデータベースに保存するコードの記述方法を取り上げるのですが、まずはデータ アクセスのテスト可能な設計について議論することに焦点を絞ります。</span><span class="sxs-lookup"><span data-stu-id="e45d5-157">We are about to address how to write code that works with EF4 to save data into a database while remaining observable and easy to isolate, but first we’ll narrow our focus to discuss testable designs for data access.</span></span>

## <a name="design-patterns-for-data-persistence"></a><span data-ttu-id="e45d5-158">データ永続性のためのデザイン パターン</span><span class="sxs-lookup"><span data-stu-id="e45d5-158">Design Patterns for Data Persistence</span></span>

<span data-ttu-id="e45d5-159">以前に提示された悪い例の両方があまりにも多くの責任を持っていました。</span><span class="sxs-lookup"><span data-stu-id="e45d5-159">Both of the bad examples presented earlier had too many responsibilities.</span></span> <span data-ttu-id="e45d5-160">最初の悪い例では、計算を実行*し、* ファイルに書き込む必要がありました。</span><span class="sxs-lookup"><span data-stu-id="e45d5-160">The first bad example had to perform a calculation *and* write to a file.</span></span> <span data-ttu-id="e45d5-161">2 番目の悪い例では、データベースからデータを読み取*り、* ビジネス計算を実行*し、* 電子メールを送信する必要がありました。</span><span class="sxs-lookup"><span data-stu-id="e45d5-161">The second bad example had to read data from a database *and* perform a business calculation *and* send email.</span></span> <span data-ttu-id="e45d5-162">懸念を分離し、他のコンポーネントに責任を委任する小さなメソッドを設計することで、テスト可能なコードの作成に大きく前進します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-162">By designing smaller methods that separate concerns and delegate responsibility to other components you’ll make great strides towards writing testable code.</span></span> <span data-ttu-id="e45d5-163">目標は、小規模で集中的な抽象化からアクションを作成することによって機能を構築することです。</span><span class="sxs-lookup"><span data-stu-id="e45d5-163">The goal is to build functionality by composing actions from small and focused abstractions.</span></span>

<span data-ttu-id="e45d5-164">データの永続化に関しては、私たちが探している小さくて焦点を絞った抽象化は、設計パターンとして文書化されているために一般的です。</span><span class="sxs-lookup"><span data-stu-id="e45d5-164">When it comes to data persistence the small and focused abstractions we are looking for are so common they’ve been documented as design patterns.</span></span> <span data-ttu-id="e45d5-165">マーティン・ファウラーの著書『エンタープライズ・アプリケーション・アーキテクチャーのパターン』は、これらのパターンを印刷物で記述した最初の作品でした。</span><span class="sxs-lookup"><span data-stu-id="e45d5-165">Martin Fowler’s book Patterns of Enterprise Application Architecture was the first work to describe these patterns in print.</span></span> <span data-ttu-id="e45d5-166">これらのパターンの簡単な説明を次のセクションで説明してから、これらのADO.NET Entity Framework がこれらのパターンを実装して動作する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-166">We’ll provide a brief description of these patterns in the following sections before we show how these ADO.NET Entity Framework implements and works with these patterns.</span></span>

### <a name="the-repository-pattern"></a><span data-ttu-id="e45d5-167">リポジトリ パターン</span><span class="sxs-lookup"><span data-stu-id="e45d5-167">The Repository Pattern</span></span>

<span data-ttu-id="e45d5-168">ファウラー氏は、リポジトリは「ドメインオブジェクトにアクセスするためのコレクションのようなインターフェイスを使用して、ドメインとデータマッピング層の間を仲介する」と述べています。</span><span class="sxs-lookup"><span data-stu-id="e45d5-168">Fowler says a repository “mediates between the domain and data mapping layers using a collection-like interface for accessing domain objects”.</span></span> <span data-ttu-id="e45d5-169">リポジトリ パターンの目的は、コードをデータ アクセスの minutiae から分離することであり、以前に見た分離はテスト容易性に必要な特性です。</span><span class="sxs-lookup"><span data-stu-id="e45d5-169">The goal of the repository pattern is to isolate code from the minutiae of data access, and as we saw earlier isolation is a required trait for testability.</span></span>

<span data-ttu-id="e45d5-170">分離の鍵は、リポジトリがコレクションのようなインターフェイスを使用してオブジェクトを公開する方法です。</span><span class="sxs-lookup"><span data-stu-id="e45d5-170">The key to the isolation is how the repository exposes objects using a collection-like interface.</span></span> <span data-ttu-id="e45d5-171">リポジトリを使用するために記述するロジックには、リポジトリが要求したオブジェクトをどのように具体化するのかわかりません。</span><span class="sxs-lookup"><span data-stu-id="e45d5-171">The logic you write to use the repository has no idea how the repository will materialize the objects you request.</span></span> <span data-ttu-id="e45d5-172">リポジトリはデータベースとの間でやり取りする場合も、メモリ内コレクションからオブジェクトを返すだけの場合もあります。</span><span class="sxs-lookup"><span data-stu-id="e45d5-172">The repository might talk to a database, or it might just return objects from an in-memory collection.</span></span> <span data-ttu-id="e45d5-173">コードで知っておくべきことは、リポジトリがコレクションを保持するように見え、コレクションからオブジェクトを取得、追加、および削除できることです。</span><span class="sxs-lookup"><span data-stu-id="e45d5-173">All your code needs to know is that the repository appears to maintain the collection, and you can retrieve, add, and delete objects from the collection.</span></span>

<span data-ttu-id="e45d5-174">既存の .NET アプリケーションでは、具象リポジトリは、次のような汎用インターフェイスから継承することがよくあります。</span><span class="sxs-lookup"><span data-stu-id="e45d5-174">In existing .NET applications a concrete repository often inherits from a generic interface like the following:</span></span>

``` csharp
    public interface IRepository<T> {       
        IEnumerable<T> FindAll();
        IEnumerable<T> FindBy(Expression<Func\<T, bool>> predicate);
        T FindById(int id);
        void Add(T newEntity);
        void Remove(T entity);
    }
```

<span data-ttu-id="e45d5-175">EF4 の実装を提供する場合は、インターフェイス定義にいくつか変更を加えますが、基本的な概念は変わりません。</span><span class="sxs-lookup"><span data-stu-id="e45d5-175">We’ll make a few changes to the interface definition when we provide an implementation for EF4, but the basic concept remains the same.</span></span> <span data-ttu-id="e45d5-176">コードでは、このインターフェイスを実装する具象リポジトリを使用して、主キー値でエンティティを取得したり、述語の評価に基づいてエンティティのコレクションを取得したり、使用可能なすべてのエンティティを単純に取得したりできます。</span><span class="sxs-lookup"><span data-stu-id="e45d5-176">Code can use a concrete repository implementing this interface to retrieve an entity by its primary key value, to retrieve a collection of entities based on the evaluation of a predicate, or simply retrieve all available entities.</span></span> <span data-ttu-id="e45d5-177">このコードでは、リポジトリ インターフェイスを使用してエンティティを追加および削除することもできます。</span><span class="sxs-lookup"><span data-stu-id="e45d5-177">The code can also add and remove entities through the repository interface.</span></span>

<span data-ttu-id="e45d5-178">Employee オブジェクトの IRepository を指定すると、コードは次の操作を実行できます。</span><span class="sxs-lookup"><span data-stu-id="e45d5-178">Given an IRepository of Employee objects, code can perform the following operations.</span></span>

``` csharp
    var employeesNamedScott =
        repository
            .FindBy(e => e.Name == "Scott")
            .OrderBy(e => e.HireDate);
    var firstEmployee = repository.FindById(1);
    var newEmployee = new Employee() {/*... */};
    repository.Add(newEmployee);
```

<span data-ttu-id="e45d5-179">コードはインターフェイス (従業員の IRepository) を使用しているため、インターフェイスのさまざまな実装をコードに提供できます。</span><span class="sxs-lookup"><span data-stu-id="e45d5-179">Since the code is using an interface (IRepository of Employee), we can provide the code with different implementations of the interface.</span></span> <span data-ttu-id="e45d5-180">1 つの実装は、EF4 によってバックアップされ、オブジェクトを Microsoft SQL Server データベースに永続化する実装である場合があります。</span><span class="sxs-lookup"><span data-stu-id="e45d5-180">One implementation might be an implementation backed by EF4 and persisting objects into a Microsoft SQL Server database.</span></span> <span data-ttu-id="e45d5-181">別の実装 (テスト中に使用する実装) は、インメモリの Employee オブジェクトのリストによってバックアップされる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="e45d5-181">A different implementation (one we use during testing) might be backed by an in-memory List of Employee objects.</span></span> <span data-ttu-id="e45d5-182">インターフェイスは、コードの分離を実現するのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="e45d5-182">The interface will help to achieve isolation in the code.</span></span>

<span data-ttu-id="e45d5-183">IRepository&lt;T&gt;インターフェイスは、保存操作を公開しないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="e45d5-183">Notice the IRepository&lt;T&gt; interface does not expose a Save operation.</span></span> <span data-ttu-id="e45d5-184">既存のオブジェクトを更新する方法</span><span class="sxs-lookup"><span data-stu-id="e45d5-184">How do we update existing objects?</span></span> <span data-ttu-id="e45d5-185">保存操作を含む IRepository 定義に遭遇する可能性があり、これらのリポジトリの実装では、オブジェクトをデータベースに直ちに永続化する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e45d5-185">You might come across IRepository definitions that do include the Save operation, and implementations of these repositories will need to immediately persist an object into the database.</span></span> <span data-ttu-id="e45d5-186">しかし、多くのアプリケーションでは、オブジェクトを個別に保持したくありません。</span><span class="sxs-lookup"><span data-stu-id="e45d5-186">However, in many applications we don’t want to persist objects individually.</span></span> <span data-ttu-id="e45d5-187">代わりに、オブジェクトを、おそらく異なるリポジトリから実現し、ビジネス アクティビティの一部としてそれらのオブジェクトを変更し、すべてのオブジェクトを単一のアトミック操作の一部として永続化します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-187">Instead, we want to bring objects to life, perhaps from different repositories, modify those objects as part of a business activity, and then persist all the objects as part of a single, atomic operation.</span></span> <span data-ttu-id="e45d5-188">幸いにも、この種の動作を許可するパターンがあります。</span><span class="sxs-lookup"><span data-stu-id="e45d5-188">Fortunately, there is a pattern to allow this type of behavior.</span></span>

### <a name="the-unit-of-work-pattern"></a><span data-ttu-id="e45d5-189">作業単位パターン</span><span class="sxs-lookup"><span data-stu-id="e45d5-189">The Unit of Work Pattern</span></span>

<span data-ttu-id="e45d5-190">ファウラー氏は、作業単位は「ビジネス・トランザクションの影響を受けるオブジェクトのリストを維持し、変更の書き出しと同時実行の問題の解決を調整する」と述べています。</span><span class="sxs-lookup"><span data-stu-id="e45d5-190">Fowler says a unit of work will “maintain a list of objects affected by a business transaction and coordinates the writing out of changes and the resolution of concurrency problems”.</span></span> <span data-ttu-id="e45d5-191">リポジトリから作成したオブジェクトの変更を追跡し、変更をコミットするように指示したときにオブジェクトに加えた変更を保持するのは作業単位の責任です。</span><span class="sxs-lookup"><span data-stu-id="e45d5-191">It is the responsibility of the unit of work to track changes to the objects we bring to life from a repository and persist any changes we’ve made to the objects when we tell the unit of work to commit the changes.</span></span> <span data-ttu-id="e45d5-192">また、すべてのリポジトリに追加した新しいオブジェクトをデータベースに挿入し、削除を管理することも、作業単位の責任です。</span><span class="sxs-lookup"><span data-stu-id="e45d5-192">It’s also the responsibility of the unit of work to take the new objects we’ve added to all repositories and insert the objects into a database, and also mange deletion.</span></span>

<span data-ttu-id="e45d5-193">ADO.NET DataSet で作業を行ったことがあれば、作業単位パターンに慣れているはずです。</span><span class="sxs-lookup"><span data-stu-id="e45d5-193">If you’ve ever done any work with ADO.NET DataSets then you’ll already be familiar with the unit of work pattern.</span></span> <span data-ttu-id="e45d5-194">ADO.NET DataSet は、DataRow オブジェクトの更新、削除、挿入を追跡する機能を備えており、TableAdapter の助けを借りてデータベースに対するすべての変更を調整することができました。</span><span class="sxs-lookup"><span data-stu-id="e45d5-194">ADO.NET DataSets had the ability to track our updates, deletions, and insertion of DataRow objects and could (with the help of a TableAdapter) reconcile all our changes to a database.</span></span> <span data-ttu-id="e45d5-195">ただし、DataSet オブジェクトは、基になるデータベースの非接続されたサブセットをモデル化します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-195">However, DataSet objects model a disconnected subset of the underlying database.</span></span> <span data-ttu-id="e45d5-196">作業単位パターンは同じ動作を示しますが、データ アクセス コードから分離され、データベースを認識していないビジネス オブジェクトおよびドメイン オブジェクトで動作します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-196">The unit of work pattern exhibits the same behavior, but works with business objects and domain objects that are isolated from data access code and unaware of the database.</span></span>

<span data-ttu-id="e45d5-197">.NET コードで作業単位をモデル化する抽象化は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="e45d5-197">An abstraction to model the unit of work in .NET code might look like the following:</span></span>

``` csharp
    public interface IUnitOfWork {
        IRepository<Employee> Employees { get; }
        IRepository<Order> Orders { get; }
        IRepository<Customer> Customers { get; }
        void Commit();
    }
```

<span data-ttu-id="e45d5-198">作業単位からリポジトリ参照を公開することにより、単一の作業単位オブジェクトが、業務トランザクション中に実体化されたすべてのエンティティを追跡する機能を持つことを保証できます。</span><span class="sxs-lookup"><span data-stu-id="e45d5-198">By exposing repository references from the unit of work we can ensure a single unit of work object has the ability to track all entities materialized during a business transaction.</span></span> <span data-ttu-id="e45d5-199">実際の作業単位の Commit メソッドの実装は、メモリ内の変更をデータベースと調整するためにすべてのマジックが発生する場所です。</span><span class="sxs-lookup"><span data-stu-id="e45d5-199">The implementation of the Commit method for a real unit of work is where all the magic happens to reconcile in-memory changes with the database.</span></span> 

<span data-ttu-id="e45d5-200">IUnitOfWork 参照を指定すると、コードは 1 つ以上のリポジトリから取得したビジネス オブジェクトを変更し、アトミック コミット操作を使用してすべての変更を保存できます。</span><span class="sxs-lookup"><span data-stu-id="e45d5-200">Given an IUnitOfWork reference, code can make changes to business objects retrieved from one or more repositories and save all the changes using the atomic Commit operation.</span></span>

``` csharp
    var firstEmployee = unitofWork.Employees.FindById(1);
    var firstCustomer = unitofWork.Customers.FindById(1);
    firstEmployee.Name = "Alex";
    firstCustomer.Name = "Christopher";
    unitofWork.Commit();
```

### <a name="the-lazy-load-pattern"></a><span data-ttu-id="e45d5-201">レイジー ロード パターン</span><span class="sxs-lookup"><span data-stu-id="e45d5-201">The Lazy Load Pattern</span></span>

<span data-ttu-id="e45d5-202">ファウラーは、名前の遅延ロードを使用して、「必要なデータをすべて含むが、それを取得する方法を知っているオブジェクト」を記述します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-202">Fowler uses the name lazy load to describe “an object that doesn’t contain all of the data you need but knows how to get it”.</span></span> <span data-ttu-id="e45d5-203">透過的な遅延読み込みは、テスト可能なビジネス コードを記述し、リレーショナル データベースを操作するときに重要な機能です。</span><span class="sxs-lookup"><span data-stu-id="e45d5-203">Transparent lazy loading is an important feature to have when writing testable business code and working with a relational database.</span></span> <span data-ttu-id="e45d5-204">例として、次のコードを考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="e45d5-204">As an example, consider the following code.</span></span>

``` csharp
    var employee = repository.FindById(id);
    // ... and later ...
    foreach(var timeCard in employee.TimeCards) {
        // .. manipulate the timeCard
    }
```

<span data-ttu-id="e45d5-205">タイムカード コレクションはどのように設定されますか?</span><span class="sxs-lookup"><span data-stu-id="e45d5-205">How is the TimeCards collection populated?</span></span> <span data-ttu-id="e45d5-206">2つの答えがあります。</span><span class="sxs-lookup"><span data-stu-id="e45d5-206">There are two possible answers.</span></span> <span data-ttu-id="e45d5-207">1 つの回答は、従業員リポジトリが、従業員を取得するように求められた場合、従業員の関連付けられたタイム カード情報と共に、両方の従業員を取得するクエリを発行することです。</span><span class="sxs-lookup"><span data-stu-id="e45d5-207">One answer is that the employee repository, when asked to fetch an employee, issues a query to retrieve both the employee along with the employee’s associated time card information.</span></span> <span data-ttu-id="e45d5-208">リレーショナル データベースでは、通常、このクエリに JOIN 句が必要であり、アプリケーションのニーズよりも多くの情報を取得する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="e45d5-208">In relational databases this generally requires a query with a JOIN clause and may result in retrieving more information than an application needs.</span></span> <span data-ttu-id="e45d5-209">アプリケーションが TimeCards プロパティに触れる必要がな場合はどうでしょうか?</span><span class="sxs-lookup"><span data-stu-id="e45d5-209">What if the application never needs to touch the TimeCards property?</span></span>

<span data-ttu-id="e45d5-210">2 つ目の答えは、TimeCards プロパティを "オンデマンド" で読み込むことです。</span><span class="sxs-lookup"><span data-stu-id="e45d5-210">A second answer is to load the TimeCards property “on demand”.</span></span> <span data-ttu-id="e45d5-211">この遅延読み込みは、タイム カード情報を取得するための特別な API を呼び出さないため、ビジネス ロジックに対して暗黙的かつ透過的です。</span><span class="sxs-lookup"><span data-stu-id="e45d5-211">This lazy loading is implicit and transparent to the business logic because the code does not invoke special APIs to retrieve time card information.</span></span> <span data-ttu-id="e45d5-212">このコードでは、必要に応じてタイムカード情報が存在することを前提としています。</span><span class="sxs-lookup"><span data-stu-id="e45d5-212">The code assumes the time card information is present when needed.</span></span> <span data-ttu-id="e45d5-213">一般的にメソッド呼び出しの実行時のインターセプトを伴う遅延読み込みに関するいくつかの魔法があります。</span><span class="sxs-lookup"><span data-stu-id="e45d5-213">There is some magic involved with lazy loading that generally involves runtime interception of method invocations.</span></span> <span data-ttu-id="e45d5-214">インターセプト コードは、ビジネス ロジックをビジネス ロジックに自由に残しながら、データベースとの通信とタイム カード情報の取得を担当します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-214">The intercepting code is responsible for talking to the database and retrieving time card information while leaving the business logic free to be business logic.</span></span> <span data-ttu-id="e45d5-215">この遅延ロードマジックにより、ビジネスコードはデータ取得操作から分離され、よりテスト可能なコードが得られます。</span><span class="sxs-lookup"><span data-stu-id="e45d5-215">This lazy load magic allows the business code to isolate itself from data retrieval operations and results in more testable code.</span></span>

<span data-ttu-id="e45d5-216">遅延読み込みの欠点は *、アプリケーションが*タイム カード情報を必要とする場合に、コードが追加のクエリを実行することです。</span><span class="sxs-lookup"><span data-stu-id="e45d5-216">The drawback to a lazy load is that when an application *does* need the time card information the code will execute an additional query.</span></span> <span data-ttu-id="e45d5-217">これは多くのアプリケーションにとって問題ではありませんが、パフォーマンスに敏感なアプリケーションやアプリケーションが多数の従業員オブジェクトをループし、ループの各反復 (N+1 クエリの問題と呼ばれる問題) のたびにタイム カードを取得するクエリを実行する場合、遅延読み込みはドラッグです。</span><span class="sxs-lookup"><span data-stu-id="e45d5-217">This isn’t a concern for many applications, but for performance sensitive applications or applications looping through a number of employee objects and executing a query to retrieve time cards during each iteration of the loop (a problem often referred to as the N+1 query problem), lazy loading is a drag.</span></span> <span data-ttu-id="e45d5-218">このようなシナリオでは、アプリケーションは、可能な限り最も効率的な方法でタイム カード情報を熱心に読み込みたい場合があります。</span><span class="sxs-lookup"><span data-stu-id="e45d5-218">In these scenarios an application might want to eagerly load time card information in the most efficient manner possible.</span></span>

<span data-ttu-id="e45d5-219">さいわい、次のセクションに進んでこれらのパターンを実装する場合、EF4 が暗黙の遅延読み込みと効率的な一括読み込みの両方をサポートする方法を見ていきます。</span><span class="sxs-lookup"><span data-stu-id="e45d5-219">Fortunately, we’ll see how EF4 supports both implicit lazy loads and efficient eager loads as we move into the next section and implement these patterns.</span></span>

## <a name="implementing-patterns-with-the-entity-framework"></a><span data-ttu-id="e45d5-220">エンティティ フレームワークを使用したパターンの実装</span><span class="sxs-lookup"><span data-stu-id="e45d5-220">Implementing Patterns with the Entity Framework</span></span>

<span data-ttu-id="e45d5-221">最後のセクションで説明したすべての設計パターンは、EF4 で簡単に実装できるということです。</span><span class="sxs-lookup"><span data-stu-id="e45d5-221">The good news is that all of the design patterns we described in the last section are straightforward to implement with EF4.</span></span> <span data-ttu-id="e45d5-222">デモンストレーションを行うために、シンプルなASP.NET MVC アプリケーションを使用して、Employees と関連付けられたタイム カード情報を編集および表示します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-222">To demonstrate we are going to use a simple ASP.NET MVC application to edit and display Employees and their associated time card information.</span></span> <span data-ttu-id="e45d5-223">まず、次の "プレーンな古い CLR オブジェクト" (POKO) を使用します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-223">We’ll start by using the following “plain old CLR objects” (POCOs).</span></span> 

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

<span data-ttu-id="e45d5-224">これらのクラス定義は、EF4 のさまざまなアプローチや機能を検討するに伴って若干変更されますが、その目的は、これらのクラスを可能な限り永続化無知 (PI) として維持することです。</span><span class="sxs-lookup"><span data-stu-id="e45d5-224">These class definitions will change slightly as we explore different approaches and features of EF4, but the intent is to keep these classes as persistence ignorant (PI) as possible.</span></span> <span data-ttu-id="e45d5-225">PI オブジェクトは、保持している状態がデータベース内に格納されている場合でも、*どのように*、または、データベース内に配置されている*のかを*知りません。</span><span class="sxs-lookup"><span data-stu-id="e45d5-225">A PI object doesn’t know *how*, or even *if*, the state it holds lives inside a database.</span></span> <span data-ttu-id="e45d5-226">PI と POCO は、テスト可能なソフトウェアと共に手をつないで行きます。</span><span class="sxs-lookup"><span data-stu-id="e45d5-226">PI and POCOs go hand in hand with testable software.</span></span> <span data-ttu-id="e45d5-227">POCO アプローチを使用するオブジェクトは、データベースがなくても動作できるため、制約が少なく、柔軟性が高く、テストが容易です。</span><span class="sxs-lookup"><span data-stu-id="e45d5-227">Objects using a POCO approach are less constrained, more flexible, and easier to test because they can operate without a database present.</span></span>

<span data-ttu-id="e45d5-228">POC を配置すると、Visual Studio でエンティティ データ モデル (EDM) を作成できます (図 1 参照)。</span><span class="sxs-lookup"><span data-stu-id="e45d5-228">With the POCOs in place we can create an Entity Data Model (EDM) in Visual Studio (see figure 1).</span></span> <span data-ttu-id="e45d5-229">EDM を使用してエンティティのコードを生成しません。</span><span class="sxs-lookup"><span data-stu-id="e45d5-229">We will not use the EDM to generate code for our entities.</span></span> <span data-ttu-id="e45d5-230">代わりに、私たちは愛情を込めて手作りするエンティティを使用したいと考えています。</span><span class="sxs-lookup"><span data-stu-id="e45d5-230">Instead, we want to use the entities we lovingly craft by hand.</span></span> <span data-ttu-id="e45d5-231">EDM を使用してデータベース スキーマを生成し、EF4 がオブジェクトをデータベースにマップするために必要なメタデータを提供します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-231">We will only use the EDM to generate our database schema and provide the metadata EF4 needs to map objects into the database.</span></span>

![ef test_01](~/ef6/media/eftest-01.jpg)

<span data-ttu-id="e45d5-233">**図 1**</span><span class="sxs-lookup"><span data-stu-id="e45d5-233">**Figure 1**</span></span>

<span data-ttu-id="e45d5-234">注: 最初に EDM モデルを開発する場合は、EDM からクリーンな POCO コードを生成することができます。</span><span class="sxs-lookup"><span data-stu-id="e45d5-234">Note: if you want to develop the EDM model first, it is possible to generate clean, POCO code from the EDM.</span></span> <span data-ttu-id="e45d5-235">これは、データ プログラミング チームが提供する Visual Studio 2010 拡張機能を使用して行うことができます。</span><span class="sxs-lookup"><span data-stu-id="e45d5-235">You can do this with a Visual Studio 2010 extension provided by the Data Programmability team.</span></span> <span data-ttu-id="e45d5-236">拡張機能をダウンロードするには、Visual Studio の [ツール] メニューから拡張機能マネージャーを起動し、テンプレートのオンライン ギャラリーで "POCO" を検索します (図 2 参照)。</span><span class="sxs-lookup"><span data-stu-id="e45d5-236">To download the extension, launch the Extension Manager from the Tools menu in Visual Studio and search the online gallery of templates for “POCO” (See figure 2).</span></span> <span data-ttu-id="e45d5-237">EF にはいくつかの POCO テンプレートがあります。</span><span class="sxs-lookup"><span data-stu-id="e45d5-237">There are several POCO templates available for EF.</span></span> <span data-ttu-id="e45d5-238">テンプレートの使用方法の詳細については、「[チュートリアル: エンティティ フレームワークの POCO テンプレート](https://docs.microsoft.com/archive/blogs/adonet/walkthrough-poco-template-for-the-entity-framework)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="e45d5-238">For more information on using the template, see “ [Walkthrough: POCO Template for the Entity Framework](https://docs.microsoft.com/archive/blogs/adonet/walkthrough-poco-template-for-the-entity-framework)”.</span></span>

![ef test_02](~/ef6/media/eftest-02.png)

<span data-ttu-id="e45d5-240">**図 2**</span><span class="sxs-lookup"><span data-stu-id="e45d5-240">**Figure 2**</span></span>

<span data-ttu-id="e45d5-241">この POCO の開始点から、テスト可能なコードに対する 2 つの異なるアプローチを探ります。</span><span class="sxs-lookup"><span data-stu-id="e45d5-241">From this POCO starting point we will explore two different approaches to testable code.</span></span> <span data-ttu-id="e45d5-242">最初のアプローチでは、作業単位とリポジトリを実装するために Entity Framework API からの抽象化を活用するため、EF アプローチを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-242">The first approach I call the EF approach because it leverages abstractions from the Entity Framework API to implement units of work and repositories.</span></span> <span data-ttu-id="e45d5-243">2 番目のアプローチでは、独自のカスタム リポジトリの抽象化を作成し、各アプローチの長所と短所を確認します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-243">In the second approach we will create our own custom repository abstractions and then see the advantages and disadvantages of each approach.</span></span> <span data-ttu-id="e45d5-244">まず、EF アプローチを見ていきます。</span><span class="sxs-lookup"><span data-stu-id="e45d5-244">We’ll start by exploring the EF approach.</span></span>  

### <a name="an-ef-centric-implementation"></a><span data-ttu-id="e45d5-245">EF セントリック実装</span><span class="sxs-lookup"><span data-stu-id="e45d5-245">An EF Centric Implementation</span></span>

<span data-ttu-id="e45d5-246">mvc プロジェクトのASP.NETから次のコントローラー アクションを検討してください。</span><span class="sxs-lookup"><span data-stu-id="e45d5-246">Consider the following controller action from an ASP.NET MVC project.</span></span> <span data-ttu-id="e45d5-247">このアクションは、Employee オブジェクトを取得し、結果を返して従業員の詳細ビューを表示します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-247">The action retrieves an Employee object and returns a result to display a detailed view of the employee.</span></span>

``` csharp
    public ViewResult Details(int id) {
        var employee = _unitOfWork.Employees
                                  .Single(e => e.Id == id);
        return View(employee);
    }
```

<span data-ttu-id="e45d5-248">コードはテスト可能ですか?</span><span class="sxs-lookup"><span data-stu-id="e45d5-248">Is the code testable?</span></span> <span data-ttu-id="e45d5-249">アクションの動作を検証するには、少なくとも 2 つのテストがあります。</span><span class="sxs-lookup"><span data-stu-id="e45d5-249">There are at least two tests we’d need to verify the action’s behavior.</span></span> <span data-ttu-id="e45d5-250">まず、アクションが正しいビューを返すことを確認します 。</span><span class="sxs-lookup"><span data-stu-id="e45d5-250">First, we’d like to verify the action returns the correct view – an easy test.</span></span> <span data-ttu-id="e45d5-251">また、アクションが正しい従業員を取得することを確認するテストを記述し、データベースを照会するコードを実行せずにこれを行いたいと思います。</span><span class="sxs-lookup"><span data-stu-id="e45d5-251">We’d also want to write a test to verify the action retrieves the correct employee, and we’d like to do this without executing code to query the database.</span></span> <span data-ttu-id="e45d5-252">テスト対象のコードを分離する必要があることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="e45d5-252">Remember we want to isolate the code under test.</span></span> <span data-ttu-id="e45d5-253">分離により、データ アクセス コードまたはデータベース構成のバグが原因でテストが失敗しないようにします。</span><span class="sxs-lookup"><span data-stu-id="e45d5-253">Isolation will ensure the test doesn’t fail because of a bug in the data access code or database configuration.</span></span> <span data-ttu-id="e45d5-254">テストが失敗した場合、コントローラロジックにバグがあり、下位レベルのシステムコンポーネントにはバグがないことがわかります。</span><span class="sxs-lookup"><span data-stu-id="e45d5-254">If the test fails, we will know we have a bug in the controller logic, and not in some lower level system component.</span></span>

<span data-ttu-id="e45d5-255">分離を実現するには、リポジトリや作業単位に関して先ほど説明したインターフェイスのような抽象化が必要になります。</span><span class="sxs-lookup"><span data-stu-id="e45d5-255">To achieve isolation we’ll need some abstractions like the interfaces we presented earlier for repositories and units of work.</span></span> <span data-ttu-id="e45d5-256">リポジトリ パターンは、ドメイン オブジェクトとデータ マッピング レイヤの間を仲介するように設計されています。</span><span class="sxs-lookup"><span data-stu-id="e45d5-256">Remember the repository pattern is designed to mediate between domain objects and the data mapping layer.</span></span> <span data-ttu-id="e45d5-257">このシナリオでは、EF4*は*データ マッピング レイヤーであり、既に (System.Data.Objects&lt;&gt;名前空間から) IObjectSet T という名前のリポジトリのような抽象化を提供しています。</span><span class="sxs-lookup"><span data-stu-id="e45d5-257">In this scenario EF4 *is* the data mapping layer, and already provides a repository-like abstraction named IObjectSet&lt;T&gt; (from the System.Data.Objects namespace).</span></span> <span data-ttu-id="e45d5-258">インターフェイス定義は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="e45d5-258">The interface definition looks like the following.</span></span>

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

<span data-ttu-id="e45d5-259">IObjectSet&lt;&gt; T は、オブジェクトのコレクションに似ており (IEnumerable&lt;T&gt;を使用)、シミュレートされたコレクションからオブジェクトを追加および削除するメソッドを提供するため、リポジトリの要件を満たしています。</span><span class="sxs-lookup"><span data-stu-id="e45d5-259">IObjectSet&lt;T&gt; meets the requirements for a repository because it resembles a collection of objects (via IEnumerable&lt;T&gt;) and provides methods to add and remove objects from the simulated collection.</span></span> <span data-ttu-id="e45d5-260">アタッチ と デタッチ メソッドは、EF4 API の追加機能を公開します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-260">The Attach and Detach methods expose additional capabilities of the EF4 API.</span></span> <span data-ttu-id="e45d5-261">IObjectSet&lt;T&gt;をリポジトリのインターフェイスとして使用するには、リポジトリをバインドするための作業抽象化単位が必要です。</span><span class="sxs-lookup"><span data-stu-id="e45d5-261">To use IObjectSet&lt;T&gt; as the interface for repositories we need a unit of work abstraction to bind repositories together.</span></span>

``` csharp
    public interface IUnitOfWork {
        IObjectSet<Employee> Employees { get; }
        IObjectSet<TimeCard> TimeCards { get; }
        void Commit();
    }
```

<span data-ttu-id="e45d5-262">このインターフェイスの具体的な実装の 1 つは SQL Server と話し合い、EF4 から ObjectContext クラスを使用して簡単に作成できます。</span><span class="sxs-lookup"><span data-stu-id="e45d5-262">One concrete implementation of this interface will talk to SQL Server and is easy to create using the ObjectContext class from EF4.</span></span> <span data-ttu-id="e45d5-263">ObjectContext クラスは、EF4 API の実際の作業単位です。</span><span class="sxs-lookup"><span data-stu-id="e45d5-263">The ObjectContext class is the real unit of work in the EF4 API.</span></span>

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

<span data-ttu-id="e45d5-264">オブジェクトを&lt;作成するのと&gt;同じくらい簡単に、オブジェクトのオブジェクトセットのメソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-264">Bringing an IObjectSet&lt;T&gt; to life is as easy as invoking the CreateObjectSet method of the ObjectContext object.</span></span> <span data-ttu-id="e45d5-265">舞台裏では、フレームワークは EDM で提供されたメタデータを使用して、具体的な ObjectSet&lt;&gt;T を生成します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-265">Behind the scenes the framework will use the metadata we provided in the EDM to produce a concrete ObjectSet&lt;T&gt;.</span></span> <span data-ttu-id="e45d5-266">クライアント コードでのテストの容易性を&lt;維持&gt;するために IObjectSet T インターフェイスを返すことに固執します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-266">We’ll stick with returning the IObjectSet&lt;T&gt; interface because it will help preserve testability in client code.</span></span>

<span data-ttu-id="e45d5-267">この具体的な実装は実稼働環境で役立ちますが、テストを容易にするために IUnitOfWork の抽象化を使用する方法に焦点を当てる必要があります。</span><span class="sxs-lookup"><span data-stu-id="e45d5-267">This concrete implementation is useful in production, but we need to focus on how we’ll use our IUnitOfWork abstraction to facilitate testing.</span></span>

### <a name="the-test-doubles"></a><span data-ttu-id="e45d5-268">テストダブルス</span><span class="sxs-lookup"><span data-stu-id="e45d5-268">The Test Doubles</span></span>

<span data-ttu-id="e45d5-269">コントローラーアクションを分離するには、実際の作業単位 (ObjectContext によってバックアップ) とテストの二重または「偽の」作業単位 (インメモリ操作の実行) を切り替える機能が必要になります。</span><span class="sxs-lookup"><span data-stu-id="e45d5-269">To isolate the controller action we’ll need the ability to switch between the real unit of work (backed by an ObjectContext) and a test double or “fake” unit of work (performing in-memory operations).</span></span> <span data-ttu-id="e45d5-270">この種類の切り替えを実行する一般的な方法は、MVC コントローラーが作業単位をインスタンス化するのではなく、作業単位をコンストラクター パラメーターとしてコントローラーに渡すことです。</span><span class="sxs-lookup"><span data-stu-id="e45d5-270">The common approach to perform this type of switching is to not let the MVC controller instantiate a unit of work, but instead pass the unit of work into the controller as a constructor parameter.</span></span>

``` csharp
    class EmployeeController : Controller {
      publicEmployeeController(IUnitOfWork unitOfWork)  {
          _unitOfWork = unitOfWork;
      }
      ...
    }
```

<span data-ttu-id="e45d5-271">上記のコードは、依存関係の挿入の例です。</span><span class="sxs-lookup"><span data-stu-id="e45d5-271">The above code is an example of dependency injection.</span></span> <span data-ttu-id="e45d5-272">コントローラーが依存関係 (作業単位) を作成することは許可しませんが、その依存関係をコントローラーに挿入します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-272">We don’t allow the controller to create it’s dependency (the unit of work) but inject the dependency into the controller.</span></span> <span data-ttu-id="e45d5-273">MVC プロジェクトでは、カスタム コント ローラー ファクトリを、依存関係の挿入を自動化する制御 (IoC) コンテナーの反転と組み合わせて使用する一般的です。</span><span class="sxs-lookup"><span data-stu-id="e45d5-273">In an MVC project it is common to use a custom controller factory in combination with an inversion of control (IoC) container to automate dependency injection.</span></span> <span data-ttu-id="e45d5-274">これらのトピックは、この記事の範囲外ですが、この記事の最後に記載されている参考資料を参照して、詳細を参照できます。</span><span class="sxs-lookup"><span data-stu-id="e45d5-274">These topics are beyond the scope of this article, but you can read more by following the references at the end of this article.</span></span>

<span data-ttu-id="e45d5-275">テストに使用できる偽の作業単位の実装は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="e45d5-275">A fake unit of work implementation that we can use for testing might look like the following.</span></span>

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

<span data-ttu-id="e45d5-276">偽の作業単位がコミットされたプロパティを公開していることに注目してください。</span><span class="sxs-lookup"><span data-stu-id="e45d5-276">Notice the fake unit of work exposes a Commited property.</span></span> <span data-ttu-id="e45d5-277">テストを容易にする偽のクラスに機能を追加すると便利な場合があります。</span><span class="sxs-lookup"><span data-stu-id="e45d5-277">It’s sometimes useful to add features to a fake class that facilitate testing.</span></span> <span data-ttu-id="e45d5-278">この場合、Commited プロパティをチェックすることによって、コードが作業単位をコミットしているかどうかは簡単に確認できます。</span><span class="sxs-lookup"><span data-stu-id="e45d5-278">In this case it is easy to observe if code commits a unit of work by checking the Commited property.</span></span>

<span data-ttu-id="e45d5-279">また、従業員オブジェクトとタイムカードオブジェクトをメモリ&lt;に&gt;保持するには、偽のIObjectSet Tが必要になります。</span><span class="sxs-lookup"><span data-stu-id="e45d5-279">We’ll also need a fake IObjectSet&lt;T&gt; to hold Employee and TimeCard objects in memory.</span></span> <span data-ttu-id="e45d5-280">ジェネリックを使用して単一の実装を提供できます。</span><span class="sxs-lookup"><span data-stu-id="e45d5-280">We can provide a single implementation using generics.</span></span>

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

<span data-ttu-id="e45d5-281">このテストは、その作業のほとんどを基になる HashSet&lt;T&gt;オブジェクトに委任します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-281">This test double delegates most of its work to an underlying HashSet&lt;T&gt; object.</span></span> <span data-ttu-id="e45d5-282">IObjectSet&lt;T&gt;には T をクラス (参照型) として適用するジェネリック制約が必要であり、また&lt;IQueryable T&gt;を実装するように強制されることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="e45d5-282">Note that IObjectSet&lt;T&gt; requires a generic constraint enforcing T as a class (a reference type), and also forces us to implement IQueryable&lt;T&gt;.</span></span> <span data-ttu-id="e45d5-283">標準 LINQ 演算子 AsQueryable を使用して、メモリ内&lt;コレクション&gt;を IQueryable T として表示するのは簡単です。</span><span class="sxs-lookup"><span data-stu-id="e45d5-283">It is easy to make an in-memory collection appear as an IQueryable&lt;T&gt; using the standard LINQ operator AsQueryable.</span></span>

### <a name="the-tests"></a><span data-ttu-id="e45d5-284">テスト</span><span class="sxs-lookup"><span data-stu-id="e45d5-284">The Tests</span></span>

<span data-ttu-id="e45d5-285">従来の単体テストでは、1 つのテスト クラスを使用して、すべてのアクションのすべてのテストを 1 つの MVC コントローラーに保持します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-285">Traditional unit tests will use a single test class to hold all of the tests for all of the actions in a single MVC controller.</span></span> <span data-ttu-id="e45d5-286">これらのテスト、またはあらゆる種類の単体テストを、私たちが構築したメモリ内の偽物を使用して書くことができます。</span><span class="sxs-lookup"><span data-stu-id="e45d5-286">We can write these tests, or any type of unit test, using the in memory fakes we’ve built.</span></span> <span data-ttu-id="e45d5-287">ただし、この記事では、モノリシック テスト クラスアプローチを避け、代わりに特定の機能に焦点を当てるためにテストをグループ化します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-287">However, for this article we will avoid the monolithic test class approach and instead group our tests to focus on a specific piece of functionality.</span></span><span data-ttu-id="e45d5-288">たとえば、"新しい従業員の作成" はテスト対象の機能である場合があるため、1 つのテスト クラスを使用して、新しい従業員の作成を担当する単一のコントローラー アクションを検証します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-288">  For example, “create new employee” might be the functionality we want to test, so we will use a single test class to verify the single controller action responsible for creating a new employee.</span></span>

<span data-ttu-id="e45d5-289">これらの細かいテストクラスすべてに必要な一般的なセットアップコードがいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="e45d5-289">There is some common setup code we need for all these fine grained test classes.</span></span> <span data-ttu-id="e45d5-290">たとえば、常にインメモリリポジトリと偽の作業単位を作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e45d5-290">For example, we always need to create our in-memory repositories and fake unit of work.</span></span> <span data-ttu-id="e45d5-291">また、偽の作業単位が挿入された従業員コントローラのインスタンスも必要です。</span><span class="sxs-lookup"><span data-stu-id="e45d5-291">We also need an instance of the employee controller with the fake unit of work injected.</span></span> <span data-ttu-id="e45d5-292">この共通セットアップ コードは、基本クラスを使用してテスト クラス間で共有します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-292">We’ll share this common setup code across test classes by using a base class.</span></span>

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

<span data-ttu-id="e45d5-293">基本クラスで使用する "オブジェクトマザー" は、テストデータを作成するための一般的なパターンの 1 つです。</span><span class="sxs-lookup"><span data-stu-id="e45d5-293">The “object mother” we use in the base class is one common pattern for creating test data.</span></span> <span data-ttu-id="e45d5-294">オブジェクトの母には、複数のテスト器具で使用するためにテストエンティティをインスタンス化するファクトリメソッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="e45d5-294">An object mother contains factory methods to instantiate test entities for use across multiple test fixtures.</span></span>

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

<span data-ttu-id="e45d5-295">いくつかのテスト備品の基本クラスとして EmployeeControllerTestBase を使用できます (図 3 を参照)。</span><span class="sxs-lookup"><span data-stu-id="e45d5-295">We can use the EmployeeControllerTestBase as the base class for a number of test fixtures (see figure 3).</span></span> <span data-ttu-id="e45d5-296">各テストフィクスチャは、特定のコントローラーアクションをテストします。</span><span class="sxs-lookup"><span data-stu-id="e45d5-296">Each test fixture will test a specific controller action.</span></span> <span data-ttu-id="e45d5-297">たとえば、あるテストフィクスチャは、HTTP GET 要求 (従業員を作成するためのビューを表示する) で使用される Create アクションのテストに焦点を当て、別の備品は HTTP POST 要求で使用される Create アクション (ユーザーが送信した情報を取得して従業員を作成する) に焦点を当てます。</span><span class="sxs-lookup"><span data-stu-id="e45d5-297">For example, one test fixture will focus on testing the Create action used during an HTTP GET request (to display the view for creating an employee), and a different fixture will focus on the Create action used in an HTTP POST request (to take information submitted by the user to create an employee).</span></span> <span data-ttu-id="e45d5-298">各派生クラスは、特定のコンテキストで必要なセットアップと、その特定のテスト コンテキストの結果を検証するために必要なアサーションを提供する場合にのみ責任を負います。</span><span class="sxs-lookup"><span data-stu-id="e45d5-298">Each derived class is only responsible for the setup needed in its specific context, and to provide the assertions needed to verify the outcomes for its specific test context.</span></span>

![ef test_03](~/ef6/media/eftest-03.png)

<span data-ttu-id="e45d5-300">**図 3**</span><span class="sxs-lookup"><span data-stu-id="e45d5-300">**Figure 3**</span></span>

<span data-ttu-id="e45d5-301">ここで示す命名規則とテストスタイルは、テスト可能なコードには必要ではなく、1 つのアプローチにすぎません。</span><span class="sxs-lookup"><span data-stu-id="e45d5-301">The naming convention and test style presented here isn’t required for testable code – it’s just one approach.</span></span> <span data-ttu-id="e45d5-302">図 4 は、Visual Studio 2010 のジェット ブレイン Resharper テスト ランナー プラグインで実行されているテストを示しています。</span><span class="sxs-lookup"><span data-stu-id="e45d5-302">Figure 4 shows the tests running in the Jet Brains Resharper test runner plugin for Visual Studio 2010.</span></span>

![ef test_04](~/ef6/media/eftest-04.png)

<span data-ttu-id="e45d5-304">**図 4**</span><span class="sxs-lookup"><span data-stu-id="e45d5-304">**Figure 4**</span></span>

<span data-ttu-id="e45d5-305">共有セットアップ コードを処理する基本クラスを使用すると、各コントローラー アクションの単体テストは小さく、書き込みが簡単です。</span><span class="sxs-lookup"><span data-stu-id="e45d5-305">With a base class to handle the shared setup code, the unit tests for each controller action are small and easy to write.</span></span> <span data-ttu-id="e45d5-306">テストは(メモリ内操作を実行しているので)迅速に実行され、無関係なインフラストラクチャまたは環境上の懸念(テスト中のユニットを分離したため)のために失敗すべきではありません。</span><span class="sxs-lookup"><span data-stu-id="e45d5-306">The tests will execute quickly (since we are performing in-memory operations), and shouldn’t fail because of unrelated infrastructure or environmental concerns (because we’ve isolated the unit under test).</span></span>

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

<span data-ttu-id="e45d5-307">これらのテストでは、基本クラスは、セットアップ作業のほとんどを行います。</span><span class="sxs-lookup"><span data-stu-id="e45d5-307">In these tests, the base class does most of the setup work.</span></span> <span data-ttu-id="e45d5-308">基本クラスのコンストラクターは、メモリ内リポジトリ、フェイク作業単位、および EmployeeController クラスのインスタンスを作成します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-308">Remember the base class constructor creates the in-memory repository, a fake unit of work, and an instance of the EmployeeController class.</span></span> <span data-ttu-id="e45d5-309">テスト クラスはこの基本クラスから派生し、Create メソッドのテストの詳細に焦点を当てています。</span><span class="sxs-lookup"><span data-stu-id="e45d5-309">The test class derives from this base class and focuses on the specifics of testing the Create method.</span></span> <span data-ttu-id="e45d5-310">この場合、詳細は、単体テストの手順で示される「配置、行動、およびアサート」の手順に煮詰まります。</span><span class="sxs-lookup"><span data-stu-id="e45d5-310">In this case the specifics boil down to the “arrange, act, and assert” steps you’ll see in any unit testing procedure:</span></span>

-   <span data-ttu-id="e45d5-311">受信データをシミュレートする newEmployee オブジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-311">Create a newEmployee object to simulate incoming data.</span></span>
-   <span data-ttu-id="e45d5-312">従業員のコント ローラーの作成アクションを呼び出し、newEmployee を渡します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-312">Invoke the Create action of the EmployeeController and pass in the newEmployee.</span></span>
-   <span data-ttu-id="e45d5-313">[作成] アクションによって期待される結果が生成されていることを確認します (従業員はリポジトリに表示されます)。</span><span class="sxs-lookup"><span data-stu-id="e45d5-313">Verify the Create action produces the expected results (the employee appears in the repository).</span></span>

<span data-ttu-id="e45d5-314">私たちが構築したものは、EmployeeControllerアクションのいずれかをテストすることができます。</span><span class="sxs-lookup"><span data-stu-id="e45d5-314">What we’ve built allows us to test any of the EmployeeController actions.</span></span> <span data-ttu-id="e45d5-315">たとえば、Employee コントローラのインデックス アクションのテストを記述する場合、テストベース クラスから継承して、テストに対して同じ基本設定を確立できます。</span><span class="sxs-lookup"><span data-stu-id="e45d5-315">For example, when we write tests for the Index action of the Employee controller we can inherit from the test base class to establish the same base setup for our tests.</span></span> <span data-ttu-id="e45d5-316">再び、基本クラスは、メモリ内リポジトリ、偽の作業単位、および EmployeeController のインスタンスを作成します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-316">Again the base class will create the in-memory repository, the fake unit of work, and an instance of the EmployeeController.</span></span> <span data-ttu-id="e45d5-317">インデックス アクションのテストは、インデックス アクションの呼び出しと、アクションが返すモデルの品質のテストだけに集中する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e45d5-317">The tests for the Index action only need to focus on invoking the Index action and testing the qualities of the model the action returns.</span></span>

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

<span data-ttu-id="e45d5-318">メモリ内の偽物で作成しているテストは、ソフトウェアの*状態*をテストする方向に向けられている.</span><span class="sxs-lookup"><span data-stu-id="e45d5-318">The tests we are creating with in-memory fakes are oriented towards testing the *state* of the software.</span></span> <span data-ttu-id="e45d5-319">たとえば、Create アクションをテストする場合、作成アクションの実行後にリポジトリの状態を検査する必要があります 。</span><span class="sxs-lookup"><span data-stu-id="e45d5-319">For example, when testing the Create action we want to inspect the state of the repository after the create action executes – does the repository hold the new employee?</span></span>

``` csharp
    [TestMethod]
    public void ShouldAddNewEmployeeToRepository() {
        _controller.Create(_newEmployee);
        Assert.IsTrue(_repository.Contains(_newEmployee));
    }
```

<span data-ttu-id="e45d5-320">後で、相互作用ベースのテストについて説明します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-320">Later we’ll look at interaction based testing.</span></span> <span data-ttu-id="e45d5-321">相互作用ベースのテストでは、テスト対象のコードがオブジェクトの適切なメソッドを呼び出し、正しいパラメータを渡したかどうかを尋ねます。</span><span class="sxs-lookup"><span data-stu-id="e45d5-321">Interaction based testing will ask if the code under test invoked the proper methods on our objects and passed the correct parameters.</span></span> <span data-ttu-id="e45d5-322">ここでは、別のデザイン パターンであるレイジー ロードをカバーに移動します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-322">For now we’ll move on the cover another design pattern – the lazy load.</span></span>

## <a name="eager-loading-and-lazy-loading"></a><span data-ttu-id="e45d5-323">一括読み込みと遅延読み込み</span><span class="sxs-lookup"><span data-stu-id="e45d5-323">Eager Loading and Lazy Loading</span></span>

<span data-ttu-id="e45d5-324">ASP.NET MVC Web アプリケーションのある時点で、従業員の情報を表示し、従業員の関連付けられたタイム カードを含めることができます。</span><span class="sxs-lookup"><span data-stu-id="e45d5-324">At some point in the ASP.NET  MVC web application we might wish to display an employee’s information and include the employee’s associated time cards.</span></span> <span data-ttu-id="e45d5-325">たとえば、従業員の名前とシステム内のタイム カードの合計数を示すタイム カードの概要表示があります。</span><span class="sxs-lookup"><span data-stu-id="e45d5-325">For example, we might have a time card summary display that shows the employee’s name and the total number of time cards in the system.</span></span> <span data-ttu-id="e45d5-326">この機能を実装するには、いくつかの方法があります。</span><span class="sxs-lookup"><span data-stu-id="e45d5-326">There are several approaches we can take to implement this feature.</span></span>

### <a name="projection"></a><span data-ttu-id="e45d5-327">射影</span><span class="sxs-lookup"><span data-stu-id="e45d5-327">Projection</span></span>

<span data-ttu-id="e45d5-328">サマリーを作成する簡単な方法の 1 つは、ビューに表示する情報専用のモデルを構築することです。</span><span class="sxs-lookup"><span data-stu-id="e45d5-328">One easy approach to create the summary is to construct a model dedicated to the information we want to display in the view.</span></span> <span data-ttu-id="e45d5-329">このシナリオでは、モデルは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="e45d5-329">In this scenario the model might look like the following.</span></span>

``` csharp
    public class EmployeeSummaryViewModel {
        public string Name { get; set; }
        public int TotalTimeCards { get; set; }
    }
```

<span data-ttu-id="e45d5-330">EmployeeSummaryViewModel はエンティティではなく、データベースに保持する必要があるものではありません。</span><span class="sxs-lookup"><span data-stu-id="e45d5-330">Note that the EmployeeSummaryViewModel is not an entity – in other words it is not something we want to persist in the database.</span></span> <span data-ttu-id="e45d5-331">厳密に型指定された方法でビューにデータをシャッフルするには、このクラスを使用します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-331">We are only going to use this class to shuffle data into the view in a strongly typed manner.</span></span> <span data-ttu-id="e45d5-332">ビュー モデルには動作 (メソッドなし) が含まれるので、ビュー モデルはデータ転送オブジェクト (DTO) と似ています。</span><span class="sxs-lookup"><span data-stu-id="e45d5-332">The view model is like a data transfer object (DTO) because it contains no behavior (no methods) – only properties.</span></span> <span data-ttu-id="e45d5-333">プロパティは、移動する必要があるデータを保持します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-333">The properties will hold the data we need to move.</span></span> <span data-ttu-id="e45d5-334">LINQ の標準投影演算子である Select 演算子を使用して、このビュー モデルを簡単にインスタンス化できます。</span><span class="sxs-lookup"><span data-stu-id="e45d5-334">It is easy to instantiate this view model using LINQ’s standard projection operator – the Select operator.</span></span>

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

<span data-ttu-id="e45d5-335">上記のコードには2つの注目すべき機能があります。</span><span class="sxs-lookup"><span data-stu-id="e45d5-335">There are two notable features to the above code.</span></span> <span data-ttu-id="e45d5-336">まず、 – コードは、観察と分離が容易であるため、テストが容易です。</span><span class="sxs-lookup"><span data-stu-id="e45d5-336">First – the code is easy to test because it is still easy to observe and isolate.</span></span> <span data-ttu-id="e45d5-337">Select演算子は、実際の作業単位に対して行うのと同様に、メモリ内の偽物に対しても同様に機能します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-337">The Select operator works just as well against our in-memory fakes as it does against the real unit of work.</span></span>

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

<span data-ttu-id="e45d5-338">2 つ目の注目すべき機能は、EF4 が従業員とタイム カードの情報を組み立てる効率的な単一のクエリを生成する方法です。</span><span class="sxs-lookup"><span data-stu-id="e45d5-338">The second notable feature is how the code allows EF4 to generate a single, efficient query to assemble employee and time card information together.</span></span> <span data-ttu-id="e45d5-339">特別な API を使用せずに、従業員情報とタイム カード情報を同じオブジェクトに読み込んでいます。</span><span class="sxs-lookup"><span data-stu-id="e45d5-339">We’ve loaded employee information and time card information into the same object without using any special APIs.</span></span> <span data-ttu-id="e45d5-340">このコードは、単に、メモリ内のデータ ソースとリモート データ ソースに対して動作する標準の LINQ 演算子を使用して必要な情報を表現しただけです。</span><span class="sxs-lookup"><span data-stu-id="e45d5-340">The code merely expressed the information it requires using standard LINQ operators that work against in-memory data sources as well as remote data sources.</span></span> <span data-ttu-id="e45d5-341">EF4 は、LINQ クエリと C\#コンパイラによって生成された式ツリーを、単一の効率的な T-SQL クエリに変換できました。</span><span class="sxs-lookup"><span data-stu-id="e45d5-341">EF4 was able to translate the expression trees generated by the LINQ query and C\# compiler into a single and efficient T-SQL query.</span></span>

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

<span data-ttu-id="e45d5-342">ビュー モデルや DTO オブジェクトを操作するのではなく、実際のエンティティで作業する必要がある場合もあります。</span><span class="sxs-lookup"><span data-stu-id="e45d5-342">There are other times when we don’t want to work with a view model or DTO object, but with real entities.</span></span> <span data-ttu-id="e45d5-343">従業員*と*従業員のタイムカードが必要だとわかったら、関連データを控えめで効率的な方法で熱心に読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="e45d5-343">When we know we need an employee *and* the employee’s time cards, we can eagerly load the related data in an unobtrusive and efficient manner.</span></span>

### <a name="explicit-eager-loading"></a><span data-ttu-id="e45d5-344">明示的な一括読み込み</span><span class="sxs-lookup"><span data-stu-id="e45d5-344">Explicit Eager Loading</span></span>

<span data-ttu-id="e45d5-345">関連エンティティ情報を熱心に読み込みたい場合は、ビジネス ロジック (またはこのシナリオではコントローラ アクション ロジック) がリポジトリに対する要望を表現するためのメカニズムが必要です。</span><span class="sxs-lookup"><span data-stu-id="e45d5-345">When we want to eagerly load related entity information we need some mechanism for business logic (or in this scenario, controller action logic) to express its desire to the repository.</span></span> <span data-ttu-id="e45d5-346">EF4 ObjectQuery&lt;&gt; T クラスは、クエリ中に取得する関連オブジェクトを指定する Include メソッドを定義します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-346">The EF4 ObjectQuery&lt;T&gt; class defines an Include method to specify the related objects to retrieve during a query.</span></span> <span data-ttu-id="e45d5-347">&lt;EF4 ObjectContext は、ObjectQuery&gt; &lt;T&gt;から継承する具体的なオブジェクト セット T クラスを介してエンティティを公開します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-347">Remember the EF4 ObjectContext exposes entities via the concrete ObjectSet&lt;T&gt; class which inherits from ObjectQuery&lt;T&gt;.</span></span><span data-ttu-id="e45d5-348">コントローラーアクションで ObjectSet&lt;&gt; T 参照を使用している場合は、次のコードを記述して、各従業員にタイム カード情報の一括読み込みを指定できます。</span><span class="sxs-lookup"><span data-stu-id="e45d5-348">  If we were using ObjectSet&lt;T&gt; references in our controller action we could write the following code to specify an eager load of time card information for each employee.</span></span>

``` csharp
    _employees.Include("TimeCards")
              .Where(e => e.HireDate.Year > 2009);
```

<span data-ttu-id="e45d5-349">しかし、コードをテスト可能にするため、実際の作業単位の外部から&lt;ObjectSet&gt; T を公開することはしません。</span><span class="sxs-lookup"><span data-stu-id="e45d5-349">However, since we are trying to keep our code testable we are not exposing ObjectSet&lt;T&gt; from outside the real unit of work class.</span></span> <span data-ttu-id="e45d5-350">代わりに、偽装しやすい IObjectSet&lt;&gt; T インターフェイスに依存していますが、IObjectSet&lt;T&gt;は Include メソッドを定義しません。</span><span class="sxs-lookup"><span data-stu-id="e45d5-350">Instead, we rely on the IObjectSet&lt;T&gt; interface which is easier to fake, but IObjectSet&lt;T&gt; does not define an Include method.</span></span> <span data-ttu-id="e45d5-351">LINQ の長所は、独自の Include 演算子を作成できることです。</span><span class="sxs-lookup"><span data-stu-id="e45d5-351">The beauty of LINQ is that we can create our own Include operator.</span></span>

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

<span data-ttu-id="e45d5-352">この&lt;Include 演算子は、IObjectSet&gt; &lt;T&gt;ではなく IQueryable T の拡張メソッドとして定義されています。</span><span class="sxs-lookup"><span data-stu-id="e45d5-352">Notice this Include operator is defined as an extension method for IQueryable&lt;T&gt; instead of IObjectSet&lt;T&gt;.</span></span> <span data-ttu-id="e45d5-353">これにより、IQueryable&lt;T 、IObjectSet T、&gt;オブジェクトクエリ&lt;&gt;&lt;T 、およびオブジェクトセット&gt;&lt;T&gt;など、より幅広い型でメソッドを使用できます。</span><span class="sxs-lookup"><span data-stu-id="e45d5-353">This gives us the ability to use the method with a wider range of possible types, including IQueryable&lt;T&gt;, IObjectSet&lt;T&gt;, ObjectQuery&lt;T&gt;, and ObjectSet&lt;T&gt;.</span></span> <span data-ttu-id="e45d5-354">基礎となるシーケンスが本物の EF4 ObjectQuery&lt;T&gt;ではない場合、害はなく、Include 演算子は no-op です。</span><span class="sxs-lookup"><span data-stu-id="e45d5-354">In the event the underlying sequence is not a genuine EF4 ObjectQuery&lt;T&gt;, then there is no harm done and the Include operator is a no-op.</span></span> <span data-ttu-id="e45d5-355">基になるシーケンス*が*ObjectQuery&lt;T&gt; (または ObjectQuery&lt;&gt;T から派生) の場合、EF4 は追加データの要件を確認し、適切な SQL クエリを作成します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-355">If the underlying sequence *is* an ObjectQuery&lt;T&gt; (or derived from ObjectQuery&lt;T&gt;), then EF4 will see our requirement for additional data and formulate the proper SQL query.</span></span>

<span data-ttu-id="e45d5-356">この新しいオペレータを配置すると、リポジトリからタイムカード情報の一括読み込みを明示的に要求できます。</span><span class="sxs-lookup"><span data-stu-id="e45d5-356">With this new operator in place we can explicitly request an eager load of time card information from the repository.</span></span>

``` csharp
    public ViewResult Index() {
        var model = _unitOfWork.Employees
                               .Include("TimeCards")
                               .OrderBy(e => e.HireDate);
        return View(model);
    }
```

<span data-ttu-id="e45d5-357">実際の ObjectContext に対して実行すると、コードは次の単一のクエリを生成します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-357">When run against a real ObjectContext, the code produces the following single query.</span></span> <span data-ttu-id="e45d5-358">このクエリは、データベースから十分な情報を収集して、従業員オブジェクトを具体化し、TimeCards プロパティを完全に設定します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-358">The query gathers enough information from the database in one trip to materialize the employee objects and fully populate their TimeCards property.</span></span>

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

<span data-ttu-id="e45d5-359">優れたニュースは、アクション メソッド内のコードが完全にテスト可能なままです。</span><span class="sxs-lookup"><span data-stu-id="e45d5-359">The great news is the code inside the action method remains fully testable.</span></span> <span data-ttu-id="e45d5-360">私たちは、Include演算子をサポートするために私たちの偽物のための追加機能を提供する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="e45d5-360">We don’t need to provide any additional features for our fakes to support the Include operator.</span></span> <span data-ttu-id="e45d5-361">悪いニュースは、永続化を無知に保ちたいというコードの中で Include 演算子を使用しなければならなかったことです。</span><span class="sxs-lookup"><span data-stu-id="e45d5-361">The bad news is we had to use the Include operator inside of the code we wanted to keep persistence ignorant.</span></span> <span data-ttu-id="e45d5-362">これは、テスト可能なコードを構築するときに評価する必要があるトレードオフのタイプの典型的な例です。</span><span class="sxs-lookup"><span data-stu-id="e45d5-362">This is a prime example of the type of tradeoffs you’ll need to evaluate when building testable code.</span></span> <span data-ttu-id="e45d5-363">パフォーマンスの目標を達成するために、リポジトリの抽象化の外部に永続性の懸念を漏らす必要がある場合があります。</span><span class="sxs-lookup"><span data-stu-id="e45d5-363">There are times when you need to let persistence concerns leak outside the repository abstraction to meet performance goals.</span></span>

<span data-ttu-id="e45d5-364">一括読み込みの代わりに、遅延読み込みがあります。</span><span class="sxs-lookup"><span data-stu-id="e45d5-364">The alternative to eager loading is lazy loading.</span></span> <span data-ttu-id="e45d5-365">遅延読み込みは、関連データの要件を明示的に通知するためにビジネス コードを必要*としないことを*意味します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-365">Lazy loading means we do *not* need our business code to explicitly announce the requirement for associated data.</span></span> <span data-ttu-id="e45d5-366">代わりに、アプリケーションでエンティティを使用し、追加のデータが必要な場合は、必要に応じて、Entity Framework がデータを読み込みます。</span><span class="sxs-lookup"><span data-stu-id="e45d5-366">Instead, we use our entities in the application and if additional data is needed Entity Framework will load the data on demand.</span></span>

### <a name="lazy-loading"></a><span data-ttu-id="e45d5-367">遅延読み込み</span><span class="sxs-lookup"><span data-stu-id="e45d5-367">Lazy Loading</span></span>

<span data-ttu-id="e45d5-368">ビジネス ロジックに必要なデータがわからないシナリオは、簡単に想像できます。</span><span class="sxs-lookup"><span data-stu-id="e45d5-368">It’s easy to imagine a scenario where we don’t know what data a piece of business logic will need.</span></span> <span data-ttu-id="e45d5-369">ロジックには従業員オブジェクトが必要な場合もありますが、これらのパスの一部が従業員からのタイム カード情報を必要とし、一部が必要でない場合は、異なる実行パスに分岐する場合があります。</span><span class="sxs-lookup"><span data-stu-id="e45d5-369">We might know the logic needs an employee object, but we may branch into different execution paths where some of those paths require time card information from the employee, and some do not.</span></span> <span data-ttu-id="e45d5-370">このようなシナリオは、データが必要に応じて魔法のように表示されるため、暗黙的な遅延読み込みに最適です。</span><span class="sxs-lookup"><span data-stu-id="e45d5-370">Scenarios like this are perfect for implicit lazy loading because data magically appears on an as-needed basis.</span></span>

<span data-ttu-id="e45d5-371">遅延読み込み (遅延読み込みとも呼ばれます) は、エンティティ オブジェクトにいくつかの要件を設定します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-371">Lazy loading, also known as deferred loading, does place some requirements on our entity objects.</span></span> <span data-ttu-id="e45d5-372">真の永続性の無知を持つ POPO は、永続化層からの要件に直面しませんが、真の永続性の無知を達成することは事実上不可能です。</span><span class="sxs-lookup"><span data-stu-id="e45d5-372">POCOs with true persistence ignorance would not face any requirements from the persistence layer, but true persistence ignorance is practically impossible to achieve.</span></span><span data-ttu-id="e45d5-373">代わりに、相対的な度数で永続性の無知を測定します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-373">  Instead we measure persistence ignorance in relative degrees.</span></span> <span data-ttu-id="e45d5-374">永続化指向の基本クラスから継承する必要がある場合や、POTO で遅延読み込みを実現するために特殊なコレクションを使用する必要がある場合は、残念です。</span><span class="sxs-lookup"><span data-stu-id="e45d5-374">It would be unfortunate if we needed to inherit from a persistence oriented base class or use a specialized collection to achieve lazy loading in POCOs.</span></span> <span data-ttu-id="e45d5-375">幸いなことに、EF4 は、あまり侵入性の低いソリューションを持っています。</span><span class="sxs-lookup"><span data-stu-id="e45d5-375">Fortunately, EF4 has a less intrusive solution.</span></span>

### <a name="virtually-undetectable"></a><span data-ttu-id="e45d5-376">事実上検出不可能</span><span class="sxs-lookup"><span data-stu-id="e45d5-376">Virtually Undetectable</span></span>

<span data-ttu-id="e45d5-377">POCO オブジェクトを使用する場合、EF4 はエンティティの実行時プロキシを動的に生成できます。</span><span class="sxs-lookup"><span data-stu-id="e45d5-377">When using POCO objects, EF4 can dynamically generate runtime proxies for entities.</span></span> <span data-ttu-id="e45d5-378">これらのプロキシは、具体化された POKO を目に見えない形でラップし、追加の処理を実行する各プロパティの取得および設定操作をインターセプトして追加のサービスを提供します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-378">These proxies invisibly wrap the materialized POCOs and provide additional services by intercepting each property get and set operation to perform additional work.</span></span> <span data-ttu-id="e45d5-379">そのようなサービスの 1 つは、私たちが探している遅延読み込み機能です。</span><span class="sxs-lookup"><span data-stu-id="e45d5-379">One such service is the lazy loading feature we are looking for.</span></span> <span data-ttu-id="e45d5-380">もう 1 つのサービスは、プログラムがエンティティのプロパティ値を変更するタイミングを記録できる効率的な変更追跡メカニズムです。</span><span class="sxs-lookup"><span data-stu-id="e45d5-380">Another service is an efficient change tracking mechanism which can record when the program changes the property values of an entity.</span></span> <span data-ttu-id="e45d5-381">変更の一覧は、SaveChanges メソッドの実行中に、UPDATE コマンドを使用して変更されたエンティティを保持するために ObjectContext によって使用されます。</span><span class="sxs-lookup"><span data-stu-id="e45d5-381">The list of changes is used by the ObjectContext during the SaveChanges method to persist any modified entities using UPDATE commands.</span></span>

<span data-ttu-id="e45d5-382">ただし、これらのプロキシが機能するには、プロパティの取得とエンティティの設定操作にフックする方法が必要であり、プロキシは仮想メンバーをオーバーライドすることによってこの目標を達成します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-382">For these proxies to work, however, they need a way to hook into property get and set operations on an entity, and the proxies achieve this goal by overriding virtual members.</span></span> <span data-ttu-id="e45d5-383">したがって、暗黙の遅延読み込みと効率的な変更追跡を行いたい場合は、POCO クラス定義に戻り、プロパティを仮想としてマークする必要があります。</span><span class="sxs-lookup"><span data-stu-id="e45d5-383">Thus, if we want to have implicit lazy loading and efficient change tracking we need to go back to our POCO class definitions and mark properties as virtual.</span></span>

``` csharp
    public class Employee {
        public virtual int Id { get; set; }
        public virtual string Name { get; set; }
        public virtual DateTime HireDate { get; set; }
        public virtual ICollection<TimeCard> TimeCards { get; set; }
    }
```

<span data-ttu-id="e45d5-384">従業員エンティティは主に永続性が無知であるといえます。</span><span class="sxs-lookup"><span data-stu-id="e45d5-384">We can still say the Employee entity is mostly persistence ignorant.</span></span> <span data-ttu-id="e45d5-385">唯一の要件は仮想メンバーを使用する必要があり、これはコードのテストの容易性に影響を与えません。</span><span class="sxs-lookup"><span data-stu-id="e45d5-385">The only requirement is to use virtual members and this does not impact the testability of the code.</span></span> <span data-ttu-id="e45d5-386">特別な基本クラスから派生したり、遅延読み込みに特化した特別なコレクションを使用したりする必要はありません。</span><span class="sxs-lookup"><span data-stu-id="e45d5-386">We don’t need to derive from any special base class, or even use a special collection dedicated to lazy loading.</span></span> <span data-ttu-id="e45d5-387">コードが示すように、ICollection&lt;T&gt;を実装するクラスは、関連するエンティティを保持するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="e45d5-387">As the code demonstrates, any class implementing ICollection&lt;T&gt; is available to hold related entities.</span></span>

<span data-ttu-id="e45d5-388">また、作業単位内で行う必要がある小さな変更も 1 つあります。</span><span class="sxs-lookup"><span data-stu-id="e45d5-388">There is also one minor change we need to make inside our unit of work.</span></span> <span data-ttu-id="e45d5-389">ObjectContext オブジェクトを直接操作する場合、遅延読み込みは既定で*オフ*になっています。</span><span class="sxs-lookup"><span data-stu-id="e45d5-389">Lazy loading is *off* by default when working directly with an ObjectContext object.</span></span> <span data-ttu-id="e45d5-390">遅延読み込みを有効にする ContextOptions プロパティに設定できるプロパティがあり、どこでも遅延読み込みを有効にする場合は、このプロパティを実際の作業単位内に設定できます。</span><span class="sxs-lookup"><span data-stu-id="e45d5-390">There is a property we can set on the ContextOptions property to enable deferred loading, and we can set this property inside our real unit of work if we want to enable lazy loading everywhere.</span></span>

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

<span data-ttu-id="e45d5-391">暗黙的な遅延読み込みが有効になっていると、アプリケーション コードは従業員と従業員の関連付けられたタイム カードを使用しながら、EF が余分なデータを読み込むために必要な作業を意識しなくなります。</span><span class="sxs-lookup"><span data-stu-id="e45d5-391">With implicit lazy loading enabled, application code can use an employee and the employee’s associated time cards while remaining blissfully unaware of the work required for EF to load the extra data.</span></span>

``` csharp
    var employee = _unitOfWork.Employees
                              .Single(e => e.Id == id);
    foreach (var card in employee.TimeCards) {
        // ...
    }
```

<span data-ttu-id="e45d5-392">遅延読み込みにより、アプリケーション コードの記述が簡単になり、プロキシ マジックを使用すると、コードは完全にテスト可能です。</span><span class="sxs-lookup"><span data-stu-id="e45d5-392">Lazy loading makes the application code easier to write, and with the proxy magic the code remains completely testable.</span></span> <span data-ttu-id="e45d5-393">作業単位のメモリ内フェイクは、テスト中に必要に応じて、関連データを持つ偽のエンティティをプリロードできます。</span><span class="sxs-lookup"><span data-stu-id="e45d5-393">In-memory fakes of the unit of work can simply preload fake entities with associated data when needed during a test.</span></span>

<span data-ttu-id="e45d5-394">この時点で、IObjectSet&lt;T&gt;を使用してリポジトリを構築することから注意を向け、抽象化を見て永続化フレームワークのすべての兆候を隠します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-394">At this point we’ll turn our attention from building repositories using IObjectSet&lt;T&gt; and look at abstractions to hide all signs of the persistence framework.</span></span>

## <a name="custom-repositories"></a><span data-ttu-id="e45d5-395">カスタム リポジトリ</span><span class="sxs-lookup"><span data-stu-id="e45d5-395">Custom Repositories</span></span>

<span data-ttu-id="e45d5-396">この記事で作業単位のデザイン パターンを最初に説明した時点で、作業単位の外観に関するサンプル コードをいくつか提供しました。</span><span class="sxs-lookup"><span data-stu-id="e45d5-396">When we first presented the unit of work design pattern in this article we provided some sample code for what the unit of work might look like.</span></span> <span data-ttu-id="e45d5-397">この元のアイデアを、作業してきた従業員と従業員のタイム カード シナリオを使用して再提示しましょう。</span><span class="sxs-lookup"><span data-stu-id="e45d5-397">Let’s re-present this original idea using the employee and employee time card scenario we’ve been working with.</span></span>

``` csharp
    public interface IUnitOfWork {
        IRepository<Employee> Employees { get; }
        IRepository<TimeCard> TimeCards { get;  }
        void Commit();
    }
```

<span data-ttu-id="e45d5-398">この作業単位と最後のセクションで作成した作業単位の主な違いは、この作業単位が EF4 フレームワークからの抽象化を使用しない方法です (IObjectSet&lt;T&gt;はありません)。</span><span class="sxs-lookup"><span data-stu-id="e45d5-398">The primary difference between this unit of work and the unit of work we created in the last section is how this unit of work does not use any abstractions from the EF4 framework (there is no IObjectSet&lt;T&gt;).</span></span> <span data-ttu-id="e45d5-399">IObjectSet&lt;&gt; T はリポジトリ インターフェイスとして機能しますが、公開する API はアプリケーションのニーズに完全に合わない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="e45d5-399">IObjectSet&lt;T&gt; works well as a repository interface, but the API it exposes might not perfectly align with our application’s needs.</span></span> <span data-ttu-id="e45d5-400">この今後のアプローチでは、カスタム IRepository T&lt;&gt;抽象化を使用してリポジトリを表します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-400">In this upcoming approach we will represent repositories using a custom IRepository&lt;T&gt; abstraction.</span></span>

<span data-ttu-id="e45d5-401">テスト駆動型設計、行動駆動型設計、およびドメイン駆動型の手法設計に従う開発者の多くは、&lt;いくつかの&gt;理由から IRepository T アプローチを採用しています。</span><span class="sxs-lookup"><span data-stu-id="e45d5-401">Many developers who follow test-driven design, behavior-driven design, and domain driven methodologies design prefer the IRepository&lt;T&gt; approach for several reasons.</span></span> <span data-ttu-id="e45d5-402">最初に、IRepository&lt;&gt; T インターフェイスは"破損防止" 層を表します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-402">First, the IRepository&lt;T&gt; interface represents an “anti-corruption” layer.</span></span> <span data-ttu-id="e45d5-403">ドメイン駆動型デザインの書籍で Eric Evans が説明したように、破損防止レイヤーは、永続 API のように、インフラストラクチャ API からドメイン コードを遠ざけます。</span><span class="sxs-lookup"><span data-stu-id="e45d5-403">As described by Eric Evans in his Domain Driven Design book an anti-corruption layer keeps your domain code away from infrastructure APIs, like a persistence API.</span></span> <span data-ttu-id="e45d5-404">次に、開発者は、(テストの作成中に検出された) アプリケーションの正確なニーズを満たすメソッドをリポジトリに組み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="e45d5-404">Secondly, developers can build methods into the repository that meet the exact needs of an application (as discovered while writing tests).</span></span> <span data-ttu-id="e45d5-405">たとえば、ID 値を使用して 1 つのエンティティを検索する必要が頻繁に発生するため、FindById メソッドをリポジトリ インターフェイスに追加できます。</span><span class="sxs-lookup"><span data-stu-id="e45d5-405">For example, we might frequently need to locate a single entity using an ID value, so we can add a FindById method to the repository interface.</span></span><span data-ttu-id="e45d5-406">IRepository&lt;T&gt;の定義は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="e45d5-406">  Our IRepository&lt;T&gt; definition will look like the following.</span></span>

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

<span data-ttu-id="e45d5-407">エンティティ コレクションを公開する IQueryable&lt;T&gt;インターフェイスの使用に戻ります。</span><span class="sxs-lookup"><span data-stu-id="e45d5-407">Notice we’ll drop back to using an IQueryable&lt;T&gt; interface to expose entity collections.</span></span> <span data-ttu-id="e45d5-408">IQueryable&lt;&gt; T を使用すると、LINQ 式ツリーを EF4 プロバイダーにフローし、プロバイダーにクエリの全体的なビューを提供できます。</span><span class="sxs-lookup"><span data-stu-id="e45d5-408">IQueryable&lt;T&gt; allows LINQ expression trees to flow into the EF4 provider and give the provider a holistic view of the query.</span></span> <span data-ttu-id="e45d5-409">2 つ目のオプションは&lt;IEnumerable&gt;T を返す方法で、EF4 LINQ プロバイダーはリポジトリ内に構築された式のみを表示します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-409">A second option would be to return IEnumerable&lt;T&gt;, which means the EF4 LINQ provider will only see the expressions built inside of the repository.</span></span> <span data-ttu-id="e45d5-410">リポジトリーの外部で行われたグループ化、順序付け、およびプロジェクションは、データベースに送信される SQL コマンドに構成されず、パフォーマンスが低下する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="e45d5-410">Any grouping, ordering, and projection done outside of the repository will not be composed into the SQL command sent to the database, which can hurt performance.</span></span> <span data-ttu-id="e45d5-411">一方、IEnumerable&lt;T&gt;の結果のみを返すリポジトリは、新しい SQL コマンドで驚くことはありません。</span><span class="sxs-lookup"><span data-stu-id="e45d5-411">On the other hand, a repository returning only IEnumerable&lt;T&gt; results will never surprise you with a new SQL command.</span></span> <span data-ttu-id="e45d5-412">どちらの方法も機能し、両方のアプローチはテスト可能なままです。</span><span class="sxs-lookup"><span data-stu-id="e45d5-412">Both approaches will work, and both approaches remain testable.</span></span>

<span data-ttu-id="e45d5-413">ジェネリックと EF4 ObjectContext API を使用して&lt;&gt; IRepository T インターフェイスの単一の実装を提供するのは簡単です。</span><span class="sxs-lookup"><span data-stu-id="e45d5-413">It’s straightforward to provide a single implementation of the IRepository&lt;T&gt; interface using generics and the EF4 ObjectContext API.</span></span>

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

<span data-ttu-id="e45d5-414">IRepository&lt;T&gt;アプローチでは、クライアントがエンティティにアクセスするためのメソッドを呼び出す必要があるため、クエリを制御する追加の方法が提供されます。</span><span class="sxs-lookup"><span data-stu-id="e45d5-414">The IRepository&lt;T&gt; approach gives us some additional control over our queries because a client has to invoke a method to get to an entity.</span></span> <span data-ttu-id="e45d5-415">メソッド内では、追加のチェックと LINQ 演算子を提供して、アプリケーションの制約を適用できます。</span><span class="sxs-lookup"><span data-stu-id="e45d5-415">Inside the method we could provide additional checks and LINQ operators to enforce application constraints.</span></span> <span data-ttu-id="e45d5-416">インターフェイスには、ジェネリック型パラメーターに 2 つの制約があることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="e45d5-416">Notice the interface has two constraints on the generic type parameter.</span></span> <span data-ttu-id="e45d5-417">1 つ目の制約は ObjectSet&lt;T&gt;で必要とされるクラスの制約であり、2 つ目の制約は、アプリケーション用に作成された抽象化である IEntity をエンティティに強制的に実装します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-417">The first constraint is the class cons taint required by ObjectSet&lt;T&gt;, and the second constraint forces our entities to implement IEntity – an abstraction created for the application.</span></span> <span data-ttu-id="e45d5-418">IEntity インターフェイスは、エンティティに読み取り可能な Id プロパティを強制し、FindById メソッドでこのプロパティを使用できます。</span><span class="sxs-lookup"><span data-stu-id="e45d5-418">The IEntity interface forces entities to have a readable Id property, and we can then use this property in the FindById method.</span></span> <span data-ttu-id="e45d5-419">IEntity は次のコードで定義されています。</span><span class="sxs-lookup"><span data-stu-id="e45d5-419">IEntity is defined with the following code.</span></span>

``` csharp
    public interface IEntity {
        int Id { get; }
    }
```

<span data-ttu-id="e45d5-420">IEntity は、このインターフェイスを実装するためにエンティティが必要であるため、永続化の無知の小さな違反と見なされる可能性があります。</span><span class="sxs-lookup"><span data-stu-id="e45d5-420">IEntity could be considered a small violation of persistence ignorance since our entities are required to implement this interface.</span></span> <span data-ttu-id="e45d5-421">永続性の無知はトレードオフに関するもので、FindById 機能の多くはインターフェイスによって課される制約を上回ることを覚えておいてください。</span><span class="sxs-lookup"><span data-stu-id="e45d5-421">Remember persistence ignorance is about tradeoffs, and for many the FindById functionality will outweigh the constraint imposed by the interface.</span></span> <span data-ttu-id="e45d5-422">このインターフェイスは、テストの容易性に影響を与える必要はありません。</span><span class="sxs-lookup"><span data-stu-id="e45d5-422">The interface has no impact on testability.</span></span>

<span data-ttu-id="e45d5-423">ライブ IRepository&lt;T&gt;をインスタンス化するには、EF4 ObjectContext が必要であるため、具体的な作業単位の実装では、インスタンス化を管理する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e45d5-423">Instantiating a live IRepository&lt;T&gt; requires an EF4 ObjectContext, so a concrete unit of work implementation should manage the instantiation.</span></span>

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

### <a name="using-the-custom-repository"></a><span data-ttu-id="e45d5-424">カスタム リポジトリの使用</span><span class="sxs-lookup"><span data-stu-id="e45d5-424">Using the Custom Repository</span></span>

<span data-ttu-id="e45d5-425">カスタム リポジトリの使用は、IObjectSet&lt;T&gt;に基づくリポジトリを使用する場合と大きな違いはありません。</span><span class="sxs-lookup"><span data-stu-id="e45d5-425">Using our custom repository is not significantly different from using the repository based on IObjectSet&lt;T&gt;.</span></span> <span data-ttu-id="e45d5-426">LINQ 演算子をプロパティに直接適用する代わりに、まずリポジトリのメソッドを呼び出して IQueryable&lt;T&gt;参照を取得する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e45d5-426">Instead of applying LINQ operators directly to a property, we’ll first need to invoke one the repository’s methods to grab an IQueryable&lt;T&gt; reference.</span></span>

``` csharp
    public ViewResult Index() {
        var model = _repository.FindAll()
                               .Include("TimeCards")
                               .OrderBy(e => e.HireDate);
        return View(model);
    }
```

<span data-ttu-id="e45d5-427">以前に実装したカスタムの Include 演算子は、変更せずに動作します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-427">Notice the custom Include operator we implemented previously will work without change.</span></span> <span data-ttu-id="e45d5-428">リポジトリの FindById メソッドは、1 つのエンティティを取得しようとするアクションから重複したロジックを削除します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-428">The repository’s FindById method removes duplicated logic from actions trying to retrieve a single entity.</span></span>

``` csharp
    public ViewResult Details(int id) {
        var model = _repository.FindById(id);
        return View(model);
    }
```

<span data-ttu-id="e45d5-429">調査した 2 つのアプローチのテスト可能性に大きな違いはありません。</span><span class="sxs-lookup"><span data-stu-id="e45d5-429">There is no significant difference in the testability of the two approaches we’ve examined.</span></span> <span data-ttu-id="e45d5-430">前の&lt;セクションで行ったように、HashSet&gt; &lt;Employee&gt;の支援を受けた具体的なクラスを構築することで、IRepository Tの偽の実装を提供することができました。</span><span class="sxs-lookup"><span data-stu-id="e45d5-430">We could provide fake implementations of IRepository&lt;T&gt; by building concrete classes backed by HashSet&lt;Employee&gt; - just like what we did in the last section.</span></span> <span data-ttu-id="e45d5-431">ただし、開発者によっては、偽物を作成する代わりにモック オブジェクトとモック オブジェクト フレームワークを使用することを好む開発者もいます。</span><span class="sxs-lookup"><span data-stu-id="e45d5-431">However, some developers prefer to use mock objects and mock object frameworks instead of building fakes.</span></span> <span data-ttu-id="e45d5-432">モックを使用して実装をテストし、モックとフェイクの違いについては次のセクションで説明します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-432">We’ll look at using mocks to test our implementation and discuss the differences between mocks and fakes in the next section.</span></span>

### <a name="testing-with-mocks"></a><span data-ttu-id="e45d5-433">モックを使用したテスト</span><span class="sxs-lookup"><span data-stu-id="e45d5-433">Testing with Mocks</span></span>

<span data-ttu-id="e45d5-434">マーティン・ファウラーが「テスト・ダブル」と呼ぶものを構築するには、さまざまなアプローチがあります。</span><span class="sxs-lookup"><span data-stu-id="e45d5-434">There are different approaches to building what Martin Fowler calls a “test double”.</span></span> <span data-ttu-id="e45d5-435">テストダブル(映画スタントダブルなど)は、テスト中に実際のプロダクションオブジェクトのために「立つ」ために構築するオブジェクトです。</span><span class="sxs-lookup"><span data-stu-id="e45d5-435">A test double (like a movie stunt double) is an object you build to “stand in” for real, production objects during tests.</span></span> <span data-ttu-id="e45d5-436">作成したメモリ内リポジトリは、SQL Server との間でやり取りするリポジトリのテストダブルです。</span><span class="sxs-lookup"><span data-stu-id="e45d5-436">The in-memory repositories we created are test doubles for the repositories that talk to SQL Server.</span></span> <span data-ttu-id="e45d5-437">単体テスト中にこれらのテストダブルを使用してコードを分離し、テストを高速に実行する方法を見てきました。</span><span class="sxs-lookup"><span data-stu-id="e45d5-437">We’ve seen how to use these test-doubles during the unit tests to isolate code and keep tests running fast.</span></span>

<span data-ttu-id="e45d5-438">私たちが構築したテストダブルは、実際の作業実装を持っています。</span><span class="sxs-lookup"><span data-stu-id="e45d5-438">The test doubles we’ve built have real, working implementations.</span></span> <span data-ttu-id="e45d5-439">シーンの背後には、それぞれがオブジェクトの具体的なコレクションを格納し、テスト中にリポジトリを操作する際に、このコレクションに対してオブジェクトを追加および削除します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-439">Behind the scenes each one stores a concrete collection of objects, and they will add and remove objects from this collection as we manipulate the repository during a test.</span></span> <span data-ttu-id="e45d5-440">実際のコードと実際の実装で、テストをこのようにビルドしたい開発者もいます。</span><span class="sxs-lookup"><span data-stu-id="e45d5-440">Some developers like to build their test doubles this way – with real code and working implementations.</span></span><span data-ttu-id="e45d5-441">これらのテストダブルは、私たちが*偽物と*呼ぶものです.</span><span class="sxs-lookup"><span data-stu-id="e45d5-441">  These test doubles are what we call *fakes*.</span></span> <span data-ttu-id="e45d5-442">彼らは作業実装を持っていますが、実稼働環境では十分ではありません。</span><span class="sxs-lookup"><span data-stu-id="e45d5-442">They have working implementations, but they aren’t real enough for production use.</span></span> <span data-ttu-id="e45d5-443">偽のリポジトリは実際にはデータベースに書き込みません。</span><span class="sxs-lookup"><span data-stu-id="e45d5-443">The fake repository doesn’t actually write to the database.</span></span> <span data-ttu-id="e45d5-444">偽の SMTP サーバーは、実際にはネットワーク経由で電子メール メッセージを送信しません。</span><span class="sxs-lookup"><span data-stu-id="e45d5-444">The fake SMTP server doesn’t actually send an email message over the network.</span></span>

### <a name="mocks-versus-fakes"></a><span data-ttu-id="e45d5-445">モック対フェイク</span><span class="sxs-lookup"><span data-stu-id="e45d5-445">Mocks versus Fakes</span></span>

<span data-ttu-id="e45d5-446">*モック*と呼ばれる別のタイプのテストダブルがあります。</span><span class="sxs-lookup"><span data-stu-id="e45d5-446">There is another type of test double known as a *mock*.</span></span> <span data-ttu-id="e45d5-447">偽物は動作する実装を持っていますが、モックは実装なしで来ます。</span><span class="sxs-lookup"><span data-stu-id="e45d5-447">While fakes have working implementations, mocks come with no implementation.</span></span> <span data-ttu-id="e45d5-448">モック オブジェクト フレームワークの助けを借りて、実行時にこれらのモック オブジェクトを構築し、テストダブルとして使用します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-448">With the help of a mock object framework we construct these mock objects at run time and use them as test doubles.</span></span> <span data-ttu-id="e45d5-449">このセクションでは、オープンソースのモックフレームワーク Moq を使用します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-449">In this section we’ll be using the open source mocking framework Moq.</span></span> <span data-ttu-id="e45d5-450">Moq を使用して従業員リポジトリのテストダブルを動的に作成する簡単な例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-450">Here is a simple example of using Moq to dynamically create a test double for an employee repository.</span></span>

``` csharp
    Mock<IRepository<Employee>> mock =
        new Mock<IRepository<Employee>>();
    IRepository<Employee> repository = mock.Object;
    repository.Add(new Employee());
    var employee = repository.FindById(1);
```

<span data-ttu-id="e45d5-451">Moq に IRepository&lt;従業員&gt;の実装を依頼し、動的に 1 つを構築します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-451">We ask Moq for an IRepository&lt;Employee&gt; implementation and it builds one dynamically.</span></span> <span data-ttu-id="e45d5-452">モック&lt;T&gt;オブジェクトの Object プロパティ&lt;にアクセスすることで、IRepository Employee&gt;を実装するオブジェクトにアクセスできます。</span><span class="sxs-lookup"><span data-stu-id="e45d5-452">We can get to the object implementing IRepository&lt;Employee&gt; by accessing the Object property of the Mock&lt;T&gt; object.</span></span> <span data-ttu-id="e45d5-453">コントローラに渡すことができるのはこの内部オブジェクトであり、これがテストダブルか実際のリポジトリかは分かりません。</span><span class="sxs-lookup"><span data-stu-id="e45d5-453">It is this inner object we can pass into our controllers, and they won’t know if this is a test double or the real repository.</span></span> <span data-ttu-id="e45d5-454">実際の実装を使用してオブジェクトのメソッドを呼び出すのと同じように、オブジェクトのメソッドを呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="e45d5-454">We can invoke methods on the object just like we would invoke methods on an object with a real implementation.</span></span>

<span data-ttu-id="e45d5-455">Add メソッドを呼び出すときにモック リポジトリが何をするのか疑問に思う必要があります。</span><span class="sxs-lookup"><span data-stu-id="e45d5-455">You must be wondering what the mock repository will do when we invoke the Add method.</span></span> <span data-ttu-id="e45d5-456">モック オブジェクトの背後には実装がないため、Add は何もしません。</span><span class="sxs-lookup"><span data-stu-id="e45d5-456">Since there is no implementation behind the mock object, Add does nothing.</span></span> <span data-ttu-id="e45d5-457">私たちが書いた偽物のように、舞台裏に具体的なコレクションがないので、従業員は捨てられます。</span><span class="sxs-lookup"><span data-stu-id="e45d5-457">There is no concrete collection behind the scenes like we had with the fakes we wrote, so the employee is discarded.</span></span> <span data-ttu-id="e45d5-458">FindById の戻り値についてはどうでしょうか。</span><span class="sxs-lookup"><span data-stu-id="e45d5-458">What about the return value of FindById?</span></span> <span data-ttu-id="e45d5-459">この場合、モックオブジェクトは、それができる唯一の操作を行います。</span><span class="sxs-lookup"><span data-stu-id="e45d5-459">In this case the mock object does the only thing it can do, which is return a default value.</span></span> <span data-ttu-id="e45d5-460">参照型 (Employee) を返すため、戻り値は null 値です。</span><span class="sxs-lookup"><span data-stu-id="e45d5-460">Since we are returning a reference type (an Employee), the return value is a null value.</span></span>

<span data-ttu-id="e45d5-461">モックは価値のない音かもしれません。しかし、私たちが話していないモックの機能が2つあります。</span><span class="sxs-lookup"><span data-stu-id="e45d5-461">Mocks might sound worthless; however, there are two more features of mocks we haven’t talked about.</span></span> <span data-ttu-id="e45d5-462">まず、Moq フレームワークはモック オブジェクトで行われたすべての呼び出しを記録します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-462">First, the Moq framework records all the calls made on the mock object.</span></span> <span data-ttu-id="e45d5-463">コードの後半で、誰かが Add メソッドを呼び出したか、または FindById メソッドを呼び出した場合は Moq に問い合わせることができます。</span><span class="sxs-lookup"><span data-stu-id="e45d5-463">Later in the code we can ask Moq if anyone invoked the Add method, or if anyone invoked the FindById method.</span></span> <span data-ttu-id="e45d5-464">この「ブラックボックス」の記録機能をテストで使用する方法については、後で説明します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-464">We’ll see later how we can use this “black box” recording feature in tests.</span></span>

<span data-ttu-id="e45d5-465">2番目の大きな特徴は、モックを使用して *、期待*したモックオブジェクトをプログラムする方法です。</span><span class="sxs-lookup"><span data-stu-id="e45d5-465">The second great feature is how we can use Moq to program a mock object with *expectations*.</span></span> <span data-ttu-id="e45d5-466">期待は、モックオブジェクトに特定の相互作用にどのように応答するかを伝えます。</span><span class="sxs-lookup"><span data-stu-id="e45d5-466">An expectation tells the mock object how to respond to any given interaction.</span></span> <span data-ttu-id="e45d5-467">たとえば、期待をモックにプログラミングし、誰かが FindById を呼び出したときに従業員オブジェクトを返すように指示できます。</span><span class="sxs-lookup"><span data-stu-id="e45d5-467">For example, we can program an expectation into our mock and tell it to return an employee object when someone invokes FindById.</span></span> <span data-ttu-id="e45d5-468">Moq フレームワークは、セットアップ API とラムダ式を使用して、これらの期待値をプログラミングします。</span><span class="sxs-lookup"><span data-stu-id="e45d5-468">The Moq framework uses a Setup API and lambda expressions to program these expectations.</span></span>

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

<span data-ttu-id="e45d5-469">このサンプルでは、Moq にリポジトリを動的に構築するよう依頼し、期待してリポジトリをプログラムします。</span><span class="sxs-lookup"><span data-stu-id="e45d5-469">In this sample we ask Moq to dynamically build a repository, and then we program the repository with an expectation.</span></span> <span data-ttu-id="e45d5-470">期待値は、値 5 を渡す FindById メソッドを呼び出したときに、Id 値が 5 の新しい従業員オブジェクトを返すようにモック オブジェクトに指示します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-470">The expectation tells the mock object to return a new employee object with an Id value of 5 when someone invokes the FindById method passing a value of 5.</span></span> <span data-ttu-id="e45d5-471">このテストは成功し、IRepository&lt;T&gt;を偽るための完全な実装を構築する必要はありませんでした。</span><span class="sxs-lookup"><span data-stu-id="e45d5-471">This test passes, and we didn’t need to build a full implementation to fake IRepository&lt;T&gt;.</span></span>

<span data-ttu-id="e45d5-472">先ほど書いたテストを再検討し、偽物の代わりにモックを使用するように再作業してみましょう。</span><span class="sxs-lookup"><span data-stu-id="e45d5-472">Let’s revisit the tests we wrote earlier and rework them to use mocks instead of fakes.</span></span> <span data-ttu-id="e45d5-473">以前と同様に、基本クラスを使用して、すべてのコントローラーのテストに必要な共通のインフラストラクチャを設定します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-473">Just like before, we’ll use a base class to setup the common pieces of infrastructure we need for all of the controller’s tests.</span></span>

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

<span data-ttu-id="e45d5-474">セットアップ コードはほとんど同じです。</span><span class="sxs-lookup"><span data-stu-id="e45d5-474">The setup code remains mostly the same.</span></span> <span data-ttu-id="e45d5-475">偽物を使用する代わりに、モックオブジェクトを構築するためにMoqを使用します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-475">Instead of using fakes, we’ll use Moq to construct mock objects.</span></span> <span data-ttu-id="e45d5-476">基本クラスは、コードが Employees プロパティを呼び出すときにモック リポジトリを返すように、作業のモック単位を配置します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-476">The base class arranges for the mock unit of work to return a mock repository when code invokes the Employees property.</span></span> <span data-ttu-id="e45d5-477">モックセットアップの残りの部分は、各特定のシナリオ専用のテスト備品内で行われます。</span><span class="sxs-lookup"><span data-stu-id="e45d5-477">The rest of the mock setup will take place inside the test fixtures dedicated to each specific scenario.</span></span> <span data-ttu-id="e45d5-478">たとえば、インデックス アクションのテストフィクスチャは、アクションがモック リポジトリの FindAll メソッドを呼び出したときに、従業員のリストを返すようにモック リポジトリを設定します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-478">For example, the test fixture for the Index action will setup the mock repository to return a list of employees when the action invokes the FindAll method of the mock repository.</span></span>

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

<span data-ttu-id="e45d5-479">期待を除いて、私たちのテストは以前のテストに似ています。</span><span class="sxs-lookup"><span data-stu-id="e45d5-479">Except for the expectations, our tests look similar to the tests we had before.</span></span> <span data-ttu-id="e45d5-480">しかし、モックフレームワークの記録能力を使用して、異なる角度からテストにアプローチすることができます。</span><span class="sxs-lookup"><span data-stu-id="e45d5-480">However, with the recording ability of a mock framework we can approach testing from a different angle.</span></span> <span data-ttu-id="e45d5-481">この新しい視点は次のセクションで見ていきます。</span><span class="sxs-lookup"><span data-stu-id="e45d5-481">We’ll look at this new perspective in the next section.</span></span>

### <a name="state-versus-interaction-testing"></a><span data-ttu-id="e45d5-482">状態と相互作用テスト</span><span class="sxs-lookup"><span data-stu-id="e45d5-482">State versus Interaction Testing</span></span>

<span data-ttu-id="e45d5-483">モック オブジェクトを使用してソフトウェアをテストするために使用できるさまざまな手法があります。</span><span class="sxs-lookup"><span data-stu-id="e45d5-483">There are different techniques you can use to test software with mock objects.</span></span> <span data-ttu-id="e45d5-484">1 つのアプローチは、状態ベースのテストを使用することです。</span><span class="sxs-lookup"><span data-stu-id="e45d5-484">One approach is to use state based testing, which is what we have done in this paper so far.</span></span> <span data-ttu-id="e45d5-485">状態ベースのテストでは、ソフトウェアの状態に関するアサーションが行われます。</span><span class="sxs-lookup"><span data-stu-id="e45d5-485">State based testing makes assertions about the state of the software.</span></span> <span data-ttu-id="e45d5-486">最後のテストでは、コント ローラーのアクション メソッドを呼び出し、ビルドする必要があるモデルに関するアサーションを行いました。</span><span class="sxs-lookup"><span data-stu-id="e45d5-486">In the last test we invoked an action method on the controller and made an assertion about the model it should build.</span></span> <span data-ttu-id="e45d5-487">テスト状態の他の例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-487">Here are some other examples of testing state:</span></span>

-   <span data-ttu-id="e45d5-488">Create の実行後に、リポジトリに新しい従業員オブジェクトが含まれていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-488">Verify the repository contains the new employee object after Create executes.</span></span>
-   <span data-ttu-id="e45d5-489">Index の実行後に、モデルがすべての従業員のリストを保持しているかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-489">Verify the model holds a list of all employees after Index executes.</span></span>
-   <span data-ttu-id="e45d5-490">Delete の実行後に、リポジトリに特定の従業員が含まれていないかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-490">Verify the repository does not contain a given employee after Delete executes.</span></span>

<span data-ttu-id="e45d5-491">モック オブジェクトで見るもう 1 つの方法は、相互作用を検証*することです*。</span><span class="sxs-lookup"><span data-stu-id="e45d5-491">Another approach you’ll see with mock objects is to verify *interactions*.</span></span> <span data-ttu-id="e45d5-492">状態ベースのテストではオブジェクトの状態に関するアサーションが行われますが、相互作用ベースのテストでは、オブジェクトの相互作用に関するアサーションが行われます。</span><span class="sxs-lookup"><span data-stu-id="e45d5-492">While state based testing makes assertions about the state of objects, interaction based testing makes assertions about how objects interact.</span></span> <span data-ttu-id="e45d5-493">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-493">For example:</span></span>

-   <span data-ttu-id="e45d5-494">Create の実行時に、コントローラーがリポジトリの Add メソッドを呼び出すかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-494">Verify the controller invokes the repository’s Add method when Create executes.</span></span>
-   <span data-ttu-id="e45d5-495">コントローラーが、Index の実行時にリポジトリの FindAll メソッドを呼び出すかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-495">Verify the controller invokes the repository’s FindAll method when Index executes.</span></span>
-   <span data-ttu-id="e45d5-496">コントローラーが作業単位の Commit メソッドを呼び出して、Edit の実行時に変更を保存することを確認します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-496">Verify the controller invokes the unit of work’s Commit method to save changes when Edit executes.</span></span>

<span data-ttu-id="e45d5-497">インタラクションテストでは、コレクション内を突いてカウントを検証していないため、多くの場合、必要なテスト データが少なくなります。</span><span class="sxs-lookup"><span data-stu-id="e45d5-497">Interaction testing often requires less test data, because we aren’t poking inside of collections and verifying counts.</span></span> <span data-ttu-id="e45d5-498">たとえば、Details アクションが正しい値を持つリポジトリの FindById メソッドを呼び出す場合、アクションは正しく動作している可能性があります。</span><span class="sxs-lookup"><span data-stu-id="e45d5-498">For example, if we know the Details action invokes a repository’s FindById method with the correct value - then the action is probably behaving correctly.</span></span> <span data-ttu-id="e45d5-499">FindById から返すテスト データを設定しなくても、この動作を検証できます。</span><span class="sxs-lookup"><span data-stu-id="e45d5-499">We can verify this behavior without setting up any test data to return from FindById.</span></span>

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

<span data-ttu-id="e45d5-500">上記のテストフィクスチャで必要なセットアップは、基本クラスによって提供されるセットアップのみです。</span><span class="sxs-lookup"><span data-stu-id="e45d5-500">The only setup required in the above test fixture is the setup provided by the base class.</span></span> <span data-ttu-id="e45d5-501">コントローラアクションを呼び出すと、モックリポジトリとの対話が記録されます。</span><span class="sxs-lookup"><span data-stu-id="e45d5-501">When we invoke the controller action, Moq will record the interactions with the mock repository.</span></span> <span data-ttu-id="e45d5-502">Moq の検証 API を使用して、コントローラが適切な ID 値で FindById を呼び出したかどうか Moq に問い合わせることができます。</span><span class="sxs-lookup"><span data-stu-id="e45d5-502">Using the Verify API of Moq, we can ask Moq if the controller invoked FindById with the proper ID value.</span></span> <span data-ttu-id="e45d5-503">コントローラーがメソッドを呼び出さなかった場合、または予期しないパラメーター値を指定してメソッドを呼び出した場合、Verify メソッドは例外をスローし、テストは失敗します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-503">If the controller did not invoke the method, or invoked the method with an unexpected parameter value, the Verify method will throw an exception and the test will fail.</span></span>

<span data-ttu-id="e45d5-504">Create アクションが現在の作業単位でコミットを呼び出すことを確認する別の例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-504">Here is another example to verify the Create action invokes Commit on the current unit of work.</span></span>

``` csharp
    [TestMethod]
    public void ShouldCommitUnitOfWork() {
        _controller.Create(_newEmployee);
        _unitOfWork.Verify(u => u.Commit());
    }
```

<span data-ttu-id="e45d5-505">相互作用テストの危険性の1つは、相互作用を過剰に指定する傾向にあります。</span><span class="sxs-lookup"><span data-stu-id="e45d5-505">One danger with interaction testing is the tendency to over specify interactions.</span></span> <span data-ttu-id="e45d5-506">モック オブジェクトがモック オブジェクトとのすべての対話を記録および検証する機能は、テストがすべての相互作用を検証しようとする必要があることを意味しません。</span><span class="sxs-lookup"><span data-stu-id="e45d5-506">The ability of the mock object to record and verify every interaction with the mock object doesn’t mean the test should try to verify every interaction.</span></span> <span data-ttu-id="e45d5-507">インタラクションの中には実装の詳細なものもありますが、現在のテストを満たすために*必要な*インタラクションのみを検証する必要があります。</span><span class="sxs-lookup"><span data-stu-id="e45d5-507">Some interactions are implementation details and you should only verify the interactions *required* to satisfy the current test.</span></span>

<span data-ttu-id="e45d5-508">モックまたは偽物の間の選択は、主にあなたがテストしているシステムとあなたの個人的な(またはチーム)の好みに依存します。</span><span class="sxs-lookup"><span data-stu-id="e45d5-508">The choice between mocks or fakes largely depends on the system you are testing and your personal (or team) preferences.</span></span> <span data-ttu-id="e45d5-509">モック オブジェクトを使用すると、テストの倍数を実装するために必要なコードの量を大幅に削減できますが、誰もが期待するプログラミングと対話の検証に慣れたわけではありません。</span><span class="sxs-lookup"><span data-stu-id="e45d5-509">Mock objects can drastically reduce the amount of code you need to implement test doubles, but not everyone is comfortable programming expectations and verifying interactions.</span></span>

## <a name="conclusions"></a><span data-ttu-id="e45d5-510">まとめ</span><span class="sxs-lookup"><span data-stu-id="e45d5-510">Conclusions</span></span>

<span data-ttu-id="e45d5-511">このペーパーでは、データの永続化にADO.NET Entity Framework を使用しながらテスト可能なコードを作成するいくつかの方法を示しました。</span><span class="sxs-lookup"><span data-stu-id="e45d5-511">In this paper we’ve demonstrated several approaches to creating testable code while using the ADO.NET Entity Framework for data persistence.</span></span> <span data-ttu-id="e45d5-512">IObjectSet&lt;T のような組み込みの抽象化&gt;を利用したり、IRepository&lt;T&gt;のような独自の抽象化を作成したりできます。</span><span class="sxs-lookup"><span data-stu-id="e45d5-512">We can leverage built in abstractions like IObjectSet&lt;T&gt;, or create our own abstractions like IRepository&lt;T&gt;.</span></span><span data-ttu-id="e45d5-513">どちらの場合も、ADO.NET Entity Framework 4.0 の POCO サポートにより、これらの抽象化のコンシューマーは、常に無知で高度にテスト可能です。</span><span class="sxs-lookup"><span data-stu-id="e45d5-513">  In both cases, the POCO support in the ADO.NET Entity Framework 4.0 allows the consumers of these abstractions to remain persistent ignorant and highly testable.</span></span> <span data-ttu-id="e45d5-514">暗黙の遅延読み込みなどの追加の EF4 機能を使用すると、リレーショナル データ ストアの詳細を気にせずにビジネスおよびアプリケーションのサービス コードを動作させることができます。</span><span class="sxs-lookup"><span data-stu-id="e45d5-514">Additional EF4 features like implicit lazy loading allows business and application service code to work without worrying about the details of a relational data store.</span></span> <span data-ttu-id="e45d5-515">最後に、作成する抽象化は単体テストの内部で簡単にモックまたは偽りで、高速な実行、高度に隔離された、信頼性の高いテストを実現するためにこれらのテストダブルを使用できます。</span><span class="sxs-lookup"><span data-stu-id="e45d5-515">Finally, the abstractions we create are easy to mock or fake inside of unit tests, and we can use these test doubles to achieve fast running, highly isolated, and reliable tests.</span></span>

### <a name="additional-resources"></a><span data-ttu-id="e45d5-516">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="e45d5-516">Additional Resources</span></span>

-   <span data-ttu-id="e45d5-517">ロバート・C・マーティン「[単一責任原則](https://www.objectmentor.com/resources/articles/srp.pdf)」</span><span class="sxs-lookup"><span data-stu-id="e45d5-517">Robert C. Martin, “ [The Single Responsibility Principle](https://www.objectmentor.com/resources/articles/srp.pdf)”</span></span>
-   <span data-ttu-id="e45d5-518">マーティン・ファウラー、*エンタープライズアプリケーションアーキテクチャのパターンからパターンの*[カタログ](https://www.martinfowler.com/eaaCatalog/index.html)</span><span class="sxs-lookup"><span data-stu-id="e45d5-518">Martin Fowler, [Catalog of Patterns](https://www.martinfowler.com/eaaCatalog/index.html) from *Patterns of Enterprise Application Architecture*</span></span>
-   <span data-ttu-id="e45d5-519">グリフィン・カプリオ「[依存性注入](https://msdn.microsoft.com/magazine/cc163739.aspx)」</span><span class="sxs-lookup"><span data-stu-id="e45d5-519">Griffin Caprio, “ [Dependency Injection](https://msdn.microsoft.com/magazine/cc163739.aspx)”</span></span>
-   <span data-ttu-id="e45d5-520">データ プログラミング ブログ[「 チュートリアル : Entity Framework 4.0 を使用したテスト駆動型開発](https://docs.microsoft.com/archive/blogs/adonet/walkthrough-test-driven-development-with-the-entity-framework-4-0)」</span><span class="sxs-lookup"><span data-stu-id="e45d5-520">Data Programmability Blog, “ [Walkthrough: Test Driven Development with the Entity Framework 4.0](https://docs.microsoft.com/archive/blogs/adonet/walkthrough-test-driven-development-with-the-entity-framework-4-0)”.</span></span>
-   <span data-ttu-id="e45d5-521">データ プログラミング ブログ"[エンティティ フレームワーク 4.0 を使用したリポジトリおよび作業単位パターンの使用](https://docs.microsoft.com/archive/blogs/adonet/using-repository-and-unit-of-work-patterns-with-entity-framework-4-0)"</span><span class="sxs-lookup"><span data-stu-id="e45d5-521">Data Programmability Blog, “ [Using Repository and Unit of Work patterns with Entity Framework 4.0](https://docs.microsoft.com/archive/blogs/adonet/using-repository-and-unit-of-work-patterns-with-entity-framework-4-0)”</span></span>
-   <span data-ttu-id="e45d5-522">アーロン・ジェンセン「[機械仕様の紹介](http://codebetter.com/blogs/aaron.jensen/archive/2008/05/08/introducing-machine-specifications-or-mspec-for-short.aspx)」</span><span class="sxs-lookup"><span data-stu-id="e45d5-522">Aaron Jensen, “ [Introducing Machine Specifications](http://codebetter.com/blogs/aaron.jensen/archive/2008/05/08/introducing-machine-specifications-or-mspec-for-short.aspx)”</span></span>
-   <span data-ttu-id="e45d5-523">エリック・リー [「MSTestとBDD」](https://saintgimp.org/2009/01/20/bdd-with-mstest/)</span><span class="sxs-lookup"><span data-stu-id="e45d5-523">Eric Lee, “ [BDD with MSTest](https://saintgimp.org/2009/01/20/bdd-with-mstest/)”</span></span>
-   <span data-ttu-id="e45d5-524">エリック・エヴァンス「[ドメイン駆動型デザイン](https://books.google.com/books?id=7dlaMs0SECsC&printsec=frontcover&dq=evans%20domain%20driven%20design&hl=en&ei=cHztS6C8KIaglAfA_dS1CA&sa=X&oi=book_result&ct=result&resnum=1&ved=0CCoQ6AEwAA)」</span><span class="sxs-lookup"><span data-stu-id="e45d5-524">Eric Evans, “ [Domain Driven Design](https://books.google.com/books?id=7dlaMs0SECsC&printsec=frontcover&dq=evans%20domain%20driven%20design&hl=en&ei=cHztS6C8KIaglAfA_dS1CA&sa=X&oi=book_result&ct=result&resnum=1&ved=0CCoQ6AEwAA)”</span></span>
-   <span data-ttu-id="e45d5-525">マーティン・ファウラー「[モックはスタブではない](https://martinfowler.com/articles/mocksArentStubs.html)」</span><span class="sxs-lookup"><span data-stu-id="e45d5-525">Martin Fowler, “ [Mocks Aren’t Stubs](https://martinfowler.com/articles/mocksArentStubs.html)”</span></span>
-   <span data-ttu-id="e45d5-526">マーティン・ファウラー「[テストダブル](https://martinfowler.com/bliki/TestDouble.html)」</span><span class="sxs-lookup"><span data-stu-id="e45d5-526">Martin Fowler, “ [Test Double](https://martinfowler.com/bliki/TestDouble.html)”</span></span>
-   [<span data-ttu-id="e45d5-527">Moq</span><span class="sxs-lookup"><span data-stu-id="e45d5-527">Moq</span></span>](https://code.google.com/p/moq/)

### <a name="biography"></a><span data-ttu-id="e45d5-528">伝記</span><span class="sxs-lookup"><span data-stu-id="e45d5-528">Biography</span></span>

<span data-ttu-id="e45d5-529">スコット・アレンは、Pluralsightの技術スタッフの一員であり、OdeToCode.comの創設者です。</span><span class="sxs-lookup"><span data-stu-id="e45d5-529">Scott Allen is a member of the technical staff at Pluralsight and the founder of OdeToCode.com.</span></span> <span data-ttu-id="e45d5-530">15年間の商用ソフトウェア開発の中で、Scottは8ビットの組み込みデバイスから拡張性の高いASP.NETWebアプリケーションまで、あらゆるソリューションに取り組んできました。</span><span class="sxs-lookup"><span data-stu-id="e45d5-530">In 15 years of commercial software development, Scott has worked on solutions for everything from 8-bit embedded devices to highly scalable ASP.NET web applications.</span></span> <span data-ttu-id="e45d5-531">スコットはOdeToCodeのブログやツイッター(.)で[https://twitter.com/OdeToCode](https://twitter.com/OdeToCode)アクセスできます。</span><span class="sxs-lookup"><span data-stu-id="e45d5-531">You can reach Scott on his blog at OdeToCode, or on Twitter at [https://twitter.com/OdeToCode](https://twitter.com/OdeToCode).</span></span>
