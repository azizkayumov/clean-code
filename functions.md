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

### Use Descriptive Names
You know you are workign on clean code when each routine turns out to be pretty much what you expected. Half the battle to achieving that principle is choosing good names for small functions that do one thing. The smaller and more focused a function is, the easier it is to choose a descriptive name.    
A long descriptive name is better than a short enigmatic name. A long descriptive name is better than a long descriptive comment.    
Try several different names and read the code with each in place.    
Be consistent in your names, use the same phrases, nouns and verbs in the function names you choose for your modules: `includeSetupAndTeardownPages`, `includeSetupPages`, `includeSuiteSetupPage`, `includeSetupPage`.

### Function Arguments
The ideal number of arguments for a function is zero! Next one, followed by two. Three or more should be avoided and needs special justification.    
Arguments are even harder from a testing point of view. Imagine the difficulty of writing all the test cases to ensure that all the various combinations of arguments work properly.    
Output arguments are harder to understand than input arguments. We don't usually expect information to be going out through the arguments. So output arguments often cause us to do a double-take.

### Common Monadic Forms
It is very common to pass a single argument into a function. Avoid monadic functions that transforms its argument (confusing af), in fact, it is better to return the transformed argument as an output value. 

### Flag Arguments
They're Ugly! Passing a boolean into a function is a truly terrible practice -> it says the function does more than one thing! `render(true)` is just a plain confusing to a poor reader, seeing the function description `render(boolean isSuite)` helps a little, we should have split the function into two: `renderForSuite()` and `renderForSingleTest()`.


### Dyadic Functions
A function with two args is harder to understand than the monadic functions: `writeField(name)` or `writeField(outputStream, name)`. The second requires a short pause until we learn to ignore the first parameter.     
*If two arguments are ordered components of a single value*, then this dyadic function is appropriate: `Point p = new Point(0,0);`. 
Dyadic functions are not evil, but come with a cost, try to convert them to monadic forms.

### Triads
Functions that take 3 arguments are significantly harder to understand than dyads. The issues of ordering, pausing, and ignoring some arguments are more than doubled. How many times should you read the function `assertEquals(message, expected, actual)` to ignore `message`?

### Argument Objects
When a function seems to need more than 2-3 arguments, it is likely that some of those arguments can be wrapped into a class of their own:
```
Circle makeCircle(double x, double y, double radius)
Circle makeCircle(Point center, double radius)
```

### Argument Lists
Consider the function:
```
String.format("%s worked %.2f hours.", name, hours);
```
If the variable arguments are all treated identically, then they're equaivalent to a single argument of `List`. Functions that take variable arguments can be monads, dyads, or even triads, but it would be a mistake to give more than that.

### Verbs and Keywords
Functions with one argument should form a very nice verb + noun pair: `write(name)` or `writeField(name)`. 
We can also encode the argument names into the function name: `assertExpectedEqualsActual(expected, actual)` is better than `assert(expected, actual)`. 

### Have no side effects
Functions should do only one thing! Sometimes, it modifies the input, sometimes it changes its own class params which results in damaging mistruths. 
Consider this function which matches a `username` and `password`. It returns `true` if they match, `false` otherwise:
```
public class UserValidator{
    private Cryptographer cryptographer;
    public boolean checkPassword(String username, String password){
        User user = UserGateway.findByName(username);
        if (user != null){
            String codedPhrase = user.getPhraseEncodedByPassword();
            String phrase = cryptographer.decrypt(codedPhrase, password);
            if ("Valid Password".equals(phrase)){
                Session.initialize();
                return true
            }
        }
        return false;
    }
}
```
The side effect is the call to `Session.initialize()`! The function name says it is supposed to check password, not initialize the session. So, a caller who believes what the name of the function says runs the risk of erasing the existing session data.

The side effect also creates a temporal coupling: the function can be called only it is safe to initialize the session. In this case, we should rename the function to `checkPasswordAndInitializeSession`, though it certainly violates **Do one thing**.

### Output Arguments
Arguments are the inputs of a function, but if the function changes the state of the input, it creates confusion. Does `appendFooter(s)` append s as the footer to something? Or does it append some footer to s? This ambiguity forces the caller to look at the function declaration: **anything that forces you to check the function signature is equaivalent to a double-take**. It would better to have `report.appendFooter(s)`.
In general, output arguments should be avoided. **If your function must change the state of something, have it change the state of its owning object.**

### Command Query Separation
**Functions should either do something or answer something, but not both!** Either your function should change the state of an object, or it should return some information about that object. Doing both often leads to confusion.
Consider this function:
`public boolean set(String attribute, String value);`
This function sets the value of a nameed attribute and return `true` if it is successful and `false` if no such attribute exists. This leads to confusing statements like this:
`if (set("username","unclebob"))...`
Now imagine the statement from the point of a reader: is it asking whether "username" attribute was previously set to "unclebob"? 
or is it asking whether the "username" attribute was succesfully set to "unclebob"? It is not clear whether the word `set` is a verb or an adjective.
The author intended `set` to be a verb, but in the context of the `if` statement if *feels* like an adjective.

The real solution is to separate the command from the query so that the ambuguity cannot occur:
```
if (attributeExists("username")){
    setAttribute("username", "unclebob");
}
```
