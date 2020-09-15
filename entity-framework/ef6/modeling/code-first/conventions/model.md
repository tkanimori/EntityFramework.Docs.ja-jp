---
title: モデルベースの規則-EF6
description: Entity Framework 6 のモデルベースの規則
author: divega
ms.date: 10/23/2016
uid: ef6/modeling/code-first/conventions/model
ms.openlocfilehash: bcb7a88f4d9fc994b51300ac30ec58671be370c8
ms.sourcegitcommit: abda0872f86eefeca191a9a11bfca976bc14468b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/14/2020
ms.locfileid: "90074004"
---
# <a name="model-based-conventions"></a>モデルベースの規則
> [!NOTE]
> **EF6 以降のみ** - このページで説明する機能、API などは、Entity Framework 6 で導入されました。 以前のバージョンを使用している場合、一部またはすべての情報は適用されません。  

モデルベースの規則は、規則ベースのモデル構成の高度な手法です。 ほとんどのシナリオでは、 [DbModelBuilder のカスタム Code First 規約 API](xref:ef6/modeling/code-first/conventions/custom) を使用する必要があります。 モデルベースの規約を使用する前に、DbModelBuilder API に関する規則について理解しておくことをお勧めします。  

モデルベースの規則を使用すると、標準規則では構成できないプロパティやテーブルに影響する規則を作成できます。 これらの例は、テーブルの階層モデルごとの識別子列と、独立した関連付け列です。  

## <a name="creating-a-convention"></a>規則の作成   

モデルベースの規則を作成する最初の手順として、パイプラインでは、モデルに適用する必要がある規則を選択します。 モデル規則には、概念 (C 空間) とストア (S スペース) の2種類があります。 C 空間規則はアプリケーションがビルドするモデルに適用されますが、S 空間規則は、データベースを表すモデルのバージョンに適用され、自動生成された列の名前付けなどの処理を制御します。  

モデル規則は、IConceptualModelConvention または IStoreModelConvention から拡張するクラスです。  これらのインターフェイスは両方とも、規則が適用されるデータ型をフィルター処理するために使用される MetadataItem 型のジェネリック型を受け入れます。  

## <a name="adding-a-convention"></a>規則の追加   

モデル規則は、通常の規則クラスと同じ方法で追加されます。 **Onmodelcreating**メソッドで、規則をモデルの規則の一覧に追加します。  

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

規則は、規則を使用して別の規則に関連して追加することもできます。 AddBefore \<\> またはコンベンション。 Addbefore \<\> メソッド。 Entity Framework 適用される規則の詳細については、「メモ」を参照してください。  

``` csharp
protected override void OnModelCreating(DbModelBuilder modelBuilder)
{
    modelBuilder.Conventions.AddAfter<IdKeyDiscoveryConvention>(new MyModelBasedConvention());
}
```  

## <a name="example-discriminator-model-convention"></a>例: 識別子モデル規則  

EF によって生成される列の名前を変更する方法の例としては、他の規則の Api では実行できないものがあります。  これは、モデル規則の使用が唯一のオプションである状況です。  

モデルベースの規則を使用して生成された列を構成する方法の例として、識別子列の名前付け方法をカスタマイズすることがあります。  "識別子" という名前のモデル内のすべての列の名前を "EntityType" に変更する単純なモデルベースの規則の例を次に示します。  これには、開発者が "識別子" という単純な列が含まれます。 "識別子" 列は、生成された列であるため、S スペースで実行する必要があります。  

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

## <a name="example-general-ia-renaming-convention"></a>例: 一般的な IA 名前変更規則   

もう1つの複雑なモデルベースの規則の例として、独立した関連付け (IAs) の名前付け方法を構成します。  これは、IAs が EF によって生成され、DbModelBuilder API がアクセスできるモデルに存在しないため、モデルの規則が適用される状況です。  

EF が IA を生成すると、EntityType_KeyName という名前の列が作成されます。 たとえば、CustomerId という名前のキー列を持つ Customer という名前のアソシエーションの場合、Customer_CustomerId という名前の列が生成されます。 次の規則は、 \_ IA 用に生成された列名から ' ' 文字を除去します。  

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

## <a name="extending-existing-conventions"></a>既存の規則の拡張   

Entity Framework 既にモデルに適用されている規則と同様の規則を作成する必要がある場合は、その規則を常に拡張して、最初から書き換える必要がないようにすることができます。  この例としては、既存の Id 一致規則をカスタムの規則に置き換えることが挙げられます。   キーの規則をオーバーライドする利点は、オーバーライドされたメソッドが、既に検出されていないか、明示的に構成されている場合にのみ呼び出されることです。 Entity Framework によって使用される規則の一覧については、「」を参照 [http://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx) してください。  

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

次に、既存のキー規則の前に新しい規則を追加する必要があります。 CustomKeyDiscoveryConvention を追加した後、IdKeyDiscoveryConvention を削除できます。  既存の IdKeyDiscoveryConvention を削除しなかった場合、この規則は最初に実行されるため、Id 検出規則よりも優先されますが、"key" プロパティが見つからない場合は、"id" 規則が実行されます。  この動作が見られるのは、それぞれの規則によってモデルが前の規則によって更新されていることを示しているためです。たとえば、前の規則によって、(名前が不要になったときに) カスタム規則と一致するように列名が更新された場合、その列に適用されます。  

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

Entity Framework によって現在適用されている規則の一覧については、MSDN ドキュメントの「」を参照して [http://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx](https://msdn.microsoft.com/library/system.data.entity.modelconfiguration.conventions.aspx) ください。  このリストは、ソースコードから直接プルされます。  Entity Framework 6 のソースコードは [GitHub](https://github.com/aspnet/entityframework6/) で入手でき、Entity Framework によって使用される規則の多くは、カスタムモデルベースの規則の出発点として適しています。  
