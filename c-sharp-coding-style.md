C# Coding Style
===============

### Whitespace and line breaks:
1. We use [Allman style](http://en.wikipedia.org/wiki/Indent_style#Allman_style) braces, where each brace begins on a new line. A single line statement block can go without braces but the block must be properly indented on its own line and must not be nested in other statement blocks that use braces (See rule 6 for more details). One exception is that a `using` statement is permitted to be nested within another `using` statement by starting on the following line at the same indentation level, even if the nested `using` contains a controlled block.
2. We use four spaces of indentation (no tabs).
3. Avoid more than one empty line at any time. For example, do not have two blank lines between members of a type.
4. Avoid spurious free spaces. For example avoid `if (someVar == 0)...`, where the dots mark the spurious free spaces. Consider enabling "View White Space (Ctrl+R, Ctrl+W)" or "Edit -> Advanced -> View White Space" if using Visual Studio to aid detection.
5. When using labels (for goto), indent the label one less than the current indentation.
6. When using a single-statement if, we follow these conventions:
    - Never use single-line form (for example: `if (source == null) throw new ArgumentNullException("source");`)
    - Using braces is always accepted, and required if any block of an `if`/`else if`/.../`else` compound statement uses braces or if a single statement body spans multiple lines.
    - Braces may be omitted only if the body of *every* block associated with an `if`/`else if`/.../`else` compound statement is placed on a single line.

### Naming:
#### Code
1. We use `PascalCase` for all method names, including local functions.
2. We use `PascalCase` to name all our constant local variables and fields. The only exception is for interop code where the constant value should exactly match the name and value of the code you are calling via interop.
3. We use `_camelCase` for internal and private fields and use `readonly` where possible. Prefix internal and private instance fields with `_`. Public fields should be used sparingly and should use `PascalCase` with no prefix when used.
    - Names of classes, methods, enumerations, public fields, public properties, namespaces: `PascalCase`.
    - Names of local variables, parameters: `camelCase`.
    - Names of private, protected, internal and protected internal fields and properties: `_camelCase`.
    - Names of interfaces start with `I`, e.g. `IInterface`.
4. Naming convention is unaffected by modifiers such as const, static, readonly, etc.
5. If a file happens to differ in style from these guidelines (e.g. private members are named `m_member` rather than `_member`), the existing style in that file takes precedence.
6. For casing, a "word" is anything written without internal spaces, including acronyms. For example, `MyRpc` instead of ~~`MyRPC`~~. The only exception is for acronyms consisting of two letters, for example `RabbitMQ`.
7. We use ```nameof(...)``` instead of ```"..."``` whenever possible and relevant.

#### Files
1. Filenames and directory names are `PascalCase`, e.g. `MyFile.cs`.
2. Where possible the file name should be the same as the name of the main class in the file, e.g. `MyClass.cs`.
3. In general, prefer one core class per file.

### Organization:
1. Modifiers occur in the following order: `public protected internal private new abstract virtual override sealed static readonly extern unsafe volatile async`.
2. Make all internal and private types static or sealed unless derivation from them is required.  As with any implementation detail, they can be changed if/when derivation is required in the future. 
3. Namespace imports should be specified at the top of the file, *outside* of `namespace` declarations, and should be sorted alphabetically, with the exception of `System.*` namespaces, which are to be placed on top of all others.
4. Class member ordering:
    - Group class members in the following order:
        - Nested classes, enums, delegates and events.
        - Static, const and readonly fields.
        - Fields and properties that are used by constructors.
        - Constructors and finalizers.
        - Fields and properties that are used independently or by methods.
        - Methods.
    - If one function calls another, they should be vertically close, and the caller should be above the callee, if at all possible.
    - ~~Within each group, elements should be in the following order:~~
        - ~~Public.~~
        - ~~Internal.~~
        - ~~Protected internal.~~
        - ~~Protected.~~
        - ~~Private.~~
    - Where possible, group interface implementations together.
4. We avoid `this.` unless absolutely necessary.
5. We always specify the visibility, even if it's the default (e.g. `private string _foo` not `string _foo`). Visibility should be the first modifier (e.g. `public abstract` not `abstract public`).
6. We only use `var` when the type is explicitly named on the right-hand side, typically due to either `new` or an explicit cast, e.g. `var stream = new FileStream(...)` not `var stream = OpenStandardInput()`.
    - Similarly, target-typed `new()` can only be used when the type is explicitly named on the left-hand side, in a variable definition statement or a field definition statement. e.g. `FileStream stream = new(...);`, but not `stream = new(...);` (where the type was specified on a previous line).
7. We use language keywords instead of BCL types (e.g. `int, string, float` instead of `Int32, String, Single`, etc) for both type references as well as method calls (e.g. `int.Parse` instead of `Int32.Parse`). See issue #13976 for examples.
8. When including non-ASCII characters in the source code use Unicode escape sequences (\uXXXX) instead of literal characters. Literal non-ASCII characters occasionally get garbled by a tool or editor.

### Constants:
- Variables and fields that can be made const should always be made const.
- If const isn’t possible, readonly can be a suitable alternative.
- Prefer named constants to magic numbers.

### IEnumerable vs IList vs IReadOnlyList:
- For inputs use the most restrictive collection type possible, for example IReadOnlyCollection / IReadOnlyList / IEnumerable as inputs to methods when the inputs should be immutable.
- For outputs, if passing ownership of the returned container to the owner, prefer IList over IEnumerable. If not transferring ownership, prefer the most restrictive option.

### Generators vs containers
- Use your best judgement, bearing in mind:
  - Generator code is often less readable than filling in a container.
  - Generator code can be more performant if the results are going to be processed lazily, e.g. when not all the results are needed.
  - Generator code that is directly turned into a container via ToList() will be less performant than filling in a container directly.
  - Generator code that is called multiple times will be considerably slower than iterating over a container multiple times.

### Property styles
- For single line read-only properties, prefer expression body properties (=>) when possible.
- For everything else, use the older { get; set; } syntax.

### Lambdas vs named methods
- If a lambda is non-trivial (e.g. more than a couple of statements, excluding declarations), or is reused in multiple places, it should probably be a named method.

### Example File:

``ObservableLinkedList`1.cs:``

```C#
using System;
using System.Collections;
using System.Collections.Generic;
using System.Collections.Specialized;
using System.ComponentModel;
using System.Diagnostics;
using Microsoft.Win32;

namespace System.Collections.Generic
{
    public partial class ObservableLinkedList<T> : INotifyCollectionChanged, INotifyPropertyChanged
    {
        private ObservableLinkedListNode<T> _head;
        private int _count;

        public ObservableLinkedList(IEnumerable<T> items)
        {
            if (items == null)
                throw new ArgumentNullException(nameof(items));

            foreach (T item in items)
            {
                AddLast(item);
            }
        }

        public event NotifyCollectionChangedEventHandler CollectionChanged;

        public int Count
        {
            get { return _count; }
        }

        public ObservableLinkedListNode AddLast(T value)
        {
            var newNode = new LinkedListNode<T>(this, value);

            InsertNodeBefore(_head, node);
        }

        protected virtual void OnCollectionChanged(NotifyCollectionChangedEventArgs e)
        {
            NotifyCollectionChangedEventHandler handler = CollectionChanged;
            if (handler != null)
            {
                handler(this, e);
            }
        }

        private void InsertNodeBefore(LinkedListNode<T> node, LinkedListNode<T> newNode)
        {
            ...
        }

        ...
    }
}
```

``ObservableLinkedList`1.ObservableLinkedListNode.cs:``

```C#
using System;

namespace System.Collections.Generics
{
    partial class ObservableLinkedList<T>
    {
        public class ObservableLinkedListNode
        {
            private readonly ObservableLinkedList<T> _parent;
            private readonly T _value;

            internal ObservableLinkedListNode(ObservableLinkedList<T> parent, T value)
            {
                Debug.Assert(parent != null);

                _parent = parent;
                _value = value;
            }

            public T Value
            {
                get { return _value; }
            }
        }

        ...
    }
}
```
