---
title: モデルベースの規則-EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 0fc4eef8-29b8-4192-9c77-08fd33d3db3a
ms.openlocfilehash: c873e9a216bd9bd1934f2149ae6af602072f3608
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78415845"
---
# <a name="model-based-conventions"></a><span data-ttu-id="1d20f-102">モデルベースの規則</span><span class="sxs-lookup"><span data-stu-id="1d20f-102">Model-Based Conventions</span></span>
> [!NOTE]
> <span data-ttu-id="1d20f-103">**EF6 以降のみ** - このページで説明する機能、API などは、Entity Framework 6 で導入されました。</span><span class="sxs-lookup"><span data-stu-id="1d20f-103">**EF6 Onwards Only** - The features, APIs, etc. discussed in this page were introduced in Entity Framework 6.</span></span> <span data-ttu-id="1d20f-104">以前のバージョンを使用している場合、一部またはすべての情報は適用されません。</span><span class="sxs-lookup"><span data-stu-id="1d20f-104">If you are using an earlier version, some or all of the information does not apply.</span></span>  

<span data-ttu-id="1d20f-105">モデルベースの規則は、規則ベースのモデル構成の高度な手法です。</span><span class="sxs-lookup"><span data-stu-id="1d20f-105">Model based conventions are an advanced method of convention based model configuration.</span></span> <span data-ttu-id="1d20f-106">ほとんどのシナリオでは、 [DbModelBuilder のカスタム Code First 規約 API](~/ef6/modeling/code-first/conventions/custom.md)を使用する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1d20f-106">For most scenarios the [Custom Code First Convention API on DbModelBuilder](~/ef6/modeling/code-first/conventions/custom.md) should be used.</span></span> <span data-ttu-id="1d20f-107">モデルベースの規約を使用する前に、DbModelBuilder API に関する規則について理解しておくことをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="1d20f-107">An understanding of the DbModelBuilder API for conventions is recommended before using model based conventions.</span></span>  

<span data-ttu-id="1d20f-108">モデルベースの規則を使用すると、標準規則では構成できないプロパティやテーブルに影響する規則を作成できます。</span><span class="sxs-lookup"><span data-stu-id="1d20f-108">Model based conventions allow the creation of conventions that affect properties and tables which are not configurable through standard conventions.</span></span> <span data-ttu-id="1d20f-109">これらの例は、テーブルの階層モデルごとの識別子列と、独立した関連付け列です。</span><span class="sxs-lookup"><span data-stu-id="1d20f-109">Examples of these are discriminator columns in table per hierarchy models and Independent Association columns.</span></span>  

## <a name="creating-a-convention"></a><span data-ttu-id="1d20f-110">規則の作成</span><span class="sxs-lookup"><span data-stu-id="1d20f-110">Creating a Convention</span></span>   

<span data-ttu-id="1d20f-111">モデルベースの規則を作成する最初の手順として、パイプラインでは、モデルに適用する必要がある規則を選択します。</span><span class="sxs-lookup"><span data-stu-id="1d20f-111">The first step in creating a model based convention is choosing when in the pipeline the convention needs to be applied to the model.</span></span> <span data-ttu-id="1d20f-112">モデル規則には、概念 (C 空間) とストア (S スペース) の2種類があります。</span><span class="sxs-lookup"><span data-stu-id="1d20f-112">There are two types of model conventions, Conceptual (C-Space) and Store (S-Space).</span></span> <span data-ttu-id="1d20f-113">C 空間規則はアプリケーションがビルドするモデルに適用されますが、S 空間規則は、データベースを表すモデルのバージョンに適用され、自動生成された列の名前付けなどの処理を制御します。</span><span class="sxs-lookup"><span data-stu-id="1d20f-113">A C-Space convention is applied to the model that the application builds, whereas an S-Space convention is applied to the version of the model that represents the database and controls things such as how automatically-generated columns are named.</span></span>  

<span data-ttu-id="1d20f-114">モデル規則は、IConceptualModelConvention または IStoreModelConvention から拡張するクラスです。</span><span class="sxs-lookup"><span data-stu-id="1d20f-114">A model convention is a class that extends from either IConceptualModelConvention or IStoreModelConvention.</span></span>  <span data-ttu-id="1d20f-115">これらのインターフェイスは両方とも、規則が適用されるデータ型をフィルター処理するために使用される MetadataItem 型のジェネリック型を受け入れます。</span><span class="sxs-lookup"><span data-stu-id="1d20f-115">These interfaces both accept a generic type that can be of type MetadataItem which is used to filter the data type that the convention applies to.</span></span>  

## <a name="adding-a-convention"></a><span data-ttu-id="1d20f-116">規則の追加</span><span class="sxs-lookup"><span data-stu-id="1d20f-116">Adding a Convention</span></span>   

<span data-ttu-id="1d20f-117">モデル規則は、通常の規則クラスと同じ方法で追加されます。</span><span class="sxs-lookup"><span data-stu-id="1d20f-117">Model conventions are added in the same way as regular conventions classes.</span></span> <span data-ttu-id="1d20f-118">**Onmodelcreating**メソッドで、規則をモデルの規則の一覧に追加します。</span><span class="sxs-lookup"><span data-stu-id="1d20f-118">In the **OnModelCreating** method, add the convention to the list of conventions for a model.</span></span>  

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

<span data-ttu-id="1d20f-119">規則は、規則を使用して別の規則に関連して追加することもできます。 AddBefore\<\> または規則です。 Addbefore\<\> メソッド。</span><span class="sxs-lookup"><span data-stu-id="1d20f-119">A convention can also be added in relation to another convention using the Conventions.AddBefore\<\> or Conventions.AddAfter\<\> methods.</span></span> <span data-ttu-id="1d20f-120">Entity Framework 適用される規則の詳細については、「メモ」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1d20f-120">For more information about the conventions that Entity Framework applies see the notes section.</span></span>  

``` csharp
protected override void OnModelCreating(DbModelBuilder modelBuilder)
{
    modelBuilder.Conventions.AddAfter<IdKeyDiscoveryConvention>(new MyModelBasedConvention());
}
```  

## <a name="example-discriminator-model-convention"></a><span data-ttu-id="1d20f-121">例: 識別子モデル規則</span><span class="sxs-lookup"><span data-stu-id="1d20f-121">Example: Discriminator Model Convention</span></span>  

<span data-ttu-id="1d20f-122">EF によって生成される列の名前を変更する方法の例としては、他の規則の Api では実行できないものがあります。</span><span class="sxs-lookup"><span data-stu-id="1d20f-122">Renaming columns generated by EF is an example of something that you can’t do with the other conventions APIs.</span></span>  <span data-ttu-id="1d20f-123">これは、モデル規則の使用が唯一のオプションである状況です。</span><span class="sxs-lookup"><span data-stu-id="1d20f-123">This is a situation where using model conventions is your only option.</span></span>  

<span data-ttu-id="1d20f-124">モデルベースの規則を使用して生成された列を構成する方法の例として、識別子列の名前付け方法をカスタマイズすることがあります。</span><span class="sxs-lookup"><span data-stu-id="1d20f-124">An example of how to use a model based convention to configure generated columns is customizing the way discriminator columns are named.</span></span>  <span data-ttu-id="1d20f-125">"識別子" という名前のモデル内のすべての列の名前を "EntityType" に変更する単純なモデルベースの規則の例を次に示します。</span><span class="sxs-lookup"><span data-stu-id="1d20f-125">Below is an example of a simple model based convention that renames every column in the model named “Discriminator” to “EntityType”.</span></span>  <span data-ttu-id="1d20f-126">これには、開発者が "識別子" という単純な列が含まれます。</span><span class="sxs-lookup"><span data-stu-id="1d20f-126">This includes columns that the developer simply named “Discriminator”.</span></span> <span data-ttu-id="1d20f-127">"識別子" 列は、生成された列であるため、S スペースで実行する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1d20f-127">Since the “Discriminator” column is a generated column this needs to run in S-Space.</span></span>  

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

## <a name="example-general-ia-renaming-convention"></a><span data-ttu-id="1d20f-128">例: 一般的な IA 名前変更規則</span><span class="sxs-lookup"><span data-stu-id="1d20f-128">Example: General IA Renaming Convention</span></span>   

<span data-ttu-id="1d20f-129">もう1つの複雑なモデルベースの規則の例として、独立した関連付け (IAs) の名前付け方法を構成します。</span><span class="sxs-lookup"><span data-stu-id="1d20f-129">Another more complicated example of model based conventions in action is to configure the way that Independent Associations (IAs) are named.</span></span>  <span data-ttu-id="1d20f-130">これは、IAs が EF によって生成され、DbModelBuilder API がアクセスできるモデルに存在しないため、モデルの規則が適用される状況です。</span><span class="sxs-lookup"><span data-stu-id="1d20f-130">This is a situation where Model conventions are applicable because IAs are generated by EF and aren’t present in the model that the DbModelBuilder API can access.</span></span>  

<span data-ttu-id="1d20f-131">EF が IA を生成すると、EntityType_KeyName という名前の列が作成されます。</span><span class="sxs-lookup"><span data-stu-id="1d20f-131">When EF generates an IA, it creates a column named EntityType_KeyName.</span></span> <span data-ttu-id="1d20f-132">たとえば、CustomerId という名前のキー列を持つ Customer という名前のアソシエーションの場合、Customer_CustomerId という名前の列が生成されます。</span><span class="sxs-lookup"><span data-stu-id="1d20f-132">For example, for an association named Customer with a key column named CustomerId it would generate a column named Customer_CustomerId.</span></span> <span data-ttu-id="1d20f-133">次の規則は、IA 用に生成された列名から '\_' 文字を除去します。</span><span class="sxs-lookup"><span data-stu-id="1d20f-133">The following convention strips the ‘\_’ character out of the column name that is generated for the IA.</span></span>  

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
        for (int i = 0; i < properties.Count; ++i)
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

## <a name="extending-existing-conventions"></a><span data-ttu-id="1d20f-134">既存の規則の拡張</span><span class="sxs-lookup"><span data-stu-id="1d20f-134">Extending Existing Conventions</span></span>   

<span data-ttu-id="1d20f-135">Entity Framework 既にモデルに適用されている規則と同様の規則を作成する必要がある場合は、その規則を常に拡張して、最初から書き換える必要がないようにすることができます。</span><span class="sxs-lookup"><span data-stu-id="1d20f-135">If you need to write a convention that is similar to one of the conventions that Entity Framework already applies to your model you can always extend that convention to avoid having to rewrite it from scratch.</span></span>  <span data-ttu-id="1d20f-136">この例としては、既存の Id 一致規則をカスタムの規則に置き換えることが挙げられます。</span><span class="sxs-lookup"><span data-stu-id="1d20f-136">An example of this is to replace the existing Id matching convention with a custom one.</span></span>   <span data-ttu-id="1d20f-137">キーの規則をオーバーライドする利点は、オーバーライドされたメソッドが、既に検出されていないか、明示的に構成されている場合にのみ呼び出されることです。</span><span class="sxs-lookup"><span data-stu-id="1d20f-137">An added benefit to overriding the key convention is that the overridden method will get called only if there is no key already detected or explicitly configured.</span></span> <span data-ttu-id="1d20f-138">Entity Framework によって使用される規則の一覧については、「 [http://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1d20f-138">A list of conventions that used by Entity Framework is available here: [http://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx).</span></span>  

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

<span data-ttu-id="1d20f-139">次に、既存のキー規則の前に新しい規則を追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1d20f-139">We then need to add our new convention before the existing key convention.</span></span> <span data-ttu-id="1d20f-140">CustomKeyDiscoveryConvention を追加した後、IdKeyDiscoveryConvention を削除できます。</span><span class="sxs-lookup"><span data-stu-id="1d20f-140">After we add the CustomKeyDiscoveryConvention, we can remove the IdKeyDiscoveryConvention.</span></span>  <span data-ttu-id="1d20f-141">既存の IdKeyDiscoveryConvention を削除しなかった場合、この規則は最初に実行されるため、Id 検出規則よりも優先されますが、"key" プロパティが見つからない場合は、"id" 規則が実行されます。</span><span class="sxs-lookup"><span data-stu-id="1d20f-141">If we didn’t remove the existing IdKeyDiscoveryConvention this convention would still take precedence over the Id discovery convention since it is run first, but in the case where no “key” property is found, the “id” convention will run.</span></span>  <span data-ttu-id="1d20f-142">この動作が見られるのは、規則ごとにモデルが前の規則によって更新されていることを示しているためです。たとえば、前の規則では、次のいずれかに一致するように列名が更新されたとします。(名前が関心のない前に) カスタム規則に関心がある場合は、その列に適用されます。</span><span class="sxs-lookup"><span data-stu-id="1d20f-142">We see this behavior because each convention sees the model as updated by the previous convention (rather than operating on it independently and all being combined together) so that if for example, a previous convention updated a column name to match something of interest to your custom convention (when before that the name was not of interest) then it will apply to that column.</span></span>  

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

## <a name="notes"></a><span data-ttu-id="1d20f-143">メモ</span><span class="sxs-lookup"><span data-stu-id="1d20f-143">Notes</span></span>  

<span data-ttu-id="1d20f-144">Entity Framework によって現在適用されている規則の一覧については、MSDN のドキュメント「 [http://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx)」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="1d20f-144">A list of conventions that are currently applied by Entity Framework is available in the MSDN documentation here: [http://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx).</span></span>  <span data-ttu-id="1d20f-145">このリストは、ソースコードから直接プルされます。</span><span class="sxs-lookup"><span data-stu-id="1d20f-145">This list is pulled directly from our source code.</span></span>  <span data-ttu-id="1d20f-146">Entity Framework 6 のソースコードは[GitHub](https://github.com/aspnet/entityframework6/)で入手でき、Entity Framework によって使用される規則の多くは、カスタムモデルベースの規則の出発点として適しています。</span><span class="sxs-lookup"><span data-stu-id="1d20f-146">The source code for Entity Framework 6 is available on [GitHub](https://github.com/aspnet/entityframework6/) and many of the conventions used by Entity Framework are good starting points for custom model based conventions.</span></span>  
