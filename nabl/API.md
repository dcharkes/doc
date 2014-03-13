# Stratego API

## Name Binding Information in Terms

### The Index

Terms lack a native concept to represent cross-references. 
Spoofax maintains name binding information and associated data such as type information in a global environment called *the index*, which is shared by various transformations.
By collecting all this information about files in a project together, it ensures fast access to global information (in particular, to-be-referenced names). The index is updated automatically when files change (or are deleted) and is persisted as Eclipse exits. 

### URIs

All entries in the index have a *URI* which uniquely identifies the element across a project. 
These URIs are the basis for name resolution, and, by default, are constructed automatically, based on the name binding rules. 
As an example, consider the following entity:

    module storage

    entity Store {
        name : String 
        address : Address
    }￼￼￼￼￼￼￼￼￼￼￼

Following the name binding rules discussed so far, there are two scope levels in this fragment: 
one at the module level and one at the entity level. 
We can assign names to these scopes (`storage` and `Store`) by using the naming rules for modules and entities. 
By creating a hierarchy of these names, Spoofax creates URIs: 
the URI for `Store` is `Entity://storage.Store`, and the one for `name` is `Property:// storage.Store.name`. 
URIs are represented internally as lists of terms, that start with the namespace, followed by a reverse hierarchy of the path names. 
The reverse order used in the representation makes it easier to efficiently store and manipulate URIs in memory: 
every tail of such a list can share the same memory space.
For the `name` property of the `Store` entity in the storage module, this would be:

    [Property(), "name", "Store", "storage"]

### Annotated URIs

Spoofax annotates each definition and reference with a URI to connect names with information stored in the index. 
References are annotated with the same URI as their definition. 
This way, information about the definition site are also available at the reference. 
References that cannot be resolved are annotated with a special `Unresolved` constructor. 
For example, an unresolved variable could be represented as

    Var("x"{[Unresolved(Var()),"x",...]})

We can inspect URIs in Spoofax’ analyzed syntax view. 
This view shows the abstract syntax with all URIs as annotations. 

## Query Name Information

###### index-uri

###### index-namespace

## Lookup Names

###### index-lookup

###### index-lookup-all

###### index-get-files-of

###### index-get-all-in-file

###### index-get-current-file

## Associated Data
