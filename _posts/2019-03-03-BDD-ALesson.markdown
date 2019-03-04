---
layout: post
title:  "BDD - Behave yourself"
date:   2019-03-03 18:20:20 +0000
---

When i started working for my current company, i struggled with with concept of behaviour driven development. The idea that writing tests before you wrote your actual 'production' code sounded so foreign to me and numerous questions ran through my head:

- Why would you do that
- What benifit would it bring
- How would you know what to write

Being unable to answer these questions I created a development practice which i felt worked for me:

- Create the code fix first
- Browser test it
- Everything working? Add some unit tests at the end to keep the 'code reviewer' happy.

Im not proud of this, but its important to be thruthful, this is how i used to develop professional software prior prior BDD.

At the time i was working on a very busy project and our development team was put under pressure to deliver. When inexperienced developers are put under such pressure, they normally resort to the aforementioned approach, their comfort zone.

By following this approach what normally happens is the developer will solve problem 'A', which will be delivered to the customer and they and the project manager will be delighted the problem was resolved swiftly. 

However, when the customer starts testing their solution they will complete the testing of scenario 'A', but they will establish there is also a scenario 'B' and a scenario 'C'. When they test these new scenarios they will fail because your code only realised scenario 'A' and the outcome is the release is either heldup in test awaiting another patch or the customer refuses to accept a story in a sprint.

The outcome for the company will be lose in revenue due to the time spent having to produce a fix for scenarios 'B' and 'C' and the developer will go bat crazy producing fixes only for the customer to develop another scenario to fail in test.

This is the problem i found myself in, i would produce code which would either be rejected in sprint testing due to its inability to meet all of the scenarios. What happens when a developer follows this approach is they become a burden on a team and ultimately cost the company money. 





