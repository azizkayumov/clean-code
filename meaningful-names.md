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
        if(x[0] == 3)
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
