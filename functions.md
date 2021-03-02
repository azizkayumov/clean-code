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

Writing functions that stay at a single level of abstraction is the key to keeping functions short and making sure they do one thing. Making the code read like a top-down set of TO paragraphs is an effective technique for keeping the abstraction level consistent.

### Switch Statements
Switch statements naturally do N things. Unfortunately, it is hard to avoid switch statements, but it is possible to bury them in a low-level class and is never repeated (with polymorphism). 
```
public Money calculatePay(Employee e){
    switch(e.type){
        case COMMISSIONED:
            return calculateCommisionedPay(e);
        case HOURLY:
            return calculateHourlyPay(e);
        case SALARIED:
            return calculateSalariedPay(e);
    }
}
```
There are several problems with this function:
1. It is large, it will grow when new employees are added.
2. It clearly does more than one thing.
3. It violates SRP(Single Responsibility Principle), there is more than one reason for it to change.
4. It violates OCP(Open Closed Principle), it must change whenever new types are added.
5. There are an unlimited number of other functions that will have the same structure. For ex. `isPayday(Employee e, Date date)`, `deliverPay(Employee e, Money pay)`.

The solution is to bury the `switch` statement in the basement of abstract factory, and nevel let anyone see it. The factory will use the switch statement to create instances of the derivates of Employee, and the variatious functions, such as `calculatePay`, `isPayday`, and `deliverPay`, will be dispatched polymorphically through the Employee interface.
The generale rule for switch statement is that they can be tolerated if they appear only once, are used to create polymorphic objects, and are hidden behind an inheritance relationship so that the rest of the system can't see it.
