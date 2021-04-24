When people look under the hood, we want them to be impressed by the neatness, consistency and attention to detail that they perceive.
We want their eyebrows to rise as they scroll through the modules. If what they see is a scrambled mass of code, they're likely to conclude that the same inattention to detail pervades every other aspect of the project.

Choose a set of rules that govern the format of your code. And be consistent to apply those rules.

### The Purpose of Formatting
Code formatting is *important*. It is about communication, and communication is the professional developer's first order of business.
"Getting it working" can be the first order of business for a professional developer. However, the code you write today has a good chance of changing 
in the next release, so the readability of your code will have a profound effect on all the upcoming changes. Your code changes, your style and discipline survives.

### Vertical Formatting
How big should a source file be? How many lines of code should a file have?
Most of the source files in Junit, Fitnesse and Time and Money are less than 200 lines, and none exceeds 500 lines.    
It is possible to build significant systems (Fitnesse, 50K lines) out of files that are typically 200 lines long.
Small files are usually easier to understand than large files.

### The Newspaper Metaphor
Organize a souce file like a newspaper article. Starts with a headline that tells what the story is about. The first paragraph gives a brief introduction, hiding all the details. As you continue down, the details will increase.

The name should be sufficient enough to tell us whether we are in a right module. The topmost parts should provide the high-level concepts and algorithms. Details should increase as you scroll down. 

A newspaper contains many articles, most of them are very small. This makes the newspaper *usable*. 
