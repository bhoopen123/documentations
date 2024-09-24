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