# Scanning-testing

Types of testing:
===
***Unit testing:*** Tests individual functions or methods in isolation. Smallest building blocks of an application (like bricks).
***Funtional/Component testing:*** Tests a single feature or component of the application to ensure 
                                   it works as expected, without focusing on internal code structure.
***Integration testing:*** Tests interaction between multiple components or services (eg: is catalogue reaching mongodb?)
***Regression testing:*** Ensures that existing functionality still works after new changes, bug fixes, or enhancements.
***Sanity check:*** A quick, focused check after a small change or bug fix to verify that the core functionality works.
***End-to-End:*** Tests the entire application flow from start to finish as a real user would.

* Unit         → Smallest code piece
* Functional   → Feature works
* Integration  → Components talk
* Regression   → Old stuff still works
* Sanity       → Quick confidence check
* E2E          → User jour

Test case to check functionality of a login function
===
```
login(string username, string password){
	boolean isSuccess = checkWithDB(username, password)
	if(isSuccess){
		showDashBoard()
	}
	if(failure){
		showError()
	}
}
```

Code coverage
===
- Out of 10 functions, if we write unit test cases for 9 functions, code coverage is 90%
- Valid tests -> If you pass valid parameters the test should succeed.
- Failure test -> If incorrect parameters passed, it should fail.

