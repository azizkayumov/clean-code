When people look under the hood, we want them to be impressed by the neatness, consistency and attention to detail that they perceive.
We want their eyebrows to rise as they scroll through the modules. If what they see is a scrambled mass of code, they're likely to conclude that the same inattention to detail pervades every other aspect of the project.

Choose a set of rules that govern the format of your code. And be consistent to apply those rules.

### The Purpose of Formatting
Code formatting is *important*. It is about communication, and communication is the professional developer's first order of business.
"Getting it working" can be the first order of business for a professional developer. However, the code you write today has a good chance of changing 
in the next release, so the readability of your code will have a profound effect on all the upcoming changes. Your code changes, your style and discipline survives.

### Vertical Formatting
How big should a source file be? How many lines of code should a file have?
Most of the source files in Junit, Fitnesse and Time and Money are less than 200 lines, and none exceeds 500 lines.    
It is possible to build significant systems (Fitnesse, 50K lines) out of files that are typically 200 lines long.
Small files are usually easier to understand than large files.

### The Newspaper Metaphor
Organize a source file like a newspaper article. Starts with a headline that tells what the story is about. The first paragraph gives a brief introduction, hiding all the details. As you continue down, the details will increase.

The name should be sufficient enough to tell us whether we are in a right module. The topmost parts should provide the high-level concepts and algorithms. Details should increase as you scroll down. 

A newspaper contains many articles, most of them are very small. This makes the newspaper *usable*. 

### Vertical Openness Between Concepts
Each line represents an expression or a clause, and each group of lines represents a complete thought. These thoughts should be separated from each other with blank lines.
Consider this code:
```
package fitnesse.wikitext.widgets;

import java.util.regex.*;

public class BoldWidget extends ParentWidget{
    public static final String REGEXP = "'''.+?'''"
    private static final Pattern pattern = Pattern.compile("'''(.+?)'''", Pattern.MULTILINE + Pattern.DOTALL);
    
    public BoldWidget(ParentWidget parent, String text) throws Exception{
        super(parent);
        Matcher matcher = pattern.matcher(text);
        match.find();
        addChildWidgets(match.group(1));
    }
}
```
There are blank lines that separate the package name, declaration, the imports and each of the functions. This extremely simple rule has a profound effect on the visual layout of the code. 
Taking those blank lines out, has a remarkably obscuring effect on the readability of the code:
```
package fitnesse.wikitext.widgets;
import java.util.regex.*;
public class BoldWidget extends ParentWidget{
    public static final String REGEXP = "'''.+?'''"
    private static final Pattern pattern = Pattern.compile("'''(.+?)'''", Pattern.MULTILINE + Pattern.DOTALL);
    public BoldWidget(ParentWidget parent, String text) throws Exception{
        super(parent);
        Matcher matcher = pattern.matcher(text);
        match.find();
        addChildWidgets(match.group(1));
    }
}
```

### Vertical Density
Vertical openness should separate concepts, and vertical density should imply close accociation.
Lines of code that are tightly related should appear vertically dense.
Look at these useless comments:
```
public class ReporterConfig{
    /*
     * The class name of the reporter listener
     */
     private String m_className;
     /*
      * The properties of the reporter listener
      */
     private List<Property> m_properties = new ArrayList<Property>();
     
     public void addProperty(Property property){
        m_properties.add(property);
     }
}
```
It forces you to use much more eye and head motion to understand the code. Compare with this one:
```
public class ReporterConfig{
     private String m_className;
     private List<Property> m_properties = new ArrayList<Property>();
     
     public void addProperty(Property property){
        m_properties.add(property);
     }
}
```
### Vertical Distance
Have you ever chased your tail through a class, hopping from one function to the next, scrolling up and down the source code, trying to divine how the functions relate and operate, only to get lost in a rat's nest of confision? This is frustrating, you're trying to understand what the system does, but you're spending your time and mental energy on trying to locate and remember where the pieces are.     

Concepts that are closely related should be kept vertically close to each other. This rule does not work for concepts that belong to separate files, but then closely related concepts should not be separated into different files unless you have a very good reason.    

#### Variable Declarations
Variables should be declared as close as to their usage as possible:
```
public int countTestCases(){
    int count = 0;
    for (Test test : tests)
        count += test.countTestCases();
    return count;
}
```

#### Instance variables
On the contrary, **instance variables** should be declared at the top of the class. This should not increase the vertical distance of these variables, because in a well-designed class, they are used by many, if not all, of the methods of the class.     

Consider this code from JUnit 4.3.1: 
```
public class TestSuite implements Test{
    static public Test createTask(Class<? extends TestCase> theClass, String name){
        ...
    }
    
    public static Constructor<? extends TestCase> getTestConstructor(Class<? extends TestCase> theClass) throws NoSuchMethodException{
        ...
    }
    
    public static String exceptionToString(Throwable t){
        ...
    }
    
    private String fName; // WTF
    
    private Vector<Test> fTests = new Vector<Test>(10); // WTF
    
    public TestSuite(){
        ...
    }
}
```

#### Dependent Functions
If one function calls another, they should be vertically close, and the caller should be above the callee, if at all possible. This gives the program a natural flow. If the convention is followed reliably, readers will be able to trust that function definitions will follow shortly after their use:
```
public class WikiPageResponder implements SecureResponder{
    public Response makeResponse(FitnesseContext context, Request request) throws Exception{
        String pageName = getPageNameOrDefault(request, "FrontPage");
        loadPage(pageName, context);
        if(page == null)
            return notFoundResponse(context, request);
        else
            return makePageResponse(context);
    }
    
    private String getPageNameOrDefault(Request request, String defaultPageName){
        ...
    }
    
    protected void loadPage(String resource, FitnesseContext context){
        ...
    }
    
    private Response notFoundRespone(FitnesseContext context, Request request){
        ...
    }
    
    private SimpleResponse makePageResponse(FitnesseContext context){
        ...
    }
}
```
#### Conceptual Affinity
Certain bits of code *want* to be near other bits. They have a certain conceptual affinity, the stronger the affinity, the less vertical distance there should be between them. The affinity might be based on a direct dependence, such as one function calling another or a function using a variable. Affinity might be based on a group of functions perform a similar operation:
```
public class Assert{
    static public void assertTrue(String message, boolean condition){
        ...
    }
    
    statuc public void assertFalse(String message, boolean condition){
        ...
    }
}
```

#### Vertical Ordering
As in newspaper articles, we expect the most important concepts to come first, and we expect them to be expressed with the least amount of polluting detail. We expect the low-level details to come last:
```
Caller (high level)
  |
  V
Caller (mid level)
  |
  V
Callee (low level)
```
This allows the readers to skim source files without having to immerse themselves in the details.
