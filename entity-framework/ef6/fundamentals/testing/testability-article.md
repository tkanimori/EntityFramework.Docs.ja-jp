---
title: テストの容易性と Entity Framework 4.0
author: divega
ms.date: 10/23/2016
ms.assetid: 9430e2ab-261c-4e8e-8545-2ebc52d7a247
ms.openlocfilehash: aec177438004fd255bef85a5e5047cf6b5a6f782
ms.sourcegitcommit: 269c8a1a457a9ad27b4026c22c4b1a76991fb360
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2018
ms.locfileid: "46284045"
---
# <a name="testability-and-entity-framework-40"></a><span data-ttu-id="8098c-102">テストの容易性と Entity Framework 4.0</span><span class="sxs-lookup"><span data-stu-id="8098c-102">Testability and Entity Framework 4.0</span></span>
<span data-ttu-id="8098c-103">Scott Allen</span><span class="sxs-lookup"><span data-stu-id="8098c-103">Scott Allen</span></span>

<span data-ttu-id="8098c-104">公開日: 2010 年 5 月</span><span class="sxs-lookup"><span data-stu-id="8098c-104">Published: May 2010</span></span>

## <a name="introduction"></a><span data-ttu-id="8098c-105">はじめに</span><span class="sxs-lookup"><span data-stu-id="8098c-105">Introduction</span></span>

<span data-ttu-id="8098c-106">このホワイト ペーパーでは、について説明し、ADO.NET Entity Framework 4.0 と Visual Studio 2010 でのテストが容易なコードを記述する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="8098c-106">This white paper describes and demonstrates how to write testable code with the ADO.NET Entity Framework 4.0 and Visual Studio 2010.</span></span> <span data-ttu-id="8098c-107">このホワイト ペーパーは、テスト駆動型設計 (TDD) や動作駆動型設計 (BDD) などの特定のテスト手法に重点を試みません。</span><span class="sxs-lookup"><span data-stu-id="8098c-107">This paper does not try to focus on a specific testing methodology, like test-driven design (TDD) or behavior-driven design (BDD).</span></span> <span data-ttu-id="8098c-108">代わりにこのホワイト ペーパーは、ADO.NET Entity Framework を使用してまだを特定し、自動でテストしやすいコードを記述する方法について学びます。</span><span class="sxs-lookup"><span data-stu-id="8098c-108">Instead this paper will focus on how to write code that uses the ADO.NET Entity Framework yet remains easy to isolate and test in an automated fashion.</span></span> <span data-ttu-id="8098c-109">データ アクセス シナリオのテストを容易になり、フレームワークを使用するときに、これらのパターンを適用する方法について一般的な設計パターンに注目します。</span><span class="sxs-lookup"><span data-stu-id="8098c-109">We’ll look at common design patterns that facilitate testing in data access scenarios and see how to apply those patterns when using the framework.</span></span> <span data-ttu-id="8098c-110">これらの機能をテストしやすいコードに機能させる方法を確認するフレームワークの特定の機能についても確認します。</span><span class="sxs-lookup"><span data-stu-id="8098c-110">We’ll also look at specific features of the framework to see how those features can work in testable code.</span></span>

## <a name="what-is-testable-code"></a><span data-ttu-id="8098c-111">テストが容易なコードとは何ですか。</span><span class="sxs-lookup"><span data-stu-id="8098c-111">What Is Testable Code?</span></span>

<span data-ttu-id="8098c-112">自動化された単体テストを使用してソフトウェアを確認する機能では、多くの望ましいメリットは提供します。</span><span class="sxs-lookup"><span data-stu-id="8098c-112">The ability to verify a piece of software using automated unit tests offers many desirable benefits.</span></span> <span data-ttu-id="8098c-113">すべてのユーザーには、適切なテストがアプリケーションと増加インプレース単体テストを導入することで、アプリケーションの品質がバグを発見してだけ自体よりもはるかにソフトウェアの不具合の数を減らすことが認識しています。</span><span class="sxs-lookup"><span data-stu-id="8098c-113">Everyone knows that good tests will reduce the number of software defects in an application and increase the application’s quality - but having unit tests in place goes far beyond just finding bugs.</span></span>

<span data-ttu-id="8098c-114">適切な単体テスト スイートには、時間を節約し、制御が作成したソフトウェアを開発チームができます。</span><span class="sxs-lookup"><span data-stu-id="8098c-114">A good unit test suite allows a development team to save time and remain in control of the software they create.</span></span> <span data-ttu-id="8098c-115">チームは、既存のコード、リファクタリングの再設計に変更を加えることができ、新しい要件を満たし、テスト スイートを把握しながら、アプリケーションに新しいコンポーネントを追加するソフトウェアを再構築は、アプリケーションの動作を確認できます。</span><span class="sxs-lookup"><span data-stu-id="8098c-115">A team can make changes to existing code, refactor, redesign, and restructure software to meet new requirements, and add new components into an application all while knowing the test suite can verify the application’s behavior.</span></span> <span data-ttu-id="8098c-116">単体テストは、変更を容易になり、複雑さの増大に応じて、ソフトウェアの保守容易性を維持する簡単なフィードバック サイクルの一部です。</span><span class="sxs-lookup"><span data-stu-id="8098c-116">Unit tests are part of a quick feedback cycle to facilitate change and preserve the maintainability of software as complexity increases.</span></span>

<span data-ttu-id="8098c-117">ユニット テストには、価格、ただしします。</span><span class="sxs-lookup"><span data-stu-id="8098c-117">Unit testing comes with a price, however.</span></span> <span data-ttu-id="8098c-118">チームを作成して単体テストを管理する時間を費やす必要があります。</span><span class="sxs-lookup"><span data-stu-id="8098c-118">A team has to invest the time to create and maintain unit tests.</span></span> <span data-ttu-id="8098c-119">これらのテストを作成するために必要な作業量に直接関連する、 **testability**の基になるソフトウェアです。</span><span class="sxs-lookup"><span data-stu-id="8098c-119">The amount of effort required to create these tests is directly related to the **testability** of the underlying software.</span></span> <span data-ttu-id="8098c-120">テストするソフトウェアを簡単にですか。</span><span class="sxs-lookup"><span data-stu-id="8098c-120">How easy is the software to test?</span></span> <span data-ttu-id="8098c-121">ソフトウェアのテストの容易性を考慮して設計チームは、されていないテスト可能なソフトウェアを使用するチームよりも高速効果的なテストを作成します。</span><span class="sxs-lookup"><span data-stu-id="8098c-121">A team designing software with testability in mind will create effective tests faster than the team working with un-testable software.</span></span>

<span data-ttu-id="8098c-122">Microsoft では、テストの容易性を考慮して、ADO.NET Entity Framework 4.0 (EF4) が設計されています。</span><span class="sxs-lookup"><span data-stu-id="8098c-122">Microsoft designed the ADO.NET Entity Framework 4.0 (EF4) with testability in mind.</span></span> <span data-ttu-id="8098c-123">これは開発者が単体テスト フレームワーク コード自体を記述するという意味しません。</span><span class="sxs-lookup"><span data-stu-id="8098c-123">This doesn’t mean developers will be writing unit tests against framework code itself.</span></span> <span data-ttu-id="8098c-124">代わりに、EF4 のテストの容易性の目標を使用すると、簡単にビルド フレームワークの上にテストしやすいコードを作成できます。</span><span class="sxs-lookup"><span data-stu-id="8098c-124">Instead, the testability goals for EF4 make it easy to create testable code that builds on top of the framework.</span></span> <span data-ttu-id="8098c-125">具体的な例を見ると、前に、テストしやすいコードの品質を理解することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="8098c-125">Before we look at specific examples, it’s worthwhile to understand the qualities of testable code.</span></span>

### <a name="the-qualities-of-testable-code"></a><span data-ttu-id="8098c-126">テスト可能なコードの品質評価</span><span class="sxs-lookup"><span data-stu-id="8098c-126">The Qualities of Testable Code</span></span>

<span data-ttu-id="8098c-127">コードを簡単にテストできますが、少なくとも 2 つの特徴を示す常にします。</span><span class="sxs-lookup"><span data-stu-id="8098c-127">Code that is easy to test will always exhibit at least two traits.</span></span> <span data-ttu-id="8098c-128">まず、テスト可能なコードが簡単に**観察**します。</span><span class="sxs-lookup"><span data-stu-id="8098c-128">First, testable code is easy to **observe**.</span></span> <span data-ttu-id="8098c-129">入力のいくつかのセットを指定するには、ことが容易に、コードの出力を確認できます。</span><span class="sxs-lookup"><span data-stu-id="8098c-129">Given some set of inputs, it should be easy to observe the output of the code.</span></span> <span data-ttu-id="8098c-130">たとえば、次のメソッドのテストは簡単、メソッドが直接計算の結果を返すためです。</span><span class="sxs-lookup"><span data-stu-id="8098c-130">For example, testing the following method is easy because the method directly returns the result of a calculation.</span></span>

``` csharp
    public int Add(int x, int y) {
        return x + y;
    }
```

<span data-ttu-id="8098c-131">メソッドをテストすることは、ネットワーク ソケット、データベース テーブル、または次のコードのようなファイルに、計算された値を書き込む場合は困難です。</span><span class="sxs-lookup"><span data-stu-id="8098c-131">Testing a method is difficult if the method writes the computed value into a network socket, a database table, or a file like the following code.</span></span> <span data-ttu-id="8098c-132">テストは、値を取得する追加の作業を実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8098c-132">The test has to perform additional work to retrieve the value.</span></span>

``` csharp
    public void AddAndSaveToFile(int x, int y) {
         var results = string.Format("The answer is {0}", x + y);
         File.WriteAllText("results.txt", results);
    }
```

<span data-ttu-id="8098c-133">次に、テストしやすいコードが簡単に**分離**します。</span><span class="sxs-lookup"><span data-stu-id="8098c-133">Secondly, testable code is easy to **isolate**.</span></span> <span data-ttu-id="8098c-134">次の擬似コードを使用してテストしやすいコードの悪い例としてしましょう。</span><span class="sxs-lookup"><span data-stu-id="8098c-134">Let’s use the following pseudo-code as a bad example of testable code.</span></span>

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

<span data-ttu-id="8098c-135">メソッドが容易に確認できます: 保険ポリシーで合格し、戻り値が予期される結果と一致することを確認します。</span><span class="sxs-lookup"><span data-stu-id="8098c-135">The method is easy to observe – we can pass in an insurance policy and verify the return value matches an expected result.</span></span> <span data-ttu-id="8098c-136">ただし、方法をテストする適切なスキーマにインストールされているデータベースがあり、メソッドが、電子メールを送信しようとしています。 場合に、SMTP サーバーを構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8098c-136">However, to test the method we’ll need to have a database installed with the correct schema, and configure the SMTP server in case the method tries to send an email.</span></span>

<span data-ttu-id="8098c-137">単体テストは、メソッド内で計算ロジックの検証にだけ関心がありますが、電子メール サーバーがオフライン、またはデータベース サーバーに移動しているため、テストが失敗します。</span><span class="sxs-lookup"><span data-stu-id="8098c-137">The unit test only wants to verify the calculation logic inside the method, but the test might fail because the email server is offline, or because the database server moved.</span></span> <span data-ttu-id="8098c-138">このようなエラーの両方は、テストしたいことを確認の動作に関連しません。</span><span class="sxs-lookup"><span data-stu-id="8098c-138">Both of these failures are unrelated to the behavior the test wants to verify.</span></span> <span data-ttu-id="8098c-139">動作は、特定が困難です。</span><span class="sxs-lookup"><span data-stu-id="8098c-139">The behavior is difficult to isolate.</span></span>

<span data-ttu-id="8098c-140">多くの場合、テストしやすいコードを記述するソフトウェア開発者は、書き込むことが、コード内では関心の分離の維持に努めています。</span><span class="sxs-lookup"><span data-stu-id="8098c-140">Software developers who strive to write testable code often strive to maintain a separation of concerns in the code they write.</span></span> <span data-ttu-id="8098c-141">上記のメソッドは、ビジネスの計算に集中し、他のコンポーネントにデータベース、および電子メールの実装の詳細を委任する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8098c-141">The above method should focus on the business calculations and delegate the database and email implementation details to other components.</span></span> <span data-ttu-id="8098c-142">Robert C. Martin はこれ単一責任の原則を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="8098c-142">Robert C. Martin calls this the Single Responsibility Principle.</span></span> <span data-ttu-id="8098c-143">オブジェクトは、ポリシーの値を計算するように、1 つ、幅が狭い責任をカプセル化する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8098c-143">An object should encapsulate a single, narrow responsibility, like calculating the value of a policy.</span></span> <span data-ttu-id="8098c-144">データベースと通知の他のすべての作業を他のオブジェクトの責任となります。</span><span class="sxs-lookup"><span data-stu-id="8098c-144">All other database and notification work should be the responsibility of some other object.</span></span> <span data-ttu-id="8098c-145">このような方法で記述されたコードは、1 つのタスクにフォーカスがあるため、分離に簡単です。</span><span class="sxs-lookup"><span data-stu-id="8098c-145">Code written in this fashion is easier to isolate because it is focused on a single task.</span></span>

<span data-ttu-id="8098c-146">.NET では、単一責任の原則に従い、分離を実現する必要があります、抽象化があります。</span><span class="sxs-lookup"><span data-stu-id="8098c-146">In .NET we have the abstractions we need to follow the Single Responsibility Principle and achieve isolation.</span></span> <span data-ttu-id="8098c-147">インターフェイス定義を使用してを具象型ではなく、インターフェイスの抽象化を使用するコードを強制できます。</span><span class="sxs-lookup"><span data-stu-id="8098c-147">We can use interface definitions and force the code to use the interface abstraction instead of a concrete type.</span></span> <span data-ttu-id="8098c-148">このホワイト ペーパーの後半で見て上で示した悪い例を使用できるようにメソッドをインターフェイスをどのように*検索*データベースに連絡があるようにします。</span><span class="sxs-lookup"><span data-stu-id="8098c-148">Later in this paper we’ll see how a method like the bad example presented above can work with interfaces that *look* like they will talk to the database.</span></span> <span data-ttu-id="8098c-149">ただし、テスト時には、データベースと通信しませんが、代わりにデータをメモリに保持するダミーの実装を置き換えるできます。</span><span class="sxs-lookup"><span data-stu-id="8098c-149">At test time, however, we can substitute a dummy implementation that doesn’t talk to the database but instead holds data in memory.</span></span> <span data-ttu-id="8098c-150">このダミー実装は、データ アクセス コードまたはデータベースの構成関連のない問題が原因でコードが分離されます。</span><span class="sxs-lookup"><span data-stu-id="8098c-150">This dummy implementation will isolate the code from unrelated problems in the data access code or database configuration.</span></span>

<span data-ttu-id="8098c-151">その他の分離の利点があります。</span><span class="sxs-lookup"><span data-stu-id="8098c-151">There are additional benefits to isolation.</span></span> <span data-ttu-id="8098c-152">最後のメソッドでのビジネスの計算を実行するには数ミリ秒かかるだけが、テスト自体は、さまざまなサーバーをネットワークとプレゼンテーションを囲むコード ホップとしての数秒間の実行可能性があります。</span><span class="sxs-lookup"><span data-stu-id="8098c-152">The business calculation in the last method should only take a few milliseconds to execute, but the test itself might run for several seconds as the code hops around the network and talks to various servers.</span></span> <span data-ttu-id="8098c-153">単体テストは、小さな変更を容易に高速に実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8098c-153">Unit tests should run fast to facilitate small changes.</span></span> <span data-ttu-id="8098c-154">単体テストも繰り返し使用できるし、テストとは無関係なコンポーネントの問題があるため、失敗する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8098c-154">Unit tests should also be repeatable and not fail because a component unrelated to the test has a problem.</span></span> <span data-ttu-id="8098c-155">コードのテストの記述を簡単に時間がある開発者が容易に確認し、分離コードの意味を記述、テストを実行するを待つ時間が短縮しより重要なは、存在しないバグを追跡する時間が短縮します。</span><span class="sxs-lookup"><span data-stu-id="8098c-155">Writing code that is easy to observe and to isolate means developers will have an easier time writing tests for the code, spend less time waiting for tests to execute, and more importantly, spend less time tracking down bugs that do not exist.</span></span>

<span data-ttu-id="8098c-156">うまくいけばテストのメリットを評価し、テストしやすいコードを示す特性を理解できます。</span><span class="sxs-lookup"><span data-stu-id="8098c-156">Hopefully you can appreciate the benefits of testing and understand the qualities that testable code exhibits.</span></span> <span data-ttu-id="8098c-157">監視可能な簡単に分離すると、残りの中に、データベースにデータを保存する EF4 で動作するコードを記述する方法について説明しようとしていますが、まずデータ アクセスのテストが容易な設計の説明について見てを絞り込みます。</span><span class="sxs-lookup"><span data-stu-id="8098c-157">We are about to address how to write code that works with EF4 to save data into a database while remaining observable and easy to isolate, but first we’ll narrow our focus to discuss testable designs for data access.</span></span>

## <a name="design-patterns-for-data-persistence"></a><span data-ttu-id="8098c-158">データの永続性の設計パターン</span><span class="sxs-lookup"><span data-stu-id="8098c-158">Design Patterns for Data Persistence</span></span>

<span data-ttu-id="8098c-159">不良の前に示した例の両方には、多くの責任が必要があります。</span><span class="sxs-lookup"><span data-stu-id="8098c-159">Both of the bad examples presented earlier had too many responsibilities.</span></span> <span data-ttu-id="8098c-160">無効な最初の例は、計算を実行する必要がある*と*ファイルに書き込みます。</span><span class="sxs-lookup"><span data-stu-id="8098c-160">The first bad example had to perform a calculation *and* write to a file.</span></span> <span data-ttu-id="8098c-161">不適切な 2 番目の例は、データベースからデータを読み取る必要がある*と*ビジネス計算を実行する*と*電子メールを送信します。</span><span class="sxs-lookup"><span data-stu-id="8098c-161">The second bad example had to read data from a database *and* perform a business calculation *and* send email.</span></span> <span data-ttu-id="8098c-162">懸念事項を分離し、他のコンポーネントに責任を委任する方法を小さくデザインしてテストしやすいコードを記述する方向に大きなメリットをもたらすことになります。</span><span class="sxs-lookup"><span data-stu-id="8098c-162">By designing smaller methods that separate concerns and delegate responsibility to other components you’ll make great strides towards writing testable code.</span></span> <span data-ttu-id="8098c-163">目標は、小規模およびフォーカスのある抽象化からアクションを作成することによって機能を構築します。</span><span class="sxs-lookup"><span data-stu-id="8098c-163">The goal is to build functionality by composing actions from small and focused abstractions.</span></span>

<span data-ttu-id="8098c-164">データ永続化する小さなと探しているフォーカスがあるの抽象化は一般的なので、設計パターンとして文書化されてしました。</span><span class="sxs-lookup"><span data-stu-id="8098c-164">When it comes to data persistence the small and focused abstractions we are looking for are so common they’ve been documented as design patterns.</span></span> <span data-ttu-id="8098c-165">Martin Fowler の書籍のエンタープライズ アプリケーション アーキテクチャのパターンでは、印刷でこれらのパターンを記述する最初の作業をしました。</span><span class="sxs-lookup"><span data-stu-id="8098c-165">Martin Fowler’s book Patterns of Enterprise Application Architecture was the first work to describe these patterns in print.</span></span> <span data-ttu-id="8098c-166">これらの ADO.NET Entity Framework を実装およびこれらのパターンと連携する方法を紹介する前にように次のセクションでは、これらのパターンの簡単な説明を提供します。</span><span class="sxs-lookup"><span data-stu-id="8098c-166">We’ll provide a brief description of these patterns in the following sections before we show how these ADO.NET Entity Framework implements and works with these patterns.</span></span>

### <a name="the-repository-pattern"></a><span data-ttu-id="8098c-167">リポジトリ パターン</span><span class="sxs-lookup"><span data-stu-id="8098c-167">The Repository Pattern</span></span>

<span data-ttu-id="8098c-168">Fowler は、リポジトリ「間を仲介のドメインとデータ コレクションのようなインターフェイスを使用して、ドメイン オブジェクトにアクセスするためのマッピング レイヤー」を述べています。</span><span class="sxs-lookup"><span data-stu-id="8098c-168">Fowler says a repository “mediates between the domain and data mapping layers using a collection-like interface for accessing domain objects”.</span></span> <span data-ttu-id="8098c-169">リポジトリ パターンの目的は、データのアクセスの些細からコードを分離して、分離以前説明したように、テストの容易性に必要な特徴です。</span><span class="sxs-lookup"><span data-stu-id="8098c-169">The goal of the repository pattern is to isolate code from the minutiae of data access, and as we saw earlier isolation is a required trait for testability.</span></span>

<span data-ttu-id="8098c-170">分離、重要では、リポジトリがコレクションのようなインターフェイスを使用してオブジェクトを公開する方法です。</span><span class="sxs-lookup"><span data-stu-id="8098c-170">The key to the isolation is how the repository exposes objects using a collection-like interface.</span></span> <span data-ttu-id="8098c-171">リポジトリには考え方がない方法を使用して書き込むロジック、リポジトリは要求したオブジェクトを具体化します。</span><span class="sxs-lookup"><span data-stu-id="8098c-171">The logic you write to use the repository has no idea how the repository will materialize the objects you request.</span></span> <span data-ttu-id="8098c-172">リポジトリが、データベースと通信またはだけメモリ内コレクションからオブジェクトを返すこと可能性があります。</span><span class="sxs-lookup"><span data-stu-id="8098c-172">The repository might talk to a database, or it might just return objects from an in-memory collection.</span></span> <span data-ttu-id="8098c-173">コードを知る必要があります。 すべては、コレクションを維持するために、リポジトリが表示されます、取得、追加、およびコレクションからオブジェクトを削除できます。</span><span class="sxs-lookup"><span data-stu-id="8098c-173">All your code needs to know is that the repository appears to maintain the collection, and you can retrieve, add, and delete objects from the collection.</span></span>

<span data-ttu-id="8098c-174">既存の .NET アプリケーションで具体的なリポジトリは、多くの場合、次のようなジェネリック インターフェイスから継承します。</span><span class="sxs-lookup"><span data-stu-id="8098c-174">In existing .NET applications a concrete repository often inherits from a generic interface like the following:</span></span>

``` csharp
    public interface IRepository<T> {       
        IEnumerable<T> FindAll();
        IEnumerable<T> FindBy(Expression<Func\<T, bool>> predicate);
        T FindById(int id);
        void Add(T newEntity);
        void Remove(T entity);
    }
```

<span data-ttu-id="8098c-175">EF4 の実装を提供しますが、基本的な概念は変わりませんインターフェイス定義にいくつかの変更を行ったします。</span><span class="sxs-lookup"><span data-stu-id="8098c-175">We’ll make a few changes to the interface definition when we provide an implementation for EF4, but the basic concept remains the same.</span></span> <span data-ttu-id="8098c-176">コードでは、このインターフェイスを実装する具体的なリポジトリを使用して、述語の評価に基づいてエンティティのコレクションを取得する、主キーの値でエンティティを取得したり、単に使用できるすべてのエンティティを取得することができます。</span><span class="sxs-lookup"><span data-stu-id="8098c-176">Code can use a concrete repository implementing this interface to retrieve an entity by its primary key value, to retrieve a collection of entities based on the evaluation of a predicate, or simply retrieve all available entities.</span></span> <span data-ttu-id="8098c-177">コードを追加し、リポジトリ インターフェイスを使用してエンティティの削除もできます。</span><span class="sxs-lookup"><span data-stu-id="8098c-177">The code can also add and remove entities through the repository interface.</span></span>

<span data-ttu-id="8098c-178">IRepository の Employee オブジェクトを指定するには、コードは、次の操作を実行できます。</span><span class="sxs-lookup"><span data-stu-id="8098c-178">Given an IRepository of Employee objects, code can perform the following operations.</span></span>

``` csharp
    var employeesNamedScott =
        repository
            .FindBy(e => e.Name == "Scott")
            .OrderBy(e => e.HireDate);
    var firstEmployee = repository.FindById(1);
    var newEmployee = new Employee() {/*... */};
    repository.Add(newEmployee);
```

<span data-ttu-id="8098c-179">インターフェイス (IRepository の従業員) コードを使用しているため、コード、インターフェイスのさまざまな実装を提供できます。</span><span class="sxs-lookup"><span data-stu-id="8098c-179">Since the code is using an interface (IRepository of Employee), we can provide the code with different implementations of the interface.</span></span> <span data-ttu-id="8098c-180">1 つの実装には、EF4 と永続化するオブジェクト、Microsoft SQL Server データベースに基づく実装可能性があります。</span><span class="sxs-lookup"><span data-stu-id="8098c-180">One implementation might be an implementation backed by EF4 and persisting objects into a Microsoft SQL Server database.</span></span> <span data-ttu-id="8098c-181">(1 つのテスト中に使用して)、さまざまな実装は、メモリ内の従業員の一覧のオブジェクトによってバックアップ可能性があります。</span><span class="sxs-lookup"><span data-stu-id="8098c-181">A different implementation (one we use during testing) might be backed by an in-memory List of Employee objects.</span></span> <span data-ttu-id="8098c-182">インターフェイスは、コードでの分離を実現するために役立ちます。</span><span class="sxs-lookup"><span data-stu-id="8098c-182">The interface will help to achieve isolation in the code.</span></span>

<span data-ttu-id="8098c-183">IRepository に注意してください&lt;T&gt;インターフェイスは、保存操作を公開しません。</span><span class="sxs-lookup"><span data-stu-id="8098c-183">Notice the IRepository&lt;T&gt; interface does not expose a Save operation.</span></span> <span data-ttu-id="8098c-184">既存のオブジェクトを更新する方法</span><span class="sxs-lookup"><span data-stu-id="8098c-184">How do we update existing objects?</span></span> <span data-ttu-id="8098c-185">IRepository 定義が含まれて、保存操作が遭遇する可能性があり、これらのリポジトリの実装が直ちにデータベースにオブジェクトを保存する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8098c-185">You might come across IRepository definitions that do include the Save operation, and implementations of these repositories will need to immediately persist an object into the database.</span></span> <span data-ttu-id="8098c-186">ただし、多くのアプリケーションでオブジェクトを個別に保存するはありません。</span><span class="sxs-lookup"><span data-stu-id="8098c-186">However, in many applications we don’t want to persist objects individually.</span></span> <span data-ttu-id="8098c-187">代わりに、オブジェクトをライフ、おそらく、別のリポジトリから、ビジネス アクティビティの一部としてこれらのオブジェクトを変更し、1 つのアトミック操作の一部としてすべてのオブジェクトを保持します。</span><span class="sxs-lookup"><span data-stu-id="8098c-187">Instead, we want to bring objects to life, perhaps from different repositories, modify those objects as part of a business activity, and then persist all the objects as part of a single, atomic operation.</span></span> <span data-ttu-id="8098c-188">さいわい、この種類の動作を許可するためのパターンがあります。</span><span class="sxs-lookup"><span data-stu-id="8098c-188">Fortunately, there is a pattern to allow this type of behavior.</span></span>

### <a name="the-unit-of-work-pattern"></a><span data-ttu-id="8098c-189">Unit of Work パターン</span><span class="sxs-lookup"><span data-stu-id="8098c-189">The Unit of Work Pattern</span></span>

<span data-ttu-id="8098c-190">Fowler は、作業単位が「の管理オブジェクトの一覧がビジネス トランザクションによって影響を受けるし、変更の書き込みと同時実行の問題の解決を調整」を述べています。</span><span class="sxs-lookup"><span data-stu-id="8098c-190">Fowler says a unit of work will “maintain a list of objects affected by a business transaction and coordinates the writing out of changes and the resolution of concurrency problems”.</span></span> <span data-ttu-id="8098c-191">リポジトリからを実現し、お知らせ、unit of work、変更をコミットするときに、オブジェクトになりました変更を保存、オブジェクトに対する変更を追跡する作業の単位の役目です。</span><span class="sxs-lookup"><span data-stu-id="8098c-191">It is the responsibility of the unit of work to track changes to the objects we bring to life from a repository and persist any changes we’ve made to the objects when we tell the unit of work to commit the changes.</span></span> <span data-ttu-id="8098c-192">新しいオブジェクトのすべてのリポジトリに追加したし、データベース、およびも管理の削除に、オブジェクトを挿入する作業の単位の責任です。</span><span class="sxs-lookup"><span data-stu-id="8098c-192">It’s also the responsibility of the unit of work to take the new objects we’ve added to all repositories and insert the objects into a database, and also mange deletion.</span></span>

<span data-ttu-id="8098c-193">ADO.NET データセットでこれまでの作業を実行した場合を既に理解しておく、unit of work パターン。</span><span class="sxs-lookup"><span data-stu-id="8098c-193">If you’ve ever done any work with ADO.NET DataSets then you’ll already be familiar with the unit of work pattern.</span></span> <span data-ttu-id="8098c-194">ADO.NET データセット、更新、削除、および DataRow オブジェクトの挿入を追跡する機能がありでした (TableAdapter のヘルプ) の調整、すべての変更をデータベースにします。</span><span class="sxs-lookup"><span data-stu-id="8098c-194">ADO.NET DataSets had the ability to track our updates, deletions, and insertion of DataRow objects and could (with the help of a TableAdapter) reconcile all our changes to a database.</span></span> <span data-ttu-id="8098c-195">ただし、DataSet オブジェクト モデルの基になるデータベースのサブセットを切断します。</span><span class="sxs-lookup"><span data-stu-id="8098c-195">However, DataSet objects model a disconnected subset of the underlying database.</span></span> <span data-ttu-id="8098c-196">Unit of work パターンは、ビジネス オブジェクトやデータ アクセス コードから分離されており、データベースを認識しないドメイン オブジェクトが、同じ動作を示します。</span><span class="sxs-lookup"><span data-stu-id="8098c-196">The unit of work pattern exhibits the same behavior, but works with business objects and domain objects that are isolated from data access code and unaware of the database.</span></span>

<span data-ttu-id="8098c-197">.NET コードでの作業単位をモデル化する抽象化は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="8098c-197">An abstraction to model the unit of work in .NET code might look like the following:</span></span>

``` csharp
    public interface IUnitOfWork {
        IRepository<Employee> Employees { get; }
        IRepository<Order> Orders { get; }
        IRepository<Customer> Customers { get; }
        void Commit();
    }
```

<span data-ttu-id="8098c-198">作業の 1 つの単位を確認できる作業単位からのリポジトリの参照を公開することでは、オブジェクトは、ビジネス トランザクション中に具体化されたすべてのエンティティを追跡する機能を持ちます。</span><span class="sxs-lookup"><span data-stu-id="8098c-198">By exposing repository references from the unit of work we can ensure a single unit of work object has the ability to track all entities materialized during a business transaction.</span></span> <span data-ttu-id="8098c-199">実際の作業単位の Commit メソッドの実装をデータベースにメモリ内の変更を調整するのには、すべてのマジックが行われる場所です。</span><span class="sxs-lookup"><span data-stu-id="8098c-199">The implementation of the Commit method for a real unit of work is where all the magic happens to reconcile in-memory changes with the database.</span></span> 

<span data-ttu-id="8098c-200">IUnitOfWork の参照を指定すると、コードは 1 つまたは複数のリポジトリから取得されたビジネス オブジェクトに変更を加えるし、アトミックなコミット操作を使用してすべての変更を保存します。</span><span class="sxs-lookup"><span data-stu-id="8098c-200">Given an IUnitOfWork reference, code can make changes to business objects retrieved from one or more repositories and save all the changes using the atomic Commit operation.</span></span>

``` csharp
    var firstEmployee = unitofWork.Employees.FindById(1);
    var firstCustomer = unitofWork.Customers.FindById(1);
    firstEmployee.Name = "Alex";
    firstCustomer.Name = "Christopher";
    unitofWork.Commit();
```

### <a name="the-lazy-load-pattern"></a><span data-ttu-id="8098c-201">遅延読み込みのパターン</span><span class="sxs-lookup"><span data-stu-id="8098c-201">The Lazy Load Pattern</span></span>

<span data-ttu-id="8098c-202">Fowler は、名前の遅延読み込みを使用して、「オブジェクトのすべてのデータが含まれていないをする必要がありますがそれを取得する方法を知っている」について説明します。</span><span class="sxs-lookup"><span data-stu-id="8098c-202">Fowler uses the name lazy load to describe “an object that doesn’t contain all of the data you need but knows how to get it”.</span></span> <span data-ttu-id="8098c-203">透過的な遅延読み込みするときに重要な機能は、テスト可能なビジネスのコードを記述し、リレーショナル データベースを使用します。</span><span class="sxs-lookup"><span data-stu-id="8098c-203">Transparent lazy loading is an important feature to have when writing testable business code and working with a relational database.</span></span> <span data-ttu-id="8098c-204">たとえば、次のコードを検討してください。</span><span class="sxs-lookup"><span data-stu-id="8098c-204">As an example, consider the following code.</span></span>

``` csharp
    var employee = repository.FindById(id);
    // ... and later ...
    foreach(var timeCard in employee.TimeCards) {
        // .. manipulate the timeCard
    }
```

<span data-ttu-id="8098c-205">タイムカードのコレクションの設定方法</span><span class="sxs-lookup"><span data-stu-id="8098c-205">How is the TimeCards collection populated?</span></span> <span data-ttu-id="8098c-206">2 つの解答があります。</span><span class="sxs-lookup"><span data-stu-id="8098c-206">There are two possible answers.</span></span> <span data-ttu-id="8098c-207">1 つの答えは、従業員をフェッチするように求められたら、従業員のリポジトリが両方と関連付けられているタイム カードについては、従業員の従業員を取得するクエリを発行します。</span><span class="sxs-lookup"><span data-stu-id="8098c-207">One answer is that the employee repository, when asked to fetch an employee, issues a query to retrieve both the employee along with the employee’s associated time card information.</span></span> <span data-ttu-id="8098c-208">一般に結合句を使用したクエリが要求され、アプリケーションより多くの情報の取得中に発生する可能性があります、リレーショナル データベースで必要があります。</span><span class="sxs-lookup"><span data-stu-id="8098c-208">In relational databases this generally requires a query with a JOIN clause and may result in retrieving more information than an application needs.</span></span> <span data-ttu-id="8098c-209">場合、アプリケーション タイムカードを実際のプロパティをタッチする必要はありませんか。</span><span class="sxs-lookup"><span data-stu-id="8098c-209">What if the application never needs to touch the TimeCards property?</span></span>

<span data-ttu-id="8098c-210">2 番目の応答では、"オンデマンドで"タイムカードを実際のプロパティを読み込めません。</span><span class="sxs-lookup"><span data-stu-id="8098c-210">A second answer is to load the TimeCards property “on demand”.</span></span> <span data-ttu-id="8098c-211">この遅延読み込みでは、暗黙の型とビジネス ロジックに透過的なコードがタイム カード情報を取得する特殊な Api を呼び出さないためには。</span><span class="sxs-lookup"><span data-stu-id="8098c-211">This lazy loading is implicit and transparent to the business logic because the code does not invoke special APIs to retrieve time card information.</span></span> <span data-ttu-id="8098c-212">コードでは、タイム カード情報が必要なときに前提としています。</span><span class="sxs-lookup"><span data-stu-id="8098c-212">The code assumes the time card information is present when needed.</span></span> <span data-ttu-id="8098c-213">メソッドの呼び出しの実行時のインターセプトは一般に、遅延読み込みを伴う不思議な力です。</span><span class="sxs-lookup"><span data-stu-id="8098c-213">There is some magic involved with lazy loading that generally involves runtime interception of method invocations.</span></span> <span data-ttu-id="8098c-214">インターセプトのコードは、データベースと通信し、ビジネス ロジックを自由にビジネス ロジックはそのままタイム カード情報を取得する責任を負います。</span><span class="sxs-lookup"><span data-stu-id="8098c-214">The intercepting code is responsible for talking to the database and retrieving time card information while leaving the business logic free to be business logic.</span></span> <span data-ttu-id="8098c-215">この遅延読み込みマジックではデータ取得操作とより多くのテストが容易なコードの結果から自体を分離するビジネス コード。</span><span class="sxs-lookup"><span data-stu-id="8098c-215">This lazy load magic allows the business code to isolate itself from data retrieval operations and results in more testable code.</span></span>

<span data-ttu-id="8098c-216">アプリケーションを遅延読み込みの欠点は*は*コードでは、追加のクエリを実行します。 タイム カード情報が必要です。</span><span class="sxs-lookup"><span data-stu-id="8098c-216">The drawback to a lazy load is that when an application *does* need the time card information the code will execute an additional query.</span></span> <span data-ttu-id="8098c-217">そうでは問題になるパフォーマンス重視のアプリケーションまたは (問題 N + 1 と呼ばれます、ループの各反復中にタイム カードを取得するアプリケーションをさまざまな従業員のオブジェクトをループ処理し、クエリを実行するが、多くのアプリケーションクエリの問題の場合)、遅延読み込みは、ドラッグします。</span><span class="sxs-lookup"><span data-stu-id="8098c-217">This isn’t a concern for many applications, but for performance sensitive applications or applications looping through a number of employee objects and executing a query to retrieve time cards during each iteration of the loop (a problem often referred to as the N+1 query problem), lazy loading is a drag.</span></span> <span data-ttu-id="8098c-218">これらのシナリオで、アプリケーションが集中的に可能な最も効率的な方法でタイム カード情報を読み込むしようとする可能性があります。</span><span class="sxs-lookup"><span data-stu-id="8098c-218">In these scenarios an application might want to eagerly load time card information in the most efficient manner possible.</span></span>

<span data-ttu-id="8098c-219">さいわい、どのように EF4 が両方の暗黙的な遅延読み込みをサポートし、効率的な一括で読み込むように、次のセクションに移動し、これらのパターンを実装しましたが表示されます。</span><span class="sxs-lookup"><span data-stu-id="8098c-219">Fortunately, we’ll see how EF4 supports both implicit lazy loads and efficient eager loads as we move into the next section and implement these patterns.</span></span>

## <a name="implementing-patterns-with-the-entity-framework"></a><span data-ttu-id="8098c-220">Entity Framework でのパターンを実装します。</span><span class="sxs-lookup"><span data-stu-id="8098c-220">Implementing Patterns with the Entity Framework</span></span>

<span data-ttu-id="8098c-221">良い知らせは、最後のセクションで説明した設計パターンのすべてが EF4 で実装が簡単であります。</span><span class="sxs-lookup"><span data-stu-id="8098c-221">The good news is that all of the design patterns we described in the last section are straightforward to implement with EF4.</span></span> <span data-ttu-id="8098c-222">単純な ASP.NET MVC アプリケーションを使用して編集し、従業員とその関連タイム カード情報を表示するかを示します。</span><span class="sxs-lookup"><span data-stu-id="8098c-222">To demonstrate we are going to use a simple ASP.NET MVC application to edit and display Employees and their associated time card information.</span></span> <span data-ttu-id="8098c-223">次"plain old CLR object"を使用することから始めます (Poco)。</span><span class="sxs-lookup"><span data-stu-id="8098c-223">We’ll start by using the following “plain old CLR objects” (POCOs).</span></span> 

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

<span data-ttu-id="8098c-224">これらのクラス定義は、さまざまなアプローチと EF4 の機能について検証が永続性として依存 (PI) 可能な限りこれらのクラスを保持する目的は、若干変更されます。</span><span class="sxs-lookup"><span data-stu-id="8098c-224">These class definitions will change slightly as we explore different approaches and features of EF4, but the intent is to keep these classes as persistence ignorant (PI) as possible.</span></span> <span data-ttu-id="8098c-225">PI オブジェクトを知らない*方法*、あるいは*場合*を保持している状態が、データベース内に存在します。</span><span class="sxs-lookup"><span data-stu-id="8098c-225">A PI object doesn’t know *how*, or even *if*, the state it holds lives inside a database.</span></span> <span data-ttu-id="8098c-226">PI と Poco がテスト可能なソフトウェアが連携します。</span><span class="sxs-lookup"><span data-stu-id="8098c-226">PI and POCOs go hand in hand with testable software.</span></span> <span data-ttu-id="8098c-227">POCO アプローチを使用して、オブジェクトは、小さい制約付きより柔軟に簡単に存在するデータベースを使用せずに操作できるようにするためのテストです。</span><span class="sxs-lookup"><span data-stu-id="8098c-227">Objects using a POCO approach are less constrained, more flexible, and easier to test because they can operate without a database present.</span></span>

<span data-ttu-id="8098c-228">インプレース Poco Visual Studio で Entity Data Model (EDM) を作成できます (図 1 参照)。</span><span class="sxs-lookup"><span data-stu-id="8098c-228">With the POCOs in place we can create an Entity Data Model (EDM) in Visual Studio (see figure 1).</span></span> <span data-ttu-id="8098c-229">エンティティのコードを生成するのには、EDM を使用しません。</span><span class="sxs-lookup"><span data-stu-id="8098c-229">We will not use the EDM to generate code for our entities.</span></span> <span data-ttu-id="8098c-230">代わりに、手動で作成一目みてエンティティを使用します。</span><span class="sxs-lookup"><span data-stu-id="8098c-230">Instead, we want to use the entities we lovingly craft by hand.</span></span> <span data-ttu-id="8098c-231">データベース スキーマを生成し、EF4 をデータベースにオブジェクトをマップする必要があるメタデータを提供する、EDM にのみ使用します。</span><span class="sxs-lookup"><span data-stu-id="8098c-231">We will only use the EDM to generate our database schema and provide the metadata EF4 needs to map objects into the database.</span></span>

![ef test_01](~/ef6/media/eftest-01.jpg)

<span data-ttu-id="8098c-233">**図 1**</span><span class="sxs-lookup"><span data-stu-id="8098c-233">**Figure 1**</span></span>

<span data-ttu-id="8098c-234">注: まず EDM モデルを開発する場合は、クリーン、EDM から POCO コードを生成すること</span><span class="sxs-lookup"><span data-stu-id="8098c-234">Note: if you want to develop the EDM model first, it is possible to generate clean, POCO code from the EDM.</span></span> <span data-ttu-id="8098c-235">データ プログラマビリティ チームによって提供される Visual Studio 2010 拡張機能で、これを行うことができます。</span><span class="sxs-lookup"><span data-stu-id="8098c-235">You can do this with a Visual Studio 2010 extension provided by the Data Programmability team.</span></span> <span data-ttu-id="8098c-236">拡張機能をダウンロードするには、Visual Studio の [ツール] メニューから拡張機能マネージャーを起動し、"POCO"(図を参照して 2) のテンプレートのオンライン ギャラリーを検索します。</span><span class="sxs-lookup"><span data-stu-id="8098c-236">To download the extension, launch the Extension Manager from the Tools menu in Visual Studio and search the online gallery of templates for “POCO” (See figure 2).</span></span> <span data-ttu-id="8098c-237">POCO テンプレートがいくつかは EF 使用できます。</span><span class="sxs-lookup"><span data-stu-id="8098c-237">There are several POCO templates available for EF.</span></span> <span data-ttu-id="8098c-238">テンプレートの使用に関する詳細については、次を参照してください。"[チュートリアル: Entity Framework 用のテンプレート POCO](https://blogs.msdn.com/adonet/pages/walkthrough-poco-template-for-the-entity-framework.aspx)"。</span><span class="sxs-lookup"><span data-stu-id="8098c-238">For more information on using the template, see “ [Walkthrough: POCO Template for the Entity Framework](https://blogs.msdn.com/adonet/pages/walkthrough-poco-template-for-the-entity-framework.aspx)”.</span></span>

![ef test_02](~/ef6/media/eftest-02.png)

<span data-ttu-id="8098c-240">**図 2**</span><span class="sxs-lookup"><span data-stu-id="8098c-240">**Figure 2**</span></span>

<span data-ttu-id="8098c-241">この POCO の開始点からは 2 つの異なる方法をについて説明をテストしやすいコードです。</span><span class="sxs-lookup"><span data-stu-id="8098c-241">From this POCO starting point we will explore two different approaches to testable code.</span></span> <span data-ttu-id="8098c-242">最初のアプローチは呼び出す作業とリポジトリの単位を実装するために Entity Framework API から抽象化を利用するため、EF のアプローチです。</span><span class="sxs-lookup"><span data-stu-id="8098c-242">The first approach I call the EF approach because it leverages abstractions from the Entity Framework API to implement units of work and repositories.</span></span> <span data-ttu-id="8098c-243">2 番目の方法は、独自のカスタム リポジトリの抽象化を作成し、長所と短所のそれぞれの方法を参照してください。</span><span class="sxs-lookup"><span data-stu-id="8098c-243">In the second approach we will create our own custom repository abstractions and then see the advantages and disadvantages of each approach.</span></span> <span data-ttu-id="8098c-244">EF のアプローチを探索から始めます。</span><span class="sxs-lookup"><span data-stu-id="8098c-244">We’ll start by exploring the EF approach.</span></span>  

### <a name="an-ef-centric-implementation"></a><span data-ttu-id="8098c-245">EF の中心とした実装</span><span class="sxs-lookup"><span data-stu-id="8098c-245">An EF Centric Implementation</span></span>

<span data-ttu-id="8098c-246">ASP.NET MVC プロジェクトから次のコント ローラー アクションを検討してください。</span><span class="sxs-lookup"><span data-stu-id="8098c-246">Consider the following controller action from an ASP.NET MVC project.</span></span> <span data-ttu-id="8098c-247">アクションは、Employee オブジェクトを取得し、従業員の詳細なビューを表示する結果を返します。</span><span class="sxs-lookup"><span data-stu-id="8098c-247">The action retrieves an Employee object and returns a result to display a detailed view of the employee.</span></span>

``` csharp
    public ViewResult Details(int id) {
        var employee = _unitOfWork.Employees
                                  .Single(e => e.Id == id);
        return View(employee);
    }
```

<span data-ttu-id="8098c-248">テストが容易なコードですか。</span><span class="sxs-lookup"><span data-stu-id="8098c-248">Is the code testable?</span></span> <span data-ttu-id="8098c-249">アクションの動作を確認する必要がありますが、少なくとも 2 つのテストがあります。</span><span class="sxs-lookup"><span data-stu-id="8098c-249">There are at least two tests we’d need to verify the action’s behavior.</span></span> <span data-ttu-id="8098c-250">最初に、アクションを返します、正しいビュー – 簡単にテストすることを確認してみましょう。</span><span class="sxs-lookup"><span data-stu-id="8098c-250">First, we’d like to verify the action returns the correct view – an easy test.</span></span> <span data-ttu-id="8098c-251">アクションが正しい従業員を取得したいと思いますデータベース クエリを実行するコードの実行なしで行うことを確認するテストを記述することもなります。</span><span class="sxs-lookup"><span data-stu-id="8098c-251">We’d also want to write a test to verify the action retrieves the correct employee, and we’d like to do this without executing code to query the database.</span></span> <span data-ttu-id="8098c-252">テスト対象のコードを分離することを覚えておいてください。</span><span class="sxs-lookup"><span data-stu-id="8098c-252">Remember we want to isolate the code under test.</span></span> <span data-ttu-id="8098c-253">データ アクセス コードまたはデータベースの構成でのバグにより、テストが失敗しない分離が保証されます。</span><span class="sxs-lookup"><span data-stu-id="8098c-253">Isolation will ensure the test doesn’t fail because of a bug in the data access code or database configuration.</span></span> <span data-ttu-id="8098c-254">テストが失敗した場合、およびいくつかの下位レベルのシステム コンポーネントではなく、コント ローラー ロジックの不具合があるがわかります。</span><span class="sxs-lookup"><span data-stu-id="8098c-254">If the test fails, we will know we have a bug in the controller logic, and not in some lower level system component.</span></span>

<span data-ttu-id="8098c-255">分離を実現するために必要があります表示されるインターフェイスのようないくつかの抽象化前のリポジトリと作業単位です。</span><span class="sxs-lookup"><span data-stu-id="8098c-255">To achieve isolation we’ll need some abstractions like the interfaces we presented earlier for repositories and units of work.</span></span> <span data-ttu-id="8098c-256">リポジトリ パターンがドメイン オブジェクトとデータ マッピング レイヤーの間を仲介するように設計してください。</span><span class="sxs-lookup"><span data-stu-id="8098c-256">Remember the repository pattern is designed to mediate between domain objects and the data mapping layer.</span></span> <span data-ttu-id="8098c-257">このシナリオの EF4 で*は*データ マッピング レイヤー、および IObjectSet という名前のリポジトリのような抽象化で提供されている&lt;T&gt; (System.Data.Objects 名前空間) から。</span><span class="sxs-lookup"><span data-stu-id="8098c-257">In this scenario EF4 *is* the data mapping layer, and already provides a repository-like abstraction named IObjectSet&lt;T&gt; (from the System.Data.Objects namespace).</span></span> <span data-ttu-id="8098c-258">インターフェイス定義は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="8098c-258">The interface definition looks like the following.</span></span>

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

<span data-ttu-id="8098c-259">IObjectSet&lt;T&gt;オブジェクトのコレクションに似ているために、リポジトリの要件を満たしている (IEnumerable を使用して&lt;T&gt;) を追加し、シミュレートされたコレクションからオブジェクトを削除するメソッドを提供します。</span><span class="sxs-lookup"><span data-stu-id="8098c-259">IObjectSet&lt;T&gt; meets the requirements for a repository because it resembles a collection of objects (via IEnumerable&lt;T&gt;) and provides methods to add and remove objects from the simulated collection.</span></span> <span data-ttu-id="8098c-260">アタッチとデタッチの方法では、EF4 API の追加機能を公開します。</span><span class="sxs-lookup"><span data-stu-id="8098c-260">The Attach and Detach methods expose additional capabilities of the EF4 API.</span></span> <span data-ttu-id="8098c-261">IObjectSet を使用する&lt;T&gt;リポジトリのインターフェイスとして作業の抽象化の単位にリポジトリをまとめてバインドする必要があります。</span><span class="sxs-lookup"><span data-stu-id="8098c-261">To use IObjectSet&lt;T&gt; as the interface for repositories we need a unit of work abstraction to bind repositories together.</span></span>

``` csharp
    public interface IUnitOfWork {
        IObjectSet<Employee> Employees { get; }
        IObjectSet<TimeCard> TimeCards { get; }
        void Commit();
    }
```

<span data-ttu-id="8098c-262">このインターフェイスの 1 つの具象実装では、SQL Server と対話して、簡単に EF4 から ObjectContext クラスを使用して作成できます。</span><span class="sxs-lookup"><span data-stu-id="8098c-262">One concrete implementation of this interface will talk to SQL Server and is easy to create using the ObjectContext class from EF4.</span></span> <span data-ttu-id="8098c-263">ObjectContext クラスは、EF4 API での作業の実際の単位です。</span><span class="sxs-lookup"><span data-stu-id="8098c-263">The ObjectContext class is the real unit of work in the EF4 API.</span></span>

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

<span data-ttu-id="8098c-264">取り込む、IObjectSet&lt;T&gt;ライフを ObjectContext オブジェクトの CreateObjectSet メソッドを呼び出すだけです。</span><span class="sxs-lookup"><span data-stu-id="8098c-264">Bringing an IObjectSet&lt;T&gt; to life is as easy as invoking the CreateObjectSet method of the ObjectContext object.</span></span> <span data-ttu-id="8098c-265">バック グラウンドで、フレームワークは、メタデータを使用具象 ObjectSet を生成するために、EDM に用意されています&lt;T&gt;します。</span><span class="sxs-lookup"><span data-stu-id="8098c-265">Behind the scenes the framework will use the metadata we provided in the EDM to produce a concrete ObjectSet&lt;T&gt;.</span></span> <span data-ttu-id="8098c-266">ここで、IObjectSet を返すことで引き続き使用します&lt;T&gt;インターフェイスのクライアント コードでテストの容易性を保持するのに役立つためです。</span><span class="sxs-lookup"><span data-stu-id="8098c-266">We’ll stick with returning the IObjectSet&lt;T&gt; interface because it will help preserve testability in client code.</span></span>

<span data-ttu-id="8098c-267">この具体的な実装は、運用環境で便利ですが、IUnitOfWork の抽象化を使用して、テストを容易にする方法に注目する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8098c-267">This concrete implementation is useful in production, but we need to focus on how we’ll use our IUnitOfWork abstraction to facilitate testing.</span></span>

### <a name="the-test-doubles"></a><span data-ttu-id="8098c-268">テスト代替</span><span class="sxs-lookup"><span data-stu-id="8098c-268">The Test Doubles</span></span>

<span data-ttu-id="8098c-269">コント ローラー アクションを分離するには、実際の作業単位 (ObjectContext でサポート) と作業 (メモリ内の操作を実行する) のテスト ダブルか「偽」ユニット間で切り替えることができる必要があります。</span><span class="sxs-lookup"><span data-stu-id="8098c-269">To isolate the controller action we’ll need the ability to switch between the real unit of work (backed by an ObjectContext) and a test double or “fake” unit of work (performing in-memory operations).</span></span> <span data-ttu-id="8098c-270">この種類の切り替えを実行する一般的なアプローチでは、インスタンスの作業単位が代わりに、コンストラクター パラメーターとしてコント ローラーには、パスの作業単位、MVC コント ローラーを追加できません。</span><span class="sxs-lookup"><span data-stu-id="8098c-270">The common approach to perform this type of switching is to not let the MVC controller instantiate a unit of work, but instead pass the unit of work into the controller as a constructor parameter.</span></span>

``` csharp
    class EmployeeController : Controller {
      publicEmployeeController(IUnitOfWork unitOfWork)  {
          _unitOfWork = unitOfWork;
      }
      ...
    }
```

<span data-ttu-id="8098c-271">上記のコードは、依存関係の挿入の例です。</span><span class="sxs-lookup"><span data-stu-id="8098c-271">The above code is an example of dependency injection.</span></span> <span data-ttu-id="8098c-272">コント ローラー (作業単位) の依存関係の作成しますが、コント ローラーに、依存関係を挿入することはできません。</span><span class="sxs-lookup"><span data-stu-id="8098c-272">We don’t allow the controller to create it’s dependency (the unit of work) but inject the dependency into the controller.</span></span> <span data-ttu-id="8098c-273">MVC プロジェクトで、制御の反転 (IoC) コンテナーを組み合わせてカスタム コント ローラー ファクトリを使用して、依存関係の挿入を自動化する一般的です。</span><span class="sxs-lookup"><span data-stu-id="8098c-273">In an MVC project it is common to use a custom controller factory in combination with an inversion of control (IoC) container to automate dependency injection.</span></span> <span data-ttu-id="8098c-274">これらのトピックは、この記事の範囲を超えていますが、次のこの記事の最後に参照よりで読み取ることができます。</span><span class="sxs-lookup"><span data-stu-id="8098c-274">These topics are beyond the scope of this article, but you can read more by following the references at the end of this article.</span></span>

<span data-ttu-id="8098c-275">テストに使用できる作業の実装の偽の単位は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="8098c-275">A fake unit of work implementation that we can use for testing might look like the following.</span></span>

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

<span data-ttu-id="8098c-276">コミットのプロパティを公開する偽 unit of work に注意してください。</span><span class="sxs-lookup"><span data-stu-id="8098c-276">Notice the fake unit of work exposes a Commited property.</span></span> <span data-ttu-id="8098c-277">機能テストを簡素化するクラスをフェイクに追加する便利な場合があります。</span><span class="sxs-lookup"><span data-stu-id="8098c-277">It’s sometimes useful to add features to a fake class that facilitate testing.</span></span> <span data-ttu-id="8098c-278">ここでは、容易にコードがコミットされたプロパティをチェックしての作業単位をコミットするかどうかを確認できます。</span><span class="sxs-lookup"><span data-stu-id="8098c-278">In this case it is easy to observe if code commits a unit of work by checking the Commited property.</span></span>

<span data-ttu-id="8098c-279">フェイク IObjectSet 必要がありますも&lt;T&gt;従業員やタイムカードのオブジェクトをメモリに保持します。</span><span class="sxs-lookup"><span data-stu-id="8098c-279">We’ll also need a fake IObjectSet&lt;T&gt; to hold Employee and TimeCard objects in memory.</span></span> <span data-ttu-id="8098c-280">ジェネリックを使用して単一の実装を提供できます。</span><span class="sxs-lookup"><span data-stu-id="8098c-280">We can provide a single implementation using generics.</span></span>

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

<span data-ttu-id="8098c-281">このテスト代替はほとんどの基になる、HashSet に作業を委任&lt;T&gt;オブジェクト。</span><span class="sxs-lookup"><span data-stu-id="8098c-281">This test double delegates most of its work to an underlying HashSet&lt;T&gt; object.</span></span> <span data-ttu-id="8098c-282">注その IObjectSet&lt;T&gt;クラス (参照型) として T を適用するジェネリック制約を必要とし、強制的に IQueryable を実装することにも&lt;T&gt;します。</span><span class="sxs-lookup"><span data-stu-id="8098c-282">Note that IObjectSet&lt;T&gt; requires a generic constraint enforcing T as a class (a reference type), and also forces us to implement IQueryable&lt;T&gt;.</span></span> <span data-ttu-id="8098c-283">IQueryable として表示される、メモリ内コレクションを作成する簡単&lt;T&gt; AsQueryable、標準的な LINQ 演算子を使用します。</span><span class="sxs-lookup"><span data-stu-id="8098c-283">It is easy to make an in-memory collection appear as an IQueryable&lt;T&gt; using the standard LINQ operator AsQueryable.</span></span>

### <a name="the-tests"></a><span data-ttu-id="8098c-284">テスト</span><span class="sxs-lookup"><span data-stu-id="8098c-284">The Tests</span></span>

<span data-ttu-id="8098c-285">従来の単体テストでは、1 つのテスト クラスを 1 つの MVC コント ローラーのすべての操作のすべてのテストを保持するために使用します。</span><span class="sxs-lookup"><span data-stu-id="8098c-285">Traditional unit tests will use a single test class to hold all of the tests for all of the actions in a single MVC controller.</span></span> <span data-ttu-id="8098c-286">記述できます、これらのテストまたは単体テストでは、任意の型のメモリを使用して fakes を作成しました。</span><span class="sxs-lookup"><span data-stu-id="8098c-286">We can write these tests, or any type of unit test, using the in memory fakes we’ve built.</span></span> <span data-ttu-id="8098c-287">ただし、回避はこの記事で、モノリシック テスト クラス アプローチし、特定の機能に集中するテストをグループ代わりにします。</span><span class="sxs-lookup"><span data-stu-id="8098c-287">However, for this article we will avoid the monolithic test class approach and instead group our tests to focus on a specific piece of functionality.</span></span>  <span data-ttu-id="8098c-288">たとえば、「新しい従業員の作成」ようにをテストする 1 つのテスト クラスを使用して、新しい従業員の作成を担当する 1 つのコント ローラー アクションを確認する機能があります。</span><span class="sxs-lookup"><span data-stu-id="8098c-288">For example, “create new employee” might be the functionality we want to test, so we will use a single test class to verify the single controller action responsible for creating a new employee.</span></span>

<span data-ttu-id="8098c-289">きめ細かな設定のテスト クラスをすべての必要ないくつかの一般的なセットアップ コードがあります。</span><span class="sxs-lookup"><span data-stu-id="8098c-289">There is some common setup code we need for all these fine grained test classes.</span></span> <span data-ttu-id="8098c-290">たとえば、常に必要があります、メモリ内リポジトリおよびフェイク作業単位を作成します。</span><span class="sxs-lookup"><span data-stu-id="8098c-290">For example, we always need to create our in-memory repositories and fake unit of work.</span></span> <span data-ttu-id="8098c-291">フェイク unit of work の挿入と従業員のコント ローラーのインスタンスも必要があります。</span><span class="sxs-lookup"><span data-stu-id="8098c-291">We also need an instance of the employee controller with the fake unit of work injected.</span></span> <span data-ttu-id="8098c-292">基本クラスを使用して、テスト クラスにまたがるこの共通のセットアップ コードをご説明します。</span><span class="sxs-lookup"><span data-stu-id="8098c-292">We’ll share this common setup code across test classes by using a base class.</span></span>

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

<span data-ttu-id="8098c-293">基底クラスで使用して「オブジェクト母」は、テスト データを作成するための 1 つの一般的なパターンです。</span><span class="sxs-lookup"><span data-stu-id="8098c-293">The “object mother” we use in the base class is one common pattern for creating test data.</span></span> <span data-ttu-id="8098c-294">オブジェクトの母なるには、複数のテスト フィクスチャの間で使用するためのテスト エンティティのインスタンスを作成するファクトリ メソッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="8098c-294">An object mother contains factory methods to instantiate test entities for use across multiple test fixtures.</span></span>

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

<span data-ttu-id="8098c-295">さまざまなテスト フィクスチャが (図 3 参照)、EmployeeControllerTestBase 基底クラスとして使用できます。</span><span class="sxs-lookup"><span data-stu-id="8098c-295">We can use the EmployeeControllerTestBase as the base class for a number of test fixtures (see figure 3).</span></span> <span data-ttu-id="8098c-296">各テスト フィクスチャは特定のコント ローラー アクションをテストします。</span><span class="sxs-lookup"><span data-stu-id="8098c-296">Each test fixture will test a specific controller action.</span></span> <span data-ttu-id="8098c-297">たとえば、(従業員を作成するためのビューを表示) する HTTP GET 要求、中に使用する作成操作のテストに 1 つのテスト フィクスチャ、重点的し、さまざまなフィクスチャ、重点的に HTTP POST 要求で使用されている作成アクション (によって送信された情報を取得する、ユーザーの従業員を作成する)。</span><span class="sxs-lookup"><span data-stu-id="8098c-297">For example, one test fixture will focus on testing the Create action used during an HTTP GET request (to display the view for creating an employee), and a different fixture will focus on the Create action used in an HTTP POST request (to take information submitted by the user to create an employee).</span></span> <span data-ttu-id="8098c-298">各派生クラスは、特定のコンテキストで、その特定のテストのコンテキストの結果を確認するために必要なアサーションを提供するために必要なセットアップを担当します。</span><span class="sxs-lookup"><span data-stu-id="8098c-298">Each derived class is only responsible for the setup needed in its specific context, and to provide the assertions needed to verify the outcomes for its specific test context.</span></span>

![ef test_03](~/ef6/media/eftest-03.png)

<span data-ttu-id="8098c-300">**図 3**</span><span class="sxs-lookup"><span data-stu-id="8098c-300">**Figure 3**</span></span>

<span data-ttu-id="8098c-301">ここでは名前付け規則とテストのスタイルがテストしやすいコードの必要はありません – アプローチの 1 つだけです。</span><span class="sxs-lookup"><span data-stu-id="8098c-301">The naming convention and test style presented here isn’t required for testable code – it’s just one approach.</span></span> <span data-ttu-id="8098c-302">図 4 は、Jet の頭脳の Resharper で実行されているテストでは、Visual Studio 2010 のランナーのプラグインをテストします。</span><span class="sxs-lookup"><span data-stu-id="8098c-302">Figure 4 shows the tests running in the Jet Brains Resharper test runner plugin for Visual Studio 2010.</span></span>

![ef test_04](~/ef6/media/eftest-04.png)

<span data-ttu-id="8098c-304">**図 4**</span><span class="sxs-lookup"><span data-stu-id="8098c-304">**Figure 4**</span></span>

<span data-ttu-id="8098c-305">共有のセットアップ コードを処理する基本クラスを各コント ローラー アクションの単体テストは、小さく、簡単に記述できます。</span><span class="sxs-lookup"><span data-stu-id="8098c-305">With a base class to handle the shared setup code, the unit tests for each controller action are small and easy to write.</span></span> <span data-ttu-id="8098c-306">テストでは、すばやく (からのメモリ内の操作を実行しました) 実行され、(ため、テスト対象の単位を分離していること) 関連のないインフラストラクチャや環境の懸念事項により失敗することはできません。</span><span class="sxs-lookup"><span data-stu-id="8098c-306">The tests will execute quickly (since we are performing in-memory operations), and shouldn’t fail because of unrelated infrastructure or environmental concerns (because we’ve isolated the unit under test).</span></span>

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

<span data-ttu-id="8098c-307">これらのテストのセットアップ作業のほとんどは、基本クラス。</span><span class="sxs-lookup"><span data-stu-id="8098c-307">In these tests, the base class does most of the setup work.</span></span> <span data-ttu-id="8098c-308">基底クラスのコンストラクターは、メモリ内リポジトリ、作業、フェイク単位および EmployeeController クラスのインスタンスを作成します。 注意してください。</span><span class="sxs-lookup"><span data-stu-id="8098c-308">Remember the base class constructor creates the in-memory repository, a fake unit of work, and an instance of the EmployeeController class.</span></span> <span data-ttu-id="8098c-309">テスト クラスでは、この基本クラスから派生し、テストを作成する方法の詳細に重点を置いています。</span><span class="sxs-lookup"><span data-stu-id="8098c-309">The test class derives from this base class and focuses on the specifics of testing the Create method.</span></span> <span data-ttu-id="8098c-310">この場合、詳細は単体テストの手順で表示されます「整列、動作、およびアサート」の手順に要約します。</span><span class="sxs-lookup"><span data-stu-id="8098c-310">In this case the specifics boil down to the “arrange, act, and assert” steps you’ll see in any unit testing procedure:</span></span>

-   <span data-ttu-id="8098c-311">受信データをシミュレートするために newEmployee オブジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="8098c-311">Create a newEmployee object to simulate incoming data.</span></span>
-   <span data-ttu-id="8098c-312">EmployeeController の作成アクションを呼び出すし、newEmployee を渡します。</span><span class="sxs-lookup"><span data-stu-id="8098c-312">Invoke the Create action of the EmployeeController and pass in the newEmployee.</span></span>
-   <span data-ttu-id="8098c-313">作成アクション (リポジトリで、従業員が表示されます)、期待される結果を生成することを確認します。</span><span class="sxs-lookup"><span data-stu-id="8098c-313">Verify the Create action produces the expected results (the employee appears in the repository).</span></span>

<span data-ttu-id="8098c-314">構築した EmployeeController アクションのいずれかをテストできます。</span><span class="sxs-lookup"><span data-stu-id="8098c-314">What we’ve built allows us to test any of the EmployeeController actions.</span></span> <span data-ttu-id="8098c-315">たとえば、従業員のコント ローラーの Index アクションのテストを記述するとき、テストのために同じ基本設定を確立するために、テストの基本クラスから継承できます。</span><span class="sxs-lookup"><span data-stu-id="8098c-315">For example, when we write tests for the Index action of the Employee controller we can inherit from the test base class to establish the same base setup for our tests.</span></span> <span data-ttu-id="8098c-316">もう一度、基底クラスでは、メモリ内リポジトリ、偽の unit of work、および EmployeeController のインスタンスは作成します。</span><span class="sxs-lookup"><span data-stu-id="8098c-316">Again the base class will create the in-memory repository, the fake unit of work, and an instance of the EmployeeController.</span></span> <span data-ttu-id="8098c-317">インデックス アクションの呼び出しに注目するだけで、インデックス アクションのテストにし、テスト アクション、モデルの品質を返します。</span><span class="sxs-lookup"><span data-stu-id="8098c-317">The tests for the Index action only need to focus on invoking the Index action and testing the qualities of the model the action returns.</span></span>

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

<span data-ttu-id="8098c-318">ここではメモリ内の fakes を作成、テストがテスト指向です、*状態*ソフトウェア。</span><span class="sxs-lookup"><span data-stu-id="8098c-318">The tests we are creating with in-memory fakes are oriented towards testing the *state* of the software.</span></span> <span data-ttu-id="8098c-319">たとえば、– 作成アクションの実行後に、リポジトリの状態を検査する作成アクションをテストするときに、リポジトリ保持して、新しい従業員でしょうか。</span><span class="sxs-lookup"><span data-stu-id="8098c-319">For example, when testing the Create action we want to inspect the state of the repository after the create action executes – does the repository hold the new employee?</span></span>

``` csharp
    [TestMethod]
    public void ShouldAddNewEmployeeToRepository() {
        _controller.Create(_newEmployee);
        Assert.IsTrue(_repository.Contains(_newEmployee));
    }
```

<span data-ttu-id="8098c-320">後でベースの相互作用のテストに説明します。</span><span class="sxs-lookup"><span data-stu-id="8098c-320">Later we’ll look at interaction based testing.</span></span> <span data-ttu-id="8098c-321">テスト対象のコードが、オブジェクトに対する適切なメソッドを呼び出すし、適切なパラメーターを渡されたかどうか、ベースの相互作用のテストが求められます。</span><span class="sxs-lookup"><span data-stu-id="8098c-321">Interaction based testing will ask if the code under test invoked the proper methods on our objects and passed the correct parameters.</span></span> <span data-ttu-id="8098c-322">ここで進みますカバーのもう 1 つの設計パターン: 遅延読み込み。</span><span class="sxs-lookup"><span data-stu-id="8098c-322">For now we’ll move on the cover another design pattern – the lazy load.</span></span>

## <a name="eager-loading-and-lazy-loading"></a><span data-ttu-id="8098c-323">一括読み込みと遅延読み込み</span><span class="sxs-lookup"><span data-stu-id="8098c-323">Eager Loading and Lazy Loading</span></span>

<span data-ttu-id="8098c-324">ある時点で ASP.NET MVC web アプリケーションは従業員の情報を表示し、従業員を含めることがあるには、タイム カードが関連付けられています。</span><span class="sxs-lookup"><span data-stu-id="8098c-324">At some point in the ASP.NET  MVC web application we might wish to display an employee’s information and include the employee’s associated time cards.</span></span> <span data-ttu-id="8098c-325">たとえば、システムで、従業員の名前およびタイム カードの合計数を示すタイム カードの概要の表示があります。</span><span class="sxs-lookup"><span data-stu-id="8098c-325">For example, we might have a time card summary display that shows the employee’s name and the total number of time cards in the system.</span></span> <span data-ttu-id="8098c-326">この機能を実装するためのいくつかの方法があります。</span><span class="sxs-lookup"><span data-stu-id="8098c-326">There are several approaches we can take to implement this feature.</span></span>

### <a name="projection"></a><span data-ttu-id="8098c-327">射影</span><span class="sxs-lookup"><span data-stu-id="8098c-327">Projection</span></span>

<span data-ttu-id="8098c-328">概要を作成する 1 つの簡単なアプローチでは、専用のビューに表示する情報をモデルを作成します。</span><span class="sxs-lookup"><span data-stu-id="8098c-328">One easy approach to create the summary is to construct a model dedicated to the information we want to display in the view.</span></span> <span data-ttu-id="8098c-329">このシナリオでは、モデルは、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="8098c-329">In this scenario the model might look like the following.</span></span>

``` csharp
    public class EmployeeSummaryViewModel {
        public string Name { get; set; }
        public int TotalTimeCards { get; set; }
    }
```

<span data-ttu-id="8098c-330">EmployeeSummaryViewModel はエンティティではありません –、つまりは何かのデータベースに保持することに注意してください。</span><span class="sxs-lookup"><span data-stu-id="8098c-330">Note that the EmployeeSummaryViewModel is not an entity – in other words it is not something we want to persist in the database.</span></span> <span data-ttu-id="8098c-331">厳密に型指定された方法で、ビューにデータをシャッフルするこのクラスを使用するつもりはのみです。</span><span class="sxs-lookup"><span data-stu-id="8098c-331">We are only going to use this class to shuffle data into the view in a strongly typed manner.</span></span> <span data-ttu-id="8098c-332">ビュー モデルではない動作 (メソッドなし) – プロパティのみが含まれているために、データ転送オブジェクト (DTO)。</span><span class="sxs-lookup"><span data-stu-id="8098c-332">The view model is like a data transfer object (DTO) because it contains no behavior (no methods) – only properties.</span></span> <span data-ttu-id="8098c-333">プロパティでは、移動する必要があります。 データを保持します。</span><span class="sxs-lookup"><span data-stu-id="8098c-333">The properties will hold the data we need to move.</span></span> <span data-ttu-id="8098c-334">LINQ の標準的な射影演算子 – Select 演算子を使用して、このビュー モデルをインスタンス化を簡単になります。</span><span class="sxs-lookup"><span data-stu-id="8098c-334">It is easy to instantiate this view model using LINQ’s standard projection operator – the Select operator.</span></span>

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

<span data-ttu-id="8098c-335">上記のコードの 2 つの注目すべき機能があります。</span><span class="sxs-lookup"><span data-stu-id="8098c-335">There are two notable features to the above code.</span></span> <span data-ttu-id="8098c-336">まず – コードは、簡単に観察し、分離がまだ簡単なことをテストできます。</span><span class="sxs-lookup"><span data-stu-id="8098c-336">First – the code is easy to test because it is still easy to observe and isolate.</span></span> <span data-ttu-id="8098c-337">Select 演算子と同様、メモリ内の fakes に対しては同様に機能に対する実際の作業単位です。</span><span class="sxs-lookup"><span data-stu-id="8098c-337">The Select operator works just as well against our in-memory fakes as it does against the real unit of work.</span></span>

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

<span data-ttu-id="8098c-338">2 番目の注目すべき機能は、コードにより、EF4 従業員とタイム カード情報を一緒にアセンブルする 1 つで効率的なクエリを生成する方法です。</span><span class="sxs-lookup"><span data-stu-id="8098c-338">The second notable feature is how the code allows EF4 to generate a single, efficient query to assemble employee and time card information together.</span></span> <span data-ttu-id="8098c-339">任意の特殊な Api を使用せず、同じオブジェクトに従業員情報とタイム カード情報を読み込むしました。</span><span class="sxs-lookup"><span data-stu-id="8098c-339">We’ve loaded employee information and time card information into the same object without using any special APIs.</span></span> <span data-ttu-id="8098c-340">コードでは、メモリ内データ ソースとリモート データ ソースに対して動作する標準的な LINQ 演算子を使用して必要な情報だけで表されます。</span><span class="sxs-lookup"><span data-stu-id="8098c-340">The code merely expressed the information it requires using standard LINQ operators that work against in-memory data sources as well as remote data sources.</span></span> <span data-ttu-id="8098c-341">EF4 が LINQ クエリと C で生成された式ツリーに変換できた\#コンパイラに 1 つで効率的な T-SQL クエリ。</span><span class="sxs-lookup"><span data-stu-id="8098c-341">EF4 was able to translate the expression trees generated by the LINQ query and C\# compiler into a single and efficient T-SQL query.</span></span>

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

<span data-ttu-id="8098c-342">ビュー モデルまたは DTO のオブジェクトではなく、実際のエンティティを操作したくとそれ以外の場合があります。</span><span class="sxs-lookup"><span data-stu-id="8098c-342">There are other times when we don’t want to work with a view model or DTO object, but with real entities.</span></span> <span data-ttu-id="8098c-343">従業員必要がありますがわかっています*と*従業員のタイム カードでは、集中的に、控えめな効率的な方法で、関連するデータを読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="8098c-343">When we know we need an employee *and* the employee’s time cards, we can eagerly load the related data in an unobtrusive and efficient manner.</span></span>

### <a name="explicit-eager-loading"></a><span data-ttu-id="8098c-344">明示的な一括読み込み</span><span class="sxs-lookup"><span data-stu-id="8098c-344">Explicit Eager Loading</span></span>

<span data-ttu-id="8098c-345">いくつかのメカニズムが必要なビジネス ロジックの (または、このシナリオでは、コント ローラー アクション ロジックで) 関連するエンティティ情報を集中的にロードするときに、リポジトリの希望を表現します。</span><span class="sxs-lookup"><span data-stu-id="8098c-345">When we want to eagerly load related entity information we need some mechanism for business logic (or in this scenario, controller action logic) to express its desire to the repository.</span></span> <span data-ttu-id="8098c-346">EF4 ObjectQuery&lt;T&gt;クラスは、クエリ中に取得する関連オブジェクトを指定する Include メソッドを定義します。</span><span class="sxs-lookup"><span data-stu-id="8098c-346">The EF4 ObjectQuery&lt;T&gt; class defines an Include method to specify the related objects to retrieve during a query.</span></span> <span data-ttu-id="8098c-347">EF4 ObjectContext は、具体的な ObjectSet を介してエンティティを公開します。 注意してください。&lt;T&gt; ObjectQuery から継承されるクラス&lt;T&gt;します。</span><span class="sxs-lookup"><span data-stu-id="8098c-347">Remember the EF4 ObjectContext exposes entities via the concrete ObjectSet&lt;T&gt; class which inherits from ObjectQuery&lt;T&gt;.</span></span>  <span data-ttu-id="8098c-348">ObjectSet を使用していた場合&lt;T&gt;次のコードを記述できます、コント ローラー アクション内の参照の各従業員のタイム カード情報を一括で読み込みを指定します。</span><span class="sxs-lookup"><span data-stu-id="8098c-348">If we were using ObjectSet&lt;T&gt; references in our controller action we could write the following code to specify an eager load of time card information for each employee.</span></span>

``` csharp
    _employees.Include("TimeCards")
              .Where(e => e.HireDate.Year > 2009);
```

<span data-ttu-id="8098c-349">ただし、テスト可能なコードを保持しようとしていますのでされることはありません ObjectSet&lt;T&gt;から外部作業クラスの実際の単位。</span><span class="sxs-lookup"><span data-stu-id="8098c-349">However, since we are trying to keep our code testable we are not exposing ObjectSet&lt;T&gt; from outside the real unit of work class.</span></span> <span data-ttu-id="8098c-350">代わりに、我々 が依存、IObjectSet&lt;T&gt;偽より簡単なインターフェイスが IObjectSet&lt;T&gt; Include メソッドを一切定義しません。</span><span class="sxs-lookup"><span data-stu-id="8098c-350">Instead, we rely on the IObjectSet&lt;T&gt; interface which is easier to fake, but IObjectSet&lt;T&gt; does not define an Include method.</span></span> <span data-ttu-id="8098c-351">LINQ の優れた点は、独自の Include 演算子を作成することです。</span><span class="sxs-lookup"><span data-stu-id="8098c-351">The beauty of LINQ is that we can create our own Include operator.</span></span>

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

<span data-ttu-id="8098c-352">IQueryable 拡張メソッドとしてこの Include 演算子が定義されて&lt;T&gt; IObjectSet ではなく&lt;T&gt;します。</span><span class="sxs-lookup"><span data-stu-id="8098c-352">Notice this Include operator is defined as an extension method for IQueryable&lt;T&gt; instead of IObjectSet&lt;T&gt;.</span></span> <span data-ttu-id="8098c-353">これにより、幅広い範囲の IQueryable を含む、型のメソッドを使用して&lt;T&gt;、IObjectSet&lt;T&gt;、ObjectQuery&lt;T&gt;、および ObjectSet&lt;T&gt;します。</span><span class="sxs-lookup"><span data-stu-id="8098c-353">This gives us the ability to use the method with a wider range of possible types, including IQueryable&lt;T&gt;, IObjectSet&lt;T&gt;, ObjectQuery&lt;T&gt;, and ObjectSet&lt;T&gt;.</span></span> <span data-ttu-id="8098c-354">基になるシーケンスが正規の EF4 ObjectQuery イベント&lt;T&gt;Include 演算子は no-op、弊害はありません。</span><span class="sxs-lookup"><span data-stu-id="8098c-354">In the event the underlying sequence is not a genuine EF4 ObjectQuery&lt;T&gt;, then there is no harm done and the Include operator is a no-op.</span></span> <span data-ttu-id="8098c-355">シーケンス、基になる場合*は*ObjectQuery&lt;T&gt; (または ObjectQuery から派生した&lt;T&gt;)、EF4、要件に追加のデータを参照してください、適切な SQL を作成し、クエリ。</span><span class="sxs-lookup"><span data-stu-id="8098c-355">If the underlying sequence *is* an ObjectQuery&lt;T&gt; (or derived from ObjectQuery&lt;T&gt;), then EF4 will see our requirement for additional data and formulate the proper SQL query.</span></span>

<span data-ttu-id="8098c-356">配置でこの新しい演算子を持つリポジトリからタイム カード情報の一括読み込みを明示的に要求できます。</span><span class="sxs-lookup"><span data-stu-id="8098c-356">With this new operator in place we can explicitly request an eager load of time card information from the repository.</span></span>

``` csharp
    public ViewResult Index() {
        var model = _unitOfWork.Employees
                               .Include("TimeCards")
                               .OrderBy(e => e.HireDate);
        return View(model);
    }
```

<span data-ttu-id="8098c-357">実際の ObjectContext に対して実行すると、コードには、次の 1 つのクエリが生成されます。</span><span class="sxs-lookup"><span data-stu-id="8098c-357">When run against a real ObjectContext, the code produces the following single query.</span></span> <span data-ttu-id="8098c-358">クエリでは、employee オブジェクトを具体化して、完全、タイムカードを実際のプロパティを設定する 1 回のトリップで、データベースから十分な情報を収集します。</span><span class="sxs-lookup"><span data-stu-id="8098c-358">The query gathers enough information from the database in one trip to materialize the employee objects and fully populate their TimeCards property.</span></span>

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

<span data-ttu-id="8098c-359">すばらしいは、アクション メソッド内のコードには引き続き完全にテストします。</span><span class="sxs-lookup"><span data-stu-id="8098c-359">The great news is the code inside the action method remains fully testable.</span></span> <span data-ttu-id="8098c-360">Include 演算子をサポートするために、fakes の追加機能を提供する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="8098c-360">We don’t need to provide any additional features for our fakes to support the Include operator.</span></span> <span data-ttu-id="8098c-361">悪い面は依存しない永続性を維持したいコード内で Include 演算子を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8098c-361">The bad news is we had to use the Include operator inside of the code we wanted to keep persistence ignorant.</span></span> <span data-ttu-id="8098c-362">これは、テスト可能なコードをビルドするときに評価する必要がありますのトレードオフの種類の典型的な例です。</span><span class="sxs-lookup"><span data-stu-id="8098c-362">This is a prime example of the type of tradeoffs you’ll need to evaluate when building testable code.</span></span> <span data-ttu-id="8098c-363">パフォーマンス目標を達成するリポジトリの抽象化の外部の永続化懸念事項のリークをできるようにする必要がある場合もあります。</span><span class="sxs-lookup"><span data-stu-id="8098c-363">There are times when you need to let persistence concerns leak outside the repository abstraction to meet performance goals.</span></span>

<span data-ttu-id="8098c-364">一括読み込みを使用しない場合は、遅延読み込みです。</span><span class="sxs-lookup"><span data-stu-id="8098c-364">The alternative to eager loading is lazy loading.</span></span> <span data-ttu-id="8098c-365">遅延読み込みは*いない*ビジネスのコードに明示的に関連付けられているデータの要件をご案内が必要です。</span><span class="sxs-lookup"><span data-stu-id="8098c-365">Lazy loading means we do *not* need our business code to explicitly announce the requirement for associated data.</span></span> <span data-ttu-id="8098c-366">アプリケーションで、エンティティを使用し、追加のデータに Entity Framework が必要な代わりに、オンデマンドでデータが読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="8098c-366">Instead, we use our entities in the application and if additional data is needed Entity Framework will load the data on demand.</span></span>

### <a name="lazy-loading"></a><span data-ttu-id="8098c-367">遅延読み込み</span><span class="sxs-lookup"><span data-stu-id="8098c-367">Lazy Loading</span></span>

<span data-ttu-id="8098c-368">ビジネス ロジックの一部のデータが必要があります、わからないシナリオを考えてみましょうに簡単です。</span><span class="sxs-lookup"><span data-stu-id="8098c-368">It’s easy to imagine a scenario where we don’t know what data a piece of business logic will need.</span></span> <span data-ttu-id="8098c-369">ロジックには、employee オブジェクトが必要がありますが、タイム カードについては、従業員からそれらのパスのいくつか必要し、しない、さまざまな実行パスに分岐します可能性がありますがわかっています可能性があります。</span><span class="sxs-lookup"><span data-stu-id="8098c-369">We might know the logic needs an employee object, but we may branch into different execution paths where some of those paths require time card information from the employee, and some do not.</span></span> <span data-ttu-id="8098c-370">このようなシナリオでは、暗黙的な遅延読み込みデータは魔法のように必要な場合に表示されるために最適です。</span><span class="sxs-lookup"><span data-stu-id="8098c-370">Scenarios like this are perfect for implicit lazy loading because data magically appears on an as-needed basis.</span></span>

<span data-ttu-id="8098c-371">遅延読み込み、遅延とも呼ばれますが、エンティティ オブジェクトのいくつかの要件を配置している読み込み中はします。</span><span class="sxs-lookup"><span data-stu-id="8098c-371">Lazy loading, also known as deferred loading, does place some requirements on our entity objects.</span></span> <span data-ttu-id="8098c-372">True の永続化非依存で Poco の要件、永続化レイヤーから直面しないが true の永続性の無視が実現するために実質的に不可能ではありません。</span><span class="sxs-lookup"><span data-stu-id="8098c-372">POCOs with true persistence ignorance would not face any requirements from the persistence layer, but true persistence ignorance is practically impossible to achieve.</span></span>  <span data-ttu-id="8098c-373">代わりに相対度数で永続化非依存を測定します。</span><span class="sxs-lookup"><span data-stu-id="8098c-373">Instead we measure persistence ignorance in relative degrees.</span></span> <span data-ttu-id="8098c-374">できなくなる幸運指向の永続化の基本クラスを継承または Poco の遅延読み込みを実現するために特殊なコレクションを使用する必要がある場合。</span><span class="sxs-lookup"><span data-stu-id="8098c-374">It would be unfortunate if we needed to inherit from a persistence oriented base class or use a specialized collection to achieve lazy loading in POCOs.</span></span> <span data-ttu-id="8098c-375">さいわい、EF4 では、さほどソリューションがあります。</span><span class="sxs-lookup"><span data-stu-id="8098c-375">Fortunately, EF4 has a less intrusive solution.</span></span>

### <a name="virtually-undetectable"></a><span data-ttu-id="8098c-376">ほとんどの検出</span><span class="sxs-lookup"><span data-stu-id="8098c-376">Virtually Undetectable</span></span>

<span data-ttu-id="8098c-377">POCO オブジェクトを使用する場合、EF4 はエンティティのランタイム プロキシを動的に生成できます。</span><span class="sxs-lookup"><span data-stu-id="8098c-377">When using POCO objects, EF4 can dynamically generate runtime proxies for entities.</span></span> <span data-ttu-id="8098c-378">これらのプロキシが目に見えない形具体化された Poco をラップし各プロパティをインターセプトすることでその他のサービスの取得を提供および追加の作業を実行する操作を設定します。</span><span class="sxs-lookup"><span data-stu-id="8098c-378">These proxies invisibly wrap the materialized POCOs and provide additional services by intercepting each property get and set operation to perform additional work.</span></span> <span data-ttu-id="8098c-379">このような 1 つのサービスは、探している遅延読み込み機能です。</span><span class="sxs-lookup"><span data-stu-id="8098c-379">One such service is the lazy loading feature we are looking for.</span></span> <span data-ttu-id="8098c-380">別のサービスは、効率的な変更の追跡、プログラムには、エンティティのプロパティ値が変更されたときを記録するメカニズムです。</span><span class="sxs-lookup"><span data-stu-id="8098c-380">Another service is an efficient change tracking mechanism which can record when the program changes the property values of an entity.</span></span> <span data-ttu-id="8098c-381">変更の一覧は、更新コマンドを使用して、変更されたエンティティを保持する、SaveChanges メソッドの中に、ObjectContext によって使用されます。</span><span class="sxs-lookup"><span data-stu-id="8098c-381">The list of changes is used by the ObjectContext during the SaveChanges method to persist any modified entities using UPDATE commands.</span></span>

<span data-ttu-id="8098c-382">ただし、するのには、これらのプロキシのプロパティの取得にフックする方法を必要なプロキシと、エンティティに対するセット操作では、この目標を達成仮想メンバーをオーバーライドします。</span><span class="sxs-lookup"><span data-stu-id="8098c-382">For these proxies to work, however, they need a way to hook into property get and set operations on an entity, and the proxies achieve this goal by overriding virtual members.</span></span> <span data-ttu-id="8098c-383">そのため、暗黙の遅延読み込みと効率的な変更追跡が必要な場合、POCO クラス定義に戻り、仮想プロパティを設定する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8098c-383">Thus, if we want to have implicit lazy loading and efficient change tracking we need to go back to our POCO class definitions and mark properties as virtual.</span></span>

``` csharp
    public class Employee {
        public virtual int Id { get; set; }
        public virtual string Name { get; set; }
        public virtual DateTime HireDate { get; set; }
        public virtual ICollection<TimeCard> TimeCards { get; set; }
    }
```

<span data-ttu-id="8098c-384">引き続き Employee エンティティが永続化をほとんど依存と言えます。</span><span class="sxs-lookup"><span data-stu-id="8098c-384">We can still say the Employee entity is mostly persistence ignorant.</span></span> <span data-ttu-id="8098c-385">唯一の要件は、仮想メンバーを使用して、コードのテストの容易性に影響はありません。</span><span class="sxs-lookup"><span data-stu-id="8098c-385">The only requirement is to use virtual members and this does not impact the testability of the code.</span></span> <span data-ttu-id="8098c-386">任意の特殊な基本クラスから派生または遅延読み込みに専用の特別なコレクションを使用しても不要です。</span><span class="sxs-lookup"><span data-stu-id="8098c-386">We don’t need to derive from any special base class, or even use a special collection dedicated to lazy loading.</span></span> <span data-ttu-id="8098c-387">コードは、例、ICollection を実装するクラスと&lt;T&gt;は関連エンティティを保持するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="8098c-387">As the code demonstrates, any class implementing ICollection&lt;T&gt; is available to hold related entities.</span></span>

<span data-ttu-id="8098c-388">1 つの小さな変更が、作業単位内にする必要がありますもあります。</span><span class="sxs-lookup"><span data-stu-id="8098c-388">There is also one minor change we need to make inside our unit of work.</span></span> <span data-ttu-id="8098c-389">遅延読み込みは*オフ*既定を ObjectContext オブジェクトを直接使用する場合。</span><span class="sxs-lookup"><span data-stu-id="8098c-389">Lazy loading is *off* by default when working directly with an ObjectContext object.</span></span> <span data-ttu-id="8098c-390">設定できる ContextOptions プロパティで有効にする遅延読み込み、プロパティがありますし、すべての場所で遅延読み込みを有効にする場合、実際の作業単位内でこのプロパティを設定することができます。</span><span class="sxs-lookup"><span data-stu-id="8098c-390">There is a property we can set on the ContextOptions property to enable deferred loading, and we can set this property inside our real unit of work if we want to enable lazy loading everywhere.</span></span>

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

<span data-ttu-id="8098c-391">暗黙的な遅延読み込みが有効になっているには、従業員のでは、さいわいにも、EF に余分なデータの読み込みに必要な作業を認識しないままタイム カードが関連付けられているし、アプリケーション コードは、従業員を使用できます。</span><span class="sxs-lookup"><span data-stu-id="8098c-391">With implicit lazy loading enabled, application code can use an employee and the employee’s associated time cards while remaining blissfully unaware of the work required for EF to load the extra data.</span></span>

``` csharp
    var employee = _unitOfWork.Employees
                              .Single(e => e.Id == id);
    foreach (var card in employee.TimeCards) {
        // ...
    }
```

<span data-ttu-id="8098c-392">遅延読み込みは、アプリケーション コードを簡単に記述、およびプロキシ マジック コードは引き続き完全にテストします。</span><span class="sxs-lookup"><span data-stu-id="8098c-392">Lazy loading makes the application code easier to write, and with the proxy magic the code remains completely testable.</span></span> <span data-ttu-id="8098c-393">Unit of work のメモリ内の fakes に偽のエンティティに関連付けられているデータ、テスト中に必要なときにプリロードするだけです。</span><span class="sxs-lookup"><span data-stu-id="8098c-393">In-memory fakes of the unit of work can simply preload fake entities with associated data when needed during a test.</span></span>

<span data-ttu-id="8098c-394">この時点で払う IObjectSet を使用してリポジトリの構築から有効にします&lt;T&gt;し、永続化フレームワークのすべての記号を非表示に抽象化を確認します。</span><span class="sxs-lookup"><span data-stu-id="8098c-394">At this point we’ll turn our attention from building repositories using IObjectSet&lt;T&gt; and look at abstractions to hide all signs of the persistence framework.</span></span>

## <a name="custom-repositories"></a><span data-ttu-id="8098c-395">カスタム リポジトリ</span><span class="sxs-lookup"><span data-stu-id="8098c-395">Custom Repositories</span></span>

<span data-ttu-id="8098c-396">この記事ではまずの unit of work 設計パターンを表示とすると、unit of work のようにいくつかのサンプル コードが用意されていますが。</span><span class="sxs-lookup"><span data-stu-id="8098c-396">When we first presented the unit of work design pattern in this article we provided some sample code for what the unit of work might look like.</span></span> <span data-ttu-id="8098c-397">従業員とで操作している従業員タイム カードのシナリオを使用してこの元の意図を再表示してみましょう。</span><span class="sxs-lookup"><span data-stu-id="8098c-397">Let’s re-present this original idea using the employee and employee time card scenario we’ve been working with.</span></span>

``` csharp
    public interface IUnitOfWork {
        IRepository<Employee> Employees { get; }
        IRepository<TimeCard> TimeCards { get;  }
        void Commit();
    }
```

<span data-ttu-id="8098c-398">この作業単位と最後のセクションで作成した作業の単位の主な違いはどのように作業単位が、EF4 framework から抽象化を使用しません (IObjectSet がない&lt;T&gt;)。</span><span class="sxs-lookup"><span data-stu-id="8098c-398">The primary difference between this unit of work and the unit of work we created in the last section is how this unit of work does not use any abstractions from the EF4 framework (there is no IObjectSet&lt;T&gt;).</span></span> <span data-ttu-id="8098c-399">IObjectSet&lt;T&gt;リポジトリ インターフェイスが公開 API としても動作は、アプリケーションのニーズを持つ完全に一致可能性があります。</span><span class="sxs-lookup"><span data-stu-id="8098c-399">IObjectSet&lt;T&gt; works well as a repository interface, but the API it exposes might not perfectly align with our application’s needs.</span></span> <span data-ttu-id="8098c-400">今後この方法でカスタム IRepository を使用してリポジトリを表す&lt;T&gt;抽象化します。</span><span class="sxs-lookup"><span data-stu-id="8098c-400">In this upcoming approach we will represent repositories using a custom IRepository&lt;T&gt; abstraction.</span></span>

<span data-ttu-id="8098c-401">テスト駆動型設計、動作駆動型設計、およびドメイン主導型の方法論のデザインをフォローしている多くの開発者が、IRepository を好む&lt;T&gt;アプローチをいくつかの理由。</span><span class="sxs-lookup"><span data-stu-id="8098c-401">Many developers who follow test-driven design, behavior-driven design, and domain driven methodologies design prefer the IRepository&lt;T&gt; approach for several reasons.</span></span> <span data-ttu-id="8098c-402">最初は、IRepository、&lt;T&gt;インターフェイスは、「腐敗」レイヤーを表します。</span><span class="sxs-lookup"><span data-stu-id="8098c-402">First, the IRepository&lt;T&gt; interface represents an “anti-corruption” layer.</span></span> <span data-ttu-id="8098c-403">ドメイン駆動設計の著書で Eric Evans が説明されていると破損対策レイヤーを永続化 API などのインフラストラクチャは、Api から、ドメイン コードを保持します。</span><span class="sxs-lookup"><span data-stu-id="8098c-403">As described by Eric Evans in his Domain Driven Design book an anti-corruption layer keeps your domain code away from infrastructure APIs, like a persistence API.</span></span> <span data-ttu-id="8098c-404">次に、開発者は、(テストの書き込み中に検出) と、アプリケーションの正確なニーズを満たすリポジトリにメソッドを構築できます。</span><span class="sxs-lookup"><span data-stu-id="8098c-404">Secondly, developers can build methods into the repository that meet the exact needs of an application (as discovered while writing tests).</span></span> <span data-ttu-id="8098c-405">たとえば、リポジトリ インターフェイスを FindById メソッドを追加できるように、ID 値を使用して 1 つのエンティティを検索します必要があります頻繁に。</span><span class="sxs-lookup"><span data-stu-id="8098c-405">For example, we might frequently need to locate a single entity using an ID value, so we can add a FindById method to the repository interface.</span></span>  <span data-ttu-id="8098c-406">この IRepository&lt;T&gt;定義は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="8098c-406">Our IRepository&lt;T&gt; definition will look like the following.</span></span>

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

<span data-ttu-id="8098c-407">IQueryable を使用するバックアップをドロップに注意してください。&lt;T&gt;エンティティ コレクションを公開するインターフェイス。</span><span class="sxs-lookup"><span data-stu-id="8098c-407">Notice we’ll drop back to using an IQueryable&lt;T&gt; interface to expose entity collections.</span></span> <span data-ttu-id="8098c-408">IQueryable&lt;T&gt; EF4 プロバイダーに送信し、プロバイダーのクエリの全体像を提供する LINQ 式ツリーを使用します。</span><span class="sxs-lookup"><span data-stu-id="8098c-408">IQueryable&lt;T&gt; allows LINQ expression trees to flow into the EF4 provider and give the provider a holistic view of the query.</span></span> <span data-ttu-id="8098c-409">IEnumerable を返すを 2 つ目の方法もあります&lt;T&gt;、つまり、EF4 LINQ プロバイダーでは、リポジトリ内で作成された式のみ表示されます。</span><span class="sxs-lookup"><span data-stu-id="8098c-409">A second option would be to return IEnumerable&lt;T&gt;, which means the EF4 LINQ provider will only see the expressions built inside of the repository.</span></span> <span data-ttu-id="8098c-410">グループ化、並べ替え、およびリポジトリの外部で実行するプロジェクションは、パフォーマンスが低下することができるデータベースに送信する SQL コマンドに構成されません。</span><span class="sxs-lookup"><span data-stu-id="8098c-410">Any grouping, ordering, and projection done outside of the repository will not be composed into the SQL command sent to the database, which can hurt performance.</span></span> <span data-ttu-id="8098c-411">その一方で、IEnumerable のみを返すリポジトリ&lt;T&gt;結果は決して驚くことで新しい SQL コマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="8098c-411">On the other hand, a repository returning only IEnumerable&lt;T&gt; results will never surprise you with a new SQL command.</span></span> <span data-ttu-id="8098c-412">両方のアプローチが動作し、両方の方法がテスト可能なままです。</span><span class="sxs-lookup"><span data-stu-id="8098c-412">Both approaches will work, and both approaches remain testable.</span></span>

<span data-ttu-id="8098c-413">IRepository の 1 つの実装を提供する簡単な&lt;T&gt;インターフェイスのジェネリックと EF4 ObjectContext API を使用します。</span><span class="sxs-lookup"><span data-stu-id="8098c-413">It’s straightforward to provide a single implementation of the IRepository&lt;T&gt; interface using generics and the EF4 ObjectContext API.</span></span>

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

<span data-ttu-id="8098c-414">IRepository&lt;T&gt;アプローチにより、クエリをいくつか追加の制御、クライアントがエンティティを取得するメソッドを呼び出す必要があるためです。</span><span class="sxs-lookup"><span data-stu-id="8098c-414">The IRepository&lt;T&gt; approach gives us some additional control over our queries because a client has to invoke a method to get to an entity.</span></span> <span data-ttu-id="8098c-415">メソッド内で追加のチェックとアプリケーションの制約を適用する LINQ 演算子が実現します。</span><span class="sxs-lookup"><span data-stu-id="8098c-415">Inside the method we could provide additional checks and LINQ operators to enforce application constraints.</span></span> <span data-ttu-id="8098c-416">インターフェイスが 2 つの制約のジェネリック型パラメーターに注意してください。</span><span class="sxs-lookup"><span data-stu-id="8098c-416">Notice the interface has two constraints on the generic type parameter.</span></span> <span data-ttu-id="8098c-417">最初の制約は、ObjectSet によって必要なクラスの短所汚染&lt;T&gt;、し、2 つ目の制約が IEntity – アプリケーションの作成の抽象型を実装するために、エンティティを強制します。</span><span class="sxs-lookup"><span data-stu-id="8098c-417">The first constraint is the class cons taint required by ObjectSet&lt;T&gt;, and the second constraint forces our entities to implement IEntity – an abstraction created for the application.</span></span> <span data-ttu-id="8098c-418">IEntity インターフェイス Id の読み取り可能なプロパティがエンティティを強制して、FindById メソッドでこのプロパティを使用することができます。</span><span class="sxs-lookup"><span data-stu-id="8098c-418">The IEntity interface forces entities to have a readable Id property, and we can then use this property in the FindById method.</span></span> <span data-ttu-id="8098c-419">次のコードでは、IEntity が定義されます。</span><span class="sxs-lookup"><span data-stu-id="8098c-419">IEntity is defined with the following code.</span></span>

``` csharp
    public interface IEntity {
        int Id { get; }
    }
```

<span data-ttu-id="8098c-420">エンティティがこのインターフェイスを実装するために必要なためににより、IEntity に永続化非依存の小規模な違反を考慮でした。</span><span class="sxs-lookup"><span data-stu-id="8098c-420">IEntity could be considered a small violation of persistence ignorance since our entities are required to implement this interface.</span></span> <span data-ttu-id="8098c-421">永続化非依存では、トレードオフについて説明し、FindById 機能が、インターフェイスによって課される制約を上回る多くに注意してください。</span><span class="sxs-lookup"><span data-stu-id="8098c-421">Remember persistence ignorance is about tradeoffs, and for many the FindById functionality will outweigh the constraint imposed by the interface.</span></span> <span data-ttu-id="8098c-422">インターフェイスには、テストの容易性に影響はありません。</span><span class="sxs-lookup"><span data-stu-id="8098c-422">The interface has no impact on testability.</span></span>

<span data-ttu-id="8098c-423">ライブ IRepository をインスタンス化する&lt;T&gt; EF4 ObjectContext を必要なため、具体的な実装の作業単位がインスタンス化を管理する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8098c-423">Instantiating a live IRepository&lt;T&gt; requires an EF4 ObjectContext, so a concrete unit of work implementation should manage the instantiation.</span></span>

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

### <a name="using-the-custom-repository"></a><span data-ttu-id="8098c-424">カスタム リポジトリを使用します。</span><span class="sxs-lookup"><span data-stu-id="8098c-424">Using the Custom Repository</span></span>

<span data-ttu-id="8098c-425">IObjectSet に基づいてリポジトリを使用して、大幅に異なりますが、カスタム リポジトリを使用して&lt;T&gt;します。</span><span class="sxs-lookup"><span data-stu-id="8098c-425">Using our custom repository is not significantly different from using the repository based on IObjectSet&lt;T&gt;.</span></span> <span data-ttu-id="8098c-426">LINQ 演算子を適用するプロパティに直接、代わりに最初にする必要がありますいずれかに IQueryable を取得するリポジトリのメソッドを呼び出す&lt;T&gt;参照。</span><span class="sxs-lookup"><span data-stu-id="8098c-426">Instead of applying LINQ operators directly to a property, we’ll first need to invoke one the repository’s methods to grab an IQueryable&lt;T&gt; reference.</span></span>

``` csharp
    public ViewResult Index() {
        var model = _repository.FindAll()
                               .Include("TimeCards")
                               .OrderBy(e => e.HireDate);
        return View(model);
    }
```

<span data-ttu-id="8098c-427">変更せずに動作が以前に実装したカスタムの Include 演算子に注意してください。</span><span class="sxs-lookup"><span data-stu-id="8098c-427">Notice the custom Include operator we implemented previously will work without change.</span></span> <span data-ttu-id="8098c-428">リポジトリの FindById メソッドでは、1 つのエンティティを取得しようとしています。 アクションから複製されたロジックを削除します。</span><span class="sxs-lookup"><span data-stu-id="8098c-428">The repository’s FindById method removes duplicated logic from actions trying to retrieve a single entity.</span></span>

``` csharp
    public ViewResult Details(int id) {
        var model = _repository.FindById(id);
        return View(model);
    }
```

<span data-ttu-id="8098c-429">2 つの方法について説明しましたのテストの容易性に大きな違いはありません。</span><span class="sxs-lookup"><span data-stu-id="8098c-429">There is no significant difference in the testability of the two approaches we’ve examined.</span></span> <span data-ttu-id="8098c-430">IRepository の偽の実装を提供できる&lt;T&gt; HashSet に支え具象クラスを作成することにより&lt;従業員&gt;- 最後のセクションで行ったものと同じようにします。</span><span class="sxs-lookup"><span data-stu-id="8098c-430">We could provide fake implementations of IRepository&lt;T&gt; by building concrete classes backed by HashSet&lt;Employee&gt; - just like what we did in the last section.</span></span> <span data-ttu-id="8098c-431">ただし、モック オブジェクトを使用して、モック オブジェクト フレームワーク fakes を構築する代わりを好む開発者もいます。</span><span class="sxs-lookup"><span data-stu-id="8098c-431">However, some developers prefer to use mock objects and mock object frameworks instead of building fakes.</span></span> <span data-ttu-id="8098c-432">モックを使用して、今回の実装をテストし、次のセクションでは、モックとフェイクの間の違いについて説明することについて説明します。</span><span class="sxs-lookup"><span data-stu-id="8098c-432">We’ll look at using mocks to test our implementation and discuss the differences between mocks and fakes in the next section.</span></span>

### <a name="testing-with-mocks"></a><span data-ttu-id="8098c-433">モックを使用したテスト</span><span class="sxs-lookup"><span data-stu-id="8098c-433">Testing with Mocks</span></span>

<span data-ttu-id="8098c-434">「テスト代替」どのような Martin Fowler の呼び出しを構築するためのさまざまな方法はあります。</span><span class="sxs-lookup"><span data-stu-id="8098c-434">There are different approaches to building what Martin Fowler calls a “test double”.</span></span> <span data-ttu-id="8098c-435">(二重ムービー スタント) のような二重のテストは、テスト中に運用環境のオブジェクトに""実際のビルドするオブジェクトです。</span><span class="sxs-lookup"><span data-stu-id="8098c-435">A test double (like a movie stunt double) is an object you build to “stand in” for real, production objects during tests.</span></span> <span data-ttu-id="8098c-436">作成したメモリ内リポジトリは、SQL Server と対話するリポジトリのテスト代替です。</span><span class="sxs-lookup"><span data-stu-id="8098c-436">The in-memory repositories we created are test doubles for the repositories that talk to SQL Server.</span></span> <span data-ttu-id="8098c-437">単体テスト中にこれらのテスト代替を使用してコードを分離し、高速に実行されるテストを保持する方法を説明しました。</span><span class="sxs-lookup"><span data-stu-id="8098c-437">We’ve seen how to use these test-doubles during the unit tests to isolate code and keep tests running fast.</span></span>

<span data-ttu-id="8098c-438">作成したテスト代替では、実際、作業の実装があります。</span><span class="sxs-lookup"><span data-stu-id="8098c-438">The test doubles we’ve built have real, working implementations.</span></span> <span data-ttu-id="8098c-439">オブジェクトの具体的なコレクションを格納するバック グラウンドでそれぞれし、追加し、テスト中に、リポジトリの操作オブジェクトをこのコレクションから削除します。</span><span class="sxs-lookup"><span data-stu-id="8098c-439">Behind the scenes each one stores a concrete collection of objects, and they will add and remove objects from this collection as we manipulate the repository during a test.</span></span> <span data-ttu-id="8098c-440">一部の開発者、テスト代替の実際のコードと作業の実装でこの方法を構築したいです。</span><span class="sxs-lookup"><span data-stu-id="8098c-440">Some developers like to build their test doubles this way – with real code and working implementations.</span></span>  <span data-ttu-id="8098c-441">これらのテスト代替はいわゆる*fakes*します。</span><span class="sxs-lookup"><span data-stu-id="8098c-441">These test doubles are what we call *fakes*.</span></span> <span data-ttu-id="8098c-442">作業の実装があるが、運用環境用に十分な実際はありません。</span><span class="sxs-lookup"><span data-stu-id="8098c-442">They have working implementations, but they aren’t real enough for production use.</span></span> <span data-ttu-id="8098c-443">偽のリポジトリはデータベースに実際に作成できません。</span><span class="sxs-lookup"><span data-stu-id="8098c-443">The fake repository doesn’t actually write to the database.</span></span> <span data-ttu-id="8098c-444">偽の SMTP サーバーでは、ネットワーク経由で電子メール メッセージを送信しない実際にします。</span><span class="sxs-lookup"><span data-stu-id="8098c-444">The fake SMTP server doesn’t actually send an email message over the network.</span></span>

### <a name="mocks-versus-fakes"></a><span data-ttu-id="8098c-445">フェイクとモック</span><span class="sxs-lookup"><span data-stu-id="8098c-445">Mocks versus Fakes</span></span>

<span data-ttu-id="8098c-446">別の種類のテストと呼ばれる倍精度浮動小数点がある、*モック*します。</span><span class="sxs-lookup"><span data-stu-id="8098c-446">There is another type of test double known as a *mock*.</span></span> <span data-ttu-id="8098c-447">Fakes には、作業の実装がありますが、モック実装のが付属しません。</span><span class="sxs-lookup"><span data-stu-id="8098c-447">While fakes have working implementations, mocks come with no implementation.</span></span> <span data-ttu-id="8098c-448">モック オブジェクト フレームワークを利用して実行時にこれらのモック オブジェクトを構築し、テスト代替として使用します。</span><span class="sxs-lookup"><span data-stu-id="8098c-448">With the help of a mock object framework we construct these mock objects at run time and use them as test doubles.</span></span> <span data-ttu-id="8098c-449">このセクションで使用する、オープン ソースのフレームワーク Moq モックを作成します。</span><span class="sxs-lookup"><span data-stu-id="8098c-449">In this section we’ll be using the open source mocking framework Moq.</span></span> <span data-ttu-id="8098c-450">Moq を使用して、従業員のリポジトリの二重テストを動的に作成する簡単な例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="8098c-450">Here is a simple example of using Moq to dynamically create a test double for an employee repository.</span></span>

``` csharp
    Mock<IRepository<Employee>> mock =
        new Mock<IRepository<Employee>>();
    IRepository<Employee> repository = mock.Object;
    repository.Add(new Employee());
    var employee = repository.FindById(1);
```

<span data-ttu-id="8098c-451">Moq 要求、IRepository&lt;従業員&gt;実装および、ビルドのいずれかに動的にします。</span><span class="sxs-lookup"><span data-stu-id="8098c-451">We ask Moq for an IRepository&lt;Employee&gt; implementation and it builds one dynamically.</span></span> <span data-ttu-id="8098c-452">IRepository を実装するオブジェクトを取得できます&lt;従業員&gt;のモック オブジェクトのプロパティにアクセスする&lt;T&gt;オブジェクト。</span><span class="sxs-lookup"><span data-stu-id="8098c-452">We can get to the object implementing IRepository&lt;Employee&gt; by accessing the Object property of the Mock&lt;T&gt; object.</span></span> <span data-ttu-id="8098c-453">これは、コント ローラーに渡すことができますこの内部オブジェクトと、テスト代替または実際のリポジトリの場合は、わかりません。</span><span class="sxs-lookup"><span data-stu-id="8098c-453">It is this inner object we can pass into our controllers, and they won’t know if this is a test double or the real repository.</span></span> <span data-ttu-id="8098c-454">オブジェクトのメソッドは、実際の実装を持つオブジェクトでメソッドを呼び出すことと同様呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="8098c-454">We can invoke methods on the object just like we would invoke methods on an object with a real implementation.</span></span>

<span data-ttu-id="8098c-455">モック リポジトリが Add メソッドを呼び出しているときに疑問する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8098c-455">You must be wondering what the mock repository will do when we invoke the Add method.</span></span> <span data-ttu-id="8098c-456">モック オブジェクトの内部の実装がないため、追加が何も行われません。</span><span class="sxs-lookup"><span data-stu-id="8098c-456">Since there is no implementation behind the mock object, Add does nothing.</span></span> <span data-ttu-id="8098c-457">従業員は破棄されますのでご説明したとおり、fakes にあったように、バック グラウンドでの具体的なコレクションはありません。</span><span class="sxs-lookup"><span data-stu-id="8098c-457">There is no concrete collection behind the scenes like we had with the fakes we wrote, so the employee is discarded.</span></span> <span data-ttu-id="8098c-458">FindById の戻り値について説明します。</span><span class="sxs-lookup"><span data-stu-id="8098c-458">What about the return value of FindById?</span></span> <span data-ttu-id="8098c-459">ここで、モック オブジェクトは、既定値が返されますが、そのですだけが。</span><span class="sxs-lookup"><span data-stu-id="8098c-459">In this case the mock object does the only thing it can do, which is return a default value.</span></span> <span data-ttu-id="8098c-460">参照型 (従業員) を返すことがあるため、戻り値、null 値です。</span><span class="sxs-lookup"><span data-stu-id="8098c-460">Since we are returning a reference type (an Employee), the return value is a null value.</span></span>

<span data-ttu-id="8098c-461">モック思えますが、役に立ちません。ただし、について説明していないのでモックの 2 つの多くの機能があります。</span><span class="sxs-lookup"><span data-stu-id="8098c-461">Mocks might sound worthless; however, there are two more features of mocks we haven’t talked about.</span></span> <span data-ttu-id="8098c-462">まず、Moq フレームワークは、モック オブジェクトで行われたすべての呼び出しを記録します。</span><span class="sxs-lookup"><span data-stu-id="8098c-462">First, the Moq framework records all the calls made on the mock object.</span></span> <span data-ttu-id="8098c-463">後半のコードには、Moq を依頼して、だれでも、Add メソッドが呼び出される場合、または FindById メソッドを呼び出したすべてのユーザーの場合できます。</span><span class="sxs-lookup"><span data-stu-id="8098c-463">Later in the code we can ask Moq if anyone invoked the Add method, or if anyone invoked the FindById method.</span></span> <span data-ttu-id="8098c-464">この「ブラック_ボックス」記録機能を使用して、テストでは後でどのように表示されます。</span><span class="sxs-lookup"><span data-stu-id="8098c-464">We’ll see later how we can use this “black box” recording feature in tests.</span></span>

<span data-ttu-id="8098c-465">2 番目の優れた機能は、Moq を使用して使用して、モック オブジェクトのプログラミング方法*期待*します。</span><span class="sxs-lookup"><span data-stu-id="8098c-465">The second great feature is how we can use Moq to program a mock object with *expectations*.</span></span> <span data-ttu-id="8098c-466">予測は、モック オブジェクトに、特定の操作に応答する方法を指示します。</span><span class="sxs-lookup"><span data-stu-id="8098c-466">An expectation tells the mock object how to respond to any given interaction.</span></span> <span data-ttu-id="8098c-467">たとえば、予測をモックにプログラムをだれかが FindById を呼び出すときに、employee オブジェクトを返すことを確認できます。</span><span class="sxs-lookup"><span data-stu-id="8098c-467">For example, we can program an expectation into our mock and tell it to return an employee object when someone invokes FindById.</span></span> <span data-ttu-id="8098c-468">Moq フレームワークは、このような期待をプログラムするのにセットアップ API とラムダ式を使用します。</span><span class="sxs-lookup"><span data-stu-id="8098c-468">The Moq framework uses a Setup API and lambda expressions to program these expectations.</span></span>

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

<span data-ttu-id="8098c-469">このサンプルでは、リポジトリを動的に構築 Moq させていただき、という前提でリポジトリをプログラムします。</span><span class="sxs-lookup"><span data-stu-id="8098c-469">In this sample we ask Moq to dynamically build a repository, and then we program the repository with an expectation.</span></span> <span data-ttu-id="8098c-470">想定されるは、だれかが 5 の値を渡す FindById メソッドを呼び出すときに、5 の Id 値を持つ新しい employee オブジェクトを返すモック オブジェクトを指示します。</span><span class="sxs-lookup"><span data-stu-id="8098c-470">The expectation tells the mock object to return a new employee object with an Id value of 5 when someone invokes the FindById method passing a value of 5.</span></span> <span data-ttu-id="8098c-471">このテストが成功して、偽の IRepository に完全な実装を構築する必要はなく&lt;T&gt;します。</span><span class="sxs-lookup"><span data-stu-id="8098c-471">This test passes, and we didn’t need to build a full implementation to fake IRepository&lt;T&gt;.</span></span>

<span data-ttu-id="8098c-472">みましょう以前作成したテストを再確認し、fakes ではなく、モックを使用するように再作成します。</span><span class="sxs-lookup"><span data-stu-id="8098c-472">Let’s revisit the tests we wrote earlier and rework them to use mocks instead of fakes.</span></span> <span data-ttu-id="8098c-473">同じように、前に使用します基底クラスのすべてのコント ローラーのテストに必要なインフラストラクチャの一般的な情報を設定します。</span><span class="sxs-lookup"><span data-stu-id="8098c-473">Just like before, we’ll use a base class to setup the common pieces of infrastructure we need for all of the controller’s tests.</span></span>

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

<span data-ttu-id="8098c-474">このセットアップ コードはほとんど同じです。</span><span class="sxs-lookup"><span data-stu-id="8098c-474">The setup code remains mostly the same.</span></span> <span data-ttu-id="8098c-475">Fakes を使用せずに Moq モック オブジェクトの構築に使用します。</span><span class="sxs-lookup"><span data-stu-id="8098c-475">Instead of using fakes, we’ll use Moq to construct mock objects.</span></span> <span data-ttu-id="8098c-476">基本クラスは、コードが従業員のプロパティを呼び出すと、モック リポジトリを返すモック unit of work を配置します。</span><span class="sxs-lookup"><span data-stu-id="8098c-476">The base class arranges for the mock unit of work to return a mock repository when code invokes the Employees property.</span></span> <span data-ttu-id="8098c-477">モック セットアップの残りの部分は、特定のシナリオごとに専用のテスト フィクスチャ内で実行されます。</span><span class="sxs-lookup"><span data-stu-id="8098c-477">The rest of the mock setup will take place inside the test fixtures dedicated to each specific scenario.</span></span> <span data-ttu-id="8098c-478">など、インデックス アクション用のテスト フィクスチャはアクションは、モック リポジトリの FindAll メソッドを呼び出すときに従業員の一覧を返すモック リポジトリをセットアップします。</span><span class="sxs-lookup"><span data-stu-id="8098c-478">For example, the test fixture for the Index action will setup the mock repository to return a list of employees when the action invokes the FindAll method of the mock repository.</span></span>

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

<span data-ttu-id="8098c-479">、期待値を除くテストでは、ように、テストする前にありました。</span><span class="sxs-lookup"><span data-stu-id="8098c-479">Except for the expectations, our tests look similar to the tests we had before.</span></span> <span data-ttu-id="8098c-480">ただし、モック フレームワークの記録機能を異なる角度からテストをアプローチことができます。</span><span class="sxs-lookup"><span data-stu-id="8098c-480">However, with the recording ability of a mock framework we can approach testing from a different angle.</span></span> <span data-ttu-id="8098c-481">次のセクションで新しい視点に立つことについて説明します。</span><span class="sxs-lookup"><span data-stu-id="8098c-481">We’ll look at this new perspective in the next section.</span></span>

### <a name="state-versus-interaction-testing"></a><span data-ttu-id="8098c-482">相互作用のテストと状態</span><span class="sxs-lookup"><span data-stu-id="8098c-482">State versus Interaction Testing</span></span>

<span data-ttu-id="8098c-483">ソフトウェアとモック オブジェクトをテストに使用できるさまざまな方法はあります。</span><span class="sxs-lookup"><span data-stu-id="8098c-483">There are different techniques you can use to test software with mock objects.</span></span> <span data-ttu-id="8098c-484">1 つは、状態を使用するベースのテスト、これは、何がこのホワイト ペーパーでここまでの処理です。</span><span class="sxs-lookup"><span data-stu-id="8098c-484">One approach is to use state based testing, which is what we have done in this paper so far.</span></span> <span data-ttu-id="8098c-485">状態ベースのソフトウェアの状態の詳細についてはアサーションをテストします。</span><span class="sxs-lookup"><span data-stu-id="8098c-485">State based testing makes assertions about the state of the software.</span></span> <span data-ttu-id="8098c-486">最後のテストは、コント ローラーのアクション メソッドを呼び出すし、それを構築する必要があります、モデルに関するアサーションを作成します。</span><span class="sxs-lookup"><span data-stu-id="8098c-486">In the last test we invoked an action method on the controller and made an assertion about the model it should build.</span></span> <span data-ttu-id="8098c-487">テストの状態の他のいくつかの例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="8098c-487">Here are some other examples of testing state:</span></span>

-   <span data-ttu-id="8098c-488">リポジトリが作成の実行後に、新しい従業員オブジェクトを含むことを確認します。</span><span class="sxs-lookup"><span data-stu-id="8098c-488">Verify the repository contains the new employee object after Create executes.</span></span>
-   <span data-ttu-id="8098c-489">モデルは、インデックスの実行後に、すべての従業員の一覧を保持していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="8098c-489">Verify the model holds a list of all employees after Index executes.</span></span>
-   <span data-ttu-id="8098c-490">削除の実行後に、リポジトリは、特定の従業員を含まないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="8098c-490">Verify the repository does not contain a given employee after Delete executes.</span></span>

<span data-ttu-id="8098c-491">モック オブジェクトのもう 1 つの方法は、確認する*の相互作用*します。</span><span class="sxs-lookup"><span data-stu-id="8098c-491">Another approach you’ll see with mock objects is to verify *interactions*.</span></span> <span data-ttu-id="8098c-492">状態ベースのオブジェクトの状態に関するテストとアサーション、相互作用ベースのオブジェクトを操作する方法の詳細についてはアサーションをテストします。</span><span class="sxs-lookup"><span data-stu-id="8098c-492">While state based testing makes assertions about the state of objects, interaction based testing makes assertions about how objects interact.</span></span> <span data-ttu-id="8098c-493">例えば:</span><span class="sxs-lookup"><span data-stu-id="8098c-493">For example:</span></span>

-   <span data-ttu-id="8098c-494">作成を実行するときに、コント ローラーがリポジトリの Add メソッドを呼び出すことを確認します。</span><span class="sxs-lookup"><span data-stu-id="8098c-494">Verify the controller invokes the repository’s Add method when Create executes.</span></span>
-   <span data-ttu-id="8098c-495">インデックスを実行するときに、コント ローラーがリポジトリの FindAll メソッドを呼び出すことを確認します。</span><span class="sxs-lookup"><span data-stu-id="8098c-495">Verify the controller invokes the repository’s FindAll method when Index executes.</span></span>
-   <span data-ttu-id="8098c-496">コント ローラーが編集を実行するときに、変更を保存する作業の Commit メソッドの単位を呼び出すことを確認します。</span><span class="sxs-lookup"><span data-stu-id="8098c-496">Verify the controller invokes the unit of work’s Commit method to save changes when Edit executes.</span></span>

<span data-ttu-id="8098c-497">コレクション内に設定するときとカウントを確認することはためテストの低いデータを必要な相互作用をテスト多くの場合。</span><span class="sxs-lookup"><span data-stu-id="8098c-497">Interaction testing often requires less test data, because we aren’t poking inside of collections and verifying counts.</span></span> <span data-ttu-id="8098c-498">など、Details アクションが有効な値の指定のリポジトリの FindById メソッドを呼び出すことがわかっている場合、アクションがおそらく正しく動作します。</span><span class="sxs-lookup"><span data-stu-id="8098c-498">For example, if we know the Details action invokes a repository’s FindById method with the correct value - then the action is probably behaving correctly.</span></span> <span data-ttu-id="8098c-499">FindById から返されるすべてのテスト データをセットアップすることがなくこの動作を確認できます。</span><span class="sxs-lookup"><span data-stu-id="8098c-499">We can verify this behavior without setting up any test data to return from FindById.</span></span>

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

<span data-ttu-id="8098c-500">上記のテスト フィクスチャで必要な唯一のセットアップでは、基本クラスによって提供されるセットアップです。</span><span class="sxs-lookup"><span data-stu-id="8098c-500">The only setup required in the above test fixture is the setup provided by the base class.</span></span> <span data-ttu-id="8098c-501">コント ローラー アクションを呼び出すことと Moq モック リポジトリとの対話が記録されます。</span><span class="sxs-lookup"><span data-stu-id="8098c-501">When we invoke the controller action, Moq will record the interactions with the mock repository.</span></span> <span data-ttu-id="8098c-502">確認 API の Moq を使用して、質問 Moq、コント ローラーが、適切な ID 値を持つ FindById を呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="8098c-502">Using the Verify API of Moq, we can ask Moq if the controller invoked FindById with the proper ID value.</span></span> <span data-ttu-id="8098c-503">コント ローラーが、メソッドを呼び出しませんでした予期しないパラメーターの値を持つメソッドを呼び出した場合、検証メソッドは例外をスローし、テストは失敗します。</span><span class="sxs-lookup"><span data-stu-id="8098c-503">If the controller did not invoke the method, or invoked the method with an unexpected parameter value, the Verify method will throw an exception and the test will fail.</span></span>

<span data-ttu-id="8098c-504">作成アクションは、現在の作業ユニットでコミットを呼び出すことを確認するもう 1 つの例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="8098c-504">Here is another example to verify the Create action invokes Commit on the current unit of work.</span></span>

``` csharp
    [TestMethod]
    public void ShouldCommitUnitOfWork() {
        _controller.Create(_newEmployee);
        _unitOfWork.Verify(u => u.Commit());
    }
```

<span data-ttu-id="8098c-505">相互作用のテストに 1 つの危険性は、上の相互作用を指定する傾向です。</span><span class="sxs-lookup"><span data-stu-id="8098c-505">One danger with interaction testing is the tendency to over specify interactions.</span></span> <span data-ttu-id="8098c-506">記録して、テストといって、モック オブジェクトに対するすべての操作を確認するには、モック オブジェクトの機能は、すべての操作を確認しますしようとする必要があります。</span><span class="sxs-lookup"><span data-stu-id="8098c-506">The ability of the mock object to record and verify every interaction with the mock object doesn’t mean the test should try to verify every interaction.</span></span> <span data-ttu-id="8098c-507">いくつかの通信には、実装の詳細との相互作用を確認する必要がありますのみ*必要*を現在のテストを満たすためにします。</span><span class="sxs-lookup"><span data-stu-id="8098c-507">Some interactions are implementation details and you should only verify the interactions *required* to satisfy the current test.</span></span>

<span data-ttu-id="8098c-508">モックやフェイクの間で choice がシステムをテストして、個人に大きく左右 (またはチーム) 設定します。</span><span class="sxs-lookup"><span data-stu-id="8098c-508">The choice between mocks or fakes largely depends on the system you are testing and your personal (or team) preferences.</span></span> <span data-ttu-id="8098c-509">モック オブジェクトは、テストの代替を実装する必要があるコードの量を大幅に短縮できますが、誰もが快適なは、期待のプログラミングとの相互作用を確認しています。</span><span class="sxs-lookup"><span data-stu-id="8098c-509">Mock objects can drastically reduce the amount of code you need to implement test doubles, but not everyone is comfortable programming expectations and verifying interactions.</span></span>

## <a name="conclusions"></a><span data-ttu-id="8098c-510">まとめ</span><span class="sxs-lookup"><span data-stu-id="8098c-510">Conclusions</span></span>

<span data-ttu-id="8098c-511">このホワイト ペーパーでは、データの永続性、ADO.NET Entity Framework を使用中にテストしやすいコードを作成するいくつかの方法を説明してきました。</span><span class="sxs-lookup"><span data-stu-id="8098c-511">In this paper we’ve demonstrated several approaches to creating testable code while using the ADO.NET Entity Framework for data persistence.</span></span> <span data-ttu-id="8098c-512">IObjectSet などの組み込みの抽象化を利用できます&lt;T&gt;、IRepository のように、独自の抽象化を作成または&lt;T&gt;します。</span><span class="sxs-lookup"><span data-stu-id="8098c-512">We can leverage built in abstractions like IObjectSet&lt;T&gt;, or create our own abstractions like IRepository&lt;T&gt;.</span></span>  <span data-ttu-id="8098c-513">どちらの場合で、ADO.NET Entity Framework 4.0 で POCO サポートでは、依存しやすいに永続化するこれらの抽象化のコンシューマーができます。</span><span class="sxs-lookup"><span data-stu-id="8098c-513">In both cases, the POCO support in the ADO.NET Entity Framework 4.0 allows the consumers of these abstractions to remain persistent ignorant and highly testable.</span></span> <span data-ttu-id="8098c-514">暗黙的な遅延読み込みにより、ビジネスおよびアプリケーションのサービスと同様に、追加の EF4 機能は、リレーショナル データ ストアの詳細について心配することがなく動作するコードです。</span><span class="sxs-lookup"><span data-stu-id="8098c-514">Additional EF4 features like implicit lazy loading allows business and application service code to work without worrying about the details of a relational data store.</span></span> <span data-ttu-id="8098c-515">最後に、作成抽象化は簡単にモックやフェイク単体テストでは、内部でと、これらの高度に分離された高速の実行を実現するために、テスト代替と信頼性の高いテストを使用します。</span><span class="sxs-lookup"><span data-stu-id="8098c-515">Finally, the abstractions we create are easy to mock or fake inside of unit tests, and we can use these test doubles to achieve fast running, highly isolated, and reliable tests.</span></span>

### <a name="additional-resources"></a><span data-ttu-id="8098c-516">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="8098c-516">Additional Resources</span></span>

-   <span data-ttu-id="8098c-517">Robert C. Martin、"[単一責任の原則](http://www.objectmentor.com/resources/articles/srp.pdf)"</span><span class="sxs-lookup"><span data-stu-id="8098c-517">Robert C. Martin, “ [The Single Responsibility Principle](http://www.objectmentor.com/resources/articles/srp.pdf)”</span></span>
-   <span data-ttu-id="8098c-518">Martin Fowler、[パターンのカタログ](http://www.martinfowler.com/eaaCatalog/index.html)から*エンタープライズ アプリケーション アーキテクチャのパターン*</span><span class="sxs-lookup"><span data-stu-id="8098c-518">Martin Fowler, [Catalog of Patterns](http://www.martinfowler.com/eaaCatalog/index.html) from *Patterns of Enterprise Application Architecture*</span></span>
-   <span data-ttu-id="8098c-519">Griffin の Caprio"[依存関係の注入](https://msdn.microsoft.com/magazine/cc163739.aspx)"</span><span class="sxs-lookup"><span data-stu-id="8098c-519">Griffin Caprio, “ [Dependency Injection](https://msdn.microsoft.com/magazine/cc163739.aspx)”</span></span>
-   <span data-ttu-id="8098c-520">データ プログラマビリティ ブログでは、"[チュートリアル: Entity Framework 4.0 によるテスト駆動開発](https://blogs.msdn.com/adonet/pages/walkthrough-test-driven-development-with-the-entity-framework-4-0.aspx)"。</span><span class="sxs-lookup"><span data-stu-id="8098c-520">Data Programmability Blog, “ [Walkthrough: Test Driven Development with the Entity Framework 4.0](https://blogs.msdn.com/adonet/pages/walkthrough-test-driven-development-with-the-entity-framework-4-0.aspx)”.</span></span>
-   <span data-ttu-id="8098c-521">データ プログラマビリティ ブログでは、" [with Entity Framework 4.0 を使用してリポジトリと Unit of Work パターン](https://blogs.msdn.com/adonet/archive/2009/06/16/using-repository-and-unit-of-work-patterns-with-entity-framework-4-0.aspx)"</span><span class="sxs-lookup"><span data-stu-id="8098c-521">Data Programmability Blog, “ [Using Repository and Unit of Work patterns with Entity Framework 4.0](https://blogs.msdn.com/adonet/archive/2009/06/16/using-repository-and-unit-of-work-patterns-with-entity-framework-4-0.aspx)”</span></span>
-   <span data-ttu-id="8098c-522">Dave Astels、" [BDD 入門](http://blog.daveastels.com/files/BDD_Intro.pdf)"</span><span class="sxs-lookup"><span data-stu-id="8098c-522">Dave Astels, “ [BDD Intro](http://blog.daveastels.com/files/BDD_Intro.pdf)”</span></span>
-   <span data-ttu-id="8098c-523">Aaron Jensen、"[マシンの仕様を導入](http://codebetter.com/blogs/aaron.jensen/archive/2008/05/08/introducing-machine-specifications-or-mspec-for-short.aspx)"</span><span class="sxs-lookup"><span data-stu-id="8098c-523">Aaron Jensen, “ [Introducing Machine Specifications](http://codebetter.com/blogs/aaron.jensen/archive/2008/05/08/introducing-machine-specifications-or-mspec-for-short.aspx)”</span></span>
-   <span data-ttu-id="8098c-524">Eric Lee、" [MSTest を使用した BDD](https://blogs.msdn.com/elee/archive/2009/01/20/bdd-with-mstest.aspx)"</span><span class="sxs-lookup"><span data-stu-id="8098c-524">Eric Lee, “ [BDD with MSTest](https://blogs.msdn.com/elee/archive/2009/01/20/bdd-with-mstest.aspx)”</span></span>
-   <span data-ttu-id="8098c-525">Eric evans 著、" [Domain Driven Design](http://books.google.com/books?id=7dlaMs0SECsC&printsec=frontcover&dq=evans%20domain%20driven%20design&hl=en&ei=cHztS6C8KIaglAfA_dS1CA&sa=X&oi=book_result&ct=result&resnum=1&ved=0CCoQ6AEwAA)"</span><span class="sxs-lookup"><span data-stu-id="8098c-525">Eric Evans, “ [Domain Driven Design](http://books.google.com/books?id=7dlaMs0SECsC&printsec=frontcover&dq=evans%20domain%20driven%20design&hl=en&ei=cHztS6C8KIaglAfA_dS1CA&sa=X&oi=book_result&ct=result&resnum=1&ved=0CCoQ6AEwAA)”</span></span>
-   <span data-ttu-id="8098c-526">Martin fowler 氏は、"[モックとスタブ](http://martinfowler.com/articles/mocksArentStubs.html)"</span><span class="sxs-lookup"><span data-stu-id="8098c-526">Martin Fowler, “ [Mocks Aren’t Stubs](http://martinfowler.com/articles/mocksArentStubs.html)”</span></span>
-   <span data-ttu-id="8098c-527">Martin fowler 氏は、"[テスト ダブル](http://martinfowler.com/bliki/TestDouble.html)"</span><span class="sxs-lookup"><span data-stu-id="8098c-527">Martin Fowler, “ [Test Double](http://martinfowler.com/bliki/TestDouble.html)”</span></span>
-   <span data-ttu-id="8098c-528">Jeremy Miller"[状態相互作用のテストと](http://codebetter.com/blogs/jeremy.miller/articles/129544.aspx)"</span><span class="sxs-lookup"><span data-stu-id="8098c-528">Jeremy Miller, “ [State versus Interaction Testing](http://codebetter.com/blogs/jeremy.miller/articles/129544.aspx)”</span></span>
-   [<span data-ttu-id="8098c-529">Moq</span><span class="sxs-lookup"><span data-stu-id="8098c-529">Moq</span></span>](http://code.google.com/p/moq/)

### <a name="biography"></a><span data-ttu-id="8098c-530">略歴</span><span class="sxs-lookup"><span data-stu-id="8098c-530">Biography</span></span>

<span data-ttu-id="8098c-531">Scott Allen は、Pluralsight の技術スタッフのメンバーと OdeToCode.com の創設者です。</span><span class="sxs-lookup"><span data-stu-id="8098c-531">Scott Allen is a member of the technical staff at Pluralsight and the founder of OdeToCode.com.</span></span> <span data-ttu-id="8098c-532">商用ソフトウェア開発の 15 年間は、ASP.NET web アプリケーションを非常にスケーラブルな 8 ビットの組み込みデバイスからすべてのソリューションで Scott、努めてきました。</span><span class="sxs-lookup"><span data-stu-id="8098c-532">In 15 years of commercial software development, Scott has worked on solutions for everything from 8-bit embedded devices to highly scalable ASP.NET web applications.</span></span> <span data-ttu-id="8098c-533">Scott は、OdeToCode、彼のブログや Twitter は、上に到達できる[ http://twitter.com/OdeToCode](http://twitter.com/OdeToCode)します。</span><span class="sxs-lookup"><span data-stu-id="8098c-533">You can reach Scott on his blog at OdeToCode, or on Twitter at [http://twitter.com/OdeToCode](http://twitter.com/OdeToCode).</span></span>
