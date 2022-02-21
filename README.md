# SeleniumBypassADAuthentication

Hey there! I had this unique ask from one of the automation tester in our team, it was to by-pass Microsoft authentication on front-end application built using Angular.

**Back-story** : We have our selenium automation tests run locally using the real user and we do not want to use the same on the build pipeline for obvious reasons.

**Limitation** : we can't be creating a new account just for testing purpose and it was against the standard set in the organization.

So, what to do?

The idea is to create a service principal on Azure and provide the corresponding role for it.

Now the solution was separated into 2 steps.

 - Fetch an access token in pipeline and pass the token from pipeline to code.
 - Selenium code put the access token in session storage or any cookie. (Depending upon the client implementation)


## Step 1: Fetch the access token from build/release pipeline
 ![](https://github.com/Vikas1116/SeleniumBypassADAuthentication/blob/main/Images/Image1.png)
 
 The first task here executes couple of powershell commands, to fetch the access token for your resource id and pass this token to the subsequent tasks in the below pipeline.
 
 The second task is a visual studio test task to run the tests and also override the test run parameters, in our case it is the access token.
 
 ## Step 2: C# selenium code to put the access token in session storage
 
 Add a runsettings file with a parameter having default value.
 
 ![](https://github.com/Vikas1116/SeleniumBypassADAuthentication/blob/main/Images/Image2.png)
 
 Add the following code in your selenium project before launching your browser.
 
 ![](https://github.com/Vikas1116/SeleniumBypassADAuthentication/blob/main/Images/Image3.png)
 
We use javascript executors to go to the application url, get the access token from runsettings, set session storage with the current access token and navigate again to the url. 

The first time the page is redirected to Microsoft login, but on the second navigation the access token exists and the authentication is bypassed.
