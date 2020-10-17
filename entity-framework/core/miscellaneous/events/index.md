---
title: ログとインターセプトの概要 - EF Core
description: EF Core のログ、イベント、インターセプター、診断の概要
author: ajcvickers
ms.date: 10/01/2020
uid: core/miscellaneous/events/index
ms.openlocfilehash: 8c46b4efb205c60be51529a89cd9dd8fb5268156
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066496"
---
# <a name="overview-of-logging-and-interception"></a>ログとインターセプトの概要

Entity Framework Core (EF Core) には、ログの生成、イベントへの応答、および診断の取得を行うためのメカニズムがいくつか含まれています。 これらはそれぞれ異なる状況に合わせて調整されており、複数のメカニズムが動作する可能性がある場合でも、タスクに最適なメカニズムを選択することが重要です。 たとえば、データベース インターセプターを使用して SQL をログに記録することもできますが、これはログ固有のメカニズムのいずれかの方がより適切に処理されます。 このページでは、これらの各メカニズムの概要を示し、それぞれを使用する状況について説明します。

## <a name="simple-logging"></a>シンプルなログ

> [!NOTE]
> この機能は、EF Core 5.0 で追加されました。

[LogTo](https://github.com/dotnet/efcore/blob/ec3df8fd7e4ea4ebeebfa747619cef37b23ab2c6/src/EFCore/DbContextOptionsBuilder.cs#L135) を使用することで、任意の種類のアプリケーションから EF Core のログにアクセスすることができます <!-- Issue #2748 <xref:Microsoft.EntityFrameworkCore.DbContextOptionsBuilder.LogTo%2A> --> ([DbContext インスタンスの構成時](xref:core/miscellaneous/configuring-dbcontext))。 この構成は、通常、<xref:Microsoft.EntityFrameworkCore.DbContext.OnConfiguring%2A?displayProperty=nameWithType> のオーバーライドで行われます。 次に例を示します。

<!--
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
        => optionsBuilder.LogTo(Console.WriteLine);
-->
[!code-csharp[LogToConsole](../../../../samples/core/Miscellaneous/Logging/SimpleLogging/Program.cs?name=LogToConsole)]

この概念は、EF6 の <xref:System.Data.Entity.Database.Log?displayProperty=nameWithType> に似ています。

詳細については、「[シンプルなログ](xref:core/miscellaneous/events/simple-logging)」を参照してください。
