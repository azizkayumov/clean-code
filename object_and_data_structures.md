### Data Abstraction
There is a reason that we keep our variables private - we don't want anyone else to depend on them, we want to keep the freedom to change their implementation quickly.
Consider these two listings:
```
// Concrete point
public interface Point{
  public double x;
  public double y;
}
```
```
// Abstract point
public interface Point{
  double getX();
  double getY();
  void setCartesian(double x, double y);
  
  double getR();
  double getTheta();
  void setPolar(double r, double theta);
}
```
The good thing about of the abstract `Point` is that there is no way you can tell that the implementation is in rectangular or polar coordinates. It enforces an access policy and setting the coordinates as atomic operation.    
On the contrary, the concrete `Point` forces us to manipulate those `x` and `y` independently, it would expose implementation even if there were `setters` and `getters`.     

Hiding implementation is not just putting a layer of function between variables, it is about abstractions! It offers interfaces that allow its users to manipulate the *essence* of the data, without having to care about its implementations.    
```
public interface Vehicle{
  double getFuelTankCapacityInGallons();
  double getGallonsOfGasoline();
}
```
The above `Vehicle` uses concrete terms to communicate the fuel level of a vehicle, where this one hides the communication with abstractions:
```
public interface Vehicle{
  double getPercentFuelRemaining();
}
```
We should not **expose** our data, rather we want to express our data in abstract terms. The worst option would be to blindly add getters and setters.    

### Data / Object Anti-Symmetry

The difference between objects and data structures:

> Objects hide their data behind abstractions and expose functions that operate on that data.
> Data structures expose their data and have no meaninful functions.

Read that again. Objects and data structures are virtual opposites.    
Consider this procedural `Shape`:
```
public class Square{
  public Point topLeft;
  public double side;
}

public class Rectangle{
  public Point topLeft;
  public double height;
  public double width;
}

public class Circle{
  public Point center;
  public double radius;
}

public class Geometry{
  public final double PI = "3.14159265"
  
  public double area(Object shape) throws NoSuchShapeException{
    if(shape instanceOf Square){
      Square s = (Square) shape;
      return s.side * s.side;
    }
    if(shape instanceOf Rectangle){
      Rectangle r = (Rectangle) shape;
      return r.height * r.width;
    }
    if(shape instanceOf Circle){
      Circle c = (Circle) shape;
      return PI * c.radius * c.radius;
    }
    throw new NoSuchShapeException();
  }
}
```
If `perimeter()` function needs to be added to the `Geometry` class, the shape classes would be unaffected. Any other classes that depend upon the shapes would also be unaffected! But, if we add a new shape class `Triangle`, all functions in `Geometry` must be changed to deal with it!

Now consider, this Object Oriented solution:
```
public class Square implements Shape{
  private Point topLeft;
  private double side;
  
  public double area(){
    return side*side;
  }
}

public class Rectangle implements Shape{
  private Point topLeft;
  private double height;
  private double width;
  
  public double area(){
    return height * width;
  }
}

...
```
Again, we see the complementary nature of these two solutions, they're opposites.
> Procedural code makes it hard to add new data structures because all the functions must change.
> Object Oriented code makes it hard to add new functions because all the classes must change.

So, in any complex system, if new data types are added frequently, objects and OO are most appropriate. If we want to add functions more, procedural code and data structures will be more appropriate.

### The Law of Demeter
The Law of Demeter says that a method *f* of a class **C** should only call the methods of these:
- C
- An object created by *f*
- An object passed as an argument to *f*
- An object held in an instance of **C**    

The following code violates the Law Of Demeter:
```
final String outputDir = ctx.getOptions().getScratchDir().getAbsolutePath();
```

### Train of Wrecks
The above code is often called a train wreck since it looks like a bunch of coupled cars. Chain of calls should be avoided, it is best to split them up:
```
Options opts = ctx.getOptions();
File scratchDir = opts.getScratchDir();
final String outputDir = scratchDir.getAbsolutePath();
```
If `opts`, `scratchDir` are objects, this code also violates the Law of Demeter. On the other hand, if they're just data structures, Demeter does not apply.    
The code would be less suspicious if it's written like this:
```
final String outputDir = ctx.opts.scratchDir.absolutePath;
```
### Hybrids
This confusion sometimes leads to unfortunate hybrid structures that area half object and half data structure. They have some functions that do significant things, they also have public variables or public accessors for all purposes, make the private variables public, tempting external function use those variables the way a procedural program would use a data structure. Hybrid make it hard to add new functions and data structures. Avoid creating them.    

### Hiding Structure
What if `ctx`, `opts`, `scratchDir` are objects with real behavior, since objects hide their internal structure, we should not be able to navigate through them. Then how do we get the absolute path of scratch directory:
```
ctx.getAbsolutePathOfScratchDirectoryOption();
```
or 
```
ctx.getScratchDirectoryOption().getAbsolutePath();
```
The first options is the explosion of methods in the `ctx` object. The second assumes that `getScratchDirectoryOption()` returns a data structure, not an object.
Neither options seems good, exploring the code, the goal of getting the absolute path was to create a new file:
```
String outFile = outputDir + "/" + className.replace('.','/') + ".class";
FileOutputStream fout = new FileOutputStream(outFile);
BufferedOutputStream bos = new BufferedOutputStream(fout);
```
Since the goal of the absolute path to create a new file, what if we tell the `ctx` do this instead?
```
BufferedOutputStream bos = ctx.createScratchFileStream(classFileName);
```
### Data Transfer Objects

The essential form of a data structure is a class with public variables and no functions. This is called DTO (data transfer objects), they're usefull when comminicating with database or parsing messages from sockets. They're often become the first stage in translating raw data of database to real objects. 
```
public class Address{
    private String street;
    private String city;
    private String state;
    private String zip;
    
    public Address(String street, String city, String state, String zip){
        ...
    }
    
    public String getStreet(){
        return street;
    }
    ...
}
```

### Conclusion

Objects expose behavior and hide data, this makes it easy to add new kinds of objects without changing behaviors. Data structures expose data and have no significant behavior, this makes it easy to add new behaviors, but makes it hard to add new data structures to existing functions.

In any system, we want one of them, good software developers understand these issues and choose the approach that is best for the job. 
