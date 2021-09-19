### Class Organization
The Java convention: a class should be organized in this order: 
- public static constants
- private static variables
- private instance variables. 
- public variables (if there is any reason for their existence)       
    
Public functions should follow the list of variables followed by privaye utilities called by a public funtion right after the public function itself. This follows the stepdown rule and helps the class read like a newspaper article.    

Try to keep variables and utility functions `private`, but don't be fanatic about it. Sometimes they can be `protected` so that they can be accessed by a test. The goal should be **testability**, if a test in the same package needs to call a function or access a variable, we'll make them protected or package scope considering a way to maintain privacy. **Loosening encapsulation is always a last resort**. 
