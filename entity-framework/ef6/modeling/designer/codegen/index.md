---
title: デザイナー コード生成テンプレート - EF6
description: Entity Framework 6 でのデザイナー コード生成テンプレート
author: ajcvickers
ms.date: 10/23/2016
uid: ef6/modeling/designer/codegen/index
ms.openlocfilehash: 9eb0a930d7b283e449f6b08c090c8483453de452
ms.sourcegitcommit: 0a25c03fa65ae6e0e0e3f66bac48d59eceb96a5a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2020
ms.locfileid: "92066487"
---
# <a name="designer-code-generation-templates"></a>デザイナー コード生成テンプレート
Entity Framework Designer を使用してモデルを作成するときには、クラスと派生コンテキストは自動的に生成されます。 既定のコードの生成に加えて、生成されるコードをカスタマイズするために使用できる多数のテンプレートも提供します。 これらのテンプレートは、T4 テキスト テンプレートとして提供され、必要な場合は、テンプレートをカスタマイズすることができます。

既定で生成されるコードは、モデルの作成に使用する Visual Studio のバージョンによって異なります。

-   Visual Studio 2012 と 2013 で作成されたモデルは、単純な POCO エンティティ クラスと、簡略化された DbContext から派生したコンテキストを生成します。
-   Visual Studio 2010 で作成されたモデルは、EntityObject から派生したエンティティ クラス、および ObjectContext から派生したコンテキストを生成します。
  > [!NOTE]    
  > モデルを追加したら、DbContext Generator テンプレートに切り替えることをお勧めします。

このページでは、使用可能なテンプレートについて説明し、モデルにテンプレートを追加する方法について説明します。

## <a name="available-templates"></a>使用可能なテンプレート

Entity Framework チームによって次のテンプレートが提供されます。

### <a name="dbcontext-generator"></a>DbContext ジェネレーター

このテンプレートは、単純な POCO エンティティ クラスと、EF6 を使用して DbContext から派生したコンテキストを生成します。
これは、以下に示すその他のテンプレートのいずれかを使用する理由がない限り、推奨されるテンプレートです。
これは、最近のバージョンの Visual Studio (Visual Studio 2013 以降) を使用している場合に既定で取得するコード生成テンプレートでもあります。新しいモデルを作成するときに、このテンプレートが既定で使用され、T4 ファイル (.tt) は、.edmx ファイルの下に入れ子になります。

#### <a name="older-versions-of-visual-studio"></a>旧バージョンの Visual Studio
- **Visual Studio 2012:****EF 6.x DbContextGenerator** テンプレートを取得するには、最新バージョンの **Entity Framework Tools for Visual Studio** をインストールする必要があります。詳細については、[Entity Framework の取得](xref:ef6/fundamentals/install)に関するページを参照してください。
- **Visual Studio 2010:****EF 6.x DbContextGenerator** テンプレートは Visual Studio 2010 では使用できません。

#### <a name="dbcontext-generator-for-ef-5x"></a>EF 5.x DbContext ジェネレーター

古いバージョンの EntityFramework NuGet パッケージ (メジャー バージョン 5 のパッケージ) を使用している場合、**EF 5.x DbContext ジェネレーター** テンプレートを使用する必要があります。

Visual Studio 2013 または 2012 を使用している場合は、このテンプレートが既にインストールされています。

Visual Studio 2010 を使用している場合は、Visual Studio ギャラリーからテンプレートをダウンロードしてテンプレートを追加するときに、**[オンライン]** タブを選択する必要があります。 または、テンプレートを事前に Visual Studio ギャラリーから直接インストールできます。 以降のバージョンの Visual Studio にはテンプレートが含まれているため、ギャラリーのバージョンは Visual Studio 2010 でのみインストールできます。

- [C# 用 EF 5.x DbContext ジェネレーター](https://visualstudiogallery.msdn.microsoft.com/da740968-02f9-42a9-9ee4-1a9a06d896a2)
- [C# Web サイト用 EF 5.x DbContext ジェネレーター](https://visualstudiogallery.msdn.microsoft.com/5d01a981-91b8-492c-b42c-c771c3f31e03)
- [VB.NET 用 EF 5.x DbContext ジェネレーター](https://visualstudiogallery.msdn.microsoft.com/875c882d-333e-455a-8dae-5353510527dd?src=featured)
- [VB.NET Web サイト用 EF 5.x DbContext ジェネレーター](https://visualstudiogallery.msdn.microsoft.com/d4d7d4cd-c2d0-43e6-8944-12f6ff8f2614)

#### <a name="dbcontext-generator-for-ef-4x"></a>EF 4.x DbContext ジェネレーター

古いバージョンの EntityFramework NuGet パッケージ (メジャー バージョン 4 のパッケージ) を使用している場合、**EF 4.x DbContext ジェネレーター** テンプレートを使用する必要があります。 これは、テンプレートをスイカするときに **[オンライン]** タブで確認できます。または事前に Visual Studio ギャラリーから直接テンプレートをインストールすることができます。

- [C# 用 EF 4.x DbContext ジェネレーター](https://visualstudiogallery.msdn.microsoft.com/7812b04c-db36-4817-8a84-e73c452410a2)
- [C# Web サイト用 EF 4.x DbContext ジェネレーター](https://visualstudiogallery.msdn.microsoft.com/de0e9bc6-e86a-4448-8a2e-a1260a53203e)
- [VB.NET 用 EF 4.x DbContext ジェネレーター](https://visualstudiogallery.msdn.microsoft.com/73679ae5-e358-4e76-a538-c7b5e04ac073)
- [VB.NET Web サイト用 EF 4.x DbContext ジェネレーター](https://visualstudiogallery.msdn.microsoft.com/86f5a660-306e-4831-840c-2e4ee7474a92)

### <a name="entityobject-generator"></a>EntityObject ジェネレーター

このテンプレートは、EntityObject から派生したエンティティ クラス、および ObjectContext から派生したコンテキストを生成します。

> [!NOTE]
> DbContext ジェネレーターの使用を検討してください。

DbContext ジェネレーターは、新しいアプリケーションの推奨されるテンプレートになりました。 DbContext ジェネレーターは、より単純な DbContext API を利用します。 既存のアプリケーションをサポートするために EntityObject ジェネレーターを引き続き使用できます。

**Visual Studio 2010、2012 &amp; 2013**

Visual Studio ギャラリーからテンプレートをダウンロードしてテンプレートを追加するときに、**[オンライン]** タブを選択する必要があります。 または、テンプレートを事前に Visual Studio ギャラリーから直接インストールできます。

- [C# 用 EF 6.x EntityObject ジェネレーター](https://visualstudiogallery.msdn.microsoft.com/66612113-549c-4a9e-a14a-f629ceb3f89a)
- [C# Web サイト用 EF 6.x EntityObject ジェネレーター](https://visualstudiogallery.msdn.microsoft.com/076140f3-6dbe-451f-a0e0-16b6d2bd8996)
- [VB.NET 用 EF 6.x EntityObject ジェネレーター](https://visualstudiogallery.msdn.microsoft.com/ff479d55-2c85-43c5-a4d6-21cd659435ea)
- [VB.NET Web サイト用 EF 6.x EntityObject ジェネレーター](https://visualstudiogallery.msdn.microsoft.com/668e2b92-c142-4da2-8e60-866c6346fc6a)

**EF 5.x EntityObject ジェネレーター**


Visual Studio 2012 または 2013 を使用している場合は、Visual Studio ギャラリーからテンプレートをダウンロードしてテンプレートを追加するときに、**[オンライン]** タブを選択する必要があります。 または、テンプレートを事前に Visual Studio ギャラリーから直接インストールできます。 Visual Studio 2010 にはテンプレートが含まれているため、ギャラリーのバージョンは Visual Studio 2012 &amp; 2013 でのみインストールできます。

- [C# 用 EF 5.x EntityObject ジェネレーター](https://visualstudiogallery.msdn.microsoft.com/1da40393-b5ec-404a-a000-6a7e6e911339)
- [C# Web サイト用 EF 5.x EntityObject ジェネレーター](https://visualstudiogallery.msdn.microsoft.com/94b48556-fcf0-4b9b-8615-20f9066ae9ac)
- [VB.NET 用 EF 5.x EntityObject ジェネレーター](https://visualstudiogallery.msdn.microsoft.com/92c0129e-40dc-488c-a836-7e30846dfb30)
- [VB.NET Web サイト用 EF 5.x EntityObject ジェネレーター](https://visualstudiogallery.msdn.microsoft.com/5dd7f75c-8c98-4eb7-b4bc-06f0d0b03b41)

ObjectContext コードの生成のみが必要で、テンプレートを編集する必要がない場合、[EntityObject コードの生成を元に戻す](xref:ef6/modeling/designer/codegen/legacy-objectcontext)ことができます。

Visual Studio 2010 を使用している場合は、このテンプレートが既にインストールされています。 Visual Studio 2010 で新しいモデルを作成する場合、このテンプレートは、既定で使用されますが、.tt ファイルがプロジェクトに含まれていません。 テンプレートをカスタマイズする場合は、これをプロジェクトに追加する必要があります。

### <a name="self-tracking-entities-ste-generator"></a>自己追跡エンティティ (STE) ジェネレーター

このテンプレートは、自己追跡エンティティ クラスと、ObjectContext から派生したコンテキストを生成します。 EF アプリケーションでは、エンティティの変更はコンテキストによって追跡されます。 ただし、n 層シナリオでは、エンティティを変更する層でコンテキストを利用できない場合があります。 自己追跡エンティティでは、任意の層での変更を追跡するのに役立ちます。 詳細については、「[自己追跡エンティティ](xref:ef6/fundamentals/disconnected-entities/self-tracking-entities/index)」を参照してください。

> [!NOTE]
> STE テンプレートはお勧めしません

新しいアプリケーションでの STE のテンプレートの使用は推奨されませんが、既存のアプリケーションをサポートするために引き続き使用できます。 n 層シナリオで推奨されるその他のオプションについては、[切り離されたエンティティの記事](xref:ef6/fundamentals/disconnected-entities/index)を参照してください。

> [!NOTE]
> STE テンプレートの EF 6.x バージョンはありません。


> [!NOTE]
> STE テンプレートの Visual Studio 2013 バージョンはありません。

### <a name="visual-studio-2012"></a>Visual Studio 2012

Visual Studio 2012 を使用している場合は、Visual Studio ギャラリーからテンプレートをダウンロードしてテンプレートを追加するときに、**[オンライン]** タブを選択する必要があります。 または、テンプレートを事前に Visual Studio ギャラリーから直接インストールできます。 Visual Studio 2010 にはテンプレートが含まれているため、ギャラリーのバージョンは Visual Studio 2012 でのみインストールできます。

- [C# 用 EF 5.x STE ジェネレーター](https://visualstudiogallery.msdn.microsoft.com/a3ac10a5-9365-4096-bb58-d9a1ba71db8f)
- [C# Web サイト用 EF 5.x STE ジェネレーター](https://visualstudiogallery.msdn.microsoft.com/1b55ab82-eeb4-47ba-8d35-3c7c8b5f5a8c)
- [VB.NET 用 EF 5.x STE ジェネレーター](https://visualstudiogallery.msdn.microsoft.com/1ba8c6a3-44e9-4e1f-b21e-596f3168474b)
- [VB.NET Web サイト用 EF 5.x STE ジェネレーター](https://visualstudiogallery.msdn.microsoft.com/a9fd5f0a-9af4-4e32-9c09-0e057072152e)

#### <a name="visual-studio-2010"></a>Visual Studio 2010**

Visual Studio 2010 を使用している場合は、このテンプレートが既にインストールされています。

### <a name="poco-entity-generator"></a>POCO エンティティ ジェネレーター

このテンプレートは、POCO エンティティ クラスと、ObjectContext から派生したコンテキストを生成します。

> [!NOTE]
> DbContext ジェネレーターの使用を検討してください。

DbContext ジェネレーターは、新しいアプリケーションで POCO クラスを生成するための推奨されるテンプレートになりました。 DbContext ジェネレーターは、新しい DbContext API を利用し、単純な POCO クラスを生成できます。 既存のアプリケーションをサポートするために POCO エンティティ ジェネレーターを引き続き使用できます。

> [!NOTE]
> STE テンプレートの EF 5.x または EF 6.x バージョンはありません。

> [!NOTE]
> POCO テンプレートの Visual Studio 2013 バージョンはありません。

#### <a name="visual-studio-2012-amp-visual-studio-2010"></a>Visual Studio 2012 &amp; Visual Studio 2010

Visual Studio ギャラリーからテンプレートをダウンロードしてテンプレートを追加するときに、**[オンライン]** タブを選択する必要があります。 または、テンプレートを事前に Visual Studio ギャラリーから直接インストールできます。

- [C# 用 EF 4.x POCO ジェネレーター](https://visualstudiogallery.msdn.microsoft.com/23df0450-5677-4926-96cc-173d02752313)
- [C# Web サイト用 EF 4.x POCO ジェネレーター](https://visualstudiogallery.msdn.microsoft.com/fe568da5-aa1a-4178-a2a5-48813c707a7f)
- [VB.NET 用 EF 4.x POCO ジェネレーター](https://visualstudiogallery.msdn.microsoft.com/53ecbded-8936-4299-ab04-1e44e5489752)
- [VB.NET Web サイト用 EF 4.x POCO ジェネレーター](https://visualstudiogallery.msdn.microsoft.com/463c5aca-05ad-4cdb-910b-2e4f83269e34)

### <a name="what-are-the-web-sites-templates"></a>"Web サイト" テンプレートとは

"Web サイト" テンプレート (たとえば **EF 5.x DbContext Generator for C\# Web サイト**) は、 **[ファイル] -&gt; [新規] -&gt; [Web サイト]** を選択して作成された Web サイト プロジェクトで使用されます。これらは、 **[ファイル] -&gt; [新規] -&gt; [プロジェクト]** を選択して作成した標準のテンプレートを使用する Web アプリケーションとは異なります。 Visual Studio の項目テンプレートのシステムで必要なため、別々のテンプレートを提供しています。

## <a name="using-a-template"></a>テンプレートを使用する

コード生成テンプレートの使用を開始するには、EF Designer のデザイン画面で空いている場所を右クリックして **[コード生成項目の追加]** を選択します。

![コード生成項目を追加する](~/ef6/media/add-code-gen-item.png)

使用するテンプレートをインストールしてある場合 (または Visual Studio に含まれている場合)、左側のメニューの **[コード]** または **[データ]** セクションで使用できます。

![インストール済みテンプレート](~/ef6/media/installed.png)

まだテンプレートをインストールしていない場合は、左側のメニューから **[オンライン]** を選択し、目的のテンプレートを検索します。

![検索テンプレート](~/ef6/media/search.png) 

Visual Studio 2012 を使用している場合は、新しい .tt ファイルが .edmx ファイルの下に入れ子になります。*

> [!NOTE]
> Visual Studio 2012 で作成されたモデルでは、既定のコード生成で使用されるテンプレートを削除する必要がありますが、それ以外の場合、重複するクラスとコンテキストが生成されます。 既定のファイルは、 **&lt;モデル名&gt;.tt** と **&lt;モデル名&gt;.context.tt** です。 

![VS2012 のテンプレート](~/ef6/media/vs2012-templates.png)

Visual Studio 2010 を使用している場合は、tt ファイルがプロジェクトに直接追加されます。  

![VS2010 のテンプレート](~/ef6/media/vs2010-templates.png)
