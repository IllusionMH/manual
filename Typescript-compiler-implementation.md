# Compiler Manual

Welcome to the compiler manual. It details the compiler implementation
and its philosophy. Because it focusses on implementation, it's
necessarily out-of-date and incomplete. Also, in this version, I
totally make guesses about things I'm not sure about.

## Parser

It's a recursive descent parser. It's pretty resilient, so if you
search for functions matching the thing you want to change, you can
probably get away with just adding the code to parse. There aren't any
surprises in the general implementation style here.

TODO: More here later.

## Binder

It gathers symbols from files. There's one created when you start the
compiler, and that's it. It creates symbols for
declarations so that the checker can stick types on them.

TODO: This is woefully incomplete.

TODO: It also sets up some data structures that the checker uses, like
control flow.

## Checker

The checker makes sure the types are right. 'Nuff said!

Actually, it's
20,000 lines long, and does almost everything that's not syntactic.
Surprisingly, a checker gets created every time the language service
requests information because it tries to present an immutable
interface. This works because the checker is very lazy.

### Survey

This is a boring in-order survey of what's in the checker. I'll use
this list to see what can be moved out to a separate file.

1. `nextSymbolId, nextNodeId, nextMergeId, nextFlowId` and associated
   `get` functions. These are core to the state of the checker, so
   should not be moved. (Or moved very carefully.)
2. `createTypeChecker`. This creates a checker. Don't move this!
3. getEmitResolver and error. Miscellaneous?
4. createSymbol to addToSymbolTable. Manage Symbols and SymbolTables.
5. getSymbolLinks, getNodeLinks. Manage the look-aside tables the
   checker uses to avoid modifying binder data structures.
6. isGlobalSourceFile. ???
7. getSymbol, getSymbolsOfParameterPropertyDeclaration. More SymbolTable -- look up in SymbolTable, but with
   resolving aliases.
8. isBlockScopedNameDeclaredBeforeUse. What it says on the tin. Weird
   placement tho.
9. resolveName. Resolve a Node to a Symbol. ...and that's 5% done!
10. checkAndReportErrorForMissingPrefix. Nice error for forgotten
   'this'.
11. checkResolvedBlockScopedVariable. error based on isBlockScopedNameDeclaredBeforeUse.
12. isSameScopeDescendentOf, getAnyImportSyntax, getDeclarationOfAliasSymbol. Misc???
13. getTargetOfImportEqualsDeclaration to getTargetOfNamespaceImport.
   Resolve import Nodes to Symbols. Relies mostly on
   resolveExternalModuleName, which is a bit lower.
14. combineValueAndTypeSymbols. Merge symbols.
15. getExportOfModule, getPropertyOfVariable. These don't belong --
   they are just complex specific helper code on Symbol.
16. getExternalModuleMember to getTargetOfAliasDeclaration. Goes with
   other getTarget* functions.
17. resolveSymbol, resolveAlias. resolve aliases (from modules?) to aliases?
18. markExportAsReferenced, markAliasSymbolAsReferenced. Out of place.
   Used way lower, when checking imports.
19. getSymbolOfPartOfRightHandSideOfImportEquals to (at least)
   resolveESModuleSymbol. The actual code behind getTarget* code.
20. hasExportAssignmentSymbol to getExportsForModule. Find exports of various symbols, particularly modules.
21. getMergedSymbol to symbolOfValue. Utilities on Symbol.
22. findConstructorDeclaration. ooplace, but what it says on the tin.
23. createType to createObjectType. Create types.
24. isReservedMemberName, getNamedMembers. An interleaving of utilities to do with symbols again.
25. setObjectTypeMembers, createAnonymousType. Create/update types.
26. forEachSymbolTableInScope. Another Symbol/SymbolTable utility.
27. getQualifiedLeftMeaning to isEntityNameVisible. Figure out symbols in modules.
28. writeKeyword to getTypeAliasForTypeLiteral, plus getSymbolDisplayBuilder. Display symbols. Lots of code here.
29. isTopLevelInExternalModuleAugmentation. Sort of detector for module augmentations. Purely
syntactic and definitely out of place -- called thousands of lines later.
30. isDeclarationVisible, collectLinkedAliases plus getDeclarationContainer. More module information.
31. pushTypeResolution to popTypeResolution. Core type resolution machinery.
32. getDeclarationContainer. Out of place, see above.
33. getTypeOfPrototypeProperty to at least getTargetType. Type creators and associated utilities.


## Transformer

The transformer recently replaced the emitter. It replaces the
*emitter*, which translated TypeScript to JavaScript. The
*transformer* transforms TypeScript or JavaScript (various versions)
to JavaScript (various versions) using various module systems. The
input and output are basically both trees from the same AST type, just
using different features.

The emitter is then a fairly small AST printer that can print
any TypeScript AST.

TODO: This doesn't cover any implementation details!

## Services

I don't know anything about services. And besides, this readme is for
the compiler.

## Other files

This is an unsorted inventory. Some of these deserve an in-depth
explanation, or at least a big overview of what they mean.

1. core
2. utilities
3. program
4. scanner
5. sourcemap
6. sys
7. tsc
8. types
8. commandLineParser
9. declarationEmitter
