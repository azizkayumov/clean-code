Ray Ozzie, CTO, Microsoft:
> Complexity kills. It sucks the life out of developers,      
> it makes products difficult to plan, build and test. 

### How Would You Build A City?

Could you manage all the details yourself? Probably not. Even managing an existing city is too much for one person. Cities work because they have teams of people 
who manage particular parts of the city, the water systems, power systems, traffic, law enforcement, building codes etc. Some people are responsible for the bigger picture, 
while others focus on the details.      

Cities work since they have evolved into a high level of abstraction that makes it possible for individuals to manage the components effectively, without understading the big picture.      
Software systems are often organized like that too, the clean code helps us achieve this at the lower levels of abstraction. 

### Separate Constructing a System from Using It

Consider that *construction* is a very different process from *use*. 
> Software systems should separate the startup process, when the application objects are constructed and the dependencies are *wired* together, from the runtime  logic that takes over after startup.         

The separation of concerns is one of the most design techniques and the startup process is the first concern that any application must address. Unfortunately, most apps don't separate this concern:
```
public Service getService(){
    if(service == null)
        service = new MyServiceImpl(...);
    return service
}
```
This is the LAZY INITIALIZATION idiom that we don't incur the overhead of contruction unless we actually use the object, and our startup times can be faster as a result.        
However, we now have a hard-coded dependency on `MyServiceImpl` and everything its contructor requires. Testing can be a problem, if `MyServiceImpl` is a heavyweight object, we will need to make sure that an appropriate mock object gets assigned to the `service` field before this method is called during unit testing. Because we hava two responsibilities: contruction and normal runtime processing => which means the method is breaking the *Single Responsibility Principle* in a small way.       
One occurrence of lazy initialization is not a serious problem, however, there are normally many instances of little setup idioms like this in applications. We should never let the little, convenient idioms lead to modularity breakdown. The startup process should be modularized separately from the normal runtime logic and we should make sure that we have a global, consistent strategy for resolving our major dependencies.        

One way to separate contruction from use is simply to move all aspects of contruction to `main`, or modules called by `main`, and to design the rest of the system assuming that all objects have been constructed and wired up appropriately. The direction of dependency arrows crossing the barrier between `main` and the application all go one direction, pointing away from `main`. This means that the application has no knowledge of `main` or of the contruction process. 

### Factories

Sometimes, we need to make the application responsible for *when* an object gets created. Consider this example of order processing system. The applications must create the `LineItem` instances to add to an `Order`. We can use ABSTRACT FACTORY pattern to give the application control of *when* to build `LineItem`s, but keep the details of that contruction separate from the application code:
```
main() -------------------------> OrderProcessing()------
  |                                 |                   |
  |                                 |                   |
  v                                 v                   |
LineItemFactoryImpl() -----> LineItemFactory()          |
         \                                              |
          \                                             v
           `---------------------------------------> LineItem()
```
Notice all the dependencies point from `main` toward the `OrderProcessing` application, the application is decoupled from the details of how to build `LineItem`. That is implemented in `LineItemFactoryImpl`, yet `main` is in complete control of when the `LineItem` instances get built and can even provide app specific contructor arguments. 

