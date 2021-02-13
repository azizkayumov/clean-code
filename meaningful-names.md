### Use Intention-Revealing Names
The name of a variable, function, or class, should answer why it exists, what it does, and how it is used.
*If a name requires a comment, then the name does not reveal its intent.*
``` 
int d; // elapsed time in days
```
The name **d** reveals nothing. Choose a name that specifies what is being measured and the unit of measurement:
```
int elapsedTimeInDays;
int daysSinceCreation;
int daysSinceModification;
```
What is the purpose of this code?
```
public List<int[]> getThem() {
    List<int[]> list1 = new ArrayList<int[]>();
    for (int[] x: theList)
        if(x[0] == 4)
            list1.add(x)
    return list1
}
```
The problem is the implicity of this code, we need to know the answers to question such as:
1. What kinds of things are in the `theList`?
2. What is the significance of the zeroth subscript of an item in `theList`?
3. What is the significance of the value `4`?
4. How would I use the list being returned?    

The answers to these questions are not present in the code sample, but *they could have been.* 
```
public List<int[]> getFlaggedCells(){
    List<Cell> flaggedCells = new ArrayList<Cell>();
    for (Cell cell: gameBoard)
        if (cell.isFlagged())
            flaggedCells.add(cell)
    return flaggedCells
}
```
With these simple name changes, it's not difficult to understand what's going on. 
Notice that the simplicity of the code has not changed, but it has become much more **explicit**.

### Avoid Disinformation
Avoid leaving false clues that obscure the meaning of code. Do not name a grouping of accounts as an `accountList` unless it is actually a list. 

Do not use names which vary in small ways. How long does it take to spot the subtle difference between
`XYZControllerForEfficientHandlingOfStrings` and `XYZControllerForEfficientStorageOfStrings`?

Avoid the use of lower-case `L` or uppercase `O`, they look entirely like the constants of one and zero, respectively:
```
int a = 1;
if (O == l)
    a = O1;
else
    l = O1;
```
### Make Meaningful Distinctions
Do not just write code to satisfy a compiler or interpreter. If names must be different, then they would also mean something different. 
Number-series names are not *disinformative*, but they are *noninformative*, and provide no clue to the author's intention:
```
public static void copyChars(char a1[], char a2[])
```
Much better:
```
public static void copyChars(char source[], char destination[])
```
Noise words are another meaningless distinction:   
Names | Reason
------------ | -------------
`Product`, `ProductInfo`, `ProductData` | No difference in meaning
`int variable` | Avoid programming keywords
`Name`, `NameString` | Do not add the data type if it is obvious 
`Customer`, `CustomerObject` | Which one will represent the best path to a customer's payment history?
`getActiveAccount()`, `getActiveAccounts()`, `getActiveAccountInfo()` | Which function should I call?

Distinguish names in such a way that the reader knows what the differences offer.

### Use Pronounceable Names
Programming is a social activity, you got to discuss your code with others -> so avoid using unpronounceable names at all costs:
```
class DtaRcrd102{
    private Date genymdhms;  // generation date, year, month, day, hour, minute, second
    private Date modymdhms;  // modification date, year, month, day, hour, minute, second
    ...
}
```
Compare:
```
class Customer{
    private Date generationTimestamp;
    private Date modificationTimestamp; 
    ...
}      
```
### Use Searchable Names 
Single-letter names and numeric constants are not easy to locate across a body of text.
If a variable or constant might be seen or used in multiple places in a body of code, it is imperative to give it a search-friendly name.    
Compare:
```
for (int j=0; j<34; j++)
    s += (t[j]*4) / 5
```
to:
```
int realDaysPerIdealDay = 4;
const int WORK_DAYS_PER_WEEK = 5;
int sum = 0;
for (int j=0; j<NUMBER_OF_TASKS; j++){
    int realTaskDays = taskEstimate[j] * realDaysPerIdealDay;
    int realTaskWeeks = (realTaskDays / WORK_DAYS_PER_WEEK);
    sum += realTaskWeeks
}
```
Notice how much easier it will be to find `WORK_DAYS_PER_WEEK` than to find all the places where 5 was used.
### Member Prefixes
You don't need to prefix member variables with `m_` anymore:
```
public class Part{
    String m_dsc; // textual description
} 
```
People quickly learn to ignore the prefixes to see the meaningful part of the name:
```
public class Part{
    String description; // textual description
} 
```
### Interfaces & Implementations
Avoid adding `I` to interface names: `IShapeFactory` -> `ShapeFactory`. You don't have to let users know that you're handing them an interface (where they can quickly find out that it is an interface in modern IDEs).
Encode the implementation (not the interface): `ShapeFactoryImpl` or `CShapeFactory`.

