## SOLID

SOLID is an acronym of ther acronyms, which means its a mecronym.

**SRP** - Single Responsibility Principle

**OCP** - Open Close Principle

**LSP** - Liskov Substitution Principle

**ISP** - Interface Seragation Principle

**DIP** - Dependency Inversion Principle

### Single Responsibility Principle
Each software module should have one and only one reason to change.

The module can be a C# class or a method. The individual classes and methods in our applications defines what an application does and how it does it.

- Multipurpose tools don't perform as well as dedicated tools.
- Dedicated tools are easier to use
- A problem with one part of a multipurpose tool can impact all parts

#### Tight Coupling
Binds two (or more) details together in a way that's difficult to change.

When two or more details are intermixed in the same class, it inroduced tight coupling.

#### Loose Coupling
Offers a moduler way to choose which details are involved in a perticular operation. 

Loose Coupling refers to the approaches that can be used to support having difficult details of the application interact within each other in a modular fashion.

This led to another principle which is "Separation of Concerns".

#### Separation of Concerns
Program should be separated into distinct sections, each addressing a seperate concern, or set of information that affects the program.

> Class elements that belong together are cohesive.

Cohesive describes how closely related elements of a class or module are one another. Classes that have many responsibilities will tends to have less cohesion then classes that have single responsibilities. This means single responsibility classes will have more Cohesion.

![](/solid-principles/imgs/ClassesCohesion.png)

![](/solid-principles/imgs/LowClassesCohesion.png)

![](/solid-principles/imgs/HighClassCohesion.png)

![](/solid-principles/imgs/ClassCouplingAndCohesion.png)

The relationship within each class represents Cohesion. Relationship between classes represents Coupling.

#### How many responsibilities did you find in RatingEngine?

![](/solid-principles/imgs/RatingEngine1.png)

![](/solid-principles/imgs/RatingEngine2.png)

#### Responsibilities and Testability
- Difficult to test one responsibility in isolation
- Test become
   - Longer
   - More complex
   - Brittle
   - Coupled to implementation

#### Applying SRP to RatingEngine

![](/solid-principles/imgs/RatingEngineSRP.png)

#### Improved Testability
- The 3 new classes are easily tested
- The RatingEngine can now swap in test implementations of these 3 dependencies

### OCP - Open / Close Principle

Software entities (classes, modules, functions, etc.) should be open for extension, but closd for modification.

> It should be possible to change the behavior of a method without editing its source code.

![](/solid-principles/imgs/OpenClose.png)

#### Why should code be closed to modification?
Less likely to introduce bugs in code we don't touch or redeploy

Less likely to break dependent code when we don't have to deploy updates

Fewer conditions in code that is open to extension results in simpler code

Bug fixes are ok 

#### Typical Approaches to OCP 

- Parameters 
- inheritance
- Composition / Injection 

![](/solid-principles/imgs/ParameterBasedExtension.png)

![](/solid-principles/imgs/InheritanceBasedExtension.png)

![](/solid-principles/imgs/CompositionInjectionExtension.png)

#### Prefer implementing new features in new classes
- Why use new class?
  -  Design class to suit problem at hand
  - Nothing in system depends on it
  - Can add behaviour without touching existing code
  - Can follow Single Responsibility Principle
  - Can be unit-tested

Note: Below example is not related to OCP

Replace Switch with Reflection
![](/solid-principles/imgs/SwitchCase.png)

![](/solid-principles/imgs/SwitchCaseAlternative.png)

#### Example of OCP
- Packages and Libraries
  - Closed for modification
    - Consumers cannot change package contents
    - Should not break consumers when new behavior is added
  - Open to extension
    - Consumers should be able to extend the package to suit their needs

#### More resources on OCP
- Why you need to know OCP but don't 
  - https://bit.ly/2LSXOuo (https://hackernoon.com/why-the-open-closed-principle-is-the-one-you-need-to-know-but-dont-176f7e4416d)
- Open Closed Principle by Robert Martin
  - https://bit.ly/2Gmxg1Z (https://blog.cleancoder.com/uncle-bob/2014/05/12/TheOpenClosedPrinciple.h)
- Open Closed Principle by Jon Skeet
  - https://bit.ly/2AMmprC (https://codeblog.jonskeet.uk/2013/03/15/the-open-closed-principle-in-review/)

#### Key Takeaways
- Solve the problem first using simple, concrete code
- Identity the kinds of changes the application is likely to continue needing
- Modify code to be extensible along the axis of change you have identified
  - Without the need to modify its source each time

### LSP - Liskov Substitution Principle

Let f(x) be a property provable about objects x of type T. Then f(y) should be trur for objects y of tye S where S is a subtype of T.

#### Liskov Substitution Principle
Subtypes must be **substitutable** for their base types.

#### Basic Object-Orient Design
- Something IS-A something else
  - An eagle IS-A bird
- Something HAS-A property
  - An address HAS-A city

- **LSP states that the IS-A relationship is insufficient and should be replaces with IS-SUBSTITUTABLE-FOR.**

#### Classic Rectagle-square Problem
- A rectangle has four sides and four right angles
- A square has four equal sides and four right angles

Per geomarty, **a square is a rectangle.**

```
public class Rectangle
{
  public virtual int Height {get; set;}
  public virtual int Width {get; set;}
}

public class AreaCalculator
{
  public static int CalculateArea(Rectangle r)
  {
    return r.Height * r.Width;
  }
}

// Square (a Subtype of Rectangle)
public class Square : Rectangle
{
  private int _height;
  public override int Height
  {
    get {return _height;}
    set
    {
      _width = value;
      _height = value;
    }
  }
}

// width implemented similarly
```
**The Problem**

```
Rectangle myRect = new Square();
myRect.Width = 4:
myRect.Height = 5;

Assert.Equal(20, AreaCalculator.CalculateArea(myRect));

// Actual Result: 25
```

- **What Happened ?**
- Square has an invariant
  - Its sides must be equal

- Rectangle has an invariant
  - Its sides are independent 

This design breaks rectangle's invariant and this violates LSP

- Eliminate the 'Square' class completely and have a flag 'IsSquare'.

- **One Solution**

```
public class Rectangle
{
  public int Height { get; set; }
  public int Width { get; set; }

  public bool IsSquare => Height == Width;
}
```

- **Another Solution**

```
public class Rectangle
{
  public int Height { get: set: }
  public int Width { get; set; }
}

public class Square
{
  public int Side { get; set; } 
}
```

#### Detecting LSP Violations in your Code

- Type checking with `is` or `as` in polymorphic code
- null checks
- NotImplementationException

#### Type checking

```
foreach (var employee in employees)
{
  if (employee is Manager)
  {
    Helper.PrintManager(employee as Manager);
    break;
  }

  Helpers.PrintEmployee(employee);
}
```

- Type Checking (Corrected)

```
foreach(var employee in employees)
{
  employee.Print();
}

// OR

foreach(var employee in employees)
{
  Helpers.PrintEmployee(employee);
}

```
- But be careful NOT to violate SRP in `Employee` or `Helper` class.

#### Null Checking

```
foreach(var employee in employees)
{
  if(employee == null)
  {
    Console.Writeline("Employee not found.");
  }
  Helpers.PrintEmployee(employee);
}
```

#### Not Implemented Exception
```
public interface INotificationService
{
  void SendText(string SmsNumber, string mesage);

  void SendEmail(string to, string from, string subject, string body);
}

public class SmtpNotificationService : INOtificationService
{
  public void SendEmail(string to, string from, string subject, string body)
  {
    // actually send email here
  }

  public void SendText(string SmsNumber, string message)
  {
    throw new NotImplementationException();
  }
}
```

![](/solid-principles/imgs/LspIsASubsetOfPolymorphism.png)

#### Fixing LSP Violations
- Follow the "Tell, Don't Ask" principle
- Minimize null checks with
  -  C# Features
  -  Gaurd clauses
  -  Null Object design pattern
- Follow ISP and be sure to fully implement interfaces

![](/solid-principles/imgs/TellDontAsk.png)

#### Key Takeaways
- Subtypes must be substitutable for their base types
- Ensure base types invarient are enforced
- Look for 
  - Type checking 
  - Null checking 
  - NotImplementedException

### ISP - Interface Segregation Principle
Clients should not be forced to depend on methods they do not use.
- Prefer small, cohesive interfaces to large, "fat" onces.

#### What does interface mean in ISP ?

- C# `interface` type/keyword
- Public (or accessible) interface of a class

A type's interface in this context is whatever can be accessed by client code working with an instance of that type.

#### What's a Client?
In this context, the client is the code that is intreacting with an instance of the interface. It's the calling code.

- Violating ISP results in classes that depend on things they don't need.

#### Detecting ISP violations in your code
- Large interfaces
- NotImplementedException
- Code uses just a small subset of a larger interface

- A poorly-designed Interface

```
public interface INotificationService
{
  void SendText(string SmsNumber, string mesage);

  void SendEmail(string to, string from, string subject, string body);
}
```

- Interfce Lacks Cohesion 

```
public class SmtpNotificationService : INOtificationService
{
  public void SendEmail(string to, string from, string subject, string body)
  {
    // actually send email here
  }

  public void SendText(string SmsNumber, string message)
  {
    throw new NotImplementationException();
  }
}
```

- Split it up

```
public interface IEmailNotificationService
{
  void SendEmail(string to, string from, string subject, string body);
}

public interface ITextNotificationService
{
  void SendText(string SmsNumber, string message):
}
```