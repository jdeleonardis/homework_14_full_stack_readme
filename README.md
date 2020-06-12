# Project Title
Homework 14 Full Stack Reverse Engineer
## Table of Contents:
- [Description](#Description)
- [Document](#Document)
    - [server.js](#server.js)
    - [html-routes.js](#html-routes.js)
    - [api-routes.js](#api-routes.js)
    - [signup.html](#signup.html)
    - [members.html](#members.html)
    - [login.html](#login.html)
    - [style.css](#style.css)
    - [signup.js](#signup.js)
    - [members.js](#members.js)
    - [login.js](#login.js)
    - [user.js](#user.js)  
    - [index.js](#index.js)
    - [passport.js](#passport.js)
    - [config.json](#config.json)
    - [isAuthenticated.js](#isAuthenticated.js)
- [Changes_to_the_existing_project](#Changes_to_the_existing_project)

## Description
Each file in homework 14 should be analyzed and each piece of code described and explained.  Additionally, if there are any dependencies, they should be noted and explaned as well.  Finally, when all code is explained, a separate section explaining what changes can be made to the existing code should be included.

## Document
The project is setup with the following file structure
\Develop
	server.js
	\config
		config.json
		passport.js
		\middleware
			isAuthenticated.js
	\models
		index.js
		user.js
	\public
		login.html
		members.html
		signup.html
		\js
			login.js
			members.js
			signup.js
		\stylesheets
			style.css
	\routes
		api-routes.js
		html-routes.js

This project was originally created using the sequelize CLI tool and as a result, several objects were created or heavily modified including \models\index.js, \config\config.json, as well as some code in the server.js file.

Each file, its responsibilities, and dependencies on other files and modules are broken down here:

### **server.js**
Dependencies:  
Npm modules: express, express-session  
Code modules: \config\passport, \models (for sequelize), \routes\html-routes.js, \routes\api-routes.js	

Description:  
The server.js file is responsible for initiating the server for this application.  It first sets up the express module, including allowing the ability to parse data into json objects.  It then uses express-session to keep track of a user’s session and login status, and initializes Passport.  (Passport is a module created solely to authenticate requests. More info will be included later on when the passport files are discussed).  Next, maps to the routing files for api calls as well as html rendering are included.  Finally, the app is set to listen for requests, after the sequelize database sync is completed (verifying that the database is connected and ready for processing).

### **html-routes.js**
Dependencies:  
Npm modules: node path (included with node)  
Code modules: \config\middleware\isAuthenticated.js  

Description:  
There are three HTML pages that can be visited in this app, and the html-routes.js file controls the routing to those pages.  

The default route will check to see if a user is already in the req.user variable (which gets populated from Passport when logging in).  If the user does exist (meaning the user is logged in), it sends them to the member HTML route (if authenticated, using the isAuthenticated.js which will open the member html page), or to the signup html form.

The signup html form has a link to login.  Clicking that link opens the login route (which again checks to see if the user is already authenticated, and if so, passes them to the members.html).  If not, the user goes to the login html form.

### **api-routes.js**
Dependencies:  
Code modules: \models (for accessing the database), \config\passport
    
Description:  
There are 4 api routes that can be accessed in this application:

post(“/api/login”) - this route uses the passport module, using a local strategy (passport and local strategies will be discussed in the passport.js description).  This route is called from the login.js on the login form - if the login is successful, the user is sent to the members route, otherwise, an error is supposed to be displayed.  (More info on this error is included in the **‘Changes To Existing Project’** section.)

post(“/api/signup”) - occurs when the user is on the signup html, and enters a new email and password.  The user is created in the user table with the email and password.  The user is then redirected (code 307 temporary redirection) to the post(“api/login”) above.  If an error occurs, the error is passed back to the calling js file, and an error is supposed to be displayed.  (More info on this error is included in the **‘Changes To Existing Project’** section.)

get(“/logout) - when the user presses Logout on the Members page.  The passport logout function is called (which clears the req.user property) and then the user is redirected to the default login page.

get(“/api/user_data) - is the data (displays the email address of the logged in user) that is retrieved when the user goes to the Members page.  If the user is not logged in, an empty object is passed (so the members page wont show any information).  Otherwise, the user’s email address and the id for the session is passed.

### **signup.html**
Dependencies:  
Stylesheets: bootstrap, stylesheets/style.css  
Code: jquery, js/signup.js  
    
Description:  
This html page contains a form that allows a user to enter an email address and a password to signup for the application.  It also includes a login link that directs the user to the login html if pressed.  More information about javascript processing is included in signup.js.

### **members.html**
Dependencies:  
Stylesheets: bootstrap, stylesheets/style.css  
Code: jquery, js/signup.js  
    
Description:  
This html page will contain the user’s email address if authenticated in a ‘Welcome member-name’ header.  It also includes a logout link that logs the user out of the application.  More information about javascript processing is included in members.js.

### **login.html**
Dependencies:  
Stylesheets: bootstrap, stylesheets/style.css  
Code: jquery, js/signup.js  
    
Description:  
This html page contains a form that allows a user to enter an email address and a password to log in to the application.  It also includes a signup link that directs the user to the signup html if pressed.  More information about javascript processing is included in login.js.

### **style.css**
Dependencies:  
None

Description:  
This is a very basic stylesheet that sets the top margin of the login and signup forms to 50px.

### **signup.js**
Dependencies:  
None, but code is for signup.html

Description:  
When the user presses the Sign Up button in the signup html, the email and password are added to a userData object.  If the email address or the password are blank, processing stops.  If both the email address and the password are filled in, the signUpUser function is called (in the same js) which sends an API POST request to \api\signup (described above).  If the addition of the user to the database is successful, the user is sent to the members.html.  Otherwise, an error message appears (most likely because a user’s name already exists).  (More info on this error is included in the ‘Changes To Existing Project’ section.)

### **members.js**
Dependencies:  
None, but code is for members.html

Description:  
When the members.html loads, member’s js sends a GET request to /api/user_data.  The returned value (the logged in user’s email address) is put in the member-name h2 tag.

### **login.js**
Dependencies:  
None, but code is for login.html

Description:  
See description for signup.js.  This js file processes nearly identically to signup.js, with the exception that the API POST request is sent to \api\login.  (Currently, no information appears to the user when an incorrect login attempt is made, and that is addressed in the **‘Changes To Existing Project’** section.)

### **user.js**
Dependencies:  
Npm modules: bcryptjs
    
Description:  
This is our model, with the database table named ‘User’.  User is defined with two columns.  ‘email’, which is a string, not null, must be unique, and must be an email address; and ‘password’ which is a string, not null.

Additionally, this model uses the bcryptjs module to hash (or encrypt) passwords.  One method is included in this model which compares a password passed in to the encrypted password.  In addition to that method, another method is a ‘hook’ fires before creation of a record in User that encrypts the password before saving it to the database.

### **index.js**
Dependencies:  
Npm modules: sequelize, fs, path (included with node)  
Code modules: \config\config.json  
    
Description:  
This file is created by sequelize CLI.  It serves as a way to collect all models (looping through all models in the models folder), and include them in one exported object (in this case, ‘db’)

### **passport.js**
Dependencies:  
Npm modules: passport, passport-local (strategy)  
Code modules: \models  
    
Description:  
Passport is a module that is used as authentication middleware for node.js.  In this file, a ‘local strategy’ has been chosen, which will expect a username (or email in this case) and a password.  Passport provides several hundred strategies, but the local strategy works in this case.  This local strategy allows the user to stay ‘logged in’ across any additional instances of this application that are opened on the local workstation.

When a user attempts to login, a query against the User table is made to see if the user email address exists (findOne function).  If it doesn’t exist, several values including an error message are sent in a callback function.  Additionally, if the user exists, but an invalid password is sent (using the validPassword function in user.js), the callback includes an invalid password message.  Otherwise, validate the user.

### **config.json**
Dependencies:  
None
    
Description:  
This file is created by sequelize CLI.  It houses connection information for three different databases based on the environment being executed.

### **isAuthenticated.js**
Dependencies:  
None
    
Description:  
This file exports a function.  The function checks to see if the req.user property is populated (meaning a user is logged on).  If so, the user is allowed to proceed to whatever route they were trying to get to.  Otherwise, the user is routed to the default sign up screen.

## Changes_to_the_existing_project
To be perfectly honest, while I feel like I was pretty successful in determining how this project works, I had a very difficult time trying to come up with ways to improve it.  However, several items that I did notice:

- In signup.js, when a user enters an email address that already exists and presses ‘Sign Up’, an error object is passed back from the api-routes.js post request.  Instead of correctly parsing out that error, the message ‘[object Object]’ appears in red to the user.  This can be fixed in several different ways, however changing the code in the function ‘handleLoginErr’ to be `$(“#alert .msg”).text(err.responseJSON.errors[0].message` returns a more informative message.
- In api-routes.js and login.js, when a user enters a valid email address but an invalid password OR the user enters an email address that doesnt exist, an error message is supposed to be presented.  Instead, the user is not logged in, and the form fields presented to the user are just cleared.  (A console log message is currently included, however, displaying the err object).  As with the previous error in signup.js, there are probably a few ways to fix this.  One way to do so is to include a `.catch` in the `app.post(“/api/login”)` function that will pass the error back to login.js (with code exactly the same as the `app.post(“/api/signup”)` function.  Then, in login.js, change the code in the function loginUser to correctly grab the error message, and display it to the user (again, exactly the same as signup.js).
- On both the Sign Up and Login forms, if the user does not enter an email address or a password, processing simply stops.  Perhaps an error message could be shown telling the user to enter the missing information.
- While the first two items are definite fixes, I also noticed that login.js and signup.js are nearly identical (and will be even more so when the error problem is fixed).  It may be possible to combine to two .js files, but A) that may be more trouble than it’s worth and B) may obfuscate code that is already relatively simple.
