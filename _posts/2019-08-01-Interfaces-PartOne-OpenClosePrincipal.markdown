---
layout: post
title:  "Interfaces Part one - Open/Closed principal"
date:   2019-08-01 18:00:00 +0000
---
## Interfaces – Part one:  The open / closed principal

When I was a junior software engineer working with Borland Delphi 7 I couldn’t wrap my head around interfaces.

    'Why use an interface, surely its easier to just simply call the method.'

    'What is it even used for, is it the same as inheritance.'

 These were questions I often asked myself during this time and it wasnt until I made the move to developing web apps that their purpose started to make sense.

## What is an interface?

I like to think of an interface as an abstraction between the caller and provider, a way of adding a buffer or ‘separation of concerns'. Think of interfaces as a way of making your class less dependent on another specific class which is providing the functionality. What do I mean by that? Let's jump straight into code. 

    Public class StockRoom 
    { 

        Private readonly EmailService _service; 

        Public StockRoom() 
        { 
            _service = new EmailService(); 
        }  

        Public void CheckStock() 
        { 
            // Do some stock level calculations.. 
            
            If(stockLevel <= AcceptableLevel) 
                SendMessage(“Christina - Order more books pronto!”); 
        } 

        Public void SendMessage(string message) 
        { 
              _service.Send(message); 
        } 
    } 

If we look at the code above, we can see that the developer has defined a 'tightly coupled' class, meaning our class is highly dependent on the class email service. If we were working on a really simple web application and we were not concerned about unit testing or the open closed principal (Software should be open for extension but closed for modification) this code would be fine. 

However, what happens if one day the customer rings the developer and asks ‘Hey developer, email alerts is so old fashioned, can I please swap it out for WhatsApp notifications?’.  

Straight away the developer is thinking, ‘oh no, I have to change and redeploy my stockroom class and I also have to create a new service and add it’. 

This is where interfaces can come to the rescue and save the developer from all these headaches. 

Let’s create an interface for adding alerts, as you can see from the class below, we define an interface or ‘a contract’ which states, when you implement this interface – you must define these methods: 

    Public interface Alert 
    { 
        Void Send(string message); 
    } 

Now that we have defined our interface / contract, we can then define a class to implement this contract:

    Public class WhatsAppAlerts : Alert 
    { 
        public void Send(string message) 
        { 
            Console.WriteLine($"WhatsApp notification: { message }") 
        } 
    } 

What we have done in the code above is define a contract that states, when implemented a class must implement a send method which recieves a message as a param. In our example above, the Send message is implemented by the WhatsAppAlerts class.

Now that we have a contract and a class implementing that contract, we can update our formerly tightly coupled class, to something a little less dependant: 

    Public class StockRoom 
    { 
        Private readonly IAlert _alter; 

        // alert object managed via Dependancy Injection
        Public StockRoom(IAlert alert) 
        { 
            _alert = alert;               
        }  

        Public void CheckStock() 
        { 
            // Do some stock checking calculations.. 

            If(stockLevel <= AcceptableLevel) 
                SendMessage(“Christina - Order more books pronto!”); 
        } 

        Public void SendMessage(string message) 
        { 
            _alert.Send(message); 
        } 
    } 

## Outcome of adding an interface

As you can see in the class above, the StockRoom class no longer cares who is in charge of sending the message, it calls alert and sends a message, this class is no longer concerned with the medium used to transport this message.

        > Please note the use of dependancy injection above for the creation of the alert logic. If you are unfamilar with this concept I will create another blog article covering this concept. For now, simply think of this as a method of improving the dependancy inversion and another entity is now incharge of creating this object.

What we have achieved in the code above is add loose coupling to our stockroom class. We have moved from the class being responsible for sending the message to calling an abtract which handles the send for us. This means in the future if the customer decides to switch back to emails or an alternative messaging approach. The developer simply has to implement a new class for sending the message and inherit from Alerts – the StockRoom will remain unchanged. 

## The open / closed principal

In object orientated programming terms, this is what is known as the open-closed principal, as objects should be open for extension but closed for modification. This was implemented in our example by the SendMessage changing from Email to WhatsApp (and in the future an alternative method) without having to modify our StockRoom class.

I hope this short article has helped to slightly improve your understanding on interfaces, in part two I will explain the role interfaces plays in Polymorphisim and also allowing for unit testing to be incorporated within your application.