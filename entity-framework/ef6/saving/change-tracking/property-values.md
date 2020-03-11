---
title: プロパティ値の操作-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: e3278b4b-9378-4fdb-923d-f64d80aaae70
ms.openlocfilehash: d8a18182754980d79b71df3f227b30c4ce40366f
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78414375"
---
# <a name="working-with-property-values"></a>プロパティ値の操作
ほとんどの場合 Entity Framework は、エンティティインスタンスのプロパティの状態、元の値、および現在の値の追跡を行います。 ただし、接続が切断されたシナリオなど、EF がプロパティについての情報を表示または操作する場合もあります。 このトピックで紹介するテクニックは、Code First および EF Designer で作成されたモデルに等しく使用できます。  

Entity Framework は、追跡対象のエンティティの各プロパティの2つの値を追跡します。 現在の値は、名前が示すように、エンティティ内のプロパティの現在の値です。 元の値は、エンティティがデータベースから照会されたとき、またはコンテキストにアタッチされたときにプロパティに保持されていた値です。  

プロパティ値を操作するには、次の2つの一般的なメカニズムがあります。  

- 1つのプロパティの値は、プロパティメソッドを使用して厳密に型指定された方法で取得できます。  
- エンティティのすべてのプロパティの値は、DbPropertyValues オブジェクトに読み取ることができます。 DbPropertyValues は、プロパティ値を読み取って設定できるようにするために、ディクショナリに似たオブジェクトとして機能します。 DbPropertyValues オブジェクトの値は、別の DbPropertyValues オブジェクトの値、またはエンティティの別のコピーや単純なデータ転送オブジェクト (DTO) などの他のオブジェクトの値から設定できます。  

以下のセクションでは、上記の両方のメカニズムを使用する例を示します。  

## <a name="getting-and-setting-the-current-or-original-value-of-an-individual-property"></a>個々のプロパティの現在の値または元の値を取得および設定する  

次の例では、プロパティの現在の値を読み取って、新しい値を設定する方法を示しています。  

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

元の値の読み取りまたは設定を行うには、CurrentValue プロパティの代わりに OriginalValue プロパティを使用します。  

文字列を使用してプロパティ名を指定した場合、戻り値は "object" として入力されることに注意してください。 一方、ラムダ式が使用されている場合、戻り値は厳密に型指定されます。  

このようにプロパティ値を設定した場合、新しい値が古い値と異なる場合にのみ、プロパティが変更済みとしてマークされます。  

このようにプロパティ値が設定されている場合、自動検出の変更がオフになっていても、変更は自動的に検出されます。  

## <a name="getting-and-setting-the-current-value-of-an-unmapped-property"></a>マップされていないプロパティの現在の値の取得と設定  

データベースにマップされていないプロパティの現在の値も読み取ることができます。 マップされていないプロパティの例としては、ブログの .Rsslink プロパティがあります。 この値は、ブログ Id に基づいて計算される場合があるため、データベースに格納する必要はありません。 次に例を示します。  

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

プロパティが setter を公開している場合は、現在の値を設定することもできます。  

マップされていないプロパティの値の読み取りは、マップされていないプロパティの Entity Framework 検証を実行する場合に便利です。 同じ理由から、現在の値は、コンテキストによって現在追跡されていないエンティティのプロパティに対して読み取りおよび設定できます。 次に例を示します。  

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

マップされていないプロパティや、コンテキストによって追跡されていないエンティティのプロパティには、元の値を使用できないことに注意してください。  

## <a name="checking-whether-a-property-is-marked-as-modified"></a>プロパティが変更済みとしてマークされているかどうかを確認する  

次の例は、個々のプロパティが変更済みとしてマークされているかどうかを確認する方法を示しています。  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    var nameIsModified1 = context.Entry(blog).Property(u => u.Name).IsModified;

    // Use a string for the property name
    var nameIsModified2 = context.Entry(blog).Property("Name").IsModified;
}
```  

変更されたプロパティの値は、SaveChanges が呼び出されたときにデータベースに更新として送信されます。  

##  <a name="marking-a-property-as-modified"></a>プロパティを変更済みとしてマークする  

次の例は、個々のプロパティを強制的に変更済みとしてマークする方法を示しています。  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    context.Entry(blog).Property(u => u.Name).IsModified = true;

    // Use a string for the property name
    context.Entry(blog).Property("Name").IsModified = true;
}
```  

プロパティを変更済みとしてマークすると、プロパティの現在の値が元の値と同じであっても SaveChanges が呼び出されたときに、そのプロパティのデータベースに更新が送信されます。  

現在、個々のプロパティが変更済みとしてマークされた後で変更されないようにリセットすることはできません。 これは、将来のリリースでサポートする予定です。  

## <a name="reading-current-original-and-database-values-for-all-properties-of-an-entity"></a>エンティティのすべてのプロパティの現在の値、元の値、およびデータベースの値の読み取り  

次の例では、エンティティのマップされたすべてのプロパティについて、現在の値、元の値、およびデータベース内の実際の値を読み取る方法を示しています。  

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

現在の値は、エンティティのプロパティに現在格納されている値です。 元の値は、エンティティが照会されたときにデータベースから読み取られた値です。 データベースの値は、現在データベースに格納されている値です。 データベースの値を取得すると、データベースに対する同時編集が別のユーザーによって行われた場合など、エンティティがクエリされた後にデータベース内の値が変更された可能性があります。  

## <a name="setting-current-or-original-values-from-another-object"></a>別のオブジェクトからの現在または元の値の設定  

追跡対象エンティティの現在の値または元の値は、別のオブジェクトから値をコピーすることによって更新できます。 次に例を示します。  

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

上記のコードを実行すると、次の出力が出力されます。  

```console
Current values:
Property Id has value 1
Property Name has value My Cool Blog

Original values:
Property Id has value 1
Property Name has value My Boring Blog
```  

この手法は、サービス呼び出しまたは n 層アプリケーションのクライアントから取得した値を使用してエンティティを更新するときに使用されることがあります。 使用されるオブジェクトは、エンティティと同じ型である必要はありません。ただし、エンティティの名前と名前が一致するプロパティがある場合に限ります。 上記の例では、ブログ Dto のインスタンスを使用して、元の値を更新しています。  

他のオブジェクトからコピーしたときに異なる値に設定されているプロパティのみが、変更済みとしてマークされることに注意してください。  

## <a name="setting-current-or-original-values-from-a-dictionary"></a>ディクショナリからの現在または元の値の設定  

追跡対象エンティティの現在の値または元の値は、ディクショナリまたはその他のデータ構造から値をコピーすることによって更新できます。 次に例を示します。  

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

元の値を設定するには、CurrentValues プロパティの代わりに OriginalValues プロパティを使用します。  

## <a name="setting-current-or-original-values-from-a-dictionary-using-property"></a>プロパティを使用してディクショナリから現在または元の値を設定する  

前に示したように CurrentValues または OriginalValues を使用する代わりに、プロパティメソッドを使用して各プロパティの値を設定することもできます。 これは、複合プロパティの値を設定する必要がある場合に適しています。 次に例を示します。  

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

上記の例では、ドット形式の名前を使用して複雑なプロパティにアクセスしています。 複合プロパティにアクセスするその他の方法については、このトピックで後述する「複雑なプロパティについて」の2つのセクションを参照してください。  

## <a name="creating-a-cloned-object-containing-current-original-or-database-values"></a>現在、元、またはデータベースの値を含む複製されたオブジェクトの作成  

CurrentValues、OriginalValues、または GetDatabaseValues から返された DbPropertyValues オブジェクトを使用して、エンティティの複製を作成できます。 この複製には、作成に使用された DbPropertyValues オブジェクトのプロパティ値が含まれます。 次に例を示します。  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    var clonedBlog = context.Entry(blog).GetDatabaseValues().ToObject();
}
```  

返されるオブジェクトはエンティティではなく、コンテキストによって追跡されていないことに注意してください。 返されたオブジェクトには、他のオブジェクトに設定されたリレーションシップもありません。  

複製されたオブジェクトは、データベースに対する同時更新に関連する問題を解決するのに役立ちます。特に、特定の型のオブジェクトへのデータバインドを含む UI が使用されている場合に役立ちます。  

## <a name="getting-and-setting-the-current-or-original-values-of-complex-properties"></a>複合プロパティの現在の値または元の値の取得と設定  

複合オブジェクト全体の値は、プリミティブプロパティの場合と同様に、プロパティメソッドを使用して読み取って設定できます。 さらに、複合オブジェクトをドリルダウンして、そのオブジェクトのプロパティの読み取りや設定を行うことも、入れ子になったオブジェクトを使用することもできます。 次に例をいくつか示します。  

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

元の値を取得または設定するには、CurrentValue プロパティの代わりに OriginalValue プロパティを使用します。  

複合プロパティにアクセスするには、プロパティまたは ComplexProperty メソッドのいずれかを使用することに注意してください。 ただし、追加のプロパティまたは ComplexProperty 呼び出しを使用して複合オブジェクトをドリルダウンする場合は、ComplexProperty メソッドを使用する必要があります。  

## <a name="using-dbpropertyvalues-to-access-complex-properties"></a>DbPropertyValues を使用した複合プロパティへのアクセス  

CurrentValues、OriginalValues、または GetDatabaseValues を使用してエンティティの現在、元、またはすべての値を取得すると、複合プロパティの値が入れ子になった DbPropertyValues オブジェクトとして返されます。 これらの入れ子になったオブジェクトを使用して、複合オブジェクトの値を取得できます。 たとえば、次のメソッドは、すべてのプロパティの値を出力します。これには、複合プロパティと入れ子になった複合プロパティの値が含まれます。  

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

現在のすべてのプロパティ値を出力するには、次のようにメソッドを呼び出します。  

``` csharp
using (var context = new BloggingContext())
{
    var user = context.Users.Find("johndoe1987");

    WritePropertyValues("", context.Entry(user).CurrentValues);
}
```  
