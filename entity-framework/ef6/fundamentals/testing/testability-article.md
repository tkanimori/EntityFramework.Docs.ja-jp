---
title: テストの容易性と Entity Framework 4.0-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 9430e2ab-261c-4e8e-8545-2ebc52d7a247
ms.openlocfilehash: 28ec5446ce9faf98fb8fff141832236d70b29daf
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78413979"
---
# <a name="testability-and-entity-framework-40"></a><span data-ttu-id="74527-102">テストの容易性と Entity Framework 4.0</span><span class="sxs-lookup"><span data-stu-id="74527-102">Testability and Entity Framework 4.0</span></span>
<span data-ttu-id="74527-103">Scott Allen</span><span class="sxs-lookup"><span data-stu-id="74527-103">Scott Allen</span></span>

<span data-ttu-id="74527-104">公開: 2010 年 5 月</span><span class="sxs-lookup"><span data-stu-id="74527-104">Published: May 2010</span></span>

## <a name="introduction"></a><span data-ttu-id="74527-105">はじめに</span><span class="sxs-lookup"><span data-stu-id="74527-105">Introduction</span></span>

<span data-ttu-id="74527-106">このホワイトペーパーでは、ADO.NET Entity Framework 4.0 と Visual Studio 2010 を使用して、テスト可能なコードを記述する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="74527-106">This white paper describes and demonstrates how to write testable code with the ADO.NET Entity Framework 4.0 and Visual Studio 2010.</span></span> <span data-ttu-id="74527-107">このホワイトペーパーでは、テスト駆動設計 (TDD) や動作駆動設計 (BDD) など、特定のテスト方法に焦点を当てません。</span><span class="sxs-lookup"><span data-stu-id="74527-107">This paper does not try to focus on a specific testing methodology, like test-driven design (TDD) or behavior-driven design (BDD).</span></span> <span data-ttu-id="74527-108">代わりに、このホワイトペーパーでは、ADO.NET Entity Framework を使用するコードを記述する方法について重点的に説明します。また、簡単に分離してテストすることができます。</span><span class="sxs-lookup"><span data-stu-id="74527-108">Instead this paper will focus on how to write code that uses the ADO.NET Entity Framework yet remains easy to isolate and test in an automated fashion.</span></span> <span data-ttu-id="74527-109">ここでは、データアクセスのシナリオでのテストを容易にする共通のデザインパターンについて説明し、フレームワークの使用時にこれらのパターンを適用する方法を見ていきます。</span><span class="sxs-lookup"><span data-stu-id="74527-109">We’ll look at common design patterns that facilitate testing in data access scenarios and see how to apply those patterns when using the framework.</span></span> <span data-ttu-id="74527-110">また、これらの機能がテスト可能なコードでどのように動作するかを確認するために、フレームワークの特定の機能についても説明します。</span><span class="sxs-lookup"><span data-stu-id="74527-110">We’ll also look at specific features of the framework to see how those features can work in testable code.</span></span>

## <a name="what-is-testable-code"></a><span data-ttu-id="74527-111">テスト可能なコードとは</span><span class="sxs-lookup"><span data-stu-id="74527-111">What Is Testable Code?</span></span>

<span data-ttu-id="74527-112">自動単体テストを使用してソフトウェアの一部を検証する機能には、多くの望ましい利点があります。</span><span class="sxs-lookup"><span data-stu-id="74527-112">The ability to verify a piece of software using automated unit tests offers many desirable benefits.</span></span> <span data-ttu-id="74527-113">優れたテストはアプリケーションのソフトウェア障害の数を減らし、アプリケーションの品質を向上させることができますが、単体テストはバグの発見だけではありません。</span><span class="sxs-lookup"><span data-stu-id="74527-113">Everyone knows that good tests will reduce the number of software defects in an application and increase the application’s quality - but having unit tests in place goes far beyond just finding bugs.</span></span>

<span data-ttu-id="74527-114">優れた単体テストスイートを使用すると、開発チームは時間を節約し、作成したソフトウェアの制御を維持できます。</span><span class="sxs-lookup"><span data-stu-id="74527-114">A good unit test suite allows a development team to save time and remain in control of the software they create.</span></span> <span data-ttu-id="74527-115">チームは、既存のコードに変更を加えたり、新しい要件を満たすようにソフトウェアをリファクター、再設計、再構築したり、テストスイートでアプリケーションの動作を検証できることを確認しながら、アプリケーションに新しいコンポーネントを追加したりできます。</span><span class="sxs-lookup"><span data-stu-id="74527-115">A team can make changes to existing code, refactor, redesign, and restructure software to meet new requirements, and add new components into an application all while knowing the test suite can verify the application’s behavior.</span></span> <span data-ttu-id="74527-116">単体テストは、変更を容易にし、複雑さが増すにつれてソフトウェアの保守性を維持するための簡単なフィードバックサイクルの一部です。</span><span class="sxs-lookup"><span data-stu-id="74527-116">Unit tests are part of a quick feedback cycle to facilitate change and preserve the maintainability of software as complexity increases.</span></span>

<span data-ttu-id="74527-117">ただし、単体テストには料金が伴います。</span><span class="sxs-lookup"><span data-stu-id="74527-117">Unit testing comes with a price, however.</span></span> <span data-ttu-id="74527-118">チームは、単体テストの作成と管理に時間を費やす必要があります。</span><span class="sxs-lookup"><span data-stu-id="74527-118">A team has to invest the time to create and maintain unit tests.</span></span> <span data-ttu-id="74527-119">これらのテストを作成するために必要な作業量は、基になるソフトウェアのテストの**容易**性に直接関係しています。</span><span class="sxs-lookup"><span data-stu-id="74527-119">The amount of effort required to create these tests is directly related to the **testability** of the underlying software.</span></span> <span data-ttu-id="74527-120">ソフトウェアはどれほど簡単にテストできますか。</span><span class="sxs-lookup"><span data-stu-id="74527-120">How easy is the software to test?</span></span> <span data-ttu-id="74527-121">テストの容易性を考慮してソフトウェアを設計するチームは、テスト不可能なソフトウェアを扱うチームよりも効率的なテストを作成します。</span><span class="sxs-lookup"><span data-stu-id="74527-121">A team designing software with testability in mind will create effective tests faster than the team working with un-testable software.</span></span>

<span data-ttu-id="74527-122">Microsoft は、ADO.NET Entity Framework 4.0 (EF4) を、テストの容易性を考慮して設計しました。</span><span class="sxs-lookup"><span data-stu-id="74527-122">Microsoft designed the ADO.NET Entity Framework 4.0 (EF4) with testability in mind.</span></span> <span data-ttu-id="74527-123">これは、開発者がフレームワークコード自体に対して単体テストを記述することを意味するわけではありません。</span><span class="sxs-lookup"><span data-stu-id="74527-123">This doesn’t mean developers will be writing unit tests against framework code itself.</span></span> <span data-ttu-id="74527-124">代わりに、EF4 のテストの容易性の目標によって、フレームワークの上に構築されるテスト可能なコードを簡単に作成できます。</span><span class="sxs-lookup"><span data-stu-id="74527-124">Instead, the testability goals for EF4 make it easy to create testable code that builds on top of the framework.</span></span> <span data-ttu-id="74527-125">具体的な例を確認する前に、テスト可能なコードの品質について理解しておくことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="74527-125">Before we look at specific examples, it’s worthwhile to understand the qualities of testable code.</span></span>

### <a name="the-qualities-of-testable-code"></a><span data-ttu-id="74527-126">テスト可能なコードの品質</span><span class="sxs-lookup"><span data-stu-id="74527-126">The Qualities of Testable Code</span></span>

<span data-ttu-id="74527-127">テストが容易なコードでは、常に少なくとも2つの特徴が表示されます。</span><span class="sxs-lookup"><span data-stu-id="74527-127">Code that is easy to test will always exhibit at least two traits.</span></span> <span data-ttu-id="74527-128">まず、テスト可能なコードは簡単に確認**できます。**</span><span class="sxs-lookup"><span data-stu-id="74527-128">First, testable code is easy to **observe**.</span></span> <span data-ttu-id="74527-129">入力のセットを指定すると、コードの出力を簡単に確認できるようになります。</span><span class="sxs-lookup"><span data-stu-id="74527-129">Given some set of inputs, it should be easy to observe the output of the code.</span></span> <span data-ttu-id="74527-130">たとえば、メソッドが計算の結果を直接返すため、次のメソッドを簡単にテストできます。</span><span class="sxs-lookup"><span data-stu-id="74527-130">For example, testing the following method is easy because the method directly returns the result of a calculation.</span></span>

``` csharp
    public int Add(int x, int y) {
        return x + y;
    }
```

<span data-ttu-id="74527-131">メソッドが計算された値をネットワークソケット、データベーステーブル、または次のコードのようなファイルに書き込む場合、メソッドのテストは困難です。</span><span class="sxs-lookup"><span data-stu-id="74527-131">Testing a method is difficult if the method writes the computed value into a network socket, a database table, or a file like the following code.</span></span> <span data-ttu-id="74527-132">テストでは、値を取得するために追加の作業を実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="74527-132">The test has to perform additional work to retrieve the value.</span></span>

``` csharp
    public void AddAndSaveToFile(int x, int y) {
         var results = string.Format("The answer is {0}", x + y);
         File.WriteAllText("results.txt", results);
    }
```

<span data-ttu-id="74527-133">2番目に、テスト可能なコードは簡単に**分離**できます。</span><span class="sxs-lookup"><span data-stu-id="74527-133">Secondly, testable code is easy to **isolate**.</span></span> <span data-ttu-id="74527-134">テスト可能なコードの不適切な例として、次の擬似コードを使用してみましょう。</span><span class="sxs-lookup"><span data-stu-id="74527-134">Let’s use the following pseudo-code as a bad example of testable code.</span></span>

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

<span data-ttu-id="74527-135">このメソッドは簡単に観察できます。保険ポリシーを渡して、戻り値が予期される結果と一致することを確認することができます。</span><span class="sxs-lookup"><span data-stu-id="74527-135">The method is easy to observe – we can pass in an insurance policy and verify the return value matches an expected result.</span></span> <span data-ttu-id="74527-136">ただし、メソッドをテストするには、適切なスキーマを使用してデータベースをインストールし、メソッドが電子メールを送信しようとしたときに SMTP サーバーを構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="74527-136">However, to test the method we’ll need to have a database installed with the correct schema, and configure the SMTP server in case the method tries to send an email.</span></span>

<span data-ttu-id="74527-137">単体テストでは、メソッド内の計算ロジックだけを検証する必要がありますが、電子メールサーバーがオフラインになっているか、データベースサーバーが移動したことが原因で、テストが失敗する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="74527-137">The unit test only wants to verify the calculation logic inside the method, but the test might fail because the email server is offline, or because the database server moved.</span></span> <span data-ttu-id="74527-138">これらのエラーはどちらも、テストで検証しようとしている動作とは関係がありません。</span><span class="sxs-lookup"><span data-stu-id="74527-138">Both of these failures are unrelated to the behavior the test wants to verify.</span></span> <span data-ttu-id="74527-139">この動作を分離することは困難です。</span><span class="sxs-lookup"><span data-stu-id="74527-139">The behavior is difficult to isolate.</span></span>

<span data-ttu-id="74527-140">テスト可能なコードを記述しようとしているソフトウェア開発者は、多くの場合、記述するコードにおける懸念事項の分離を維持することに努めています。</span><span class="sxs-lookup"><span data-stu-id="74527-140">Software developers who strive to write testable code often strive to maintain a separation of concerns in the code they write.</span></span> <span data-ttu-id="74527-141">上記の方法では、ビジネスの計算に焦点を当て、データベースおよび電子メールの実装の詳細を他のコンポーネントに委任する必要があります。</span><span class="sxs-lookup"><span data-stu-id="74527-141">The above method should focus on the business calculations and delegate the database and email implementation details to other components.</span></span> <span data-ttu-id="74527-142">Robert Martin は、これを単一責任の原則と呼びます。</span><span class="sxs-lookup"><span data-stu-id="74527-142">Robert C. Martin calls this the Single Responsibility Principle.</span></span> <span data-ttu-id="74527-143">オブジェクトは、ポリシーの値の計算など、1つの限定された責任をカプセル化する必要があります。</span><span class="sxs-lookup"><span data-stu-id="74527-143">An object should encapsulate a single, narrow responsibility, like calculating the value of a policy.</span></span> <span data-ttu-id="74527-144">他のすべてのデータベースと通知の作業は、他のオブジェクトの役割である必要があります。</span><span class="sxs-lookup"><span data-stu-id="74527-144">All other database and notification work should be the responsibility of some other object.</span></span> <span data-ttu-id="74527-145">この方法で記述されたコードは、単一のタスクに重点があるため、分離しやすくなります。</span><span class="sxs-lookup"><span data-stu-id="74527-145">Code written in this fashion is easier to isolate because it is focused on a single task.</span></span>

<span data-ttu-id="74527-146">.NET では、単一責任の原則に従って分離を実現するために必要な抽象化が用意されています。</span><span class="sxs-lookup"><span data-stu-id="74527-146">In .NET we have the abstractions we need to follow the Single Responsibility Principle and achieve isolation.</span></span> <span data-ttu-id="74527-147">インターフェイス定義を使用して、具象型ではなくインターフェイスの抽象化を使用するようにコードを強制できます。</span><span class="sxs-lookup"><span data-stu-id="74527-147">We can use interface definitions and force the code to use the interface abstraction instead of a concrete type.</span></span> <span data-ttu-id="74527-148">この記事の後半では、上記の不適切な例のようなメソッドが、データベースと通信するように*見える*インターフェイスを使用する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="74527-148">Later in this paper we’ll see how a method like the bad example presented above can work with interfaces that *look* like they will talk to the database.</span></span> <span data-ttu-id="74527-149">ただし、テスト時には、データベースとは通信しないダミーの実装に置き換えることができますが、データはメモリに保持されます。</span><span class="sxs-lookup"><span data-stu-id="74527-149">At test time, however, we can substitute a dummy implementation that doesn’t talk to the database but instead holds data in memory.</span></span> <span data-ttu-id="74527-150">このダミー実装は、データアクセスコードまたはデータベース構成の関連のない問題からコードを分離します。</span><span class="sxs-lookup"><span data-stu-id="74527-150">This dummy implementation will isolate the code from unrelated problems in the data access code or database configuration.</span></span>

<span data-ttu-id="74527-151">分離には他にも利点があります。</span><span class="sxs-lookup"><span data-stu-id="74527-151">There are additional benefits to isolation.</span></span> <span data-ttu-id="74527-152">最後のメソッドのビジネスの計算は、実行に数ミリ秒しかかかりませんが、テスト自体は、ネットワーク上でコードがホップし、さまざまなサーバーと通信するため、数秒間実行される可能性があります。</span><span class="sxs-lookup"><span data-stu-id="74527-152">The business calculation in the last method should only take a few milliseconds to execute, but the test itself might run for several seconds as the code hops around the network and talks to various servers.</span></span> <span data-ttu-id="74527-153">小さな変更を容易にするために、単体テストが高速に実行される必要があります。</span><span class="sxs-lookup"><span data-stu-id="74527-153">Unit tests should run fast to facilitate small changes.</span></span> <span data-ttu-id="74527-154">テストに関連付けられていないコンポーネントに問題があるため、単体テストも反復可能で、失敗することはありません。</span><span class="sxs-lookup"><span data-stu-id="74527-154">Unit tests should also be repeatable and not fail because a component unrelated to the test has a problem.</span></span> <span data-ttu-id="74527-155">見やすく、分離するためのコードを記述すると、開発者はコードのテストを作成しやすくなり、テストの実行を待機する時間が短縮されます。さらに重要なのは、存在しないバグを追跡する時間を短縮することです。</span><span class="sxs-lookup"><span data-stu-id="74527-155">Writing code that is easy to observe and to isolate means developers will have an easier time writing tests for the code, spend less time waiting for tests to execute, and more importantly, spend less time tracking down bugs that do not exist.</span></span>

<span data-ttu-id="74527-156">テストの利点を評価し、テスト可能なコードが示す品質を理解することができます。</span><span class="sxs-lookup"><span data-stu-id="74527-156">Hopefully you can appreciate the benefits of testing and understand the qualities that testable code exhibits.</span></span> <span data-ttu-id="74527-157">この記事では、EF4 と連携してデータをデータベースに保存するコードを記述する方法について説明しますが、このコードでは、データアクセスのテスト可能な設計について説明します。</span><span class="sxs-lookup"><span data-stu-id="74527-157">We are about to address how to write code that works with EF4 to save data into a database while remaining observable and easy to isolate, but first we’ll narrow our focus to discuss testable designs for data access.</span></span>

## <a name="design-patterns-for-data-persistence"></a><span data-ttu-id="74527-158">データ永続化の設計パターン</span><span class="sxs-lookup"><span data-stu-id="74527-158">Design Patterns for Data Persistence</span></span>

<span data-ttu-id="74527-159">前述の不適切な例には、どちらもあまり多くの責任がありません。</span><span class="sxs-lookup"><span data-stu-id="74527-159">Both of the bad examples presented earlier had too many responsibilities.</span></span> <span data-ttu-id="74527-160">最初の不適切な例では、計算を実行し*て*ファイルに書き込む必要がありました。</span><span class="sxs-lookup"><span data-stu-id="74527-160">The first bad example had to perform a calculation *and* write to a file.</span></span> <span data-ttu-id="74527-161">2番目の不適切な例では、データベースからデータを読み取り *、* ビジネスの計算を実行し*て*電子メールを送信する必要がありました。</span><span class="sxs-lookup"><span data-stu-id="74527-161">The second bad example had to read data from a database *and* perform a business calculation *and* send email.</span></span> <span data-ttu-id="74527-162">関心を分離し、他のコンポーネントに責任を委任する小さなメソッドを設計することにより、テスト可能なコードを記述することに大きな進化を加えます。</span><span class="sxs-lookup"><span data-stu-id="74527-162">By designing smaller methods that separate concerns and delegate responsibility to other components you’ll make great strides towards writing testable code.</span></span> <span data-ttu-id="74527-163">目標は、小規模で焦点を絞った抽象化からアクションを作成することによって機能を構築することです。</span><span class="sxs-lookup"><span data-stu-id="74527-163">The goal is to build functionality by composing actions from small and focused abstractions.</span></span>

<span data-ttu-id="74527-164">データの永続化に関しては、探している小さな抽象化された抽象化が、設計パターンとしてドキュメントに記載されていることがよくあります。</span><span class="sxs-lookup"><span data-stu-id="74527-164">When it comes to data persistence the small and focused abstractions we are looking for are so common they’ve been documented as design patterns.</span></span> <span data-ttu-id="74527-165">エンタープライズアプリケーションアーキテクチャの Martin Fowler の書籍パターンは、これらのパターンを印刷で記述するための最初の作業でした。</span><span class="sxs-lookup"><span data-stu-id="74527-165">Martin Fowler’s book Patterns of Enterprise Application Architecture was the first work to describe these patterns in print.</span></span> <span data-ttu-id="74527-166">これらの ADO.NET Entity Framework がこれらのパターンをどのように実装して使用するかを説明する前に、これらのパターンについて簡単に説明します。</span><span class="sxs-lookup"><span data-stu-id="74527-166">We’ll provide a brief description of these patterns in the following sections before we show how these ADO.NET Entity Framework implements and works with these patterns.</span></span>

### <a name="the-repository-pattern"></a><span data-ttu-id="74527-167">リポジトリ パターン</span><span class="sxs-lookup"><span data-stu-id="74527-167">The Repository Pattern</span></span>

<span data-ttu-id="74527-168">Fowler は、ドメインオブジェクトにアクセスするためのコレクションに似たインターフェイスを使用して、ドメインとデータマッピングのレイヤーを仲介することを意味します。</span><span class="sxs-lookup"><span data-stu-id="74527-168">Fowler says a repository “mediates between the domain and data mapping layers using a collection-like interface for accessing domain objects”.</span></span> <span data-ttu-id="74527-169">リポジトリパターンの目的は、データアクセスの minutiae からコードを分離することです。これまでの分離は、テストの容易性のために必要な特徴です。</span><span class="sxs-lookup"><span data-stu-id="74527-169">The goal of the repository pattern is to isolate code from the minutiae of data access, and as we saw earlier isolation is a required trait for testability.</span></span>

<span data-ttu-id="74527-170">分離の鍵となるのは、リポジトリがコレクションに似たインターフェイスを使用してオブジェクトを公開する方法です。</span><span class="sxs-lookup"><span data-stu-id="74527-170">The key to the isolation is how the repository exposes objects using a collection-like interface.</span></span> <span data-ttu-id="74527-171">リポジトリを使用するために記述するロジックには、要求したオブジェクトをリポジトリが具体化する方法はありません。</span><span class="sxs-lookup"><span data-stu-id="74527-171">The logic you write to use the repository has no idea how the repository will materialize the objects you request.</span></span> <span data-ttu-id="74527-172">リポジトリはデータベースと通信する場合もあれば、メモリ内コレクションからオブジェクトを返す場合もあります。</span><span class="sxs-lookup"><span data-stu-id="74527-172">The repository might talk to a database, or it might just return objects from an in-memory collection.</span></span> <span data-ttu-id="74527-173">すべてのコードは、コレクションを維持するためにリポジトリが表示されること、およびコレクションからオブジェクトを取得、追加、および削除できることを認識している必要があります。</span><span class="sxs-lookup"><span data-stu-id="74527-173">All your code needs to know is that the repository appears to maintain the collection, and you can retrieve, add, and delete objects from the collection.</span></span>

<span data-ttu-id="74527-174">既存の .NET アプリケーションでは、多くの場合、具象リポジトリは次のようなジェネリックインターフェイスから継承されます。</span><span class="sxs-lookup"><span data-stu-id="74527-174">In existing .NET applications a concrete repository often inherits from a generic interface like the following:</span></span>

``` csharp
    public interface IRepository<T> {       
        IEnumerable<T> FindAll();
        IEnumerable<T> FindBy(Expression<Func\<T, bool>> predicate);
        T FindById(int id);
        void Add(T newEntity);
        void Remove(T entity);
    }
```

<span data-ttu-id="74527-175">EF4 の実装を提供するときに、インターフェイス定義にいくつかの変更を行いますが、基本的な概念は変わりません。</span><span class="sxs-lookup"><span data-stu-id="74527-175">We’ll make a few changes to the interface definition when we provide an implementation for EF4, but the basic concept remains the same.</span></span> <span data-ttu-id="74527-176">コードでは、このインターフェイスを実装する具象リポジトリを使用して、その主キー値によってエンティティを取得したり、述語の評価に基づいてエンティティのコレクションを取得したり、使用可能なすべてのエンティティを取得したりすることができます。</span><span class="sxs-lookup"><span data-stu-id="74527-176">Code can use a concrete repository implementing this interface to retrieve an entity by its primary key value, to retrieve a collection of entities based on the evaluation of a predicate, or simply retrieve all available entities.</span></span> <span data-ttu-id="74527-177">このコードでは、リポジトリインターフェイスを使用してエンティティを追加および削除することもできます。</span><span class="sxs-lookup"><span data-stu-id="74527-177">The code can also add and remove entities through the repository interface.</span></span>

<span data-ttu-id="74527-178">Employee オブジェクトの IRepository を指定すると、コードは次の操作を実行できます。</span><span class="sxs-lookup"><span data-stu-id="74527-178">Given an IRepository of Employee objects, code can perform the following operations.</span></span>

``` csharp
    var employeesNamedScott =
        repository
            .FindBy(e => e.Name == "Scott")
            .OrderBy(e => e.HireDate);
    var firstEmployee = repository.FindById(1);
    var newEmployee = new Employee() {/*... */};
    repository.Add(newEmployee);
```

<span data-ttu-id="74527-179">コードはインターフェイス (Employee の IRepository) を使用しているため、インターフェイスのさまざまな実装でコードを提供できます。</span><span class="sxs-lookup"><span data-stu-id="74527-179">Since the code is using an interface (IRepository of Employee), we can provide the code with different implementations of the interface.</span></span> <span data-ttu-id="74527-180">1つの実装は、EF4 によってサポートされ、オブジェクトを Microsoft SQL Server データベースに保持する実装である場合があります。</span><span class="sxs-lookup"><span data-stu-id="74527-180">One implementation might be an implementation backed by EF4 and persisting objects into a Microsoft SQL Server database.</span></span> <span data-ttu-id="74527-181">(テスト中に使用する) 別の実装は、Employee オブジェクトのメモリ内リストによってサポートされる場合があります。</span><span class="sxs-lookup"><span data-stu-id="74527-181">A different implementation (one we use during testing) might be backed by an in-memory List of Employee objects.</span></span> <span data-ttu-id="74527-182">インターフェイスは、コード内の分離を実現するのに役立ちます。</span><span class="sxs-lookup"><span data-stu-id="74527-182">The interface will help to achieve isolation in the code.</span></span>

<span data-ttu-id="74527-183">IRepository&lt;T&gt; インターフェイスでは、保存操作が公開されないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="74527-183">Notice the IRepository&lt;T&gt; interface does not expose a Save operation.</span></span> <span data-ttu-id="74527-184">既存のオブジェクトを更新するにはどうすればよいですか。</span><span class="sxs-lookup"><span data-stu-id="74527-184">How do we update existing objects?</span></span> <span data-ttu-id="74527-185">保存操作を含む IRepository 定義を経由することもあります。これらのリポジトリの実装では、オブジェクトをすぐにデータベースに保持する必要があります。</span><span class="sxs-lookup"><span data-stu-id="74527-185">You might come across IRepository definitions that do include the Save operation, and implementations of these repositories will need to immediately persist an object into the database.</span></span> <span data-ttu-id="74527-186">ただし、多くのアプリケーションでは、オブジェクトを個別に永続化する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="74527-186">However, in many applications we don’t want to persist objects individually.</span></span> <span data-ttu-id="74527-187">代わりに、さまざまなリポジトリからオブジェクトを有効にし、ビジネスアクティビティの一部としてそれらのオブジェクトを変更し、1つのアトミックな操作の一部としてすべてのオブジェクトを永続化する必要があります。</span><span class="sxs-lookup"><span data-stu-id="74527-187">Instead, we want to bring objects to life, perhaps from different repositories, modify those objects as part of a business activity, and then persist all the objects as part of a single, atomic operation.</span></span> <span data-ttu-id="74527-188">幸いにも、この種類の動作を許可するパターンがあります。</span><span class="sxs-lookup"><span data-stu-id="74527-188">Fortunately, there is a pattern to allow this type of behavior.</span></span>

### <a name="the-unit-of-work-pattern"></a><span data-ttu-id="74527-189">作業単位パターン</span><span class="sxs-lookup"><span data-stu-id="74527-189">The Unit of Work Pattern</span></span>

<span data-ttu-id="74527-190">Fowler は、1つの作業単位が "ビジネストランザクションの影響を受けるオブジェクトの一覧を保持し、変更と同時実行に関する問題の解決を調整する" ということを示しています。</span><span class="sxs-lookup"><span data-stu-id="74527-190">Fowler says a unit of work will “maintain a list of objects affected by a business transaction and coordinates the writing out of changes and the resolution of concurrency problems”.</span></span> <span data-ttu-id="74527-191">リポジトリから有効なオブジェクトに対する変更を追跡し、変更をコミットする作業単位を指定するときに、オブジェクトに加えられたすべての変更を保持するのは、作業単位の役割です。</span><span class="sxs-lookup"><span data-stu-id="74527-191">It is the responsibility of the unit of work to track changes to the objects we bring to life from a repository and persist any changes we’ve made to the objects when we tell the unit of work to commit the changes.</span></span> <span data-ttu-id="74527-192">また、すべてのリポジトリに追加した新しいオブジェクトを取得し、そのオブジェクトをデータベースに挿入して、削除を実行する作業単位も必要です。</span><span class="sxs-lookup"><span data-stu-id="74527-192">It’s also the responsibility of the unit of work to take the new objects we’ve added to all repositories and insert the objects into a database, and also mange deletion.</span></span>

<span data-ttu-id="74527-193">ADO.NET データセットを操作したことがある場合は、作業単位パターンについて既に理解しておく必要があります。</span><span class="sxs-lookup"><span data-stu-id="74527-193">If you’ve ever done any work with ADO.NET DataSets then you’ll already be familiar with the unit of work pattern.</span></span> <span data-ttu-id="74527-194">ADO.NET データセットには、DataRow オブジェクトの更新、削除、および挿入を追跡する機能があり、(TableAdapter を使用して) データベースへのすべての変更が調整される可能性がありました。</span><span class="sxs-lookup"><span data-stu-id="74527-194">ADO.NET DataSets had the ability to track our updates, deletions, and insertion of DataRow objects and could (with the help of a TableAdapter) reconcile all our changes to a database.</span></span> <span data-ttu-id="74527-195">ただし、DataSet オブジェクトは、基になるデータベースの切断されたサブセットをモデル化します。</span><span class="sxs-lookup"><span data-stu-id="74527-195">However, DataSet objects model a disconnected subset of the underlying database.</span></span> <span data-ttu-id="74527-196">作業単位パターンは同じ動作をしますが、データアクセスコードから分離され、データベースを認識しないビジネスオブジェクトとドメインオブジェクトで動作します。</span><span class="sxs-lookup"><span data-stu-id="74527-196">The unit of work pattern exhibits the same behavior, but works with business objects and domain objects that are isolated from data access code and unaware of the database.</span></span>

<span data-ttu-id="74527-197">.NET コードの作業単位をモデル化するための抽象化は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="74527-197">An abstraction to model the unit of work in .NET code might look like the following:</span></span>

``` csharp
    public interface IUnitOfWork {
        IRepository<Employee> Employees { get; }
        IRepository<Order> Orders { get; }
        IRepository<Customer> Customers { get; }
        void Commit();
    }
```

<span data-ttu-id="74527-198">作業単位からリポジトリ参照を公開することにより、1つの作業単位オブジェクトが、ビジネストランザクション中に具体化されたすべてのエンティティを追跡できるようになります。</span><span class="sxs-lookup"><span data-stu-id="74527-198">By exposing repository references from the unit of work we can ensure a single unit of work object has the ability to track all entities materialized during a business transaction.</span></span> <span data-ttu-id="74527-199">実際の作業単位に対して Commit メソッドを実装することは、すべてのマジックがデータベースとのメモリ内の変更を調整するために行われる場所です。</span><span class="sxs-lookup"><span data-stu-id="74527-199">The implementation of the Commit method for a real unit of work is where all the magic happens to reconcile in-memory changes with the database.</span></span> 

<span data-ttu-id="74527-200">IUnitOfWork 参照を指定すると、コードは1つ以上のリポジトリから取得したビジネスオブジェクトに変更を加え、アトミックコミット操作を使用してすべての変更を保存できます。</span><span class="sxs-lookup"><span data-stu-id="74527-200">Given an IUnitOfWork reference, code can make changes to business objects retrieved from one or more repositories and save all the changes using the atomic Commit operation.</span></span>

``` csharp
    var firstEmployee = unitofWork.Employees.FindById(1);
    var firstCustomer = unitofWork.Customers.FindById(1);
    firstEmployee.Name = "Alex";
    firstCustomer.Name = "Christopher";
    unitofWork.Commit();
```

### <a name="the-lazy-load-pattern"></a><span data-ttu-id="74527-201">遅延読み込みパターン</span><span class="sxs-lookup"><span data-stu-id="74527-201">The Lazy Load Pattern</span></span>

<span data-ttu-id="74527-202">Fowler は、"遅延読み込み" という名前を使用して、"必要なすべてのデータを含まないが、取得方法を知っている" オブジェクトを記述します。</span><span class="sxs-lookup"><span data-stu-id="74527-202">Fowler uses the name lazy load to describe “an object that doesn’t contain all of the data you need but knows how to get it”.</span></span> <span data-ttu-id="74527-203">透過的な遅延読み込みは、テスト可能なビジネスコードを記述し、リレーショナルデータベースを操作するときに必要となる重要な機能です。</span><span class="sxs-lookup"><span data-stu-id="74527-203">Transparent lazy loading is an important feature to have when writing testable business code and working with a relational database.</span></span> <span data-ttu-id="74527-204">例として、次のコードについて考えてみます。</span><span class="sxs-lookup"><span data-stu-id="74527-204">As an example, consider the following code.</span></span>

``` csharp
    var employee = repository.FindById(id);
    // ... and later ...
    foreach(var timeCard in employee.TimeCards) {
        // .. manipulate the timeCard
    }
```

<span data-ttu-id="74527-205">タイムカードコレクションはどのように設定されますか。</span><span class="sxs-lookup"><span data-stu-id="74527-205">How is the TimeCards collection populated?</span></span> <span data-ttu-id="74527-206">2つの回答が得られます。</span><span class="sxs-lookup"><span data-stu-id="74527-206">There are two possible answers.</span></span> <span data-ttu-id="74527-207">回答として、従業員リポジトリは従業員をフェッチするように求められた場合に、従業員に関連付けられたタイムカード情報と共に両方の従業員を取得するクエリを発行します。</span><span class="sxs-lookup"><span data-stu-id="74527-207">One answer is that the employee repository, when asked to fetch an employee, issues a query to retrieve both the employee along with the employee’s associated time card information.</span></span> <span data-ttu-id="74527-208">リレーショナルデータベースでは、通常、JOIN 句を使用するクエリが必要であり、アプリケーションの必要以上に多くの情報を取得することがあります。</span><span class="sxs-lookup"><span data-stu-id="74527-208">In relational databases this generally requires a query with a JOIN clause and may result in retrieving more information than an application needs.</span></span> <span data-ttu-id="74527-209">アプリケーションがタイムカードプロパティに触れる必要がない場合はどうすればよいでしょうか。</span><span class="sxs-lookup"><span data-stu-id="74527-209">What if the application never needs to touch the TimeCards property?</span></span>

<span data-ttu-id="74527-210">2番目の答えは、タイムカードプロパティを "オンデマンド" で読み込むことです。</span><span class="sxs-lookup"><span data-stu-id="74527-210">A second answer is to load the TimeCards property “on demand”.</span></span> <span data-ttu-id="74527-211">この遅延読み込みは、タイムカード情報を取得するための特別な Api をコードが呼び出さないため、暗黙的で、ビジネスロジックに対して透過的です。</span><span class="sxs-lookup"><span data-stu-id="74527-211">This lazy loading is implicit and transparent to the business logic because the code does not invoke special APIs to retrieve time card information.</span></span> <span data-ttu-id="74527-212">このコードでは、必要に応じてタイムカード情報が存在することを前提としています。</span><span class="sxs-lookup"><span data-stu-id="74527-212">The code assumes the time card information is present when needed.</span></span> <span data-ttu-id="74527-213">遅延読み込みには、通常、メソッド呼び出しの実行時のインターセプトに関連するマジックがいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="74527-213">There is some magic involved with lazy loading that generally involves runtime interception of method invocations.</span></span> <span data-ttu-id="74527-214">インターセプトコードは、ビジネスロジックをビジネスロジックとして解放したまま、データベースと通信し、タイムカード情報を取得する役割を担います。</span><span class="sxs-lookup"><span data-stu-id="74527-214">The intercepting code is responsible for talking to the database and retrieving time card information while leaving the business logic free to be business logic.</span></span> <span data-ttu-id="74527-215">この遅延読み込みマジックにより、ビジネスコードはデータ取得操作から分離され、テストが容易なコードになります。</span><span class="sxs-lookup"><span data-stu-id="74527-215">This lazy load magic allows the business code to isolate itself from data retrieval operations and results in more testable code.</span></span>

<span data-ttu-id="74527-216">遅延読み込みの欠点は、アプリケーションがタイムカード情報を必要とする場合、*コードが追加*のクエリを実行することです。</span><span class="sxs-lookup"><span data-stu-id="74527-216">The drawback to a lazy load is that when an application *does* need the time card information the code will execute an additional query.</span></span> <span data-ttu-id="74527-217">これは多くのアプリケーションでは問題ではありませんが、パフォーマンスに依存するアプリケーションやアプリケーションでは、多数の従業員オブジェクトをループし、ループの各反復処理中にクエリを実行してタイムカードを取得します (問題は N + 1 と呼ばれることがよくあります)。クエリの問題)、遅延読み込みはドラッグです。</span><span class="sxs-lookup"><span data-stu-id="74527-217">This isn’t a concern for many applications, but for performance sensitive applications or applications looping through a number of employee objects and executing a query to retrieve time cards during each iteration of the loop (a problem often referred to as the N+1 query problem), lazy loading is a drag.</span></span> <span data-ttu-id="74527-218">このようなシナリオでは、アプリケーションは、最も効率的な方法でタイムカード情報の読み込みを集中的することができます。</span><span class="sxs-lookup"><span data-stu-id="74527-218">In these scenarios an application might want to eagerly load time card information in the most efficient manner possible.</span></span>

<span data-ttu-id="74527-219">幸いにも、次のセクションに進み、これらのパターンを実装すると、EF4 が暗黙的な遅延読み込みと効率的な一括読み込みの両方をサポートしていることがわかります。</span><span class="sxs-lookup"><span data-stu-id="74527-219">Fortunately, we’ll see how EF4 supports both implicit lazy loads and efficient eager loads as we move into the next section and implement these patterns.</span></span>

## <a name="implementing-patterns-with-the-entity-framework"></a><span data-ttu-id="74527-220">Entity Framework を使用したパターンの実装</span><span class="sxs-lookup"><span data-stu-id="74527-220">Implementing Patterns with the Entity Framework</span></span>

<span data-ttu-id="74527-221">最後のセクションで説明したすべてのデザインパターンは、EF4 で実装するのが簡単です。</span><span class="sxs-lookup"><span data-stu-id="74527-221">The good news is that all of the design patterns we described in the last section are straightforward to implement with EF4.</span></span> <span data-ttu-id="74527-222">例として、単純な ASP.NET MVC アプリケーションを使用して、従業員とそれに関連付けられているタイムカード情報を編集し、表示します。</span><span class="sxs-lookup"><span data-stu-id="74527-222">To demonstrate we are going to use a simple ASP.NET MVC application to edit and display Employees and their associated time card information.</span></span> <span data-ttu-id="74527-223">まず、次の "plain old CLR objects" (POCOs) を使用します。</span><span class="sxs-lookup"><span data-stu-id="74527-223">We’ll start by using the following “plain old CLR objects” (POCOs).</span></span> 

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

<span data-ttu-id="74527-224">これらのクラス定義は、EF4 のさまざまなアプローチと機能を調べると若干変わりますが、その目的は、これらのクラスを可能な限り永続化非依存 (PI) として保持することです。</span><span class="sxs-lookup"><span data-stu-id="74527-224">These class definitions will change slightly as we explore different approaches and features of EF4, but the intent is to keep these classes as persistence ignorant (PI) as possible.</span></span> <span data-ttu-id="74527-225">PI オブジェクトは、その状態がデータベース内に存在するか*どう*か*を把握し*ていません。</span><span class="sxs-lookup"><span data-stu-id="74527-225">A PI object doesn’t know *how*, or even *if*, the state it holds lives inside a database.</span></span> <span data-ttu-id="74527-226">PI と POCOs は、テスト可能なソフトウェアで手に入ります。</span><span class="sxs-lookup"><span data-stu-id="74527-226">PI and POCOs go hand in hand with testable software.</span></span> <span data-ttu-id="74527-227">POCO アプローチを使用するオブジェクトは、データベースが存在しなくても動作するため、制約が少なく、柔軟性が高く、テストが簡単です。</span><span class="sxs-lookup"><span data-stu-id="74527-227">Objects using a POCO approach are less constrained, more flexible, and easier to test because they can operate without a database present.</span></span>

<span data-ttu-id="74527-228">POCOs が配置されたら、Visual Studio で Entity Data Model (EDM) を作成できます (図1を参照)。</span><span class="sxs-lookup"><span data-stu-id="74527-228">With the POCOs in place we can create an Entity Data Model (EDM) in Visual Studio (see figure 1).</span></span> <span data-ttu-id="74527-229">EDM を使用して、エンティティのコードを生成することはありません。</span><span class="sxs-lookup"><span data-stu-id="74527-229">We will not use the EDM to generate code for our entities.</span></span> <span data-ttu-id="74527-230">代わりに、lovingly によって構築されたエンティティを手作業で使用したいと考えています。</span><span class="sxs-lookup"><span data-stu-id="74527-230">Instead, we want to use the entities we lovingly craft by hand.</span></span> <span data-ttu-id="74527-231">EDM を使用してデータベーススキーマを生成するだけで、オブジェクトをデータベースにマップするために必要なメタデータ EF4 が提供されます。</span><span class="sxs-lookup"><span data-stu-id="74527-231">We will only use the EDM to generate our database schema and provide the metadata EF4 needs to map objects into the database.</span></span>

![ef test_01](~/ef6/media/eftest-01.jpg)

<span data-ttu-id="74527-233">**図 1**</span><span class="sxs-lookup"><span data-stu-id="74527-233">**Figure 1**</span></span>

<span data-ttu-id="74527-234">注: EDM モデルを最初に開発する場合は、EDM から POCO のクリーンなコードを生成できます。</span><span class="sxs-lookup"><span data-stu-id="74527-234">Note: if you want to develop the EDM model first, it is possible to generate clean, POCO code from the EDM.</span></span> <span data-ttu-id="74527-235">これを行うには、データプログラミングチームによって提供される Visual Studio 2010 拡張機能を使用します。</span><span class="sxs-lookup"><span data-stu-id="74527-235">You can do this with a Visual Studio 2010 extension provided by the Data Programmability team.</span></span> <span data-ttu-id="74527-236">拡張機能をダウンロードするには、Visual Studio の [ツール] メニューから拡張機能マネージャーを起動し、"POCO" のテンプレートのオンラインギャラリーを検索します (図2を参照)。</span><span class="sxs-lookup"><span data-stu-id="74527-236">To download the extension, launch the Extension Manager from the Tools menu in Visual Studio and search the online gallery of templates for “POCO” (See figure 2).</span></span> <span data-ttu-id="74527-237">EF には、いくつかの POCO テンプレートが用意されています。</span><span class="sxs-lookup"><span data-stu-id="74527-237">There are several POCO templates available for EF.</span></span> <span data-ttu-id="74527-238">テンプレートの使用方法の詳細については、「[チュートリアル: POCO template for the Entity Framework](https://blogs.msdn.com/adonet/pages/walkthrough-poco-template-for-the-entity-framework.aspx)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="74527-238">For more information on using the template, see “ [Walkthrough: POCO Template for the Entity Framework](https://blogs.msdn.com/adonet/pages/walkthrough-poco-template-for-the-entity-framework.aspx)”.</span></span>

![ef test_02](~/ef6/media/eftest-02.png)

<span data-ttu-id="74527-240">**図 2**</span><span class="sxs-lookup"><span data-stu-id="74527-240">**Figure 2**</span></span>

<span data-ttu-id="74527-241">この POCO の出発点から、テスト可能なコードに対する2つの異なるアプローチについて説明します。</span><span class="sxs-lookup"><span data-stu-id="74527-241">From this POCO starting point we will explore two different approaches to testable code.</span></span> <span data-ttu-id="74527-242">最初の方法では、EF アプローチを呼び出します。これは、Entity Framework API の抽象化を利用して作業単位とリポジトリを実装するためです。</span><span class="sxs-lookup"><span data-stu-id="74527-242">The first approach I call the EF approach because it leverages abstractions from the Entity Framework API to implement units of work and repositories.</span></span> <span data-ttu-id="74527-243">2番目の方法では、独自のカスタムリポジトリの抽象化を作成し、それぞれの方法の長所と短所を確認します。</span><span class="sxs-lookup"><span data-stu-id="74527-243">In the second approach we will create our own custom repository abstractions and then see the advantages and disadvantages of each approach.</span></span> <span data-ttu-id="74527-244">まず、EF のアプローチについて説明します。</span><span class="sxs-lookup"><span data-stu-id="74527-244">We’ll start by exploring the EF approach.</span></span>  

### <a name="an-ef-centric-implementation"></a><span data-ttu-id="74527-245">EF 中心の実装</span><span class="sxs-lookup"><span data-stu-id="74527-245">An EF Centric Implementation</span></span>

<span data-ttu-id="74527-246">ASP.NET MVC プロジェクトの次のコントローラーアクションを考えてみましょう。</span><span class="sxs-lookup"><span data-stu-id="74527-246">Consider the following controller action from an ASP.NET MVC project.</span></span> <span data-ttu-id="74527-247">アクションは Employee オブジェクトを取得し、結果を返して従業員の詳細ビューを表示します。</span><span class="sxs-lookup"><span data-stu-id="74527-247">The action retrieves an Employee object and returns a result to display a detailed view of the employee.</span></span>

``` csharp
    public ViewResult Details(int id) {
        var employee = _unitOfWork.Employees
                                  .Single(e => e.Id == id);
        return View(employee);
    }
```

<span data-ttu-id="74527-248">コードのテストが可能かどうか。</span><span class="sxs-lookup"><span data-stu-id="74527-248">Is the code testable?</span></span> <span data-ttu-id="74527-249">アクションの動作を確認するために必要なテストが少なくとも2つあります。</span><span class="sxs-lookup"><span data-stu-id="74527-249">There are at least two tests we’d need to verify the action’s behavior.</span></span> <span data-ttu-id="74527-250">まず、アクションが適切なビュー (簡単なテスト) を返すことを確認したいと考えています。</span><span class="sxs-lookup"><span data-stu-id="74527-250">First, we’d like to verify the action returns the correct view – an easy test.</span></span> <span data-ttu-id="74527-251">また、アクションによって適切な従業員が取得されることを確認するテストを作成し、データベースを照会するコードを実行せずにこれを実行したいと考えています。</span><span class="sxs-lookup"><span data-stu-id="74527-251">We’d also want to write a test to verify the action retrieves the correct employee, and we’d like to do this without executing code to query the database.</span></span> <span data-ttu-id="74527-252">テスト対象のコードを分離する必要があることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="74527-252">Remember we want to isolate the code under test.</span></span> <span data-ttu-id="74527-253">分離により、データアクセスコードまたはデータベース構成のバグが原因でテストが失敗しないようにすることができます。</span><span class="sxs-lookup"><span data-stu-id="74527-253">Isolation will ensure the test doesn’t fail because of a bug in the data access code or database configuration.</span></span> <span data-ttu-id="74527-254">テストが失敗した場合は、下位レベルのシステムコンポーネントではなく、コントローラーロジックにバグがあることがわかります。</span><span class="sxs-lookup"><span data-stu-id="74527-254">If the test fails, we will know we have a bug in the controller logic, and not in some lower level system component.</span></span>

<span data-ttu-id="74527-255">分離を実現するには、リポジトリと作業単位に対して前に説明したインターフェイスのような抽象化が必要です。</span><span class="sxs-lookup"><span data-stu-id="74527-255">To achieve isolation we’ll need some abstractions like the interfaces we presented earlier for repositories and units of work.</span></span> <span data-ttu-id="74527-256">リポジトリパターンは、ドメインオブジェクトとデータマッピングレイヤー間で仲介するように設計されています。</span><span class="sxs-lookup"><span data-stu-id="74527-256">Remember the repository pattern is designed to mediate between domain objects and the data mapping layer.</span></span> <span data-ttu-id="74527-257">このシナリオでは、EF4 はデータマッピング層で*あり*、IObjectSet&lt;t&gt; (system.string 名前空間から) というリポジトリに似た抽象化を既に提供しています。</span><span class="sxs-lookup"><span data-stu-id="74527-257">In this scenario EF4 *is* the data mapping layer, and already provides a repository-like abstraction named IObjectSet&lt;T&gt; (from the System.Data.Objects namespace).</span></span> <span data-ttu-id="74527-258">インターフェイス定義は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="74527-258">The interface definition looks like the following.</span></span>

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

<span data-ttu-id="74527-259">IObjectSet&lt;T&gt; は、(IEnumerable&lt;T&gt;を介して) オブジェクトのコレクションに似ており、シミュレートされたコレクションにオブジェクトを追加および削除するメソッドを提供するため、リポジトリの要件を満たしています。</span><span class="sxs-lookup"><span data-stu-id="74527-259">IObjectSet&lt;T&gt; meets the requirements for a repository because it resembles a collection of objects (via IEnumerable&lt;T&gt;) and provides methods to add and remove objects from the simulated collection.</span></span> <span data-ttu-id="74527-260">Attach メソッドと Detach メソッドは、EF4 API の追加機能を公開します。</span><span class="sxs-lookup"><span data-stu-id="74527-260">The Attach and Detach methods expose additional capabilities of the EF4 API.</span></span> <span data-ttu-id="74527-261">リポジトリのインターフェイスとして IObjectSet&lt;T&gt; を使用するには、リポジトリをバインドするために作業単位の抽象化が必要です。</span><span class="sxs-lookup"><span data-stu-id="74527-261">To use IObjectSet&lt;T&gt; as the interface for repositories we need a unit of work abstraction to bind repositories together.</span></span>

``` csharp
    public interface IUnitOfWork {
        IObjectSet<Employee> Employees { get; }
        IObjectSet<TimeCard> TimeCards { get; }
        void Commit();
    }
```

<span data-ttu-id="74527-262">このインターフェイスの具象実装の1つは、SQL Server と対話し、EF4 の ObjectContext クラスを使用して簡単に作成できます。</span><span class="sxs-lookup"><span data-stu-id="74527-262">One concrete implementation of this interface will talk to SQL Server and is easy to create using the ObjectContext class from EF4.</span></span> <span data-ttu-id="74527-263">ObjectContext クラスは、EF4 API の実際の作業単位です。</span><span class="sxs-lookup"><span data-stu-id="74527-263">The ObjectContext class is the real unit of work in the EF4 API.</span></span>

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

<span data-ttu-id="74527-264">IObjectSet&lt;T&gt; を生活に取り込むことは、ObjectContext オブジェクトの受け取る createobjectset<tentity> メソッドを呼び出すのと同じように簡単です。</span><span class="sxs-lookup"><span data-stu-id="74527-264">Bringing an IObjectSet&lt;T&gt; to life is as easy as invoking the CreateObjectSet method of the ObjectContext object.</span></span> <span data-ttu-id="74527-265">背後では、フレームワークは EDM で提供されたメタデータを使用して、具象 ObjectSet&lt;T&gt;を生成します。</span><span class="sxs-lookup"><span data-stu-id="74527-265">Behind the scenes the framework will use the metadata we provided in the EDM to produce a concrete ObjectSet&lt;T&gt;.</span></span> <span data-ttu-id="74527-266">クライアントコードでのテストの容易性を維持するために、IObjectSet&lt;T&gt; インターフェイスを返すことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="74527-266">We’ll stick with returning the IObjectSet&lt;T&gt; interface because it will help preserve testability in client code.</span></span>

<span data-ttu-id="74527-267">この具象実装は実稼働環境で役立ちますが、IUnitOfWork 抽象化を使用してテストを容易にする方法に焦点を当てる必要があります。</span><span class="sxs-lookup"><span data-stu-id="74527-267">This concrete implementation is useful in production, but we need to focus on how we’ll use our IUnitOfWork abstraction to facilitate testing.</span></span>

### <a name="the-test-doubles"></a><span data-ttu-id="74527-268">テストの倍精度浮動小数点数</span><span class="sxs-lookup"><span data-stu-id="74527-268">The Test Doubles</span></span>

<span data-ttu-id="74527-269">コントローラーアクションを分離するには、(ObjectContext によって支えられた) 実際の作業単位と、テストの double または "偽の" 作業単位 (インメモリ操作の実行) を切り替える機能が必要です。</span><span class="sxs-lookup"><span data-stu-id="74527-269">To isolate the controller action we’ll need the ability to switch between the real unit of work (backed by an ObjectContext) and a test double or “fake” unit of work (performing in-memory operations).</span></span> <span data-ttu-id="74527-270">この種の切り替えを実行する一般的な方法は、MVC コントローラーが作業単位をインスタンス化せずに、代わりに、コンストラクターパラメーターとしてコントローラーに作業単位を渡すことです。</span><span class="sxs-lookup"><span data-stu-id="74527-270">The common approach to perform this type of switching is to not let the MVC controller instantiate a unit of work, but instead pass the unit of work into the controller as a constructor parameter.</span></span>

``` csharp
    class EmployeeController : Controller {
      publicEmployeeController(IUnitOfWork unitOfWork)  {
          _unitOfWork = unitOfWork;
      }
      ...
    }
```

<span data-ttu-id="74527-271">上記のコードは、依存関係の挿入の例です。</span><span class="sxs-lookup"><span data-stu-id="74527-271">The above code is an example of dependency injection.</span></span> <span data-ttu-id="74527-272">コントローラーに依存関係 (作業単位) を作成することは許可されていませんが、コントローラーに依存関係が挿入されます。</span><span class="sxs-lookup"><span data-stu-id="74527-272">We don’t allow the controller to create it’s dependency (the unit of work) but inject the dependency into the controller.</span></span> <span data-ttu-id="74527-273">MVC プロジェクトでは、カスタムコントローラーファクトリとコントロールの反転 (IoC) コンテナーを組み合わせて使用して、依存関係の挿入を自動化するのが一般的です。</span><span class="sxs-lookup"><span data-stu-id="74527-273">In an MVC project it is common to use a custom controller factory in combination with an inversion of control (IoC) container to automate dependency injection.</span></span> <span data-ttu-id="74527-274">これらのトピックはこの記事の範囲を超えていますが、詳細については、この記事の最後に記載されている参照情報を参照してください。</span><span class="sxs-lookup"><span data-stu-id="74527-274">These topics are beyond the scope of this article, but you can read more by following the references at the end of this article.</span></span>

<span data-ttu-id="74527-275">テストに使用できる仮の作業単位実装は、次のようになります。</span><span class="sxs-lookup"><span data-stu-id="74527-275">A fake unit of work implementation that we can use for testing might look like the following.</span></span>

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

<span data-ttu-id="74527-276">仮の作業単位は、コミットされたプロパティを公開することに注意してください。</span><span class="sxs-lookup"><span data-stu-id="74527-276">Notice the fake unit of work exposes a Commited property.</span></span> <span data-ttu-id="74527-277">テストを容易にする偽クラスに機能を追加すると便利な場合があります。</span><span class="sxs-lookup"><span data-stu-id="74527-277">It’s sometimes useful to add features to a fake class that facilitate testing.</span></span> <span data-ttu-id="74527-278">この場合、コミットされたプロパティをチェックすることで、コードが作業単位をコミットするかどうかを簡単に確認できます。</span><span class="sxs-lookup"><span data-stu-id="74527-278">In this case it is easy to observe if code commits a unit of work by checking the Commited property.</span></span>

<span data-ttu-id="74527-279">また、従業員とタイムカードオブジェクトをメモリに保持するために、偽の IObjectSet&lt;T&gt; も必要になります。</span><span class="sxs-lookup"><span data-stu-id="74527-279">We’ll also need a fake IObjectSet&lt;T&gt; to hold Employee and TimeCard objects in memory.</span></span> <span data-ttu-id="74527-280">ジェネリックを使用して1つの実装を提供できます。</span><span class="sxs-lookup"><span data-stu-id="74527-280">We can provide a single implementation using generics.</span></span>

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

<span data-ttu-id="74527-281">このテストでは、ほとんどの作業を、基になる HashSet&lt;T&gt; オブジェクトにダブルデリゲートします。</span><span class="sxs-lookup"><span data-stu-id="74527-281">This test double delegates most of its work to an underlying HashSet&lt;T&gt; object.</span></span> <span data-ttu-id="74527-282">IObjectSet&lt;T&gt; には、クラス (参照型) として T を適用する汎用的な制約が必要です。また、IQueryable&lt;T&gt;も実装する必要があります。</span><span class="sxs-lookup"><span data-stu-id="74527-282">Note that IObjectSet&lt;T&gt; requires a generic constraint enforcing T as a class (a reference type), and also forces us to implement IQueryable&lt;T&gt;.</span></span> <span data-ttu-id="74527-283">標準的な LINQ 演算子 AsQueryable を使用して、メモリ内コレクションを IQueryable&lt;T&gt; として表示するのは簡単です。</span><span class="sxs-lookup"><span data-stu-id="74527-283">It is easy to make an in-memory collection appear as an IQueryable&lt;T&gt; using the standard LINQ operator AsQueryable.</span></span>

### <a name="the-tests"></a><span data-ttu-id="74527-284">テスト</span><span class="sxs-lookup"><span data-stu-id="74527-284">The Tests</span></span>

<span data-ttu-id="74527-285">従来の単体テストでは、単一のテストクラスを使用して、1つの MVC コントローラー内のすべてのアクションに対するすべてのテストを保持します。</span><span class="sxs-lookup"><span data-stu-id="74527-285">Traditional unit tests will use a single test class to hold all of the tests for all of the actions in a single MVC controller.</span></span> <span data-ttu-id="74527-286">これらのテスト、または任意の種類の単体テストを、作成したメモリ内のフェイクを使用して記述できます。</span><span class="sxs-lookup"><span data-stu-id="74527-286">We can write these tests, or any type of unit test, using the in memory fakes we’ve built.</span></span> <span data-ttu-id="74527-287">ただし、この記事では、モノリシックテストクラスのアプローチを避け、代わりに特定の機能に重点を置いてテストをグループ化します。</span><span class="sxs-lookup"><span data-stu-id="74527-287">However, for this article we will avoid the monolithic test class approach and instead group our tests to focus on a specific piece of functionality.</span></span><span data-ttu-id="74527-288">  たとえば、"新しい従業員の作成" はテストする機能である可能性があるため、1つのテストクラスを使用して、新しい従業員を作成する1つのコントローラーアクションを確認します。</span><span class="sxs-lookup"><span data-stu-id="74527-288">  For example, “create new employee” might be the functionality we want to test, so we will use a single test class to verify the single controller action responsible for creating a new employee.</span></span>

<span data-ttu-id="74527-289">これらの細かいテストクラスには、いくつかの一般的なセットアップコードが必要です。</span><span class="sxs-lookup"><span data-stu-id="74527-289">There is some common setup code we need for all these fine grained test classes.</span></span> <span data-ttu-id="74527-290">たとえば、常にメモリ内リポジトリと偽の作業単位を作成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="74527-290">For example, we always need to create our in-memory repositories and fake unit of work.</span></span> <span data-ttu-id="74527-291">また、作業単位が挿入された従業員コントローラーのインスタンスも必要です。</span><span class="sxs-lookup"><span data-stu-id="74527-291">We also need an instance of the employee controller with the fake unit of work injected.</span></span> <span data-ttu-id="74527-292">基本クラスを使用して、この共通セットアップコードをテストクラス間で共有します。</span><span class="sxs-lookup"><span data-stu-id="74527-292">We’ll share this common setup code across test classes by using a base class.</span></span>

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

<span data-ttu-id="74527-293">基底クラスで使用される "オブジェクト母" は、テストデータを作成するための一般的なパターンの1つです。</span><span class="sxs-lookup"><span data-stu-id="74527-293">The “object mother” we use in the base class is one common pattern for creating test data.</span></span> <span data-ttu-id="74527-294">オブジェクト母親には、複数のテストフィクスチャで使用するテストエンティティをインスタンス化するファクトリメソッドが含まれています。</span><span class="sxs-lookup"><span data-stu-id="74527-294">An object mother contains factory methods to instantiate test entities for use across multiple test fixtures.</span></span>

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

<span data-ttu-id="74527-295">EmployeeControllerTestBase は、多数のテストフィクスチャの基底クラスとして使用できます (図3を参照)。</span><span class="sxs-lookup"><span data-stu-id="74527-295">We can use the EmployeeControllerTestBase as the base class for a number of test fixtures (see figure 3).</span></span> <span data-ttu-id="74527-296">各テストフィクスチャは、特定のコントローラーアクションをテストします。</span><span class="sxs-lookup"><span data-stu-id="74527-296">Each test fixture will test a specific controller action.</span></span> <span data-ttu-id="74527-297">たとえば、あるテストフィクスチャは、(従業員を作成するためのビューを表示するために) HTTP GET 要求中に使用される作成アクションのテストに重点を置いています。また、別のフィクスチャは、HTTP POST 要求で使用される作成アクション (ユーザーが従業員を作成します)。</span><span class="sxs-lookup"><span data-stu-id="74527-297">For example, one test fixture will focus on testing the Create action used during an HTTP GET request (to display the view for creating an employee), and a different fixture will focus on the Create action used in an HTTP POST request (to take information submitted by the user to create an employee).</span></span> <span data-ttu-id="74527-298">各派生クラスは、特定のコンテキストで必要なセットアップだけを担当し、特定のテストコンテキストの結果を検証するために必要なアサーションを提供します。</span><span class="sxs-lookup"><span data-stu-id="74527-298">Each derived class is only responsible for the setup needed in its specific context, and to provide the assertions needed to verify the outcomes for its specific test context.</span></span>

![ef test_03](~/ef6/media/eftest-03.png)

<span data-ttu-id="74527-300">**図 3**</span><span class="sxs-lookup"><span data-stu-id="74527-300">**Figure 3**</span></span>

<span data-ttu-id="74527-301">ここに示されている名前付け規則とテストスタイルは、テスト可能なコードには必要ありません。これは1つの方法にすぎません。</span><span class="sxs-lookup"><span data-stu-id="74527-301">The naming convention and test style presented here isn’t required for testable code – it’s just one approach.</span></span> <span data-ttu-id="74527-302">図4は、Visual Studio 2010 用の Jet 頭脳のテストランナープラグインで実行されるテストを示しています。</span><span class="sxs-lookup"><span data-stu-id="74527-302">Figure 4 shows the tests running in the Jet Brains Resharper test runner plugin for Visual Studio 2010.</span></span>

![ef test_04](~/ef6/media/eftest-04.png)

<span data-ttu-id="74527-304">**図 4**</span><span class="sxs-lookup"><span data-stu-id="74527-304">**Figure 4**</span></span>

<span data-ttu-id="74527-305">共有セットアップコードを処理する基底クラスを使用すると、各コントローラーアクションの単体テストは小さく、簡単に記述できます。</span><span class="sxs-lookup"><span data-stu-id="74527-305">With a base class to handle the shared setup code, the unit tests for each controller action are small and easy to write.</span></span> <span data-ttu-id="74527-306">テストは (メモリ内の操作を実行しているため) すぐに実行されます。また、関連のないインフラストラクチャや環境の問題 (テスト対象の単位が分離されているため) が原因で失敗することはありません。</span><span class="sxs-lookup"><span data-stu-id="74527-306">The tests will execute quickly (since we are performing in-memory operations), and shouldn’t fail because of unrelated infrastructure or environmental concerns (because we’ve isolated the unit under test).</span></span>

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

<span data-ttu-id="74527-307">これらのテストでは、基本クラスによってほとんどのセットアップ作業が実行されます。</span><span class="sxs-lookup"><span data-stu-id="74527-307">In these tests, the base class does most of the setup work.</span></span> <span data-ttu-id="74527-308">基底クラスのコンストラクターは、メモリ内のリポジトリ、仮の作業単位、および EmployeeController クラスのインスタンスを作成することに注意してください。</span><span class="sxs-lookup"><span data-stu-id="74527-308">Remember the base class constructor creates the in-memory repository, a fake unit of work, and an instance of the EmployeeController class.</span></span> <span data-ttu-id="74527-309">このテストクラスは、この基本クラスから派生し、Create メソッドのテストの詳細に焦点を当てています。</span><span class="sxs-lookup"><span data-stu-id="74527-309">The test class derives from this base class and focuses on the specifics of testing the Create method.</span></span> <span data-ttu-id="74527-310">この例では、単体テストの手順に表示される "整列、操作、およびアサート" の手順について詳しく説明しています。</span><span class="sxs-lookup"><span data-stu-id="74527-310">In this case the specifics boil down to the “arrange, act, and assert” steps you’ll see in any unit testing procedure:</span></span>

-   <span data-ttu-id="74527-311">受信データをシミュレートする newEmployee オブジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="74527-311">Create a newEmployee object to simulate incoming data.</span></span>
-   <span data-ttu-id="74527-312">EmployeeController の Create アクションを呼び出し、newEmployee を渡します。</span><span class="sxs-lookup"><span data-stu-id="74527-312">Invoke the Create action of the EmployeeController and pass in the newEmployee.</span></span>
-   <span data-ttu-id="74527-313">[作成] アクションによって、予想される結果が生成されることを確認します (従業員がリポジトリに表示されます)。</span><span class="sxs-lookup"><span data-stu-id="74527-313">Verify the Create action produces the expected results (the employee appears in the repository).</span></span>

<span data-ttu-id="74527-314">私たちが構築したことで、EmployeeController アクションをテストできます。</span><span class="sxs-lookup"><span data-stu-id="74527-314">What we’ve built allows us to test any of the EmployeeController actions.</span></span> <span data-ttu-id="74527-315">たとえば、Employee コントローラーの Index アクションのテストを作成するときに、テスト基本クラスから継承して、テストの基本設定を同じにすることができます。</span><span class="sxs-lookup"><span data-stu-id="74527-315">For example, when we write tests for the Index action of the Employee controller we can inherit from the test base class to establish the same base setup for our tests.</span></span> <span data-ttu-id="74527-316">ここでも、基底クラスによって、インメモリリポジトリ、仮の作業単位、および EmployeeController のインスタンスが作成されます。</span><span class="sxs-lookup"><span data-stu-id="74527-316">Again the base class will create the in-memory repository, the fake unit of work, and an instance of the EmployeeController.</span></span> <span data-ttu-id="74527-317">インデックスアクションのテストでは、インデックスアクションの呼び出しと、アクションによって返されるモデルの品質のテストにのみ焦点を当てる必要があります。</span><span class="sxs-lookup"><span data-stu-id="74527-317">The tests for the Index action only need to focus on invoking the Index action and testing the qualities of the model the action returns.</span></span>

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

<span data-ttu-id="74527-318">インメモリのフェイクで作成しているテストは、ソフトウェアの*状態*のテストに向いています。</span><span class="sxs-lookup"><span data-stu-id="74527-318">The tests we are creating with in-memory fakes are oriented towards testing the *state* of the software.</span></span> <span data-ttu-id="74527-319">たとえば、作成アクションをテストするときに、作成アクションの実行後にリポジトリの状態を検査する必要があります。リポジトリは新しい従業員を保持しますか。</span><span class="sxs-lookup"><span data-stu-id="74527-319">For example, when testing the Create action we want to inspect the state of the repository after the create action executes – does the repository hold the new employee?</span></span>

``` csharp
    [TestMethod]
    public void ShouldAddNewEmployeeToRepository() {
        _controller.Create(_newEmployee);
        Assert.IsTrue(_repository.Contains(_newEmployee));
    }
```

<span data-ttu-id="74527-320">後で、相互作用ベースのテストについて説明します。</span><span class="sxs-lookup"><span data-stu-id="74527-320">Later we’ll look at interaction based testing.</span></span> <span data-ttu-id="74527-321">相互作用ベースのテストでは、テスト対象のコードがオブジェクトで適切なメソッドを呼び出し、正しいパラメーターを渡されたかどうかを確認します。</span><span class="sxs-lookup"><span data-stu-id="74527-321">Interaction based testing will ask if the code under test invoked the proper methods on our objects and passed the correct parameters.</span></span> <span data-ttu-id="74527-322">ここでは、もう1つの設計パターン (遅延読み込み) について説明します。</span><span class="sxs-lookup"><span data-stu-id="74527-322">For now we’ll move on the cover another design pattern – the lazy load.</span></span>

## <a name="eager-loading-and-lazy-loading"></a><span data-ttu-id="74527-323">一括読み込みと遅延読み込み</span><span class="sxs-lookup"><span data-stu-id="74527-323">Eager Loading and Lazy Loading</span></span>

<span data-ttu-id="74527-324">ASP.NET MVC web アプリケーションのある時点で、従業員の情報を表示し、従業員に関連付けられたタイムカードを含めることをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="74527-324">At some point in the ASP.NET  MVC web application we might wish to display an employee’s information and include the employee’s associated time cards.</span></span> <span data-ttu-id="74527-325">たとえば、従業員の名前とシステム内のタイムカードの合計数を示すタイムカードの概要が表示される場合があります。</span><span class="sxs-lookup"><span data-stu-id="74527-325">For example, we might have a time card summary display that shows the employee’s name and the total number of time cards in the system.</span></span> <span data-ttu-id="74527-326">この機能を実装するには、いくつかの方法があります。</span><span class="sxs-lookup"><span data-stu-id="74527-326">There are several approaches we can take to implement this feature.</span></span>

### <a name="projection"></a><span data-ttu-id="74527-327">Projection</span><span class="sxs-lookup"><span data-stu-id="74527-327">Projection</span></span>

<span data-ttu-id="74527-328">概要を作成する簡単な方法の1つは、ビューに表示する情報専用のモデルを構築することです。</span><span class="sxs-lookup"><span data-stu-id="74527-328">One easy approach to create the summary is to construct a model dedicated to the information we want to display in the view.</span></span> <span data-ttu-id="74527-329">このシナリオでは、モデルは次のようになります。</span><span class="sxs-lookup"><span data-stu-id="74527-329">In this scenario the model might look like the following.</span></span>

``` csharp
    public class EmployeeSummaryViewModel {
        public string Name { get; set; }
        public int TotalTimeCards { get; set; }
    }
```

<span data-ttu-id="74527-330">EmployeeSummaryViewModel はエンティティではなく、データベースに保持するものではないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="74527-330">Note that the EmployeeSummaryViewModel is not an entity – in other words it is not something we want to persist in the database.</span></span> <span data-ttu-id="74527-331">このクラスは、厳密に型指定された方法でデータをビューにシャッフルする場合にのみ使用します。</span><span class="sxs-lookup"><span data-stu-id="74527-331">We are only going to use this class to shuffle data into the view in a strongly typed manner.</span></span> <span data-ttu-id="74527-332">ビューモデルはデータ転送オブジェクト (DTO) に似ています。これは、[動作なし] (メソッドなし) のプロパティのみが含まれているためです。</span><span class="sxs-lookup"><span data-stu-id="74527-332">The view model is like a data transfer object (DTO) because it contains no behavior (no methods) – only properties.</span></span> <span data-ttu-id="74527-333">プロパティは、移動する必要があるデータを保持します。</span><span class="sxs-lookup"><span data-stu-id="74527-333">The properties will hold the data we need to move.</span></span> <span data-ttu-id="74527-334">LINQ の標準プロジェクション演算子 (Select 演算子) を使用して、このビューモデルのインスタンス化を簡単に行うことができます。</span><span class="sxs-lookup"><span data-stu-id="74527-334">It is easy to instantiate this view model using LINQ’s standard projection operator – the Select operator.</span></span>

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

<span data-ttu-id="74527-335">上記のコードには、次の2つの注目すべき機能があります。</span><span class="sxs-lookup"><span data-stu-id="74527-335">There are two notable features to the above code.</span></span> <span data-ttu-id="74527-336">最初の方法では、コードを簡単に確認し、分離することができるため、コードを簡単にテストできます。</span><span class="sxs-lookup"><span data-stu-id="74527-336">First – the code is easy to test because it is still easy to observe and isolate.</span></span> <span data-ttu-id="74527-337">Select 演算子は、実際の作業単位に対して実行されるメモリ内のフェイクに対しても同様に機能します。</span><span class="sxs-lookup"><span data-stu-id="74527-337">The Select operator works just as well against our in-memory fakes as it does against the real unit of work.</span></span>

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

<span data-ttu-id="74527-338">2番目の注目すべき機能は、EF4 が1つの効率的なクエリを生成して従業員とタイムカード情報をまとめて組み立てることができるようにする方法です。</span><span class="sxs-lookup"><span data-stu-id="74527-338">The second notable feature is how the code allows EF4 to generate a single, efficient query to assemble employee and time card information together.</span></span> <span data-ttu-id="74527-339">特別な Api を使用せずに、従業員情報とタイムカード情報を同じオブジェクトに読み込んでいます。</span><span class="sxs-lookup"><span data-stu-id="74527-339">We’ve loaded employee information and time card information into the same object without using any special APIs.</span></span> <span data-ttu-id="74527-340">このコードでは、インメモリデータソースとリモートデータソースに対して機能する標準の LINQ 演算子を使用して、必要な情報だけを表現しています。</span><span class="sxs-lookup"><span data-stu-id="74527-340">The code merely expressed the information it requires using standard LINQ operators that work against in-memory data sources as well as remote data sources.</span></span> <span data-ttu-id="74527-341">EF4 は、LINQ クエリおよび C\# コンパイラによって生成された式ツリーを、効率的な単一の T-sql クエリに変換できました。</span><span class="sxs-lookup"><span data-stu-id="74527-341">EF4 was able to translate the expression trees generated by the LINQ query and C\# compiler into a single and efficient T-SQL query.</span></span>

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

<span data-ttu-id="74527-342">ビューモデルまたは DTO オブジェクトではなく、実際のエンティティを使用しない場合もあります。</span><span class="sxs-lookup"><span data-stu-id="74527-342">There are other times when we don’t want to work with a view model or DTO object, but with real entities.</span></span> <span data-ttu-id="74527-343">従業員*と*従業員のタイムカードが必要であることがわかったら、関連データを控えめで効率的な方法で集中的に読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="74527-343">When we know we need an employee *and* the employee’s time cards, we can eagerly load the related data in an unobtrusive and efficient manner.</span></span>

### <a name="explicit-eager-loading"></a><span data-ttu-id="74527-344">明示的な一括読み込み</span><span class="sxs-lookup"><span data-stu-id="74527-344">Explicit Eager Loading</span></span>

<span data-ttu-id="74527-345">関連エンティティ情報の読み込みを集中的する場合は、ビジネスロジック (またはこのシナリオではコントローラーアクションロジック) 用のメカニズムを使用して、リポジトリに対する希望を表現する必要があります。</span><span class="sxs-lookup"><span data-stu-id="74527-345">When we want to eagerly load related entity information we need some mechanism for business logic (or in this scenario, controller action logic) to express its desire to the repository.</span></span> <span data-ttu-id="74527-346">EF4 ObjectQuery&lt;T&gt; クラスは、クエリの実行中に取得する関連オブジェクトを指定する Include メソッドを定義します。</span><span class="sxs-lookup"><span data-stu-id="74527-346">The EF4 ObjectQuery&lt;T&gt; class defines an Include method to specify the related objects to retrieve during a query.</span></span> <span data-ttu-id="74527-347">EF4 ObjectContext は、ObjectQuery&lt;T&gt;から継承する具象 ObjectSet&lt;T&gt; クラスを介してエンティティを公開していることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="74527-347">Remember the EF4 ObjectContext exposes entities via the concrete ObjectSet&lt;T&gt; class which inherits from ObjectQuery&lt;T&gt;.</span></span><span data-ttu-id="74527-348">  ObjectSet&lt;&gt; T を使用している場合は、次のコードを記述して、各従業員のタイムカード情報の一括読み込みを指定することができます。</span><span class="sxs-lookup"><span data-stu-id="74527-348">  If we were using ObjectSet&lt;T&gt; references in our controller action we could write the following code to specify an eager load of time card information for each employee.</span></span>

``` csharp
    _employees.Include("TimeCards")
              .Where(e => e.HireDate.Year > 2009);
```

<span data-ttu-id="74527-349">ただし、コードをテストできるようにするために、実際の作業単位クラスの外部から ObjectSet&lt;T&gt; を公開しているわけではありません。</span><span class="sxs-lookup"><span data-stu-id="74527-349">However, since we are trying to keep our code testable we are not exposing ObjectSet&lt;T&gt; from outside the real unit of work class.</span></span> <span data-ttu-id="74527-350">代わりに、IObjectSet&lt;T&gt; インターフェイスを使用します。これは簡単に偽装できますが、IObjectSet&lt;T&gt; は Include メソッドを定義していません。</span><span class="sxs-lookup"><span data-stu-id="74527-350">Instead, we rely on the IObjectSet&lt;T&gt; interface which is easier to fake, but IObjectSet&lt;T&gt; does not define an Include method.</span></span> <span data-ttu-id="74527-351">LINQ の利点は、独自の Include 演算子を作成できることです。</span><span class="sxs-lookup"><span data-stu-id="74527-351">The beauty of LINQ is that we can create our own Include operator.</span></span>

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

<span data-ttu-id="74527-352">この Include 演算子は、IObjectSet&lt;T&gt;ではなく、IQueryable&lt;T&gt; の拡張メソッドとして定義されていることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="74527-352">Notice this Include operator is defined as an extension method for IQueryable&lt;T&gt; instead of IObjectSet&lt;T&gt;.</span></span> <span data-ttu-id="74527-353">これにより、IQueryable&lt;T&gt;、IObjectSet&lt;T&gt;、ObjectQuery&lt;T&gt;、ObjectSet&lt;T&gt;など、さまざまな種類の型でメソッドを使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="74527-353">This gives us the ability to use the method with a wider range of possible types, including IQueryable&lt;T&gt;, IObjectSet&lt;T&gt;, ObjectQuery&lt;T&gt;, and ObjectSet&lt;T&gt;.</span></span> <span data-ttu-id="74527-354">基になるシーケンスが正規の EF4 ObjectQuery&lt;T&gt;ではない場合、問題は発生せず、Include 操作は実行されません。</span><span class="sxs-lookup"><span data-stu-id="74527-354">In the event the underlying sequence is not a genuine EF4 ObjectQuery&lt;T&gt;, then there is no harm done and the Include operator is a no-op.</span></span> <span data-ttu-id="74527-355">基になるシーケンス*が*ObjectQuery&lt;t&gt; (または ObjectQuery&lt;t&gt;から派生) である場合、EF4 には追加データの要件が表示され、適切な SQL クエリが生成されます。</span><span class="sxs-lookup"><span data-stu-id="74527-355">If the underlying sequence *is* an ObjectQuery&lt;T&gt; (or derived from ObjectQuery&lt;T&gt;), then EF4 will see our requirement for additional data and formulate the proper SQL query.</span></span>

<span data-ttu-id="74527-356">この新しいオペレーターを使用して、リポジトリからのタイムカード情報の一括読み込みを明示的に要求できます。</span><span class="sxs-lookup"><span data-stu-id="74527-356">With this new operator in place we can explicitly request an eager load of time card information from the repository.</span></span>

``` csharp
    public ViewResult Index() {
        var model = _unitOfWork.Employees
                               .Include("TimeCards")
                               .OrderBy(e => e.HireDate);
        return View(model);
    }
```

<span data-ttu-id="74527-357">実際の ObjectContext に対して実行すると、コードは次の1つのクエリを生成します。</span><span class="sxs-lookup"><span data-stu-id="74527-357">When run against a real ObjectContext, the code produces the following single query.</span></span> <span data-ttu-id="74527-358">このクエリでは、従業員オブジェクトを具体化し、それらのタイムカードプロパティを完全に設定するために、データベースから十分な情報が1つのトリップで収集されます。</span><span class="sxs-lookup"><span data-stu-id="74527-358">The query gathers enough information from the database in one trip to materialize the employee objects and fully populate their TimeCards property.</span></span>

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

<span data-ttu-id="74527-359">優れたニュースは、アクションメソッド内のコードが完全にテスト可能な状態であることです。</span><span class="sxs-lookup"><span data-stu-id="74527-359">The great news is the code inside the action method remains fully testable.</span></span> <span data-ttu-id="74527-360">Include 演算子をサポートするために、フェイクの追加機能を提供する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="74527-360">We don’t need to provide any additional features for our fakes to support the Include operator.</span></span> <span data-ttu-id="74527-361">悪い知らせは、永続化を意識せずに、コード内で Include 演算子を使用する必要があるということです。</span><span class="sxs-lookup"><span data-stu-id="74527-361">The bad news is we had to use the Include operator inside of the code we wanted to keep persistence ignorant.</span></span> <span data-ttu-id="74527-362">これは、テスト可能なコードを作成するときに評価する必要があるトレードオフの例です。</span><span class="sxs-lookup"><span data-stu-id="74527-362">This is a prime example of the type of tradeoffs you’ll need to evaluate when building testable code.</span></span> <span data-ttu-id="74527-363">リポジトリの抽象化の外部で、パフォーマンスの目標を達成するために、永続化に関する問題が発生する場合があります。</span><span class="sxs-lookup"><span data-stu-id="74527-363">There are times when you need to let persistence concerns leak outside the repository abstraction to meet performance goals.</span></span>

<span data-ttu-id="74527-364">一括読み込みの代わりに、遅延読み込みがあります。</span><span class="sxs-lookup"><span data-stu-id="74527-364">The alternative to eager loading is lazy loading.</span></span> <span data-ttu-id="74527-365">遅延読み込みは、関連付けられたデータの要件を明示的に通知するためにビジネスコードを必要とし*ない*ことを意味します。</span><span class="sxs-lookup"><span data-stu-id="74527-365">Lazy loading means we do *not* need our business code to explicitly announce the requirement for associated data.</span></span> <span data-ttu-id="74527-366">代わりに、アプリケーションでエンティティを使用します。追加データが必要な場合は Entity Framework によってデータが要求時に読み込まれます。</span><span class="sxs-lookup"><span data-stu-id="74527-366">Instead, we use our entities in the application and if additional data is needed Entity Framework will load the data on demand.</span></span>

### <a name="lazy-loading"></a><span data-ttu-id="74527-367">遅延読み込み</span><span class="sxs-lookup"><span data-stu-id="74527-367">Lazy Loading</span></span>

<span data-ttu-id="74527-368">ビジネスロジックが必要とするデータがわからないシナリオを簡単に想像できます。</span><span class="sxs-lookup"><span data-stu-id="74527-368">It’s easy to imagine a scenario where we don’t know what data a piece of business logic will need.</span></span> <span data-ttu-id="74527-369">このロジックには employee オブジェクトが必要であることがわかっているかもしれませんが、これらのパスの中には従業員からのタイムカード情報を必要とするものもあれば、異なる実行パスに分岐する場合もあります。</span><span class="sxs-lookup"><span data-stu-id="74527-369">We might know the logic needs an employee object, but we may branch into different execution paths where some of those paths require time card information from the employee, and some do not.</span></span> <span data-ttu-id="74527-370">このようなシナリオは、データが必要に応じて表示されるため、暗黙的な遅延読み込みに最適です。</span><span class="sxs-lookup"><span data-stu-id="74527-370">Scenarios like this are perfect for implicit lazy loading because data magically appears on an as-needed basis.</span></span>

<span data-ttu-id="74527-371">遅延読み込みとも呼ばれる遅延読み込みでは、エンティティオブジェクトにいくつかの要件があります。</span><span class="sxs-lookup"><span data-stu-id="74527-371">Lazy loading, also known as deferred loading, does place some requirements on our entity objects.</span></span> <span data-ttu-id="74527-372">真の永続性無視を持つ POCOs は、永続化層の要件を満たしていませんが、実際の永続性無視は実際には実現できません。</span><span class="sxs-lookup"><span data-stu-id="74527-372">POCOs with true persistence ignorance would not face any requirements from the persistence layer, but true persistence ignorance is practically impossible to achieve.</span></span><span data-ttu-id="74527-373">  代わりに、永続性の無視を相対的度単位で測定します。</span><span class="sxs-lookup"><span data-stu-id="74527-373">  Instead we measure persistence ignorance in relative degrees.</span></span> <span data-ttu-id="74527-374">永続指向の基底クラスから継承する必要がある場合、または特殊なコレクションを使用して POCOs で遅延読み込みを行う必要がある場合は、不幸になります。</span><span class="sxs-lookup"><span data-stu-id="74527-374">It would be unfortunate if we needed to inherit from a persistence oriented base class or use a specialized collection to achieve lazy loading in POCOs.</span></span> <span data-ttu-id="74527-375">幸いなことに、EF4 には、あまり邪魔にならないソリューションがあります。</span><span class="sxs-lookup"><span data-stu-id="74527-375">Fortunately, EF4 has a less intrusive solution.</span></span>

### <a name="virtually-undetectable"></a><span data-ttu-id="74527-376">事実上検出不可能</span><span class="sxs-lookup"><span data-stu-id="74527-376">Virtually Undetectable</span></span>

<span data-ttu-id="74527-377">POCO オブジェクトを使用する場合、EF4 はエンティティのランタイムプロキシを動的に生成できます。</span><span class="sxs-lookup"><span data-stu-id="74527-377">When using POCO objects, EF4 can dynamically generate runtime proxies for entities.</span></span> <span data-ttu-id="74527-378">これらのプロキシは、具体化された POCOs を非表示にして、追加の作業を実行するために各プロパティの get および set 操作をインターセプトすることによって追加のサービスを提供します</span><span class="sxs-lookup"><span data-stu-id="74527-378">These proxies invisibly wrap the materialized POCOs and provide additional services by intercepting each property get and set operation to perform additional work.</span></span> <span data-ttu-id="74527-379">このようなサービスの1つは、お探しの遅延読み込み機能です。</span><span class="sxs-lookup"><span data-stu-id="74527-379">One such service is the lazy loading feature we are looking for.</span></span> <span data-ttu-id="74527-380">別のサービスは、プログラムがエンティティのプロパティ値を変更したときに記録できる効率的な変更追跡メカニズムです。</span><span class="sxs-lookup"><span data-stu-id="74527-380">Another service is an efficient change tracking mechanism which can record when the program changes the property values of an entity.</span></span> <span data-ttu-id="74527-381">変更の一覧は、SaveChanges メソッドで、更新コマンドを使用して変更されたエンティティを永続化するために使用されます。</span><span class="sxs-lookup"><span data-stu-id="74527-381">The list of changes is used by the ObjectContext during the SaveChanges method to persist any modified entities using UPDATE commands.</span></span>

<span data-ttu-id="74527-382">ただし、これらのプロキシを機能させるには、エンティティに対してプロパティの get 操作と set 操作をフックする方法が必要です。プロキシは、仮想メンバーをオーバーライドすることによってこの目標を達成します。</span><span class="sxs-lookup"><span data-stu-id="74527-382">For these proxies to work, however, they need a way to hook into property get and set operations on an entity, and the proxies achieve this goal by overriding virtual members.</span></span> <span data-ttu-id="74527-383">したがって、暗黙的な遅延読み込みと効率的な変更の追跡を行う場合は、POCO クラス定義に戻り、プロパティを virtual としてマークする必要があります。</span><span class="sxs-lookup"><span data-stu-id="74527-383">Thus, if we want to have implicit lazy loading and efficient change tracking we need to go back to our POCO class definitions and mark properties as virtual.</span></span>

``` csharp
    public class Employee {
        public virtual int Id { get; set; }
        public virtual string Name { get; set; }
        public virtual DateTime HireDate { get; set; }
        public virtual ICollection<TimeCard> TimeCards { get; set; }
    }
```

<span data-ttu-id="74527-384">Employee エンティティは、ほとんどの場合、永続化に依存しないと言ってもかまいません。</span><span class="sxs-lookup"><span data-stu-id="74527-384">We can still say the Employee entity is mostly persistence ignorant.</span></span> <span data-ttu-id="74527-385">唯一の要件は、仮想メンバーを使用することです。これは、コードのテストの容易性には影響しません。</span><span class="sxs-lookup"><span data-stu-id="74527-385">The only requirement is to use virtual members and this does not impact the testability of the code.</span></span> <span data-ttu-id="74527-386">特別な基底クラスから派生する必要はなく、遅延読み込み専用の特別なコレクションを使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="74527-386">We don’t need to derive from any special base class, or even use a special collection dedicated to lazy loading.</span></span> <span data-ttu-id="74527-387">コードに示すように、ICollection&lt;T&gt; を実装するクラスは、関連エンティティを保持するために使用できます。</span><span class="sxs-lookup"><span data-stu-id="74527-387">As the code demonstrates, any class implementing ICollection&lt;T&gt; is available to hold related entities.</span></span>

<span data-ttu-id="74527-388">また、作業単位内で行う必要がある軽微な変更も1つあります。</span><span class="sxs-lookup"><span data-stu-id="74527-388">There is also one minor change we need to make inside our unit of work.</span></span> <span data-ttu-id="74527-389">既定では、ObjectContext オブジェクトを直接操作する場合、遅延読み込みは*無効*になっています。</span><span class="sxs-lookup"><span data-stu-id="74527-389">Lazy loading is *off* by default when working directly with an ObjectContext object.</span></span> <span data-ttu-id="74527-390">遅延読み込みを有効にするために ContextOptions プロパティに設定できるプロパティがあります。このプロパティは、どこでも遅延読み込みを有効にする必要がある場合に、実際の作業単位内で設定できます。</span><span class="sxs-lookup"><span data-stu-id="74527-390">There is a property we can set on the ContextOptions property to enable deferred loading, and we can set this property inside our real unit of work if we want to enable lazy loading everywhere.</span></span>

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

<span data-ttu-id="74527-391">暗黙の遅延読み込みが有効になっている場合、アプリケーションコードは従業員と従業員の関連付けられたタイムカードを使用できますが、EF が余分なデータを読み込むために必要な作業を意識意識する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="74527-391">With implicit lazy loading enabled, application code can use an employee and the employee’s associated time cards while remaining blissfully unaware of the work required for EF to load the extra data.</span></span>

``` csharp
    var employee = _unitOfWork.Employees
                              .Single(e => e.Id == id);
    foreach (var card in employee.TimeCards) {
        // ...
    }
```

<span data-ttu-id="74527-392">遅延読み込みでは、アプリケーションコードの記述が容易になります。また、プロキシマジックを使用すると、コードは完全にテスト可能な状態のままになります。</span><span class="sxs-lookup"><span data-stu-id="74527-392">Lazy loading makes the application code easier to write, and with the proxy magic the code remains completely testable.</span></span> <span data-ttu-id="74527-393">作業単位のメモリ内フェイクでは、テスト中に必要に応じて、関連するデータを使用して偽エンティティを事前に読み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="74527-393">In-memory fakes of the unit of work can simply preload fake entities with associated data when needed during a test.</span></span>

<span data-ttu-id="74527-394">この時点で、IObjectSet&lt;T&gt; を使用してリポジトリを構築し、抽象化を調べて永続化フレームワークのすべての署名を非表示にします。</span><span class="sxs-lookup"><span data-stu-id="74527-394">At this point we’ll turn our attention from building repositories using IObjectSet&lt;T&gt; and look at abstractions to hide all signs of the persistence framework.</span></span>

## <a name="custom-repositories"></a><span data-ttu-id="74527-395">カスタムリポジトリ</span><span class="sxs-lookup"><span data-stu-id="74527-395">Custom Repositories</span></span>

<span data-ttu-id="74527-396">この記事では、最初に作業単位のデザインパターンを提示したときに、作業単位がどのようなものかを示すサンプルコードを提供しました。</span><span class="sxs-lookup"><span data-stu-id="74527-396">When we first presented the unit of work design pattern in this article we provided some sample code for what the unit of work might look like.</span></span> <span data-ttu-id="74527-397">作業中の従業員と従業員のタイムカードシナリオを使用して、この元のアイデアを再提示してみましょう。</span><span class="sxs-lookup"><span data-stu-id="74527-397">Let’s re-present this original idea using the employee and employee time card scenario we’ve been working with.</span></span>

``` csharp
    public interface IUnitOfWork {
        IRepository<Employee> Employees { get; }
        IRepository<TimeCard> TimeCards { get;  }
        void Commit();
    }
```

<span data-ttu-id="74527-398">この作業単位と最後のセクションで作成した作業単位の主な違いは、この作業単位が EF4 framework の抽象化を使用しないことです (IObjectSet&lt;T&gt;はありません)。</span><span class="sxs-lookup"><span data-stu-id="74527-398">The primary difference between this unit of work and the unit of work we created in the last section is how this unit of work does not use any abstractions from the EF4 framework (there is no IObjectSet&lt;T&gt;).</span></span> <span data-ttu-id="74527-399">IObjectSet&lt;T&gt; はリポジトリインターフェイスとして適切に機能しますが、公開される API はアプリケーションのニーズと完全に一致しない可能性があります。</span><span class="sxs-lookup"><span data-stu-id="74527-399">IObjectSet&lt;T&gt; works well as a repository interface, but the API it exposes might not perfectly align with our application’s needs.</span></span> <span data-ttu-id="74527-400">この今後のアプローチでは、カスタム IRepository&lt;T&gt; 抽象化を使用してリポジトリを表現します。</span><span class="sxs-lookup"><span data-stu-id="74527-400">In this upcoming approach we will represent repositories using a custom IRepository&lt;T&gt; abstraction.</span></span>

<span data-ttu-id="74527-401">テスト駆動設計、行動駆動設計、およびドメイン主導型の手法の設計に従う多くの開発者は、いくつかの理由により、IRepository&lt;T&gt; アプローチを採用しています。</span><span class="sxs-lookup"><span data-stu-id="74527-401">Many developers who follow test-driven design, behavior-driven design, and domain driven methodologies design prefer the IRepository&lt;T&gt; approach for several reasons.</span></span> <span data-ttu-id="74527-402">まず、IRepository&lt;T&gt; インターフェイスは "破損の防止" レイヤーを表します。</span><span class="sxs-lookup"><span data-stu-id="74527-402">First, the IRepository&lt;T&gt; interface represents an “anti-corruption” layer.</span></span> <span data-ttu-id="74527-403">ドメイン駆動設計ブックの Eric Evans の説明に従って、破損対策レイヤーは、永続化 API などのインフラストラクチャ Api からドメインコードを分離します。</span><span class="sxs-lookup"><span data-stu-id="74527-403">As described by Eric Evans in his Domain Driven Design book an anti-corruption layer keeps your domain code away from infrastructure APIs, like a persistence API.</span></span> <span data-ttu-id="74527-404">2つ目の方法として、開発者は、(テストの作成時に検出された) アプリケーションの正確なニーズを満たすメソッドをリポジトリに組み込むことができます。</span><span class="sxs-lookup"><span data-stu-id="74527-404">Secondly, developers can build methods into the repository that meet the exact needs of an application (as discovered while writing tests).</span></span> <span data-ttu-id="74527-405">たとえば、多くの場合、ID 値を使用して1つのエンティティを検索する必要があるため、FindById メソッドをリポジトリインターフェイスに追加できます。</span><span class="sxs-lookup"><span data-stu-id="74527-405">For example, we might frequently need to locate a single entity using an ID value, so we can add a FindById method to the repository interface.</span></span><span data-ttu-id="74527-406">  IRepository&lt;T&gt; 定義は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="74527-406">  Our IRepository&lt;T&gt; definition will look like the following.</span></span>

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

<span data-ttu-id="74527-407">ここでは、IQueryable&lt;T&gt; インターフェイスを使用してエンティティコレクションを公開する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="74527-407">Notice we’ll drop back to using an IQueryable&lt;T&gt; interface to expose entity collections.</span></span> <span data-ttu-id="74527-408">IQueryable&lt;T&gt; を使用すると、LINQ 式ツリーを EF4 プロバイダーにフローし、プロバイダーにクエリの全体像を与えることができます。</span><span class="sxs-lookup"><span data-stu-id="74527-408">IQueryable&lt;T&gt; allows LINQ expression trees to flow into the EF4 provider and give the provider a holistic view of the query.</span></span> <span data-ttu-id="74527-409">2つ目の方法は、IEnumerable&lt;T&gt;を返すことです。これは、EF4 LINQ プロバイダーによって、リポジトリ内に構築された式のみが表示されることを意味します。</span><span class="sxs-lookup"><span data-stu-id="74527-409">A second option would be to return IEnumerable&lt;T&gt;, which means the EF4 LINQ provider will only see the expressions built inside of the repository.</span></span> <span data-ttu-id="74527-410">リポジトリの外部で行われたグループ化、順序付け、および射影は、データベースに送信される SQL コマンドには構成されないため、パフォーマンスが低下する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="74527-410">Any grouping, ordering, and projection done outside of the repository will not be composed into the SQL command sent to the database, which can hurt performance.</span></span> <span data-ttu-id="74527-411">一方、リポジトリは IEnumerable&lt;&gt; T だけを返すので、新しい SQL コマンドを使用しても驚くことはありません。</span><span class="sxs-lookup"><span data-stu-id="74527-411">On the other hand, a repository returning only IEnumerable&lt;T&gt; results will never surprise you with a new SQL command.</span></span> <span data-ttu-id="74527-412">どちらの方法も機能し、どちらの方法もテスト可能な状態を維持します。</span><span class="sxs-lookup"><span data-stu-id="74527-412">Both approaches will work, and both approaches remain testable.</span></span>

<span data-ttu-id="74527-413">ジェネリックと EF4 ObjectContext API を使用して、IRepository&lt;T&gt; インターフェイスの単一の実装を簡単に提供できます。</span><span class="sxs-lookup"><span data-stu-id="74527-413">It’s straightforward to provide a single implementation of the IRepository&lt;T&gt; interface using generics and the EF4 ObjectContext API.</span></span>

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

<span data-ttu-id="74527-414">IRepository&lt;T&gt; アプローチでは、クライアントがエンティティにアクセスするためにメソッドを呼び出す必要があるため、クエリをさらに制御できます。</span><span class="sxs-lookup"><span data-stu-id="74527-414">The IRepository&lt;T&gt; approach gives us some additional control over our queries because a client has to invoke a method to get to an entity.</span></span> <span data-ttu-id="74527-415">メソッド内では、アプリケーションの制約を適用するための追加チェックと LINQ 演算子を提供できます。</span><span class="sxs-lookup"><span data-stu-id="74527-415">Inside the method we could provide additional checks and LINQ operators to enforce application constraints.</span></span> <span data-ttu-id="74527-416">インターフェイスには、ジェネリック型パラメーターに対する2つの制約があることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="74527-416">Notice the interface has two constraints on the generic type parameter.</span></span> <span data-ttu-id="74527-417">1つ目の制約は、ObjectSet&lt;T&gt;によって要求される及びクラスであり、2つ目の制約は、エンティティに IEntity (アプリケーション用に作成された抽象化) を実装するように強制します。</span><span class="sxs-lookup"><span data-stu-id="74527-417">The first constraint is the class cons taint required by ObjectSet&lt;T&gt;, and the second constraint forces our entities to implement IEntity – an abstraction created for the application.</span></span> <span data-ttu-id="74527-418">IEntity インターフェイスでは、エンティティに読み取り可能な Id プロパティが強制的に設定されます。その後、FindById メソッドでこのプロパティを使用できます。</span><span class="sxs-lookup"><span data-stu-id="74527-418">The IEntity interface forces entities to have a readable Id property, and we can then use this property in the FindById method.</span></span> <span data-ttu-id="74527-419">IEntity は、次のコードで定義されています。</span><span class="sxs-lookup"><span data-stu-id="74527-419">IEntity is defined with the following code.</span></span>

``` csharp
    public interface IEntity {
        int Id { get; }
    }
```

<span data-ttu-id="74527-420">IEntity は、このインターフェイスを実装するためにエンティティが必要であるため、永続性の無視の小さな違反と見なすことができます。</span><span class="sxs-lookup"><span data-stu-id="74527-420">IEntity could be considered a small violation of persistence ignorance since our entities are required to implement this interface.</span></span> <span data-ttu-id="74527-421">永続化の無視はトレードオフに関するものです。多くの場合、FindById の機能は、インターフェイスによって課される制約を上回ることになります。</span><span class="sxs-lookup"><span data-stu-id="74527-421">Remember persistence ignorance is about tradeoffs, and for many the FindById functionality will outweigh the constraint imposed by the interface.</span></span> <span data-ttu-id="74527-422">インターフェイスは、テストの容易性には影響しません。</span><span class="sxs-lookup"><span data-stu-id="74527-422">The interface has no impact on testability.</span></span>

<span data-ttu-id="74527-423">ライブ IRepository&lt;T&gt; をインスタンス化するには EF4 ObjectContext が必要であるため、具体的な作業単位の実装ではインスタンス化を管理する必要があります。</span><span class="sxs-lookup"><span data-stu-id="74527-423">Instantiating a live IRepository&lt;T&gt; requires an EF4 ObjectContext, so a concrete unit of work implementation should manage the instantiation.</span></span>

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

### <a name="using-the-custom-repository"></a><span data-ttu-id="74527-424">カスタムリポジトリの使用</span><span class="sxs-lookup"><span data-stu-id="74527-424">Using the Custom Repository</span></span>

<span data-ttu-id="74527-425">カスタムリポジトリの使用は、IObjectSet&lt;T&gt;に基づくリポジトリの使用とは大きく異なります。</span><span class="sxs-lookup"><span data-stu-id="74527-425">Using our custom repository is not significantly different from using the repository based on IObjectSet&lt;T&gt;.</span></span> <span data-ttu-id="74527-426">LINQ 演算子を直接プロパティに適用するのではなく、まず、リポジトリのメソッドを呼び出して、IQueryable&lt;T&gt; 参照を取得する必要があります。</span><span class="sxs-lookup"><span data-stu-id="74527-426">Instead of applying LINQ operators directly to a property, we’ll first need to invoke one the repository’s methods to grab an IQueryable&lt;T&gt; reference.</span></span>

``` csharp
    public ViewResult Index() {
        var model = _repository.FindAll()
                               .Include("TimeCards")
                               .OrderBy(e => e.HireDate);
        return View(model);
    }
```

<span data-ttu-id="74527-427">前に実装したカスタムの Include 演算子が変更なしで動作することに注意してください。</span><span class="sxs-lookup"><span data-stu-id="74527-427">Notice the custom Include operator we implemented previously will work without change.</span></span> <span data-ttu-id="74527-428">リポジトリの FindById メソッドは、1つのエンティティを取得しようとしているアクションから重複したロジックを削除します。</span><span class="sxs-lookup"><span data-stu-id="74527-428">The repository’s FindById method removes duplicated logic from actions trying to retrieve a single entity.</span></span>

``` csharp
    public ViewResult Details(int id) {
        var model = _repository.FindById(id);
        return View(model);
    }
```

<span data-ttu-id="74527-429">調査した2つのアプローチのテストの容易性に大きな違いはありません。</span><span class="sxs-lookup"><span data-stu-id="74527-429">There is no significant difference in the testability of the two approaches we’ve examined.</span></span> <span data-ttu-id="74527-430">前のセクションで行ったのと同じように、HashSet&lt;Employee&gt; によって支えられた具象クラスを構築することにより、IRepository&lt;T&gt; の偽の実装を提供できます。</span><span class="sxs-lookup"><span data-stu-id="74527-430">We could provide fake implementations of IRepository&lt;T&gt; by building concrete classes backed by HashSet&lt;Employee&gt; - just like what we did in the last section.</span></span> <span data-ttu-id="74527-431">ただし、一部の開発者は、フェイクを構築するのではなく、モックオブジェクトとモックオブジェクトフレームワークを使用することを希望しています。</span><span class="sxs-lookup"><span data-stu-id="74527-431">However, some developers prefer to use mock objects and mock object frameworks instead of building fakes.</span></span> <span data-ttu-id="74527-432">モックを使用して実装をテストし、次のセクションでモックとフェイクの違いについて説明します。</span><span class="sxs-lookup"><span data-stu-id="74527-432">We’ll look at using mocks to test our implementation and discuss the differences between mocks and fakes in the next section.</span></span>

### <a name="testing-with-mocks"></a><span data-ttu-id="74527-433">モックを使用したテスト</span><span class="sxs-lookup"><span data-stu-id="74527-433">Testing with Mocks</span></span>

<span data-ttu-id="74527-434">Martin Fowler が "test double" を呼び出す対象を構築するには、さまざまな方法があります。</span><span class="sxs-lookup"><span data-stu-id="74527-434">There are different approaches to building what Martin Fowler calls a “test double”.</span></span> <span data-ttu-id="74527-435">テスト用の double (movie 代役 double など) は、テスト中に実際の実稼働オブジェクト用に "スタンドイン" するために構築するオブジェクトです。</span><span class="sxs-lookup"><span data-stu-id="74527-435">A test double (like a movie stunt double) is an object you build to “stand in” for real, production objects during tests.</span></span> <span data-ttu-id="74527-436">作成したインメモリリポジトリは、SQL Server と通信するリポジトリのテスト double です。</span><span class="sxs-lookup"><span data-stu-id="74527-436">The in-memory repositories we created are test doubles for the repositories that talk to SQL Server.</span></span> <span data-ttu-id="74527-437">単体テストでこれらのテストの double を使用して、コードを分離し、テストを高速に実行する方法について説明しました。</span><span class="sxs-lookup"><span data-stu-id="74527-437">We’ve seen how to use these test-doubles during the unit tests to isolate code and keep tests running fast.</span></span>

<span data-ttu-id="74527-438">ビルドしたテストの倍精度浮動小数点数の実装は、実際に動作しています。</span><span class="sxs-lookup"><span data-stu-id="74527-438">The test doubles we’ve built have real, working implementations.</span></span> <span data-ttu-id="74527-439">各シーンの背後では、オブジェクトの具象コレクションが格納され、テスト中にリポジトリを操作するときに、このコレクションのオブジェクトが追加および削除されます。</span><span class="sxs-lookup"><span data-stu-id="74527-439">Behind the scenes each one stores a concrete collection of objects, and they will add and remove objects from this collection as we manipulate the repository during a test.</span></span> <span data-ttu-id="74527-440">実際のコードと実用的な実装を使用して、この方法でテストを作成する開発者もいます。</span><span class="sxs-lookup"><span data-stu-id="74527-440">Some developers like to build their test doubles this way – with real code and working implementations.</span></span><span data-ttu-id="74527-441">  これらのテスト2つは、*フェイク*と呼ばれるものです。</span><span class="sxs-lookup"><span data-stu-id="74527-441">  These test doubles are what we call *fakes*.</span></span> <span data-ttu-id="74527-442">実装は動作していますが、実稼働環境での使用には十分ではありません。</span><span class="sxs-lookup"><span data-stu-id="74527-442">They have working implementations, but they aren’t real enough for production use.</span></span> <span data-ttu-id="74527-443">仮のリポジトリは実際にはデータベースに書き込みを行いません。</span><span class="sxs-lookup"><span data-stu-id="74527-443">The fake repository doesn’t actually write to the database.</span></span> <span data-ttu-id="74527-444">偽の SMTP サーバーは、実際にはネットワーク経由で電子メールメッセージを送信しません。</span><span class="sxs-lookup"><span data-stu-id="74527-444">The fake SMTP server doesn’t actually send an email message over the network.</span></span>

### <a name="mocks-versus-fakes"></a><span data-ttu-id="74527-445">モックとフェイク</span><span class="sxs-lookup"><span data-stu-id="74527-445">Mocks versus Fakes</span></span>

<span data-ttu-id="74527-446">*モック*と呼ばれる別の種類のテスト double があります。</span><span class="sxs-lookup"><span data-stu-id="74527-446">There is another type of test double known as a *mock*.</span></span> <span data-ttu-id="74527-447">フェイクの実装は正常に実行されていますが、モックには実装がありません。</span><span class="sxs-lookup"><span data-stu-id="74527-447">While fakes have working implementations, mocks come with no implementation.</span></span> <span data-ttu-id="74527-448">モックオブジェクトフレームワークを使用すると、これらのモックオブジェクトを実行時に構築し、テスト代替として使用できます。</span><span class="sxs-lookup"><span data-stu-id="74527-448">With the help of a mock object framework we construct these mock objects at run time and use them as test doubles.</span></span> <span data-ttu-id="74527-449">このセクションでは、オープンソースのモックフレームワーク Moq を使用します。</span><span class="sxs-lookup"><span data-stu-id="74527-449">In this section we’ll be using the open source mocking framework Moq.</span></span> <span data-ttu-id="74527-450">次に、Moq を使用して、従業員リポジトリのテスト double を動的に作成する簡単な例を示します。</span><span class="sxs-lookup"><span data-stu-id="74527-450">Here is a simple example of using Moq to dynamically create a test double for an employee repository.</span></span>

``` csharp
    Mock<IRepository<Employee>> mock =
        new Mock<IRepository<Employee>>();
    IRepository<Employee> repository = mock.Object;
    repository.Add(new Employee());
    var employee = repository.FindById(1);
```

<span data-ttu-id="74527-451">IRepository に対して Moq に&lt;従業員&gt; の実装を要求し、それを動的に構築します。</span><span class="sxs-lookup"><span data-stu-id="74527-451">We ask Moq for an IRepository&lt;Employee&gt; implementation and it builds one dynamically.</span></span> <span data-ttu-id="74527-452">モック&lt;T&gt; オブジェクトのオブジェクトプロパティにアクセスすることによって、IRepository&lt;Employee&gt; を実装するオブジェクトを取得できます。</span><span class="sxs-lookup"><span data-stu-id="74527-452">We can get to the object implementing IRepository&lt;Employee&gt; by accessing the Object property of the Mock&lt;T&gt; object.</span></span> <span data-ttu-id="74527-453">これは、コントローラーに渡すことができる内部オブジェクトであり、テスト用の double または実際のリポジトリであるかどうかはわかりません。</span><span class="sxs-lookup"><span data-stu-id="74527-453">It is this inner object we can pass into our controllers, and they won’t know if this is a test double or the real repository.</span></span> <span data-ttu-id="74527-454">実際の実装でオブジェクトに対してメソッドを呼び出すのと同じように、オブジェクトでメソッドを呼び出すことができます。</span><span class="sxs-lookup"><span data-stu-id="74527-454">We can invoke methods on the object just like we would invoke methods on an object with a real implementation.</span></span>

<span data-ttu-id="74527-455">Add メソッドを呼び出すと、モックリポジトリによって実行される内容を確認する必要があります。</span><span class="sxs-lookup"><span data-stu-id="74527-455">You must be wondering what the mock repository will do when we invoke the Add method.</span></span> <span data-ttu-id="74527-456">モックオブジェクトの背後に実装が存在しないため、Add は何も行いません。</span><span class="sxs-lookup"><span data-stu-id="74527-456">Since there is no implementation behind the mock object, Add does nothing.</span></span> <span data-ttu-id="74527-457">作成したフェイクとは別の具象コレクションではなく、従業員は破棄されます。</span><span class="sxs-lookup"><span data-stu-id="74527-457">There is no concrete collection behind the scenes like we had with the fakes we wrote, so the employee is discarded.</span></span> <span data-ttu-id="74527-458">FindById の戻り値について</span><span class="sxs-lookup"><span data-stu-id="74527-458">What about the return value of FindById?</span></span> <span data-ttu-id="74527-459">この場合、モックオブジェクトは、既定値を返すことができる処理だけを行います。</span><span class="sxs-lookup"><span data-stu-id="74527-459">In this case the mock object does the only thing it can do, which is return a default value.</span></span> <span data-ttu-id="74527-460">参照型 (Employee) を返すため、戻り値は null 値になります。</span><span class="sxs-lookup"><span data-stu-id="74527-460">Since we are returning a reference type (an Employee), the return value is a null value.</span></span>

<span data-ttu-id="74527-461">モックは、意味がない可能性があります。ただし、まだ説明していないモックには2つの機能があります。</span><span class="sxs-lookup"><span data-stu-id="74527-461">Mocks might sound worthless; however, there are two more features of mocks we haven’t talked about.</span></span> <span data-ttu-id="74527-462">まず、Moq フレームワークは、モックオブジェクトに対して行われたすべての呼び出しを記録します。</span><span class="sxs-lookup"><span data-stu-id="74527-462">First, the Moq framework records all the calls made on the mock object.</span></span> <span data-ttu-id="74527-463">コードの後半で、Add メソッドを呼び出した場合、またはだれかが FindById メソッドを呼び出した場合は、Moq に要求できます。</span><span class="sxs-lookup"><span data-stu-id="74527-463">Later in the code we can ask Moq if anyone invoked the Add method, or if anyone invoked the FindById method.</span></span> <span data-ttu-id="74527-464">この "ブラックボックス" の記録機能をテストで使用する方法については、後で説明します。</span><span class="sxs-lookup"><span data-stu-id="74527-464">We’ll see later how we can use this “black box” recording feature in tests.</span></span>

<span data-ttu-id="74527-465">2つ目の優れた機能は、Moq を使用して、*予測*されるモックオブジェクトをプログラミングする方法です。</span><span class="sxs-lookup"><span data-stu-id="74527-465">The second great feature is how we can use Moq to program a mock object with *expectations*.</span></span> <span data-ttu-id="74527-466">予測は、特定の相互作用に対する応答方法をモックオブジェクトに伝えます。</span><span class="sxs-lookup"><span data-stu-id="74527-466">An expectation tells the mock object how to respond to any given interaction.</span></span> <span data-ttu-id="74527-467">たとえば、モックに対する予測をプログラミングして、だれかが FindById を呼び出したときに employee オブジェクトを返すように指示することができます。</span><span class="sxs-lookup"><span data-stu-id="74527-467">For example, we can program an expectation into our mock and tell it to return an employee object when someone invokes FindById.</span></span> <span data-ttu-id="74527-468">Moq フレームワークは、セットアップ API とラムダ式を使用して、これらの予測をプログラミングします。</span><span class="sxs-lookup"><span data-stu-id="74527-468">The Moq framework uses a Setup API and lambda expressions to program these expectations.</span></span>

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

<span data-ttu-id="74527-469">このサンプルでは、Moq にリポジトリを動的に構築するように指示し、その後、予測を使用してリポジトリをプログラミングします。</span><span class="sxs-lookup"><span data-stu-id="74527-469">In this sample we ask Moq to dynamically build a repository, and then we program the repository with an expectation.</span></span> <span data-ttu-id="74527-470">この予測では、FindById メソッドを呼び出したときに5の値が渡されたときに、モックオブジェクトが Id 値5の新しい employee オブジェクトを返すように指示します。</span><span class="sxs-lookup"><span data-stu-id="74527-470">The expectation tells the mock object to return a new employee object with an Id value of 5 when someone invokes the FindById method passing a value of 5.</span></span> <span data-ttu-id="74527-471">このテストは成功するため、完全な実装を作成する必要はありませんでした。&lt;T&gt;をフェイクにします。</span><span class="sxs-lookup"><span data-stu-id="74527-471">This test passes, and we didn’t need to build a full implementation to fake IRepository&lt;T&gt;.</span></span>

<span data-ttu-id="74527-472">先ほど作成したテストにもう一度戻って、フェイクではなくモックを使用するように書き直してみましょう。</span><span class="sxs-lookup"><span data-stu-id="74527-472">Let’s revisit the tests we wrote earlier and rework them to use mocks instead of fakes.</span></span> <span data-ttu-id="74527-473">前と同様に、基本クラスを使用して、コントローラーのすべてのテストに必要なインフラストラクチャの共通部分を設定します。</span><span class="sxs-lookup"><span data-stu-id="74527-473">Just like before, we’ll use a base class to setup the common pieces of infrastructure we need for all of the controller’s tests.</span></span>

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

<span data-ttu-id="74527-474">セットアップコードはほとんど同じです。</span><span class="sxs-lookup"><span data-stu-id="74527-474">The setup code remains mostly the same.</span></span> <span data-ttu-id="74527-475">フェイクを使用する代わりに、Moq を使用してモックオブジェクトを構築します。</span><span class="sxs-lookup"><span data-stu-id="74527-475">Instead of using fakes, we’ll use Moq to construct mock objects.</span></span> <span data-ttu-id="74527-476">基本クラスは、コードが Employees プロパティを呼び出すときに、モックリポジトリを返すようにモックの作業単位を配置します。</span><span class="sxs-lookup"><span data-stu-id="74527-476">The base class arranges for the mock unit of work to return a mock repository when code invokes the Employees property.</span></span> <span data-ttu-id="74527-477">モックセットアップの残りの部分は、特定のシナリオごとに専用のテストフィクスチャ内で実行されます。</span><span class="sxs-lookup"><span data-stu-id="74527-477">The rest of the mock setup will take place inside the test fixtures dedicated to each specific scenario.</span></span> <span data-ttu-id="74527-478">たとえば、Index アクションのテストフィクスチャは、アクションがモックリポジトリの FindAll メソッドを呼び出したときに、従業員の一覧を返すようにモックリポジトリを設定します。</span><span class="sxs-lookup"><span data-stu-id="74527-478">For example, the test fixture for the Index action will setup the mock repository to return a list of employees when the action invokes the FindAll method of the mock repository.</span></span>

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

<span data-ttu-id="74527-479">期待を除けば、テストは前に行ったテストに似ています。</span><span class="sxs-lookup"><span data-stu-id="74527-479">Except for the expectations, our tests look similar to the tests we had before.</span></span> <span data-ttu-id="74527-480">ただし、モックフレームワークの記録機能を使用すると、さまざまな角度からテストにアプローチできます。</span><span class="sxs-lookup"><span data-stu-id="74527-480">However, with the recording ability of a mock framework we can approach testing from a different angle.</span></span> <span data-ttu-id="74527-481">次のセクションでは、この新しいパースペクティブについて説明します。</span><span class="sxs-lookup"><span data-stu-id="74527-481">We’ll look at this new perspective in the next section.</span></span>

### <a name="state-versus-interaction-testing"></a><span data-ttu-id="74527-482">状態と相互作用テスト</span><span class="sxs-lookup"><span data-stu-id="74527-482">State versus Interaction Testing</span></span>

<span data-ttu-id="74527-483">モックオブジェクトを使用してソフトウェアをテストするには、さまざまな方法があります。</span><span class="sxs-lookup"><span data-stu-id="74527-483">There are different techniques you can use to test software with mock objects.</span></span> <span data-ttu-id="74527-484">1つの方法は、これまでこのホワイトペーパーで行った状態ベースのテストを使用することです。</span><span class="sxs-lookup"><span data-stu-id="74527-484">One approach is to use state based testing, which is what we have done in this paper so far.</span></span> <span data-ttu-id="74527-485">状態ベースのテストによって、ソフトウェアの状態に関するアサーションが作成されます。</span><span class="sxs-lookup"><span data-stu-id="74527-485">State based testing makes assertions about the state of the software.</span></span> <span data-ttu-id="74527-486">前回のテストでは、コントローラーでアクションメソッドを呼び出し、ビルドする必要のあるモデルに関するアサーションを作成しました。</span><span class="sxs-lookup"><span data-stu-id="74527-486">In the last test we invoked an action method on the controller and made an assertion about the model it should build.</span></span> <span data-ttu-id="74527-487">テストの状態のその他の例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="74527-487">Here are some other examples of testing state:</span></span>

-   <span data-ttu-id="74527-488">作成を実行した後に、リポジトリに新しい employee オブジェクトが含まれていることを確認します。</span><span class="sxs-lookup"><span data-stu-id="74527-488">Verify the repository contains the new employee object after Create executes.</span></span>
-   <span data-ttu-id="74527-489">インデックスが実行された後に、モデルがすべての従業員の一覧を保持していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="74527-489">Verify the model holds a list of all employees after Index executes.</span></span>
-   <span data-ttu-id="74527-490">削除の実行後に、リポジトリに特定の従業員が含まれていないことを確認します。</span><span class="sxs-lookup"><span data-stu-id="74527-490">Verify the repository does not contain a given employee after Delete executes.</span></span>

<span data-ttu-id="74527-491">モックオブジェクトのもう1つの方法は、*相互作用*を検証することです。</span><span class="sxs-lookup"><span data-stu-id="74527-491">Another approach you’ll see with mock objects is to verify *interactions*.</span></span> <span data-ttu-id="74527-492">状態ベースのテストでは、オブジェクトの状態に関するアサーションが発生しますが、相互作用ベースのテストでは、オブジェクトがどのように対話するかについてのアサーションを行います</span><span class="sxs-lookup"><span data-stu-id="74527-492">While state based testing makes assertions about the state of objects, interaction based testing makes assertions about how objects interact.</span></span> <span data-ttu-id="74527-493">次に例を示します。</span><span class="sxs-lookup"><span data-stu-id="74527-493">For example:</span></span>

-   <span data-ttu-id="74527-494">Create の実行時に、コントローラーがリポジトリの Add メソッドを呼び出していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="74527-494">Verify the controller invokes the repository’s Add method when Create executes.</span></span>
-   <span data-ttu-id="74527-495">インデックスの実行時に、コントローラーによってリポジトリの FindAll メソッドが呼び出されることを確認します。</span><span class="sxs-lookup"><span data-stu-id="74527-495">Verify the controller invokes the repository’s FindAll method when Index executes.</span></span>
-   <span data-ttu-id="74527-496">編集の実行時に変更を保存するために、コントローラーが作業単位のコミット方法を呼び出していることを確認します。</span><span class="sxs-lookup"><span data-stu-id="74527-496">Verify the controller invokes the unit of work’s Commit method to save changes when Edit executes.</span></span>

<span data-ttu-id="74527-497">相互作用テストでは、コレクション内の調査やカウントの検証が行われないため、テストデータの量が少なくなることがよくあります。</span><span class="sxs-lookup"><span data-stu-id="74527-497">Interaction testing often requires less test data, because we aren’t poking inside of collections and verifying counts.</span></span> <span data-ttu-id="74527-498">たとえば、Details アクションが、正しい値を持つリポジトリの FindById メソッドを呼び出すことがわかっている場合、アクションは正しく動作していると考えられます。</span><span class="sxs-lookup"><span data-stu-id="74527-498">For example, if we know the Details action invokes a repository’s FindById method with the correct value - then the action is probably behaving correctly.</span></span> <span data-ttu-id="74527-499">FindById から返されるテストデータを設定しなくても、この動作を確認できます。</span><span class="sxs-lookup"><span data-stu-id="74527-499">We can verify this behavior without setting up any test data to return from FindById.</span></span>

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

<span data-ttu-id="74527-500">上記のテストフィクスチャで必要なセットアップは、基本クラスによって提供されるセットアップだけです。</span><span class="sxs-lookup"><span data-stu-id="74527-500">The only setup required in the above test fixture is the setup provided by the base class.</span></span> <span data-ttu-id="74527-501">コントローラーアクションを呼び出すと、Moq はモックリポジトリとのやり取りを記録します。</span><span class="sxs-lookup"><span data-stu-id="74527-501">When we invoke the controller action, Moq will record the interactions with the mock repository.</span></span> <span data-ttu-id="74527-502">Moq の Verify API を使用して、コントローラーが適切な ID 値で FindById を呼び出したかどうかを確認できます。</span><span class="sxs-lookup"><span data-stu-id="74527-502">Using the Verify API of Moq, we can ask Moq if the controller invoked FindById with the proper ID value.</span></span> <span data-ttu-id="74527-503">コントローラーがメソッドを呼び出さなかった場合、または予期しないパラメーター値を使用してメソッドを呼び出した場合は、Verify メソッドによって例外がスローされ、テストは失敗します。</span><span class="sxs-lookup"><span data-stu-id="74527-503">If the controller did not invoke the method, or invoked the method with an unexpected parameter value, the Verify method will throw an exception and the test will fail.</span></span>

<span data-ttu-id="74527-504">次に、作成アクションによって現在の作業単位に対して Commit が呼び出されることを確認する別の例を示します。</span><span class="sxs-lookup"><span data-stu-id="74527-504">Here is another example to verify the Create action invokes Commit on the current unit of work.</span></span>

``` csharp
    [TestMethod]
    public void ShouldCommitUnitOfWork() {
        _controller.Create(_newEmployee);
        _unitOfWork.Verify(u => u.Commit());
    }
```

<span data-ttu-id="74527-505">相互作用テストの1つの危険性は、相互作用を指定する傾向です。</span><span class="sxs-lookup"><span data-stu-id="74527-505">One danger with interaction testing is the tendency to over specify interactions.</span></span> <span data-ttu-id="74527-506">モックオブジェクトとのすべての相互作用を記録および検証する機能は、テストですべての相互作用を検証する必要があるという意味ではありません。</span><span class="sxs-lookup"><span data-stu-id="74527-506">The ability of the mock object to record and verify every interaction with the mock object doesn’t mean the test should try to verify every interaction.</span></span> <span data-ttu-id="74527-507">一部の対話は実装の詳細であり、現在のテストを満たすために*必要な*相互作用だけを検証する必要があります。</span><span class="sxs-lookup"><span data-stu-id="74527-507">Some interactions are implementation details and you should only verify the interactions *required* to satisfy the current test.</span></span>

<span data-ttu-id="74527-508">モックまたはフェイクの選択は、テストするシステムと、個人 (またはチーム) の設定に大きく左右されます。</span><span class="sxs-lookup"><span data-stu-id="74527-508">The choice between mocks or fakes largely depends on the system you are testing and your personal (or team) preferences.</span></span> <span data-ttu-id="74527-509">モックオブジェクトを使用すると、テストの double を実装するために必要なコードの量を大幅に削減できますが、すべてのユーザーがプログラミングの期待を理解し、相互作用を確認することはできません。</span><span class="sxs-lookup"><span data-stu-id="74527-509">Mock objects can drastically reduce the amount of code you need to implement test doubles, but not everyone is comfortable programming expectations and verifying interactions.</span></span>

## <a name="conclusions"></a><span data-ttu-id="74527-510">結論</span><span class="sxs-lookup"><span data-stu-id="74527-510">Conclusions</span></span>

<span data-ttu-id="74527-511">このホワイトペーパーでは、データの永続化に ADO.NET Entity Framework を使用しながら、テスト可能なコードを作成するためのいくつかの方法を説明しました。</span><span class="sxs-lookup"><span data-stu-id="74527-511">In this paper we’ve demonstrated several approaches to creating testable code while using the ADO.NET Entity Framework for data persistence.</span></span> <span data-ttu-id="74527-512">IObjectSet&lt;T&gt;などの組み込みの抽象化を活用したり、IRepository&lt;T&gt;のような独自の抽象化を作成したりすることができます。</span><span class="sxs-lookup"><span data-stu-id="74527-512">We can leverage built in abstractions like IObjectSet&lt;T&gt;, or create our own abstractions like IRepository&lt;T&gt;.</span></span><span data-ttu-id="74527-513">  どちらの場合も、ADO.NET Entity Framework 4.0 での POCO のサポートにより、これらの抽象化のコンシューマーは、永続的ではありませんが、高度なテストが可能になります。</span><span class="sxs-lookup"><span data-stu-id="74527-513">  In both cases, the POCO support in the ADO.NET Entity Framework 4.0 allows the consumers of these abstractions to remain persistent ignorant and highly testable.</span></span> <span data-ttu-id="74527-514">暗黙の遅延読み込みなどの追加の EF4 機能を使用すると、リレーショナルデータストアの詳細を気にせずに、ビジネスおよびアプリケーションのサービスコードを動作させることができます。</span><span class="sxs-lookup"><span data-stu-id="74527-514">Additional EF4 features like implicit lazy loading allows business and application service code to work without worrying about the details of a relational data store.</span></span> <span data-ttu-id="74527-515">最後に、作成した抽象化は単体テスト内で簡単にモックまたはフェイクにすることができます。また、これらのテスト double を使用して、高速な実行、高度に分離された信頼性の高いテストを実現できます。</span><span class="sxs-lookup"><span data-stu-id="74527-515">Finally, the abstractions we create are easy to mock or fake inside of unit tests, and we can use these test doubles to achieve fast running, highly isolated, and reliable tests.</span></span>

### <a name="additional-resources"></a><span data-ttu-id="74527-516">その他のリソース</span><span class="sxs-lookup"><span data-stu-id="74527-516">Additional Resources</span></span>

-   <span data-ttu-id="74527-517">Robert Martin "[単一責任の原則](https://www.objectmentor.com/resources/articles/srp.pdf)"</span><span class="sxs-lookup"><span data-stu-id="74527-517">Robert C. Martin, “ [The Single Responsibility Principle](https://www.objectmentor.com/resources/articles/srp.pdf)”</span></span>
-   <span data-ttu-id="74527-518">Martin Fowler、*エンタープライズアプリケーションアーキテクチャのパターン*[のパターンのカタログ](https://www.martinfowler.com/eaaCatalog/index.html)</span><span class="sxs-lookup"><span data-stu-id="74527-518">Martin Fowler, [Catalog of Patterns](https://www.martinfowler.com/eaaCatalog/index.html) from *Patterns of Enterprise Application Architecture*</span></span>
-   <span data-ttu-id="74527-519">Griffin Caprio、「[依存関係の注入](https://msdn.microsoft.com/magazine/cc163739.aspx)」</span><span class="sxs-lookup"><span data-stu-id="74527-519">Griffin Caprio, “ [Dependency Injection](https://msdn.microsoft.com/magazine/cc163739.aspx)”</span></span>
-   <span data-ttu-id="74527-520">データプログラミングに関するブログ「[チュートリアル: Entity Framework 4.0 を使用したテスト駆動開発](https://blogs.msdn.com/adonet/pages/walkthrough-test-driven-development-with-the-entity-framework-4-0.aspx)」をご覧ください。</span><span class="sxs-lookup"><span data-stu-id="74527-520">Data Programmability Blog, “ [Walkthrough: Test Driven Development with the Entity Framework 4.0](https://blogs.msdn.com/adonet/pages/walkthrough-test-driven-development-with-the-entity-framework-4-0.aspx)”.</span></span>
-   <span data-ttu-id="74527-521">データプログラミングのブログ「 [Entity Framework 4.0 でのリポジトリと作業単位のパターンの使用](https://blogs.msdn.com/adonet/archive/2009/06/16/using-repository-and-unit-of-work-patterns-with-entity-framework-4-0.aspx)」</span><span class="sxs-lookup"><span data-stu-id="74527-521">Data Programmability Blog, “ [Using Repository and Unit of Work patterns with Entity Framework 4.0](https://blogs.msdn.com/adonet/archive/2009/06/16/using-repository-and-unit-of-work-patterns-with-entity-framework-4-0.aspx)”</span></span>
-   <span data-ttu-id="74527-522">Aaron Jensen、「[マシン仕様の紹介](http://codebetter.com/blogs/aaron.jensen/archive/2008/05/08/introducing-machine-specifications-or-mspec-for-short.aspx)」</span><span class="sxs-lookup"><span data-stu-id="74527-522">Aaron Jensen, “ [Introducing Machine Specifications](http://codebetter.com/blogs/aaron.jensen/archive/2008/05/08/introducing-machine-specifications-or-mspec-for-short.aspx)”</span></span>
-   <span data-ttu-id="74527-523">Eric Lee、" [BDD With MSTest](https://blogs.msdn.com/elee/archive/2009/01/20/bdd-with-mstest.aspx)"</span><span class="sxs-lookup"><span data-stu-id="74527-523">Eric Lee, “ [BDD with MSTest](https://blogs.msdn.com/elee/archive/2009/01/20/bdd-with-mstest.aspx)”</span></span>
-   <span data-ttu-id="74527-524">Eric Evans ([ドメイン駆動設計](https://books.google.com/books?id=7dlaMs0SECsC&printsec=frontcover&dq=evans%20domain%20driven%20design&hl=en&ei=cHztS6C8KIaglAfA_dS1CA&sa=X&oi=book_result&ct=result&resnum=1&ved=0CCoQ6AEwAA))</span><span class="sxs-lookup"><span data-stu-id="74527-524">Eric Evans, “ [Domain Driven Design](https://books.google.com/books?id=7dlaMs0SECsC&printsec=frontcover&dq=evans%20domain%20driven%20design&hl=en&ei=cHztS6C8KIaglAfA_dS1CA&sa=X&oi=book_result&ct=result&resnum=1&ved=0CCoQ6AEwAA)”</span></span>
-   <span data-ttu-id="74527-525">Martin Fowler、"[モックはスタブ](https://martinfowler.com/articles/mocksArentStubs.html)ではない"</span><span class="sxs-lookup"><span data-stu-id="74527-525">Martin Fowler, “ [Mocks Aren’t Stubs](https://martinfowler.com/articles/mocksArentStubs.html)”</span></span>
-   <span data-ttu-id="74527-526">Martin Fowler、" [Test Double](https://martinfowler.com/bliki/TestDouble.html)"</span><span class="sxs-lookup"><span data-stu-id="74527-526">Martin Fowler, “ [Test Double](https://martinfowler.com/bliki/TestDouble.html)”</span></span>
-   [<span data-ttu-id="74527-527">Moq</span><span class="sxs-lookup"><span data-stu-id="74527-527">Moq</span></span>](https://code.google.com/p/moq/)

### <a name="biography"></a><span data-ttu-id="74527-528">略歴</span><span class="sxs-lookup"><span data-stu-id="74527-528">Biography</span></span>

<span data-ttu-id="74527-529">Scott Allen は、Pluralsight のテクニカルスタッフのメンバーであり、OdeToCode.com の創設者です。</span><span class="sxs-lookup"><span data-stu-id="74527-529">Scott Allen is a member of the technical staff at Pluralsight and the founder of OdeToCode.com.</span></span> <span data-ttu-id="74527-530">15年間の商用ソフトウェア開発において、8ビット embedded デバイスから拡張性の高い ASP.NET web アプリケーションまで、すべてのソリューションについて Scott が取り組んでいます。</span><span class="sxs-lookup"><span data-stu-id="74527-530">In 15 years of commercial software development, Scott has worked on solutions for everything from 8-bit embedded devices to highly scalable ASP.NET web applications.</span></span> <span data-ttu-id="74527-531">OdeToCode、または[https://twitter.com/OdeToCode](https://twitter.com/OdeToCode)の Twitter で Scott に連絡できます。</span><span class="sxs-lookup"><span data-stu-id="74527-531">You can reach Scott on his blog at OdeToCode, or on Twitter at [https://twitter.com/OdeToCode](https://twitter.com/OdeToCode).</span></span>
