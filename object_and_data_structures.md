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
