[Refactoring](https://martinfowler.com/books/refactoring.html) by Martin Fowler

The first edition published in 1999 introduced the refactoring patterns, using Java examples. The 2nd edition published in 2018 uses Javascript instead.

Either way, the first 5 chapters motivate why refactoring is an important practice, when to do it, how to do it safely.

The rest of the book is structured as a reference with each chapter covering a group of related refactoring patterns, using the same “catalog” template for each.

Many of the examples and patterns are geared towards object-oriented programming, but the techniques can apply to any paradigm.

_The notes below are based on the 1st edition._

---

## Preface

What is **refactoring**?

> Refactoring is the process of changing a software system in such a way that it _does not alter the external behavior_ of the code yet _improves its internal structure_.

Rather than doing all design up front, refactoring occurs continually during development, learning from building the system how to improve the design.

---

## Chapter 1: Refactoring, a First Example

Gives an example of a video store rental system. A **Customer** has zero or more **Rentals**, where a Rental involves a single **Movie** with a duration for the days rented.

The book uses Java code snippets and UML diagrams like above to illustrate.

_I will translate these to python so you don’t barf on yourself like I do when reading Java code._

> When you find you have to add a feature to a program, and the program’s code is not structured in a convenient way to add the feature, first refactor the program to make it easy to add the feature, then add the feature.

_Commentary: I don’t necessarily agree with this advice in all cases, but the idea that a valid motivation for refactoring code to make it easier to maintain and extend makes sense._

The **first step in refactoring is making sure you have a solid set of tests** that will act as guardrails to ensure you didn’t make a mistake and break existing functionality. Remember, your goal is to change the internal design without altering the external behaviors of the system. If tests already exist, great. If not, create them. You will want these tests to run fast so you can run them frequently. Refactoring proceeds in small steps, checking at each step that you didn’t break something. And if you did, it's easier to spot the problem.

> Any fool can write code that a computer can understand. Good programmers write code that humans can understand.

Code should clearly communicate its purpose. Changing variable names can help make code more understandable.

---

## Chapter 2: Principles in Refactoring

> Refactoring (noun): a change made to the internal structure of software to make it easier to understand and cheaper to modify without changing its observable behavior.

**Performance optimizations** is similar to refactoring since they make internal changes to code, but they have different goals. Performance optimizations improve the speed (observable behavior change), but often at the expense of readability.

When coding, you wear **two hats**:

- _When adding a function_, you shouldn’t be changing existing code; you are just adding new capabilities. Measure your progress by adding tests and getting them to work
    
- _When you refactor_, you make a point of not adding function, you only restructure the code. You don’t add any tests (unless you identify some that are missing), and only change tests if absolutely necessary (i.e. they are coupled to the implement, violating the “test behaviors not implementation” principle).
    

**Refactoring improves the design of software**. Without it, the design of the program will decay over time and start losing its structure. It becomes harder to see the design just by reading the code. Loss of structure has a cumulative effect.

**Refactoring makes software easier to understand**.

> I make a point of trying to put everything I should remember into the code so I don’t have to remember it.

**Refactoring helps you find bugs.**

**Refactoring helps you program faster.** A good design is essential for rapid software development, and refactoring helps keep the design from decaying.

**When should you refactor?**

- Rule of three - first time, just do it. Second time you wince at the duplication, but do it anyways. Third time you do something similar, refactor it.
    
- When adding function. It may help you better understand the code you need to modify first, especially if written by someone else.
    
- When you need to fix a bug.
    
- As you do code review. It might help you review someone else’s code, and have specific suggestions for the contributor.
    

**What makes programs hard to work with?**

- Hard to read, hard to modify.
    
- Duplicated logic, hard to modify.
    
- Complex conditional logic.
    

**Problems with refactoring**

- Databases, especially if the code is tightly coupled to the database schema (_commentary: and not using some object-relational mapper, or schema migration - this part of the book seems antiquated_)
    
- Changing interfaces. There is a difference between a public interface and a published interface, where the former you have control over and can discover all the usages of the interface in code and the latter you can not. Once published, you need to maintain backwards compatibility for your clients.
    
- Design changes that are difficult to refactor.
    

**When shouldn’t you refactor?**

- If close to a deadline
    

**Refactoring and performance**

- Don’t make micro optimizations unless there’s a strong reason. Too much premature optimization can erode the readability of the code, and can be a waste of time.
    
- In most cases, the Pareto principle applies here and 90% of the performance problems are caused by a few underlying issues.
    
- You should profile your code to find the root causes and address those.
    

---

## Chapter 3: Bad smells in code

_Commentary: I removed some that didn’t make a lot of sense, or were too Java-specific._

- **Duplicated code** - the same code in multiple places. Unify them.
    
- **Long method** - trying to do too many things. Extract Method to smaller methods. It will be much easier to understand.
    
    - Look for comments, they are often a sign that the block of code could be extracted to a method with a name based on the comment.
        
    - Conditionals and loops are good signs for extraction.
        
- **Large class** - trying to do too much (_commentary: violating Single Responsibility Principle_).
    
- **Long parameter list** - instead of passing lots of individual parameters, pass an object with the parameter data that the class can access instead.
    
- **Divergent change** - when you have to make coordinated changes in multiple areas every time.
    
- **Shotgun surgery** - when you make a change and have to make a lot of little changes to a lot of different classes.
    
- **Feature envy** - a method that invokes a half-dozen getters on another object to calculate something. Put things together that change together.
    
- **Data clumps** - bunches of data that hang around together really ought to be made into their own object.
    
- **Switch statements** - often these same switch statements will occur in multiple places of the code base. A solution is to use polymorphism instead (i.e. subclasses)
    
- **Parallel inheritance hierarchies** - every time you make a subclass of one class, you also have to make a subclass of another.
    
- **Lazy class** - doesn’t do anything useful, kill it.
    
- **Speculative generality** - a fancy way of saying that the code was designed to be generic, but its not actually needed or used.
    
- **Temporary field** - Instance variables that is only set in certain circumstances. An example is a complicated algorithm that needs several variables, but they aren’t always needed depending on the context.
    
- **Message chains** - When a client asks one object for another object, which asks for yet another object, etc. The client becomes coupled to the structure of the navigation. Cut out the middleman.
    
- **Middle man** - when a class’s interface has a lot of methods that actually delegates to another class.
    
- **Inappropriate intimacy** - when classes are tightly coupled and rely on knowing internal details
    
- **Alternative classes with different interfaces** - methods that do the same thing, but have different signatures.
    
- **Data class** - when a class is just a data holder (mostly property getter/setters). Sometimes the users of these data classes do too much manipulation, and become coupled to the data class’s implementation (such as which collections it uses)
    
- **Comments** - thickly commented code can indicate the code is bad or hard to understand
    

---

## Chapter 4: Building tests

_Commentary: mostly a primer in junit… yawn…_

---

## Chapter 5: Toward a catalog of refactorings

Explains the structure of the rest of the book, which has the reference list of all refactoring patterns.

_Commentary: I’ve only included the most common and useful techniques. See the book for the exhaustive list._

---

## Chapter 6: Composing Methods

How to package code properly into methods.

### Extract Method

_**WHEN**: You have a code fragment that can be grouped together._  
_**THEN**: Turn the fragment into a method whose name explains its purpose._

Before:

`def print_what_is_owed(self, amount: float):   self._print_banner()      # print details   print(f"name: {self._name}")   print(f"amount: {amount}")`

After:

`def print_what_is_owed(self, amount: float):   self._print_banner()   self._print_details()  def _print_details(self, amount: float):   print(f"name: {self._name}")   print(f"amount: {amount}")`

- Might need to handle local variables. If only read but not modified, pass in as arguments. If modified, return the mutated values from the extracted method (or use class instance variables)
    

### Inline Method

_**WHEN**: Method’s body is as clear as it’s name._  
_**THEN**: Put the method’s body into the body of its caller and remove the method._

Before:

`def get_rating(self):   return (2 if self.more_than_five_late_deliveries() else 1)  def more_than_five_late_deliveries(self):   return self._number_of_late_deliveries > 5`

After:

`def get_rating(self):   return (2 if self._number_of_late_deliveries > 5 else 1)`

### Inline Temp

_**WHEN**: A temp variable assigned to once with simple expression_  
_**THEN**: replace all references to that temp with the expression_

Before:

`base_price = order.base_price() return base_price > 1000`

After:

`return order.base_price() > 1000`

### Replace temp with query

_**WHEN**: A temporary variable holds the result of an expression_  
_**THEN**: extract the expression to a method and replaces occurrences of temp with the new method._

Before:

`def compute_discounted_price(self):   base_price = self.quantity * self.item_price   if base_price > 1000:     return base_price * .95   else:     return base_price * .98`

After:

`@property def discounted_price(self):   if self.base_price > 1000:     return self.base_price * .95   else:     return self.base_price * .98  @property def base_price(self):   return self.quantity * self.item_price`

- If the expression is expense to compute, then this refactor doesn’t make sense. You’re better off computing once and keeping a copy of it.
    

### Introduce explaining variable

_**WHEN**: you have a complicated expression_  
_**THEN**: use temporary variables for parts of the expression, with descriptive names._

Before:

`if (   'MAC' in platform.upper() &&   'IE' in browser.upper() &&   was_initialized() &&    resize > 0 ):   # do something`

After:

`is_mac_os = 'MAC' in platform.upper() is_ie_browser = 'IE' in browser.upper() was_resized = resize > 0  if (   is_mac_os &&   is_ie_browser &&    was_initialized() &&    was_resized ):   # do something`

- Easier to understand semantically what the conditional logic is doing. Otherwise you are translating in your head what an expression like “'MAC' in platform.upper()“ means.
    

## Chapter 7: Moving Features Between Objects

## Chapter 8: Organizing Data

## Chapter 9: Simplifying conditional expressions

## Chapter 10: Making method calls simpler

## Chapter 11: Dealing with Generalization
