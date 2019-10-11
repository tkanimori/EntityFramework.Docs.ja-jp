---
title: プロパティ値の操作-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: e3278b4b-9378-4fdb-923d-f64d80aaae70
ms.openlocfilehash: d8a18182754980d79b71df3f227b30c4ce40366f
ms.sourcegitcommit: 708b18520321c587b2046ad2ea9fa7c48aeebfe5
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2019
ms.locfileid: "72182142"
---
# <a name="working-with-property-values"></a><span data-ttu-id="ebf8b-102">プロパティ値の操作</span><span class="sxs-lookup"><span data-stu-id="ebf8b-102">Working with property values</span></span>
<span data-ttu-id="ebf8b-103">ほとんどの場合 Entity Framework は、エンティティインスタンスのプロパティの状態、元の値、および現在の値の追跡を行います。</span><span class="sxs-lookup"><span data-stu-id="ebf8b-103">For the most part Entity Framework will take care of tracking the state, original values, and current values of the properties of your entity instances.</span></span> <span data-ttu-id="ebf8b-104">ただし、接続が切断されたシナリオなど、EF がプロパティについての情報を表示または操作する場合もあります。</span><span class="sxs-lookup"><span data-stu-id="ebf8b-104">However, there may be some cases - such as disconnected scenarios - where you want to view or manipulate the information EF has about the properties.</span></span> <span data-ttu-id="ebf8b-105">このトピックで紹介するテクニックは、Code First および EF Designer で作成されたモデルに等しく使用できます。</span><span class="sxs-lookup"><span data-stu-id="ebf8b-105">The techniques shown in this topic apply equally to models created with Code First and the EF Designer.</span></span>  

<span data-ttu-id="ebf8b-106">Entity Framework は、追跡対象のエンティティの各プロパティの2つの値を追跡します。</span><span class="sxs-lookup"><span data-stu-id="ebf8b-106">Entity Framework keeps track of two values for each property of a tracked entity.</span></span> <span data-ttu-id="ebf8b-107">現在の値は、名前が示すように、エンティティ内のプロパティの現在の値です。</span><span class="sxs-lookup"><span data-stu-id="ebf8b-107">The current value is, as the name indicates, the current value of the property in the entity.</span></span> <span data-ttu-id="ebf8b-108">元の値は、エンティティがデータベースから照会されたとき、またはコンテキストにアタッチされたときにプロパティに保持されていた値です。</span><span class="sxs-lookup"><span data-stu-id="ebf8b-108">The original value is the value that the property had when the entity was queried from the database or attached to the context.</span></span>  

<span data-ttu-id="ebf8b-109">プロパティ値を操作するには、次の2つの一般的なメカニズムがあります。</span><span class="sxs-lookup"><span data-stu-id="ebf8b-109">There are two general mechanisms for working with property values:</span></span>  

- <span data-ttu-id="ebf8b-110">1つのプロパティの値は、プロパティメソッドを使用して厳密に型指定された方法で取得できます。</span><span class="sxs-lookup"><span data-stu-id="ebf8b-110">The value of a single property can be obtained in a strongly typed way using the Property method.</span></span>  
- <span data-ttu-id="ebf8b-111">エンティティのすべてのプロパティの値は、DbPropertyValues オブジェクトに読み取ることができます。</span><span class="sxs-lookup"><span data-stu-id="ebf8b-111">Values for all properties of an entity can be read into a DbPropertyValues object.</span></span> <span data-ttu-id="ebf8b-112">DbPropertyValues は、プロパティ値を読み取って設定できるようにするために、ディクショナリに似たオブジェクトとして機能します。</span><span class="sxs-lookup"><span data-stu-id="ebf8b-112">DbPropertyValues then acts as a dictionary-like object to allow property values to be read and set.</span></span> <span data-ttu-id="ebf8b-113">DbPropertyValues オブジェクトの値は、別の DbPropertyValues オブジェクトの値、またはエンティティの別のコピーや単純なデータ転送オブジェクト (DTO) などの他のオブジェクトの値から設定できます。</span><span class="sxs-lookup"><span data-stu-id="ebf8b-113">The values in a DbPropertyValues object can be set from values in another DbPropertyValues object or from values in some other object, such as another copy of the entity or a simple data transfer object (DTO).</span></span>  

<span data-ttu-id="ebf8b-114">以下のセクションでは、上記の両方のメカニズムを使用する例を示します。</span><span class="sxs-lookup"><span data-stu-id="ebf8b-114">The sections below show examples of using both of the above mechanisms.</span></span>  

## <a name="getting-and-setting-the-current-or-original-value-of-an-individual-property"></a><span data-ttu-id="ebf8b-115">個々のプロパティの現在の値または元の値を取得および設定する</span><span class="sxs-lookup"><span data-stu-id="ebf8b-115">Getting and setting the current or original value of an individual property</span></span>  

<span data-ttu-id="ebf8b-116">次の例では、プロパティの現在の値を読み取って、新しい値を設定する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="ebf8b-116">The example below shows how the current value of a property can be read and then set to a new value:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(3);

    // Read the current value of the Name property
    string currentName1 = context.Entry(blog).Property(u => u.Name).CurrentValue;

    // Set the Name property to a new value
    context.Entry(blog).Property(u => u.Name).CurrentValue = "My Fancy Blog";

    // Read the current value of the Name property using a string for the property name
    object currentName2 = context.Entry(blog).Property("Name").CurrentValue;

    // Set the Name property to a new value using a string for the property name
    context.Entry(blog).Property("Name").CurrentValue = "My Boring Blog";
}
```  

<span data-ttu-id="ebf8b-117">元の値の読み取りまたは設定を行うには、CurrentValue プロパティの代わりに OriginalValue プロパティを使用します。</span><span class="sxs-lookup"><span data-stu-id="ebf8b-117">Use the OriginalValue property instead of the CurrentValue property to read or set the original value.</span></span>  

<span data-ttu-id="ebf8b-118">文字列を使用してプロパティ名を指定した場合、戻り値は "object" として入力されることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="ebf8b-118">Note that the returned value is typed as “object” when a string is used to specify the property name.</span></span> <span data-ttu-id="ebf8b-119">一方、ラムダ式が使用されている場合、戻り値は厳密に型指定されます。</span><span class="sxs-lookup"><span data-stu-id="ebf8b-119">On the other hand, the returned value is strongly typed if a lambda expression is used.</span></span>  

<span data-ttu-id="ebf8b-120">このようにプロパティ値を設定した場合、新しい値が古い値と異なる場合にのみ、プロパティが変更済みとしてマークされます。</span><span class="sxs-lookup"><span data-stu-id="ebf8b-120">Setting the property value like this will only mark the property as modified if the new value is different from the old value.</span></span>  

<span data-ttu-id="ebf8b-121">このようにプロパティ値が設定されている場合、自動検出の変更がオフになっていても、変更は自動的に検出されます。</span><span class="sxs-lookup"><span data-stu-id="ebf8b-121">When a property value is set in this way the change is automatically detected even if AutoDetectChanges is turned off.</span></span>  

## <a name="getting-and-setting-the-current-value-of-an-unmapped-property"></a><span data-ttu-id="ebf8b-122">マップされていないプロパティの現在の値の取得と設定</span><span class="sxs-lookup"><span data-stu-id="ebf8b-122">Getting and setting the current value of an unmapped property</span></span>  

<span data-ttu-id="ebf8b-123">データベースにマップされていないプロパティの現在の値も読み取ることができます。</span><span class="sxs-lookup"><span data-stu-id="ebf8b-123">The current value of a property that is not mapped to the database can also be read.</span></span> <span data-ttu-id="ebf8b-124">マップされていないプロパティの例としては、ブログの .Rsslink プロパティがあります。</span><span class="sxs-lookup"><span data-stu-id="ebf8b-124">An example of an unmapped property could be an RssLink property on Blog.</span></span> <span data-ttu-id="ebf8b-125">この値は、ブログ Id に基づいて計算される場合があるため、データベースに格納する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="ebf8b-125">This value may be calculated based on the BlogId, and therefore doesn't need to be stored in the database.</span></span> <span data-ttu-id="ebf8b-126">以下に例を示します。</span><span class="sxs-lookup"><span data-stu-id="ebf8b-126">For example:</span></span>  

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

<span data-ttu-id="ebf8b-127">プロパティが setter を公開している場合は、現在の値を設定することもできます。</span><span class="sxs-lookup"><span data-stu-id="ebf8b-127">The current value can also be set if the property exposes a setter.</span></span>  

<span data-ttu-id="ebf8b-128">マップされていないプロパティの値の読み取りは、マップされていないプロパティの Entity Framework 検証を実行する場合に便利です。</span><span class="sxs-lookup"><span data-stu-id="ebf8b-128">Reading the values of unmapped properties is useful when performing Entity Framework validation of unmapped properties.</span></span> <span data-ttu-id="ebf8b-129">同じ理由から、現在の値は、コンテキストによって現在追跡されていないエンティティのプロパティに対して読み取りおよび設定できます。</span><span class="sxs-lookup"><span data-stu-id="ebf8b-129">For the same reason current values can be read and set for properties of entities that are not currently being tracked by the context.</span></span> <span data-ttu-id="ebf8b-130">以下に例を示します。</span><span class="sxs-lookup"><span data-stu-id="ebf8b-130">For example:</span></span>  

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

<span data-ttu-id="ebf8b-131">マップされていないプロパティや、コンテキストによって追跡されていないエンティティのプロパティには、元の値を使用できないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="ebf8b-131">Note that original values are not available for unmapped properties or for properties of entities that are not being tracked by the context.</span></span>  

## <a name="checking-whether-a-property-is-marked-as-modified"></a><span data-ttu-id="ebf8b-132">プロパティが変更済みとしてマークされているかどうかを確認する</span><span class="sxs-lookup"><span data-stu-id="ebf8b-132">Checking whether a property is marked as modified</span></span>  

<span data-ttu-id="ebf8b-133">次の例は、個々のプロパティが変更済みとしてマークされているかどうかを確認する方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="ebf8b-133">The example below shows how to check whether or not an individual property is marked as modified:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    var nameIsModified1 = context.Entry(blog).Property(u => u.Name).IsModified;

    // Use a string for the property name
    var nameIsModified2 = context.Entry(blog).Property("Name").IsModified;
}
```  

<span data-ttu-id="ebf8b-134">変更されたプロパティの値は、SaveChanges が呼び出されたときにデータベースに更新として送信されます。</span><span class="sxs-lookup"><span data-stu-id="ebf8b-134">The values of modified properties are sent as updates to the database when SaveChanges is called.</span></span>  

##  <a name="marking-a-property-as-modified"></a><span data-ttu-id="ebf8b-135">プロパティを変更済みとしてマークする</span><span class="sxs-lookup"><span data-stu-id="ebf8b-135">Marking a property as modified</span></span>  

<span data-ttu-id="ebf8b-136">次の例は、個々のプロパティを強制的に変更済みとしてマークする方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="ebf8b-136">The example below shows how to force an individual property to be marked as modified:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    context.Entry(blog).Property(u => u.Name).IsModified = true;

    // Use a string for the property name
    context.Entry(blog).Property("Name").IsModified = true;
}
```  

<span data-ttu-id="ebf8b-137">プロパティを変更済みとしてマークすると、プロパティの現在の値が元の値と同じであっても SaveChanges が呼び出されたときに、そのプロパティのデータベースに更新が送信されます。</span><span class="sxs-lookup"><span data-stu-id="ebf8b-137">Marking a property as modified forces an update to be send to the database for the property when SaveChanges is called even if the current value of the property is the same as its original value.</span></span>  

<span data-ttu-id="ebf8b-138">現在、個々のプロパティが変更済みとしてマークされた後で変更されないようにリセットすることはできません。</span><span class="sxs-lookup"><span data-stu-id="ebf8b-138">It is not currently possible to reset an individual property to be not modified after it has been marked as modified.</span></span> <span data-ttu-id="ebf8b-139">これは、将来のリリースでサポートする予定です。</span><span class="sxs-lookup"><span data-stu-id="ebf8b-139">This is something we plan to support in a future release.</span></span>  

## <a name="reading-current-original-and-database-values-for-all-properties-of-an-entity"></a><span data-ttu-id="ebf8b-140">エンティティのすべてのプロパティの現在の値、元の値、およびデータベースの値の読み取り</span><span class="sxs-lookup"><span data-stu-id="ebf8b-140">Reading current, original, and database values for all properties of an entity</span></span>  

<span data-ttu-id="ebf8b-141">次の例では、エンティティのマップされたすべてのプロパティについて、現在の値、元の値、およびデータベース内の実際の値を読み取る方法を示しています。</span><span class="sxs-lookup"><span data-stu-id="ebf8b-141">The example below shows how to read the current values, the original values, and the values actually in the database for all mapped properties of an entity.</span></span>  

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

<span data-ttu-id="ebf8b-142">現在の値は、エンティティのプロパティに現在格納されている値です。</span><span class="sxs-lookup"><span data-stu-id="ebf8b-142">The current values are the values that the properties of the entity currently contain.</span></span> <span data-ttu-id="ebf8b-143">元の値は、エンティティが照会されたときにデータベースから読み取られた値です。</span><span class="sxs-lookup"><span data-stu-id="ebf8b-143">The original values are the values that were read from the database when the entity was queried.</span></span> <span data-ttu-id="ebf8b-144">データベースの値は、現在データベースに格納されている値です。</span><span class="sxs-lookup"><span data-stu-id="ebf8b-144">The database values are the values as they are currently stored in the database.</span></span> <span data-ttu-id="ebf8b-145">データベースの値を取得すると、データベースに対する同時編集が別のユーザーによって行われた場合など、エンティティがクエリされた後にデータベース内の値が変更された可能性があります。</span><span class="sxs-lookup"><span data-stu-id="ebf8b-145">Getting the database values is useful when the values in the database may have changed since the entity was queried such as when a concurrent edit to the database has been made by another user.</span></span>  

## <a name="setting-current-or-original-values-from-another-object"></a><span data-ttu-id="ebf8b-146">別のオブジェクトからの現在または元の値の設定</span><span class="sxs-lookup"><span data-stu-id="ebf8b-146">Setting current or original values from another object</span></span>  

<span data-ttu-id="ebf8b-147">追跡対象エンティティの現在の値または元の値は、別のオブジェクトから値をコピーすることによって更新できます。</span><span class="sxs-lookup"><span data-stu-id="ebf8b-147">The current or original values of a tracked entity can be updated by copying values from another object.</span></span> <span data-ttu-id="ebf8b-148">以下に例を示します。</span><span class="sxs-lookup"><span data-stu-id="ebf8b-148">For example:</span></span>  

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

<span data-ttu-id="ebf8b-149">上記のコードを実行すると、次の出力が出力されます。</span><span class="sxs-lookup"><span data-stu-id="ebf8b-149">Running the code above will print out:</span></span>  

```console
Current values:
Property Id has value 1
Property Name has value My Cool Blog

Original values:
Property Id has value 1
Property Name has value My Boring Blog
```  

<span data-ttu-id="ebf8b-150">この手法は、サービス呼び出しまたは n 層アプリケーションのクライアントから取得した値を使用してエンティティを更新するときに使用されることがあります。</span><span class="sxs-lookup"><span data-stu-id="ebf8b-150">This technique is sometimes used when updating an entity with values obtained from a service call or a client in an n-tier application.</span></span> <span data-ttu-id="ebf8b-151">使用されるオブジェクトは、エンティティと同じ型である必要はありません。ただし、エンティティの名前と名前が一致するプロパティがある場合に限ります。</span><span class="sxs-lookup"><span data-stu-id="ebf8b-151">Note that the object used does not have to be of the same type as the entity so long as it has properties whose names match those of the entity.</span></span> <span data-ttu-id="ebf8b-152">上記の例では、ブログ Dto のインスタンスを使用して、元の値を更新しています。</span><span class="sxs-lookup"><span data-stu-id="ebf8b-152">In the example above, an instance of BlogDTO is used to update the original values.</span></span>  

<span data-ttu-id="ebf8b-153">他のオブジェクトからコピーしたときに異なる値に設定されているプロパティのみが、変更済みとしてマークされることに注意してください。</span><span class="sxs-lookup"><span data-stu-id="ebf8b-153">Note that only properties that are set to different values when copied from the other object will be marked as modified.</span></span>  

## <a name="setting-current-or-original-values-from-a-dictionary"></a><span data-ttu-id="ebf8b-154">ディクショナリからの現在または元の値の設定</span><span class="sxs-lookup"><span data-stu-id="ebf8b-154">Setting current or original values from a dictionary</span></span>  

<span data-ttu-id="ebf8b-155">追跡対象エンティティの現在の値または元の値は、ディクショナリまたはその他のデータ構造から値をコピーすることによって更新できます。</span><span class="sxs-lookup"><span data-stu-id="ebf8b-155">The current or original values of a tracked entity can be updated by copying values from a dictionary or some other data structure.</span></span> <span data-ttu-id="ebf8b-156">以下に例を示します。</span><span class="sxs-lookup"><span data-stu-id="ebf8b-156">For example:</span></span>  

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

<span data-ttu-id="ebf8b-157">元の値を設定するには、CurrentValues プロパティの代わりに OriginalValues プロパティを使用します。</span><span class="sxs-lookup"><span data-stu-id="ebf8b-157">Use the OriginalValues property instead of the CurrentValues property to set original values.</span></span>  

## <a name="setting-current-or-original-values-from-a-dictionary-using-property"></a><span data-ttu-id="ebf8b-158">プロパティを使用してディクショナリから現在または元の値を設定する</span><span class="sxs-lookup"><span data-stu-id="ebf8b-158">Setting current or original values from a dictionary using Property</span></span>  

<span data-ttu-id="ebf8b-159">前に示したように CurrentValues または OriginalValues を使用する代わりに、プロパティメソッドを使用して各プロパティの値を設定することもできます。</span><span class="sxs-lookup"><span data-stu-id="ebf8b-159">An alternative to using CurrentValues or OriginalValues as shown above is to use the Property method to set the value of each property.</span></span> <span data-ttu-id="ebf8b-160">これは、複合プロパティの値を設定する必要がある場合に適しています。</span><span class="sxs-lookup"><span data-stu-id="ebf8b-160">This can be preferable when you need to set the values of complex properties.</span></span> <span data-ttu-id="ebf8b-161">以下に例を示します。</span><span class="sxs-lookup"><span data-stu-id="ebf8b-161">For example:</span></span>  

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

<span data-ttu-id="ebf8b-162">上記の例では、ドット形式の名前を使用して複雑なプロパティにアクセスしています。</span><span class="sxs-lookup"><span data-stu-id="ebf8b-162">In the example above complex properties are accessed using dotted names.</span></span> <span data-ttu-id="ebf8b-163">複合プロパティにアクセスするその他の方法については、このトピックで後述する「複雑なプロパティについて」の2つのセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="ebf8b-163">For other ways to access complex properties see the two sections later in this topic specifically about complex properties.</span></span>  

## <a name="creating-a-cloned-object-containing-current-original-or-database-values"></a><span data-ttu-id="ebf8b-164">現在、元、またはデータベースの値を含む複製されたオブジェクトの作成</span><span class="sxs-lookup"><span data-stu-id="ebf8b-164">Creating a cloned object containing current, original, or database values</span></span>  

<span data-ttu-id="ebf8b-165">CurrentValues、OriginalValues、または GetDatabaseValues から返された DbPropertyValues オブジェクトを使用して、エンティティの複製を作成できます。</span><span class="sxs-lookup"><span data-stu-id="ebf8b-165">The DbPropertyValues object returned from CurrentValues, OriginalValues, or GetDatabaseValues can be used to create a clone of the entity.</span></span> <span data-ttu-id="ebf8b-166">この複製には、作成に使用された DbPropertyValues オブジェクトのプロパティ値が含まれます。</span><span class="sxs-lookup"><span data-stu-id="ebf8b-166">This clone will contain the property values from the DbPropertyValues object used to create it.</span></span> <span data-ttu-id="ebf8b-167">以下に例を示します。</span><span class="sxs-lookup"><span data-stu-id="ebf8b-167">For example:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    var clonedBlog = context.Entry(blog).GetDatabaseValues().ToObject();
}
```  

<span data-ttu-id="ebf8b-168">返されるオブジェクトはエンティティではなく、コンテキストによって追跡されていないことに注意してください。</span><span class="sxs-lookup"><span data-stu-id="ebf8b-168">Note that the object returned is not the entity and is not being tracked by the context.</span></span> <span data-ttu-id="ebf8b-169">返されたオブジェクトには、他のオブジェクトに設定されたリレーションシップもありません。</span><span class="sxs-lookup"><span data-stu-id="ebf8b-169">The returned object also does not have any relationships set to other objects.</span></span>  

<span data-ttu-id="ebf8b-170">複製されたオブジェクトは、データベースに対する同時更新に関連する問題を解決するのに役立ちます。特に、特定の型のオブジェクトへのデータバインドを含む UI が使用されている場合に役立ちます。</span><span class="sxs-lookup"><span data-stu-id="ebf8b-170">The cloned object can be useful for resolving issues related to concurrent updates to the database, especially where a UI that involves data binding to objects of a certain type is being used.</span></span>  

## <a name="getting-and-setting-the-current-or-original-values-of-complex-properties"></a><span data-ttu-id="ebf8b-171">複合プロパティの現在の値または元の値の取得と設定</span><span class="sxs-lookup"><span data-stu-id="ebf8b-171">Getting and setting the current or original values of complex properties</span></span>  

<span data-ttu-id="ebf8b-172">複合オブジェクト全体の値は、プリミティブプロパティの場合と同様に、プロパティメソッドを使用して読み取って設定できます。</span><span class="sxs-lookup"><span data-stu-id="ebf8b-172">The value of an entire complex object can be read and set using the Property method just as it can be for a primitive property.</span></span> <span data-ttu-id="ebf8b-173">さらに、複合オブジェクトをドリルダウンして、そのオブジェクトのプロパティの読み取りや設定を行うことも、入れ子になったオブジェクトを使用することもできます。</span><span class="sxs-lookup"><span data-stu-id="ebf8b-173">In addition you can drill down into the complex object and read or set properties of that object, or even a nested object.</span></span> <span data-ttu-id="ebf8b-174">次にいくつかの例を示します。</span><span class="sxs-lookup"><span data-stu-id="ebf8b-174">Here are some examples:</span></span>  

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

<span data-ttu-id="ebf8b-175">元の値を取得または設定するには、CurrentValue プロパティの代わりに OriginalValue プロパティを使用します。</span><span class="sxs-lookup"><span data-stu-id="ebf8b-175">Use the OriginalValue property instead of the CurrentValue property to get or set an original value.</span></span>  

<span data-ttu-id="ebf8b-176">複合プロパティにアクセスするには、プロパティまたは ComplexProperty メソッドのいずれかを使用することに注意してください。</span><span class="sxs-lookup"><span data-stu-id="ebf8b-176">Note that either the Property or the ComplexProperty method can be used to access a complex property.</span></span> <span data-ttu-id="ebf8b-177">ただし、追加のプロパティまたは ComplexProperty 呼び出しを使用して複合オブジェクトをドリルダウンする場合は、ComplexProperty メソッドを使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ebf8b-177">However, the ComplexProperty method must be used if you wish to drill down into the complex object with additional Property or ComplexProperty calls.</span></span>  

## <a name="using-dbpropertyvalues-to-access-complex-properties"></a><span data-ttu-id="ebf8b-178">DbPropertyValues を使用した複合プロパティへのアクセス</span><span class="sxs-lookup"><span data-stu-id="ebf8b-178">Using DbPropertyValues to access complex properties</span></span>  

<span data-ttu-id="ebf8b-179">CurrentValues、OriginalValues、または GetDatabaseValues を使用してエンティティの現在、元、またはすべての値を取得すると、複合プロパティの値が入れ子になった DbPropertyValues オブジェクトとして返されます。</span><span class="sxs-lookup"><span data-stu-id="ebf8b-179">When you use CurrentValues, OriginalValues, or GetDatabaseValues to get all the current, original, or database values for an entity, the values of any complex properties are returned as nested DbPropertyValues objects.</span></span> <span data-ttu-id="ebf8b-180">これらの入れ子になったオブジェクトを使用して、複合オブジェクトの値を取得できます。</span><span class="sxs-lookup"><span data-stu-id="ebf8b-180">These nested objects can then be used to get values of the complex object.</span></span> <span data-ttu-id="ebf8b-181">たとえば、次のメソッドは、すべてのプロパティの値を出力します。これには、複合プロパティと入れ子になった複合プロパティの値が含まれます。</span><span class="sxs-lookup"><span data-stu-id="ebf8b-181">For example, the following method will print out the values of all properties, including values of any complex properties and nested complex properties.</span></span>  

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

<span data-ttu-id="ebf8b-182">現在のすべてのプロパティ値を出力するには、次のようにメソッドを呼び出します。</span><span class="sxs-lookup"><span data-stu-id="ebf8b-182">To print out all current property values the method would be called like this:</span></span>  

``` csharp
using (var context = new BloggingContext())
{
    var user = context.Users.Find("johndoe1987");

    WritePropertyValues("", context.Entry(user).CurrentValues);
}
```  
