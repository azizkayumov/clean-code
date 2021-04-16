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
1. Mumbling
