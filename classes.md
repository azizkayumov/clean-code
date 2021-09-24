### Class Organization
The Java convention: a class should be organized in this order: 
- public static constants
- private static variables
- private instance variables. 
- public variables (if there is any reason for their existence)       
    
Public functions should follow the list of variables followed by private utilities called by a public funtion right after the public function itself. This follows the stepdown rule and helps the class read like a newspaper article.    

Try to keep variables and utility functions `private`, but don't be fanatic about it. Sometimes they can be `protected` so that they can be accessed by a test. The goal should be **testability**, if a test in the same package needs to call a function or access a variable, we'll make them protected or package scope considering a way to maintain privacy. **Loosening encapsulation is always a last resort**. 

### Classes Should Be Small!
In case of functions, we use *the number of physical lines* to measure how small a function is. With classes, we use **responsibities**.     
Consider this class that exposes 70 public methods, some devs call it *God class*:
```
public class SuperDashboard extends JFrame implements MetaDataUser{
    public String getCustomizedLanguagePath()
    public void setSystemConfigPath(String path)
    public boolean getGuruState()
    public boolean getOpenSourceState()s
    ...
    public String getUserHome()
    public String getBaseDir()
    ...
    public void processMenuItems()
    public void processTabPages()
    ...
    public void runProject()
}
```
What if `SuperDashboard` contained only 5 methods:
```
public class SuperDashboard extends JFrame implements MetaDataUser{
    public Component getLastFocusedComponent()
    public void setLastFocused(Component lastFocused)
    public int getMajorVersionNumber()league
    public int getMinorVersionNumber()
    public int getBuildNumber()
}
```
Despite its small number of methods, `SuperDashboard` has too many *responsibilities*.      

The name of a class should describe what responsibilities it fulfills. Naming is probably the first way of helping determine class size. The more ambigious the class name, the more likely it has too many responsibilities. `Supesr`, `Manager` or `Processor` often hint at unfortunate aggregation of responsibilities.      

We should also be able to describe a class in about 25 words, without "if", "and", "or" or "but". The smaller version of `SuperDashboard` provides access to the last focused component **and** it also allows us to track the version and build numbers. The first "and" is a hint that `SuperDashboard` has too many responsibilities.

### The Single Responsibility Principle (SRP)
The Single Responsibility Principle states that a class or module should have one, and only one, reason to change. This principle gives us both a definition of responsibility. Classes should have one responsibility - one reason to change.      

The small `SuperDashboard` class has two reasons to change: first, it tracks version information that should be updated on every time the software gets shipped and second, it manages Java Swing components. We might change the version information based on changes to other code in the system.     

Trying to identify responsibilities help us create meaningful abstractions in our code. We may easily extract one responsibility from `SuperDashboard`: the `Version` class that has a potential for reuse in other applications:
```
public class Version{
    public int getMajorVersion()
    public int getMinorVersion()league
    public int getBuiltNumber()
}
```
SRP is one of the most important concept in OO design, yet it is the most abused class design principle.     
Getting softare to work and making software clean are two very different activities. The problem is that we think we're done once the program works and failt to switch to the other concern of organization and cleanliness. We moves on to the next problem rather than going back and breaking the overstuffed classes into decoupled unit with single responsilibities.       

Many developers fear that having many single-purpose classes makes it more difficult to understand the bigger picture. Their concern is they must navigate from class to class in order to figure out how a piece of work gets accomplished.       

A system with many small pieces has no more moving parts that a system with a few large classes. There is always as much to learn in both approaches. *Do you want your tools organized into toolboxes with many small drawers each containing well-defined and well-labeled components? Or do you want a few drawers that you just toss everything into?*     

Every system contains a large amount of logic and complexity. The main goal in managing such complexity is to organize so that a developer knows where to look to find things and need only understand the directly affected complexity at any given time. 

### Cohesion

Classes should have a small number of instance variables. Each method should maximally operate on these variables. Generally, we would like cohesion to be high, it means that the methods and variables of the class are co-dependent and hang together as a logical whole.     

This `Stack` is a very cohesize class, with only `size()` fails to use both instance variables.
```
public class Stack{
    private int topOfStack = 0;
    List<Integer> elements = new LinkedList<Integer>();
    
    public int size(){
        return topOfStack;
    }
    
    public void push(int element){
        topOfStack++;
        elements.add(element);
    }
    
    public int pop() throws PoppedWhenEmpty{
        if(topOfStack == 0)
            throw new PoppedWhenEmpty();
        int element = elements.get(--topOfStack);
        elements.remove(topOfStack);
        return element;
    }
}
```
*Maintaining Cohesion Results in Many Small Classes*     
Breaking large functions into smaller functions means that our classes lose cohesion since they accumulate more and more instance variables that exist solely to allow a few functions to share them. When classes lose cohesion, split them! This gives our program a much better organization and a more transparent structure. 


### Organizing For Change
We should try to organize classes so as to reduce the risk of change. This `Sql` class is used to generate SQL strings given approriate metadata. It's a work in progress and does not yet support SQL functionality like `update` statements. When the time comes to add `update` statement, we will have to open up the class to make modifications. The problem with that is the new change introduces risk: any modifications must fully be retested.
```
public class Sql{
    public Sql(String table, Column[] columns)
    public String create()
    public String insert(Object[] fields)
    public String selectAll()
    public String findByKey(String keyColumn, String keyValue)
    public String select(Column column, String patern)
    public String select(Criteria criteria)
    private String valuesList(Object[] fields, final Column[] columns)
    private String selectWithCriteria(String criteria)
    ...
}
```
The `Sql` class must change when we add a new type of statement like `update`. It must also change when we want to alter the details of a single statement type, for ex., modifying the `select` statement to support subselects. These two reasons to change mean that the `Sql` class violates the SRP.      

Private method behavior that applies only to a small subset of a class can be a useful heuristic for spotting potential areas for improvement (`selectWithCriteria()` method). The primary goal to open up the class should be the change itself. If we won't need `update` functionality in future, there is no need to change `Sql` class at all.     

Consider this solution with each public interface method refactored into its own derivative if the `Sql` class. Note that private methods, such as `valuesList`, move directly where they are needed:
```
abstract public class Sql{
    public Sql(String table, Column[] columns)
    abstract public String generate();
}

public class CreateSql extends Sql{
    public CreateSql(String table, Column[] columns)
    @Override public String generate()
}

public class SelectSql extends Sql{
    public CreateSql(String table, Column[] columns)
    @Override public String generate()
}

public class InsertSql extends Sql{
    public CreateSql(String table, Column[] columns, Object[] fields)
    @Override public String generate()
    private String valuesList(Object[] fields, final Column[] columns)
}

public class SelectWithSql extends Sql{
    public CreateSql(String table, Column[] columns, Criteria criteria)
    @Override public String generate()
}

public class Where{
    public Where(String criteria);
    public String generate()
}
```
This code becomes excruciatingly simple: our required complehension time to undertand any class decreases to almost nothing. The risk that one function could break another becomes vanishingly small. It is now easier to test isolated classes! When it's time to add the `update` statements, none of the existing classes need to change.      
Our restructed code now supports SRP. It also supports another key OO principle known as the Open-Closed Principle: **classes should be open for extension but closed for modification**. We can simple drop our `UpdateSql` class while keeping other classes closed for modification. We want to structure our systems so that we introduce changes as little as possible when we update them with new features.







