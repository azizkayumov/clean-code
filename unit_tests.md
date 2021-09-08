We painstakingly write our classes and methods and concoct some ad hoc code to test just to make sure our programs "worked". 
Test Driven Development was not popular until recently. The Agile and TDD movements have encouraged many programmers to write automated unit tests, and more are joining their ranks everyday. 

### The Three Laws of TDD
Everyone knows that TDD asks us to write unit tests first, before we write production code. That rule is just the tip of the iceberg. Consider the following three rules:

**First Law**     
You may not write production code until you have written a failing unit test.

**Second Law**      
You may not write more of a unit test than is sufficient to fail, and not compiling is failing.

**Third Law**      
You may not write more of production code that is sufficient to pass the currently failing test.


The tests and the production code are written *together*, with the tests just a few seconds ahead of the production code. If we work this way, we will write dozens of tests every day, hundreds of tests every month and thousands every year. If we work this way, these tests will cover virtually all of our production code. However, the test code which can rival the production code in size, can present a management problem.

### Keeping Tests Clean
"Quick and dirty" should not be the watchward for writing tests. Tests need to be well designed and thoughtfully partioned. **Having dirty tests is equivalent to, if not worse than, having no tests at all**. The problem is that tests must change as the production code evolves. The dirtier the tests, the harder they are to change.     
As you modify the production code, old dirty tests start to fail, it will become even harder to change the old tests to pass again. Eventually, the dirty tests will be discarded and all you are left with the production code that is not guaranteed to work on every little change. If you keep your tests clean, your testing effort will not have failed.     
**Test code is just as important as production code**, it is not a second-class citizen. It requires thought, design care and must be kept as clean as production code.

### Test Enable the -ilities
If you don't keep your tests clean, you will lose them. And without them, you lose the very thing that keeps your production code flexible. It is *unit tests* that keep our code flexible, maintainable and reusable. The reason is *if you have tests, you don't fear making changes to the code!* Without tests every possible change is a possible bug. The dirtier your tests, the dirtier your code becomes. Eventually, you lose the tests, and your code rots.     

The higher your test coverage, the less you fear. Indeed, you can improve that architecture and design without fear. Having an automated suite of unit tests that cover the production code is the key to keeping your design and architecture as clean as possible. Tests enable all the -ilities, because tests enable *change*. 

