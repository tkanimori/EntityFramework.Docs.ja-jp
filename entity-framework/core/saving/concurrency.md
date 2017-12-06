---
title: "同時実行制御 - EF コアの処理"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: bce0539d-b0cd-457d-be71-f7ca16f3baea
ms.technology: entity-framework-core
uid: core/saving/concurrency
ms.openlocfilehash: bbd3e154c1b27b16c7d8f8fbf9ed51df0849795c
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2017
---
# <a name="handling-concurrency"></a>同時実行の処理

プロパティが、同時実行トークンとして構成されている場合 EF は、その他のユーザーが変更なし、データベース内でその値そのレコードに変更を保存するときにでチェックされます。

> [!TIP]  
> この記事を表示する[サンプル](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Saving/Saving/Concurrency/)GitHub でします。

## <a name="how-concurrency-handling-works-in-ef-core"></a>EF Core での同時実行処理のしくみ

Entity Framework Core での同時実行処理のしくみの詳細については、次を参照してください。[同時実行トークン](../modeling/concurrency.md)です。

## <a name="resolving-concurrency-conflicts"></a>同時実行の競合を解決します。

同時実行の競合を解決するには、データベースに加えられた変更と、現在のユーザーから、保留中の変更をマージするアルゴリズムを使用してが含まれます。 正確な方法、アプリケーションは異なりますが、一般的な方法をユーザーに値を表示し、データベースに格納するための正しい値を決定する、です。

**同時実行の競合を解決するために使用可能な値の 3 つのセットがあります。**

* **現在の値**値、アプリケーションがデータベースへの書き込みを試みたを示します。

* **元の値**はすべての編集が行われる前に、データベースから取得された元の値。

* **値をデータベース**はデータベースに格納されている値です。

同時実行の競合を処理する catch、`DbUpdateConcurrencyException`中に`SaveChanges()`を使用して`DbUpdateConcurrencyException.Entries`を影響を受けるエンティティでは、一連の変更を準備し、再試行、`SaveChanges()`操作します。

次の例では、`Person.FirstName`と`Person.LastName`同時実行トークンとしてセットアップします。 `// TODO:`をデータベースに保存する値を選択するアプリケーション固有のロジックを含めると、場所にコメントです。

<!-- [!code-csharp[Main](samples/core/Saving/Saving/Concurrency/Sample.cs?highlight=53,54)] -->
``` csharp
using Microsoft.EntityFrameworkCore;
using System;
using System.ComponentModel.DataAnnotations;
using System.Linq;

namespace EFSaving.Concurrency
{
    public class Sample
    {
        public static void Run()
        {
            // Ensure database is created and has a person in it
            using (var context = new PersonContext())
            {
                context.Database.EnsureDeleted();
                context.Database.EnsureCreated();

                context.People.Add(new Person { FirstName = "John", LastName = "Doe" });
                context.SaveChanges();
            }

            using (var context = new PersonContext())
            {
                // Fetch a person from database and change phone number
                var person = context.People.Single(p => p.PersonId == 1);
                person.PhoneNumber = "555-555-5555";

                // Change the persons name in the database (will cause a concurrency conflict)
                context.Database.ExecuteSqlCommand("UPDATE dbo.People SET FirstName = 'Jane' WHERE PersonId = 1");

                try
                {
                    // Attempt to save changes to the database
                    context.SaveChanges();
                }
                catch (DbUpdateConcurrencyException ex)
                {
                    foreach (var entry in ex.Entries)
                    {
                        if (entry.Entity is Person)
                        {
                            // Using a NoTracking query means we get the entity but it is not tracked by the context
                            // and will not be merged with existing entities in the context.
                            var databaseEntity = context.People.AsNoTracking().Single(p => p.PersonId == ((Person)entry.Entity).PersonId);
                            var databaseEntry = context.Entry(databaseEntity);

                            foreach (var property in entry.Metadata.GetProperties())
                            {
                                var proposedValue = entry.Property(property.Name).CurrentValue;
                                var originalValue = entry.Property(property.Name).OriginalValue;
                                var databaseValue = databaseEntry.Property(property.Name).CurrentValue;

                                // TODO: Logic to decide which value should be written to database
                                // entry.Property(property.Name).CurrentValue = <value to be saved>;

                                // Update original values to
                                entry.Property(property.Name).OriginalValue = databaseEntry.Property(property.Name).CurrentValue;
                            }
                        }
                        else
                        {
                            throw new NotSupportedException("Don't know how to handle concurrency conflicts for " + entry.Metadata.Name);
                        }
                    }

                    // Retry the save operation
                    context.SaveChanges();
                }
            }
        }

        public class PersonContext : DbContext
        {
            public DbSet<Person> People { get; set; }

            protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
            {
                optionsBuilder.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=EFSaving.Concurrency;Trusted_Connection=True;");
            }
        }

        public class Person
        {
            public int PersonId { get; set; }

            [ConcurrencyCheck]
            public string FirstName { get; set; }

            [ConcurrencyCheck]
            public string LastName { get; set; }

            public string PhoneNumber { get; set; }
        }

    }
}
```
