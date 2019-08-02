---
layout: post
title:  "Information Expert Principal"
date:   2019-07-25 17:00:00 +0000
---
## Right, what is this all about?

In a nutshell, the Information Expert Principal states if you are writing a new method in your application, rather than placing it within a bloated service or controller, you should place the method where the properties used within the method are defined.

## Lets look at some code
Below is a really simple controller and a helper method known as process van.
Please ignore the fact that this is poor ASP.Net code as the controller is not only managing and directing the request, but also performing validation.

    public ActionResult ProcessVan(Van model)
    {
        var okToProceed = ValidateVan(model);

        if(okToProceed) return RedirectToAction("NextStep");    
    }

    public bool ProcessVan(van model)
    {
        if (model.engineSize < 140)
            return false;
    
        if(model.type != "Volkswagen")
            return false;

        if(model.Colour == "Grey")
            return true;

        return true;
    }

## Feedback

If we look at ProcessVan method, within this method we are working with the class Van and using the properties within this class to validate the instance of van. This is an example of smelly code, as here we are defining behaviour (in this instance validation behaviour) of an object which is outside of the object. 

Also within this method we are constantly having to prefix the properties we want to evaluate with the model name, this is not helping to improve code readability.

To help improve the code readability for the first time reader, lets move this code to the information expert, which is the Van class.

## Refactor

    public ActionResult ProcessVan(Van model)
    {
        if(model.Validate()) return RedirectToAction("NextStep");
    }

    public class Van : Vehicle
    {        
        public string Type { get; set;}
        public string Colour {get; set;}
        public strubg EngineSize {get; set;}             

        public Validate()
        {
            if (this.EngineSize < 140)
                return false;
    
            if(this.Type != "Volkswagen")
                return false;

            if(this.Colour == "Grey")
                return true;

            return true;
        }        
    }

Firstly, we could further improve this code by making this class inherit from IValidatable and remove the need to call the method from the controller. However, I wanted to keep things simple for this blog entry, I will touch on IValidatable in another entry.

Lets now have a look at our controller, now that we have placed the validation method within the model, we can simply give it a more meaningful name like <em>'validate'</em> and the user will be aware of the methods purpose. Also note the lack of params passed into the method. This is because we are currently working with the current instance of this object and the method is already aware of the property values.

Finally, we are not only removing the validation logic from the controller and placing it within a shared place which can be easily unit tested. By employing the information expert principal, we are allowing this code to be reused within multiple places within our application. 

This also means that if we wish to extend or modify the rules for validating a van, we can make a change in one place and it will be reflected throughout the rest of the applicaton.

## Conclusion

The Information Expert principal not only allows engineers to improve the readability of their code. It also helps to improve Encapsulation or information hiding, as if we wish to extend or remove rules from the validation method. This change will only affect the van class and we will not have a number of dependant classes affected throughout the application, such as a the VehicleController in our earlier example.

When I first started developing ASP.Net web applications, I failed to deploy this principal and as a result I was developing fat controllers and services which were hard to unit test and also maintain. By applying this prinicpal I have found I have been able to improve the quality of work via regards to <em>'clean code'</em> and testability.

So if you are writing a new method in your class, why not take a second and look at your rules and think <em>'Does this method belong here?'</em>