### Small
The first rule of functions is that they should be small. The second rule is *they should be smaller than that.*
The blocks within `if`, `else`, `while` and so on should be one line long, probably be a function call. **The indent level of a function should not be greater than one or two.**
Try to express what a function does in English, it should not be a long sentence!

### Do One Thing
Functions should do one thing. They should do it well. They should do it only. Use TO technique to determine if a function is doing *one thing* or not:
For `renderPageWithSetupsAndTeardowns()`:
> TO RenderPageWithSetupsAndTeardowns, we check to see whether the page is a test page and if so, we include the setups and teardowns. In either case we render the page in HTML.

If a function does only those steps that are one level below the stated name of the function, then the function is doing **one thing**. 
The reason to write a function is to decompose a larger concept into a set of steps at the next level of abstraction. Another way to know that a function is doing more than **one thing**
is if you can extract another function from it with a name that is not merely a restatement of its implementation.

### One Level of Abstraction per Function
The statements within a function should be in the same level of abstraction. 
A function with `getHtml()`(high level), `String pagePathName = PathParser.render(pagePath)`(medium level), `.append("\n")`(low level) violates the rule. 
Mixing different levels of abstraction is always confusing. Once the different levels are mixed, more and more different levels tend to be added.

### Reading Code from Top to Bottom: The Stepdown Rule
We want to be able to read the program as though it were a set of **TO** paragraphs, each of which is describing the current level of abstraction and referencing subsequent TO paragraphs
at the next level down:
> To include the setups and teardowns, we include setups, then we include the test page content, and then include the teardowns.
