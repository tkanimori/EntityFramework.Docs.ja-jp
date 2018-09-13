---
title: モデルに基づく規則 - EF6
author: divega
ms.date: 10/23/2016
ms.assetid: 0fc4eef8-29b8-4192-9c77-08fd33d3db3a
ms.openlocfilehash: fb79164f71cb3afff705a83f5078a13d043abca8
ms.sourcegitcommit: 2b787009fd5be5627f1189ee396e708cd130e07b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2018
ms.locfileid: "45490936"
---
# <a name="model-based-conventions"></a>モデルに基づく規則
> [!NOTE]
> **EF6 以降のみ** - このページで説明する機能、API などは、Entity Framework 6 で導入されました。 以前のバージョンを使用している場合、一部またはすべての情報は適用されません。  

モデル ベースの規則は、規則ベースのモデルの構成の高度な方法です。 ほとんどのシナリオ、[カスタム Code First 規約 API DbModelBuilder で](~/ef6/modeling/code-first/conventions/custom.md)使用する必要があります。 モデル ベースの規則を使用する前に、規則の DbModelBuilder API の理解をお勧めします。  

モデル ベースの規則は、プロパティとは、標準の規則を構成できないテーブルに影響する規則の作成を許可します。 これらの例は、階層モデルごとにテーブル内の列の識別子および独立アソシエーションの列です。  

## <a name="creating-a-convention"></a>規則を作成します。   

モデル ベースの規則を作成する最初の手順は、パイプラインで、規則は、モデルに適用する必要がある場合に選択します。 モデルの規則、(C 領域) の概念とストア (S 領域) の 2 種類があります。 C 領域の規則は、S 領域の規則が、データベースを表すモデルのバージョンに適用されますが、アプリケーションがビルドをどのように自動的に生成された列などのコントロールの名前はモデルに適用されます。  

モデルの規則は、IConceptualModelConvention または IStoreModelConvention から拡張するクラスです。  これらのインターフェイスが両方のまま使用することができるジェネリック型では、MetadataItem、規則が適用されるデータ型をフィルター処理するために使用を入力します。  

## <a name="adding-a-convention"></a>規則の追加   

モデルの規則は、通常の規則クラスと同じ方法で追加されます。 **OnModelCreating**メソッドでは、モデルの規則の一覧に、規則を追加します。  

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

Conventions.AddBefore を使用して別の規則に関連する規則を追加することも\<\>または Conventions.AddAfter\< \>メソッド。 Entity Framework が適用される規則に関する詳細については、ノートのセクションを参照してください。  

``` csharp
protected override void OnModelCreating(DbModelBuilder modelBuilder)
{
    modelBuilder.Conventions.AddAfter<IdKeyDiscoveryConvention>(new MyModelBasedConvention());
}
```  

## <a name="example-discriminator-model-convention"></a>例: 識別子モデル規則  

EF によって生成された列の名前は、何か他の規則は、Api を使用して行うことはできませんの例です。  これは、状況が唯一の選択肢は、モデルの規則を使用します。  

モデル ベースの規則を使用して生成された列を構成する方法の例では、識別子列の名前は、方法をカスタマイズします。  "EntityType"を「識別子」をという名前のモデルの各列の名前を変更する規則に基づく単純なモデルの例を次に示します。  これには、開発者は、「識別子」を単にという列が含まれます。 「識別子」列は生成された列であるためこれ S 領域で実行する必要があります。  

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

## <a name="example-general-ia-renaming-convention"></a>例: 一般的な IA の規則の名前を変更します。   

別のより複雑なアクションに基づくモデル規則の例では、独立した関連付け (IAs)、名前をする方法を構成します。  これは、モデルの規則が IAs が EF によって生成されるため、適用し、DbModelBuilder API にアクセスできるモデルではない状況です。  

EF では、IA を生成するときに、EntityType_KeyName という名前の列を作成します。 たとえば、キー列を含む顧客をという名前のアソシエーションの customerid Customer_CustomerId という名前の列が生成されます。 次の規則ストリップ、'\_'、指定された列名からの文字  

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

## <a name="extending-existing-conventions"></a>既存の規則を拡張します。   

Entity Framework は、既にモデルに適用される規則のいずれかのような規則を記述する必要がある場合は、最初から修正することを回避するには、その規則を常に拡張できます。  この例では、カスタムの規則に一致する既存の Id を置き換えます。   キーの規則をオーバーライドする追加のメリットは、既に検出または明示的に構成されているキーが存在しない場合にのみ、オーバーライドされたメソッドが呼び出さことです。 規則の一覧は、ここで用意されている Entity Framework によって使用: [ http://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx](http://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx)します。  

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

既存のキーの規則の前に、新しい規則を追加する必要があります。 CustomKeyDiscoveryConvention を追加した後、IdKeyDiscoveryConvention を削除できます。  既存の IdKeyDiscoveryConvention この規則が優先順位は Id の検出規則を最初が、「キー」プロパティが見つからない場合は実行されるので、削除していない場合は、"id"の規則が実行されます。  各規則は、前の規則が何か、一致する列名を更新する場合、たとえば、ように (動作しないことで個別にではなくおよびすべてまとめて結合している)、前の規則によって更新としてにモデルを認識しているために、この動作がわかりますカスタムの規則 (前に、その名前でなかった場合に関心のある) し、その目的は、その列に適用されます。  

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

## <a name="notes"></a>メモ  

Entity Framework によって現在適用されている規則の一覧については、MSDN のドキュメント: [ http://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx](http://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx)します。  この一覧は、ソース コードから直接取得されます。  Entity Framework 6 のソース コードは 使用可能な[GitHub](https://github.com/aspnet/entityframework6/)と Entity Framework によって使用される規則の多くは、適切な規則に基づくカスタム モデルの開始点。  
