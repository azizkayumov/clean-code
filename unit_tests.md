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
