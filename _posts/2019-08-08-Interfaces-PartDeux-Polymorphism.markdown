---
layout: post
title:  "Interfaces [Part Deux] - Adding Polymorphism"
date:   2019-08-8 18:00:00 +0000
---
### Hang on a minute, what’s this polymorphism about?

Firstly what is this term Polymorphism and what does it mean?

When you <em>'Google'</em> the term Polymorphisim OOP we receive a barrage of answers, some of which state

>the ability for an object to take on many forms

>It is the ability to process objects differently based upon their type / class.

### Enough talk - show me code

It really quite a simple and yet powerful object orientated programming concept. 
Let’s try and understand this concept via a simple code example:

    Public class Program 
	{
        Public void DrawShapes(List<Shape> shapes)
        {
	        Foreach(var shape in shapes)
	        {
                Switch(shape.type)
                {
	                Case “circle” :
		                Console.Writeline(“Printing a circle”);
		                break;
	                Case “Square” :
		                Console.Writeline(“Printing a square”);
		                Break;
                }
            }
        }
    }

In the code above we have a method which simply wants to print all of the shapes which are currently in the list shapes. But what if someone decides to make a change to the original requirements (i know like that will ever happen) and adds a new shape...

This code is not <em>extensible</em> and the addition of this new shape will force a code change in our switch statement to handle the new type and printing method needed.

This class is also breaking the <em>information expert principal</em> that we discussed in a previous entry. 

At present the DrawShape class knows how to print each shape, this is knowledge that doesn’t belong in this class. This information belongs within each shape class (will also introduce cleaner code).

### How can we fix this issue?

Ideally we want our DrawShapes method to simply iterate through the list of shapes and ask each shape to draw itself. After all isn't that the single reponsibility of this method?

This method doesn’t need to know how each shape does it, its role is to simply pass through the list and request it’s drawn on screen.

    // This is Polymorphism, Draw() knows how to handle each shape
    Public void DrawShapes(List<Shape> shapes)
    {
	    Foreach(var shape in shapes)
		    shape.Draw();
    }

Ok – so how do can we make this change possible in our application? 
Let’s start by adding a Draw method to our base class of Shape, which is being called in the method above.

    Public class Shape
    {
	    private int Area  { get; set; }
	    private int Sides { get; set; }
        
        public virtual void Draw()
        {
	        Console.Writeline(“ShapeClass: Drawing a generic shape.”)
        }
    }

Note we have added the <em>virtual</em> keyword above, the reason for adding this keyword is so this method if necessary can be overwritten within inherited classes to draw specific shapes.

So now we have a base class created, let’s invoke the information expert principal and move the draw logic for drawing each specific shape from the DrawShapes method into the specific classes:

    Public class Square : Shape
    {
        Public override void Draw()
        {
	        Console.Writeline(“Square class: Drawing a square shape”);
		}
    }

    Public class Circle : Shape
    {
        Public override void Draw()
        {
	        Console.Writeline(“Circle class: Drawing a circle shape”);
		}	
    }

This code is polymorphism in action, we are inheriting the virtual shape draw method within each child shape class and then overriding the base draw method with a specialised method for our specific class. 
We then call this for each specific shape or use the default method if suitable.

### Outcome
By introducing Polymorphism into our application we are improving encapsulation and also preventing the creation of fat methods and helper classes within our application. 
This convention also improves code readability and testability.

### Using Polymorphism with interfaces
In the last entry, we resolved a problem with the customer wanting to change their messaging platform but this change was going to have an impact on the existing Stockroom class and force a code change. 

This is why we introduced interfaces into our application to implement the <em>'Open/Closed'</em> principal, to make our app more extensible and reactive to change.

>A modern software application is one where we can change the behaviour of a class without having to change the code. This is achieved through extensibility, rather than changing existing application code we alternatively add new classes to our application

However, in the last module if the customer wanted to add a new communication method. Our solution meant they had to remove a class and add a new class which implements the messaging contract (interface).

    Public class WhatsAppMessenger : IAlert
    {
        Public void Send(string message)
        {
            Console.WriteLine($”WhatsApp notification: {message}”);
        }
	}

### Using multiple notification alerts

What if the customer wanted to keep the email service and add a WhatsApp notification on top of their current notification stack?

Now using our newly founded Polymorphism knowledge, we can implement this into our StockRoom application.

    Public class StockRoom 
    { 
         // A list of possible message portals
        private readonly IList<IAlert> _alerts
    
        Public StockRoom() 
        { 
	        _alerts = new List<IAlert>();
        }  

        Public void CheckStock() 
        { 
            // Do some stock checking calculations.. 
            If(stockLevel <= AcceptableLevel) 
                SendMessage(“Christina - Order more books pronto!”); 
        } 

        Public void SendMessage(string message) 
        { 	
            // Polymorphically sending a message via multiple forms
	        foreach(var alert in _alerts)
                portal.Send(message); 
        } 

        // A method used to add alert types
        Public void AddNewMessagePortal(IAlert alert)
        {
            _messagePortal.Add(alert);
        }
    }

We have introduced polymorphism into our StockRoom application via interfaces. All of the messaging types (WhatsApp, TextServicem EmailService) now implement IAlert and we circulate through a list of alert types and send the message.

We can now call the StockRoom App and add or remove messaging types without having to make changes and recompile the main stock room class.

    public class Program
    {
        var stockRoom = new StockRoom();
        stockRoom.AddMessagePortal(new WhatsAppService());
        stockRoom.AddMessagePortal(new EmailService());
        stockRoom.CheckStock();
    }

