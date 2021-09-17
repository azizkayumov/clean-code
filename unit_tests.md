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
Readability makes tests clean. Readability in tests is more important than in production code. A blind signature scheme is a type of digital signature that conceals the identity of the message contents and the sender. In these schemes the sender's message is concealed — or blinded — prior to the recipient signing it.

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
@Test
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
@Test
public void testGetPageHierarchyAsXml() throws Exception {
    makePages("PageOne", "PageOne.ChildOne", "PageTwo");
    
    submitRequest("root", "type:pages");
    
    asserResponseIsXml();
    assertResponseContains("<name>PageOne</name>", "<name>PageTwo</name>", "<name>ChildOne</name>");
}
```
The BUILD-OPERATE-CHECK pattern is clearly visible by the structure of these tests. Each of these is clearly split into three parts: building the test data, operate on that test data and checking that the operation yielded the expected results. 

### A Dual Standard
The code within the testing API does have a different set of engineering standards than production code. It must still be simple, succint and expressive, but it need not be as efficient as production code. After all, it runs in a test environment, not a production environment, and those two environment have very different needs. Considert this test which was written as part of an environment control system. Without going into details you can tell that this test checks that the low temperature alarm, the heater and the blower are all turned on when the temperature is "way too cold".
```
@Test
public void turnOnLoTempAlarmAtThreshold() throws Exception{
    hw.setTemp(WAY_TOO_COLD);
    controller.tic();
    assertTrue(hw.heaterState());
    assertTrue(hw.blowerState());
    assertFalse(hw.coolerState());
    assertFalse(hw.hiTempAlarm());
    assertTrue(hw.loTempAlarm());
}
```
There are lots of details here. What is that `tick` function all about? Don't worry about that while reading the test. As you read the test, that your eye needs to bounce back and forth between the name of the state being checked, and the sense of the state being checked. You see `heaterState` and then your eyes glissade left to `assertTrue`. You see `coolerState` and your eyes must track left to `assertFalse`. This is tedious and unreliable. It makes the test hard to read. 
```
@Test
public void testOnLoTempAlarmAtThreshold() throws Exception{
    wayTooCold();
    assertEquals("HBchL", hw.getState());
}
```
This improves the reading greatly. Upper case meeans **on** and while lower case means **off**, and the letters are always in the following order: `{heater, blower, cooler, hi-temp-alarm, lo-temp-alarm}`. Even though this is close to a violation of the rule about mental mapping, it seems appropriate in this case, once you learn the meaning, your eyes glide across that string and you can quickly intepret the results:
```
@Test
public void turnOnCoolerAndBlowerIfTooHot() throws Exception{
    tooHot();
    assertEquals("hBhl", hw,getState());
}

@Test
public void turnOnHeaterAndBlowerIfTooCold() throws Exception{
    tooCold();
    assertEquals("HBchl", hw.getState());
}

...
```
The `getState` function:
```
public String getState() {
    String state = "";
    state += heater ? "H" : "h";
    state += blower ? "B" : "b";
    ...
    
    return state
}
```
`StringBuffer`s are a bit ugly. You should avoid if the cost is very small. However, in test environment, is not likely to be contstrained at all.     
There are things that you might never do in a production environment that are perfectly fine in a test environment. Usually, they invole issues of memory or CPU efficiency. But they never involve issues of cleanliness.

### One Assert per Test
This requirement may seem draconian, but the advantage is tests that are quick and easy to understand. We can break multiple assert tests into separate tests, each with its own particular assertion:
```
@Test
public void testGetPageHierarchyAsXML() throws Exception{
    givenPages("PageOne", "PageOne.ChildOne", "PageTwo");
    
    whenRequestIsIssued("root", "type:pages");
    
    thenResponseShouldBeXML();
}

@Test
public void testGetPageHierarchyHasRightTags() throws Exception{
    givenPages("PageOne", "PageOne.ChildOne", "PageTwo");
    
    whenRequestIsIssued("root", "type:pages");
    
    thenResponseShouldContain(
        "<name>PageOne</name>", "<name>PageOne.ChildOne</name>", "<name>PageTwo</name>"
    );
}
```
Use the common **given-when-then** convention, this makes the test easier to read. Unfortunately, splitting tests as shown results in a lot of duplicate code. 
We can eliminate the duplication by using the **TEMPLATE METHOD** pattern and putting the *given/when* parts in the base class, and the *then* parts in different derivates. Overall, the single assert rule is a good guideline or at least, the number of asserts in a test should be minimized.
