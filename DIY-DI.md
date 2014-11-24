# DIY  - Dependency injection container
## Introduction
If you are a IoC guru, this article is not for you. There are planty of good articles out there. This article is all about hello world and the most over engineered hello world you have seen.
Whole point of using IoC is to enable dependency inversion pattern and role of dependency inversion is to make code testable. 
## Lets get hand dirty

1. How we are going to test our code, changing the system clock right. That gona be a awasome. Acthually that's crap.

2. lets start by seprating some of the concerns. Now Autocue will say someting instead of Clarkson.  
3. Now lets try to remove one more dependency by adding time variable instead of using Date.Time every where
4. Lets try to also get rid of another dependency datetime.now by adding clock interface IClock.
5. Lets add test project and out first test When reading the autocue on saturday.
6. Lets also add shouldly
7. We have added a new test "TheMessageShouldBeYoureWrong" which will test how our autocue will behave on saturday.
8. So to test this our clock needs to say taht right now it is satuerday without changing the system clock.
9.  

## Availabe containers
## 
