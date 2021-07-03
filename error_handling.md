Error handling is important, but **if it obscures logic, it's wrong.**

### Use Exceptions Rather Than Return Codes
```
public void sendShutDown(){
    DeviceHandle handle = getHandle(DEV1);
    // check the state of the device
    if (handle != DeviceHandle.INVALID){
        // save the device status
        retrieveDeviceRecord(handle);
        // if not suspended, shut down
        if (record.getStatus() != DEVICE_SUSPENDED){
            pauseDevice(handle);
            clearDeviceWorkQueue(handle);
            closeDevice(handle);
        }else{
            logger.log("Device suspended. Unable to shut down");
        }
    }else{
        logger.log("Invalid handle for:" + DEV1.toString());
    }
}
```
The problem with error codes is the caller must check for errors immediately after the call. Unfortunately, it's easy to forget. So, it is better to throw an exception when you encounter an error. The calling code is cleaner and its logic is not obscured by error handling:
```
public void sendShutDown(){
    try{
      tryToShutDown();
    }catch(DeviceShutDownError e){
      logger.log(e);
    }
}

private void tryToShutDown() throws DeviceShutDownError{
    DeviceHandle handle = getHandle(DEV1);
    DeviceRecord record = retrieveDeviceRecord(handle);
    
    pauseDevice(handle);
    clearDeviceWorkQueue(handle);
    closeDevice(handle);
}

private DeviceHandle getHandle(DeviceID id){
    ...
    throw new DeviceShutDownError("Invalid handle for: " + id.toString());
    ...
}

...
```
Notice how cleaner it is. This is not about a matter of aesthetics, the code is better because two concerns were tangled: the logic for device shutdown and error handling, are now separated!

### Write Your *Try-Catch-Finally* Statement First
First, try to write tests that force exceptions, and then add behavior to your handler to satisfy your tests. This will cause you to build the transaction scope of the **try** block first and will help you maintain the transaction nature of the scope.
```
@Test(expected = StorageException.class)
public void retrieveSectionShouldThrowOnInvalidFileName(){
    sectionStore.retrieveSection("invalid - file");
}
```
Create a dummy stub:
```
public List<RecordedGrip> retrieveSection(String sectionName){
    return new ArrayList<RecordedGrip>();
}
```
Next, force to throw the expected exception:
```
public List<RecordedGrip> retrieveSection(String sectionName){
    try{
        FileInputStream stream = new FileInputStream(sectionName);
    }catch(Exception e){
        throw new StorageException("retrieval error", e);
    }
    return new ArrayList<RecordedGrip>();
}
```
Finally, add the real implementation and narrow down the scope of caught exceptions:
```
public List<RecordedGrip> retrieveSection(String sectionName){
    try{
        FileInputStream stream = new FileInputStream(sectionName);
        stream.close();
    }catch(FileNotFoundException e){
        throw new StorageException("retrieval error", e);
    }
    return new ArrayList<RecordedGrip>();
}
```

### Use Unchecked Exceptions
Checked exceptions are checked at compile time (e.g. `FileNotFoundException`, `IOException`). Once a function signature throws a checked exception, it forces the function callers to change their implementations. This behavior is a violation of Open/Closed principle: low level functions are forcing high level functions to catch the mess. Use unchecked exceptions (e.g. `RuntimeException`,`Error`) instead.

### Provide Context with Exceptions
Create informative error messages and pass themm along with your exceptions. Mentiona the operation that failed and the type of a failure.

### Define Exception Classes in Terms of a Caller's Needs
Provide exception classes in terms of the way they're caught. Look at the poor implementation:
```
ACMEPort port = new ACMEPort(12);
try{
    port.open();
}catch(DeviceResponseException e){
    reportPortError(e);
    logger.log("Device response exception", e);
}catch(ATM1212UnlockedException e){
    reportPortError(e);
    logger.log("Unlock exception", e);
}catch(GMXError e){
    reportPortError(e);
    logger.log("GMX exception", e);
}
...
```
It covers all the cases. While it is reasonable to log all cases according to exception types, it is still practical to avoid duplication and wrap them in a common exception:
```
LocalPort port = new LocalPort(12);
try{
    port.open();
}catch(PortDeviceFailure e){
    reportPortError(e);
    logger.log(e.getMessage(), e);
}
...
```
The `LocalPort` class is a wrapper class to the `ACMEPort` library class. Wrapper like this can be very useful, it minimizes your dependency on the library, and you can move to a different library without much penalty:
```
public class LocalPort{
    private ACMEPort innerPort;
    public LocalPort(int portNumber){
        innerPort = new ACMEPort(portNumber);
    }
    
    public void open(){
        try{
            port.open();
        }catch(DeviceResponseException e){
            throw new PortDeviceFailure(e);
        }catch(ATM1212UnlockedException e){
            throw new PortDeviceFailure(e);
        }catch(GMXError e){
            throw new PortDeviceFailure(e);
        }
        throw new PortDeviceFailure(e);
    }
}
```
### Special Case Pattern
Consider this awkward code that sums expenses in a billing application:
```
try{
    MealExpenses expenses = expenseReportDAO.getMeals(employee.getId());
    m_total += expenses.getTotal();
}catch(MealExpensesNotFound e){
    m_total += getMealPerDiem();
}
```
The exception clutters the logic, it would be great to have simpler code like this:
```
MealExpenses expenses = expenseReportDAO.getMeals(employee.getId());
m_total += expenses.getTotal();
```
We can change the `ExpenseReportDAO` so that it always returns `MealExpense` object, if there are no meal expenses, it returns a `MealExpense` object that returns the `per diem` as its total:
```
public class PerDiemMealExpenses implements MealExpenses{
    public int getTotal(){
        // return the per diem default
    }
}
```
This is called SPECIAL CASE PATTERN, you create a special class or configure an object so that it handles a special case for you, hence, the client code does not have to deal with exceptional behavior.

### Don't Return `null`
All it takes is one missing `null` check to send an application spinning out of control. By returning `null`, we're essentially creating work for ourselves and foisting problems upon our callers. Consider this code:
```
public void registerItem(Item item){
    if(item != null){
        ItemRegistry registry = persistentStore.getItemRegistry();
        if(registry != null){
            Item existing = registry.getItem(item.getId());
            if(existing.getBillingPeriod().hasRetailOwner()){
                existing.register(item);
            }
        }
    }
}
```
Did you notice the fact that there wasn't a `null` check in the second line of that nested `if` statement. What would have happened of `persistentStore` were null? It would create `NullPointerException` at runtime, and either someone is catching `NullPointerException` at the top level or not. Either was it's bad!     
It is easy to say the code above is missing `NPE` check, but in actuality, it has many other problems.       

If you tempted to return `null` from a method, consider throwing an exception or returning a SPECIAL CASE object instead. If you are calling a `null`-returning method from a third-party API, consider wrapping it with a method that either throws an exception or returns a special case object. In many cases, special case objects are an easy remedy. Imagine that you have code like this:
```
List<Employee> employees = getEmployees();
if(employees != null){
    for(Employee e: employees)
        totalPay += e.getPay();
}
```
Does `getEmployees()` have to return `null`? If we change the method to return an empty list, we can clean up the code:
```
List<Employee> employees = getEmployees();
for(Employee e: employees)
totalPay += e.getPay();
```
### Don't Pass `null`
Return `null` is bad, passing `null` is worse! Unless you're workign with an API which expects you to pass `null`, you should avoid passing `null` whenever possible. Consider this simple method:
```
public double xProjection(Point p1, Point p2){
    return (p2.x - p1.x) * 1.5;
}
```
What happens when someone passes `null` as an argument? We'll get `NullPointerException`. How can we fix this? We could create an exception and throw it:
```
public double xProjection(Point p1, Point p2){
    if(p1 == null || p2 == null)
        return InvalidArgumentException("InvalidArgument for MetricsCalculator.xProjection");
    return (p2.x - p1.x) * 1.5;
}
```
It might be better than `NullPointerException` since it is specific to the type of error. There is another alternative:
```
public double xProjection(Point p1, Point p2){
    assert p1 != null : "p1 should not be null";
    assert p2 != null : "p2 should not be null";
    return (p2.x - p1.x) * 1.5;
}
```
It's good documentation, but it still doesn't solve the problem. In most prog. languages, there's no way to deal with a `null` that is passed by a caller accidentally. Because this is the case, the rational approach is to forbid passing `null` by default and end up with fewer careless mistakes. 
