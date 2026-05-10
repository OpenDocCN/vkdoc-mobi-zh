![](images/979-8-8688-2323-7_CoverFigure.jpg)

ISBN 979-8-8688-2322-0e-ISBN 979-8-8688-2323-7 [https://doi.org/10.1007/979-8-8688-2323-7](https://doi.org/10.1007/979-8-8688-2323-7) © Charlie Cocchiaro 2026 This work is subject to copyright. All rights are solely and exclusively licensed by the Publisher, whether the whole or part of the material is concerned, specifically the rights of translation, reprinting, reuse of illustrations, recitation, broadcasting, reproduction on microfilms or in any other physical way, and transmission or information storage and retrieval, electronic adaptation, computer software, or by similar or dissimilar methodology now known or hereafter developed. The use of general descriptive names, registered names, trademarks, service marks, etc. in this publication does not imply, even in the absence of a specific statement, that such names are exempt from the relevant protective laws and regulations and therefore free for general use. The publisher, the authors and the editors are safe to assume that the advice and information in this book are believed to be true and accurate at the date of publication. Neither the publisher nor the authors or the editors give a warranty, expressed or implied, with respect to the material contained herein or for any errors or omissions that may have been made. The publisher remains neutral with regard to jurisdictional claims in published maps and institutional affiliations.

This Apress imprint is published by the registered company APress Media, LLC, part of Springer Nature.

The registered company address is: 1 New York Plaza, New York, NY 10004, U.S.A.

*To my wife Elaine, for inspiring me to do great things. To my parents Sam and Jean, for the many sacrifices they made so I could chase my dreams. To my brother Carl, for pushing me to strive for perfection. To my sister Celia and brother Chris, for their unwavering support.*

# Preface

The motivation for writing this book stemmed from the belief that software should be developed to maximize efforts, minimize redundancy, and promote innovation.

Deliberate attention was given to avoid providing excessive detail for some technologies and topics and to maintain a concentrated focus on techniques and technologies directly applicable to iOS Frameworks. It’s important to recognize that subjects like testing, branding, and internationalization are substantial enough to warrant their own comprehensive books.

Readers are strongly encouraged to complete every chapter of this book, with particular emphasis on actively engaging in the process of writing the code outlined within each chapter.

# Introduction

The objective of this book is to demonstrate practical techniques for designing and developing reusable, configurable, brandable, and localizable iOS mobile software. This will be achieved by leveraging iOS Frameworks to build commercial-quality mobile applications efficiently and systematically.

Designing and developing these types of reusable, modular solutions offers numerous advantages. Common functionality used across an engineering organization can be reused in multiple applications and extended as needed, reducing development time and improving consistency. Revenue opportunities can be enhanced by offering feature sets through pricing tiers and In-App Purchases. New functionality can be added dynamically with minimal effort. Centralized assets and styles make rebranding simple and straightforward. Furthermore, internationalization and localization can be incorporated without extensive re-engineering, enabling apps to reach a global audience quickly.

This book is intended for intermediate software developers who have a foundational understanding of Xcode, iOS SDK, Swift, Storyboards, and SwiftUI.

The book begins with core concepts for developing reusable software using iOS Frameworks and gradually builds complexity in a clear, step-by-step manner. Readers will explore Swift Protocols, the Model-View-ViewModel (MVVM) design pattern, local and remote service requests, Dependency Injection, Combine, SwiftData, Swift Testing, Storyboards, and SwiftUI. Techniques for dynamically integrating iOS Frameworks into applications are also covered. By the conclusion of this book, readers will have developed two distinct applications using the same frameworks, demonstrating the power and flexibility of reusable, modular iOS software without needing to modify underlying code.

Any source code or other supplementary material referenced by the author in this book is available to readers on GitHub (https://github.com/Apress/Extreme-Mobile). For more detailed information, please visit https://www.apress.com/gp/services/source-code.

# About the Author

# About the Technical Reviewer

# 1. Building the Foundations of Modular Framework Design

Designing the SettingsFW Framework

Swift protocols will be introduced as the first means of supporting software modularization in mobile apps. A Swift protocol is a blueprint or interface that defines a set of methods, properties, and requirements that a class, structure, or enumeration can adopt. It provides a way to establish a contract between different types, allowing them to communicate and interact with each other in a consistent manner. Protocols in Swift are used to define common behavior or functionality that can be shared among multiple types, enabling code reuse and promoting modularity. By adopting a protocol, a type guarantees it will implement the required methods and properties defined by the protocol, thereby ensuring a common set of capabilities and enabling polymorphism. You can find additional details regarding Swift protocols^([¹](#fn1)) at Swift.org.

When designing iOS Frameworks, it’s critical to strive for decoupling software components. This goal ensures that modifications made to one component will have minimal impact on others. When components are tightly coupled, where strong dependencies exist, maintaining the system becomes arduous and time-consuming. Swift protocols can help reduce coupling by abstracting implementation details and minimizing the need for widespread changes across the framework and apps that integrate it. This can facilitate a more streamlined and manageable development process.

In this chapter, we’ll design an iOS Framework using a Swift protocol. By the end, you’ll have created your first iOS Framework and successfully integrated it into a mobile app, giving you hands-on experience with modular, reusable code.

## Creating an iOS Framework

Xcode provides a project template called **Framework**, located under the **Frameworks & Libraries** group, for creating an iOS Framework. Originally called Cocoa Touch Framework, the Framework template will provide a default set of files for the project. You’ll add to those as you progress through the chapter. Let’s get started!

Choose a template for your new project:

![Screenshot of a software development interface for creating a new project. Tabs at the top include options like Multiplatform, iOS, macOS, and others. The iOS tab is selected. Below, there are sections for “Application” and “Framework & Library.” The Application section shows icons for App, Document App, Game, Augmented Reality App, and more. The Framework & Library section includes options like Framework, Static Library, and Metal Library, with Framework selected. Buttons for “Cancel,” “Previous,” and “Next” are at the bottom.](images/678928_1_En_1_Chapter/678928_1_En_1_Fig1_HTML.jpg)

Figure 1-1

New Project Template

1.  Launch Xcode

2.  Select **Create New project**

3.  Select the **Framework** template under the **Framework & Library** group (**Figure** [**1-1**](#678928_1_En_1_Chapter.xhtml#Fig1))

4.  Select **Next**

Choose options for the new project (**Figure** [**1-2**](#678928_1_En_1_Chapter.xhtml#Fig2)):

![Options screen for creating a new project in a software development environment. Fields include “Product Name” set to “SettingsFW,” “Team” set to “Graphixware, LLC,” “Organization Identifier” as “com.gw,” and “Bundle Identifier” as “com.gw.SettingsFW.” The “Language” is set to “Swift,” and the “Testing System” is “Swift Testing.” There is an option to “Include Documentation,” which is unchecked. Navigation buttons “Cancel,” “Previous,” and “Next” are at the bottom.](images/678928_1_En_1_Chapter/678928_1_En_1_Fig2_HTML.jpg)

Figure 1-2

New Project Settings

**Product Name:** Your new product’s name

This will be the name of the framework project. A folder with the same name will be created in the root directory of the project. Use **SettingsFW** for this project.

**Team:** Your development team

This field is a drop-down containing code signing identities associated with your Apple ID. You’ll need to select one to ensure the app comes from your organization when releasing it on the Apple App Store. If you need to create a code signing identity, you can do so after the framework has been created, using the corresponding Apple documentation.^([²](#fn2))

**Organization Identifier:** Your organization’s bundle identifier prefix

This value will be prepended to the Bundle Identifier.

**Language:** Your primary implementation language

This value should be set to **Swift**, as this book provides code samples in Swift.

**Testing System:** XCTest or Swift Testing

This value should be set to **Swift Testing**, as this book provides code samples using the Swift Testing framework.

The remaining settings are arbitrary.

## Configuring an iOS Framework

The **Framework** template sets most of the necessary project settings. If you still need to associate a Team with your framework, create one using the Apple documentation referenced in the previous step, download it, and select it (**Figure** [**1-3**](#678928_1_En_1_Chapter.xhtml#Fig3)):

![Screenshot of an Xcode project window titled “SettingsFW.” The left panel shows a file directory with “SettingsFW” and “SettingsFWTests” folders, each containing a Swift file. The main panel displays the “Signing & Capabilities” tab, showing settings for automatic signing, team name “Graphixware, LLC,” and bundle identifiers for iOS and macOS. The iOS section lists a provisioning profile as “None Required” and a signing certificate for Apple Development. The macOS section shows similar details.](images/678928_1_En_1_Chapter/678928_1_En_1_Fig3_HTML.jpg)

Figure 1-3

Signing & Capabilities Settings

1.  Select the **Xcode****/****Settings** menu item

2.  Select the **Apple** **Accounts** tab. If you aren’t logged into your Apple account, do so now, as this will automatically download your teams to the Team section

3.  Close Settings and select the root node of the project in the **Project Navigator**

4.  Select the default target under the **Show project and target lists**

5.  Select the **Signing & Capabilities** tab

6.  Select **Automatically manage** **signing** under the **Signing** section

7.  Select your team in the **Team** drop-down list

Once the project has been created, the **DEFINES_MODULE** setting under the Build Settings/Packaging section can be set to **YES** to package the framework as a Swift module, allowing it to be imported with “import [framework name]” in Swift code, as well as exposing any public Objective-C headers through an umbrella header. If an umbrella header is needed to expose Objective-C headers, the following steps will need to be performed:

![Screenshot of a development environment interface showing a project named “SettingsFW.” The left panel lists files: “SettingsFW.h,” “SettingsFW.swift,” and “SettingsFWTests.swift.” The right panel displays the “Build Phases” tab with sections for “Target Dependencies,” “Run Build Tool Plug-ins,” and “Headers,” which includes public, private, and project headers. The interface is set to an iPhone 17 Pro Max simulator, with a status indicating readiness at 12:40 PM.](images/678928_1_En_1_Chapter/678928_1_En_1_Fig4_HTML.jpg)

Figure 1-4

Build Phase Settings

1.  Right-click the framework folder within the **Project Navigator** and select **New File from Template…**

2.  Choose the **Header File** template under the **Source** section and **Next**

3.  Input a name for the file, e.g., **SettingsFW.h**, and create the file

4.  Select the Root node within the **Project Navigator**

5.  Select the **Build Phases** tab, expand the **Headers** section, and drag the umbrella file to the **Public** list (**Figure** [**1-4**](#678928_1_En_1_Chapter.xhtml#Fig4))

6.  Select the umbrella file within the **Project Navigator** and insert the following (using the appropriate framework name):

    ```
    #import 
    //! Project version number for SettingsFW.
    FOUNDATION_EXPORT double SettingsFWVersionNumber;
    //! Project version string for SettingsFW.
    FOUNDATION_EXPORT const unsigned char SettingsFWVersionString[];
    // MARK: - Public Objective-C Headers
    // Import all headers you want visible to Swift here
    // MARK: - Optional Swift Interop
    // Swift-only classes are automatically available, no imports needed
    ```

**Fun Fact:** Previous versions of Xcode used to generate this file automatically.

## Creating a Storyboard

iOS Frameworks can provide different types of functionality you may want to use in a mobile app, including Swift class extensions, common utilities, Third-Party API integrations, and feature sets. In this example, a User Interface Storyboard feature will be developed using Interface Builder. Later in the book, a more involved SwiftUI User Interface will be created.

![Screenshot of a software interface for selecting a template for a new file. The interface includes options for different operating systems like iOS, macOS, watchOS, tvOS, visionOS, and DriverKit. Under “Source,” options include Swift File, Cocoa Touch Class, Objective-C File, Header File, C++ File, Metal File, and C File. Under “User Interface,” options include SwiftUI View, Storyboard, View, Empty, and Launch Screen. The “Storyboard” option is highlighted, and there are “Cancel,” “Previous,” and “Next” buttons at the bottom.](images/678928_1_En_1_Chapter/678928_1_En_1_Fig5_HTML.jpg)

Figure 1-5

New File Template Window

1.  Right-click on the framework folder within the **Project Navigator** and select **New File from Template…**

2.  Select the **Storyboard** template under the **User Interface** group (**Figure** [**1-5**](#678928_1_En_1_Chapter.xhtml#Fig5)) and **Next**

3.  Save the file as **SettingsUI.storyboard**

## Creating a User Interface

This framework will represent account settings. You can practice developing this UI using Interface Builder, or you can download the source code from GitHub and replace the Storyboard file contents as follows:

![Screenshot of an Xcode interface displaying a storyboard for an iOS app. The storyboard includes two iPhone screens. The first screen shows a “Navigation Controller” with a placeholder for a title. The second screen is labeled “Settings” and contains a table view with static content, listing “General” information such as Name, Email, and Website with example entries: “Graphixware, LLC,” “graphixware@gmail.com,” and “https:/graphixware.com.” The left sidebar shows a file structure with files like “SettingsFW.h” and “SettingsUI.storyboard” highlighted.](images/678928_1_En_1_Chapter/678928_1_En_1_Fig6_HTML.jpg)

Figure 1-6

Storyboard Editor

1.  Right-click **SettingsUI****.storyboard** in the **Project Navigator** and select Open As/Source Code

2.  Select the file contents and delete it

3.  Open the downloaded **SettingsUI.storyboard** file in a text editor and paste it into the project’s .storyboard file

4.  Right-click the storyboard file in the **Project Navigator** and select Open As/Interface Builder - Storyboard (**Figure** [**1-6**](#678928_1_En_1_Chapter.xhtml#Fig6))

## Creating a Cocoa Touch Class

A Cocoa Touch class will need to be created to support the User Interface defined in the Storyboard:

1.  Right-click the folder within the **Project Navigator** and select **New File from Template…**

2.  Choose the **Cocoa Touch** **Class** template under the **Source** section and **Next**

3.  Input a name for the class, e.g., **SettingsTVC**, and set **UITableViewController** as the subclass

4.  Ensure **Also create** **XIB** **file** is unchecked and **Language** is Swift

Replace the default generated code within **SettingsTVC.swift** with the following:

```
import UIKit
class SettingsTVC: UITableViewController {
@IBOutlet weak var nameLabel: UILabel!
@IBOutlet weak var nameValueLabel: UILabel!
@IBOutlet weak var emailLabel: UILabel!
@IBOutlet weak var emailValueLabel: UILabel!
@IBOutlet weak var websiteLabel: UILabel!
@IBOutlet weak var websiteValueLabel: UILabel!
var configuration: Dictionary?
func configure(configuration: Dictionary) {
self.configuration = configuration
}
override func viewDidLoad() {
super.viewDidLoad()
self.navigationController?.navigationBar.prefersLargeTitles = true
self.navigationItem.largeTitleDisplayMode = .always
// The following hard-coded values will be fetched from a backing store via API in an actual app...
self.nameValueLabel.text = "Graphixware, LLC"
self.emailValueLabel.text = "gw@graphixware.com"
self.websiteValueLabel.text = "https://graphixware.com"
}
override func numberOfSections(in tableView: UITableView) -> Int {
return 1
}
override func tableView(_ tableView: UITableView, heightForHeaderInSection section: Int) -> CGFloat {
return 0
}
override func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
return 3
}
override func tableView(_ tableView: UITableView, canEditRowAt indexPath: IndexPath) -> Bool {
return false
}
}
```

Since this class is only a static representation of arbitrary account settings, the numbers of sections and rows are fixed via **numberOfSections()** and **numberOfRowsInSection()**.

The Dictionary variable **configuration** and the function **configure()** will allow the enablement and configuration of new features in the future, as they become available via framework enhancements. Additional code needed for SettingsTVC will be added throughout the chapter.

## Creating a Swift Protocol

To enable the integration of a framework into an app, it is necessary to define a public interface that the app can utilize. While the framework could simply expose a public function in a Swift class without the use of a protocol, doing so would result in a tightly coupled relationship between the two entities and hinder the framework's ability to modify its user interface in the future without impacting consuming apps. A better approach will be to define a public protocol to hide the implementation details, allowing the framework to change it in the future without requiring app changes and another App Store release. Perform the following steps to create the protocol:

1.  Right-click the folder within the **Project Navigator** and select **New File from Template…**

2.  Choose the **Swift** **File** template under the **Source** section and **Next**

3.  Name the file **SettingsProtocol.swift**

A protocol function will need to be defined with a Dictionary parameter, which will be used in the future to expose, enable, and configure framework features as they become available:

```
import Foundation
import UIKit
protocol DisplayableViewController {
func instantiateRootViewController(configuration: Dictionary) -> UIViewController
}
```

The protocol will need to be implemented to instantiate the root view controller:

```
public class SettingsProtocol: DisplayableViewController {
public init() {}
public func instantiateRootViewController(configuration: Dictionary) -> UIViewController {
let storyboard = UIStoryboard(name: "SettingsUI", bundle: Bundle(for: SettingsTVC.self))
let vc = storyboard.instantiateViewController(withIdentifier: "SettingsNC")
if let navController = vc as? UINavigationController {
if let settingsTVC = navController.topViewController as? SettingsTVC {
settingsTVC.configure(configuration: configuration)
}
}
return vc
}
}
```

**Fun Fact:** A framework storyboard will not be found when referenced from an external app target unless the UIStoryboard initializer **Bundle(for: [class])** is used.

**Fun Fact:** The framework view controller will not load properly unless a **Storyboard** **ID** has been assigned to it within the Storyboard, along with the corresponding class (the sample storyboard has set these already).

**Fun Fact:** An access modifier was not used when defining the function protocol, but it must be made public when implemented in order for an external app target to integrate it properly.

The code should compile cleanly up to this point.

## Creating an App Target

In order to run the iOS Framework without integrating it into an external app (which will be done outside of the framework project), an app target needs to be created within the framework project to test it. Perform the following steps to create the app target:

![Screenshot of a software development interface for selecting a new target template. Options include platforms like iOS, macOS, and others. Application extensions listed are Spotlight Import, Sticker Pack, Thumbnail, Translation Provider, Unwanted Communication, URL Filter Network, Virtual Conference, and Widget Extension. Application options include App, Document App, App Clip, Game, and Augmented Reality App. A search filter is present, and navigation buttons for “Previous” and “Next” are at the bottom.](images/678928_1_En_1_Chapter/678928_1_En_1_Fig7_HTML.jpg)

Figure 1-7

New Target Template

1.  Select the root node within the **Project Navigator**

2.  Select the **+** icon at the bottom of the **Targets** **Navigator** to create a new target

3.  Select the **App** template under the **Application** group (**Figure** [**1-7**](#678928_1_En_1_Chapter.xhtml#Fig7)) and **Next**

4.  Set the **Project Name** to **SettingsApp**

5.  Set the **Interface** to **Storyboard**

6.  Set the **Language** to **Swift**

The remaining settings are arbitrary.

## Creating an App Target User Interface

When the app target is created, a default set of files is generated. Some of these files are not needed, and some will need to be modified to expose the framework’s functionality, and in this case, the framework’s User Interface. Perform the following steps to create an app target User Interface:

1.  Select **ViewController****.swift** from within the **Project Navigator** and delete it, as it’s not needed

2.  Select **Main.storyboard** from within the **Project Navigator**, select the **View** **Controller Scene** from within the **Storyboard** **Navigator**, and delete it

3.  Select the **Library** icon near the bottom left of the **Interface Builder** (+ icon) and create a **Tab Bar Controller**

4.  Delete **Item 1 Scene** and **Item 2 Scene**, as they’re not needed

## Designing an App Target User Interface

Perform the following steps to configure an app target User Interface:

![Xcode interface displaying a storyboard for an iOS app. The main window shows a “Tab Bar Controller” with a placeholder for a tab bar. The left panel lists project files, including Swift files and storyboards. The right panel displays properties for the view controller, such as layout and transition settings. The top bar indicates the project name “SettingsFW” and the build status as succeeded.](images/678928_1_En_1_Chapter/678928_1_En_1_Fig9_HTML.jpg)

Figure 1-9

Storyboard Editor

![Screenshot of an Xcode interface displaying a project named “SettingsFW.” The left panel shows a file directory with folders and Swift files, including “AppDelegate.swift” and “Main.storyboard.” The central panel features a storyboard editor with a “Tab Bar Controller” layout for an iPhone 17 Pro. The right panel contains properties for the selected “Tab Bar Controller,” including class and storyboard settings. The interface includes various toolbars and icons for navigation and editing.](images/678928_1_En_1_Chapter/678928_1_En_1_Fig8_HTML.jpg)

Figure 1-8

Storyboard Editor

1.  Select the **Tab Bar Controller** **Scene** in the Interface Builder

2.  Select **Show the Identity inspector** using the corresponding top right icon

3.  Input a **Storyboard** **ID** in the corresponding field, e.g., **SettingsAppId** (**Figure** [**1-8**](#678928_1_En_1_Chapter.xhtml#Fig8))

4.  Select **Show the** **Attributes** **Inspector** using the corresponding top right icon and select **Is Initial** **View** **Controller** (**Figure** [**1-9**](#678928_1_En_1_Chapter.xhtml#Fig9))

## Adding an iOS Framework

Before integrating the iOS Framework into the app, it needs to be added to the app target:

![A screenshot of a development environment interface showing a project named “SettingsFW” with a sidebar listing files like “AppDelegate.swift” and “Main.storyboard.” A dialog box titled “Choose frameworks and libraries to add” is open, displaying options such as “SettingsFW.framework” and various Apple SDKs. The “SettingsFW.framework” is highlighted, and there are buttons labeled “Add Other,” “Cancel,” and “Add” at the bottom.](images/678928_1_En_1_Chapter/678928_1_En_1_Fig10_HTML.jpg)

Figure 1-10

Add Frameworks

1.  Select the root node of the framework project within the **Project Navigator**

2.  Select the **SettingsApp** target under the **Targets** section

3.  Select the **General** tab and the **+** icon under the **Frameworks, Libraries, and Embedded Content** section

4.  Add the framework to the app target and it will appear under the **Frameworks, Libraries, and Embedded Content** section with **Embed & Sign** (**Figure** [**1-10**](#678928_1_En_1_Chapter.xhtml#Fig10))

## Integrating an iOS Framework

The iOS Framework will need to be integrated into the app from within the app’s **SceneDelegate****.****willConnectTo****()** to instantiate the framework’s User Interface using the protocol defined:

```
import SettingsFW
func scene(_ scene: UIScene, willConnectTo session: UISceneSession, options connectionOptions: UIScene.ConnectionOptions) {
guard let winScene = (scene as? UIWindowScene) else { return }
if let storyboard = session.configuration.storyboard {
if let tabBarController = storyboard.instantiateInitialViewController() as? UITabBarController {
window = UIWindow(windowScene: winScene)
window?.rootViewController = tabBarController
window?.makeKeyAndVisible()
// Add framework User Interfaces via protocol...
var tbcViewControllers = tabBarController.viewControllers ?? []
tbcViewControllers.append(SettingsProtocol().instantiateRootViewController(configuration: ["TestAttribute" : true]))
tabBarController.setViewControllers(tbcViewControllers, animated: false)
tabBarController.tabBar.isHidden = (tbcViewControllers.count <= 1)
}
}
}
```

## Running the App Target

Set **SettingsApp** as the active scheme in the scheme drop-down, build the app, and run it using one of the Xcode simulators or an actual device. The app should compile and run properly, exposing the corresponding framework features (**Figure** [**1-11**](#678928_1_En_1_Chapter.xhtml#Fig11)).

![Screenshot of a development environment displaying a mobile app simulator. The simulator shows a “Settings” screen with details: Name as “Graphixware, LLC,” Email as “gw@graphixware.com,” and Website as “https:/graphixware.com.” The development environment includes a project file structure on the left and build settings on the right.](images/678928_1_En_1_Chapter/678928_1_En_1_Fig11_HTML.jpg)

Figure 1-11

Xcode Simulator

In this chapter, you learned how to design and implement an iOS Framework with Swift protocols. You built a framework from scratch, explored how to structure it for modularity and reusability, and integrated it into a mobile app. By completing these steps, you gained practical experience in creating frameworks that can simplify app architecture and promote code sharing across projects.

With your first iOS Framework built and integrated into an app, you’re ready to explore more advanced design techniques. In the next chapter, we’ll apply modern architectural patterns to create scalable, maintainable frameworks that can adapt to multiple apps and use cases.

Footnotes [1](#678928_1_En_1_Chapter.xhtml#Fn1_source)   [2](#678928_1_En_1_Chapter.xhtml#Fn2_source)  

# 2. Applying Modern Design Patterns for Scalable, Maintainable Frameworks

Designing the RecordingsFW Framework

This chapter will delve into the intricacies of designing an iOS Framework that can be utilized across different applications for various purposes. It will showcase the efficacy of software reusability by demonstrating the integration of a local file service into a User Interface using the Model-View-ViewModel (MVVM) design pattern. The chapter will continue to reinforce the importance of software decoupling, building upon the concepts introduced in the previous chapter. As you progress through the book, this framework's protocol will be replaced by a more versatile, generic solution that further enhances software decoupling.

For individuals unfamiliar with the MVVM design pattern, it is an architectural pattern in computer software that facilitates the separation of the graphical user interface (view) from the business logic (model) such that the view is not dependent upon any specific model platform.^([³](#fn3)) The internet offers an abundance of articles on this topic specifically tailored for iOS mobile apps.

In this chapter, we’ll focus on designing the RecordingsFW Framework with an emphasis on scalability, maintainability, and reusability. You’ll see how to integrate a local file service into a User Interface using the Model-View-ViewModel (MVVM) design pattern, reinforcing the principles of software decoupling introduced earlier. As you progress, the framework’s protocol will eventually evolve into a more versatile, generic solution, further enhancing modularity and flexibility across different applications.

## Setting Up the iOS Framework and Project

A new iOS Framework and Project will need to be created and configured, similar to Chapter [1](#678928_1_En_1_Chapter.xhtml). Name the project and corresponding files using the **Recordings** prefix, e.g., **RecordingsFW**:

1.  Perform the steps listed under the section “**Creating an iOS Framework**” in Chapter [1](#678928_1_En_1_Chapter.xhtml)

2.  Perform the steps listed under the section “**Configuring an iOS Framework**” in Chapter [1](#678928_1_En_1_Chapter.xhtml)

3.  Perform the steps listed under the section “**Creating a Storyboard**” in Chapter [1](#678928_1_En_1_Chapter.xhtml)

## Creating a User Interface

The framework in this chapter will display a list of local files that will represent audio recordings. You can practice developing this UI using Interface Builder, or you can download the code from GitHub and replace the Storyboard file contents as follows:

![Screenshot of a development environment showing a storyboard for a mobile app. The interface includes three main panels representing different app screens. The first panel is a navigation controller with a “Title” label. The second panel displays a list view titled “Recordings” with a prototype cell labeled “My Acoustic Recording.” The third panel shows details for “My Acoustic Recording,” including copyright information, release date, length, type, bit rate, and size. Navigation and control buttons are visible at the bottom of each panel. The left sidebar lists project files and components.](images/678928_1_En_2_Chapter/678928_1_En_2_Fig1_HTML.jpg)

Figure 2-1

Storyboard Editor

1.  Right-click **RecordingsUI.storyboard** in the **Project Navigator** and select **Open As/Source Code**

2.  Select the file contents and delete it

3.  Copy the storyboard file contents from the downloaded **RecordingsUI.storyboard** file and paste it into the framework’s .storyboard file

4.  Right-click **RecordingsUI.storyboard** in the **Project Navigator** and select **Open As/Interface Builder** - **Storyboard** (**Figure** [**2-1**](#678928_1_En_2_Chapter.xhtml#Fig1))

## Creating a Cocoa Touch Class

Perform the following steps to create a Cocoa Touch class to support the User Interface defined in the last step:

1.  Right-click the folder within the Project Navigator and select **New File from Template…**

2.  Choose the **Cocoa Touch** **Class** template under the **Source** section and **Next**

3.  Input **RecordingsTVC** as the class name, and set **UITableViewController** as the subclass

4.  Ensure **Also create** **XIB** **file** is unchecked and **Language** is Swift

Again, the Cocoa Touch class will need to be modified to correspond to the downloaded storyboard configuration:

```
import UIKit
class RecordingsTVC: UITableViewController {
@IBOutlet weak var publishBarButtonItem: UIBarButtonItem! // Note: To be used in a future chapter
override func viewDidLoad() {
super.viewDidLoad()
self.publishBarButtonItem.isHidden = true // Note: To be used in a future chapter
self.clearsSelectionOnViewWillAppear = true
self.navigationController?.navigationBar.prefersLargeTitles = true
self.navigationItem.largeTitleDisplayMode = .always
self.extendedLayoutIncludesOpaqueBars = true
self.tableView.refreshControl?.addTarget(self, action: #selector(self.refresh(_:)), for: .valueChanged)
self.tableView.refreshControl?.beginRefreshing()
}
@objc func refresh(_ sender: AnyObject) {
}
override func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
return 0
}
override func numberOfSections(in tableView: UITableView) -> Int {
return 1
}
override func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
let cell = tableView.dequeueReusableCell(withIdentifier: "RecordingsCell", for: indexPath)
return cell
}
override func tableView(_ tableView: UITableView, canEditRowAt indexPath: IndexPath) -> Bool {
return false
}
}
class RecordingsCell: UITableViewCell {
@IBOutlet weak var titleLabel: UILabel!
@IBOutlet weak var copyrightLabel: UILabel!
}
```

In this code sample, **cellForRowAt****()** dequeues a reusable UITableViewCell via identifier to render each row of the table. The class to support this, **RecordingsCell**, is also defined in this class.

## Creating a Protocol and Model

In order to implement the MVVM design pattern for this framework, a protocol will need to be created for the ViewModel. The ViewModel implementation of the protocol will fetch a list of local files representing audio recordings. Once the protocol has been implemented in the ViewModel, the ViewModel will need to be integrated into the table view. Perform the following steps to create the protocol:

1.  Right-click the folder in **Project Navigator** and select **New File from Template…**

2.  Choose the **Swift** **File** template under the **Source** section

3.  Create a class called **RecordingsProtocol.swift**

Define the protocol to return an array of **Recording** models, which will be generated from local files on the device:

```
protocol RefreshableViewModel {
func getRecordings() -> [Recording]
func refresh()
}
```

Define a Recording model to represent (arbitrary) metadata for an audio recording. The model will need to be Codable^([⁴](#fn4)) so that these data types can be read from, and written to, an external representation:

```
struct Recording: Codable {
let bitrate: Int64
let copyright: String
let date: String
let length: Int64
let size: Int64
var title: String
let type: String
let url: String
}
```

## Creating a ViewModel

A ViewModel class will need to be created to implement the protocol defined in the previous section:

1.  Right-click the folder within the **Project Navigator** and select **New File from Template…**

2.  Choose the **Swift** **File** template under the **Source** section

3.  Create a class called **RecordingsViewModel.swift**

Adopt **RefreshableViewModel**, implement the required protocol functions, and define a variable named **recordings** to hold the models returned by the underlying service (to be defined next):

```
import Foundation
class RecordingsViewModel: RefreshableViewModel {
private(set) var recordings: [Recording]!
init() {
}
func getRecordings() -> [Recording] {
return recordings
}
func refresh() {
fetch()
}
private func fetch() {
}
}
```

The **recordings** variable **didSet** will need to be overridden to provide a way to notify the view when recordings have changed. A compound (function) type^([⁵](#fn5)) variable will also need to be defined to perform the notification. This will be set in the View and will bind the View to the ViewModel:

```
private(set) var recordings: [Recording]! {
didSet {
self.bindRecordingsViewModelToController()
}
}
var bindRecordingsViewModelToController : (() -> ()) = {}
```

The following code should be implemented in the **fetch****()** function to call the local service to read files from the device, create corresponding models, and set the **recordings** variable to trigger a notification to the View to update itself.

Note

The *fetch* function won’t compile until the RecordingsService class has been created, so it can be commented out until it has been created.

```
private func fetch() {
RecordingsService.shared.fetch() { [weak self] recordings, error in
guard let strongSelf = self else { return }
if let err = error {
print("RecordingsViewModel.fetch(): error = \(err.localizedDescription)")
} else {
strongSelf.recordings = recordings
}
}
}
```

## Creating a Local File Service

In this framework, a local file service will be used to read files from a device and return metadata representing audio files. Perform the following steps to create the service:

1.  Right-click the folder within the **Project Navigator** and select **New File from Template…**

2.  Choose the **Swift** **File** template under the **Source** section

3.  Create a class called **RecordingsService.swift**

Create a **singleton**^([⁶](#fn6)) class for the service. A singleton is a common design pattern that prevents multiple instances of a class from being instantiated. Once instantiated, the same instance is used wherever it’s referenced.

```
import Foundation
class RecordingsService {
public static let shared = RecordingsService()
}
```

Define a completion handler as a compound (function) type, which the service will call when data has been read from the file system and converted to **Recording** models.

```
typealias RecordingCompletionHandler = ([Recording]?, Error?) -> ()
```

Since this framework reads files from a device and returns metadata representing those files, sample data will need to be defined for use when no files exist. The **init()** function will use the **title** attribute of each **Recording** model to check for the existence of a corresponding file and create the file if it doesn’t exist. Add the following code to the class:

```
private let recordings: [Recording] = [
Recording(bitrate: 131072, copyright: "© 2025 Words and Music by  John Doe", date: "04/28/2025", length: 270, size: 5*1024*1024,
title: "My Electric Guitar Anthem", type: "AAC Audio File", url: "https://graphixware.com/MyElectricAnthem.aac"),
Recording(bitrate: 131072, copyright: "© 2025 Words and Music by John Doe", date: "08/24/2025", length: 228, size: 10*1024*1024,
title: "My Acoustic Guitar Ballad", type: "AAC Audio File", url: "https://graphixware.com/MyAcousticBallad.aac")
]
private init() {
let encoder = JSONEncoder()
encoder.outputFormatting = .prettyPrinted
for recording in recordings {
do {
let file = try FileManager.default.url(for: .documentDirectory, in: .userDomainMask, appropriateFor: nil, create: true)
.appendingPathComponent(recording.title + ".aac")
if !FileManager.default.fileExists(atPath: file.lastPathComponent) {
try encoder.encode(recording).write(to: file)
}
} catch {
print("RecordingsService.init(): Error = \(error.localizedDescription)")
}
}
}
```

Define a **fetch****()** function to be called by the ViewModel to fetch the data, which the View, in turn, will display in its User Interface treatment.

```
func fetch(completion: @escaping RecordingCompletionHandler) {
var recordingModels: [Recording] = []
let fileManager = FileManager.default
let documentsURL = fileManager.urls(for: .documentDirectory, in: .userDomainMask)[0]
do {
let fileURLs = try fileManager.contentsOfDirectory(at: documentsURL, includingPropertiesForKeys: nil)
for fileURL in fileURLs {
if let recording = read(type: Recording.self, file: fileURL) {
recordingModels.append(recording)
}
}
} catch {
print("RecordingsService.fetch(): Error = \(error.localizedDescription)")
}
completion(recordingModels, nil)
}
private func read(type: T.Type, file: URL) -> T? {
var data: Data?
do {
data = try Data(contentsOf: file)
} catch {
print("RecordingsService.read(): Error = \(error.localizedDescription)")
}
guard let recordingData = data else { return nil }
do {
let decoder = JSONDecoder()
return try decoder.decode(T.self, from: recordingData)
} catch {
print("RecordingsService.read(): Error = \(error.localizedDescription)")
}
return nil
}
```

Note

Since the RecordingsService is now defined, uncomment the code that was previously commented out in the *fetch*() function within the RecordingsViewModel, so that the project will compile properly.

## Integrating the ViewModel into the View

Select **RecordingsTVC.swift** and define a variable called **viewModel**, along with a function called **initLocalServiceVM()**, to instantiate the ViewModel, bind the ViewModel to the View, fetch the data from the local file service, and update the User Interface when the data changes:

```
class RecordingsTVC: UITableViewController {
var viewModel: RefreshableViewModel?
override func viewDidLoad() {
super.viewDidLoad()
...
initLocalServiceVM()
}
func initLocalServiceVM() {
self.viewModel = RecordingsViewModel()
(self.viewModel as! RecordingsViewModel).bindRecordingsViewModelToController = { [weak self] in
guard let strongSelf = self else { return }
if let recordings = strongSelf.viewModel?.getRecordings() {
print("RecordingsTVC.initLocalServiceVM().bindRecordingsViewModelToController(): recordings.count= \(recordings.count)")
}
DispatchQueue.main.async {
strongSelf.refreshControl?.endRefreshing()
strongSelf.tableView.reloadData()
}
}
self.viewModel?.refresh()
}
}
```

Integrate the ViewModel into each function of the User Interface implementation that utilizes the data:

```
@objc func refresh(_ sender: AnyObject) {
viewModel?.refresh()
}
override func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
return self.viewModel?.getRecordings().count ?? 0
}
override func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
let cell = tableView.dequeueReusableCell(withIdentifier: "RecordingsCell", for: indexPath)
if let recordingCell = cell as? RecordingsCell, let recording = self.viewModel?.getRecordings()[indexPath.row] {
recordingCell.titleLabel.text = recording.title
recordingCell.copyrightLabel.text = recording.copyright
return recordingCell
}
return cell
}
```

## Integrating the Details View into the Table

As previously discussed, the User Interface in this framework utilizes a Master-Detail design pattern. To complete the Details View, create a class to display details of an audio file when the corresponding row is tapped in the table view:

1.  Right-click the folder within the Project Navigator and select **New File from Template…**

2.  Choose the **Cocoa Touch** **Class** template under the **Source** section and **Next**

3.  Input a class name, e.g., **RecordingTVC**, and set **UITableViewController** as the subclass

4.  Ensure **Also create** **XIB** **file** is unchecked and **Language** is Swift

Note

This class is insignificant to this framework other than to allow it to operate correctly. Replace the default code that was generated with the following code, which supports the corresponding Storyboard previously created.

```
import UIKit
class RecordingTVC: UITableViewController {
@IBOutlet weak var copyrightNameLabel: UILabel!
@IBOutlet weak var copyrightLabel: UILabel!
@IBOutlet weak var dateNameLabel: UILabel!
@IBOutlet weak var dateLabel: UILabel!
@IBOutlet weak var lengthNameLabel: UILabel!
@IBOutlet weak var lengthLabel: UILabel!
@IBOutlet weak var typeNameLabel: UILabel!
@IBOutlet weak var typeLabel: UILabel!
@IBOutlet weak var bitrateNameLabel: UILabel!
@IBOutlet weak var bitrateLabel: UILabel!
@IBOutlet weak var sizeNameLabel: UILabel!
@IBOutlet weak var sizeLabel: UILabel!
let detailsTitle = "Details"
let previewTitle = "Preview"
var recording: Recording?
let kbps = "kbps"
let mb = "MB"
override func viewDidLoad() {
super.viewDidLoad()
self.clearsSelectionOnViewWillAppear = true
self.navigationItem.title = recording?.title ?? detailsTitle
self.tableView.separatorInset = UIEdgeInsets.init(top: 0.0, left: 20.0, bottom: 0.0, right: 0.0)
copyrightLabel.text = recording?.copyright ?? ""
dateLabel.text = recording?.date ?? ""
if let length = recording?.length {
let quotient = length / 60
let remainder = length % 60
lengthLabel.text = String(describing: quotient) + ":" + String(describing: remainder)
}
typeLabel.text = recording?.type ?? ""
if let bitrate = recording?.bitrate {
bitrateLabel.text = String(describing: bitrate / 1024) + " kbps"
}
if let size = recording?.size {
let quotientMB = size / (1024 * 1024)
let remainder = size % (1024 * 1024)
let value = remainder > 0 ? String(describing: remainder) + "." : ""
sizeLabel.text = String(describing: quotientMB) + value + " MB"
}
}
override func numberOfSections(in tableView: UITableView) -> Int {
return 2
}
override func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
switch section {
case 0:
return 6
case 1:
return 1
default:
return 0
}
}
override func tableView(_ tableView: UITableView, canEditRowAt indexPath: IndexPath) -> Bool {
return false
}
override func tableView(_ tableView: UITableView, viewForFooterInSection section: Int) -> UIView? {
if section  CGFloat {
return 21.0
}
override func tableView(_ tableView: UITableView, heightForRowAt indexPath: IndexPath) -> CGFloat {
return indexPath.section == 0 ? 58.0 : 44.0
}
override func tableView(_ tableView: UITableView, willDisplayHeaderView view: UIView, forSection section: Int) {
var title = ""
switch section {
case 0:
title = detailsTitle
case 1:
title = previewTitle
default:
break
}
let titleView = view as! UITableViewHeaderFooterView
titleView.textLabel?.text = title // Remove all capitalized letters
}
}
```

Select the **RecordingsTVC** (Master View), and create a function called **prepare()** to pass the **Recording** model corresponding to the selected table row to this Details View:

```
override func prepare(for segue: UIStoryboardSegue, sender: Any?) {
if segue.identifier == "RecordingDetailsSegue" {
if let recordingTVC = segue.destination as? RecordingTVC {
if let indexPath = self.tableView.indexPathForSelectedRow, let recording = self.viewModel?.getRecordings()[indexPath.row]  {
recordingTVC.recording = recording
}
}
}
}
```

## Implementing a Swift Protocol

Since protocols are still being used to integrate this framework into a corresponding app, the protocol created in **RecordingsProtocol.swift** will also need to be adopted by this framework (also in RecordingsProtocol):

```
public class RecordingsProtocol: DisplayableViewController {
public init() {}
public func instantiateRootViewController() -> UIViewController {
let storyboard = UIStoryboard(name: "RecordingsUI", bundle: Bundle(for: RecordingsTVC.self))
let vc = storyboard.instantiateViewController(withIdentifier: "RecordingsNC")
return vc
}
}
```

## Creating an App Target

As was done in the previous chapter, an App Target within the iOS Framework project will need to be created and configured:

1.  Perform the steps listed under the section “**Creating an App Target**” in Chapter [1](#678928_1_En_1_Chapter.xhtml)

2.  Perform the steps listed under the section “**Creating an** **App Target** **User Interface**” in Chapter [1](#678928_1_En_1_Chapter.xhtml)

3.  Perform the steps listed under the section “**Designing an** **App Target** **User Interface**” in Chapter [1](#678928_1_En_1_Chapter.xhtml)

4.  Perform the steps listed under the section “**Adding an iOS Framework**” in Chapter [1](#678928_1_En_1_Chapter.xhtml), and then select **RecordingsFW.framework**

## Integrating an iOS Framework

The iOS Framework will need to be integrated into the app from within the app’s **SceneDelegate.willConnectTo**():

```
import RecordingsFW
func scene(_ scene: UIScene, willConnectTo session: UISceneSession, options connectionOptions: UIScene.ConnectionOptions) {
guard let winScene = (scene as? UIWindowScene) else { return }
if let storyboard = session.configuration.storyboard {
if let tabBarController = storyboard.instantiateInitialViewController() as? UITabBarController {
window = UIWindow(windowScene: winScene)
window?.rootViewController = tabBarController
window?.makeKeyAndVisible()
// Add framework User Interfaces via protocol...
var tbcViewControllers = tabBarController.viewControllers ?? []
tbcViewControllers.append(RecordingsProtocol().instantiateRootViewController())
tabBarController.setViewControllers(tbcViewControllers, animated: false)
tabBarController.tabBar.isHidden = (tbcViewControllers.count <= 1)
}
}
}
```

## Running the App Target

Set **RecordingsApp** as the active scheme in the scheme drop-down, build the app, and run it using one of the Xcode simulators or an actual device. The app should compile and run properly, exposing the corresponding framework features (**Figure** [**2-2**](#678928_1_En_2_Chapter.xhtml#Fig2)).

![A screenshot of a development environment displaying an iOS simulator running an app called “Local Recordings.” The app interface shows two music recordings: “My Acoustic Guitar Ballad” and “My Electric Guitar Anthem,” both credited to John Doe with a copyright date of 2025\. The simulator is shown on an iPhone 17 Pro Max with iOS 26.0\. The development environment includes a project directory with files related to the app’s code and settings.](images/678928_1_En_2_Chapter/678928_1_En_2_Fig2_HTML.jpg)

Figure 2-2

Xcode Simulator

In this chapter, you learned how to design an iOS Framework using the MVVM pattern to separate concerns and promote software decoupling. You integrated a local file service into the UI, explored protocol-driven design, and prepared the framework for future enhancements with more generic solutions. By applying these modern design patterns, you created a scalable, maintainable framework that can be reused across multiple applications.

With the RecordingsFW Framework built using MVVM and designed for modularity, you’re now ready to make it even more versatile. In the next chapter, we’ll extend the framework to handle remote data, integrate network services, and apply advanced design patterns to create reusable, decoupled architectures.

Footnotes [1](#678928_1_En_2_Chapter.xhtml#Fn1_source)   [2](#678928_1_En_2_Chapter.xhtml#Fn2_source)   [3](#678928_1_En_2_Chapter.xhtml#Fn3_source)   [4](#678928_1_En_2_Chapter.xhtml#Fn4_source)  

# 3. Designing Generic, Decoupled Architectures for Reusable Frameworks

Extending the RecordingsFW Framework

The framework designed in the previous chapter supports the management of **local** audio recordings. As the book progresses, it will be combined with other frameworks to produce a “recording studio” style application. This chapter will enhance the framework to support the display of **remote** audio recordings, or “published recordings,” within a separate mobile app. The mode of operation will be controlled using a Property List. A network API will be integrated into a remote service and used within a Model-View-ViewModel (MVVM) design pattern.

This chapter will also utilize a software design pattern called Dependency Injection. Dependency Injection is a programming technique that makes implicit dependencies of an object explicit, allowing an object to receive those dependencies from another object, either at construction or through function calls.^([⁷](#fn7)) In this example, Dependency Injection will be used in the frameworks’ ViewModel by injecting an API service and endpoint via constructor. Since the ViewModel will not be concerned with the implementation of the service, it won’t require changes if the service is replaced.

Along with Dependency Injection, this chapter will incorporate Combine to process and publish the results of network API requests. The Combine framework provides a declarative Swift API for processing values over time. These values can represent many kinds of asynchronous events. Combine declares publishers to expose values and subscribers to receive those values from the publishers. Documentation on the Combine framework is available on the Apple Developer website.^([⁸](#fn8))

In a nutshell, this chapter will extend the RecordingsFW Framework to support remote audio recordings while maintaining scalability and reusability. A Property List will be used to control the framework’s mode of operation. A network API will be integrated within the MVVM pattern using Dependency Injection to decouple the ViewModel from service implementations. And we’ll leverage Combine to process and publish asynchronous network results, ensuring that the framework can produce multiple mobile apps without the need for code changes.

## Creating a Property List

A Property List, or “p-list,” will need to be added to the RecordingsFW project to control the framework’s mode of operation. A Property List is simply a representation of a hierarchy of objects persisted/loaded via a file system. Property Lists are useful for storing small amounts of data.^([⁹](#fn9)) Perform the following steps to create the property list:

![Screenshot of a software interface for selecting a new file template. Options include iOS, macOS, watchOS, tvOS, visionOS, and DriverKit. Core Data options are “Data Model” and “Mapping Model.” Resource options include “App Privacy,” “Asset Catalog,” “GeoJSON File,” “GPX File,” “Module Map,” “Property List,” “Rich Text File,” “SceneKit Catalog,” “SceneKit Scene File,” and “Settings Bundle.” A “Filter” search bar is at the top right. “Cancel,” “Previous,” and “Next” buttons are at the bottom.](images/678928_1_En_3_Chapter/678928_1_En_3_Fig1_HTML.jpg)

Figure 3-1

New File Template

1.  Right-click the framework folder within the **Project Navigator** and select **New File from Template…**

2.  Select the **Property List** template under the **Resource** group (**Figure** [**3-1**](#678928_1_En_3_Chapter.xhtml#Fig1)) and **Next**

3.  Save the file as **Recordings.plist**

## Configuring a Property List

After creating the Property List, add a Boolean value to control the framework’s mode of operation, set statically based on a desired outcome (e.g., display local files vs. remote files):

![Screenshot of a development environment showing a project named “RecordingsFW.” The left panel lists files and folders, including “RecordingsFW.h,” “RecordingsFW.swift,” and “RecordingsApp.” The right panel displays the contents of “Recordings.plist,” showing a dictionary with a key “showPublished” set to a Boolean value “YES.” The interface includes standard navigation and filter options.](images/678928_1_En_3_Chapter/678928_1_En_3_Fig2_HTML.jpg)

Figure 3-2

Property List Editor

1.  Select the **Property List** file within the **Project Navigator**

2.  Select the **+** icon in the Root row of the empty table

3.  Create a variable called **showPublished**; set its type to **Boolean** and its value to **YES** (**Figure** [**3-2**](#678928_1_En_3_Chapter.xhtml#Fig2))

## Configuring an External API

Since the mode of operation for this framework is to show “published recordings,” they will need to be fetched remotely using a network API request. The sample code in this chapter doesn’t include an actual service to support this, so it will need to be simulated.

There are various ways to simulate network API requests, including the use of Third-Party “stubbing” libraries such as OHHTTPStubs^([^(10)](#fn10)) and “mocking” services such as Beeceptor.^([^(11)](#fn11)) The sample code in this chapter will use Beeceptor to return the following JSON data representing audio recordings via network API request:

```
[
{
"bitrate": 131072,
"copyright": "© 2025 Words and Music by John Doe",
"date": "04/28/2025",
"length": 270,
"size": 5242880,
"title": "My (Published) Electric Anthem",
"type": "AAC Audio File",
"url": "https://graphixware.com/MyElectricAnthem.aac"
},
{
"bitrate": 131072,
"copyright": "© 2025 Words and Music by John Doe",
"date": "08/24/2025",
"length": 228,
"size": 10485760,
"title": "My (Published) Acoustic Ballad",
"type": "AAC Audio File",
"url": "https://graphixware.com/MyAcousticBallad.aac"
}
]
```

Define an enum in **RecordingsProtocol.swift** to represent the endpoint:

```
enum RecordingsEndpoint {
case fetchRecordings
var urlString: String {
switch self {
case .fetchRecordings:
return "https://graphixware.free.beeceptor.com/scratchtrax/recordings"
}
}
}
```

The URL in this example is not guaranteed to remain valid, so now is a good time to create a free account, as well as your own endpoint, at Beeceptor.com.

Define another enum in **RecordingsProtocol.swift** to handle simulated error conditions:

```
enum RecordingsError: Error {
case request(message: String)
case network(message: String)
case status(message: String)
case parsing(message: String)
case other(message: String)
}
```

## Designing a Generic Service

Create a new class called **PublishedRecordingsService** under the framework project to use as the remote service. This service will fetch recordings via network API and publish it to objects that have subscribed to change notifications, e.g., ViewModel. To prevent multiple instances of the class from being created, it will be defined as a singleton:

```
class PublishedRecordingsService {
public static let shared = PublishedRecordingsService()
private init() {
}
}
```

Define a protocol for the remote service in **PublishedRecordingsService**. Any service that adheres to the protocol will be able to be injected into the ViewModel, without changes needed to the ViewModel. The protocol and implementation will utilize Combine, so the Combine framework will need to be imported as well:

```
import Combine
protocol StubAPIService {
func fetch(_t: T.Type, url: URL) -> AnyPublisher where T: Decodable
}
```

The **fetch()** function utilizes **Swift** **Generics**. Swift Generics allow flexible, reusable functions and types to be created that can work with different types, avoiding duplication of code.^([^(12)](#fn12))

When the **fetch()** function is called, the desired type of object to be returned from the network API request will be passed into the function:

```
service.fetch(_t: Recording.self, url: url)
```

The return value of the **fetch** function is defined as **AnyPublisher****<>**. **AnyPublisher** is a concrete implementation of **Publisher** that has no significant properties of its own and merely acts as a pass-through object. A Publisher delivers elements to one or more Subscriber instances. The subscriber’s Input and Failure associated types must match the Output and Failure types declared by the publisher. The publisher implements the receive(subscriber:) method to accept a subscriber.^([^(13)](#fn13))

Adopt the protocol in **PublishedRecordingsService** and implement its required behavior:

```
class PublishedRecordingsService: StubAPIService {
public static let shared = PublishedRecordingsService()
private init() {
}
func fetch(_t: T.Type, url: URL) -> AnyPublisher where T : Decodable {
return URLSession.shared.dataTaskPublisher(for: url)
.mapError { error in
RecordingsError.network(message: error.localizedDescription)
}
.flatMap(maxPublishers: .max(1)) { pair in
self.decode(pair.data)
}
.eraseToAnyPublisher()
}
private func decode(_ data: Data) -> AnyPublisher {
let decoder = JSONDecoder()
decoder.dateDecodingStrategy = .secondsSince1970
return Just(data)
.decode(type: T.self, decoder: decoder)
.mapError { error in
RecordingsError.parsing(message: error.localizedDescription)
}
.eraseToAnyPublisher()
}
}
```

**mapError** converts any failure from the upstream publisher into a new error.

**flatMap** transforms elements from an upstream publisher into a new publisher up to a maximum number of publishers you specify. In this example, flatMap decodes the output from the upstream publisher using a specified decoder to produce **Recording** models.

**eraseToAnyPublisher** wraps this publisher with a type eraser to expose an instance of AnyPublisher to the downstream subscriber rather than this publisher’s actual type.

## Designing a ViewModel with Dependency Injection

Create a ViewModel named **PublishedRecordingsViewModel** to fetch published recordings and notify the View to update its treatment.

**PublishedRecordingsViewModel** will adopt the **RefreshableViewModel** protocol to ensure the required behavior needed by the View is supported. The ViewModel will also adopt the **ObservableObject**^([^(14)](#fn14)) protocol to support “pre-publishing” a change.

The **service** and **endpoint** variables will need to be defined to support Dependency Injection. Since these are external dependencies, they will be injected into the ViewModel via constructor to maintain decoupling within the ViewModel.

The **recordings** variable will need to be defined using the **@Published** typealias. It will maintain transformed data returned from the service and publish change events to subscribers whenever set.

The **fetch****()** function will fetch the audio recording metadata generically using the injected service and endpoint, store it in the **recordings** variable, and notify subscribers of the change.

Add the following code to the class:

```
import Foundation
import Combine
class PublishedRecordingsViewModel: RefreshableViewModel, ObservableObject {
// Dependency Injection...
private let service: StubAPIService
private let endpoint: RecordingsEndpoint
private var disposables = Set()
@Published private(set) var recordings: [Recording] = []
init(service: StubAPIService, endpoint: RecordingsEndpoint) {
self.service = service
self.endpoint = endpoint
}
func getRecordings() -> [Recording] {
return recordings
}
func refresh() {
fetch()
}
func fetch() {
if let url = URL(string: endpoint.urlString) {
service.fetch(_t: Recording.self, url: url)
.receive(on: DispatchQueue.main)
.sink { [weak self] value in
switch value {
case .failure:
self?.recordings = []
case .finished:
break
}
} receiveValue: { [weak self] response in
self?.recordings = response
}
.store(in: &disposables)
}
}
}
```

## Integrating a ViewModel into a View

In order to complete the MVVM design pattern for this example, the ViewModel will need to be integrated into the View. The View in this example is managed by **RecordingsTVC**, created in Chapter [2](#678928_1_En_2_Chapter.xhtml).

The Property List setting will be used to determine the mode of operation for the framework. A function called **showPublished()** will need to be created to read the Property List value and execute the corresponding code path. As before, the Property List will need to be loaded using the Bundle Identifier in order to work properly.

**Fun Fact:** Notice the statement following the retrieval of the Property List value, which overrides the Property List value if a UserDefaults value is found. Later in the book, when In-App Purchases are integrated into the framework, UserDefaults will be used to do so.

Integrate the following code into RecordingsTVC (don’t delete existing code):

```
let publishedTitle = "Published Recordings"
let localTitle = "Local Recordings"
override func viewDidLoad() {
if showPublished() {
self.navigationItem.title = publishedTitle
// Initialize published recordings
initRemoteServiceVM()
} else {
self.navigationItem.title = localTitle
// Initialize local recordings
initLocalServiceVM()
}
}
func showPublished() -> Bool {
var showPublished = false
if let bundle = Bundle(identifier:"com.gw.RecordingsFW"), let path = bundle.path(forResource: "Recordings", ofType: "plist"),
let plistDict = NSDictionary(contentsOfFile: path) {
showPublished = plistDict.object(forKey: "showPublished") as? Bool ?? false
}
if let features = UserDefaults.standard.object(forKey: "features") as? [String:String] {
if let publishedValue = features["showPublished"], let published = Bool(publishedValue) {
showPublished = published
}
}
return showPublished
}
func initRemoteServiceVM() {
// This will be completed in the next step…
}
```

## Injecting Dependencies into a ViewModel

Chapter [2](#678928_1_En_2_Chapter.xhtml) initialized a ViewModel for managing local audio recordings via initLocalServiceVM(). Chapter [3](#678928_1_En_3_Chapter.xhtml) will need to initialize a ViewModel for managing remote audio recordings via **initRemoteServiceVM()**.

Integrate the following code into RecordingsTVC (don’t delete existing code):

```
import Combine
class RecordingsTVC: UITableViewController {
private var cancellables: Set = []
func initRemoteServiceVM() {
self.viewModel = PublishedRecordingsViewModel(service: PublishedRecordingsService.shared, endpoint: .fetchRecordings)
(self.viewModel as! PublishedRecordingsViewModel).$recordings
.sink { [weak self] recordings in
guard let strongSelf = self else { return }
print("RecordingsTVC.initRemoteServiceVM().sink(): recordings.count = \(recordings.count)")
DispatchQueue.main.asyncAfter(deadline: .now()+1) {
strongSelf.refreshControl?.endRefreshing()
strongSelf.tableView.reloadData()
}
}
.store(in: &cancellables)
self.viewModel?.refresh()
}
}
```

The **AnyCancellable** variable is a token that represents an active Combine subscription, which is automatically cancelled when it’s deallocated.

The creation of the ViewModel in initRemoteServicesVM() will inject the service and endpoint into the object via constructor.

The **sink()** function will be used to subscribe to asynchronous change events that will be published whenever the **recordings** value changes. When a change is detected, the table view will be reloaded on the main thread (which is the thread used to update the User Interface) to reflect the changes.

store(in:) will save the cancellables tokens into a collection (usually a Set<AnyCancellable>) so the subscriptions stay alive for as long as the owner object does.

**ViewModel****.refresh()** will trigger the ViewModel to fetch data from the service and display it in the View.

## Testing

Full test coverage of source code is critical to achieve and maintain a high level of quality in software applications, which reduces future maintenance costs once applications are released. Unit, Integration, and UI Tests should be incorporated into the software development process as code is being developed (or beforehand if a Test-Driven Development approach is being used).

Most iOS developers are familiar with Xcode tools available for creating tests, specifically those based on the XCTest framework. A new testing framework, Swift Testing, was introduced in Xcode 16\. When creating an Xcode project, the option for generating stub tests now includes XCTest and Swift Testing. Choosing either option will generate stub tests based on the framework selected, and a Test Plan. Tests created using Swift Testing are not automatically added to the Test Plan (as of Xcode 26), but they can be easily added by editing the Test Plan and selecting them.

The main difference between **XCTest** and **Swift Testing** is how they’re structured and discovered. XCTest uses classes that inherit from XCTestCase, and Xcode automatically finds test methods starting with test, showing them in the Test navigator. Swift Testing, on the other hand, lets you write tests in structs using @Test and #expect, which is more “Swift-like” and works well with concurrency, but Xcode doesn’t automatically detect them. However, if you add your Swift Testing tests to a Test Plan, they will appear in the Test navigator, letting you run them inside Xcode just like XCTest tests.

A comprehensive overview of writing Unit, Integration, and UI Tests using the XCTest and Swift Testing framework can be found on the Apple developer website.^([^(15)](#fn15))

This chapter will demonstrate testing a ViewModel portion of a Model-View-ViewModel (MVVM) design pattern using a mock service. In an MVVM design, the service is mocked to isolate the ViewModel’s logic from external dependencies. Mocks provide predictable, hard-coded data or simulated errors, allowing tests to be fast, reliable, and repeatable without relying on network conditions or a live API. This also makes it easy to test edge cases, such as parsing failures or empty responses, ensuring the ViewModel handles all scenarios correctly while keeping tests focused purely on its behavior.

Under the RecordingsFWTests folder (created when the project was created), select RecordingsFWTests.swift and import the Foundation and Combine frameworks (the Testing and RecordingsFW frameworks should already be imported, with RecordingsFW marked as @testable):

```
import Foundation
import Combine
import Testing
@testable import RecordingsFW
```

A mock service based on the StubAPIService protocol (created earlier in the chapter) will be created to return canned data identical to that of the PublishedRecordingsService API. It will define a MockMode enum to simulate different scenarios (success, networkError, parsingError) and provide a hard-coded list of sample Recording objects similar to the API. The fetch method will return a Combine publisher that either emits the recordings or fails with a simulated error depending on the current mode, allowing tests to verify how the ViewModel handles various responses.

Add the following code to the class:

```
final class MockRecordingsService: StubAPIService {
enum MockMode {
case success
case networkError
case parsingError
}
var mode: MockMode = .success
private let recordings: [Recording] = [
Recording(bitrate: 131_072,
copyright: "© 2025 Words and Music by John Doe",
date: "04/28/2025",
length: 270,
size: 5 * 1024 * 1024,
title: "My Electric Guitar Anthem",
type: "AAC Audio File",
url: "https://graphixware.com/MyElectricAnthem.aac"),
Recording(bitrate: 131_072,
copyright: "© 2025 Words and Music by John Doe",
date: "08/24/2025",
length: 228,
size: 10 * 1024 * 1024,
title: "My Acoustic Guitar Ballad",
type: "AAC Audio File",
url: "https://graphixware.com/MyAcousticBallad.aac")
]
func fetch(_t: T.Type, url: URL) -> AnyPublisher where T : Decodable {
switch mode {
case .networkError:
return Fail(error: RecordingsError.network(message: ".networkError: Simulated network error"))
.eraseToAnyPublisher()
case .parsingError:
return Fail(error: RecordingsError.parsing(message: ".parsingError: Simulated parsing error"))
.eraseToAnyPublisher()
case .success:
guard let result = recordings as? [T] else {
return Fail(error: RecordingsError.parsing(message: ".success: Type mismatch"))
.eraseToAnyPublisher()
}
return Just(result)
.setFailureType(to: RecordingsError.self)
.eraseToAnyPublisher()
}
}
}
```

A Publisher will need to be created, as an extension in this example, to wait asynchronously for a publisher to emit a value that satisfies a given condition. It uses withCheckedContinuation to suspend the async function until the condition is met or a timeout occurs, making it easier to test @Published properties in an async context.

Add the following code to the class:

```
extension Publisher {
func waitForValue(
timeout: Duration = .seconds(1),
condition: @escaping (Output) -> Bool
) async -> Output? {
await withCheckedContinuation { continuation in
var cancellable: AnyCancellable?
let deadline = ContinuousClock.now + timeout
cancellable = self.sink(
receiveCompletion: { _ in
continuation.resume(returning: nil)
cancellable?.cancel()
},
receiveValue: { value in
if condition(value) {
continuation.resume(returning: value)
cancellable?.cancel()
}
})
Task {
try? await Task.sleep(until: deadline, clock: .continuous)
continuation.resume(returning: nil)
cancellable?.cancel()
}
}
}
}
```

An extension will need to be created that builds on the Publisher extension to work specifically with @Published properties in any ObservableObject. It allows tests to asynchronously wait for a property to reach a desired state, such as loading completion, or data being updated, improving test readability and reliability.

Add the following code to the class:

```
extension ObservableObject {
func waitForPublishedValue(
_ keyPath: KeyPath.Publisher>,
timeout: Duration = .seconds(1),
condition: @escaping (Value) -> Bool
) async -> Value? {
let publisher = self[keyPath: keyPath]
return await publisher.waitForValue(timeout: timeout, condition: condition)
}
}
```

An extension will need to be created that builds on the PublishedRecordingsViewModel to wrap the generic **waitForPublishedValue** method for the **recordings** property. It allows tests to easily wait for the recordings array to update without writing repetitive Combine code, streamlining async unit tests for the ViewModel.

Add the following code to the class:

```
extension PublishedRecordingsViewModel {
func waitForRecordings(
timeout: Duration = .seconds(1),
condition: @escaping ([Recording]) -> Bool
) async -> [Recording]? {
await waitForPublishedValue(\.$recordings, timeout: timeout, condition: condition)
}
}
```

Finally, tests will need to be added to test the ViewModel based on the use cases defined via MockMode enum. These tests contain Swift Testing test cases using @Test and #expect. Each method tests a different scenario for the PublishedRecordingsViewModel: successful fetch, network error, parsing error, and a generic @Published property helper. The tests run asynchronously, use the mock service to control behavior, and assert expected outcomes with #expect, demonstrating how the ViewModel responds to different conditions in a Swift-native, concurrency-friendly testing style.

Add the following code to the class:

```
@MainActor
struct PublishedRecordingsViewModelTests {
@Test("Fetch recordings successfully")
func testFetchRecordingsSuccess() async throws {
let mockService = MockRecordingsService()
mockService.mode = .success
let viewModel = PublishedRecordingsViewModel(
service: mockService,
endpoint: .fetchRecordings
)
viewModel.fetch()
let recordings = await viewModel.waitForRecordings { !$0.isEmpty }
#expect(recordings?.count == 2)
#expect(recordings?.first?.title == "My Electric Guitar Anthem")
#expect(recordings?.last?.title == "My Acoustic Guitar Ballad")
}
@Test("Handle network error properly")
func testFetchRecordingsNetworkError() async throws {
let mockService = MockRecordingsService()
mockService.mode = .networkError
let viewModel = PublishedRecordingsViewModel(
service: mockService,
endpoint: .fetchRecordings
)
viewModel.fetch()
let recordings = await viewModel.waitForRecordings { $0.isEmpty }
#expect(recordings?.isEmpty == true)
}
@Test("Handle parsing error properly")
func testFetchRecordingsParsingError() async throws {
let mockService = MockRecordingsService()
mockService.mode = .parsingError
let viewModel = PublishedRecordingsViewModel(
service: mockService,
endpoint: .fetchRecordings
)
viewModel.fetch()
let recordings = await viewModel.waitForRecordings { $0.isEmpty }
#expect(recordings?.isEmpty == true)
}
@Test("Use generic @Published helper properly")
func testGenericWaitForPublishedValue() async throws {
class DummyViewModel: ObservableObject {
@Published var isLoading = true
func finishLoading() { isLoading = false }
}
let dummy = DummyViewModel()
Task {
try? await Task.sleep(for: .milliseconds(200))
dummy.finishLoading()
}
let isDone = await dummy.waitForPublishedValue(\.$isLoading) { $0 == false }
#expect(isDone == false)
}
}
```

In order to run the test cases, they will need to be added to the Test Plan if they don’t yet appear in the Test Navigator, as follows:

![Screenshot of a code editor displaying a Swift test file for a project named “RecordingsFW.” The left panel shows a file hierarchy with test files and functions. The main panel contains code for testing a view model, including functions to fetch recordings and handle errors. The code uses annotations like `@Test` and `@MainActor`. The bottom panel shows a test run log, indicating all tests passed successfully with an exit code of 0.](images/678928_1_En_3_Chapter/678928_1_En_3_Fig4_HTML.jpg)

Figure 3-4

Test Navigator

![A screenshot of a software interface titled “Choose Test Targets.” It displays a list of test targets with checkboxes for selection. The targets listed are “RecordingsAppTests,” “RecordingsAppUITests,” and “RecordingsFWTests,” with the first and third options checked. There are tabs labeled “All,” “Unit,” and “UI” at the top, and buttons labeled “Cancel” and “Choose” at the bottom.](images/678928_1_En_3_Chapter/678928_1_En_3_Fig3_HTML.jpg)

Figure 3-3

Choose Test Targets

1.  Show the **Test Navigator** (an alternate tab to the Project Navigator) and verify whether the tests exist under RecordingsFW

2.  If the tests don’t exist, select them using the **Edit Test Plan** menu item displayed via icon at the lower left of the Test Navigator (**Figure** [**3-3**](#678928_1_En_3_Chapter.xhtml#Fig3))

3.  Once they appear in the Test Navigator, run them using the corresponding Run icon to the right of the main test hierarchy (**Figure** [**3-4**](#678928_1_En_3_Chapter.xhtml#Fig4))

## Running the App Target

Set the app as the active scheme in the scheme drop-down, build the app, and run it using one of the Xcode simulators or an actual device. The app should compile and run properly, exposing the corresponding framework features (**Figure** [**3-5**](#678928_1_En_3_Chapter.xhtml#Fig5)).

![Screenshot of a development environment showing a mobile app simulation on an iPhone 17 Pro Max. The app displays a list titled “Published Recordings” with two entries: “My (Published) Electric Anthem” and “My (Published) Acoustic Ballad,” both credited to John Doe with the year 2023\. The development interface includes a file directory on the left, showing various Swift files, and project settings on the right.](images/678928_1_En_3_Chapter/678928_1_En_3_Fig5_HTML.jpg)

Figure 3-5

Xcode Simulator

The framework is now capable of displaying both local and remote audio recordings within different mobile apps without the need for code changes.

In this chapter, you enhanced the RecordingsFW Framework to support remote audio recordings and integrated a network API using MVVM. You applied Dependency Injection to decouple the ViewModel from the service implementation and used Combine to handle asynchronous data flows. By completing these steps, you built a flexible, reusable framework capable of supporting multiple apps without requiring changes to the core code, reinforcing modern design principles for scalable iOS development.

With the RecordingsFW Framework extended to support remote recordings and fully decoupled via Dependency Injection, you’re ready to explore dynamic feature enablement. In the next chapter, we’ll design a framework that can expose and manage features at runtime, including functionality unlocked through In-App Purchases.

Footnotes [1](#678928_1_En_3_Chapter.xhtml#Fn1_source)   [2](#678928_1_En_3_Chapter.xhtml#Fn2_source)   [3](#678928_1_En_3_Chapter.xhtml#Fn3_source)   [4](#678928_1_En_3_Chapter.xhtml#Fn4_source)   [5](#678928_1_En_3_Chapter.xhtml#Fn5_source)   [6](#678928_1_En_3_Chapter.xhtml#Fn6_source)   [7](#678928_1_En_3_Chapter.xhtml#Fn7_source)   [8](#678928_1_En_3_Chapter.xhtml#Fn8_source)   [9](#678928_1_En_3_Chapter.xhtml#Fn9_source)  

# 4. Laying the Groundwork for Dynamic Feature Enablement

Designing the RecordingsStoreFW Framework

This chapter will establish the architectural foundation for dynamic feature enablement within iOS frameworks. You’ll design an extensible, modular framework capable of exposing, activating, and managing features at runtime. Simulated In-App Purchases^([^(16)](#fn16)) will be utilized to demonstrate how existing functionality built into the framework can be unlocked to drive revenue growth.

In-App Purchases, comparable to a digital storefront, allow content and functionality to be purchased by users from within an app without the need to download and install a new version of the app. In-App Purchases allow mobile app developers to design feature sets as separate entities that can be purchased based on user experience and need. In-App Purchases are supported by the Apple StoreKit framework, as well as the App Store Connect portal. The integration, configuration, and testing of In-App Purchases is well documented by Apple and will not be the focus of this chapter. This chapter will focus on techniques to enable functionality in external frameworks through the use of simulated In-App Purchases.

In this chapter, we’ll build the RecordingsStoreFW Framework to support dynamic feature enablement in iOS frameworks. You’ll create an extensible, modular system capable of exposing, activating, and managing features at runtime. Using simulated In-App Purchases, we’ll demonstrate how functionality within the framework can be unlocked to drive revenue while maintaining a decoupled architecture. As before, MVVM will structure the framework, with SwiftData supporting the model, SwiftUI and Combine handling the view, and Dependency Injection integrating a stub API into the ViewModel. The ViewModel will integrate a Third-Party stub API by way of Dependency Injection as well.

## Setting Up the iOS Framework and Project

A new iOS Framework and Project will need to be created and configured, similar to Chapter [1](#678928_1_En_1_Chapter.xhtml). Name the project and corresponding files using the **RecordingsStore** prefix, e.g., **RecordingsStoreFW**:

1.  Perform the steps listed under the section “**Creating an iOS Framework**” in Chapter [1](#678928_1_En_1_Chapter.xhtml)

2.  Perform the steps listed under the section “**Configuring an iOS Framework**” in Chapter [1](#678928_1_En_1_Chapter.xhtml)

## Creating a SwiftData Manager

SwiftData, introduced in iOS 17, is a modern transformation of Core Data, built entirely for Swift and tightly integrated with SwiftUI. It uses Swift-native model types marked with the @Model attribute instead of .xcdatamodeld files, automatically handles model schema evolution, and provides a simpler, type-safe API. Data is stored in a ModelContainer, queried through FetchDescriptors, and saved via a lightweight ModelContext. It also integrates seamlessly with SwiftUI’s @Query and @Environment(\.modelContext) to automatically update views when data changes. Essentially, SwiftData replaces Core Data’s complexity with a modern, declarative, and Swift-first design.

SwiftData^([^(17)](#fn17)) will be used to persist simulated In-App Purchases. In-App Purchase transactions and receipts are typically fetched from the Apple App Store when an app is launched, becomes active, or receives a transaction state change related to a purchase. SwiftData will be used to enable features that require purchase. The following section will explain how to integrate SwiftData into an iOS Framework.

**Create a new file to manage SwiftData interactions:**

1.  Right-click the folder within the **Project Navigator** and select **New File from Template…**

2.  Select the **Swift File** template under the **Source** group and save the file as **SwiftDataManager.swift**

**Import the required frameworks:**

```
import Foundation
import SwiftData
import Combine
```

**Define the SwiftData models:**

In SwiftData, models are defined as pure Swift types annotated with @Model. This replaces Core Data’s .xcdatamodeld file and NSManagedObject subclasses. Each stored property automatically becomes a persisted attribute, so entities or attribute types don’t need to be manually defined.

The @Attribute(.unique) modifier ensures that each record `id` is unique across the data store, similar to a primary key.

SwiftData automatically generates schema metadata, migration support, and persistent storage for this class, so a Core Data model file no longer needs to be maintained.

```
@Model
public final class Configuration {
@Attribute(.unique) public var id: UUID
public var publishRecordings: Bool
public var trackCount: Int16
public init(id: UUID = UUID(), publishRecordings: Bool = false, trackCount: Int16 = 0) {
self.id = id
self.publishRecordings = publishRecordings
self.trackCount = trackCount
}
}
```

**Define the Class:**

SwiftDataManager will be implemented as a singleton to ensure the app interacts with one consistent persistence layer throughout its life cycle.

It will conform to ObservableObject so it can integrate directly with SwiftUI’s data flow. Any SwiftUI view that observes SwiftDataManager.shared using @StateObject or @ObservedObject will automatically refresh when one of its @Published properties changes.

A Configuration property will be defined and marked as @Published, so that when the app loads, saves, or modifies it, SwiftUI views instantly update.

A **ModelContainer** and **ModelContext** will also be defined. ModelContainer replaces Core Data’s NSPersistentContainer and defines which models exist in the schema and where data is stored. ModelContext replaces NSManagedObjectContext and allows interactions such as insert, fetch, update, and delete with those models.

```
public class SwiftDataManager: ObservableObject {
public static let shared = SwiftDataManager()
@Published public var configuration: Configuration?
private let modelContainer: ModelContainer
private let context: ModelContext
}
```

**Initialize the SwiftData environment:**

The class initializer will build the entire SwiftData environment at launch. ModelContainer(for: Configuration.self) will create a container managing only the Configuration model, and ModelContext(modelContainer) will establish the working context for persistence operations.

fetchConfiguration() will immediately load any existing configuration from disk into the observable Configuration property, ensuring the app always starts with up-to-date settings.

```
private init() {
do {
// Correct initialization of ModelContainer
modelContainer = try ModelContainer(for: Configuration.self)
context = ModelContext(modelContainer)
fetchConfiguration()
} catch {
fatalError("Failed to create ModelContainer: \(error)")
}
}
public func fetchConfiguration() {
let fetchDescriptor = FetchDescriptor()
do {
if let firstConfig = try context.fetch(fetchDescriptor).first {
self.configuration = firstConfig
}
} catch {
print("Fetch failed: \(error)")
}
}
```

**Persist the SwiftData models and context:**

saveConfiguration() will update an existing configuration or create a new one if none exists. If a new instance is created, it will be inserted into the context and then populated with the new values. After applying updates to the configuration model, it will call context.save() to commit in-memory changes to the persistent store.

```
public func saveConfiguration(existing: Configuration? = nil, publishRecordings: Bool, trackCount: Int16) {
let config: Configuration
if let existing = existing {
config = existing
} else {
config = Configuration()
context.insert(config)
}
config.publishRecordings = publishRecordings
config.trackCount = trackCount
do {
try context.save()
self.configuration = config
} catch {
print("Save failed: \(error)")
}
}
```

## Configuring a Stub API Service

This chapter will use a stub API service called OHHTTPStubs to return JSON data, representing In-App Purchases, from a simulated network API request. Dependency Injection will be used to inject the service and endpoint into the ViewModel, similar to how it was done in Chapter [3](#678928_1_En_3_Chapter.xhtml). Use of this pattern reinforces decoupling.

Create a file called **IAP****.json** to hold the following JSON data to represent available In-App Purchases:

```
[
{
"category": "Publishing",
"name": "Publish Recordings",
"price": 3.99,
"type": "publishRecordings" },
{
"category": "Recording",
"name": "2 Recording Tracks",
"price": 0.99,
"type": "twoTracks" },
{
"category": "Recording",
"name": "3 Recording Tracks",
"price": 1.99,
"type": "threeTracks" },
{
"category": "Recording",
"name": "4 Recording Tracks",
"price": 2.99,
"type": "fourTracks" }
]
```

Create a new class named **IAP.swift** and define an enum for the endpoint. The URL in **.fetchInAppPurchases** is arbitrary, but the host should match the one that will be used when the stub API service is integrated:

```
enum IAPEndpoint {
case fetchInAppPurchases
var urlString: String {
switch self {
case .fetchInAppPurchases:
return "https://graphixware.com/iaps"
}
}
}
```

Define an enum to map error conditions:

```
enum IAPError: Error {
case request(message: String)
case network(message: String)
case status(message: String)
case parsing(message: String)
case other(message: String)
}
```

## Integrating OHHTTPStubs

OHHTTPStubs^([^(18)](#fn18)) is a library that allows a network API request to be intercepted and mocked. It’s ideal for simulating network requests in reference apps. This chapter will use it to simulate the retrieval of available In-App-Purchases which are normally configured for an iOS app via App Store Connect. In an actual application, the StoreKit API^([^(19)](#fn19)) would be used instead.

OHHTTPStubs can be installed using CocoaPods or the Swift Package Manager. **In order for this framework to remain as a** **.xcodeproj****, the Swift Package option will be used.**

![Screenshot of a software package manager interface displaying the “OHHTTPStubs” package. The interface shows the package’s repository link, version compatibility, and support for Swift, Carthage, and SPM. A description explains the package’s purpose in stubbing network requests and testing apps with fake network data. The interface includes options to add the package to a project and a notification indicating the project already depends on “ohhttpstubs.”](images/678928_1_En_4_Chapter/678928_1_En_4_Fig1_HTML.jpg)

Figure 4-1

Swift Package Manager

1.  Right-click in an empty area within the **Project Navigator** and select **Add Package Dependencies…**

2.  Enter the following OHHTTPStubs URL in the Search field: [`https://github.com/AliSoftware/OHHTTPStubs`](https://github.com/AliSoftware/OHHTTPStubs)

3.  Select **Add Package**, and then select **OHHTTPStubsSwift** for the Package Product in the corresponding pop-up (**Figure** [**4-1**](#678928_1_En_4_Chapter.xhtml#Fig1))

## Designing a Generic Service

Create a new class named **IAPService.swift** to manage In-App Purchases. To prevent multiple instances of the class from being created, define it as a singleton:

```
import Foundation
class IAPService {
public static let shared = IAPService()
private init() {
}
}
```

Create a protocol for the service (before the class definition), which will be used by the ViewModel:

```
import Foundation
import Combine
protocol StubAPIService {
func fetch(_t: T.Type, url: URL) -> AnyPublisher where T: Decodable
}
```

As in the previous chapter, **fetch****()** will utilize **Swift** **Generics** to allow flexible, reusable functions and types to be created that can work with different types, avoiding duplication of code.^([^(20)](#fn20)) When the **fetch()** function is called, the desired type of object to be returned from the network API request is passed into the function.

```
e.g. service.fetch(_t: IAP.self, url: url)
```

OHHTTPStubs library will be integrated into the **init()** function to intercept the network API request and mock its response. The Host name should be replaced with your own domain name. If you don’t currently own one, the one used in the sample code can be used. All that is needed is a variable to hold a “stub descriptor,” which will define a condition for intercepting the request (e.g., host match), and a response using the JSON data previously defined in IAP.json. The **onStubActivation()** callback can be used to monitor the request being stubbed. The OHHTTPStubs frameworks will also need to be imported:

```
internal import OHHTTPStubs
internal import OHHTTPStubsSwift
class IAPService: StubAPIService {
public static let shared = IAPService()
weak var stubDesc: HTTPStubsDescriptor?
private init() {
stubDesc = stub(condition: isHost("graphixware.com")) { request in
return HTTPStubsResponse(fileAtPath: OHPathForFile("IAP.json", type(of: self))!, statusCode: 200,
headers: ["Content-Type":"application/json"]
)
}
stubDesc?.name = "IAP.json"
HTTPStubs.onStubActivation { (request: URLRequest, stub: HTTPStubsDescriptor, response: HTTPStubsResponse) in
print("IAPService.init(): Request to \(request.url!) has been stubbed with \((stub.name != nil) ? stub.name! : "?")")
}
}
}
```

Note

The OHHTTPStubs and OHHTTPStubsSwift frameworks were imported using an internal access modifier to resolve a compiler warning that the modules were not compiled using “library evolution support.” Since an actual app would never be shipped with these test frameworks, resolving the warnings is sufficient for this example.

Adopt the StubAPIService protocol and implement its required behavior:

```
func fetch(_t: T.Type, url: URL) -> AnyPublisher where T : Decodable {
return URLSession.shared.dataTaskPublisher(for: url)
.mapError { error in
IAPError.network(message: error.localizedDescription)
}
.flatMap(maxPublishers: .max(1)) { pair in
self.decode(pair.data)
}
.eraseToAnyPublisher()
}
private func decode(_ data: Data) -> AnyPublisher {
let decoder = JSONDecoder()
decoder.dateDecodingStrategy = .secondsSince1970
return Just(data)
.decode(type: T.self, decoder: decoder)
.mapError { error in
IAPError.parsing(message: error.localizedDescription)
}
.eraseToAnyPublisher()
}
```

The return value of the **fetch** function is defined as **AnyPublisher****<>**. **AnyPublisher** is a concrete implementation of **Publisher** that has no significant properties of its own and merely acts as a pass-through object. A Publisher delivers elements to one or more Subscriber instances. The subscriber’s Input and Failure associated types must match the Output and Failure types declared by the publisher. The publisher implements the receive(subscriber:) method to accept a subscriber.

**mapError** will convert any failure from the upstream publisher into a new error.

**flatMap** will transform elements from an upstream publisher into a new publisher up to a maximum number of publishers you specify. In our example, flatMap decodes the output from the upstream publisher using a specified decoder to produce **IAP** models.

**eraseToAnyPublisher** will wrap this publisher with a type eraser to expose an instance of AnyPublisher to the downstream subscriber rather than this publisher’s actual type.

## Designing a ViewModel Using Dependency Injection

Once the stub remote service has been created, a ViewModel will be needed to fetch the published recording metadata and notify the View to update its User Interface treatment.

Create a new protocol, **RefreshableViewModel,** in **IAP****.swift**. The View will rely on the protocol’s contract being fulfilled by the ViewModel to meet its own requirements.

```
protocol RefreshableViewModel {
func getIAPs() -> [IAP]
func refresh()
}
```

Create an enum and struct to support In-App Purchases to be displayed in the View. The **IAPType** enum will include a case for each type of In-App Purchase. The **IAP** struct will include a category to support a grouped table treatment in the View. The **IAP** struct will need to be **Decodable**^([^(21)](#fn21)) per the fetch protocol, **Hashable**^([^(22)](#fn22)) per the SwiftUI List, and **Identifiable**^([^(23)](#fn23)) to satisfy the signature for a SwiftUI **ForEach**:

```
enum IAPType: String, Decodable {
case publishRecordings = "publishRecordings"
case twoTracks = "twoTracks"
case threeTracks = "threeTracks"
case fourTracks = "fourTracks"
}
struct IAP: Hashable, Identifiable, Decodable {
var id: String { name }
let category: String
let name: String
let price: Double
let type: IAPType
init(category: String, name: String, price: Double, type: IAPType) {
self.category = category
self.name = name
self.price = price
self.type = type
}
}
```

The following steps will need to be performed to define the IAPViewModel. The actual code to support these steps will follow.

Create a new ViewModel class named **IAPViewModel.swift** and adopt the **RefreshableViewModel** protocol to ensure the required behavior needed by the View is supported. The ViewModel will also adopt the **ObservableObject** protocol to support “pre-publishing” changes.

Create **service** and **endpoint** variables to support Dependency Injection. Since these are external dependencies, they will be injected into the ViewModel via constructor to maintain decoupling within the ViewModel.

Define an **iaps** variable using the **@Published** typealias. It will maintain the transformed data returned from the service and publish change events to subscribers whenever it is set.

Create a **fetch****()** function to fetch sample metadata generically using the injected service and endpoint, store it in the **iaps** variable, and notify subscribers of the change.

Add the following code to the IAPViewModel class:

```
import Foundation
import Combine
class IAPViewModel: ObservableObject, RefreshableViewModel {
// Dependency Injection...
private let service: StubAPIService
private let endpoint: IAPEndpoint
private var disposables = Set()
@Published private(set) var iaps: [IAP] = []
init(service: StubAPIService, endpoint: IAPEndpoint) {
self.service = service
self.endpoint = endpoint
}
func getIAPs() -> [IAP] {
return iaps
}
func refresh() {
fetch()
}
private func fetch() {
if let url = URL(string: endpoint.urlString) {
service.fetch(_t: IAP.self, url: url)
.receive(on: DispatchQueue.main)
.sink { [weak self] value in
switch value {
case .failure:
self?.iaps = []
case .finished:
break
}
} receiveValue: { [weak self] response in
self?.iaps = response
}
.store(in: &disposables)
}
}
}
```

## Integrating the ViewModel into the View

In order to complete the MVVM design pattern for this example, the ViewModel will need to be integrated into the View. In this framework, the View will be created using SwiftUI. To integrate a SwiftUI view into a UIKit view hierarchy, the view will need to be embedded within a UIHostingController, with the UIHostingController being specified as the root view controller of the framework’s Storyboard.

Create a SwiftUI class named **RecordingsStoreView** using the **SwiftUI** **View** template under the **User Interface** group. The generated code will include a default View, as well as #Preview, which Xcode uses to generate a preview of the View as it’s being designed in the SwiftUI editor.

Create a UIKit class named **RecordingsStoreVC** using the **Cocoa Touch** **Class** template under the **Source** group (the “**Subclass of:”** field is arbitrary and will be changed to UIHostController after the class has been created).

**RecordingsStoreVC** will need to include a generic in the class definition for the class type being embedded, e.g., **RecordingsStoreView**. The View initialization will include a ViewModel input parameter:

```
import Foundation
import SwiftUI
class RecordingsStoreVC: UIHostingController {
required init?(coder: NSCoder) {
super.init(coder: coder, rootView: RecordingsStoreView(viewModel:
IAPViewModel(service: IAPService.shared, endpoint: .fetchInAppPurchases)))
}
}
```

Note

The code will not compile until the RecordingsStoreView class has been created.

Create a storyboard named **RecordingsStoreUI.storyboard** using the **Storyboard** template under the **User Interface** group, and configure it to host a SwiftUI view:

1.  Select **RecordingsStoreUI.storyboard** in the **Project Navigator**

2.  Select the root view controller in the **Document Outline** of the **Interface Builder** and delete it

3.  Select the **Library** icon in the **Interface Builder** and add a **Hosting** **View** **Controller**

4.  Set **Class** under the **Custom Class** section of the **Identity Inspector** to **RecordingsStoreVC**

5.  Set **Storyboard** **ID** under the **Identity** section of the **Identity Inspector** to **RecordingsStoreVC**

6.  Set **Is Initial** **View** **Controller** under the **View Controller** section of the **Attributes** **Inspector** to checked

## Designing the SwiftUI View

The following code will need to be added to the **RecordingsStoreView** class. Since it’s fairly involved, it will be documented around specific code snippets. The entire class can be copied using the code at the end of this section.

Variables will be defined as **@State** property wrappers. State property wrappers can read and write values managed by SwiftUI. State is used as the “single source of truth” for a given value type stored in a view hierarchy.^([^(24)](#fn24))

The ViewModel will be defined as a **StateObject** property wrapper. StateObject proper wrappers can instantiate an observable object. StateObject is used as the “single source of truth” for a reference type stored in a view hierarchy.^([^(25)](#fn25))

```
@State private var selectedIAP: IAP?
@State private var showAlert = false
@State private var loaded = false
@StateObject private var viewModel: IAPViewModel
init(viewModel: IAPViewModel) {
_viewModel = StateObject(wrappedValue: viewModel)
}
```

**groupByCategory()** is a sorting algorithm that sorts In-App Purchases alphabetically by category.

```
func groupByCategory(_ iaps: [IAP]) -> [(String, [IAP])] {
let grouped = Dictionary(grouping: iaps, by: { $0.category })
return grouped.sorted(by: { $0.key < $1.key })
}
```

The View body will be defined to be a SwiftUI **List**, which is comparable to a UIKit table view. Each row will correspond to an In-App Purchase. If an In-App Purchase has previously been made, the row will be disabled. Selecting a row will display a prompt to the user to proceed with the (simulated) purchase. If the user chooses to continue, the transaction will be persisted using SwiftData and propagated via UserDefaults.

Note

In an actual iOS app that integrates In-App Purchases, the StoreKit framework will be used to perform the transaction, receive transaction state changes, and validate the transaction receipt.

```
var body: some View {
NavigationView {
List(selection: $selectedIAP) {
ForEach(groupByCategory(viewModel.getIAPs()), id:\.0) { iap in
Section(header: Text("\(iap.0)")
.foregroundColor(.black)
.font(.custom("HelveticaNeue-Bold", fixedSize: 20.0))
){
ForEach(iap.1) { iap in
HStack {
Button("\(Image(systemName: "music.note")) \(iap.name)") {
showAlert = true
selectedIAP = iap
}
.font(.custom("HelveticaNeue", fixedSize: 16.0))
Spacer()
Text("\(NumberFormatter.localizedString(from: NSNumber(value: iap.price), number: .currency))")
.font(.custom("HelveticaNeue-Bold", fixedSize: 16.0))
}
.listRowSeparator(.hidden)
.disabled(isPurchased(iap: iap))
}
}
}
}
.listStyle(.plain)
.navigationBarTitle("Pro Features", displayMode: .large)
.alert(isPresented: self.$showAlert) {
Alert(
title: Text("Pro Features"),
message: Text("Do you want to purchase '\(selectedIAP?.name ?? "?")'"),
primaryButton: .default(Text("OK")) {
if let iap = selectedIAP {
updateFeatures(type: iap.type)
}
},
secondaryButton: .default(Text("Cancel"))
)
}
}
}
```

**onAppear()** will be used to fetch In-App Purchases when the view first appears by calling **ViewModel****.refresh()** to trigger the underlying service request:

```
.onAppear {
if !loaded {
loaded = true
viewModel.refresh()
}
}
```

**isPurchased()** will utilize UserDefaults to reflect the state of In-App Purchase transactions persisted via SwiftData.

```
func isPurchased(iap: IAP) -> Bool {
if let features = UserDefaults.standard.object(forKey: "iaps") as? [String: Any] {
switch iap.type {
case .publishRecordings:
return features["publishRecordings"] as? Bool ?? false
case .twoTracks:
if let trackCount = features["trackCount"] as? Int16 { return trackCount > 1 }
return false
case .threeTracks:
if let trackCount = features["trackCount"] as? Int16 { return trackCount > 2 }
return false
case .fourTracks:
if let trackCount = features["trackCount"] as? Int16 { return trackCount > 3 }
return false
}
}
return false
}
```

**updateFeatures()** will persist a new In-App Purchase transaction via SwiftData.

```
func updateFeatures(type: IAPType) {
guard let config = dataManager.configuration else {
saveConfiguration(existing: nil, type: type)
return
}
saveConfiguration(existing: config, type: type)
}
private func saveConfiguration(existing: Configuration?, type: IAPType) {
var publishRecordings = false
var trackCount: Int16 = 1
switch type {
case .publishRecordings:
publishRecordings = true
trackCount = existing?.trackCount ?? 1
case .twoTracks:
publishRecordings = existing?.publishRecordings ?? false
trackCount = 2
case .threeTracks:
publishRecordings = existing?.publishRecordings ?? false
trackCount = 3
case .fourTracks:
publishRecordings = existing?.publishRecordings ?? false
trackCount = 4
}
dataManager.saveConfiguration(existing: existing, publishRecordings: publishRecordings, trackCount: trackCount)
UserDefaults.standard.set(["publishRecordings": publishRecordings, "trackCount": trackCount], forKey: "iaps")
}
```

Now you can replace the default code in **RecordingsStoreView** with the following:

```
import SwiftUI
struct RecordingsStoreView: View {
@State private var selectedIAP: IAP?
@State private var showAlert = false
@State private var loaded = false
@StateObject private var viewModel: IAPViewModel
@State private var fontName: String
@State private var fontSize: CGFloat
@StateObject private var dataManager = SwiftDataManager.shared
init(viewModel: IAPViewModel, fontName: String = "HelveticaNeue", fontSize: CGFloat = 16.0) {
_viewModel = StateObject(wrappedValue: viewModel)
self.fontName = fontName
self.fontSize = fontSize
}
func groupByCategory(_ iaps: [IAP]) -> [(String, [IAP])] {
let grouped = Dictionary(grouping: iaps, by: { $0.category })
return grouped.sorted(by: { $0.key  Bool {
if let features = UserDefaults.standard.object(forKey: "iaps") as? [String: Any] {
switch iap.type {
case .publishRecordings:
return features["publishRecordings"] as? Bool ?? false
case .twoTracks:
if let trackCount = features["trackCount"] as? Int16 { return trackCount > 1 }
return false
case .threeTracks:
if let trackCount = features["trackCount"] as? Int16 { return trackCount > 2 }
return false
case .fourTracks:
if let trackCount = features["trackCount"] as? Int16 { return trackCount > 3 }
return false
}
}
return false
}
func updateFeatures(type: IAPType) {
guard let config = dataManager.configuration else {
saveConfiguration(existing: nil, type: type)
return
}
saveConfiguration(existing: config, type: type)
}
private func saveConfiguration(existing: Configuration?, type: IAPType) {
var publishRecordings = false
var trackCount: Int16 = 1
switch type {
case .publishRecordings:
publishRecordings = true
trackCount = existing?.trackCount ?? 1
case .twoTracks:
publishRecordings = existing?.publishRecordings ?? false
trackCount = 2
case .threeTracks:
publishRecordings = existing?.publishRecordings ?? false
trackCount = 3
case .fourTracks:
publishRecordings = existing?.publishRecordings ?? false
trackCount = 4
}
dataManager.saveConfiguration(existing: existing, publishRecordings: publishRecordings, trackCount: trackCount)
UserDefaults.standard.set(["publishRecordings": publishRecordings, "trackCount": trackCount], forKey: "iaps")
}
}
struct RecordingsStoreView_Previews: PreviewProvider {
static var previews: some View {
RecordingsStoreView(viewModel: IAPViewModel(service: IAPService.shared, endpoint: .fetchInAppPurchases))
}
}
```

## Creating a Swift Protocol

Since protocols will still be used to integrate the framework into an app target, create a new class named **RecordingsStoreProtocol.swift** and define the protocol:

```
import Foundation
import UIKit
protocol DisplayableViewController {
func instantiateRootViewController() -> UIViewController
}
```

Adopt the protocol:

```
public class RecordingsStoreProtocol: DisplayableViewController {
public init() {}
public func instantiateRootViewController() -> UIViewController {
let storyboard = UIStoryboard(name: "RecordingsStoreUI", bundle: Bundle(for: RecordingsStoreVC.self))
let vc = storyboard.instantiateViewController(withIdentifier: "RecordingsStoreVC")
return vc
}
}
```

## Creating an App Target

As was done in previous chapters, create and configure an App Target to integrate the framework.

1.  Perform the steps listed under the section “**Creating an App Target**” in Chapter [1](#678928_1_En_1_Chapter.xhtml)

2.  Perform the steps listed under the section “**Creating an** **App Target** **User Interface**” in Chapter [1](#678928_1_En_1_Chapter.xhtml)

3.  Perform the steps listed under the section “**Designing an** **App Target** **User Interface**” in Chapter [1](#678928_1_En_1_Chapter.xhtml)

4.  Perform the steps listed under the section “**Adding an iOS Framework**” in Chapter [1](#678928_1_En_1_Chapter.xhtml), and then select **RecordingsStoreFW.framework**

## Integrating the iOS Framework

The framework user interface will be integrated into the app from within **SceneDelegate.willConnectTo**, as follows:

```
import RecordingsStoreFW
func scene(_ scene: UIScene, willConnectTo session: UISceneSession, options connectionOptions: UIScene.ConnectionOptions) {
guard let winScene = (scene as? UIWindowScene) else { return }
if let storyboard = session.configuration.storyboard {
if let tabBarController = storyboard.instantiateInitialViewController() as? UITabBarController {
window = UIWindow(windowScene: winScene)
window?.rootViewController = tabBarController
window?.makeKeyAndVisible()
// Add framework User Interfaces via protocol...
var tbcViewControllers = tabBarController.viewControllers ?? []
tbcViewControllers.append(RecordingsStoreProtocol().instantiateRootViewController())
tabBarController.setViewControllers(tbcViewControllers, animated: false)
tabBarController.tabBar.isHidden = (tbcViewControllers.count <= 1)
}
}
}
```

## Running the App Target

Set the app as the active scheme in the scheme drop-down, build the app, and run it using one of the Xcode simulators or an actual device. The app should compile and run properly, exposing the corresponding framework features (**Figure** [**4-2**](#678928_1_En_4_Chapter.xhtml#Fig2)).

![A screenshot of a mobile app interface displayed on an iPhone 17 Pro Max simulator within a development environment. The app screen shows “Pro Features” with options for “Publishing” and “Recording.” Under “Publishing,” “Publish Recordings” is listed for $3.99\. Under “Recording,” options include “2 Recording Tracks” for $0.99, “3 Recording Tracks” for $1.99, and “4 Recording Tracks” for $2.99\. The development environment in the background shows project details and resource usage.](images/678928_1_En_4_Chapter/678928_1_En_4_Fig2_HTML.jpg)

Figure 4-2

Xcode Simulator

## Managing In-App Purchases

Simulated In-App Purchases can be made by selecting a row in the table and agreeing to make the purchase. Once a purchase has been made, it will be persisted via SwiftData, and a UserDefault corresponding to that purchase will be created. All code running within the containing app, including other frameworks, will have access to that UserDefault and can enable or disable features accordingly. Shutting down and running the app again will reflect the persisted state of these In-App Purchases (**Figure** [**4-3**](#678928_1_En_4_Chapter.xhtml#Fig3)).

![A screenshot of an Xcode development environment displaying a mobile app interface on an iPhone simulator. The app screen titled “Add Features” lists options for purchasing features: “Publish Recordings” for $3.99, “2 Recording Tracks” for $0.99, “3 Recording Tracks” for $1.99, and “4 Recording Tracks” for $2.99\. The Xcode interface shows project files and settings on the left and right panels.](images/678928_1_En_4_Chapter/678928_1_En_4_Fig3_HTML.jpg)

Figure 4-3

Xcode Simulator

In this chapter, you developed the RecordingsStoreFW framework to enable dynamic feature activation via simulated In-App Purchases. By leveraging MVVM, SwiftData, SwiftUI, Combine, and Dependency Injection, you built a decoupled, modular framework capable of driving revenue and managing runtime features. This foundation positions you to implement flexible, monetizable functionality in multiple apps without modifying the core framework.

After creating a framework that enables dynamic features with In-App Purchases, you’re ready to see how this approach scales across multiple frameworks. In the next chapter, we’ll build a framework that demonstrates unlockable functionality in different contexts while maintaining decoupling between frameworks.

Footnotes [1](#678928_1_En_4_Chapter.xhtml#Fn1_source)   [2](#678928_1_En_4_Chapter.xhtml#Fn2_source)   [3](#678928_1_En_4_Chapter.xhtml#Fn3_source)   [4](#678928_1_En_4_Chapter.xhtml#Fn4_source)   [5](#678928_1_En_4_Chapter.xhtml#Fn5_source)   [6](#678928_1_En_4_Chapter.xhtml#Fn6_source)   [7](#678928_1_En_4_Chapter.xhtml#Fn7_source)   [8](#678928_1_En_4_Chapter.xhtml#Fn8_source)   [9](#678928_1_En_4_Chapter.xhtml#Fn9_source)   [10](#678928_1_En_4_Chapter.xhtml#Fn10_source)  

# 5. Expanding the Dynamic Feature Enablement Example

Designing the RecordingStudioFW Framework

Building on the concepts from Chapter [4](#678928_1_En_4_Chapter.xhtml), this chapter presents another framework to demonstrate how dynamic feature enablement can be applied in different contexts. You’ll implement a similar architecture to show how multiple frameworks can independently support unlockable, purchase-driven features, reinforcing the flexibility and scalability of this approach across your app ecosystem. Essentially, In-App Purchases made from within one framework will unlock functionality within other frameworks, without introducing a tight coupling between these frameworks.

This chapter contains a User Interface that mocks a multitrack recording studio. In order to understand the use case clearly, the following is a brief description of **multitrack recording**:

*Multitrack recording allows musicians to record each instrument separately to their own audio recording, or track, enabling flexible editing, re-recording, and applying of effects, EQ, or volume adjustments to those individual tracks before mixing them into a final song.*

In this chapter, we’ll design the RecordingStudioFW Framework to extend dynamic feature enablement across multiple frameworks. Using a similar architecture to the previous chapter, you’ll implement unlockable, purchase-driven features that operate independently, demonstrating how In-App Purchases in one framework can activate functionality in others without introducing tight coupling. A mock multitrack recording studio interface will provide a practical context for these features, illustrating flexibility and scalability in a modular app ecosystem.

## Setting Up the iOS Framework and Project

A new iOS Framework and Project will need to be created and configured, similar to Chapter [1](#678928_1_En_1_Chapter.xhtml). Name the project and corresponding files using the **RecordingStudio** prefix, e.g., **RecordingStudioFW**:

1.  Perform the steps listed under the section “**Creating an iOS Framework**” in Chapter [1](#678928_1_En_1_Chapter.xhtml)

2.  Perform the steps listed under the section “**Configuring an iOS Framework**” in Chapter [1](#678928_1_En_1_Chapter.xhtml)

3.  Perform the steps listed under the section “**Creating a Storyboard**” in Chapter [1](#678928_1_En_1_Chapter.xhtml)

## Creating a Property List

A Property List will need to be added to the project to control the framework’s mode of operation, namely, track enablement:

![Screenshot of a development environment showing a project named “RecordingStudioFW.” The left panel lists project files, including “RecordingStudio.plist,” which is selected. The center panel displays the contents of “RecordingStudio.plist,” showing a dictionary with a key “trackCount” set to the number 1\. The right panel provides details about the file, including its name, type, and location.](images/678928_1_En_5_Chapter/678928_1_En_5_Fig1_HTML.jpg)

Figure 5-1

Property List Editor

1.  Right-click the folder within the **Project Navigator** and select **New File from Template…**

2.  Select the **Property List** template under the **Resource** group and name it **RecordingStudio.plist**

3.  Select **RecordingStudio.plist** in the **Project Navigator**

4.  Select the **+** icon in the empty row and create a setting called **trackCount**, with a type of Number, and default value of 1 (**Figure** [**5-1**](#678928_1_En_5_Chapter.xhtml#Fig1))

## Creating a Cocoa Touch Class

The User Interface for this framework will mock a 4-track digital recording studio. Each track will be represented by an image containing typical controls found on a recording studio console. One track will be enabled by default.

Since this framework doesn’t contain an In-App Purchases view to control track enablement, a Property List will be used to do so, allowing the framework to be statically configured and used within a stand-alone app. Once this framework has been integrated within an app that includes the In-App Purchases framework, In-App Purchases will control track enablement.

1.  Right-click the folder within the Project Navigator and select **New File from Template…**

2.  Choose the **Cocoa Touch** **Class** template under the **Source** section and **Next**

3.  Input **RecordingStudioVC** as the class name, and set **UIViewController** as the subclass

4.  Ensure **Also create** **XIB** **file** is unchecked and **Language** is Swift

The following code will be automatically generated:

```
import UIKit
class RecordingStudioVC: UIViewController {
override func viewDidLoad() {
super.viewDidLoad()
}
}
```

Four “image” view components will need to be defined using @IBOutlet so that they can be connected via Interface Builder to their respective User Interface components. Add the following to the class:

```
class RecordingStudioVC: UIViewController {
@IBOutlet weak var track1ImageView: UIImageView!
@IBOutlet weak var track2ImageView: UIImageView!
@IBOutlet weak var track3ImageView: UIImageView!
@IBOutlet weak var track4ImageView: UIImageView!
}
```

For consistency with other frameworks developed in this book, add the following title attributes to the navigation controller:

```
override func viewDidLoad() {
super.viewDidLoad()
self.navigationController?.navigationBar.prefersLargeTitles = true
self.navigationItem.largeTitleDisplayMode = .always
}
```

**viewWillAppear()** will be used to enable/disable track components immediately before their respective view becomes visible. The Property List setting will be used by default and, eventually, overridden by UserDefaults reflecting In-App Purchases.

In the mock User Interface, track enablement will simply consist of enabling/disabling user interaction for the image view component, as well as changing the opacity of the image view layer to give the appearance of it being enabled/disabled. Add the following to the class:

```
override func viewWillAppear(_ animated: Bool) {
super.viewWillAppear(animated)
var trackCount = 1
if let bundle = Bundle(identifier:"com.gw.RecordingStudioFW"),
let path = bundle.path(forResource: "RecordingStudio", ofType: "plist"),
let plistDict = NSDictionary(contentsOfFile: path),
let tracks = plistDict.object(forKey: "trackCount") as? Int {
trackCount = tracks
}
if let features = UserDefaults.standard.object(forKey: "iaps") as? [String: Any],
let tracks = features["tracks"] as? Int {
trackCount = tracks
}
switch trackCount {
case 2:
initializeImageView(imageView: self.track1ImageView, enableTrack: true)
initializeImageView(imageView: self.track2ImageView, enableTrack: true)
initializeImageView(imageView: self.track3ImageView, enableTrack: false)
initializeImageView(imageView: self.track4ImageView, enableTrack: false)
case 3:
initializeImageView(imageView: self.track1ImageView, enableTrack: true)
initializeImageView(imageView: self.track2ImageView, enableTrack: true)
initializeImageView(imageView: self.track3ImageView, enableTrack: true)
initializeImageView(imageView: self.track4ImageView, enableTrack: false)
case 4:
initializeImageView(imageView: self.track1ImageView, enableTrack: true)
initializeImageView(imageView: self.track2ImageView, enableTrack: true)
initializeImageView(imageView: self.track3ImageView, enableTrack: true)
initializeImageView(imageView: self.track4ImageView, enableTrack: true)
default:
initializeImageView(imageView: self.track1ImageView, enableTrack: true)
initializeImageView(imageView: self.track2ImageView, enableTrack: false)
initializeImageView(imageView: self.track3ImageView, enableTrack: false)
initializeImageView(imageView: self.track4ImageView, enableTrack: false)
}
}
func initializeImageView(imageView: UIImageView, enableTrack: Bool) {
imageView.image = UIImage(named: "AudioMixer_Track", in: Bundle(for: RecordingStudioVC.self), with: nil)
imageView.isUserInteractionEnabled = enableTrack ? true : false
imageView.layer.opacity = enableTrack ? 1.0 : 0.25
imageView.layer.cornerRadius = 10.0
imageView.layer.masksToBounds = true
imageView.layer.borderWidth = 1.0
imageView.layer.borderColor = UIColor(red: 220.0/255.0, green: 220.0/255.0, blue: 220.0/255.0, alpha: 1.0).cgColor
}
```

## Creating an Asset Catalog

Asset Catalogs^([^(26)](#fn26)) are typically used to organize and manage images used within an iOS app. This framework will contain an image representing a recording console track, which will be used for each IBOutlet. The image can be found under this framework’s downloaded code.

![Screenshot of a development environment interface showing a project named “RecordingStudioFW.” The left panel lists project files, including “RecordingStudio.xcassets” and various Swift files. The center panel displays an asset named “AudioMixer_Track” with a preview. The right panel shows asset properties, including rendering options, compression settings, and device compatibility, such as iPhone, iPad, and Mac. Various appearance and scaling options are also visible.](images/678928_1_En_5_Chapter/678928_1_En_5_Fig2_HTML.jpg)

Figure 5-2

Asset Catalog Editor

1.  Right-click the folder within the **Project Navigator** and select **New File from Template…**

2.  Select the **Asset Catalog** template under the **Resource** group and name it **RecordingStudio.xcassets**

3.  Select **RecordingStudio.xcassets** in the **Project Navigator**

4.  Select the **+** icon along the bottom of the asset catalog document and choose **Import…**

5.  Select **AudioMixer_Track.png** and set it to a Universal, Single Scale image (**Figure** [**5-2**](#678928_1_En_5_Chapter.xhtml#Fig2))

## Creating a User Interface

The User Interface for this framework will mock a 4-track digital recording studio. Each track will be represented by an image depicting typical controls found on a recording studio console. You can practice developing this UI using Interface Builder, or you can download the source code from GitHub and replace the Storyboard file contents as follows:

1.  Right-click **RecordingStudioUI.storyboard** in the Project Navigator and select **Open As/Source Code** from the pop-up menu

2.  Delete the file contents displayed in the source code editor

3.  Copy the storyboard file contents from the downloaded **RecordingStudioUI.storyboard** file and paste it into the editor

4.  Right-click **RecordingStudioUI.storyboard** in the Project Navigator and select **Open As/Interface Builder** **-** **Storyboard** from the pop-up menu

The view for this User Interface is composed of a horizontal axis stack view (filled equally) and four image views, each containing the image added via asset catalog, connected to the @IBOutlet variables added to the RecordingStudioVC class (**Figure** [**5-3**](#678928_1_En_5_Chapter.xhtml#Fig3)).

![Screenshot of an Xcode interface displaying a storyboard for a recording studio app. The left panel shows a project navigator with files like “RecordingStudioFW” and “RecordingStudioFWTests.” The main section features two iPhone mockups: one labeled “Navigation Controller” and the other “New Recording,” with a detailed layout of controls resembling a mixing console. The right panel lists triggered segues and outlets related to the storyboard elements.](images/678928_1_En_5_Chapter/678928_1_En_5_Fig3_HTML.jpg)

Figure 5-3

Storyboard Editor

## Creating a Swift Protocol

As was done in Chapter [1](#678928_1_En_1_Chapter.xhtml), a public interface will be used to integrate the framework into the app target. It will be replaced with a completely generic solution in an upcoming chapter.

1.  Right-click the folder within the **Project Navigator** and select **New File from Template…**

2.  Select the **Swift** **File** template under the **Source** group and name it **RecordingStudioProtocol.swift**

Define a protocol in the class to instantiate the framework’s User Interface:

```
import Foundation
import UIKit
protocol DisplayableViewController {
func instantiateRootViewController()-> UIViewController
}
```

Adopt the protocol and continue to create the storyboard using the framework Bundle ID and class:

```
public class RecordingStudioProtocol: DisplayableViewController {
public init() {}
public func instantiateRootViewController() -> UIViewController {
let storyboard = UIStoryboard(name: "RecordingStudioUI", bundle: Bundle(for: RecordingStudioProtocol.self))
let vc = storyboard.instantiateViewController(withIdentifier: "RecordingStudioNC")
return vc
}
}
```

## Creating an App Target

As was done in the previous chapters, an App Target within the iOS Framework project will need to be created and configured:

1.  Perform the steps listed under the section “**Creating an App Target**” in Chapter [1](#678928_1_En_1_Chapter.xhtml)

2.  Perform the steps listed under the section “**Creating an** **App Target** **User Interface**” in Chapter [1](#678928_1_En_1_Chapter.xhtml)

3.  Perform the steps listed under the section “**Designing an** **App Target** **User Interface**” in Chapter [1](#678928_1_En_1_Chapter.xhtml)

4.  Perform the steps listed under the section “**Adding an iOS Framework**” in Chapter [1](#678928_1_En_1_Chapter.xhtml), and select RecordingsStudioFW.framework

## Integrating the iOS Framework

As with previous chapters, the framework will need to be integrated into the app from within **SceneDelegate.willConnectTo()**:

```
import RecordingStudioFW
func scene(_ scene: UIScene, willConnectTo session: UISceneSession, options connectionOptions: UIScene.ConnectionOptions) {
guard let winScene = (scene as? UIWindowScene) else { return }
if let storyboard = session.configuration.storyboard {
if let tabBarController = storyboard.instantiateInitialViewController() as? UITabBarController {
window = UIWindow(windowScene: winScene)
window?.rootViewController = tabBarController
window?.makeKeyAndVisible()
// Add framework User Interfaces via protocol...
var tbcViewControllers = tabBarController.viewControllers ?? []
tbcViewControllers.append(RecordingStudioProtocol().instantiateRootViewController())
tabBarController.setViewControllers(tbcViewControllers, animated: false)
tabBarController.tabBar.isHidden = (tbcViewControllers.count <= 1)
}
}
}
```

## Running the App Target

Set the app as the active scheme in the scheme drop-down, build the app, and run it using one of the Xcode simulators or an actual device. The app should compile and run properly, showing one track enabled (**Figure** [**5-4**](#678928_1_En_5_Chapter.xhtml#Fig4)).

![A screenshot showing a mobile app interface for a recording studio on an iPhone simulator, overlaid on an Xcode development environment. The app interface displays a “New Recording” screen with various control knobs and sliders. The Xcode background shows a Swift file with code related to a scene delegate, including import statements and function definitions. The project navigator and properties panel are visible on the sides.](images/678928_1_En_5_Chapter/678928_1_En_5_Fig4_HTML.jpg)

Figure 5-4

Xcode Simulator

In this chapter, you expanded dynamic feature enablement by developing the RecordingStudioFW Framework. You implemented unlockable, purchase-driven features across multiple frameworks while preserving decoupling and used a mock multitrack recording studio UI to demonstrate a practical application. By completing this chapter, you reinforced the principles of modularity, scalability, and flexible feature management across an interconnected framework ecosystem.

With multiple frameworks now supporting unlockable features independently, you’re ready to explore how In-App Purchases can drive revenue across your app ecosystem. In the next chapter, we’ll extend an existing framework to demonstrate monetizing functionality while maintaining decoupling.

Footnotes [1](#678928_1_En_5_Chapter.xhtml#Fn1_source)  

# 6. Driving Revenue Growth Through Dynamic Feature Enablement

Extending the RecordingsFW Framework

This chapter will extend the RecordingsFW framework to support upcoming chapters that will demonstrate utilizing In-App Purchases to drive revenue by upselling functionality based on user needs and desires.

The chapter will demonstrate the enablement of a “mock” song publishing feature via In-App Purchases made in another framework. We’ll extend the RecordingsFW Framework to enable revenue-driven functionality. This approach prepares the framework for more complex monetization scenarios in future chapters.

## Feature Enablement

Open the RecordingsFW framework, as this chapter will extend the code developed in previous chapters.

The song publishing feature will be controlled by a Property List setting and ultimately driven by In-App Purchases. To integrate the Property List setting, a new attribute will need to be added to **Recordings.plist** to control the visibility of the feature. Create a new property named **enablePublishing** and define it to be a Bool with a value set to NO (**Figure** [**6-1**](#678928_1_En_6_Chapter.xhtml#Fig1)).

![Screenshot of a development environment displaying a project directory and a property list file. The left panel shows a file tree with various Swift files and a “Recordings.plist” file selected. The middle panel displays the contents of “Recordings.plist,” showing two Boolean keys: “enablePublishing” set to “NO” and “showPublished” set to “YES.” The right panel provides metadata about the file, including its name, type, and location.](images/678928_1_En_6_Chapter/678928_1_En_6_Fig1_HTML.jpg)

Figure 6-1

Property List Editor

## Integrating a Call-to-Action

Once the Property List setting has been added, it will be used to control the visibility of a User Interface component, **UIBarButtonItem**. The UIBarButtonItem will trigger a Call-to-Action (CTA) to display the mock User Interface for the song publishing feature.

The **UIBarButtonItem** was previously added to RecordingsTVC as an **IBOutlet**. A Call-to-Action function, **publishButtonPressed()**, should be added as an **IBAction** to respond to the button selection (the corresponding storyboard connections have already been set up):

```
@IBAction func publishButtonPressed(_ sender: Any) {
let storyboard = UIStoryboard(name: "RecordingsUI", bundle: Bundle(for: RecordingsTVC.self))
let viewController = storyboard.instantiateViewController(withIdentifier: "PublishRecordingNC")
self.present(viewController, animated: true)
}
```

The visibility of the button will need to be set in **viewWillAppear**() based on the Property List setting (eventually overridden by a UserDefaults setting to reflect an In-App Purchase):

```
override func viewWillAppear(_ animated: Bool) {
super.viewWillAppear(animated)
var enablePublishing = false
if let bundle = Bundle(identifier:"com.gw.RecordingsFW"),
let path = bundle.path(forResource: "Recordings", ofType: "plist"),
let plistDict = NSDictionary(contentsOfFile: path),
let publishing = plistDict.object(forKey: "enablePublishing") as? Bool {
enablePublishing = publishing
}
if let iaps = UserDefaults.standard.object(forKey: "iaps") as? [String: Any],
let publishing = iaps["publish"] as? Bool {
enablePublishing = publishing
}
self.publishBarButtonItem.isHidden = !enablePublishing
}
```

## Creating the User Interface for the CTA

A new class called **PublishRecordingTVC** will need to be created to support the User Interface for the song publishing feature:

1.  Right-click the folder within the Project Navigator and select **New File from Template…**

2.  Choose the **Cocoa Touch** **Class** template under the **Source** section and **Next**

3.  Name the class **PublishRecordingTVC**, and set **UITableViewController** as the subclass

4.  Ensure **Also create** **XIB** **file** is unchecked and **Language** is Swift

Because the User Interface for this feature is non-functional and included solely for demonstration purposes, the default code generated for the class can be replaced with the following:

```
import UIKit
class PublishRecordingTVC: UITableViewController {
@IBOutlet weak var titleLabel: UILabel!
@IBOutlet weak var songwritersLabel: UILabel!
@IBOutlet weak var yearLabel: UILabel!
@IBOutlet weak var publishButton: UIButton!
@IBOutlet weak var cancelBarButtonItem: UIBarButtonItem!
let copyrightTitle = "Copyright"
let previewTitle = "Preview"
let publishTitle = "Publish"
override func viewDidLoad() {
super.viewDidLoad()
self.clearsSelectionOnViewWillAppear = false
self.tableView.separatorInset = UIEdgeInsets.init(top: 0.0, left: 20.0, bottom: 0.0, right: 0.0)
self.navigationItem.rightBarButtonItem = self.editButtonItem
self.navigationItem.backBarButtonItem = cancelBarButtonItem
if let font = UILabel.appearance().font {
let boldFont = UIFont(descriptor: font.fontDescriptor.withSymbolicTraits(.traitBold)!, size: font.pointSize)
titleLabel.font = boldFont
songwritersLabel.font = boldFont
yearLabel.font = boldFont
}
}
override func numberOfSections(in tableView: UITableView) -> Int {
return 3
}
override func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
switch section {
case 0:
return 3
case 1:
return 1
case 2:
return 2
default:
return 0
}
}
override func tableView(_ tableView: UITableView, heightForRowAt indexPath: IndexPath) -> CGFloat {
return 44.0
}
override func tableView(_ tableView: UITableView, viewForFooterInSection section: Int) -> UIView? {
if section  CGFloat {
return 21.0
}
override func tableView(_ tableView: UITableView, willDisplayHeaderView view: UIView, forSection section: Int) {
var title = ""
switch section {
case 0:
title = copyrightTitle
case 1:
title = previewTitle
case 2:
title = publishTitle
default:
break
}
let titleView = view as! UITableViewHeaderFooterView
titleView.textLabel?.text = title // Remove all capitalized letters
}
@IBAction func cancelButtonPressed(_ sender: Any) {
self.dismiss(animated: true)
}
@IBAction func publishButtonPressed(_ sender: Any) {
self.dismiss(animated: true)
}
}
```

## Running the App Target

Before running the app target, select **Recordings.plist** and set **showPublished** to NO and **enablePublishing** to YES. This will mimic an actual In-App Purchase to be demonstrated in an upcoming chapter.

Set the app as the active scheme in the scheme drop-down, build the app, and run it using one of the Xcode simulators or an actual device. The app should compile and run properly.

The table view now displays local audio recordings ready to be published and a visible/enabled Publish Recording button. Selecting the button will display the User Interface for the song publishing feature (**Figure** [**6-2**](#678928_1_En_6_Chapter.xhtml#Fig2)).

![Screenshot of a development environment displaying a mobile app interface for publishing a recording. The app screen includes fields for entering the song title, songwriters, and year. There are playback controls for previewing the recording and options to select a file and publish it. The development environment shows a file directory on the left and properties on the right.](images/678928_1_En_6_Chapter/678928_1_En_6_Fig2_HTML.jpg)

Figure 6-2

Xcode Simulator

In this chapter, you enhanced the RecordingsFW Framework to support revenue-driven features using In-App Purchases. By enabling a mock song publishing feature through purchases in another framework, you maintained decoupling while demonstrating how modular frameworks can collaborate to upsell functionality. This extension solidifies your understanding of monetizable, flexible, and reusable iOS frameworks. After extending the RecordingsFW Framework to support revenue-driven features, you’re ready to see how frameworks can be dynamically integrated into apps.

In the next chapter, we’ll explore no-code integration techniques that allow a framework to expand app functionality dynamically, without the need for code changes.

# 7. Enabling Dynamic Framework Integration

Designing the ScratchTraxApp1 App

This chapter introduces the concept of dynamic integration, enabling a framework developed in previous chapters to be seamlessly integrated into a mobile app without the use of protocols or the need for code changes. This chapter demonstrates how dynamic, no-code framework integration allows iOS apps to expand and adapt with greater flexibility, scalability, and efficiency, providing a practical blueprint for highly modular and maintainable architectures.

The framework will need to be modified to allow it to be integrated dynamically through a series of innovative techniques.

We’ll design the ScratchTraxApp1 App to demonstrate this dynamic framework integration. You’ll learn how to incorporate a framework developed in previous chapters into a mobile app without using protocols or making code changes. By applying these innovative techniques, you’ll create a flexible, scalable, and maintainable app architecture that allows frameworks to adapt and expand app functionality dynamically.

## Creating an iOS App

Up to this point, each framework was developed independently and executed using an App Target within its own framework project. The App Targets won’t be needed by the iOS apps being constructed in this chapter, but they remain to support future enhancements to the frameworks, independent of these iOS apps.

Creating the **ScratchTraxApp1** app will be similar to creating an internal App Target, and the app will display a table of published audio recordings retrieved using a remote network request.

Create a new project as follows:

![Screenshot of a software development interface for creating a new project. Tabs at the top include options for different platforms like iOS, macOS, and others. The “iOS” tab is selected. Below, under “Application,” icons for different app types are displayed, such as App, Document App, Game, and Augmented Reality App. The “App” option is highlighted. Under “Framework & Library,” options include Framework, Static Library, and Metal Library. Buttons for “Cancel,” “Previous,” and “Next” are at the bottom.](images/678928_1_En_7_Chapter/678928_1_En_7_Fig1_HTML.jpg)

Figure 7-1

New Project Template

1.  Launch Xcode

2.  Select **Create a new** **Xcode** **project**

3.  Select the **App** template under the **Application** group (**Figure** [**7-1**](#678928_1_En_7_Chapter.xhtml#Fig1))

4.  Set Product Name to **ScratchTraxApp1**

5.  Set Interface to **Storyboard**

6.  Set Language to **Swift**

## Creating a User Interface for the iOS App

The iOS app being constructed in this chapter will need a User Interface to support features provided by the frameworks. A Tab Bar Controller will be used, like the one used with each framework App Target. Perform the following steps to create and configure the Tab Bar Controller:

![Screenshot of an Xcode interface showing a project named “ScratchTraxApp1” with a storyboard open. The main view displays a “Tab Bar Controller” for an iPhone 17 Pro Max. The left panel lists project files, including “AppDelegate.swift” and “Main.storyboard.” The right panel shows properties for the view controller, such as layout and transition style settings. Various interface elements and options are visible, indicating a development environment for iOS applications.](images/678928_1_En_7_Chapter/678928_1_En_7_Fig2_HTML.jpg)

Figure 7-2

Storyboard Editor

1.  Select **ViewController****.swift** from within the **Project Navigator** and delete it

2.  Select **Main.storyboard** from within the **Project Navigator** to display the **Interface Builder**

3.  Select the **View** **Controller Scene** from within the **Storyboard** **Navigator** and delete it

4.  Select the **Library** icon (+) at the bottom left of the **Interface Builder** and create a **Tab Bar Controller**

5.  Delete **Item 1 Scene** and **Item 2 Scene** in the Storyboard Navigator

6.  Select **Tab Bar Controller** **Scene** in the Storyboard Navigator

7.  Show **Inspectors** using the top right icon

8.  Select the **Identity Inspector** and input a **Storyboard** **ID**

9.  Select the **Attributes** **Inspector** and select **Is Initial** **View** **Controller** (**Figure** [**7-2**](#678928_1_En_7_Chapter.xhtml#Fig2))

## Creating a Property List for the iOS App

A Property List will need to be added to the project to override the RecordingsFW framework Property List setting, **showPublished**, which controls the framework’s mode of operation:

![Screenshot of an Xcode interface displaying a project named “ScratchTraxApp1.” The left panel shows a file directory with various files, including “ScratchTraxApp1.plist” highlighted. The central panel displays the contents of this plist file, showing a dictionary with a key “showPublished” set to a Boolean value “YES.” The right panel provides details about the file, including its location and type.](images/678928_1_En_7_Chapter/678928_1_En_7_Fig3_HTML.jpg)

Figure 7-3

Property List Editor

1.  Right-click the folder within the **Project Navigator** and select **New File from Template…**

2.  Select the **Property List** template under the **Resource** group and **Next**

3.  Name the file **ScratchTraxApp1.plist**

4.  Select **ScratchTraxApp1.plist** from within the **Project Navigator**

5.  Select the **+** icon in the Root row of the empty table (hover over the Root cell to make it visible)

6.  Create a variable called **showPublished,** with Type set to Boolean and Value set to YES (**Figure** [**7-3**](#678928_1_En_7_Chapter.xhtml#Fig3))

## Creating an Xcode Workspace

An Xcode workspace will be used to structure the sets of projects needed to construct the mobile app in this chapter. When projects are added to a workspace, Xcode automatically detects dependencies between the targets in those projects and builds them in the correct order. Extensive details regarding workspaces can be found under the **Files and workspaces** topic of the **Projects and workspaces** website per Apple Developer documentation.^([^(27)](#fn27))

**Create a new** **workspace****:**

1.  Shut down Xcode if it’s running, launch it, and close the **Welcome to Xcode** screen (there’s no option for creating a Workspace so an Xcode menu will be used)

2.  Select the Xcode **File/New/Workspace...** menu

3.  Name the workspace **ScratchTraxApp1** and save it to the root directory containing the other Xcode projects

4.  Right-click an empty area within the Project Navigator and select **Add Files to ScratchTraxApp1…**

5.  Navigate to the directory containing the ScratchTraxApp1 project and add **ScratchTraxApp1.xcodeproj**

6.  Under **Choose options for adding File**, set the **Action** to **Reference files in place**

7.  Right-click an empty area within the Project Navigator and select **Add Files to ScratchTraxApp1…**

8.  Navigate to the directory containing the RecordingsFW project and add **RecordingsFW.xcodeproj**

9.  Under **Choose options for adding File**, set the **Action** to **Reference files in place**

**Add the RecordingsFW framework to the app:**

1.  Select the root node of the **ScratchTraxApp1** project in the **Project Navigator**

2.  Select **ScratchTraxApp1** under the **Targets** section

3.  Select the **General** tab and the **+** icon under the **Frameworks, Libraries, and Embedded Content** section

4.  Add **RecordingsFW.framework** to the app and verify it appears under the **Frameworks, Libraries, and Embedded Content** section with Embed & Sign

## Integrating iOS Frameworks Dynamically

Up to this point, iOS Frameworks have been integrated into app targets via **SceneDelegate****.****willConnectTo****()** using public protocols defined within the frameworks. We’ll replace the use of these protocols with a dynamic, decoupled solution.

To accomplish this, a mechanism will be designed to allow frameworks to register their root view controllers for consumption/exposure by iOS apps. UserDefaults will be utilized to propagate these “registrations.”

The following requirements will need to be met to support the generic registration of frameworks:

1.  The data format must be well-known to both apps and frameworks

2.  Frameworks will need to register themselves when loaded

3.  Apps will need to process registrations in SceneDelegate .willConnectTo()

First, a well-known data format will need to be defined. In order to instantiate, load, and integrate a framework’s User Interface view controller, the app will need to know the framework Bundle ID, Storyboard Name, Storyboard Identifier, title, and icon. Each view registered by a framework will be added to a UserDefaults top-level “views” key:

```
let config = [ "bundle": "com.gw.RecordingsFW", "storyboard": "RecordingsUI", "view":
"RecordingsNC", "title": "Recordings", "icon": "music.note.list"]
```

UserDefaults works well for managing registrations across apps and frameworks for several reasons:

1.  Frameworks will always load before the app’s SceneDelegate.willConnectTo() is called

2.  Frameworks will always register or update their registrations when loaded, allowing changes made in a framework to be reflected in an app without the app requiring changes

3.  If an app is deleted from a device and reinstalled, frameworks will reconstitute their registrations when loaded

4.  UserDefaults set by an app, or any of its frameworks, is accessible to all of them

To implement the registration process for the app, SceneDelegate.willConnectTo() will need to search for a top-level “views” key via UserDefaults. If found, it will iterate through the array of registrations and add an instantiated view controller to its tab bar controller for each one found. Once completed, it will check for an app Property List setting that overrides a corresponding one in a framework, and if found, propagate it via UserDefaults for use in framework code.

The framework protocol referenced in each framework’s internal App Target will no longer be needed. Instead, replace this app’s SceneDelegate.willConnectTo() in the ScratchTraxApp1 project with the following registration code to dynamically add the framework to its UI:

```
func scene(_ scene: UIScene, willConnectTo session: UISceneSession, options connectionOptions: UIScene.ConnectionOptions) {
guard let winScene = (scene as? UIWindowScene) else { return }
if let storyboard = session.configuration.storyboard {
if let tabBarController = storyboard.instantiateInitialViewController() as? UITabBarController {
window = UIWindow(windowScene: winScene)
window?.rootViewController = tabBarController
window?.makeKeyAndVisible()
var tbcViewControllers = tabBarController.viewControllers ?? []
// Add framework UI's generically...
if let configs = UserDefaults.standard.object(forKey: "views") as? [[String:String]] {
var lastTitle = ""
for config in configs {
if let bundle = config["bundle"], let storyboard = config["storyboard"], let view = config["view"] {
if let bundle = Bundle(identifier: bundle) {
let storyboard = UIStoryboard(name: storyboard, bundle: bundle)
let storyboardVC = storyboard.instantiateViewController(withIdentifier: view)
var image: UIImage?
if let icon = config["icon"] {
image = UIImage(systemName: icon, withConfiguration: UIImage.SymbolConfiguration(weight: .medium))
}
storyboardVC.tabBarItem = UITabBarItem(title: config["title"], image: image, tag: 0)
if lastTitle == "" || storyboardVC.tabBarItem.title?.caseInsensitiveCompare(lastTitle) == .orderedDescending {
tbcViewControllers.append(storyboardVC)
} else {
tbcViewControllers.insert(storyboardVC, at: tabBarController.viewControllers?.count ?? 0)
}
lastTitle = storyboardVC.tabBarItem.title ?? ""
}
}
}
tabBarController.setViewControllers(tbcViewControllers, animated: false)
}
}
}
// Override any frameworks’ p-list settings with its own…
if let bundle = Bundle(identifier:"com.gw.ScratchTraxApp1"), let path = bundle.path(forResource: "ScratchTraxApp1", ofType: "plist"),
let plistDict = NSDictionary(contentsOfFile: path), let showPublished = plistDict["showPublished"] as? Bool {
var features = UserDefaults.standard.object(forKey: "features") as? [String:String] ?? [:]
features["showPublished"] = String(showPublished)
UserDefaults.standard.set(features, forKey: "features")
}
}
```

## Framework Registration

In order to provide ample opportunity for a framework to register its User Interface view controller for use in an app, it needs to do so early in the app life cycle, preferable when the framework is loaded, and before SceneDelegate.willConnectTo() executes. Since all frameworks integrated into an app are loaded before the app starts its life cycle, detecting framework loading is critical.

So how can this be done in a Swift-based framework? In a nutshell, it really can’t. In the Objective-C programming language, NSObject has a +load method which is always called when instantiating an Objective-C object, after all of its superclasses’ +load methods have been called.^([^(28)](#fn28)) However, it’s not called automatically in Swift classes.

That complicates the framework registration process and, in fact, prevents it from working. But there’s an alternative. We’ll bridge Objective-C with Swift and have it act as a proxy for loading for the framework.

First, create an Objective-C header file:

1.  Right-click the RecordingsFW project folder within the **Project Navigator** and select **New File from Template…**

2.  Select the **Header File** template under the **Source** group

3.  Name the file **RecordingsFWObjcLoader.h**

Add following code to the header file to define the Objective-C class interface:

```
#ifndef RecordingsFWObjcLoader_h
#define RecordingsFWObjcLoader_h
#import 
// NS_ASSUME_NONNULL_BEGIN: https://developer.apple.com/swift/blog/?id=25
NS_ASSUME_NONNULL_BEGIN
@interface RecordingsFWObjcLoader : NSObject
@end
NS_ASSUME_NONNULL_END
#endif /* RecordingsFWObjcLoader_h */
```

Create an Objective-C implementation file for the class:

1.  Right-click the RecordingsFW project folder within the **Project Navigator** and select **New File from Template…**

2.  Select the **Objective-C** **File** template under the **Source** group

3.  Name the file **RecordingsFWObjcLoader.m**

Add the following code to the class file to define the Objective-C class implementation, which implements the +load() method for the registration hook:

```
#import "RecordingsFWObjcLoader.h"
#import "RecordingsFW/RecordingsFW-Swift.h"
@implementation RecordingsFWObjcLoader
// https://developer.apple.com/documentation/objectivec/nsobject/1418815-load
+(void) load {
if ([[RecordingsFWSwiftLoader alloc] init]) {
NSLog(@"RecordingsFWObjcLoader.load() succeeded...");
} else {
NSLog(@"RecordingsFWObjcLoader.load() failed...");
}
}
@end
```

Since **RecordingsFW/RecordingsFW-****Swift****.h** is a non-visible bridging header needed to support the referencing of Swift classes from within Objective-C, the following build setting needs to be set to force the compiler to generate it:

1.  Select the **RecordingsFW** project folder within the **Project Navigator**

2.  Select the **RecordingsFW** target

3.  Select the **Build Settings** tab and search for **Install Generated Header,** and set it to **YES**

Note

RecordingsFWObjcLoader.m will not compile until the next step has been completed.

Create a Swift file for registering the framework:

1.  Right-click the RecordingsFW project folder within the **Project Navigator** and select **New File from Template…**

2.  Select the **Swift** **File** template under the **Source** group

3.  Name the file **RecordingsFWSwiftLoader.swift**

Add the following code to the class to perform the framework registration, so that the app can expose the framework’s functionality:

```
import Foundation
@objcMembers public class RecordingsFWSwiftLoader: NSObject {
override public init() {
super.init()
initViews()
}
func initViews() {
let config = [ "bundle": "com.gw.RecordingsFW", "storyboard": "RecordingsUI", "view": "RecordingsNC", "title": "Recordings", "icon": "music.note.list"]
var configs: [[String:String]] = []
if let persistedConfigs = UserDefaults.standard.object(forKey: "views") as? [Dictionary], !persistedConfigs.isEmpty {
configs.append(contentsOf: persistedConfigs)
if !findConfig(configs: persistedConfigs) {
configs.append(config)
}
} else {
configs.append(config)
}
UserDefaults.standard.set(configs, forKey: "views")
}
func findConfig(configs: [[String:String]]) -> Bool {
var found = false
for config in configs {
if config["bundle"] == "com.gw.RecordingsFW", config["storyboard"] == "RecordingsUI", config["view"] == "RecordingsNC" {
found = true
break
}
}
return found
}
}
```

## The Acid Test

Set the app as the active scheme in the scheme drop-down, build the app, and run it using one of the Xcode simulators or an actual device. The code should compile and run properly. The results should match the screenshot in **Figure** [**7-4**](#678928_1_En_7_Chapter.xhtml#Fig4).

When the framework is loaded, it registers its view controller. The app then retrieves this registration from UserDefaults, instantiates the corresponding view controller, integrates it into its own user interface, and overrides any framework Property List settings to ensure the view displays **published** audio recordings in the Recordings feature.

![Screenshot of a development environment showing an iPhone simulator displaying an app titled “Published Recordings.” The app lists two recordings: “My (Published) Electric Anthem” and “My (Published) Acoustic Ballad,” both credited to John Doe. The simulator is surrounded by a code editor with project files and settings for “ScratchTraxApp1” in Xcode, including files like “AppDelegate.swift” and “Main.storyboard.”](images/678928_1_En_7_Chapter/678928_1_En_7_Fig4_HTML.jpg)

Figure 7-4

Xcode Simulator

In this chapter, you successfully integrated a framework dynamically into the ScratchTraxApp1 app without using protocols or altering the app’s underlying code (once the code was modified to be able to do so). You explored techniques for no-code framework integration, demonstrating how to build flexible, scalable, and maintainable app architectures that can easily incorporate modular frameworks. This approach provides a practical blueprint for highly adaptable iOS applications.

With dynamic integration techniques in place, you’re ready to unify multiple frameworks into a single, cohesive app architecture. In the next chapter, we’ll combine the frameworks developed so far into one app, demonstrating modular functionality and revenue-driven features across the system.

Footnotes [1](#678928_1_En_7_Chapter.xhtml#Fn1_source)   [2](#678928_1_En_7_Chapter.xhtml#Fn2_source)  

# 8. Unifying the Dynamic Framework Architecture

Designing the ScratchTraxApp2 App

This chapter brings together the frameworks developed throughout this book into a single, cohesive architecture. Each framework is integrated to load dynamically at runtime, building on the principles introduced in Chapter [7](#678928_1_En_7_Chapter.xhtml). This unified system also incorporates In-App Purchases across frameworks, demonstrating how dynamic feature enablement can drive modular functionality and revenue growth within a scalable iOS architecture.

The RecordingsStoreFW and RecordingStudioFW frameworks will need to be modified as in Chapter [7](#678928_1_En_7_Chapter.xhtml) to allow them to be integrated dynamically. SettingsFW will continue to be loaded using a protocol.

In this chapter, we’ll design the ScratchTraxApp2 App to bring together all previously developed frameworks into a unified, dynamically integrated system. In-App Purchases will showcase how dynamic feature enablement can drive modular functionality and revenue growth while maintaining a scalable and maintainable iOS architecture.

## Creating Another iOS App

A second app will need to be created, **ScratchTraxApp2**, utilizing the frameworks developed in the book:

1.  Launch Xcode

2.  Select **Create a new** **Xcode** **project**

3.  Select the **App** template under the **Application** group

4.  Set Product Name to **ScratchTraxApp2**

5.  Set Interface to **Storyboard**

6.  Set Language to **Swift**

### Creating a User Interface for the iOS App

Again, the app will require a user interface to expose the features provided by the frameworks. Create and configure a Tab Bar Controller:

1.  Select **ViewController****.swift** from within the **Project Navigator** and delete it

2.  Select **Main.storyboard** from within the **Project Navigator** to display the **Interface Builder**

3.  Select the **View** **Controller Scene** from within the **Storyboard** **Navigator** and delete it

4.  Select the **Library** icon (+) at the bottom left of the **Interface Builder** and create a **Tab Bar Controller**

5.  Delete **Item 1 Scene** and **Item 2 Scene** in the Storyboard Navigator

6.  Select **Tab Bar Controller** **Scene** in the Storyboard Navigator

7.  Show **Inspectors** using the top right icon

8.  Select the **Identity Inspector** and input a **Storyboard** **ID**

9.  Select the **Attributes** **Inspector** and select **Is Initial** **View** **Controller**

## Creating a Property List for the iOS App

A Property List will need to be added to the project to override the RecordingsFW framework Property List setting, **showPublished**, which controls the framework’s mode of operation:

1.  Right-click the folder within the **Project Navigator** and select **New File from Template…**

2.  Select the **Property List** template under the **Resource** group and **Next**

3.  Name the file **ScratchTraxApp2.plist**

4.  Select **ScratchTraxApp2.plist** from within the **Project Navigator**

5.  Select the **+** icon in the Root row of the empty table (hover over the Root cell to make it visible)

6.  Create a variable called **showPublished**, with Type set to Boolean and Value set to NO

## Creating an Xcode Workspace

Again, an Xcode workspace will be used to structure the sets of projects needed to construct the mobile app in this chapter:

1.  Shut down Xcode if it’s running, launch it, and close the **Welcome to Xcode** screen (there’s no option for creating a Workspace so an Xcode menu will be used)

2.  Select the Xcode **File/New/Workspace...** menu

3.  Name the workspace **ScratchTraxApp2** and save it to the root directory containing the other Xcode projects

4.  Right-click an empty area within the Project Navigator and select **Add Files to “ScratchTraxApp2”…**

5.  Navigate to the directory containing the ScratchTraxApp2 project and add **ScratchTraxApp2.xcodeproj**

6.  Under **Choose options for adding File**, set the **Action** to **Reference files in place**

7.  Right-click an empty area within the Project Navigator and select **Add Files to “ScratchTraxApp2”…**

8.  Navigate to the directory containing the RecordingsFW project and add **RecordingsFW.xcodeproj**

9.  Under **Choose options for adding File**, set the **Action** to **Reference files in place**

10.  Right-click an empty area within the Project Navigator and select **Add Files to “ScratchTraxApp2”…**

11.  Navigate to the directory containing the RecordingsFW project and add **RecordingsStoreFW.xcodeproj**

12.  Under **Choose options for adding File**, set the **Action** to **Reference files in place**

13.  Right-click an empty area within the Project Navigator and select **Add Files to “ScratchTraxApp2”…**

14.  Navigate to the directory containing the RecordingsFW project and add **RecordingStudioFW.xcodeproj**

15.  Under **Choose options for adding File**, set the **Action** to **Reference files in place**

16.  Right-click an empty area within the Project Navigator and select **Add Files to “ScratchTraxApp2”…**

17.  Navigate to the directory containing the RecordingsFW project and add **SettingsFW.xcodeproj**

18.  Under **Choose options for adding File**, set the **Action** to **Reference files in place**

**Add the frameworks to the app:**

1.  Select the root node of the **ScratchTraxApp2** project in the **Project Navigator**

2.  Select **ScratchTraxApp2** under the **Targets** section

3.  Select the **General** tab and the **+** icon under the **Frameworks, Libraries, and Embedded Content** section

4.  Add **RecordingsFW.framework**, **RecordingsStoreFW.framework**, **RecordingStudioFW.framework**, and **SettingsFW.framework** to the app and verify they appear under the **Frameworks, Libraries, and Embedded Content** section with Embed & Sign

## Integrating iOS Frameworks Dynamically

As previously discussed in Chapter [7](#678928_1_En_7_Chapter.xhtml), the frameworks will be integrated into the app from within the SceneDelegate class. Select that class under the app’s folder, within the **Project Navigator**, and import the **SettingsFW** framework. It will be the only framework imported via public protocol.

```
import SettingsFW
```

As previously noted, a well-known data format will need to be defined. In order to instantiate, load, and integrate the framework’s User Interface view controllers, the app will need to know the framework Bundle ID, Storyboard Name, Storyboard Identifier, title, and icon. Each view registered by a framework will be added to a UserDefaults top-level “views” key. The following will be used in each of the corresponding framework’s registration:

**RecordingsFW.framework:**

```
let config = [ "bundle": "com.gw.RecordingsFW", "storyboard": "RecordingsUI", "view":
"RecordingsNC", "title": "Recordings", "icon": "music.note.list"]
```

**RecordingsStoreFW.framework:**

```
let config = [ "bundle": "com.gw.RecordingsStoreFW", "storyboard": "RecordingsStoreUI",
"view": "RecordingsStoreVC", "title": "Pro Features", "icon": "storefront"]
```

**RecordingStudioFW.framework:**

```
let config = [ "bundle": "com.gw.RecordingStudioFW", "storyboard": "RecordingStudioUI",
"view": "RecordingStudioNC", "title": "Recording Studio", "icon": "music.note"]
```

To implement the registration process for the app, SceneDelegate.willConnectTo() will need to search for a top-level “views” key via UserDefaults. If found, it will iterate through the array of registrations and add an instantiated view controller to its tab bar controller for each one found. Once completed, it will check for an app Property List setting that overrides a corresponding one in a framework, and if found, propagate it via UserDefaults for use in framework code.

The framework protocol referenced in each framework’s internal App Target will no longer be needed. Instead, replace this app’s SceneDelegate.willConnectTo() in the ScratchTraxApp2 project with the following registration code to dynamically add the frameworks to its UI:

```
func scene(_ scene: UIScene, willConnectTo session: UISceneSession, options connectionOptions: UIScene.ConnectionOptions) {
guard let winScene = (scene as? UIWindowScene) else { return }
if let storyboard = session.configuration.storyboard {
if let tabBarController = storyboard.instantiateInitialViewController() as? UITabBarController {
window = UIWindow(windowScene: winScene)
window?.rootViewController = tabBarController
window?.makeKeyAndVisible()
var tbcViewControllers = tabBarController.viewControllers ?? []
// Add framework UI's generically...
if let configs = UserDefaults.standard.object(forKey: "views") as? [[String:String]] {
var lastTitle = ""
for config in configs {
if let bundle = config["bundle"], let storyboard = config["storyboard"], let view = config["view"] {
if let bundle = Bundle(identifier: bundle) {
let storyboard = UIStoryboard(name: storyboard, bundle: bundle)
let storyboardVC = storyboard.instantiateViewController(withIdentifier: view)
var image: UIImage?
if let icon = config["icon"] {
image = UIImage(systemName: icon, withConfiguration: UIImage.SymbolConfiguration(weight: .medium))
}
storyboardVC.tabBarItem = UITabBarItem(title: config["title"], image: image, tag: 0)
// Since frameworks are loaded in an arbitrary order which cannot be controlled, add them
// to the tab bar in alphabetical order...
if lastTitle == "" || storyboardVC.tabBarItem.title?.caseInsensitiveCompare(lastTitle) == .orderedDescending {
tbcViewControllers.append(storyboardVC)
} else {
tbcViewControllers.insert(storyboardVC, at: tabBarController.viewControllers?.count ?? 0)
}
lastTitle = storyboardVC.tabBarItem.title ?? ""
}
}
}
// Add framework User Interfaces via protocol...
tbcViewControllers.append(SettingsProtocol().instantiateRootViewController(configuration: ["Test" : true]))
tabBarController.setViewControllers(tbcViewControllers, animated: false)
}
}
}
// Override any frameworks’ p-list settings with its own…
if let bundle = Bundle(identifier:"com.gw.ScratchTraxApp2"), let path = bundle.path(forResource: "ScratchTraxApp2", ofType: "plist"),
let plistDict = NSDictionary(contentsOfFile: path), let showPublished = plistDict["showPublished"] as? Bool {
var features = UserDefaults.standard.object(forKey: "features") as? [String:String] ?? [:]
features["showPublished"] = String(showPublished)
UserDefaults.standard.set(features, forKey: "features")
}
}
```

## Framework Registration

As was done in Chapter [7](#678928_1_En_7_Chapter.xhtml) per the RecordingsFW framework, the RecordingStudioFW and RecordingsStoreFW frameworks will need to add the required Objective-C and Swift classes to perform their registrations at load time. The SettingsFW framework will continue to be integrated via public protocol.

**RecordingStudioFW**

Create the Objective-C header file:

1.  Right-click the RecordingStudioFW project folder within the **Project Navigator** and select **New File from Template…**

2.  Select the **Header File** template under the **Source** group

3.  Name the file **RecordingStudioFWObjcLoader.h**

Add the following code to the header file to define the Objective-C class interface:

```
#ifndef RecordingStudioFWObjcLoader_h
#define RecordingStudioFWObjcLoader_h
#import 
// NS_ASSUME_NONNULL_BEGIN: https://developer.apple.com/swift/blog/?id=25
NS_ASSUME_NONNULL_BEGIN
@interface RecordingStudioFWObjcLoader : NSObject
@end
NS_ASSUME_NONNULL_END
#endif /* RecordingStudioFWObjcLoader_h */
```

Create the Objective-C implementation file for the class:

1.  Right-click the RecordingStudioFW project folder within the **Project Navigator** and select **New File from Template…**

2.  Select the **Objective-C** **File** template under the **Source** group

3.  Name the file **RecordingStudioFWObjcLoader.m**

Add the following code to the class file to define the Objective-C class implementation, which implements the +load() method for the registration hook:

```
#import "RecordingStudioFWObjcLoader.h"
#import "RecordingStudioFW/RecordingStudioFW-Swift.h"
@implementation RecordingStudioFWObjcLoader
// https://developer.apple.com/documentation/objectivec/nsobject/1418815-load
+(void) load {
if ([[RecordingStudioFWSwiftLoader alloc] init]) {
NSLog(@"RecordingStudioFWObjcLoader.load() succeeded...");
} else {
NSLog(@"RecordingStudioFWObjcLoader.load() failed...");
}
}
@end
```

Since **RecordingStudioFW/RecordingStudioFW-****Swift****.h** is a non-visible bridging header needed to support the referencing of Swift classes from within Objective-C, the following build setting needs to be set to force the compiler to generate it:

1.  Select the **RecordingStudioFW** project folder within the **Project Navigator**

2.  Select the **RecordingStudioFW** target

3.  Select the **Build Settings** tab and search for **Install Generated Header**, and set it to **YES**

Note

RecordingStudioFWObjcLoader.m will not compile until the next step has been completed.

Create the Swift file for registering the framework:

1.  Right-click the RecordingsFW project folder within the **Project Navigator** and select **New File from Template…**

2.  Select the **Swift** **File** template under the **Source** group

3.  Name the file **RecordingStudioFWSwiftLoader.swift**

Add the following code to the class to perform the framework registration, so that the app can expose the framework’s functionality:

```
import Foundation
@objcMembers public class RecordingStudioFWSwiftLoader: NSObject {
override public init() {
super.init()
initViews()
}
func initViews() {
let config = [ "bundle": "com.gw.RecordingStudioFW", "storyboard": "RecordingStudioUI", "view": "RecordingStudioNC", "title": "Recording Studio", "icon": "music.note"]
var configs: [[String:String]] = []
if let persistedConfigs = UserDefaults.standard.object(forKey: "views") as? [Dictionary], !persistedConfigs.isEmpty {
configs.append(contentsOf: persistedConfigs)
if !findConfig(configs: persistedConfigs) {
configs.append(config)
}
} else {
configs.append(config)
}
UserDefaults.standard.set(configs, forKey: "views")
}
func findConfig(configs: [[String:String]]) -> Bool {
var found = false
for config in configs {
if config["bundle"] == "com.gw.RecordingStudioFW", config["storyboard"] == "RecordingStudioUI", config["view"] == "RecordingStudioNC" {
found = true
break
}
}
return found
}
}
```

**RecordingsStoreFW**

Create the Objective-C header file:

1.  Right-click the RecordingsStoreFW project folder within the **Project Navigator** and select **New File from Template…**

2.  Select the **Header File** template under the **Source** group

3.  Name the file **RecordingsStoreFWObjcLoader.h**

Add the following code to the header file to define the Objective-C class interface:

```
#ifndef RecordingsStoreFWObjcLoader_h
#define RecordingsStoreFWObjcLoader_h
#import 
// NS_ASSUME_NONNULL_BEGIN: https://developer.apple.com/swift/blog/?id=25
NS_ASSUME_NONNULL_BEGIN
@interface RecordingsStoreFWObjcLoader : NSObject
@end
NS_ASSUME_NONNULL_END
#endif /* RecordingsStoreFWObjcLoader_h */
```

Create the Objective-C implementation file for the class:

1.  Right-click the RecordingsStoreFW project folder within the **Project Navigator** and select **New File from Template…**

2.  Select the **Objective-C** **File** template under the **Source** group

3.  Name the file **RecordingsStoreFWObjcLoader.m**

Add the following code to the class file to define the Objective-C class implementation, which implements the +load() method for the registration hook:

```
#import "RecordingsStoreFWObjcLoader.h"
#import "RecordingsStoreFW/RecordingsStoreFW-Swift.h"
@implementation RecordingsStoreFWObjcLoader
// https://developer.apple.com/documentation/objectivec/nsobject/1418815-load
+(void) load {
if ([[RecordingsStoreFWSwiftLoader alloc] init]) {
NSLog(@"RecordingsStoreFWObjcLoader.load() succeeded...");
} else {
NSLog(@"RecordingsStoreFWObjcLoader.load() failed...");
}
}
@end
```

Since **RecordingsStoreFW/RecordingsStoreFW-****Swift****.h** is a non-visible bridging header needed to support the referencing of Swift classes from within Objective-C, the following build setting needs to be set to force the compiler to generate it:

1.  Select the **RecordingsStoreFW** project folder within the **Project Navigator**

2.  Select the **RecordingsStoreFW** target

3.  Select the **Build Settings** tab and search for **Install Generated Header**, and set it to **YES**

Note

RecordingsStoreFWObjcLoader.m will not compile until the next step has been completed.

Create the Swift file for registering the framework:

1.  Right-click the RecordingsFW project folder within the **Project Navigator** and select **New File from Template…**

2.  Select the **Swift** **File** template under the **Source** group

3.  Name the file **RecordingsStoreFWSwiftLoader.swift**

Add the following code to the class to perform the framework registration, so that the app can expose the framework’s functionality:

```
import Foundation
@objcMembers public class RecordingsStoreFWSwiftLoader: NSObject {
override public init() {
super.init()
initViews()
}
func initViews() {
let config = [ "bundle": "com.gw.RecordingsStoreFW", "storyboard": "RecordingsStoreUI", "view": "RecordingsStoreVC", "title": "Pro Features", "icon": "storefront"]
var configs: [[String:String]] = []
if let persistedConfigs = UserDefaults.standard.object(forKey: "views") as? [Dictionary], !persistedConfigs.isEmpty {
configs.append(contentsOf: persistedConfigs)
if !findConfig(configs: persistedConfigs) {
configs.append(config)
}
} else {
configs.append(config)
}
UserDefaults.standard.set(configs, forKey: "views")
}
func findConfig(configs: [[String:String]]) -> Bool {
var found = false
for config in configs {
if config["bundle"] == "com.gw.RecordingsStoreFW", config["storyboard"] == "RecordingsStoreUI", config["view"] == "RecordingsStoreVC" {
found = true
break
}
}
return found
}
}
```

Add **initFeatures()** to **RecordingsStoreFWSwiftLoader** to fetch In-App Purchases persisted to Core Data, so that they can be propagated via UserDefaults.

```
import Foundation
@objcMembers public class RecordingsStoreFWSwiftLoader: NSObject {
override public init() {
super.init()
initViews()
initFeatures()
}
func initFeatures() {
var publish = false
var tracks: Int16 = 1
let manager = SwiftDataManager.shared
if let config = manager.configuration {
publish = config.publishRecordings
tracks = config.trackCount
} else {
// No existing configuration — create a default one
manager.saveConfiguration(existing: nil, publishRecordings: false, trackCount: 1)
if let config = manager.configuration {
publish = config.publishRecordings
tracks = config.trackCount
}
}
UserDefaults.standard.set(["publishRecordings": publish, "trackCount": tracks], forKey: "features")
}
}
```

## Icing on the Cake

Set the app as the active scheme in the scheme drop-down, build the app, and run it using one of the Xcode simulators or an actual device. The code should compile and run properly. The results should match the screenshot in **Figure** [**8-1**](#678928_1_En_8_Chapter.xhtml#Fig1).

![A screenshot of a mobile app interface displayed on an iPhone 17 Pro Max. The app screen titled “Add Features” lists options for purchasing features: “Publish Recordings” for $3.99, “2 Recording Tracks” for $0.99, “3 Recording Tracks” for $1.99, and “4 Recording Tracks” for $2.99\. The bottom navigation bar includes icons labeled “Pro Features,” “Recording Studio,” “Recordings,” and “Settings.” The background shows a development environment with project files and settings for an app named “ScratchTraxApp2” in Xcode.](images/678928_1_En_8_Chapter/678928_1_En_8_Fig1_HTML.jpg)

Figure 8-1

Xcode Simulator

When the frameworks are loaded, they register their view controllers. The app retrieves these registrations from `UserDefaults`, instantiates the corresponding view controllers, integrates them into its own user interface, and overrides any framework Property List settings as needed. In this case, the app will show **local audio recordings** in the Recordings feature.

Amazing! Two distinct apps have now been created using the same frameworks. Both load the frameworks dynamically without relying on a protocol, and neither requires code changes to support different operation modes or functionality.

## Driving Revenue Through In-App Purchases

In this chapter, **ScratchTraxApp2** simulates a digital recording studio. Local recordings are handled through the Recordings view, while audio track management and song publishing are controlled via the Pro Features view.

Simulated In-App Purchases will be used to test the proper integration of dynamic feature enablement.

**Use Case 1:**

![A screenshot of a development environment displaying an iOS app interface on an iPhone 17 Pro Max simulator. The app shows a list titled “Local Recordings” with two entries: “My Acoustic Guitar Ballad” and “My Electric Guitar Anthem,” both credited to John Doe for words and music in 2025\. The development environment includes project files and settings for “ScratchTraxApp2” in Xcode, with various panels showing file directories and project configurations.](images/678928_1_En_8_Chapter/678928_1_En_8_Fig3_HTML.jpg)

Figure 8-3

Xcode Simulator

![A screenshot of an iOS app development environment showing a simulated iPhone screen with the “Add Features” menu. The menu lists options for purchasing publishing and recording features, with prices ranging from $0.99 to $2.99\. The development interface includes project files and settings for an app named “ScratchTraxApp2,” with various folders and configuration options visible.](images/678928_1_En_8_Chapter/678928_1_En_8_Fig2_HTML.jpg)

Figure 8-2

Xcode Simulator

1.  Select the Pro Features tab and purchase the “Publish Recordings” In-App Purchase. The IAP will become disabled, indicating the purchase was made.

2.  Navigate to the Recordings view and select the Publish Recording icon on the left of the navigation controller. The Publish Recording view will be displayed.

3.  Shut down and restart the app to confirm that the IAP remains disabled, verifying persistence through SwiftData (**Figure** [**8-2**](#678928_1_En_8_Chapter.xhtml#Fig2) and **Figure** [**8-3**](#678928_1_En_8_Chapter.xhtml#Fig3)).

**Use Case 2:**

![A screenshot of an Xcode development environment displaying a project named “ScratchTraxApp2.” The main window shows a simulated iPhone interface with a “New Recording” screen featuring a digital audio mixer with sliders and knobs. The left sidebar lists project files, and the right panel displays project settings. The interface includes tabs for “Pro Features,” “Recording Studio,” “Recordings,” and “Settings.”](images/678928_1_En_8_Chapter/678928_1_En_8_Fig5_HTML.jpg)

Figure 8-5.

![A screenshot of an iOS app development environment in Xcode. The main focus is a simulator displaying an app interface titled “Add Features,” listing options for purchasing features like “Publish Recordings” for $3.99, and various “Recording Tracks” priced at $0.99, $1.99, and $2.99\. The Xcode interface shows project files and settings on the left and right panels, with the app’s code and configuration details visible.](images/678928_1_En_8_Chapter/678928_1_En_8_Fig4_HTML.jpg)

Figure 8-4.

1.  Select the Pro Features tab and purchase the “3 Recording Tracks” In-App Purchase. The IAP will become disabled, indicating the purchase was made.

2.  Navigate to the Recording Studio view and verify that three recording tracks are now enabled.

3.  Shut down and restart the app to confirm that the IAP remains disabled, verifying persistence through SwiftData (**Figure** [**8-4**](#678928_1_En_8_Chapter.xhtml#Fig4) and **Figure** [**8-5**](#678928_1_En_8_Chapter.xhtml#Fig5)).

In this chapter, you successfully unified multiple frameworks into the ScratchTraxApp2 App using dynamic runtime integration. By modifying frameworks as needed and leveraging In-App Purchases across modules, you built a cohesive, modular, and revenue-capable app without changing code. This chapter reinforces scalable architecture principles, demonstrating how dynamic frameworks can work together seamlessly.

With multiple frameworks now unified in a single app, you’re ready to ensure your applications can reach a global audience. In the next chapter, we’ll explore how to localize frameworks for multilingual support, adapting interfaces and content for different languages and regions.

# 9. Internationalizing Frameworks for Multilingual Support

Localizing the SettingsFW Framework

Internationalization is a complex topic that spans User Experience Design, User Interface Development, Language Translation, Date and Time Formats, Currency, Cultural Sensitivity, Legal and Regulatory Compliance, Character Encoding and Font Support, Keyboard and Input Method Support, and Accessibility (ADA and Accessibility Standards).

This chapter will focus on localization and best practices for developing iOS frameworks that automatically adjust to different language settings.

A complete set of topics on the many facets of localization, including text, assets, layouts, and import/export tools, is available from the Apple Developer Documentation.^([^(29)](#fn29))

In this chapter, we’ll focus on localizing the SettingsFW Framework to provide multilingual support in iOS apps. You’ll learn best practices for adapting text to different languages, ensuring a seamless user experience. By the end, ScratchTraxApp2 will be able to display the Settings view in multiple languages, preparing your frameworks for international audiences.

## Localizing a View Controller

When developing an iOS framework that includes a user interface, it’s best to localize the relevant code to simplify future efforts toward internationalization. The display text used throughout this book’s sample projects was intentionally hard-coded for clarity. This chapter will walk through the steps needed to localize the **SettingsFW** framework for multiple languages.

The **SettingsTVC** class defines the framework’s user interface, which includes three UILabel components: **nameLabel**, **emailLabel**, and **websiteLabel**. These labels must be properly initialized when the view loads, using values from the appropriate language file. In a production app, the corresponding value labels **nameValueLabel**, **emailValueLabel**, and **websiteValueLabel** would typically be set dynamically through a network API request. For the purposes of this example, however, those values remain hard-coded.

**String**, the newer and preferred way to localize strings in Swift, will be used to retrieve the UILabel values from the appropriate language file rather than using the legacy **NSLocalizedString**.

```
String("settingsTVCUILabelName", bundle: Bundle(for: SettingsTVC.self), comment: "Name field label in the Settings view")
```

The first parameter is the key to be defined in the appropriate language file. Although no strict naming conventions are enforced for keys, they must be unique across the framework code base and should be easily identifiable when translations occur. This becomes particularly important when the number of key-value pairs becomes large. If no key is found in the translation file, the key is displayed.

Note

The Xcode String Catalog Editor will convert dot-delimited, hyphen-delimited, underscore-delimited, and camelCase keys to lowerCamelCase variations. If a specific format is desired for keys, the Strings catalog can be edited manually (right-click + Open As/Source Code).

The second parameter is the framework bundle ID, which is critical for the function to work correctly. If a variation of String() is used that doesn’t include the bundle ID, the function will attempt to retrieve the string from the app’s language file instead of the framework’s language file, causing it to fail.

The third parameter is a comment that provides context for translators. It doesn’t appear in the app’s UI, but it’s stored in the generated translation file when extracting localized strings using Xcode’s **Export for Localization** feature. Since the Comment parameter will be provided by the String Catalog, it can be left out.

Integrate String() into the SettingsTVC class to localize the displayable text in the Settings feature:

```
override func viewDidLoad() {
super.viewDidLoad()
let frameworkBundle = Bundle(for: SettingsTVC.self)
self.navigationItem.title = String(localized: "settingsTVCUINavigationItemTitle", bundle: frameworkBundle)
self.nameLabel.text = String(localized: "settingsTVCUILabelName", bundle: frameworkBundle)
self.emailLabel.text = String(localized: "settingsTVCUILabelEmail", bundle: frameworkBundle)
self.websiteLabel.text = String(localized: "settingsTVCUILabelWebsite", bundle: frameworkBundle)
}
```

## Creating a String Catalog

Create a String Catalog for the SettingsFW framework:

![Screenshot of a file template selection window for creating a new file in a development environment. The options include “String Catalog,” “Strings File (Legacy),” and “Stringsdict File (Legacy).” The interface shows tabs for different platforms like iOS, macOS, watchOS, tvOS, visionOS, and DriverKit. A search bar at the top right contains the term “string.” The “Next” button is highlighted in orange at the bottom right.](images/678928_1_En_9_Chapter/678928_1_En_9_Fig1_HTML.jpg)

Figure 9-1

String Catalog Template

1.  Right-click the **SettingsFW** folder within the **Project Navigator** and select **New File from Template…**

2.  Select the **String Catalog** template under the **Resource** group and **Next** (**Figure** [**9-1**](#678928_1_En_9_Chapter.xhtml#Fig1))

3.  Name the file **Localizable.xcstrings**

## Add Localizations to the String Catalog

Add the English (en), French (fr), and Italian (it) localizations to the String Catalog:

1.  Select the .xcstrings file within the **Project Navigator**

2.  Select **Add new language** via + icon at the bottom left of the **Localizations Navigator** and add **French (fr)** and then **Italian (it)**, as English (en) is created by default

## Add Values to the English Localization

Add localization values to the English Localization:

![Screenshot of an Xcode interface displaying a project named “ScratchTraxApp.” The left panel shows a file directory with various files, including “SettingsFW.xcstrings” selected. The central panel lists key-value pairs for localization, with keys like “settingsTVCUILabelName” and corresponding English translations such as “Name” and “Settings.” The right panel shows details about the selected file, including its name and location. The bottom panel displays a debug message: “Message from debugger: killed.”](images/678928_1_En_9_Chapter/678928_1_En_9_Fig2_HTML.jpg)

Figure 9-2

String Catalog Editor

1.  Select **English** in the **String Catalog Navigator**

2.  Select **Add a new manual string** using + icon at the top left of the empty English Localization editor, and set Key to **settingsTVCUINavigationItemTitle**, English (en) to **Settings**, and Comment to “**Title for the Settings view**” (**Figure** [**9-2**](#678928_1_En_9_Chapter.xhtml#Fig2))

3.  Repeat Step 2 for the remaining strings

## Add Values to the French Localization

Add localization values to the French Localization:

1.  Select **French** in the **String Catalog Navigator**

2.  Select **Add a new manual string** using + icon at the top left of the empty English Localization editor, and set Key to **settingsTVCUINavigationItemTitle**, French (fr) to **Settings** 🇫🇷 (you can add an emoji for France by selecting Control-Command-Space and searching for France), and Comment to “**Title for the Settings view**”

3.  Repeat Step 2 for the remaining strings

## Add Values to the Italian Localization

Add localization values to the Italian Localization:

1.  Select **Italian** in the **String Catalog Navigator**

2.  Select **Add a new manual string** using + icon at the top left of the empty English Localization editor, and set Key to **settingsTVCUINavigationItemTitle**, French (fr) to **Settings** 🇮🇹 (you can add an emoji for Italy by selecting Control-Command-Space and searching for Italy), and Comment to “**Title for the Settings view**”

3.  Repeat Step 2 for the remaining strings

## Creating a Localization File for the App

For the SettingsFW framework to display in the correct locale, the app containing the framework must also contain a localization file, albeit empty. Without doing so will result in the default locale, English, always being displayed.

Create a localization file for ScratchTraxApp2:

![Screenshot of an Xcode project interface showing a file structure on the left with “Localizable.stringsdict” selected. The center panel displays a dictionary with a localized string key. The right panel shows file details, including name, location, and localization options for English, French, and Italian. The project is named “ScratchTraxApp2” for an iPhone 17 Pro Max.](images/678928_1_En_9_Chapter/678928_1_En_9_Fig3_HTML.jpg)

Figure 9-3

Stringsdict Editor

1.  Right-click the **ScratchTraxApp2** folder within the **Project Navigator** and select **New File from Template…**

2.  Select the **Stringsdict File (Legacy)** template under the **Resource** group and **Next**

3.  Name the file **Localizable.xcstrings** and create the empty file

4.  Select **Localizable.xcstrings** under the **ScratchTraxApp2** folder within the **Project Navigator** and select **Show the File inspector** on the right

5.  Under the **Localization** section, check English, French, and Italian (**Figure** [**9-3**](#678928_1_En_9_Chapter.xhtml#Fig3))

## Configuring the Scheme

Before running the app in an iOS simulator, the **App Language** and **App Region** settings for the **ScratchTraxApp2** Scheme need to be set to the desired language:

![Screenshot of a software development environment showing the “Options” tab for a project named “ScratchTraxApp2.” The interface includes various configuration settings such as Core Location, App Data, GameKit Configuration, and more. Options like “Allow Location Simulation” and “Enable Debug Mode” are visible. The app language and region are set to French and France, respectively. The interface includes buttons for duplicating and managing schemes, and a “Close” button.](images/678928_1_En_9_Chapter/678928_1_En_9_Fig4_HTML.jpg)

Figure 9-4

Edit Scheme Options

1.  Select **ScratchTraxApp2** in the scheme selection drop-down field above the editor

2.  Select **Edit Scheme…** in the scheme selection drop-down field above the editor

3.  Select the **Run** scheme in the Scheme navigator and the **Options** tab in the Scheme settings

4.  Change **App Language** to French and the **App Region** to France (**Figure** [**9-4**](#678928_1_En_9_Chapter.xhtml#Fig4))

## Running the App in a Simulator

When the app is run in the iOS simulator, the Settings view Title, Name, Email Address, and Website labels will be displayed in the French Locale (**Figure** [**9-5**](#678928_1_En_9_Chapter.xhtml#Fig5)).

![Screenshot of a development environment displaying an iPhone simulator with a “Settings” screen. The screen shows contact information for “Graphixware, LLC,” including an email address and website URL. The simulator is part of a project named “ScratchTraxApp2,” with localization options for English, French, and Italian. The development interface includes a file directory and settings for localization and text indentation.](images/678928_1_En_9_Chapter/678928_1_En_9_Fig5_HTML.jpg)

Figure 9-5

Xcode Simulator

## Running the App on a Device

In order to run the app on a real device and receive the same results, the following steps will need to be performed:

![Screenshot of a development environment displaying an iPhone simulator with a “Settings” screen. The screen shows contact information for “Graphixware, LLC,” including an email address “gw@graphixware.com” and a website “https:/graphixware.com,” with Italian flags next to each item. The development environment includes a file directory on the left, showing various project files, and a panel on the right with localization settings for English, French, and Italian. The bottom of the simulator displays navigation icons for “Pro Features,” “Recording Studio,” “Recordings,” and “Settings.”](images/678928_1_En_9_Chapter/678928_1_En_9_Fig6_HTML.jpg)

Figure 9-6

App Running on Device

1.  Edit the Scheme again and set the **App Language** to **System Language**, **App Region** to **System Region**, and **Localization Debugging** to checked (SwiftUI automatically shows non-localized text in uppercase)

2.  Select a real device under **Run Destinations** via Xcode and then select Run to install the app on the device

3.  On the device, go to **Settings****/General/Language & Region** and **Add Italian** (choose **Use Italian** when prompted)

4.  After the device reboots, run the app on the device (outside of Xcode) to view the results (**Figure** [**9-6**](#678928_1_En_9_Chapter.xhtml#Fig6))

In this chapter, you localized the SettingsFW Framework to support multiple languages, applying best practices for text adaptation. By completing this work, ScratchTraxApp2 can now display settings in different languages, demonstrating how iOS frameworks can be prepared for international users while maintaining modularity and usability

After preparing your frameworks for international audiences, you’re ready to explore how to make your apps visually flexible. In the next chapter, we’ll implement dynamic branding to allow consistent, centralized styling across frameworks without changing code.

Footnotes [1](#678928_1_En_9_Chapter.xhtml#Fn1_source)  

# 10. Designing Dynamic Branding in iOS Frameworks

Enhancing the ScratchTraxApp2 App

Branding involves creating a distinct and memorable identity for an app that ensures consistency across all platforms and materials. Key aspects of branding include the app icon, splash screen, color palette, typography, imagery, iconography, accessibility, and UI elements.

Comprehensive documentation can be found on the Apple Developers website regarding Branding,^([^(30)](#fn30)) Human Interface Guidelines (HIG),^([^(31)](#fn31)) UIAppearance,^([^(32)](#fn32)) and Configuration.^([^(33)](#fn33))

In this chapter, we’ll enhance the ScratchTraxApp2 App by implementing dynamic branding across frameworks. You’ll learn how to centralize font and style attributes for both UIKit and SwiftUI components, ensuring consistent app identity. By applying these techniques, your app will be able to update branding dynamically without modifying the underlying code.

## Dynamic Branding Techniques

Dynamic branding will first be demonstrated with UIKit components, followed by SwiftUI components. UIKit elements will use the **Configuration** feature when available and alternative techniques otherwise. SwiftUI elements will leverage **Environment Keys**. Class, protocol, and struct extensions will be used to apply branding consistently. All dynamic branding will be managed from a centralized location to ensure uniform appearance across the app’s user interfaces, which are being provided by the frameworks.

In iOS 26+, UIKit components that support the modern Configuration feature include UIButton, UIBarButtonItem, UISegmentedControl, UIToolbar, UITabBarItem, UITabBar, UINavigationBar, UIStackView buttons embedded in menus, and UICollectionView/UITableView cells via content configuration. Components that don’t support the Configuration feature and still require direct property settings, or use of the UIAppearance feature, include UILabel, UITextField, UITextView, UIImageView, UISlider, UISwitch, UIProgressView, UIActivityIndicatorView, and most custom UIView subclasses.

SwiftUI components will be branded by defining Environment Keys for applicable component fonts. Extensions on View (like .appBodyFont() and .appButtonFont()) apply the fonts automatically, ensuring consistent branding across all SwiftUI components throughout the app.

## Creating a Property List for Branding

A Property List (plist) will be used to define and manage branding attributes, providing a centralized, human-readable, and structured format that can be loaded at runtime to configure fonts, sizes, and colors without the need for code changes. Translators or localization teams can use this plist, or an equivalent CSV version, to brand attributes.

Create a Property List in the ScratchTraxApp2 project:

1.  Right-click the **ScratchTraxApp2** folder within the **Project Navigator** and select **New File from Template…**

2.  Select the **Property List** template under the **Resource** group and name it **Theme.plist**

3.  Right-click **Theme.plist** in the Project Navigator and select **Open As/Source Code**

4.  Replace **<dict/>** with the example branding attributes below

5.  Right-click **Theme.plist** in the Project Navigator and select **Open As/Property List**

```
bodyFontName
SnellRoundhand
bodyFontSize
24
bodyFontColor
darkGray
titleFontName
SnellRoundhand-Bold
titleFontSize
30
buttonBaseBackgroundColor
systemTeal
buttonBaseForegroundColor
white
buttonCornerRadius
12
buttonStrokeColor
white
buttonStrokeWidth
2
buttonHighlightedColor
systemRed
buttonDisabledColor
systemGray

```

## Centralizing Branding Attributes Across Frameworks

The AppDelegate is an ideal place to read branding attributes from a plist to populate UserDefaults, as it runs once when the app launches, before any views or view controllers are loaded. This ensures that all UI elements and code that rely on branding values, fonts, or other configuration settings have access to the data from the very beginning. By initializing UserDefaults here, your theming system is guaranteed to be ready app-wide, whether in UIKit components, SwiftUI views, or other services, without needing to repeat setup in multiple places.

Select the AppDelegate class under the ScratchTraxApp2 folder and replace **didFinishLaunchingWithOptions()** with the following:

```
func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
if let url = Bundle.main.url(forResource: "Theme", withExtension: "plist"),
let data = NSDictionary(contentsOf: url) as? [String: Any] {
UserDefaults.standard.set(data, forKey: "Theme")
}
return true
}
```

The code simply reads the plist dictionary and propagates it across the app and frameworks via UserDefaults.

## Creating UIKit Class Extensions

Using **class extensions** is convenient for applying branding attributes because they allow you to encapsulate styling logic in one place without modifying individual view controllers. By extending classes like UILabel or UIButton, you can automatically apply fonts, colors, and other theme settings whenever a component is instantiated, ensuring consistency across the app, reducing repetitive code, and making it easy to update branding in the future.

When creating UIKit class extensions, the branding attributes will be applied to the component via **awakeFromNib()** and **didMoveToSuperView()**. awakeFromNib() is called after the component is loaded from a storyboard or XIB, so it’s ideal for initializing fonts, colors, or other styling immediately after the view exists but before it appears. didMoveToSuperview() is called when the view is added to a view hierarchy, covering cases where the component may be created programmatically.

Add the following class extensions to the AppDelegate class to apply branding attributes circulated via UserDefaults to UILabel components:

```
public extension UILabel {
override func awakeFromNib() {
super.awakeFromNib()
applyBrandedFont()
}
override func didMoveToSuperview() {
super.didMoveToSuperview()
applyBrandedFont()
}
private func applyBrandedFont() {
guard let theme = UserDefaults.standard.dictionary(forKey: "Theme"),
let name = theme["bodyFontName"] as? String,
let size = theme["bodyFontSize"] as? CGFloat else { return }
self.font = UIFont(name: name, size: size) ?? UIFont.systemFont(ofSize: size)
if let colorName = theme["bodyFontColor"] as? String {
self.textColor = UIColor(namedSystemOrHex: colorName) ?? .darkGray
}
}
}
extension UIColor {
convenience init?(namedSystemOrHex name: String?) {
guard let name = name else { return nil }
let systemColors: [String: UIColor] = [
"systemRed": .systemRed,
"systemBlue": .systemBlue,
"systemTeal": .systemTeal,
"systemGray": .systemGray,
"white": .white,
"black": .black,
"darkGray": .darkGray,
"lightGray": .lightGray
]
if let color = systemColors[name.lowercased()] {
self.init(cgColor: color.cgColor)
return
}
// Hex format: #RRGGBB
if name.hasPrefix("#"), name.count == 7 {
let r = CGFloat(Int(name.dropFirst().prefix(2), radix: 16) ?? 0) / 255.0
let g = CGFloat(Int(name.dropFirst(3).prefix(2), radix: 16) ?? 0) / 255.0
let b = CGFloat(Int(name.dropFirst(5).prefix(2), radix: 16) ?? 0) / 255.0
self.init(red: r, green: g, blue: b, alpha: 1.0)
return
}
return nil
}
}
```

Since UILabel doesn’t support the Configuration feature, the font and text color attributes will be applied directly to the component using the plist settings exposed via UserDefaults.

The UIColor class extension is a convenience class to create colors from either system color names (like systemRed or systemTeal) or hex strings (like `#RRGGBB`). This allows branding attributes stored in the plist to be applied dynamically, supporting both standard UIKit colors and custom colors.

Add the following class extension to the AppDelegate class to apply branding to UIButton components:

```
public extension UIButton {
override func awakeFromNib() {
super.awakeFromNib()
applyBrandedFont()
}
override func didMoveToSuperview() {
super.didMoveToSuperview()
applyBrandedFont()
}
private func applyBrandedFont() {
guard let theme = UserDefaults.standard.dictionary(forKey: "Theme"),
let name = theme["titleFontName"] as? String,
let size = theme["titleFontSize"] as? CGFloat else { return }
var config = UIButton.Configuration.filled()
// Font
config.titleTextAttributesTransformer = UIConfigurationTextAttributesTransformer { attrs in
var newAttrs = attrs
newAttrs.font = UIFont(name: name, size: size) ?? UIFont.systemFont(ofSize: size, weight: .bold)
return newAttrs
}
// Base colors and styling
config.baseBackgroundColor = UIColor(namedSystemOrHex: theme["buttonBaseBackgroundColor"] as? String) ?? .systemTeal
config.baseForegroundColor = UIColor(namedSystemOrHex: theme["buttonBaseForegroundColor"] as? String) ?? .white
config.background.cornerRadius = theme["buttonCornerRadius"] as? CGFloat ?? 12
config.background.strokeColor = UIColor(namedSystemOrHex: theme["buttonStrokeColor"] as? String) ?? .white
config.background.strokeWidth = theme["buttonStrokeWidth"] as? CGFloat ?? 2
self.configuration = config
// Dynamic state colors
self.configurationUpdateHandler = { button in
var updated = button.configuration ?? UIButton.Configuration.filled()
if button.isHighlighted {
updated.baseBackgroundColor = UIColor(namedSystemOrHex: theme["buttonHighlightedColor"] as? String) ?? .systemRed
} else if !button.isEnabled {
updated.baseBackgroundColor = UIColor(namedSystemOrHex: theme["buttonDisabledColor"] as? String) ?? .systemGray
} else {
updated.baseBackgroundColor = UIColor(namedSystemOrHex: theme["buttonBaseBackgroundColor"] as? String) ?? .systemTeal
}
button.configuration = updated
}
}
}
```

Since UIButton supports the Configuration feature, the font and text color attributes will be applied to the component using this feature, using the plist settings exposed via UserDefaults.

The UIButton class extension also includes a **configurationUpdateHandler** to dynamically change the button text and background for different action states.

## Running the App in a Simulator

When the app is run in the iOS simulator, the Settings view will now display the Title, Name, Email Address, and Website labels using the branded styles (**Figure** [**10-1**](#678928_1_En_10_Chapter.xhtml#Fig1)).

![A screenshot of a development environment displaying an iOS app interface on an iPhone simulator. The app shows a settings page with text fields for “Name,” “Email Address,” and “Website,” filled with “Graphixware, LLC,” “gw@graphixware.com,” and “http:/graphixware.com” respectively. The development environment includes a project navigator with files and folders related to the app’s codebase.](images/678928_1_En_10_Chapter/678928_1_En_10_Fig1_HTML.jpg)

Figure 10-1

Xcode Simulator

## Creating SwiftUI Class Extensions and Environment Keys

SwiftUI class extensions and Environment Keys will be used to centralize and streamline branding for SwiftUI components. Environment Keys allow font and style values loaded from the plist to be made globally accessible across all SwiftUI views. This approach ensures consistent styling, reduces repetitive code, and keeps the branding system decoupled from individual view implementations. The changes in this section will be made to the **RecordingsStoreFW** framework.

Create a new Swift class, **ThemeEnvironment**, under the RecordingsStoreFW framework folder, and add the following environment keys and class extensions:

```
import SwiftUI
private struct AppBodyFontKey: EnvironmentKey {
static let defaultValue: Font = .body
}
private struct AppButtonFontKey: EnvironmentKey {
static let defaultValue: Font = .headline
}
public extension EnvironmentValues {
var appBodyFont: Font {
get { self[AppBodyFontKey.self] }
set { self[AppBodyFontKey.self] = newValue }
}
var appButtonFont: Font {
get { self[AppButtonFontKey.self] }
set { self[AppButtonFontKey.self] = newValue }
}
}
public extension View {
/// Applies theme-based fonts from UserDefaults to the environment.
func applyThemeEnvironment() -> some View {
let theme = UserDefaults.standard.dictionary(forKey: "Theme")
let bodyFont: Font = {
if let name = theme?["bodyFontName"] as? String,
let size = theme?["bodyFontSize"] as? CGFloat {
return Font.custom(name, size: size, relativeTo: .body)
}
return .body
}()
let buttonFont: Font = {
if let name = theme?["titleFontName"] as? String,
let size = theme?["titleFontSize"] as? CGFloat {
return Font.custom(name, size: size, relativeTo: .headline)
}
return .headline
}()
return self
.environment(\.appBodyFont, bodyFont)
.environment(\.appButtonFont, buttonFont)
}
}
```

Replace the code in the **RecordingsStoreView** class with the following code, which makes minor changes to remove hard-coded fonts and styles and applies the themed branding using the environment variables:

```
import SwiftUI
struct RecordingsStoreView: View {
@State private var selectedIAP: IAP?
@State private var showAlert = false
@State private var loaded = false
@StateObject private var viewModel: IAPViewModel
@StateObject private var dataManager = SwiftDataManager.shared
init(viewModel: IAPViewModel) {
_viewModel = StateObject(wrappedValue: viewModel)
}
var body: some View {
NavigationView {
List(selection: $selectedIAP) {
ForEach(groupByCategory(viewModel.getIAPs()), id: \.0) { category in
IAPCategorySectionView(
category: category,
isPurchased: isPurchased,
selectAction: { iap in
selectedIAP = iap
showAlert = true
}
)
}
}
.listStyle(.plain)
.navigationBarTitle("Add Features", displayMode: .large)
.alert("Add Features", isPresented: $showAlert) {
Button("OK") {
if let iap = selectedIAP {
updateFeatures(type: iap.type)
}
}
Button("Cancel", role: .cancel) {}
} message: {
AlertMessageText(message: "Do you want to purchase \"\(selectedIAP?.name ?? "?")\"")
}
}
.onAppear {
if !loaded {
loaded = true
viewModel.refresh()
}
}
}
private struct IAPRowView: View {
let iap: IAP
let isDisabled: Bool
let action: () -> Void
@Environment(\.appButtonFont) private var titleFont
@Environment(\.appBodyFont) private var bodyFont
var body: some View {
HStack {
Button(action: action) {
Text("\(Image(systemName: "music.note")) \(iap.name)")
.font(titleFont)
}
Spacer()
Text(NumberFormatter.localizedString(from: NSNumber(value: iap.price), number: .currency))
.font(bodyFont)
}
.listRowSeparator(.hidden)
.disabled(isDisabled)
}
}
private struct IAPCategorySectionView: View {
let category: (String, [IAP])
let isPurchased: (IAP) -> Bool
let selectAction: (IAP) -> Void
@Environment(\.appButtonFont) private var titleFont
var body: some View {
Section(header: Text(category.0).font(titleFont)) {
ForEach(category.1) { item in
IAPRowView(
iap: item,
isDisabled: isPurchased(item),
action: { selectAction(item) }
)
}
}
}
}
private struct AlertMessageText: View {
let message: String
@Environment(\.appBodyFont) private var bodyFont
var body: some View {
Text(message)
.font(bodyFont)
}
}
private func groupByCategory(_ iaps: [IAP]) -> [(String, [IAP])] {
let grouped = Dictionary(grouping: iaps, by: { $0.category })
return grouped.sorted(by: { $0.key  Bool {
if let features = UserDefaults.standard.object(forKey: "iaps") as? [String: Any] {
switch iap.type {
case .publishRecordings:
return features["publishRecordings"] as? Bool ?? false
case .twoTracks:
if let trackCount = features["trackCount"] as? Int16 { return trackCount > 1 }
return false
case .threeTracks:
if let trackCount = features["trackCount"] as? Int16 { return trackCount > 2 }
return false
case .fourTracks:
if let trackCount = features["trackCount"] as? Int16 { return trackCount > 3 }
return false
}
}
return false
}
private func updateFeatures(type: IAPType) {
guard let config = dataManager.configuration else {
saveConfiguration(existing: nil, type: type)
return
}
saveConfiguration(existing: config, type: type)
}
private func saveConfiguration(existing: Configuration?, type: IAPType) {
var publishRecordings = false
var trackCount: Int16 = 1
switch type {
case .publishRecordings:
publishRecordings = true
trackCount = existing?.trackCount ?? 1
case .twoTracks:
publishRecordings = existing?.publishRecordings ?? false
trackCount = 2
case .threeTracks:
publishRecordings = existing?.publishRecordings ?? false
trackCount = 3
case .fourTracks:
publishRecordings = existing?.publishRecordings ?? false
trackCount = 4
}
dataManager.saveConfiguration(existing: existing, publishRecordings: publishRecordings, trackCount: trackCount)
UserDefaults.standard.set(["publishRecordings": publishRecordings, "trackCount": trackCount], forKey: "iaps")
}
}
struct RecordingsStoreView_Previews: PreviewProvider {
static var previews: some View {
RecordingsStoreView(
viewModel: IAPViewModel(service: IAPService.shared, endpoint: .fetchInAppPurchases)
)
}
}
```

Replace the code in the **RecordingsStoreVC** class with the following code, which integrates the centralized fonts and styles into the SwiftUI components and views using the .environment feature:

```
import SwiftUI
import UIKit
class RecordingsStoreVC: UIHostingController {
required init?(coder: NSCoder) {
let viewModel = IAPViewModel(
service: IAPService.shared,
endpoint: .fetchInAppPurchases
)
// Build the root view and apply environment values
let view = RecordingsStoreView(viewModel: viewModel)
.applyThemeEnvironment()
.eraseToAnyView()
super.init(coder: coder, rootView: view)
}
}
private extension View {
func eraseToAnyView() -> AnyView { AnyView(self) }
}
```

## Running the App in a Simulator

When the app is run in the iOS simulator, the Pro Features (SwiftUI) view will now display the corresponding text and buttons using the branded styles (**Figure** [**10-2**](#678928_1_En_10_Chapter.xhtml#Fig2)).

![A screenshot showing a development environment for an iOS app named “ScratchTraxApp2.” The interface displays a file directory on the left with various Swift files and resources. In the center, an iPhone simulator shows an app screen titled “Add Features,” listing options like “Publish Recordings” for $3.99 and “Recording Tracks” with different quantities and prices. The bottom of the app screen has a navigation bar with icons labeled “Pro Features,” “Recording Studio,” “Recordings,” and “Settings.” The right side shows build settings for the app.](images/678928_1_En_10_Chapter/678928_1_En_10_Fig2_HTML.jpg)

Figure 10-2

Xcode Simulator

In this chapter, you applied centralized, dynamic branding to the ScratchTraxApp2 App, enabling consistent styling across UIKit and SwiftUI components. By setting font attributes and other visual elements dynamically, you created a flexible, maintainable framework that can adapt branding without code changes, reinforcing modular and scalable app architecture.

With dynamic branding in place, your frameworks are now flexible both visually and functionally. In the final chapter, we’ll explore how to package and distribute these frameworks so they can be easily shared, integrated, and maintained across multiple projects.

Footnotes [1](#678928_1_En_10_Chapter.xhtml#Fn1_source)   [2](#678928_1_En_10_Chapter.xhtml#Fn2_source)   [3](#678928_1_En_10_Chapter.xhtml#Fn3_source)   [4](#678928_1_En_10_Chapter.xhtml#Fn4_source)  

# 11. Packaging and Distribution

There are a number of ways to integrate iOS frameworks into mobile applications, each offering different advantages depending on a project’s structure and distribution needs. Frameworks can be added directly to an Xcode project for tight control and simplicity, as we’ve done in this book. They can also be integrated through dependency managers such as **CocoaPods** and **Swift Package Manager**. CocoaPods provides a well-established system for managing and updating third-party libraries via a centralized repository, while Swift Package Manager offers native support within Xcode for seamless version control and integration. Together, these options give developers flexibility in how they organize, share, and maintain reusable code across iOS apps.

**Direct Project Integration** offers developers complete control over how a framework is incorporated into an iOS app. By manually adding the framework’s source code or compiled bundle directly to the Xcode project, developers can fine-tune build settings, version control, and visibility. One major advantage of this approach is that it allows developers to step through the framework’s source code during debugging, providing full transparency into its internal behavior and interactions with the host app. This makes it particularly useful for internal or proprietary frameworks that are not publicly distributed. However, it requires manual updates whenever the framework changes, which can become cumbersome over time. Managing multiple projects that depend on the same framework can also lead to redundancy and maintenance overhead, making this method less scalable for larger development environments.

**CocoaPods** is a long-standing and widely adopted dependency manager that streamlines the integration of third-party libraries into iOS apps. It automates downloading, linking, and versioning, allowing developers to easily include thousands of open-source frameworks maintained in its central repository. CocoaPods supports both static and dynamic frameworks and is well suited for complex projects with multiple dependencies. On the downside, it requires the creation of an Xcode workspace (which we’re using in our apps), which changes the project structure. Dependency resolution can sometimes introduce version conflicts, and builds may be slower compared to more modern tools like Swift Package Manager.

**Swift Package Manager (SPM)** provides a modern, Apple-supported solution for dependency management, built directly into Xcode. It allows developers to easily add, update, and manage frameworks through simple Git-based integration, reducing setup time and maintaining clean source control. SPM’s tight integration with the Apple toolchain ensures smooth updates and compatibility across projects. However, its library ecosystem is still smaller than CocoaPods, and it lacks some advanced customization options needed for complex build setups. Additionally, certain older or legacy frameworks may not yet support SPM distribution, which can limit its use in mixed-environment projects.

Since CocoaPods and Swift Package Manager are well-documented technologies, this chapter will simply direct you to the resources needed to use these technologies to package and distribute iOS frameworks. By the end, you’ll understand options and best practices for organizing, sharing, and maintaining reusable code across multiple iOS applications.

## CocoaPods

Extensive documentation is available for creating, installing, and integrating CocoaPods:

**Official Documentation**

*   What is CocoaPods^([^(34)](#fn34))

*   Making a CocoaPod^([^(35)](#fn35))

*   Podspec Syntax Reference^([^(36)](#fn36))

*   Trunk Service (Publishing Pods)^([^(37)](#fn37))

*   All CocoaPods Guides Index^([^(38)](#fn38))

**Supplemental Resources**

*   CocoaPods Blog (News and Updates)^([^(39)](#fn39))

*   CocoaPods GitHub Repository^([^(40)](#fn40))

*   CocoaPods Core API Reference^([^(41)](#fn41))

## Swift Package Manager

Extensive documentation on integrating and creating Swift Packages is available from swift.org:

**Official Documentation**

*   Swift Package Manager Overview^([^(42)](#fn42))

*   Creating a Swift Package^([^(43)](#fn43))

*   Adding Package Dependencies to Your App^([^(44)](#fn44))

*   Package.swift Manifest Reference^([^(45)](#fn45))

*   Distributing Binary Frameworks as Swift Packages^([^(46)](#fn46))

**Supplemental Resources**

*   Swift Package Manager GitHub Repository^([^(47)](#fn47))

*   Swift Package Index (Package Discovery)^([^(48)](#fn48))

In this chapter, you explored the options for packaging and distributing iOS frameworks. Beyond integrating frameworks directly into Xcode projects, as covered earlier in this book, you were introduced to alternative distribution methods using dependency managers such as CocoaPods and Swift Package Manager. The approach you choose will depend on factors related to both the development of your frameworks and the audience for which they are intended.

Footnotes [1](#678928_1_En_11_Chapter.xhtml#Fn1_source)   [2](#678928_1_En_11_Chapter.xhtml#Fn2_source)   [3](#678928_1_En_11_Chapter.xhtml#Fn3_source)   [4](#678928_1_En_11_Chapter.xhtml#Fn4_source)   [5](#678928_1_En_11_Chapter.xhtml#Fn5_source)   [6](#678928_1_En_11_Chapter.xhtml#Fn6_source)   [7](#678928_1_En_11_Chapter.xhtml#Fn7_source)   [8](#678928_1_En_11_Chapter.xhtml#Fn8_source)   [9](#678928_1_En_11_Chapter.xhtml#Fn9_source)   [10](#678928_1_En_11_Chapter.xhtml#Fn10_source)   [11](#678928_1_En_11_Chapter.xhtml#Fn11_source)   [12](#678928_1_En_11_Chapter.xhtml#Fn12_source)   [13](#678928_1_En_11_Chapter.xhtml#Fn13_source)   [14](#678928_1_En_11_Chapter.xhtml#Fn14_source)   [15](#678928_1_En_11_Chapter.xhtml#Fn15_source)  

# Summary

Congratulations and thank you for completing the book! You’ve covered a great deal of topics, written a substantial amount of code, and delved into the intricacies of designing reusable, configurable, localizable, and brandable iOS software. Utilize the inspiration and knowledge you've acquired to propel yourself forward in your journey to master the craft of developing world-class iOS mobile apps.

To recap:

**Chapter**[**1**](#678928_1_En_1_Chapter.xhtml) covered the basics of modular iOS framework design using Swift protocols. The chapter guided readers through creating and configuring a framework in Xcode, adding a storyboard and a Cocoa Touch class for an account settings UI, and setting up a Swift protocol to expose a public interface while hiding implementation details. It then explained creating an app target, designing its UI with a tab bar controller, adding and integrating the framework into the app via the protocol, and finally building and running the app to verify successful framework integration.

**Chapter**[**2**](#678928_1_En_2_Chapter.xhtml) focused on designing scalable and maintainable iOS frameworks using the Model-View-ViewModel (MVVM) pattern. It introduced the creation of a new framework, RecordingsFW, and a storyboard-based user interface to display a list of local audio files. The chapter explained defining a protocol and a model to fetch data and connecting it to the view through binding. A local file service was created to read files and provide sample data. The chapter then integrated the ViewModel into the view, added a details view for individual recordings, and defined a public protocol to expose the framework to apps. Finally, it guided creating an app target, integrating the framework, and running the app to verify functionality, demonstrating how MVVM and decoupling supported framework reusability across multiple apps.

**Chapter**[**3**](#678928_1_En_3_Chapter.xhtml) extended RecordingsFW to support remote “published” audio recordings, introducing Dependency Injection and Combine for flexible, decoupled design. A Property List controlled the framework’s mode, and a generic service fetched JSON data via a network API. A ViewModel received the service and endpoint via Dependency Injection, fetched data asynchronously, and published changes to the view. The chapter showed integrating the ViewModel into a View, updating the UI reactively based on Property List or UserDefaults settings, and demonstrated testing with mock services, illustrating how decoupling and Dependency Injection enable reusable, testable framework components.

**Chapter**[**4**](#678928_1_En_4_Chapter.xhtml) demonstrated how to enable dynamic, revenue-driving features in an iOS framework using simulated In-App Purchases. It created a new framework using SwiftData to persist purchase states with modern, type-safe models. A stub API service was integrated to simulate remote In-App Purchase data, while Dependency Injection ensured decoupled, testable design. The chapter covered building a ViewModel to fetch and publish purchase data, integrating it into a SwiftUI view embedded in a UIHostingController, and handling transactions with persistence via SwiftData and UserDefaults. Finally, it showed creating a protocol for app integration, adding an app target, and running the app to display and manage purchased features dynamically.

**Chapter**[**5**](#678928_1_En_5_Chapter.xhtml) demonstrated how to drive revenue growth by enabling dynamic features across multiple iOS frameworks using In-App Purchases. It created a new framework with a storyboard-based UI that mocked a 4-track digital recording studio. A Property List controlled the default number of enabled tracks, while UserDefaults allowed track enablement to be overridden by In-App Purchases. A Swift protocol exposed the framework’s UI to an app target while maintaining decoupling. Finally, the chapter covered creating an app target, integrating the framework via the protocol in SceneDelegate, and running the app to verify that tracks were correctly enabled according to the configuration.

**Chapter**[**6**](#678928_1_En_6_Chapter.xhtml) extended the RecordingsFW framework to support revenue-driven feature enablement via In-App Purchases. It introduced a mock song publishing feature, controlled by a Property List setting and ultimately overridden by UserDefaults to reflect In-App Purchases. The chapter showed integrating a Call-to-Action (CTA) button, with visibility dynamically controlled based on the feature flag. The chapter concluded by configuring the app target to simulate the feature being enabled, building and running the app, and verifying that the CTA displayed correctly, demonstrating how framework functionality can be extended while maintaining decoupling.

**Chapter**[**7**](#678928_1_En_7_Chapter.xhtml) introduced dynamic framework integration, demonstrating how an iOS app can consume a framework without using protocols or requiring code changes. The chapter created the ScratchTraxApp1 app, configuring a storyboard-based Tab Bar Controller UI and adding a Property List to override framework settings. It showed setting up an Xcode workspace containing the app framework projects, adding the framework to the app, and replacing protocol-based integration with a dynamic registration mechanism using UserDefaults. The chapter bridged Swift and Objective-C by creating a loader class, which triggers Swift code to register framework views. Finally, the chapter verified the integration by running the app, demonstrating that the framework UI appeared dynamically and framework Property List settings were properly overridden, providing a fully decoupled, modular architecture.

**Chapter**[**8**](#678928_1_En_8_Chapter.xhtml) demonstrated building *another app* which dynamically integrated multiple frameworks using Objective-C/Swift loader classes and UserDefaults for registration. Dynamic features, including simulated In-App Purchases for publishing recordings and additional tracks, were persisted via SwiftData and propagated through UserDefaults. This architecture allowed multiple apps to share frameworks without code changes, providing a scalable, modular system that supported feature enablement and revenue generation.

**Chapter**[**9**](#678928_1_En_9_Chapter.xhtml) demonstrated localizing a framework to support multiple languages. The view controller was updated to retrieve text values from a framework-specific String Catalog. English, French, and Italian localizations were added. The app’s scheme and simulator were configured to verify localized content, allowing the view to appear in the appropriate language without additional code changes.

**Chapter**[**10**](#678928_1_En_10_Chapter.xhtml) demonstrated implementing dynamic branding across an app using centralized theme management. A Property List defined fonts, sizes, colors, and button styles, which were loaded into UserDefaults during app launch via AppDelegate. UIKit components, including UILabel and UIButton, were extended to automatically apply branding attributes using the Configuration API. SwiftUI components leveraged Environment Keys and view extensions to propagate fonts and styles globally. Running the app in the simulator confirmed that all labels, buttons, and SwiftUI views displayed the appropriate branded appearance without code changes.

**Chapter**[**11**](#678928_1_En_11_Chapter.xhtml) summarized alternative methods for packaging and distributing iOS frameworks. CocoaPods was highlighted as a long-established dependency manager that automated downloading, linking, and versioning of third-party libraries, supporting both static and dynamic frameworks. Swift Package Manager (SPM) was also introduced as a modern, Apple-native solution with seamless Git-based integration and tight Xcode support. The chapter referenced official documentation and supplemental resources for both CocoaPods and SPM to guide framework creation, integration, and distribution.

# Index

## A

*   Accounts[5](#678928_1_En_1_Chapter.xhtml#PB5)
*   AnyPublisher[46–48](#678928_1_En_3_Chapter.xhtml#PB46), [72–74](#678928_1_En_4_Chapter.xhtml#PB72)
*   API[7](#678928_1_En_1_Chapter.xhtml#PB7), [41](#678928_1_En_3_Chapter.xhtml#PB41), [44](#678928_1_En_3_Chapter.xhtml#PB44), [47](#678928_1_En_3_Chapter.xhtml#PB47), [64](#678928_1_En_4_Chapter.xhtml#PB64), [68–70](#678928_1_En_4_Chapter.xhtml#PB68), [72](#678928_1_En_4_Chapter.xhtml#PB72)
*   Apple[3](#678928_1_En_1_Chapter.xhtml#PB3), [5](#678928_1_En_1_Chapter.xhtml#PB5), [42](#678928_1_En_3_Chapter.xhtml#PB42), [63](#678928_1_En_4_Chapter.xhtml#PB63), [117](#678928_1_En_7_Chapter.xhtml#PB117), [147](#678928_1_En_9_Chapter.xhtml#PB147)
*   App Store Connect[63](#678928_1_En_4_Chapter.xhtml#PB63), [70](#678928_1_En_4_Chapter.xhtml#PB70)
*   App Target[13–15](#678928_1_En_1_Chapter.xhtml#PB13), [18](#678928_1_En_1_Chapter.xhtml#PB18), [38](#678928_1_En_2_Chapter.xhtml#PB38), [39](#678928_1_En_2_Chapter.xhtml#PB39), [61](#678928_1_En_3_Chapter.xhtml#PB61), [88](#678928_1_En_4_Chapter.xhtml#PB88), [89](#678928_1_En_4_Chapter.xhtml#PB89), [102](#678928_1_En_5_Chapter.xhtml#PB102), [103](#678928_1_En_5_Chapter.xhtml#PB103), [110](#678928_1_En_6_Chapter.xhtml#PB110), [113](#678928_1_En_7_Chapter.xhtml#PB113)
*   Asset Catalogs[99](#678928_1_En_5_Chapter.xhtml#PB99)
*   Asynchronous[42](#678928_1_En_3_Chapter.xhtml#PB42), [53](#678928_1_En_3_Chapter.xhtml#PB53)
*   Attributes[15](#678928_1_En_1_Chapter.xhtml#PB15), [78](#678928_1_En_4_Chapter.xhtml#PB78), [115](#678928_1_En_7_Chapter.xhtml#PB115), [128](#678928_1_En_8_Chapter.xhtml#PB128)

## B

*   Beeceptor[44](#678928_1_En_3_Chapter.xhtml#PB44), [46](#678928_1_En_3_Chapter.xhtml#PB46)
*   Brandable[179](#Backmatter_001.xhtml#PB179)
*   Branding[157](#678928_1_En_10_Chapter.xhtml#PB157)
*   Bundle ID[101](#678928_1_En_5_Chapter.xhtml#PB101), [119](#678928_1_En_7_Chapter.xhtml#PB119), [131](#678928_1_En_8_Chapter.xhtml#PB131)
*   Bundle Identifier[3](#678928_1_En_1_Chapter.xhtml#PB3), [50](#678928_1_En_3_Chapter.xhtml#PB50)

## C

*   Call-To-Action (CTA)[106](#678928_1_En_6_Chapter.xhtml#PB106)
*   cellForRowAt[25](#678928_1_En_2_Chapter.xhtml#PB25), [33](#678928_1_En_2_Chapter.xhtml#PB33)
*   CocoaPods[70](#678928_1_En_4_Chapter.xhtml#PB70), [176](#678928_1_En_11_Chapter.xhtml#PB176)
*   Cocoa Touch[2](#678928_1_En_1_Chapter.xhtml#PB2), [9](#678928_1_En_1_Chapter.xhtml#PB9), [23](#678928_1_En_2_Chapter.xhtml#PB23), [33](#678928_1_En_2_Chapter.xhtml#PB33), [78](#678928_1_En_4_Chapter.xhtml#PB78), [95](#678928_1_En_5_Chapter.xhtml#PB95), [107](#678928_1_En_6_Chapter.xhtml#PB107)
*   Codable[26](#678928_1_En_2_Chapter.xhtml#PB26), [31](#678928_1_En_2_Chapter.xhtml#PB31)
*   Combine[41](#678928_1_En_3_Chapter.xhtml#PB41), [46](#678928_1_En_3_Chapter.xhtml#PB46), [49](#678928_1_En_3_Chapter.xhtml#PB49), [72](#678928_1_En_4_Chapter.xhtml#PB72), [76](#678928_1_En_4_Chapter.xhtml#PB76)
*   Completion[29–31](#678928_1_En_2_Chapter.xhtml#PB29)
*   Compound[27](#678928_1_En_2_Chapter.xhtml#PB27), [29](#678928_1_En_2_Chapter.xhtml#PB29)
*   Configurable[179](#Backmatter_001.xhtml#PB179)
*   Constructor[41](#678928_1_En_3_Chapter.xhtml#PB41), [48](#678928_1_En_3_Chapter.xhtml#PB48), [52](#678928_1_En_3_Chapter.xhtml#PB52), [76](#678928_1_En_4_Chapter.xhtml#PB76)
*   Core Data[64](#678928_1_En_4_Chapter.xhtml#PB64), [79](#678928_1_En_4_Chapter.xhtml#PB79), [81](#678928_1_En_4_Chapter.xhtml#PB81), [82](#678928_1_En_4_Chapter.xhtml#PB82), [90](#678928_1_En_4_Chapter.xhtml#PB90), [140](#678928_1_En_8_Chapter.xhtml#PB140), [143](#678928_1_En_8_Chapter.xhtml#PB143), [144](#678928_1_En_8_Chapter.xhtml#PB144)

## D

*   Declarative[41](#678928_1_En_3_Chapter.xhtml#PB41)
*   Decodable[46–48](#678928_1_En_3_Chapter.xhtml#PB46), [72–75](#678928_1_En_4_Chapter.xhtml#PB72)
*   Decoupled[119](#678928_1_En_7_Chapter.xhtml#PB119)
*   Decoupling[1](#678928_1_En_1_Chapter.xhtml#PB1), [21](#678928_1_En_2_Chapter.xhtml#PB21), [48](#678928_1_En_3_Chapter.xhtml#PB48), [68](#678928_1_En_4_Chapter.xhtml#PB68), [76](#678928_1_En_4_Chapter.xhtml#PB76)
*   Dependencies[1](#678928_1_En_1_Chapter.xhtml#PB1), [41](#678928_1_En_3_Chapter.xhtml#PB41), [48](#678928_1_En_3_Chapter.xhtml#PB48), [76](#678928_1_En_4_Chapter.xhtml#PB76), [117](#678928_1_En_7_Chapter.xhtml#PB117)
*   Dependency Injection[41](#678928_1_En_3_Chapter.xhtml#PB41), [48](#678928_1_En_3_Chapter.xhtml#PB48), [49](#678928_1_En_3_Chapter.xhtml#PB49), [64](#678928_1_En_4_Chapter.xhtml#PB64), [68](#678928_1_En_4_Chapter.xhtml#PB68), [74](#678928_1_En_4_Chapter.xhtml#PB74), [76](#678928_1_En_4_Chapter.xhtml#PB76)
*   Dequeues[25](#678928_1_En_2_Chapter.xhtml#PB25)
*   Descriptor[72](#678928_1_En_4_Chapter.xhtml#PB72)
*   Dictionary[12](#678928_1_En_1_Chapter.xhtml#PB12), [79](#678928_1_En_4_Chapter.xhtml#PB79), [125](#678928_1_En_7_Chapter.xhtml#PB125), [136](#678928_1_En_8_Chapter.xhtml#PB136), [139](#678928_1_En_8_Chapter.xhtml#PB139)
*   didSet[27](#678928_1_En_2_Chapter.xhtml#PB27)
*   Downstream[48](#678928_1_En_3_Chapter.xhtml#PB48), [74](#678928_1_En_4_Chapter.xhtml#PB74)
*   Dynamic[119](#678928_1_En_7_Chapter.xhtml#PB119)

## E

*   Enabled[95](#678928_1_En_5_Chapter.xhtml#PB95), [96](#678928_1_En_5_Chapter.xhtml#PB96), [111](#678928_1_En_6_Chapter.xhtml#PB111), [144](#678928_1_En_8_Chapter.xhtml#PB144)
*   Endpoint[41](#678928_1_En_3_Chapter.xhtml#PB41), [45](#678928_1_En_3_Chapter.xhtml#PB45), [46](#678928_1_En_3_Chapter.xhtml#PB46), [48](#678928_1_En_3_Chapter.xhtml#PB48), [49](#678928_1_En_3_Chapter.xhtml#PB49), [52](#678928_1_En_3_Chapter.xhtml#PB52), [68](#678928_1_En_4_Chapter.xhtml#PB68), [69](#678928_1_En_4_Chapter.xhtml#PB69), [76–78](#678928_1_En_4_Chapter.xhtml#PB76)
*   enum[45](#678928_1_En_3_Chapter.xhtml#PB45), [46](#678928_1_En_3_Chapter.xhtml#PB46), [69](#678928_1_En_4_Chapter.xhtml#PB69), [75](#678928_1_En_4_Chapter.xhtml#PB75)
*   eraseToAnyPublisher[47](#678928_1_En_3_Chapter.xhtml#PB47), [48](#678928_1_En_3_Chapter.xhtml#PB48), [73](#678928_1_En_4_Chapter.xhtml#PB73), [74](#678928_1_En_4_Chapter.xhtml#PB74)

## F

*   Fetch[25](#678928_1_En_2_Chapter.xhtml#PB25), [27](#678928_1_En_2_Chapter.xhtml#PB27), [28](#678928_1_En_2_Chapter.xhtml#PB28), [30–32](#678928_1_En_2_Chapter.xhtml#PB30), [46–49](#678928_1_En_3_Chapter.xhtml#PB46), [53](#678928_1_En_3_Chapter.xhtml#PB53), [72–77](#678928_1_En_4_Chapter.xhtml#PB72), [81](#678928_1_En_4_Chapter.xhtml#PB81), [140](#678928_1_En_8_Chapter.xhtml#PB140)
*   Fetched[44](#678928_1_En_3_Chapter.xhtml#PB44), [64](#678928_1_En_4_Chapter.xhtml#PB64)
*   flatMap[47](#678928_1_En_3_Chapter.xhtml#PB47), [48](#678928_1_En_3_Chapter.xhtml#PB48), [73](#678928_1_En_4_Chapter.xhtml#PB73), [74](#678928_1_En_4_Chapter.xhtml#PB74)
*   Font[80](#678928_1_En_4_Chapter.xhtml#PB80)
*   Frameworks, Libraries, and Embedded Content[16](#678928_1_En_1_Chapter.xhtml#PB16), [118](#678928_1_En_7_Chapter.xhtml#PB118), [130](#678928_1_En_8_Chapter.xhtml#PB130)

## G

*   Generics[47](#678928_1_En_3_Chapter.xhtml#PB47), [72](#678928_1_En_4_Chapter.xhtml#PB72)
*   GitHub[8](#678928_1_En_1_Chapter.xhtml#PB8), [22](#678928_1_En_2_Chapter.xhtml#PB22), [100](#678928_1_En_5_Chapter.xhtml#PB100)

## H

*   Handler[29](#678928_1_En_2_Chapter.xhtml#PB29)
*   Hashable[75](#678928_1_En_4_Chapter.xhtml#PB75)
*   Header[80](#678928_1_En_4_Chapter.xhtml#PB80), [122](#678928_1_En_7_Chapter.xhtml#PB122), [123](#678928_1_En_7_Chapter.xhtml#PB123), [134](#678928_1_En_8_Chapter.xhtml#PB134), [137](#678928_1_En_8_Chapter.xhtml#PB137)
*   Host[72](#678928_1_En_4_Chapter.xhtml#PB72)

## I

*   IAP[68](#678928_1_En_4_Chapter.xhtml#PB68), [69](#678928_1_En_4_Chapter.xhtml#PB69), [72](#678928_1_En_4_Chapter.xhtml#PB72), [74–77](#678928_1_En_4_Chapter.xhtml#PB74), [79](#678928_1_En_4_Chapter.xhtml#PB79), [142–144](#678928_1_En_8_Chapter.xhtml#PB142)
*   IBAction[106](#678928_1_En_6_Chapter.xhtml#PB106)
*   @IBOutlet[96](#678928_1_En_5_Chapter.xhtml#PB96), [100](#678928_1_En_5_Chapter.xhtml#PB100), [106](#678928_1_En_6_Chapter.xhtml#PB106)
*   Identifiable[75](#678928_1_En_4_Chapter.xhtml#PB75)
*   Image[95](#678928_1_En_5_Chapter.xhtml#PB95), [96](#678928_1_En_5_Chapter.xhtml#PB96), 98–100, 121, [132](#678928_1_En_8_Chapter.xhtml#PB132), 133
*   Implementation[1](#678928_1_En_1_Chapter.xhtml#PB1), [11](#678928_1_En_1_Chapter.xhtml#PB11), [41](#678928_1_En_3_Chapter.xhtml#PB41), [46](#678928_1_En_3_Chapter.xhtml#PB46), [47](#678928_1_En_3_Chapter.xhtml#PB47), [74](#678928_1_En_4_Chapter.xhtml#PB74), [123](#678928_1_En_7_Chapter.xhtml#PB123), [135](#678928_1_En_8_Chapter.xhtml#PB135), [138](#678928_1_En_8_Chapter.xhtml#PB138)
*   Imported[46](#678928_1_En_3_Chapter.xhtml#PB46), [72](#678928_1_En_4_Chapter.xhtml#PB72), [131](#678928_1_En_8_Chapter.xhtml#PB131)
*   In-App Purchases[50](#678928_1_En_3_Chapter.xhtml#PB50), [63](#678928_1_En_4_Chapter.xhtml#PB63), [64](#678928_1_En_4_Chapter.xhtml#PB64), [68](#678928_1_En_4_Chapter.xhtml#PB68), [71](#678928_1_En_4_Chapter.xhtml#PB71), [75](#678928_1_En_4_Chapter.xhtml#PB75), [79–81](#678928_1_En_4_Chapter.xhtml#PB79), [90](#678928_1_En_4_Chapter.xhtml#PB90), [95](#678928_1_En_5_Chapter.xhtml#PB95), [96](#678928_1_En_5_Chapter.xhtml#PB96), [105](#678928_1_En_6_Chapter.xhtml#PB105), [140](#678928_1_En_8_Chapter.xhtml#PB140), [142](#678928_1_En_8_Chapter.xhtml#PB142)
*   Injected[46](#678928_1_En_3_Chapter.xhtml#PB46), [48](#678928_1_En_3_Chapter.xhtml#PB48), [49](#678928_1_En_3_Chapter.xhtml#PB49), [76](#678928_1_En_4_Chapter.xhtml#PB76)
*   Inspectors[15](#678928_1_En_1_Chapter.xhtml#PB15), [115](#678928_1_En_7_Chapter.xhtml#PB115), [128](#678928_1_En_8_Chapter.xhtml#PB128)
*   Instances[29](#678928_1_En_2_Chapter.xhtml#PB29), [46](#678928_1_En_3_Chapter.xhtml#PB46), [47](#678928_1_En_3_Chapter.xhtml#PB47), [71](#678928_1_En_4_Chapter.xhtml#PB71), [74](#678928_1_En_4_Chapter.xhtml#PB74)
*   Instantiated[29](#678928_1_En_2_Chapter.xhtml#PB29), [120](#678928_1_En_7_Chapter.xhtml#PB120), [131](#678928_1_En_8_Chapter.xhtml#PB131)
*   Interface Builder[7](#678928_1_En_1_Chapter.xhtml#PB7), [8](#678928_1_En_1_Chapter.xhtml#PB8), [14](#678928_1_En_1_Chapter.xhtml#PB14), [15](#678928_1_En_1_Chapter.xhtml#PB15), [22](#678928_1_En_2_Chapter.xhtml#PB22), [78](#678928_1_En_4_Chapter.xhtml#PB78), [96](#678928_1_En_5_Chapter.xhtml#PB96), [100](#678928_1_En_5_Chapter.xhtml#PB100), [106](#678928_1_En_6_Chapter.xhtml#PB106), [115](#678928_1_En_7_Chapter.xhtml#PB115), [128](#678928_1_En_8_Chapter.xhtml#PB128)
*   Internationalization[147](#678928_1_En_9_Chapter.xhtml#PB147)
*   iOS Frameworks[1](#678928_1_En_1_Chapter.xhtml#PB1), [7](#678928_1_En_1_Chapter.xhtml#PB7), [119](#678928_1_En_7_Chapter.xhtml#PB119), [131](#678928_1_En_8_Chapter.xhtml#PB131)

## J, K

*   JSON[44](#678928_1_En_3_Chapter.xhtml#PB44), [68](#678928_1_En_4_Chapter.xhtml#PB68), [72](#678928_1_En_4_Chapter.xhtml#PB72)

## L

*   Lifecycle[122](#678928_1_En_7_Chapter.xhtml#PB122)
*   List[33](#678928_1_En_2_Chapter.xhtml#PB33), [42](#678928_1_En_3_Chapter.xhtml#PB42), [50](#678928_1_En_3_Chapter.xhtml#PB50), [75](#678928_1_En_4_Chapter.xhtml#PB75), [79](#678928_1_En_4_Chapter.xhtml#PB79), [80](#678928_1_En_4_Chapter.xhtml#PB80), [105](#678928_1_En_6_Chapter.xhtml#PB105), [116](#678928_1_En_7_Chapter.xhtml#PB116), [128](#678928_1_En_8_Chapter.xhtml#PB128)
*   Load[13](#678928_1_En_1_Chapter.xhtml#PB13), [119](#678928_1_En_7_Chapter.xhtml#PB119), [122](#678928_1_En_7_Chapter.xhtml#PB122), [123](#678928_1_En_7_Chapter.xhtml#PB123), [131](#678928_1_En_8_Chapter.xhtml#PB131), [134](#678928_1_En_8_Chapter.xhtml#PB134), [135](#678928_1_En_8_Chapter.xhtml#PB135), [138](#678928_1_En_8_Chapter.xhtml#PB138)
*   Loaded[50](#678928_1_En_3_Chapter.xhtml#PB50), [79](#678928_1_En_4_Chapter.xhtml#PB79), [81](#678928_1_En_4_Chapter.xhtml#PB81), [119](#678928_1_En_7_Chapter.xhtml#PB119), [120](#678928_1_En_7_Chapter.xhtml#PB120), [122](#678928_1_En_7_Chapter.xhtml#PB122), 133
*   Locale[152](#678928_1_En_9_Chapter.xhtml#PB152)
*   Localizable[179](#Backmatter_001.xhtml#PB179)
*   Localization[147](#678928_1_En_9_Chapter.xhtml#PB147)

## M

*   mapError[47](#678928_1_En_3_Chapter.xhtml#PB47), [48](#678928_1_En_3_Chapter.xhtml#PB48), [73](#678928_1_En_4_Chapter.xhtml#PB73), [74](#678928_1_En_4_Chapter.xhtml#PB74)
*   Master List-Detail[33](#678928_1_En_2_Chapter.xhtml#PB33)
*   Metadata[26](#678928_1_En_2_Chapter.xhtml#PB26), [28](#678928_1_En_2_Chapter.xhtml#PB28), [29](#678928_1_En_2_Chapter.xhtml#PB29), [49](#678928_1_En_3_Chapter.xhtml#PB49), [74](#678928_1_En_4_Chapter.xhtml#PB74), [76](#678928_1_En_4_Chapter.xhtml#PB76)
*   Models[21](#678928_1_En_2_Chapter.xhtml#PB21), [26–29](#678928_1_En_2_Chapter.xhtml#PB26), [37](#678928_1_En_2_Chapter.xhtml#PB37), [48](#678928_1_En_3_Chapter.xhtml#PB48), [74](#678928_1_En_4_Chapter.xhtml#PB74)
*   Model-View-ViewModel (MVVM)[21](#678928_1_En_2_Chapter.xhtml#PB21), [25](#678928_1_En_2_Chapter.xhtml#PB25), [41](#678928_1_En_3_Chapter.xhtml#PB41), [50](#678928_1_En_3_Chapter.xhtml#PB50), [77](#678928_1_En_4_Chapter.xhtml#PB77)

## N

*   Network[41](#678928_1_En_3_Chapter.xhtml#PB41), [44](#678928_1_En_3_Chapter.xhtml#PB44), [46](#678928_1_En_3_Chapter.xhtml#PB46), [47](#678928_1_En_3_Chapter.xhtml#PB47), [68–70](#678928_1_En_4_Chapter.xhtml#PB68), [72](#678928_1_En_4_Chapter.xhtml#PB72), [73](#678928_1_En_4_Chapter.xhtml#PB73), [114](#678928_1_En_7_Chapter.xhtml#PB114)
*   NSLocalizedString[148](#678928_1_En_9_Chapter.xhtml#PB148)
*   NSObject[122–124](#678928_1_En_7_Chapter.xhtml#PB122), [134](#678928_1_En_8_Chapter.xhtml#PB134), [136](#678928_1_En_8_Chapter.xhtml#PB136), [137](#678928_1_En_8_Chapter.xhtml#PB137), [139](#678928_1_En_8_Chapter.xhtml#PB139), [140](#678928_1_En_8_Chapter.xhtml#PB140)

## O

*   Objective-C[122](#678928_1_En_7_Chapter.xhtml#PB122), [123](#678928_1_En_7_Chapter.xhtml#PB123), [134](#678928_1_En_8_Chapter.xhtml#PB134), [135](#678928_1_En_8_Chapter.xhtml#PB135), [137](#678928_1_En_8_Chapter.xhtml#PB137), [138](#678928_1_En_8_Chapter.xhtml#PB138)
*   ObservableObject[48](#678928_1_En_3_Chapter.xhtml#PB48), [49](#678928_1_En_3_Chapter.xhtml#PB49), [76](#678928_1_En_4_Chapter.xhtml#PB76)
*   OHHTTPStubs[44](#678928_1_En_3_Chapter.xhtml#PB44), [68](#678928_1_En_4_Chapter.xhtml#PB68), [70](#678928_1_En_4_Chapter.xhtml#PB70), [72](#678928_1_En_4_Chapter.xhtml#PB72)
*   Operation[41](#678928_1_En_3_Chapter.xhtml#PB41), [42](#678928_1_En_3_Chapter.xhtml#PB42), [44](#678928_1_En_3_Chapter.xhtml#PB44), [50](#678928_1_En_3_Chapter.xhtml#PB50), [94](#678928_1_En_5_Chapter.xhtml#PB94), [116](#678928_1_En_7_Chapter.xhtml#PB116), [128](#678928_1_En_8_Chapter.xhtml#PB128)
*   Organization Identifier[3](#678928_1_En_1_Chapter.xhtml#PB3)

## P, Q

*   p-list[42](#678928_1_En_3_Chapter.xhtml#PB42), 121, 133
*   Polymorphism[1](#678928_1_En_1_Chapter.xhtml#PB1)
*   PreviewProvider[77](#678928_1_En_4_Chapter.xhtml#PB77)
*   Product Name[114](#678928_1_En_7_Chapter.xhtml#PB114)
*   Project Navigator[5–9](#678928_1_En_1_Chapter.xhtml#PB5), [11](#678928_1_En_1_Chapter.xhtml#PB11), [13](#678928_1_En_1_Chapter.xhtml#PB13), [14](#678928_1_En_1_Chapter.xhtml#PB14), [16](#678928_1_En_1_Chapter.xhtml#PB16), [22](#678928_1_En_2_Chapter.xhtml#PB22), [23](#678928_1_En_2_Chapter.xhtml#PB23), [25](#678928_1_En_2_Chapter.xhtml#PB25), [26](#678928_1_En_2_Chapter.xhtml#PB26), [28](#678928_1_En_2_Chapter.xhtml#PB28), [33](#678928_1_En_2_Chapter.xhtml#PB33), [42](#678928_1_En_3_Chapter.xhtml#PB42), [43](#678928_1_En_3_Chapter.xhtml#PB43), [65](#678928_1_En_4_Chapter.xhtml#PB65), [70](#678928_1_En_4_Chapter.xhtml#PB70), [78](#678928_1_En_4_Chapter.xhtml#PB78), [94](#678928_1_En_5_Chapter.xhtml#PB94), [95](#678928_1_En_5_Chapter.xhtml#PB95), [99–101](#678928_1_En_5_Chapter.xhtml#PB99), [107](#678928_1_En_6_Chapter.xhtml#PB107), [115](#678928_1_En_7_Chapter.xhtml#PB115), [116](#678928_1_En_7_Chapter.xhtml#PB116), [118](#678928_1_En_7_Chapter.xhtml#PB118), [122–124](#678928_1_En_7_Chapter.xhtml#PB122), [128–131](#678928_1_En_8_Chapter.xhtml#PB128), [134–139](#678928_1_En_8_Chapter.xhtml#PB134), [149–153](#678928_1_En_9_Chapter.xhtml#PB149), [158](#678928_1_En_10_Chapter.xhtml#PB158), 159
*   Property List[41–43](#678928_1_En_3_Chapter.xhtml#PB41), [50](#678928_1_En_3_Chapter.xhtml#PB50), [94–96](#678928_1_En_5_Chapter.xhtml#PB94), [105–107](#678928_1_En_6_Chapter.xhtml#PB105), [116](#678928_1_En_7_Chapter.xhtml#PB116), [120](#678928_1_En_7_Chapter.xhtml#PB120), [128](#678928_1_En_8_Chapter.xhtml#PB128), [131](#678928_1_En_8_Chapter.xhtml#PB131), [158](#678928_1_En_10_Chapter.xhtml#PB158)
*   Protocols[1](#678928_1_En_1_Chapter.xhtml#PB1)
*   Proxy[122](#678928_1_En_7_Chapter.xhtml#PB122)
*   @Published[49](#678928_1_En_3_Chapter.xhtml#PB49), [76](#678928_1_En_4_Chapter.xhtml#PB76)
*   Publisher[47](#678928_1_En_3_Chapter.xhtml#PB47), [74](#678928_1_En_4_Chapter.xhtml#PB74)

## R

*   Receipts[64](#678928_1_En_4_Chapter.xhtml#PB64)
*   Registration[119](#678928_1_En_7_Chapter.xhtml#PB119), [120](#678928_1_En_7_Chapter.xhtml#PB120), [122–124](#678928_1_En_7_Chapter.xhtml#PB122), [131](#678928_1_En_8_Chapter.xhtml#PB131), [135](#678928_1_En_8_Chapter.xhtml#PB135), [136](#678928_1_En_8_Chapter.xhtml#PB136), [138](#678928_1_En_8_Chapter.xhtml#PB138), [139](#678928_1_En_8_Chapter.xhtml#PB139)
*   Request[44](#678928_1_En_3_Chapter.xhtml#PB44), [46](#678928_1_En_3_Chapter.xhtml#PB46), [47](#678928_1_En_3_Chapter.xhtml#PB47), [68–70](#678928_1_En_4_Chapter.xhtml#PB68), [72](#678928_1_En_4_Chapter.xhtml#PB72), [81](#678928_1_En_4_Chapter.xhtml#PB81), [114](#678928_1_En_7_Chapter.xhtml#PB114)
*   Reusable[25](#678928_1_En_2_Chapter.xhtml#PB25), [47](#678928_1_En_3_Chapter.xhtml#PB47), [72](#678928_1_En_4_Chapter.xhtml#PB72), [179](#Backmatter_001.xhtml#PB179)
*   Revenue[63](#678928_1_En_4_Chapter.xhtml#PB63), [105](#678928_1_En_6_Chapter.xhtml#PB105)

## S

*   SceneDelegate[17](#678928_1_En_1_Chapter.xhtml#PB17), [39](#678928_1_En_2_Chapter.xhtml#PB39), [102](#678928_1_En_5_Chapter.xhtml#PB102), [119](#678928_1_En_7_Chapter.xhtml#PB119), [120](#678928_1_En_7_Chapter.xhtml#PB120), [122](#678928_1_En_7_Chapter.xhtml#PB122), [131](#678928_1_En_8_Chapter.xhtml#PB131), [132](#678928_1_En_8_Chapter.xhtml#PB132)
*   Scheme[18](#678928_1_En_1_Chapter.xhtml#PB18), [39](#678928_1_En_2_Chapter.xhtml#PB39), [61](#678928_1_En_3_Chapter.xhtml#PB61), [89](#678928_1_En_4_Chapter.xhtml#PB89), [103](#678928_1_En_5_Chapter.xhtml#PB103), [111](#678928_1_En_6_Chapter.xhtml#PB111), [125](#678928_1_En_7_Chapter.xhtml#PB125), [141](#678928_1_En_8_Chapter.xhtml#PB141), [153](#678928_1_En_9_Chapter.xhtml#PB153), [155](#678928_1_En_9_Chapter.xhtml#PB155)
*   Service[21](#678928_1_En_2_Chapter.xhtml#PB21), [28](#678928_1_En_2_Chapter.xhtml#PB28), [29](#678928_1_En_2_Chapter.xhtml#PB29), [32](#678928_1_En_2_Chapter.xhtml#PB32), [41](#678928_1_En_3_Chapter.xhtml#PB41), [44](#678928_1_En_3_Chapter.xhtml#PB44), [46–49](#678928_1_En_3_Chapter.xhtml#PB46), [52](#678928_1_En_3_Chapter.xhtml#PB52), [53](#678928_1_En_3_Chapter.xhtml#PB53), [68](#678928_1_En_4_Chapter.xhtml#PB68), [69](#678928_1_En_4_Chapter.xhtml#PB69), [71](#678928_1_En_4_Chapter.xhtml#PB71), [72](#678928_1_En_4_Chapter.xhtml#PB72), [74](#678928_1_En_4_Chapter.xhtml#PB74), [76–78](#678928_1_En_4_Chapter.xhtml#PB76), [81](#678928_1_En_4_Chapter.xhtml#PB81)
*   Settings[5](#678928_1_En_1_Chapter.xhtml#PB5), [12](#678928_1_En_1_Chapter.xhtml#PB12), [155](#678928_1_En_9_Chapter.xhtml#PB155)
*   Signing[3](#678928_1_En_1_Chapter.xhtml#PB3), [5](#678928_1_En_1_Chapter.xhtml#PB5)
*   Signing & Capabilities[5](#678928_1_En_1_Chapter.xhtml#PB5)
*   Simulators[18](#678928_1_En_1_Chapter.xhtml#PB18), [39](#678928_1_En_2_Chapter.xhtml#PB39), [61](#678928_1_En_3_Chapter.xhtml#PB61), [89](#678928_1_En_4_Chapter.xhtml#PB89), [103](#678928_1_En_5_Chapter.xhtml#PB103), [111](#678928_1_En_6_Chapter.xhtml#PB111), [125](#678928_1_En_7_Chapter.xhtml#PB125), [141](#678928_1_En_8_Chapter.xhtml#PB141)
*   Singleton[29](#678928_1_En_2_Chapter.xhtml#PB29), [46](#678928_1_En_3_Chapter.xhtml#PB46), [71](#678928_1_En_4_Chapter.xhtml#PB71)
*   Stack[100](#678928_1_En_5_Chapter.xhtml#PB100)
*   Standalone[95](#678928_1_En_5_Chapter.xhtml#PB95)
*   @State[79](#678928_1_En_4_Chapter.xhtml#PB79)
*   StateObject[79](#678928_1_En_4_Chapter.xhtml#PB79)
*   Store[49](#678928_1_En_3_Chapter.xhtml#PB49), [50](#678928_1_En_3_Chapter.xhtml#PB50), [76](#678928_1_En_4_Chapter.xhtml#PB76), [77](#678928_1_En_4_Chapter.xhtml#PB77)
*   StoreKit[63](#678928_1_En_4_Chapter.xhtml#PB63), [70](#678928_1_En_4_Chapter.xhtml#PB70), [80](#678928_1_En_4_Chapter.xhtml#PB80)
*   .storyboard[8](#678928_1_En_1_Chapter.xhtml#PB8), [22](#678928_1_En_2_Chapter.xhtml#PB22)
*   Storyboard[7–9](#678928_1_En_1_Chapter.xhtml#PB7), [13–15](#678928_1_En_1_Chapter.xhtml#PB13), [22](#678928_1_En_2_Chapter.xhtml#PB22), [77](#678928_1_En_4_Chapter.xhtml#PB77), [78](#678928_1_En_4_Chapter.xhtml#PB78), [100](#678928_1_En_5_Chapter.xhtml#PB100), [115](#678928_1_En_7_Chapter.xhtml#PB115), [119](#678928_1_En_7_Chapter.xhtml#PB119), [128](#678928_1_En_8_Chapter.xhtml#PB128), [131](#678928_1_En_8_Chapter.xhtml#PB131)
*   struct[26](#678928_1_En_2_Chapter.xhtml#PB26), [75](#678928_1_En_4_Chapter.xhtml#PB75)
*   Subscriber[47](#678928_1_En_3_Chapter.xhtml#PB47), [74](#678928_1_En_4_Chapter.xhtml#PB74)
*   Swift[1](#678928_1_En_1_Chapter.xhtml#PB1), [4](#678928_1_En_1_Chapter.xhtml#PB4), [7](#678928_1_En_1_Chapter.xhtml#PB7), [9](#678928_1_En_1_Chapter.xhtml#PB9), [11](#678928_1_En_1_Chapter.xhtml#PB11), [23](#678928_1_En_2_Chapter.xhtml#PB23), [25](#678928_1_En_2_Chapter.xhtml#PB25), [26](#678928_1_En_2_Chapter.xhtml#PB26), [28](#678928_1_En_2_Chapter.xhtml#PB28), [34](#678928_1_En_2_Chapter.xhtml#PB34), [38](#678928_1_En_2_Chapter.xhtml#PB38), [41](#678928_1_En_3_Chapter.xhtml#PB41), [47](#678928_1_En_3_Chapter.xhtml#PB47), [70](#678928_1_En_4_Chapter.xhtml#PB70), [72](#678928_1_En_4_Chapter.xhtml#PB72), [87](#678928_1_En_4_Chapter.xhtml#PB87), [96](#678928_1_En_5_Chapter.xhtml#PB96), [101](#678928_1_En_5_Chapter.xhtml#PB101), [107](#678928_1_En_6_Chapter.xhtml#PB107), [122–124](#678928_1_En_7_Chapter.xhtml#PB122), [134–136](#678928_1_En_8_Chapter.xhtml#PB134), [138](#678928_1_En_8_Chapter.xhtml#PB138), [139](#678928_1_En_8_Chapter.xhtml#PB139), [177](#678928_1_En_11_Chapter.xhtml#PB177)
*   Swift Package Manager[70](#678928_1_En_4_Chapter.xhtml#PB70), [177](#678928_1_En_11_Chapter.xhtml#PB177)
*   SwiftUI[7](#678928_1_En_1_Chapter.xhtml#PB7), [75](#678928_1_En_4_Chapter.xhtml#PB75), [77–79](#678928_1_En_4_Chapter.xhtml#PB77), [155](#678928_1_En_9_Chapter.xhtml#PB155)

## T

*   Tab Bar Controller[14](#678928_1_En_1_Chapter.xhtml#PB14), [15](#678928_1_En_1_Chapter.xhtml#PB15), [115](#678928_1_En_7_Chapter.xhtml#PB115), [128](#678928_1_En_8_Chapter.xhtml#PB128)
*   Targets[5](#678928_1_En_1_Chapter.xhtml#PB5), [12–14](#678928_1_En_1_Chapter.xhtml#PB12), [16](#678928_1_En_1_Chapter.xhtml#PB16), [101](#678928_1_En_5_Chapter.xhtml#PB101), [110](#678928_1_En_6_Chapter.xhtml#PB110), [113](#678928_1_En_7_Chapter.xhtml#PB113), [118](#678928_1_En_7_Chapter.xhtml#PB118), [130](#678928_1_En_8_Chapter.xhtml#PB130)
*   Team[3](#678928_1_En_1_Chapter.xhtml#PB3), [5](#678928_1_En_1_Chapter.xhtml#PB5)
*   Template[2](#678928_1_En_1_Chapter.xhtml#PB2), [5–7](#678928_1_En_1_Chapter.xhtml#PB5), [9](#678928_1_En_1_Chapter.xhtml#PB9), [11](#678928_1_En_1_Chapter.xhtml#PB11), [13](#678928_1_En_1_Chapter.xhtml#PB13), [23](#678928_1_En_2_Chapter.xhtml#PB23), [25](#678928_1_En_2_Chapter.xhtml#PB25), [26](#678928_1_En_2_Chapter.xhtml#PB26), [28](#678928_1_En_2_Chapter.xhtml#PB28), [33](#678928_1_En_2_Chapter.xhtml#PB33), [42](#678928_1_En_3_Chapter.xhtml#PB42), [65](#678928_1_En_4_Chapter.xhtml#PB65), [77](#678928_1_En_4_Chapter.xhtml#PB77), [78](#678928_1_En_4_Chapter.xhtml#PB78), [94](#678928_1_En_5_Chapter.xhtml#PB94), [95](#678928_1_En_5_Chapter.xhtml#PB95), [99](#678928_1_En_5_Chapter.xhtml#PB99), [101](#678928_1_En_5_Chapter.xhtml#PB101), [107](#678928_1_En_6_Chapter.xhtml#PB107), [114](#678928_1_En_7_Chapter.xhtml#PB114), [116](#678928_1_En_7_Chapter.xhtml#PB116), [122–124](#678928_1_En_7_Chapter.xhtml#PB122), [127](#678928_1_En_8_Chapter.xhtml#PB127), [128](#678928_1_En_8_Chapter.xhtml#PB128), [134–139](#678928_1_En_8_Chapter.xhtml#PB134), [149](#678928_1_En_9_Chapter.xhtml#PB149), [152](#678928_1_En_9_Chapter.xhtml#PB152), [158](#678928_1_En_10_Chapter.xhtml#PB158)
*   Testing[53](#678928_1_En_3_Chapter.xhtml#PB53)
*   Text[80](#678928_1_En_4_Chapter.xhtml#PB80), [81](#678928_1_En_4_Chapter.xhtml#PB81)
*   Thread[53](#678928_1_En_3_Chapter.xhtml#PB53)
*   Transactions[64](#678928_1_En_4_Chapter.xhtml#PB64), [81](#678928_1_En_4_Chapter.xhtml#PB81)
*   Treatment[30](#678928_1_En_2_Chapter.xhtml#PB30), [48](#678928_1_En_3_Chapter.xhtml#PB48), [74](#678928_1_En_4_Chapter.xhtml#PB74), [75](#678928_1_En_4_Chapter.xhtml#PB75), [124](#678928_1_En_7_Chapter.xhtml#PB124), [136](#678928_1_En_8_Chapter.xhtml#PB136), [139](#678928_1_En_8_Chapter.xhtml#PB139)
*   Typealias[29](#678928_1_En_2_Chapter.xhtml#PB29), [49](#678928_1_En_3_Chapter.xhtml#PB49), [76](#678928_1_En_4_Chapter.xhtml#PB76)

## U

*   UIAppearance[157](#678928_1_En_10_Chapter.xhtml#PB157), [158](#678928_1_En_10_Chapter.xhtml#PB158), [160](#678928_1_En_10_Chapter.xhtml#PB160)
*   UIBarButtonItem[106](#678928_1_En_6_Chapter.xhtml#PB106)
*   UIHostingController[77](#678928_1_En_4_Chapter.xhtml#PB77), [78](#678928_1_En_4_Chapter.xhtml#PB78)
*   UIKit[12](#678928_1_En_1_Chapter.xhtml#PB12), [34](#678928_1_En_2_Chapter.xhtml#PB34), [77–79](#678928_1_En_4_Chapter.xhtml#PB77), [87](#678928_1_En_4_Chapter.xhtml#PB87), [96](#678928_1_En_5_Chapter.xhtml#PB96), [101](#678928_1_En_5_Chapter.xhtml#PB101)
*   UIStoryboard[12](#678928_1_En_1_Chapter.xhtml#PB12), [38](#678928_1_En_2_Chapter.xhtml#PB38), [101](#678928_1_En_5_Chapter.xhtml#PB101), [106](#678928_1_En_6_Chapter.xhtml#PB106), 121, [132](#678928_1_En_8_Chapter.xhtml#PB132)
*   UITableViewCell[25](#678928_1_En_2_Chapter.xhtml#PB25), [33](#678928_1_En_2_Chapter.xhtml#PB33)
*   UITableViewController[9](#678928_1_En_1_Chapter.xhtml#PB9), [23](#678928_1_En_2_Chapter.xhtml#PB23), [32](#678928_1_En_2_Chapter.xhtml#PB32), [34](#678928_1_En_2_Chapter.xhtml#PB34), [107](#678928_1_En_6_Chapter.xhtml#PB107)
*   Upstream[48](#678928_1_En_3_Chapter.xhtml#PB48), [74](#678928_1_En_4_Chapter.xhtml#PB74)
*   URL[31](#678928_1_En_2_Chapter.xhtml#PB31), [46](#678928_1_En_3_Chapter.xhtml#PB46), [47](#678928_1_En_3_Chapter.xhtml#PB47), [49](#678928_1_En_3_Chapter.xhtml#PB49), [69](#678928_1_En_4_Chapter.xhtml#PB69), [70](#678928_1_En_4_Chapter.xhtml#PB70), [72](#678928_1_En_4_Chapter.xhtml#PB72), [73](#678928_1_En_4_Chapter.xhtml#PB73), [77](#678928_1_En_4_Chapter.xhtml#PB77)
*   UserDefaults[50](#678928_1_En_3_Chapter.xhtml#PB50), [79](#678928_1_En_4_Chapter.xhtml#PB79), [81](#678928_1_En_4_Chapter.xhtml#PB81), [90](#678928_1_En_4_Chapter.xhtml#PB90), [96](#678928_1_En_5_Chapter.xhtml#PB96), 97, [107](#678928_1_En_6_Chapter.xhtml#PB107), [119](#678928_1_En_7_Chapter.xhtml#PB119), [120](#678928_1_En_7_Chapter.xhtml#PB120), [122](#678928_1_En_7_Chapter.xhtml#PB122), [125](#678928_1_En_7_Chapter.xhtml#PB125), [131](#678928_1_En_8_Chapter.xhtml#PB131), [132](#678928_1_En_8_Chapter.xhtml#PB132), [134](#678928_1_En_8_Chapter.xhtml#PB134), [136](#678928_1_En_8_Chapter.xhtml#PB136), [137](#678928_1_En_8_Chapter.xhtml#PB137), [139](#678928_1_En_8_Chapter.xhtml#PB139), [140](#678928_1_En_8_Chapter.xhtml#PB140)
*   User Interface[7–9](#678928_1_En_1_Chapter.xhtml#PB7), [14](#678928_1_En_1_Chapter.xhtml#PB14), [15](#678928_1_En_1_Chapter.xhtml#PB15), [17](#678928_1_En_1_Chapter.xhtml#PB17), [21–23](#678928_1_En_2_Chapter.xhtml#PB21), [30](#678928_1_En_2_Chapter.xhtml#PB30), [32](#678928_1_En_2_Chapter.xhtml#PB32), [33](#678928_1_En_2_Chapter.xhtml#PB33), [53](#678928_1_En_3_Chapter.xhtml#PB53), [77](#678928_1_En_4_Chapter.xhtml#PB77), [78](#678928_1_En_4_Chapter.xhtml#PB78), [93](#678928_1_En_5_Chapter.xhtml#PB93), [95](#678928_1_En_5_Chapter.xhtml#PB95), [96](#678928_1_En_5_Chapter.xhtml#PB96), [100](#678928_1_En_5_Chapter.xhtml#PB100), [106](#678928_1_En_6_Chapter.xhtml#PB106), [107](#678928_1_En_6_Chapter.xhtml#PB107), [111](#678928_1_En_6_Chapter.xhtml#PB111), [115](#678928_1_En_7_Chapter.xhtml#PB115), [119](#678928_1_En_7_Chapter.xhtml#PB119), [122](#678928_1_En_7_Chapter.xhtml#PB122), [128](#678928_1_En_8_Chapter.xhtml#PB128), [131](#678928_1_En_8_Chapter.xhtml#PB131)

## V

*   View[14](#678928_1_En_1_Chapter.xhtml#PB14), [15](#678928_1_En_1_Chapter.xhtml#PB15), [21](#678928_1_En_2_Chapter.xhtml#PB21), [27](#678928_1_En_2_Chapter.xhtml#PB27), [28](#678928_1_En_2_Chapter.xhtml#PB28), [30](#678928_1_En_2_Chapter.xhtml#PB30), [32](#678928_1_En_2_Chapter.xhtml#PB32), [33](#678928_1_En_2_Chapter.xhtml#PB33), [37](#678928_1_En_2_Chapter.xhtml#PB37), [41](#678928_1_En_3_Chapter.xhtml#PB41), [48](#678928_1_En_3_Chapter.xhtml#PB48), [50](#678928_1_En_3_Chapter.xhtml#PB50), [53](#678928_1_En_3_Chapter.xhtml#PB53), [74–80](#678928_1_En_4_Chapter.xhtml#PB74), [115](#678928_1_En_7_Chapter.xhtml#PB115), [128](#678928_1_En_8_Chapter.xhtml#PB128), [148](#678928_1_En_9_Chapter.xhtml#PB148)
*   ViewController[14](#678928_1_En_1_Chapter.xhtml#PB14), [115](#678928_1_En_7_Chapter.xhtml#PB115), [119](#678928_1_En_7_Chapter.xhtml#PB119), [128](#678928_1_En_8_Chapter.xhtml#PB128), [131](#678928_1_En_8_Chapter.xhtml#PB131)
*   ViewModel[26](#678928_1_En_2_Chapter.xhtml#PB26), [27](#678928_1_En_2_Chapter.xhtml#PB27), [30](#678928_1_En_2_Chapter.xhtml#PB30), [32](#678928_1_En_2_Chapter.xhtml#PB32), [33](#678928_1_En_2_Chapter.xhtml#PB33), [41](#678928_1_En_3_Chapter.xhtml#PB41), [48](#678928_1_En_3_Chapter.xhtml#PB48), [50](#678928_1_En_3_Chapter.xhtml#PB50), [52](#678928_1_En_3_Chapter.xhtml#PB52), [53](#678928_1_En_3_Chapter.xhtml#PB53), [64](#678928_1_En_4_Chapter.xhtml#PB64), [68](#678928_1_En_4_Chapter.xhtml#PB68), [72](#678928_1_En_4_Chapter.xhtml#PB72), [74](#678928_1_En_4_Chapter.xhtml#PB74), [76–78](#678928_1_En_4_Chapter.xhtml#PB76), [81](#678928_1_En_4_Chapter.xhtml#PB81)

## W

*   willConnectTo[17](#678928_1_En_1_Chapter.xhtml#PB17), [18](#678928_1_En_1_Chapter.xhtml#PB18), [39](#678928_1_En_2_Chapter.xhtml#PB39), [88](#678928_1_En_4_Chapter.xhtml#PB88), [102](#678928_1_En_5_Chapter.xhtml#PB102), [119](#678928_1_En_7_Chapter.xhtml#PB119), [120](#678928_1_En_7_Chapter.xhtml#PB120), [122](#678928_1_En_7_Chapter.xhtml#PB122), [131](#678928_1_En_8_Chapter.xhtml#PB131), [132](#678928_1_En_8_Chapter.xhtml#PB132)
*   Workspace[117](#678928_1_En_7_Chapter.xhtml#PB117), [118](#678928_1_En_7_Chapter.xhtml#PB118), [129](#678928_1_En_8_Chapter.xhtml#PB129)

## X, Y, Z

*   Xcode[2](#678928_1_En_1_Chapter.xhtml#PB2), [5](#678928_1_En_1_Chapter.xhtml#PB5), [18](#678928_1_En_1_Chapter.xhtml#PB18), [39](#678928_1_En_2_Chapter.xhtml#PB39), [61](#678928_1_En_3_Chapter.xhtml#PB61), [77](#678928_1_En_4_Chapter.xhtml#PB77), [89](#678928_1_En_4_Chapter.xhtml#PB89), [103](#678928_1_En_5_Chapter.xhtml#PB103), [111](#678928_1_En_6_Chapter.xhtml#PB111), [114](#678928_1_En_7_Chapter.xhtml#PB114), [117](#678928_1_En_7_Chapter.xhtml#PB117), [118](#678928_1_En_7_Chapter.xhtml#PB118), [125](#678928_1_En_7_Chapter.xhtml#PB125), [127](#678928_1_En_8_Chapter.xhtml#PB127), [129](#678928_1_En_8_Chapter.xhtml#PB129), [141](#678928_1_En_8_Chapter.xhtml#PB141), [155](#678928_1_En_9_Chapter.xhtml#PB155), [156](#678928_1_En_9_Chapter.xhtml#PB156)
*   .xcodeproj[70](#678928_1_En_4_Chapter.xhtml#PB70)
*   XIB[9](#678928_1_En_1_Chapter.xhtml#PB9), [23](#678928_1_En_2_Chapter.xhtml#PB23), [34](#678928_1_En_2_Chapter.xhtml#PB34), [96](#678928_1_En_5_Chapter.xhtml#PB96), [107](#678928_1_En_6_Chapter.xhtml#PB107)