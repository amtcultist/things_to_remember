Creational Design Pattern
=========================

## Factory Method

As a matter of facts, factory method commonly referred to both the method that create an instance, and the design pattern.

#### Example of a factory method:
```ts
function createBMWCar(): Car {
  car: Car = new Car();
  car.name = "BMW";
  car.doStuff = () => {
    console.log("im rich");
  };
  return car;
}
```
This, however, is different from the ***SPECIAL*** factory method we will use.

### Problem
You are working at a rising company. However, when your company extends, you received a new deal. Your codebase, however, is coupled to a certain class now, and it will take a lot of effort to at another one. Have I mentioned the risks within this action ?

### Solution
* Replace direct call (using `new`) with a special factory method. Objects return from these methods often referred as products.
* By doing so, you can have the advantage of override the factory method within subclasses and change property - if not the whole class of the product.

### Intent
* Define an interface for creating an object, but let subclasses decide class to instantiate
* Defining a virtual constructor, since it will create instances not by using a normal constructor (new keyword) but a method.

### Limitation
* Only products with common base class or interface - aka same factory, can apply this. This is common sense, since you cannot product shoes in a food factory, right.
* The factory method in the base class should have its return type declared as this interface.

### Study case
We have this nice constructor of a car factory - and different car brands

```
Car -> BMW 
    -> Audi
    -> Lamborghini
```
To be honest, if we ***KNEW*** there only 3 subclasses of car, we probably won't need to apply factory methods <br/><br/>

However, life is not simple, so are BAs and POs. If my worklife balancing is a game quest, they must be that impossible boss you died 69420 time before get over in darksoul. <br/><br/>

Anyway, since we don't know and won't sure that there only 3 subclasses, we create a factory method within the base class to ***STANDARIZE*** the architechture model. <br/><br/>

### Study case application
First we have our common interface
```ts
interface ICar {
  honk: () => void
}
```

Then we have these three subclasses
```ts
class BMW: ICar {
  constructor() {}
  
  honk() {
    console.log('BMW');
  }
}
```

```ts
class Audi: ICar {
  constructor() {}
  
  honk() {
    console.log('Audi');
  }
}
```

```ts
class Lamborghini: ICar {
  constructor() {}
  
  honk() {
    console.log('Lamborghini');
  }
}
```

#### Example of special factory method within base class
```ts
class Car {
  createCar(carType: CarEnum): ICar {
    switch (carType) {
      case CarEnum.BMW:
        return new BMW();
      case CarEnum.LAMBORGHINI:
        return new Lamborghini();
      case CarEnum.Audi:
        return new Audi();
      // TODO add more car
      default:
        throw new Error("Illegal car");
    }
  }
}
```

So for example, when we call a car like this
```ts
  @Inject(Car) car
  const currentCar = car.createCar(CarEnum.BMW);
  currentCar.honk();
```
While the output in this case is 'BMW', we can simply change the CarEnum value and get another subclass instantiate. How simple is that!

## TLDR
* Factory method design pattern provides a way for creating objects in the base class but also allow subclasses options to choose the type of objects to instantiate.
* The `new` keyword is a no-no in this case. There is a difference between requesting an object and creating a new one.
Since the `new` operator ***ALWAYS*** creates an object, it will fail to encapsulate object creation! Factory Method, on the other hand, enforces that sweet encapsulation for us,
and allows an object to be requested without create another one.
## Extra info within Factory Method
* One of the advantage of a Factory Method is that it can be customizable easy. You can return the same instance multiple times (like a singleton) by saving the instance within base class, or can return a subclass rather than an object<br/>

Example of return absolute singletons by saving
```ts
class Car {
  // Singletons variable
  private bmw: BMW;
  private lamborghini: Lamborghini;
  private audi: Audi;
  
  createCar(carType: CarEnum) {
    switch (carType) {
      case CarEnum.BMW:
        return this.bmw ?? new BMW();
      case CarEnum.LAMBORGHINI:
        return this.lamborghini ?? new Lamborghini();
      case CarEnum.Audi:
        return this.audi ?? new Audi();
      // TODO add more car
      default:
        throw new Error("Illegal car");
    }
  }
}
```

Example of returning subclass
```ts
class Car {
  createCar(carType: CarEnum) {
    switch (carType) {
      case CarEnum.BMW:
        return BMW; <- subclass
      case CarEnum.LAMBORGHINI:
        return Lamborghini 
      case CarEnum.Audi:
        return Audi;
      // TODO add more car
      default:
        throw new Error("Illegal car");
    }
  }
}
```
To be honest, since JS treated ***EVERYTHING*** as object (so are subclasses), it is easy to return like this.
* Some recommend that when using Factory Method, absolute all constructors should be private or protected. This, is a matter of how programming languages handle classes (and style, of course).
You should research what's best when designing your projects.

### When to use Factory Method
* When you don’t know beforehand the exact types and dependencies of the objects your code should work with.
* When you want to provide users of your library or framework with a way to extend its internal components.
* When you want to save system resources by reusing existing objects instead of rebuilding them each time.

## Extra usage info (require other Design Patterns knowledge)
* Abstract Factory (another design patterns) classes often implemented Factory Methods, but they can be implemented using Prototype.
* Factory Methods are usually called within Template Methods.
* Factory Method: creation through inheritance. Prototype: creation through delegation.
* Common design practice: Factory Method (simple, customizable, subclasses proliferate) -> Abstract Factory -> Prototype -> Builder (more flexible, more complex) if more flexibility is required.
* Prototype doesn't require subclassing, but it does require an initialize operation. Factory Method requires subclassing, but doesn't require initialize (since it will initialize within the factory)
