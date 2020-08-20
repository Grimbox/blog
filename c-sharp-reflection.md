---
title: Définir des CustomAttributes en C# et les récupérer par introspection
---

Le premier point consiste à définir une nouvelle classe qui représentera nos attributs. 
Dans l'exemple ci-dessous, on souhaite les utiliser uniquement sur des propriétés, raison pour laquelle on utilisera un attribut (aussi...) 
de type `System.AttributeUsage(AttributeTargets.Property)`.

Par exemple:

```csharp
public class RedCapAttribute : System.Attribute
{
    public string Label { get; set; }
    public string Description { get; set; }
    public bool Temporal { get; set; }
    public string Category { get; set; }
    public string Identifier { get; set; }

    public RedCapAttribute(string label, string description, bool temporal, string category, string identifier)
    {
        this.Label = label;
        this.Description = description;
        this.Temporal = temporal;
        this.Category = category;
        this.Identifier = identifier;
    }
}
```

Les différentes valeurs de propriétés qui composent cet attribut pourront ensuite être définies directement au niveau de la propriété de l'objet.

```csharp
public class People
{
    [RedCap("Nom", "Nom du patient", false, "Demographics", "0")]
    public string LastName { get; set; }

    [RedCap("Prénom", "Prénom du patient", false, "Demographics", "0")]
    public string FirstName { get; set; }

    [RedCap("ID Patient", "ID patient", false, "Demographics", "1")]
    public string PatientId { get; set; }
}
```

Pour récupérer dynamiquement les différentes propriétés de nos attributs, on peut ensuite utiliser la DLL System.Reflection. 

```csharp
// On récupère les propriétés de la classe `People` pour lesquelles un attribut de type `RedCapAttribute` a été défini
IEnumerable<PropertyInfo> property_infos = typeof(People).GetProperties().Where(p => p.GetCustomAttribute(typeof(RedCapAttribute), true) != null);

// Pour chacune de ces propriétés, on récupère le premier attribut de type RedCapAttribute
// et on la convertit vers la bonne classe.
foreach (var property in property_infos)
{
    var custom_attribute = property.GetCustomAttributes().FirstOrDefault();                

    var redcap_custom_attribute = custom_attribute as RedCapAttribute;
}
```

Il suffit ensuite d'accéder aux différentes propriétés de l'instance ´redcap_custom_attribute`.
