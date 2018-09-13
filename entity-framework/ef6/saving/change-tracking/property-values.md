---
title: プロパティ値 - EF6 の使用
author: divega
ms.date: 10/23/2016
ms.assetid: e3278b4b-9378-4fdb-923d-f64d80aaae70
ms.openlocfilehash: 97902021a671dea9854a365dc2f10eaecb9e5ab8
ms.sourcegitcommit: 2b787009fd5be5627f1189ee396e708cd130e07b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2018
ms.locfileid: "45488836"
---
# <a name="working-with-property-values"></a><span data-ttu-id="339ba-102">プロパティ値の使用</span><span class="sxs-lookup"><span data-stu-id="339ba-102">Working with property values</span></span>
<span data-ttu-id="339ba-103">ほとんどの場合 Entity Framework は、状態、元の値と、エンティティ インスタンスのプロパティの現在の値を追跡の考慮されます。</span><span class="sxs-lookup"><span data-stu-id="339ba-103">For the most part Entity Framework will take care of tracking the state, original values, and current values of the properties of your entity instances.</span></span> <span data-ttu-id="339ba-104">ただし、表示または EF がプロパティに関する情報を操作する場合の切断されたシナリオ - などがあります。</span><span class="sxs-lookup"><span data-stu-id="339ba-104">However, there may be some cases - such as disconnected scenarios - where you want to view or manipulate the information EF has about the properties.</span></span> <span data-ttu-id="339ba-105">このトピックで紹介するテクニックは、Code First および EF Designer で作成されたモデルに等しく使用できます。</span><span class="sxs-lookup"><span data-stu-id="339ba-105">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

<span data-ttu-id="339ba-106">Entity Framework は、追跡対象エンティティの各プロパティの 2 つの値を追跡します。</span><span class="sxs-lookup"><span data-stu-id="339ba-106">Entity Framework keeps track of two values for each property of a tracked entity.</span></span> <span data-ttu-id="339ba-107">現在の値は、その名前が示すエンティティのプロパティの現在の値です。</span><span class="sxs-lookup"><span data-stu-id="339ba-107">The current value is, as the name indicates, the current value of the property in the entity.</span></span> <span data-ttu-id="339ba-108">元の値には、プロパティがエンティティをデータベースからクエリを実行またはコンテキストにアタッチしたときに、値です。</span><span class="sxs-lookup"><span data-stu-id="339ba-108">The original value is the value that the property had when the entity was queried from the database or attached to the context.</span></span>  

<span data-ttu-id="339ba-109">プロパティの値を操作するための 2 つの一般的なメカニズムがあります。</span><span class="sxs-lookup"><span data-stu-id="339ba-109">There are two general mechanisms for working with property values:</span></span>  

- <span data-ttu-id="339ba-110">プロパティ メソッドを使用して厳密に型指定された方法では、1 つのプロパティの値を取得できます。</span><span class="sxs-lookup"><span data-stu-id="339ba-110">The value of a single property can be obtained in a strongly typed way using the Property method.</span></span>  
- <span data-ttu-id="339ba-111">DbPropertyValues オブジェクトには、エンティティのすべてのプロパティの値を読み取ることができます。</span><span class="sxs-lookup"><span data-stu-id="339ba-111">Values for all properties of an entity can be read into a DbPropertyValues object.</span></span> <span data-ttu-id="339ba-112">DbPropertyValues し、読み取り、設定のプロパティの値を許可するディクショナリに似たオブジェクトとしては機能します。</span><span class="sxs-lookup"><span data-stu-id="339ba-112">DbPropertyValues then acts as a dictionary-like object to allow property values to be read and set.</span></span> <span data-ttu-id="339ba-113">DbPropertyValues の別のオブジェクトの値とは、エンティティまたは単純なデータ転送オブジェクト (DTO) の別のコピーなど、他のオブジェクトの値から、DbPropertyValues オブジェクト内の値を設定できます。</span><span class="sxs-lookup"><span data-stu-id="339ba-113">The values in a DbPropertyValues object can be set from values in another DbPropertyValues object or from values in some other object, such as another copy of the entity or a simple data transfer object (DTO).</span></span>  

<span data-ttu-id="339ba-114">以下のセクションでは、上記のメカニズムの両方を使用しての例を紹介します。</span><span class="sxs-lookup"><span data-stu-id="339ba-114">The sections below show examples of using both of the above mechanisms.</span></span>  

## <a name="getting-and-setting-the-current-or-original-value-of-an-individual-property"></a><span data-ttu-id="339ba-115">取得と個々 のプロパティの現在または元の値の設定</span><span class="sxs-lookup"><span data-stu-id="339ba-115">Getting and setting the current or original value of an individual property</span></span>  

<span data-ttu-id="339ba-116">次の例では、プロパティの現在の値を読み取り、新しい値を設定する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="339ba-116">The example below shows how the current value of a property can be read and then set to a new value:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(3);

    // Read the current value of the Name property
    string currentName1 = context.Entry(blog).Property(u => u.Name).CurrentValue;

    // Set the Name property to a new value
    context.Entry(name).Property(u => u.Name).CurrentValue = "My Fancy Blog";

    // Read the current value of the Name property using a string for the property name
    object currentName2 = context.Entry(blog).Property("Name").CurrentValue;

    // Set the Name property to a new value using a string for the property name
    context.Entry(blog).Property("Name").CurrentValue = "My Boring Blog";
}
```  

<span data-ttu-id="339ba-117">CurrentValue プロパティではなく OriginalValue プロパティを使用して、参照したり、元の値を設定します。</span><span class="sxs-lookup"><span data-stu-id="339ba-117">Use the OriginalValue property instead of the CurrentValue property to read or set the original value.</span></span>  

<span data-ttu-id="339ba-118">プロパティ名を指定する文字列を使用すると、「オブジェクト」として返される値を入力することに注意してください。</span><span class="sxs-lookup"><span data-stu-id="339ba-118">Note that the returned value is typed as “object” when a string is used to specify the property name.</span></span> <span data-ttu-id="339ba-119">その一方で、ラムダ式を使用する場合は、返される値は厳密に型指定します。</span><span class="sxs-lookup"><span data-stu-id="339ba-119">On the other hand, the returned value is strongly typed if a lambda expression is used.</span></span>  

<span data-ttu-id="339ba-120">このようなプロパティ値を設定しても、新しい値が古い値と異なる場合は変更と、プロパティをマークのみされます。</span><span class="sxs-lookup"><span data-stu-id="339ba-120">Setting the property value like this will only mark the property as modified if the new value is different from the old value.</span></span>  

<span data-ttu-id="339ba-121">この方法でプロパティ値が設定されている場合 AutoDetectChanges がになっている場合でも、変更が自動的に検出します。</span><span class="sxs-lookup"><span data-stu-id="339ba-121">When a property value is set in this way the change is automatically detected even if AutoDetectChanges is turned off.</span></span>  

## <a name="getting-and-setting-the-current-value-of-an-unmapped-property"></a><span data-ttu-id="339ba-122">取得と、マップされていないプロパティの現在の値の設定</span><span class="sxs-lookup"><span data-stu-id="339ba-122">Getting and setting the current value of an unmapped property</span></span>  

<span data-ttu-id="339ba-123">データベースにマップされていないプロパティの現在の値が読み込むこともできます。</span><span class="sxs-lookup"><span data-stu-id="339ba-123">The current value of a property that is not mapped to the database can also be read.</span></span> <span data-ttu-id="339ba-124">マップされていないプロパティの例には、ブログ、RssLink プロパティ可能性があります。</span><span class="sxs-lookup"><span data-stu-id="339ba-124">An example of an unmapped property could be an RssLink property on Blog.</span></span> <span data-ttu-id="339ba-125">この値は、BlogId に基づいて計算して、そのため、データベースに格納する必要があります。</span><span class="sxs-lookup"><span data-stu-id="339ba-125">This value may be calculated based on the BlogId, and therefore doesn't need to be stored in the database.</span></span> <span data-ttu-id="339ba-126">例えば:</span><span class="sxs-lookup"><span data-stu-id="339ba-126">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    // Read the current value of an unmapped property
    var rssLink = context.Entry(blog).Property(p => p.RssLink).CurrentValue;

    // Use a string to specify the property name
    var rssLinkAgain = context.Entry(blog).Property("RssLink").CurrentValue;
}
```  

<span data-ttu-id="339ba-127">現在の値は、プロパティ setter を公開している場合にも設定できます。</span><span class="sxs-lookup"><span data-stu-id="339ba-127">The current value can also be set if the property exposes a setter.</span></span>  

<span data-ttu-id="339ba-128">マップされていないプロパティの Entity Framework の検証を実行するときに、マップされていないプロパティの値を読み取ることは便利です。</span><span class="sxs-lookup"><span data-stu-id="339ba-128">Reading the values of unmapped properties is useful when performing Entity Framework validation of unmapped properties.</span></span> <span data-ttu-id="339ba-129">同様の理由には、現在の値を読み取りし、いない現在追跡されているコンテキストによってエンティティのプロパティを設定することができます。</span><span class="sxs-lookup"><span data-stu-id="339ba-129">For the same reason current values can be read and set for properties of entities that are not currently being tracked by the context.</span></span> <span data-ttu-id="339ba-130">例えば:</span><span class="sxs-lookup"><span data-stu-id="339ba-130">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    // Create an entity that is not being tracked
    var blog = new Blog { Name = "ADO.NET Blog" };

    // Read and set the current value of Name as before
    var currentName1 = context.Entry(blog).Property(u => u.Name).CurrentValue;
    context.Entry(blog).Property(u => u.Name).CurrentValue = "My Fancy Blog";
    var currentName2 = context.Entry(blog).Property("Name").CurrentValue;
    context.Entry(blog).Property("Name").CurrentValue = "My Boring Blog";
}
```  

<span data-ttu-id="339ba-131">元の値がマップされていないプロパティやいないコンテキストによって追跡されているエンティティのプロパティには使用できないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="339ba-131">Note that original values are not available for unmapped properties or for properties of entities that are not being tracked by the context.</span></span>  

## <a name="checking-whether-a-property-is-marked-as-modified"></a><span data-ttu-id="339ba-132">プロパティが変更済みとしてマークされているかどうかをチェックします。</span><span class="sxs-lookup"><span data-stu-id="339ba-132">Checking whether a property is marked as modified</span></span>  

<span data-ttu-id="339ba-133">次の例では、個々 のプロパティが変更済みとしてマークされているかどうかをチェックする方法を示します。</span><span class="sxs-lookup"><span data-stu-id="339ba-133">The example below shows how to check whether or not an individual property is marked as modified:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    var nameIsModified1 = context.Entry(blog).Property(u => u.Name).IsModified;

    // Use a string for the property name
    var nameIsModified2 = context.Entry(blog).Property("Name").IsModified;
}
```  

<span data-ttu-id="339ba-134">変更されたプロパティの値は、SaveChanges が呼び出されたときに更新プログラムとして、データベースに送信されます。</span><span class="sxs-lookup"><span data-stu-id="339ba-134">The values of modified properties are sent as updates to the database when SaveChanges is called.</span></span>  

##  <a name="marking-a-property-as-modified"></a><span data-ttu-id="339ba-135">プロパティ変更済みとしてマークします。</span><span class="sxs-lookup"><span data-stu-id="339ba-135">Marking a property as modified</span></span>  

<span data-ttu-id="339ba-136">次の例では、変更済みとしてマークする個々 のプロパティを強制する方法を示します。</span><span class="sxs-lookup"><span data-stu-id="339ba-136">The example below shows how to force an individual property to be marked as modified:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    context.Entry(blog).Property(u => u.Name).IsModified = true;

    // Use a string for the property name
    context.Entry(blog).Property("Name").IsModified = true;
}
```  

<span data-ttu-id="339ba-137">変更の強制を更新するのには、プロパティの現在の値が元の値と同じ場合でも、SaveChanges が呼び出されたときに、プロパティのデータベースに送信プロパティをマークします。</span><span class="sxs-lookup"><span data-stu-id="339ba-137">Marking a property as modified forces an update to be send to the database for the property when SaveChanges is called even if the current value of the property is the same as its original value.</span></span>  

<span data-ttu-id="339ba-138">現在では、後は変更済みとしてマークされていない変更する個々 のプロパティをリセットすることはできません。</span><span class="sxs-lookup"><span data-stu-id="339ba-138">It is not currently possible to reset an individual property to be not modified after it has been marked as modified.</span></span> <span data-ttu-id="339ba-139">これは将来のリリースでサポートする予定です。</span><span class="sxs-lookup"><span data-stu-id="339ba-139">This is something we plan to support in a future release.</span></span>  

## <a name="reading-current-original-and-database-values-for-all-properties-of-an-entity"></a><span data-ttu-id="339ba-140">現在、元のデータベース エンティティのすべてのプロパティの値の読み取り</span><span class="sxs-lookup"><span data-stu-id="339ba-140">Reading current, original, and database values for all properties of an entity</span></span>  

<span data-ttu-id="339ba-141">次の例では、現在の値、元の値、およびエンティティのマップされたプロパティのすべてのデータベースの実際の値を読み取る方法を示します。</span><span class="sxs-lookup"><span data-stu-id="339ba-141">The example below shows how to read the current values, the original values, and the values actually in the database for all mapped properties of an entity.</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    // Make a modification to Name in the tracked entity
    blog.Name = "My Cool Blog";

    // Make a modification to Name in the database
    context.Database.SqlCommand("update dbo.Blogs set Name = 'My Boring Blog' where Id = 1");

    // Print out current, original, and database values
    Console.WriteLine("Current values:");
    PrintValues(context.Entry(blog).CurrentValues);

    Console.WriteLine("\nOriginal values:");
    PrintValues(context.Entry(blog).OriginalValues);

    Console.WriteLine("\nDatabase values:");
    PrintValues(context.Entry(blog).GetDatabaseValues());
}

public static void PrintValues(DbPropertyValues values)
{
    foreach (var propertyName in values.PropertyNames)
    {
        Console.WriteLine("Property {0} has value {1}",
                          propertyName, values[propertyName]);
    }
}
```  

<span data-ttu-id="339ba-142">現在の値は、現在のエンティティのプロパティが含まれている値です。</span><span class="sxs-lookup"><span data-stu-id="339ba-142">The current values are the values that the properties of the entity currently contain.</span></span> <span data-ttu-id="339ba-143">元の値は、エンティティが照会されたときに、データベースから読み取られた値です。</span><span class="sxs-lookup"><span data-stu-id="339ba-143">The original values are the values that were read from the database when the entity was queried.</span></span> <span data-ttu-id="339ba-144">データベースの値は、データベースに現在格納されている値です。</span><span class="sxs-lookup"><span data-stu-id="339ba-144">The database values are the values as they are currently stored in the database.</span></span> <span data-ttu-id="339ba-145">別のユーザーによって、データベースを加え、同時実行を編集するときなどに、エンティティのクエリが実行されましたので、データベースの値が変更された可能性がありますがある場合は、データベースの値を取得すると便利です。</span><span class="sxs-lookup"><span data-stu-id="339ba-145">Getting the database values is useful when the values in the database may have changed since the entity was queried such as when a concurrent edit to the database has been made by another user.</span></span>  

## <a name="setting-current-or-original-values-from-another-object"></a><span data-ttu-id="339ba-146">別のオブジェクトから現在または元の値の設定</span><span class="sxs-lookup"><span data-stu-id="339ba-146">Setting current or original values from another object</span></span>  

<span data-ttu-id="339ba-147">別のオブジェクトから値をコピーして、追跡対象エンティティの現在または元の値を更新できます。</span><span class="sxs-lookup"><span data-stu-id="339ba-147">The current or original values of a tracked entity can be updated by copying values from another object.</span></span> <span data-ttu-id="339ba-148">例えば:</span><span class="sxs-lookup"><span data-stu-id="339ba-148">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);
    var coolBlog = new Blog { Id = 1, Name = "My Cool Blog" };
    var boringBlog = new BlogDto { Id = 1, Name = "My Boring Blog" };

    // Change the current and original values by copying the values from other objects
    var entry = context.Entry(blog);
    entry.CurrentValues.SetValues(coolBlog);
    entry.OriginalValues.SetValues(boringBlog);

    // Print out current and original values
    Console.WriteLine("Current values:");
    PrintValues(entry.CurrentValues);

    Console.WriteLine("\nOriginal values:");
    PrintValues(entry.OriginalValues);
}

public class BlogDto
{
    public int Id { get; set; }
    public string Name { get; set; }
}
```  

<span data-ttu-id="339ba-149">上記のコードを実行しているが表示されます。</span><span class="sxs-lookup"><span data-stu-id="339ba-149">Running the code above will print out:</span></span>  

```  
Current values:
Property Id has value 1
Property Name has value My Cool Blog

Original values:
Property Id has value 1
Property Name has value My Boring Blog
```  

<span data-ttu-id="339ba-150">エンティティをサービスの呼び出しまたは n 層アプリケーションでクライアントから取得した値に更新する場合は、この方法は使用こともあります。</span><span class="sxs-lookup"><span data-stu-id="339ba-150">This technique is sometimes used when updating an entity with values obtained from a service call or a client in an n-tier application.</span></span> <span data-ttu-id="339ba-151">名前に一致するエンティティのプロパティがある限り、エンティティと同じ型であるために使用するオブジェクトがないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="339ba-151">Note that the object used does not have to be of the same type as the entity so long as it has properties whose names match those of the entity.</span></span> <span data-ttu-id="339ba-152">上記の例では、BlogDTO のインスタンスが元の値を更新するために使用します。</span><span class="sxs-lookup"><span data-stu-id="339ba-152">In the example above, an instance of BlogDTO is used to update the original values.</span></span>  

<span data-ttu-id="339ba-153">変更済みとに、その他のオブジェクトからコピーするときに異なる値に設定されているプロパティのみをマークすることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="339ba-153">Note that only properties that are set to different values when copied from the other object will be marked as modified.</span></span>  

## <a name="setting-current-or-original-values-from-a-dictionary"></a><span data-ttu-id="339ba-154">ディクショナリの現在または元の値の設定</span><span class="sxs-lookup"><span data-stu-id="339ba-154">Setting current or original values from a dictionary</span></span>  

<span data-ttu-id="339ba-155">追跡対象エンティティの現在または元の値は、ディクショナリまたはその他のいくつかのデータ構造体から値をコピーすることで更新できます。</span><span class="sxs-lookup"><span data-stu-id="339ba-155">The current or original values of a tracked entity can be updated by copying values from a dictionary or some other data structure.</span></span> <span data-ttu-id="339ba-156">例えば:</span><span class="sxs-lookup"><span data-stu-id="339ba-156">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    var newValues = new Dictionary\<string, object>
    {
        { "Name", "The New ADO.NET Blog" },
        { "Url", "blogs.msdn.com/adonet" },
    };

    var currentValues = context.Entry(blog).CurrentValues;

    foreach (var propertyName in newValues.Keys)
    {
        currentValues[propertyName] = newValues[propertyName];
    }

    PrintValues(currentValues);
}
```  

<span data-ttu-id="339ba-157">CurrentValues プロパティの代わりに行っていましたプロパティを使用して、元の値を設定します。</span><span class="sxs-lookup"><span data-stu-id="339ba-157">Use the OriginalValues property instead of the CurrentValues property to set original values.</span></span>  

## <a name="setting-current-or-original-values-from-a-dictionary-using-property"></a><span data-ttu-id="339ba-158">プロパティを使用してディクショナリから現在または元の値の設定</span><span class="sxs-lookup"><span data-stu-id="339ba-158">Setting current or original values from a dictionary using Property</span></span>  

<span data-ttu-id="339ba-159">CurrentValues または上記のように行っていましたを使用する代わりにでは、各プロパティの値を設定するプロパティのメソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="339ba-159">An alternative to using CurrentValues or OriginalValues as shown above is to use the Property method to set the value of each property.</span></span> <span data-ttu-id="339ba-160">これは、複雑なプロパティの値を設定する必要がある場合ことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="339ba-160">This can be preferable when you need to set the values of complex properties.</span></span> <span data-ttu-id="339ba-161">例えば:</span><span class="sxs-lookup"><span data-stu-id="339ba-161">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var user = context.Users.Find("johndoe1987");

    var newValues = new Dictionary\<string, object>
    {
        { "Name", "John Doe" },
        { "Location.City", "Redmond" },
        { "Location.State.Name", "Washington" },
        { "Location.State.Code", "WA" },
    };

    var entry = context.Entry(user);

    foreach (var propertyName in newValues.Keys)
    {
        entry.Property(propertyName).CurrentValue = newValues[propertyName];
    }
}
```  

<span data-ttu-id="339ba-162">複雑なプロパティを上記の例ではアクセス ドット形式の名前を使用します。</span><span class="sxs-lookup"><span data-stu-id="339ba-162">In the example above complex properties are accessed using dotted names.</span></span> <span data-ttu-id="339ba-163">複雑なプロパティにアクセスするには、その他の方法は、複雑なプロパティについて具体的には、このトピックの後半の 2 つのセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="339ba-163">For other ways to access complex properties see the two sections later in this topic specifically about complex properties.</span></span>  

## <a name="creating-a-cloned-object-containing-current-original-or-database-values"></a><span data-ttu-id="339ba-164">現在、元のパスまたはデータベースの値を格納している複製されたオブジェクトを作成します。</span><span class="sxs-lookup"><span data-stu-id="339ba-164">Creating a cloned object containing current, original, or database values</span></span>  

<span data-ttu-id="339ba-165">CurrentValues、行っていましたから返された DbPropertyValues オブジェクトまたはでは、エンティティの複製の作成に使用できます。</span><span class="sxs-lookup"><span data-stu-id="339ba-165">The DbPropertyValues object returned from CurrentValues, OriginalValues, or GetDatabaseValues can be used to create a clone of the entity.</span></span> <span data-ttu-id="339ba-166">この複製を作成するために使用した DbPropertyValues オブジェクトからプロパティ値が含まれます。</span><span class="sxs-lookup"><span data-stu-id="339ba-166">This clone will contain the property values from the DbPropertyValues object used to create it.</span></span> <span data-ttu-id="339ba-167">例えば:</span><span class="sxs-lookup"><span data-stu-id="339ba-167">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    var clonedBlog = context.Entry(blog).GetDatabaseValues().ToObject();
}
```  

<span data-ttu-id="339ba-168">返されるオブジェクトがエンティティでないし、コンテキストによって追跡されていませんことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="339ba-168">Note that the object returned is not the entity and is not being tracked by the context.</span></span> <span data-ttu-id="339ba-169">また、返されたオブジェクトには、リレーションシップのセットを他のオブジェクトにはありません。</span><span class="sxs-lookup"><span data-stu-id="339ba-169">The returned object also does not have any relationships set to other objects.</span></span>  

<span data-ttu-id="339ba-170">複製されたオブジェクトは、特に、特定の種類のオブジェクトへのデータ バインディングを含む UI が使用されている、データベースの同時実行更新に関連する問題を解決するために役立ちます。</span><span class="sxs-lookup"><span data-stu-id="339ba-170">The cloned object can be useful for resolving issues related to concurrent updates to the database, especially where a UI that involves data binding to objects of a certain type is being used.</span></span>  

## <a name="getting-and-setting-the-current-or-original-values-of-complex-properties"></a><span data-ttu-id="339ba-171">取得と複雑なプロパティの現在または元の値の設定</span><span class="sxs-lookup"><span data-stu-id="339ba-171">Getting and setting the current or original values of complex properties</span></span>  

<span data-ttu-id="339ba-172">全体の複雑なオブジェクトの値には、読み取りとプリミティブ プロパティのことができ、同様、プロパティ メソッドを使用してセットを指定できます。</span><span class="sxs-lookup"><span data-stu-id="339ba-172">The value of an entire complex object can be read and set using the Property method just as it can be for a primitive property.</span></span> <span data-ttu-id="339ba-173">さらに複雑なオブジェクトと、そのオブジェクトまたは入れ子になったオブジェクトもの読み取りや設定のプロパティにドリルダウンすることができます。</span><span class="sxs-lookup"><span data-stu-id="339ba-173">In addition you can drill down into the complex object and read or set properties of that object, or even a nested object.</span></span> <span data-ttu-id="339ba-174">次にいくつかの例を示します。</span><span class="sxs-lookup"><span data-stu-id="339ba-174">Here are some examples:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var user = context.Users.Find("johndoe1987");

    // Get the Location complex object
    var location = context.Entry(user)
                       .Property(u => u.Location)
                       .CurrentValue;

    // Get the nested State complex object using chained calls
    var state1 = context.Entry(user)
                     .ComplexProperty(u => u.Location)
                     .Property(l => l.State)
                     .CurrentValue;

    // Get the nested State complex object using a single lambda expression
    var state2 = context.Entry(user)
                     .Property(u => u.Location.State)
                     .CurrentValue;

    // Get the nested State complex object using a dotted string
    var state3 = context.Entry(user)
                     .Property("Location.State")
                     .CurrentValue;

    // Get the value of the Name property on the nested State complex object using chained calls
    var name1 = context.Entry(user)
                       .ComplexProperty(u => u.Location)
                       .ComplexProperty(l => l.State)
                       .Property(s => s.Name)
                       .CurrentValue;

    // Get the value of the Name property on the nested State complex object using a single lambda expression
    var name2 = context.Entry(user)
                       .Property(u => u.Location.State.Name)
                       .CurrentValue;

    // Get the value of the Name property on the nested State complex object using a dotted string
    var name3 = context.Entry(user)
                       .Property("Location.State.Name")
                       .CurrentValue;
}
```  

<span data-ttu-id="339ba-175">CurrentValue プロパティではなく OriginalValue プロパティを使用して、元の値取得または設定します。</span><span class="sxs-lookup"><span data-stu-id="339ba-175">Use the OriginalValue property instead of the CurrentValue property to get or set an original value.</span></span>  

<span data-ttu-id="339ba-176">プロパティまたは ComplexProperty メソッドのいずれかを複合プロパティへのアクセスに使用できることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="339ba-176">Note that either the Property or the ComplexProperty method can be used to access a complex property.</span></span> <span data-ttu-id="339ba-177">ただし、追加のプロパティを持つ複雑なオブジェクトにドリル ダウンするまたは ComplexProperty を呼び出す場合は、ComplexProperty メソッドを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="339ba-177">However, the ComplexProperty method must be used if you wish to drill down into the complex object with additional Property or ComplexProperty calls.</span></span>  

## <a name="using-dbpropertyvalues-to-access-complex-properties"></a><span data-ttu-id="339ba-178">DbPropertyValues を使用して複雑なプロパティにアクセスするには</span><span class="sxs-lookup"><span data-stu-id="339ba-178">Using DbPropertyValues to access complex properties</span></span>  

<span data-ttu-id="339ba-179">ときに、現在のすべてを取得する CurrentValues、行っていました、またはでを使用することもエンティティには、データベースの値、複雑なプロパティの値は、入れ子になった DbPropertyValues オブジェクトとして返されます。</span><span class="sxs-lookup"><span data-stu-id="339ba-179">When you use CurrentValues, OriginalValues, or GetDatabaseValues to get all the current, original, or database values for an entity, the values of any complex properties are returned as nested DbPropertyValues objects.</span></span> <span data-ttu-id="339ba-180">これらのオブジェクトが入れ子になったし、複雑なオブジェクトの値を取得するために使用します。</span><span class="sxs-lookup"><span data-stu-id="339ba-180">These nested objects can then be used to get values of the complex object.</span></span> <span data-ttu-id="339ba-181">たとえば、次のメソッドは、任意の複雑なプロパティと入れ子になった複雑なプロパティの値を含むすべてのプロパティの値が印刷されます。</span><span class="sxs-lookup"><span data-stu-id="339ba-181">For example, the following method will print out the values of all properties, including values of any complex properties and nested complex properties.</span></span>  

``` csharp
public static void WritePropertyValues(string parentPropertyName, DbPropertyValues propertyValues)
{
    foreach (var propertyName in propertyValues.PropertyNames)
    {
        var nestedValues = propertyValues[propertyName] as DbPropertyValues;
        if (nestedValues != null)
        {
            WritePropertyValues(parentPropertyName + propertyName + ".", nestedValues);
        }
        else
        {
            Console.WriteLine("Property {0}{1} has value {2}",
                              parentPropertyName, propertyName,
                              propertyValues[propertyName]);
        }
    }
}
```  

<span data-ttu-id="339ba-182">メソッドを現在のプロパティ値をすべて出力が次のように呼び出されます。</span><span class="sxs-lookup"><span data-stu-id="339ba-182">To print out all current property values the method would be called like this:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var user = context.Users.Find("johndoe1987");

    WritePropertyValues("", context.Entry(user).CurrentValues);
}
```  
