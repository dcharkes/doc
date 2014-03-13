# Modules

## Basic module structure

An SDF3 specification consists of a number of module declarations. Each module may define sections containing lexical syntax, context-free syntax in the form of templates or SDF2 productions, disambiguations and template options. 

## Imports

Modules may import other modules for reuse or separation of concerns. A module may extend the definition of a non-terminal in another module. A module may compose the definition of a language by importing the parts of the language.
The structure of a module is as follows:

    module <ModuleName>
  
    <ImportSection*>
  
    <Section>*
  
The `module` keyword is followed by the module name, then a series of imports can be made, followed by sections that contain the actual definition of the syntax.
An import section is structed as follows:

    imports <ModuleName>*

# Grammars

## Sort declarations

Sorts are declared by listing their name in a sorts section which has the following form:

    sorts
      <Symbol>*
    
Sort names always start with a capital letter and can be followed by letters, digits or hyphens(-).

## Start symbols

The lexical or context-free start symbols sections explicitly define the symbols which will serve as start symbols when parsing terms. If no start symbols are defined it is not possible to recognize terms. This has the effect that input sentences corresponding to these symbols can be parsed. So, if we want to recognize boolean terms we have to define explicitly the sort Boolean as a start symbol in the module Booleans. Any symbol and also lists, tuples, etc., can serve as a start-symbol. A definition of lexical start symbols looks like

    lexical start-symbols
      <Symbol>*
      
while context-free start symbols are defined as

    context-free start-symbols
      <Symbol>*
      
Start symbols are short-hand notation, for which SDF generates productions as in:

    lexical start-symbols
      Identifier

generates

    <Identifier-LEX> -> <START>

and

    context-free start-symbols
      Program

generates
    
    <LAYOUT?-CF> <Program-CF> <LAYOUT?-CF> -> <START>

## Lexical syntax

The lexical syntax usually describes the low level structure of programs (often referred to as lexical tokens.) However, in SDF the token concept is not really relevant, since only character classes are terminals. The lexical syntax sections in SDF are simply a convenient notation for the low level syntax of a language. The LAYOUT symbol should also be defined in a lexical syntax section. A lexical syntax consists of a list of productions.

Lexical syntax is described as follows:

    lexical syntax
      
      <Production>*

Lexical syntax section allows the definition of rules in both reductive and productive form. However, SDF does not allow both reductive and productive rules to be mixed inside the same lexical syntax section.

An example of a lexical syntax rule written in productive form:
    
    lexical syntax
      
      BinaryConst = [0-1]+
      
And the same rule, written in reductive form:

    lexical syntax
        
      [0-1]+ -> BinaryConst   

## Context-free syntax

The context-free syntax describes the more high-level syntactic structure of sentences in a language. A context-free syntax contains a list of productions. Elements of the right-hand side of a context-free function are pre-processed before parser generation by adding the LAYOUT? symbol everywhere. Context-free syntax has the form:

    context-free syntax
      <Production>*

Similarly to the lexical syntax sections, the context-free sections allow the specification of rules in both productive and reductive forms, but it does not allow both forms to be mixed inside the same section.

An example (productive form):

    context-free syntax
      Block = "{" Statement* "}" 

SDF automatically allows for layout to be present between the symbols of a rule. This means that a fragment such as:

    {
    
    }

will still be recognized as a block (assuming that the newline and line-feed characters are defined as layout).

### Productions

The basic building block of a context-free syntax, lexical syntax or variables section is the production. Productive rules consist of a left-hand side that contains one symbol, an equals symbol (=) and a right-hand side that contains zero or more symbols and an optional list of attributes. This is summarized as follows:

    <Symbol> = <Symbol>*

The symbol on the left-hand side can be either just a sort or a sort followed by a dot (.) and a constructor name.
A production is read as the definition of a symbol. The symbol on the left-hand side is defined by the right-hand side of the production.

Productions can also be written in reductive form, which consists of a left-hand side of zero or more symbols, followed by an arrow symbol (->) and a right-hand side that consists of one symbol and an optional list of attributes:

    <Symbol>* -> <Symbol>

The symbols in a production can be arbitrarily complex but the implementation may impose some limitations on this. Productions are used to describe lexical as well as context-free syntax, variables and lexical variables. Productions also occur in priority sections. All productions with the same sort together define the alternatives for that symbol.

### Attributes

The definition of a lexical, context-free productions and variables may be followed by attributes that define additional (syntactic or semantic) properties of that function. The attributes are written between curly brackets after the non-terminal in the right hand side. If a production rule has more than one attribute they are separated by commas. Productions with attributes have thus the following form:

    <Symbol> = <Symbol>* { <Attribute1>, <Attribute2>, ...}

Or, written in reductive form:

    <Symbol>* -> <Symbol> { <Attribute1>, <Attribute2>, ...}

The following syntax-related attributes exist:

* `{bracket}` is an attribute without SDF semantics, but is important nevertheless in combination with priorities. It is not used by SDF, but some back-ends use it. For example, the restore-brackets tool uses the bracket attribute to find productions to add to a parse tree before pretty printing (when the tree violates priority constraints). Note that most of these tools demand the production with a `{bracket}` attribute to have the shape: `X = "(" X ")" {bracket}` with any kind of bracket syntax but the X being the same symbol on the left-hand side and the right-hand side. The connection with priorities and associativity is that when a non-terminal is disambiguated using either of them, a production rule with the `{bracket}` attribute is probably also needed.

* `{left}`, `{right}`, `{non-assoc}`, `{assoc}` are disambiguation constructs used to define the associativity of productions. See associativity.

* `{prefer}` and `{avoid}` are disambiguation constructs to define preference of one derivation over others. See preferences.

* `{reject}` is a disambiguation construct that implements language difference. It is used for keyword reservation. See rejections.

## Templates

Templates are an alternative way of defining productions. Similarly, they consist of a left-hand side that contains a symbol followed by equals (=) and a right hand side that contains a template section denoted by the less-than (<) and greater-than (>) signs. The template section can contain zero or more symbols and can be followed by optional attributes:

    <Symbol> = < <Symbol>* > { <Attribute1>, <Attribute2> }

Alternatively, square brackets can be used to denote the template section. Thus, the rule looks like:
    
    <Symbol> = [ <Symbol>* ] { <Attribute1>, <Attribute2> }

The symbols on the right-hand side can either be placeholders or literal strings. 
It is worth noting that: 

* placeholders need to be enclosed within the less-than (<) and greater-than (>) signs;
* literal strings need not not be enclosed within quotation marks;
* literal strings are tokenized on space characters (whitespace, tab);
* additionally, literal strings are tokenized on boundaries between characters from the set given by the tokenize option, see the tokenize template option;
* placeholders translate literally. If a separator option containing any non-layout characters is given, the placeholder maps to a list with separator.

An example of a template rule:

    Exp.Addition = < <Exp> + <Exp> >

Here, the + symbol is a literal string and <Exp> is a placeholder for the Exp sort.

Placeholders can also have a number of options:

|Syntax | Meaning|
|-------|:--------:|
| `<Sort?>` | Optional placeholder |
| `<Sort*>` | Repetition (0...n) |
| `<Sort+>` | Repetition (1...n) |
| `<Sort*; separator=",">` | Repetition with separator |
| `<Sort; text="hi">` | Placeholder with replacement text |
| `<Sort; hide>` | Placeholder hidden from completion template |

## Template options

Template options are options that are applied to the current file.
A template options section is structured as follows:

    template options
    
      <TemplateOption*>
    
Multiple template option sections are not supported. If multiple template option sections are specified, the last one is used.
    
There are three kinds of template options.

### keyword

Convenient way for setting up lexical follow restrictions for keywords. See the section on follow restrictions for more information.
The structure of the keyword option is as follows:

    keyword -/- <Pattern>
  
This will add a follow restriction on the pattern for each keyword in the language. Keywords are automatically detected, any terminal that ends with an alphanumeric character is considered a keyword.

Multiple keyword options are not supported. If multiple keyword options are specified, the last one is used.

Note that this only sets up follow restrictions, rejection of keywords as identifiers still needs to be written manually.

### tokenize

Specifies which characters may have layout around them.
The structure of a tokenize option is as follows:

    tokenize : "<Character*>"

Consider the following grammar specification:

    template options
  
      tokenize : "()"
  	
    templates
  
      Exp.Call = <<ID>();>
  	
Because layout is allowed around the `(` and `)` characters, there may be layout between `()` and `;` in the template rule. 
If no tokenize option is specified, it defaults to the default value of `()`. 

Multiple tokenise options are not supported. If multiple tokenise options are specified, the last one is used.

### newlines

Specifies that the grammar requires line feeds.
The structure is as follows:

    newlines : (none|separating|leading|trailing)
  
There are 4 modes this option can be set to:

* none: No line feeds are required. This is the default.
* separating: Line feeds are required whenever a template contains a line feed. Subsequent line feeds in a template are combined into one line feed.
* leading: In addition to separating line feeds, also requires a line feed at the beginning of each template.
* trailing: In addition to separating line feeds, also requires a line feed at the end of each template.

For example, consider the following grammar specification:

    template options
  
      newlines : separating
  	
    templates
  
      Exp.Call = <<ID>(
        
      );>

This grammar requires a line feed after the `(` character. The program `func();` is not valid, but `func(\n);` is.

Multiple newline options are not supported. If multiple newline options are specified, the last one is used.

# Disambiguation

The semantics of SDF can be seen as two-staged. First, the grammar generates all possible derivations. Second, the disambiguation constructs remove a number of derivations that are not valid. 

## Rejections

The reject disambiguation construct filters derivations. The semantics of a rejection is that the set of valid derivations for the left-hand side of the production will not contain the construction described on the right-hand side.  In other words, the language defined by the sort on the left-hand side has become smaller, removing all the constructions generated by the rule on the right-hand side.

A rule can be marked as rejected by using the attribute `{reject}` after the rule:
    
    <Sort> = ... {reject}

The `{reject}` attribute works well for lexical rejections, especially keyword reservation in the form of productions like :

    ID = "keyword" {reject}

## Preferences

The preferences mechanism is another disambiguation filter that provides a filter semantics to a production attribute. The attributes `{prefer}` and `{avoid}` are the only disambiguation constructs that compare alternative derivations. 

The following definition assumes that derivations are represented using parse forests with "packaged ambiguity nodes". This means that whenever in a derivation there is a choice for several sub-derivations, at that point a special choice node (ambiguity constructor) is placed with all alternatives as children. We assume here that the ambiguity constructor is always placed at the location where a choice is needed, and not higher (i.e. a minimal parse forest representation). The preference mechanism compares the top nodes of each alternative:

* All alternative derivations that have `{avoid}` at the top node will be removed, but only if other alternatives derivations are there that do not have `{avoid}` at the top node.
* If there are derivations that have `{prefer}` at the top node, all other derivations that do not have `{prefer}` at the top node will be removed.

The preference attribute can be used to handle the 'dangling else' problem. Here is an example:

    Exp.IfThenElse = <"if" Exp "then" Exp "else" Exp>
    Exp.IfThen     = <"if" Exp "then" Exp>  {prefer}

## Priorities

Priorities are one of SDF's most often used disambiguation constructs. A priority 'grammar' defines the relative priorities between productions. Priorities are a powerful disambiguation construct. The idea behind the semantics of priorities is that productions with a higher priority "bind stronger" than productions with a lower priority. The essence of the priority disambiguation construct is that certain parse trees are removed from the ‘forest’ (the set of all possible parse trees that can be derived from a segment of code).The basic priority syntax looks like this:

    context-free priorities
      <Production> >  <Production>

Several priorities in a priority grammar are separated by commas. If more productions have the same priority they may be grouped between curly braces on each side of the > sign.

    context-free priorities
      {<Production> <Production> }
                    >  <Production>,
       <Production>
                    >  <Production>

By default, the priority relation is automatically transitively closed (i.e. if A > B and B > C then A > C).

The priority relation applies to all arguments of the first production (i.e. in the parse tree, the second production can not be a child of any member of the first production). If A > B, then all trees are removed that have a B node as a direct child of an A node.

An example defining priorities for the addition, substraction and multiplication operators is listed below. Because addition and substaction have the same priority, the are grouped together between brackets.

    context-free priorities
      {Exp.Times} >
      {Exp.Plus Exp.Minus}

## Associativity


Like with priorities, the essence of the associativity attribute is that certain parse trees are removed from the ‘forest’. 

* The `{left}` associativity attribute on a production P filters all occurrences of P as a direct child of P in the right-most argument. This implies that `{left}` is only effective on productions that are recursive on the right (as in A B C -> C).
* The `{right}` associativity attribute on a production P filters all occurrences of P as a direct child of P in the left-most argument. This implies that `{right}` is only effective on productions that are recursive on the left ( as in C A B -> C).
* The `{non-assoc}` associativity attribute on a production P filters all occurrences of P as a direct child of P in any argument. This implement that `{non-assoc}` is only effective if a production is indeed recursive (as in A C B -> C).
* The `{assoc}` attribute means the same as `{left}`

Associativity declarations occur in two places in SDF. The first is as production attributes. The second is as associativity declarations in priority groups. 

An example on how to mention associativity as a production attribute is given below:

    Exp.Plus = <<Exp> + <Exp>> {left}

In priority groups, the associativity has the same semantics as the associativity attributes, except that the filter refers to more nested productions instead of a recursive nesting of one production. The group associativity attribute works pairwise and commutative on all combinations of productions in the group. If there is only one element in the group the attribute is reflexive, otherwise it is not reflexive.

    context-free priorities
      {left: Exp.Times} >
      {left: Exp.Plus Exp.Minus}

## Restrictions

The notion of restrictions enables the formulation of lexical disambiguation strategies. Examples are "shift before reduce" and "longest match". A restriction filters applications of productions for certain non-terminals if the following character (lookahead) is in a certain class. The result is that specific symbols may not be followed by a character from a given character class. A lookahead may consist of more than one character class (multiple lookahead). Restrictions come in two flavors:

* lexical restrictions that apply to lexical non-terminals
* context-free restrictions that apply to context-free non-terminals.

The general form of a restriction is:

    <Symbol>+ -/- <Lookaheads>

The semantics of a restriction is to remove all derivations that produce a certain `<Symbol>`. The condition for this removal is that the derivation tree for that symbol is followed immediately by something that matches the lookahead declaration. Note that to be able to check this condition, one must look past derivations that produce the empty language, until the characters to the right of the filtered symbol are found. Also, for finding multiple lookahead matches, one must ignore nullable sub-trees that may occur in the middle of the matched lookahead.

In case of lexical restrictions `<Symbol>` may be either a literal or sort. In case of context-free restrictions only a sort or symbol is allowed. The restriction operator `-/-` should be read as may not be followed by. Before the restriction operator `-/-` a list of symbols is given for which the restriction holds.

As an example, the following restriction rule implements the “longest match” policy: an identifier can not be followed by an alpha-numeric character..

    ID -/- [a-zA-Z0-9\_]
