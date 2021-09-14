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
Readability makes tests clean. Readability in tests is more important than in production code. 
31


The tests and the production code are written *together*, with the tests just a few seconds ahead of the production code. If we work this way, we will write dozens of tests every day, hundreds of tests every month and thousands every year. If we work this way, these tests will cover virtually all of our production code. However, the test code which can rival the production code in size, can present a management problem.

### Keeping Tests Clean
"Quick and dirty" should not be the watchward for writing tests. Tests need to be well designed and thoughtfully partioned. **Having dirty tests is equivalent to, if not worse than, having no tests at all**. The problem is that tests must change as the production code evolves. The dirtier the tests, the harder they are to change.     
As you modify the production code, old dirty tests start to fail, it will become even harder to change the old tests to pass again. Eventually, the dirty tests will be discarded and all you are left with the production code that is not guaranteed to work on every little change. If you keep your tests clean, your testing effort will not have failed.     
**Test code is just as important as production code**, it is not a second-class citizen. It requires thought, design care and must be kept as clean as production code.

### Test Enable the -ilities
If you don't keep your tests clean, you will lose them. And without them, you lose the very thing that keeps your production code flexible. It is *unit tests* that keep our code flexible, maintainable and reusable. The reason is *if you have tests, you don't fear making changes to the code!* Without tests every possible change is a possible bug. The dirtier your tests, the dirtier your code becomes. Eventually, you lose the tests, and your code rots.     

The higher your test coverage, the less you fear. Indeed, you can improve that architecture and design without fear. Having an automated suite of unit tests that cover the production code is the key to keeping your design and architecture as clean as possible. Tests enable all the -ilities, because tests enable *change*. 

### Clean Tests
Readability in tests is more important that it is in production code. What makes tests clean? The same things that makes all code readable: clarity, simplicity and density of expression. In tests, you want to say a lot with as few expressions as possible. Consider these 3 tests that are difficult to understand and can certainly be improved: there is a terrible amount of duplicate code `addPage` and `assertSubString` and more importantly, this code is just loaded with details that interfere with the expressiveness of the test.
```
public void testGetPageHierarchiAsXml() throws Exception{
    crawler.addPage(root, PathParser.parse("PageOne"));
    crawler.addPage(root, PathParser.parse("PageOne.ChildOne"));
    crawler.addPage(root, PathParser.parse("PageTwo"));
    
    request.setResource("root");
    request.addInput("type","pages");
    Responder responder = new SerializedPageResponder();
    SimpleResponse response = (SimpleResponse) responder.makeResponse(new FitnesseContext(root), request);
    String xml = response.getContent();
    
    assertEquals("test/xml", response.getContentType());
    assertSubString("<name>PageOne</name>", xml);
    assertSubString("<name>PageTwo</name>", xml);
    assertSubString("<name>ChildOne</name>", xml);
}
```
For ex., `PathParser` calls transform strings into `PagePath` instances used by the crawlers. This transformation is completely irrelevant to the test at hand and serves only to obfuscate the intent. The details surrounding the creation of the `responder` and the gathering and casting of the `response` are also just noise. Then there's the ham-handed way that the request URL is built from `resource` and an argument. In the end, this code was not designed to be read. Now consider the improved test:
```
public void testGetPageHierarchyAsXml() throws Exception {
    makePages("PageOne", "PageOne.ChildOne", "PageTwo");
    
    submitRequest("root", "type:pages");
    
    asserResponseIsXml();
    assertResponseContains("<name>PageOne</name>", "<name>PageTwo</name>", "<name>ChildOne</name>");
}
```
The BUILD-OPERATE-CHECK pattern is clearly visible by the structure of these tests. Each of these is clearly split into three parts: building the test data, operate on that test data and checking that the operation yielded the expected results. 
