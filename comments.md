Brian W.Kernighan and P.J. Plaugher:
> Don't comment bad code - rewrite it.

Comments are not "pure good". They're a **necessary evil**. We would not need comments very much if our code or the programming language is expressive enough.

The main goal to write comments is compensate for our failure to express ourself in code: comments = *failure*. So having comments is not a *celebration*.
If you have to write comments, think about expressing yourself in code first.

Comments are pure **lies**. Code evolves, but programmers hardly maintain comments, so eventually they become lies. The truth is code. Spend energy to minimize comments.

### Comments Do Not Make Up for Bad Code
A motivation to write comments is **bad code**. 
> Ooh, I'd better comment that!    
> No, you'd better clean it!

### Explain Yourself in Code
Many programmers think that explanation is always good. This is parently false. Which would you rather see?
This:
```
// Check to see if the employee is eligible for full beniefits
if ((employee.flags & HOURLY_FLAG) && employee.age > 65)
```
Or this:
```
if (employee.isEligibleForFullBenefits())
```
It takes only seconds to create a function that says the same thing as the comment.

### Good comments

1. Legal comments
```
// Copyright (C) 2003, 2004, 2005 by Object Mentor, Inc. All rights reserved.
// Released under the terms of the GNU General Public License version 2 or later.
```

2. Informative comments    
Comments that are expressable in code are not informative comments:
```
// Returns an instance of the Responder being tested
protected abstract Responder responderInstance();
```
In this case, the intention is clearer:
```
// format matched kk:mm:ss EEE, MMM dd, yyyy
Pattern timeMatcher = Pattern.compile("\\d*:\\d*:\\d* \\w*, \\w* \\d*, \\d*")
```

3. Explanation of Intent    
Sometimes a comment can provide the intent behind a decision:
```
// This is our best attempt to get a race condition
// by creating large number of threads
for (int i = 0; i < 25000; i++){
    WidgetBuilderThread widgetBuilderThread = new WidgetBuilderThread(widgetBuilder, text, parent, failFlag);
    Thread thread = new Thread(widgetBuilderThread);
    thread.start();
}
```

4. Clarification    
It is helpful to translate some obscure code into something more readable:
```
assertTrue(a.compareTo(b) != 0); // a != b
```
Note that there is a risk of writing incorrect clarification

5. Warning of Consequences    
```
public static SimpleDateFormat makeStandardHttpDateFormat(){
    // SimpleDateFormat is not thread safe,
    // so we need to create each instance independently
    SimpleDateFormat df = new SimpleDateFormat("EEE, dd MMM yyyy HH:mm:ss z");
    df.setTimeZone(TimeZone.getTimeZone("GMT"));
    return df
}
```

6. TODO comments    
It is reasonable to leave TODO comments:
```
// TODO Mdm these are not needed
// We expect this to go away when we do the checkout model
protected VersionInfo makeVersion() throws Exception{
    return null;
}
```
TODOs are jobs that should be done later, clearly they're not an excuse to leave bad code. 

7. Amplification    
A comment to explain the importance of something that may otherwise seem inconsequential:
```
String username = editText.getText().trim();
// the trim is real important. It removes the starting 
// whitespaces that could cause the username to be
// recognized as another username.
```

8. Javadocs in Public APIs    


### Bad Comments
Most comments are bad comments. They are excuses for poor code or justifications for insufficient decisions.

1. Mumbling    
Commenting just because you should or because the process requires it - a hack. Dedicate some time to leave a good comment or do not comment at all.
```
try{
    FileInputStream propertiesStream = new FileInputStream(propertiesPatch);
    loadedProperties.load(propertiesStream);
}catch(IOException e){
    // No properties files means all defaults are loaded
}
```
What was the intention of the comment above?
Any comment that forces to look at other modules has failed to communicate to you and is not worth the bits it consumes.

2. Redundant Comments    
Here is the comment that takes more time to read than the code itself:
```
// Utility method that returns when this.closed is true. Throws an exception
// if the timeout is reached
public void waitForClose(final long milliseconds) throws Exception{
    if(!closed){
        wait(milliseconds);
        if(!closed)
            throw new Exception("MockResponseSender could not be closed!");
    }
}
```
What purpose does this comment serve? It is certainly not more informative than the code! Not easier to read than the code. Less precise than the code!
Now imagine this army of useless and redundant javadocs:
```
/**
* The processor delay for this component
*/
protected int backgroundProcessorDelay = -1;

/**
* The container event listeners for this Container
*/
protected ArrayList listeners = new ArrayList();

/**
* The logger implementation with which this Container is associated
*/
protected Log logger = null;
```

3. Misleading Comments    
```
// Utility method that returns when this.closed is true. Throws an exception
// if the timeout is reached
public void waitForClose(final long milliseconds) throws Exception{
    if(!closed){
        wait(milliseconds);
        if(!closed)
            throw new Exception("MockResponseSender could not be closed!");
    }
}
```
This subtle bit of misinformation could cause another programmer to call this function in the expectation that it will return as soon as `this.closed` becomes true. That poor programmer would then find himself debugging the function why his code executed so slowly.

4. Mandated Comments    
It is silly to have a rule that every function must have a javadoc, or every variable must have a comment. These comments lead to confusion, lies and disorganization.
```
/**
* @param title The title of the CD
* @param author The author of the CD
* @param tracks The number of tracks on the CD
* @param durationInMinutes The duration of the CD in minutes
*/
public void addCD(String title, String author, int tracks, int durationInMinutes){
    CD cd = new CD();
    cd.title = title;
    cd.author = author;
    cd.tracks = tracks;
    cd.duration = duration;
    cdList.add(cd);
}
```

5. Journal Comments    
Comments that keep every change that has ever been made to the code are not necessary anymore since we have source code control systems that do exactly the same:
```
11-Oct-2020 : Reorganized the class and moved it to new package
05-Nov-2020 : Added a getDescription() method, and eliminated NotableDate class
12-Nov-2020 : Fixed bug in SpreadsheetDate class
....
```

6. Noise Comments    
Comments that restate the obvious and provide no new information:
```
/**
* Default constructor
*/
protected AnnualDateRule(){
}
```
How about this?
```
/** The day of the month */
private int dayOfMonth;

/**
* Returns the day of the month
*/
public int getDayOfMonth(){
    return dayOfMonth;
}
```
These comments are so noisy that we learn to ignore them. They will eventually become *lies* as the code around them changes.
**Replace the temptation to create noise with the determination to clean your code - you will become a better and happier programmer.**

7. Don't use a comment when you can use a function or variable    
Consider this code:
```
// does the module from the global list depend on the
// subsystem we are part of?
if (smodule.getDependSystems().contains(subSysMod.getSubsystem()))
```
which could be rephrased to:
```
ArrayList moduleDependees = smodule.getDependSubsystems();
String ourSubsystem = subSysMod.getSubSystem();
if (moduleDependees.contains(ourSubsystem))
```

8. Position markers    
If you overuse banners, they will fall into the background noise and be ignored:
```
/// Actions //////////////////////////////////
```
9. Closing Brace Comments    
Closing brace comment may make sense with deeply nested structures, it serves only to clutter the kind of small and encapsulated functions:
```
try{
    while ((line = in.readline()) != null){
        lineCount++;
        charCount += line.length();
    } // while
}// try
```

10. Attributions and Bylines    
```
/* Added by Rick */ 
```
Source code systems are very good at remembering who added what, when.

11. Commented-Out Code    
```
InputStreamResponse response = new InputStreamResponse();
response.setBody(formatter.getResultStream());
// InputStreamResponse response = formatter.getResultStream();
// StreamReader reader = new StreamReader(resultsStream);
// response.setBody(formatter.getResultStream()); 
```
Why are these 3 lines of code commented? Are they still important? Are the reminders of something?
We have good source code control systems. Don't comment-out code. Just delete it.

12. HTML Comments    
HTML in source code comments is an abomination. It makes the code hard to read in the place where they should be easy to read - the IDE. If they're meant to be read by some Javadocs tool, it should be the responsility of that damn tool, not the programmer.
```
/**
* Task to run fit tests
* <p>
* <pre>
* Usage:
* </pre>
* &lt;taskdef name=&quot;execute-fitnesse-tests&quot
* </p>
*/
```

13. Nonlocal Information    
Write comment if it describes the code it appears near. Do not comment more than expected:
```
/**
* Port on which fitnesse would run. Defaults to <b>8082</b>
*
* @param fitnessePort
*/
public void setFitnessePort(int fitnessePort){
    this.fitnessePort = fitnessePort;
}
```

14. Too much information    
Do not bring interesting historical discussions:
```
/*
* RFC 2045 - Multipurpose Internet Mail Extentions (MIME)
* Part One: Format of Internet Message Bodies
* section 6.8. Base64 Content-Transfer-Encoding
* ....
* ...
* ..
*/
```

15. Inobvious Connection    
The connection between a comment and the code should be obvious. 
```
/*
* start with an array that is big enough to hold all the pixels
* (plus filter bytes), and an extra 200 bytes for header info
*/
this.pngBytes = new byte[((this.width + 1) * this.height * 3) + 200];
```
It is a pity when a comment needs its own explanation.

16. Function Headers    
Short functions do not need much description. Choose a well-suited name instead of investing time for a comment header.

17. Javadocs in Nonpublic Code    
Generating javadoc pages for the classes and functions inside a system is not generally useful, and the extra formality of the  javadoc comments amounts to distraction.
