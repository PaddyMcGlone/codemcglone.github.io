---
layout: post
title:  "BDD - Behave yourself now"
date:   2019-07-20 18:20:20 +0000
---
## Why waste time writing tests, I've work to do 

>‘Why should I bother writing tests, sure it's only a one-line change.’ 

>‘There's no need to write a test, sure there's bound to be other tests in the system that already test this.’ 

>‘I’ve no time to write tests, I need to get this in for the release.’ 

In any modern software application, your tests are just as important (if not more important) than the code you are delivering. These tests represent the requirements of your software application, the <em>‘living documentation’</em>. These tests allow you to confidently make bold changes to live software and help explain your application to new team members or even the customer (via Gerkin). 

If you feel you cannot write a test to represent the functionality you are about to deliver then stop, you don’t understand your requirements. 

PS. If you have no testing backbone in your app, how can you ever confidently refactor, answer is you can't. 

If you keep reading I will tell you my story and how BDD improved me as an engineer. 

## My story 

When I started working for my current company, I struggled with the concept of behavior driven development (BDD). I came from a background of fix the problem, test the fix thoroughly by hand and then release. 

The idea that writing tests before you wrote your actual ‘production’ code sounded so foreign to me that I thought it was a waste time and a distraction from the ‘real software development’. 

When I was learning this new approach to software development, a number of questions ran through my head: 

>Why would you want develop code like this? 

>What benefit would it bring to my solution, other than taking longer to create something simple? 

>How would you know what tests to write up front and what to put in these tests? 

Questions many inexperienced developers such as myself ask themselves at the start of their BDD journey. Being unable to work out the answer to these questions, I created what I thought was a development practice which worked for me: 

<ol>
<li> Create the code fix first </li>

<li> Browser test it </li>

<li> Add some tests to keep the ‘reviewer’ happy. <li>
</ol>

I'm not proud of this approach, but it's important to be truthful, this is how I used to develop professional software prior to BDD! <strong>My apologies to Roger & Colin if you are reading this</strong>. 

## The outcome of this approach 

At the time of learning BDD, I was working on a very busy project and our development team was put under a lot of pressure to deliver software. When inexperienced developers are put under such pressure, they normally resort to the aforementioned approach, their comfort zone. Following this approach is easy and it allows a developer to produce results quickly (which looks good to project managers and on Kanban boards). 

However, by following this approach what normally happens is the developer will solve problem <em>‘A’</em> and the test team / customer will test and confirm the issue is resolved.  

But during test cycles, the test engineer or customer will normally create numerous scenarios to verify your fix (problems ‘B’ and ‘C’), these scenarios are normally different workflows that will occur when your system goes <em>‘live’</em>. 

If you have developed a software solution which is just focused on problem <em>‘A’</em> then the chances are problem <em>‘B’</em> and problem <em>‘C’</em> are going to fail during testing and potentially jeopardize the release. 

The outcome of the developer not considering all of the possible scenarios when designing their <em>‘fix’</em> is the company will lose revenue due to the time spent resolving these ‘unforeseen issues’ and a possible lack of confidence in the developer's ability to produce working solutions from the customer and the project manager. 

Remember the goal is always to develop and deliver a software solution which meets all of the customers expectations. 

## Behave yourself 

Receiving an email from the customer or project manager stating there’s problems with your code fix soon becomes old, and the importance of code quality and testing coverage soon becomes paramount. In these scenarios I found myself turning to BDD and it soon transformed how I worked. 

BDD or Behavioral Driven Development focuses on creating test scenarios which are focused on the end user and how they behave when they are using your application. 

<strong>Given</strong>

<strong>When</strong>

<strong>Then</strong>

In our current project we implement BDD using both functional (integration) and unit tests, my personal favorite being the unit tests delivered using Machine Specification (MSpec) due to their simplicity and speed of execution and it is for that reason they will become our focus. 

## Creating our first test 
A driver with a current driving license and a lorry driving entitlements should provide advanced driving info when completing a license renewal. 

In the scenario above, we will want to add a page to the renewal online form which will request the additional information from a driver of a motor vehicle. So, before we write any production code, we will define the first test: 

    // Test 1 - Problem A 
    class when_I_call_CreateWizard_with_lorry_driver  
    { 
        static IEnumerable results; 

        // Given  
        Establish context = () => 
            var driver = new Driver{ LorryDriver = true }  

        // When  
        Because of = () => 
            results = renewal.CreateWizard(driver);  

        // Then  
        It adds_the_advanced_page = () => 
            results.Contains("AdvancedDriverInfo").ShouldBeTrue();  
    }  

By writing this test first, the developer will gain an understanding of what code they will have to write before they produce any production code. The next step is to add just enough produce code to make our test pass, in this step we add just enough code to ensure our test passes - nothing else. 

## The advanced scenarios 
Now that we have created just enough code to get our test to pass, the next step is to consider the other possible scenarios (problems ‘B’ and ‘C’).  

This is the most important stage of BDD as it is here that the developer will consider all possible scenarios, strengthen their understanding of the problem domain and potentially raise questions to the product owner / BA which will force them to contact the customer. Thereby encouraging the customer to fully consider the problem domain as well. 

>(The developer here is actually helping their BA and the customer understand all possible issues)

Our scenario is a little simpler, we will test what happens when a non-lorry driver calls the wizard 

    class when_I_call_CreateWizard_with_motorbike_driver  
    {  
        static IEnumerable results; 

        // Given  
        Establish context = () => 
            var driver = new Driver{ MotorBikeDriver = true }  

        // When  
        Because of = () => 
            results = renewal.CreateWizard(driver);   

        // Then  
        It adds_the_advanced_page = () => 
        results.Contains("AdvancedDriverInfo").ShouldBeFalse(); 
    }  

When we run this test, it should now pass due to the code written for test one. But what if we question does the drivers age have an impact on the page being displayed, and the BA says yes. This is where your BDD approach can become vital. 

In this instance we could write new tests this time stating the drivers age (above and below limit) and verify the page is either shown or hidden. 

## The hidden advantage of BDD 

Another hidden advantage of BDD, is in the scenario above we are capturing all of our requirements for the new functionality. We are taking the workflow that the customer wants to perform and persisting it to a test - hence the term 'Living Documentation' as it lives within your software application. This means if anyone decides to alter the production code in the future, the tests will fail and developer will have the ability to perform a code fix to prevent the code from regressing. 

When you are developing a large software solution, if you want to understand how a piece of functionality works. Rather than having to decipher the code and business rules. The engineer can simple read a few requirements (functional/unit tests) and gain an insight into not only how the code works, but also why it was written that way. 

Another example of behavioral driven development saving the developer time and money. 

I love BDD, hopefully this short article might persue you to investigate this development approach a little further.