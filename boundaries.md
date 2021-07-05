### Using Third-Party Code
The natural tension between the user of an interface and the provider of an interface: providers strive for broad applicability and users, on the other hand, want an interface that is focused on their particular needs.     
Let's look at the method of `java.util.Map`:
- clear()
- containsKey(Object key)
- containsValue(Object value)
- entrySet() Set - Map
- ...
- size()
Our application might build up a `Map` and pass it around. Out intention might be that none of the recipients of our `Map` delete anything in the map. But right there at the top of the list is the `clear()` method. Any user has the power to clear it or add ambigious objects to it.      
If our application needs a `Map` of sensors, you might find the sensors set up like this:
```
Map sensors = new HashMap();
```
Then, some code needs to access the sensor, you see this:
```
Sensor s = (Sensor) sensors.get(sensorId);
```
The client of this code carries the responsibility of getting an `Object` from the `Map` and casting it to the right type. This works, but it's not clean code. The readability of this code can be greatly improved by using generics:
```
Map<Sensor> sensors = new HashMap<Sensor>();
...
Sensor s = sensors.get(sensorId);
```
Passing an instance of `Map<Sensor>` liberally around the system means that there will be a lot of places to fix if the interface to `Map` ever changes.
A cleaner way to use `Map` might look like the following:
```
public class Sensor{
    private Map sensors = new HashMap();
    
    public Sensor getById(String id){
      return (Sensor)sensors.get(id);
    }
    
    ...
}
```
No user of `Sensors` would care one bit if generics were used or not. That choice has become (and always should be) an implementation detail.      
The interface at the boundary(`Map`) is hidden. It is able to evolve with very little impact on the rest of the application. The use of generics is no longer a big issue because the casting and type management are handled inside the `Sensors` class. It results in code that is **easier to read and harder to misuse.**. The `Sensors` class can enforce design and business rules.     

It is advised to not pass `Map`(or any interface at a boundary), avoid returning it from or accepting it as an argument to, Public APIs. Keep it inside the class or close family of classes, where it is used.

### Exploring and Learning Boundaries
It's not our job to test the third-party code, but it may be our best interest to write tests for the third-party code we use.
Long hours of reading the documentation might be spent to decide how to use the third-party code. Then we might be able to use it and see whether it does what we think. We would not be surprised to find ourselves bogged down in long debugging sessions trying to figure out whether the bugs we're experincing are in our code or theirs.     
Learning the third-party code is hard, integrating it is hard too. Doing both at the same time is doubly hard. Instead of experimenting and trying out the new stuff in our production code, we could write some test to explore our understanding of the third-party code. Such test are called *learning tests* (Jim Newkirk).
We essentially create controlled tests that check our understanding of that API. The test focus on what we want out of the API.

### Learning `log4j`

