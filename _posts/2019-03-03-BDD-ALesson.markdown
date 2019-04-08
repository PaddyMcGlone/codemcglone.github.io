---
layout: post
title:  "BDD - Behave yourself"
date:   2019-03-03 18:20:20 +0000
---
## Intro - Why waste time writing tests?

When i started working for my current company, i struggled with with concept of behaviour driven development. I came from a background of fix the problem, test the fix throughly by hand and then release. What some would say the 'old school' approach to software development.

The idea that writing tests before you wrote your actual 'production' code sounded so foreign to me that i thought it was a waste time and a distraction from the 'real software development. When i was learning this new approach to development a number of questions ran through my head:

- Why would you develop code like that?
- What benifit would it bring to my solution, other than taking longer to create something simple?
- How would you know what test to write and what to put in them..

All reasonable questions and questions many inexperienced developers ask themselves at the start of their BDD journey.
Being unable to answer these questions suitable, I created what i thought was a development practice which worked for me:

- Create the code fix first
- Browser test it
- Add some tests to keep the 'reviewer' happy.

Im not proud of this approach, but its important to be thruthful, this is how i used to develop professional software prior to BDD!

## The outcome of this approach

At the time of learning BDD, I was working on a very busy project and our development team was put under alot of pressure to deliver. When inexperienced developers are put under such pressure, they normally resort to the aforementioned approach, their comfort zone. Following this approach is easy and it allows a developer to produce results quickly (which looks good to project managers and on Kanbhan boards).

However, by following this approach what normally happens is the developer will solve problem 'A' and the test team / customer will test and confirm the issue is resolved. But during test cycles, the test engineer or customer will normally create numerous scenarios to verify your fix (problems 'B' and 'C'), these scenarieos are normally different system workflows that will traditionally occur when your system goes 'live'.

If you have developed a software solution which is just focused on problem 'A' then the chances are problem 'B' and problem 'C' are going to fail during testing and potentially jepordaise the release.

Recently, I have worked with customers that dont consider the 'full picture' until they have the software solution in front of them. When the delivered solution was infront of them it forced the customer to determine all of the requirements and problem scenarios in order for our fix to 'pass through test'.

It can be very fustrating to work with customers like this, but unfortunatley many customers still follow this approach.

The outcome of the developer not considering all of the possible problems when designing their 'fix' is the company will lose revenue due to the time spent resolving these 'unforeseen issues' and a possible lack of confidence in the developers ability to produce working solutions from the customer and the project manager.

** Remember the goal is always to develop and deliver a software solution which meets all of the customers expectations. **

## I started behaving myself

Recieving an email from the customer or project manager stating there's problems with your code fix soon becomes old, and the importance of code quality and testing coverage soon become paramount. In these scenarios I found myself turning to BDD and it soon transformed how i worked.

BDD or Behavioural Driver Development focuses on creating test scenarios which are focused on the end user and how they behave when they are using your application.

- Given
- When
- Then

In our current project we implement BDD using both functional (integration) and unit tests, my personal favourite being the unit tests devlivered using Machine Specification (MSpec) due to their simplicity and speed of execution and it is for that reason we will become our focus.

## The problem ##

A driver with a current driving licence and vocational (Lorry) entitlements should provide advanced driving info when completing a renewal.

In the scenario above, we will want to add a page to the renewal wizard which will request additional info from the driver. So before we write any production code we will write out first test:

// Test 1 - Problem A

class when_I_call_CreateWizard_with_lorry_driver 
{
    static IEnumerable<string> results;

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

By writing this test first, the developer will gain an understanding of what code they will have to write (driver needs to be lorry driver = true) before they produce any production code.
The next step is to add just enough produce code to make our test pass, in this step we add just enough code to ensure our test passes - nothing else.

// Test 2 - Problem B, C
Now that we have created just enough code to get our test to pass, the next step is to consider the other possible scenarios (problems 'B' and 'C'). This is the most important stage of BDD as it is here that the developer will consider all possible scenarios, strengthen their understanding of the problem domain and potentially raise questions to the product owner / BA which will force them to contact the customer (thereby encourgaing the customer to fully consider the problem domain as well).

Our scenario is a little simplier, we will test what happens when a non lorry driver calls the wizard

class when_I_call_CreateWizard_with_motorbike_driver 
{
    static IEnumerable<string> results;

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

When we run this test it should now pass due to the code written for test one. But what if we question does the drivers age have an impact on the page being displayed, and the BA says yes. This is where your BDD approach can become vital. In this instance we could write new tests this time stating the drivers age (above and below limit) and verify the page is either shown or hidden.


Another hidden advantage of BDD, is in the scenario above we are capturing our requirements for the new functionality. We are taking the workflow that the customer wants to perform and persisting it to a test. This means if anyone decides to alter the production code in the future, the tests will fail and developer will have the ability to perform a code fix to prevent the code from regressing.

When you are developing a large software solution, if you want to understand how a peice of funcitonality works. Rather than having to decipher the code and business rules. The engineer can simple read a few requirements (functional/unit tests) and gain an insight into not only how the code works, but also why it was written that way.

Another example of behavioural driven development saving the developer time and money.





