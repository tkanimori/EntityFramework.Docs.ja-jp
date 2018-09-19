---
title: モデルに基づく規則 - EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 0fc4eef8-29b8-4192-9c77-08fd33d3db3a
ms.openlocfilehash: 80b722730b4ca6c9d00a8611b6c9027e8bc9fe61
ms.sourcegitcommit: 269c8a1a457a9ad27b4026c22c4b1a76991fb360
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2018
ms.locfileid: "46283708"
---
# <a name="model-based-conventions"></a><span data-ttu-id="0fd4a-102">モデルに基づく規則</span><span class="sxs-lookup"><span data-stu-id="0fd4a-102">Model-Based Conventions</span></span>
> [!NOTE]
> <span data-ttu-id="0fd4a-103">**EF6 以降のみ** - このページで説明する機能、API などは、Entity Framework 6 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="0fd4a-103">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="0fd4a-104">以前のバージョンを使用している場合、一部またはすべての情報は適用されません。</span><span class="sxs-lookup"><span data-stu-id="0fd4a-104">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="0fd4a-105">モデル ベースの規則は、規則ベースのモデルの構成の高度な方法です。</span><span class="sxs-lookup"><span data-stu-id="0fd4a-105">Model based conventions are an advanced method of convention based model configuration.</span></span> <span data-ttu-id="0fd4a-106">ほとんどのシナリオ、[カスタム Code First 規約 API DbModelBuilder で](~/ef6/modeling/code-first/conventions/custom.md)使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="0fd4a-106">For most scenarios the [Custom Code First Convention API on DbModelBuilder](~/ef6/modeling/code-first/conventions/custom.md) should be used.</span></span> <span data-ttu-id="0fd4a-107">モデル ベースの規則を使用する前に、規則の DbModelBuilder API の理解をお勧めします。</span><span class="sxs-lookup"><span data-stu-id="0fd4a-107">An understanding of the DbModelBuilder API for conventions is recommended before using model based conventions.</span></span>  

<span data-ttu-id="0fd4a-108">モデル ベースの規則は、プロパティとは、標準の規則を構成できないテーブルに影響する規則の作成を許可します。</span><span class="sxs-lookup"><span data-stu-id="0fd4a-108">Model based conventions allow the creation of conventions that affect properties and tables which are not configurable through standard conventions.</span></span> <span data-ttu-id="0fd4a-109">これらの例は、階層モデルごとにテーブル内の列の識別子および独立アソシエーションの列です。</span><span class="sxs-lookup"><span data-stu-id="0fd4a-109">Examples of these are discriminator columns in table per hierarchy models and Independent Association columns.</span></span>  

## <a name="creating-a-convention"></a><span data-ttu-id="0fd4a-110">規則を作成します。</span><span class="sxs-lookup"><span data-stu-id="0fd4a-110">Creating a Convention</span></span>   

<span data-ttu-id="0fd4a-111">モデル ベースの規則を作成する最初の手順は、パイプラインで、規則は、モデルに適用する必要がある場合に選択します。</span><span class="sxs-lookup"><span data-stu-id="0fd4a-111">The first step in creating a model based convention is choosing when in the pipeline the convention needs to be applied to the model.</span></span> <span data-ttu-id="0fd4a-112">モデルの規則、(C 領域) の概念とストア (S 領域) の 2 種類があります。</span><span class="sxs-lookup"><span data-stu-id="0fd4a-112">There are two types of model conventions, Conceptual (C-Space) and Store (S-Space).</span></span> <span data-ttu-id="0fd4a-113">C 領域の規則は、S 領域の規則が、データベースを表すモデルのバージョンに適用されますが、アプリケーションがビルドをどのように自動的に生成された列などのコントロールの名前はモデルに適用されます。</span><span class="sxs-lookup"><span data-stu-id="0fd4a-113">A C-Space convention is applied to the model that the application builds, whereas an S-Space convention is applied to the version of the model that represents the database and controls things such as how automatically-generated columns are named.</span></span>  

<span data-ttu-id="0fd4a-114">モデルの規則は、IConceptualModelConvention または IStoreModelConvention から拡張するクラスです。</span><span class="sxs-lookup"><span data-stu-id="0fd4a-114">A model convention is a class that extends from either IConceptualModelConvention or IStoreModelConvention.</span></span>  <span data-ttu-id="0fd4a-115">これらのインターフェイスが両方のまま使用することができるジェネリック型では、MetadataItem、規則が適用されるデータ型をフィルター処理するために使用を入力します。</span><span class="sxs-lookup"><span data-stu-id="0fd4a-115">These interfaces both accept a generic type that can be of type MetadataItem which is used to filter the data type that the convention applies to.</span></span>  

## <a name="adding-a-convention"></a><span data-ttu-id="0fd4a-116">規則の追加</span><span class="sxs-lookup"><span data-stu-id="0fd4a-116">Adding a Convention</span></span>   

<span data-ttu-id="0fd4a-117">モデルの規則は、通常の規則クラスと同じ方法で追加されます。</span><span class="sxs-lookup"><span data-stu-id="0fd4a-117">Model conventions are added in the same way as regular conventions classes.</span></span> <span data-ttu-id="0fd4a-118">**OnModelCreating**メソッドでは、モデルの規則の一覧に、規則を追加します。</span><span class="sxs-lookup"><span data-stu-id="0fd4a-118">In the **OnModelCreating** method, add the convention to the list of conventions for a model.</span></span>  

``` csharp
using System.Data.Entity;
using System.Data.Entity.Core.Metadata.Edm;
using System.Data.Entity.Infrastructure;
using System.Data.Entity.ModelConfiguration.Conventions;

public class BlogContext : DbContext  
{  
    public DbSet<Post> Posts { get; set; }  
    public DbSet<Comment> Comments { get; set; }  

    protected override void OnModelCreating(DbModelBuilder modelBuilder)  
    {  
        modelBuilder.Conventions.Add<MyModelBasedConvention>();  
    }  
}
```  

<span data-ttu-id="0fd4a-119">Conventions.AddBefore を使用して別の規則に関連する規則を追加することも\<\>または Conventions.AddAfter\< \>メソッド。</span><span class="sxs-lookup"><span data-stu-id="0fd4a-119">A convention can also be added in relation to another convention using the Conventions.AddBefore\<\> or Conventions.AddAfter\<\> methods.</span></span> <span data-ttu-id="0fd4a-120">Entity Framework が適用される規則に関する詳細については、ノートのセクションを参照してください。</span><span class="sxs-lookup"><span data-stu-id="0fd4a-120">For more information about the conventions that Entity Framework applies see the notes section.</span></span>  

``` csharp
protected override void OnModelCreating(DbModelBuilder modelBuilder)
{
    modelBuilder.Conventions.AddAfter<IdKeyDiscoveryConvention>(new MyModelBasedConvention());
}
```  

## <a name="example-discriminator-model-convention"></a><span data-ttu-id="0fd4a-121">例: 識別子モデル規則</span><span class="sxs-lookup"><span data-stu-id="0fd4a-121">Example: Discriminator Model Convention</span></span>  

<span data-ttu-id="0fd4a-122">EF によって生成された列の名前は、何か他の規則は、Api を使用して行うことはできませんの例です。</span><span class="sxs-lookup"><span data-stu-id="0fd4a-122">Renaming columns generated by EF is an example of something that you can’t do with the other conventions APIs.</span></span>  <span data-ttu-id="0fd4a-123">これは、状況が唯一の選択肢は、モデルの規則を使用します。</span><span class="sxs-lookup"><span data-stu-id="0fd4a-123">This is a situation where using model conventions is your only option.</span></span>  

<span data-ttu-id="0fd4a-124">モデル ベースの規則を使用して生成された列を構成する方法の例では、識別子列の名前は、方法をカスタマイズします。</span><span class="sxs-lookup"><span data-stu-id="0fd4a-124">An example of how to use a model based convention to configure generated columns is customizing the way discriminator columns are named.</span></span>  <span data-ttu-id="0fd4a-125">"EntityType"を「識別子」をという名前のモデルの各列の名前を変更する規則に基づく単純なモデルの例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="0fd4a-125">Below is an example of a simple model based convention that renames every column in the model named “Discriminator” to “EntityType”.</span></span>  <span data-ttu-id="0fd4a-126">これには、開発者は、「識別子」を単にという列が含まれます。</span><span class="sxs-lookup"><span data-stu-id="0fd4a-126">This includes columns that the developer simply named “Discriminator”.</span></span> <span data-ttu-id="0fd4a-127">「識別子」列は生成された列であるためこれ S 領域で実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="0fd4a-127">Since the “Discriminator” column is a generated column this needs to run in S-Space.</span></span>  

``` csharp
using System.Data.Entity;
using System.Data.Entity.Core.Metadata.Edm;
using System.Data.Entity.Infrastructure;
using System.Data.Entity.ModelConfiguration.Conventions;

class DiscriminatorRenamingConvention : IStoreModelConvention<EdmProperty>  
{  
    public void Apply(EdmProperty property, DbModel model)  
    {            
        if (property.Name == "Discriminator")  
        {  
            property.Name = "EntityType";  
        }  
    }  
}
```  

## <a name="example-general-ia-renaming-convention"></a><span data-ttu-id="0fd4a-128">例: 一般的な IA の規則の名前を変更します。</span><span class="sxs-lookup"><span data-stu-id="0fd4a-128">Example: General IA Renaming Convention</span></span>   

<span data-ttu-id="0fd4a-129">別のより複雑なアクションに基づくモデル規則の例では、独立した関連付け (IAs)、名前をする方法を構成します。</span><span class="sxs-lookup"><span data-stu-id="0fd4a-129">Another more complicated example of model based conventions in action is to configure the way that Independent Associations (IAs) are named.</span></span>  <span data-ttu-id="0fd4a-130">これは、モデルの規則が IAs が EF によって生成されるため、適用し、DbModelBuilder API にアクセスできるモデルではない状況です。</span><span class="sxs-lookup"><span data-stu-id="0fd4a-130">This is a situation where Model conventions are applicable because IAs are generated by EF and aren’t present in the model that the DbModelBuilder API can access.</span></span>  

<span data-ttu-id="0fd4a-131">EF では、IA を生成するときに、EntityType_KeyName という名前の列を作成します。</span><span class="sxs-lookup"><span data-stu-id="0fd4a-131">When EF generates an IA, it creates a column named EntityType_KeyName.</span></span> <span data-ttu-id="0fd4a-132">たとえば、キー列を含む顧客をという名前のアソシエーションの customerid Customer_CustomerId という名前の列が生成されます。</span><span class="sxs-lookup"><span data-stu-id="0fd4a-132">For example, for an association named Customer with a key column named CustomerId it would generate a column named Customer_CustomerId.</span></span> <span data-ttu-id="0fd4a-133">次の規則ストリップ、'\_'、指定された列名からの文字</span><span class="sxs-lookup"><span data-stu-id="0fd4a-133">The following convention strips the ‘\_’ character out of the column name that is generated for the IA.</span></span>  

``` csharp
using System.Data.Entity;
using System.Data.Entity.Core.Metadata.Edm;
using System.Data.Entity.Infrastructure;
using System.Data.Entity.ModelConfiguration.Conventions;

// Provides a convention for fixing the independent association (IA) foreign key column names.  
public class ForeignKeyNamingConvention : IStoreModelConvention<AssociationType>
{

    public void Apply(AssociationType association, DbModel model)
    {
        // Identify ForeignKey properties (including IAs)  
        if (association.IsForeignKey)
        {
            // rename FK columns  
            var constraint = association.Constraint;
            if (DoPropertiesHaveDefaultNames(constraint.FromProperties, constraint.ToRole.Name, constraint.ToProperties))
            {
                NormalizeForeignKeyProperties(constraint.FromProperties);
            }
            if (DoPropertiesHaveDefaultNames(constraint.ToProperties, constraint.FromRole.Name, constraint.FromProperties))
            {
                NormalizeForeignKeyProperties(constraint.ToProperties);
            }
        }
    }

    private bool DoPropertiesHaveDefaultNames(ReadOnlyMetadataCollection<EdmProperty> properties, string roleName, ReadOnlyMetadataCollection<EdmProperty> otherEndProperties)
    {
        if (properties.Count != otherEndProperties.Count)
        {
            return false;
        }

        for (int i = 0; i < properties.Count; ++i)
        {
            if (!properties[i].Name.EndsWith("_" + otherEndProperties[i].Name))
            {
                return false;
            }
        }
        return true;
    }

    private void NormalizeForeignKeyProperties(ReadOnlyMetadataCollection<EdmProperty> properties)
    {
        for (int i = 0; i \< properties.Count; ++i)
        {
            int underscoreIndex = properties[i].Name.IndexOf('_');
            if (underscoreIndex > 0)
            {
                properties[i].Name = properties[i].Name.Remove(underscoreIndex, 1);
            }                 
        }
    }
}
```  

## <a name="extending-existing-conventions"></a><span data-ttu-id="0fd4a-134">既存の規則を拡張します。</span><span class="sxs-lookup"><span data-stu-id="0fd4a-134">Extending Existing Conventions</span></span>   

<span data-ttu-id="0fd4a-135">Entity Framework は、既にモデルに適用される規則のいずれかのような規則を記述する必要がある場合は、最初から修正することを回避するには、その規則を常に拡張できます。</span><span class="sxs-lookup"><span data-stu-id="0fd4a-135">If you need to write a convention that is similar to one of the conventions that Entity Framework already applies to your model you can always extend that convention to avoid having to rewrite it from scratch.</span></span>  <span data-ttu-id="0fd4a-136">この例では、カスタムの規則に一致する既存の Id を置き換えます。</span><span class="sxs-lookup"><span data-stu-id="0fd4a-136">An example of this is to replace the existing Id matching convention with a custom one.</span></span>   <span data-ttu-id="0fd4a-137">キーの規則をオーバーライドする追加のメリットは、既に検出または明示的に構成されているキーが存在しない場合にのみ、オーバーライドされたメソッドが呼び出さことです。</span><span class="sxs-lookup"><span data-stu-id="0fd4a-137">An added benefit to overriding the key convention is that the overridden method will get called only if there is no key already detected or explicitly configured.</span></span> <span data-ttu-id="0fd4a-138">規則の一覧は、ここで用意されている Entity Framework によって使用: [ http://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx)します。</span><span class="sxs-lookup"><span data-stu-id="0fd4a-138">A list of conventions that used by Entity Framework is available here: [http://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx).</span></span>  

``` csharp
using System.Data.Entity;
using System.Data.Entity.Core.Metadata.Edm;
using System.Data.Entity.Infrastructure;
using System.Data.Entity.ModelConfiguration.Conventions;
using System.Linq;  

// Convention to detect primary key properties.
// Recognized naming patterns in order of precedence are:
// 1. 'Key'
// 2. [type name]Key
// Primary key detection is case insensitive.
public class CustomKeyDiscoveryConvention : KeyDiscoveryConvention
{
    private const string Id = "Key";

    protected override IEnumerable<EdmProperty> MatchKeyProperty(
        EntityType entityType, IEnumerable<EdmProperty> primitiveProperties)
    {
        Debug.Assert(entityType != null);
        Debug.Assert(primitiveProperties != null);

        var matches = primitiveProperties
            .Where(p => Id.Equals(p.Name, StringComparison.OrdinalIgnoreCase));

        if (!matches.Any())
       {
            matches = primitiveProperties
                .Where(p => (entityType.Name + Id).Equals(p.Name, StringComparison.OrdinalIgnoreCase));
        }

        // If the number of matches is more than one, then multiple properties matched differing only by
        // case--for example, "Key" and "key".  
        if (matches.Count() > 1)
        {
            throw new InvalidOperationException("Multiple properties match the key convention");
        }

        return matches;
    }
}
```  

<span data-ttu-id="0fd4a-139">既存のキーの規則の前に、新しい規則を追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="0fd4a-139">We then need to add our new convention before the existing key convention.</span></span> <span data-ttu-id="0fd4a-140">CustomKeyDiscoveryConvention を追加した後、IdKeyDiscoveryConvention を削除できます。</span><span class="sxs-lookup"><span data-stu-id="0fd4a-140">After we add the CustomKeyDiscoveryConvention, we can remove the IdKeyDiscoveryConvention.</span></span>  <span data-ttu-id="0fd4a-141">既存の IdKeyDiscoveryConvention この規則が優先順位は Id の検出規則を最初が、「キー」プロパティが見つからない場合は実行されるので、削除していない場合は、"id"の規則が実行されます。</span><span class="sxs-lookup"><span data-stu-id="0fd4a-141">If we didn’t remove the existing IdKeyDiscoveryConvention this convention would still take precedence over the Id discovery convention since it is run first, but in the case where no “key” property is found, the “id” convention will run.</span></span>  <span data-ttu-id="0fd4a-142">各規則は、前の規則が何か、一致する列名を更新する場合、たとえば、ように (動作しないことで個別にではなくおよびすべてまとめて結合している)、前の規則によって更新としてにモデルを認識しているために、この動作がわかりますカスタムの規則 (前に、その名前でなかった場合に関心のある) し、その目的は、その列に適用されます。</span><span class="sxs-lookup"><span data-stu-id="0fd4a-142">We see this behavior because each convention sees the model as updated by the previous convention (rather than operating on it independently and all being combined together) so that if for example, a previous convention updated a column name to match something of interest to your custom convention (when before that the name was not of interest) then it will apply to that column.</span></span>  

``` csharp
public class BlogContext : DbContext
{
    public DbSet<Post> Posts { get; set; }
    public DbSet<Comment> Comments { get; set; }

    protected override void OnModelCreating(DbModelBuilder modelBuilder)
    {
        modelBuilder.Conventions.AddBefore<IdKeyDiscoveryConvention>(new CustomKeyDiscoveryConvention());
        modelBuilder.Conventions.Remove<IdKeyDiscoveryConvention>();
    }
}
```  

## <a name="notes"></a><span data-ttu-id="0fd4a-143">メモ</span><span class="sxs-lookup"><span data-stu-id="0fd4a-143">Notes</span></span>  

<span data-ttu-id="0fd4a-144">Entity Framework によって現在適用されている規則の一覧については、MSDN のドキュメント: [ http://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx)します。</span><span class="sxs-lookup"><span data-stu-id="0fd4a-144">A list of conventions that are currently applied by Entity Framework is available in the MSDN documentation here: [http://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx).</span></span>  <span data-ttu-id="0fd4a-145">この一覧は、ソース コードから直接取得されます。</span><span class="sxs-lookup"><span data-stu-id="0fd4a-145">This list is pulled directly from our source code.</span></span>  <span data-ttu-id="0fd4a-146">Entity Framework 6 のソース コードは 使用可能な[GitHub](https://github.com/aspnet/entityframework6/)と Entity Framework によって使用される規則の多くは、適切な規則に基づくカスタム モデルの開始点。</span><span class="sxs-lookup"><span data-stu-id="0fd4a-146">The source code for Entity Framework 6 is available on [GitHub](https://github.com/aspnet/entityframework6/) and many of the conventions used by Entity Framework are good starting points for custom model based conventions.</span></span>  
