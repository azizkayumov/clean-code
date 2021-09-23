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
    public boolean getOpenSourceState()
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

The name of a class should describe what responsibilities it fulfills. Naming is probably the first way of helping determine class size. The more ambigious the class name, the more likely it has too many responsibilities. `Super`, `Manager` or `Processor` often hint at unfortunate aggregation of responsibilities.      

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
Getting softare to work and making software clean are two very different activities. The problem is that we think we're done once the program works and failt to switch to the other concern of organization and cleanliness. We move on to the next problem rather than going back and breaking the overstuffed classes into decoupled unit with single responsilibities.       

Many developers fear that having many single-purpose classes makes it more difficult to understand the bigger picture. Their concern is they must navigate from class to class in order to figure out how a piece of work gets accomplished.       

A system with many small pieces has no more moving parts that a system with a few large classes. There is always as much to learn in both approaches. *Do you want your tools organized into toolboxes with many small drawers each containing well-defined and well-labeled components? Or do you want a few drawers that you just toss everything into?*     

Every system contains a large amount of logic and complexity. The main goal in managing such complexity is to organize so that a developer knows where to look to find things and need only understand the directly affected complexity at any given time. 



