---
title: EF Core を使用するコードのテスト - EF Core
description: EF Core を使用するアプリケーションをテストするさまざまな方法
author: ajcvickers
ms.date: 04/22/2020
uid: core/miscellaneous/testing/index
ms.openlocfilehash: 7929c284c2794b2fcc95235ae413d56895ebb6e2
ms.sourcegitcommit: 949faaba02e07e44359e77d7935f540af5c32093
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2020
ms.locfileid: "87526811"
---
# <a name="testing-code-that-uses-ef-core"></a><span data-ttu-id="c0d75-103">EF Core を使用するコードのテスト</span><span class="sxs-lookup"><span data-stu-id="c0d75-103">Testing code that uses EF Core</span></span>

<span data-ttu-id="c0d75-104">データベースにアクセスするコードをテストするには、次のいずれかを実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c0d75-104">Testing code that accesses a database requires either:</span></span>
* <span data-ttu-id="c0d75-105">運用環境で使用されているのと同じデータベース システムに対して、クエリと更新を実行する。</span><span class="sxs-lookup"><span data-stu-id="c0d75-105">Running queries and updates against the same database system used in production.</span></span>
* <span data-ttu-id="c0d75-106">管理がより容易な他のデータベース システムに、クエリと更新プログラムを実行する。</span><span class="sxs-lookup"><span data-stu-id="c0d75-106">Running queries and updates against some other easier to manage database system.</span></span>
* <span data-ttu-id="c0d75-107">データベースをまったく使用しない、テスト ダブルまたはその他のメカニズムを使用する。</span><span class="sxs-lookup"><span data-stu-id="c0d75-107">Using test doubles or some other mechanism to avoid using a database at all.</span></span>

<span data-ttu-id="c0d75-108">このドキュメントでは、これらの各選択肢とのトレードオフについて概説し、各方法で EF Core を使用する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="c0d75-108">This document outlines the trade-offs involved in each of these choices and shows how EF Core can be used with each approach.</span></span>  

> [!TIP]
> <span data-ttu-id="c0d75-109">ここで紹介した概念を示すコードについては、「[EF Core テストのサンプル](xref:core/miscellaneous/testing/testing-sample)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c0d75-109">See [EF Core testing sample](xref:core/miscellaneous/testing/testing-sample) for code demonstrating the concepts introduced here.</span></span> 

## <a name="all-database-providers-are-not-equal"></a><span data-ttu-id="c0d75-110">すべてのデータベース プロバイダーが同一ではない</span><span class="sxs-lookup"><span data-stu-id="c0d75-110">All database providers are not equal</span></span>

<span data-ttu-id="c0d75-111">EF Core は、基になるデータベース システムのすべての側面を抽象化するように設計されていないことを理解しておくことが非常に重要です。</span><span class="sxs-lookup"><span data-stu-id="c0d75-111">It is very important to understand that EF Core is not designed to abstract every aspect of the underlying database system.</span></span>
<span data-ttu-id="c0d75-112">EF Core はむしろ、すべてのデータベース システムで使用できるパターンと概念のセットです。</span><span class="sxs-lookup"><span data-stu-id="c0d75-112">Instead, EF Core is a common set of patterns and concepts that can be used with any database system.</span></span>
<span data-ttu-id="c0d75-113">EF Core データベース プロバイダーは、この共通のフレームワーク上に、データベース固有の動作と機能をレイヤー化します。</span><span class="sxs-lookup"><span data-stu-id="c0d75-113">EF Core database providers then layer database-specific behavior and functionality over this common framework.</span></span>
<span data-ttu-id="c0d75-114">これにより、各データベース システムは、必要に応じて他のデータベース システムと同じ機能を果たしながら、それが最も得意とする操作を実行します。</span><span class="sxs-lookup"><span data-stu-id="c0d75-114">This allows each database system to do what it does best while still maintaining commonality, where appropriate, with other database systems.</span></span> 

<span data-ttu-id="c0d75-115">これは基本的に、データベース プロバイダーを切り替えた場合、EF Core の動作が変わってしまい、別のすべての動作に明確に対応するとされていない限り、そのアプリケーションの正常な動作は期待できないことを意味します。</span><span class="sxs-lookup"><span data-stu-id="c0d75-115">Fundamentally, this means that switching out the database provider will change EF Core behavior and the application can't be expected to function correctly unless it explicitly accounts for any differences in behavior.</span></span>
<span data-ttu-id="c0d75-116">ただし、リレーショナル データベース同士には共通性が多いため、多くの場合、これで機能します。</span><span class="sxs-lookup"><span data-stu-id="c0d75-116">That being said, in many cases doing this will work because there is a high degree of commonality amongst relational databases.</span></span>
<span data-ttu-id="c0d75-117">これには良い点と悪い点があります。</span><span class="sxs-lookup"><span data-stu-id="c0d75-117">This is good and bad.</span></span>
<span data-ttu-id="c0d75-118">良い点は、データベース システムを比較的簡単に切り替えることができることです。</span><span class="sxs-lookup"><span data-stu-id="c0d75-118">Good because moving between database systems can be relatively easy.</span></span>
<span data-ttu-id="c0d75-119">悪い点は、アプリケーションが新しいデータベース システムで完全にテストされていない場合、セキュリティについて誤った意識を与える可能性があるということです。</span><span class="sxs-lookup"><span data-stu-id="c0d75-119">Bad because it can give a false sense of security if the application is not fully tested against the new database system.</span></span>  

## <a name="approach-1-production-database-system"></a><span data-ttu-id="c0d75-120">アプローチ 1: 運用データベース システム</span><span class="sxs-lookup"><span data-stu-id="c0d75-120">Approach 1: Production database system</span></span>

<span data-ttu-id="c0d75-121">前のセクションで説明したように、運用環境で実行される内容が確実にテストされるには、同じデータベース システムを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c0d75-121">As described in the previous section, the only way to be sure you are testing what runs in production is to use the same database system.</span></span>
<span data-ttu-id="c0d75-122">たとえば、展開されているアプリケーションが SQL Azure を使用する場合、SQL Azure に対してもテストを実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="c0d75-122">For example, if the deployed application uses SQL Azure, then testing should also be done against SQL Azure.</span></span>

<span data-ttu-id="c0d75-123">ただし、コードを書き進めながらすべての開発者が SQL Azure でテストを実行するには、時間もコストもかかります。</span><span class="sxs-lookup"><span data-stu-id="c0d75-123">However, having every developer run tests against SQL Azure while actively working on the code would be both slow and expensive.</span></span>
<span data-ttu-id="c0d75-124">テスト効率の向上には、どのタイミングで実稼働データベース システムから離れるのが適しているかというのが、これらの全アプローチにおける主なトレードオフです。</span><span class="sxs-lookup"><span data-stu-id="c0d75-124">This illustrates the main trade-off involved throughout these approaches: when is it appropriate to deviate from the production database system so as to improve test efficiency?</span></span>

<span data-ttu-id="c0d75-125">さいわいにも、この場合の答えは非常に簡単です。開発者は、ローカルまたはオンプレミスの SQL Server を使用してテストすればよいのです。</span><span class="sxs-lookup"><span data-stu-id="c0d75-125">Luckily, in this case the answer is quite easy: use local or on-premises SQL Server for developer testing.</span></span>
<span data-ttu-id="c0d75-126">SQL Azure と SQL Server は非常に似ているため、SQL Server でテストをすることは通常、妥当なトレードオフになります。</span><span class="sxs-lookup"><span data-stu-id="c0d75-126">SQL Azure and SQL Server are extremely similar, so testing against SQL Server is usually a reasonable trade-off.</span></span>
<span data-ttu-id="c0d75-127">ただし、それでも実稼働環境に移行する前に、SQL Azure 自体でもテストをした方が賢明です。</span><span class="sxs-lookup"><span data-stu-id="c0d75-127">That being said, it is still wise to run tests against SQL Azure itself before going into production.</span></span>
 
### <a name="localdb"></a><span data-ttu-id="c0d75-128">LocalDB</span><span class="sxs-lookup"><span data-stu-id="c0d75-128">LocalDB</span></span> 

<span data-ttu-id="c0d75-129">すべての主要なデータベース システムには、ローカルでのテスト用の何らかの "開発者向けのバージョン" があります。</span><span class="sxs-lookup"><span data-stu-id="c0d75-129">All the major database systems have some form of "Developer Edition" for local testing.</span></span>
<span data-ttu-id="c0d75-130">SQL Server にも [LocalDB](/sql/database-engine/configure-windows/sql-server-express-localdb?view=sql-server-ver15) という機能があります。</span><span class="sxs-lookup"><span data-stu-id="c0d75-130">SQL Server also has a feature called [LocalDB](/sql/database-engine/configure-windows/sql-server-express-localdb?view=sql-server-ver15).</span></span>
<span data-ttu-id="c0d75-131">LocalDB の主な利点は、必要に応じてデータベース インスタンスを起動できることです。</span><span class="sxs-lookup"><span data-stu-id="c0d75-131">The primary advantage of LocalDB is that it spins up the database instance on demand.</span></span>
<span data-ttu-id="c0d75-132">これにより、テストを実行していないときも、お使いのコンピューター上でデータベース サービスが実行されることを回避できます。</span><span class="sxs-lookup"><span data-stu-id="c0d75-132">This avoids having a database service running on your machine even when you're not running tests.</span></span>

<span data-ttu-id="c0d75-133">LocalDB に問題がないわけではありません。</span><span class="sxs-lookup"><span data-stu-id="c0d75-133">LocalDB is not without its issues:</span></span>
* <span data-ttu-id="c0d75-134">これでは、[SQL Server Developer エディション](/sql/sql-server/editions-and-components-of-sql-server-2016?view=sql-server-ver15)がサポートするものをすべてサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="c0d75-134">It doesn't support everything that [SQL Server Developer Edition](/sql/sql-server/editions-and-components-of-sql-server-2016?view=sql-server-ver15) does.</span></span>
* <span data-ttu-id="c0d75-135">Linux では利用できません。</span><span class="sxs-lookup"><span data-stu-id="c0d75-135">It isn't available on Linux.</span></span>
* <span data-ttu-id="c0d75-136">サービスの起動後の最初のテスト実行では、遅延が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="c0d75-136">It can cause lag on first test run as the service is spun up.</span></span>

<span data-ttu-id="c0d75-137">個人的には、私は自分の開発用コンピューターでデータベース サービスの実行が問題になったことはなく、開発者向けバージョンの一般使用をお勧めしています。</span><span class="sxs-lookup"><span data-stu-id="c0d75-137">Personally, I've never found it a problem having a database service running on my dev machine and I would generally recommend using Developer Edition instead.</span></span>
<span data-ttu-id="c0d75-138">ただし、LocalDB は特にあまり高性能ではない開発用コンピューターを使用している一部のユーザーには適していると言えるでしょう。</span><span class="sxs-lookup"><span data-stu-id="c0d75-138">However, LocalDB may be appropriate for some people, especially on less powerful dev machines.</span></span>

<span data-ttu-id="c0d75-139">開発マシン上でデータベース システムを直接実行しないようにするには、Docker コンテナー (または同様のもの) で [SQL Server を実行する](/sql/linux/quickstart-install-connect-docker?view=sql-server-ver15) (または他のデータベース システムを実行する) 方法もあります。</span><span class="sxs-lookup"><span data-stu-id="c0d75-139">[Running SQL Server](/sql/linux/quickstart-install-connect-docker?view=sql-server-ver15) (or any other database system) in a Docker container (or similar) is another way to avoid running the database system directly on your development machine.</span></span>  

## <a name="approach-2-sqlite"></a><span data-ttu-id="c0d75-140">アプローチ 2: SQLite</span><span class="sxs-lookup"><span data-stu-id="c0d75-140">Approach 2: SQLite</span></span>

<span data-ttu-id="c0d75-141">EF Core では、SQL Server プロバイダーを、主にローカルの SQL Server インスタンスに対して実行することでテストします。</span><span class="sxs-lookup"><span data-stu-id="c0d75-141">EF Core tests the SQL Server provider primarily by running it against a local SQL Server instance.</span></span>
<span data-ttu-id="c0d75-142">高速なコンピューターでは、これらのテストでは数千のクエリが数分で実行されます。</span><span class="sxs-lookup"><span data-stu-id="c0d75-142">These tests run tens of thousands of queries in a couple of minutes on a fast machine.</span></span>
<span data-ttu-id="c0d75-143">これは、実際のデータベース システムを使用することは、パフォーマンスに優れたソリューションになる可能性があることを示しています。</span><span class="sxs-lookup"><span data-stu-id="c0d75-143">This illustrates that using the real database system can be a performant solution.</span></span>
<span data-ttu-id="c0d75-144">一部の軽量データベースの使用のみが、すばやくテストを実行できるというのは神話です。</span><span class="sxs-lookup"><span data-stu-id="c0d75-144">It is a myth that using some lighter-weight database is the only way to run tests quickly.</span></span>

<span data-ttu-id="c0d75-145">そうは言っても、ご自分の実稼働データベース システムに近いものでテストを実行できない理由がある場合はどうしたらよいでしょうか。</span><span class="sxs-lookup"><span data-stu-id="c0d75-145">That being said, what if for whatever reason you can't run tests against something close to your production database system?</span></span>
<span data-ttu-id="c0d75-146">次によい選択肢は、同様の機能を持つものを使用することです。</span><span class="sxs-lookup"><span data-stu-id="c0d75-146">The next best choice is to use something with similar functionality.</span></span>
<span data-ttu-id="c0d75-147">これは通常、別のリレーショナル データベースを使用することを意味し、[SQLite](https://sqlite.org/index.html) が選択肢であることは明らかです。</span><span class="sxs-lookup"><span data-stu-id="c0d75-147">This usually means another relational database, for which [SQLite](https://sqlite.org/index.html) is the obvious choice.</span></span>

<span data-ttu-id="c0d75-148">SQLite が選択肢として適切であるのは次のためです。</span><span class="sxs-lookup"><span data-stu-id="c0d75-148">SQLite is a good choice because:</span></span>
* <span data-ttu-id="c0d75-149">お使いのアプリケーションでインプロセスで実行されるため、オーバーヘッドが低いです。</span><span class="sxs-lookup"><span data-stu-id="c0d75-149">It runs in-process with your application and so has low overhead.</span></span>
* <span data-ttu-id="c0d75-150">単純で自動作成されたファイルをデータベースに使用するため、データベース管理が不要です。</span><span class="sxs-lookup"><span data-stu-id="c0d75-150">It uses simple, automatically created files for databases, and so doesn't require database management.</span></span>
* <span data-ttu-id="c0d75-151">ファイルの作成さえをも回避するインメモリ モードがあります。</span><span class="sxs-lookup"><span data-stu-id="c0d75-151">It has an in-memory mode that avoids even the file creation.</span></span>

<span data-ttu-id="c0d75-152">ただし、次の点に注意してください。</span><span class="sxs-lookup"><span data-stu-id="c0d75-152">However, remember that:</span></span>
* <span data-ttu-id="c0d75-153">SQLite は当然、お使いの実稼働データベース システムで実行されるすべての機能をサポートしているわけではありません。</span><span class="sxs-lookup"><span data-stu-id="c0d75-153">SQLite inevitability doesn't support everything that your production database system does.</span></span>
* <span data-ttu-id="c0d75-154">SQLite は、一部のクエリではお使いの実稼働データベース システムと異なる動作をします。</span><span class="sxs-lookup"><span data-stu-id="c0d75-154">SQLite will behave differently than your production database system for some queries.</span></span>

<span data-ttu-id="c0d75-155">そのため、何らかのテストに SQLite を使用する場合は、お使いの実稼働データベース システムでも必ずテストをする必要があります。</span><span class="sxs-lookup"><span data-stu-id="c0d75-155">So if you do use SQLite for some testing, make sure to also test against your real database system.</span></span>

<span data-ttu-id="c0d75-156">EF Core 固有のガイダンスについては、「[SQLite を使用したテスト](xref:core/miscellaneous/testing/sqlite)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="c0d75-156">See [Testing with SQLite](xref:core/miscellaneous/testing/sqlite) for EF Core specific guidance.</span></span> 

## <a name="approach-3-the-ef-core-in-memory-database"></a><span data-ttu-id="c0d75-157">アプローチ 3:EF Core のインメモリ データベース</span><span class="sxs-lookup"><span data-stu-id="c0d75-157">Approach 3: The EF Core in-memory database</span></span>

<span data-ttu-id="c0d75-158">EF Core には、EF Core 自体の内部テストに使用するインメモリ データベースが付属しています。</span><span class="sxs-lookup"><span data-stu-id="c0d75-158">EF Core comes with an in-memory database that we use for internal testing of EF Core itself.</span></span>
<span data-ttu-id="c0d75-159">一般に、このデータベースは、**EF Core を使用するアプリケーションのテストには適していません**。</span><span class="sxs-lookup"><span data-stu-id="c0d75-159">This database is in general **not suitable for testing applications that use EF Core**.</span></span> <span data-ttu-id="c0d75-160">具体的には、次のように使用します。</span><span class="sxs-lookup"><span data-stu-id="c0d75-160">Specifically:</span></span>

* <span data-ttu-id="c0d75-161">リレーショナル データベースではありません。</span><span class="sxs-lookup"><span data-stu-id="c0d75-161">It is not a relational database.</span></span>
* <span data-ttu-id="c0d75-162">トランザクションはサポートされていません。</span><span class="sxs-lookup"><span data-stu-id="c0d75-162">It doesn't support transactions.</span></span>
* <span data-ttu-id="c0d75-163">生 SQL クエリを実行することはできません。</span><span class="sxs-lookup"><span data-stu-id="c0d75-163">It cannot run raw SQL queries.</span></span>
* <span data-ttu-id="c0d75-164">パフォーマンスのために最適化されていません。</span><span class="sxs-lookup"><span data-stu-id="c0d75-164">It is not optimized for performance.</span></span>

<span data-ttu-id="c0d75-165">EF Core 内部をテストする際には、これをテストにデータベースが関係ないときに特に使用するため、これらのいずれも重要ではありません。</span><span class="sxs-lookup"><span data-stu-id="c0d75-165">None of this is very important when testing EF Core internals because we use it specifically where the database is irrelevant to the test.</span></span>
<span data-ttu-id="c0d75-166">一方、EF Core を使用するアプリケーションをテストする場合、これらが非常に重要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="c0d75-166">On the other hand, these things tend to be very important when testing an application that uses EF Core.</span></span>

## <a name="unit-testing"></a><span data-ttu-id="c0d75-167">単体テスト</span><span class="sxs-lookup"><span data-stu-id="c0d75-167">Unit testing</span></span>

<span data-ttu-id="c0d75-168">データベースの一部のデータは使用するが、データベースの相互作用を実際にはテストしないビジネス ロジックをテストするとします。</span><span class="sxs-lookup"><span data-stu-id="c0d75-168">Consider testing a piece of business logic that might need to use some data from a database, but is not inherently testing the database interactions.</span></span>
<span data-ttu-id="c0d75-169">これの方法の 1 つに、モックやフェイクなど、[テスト ダブル](https://en.wikipedia.org/wiki/Test_double)を使用する方法があります。</span><span class="sxs-lookup"><span data-stu-id="c0d75-169">One option is to use a [test double](https://en.wikipedia.org/wiki/Test_double) such as a mock or fake.</span></span>

<span data-ttu-id="c0d75-170">テスト ダブルは、EF Core の内部テストで使用します。</span><span class="sxs-lookup"><span data-stu-id="c0d75-170">We use test doubles for internal testing of EF Core.</span></span>
<span data-ttu-id="c0d75-171">ただし、DbContext または IQueryable をモックすることはありません。</span><span class="sxs-lookup"><span data-stu-id="c0d75-171">However, we never try to mock DbContext or IQueryable.</span></span>
<span data-ttu-id="c0d75-172">これを行うのは困難で、煩雑で、不安定です。</span><span class="sxs-lookup"><span data-stu-id="c0d75-172">Doing so is difficult, cumbersome, and fragile.</span></span>
<span data-ttu-id="c0d75-173">**これは行わないでください。**</span><span class="sxs-lookup"><span data-stu-id="c0d75-173">**Don't do it.**</span></span>

<span data-ttu-id="c0d75-174">代わりに、DbContext を使用するものの単体テストには、EF インメモリ データベースを使用します。</span><span class="sxs-lookup"><span data-stu-id="c0d75-174">Instead we use the EF in-memory database when unit testing something that uses DbContext.</span></span>
<span data-ttu-id="c0d75-175">この場合、テストがデータベースの動作に依存しないため、EF インメモリ データベースを使用するのが適切です。</span><span class="sxs-lookup"><span data-stu-id="c0d75-175">In this case using the EF in-memory database is appropriate because the test is not dependent on database behavior.</span></span>
<span data-ttu-id="c0d75-176">実際のデータベース クエリや更新プログラムをテストには、これを行わないでください。</span><span class="sxs-lookup"><span data-stu-id="c0d75-176">Just don't do this to test actual database queries or updates.</span></span>   

<span data-ttu-id="c0d75-177">[EF Core テスト サンプル](xref:core/miscellaneous/testing/testing-sample)は、EF インメモリ データベースだけでなく SQL Server と SQLite を使用したテストを示しています。</span><span class="sxs-lookup"><span data-stu-id="c0d75-177">The [EF Core testing sample](xref:core/miscellaneous/testing/testing-sample) demonstrates tests using the EF in-memory database, as well as SQL Server and SQLite.</span></span> 
