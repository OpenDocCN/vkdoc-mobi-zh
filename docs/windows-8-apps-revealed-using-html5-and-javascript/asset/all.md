![](cover.jpeg)

![](index-1_1.jpg)

[www.it-ebooks.info](http://www.it-ebooks.info/)

![](index-2_1.png)

**Windows 8 Apps**

**Revealed**

**Using HTML5 and JavaScript**

**Adam Freeman**

[www.it-ebooks.info](http://www.it-ebooks.info/)

**Apress Media**

**Windows 8 Apps Revealed: Using HTML5 and JavaScript** Copyright © 2012 by Adam Freeman

Th

is work is subject to copyright. All rights are reserved by Apress Media, whether the whole or part of the material is concerned, specifi cally the rights of translation, reprinting, reuse of illustrations, recitation, broadcasting, reproduction on microfi lms or in any other physical way, and transmission or information storage and retrieval, electronic adaptation, computer software, or by similar or dissimilar methodology now known or hereafter developed. Exempted from this legal reservation are brief excerpts in connection with reviews or scholarly analysis or material supplied specifi cally for the purpose of being entered and executed on a computer system, for exclusive use by the purchaser of the work. Duplication of this publication or parts thereof is permitted only under the provisions of the Copyright Law of the Publisher’s location, in its current version, and permission for use must always be obtained from Springer. Permissions for use may be obtained through RightsLink at the Copyright Clearance Center. Violations are liable to prosecution under the respective Copyright Law.

ISBN 978-1-4302-5013-5

ISBN 978-1-4302-5014-2 (eBook)

Trademarked names, logos, and images may appear in this book. Rather than use a trademark symbol with every occurrence of a trademarked name, logo, or image we use the names, logos, and images only in an editorial fashion and to the benefi t of the trademark owner, with no intention of infringement of the trademark.

Th

e use in this publication of trade names, trademarks, service marks, and similar terms, even if they are not identifi ed as such, is not to be taken as an expression of opinion as to whether or not they are subject to proprietary rights.

While the advice and information in this book are believed to be true and accurate at the date of publication, neither the authors nor the editors nor the publisher can accept any legal responsibility for any errors or omissions that may be made. Th

e publisher makes no warranty, express or implied,

with respect to the material contained herein.

President and Publisher of Apress: Paul Manning

Lead Editor: Ewan Buckingham

Technical Reviewer: Fabio Claudio Ferracchiati

Apress Editorial Board: Steve Anglin, Ewan Buckingham, Gary Cornell, Louise Corrigan, Morgan Ertel, Jonathan Gennick, Jonathan Hassell, Robert Hutchinson, Michelle Lowman,James Markham, Matthew Moodie, Jeff Olson, Jeff rey Pepper, Douglas Pundick, Ben Renow-Clarke, Dominic Shakeshaft, Gwenan Spearing, Matt Wade, Tom Welsh Coordinating Editor: Christine Ricketts

Copy Editor: Kim Wimpsett

Compositor: SPi Global

Indexer: SPi Global

Artist: SPi Global

Cover Designer: Anna Ishchenko

Distributed to the book trade worldwide by Springer Science+Business Media New York, 233 Spring Street, 6th Floor, New York, NY 10013\. Phone 1-800-SPRINGER, fax (201) 348-4505, e-mail orders-ny@springer-sbm.com, or visit www.springeronline.com.

For information on translations, please e-mail rights@apress.com, or visit www.apress.com.

Apress and friends of ED books may be purchased in bulk for academic, corporate, or promotional use. Ebook versions and licenses are also available for most titles. For more information, reference our Special Bulk Sales–eBook Licensing web page at www.apress.com/bulk-sales.

Any source code or other supplementary materials referenced by the author in this text is available to readers at www.apress.com. For detailed information about how to locate your book’s source code, go to www.apress.com/source-code

[www.it-ebooks.info](http://www.it-ebooks.info/)

*Dedicated to my lovely wife, Jacqui Griff yth.*

—Adam Freeman

[www.it-ebooks.info](http://www.it-ebooks.info/)

**Contents at a Glance**

**About the Author .............................................................................. xi** **About the Technical Reviewer ........................................................ xiii** **Acknowledgments ........................................................................... xv** **Introduction ................................................................................... xvii** **Chapter 1: Getting Started**

 **■

**.............................................................. 1**

**Chapter 2: Data and Bindings**

 **■

**....................................................... 21**

**Chapter 3: Application Contr**

 **■

**ols .................................................... 47**

**Chapter 4: La**

 **■

**youts and Tiles ........................................................ 73**

**Chapter 5: Life-Cycle Ev**

 **■

**ents ....................................................... 103**

**Index .............................................................................................. 125**

v

[www.it-ebooks.info](http://www.it-ebooks.info/)

**Contents**

**About the Author .............................................................................. xi** **About the Technical Reviewer ........................................................ xiii** **Acknowledgments ........................................................................... xv** **Introduction ................................................................................... xvii** **Chapter 1:**

 **■

**Getting Started .............................................................. 1**

About This Book ...................................................................................... 1

What Do You Need to Know Before You Read This Book? ......................................... 1

Do You Need to Know About HTML5? ....................................................................... 2

What Software Do You Need for This Book? ............................................................. 2

What Is the Structure of This Book? ......................................................................... 3

Tell Me More About the Example Windows 8 App ..................................................... 4

Is There a Lot of Code in This Book? ......................................................................... 5

Getting Up and Running .......................................................................... 6

Creating the Project .................................................................................................. 6

Exploring the default.html File .................................................................................. 8

Exploring the default.css File ................................................................................. 11

Exploring the default.js File .................................................................................... 14

Starting and Debugging a JavaScript Windows 8 App .......................... 16

Reloading the Windows 8 App ............................................................................... 17

Debugging Windows Store apps ............................................................................. 19

Summary ............................................................................................... 20

vii

[www.it-ebooks.info](http://www.it-ebooks.info/)

■ CONTENTS

**Chapter 2:**

 **■

**Data and Bindings ....................................................... 21**

Creating the JavaScript File .................................................................. 22

Using Code Completion........................................................................................... 23

Reducing Global Namespace Pollution ................................................................... 24

Returning to the View Model .................................................................................. 25

Using Data Binding ................................................................................ 26

Using Basic Declarative Bindings ........................................................................... 27

Creating Dynamic Bindings .................................................................................... 29

Updating an Observable Data Item ......................................................................... 32

Creating Observable Arrays ................................................................... 35

Using Templates .................................................................................... 38

Using the Template ................................................................................................. 39

Responding to List Changes ................................................................................... 41

Tracking the Selected Item ..................................................................................... 41

Applying the Template to the App ........................................................................... 42

Summary ............................................................................................... 45

■**Chapter 3:**

 ****Application Controls .................................................... 47**

Adding an AppBar .................................................................................. 48

Implementing App Bar Buttons ............................................................................... 52

Adding Flyouts ....................................................................................... 53

Associating the Flyout with the AppBar Command ................................................. 55

Importing the Flyout Content .................................................................................. 56

Wiring Up the Controls in a Flyout .......................................................................... 57

Using Pages ........................................................................................... 58

Defi ning the HTML .................................................................................................. 59

Creating the JavaScript Callback ............................................................................ 59

Loading and Displaying the HTML .......................................................................... 61

viii

[www.it-ebooks.info](http://www.it-ebooks.info/)

■ CONTENTS

Loading a Complete HTML Document ................................................... 62

Switching Between Pages ...................................................................................... 65

Displaying External Content .................................................................. 66

Adding the Callback ................................................................................................ 67

Showing the Page ................................................................................................... 68

Checking Manifest Permissions ............................................................................. 70

Summary ............................................................................................... 71

**Chapter 4:**

 **■

**Layouts and Tiles ........................................................ 73**

Dealing with Windows 8 Orientations and Views .................................. 74

Simulating Orientation and View Changes.............................................................. 74

Simulating Views Changes ..................................................................................... 75

Adapting to Orientation and View Changes ............................................................ 77

Programmatically Exiting the Snapped View .......................................................... 83

Using Tiles and Badges ......................................................................... 86

Improving Static Tiles ............................................................................................. 87

Applying the Images ............................................................................................... 88

Updating Tiles ......................................................................................................... 90

Updating Wide Tiles ................................................................................................ 95

Applying Badges .................................................................................... 98

Summary ............................................................................................. 101

**Chapter 5:**

 **■

**Life-Cycle Events ....................................................... 103**

Dealing with the App Life Cycle ........................................................... 103

Correcting the Visual Studio Event Code .............................................................. 104

Simulating Life-Cycle Events Using Visual Studio ............................................... 106

Triggering Life-Cycle Events ................................................................................ 107

Adding an Asynchronous Activity .......................................................................... 108

ix

[www.it-ebooks.info](http://www.it-ebooks.info/)

■ CONTENTS

Implementing the Search Contract ...................................................... 116

Declaring Support for the Contract ....................................................................... 116

Handling the Search ............................................................................................. 117

Implementing the Activated Event Handler .......................................................... 119

Testing the Search Contract ................................................................................. 122

Summary ............................................................................................. 123

**Index .............................................................................................. 125**

x

[www.it-ebooks.info](http://www.it-ebooks.info/)

![](index-10_1.jpg)

**About the Author**

**Adam Freeman** is an experienced IT professional who has held senior positions in a range of companies,

most recently serving as chief technology offi

cer and

chief operating offi

cer of a global bank. Now retired, he

spends his time writing and running.

**His other upcoming publications include:**

**Pro Windows 8 Development in HTML5 and JavaScript**

**Pro ASP.NET MVC 4**

**His other publications include:**

*Th*

*e Defi nitive Guide to HTML5*

 **Pro ASP.NET 4 in C# 2010 4 Edition*

*Applied ASP.NET 4 in Context*

 **Pro ASP.NET 4 in VB 2010 3rd Edition*

*Pro ASP.NET MVC 3 Framework 3rd Edition*

*Pro LINQ*

*Pro jQuery*

 ****Pro .NET 4 Parallel Programming in C#*

*Introducing Visual C# 2010*

 **Visual C# 2010 Recipes .*

xi

[www.it-ebooks.info](http://www.it-ebooks.info/)

**About the Technical**

**Reviewer**

**Fabio Claudio Ferracchiati** is a senior consultant and a senior analyst/developer using Microsoft technologies. He works for Brain Force (www.brainforce.com) in its Italian branch (www.brainforce.it). He is a Microsoft Certifi ed Solution Developer for .NET, a Microsoft Certifi ed Application Developer for .NET, a Microsoft Certifi ed Professional, and a prolifi c author and technical reviewer. Over the past ten years, he’s written articles for Italian and international magazines and coauthored more than ten books on a variety of computer topics.

xiii

[www.it-ebooks.info](http://www.it-ebooks.info/)

**Acknowledgments**

I would like to thank everyone at Apress for working so hard to bring this book to print.

In particular, I would like to thank Jennifer Blackwell for keeping me on track and Ewan Buckingham for commissioning and editing this book. I would also like to thank my technical reviewer, Fabio, whose eff orts made this book far better than it would have been otherwise.

xv

[www.it-ebooks.info](http://www.it-ebooks.info/)

**Introduction**

Windows 8 apps are an important addition to Microsoft Windows 8, providing the cornerstone for a single, consistent programming and interaction model across desktops, tablets, and smartphones. Th

e Windows 8 app user experience is very diff erent from previous generations of Windows applications: Windows 8 apps are full-screen and favor a usability style that is simple, direct, and free from distractions.

Windows 8 app development represents a complete departure from previous versions of Windows. Th

ere are entirely new APIs, new interaction controls, and a very diff erent approach to managing the life cycle of applications.

Windows 8 apps can be developed using a range of languages, including C#, Visual Basic, C++, and, the topic of this book, JavaScript. Windows 8 is the fi rst version of Windows that embraces the skills and knowledge of web application developers and makes JavaScript and HTML fi rst-class citizens in application development.

In this book, I show you how you can build on your knowledge of web app development to create Windows 8 apps using HTML and JavaScript. Th e result is apps

that look and feel like an integral part of the Windows experience and that take advantage of core platform facilities.

Th

is book gives you an essential jump start into the world of Windows 8; by the end, you will understand how to use the controls and features that defi ne the core Windows 8

experience.

About This Book

Th

is book is for experienced HTML and JavaScript developers who want to get a head start creating Windows 8 applications for Windows 8 using the Consumer Preview test release. I explain the concepts and techniques you need to get up to speed quickly and to boost your Windows 8 development techniques and knowledge before the fi nal version of Windows 8 is released.

What Do You Need to Know Before

You Read Th

is Book?

You need to have a good understanding of HTML and JavaScript, ideally from creating rich web apps. You need to understand the DOM API, know how events work, and have a solid grasp of the HTML elements and their DOM object counterparts.

xvii

[www.it-ebooks.info](http://www.it-ebooks.info/)

■ INTRODUCTION

Do You Need to Know About HTML5?

No. You can use some of the HTML5 JavaScript APIs when developing Windows 8 apps, but that is not the focus of this book. A good basic knowledge of HTML4 or HTML5 will be enough, combined with solid JavaScript experience.

What Software Do You Need for Th

is Book?

You will need the Windows 8 Consumer Preview and the Visual Studio 11 Express Beta for Windows 8\. You can download both of them from http://preview.windows.com.

You don’t need any other tools to develop Windows 8 applications or for the examples in this book.

Windows 8 Consumer Preview is not a fi nished product, and it has some stability issues. You’ll get the best experience if you install Windows 8 directly onto a well-specifi ed PC, but you can get by with a virtual machine if you are not ready to make the switch.

What Is the Structure of Th

is Book?

I focus on the key techniques and features that make a Windows 8 app. You already know how to write HTML and use form elements to gather input from the user, and I am not going to waste your time teaching you what you already know. Th is book is about

translating your web app development experience into the Windows 8 world, and that means focusing on what makes a Windows 8 app special.

I have taken a relaxed approach to mixing topics. Aside from the main theme in each chapter, you’ll fi nd some essential context to explain why features are important and why you should implement them. Along the way, I’ll show you the conventions for writing JavaScript Windows 8 apps and introduce as many Windows 8 features as I can.

By the end of this book, you will understand how to build a Windows 8 app that integrates properly into Windows 8 and presents a user experience that is consistent with Windows 8 apps written using other technologies, such as XAML/C#.

Th

is is a primer to get you started on Windows 8 programming for Windows 8\. It isn’t a comprehensive tutorial; as a consequence, I have focused on those topics that are the major building blocks for a Windows 8 app. Th

ere is a lot of information that I just

couldn’t fi t into such a slim volume. If you do want more comprehensive coverage of Windows 8 development, then Apress will be publishing my *Pro Windows 8 Development* *with HTML5 and JavaScript* book for the fi nal release of Windows 8.

Th

e following sections summarize the chapters in this book.

Chapter 1: Getting Started

Aside from introducing this book, I show you how to create the Visual Studio project for the example Windows 8 app that I use throughout this book. I show you how to use the JavaScript tools in Visual Studio, how to test your Windows 8 apps in the Visual Studio simulator, and how to use the debugger.

xviii

[www.it-ebooks.info](http://www.it-ebooks.info/)

■ INTRODUCTION

Chapter 2: Data and Bindings

Data is at the heart of any Windows 8 application, and in this chapter I show you how to defi ne a view model and how to use Windows 8 data bindings to bring that data into your application layouts. Th

ese techniques are essential to building Windows 8 apps that are easy to extend, easy to test, and easy to maintain. Along the way, I’ll show you how to defi ne Windows 8 JavaScript namespaces, create observable arrays, use JavaScript promises, and generate content using templates.

Chapter 3: Application Controls

Certain user interface controls are common to all Windows 8 apps, regardless of which language is used to create them. In this chapter, I show you how to create and confi gure AppBars and Flyouts, which are the two most important of these common controls; together they form the backbone of your interaction with the user. I also show you how to break up your Windows 8 content and code into pieces to make your app easy to manage and show how to bring those pieces together at runtime.

Chapter 4: Layouts and Tiles

Th

e functionality of a Windows 8 application extends to the Windows 8 Start menu, which off ers a number of ways to present the user with additional information. In this chapter, I show you how to create and update dynamic Start tiles and how to apply badges to those tiles.

I also show you how to deal with the Windows 8 *snapped* and *fi lled* layouts, which allow a Windows 8 user to use two Windows 8 apps side by side. You can adapt to these layouts using CSS or JavaScript, and I show you both approaches.

Chapter 5: Life-Cycle Events

Windows applies a very specifi c life-cycle model to Windows 8 apps. In this chapter, I explain how the model works, show you how to receive and respond to critical life-cycle events, and describe how to manage the transitions between suspended and running applications. I demonstrate how to create and manage asynchronous tasks and how to bring them under *control* when your application is suspended. Finally, I show you how to support Windows 8 contracts, which allow your application to seamlessly integrate into the wider Windows 8 experience.

xix

[www.it-ebooks.info](http://www.it-ebooks.info/)

**CHAPTER 1**

**Getting Started**

Windows Store apps are an important addition to Microsoft Windows 8 , providing the cornerstone for a single, consistent programming and interaction model across desktops, tablets, and smartphones. The Windows 8 app user experience is very different from previous generations of Windows desktop applications: Windows Store apps are full-screen and favor a usability style that is simple, direct, and free from distractions.

Windows Store apps represent a complete departure from previous versions of Windows. There are entirely new APIs, new interaction controls, and a very different approach to managing the life cycle of applications.

Windows Store apps can be developed using a range of languages, including C#, Visual Basic, C++, and, the topic of this book, JavaScript. Windows 8 is the first version of Windows that embraces the skills and knowledge of web application developers and makes JavaScript and HTML first-class citizens in application development.

In this book, I show you how you can build on your knowledge of web app development to create Windows Store apps using HTML and JavaScript. The result is apps that look and feel like an integral part of the Windows experience and that take advantage of core platform facilities.

This book gives you an essential jump start into the world of Windows 8; by the end, you will understand how to use the controls and features that define the core Windows 8

experience.

About This Book

This book is for experienced HTML and JavaScript developers who want to get a head start creating apps for Windows 8\. I explain the concepts and techniques you need to get up to speed quickly and to boost your Windows 8 development techniques and knowledge.

What Do You Need to Know Before You Read This Book?

You need to have a good understanding of HTML and JavaScript, ideally from creating rich web apps. You need to understand the DOM API, know how events work, and have a solid grasp of the HTML elements and their DOM object counterparts.

1

[www.it-ebooks.info](http://www.it-ebooks.info/)

CHAPTER 1 ■ GETTING STARTED

Do You Need to Know About HTML5?

No. You can use some of the HTML5 JavaScript APIs when developing Windows Store apps, but that is not the focus of this book. A good basic knowledge of HTML4 or HTML5

will be enough, combined with solid JavaScript experience.

What Software Do You Need for This Book?

You need two things for Windows 8 app development: a PC running Windows 8 and Visual Studio 2012\. If you get serious about Windows 8 development, then you’ll need to buy a copy of Windows 8, but if you are just curious, then you can get a 90-day trial from Microsoft here: http://msdn.microsoft.com/en-us/evalcenter . You’ll find what you need by clicking the Release link in the Windows and Platform Development section.

You are required to create a Microsoft account to get the evaluation, but you’ll need one of those anyway to get set up as a developer. Microsoft accounts are free to create, and there are instructions to follow if you don’t already have one.

**T**

■ **ip** You can run Windows 8 in a virtual machine , but for the best results I recommend installing the operating system directly onto a PC.

Visual Studio 2012 is Microsoft’s development environment. The good news is that Microsoft makes a basic version of Visual Studio, available for free, and that’s the version I’ll be using in this book. It has the catchy name of Visual Studio 2012

Express for Windows 8, and you can download the installer from www.microsoft.com/

visualstudio/11/en-us/products/express . Several flavors are available, each of which can be used to develop a specific kind of application. For Windows 8 development, you will need Visual Studio Express 2012 for Windows 8\. The names of the different flavors are confusingly similar, so be sure to download the right one.

Visual Studio Express 2012 for Windows 8 doesn’t have all of the testing and integration features that some of the paid-for versions of Visual Studio contain, but you don’t need those features to create Windows Store apps. In all other respects, the free version of Visual Studio is fully featured and does everything you will need.

**T**

■ **ip** Don’t worry if you have a paid-for version of Visual Studio 2012\. You won’t need all of the features in your edition, but all of the instructions and examples in this book will work without any problems or modification.

Install Visual Studio as you would any other app. Although the software is free, you will need to activate it, which requires the Microsoft account you created earlier. After you have gone through the process, you’ll end up with a code that you can use to activate Visual Studio. You will also need a developer license, which is free. When you first start 2

[www.it-ebooks.info](http://www.it-ebooks.info/)

CHAPTER 1 ■ GETTING STARTED

Visual Studio 2012, you will be prompted to obtain a license; it takes only a second, and once again, it requires the Microsoft account you created earlier.

What Is the Structure of This Book?

I focus on the key techniques and features that make a Windows 8 app. You already know how to write HTML and use form elements, such as the input element, to gather data from the user, and I am not going to waste your time teaching you what you already know.

This book is about translating your web app development experience into the Windows 8

world, and that means focusing on what makes a Windows 8 app special.

I have taken a relaxed approach to mixing topics. Aside from the main theme in each chapter, you’ll find some essential context to explain why features are important and why you should implement them. Along the way, I’ll show you the conventions for writing JavaScript Windows Store apps and introduce as many Windows 8 features as I can. By the end of this book, you will understand how to build a Windows 8 app that integrates properly into Windows 8 and presents a user experience that is consistent with Windows Store apps written using other technologies, such as XAML/C#.

This is a primer to get you started on programming Windows Store apps. It isn’t a comprehensive tutorial; as a consequence, I have focused on those topics that are the major building blocks for a Windows 8 app. There is a lot of information that I just couldn’t fit into such a slim volume. You can find more comprehensive coverage of Windows 8 development in my *Pro Windows 8 Development with HTML5 and JavaScript* book, also from Apress.

The following sections summarize the chapters in this book.

Chapter 1: Getting Started

Aside from introducing this book, I show you how to create the Visual Studio project for the example Windows 8 app that I use throughout this book. I show you how to use the JavaScript tools in Visual Studio, how to test your Windows Store apps in the Visual Studio simulator, and how to use the debugger.

Chapter 2: Data and Bindings

Data is at the heart of any Windows 8 app, and in this chapter I show you how to define a view model and how to use Windows 8 data bindings to bring that data into your app layouts. These techniques are essential to building Windows Store apps that are easy to extend, easy to test, and easy to maintain. Along the way, I’ll show you how to define Windows 8 JavaScript namespaces, create observable arrays, use JavaScript promises, and generate content using templates.

Chapter 3: Application Controls

Certain user interface controls are common to all Windows Store apps, regardless of which language is used to create them. In this chapter, I show you how to create and 3

[www.it-ebooks.info](http://www.it-ebooks.info/)

CHAPTER 1 ■ GETTING STARTED

configure AppBars and Flyouts, which are the two most important of these common controls; together they form the backbone of your interaction with the user. I also show you how to break up your Windows 8 content and code into pieces to make your app easy to manage and how to bring those pieces together at runtime.

Chapter 4: Layouts and Tiles

The functionality of a Windows 8 app extends to the Windows 8 Start menu, which offers a number of ways to present the user with additional information. In this chapter, I show you how to create and update dynamic Start tiles and how to apply badges to those tiles.

I also show you how to deal with the Windows 8 *snapped* and *filled* layouts, which allow a Windows 8 user to use two Windows Store apps side by side. You can adapt to these layouts using CSS or JavaScript, and I show you both approaches.

Chapter 5: Life-Cycle Events

Windows applies a very specific life-cycle model to Windows Store apps. In this chapter, I explain how the model works, show you how to receive and respond to critical lifecycle events, and describe how to manage the transitions between suspended and running apps. I demonstrate how to create and manage asynchronous tasks and how to bring them under control when your app is suspended. Finally, I show you how to support Windows 8 *contracts* , which allow your app to seamlessly integrate into the wider Windows 8 experience.

Tell Me More About the Example Windows 8 App

The example app for this book is a simple grocery list manager called MetroGrocer.

As an app in its own right, MetroGrocer is pretty simple, but it is a good platform to demonstrate the most important Windows 8 features. You can see how the app looks by the end of this book in Figure 1-1 .

4

[www.it-ebooks.info](http://www.it-ebooks.info/)

![](index-20_1.jpg)

CHAPTER 1 ■ GETTING STARTED

***Figure 1-1 .** The example app*

This is a book about programming and not design. MetroGrocer is not a pretty app, and I don’t even implement all of its features. It is a vehicle for demonstrating coding techniques, pure and simple.

Is There a Lot of Code in This Book?

Yes. In fact, there is so much code that I couldn’t fit it all in without some editing. So, when I introduce a new topic or make a lot of changes, I’ll show you a complete HTML

or JavaScript file. When I make small changes or want to emphasize a few critical lines of code or markup, I’ll show you a code fragment, like the one in Listing 1-1, which is taken from Chapter 5 .

***Listing 1-1.*** A Code Fragment

. . .

if (e.kind == actNS.ActivationKind.search) {

Search.searchAndSelect(e.queryText);

}

. . .

These fragments make it easier for me to pack more code into the book, but they make following along with the examples in isolation by typing them into Visual Studio more difficult. If you do want to follow the examples, then the best way is to download the source code for this book from Apress.com. The code is available for free and includes a complete Visual Studio project for every chapter in the book, which means you’ll always be able to see the big picture.

5

[www.it-ebooks.info](http://www.it-ebooks.info/)

CHAPTER 1 ■ GETTING STARTED

I have focused on introducing new techniques and avoid showing you what you already know. A causality of this approach is CSS style sheets. CSS classes are very repetitive and verbose, and I don’t want to waste time by listing endless reams of styles when I could be showing you something more interesting. You can find all of the CSS in the source code download if you want to make your projects look identical to the example project.

Getting Up and Running

In this section, I will create the project for the example Windows 8 app that I will build up throughout the book. The app is a simple grocery list tracker; it’s a tool that is simple enough to complete in this short book but that has enough features to demonstrate the most important aspects of Windows Store development.

**Note**

■

Microsoft uses the term *Windows Store app* . I can’t bring myself to use this awkward term, so I am just refer to *Windows Store apps* and, often, just *apps* . I’ll leave you to mentally insert the official term as needed.

Creating the Project

To create the example project, start Visual Studio and select New Project, either from the File menu or from the link on the start page. In the New Project dialog, navigate to Installed ➤ Templates ➤ JavaScript. Select the Blank App template, set the name of the project to be MetroGrocer, and click the OK button to create the project, as shown in Figure 1-2 .

**T**

■ **ip** If this is the first time you have used Visual Studio, then you will be prompted to obtain a developer license and perform some other initial configuration steps.

6

[www.it-ebooks.info](http://www.it-ebooks.info/)

![](index-22_1.jpg)

CHAPTER 1 ■ GETTING STARTED

***Figure 1-2 .** Creating the example project* **T**

■ **ip** Visual Studio includes templates preconfigured for some basic project scenarios.

They are not much use, and, to my mind at least, they direct the programmer down a path that doesn’t reflect the strengths of HTML5 and JavaScript. I recommend starting with a blank project and building your app from the ground up, which is the approach I have taken in this book.

The Solution Explorer shows the contents of the project, which you can see in Figure 1-3 . The References folder contains the Microsoft JavaScript and CSS files that are required for Windows 8 development. The default.html file is the page that will be loaded when the app is started, and the css , images , and js folders contain the resources that the app will depend on.

7

[www.it-ebooks.info](http://www.it-ebooks.info/)

![](index-23_1.png)

CHAPTER 1 ■ GETTING STARTED

***Figure 1-3 .** The blank example project as shown by the Solution Explorer* The essential files are default.html , default.css , and default.js . These files define the structure of the layout, the styles applied to it, and the code that manages the data and the interactions with the user. The fact that these files are the same ones you would generally see in web app development reflects the way in which Windows 8 app development embraces web development techniques and tools.

In the sections that follow, I’ll show you each of the most important files in the project, explain what they do, and make some initial changes to create the structure I’ll need later in this book.

Exploring the default.html File

The default.html file is the one that Windows 8 loads when the Windows 8 app is started. You can change the start file by opening the package.appxmanifest file and changing the value for the Start Page setting, but I am going to stick with the default.

(Don’t worry about the rest of the package.appxmanifest file; I’ll come back to that in 8

[www.it-ebooks.info](http://www.it-ebooks.info/)

CHAPTER 1 ■ GETTING STARTED

later chapters.) Windows 8 HTML files are just like regular HTML files, and all of the HTML5 features and support available in Internet Explorer 10 are available for use in your Windows Store apps. Listing 1-2 shows the initial contents of default.html as created by Visual Studio

***Listing 1-2.*** The Initial Contents of the default.html File

<!DOCTYPE html>

<html>

<head>

<meta charset="utf-8" />

<title>MetroGrocer</title>

<!-- WinJS references -->

<link href="//Microsoft.WinJS.1.0/css/ui-dark.css" rel="stylesheet" />

<script src="//Microsoft.WinJS.1.0/js/base.js"></script>

<script src="//Microsoft.WinJS.1.0/js/ui.js"></script>

<!-- MetroGrocer references -->

<link href="/css/default.css" rel="stylesheet" />

<script src="/js/default.js"></script>

</head>

<body>

<p>Content goes here</p>

</body>

</html>

When Visual Studio creates the file, it adds link and script elements to import the core files that are required for a Windows 8 app. The default.js and default.css files are where you put the JavaScript and CSS styles for your project, and I’ll get to those files shortly. You can rename or replace these files as you see fit (as long as you change the link or script elements as well), but I will stick with the defaults to keep things simple.

Visual Studio also adds some link and script elements that have nonstandard URLs, as follows:

<link href="//Microsoft.WinJS.1.0/css/ui-dark.css" rel="stylesheet" />

<script src="//Microsoft.WinJS.1.0/js/base.js"></script>

<script src="//Microsoft.WinJS.1.0/js/ui.js"></script> The base.js and ui.js files contain the JavaScript code for the WinJS API, which you use to create JavaScript Windows Store apps. I’ll introduce some of the most useful parts of WinJS in later chapters.

**THE WORLD OF WINDOWS 8 APIS**

You have access to several different APIs when writing Windows Store apps. There is the Windows API, which is shared across all Windows Store apps, regardless of the language used to write them. There is the WinJS API, which is just for JavaScript Windows Store apps and which acts as a bridge between the capabilities of 9

[www.it-ebooks.info](http://www.it-ebooks.info/)

CHAPTER 1 ■ GETTING STARTED

HTML/JavaScript and Windows. Finally, you have the standard Document Object Model API, which you can use to navigate the HTML markup in your app, register event handlers, and so on. JavaScript is a first-class citizen in the Windows 8 world, and your web app development knowledge will be very useful as you start your development projects.

For the most part, the WinJS API is where you will spend most of your development time, and this is my focus for the first part of this book. The Windows API comes into its own when you want to integrate your app into the wider Windows 8 platform, which I describe in Chapters 4 and 5 .

The ui-dark.css file contains the styles that Windows 8 uses for Windows Store apps, tailored for use with a dark color scheme (meaning white text on a dark background). There is a corresponding file called ui-light.css that you can use if you want to have dark text on a light background instead. The CSS files provide styles for all of the common HTML elements so that they fit into the Windows 8 visual theme and are consistent with Windows Store apps written in other languages, such as C#/XAML. You can customize these styles by overriding them in your app, but for the most part, it is important to retain consistency with other Windows Store apps.

**T**

■ **ip** It is worth opening and reading these files. One of the nice things about developing Windows Store apps with web technologies is that you can read the source code for the WinJS library and the CSS files. You can’t edit the files, but you can see what is going on and, most usefully, set breakpoints in the WinJS code when using the debugger (which I demonstrate later in this chapter).

The body element in the default.html file is created with a simple placeholder element. In Listing 1-3, you can see how I have replaced this element with the content for my app. My additions are shown in bold, and I will use these elements to provide the basic structure for the content layout.

***Listing 1-3.*** The Revised Contents of the default.html File

<!DOCTYPE html>

<html>

<head>

<meta charset="utf-8" />

<title>MetroGrocer</title>

<!-- WinJS references -->

<link href="//Microsoft.WinJS.1.0/css/ui-dark.css" rel="stylesheet" />

<script src="//Microsoft.WinJS.1.0/js/base.js"></script>

<script src="//Microsoft.WinJS.1.0/js/ui.js"></script> 10

[www.it-ebooks.info](http://www.it-ebooks.info/)

CHAPTER 1 ■ GETTING STARTED

<!-- MetroGrocer references -->

<link href="/css/default.css" rel="stylesheet" />

<script src="/js/default.js"></script>

</head>

<body>

<div id="contentGrid">

<div id="leftContainer" class="gridLeft">

<h1 class="win-type-xx-large">Left Container</h1>

</div>

<div id="topRightContainer" class="gridRight">

<h1 class="win-type-xx-large">Top Right Container</h1>

</div>

<div id="bottomRightContainer" class="gridRight">

<h1 class="win-type-xx-large">Bottom Right Container</h1>

</div>

</div>

</body>

</html>

I have added a div element whose id is contentGrid . This will be the container for most of the content in my example app, and it contains three other div elements: leftContainer , topRightContainer , and bottomRightContainer . I’ll add content to these elements as we proceed through the book.

Class names that begin with win-type are defined in ui-dark.css (or ui-light.css if you have switched) and are used to set the size of text in a Windows 8 app. There are a series of styles that relate to gradations in text size from largest to smallest: win-type-xx-large , win-type-x-large , win-type-large , win-type-medium , win-type-small , and win-type-x-small . There are two other win-type styles: win-type-ellipsis uses an ellipsis ( ... ) when text doesn’t fit into its parent element, and win-type-interactive makes an element resemble a link. In default.html , I have used the win-type-xx-large style to create placeholder headers in the layout.

Exploring the default.css File

Listing 1-4 shows the contents of the /css/default.css file. By convention, the css folder is where you put the files that define your CSS styles, and the default.css file is created automatically by Visual Studio, which adds a corresponding link element in the default.html file.

Windows 8 projects rely on standard CSS with some vendor-specific prefixes.

Microsoft used to be terrible for introducing its own CSS properties, but the ones you’ll encounter in this book exist either because the relevant W3C standard is still unfinished or because there are properties that are specific to Windows 8 functionality that need to be expressed to Windows Store apps. You can see examples of both in the listing. The file that Visual Studio creates is very simple, and my additions are shown in bold.

11

[www.it-ebooks.info](http://www.it-ebooks.info/)

CHAPTER 1 ■ GETTING STARTED

***Listing 1-4.*** The Contents of the default.css File body {

background-color: #4A8C43;

}

#contentGrid {

display: -ms-grid;

-ms-grid-rows: 1fr 1fr;

-ms-grid-columns: 60% 60%;

height: 100%;

overflow: scroll;

}

#contentGrid div.gridLeft {

margin-left: 1em;

margin-right: 1em;

}

#contentGrid div.gridRight {

margin-right: 1em;

}

#leftContainer {

-ms-grid-column: 1;

-ms-grid-row: 1;

-ms-grid-row-span: 2;

}

#topRightContainer {

-ms-grid-column: 2;

-ms-grid-row: 1;

}

#bottomRightContainer {

-ms-grid-column: 2;

-ms-grid-row: 2;

}

@media screen and (-ms-view-state: fullscreen-landscape) {

}

@media screen and (-ms-view-state: filled) {

}

12

[www.it-ebooks.info](http://www.it-ebooks.info/)

CHAPTER 1 ■ GETTING STARTED

@media screen and (-ms-view-state: snapped) {

}

@media screen and (-ms-view-state: fullscreen-portrait) {

}

The @media rules work like regular media queries, but the property that they are responding to is specific to Windows 8 and represents different orientations and usage scenarios (which I will explain and demonstrate in Chapter 4 ).

**T**

■ **ip** Visual Studio indents CSS styles to create a visual hierarchy. This drives me crazy for some reason, so I have disabled this feature for all of the listings in this book. You can change the way that Visual Studio displays CSS by selecting Tools ➤ Options, navigating to Text Editor ➤ CSS ➤ Formatting, and unchecking the “Hierarchical indentation” option.

With my additions, I have defined a background color for the app, following the apparent Windows 8 trend toward muted colors. The other additions I have made apply a CSS3 grid layout to the div elements I defined in default.html . You can use any of the new CSS3 layout models in a Windows 8 app (or any CSS layout for that matter), but the specification for the grid layout has yet to be finalized, so I have to prefix my layout properties with –ms .

**A QUICK INTRODUCTION TO CSS3 GRID LAYOUTS**

You may not have used the grid layout because it is not consistently implemented in mainstream web browsers. Fortunately, when developing Windows 8 web apps, you need to worry only about Internet Explorer 10, which is used to display JavaScript Windows Store apps to the user. In this sidebar, I provide you with a very quick introduction to the basic features of CSS3 grid layouts. To get started with a grid layout, you must set the display property and specify the number of rows and columns for the element that will contain the grid, like this:

#contentGrid {

display: -ms-grid;

-ms-grid-rows: 1fr 1fr;

-ms-grid-columns: 60% 60%;

}

The display property must be set to –ms-grid . The –ms-grid-rows and –ms-grid-columns properties specify the dimensions of the grid. These can be specified as fractional units (expressed as fr ), as a percentage of the available space, or as fixed dimensions. I have specified two equal-sized rows and two columns, the width of which is set to be 60 percent of the width of the parent element.

13

[www.it-ebooks.info](http://www.it-ebooks.info/)

CHAPTER 1 ■ GETTING STARTED

It is common in Windows Store apps (or at least common in the ones developed so far) to provide a content area that is wider than the screen and allow the user to scroll from left to right to access different regions of the app. Setting the cumulative width to 120 percent sets up that behavior, which you will be able to see when I run the example web app later in this chapter.

For individual items, you specify which row and column they should appear in, like this:

#leftContainer {

-ms-grid-column: 1;

-ms-grid-row: 1;

-ms-grid-row-span: 2;

}

The –ms-grid-column and –ms-grid-row properties locate an element in the grid.

Both properties are 1-based, meaning that locating an element in column 1 and row 1 will place it in the top-left position in the grid. By default, elements occupy one grid square, but you can change this using the –ms-grid-row-span and –ms-grid-column-span properties. In the example, I have made the leftContainer element span two rows. The only other property of interest is –ms-grid-column-align , which I have not used in my example. This property specifies the alignment of an element within a grid square and can be set to start , end center , or stretch . If you are using a left-to-right language such as English, the start and end values left- and right-justify the element. The center value centers the element, and the stretch value resizes the element so that it completely fills its allocated space.

You can create some very complex layouts using the grid properties. See the full specification at www.w3.org/TR/css3-grid for details, bearing in mind that this is not yet a ratified standard.

Exploring the default.js File

The last of the important files that Visual Studio has created is default.js , which is referenced in default.html using a standard script element. You can see the content of this file in Listing 1-5\. I have not made any changes to this file aside from formatting the code so that it fits on the page.

***Listing 1-5.*** The Content of the default.js File

// For an introduction to the Blank template, see the following documentation:

//

http://go.microsoft.com/fwlink/?LinkId=232509

(function () {

"use strict";

14

[www.it-ebooks.info](http://www.it-ebooks.info/)

CHAPTER 1 ■ GETTING STARTED

WinJS.Binding.optimizeBindingReferences = true;

var app = WinJS.Application;

var activation = Windows.ApplicationModel.Activation; app.onactivated = function (args) {

if (args.detail.kind === activation.ActivationKind.launch) {

if (args.detail.previousExecutionState !==

activation.ApplicationExecutionState.terminated) {

// TODO: This application has been newly launched. Initialize

// your application here.

} else {

// TODO: This application has been reactivated from suspension.

// Restore application state here.

}

args.setPromise(WinJS.UI.processAll());

}

};

app.oncheckpoint = function (args) {

// TODO: This application is about to be suspended. Save any state

// that needs to persist across suspensions here. You might use the

// WinJS.Application.sessionState object, which is automatically

// saved and restored across suspension. If you need to complete an

// asynchronous operation before your application is suspended, call

// args.setPromise().

};

app.start();

})();

The default.js file is where Windows Store apps begin to depart from the standard web app environment and you start to see some of the Windows-specific APIs poke through. Windows Store apps have a life cycle that is more complex than a web app, and the code added to default.js by Visual Studio provides some basic support for handling different application states. It isn’t as bad as it looks, and I’ll explain what you need to know about the Windows 8 app life cycle, and how to respond to it, in Chapter 5 .

**USING YOUR FAVORITE JAVASCRIPT LIBRARIES**

**WITH WINDOWS 8**

By this point, you will have realized that a lot of your experience in web app development is directly transferable to the world of Windows 8 development. You can get a head start on your Windows 8 projects by using your favorite JavaScript libraries. I am a huge fan of jQuery, for example, as anyone who has read my *Pro jQuery* book will know. For the most part, you shouldn’t have any problems 15

[www.it-ebooks.info](http://www.it-ebooks.info/)

![](index-31_1.png)

CHAPTER 1 ■ GETTING STARTED

using well-written libraries as long as you avoid areas where Windows 8 follows a different model than mainstream browsers. So, for example, jQuery works well in Windows Store apps, but be careful when using the ready event. In a Windows 8

app, you need to respond directly to the life-cycle events. Another area to avoid is asynchronous script loading; the JavaScript files for a Windows 8 app are installed locally and are loaded very quickly. There is no need to manage the script loading process, and since I have had some problems in this area with Windows 8, I have found it simpler just to load code using a regular script element.

There are some libraries, however, that it just doesn’t make much sense to use.

Examples include user interface toolkits such as jQuery UI and jQuery Mobile . You can make these work in Windows 8, but you end up with an app that doesn’t follow the distinctive Windows 8 style and that may not respond to touch events in quite the same way as other Windows Store apps.

As a general guide, I recommend you get used to the capabilities of the WinJS API before you start using your favorite JavaScript packages. Microsoft has provided a reasonably solid set of foundation capabilities, including interface controls, data binding, and even a cut-down version of jQuery. These sometimes have flaws, some of which you will see in this book, but I suggest you learn what WinJS has to offer before adding other JavaScript libraries.

Starting and Debugging a JavaScript

Windows 8 App

The best way of testing and debugging a Windows 8 app is using the *simulator* , which is included as part of the Visual Studio installation. In the Visual Studio window, you will see a right arrow next to the words *Local Machine* . Click the small down arrow to the right of the words, and select Simulator from the menu, as shown in Figure 1-4 .

***Figure 1-4 .** Selecting the simulator for a Windows 8 app* 16

[www.it-ebooks.info](http://www.it-ebooks.info/)

![](index-32_1.jpg)

CHAPTER 1 ■ GETTING STARTED

To start the Windows 8 app, click the button, which will now read Simulator. A new window will appear that displays the Windows 8 app, as shown in Figure 1-5 .

***Figure 1-5 .** The Windows 8 simulator* This is the best mechanism for testing Windows Store apps because it allows you to simulate device capabilities that are not natively available on your development machine.

If you explore the buttons on the right edge of the simulator window, you will see options for changing the screen resolution, changing the orientation of the device, and simulating touch interactions and location data.

**Note**

■

You will recall that I set the width of the grid layout to 120 percent of the available space when I added styles to the default.css file earlier in this chapter. You can see the effect of this in the figure. The text for the bottom-right container is clipped, and part of the layout isn’t immediately visible. You can slide the view by touch or by using the mouse.

Reloading the Windows 8 App

One of the nice aspects of using JavaScript to develop Windows Store apps is that you don’t have to stop and restart the app on the simulator to reflect changes you make. To demonstrate this, I have made a couple of simple changes. First, I have changed the text contained in one of the div elements in default.html , as shown in Listing 1-6.

17

[www.it-ebooks.info](http://www.it-ebooks.info/)

![](index-33_1.jpg)

CHAPTER 1 ■ GETTING STARTED

***Listing 1-6.*** Making an HTML Change

. . .

<div id="leftContainer" class="gridLeft">

<h1 class="win-type-xx-large">Left Full Container</h1>

</div>

. . .

I have also made a change to default.css , as shown in Listing 1-7, assigning a different background color to another element.

***Listing 1-7.*** Making a CSS Change

. . .

#topRightContainer {

-ms-grid-column: 2;

-ms-grid-row: 1;

background-color: #808080;

}

. . .

**T**

■ **ip** When I show partial listings like these, only the bold area has changed. The rest of the file remains just as it was in earlier listings. I know some readers prefer that all code listings be shown complete, but I need to pack a lot of information into a slim book, and this is an excellent way of increasing the content density. Don’t forget that you can get the complete source code, without charge, from Apress.com.

Figure 1-6 shows the Visual Studio controls that control the execution of the Windows 8 app in the simulator. The first three pause, stop, and restart execution. These are the traditional debugger controls that Visual Studio has had for as long as I can remember.

***Figure 1-6 .** Controls for restarting and reloading the Windows 8 app* The addition is the reload button, which I have highlighted and which is to the right of the other controls. Clicking this button quickly reloads the content of your Windows 8

app and immediately reflects any changes. You can also reload the project by pressing the F4 key or by selecting Debug ➤ Refresh Windows App. You can see the effect of my HTML

and CSS changes in Figure 1-7 .

18

[www.it-ebooks.info](http://www.it-ebooks.info/)

![](index-34_1.jpg)

CHAPTER 1 ■ GETTING STARTED

***Figure 1-7 .** The Windows 8 simulator now showing the HTML5 and CSS changes* It isn’t just the HTML and CSS that are reloaded. The JavaScript for the app is refreshed as well. This is a nice, quick way of getting an iterative write-and-test development cycle going for Windows Store apps.

**T**

■ **ip** You can’t reload an app if you have added new files, but Visual Studio will warn you when this happens and offer to reload the app instead.

Debugging Windows Store apps

Visual Studio has an excellent debugger, and it can be used very easily to track down problems in JavaScript Windows Store apps. In my own app code, I find it easiest to add the debugger keyword to my code. When the runtime encounters the keyword, the debugger breaks, and I can step through my code, inspect variables, and execute little snippets of code using the JavaScript console.

You can use the debugger keyword only for code you can modify, however, which means that I have to take a different approach when I want to break the debugger on a statement in one of the Microsoft base.js and ui.js JavaScript files. To do this, I have to select the statement I am interested in, right-click, and select Breakpoint ➤ Insert Breakpoint from the pop-up menu. The effect is the same, but no modifications are made to the file.

I recommend exploring the Visual Studio debugger; it is more sophisticated than the developer tools available in browsers, and it is well worth a couple of hours to get to grips with how it works.

19

[www.it-ebooks.info](http://www.it-ebooks.info/)

CHAPTER 1 ■ GETTING STARTED

Summary

In this chapter, I introduced the structure of the book and provided a brief preview of what you will find in the chapters that follow. I also showed you the anatomy of a basic Windows 8 project and created the example project that I will build on for the rest of the book. Finally, I showed you how to use the simulator to run a Windows 8 app and briefly touched on the two ways to cause the Visual Studio debugger to break when running your code. In the next chapter, I’ll start to add some functionality to the example and begin using some of the features of the WinJS API. I start by defining a view model and demonstrating how to bind the data it contains to the Windows 8 app layout; this is an essential technique for building robust apps that are easily to build, scale, and maintain.

20

[www.it-ebooks.info](http://www.it-ebooks.info/)

**CHAPTER 2**

**Data and Bindings**

In this chapter, I show you how to define and use the data that forms the core of a Windows 8 app. To do this, I will be loosely following the *view model* pattern, which allows me to cleanly separate the data from the HTML that is used to present it to the user. This makes apps easier to write, test, and maintain.

You may already be familiar with models and view models from design patterns such as Model-View-Controller (MVC) and Model-View-View Controller (MVVC).

I am not going to get into the details of these patterns in this book. There is a lot of good information about MVC and MVVC available, starting with Wikipedia, which has some very balanced and insightful descriptions.

I find the benefits of using a view model to be enormous and well worth considering for all but the simplest Windows 8 projects, and I recommend you seriously consider following the same path. I am not a pattern zealot, and I firmly believe in taking the parts of patterns and techniques that solve real problems and adapting them to work in specific projects. To that end, you will find that I take a pretty liberal view of how a view model should be used.

This chapter is largely focused on the behind-the-scenes plumbing in a Windows Store app. I start slowly, showing you the conventions for adding JavaScript code to a Windows 8 project and how to use the Windows 8 JavaScript features to reduce global namespace pollution. From there, I define a simple view model and demonstrate different techniques for bringing the data from the view model into the app display. This chapter is all about creating a solid foundation for a Windows Store app and getting to grips with the core Windows 8 JavaScript functionality. Table 2-1 provides the summary for this chapter.

***Table 2-1 .** Chapter Summary*

**Problem**

**Solution**

**Listing**

Create a view model.

Use the

WinJS.Namespace.define

1

method to create a global object

containing your app data.

Display values from

Use the

data-win-bind

attribute to

2, 3

the view model.

create declarative bindings and call

the

WinJS.Binding.processAll

method

to process them.

( *continued*)

21

[www.it-ebooks.info](http://www.it-ebooks.info/)

CHAPTER 2 ■ DATA AND BINDINGS

***Table 2-1.*** ( *continued* )

**Problem**

**Solution**

**Listing**

Create a dynamic binding.

Use the

WinJS.Binding.as

method

4, 6–8

to create an observable data property.

Assign new values to the property to

trigger updates in the HTML.

Create observable data

Ensure that the object with the observable

5

properties in a globally

properties isn’t directly exported by the

available namespace object.

WinJS.Namespace.define

method.

Create observable arrays.

Use the

WinJS.Binding.List

object.

9–11

Generate HTML elements

User the WinJS template feature.

12–17

from observable arrays.

Creating the JavaScript File

The first step is to create a new JavaScript file. Unlike a web app, there is no reason to reduce the number of JavaScript files in a Windows Store app because they are already resident on the user’s computer when the app is started. This means I don’t have to concatenate files or worry about minimizing code to reduce the size of script files.

Instead, I can create separate files for each main app feature.

Add a new file by right-clicking the js folder in the Solution Explorer window and selecting Add ➤ New Item from the menu. Select JavaScript File from the list, set the name to viewmodel.js , and click the Add button to create the file. Visual Studio will add a new empty file to the project and open it for editing. Add the statements shown in Listing 2-1.

***Listing 2-1.*** The Initial Contents of the View Model Class

/// <reference path="//Microsoft.WinJS.1.0/js/base.js" />

/// <reference path="//Microsoft.WinJS.1.0/js/ui.js" /> (function () {

"use strict";

WinJS.Namespace.define("ViewModel.UserData", {

// private members

_shoppingItems: [],

_preferredStores: [],

// public members

homeZipCode: null,

getStores: function () {

return this._preferredStores;

},

22

[www.it-ebooks.info](http://www.it-ebooks.info/)

CHAPTER 2 ■ DATA AND BINDINGS

addStore: function (newStore) {

this._preferredStores.push(newStore);

},

getItems: function () {

return this._shoppingItems;

},

addItem: function (newName, newQuantity, newStore) {

this._shoppingItems.push({

item: newName,

quantity: newQuantity,

store: newStore

});

}

});

ViewModel.UserData.homeZipCode = "NY 10118"; ViewModel.UserData.addStore("Whole Foods"); ViewModel.UserData.addStore("Kroger");

ViewModel.UserData.addStore("Costco");

ViewModel.UserData.addStore("Walmart");

ViewModel.UserData.addItem("Apples", 4, "Whole Foods"); ViewModel.UserData.addItem("Hotdogs", 12, "Costco"); ViewModel.UserData.addItem("Soda", "4 pack", "Costco");

})();

I’ll return to the view model in a moment, but first I need to explain some of the other parts of the code and the conventions they represent. I won’t do this for subsequent JavaScript files, but there is some useful context to put in place as you get started with Windows 8 development.

Using Code Completion

Visual Studio supports JavaScript code completion in the editor, which makes writing code simpler and less error-prone. You must use a reference element to bring JavaScript files into scope for code completion for files that are not in the local directory, like this:

/// <reference path="//Microsoft.WinJS.1.0/js/base.js" />

/// <reference path="//Microsoft.WinJS.1.0/js/ui.js" /> Prefixing the reference element with three slash ( / ) characters brings the reference to the attention of Visual Studio and causes the JavaScript runtime to treat the statement like a comment. With these additions, code-competition support for the WinJS API is 23

[www.it-ebooks.info](http://www.it-ebooks.info/)

CHAPTER 2 ■ DATA AND BINDINGS

added to the viewmodel.js file. You need to add these reference elements to each of your JavaScript files if you want WinJS completion.

Reducing Global Namespace Pollution

One of the biggest problems in JavaScript is variable name collision. JavaScript variables defined outside a function are globally available, and since there are only so many meaningful variable names, it is only a matter of time before two different regions of code try to use the same variable name for different purposes. Global variables are said to be part of the *global namespace* , and defining global variables is often described as *polluting* *the global namespace* . Windows 8 JavaScript files follow three different conventions to reduce namespace pollution, all of which you should adopt.

Creating Namespaces

The WinJS API helps reduce global namespace pollution through the define method of the Namespace object:

WinJS.Namespace.define("ViewModel.UserData", {

//

. . .

*members for the ViewModel.UserData object are defined here*

. . .

});

The first argument to the define method is the global name that you want to assign to your object. In this case, I have specified ViewModel.UserData , which creates a global variable called ViewModel that has a UserData property. The value of the UserData property is the object that I pass as the second argument to the define method, effectively exporting the members of the object so that they are available globally via ViewModel.

UserData . You’ll see how this works when I come to apply the data to markup shortly.

There are a couple of reasons to use the define method, as opposed to handling this yourself. First, the ViewModel object will be created only if it doesn’t already exist.

This means I can easily build up the capabilities of my view model through multiple calls to the define method. The idea is that I can associate complex objects and functions together under a single global namespace object and reduce the likelihood of a variable name collision.

**T**

■ **ip** There is a more sophisticated approach to dealing with this issue, known as the *Asynchronous Module Definition* (AMD) . The AMD technique effectively eliminates global variable name collisions by allowing the consumer of a JavaScript file to pick the name of the variable through which a JavaScript library is accessed. Windows 8 doesn’t support AMD

modules directly, but you can use an AMD-aware script loader such as require.js .

The second reason to use the define method is because it doesn’t export any property that begins with an underscore character, which is a common JavaScript 24

[www.it-ebooks.info](http://www.it-ebooks.info/)

CHAPTER 2 ■ DATA AND BINDINGS

convention for defining private members. This means that when I export my UserData object, the _shoppingItems and _preferredStores properties are not globally available.

This is a nice trick, and it means that the private implementation details of your global objects remain private, but, as you’ll learn, it does cause some mild issues with other WinJS features.

Using Self-executing Functions

The second convention is to use self-executing functions in your JavaScript files. The basic shape of a self-executing function looks like this: (function() {

//

. . . *statements go here* . . .

})();

Wrapping a function in parentheses and then adding another pair of parentheses immediately afterward causes the function to be defined and then executed right away.

Any variables you define inside the function are scoped to the function itself and don’t pollute the global namespace. When the function has been executed, the JavaScript runtime automatically cleans up any variables that have not been exported globally.

Using Strict Mode

The use strict statement applies some constraints on the way you can use JavaScript.

One constraint is that it becomes an error to implicitly create a global variable in a function. You implicitly create a global variable when you don’t use the var keyword: var color1 = "blue"; // OK - scope is local to function color2 = "red"; // Not OK - this is a global variable The JavaScript runtime will generate an error if you define a variable that is implicitly global. Using strict mode is entirely optional, but it is good practice, and it disables some of the more dangerous and odd JavaScript behaviors. You can get full details of the changes that strict mode enforces by reading Appendix C of the ECMAScript Language Specification at

www.ecma-international.org/publications/files/ECMA-ST/Ecma-262.pdf

.

Returning to the View Model

Now that I have explained the context and conventions of a Windows 8 JavaScript file, I can turn to the definition of the view model. The view model for the example Windows 8

app will be simple, and this part of it, represented by the ViewModel.UserData object, will contain the data that is specific to the user: the user’s home zip code, their grocery list, and the stores from which they buy groceries.

I defined two arrays that will hold details of the items on the shopping list and the user’s preferred stores, _shoppingItems and _preferredStores . These properties are not exported as part of the ViewModel.UserData object because they start with an 25

[www.it-ebooks.info](http://www.it-ebooks.info/)

CHAPTER 2 ■ DATA AND BINDINGS

underscore. To provide access to the data, I have defined a set of functions that return the array data and that accept new items to be added to the arrays. The homeZipCode property *is* public and forms part of the globally available ViewModel.UserData object. You can read and change the value of this property directly.

**Note**

■

The shape and structure of the UserData object are a little odd because I want to show different aspects of the WinJS API. In a real project, you would treat the data items in a more consistent manner.

So that there is some data to work with in the app, I have added some statements to the self-executing function to define a zip code, add some stores, and put a few simple items on the shopping list:

ViewModel.UserData.homeZipCode = "NY 10118"; ViewModel.UserData.addStore("Whole Foods"); ViewModel.UserData.addStore("Kroger");

ViewModel.UserData.addStore("Costco");

ViewModel.UserData.addStore("Walmart");

ViewModel.UserData.addItem("Apples", 4, "Whole Foods"); ViewModel.UserData.addItem("Hotdogs", 12, "Costco"); ViewModel.UserData.addItem("Soda", "4 pack", "Costco"); Using Data Binding

*Data binding* is the mechanism by which you include data from your view model in the HTML that is displayed to the user. The WinJS API supports binding through the WinJS.

Binding namespace. Data binding is the key to being able to use a view model in a Windows Store app. I recommend investing time to learn how to use the WinJS binding capabilities fully if you want to build scalable and robust Windows 8 apps.

**T**

■ **ip** WinJS data binding isn’t as complete or flexible as some of the more widely used web app JavaScript libraries such as Knockout.js, Backbone, and Angular.js. You can easily use these libraries in your Windows Store app, but my recommendation is to take the time to understand the WinJS functionality, because it is integrated into some of the UI controls contained in the WinJS API.

26

[www.it-ebooks.info](http://www.it-ebooks.info/)

CHAPTER 2 ■ DATA AND BINDINGS

Using Basic Declarative Bindings

The simplest way to use bindings is *declaratively* , meaning that you include details of the view model data directly in your HTML markup. Listing 2-2 shows how I can bind to the homeZipCode value in default.html.

***Listing 2-2.*** A Simple Declarative Binding

<!DOCTYPE html>

<html>

<head>

<meta charset="utf-8">

<title>MetroGrocer</title>

<!-- WinJS references -->

<link href="//Microsoft.WinJS.1.0/css/ui-dark.css" rel="stylesheet">

<script src="//Microsoft.WinJS.1.0/js/base.js"></script>

<script src="//Microsoft.WinJS.1.0/js/ui.js"></script>

<!-- MetroGrocer references -->

<link href="/css/default.css" rel="stylesheet">

<script src="/js/viewmodel.js"></script>

<script src="/js/default.js"></script>

</head>

<body>

<div id="contentGrid">

<div id="leftContainer" class="gridLeft">

<h1 class="win-type-xx-large">Left Full Container</h1>

<div class="win-type-x-large">

The zip code is:

<span data-win-bind="innerText: UserData.homeZipCode"></span>

</div>

</div>

<div id="topRightContainer" class="gridRight">

<h1 class="win-type-xx-large">Top Right Container</h1>

</div>

<div id="bottomRightContainer" class="gridRight">

<h1 class="win-type-xx-large">Bottom Right Container</h1>

</div>

</div>

</body>

</html>

I have added a script element that adds viewmodel.js to the HTML document. The most important addition, however, is the span element and its data-win-bind attribute.

27

[www.it-ebooks.info](http://www.it-ebooks.info/)

CHAPTER 2 ■ DATA AND BINDINGS

**T**

■ **ip** The order of script elements is important in a Windows Store app, just like it is in a web app. The code in my default.js file will depend on my view model, so I must ensure that the script element for viewmodel.js appears before the one for default.js .

There are two parts to a declarative binding . The first part is the name of a property defined by the HTMLElement object that represents the element in the Document Object Model (DOM). I have used innerText , which will set the text content of the span element.

The property name is followed by a colon ( : ) and then the name of the data item that should be assigned to that property. I have specified UserData.homeZipCode .

**T**

■ **ip** Declarative bindings will silently fail if you specify any property name that is also a reserved JavaScript keyword. This means, for example, that you avoid using the class property in bindings and use the className property instead.

It isn’t enough just to add data-win-binding attributes to HTML elements. I also have to tell the WinJS API to search through the document so that the binding attributes are found and processed. Listing 2-3 shows the default.js file. I removed some of the comments that Visual Studio creates and defined some placeholder functions that I’ll build on later.

***Listing 2-3.*** Processing WinJS Bindings (function () {

"use strict";

var app = WinJS.Application;

app.onactivated = function (eventObject) {

if (eventObject.detail.kind ===

Windows.ApplicationModel.Activation.ActivationKind.launch) {

if (eventObject.detail.previousExecutionState !==

Windows.ApplicationModel.Activation

.ApplicationExecutionState.terminated) {

performInitialSetup(eventObject);

} else {

performRestore(eventObject);

}

WinJS.UI.processAll();

}

};

28

[www.it-ebooks.info](http://www.it-ebooks.info/)

![](index-44_1.jpg)

CHAPTER 2 ■ DATA AND BINDINGS

app.oncheckpoint = function (eventObject) {

performSuspend(eventObject);

};

app.start();

function performInitialSetup(e) {

WinJS.Binding.processAll(document.body, ViewModel);

}

function performRestore(e) {

// TODO

}

function performSuspend(e) {

// TODO

}

})();

The import change is the call to the WinJS.Binding.processAll method. The arguments are the element from which the processing should start and the source of data values for the declarative bindings. By specifying the document.body element, I have told WinJS to process the entire document. The second argument tells WinJS to use the ViewModel object as the data source.

Declarative data bindings are relative to the data source, which is why the binding in my example references UserData.homeZipCode and not ViewModel.UserData.

homeZipCode :

<span data-win-bind="innerText: UserData.homeZipCode"></span> The result of these changes is that the content of the span element is set to the value of the homeZipCode property, as shown in Figure 2-1 .

***Figure 2-1 .** Using a declarative binding to display view model values* Creating Dynamic Bindings

The binding that I used in the previous example is *static* , meaning that there is no ongoing relationship between the value of the view model property and the value of the span element that contains the binding declaration. Static bindings are like a snapshot of a view model value. Once the snapshot has been taken, the value in the markup is fixed, even if the value of the property changes.

*Dynamic bindings* , where property changes *do* result in updated elements, are more useful for most apps. In WinJS, declarative bindings are automatically dynamic when the data property they depend on is *observable* . To create an observable property, I have to use the WinJS.Binding.as method in my view model, as Listing 2-4 shows.

29

[www.it-ebooks.info](http://www.it-ebooks.info/)

CHAPTER 2 ■ DATA AND BINDINGS

***Listing 2-4.*** Creating an Observable Item in the View Model

/// <reference path="//Microsoft.WinJS.1.0/js/base.js" />

/// <reference path="//Microsoft.WinJS.1.0/js/ui.js" /> (function () {

"use strict";

WinJS.Namespace.define("ViewModel", {

UserData: WinJS.Binding.as({

// private members

_shoppingItems: [],

_preferredStores: [],

// public members

homeZipCode: null,

getStores: function () {

return this._preferredStores;

},

addStore: function (newStore) {

this._preferredStores.push(newStore);

},

getItems: function () {

return this._shoppingItems;

},

addItem: function (newName, newQuantity, newStore) {

this._shoppingItems.push({

item: newName,

quantity: newQuantity,

store: newStore

});

}

})

});

ViewModel.UserData.homeZipCode = "NY 10118"; ViewModel.UserData.addStore("Whole Foods"); ViewModel.UserData.addStore("Kroger");

ViewModel.UserData.addStore("Costco");

ViewModel.UserData.addStore("Walmart");

30

[www.it-ebooks.info](http://www.it-ebooks.info/)

CHAPTER 2 ■ DATA AND BINDINGS

ViewModel.UserData.addItem("Apples", 4, "Whole Foods"); ViewModel.UserData.addItem("Hotdogs", 12, "Costco"); ViewModel.UserData.addItem("Soda", "4 pack", "Costco");

})();

The change here is subtle but important. The WinJS.Binding.as takes an object as its argument and returns an object whose *simple properties* are observable. A simple property is one where the value is primitive, such as a number or a string. Properties whose values are objects, arrays, or functions are not simple and are not made observable by the as method.

The WinJS.Binding.as method replaces data properties with getters and setters that trigger notifications when the property value change. Properties that refer to objects, arrays, or functions are left unchanged by the as method. (I explain how to handle arrays later in this chapter.)

You call the as method on *objects* , rather than on properties or simple values. If you call WinJS.Binding.as directly on a property, you will simply get back the property value, and any bindings that use the property won’t automatically update:

// this will NOT update

var myObject = {

myObservableValue: WinJS.Binding.as("MyInitialValue")

};

// this WILL update

var myOtherObject = WinJS.Binding.as({

myObservableValue: "MyInitialValue"

});

No error is reported when you try to create an observable value using the first approach. WinJS just quietly ignores the request, and you get a static binding. Following the second technique will allow you to create bindings that update when the value changes.

Combining Namespaces with Observable Items

There are occasions when the WinJS API creates the impression that different development teams could have coordinated their efforts more carefully. One such example comes when trying to create an observable data item on an object that is exported globally using the WinJS.Namespace.define method.

The WinJS.Binding.as method adds a number of private members to an object when it processes the simple properties, following the common convention of prefixing the names of these members with an underscore. But, as I explained, the WinJS.

Namespace.define method won’t export these members globally. To get around this conflict, I have adjusted the way that I create my ViewModel.UserData object, as Listing 2-5 shows.

31

[www.it-ebooks.info](http://www.it-ebooks.info/)

CHAPTER 2 ■ DATA AND BINDINGS

***Listing 2-5.*** Adjusting the Structure of a Global Object to Export Private Members

. . .

WinJS.Namespace.define("ViewModel", {

UserData: WinJS.Binding.as({

//

*. . . members for UserData object go here. . .*

})

});

. . .

The define method doesn’t remove the private members of properties within the object it exports, which allows me to export the private members of my UserData object by specifying it as a property of the ViewModel object.

Updating an Observable Data Item

Updates from observable data items flow in only one direction, from the view model to the binding. You must use conventional JavaScript DOM API techniques to make data updates flow in the other direction, from the elements to the view model. Listing 2-6

shows the addition of input and button elements to the markup in default.html for updating the homeZipCode property.

***Listing 2-6.*** Capturing Data That Will Be Used to Update the View Model

<!DOCTYPE html>

<html>

<head>

<meta charset="utf-8">

<title>MetroGrocer</title>

<!-- WinJS references -->

<link href="//Microsoft.WinJS.1.0/css/ui-dark.css" rel="stylesheet">

<script src="//Microsoft.WinJS.1.0/js/base.js"></script>

<script src="//Microsoft.WinJS.1.0/js/ui.js"></script>

<!-- MetroGrocer references -->

<link href="/css/default.css" rel="stylesheet">

<script src="/js/viewmodel.js"></script>

<script src="/js/default.js"></script>

</head>

<body>

<div id="contentGrid">

<div id="leftContainer" class="gridLeft">

<h1 class="win-type-xx-large">Left Full Container</h1>

<div class="win-type-x-large">

The zip code is:

<span data-win-bind="innerText: UserData.homeZipCode"></span>

</div>

32

[www.it-ebooks.info](http://www.it-ebooks.info/)

CHAPTER 2 ■ DATA AND BINDINGS

<div class="win-type-x-large">

<label for="newZip">Enter new zip code:</label>

<input id="newZip" data-win-bind="value: UserData.

homeZipCode"/>

<button id="newZipButton">Update Zip Code</button>

</div>

</div>

<div id="topRightContainer" class="gridRight">

<h1 class="win-type-xx-large">Top Right Container</h1>

</div>

<div id="bottomRightContainer" class="gridRight">

<h1 class="win-type-xx-large">Bottom Right Container</h1>

</div>

</div>

</body>

</html>

No Windows 8–specific technique is required to take the value from the input element and update the view model value. Listing 2-7 shows the changes to the default.

js file that respond to the click event from the button element and update the view model using the value property of the input element DOM object.

***Listing 2-7.*** Updating the View Model in Response to the Change Event

. . .

function performInitialSetup(e) {

WinJS.Binding.processAll(document.body, ViewModel);

WinJS.Utilities.query(‘#newZipButton’).listen("click", function (e) {

ViewModel.UserData.homeZipCode =

WinJS.Utilities.query(‘#newZip’)[0].value;

});

}

. . .

I have written this addition to take advantage of the WinJS.Utilities namespace, which contains a subset of the functionality found in utility libraries such as jQuery.

The API is broadly the same as jQuery, but instead of the $ shortcut function, querying elements is done through the WinJS.Utilities.query method. Not all of the functionality of jQuery is available, but you can use the WinJS.Utilities namespace to locate elements, apply CSS styles and classes, and set up handlers for events.

In this listing, I have used the query method to search the HTML document for the newZipButton element and called the listen method on the result to set up a handler for the click event. When the button is clicked, I locate the input element, read the value property from the object, and assign it to the homeZipCode property in the view model.

The result from the query method is an array of elements. There is no equivalent to the jQuery val method, so I treat the response like an array to get the HTMLElement 33

[www.it-ebooks.info](http://www.it-ebooks.info/)

![](index-49_1.jpg)

CHAPTER 2 ■ DATA AND BINDINGS

object that represents the first element that matches my selector and then read the value property. Another way of achieving the same result is to locate elements by using their id attribute values as global variables. This is a feature that is usually avoided in regular web app development because it isn’t implemented consistently across all browsers, but when developing Windows Store apps, you can safely build on features that Internet Explorer10

implements because it will always be the browser engine used to run your app. You can see how I have used this feature to simplify the code for setting up my click event handler function in Listing 2-8\. I don’t have access to the listen method from the WinJS.

Utilities namespace, of course, so I have used the addEventListener method, which is part of the standard DOM API.

***Listing 2-8.*** Locating Elements by Treating Their id Attributes as Global Variables

. . .

function performInitialSetup(e) {

WinJS.Binding.processAll(document.body, ViewModel);

newZipButton.addEventListener("click", function (e) {

ViewModel.UserData.homeZipCode = newZip.value;

});

}

. . .

It took a while before I was happy using Internet Explorer–specific features in my Windows Store apps, but some of them can be real time-saver, and I find myself using this particular feature all the time. One word of caution, though: if there is more than one element in the HTML with the same id attribute value, then the value of the corresponding global variable is an array containing all of the matching elements.

The result of adding the handler function is that the user can enter a new zip code in the input element, and when the button is clicked, the view model zip code value is updated. Since the update is applied to an observable property, the content of the span element is updated automatically to show the new value through its binding, as illustrated by Figure 2-2 .

***Figure 2-2 .** Updating an observable value in the view model* 34

[www.it-ebooks.info](http://www.it-ebooks.info/)

CHAPTER 2 ■ DATA AND BINDINGS

■ **T**

**ip** If you have used a web app view model library such as Knockout.js, you might be used to updating view model values by calling methods, like this: ViewModel.UserData.

homeZipCode(myNewValue) . Knockout uses methods to preserve compatibility with older browsers that don’t support getters and setters, including most previous versions of Internet Explorer. Internet Explorer 10, which is used to display HTML5 Windows Store apps, *does* support getters and setters, which means you can assign values directly, just as I did in the example.

Creating Observable Arrays

Making arrays observable requires a little more effort. To begin with, you must use the WinJS.Binding.List class to create a wrapper around the array you want to work with.

Listing 2-9 shows the List class applied to my viewmodel in the viewmodel.js file.

***Listing 2-9.*** Using the List Class to Create Observable Arrays

/// <reference path="//Microsoft.WinJS.1.0/js/base.js" />

/// <reference path="//Microsoft.WinJS.1.0/js/ui.js" /> (function () {

"use strict";

var shoppingItemsList = new WinJS.Binding.List();

var preferredStoresList = new WinJS.Binding.List();

WinJS.Namespace.define("ViewModel", {

UserData: WinJS.Binding.as({

homeZipCode: null,

getStores: function () {

return preferredStoresList;

},

addStore: function (newStore) {

preferredStoresList.push(newStore);

},

getItems: function () {

return shoppingItemsList;

},

35

[www.it-ebooks.info](http://www.it-ebooks.info/)

CHAPTER 2 ■ DATA AND BINDINGS

addItem: function (newName, newQuantity, newStore) {

shoppingItemsList.push({

item: newName,

quantity: newQuantity,

store: newStore

});

}

})

});

//

*statements to add test data removed for brevity*

 *})();

Creating a List is simple, but you will encounter problems if you try to do so within the scope of an object that has passed to the WinJS.Binding.as method (there is a clash of assumptions over the value of the special this variable). To avoid this problem, define your lists outside of the view model, as I have done in the example.

Using a List object isn’t the same as using an array. The most important difference is that you cannot use array indexers to read or write data values. Instead, you must use the getAt and setAt methods. Other array members, such as push and pop , are supported by List , and there are some useful additions for sorting and projecting the contents of List objects.

Another important difference is that you cannot access array values using declarative bindings. Instead, you must set values in the DOM using JavaScript and handle events emitted by the List object to keep those values up-to-date. Listing 2-10 shows elements in the default.html file that display the number of items in one of the view model lists and some buttons to add and remove items.

***Listing 2-10.*** Elements for Interacting with a List Object

. . .

<div id="leftContainer" class="gridLeft">

<h1 class="win-type-xx-large">Left Full Container</h1>

<div class="win-type-x-large">

The last item is <span id="listInfo"></span>

</div>

<div class="win-type-x-large">

<button id="addItemButton">Add Item</button>

<button id="removeItemButton">Remove Item</button>

</div>

</div>

. . .

There are no special data attributes in these elements, just regular HTML. Everything happens in the default.js file, as shown in Listing 2-11.

36

[www.it-ebooks.info](http://www.it-ebooks.info/)

![](index-52_1.jpg)

CHAPTER 2 ■ DATA AND BINDINGS

***Listing 2-11.*** Using JavaScript to Bridge Between HTML Elements and a WinJS.Binding.List

. . .

function performInitialSetup(e) {

WinJS.Utilities.query(‘button’).listen("click", function (e) {

if (this.id == "addItemButton") {

ViewModel.UserData.addItem("Ice Cream", 1, "Vanilla", "Walmart");

} else {

ViewModel.UserData.getItems().pop();

}

});

var setValue = function () {

var list = ViewModel.UserData.getItems();

listInfo.innerText = list.getAt(list.length - 1).item;

};

var eventTypes = ["itemchanged", "iteminserted",

"itemmoved", "itemremoved"];

eventTypes.forEach(function (type) {

ViewModel.UserData.getItems().addEventListener(type, setValue);

});

setValue();

}

. . .

The List object defines four events that are triggered when the data items change.

These events are itemchanged , iteminserted , itemmoved , and itemremoved . The List object defines an addEventListener method that allows for the registration of event handler functions for these events. In the listing, I register the same handler function for all four events; it updates the innerText property of the span element to display the item property of the first element in the List .

The two button elements add and remove items from the List . I have left the addItem method in the view model because I prefer to have little helper functions like this to make the structure of the data objects more consistent, but I could have directly called the push method on the List object to get the same effect. You can see the result in Figure 2-3 .

***Figure 2-3 .** Displaying details of List objects in HTML elements* 37

[www.it-ebooks.info](http://www.it-ebooks.info/)

CHAPTER 2 ■ DATA AND BINDINGS

**T**

■ **ip** I was able to remove the call to the WinJS.Binding.processAll method from this listing because there are no declarative bindings in the HTML document.

Using Templates

WinJS.Binding.List objects come into their own when used with *binding templates* , which allow for regions of markup to be duplicated for a series of data values. Templates are defined within the HTML document and annotated with the data-win-control attribute, as shown in Listing 2-12.

***Listing 2-12.*** Adding a Template to the HTML Document

<!DOCTYPE html>

<html>

<head>

<meta charset="utf-8">

<title>MetroGrocer</title>

<!-- WinJS references -->

<link href="//Microsoft.WinJS.1.0/css/ui-dark.css" rel="stylesheet">

<script src="//Microsoft.WinJS.1.0/js/base.js"></script>

<script src="//Microsoft.WinJS.1.0/js/ui.js"></script>

<!-- MetroGrocer references -->

<link href="/css/default.css" rel="stylesheet">

<script src="/js/viewmodel.js"></script>

<script src="/js/default.js"></script>

</head>

<body>

<div id="contentGrid">

<div id="leftContainer" class="gridLeft">

<h1 class="win-type-xx-large">Grocery List</h1>

<table id="listTable" class="type-table-header">

<thead>

<tr>

<th>Quantity</th>

<th class="itemName">Item</th>

<th>Store</th>

</tr>

</thead>

<tbody id="itemBody"></tbody>

</table>

</div>

38

[www.it-ebooks.info](http://www.it-ebooks.info/)

CHAPTER 2 ■ DATA AND BINDINGS

<div id="topRightContainer" class="gridRight">

<h1 class="win-type-xx-large">Top Right Container</h1>

</div>

<div id="bottomRightContainer" class="gridRight">

<h1 class="win-type-xx-large">Bottom Right Container</h1>

</div>

</div>

<!-- template for grocery list items -->

<table>

<tbody id="itemTemplate" data-win-control="WinJS.Binding.Template">

<tr class="groceryItem">

<td data-win-bind="innerText: quantity"></td>

<td data-win-bind="innerText: item"></td>

<td data-win-bind="innerText: store"></td>

</tr>

</tbody>

</table>

<!-- end of template for grocery list items -->

</body>

</html>

There are two new table elements in this document. The first one, with the id attribute of listTable , is what the user will see. This table has a header with column titles, but the tbody element is empty. This is where I will use a template to insert a row for each item in the grocery list.

The second table element contains the template. The template itself is defined by the tbody element, but it is an oddity of WinJS templates that they need to be within well-formed HTML. You can’t just define the tbody element as a child of the body , for example, because tbody elements aren’t allowed to be children of body elements. This means you end up with some redundant elements in the HTML document when using templates.

Using the Template

The template is denoted by a data-win-control value of WinJS.Binding.Template . This tells WinJS to process the element, look for declarative bindings, and add some special properties to the HTMLElement object that represents the template element in the DOM.

As mentioned previously, I like to break my projects up so that there JavaScript files for each area of functionality. To that end, I have created a new JavaScript file called ui.js , which is shown in Listing 2-13.

39

[www.it-ebooks.info](http://www.it-ebooks.info/)

CHAPTER 2 ■ DATA AND BINDINGS

***Listing 2-13.*** Using a Template in the ui.js File

/// <reference path="//Microsoft.WinJS.1.0/js/base.js" />

/// <reference path="//Microsoft.WinJS.1.0/js/ui.js" /> (function () {

"use strict";

WinJS.Namespace.define("UI.List", {

displayListItems: function () {

WinJS.Utilities.empty(itemBody);

var list = ViewModel.UserData.getItems();

for (var i = 0; i < list.length; i++) {

itemTemplate.winControl.render(list.getAt(i), itemBody);

}

WinJS.Utilities.children(itemBody).listen("click", function (e) {

ViewModel.State.selectedItemIndex = this.rowIndex - 1; WinJS.Utilities.children(itemBody).removeClass("selected"); WinJS.Utilities.addClass(this, "selected");

});

},

setupListEvents: function () {

var eventTypes = ["itemchanged", "iteminserted",

"itemmoved", "itemremoved"];

var itemsList = ViewModel.UserData.getItems();

eventTypes.forEach(function (type) {

itemsList.addEventListener(type, UI.List.displayListItems);

});

},

});

})();

I have defined a UI namespace, which contains a List object with displayListItems and setupListEvents functions. In the displayListItems function, I locate the HTMLElement objects that represent the template and the target for the generated elements. I have used the document.getElementById method to locate the elements by their id attribute values.

The WinJS.Utilities.empty method removed the children for an element, which I do so that I don’t just add new rows to the table each time the function is called.

To iterate through the items in the WinJS.Binding.List object, call the winControl.

render method on the template object for each one. The winControl is created by the 40

[www.it-ebooks.info](http://www.it-ebooks.info/)

CHAPTER 2 ■ DATA AND BINDINGS

WinJS.UI.processAll method, and it returns the Windows 8–specific properties created for different types of user interface controls.

The render method is added to those elements whose data-win-control attribute is WinJS.Binding.Template . The arguments to the render method are the data item to process and the target element to which the newly generated content will be added, so, by calling the render method for each item in my grocery List object, I am able to create table rows to populate my Windows Store app layout.

Once I have created the table rows, I use the WinJS.Utilities.children method to set up an event listener for the click events on the newly tr elements.

Finally, I set the ViewModel.State.selectedItemIndex property (which I will define shortly) to indicate which row has been selected when the click event is received, using the rowIndex property that is available for tr elements and ensuring that the selected class is applied only to the row the user has selected.

Responding to List Changes

The other function I defined in the UI namespace is setupListEvents . This function listens for each of the list events I described in the previous section and, when they are received, calls the displayListItems function.

This allows me to use my template to render the contents of the table element whenever the contents of the list change. This is a crude way of handling list changes, but it is sufficient for a simple example. The event object passed to the handler function contains details of which list element has changed, which is essential information when implementing a more elegant approach.

■ **Tip** I set up the event handlers in a separate function so that I can call displayListItems repeatedly. If I had set up the event handlers inside the displayListItems function, then a new set of handlers would be created each time the contents of the list changed.

Tracking the Selected Item

In the displayListItems function, I updated the value of the ViewModel.State.

selectedItemIndex property to keep track of which item in the table element had been selected. It is now time to define this property. Listing 2-14 shows the addition to the viewmodel.js file.

***Listing 2-14.*** Defining the selectedItemIndex Property

/// <reference path="//Microsoft.WinJS.1.0/js/base.js" />

/// <reference path="//Microsoft.WinJS.1.0/js/ui.js" /> (function () {

"use strict";

41

[www.it-ebooks.info](http://www.it-ebooks.info/)

CHAPTER 2 ■ DATA AND BINDINGS

var shoppingItemsList = new WinJS.Binding.List();

var preferredStoresList = new WinJS.Binding.List();

WinJS.Namespace.define("ViewModel", {

UserData: WinJS.Binding.as({

homeZipCode: null,

getStores: function () {

return preferredStoresList;

},

addStore: function (newStore) {

preferredStoresList.push(newStore);

},

getItems: function () {

return shoppingItemsList;

},

addItem: function (newName, newQuantity, newStore) {

shoppingItemsList.push({

item: newName,

quantity: newQuantity,

store: newStore

});

}

}),

State: WinJS.Binding.as({

selectedItemIndex: -1

})

});

//

*. . .test data removed for brevity . . .*

})();

I use the State object to differentiate between data required for the current state of the app and data created by the user (which is represented by the UserData object).

Applying the Template to the App

Before you use a template, you must ensure that the WinJS.UI.processAll method has been called. This method processes the HTML document, looking for elements that have the data-win-control attribute and configuring their capabilities. This includes finding and processing templates.

42

[www.it-ebooks.info](http://www.it-ebooks.info/)

CHAPTER 2 ■ DATA AND BINDINGS

Listing 2-15 shows the changes to the performInitialSetup function defined in the default.js file where I added the call to the displayListItems and setupListEvents functions after the call to WinJS.UI.processAll (and where I removed the code for the previous example from the performInitialSetup function).

***Listing 2-15.*** Ensuring That Elements Are Processed Before Using a Template

. . .

function performInitialSetup(e) {

WinJS.UI.processAll().then(function () {

UI.List.displayListItems();

UI.List.setupListEvents();

});

}

. . .

The processAll method does its work in the background, allowing the JavaScript statements that follow a call to processAll to be executed at the same time. Using background tasks is a good idea, but since my displayListItems function relies on the winControl property that is created by processAll , I need to make sure that the background task has been completed before I use my template.

**UNDERSTANDING PROMISES**

Windows Store apps rely on the JavaScript Promise pattern to represent background tasks. The result from the processAll method is a WinJS.Promise object, which is the Windows 8 implementation of the JavaScript Promise pattern.

To use the Promise object, I call the then method on the object, passing in a function that will be executed when the task has completed. In my example, this function makes the calls to the UI.List namespace that depend on the processAll method having completed its work.

The then method takes optional second and third arguments that define functions that will be executed if there is an error and that will receive process information, but for this book I just assume that all background tasks complete properly. I recommend you take the time to handle any errors properly in your own projects.

This is the most basic and common use of a JavaScript promise. Take a look at the API definition for the WinJS.Promise object to learn about the other capabilities available.

Styling the Template Elements

To style the elements that are generated by the template, I have added a new file called list.css in the css folder. You can see the contents of this file in Listing 2-16\. This 43

[www.it-ebooks.info](http://www.it-ebooks.info/)

CHAPTER 2 ■ DATA AND BINDINGS

file contains standard CSS and doesn’t use any features specific to Windows Store app development.

***Listing 2-16.*** The Contents of the /css/list.css File

#listTable {margin-top: 30px;border-collapse: collapse;}

#listTable th, #listTable td {padding-right: 20px;padding-left: 10px;}

#listTable th {font-size: 30pt; text-align: left;}

#listTable td {font-size: 26pt;padding-top: 5px;padding-bottom: 5px;}

#listTable tbody tr.selected {background-color: #85C54C;}

#listTable th.itemName, #listTable th.store {width: 40%;}

Importing the JavaScript and CSS

The final step in applying my template is to add my /js/ui.js and /css/list.css files to default.html , as shown in Listing 2-17.

***Listing 2-17.*** Adding the ui.js File to default.html

. . .

<head>

<meta charset="utf-8">

<title>MetroGrocer</title>

<!-- WinJS references -->

<link href="//Microsoft.WinJS.1.0/css/ui-dark.css" rel="stylesheet">

<script src="//Microsoft.WinJS.1.0/js/base.js"></script>

<script src="//Microsoft.WinJS.1.0/js/ui.js"></script>

<!-- MetroGrocer references -->

<link href="/css/default.css" rel="stylesheet">

<link href="/css/list.css" rel="stylesheet">

<script src="/js/viewmodel.js"></script>

<script src="/js/ui.js"></script>

<script src="/js/default.js"></script>

</head>

. . .

Testing the Template

You can see the result of using the template to populate the table in Figure 2-4 . Notice that you can’t see the template itself, even though it is defined in the default.html file. This is because the WinJS.UI.processAll method sets the CSS display property to none so that the template isn’t visible to the user.

44

[www.it-ebooks.info](http://www.it-ebooks.info/)

![](index-60_1.jpg)

CHAPTER 2 ■ DATA AND BINDINGS

***Figure 2-4 .** Generating table rows using a template and a List object* Summary

In this chapter, I showed you how to create a view model in a Windows 8 app, how to make it available globally, and how to make data items observable. The current state of the WinJS API makes this a somewhat awkward process, but the reward is the ability to use declarative bindings and templates to populate your HTML with app data.

45

[www.it-ebooks.info](http://www.it-ebooks.info/)

**CHAPTER 3**

**Application Controls**

For the most part, the user interface controls you use in a Windows 8 app are the same as the ones you use when creating a regular HTML web app. If you want to gather data from the user, then you can use elements such as select , input , and textarea . If you want to allow the user to commence an action, then you can use button elements, and so on. The appearance of these elements in your app is controlled by a combination of the CSS in your project and the capabilities of Internet Explorer 10.

In addition to these basic controls, the WinJS API defines some that are specific to Windows 8\. In this chapter, I show you how to use the most important of these controls, focusing on those that are essential to implementing the Windows 8 app experience, both for the user and for the developer.

I begin by showing you the AppBar and Flyout controls. The AppBar is a core feature of all Windows 8 apps and is the mechanism by which users perform interactions that are not directly possible using the data and controls in the main application layout. Flyouts are a way of providing consistent pop-ups to users and are closely associated with AppBars.

Not all WinJS controls are for the benefit of the user. In this chapter, I show you how to use the HTMLControl to import fragments of HTML into your application. The HTMLControl is useful only for static HTML content, so I also show you how to use the *Pages* feature to load HTML and its associated JavaScript and CSS. These techniques allow for the effective partitioning of application content and functionality, which makes for an easier development experience and simpler testing and maintenance. For completeness, I finish this chapter showing you how to import external content into your Windows 8 app, which can be useful if you have invested in an existing content infrastructure and want to incorporate it into your application. Table 3-1 provides the summary for this chapter.

***Table 3-1.** Chapter Summary*

**Problem**

**Solution**

**Listing**

Denote an AppBar.

Define a div element with a data-win-control

1, 3, 4

attribute of WinJS.UI.AppBar .

Import a fragment of

Use the HTMLControl feature.

2

static content.

Denote a flyout.

Define a div element with a data-win-control

5–7

attribute of WinJS.UI.Flyout .

( *continued*)

47

[www.it-ebooks.info](http://www.it-ebooks.info/)

CHAPTER 3 ■ APPLICATION CONTROLS

***Table 3-1.*** ( *continued* )

**Problem**

**Solution**

**Listing**

Associate a flyout with an Configure the button element so that the type 8

AppBar button.

property is flyout and the flyout property is

set to the id of the flyout div element.

Manage a flyout.

Use the DOM API to manage the controls in

9, 10

the flyout and the members of the flyout div

element’s winControl property to manage the

flyout functionality.

Load content into a

Create fragments of HTML and use the WinJS.

11–15

Windows 8 app.

UI.Pages.define method to register a callback

function that will be executed when the content

is loaded. Use the WinJS.UI.Pages.render

method to load the content.

Load HTML, JavaScript,

Create an HTML document and use script and

16–19

and CSS into a Windows

link elements to reference the JavaScript and

8 app together.

CSS files. Use the WinJS.UI.Pages.define

and WinJS.UI.Pages.render methods to register

a callback function and load the document.

Display content that is

Use an iframe and ensure that the Internet

20–22

external to a Windows 8

(Client) permission is declared in the app’s

app.

manifest.

Adding an AppBar

The *AppBar* appears at the bottom of the screen when the user makes an upward swiping gesture or right-clicks with the mouse. The emphasis in the Windows 8 app UI seems to be to have as few controls as is possible on the main layout and rely on the AppBar as the mechanism for any interaction that is not about the immediately available functionality.

I could define the HTML for AppBars directly in default.html , but as I mentioned at the start of this chapter, WinJS supports dynamically loading and inserting fragments of HTML. To demonstrate this feature, I have created an html folder in the Solution Explorer and created an appbar.html file using the HTML Page item in the Add New Item dialog.

The contents of appbar.html , which contains the definition of my AppBar, are shown in Listing 3-1.

**T**

■ **ip** Ensure that you are not running the debugger when you create the html folder. If you are, Visual Studio will create a folder called NewFolder but won’t let you change the name until the debugger has stopped.

48

[www.it-ebooks.info](http://www.it-ebooks.info/)

CHAPTER 3 ■ APPLICATION CONTROLS

***Listing 3-1.*** Defining an AppBar in the appbar.html File

<div id="appBar" data-win-control="WinJS.UI.AppBar">

<button data-win-control="WinJS.UI.AppBarCommand"

data-win-options="{section: 'global', label: 'New Item', icon:

'add'}">

</button>

<button data-win-control="WinJS.UI.AppBarCommand"

data-win-options="{section: 'global', label: 'Stores', icon: 'shop'}">

</button>

<button data-win-control="WinJS.UI.AppBarCommand"

data-win-options="{section: 'global', label: 'Zip Code', icon:

'home'}">

</button>

<button data-win-control="WinJS.UI.AppBarCommand"

data-win-options="{id: 'done', disabled: true,

section: 'selection', label: 'Done', icon: 'accept'}">

</button>

</div>

The AppBar is denoted by a div element that has the data-win-control set to WinJS.AppBar . An AppBar contains one or more button elements whose data-win-control attribute is set to WinJS.UI.AppBarCommand . Each button requires configuration information, and this is provided through the data-win-options attribute, following the format of a simple JavaScript object. I’ll explain the meaning of the configuration properties shortly.

**T**

■ **ip** You can also specify AppBar buttons using a series of nested HTML elements, which obviates the need for the JavaScript-like configuration object. I don’t like having to embed fragments of code into my markup, but it is something that pervades WinJS, so I tend to stick with the approach shown in the listing. It is ugly, but it is consistent with the way other controls work.

Listing 3-2 shows how I import the HTML fragment defined in appbar.html into the default.html document.

***Listing 3-2.*** Importing an HTML Fragment into default.html

<!DOCTYPE html>

<html>

<head>

<meta charset="utf-8">

<title>MetroGrocer</title>

49

[www.it-ebooks.info](http://www.it-ebooks.info/)

CHAPTER 3 ■ APPLICATION CONTROLS

<!-- WinJS references -->

<link href="//Microsoft.WinJS.1.0/css/ui-dark.css" rel="stylesheet">

<script src="//Microsoft.WinJS.1.0/js/base.js"></script>

<script src="//Microsoft.WinJS.1.0/js/ui.js"></script>

<!-- MetroGrocer references -->

<link href="/css/list.css" rel="stylesheet">

<link href="/css/default.css" rel="stylesheet">

<script src="/js/viewmodel.js"></script>

<script src="/js/ui.js"></script>

<script src="/js/default.js"></script>

</head>

<body>

<div id="contentGrid">

<div id="leftContainer" class="gridLeft">

<h1 class="win-type-xx-large">Grocery List</h1>

<table id="listTable" class="type-table-header">

<thead>

<tr>

<th>Quantity</th>

<th class="itemName">Item</th>

<th class="store">Store</th>

</tr>

</thead>

<tbody id="itemBody"></tbody>

</table>

</div>

<div id="topRightContainer" class="gridRight">

<h1 class="win-type-xx-large">Top Right Container</h1>

</div>

<div id="bottomRightContainer" class="gridRight">

<h1 class="win-type-xx-large">Bottom Right Container</h1>

</div>

</div>

<!-- import HTML fragments -->

<div data-win-control="WinJS.UI.HtmlControl"

data-win-options="{uri: '/html/appbar.html'}"></div>

<!-- end of HTML fragments -->

<!-- template for grocery list items -->

<table>

<tbody id="itemTemplate" data-win-control="WinJS.Binding.Template">

<tr class="groceryItem">

<td data-win-bind="innerText: quantity"></td>

<td data-win-bind="innerText: item"></td>

<td data-win-bind="innerText: store"></td>

</tr>

50

[www.it-ebooks.info](http://www.it-ebooks.info/)

![](index-65_1.jpg)

CHAPTER 3 ■ APPLICATION CONTROLS

</tbody>

</table>

<!-- end of template for grocery list items -->

</body>

</html>

When you call the WinJS.UI.processAll method, WinJS finds all div elements whose data-win-control attribute is set to WinJS.UI.HtmlControl and sets their content to the HTML fragment specified in the data-win-options attribute. You can’t specify the fragment file directly; instead, you have to use the JSON-like format I have shown in the listing, specifying the file name as the value for the uri property.

**T**

■ **ip**

The

HTMLControl is only for loading fragments of content that don’t need to execute script or define any CSS. This works in my example because the JavaScript that sets up the AppBar from the HTML fragment is part of default.js , which is already associated with the main HTML document. And, as I describe in a moment, I have to take precautions to make sure that the HTML fragment is loaded before performing the AppBar setup. Later in this chapter, I show you WinJS *pages* , which support their own CSS and JavaScript.

The contents of the loaded file are processed automatically, and WinJS finds and configures my AppBar. You don’t have to worry about making the AppBar appear and disappear. This is configured by WinJS, and the AppBar will appear when the user right-clicks or swipes up from the bottom of the screen. You can see how the AppBar appears in Figure 3-1 .

***Figure 3-1 .** Adding an AppBar to the example application* I have magnified a couple of the buttons in the figure to make them easier to see the effect of the configuration properties applied to each AppBar button. The id and disabled properties set the corresponding attributes on the button element, and the label property sets the text displayed underneath the button.

There are two regions on an AppBar, and the section property specifies which one a button appears in. If the section property is set to global , then the button will be toward 51

[www.it-ebooks.info](http://www.it-ebooks.info/)

CHAPTER 3 ■ APPLICATION CONTROLS

the right side of the AppBar. This area is for actions that affect the entire application. A button whose section is selection performs actions that apply to the currently selected item and are displayed on the left of the AppBar.

The icon property sets the button image. You can specify a custom PNG file for this property or use one of the symbol characters defined in the Segoe UI Symbol font.

You can refer to these icons either by specifying one of the values from the WinJS.

UI.AppBarIcon enumeration or by specifying its character code directly (which you can get using the Windows 8 Character Map tool). As an example, I specified the add icon for one of the buttons, which corresponds to the WinJS.UI.AppBarIcon.add value or the character code \uE109 .

**T**

■ **ip** There are a lot of icons to choose from, far more than the API documentation suggests. Open the js/ui.js file in the References section and search for *icon* to see the list defined by the enumeration. The enumeration just contains common icons; there are even more defined by the font itself.

Implementing App Bar Buttons

In this section, I will add the code to implement the selection-specific Done button I added to the AppBar. To do this, I have defined a UI.AppBar namespace in the ui.js file and created the setupButtons function, which you can see in Listing 3-3.

***Listing 3-3.*** Setting Up Support for the AppBar Buttons

/// <reference path="//Microsoft.WinJS.1.0/js/base.js" />

/// <reference path="//Microsoft.WinJS.1.0/js/ui.js" /> (function () {

"use strict";

WinJS.Namespace.define("UI.AppBar", {

setupButtons: function () {

ViewModel.State.bind("selectedItemIndex",

function (newValue, oldValue) {

done.disabled = (newValue == −1);

});

done.addEventListener("click", function (e) {

var selectedIndex = ViewModel.State.selectedItemIndex; ViewModel.UserData.getItems().splice(selectedIndex, 1); ViewModel.State.selectedItemIndex = −1;

});

}

});

52

[www.it-ebooks.info](http://www.it-ebooks.info/)

CHAPTER 3 ■ APPLICATION CONTROLS

WinJS.Namespace.define("UI.List", {

//

. . .code removed for brevity. . .

});

})();

Since AppBar buttons are created from HTML button elements, I can use the standard disabled property to control the button state and handle the click event to respond to user interaction.

I want to control the state of the button in response to changes to the ViewModel.

State.selectedItemIndex property in the view model. To monitor an object with observable properties, you use the bind method to register a function that will be executed when the value the property changes. In this listing, I have created a binding for the selectedItemIndex property so that I can change the status of the button when the user makes a selection. This is a nice demonstration of how you can use WinJS data bindings to tie together view model data, event handler functions, and HTML elements.

The last step is to call the setupButtons function from within performInitialSetup in the default.js file, as shown in Listing 3-4. Notice that, once again, I make the call to my function within the then callback provided by the Promise object so that I can be sure my HTML fragment has been loaded and processed before I start performing operations on the elements it contains.

***Listing 3-4.*** Calling the setupButtons Function

. . .

function performInitialSetup(e) {

WinJS.UI.processAll().then(function () {

UI.List.displayListItems();

UI.List.setupListEvents();

UI.AppBar.setupButtons();

});

}

. . .

And, with these additions, I have a basic AppBar in place. If you select an item in the grocery list, the Done button will be enabled, allowing you to reveal the AppBar and mark the item as completed, removing it from the list. I am not going to implement functionality for all of the AppBar buttons, but in the following section I’ll wire up the Add button so that I can demonstrate how to create and use a Flyout.

Adding Flyouts

Flyouts are pop-up windows that you can use to provide information to, or gather data from, the user. Flyouts are often used in conjunction with AppBar buttons, and in this section I show you how to use a Flyout to complete the Add Item AppBar button.

To begin, I have created a new file in the html folder called addItemFlyout.html , the contents of which are shown in Listing 3-5.

53

[www.it-ebooks.info](http://www.it-ebooks.info/)

CHAPTER 3 ■ APPLICATION CONTROLS

***Listing 3-5.*** Defining a Flyout

<div id="addItemFlyout" class="flyout" data-win-control="WinJS.UI.Flyout">

<div>

<label for="groceryItem">Item:</label>

<input id="groceryItem" placeholder="e.g. Apples">

</div>

<div>

<label for="quantity">Quantity:</label>

<input id="quantity" placeholder="e.g. 4"/>

</div>

<div>

<label for="stores">Store:</label>

<select id="stores"></select>

</div>

<div class="rightAlign">

<button id="addItemButton">Add</button>

</div>

</div>

**T**

■ **ip** If you are used to developing web apps, you will be accustomed to building data-gathering interactions around the HTML form element. In a Windows 8 app, the form element isn’t that important because the majority of interactions are handled entirely within the client, even if the data is being sent to a remote server as part of an Ajax request.

Flyouts are denoted by a div element whose data-win-control attribute is set to WinJS.UI.Flyout . This is the only limitation to creating a flyout, and I am free to add any content inside the div element that I need to support my interaction with the user. In this example, I have used some standard HTML form controls to gather details of the new item from the user. To style the elements in the flyout, I have added a file called flyout.

css to the css folder in the Visual Studio project. You can see the contents of this file in Listing 3-6.

***Listing 3-6.*** The Contents of the /css/flyouts.css File

#addItemFlyout { background-color: #85C54C; font-weight: bold;}

div.flyout input { margin-bottom: 5px; width: calc(100% - 5px); }

div.flyout select { margin-top: 0px; width: 100% }

div.rightAlign { margin-top: 10px; float: right;}

I have added a link element to the head section of the default.html file to bring the flyout.css file into scope, as shown in Listing 3-7.

54

[www.it-ebooks.info](http://www.it-ebooks.info/)

CHAPTER 3 ■ APPLICATION CONTROLS

***Listing 3-7.*** Bringing the flyout.css File into the default.html File

. . .

<head>

<meta charset="utf-8">

<title>MetroGrocer</title>

<!-- WinJS references -->

<link href="//Microsoft.WinJS.1.0/css/ui-dark.css" rel="stylesheet">

<script src="//Microsoft.WinJS.1.0/js/base.js"></script>

<script src="//Microsoft.WinJS.1.0/js/ui.js"></script>

<!-- MetroGrocer references -->

<link href="/css/list.css" rel="stylesheet">

<link href="/css/flyout.css" rel="stylesheet">

<link href="/css/default.css" rel="stylesheet">

<script src="/js/viewmodel.js"></script>

<script src="/js/ui.js"></script>

<script src="/js/default.js"></script>

</head>

. . .

Associating the Flyout with the AppBar Command

Having defined the flyout, I can now associate it with my AppBar, which I do by adding configuration properties to the button element for New Item in the appbar.html file, as shown in Listing 3-8.

***Listing 3-8.*** Associating a Flyout with an AppBar Button

<div id="appBar" data-win-control="WinJS.UI.AppBar">

<button data-win-control="WinJS.UI.AppBarCommand"

data-win-options="{section: 'global', label: 'New Item', icon: 'add', type:'flyout', flyout: 'addItemFlyout'}">

</button>

<button data-win-control="WinJS.UI.AppBarCommand"

data-win-options="{section: 'global', label: 'Stores', icon: 'shop'}">

</button>

<button data-win-control="WinJS.UI.AppBarCommand"

data-win-options="{section: 'global', label: 'Zip Code', icon: 'home'}">

</button>

<button data-win-control="WinJS.UI.AppBarCommand"

data-win-options="{id: 'done', disabled: true,

section: 'selection', label: 'Done', icon: 'accept'}">

</button>

</div>

55

[www.it-ebooks.info](http://www.it-ebooks.info/)

![](index-70_1.jpg)

CHAPTER 3 ■ APPLICATION CONTROLS

**T**

■ **ip** In addition to the flyout value, you can also set the type property to toggle to create an AppBar button that the user can switch on or off. A value of button is equivalent to not setting a type property at all, and AppBar buttons work just like regular HTML buttons (as demonstrated earlier in this chapter).

To specify the flyout associated with the button , I set the type property to flyout and the flyout property to the id of the flyout element.

Importing the Flyout Content

To bring my flyout HTML fragment into the application, I have to update default.html and make use of HTMLControl again:

. . .

<!-- import HTML fragments -->

<div data-win-control="WinJS.UI.HtmlControl"

data-win-options="{uri: '/html/appbar.html'}"></div>

<div data-win-control="WinJS.UI.HtmlControl"

data-win-options="{uri: '/html/addItemFlyout.html'}"></div>

<!-- end of HTML fragments -->

. . .

When the user clicks the button , my flyout is automatically displayed, as shown in Figure 3-2 .

***Figure 3-2 .** Associating a Flyout with an AppBar button* 56

[www.it-ebooks.info](http://www.it-ebooks.info/)

CHAPTER 3 ■ APPLICATION CONTROLS

Wiring Up the Controls in a Flyout

Clicking the New Item AppBar command button shows the flyout, but the elements it contains are not yet wired up to the rest of the app. To complete the flyout, I need to populate the select element with details of the stores in the view model and handle the click event for the Add button so that the new item is added to the list.

Listing 3-9 shows the additions to the UI namespace in the ui.js file to handle both of these tasks.

***Listing 3-9.*** Adding Support for the Flyout Controls to the UI Namespace

/// <reference path="//Microsoft.WinJS.1.0/js/base.js" />

/// <reference path="//Microsoft.WinJS.1.0/js/ui.js" /> (function () {

"use strict";

WinJS.Namespace.define("UI.Flyouts", {

setupAddItemFlyout: function () {

WinJS.Utilities.empty(stores);

var list = ViewModel.UserData.getStores();

list.forEach(function (item) {

var newOption = document.createElement("option"); newOption.text = item;

stores.add(newOption);

});

addItemButton.addEventListener("click", function () {

ViewModel.UserData.addItem(groceryItem.value, quantity.value, stores.value);

addItemFlyout.winControl.hide();

appBar.winControl.hide();

});

}

});

//

. . . *other namespace definitions omitted for brevity*

})();

I have created a UI.Flyouts object that defines the setupAddItemFlyout function.

This function populates the select control from the view model, and when the Add button is clicked, it reads the values from the input and select elements and uses them to create a item in the grocery list.

When I am done with the flyout, I locate the div element and use the winControl property to get the WinJS members that are specific to flyouts. The hide method dismisses the Flyout and returns the user to the main layout. I perform the same task 57

[www.it-ebooks.info](http://www.it-ebooks.info/)

![](index-72_1.jpg)

CHAPTER 3 ■ APPLICATION CONTROLS

on the AppBar element, which would otherwise be left visible when the Flyout is closed.

See the WinJS API documentation for details of the other methods available through the winControl property of AppBar and Flyout elements.

Of course, defining this code isn’t enough; I have to call the function from the default.js file, as shown in Listing 3-10.

***Listing 3-10.*** Calling the Code to Set Up the Flyout Controls

. . .

function performInitialSetup(e) {

WinJS.UI.processAll().then(function () {

UI.List.displayListItems();

UI.List.setupListEvents();

UI.AppBar.setupButtons();

UI.Flyouts.setupAddItemFlyout();

});

}

. . .

The effect of these additions is to provide the user with a drop-down list of stores to pick from, and when the user clicks the Add button, the details they have added will be used to create a new data item in the view model. You can see the effect of the populated select element in Figure 3-3 .

***Figure 3-3 .** Populating the select element in the Flyout* Using Pages

Earlier in the chapter, I used HTMLControl to import fragments into my main HTML

document. The main limitation in using HTMLControl is that you can’t arrange to be notified when your content is loaded. This is fine for content when you are using 58

[www.it-ebooks.info](http://www.it-ebooks.info/)

CHAPTER 3 ■ APPLICATION CONTROLS

fragments just to reuse regions of markup, but the HTMLControl isn’t much help if you want to load content fragments dynamically in response to user input.

HTMLControl is a simple declarative wrapper around a more complex WinJS feature called *pages* . Pages must be set up and managed in JavaScript, but they provide a richer set of functions and, crucially, support callbacks that can be used to integrate content at any point in the application’s life cycle.

To demonstrate the pages feature, I am going to implement the top-right region of the layout, allowing the user to edit the contents of the currently selected item and display a useful message when no selection has been made.

I need do to three things to use a page. The first is to define the HTML, the second is to write the JavaScript that will be executed when the HTML is loaded, and the third thing is to load and display the HTML as part of the application.

Defining the HTML

For the first step, I have created a file called noSelection.html in the html folder of the project. Listing 3-11 shows the contents of this file. This is the markup that will be displayed to the user when no grocery list item has been selected.

***Listing 3-11.*** The noSelection.html File

<div id="noselectionContainer" class="win-type-x-large">

<p>There are <span id="numberCount"></span> items on your list</p>

<p>Select to edit</p>

</div>

This is the complete content of the HTML file; it contains just the elements I want to insert into the document, which is the same approach when I used the HTMLControl feature previously. I’ll show you how to use complete HTML documents shortly, but I wanted to emphasize that the pages feature will quite happily operate on fragments of markup, which is how I tend to break up my applications.

Creating the JavaScript Callback

The second step is to define the code that will be executed when the HTML is loaded.

Remember, this is the main benefit of using the pages feature; I can rely on the code I specify being executed every time I display the fragment, allowing me to configure the elements to match the present state of the app. Listing 3-12 shows the contents of the pages.js file, which I created in the js folder.

***Listing 3-12.*** Defining the Code to Be Executed When a Page Fragment Is Loaded

/// <reference path="//Microsoft.WinJS.1.0/js/base.js" />

/// <reference path="//Microsoft.WinJS.1.0/js/ui.js" /> (function () {

"use strict";

59

[www.it-ebooks.info](http://www.it-ebooks.info/)

CHAPTER 3 ■ APPLICATION CONTROLS

WinJS.UI.Pages.define("/html/noselection.html", {

ready: function (targetElement) {

numberCount.innerText = ViewModel.UserData.getItems().length;

}

});

})();

The callback function is set up through the WinJS.UI.Pages.define method.

The arguments are the URL of the HTML file that will be loaded and an object whose properties define the functions that will be executed when this happens. Several property names are supported, but the one that is most useful is ready , and the function assigned to this property will be executed when the HTML has been loaded and processed by the Windows 8 runtime.

In this example, my ready callback function locates the span element whose id attribute value is numberCount in the noSelection.html markup and sets its content to be the number of items on the grocery list. I have added a script element to the default.html file to load pages.js :

. . .

<head>

<meta charset="utf-8">

<title>MetroGrocer</title>

<!-- WinJS references -->

<link href="//Microsoft.WinJS.1.0/css/ui-dark.css" rel="stylesheet">

<script src="//Microsoft.WinJS.1.0/js/base.js"></script>

<script src="//Microsoft.WinJS.1.0/js/ui.js"></script>

<!-- MetroGrocer references -->

<link href="/css/list.css" rel="stylesheet">

<link href="/css/flyout.css" rel="stylesheet">

<link href="/css/default.css" rel="stylesheet">

<script src="/js/viewmodel.js"></script>

<script src="/js/ui.js"></script>

<script src="/js/pages.js"></script>

<script src="/js/default.js"></script>

</head>

. . .

**T**

■ **ip** I have shown the definition of the HTML and the JavaScript for this feature in separate files because it lets me build up the JavaScript functionality in one place. A popular alternative is to put the code that called the WinJS.UI.Pages.define method in a script element contained in the same file as the HTML.

60

[www.it-ebooks.info](http://www.it-ebooks.info/)

CHAPTER 3 ■ APPLICATION CONTROLS

Loading and Displaying the HTML

The define method doesn’t load the HTML; it just sets up the callbacks. My next step is to add a target element to the default.html file into which I’ll load content using the pages feature. You can see the element I have added in Listing 3-13\. No special attributes are required for the placeholder; a regular div element will do, ideally with an id attribute so you can easily locate it later.

***Listing 3-13.*** Adding a Page Placeholder Element in default.html

. . .

<div id="topRightContainer" class="gridRight">

<h1 class="win-type-xx-large">Item Detail</h1>

<div id="itemDetailTarget"></div>

</div>

. . .

The final step is to display the page within the target element. You can see the additions to the default.js file in Listing 3-14.

***Listing 3-14.*** Loading a WinJS Page

. . .

function performInitialSetup(e) {

WinJS.UI.processAll().then(function () {

UI.List.displayListItems();

UI.List.setupListEvents();

UI.AppBar.setupButtons();

UI.Flyouts.setupAddItemFlyout();

WinJS.Utilities.empty(itemDetailTarget)

WinJS.UI.Pages.render("/html/noSelection.html", itemDetailTarget);

});

}

. . .

The first new statement removes any child elements that already exist in the target element. The second new statement is the important one: I display the page by calling the WinJS.UI.Pages.render method. The arguments to this method are the URL of the HTML document and the placeholder element where the content will be inserted.

When I call the render method, WinJS not only inserts the HTML from the specified file into the document but ensures that my ready callback function, the one I defined in pages.js , is executed. This allows me to process the elements, in this case, to insert details about how many items are on the grocery list, as shown in Figure 3-4 .

61

[www.it-ebooks.info](http://www.it-ebooks.info/)

![](index-76_1.jpg)

CHAPTER 3 ■ APPLICATION CONTROLS

***Figure 3-4 .** Using a page to associate a callback function with a fragment of HTML*

Now that I am adding content to the top-right element in the layout, I have commented out the CSS background-color property I added in Chapter 1 to demonstrate how reloading an app in Visual Studio works. You can see the change I have made to the /css/default.css file to restore the element to the default background color used elsewhere in the app in Listing 3-15.

***Listing 3-15.*** Commenting Out the background-color Property from the default.css File

. . .

#topRightContainer {

-ms-grid-column: 2;

-ms-grid-row: 1;

/*background-color: #808080;*/

}

. . .

Loading a Complete HTML Document

A variation on the page example is to work with complete HTML documents, rather than fragments. The basic approach is the same, but I can include script and link elements to load JavaScript and CSS files that are specific to the document. This means I can keep my callback definition code and CSS styles separate from the rest of the application. To begin, I create a new folder called pages and another one called itemDetail inside it. I then add a new HTML file called itemDetail.html , which is shown in Listing 3-16.

***Listing 3-16.*** The itemDetail.html File

<!DOCTYPE html>

<html>

<head>

<title></title>

<script src="itemDetail.js"></script>

<link href="itemDetail.css" rel="stylesheet">

</head>

62

[www.it-ebooks.info](http://www.it-ebooks.info/)

CHAPTER 3 ■ APPLICATION CONTROLS

<body>

<div id="itemEditor">

<div>

<label for="itemInput">Item:</label><input id="itemInput">

</div>

<div>

<label for="quantityInput">Quantity:</label>

<input id="quantityInput">

</div>

<div>

<label for="storeSelect">Store:</label>

<select id="storeSelect" size="3"></select>

</div>

</div>

</body>

</html>

The markup in this file provides a select and two input elements to allow the user to edit the details of a grocery list item. The difference from the previous example is that this is a complete HTML document that contains a script element that imports the itemDetail.js file and a link element that imports the itemDetail.css file, both of which I have created in the same pages/itemDetail folder, alongside itemDetail.html .

The /pages/itemDetail/itemDetail.css file contains some basic styles to lay out the elements in the HTML file. You can see the contents of the file in Listing 3-17\. This is just standard CSS, and the file contains no Windows 8—specific features.

***Listing 3-17.*** The Contents of itemDetail.css

#itemEditor {font-weight: bold;margin-top: 20px;}

#itemEditor input {margin-bottom: 5px;display: block; font-size: 16pt;}

#itemEditor select {font-size: 16pt;}

#itemEditor select, #itemEditor textarea {margin-top: 0px;display: block;}

#buttons {margin-top: 20px;}

iframe {width: calc(100% - 10px);margin-top: 20px;height: 100%;}

#storeDetailTarget, #storeSelectionContainer {height: 100%;}

The /p/ages/itemDetail/itemDetail.js file contains the callback for when the page is loaded, as shown in Listing 3-18.

***Listing 3-18.*** Defining the Page Callback for the itemDetail.html Page

/// <reference path="//Microsoft.WinJS.1.0/js/base.js" />

/// <reference path="//Microsoft.WinJS.1.0/js/ui.js" /> (function () {

"use strict";

63

[www.it-ebooks.info](http://www.it-ebooks.info/)

CHAPTER 3 ■ APPLICATION CONTROLS

WinJS.UI.Pages.define("/pages/itemDetail/itemDetail.html", {

ready: function (targetElement) {

var selectedIndex = ViewModel.State.selectedItemIndex; var selectedItem = ViewModel.UserData.getItems()

.getAt(selectedIndex);

itemInput.value = selectedItem.item;

quantityInput.value = selectedItem.quantity;

WinJS.Utilities.empty(storeSelect);

var list = ViewModel.UserData.getStores();

list.forEach(function (item) {

var newOption = document.createElement("option"); newOption.text = item;

if (selectedItem.store == item) {

newOption.selected = true;

}

storeSelect.add(newOption);

});

WinJS.Utilities.query('#itemEditor input, #itemEditor select')

.listen("change", function () {

ViewModel.UserData.getItems().setAt(selectedIndex, {

item: itemInput.value,

quantity: quantityInput.value,

store: storeSelect.value

});

});

}

})

})();

When I call the define method, I have to specify the *complete* path to the HTML

document that I want to be notified about. This means pages/itemDetail/itemDetail.

html rather than just itemDetail.html . This is different from the links to the CSS and JavaScript files defined inside itemDetail.html , which are relative (i.e., just itemDetail.js ).

Since the Windows 8 runtime for JavaScript apps is essentially a regular browser, the JavaScript code is executed as soon as the script element is processed. This means that the callback function is registered before the document has been completely loaded and will then be called once the document has been completely processed. This provides the foundation for a nice way to keep pages self-contained, and you don’t need to have the callbacks defined alongside the main app code.

The callback function in this example is pretty simple and just uses the values from the input and select elements to modify the list of grocery items using the WinJS.Binding.List.setAt method. The setAt method completely replaces the item in 64

[www.it-ebooks.info](http://www.it-ebooks.info/)

CHAPTER 3 ■ APPLICATION CONTROLS

the list and triggers an itemchanged event, which I showed you in Chapter 2 . This event causes the table element displaying the list items to be updated with the changes the user has made.

Switching Between Pages

Now that I have two pages, I can switch between them in the main layout. I want to display the noSelection.html page when no item has been selected and the itemDetail.html page when a selection has been made. The simplest way to arrange this is to bind to the ViewModel.State.selectedItemIndex property. Listing 3-19 shows the changes in the default.js file.

***Listing 3-19.*** Switching Between Two Pages in Response to a View Model Update

. . .

function performInitialSetup(e) {

WinJS.UI.processAll().then(function () {

UI.List.displayListItems();

UI.List.setupListEvents();

UI.AppBar.setupButtons();

UI.Flyouts.setupAddItemFlyout();

ViewModel.State.bind("selectedItemIndex", function (newValue) {

WinJS.Utilities.empty(itemDetailTarget)

var url = newValue == −1 ? "/html/noSelection.html" :

"/pages/itemDetail/itemDetail.html"

WinJS.UI.Pages.render(url, itemDetailTarget);

});

});

}

. . .

I respond to changes in the selectedItemIndex property by altering the value of the URL argument I pass to the WinJS.UI.Pages.render method. Each time a page is displayed, the relevant callback function is executed, and my content is updated and presented to the user.

■ **Tip** Note that when you use the bind method, your callback function will be executed with the current value of the property you are monitoring. This allows me to ensure that the correct page is displayed, even when the user has yet to make a selection.

The result of these additions is that clicking an item in the list allows the user to edit its details, while marking the item done or adding a new item clears the selection and hides the detail page. You can see the effect in Figure 3-5 .

65

[www.it-ebooks.info](http://www.it-ebooks.info/)

![](index-80_1.jpg)

CHAPTER 3 ■ APPLICATION CONTROLS

***Figure 3-5 .** Allowing the user to edit the details of a grocery list item* The code in the callback for a page is executed *every time* that page is displayed. You need to ensure that your code doesn’t make assumptions about the state of the elements in your page, other than they have been loaded and added to the main layout. Since a page is loaded within the context of the main layout, you can safely use the functions defined by JavaScript code used by the main HTML document. In my example, this includes the view model. Similarly, your elements will be subject to the CSS styles that the main document defines.

**T**

■ **ip** Notice that I have used the size attribute on the select element so that several choices are shown at once. This is the Windows 8 convention for select elements that are part of the main application layout. However, for flyouts, such as the one I created earlier in this chapter, the convention is a single-line select that opens a drop-down list of options.

Displaying External Content

The WinJS page feature works only on content that is part of the application. You will generate an error if you request an external URL using the WinJS.UI.Pages.render method. Instead, you must use an iframe element if you want to display an external HTML document, but you can combine this element with the WinJS page feature if you want to get the benefit of breaking your application down into small and manageable pieces. To demonstrate this, I have created a new file in the html directory called storeDetail.html . Listing 3-20 shows the contents of this file.

***Listing 3-20.*** The storeDetail.html File

<!DOCTYPE html>

<html>

<head>

<title></title>

</head>

66

[www.it-ebooks.info](http://www.it-ebooks.info/)

CHAPTER 3 ■ APPLICATION CONTROLS

<body>

<div id="noStoreSelectionContainer" class="win-type-x-large">

<p>Select an item on the list</p>

</div>

<div id="storeSelectionContainer">

<iframe id="storeFrame" seamless sandbox=""></iframe>

</div>

</body>

</html>

This is a simple document that acts as a wrapper around the iframe element and a placeholder element to display when no list item has been selected. The most important element is, of course, the iframe . The seamless attribute specifies that no border should be drawn around the iframe , and setting the sandbox attribute to the empty string prevents the embedded content from running any scripts, navigating to new pages, and submitting forms. I am going to use the iframe to display the home page of the grocery store associated with the selected item. These pages contain all sorts of tracking scripts and some very exception-prone code, all of which I want to spare my user from having to deal with.

Adding the Callback

I am going to use the same approach for defining a callback as I did when loading a fragment. I have added the callback function to the pages.js file, and you can see the additions in Listing 3-21.

***Listing 3-21.*** Defining a Callback for the Page

/// <reference path="//Microsoft.WinJS.1.0/js/base.js" />

/// <reference path="//Microsoft.WinJS.1.0/js/ui.js" /> (function () {

"use strict";

WinJS.UI.Pages.define("/html/noselection.html", {

ready: function (targetElement) {

numberCount.innerText = ViewModel.UserData.getItems().length;

}

});

WinJS.UI.Pages.define("/html/storeDetail.html", {

ready: function (targetElement) {

ViewModel.State.bind("selectedItemIndex", function (newValue) {

67

[www.it-ebooks.info](http://www.it-ebooks.info/)

CHAPTER 3 ■ APPLICATION CONTROLS

noStoreSelectionContainer.style.display =

(newValue != −1 ? "none" : "");

storeSelectionContainer.style.display =

(newValue == −1 ? "none" : "");

if (newValue != −1) {

var store = ViewModel.UserData.getItems()

.getAt(newValue).store;

var url = "http://" + store.replace(" ", "") + ".com"; document.getElementById("storeFrame").src = url;

}

});

}

})

})();

In the ready callback for this page, I bind to the selectedItemIndex property.

When the user selects an item, I use the iframe to display the home page of the relevant store. Changing the value of the style.display property lets me switch between the placeholder element and the detail for the current store. My technique to creating a URL

from a store name is pretty basic. I just remove any spaces and add .com to the end of the name. This won’t suffice for a real project, but it will do just fine for my simple example.

**Note**

■

Notice that I obtain the iframe element by using the document.getElementById method. If you refer to the element via its global variable, you will cause a security exception when you try to set the src attribute. This problem doesn’t occur if you obtain the element via the DOM API.

Showing the Page

You must avoid using ready callbacks to set up bindings for pages that are not always displayed, because the JavaScript code will be executed each time the page is loaded.

Over time, you end up with a series of bindings, all of which perform the same task. WinJS

doesn’t provide a mechanism for cleaning up when the content of a page is removed from the layout, so the best approach is to move bindings into the main JavaScript files for the application.

This isn’t a problem for the storeDetails.html page because it is loaded only once and never removed from the document. In this case, I am using the WinJS page feature only so that I can decompose my Windows 8 app into self-contained pieces to make development and maintenance easier. As you can see in Listing 3-22, I display the storeDetails.html page just once, meaning that I can expect the code in my callback to be executed just once as well.

68

[www.it-ebooks.info](http://www.it-ebooks.info/)

CHAPTER 3 ■ APPLICATION CONTROLS

***Listing 3-22.*** Displaying the Page

. . .

function performInitialSetup(e) {

WinJS.UI.processAll().then(function () {

UI.List.displayListItems();

UI.List.setupListEvents();

UI.AppBar.setupButtons();

UI.Flyouts.setupAddItemFlyout();

ViewModel.State.bind("selectedItemIndex", function (newValue) {

WinJS.Utilities.empty(itemDetailTarget)

var url = newValue == −1 ? "/html/noSelection.html" :

"/pages/itemDetail/itemDetail.html"

WinJS.UI.Pages.render(url, itemDetailTarget);

});

WinJS.UI.Pages.render("/html/storeDetail.html", storeDetailTarget);

});

}

. . .

The final step is to update the layout element in default.html , as follows:

. . .

<div id="bottomRightContainer" class="gridRight">

<h1 class="win-type-xx-large">Store Detail</h1>

<div id="storeDetailTarget"></div>

</div>

. . .

The result is that selecting an item on the main list will display the home page of the relevant store, as shown in Figure 3-6 .

69

[www.it-ebooks.info](http://www.it-ebooks.info/)

![](index-84_1.jpg)

CHAPTER 3 ■ APPLICATION CONTROLS

***Figure 3-6 .** Displaying external content* **Caution**

■

The content of the web sites that I have used in this example change often, and I have found that sometimes there are periods when the content won’t appear in the app because the JavaScript code on the web page upsets the app security policy applied by Windows. If this is the case, you can see the effect by using a “safer” web site. I find that Apress.com works well as a substitute.

Checking Manifest Permissions

Windows 8 apps are subject to a security sandbox. To obtain access to external content, your app must be granted one or more permissions in its manifest file. To ensure you have the access you need, open the package.appxmanifest file from the Solution Explorer and go to the Capabilities tab.

You will see a list of capabilities you can request. These are presented to the user when they view your app in the Windows 8 Store so that they can make an informed choice about what access they grant to your application. (Well, that’s the theory; in practice, users don’t generally pay attention to these declarations until an app does something that surprises them.)

The Internet (Client) capability is checked by default, but you may also require the Private Networks (Client & Server) capability, depending on where your content is coming from. Check the capabilities you require and save the manifest. I’ll return to the manifest in Chapter 4 to configure some of the other settings.

70

[www.it-ebooks.info](http://www.it-ebooks.info/)

CHAPTER 3 ■ APPLICATION CONTROLS

Summary

In this chapter, I introduced three important structural features for a Windows 8

app: AppBars, Flyouts, and the navigation model. These facilities start to bridge the gap between a generic web app and the tools for decomposing your application into manageable chunks.

You don’t have to use AppBars and Flyouts, but your application won’t fit into the Windows 8 model if you don’t. Part of the attraction of Windows 8 is to be able to use your HTML5 and JavaScript skills to create Windows applications. Creating an app that doesn’t follow the Windows 8 conventions is to miss the opportunity that Windows 8 presents to the web programmer.

Equally, you could elect to build your Windows 8 app using a single HTML

document. But, once again, this would be a missed opportunity. The constraints that drive web apps toward content consolidation don’t exist for Windows 8 apps, which means that the ease of development, testing, and maintenance that come from decomposing your content and code are worth exploring. The HTMLControl and pages features are key enablers to this development style.

71

[www.it-ebooks.info](http://www.it-ebooks.info/)

**CHAPTER 4**

**Layouts and Tiles**

In this chapter, I describe two of the features that allow an app to fit into the wider user experience presented by Windows 8\. The first of these features is the way that apps can be *snapped* and *filled* so that two apps can be viewed side by side. I show you how to adapt when your app is placed into one of these layouts and how to change the layout when your interactions don’t fit inside the layout constraints.

The second feature is the Windows 8 *tile* model. Tiles are at the heart of the Windows 8 replacement for the Start screen. At their simplest, they are static buttons that can be used to launch your app, but with a little work they can present the user with an invaluable snapshot of the state of your application, allowing the user to get an overview without having to run the application itself. In this chapter, I show you how to create *live* *tiles* by applying updates and by using a related feature, *badges* . Table 4-1 provides the summary for this chapter.

***Table 4-1.** Chapter Summary*

**Problem**

**Solution**

**Listing**

Adapt an app’s layout when it has been

Use CSS media queries with

1, 2

placed into a snapped or filled layout.

Windows 8–specific properties.

Detect changes in an app’s layout

Use the Windows.

3, 4

in code.

UI.ViewManagement namespace.

Break out from the snapped view

Call the tryUnsnap method.

5–7

Update for a tile.

Use an XML tile template and use

8–11

the Windows.UI.Notifications

namespace.

Update square and wide tiles.

Populate and combine two

12, 13

XML tile templates.

Apply a badge to a tile.

Populate and apply an XML

14–16

badge template.

73

[www.it-ebooks.info](http://www.it-ebooks.info/)

CHAPTER 4 ■ LAYOUTS AND TILES

Dealing with Windows 8 Orientations and Views

Windows 8 apps support a range of *orientations* and *layouts* to respond to changes in the device configuration. Orientation changes occur when the device is rotated, which is a common occurrence when devices are equipped with an orientation sensor. This includes most tablets and some laptop and hybrid devices. There are two orientations: *landscape* and *portrait* .

In addition, Windows 8 apps support *views* . The default view is *full-screen* and this is what you have seen so far with the examples in the book. The app occupies the entire screen, and no other apps are visible. Windows 8 apps can also be *snapped* into a 320-pixel strip of the screen so that the user can see two applications at once. The other app, which occupies all of the display aside from those 320 pixels, is said to be *filled* .

Ensuring that your application can adapt to different orientations and views is essential to providing the full Windows 8 experience to your users.

**Note**

■

The user is responsible for changing the orientation and view, and the app is responsible for adapting. This means you can’t programmatically enter a new orientation or force your app into the filled or snapped view. Equally, you can’t write a Windows 8 app that refuses to enter the filled or snapped views. You can elect not to present the user with any useful functionality in certain views, but this will severely limit the value your app offers, and I recommend against this approach.

Simulating Orientation and View Changes

The best place to start with orientations and view changes is to learn how to create them in the Visual Studio simulator. This lets you test the way that your app responds without needing a device equipped with an orientation sensor (which, for obvious reasons, are rarely found on development PCs).

Simulating Orientation Changes

The Visual Studio simulator has two dedicated buttons for simulating orientation changes, which I have shown in Figure 4-1 . Clicking these buttons simulates the effect of rotating the device by 90 degrees in the direction shown by the button icon.

74

[www.it-ebooks.info](http://www.it-ebooks.info/)

![](index-88_1.jpg)

CHAPTER 4 ■ LAYOUTS AND TILES

***Figure 4-1 .** The Visual Studio simulator orientation buttons* The figure also shows what happens to the layout of the example app when the device is rotated to the portrait orientation. As you can see, the sizes that I allocated to the various elements in the HTML cumulatively occupy more space than is available in this orientation, which causes parts of the app to overlap.

Simulating Views Changes

The snapped and filled views are available only in the landscape orientation and when the horizontal resolution of the display is 1,366 pixels or greater. This means that the first step in changing the view is to orient the simulator to landscape and ensure that the resolution is set appropriately (you can change the resolution of the simulator using the button immediately below those used for orientation changes).

The simplest way to change the view is to press Win+. (the Windows and period keys). This will activate the snapped view, where the app is displayed in the small 320-pixel strip, as shown in Figure 4-2 . My example app doesn’t yet adapt to view changes, and you can see the effect of trying to fit all of the HTML elements in such a small space.

75

[www.it-ebooks.info](http://www.it-ebooks.info/)

![](index-89_1.jpg)

![](index-89_2.jpg)

CHAPTER 4 ■ LAYOUTS AND TILES

***Figure 4-2 .** The example app shown in the snapped view* Windows displays a divider bar between the two apps that are displayed. Drag the bar to move the app into the filled view, which I have shown in Figure 4-3 .

***Figure 4-3 .** The example app shown in the filled view* 76

[www.it-ebooks.info](http://www.it-ebooks.info/)

CHAPTER 4 ■ LAYOUTS AND TILES

The example app shows pretty common default behavior. The loss of the 320 pixels in the filled view doesn’t have much of an effect, especially on larger-resolution screens.

As you can see in Figure 4-3 , the app layout has coped reasonably well, and while the content of some elements has been wrapped across two lines, the basic essence of the app is preserved.

By contrast, the snapped view has rendered the app entirely useless; 320 pixels isn’t a lot of space when you have been writing HTML and CSS that assumes you have a lot more room to cope with.

Adapting to Orientation and View Changes

Two mechanisms are available for responding to orientation changes. You can use CSS, or you can use JavaScript. You can, if you want, mix and match the approaches, and that’s what I’ll be doing in the sections that follow to demonstrate both approaches.

Adapting with CSS

Microsoft has defined a custom CSS media query property and a set of values that can be used to change the CSS for a Windows 8 app when the orientation or view is changed.

These are added to the default.css file when Visual Studio creates a new Windows 8

application project, as shown in Listing 4-1.

***Listing 4-1.*** The Orientation and View Media Queries

/*

 **...MetroGrocer styles removed for brevity...*

 ***/*

@media screen and (-ms-view-state: fullscreen-landscape) {

}

@media screen and (-ms-view-state: filled) {

}

@media screen and (-ms-view-state: snapped) {

}

@media screen and (-ms-view-state: fullscreen-portrait) {

}

The property name is –ms-view-state , and you can see the range of supported values in the listing, which cover the orientation and view permutations. (Remember that the filled and snapped views are available only when the device is in the landscape orientation.) To adapt to a given orientation of view, you simple define CSS styles in the corresponding media query. The styles you define will be applied only when the user changes the orientation or view, and as a demonstration, Listing 4-2 shows how I have defined styles that are applied only when the user puts the app into the snapped view.

77

[www.it-ebooks.info](http://www.it-ebooks.info/)

CHAPTER 4 ■ LAYOUTS AND TILES

***Listing 4-2.*** Defining Styles for the Snapped View

/*

... *MetroGrocer styles removed for brevity*...

*/

@media screen and (-ms-view-state: fullscreen-landscape) {

}

@media screen and (-ms-view-state: filled) {

}

@media screen and (-ms-view-state: snapped) {

#contentGrid div.gridRight, #listTable td:last-child, #listTable th {

display: none;

}

#listTable td { white-space: nowrap;}

#listTable td:first-child { border-right: thin solid white;}

#contentGrid div.gridLeft { margin-left: 0.5em;}

#contentGrid {-ms-grid-columns: 1fr;}

}

@media screen and (-ms-view-state: fullscreen-portrait) {

}

For my example application, the snapped view presents too little space to display the entire layout. As you can see in the listing, I have used the snapped media query to hide some elements and change the appearance and behavior of others. You can see how these styles are applied in the snapped view in Figure 4-4 , where my styles present the user with a reduced view of the data in the app.

78

[www.it-ebooks.info](http://www.it-ebooks.info/)

![](index-92_1.jpg)

CHAPTER 4 ■ LAYOUTS AND TILES

***Figure 4-4 .** Adapting the app layout using CSS media queries* Notice that Windows adapts the Windows 8 structural elements automatically, such as removing the labels from AppBar buttons in the snapped layout.

**T**

■ **ip** The normal CSS precedence rules apply to styles defined within these queries, which means you would normally want to make the link element for the default.css file the last one to appear in the default.html file.

Adapting with JavaScript

You can do a surprising amount to adapt to different layouts using just CSS, but there comes a point where you need to be able to adapt in JavaScript as well. The Windows.

UI.ViewManagement namespace defines an object called ApplicationView that provides details about the current layout. You can receive notification when the orientation or the view changes by handling the standard DOM resize event. In Listing 4-3, you can see how I have added a handler function for the resize event to the default.js file.

***Listing 4-3.*** Adapting to Changes in the default.js File

...

function performInitialSetup(e) {

WinJS.UI.processAll().then(function () {

UI.List.displayListItems();

79

[www.it-ebooks.info](http://www.it-ebooks.info/)

CHAPTER 4 ■ LAYOUTS AND TILES

UI.List.setupListEvents();

UI.AppBar.setupButtons();

UI.Flyouts.setupAddItemFlyout();

ViewModel.State.bind("selectedItemIndex", function (newValue) {

WinJS.Utilities.empty(itemDetailTarget)

var url = newValue == -1 ? "/html/noSelection.html" :

"/pages/itemDetail/itemDetail.html"

WinJS.UI.Pages.render(url, itemDetailTarget);

});

WinJS.UI.Pages.render("/html/storeDetail.html", storeDetailTarget); function setOrientationClass() {

if (Windows.UI.ViewManagement.ApplicationView.value ==

Windows.UI.ViewManagement.ApplicationViewState

.fullScreenPortrait) {

WinJS.Utilities.addClass(contentGrid, "flex");

} else {

WinJS.Utilities.removeClass(contentGrid, "flex");

}

};

window.addEventListener("resize", setOrientationClass); setOrientationClass();

});

}

I have defined a function called setOrientationClass , which reads the value property of the ApplicationView object in the Windows.UI.ViewManagement namespace.

This property returns one of the values defined in the ApplicationViewState object, which can also be found in the Windows.UI.ViewManagement namespace; these values correspond to the CSS media query properties and are fullScreenLandscape , filled , snapped , and fullScreenPortrait . In my setOrientationClass function, I add a CSS class called flex to the contentGrid element if the value property returns fullScreenPortrait . For all of the other orientation and view values, I remove the flex class.

I use the setOrientationClass as the handler function for the resize event, which is emitted by the DOM window object. You will receive the resize event only when the user changes the view or orientation after the app has changed, so I also call the setOrientationClass function directly to ensure that I apply my CSS flex class if the app is started while the device is in the portrait mode. To support this code, I have added the CSS styles shown in Listing 4-4 to the /css/default.css file. You can respond to the resize event in any way that makes sense in your app, but for the most part, the easiest way to control the layout of a Windows 8 app is just the same as for a regular web app: by adding and removing CSS classes and styles.

80

[www.it-ebooks.info](http://www.it-ebooks.info/)

![](index-94_1.jpg)

CHAPTER 4 ■ LAYOUTS AND TILES

***Listing 4-4.*** Additions to the /css/default.css File to Support JavaScript Orientation Changes

...

#contentGrid.flex {

display: -ms-flexbox;

-ms-flex-direction: column;

-ms-flex-align: stretch;

-ms-flex-pack: start;

}

#contentGrid.flex > * {

background-color: gray;

padding: 20px;

margin: 10px;

border: medium solid white;

}

...

For this example, I am relying on the flexbox layout feature, which I describe in the “A Quick Introduction to CSS3 Flexbox layouts” sidebar. You can see the effect of adapting to the orientation in this way in Figure 4-5 , which shows what happens when I put the Visual Studio simulator into a portrait orientation.

***Figure 4-5 .** Adapting to the portrait orientation using JavaScript* As you can see, the CSS I have applied changes the layout of the app so that the functional areas are stacked vertically—a layout particularly well suited to the portrait orientation.

81

[www.it-ebooks.info](http://www.it-ebooks.info/)

CHAPTER 4 ■ LAYOUTS AND TILES

**A QUICK INTRODUCTION TO CSS3 FLEXBOX LAYOUTS**

In Listing 4-4, I have applied the CSS3 *flexbox* layout, which is more properly known as the flexible box layout. This is another part of CSS3 that has not yet been finalized and that is accessed through an –ms vendor prefix, in this case –ms-flexbox .

Flexbox layouts make it easy to create fluid and adaptable layouts, in contrast to the precision offered by the grid layout that I described in Chapter 1 . In this sidebar, I provide you with a very quick introduction to the basic features of CSS3 flexbox layouts.

To create a flexbox layout, you must set the display property to –ms-flexbox and specify how the child elements will be laid out, like this:

#contentGrid.flex {

display: -ms-flexbox;

-ms-flex-direction: column;

-ms-flex-align: stretch;

-ms-flex-pack: start;

}

The –ms-flex-direction property specifies the direction that elements will laid out. The most commonly used values are column (which lays out elements top to bottom) and row (which lays out elements left to right).

The –ms-flex-pack property specifies how the elements will be laid out along the axis you have set with the –ms-flex-direction property. I have used the start value, which stacks elements next to each other at the start of the axis. The other commonly used values are end (which stacks elements at the end of the axis), distribute (which spaces out the elements along the axis), and justify (which resizes the elements so they fully occupy the space available).

The –ms-flex-align property specifies how elements are positioned in the other direction. I have used the stretch value, which resizes elements so they fill the space available. Other commonly used values are start , end , and center , which position the elements to the leading or trailing edge of the container or position them centrally.

The best way to understand the effect of these properties is to experiment with them and with a bit of practice, you can create very flexible and adaptable layouts. See the full specification at http://www.w3.org/TR/css3-flexbox for full details, bearing in mind that this is still a draft standard.

82

[www.it-ebooks.info](http://www.it-ebooks.info/)

CHAPTER 4 ■ LAYOUTS AND TILES

Programmatically Exiting the Snapped View

In this section, I’ll show you how to programmatically exit the snapped view. To demonstrate this feature, I first need to make some additions to the example app to present the user with a button only when the app is actually in the snapped view. I’ll define and style the button using HTML and CSS. To begin, I have added some new elements to the default.html file, as shown in Listing 4-5.

***Listing 4-5.*** Adding Elements to the default.html File

...

<div id="contentGrid">

<div id="leftContainer" class="gridLeft">

<h1 class="win-type-xx-large">Grocery List</h1>

<table id="listTable" class="type-table-header">

<thead>

<tr>

<th>Quantity</th>

<th class="itemName">Item</th>

<th class="store">Store</th>

</tr>

</thead>

<tbody id="itemBody"></tbody>

</table>

</div>

<div class="buttonContainer">

<button id="unsnapButton">Unsnap</button>

</div>

<div id="topRightContainer" class="gridRight">

<h1 class="win-type-xx-large">Item Detail</h1>

<div id="itemDetailTarget"></div>

</div>

<div id="bottomRightContainer" class="gridRight">

<h1 class="win-type-xx-large">Store Detail</h1>

<div id="storeDetailTarget"></div>

</div>

</div>

...

The additions are a button element that the user will click to exit the snapped view, contained in a div element that I’ll use to make managing the layout and visibility of the button easier. You can see the additions I have made to the /css/default.css file to configure the new elements in Listing 4-6.

83

[www.it-ebooks.info](http://www.it-ebooks.info/)

CHAPTER 4 ■ LAYOUTS AND TILES

***Listing 4-6.*** Adding New Styles to the default.css File to Support the Button body {

background-color: #4A8C43;

}

/*

... *other styles omitted for brevity*...

*/

div.buttonContainer {visibility: collapse;-ms-grid-row: 2; width: 100%; text-align:center;}

div.buttonContainer button {font-size: 20pt; margin: 20px;}

@media screen and (-ms-view-state: fullscreen-landscape) {

}

@media screen and (-ms-view-state: filled) {

}

@media screen and (-ms-view-state: snapped) {

#contentGrid div.gridRight, #listTable td:last-child, #listTable th {

display: none;

}

#listTable td { white-space: nowrap;}

#listTable td:first-child { border-right: thin solid white;}

#contentGrid div.gridLeft { margin-left: 0.5em;}

#contentGrid {-ms-grid-columns: 1fr;}

div.buttonContainer {visibility: visible;}

}

@media screen and (-ms-view-state: fullscreen-portrait) {

}

These styles ensure that the container element for the button is hidden unless the app is in the layout view, through the application of the visibility property. The value defined in the media query will be applied only when the snapped view is in use, and it will override the value set in the main body of the CSS file. Figure 4-6 shows how the button is displayed. The button and its container are hidden in all other views and orientations.

84

[www.it-ebooks.info](http://www.it-ebooks.info/)

![](index-98_1.jpg)

CHAPTER 4 ■ LAYOUTS AND TILES

***Figure 4-6 .** Showing the button element in the snapped view* I can now add a handler function to the /js/default.js file for the button element’s click event, which will exit the snapped view, as shown in Listing 4-7.

***Listing 4-7.*** Adding Code to Exit the Snapped View

...

function performInitialSetup(e) {

WinJS.UI.processAll().then(function () {

UI.List.displayListItems();

UI.List.setupListEvents();

UI.AppBar.setupButtons();

UI.Flyouts.setupAddItemFlyout();

ViewModel.State.bind("selectedItemIndex", function (newValue) {

WinJS.Utilities.empty(itemDetailTarget)

var url = newValue == -1 ? "/html/noSelection.html" :

"/pages/itemDetail/itemDetail.html"

WinJS.UI.Pages.render(url, itemDetailTarget);

});

WinJS.UI.Pages.render("/html/storeDetail.html", storeDetailTarget); function setOrientationClass() {

if (Windows.UI.ViewManagement.ApplicationView.value ==

Windows.UI.ViewManagement.ApplicationViewState

.fullScreenPortrait) {

WinJS.Utilities.addClass(contentGrid, "flex");

} else {

85

[www.it-ebooks.info](http://www.it-ebooks.info/)

CHAPTER 4 ■ LAYOUTS AND TILES

WinJS.Utilities.removeClass(contentGrid, "flex");

}

};

window.addEventListener("resize", setOrientationClass); setOrientationClass();

unsnapButton.addEventListener("click", function (e) {

Windows.UI.ViewManagement.ApplicationView.tryUnsnap();

});

});

}

...

The Windows.UI.ViewManagement.ApplicationView object defines a method called tryUnsnap , which attempts to break the app out of the snapped view. The result from this method is true if the view of the app was successfully changed and false otherwise. You can call this method at any time, but it will have an effect only if your app is visible to the user and is in the snapped view. This may seem obvious, but a result of false is usually attributed to one of these conditions not being met.

**Note**

■

The effect of tryUnsnap will depend on whether there is an app in the filled view.

If there is, then the views for the two on-screen apps will be reversed so that your app is in the filled view and the other app is in the snapped view. If there is no app in the filled view, then your app will be expanded to fill the screen.

Using Tiles and Badges

Tiles are the representation of your application on the Start screen. At their simplest, tiles are just static icons for starting your app. However, with a little effort, you can use your tile to present the user with a useful summary of your app’s state and to draw their attention to activities they may want to perform.

In the sections that follow, I demonstrate how to present information through the tile of my example Windows 8 app. There are two possible, and conflicting, goals when you create a live tile; you are trying to either encourage the user to run your app or dissuade them from running it. If you are trying to attract the user, then your tile becomes an ad for the experience, insights, or content you offer. This is appropriate for entertainment apps or those that present external content such as news.

86

[www.it-ebooks.info](http://www.it-ebooks.info/)

CHAPTER 4 ■ LAYOUTS AND TILES

Dissuading the user from running an app may seem like a strange goal, but it can significantly improve the user experience. Consider productivity apps as an example. I dread to think the hours I have lost waiting for calendar or to-do apps to load just so I can check where my next appointment is or what my most urgent action requires. You can reduce the friction and frustration that your users experience when using your app and create a more pleasing and immediate experience by displaying the information that the user needs in your app tile.

Both goals require careful thought. The overall Windows 8 experience is simple and subdued. If you are using your tile as an ad, then the muted nature of Windows 8 makes it easy to create tiles that stand out. If you go too far, though, you will create something that is discordant and jarring and is more of an eyesore than an attraction.

If your goal is to reduce the number of times the user needs to run your app, then you need to present the right information at the right time. This requires a good understanding of what drives your users to adopt your app and the ability to customize the data that is presented. Adaptability is essential; there is no point showing me the most urgent work action on my task list on a Saturday morning, for example. Every time you present the user with the wrong information, you force them to run your app to get what they do need.

Improving Static Tiles

The simplest way to improve the appearance of your application in the Start screen is to change the images used for your app’s tile. You should customize the images for your app, even if you don’t use any other tile features.

To do this, you will need a set of three images of specific sizes: 30x30 pixels, 150x150

pixels, and 310x150 pixels. These images should contain the logo or text you want to display but be otherwise transparent. I used a barcode motif for my example app, creating images called tile30.png , tile150.png , and tile310.png and placing them in the images folder of my Visual Studio project, as shown in Figure 4-7 . The tile620.png file is a 620x300 pixel, which I will use as the splash screen for the app that is shown as the app is being launched. This is not a tile-related feature, but it is applied through the same mechanism, so I will demonstrate its use here.

87

[www.it-ebooks.info](http://www.it-ebooks.info/)

![](index-101_1.png)

CHAPTER 4 ■ LAYOUTS AND TILES

***Figure 4-7 .** Adding tile images to the images folder of the example project* Applying the Images

To apply the new images, open the package.appxmanifest file from the Solution Explorer window. There is a Tile section on the Application UI tab that has options to set the logo, wide logo, and small logo. There are hints to explain which size is required for each option. In Figure 4-8 , you can see the how I have applied the images to populate the fields in the manifest, for both the file and the splash screen.

88

[www.it-ebooks.info](http://www.it-ebooks.info/)

![](index-102_1.png)

CHAPTER 4 ■ LAYOUTS AND TILES

***Figure 4-8 .** Populating the manifest fields* You also have to set the background color that will be used for the tile and the splash screen; I set mine to the same color I use for the body element of my app.

**T**

■ **ip** It is important to set the background color in the manifest, rather than include a background in the images. When you update a tile, which I demonstrate in the next section, the image is replaced with dynamic information, on a backdrop of the color specified in the manifest.

You may have to uninstall your app from the start screen for the tile images to take effect. The next time you start your app from Visual Studio, you should see the new tile.

You can toggle between the standard and wide views by selecting the tile and picking the 89

[www.it-ebooks.info](http://www.it-ebooks.info/)

![](index-103_1.jpg)

CHAPTER 4 ■ LAYOUTS AND TILES

Larger or Smaller buttons from the AppBar. You can see the square and wide tile formats for the example application in Figure 4-9 .

***Figure 4-9 .** The updated static normal wide tiles* Updating Tiles

For my example app, I am going to display the first few items on the grocery list. Tile updates are based on preconfigured templates, which contain a mix of graphics and text and are designed for either standard or wide tiles. The first thing you must do is pick the template you want. The easiest way to do this is to look at the API documentation for the Windows.UI.Notifications.TileTemplateType enumeration, which is available at http://msdn.microsoft.com/en-us/library/windows/apps/windows.

ui.notifications.tiletemplatetype.aspx .

The template system is based on XML fragments. I have chosen the tileSquareText03 template. This is for a square tile and has four lines of nonwrapping text, without any images. You can see the XML fragment that represents the tile in Listing 4-8.

***Listing 4-8.*** The XML Fragment for the tileSquareText03 Tile Template

<tile>

<visual lang="en-US">

<binding template="TileSquareText03">

<text id="1">Text Field 1</text>

<text id="2">Text Field 2</text>

<text id="3">Text Field 3</text>

<text id="4">Text Field 4</text>

</binding>

</visual>

</tile>

The idea is to populate the text elements with information from the application and pass the result to the Windows notifications system. To demonstrate this feature, I had added a new JavaScript file to the project called tiles.js , the content of which is shown in Listing 4-9.

90

[www.it-ebooks.info](http://www.it-ebooks.info/)

CHAPTER 4 ■ LAYOUTS AND TILES

***Listing 4-9.*** The tiles.js File

/// <reference path="//Microsoft.WinJS.1.0/js/base.js" />

/// <reference path="//Microsoft.WinJS.1.0/js/ui.js" /> (function () {

"use strict";

WinJS.Namespace.define("Tiles", {

sendTileUpdate: function () {

var tn = Windows.UI.Notifications;

var xmlFragment = tn.TileUpdateManager

.getTemplateContent(tn.TileTemplateType.tileSquareText03); var textNodes = xmlFragment.getElementsByTagName("text"); var items = ViewModel.UserData.getItems();

for (var i = 0; i < textNodes.length; i++) {

var listItem = items.getAt(i);

if (listItem) {

textNodes[i].innerText = listItem.item;

}

}

for (var i = 0; i < 5; i++) {

tn.TileUpdateManager.createTileUpdaterForApplication()

.update(new tn.TileNotification(xmlFragment));

}

}

});

var eventTypes = ["itemchanged", "iteminserted",

"itemmoved", "itemremoved"];

var itemsList = ViewModel.UserData.getItems();

eventTypes.forEach(function (type) {

itemsList.addEventListener(type, Tiles.sendTileUpdate);

});

})();

**Note**

■

The length of the Windows.UI.Notifications namespace is long enough to cause layout problems for code on the printed page, so I have created a variable called tn as shorthand and assigned the namespace to it.

**T**

■ **ip** Notice that the first letter of the template name is lowercase. If you use an uppercase letter, then you will get the default template rather than the one you wanted.

91

[www.it-ebooks.info](http://www.it-ebooks.info/)

CHAPTER 4 ■ LAYOUTS AND TILES

Populating the XML Template

To get the template XML fragment, I call the TileUpdateManager.getTemplateContent method, specifying the template I want with a value from the TileTemplateType .

This gives me a Windows.Data.Xml.Dom.XmlDocument object to which I can apply standard DOM methods to set the value of the text elements in the template. Well, sort of—because the XmlDocument object’s implementation of getElementById doesn’t work, I have to use the getElementsByTagName method to get an array containing all of the text elements in the XML. These elements are returned in the order they are defined in the XML fragment, so I can iterate through and set the innerText property of each element to one of my grocery list items.

**T**

■ **ip** Only three of the four text elements defined by the XML template will be visible by the user on the Start menu. The last element is obscured by the application name or icon.

This is true for many of the tile templates.

Creating the Tile Update

Once I have set the content of the XML document, I use it to create the update for the application tile. I need to create a TileNotification object from the XML and then pass this to the update method of the TileUpdater object that is returned from the TileUpdateManager.createTileUpdaterForApplication method:

...

for (var i = 0; i < 5; i++) {

tn.TileUpdateManager.createTileUpdaterForApplication()

.update(new tn.TileNotification(xmlFragment));

}

...

Not all tile updates are processed properly, which is why I repeat the notification using a for loop. Five seems to be the smallest number of repetitions that guarantees that an update will be displayed on the Start screen.

Applying the Tile Update

My tile update is applied in two places. As you saw in Listing 4-9, the tiles.js file sets up event handlers that call the sendTileUpdate function whenever the contents of the grocery list change. This ensures that the tile always reflects changes the user makes to the list. I also call the sendTileUpdate method from the performInitialSetup function in default.js , as shown in Listing 4-10.

92

[www.it-ebooks.info](http://www.it-ebooks.info/)

CHAPTER 4 ■ LAYOUTS AND TILES

***Listing 4-10.*** Updating the Tile As Part of the Application Setup

...

function performInitialSetup(e) {

WinJS.UI.processAll().then(function () {

UI.List.displayListItems();

UI.List.setupListEvents();

UI.AppBar.setupButtons();

UI.Flyouts.setupAddItemFlyout();

ViewModel.State.bind("selectedItemIndex", function (newValue) {

WinJS.Utilities.empty(itemDetailTarget)

var url = newValue == -1 ? "/html/noSelection.html" :

"/pages/itemDetail/itemDetail.html"

WinJS.UI.Pages.render(url, itemDetailTarget);

});

WinJS.UI.Pages.render("/html/storeDetail.html", storeDetailTarget); function setOrientationClass() {

if (Windows.UI.ViewManagement.ApplicationView.value ==

Windows.UI.ViewManagement.ApplicationViewState

.fullScreenPortrait) {

WinJS.Utilities.addClass(contentGrid, "flex");

} else {

WinJS.Utilities.removeClass(contentGrid, "flex");

}

};

window.addEventListener("resize", setOrientationClass); setOrientationClass();

unsnapButton.addEventListener("click", function (e) {

Windows.UI.ViewManagement.ApplicationView.tryUnsnap();

});

Tiles.sendTileUpdate();

});

}

...

Of course, since I have created a new JavaScript file, I need to link it to default.html , as shown in Listing 4-11.

***Listing 4-11.*** Adding the tiles.js File to default.html

...

<head>

<meta charset="utf-8">

<title>MetroGrocer</title>

93

[www.it-ebooks.info](http://www.it-ebooks.info/)

![](index-107_1.png)

CHAPTER 4 ■ LAYOUTS AND TILES

<!-- WinJS references -->

<link href="//Microsoft.WinJS.1.0/css/ui-dark.css" rel="stylesheet">

<script src="//Microsoft.WinJS.1.0/js/base.js"></script>

<script src="//Microsoft.WinJS.1.0/js/ui.js"></script>

<!-- MetroGrocer references -->

<link href="/css/list.css" rel="stylesheet">

<link href="/css/flyout.css" rel="stylesheet">

<link href="/css/default.css" rel="stylesheet">

<script src="/js/viewmodel.js"></script>

<script src="/js/ui.js"></script>

<script src="/js/pages.js"></script>

<script src="/js/tiles.js"></script>

<script src="/js/default.js"></script>

</head>

...

Testing the Tile Update

A couple of preparatory steps are required before I can test my updating tile. First, the Visual Studio simulator doesn’t support updating tiles, which means I am going to have to test directly on my development machine. To do this, I need to change the Visual Studio deployment target to Local Machine, as shown in Figure 4-10 .

***Figure 4-10 .** Selecting the local machine for debugging* The second step is to uninstall my example app from the Start screen (which you do by selecting Uninstall from the AppBar). There seems to be some “stickiness” where apps that have previously relied on static tiles don’t process updates correctly.

With both of these steps completed, I can now start my application from Visual Studio by selecting Start Debugging from the Debug menu. When the application has started, I can make changes to the grocery list, and a pithy summary of the first three items will be shown on the start tile, as shown in Figure 4-11 .

94

[www.it-ebooks.info](http://www.it-ebooks.info/)

![](index-108_1.jpg)

CHAPTER 4 ■ LAYOUTS AND TILES

***Figure 4-11 .** Updating an application tile* **T**

■ **ip** You may not be able to find the tile on the Start screen if you have been using the simulator. If this is the case, search for the app by typing the first few letters of its name.

Clicking the small search result icon will cause the tile to appear. If that doesn’t work, then reboot and start the application locally, making sure not to start the simulator.

Updating Wide Tiles

The technique I showed you in the previous section is useful if you want to be able to update the square *or* the wide tile for your application. But, unless you have very specific presentation needs for your data, you should provide updates for both square and wide tiles since you have no idea which your users will select.

To update both tile sizes, you need to combine two XML templates to create a single fragment that contains both updates. In this section, I am going to combine the tileSquareText03 and tileWideBlockAndText01 templates. The wide template has a couple of additional fields, which I will use to display the number of stores that the user has to visit to get all of the items on the grocery list. You can see what I am aiming to produce in Listing 4-12—a fragment that follows the same format as a single template but that combines two binding elements.

***Listing 4-12.*** Composing a Single XML Fragment

<tile>

<visual lang="en-US">

<binding template="TileSquareText03">

<text id="1">Apples</text>

<text id="2">Hotdogs</text>

<text id="3">Soda</text>

<text id="4"></text>

</binding>

<binding template="TileWideBlockAndText01">

<text id="1">Apples (Whole Foods)</text>

<text id="2">Hotdogs (Costco)</text>

<text id="3">Soda (Costco)</text> 95

[www.it-ebooks.info](http://www.it-ebooks.info/)

CHAPTER 4 ■ LAYOUTS AND TILES

<text id="4"></text>

<text id="5">2</text>

<text id="6">Stores</text>

</binding>

</visual>

</tile>

There is no convenient API for combining templates. The approach I have taken is to use the XML handling support to populate the templates separately and then combine them at the end of the process. You can see how I have done this in Listing 4-13, which shows the changes I have made to the tiles.js file.

***Listing 4-13.*** Producing a Single Update for Square and Wide Tiles

/// <reference path="//Microsoft.WinJS.1.0/js/base.js" />

/// <reference path="//Microsoft.WinJS.1.0/js/ui.js" /> (function () {

"use strict";

WinJS.Namespace.define("Tiles", {

sendTileUpdate: function () {

var storeCounter = { count: 0 };

ViewModel.UserData.getItems().forEach(function (listItem) {

if (!storeCounter[listItem.store]) {

storeCounter[listItem.store] = true;

storeCounter.count++;

}

});

var tn = Windows.UI.Notifications;

var squareXmlFragment = tn.TileUpdateManager

.getTemplateContent(tn.TileTemplateType.tileSquareText03); var wideXmlFragment = tn.TileUpdateManager

.getTemplateContent(tn.TileTemplateType.tileWideBlockAndText01); var squareTextNodes =

squareXmlFragment.getElementsByTagName("text"); var wideTextNodes =

wideXmlFragment.getElementsByTagName("text"); var items = ViewModel.UserData.getItems();

for (var i = 0; i < squareTextNodes.length; i++) {

var listItem = items.getAt(i);

if (listItem) {

squareTextNodes[i].innerText = listItem.item;

96

[www.it-ebooks.info](http://www.it-ebooks.info/)

CHAPTER 4 ■ LAYOUTS AND TILES

wideTextNodes[i].innerText = listItem.item + " ("

+ listItem.store + ")";

}

}

wideTextNodes[4].innerText = storeCounter.count;

wideTextNodes[5].innerText = "Stores";

var wideBindingElement =

wideXmlFragment.getElementsByTagName("binding")[0]; var importedNode = squareXmlFragment.importNode(wideBindingElement, true);

var squareVisualElement =

squareXmlFragment.getElementsByTagName

("visual")[0];

squareVisualElement.appendChild(importedNode);

for (var i = 0; i < 5; i++) {

tn.TileUpdateManager.createTileUpdaterForApplication()

.update(new tn.TileNotification(squareXmlFragment))

}

}

});

var eventTypes = ["itemchanged", "iteminserted",

"itemmoved", "itemremoved"];

var itemsList = ViewModel.UserData.getItems();

eventTypes.forEach(function (type) {

itemsList.addEventListener(type, Tiles.sendTileUpdate);

});

})();

The wider format tile gives me an opportunity to present more information to the user on each line. In this case, I include information about which store an item is to be purchased from in addition to the overall number of store visits required.

Combining templates isn’t a difficult process to master, but you have to take care when trying to merge the two XML fragments. I have used the template for the square tile as the basis for my combined update. When I add the binding element from the wide template, I have to first import it into the square XML document, like this: var importedNode = squareXmlFragment.importNode(wideBindingElement, true); The importNode method creates a new copy of my wide binding element in the context of my square document. The arguments to the importNode method are the element I want to import and a value indicating whether I want child nodes to be imported as well (which, of course, I do). Once I have created this new element, I insert it into the square XML using the appendChild element: squareVisualElement.appendChild(importedNode);

97

[www.it-ebooks.info](http://www.it-ebooks.info/)

![](index-111_1.jpg)

CHAPTER 4 ■ LAYOUTS AND TILES

The result is the combined document I showed you in Listing 4-12\. You can see the appearance of both tile sizes in Figure 4-12 . (You can toggle between the square and wide versions by selecting the tile in the Windows Start screen and right-clicking or swiping to show the AppBar. The Smaller and Larger buttons switch the tile to and from the wide configuration.)

***Figure 4-12 .** Updating a wide tile* Applying Badges

Windows 8 manages to pack a lot of features into tiles, including support for *badges* , which are small icon or numeric overlays for a tile. The latter fall into the tile-as-an-advert category because there are very few situations in which a numeric representation does anything other than invite the user to start the app. To demonstrate badges, I am going to show a simple indicator based on the number of items in the grocery list. Listing 4-14

shows the additions to the tiles.js file.

***Listing 4-14.*** Adding Support for Tile Badges

/// <reference path="//Microsoft.WinJS.1.0/js/base.js" />

/// <reference path="//Microsoft.WinJS.1.0/js/ui.js" /> (function () {

"use strict";

WinJS.Namespace.define("Tiles", {

sendBadgeUpdate: function () {

var itemCount = ViewModel.UserData.getItems().length; var tn = Windows.UI.Notifications;

var templateType = itemCount ? tn.BadgeTemplateType.badgeGlyph

: tn.BadgeTemplateType.badgeNumber;

var badgeXml =

tn.BadgeUpdateManager.getTemplateContent(templateType); var badgeAttribute = badgeXml.getElementsByTagName("badge")[0]; badgeAttribute.setAttribute("value",

itemCount > 3 ? "alert" : itemCount);

98

[www.it-ebooks.info](http://www.it-ebooks.info/)

CHAPTER 4 ■ LAYOUTS AND TILES

for (var i = 0; i < 5; i++) {

var badgeNotification = new tn.BadgeNotification(badgeXml); tn.BadgeUpdateManager.createBadgeUpdaterForApplication()

.update(badgeNotification);

}

},

sendTileUpdate: function () {

//

*...code removed for brevity...*

 *}

});

var eventTypes = ["itemchanged", "iteminserted",

"itemmoved", "itemremoved"];

var itemsList = ViewModel.UserData.getItems();

eventTypes.forEach(function (type) {

itemsList.addEventListener(type, function () {

Tiles.sendTileUpdate();

Tiles.sendBadgeUpdate();

});

});

})();

Badges work in a similar way to tile notifications. You obtain an XML template, populate the content, and use it to present some information to the user via the Start menu.

Two types of badge template are available. The first will display a numeric value between 1 and 99, and the second will display a small image from a limited range defined by Windows.

The numeric and iconic templates are represented using the same XML and, as Listing 4-15 shows, are much simpler than the ones I used for tiles.

***Listing 4-15.*** The Template for Numeric and Image Badges

<badge value=""/>

The objective is to set the value attribute to either a numeric value or the name of an icon. I display a numeric badge if there are three or fewer items on the grocery list. If there are more than three items, then I use an icon to indicate that the user should be concerned about the extent of their shopping obligations.

The process for creating a badge begins with selecting a template. The two template types are Windows.UI.Notifications.BadgeTemplateType : for numeric badges you use the badgeNumber template, and for icons you use the badgeGlyph template. You could use the same template in both situations because they return the same XML. This may change in later releases or updates, so it is prudent to select the right template, even though the content is the same.

The next step is to locate the value attribute in the XML and assign it either a numeric value or the name of an icon. The numeric range for badges is very specific; it is from 1 to 99\. If you set the value to less than 1, the badge won’t be displayed at all. Any value greater than 99 results in a badge showing 99.

99

[www.it-ebooks.info](http://www.it-ebooks.info/)

CHAPTER 4 ■ LAYOUTS AND TILES

The list of icons is equally prescriptive. You cannot use your own icons and must choose from a list of ten that Windows supports. You can see a list of the icons at http://msdn.microsoft.com/en-us/library/windows/apps/hh761458.aspx .

For this example, I have chosen the alert icon, which looks like an asterisk. Once the XML is populated, you create a new BadgeNotification object and use it to post the update. As with tiles, I find that not all badges updates are processed, so I repeat the update five times to make sure it gets through:

...

for (var i = 0; i < 5; i++) {

var badgeNotification = new tn.BadgeNotification(badgeXml); tn.BadgeUpdateManager.createBadgeUpdaterForApplication()

.update(badgeNotification);

}

...

All that remains is to ensure that my badge updates are created. To do this, I have changed the event handler for the grocery list events so that the tile and badge are updated together. I also need to add a call to the sendBadgeUpdate method in the

/js/default.js file, which you can see in Listing 4-16\. This ensures that the badge is updated as soon as the app is started.

***Listing 4-16.*** Updating the Badge When the App Starts

...

function performInitialSetup(e) {

WinJS.UI.processAll().then(function () {

UI.List.displayListItems();

UI.List.setupListEvents();

UI.AppBar.setupButtons();

UI.Flyouts.setupAddItemFlyout();

ViewModel.State.bind("selectedItemIndex", function (newValue) {

WinJS.Utilities.empty(itemDetailTarget)

var url = newValue == -1 ? "/html/noSelection.html" :

"/pages/itemDetail/itemDetail.html"

WinJS.UI.Pages.render(url, itemDetailTarget);

});

WinJS.UI.Pages.render("/html/storeDetail.html", storeDetailTarget); function setOrientationClass() {

if (Windows.UI.ViewManagement.ApplicationView.value

== Windows.UI.ViewManagement.ApplicationViewState

.fullScreenPortrait) {

WinJS.Utilities.addClass(contentGrid, "flex"); 100

[www.it-ebooks.info](http://www.it-ebooks.info/)

![](index-114_1.jpg)

CHAPTER 4 ■ LAYOUTS AND TILES

} else {

WinJS.Utilities.removeClass(contentGrid, "flex");

}

};

window.addEventListener("resize", setOrientationClass); setOrientationClass();

unsnapButton.addEventListener("click", function (e) {

Windows.UI.ViewManagement.ApplicationView.tryUnsnap();

});

Tiles.sendTileUpdate();

Tiles.sendBadgeUpdate();

});

}

...

You can see the four different badge/tile configurations in Figure 4-13 , wide and square tiles, with number and icon badges.

***Figure 4-13 .** Displaying a badge on a tile* Summary

In this chapter, I showed you how to adapt to the Windows 8 snapped and filled layouts and how to use tiles to provide your users with enticements to run your app or the data they require to avoid doing so. These features are essential in delivering an app that is integrated into the broader Windows 8 experience.

You may feel that the amount of space available in a snapped layout is too limited to offer any serious functionality, but with some careful consideration, it is possible to focus on the essence of the service that you offer and omit everything else. Careful consideration is also required to get the most from tiles and badges. Well-thought-out badges can significantly improve the attractiveness or utility of your app, but ill-considered tiles are annoying or just plain useless.

101

[www.it-ebooks.info](http://www.it-ebooks.info/)

**CHAPTER 5**

**Life-Cycle Events**

In this, the final chapter in this book, I show you how to take control of the Windows 8

app life cycle by responding to key Windows events. I show you how to replace code that Visual Studio adds to projects, how to properly deal with your app being suspended and resumed, and how to implement contracts that tie your app into the wider user experience that Windows 8 offers. Along the way, I’ll demonstrate the use of the geolocation feature and show you how to set up and manage a recurring asynchronous task. Table 5-1 provides the summary for this chapter.

***Table 5-1.** Chapter Summary*

**Problem**

**Solution**

**Listing**

Ensure that your app

Subscribe to events from the

1

receives the suspending

Windows.UI.WebUI.WebUIApplication

and resuming events.

object.

Create a recurring

Use the WinJS.Promise object as a wrapper

2–4

background task.

around other asynchronous activities.

Request more time before

Call the suspendingOperation.getDeferral

5

your app is suspended.

method on the event passed to your

suspending handler function.

Implement a contract.

Declare the contract in the manifest

6–8

and respond to the type information

in the activation event.

Dealing with the App Life Cycle

In Chapter 1 , I showed you the skeletal code that Visual Studio placed into the default.js file to give me a jump-start with my example project. This code handles the Windows 8 application *life-cycle events* , ensuring that I can respond appropriately to the signals that the operating system is sending me. There are three key stages in the life of a Windows 8 app.

The first stage, *activation* , occurs when Windows wants your app to perform some task. The most common task is when Windows wants to launch your app and display it to the user, but there are other tasks as well, and you’ll see one example when I show you how to implement a *contract* later in this chapter.

103

[www.it-ebooks.info](http://www.it-ebooks.info/)

CHAPTER 5 ■ LIFE-CYCLE EVENTS

Users don’t close Windows 8 apps; they just move to another application and leave Windows to sort things out. This is why there are no close buttons or menu bars on a Windows 8 UI. A Windows 8 app that is no longer required is moved into the second stage and is *suspended* . While suspended, no execution of the app code takes place, and there is no interaction with the user.

If the user switches back to a suspended app, then the third stage occurs: the application is *resumed* . The app is displayed to the user once execution continues.

Suspended applications are not always restored. If the device is low on memory, for example, Windows may simply *terminate* a suspended app.

Correcting the Visual Studio Event Code

The code that Visual Studio adds to a new project is sufficient to get a basic app up and running, but it doesn’t support the full range of life-cycle events. It deals with activation and suspension quite happily, but it prevents the application from being notified when it is being resumed. Fortunately, there are other places in the API where I can register to receive the life-cycle events, so my first task in this chapter is to update default.js so that I am properly notified when my app enters all three life-cycle stages. You can see the changes in Listing 5-1.

***Listing 5-1.*** Registering for Life-Cycle Event Notifications (function () {

"use strict";

Windows.UI.WebUI.WebUIApplication.addEventListener("activated", performInitialSetup);

Windows.UI.WebUI.WebUIApplication.addEventListener("resuming", performResume);

Windows.UI.WebUI.WebUIApplication.addEventListener("suspending", performSuspend);

function performInitialSetup(e) {

WinJS.UI.processAll().then(function () {

UI.List.displayListItems();

UI.List.setupListEvents();

UI.AppBar.setupButtons();

UI.Flyouts.setupAddItemFlyout();

ViewModel.State.bind("selectedItemIndex", function (newValue) {

WinJS.Utilities.empty(itemDetailTarget)

var url = newValue == -1 ? "/html/noSelection.html" :

"/pages/itemDetail/itemDetail.html"

WinJS.UI.Pages.render(url, itemDetailTarget);

});

WinJS.UI.Pages.render("/html/storeDetail.html", storeDetailTarget); 104

[www.it-ebooks.info](http://www.it-ebooks.info/)

CHAPTER 5 ■ LIFE-CYCLE EVENTS

function setOrientationClass() {

if (Windows.UI.ViewManagement.ApplicationView.value ==

Windows.UI.ViewManagement.ApplicationViewState

.fullScreenPortrait) {

WinJS.Utilities.addClass(contentGrid, "flex");

} else {

WinJS.Utilities.removeClass(contentGrid, "flex");

}

};

window.addEventListener("resize", setOrientationClass); setOrientationClass();

unsnapButton.addEventListener("click", function (e) {

Windows.UI.ViewManagement.ApplicationView.tryUnsnap();

});

Tiles.sendTileUpdate();

Tiles.sendBadgeUpdate();

});

}

function performResume(e) {

WinJS.Utilities.query("#topRightContainer h1")[0].innerText = "Resumed";

}

function performSuspend(e) {

WinJS.Utilities.query("#leftContainer h1")[0].innerText = "Suspended";

}

})();

I have used the events provided through the Windows.UI.WebUI.WebUIApplication class, and they neatly map to the life-cycle events. My example Windows 8 app doesn’t currently perform any tasks that are affected by the application being suspended and resumed, but I want to show you how to test for the events. To that end, I have added statements to the performResume and performSuspend functions that change the value of h1 elements in the HTML document to indicate when the suspending and resuming events are received.

Visual Studio contains support for simulating the suspending and resuming events in the debugger, but I suggest that you learn how to trigger these events directly in the operating system so that you can test without needing to use Visual Studio.

The problem is that Visual Studio has to disable the real operating system events so that the Windows 8 app process remains under its control at all times. This means that Visual Studio can create a slightly different experience from what the user will see when they use your app.

Unfortunately, when you work without Visual Studio, you lose access to helpful tools like the JavaScript Console window and, of course, the debugger itself. You have to find an alternative mechanism for observing changes in the app lifecycle, which is why I handle 105

[www.it-ebooks.info](http://www.it-ebooks.info/)

![](index-118_1.jpg)

CHAPTER 5 ■ LIFE-CYCLE EVENTS

the resuming and suspending events by changing the text displayed by h1 elements in the layout, like this:

...

WinJS.Utilities.query("#leftContainer h1")[0].innerText = "Suspended";

...

By using the layout to record receipt life cycle events, I am able to ensure that my app is getting the events I expect. Now that I have fixed the event-handling code in the default.js file and added support for recording the receipt of the events by changing the layout of the app, I will show you the different ways you can generate the events, through Visual Studio and by using Windows 8 directly.

Simulating Life-Cycle Events Using Visual Studio

To use Visual Studio to simulate the life-cycle events, start the app by selecting Start Debugging from the Debug menu. Visual Studio will display a pop-up menu on the toolbar, as shown in Figure 5-1 , that has options to suspend and resume the app; these options send the suspending and resuming events to the app. There is also an option that will send the suspending event and then terminate the app (this will actually terminate the app and not just simulate the effect).

***Figure 5-1 .** Using Visual Studio to simulate life-cycle events* **T**

■ **ip** For some editions of Visual Studio, the toolbar menu won’t appear until you select the Debug Location option on the View ➤ Toolbars menu.

To test the Visual Studio support for the life-cycle events, start the app and then select the Suspend item from the menu. The app will be sent the suspending event and will no longer be displayed. Wait a moment and then select Resume from the Visual Studio menu; the app will reappear, and you will see that the h1 elements along the top edge of the screen have been updated to reflect the fact that the suspending and resuming events have been received, as shown in Figure 5-2 .

106

[www.it-ebooks.info](http://www.it-ebooks.info/)

![](index-119_1.jpg)

CHAPTER 5 ■ LIFE-CYCLE EVENTS

***Figure 5-2 .** Using the elements in the app layout to record life-cycle events* Triggering Life-Cycle Events

In the sections that follow, I show you how to trigger the life-cycle events without using Visual Studio. It is important that you test your app behavior using the real events and not just rely on the simulated life cycle that Visual Studio supports.

Activate the Application

To trigger the activated event, start the application by selecting Start Without Debugging from the Visual Studio Debug menu. You can also start the app from the Start screen, either in the simulator or on your local machine. The important thing is not to use the debugger to start the app.

Suspend the Application

The easiest way to suspend the application is to switch to the desktop by pressing Win+D.

Open the Task Manager, right-click the item for your Windows 8 app, and select Go to Details from the pop-up menu. The Task Manager will switch to the Details tab. (You may have to click the More Details button before you can select the Details tab.) On the Details tab, you will see one or more WWAHost.exe processes, each of which corresponds to a Windows 8 app. You can tell which one is the example app because after a few seconds, the value shown in the Status column will change from Running to Suspended , which tells you that Windows has suspended the app, as shown in Figure 5-3 .

107

[www.it-ebooks.info](http://www.it-ebooks.info/)

![](index-120_1.png)

CHAPTER 5 ■ LIFE-CYCLE EVENTS

***Figure 5-3 .** The Windows Task Manager showing a suspended Windows 8 app* Resuming the Application

Switching back to the application will resume it. You will see that the h1 elements displayed at the top of the layout show that both the resuming and suspending events were sent by Windows.

The state of a resumed application is exactly as it was at the moment it was suspended. Your layout, data, event handlers, and everything else will be just as it was.

You don’t have to call processAll when handling the resuming event, for example.

Your application could have been suspended for a long time, especially if the device was put into a low-power state (such as sleeping). Network connections will have been closed by any servers you were talking to (which is why you should close them explicitly when you get the suspending event) and will have to be reopened when your application is resumed. You will also have to refresh data that may have become stale. This includes location data, since the device may have been moved during the period your app was suspended.

Adding an Asynchronous Activity

Now that I have confirmed that my app can get and respond to the resuming and suspending events, I can add some functionality that requires a recurring task. For this example, I am going to use the geolocation service to report on the current device location. To do this, I have created a new JavaScript file called location.js , the contents of which are shown in Listing 5-2.

***Listing 5-2.*** Tracking the Device Location

/// <reference path="//Microsoft.WinJS.1.0/js/base.js" />

/// <reference path="//Microsoft.WinJS.1.0/js/ui.js" /> 108

[www.it-ebooks.info](http://www.it-ebooks.info/)

CHAPTER 5 ■ LIFE-CYCLE EVENTS

(function () {

"use strict";

var currentPromise;

var tracking = false;

function trackLocation() {

currentPromise = new WinJS.Promise(function (complete) {

var geo = new Windows.Devices.Geolocation.Geolocator(); if (geo) {

geo.getGeopositionAsync().then(function (position) {

WinJS.xhr({

url: "http://nominatim.openstreetmap.org"

+ "/reverse?format=json&lat="

+ position.coordinate.latitude

+ "&lon=" + position.coordinate.longitude

}).then(function (data) {

var dataObject = JSON.parse(data.response);

if (dataObject.address.road) {

var date = new Date();

var time = date.getHours() + ":" + date.getMinutes()

+ ":" + date.getSeconds();

document.getElementById("geo").innerText =

dataObject.address.road + " (" + time + ")";

}

});

});

}

complete();

});

currentPromise.then(function () {

if (tracking) {

setTimeout(trackLocation, 5000);

}

});

}

WinJS.Namespace.define("Location", {

startTracking: function () {

tracking = true;

trackLocation();

},

109

[www.it-ebooks.info](http://www.it-ebooks.info/)

CHAPTER 5 ■ LIFE-CYCLE EVENTS

stopTracking: function () {

tracking = false;

return currentPromise;

}

});

})();

Using Location Tracking

The Windows 8 geolocation service is available through the Windows.Devices.

Geolocation.Geolocator object. You can subscribe to receive events when the location information changes, but I want to demonstrate a repeating background task, so I have used the getGeopositionAsync method, which produces a snapshot of the current location. This is an asynchronous operation, and so the getGeopositionAsync method returns a WinJS.Promise object that is fulfilled when the location information is available.

When I get the location data, I make an Ajax call using the WinJS.xhr object, which is a Promise wrapper around the standard browser XmlHttpRequest object. My Ajax request is to a reverse geocoding service, which allows me to translate the latitude and longitude information from the geolocation service into a street address. The geocoding service returns a JSON string, which I parse into a JavaScript object from which I read the street so that I can display it to the user, along with a timestamp indicating the last location update.

**T**

■ **ip** I have used the OpenStreetMap geocoding service because it doesn’t require a unique account token. This means you can run the example without having to create a Google Maps or Bing Maps account.

I have added some simple elements to the default.html file so that I can display the location to the user. You can see the additions in Listing 5-3.

***Listing 5-3.*** Adding Elements to default.html to Display Location Information

...

<script src="/js/tiles.js"></script>

<script src="/js/location.js"></script>

<script src="/js/default.js"></script>

</head>

<body>

<div class="midtitle">

<h2>Your location is: <span id="geo"></span></h2>

</div>

<div id="contentGrid">

<div id="leftContainer" class="gridLeft">

<h1 class="win-type-xx-large">Grocery List</h1>

...

110

[www.it-ebooks.info](http://www.it-ebooks.info/)

CHAPTER 5 ■ LIFE-CYCLE EVENTS

**T**

■ **ip** One of the pleasures of working with Windows 8 using HTML5 and JavaScript is that you can choose between the HTML5 and Windows APIs for some key areas of functionality, including geolocation. I have used the Windows API in this example, but the HTML5 equivalent would have worked just as well.

To manage the layout of the new elements, I have added new properties to the

/css/default.css file, as shown in Listing 5-4.

***Listing 5-4.*** Adding New Properties to the default.css File

...

div.midtitle {

text-align: center;

margin: 20px;

padding-bottom: 5px;

border-bottom: thin solid white;

}

...

Controlling the Task

I am describing the geolocation and Ajax requests only in passing because the point of this example is the creation of a periodically repeating background task. It doesn’t really matter what the task does. The most important part of the location.js file is the startTracking and stopTracking functions. When I call the startTracking function, I create a new Promise object that represents the overall background task.

■ **Tip** I have used the Promise object as a wrapper around the two Promise objects representing the request for location data and the subsequent Ajax request. When both my inner Promise objects have finished, I call the complete function, which is passed to the callback function I used when I created the Promise object. See the API reference for more information about the WinJS.Promise object.

Each time the request completes, I create another Promise encapsulating a new request, repeating as long as the tracking variable is true . I start a new request cycle every five seconds.

When I call the stopTracking function, I set the tracking variable to false and return the Promise representing the current request cycle. The Promise I return represents a request cycle in one of two states. The first state is when the request is active, meaning that I am waiting for the geolocation data or the Ajax request to complete, or I am applying an update to the DOM. If you call the then method on an active Promise , the 111

[www.it-ebooks.info](http://www.it-ebooks.info/)

CHAPTER 5 ■ LIFE-CYCLE EVENTS

callback function won’t be executed until the cycle is complete. The second state is when the request is complete and I am in the lull before the next cycle starts. Calling the then method on a completed Promise will cause the callback function to be executed without delay.

With this in mind, I am able to integrate my background task into default.js using the handlers for the different life-cycle events, as shown in Listing 5-5.

***Listing 5-5.*** Using the Life-Cycle Events to Control a Background Task (function () {

"use strict";

Windows.UI.WebUI.WebUIApplication.addEventListener("activated", performInitialSetup);

Windows.UI.WebUI.WebUIApplication.addEventListener("resuming", performResume);

Windows.UI.WebUI.WebUIApplication.addEventListener("suspending", performSuspend);

function performInitialSetup(e) {

WinJS.UI.processAll().then(function () {

UI.List.displayListItems();

UI.List.setupListEvents();

UI.AppBar.setupButtons();

UI.Flyouts.setupAddItemFlyout();

ViewModel.State.bind("selectedItemIndex", function (newValue) {

var targetElement = document.getElementById("itemDetailTarget"); WinJS.Utilities.empty(targetElement)

var url = newValue == -1 ? "/html/noSelection.html"

: "/pages/itemDetail/itemDetail.html"

WinJS.UI.Pages.render(url, targetElement);

});

WinJS.UI.Pages.render("/html/storeDetail.html", document.getElementById("storeDetailTarget"));

//Tiles.sendTileUpdate();

//Tiles.sendBadgeUpdate();

Location.startTracking();

});

}

function performResume(e) {

Location.startTracking();

}

112

[www.it-ebooks.info](http://www.it-ebooks.info/)

CHAPTER 5 ■ LIFE-CYCLE EVENTS

function performSuspend(e) {

var promise = Location.stopTracking();

if (promise) {

var deferral = e.suspendingOperation.getDeferral();

promise.then(function () {

deferral.complete();

});

}

}

})();

The changes for the activated and resuming events are simple: in both cases I want to start my background task, so I just have to call the Location.startTracking method. The interesting part of this listing, and the reason that I included the example in this chapter, is how I handle the suspending event.

**T**

■ **ip** Notice I have commented out the lines that apply the tile and badge updates. This is so that the example app will run in the simulator. I have also disabled the event handlers in the tiles.js file.

My problem is that any background task that is active when Windows suspends my app automatically carries on when the app is resumed. Depending on where in the request cycle the task was suspended, I can expect to see an error (for example, trying to read data from a network request that timed out during suspension) or stale data (because my task was just about to update the DOM when the app was suspended).

To help work around these problems, the suspending event defines a property called suspendingOperation that returns a Windows.ApplicationModel.SuspendingOperation object. Calling this object’s getDeferral method asks Windows to give your app a little more time to prepare for suspension. When you have finished winding up your background tasks, you call the complete method on the object that the getDeferral method returned, signaling to Windows that your app is now ready to be suspended.

Asking for a deferral grants an extra five seconds to prepare for suspension. This may not sound like a lot, but it is pretty generous given that Window may be under a lot of pressure to get your app out of the way to make system resources available.

**Caution**

■

Windows will terminate an app that doesn’t call the complete method on the deferral object within the five-second allowance.

113

[www.it-ebooks.info](http://www.it-ebooks.info/)

![](index-126_1.png)

CHAPTER 5 ■ LIFE-CYCLE EVENTS

Declaring the Location Capability

Apps must declare their need to access the location service in their manifest. Before running the updated app, open package.appxmanifest , switch to the Capabilities tab, ensure that the Location capability is checked, and save the file, as shown in Figure 5-4 .

***Figure 5-4 .** Declaring the Location Capability in the manifest* Testing the Background Task

All that remains is to test that the background task is meshing properly with the life-cycle events. The easiest way to do this is with the simulator, which supports simulated location data. When you first start the example after making the changes in the preceding sections, you will be prompted to give the app access to location data, as shown in Figure 5-5 . Click the Allow button so that the code in the example will work properly.

114

[www.it-ebooks.info](http://www.it-ebooks.info/)

![](index-127_1.jpg)

![](index-127_2.jpg)

CHAPTER 5 ■ LIFE-CYCLE EVENTS

***Figure 5-5 .** Granting access to location data* **T**

■ **ip** You may also have to grant permission for the simulator to access the location data of your development PC. There is an automated process that checks the required settings and prompts you to make the required changes to your system configuration.

The simulator will try to use the location information from your development PC, but for this example I want to use a simulated location so that I can demonstrate the effect of the device moving while the app is suspended. You can enter a location by clicking the simulator Set Location button and filling in the form fields, as shown in Figure 5-6 .

***Figure 5-6 .** Setting a simulated location in the Visual Studio simulator* I have used the coordinates of the Empire State Building for this example, which has a latitude value of 40.748 and a longitude of -73.98\. Click the Set Location button to change the location reported by the simulator to apps.

115

[www.it-ebooks.info](http://www.it-ebooks.info/)

![](index-128_1.jpg)

![](index-128_2.jpg)

CHAPTER 5 ■ LIFE-CYCLE EVENTS

After a moment, you see the new location displayed at the top of the app layout.

For the coordinates I entered into the Set Location dialog, the web service reports the street address as Park Avenue, which you can see in Figure 5-7 along with the timestamp indicating when the location was last updated.

***Figure 5-7 .** The street address of the current location displayed by the app* The next step is to suspend the app, either by using the Visual Studio simulated events or by putting the app into the background. While the app is suspended, change the location and then resume the app. For this example, I have changed the simulated latitude to 51.50 and the longitude of -0.14 (these are the coordinates of Buckingham Palace).

Resume the app, and you will see that the background task is restarted in response to the resuming event, updating the layout with the new location data and fresh timestamps, as shown in Figure 5-8 .

***Figure 5-8 .** The modified street address displayed by the app* Implementing the Search Contract

The suspending and resuming events are important, but I want to return to the activated event and show you how it can be used to get tighter integration between your app and the rest of Windows 8\. To do this, I am going to implement a *contract* , which is how Windows 8 expresses some key aspects of the user experience to apps. I am going to implement the search contract, which tells Windows that my example application is capable of supporting the operating-system-wide search mechanism. In the sections that follow, I’ll show you how to declare support for the contract and implement the contract terms.

Declaring Support for the Contract

The first step toward implementing a contract is to update the manifest. Open the package.appxmanifest file, and switch to the Declarations tab. If you open the Available Declarations menu, you will see the lists of contracts that you can declare support for.

Select Search and click the Add button. The Search contract will appear on the Supported Declarations list, as shown in Figure 5-9 . Ignore the properties for the contract; they don’t do anything for JavaScript apps.

116

[www.it-ebooks.info](http://www.it-ebooks.info/)

![](index-129_1.png)

CHAPTER 5 ■ LIFE-CYCLE EVENTS

***Figure 5-9 .** Declaring support for the search contract* Handling the Search

The purpose of the search contract is to connect the operating system search system with some kind of search capability within your application. For my example app, I am going to handle search requests by iterating through the items on the grocery list and finding the first one that contains the string the user is looking for. This won’t be the most sophisticated search implementation, but it will let me focus on the contract without getting bogged down in creating lots of new code to handle searches. I have added a new JavaScript file to the project called search.js , the contents of which you can see in Listing 5-6.

117

[www.it-ebooks.info](http://www.it-ebooks.info/)

CHAPTER 5 ■ LIFE-CYCLE EVENTS

***Listing 5-6.*** Implementing a Basic Search Feature

/// <reference path="//Microsoft.WinJS.1.0/js/base.js" />

/// <reference path="//Microsoft.WinJS.1.0/js/ui.js" /> (function () {

"use strict";

WinJS.Namespace.define("Search", {

searchAndSelect: function (searchTerm) {

var searchTerm = searchTerm.toLowerCase();

var items = ViewModel.UserData.getItems();

var matchedIndex = -1;

for (var i = 0 ; i < items.length; i++) {

if (items.getAt(i).item.toLowerCase()

.indexOf(searchTerm) > -1) {

matchedIndex = i;

break;

}

}

ViewModel.State.selectedItemIndex = matchedIndex;

}

});

})();

In this file, I have defined a namespace called Search that contains the searchAndSelect function. This function accepts the term that the user is searching for and performs a basic case-insensitive search through the items in the view model. If there is a match, then I set the selectedItemIndex property in the view model, which, through the magic of binding, will cause the matched item to be highlighted and for its details to be displayed. To bring this code into the app, I have added a script element to the head section of the default.html file, as shown in Listing 5-7.

***Listing 5-7.*** Adding a script Element to the default.html File for the search.js File

...

<head>

<meta charset="utf-8">

<title>MetroGrocer</title>

<!-- WinJS references -->

<link href="//Microsoft.WinJS.1.0/css/ui-dark.css" rel="stylesheet">

<script src="//Microsoft.WinJS.1.0/js/base.js"></script>

<script src="//Microsoft.WinJS.1.0/js/ui.js"></script>

<!-- MetroGrocer references -->

<link href="/css/list.css" rel="stylesheet">

<link href="/css/flyout.css" rel="stylesheet">

<link href="/css/default.css" rel="stylesheet"> 118

[www.it-ebooks.info](http://www.it-ebooks.info/)

CHAPTER 5 ■ LIFE-CYCLE EVENTS

<script src="/js/viewmodel.js"></script>

<script src="/js/ui.js"></script>

<script src="/js/pages.js"></script>

<script src="/js/tiles.js"></script>

<script src="/js/location.js"></script>

<script src="/js/search.js"></script>

<script src="/js/default.js"></script>

</head>

...

Implementing the Activated Event Handler

As I mentioned earlier, the activated event is used by Windows to signal a range of instructions. Previously, I treated all activated events as an instruction to launch the app, but in order to support the search contract, I have to check the activated event to figure out what my app is being asked to do. You can see how I have updated my handler for the activated event in the /js/default.js file in Listing 5-8.

***Listing 5-8.*** Determining the Activation Detail in an Event (function () {

"use strict";

Windows.UI.WebUI.WebUIApplication.addEventListener("activated", function (e) {

var actNS = Windows.ApplicationModel.Activation;

if (e.previousExecutionState !=

actNS.ApplicationExecutionState.suspended) {

performInitialSetup(e);

}

if (e.kind == actNS.ActivationKind.search) {

Search.searchAndSelect(e.queryText);

}

}

);

Windows.UI.WebUI.WebUIApplication.addEventListener("resuming", performResume);

Windows.UI.WebUI.WebUIApplication.addEventListener("suspending", performSuspend);

function performInitialSetup(e) {

WinJS.UI.processAll().then(function () {

UI.List.displayListItems();

UI.List.setupListEvents();

UI.AppBar.setupButtons();

UI.Flyouts.setupAddItemFlyout();

119

[www.it-ebooks.info](http://www.it-ebooks.info/)

CHAPTER 5 ■ LIFE-CYCLE EVENTS

ViewModel.State.bind("selectedItemIndex", function (newValue) {

WinJS.Utilities.empty(itemDetailTarget)

var url = newValue == -1 ? "/html/noSelection.html" :

"/pages/itemDetail/itemDetail.html"

WinJS.UI.Pages.render(url, itemDetailTarget);

});

WinJS.UI.Pages.render("/html/storeDetail.html", storeDetailTarget); function setOrientationClass() {

if (Windows.UI.ViewManagement.ApplicationView.value ==

Windows.UI.ViewManagement.ApplicationViewState

.fullScreenPortrait) {

WinJS.Utilities.addClass(contentGrid, "flex");

} else {

WinJS.Utilities.removeClass(contentGrid, "flex");

}

};

window.addEventListener("resize", setOrientationClass); setOrientationClass();

unsnapButton.addEventListener("click", function (e) {

Windows.UI.ViewManagement.ApplicationView.tryUnsnap();

});

//Tiles.sendTileUpdate();

//Tiles.sendBadgeUpdate();

Location.startTracking();

});

}

function performResume(e) {

Location.startTracking();

}

function performSuspend(e) {

var promise = Location.stopTracking();

if (promise) {

var deferral = e.suspendingOperation.getDeferral();

promise.then(function () {

deferral.complete();

});

}

}

})();

120

[www.it-ebooks.info](http://www.it-ebooks.info/)

CHAPTER 5 ■ LIFE-CYCLE EVENTS

Windows provides information about why your application has been sent the activated event through the kind property of the Event object passed to your handler function. The range of possible values is enumerated by Windows.ApplicationModel.

Activation.ActivationKind . There are different kinds of activation for different contracts for printing, for opening a file, for working with a device camera, and, of course, for handling search requests. By checking the value of the kind property, I can discover whether the activated event has been sent for a search request:

...

if (e.kind == actNS.ActivationKind.search) {

Search.searchAndSelect(e.queryText);

}

...

The search term that the user has provided is available through the event’s queryText property. If my activated event has been sent for a search request, then I call the searchAndSelect function, passing in the queryText value. This has the effect of locating and selecting the first matching item in the grocery list.

Ensuring Application Setup

My technique for servicing search requests relies on the performInitialSetup function having been called before I call searchAndSelect , because I rely on the view model and its associated bindings to translate a change in the selectedItemIndex property into changes in the display.

The problem is that the search-related activated event can have two meanings. If my app is already running, the search activated event means just “perform this search.”

But, if my app isn’t running, then the event means “start the app *and* perform this search.”

Working out which meaning I am dealing with is important. You get only one activated event, even if your app isn’t running when the user initiated the search.

I create duplicate bindings and event handlers if I call performInitialSetup when my app is already running. All sorts of odd behaviors emerge when this happens, but the most visible problem is that the right column in the layout contains two sets of content because I end up calling the WinJS.Pages.render method too many times. By contrast, if I fail to call performInitialSetup when my app wasn’t running, then I don’t have any bindings and event handlers, and I don’t display any content at all.

I need to be careful to call the performInitialSetup function when my app hasn’t been running and avoid calling it the function if it has. I do this by looking at the previousExecutionState property of the event passed to the activated handler function, which reports on the execution state of my app just before the activated event was sent. The range of values for this property is enumerated by Windows.

ApplicationModel.Activation.ApplicationExecutionState , but the only value I care 121

[www.it-ebooks.info](http://www.it-ebooks.info/)

![](index-134_1.jpg)

CHAPTER 5 ■ LIFE-CYCLE EVENTS

about to solve this problem is suspended , which tells me that the app setup has already been performed:

...

if (e.previousExecutionState != actNS.ApplicationExecutionState.suspended) {

performInitialSetup(e);

}

...

Testing the Search Contract

To test the contract, start your app. It doesn’t matter if you start it with or without the debugger, and it doesn’t matter if you exit or suspend the app after you start it. The key thing is to make sure it is present on the simulator.

Next, switch to the Start screen. Your app, if it was still running, will be switched to the background and, after a few seconds, will be suspended. To begin a search, just start typing. You want to search for something that will make a match, so type *hot* (so that your search will match against the hot dogs item in the grocery list).

As you type, Windows will automatically begin a standard search, looking for applications whose names contain *hot* . You will see something similar to Figure 5-10 , since there are no such apps in the default Windows 8 installation.

***Figure 5-10 .** Initiating a search*

To switch to an application-specific search, click the app’s name on the list below the search box. This will send the search activated event to the app. For my example app, I have clicked the MetroGrocer item in the list, and the activated event triggers my simple search handler, the result of which is shown in Figure 5-11 .

122

[www.it-ebooks.info](http://www.it-ebooks.info/)

![](index-135_1.jpg)

CHAPTER 5 ■ LIFE-CYCLE EVENTS

***Figure 5-11 .** Performing an application-specific search* **T**

■ **ip** If you start a search from the charms bar while your app is being displayed, then Windows will automatically select it from the list, focusing the initial search on your app.

Summary

In this chapter, I showed you how to use the life-cycle events to respond to the way in which Windows manages apps. I described each of the key events and showed you how to use the app layout to ensure that your app is receiving and processing them correctly.

Particular care must be taken to cleanly wrap up background tasks when an app is being suspended, and I showed you how to get control of this process by requesting a suspension deferral, allowing an extra few seconds to minimize the risk of potential errors or stale data when the app is resumed.

Finally, I showed you how the activated event is used to signal different requests, including obligations to fulfill the contracts that bind an app to the wider Windows platform. I showed you the search contract, but there are several others, and I recommend you take the time to explore them fully. The more contracts you implement, the more integrated your app becomes.

In this short book, I set out to show you the core system features that will jump-start your Windows 8 app development. I have shown you how to use data bindings, how to use the major structural controls, how to deal with snapped and filled layouts, how to customize your application’s tile, and, in this chapter, how to take control of the application life cycle. With these skills as your foundation, you will be able to create rich and expressive apps and get a head start on Windows 8 development.

I wish you every success in your Windows 8 app development projects.

123

[www.it-ebooks.info](http://www.it-ebooks.info/)

**Index**

**„ **A, B, C****

 **UI namespaces , 57

wiring up , 57

Application controls , 47

HTML document , 62–66

AppBar , 48–49, 51–53

code execution , 66

appearance , 51

complete path , 64

attributes , 51

contents of css fi le , 63

button implement , 52

edit list , 66

data-win-control , 49

itemDetail.html , 62

defi ne , 49

page callback , 63

HTML fragment , 49

script element , 64

icon property , 52

switching, pages , 65

regions , 51

URL argument , 65

setting support , 52

view model, switching , 65

setupButtons functions , 53

pages , 58–62

WinJS.UI.processAll method , 51

associate a callback , 62

application infrastructure , 47

callback function , 60

external content , 66–70

color property , 62

callback , 67

defi ne , 59

display , 70

HTML , 59

display page , 69

JavaScript callback , 59

layout element , 69

load and display, HTML , 61

manifest permission , 70

loading fragment , 59

page, callback , 67

noSelection.html , 59

property , 68

Placeholder element , 61

show, page , 68

ready callback , 60

storeDetail.html , 66

statements , 61

Flyouts , 57–58

WinJS load , 61

addItemFlyout.html , 53

WinJS controls , 47

with AppBar , 55

Asynchronous Module Defi nition

associate with AppBar , 55

(AMD) , 24

button, AppBar , 56

code to set up , 58

**„ **D, E, F, G, H, I, J, K****

 **content import , 56

contents of css fi le , 54

Data bindings , 21

css to html , 55

design patterns , 21

defi ne , 54

JavaScript fi le creation , 22–25

div element , 54

AMD technique , 24

select eff ect , 58

code completion , 23

125

[www.it-ebooks.info](http://www.it-ebooks.info/)

■ INDEX

Data bindings ( *cont.* )

parts, declarative , 28

global namespace pollution ,

placeholder functions , 28

24–25

processing , 28

method and properties,

query method , 33

namespace , 24

simple binding , 27

namespace creation , 24

updating data item , 32

self-executing functions , 25

value update , 34

strict mode , 25

view model , 29, 32

view model , 22, 25

zip code , 34

JavaScript functionality , 21

MVC , 21

**„ **L****

 **MVVC , 21

observable arrays , 35–37

Layouts and tiles

bridge, HTML and WinJS , 37

applying badges , 98–101

details, list objects , 37

attribute , 99

list class , 35

selecting templates , 99

list methods , 36

support , 98

list objects , 36

templates , 99

templates , 38–44

on a tile , 101

add, ui.js fi le , 44

types, template , 99

adding to HTML , 38

update , 100

applying to apps , 42

value attribute , 99

CSS fi les , 44

and badges , 86–88, 90–98

data-win-control , 39

application setup , 93

empty method , 40

apply, update , 92

importing JavaScript and CSS , 44

apply images , 88

list change response , 41

compose XML format , 95

processing elements , 43

creating updates , 92

promise pattern , 43

debug , 94

render method , 41

fi nal updated tile , 95

selectedItemIndex , 41

images to folder , 88

setupListEvents , 41

importNode method , 97

styling , 43

.js to .html , 93

table elements , 39

manifest fi elds , 89

table rows and list objects , 45

square and wide production , 96

testing , 44

static tiles , 87

tracking item , 41

templates , XML, 92

ui.js fi le , 40

testing , 94

UI namespace , 40

tiles.js fi le , 91

view model , 21

tiles updating , 90

WinJS.Binding , 26–35

updated static tile , 90

combining namespaces , 31

wide tiles , 95, 98

declarative binding , 27

XML fragment , 90

dynamic creation , 29

CSS3 fl exbox layouts , 82

dynamic methods , 31

snap and fi ll , 73

event changing , 33

Windows 8 orientation and

global object , 32

view , 74–75, 77–81, 83–84

handler function , 34

adapting , 77

id attributes , 34

add JavaScript , 81

knockout methods , 35

app layout , 79

observable item , 30

change simulation , 74

126

[www.it-ebooks.info](http://www.it-ebooks.info/)

■ INDEX

code to exit , 85

suspend , 104, 107

CSS, adapt , 77

task control , 111

default.js fi le changes , 79

terminate , 104

default view , 74

trigger , 107

elements addition , 83

Visual Studio simulation , 106

exit, snapped view , 83

Windows 8 app , 108

fi lled view , 76

search contract , 116–119, 121–122

JavaScript adapting , 79

application setup , 121

media queries , 77

application specifi c , 123

new styles, button , 84

basic feature , 118

portrait orientation , 81

detail, activation , 119

setOrientationClass , 80

event handler, implement , 119

show, snapped view , 85

function, events , 121

simulation and change , 74

handling , 117

snapped view , 76

initiating , 122

styles , 78

namespace , 118

tryUnsnap , 86

performInitialSetup , 121

view simulation , 75

script element , 118

visibility property , 84

support declaration , 116

Visual Studio simulator

testing , 122

orientation , 75

Life cycle

App dealing , 103–108, 110–116

**„ **M, N, O, P, Q, R, S, T,****

 **access location , 115

**U, V, W, X, Y, Z**

activation , 103, 107

asynchronous activity , 108

Microsoft Windows 8

background task, control , 112

API’s , 9

code correction , 104

application controls , 3

css properties , 111

code fragment , 5

declaring location , 114

data and bindings , 3

display, location , 116

features , 3

event notifi cations , 104

HTML5 , 2

geocoding , 110

JavaScript start and debug ,

getDeferral method , 113

16–19

getGeopositionAsync method , 110

changes, simulator , 19

information location , 110

controls, restart an reload , 18

layout , 106

CSS change , 18

location simulator , 115

debugging apps , 19

location tracking , 108

HTML change , 18

manifest, location , 114

metro simulator , 17

method, tracking , 113

reload , 17

modifi ed location , 116

simulator , 16

OS events , 105

key techniques , 3

perform statements , 105

layouts and tiles , 4

promise wrapper , 110

life cycle events , 4

record, layout , 107

MetroGrocer , 4

resume , 104, 108

projects , 8–16

simulation , 105

blank, solution explorer , 8

skeletal code , 103

contents, HTML fi le , 9

startTracking , 111

creation , 6

stopTracking , 111

CSS3 grid layouts , 13

127

[www.it-ebooks.info](http://www.it-ebooks.info/)

■ INDEX

Microsoft Windows 8 ( *cont.* )

.js fi le contents , 14

CSS fi le contents , 12

revised contents, HTML fi le , 10

default.js fi le , 14

Visual Studio , 9

example project , 7

WinJS API , 16

exploring CSS fi le , 11

sample app , 5

HTML fi le exploring , 8

softwares needed , 2

JavaScript libraries , 15

Visual Studio Express , 2

jQuery , 16

in virtual machine , 2

jQuery UI and mobile , 16

WinJS library and code , 10

128

[www.it-ebooks.info](http://www.it-ebooks.info/)

# Document Outline

*   [Title Page](#index_split_000.html#p2)
*   [Copyright Page](#index_split_000.html#p3)
*   [Dedication Page](#index_split_000.html#p4)
*   [Contents at a Glance](#index_split_000.html#p5)
*   [Table of Contents](#index_split_000.html#p6)
*   [About the Author](#index_split_000.html#p10)
*   [About the Technical Reviewer](#index_split_000.html#p11)
*   [Acknowledgments](#index_split_000.html#p12)
*   [Introduction](#index_split_000.html#p13)
    *   [About This Book](#index_split_000.html#p13)
        *   [What Do You Need to Know Before You Read Th is Book?](#index_split_000.html#p13)
        *   [Do You Need to Know About HTML5?](#index_split_000.html#p14)
        *   [What Software Do You Need for Th is Book?](#index_split_000.html#p14)
        *   [What Is the Structure of Th is Book?](#index_split_000.html#p14)
            *   [Chapter 1: Getting Started](#index_split_000.html#p14)
            *   [Chapter 2: Data and Bindings](#index_split_000.html#p15)
            *   [Chapter 3: Application Controls](#index_split_000.html#p15)
            *   [Chapter 4: Layouts and Tiles](#index_split_000.html#p15)
            *   [Chapter 5: Life-Cycle Events](#index_split_000.html#p15)
*   [CHAPTER 1 Getting Started](#index_split_000.html#p16)
    *   [About This Book](#index_split_000.html#p16)
        *   [What Do You Need to Know Before You Read This Book?](#index_split_000.html#p16)
        *   [Do You Need to Know About HTML5?](#index_split_000.html#p17)
        *   [What Software Do You Need for This Book?](#index_split_000.html#p17)
        *   [What Is the Structure of This Book?](#index_split_000.html#p18)
            *   [Chapter 1: Getting Started](#index_split_000.html#p18)
            *   [Chapter 2: Data and Bindings](#index_split_000.html#p18)
            *   [Chapter 3: Application Controls](#index_split_000.html#p18)
            *   [Chapter 4: Layouts and Tiles](#index_split_000.html#p19)
            *   [Chapter 5: Life-Cycle Events](#index_split_000.html#p19)
        *   [Tell Me More About the Example Windows 8 App](#index_split_000.html#p19)
        *   [Is There a Lot of Code in This Book?](#index_split_000.html#p20)
    *   [Getting Up and Running](#index_split_000.html#p21)
        *   [Creating the Project](#index_split_000.html#p21)
        *   [Exploring the default.html File](#index_split_000.html#p23)
        *   [Exploring the default.css File](#index_split_000.html#p26)
        *   [Exploring the default.js File](#index_split_000.html#p29)
    *   [Starting and Debugging a JavaScript Windows 8 App](#index_split_000.html#p31)
        *   [Reloading the Windows 8 App](#index_split_000.html#p32)
        *   [Debugging Windows Store apps](#index_split_000.html#p34)
    *   [Summary](#index_split_000.html#p35)
*   [CHAPTER 2 Data and Bindings](#index_split_000.html#p36)
    *   [Creating the JavaScript File](#index_split_000.html#p37)
        *   [Using Code Completion](#index_split_000.html#p38)
        *   [Reducing Global Namespace Pollution](#index_split_000.html#p39)
            *   [Creating Namespaces](#index_split_000.html#p39)
            *   [Using Self-executing Functions](#index_split_000.html#p40)
            *   [Using Strict Mode](#index_split_000.html#p40)
        *   [Returning to the View Model](#index_split_000.html#p40)
    *   [Using Data Binding](#index_split_000.html#p41)
        *   [Using Basic Declarative Bindings](#index_split_000.html#p42)
        *   [Creating Dynamic Bindings](#index_split_000.html#p44)
            *   [Combining Namespaces with Observable Items](#index_split_000.html#p46)
        *   [Updating an Observable Data Item](#index_split_000.html#p47)
    *   [Creating Observable Arrays](#index_split_000.html#p50)
    *   [Using Templates](#index_split_000.html#p53)
        *   [Using the Template](#index_split_000.html#p54)
        *   [Responding to List Changes](#index_split_000.html#p56)
        *   [Tracking the Selected Item](#index_split_000.html#p56)
        *   [Applying the Template to the App](#index_split_000.html#p57)
            *   [Styling the Template Elements](#index_split_000.html#p58)
            *   [Importing the JavaScript and CSS](#index_split_000.html#p59)
            *   [Testing the Template](#index_split_000.html#p59)
    *   [Summary](#index_split_000.html#p60)
*   [CHAPTER 3 Application Controls](#index_split_000.html#p61)
    *   [Adding an AppBar](#index_split_000.html#p62)
        *   [Implementing App Bar Buttons](#index_split_000.html#p66)
    *   [Adding Flyouts](#index_split_000.html#p67)
        *   [Associating the Flyout with the AppBar Command](#index_split_000.html#p69)
        *   [Importing the Flyout Content](#index_split_000.html#p70)
        *   [Wiring Up the Controls in a Flyout](#index_split_000.html#p71)
    *   [Using Pages](#index_split_000.html#p72)
        *   [Defining the HTML](#index_split_000.html#p73)
        *   [Creating the JavaScript Callback](#index_split_000.html#p73)
        *   [Loading and Displaying the HTML](#index_split_000.html#p75)
    *   [Loading a Complete HTML Document](#index_split_001.html#p76)
        *   [Switching Between Pages](#index_split_001.html#p79)
    *   [Displaying External Content](#index_split_001.html#p80)
        *   [Adding the Callback](#index_split_001.html#p81)
        *   [Showing the Page](#index_split_001.html#p82)
        *   [Checking Manifest Permissions](#index_split_001.html#p84)
    *   [Summary](#index_split_001.html#p85)
*   [CHAPTER 4 Layouts and Tiles](#index_split_001.html#p86)
    *   [Dealing with Windows 8 Orientations and Views](#index_split_001.html#p87)
        *   [Simulating Orientation and View Changes](#index_split_001.html#p87)
            *   [Simulating Orientation Changes](#index_split_001.html#p87)
        *   [Simulating Views Changes](#index_split_001.html#p88)
        *   [Adapting to Orientation and View Changes](#index_split_001.html#p90)
            *   [Adapting with CSS](#index_split_001.html#p90)
            *   [Adapting with JavaScript](#index_split_001.html#p92)
        *   [Programmatically Exiting the Snapped View](#index_split_001.html#p96)
    *   [Using Tiles and Badges](#index_split_001.html#p99)
        *   [Improving Static Tiles](#index_split_001.html#p100)
        *   [Applying the Images](#index_split_001.html#p101)
        *   [Updating Tiles](#index_split_001.html#p103)
            *   [Populating the XML Template](#index_split_001.html#p105)
            *   [Creating the Tile Update](#index_split_001.html#p105)
            *   [Applying the Tile Update](#index_split_001.html#p105)
            *   [Testing the Tile Update](#index_split_001.html#p107)
        *   [Updating Wide Tiles](#index_split_001.html#p108)
    *   [Applying Badges](#index_split_001.html#p111)
    *   [Summary](#index_split_001.html#p114)
*   [CHAPTER 5 Life-Cycle Events](#index_split_001.html#p115)
    *   [Dealing with the App Life Cycle](#index_split_001.html#p115)
        *   [Correcting the Visual Studio Event Code](#index_split_001.html#p116)
        *   [Simulating Life-Cycle Events Using Visual Studio](#index_split_001.html#p118)
        *   [Triggering Life-Cycle Events](#index_split_001.html#p119)
            *   [Activate the Application](#index_split_001.html#p119)
            *   [Suspend the Application](#index_split_001.html#p119)
            *   [Resuming the Application](#index_split_001.html#p120)
        *   [Adding an Asynchronous Activity](#index_split_001.html#p120)
            *   [Using Location Tracking](#index_split_001.html#p122)
            *   [Controlling the Task](#index_split_001.html#p123)
            *   [Declaring the Location Capability](#index_split_001.html#p126)
            *   [Testing the Background Task](#index_split_001.html#p126)
    *   [Implementing the Search Contract](#index_split_001.html#p128)
        *   [Declaring Support for the Contract](#index_split_001.html#p128)
        *   [Handling the Search](#index_split_001.html#p129)
        *   [Implementing the Activated Event Handler](#index_split_001.html#p131)
            *   [Ensuring Application Setup](#index_split_001.html#p133)
        *   [Testing the Search Contract](#index_split_001.html#p134)
    *   [Summary](#index_split_001.html#p135)
*   [Index](#index_split_001.html#p136)**************************************