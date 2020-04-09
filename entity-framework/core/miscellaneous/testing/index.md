---
title: EF Core を使ったコンポーネントのテスト - EF Core
description: EF Core を使用するアプリケーションをテストするさまざまな方法
author: ajcvickers
ms.date: 03/23/2020
uid: core/miscellaneous/testing/index
ms.openlocfilehash: b1ab37ebb0a3aae09d5d5b225f746cf83dfba170
ms.sourcegitcommit: 9b562663679854c37c05fca13d93e180213fb4aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2020
ms.locfileid: "80634248"
---
# <a name="testing-code-that-uses-ef-core"></a><span data-ttu-id="92e07-103">EF Core を使用するコードのテスト</span><span class="sxs-lookup"><span data-stu-id="92e07-103">Testing code that uses EF Core</span></span>

<span data-ttu-id="92e07-104">データベースにアクセスするコードをテストするには、次のいずれかを実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="92e07-104">Testing code that accesses a database requires either:</span></span>
* <span data-ttu-id="92e07-105">運用環境で使用されているのと同じデータベース システムに対して、クエリと更新を実行する。</span><span class="sxs-lookup"><span data-stu-id="92e07-105">Running queries and updates against the same database system used in production.</span></span>
* <span data-ttu-id="92e07-106">管理がより容易な他のデータベース システムに、クエリと更新プログラムを実行する。</span><span class="sxs-lookup"><span data-stu-id="92e07-106">Running queries and updates against some other easier to manage database system.</span></span>
* <span data-ttu-id="92e07-107">データベースをまったく使用しない、テスト ダブルまたはその他のメカニズムを使用する。</span><span class="sxs-lookup"><span data-stu-id="92e07-107">Using test doubles or some other mechanism to avoid using a database at all.</span></span>

<span data-ttu-id="92e07-108">このドキュメントでは、これらの各選択肢とのトレードオフについて概説し、各方法で EF Core を使用する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="92e07-108">This document outlines the trade offs involved in each of these choices and shows how EF Core can be used with each approach.</span></span>  

## <a name="all-database-providers-are-not-equal"></a><span data-ttu-id="92e07-109">すべてのデータベース プロバイダーが同一ではない</span><span class="sxs-lookup"><span data-stu-id="92e07-109">All database providers are not equal</span></span>

<span data-ttu-id="92e07-110">EF Core は、基になるデータベース システムのすべての側面を抽象化するように設計されていないことを理解しておくことが非常に重要です。</span><span class="sxs-lookup"><span data-stu-id="92e07-110">It is very important to understand that EF Core is not designed to abstract every aspect of the underlying database system.</span></span>
<span data-ttu-id="92e07-111">EF Core はむしろ、すべてのデータベース システムで使用できるパターンと概念のセットです。</span><span class="sxs-lookup"><span data-stu-id="92e07-111">Instead, EF Core is a common set of patterns and concepts that can be used with any database system.</span></span>
<span data-ttu-id="92e07-112">EF Core データベース プロバイダーは、この共通のフレームワーク上に、データベース固有の動作と機能をレイヤー化します。</span><span class="sxs-lookup"><span data-stu-id="92e07-112">EF Core database providers then layer database-specific behavior and functionality over this common framework.</span></span>
<span data-ttu-id="92e07-113">これにより、各データベース システムは、必要に応じて他のデータベース システムと同じ機能を果たしながら、それが最も得意とする操作を実行します。</span><span class="sxs-lookup"><span data-stu-id="92e07-113">This allows each database system to do what it does best while still maintaining commonality, where appropriate, with other database systems.</span></span> 

<span data-ttu-id="92e07-114">これは基本的に、データベース プロバイダーを切り替えた場合、EF Core の動作が変わってしまい、別のすべての動作に明確に対応するとされていない限り、そのアプリケーションの正常な動作は期待できないことを意味します。</span><span class="sxs-lookup"><span data-stu-id="92e07-114">Fundamentally, this means that switching out the database provider will change EF Core behavior and the application can't be expected to function correctly unless it explicitly accounts for all differences in behavior.</span></span>
<span data-ttu-id="92e07-115">ただし、リレーショナル データベース同士には共通性が多いため、多くの場合、これで機能します。</span><span class="sxs-lookup"><span data-stu-id="92e07-115">That being said, in many cases doing this will work because there is a high degree of commonality amongst relational databases.</span></span>
<span data-ttu-id="92e07-116">これには良い点と悪い点があります。</span><span class="sxs-lookup"><span data-stu-id="92e07-116">This is good and bad.</span></span>
<span data-ttu-id="92e07-117">良い点は、データベースを比較的簡単に切り替えることができることです。</span><span class="sxs-lookup"><span data-stu-id="92e07-117">Good because moving between databases can be relatively easy.</span></span>
<span data-ttu-id="92e07-118">悪い点は、アプリケーションが新しいデータベース システムで完全にテストされていない場合、セキュリティについて誤った意識を与える可能性があるということです。</span><span class="sxs-lookup"><span data-stu-id="92e07-118">Bad because it can give a false sense of security if the application is not fully tested against the new database system.</span></span>  

## <a name="approach-1-production-database-system"></a><span data-ttu-id="92e07-119">アプローチ 1: 運用データベース システム</span><span class="sxs-lookup"><span data-stu-id="92e07-119">Approach 1: Production database system</span></span>

<span data-ttu-id="92e07-120">前のセクションで説明したように、運用環境で実行される内容が確実にテストされるには、同じデータベース システムを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="92e07-120">As described in the previous section, the only way to be sure you are testing what runs in production is to use the same database system.</span></span>
<span data-ttu-id="92e07-121">たとえば、展開されているアプリケーションが SQL Azure を使用する場合、SQL Azure に対してもテストを実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="92e07-121">For example, if the deployed application uses SQL Azure, then testing should also be done against SQL Azure.</span></span>

<span data-ttu-id="92e07-122">ただし、コードを書き進めながらすべての開発者が SQL Azure でテストを実行するには、時間もコストもかかります。</span><span class="sxs-lookup"><span data-stu-id="92e07-122">However, having every developer run tests against SQL Azure while actively working on the code would be both slow and expensive.</span></span>
<span data-ttu-id="92e07-123">テスト効率の向上には、どのタイミングで実稼働データベース システムから離れるのが適しているかというのが、これらの全アプローチにおける主なトレードオフです。</span><span class="sxs-lookup"><span data-stu-id="92e07-123">This illustrates the main trade off involved throughout these approaches: when is it appropriate to deviate from the production database system so as to improve test efficiency?</span></span>

<span data-ttu-id="92e07-124">さいわいにも、この場合の答えは非常に簡単です。開発者は、ローカルまたはオンプレミスの SQL Server を使用してテストすればよいのです。</span><span class="sxs-lookup"><span data-stu-id="92e07-124">Luckily, in this case the answer is quite easy: use local or on-premises SQL Server for developer testing.</span></span>
<span data-ttu-id="92e07-125">SQL Azure と SQL Server は非常に似ているため、SQL Server でテストをすることは通常、妥当なトレードオフになります。</span><span class="sxs-lookup"><span data-stu-id="92e07-125">SQL Azure and SQL Server are extremely similar, so testing against SQL Server is usually a reasonable trade off.</span></span>
<span data-ttu-id="92e07-126">ただし、それでも実稼働環境に移行する前に、SQL Azure 自体でもテストをした方が賢明です。</span><span class="sxs-lookup"><span data-stu-id="92e07-126">That being said, it is still wise to run tests against SQL Azure itself before going into production.</span></span>
 
### <a name="localdb"></a><span data-ttu-id="92e07-127">LocalDb</span><span class="sxs-lookup"><span data-stu-id="92e07-127">LocalDb</span></span> 

<span data-ttu-id="92e07-128">すべての主要なデータベース システムには、ローカルでのテスト用の何らかの "開発者向けのバージョン" があります。</span><span class="sxs-lookup"><span data-stu-id="92e07-128">All the major database systems have some form of "Developer Edition" for local testing.</span></span>
<span data-ttu-id="92e07-129">SQL Server にも、[LocalDb](/sql/database-engine/configure-windows/sql-server-express-localdb?view=sql-server-ver15) と呼ばれる機能があります。</span><span class="sxs-lookup"><span data-stu-id="92e07-129">SQL Server also also has a feature called [LocalDb](/sql/database-engine/configure-windows/sql-server-express-localdb?view=sql-server-ver15).</span></span>
<span data-ttu-id="92e07-130">LocalDb の主な利点は、必要に応じてデータベース インスタンスを起動できることです。</span><span class="sxs-lookup"><span data-stu-id="92e07-130">The primary advantage of LocalDb is that it spins up the database instance on demand.</span></span>
<span data-ttu-id="92e07-131">これにより、テストを実行していないときも、お使いのコンピューター上でデータベース サービスが実行されることを回避できます。</span><span class="sxs-lookup"><span data-stu-id="92e07-131">This avoids having a database service running on your machine even when you're not running tests.</span></span>

<span data-ttu-id="92e07-132">LocalDb に問題がないわけではありません。</span><span class="sxs-lookup"><span data-stu-id="92e07-132">LocalDb is not without it's issues:</span></span>
* <span data-ttu-id="92e07-133">これでは、[SQL Server Developer エディション](/sql/sql-server/editions-and-components-of-sql-server-2016?view=sql-server-ver15)がサポートするものをすべてサポートしていません。</span><span class="sxs-lookup"><span data-stu-id="92e07-133">It doesn't support everything that [SQL Server Developer Edition](/sql/sql-server/editions-and-components-of-sql-server-2016?view=sql-server-ver15) does.</span></span>
* <span data-ttu-id="92e07-134">Linux では利用できません。</span><span class="sxs-lookup"><span data-stu-id="92e07-134">It isn't available on Linux.</span></span>
* <span data-ttu-id="92e07-135">サービスの起動後の最初のテスト実行では、遅延が発生する可能性があります。</span><span class="sxs-lookup"><span data-stu-id="92e07-135">It can cause lag on first test run as the service is spun up.</span></span>

<span data-ttu-id="92e07-136">個人的には、私は自分の開発用コンピューターでデータベース サービスの実行が問題になったことはなく、開発者向けバージョンの一般使用をお勧めしています。</span><span class="sxs-lookup"><span data-stu-id="92e07-136">Personally, I've never found it a problem having a database service running on my dev machine and I would generally recommend using Developer Edition instead.</span></span>
<span data-ttu-id="92e07-137">ただし、これは特にあまり高性能ではない開発用コンピューターを使用している一部のユーザーには適していると言えるでしょう。</span><span class="sxs-lookup"><span data-stu-id="92e07-137">However, it may be appropriate for some people, especially on less powerful dev machines.</span></span>  

## <a name="approach-2-sqlite"></a><span data-ttu-id="92e07-138">アプローチ 2: SQLite</span><span class="sxs-lookup"><span data-stu-id="92e07-138">Approach 2: SQLite</span></span>

<span data-ttu-id="92e07-139">EF Core では、SQL Server プロバイダーを、主にローカルの SQL Server インスタンスに対して実行することでテストします。</span><span class="sxs-lookup"><span data-stu-id="92e07-139">EF Core tests the SQL Server provider primarily by running it against a local SQL Server instance.</span></span>
<span data-ttu-id="92e07-140">高速なコンピューターでは、これらのテストでは数千のクエリが数分で実行されます。</span><span class="sxs-lookup"><span data-stu-id="92e07-140">These tests run tens of thousands of queries in a couple of minutes on a fast machine.</span></span>
<span data-ttu-id="92e07-141">これは、実際のデータベース システムを使用することは、パフォーマンスに優れたソリューションになる可能性があることを示しています。</span><span class="sxs-lookup"><span data-stu-id="92e07-141">This illustrates that using the real database system can be a performant solution.</span></span>
<span data-ttu-id="92e07-142">一部の軽量データベースの使用のみが、すばやくテストを実行できるというのは神話です。</span><span class="sxs-lookup"><span data-stu-id="92e07-142">It is a myth that using some lighter-weight database is the only way to run tests quickly.</span></span>

<span data-ttu-id="92e07-143">そうは言っても、ご自分の実稼働データベース システムに近いものでテストを実行できない理由がある場合はどうしたらよいでしょうか。</span><span class="sxs-lookup"><span data-stu-id="92e07-143">That being said, what if for whatever reason you can't run tests against something close to your production database system?</span></span>
<span data-ttu-id="92e07-144">次によい選択肢は、同様の機能を持つものを使用することです。</span><span class="sxs-lookup"><span data-stu-id="92e07-144">The next best choice is to use something with similar functionality.</span></span>
<span data-ttu-id="92e07-145">これは通常、別のリレーショナル データベースを使用することを意味し、[SQLite](https://sqlite.org/index.html) が選択肢であることは明らかです。</span><span class="sxs-lookup"><span data-stu-id="92e07-145">This usually means another relational database, for which [SQLite](https://sqlite.org/index.html) is the obvious choice.</span></span>

<span data-ttu-id="92e07-146">SQLite が選択肢として適切であるのは次のためです。</span><span class="sxs-lookup"><span data-stu-id="92e07-146">SQLite is a good choice because:</span></span>
* <span data-ttu-id="92e07-147">お使いのアプリケーションでインプロセスで実行されるため、オーバーヘッドが低いです。</span><span class="sxs-lookup"><span data-stu-id="92e07-147">It runs in-process with your application and so has low overhead.</span></span>
* <span data-ttu-id="92e07-148">単純で自動作成されたファイルをデータベースに使用するため、データベース管理が不要です。</span><span class="sxs-lookup"><span data-stu-id="92e07-148">It uses simple, automatically created files for databases, and so doesn't require database management.</span></span>
* <span data-ttu-id="92e07-149">ファイルの作成さえをも回避するインメモリ モードがあります。</span><span class="sxs-lookup"><span data-stu-id="92e07-149">It has an in-memory mode that avoids even the file creation.</span></span>

<span data-ttu-id="92e07-150">ただし、次の点に注意してください。</span><span class="sxs-lookup"><span data-stu-id="92e07-150">However, remember that:</span></span>
* <span data-ttu-id="92e07-151">SQLite は当然、お使いの実稼働データベース システムで実行されるすべての機能をサポートしているわけではありません。</span><span class="sxs-lookup"><span data-stu-id="92e07-151">SQLite inevitability doesn't support everything that your production database system does.</span></span>
* <span data-ttu-id="92e07-152">SQLite は、一部のクエリではお使いの実稼働データベース システムと異なる動作をします。</span><span class="sxs-lookup"><span data-stu-id="92e07-152">SQLite will behave differently than your production database system for some queries.</span></span>

<span data-ttu-id="92e07-153">そのため、何らかのテストに SQLite を使用する場合は、お使いの実稼働データベース システムでも必ずテストをする必要があります。</span><span class="sxs-lookup"><span data-stu-id="92e07-153">So if you do use SQLite for some testing, make sure to also test against your real database system.</span></span>

<span data-ttu-id="92e07-154">EF Core 固有のガイダンスについては、「[SQLite を使用したテスト](xref:core/miscellaneous/testing/sqlite)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="92e07-154">See [Testing with SQLite](xref:core/miscellaneous/testing/sqlite) for EF Core specific guidance.</span></span> 

## <a name="approach-3-the-ef-core-in-memory-database"></a><span data-ttu-id="92e07-155">アプローチ 3:EF Core のインメモリ データベース</span><span class="sxs-lookup"><span data-stu-id="92e07-155">Approach 3: The EF Core in-memory database</span></span>

<span data-ttu-id="92e07-156">EF Core には、EF Core 自体の内部テストに使用するインメモリ データベースが付属しています。</span><span class="sxs-lookup"><span data-stu-id="92e07-156">EF Core comes with an in-memory database that we use for internal testing of EF Core itself.</span></span>
<span data-ttu-id="92e07-157">このデータベースは、**EF Core を使用するテスト アプリケーションの代わりには一般に適していません**。</span><span class="sxs-lookup"><span data-stu-id="92e07-157">This database is in general **not suitable as a substitute for testing applications that use EF Core**.</span></span> <span data-ttu-id="92e07-158">具体的には、次のように使用します。</span><span class="sxs-lookup"><span data-stu-id="92e07-158">Specifically:</span></span>
* <span data-ttu-id="92e07-159">リレーショナル データベースでない</span><span class="sxs-lookup"><span data-stu-id="92e07-159">It is not a relational database</span></span>
* <span data-ttu-id="92e07-160">トランザクションをサポートしない</span><span class="sxs-lookup"><span data-stu-id="92e07-160">It doesn't support transactions</span></span>
* <span data-ttu-id="92e07-161">パフォーマンスが最適化されていない</span><span class="sxs-lookup"><span data-stu-id="92e07-161">It is not optimized for performance</span></span>

<span data-ttu-id="92e07-162">EF Core 内部をテストする際には、これをテストにデータベースが関係ないときに特に使用するため、これらのいずれも重要ではありません。</span><span class="sxs-lookup"><span data-stu-id="92e07-162">None of this is very important when testing EF Core internals because we use it specifically where the database is irrelevant to the test.</span></span>
<span data-ttu-id="92e07-163">一方、EF Core を使用するアプリケーションをテストする場合、これらが非常に重要になる場合があります。</span><span class="sxs-lookup"><span data-stu-id="92e07-163">On the other hand, these things tend to be very important when testing an application that uses EF Core.</span></span>

## <a name="unit-testing"></a><span data-ttu-id="92e07-164">単体テスト</span><span class="sxs-lookup"><span data-stu-id="92e07-164">Unit testing</span></span>

<span data-ttu-id="92e07-165">データベースの一部のデータは使用するが、データベースの相互作用を実際にはテストしないビジネス ロジックをテストするとします。</span><span class="sxs-lookup"><span data-stu-id="92e07-165">Consider testing a piece of business logic that might need to use some data from a database, but is not inherently testing the database interactions.</span></span>
<span data-ttu-id="92e07-166">これの方法の 1 つに、モックやフェイクなど、[テスト ダブル](https://en.wikipedia.org/wiki/Test_double)を使用する方法があります。</span><span class="sxs-lookup"><span data-stu-id="92e07-166">One option is to use a [test double](https://en.wikipedia.org/wiki/Test_double) such as a mock or fake.</span></span>

<span data-ttu-id="92e07-167">テスト ダブルは、EF Core の内部テストで使用します。</span><span class="sxs-lookup"><span data-stu-id="92e07-167">We use test doubles for internal testing of EF Core.</span></span>
<span data-ttu-id="92e07-168">ただし、DbContext または IQueryable をモックすることはありません。</span><span class="sxs-lookup"><span data-stu-id="92e07-168">However, we never try to mock DbContext or IQueryable.</span></span>
<span data-ttu-id="92e07-169">これを行うのは困難で、煩雑で、不安定です。</span><span class="sxs-lookup"><span data-stu-id="92e07-169">Doing so is difficult, cumbersome, and fragile.</span></span>
<span data-ttu-id="92e07-170">**これは行わないでください。**</span><span class="sxs-lookup"><span data-stu-id="92e07-170">**Don't do it.**</span></span>

<span data-ttu-id="92e07-171">代わりに、DbContext を使用するものの単体テストには、インメモリ データベースを使用します。</span><span class="sxs-lookup"><span data-stu-id="92e07-171">Instead we use the in-memory database when unit testing something that uses DbContext.</span></span>
<span data-ttu-id="92e07-172">この場合、テストがデータベースの動作に依存しないため、インメモリ データベースを使用するのが適切です。</span><span class="sxs-lookup"><span data-stu-id="92e07-172">In this case using the in-memory database is appropriate because the test is not dependent on database behavior.</span></span>
<span data-ttu-id="92e07-173">実際のデータベース クエリや更新プログラムをテストには、これを行わないでください。</span><span class="sxs-lookup"><span data-stu-id="92e07-173">Just don't do this to test actual database queries or updates.</span></span>   

<span data-ttu-id="92e07-174">単体テストでのインメモリ データベースの使用に関する EF Core 固有のガイダンスについては、[インメモリ プロバイダーを使用したテスト](xref:core/miscellaneous/testing/in-memory)に関する記事を参照してください。</span><span class="sxs-lookup"><span data-stu-id="92e07-174">See [Testing with the in-memory provider](xref:core/miscellaneous/testing/in-memory) for EF Core specific guidance on using the in-memory database for unit testing.</span></span>
