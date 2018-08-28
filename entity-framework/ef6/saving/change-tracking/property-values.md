---
title: プロパティ値 - EF6 の使用
author: divega
ms.date: 2016-10-23
ms.assetid: e3278b4b-9378-4fdb-923d-f64d80aaae70
ms.openlocfilehash: a9b969950ec7dcfb86a2abc9c8bd6cc24899948c
ms.sourcegitcommit: dadee5905ada9ecdbae28363a682950383ce3e10
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2018
ms.locfileid: "42998304"
---
# <a name="working-with-property-values"></a>プロパティ値の使用
ほとんどの場合 Entity Framework は、状態、元の値と、エンティティ インスタンスのプロパティの現在の値を追跡の考慮されます。 ただし、表示または EF がプロパティに関する情報を操作する場合の切断されたシナリオ - などがあります。 このトピックで紹介するテクニックは、Code First および EF Designer で作成されたモデルに等しく使用できます。  

Entity Framework は、追跡対象エンティティの各プロパティの 2 つの値を追跡します。 現在の値は、その名前が示すエンティティのプロパティの現在の値です。 元の値には、プロパティがエンティティをデータベースからクエリを実行またはコンテキストにアタッチしたときに、値です。  

プロパティの値を操作するための 2 つの一般的なメカニズムがあります。  

- プロパティ メソッドを使用して厳密に型指定された方法では、1 つのプロパティの値を取得できます。  
- DbPropertyValues オブジェクトには、エンティティのすべてのプロパティの値を読み取ることができます。 DbPropertyValues し、読み取り、設定のプロパティの値を許可するディクショナリに似たオブジェクトとしては機能します。 DbPropertyValues の別のオブジェクトの値とは、エンティティまたは単純なデータ転送オブジェクト (DTO) の別のコピーなど、他のオブジェクトの値から、DbPropertyValues オブジェクト内の値を設定できます。  

以下のセクションでは、上記のメカニズムの両方を使用しての例を紹介します。  

## <a name="getting-and-setting-the-current-or-original-value-of-an-individual-property"></a>取得と個々 のプロパティの現在または元の値の設定  

次の例では、プロパティの現在の値を読み取り、新しい値を設定する方法を示します。  

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

CurrentValue プロパティではなく OriginalValue プロパティを使用して、参照したり、元の値を設定します。  

プロパティ名を指定する文字列を使用すると、「オブジェクト」として返される値を入力することに注意してください。 その一方で、ラムダ式を使用する場合は、返される値は厳密に型指定します。  

このようなプロパティ値を設定しても、新しい値が古い値と異なる場合は変更と、プロパティをマークのみされます。  

この方法でプロパティ値が設定されている場合 AutoDetectChanges がになっている場合でも、変更が自動的に検出します。  

## <a name="getting-and-setting-the-current-value-of-an-unmapped-property"></a>取得と、マップされていないプロパティの現在の値の設定  

データベースにマップされていないプロパティの現在の値が読み込むこともできます。 マップされていないプロパティの例には、ブログ、RssLink プロパティ可能性があります。 この値は、BlogId に基づいて計算して、そのため、データベースに格納する必要があります。 例えば:  

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

現在の値は、プロパティ setter を公開している場合にも設定できます。  

マップされていないプロパティの Entity Framework の検証を実行するときに、マップされていないプロパティの値を読み取ることは便利です。 同様の理由には、現在の値を読み取りし、いない現在追跡されているコンテキストによってエンティティのプロパティを設定することができます。 例えば:  

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

元の値がマップされていないプロパティやいないコンテキストによって追跡されているエンティティのプロパティには使用できないことに注意してください。  

## <a name="checking-whether-a-property-is-marked-as-modified"></a>プロパティが変更済みとしてマークされているかどうかをチェックします。  

次の例では、個々 のプロパティが変更済みとしてマークされているかどうかをチェックする方法を示します。  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    var nameIsModified1 = context.Entry(blog).Property(u => u.Name).IsModified;

    // Use a string for the property name
    var nameIsModified2 = context.Entry(blog).Property("Name").IsModified;
}
```  

変更されたプロパティの値は、SaveChanges が呼び出されたときに更新プログラムとして、データベースに送信されます。  

##  <a name="marking-a-property-as-modified"></a>プロパティ変更済みとしてマークします。  

次の例では、変更済みとしてマークする個々 のプロパティを強制する方法を示します。  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    context.Entry(blog).Property(u => u.Name).IsModified = true;

    // Use a string for the property name
    context.Entry(blog).Property("Name").IsModified = true;
}
```  

変更の強制を更新するのには、プロパティの現在の値が元の値と同じ場合でも、SaveChanges が呼び出されたときに、プロパティのデータベースに送信プロパティをマークします。  

現在では、後は変更済みとしてマークされていない変更する個々 のプロパティをリセットすることはできません。 これは将来のリリースでサポートする予定です。  

## <a name="reading-current-original-and-database-values-for-all-properties-of-an-entity"></a>現在、元のデータベース エンティティのすべてのプロパティの値の読み取り  

次の例では、現在の値、元の値、およびエンティティのマップされたプロパティのすべてのデータベースの実際の値を読み取る方法を示します。  

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

現在の値は、現在のエンティティのプロパティが含まれている値です。 元の値は、エンティティが照会されたときに、データベースから読み取られた値です。 データベースの値は、データベースに現在格納されている値です。 別のユーザーによって、データベースを加え、同時実行を編集するときなどに、エンティティのクエリが実行されましたので、データベースの値が変更された可能性がありますがある場合は、データベースの値を取得すると便利です。  

## <a name="setting-current-or-original-values-from-another-object"></a>別のオブジェクトから現在または元の値の設定  

別のオブジェクトから値をコピーして、追跡対象エンティティの現在または元の値を更新できます。 例えば:  

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

上記のコードを実行しているが表示されます。  

```  
Current values:
Property Id has value 1
Property Name has value My Cool Blog

Original values:
Property Id has value 1
Property Name has value My Boring Blog
```  

エンティティをサービスの呼び出しまたは n 層アプリケーションでクライアントから取得した値に更新する場合は、この方法は使用こともあります。 名前に一致するエンティティのプロパティがある限り、エンティティと同じ型であるために使用するオブジェクトがないことに注意してください。 上記の例では、BlogDTO のインスタンスが元の値を更新するために使用します。  

変更済みとに、その他のオブジェクトからコピーするときに異なる値に設定されているプロパティのみをマークすることに注意してください。  

## <a name="setting-current-or-original-values-from-a-dictionary"></a>ディクショナリの現在または元の値の設定  

追跡対象エンティティの現在または元の値は、ディクショナリまたはその他のいくつかのデータ構造体から値をコピーすることで更新できます。 例えば:  

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

CurrentValues プロパティの代わりに行っていましたプロパティを使用して、元の値を設定します。  

## <a name="setting-current-or-original-values-from-a-dictionary-using-property"></a>プロパティを使用してディクショナリから現在または元の値の設定  

CurrentValues または上記のように行っていましたを使用する代わりにでは、各プロパティの値を設定するプロパティのメソッドを使用します。 これは、複雑なプロパティの値を設定する必要がある場合ことをお勧めします。 例えば:  

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

複雑なプロパティを上記の例ではアクセス ドット形式の名前を使用します。 複雑なプロパティにアクセスするには、その他の方法は、複雑なプロパティについて具体的には、このトピックの後半の 2 つのセクションを参照してください。  

## <a name="creating-a-cloned-object-containing-current-original-or-database-values"></a>現在、元のパスまたはデータベースの値を格納している複製されたオブジェクトを作成します。  

CurrentValues、行っていましたから返された DbPropertyValues オブジェクトまたはでは、エンティティの複製の作成に使用できます。 この複製を作成するために使用した DbPropertyValues オブジェクトからプロパティ値が含まれます。 例えば:  

``` csharp
using (var context = new BloggingContext())
{
    var blog = context.Blogs.Find(1);

    var clonedBlog = context.Entry(blog).GetDatabaseValues().ToObject();
}
```  

返されるオブジェクトがエンティティでないし、コンテキストによって追跡されていませんことに注意してください。 また、返されたオブジェクトには、リレーションシップのセットを他のオブジェクトにはありません。  

複製されたオブジェクトは、特に、特定の種類のオブジェクトへのデータ バインディングを含む UI が使用されている、データベースの同時実行更新に関連する問題を解決するために役立ちます。  

## <a name="getting-and-setting-the-current-or-original-values-of-complex-properties"></a>取得と複雑なプロパティの現在または元の値の設定  

全体の複雑なオブジェクトの値には、読み取りとプリミティブ プロパティのことができ、同様、プロパティ メソッドを使用してセットを指定できます。 さらに複雑なオブジェクトと、そのオブジェクトまたは入れ子になったオブジェクトもの読み取りや設定のプロパティにドリルダウンすることができます。 次にいくつかの例を示します。  

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

CurrentValue プロパティではなく OriginalValue プロパティを使用して、元の値取得または設定します。  

プロパティまたは ComplexProperty メソッドのいずれかを複合プロパティへのアクセスに使用できることに注意してください。 ただし、追加のプロパティを持つ複雑なオブジェクトにドリル ダウンするまたは ComplexProperty を呼び出す場合は、ComplexProperty メソッドを使用する必要があります。  

## <a name="using-dbpropertyvalues-to-access-complex-properties"></a>DbPropertyValues を使用して複雑なプロパティにアクセスするには  

ときに、現在のすべてを取得する CurrentValues、行っていました、またはでを使用することもエンティティには、データベースの値、複雑なプロパティの値は、入れ子になった DbPropertyValues オブジェクトとして返されます。 これらのオブジェクトが入れ子になったし、複雑なオブジェクトの値を取得するために使用します。 たとえば、次のメソッドは、任意の複雑なプロパティと入れ子になった複雑なプロパティの値を含むすべてのプロパティの値が印刷されます。  

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

メソッドを現在のプロパティ値をすべて出力が次のように呼び出されます。  

``` csharp
using (var context = new BloggingContext())
{
    var user = context.Users.Find("johndoe1987");

    WritePropertyValues("", context.Entry(user).CurrentValues);
}
```  
