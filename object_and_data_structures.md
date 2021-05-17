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
