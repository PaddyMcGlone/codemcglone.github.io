---
layout: post
title:  "Dependency Inversion Principle [DIP]"
date:   2019-08-27 18:00:00 +0000
---

> A high level module should not depend on a low level module, they should both rely upon abstractions.
> Abstractions should not depend on details, details should depend upon abstractions

Ok another principle, this time the Dependency Inversion Prinicple - what is it, why is it important, what does it look like in code. Lets get stuck in !

### Part One

This principle can be easily split into two parts, lets make a start on the first part:

> A high level module should not depend on a low level module, they should both rely upon abstractions.

In a basic ASP.Net web application, when you create a standard CRUD controller, it will more than likely have a dependency on a persistence layer (unit of work) for retrieving and saving data. In the example below we have a very simply controller which is used for displaying a list of all vans on a web page.

    public controller VansController : Controller
    {
        private readonly DbContext _context;

        public VansController()
        {
            _context = new DbContext;
        }

        public actionresult Index()
        {
            var vans = _context.Vans.GetAll();

            Return View("Index", vans);
        }
    }

In the code example shown above, the VanController is known as a 'High-level' module and the DbContext is known as the 'Low-level' module. The problem with the code example is, we have introduced a 'tightly-coupled' relationship between our van controller and entity framework. 

Tightly coupled means the two items are highly dependent on one another, in this example our controller is highly dependent on entity framework in order to retrieve the list of vans.  This means we change in one module will result in code change in the other highly dependant module.

Software enginering is about minimizing the impact change can have on your application.

The solution

As noted earlier, the solution to this issue is to introduce an abstraction into the relationship between the two modules. As you can see in the code example below this abstraction takes the form of an interface known as IUnitOfWork and both the controller and unit of work and therefore reliant upon this abstraction rather than each other. 

    public controller VansController : Controller
    {
        private readonly IUnitOfWork _unitOfWork;

        public VansController(IUnitOfWork unitOfWork)
        {
            _unitOfWork = unitOfWork;
        }

        public actionresult Index()
        {
            var vans = _unitOfWork.Vans.GetAll();

            Return View("Index", vans);
        }
    }

The beauty of this abstraction is we can now swap out Entity Framework for another presistance method and it will have no affect on our controllers. 

This abstraction also means we can introduce unit testing within our application as we can now 'mock' our dependencies.

### Part two

> Abstractions should not depend on details, details should depend upon abstractions

The second part to this principal sounds complicated, but it is fairly straight forward. What we are stating here is when we create an abstraction such as our interface earlier. This interface should not be reliant upon a module, such as Entity Framework.

    public interface IUnitOfWork
    {
        void SaveChange();
        Vans: VanRepository;
    }

In our interface above, the van repository is still dependent upon entity framework. To resolve this issue we can introduce another abstraction which knows nothing about Entity Framework.

    public interface IUnitOfWork
    {
        void SaveChanges();
        Van: IVanRepository;   
    }