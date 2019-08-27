---
layout: post
title:  "Dependency Inversion Principle [DIP]"
date:   2019-08-27 18:00:00 +0000
---

> A high level module should not depend on a low level module, they should both rely upon abstractions.
> Abstractions should not depend on details, details should depend upon abstractions

The Dependency Inversion Principle is the 'D' of the 'Uncle Bob's' [SOLID principles](http://blog.cleancoder.com/) which are implemented to improve the robustness and maintainability of your software application.

This principle although it sounds scary, can be better explained by breaking it down into two parts following a simple code example.

## The code issue we will improve

We have been approached by Volkswagen to create a web application for white water kayakers whom love their vans (sounds great so far).
The first requirement from the customer is to create a list of all the current vans in stock on the home page, so our developer creates a very simple app and delivers it to the customer:

    public controller VansController : Controller
    {
        private readonly UnitOfWork _unitofWork;

        public VansController()
        {
            _unitOfWork = new UnitOfWork;
        }

        public actionresult Index()
        {
            var vans = _UnitOfWork.VanRepository.GetAll();

            Return View("Index", vans);
        }
    }

As you can see from the code above, this is a really simple web application. We are mannually creating an instance of the unit of work and using it to retrieve the data we need for our view and passing it to the index page. 

For a really simple web application, this design approach is totally acceptable. However, when our application grows in size managing the risk involved in implementing these changes is going to be a difficult task.

Lets use the dependency principle to improve our web application, as mentioned earlier we can split this principle into two sections:

## Part One - Adding abstractions.

> A high level module should not depend on a low level module, they should both rely upon abstractions.

- High level module = area which provides complex rules / business logic i.e. a controller.
- Low level module = area which provides utility functionality i.e. Entity Framework / unit of work.

In our van application, the VanController is known as a *High-level* module and the Unit of work is known as the *Low-level* module. The van controller is directly responsible for creating an instance of the UnitOfWork and uses this instance to retrieve data from the database. You could describe this relationship between both modules as being *tightly-coupled*. 

Tightly-coupled means the two items are highly dependent on one another, in this example our controller is highly dependent on entity framework to retrieve the data. This means a code change in one module will almost certainly result in code change in the other highly dependant module.

Software engineering is about minimizing the impact change can have on your application.

### The solution

As hinted in the title earlier, the solution is to introduce an abstraction between both modules. The purpose of adding an abstraction is to allow the module to become dependant upon a *go-between* instead of each other. So lets introduce an abstraction between our high level and low level modules in the van application:

    public controller VansController : Controller
    {
        // Note: We are now using the unit of work interface.
        private readonly IUnitOfWork _unitOfWork;

        public VansController(IUnitOfWork unitOfWork)
        {
            _unitOfWork = unitOfWork;
        }

        public actionresult Index()
        {
            // GetAll() method will be defined within the interface.
            var vans = _unitOfWork.Vans.GetAll();

            Return View("Index", vans);
        }
    }

As you can see in the code example above this abstraction takes the form of an interface known as IUnitOfWork and both the controller and unit of work and therefore reliant upon this abstraction rather than each other. 

The beauty of this abstraction is we can now swap out Entity Framework for another presistance method and it will have no affect on our controllers. The controller no longer has any knowledge of how the data is retrieved, it simply tells a black box - *go get me some data..now*.

This abstraction also has other benifits, we can now also introduce unit testing within our application as we can now 'mock' our data retrieval dependencies.

## Part two

> Abstractions should not depend on details, details should depend upon abstractions

The second part to this principal sounds complicated, but when explained with a simple problem I feel its actually quite straight forward. 

What we are stating here is when we create an abstraction such as our interface earlier, this interface should not be reliant upon details, such as the persistance method (Entity Framework).

When we created our interface earlier we added the standard save changes method and also the VanRepository so we could access the van data we require. 
The problem with this is the VanRepository is still reliant on Entity Framework, which means our abstraction is reliant upon Entity Framework. Therefore, our abstraction is dependant upon details.

    public interface IUnitOfWork
    {
        void SaveChange();
        Vans: VanRepository;
    }

The solution is really simple, lets add another layer of abstraction between our interface and the data layer. Lets create an interface for our repository and then use this definition within our new unit of work interface

    public interface IVanRepository
    {
        //.. Some repository data here
    }

    public interface IUnitOfWork
    {
        void SaveChanges();
        Van: IVanRepository;   
    }

As you can see from the snippet above, our unit of work interface is know completely independant from any low level module or details.

## Conclusion

<em>Before:</em>

    public controller VansController : Controller
    {   
        private readonly UnitOfWork _unitofWork;

        public VansController()
        {
            _unitOfWork = new UnitOfWork;
        }

        public actionresult Index()
        {
            var vans = _UnitOfWork.VanRepository.GetAll();

            Return View("Index", vans);
        }
    }

<em>After:</em>

    public controller VansController : Controller
    {
        // Note: We are now using the unit of work interface.
        private readonly IUnitOfWork _unitOfWork;

        public VansController(IUnitOfWork unitOfWork)
        {
            _unitOfWork = unitOfWork;
        }

        public actionresult Index()
        {
            // GetAll() method will be defined within the interface.
            var vans = _unitOfWork.Vans.GetAll();

            Return View("Index", vans);
        }
    }

So what have we improved in our web application by introducing these changes:
- Loose coupling: Our modules are no longer strongly reliant upon each other
- Ability to unit test: The introduce of loose coupling as allowed for the ability to add unit tests
- Improved robustness & maintianability: Our web applicaiton is now in a better place to respond to change. A change which will not result in large knock on effect within the application.

I like to think of a good software application as a modular device. You should be able to lift areas of application and re-use it within another similar applications. Much like a van manufacturer would implement a new efficent engine component and re-use it accross all the engines in their vehicle range.

A poor software application is one which doesnt have the ability to share its functionality or recieve updates from other apps. 

By implementing the Dependency Inversion Principle, I feel you are ensuring your application has the ability to gracefully react to change and recieve or share components.

Thanks for reading