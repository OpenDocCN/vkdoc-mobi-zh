![](images/979-8-8688-0944-6_CoverFigure.jpg)

ISBN 979-8-8688-0943-9e-ISBN 979-8-8688-0944-6 [https://doi.org/10.1007/979-8-8688-0944-6](https://doi.org/10.1007/979-8-8688-0944-6) © Shantanu Baruah and Shaurya Baruah 2023, 2024 This work is subject to copyright. All rights are solely and exclusively licensed by the Publisher, whether the whole or part of the material is concerned, specifically the rights of translation, reprinting, reuse of illustrations, recitation, broadcasting, reproduction on microfilms or in any other physical way, and transmission or information storage and retrieval, electronic adaptation, computer software, or by similar or dissimilar methodology now known or hereafter developed. The use of general descriptive names, registered names, trademarks, service marks, etc. in this publication does not imply, even in the absence of a specific statement, that such names are exempt from the relevant protective laws and regulations and therefore free for general use. The publisher, the authors and the editors are safe to assume that the advice and information in this book are believed to be true and accurate at the date of publication. Neither the publisher nor the authors or the editors give a warranty, expressed or implied, with respect to the material contained herein or for any errors or omissions that may have been made. The publisher remains neutral with regard to jurisdictional claims in published maps and institutional affiliations.

This Apress imprint is published by the registered company APress Media, LLC, part of Springer Nature.

The registered company address is: 1 New York Plaza, New York, NY 10004, U.S.A.

Introduction

Build cutting-edge iOS apps with the latest iCloud features! This is the second version of our earlier book *App Development Using iOS iCloud*, which offers comprehensive coverage of iOS 17’s changes, empowering you to create efficient, user-friendly applications.

Master enhanced asynchronous iCloud database operations, leverage class extensions for streamlined development, and implement robust MVC architecture principles in your code. Explore advanced image manipulation, create engaging drop-down lists, and design expanding text fields for optimal user experience. Also learn about advanced UI animations and efficiently manage multiple database records.

Also, build a real-world chat app to solidify your learning and discover the power of push notifications. Elevate your iOS development skills and stay ahead of the curve with this new edition.

New to this edition: in-depth exploration of iOS 17’s latest features, updated code examples, and best practices for modern app development.

What you’ll learn:

*   **Master iCloud database operations:** Utilize iOS 17’s enhanced query methods for efficient data management.

*   **Build robust app architecture:** Implement the MVC design pattern for modular and maintainable code.

*   **Enhance user experience:** Create visually appealing interfaces with drop-down lists, expanding text fields, and advanced animations.

*   **Optimize image handling:** Store, retrieve, and manipulate images effectively using iCloud.

*   **Leverage iOS 17’s new features:** Explore class extensions and other tools to streamline development.

*   **Write cleaner code:** Benefit from improved error handling and a well-defined closure syntax.

*   **Develop feature-rich apps:** Build chat functionality with iCloud public databases and implement push notifications.

## Who This Book Is For

This book is for iOS developers looking to master advanced app development techniques using iCloud. Whether you’re building on existing iOS knowledge or seeking to create cutting-edge applications, this book provides the essential tools and insights to take your skills to the next level.

Acknowledgments

> Writing a book on technology is quite a time-consuming task, for it is not just about writing your thoughts on paper but also writing code and validating its completeness. I would like to thank my wife Kapila Sood and my daughter Nitara Baruah for their undeterred support and patience as I put together endless hours to bring this book to life. Their feedback on the aesthetics of the app, which is an integral part of the book, and the outline of the book content is invaluable.

> I would also like to thank my mother Meenu Baruah who discovered the writing itch in me quite early in my life and has always encouraged me to pursue writing. While at times I remained uncertain, she never doubted that one day I will be a published author. She is also the first published author in our family, and I am certain I got my writing genes from her.

> I would also like to thank my father, late Sailender Nath Baruah, who introduced me to the world of software back in the day when computers were not ubiquitously available.

> I cannot thank enough my Apress team for all their help in making this book a reality: Miriam Haidara, who I pitched the second version of the book; Sowmya Thodur, in helping with all coordination; and Massimo, for reviewing my code and making sure it works as expected and for providing valuable comments in reorganizing the chapters and content.
> 
> —Shantanu Baruah

About the Authors About the Technical Reviewer

# 1. Introduction

Thank you for reading and liking our book *App Development Using iOS iCloud*. When we wrote this book in 2023, the iOS version was at 15\. Since then, iOS has released two major versions and several minor versions. This book is an update to our previous book to provide details on all the changes that are pivotal to enhance your code quality and experience. The following are the key areas we are going to cover in this book.

## Enhanced Asynchronous iCloud Database Operations

iOS 17 has introduced several new block methods (recordMatchedBlock and queryResultBlock) to improve query efficiency and effectiveness. These block methods dramatically reduce lines of code and have a well-defined closure syntax. Once the query completes, the return block has a case-based return statement default definition of conditions such as success and failure. The blocks also have enhanced its error handling capabilities.

The book will have a chapter dedicated to this. We will update all queries from the previous book with the new block-based query structures.

## Class Extension

Class extension is not new to iOS 17 but was not covered in our first version of the book. Class extension is a property tool. Based on the object-oriented principle, extensions add new functionality to an existing class.

Chapter [3](#533539_2_En_3_Chapter.xhtml) will focus on creating class-level procedures, defining properties, and declaring class-level protocols. Our example will create an image picker controller delegate and a UI navigation controller delegate as class extension, allowing users to set an image to an image object.

## Model-View-Controller Design Framework

Model-View-Controller (MVC) is a design pattern which fits well for Cocoa applications. MVC design patterns make the code modular easy to read.

We will have a chapter dedicated to MVC architecture implementation in Xcode. We will use a model class for persisting data, a controller for navigation, and a view for screen display and presentation.

## Image Manipulation

iCloud provides an option to store binary objects such as images. The book will have a chapter dedicated to image manipulation.

We will learn about uploading an image from a photo library or taking pictures from the phone camera and storing the picture binary content in iCloud repository (asset type). We will also learn about the configuration changes (info.plist) required for allowing users to access the camera and photo library. We will also learn about image manipulation in this chapter.

## Drop-Down List

User interface design is a critical element of iPhone app design. As the iPhone form factor is limited, developers need to use the available real estate wisely and smartly. This chapter will focus on creating a drop-down list with prepopulated data using table views. It will allow users to select a value from the drop-down and display the selected value on some UI object (button, label, or a text field UI object).

## Create an Expanding Text Field UI Object

This chapter will explore concepts on how to expand the height of a textbox based on the text length to show the entire text content to the user. As the text overflows, it will allow text to wrap text by word or characters. Also, it will make the textbox appear over the keyboard.

## Animated View

In the first version of the book, we learned about UI object animation. We will have a chapter dedicated to more UI object animation.

## Saving Records to Multiple Tables

iOS 17 allows saving of multiple records across private and public databases. In this section, we will learn to use CKModifyRecordsOperation and modifyRecordsResultBlock to achieve the same.

## Push Notifications

Sending push notifications is an important feature of iOS development. It is the way an app can communicate with the user. We have a section in this book where you will learn about push notifications, subscribe to an alert, and display reminders on a particular date and time. Before sending any notification, the app must seek permission to allow push notifications; this section will provide details on achieving the same.

## Conversation Between Users

To explain the abovementioned features, we will create a chat app. The chat app will allow users to communicate with each other. We will use the iCloud public database for storing all chat conversational data.

# 2. Block Methods

Your iPhone app powered by iCloud database would need to query the database primarily to display results on your application screen. The iCloud database has a CKQueryOperation object, which can be used to execute queries. To execute a query, you would need the following:

*   Create a CKQuery object with the required search criteria. You can also set a data sort option while setting the query and the zone from which the table exists. Please note that CloudKit restricts query to a particular record zone. Each CKQueryOperation is limited to a single zone.

*   Create a CKQueryOperation object initialized with the CKQuery object defined before.

*   Optionally, we can also configure parameters such as resultsLimit (this will limit the number of records to be fetched upon firing the query) and desiredKeys (you can specify the fields of your query table that you want the query to return).

*   Finally, we can pass the CKQueryOperation object to the target database (public, private, or shared) to execute the operation.

When a completion block handler is assigned to the query, the CKQueryOperation calls the handler after the query is executed and returns any results. The handler can be used for handling errors, failures, and results.

In iOS 15, we used recordFetchedBlock and queryCompletionBlock. While recordFetchedBlock is deprecated, queryCompletionBlock is still available. However, in this chapter, we will focus on the completion block handler called recordMatchedBlock and queryResultBlock. Find below a CKQuery example which uses recordFetchedBlock and queryCompletionBlock.

## Example Code Description

In this example code, we are querying the private cloud database of the current user to retrieve all records from the iCloud table ***chattables***. Upon completion of the code, a class object ***Chattable*** (which depicts the iCloud table ***chattables***) is populated and returned to the calling function. Let us understand the code.

### Function Definition

```
func gettableNames(completion:@escaping ([Chattable], Error?) -> Void){
```

The function name is ***getTableNames*** which has a special keyword ***completion@escaping*** as one of its parameters. This keyword means that when the function finishes executing, the closure is called, which in our example is passing an object of class object ***Chattable***. You will see later how and when we will call ***completion***.

The ***@escaping*** is an important tag when the function is an async operation. Async operations may take a while to get triggered. It also makes the Swift compiler aware that we are leaving the function in a logical manner, whenever the async function completes. In our example below, we are calling upon the iCloud query function, which is asynchronous, and thus, the ***completion@escaping*** is apt to meet our function objectives.

### Return Value Description

```
var chattables:[Chattable] = []
```

Declaring an object array of ***chattables*** of class type ***Chattable***. This variable will hold all the returned table names after the query is executed and will be returned through ***completion@escaping*** to the calling function.

### Query Definition

```
let pred = NSPredicate(format: "dummy == %@", "1")
```

Defining the query predicate using ***NSPredicate*** and stored in variable ***pred***. The condition is set to return all records where the table field ***dummy*** is set to value 1.

```
let query = CKQuery(recordType: "chattables", predicate: pred)
```

An object with the name ***query*** is defined above of type ***CKQuery***. This is where the query is defined for iCloud object type ***chattables*** and the condition for query retrieval is set through the ***pred*** variable defined before. ***CKQuery*** has two inputs: the record type and the predicate.

```
let operation = CKQueryOperation(query: query)
```

Next, we are defining the CKQueryOperation and assigning the query object to it.

```
let operationConfiguration = CKOperation.Configuration()
```

Defining a CKOperation.Configuration is optional. We are defining for set timeout intervals, so that the query can exit if not responding after a particular set time.

```
operationConfiguration.timeoutIntervalForRequest = 10
```

This property determines the request timeout interval (in seconds) for the query to complete. In our example, the task will wait for 10 sec for additional data to arrive before it gives up.

```
operation.configuration = operationConfiguration
```

The operation configuration object we created is now assigned to the ***operation*** object we have defined before.

```
CKContainer.default().privateCloudDatabase.add(operation)
```

The operation is now assigned to the user private database for execution.

### Query Matched Block

```
operation.recordMatchedBlock = { recordID, result in }
```

iCloud database calls are asynchronous. After the query is executed, the results are returned asynchronously. ***recordMatchedBlock*** is the block that is executed once the operation returns a value. The ***recordMatchedBlock*** has two parameters as described below:

*   ***recordID*** of type CKRecord.ID

*   ***result***, which is an Enum of type Result<CKRecord, Error>

The block iterates through all the return record.

```
switch result {
```

We need to now unwrap the ***result*** Enum, which has two data elements – ***success*** and ***failure*** – for each record. The above statement will help us switch between the two data elements.

```
case .success(let record):
let chattable = Chattable(chattablename: record["chattablename"] as? String, receipentprofile: record["receipentprofile"] as? CKAsset, receipentname: record["receipentname"] as? String, lastchat: record["lastchat"] as? String)
chattable.record = record
chattables.append(chattable)
```

This is the first case – ***success*** – which means there is a record returned. We will capture the returned result in the variable ***record***. Subsequently, we will extract the required fields of the record, store in scope-level local variables of type ***Chattable*** class, and after that, append the variable to the function-level ***Chattable*** array object. As this is iterative in nature, all records will be appended to our function-level variable ***chattables***.

```
case .failure(let error):
print(error.localizedDescription)
}
```

This is the other case – ***failure***. In case the record couldn’t be retrieved, we will print the localized description of the ***error*** object on the console.

### Query Result Block

```
operation.queryResultBlock = { result in
```

Once the ***recordMatchedBlock*** iterates through all records, the next block is called the ***queryResultBlock***. It has one parameter result, which is an Enum of type ***Result<CKQueryOperation.Cursor?, Error>***.

The ***CKQueryOperation.Cursor*** has the records and error object in case there is an error.

```
DispatchQueue.main.async {
```

We have defined a class-level ***DispatchQueue*** object which will wait at the main thread for the async job to finish. Once done, the body of the ***DispatchQueue*** will be executed.

```
switch result {
```

We are expanding the ***result*** Enum on its two parameters ***failure*** and ***success***.

```
case .failure(let error):
debugPrint(error)
print(error)
completion(chattables, error)
```

If there is failure, this block of code is executed, where we are printing the error on screen. Notice we are now calling the completion handler with the chattbles array object and the error reported for the calling function to take appropriate action.

```
case .success(_):
completion(chattables, nil)
}
```

This is the success block. If there is a success, the completion handler is called with the chattables object and error is marked as nil.

## Complete Code

```
func gettableNames(completion:@escaping ([Chattable], Error?) -> Void){
var chattables:[Chattable] = []
let pred = NSPredicate(format: "dummy == %@", "1")
let query = CKQuery(recordType: "chattables", predicate: pred)
let operation = CKQueryOperation(query: query)
let operationConfiguration = CKOperation.Configuration()
operationConfiguration.timeoutIntervalForRequest = 10
operation.configuration = operationConfiguration
CKContainer.default().privateCloudDatabase.add(operation)
operation.recordMatchedBlock = { recordID, result in
switch result {
case .success(let record):
let chattable = Chattable(chattablename: record["chattablename"] as? String, receipentprofile: record["receipentprofile"] as? CKAsset, receipentname: record["receipentname"] as? String, lastchat: record["lastchat"] as? String)
chattable.record = record
chattables.append(chattable)
case .failure(let error):
print(error.localizedDescription)
}
}
operation.queryResultBlock = { result in
DispatchQueue.main.async {
switch result {
case .failure(let error):
debugPrint(error)
print(error)
completion(chattables, error)
case .success(_):
completion(chattables, nil)
}
}
}
}
```

# 3. Class Extensions

Extensions provide a powerful way to add new functionalities to an existing class, structure, enumeration, or a protocol. Once added, the function is available as a natural extension to the added class or the object. Extension can be added to user-defined objects or to a system-defined class. In this chapter, we will learn how to add extension to a system-defined class, and in Chapter [5](#533539_2_En_5_Chapter.xhtml) dedicated to image manipulation, we will learn how to add extension to user-defined view controller.

Once the extension is defined, we can do everything as one can expect to in defining a function. You can, for example,

*   Add delegates

*   Define methods, structures, and types

*   Define initializers

*   Make or confirm to a protocol

## Example Code Description

In the example below, we will extend the system-defined class String to add a new string manipulation function which currently doesn’t exist. We will add the following three functions:

*   Get the last index of a string

*   Check if a user-provided string input confirms to be a valid email address

*   Check if a user-provided string input confirms to be a valid URL

In our example, we will add the extension on top of a view controller class. Please note that the extension is not inside the view controller class definition, and once defined, it is visible across the project scope.

### String Extension

```
extension String {
}
```

For defining an extension, we need to use the system-defined word extension (note that it is case sensitive). After the word extension, we need to provide the name of the extension. In our example, we are extending the system-defined ***String*** class; hence, we have the same system-defined class name.

#### Method: Find Last Index

```
func lastIndex(of string: String) -> Int? {
}
```

The name of the function is ***lastIndex***. It will take in a ***String*** literal and return the last index number of the string, which is an integer. Note that ***lastIndex*** will be available across the project as an added function to the ***String*** function.

```
guard let index = range(of: string, options: .backwards) else { return nil }
```

In this line, we define a variable ***index***. Note that the variable is defined as **let** as we will not be reusing it within the scope of the function. Also, we are using guard to ensure if the user-provided string has issues it won’t result in throwing system error.

The ***range*** system-defined functions look for the user-provided string backwards and if found return a valid range to the ***index*** variable; if not found or if it encounters an error, it will return nil (the else part of the function is executed).

```
return self.distance(from: self.startIndex, to: index.lowerBound)
```

This line of code calculates the distance between the beginning of the string and the found substring’s starting position and returns its position.

The word self refers to the string in the current context (which is the string passed to the function), and distance is a system-defined method of the String class in Swift, which take two parameters as defined below:

*   ***self.startIndex*** refers to the first character in the string.

*   ***index.lowerBound*** refers to the index of the first character of the substring returned by the pervious range function.

Here is the complete definition of the method:

```
func lastIndex(of string: String) -> Int? {
guard let index = range(of: string, options: .backwards) else { return nil }
return self.distance(from: self.startIndex, to: index.lowerBound)
}
```

#### Method: Find If the Provided String Is a Valid Email

```
func isValidEmail() -> Bool {
}
```

The name of the function is ***isValidEmail***, which doesn’t take in any argument and returns a Boolean value. (If the string is a valid email, it will return true or else false.) Note that ***isValidEmail*** will be available across the project as an added function to the ***String*** function. Computed properties are like methods but behave like properties and are automatically called when we access them on any string literal.

```
let regex = try! NSRegularExpression(pattern: "^[a-zA-Z0-9.!#$%&'*+/=?^_`{|}~-]+@[a-zA-Z0-9](?:[a-zA-Z0-9-]{0,61}[a-zA-Z0-9])?(?:\\.[a-zA-Z0-9](?:[a-zA-Z0-9-]{0,61}[a-zA-Z0-9])?)*$", options: .caseInsensitive)
```

This line defines a constant named ***regex*** and attempts to create a regular expression object using NSRegularExpression. The pattern defined is the core of the validation and checks the email address format.

The symbol “***^***” defines the beginning of the string and allows one or more occurrences of letters (case sensitive), numbers, and a few special characters.

The symbol “***@***” separates the username from the domain name and allows one or more occurrences of letters (case sensitive) and numbers.

The symbol “***?***” is the noncapturing group, which allows for an optional subdomain name. It matches zero to 61 occurrences of letters, numbers, and hyphens, followed by a letter or number. This part can repeat zero or more times using the “***?***” quantifier.

The next noncapturing group allows for zero or more domain name separators "***.***" followed by a subdomain name.

“***$***” matches the end of the string.

.***caseInsensitive***: This option makes the regular expression case insensitive, meaning it won't differentiate between uppercase and lowercase letters.

```
return regex.firstMatch(in: self, options: [], range: NSRange(location: 0, length: count)) != nil
```

This line returns whether the email address is valid.

***regex.firstMatch(in: self, options: [], range: NSRange(location: 0, length: count))***: This part uses the regex object (defined above) to search for the first occurrence of the email address pattern (***firstMatch***) within the current string object (***self***). It specifies the entire string to be searched ***(NSRange(location: 0, length: count)) and uses an empty options array ([])***.

***!= nil***: This checks if the ***firstMatch*** function returned a value. A non-nil value indicates that the pattern was found in the string, which indicates that the email format seems valid according to the defined regular expression.

Here is the complete definition of the method:

```
func isValidEmail() -> Bool {
let regex = try! NSRegularExpression(pattern: "^[a-zA-Z0-9.!#$%&'*+/=?^_`{|}~-]+@[a-zA-Z0-9](?:[a-zA-Z0-9-]{0,61}[a-zA-Z0-9])?(?:\\.[a-zA-Z0-9](?:[a-zA-Z0-9-]{0,61}[a-zA-Z0-9])?)*$", options: .caseInsensitive)
return regex.firstMatch(in: self, options: [], range: NSRange(location: 0, length: count)) != nil
}
```

#### Method: Find If the Provided String Is a Valid URL

```
var isValidURL: Bool {
}
```

The name of the function is ***isValidURL***, which doesn’t take in any argument and returns a Boolean value. (If the string is a valid URL, it will return true or else false.) Note that ***isValidURL*** will be available across the project as an added function to the ***String*** function. Computed properties are like methods but behave like properties and are automatically called when we access them on any string literal.

```
let detector = try! NSDataDetector(types: NSTextCheckingResult.CheckingType.link.rawValue)
```

This line creates a constant named detector of type ***NSDataDetector***. This detector object is used to identify specific data types within a text string. In our code, it is configured to detect links (types: ***NSTextCheckingResult.CheckingType.link.rawValue***). The try! unwraps an optional value and forces the creation of the detector, assuming no errors occur during initialization.

```
if let match = detector.firstMatch(in: self, options: [], range: NSRange(location: 0, length: self.utf16.count)) {
return match.range.length == self.utf16.count
} else {
return false
}
```

This conditional block checks if a URL is found in the string.

***detector.firstMatch*** searches for the first occurrence of a link within the current string object. The ***NSRange*** in our example specifies that the entire range must be searched (starts from zero and goes until the last character, count on the self-string gives the length of the string).

By comparing the entire length of the string, the code ensures that the entire string represents a valid URL, not just a part of it.

The entire code is listed below:

```
extension String {
func lastIndex(of string: String) -> Int? {
guard let index = range(of: string, options: .backwards) else { return nil }
return self.distance(from: self.startIndex, to: index.lowerBound)
}
func isValidEmail() -> Bool {
let regex = try! NSRegularExpression(pattern: "^[a-zA-Z0-9.!#$%&'*+/=?^_`{|}~-]+@[a-zA-Z0-9](?:[a-zA-Z0-9-]{0,61}[a-zA-Z0-9])?(?:\\.[a-zA-Z0-9](?:[a-zA-Z0-9-]{0,61}[a-zA-Z0-9])?)*$", options: .caseInsensitive)
return regex.firstMatch(in: self, options: [], range: NSRange(location: 0, length: count)) != nil
}
var isValidURL: Bool {
let detector = try! NSDataDetector(types: NSTextCheckingResult.CheckingType.link.rawValue)
if let match = detector.firstMatch(in: self, options: [], range: NSRange(location: 0, length: self.utf16.count)) {
return match.range.length == self.utf16.count
} else {
return false
}
}
}
```

# 4. Model-View-Controller Design Framework

Model-View-Controller (MVC) architecture is one of the most popular design patterns used in software application development. Based on what function that code represents, MVC segregates the code into manageable components, thereby helping better readability, usability, and maintainability of written code. MVC design pattern assigns objects in the application of the following three roles:

**Model**: Model objects are data that the application needs. This class would have all definitions of data objects and methods to manage data, including persisting them to a database. The data required is encapsulated in a model object and can have a one-to-many relationship with other model objects. From a typical design perspective, anything that needs to be persisted in a database should be in model objects.

**View**: A view object has the screen and user interaction part of the application. The screen rendering and drawing function resides in view objects. A view object can utilize the data object to display or capture data for your application.

**Controller**: A controller connects different view objects so that the application can interact and perform the desired function. It interprets user actions made in view objects and communicates data changes (new/updated/deleted) to the model objects.

## Example Code

We will explain this concept with an example as described below.

In our application, the system will check if the user already exists, and if it doesn’t, then the user will be considered a first-time user and they will be prompted to create a new login profile. In case if it is an existing user, then the user will be redirected to the user home screen. To achieve this function, do the following:

**Model**

*   Create a Model UIViewController class, and find the iCloud user ID details.

*   In the **Model** UIViewController class, we will call the iCloud database to check if the user ID exists as a record in the profile database. If the record exists, a true Boolean value is returned or a false Boolean value is returned to the calling function.

**View**

*   Create a **View** UIViewController class to display the new profile creation screen. We will show a simple header label to show the concept.

*   Create a **View** UIViewController class to display the user home screen.

**Controller**

*   Create a **Controller** UIViewController class and get the iCloud user ID by calling a respective model method defined in a user-defined method.

*   In the **Controller** UIViewController class, this class will have a method that will call a function to check the iCloud database for an existing user ID (the definition of the function will be in our model UIViewController as explained in the next bullet point).

*   In the **Controller** UIViewController class, we will check the return Boolean value of the user's existence, and if false, call a function to display a new profile screen. If the return value is true, it will call a different function to display the user's home screen.

Let us now look at the code to achieve the same.

### Model Definition

```
import UIKit
import CloudKit
class Contact: NSObject {
```

The model class is importing two frameworks, **UIKit** for all user UI definition, which is the default framework for UI view controllers, and the **Cloudkit**, as we are using iCloud to persist data. The name of our model class is **Contact**, which is of type **NSObject**, a foundation object for developing **iOS** applications.

**UIKit** has the following important components:

*   User interface components, such as text field, labels, and buttons

*   Event handling, to handle user interactions on the user objects

*   Drawing and animations

*   Application lifecycle management, from launch to termination of the application

```
let group = DispatchGroup()
let syncQueue = DispatchQueue(label: "com.domain.app.sections")
var profileExists = false
```

We are defining a constant called **group** which stores an instantiated object of a synchronization object called **DispatchGroup**.

Next, we are instantiating an object of type **DispatchQueue** and storing it in a constant called **syncQueue**. The name of the queue is **com.domain.app.sections**. This will help us to serial process many asynchronous calls one after the other.

**profileExists** is a variable which is set to false initially. The value will be updated to true if the profile of the user exists in our persistent iCloud storage.

```
class Profile:NSObject{
var record:CKRecord!
var userid:String!
var personalprofile:CKAsset!
var name: String!
var personalphone:String!
init(userid:String, personalprofile:CKAsset, name:String,  personalphone: String) {
self.userid = userid
self.personalprofile = personalprofile
self.name = name
self.personalphone = personalphone
}
}
```

We are now defining a nested class called **Profile** instead of our **Contact** class. This class mimics the custom table **profile**, persisted in our iCloud storage. The **Profile** class has the following elements:

*   **record**: This is of type **CKRecord** defined in iCloud tool kit. When instantiated, it will store the record reference ID.

*   **userid**: A string value that is the unique ID of the user.

*   **personalprofile**: A **CKAsset** (defined in the iCloud tool kit) to store user profile image.

*   **name**: A string value to store the username.

*   **personalphone**: A string value to represent user personal phone number.

*   The **init** will allow the calling class to instantiate with value that will be stored in the object of the class for future reference.

```
var profile:Profile?
```

We are now defining a class-level variable profile of our class Profile where we will store the user profile if it exists.

```
func getICloudUserID(completion: @escaping (String?, Error?) -> Void) {
```

**getICloudUserID** is a custom method in our Contact class that will help fetch the iCloud ID of the application user. Note that every Apple user has a unique user ID. This method will fetch that user ID for unique reference.

The **completion** handler helps the method to return two values upon completion to the calling method, a string literal representing the user ID and error if any. In case there is no error, it will return nil.

The **@escaping** keyword indicates that the closure might be called after the function returns. This is necessary because retrieving the user ID might take some time (asynchronous).

**-> Void**: This specifies that the closure doesn't return any value.

```
let container = CKContainer.default()
```

A **constant** name container is defined, which stores the reference to the default iCloud container.

```
container.fetchUserRecordID { (recordID, error) in
```

We are now executing the system-defined method **fetchUserRecordID**, which exists in the **CKContainer**.**default()** method. This method will fetch the details of the Apple user ID if it exists; otherwise, it will throw an error.

```
if let error = error {
```

In this statement, we are checking if the methods are successfully executed. If not, an error will be returned, which will be stored in the **error** constant.

```
completion(nil, error)
```

This statement will be executed if there is an error. Note that we are calling the completion handler. We are making the first parameter nil (which is the user ID) and returning the error as the second parameter. This will tell the calling function an error exists.

```
} else if let recordID = recordID {
```

If the method was successful in retrieving the user ID, it will be stored in a constant **recordID**.

```
let userID = recordID.recordName
```

We are retrieving the name of the user from the constant **recordID**, which has a property called **recordName** that stores the user ID, and storing it in a constant **userID**.

```
completion(userID, nil)
}
}
}
```

Note that we are again calling the completion handler. In this type, we are passing back the user ID and making the error as nil.

```
func checkProfileExist(userID:String) {
```

Now, we are defining our next function which will check if a profile record exists in the **profile** iCloud database. Note that the function takes in the user ID (retrieved from the previous function, when successfully executed), which will be used as a unique identifier to query the **profile** database.

```
let pred = NSPredicate(format: "userid == %@", userID)
let query = CKQuery(recordType: "profile", predicate: pred)
let operation = CKQueryOperation(query: query)
```

The above three statements are preparing the iCloud query operation.

*   The first statement defines a constant called **pred**. This will store the condition for our query. In our example, we are creating a **NSPredicate** where the field **userid** is equal to the passed **userID** to the method.

*   Next, we are defining the query and storing it in a constant named **query**. Note that the query is executed with the condition passed as a variable (**pred**) and it is executed against the database **profile**.

*   Finally, we are creating the operation object which takes the query constant as a single parameter.

```
let operationConfiguration = CKOperation.Configuration()
operationConfiguration.timeoutIntervalForRequest = 10
operationConfiguration.timeoutIntervalForResource = 10
operation.configuration = operationConfiguration
```

Before we execute the query, we will now set up some configuration elements. Although these configuration elements are not important for our example, they are key elements when you have a large result set returned:

*   Define an operation configuration constant with name **operationConfiguration** which stores an object of **CKOperation.Configuration()**.

*   Next, we are setting the timeout interval for request to 10 sec.

*   We are also setting the timeout interval for resource (for **CKAsset** objects) to 10 sec.

*   Finally, we add the configuration object to the **operation configuration** property.

```
CKContainer.default().publicCloudDatabase.add(operation)
```

In this statement, we are adding the operation to the public cloud database (which is where the profile object exists) and execute the query.

```
operation.recordMatchedBlock = { recordID, result in
```

This segment of code is executed when the query is completed (note that our query is asynchronous).

**recordMatchedBlock** is a newly introduced (iOS 15) closure in CloudKit, which is particularly designed to handle asynchronous calls. The **recordID** is the uniquely identified record being processed; it is of type CKRecord.ID.

Result variable represents a Swift structure defined as **Result<CKRecord, Error>**, which encapsulates the outcome of a fetching record. It can either be a **success** containing the fetched record as **CKRecord** object or a **failure** containing the encountered **error**.

```
switch result {
```

We are now using **switch** statements to decide how to execute each case of the result structure defined above.

```
case .success(let record):
```

If there is a record available, success will be called, and it has the current record in the **record** object.

```
self.profileExists = true
```

We are setting the class-level variable **profileExists** to true as we have found that the user profile exists.

```
self.profile = Profile(userid: record["userid"] as! String, personalprofile: record["personalprofile"] as! CKAsset, name: record["name"] as! String, personalphone: record["personalphone"] as! String)
```

We are also retrieving the individual field value from the record object and storing it in our profile object. It is important to note how we are casting the retrieve fields to the respective type.

```
self.profile!.record = record
```

Finally, we are storing the complete record as well in the **record.ID** type for future reference of other properties if needed.

```
case .failure(let error):
```

This part of the switch block is for failure. In case of failure, an **error** object is returned.

```
print(error.localizedDescription)
```

We are printing the error-localized description on the console.

```
self.profileExists = false
}
}
```

And setting the class variable **profileExists** to false as there is no record it could retrieve.

```
operation.queryResultBlock = { result in
```

The operation object has a new method called **queryResultBlock**. This closure is defined in iOS 15, which is well suited for synchronous calls. This block is called after the **recordMatchedBlock** where we can make some final adjustments before leaving the method. The query result block has the same result structure as the record-matched block.

```
DispatchQueue.main.async {
```

The function checkProfileExist is called on a group dispatch main queue thread. We are going to exit the function now, informing the calling function that the thread is over and it can resume the rest of the code processing.

```
switch result {
```

We are using a switch to check success and failure conditions.

```
case .failure(let error):
```

This block is executed if there is a failure. It has an error object as a parameter containing the error definitions.

```
debugPrint(error)
```

We are printing the error on the debug console.

```
self.profileExists = false
```

As there is an error setting the class variable profileExists to false.

```
self.syncQueue.async {
self.group.leave()
}
```

And leaving the thread to return to the main queue thread to resume function.

```
case .success(_):
```

This block is executed if the user profile record exist.

```
self.syncQueue.async {
self.group.leave()
}
}
}
}
}
}
```

We are leaving the function to return to the main thread to resume from asynchronous wait.

Model View complete code is listed below:

```
import UIKit
import CloudKit
class Contact: NSObject {
let group = DispatchGroup()
let syncQueue = DispatchQueue(label: "com.domain.app.sections")
var profileExists = false
class Profile:NSObject{
var record:CKRecord!
var userid:String!
var personalprofile:CKAsset!
var name: String!
var personalphone:String!
init(userid:String, personalprofile:CKAsset, name:String,  personalphone: String) {
self.userid = userid
self.personalprofile = personalprofile
self.name = name
self.personalphone = personalphone
}
}
var profile:Profile?
func getICloudUserID(completion: @escaping (String?, Error?) -> Void) {
let container = CKContainer.default()
container.fetchUserRecordID { (recordID, error) in
if let error = error {
completion(nil, error)
} else if let recordID = recordID {
let userID = recordID.recordName
completion(userID, nil)
}
}
}
func checkProfileExist(userID:String) {
let pred = NSPredicate(format: "userid == %@", userID)
let query = CKQuery(recordType: "profile", predicate: pred)
let operation = CKQueryOperation(query: query)
let operationConfiguration = CKOperation.Configuration()
operationConfiguration.timeoutIntervalForRequest = 10
operationConfiguration.timeoutIntervalForResource = 10
operation.configuration = operationConfiguration
CKContainer.default().publicCloudDatabase.add(operation)
operation.recordMatchedBlock = { recordID, result in
switch result {
case .success(let record):
self.profileExists = true
self.profile = Profile(userid: record["userid"] as! String, personalprofile: record["personalprofile"] as! CKAsset, name: record["name"] as! String, personalphone: record["personalphone"] as! String)
self.profile!.record = record
case .failure(let error):
print(error.localizedDescription)
self.profileExists = false
}
}
operation.queryResultBlock = { result in
DispatchQueue.main.async {
switch result {
case .failure(let error):
debugPrint(error)
self.profileExists = false
self.syncQueue.async {
self.group.leave()
}
case .success(_):
self.syncQueue.async {
self.group.leave()
}
}
}
}
}
}
}
```

### View Definition

```
class ProfileViewController: UIViewController{
```

This is the view part of our MVC architecture design. We have two views to display. In this example, we are showing the display of Profile View Controller. This will be called by the controller when the user profile doesn’t exist. This view will help capture the user profile. We will not be defining a full profile capture screen, as the intent is to learn the MVC concept.

We are defining our **ProfileViewController** as a **UIViewController** class.

```
private let headerLabel:UILabel = {
let label = UILabel()
label.text = "Profile Page"
label.translatesAutoresizingMaskIntoConstraints = false
label.textColor = UIColor(displayP3Red: 128/255, green: 0/255, blue: 128/255, alpha: 1)
label.textAlignment = .center
label.font = UIFont.boldSystemFont(ofSize: 35)
return label
}()
```

For display purposes, we are defining a header label to be displayed as a UI label. We are setting some value to the label and providing some properties such as color, text alignment, and font. Note that the property **translatesAutoresizingMaskIntoConstraints**. This is required to be set to false if you want to draw the object using code.

```
func drawProfileCreationScreen(){
```

The **drawProfileCreationScreen** is a custom function to draw the label on the screen.

```
let screensize: CGRect = UIScreen.main.bounds
let screenWidth = screensize.width
```

Defining two constants that will capture the iPhone screen height and width. Both constants are important to note as we draw the label on the screen.

```
view.addSubview(headerLabel)
let constraints = [
headerLabel.topAnchor.constraint(equalTo: view.topAnchor, constant: 30),
headerLabel.widthAnchor.constraint(equalToConstant: screenWidth),
headerLabel.heightAnchor.constraint(equalToConstant: 40)
]
NSLayoutConstraint.activate(constraints)
}
```

In this code segment, we are adding the **headerlabel** object defined before to the current view as a subview. Please note how the constraints are defined so that I can position the label 30 pixels from the top, occupying the entire screen width, and with a height of 40 pixels. We need to activate the constraints to make the header visible on the screen.

```
override func viewDidLoad() {
super.viewDidLoad()
drawProfileCreationScreen()
}
```

This is the lifecycle part of the UI view controller. **viewDidLoad**() is called when the View is instantiated by the controller class. Beside calling the super method of the view, we are calling our function **drawProfileCreationScreen**() to draw the header on the screen.

Profile View complete code is listed below:

```
class ProfileViewController: UIViewController{
private let headerLabel:UILabel = {
let label = UILabel()
label.text = "InTribe"
label.translatesAutoresizingMaskIntoConstraints = false
label.textColor = UIColor(displayP3Red: 128/255, green: 0/255, blue: 128/255, alpha: 1)
label.textAlignment = .center
label.font = UIFont.boldSystemFont(ofSize: 35)
return label
}()
func drawProfileCreationScreen(){
let screensize: CGRect = UIScreen.main.bounds
let screenWidth = screensize.width
view.addSubview(headerLabel)
let constraints = [
headerLabel.topAnchor.constraint(equalTo: view.topAnchor, constant: 30),
headerLabel.widthAnchor.constraint(equalToConstant: screenWidth),
headerLabel.heightAnchor.constraint(equalToConstant: 40)
]
NSLayoutConstraint.activate(constraints)
}
override func viewDidLoad() {
super.viewDidLoad()
drawProfileCreationScreen ()
}
}
```

Please note I am not showing the code of the home screen. The intent was to show the MVC architecture layout.

### Controller Definition

```
class MainViewController: UIViewController{
```

We are creating a Controller class with the name **MainViewController**. Note that our class is of type **UIViewController**.

```
var userid:String = ""
```

Define a class-level variable which will store the user ID if the user exists in the database.

```
func checkProfileExist(){
```

**checkProfileExist** is our custom function that will check if the user exists in our persistent database or not.

```
var contact = Contact()
```

We are now defining a variable with the name **contact** of our custom model class **Contact**. Note that Contact doesn’t have any initializing parameters. This model class will have the actual code to check the existence of the user profile in our iCloud database.

```
contact.getICloudUserID { (userID, error) in
```

We are now calling our custom method **getICloudUserID** which has a completion handler returning the ID of the user and error if any.

```
if let error = error {
print("Error: \(error)")
}
```

If the method returns an error, it will be assigned to constant **error** and will be printed on the screen.

```
else if let userID = userID {
```

Else if the user ID exists, the same will be assigned to constant **userID**.

```
self.userid = userID
```

Now, we are inside the else if statement. This statement will be executed only if the user ID exists. We are assigning the **userID** constant to the class-level variable **userID** defined before.

```
self.contact.group.enter()
```

From the **contact** model class, we instantiate a constant named **group**. This is a synchronization object from Apple's Dispatch framework. It helps coordinate multiple asynchronous tasks and ensures they all finish before proceeding further.

```
self.contact.checkProfileExist(userID: userID)
```

The method **checkProfileExist** is called which exists in the contact variable model object. It takes the ID of the user as input.

```
self.contact.group.notify(queue: .main) { [self] in
```

The **checkProfileExist** method in the **Contact** model class is an asynchronous method. When it completes, it will notify the main thread in the **group** object about its completion. This is important to note, as in an asynchronous method we need to wait for the method to complete its operation.

```
if(contact.profileExists){
```

Now that the **checkProfileExist** method is complete, we are checking if a Boolean variable **profileExists** is true (note that the value for **profileExists** is set to true or false in the contact class based on user ID existence).

```
popUpHome()
```

If it exits, we will call the method popUpProfile() to instantiate the home screen.

```
}else{
popUpProfile()
}
}
}
}
```

Or else it will pop up the Profile Creation View controller.

```
func popUpHome(){
let viewController = HomeViewController()
viewController.profile = contact.profile
viewController.modalPresentationStyle = .overCurrentContext
viewController.modalTransitionStyle = .crossDissolve
self.present(viewController, animated: true, completion: nil)
}
```

This is the **popUpHome** function in our controller. When this function is called, it will

*   Create a new instance of a custom **HomeViewController** class and store the reference in a constant **viewController**

*   Pass the profile object to the **HomeViewController** object

*   Set the presentation style of the **HomeViewController** to be presented on top of the current view controller

*   Set the animation to create a smooth dissolve

*   Present the new view controller

```
func popUpProfile(){
let viewController = ProfileViewController()
viewController.modalPresentationStyle = .overCurrentContext
viewController.modalTransitionStyle = .crossDissolve
self.present(viewController, animated: true, completion: nil)
}
```

This is the **popUpProfile** function in our controller. When this function is called, it will

*   Create a new instance of a custom **ProfileViewController** class and store the reference in a constant **viewController**

*   Set the presentation style of the **ProfileViewController** to be presented on top of the current view controller

*   Set the animation to create a smooth dissolve

*   Present the new view controller

Below is the Controller View Controller complete code:

```
class MainViewController: UIViewController{
var userid:String = ""
func checkProfileExist(){
contact.getICloudUserID { (userID, error) in
if let error = error {
print("Error: \(error)")
} else if let userID = userID {
self.userid = userID
self.contact.group.enter()
self.contact.checkProfileExist(userID: userID)
self.contact.group.notify(queue: .main) { [self] in
if(contact.profileExists){
popUpHome()
}else{
popUpProfile()
}
}
}
}
}
func popUpHome(){
let viewController = HomeViewController()
viewController.profile = contact.profile
viewController.modalPresentationStyle = .overCurrentContext
viewController.modalTransitionStyle = .crossDissolve
self.present(viewController, animated: true, completion: nil)
}
func popUpProfile(){
let viewController = ProfileViewController()
viewController.modalPresentationStyle = .overCurrentContext
viewController.modalTransitionStyle = .crossDissolve
self.present(viewController, animated: true, completion: nil)
}
}
```

# 5. Image Management

Xcode projects often contain images to create a great visual experience for your end consumers. We have learned about basic image creation and management in the first version of the book.

In this chapter, we will learn the additional new concepts:

*   Define an UIImageView to display images

*   Add gesture to the image view to allow certain actions to be performed when the user touches the image

*   Display the image view with user-defined constraints

*   Define image picker as an extension and add functions to display an action sheet with options to either chose photos from photo library or activate the camera

*   Understand all configuration changes required for allowing users to access the camera and photo library

*   Manipulate image properties to give it an elegant look

*   Manage images as CKAsset to save it to our iCloud database

*   Retrieve the image from iCloud repository

*   Display it on the user profile screen

*   Implement these features using the MVC design pattern we have learned in the previous chapter

In our example, we will have three view controllers as explained below:

*   **ProfileCreationViewController:** This UIViewController will help in uploading the image from photo library or capture image directly from the camera (view).

*   **Profile:** This NSObject class represents the profile table in our iCloud database.

*   **ProfileDisplayViewController:** This UIViewController will display our stored image on the iOS screen.

## View Controller to Upload an Image

In this view controller, we will have an image view object drawn on a screen. When the user clicks on the image, an action sheet will be presented with options to either select an image from the photo library or use the camera to capture an image. Once the image is captured, the view controller will call save method of a modal class called Profile to save the image and its attributes to the repository.

```
import UIKit
import CloudKit
class ProfileCreationViewController: UIViewController{
```

We imported two Xcode toolkits: **UIKit** for all lifecycle and UI objects inclusion and **Cloudkit** for all iCloud persistence and data storage. Next, we are defining our UI view controller, **ProfileCreationViewController**, to create our profile image picker.

```
let personalProfileImageView: UIImageView = {
let theImageView = UIImageView()
theImageView.image = UIImage(systemName: "person.fill")
theImageView.contentMode = .scaleAspectFit
theImageView.translatesAutoresizingMaskIntoConstraints = false
theImageView.layer.masksToBounds = true
theImageView.layer.borderWidth = 1
theImageView.layer.borderColor = UIColor.systemGray.cgColor
return theImageView
}()
```

Our view controller will have an UIImageView object. This object will allow interaction, and clicking on the image view will offer the user an action sheet to either choose an image from the user photo library or take a picture from the camera. Note that to enable this, we need to set the application to prompt the user with a permission dialogue to access the photo library and the camera. Refer to the section at the end of this chapter to learn how to configure these properties.

Also, note our image view has an image object, which is by default displaying a system-defined person image with gray background.

```
let saveButton:UIButton = {
let button = UIButton()
button.translatesAutoresizingMaskIntoConstraints = false
button.contentHorizontalAlignment = .center
button.setTitleColor(.white, for: .normal)
button.titleLabel?.font = UIFont.boldSystemFont(ofSize: 14)
button.titleLabel?.textAlignment = .center
button.setTitle("Save and Exit", for: .normal)
button.layer.cornerRadius = 5
button.backgroundColor = UIColor(displayP3Red: 128/255, green: 0/255, blue: 128/255, alpha: 1)
button.isEnabled = true
return button
}()
```

Beside the image view, we are also defining a save button, and clicking the button will allow the user to save the image to iCloud. We are giving a blue background color to the button.

```
override func viewDidLoad() {
super.viewDidLoad()
let personalProfileGestureRecognizer = UITapGestureRecognizer(target: self, action: #selector(personalImageTapped(tapGestureRecognizer:)))
personalProfileImageView.isUserInteractionEnabled = true
personalProfileImageView.addGestureRecognizer(personalProfileGestureRecognizer)
personalProfileImageView.layer.cornerRadius =  50
drawPersonalScreen()
}
```

Now that we have defined the objects, next, we are going to define our lifecycle method viewDidLoad(). This method is evoked as soon as the user instantiates the Profile Creation View Controller. In this function, we are doing a few operations as described below:

*   Image view controller doesn’t have predefined events like a UI button or a UI text field, so we need to manually define it. UITapGestureRecognizer is an event that we are defining to achieve the tap event on the image. We are defining the action on tap to call a method personalImageTapped which takes the tap gesture as a default input parameter.

*   We are then making sure that the UIImageView object that we defined before (personalProfileImageView) its user interaction property is set to true. This will allow the gesture to capture event.

*   Finally, we are attaching our user-defined gesture event to our UI image object.

*   We are also setting a value of 50 to the corner radius of our image view. This will make the image view appear as a circle.

*   We are at the end calling a method called drawPersonalScreen, which will draw the image view and the button on the screen.

```
func drawPersonalScreen(){
let screensize: CGRect = UIScreen.main.bounds
let screenWidth = screensize.width
```

This is our drawPersonalScreen method. We are getting the iPhone screen width, which we will need to calculate the positioning of the UI objects on the screen. As there are various form factors of the iPhone, it is recommended to dynamically calculate the layout for better display of objects. Although in the below code we will not use it, it is a good feature to learn as you start creating complex screens.

```
view.addSubview(personalProfileImageView)
let constraints2 = [
personalProfileImageView.topAnchor.constraint(equalTo: view.topAnchor, constant: 5),
personalProfileImageView.leftAnchor.constraint(equalTo: view.leftAnchor, constant: 140),
personalProfileImageView.widthAnchor.constraint(equalToConstant: 100),
personalProfileImageView.heightAnchor.constraint(equalToConstant: 100)
]
NSLayoutConstraint.activate(constraints2)
```

This part of the code adds the UIImageView to the screen. As the image needs to be circular, it is important that the height and the width are the same. We are also leaving 5 spaces from the top and 140 spaces from the left.

```
view.addSubview(saveButton)
let constraints = [
saveButton.topAnchor.constraint(equalTo: personalProfileImageView.bottomAnchor, constant: 40),
saveButton.leftAnchor.constraint(equalTo: view.rightAnchor, constant: 5),
saveButton.widthAnchor.constraint(equalToConstant: 185),
saveButton.heightAnchor.constraint(equalToConstant: 40)
]
NSLayoutConstraint.activate(constraints)
saveButton.addTarget(self, action: #selector(saveAction), for: .touchUpInside)
}
```

Now, we are adding the save button to the screen. Notice how the top anchor of the button is taking reference from the UIImageView and drawing it 40 pixels after the image.

Notice that we are also adding the touch up inside the event and calling a method saveAction. So, when the user taps the save button, it will call this method to save the image to the iCloud database.

```
@objc func personalImageTapped(tapGestureRecognizer: UITapGestureRecognizer){
presentPhotoOptions()
}
```

This method is invoked when the user taps the image view. The method in turn calls our custom method presentPhotoOptions.

```
@objc func saveAction(sender: UIButton){
profileSave()
}
```

This method is called when the user taps the save button, which in turn calls the profileSave function defined in the next section.

```
func profileSave(){
```

This is the profileSave which will save the image and some other profile data (not captured in our screen to keep the code simple) to the iCloud repository.

```
let personalData = personalProfileImageView.image?.pngData()
let personalFilename = commonFunctions.getDocumentsDirectory().appendingPathComponent("copy.png")
try? personalData?.write(to: personalFilename)
let personalProfile : CKAsset?  = CKAsset(fileURL: personalFilename)
```

Data in an iCloud is stored as a CKAsset type object. This object stores the image as a binary object. In the code defined above, we are doing the following:

*   From the UI image object, we are getting the data in png format by using the system-defined method pngData() on the image and storing it in a local constant personalData.

*   Next, we are creating a dumpy object copy.png in the current workspace of the user. The path to the file is stored in a local constant personalFilename.

*   Then we are overwriting the copy.png file with the png data of the user uploaded profile image.

*   And finally, we are creating a CKAsset object giving the path to the local directory and the filename.

This CKAsset can now be stored in an iCloud repository.

```
let profile:Contact.Profile = Contact.Profile(userid: userid, personalprofile: personalProfile!, name: nameTextField.text!, personalphone: personalPhone)
```

We are now instantiating an object of our custom object, Profile with all required parameters (ID, image, name, phone). This is the modal class, which we will learn in the next section. Profile class will have the methods and the properties to define, store, and retrieve profile objects from the iCloud database.

```
self.contact.group.enter()
self.contact.saveProfile(profile: profile)
self.contact.group.notify(queue: .main) {
}
}
}
```

Any iCloud operation runs as an asynchronous method. The group helps us to cluster the methods together and wait on a main thread for completion. We are calling the saveProfile method of our modal class, which will have the routines to save the object to the iCloud repository. Notice that saveProfile takes Profile object as an IN parameter.

```
extension ProfileCreationViewController:UIImagePickerControllerDelegate, UINavigationControllerDelegate{
```

For all image manipulation functions, we will define an extension to the view controller class. We could have done this without an extension as well, but extension increases modularity and can help you to include this feature in other areas where you need to upload image. The following are some of the key call outs:

*   We are extending our ProfileCreationViewController class.

*   The class extends two delegates. The first one is UIImagePickerControllerDelegate, required for involving all image picking and display built in function, and the next one is UINavigationControllerDelegate for action sheet navigation.

```
func presentPhotoOptions(){
```

If you recall from the previous section, this method is called when the image is clicked and a tap gesture is invoked, which in turn calls this method.

```
var message:String = "Personal Profile Picture"
```

This is a display message on the action sheet. We are labeling it as “Personal Profile Picture”.

```
let actionSheet = UIAlertController(title: message, message: "Select your Option", preferredStyle: .actionSheet)
```

Next, we are defining our action sheet.

```
actionSheet.addAction(UIAlertAction(title: "cancel", style: .cancel, handler: nil))
```

The first action in the sheet is cancel. Selecting this option will close the action sheet without performing any function.

```
actionSheet.addAction(UIAlertAction(title: "Take Photo", style: .default, handler: { [weak self]_ in
self?.presentCamera()
}))
```

The second action allows the user to present the camera to the user to click a photo. Notice that when this option is selected, we are calling a custom function presentCamera().

```
actionSheet.addAction(UIAlertAction(title: "Choose Photo", style: .default, handler: {[weak self]_ in
self?.presentPhoto()
}))
```

And our last action allows the user to choose a photo from the photo library. Notice that when this option is selected, we are calling a custom function presentPhoto().

```
present(actionSheet, animated: true)
}
```

The method present will present the action sheet to the user. The action sheet appears by default on the bottom part of the screen.

```
func presentCamera(){
let ip = UIImagePickerController()
ip.sourceType = .camera
ip.delegate = self
ip.allowsEditing = true
present(ip, animated: true)
}
```

Method presentCamera will present a camera to the user and provide a UIImagePicker to capture the image.

```
func presentPhoto(){
let ip = UIImagePickerController()
ip.sourceType = .photoLibrary
ip.delegate = self
ip.allowsEditing = true
present(ip, animated: true)
}
```

Method presentPhoto will present a camera to the user and provide a UIImagePicker to capture the image.

```
func imagePickerController(_ picker: UIImagePickerController, didFinishPickingMediaWithInfo info: [UIImagePickerController.InfoKey : Any]) {
let selectedImage  = info[UIImagePickerController.InfoKey.editedImage]
self.personalProfileImageView.image = selectedImage as? UIImage
picker.dismiss(animated: true)
}
```

The function imagePickerController is a system-defined method of type UIImagePicker. The selected image is stored in the constant selectedImage and then assigned to the UIImageView. This will display the select profile image on the screen.

```
func imagePickerControllerDidCancel(_ picker: UIImagePickerController) {
picker.dismiss(animated: true)
}
}
}
```

This function is invoked when the user selects the cancel option, which will make the picker disappear.

**The complete code of view controller to upload image is given below:**

```
import UIKit
import CloudKit
class ProfileViewController: UIViewController, UITableViewDelegate, UITableViewDataSource, UITextFieldDelegate {
var contact = Contact()
let personalProfileImageView: UIImageView = {
let theImageView = UIImageView()
theImageView.image = UIImage(systemName: "person.fill")
theImageView.contentMode = .scaleAspectFit
theImageView.translatesAutoresizingMaskIntoConstraints = false
theImageView.layer.masksToBounds = true
theImageView.layer.borderWidth = 1
theImageView.layer.borderColor = UIColor.systemGray.cgColor
return theImageView
}()
let saveButton:UIButton = {
let button = UIButton()
button.translatesAutoresizingMaskIntoConstraints = false
button.contentHorizontalAlignment = .center
button.setTitleColor(.white, for: .normal)
button.titleLabel?.font = UIFont.boldSystemFont(ofSize: 14)
button.titleLabel?.textAlignment = .center
button.setTitle("Save and Exit", for: .normal)
button.layer.cornerRadius = 5
button.backgroundColor = UIColor(displayP3Red: 128/255, green: 0/255, blue: 128/255, alpha: 1)
button.isEnabled = true
return button
}()
override func viewDidLoad() {
super.viewDidLoad()
let personalProfileGestureRecognizer = UITapGestureRecognizer(target: self, action: #selector(personalImageTapped(tapGestureRecognizer:)))
personalProfileImageView.isUserInteractionEnabled = true
personalProfileImageView.addGestureRecognizer(personalProfileGestureRecognizer)
personalProfileImageView.layer.cornerRadius =  50
drawPersonalScreen()
}
func drawPersonalScreen(){
let screensize: CGRect = UIScreen.main.bounds
let screenWidth = screensize.width
view.addSubview(personalProfileImageView)
let constraints2 = [
personalProfileImageView.topAnchor.constraint(equalTo: view.topAnchor, constant: 5),
personalProfileImageView.leftAnchor.constraint(equalTo: view.leftAnchor, constant: 140),
personalProfileImageView.widthAnchor.constraint(equalToConstant: 100),
personalProfileImageView.heightAnchor.constraint(equalToConstant: 100)
]
view.addSubview(saveButton)
NSLayoutConstraint.activate(constraints2)
let constraints = [
saveButton.topAnchor.constraint(equalTo: personalProfileImageView.bottomAnchor, constant: 40),
saveButton.leftAnchor.constraint(equalTo: view.rightAnchor, constant: 5),
saveButton.widthAnchor.constraint(equalToConstant: 185),
saveButton.heightAnchor.constraint(equalToConstant: 40)
]
NSLayoutConstraint.activate(constraints)
saveButton.addTarget(self, action: #selector(saveAction), for: .touchUpInside)
}
@objc func saveAction(sender: UIButton){
profileSave()
}
//Image Tapped
@objc func personalImageTapped(tapGestureRecognizer: UITapGestureRecognizer){
presentPhotoOptions()
}
func profileSave(){
let personalData = personalProfileImageView.image?.pngData()
let personalFilename = commonFunctions.getDocumentsDirectory().appendingPathComponent("copy.png")
try? personalData?.write(to: personalFilename)
let personalProfile : CKAsset?  = CKAsset(fileURL: personalFilename)
let profile:Contact.Profile = Contact.Profile(userid: userid, personalprofile: personalProfile!, name: nameTextField.text!, personalphone: personalPhone)
self.contact.group.enter()
self.contact.saveProfile(profile: profile)
self.contact.group.notify(queue: .main) {
}
}
}
extension ProfileViewController:UIImagePickerControllerDelegate, UINavigationControllerDelegate{
func presentPhotoOptions(fromwhere: Int){
var message:String = " Personal Profile Picture "
let actionSheet = UIAlertController(title: message, message: "Select your Option", preferredStyle: .actionSheet)
actionSheet.addAction(UIAlertAction(title: "cancel", style: .cancel, handler: nil))
actionSheet.addAction(UIAlertAction(title: "Take Photo", style: .default, handler: { [weak self]_ in
self?.presentCamera(fromwhere: fromwhere)
}))
actionSheet.addAction(UIAlertAction(title: "Choose Photo", style: .default, handler: {[weak self]_ in
self?.presentPhoto(fromWhere: fromwhere)
}))
present(actionSheet, animated: true)
}
func presentCamera(fromwhere:Int){
let ip = UIImagePickerController()
ip.sourceType = .camera
ip.delegate = self
ip.allowsEditing = true
ip.navigationBar.tag = fromwhere
present(ip, animated: true)
}
func presentPhoto(fromWhere:Int){
let ip = UIImagePickerController()
ip.sourceType = .photoLibrary
ip.delegate = self
ip.allowsEditing = true
ip.navigationBar.tag = fromWhere
present(ip, animated: true)
}
func imagePickerController(_ picker: UIImagePickerController, didFinishPickingMediaWithInfo info: [UIImagePickerController.InfoKey : Any]) {
let selectedImage  = info[UIImagePickerController.InfoKey.editedImage]
self.personalProfileImageView.image = selectedImage as? UIImage
picker.dismiss(animated: true)
}
func imagePickerControllerDidCancel(_ picker: UIImagePickerController) {
picker.dismiss(animated: true)
}
}
```

## Profile Modal Class to Save and Retrieve User Profile Image

In this modal class, we will save the user image as a **CKAsset** object in the iCloud repository. We will also retrieve all contacts for the user and store contact profile picture and name in an array of type **Profile** class.

```
import UIKit
import CloudKit
class Profile: NSObject {
```

We imported two Xcode toolkits: **UIKit** for all lifecycle and UI objects inclusion and **Cloudkit** for all iCloud persistence and data storage. Next, we are defining NSObject class, Profile, which will define the profile object and all related database manipulation methods.

```
let group = DispatchGroup()
let syncQueue = DispatchQueue(label: "com.domain.app.sections")
```

We are defining a constant called **group** which stores an instantiated object of a synchronization object called **DispatchGroup**.

Next, we are instantiating an object of type **DispatchQueue** and storing it in a constant called **syncQueue**. The name of the queue is **com.domain.app.sections**. This will help us to serial process many asynchronous calls one after the other.

```
class Profile:NSObject{
var record:CKRecord!
var userid:String!
var personalprofile:CKAsset!
var name: String!
var personalphone:String!
init(userid:String, personalprofile:CKAsset, name:String,  personalphone: String) {
self.userid = userid
self.personalprofile = personalprofile
self.name = name
self.personalphone = personalphone
}
}
```

This is our custom Profile object. The properties of this class mimic the profile table in the iCloud repository. Besides the profile image, we have a few other attributes defined; for the simplicity of the code, we didn’t include those elements in our profile capture screen. Also, the init method allows the user to instantiate the object of the class profile with default value. When we created the profile object in the Profile Creation View Controller, we used the init function to store all necessary data in the object, ready to be saved into the repository.

```
var profile:Profile?
var profiles:[Profile] = []
```

We are defining two variables of our Profile class. The first one, profile, will store a single profile object, and the next one, profiles, will store an array of profile objects. The latter is important, as you will see the display view controller which will retrieve and display all contacts a user has.

```
func saveProfile(profile:Profile){
```

If you recall, saveProfile was called with the profile object from the Profile Creation View Controller. This is the definition of this method. You will see in the subsequent lines how we are saving this to the iCloud repository.

```
let publicDatabase = CKContainer.default().publicCloudDatabase
let aRecord = CKRecord(recordType: "profile")
```

```
aRecord["userid"] = profile.userid
aRecord["personalprofile"] = profile.personalprofile
aRecord["name"] = profile.name
aRecord["personalphone"] = profile.personalphone
```

The following are the key elements in this block of code:

*   Define a constant to hold reference of the public cloud database.

*   Define a constant to hold the reference of the iCloud table (record type) profile.

*   Assign the user-provided values to the respective fields of the iCloud profile. It is important to note that the data type should properly match, or otherwise, it will give run time error. For example, personalprofile is of type CKAsset.

```
publicDatabase.save(aRecord, completionHandler: { (record, error) -> Void in
DispatchQueue.main.async {
if let error = error {
print(error)
}
else {
self.syncQueue.async {
self.profile = Profile(userid: record!.object(forKey: "userid")! as! String, personalprofile: record!.object(forKey: "personalprofile")! as! CKAsset, name: record!.object(forKey: "name")! as! String, personalphone: record!.object(forKey: "personalphone")! as! String)
self.profile?.record = record
self.group.leave()
}
}
}
})
}
```

Now that we have the record defined, we will go ahead and attempt to save it. The following are the key points to note in this block of code:

*   We are calling the save method on the public database constant; it takes reference to a CKRecord type (record to be stored) and has a completion handler returning the saved record or an error.

*   All iCloud methods are asynchronous; hence, we run it on a main thread as an async process.

*   Upon completion, if there is an error, it will print the error on the console.

*   Or we are retrieving the stored record into our local variable profile and leave the thread to notify the calling function that the operation is successfully completed.

```
func getContacts(businessType:String) {
```

The getContacts retrieves all contacts of the current user. It takes a parameter called businessType to formulate a conditional boundary to retrieve the user contacts for a particular business type.

```
profiles.removeAll()
```

The variable profiles are defined before, which can store an array of contacts. We are removing all data to ensure the array object is empty.

```
let pred = NSPredicate(format: "businessType == %@", businessType)
let query = CKQuery(recordType: "profile", predicate: pred)
let operation = CKQueryOperation(query: query)
```

The following are the key elements in the above code block:

*   NSPredicate helps to define a condition for our query. In our case, we are instructing it to get all contacts where business type is equal to the user-provided user business type.

*   Next, we are defining a CKQuery, a query which takes our predicate as an input alongside a record type name against which the query needs to be executed.

*   Then we are defining the CKQueryOperation, to execute our query.

```
let operationConfiguration = CKOperation.Configuration()
operationConfiguration.timeoutIntervalForRequest = 10
operationConfiguration.timeoutIntervalForResource = 10
operation.configuration = operationConfiguration
```

The following are the key points in this block of code:

*   Defining a CKOperation.Configuration is optional. We are defining for set timeout intervals, so that the query can exit if not responding after a particular set time.

*   This property determines the request timeout interval (in seconds) for the query to complete. In our example, the task will wait for 10 sec for additional data to arrive before it gives up.

*   This property determines the request timeout interval (in seconds) for the resource retrieval to complete. In our example, the task will wait for 10 sec for additional data to arrive before it gives up.

*   The operation configuration object we created is now assigned to the ***operation*** object we have defined before.

```
CKContainer.default().publicCloudDatabase.add(operation)
```

We now added the operation to our public cloud. This will execute the query in asynchronous mode.

```
operation.recordMatchedBlock = { recordID, result in
switch result {
case .success(let record):
let profile = Profile(userid: record["userid"] as! String, personalprofile: record["personalprofile"] as! CKAsset, name: record["name"] as! String, personalphone: record["personalphone"] as! String)
profile.record = record
self.profiles.append(profile)
case .failure(let error):
print(error.localizedDescription)
// Handle the case when the table doesn't exist or there's an error
}
}
```

The following are the key elements of this block of code:

*   The recordMatchedBlock is called when the query is completed. It has two parameters: the recordID and a result set.

*   Result has a success and failure block, which is what we are next switching on to form a conditional block.

*   If it is a success, we are retrieving the value and storing it in our profiles array. Note that this block will repeat like a cursor until all records are processed.

*   In case of failure, we are printing the error on the console.

```
operation.queryResultBlock = { result in
DispatchQueue.main.async {
switch result {
case .failure(let error):
debugPrint(error)
// Handle the query result failure
self.profileExists = false
self.syncQueue.async {
self.group.leave()
}
case .success(_):
// Handle the query result success
self.syncQueue.async {
self.group.leave()
}
}
}
}
}
}
```

This block queryResultBlock is executed once the recordMatchedBlock is iterated through and on success we are returning to the calling function.

**The complete code for the Contact modal class is given below:**

```
import UIKit
import CloudKit
class Contact: NSObject {
let group = DispatchGroup()
let syncQueue = DispatchQueue(label: "com.domain.app.sections")
class Profile:NSObject{
var record:CKRecord!
var userid:String!
var personalprofile:CKAsset!
var name: String!
var personalphone:String!
var businessprofile:CKAsset!
init(userid:String, personalprofile:CKAsset, name:String,  personalphone: String) {
self.userid = userid
self.personalprofile = personalprofile
self.name = name
self.personalphone = personalphone
}
}
var profile:Profile?
var profiles:[Profile] = []
func getContacts(businessType:String) {
profiles.removeAll()
let pred = NSPredicate(format: "businesstype == %@", businessType)
let query = CKQuery(recordType: "profile", predicate: pred)
let operation = CKQueryOperation(query: query)
let operationConfiguration = CKOperation.Configuration()
operationConfiguration.timeoutIntervalForRequest = 10
operationConfiguration.timeoutIntervalForResource = 10
operation.configuration = operationConfiguration
CKContainer.default().publicCloudDatabase.add(operation)
operation.recordMatchedBlock = { recordID, result in
switch result {
case .success(let record):
let profile = Profile(userid: record["userid"] as! String, personalprofile: record["personalprofile"] as! CKAsset, name: record["name"] as! String, personalphone: record["personalphone"] as! String)
profile.record = record
self.profiles.append(profile)
case .failure(let error):
print(error.localizedDescription)
}
}
operation.queryResultBlock = { result in
DispatchQueue.main.async {
switch result {
case .failure(let error):
debugPrint(error)
self.syncQueue.async {
self.group.leave()
}
case .success(_):
// Handle the query result success
self.syncQueue.async {
self.group.leave()
}
}
}
}
}
func saveProfile(profile:Profile){
let publicDatabase = CKContainer.default().publicCloudDatabase
let aRecord = CKRecord(recordType: "profile")
aRecord["userid"] = profile.userid
aRecord["personalprofile"] = profile.personalprofile
aRecord["name"] = profile.name
aRecord["personalphone"] = profile.personalphone
publicDatabase.save(aRecord, completionHandler: { (record, error) -> Void in
DispatchQueue.main.async {
if let error = error {
print(error)
}
else {
self.syncQueue.async {
self.profile = Profile(userid: record!.object(forKey: "userid")! as! String, personalprofile: record!.object(forKey: "personalprofile")! as! CKAsset, name: record!.object(forKey: "name")! as! String, personalphone: record!.object(forKey: "personalphone")! as! String)
self.profile?.record = record
self.group.leave()
}
}
}
})
}
}
```

## View Controller to Display the Image

In this view controller, we will display the image retrieved from the iCloud database.

```
import UIKit
class ProfileDisplayViewController: UIViewController, UITableViewDelegate, UITableViewDataSource{
```

In the above block of code, we imported the Xcode toolkit, **UIKit** for all lifecycle and UI objects inclusion. Next, we are defining a UI view controller class, ProfileDisplayViewController, which will display all contacts of a user in a table view. We will also define a custom cell to display the circular contact image and contact name next to each other, like how it is displayed in WhatsApp. Notice that we are extending the class to include table delegate and data source.

```
var contact = Contact()
var profile:Contact.Profile?
```

Defining a variable contact of our modal class Contact type and an object to hold the profile defined in out Contact class.

```
private let contactTableView:UITableView = {
let tableView = UITableView()
tableView.translatesAutoresizingMaskIntoConstraints = false
tableView.backgroundColor = .white
tableView.isScrollEnabled = true
return tableView
}()
```

A table view to display the contacts in a table is given below:

```
override func viewWillAppear(_ animated: Bool) {
super.viewWillAppear(true)
getContacts()
}
```

We are using viewWillAppear lifecycle method to perform to get all contacts so that we can display it in the table view.

```
func setup(){
contactTableView.delegate = self
contactTableView.dataSource = self
contactTableView.register(ContactTableViewCell.self, forCellReuseIdentifier: "contact")
contactTableView.layer.borderWidth = 0
contactTableView.layer.borderColor = UIColor.white.cgColor
contactTableView.separatorStyle = .singleLine
contactTableView.isHidden = false
}
```

The setup() function defines the following:

*   Table delegate and data source as self

*   Assign a custom cell named ContactTableViewCell to the table

*   Set up some design elements for the table

```
func drawScreen(){
let screensize: CGRect = UIScreen.main.bounds
let screenWidth = screensize.width
let screenHeight = screensize.height
view.addSubview(contactTableView)
let constraints = [
contactTableView.topAnchor.constraint(equalTo: view.topAnchor, constant: 5),
contactTableView.leftAnchor.constraint(equalTo: view.leftAnchor, constant: 5),
contactTableView.widthAnchor.constraint(equalToConstant: screenWidth - 10),
contactTableView.heightAnchor.constraint(equalToConstant: screenHeight - 50)
]
NSLayoutConstraint.activate(constraints)
}
```

This function draws the table on the screen. We are first retrieving the current screen width and height and depending on the form factor making the table occupy the entire screen except for few edges left on all sides.

```
getContacts(){
self.contact.group.enter()
self.contact.getContacts(businessType: businessTypeTextField.text!)
self.contact.group.notify(queue: .main) { [self] in
contact.profiles = contact.profiles.filter{
contact in return contact.userid != profile?.userid
}
setup()
drawScreen()
contactTableView.reloadData()
}
}
```

The getContacts method calls the modal class getContacts to retrieve all user contacts based on the user type of business. Note that the type of business is assumed to be a UI text field, which we are not defining in this example. Once the asynchronous method completes, it will call the setup() function to do some initial declaration of the table object and the drawScreen() to draw the table on the screen.

```
func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
return contact.profiles.count
}
```

Table view has a few predefined methods that must be implemented. This function tells the table view how many records exist.

```
func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
let cell = tableView.dequeueReusableCell(withIdentifier: "contact", for: indexPath) as! ContactTableViewCell
let file = self.contact.profiles[indexPath.row].personalprofile.fileURL
if let data = NSData(contentsOf: file!) {
cell.profileImageView.image = UIImage(data: data as Data)
}
cell.profileImageView.layer.cornerRadius =  60
cell.nameButton.setTitle(self.contact.profiles[indexPath.row].name.capitalized, for: .normal)
return cell
}
```

This system-defined table view function displays the name and the contact on a table row. This will repeat for all records. Here are some of the key call outs of this function:

*   Define a reference to the custom cell.

*   For image, we are getting the file content using fileURL of CKAsset object and storing it n a constant file.

*   And if data exists in the file, it is using the UI image data constructor to pass the data and render the image in the custom cell image object of the UIImageView.

*   The custom cell provides a label object to set the contact’s name.

```
func tableView(_ tableView: UITableView, heightForRowAt indexPath: IndexPath) -> CGFloat {
return 155
}
}
```

This is also a system-defined table view function which defines the width of the cell.

**The complete code for the Profile display class is given below:**

```
import UIKit
class ContactViewController: UIViewController, UITableViewDelegate, UITableViewDataSource {
var contact = Contact()
var profile:Contact.Profile?
private let contactTableView:UITableView = {
let tableView = UITableView()
tableView.translatesAutoresizingMaskIntoConstraints = false
tableView.backgroundColor = .white
tableView.isScrollEnabled = true
return tableView
}()
override func viewWillAppear(_ animated: Bool) {
super.viewWillAppear(true)
getContacts()
}
func setup(){
contactTableView.delegate = self
contactTableView.dataSource = self
contactTableView.register(ContactTableViewCell.self, forCellReuseIdentifier: "contact")
contactTableView.layer.borderWidth = 0
contactTableView.layer.borderColor = UIColor.white.cgColor
contactTableView.separatorStyle = .singleLine
contactTableView.isHidden = true
}
func drawScreen(){
let screensize: CGRect = UIScreen.main.bounds
let screenWidth = screensize.width
let screenHeight = screensize.height
view.addSubview(contactTableView)
let constraints = [
contactTableView.topAnchor.constraint(equalTo: view.topAnchor, constant: 0),
contactTableView.leftAnchor.constraint(equalTo: view.leftAnchor, constant: 5),
contactTableView.widthAnchor.constraint(equalToConstant: screenWidth - 10),
contactTableView.heightAnchor.constraint(equalToConstant: screenHeight - 50)
]
NSLayoutConstraint.activate(constraints)
}
getContacts(){
self.contact.group.enter()
self.contact.getContacts(businessType: businessTypeTextField.text!)
self.contact.group.notify(queue: .main) { [self] in
contact.profiles = contact.profiles.filter{
contact in return contact.userid != profile?.userid
}
setup()
drawScreen()
contactTableView.reloadData()
}
}
func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
return contact.profiles.count
}
func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
let cell = tableView.dequeueReusableCell(withIdentifier: "contact", for: indexPath) as! ContactTableViewCell
let file = self.contact.profiles[indexPath.row].businessprofile.fileURL
if let data = NSData(contentsOf: file!) {
cell.profileImageView.image = UIImage(data: data as Data)
}
cell.profileImageView.layer.cornerRadius =  60
cell.nameButton.setTitle(self.contact.profiles[indexPath.row].name.capitalized, for: .normal)
return cell
}
}
func tableView(_ tableView: UITableView, heightForRowAt indexPath: IndexPath) -> CGFloat {
return 155
}
}
```

## Defining the Custom Table Cell

To display the contacts in a table, we will define a custom table cell. The custom cell will show the contact as a circle image with the name of the contact next to it in a row.

```
import UIKit
class ContactTableViewCell: UITableViewCell {
```

We are importing UIKit to get access to all lifecycle methods and the UI objects. We are now defining a UITableViewCell class where we will define custom image and title display.

```
var profileImageView: UIImageView = {
let imageView = UIImageView()
imageView.image = UIImage(systemName: "person.fill")
imageView.contentMode = .scaleAspectFit
imageView.translatesAutoresizingMaskIntoConstraints = false
imageView.layer.masksToBounds = true
imageView.layer.borderWidth = 1
imageView.layer.borderColor = UIColor.systemGray.cgColor
return imageView
}()
```

This is the UIImageView object, which will display the contact image in the cell row.

```
let nameButton:UIButton = {
let uiButton  = UIButton()
uiButton.translatesAutoresizingMaskIntoConstraints = false
uiButton.setTitleColor(UIColor.black, for: .normal)
uiButton.contentHorizontalAlignment = .left
uiButton.contentVerticalAlignment = .top
uiButton.titleLabel?.font = UIFont.systemFont(ofSize: 20, weight: .semibold)
return uiButton
}()
```

This is the UI button object which will display the name in the cell row.

```
override func awakeFromNib() {
super.awakeFromNib()
}
```

This is a lifecycle method and is called when the cell is loaded from a storyboard or nib file. It's empty in this code, but you can use it to perform additional setup if needed.

```
override func setSelected(_ selected: Bool, animated: Bool) {
super.setSelected(selected, animated: animated)
let screensize: CGRect = UIScreen.main.bounds
let screenWidth = screensize.width
contentView.addSubview(profileImageView)
let constraints = [
profileImageView.topAnchor.constraint(equalTo: contentView.topAnchor, constant: 5),
profileImageView.leftAnchor.constraint(equalTo: contentView.leftAnchor, constant: 5),
profileImageView.widthAnchor.constraint(equalToConstant: 120),
profileImageView.heightAnchor.constraint(equalToConstant: 120)
]
NSLayoutConstraint.activate(constraints)
contentView.addSubview(nameButton)
let constraints1 = [
nameButton.topAnchor.constraint(equalTo: contentView.topAnchor, constant: 5),
nameButton.leftAnchor.constraint(equalTo: profileImageView.rightAnchor, constant: 5),
nameButton.widthAnchor.constraint(equalToConstant: screenWidth),
nameButton.heightAnchor.constraint(equalToConstant: 20)
]
NSLayoutConstraint.activate(constraints1)
}
}
```

This is part of the lifecycle framework and is called whenever the selection state of the cell changes. It's overridden here to add the UI elements. In this code, we are adding the image view and the title button next to each other.

**The complete code for the table cell is given below:**

```
import UIKit
class ContactTableViewCell: UITableViewCell {
var profileImageView: UIImageView = {
let imageView = UIImageView()
imageView.image = UIImage(systemName: "person.fill")
imageView.contentMode = .scaleAspectFit
imageView.translatesAutoresizingMaskIntoConstraints = false
imageView.layer.masksToBounds = true
imageView.layer.borderWidth = 1
imageView.layer.borderColor = UIColor.systemGray.cgColor
return imageView
}()
let nameButton:UIButton = {
let uiButton  = UIButton()
uiButton.translatesAutoresizingMaskIntoConstraints = false
uiButton.setTitleColor(UIColor.black, for: .normal)
uiButton.contentHorizontalAlignment = .left
uiButton.contentVerticalAlignment = .top
uiButton.titleLabel?.font = UIFont.systemFont(ofSize: 20, weight: .semibold)
return uiButton
}()
override func awakeFromNib() {
super.awakeFromNib()
}
override func setSelected(_ selected: Bool, animated: Bool) {
super.setSelected(selected, animated: animated)
let screensize: CGRect = UIScreen.main.bounds
let screenWidth = screensize.width
contentView.addSubview(profileImageView)
let constraints = [
profileImageView.topAnchor.constraint(equalTo: contentView.topAnchor, constant: 5),
profileImageView.leftAnchor.constraint(equalTo: contentView.leftAnchor, constant: 5),
profileImageView.widthAnchor.constraint(equalToConstant: 120),
profileImageView.heightAnchor.constraint(equalToConstant: 120)
]
NSLayoutConstraint.activate(constraints)
contentView.addSubview(nameButton)
let constraints1 = [
nameButton.topAnchor.constraint(equalTo: contentView.topAnchor, constant: 5),
nameButton.leftAnchor.constraint(equalTo: profileImageView.rightAnchor, constant: 5),
nameButton.widthAnchor.constraint(equalToConstant: screenWidth),
nameButton.heightAnchor.constraint(equalToConstant: 20)
]
NSLayoutConstraint.activate(constraints1)
}
}
```

## Enabling Access to Photo Library and Camera

To access the photo library and the camera, our code needs to take permission from the user, and to achieve this, we need to set certain properties in the info.plist. Without this, the code won’t work. Follow along the following steps to achieve the same.

*   In your Xcode navigator, locate info.plist.

![](images/533539_2_En_5_Chapter/533539_2_En_5_Figa_HTML.jpg)

*   Add a property “Privacy - Camera Usage Description”.

*   In the value field, add your message. In our example, I added the following text “The App utilizes the camera functionality to capture an image, which serves as the user's profile picture and is visible to other users.”

*   Build and run your project

# 6. Drop-Down List

A drop-down list is quite a useful feature that an iOS app developer can use while designing data input forms. In this chapter, we will design a drop-down list with the following features:

*   Define a UI text field with an add icon left aligned to the text field.

*   Defining the tap inside event on the UI text field to invoke the drop-down function.

*   Upon user tapping, a hidden table will pop up, which is aligned to appear right at the bottom of the text field with all required values.

*   User can also type directly in the text box, which will dynamically shorten or broaden the list.

*   Once user select the desired value from the list, the table is hidden, and the selected value is displayed in the UI text field.

In our example, we will assume we have a list of business types that the user can select to indicate the type of business the user has as a part of his profile creation. The following are the key elements:

*   Define a class **“Business” of type NSObject** which will have all business type properties and methods

*   Define a UI view controller with the name **“CreateProfileViewController”** to demonstrate the drop-down list feature

*   Define a UI text field with the name **“businessTypeTextField****”** and its associated methods where user can type the desired business type and the drop-down list will appear

*   Define a UI table view with the name **“businessTypeTableView”** and its associated methods which will show a prepopulated business type

## Define the Business Class

The Business class will have all the properties that define the business type and an initialization method to set the values. The following code defines the class and the initialization method:

```
import UIKit
class Business: NSObject{
var businesstype:String!
var businessdesc:String!
var imagename:String!
init(businesstype:String, businessdesc:String, imagename:String) {
self.businesstype = businesstype
self.businessdesc = businessdesc
self.imagename = imagename
}
}
```

The class name is **Business**, and it has three properties: the type of the business (**businesstype**), description of the business (**businessdesc**), and an image that will give a pictorial identification of the business type (**imagename**).

The class also has a system-defined **init** method to initialize the values for the instantiated objects.

## Define the CreateProfileViewController

This class will create the user profile. While a user profile will have many properties like name, profile picture, and phone number, in this example, we will only focus on creating the business type drop-down list. Follow through the code to learn how to create a dynamic drop-down list using text field and a table view.

```
class CreateProfileViewController: UIViewController, UITableViewDelegate, UITableViewDataSource, UITextFieldDelegate {
```

The above code creates a view controller class with the name “**CreateProfileViewController**”. We are extending the following delegates:

*   **UIViewController:** For UI view controller lifecycle methods.

*   **UITableViewDelegate and UITableViewDataSource:** For all table functions. This is to show the table with all business type values.

*   **UITextFieldDelegate:** For all UI text field event functions to show and populate the business type based on user input.

```
let businessTypeTextField:UITextField = {
let textField = UITextField()
textField.translatesAutoresizingMaskIntoConstraints = false
textField.borderStyle = .roundedRect
textField.textColor = .black
textField.layer.borderColor  = UIColor.systemGray.cgColor
textField.layer.borderWidth = 1
textField.layer.cornerRadius = 5
textField.placeholder = "Select Business Type"
textField.textColor = .black
textField.isEnabled = true
textField.keyboardType = .default
return textField
}()
```

The above code defines a UI text field object with the name **businessTypeTextField**. There are certain properties which are attached to the text field, such as the text color is set to black, the border color is set to gray with a width of one point, and an effect to make the corner bend (**cornerRadius**). The text field is also given an initial placeholder value and with the default QWERTY keyboard.

```
private let businessTypeTableView:UITableView = {
let tableView = UITableView()
tableView.translatesAutoresizingMaskIntoConstraints = false
tableView.backgroundColor = .white
tableView.isScrollEnabled = true
return tableView
}()
```

The above code defines a **UITableView** with the name **businessTypeTableView**. We are enabling the scroll (**isScrollEnabled** is set to true) since we will have many business types.

```
override func viewDidLoad() {
super.viewDidLoad()
setup()
drawProfileCreationScreen()
}
```

The above code is the UI view controller lifecycle method. **viewDidLoad** is the first function to be called. There are two custom functions we are calling from this function: the **setup()** function to initialize a few variables and set a few methods and the **drawProfileCreationScreen()** for drawing the screen.

```
func setup(){
view.backgroundColor = .black
businessTypeTableView.delegate = self
businessTypeTableView.dataSource = self
businessTypeTableView.register(UITableViewCell.self, forCellReuseIdentifier: "business")
businessTypeTableView.layer.borderWidth = 0
businessTypeTableView.layer.borderColor = UIColor.white.cgColor
businessTypeTableView.separatorStyle = .none
businessTypeTableView.isHidden = true
businessTypeTextField.delegate = self
businessTypeTextField.leftView = UIImageView(image: UIImage(systemName: "plus"))
businessTypeTextField.leftViewMode = .always
businessTypeTextField.addTarget(self, action: #selector(businessTypeEditingChanged), for: .editingChanged)
businessTypeTextField.addTarget(self, action: #selector(businessTypeEditingDidBegin), for: .editingDidBegin)
businessTypeTextField.addTarget(self, action: #selector(businessTypeEditingDidEnd), for: .editingDidEnd)
setBusiness()
}
```

The above code is for the setup() method. In this function, we are doing the following:

*   Setting the delegate and data source for the business type table view.

*   Setting the table cell as the default table view cell with name **“business”**.

*   Setting some style properties of the table such a border width and color and setting the table display value as hidden.

*   We are also setting the business type input text field delegate so that we can invoke the text field functions.

*   A system icon “+” is added to the left of the text box to give a visual reference to the user that clicking the text field will display the drop-down.

*   Finally, we are adding three system methods: The first one is **“businessTypeEditingChanged”**, which will be invoked anytime when the user edits the value of the text field. The second one is **“editingDidBegin”**, which will be triggered when user for the first time starts typing something to the text field, and the third one is **“editingDidEnd”**, which is executed once the focus goes out of the text field.

```
var business:[Business] = []
var tempBusiness:[Business] = []
```

The above two statements define arrays of class Business. The first one will hold all the business type object details and the second one is a temporary holder of business values, which is used to transfer the narrow list of business types based on user search criteria.

```
func setBusiness(){
business.append(Business(businesstype: "Mortgage", businessdesc: "Provide Mortgage Solution", imagename: "scribble"))
business.append(Business(businesstype: "Insurance", businessdesc: "Provide Life/Health/Car/P&C Insurance", imagename: "folder"))
business.append(Business(businesstype: "Electrician", businessdesc: "Provide Household electrical solution", imagename: "doc.text"))
business.append(Business(businesstype: "Handyman", businessdesc: "Provide any household related work", imagename: "book"))
}
```

The function above sets some business type values to the **“business”** array object we defined above. Note that we are storing only four business type objects to keep the code simple.

```
func drawProfileCreationScreen(){
let screensize: CGRect = UIScreen.main.bounds
let screenWidth = screensize.width
view.addSubview(businessTypeTextField)
let constraints5 = [
businessTypeTextField.topAnchor.constraint(equalTo: view.topAnchor, constant: 10),
businessTypeTextField.leftAnchor.constraint(equalTo: view.leftAnchor, constant: 5),
businessTypeTextField.widthAnchor.constraint(equalToConstant: screenWidth - 10),
businessTypeTextField.heightAnchor.constraint(equalToConstant: 40)
]
NSLayoutConstraint.activate(constraints5)
view.addSubview(businessTypeTableView)
let constraints6 = [
businessTypeTableView.topAnchor.constraint(equalTo: businessTypeTextField.bottomAnchor, constant: 0),
businessTypeTableView.leftAnchor.constraint(equalTo: view.leftAnchor, constant: 5),
businessTypeTableView.widthAnchor.constraint(equalToConstant: screenWidth - 10),
businessTypeTableView.heightAnchor.constraint(equalToConstant: 300)
]
NSLayoutConstraint.activate(constraints6)
}
```

The above method is called right after the setup method is called in the viewDidLoad() method. In this method, we are drawing our drop-down screen by placing the text field on the top of the screen, followed by the table view, which will be hidden when the screen is first displayed.

### Table View Methods

The next four methods are about the table view functions.

```
func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
return business.count
}
```

The above method informs the table view controller about the number of records the **business** object has. This will help the table view to loop through all business types to be displayed in the table when the user brings the focus on the input text field.

```
func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
let cell = tableView.dequeueReusableCell(withIdentifier: "business", for: indexPath)
cell.textLabel?.text = business[indexPath.row].businesstype + " " + business[indexPath.row].businessdesc
return cell
}
```

The above code is drawing the table by looping through each of the business objects. It uses the default UI cell table view and appends the business type and business description of the looped through **business** objects.

```
func tableView(_ tableView: UITableView, heightForRowAt indexPath: IndexPath) -> CGFloat {
return 40.0
}
```

This above code is the third method of the table view which defines the width of each displayed row as 40 pixels.

```
func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath) {
businessTypeTextField.text = business[indexPath.row].businesstype
businessTypeTextField.resignFirstResponder()
}
```

The above code is the final block of code for the table view. This function is invoked when the user selects a row. At that moment, we are assigning the text field the business type value that the user selected and making sure the focus is away from the text field.

### Text Field Functions

The next three functions are text field event functions. These methods are triggered when certain actions are performed on the text field.

```
@objc func businessTypeEditingDidBegin(sender: UITextField){
tempBusiness = business
businessTypeTableView.isHidden = false
}
```

The above code is executed when the user begins texting. At this time, we are transferring the entire **business** object to a temporary variable called **tempBusiness**. This is required because we are going to manipulate the **business** object to contain only the objects that matched the user's search criteria. Once the editing is done, we will make sure the original list is reset into the **business** object so that it is ready for the next search.

We are also now making the business type table view visible.

```
@objc func businessTypeEditingDidEnd(sender: UITextField){
business = tempBusiness
businessTypeTableView.isHidden = true
}
```

The above code is executed when the editing is over. Currently, we are transferring the **tempBusiness** object (which contains the original list of business type objects) to the original business object (so that it is ready for next search).

We are also now making the business type table view hidden now.

```
@objc func businessTypeEditingChanged(sender: UITextField){
business = business.filter{
business in return business.businesstype.trimmingCharacters(in: .whitespaces).lowercased().contains(sender.text!.lowercased())
}
if(sender.text == ""){
business = tempBusiness
}
businessTypeTableView.reloadData()
}
```

The above code is executed every time a user is typing into the text field. Upon user typing, we are filtering the **business** object based on the current text in the text field. We are removing the white spaces and making everything lowercase to make case-insensitive search. Once the search is commenced, the searched objects are transferred to the **business** object.

In case the user deletes all text, we are reassigning the original list to the **business** object by transferring values from **tempBusiness** object to business object.

At the end, by refreshing the **business** type table view, we are reloading the table to only show the retracted list of **business** object as stored in the business object (notice table is drawn based on the **business** object value).

## Complete Code of Drop-Down Function

Below is the complete code of the drop-down function as discussed in the code above.

```
import UIKit
class Business: NSObject{
var businesstype:String!
var businessdesc:String!
var imagename:String!
init(businesstype:String, businessdesc:String, imagename:String) {
self.businesstype = businesstype
self.businessdesc = businessdesc
self.imagename = imagename
}
}
class CreateProfileViewController: UIViewController, UITableViewDelegate, UITableViewDataSource, UITextFieldDelegate {
let businessTypeTextField:UITextField = {
let textField = UITextField()
textField.translatesAutoresizingMaskIntoConstraints = false
textField.borderStyle = .roundedRect
textField.textColor = .black
textField.layer.borderColor  = UIColor.systemGray.cgColor
textField.layer.borderWidth = 1
textField.layer.cornerRadius = 5
textField.placeholder = "Select Business Type"
textField.textColor = .black
textField.isEnabled = true
textField.keyboardType = .default
return textField
}()
private let businessTypeTableView:UITableView = {
let tableView = UITableView()
tableView.translatesAutoresizingMaskIntoConstraints = false
tableView.backgroundColor = .white
tableView.isScrollEnabled = true
return tableView
}()
override func viewDidLoad() {
super.viewDidLoad()
setup()
drawProfileCreationScreen()
}
func setup(){
view.backgroundColor = .black
businessTypeTableView.delegate = self
businessTypeTableView.dataSource = self
businessTypeTableView.register(UITableViewCell.self, forCellReuseIdentifier: "business")
businessTypeTableView.layer.borderWidth = 0
businessTypeTableView.layer.borderColor = UIColor.white.cgColor
businessTypeTableView.separatorStyle = .none
businessTypeTableView.isHidden = true
businessTypeTextField.delegate = self
businessTypeTextField.leftView = UIImageView(image: UIImage(systemName: "plus"))
businessTypeTextField.leftViewMode = .always
businessTypeTextField.addTarget(self, action: #selector(businessTypeEditingChanged), for: .editingChanged)
businessTypeTextField.addTarget(self, action: #selector(businessTypeEditingDidBegin), for: .editingDidBegin)
businessTypeTextField.addTarget(self, action: #selector(businessTypeEditingDidEnd), for: .editingDidEnd)
setBusiness()
}
var business:[Business] = []
var tempBusiness:[Business] = []
func setBusiness(){
business.append(Business(businesstype: "Mortgage", businessdesc: "Provide Mortgage Solution", imagename: "scribble"))
business.append(Business(businesstype: "Insurance", businessdesc: "Provide Life/Health/Car/P&C Insurance", imagename: "folder"))
business.append(Business(businesstype: "Electrician", businessdesc: "Provide Household electrical solution", imagename: "doc.text"))
business.append(Business(businesstype: "Handyman", businessdesc: "Provide any household related work", imagename: "book"))
}
func drawProfileCreationScreen(){
let screensize: CGRect = UIScreen.main.bounds
let screenWidth = screensize.width
view.addSubview(businessTypeTextField)
let constraints5 = [
businessTypeTextField.topAnchor.constraint(equalTo: view.topAnchor, constant: 10),
businessTypeTextField.leftAnchor.constraint(equalTo: view.leftAnchor, constant: 5),
businessTypeTextField.widthAnchor.constraint(equalToConstant: screenWidth - 10),
businessTypeTextField.heightAnchor.constraint(equalToConstant: 40)
]
NSLayoutConstraint.activate(constraints5)
view.addSubview(businessTypeTableView)
let constraints6 = [
businessTypeTableView.topAnchor.constraint(equalTo: businessTypeTextField.bottomAnchor, constant: 0),
businessTypeTableView.leftAnchor.constraint(equalTo: view.leftAnchor, constant: 5),
businessTypeTableView.widthAnchor.constraint(equalToConstant: screenWidth - 10),
businessTypeTableView.heightAnchor.constraint(equalToConstant: 300)
]
NSLayoutConstraint.activate(constraints6)
}
func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
return business.count
}
func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
let cell = tableView.dequeueReusableCell(withIdentifier: "business", for: indexPath)
cell.textLabel?.text = business[indexPath.row].businesstype + " " + business[indexPath.row].businessdesc
return cell
}
func tableView(_ tableView: UITableView, heightForRowAt indexPath: IndexPath) -> CGFloat {
return 40.0
}
func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath) {
businessTypeTextField.text = business[indexPath.row].businesstype
businessTypeTextField.resignFirstResponder()
}
@objc func businessTypeEditingDidBegin(sender: UITextField){
tempBusiness = business
businessTypeTableView.isHidden = false
}
@objc func businessTypeEditingDidEnd(sender: UITextField){
business = tempBusiness
businessTypeTableView.isHidden = true
}
@objc func businessTypeEditingChanged(sender: UITextField){
business = business.filter{
business in return business.businesstype.trimmingCharacters(in: .whitespaces).lowercased().contains(sender.text!.lowercased())
}
if(sender.text == ""){
business = tempBusiness
}
businessTypeTableView.reloadData()
}
}
```

# 7. Create an Expanding Text View UI Object

UI text view by default has a fixed size, which can fit a few rows of texts. One can extend the text view size by setting the height and the width according to your app requirement. In this chapter, we will learn how to dynamically expand and contract a text view based on the text length. This feature can come handy when the text that the user will input is of variable length and you want to occupy the limited screen space based on how much the user provides as input text.

## Defining Class and Class Objects

In this section of the code blocks, we will define the class and some necessary UI objects to create our expanding text field functionalities.

```
class ChatDetailViewController: UIViewController, UITextViewDelegate {
```

In the code above, we are defining a UI view controller class, which extends the following two delegates:

*   **UIViewController** to provide all view controller lifecycle methods to the class

*   **UITextViewDelegate** to add UI text view built in functions to manipulate the text view

```
let shellTextView: UITextView = {
let textView = UITextView()
textView.translatesAutoresizingMaskIntoConstraints = false
textView.isScrollEnabled = false
textView.font = UIFont.systemFont(ofSize: 17)
textView.layer.borderWidth = 1
textView.layer.borderColor = UIColor.gray.cgColor
textView.layer.cornerRadius = 10
return textView
}()
let shellSaveButton:UIButton = {
let uiButton  = UIButton()
uiButton.translatesAutoresizingMaskIntoConstraints = false
uiButton.setTitleColor(UIColor.black, for: .normal)
uiButton.contentHorizontalAlignment = .left
uiButton.contentVerticalAlignment = .top
uiButton.titleLabel?.font = UIFont.systemFont(ofSize: 16, weight: .semibold)
uiButton.isEnabled = false
return uiButton
}()
```

The above code defines a UI text view and a UI button. As you can see from the name, this is a shell text view and a shell button, which we are using just for display purposes. We will replace this with our programmatically expanding/contracting text view. The reason we must do this is because we cannot replace an expanding text view back to the original size post saving the content. This is a technical display glitch in Xcode, which I hope will be taken care of in the future iOS versions.

```
let chatTextView: UITextView = {
let textView = UITextView()
textView.translatesAutoresizingMaskIntoConstraints = false
textView.isScrollEnabled = false
textView.font = UIFont.systemFont(ofSize: 17)
textView.layer.borderWidth = 1
textView.layer.borderColor = UIColor.gray.cgColor
textView.layer.cornerRadius = 10
return textView
}()
let saveButton:UIButton = {
let uiButton  = UIButton()
uiButton.translatesAutoresizingMaskIntoConstraints = false
uiButton.setTitleColor(UIColor.black, for: .normal)
uiButton.contentHorizontalAlignment = .left
uiButton.contentVerticalAlignment = .top
uiButton.titleLabel?.font = UIFont.systemFont(ofSize: 16, weight: .semibold)
uiButton.isEnabled = false
return uiButton
}()
```

The above code defines a UI text view and a UI button, which we will expand the contract using code. Note that we are defining some basic properties such as font size, border width, corner radius (to give a curve to the corners of the text view), and border color to the text view. And similarly, for the button, we are setting the button title color, the text within the button horizontal and vertical position, and its font.

Although we are defining a button, we will not use the button in our code demonstration as it is not relevant for the functionality we are demonstrating in this chapter.

## Lifecycle Methods

The code blocks below have the **viewDidLoad** lifecycle method and the screen drawing custom method.

```
override func viewDidLoad() {
super.viewDidLoad()
drawScreen ()
}
```

**viewDidLoad**() is called when the view controller is first loaded. From this system-defined method, we are invoking a custom method **drawScreen**(), which is described in the next block.

```
func drawScreen(){
let screensize: CGRect = UIScreen.main.bounds
let screenWidth = screensize.width
let screenHeight = screensize.height
shellTextView.delegate = self
view.addSubview(shellTextView)
NSLayoutConstraint.activate([
shellTextView.bottomAnchor.constraint(equalTo: view.bottomAnchor, constant: -40),
shellTextView.leftAnchor.constraint(equalTo: view.leftAnchor, constant: 5),
shellTextView.widthAnchor.constraint(equalToConstant: screenWidth - 40),
shellTextView.heightAnchor.constraint(equalToConstant: 40)
])
view.addSubview(shellSaveButton)
let constraints3 = [
shellSaveButton.topAnchor.constraint(equalTo: view.bottomAnchor, constant: -70),
shellSaveButton.leftAnchor.constraint(equalTo: shellTextView.rightAnchor, constant: 5),
shellSaveButton.rightAnchor.constraint(equalToSystemSpacingAfter: shellTextView.rightAnchor, multiplier: 40),
shellSaveButton.heightAnchor.constraint(equalToConstant: 40)
]
NSLayoutConstraint.activate(constraints3)
shellSaveButton.setImage(UIImage(systemName: "paperplane.fill"), for: .normal)
}
```

In the above code, we are drawing the **shellTextView** text view toward the bottom of the screen and the save button right next to the **shellTextView**.

## TextView Methods

There are two methods we have in this section. The first one is called when the user starts editing the text view, and the second one expands and contracts the text view based on the text length.

```
func textViewDidBeginEditing(_ textView: UITextView) {
let screensize: CGRect = UIScreen.main.bounds
let screenWidth = screensize.width
shellTextView.removeConstraints(shellTextView.constraints)
shellTextView.removeFromSuperview()
shellSaveButton.removeConstraints(shellSaveButton.constraints)
shellSaveButton.removeFromSuperview()
view.addSubview(chatTextView)
NSLayoutConstraint.activate([
chatTextView.bottomAnchor.constraint(equalTo: view.bottomAnchor, constant: -340),
chatTextView.leftAnchor.constraint(equalTo: view.leftAnchor, constant: 5),
chatTextView.widthAnchor.constraint(equalToConstant: screenWidth - 40),
chatTextView.heightAnchor.constraint(equalToConstant: 40)
])
chatTextView.becomeFirstResponder()
// Set up a notification observer to handle text changes
NotificationCenter.default.addObserver(self, selector: #selector(handleTextChange), name: UITextView.textDidChangeNotification, object: chatTextView)
view.addSubview(saveButton)
let constraints3 = [
saveButton.topAnchor.constraint(equalTo: view.bottomAnchor, constant: -370),
saveButton.leftAnchor.constraint(equalTo: chatTextView.rightAnchor, constant: 5),
saveButton.rightAnchor.constraint(equalToSystemSpacingAfter: chatTextView.rightAnchor, multiplier: 40),
saveButton.heightAnchor.constraint(equalToConstant: 40)
]
NSLayoutConstraint.activate(constraints3)
saveButton.setImage(UIImage(systemName: "paperplane.fill"), for: .normal)
saveButton.addTarget(self, action: #selector(saveChatAction), for: .touchUpInside)
}
```

The above method is a system-defined method, which will be invoked when the user begins typing into the text view. Please note that this will be only called when the class extends the **UITextViewDelegate**, as explained before. The following are the key call outs of this function:

*   We remove the shellTextView and the shellSaveButton.

*   Next, we are drawing the actual text view and the button, chatTextView and saveButton at the exact position where the shellTextView and the shellSaveButton are.

*   We are also defining the touch up inside event for the button, which will call the saveChatAction method.

```
@objc private func handleTextChange() {
if(chatTextView.text.trimmingCharacters(in: .whitespaces).count == 0){
saveButton.isEnabled = false
}else{
saveButton.isEnabled = true
}
let size = CGSize(width: UIScreen.main.bounds.width - 30, height: .infinity)
let estimatedSize = chatTextView.sizeThatFits(size)
chatTextView.constraints.forEach { (constraint) in
if constraint.firstAttribute == .height {
constraint.constant = estimatedSize.height
}
}
}
```

The above code is a system-defined method, which is called every time a user adds or deletes a character. There are a few things happening in this block of code as explained below:

*   If a user has typed a character other than a blank space, the save button is enabled. If user types and backspace to delete the typed character, the save button is again disabled. This is done to make sure user doesn’t accidently save a blank string.

*   Next, we are defining a size object, which is the width of the screen. Thirty pixels are subtracted, which is the width of the save button. Essentially, we are defining initial size as the original size of the displayed text view, which can infinitely grow.

*   Next, we are defining the estimated size of the new text view based on the length of user-provided text. This is drawn from the system-defined function called **sizeThatFits** that is available for any text view.

*   Finally, we are dynamically changing the height constraint of our **chatTextView** by iterating through all constraints and looking for the height constraint. Once found, we are assigning the **chatTextView** height as the **estimatedSize** object height.

## Button Method

This is the system-defined event touch up inside. We have already set saveChatAction() as the target function when user clicks on the button.

```
@objc func saveChatAction(sender: UIButton){
}
```

This function is intentionally left blank. You can implement it based on your application needs, like saving the text to a persistent database.

## Complete Code of TextView Expand

Below is the complete code of the text view expanding function as discussed in the section above.

```
class ChatDetailViewController: UIViewController, UITextViewDelegate {
let shellTextView: UITextView = {
let textView = UITextView()
textView.translatesAutoresizingMaskIntoConstraints = false
textView.isScrollEnabled = false
textView.font = UIFont.systemFont(ofSize: 17)
textView.layer.borderWidth = 1
textView.layer.borderColor = UIColor.gray.cgColor
textView.layer.cornerRadius = 10
return textView
}()
let shellSaveButton:UIButton = {
let uiButton  = UIButton()
uiButton.translatesAutoresizingMaskIntoConstraints = false
uiButton.setTitleColor(UIColor.black, for: .normal)
uiButton.contentHorizontalAlignment = .left
uiButton.contentVerticalAlignment = .top
uiButton.titleLabel?.font = UIFont.systemFont(ofSize: 16, weight: .semibold)
uiButton.isEnabled = false
return uiButton
}()
let chatTextView: UITextView = {
let textView = UITextView()
textView.translatesAutoresizingMaskIntoConstraints = false
textView.isScrollEnabled = false
textView.font = UIFont.systemFont(ofSize: 17)
textView.layer.borderWidth = 1
textView.layer.borderColor = UIColor.gray.cgColor
textView.layer.cornerRadius = 10
return textView
}()
let saveButton:UIButton = {
let uiButton  = UIButton()
uiButton.translatesAutoresizingMaskIntoConstraints = false
uiButton.setTitleColor(UIColor.black, for: .normal)
uiButton.contentHorizontalAlignment = .left
uiButton.contentVerticalAlignment = .top
uiButton.titleLabel?.font = UIFont.systemFont(ofSize: 16, weight: .semibold)
uiButton.isEnabled = false
return uiButton
}()
override func viewDidLoad() {
super.viewDidLoad()
drawScreen ()
}
func drawScreen(){
let screensize: CGRect = UIScreen.main.bounds
let screenWidth = screensize.width
let screenHeight = screensize.height
shellTextView.delegate = self
view.addSubview(shellTextView)
NSLayoutConstraint.activate([
shellTextView.bottomAnchor.constraint(equalTo: view.bottomAnchor, constant: -40),
shellTextView.leftAnchor.constraint(equalTo: view.leftAnchor, constant: 5),
shellTextView.widthAnchor.constraint(equalToConstant: screenWidth - 40),
shellTextView.heightAnchor.constraint(equalToConstant: 40)
])
view.addSubview(shellSaveButton)
let constraints3 = [
shellSaveButton.topAnchor.constraint(equalTo: view.bottomAnchor, constant: -70),
shellSaveButton.leftAnchor.constraint(equalTo: shellTextView.rightAnchor, constant: 5),
shellSaveButton.rightAnchor.constraint(equalToSystemSpacingAfter:shellTextView.rightAnchor,  multiplier: 40),
shellSaveButton.heightAnchor.constraint(equalToConstant: 40)
]
NSLayoutConstraint.activate(constraints3)
shellSaveButton.setImage(UIImage(systemName: "paperplane.fill"), for: .normal)
}
func textViewDidBeginEditing(_ textView: UITextView) {
let screensize: CGRect = UIScreen.main.bounds
let screenWidth = screensize.width
shellTextView.removeConstraints(shellTextView.constraints)
shellTextView.removeFromSuperview()
shellSaveButton.removeConstraints(shellSaveButton.constraints)
shellSaveButton.removeFromSuperview()
view.addSubview(chatTextView)
NSLayoutConstraint.activate([
chatTextView.bottomAnchor.constraint(equalTo: view.bottomAnchor, constant: -340),
chatTextView.leftAnchor.constraint(equalTo: view.leftAnchor, constant: 5),
chatTextView.widthAnchor.constraint(equalToConstant: screenWidth - 40),
chatTextView.heightAnchor.constraint(equalToConstant: 40)
])
chatTextView.becomeFirstResponder()
// Set up a notification observer to handle text changes
NotificationCenter.default.addObserver(self, selector: #selector(handleTextChange), name: UITextView.textDidChangeNotification, object: chatTextView)
view.addSubview(saveButton)
let constraints3 = [
saveButton.topAnchor.constraint(equalTo: view.bottomAnchor, constant: -370),
saveButton.leftAnchor.constraint(equalTo: chatTextView.rightAnchor, constant: 5),
saveButton.rightAnchor.constraint(equalToSystemSpacingAfter: chatTextView.rightAnchor, multiplier: 40),
saveButton.heightAnchor.constraint(equalToConstant: 40)
]
NSLayoutConstraint.activate(constraints3)
saveButton.setImage(UIImage(systemName: "paperplane.fill"), for: .normal)
saveButton.addTarget(self, action: #selector(saveChatAction), for: .touchUpInside)
}
@objc private func handleTextChange() {
if(chatTextView.text.trimmingCharacters(in: .whitespaces).count == 0){
saveButton.isEnabled = false
}else{
saveButton.isEnabled = true
}
let size = CGSize(width: UIScreen.main.bounds.width - 30, height: .infinity)
let estimatedSize = chatTextView.sizeThatFits(size)
chatTextView.constraints.forEach { (constraint) in
if constraint.firstAttribute == .height {
constraint.constant = estimatedSize.height
}
}
}
@objc func saveChatAction(sender: UIButton){
}
}
```

# 8. Animated View

Animations are a fundamental part of iOS programming and help improve user experience as within your app user transitions between views. Based on the type of animation, it would also provide clarity to the user by communicating the user about their action.

In the example below, we will demonstrate animation using the system-defined function **animate**. We will draw a view controller with a button on it. When the user taps the button, it will open another view, which will animate as it displays on the screen.

## Define the Main View Controller

```
class firstViewController: UIViewController {
```

The code above defines a UIViewController with the name **firstViewController**

## Define the UI Objects

```
private let animatedView: UIView = {
let view = UIView()
view.translatesAutoresizingMaskIntoConstraints = false
view.backgroundColor = .systemGray6
view.alpha = 0.0
view.center = view.center
return view
}()
```

The code above defines a custom view which will be animated to demonstrate the animated feature. We are making the background of the view gray and putting the view in the center of the current view controller.

```
let viewButton:UIButton = {
let uiButton  = UIButton()
uiButton.translatesAutoresizingMaskIntoConstraints = false
uiButton.setImage(UIImage(systemName: "doc.viewfinder.fill"), for: .normal)
return uiButton
}()
```

Next, we are defining the button. Upon tapping the button, we will open the custom view with animation. Button is displayed as an image using a system-defined image called **doc.viewfinder.fill**.

## Define Lifecycle Methods

```
override func viewWillAppear(_ animated: Bool) {
super.viewWillAppear(true)
setup()
drawAnimation()
}
```

The above-defined method **viewWillAppear** is a system function classified as a lifecycle method of **UIViewController**. Unlike **viewDidLoad**, which is only called once per app session, **viewWillAppear** is called every time whenever the view controller is invoked.

We are calling two custom methods from this method. The first method is **setup**() to initialize a few elements before the view controller is displayed and the **drawAnimation**() function, which will draw the button on the screen.

```
setup(){
viewButton.addTarget(self, action: #selector(animateView), for: .touchUpInside)
}
```

In the above method, we are adding a custom target function to the button **viewButton** called **animateView**, which will be invoked when the user taps the button ( **touchUpInside event**).

```
func drawAnimation(){
let screensize: CGRect = UIScreen.main.bounds
let screenWidth = screensize.width
let screenHeight = screensize.height
view.addSubview(viewButton)
let constraints = [
viewButton.topAnchor.constraint(equalTo: view.topAnchor, constant: 25),
viewButton.leftAnchor.constraint(equalTo: profileImageView.rightAnchor, constant: 5),
viewButton.widthAnchor.constraint(equalToConstant: 80),
viewButton.heightAnchor.constraint(equalToConstant: 80)
]
NSLayoutConstraint.activate(constraints)
}
```

The code above draws the button on the screen. The target action is already defined for the button. Upon user pressing on the button, the animated view would open with the preset animation.

## Animate View

```
@objc func animateView(sender: UIButton){
let screensize: CGRect = UIScreen.main.bounds
let screenWidth = screensize.width
let screenHeight = screensize.height
view.addSubview(animatedView)
let constraints = [
animatedView.topAnchor.constraint(equalTo: view.topAnchor, constant: 20),
animatedView.leftAnchor.constraint(equalTo: view.leftAnchor, constant: 20),
animatedView.widthAnchor.constraint(equalToConstant: screenWidth - 20),
animatedView.heightAnchor.constraint(equalToConstant: screenHeight - 20)
]
NSLayoutConstraint.activate(constraints)
UIView.animate(withDuration: 1.0, delay: 0.0, options: .curveEaseOut, animations: { [self] in
animatedView.alpha = 1.0
}, completion: nil)
}
```

The code above is called when the user taps on the view button. The code will do the following:

*   Add the custom **UIView animatedView** on the **UIViewController**. We are making the **animatedView** about the same size as the view controller leaving 20 pixels from all sides.

*   Immediately after that, we are calling on the system-defined animate method on the **UIView**. The effect we are applying is curve ease out for a duration of 1 sec with no delay, and in the completion block, we are setting the transparency to 1.0\. This will bring the **animatedView** with a zoom in effect. To learn more about the **UIView** animate method, refer to the next section.

## UIView.animate Function Details

UIView.animate has several attributes that you can use to control and set the animation behavior you desire for your specific animation. The key attributes are listed below.

### Duration (TimeInterval)

A mandatory parameter specifying the total time the animation will take in seconds. It's a TimeInterval value, which is a double-precision floating-point number representing a duration in seconds.

### Animations

@escaping () -> Void: This is also a mandatory closure containing the actual animation code. Inside this closure, you modify the properties of the view you want to animate. Common properties to change include frame (position and size), alpha (transparency), center, transform (for rotations and scaling), and background color.

### Delay (TimeInterval)

This optional parameter specifies a time delay in seconds before the animation starts. It's a TimeInterval value, which is a double-precision floating-point number representing a duration in seconds.

### Options

UIView.AnimationOptions: This optional parameter allows you to configure various aspects of the animation. It's a set of flags from the UIView.AnimationOptions enumeration. Here are some common options:

*   **.curveEaseInOut:** Defines an ease-in-ease-out easing curve for the animation (smooth start and end)

*   **.curveEaseIn:** Defines an ease-in easing curve (starts slow and speeds up)

*   **.curveEaseOut:** Defines an ease-out easing curve (starts fast and slows down)

*   **.repeat:** Makes the animation repeat a certain number of times (specify count with repeatCount)

*   **.autoreverse:** Reverses the animation after it completes (playing it backward)

*   **.beginFromCurrentState:** Starts the animation from the view's current state instead of its starting state in the storyboard

### Completion: ((Bool) -> Void)?

This optional closure gets called when the animation finishes. It takes a Boolean parameter indicating whether the animation completed successfully or was interrupted. You can use this closure for any postanimation actions you might need.

## Complete Code

```
class firstViewController: UIViewController {
private let animatedView: UIView = {
let view = UIView()
view.translatesAutoresizingMaskIntoConstraints = false
view.backgroundColor = .systemGray6
view.alpha = 0.0
view.center = view.center
return view
}()
let viewButton:UIButton = {
let uiButton  = UIButton()
uiButton.translatesAutoresizingMaskIntoConstraints = false
uiButton.setImage(UIImage(systemName: "doc.viewfinder.fill"), for: .normal)
return uiButton
}()
override func viewWillAppear(_ animated: Bool) {
super.viewWillAppear(true)
setup()
drawAnimation()
}
setup(){
viewButton.addTarget(self, action: #selector(animateView), for: .touchUpInside)
}
func drawAnimation(){
let screensize: CGRect = UIScreen.main.bounds
let screenWidth = screensize.width
let screenHeight = screensize.height
view.addSubview(viewButton)
let constraints = [
viewButton.topAnchor.constraint(equalTo: view.topAnchor, constant: 25),
viewButton.leftAnchor.constraint(equalTo: profileImageView.rightAnchor, constant: 5),
viewButton.widthAnchor.constraint(equalToConstant: 80),
viewButton.heightAnchor.constraint(equalToConstant: 80)
]
NSLayoutConstraint.activate(constraints)
}
@objc func animateView(sender: UIButton){
let screensize: CGRect = UIScreen.main.bounds
let screenWidth = screensize.width
let screenHeight = screensize.height
view.addSubview(animatedView)
let constraints = [
animatedView.topAnchor.constraint(equalTo: view.topAnchor, constant: 20),
animatedView.leftAnchor.constraint(equalTo: view.leftAnchor, constant: 20),
animatedView.widthAnchor.constraint(equalToConstant: screenWidth - 20),
animatedView.heightAnchor.constraint(equalToConstant: screenHeight - 20)
]
NSLayoutConstraint.activate(constraints)
UIView.animate(withDuration: 1.0, delay: 0.0, options: .curveEaseOut, animations: { [self] in
animatedView.alpha = 1.0
}, completion: nil)
}
}
```

# 9. Saving Records to Multiple Tables

Often based on your app data persistent design, you must save multiple records to many tables in one transaction. As the iCloud callbacks are asynchronous, if you save one record at a time, it becomes a cumbersome process, for you need to wait for the first record save option to complete before you can invoke the next one.

Fortunately, iOS provides a smart way to handle this whereby you can use one block statement to save multiple records in a many-to-many relationship. In the example below, we will save.

## Example Outline

We are taking a multiuser chat as an example to demonstrate records persisting in multiple iCloud tables. Our example assumes the user will have two profiles. The first one is a business profile visible to all, and the second is a private profile only visible to the user. Also, we will have a common chat table which is available to all users. We will save the business profile of the user and the chat conversation in two separate public databases and the personal profile of the user in a private database.

Please note this example assumes the interface and all the logic for chat creation is existing (in Chapter [11](#533539_2_En_11_Chapter.xhtml), we will learn a bidirectional chat creation example) and only focuses on the multiple iCloud table persistence. The function assumes the necessary inputs are provided to execute the save operation.

### Class Definition

In the code below, we are defining a chat class of NSObject. It imports the UIKit and CloudKit libraries to ensure all necessary functions are available to execute the operation.

```
import UIKit
import CloudKit
class Chat: NSObject {
```

### Save Function Definition

We are now defining a function called **saveRecordsToCloud**, which will save three records to three different tables. The function takes the business profile, personal profile, and chat object as input. The function assumes these custom classes are predefined.

Since we are going to save the record to an iCloud persistence database, the function also initializes the default container of the iCloud in the **container** variable and the public and private databases in **publicDatabase** and **privateDatabase** variable, respectively.

```
func saveRecordsToCloud(business:businessprofile, personal:personalProfile, chat:chat){
let container = CKContainer.default()
let publicDatabase = container.publicCloudDatabase
let privateDatabase = container.privateCloudDatabase
```

### Table Record Initialization

In this section, we will initialize the record types with record values as provided to the function.

Below code initializes the **businessprofile** record type with the user provided values.

We are first defining the record type using the **CKRecord Cloudkit** function. In this example, **businessprofile** is the record type. If the record type doesn’t exist, the operation will create it for the first time, and if it exists, it will append the new record to the table.

The record has three parameters: name of the business(**businessname**), an image of the business (**businessprofile**), and the description of the business(**businessdesc**).

Please note for the image to be stored in the database we need to extract the binaries using **fileURL** function of the image and pass it to the **CKAsset** Type.

```
let businessRecord = CKRecord(recordType: "businessproflle")
businessRecord["businessname"] = business. businessname
businessRecord["businessprofile"] = CKAsset(fileURL: business.businessprofile.fileURL!)
businessRecord["businessdesc"] = business.businessdesc
```

This is the next block of code for defining the chat table record as chat. The record has three values: an originator ID indicating who created the chat (**originatorid**), the actual chat text (**chattext**), and the date of creation (**datecreated**), which is the default system date and time.

```
let chatRecord = CKRecord(recordType: "\chat")
chatRecord["originatorid"] = chat.originatorid
chatRecord["chattext"] = chat.chattext
chatRecord["datecreated"] = Date()
```

This is the third and final block of record definition. The record type is **personalprofile**. This record will be stored in a private database. Three values are assigned to the record: the profile image of the user, the name of the user, and the chat text, which will be the latest chat in the chat trail.

```
let personalRecord = CKRecord(recordType: "personalprofile")
personalRecord[profile"] = CKAsset(fileURL: personal.profile.fileURL!)
personalRecord["name"] = personal.name
personalChatRecord["lastchat"] = chat.chattext
```

### Save Operation

In the below code, we are defining our public database save operation. The variable name is **publicSaveOperation** (notice it is a **let** variable as we are only going to use it once), which uses the **CloudKit CKModifyRecordsOperation** system function. The method takes an array of records to be saved as input for its parameter labeled as **recordsToSave**.

We are providing both public database records to this operation, **businessRecord** and **chatRecord**.

```
let publicSaveOperation = CKModifyRecordsOperation(recordsToSave: [businessRecord, chatRecord])
publicSaveOperation.savePolicy = .changedKeys
```

Next, we are asynchronously waiting for the operations to complete. **modifyRecordsResultBlock** is available in the new iOS release, which provides a result **enum** (refer to Chapter [2](#533539_2_En_2_Chapter.xhtml)).

If the operation is a success, we now want to save the user profile to the user’s private database. Notice that **recordsToSave** can take an array; however, in our case, we are only providing **personalRecord** as the only record to be saved.

We are asynchronously waiting again for the private database operation to complete. This time if it is a success, we are exiting the function (**self.group.leave()**). In case of failure, we are printing a message on the console.

**privateDatabase.add(privateSaveOperation)** and **publicDatabase.add(publicSaveOperation)** will queue the function to execute in the background.

```
publicSaveOperation.modifyRecordsResultBlock = { result in
switch result {
case .success:
self.syncQueue.async {
let privateSaveOperation = CKModifyRecordsOperation(recordsToSave: [personalRecord])
privateSaveOperation.savePolicy = .changedKeys
privateSaveOperation.modifyRecordsResultBlock = { result in
switch result {
case .success:
self.syncQueue.async {
self.group.leave()
}
case .failure(let error):
print("Error saving record to private database: \(error.localizedDescription)")
// Handle the error case
}
}
privateDatabase.add(privateSaveOperation)
}
// Handle any further actions or UI updates
case .failure(let error):
print("Error saving records: \(error.localizedDescription)")
// Handle the error case
}
}
publicDatabase.add(publicSaveOperation)
}
```

## Complete Code

```
import UIKit
import CloudKit
class Chat: NSObject {
func saveRecordsToCloud(business:businessprofile, personal:personalProfile, chat:chat){
let container = CKContainer.default()
let publicDatabase = container.publicCloudDatabase
let privateDatabase = container.privateCloudDatabase
let businessRecord = CKRecord(recordType: "businessproflle")
businessRecord["businessname"] = business. businessname
businessRecord["businessprofile"] = CKAsset(fileURL: business.businessprofile.fileURL!)
businessRecord["businessdesc"] = business.businessdesc
let chatRecord = CKRecord(recordType: "\chat")
chatRecord["originatorid"] = chat.originatorid
chatRecord["chattext"] = chat.chattext
chatRecord["datecreated"] = Date()
let personalRecord = CKRecord(recordType: "personalprofile")
personalRecord[profile"] = CKAsset(fileURL: personal.profile.fileURL!)
personalRecord["name"] = personal.name
personalChatRecord["lastchat"] = chat.chattext
let publicSaveOperation = CKModifyRecordsOperation(recordsToSave: [businessRecord, chatRecord])
publicSaveOperation.savePolicy = .changedKeys
publicSaveOperation.modifyRecordsResultBlock = { result in
switch result {
case .success:
self.syncQueue.async {
let privateSaveOperation = CKModifyRecordsOperation(recordsToSave: [personalRecord])
privateSaveOperation.savePolicy = .changedKeys
privateSaveOperation.modifyRecordsResultBlock = { result in
switch result {
case .success:
self.syncQueue.async {
self.group.leave()
}
case .failure(let error):
print("Error saving record to private database: \(error.localizedDescription)")
// Handle the error case
}
}
privateDatabase.add(privateSaveOperation)
}
// Handle any further actions or UI updates
case .failure(let error):
print("Error saving records: \(error.localizedDescription)")
// Handle the error case
}
}
publicDatabase.add(publicSaveOperation)
}
}
```

# 10. Push Notifications

Push notifications are an essential part of your application. At times when the state of your application changes, you would like to notify the user on the change so that the user can take appropriate action. In our chat application, when a user sends a text to another user, notification will help the other user to respond to the change or take appropriate action.

## App Delegate Registration

The first step to implement push notifications is to register for notifications. The iCloud iOS toolkit offers a database subscription method, using which we can register for a subscription with a unique identifier. Once persisted and permitted to provide notification, it will enable the push notifications for the application.

Open your App Delegate class and add the following function to the class. The method does the following:

*   Takes reference of the default container public database

    ```
    let container = CKContainer.default()
    let publicDatabase = container.publicCloudDatabase
    let subscription = CKDatabaseSubscription(subscriptionID: "mySubscription")
    ```

*   Creates a subscription variable of type **CKDatabaseSubscription** with a name “**mySubscription**”

    ```
    let subscription = CKDatabaseSubscription(subscriptionID: "mySubscription")
    ```

*   Create a notification info object and setting the property shouldSendContentAvailable to true

    ```
    let notificationInfo = CKSubscription.NotificationInfo()
    notificationInfo.shouldSendContentAvailable = true
    ```

*   Assigning the notification info object to the subscription variable

*   Updating the database with the subscription object

    ```
    let operation = CKModifySubscriptionsOperation(subscriptionsToSave: [subscription], subscriptionIDsToDelete: [])
    operation.modifySubscriptionsResultBlock = { result in
    switch result {
    case .success:
    print("Succesfully Subscribed")
    case .failure(let error):
    print("Error registering for notifications: \(error.localizedDescription)")
    }
    }
    publicDatabase.add(operation)
    }
    ```

```
subscription.notificationInfo = notificationInfo
```

```
func registerForNotifications() {
let container = CKContainer.default()
let publicDatabase = container.publicCloudDatabase
let subscription = CKDatabaseSubscription(subscriptionID: "mySubscription")
let notificationInfo = CKSubscription.NotificationInfo()
notificationInfo.shouldSendContentAvailable = true
subscription.notificationInfo = notificationInfo
let operation = CKModifySubscriptionsOperation(subscriptionsToSave: [subscription], subscriptionIDsToDelete: [])
operation.modifySubscriptionsResultBlock = { result in
switch result {
case .success:
print("Succesfully Subscribed")
case .failure(let error):
print("Error registering for notifications: \(error.localizedDescription)")
}
}
publicDatabase.add(operation)
}
```

## Setting Subscription

Next, we need to set a subscription for the change. You can add the below method to any modal class after the record update or creation. In our example, we will add this to our chat model class. The function performs the following:

*   Get access to the container and the public database of the iCloud environment.

    ```
    let container = CKContainer.default()
    let publicDatabase = container.publicCloudDatabase
    ```

*   Create a query subscription. Notice that the query subscription needs the record type name and the subscription name (the name must be like what you have created before and options which tells the system that notification needs to be provided upon record creation or record update on this record type). Also, note you can provide a condition to your query as well. In our case, we are applying it to all records; hence, the NSPredicate value is True.

    ```
    let subscription = CKQuerySubscription(
    recordType: "\(tablename)",
    predicate: NSPredicate(value: true),
    subscriptionID: "mySubscription",
    options: [.firesOnRecordCreation, .firesOnRecordUpdate]
    )
    ```

*   Create notification info with customized text. This text will appear on the user's screen.

    ```
    let notificationInfo = CKSubscription.NotificationInfo()
    notificationInfo.alertBody = "You have a New Chat"
    subscription.notificationInfo = notificationInfo
    ```

*   Finally, save the subscription.

    ```
    publicDatabase.save(subscription) { (subscription, error) in
    if let error = error {
    print("Error saving subscription: \(error.localizedDescription)")
    self.group.leave()
    } else {
    print("Subscription saved successfully!")
    self.group.leave()
    }
    }
    }
    ```

This will trigger the notification process. All the users who are subscribed to the record type will get an alert as specified in the notification object.

```
func setSubscription(tablename: String, chattext:String,originatorname:String){
// set up a CKSubscription for the database table
let container = CKContainer.default()
let publicDatabase = container.publicCloudDatabase
let subscription = CKQuerySubscription(
recordType: "\(tablename)",
predicate: NSPredicate(value: true),
subscriptionID: "mySubscription",
options: [.firesOnRecordCreation, .firesOnRecordUpdate]
)
let notificationInfo = CKSubscription.NotificationInfo()
notificationInfo.alertBody = "You have a New Chat"
subscription.notificationInfo = notificationInfo
publicDatabase.save(subscription) { (subscription, error) in
if let error = error {
print("Error saving subscription: \(error.localizedDescription)")
self.group.leave()
} else {
print("Subscription saved successfully!")
self.group.leave()
}
}
}
```

# 11. Conversation Between User Metadata

We have learned in the previous chapters about MVC architecture, iCloud, new iOS block routines to manage asynchronous calls, image manipulation, and many other concepts. In this chapter, we will put all these concepts together to create a user chat app. The chat program will have the following feature:

*   Ability to create user contact. We will be able to create a contact, store it in iCloud repository, and display the contact on the app screen.

*   Launch a conversation with a particular contact. We will be able to converse with a user from our contact real time. Persist conversations in a database, and retrieve past conversation.

*   For keeping the program simple, we will keep all conversations in a public database and use a unique table name as a concept to find out whose conversations we need to retrieve.

*   Please note that we are not performing end-to-end encryption, or secure channel for privacy. Neither are we focusing on displaying and storing image or videos. The intent of the program is to show how the concepts learned before can be applied to create real-life applications.

## iCloud Database

There are four tables we need to create in the public database as described below.

### Profile Record Type

This record type will store all contacts a user will have to initiate conversations. The definition of the record type is given below:

| Field name | Field type | Description |
| --- | --- | --- |
| userid | String | Unique ID to identify the user |
| personalprofile | CKAsset | Contact profile image |
| name | String | Name of the contact |
| businessname | String | User business name |
| businesstype | String | Type of business |

In the iCloud repository, the profile record type will look like the below screen.

![](images/533539_2_En_11_Chapter/533539_2_En_11_Figa_HTML.jpg)

### Chattable Record Type

Chattable record type will store the name of the table where user conversation is stored. Please note that between the app user and the selected contact, the conversation is stored in a unique table. The name of the table is stored as a reference here to uniquely identify the conversation.

| Field name | Field type | Description |
| --- | --- | --- |
| chattablename | String | Unique chat table which will store the conversation between the user and a contact |
| receipentprofile | CKAsset | Profile image of the contact. Note that we could have stored the reference here, but for simplicity, we are duplicating the image of the contact. This will be shown next to the contact conversation |
| receipentname | String | Name of the contact whose chat will be displayed |
| lastchat | String | The last chat text details. This will be shown next to the contact’s name |

In the iCloud repository, the chattable record type will look like the below screen.

![](images/533539_2_En_11_Chapter/533539_2_En_11_Figb_HTML.jpg)

### Conversation Record Type

This record type will store the chat conversation between the app user and a contact. The name of this table is stored in the chattable defined above.

| Field name | Field type | Description |
| --- | --- | --- |
| chattext | String | This field will store the chat texts |
| originatorid | CKAsset | The ID of the user who created the text |

In the iCloud repository, the conversation record type will look like the below screen. Note that there will be multiple conversation record types based on the unique conversation between users. The nomenclature of the table name is based on the username of the chat initiator + “_” + “the name of the business type” of the target chat user.

![](images/533539_2_En_11_Chapter/533539_2_En_11_Figc_HTML.jpg)

### Business Record Type

Every user business will have one table, uniquely named after the business name. This table will store all the conversation table name, which in turn stores the unique conversation between the business and the other user.

| Field name | Field type | Description |
| --- | --- | --- |
| chattablename | String | Unique chat table which will store the conversation between the user and a contact |
| originatorprofile | CKAsset | Profile image of the contact. Note that we could have stored the reference here, but for simplicity, we are duplicating the image of the contact. This will be shown next to the contact conversation |
| originatorname | String | Name of the originator of conversation |
| lastchat | String | The last chat conversation with the user for whom we have the chattablename created |

In the iCloud repository, the business record type will look like the below screen. Note that there will be one business record type per user. The nomenclature of the table name is based on unique business name of the user.

![](images/533539_2_En_11_Chapter/533539_2_En_11_Figd_HTML.jpg)

Although you can choose to do it, you don’t have to create the tables manually in the iCloud repository. Once you do your iCloud configuration on your project (we have learned this in the Part I of this book), the tables are created automatically upon the first save request.

# 12. Conversation Between Users – Modal Definition

## Define the Modal Classes

To mimic the above tables, we would need our conversational app to create the respective modal class. Often the modal class has a one-to-one relationship with an iCloud table, but at times, we have also had a one-to-many relationship. We will explore this in detail in the section below. Each class will have respective CRUD functions to achieve the iCloud operations such as creation of records and retrieval of records.

### Profile Modal Class

The first modal class we are coding is the user profile. The code below confirms the **UIKit** (default framework for UI definition) and the **CloudKit** (this is required for iCloud operations) modules and defines a custom class name Profile of type **NSObject**.

```
import UIKit
import CloudKit
class Profile: NSObject {
```

In this class, we will perform many **iCloud** asynchronous operations. To run the code on a thread and wait for the method operations to complete, we are defining a dispatch group and a synchronization queue.

We are also defining a class level object named **profile** of type **Profile** (notice the case-sensitive definition).

```
let group = DispatchGroup()
let syncQueue = DispatchQueue(label: "com.domain.app.sections")
var profile:Profile?
```

The variables defined below mimics the profile table in the iCloud. Apart from the custom-defined variables, we are also defining a variable called record, which is of type **CKRecord**; when instantiated, this will have the reference to the record ID.

An **init** method is defined for the custom variables to initialize any class object, which will be the required parameters to instantiate a variable for the class. The **init** method will take in the parameters provided by the user and assign it to the object of the **Profile** class.

```
var record:CKRecord!
var userid:String!
var personalprofile:CKAsset!
var name: String!
var businessname:String!
var businesstype:String!
init(userid:String, personalprofile:CKAsset, name:String, businessname:String, businesstype:String) {
self.userid = userid
self.personalprofile = personalprofile
self.name = name
self.businessname = businessname
self.businesstype = businesstype
}
```

#### Save Profile Method

This method will save the user profile to the iCloud **profile** table. The method takes a **profile** object as input.

```
func saveProfile(profile:Profile){
```

The profile table is in the iCloud public cloud database. The code below takes reference to the public cloud database, defines a record type with the name **aRecord** of record type **profile**, and assigns the user-provided profile data to the respective fields of the **profile** record (user ID, profile image, username, business name, business type).

```
let publicDatabase = CKContainer.default().publicCloudDatabase
let aRecord = CKRecord(recordType: "profile")
aRecord["userid"] = profile.userid
aRecord["personalprofile"] = profile.personalprofile
aRecord["name"] = profile.name
aRecord["businessname"] = profile.businessname
aRecord["businesstype"] = profile.businesstype
```

The code below now attempts to persist the record to the **profile** table in the iCloud repository. If successful, it returns the saved record, which is stored to the class level variable **profile**. Notice we are waiting on the main thread, and once we receive the record as a return value, we leave the thread and continue with other obligations. In case it is unable to save the record, an error will be displayed on the console.

You may be thinking why we can’t directly assign the value provided by the user to the profile object instead of waiting for the iCloud to provide the same value. This is because once the record is saved, we get the record ID of the saved record, which is critical for performing other CRUD operations such as update, delete, or query.

```
publicDatabase.save(aRecord, completionHandler: { (record, error) -> Void in
DispatchQueue.main.async {
if let error = error {
print(error)
}
else {
self.syncQueue.async {
self.profile = Profile(userid: record!.object(forKey: "userid")! as! String, personalprofile: record!.object(forKey: "personalprofile")! as! CKAsset, name: record!.object(forKey: "name")! as! String, businessname: record!.object(forKey: "businessname")! as! String, businesstype: record!.object(forKey: "businesstype")! as! String)
self.profile?.record = record
self.group.leave()
}
}
}
})
}
}
```

#### Complete Code

The complete code of the **Profile** modal class is given below:

```
import UIKit
import CloudKit
class Profile: NSObject {
let group = DispatchGroup()
let syncQueue = DispatchQueue(label: "com.domain.app.sections")
var profile:Profile?
var record:CKRecord!
var userid:String!
var personalprofile:CKAsset!
var name: String!
var businessname:String!
var businesstype:String!
init(userid:String, personalprofile:CKAsset, name:String, businessname:String, businesstype:String) {
self.userid = userid
self.personalprofile = personalprofile
self.name = name
self.businessname = businessname
self.businesstype = businesstype
}
//MARK: - Save Routines
func saveProfile(profile:Profile){
let publicDatabase = CKContainer.default().publicCloudDatabase
let aRecord = CKRecord(recordType: "profile")
aRecord["userid"] = profile.userid
aRecord["personalprofile"] = profile.personalprofile
aRecord["name"] = profile.name
aRecord["businessname"] = profile.businessname
aRecord["businesstype"] = profile.businesstype
publicDatabase.save(aRecord, completionHandler: { (record, error) -> Void in
DispatchQueue.main.async {
if let error = error {
print(error)
}
else {
self.syncQueue.async {
self.profile = Profile(userid: record!.object(forKey: "userid")! as! String, personalprofile: record!.object(forKey: "personalprofile")! as! CKAsset, name: record!.object(forKey: "name")! as! String, businessname: record!.object(forKey: "businessname")! as! String, businesstype: record!.object(forKey: "businesstype")! as! String)
self.profile?.record = record
self.group.leave()
}
}
}
})
}
}
```

### Business Modal Class

The Business modal class does not represent a table in our iCloud repository. This class is defined to provide all business types as an input to the business type field for the profile class. Currently, we are persisting this as a part of the class (you will see that in the later part of the program). In your implementation, you may choose to persist it in an iCloud table and retrieve it as an input assist table.

The class has three fields: business type, business description, and an image providing a visual depiction of the business type. The **init** method will help initialize the object of the class.

```
import UIKit
class Business: NSObject{
var businesstype:String!
var businessdesc:String!
var imagename:String!
init(businesstype:String, businessdesc:String, imagename:String) {
self.businesstype = businesstype
self.businessdesc = businessdesc
self.imagename = imagename
}
}
```

### Contact Modal Class

Contact modal class does not have a table representation in our iCloud repository. It provides all CRUD functions that we would perform on a **profile** class object.

In the code below, we are defining the Contact class and also importing **CloudKit** for cloud operation besides the default **UIKit** module.

```
import UIKit
import CloudKit
class Contact: NSObject {
```

As we will be performing asynchronous iCloud operations, we are defining the queue and the group. Three more class level variables are defined below:

*   **profiles**, an array of type **Profile**

*   **profileExists**, a Boolean variable indicating whether a profile exists

*   **profile**, an object of type **Profile**

```
let group = DispatchGroup()
let syncQueue = DispatchQueue(label: "com.domain.app.sections")
var profiles:[Profile] = []
var profileExists = false
var profile:Profile?
```

#### Get Contacts Method

This method retrieves all available profiles from the iCloud repository that has the same business type. The method takes the **businessType** as input to perform the following function:

```
func getContacts(businessType:String) {
```

The function stores all profiles in the profiles array defined before. We are emptying the array to avoid duplicate entries in case the function is called twice. We are setting our query predicate by putting a condition to retrieve value only when the field **businesstype** is equal to the user-provided **businesType** value.

The query is then assigned to an **operation** of type **CKQueryOperation** which is then executed on the public cloud database; this is where the profile table exist. We are also setting some operation configuration to timeout if the request is taking more than 10 sec.

```
profiles.removeAll()
let pred = NSPredicate(format: "businesstype == %@", businessType)
let query = CKQuery(recordType: "profile", predicate: pred)
let operation = CKQueryOperation(query: query)
CKContainer.default().publicCloudDatabase.add(operation)
let operationConfiguration = CKOperation.Configuration()
operationConfiguration.timeoutIntervalForRequest = 10
operationConfiguration.timeoutIntervalForResource = 10
operation.configuration = operationConfiguration
```

The **recordMatchedBlock** is invoked once the asynchronous method is complete. It returns a result object, which is an Enum. We need to now unwrap the ***result*** Enum, which has two data elements – ***success*** and ***failure*** – for each record. The case statement will help us switch between the two data elements. If successful, we will store the returned object value to the class variable **profile** and append it to the class level defined array variable **profiles**.

Upon failure, we print the error on the console and set the **profileExists** variable to false.

```
operation.recordMatchedBlock = { recordID, result in
switch result {
case .success(let record):
let profile = Profile(userid: record["userid"] as! String, personalprofile: record["personalprofile"] as! CKAsset, name: record["name"] as! String, businessname: record["businessname"] as! String,  businesstype: record["businesstype"] as! String)
profile.record = record
self.profiles.append(profile)
case .failure(let error):
print(error.localizedDescription)
// Handle the case when the table doesn't exist or there's an error
//self.profileExists = false
}
}
```

In the **queryResultBlock**, we again switch on the **enum** variable result. On success, we return to the main thread and on failure setting the **profileExists** variable to false and exiting the thread.

```
operation.queryResultBlock = { result in
DispatchQueue.main.async {
switch result {
case .failure(let error):
debugPrint(error)
// Handle the query result failure
//self.profileExists = false
self.syncQueue.async {
self.group.leave()
}
case .success(_):
// Handle the query result success
self.syncQueue.async {
self.group.leave()
}
}
}
}
}
```

#### Get User Cloud ID Method

Every user in iCloud has a unique cloud ID. We are using this to uniquely identify every user in our chat application. The function below is fetching the user ID from the default container.

Notice we have a completion handler in this method with two input variables, the first one takes a string value (user ID) and the other one takes an error, if one exists. Upon success, we are passing the user ID to the completion handler, and nil to the error variable, and returning to the method from where **getICloudUserID** is called. And on failure, we are providing the input to the user ID as nil and returning the error to the calling method.

```
func getICloudUserID(completion: @escaping (String?, Error?) -> Void) {
let container = CKContainer.default()
container.fetchUserRecordID { (recordID, error) in
if let error = error {
// Handle the error
completion(nil, error)
} else if let recordID = recordID {
let userID = recordID.recordName
completion(userID, nil)
}
}
}
```

#### Check If Profile Exists

This method takes the user ID as input and checks if the user is existing in the **profile** iCloud repository. The query object is formed with the condition stating if the **userid** field is equal to the **userID** variable input provided by the method. The query object is then added to the operation variable of type **CKQueryOperation**. Some operation timeout configurations are added, and finally, the operation is added to the public cloud database for execution.

```
func checkProfileExist(userID:String) {
let pred = NSPredicate(format: "userid == %@", userID)
let query = CKQuery(recordType: "profile", predicate: pred)
let operation = CKQueryOperation(query: query)
let operationConfiguration = CKOperation.Configuration()
operationConfiguration.timeoutIntervalForRequest = 10
operationConfiguration.timeoutIntervalForResource = 10
operation.configuration = operationConfiguration
CKContainer.default().publicCloudDatabase.add(operation)
```

The block **recordMatchedBlock** is triggered upon asynchronous completion of the operation. The **enum** object is iterated for **success** or **failure** result. On success, we are setting the class variable **profileExists** to true also assigning the return record to the class variable **profile**. On failure, we are setting **profileExists** to false.

```
operation.recordMatchedBlock = { recordID, result in
switch result {
case .success(let record):
self.profileExists = true
self.profile = Profile(userid: record["userid"] as! String, personalprofile: record["personalprofile"] as! CKAsset, name: record["name"] as! String, businessname: record["businessname"] as! String, businesstype: record["businesstype"] as! String)
self.profile!.record = record
case .failure(let error):
print(error.localizedDescription)
// Handle the case when the table doesn't exist or there's an error
self.profileExists = false
}
}
```

In the queryResultBlock, we again iterate through the **enum** result object. On **failure**, we set the profileExists to false and return to the main threads. Upon **success**, we simply leave the thread to return to the main thread.

```
operation.queryResultBlock = { result in
DispatchQueue.main.async {
switch result {
case .failure(let error):
debugPrint(error)
// Handle the query result failure
self.profileExists = false
self.syncQueue.async {
self.group.leave()
}
case .success(_):
// Handle the query result success
self.syncQueue.async {
self.group.leave()
}
}
}
}
}
}
```

#### Complete Code

Find below the complete code of the Contact class:

```
import UIKit
import CloudKit
class Contact: NSObject {
let group = DispatchGroup()
let syncQueue = DispatchQueue(label: "com.domain.app.sections")
var profiles:[Profile] = []
var profileExists = false
var profile:Profile?
func getContacts(businessType:String) {
profiles.removeAll()
let pred = NSPredicate(format: "businesstype == %@", businessType)
let query = CKQuery(recordType: "profile", predicate: pred)
let operation = CKQueryOperation(query: query)
CKContainer.default().publicCloudDatabase.add(operation)
let operationConfiguration = CKOperation.Configuration()
operationConfiguration.timeoutIntervalForRequest = 10
operationConfiguration.timeoutIntervalForResource = 10
operation.configuration = operationConfiguration
operation.recordMatchedBlock = { recordID, result in
switch result {
case .success(let record):
let profile = Profile(userid: record["userid"] as! String, personalprofile: record["personalprofile"] as! CKAsset, name: record["name"] as! String, businessname: record["businessname"] as! String,  businesstype: record["businesstype"] as! String)
profile.record = record
self.profiles.append(profile)
case .failure(let error):
print(error.localizedDescription)
// Handle the case when the table doesn't exist or there's an error
//self.profileExists = false
}
}
operation.queryResultBlock = { result in
DispatchQueue.main.async {
switch result {
case .failure(let error):
debugPrint(error)
// Handle the query result failure
//self.profileExists = false
self.syncQueue.async {
self.group.leave()
}
case .success(_):
// Handle the query result success
self.syncQueue.async {
self.group.leave()
}
}
}
}
}
func getICloudUserID(completion: @escaping (String?, Error?) -> Void) {
let container = CKContainer.default()
container.fetchUserRecordID { (recordID, error) in
if let error = error {
// Handle the error
completion(nil, error)
} else if let recordID = recordID {
let userID = recordID.recordName
completion(userID, nil)
}
}
}
func checkProfileExist(userID:String) {
let pred = NSPredicate(format: "userid == %@", userID)
let query = CKQuery(recordType: "profile", predicate: pred)
let operation = CKQueryOperation(query: query)
let operationConfiguration = CKOperation.Configuration()
operationConfiguration.timeoutIntervalForRequest = 10
operationConfiguration.timeoutIntervalForResource = 10
operation.configuration = operationConfiguration
CKContainer.default().publicCloudDatabase.add(operation)
operation.recordMatchedBlock = { recordID, result in
switch result {
case .success(let record):
self.profileExists = true
self.profile = Profile(userid: record["userid"] as! String, personalprofile: record["personalprofile"] as! CKAsset, name: record["name"] as! String, businessname: record["businessname"] as! String, businesstype: record["businesstype"] as! String)
self.profile!.record = record
case .failure(let error):
print(error.localizedDescription)
// Handle the case when the table doesn't exist or there's an error
self.profileExists = false
}
}
operation.queryResultBlock = { result in
DispatchQueue.main.async {
switch result {
case .failure(let error):
debugPrint(error)
// Handle the query result failure
self.profileExists = false
self.syncQueue.async {
self.group.leave()
}
case .success(_):
// Handle the query result success
self.syncQueue.async {
self.group.leave()
}
}
}
}
}
}
```

### Conversation Modal Class

Conversation modal class mimics the conversation tables in the iCloud repository. We will have unique chat table names in the repository based on who is conversing with whom. The table name is combination of originator name (one who originates the conversation) and the recipient’s name (one who the chat is written to).

A class with the name conversation is defined of type **NSObject**, inheriting two modules **Cloudkit**, for iCloud operations, and **UIKit** for UI definitions.

```
import UIKit
import CloudKit
class Conversation: NSObject {
```

The queue and the group are defined for the iCloud asynchronous operations.

```
let group = DispatchGroup()
let syncQueue = DispatchQueue(label: "com.domain.app.sections")
```

The class variables depicting the fields of the conversation table are defined. It has two main variables:

*   chattext, user-provided chat text

*   originatorid, the user ID of the person who has provided the text

We have two **init** methods: one which takes the user-provided input and assigns it to the class object and the other which is an empty constructor. The empty constructor helps us to create an object of the class for calling methods of the class without providing any input values.

```
var record:CKRecord!
var chattext:String!
var originatorid:String!
init(chattext:String!, originatorid:String!) {
self.chattext = chattext
self.originatorid = originatorid
}
override init() {
}
```

#### Get Conversation Method

This function retrieves all chats between the originator of the chat and a particular recipient user. The method takes the originator and the recipient names as input and has a completion handler, which returns a **Conversation** class array object and error, if any.

```
func getChats(receipentname:String, originatorname:String, completion:@escaping ([Conversation], Error?) -> Void){
```

In the code below, we are defining a method level variable with the name **chats**, which is an array of type **Conversation**. As the recordtype is dynamic, we are constructing that by combining the originator and recipient names.

```
var chats:[Conversation] = []
var recordType = "\(originatorname.replacingOccurrences(of: " ", with: ""))_\(receipentname.replacingOccurrences(of: " ", with: ""))"
recordType = recordType.lowercased()
```

We want to get all conversation. The dummy variable is set during creation of the chat with the value set to 1 always. This will help us to get all conversations. We are sorting the result by creation date. This way we will have all recently created records at the top, setting the operation configuration and limiting the result to the top 2000 chat records.

Note that you can also implement batch retrieval, whereby a fixed number of conversations are retrieved first. Upon user action such as pull-down gesture, you can retrieve the next set. In our code, we are simply retrieving the top 2000 records.

```
let pred = NSPredicate(format: "dummy == %@", "1")
let query = CKQuery(recordType: "\(recordType)", predicate: pred)
query.sortDescriptors = [NSSortDescriptor(key: "datecreated", ascending: true)]
let operation = CKQueryOperation(query: query)
CKContainer.default().publicCloudDatabase.add(operation)
let operationConfiguration = CKOperation.Configuration()
operationConfiguration.timeoutIntervalForRequest = 10
operationConfiguration.timeoutIntervalForResource = 10
operation.configuration = operationConfiguration
operation.resultsLimit = 2000
```

The **recordMatchedBlock** is called when the operation is complete. Iterating through the **enum** object upon **success**, we are setting our chat object to the retrieved value and appending it to the function level **chats** variable. On **failure**, we are printing the error on the console.

```
operation.recordMatchedBlock = { recordID, result in
switch result {
case .success(let record):
let chat = Conversation(chattext: record["chattext"] as? String, originatorid: record["originatorid"] as? String)
chat.record = record
chats.append(chat)
case .failure(let error):
print(error.localizedDescription)
}
}
```

Finally, in the queryResultBlock, we are returning the completing handler with chats array object and nil in case of success and nil and error in case of failure.

```
operation.queryResultBlock = { result in
DispatchQueue.main.async {
switch result {
case .failure(let error):
debugPrint(error)
print(error)
completion(nil, error)
case .success(_):
completion(chats, nil)
}
}
}
}
```

#### Save Conversation Method

This method saves the conversation to cloud when the chat is initiated for the first time. It takes the originator name, recipient name, the profiles of both originator and recipient, and the actual chat text.

```
func saveNewChatRecordsToCloud(receipentname:String, originatorname:String, receipentprofile:CKAsset, originatorprofile:CKAsset, lastchat:String) {
```

In this method, we will save two records to a public cloud database and one to the private cloud. The code below defines the database variables and also the conversation table name as explained before.

```
let container = CKContainer.default()
let publicDatabase = container.publicCloudDatabase
let privateDatabase = container.privateCloudDatabase
var recordType = "\(originatorname.replacingOccurrences(of: " ", with: ""))_\(receipentname.replacingOccurrences(of: " ", with: ""))"
recordType = recordType.lowercased()
```

The code below defines the following three record:

*   **businessChatRecord**: This is defined dynamically one per user business. The table is names after the business name. It stores the originator of the chat name, the originator profile, a dummy value 1 for easy query retrieval, and the last chat conversation. This record will be stored in a public database.

*   **chatRecord**: This is the conversation table which stores all conversation between two users (originator and recipient). It has four fields, originator user ID, chat text, date of chat creation, and dummy field with default value 1, for easy retrieval of records. This record will be stored in a public database.

*   **personalChatRecord**: This record will be stored in chattables. It contains the conversation table name and recipient name, profile image, a dummy variable with default value 1, and the chat text. This record will be stored in a private database.

An operation for the public cloud database is created for the **businessChatRecord** and the **personalChatRecord**.

```
let businessChatRecord = CKRecord(recordType: "\(receipentname.replacingOccurrences(of: " ", with: "").lowercased())")
businessChatRecord["chattablename"] =  "\(recordType)"
businessChatRecord["originatorname"] = originatorname
businessChatRecord["originatorprofile"] = CKAsset(fileURL: originatorprofile.fileURL!)
businessChatRecord["dummy"] = "1"
businessChatRecord["lastchat"] = lastchat
let chatRecord = CKRecord(recordType: "\(recordType)")
chatRecord["originatorid"] = self.originatorid
chatRecord["chattext"] = self.chattext
chatRecord["datecreated"] = Date()
chatRecord["dummy"] = "1"
let personalChatRecord = CKRecord(recordType: "chattables")
personalChatRecord["chattablename"] = "\(recordType)"
personalChatRecord["receipentprofile"] = CKAsset(fileURL: receipentprofile.fileURL!)
personalChatRecord["receipentname"] = receipentname
personalChatRecord["dummy"] = "1"
personalChatRecord["lastchat"] = lastchat
let publicSaveOperation = CKModifyRecordsOperation(recordsToSave: [businessChatRecord, chatRecord])
publicSaveOperation.savePolicy = .changedKeys
```

The code below is executed to update the iCloud repository for the public cloud. Upon success, we are creating a private cloud operation for our **personalChatRecord** data and executing it. Upon success, we are leaving the group. In case of failure, we are printing errors to the console.

```
publicSaveOperation.modifyRecordsResultBlock = { result in
switch result {
case .success:
self.syncQueue.async {
let privateSaveOperation = CKModifyRecordsOperation(recordsToSave: [personalChatRecord])
privateSaveOperation.savePolicy = .changedKeys
privateSaveOperation.modifyRecordsResultBlock = { result in
switch result {
case .success:
self.syncQueue.async {
self.group.leave()
}
// Handle any further actions or UI updates
case .failure(let error):
print("Error saving record to private database: \(error.localizedDescription)")
// Handle the error case
}
}
privateDatabase.add(privateSaveOperation)
}
// Handle any further actions or UI updates
case .failure(let error):
print("Error saving records: \(error.localizedDescription)")
// Handle the error case
}
}
publicDatabase.add(publicSaveOperation)
}
```

#### Saving Conversation Cloud Second Method

This method is called when the chat between two users already exists. It takes in the originator and the recipient’s name.

Reference to the public cloud is established and the record type dynamically created based on the user-provided inputs. A record of the conversation type is then created, and its fields are initialized with the originator user ID, chat text, and a dummy variable with text set to value 1.

```
func saveNewChatToCloud(tablename: String, receipentname:String, originatorname:String) {
let container = CKContainer.default()
let publicDatabase = container.publicCloudDatabase
var recordType = "\(originatorname.replacingOccurrences(of: " ", with: ""))_\(receipentname.replacingOccurrences(of: " ", with: ""))"
recordType = recordType.lowercased()
let chatRecord = CKRecord(recordType: "\(recordType)")
chatRecord["originatorid"] = self.originatorid
chatRecord["chattext"] = self.chattext
chatRecord["datecreated"] = Date()
chatRecord["dummy"] = "1"
```

In the code below, we are now executing **CKModifyRecordsOperation** and saving the chat record to the table. If successful, we are calling a custom function set subscription. This is to inform the recipient user that a new chat has been created. Upon failure, we print the error on the console.

```
let publicSaveOperation = CKModifyRecordsOperation(recordsToSave: [chatRecord])
publicSaveOperation.savePolicy = .changedKeys
publicSaveOperation.modifyRecordsResultBlock = { result in
switch result {
case .success:
self.syncQueue.async {
self.setSubscription(tablename: recordType, chattext: self.chattext, originatorname: originatorname)
}
case .failure(let error):
print("Error saving records: \(error.localizedDescription)")
}
}
publicDatabase.add(publicSaveOperation)
}
```

#### Set Subscription Method

This method will help the recipient user be aware of a new chat addition to the conversation. We are taking the conversation table name, the actual chat, and the name of the originator. We get access to the public database and then create a **subscription** variable of type **CKQuerySubscription**. The constructor needs the table name, a query constructor, name of the subscription, and the options we would like. In our example code, we are setting the option to trigger it when the user creates or modifies a new record for the given table.

Next, we are creating a notification info variable and setting its alert with text “You have a New Chat”. The notification info is added to the **subscription** variable, and we execute the save operations on the public database. Once completed, we leave the thread to return to the main thread.

```
func setSubscription(tablename: String, chattext:String,originatorname:String){
// set up a CKSubscription for the database table
let container = CKContainer.default()
let publicDatabase = container.publicCloudDatabase
let subscription = CKQuerySubscription(
recordType: "\(tablename)",
predicate: NSPredicate(value: true),
subscriptionID: "mySubscription",
options: [.firesOnRecordCreation, .firesOnRecordUpdate]
)
let notificationInfo = CKSubscription.NotificationInfo()
notificationInfo.alertBody = "You have a New Chat"
subscription.notificationInfo = notificationInfo
publicDatabase.save(subscription) { (subscription, error) in
if let error = error {
print("Error saving subscription: \(error.localizedDescription)")
self.group.leave()
} else {
print("Subscription saved successfully!")
self.group.leave()
}
}
}
```

#### Chat Table Exist Method

This method will help the user to know whether a chat conversation exists with any other user. It takes recipient and originator names as input and has a completion handler which returns two values – a Boolean value to indicate if the chat table exists and an error object, in case a query results with a system error.

```
func chatTableExists(receipentname:String, originatorname:String, completion:@escaping (Bool, Error?) -> Void ){
```

The record type is dynamically generated, and a public database reference variable is defined. We then create a CKQuery object with the record type name and the query is executed.

```
var recordType = "\(originatorname.replacingOccurrences(of: " ", with: ""))_\(receipentname.replacingOccurrences(of: " ", with: ""))"
recordType = recordType.lowercased()
let container = CKContainer.default()
let database = container.publicCloudDatabase
let predicate = NSPredicate(format: "dummy == %@", "1")
let query = CKQuery(recordType: recordType, predicate: predicate)
let operation = CKQueryOperation(query: query)
operation.zoneID = CKRecordZone.default().zoneID
```

The **queryResultBlock** will asynchronously wait, and upon success, the completion handler returns true or else false.

```
operation.queryResultBlock = { result in
DispatchQueue.main.async {
switch result {
case .failure(let error):
print(error)
debugPrint(error)
// Handle the query result failure
//self.syncQueue.async {
completion(false, error)
//  self.group.leave()
//}
case .success(_):
// Handle the query result success
//self.syncQueue.async {
completion(true, nil)
//  self.group.leave()
//}
}
}
}
database.add(operation)
}
```

#### Get Last Chat Method

This method will get the last chat from the conversation trail. It takes the chat table name as input and has a completion handler with two variables: a conversation array object and an error object.

A method level conversation array is defined with the name **chats**. A Query object is formed to retrieve all records, and the results are sorted on reverse chronological order based on creation date. Query configuration object is set to 10 sec, and operation is added to the public database.

```
func getlastChat(chattablename:String, completion:@escaping ([Conversation], Error?) -> Void){
var chats:[Conversation] = []
let pred = NSPredicate(format: "dummy == %@", "1")
let query = CKQuery(recordType: "\(chattablename)", predicate: pred)
query.sortDescriptors = [NSSortDescriptor(key: "datecreated", ascending: false)]
let operation = CKQueryOperation(query: query)
operation.resultsLimit = 1
CKContainer.default().publicCloudDatabase.add(operation)
let operationConfiguration = CKOperation.Configuration()
operationConfiguration.timeoutIntervalForRequest = 10
operationConfiguration.timeoutIntervalForResource = 10
operation.configuration = operationConfiguration
```

The **recordMatchedBlock** upon success iterates through all conversations and adds it to the **chats** array object. If the query fails, it prints the error message on the console.

```
operation.recordMatchedBlock = { recordID, result in
switch result {
case .success(let record):
let chat = Conversation(chattext: record["chattext"] as? String, originatorid: record["originatorid"] as? String)
chat.record = record
chats.append(chat)
case .failure(let error):
print(error.localizedDescription)
}
}
```

Finally, in the **queryResultBlock**, we complete our completion handler with either the conversation (when it succeeds) or error (when it fails).

```
operation.queryResultBlock = { result in
DispatchQueue.main.async {
switch result {
case .failure(let error):
debugPrint(error)
print(error)
completion(nil, error)
case .success(_):
completion(chats, nil)
}
}
}
}
}
```

#### Complete Code

Find below the complete code of the conversation modal class:

```
//
//  Conversation.swift
//  Chat
//
//  Created by Shantanu Baruah on 6/23/24.
//
import UIKit
import CloudKit
class Conversation: NSObject {
let group = DispatchGroup()
let syncQueue = DispatchQueue(label: "com.domain.app.sections")
var record:CKRecord!
var chattext:String!
var originatorid:String!
init(chattext:String!, originatorid:String!) {
self.chattext = chattext
self.originatorid = originatorid
}
override init() {
}
//MARK: - Query Chat
//Query to find Contacts with the right Business Type
func getChats(receipentname:String, originatorname:String, completion:@escaping ([Conversation], Error?) -> Void){
var chats:[Conversation] = []
var recordType = "\(originatorname.replacingOccurrences(of: " ", with: ""))_\(receipentname.replacingOccurrences(of: " ", with: ""))"
recordType = recordType.lowercased()
let pred = NSPredicate(format: "dummy == %@", "1")
let query = CKQuery(recordType: "\(recordType)", predicate: pred)
query.sortDescriptors = [NSSortDescriptor(key: "datecreated", ascending: true)]
let operation = CKQueryOperation(query: query)
CKContainer.default().publicCloudDatabase.add(operation)
let operationConfiguration = CKOperation.Configuration()
operationConfiguration.timeoutIntervalForRequest = 10
operationConfiguration.timeoutIntervalForResource = 10
operation.configuration = operationConfiguration
operation.resultsLimit = 2000
operation.recordMatchedBlock = { recordID, result in
switch result {
case .success(let record):
let chat = Conversation(chattext: record["chattext"] as? String, originatorid: record["originatorid"] as? String)
chat.record = record
chats.append(chat)
case .failure(let error):
print(error.localizedDescription)
}
}
operation.queryResultBlock = { result in
DispatchQueue.main.async {
switch result {
case .failure(let error):
debugPrint(error)
print(error)
completion(chats, error)
case .success(_):
completion(chats, nil)
}
}
}
}
func saveNewChatRecordsToCloud(receipentname:String, originatorname:String, receipentprofile:CKAsset, originatorprofile:CKAsset, lastchat:String) {
let container = CKContainer.default()
let publicDatabase = container.publicCloudDatabase
let privateDatabase = container.privateCloudDatabase
var recordType = "\(originatorname.replacingOccurrences(of: " ", with: ""))_\(receipentname.replacingOccurrences(of: " ", with: ""))"
recordType = recordType.lowercased()
let businessChatRecord = CKRecord(recordType: "\(receipentname.replacingOccurrences(of: " ", with: "").lowercased())")
businessChatRecord["chattablename"] =  "\(recordType)"
businessChatRecord["originatorname"] = originatorname
businessChatRecord["originatorprofile"] = CKAsset(fileURL: originatorprofile.fileURL!)
businessChatRecord["dummy"] = "1"
businessChatRecord["lastchat"] = lastchat
let chatRecord = CKRecord(recordType: "\(recordType)")
chatRecord["originatorid"] = self.originatorid
chatRecord["chattext"] = self.chattext
chatRecord["datecreated"] = Date()
chatRecord["dummy"] = "1"
let personalChatRecord = CKRecord(recordType: "chattables")
personalChatRecord["chattablename"] = "\(recordType)"
personalChatRecord["receipentprofile"] = CKAsset(fileURL: receipentprofile.fileURL!)
personalChatRecord["receipentname"] = receipentname
personalChatRecord["dummy"] = "1"
personalChatRecord["lastchat"] = lastchat
let publicSaveOperation = CKModifyRecordsOperation(recordsToSave: [businessChatRecord, chatRecord])
publicSaveOperation.savePolicy = .changedKeys
publicSaveOperation.modifyRecordsResultBlock = { result in
switch result {
case .success:
self.syncQueue.async {
let privateSaveOperation = CKModifyRecordsOperation(recordsToSave: [personalChatRecord])
privateSaveOperation.savePolicy = .changedKeys
privateSaveOperation.modifyRecordsResultBlock = { result in
switch result {
case .success:
self.syncQueue.async {
self.group.leave()
}
// Handle any further actions or UI updates
case .failure(let error):
print("Error saving record to private database: \(error.localizedDescription)")
// Handle the error case
}
}
privateDatabase.add(privateSaveOperation)
}
// Handle any further actions or UI updates
case .failure(let error):
print("Error saving records: \(error.localizedDescription)")
// Handle the error case
}
}
publicDatabase.add(publicSaveOperation)
}
//Save Chat
func saveNewChatToCloud(tablename: String, receipentname:String, originatorname:String) {
let container = CKContainer.default()
let publicDatabase = container.publicCloudDatabase
var recordType = "\(originatorname.replacingOccurrences(of: " ", with: ""))_\(receipentname.replacingOccurrences(of: " ", with: ""))"
recordType = recordType.lowercased()
let chatRecord = CKRecord(recordType: "\(recordType)")
chatRecord["originatorid"] = self.originatorid
chatRecord["chattext"] = self.chattext
chatRecord["datecreated"] = Date()
chatRecord["dummy"] = "1"
let publicSaveOperation = CKModifyRecordsOperation(recordsToSave: [chatRecord])
publicSaveOperation.savePolicy = .changedKeys
publicSaveOperation.modifyRecordsResultBlock = { result in
switch result {
case .success:
self.syncQueue.async {
self.setSubscription(tablename: recordType, chattext: self.chattext, originatorname: originatorname)
}
case .failure(let error):
print("Error saving records: \(error.localizedDescription)")
}
}
publicDatabase.add(publicSaveOperation)
}
//MARK: - Notfication
func setSubscription(tablename: String, chattext:String,originatorname:String){
// set up a CKSubscription for the database table
let container = CKContainer.default()
let publicDatabase = container.publicCloudDatabase
let subscription = CKQuerySubscription(
recordType: "\(tablename)",
predicate: NSPredicate(value: true),
subscriptionID: "mySubscription",
options: [.firesOnRecordCreation, .firesOnRecordUpdate]
)
let notificationInfo = CKSubscription.NotificationInfo()
print(chattext)
notificationInfo.alertBody = "You have a New Chat"
subscription.notificationInfo = notificationInfo
publicDatabase.save(subscription) { (subscription, error) in
if let error = error {
print("Error saving subscription: \(error.localizedDescription)")
self.group.leave()
} else {
print("Subscription saved successfully!")
self.group.leave()
}
}
}
//MARK: - Check if the chat table between the requestor and the sendor exists. If exists then inform the caller about it
func chatTableExists(receipentname:String, originatorname:String, completion:@escaping (Bool, Error?) -> Void ){
// Define the name of the table you want to check
var recordType = "\(originatorname.replacingOccurrences(of: " ", with: ""))_\(receipentname.replacingOccurrences(of: " ", with: ""))"
recordType = recordType.lowercased()
let container = CKContainer.default()
let database = container.publicCloudDatabase
//let predicate = NSPredicate(value: true)
let predicate = NSPredicate(format: "dummy == %@", "1")
let query = CKQuery(recordType: recordType, predicate: predicate)
//query.fetchLimit = 1
let operation = CKQueryOperation(query: query)
operation.zoneID = CKRecordZone.default().zoneID
operation.queryResultBlock = { result in
DispatchQueue.main.async {
switch result {
case .failure(let error):
print(error)
debugPrint(error)
// Handle the query result failure
completion(false, error)
case .success(_):
// Handle the query result success
completion(true, nil)
}
}
}
database.add(operation)
}
func getlastChat(chattablename:String, completion:@escaping ([Conversation], Error?) -> Void){
var chats:[Conversation] = []
let pred = NSPredicate(format: "dummy == %@", "1")
let query = CKQuery(recordType: "\(chattablename)", predicate: pred)
query.sortDescriptors = [NSSortDescriptor(key: "datecreated", ascending: false)]
let operation = CKQueryOperation(query: query)
operation.resultsLimit = 1
CKContainer.default().publicCloudDatabase.add(operation)
let operationConfiguration = CKOperation.Configuration()
operationConfiguration.timeoutIntervalForRequest = 10
operationConfiguration.timeoutIntervalForResource = 10
operation.configuration = operationConfiguration
operation.recordMatchedBlock = { recordID, result in
switch result {
case .success(let record):
let chat = Conversation(chattext: record["chattext"] as? String, originatorid: record["originatorid"] as? String)
chat.record = record
chats.append(chat)
case .failure(let error):
print(error.localizedDescription)
}
}
operation.queryResultBlock = { result in
DispatchQueue.main.async {
switch result {
case .failure(let error):
debugPrint(error)
print(error)
completion(nil, error)
case .success(_):
completion(chats, nil)
}
}
}
}
}
```

### Chattable Modal Class

This class mimics the **chattables** iCloud repository. The code below defines the class **Chattable** as **NSObject**; **it inherits** the **UiKit** and the **CloudKit** modules. It also defines the group and the queue for asynchronous iCloud method management.

The class has a chat table name, recipient profile and name, and chat text as its variables. An **init** method is defined to initialize the objects of the class with the value provided by the user. An empty **init** is also defined to invoke the class without initializing the variables (usually, this is done to access the method).

```
import UIKit
import CloudKit
class Chattable: NSObject {
let group = DispatchGroup()
let syncQueue = DispatchQueue(label: "com.domain.app.sections")
var chattablename:String!
var receipentprofile:CKAsset!
var receipentname:String!
var lastchat:String!
var record:CKRecord!
init(chattablename: String!, receipentprofile: CKAsset!, receipentname: String!, lastchat: String!) {
self.chattablename = chattablename
self.receipentprofile = receipentprofile
self.receipentname = receipentname
self.lastchat = lastchat
}
override init(){
}
```

#### Get Table Name Method

This method gets all the table names for the current user and returns it using the completion handler.

An array variable of the class is defined as **chattables**. A CKQuery object is created for the **chattables** iCloud repository and added to the operation, which is executed on a public database.

```
func gettableNames(completion:@escaping ([Chattable], Error?) -> Void){
var chattables:[Chattable] = []
let pred = NSPredicate(format: "dummy == %@", "1")
let query = CKQuery(recordType: "chattables", predicate: pred)
let operation = CKQueryOperation(query: query)
let operationConfiguration = CKOperation.Configuration()
operationConfiguration.timeoutIntervalForRequest = 10
operationConfiguration.timeoutIntervalForResource = 10
operation.configuration = operationConfiguration
CKContainer.default().privateCloudDatabase.add(operation)
```

In the recordMatchedBlock, we iterate and **switch** on the **enum** object result and on success appends the found chattable to the chattables array object. Upon failure, we print the error on the console.

```
operation.recordMatchedBlock = { recordID, result in
switch result {
case .success(let record):
let chattable = Chattable(chattablename: record["chattablename"] as? String, receipentprofile: record["receipentprofile"] as? CKAsset, receipentname: record["receipentname"] as? String, lastchat: record["lastchat"] as? String)
chattable.record = record
chattables.append(chattable)
case .failure(let error):
print(error.localizedDescription)
}
}
```

In the queryResultBlock, we complete our completion handler. Upon success, the handler returns the chattables, and in case of failure, it returns the error to the calling method.

```
operation.queryResultBlock = { result in
DispatchQueue.main.async {
switch result {
case .failure(let error):
debugPrint(error)
print(error)
completion(nil, error)
case .success(_):
completion(chattables, nil)
}
}
}
}
}
```

#### Complete Code

Find below the complete code of the Chattable modal class:

```
//
//  Chattable.swift
//  Chat
//
//  Created by Shantanu Baruah on 6/25/24.
//
import UIKit
import CloudKit
class Chattable: NSObject {
let group = DispatchGroup()
let syncQueue = DispatchQueue(label: "com.domain.app.sections")
var chattablename:String!
var receipentprofile:CKAsset!
var receipentname:String!
var lastchat:String!
var record:CKRecord!
init(chattablename: String!, receipentprofile: CKAsset!, receipentname: String!, lastchat: String!) {
self.chattablename = chattablename
self.receipentprofile = receipentprofile
self.receipentname = receipentname
self.lastchat = lastchat
}
override init(){
}
func gettableNames(completion:@escaping ([Chattable], Error?) -> Void){
var chattables:[Chattable] = []
let pred = NSPredicate(format: "dummy == %@", "1")
let query = CKQuery(recordType: "chattables", predicate: pred)
let operation = CKQueryOperation(query: query)
let operationConfiguration = CKOperation.Configuration()
operationConfiguration.timeoutIntervalForRequest = 10
operationConfiguration.timeoutIntervalForResource = 10
operation.configuration = operationConfiguration
CKContainer.default().privateCloudDatabase.add(operation)
operation.recordMatchedBlock = { recordID, result in
switch result {
case .success(let record):
let chattable = Chattable(chattablename: record["chattablename"] as? String, receipentprofile: record["receipentprofile"] as? CKAsset, receipentname: record["receipentname"] as? String, lastchat: record["lastchat"] as? String)
chattable.record = record
chattables.append(chattable)
case .failure(let error):
print(error.localizedDescription)
}
}
operation.queryResultBlock = { result in
DispatchQueue.main.async {
switch result {
case .failure(let error):
debugPrint(error)
print(error)
completion(nil, error)
case .success(_):
completion(chattables, nil)
}
}
}
}
}
```

### Businesstable Modal Class

This modal class represents the business table of each individual user. Please note that every user will have a table with the name same as its business name. It is a reference table for all users with whom a chat has been initiated. We also store the originator profile information in this table, and although it is a duplicate of the information stored in profile, it helps in retrieving originator information swiftly without wasting costly query resources.

In the code below, we define the class **Businesstable** as **NSObject** inheriting two modules **UIKit** and **CloudKit**. The queue and group objects are defined, which will help in calling the iCloud asynchronous CRUD function. The class mimics the following fields of the table as variables: the chat table name with which the user has a conversation initiated, the user’s name and profile image, and the last chat text. An **init** method is defined to initialize the variables. An empty **init** is also defined in case we need to use the class function without initializing the variables.

```
import UIKit
import CloudKit
class Businesstable: NSObject {
let group = DispatchGroup()
let syncQueue = DispatchQueue(label: "com.domain.app.sections")
var chattablename:String!
var originatorprofile:CKAsset!
var originatorname:String!
var lastchat:String!
var record:CKRecord!
init(chattablename: String!, originatorprofile: CKAsset!, originatorname: String!, lastchat: String!) {
self.chattablename = chattablename
self.originatorprofile = originatorprofile
self.originatorname = originatorname
self.lastchat = lastchat
}
override init(){
}
```

#### Get Table Names Method

This method gets all the chat table names for the user. The method takes the table name (the business name of the user) on which the query must be performed and has a completion handler returning the table names as an array.

A method array variable **businesstables** is defined to store all the business table names. A query object is formed on a dummy variable (every record is stored with value as 1, so it is going to return all records), and the query is added to the operations object, which will be executed on the user public database. An operation configuration is set for a 10-sec timeout and added to the operation configuration setting.

```
func gettableNames(tablename: String, completion:@escaping ([Businesstable], Error?) ->Void ){
var businesstables:[Businesstable] = []
let pred = NSPredicate(format: "dummy == %@", "1")
let query = CKQuery(recordType: "\(tablename)", predicate: pred)
let operation = CKQueryOperation(query: query)
CKContainer.default().publicCloudDatabase.add(operation)
let operationConfiguration = CKOperation.Configuration()
operationConfiguration.timeoutIntervalForRequest = 10
operationConfiguration.timeoutIntervalForResource = 10
operation.configuration = operationConfiguration
```

If the execution is a success, the **recordMatchedBlock** will iterate through the records and store it in the method level **businesstables** array. Upon failure, it will print the error on the console.

```
operation.recordMatchedBlock = { recordID, result in
switch result {
case .success(let record):
let businesstable = Businesstable(chattablename: record["chattablename"] as? String, originatorprofile: record["originatorprofile"] as? CKAsset, originatorname: record["originatorname"] as? String, lastchat: record["lastchat"] as? String)
businesstable.record = record
businesstables.append(businesstable)
case .failure(let error):
print(error.localizedDescription)
}
}
```

The **queryResultBlock** will switch on the **enum** result and complete the handler by returning the **businesstables** array if it is a success or the error object in case of failure.

```
operation.queryResultBlock = { result in
DispatchQueue.main.async {
switch result {
case .failure(let error):
debugPrint(error)
print(error)
completion(businesstables, error)
case .success(_):
completion(businesstables, nil)
}
}
}
}
```

#### Complete Code

Complete code of Businesstable class is given below:

```
//
//  Businesstable.swift
//  Chat
//
//  Created by Shantanu Baruah on 6/25/24.
//
import UIKit
import CloudKit
class Businesstable: NSObject {
let group = DispatchGroup()
let syncQueue = DispatchQueue(label: "com.domain.app.sections")
var chattablename:String!
var originatorprofile:CKAsset!
var originatorname:String!
var lastchat:String!
var record:CKRecord!
init(chattablename: String!, originatorprofile: CKAsset!, originatorname: String!, lastchat: String!) {
self.chattablename = chattablename
self.originatorprofile = originatorprofile
self.originatorname = originatorname
self.lastchat = lastchat
}
override init(){
}
func gettableNames(tablename: String, completion:@escaping ([Businesstable], Error?) ->Void ){
var businesstables:[Businesstable] = []
let pred = NSPredicate(format: "dummy == %@", "1")
print(tablename)
let query = CKQuery(recordType: "\(tablename)", predicate: pred)
let operation = CKQueryOperation(query: query)
CKContainer.default().publicCloudDatabase.add(operation)
let operationConfiguration = CKOperation.Configuration()
operationConfiguration.timeoutIntervalForRequest = 10
operationConfiguration.timeoutIntervalForResource = 10
operation.configuration = operationConfiguration
operation.recordMatchedBlock = { recordID, result in
switch result {
case .success(let record):
let businesstable = Businesstable(chattablename: record["chattablename"] as? String, originatorprofile: record["originatorprofile"] as? CKAsset, originatorname: record["originatorname"] as? String, lastchat: record["lastchat"] as? String)
businesstable.record = record
businesstables.append(businesstable)
case .failure(let error):
print(error.localizedDescription)
}
}
operation.queryResultBlock = { result in
DispatchQueue.main.async {
switch result {
case .failure(let error):
debugPrint(error)
print(error)
completion(businesstables, error)
case .success(_):
completion(businesstables, nil)
}
}
}
}
}
```

# 13. Conversation Between Users – View Definition

## Define the View Classes

In the MVC architecture, the view is responsible for presenting the data (from the modal view) in a user-friendly interface. The UI also handles user input and events. In our chat application, we have four views as described below:

*   **CreateProfileViewController:** Helps in creating a new user profile

*   **ContactViewController:** Finding and adding contact to user contact list

*   **ChatDetailViewController:** Displays conversation between the user and another user

*   **ChatViewController:** Displays all contacts with whom the user has initiated chat

### CreateProfileViewController

CreateProfileViewController takes user input to create a profile and invokes methods to store the profile object in the iCloud repository.

#### Class Definition

The code snippet below is the beginning of a view controller class for creating a user profile. The view controller will contain a table view, text fields, and a tab bar. It will use **CloudKit** to manage profile data and will handle user interactions with these UI elements. The following are the class properties:

*   **var profile: Profile?:** This line declares a property named profile of type Profile (modal Class). The ? indicates that profile is an optional value, meaning it can be either a Profile object or nil.

*   **var userid: String = ““:** This line declares a property named **userid** of type String and initializes it with an empty string.

```
import UIKit
import CloudKit
class CreateProfileViewController: UIViewController, UITableViewDelegate, UITableViewDataSource, UITabBarDelegate, UITextFieldDelegate {
var profile:Profile?
var userid:String = ""
```

#### UI Object Definition

The code below defines several UI elements that are used in a **CreateProfileViewController** class to build a user interface for creating a profile. These elements include a tab bar; a header label; an image view for the profile picture; text fields for name, business name, and business type; a table view for business type selection; and a save button. The following are the breakdown of the UI objects:

**Tab bar**

*   Creates a UITabBar with a specific background color

*   Sets up three UITabBarItem instances for “Profile”, “Contacts”, and “Chats” with corresponding images and tags

*   Assigns the created tab bar items to the tab bar

**Header label**

*   Creates a UILabel for displaying the header text “Create Profile”

*   Sets text color, alignment, and font

**Personal profile image view**

*   Creates an UIImageView for displaying the profile image

*   Sets image, content mode, and border style

**Text fields**

*   Creates three UITextField instances for name, business name, and business type

*   Sets placeholder text, border style, and other appearance properties

**Business type table view**

*   Creates a UITableView for displaying business type options

*   Sets background color and enables scrolling

**Save button**

*   Creates a UIButton for saving the profile

*   Sets title, color, font, and appearance

```
private let tabBar:UITabBar = {
let tabbar = UITabBar()
tabbar.translatesAutoresizingMaskIntoConstraints = false
tabbar.backgroundColor = UIColor.init(red: 171/255, green: 189/255, blue: 217/255, alpha: 1)
let saveSymbolConfiguration = UIImage.SymbolConfiguration(pointSize: 16, weight: .black)
let contact = UIImage(systemName: "doc.on.clipboard", withConfiguration: saveSymbolConfiguration)
let contactbutton = UITabBarItem(title: "Contacts", image: contact, selectedImage: contact)
contactbutton.tag = 1
let chat = UIImage(systemName: "doc.plaintext", withConfiguration: saveSymbolConfiguration)
let chatbutton = UITabBarItem(title: "Chats", image: chat, selectedImage: chat)
chatbutton.tag = 2
let profile = UIImage(systemName: "person.fill", withConfiguration: saveSymbolConfiguration)
let profilebutton = UITabBarItem(title: "Profile", image: profile, selectedImage: profile)
profilebutton.tag = 0
tabbar.setItems([profilebutton, contactbutton, chatbutton], animated: false)
return tabbar
}()
private let headerLabel:UILabel = {
let label = UILabel()
label.text = "Create Profile"
label.translatesAutoresizingMaskIntoConstraints = false
label.textColor = UIColor(displayP3Red: 128/255, green: 0/255, blue: 128/255, alpha: 1)
label.textAlignment = .center
label.font = UIFont.boldSystemFont(ofSize: 35)
return label
}()
//Personal Section UI Object
let personalProfileImageView: UIImageView = {
let theImageView = UIImageView()
theImageView.image = UIImage(systemName: "person.fill")
theImageView.contentMode = .scaleAspectFit
theImageView.translatesAutoresizingMaskIntoConstraints = false
theImageView.layer.masksToBounds = true
theImageView.layer.borderWidth = 1
theImageView.layer.borderColor = UIColor.systemGray.cgColor
return theImageView
}()
let nameTextField:UITextField = {
let textField = UITextField()
textField.translatesAutoresizingMaskIntoConstraints = false
textField.borderStyle = .roundedRect
textField.textColor = .black
textField.layer.borderColor  = UIColor.systemGray.cgColor
textField.layer.borderWidth = 1
textField.layer.cornerRadius = 5
textField.placeholder = "Enter Name"
textField.textColor = .black
textField.isEnabled = true
textField.keyboardType = .default
return textField
}()
let businessNameTextField:UITextField = {
let textField = UITextField()
textField.translatesAutoresizingMaskIntoConstraints = false
textField.borderStyle = .roundedRect
textField.textColor = .black
textField.layer.borderColor  = UIColor.systemGray.cgColor
textField.layer.borderWidth = 1
textField.layer.cornerRadius = 5
textField.placeholder = "Enter Business Name"
textField.textColor = .black
textField.isEnabled = true
textField.keyboardType = .default
return textField
}()
let businessTypeTextField:UITextField = {
let textField = UITextField()
textField.translatesAutoresizingMaskIntoConstraints = false
textField.borderStyle = .roundedRect
textField.textColor = .black
textField.layer.borderColor  = UIColor.systemGray.cgColor
textField.layer.borderWidth = 1
textField.layer.cornerRadius = 5
textField.placeholder = "Select Business Type"
textField.textColor = .black
textField.isEnabled = true
textField.keyboardType = .default
return textField
}()
private let businessTypeTableView:UITableView = {
let tableView = UITableView()
tableView.translatesAutoresizingMaskIntoConstraints = false
tableView.backgroundColor = .white
tableView.isScrollEnabled = true
return tableView
}()
let saveButton:UIButton = {
let button = UIButton()
button.translatesAutoresizingMaskIntoConstraints = false
button.contentHorizontalAlignment = .center
button.setTitleColor(.white, for: .normal)
button.titleLabel?.font = UIFont.boldSystemFont(ofSize: 14)
button.titleLabel?.textAlignment = .center
button.setTitle("Save and Exit", for: .normal)
button.layer.cornerRadius = 5
button.backgroundColor = UIColor(displayP3Red: 128/255, green: 0/255, blue: 128/255, alpha: 1)
button.isEnabled = true
return button
}()
```

#### Lifecycle Method

This code defines a method named **viewDidLoad**() within a class that inherits from **UIViewController**. This method is automatically called by the system when the view controller’s view is loaded into memory. The following are the key elements of this method:

*   **override func viewDidLoad():** This line declares the method viewDidLoad() as an override of the parent class’s implementation. This means it replaces the default behavior of viewDidLoad() with custom logic.

*   **super.viewDidLoad():** This line calls the viewDidLoad() method of the superclass (in this case, UIViewController). It’s essential to call the superclass’s implementation first to ensure proper initialization of the view controller.

*   **setup():** This line calls a custom method named setup(). This method is used to perform initial setup tasks, such as setting up delegates, observers, or loading data.

*   **drawProfileCreationScreen():** This line calls another custom method named drawProfileCreationScreen(). This method is responsible for creating and arranging the UI elements (like those defined in the previous code snippet) to construct the profile creation screen.

```
override func viewDidLoad() {
super.viewDidLoad()
setup()
drawProfileCreationScreen()
}
```

#### Setup Method

The **setup**() method is responsible for configuring the initial state of the view controller and its UI elements. It’s called from the **viewDidLoad**() method.

*   **view.backgroundColor = .black:** Sets the background color of the main view to black

*   **tabBar.delegate = self:** Sets the current view controller as the delegate for the tab bar, allowing it to handle tab bar events

*   **tabBar.selectedItem = tabBar.items![0]:** Sets the first tab bar item as the selected item

*   Creates a tap gesture recognizer for the personalProfileImageView. Enables user interaction for the image view and adds the gesture recognizer and sets the corner radius of the image view.

*   Sets the view controller as the delegate and data source for the businessTypeTableView; registers a cell for reuse; sets border, color, and separator style; and initially hides the table view.

*   Sets the view controller as the delegate for the businessTypeTextField, adds a plus icon to the left of the text field, and adds target actions for text field editing events.

*   **setBusiness():** Calls a custom method to set up initial business data

```
func setup(){
view.backgroundColor = .black
tabBar.delegate = self
tabBar.selectedItem = tabBar.items![0]
let personalProfileGestureRecognizer = UITapGestureRecognizer(target: self, action: #selector(personalImageTapped(tapGestureRecognizer:)))
personalProfileImageView.isUserInteractionEnabled = true
personalProfileImageView.addGestureRecognizer(personalProfileGestureRecognizer)
personalProfileImageView.layer.cornerRadius =  50
businessTypeTableView.delegate = self
businessTypeTableView.dataSource = self
businessTypeTableView.register(UITableViewCell.self, forCellReuseIdentifier: "business")
businessTypeTableView.layer.borderWidth = 0
businessTypeTableView.layer.borderColor = UIColor.white.cgColor
businessTypeTableView.separatorStyle = .none
businessTypeTableView.isHidden = true
businessTypeTextField.delegate = self
businessTypeTextField.leftView = UIImageView(image: UIImage(systemName: "plus"))
businessTypeTextField.leftViewMode = .always
businessTypeTextField.addTarget(self, action: #selector(businessTypeEditingChanged), for: .editingChanged)
businessTypeTextField.addTarget(self, action: #selector(businessTypeEditingDidBegin), for: .editingDidBegin)
businessTypeTextField.addTarget(self, action: #selector(businessTypeEditingDidEnd), for: .editingDidEnd)
setBusiness()
}
```

#### Draw Profile Screen

This method is responsible for laying out the UI elements defined in the previous code snippets onto the main view using Auto Layout constraints. The detail of the code is given below:

*   **Calculate screen size:** Gets the dimensions of the main screen.

*   **Add Subviews**: Adds each UI element (header label, profile image, text fields, table view, save button, and tab bar) as a **subview** of the main view

*   **Create constraints:** For each UI element, creates an array of **NSLayoutConstraints** to define its position and size relative to other elements or the view itself

*   **Activate constraints:** Activates the created constraints to apply the layout to the UI elements

*   **Add target for save button:** Adds a target action to the save button to trigger the **saveAction** method when the button is tapped

Please note, for multiple UI elements with similar constraints, you can consider using a helper function to create constraints more efficiently. Try creating a helper method in your code.

```
func drawProfileCreationScreen(){
let screensize: CGRect = UIScreen.main.bounds
let screenWidth = screensize.width
view.addSubview(headerLabel)
let constraints = [
headerLabel.topAnchor.constraint(equalTo: view.topAnchor, constant: 30),
headerLabel.widthAnchor.constraint(equalToConstant: screenWidth),
headerLabel.heightAnchor.constraint(equalToConstant: 40)
]
NSLayoutConstraint.activate(constraints)
view.addSubview(personalProfileImageView)
let constraints2 = [
personalProfileImageView.topAnchor.constraint(equalTo: headerLabel.bottomAnchor, constant: 5),
personalProfileImageView.leftAnchor.constraint(equalTo: view.leftAnchor, constant: 140),
personalProfileImageView.widthAnchor.constraint(equalToConstant: 100),
personalProfileImageView.heightAnchor.constraint(equalToConstant: 100)
]
NSLayoutConstraint.activate(constraints2)
NSLayoutConstraint.activate(constraints2)
view.addSubview(nameTextField)
let constraints3 = [
nameTextField.topAnchor.constraint(equalTo: personalProfileImageView.bottomAnchor, constant: 10),
nameTextField.leftAnchor.constraint(equalTo: view.leftAnchor, constant: 5),
nameTextField.widthAnchor.constraint(equalToConstant: screenWidth - 10),
nameTextField.heightAnchor.constraint(equalToConstant: 40)
]
NSLayoutConstraint.activate(constraints3)
view.addSubview(businessNameTextField)
let constraints4 = [
businessNameTextField.topAnchor.constraint(equalTo: nameTextField.bottomAnchor, constant: 10),
businessNameTextField.leftAnchor.constraint(equalTo: view.leftAnchor, constant: 5),
businessNameTextField.widthAnchor.constraint(equalToConstant: screenWidth - 10),
businessNameTextField.heightAnchor.constraint(equalToConstant: 40)
]
NSLayoutConstraint.activate(constraints4)
view.addSubview(businessTypeTextField)
let constraints5 = [
businessTypeTextField.topAnchor.constraint(equalTo: businessNameTextField.bottomAnchor, constant: 10),
businessTypeTextField.leftAnchor.constraint(equalTo: view.leftAnchor, constant: 5),
businessTypeTextField.widthAnchor.constraint(equalToConstant: screenWidth - 10),
businessTypeTextField.heightAnchor.constraint(equalToConstant: 40)
]
NSLayoutConstraint.activate(constraints5)
view.addSubview(businessTypeTableView)
let constraints6 = [
businessTypeTableView.topAnchor.constraint(equalTo: businessTypeTextField.bottomAnchor, constant: 0),
businessTypeTableView.leftAnchor.constraint(equalTo: view.leftAnchor, constant: 5),
businessTypeTableView.widthAnchor.constraint(equalToConstant: screenWidth - 10),
businessTypeTableView.heightAnchor.constraint(equalToConstant: 300)
]
NSLayoutConstraint.activate(constraints6)
view.addSubview(saveButton)
let constraints7 = [
saveButton.topAnchor.constraint(equalTo: businessTypeTextField.bottomAnchor, constant: 40),
saveButton.leftAnchor.constraint(equalTo: view.leftAnchor, constant: 100),
saveButton.widthAnchor.constraint(equalToConstant: 185),
saveButton.heightAnchor.constraint(equalToConstant: 40)
]
NSLayoutConstraint.activate(constraints7)
saveButton.addTarget(self, action: #selector(saveAction), for: .touchUpInside)
view.addSubview(tabBar)
let constraints8 = [
tabBar.bottomAnchor.constraint(equalTo: view.bottomAnchor, constant: 5),
tabBar.leftAnchor.constraint(equalTo: view.leftAnchor, constant: 5),
tabBar.widthAnchor.constraint(equalToConstant: screenWidth),
tabBar.heightAnchor.constraint(equalToConstant: 80)
]
NSLayoutConstraint.activate(constraints8)
}
```

#### Image Event Method

The primary purpose of this function is to handle the user’s tap on the personal profile image view. When the user taps the image, the **presentPhotoOptions**() function is called, allowing the user to choose a new profile photo. The key elements in the method are as follows:

*   **@objc:** This attribute is necessary to make the function callable from Objective-C code. It’s required for using the function with **UITapGestureRecognizer**.

*   **personalImageTapped(tapGestureRecognizer: UITapGestureRecognizer):** This is the function signature. It takes a UITapGestureRecognizer as a parameter, which provides information about the tap gesture.

*   **presentPhotoOptions():** This line calls a function named **presentPhotoOptions()**, which is defined in a class extension (to be covered at the end of this section). This function is responsible for presenting options to the user for selecting a profile photo (taking a new photo, choosing from the photo library).

```
@objc func personalImageTapped(tapGestureRecognizer: UITapGestureRecognizer){
presentPhotoOptions()
}
```

#### Save Action Method

The primary purpose of this function is to handle the user’s tap on the save button. When the user taps the save button, the profileSave() function is called, which will save the profile data to the iCloud storage. The key elements of the method are as follows:

*   **@objc:** This attribute is necessary to make the function callable from Objective-C code. It’s required for using the function with the button’s **addTarget** method.

*   **saveAction(sender: UIButton):** This is the function signature. It takes a **UIButton** as a parameter, which represents the button that triggered the action. However, in this case, the sender parameter is not used.

*   **profileSave():** This line calls a function named **profileSave()**, which is defined later in the code. This function is responsible for saving the profile data.

```
@objc func saveAction(sender: UIButton){
profileSave()
}
```

#### Tab Bar Method

The purpose of the code below is to handle the selection of different tab bar items and perform corresponding actions. In this case, the first tab bar item (with tag 0) or any other item will trigger the **popUpContact()** function, while selecting the third tab bar item (with tag 2) will trigger the **popUpChat()** function.

*   **tabBar(_:didSelect:):** This is the method signature for the **UITabBarDelegate** protocol. It takes two parameters: **tabBar**, the tab bar object that triggered the event, and item, the selected tab bar item.

*   **switch item.tag:** This switch statement checks the tag property of the selected tab bar item to determine which action to perform: case 0, if the tag is 0, call the **popUpContact**() function, and case 2, if the tag is 2, call the **popUpChat**() function and default: for any other tag value, call the **popUpContact**() function.

```
func tabBar(_ tabBar: UITabBar, didSelect item: UITabBarItem) {
switch item.tag {
case 0:
popUpContact()
case 2:
popUpChat()
default:
popUpContact()
}
}
```

**popUpContact()**

This function creates a new instance of **ContactViewController** and presents it modally over the current view controller. Here’s a breakdown:

*   **viewController = ContactViewController():** Creates a new instance of **ContactViewController**

*   **viewController.profile = profile:** Passes the profile data to the **ContactViewController**

*   **viewController.modalPresentationStyle = .overCurrentContext:** Sets the presentation style to overlay the current view controller

*   **viewController.modalTransitionStyle = .crossDissolve:** Sets the transition style to a cross-dissolve effect

*   **self.present(viewController, animated: true, completion: nil):** Presents the **ContactViewController** modally with animation

**popUpChat() and popUpMarket()**

These functions are currently empty, as the create profile is only called once when the profile doesn’t exist.

```
func popUpContact(){
let viewController = ContactViewController()
viewController.profile = profile
viewController.modalPresentationStyle = .overCurrentContext
viewController.modalTransitionStyle = .crossDissolve
self.present(viewController, animated: true, completion: nil)
}
func popUpChat(){
}
func popUpMarket(){
}
```

#### Table View Methods

In this section, we will define the table method.

**Number of rows**

This Swift code defines a method that conforms to the **UITableViewDataSource** protocol. Its role is to tell the table view how many rows to display in each section. The details of the code are given below:

*   **func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int:** This is the method signature for the numberOfRowsInSection delegate method.
    *   **tableView:** The table view that is requesting the information

    *   **section:** The index of the section for which the number of rows is being requested

    *   **-> Int:** The method returns an integer representing the number of rows

*   **return business.count:** This line returns the count of the business array, which is an array of data that will be displayed in the table view.

```
func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
return business.count
}
```

**Display cell method**

This code defines a method that conforms to the UITableViewDataSource protocol. Its role is to provide a configured table view cell for a given row in the table view. The details of the method are explained below:

*   **func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell:** This is the method signature for the cellForRowAt delegate method.
    *   **tableView:** The table view that is requesting the cell.

    *   **indexPath:** The index path of the cell to be returned.

    *   **-> UITableViewCell:** The method returns a configured table view cell.

*   **let cell = tableView.dequeueReusableCell(withIdentifier: “business”, for: indexPath):** This line dequeues a reusable cell with the identifier “business”. If no reusable cell is available, a new cell is created.

*   **cell.textLabel?.text = business[indexPath.row].businesstype + “ ” + business[indexPath.row].businessdesc:** This line sets the text of the cell’s textLabel to a concatenation of the businesstype and businessdesc properties of the corresponding business object in the business array.

*   **return cell:** This line returns the configured cell to the table view.

```
func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
let cell = tableView.dequeueReusableCell(withIdentifier: "business", for: indexPath)
cell.textLabel?.text = business[indexPath.row].businesstype + " " + business[indexPath.row].businessdesc
return cell
}
```

**Row height method**

This Swift code defines a method that conforms to the **UITableViewDelegate** protocol. Its role is to specify the height of a row in a table view. The details of the method are explained below:

*   **func tableView(_ tableView: UITableView, heightForRowAt indexPath: IndexPath) -> CGFloat:** This is the method signature for the heightForRowAt delegate method.
    *   **tableView:** The table view that is requesting the row height.

    *   **indexPath:** The index path of the row for which the height is being requested.

    *   **-> CGFloat:** The method returns a floating-point value representing the height of the row in points.

*   **return 40.0:** This line returns a fixed height of 40 points for all rows in the table view.

```
func tableView(_ tableView: UITableView, heightForRowAt indexPath: IndexPath) -> CGFloat {
return 40.0
}
```

**Row selection method**

This code defines a method that conforms to the **UITableViewDelegate** protocol. Its role is to handle the event when a user taps on a table view row. The details of the method are explained below:

*   **func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath):** This is the method signature for the **didSelectRowAt** delegate method.
    *   **tableView:** The table view where the selection occurred.

    *   **indexPath:** The index path of the selected row.

*   **businessTypeTextField.text = business[indexPath.row].businesstype:** This line sets the text of the businessTypeTextField to the businesstype property of the selected business object from the business array.

*   **businessTypeTextField.resignFirstResponder():** This line dismisses the keyboard if it’s currently active on the **businessTypeTextField**.

```
func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath) {
businessTypeTextField.text = business[indexPath.row].businesstype
businessTypeTextField.resignFirstResponder()
}
```

#### Defining and Initializing the Business Object

The **setBusiness**() function populates a business array with Business objects. Each Business object has properties for **businesstype**, **businessdesc**, and **imagename**. The details of the method are explained below:

*   **var business:[Business] = []:** Initializes an empty array of Business objects.

*   **var tempBusiness:[Business] = []:** Initializes an empty temporary array of Business objects. This array is currently unused in the provided code.

*   **setBusiness():** Defines a function to populate the business array.

*   **Business(businesstype: ..., businessdesc: ..., imagename: ...):** Creates Business objects with specified properties.

*   **business.append(...):** Adds the created Business objects to the business array.

```
var business:[Business] = []
var tempBusiness:[Business] = []
func setBusiness(){
business.append(Business(businesstype: "Mortgage", businessdesc: "Provide Mortgage Solution", imagename: "scribble"))
business.append(Business(businesstype: "Insurance", businessdesc: "Provide Life/Health/Car/P&C Insurance", imagename: "folder"))
business.append(Business(businesstype: "Electrician", businessdesc: "Provide Household electrical solution", imagename: "doc.text"))
business.append(Business(businesstype: "Handyman", businessdesc: "Provide any household related work", imagename: "book"))
business.append(Business(businesstype: "Artisan", businessdesc: "Art clasess/ Pottery Classes", imagename: "graduationcap"))
business.append(Business(businesstype: "Magician", businessdesc: "Magic trick for birthday parties", imagename: "square.and.arrow.down.fill"))
business.append(Business(businesstype: "Tutor", businessdesc: "Providing Tutoring Service", imagename: "paperclip"))
business.append(Business(businesstype: "Plumber", businessdesc: "Provide plumbing Solution", imagename: "links"))
business.append(Business(businesstype: "Cleaners", businessdesc: "Provide House cleaning services", imagename: "person"))
business.append(Business(businesstype: "Architect", businessdesc: "House Design and architect services", imagename: "globe"))
business.append(Business(businesstype: "Physcial Trainer", businessdesc: "Persnoal trainer", imagename: "music.note.list"))
business.append(Business(businesstype: "Nutriontist", businessdesc: "Provide Nutriotional guidance and service", imagename: "square.and.pencil"))
business.append(Business(businesstype: "Spa", businessdesc: "Get home based spa services", imagename: "swift"))
business.append(Business(businesstype: "Decor", businessdesc: "Provide Home Design and Decor Service", imagename: "magnifyingglass"))
business.append(Business(businesstype: "Builder", businessdesc: "General Contractor", imagename: "heart.fill"))
business.append(Business(businesstype: "Pottery", businessdesc: "Learn how to create Pottery based artwork", imagename: "star.fill"))
business.append(Business(businesstype: "Dog Walker", businessdesc: "Provide Dog Care", imagename: "flag.fill"))
business.append(Business(businesstype: "Baker", businessdesc: "Get Custom Made Cakes or party needs", imagename: "folder.fill"))
business.append(Business(businesstype: "Dance Trainer", businessdesc: "Get training on various western/indian classical dance forms", imagename: "bell.fill"))
business.append(Business(businesstype: "Nurse", businessdesc: "Get In house nursing service", imagename: "tag.fill"))
business.append(Business(businesstype: "Gardner", businessdesc: "Uplift your flower or vegetable beds", imagename: "bolt.fill"))
business.append(Business(businesstype: "Snow remover", businessdesc: "Get Snow removal services", imagename: "camera.fill"))
business.append(Business(businesstype: "Swim Trainer", businessdesc: "Personal or group swim lessons", imagename: "phone.fill"))
business.append(Business(businesstype: "Marital Arts", businessdesc: "Learn Martial Arts Karte/Tae-Kon-Do", imagename: "calendar"))
business.append(Business(businesstype: "Video Grapher", businessdesc: "Get professional videographer for your events", imagename: "video.fill"))
business.append(Business(businesstype: "Photographer", businessdesc: "Get professional photographer for your events", imagename: "envelope.fill"))
business.append(Business(businesstype: "Mountainer", businessdesc: "Learn how to climb mountains", imagename: "gearshape.fill"))
business.append(Business(businesstype: "Limo Service", businessdesc: "Provide Limo services", imagename: "cart.fill"))
business.append(Business(businesstype: "Comparer", businessdesc: "For your functions and programs", imagename: "giftcard.fill"))
business.append(Business(businesstype: "Disc Jockey", businessdesc: "Get DJ your private parties anf functions", imagename: "sunrise.fill"))
business.append(Business(businesstype: "Tailor", businessdesc: "Provide custom tailoring services", imagename: "paintbrush.fill"))
business.append(Business(businesstype: "Painter", businessdesc: "Learn how to paint", imagename: "wrench.fill"))
business.append(Business(businesstype: "Vent Cleaners", businessdesc: "Provide Vent cleaning services", imagename: "hammer.fill"))
business.append(Business(businesstype: "Repair", businessdesc: "Provide general household repairs", imagename: "house.fill"))
business.append(Business(businesstype: "Locksmith", businessdesc: "Creating Keys or handle locked out situation", imagename: "lock.fill"))
business.append(Business(businesstype: "Pest Control", businessdesc: "Provide pest control services", imagename: "sparkles"))
}
```

#### Text Field Event Methods

There are three events we will define in this section as described below. The details of the method are explained below:

**Did editing begin**

This function is triggered when the user begins editing the **businessTypeTextField**. It prepares the UI for selecting a business type from the table view. The details of the method are explained below:

*   **@objc:** Makes the function callable from Objective-C.

*   **businessTypeEditingDidBegin(sender: UITextField):** Defines the function with a **UITextField** parameter (although not used in this implementation).

*   **tempBusiness = business:** Creates a copy of the business array into the **tempBusiness** array. This is to store the original business data before filtering.

*   **businessTypeTableView.isHidden = false:** Makes the **businessTypeTableView** visible.

*   **saveButton.isHidden = true:** Hides the save button while the user is selecting a business type.

```
@objc func businessTypeEditingDidBegin(sender: UITextField){
tempBusiness = business
businessTypeTableView.isHidden = false
saveButton.isHidden = true
}
```

**Editing did end method**

This function is triggered when the user finishes editing the businessTypeTextField. It restores the original business data and hides the table view and save button. The details of the method are explained below:

*   **@objc:** Makes the function callable from Objective-C

*   **businessTypeEditingDidEnd(sender: UITextField):** Defines the function with a **UITextField** parameter (although not used in this implementation)

*   **business = tempBusiness:** Restores the original business array from the **tempBusiness** array

*   **businessTypeTableView.isHidden = true:** Hides the **businessTypeTableView**

*   **saveButton.isHidden = false:** Shows the save button again

```
@objc func businessTypeEditingDidEnd(sender: UITextField){
business = tempBusiness
businessTypeTableView.isHidden = true
saveButton.isHidden = false
}
```

**When editing changed method**

This function filters the business array based on the text entered in the **businessTypeTextField** and reloads the table view. The details of the method are explained below:

*   **@objc:** Makes the function callable from Objective-C.

*   **businessTypeEditingChanged(sender: UITextField):** Defines the function with a UITextField parameter.

*   **business = business.filter { ... }:** Filters the business array using a closure.
    *   The closure iterates over each business element.

    *   It checks if the **businesstype** property, after trimming whitespace and converting to lowercase, contains the lowercase version of the text in the sender (text field).

    *   If the condition is true, the business is included in the filtered array.

*   **if (sender.text == “”):** Checks if the text field is empty.
    *   If empty, it restores the original business array from **tempBusiness**.

*   **businessTypeTableView.reloadData():** Reloads the table view to reflect the filtered data.

```
@objc func businessTypeEditingChanged(sender: UITextField){
business = business.filter{
business in return business.businesstype.trimmingCharacters(in: .whitespaces).lowercased().contains(sender.text!.lowercased())
}
if(sender.text == ""){
business = tempBusiness
}
businessTypeTableView.reloadData()
}
```

#### Saving Provide Method

This function appears to handle saving user profile information, including an image, to **CloudKit**. A detail breakdown of the code is given below:

**Image saving**

*   **let personalData = personalProfileImageView.image?.pngData():** Extracts PNG data from the personalProfileImageView image

*   **let personalFilename = getDocumentsDirectory().appendingPathComponent(“copy.png”):** Creates a file path for the image in the documents directory with the name “copy.png”

*   **try? personalData?.write(to: personalFilename):** Attempts to write the image data to the file path

**CloudKit asset creation**

*   **let personalProfile : CKAsset? = CKAsset(fileURL: personalFilename):** Creates a CKAsset object referencing the saved image file

**Profile object creation**

*   **let profile:Profile = Profile(userid: userid, personalprofile: personalProfile!, name: nameTextField.text!, businessname: businessNameTextField.text!, businesstype: businessTypeTextField.text!):** Creates a Profile object with user data and the CKAsset

**Saving to CloudKit (asynchronous)**

*   **profile.group.enter():** Starts a **CloudKit** operation group

*   **profile.saveProfile(profile: profile):** Calls a method (defined as a modal class) to save the profile object to CloudKit

*   **profile.group.notify(queue: .main) { ... }:** Sets a completion block to be called on the main queue when the operation group finishes

*   **self.profile = profile.profile:** Updates the local profile variable with the saved profile data (including a CloudKit record ID)

*   **self.popUpContact():** Presents the contact pop-up view after successful save

```
func profileSave(){
let personalData = personalProfileImageView.image?.pngData()
let personalFilename = getDocumentsDirectory().appendingPathComponent("copy.png")
try? personalData?.write(to: personalFilename)
let personalProfile : CKAsset?  = CKAsset(fileURL: personalFilename)
let profile:Profile = Profile(userid: userid, personalprofile: personalProfile!, name: nameTextField.text!, businessname: businessNameTextField.text!, businesstype: businessTypeTextField.text!)
profile.group.enter()
profile.saveProfile(profile: profile)
profile.group.notify(queue: .main) {
self.profile = profile.profile
self.popUpContact()
}
}
```

#### Get Document Directory Method

This function retrieves the path to the user’s documents directory on the device. A detail breakdown of the code is given below:

*   **func getDocumentsDirectory() -> URL:** Defines a function returning a URL

*   **let paths = FileManager.default.urls(for: .documentDirectory, in: .userDomainMask):** Gets an array of URLs representing the document directory

*   **return paths[0]:** Returns the first URL in the array, which is the documents directory path

```
func getDocumentsDirectory() -> URL {
let paths = FileManager.default.urls(for: .documentDirectory, in: .userDomainMask)
return paths[0]
}
}
```

#### Class Extension for Image-Enhanced Functions

The provided code extends the **CreateProfileViewController** to handle image selection from the camera or photo library. The detail of the code is given below:

**presentPhotoOptions()**

*   Creates an UIAlertController to present options for taking a photo or choosing one from the library

*   Uses weak self to prevent strong reference cycles

*   Presents the action sheet

**presentCamera() and presentPhoto()**

*   Create **UIImagePickerController** instances with the appropriate source type (camera or photo library)

*   Set the delegate to self to handle image selection results

*   Present the image picker controller

**imagePickerController(_:didFinishPickingMediaWithInfo:)**

*   Retrieves the selected image from the info dictionary

*   Sets the **personalProfileImageView’s** image to the selected image

*   Dismisses the image picker controller

**imagePickerControllerDidCancel(_:)**

*   Dismisses the image picker controller when the user cancels the selection

```
extension CreateProfileViewController:UIImagePickerControllerDelegate, UINavigationControllerDelegate{
func presentPhotoOptions(){
var message:String = ""
message = "Personal Profile Picture"
let actionSheet = UIAlertController(title: message, message: "Select your Option", preferredStyle: .actionSheet)
actionSheet.addAction(UIAlertAction(title: "cancel", style: .cancel, handler: nil))
actionSheet.addAction(UIAlertAction(title: "Take Photo", style: .default, handler: { [weak self]_ in
self?.presentCamera()
}))
actionSheet.addAction(UIAlertAction(title: "Choose Photo", style: .default, handler: {[weak self]_ in
self?.presentPhoto()
}))
present(actionSheet, animated: true)
}
func presentCamera(){
let ip = UIImagePickerController()
ip.sourceType = .camera
ip.delegate = self
ip.allowsEditing = true
present(ip, animated: true)
}
func presentPhoto(){
let ip = UIImagePickerController()
ip.sourceType = .photoLibrary
ip.delegate = self
ip.allowsEditing = true
present(ip, animated: true)
}
func imagePickerController(_ picker: UIImagePickerController, didFinishPickingMediaWithInfo info: [UIImagePickerController.InfoKey : Any]) {
let selectedImage  = info[UIImagePickerController.InfoKey.editedImage]
self.personalProfileImageView.image = selectedImage as? UIImage
picker.dismiss(animated: true)
}
func imagePickerControllerDidCancel(_ picker: UIImagePickerController) {
picker.dismiss(animated: true)
}
}
```

#### Run the App

As you run the app, for the first time, the Create Profile screen appears as shown below.

![](images/533539_2_En_13_Chapter/533539_2_En_13_Figa_HTML.jpg)

Users can select an image from the photo library or take a picture from the camera, enter user and business name, and select a business type from the drop-down list as soon below. The business list will filter based on user inputs. For our example, we will use two users. First user is Shantanu (shown below), who is a tutor, and our other user is Shaurya (not shown in the screen captures), who will initiate the chat conversation.

![](images/533539_2_En_13_Chapter/533539_2_En_13_Figb_HTML.jpg)

Once the profile information is entered, then the user can save the profile using the save and exit button as shown in the screen below.

![](images/533539_2_En_13_Chapter/533539_2_En_13_Figc_HTML.jpg)

#### Complete Code

The complete code of CreateProfileViewController is given below:

```
//
//  CreateProfileViewController.swift
//  Chat
//
//  Created by Shantanu Baruah on 5/22/24.
//
import UIKit
import CloudKit
class CreateProfileViewController: UIViewController, UITableViewDelegate, UITableViewDataSource, UITabBarDelegate, UITextFieldDelegate {
var profile:Profile?
var userid:String = ""
private let tabBar:UITabBar = {
let tabbar = UITabBar()
tabbar.translatesAutoresizingMaskIntoConstraints = false
tabbar.backgroundColor = UIColor.init(red: 171/255, green: 189/255, blue: 217/255, alpha: 1)
let saveSymbolConfiguration = UIImage.SymbolConfiguration(pointSize: 16, weight: .black)
let contact = UIImage(systemName: "doc.on.clipboard", withConfiguration: saveSymbolConfiguration)
let contactbutton = UITabBarItem(title: "Contacts", image: contact, selectedImage: contact)
contactbutton.tag = 1
let chat = UIImage(systemName: "doc.plaintext", withConfiguration: saveSymbolConfiguration)
let chatbutton = UITabBarItem(title: "Chats", image: chat, selectedImage: chat)
chatbutton.tag = 2
let profile = UIImage(systemName: "person.fill", withConfiguration: saveSymbolConfiguration)
let profilebutton = UITabBarItem(title: "Profile", image: profile, selectedImage: profile)
profilebutton.tag = 0
tabbar.setItems([profilebutton, contactbutton, chatbutton], animated: false)
return tabbar
}()
private let headerLabel:UILabel = {
let label = UILabel()
label.text = "Create Profile"
label.translatesAutoresizingMaskIntoConstraints = false
label.textColor = UIColor(displayP3Red: 128/255, green: 0/255, blue: 128/255, alpha: 1)
label.textAlignment = .center
label.font = UIFont.boldSystemFont(ofSize: 35)
return label
}()
//Personal Section UI Object
let personalProfileImageView: UIImageView = {
let theImageView = UIImageView()
theImageView.image = UIImage(systemName: "person.fill")
theImageView.contentMode = .scaleAspectFit
theImageView.translatesAutoresizingMaskIntoConstraints = false
theImageView.layer.masksToBounds = true
theImageView.layer.borderWidth = 1
theImageView.layer.borderColor = UIColor.systemGray.cgColor
return theImageView
}()
let nameTextField:UITextField = {
let textField = UITextField()
textField.translatesAutoresizingMaskIntoConstraints = false
textField.borderStyle = .roundedRect
textField.textColor = .black
textField.layer.borderColor  = UIColor.systemGray.cgColor
textField.layer.borderWidth = 1
textField.layer.cornerRadius = 5
textField.placeholder = "Enter Name"
textField.textColor = .black
textField.isEnabled = true
textField.keyboardType = .default
return textField
}()
let businessNameTextField:UITextField = {
let textField = UITextField()
textField.translatesAutoresizingMaskIntoConstraints = false
textField.borderStyle = .roundedRect
textField.textColor = .black
textField.layer.borderColor  = UIColor.systemGray.cgColor
textField.layer.borderWidth = 1
textField.layer.cornerRadius = 5
textField.placeholder = "Enter Business Name"
textField.textColor = .black
textField.isEnabled = true
textField.keyboardType = .default
return textField
}()
let businessTypeTextField:UITextField = {
let textField = UITextField()
textField.translatesAutoresizingMaskIntoConstraints = false
textField.borderStyle = .roundedRect
textField.textColor = .black
textField.layer.borderColor  = UIColor.systemGray.cgColor
textField.layer.borderWidth = 1
textField.layer.cornerRadius = 5
textField.placeholder = "Select Business Type"
textField.textColor = .black
textField.isEnabled = true
textField.keyboardType = .default
return textField
}()
private let businessTypeTableView:UITableView = {
let tableView = UITableView()
tableView.translatesAutoresizingMaskIntoConstraints = false
tableView.backgroundColor = .white
tableView.isScrollEnabled = true
return tableView
}()
let saveButton:UIButton = {
let button = UIButton()
button.translatesAutoresizingMaskIntoConstraints = false
button.contentHorizontalAlignment = .center
button.setTitleColor(.white, for: .normal)
button.titleLabel?.font = UIFont.boldSystemFont(ofSize: 14)
button.titleLabel?.textAlignment = .center
button.setTitle("Save and Exit", for: .normal)
button.layer.cornerRadius = 5
button.backgroundColor = UIColor(displayP3Red: 128/255, green: 0/255, blue: 128/255, alpha: 1)
button.isEnabled = true
return button
}()
override func viewDidLoad() {
super.viewDidLoad()
setup()
drawProfileCreationScreen()
}
func setup(){
view.backgroundColor = .black
tabBar.delegate = self
tabBar.selectedItem = tabBar.items![0]
let personalProfileGestureRecognizer = UITapGestureRecognizer(target: self, action: #selector(personalImageTapped(tapGestureRecognizer:)))
personalProfileImageView.isUserInteractionEnabled = true
personalProfileImageView.addGestureRecognizer(personalProfileGestureRecognizer)
personalProfileImageView.layer.cornerRadius =  50
businessTypeTableView.delegate = self
businessTypeTableView.dataSource = self
businessTypeTableView.register(UITableViewCell.self, forCellReuseIdentifier: "business")
businessTypeTableView.layer.borderWidth = 0
businessTypeTableView.layer.borderColor = UIColor.white.cgColor
businessTypeTableView.separatorStyle = .none
businessTypeTableView.isHidden = true
businessTypeTextField.delegate = self
businessTypeTextField.leftView = UIImageView(image: UIImage(systemName: "plus"))
businessTypeTextField.leftViewMode = .always
businessTypeTextField.addTarget(self, action: #selector(businessTypeEditingChanged), for: .editingChanged)
businessTypeTextField.addTarget(self, action: #selector(businessTypeEditingDidBegin), for: .editingDidBegin)
businessTypeTextField.addTarget(self, action: #selector(businessTypeEditingDidEnd), for: .editingDidEnd)
setBusiness()
}
func drawProfileCreationScreen(){
let screensize: CGRect = UIScreen.main.bounds
let screenWidth = screensize.width
view.addSubview(headerLabel)
let constraints = [
headerLabel.topAnchor.constraint(equalTo: view.topAnchor, constant: 30),
headerLabel.widthAnchor.constraint(equalToConstant: screenWidth),
headerLabel.heightAnchor.constraint(equalToConstant: 40)
]
NSLayoutConstraint.activate(constraints)
view.addSubview(personalProfileImageView)
let constraints2 = [
personalProfileImageView.topAnchor.constraint(equalTo: headerLabel.bottomAnchor, constant: 5),
personalProfileImageView.leftAnchor.constraint(equalTo: view.leftAnchor, constant: 140),
personalProfileImageView.widthAnchor.constraint(equalToConstant: 100),
personalProfileImageView.heightAnchor.constraint(equalToConstant: 100)
]
NSLayoutConstraint.activate(constraints2)
NSLayoutConstraint.activate(constraints2)
view.addSubview(nameTextField)
let constraints3 = [
nameTextField.topAnchor.constraint(equalTo: personalProfileImageView.bottomAnchor, constant: 10),
nameTextField.leftAnchor.constraint(equalTo: view.leftAnchor, constant: 5),
nameTextField.widthAnchor.constraint(equalToConstant: screenWidth - 10),
nameTextField.heightAnchor.constraint(equalToConstant: 40)
]
NSLayoutConstraint.activate(constraints3)
view.addSubview(businessNameTextField)
let constraints4 = [
businessNameTextField.topAnchor.constraint(equalTo: nameTextField.bottomAnchor, constant: 10),
businessNameTextField.leftAnchor.constraint(equalTo: view.leftAnchor, constant: 5),
businessNameTextField.widthAnchor.constraint(equalToConstant: screenWidth - 10),
businessNameTextField.heightAnchor.constraint(equalToConstant: 40)
]
NSLayoutConstraint.activate(constraints4)
view.addSubview(businessTypeTextField)
let constraints5 = [
businessTypeTextField.topAnchor.constraint(equalTo: businessNameTextField.bottomAnchor, constant: 10),
businessTypeTextField.leftAnchor.constraint(equalTo: view.leftAnchor, constant: 5),
businessTypeTextField.widthAnchor.constraint(equalToConstant: screenWidth - 10),
businessTypeTextField.heightAnchor.constraint(equalToConstant: 40)
]
NSLayoutConstraint.activate(constraints5)
view.addSubview(businessTypeTableView)
let constraints6 = [
businessTypeTableView.topAnchor.constraint(equalTo: businessTypeTextField.bottomAnchor, constant: 0),
businessTypeTableView.leftAnchor.constraint(equalTo: view.leftAnchor, constant: 5),
businessTypeTableView.widthAnchor.constraint(equalToConstant: screenWidth - 10),
businessTypeTableView.heightAnchor.constraint(equalToConstant: 300)
]
NSLayoutConstraint.activate(constraints6)
view.addSubview(saveButton)
let constraints7 = [
saveButton.topAnchor.constraint(equalTo: businessTypeTextField.bottomAnchor, constant: 40),
saveButton.leftAnchor.constraint(equalTo: view.leftAnchor, constant: 100),
saveButton.widthAnchor.constraint(equalToConstant: 185),
saveButton.heightAnchor.constraint(equalToConstant: 40)
]
NSLayoutConstraint.activate(constraints7)
saveButton.addTarget(self, action: #selector(saveAction), for: .touchUpInside)
view.addSubview(tabBar)
let constraints8 = [
tabBar.bottomAnchor.constraint(equalTo: view.bottomAnchor, constant: 5),
tabBar.leftAnchor.constraint(equalTo: view.leftAnchor, constant: 5),
tabBar.widthAnchor.constraint(equalToConstant: screenWidth),
tabBar.heightAnchor.constraint(equalToConstant: 80)
]
NSLayoutConstraint.activate(constraints8)
}
@objc func personalImageTapped(tapGestureRecognizer: UITapGestureRecognizer){
presentPhotoOptions()
}
@objc func saveAction(sender: UIButton){
profileSave()
}
func tabBar(_ tabBar: UITabBar, didSelect item: UITabBarItem) {
switch item.tag {
case 0:
popUpContact()
case 2:
popUpChat()
default:
popUpContact()
}
}
func popUpContact(){
let viewController = ContactViewController()
viewController.profile = profile
viewController.modalPresentationStyle = .overCurrentContext
viewController.modalTransitionStyle = .crossDissolve
self.present(viewController, animated: true, completion: nil)
}
func popUpChat(){
}
func popUpMarket(){
}
//MARK: - Table Functions
func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
return business.count
}
func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
let cell = tableView.dequeueReusableCell(withIdentifier: "business", for: indexPath)
cell.textLabel?.text = business[indexPath.row].businesstype + " " + business[indexPath.row].businessdesc
return cell
}
/*
The Height of the Cell Row is set to default 40 pixels
*/
func tableView(_ tableView: UITableView, heightForRowAt indexPath: IndexPath) -> CGFloat {
return 40.0
}
func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath) {
businessTypeTextField.text = business[indexPath.row].businesstype
businessTypeTextField.resignFirstResponder()
}
var business:[Business] = []
var tempBusiness:[Business] = []
func setBusiness(){
business.append(Business(businesstype: "Mortgage", businessdesc: "Provide Mortgage Solution", imagename: "scribble"))
business.append(Business(businesstype: "Insurance", businessdesc: "Provide Life/Health/Car/P&C Insurance", imagename: "folder"))
business.append(Business(businesstype: "Electrician", businessdesc: "Provide Household electrical solution", imagename: "doc.text"))
business.append(Business(businesstype: "Handyman", businessdesc: "Provide any household related work", imagename: "book"))
business.append(Business(businesstype: "Artisan", businessdesc: "Art clasess/ Pottery Classes", imagename: "graduationcap"))
business.append(Business(businesstype: "Magician", businessdesc: "Magic trick for birthday parties", imagename: "square.and.arrow.down.fill"))
business.append(Business(businesstype: "Tutor", businessdesc: "Providing Tutoring Service", imagename: "paperclip"))
business.append(Business(businesstype: "Plumber", businessdesc: "Provide plumbing Solution", imagename: "links"))
business.append(Business(businesstype: "Cleaners", businessdesc: "Provide House cleaning services", imagename: "person"))
business.append(Business(businesstype: "Architect", businessdesc: "House Design and architect services", imagename: "globe"))
business.append(Business(businesstype: "Physcial Trainer", businessdesc: "Persnoal trainer", imagename: "music.note.list"))
business.append(Business(businesstype: "Nutriontist", businessdesc: "Provide Nutriotional guidance and service", imagename: "square.and.pencil"))
business.append(Business(businesstype: "Spa", businessdesc: "Get home based spa services", imagename: "swift"))
business.append(Business(businesstype: "Decor", businessdesc: "Provide Home Design and Decor Service", imagename: "magnifyingglass"))
business.append(Business(businesstype: "Builder", businessdesc: "General Contractor", imagename: "heart.fill"))
business.append(Business(businesstype: "Pottery", businessdesc: "Learn how to create Pottery based artwork", imagename: "star.fill"))
business.append(Business(businesstype: "Dog Walker", businessdesc: "Provide Dog Care", imagename: "flag.fill"))
business.append(Business(businesstype: "Baker", businessdesc: "Get Custom Made Cakes or party needs", imagename: "folder.fill"))
business.append(Business(businesstype: "Dance Trainer", businessdesc: "Get training on various western/indian classical dance forms", imagename: "bell.fill"))
business.append(Business(businesstype: "Nurse", businessdesc: "Get In house nursing service", imagename: "tag.fill"))
business.append(Business(businesstype: "Gardner", businessdesc: "Uplift your flower or vegetable beds", imagename: "bolt.fill"))
business.append(Business(businesstype: "Snow remover", businessdesc: "Get Snow removal services", imagename: "camera.fill"))
business.append(Business(businesstype: "Swim Trainer", businessdesc: "Personal or group swim lessons", imagename: "phone.fill"))
business.append(Business(businesstype: "Marital Arts", businessdesc: "Learn Martial Arts Karte/Tae-Kon-Do", imagename: "calendar"))
business.append(Business(businesstype: "Video Grapher", businessdesc: "Get professional videographer for your events", imagename: "video.fill"))
business.append(Business(businesstype: "Photographer", businessdesc: "Get professional photographer for your events", imagename: "envelope.fill"))
business.append(Business(businesstype: "Mountainer", businessdesc: "Learn how to climb mountains", imagename: "gearshape.fill"))
business.append(Business(businesstype: "Limo Service", businessdesc: "Provide Limo services", imagename: "cart.fill"))
business.append(Business(businesstype: "Comparer", businessdesc: "For your functions and programs", imagename: "giftcard.fill"))
business.append(Business(businesstype: "Disc Jockey", businessdesc: "Get DJ your private parties anf functions", imagename: "sunrise.fill"))
business.append(Business(businesstype: "Tailor", businessdesc: "Provide custom tailoring services", imagename: "paintbrush.fill"))
business.append(Business(businesstype: "Painter", businessdesc: "Learn how to paint", imagename: "wrench.fill"))
business.append(Business(businesstype: "Vent Cleaners", businessdesc: "Provide Vent cleaning services", imagename: "hammer.fill"))
business.append(Business(businesstype: "Repair", businessdesc: "Provide general household repairs", imagename: "house.fill"))
business.append(Business(businesstype: "Locksmith", businessdesc: "Creating Keys or handle locked out situation", imagename: "lock.fill"))
business.append(Business(businesstype: "Pest Control", businessdesc: "Provide pest control services", imagename: "sparkles"))
}
// Business Type Text Field Functions
@objc func businessTypeEditingDidBegin(sender: UITextField){
tempBusiness = business
businessTypeTableView.isHidden = false
saveButton.isHidden = true
}
@objc func businessTypeEditingDidEnd(sender: UITextField){
business = tempBusiness
businessTypeTableView.isHidden = true
saveButton.isHidden = false
}
@objc func businessTypeEditingChanged(sender: UITextField){
business = business.filter{
business in return business.businesstype.trimmingCharacters(in: .whitespaces).lowercased().contains(sender.text!.lowercased())
}
if(sender.text == ""){
business = tempBusiness
}
businessTypeTableView.reloadData()
}
//var profile:Profile?
func profileSave(){
let personalData = personalProfileImageView.image?.pngData()
let personalFilename = getDocumentsDirectory().appendingPathComponent("copy.png")
try? personalData?.write(to: personalFilename)
let personalProfile : CKAsset?  = CKAsset(fileURL: personalFilename)
let profile:Profile = Profile(userid: userid, personalprofile: personalProfile!, name: nameTextField.text!, businessname: businessNameTextField.text!, businesstype: businessTypeTextField.text!)
profile.group.enter()
profile.saveProfile(profile: profile)
profile.group.notify(queue: .main) {
self.profile = profile.profile
self.popUpContact()
}
}
func getDocumentsDirectory() -> URL {
let paths = FileManager.default.urls(for: .documentDirectory, in: .userDomainMask)
return paths[0]
}
}
extension CreateProfileViewController:UIImagePickerControllerDelegate, UINavigationControllerDelegate{
func presentPhotoOptions(){
var message:String = ""
message = "Personal Profile Picture"
let actionSheet = UIAlertController(title: message, message: "Select your Option", preferredStyle: .actionSheet)
actionSheet.addAction(UIAlertAction(title: "cancel", style: .cancel, handler: nil))
actionSheet.addAction(UIAlertAction(title: "Take Photo", style: .default, handler: { [weak self]_ in
self?.presentCamera()
}))
actionSheet.addAction(UIAlertAction(title: "Choose Photo", style: .default, handler: {[weak self]_ in
self?.presentPhoto()
}))
present(actionSheet, animated: true)
}
func presentCamera(){
let ip = UIImagePickerController()
ip.sourceType = .camera
ip.delegate = self
ip.allowsEditing = true
present(ip, animated: true)
}
func presentPhoto(){
let ip = UIImagePickerController()
ip.sourceType = .photoLibrary
ip.delegate = self
ip.allowsEditing = true
present(ip, animated: true)
}
func imagePickerController(_ picker: UIImagePickerController, didFinishPickingMediaWithInfo info: [UIImagePickerController.InfoKey : Any]) {
let selectedImage  = info[UIImagePickerController.InfoKey.editedImage]
self.personalProfileImageView.image = selectedImage as? UIImage
picker.dismiss(animated: true)
}
func imagePickerControllerDidCancel(_ picker: UIImagePickerController) {
picker.dismiss(animated: true)
}
}
```

### ContactViewController

ContactViewController provides the user an interface to search for contact based on a business type. Once found, the user may choose to save it to the iCloud repository.

#### Class Definition

The provided code defines a **ContactViewController** class, which serves as a view controller for managing contacts within an application. The breakdown of the code is given below:

*   **Imports:** Imports the **UIKit** framework, essential for building user interfaces

*   **Class definition:** Defines the **ContactViewController** class, inheriting from **UIViewController** and conforming to several protocols:
    *   **UITabBarDelegate:** For handling tab bar interactions

    *   **UITableViewDelegate** and **UITableViewDataSource:** For managing table view data and interactions

    *   **UITextFieldDelegate:** For handling text field interactions

**Class variable**

*   **contact:** An instance of a Contact class as defined in the Modal section

*   **profile:** A reference to the current user’s profile

*   **userid:** The user’s ID

*   **origniatorProfile:** The profile of the contact initiator

*   **receipentProfile:** The profile of the contact recipient

```
import UIKit
class ContactViewController: UIViewController, UITabBarDelegate, UITableViewDelegate, UITableViewDataSource, UITextFieldDelegate {
var contact = Contact()
var profile:Profile?
var userid:String = ""
var origniatorProfile:Profile?
var receipentProfile:Profile?
```

#### UI Object Definition

The below provided code defines several UI elements that will be used in the ContactViewController to create a user interface for managing contacts. These elements include a tab bar, a header label, a text field for searching contacts, and two table views for displaying business types and contacts.

**Tab bar**

*   Creates a **UITabBar** with a specific background color

*   Sets up three **UITabBarItem** instances for “Profile”, “Contacts”, and “Chats” with corresponding images and tags

**Header label**

*   Creates a **UILabel** for displaying the header text “**Chat**”

*   Sets text color, alignment, and font

**Business type text field**

*   Creates a **UITextField** for searching contacts based on business type

*   Sets placeholder text, border style, and other appearance properties

**Business type table view**

*   Creates a UITableView for displaying business types

*   Sets background color and enables scrolling

**Contact table view**

*   Creates a **UITableView** for displaying contacts

*   Sets background color and enables scrolling

Some key points to note in these definitions are

*   **Lazy loading:** The use of private let with closures creates lazy-loaded properties, meaning the objects are only created when first accessed.

*   **Auto Layout:** The **translatesAutoresizingMaskIntoConstraints** = false property is used for all UI elements, indicating that Auto Layout will be used for positioning and sizing.

*   **UI Customization:** The code sets various properties to customize the appearance of the UI elements, such as colors, fonts, and border styles.

```
private let tabBar:UITabBar = {
let tabbar = UITabBar()
tabbar.translatesAutoresizingMaskIntoConstraints = false
tabbar.backgroundColor = UIColor.init(red: 171/255, green: 189/255, blue: 217/255, alpha: 1)
let saveSymbolConfiguration = UIImage.SymbolConfiguration(pointSize: 16, weight: .black)
let contact = UIImage(systemName: "doc.on.clipboard", withConfiguration: saveSymbolConfiguration)
let contactbutton = UITabBarItem(title: "Contacts", image: contact, selectedImage: contact)
contactbutton.tag = 1
let chat = UIImage(systemName: "doc.plaintext", withConfiguration: saveSymbolConfiguration)
let chatbutton = UITabBarItem(title: "Chats", image: chat, selectedImage: chat)
chatbutton.tag = 2
let profile = UIImage(systemName: "person.fill", withConfiguration: saveSymbolConfiguration)
let profilebutton = UITabBarItem(title: "Profile", image: profile, selectedImage: profile)
profilebutton.tag = 0
tabbar.setItems([profilebutton, contactbutton, chatbutton], animated: false)
return tabbar
}()
private let headerLabel:UILabel = {
let label = UILabel()
label.text = "Chat"
label.translatesAutoresizingMaskIntoConstraints = false
label.textColor = UIColor(displayP3Red: 128/255, green: 0/255, blue: 128/255, alpha: 1)
label.textAlignment = .center
label.font = UIFont.boldSystemFont(ofSize: 35)
return label
}()
let businessTypeTextField:UITextField = {
let textField = UITextField()
textField.translatesAutoresizingMaskIntoConstraints = false
textField.borderStyle = .roundedRect
textField.textColor = .black
textField.layer.borderColor  = UIColor.systemGray4.cgColor
textField.layer.borderWidth = 1
textField.layer.cornerRadius = 10
textField.placeholder = "Select Business Type to Search Contacts"
textField.textColor = .black
textField.isEnabled = true
textField.keyboardType = .default
return textField
}()
private let businessTypeTableView:UITableView = {
let tableView = UITableView()
tableView.translatesAutoresizingMaskIntoConstraints = false
tableView.backgroundColor = .white
tableView.isScrollEnabled = true
return tableView
}()
private let contactTableView:UITableView = {
let tableView = UITableView()
tableView.translatesAutoresizingMaskIntoConstraints = false
tableView.backgroundColor = .white
tableView.isScrollEnabled = true
return tableView
}()
```

#### Lifecycle Method

This code defines the viewDidLoad() method, which is a lifecycle method called when the view controller’s view is loaded into memory. The key element of the code is defined below:

*   **super.viewDidLoad():** Calls the **viewDidLoad()** method of the superclass to ensure proper initialization

*   **setup():** Calls a custom method named setup(), to perform initial setup tasks for the view controller

*   **drawTabBar():** Calls a custom method named **drawTabBar()** and adds and positions the UI objects within the view

```
override func viewDidLoad() {
super.viewDidLoad()
setup()
drawTabBar()
}
```

#### Setup Method

The **setup**() method performs initial configurations for the **ContactViewController**. It sets up delegates, data sources, and initial states for UI elements. Key code elements are defined below:

*   **view.backgroundColor = .white:** Sets the background color of the view to white

*   **tabBar.delegate = self:** Sets the current view controller as the delegate for the tab bar

*   **tabBar.selectedItem = tabBar.items![1]:** Sets the second tab bar item as the selected one

*   **businessTypeTextField.delegate = self:** Sets the current view controller as the delegate for the business type text field

*   **businessTypeTextField.leftView = UIImageView(image: UIImage(systemName: “plus”)):** Adds a plus icon to the left of the text field

*   **businessTypeTextField.leftViewMode = .always:** Ensures the plus icon is always visible

*   **businessTypeTextField.addTarget(...):** Adds target action methods for text field editing events

*   **businessTypeTableView.delegate = self:** Sets the current view controller as the delegate for the business type table view

*   **businessTypeTableView.dataSource = self:** Sets the current view controller as the data source for the business type table view

*   **businessTypeTableView.register(UITableViewCell.self, forCellReuseIdentifier: “business”):** Registers a cell for reuse in the business type table view

*   **businessTypeTableView.layer.borderWidth = 0:** Removes the border from the business type table view

*   **businessTypeTableView.layer.borderColor = UIColor.white.cgColor:** Sets the border color to white (though it’s not visible due to the border width being 0)

*   **businessTypeTableView.separatorStyle = .none:** Removes separator lines from the business type table view

*   **businessTypeTableView.isHidden = true:** Initially hides the business type table view

*   **contactTableView.delegate = self:** Sets the current view controller as the delegate for the contact table view

*   **contactTableView.dataSource = self:** Sets the current view controller as the data source for the contact table view

*   **contactTableView.register(ContactTableViewCell.self, forCellReuseIdentifier: “contact”):** Registers a custom cell class (**ContactTableViewCell**) for reuse in the contact table view

*   **contactTableView.layer.borderWidth = 0:** Removes the border from the contact table view

*   **contactTableView.layer.borderColor = UIColor.white.cgColor:** Sets the border color to white (though it’s not visible due to the border width being 0)

*   **contactTableView.separatorStyle = .singleLine:** Adds single-line separators between contact table view cells

*   **contactTableView.isHidden = true:** Initially hides the contact table view

*   **setBusiness():** Calls a method to populate the business array with data

```
func setup(){
view.backgroundColor = .white
tabBar.delegate = self
tabBar.selectedItem = tabBar.items![1]
businessTypeTextField.delegate = self
businessTypeTextField.leftView = UIImageView(image: UIImage(systemName: "plus"))
businessTypeTextField.leftViewMode = .always
businessTypeTextField.addTarget(self, action: #selector(businessTypeEditingChanged), for: .editingChanged)
businessTypeTextField.addTarget(self, action: #selector(businessTypeEditingDidBegin), for: .editingDidBegin)
businessTypeTextField.addTarget(self, action: #selector(businessTypeEditingDidEnd), for: .editingDidEnd)
businessTypeTableView.delegate = self
businessTypeTableView.dataSource = self
businessTypeTableView.register(UITableViewCell.self, forCellReuseIdentifier: "business")
businessTypeTableView.layer.borderWidth = 0
businessTypeTableView.layer.borderColor = UIColor.white.cgColor
businessTypeTableView.separatorStyle = .none
businessTypeTableView.isHidden = true
contactTableView.delegate = self
contactTableView.dataSource = self
contactTableView.register(ContactTableViewCell.self, forCellReuseIdentifier: "contact")
contactTableView.layer.borderWidth = 0
contactTableView.layer.borderColor = UIColor.white.cgColor
contactTableView.separatorStyle = .singleLine
contactTableView.isHidden = true
setBusiness()
}
```

#### Tab Bar Method

The **tabBar(_:didSelect:)** method handles the selection of tab bar items:

*   **Case 0:** Triggers the **popUpContact**() function

*   **Case 2:** Triggers the **popUpChat**() function

*   **Default:** Triggers the **popUpMarket**() function (currently not implemented)

**Pop-up functions**

*   **popUpContact**(): Currently empty, as it is the current view

*   **popUpChat**(): Creates a **ChatViewController** instance, passes relevant profile information, and presents it modally

*   **popUpMarket():** Currently empty and not implemented

```
func tabBar(_ tabBar: UITabBar, didSelect item: UITabBarItem) {
switch item.tag {
case 0:
popUpContact()
case 2:
popUpChat()
default:
popUpMarket()
}
}
func popUpContact(){
}
func popUpChat(){
let viewController = ChatViewController()
viewController.profile = profile
viewController.origniatorProfile = origniatorProfile
viewController.receipentProfile = receipentProfile
viewController.modalPresentationStyle = .overCurrentContext
viewController.modalTransitionStyle = .crossDissolve
self.present(viewController, animated: true, completion: nil)
}
func popUpMarket(){
}
```

#### Draw Screen

The **drawTabBar**() function is responsible for arranging the UI elements within the **ContactViewController** using Auto Layout constraints. The code is described below:

*   **Screen size calculation:** Calculates the screen width and height for layout purposes

*   **Adding subviews:** Adds the header label, business type text field, business type table view, contact table view, and tab bar as **subviews** to the main view

*   **Creating constraints:** Defines constraints for each **subview** using **NSLayoutConstraint** to position and size them within the view

*   **Activating constraints:** Activates the created constraints to apply the layout

```
func drawTabBar(){
let screensize: CGRect = UIScreen.main.bounds
let screenWidth = screensize.width
let screenHeight = screensize.height
view.addSubview(headerLabel)
let constraints = [
headerLabel.topAnchor.constraint(equalTo: view.topAnchor, constant: 45),
headerLabel.widthAnchor.constraint(equalToConstant: screenWidth),
headerLabel.heightAnchor.constraint(equalToConstant: 40)
]
NSLayoutConstraint.activate(constraints)
view.addSubview(businessTypeTextField)
let constraints2 = [
businessTypeTextField.topAnchor.constraint(equalTo: headerLabel.bottomAnchor, constant: 10),
businessTypeTextField.leftAnchor.constraint(equalTo: view.leftAnchor, constant: 10),
businessTypeTextField.widthAnchor.constraint(equalToConstant: screenWidth - 20),
businessTypeTextField.heightAnchor.constraint(equalToConstant: 60)
]
NSLayoutConstraint.activate(constraints2)
view.addSubview(businessTypeTableView)
let constraints3 = [
businessTypeTableView.topAnchor.constraint(equalTo: businessTypeTextField.bottomAnchor, constant: 0),
businessTypeTableView.leftAnchor.constraint(equalTo: view.leftAnchor, constant: 5),
businessTypeTableView.widthAnchor.constraint(equalToConstant: screenWidth - 10),
businessTypeTableView.heightAnchor.constraint(equalToConstant: 300)
]
NSLayoutConstraint.activate(constraints3)
view.addSubview(contactTableView)
let constraints4 = [
contactTableView.topAnchor.constraint(equalTo: businessTypeTextField.bottomAnchor, constant: 0),
contactTableView.leftAnchor.constraint(equalTo: view.leftAnchor, constant: 5),
contactTableView.widthAnchor.constraint(equalToConstant: screenWidth - 10),
contactTableView.heightAnchor.constraint(equalToConstant: screenHeight - 50)
]
NSLayoutConstraint.activate(constraints4)
view.addSubview(tabBar)
let constraints5 = [
tabBar.bottomAnchor.constraint(equalTo: view.bottomAnchor, constant: 5),
tabBar.leftAnchor.constraint(equalTo: view.leftAnchor, constant: 5),
tabBar.widthAnchor.constraint(equalToConstant: screenWidth),
tabBar.heightAnchor.constraint(equalToConstant: 80)
]
NSLayoutConstraint.activate(constraints5)
}
```

#### Table Methods

There are two tables we will display on the user screen. The first one is to show the business types for contact selection, and the second table shows the contacts of a selected business type.

**Number of rows**

This code defines the **numberOfRowsInSection** delegate method for a table view, determining the number of rows to display based on which table view is being queried.

*   **if(tableView == businessTypeTableView):** Checks if the current table view is the businessTypeTableView
    *   If true, it returns the count of the **business** array

    *   If false, it returns the count of the **contact.profiles** array

```
func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
if(tableView == businessTypeTableView){
return business.count
}else{
return contact.profiles.count
}
}
```

**Displaying content in cell**

This code defines the **cellForRowAt** delegate method, responsible for configuring and returning table view cells based on the specified index path.

*   **Conditional check:** Determines which table view is being queried

*   **BusinessTypeTableView:** Dequeues a reusable cell with the identifier “**business**”, sets the cell’s text label to a combination of **businesstype** and **businessdesc** from the business array, and returns the cell

*   **ContactTableView:** Dequeues a reusable cell with the identifier “**contact**” and casts it to **ContactTableViewCell**
    *   Sets the **nameButton** title to the contact’s name

    *   Retrieves image data from the **personalprofile’s** file URL and sets it as the **profileImageView’s** image

    *   Sets the corner radius of the **profileImageView**

    *   Adds a target action for the **chatButton** to trigger the **chatAction** method

    *   Sets the tag property of the **chatButton** to the index path row for later identification

    *   Returns the configured cell

```
func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
if(tableView == businessTypeTableView){
let cell = tableView.dequeueReusableCell(withIdentifier: "business", for: indexPath)
cell.textLabel?.text = business[indexPath.row].businesstype + " " + business[indexPath.row].businessdesc
return cell
}else{
let cell = tableView.dequeueReusableCell(withIdentifier: "contact", for: indexPath) as! ContactTableViewCell
cell.nameButton.setTitle(contact.profiles[indexPath.row].name, for: .normal)
let file = contact.profiles[indexPath.row].personalprofile.fileURL
if let data = NSData(contentsOf: file!) {
cell.profileImageView.image = UIImage(data: data as Data)
}
cell.profileImageView.layer.cornerRadius =  60
cell.chatButton.addTarget(self, action: #selector(chatAction), for: .touchUpInside)
cell.chatButton.tag = indexPath.row
return cell
}
}
```

**Row height method**

This code defines the **heightForRowAt** delegate method, determining the height of each row in the specified table view.

*   **Conditional check:** Determines which table view is being queried

*   **BusinessTypeTableView:** Returns a fixed height of 40 points for each row

*   **ContactTableView:** Returns a fixed height of 155 points for each row

```
func tableView(_ tableView: UITableView, heightForRowAt indexPath: IndexPath) -> CGFloat {
if(tableView == businessTypeTableView){
return 40
}else{
return 155
}
}
```

**Row selection method**

This code defines the **didSelectRowAt** delegate method for the table view, handling user selection of a row in either the business type or contact table view.

*   **Conditional check:** Verifies if the selected table view is **businessTypeTableView**

*   **Clear contact data:** Removes all elements from the **contact.profiles** array

*   **Update text field:** Sets the text field text to the selected business type from the **business** array

*   **Dismiss keyboard:** Calls **resignFirstResponder** on the text field to hide the keyboard if it’s open

*   **Enter group:** Enters a group using **contact.group.enter()**

*   **Get contacts:** Calls contact.getContacts(businessType:) to retrieve contacts based on the selected business type

*   **Notify on main queue:** Uses a notification block with **notify(queue: .main)** to execute code on the main thread after the **getContacts** operation completes

*   **Filter contacts:** Filters the **contact.profiles** array to exclude the user’s own profile

*   **Show contact table view:** Sets **contactTableView.isHidden** to false to make it visible

*   **Reload contact table view:** Calls **contactTableView.reloadData()** to update the table view with the filtered data

```
func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath) {
if(tableView == businessTypeTableView){
contact.profiles.removeAll()
businessTypeTextField.text = business[indexPath.row].businesstype
businessTypeTextField.resignFirstResponder()
self.contact.group.enter()
self.contact.getContacts(businessType: businessTypeTextField.text!)
self.contact.group.notify(queue: .main) { [self] in
contact.profiles = contact.profiles.filter{
contact in return contact.userid != profile?.userid
}
contactTableView.isHidden = false
contactTableView.reloadData()
}
}
}
```

#### Chat Button Action Event

This function handles the action when the “chat” button in a contact cell is tapped. It initiates a chat with the selected contact by presenting a **ChatDetailViewController**. The code details are given below:

*   **Create ChatDetailViewController:** Instantiates a **ChatDetailViewController** instance

*   **Set presentation styles:** Sets the presentation and transition styles for the view controller

*   **Assign profile information:** Assigns **origniatorProfile** and **receipentProfile** with the current user’s profile and the selected contact’s profile, respectively

*   **Pass data to ChatViewController:** Passes necessary data (profiles, business name, originator name, recipient profile image) to the **ChatDetailViewController**

*   **Present ChatViewController:** Presents the **ChatDetailViewController** modally.

```
@objc func chatAction(sender: UIButton){
let viewController = ChatDetailViewController()
viewController.modalPresentationStyle = .overCurrentContext
viewController.modalTransitionStyle = .crossDissolve
origniatorProfile = profile
receipentProfile = contact.profiles[sender.tag]
viewController.profile = profile
viewController.origniatorProfile = profile
viewController.receipentProfile = contact.profiles[sender.tag]
viewController.businessname = contact.profiles[sender.tag].businessname
viewController.originatorname = (profile?.name)!
viewController.recipientProfileImage = contact.profiles[sender.tag].personalprofile
self.present(viewController, animated: true, completion: nil)
}
```

#### Text Field Event Methods

These three functions handle the user interaction with the **businessTypeTextField** and manage the visibility of the **businessTypeTableView** and **contactTableView**.

**businessTypeEditingDidBegin(sender:)**

*   Creates a copy of the business array into **tempBusiness** to preserve original data

*   Shows the **businessTypeTableView** for selecting business types

*   Hides the **contactTableView** while the user is selecting a business type

**businessTypeEditingDidEnd(sender:)**

*   Restores the original business array from **tempBusiness**

*   Hides the **businessTypeTableView**

*   Shows the **contactTableView** after business type selection is complete

**businessTypeEditingChanged(sender:)**

*   Filters the business array based on the text entered in the **businessTypeTextField**

*   If the text field is empty, it restores the original business array from **tempBusiness**

*   Reloads the **businessTypeTableView** to reflect the filtered data

```
@objc func businessTypeEditingDidBegin(sender: UITextField){
tempBusiness = business
businessTypeTableView.isHidden = false
contactTableView.isHidden = true
}
@objc func businessTypeEditingDidEnd(sender: UITextField){
business = tempBusiness
businessTypeTableView.isHidden = true
contactTableView.isHidden = false
}
@objc func businessTypeEditingChanged(sender: UITextField){
business = business.filter{
business in return business.businesstype.trimmingCharacters(in: .whitespaces).lowercased().contains(sender.text!.lowercased())
}
if(sender.text == ""){
business = tempBusiness
}
businessTypeTableView.reloadData()
}
```

#### Business Method

The **setBusiness**() function populates a business array with Business objects. Each Business object has properties for **businesstype**, **businessdesc**, and **imagename.** The details of the method are explained below:

*   **var business:[Business] = []:** Initializes an empty array of Business objects.

*   **var tempBusiness:[Business] = []:** Initializes an empty temporary array of Business objects. This array is currently unused in the provided code.

*   **setBusiness():** Defines a function to populate the business array.

*   **Business(businesstype: ..., businessdesc: ..., imagename: ...):** Creates Business objects with specified properties.

*   **business.append(...):** Adds the created Business objects to the business array.

```
var business:[Business] = []
var tempBusiness:[Business] = []
func setBusiness(){
business.append(Business(businesstype: "Mortgage", businessdesc: "Provide Mortgage Solution", imagename: "scribble"))
business.append(Business(businesstype: "Insurance", businessdesc: "Provide Life/Health/Car/P&C Insurance", imagename: "folder"))
business.append(Business(businesstype: "Electrician", businessdesc: "Provide Household electrical solution", imagename: "doc.text"))
business.append(Business(businesstype: "Handyman", businessdesc: "Provide any household related work", imagename: "book"))
business.append(Business(businesstype: "Artisian", businessdesc: "Art clasess/ Pottery Classes", imagename: "graduationcap"))
business.append(Business(businesstype: "Magician", businessdesc: "Magic trick for birthday parties", imagename: "square.and.arrow.down.fill"))
business.append(Business(businesstype: "Tutor", businessdesc: "Providing Tutoring Service", imagename: "paperclip"))
business.append(Business(businesstype: "Plumber", businessdesc: "Provide plumbing Solution", imagename: "links"))
business.append(Business(businesstype: "Cleaners", businessdesc: "Provide House cleaning services", imagename: "person"))
business.append(Business(businesstype: "Architect", businessdesc: "House Design and architect services", imagename: "globe"))
business.append(Business(businesstype: "Physcial Trainer", businessdesc: "Persnoal trainer", imagename: "music.note.list"))
business.append(Business(businesstype: "Nutriontist", businessdesc: "Provide Nutriotional guidance and service", imagename: "square.and.pencil"))
business.append(Business(businesstype: "Spa", businessdesc: "Get home based spa services", imagename: "swift"))
business.append(Business(businesstype: "Decor", businessdesc: "Provide Home Design and Decor Service", imagename: "magnifyingglass"))
business.append(Business(businesstype: "Builder", businessdesc: "General Contractor", imagename: "heart.fill"))
business.append(Business(businesstype: "Pottery", businessdesc: "Learn how to create Pottery based artwork", imagename: "star.fill"))
business.append(Business(businesstype: "Dog Walker", businessdesc: "Provide Dog Care", imagename: "flag.fill"))
business.append(Business(businesstype: "Baker", businessdesc: "Get Custom Made Cakes or party needs", imagename: "folder.fill"))
business.append(Business(businesstype: "Dance Trainer", businessdesc: "Get training on various western/indian classical dance forms", imagename: "bell.fill"))
business.append(Business(businesstype: "Nurse", businessdesc: "Get In house nursing service", imagename: "tag.fill"))
business.append(Business(businesstype: "Gardner", businessdesc: "Uplift your flower or vegetable beds", imagename: "bolt.fill"))
business.append(Business(businesstype: "Snow remover", businessdesc: "Get Snow removal services", imagename: "camera.fill"))
business.append(Business(businesstype: "Swim Trainer", businessdesc: "Personal or group swim lessons", imagename: "phone.fill"))
business.append(Business(businesstype: "Marital Arts", businessdesc: "Learn Martial Arts Karte/Tae-Kon-Do", imagename: "calendar"))
business.append(Business(businesstype: "Video Grapher", businessdesc: "Get professional videographer for your events", imagename: "video.fill"))
business.append(Business(businesstype: "Photographer", businessdesc: "Get professional photographer for your events", imagename: "envelope.fill"))
business.append(Business(businesstype: "Mountainer", businessdesc: "Learn how to climb mountains", imagename: "gearshape.fill"))
business.append(Business(businesstype: "Limo Service", businessdesc: "Provide Limo services", imagename: "cart.fill"))
business.append(Business(businesstype: "Comparer", businessdesc: "For your functions and programs", imagename: "giftcard.fill"))
business.append(Business(businesstype: "Disc Jockey", businessdesc: "Get DJ your private parties anf functions", imagename: "sunrise.fill"))
business.append(Business(businesstype: "Tailor", businessdesc: "Provide custom tailoring services", imagename: "paintbrush.fill"))
business.append(Business(businesstype: "Painter", businessdesc: "Learn how to paint", imagename: "wrench.fill"))
business.append(Business(businesstype: "Vent Cleaners", businessdesc: "Provide Vent cleaning services", imagename: "hammer.fill"))
business.append(Business(businesstype: "Repair", businessdesc: "Provide general household repairs", imagename: "house.fill"))
business.append(Business(businesstype: "Locksmith", businessdesc: "Creating Keys or handle locked out situation", imagename: "lock.fill"))
business.append(Business(businesstype: "Pest Control", businessdesc: "Provide pest control services", imagename: "sparkles"))
}
}
```

#### Running the App

The Contact tab will show a search box to search by business type with whom you would like to converse. The below screen shows that look up screen.

![](images/533539_2_En_13_Chapter/533539_2_En_13_Figd_HTML.jpg)

Use a different phone and create your profile and then look up for the business type Tutor (as from the first phone, we have created Shantanu as a Tutor). Please see the screen below for details. Note how the list filters based on the first two letters that we entered in the input box.

| ![](images/533539_2_En_13_Chapter/533539_2_En_13_Fige_HTML.jpg) | ![](images/533539_2_En_13_Chapter/533539_2_En_13_Figf_HTML.jpg) |

When we select the Tutoring service, we will see Shantanu as a contact with whom we can initiate a chat by clicking on the row. See screen below for details.

![](images/533539_2_En_13_Chapter/533539_2_En_13_Figg_HTML.jpg)

#### Complete Code

Find below the complete code of the ContactViewController:

```
//
//  ContactViewController.swift
//  Chat
//
//  Created by Shantanu Baruah on 5/31/24.
//
import UIKit
class ContactViewController: UIViewController, UITabBarDelegate, UITableViewDelegate, UITableViewDataSource, UITextFieldDelegate {
var contact = Contact()
var profile:Profile?
var userid:String = ""
var origniatorProfile:Profile?
var receipentProfile:Profile?
private let tabBar:UITabBar = {
let tabbar = UITabBar()
tabbar.translatesAutoresizingMaskIntoConstraints = false
tabbar.backgroundColor = UIColor.init(red: 171/255, green: 189/255, blue: 217/255, alpha: 1)
let saveSymbolConfiguration = UIImage.SymbolConfiguration(pointSize: 16, weight: .black)
let contact = UIImage(systemName: "doc.on.clipboard", withConfiguration: saveSymbolConfiguration)
let contactbutton = UITabBarItem(title: "Contacts", image: contact, selectedImage: contact)
contactbutton.tag = 1
let chat = UIImage(systemName: "doc.plaintext", withConfiguration: saveSymbolConfiguration)
let chatbutton = UITabBarItem(title: "Chats", image: chat, selectedImage: chat)
chatbutton.tag = 2
let profile = UIImage(systemName: "person.fill", withConfiguration: saveSymbolConfiguration)
let profilebutton = UITabBarItem(title: "Profile", image: profile, selectedImage: profile)
profilebutton.tag = 0
tabbar.setItems([profilebutton, contactbutton, chatbutton], animated: false)
return tabbar
}()
private let headerLabel:UILabel = {
let label = UILabel()
label.text = "Chat"
label.translatesAutoresizingMaskIntoConstraints = false
label.textColor = UIColor(displayP3Red: 128/255, green: 0/255, blue: 128/255, alpha: 1)
label.textAlignment = .center
label.font = UIFont.boldSystemFont(ofSize: 35)
return label
}()
let businessTypeTextField:UITextField = {
let textField = UITextField()
textField.translatesAutoresizingMaskIntoConstraints = false
textField.borderStyle = .roundedRect
textField.textColor = .black
textField.layer.borderColor  = UIColor.systemGray4.cgColor
textField.layer.borderWidth = 1
textField.layer.cornerRadius = 10
textField.placeholder = "Select Business Type to Search Contacts"
textField.textColor = .black
textField.isEnabled = true
textField.keyboardType = .default
return textField
}()
private let businessTypeTableView:UITableView = {
let tableView = UITableView()
tableView.translatesAutoresizingMaskIntoConstraints = false
tableView.backgroundColor = .white
tableView.isScrollEnabled = true
return tableView
}()
private let contactTableView:UITableView = {
let tableView = UITableView()
tableView.translatesAutoresizingMaskIntoConstraints = false
tableView.backgroundColor = .white
tableView.isScrollEnabled = true
return tableView
}()
override func viewDidLoad() {
super.viewDidLoad()
print(self.profile?.name ?? "None")
setup()
drawTabBar()
}
func setup(){
view.backgroundColor = .white
tabBar.delegate = self
tabBar.selectedItem = tabBar.items![1]
businessTypeTextField.delegate = self
businessTypeTextField.leftView = UIImageView(image: UIImage(systemName: "plus"))
businessTypeTextField.leftViewMode = .always
businessTypeTextField.addTarget(self, action: #selector(businessTypeEditingChanged), for: .editingChanged)
businessTypeTextField.addTarget(self, action: #selector(businessTypeEditingDidBegin), for: .editingDidBegin)
businessTypeTextField.addTarget(self, action: #selector(businessTypeEditingDidEnd), for: .editingDidEnd)
businessTypeTableView.delegate = self
businessTypeTableView.dataSource = self
businessTypeTableView.register(UITableViewCell.self, forCellReuseIdentifier: "business")
businessTypeTableView.layer.borderWidth = 0
businessTypeTableView.layer.borderColor = UIColor.white.cgColor
businessTypeTableView.separatorStyle = .none
businessTypeTableView.isHidden = true
contactTableView.delegate = self
contactTableView.dataSource = self
contactTableView.register(ContactTableViewCell.self, forCellReuseIdentifier: "contact")
contactTableView.layer.borderWidth = 0
contactTableView.layer.borderColor = UIColor.white.cgColor
contactTableView.separatorStyle = .singleLine
contactTableView.isHidden = true
setBusiness()
}
func tabBar(_ tabBar: UITabBar, didSelect item: UITabBarItem) {
switch item.tag {
case 0:
popUpContact()
case 2:
popUpChat()
default:
popUpMarket()
}
}
func popUpContact(){
}
func popUpChat(){
let viewController = ChatViewController()
viewController.profile = profile
viewController.origniatorProfile = origniatorProfile
viewController.receipentProfile = receipentProfile
viewController.modalPresentationStyle = .overCurrentContext
viewController.modalTransitionStyle = .crossDissolve
self.present(viewController, animated: true, completion: nil)
}
func popUpMarket(){
}
func drawTabBar(){
let screensize: CGRect = UIScreen.main.bounds
let screenWidth = screensize.width
let screenHeight = screensize.height
view.addSubview(headerLabel)
let constraints = [
headerLabel.topAnchor.constraint(equalTo: view.topAnchor, constant: 45),
headerLabel.widthAnchor.constraint(equalToConstant: screenWidth),
headerLabel.heightAnchor.constraint(equalToConstant: 40)
]
NSLayoutConstraint.activate(constraints)
view.addSubview(businessTypeTextField)
let constraints2 = [
businessTypeTextField.topAnchor.constraint(equalTo: headerLabel.bottomAnchor, constant: 10),
businessTypeTextField.leftAnchor.constraint(equalTo: view.leftAnchor, constant: 10),
businessTypeTextField.widthAnchor.constraint(equalToConstant: screenWidth - 20),
businessTypeTextField.heightAnchor.constraint(equalToConstant: 60)
]
NSLayoutConstraint.activate(constraints2)
view.addSubview(businessTypeTableView)
let constraints3 = [
businessTypeTableView.topAnchor.constraint(equalTo: businessTypeTextField.bottomAnchor, constant: 0),
businessTypeTableView.leftAnchor.constraint(equalTo: view.leftAnchor, constant: 5),
businessTypeTableView.widthAnchor.constraint(equalToConstant: screenWidth - 10),
businessTypeTableView.heightAnchor.constraint(equalToConstant: 300)
]
NSLayoutConstraint.activate(constraints3)
view.addSubview(contactTableView)
let constraints4 = [
contactTableView.topAnchor.constraint(equalTo: businessTypeTextField.bottomAnchor, constant: 0),
contactTableView.leftAnchor.constraint(equalTo: view.leftAnchor, constant: 5),
contactTableView.widthAnchor.constraint(equalToConstant: screenWidth - 10),
contactTableView.heightAnchor.constraint(equalToConstant: screenHeight - 50)
]
NSLayoutConstraint.activate(constraints4)
view.addSubview(tabBar)
let constraints5 = [
tabBar.bottomAnchor.constraint(equalTo: view.bottomAnchor, constant: 5),
tabBar.leftAnchor.constraint(equalTo: view.leftAnchor, constant: 5),
tabBar.widthAnchor.constraint(equalToConstant: screenWidth),
tabBar.heightAnchor.constraint(equalToConstant: 80)
]
NSLayoutConstraint.activate(constraints5)
}
//MARK: - Table Functions
func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
if(tableView == businessTypeTableView){
return business.count
}else{
return contact.profiles.count
}
}
func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
if(tableView == businessTypeTableView){
let cell = tableView.dequeueReusableCell(withIdentifier: "business", for: indexPath)
cell.textLabel?.text = business[indexPath.row].businesstype + " " + business[indexPath.row].businessdesc
return cell
}else{
let cell = tableView.dequeueReusableCell(withIdentifier: "contact", for: indexPath) as! ContactTableViewCell
cell.nameButton.setTitle(contact.profiles[indexPath.row].name, for: .normal)
let file = contact.profiles[indexPath.row].personalprofile.fileURL
if let data = NSData(contentsOf: file!) {
cell.profileImageView.image = UIImage(data: data as Data)
}
cell.profileImageView.layer.cornerRadius =  60
cell.chatButton.addTarget(self, action: #selector(chatAction), for: .touchUpInside)
cell.chatButton.tag = indexPath.row
return cell
}
}
/*
The Height of the Cell Row is set to default 40 pixels
*/
func tableView(_ tableView: UITableView, heightForRowAt indexPath: IndexPath) -> CGFloat {
if(tableView == businessTypeTableView){
return 40
}else{
return 155
}
}
func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath) {
if(tableView == businessTypeTableView){
contact.profiles.removeAll()
businessTypeTextField.text = business[indexPath.row].businesstype
businessTypeTextField.resignFirstResponder()
self.contact.group.enter()
self.contact.getContacts(businessType: businessTypeTextField.text!)
self.contact.group.notify(queue: .main) { [self] in
contact.profiles = contact.profiles.filter{
contact in return contact.userid != profile?.userid
}
contactTableView.isHidden = false
contactTableView.reloadData()
}
}
}
@objc func chatAction(sender: UIButton){
let viewController = ChatDetailViewController()
viewController.modalPresentationStyle = .overCurrentContext
viewController.modalTransitionStyle = .crossDissolve
origniatorProfile = profile
receipentProfile = contact.profiles[sender.tag]
viewController.profile = profile
viewController.origniatorProfile = profile
viewController.receipentProfile = contact.profiles[sender.tag]
viewController.businessname = contact.profiles[sender.tag].businessname
viewController.originatorname = (profile?.name)!
viewController.recipientProfileImage = contact.profiles[sender.tag].personalprofile
self.present(viewController, animated: true, completion: nil)
}
//MARK: - Object Actions
// Business Type Text Field Functions
@objc func businessTypeEditingDidBegin(sender: UITextField){
tempBusiness = business
businessTypeTableView.isHidden = false
contactTableView.isHidden = true
}
@objc func businessTypeEditingDidEnd(sender: UITextField){
business = tempBusiness
businessTypeTableView.isHidden = true
contactTableView.isHidden = false
}
@objc func businessTypeEditingChanged(sender: UITextField){
business = business.filter{
business in return business.businesstype.trimmingCharacters(in: .whitespaces).lowercased().contains(sender.text!.lowercased())
}
if(sender.text == ""){
business = tempBusiness
}
businessTypeTableView.reloadData()
}
var business:[Business] = []
var tempBusiness:[Business] = []
func setBusiness(){
business.append(Business(businesstype: "Mortgage", businessdesc: "Provide Mortgage Solution", imagename: "scribble"))
business.append(Business(businesstype: "Insurance", businessdesc: "Provide Life/Health/Car/P&C Insurance", imagename: "folder"))
business.append(Business(businesstype: "Electrician", businessdesc: "Provide Household electrical solution", imagename: "doc.text"))
business.append(Business(businesstype: "Handyman", businessdesc: "Provide any household related work", imagename: "book"))
business.append(Business(businesstype: "Artisian", businessdesc: "Art clasess/ Pottery Classes", imagename: "graduationcap"))
business.append(Business(businesstype: "Magician", businessdesc: "Magic trick for birthday parties", imagename: "square.and.arrow.down.fill"))
business.append(Business(businesstype: "Tutor", businessdesc: "Providing Tutoring Service", imagename: "paperclip"))
business.append(Business(businesstype: "Plumber", businessdesc: "Provide plumbing Solution", imagename: "links"))
business.append(Business(businesstype: "Cleaners", businessdesc: "Provide House cleaning services", imagename: "person"))
business.append(Business(businesstype: "Architect", businessdesc: "House Design and architect services", imagename: "globe"))
business.append(Business(businesstype: "Physcial Trainer", businessdesc: "Persnoal trainer", imagename: "music.note.list"))
business.append(Business(businesstype: "Nutriontist", businessdesc: "Provide Nutriotional guidance and service", imagename: "square.and.pencil"))
business.append(Business(businesstype: "Spa", businessdesc: "Get home based spa services", imagename: "swift"))
business.append(Business(businesstype: "Decor", businessdesc: "Provide Home Design and Decor Service", imagename: "magnifyingglass"))
business.append(Business(businesstype: "Builder", businessdesc: "General Contractor", imagename: "heart.fill"))
business.append(Business(businesstype: "Pottery", businessdesc: "Learn how to create Pottery based artwork", imagename: "star.fill"))
business.append(Business(businesstype: "Dog Walker", businessdesc: "Provide Dog Care", imagename: "flag.fill"))
business.append(Business(businesstype: "Baker", businessdesc: "Get Custom Made Cakes or party needs", imagename: "folder.fill"))
business.append(Business(businesstype: "Dance Trainer", businessdesc: "Get training on various western/indian classical dance forms", imagename: "bell.fill"))
business.append(Business(businesstype: "Nurse", businessdesc: "Get In house nursing service", imagename: "tag.fill"))
business.append(Business(businesstype: "Gardner", businessdesc: "Uplift your flower or vegetable beds", imagename: "bolt.fill"))
business.append(Business(businesstype: "Snow remover", businessdesc: "Get Snow removal services", imagename: "camera.fill"))
business.append(Business(businesstype: "Swim Trainer", businessdesc: "Personal or group swim lessons", imagename: "phone.fill"))
business.append(Business(businesstype: "Marital Arts", businessdesc: "Learn Martial Arts Karte/Tae-Kon-Do", imagename: "calendar"))
business.append(Business(businesstype: "Video Grapher", businessdesc: "Get professional videographer for your events", imagename: "video.fill"))
business.append(Business(businesstype: "Photographer", businessdesc: "Get professional photographer for your events", imagename: "envelope.fill"))
business.append(Business(businesstype: "Mountainer", businessdesc: "Learn how to climb mountains", imagename: "gearshape.fill"))
business.append(Business(businesstype: "Limo Service", businessdesc: "Provide Limo services", imagename: "cart.fill"))
business.append(Business(businesstype: "Comparer", businessdesc: "For your functions and programs", imagename: "giftcard.fill"))
business.append(Business(businesstype: "Disc Jockey", businessdesc: "Get DJ your private parties anf functions", imagename: "sunrise.fill"))
business.append(Business(businesstype: "Tailor", businessdesc: "Provide custom tailoring services", imagename: "paintbrush.fill"))
business.append(Business(businesstype: "Painter", businessdesc: "Learn how to paint", imagename: "wrench.fill"))
business.append(Business(businesstype: "Vent Cleaners", businessdesc: "Provide Vent cleaning services", imagename: "hammer.fill"))
business.append(Business(businesstype: "Repair", businessdesc: "Provide general household repairs", imagename: "house.fill"))
business.append(Business(businesstype: "Locksmith", businessdesc: "Creating Keys or handle locked out situation", imagename: "lock.fill"))
business.append(Business(businesstype: "Pest Control", businessdesc: "Provide pest control services", imagename: "sparkles"))
}
}
```

### ChatDetailViewController

ChatDetailViewController shows the conversation between the user and another person from the contact list. It inherits from **UIViewController** and conforms to **UITableViewDelegate**, **UITableViewDataSource**, and **UITextViewDelegate** protocols. The key properties are defined below:

*   **profile:** The current user’s profile

*   **origniatorProfile:** The profile of the chat initiator

*   **receipentProfile:** The profile of the chat recipient

*   **businessname:** The name of the business associated with the chat

*   **originatorname:** The name of the chat initiator

*   **recipientProfileImage:** The recipient’s profile image as a CloudKit asset

*   **whichTab:** Used to track from where the request has come

*   **chats:** An array to store chat messages or conversations

*   **chatFlag:** A Boolean flag for chat-related logic.

*   **recipientProfileImageBanner:** Another profile image asset

```
import UIKit
import CloudKit
class ChatDetailViewController: UIViewController, UITableViewDelegate, UITableViewDataSource, UITextViewDelegate {
var profile:Profile?
var origniatorProfile:Profile?
var receipentProfile:Profile?
var businessname:String = ""
var originatorname:String = ""
var recipientProfileImage:CKAsset?
var whichTab:Int?
var chats:[Conversation] = []
var chatFlag:Bool = true
var recipientProfileImageBanner:CKAsset?
```

#### UI Object Definition

This code defines several UI components for the ChatDetailViewController:

**Chat table view (chatTableView):**

This is the main element for displaying chat messages in a table view format

**Top bar (topBar):**

This view uses a light gray background color and serves as a header within the chat view.

**Profile image view (profileImageView):**

This image view displays a placeholder image (person silhouette) and is intended to show the recipient’s profile picture. It has rounded corners and a border.

**Top label (topLabel):**

This label displays the chat title. It is left-aligned and uses a bold font.

**Cancel image view (cancelImageView):**

This image view displays a close icon (xmark) and is used to close or dismiss the chat view.

**Shell text view (shellTextView):**

This text view is non-editable, has a border, and uses a smaller font size. This is to show a placeholder text view.

**Shell save button (shellSaveButton):**

This button has a disabled state and will be displayed next to shell text view. This is also a placeholder button.

**Chat text view (chatTextView):**

This text view is for the user to compose chat messages. It has a border and rounded corners and uses a smaller font size.

**Save button (saveButton):**

This button has a disabled state and is used to save the chat to the repository.

```
private let chatTableView:UITableView = {
let tableView = UITableView()
tableView.translatesAutoresizingMaskIntoConstraints = false
tableView.backgroundColor = .white
tableView.isScrollEnabled = true
return tableView
}()
let topBar:UIView = {
let view = UIView()
view.backgroundColor = UIColor.systemGray6
view.translatesAutoresizingMaskIntoConstraints = false
return view
}()
var profileImageView: UIImageView = {
let imageView = UIImageView()
imageView.image = UIImage(systemName: "person.fill")
imageView.contentMode = .scaleAspectFit
imageView.translatesAutoresizingMaskIntoConstraints = false
imageView.layer.masksToBounds = true
imageView.layer.borderWidth = 1
imageView.layer.borderColor = UIColor.systemGray.cgColor
imageView.layer.cornerRadius = 20
return imageView
}()
let topLabel:UILabel = {
let label = UILabel()
label.translatesAutoresizingMaskIntoConstraints = false
label.textAlignment = .left
label.textColor = .black
label.font = UIFont.systemFont(ofSize: 20, weight: .bold)
return label
}()
var cancelImageView: UIImageView = {
let imageView = UIImageView()
imageView.contentMode = .scaleAspectFit
imageView.translatesAutoresizingMaskIntoConstraints = false
imageView.layer.masksToBounds = true
imageView.image = UIImage(systemName: "xmark")
return imageView
}()
let shellTextView: UITextView = {
let textView = UITextView()
textView.translatesAutoresizingMaskIntoConstraints = false
textView.isScrollEnabled = false
textView.font = UIFont.systemFont(ofSize: 17)
textView.layer.borderWidth = 1
textView.layer.borderColor = UIColor.gray.cgColor
textView.layer.cornerRadius = 10
return textView
}()
let shellSaveButton:UIButton = {
let uiButton  = UIButton()
uiButton.translatesAutoresizingMaskIntoConstraints = false
uiButton.setTitleColor(UIColor.black, for: .normal)
uiButton.contentHorizontalAlignment = .left
uiButton.contentVerticalAlignment = .top
uiButton.titleLabel?.font = UIFont.systemFont(ofSize: 16, weight: .semibold)
uiButton.isEnabled = false
return uiButton
}()
let chatTextView: UITextView = {
let textView = UITextView()
textView.translatesAutoresizingMaskIntoConstraints = false
textView.isScrollEnabled = false
textView.font = UIFont.systemFont(ofSize: 17)
textView.layer.borderWidth = 1
textView.layer.borderColor = UIColor.gray.cgColor
textView.layer.cornerRadius = 10
return textView
}()
let saveButton:UIButton = {
let uiButton  = UIButton()
uiButton.translatesAutoresizingMaskIntoConstraints = false
uiButton.setTitleColor(UIColor.black, for: .normal)
uiButton.contentHorizontalAlignment = .left
uiButton.contentVerticalAlignment = .top
uiButton.titleLabel?.font = UIFont.systemFont(ofSize: 16, weight: .semibold)
uiButton.isEnabled = false
return uiButton
}()
```

#### Lifecycle Method

The **viewDidLoad**() method is used to perform initial setup tasks for the view controller. In the code below:

*   It initializes the view controller’s superclass.

*   It fetches chat data to populate the chat view.

The code breakdown is given below:

*   **super.viewDidLoad():** Calls the parent class’s **viewDidLoad**() method to ensure proper initialization

*   **queryChats**(): Calls a custom method named queryChats(), for fetching chat data from a data source

```
override func viewDidLoad() {
super.viewDidLoad()
queryChats()
}
```

#### Query Chat Method

The **queryChats**() function is responsible for fetching chat data for the current conversation and setting up the UI elements. Find below the breakdown of the code:

*   **Create conversation object:** Creates a conversation object (a custom modal class representing a chat conversation).

*   **Enter group:** Enters a group using chat.group.enter(). This involves managing data access or synchronization within CloudKit.

*   **Get chats:** Calls the getChats method on the Conversation object. It takes recipient name (businessname), originator name (originatorname), and a completion handler.

**Completion handler:** The completion handler receives two parameters:

*   **Chats:** An array of conversation objects

*   **Error:** An error object if there’s a problem fetching data.

The following are actions within completion handler:

*   **Update chats array:** Updates the **self.chats** property with the retrieved chat data

*   **Call setup function:** Calls a method named **setup**(), responsible for setting up the chat table view and other UI elements based on the chat data

*   **Call draw top bar function:** Calls a method named **drawTopBar**(), responsible for setting up the UI elements for the chat window

```
func queryChats(){
let chat:Conversation = Conversation()
chat.group.enter()
chat.getChats(receipentname: businessname, originatorname: originatorname){
//chat.getChats(receipentname: (receipentProfile?.businessname)!, originatorname: (origniatorProfile?.name)!){
[self] (chats, error) in
self.chats = chats
setup()
drawTopBar()
}
}
```

#### Setup Method

The **setup**() function configures the **chatTableView** for displaying chat messages. The breakdown of the code is given below:

*   **chatTableView.delegate = self:** Sets the current view controller as the delegate for the chat table view, responsible for handling user interactions

*   **chatTableView.dataSource = self:** Sets the current view controller as the data source for the chat table view, responsible for providing data to populate the table view

*   **chatTableView.register(ChatDetailsTableViewCell.self, forCellReuseIdentifier: “chat”):** Registers a custom cell class named **ChatDetailsTableViewCell** for reuse in the table view

*   **chatTableView.layer.borderWidth = 0:** Removes the border from the table view

*   **chatTableView.layer.borderColor = UIColor.white.cgColor:** Sets the border color to white (though it’s not visible due to the border width being 0)

*   **chatTableView.separatorStyle = .none:** Removes separator lines between table view cells

```
func setup(){
chatTableView.delegate = self
chatTableView.dataSource = self
chatTableView.register(ChatDetailsTableViewCell.self, forCellReuseIdentifier: "chat")
chatTableView.layer.borderWidth = 0
chatTableView.layer.borderColor = UIColor.white.cgColor
chatTableView.separatorStyle = .none
}
```

#### Drawing the UI Objects on Screen

The drawTopBar() function is responsible for constructing the chat view interface, including elements like a profile image, a top label, and a cancel button. It also positions the chat table view and text input components at the bottom of the screen. The code breakdown is given below:

**Screen size calculation:** Obtains the screen dimensions for layout purposes

**Top bar creation:**

*   Creates a **topBar** view with a specific background color

*   Adds the **topBar** as a **subview** to the main view

*   Sets constraints for the **topBar** to cover the top of the screen with a specific height

**Profile image view creation:**

*   Creates a **profileImageView** to display the recipient’s profile picture

*   Adds the **profileImageView** as a **subview** to the **topBar**

*   Sets constraints for the **profileImageView** to position it at the top left corner of the topBar

*   Loads the profile image from the **recipientProfileImage** asset if available

**Top label creation:**

*   Creates a **topLabel** to display the chat title or originator’s name

*   Adds the **topLabel** as a **subview** to the **topBar**

*   Sets constraints for the **topLabel** to position it to the right of the **profileImageView**

*   Sets the **topLabel** text based on whether **recipientProfileImageBanner** is nil or not

**Cancel button creation:**

*   Creates a **cancelImageView** with a close icon

*   Adds the **cancelImageView** as a **subview** to the **topBar**

*   Sets constraints for the **cancelImageView** to position it at the top right corner of the **topBar**

*   Adds a tap gesture recognizer to the **cancelImageView** to trigger the **cancel(tapGestureRecognizer:)** method

**Chat table view creation:**

*   Creates a **chatTableView** to display chat messages

*   Adds the **chatTableView** as a **subview** to the main view

*   Sets constraints for the **chatTableView** to position it below the **topBar** and cover most of the screen

**Shell text view creation:**

*   Creates a **shellTextView** as a placeholder view

*   Adds the **shellTextView** as a **subview** to the main view

*   Sets constraints for the **shellTextView** to position it at the bottom of the screen

**Shell save button creation:**

*   Creates a **shellSaveButton** with a paper plane icon

*   Adds the **shellSaveButton** as a **subview** to the main view

*   Sets constraints for the **shellSaveButton** to position it to the right of the **shellTextView**

```
func drawTopBar(){
let screensize: CGRect = UIScreen.main.bounds
let screenWidth = screensize.width
let screenHeight = screensize.height
view.addSubview(topBar)
NSLayoutConstraint.activate([
topBar.topAnchor.constraint(equalTo: view.topAnchor),
topBar.leadingAnchor.constraint(equalTo: view.leadingAnchor),
topBar.trailingAnchor.constraint(equalTo: view.trailingAnchor),
topBar.heightAnchor.constraint(equalToConstant: 100) // Customize the height as needed
])
topBar.addSubview(profileImageView)
NSLayoutConstraint.activate([
profileImageView.topAnchor.constraint(equalTo: topBar.topAnchor, constant: 50),
profileImageView.leftAnchor.constraint(equalTo: view.leftAnchor, constant: 5),
profileImageView.heightAnchor.constraint(equalToConstant: 40),
profileImageView.widthAnchor.constraint(equalToConstant: 40)
])
let file = self.recipientProfileImage?.fileURL
if let data = NSData(contentsOf: file!) {
profileImageView.image = UIImage(data: data as Data)
}
topBar.addSubview(topLabel)
NSLayoutConstraint.activate([
topLabel.topAnchor.constraint(equalTo: topBar.topAnchor, constant: 55),
topLabel.leftAnchor.constraint(equalTo: profileImageView.rightAnchor, constant: 10),
topLabel.widthAnchor.constraint(equalToConstant: screenWidth - 70)
])
if(recipientProfileImageBanner != nil){
topLabel.text = self.originatorname
}else{
topLabel.text = self.businessname
}
topBar.addSubview(cancelImageView)
NSLayoutConstraint.activate([
cancelImageView.topAnchor.constraint(equalTo: topBar.topAnchor, constant: 55),
cancelImageView.rightAnchor.constraint(equalTo: topBar.rightAnchor, constant: -25),
cancelImageView.heightAnchor.constraint(equalToConstant: 25),
cancelImageView.widthAnchor.constraint(equalToConstant: 25)
])
let cancelGestureRecognizer = UITapGestureRecognizer(target: self, action: #selector(cancel(tapGestureRecognizer:)))
cancelImageView.isUserInteractionEnabled = true
cancelImageView.addGestureRecognizer(cancelGestureRecognizer)
view.addSubview(chatTableView)
let constraints1 = [
chatTableView.topAnchor.constraint(equalTo: topBar.bottomAnchor, constant: 0),
chatTableView.leftAnchor.constraint(equalTo: view.leftAnchor, constant: 5),
chatTableView.widthAnchor.constraint(equalToConstant: screenWidth),
chatTableView.heightAnchor.constraint(equalToConstant: screenHeight - 170)
]
NSLayoutConstraint.activate(constraints1)
shellTextView.delegate = self
view.addSubview(shellTextView)
NSLayoutConstraint.activate([
shellTextView.bottomAnchor.constraint(equalTo: view.bottomAnchor, constant: -40),
shellTextView.leftAnchor.constraint(equalTo: view.leftAnchor, constant: 5),
shellTextView.widthAnchor.constraint(equalToConstant: screenWidth - 40),
shellTextView.heightAnchor.constraint(equalToConstant: 40)
])
view.addSubview(shellSaveButton)
let constraints3 = [
shellSaveButton.topAnchor.constraint(equalTo: view.bottomAnchor, constant: -70),
shellSaveButton.leftAnchor.constraint(equalTo: shellTextView.rightAnchor, constant: 5),
shellSaveButton.rightAnchor.constraint(equalToSystemSpacingAfter: shellTextView.rightAnchor, multiplier: 40),
shellSaveButton.heightAnchor.constraint(equalToConstant: 40)
]
NSLayoutConstraint.activate(constraints3)
shellSaveButton.setImage(UIImage(systemName: "paperplane.fill"), for: .normal)
// Set up a notification observer to handle text changes
}
```

#### Cancel Event Method

The cancel function handles the action when the user taps the cancel button on the chat detail view. It dismisses the current chat view and presents the **ContactViewController**. The breakdown of the code snippet is given below:

*   **Remove constraints:** Calls a function named **removeChatDetailsConstraints**(), responsible for removing Auto Layout constraints related to the chat view elements

*   **Create ContactViewController:** Instantiates a **ContactViewController** instance

*   **Pass profile information:** Passes the **origniatorProfile**, **receipentProfile**, and **profile** objects to the new view controller

*   **Set presentation styles:** Sets the presentation and transition styles for the **ContactViewController**.

*   **Present ContactViewController:** Modally presents the **ContactViewController** over the current view controller

```
@objc func cancel(tapGestureRecognizer: UITapGestureRecognizer){
removeChatDetailsConstraints()
let viewController = ContactViewController()
viewController.origniatorProfile = origniatorProfile
viewController.receipentProfile = receipentProfile
viewController.profile = profile
viewController.modalPresentationStyle = .overCurrentContext
viewController.modalTransitionStyle = .crossDissolve
self.present(viewController, animated: true, completion: nil)
}
```

#### Removing Constraints Method

The **removeChatDetailsConstraints**() function is responsible for removing all constraints and **subviews** related to the chat detail view from the parent view. The code details are given below:

*   **Removes constraints:** Iterates through the constraints of each UI element (**topBar**, **profileImageView**, **topLabel**, **cancelImageView**, **shellTextView**, **shellSaveButton**, and **chatTableView**) and removes them

*   **Removes subviews:** Removes each UI element from its **superview**

```
func removeChatDetailsConstraints(){
topBar.removeConstraints(topBar.constraints)
topBar.removeFromSuperview()
profileImageView.removeConstraints(profileImageView.constraints)
profileImageView.removeFromSuperview()
topLabel.removeConstraints(topLabel.constraints)
topLabel.removeFromSuperview()
cancelImageView.removeConstraints(cancelImageView.constraints)
cancelImageView.removeFromSuperview()
shellTextView.removeConstraints(shellTextView.constraints)
shellTextView.removeFromSuperview()
shellSaveButton.removeConstraints(shellSaveButton.constraints)
shellSaveButton.removeFromSuperview()
chatTableView.removeConstraints(chatTableView.constraints)
chatTableView.removeFromSuperview()
}
```

#### UI Table Methods

The provided code implements the UITableViewDataSource and UITableViewDelegate methods to configure and manage the chat table view. The code breakdown is given below:

**tableView(_:numberOfRowsInSection:)**

*   Returns the number of rows in the table view, which is equal to the count of the chats array

**tableView(_:cellForRowAt:)**

*   Dequeues a reusable cell of type **ChatDetailsTableViewCell**

*   Removes constraints and **subviews** from the cell’s **profileImageView** and **chatButton**

*   Sets the cell’s selection style to none to prevent highlighting on selection

*   Creates an attributed string for the chat text with paragraph style for indentation

*   Determines the sender of the message based on the **originatorid** property

*   Sets the profile image, chat button background color, and right position based on the message sender

*   Calculates the cell height based on the chat text length

*   Sets the chat button’s attributed title and returns the configured cell

**tableView(_:heightForRowAt:)**

*   Calculates the height of the chat table view cell based on the length of the chat text

```
func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
return chats.count
}
func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
let cell = tableView.dequeueReusableCell(withIdentifier: "chat", for: indexPath) as! ChatDetailsTableViewCell
cell.profileImageView.removeConstraints(cell.profileImageView.constraints)
cell.profileImageView.removeFromSuperview()
cell.chatButton.removeConstraints(cell.chatButton.constraints)
cell.chatButton.removeFromSuperview()
cell.selectionStyle = .none
let paragraphStyle = NSMutableParagraphStyle()
paragraphStyle.firstLineHeadIndent = 5
paragraphStyle.headIndent = 5
// Create an attributed string with the paragraph style
let attributedText = NSAttributedString(string: "\(chats[indexPath.row].chattext!)", attributes: [NSAttributedString.Key.paragraphStyle: paragraphStyle])
if(chats[indexPath.row].originatorid == profile?.userid){
var file1 = profile?.personalprofile.fileURL
if(recipientProfileImageBanner != nil){
file1 = profile?.personalprofile.fileURL
}
if var data1 = NSData(contentsOf: file1!) {
cell.profileImageView.image = UIImage(data: data1 as Data)
cell.profileImageView.layer.cornerRadius =  15
data1 = NSData()
cell.chatButton.backgroundColor = .white
}
file1 = nil
cell.rightPosition = 5
}else{
var file = recipientProfileImage?.fileURL
if var data = NSData(contentsOf: file!) {
cell.profileImageView.image = UIImage(data: data as Data)
cell.profileImageView.layer.cornerRadius =  15
data = NSData()
cell.chatButton.backgroundColor = .systemGray6
}
file = nil
cell.rightPosition = 40
}
var size = chats[indexPath.row].chattext.count / 40
if(size == 0 || size == 1){
size = 55
}else{
size = size * 32
}
cell.buttonPosition = CGFloat(size)
cell.chatButton.setAttributedTitle(attributedText, for: .normal)
return cell
}
func tableView(_ tableView: UITableView, heightForRowAt indexPath: IndexPath) -> CGFloat {
var size = chats[indexPath.row].chattext.count / 40
if(size == 0 || size == 1){
size = 55 + 20
}else{
size = size * 32 + 20
}
return CGFloat(size)
}
```

#### Layout Subview Lifecycle Method

The **viewDidLayoutSubviews**() method is called multiple times during the view lifecycle, particularly when the **subviews’** frames need to be adjusted. In this case, it’s used to scroll the chat table view to the last row after the initial layout. Code breakdown is given below:

*   **super.viewDidLayoutSubviews():** Calls the superclass implementation to ensure proper behavior

*   **Conditional check:** Checks if the chats array has elements and the **chatFlag** is true

*   **Scroll to last row:** Calls the **scrollToLastRow**() method to scroll the chat table view to the last message

*   **Update flag:** Sets the **chatFlag** to false to prevent repeated scrolling on subsequent calls to **viewDidLayoutSubviews**()

```
override func viewDidLayoutSubviews() {
super.viewDidLayoutSubviews()
if(chats.count != 0 && chatFlag){
scrollToLastRow()
chatFlag = false
}
}
```

#### Custom Method scrollToLastRow( )

The scrollToLastRow() function is designed to scroll the chat table view to the last message in the conversation. The code breakdown is given below:

*   **Calculate last row index:** Determines the index of the last row in the table view by subtracting 1 from the total number of rows in section 0

*   **Create index path:** Creates an **IndexPath** object representing the last row in the table view.

*   **Scroll to last row:** Uses the **scrollToRow(at:at:animated:)** method of the **chatTableView** to scroll to the specified index path with the scroll position at the bottom and animated

```
func scrollToLastRow() {
let lastRowIndex = chatTableView.numberOfRows(inSection: 0) - 1
let lastRowIndexPath = IndexPath(row: lastRowIndex, section: 0)
chatTableView.scrollToRow(at: lastRowIndexPath, at: .bottom, animated: true)
}
```

#### Text View Event Methods

This function is triggered when the user starts editing the **shellTextView**. Its primary goal is to transition from the initial state (with placeholders) to an active chat input state. Please see the code breakdown below:

*   **Remove placeholder elements:** Removes constraints and **subviews** for the **shellTextView** and **shellSaveButton** to clear the placeholder area

*   **Add chat text view:** Adds the **chatTextView** as a **subview** and sets its constraints to position it at the bottom of the view

*   **Activate text input:** Makes the **chatTextView** the first responder to enable keyboard input

*   **Set up notification observer:** Adds a notification observer to monitor text changes in the **chatTextView** using **UITextView.textDidChangeNotification**

*   **Add save button:** Adds the **saveButton** as a **subview** and sets its constraints to position it to the right of the **chatTextView**

*   **Configure save button:** Sets the image for the **saveButton** and adds a target action for the **saveChatAction** method

```
func textViewDidBeginEditing(_ textView: UITextView) {
let screensize: CGRect = UIScreen.main.bounds
let screenWidth = screensize.width
shellTextView.removeConstraints(shellTextView.constraints)
shellTextView.removeFromSuperview()
shellSaveButton.removeConstraints(shellSaveButton.constraints)
shellSaveButton.removeFromSuperview()
view.addSubview(chatTextView)
NSLayoutConstraint.activate([
chatTextView.bottomAnchor.constraint(equalTo: view.bottomAnchor, constant: -340),
chatTextView.leftAnchor.constraint(equalTo: view.leftAnchor, constant: 5),
chatTextView.widthAnchor.constraint(equalToConstant: screenWidth - 40),
chatTextView.heightAnchor.constraint(equalToConstant: 40)
])
chatTextView.becomeFirstResponder()
// Set up a notification observer to handle text changes
NotificationCenter.default.addObserver(self, selector: #selector(handleTextChange), name: UITextView.textDidChangeNotification, object: chatTextView)
view.addSubview(saveButton)
let constraints3 = [
saveButton.topAnchor.constraint(equalTo: view.bottomAnchor, constant: -370),
saveButton.leftAnchor.constraint(equalTo: chatTextView.rightAnchor, constant: 5),
saveButton.rightAnchor.constraint(equalToSystemSpacingAfter: chatTextView.rightAnchor, multiplier: 40),
saveButton.heightAnchor.constraint(equalToConstant: 40)
]
NSLayoutConstraint.activate(constraints3)
saveButton.setImage(UIImage(systemName: "paperplane.fill"), for: .normal)
saveButton.addTarget(self, action: #selector(saveChatAction), for: .touchUpInside)
}
```

#### Event Function handleTextChange()

The **handleTextChange**() function is responsible for updating the UI in response to changes in the **chatTextView’s** text content. The code breakdown is given below:

*   **Enable/disable save button:** Checks if the trimmed text in the **chatTextView** is empty. If empty, it disables the **saveButton**; otherwise, it enables it.

*   **Calculate text view height:** Calculates the estimated size of the **chatTextView** based on its content to adjust its height dynamically.

*   **Update text view height constraint:** Iterates over the constraints of the **chatTextView** and updates the height constraint based on the calculated estimated size

```
@objc private func handleTextChange() {
if(chatTextView.text.trimmingCharacters(in: .whitespaces).count == 0){
saveButton.isEnabled = false
}else{
saveButton.isEnabled = true
}
let size = CGSize(width: UIScreen.main.bounds.width - 30, height: .infinity)
let estimatedSize = chatTextView.sizeThatFits(size)
chatTextView.constraints.forEach { (constraint) in
if constraint.firstAttribute == .height {
constraint.constant = estimatedSize.height
}
}
}
```

#### Save Chat Event Method

This function handles the logic for saving a new chat message and updating the UI. The code breakdown is given below:

**Create new chat object:** Creates a new conversation object with the entered text and originator ID

**Disable text input and save button:** Disables editing for the **chatTextView** and disables the **saveButton** to prevent accidental resubmission

**Reset chat text view:** Clears the text content, removes constraints, and removes the **chatTextView** from the **superview**

**Restore placeholder elements:** Adds the placeholder **shellTextView**, sets its constraints, and re-enables it

**Add new chat to array:** Appends the newly created chat object to the chats array

**Reload chat table view:** Reloads the data in the **chatTableView** to reflect the new message

**Re-enable text input and save button:** Enables editing for the **chatTextView** and re-enables the **saveButton**

**Restore placeholder save button:** Adds the placeholder **shellSaveButton**, sets its constraints, and re-configures it

**Determine originator name:** Sets the on variable based on the presence of **recipientProfileImageBanner** to display the appropriate originator name

**Scroll to last row:** Call **scrollToLastRow()** to ensure the new message is visible

**Check chat table existence (asynchronous):** Uses the **chatTableExists** method of the chat object to check if a conversation table exists in the cloud for the recipient and handles potential errors during the check. And based on the result:

*   **Record exists:** If a table exists, enter the chat group and call **saveNewChatToCloud** to create a new chat entry with basic information.

*   **Record doesn’t exist:** If the table doesn’t exist, enter the chat group and call **saveNewChatRecordsToCloud** to create the chat table and save the first message along with additional profile information.

**Notification upon completion:** Uses a completion closure to print messages upon successful or unsuccessful operations

```
@objc func saveChatAction(sender: UIButton){
let chat = Conversation(chattext: chatTextView.text, originatorid: profile?.userid)
chatTextView.isEditable = false
saveButton.isEnabled = false
let screensize: CGRect = UIScreen.main.bounds
let screenWidth = screensize.width
chatTextView.resignFirstResponder()
chatTextView.text = ""
chatTextView.removeConstraints(chatTextView.constraints)
chatTextView.removeFromSuperview()
saveButton.removeConstraints(saveButton.constraints)
saveButton.removeFromSuperview()
shellTextView.delegate = self
view.addSubview(shellTextView)
NSLayoutConstraint.activate([
shellTextView.bottomAnchor.constraint(equalTo: view.bottomAnchor, constant: -40),
shellTextView.leftAnchor.constraint(equalTo: view.leftAnchor, constant: 5),
shellTextView.widthAnchor.constraint(equalToConstant: screenWidth - 40),
shellTextView.heightAnchor.constraint(equalToConstant: 40)
])
chats.append(chat)
chatTableView.reloadData()
chatTextView.isEditable = true
saveButton.isEnabled = true
view.addSubview(shellSaveButton)
let constraints3 = [
shellSaveButton.topAnchor.constraint(equalTo: view.bottomAnchor, constant: -70),
shellSaveButton.leftAnchor.constraint(equalTo: shellTextView.rightAnchor, constant: 5),
shellSaveButton.rightAnchor.constraint(equalToSystemSpacingAfter: shellTextView.rightAnchor, multiplier: 40),
shellSaveButton.heightAnchor.constraint(equalToConstant: 40)
]
NSLayoutConstraint.activate(constraints3)
shellSaveButton.setImage(UIImage(systemName: "paperplane.fill"), for: .normal)
var on:String = ""
if(recipientProfileImageBanner != nil){
on = originatorname
}else{
on = (profile?.name)!
}
scrollToLastRow()
chat.chatTableExists(receipentname: businessname , originatorname: on) { [self] (recordExists, error) in
if let error = error{
print(error.localizedDescription)
chat.group.enter()
if(recipientProfileImageBanner != nil){
chat.saveNewChatRecordsToCloud(receipentname: businessname, originatorname: originatorname, receipentprofile: recipientProfileImage!, originatorprofile: (profile?.personalprofile)!, lastchat: chatTextView.text)
}else{
chat.saveNewChatRecordsToCloud(receipentname: businessname, originatorname: (profile?.name)!, receipentprofile: recipientProfileImage!, originatorprofile: (profile?.personalprofile)!, lastchat: chatTextView.text)
}
chat.group.notify(queue: .main) {
print("First One")
}
}else{
if recordExists{
print("Record Type Exists")
chat.group.enter()
if(recipientProfileImageBanner != nil){
chat.saveNewChatToCloud(tablename: businessname, receipentname: businessname, originatorname: originatorname)
}else{
chat.saveNewChatToCloud(tablename: businessname, receipentname: businessname, originatorname: (profile?.name)!)
}
chat.group.notify(queue: .main){
print("Here now")
}
}
}
}
}
}
```

#### Running the App

From the contact list as Shaurya selects to chat with Shantanu, the following screen is shown. Please note that the name is Shantanu’s business (SSNEnterprise).

![](images/533539_2_En_13_Chapter/533539_2_En_13_Figh_HTML.jpg)

Shaurya is initiating a chat as shown below.

![](images/533539_2_En_13_Chapter/533539_2_En_13_Figi_HTML.jpg)

We are now using Shantanu’s phone, and in the chat window, see the following screen, a message from Shaurya.

![](images/533539_2_En_13_Chapter/533539_2_En_13_Figj_HTML.jpg)

There are three screens shown below. The first screen is displayed when the user taps on the chat user (in our case Shaurya). The second screen is where the user can converse with the other user (Shantanu conversing with Shaurya), and the last screen is a display screen of conversation.

| ![](images/533539_2_En_13_Chapter/533539_2_En_13_Figk_HTML.jpg) | ![](images/533539_2_En_13_Chapter/533539_2_En_13_Figl_HTML.jpg) | ![](images/533539_2_En_13_Chapter/533539_2_En_13_Figm_HTML.jpg) |

#### Complete Code

Find below the complete code of the ChatDetailViewController.

```
//
//  ChatViewController.swift
//  Chat
//
//  Created by Shantanu Baruah on 6/23/24.
//
import UIKit
import CloudKit
class ChatDetailViewController: UIViewController, UITableViewDelegate, UITableViewDataSource, UITextViewDelegate {
var profile:Profile?
var origniatorProfile:Profile?
var receipentProfile:Profile?
var businessname:String = ""
var originatorname:String = ""
var recipientProfileImage:CKAsset?
var whichTab:Int?
var chats:[Conversation] = []
var chatFlag:Bool = true
var recipientProfileImageBanner:CKAsset?
private let chatTableView:UITableView = {
let tableView = UITableView()
tableView.translatesAutoresizingMaskIntoConstraints = false
tableView.backgroundColor = .white
tableView.isScrollEnabled = true
return tableView
}()
let topBar:UIView = {
let view = UIView()
view.backgroundColor = UIColor.systemGray6
view.translatesAutoresizingMaskIntoConstraints = false
return view
}()
var profileImageView: UIImageView = {
let imageView = UIImageView()
imageView.image = UIImage(systemName: "person.fill")
imageView.contentMode = .scaleAspectFit
imageView.translatesAutoresizingMaskIntoConstraints = false
imageView.layer.masksToBounds = true
imageView.layer.borderWidth = 1
imageView.layer.borderColor = UIColor.systemGray.cgColor
imageView.layer.cornerRadius = 20
return imageView
}()
let topLabel:UILabel = {
let label = UILabel()
label.translatesAutoresizingMaskIntoConstraints = false
label.textAlignment = .left
label.textColor = .black
label.font = UIFont.systemFont(ofSize: 20, weight: .bold)
return label
}()
var cancelImageView: UIImageView = {
let imageView = UIImageView()
imageView.contentMode = .scaleAspectFit
imageView.translatesAutoresizingMaskIntoConstraints = false
imageView.layer.masksToBounds = true
imageView.image = UIImage(systemName: "xmark")
return imageView
}()
let shellTextView: UITextView = {
let textView = UITextView()
textView.translatesAutoresizingMaskIntoConstraints = false
textView.isScrollEnabled = false
textView.font = UIFont.systemFont(ofSize: 17)
textView.layer.borderWidth = 1
textView.layer.borderColor = UIColor.gray.cgColor
textView.layer.cornerRadius = 10
return textView
}()
let shellSaveButton:UIButton = {
let uiButton  = UIButton()
uiButton.translatesAutoresizingMaskIntoConstraints = false
uiButton.setTitleColor(UIColor.black, for: .normal)
uiButton.contentHorizontalAlignment = .left
uiButton.contentVerticalAlignment = .top
uiButton.titleLabel?.font = UIFont.systemFont(ofSize: 16, weight: .semibold)
uiButton.isEnabled = false
return uiButton
}()
let chatTextView: UITextView = {
let textView = UITextView()
textView.translatesAutoresizingMaskIntoConstraints = false
textView.isScrollEnabled = false
textView.font = UIFont.systemFont(ofSize: 17)
textView.layer.borderWidth = 1
textView.layer.borderColor = UIColor.gray.cgColor
textView.layer.cornerRadius = 10
return textView
}()
let saveButton:UIButton = {
let uiButton  = UIButton()
uiButton.translatesAutoresizingMaskIntoConstraints = false
uiButton.setTitleColor(UIColor.black, for: .normal)
uiButton.contentHorizontalAlignment = .left
uiButton.contentVerticalAlignment = .top
uiButton.titleLabel?.font = UIFont.systemFont(ofSize: 16, weight: .semibold)
uiButton.isEnabled = false
return uiButton
}()
override func viewDidLoad() {
super.viewDidLoad()
queryChats()
}
func queryChats(){
let chat:Conversation = Conversation()
chat.group.enter()
chat.getChats(receipentname: businessname, originatorname: originatorname){
//chat.getChats(receipentname: (receipentProfile?.businessname)!, originatorname: (origniatorProfile?.name)!){
[self] (chats, error) in
self.chats = chats
setup()
drawTopBar()
}
}
func setup(){
chatTableView.delegate = self
chatTableView.dataSource = self
chatTableView.register(ChatDetailsTableViewCell.self, forCellReuseIdentifier: "chat")
chatTableView.layer.borderWidth = 0
chatTableView.layer.borderColor = UIColor.white.cgColor
chatTableView.separatorStyle = .none
}
//MARK: - Draw Functions
func drawTopBar(){
let screensize: CGRect = UIScreen.main.bounds
let screenWidth = screensize.width
let screenHeight = screensize.height
view.addSubview(topBar)
NSLayoutConstraint.activate([
topBar.topAnchor.constraint(equalTo: view.topAnchor),
topBar.leadingAnchor.constraint(equalTo: view.leadingAnchor),
topBar.trailingAnchor.constraint(equalTo: view.trailingAnchor),
topBar.heightAnchor.constraint(equalToConstant: 100) // Customize the height as needed
])
topBar.addSubview(profileImageView)
NSLayoutConstraint.activate([
profileImageView.topAnchor.constraint(equalTo: topBar.topAnchor, constant: 50),
profileImageView.leftAnchor.constraint(equalTo: view.leftAnchor, constant: 5),
profileImageView.heightAnchor.constraint(equalToConstant: 40),
profileImageView.widthAnchor.constraint(equalToConstant: 40)
])
let file = self.recipientProfileImage?.fileURL
if let data = NSData(contentsOf: file!) {
profileImageView.image = UIImage(data: data as Data)
}
topBar.addSubview(topLabel)
NSLayoutConstraint.activate([
topLabel.topAnchor.constraint(equalTo: topBar.topAnchor, constant: 55),
topLabel.leftAnchor.constraint(equalTo: profileImageView.rightAnchor, constant: 10),
topLabel.widthAnchor.constraint(equalToConstant: screenWidth - 70)
])
if(recipientProfileImageBanner != nil){
topLabel.text = self.originatorname
}else{
topLabel.text = self.businessname
}
topBar.addSubview(cancelImageView)
NSLayoutConstraint.activate([
cancelImageView.topAnchor.constraint(equalTo: topBar.topAnchor, constant: 55),
cancelImageView.rightAnchor.constraint(equalTo: topBar.rightAnchor, constant: -25),
cancelImageView.heightAnchor.constraint(equalToConstant: 25),
cancelImageView.widthAnchor.constraint(equalToConstant: 25)
])
let cancelGestureRecognizer = UITapGestureRecognizer(target: self, action: #selector(cancel(tapGestureRecognizer:)))
cancelImageView.isUserInteractionEnabled = true
cancelImageView.addGestureRecognizer(cancelGestureRecognizer)
view.addSubview(chatTableView)
let constraints1 = [
chatTableView.topAnchor.constraint(equalTo: topBar.bottomAnchor, constant: 0),
chatTableView.leftAnchor.constraint(equalTo: view.leftAnchor, constant: 5),
chatTableView.widthAnchor.constraint(equalToConstant: screenWidth),
chatTableView.heightAnchor.constraint(equalToConstant: screenHeight - 170)
]
NSLayoutConstraint.activate(constraints1)
shellTextView.delegate = self
view.addSubview(shellTextView)
NSLayoutConstraint.activate([
shellTextView.bottomAnchor.constraint(equalTo: view.bottomAnchor, constant: -40),
shellTextView.leftAnchor.constraint(equalTo: view.leftAnchor, constant: 5),
shellTextView.widthAnchor.constraint(equalToConstant: screenWidth - 40),
shellTextView.heightAnchor.constraint(equalToConstant: 40)
])
view.addSubview(shellSaveButton)
let constraints3 = [
shellSaveButton.topAnchor.constraint(equalTo: view.bottomAnchor, constant: -70),
shellSaveButton.leftAnchor.constraint(equalTo: shellTextView.rightAnchor, constant: 5),
shellSaveButton.rightAnchor.constraint(equalToSystemSpacingAfter: shellTextView.rightAnchor, multiplier: 40),
shellSaveButton.heightAnchor.constraint(equalToConstant: 40)
]
NSLayoutConstraint.activate(constraints3)
shellSaveButton.setImage(UIImage(systemName: "paperplane.fill"), for: .normal)
// Set up a notification observer to handle text changes
}
@objc func cancel(tapGestureRecognizer: UITapGestureRecognizer){
removeChatDetailsConstraints()
let viewController = ContactViewController()
viewController.origniatorProfile = origniatorProfile
viewController.receipentProfile = receipentProfile
viewController.profile = profile
viewController.modalPresentationStyle = .overCurrentContext
viewController.modalTransitionStyle = .crossDissolve
self.present(viewController, animated: true, completion: nil)
}
func removeChatDetailsConstraints(){
topBar.removeConstraints(topBar.constraints)
topBar.removeFromSuperview()
profileImageView.removeConstraints(profileImageView.constraints)
profileImageView.removeFromSuperview()
topLabel.removeConstraints(topLabel.constraints)
topLabel.removeFromSuperview()
cancelImageView.removeConstraints(cancelImageView.constraints)
cancelImageView.removeFromSuperview()
shellTextView.removeConstraints(shellTextView.constraints)
shellTextView.removeFromSuperview()
shellSaveButton.removeConstraints(shellSaveButton.constraints)
shellSaveButton.removeFromSuperview()
chatTableView.removeConstraints(chatTableView.constraints)
chatTableView.removeFromSuperview()
}
//MARK: - Table functions
func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
return chats.count
}
func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
let cell = tableView.dequeueReusableCell(withIdentifier: "chat", for: indexPath) as! ChatDetailsTableViewCell
cell.profileImageView.removeConstraints(cell.profileImageView.constraints)
cell.profileImageView.removeFromSuperview()
cell.chatButton.removeConstraints(cell.chatButton.constraints)
cell.chatButton.removeFromSuperview()
cell.selectionStyle = .none
let paragraphStyle = NSMutableParagraphStyle()
paragraphStyle.firstLineHeadIndent = 5
paragraphStyle.headIndent = 5
// Create an attributed string with the paragraph style
let attributedText = NSAttributedString(string: "\(chats[indexPath.row].chattext!)", attributes: [NSAttributedString.Key.paragraphStyle: paragraphStyle])
if(chats[indexPath.row].originatorid == profile?.userid){
var file1 = profile?.personalprofile.fileURL
if(recipientProfileImageBanner != nil){
file1 = profile?.personalprofile.fileURL
}
if var data1 = NSData(contentsOf: file1!) {
cell.profileImageView.image = UIImage(data: data1 as Data)
cell.profileImageView.layer.cornerRadius =  15
data1 = NSData()
cell.chatButton.backgroundColor = .white
}
file1 = nil
cell.rightPosition = 5
}else{
var file = recipientProfileImage?.fileURL
if var data = NSData(contentsOf: file!) {
cell.profileImageView.image = UIImage(data: data as Data)
cell.profileImageView.layer.cornerRadius =  15
data = NSData()
cell.chatButton.backgroundColor = .systemGray6
}
file = nil
cell.rightPosition = 40
}
var size = chats[indexPath.row].chattext.count / 40
if(size == 0 || size == 1){
size = 55
}else{
size = size * 32
}
cell.buttonPosition = CGFloat(size)
cell.chatButton.setAttributedTitle(attributedText, for: .normal)
return cell
}
func tableView(_ tableView: UITableView, heightForRowAt indexPath: IndexPath) -> CGFloat {
var size = chats[indexPath.row].chattext.count / 40
if(size == 0 || size == 1){
size = 55 + 20
}else{
size = size * 32 + 20
}
return CGFloat(size)
}
override func viewDidLayoutSubviews() {
super.viewDidLayoutSubviews()
if(chats.count != 0 && chatFlag){
scrollToLastRow()
chatFlag = false
}
}
func scrollToLastRow() {
let lastRowIndex = chatTableView.numberOfRows(inSection: 0) - 1
let lastRowIndexPath = IndexPath(row: lastRowIndex, section: 0)
chatTableView.scrollToRow(at: lastRowIndexPath, at: .bottom, animated: true)
}
func textViewDidBeginEditing(_ textView: UITextView) {
let screensize: CGRect = UIScreen.main.bounds
let screenWidth = screensize.width
shellTextView.removeConstraints(shellTextView.constraints)
shellTextView.removeFromSuperview()
shellSaveButton.removeConstraints(shellSaveButton.constraints)
shellSaveButton.removeFromSuperview()
view.addSubview(chatTextView)
NSLayoutConstraint.activate([
chatTextView.bottomAnchor.constraint(equalTo: view.bottomAnchor, constant: -340),
chatTextView.leftAnchor.constraint(equalTo: view.leftAnchor, constant: 5),
chatTextView.widthAnchor.constraint(equalToConstant: screenWidth - 40),
chatTextView.heightAnchor.constraint(equalToConstant: 40)
])
chatTextView.becomeFirstResponder()
// Set up a notification observer to handle text changes
NotificationCenter.default.addObserver(self, selector: #selector(handleTextChange), name: UITextView.textDidChangeNotification, object: chatTextView)
view.addSubview(saveButton)
let constraints3 = [
saveButton.topAnchor.constraint(equalTo: view.bottomAnchor, constant: -370),
saveButton.leftAnchor.constraint(equalTo: chatTextView.rightAnchor, constant: 5),
saveButton.rightAnchor.constraint(equalToSystemSpacingAfter: chatTextView.rightAnchor, multiplier: 40),
saveButton.heightAnchor.constraint(equalToConstant: 40)
]
NSLayoutConstraint.activate(constraints3)
saveButton.setImage(UIImage(systemName: "paperplane.fill"), for: .normal)
saveButton.addTarget(self, action: #selector(saveChatAction), for: .touchUpInside)
}
@objc private func handleTextChange() {
if(chatTextView.text.trimmingCharacters(in: .whitespaces).count == 0){
saveButton.isEnabled = false
}else{
saveButton.isEnabled = true
}
let size = CGSize(width: UIScreen.main.bounds.width - 30, height: .infinity)
let estimatedSize = chatTextView.sizeThatFits(size)
chatTextView.constraints.forEach { (constraint) in
if constraint.firstAttribute == .height {
constraint.constant = estimatedSize.height
}
}
}
@objc func saveChatAction(sender: UIButton){
let chat = Conversation(chattext: chatTextView.text, originatorid: profile?.userid)
chatTextView.isEditable = false
saveButton.isEnabled = false
let screensize: CGRect = UIScreen.main.bounds
let screenWidth = screensize.width
chatTextView.resignFirstResponder()
chatTextView.text = ""
chatTextView.removeConstraints(chatTextView.constraints)
chatTextView.removeFromSuperview()
saveButton.removeConstraints(saveButton.constraints)
saveButton.removeFromSuperview()
shellTextView.delegate = self
view.addSubview(shellTextView)
NSLayoutConstraint.activate([
shellTextView.bottomAnchor.constraint(equalTo: view.bottomAnchor, constant: -40),
shellTextView.leftAnchor.constraint(equalTo: view.leftAnchor, constant: 5),
shellTextView.widthAnchor.constraint(equalToConstant: screenWidth - 40),
shellTextView.heightAnchor.constraint(equalToConstant: 40)
])
chats.append(chat)
chatTableView.reloadData()
chatTextView.isEditable = true
saveButton.isEnabled = true
view.addSubview(shellSaveButton)
let constraints3 = [
shellSaveButton.topAnchor.constraint(equalTo: view.bottomAnchor, constant: -70),
shellSaveButton.leftAnchor.constraint(equalTo: shellTextView.rightAnchor, constant: 5),
shellSaveButton.rightAnchor.constraint(equalToSystemSpacingAfter: shellTextView.rightAnchor, multiplier: 40),
shellSaveButton.heightAnchor.constraint(equalToConstant: 40)
]
NSLayoutConstraint.activate(constraints3)
shellSaveButton.setImage(UIImage(systemName: "paperplane.fill"), for: .normal)
var on:String = ""
if(recipientProfileImageBanner != nil){
on = originatorname
}else{
on = (profile?.name)!
}
scrollToLastRow()
chat.chatTableExists(receipentname: businessname , originatorname: on) { [self] (recordExists, error) in
if let error = error{
print(error.localizedDescription)
chat.group.enter()
if(recipientProfileImageBanner != nil){
chat.saveNewChatRecordsToCloud(receipentname: businessname, originatorname: originatorname, receipentprofile: recipientProfileImage!, originatorprofile: (profile?.personalprofile)!, lastchat: chatTextView.text)
}else{
chat.saveNewChatRecordsToCloud(receipentname: businessname, originatorname: (profile?.name)!, receipentprofile: recipientProfileImage!, originatorprofile: (profile?.personalprofile)!, lastchat: chatTextView.text)
}
chat.group.notify(queue: .main) {
}
}else{
if recordExists{
chat.group.enter()
if(recipientProfileImageBanner != nil){
chat.saveNewChatToCloud(tablename: businessname, receipentname: businessname, originatorname: originatorname)
}else{
chat.saveNewChatToCloud(tablename: businessname, receipentname: businessname, originatorname: (profile?.name)!)
}
chat.group.notify(queue: .main){
}
}
}
}
}
}
```

### ChatViewController

The below code defines a **ChatViewController** class that displays all contacts with whom the user has initiated chat. It inherits from **UIViewController** and conforms to **UITabBarDelegate**, **UITableViewDelegate**, and **UITableViewDataSource** protocols. The following are the class properties:

*   **profile:** The current user’s profile information

*   **origniatorProfile:** The profile of the chat initiator

*   **receipentProfile:** The profile of the chat recipient

*   **chattables:** An array to store all chat table names

*   **businesstables:** An array to store business tables

```
import UIKit
class ChatViewController: UIViewController, UITabBarDelegate, UITableViewDelegate, UITableViewDataSource {
var profile:Profile?
var origniatorProfile:Profile?
var receipentProfile:Profile?
var chattables:[Chattable] = []
var businesstables:[Businesstable] = []
```

#### UI Object Definition

The below code defines several UI components that will be used to construct a chat interface with a tab bar for navigation.

*   **tabBar:** Creates a tab bar with three items: “Profile”, “Contacts”, and “Chats”

*   **chatTableView:** Creates a table view for displaying chat messages

*   **businessTableView:** Creates a table view, for displaying business-related information (based on the **businesstables** property)

*   **headerView:** Creates a header view with a light gray background, for displaying additional information or controls

*   **headerLabel:** Creates a label to be used within the headerView, for displaying the chat screen title

```
private let tabBar:UITabBar = {
let tabbar = UITabBar()
tabbar.translatesAutoresizingMaskIntoConstraints = false
tabbar.backgroundColor = UIColor.init(red: 171/255, green: 189/255, blue: 217/255, alpha: 1)
let saveSymbolConfiguration = UIImage.SymbolConfiguration(pointSize: 16, weight: .black)
let contact = UIImage(systemName: "doc.on.clipboard", withConfiguration: saveSymbolConfiguration)
let contactbutton = UITabBarItem(title: "Contacts", image: contact, selectedImage: contact)
contactbutton.tag = 1
let chat = UIImage(systemName: "doc.plaintext", withConfiguration: saveSymbolConfiguration)
let chatbutton = UITabBarItem(title: "Chats", image: chat, selectedImage: chat)
chatbutton.tag = 2
let profile = UIImage(systemName: "person.fill", withConfiguration: saveSymbolConfiguration)
let profilebutton = UITabBarItem(title: "Profile", image: profile, selectedImage: profile)
profilebutton.tag = 0
tabbar.setItems([profilebutton, contactbutton, chatbutton], animated: false)
return tabbar
}()
private let chatTableView:UITableView = {
let tableView = UITableView()
tableView.translatesAutoresizingMaskIntoConstraints = false
tableView.backgroundColor = .white
tableView.isScrollEnabled = true
return tableView
}()
private let businessTableView:UITableView = {
let tableView = UITableView()
tableView.translatesAutoresizingMaskIntoConstraints = false
tableView.backgroundColor = .white
tableView.isScrollEnabled = true
return tableView
}()
private let headerView: UIView = {
let view = UIView()
view.translatesAutoresizingMaskIntoConstraints = false
view.backgroundColor = .systemGray6
return view
}()
private let headerLabel:UILabel = {
let label = UILabel()
label.text = "Chats"
label.translatesAutoresizingMaskIntoConstraints = false
label.textColor = UIColor(displayP3Red: 128/255, green: 0/255, blue: 128/255, alpha: 1)
label.textAlignment = .center
label.font = UIFont.boldSystemFont(ofSize: 35)
return label
}()
```

#### Lifecycle Method

The **viewDidLoad**() method is a lifecycle method in **UIKit** that is called when the view controller’s view is loaded into memory. In this case, it’s used to initiate the process of fetching chat tables. The breakdown of the code is given below:

*   **super**.**viewDidLoad**()**:** Calls the parent class’s implementation of **viewDidLoad**() to ensure proper initialization

*   **getChatTables**()**:** Calls a custom method named **getChatTables**() to retrieve chat-related data

```
override func viewDidLoad() {
super.viewDidLoad()
getChatTables()
}
```

#### Setup Method

The setup() function configures the initial state of the view controller, including setting up delegates, data sources, and table view properties. The breakdown code is given below:

*   **view.backgroundColor = .white:** Sets the background color of the main view to white

*   **tabBar.delegate = self:** Sets the current view controller as the delegate for the tab bar, allowing it to handle tab selection events

*   **tabBar.selectedItem = tabBar.items![1]:** Sets the second tab item as the initially selected tab

*   **chatTableView Configuration:**
    *   Sets the current view controller as the delegate and data source for the chatTableView

    *   Registers the ChatTableViewCell class for reuse in the chatTableView

    *   Removes the border from the chatTableView

    *   Sets the separator style for the chatTableView to single line

*   **businessTableView Configuration:**
    *   Sets the current view controller as the delegate and data source for the businessTableView

    *   Registers the ChatTableViewCell class for reuse in the businessTableView

    *   Removes the border from the businessTableView

    *   Sets the separator style for the businessTableView to single line

```
func setup(){
view.backgroundColor = .white //
tabBar.delegate = self
tabBar.selectedItem = tabBar.items![1]
chatTableView.delegate = self
chatTableView.dataSource = self
chatTableView.register(ChatTableViewCell.self, forCellReuseIdentifier: "chat")
chatTableView.layer.borderWidth = 0
chatTableView.layer.borderColor = UIColor.white.cgColor
chatTableView.separatorStyle = .singleLine
businessTableView.delegate = self
businessTableView.dataSource = self
businessTableView.register(ChatTableViewCell.self, forCellReuseIdentifier: "business")
businessTableView.layer.borderWidth = 0
businessTableView.layer.borderColor = UIColor.white.cgColor
businessTableView.separatorStyle = .singleLine
}
```

#### Draw Screen Method

This function is responsible for setting up the initial layout of the view controller, including creating and positioning the header view, header label, either the chat table view or the business table view, and the tab bar. The breakdown of the code is given below:

**Obtaining screen dimensions**

*   **let screensize: CGRect = UIScreen.main.bounds:** Gets the dimensions of the main screen

*   **let screenWidth = screensize.width:** Extracts the width of the screen

*   **let screenHeight = screensize.height:** Extracts the height of the screen

**Creating and positioning header view**

*   **view.addSubview(headerView):** Adds the headerView as a subview to the main view

*   **NSLayoutConstraint.activate(constraints):** Applies constraints to the headerView to position it at the top of the screen with full width and a height of 100 points

**Creating and positioning header label**

*   **headerView.addSubview(headerLabel):** Adds the headerLabel as a subview to the headerView

*   **NSLayoutConstraint.activate(constraints0):** Applies constraints to the headerLabel to position it within the headerView with a specific top margin, full width, and height

**Conditional display of table view**

*   **if(chattables.count > 0):** Checks if the chattables array has elements.

*   If true, it adds the chatTableView as a subview and sets its constraints to fill the area below the headerView.

*   If false, it adds the businessTableView as a subview and sets its constraints to fill the area below the headerView.

**Creating and positioning tab bar**

*   **view.addSubview(tabBar):** Adds the tabBar as a subview to the main view

*   **NSLayoutConstraint.activate(constraints5):** Applies constraints to the tabBar to position it at the bottom of the screen with a specific height

```
func drawInitialScreen(){
let screensize: CGRect = UIScreen.main.bounds
let screenWidth = screensize.width
let screenHeight = screensize.height
view.addSubview(headerView)
let constraints = [
headerView.topAnchor.constraint(equalTo: view.topAnchor, constant: 0),
headerView.leftAnchor.constraint(equalTo: view.leftAnchor, constant: 0),
headerView.widthAnchor.constraint(equalToConstant: screenWidth),
headerView.heightAnchor.constraint(equalToConstant: 100)
]
NSLayoutConstraint.activate(constraints)
headerView.addSubview(headerLabel)
let constraints0 = [
headerLabel.topAnchor.constraint(equalTo: view.topAnchor, constant: 50),
headerLabel.widthAnchor.constraint(equalToConstant: screenWidth),
headerLabel.heightAnchor.constraint(equalToConstant: 40)
]
NSLayoutConstraint.activate(constraints0)
if(chattables.count > 0){
view.addSubview(chatTableView)
let constraints4 = [
chatTableView.topAnchor.constraint(equalTo: headerView.bottomAnchor, constant: 0),
chatTableView.leftAnchor.constraint(equalTo: view.leftAnchor, constant: 5),
chatTableView.widthAnchor.constraint(equalToConstant: screenWidth - 10),
chatTableView.heightAnchor.constraint(equalToConstant: screenHeight - 50)
]
NSLayoutConstraint.activate(constraints4)
}else{
view.addSubview(businessTableView)
let constraints4 = [
businessTableView.topAnchor.constraint(equalTo: headerView.bottomAnchor, constant: 0),
businessTableView.leftAnchor.constraint(equalTo: view.leftAnchor, constant: 5),
businessTableView.widthAnchor.constraint(equalToConstant: screenWidth - 10),
businessTableView.heightAnchor.constraint(equalToConstant: screenHeight - 50)
]
NSLayoutConstraint.activate(constraints4)
}
view.addSubview(tabBar)
let constraints5 = [
tabBar.bottomAnchor.constraint(equalTo: view.bottomAnchor, constant: 5),
tabBar.leftAnchor.constraint(equalTo: view.leftAnchor, constant: 5),
tabBar.widthAnchor.constraint(equalToConstant: screenWidth),
tabBar.heightAnchor.constraint(equalToConstant: 80)
]
NSLayoutConstraint.activate(constraints5)
}
```

#### Tab Bar Method

The provided code implements the **tabBar(_:didSelect:)** delegate method, which handles the selection of tab bar items:

*   **Case 0:** Triggers the popUpContact() function

*   **Case 2:** Triggers the popUpChat() function

*   **Default:** Triggers the popUpMarket() function (currently not implemented)

**Pop-up functions**

*   **popUpContact():** Creates a ContactViewController instance, passes relevant profile information, and presents it modally

*   **popUpChat():** Currently empty, intended to present a chat-related view

*   **popUpMarket():** Currently empty, intended to present a market-related view. Not implemented now

```
func tabBar(_ tabBar: UITabBar, didSelect item: UITabBarItem) {
switch item.tag {
case 0:
popUpContact()
case 2:
popUpChat()
default:
popUpMarket()
}
}
func popUpContact(){
let viewController = ContactViewController()
viewController.profile = profile
viewController.origniatorProfile = origniatorProfile
viewController.receipentProfile = receipentProfile
viewController.modalPresentationStyle = .overCurrentContext
viewController.modalTransitionStyle = .crossDissolve
self.present(viewController, animated: true, completion: nil)
}
func popUpChat(){
}
func popUpMarket(){
}
```

#### Get Chat Table Method

The getChatTables() function is responsible for fetching data related to chat tables based on the user’s profile information. The function primarily handles two cases:

**User without a business name:**

*   Fetches chat tables using a Chattable object.

*   Handles potential errors and calls setup() and drawInitialScreen() in case of failure.

*   Upon successful retrieval, it stores the chat tables in the chattables array and calls getlastChatFromChatTable with “Personal” as the parameter.

**User with a business name:**

*   Fetches business tables using a Businesstable object.

*   Handles potential errors.

*   If an error occurs, it falls back to fetching chat tables using the Chattable object.

*   If successful, it stores the business tables in the businesstables array and calls getlastChatFromChatTable with “Business” as the parameter.

```
func getChatTables(){
if(profile?.businessname == ""){
let chattable = Chattable()
chattable.gettableNames{ [self] (chattables, error) in
if let error = error {
print("Error: \(error)")
setup()
drawInitialScreen()
}else{
self.chattables = chattables
getlastChatFromChatTable(fromWhere: "Personal")
}
}
}else if(profile?.businessname != nil){
let businesstable = Businesstable()
businesstable.gettableNames(tablename: (profile?.businessname)!) { [self] (businesstables, error) in
if let error = error {
print("Error: \(error)")
let chattable = Chattable()
chattable.gettableNames{ [self] (chattables, error) in
if let error = error {
print("Error: \(error)")
setup()
drawInitialScreen()
}else{
self.chattables = chattables
getlastChatFromChatTable(fromWhere: "Personal")
}
}
}else{
self.businesstables = businesstables
getlastChatFromChatTable(fromWhere: "Business")
}
}
}
}
```

#### Get Last Chat Method

This function retrieves the last chat message from each chat table (either personal or business) and updates the corresponding Chattable or Businesstable object with the last chat message. Once all chat tables have been processed, it calls the setup() and drawInitialScreen() functions. The following are the code details:

*   **Create conversation object:** Initializes a conversation object to interact with chat data

*   **Initialize counter:** Sets a counter variable to track the number of processed chat tables

*   **Conditional check:** Determines whether to process personal or business chat tables based on the fromWhere parameter

*   **Iterate through chat tables:** Iterates through either chattables or businesstables depending on the fromWhere parameter
    *   For each chat table
        *   Calls getlastChat to retrieve the last chat message from the current chat table.

        *   Handles potential errors.

        *   If successful, it updates the lastchat property of the current Chattable or Businesstable object with the retrieved chat message.

        *   Checks if all chat tables have been processed:
            *   If the current counter equals the total number of chat tables, it calls setup() and drawInitialScreen().

            *   Otherwise, it increments the counter.

```
func getlastChatFromChatTable(fromWhere: String){
let chat:Conversation = Conversation()
var counter:Int = 1
if(fromWhere == "Personal"){
for chattable in chattables{
chat.getlastChat(chattablename: chattable.chattablename) { [self] (chats, error) in
if let error = error {
print("Error: \(error)")
}else{
if(chats.count > 0){
chattable.lastchat = chats[0].chattext
}
if(chattables.count == counter){
setup()
drawInitialScreen()
}else{
counter = counter + 1
}
}
}
}
}else{
for chattable in businesstables{
chat.getlastChat(chattablename: chattable.chattablename) { [self] (chats, error) in
if let error = error {
print("Error: \(error)")
}else{
if(chats.count > 0){
chattable.lastchat = chats[0].chattext
}
if(businesstables.count == counter){
setup()
drawInitialScreen()
}else{
counter = counter + 1
}
}
}
}
}
}
```

#### Table View Methods

The below code implements the UITableViewDataSource and UITableViewDelegate methods to manage the chatTableView and businessTableView.

**tableView(_:numberOfRowsInSection:)**

*   Determines the number of rows for each table view based on the count of chattables or businesstables arrays

**tableView(_:cellForRowAt:)**

*   Dequeues a reusable cell for the specified table view

*   Configures the cell with data from the corresponding array

*   Sets the nameButton and titleButton titles based on data from chattables or businesstables

*   Sets the profileImageView image from the profile data

*   Adds target action for nameButton and titleButton to trigger showChatsActionButton

*   Sets accessibility hints for buttons for accessibility purposes

*   Returns the configured cell

**tableView(_:heightForRowAt:)**

*   Sets a fixed height of 100 points for each table view cell

```
func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
if(tableView == chatTableView){
return chattables.count
}else{
return businesstables.count
}
}
func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
if(tableView == chatTableView){
let cell = tableView.dequeueReusableCell(withIdentifier: "chat", for: indexPath) as! ChatTableViewCell
cell.nameButton.setTitle(chattables[indexPath.row].receipentname, for: .normal)
cell.titleButton.setTitle(chattables[indexPath.row].lastchat, for: .normal)
cell.selectionStyle = .none
let file = self.chattables[indexPath.row].receipentprofile.fileURL
if let data = NSData(contentsOf: file!) {
cell.profileImageView.image = UIImage(data: data as Data)
}
cell.profileImageView.layer.cornerRadius =  30
cell.nameButton.addTarget(self, action: #selector(showChatsActionButton), for: .touchUpInside)
cell.titleButton.addTarget(self, action: #selector(showChatsActionButton), for: .touchUpInside)
cell.nameButton.tag = indexPath.row
cell.titleButton.tag = indexPath.row
cell.nameButton.accessibilityHint = "Self"
cell.titleButton.accessibilityHint = "Self"
return cell
}else{
let cell = tableView.dequeueReusableCell(withIdentifier: "business", for: indexPath) as! ChatTableViewCell
cell.nameButton.setTitle(businesstables[indexPath.row].originatorname, for: .normal)
cell.titleButton.setTitle(businesstables[indexPath.row].lastchat, for: .normal)
cell.selectionStyle = .none
let file = self.businesstables[indexPath.row].originatorprofile.fileURL
if let data = NSData(contentsOf: file!) {
cell.profileImageView.image = UIImage(data: data as Data)
}
cell.profileImageView.layer.cornerRadius =  30
cell.nameButton.addTarget(self, action: #selector(showChatsActionButton), for: .touchUpInside)
cell.titleButton.addTarget(self, action: #selector(showChatsActionButton), for: .touchUpInside)
cell.nameButton.tag = indexPath.row
cell.titleButton.tag = indexPath.row
cell.nameButton.accessibilityHint = "Business"
cell.titleButton.accessibilityHint = "Business"
return cell
}
}
/*
The Height of the Cell Row is set to default 40 pixels
*/
func tableView(_ tableView: UITableView, heightForRowAt indexPath: IndexPath) -> CGFloat {
return 100
}
```

#### Display Chat Button Action Method

This function handles the action when either the nameButton or titleButton within a table view cell is tapped. It presents a ChatDetailViewController with relevant data based on the tapped button and its associated data. The code details are given below:

*   **Remove constraints:** Calls a function named removeConstraints(), responsible for removing constraints related to the current view

*   **Create ChatDetailViewController:** Instantiates a ChatDetailViewController instance

*   **Set presentation styles:** Sets the presentation and transition styles for the view controller

*   **Pass profile information:** Passes the profile, origniatorProfile, and receipentProfile objects to the new view controller

*   **Set whichTab property:** Sets the whichTab property to 1, indicating a specific tab or view within the ChatDetailViewController

*   **Determine chat type:** Checks the accessibilityHint of the sender button to determine whether it’s a “Self” or “Business” chat
    *   **For “Self” chats:** Sets the businessname, originatorname, and recipientProfileImage properties of the view controller based on the chattables array and the button’s tag

    *   **For “Business” chats:** Sets the businessname, originatorname, and recipientProfileImage properties of the view controller based on the businesstables array and the button’s tag. Additionally, sets the recipientProfileImageBanner property

*   **Present ChatDetailViewController:** Modally presents the ChatDetailViewController over the current view controller.

```
@objc func showChatsActionButton(sender: UIButton){
removeConstraints()
let viewController = ChatDetailViewController()
viewController.modalPresentationStyle = .overCurrentContext
viewController.modalTransitionStyle = .crossDissolve
viewController.profile = profile
viewController.origniatorProfile = origniatorProfile
viewController.receipentProfile = receipentProfile
viewController.whichTab = 1
if(sender.accessibilityHint == "Self"){
viewController.businessname = chattables[sender.tag].receipentname
viewController.originatorname = (profile?.name)!
viewController.recipientProfileImage = chattables[sender.tag].receipentprofile
}else{
viewController.businessname = (profile?.businessname)!
viewController.originatorname =  businesstables[sender.tag].originatorname
viewController.recipientProfileImage =  businesstables[sender.tag].originatorprofile
viewController.recipientProfileImageBanner =  profile?.personalprofile
}
self.present(viewController, animated: true, completion: nil)
}
```

#### Removing Constraints

The removeConstraints() function is designed to remove all constraints and subviews associated with the main UI elements of the ChatViewController. This is to prepare the view for a modal presentation or other UI changes.

*   **Removes constraints:** Iterates through the constraints of each UI element (tabBar, chatTableView, businessTableView, headerView, and headerLabel) and removes them

*   **Removes subviews:** Removes each UI element from its superview

```
func removeConstraints(){
tabBar.removeConstraints(tabBar.constraints)
tabBar.removeFromSuperview()
chatTableView.removeConstraints(chatTableView.constraints)
chatTableView.removeFromSuperview()
businessTableView.removeConstraints(businessTableView.constraints)
businessTableView.removeFromSuperview()
headerView.removeConstraints(headerView.constraints)
headerView.removeFromSuperview()
headerLabel.removeConstraints(headerLabel.constraints)
headerLabel.removeFromSuperview()
}
}
```

#### Complete Code

The complete code of ChatViewController is given below:

```
//
//  ChatViewController.swift
//  Chat
//
//  Created by Shantanu Baruah on 6/25/24.
//
import UIKit
class ChatViewController: UIViewController, UITabBarDelegate, UITableViewDelegate, UITableViewDataSource {
var profile:Profile?
var origniatorProfile:Profile?
var receipentProfile:Profile?
var chattables:[Chattable] = []
var businesstables:[Businesstable] = []
private let tabBar:UITabBar = {
let tabbar = UITabBar()
tabbar.translatesAutoresizingMaskIntoConstraints = false
tabbar.backgroundColor = UIColor.init(red: 171/255, green: 189/255, blue: 217/255, alpha: 1)
let saveSymbolConfiguration = UIImage.SymbolConfiguration(pointSize: 16, weight: .black)
let contact = UIImage(systemName: "doc.on.clipboard", withConfiguration: saveSymbolConfiguration)
let contactbutton = UITabBarItem(title: "Contacts", image: contact, selectedImage: contact)
contactbutton.tag = 1
let chat = UIImage(systemName: "doc.plaintext", withConfiguration: saveSymbolConfiguration)
let chatbutton = UITabBarItem(title: "Chats", image: chat, selectedImage: chat)
chatbutton.tag = 2
let profile = UIImage(systemName: "person.fill", withConfiguration: saveSymbolConfiguration)
let profilebutton = UITabBarItem(title: "Profile", image: profile, selectedImage: profile)
profilebutton.tag = 0
tabbar.setItems([profilebutton, contactbutton, chatbutton], animated: false)
return tabbar
}()
private let chatTableView:UITableView = {
let tableView = UITableView()
tableView.translatesAutoresizingMaskIntoConstraints = false
tableView.backgroundColor = .white
tableView.isScrollEnabled = true
return tableView
}()
private let businessTableView:UITableView = {
let tableView = UITableView()
tableView.translatesAutoresizingMaskIntoConstraints = false
tableView.backgroundColor = .white
tableView.isScrollEnabled = true
return tableView
}()
private let headerView: UIView = {
let view = UIView()
view.translatesAutoresizingMaskIntoConstraints = false
view.backgroundColor = .systemGray6
return view
}()
private let headerLabel:UILabel = {
let label = UILabel()
label.text = "Chats"
label.translatesAutoresizingMaskIntoConstraints = false
label.textColor = UIColor(displayP3Red: 128/255, green: 0/255, blue: 128/255, alpha: 1)
label.textAlignment = .center
label.font = UIFont.boldSystemFont(ofSize: 35)
return label
}()
override func viewDidLoad() {
super.viewDidLoad()
getChatTables()
}
func setup(){
view.backgroundColor = .white //
tabBar.delegate = self
tabBar.selectedItem = tabBar.items![1]
chatTableView.delegate = self
chatTableView.dataSource = self
chatTableView.register(ChatTableViewCell.self, forCellReuseIdentifier: "chat")
chatTableView.layer.borderWidth = 0
chatTableView.layer.borderColor = UIColor.white.cgColor
chatTableView.separatorStyle = .singleLine
businessTableView.delegate = self
businessTableView.dataSource = self
businessTableView.register(ChatTableViewCell.self, forCellReuseIdentifier: "business")
businessTableView.layer.borderWidth = 0
businessTableView.layer.borderColor = UIColor.white.cgColor
businessTableView.separatorStyle = .singleLine
}
//MARK: - Draw Screen
func drawInitialScreen(){
let screensize: CGRect = UIScreen.main.bounds
let screenWidth = screensize.width
let screenHeight = screensize.height
view.addSubview(headerView)
let constraints = [
headerView.topAnchor.constraint(equalTo: view.topAnchor, constant: 0),
headerView.leftAnchor.constraint(equalTo: view.leftAnchor, constant: 0),
headerView.widthAnchor.constraint(equalToConstant: screenWidth),
headerView.heightAnchor.constraint(equalToConstant: 100)
]
NSLayoutConstraint.activate(constraints)
headerView.addSubview(headerLabel)
let constraints0 = [
headerLabel.topAnchor.constraint(equalTo: view.topAnchor, constant: 50),
headerLabel.widthAnchor.constraint(equalToConstant: screenWidth),
headerLabel.heightAnchor.constraint(equalToConstant: 40)
]
NSLayoutConstraint.activate(constraints0)
if(chattables.count > 0){
view.addSubview(chatTableView)
let constraints4 = [
chatTableView.topAnchor.constraint(equalTo: headerView.bottomAnchor, constant: 0),
chatTableView.leftAnchor.constraint(equalTo: view.leftAnchor, constant: 5),
chatTableView.widthAnchor.constraint(equalToConstant: screenWidth - 10),
chatTableView.heightAnchor.constraint(equalToConstant: screenHeight - 50)
]
NSLayoutConstraint.activate(constraints4)
}else{
view.addSubview(businessTableView)
let constraints4 = [
businessTableView.topAnchor.constraint(equalTo: headerView.bottomAnchor, constant: 0),
businessTableView.leftAnchor.constraint(equalTo: view.leftAnchor, constant: 5),
businessTableView.widthAnchor.constraint(equalToConstant: screenWidth - 10),
businessTableView.heightAnchor.constraint(equalToConstant: screenHeight - 50)
]
NSLayoutConstraint.activate(constraints4)
}
view.addSubview(tabBar)
let constraints5 = [
tabBar.bottomAnchor.constraint(equalTo: view.bottomAnchor, constant: 5),
tabBar.leftAnchor.constraint(equalTo: view.leftAnchor, constant: 5),
tabBar.widthAnchor.constraint(equalToConstant: screenWidth),
tabBar.heightAnchor.constraint(equalToConstant: 80)
]
NSLayoutConstraint.activate(constraints5)
}
//MARK: - TabBar Screen
func tabBar(_ tabBar: UITabBar, didSelect item: UITabBarItem) {
switch item.tag {
case 0:
popUpContact()
case 2:
popUpChat()
default:
popUpMarket()
}
}
func popUpContact(){
let viewController = ContactViewController()
viewController.profile = profile
viewController.origniatorProfile = origniatorProfile
viewController.receipentProfile = receipentProfile
viewController.modalPresentationStyle = .overCurrentContext
viewController.modalTransitionStyle = .crossDissolve
self.present(viewController, animated: true, completion: nil)
}
func popUpChat(){
}
func popUpMarket(){
}
//MARK: - Query for Chat Tables
func getChatTables(){
if(profile?.businessname == ""){
let chattable = Chattable()
chattable.gettableNames{ [self] (chattables, error) in
if let error = error {
print("Error: \(error)")
setup()
drawInitialScreen()
}else{
self.chattables = chattables
getlastChatFromChatTable(fromWhere: "Personal")
}
}
}else if(profile?.businessname != nil){
let businesstable = Businesstable()
businesstable.gettableNames(tablename: (profile?.businessname)!) { [self] (businesstables, error) in
if let error = error {
print("Error: \(error)")
let chattable = Chattable()
chattable.gettableNames{ [self] (chattables, error) in
if let error = error {
print("Error: \(error)")
setup()
drawInitialScreen()
}else{
self.chattables = chattables
getlastChatFromChatTable(fromWhere: "Personal")
}
}
}else{
self.businesstables = businesstables
getlastChatFromChatTable(fromWhere: "Business")
}
}
}
}
func getlastChatFromChatTable(fromWhere: String){
let chat:Conversation = Conversation()
var counter:Int = 1
if(fromWhere == "Personal"){
for chattable in chattables{
chat.getlastChat(chattablename: chattable.chattablename) { [self] (chats, error) in
if let error = error {
print("Error: \(error)")
}else{
if(chats.count > 0){
chattable.lastchat = chats[0].chattext
}
if(chattables.count == counter){
setup()
drawInitialScreen()
}else{
counter = counter + 1
}
}
}
}
}else{
for chattable in businesstables{
chat.getlastChat(chattablename: chattable.chattablename) { [self] (chats, error) in
if let error = error {
print("Error: \(error)")
}else{
if(chats.count > 0){
chattable.lastchat = chats[0].chattext
}
if(businesstables.count == counter){
setup()
drawInitialScreen()
}else{
counter = counter + 1
}
}
}
}
}
}
//MARK: - Table Functions
func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
if(tableView == chatTableView){
return chattables.count
}else{
return businesstables.count
}
}
func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
if(tableView == chatTableView){
let cell = tableView.dequeueReusableCell(withIdentifier: "chat", for: indexPath) as! ChatTableViewCell
cell.nameButton.setTitle(chattables[indexPath.row].receipentname, for: .normal)
cell.titleButton.setTitle(chattables[indexPath.row].lastchat, for: .normal)
cell.selectionStyle = .none
let file = self.chattables[indexPath.row].receipentprofile.fileURL
if let data = NSData(contentsOf: file!) {
cell.profileImageView.image = UIImage(data: data as Data)
}
cell.profileImageView.layer.cornerRadius =  30
cell.nameButton.addTarget(self, action: #selector(showChatsActionButton), for: .touchUpInside)
cell.titleButton.addTarget(self, action: #selector(showChatsActionButton), for: .touchUpInside)
cell.nameButton.tag = indexPath.row
cell.titleButton.tag = indexPath.row
cell.nameButton.accessibilityHint = "Self"
cell.titleButton.accessibilityHint = "Self"
return cell
}else{
let cell = tableView.dequeueReusableCell(withIdentifier: "business", for: indexPath) as! ChatTableViewCell
cell.nameButton.setTitle(businesstables[indexPath.row].originatorname, for: .normal)
cell.titleButton.setTitle(businesstables[indexPath.row].lastchat, for: .normal)
cell.selectionStyle = .none
let file = self.businesstables[indexPath.row].originatorprofile.fileURL
if let data = NSData(contentsOf: file!) {
cell.profileImageView.image = UIImage(data: data as Data)
}
cell.profileImageView.layer.cornerRadius =  30
cell.nameButton.addTarget(self, action: #selector(showChatsActionButton), for: .touchUpInside)
cell.titleButton.addTarget(self, action: #selector(showChatsActionButton), for: .touchUpInside)
cell.nameButton.tag = indexPath.row
cell.titleButton.tag = indexPath.row
cell.nameButton.accessibilityHint = "Business"
cell.titleButton.accessibilityHint = "Business"
return cell
}
}
/*
The Height of the Cell Row is set to default 40 pixels
*/
func tableView(_ tableView: UITableView, heightForRowAt indexPath: IndexPath) -> CGFloat {
return 100
}
//MARK: - Button Action
@objc func showChatsActionButton(sender: UIButton){
removeConstraints()
let viewController = ChatDetailViewController()
viewController.modalPresentationStyle = .overCurrentContext
viewController.modalTransitionStyle = .crossDissolve
viewController.profile = profile
viewController.origniatorProfile = origniatorProfile
viewController.receipentProfile = receipentProfile
viewController.whichTab = 1
if(sender.accessibilityHint == "Self"){
viewController.businessname = chattables[sender.tag].receipentname
viewController.originatorname = (profile?.name)!
viewController.recipientProfileImage = chattables[sender.tag].receipentprofile
}else{
viewController.businessname = (profile?.businessname)!
viewController.originatorname =  businesstables[sender.tag].originatorname
viewController.recipientProfileImage =  businesstables[sender.tag].originatorprofile
viewController.recipientProfileImageBanner =  profile?.personalprofile
}
self.present(viewController, animated: true, completion: nil)
}
//MARK: - Remove Constraints
func removeConstraints(){
tabBar.removeConstraints(tabBar.constraints)
tabBar.removeFromSuperview()
chatTableView.removeConstraints(chatTableView.constraints)
chatTableView.removeFromSuperview()
businessTableView.removeConstraints(businessTableView.constraints)
businessTableView.removeFromSuperview()
headerView.removeConstraints(headerView.constraints)
headerView.removeFromSuperview()
headerLabel.removeConstraints(headerLabel.constraints)
headerLabel.removeFromSuperview()
}
}
```

# 14. Conversation Between Users – Table Cell Definition

## View Classes for Table Cells

In our view classes while defining table views, we have used three different custom UITableViewCell classes. The UITableViewCell class helps to customize the table row to display data in a manner which is more user-friendly. It provides a structured container for displaying data in a list-like format, common in many iOS apps, and can be used to customize the appearance and layout of a UITableViewCell to match our app’s design.

### ContactTableViewCell

The below code defines a custom UITableViewCell subclass named ContactTableViewCell. This subclass is specifically designed for displaying contact-related information within a table view.

*   **Import UIKit:** Imports the UIKit framework, which provides the necessary classes and protocols for building user interfaces.

*   **Custom UITableViewCell class:** Defines a new class named ContactTableViewCell that inherits from UITableViewCell. This allows us to create custom table view cells with specific layouts and content.

```
import UIKit
class ContactTableViewCell: UITableViewCell {
```

#### UI Object Details

The below code defines three properties for the ContactTableViewCell class:

**profileImageView:**

*   This property creates a new UIImageView instance.

*   It sets a default image using UIImage(systemName: “person.fill”), displaying a person icon.

*   It configures the image view with properties for the following:
    *   Content mode: .scaleAspectFit to fit the image within the view’s bounds.

    *   Auto Layout constraints: translatesAutoresizingMaskIntoConstraints is set to false to allow programmatic layout.

    *   Rounded corners: layer.masksToBounds is set to true for rounded corners.

    *   Border: layer.borderWidth and layer.borderColor define a one-pixel wide border with system gray color.

*   The closure syntax ({ ... }) initializes the image view with these settings and returns it, assigning it to the profileImageView property.

**nameButton:**

*   This property creates a new UIButton instance.

*   It disables automatic constraints with translatesAutoresizingMaskIntoConstraints = false.

*   It sets default button properties:
    *   Text color: UIColor.black for black text color.

    *   Text alignment: .left for left-aligned text and .top for top-aligned text.

    *   Font: UIFont.systemFont(ofSize: 20, weight: .semibold) sets the font to system font with a size of 20 and a semibold weight.

**chatButton:**

*   This property creates a new UIButton instance.

*   It disables automatic constraints with translatesAutoresizingMaskIntoConstraints = false.

*   It sets a default image using UIImage(systemName: “message.fill”), displaying a message icon.

```
var profileImageView: UIImageView = {
let imageView = UIImageView()
imageView.image = UIImage(systemName: "person.fill")
imageView.contentMode = .scaleAspectFit
imageView.translatesAutoresizingMaskIntoConstraints = false
imageView.layer.masksToBounds = true
imageView.layer.borderWidth = 1
imageView.layer.borderColor = UIColor.systemGray.cgColor
return imageView
}()
let nameButton:UIButton = {
let uiButton  = UIButton()
uiButton.translatesAutoresizingMaskIntoConstraints = false
uiButton.setTitleColor(UIColor.black, for: .normal)
uiButton.contentHorizontalAlignment = .left
uiButton.contentVerticalAlignment = .top
uiButton.titleLabel?.font = UIFont.systemFont(ofSize: 20, weight: .semibold)
return uiButton
}()
let chatButton:UIButton = {
let uiButton  = UIButton()
uiButton.translatesAutoresizingMaskIntoConstraints = false
uiButton.setImage(UIImage(systemName: "message.fill"), for: .normal)
return uiButton
}()
```

#### Lifecycle Method

This code defines the layout and configuration of the ContactTableViewCell. It places the profileImageView, nameButton, and chatButton within the cell’s content view using Auto Layout constraints. The following are the key elements of the code:

**awakeFromNib()**

*   This method is called when the cell is loaded from the nib file (if used) or when it’s created programmatically.

*   In this case, there’s no specific initialization code, so the method simply calls super.awakeFromNib().

**setSelected(_:animated:)**

*   This method is called when the cell is selected or deselected. Here’s where the actual layout of the cell’s content is defined:
    *   Adds profileImageView, nameButton, and chatButton as subviews of contentView.

    *   Sets Auto Layout constraints for each subview to position them within the cell:
        *   profileImageView is placed at the top-left corner with a fixed size.

        *   nameButton is placed to the right of profileImageView with a fixed height.

        *   chatButton is placed below nameButton and to the right of profileImageView.

```
override func awakeFromNib() {
super.awakeFromNib()
// Initialization code
}
override func setSelected(_ selected: Bool, animated: Bool) {
super.setSelected(selected, animated: animated)
let screensize: CGRect = UIScreen.main.bounds
let screenWidth = screensize.width
contentView.addSubview(profileImageView)
let constraints = [
profileImageView.topAnchor.constraint(equalTo: contentView.topAnchor, constant: 5),
profileImageView.leftAnchor.constraint(equalTo: contentView.leftAnchor, constant: 5),
profileImageView.widthAnchor.constraint(equalToConstant: 120),
profileImageView.heightAnchor.constraint(equalToConstant: 120)
]
NSLayoutConstraint.activate(constraints)
contentView.addSubview(nameButton)
let constraints1 = [
nameButton.topAnchor.constraint(equalTo: contentView.topAnchor, constant: 5),
nameButton.leftAnchor.constraint(equalTo: profileImageView.rightAnchor, constant: 5),
nameButton.widthAnchor.constraint(equalToConstant: screenWidth),
nameButton.heightAnchor.constraint(equalToConstant: 20)
]
NSLayoutConstraint.activate(constraints1)
contentView.addSubview(chatButton)
let constraints5 = [
chatButton.topAnchor.constraint(equalTo: nameButton.bottomAnchor, constant: 5),
chatButton.leftAnchor.constraint(equalTo: profileImageView.rightAnchor, constant: 10),
chatButton.widthAnchor.constraint(equalToConstant: 80),
chatButton.heightAnchor.constraint(equalToConstant: 80)
]
NSLayoutConstraint.activate(constraints5)
}
}
```

#### Complete Code

The complete code of “Contact” TableViewCell is given below:

```
//
//  ContactTableViewCell.swift
//  Chat
//
//  Created by Shantanu Baruah on 6/7/24.
//
import UIKit
class ContactTableViewCell: UITableViewCell {
var profileImageView: UIImageView = {
let imageView = UIImageView()
imageView.image = UIImage(systemName: "person.fill")
imageView.contentMode = .scaleAspectFit
imageView.translatesAutoresizingMaskIntoConstraints = false
imageView.layer.masksToBounds = true
imageView.layer.borderWidth = 1
imageView.layer.borderColor = UIColor.systemGray.cgColor
return imageView
}()
let nameButton:UIButton = {
let uiButton  = UIButton()
uiButton.translatesAutoresizingMaskIntoConstraints = false
uiButton.setTitleColor(UIColor.black, for: .normal)
uiButton.contentHorizontalAlignment = .left
uiButton.contentVerticalAlignment = .top
uiButton.titleLabel?.font = UIFont.systemFont(ofSize: 20, weight: .semibold)
return uiButton
}()
let chatButton:UIButton = {
let uiButton  = UIButton()
uiButton.translatesAutoresizingMaskIntoConstraints = false
uiButton.setImage(UIImage(systemName: "message.fill"), for: .normal)
return uiButton
}()
override func awakeFromNib() {
super.awakeFromNib()
// Initialization code
}
override func setSelected(_ selected: Bool, animated: Bool) {
super.setSelected(selected, animated: animated)
let screensize: CGRect = UIScreen.main.bounds
let screenWidth = screensize.width
contentView.addSubview(profileImageView)
let constraints = [
profileImageView.topAnchor.constraint(equalTo: contentView.topAnchor, constant: 5),
profileImageView.leftAnchor.constraint(equalTo: contentView.leftAnchor, constant: 5),
profileImageView.widthAnchor.constraint(equalToConstant: 120),
profileImageView.heightAnchor.constraint(equalToConstant: 120)
]
NSLayoutConstraint.activate(constraints)
contentView.addSubview(nameButton)
let constraints1 = [
nameButton.topAnchor.constraint(equalTo: contentView.topAnchor, constant: 5),
nameButton.leftAnchor.constraint(equalTo: profileImageView.rightAnchor, constant: 5),
nameButton.widthAnchor.constraint(equalToConstant: screenWidth),
nameButton.heightAnchor.constraint(equalToConstant: 20)
]
NSLayoutConstraint.activate(constraints1)
contentView.addSubview(chatButton)
let constraints5 = [
chatButton.topAnchor.constraint(equalTo: nameButton.bottomAnchor, constant: 5),
chatButton.leftAnchor.constraint(equalTo: profileImageView.rightAnchor, constant: 10),
chatButton.widthAnchor.constraint(equalToConstant: 80),
chatButton.heightAnchor.constraint(equalToConstant: 80)
]
NSLayoutConstraint.activate(constraints5)
}
}
```

### ChatDetailsTableViewCell

The ChatDetailsTableViewCell class is a custom subclass of UITableViewCell designed to display individual chat messages within a conversation. The breakdown of the code is given below:

*   **Import UIKit:** Includes the necessary UIKit framework for UI elements and functionalities.

*   **Class declaration:** Defines a new class named ChatDetailsTableViewCell inheriting from UITableViewCell.

**Class properties:**

*   **buttonPosition:** A CGFloat property to store the x-position of the chat bubble button

*   **buttonWidth:** A CGFloat property to store the width of the chat bubble button

*   **rightPosition:** A CGFloat property to adjust the right-side positioning of the chat bubble

```
import UIKit
class ChatDetailsTableViewCell: UITableViewCell {
var buttonPosition: CGFloat = 40
var buttonWidth:CGFloat = 120
var rightPosition:CGFloat = 5
```

#### UI Object Definition

The code snippet below defines two more properties for the ChatDetailsTableViewCell class:

**profileImageView:**

This property is identical to the one defined in the ContactTableViewCell. It creates a UIImageView with a default image, rounded corners, and a border.

**chatButton:**

*   This property creates a new UIButton instance configured for displaying chat messages:
    *   Disables automatic constraints (translatesAutoresizingMaskIntoConstraints = false) for programmatic layout

    *   Sets button appearance:
        *   Text color: UIColor.black.

        *   Alignment: .left and .top for left-aligned and top-aligned text.

        *   Font: UIFont.systemFont(ofSize: 14, weight: .regular) sets the font size to 14 with a regular weight.

        *   Rounded corners: layer.cornerRadius = 8 sets rounded corners with a radius of 8 points.

        *   Border: layer.borderWidth = 1 and layer.borderColor = UIColor.black.cgColor define a black 1-pixel wide border.

        *   Background color: backgroundColor = UIColor.white sets a white background for the button.

        *   Multiline text: titleLabel?.numberOfLines = 10 allows the button title to display up to 10 lines of text, for longer messages.

```
var profileImageView: UIImageView = {
let imageView = UIImageView()
imageView.image = UIImage(systemName: "person.fill")
imageView.contentMode = .scaleAspectFit
imageView.translatesAutoresizingMaskIntoConstraints = false
imageView.layer.masksToBounds = true
imageView.layer.borderWidth = 1
imageView.layer.borderColor = UIColor.systemGray.cgColor
return imageView
}()
let chatButton:UIButton = {
let uiButton  = UIButton()
uiButton.translatesAutoresizingMaskIntoConstraints = false
uiButton.setTitleColor(UIColor.black, for: .normal)
uiButton.contentHorizontalAlignment = .left
uiButton.contentVerticalAlignment = .top
uiButton.titleLabel?.font = UIFont.systemFont(ofSize: 14, weight: .regular)
uiButton.layer.cornerRadius = 8
uiButton.layer.borderWidth = 1
uiButton.layer.borderColor = UIColor.black.cgColor
uiButton.backgroundColor = UIColor.white
uiButton.titleLabel?.numberOfLines = 10
return uiButton
}()
```

#### Lifecycle Methods

This code below completes the implementation of the ChatDetailsTableViewCell class by defining the cell’s layout and configuration. The code breakdown is given below:

**awakeFromNib()**

*   This method is called when the cell is loaded from a nib file (if used).

*   In this case, there’s no specific initialization code, so the method simply calls super.awakeFromNib().

**init(style:reuseIdentifier:)**

*   This is the designated initializer for the cell. It’s called when the cell is created programmatically.

*   In this case, it simply calls super.init(style: style, reuseIdentifier: reuseIdentifier) to initialize the base class.

**required init?(coder aDecoder: NSCoder)**

*   This is a required initializer for decoding the cell from a storyboard or nib file.

*   Since we’re not using storyboards or nibs in this example, it simply calls fatalError to indicate that this initializer is not implemented.

**layoutSubviews()**

*   This method is called after the cell’s bounds have been set, allowing for precise layout calculations.

*   Adds profileImageView and chatButton as subviews to the contentView.

*   Sets Auto Layout constraints for both views using NSLayoutConstraint.activate to position them within the cell:
    *   profileImageView is positioned at the top-left corner with specified dimensions.

    *   chatButton is positioned to the right of the profileImageView with a dynamic width based on the buttonWidth property.

```
override func awakeFromNib() {
super.awakeFromNib()
// Initialization code
}
override init(style: UITableViewCell.CellStyle, reuseIdentifier: String?) {
super.init(style: style, reuseIdentifier: reuseIdentifier)
}
required init?(coder aDecoder: NSCoder) {
fatalError("init(coder:) has not been implemented")
}
override func layoutSubviews() {
super.layoutSubviews()
let screensize: CGRect = UIScreen.main.bounds
let screenWidth = screensize.width
contentView.addSubview(profileImageView)
let constraints = [
profileImageView.topAnchor.constraint(equalTo: contentView.topAnchor, constant: 5),
profileImageView.leftAnchor.constraint(equalTo: contentView.leftAnchor, constant: rightPosition),
profileImageView.widthAnchor.constraint(equalToConstant: 30),
profileImageView.heightAnchor.constraint(equalToConstant: 30)
]
NSLayoutConstraint.activate(constraints)
contentView.addSubview(chatButton)
let constraints1 = [
chatButton.leftAnchor.constraint(equalTo: profileImageView.rightAnchor, constant: 5),
chatButton.widthAnchor.constraint(equalToConstant: screenWidth - buttonWidth),
chatButton.heightAnchor.constraint(equalToConstant:buttonPosition)
]
NSLayoutConstraint.activate(constraints1)
}
}
```

#### Complete Code

Complete code of ChatDetailsTableViewCell is given below:

```
//
//  ChatDetailsTableViewCell.swift
//  Chat
//
//  Created by Shantanu Baruah on 6/24/24.
//
import UIKit
class ChatDetailsTableViewCell: UITableViewCell {
var buttonPosition: CGFloat = 40
var buttonWidth:CGFloat = 120
var rightPosition:CGFloat = 5
var profileImageView: UIImageView = {
let imageView = UIImageView()
imageView.image = UIImage(systemName: "person.fill")
imageView.contentMode = .scaleAspectFit
imageView.translatesAutoresizingMaskIntoConstraints = false
imageView.layer.masksToBounds = true
imageView.layer.borderWidth = 1
imageView.layer.borderColor = UIColor.systemGray.cgColor
return imageView
}()
let chatButton:UIButton = {
let uiButton  = UIButton()
uiButton.translatesAutoresizingMaskIntoConstraints = false
uiButton.setTitleColor(UIColor.black, for: .normal)
uiButton.contentHorizontalAlignment = .left
uiButton.contentVerticalAlignment = .top
uiButton.titleLabel?.font = UIFont.systemFont(ofSize: 14, weight: .regular)
uiButton.layer.cornerRadius = 8
uiButton.layer.borderWidth = 1
uiButton.layer.borderColor = UIColor.black.cgColor
uiButton.backgroundColor = UIColor.white
uiButton.titleLabel?.numberOfLines = 10
return uiButton
}()
override func awakeFromNib() {
super.awakeFromNib()
// Initialization code
}
override init(style: UITableViewCell.CellStyle, reuseIdentifier: String?) {
super.init(style: style, reuseIdentifier: reuseIdentifier)
}
required init?(coder aDecoder: NSCoder) {
fatalError("init(coder:) has not been implemented")
}
override func layoutSubviews() {
super.layoutSubviews()
let screensize: CGRect = UIScreen.main.bounds
let screenWidth = screensize.width
contentView.addSubview(profileImageView)
let constraints = [
profileImageView.topAnchor.constraint(equalTo: contentView.topAnchor, constant: 5),
profileImageView.leftAnchor.constraint(equalTo: contentView.leftAnchor, constant: rightPosition),
profileImageView.widthAnchor.constraint(equalToConstant: 30),
profileImageView.heightAnchor.constraint(equalToConstant: 30)
]
NSLayoutConstraint.activate(constraints)
contentView.addSubview(chatButton)
let constraints1 = [
chatButton.leftAnchor.constraint(equalTo: profileImageView.rightAnchor, constant: 5),
chatButton.widthAnchor.constraint(equalToConstant: screenWidth - buttonWidth),
chatButton.heightAnchor.constraint(equalToConstant:buttonPosition)
]
NSLayoutConstraint.activate(constraints1)
}
}
```

### ChatTableViewCell

This code defines a custom UITableViewCell subclass named ChatTableViewCell. This class will be used to display individual chat messages within a table view. Code details are given below:

*   **import UIKit:** Imports the UIKit framework, which provides the necessary classes and protocols for building user interfaces.

*   **class ChatTableViewCell:** UITableViewCell {: Declares a new class named ChatTableViewCell that inherits from UITableViewCell. This allows for customization of the cell’s appearance and behavior.

```
import UIKit
class ChatTableViewCell: UITableViewCell {
```

#### UI Object Definition

The provided code below defines three properties that represent the UI elements for the ChatTableViewCell:

*   **profileImageView:** This creates a rounded image view with a default person icon, to display the sender’s profile picture.

*   **nameButton:** This creates a button with black text and a larger, semibold font, to display the sender’s name.

*   **titleButton:** This creates a button with black text, a smaller regular font, and allows for up to three lines of text, to display a message preview or title.

*   Adding these elements to the cell:

```
var profileImageView: UIImageView = {
let imageView = UIImageView()
imageView.image = UIImage(systemName: "person.fill")
imageView.contentMode = .scaleAspectFit
imageView.translatesAutoresizingMaskIntoConstraints = false
imageView.layer.masksToBounds = true
imageView.layer.borderWidth = 1
imageView.layer.borderColor = UIColor.systemGray.cgColor
return imageView
}()
let nameButton:UIButton = {
let uiButton  = UIButton()
uiButton.translatesAutoresizingMaskIntoConstraints = false
uiButton.setTitleColor(UIColor.black, for: .normal)
uiButton.contentHorizontalAlignment = .left
uiButton.contentVerticalAlignment = .top
uiButton.titleLabel?.font = UIFont.systemFont(ofSize: 20, weight: .semibold)
return uiButton
}()
let titleButton:UIButton = {
let uiButton  = UIButton()
uiButton.translatesAutoresizingMaskIntoConstraints = false
uiButton.setTitleColor(UIColor.black, for: .normal)
uiButton.contentHorizontalAlignment = .left
uiButton.contentVerticalAlignment = .top
uiButton.titleLabel?.font = UIFont.systemFont(ofSize: 15, weight: .regular)
uiButton.titleLabel?.numberOfLines = 3
uiButton.setTitleColor(.black, for: .normal)
return uiButton
}()
```

#### Lifecycle Methods

This code defines the layout for the ChatDetailsTableViewCell, positioning the profileImageView, nameButton, and titleButton within the cell’s content view. The code breakdown is given below:

**awakeFromNib()**

*   This method is called when the cell is loaded from a nib file (if used).

*   In this case, there’s no specific initialization code, so it simply calls super.awakeFromNib().

**setSelected(_:animated:)**

*   This method is called when the cell is selected or deselected.

*   The core logic for laying out the cell’s content occurs here:
    *   Adds profileImageView, nameButton, and titleButton as subviews to the contentView.

    *   Uses Auto Layout constraints to position the elements:
        *   profileImageView is placed at the top-left corner with fixed dimensions.

        *   nameButton is placed to the right of the profileImageView with a fixed height.

        *   titleButton is placed below the nameButton and to the right of the profileImageView.

```
override func awakeFromNib() {
super.awakeFromNib()
// Initialization code
}
override func setSelected(_ selected: Bool, animated: Bool) {
super.setSelected(selected, animated: animated)
let screensize: CGRect = UIScreen.main.bounds
let screenWidth = screensize.width
contentView.addSubview(profileImageView)
let constraints = [
profileImageView.topAnchor.constraint(equalTo: contentView.topAnchor, constant: 5),
profileImageView.leftAnchor.constraint(equalTo: contentView.leftAnchor, constant: 5),
profileImageView.widthAnchor.constraint(equalToConstant: 60),
profileImageView.heightAnchor.constraint(equalToConstant: 60)
]
NSLayoutConstraint.activate(constraints)
contentView.addSubview(nameButton)
let constraints1 = [
nameButton.topAnchor.constraint(equalTo: contentView.topAnchor, constant: 5),
nameButton.leftAnchor.constraint(equalTo: profileImageView.rightAnchor, constant: 5),
nameButton.widthAnchor.constraint(equalToConstant: screenWidth),
nameButton.heightAnchor.constraint(equalToConstant: 20)
]
NSLayoutConstraint.activate(constraints1)
contentView.addSubview(titleButton)
let constraints2 = [
titleButton.topAnchor.constraint(equalTo: nameButton.bottomAnchor, constant: 5),
titleButton.leftAnchor.constraint(equalTo: profileImageView.rightAnchor, constant: 5),
titleButton.widthAnchor.constraint(equalToConstant: screenWidth - 100),
titleButton.heightAnchor.constraint(equalToConstant: 80)
]
NSLayoutConstraint.activate(constraints2)
}
}
```

#### Complete Code

Complete code of ChatTableViewCell is given below:

```
//
//  ChatTableViewCell.swift
//  Chat
//
//  Created by Shantanu Baruah on 6/25/24.
//
import UIKit
class ChatTableViewCell: UITableViewCell {
var profileImageView: UIImageView = {
let imageView = UIImageView()
imageView.image = UIImage(systemName: "person.fill")
imageView.contentMode = .scaleAspectFit
imageView.translatesAutoresizingMaskIntoConstraints = false
imageView.layer.masksToBounds = true
imageView.layer.borderWidth = 1
imageView.layer.borderColor = UIColor.systemGray.cgColor
return imageView
}()
let nameButton:UIButton = {
let uiButton  = UIButton()
uiButton.translatesAutoresizingMaskIntoConstraints = false
uiButton.setTitleColor(UIColor.black, for: .normal)
uiButton.contentHorizontalAlignment = .left
uiButton.contentVerticalAlignment = .top
uiButton.titleLabel?.font = UIFont.systemFont(ofSize: 20, weight: .semibold)
return uiButton
}()
let titleButton:UIButton = {
let uiButton  = UIButton()
uiButton.translatesAutoresizingMaskIntoConstraints = false
uiButton.setTitleColor(UIColor.black, for: .normal)
uiButton.contentHorizontalAlignment = .left
uiButton.contentVerticalAlignment = .top
uiButton.titleLabel?.font = UIFont.systemFont(ofSize: 15, weight: .regular)
uiButton.titleLabel?.numberOfLines = 3
uiButton.setTitleColor(.black, for: .normal)
return uiButton
}()
override func awakeFromNib() {
super.awakeFromNib()
// Initialization code
}
override func setSelected(_ selected: Bool, animated: Bool) {
super.setSelected(selected, animated: animated)
let screensize: CGRect = UIScreen.main.bounds
let screenWidth = screensize.width
contentView.addSubview(profileImageView)
let constraints = [
profileImageView.topAnchor.constraint(equalTo: contentView.topAnchor, constant: 5),
profileImageView.leftAnchor.constraint(equalTo: contentView.leftAnchor, constant: 5),
profileImageView.widthAnchor.constraint(equalToConstant: 60),
profileImageView.heightAnchor.constraint(equalToConstant: 60)
]
NSLayoutConstraint.activate(constraints)
contentView.addSubview(nameButton)
let constraints1 = [
nameButton.topAnchor.constraint(equalTo: contentView.topAnchor, constant: 5),
nameButton.leftAnchor.constraint(equalTo: profileImageView.rightAnchor, constant: 5),
nameButton.widthAnchor.constraint(equalToConstant: screenWidth),
nameButton.heightAnchor.constraint(equalToConstant: 20)
]
NSLayoutConstraint.activate(constraints1)
contentView.addSubview(titleButton)
let constraints2 = [
titleButton.topAnchor.constraint(equalTo: nameButton.bottomAnchor, constant: 5),
titleButton.leftAnchor.constraint(equalTo: profileImageView.rightAnchor, constant: 5),
titleButton.widthAnchor.constraint(equalToConstant: screenWidth - 100),
titleButton.heightAnchor.constraint(equalToConstant: 80)
]
NSLayoutConstraint.activate(constraints2)
}
}
```

# 15. Conversation Between Users – Controller Definition

## Controller Class

In the Model-View-Controller (MVC) architectural pattern, the Controller is the intermediary between the Model and the View. In our chat application, when the user opens the application for the first time, it will navigate the user to create a profile view controller. For all other cases, it will show the ContactViewController for either adding contact or initiating a chat with an existing contact. Please find below the code breakdown:

**Import UIKit:**

*   This line imports the UIKit framework, essential for building user interfaces in iOS applications.

**Class declaration:**

*   **class ControllerViewController:** UIViewController: Defines a new class named ControllerViewController that inherits from UIViewController. This indicates that this class will manage a view and its interactions.

**Properties:**

*   **userid: String = “”:** A string property to store the user’s ID. It’s initialized with an empty string.

*   **contact: Contact = Contact():** An instance of a Contact class.

*   **profile: Profile?:** An optional profile object. The question mark indicates that it can be nil.

```
import UIKit
class ControllerViewController: UIViewController {
var userid:String = ""
let contact:Contact = Contact()
var profile:Profile?
```

## Lifecycle Method

This method is a lifecycle method in UIKit that is called when the view controller’s view is loaded into memory. It is a common place to perform initial setup tasks for the view controller, such as setting up the user interface, loading data, or registering for notifications.

We are calling a custom method from viewDidLoad() called checkProfileExist(). It is responsible for checking if a user profile exists.

```
override func viewDidLoad() {
super.viewDidLoad()
checkProfileExist()
}
```

## Check Profile Exist Method

This function handles checking for a user profile’s existence and calls different view controller based on the outcome. The following are the steps the code goes through:

**Get iCloud user ID:**

*   Calls contact.getICloudUserID { ... } to retrieve the user’s iCloud user ID using a completion handler.

*   The completion handler receives two parameters:
    *   **userID:** The retrieved iCloud user ID (optional String)

    *   **error:** Any error encountered during retrieval (optional error)

**Handle errors:**

*   If error is not nil, an error message is printed indicating an issue retrieving the iCloud user ID.

**Process user ID (if retrieved successfully):**

*   If userID has a value, it’s stored in the self.userid property.

*   self.contact.group.enter() enters a dispatch group to manage asynchronous tasks.

*   self.contact.checkProfileExist(userID: userID) checks if a user profile exists for the retrieved ID.

*   self.contact.group.notify(queue: .main) { ... } sets up a notification to be called on the main thread when the dispatch group finishes all its tasks.

**Handle profile existence:**

*   Inside the notification block, it checks contact.profileExists (a Boolean property).

*   If contact.profileExists is true:
    *   Sets self.profile to the retrieved contact.profile (assuming it’s populated)

    *   Calls popUpContact() (see below)

*   If contact.profileExists is false:
    *   Calls popUpProfile() (see below)

```
func checkProfileExist(){
contact.getICloudUserID { (userID, error) in
if let error = error {
print("Error: \(error)")
} else if let userID = userID {
self.userid = userID
self.contact.group.enter()
self.contact.checkProfileExist(userID: userID)
self.contact.group.notify(queue: .main) { [self] in
if(contact.profileExists){
self.profile = contact.profile
popUpContact()
}else{
popUpProfile()
}
}
}
}
}
```

## Navigating to View Controllers

These functions handle the presentation of modal view controllers based on whether a user profile exists. Code breakdown is given below:

**popUpProfile()**

*   Creates an instance of CreateProfileViewController

*   Passes the userid to the new view controller

*   Sets the presentation style and transition style for the modal view controller

*   Presents the CreateProfileViewController modally

**popUpContact()**

*   Creates an instance of ContactViewController

*   Passes the profile and userid to the new view controller

*   Sets the presentation style and transition style for the modal view controller

*   Presents the ContactViewController modally

```
func popUpProfile(){
let viewController = CreateProfileViewController()
viewController.userid = userid
viewController.modalPresentationStyle = .overCurrentContext
viewController.modalTransitionStyle = .crossDissolve
self.present(viewController, animated: true, completion: nil)
}
func popUpContact(){
let viewController = ContactViewController()
viewController.profile = profile
viewController.userid = userid
viewController.modalPresentationStyle = .overCurrentContext
viewController.modalTransitionStyle = .crossDissolve
self.present(viewController, animated: true, completion: nil)
}
}
```

## Complete Code

```
//
//  ControllerViewController.swift
//  Chat
//
//  Created by Shantanu Baruah on 6/15/24.
//
import UIKit
class ControllerViewController: UIViewController {
var userid:String = ""
let contact:Contact = Contact()
var profile:Profile?
override func viewDidLoad() {
super.viewDidLoad()
checkProfileExist()
}
func checkProfileExist(){
contact.getICloudUserID { (userID, error) in
if let error = error {
print("Error: \(error)")
} else if let userID = userID {
self.userid = userID
self.contact.group.enter()
self.contact.checkProfileExist(userID: userID)
self.contact.group.notify(queue: .main) { [self] in
if(contact.profileExists){
self.profile = contact.profile
popUpContact()
}else{
popUpProfile()
}
}
}
}
}
func popUpProfile(){
let viewController = CreateProfileViewController()
viewController.userid = userid
viewController.modalPresentationStyle = .overCurrentContext
viewController.modalTransitionStyle = .crossDissolve
self.present(viewController, animated: true, completion: nil)
}
func popUpContact(){
let viewController = ContactViewController()
viewController.profile = profile
viewController.userid = userid
viewController.modalPresentationStyle = .overCurrentContext
viewController.modalTransitionStyle = .crossDissolve
self.present(viewController, animated: true, completion: nil)
}
}
```

## In Summary

This brings an end to our basic chat application building chapter. The provided code outlines a basic structure for a chat-based application using the Model-View-Controller (MVC) pattern.

**The following are the key components of the application:**

*   **ControllerViewController:** This is the main controller class that manages the application’s flow. It handles user authentication, profile management, and navigation between different views.

*   **ContactTableViewCell:** A custom table view cell designed for displaying contact information, including profile picture, name, and potentially a chat preview.

*   **ChatDetailsTableViewCell:** Another custom table view cell specifically designed for displaying individual chat messages with details like sender, message content, and timestamps.

**We also explored the following core functionalities:**

*   **User authentication:** Checks for a user profile and presents either a profile creation screen or a contact list

*   **Contact list:** Displays a list of contacts with basic information using the ContactTableViewCell

*   **Chat details:** Presents a detailed view of a chat conversation using the ChatDetailsTableViewCell

**In implementing MVC pattern, we accomplished the following:**

*   The code demonstrates basic MVC principles by separating concerns between the controller, view, and model (implied through the Contact class and potential data structures).

*   The use of table view cells for displaying data aligns with the MVC pattern.

### Where You Can Go from Here

The following are the areas you can strengthen further to take your learning process to the next dimension.

#### Modal Layer

*   Consider using a networking layer for fetching data from external sources.

#### View Layer

*   Enhance the UI with animations, transitions, and visual cues.

*   Improve accessibility by providing appropriate labels and hints for UI elements.

*   Optimize the layout and performance for different screen sizes and devices.

#### Controller Logic

*   Implement error handling and edge case scenarios.

*   Improve code organization and readability through refactoring.

*   Consider using design patterns like MVVM or VIPER for larger and more complex applications.

#### Data Management

*   Implement efficient data fetching and caching mechanisms.

### User Experience

*   Focus on user-friendly interactions and intuitive navigation.

*   Provide clear feedback to the user during loading or processing tasks.

Happy learning.

Index A animateView Animations animate view code firstViewController lifecycle methods system-defined function UI objects UIView.animate function B Block methods CKQueryOperation object complete code example code function definition query query matched block query result block return value Businesstable modal class code get table name method init method NSObject businessType C cellForRowAt delegate method ChatDetailViewController cancel function code draw object on screen handleTextChange() function layout subview lifecycle method lifecycle method properties queryChats() function removeChatDetailsConstraints() function running app save chat event method scrollToLastRow() function setup() function text view event methods UI components UI table methods ChatViewController class properties code display chat button action method draw screen method getChatTables get last chat method lifecycle method removing constraints setup() function tab bar method table view methods UI components checkProfileExist() CKModifyRecordsOperation CloudKit CKModifyRecordsOperation system function completion@escaping Contact modal class class level variables code CRUD functions get contacts method getICloudUserID profileExists UIKit module ContactViewController business method chat button action event class definition code draw screen lifecycle method running app setup() method tab bar method table methods text field event methods UI elements Controller class class declaration code lifecycle method profile exit method, check properties UIKit framework view controllers, navigating Conversation modal class chat table exists class variables cloud second method, save code get conversation method get last chat init methods NSObject save conversation subscription CreateProfileViewController business objects class definition code draw profile screen image-enhanced functions, class extensions image event method lifecycle method run app save action method saving profile information setup() method tab bar method table view method text field event methods UI object definition user’s documents directory D didSelectRowAt delegate method DispatchGroup drawInitialScreen() functions drawPersonalScreen drawTabBar() function drawTopBar() function Drop-down list business class business types code CreateProfileViewController arrays of class business type objects businessTypeTextField code drawing screen setup() method table view functions text field event functions UITableView viewDidLoad features E @escaping @escaping keyword Extensions functions string isValidEmail isValidURL lastIndex system-defined class user-defined objects F fetchUserRecordID fileURL function firstMatch function G getChatTables() function getContacts method getICloudUserID H handleTextChange() function heightForRowAt delegate method I, J, K, L iCloud public database block methods class extension conversational data drop-down list image manipulation Image management contact modal class, code custom table cell photo library and camera profile model class asynchronous calls asynchronous mode block of code init function Xcode toolkits UIImageView upload image, view controllers code data image manipulation functions imagePickerController operations presentCamera presentPhoto() presentPhotoOptions ProfileCreationViewController profileSave save button saveProfile UIImagePicker UIImageView UIImageView object view controller, display image init method M Modal classes business modal Businesstable chattable contact modal conversation one-to-one relationship profile Model-View-Controller (MVC) application components functionalities layers user experience modifyRecordsResultBlock Multiple iCloud tables class definition code save business profile save function save operation table record initialization MVC design controller definition example code Controller UIViewController class Model UIViewController class View UIViewController class model definition checkProfileExist code error constant iCloud query operation profile class profileExists recordID Swift structure UIKit objects view definition N, O numberOfRowsInSection delegate method P popUpChat() function popUpContact() function popUpMarket() function popUpProfile() popUpProfile function presentPhotoOptions() function Profile modal class class level object code init method NSObject save profile method profileSave() Push notifications App Delegate registration chat application subscription, setting Q queryChats() function queryResultBlock R Range system-defined functions recordMatchedBlock removeChatDetailsConstraints() function removeConstraints() function S saveAction method saveProfile saveRecordsToCloud scrollToLastRow() function setBusiness() function setup() function setup() method syncQueue T tabBar(_:didSelect:) delegate method tabBar(_:didSelect:) method Table cells ChatDetailsTableViewCell class class properties code lifecycle method UI object ChatTableViewCell code lifecycle methods UI elements ContactTableViewCell code contact-related information lifecycle method UI object UITableViewCell class U UIKit and CloudKit libraries UITableViewDataSource and UITableViewDelegate methods UI text view button method class/class objects code lifecycle methods textview methods User chat app feature record type business chattable conversation profile V, W, X, Y, Z View classes ChatDetailViewController ChatViewController ContactViewController CreateProfileViewController UI viewDidLayoutSubviews() method viewDidLoad lifecycle method viewDidLoad() method viewWillAppear lifecycle method