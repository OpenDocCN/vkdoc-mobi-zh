![](Images/A978-1-4842-3174-6_CoverFigure.jpg)

![A978-1-4842-3174-6_CoverFigure.jpg](Images/A978-1-4842-3174-6_CoverFigure.jpg)Allan Fowler and Philip Chu Learn Unity 2017 for iOS Game DevelopmentCreate Amazing 3D Games for iPhone and iPad2nd ed.

![A310332_2_En_BookFrontmatter_Figa_HTML.png](Images/A310332_2_En_BookFrontmatter_Figa_HTML.png)

Any source code or other supplementary material referenced by the author in this book is available to readers on GitHub via the book's product page, located at [`www.apress.com/978-1-4842-3173-9`](http://www.apress.com/978-1-4842-3173-9) . For more detailed information, please visit [`www.apress.com/source-code`](http://www.apress.com/source-code) . ISBN 978-1-4842-3173-9e-ISBN 978-1-4842-3174-6 [https://doi.org/10.1007/978-1-4842-3174-6](https://doi.org/10.1007/978-1-4842-3174-6) Library of Congress Control Number: 2017959340 © Allan Fowler and Philip Chu 2017 This work is subject to copyright. All rights are reserved by the Publisher, whether the whole or part of the material is concerned, specifically the rights of translation, reprinting, reuse of illustrations, recitation, broadcasting, reproduction on microfilms or in any other physical way, and transmission or information storage and retrieval, electronic adaptation, computer software, or by similar or dissimilar methodology now known or hereafter developed. Trademarked names, logos, and images may appear in this book. Rather than use a trademark symbol with every occurrence of a trademarked name, logo, or image we use the names, logos, and images only in an editorial fashion and to the benefit of the trademark owner, with no intention of infringement of the trademark. The use in this publication of trade names, trademarks, service marks, and similar terms, even if they are not identified as such, is not to be taken as an expression of opinion as to whether or not they are subject to proprietary rights. While the advice and information in this book are believed to be true and accurate at the date of publication, neither the authors nor the editors nor the publisher can accept any legal responsibility for any errors or omissions that may be made. The publisher makes no warranty, express or implied, with respect to the material contained herein. Printed on acid-free paper Distributed to the book trade worldwide by Springer Science+Business Media New York, 233 Spring Street, 6th Floor, New York, NY 10013\. Phone 1-800-SPRINGER, fax (201) 348-4505, e-mail orders-ny@springer-sbm.com, or visit www.springeronline.com. Apress Media, LLC is a California LLC and the sole member (owner) is Springer Science + Business Media Finance Inc (SSBM Finance Inc). SSBM Finance Inc is a Delaware corporation. For Hao, Ciaran, and Annah: kia kaha, kia aroha, kia mana. Acknowledgments

Thank you to the dedicated team at Apress. Your patience and professionalism are admirable.

Contents [Chapter 1:​ Getting Started](../Text/A310332_2_En_1_Chapter.html) 1 [Prerequisites](../Text/A310332_2_En_1_Chapter.html#Sec1) 1 [Prepare Your Mac](../Text/A310332_2_En_1_Chapter.html#Sec2) 1 [Register as an iOS Developer](../Text/A310332_2_En_1_Chapter.html#Sec3) 2 [Download Xcode](../Text/A310332_2_En_1_Chapter.html#Sec4) 2 [Download Unity](../Text/A310332_2_En_1_Chapter.html#Sec5) 2 [Install Unity](../Text/A310332_2_En_1_Chapter.html#Sec6) 3 [Run the Download Assistant](../Text/A310332_2_En_1_Chapter.html#Sec7) 3 [Welcome to Unity!](../Text/A310332_2_En_1_Chapter.html#Sec8) 6 [Manage Unity](../Text/A310332_2_En_1_Chapter.html#Sec9) 7 [Change Skins (Pro)](../Text/A310332_2_En_1_Chapter.html#Sec10) 7 [Report Problems](../Text/A310332_2_En_1_Chapter.html#Sec11) 9 [Check for Updates](../Text/A310332_2_En_1_Chapter.html#Sec12) 12 [Explore Further](../Text/A310332_2_En_1_Chapter.html#Sec13) 12 [iOS Development Requirements](../Text/A310332_2_En_1_Chapter.html#Sec14) 13 [The Unity Web Site](../Text/A310332_2_En_1_Chapter.html#Sec15) 13 [Unity Manuals and References](../Text/A310332_2_En_1_Chapter.html#Sec16) 13 [The Unity Community](../Text/A310332_2_En_1_Chapter.html#Sec17) 14 [Chapter 2:​ A Unity Tour](../Text/A310332_2_En_2_Chapter.html) 15 [Introducing The Climber Game](../Text/A310332_2_En_2_Chapter.html#Sec1) 15 [Open the Climber Project](../Text/A310332_2_En_2_Chapter.html#Sec2) 16 [Open the Climber Game Scene](../Text/A310332_2_En_2_Chapter.html#Sec3) 17 [Play Climber](../Text/A310332_2_En_2_Chapter.html#Sec4) 19 [Building a game](../Text/A310332_2_En_2_Chapter.html#Sec5) 20 [The Editor Layout](../Text/A310332_2_En_2_Chapter.html#Sec6) 24 [Preset Layouts](../Text/A310332_2_En_2_Chapter.html#Sec7) 26 [Custom Layouts](../Text/A310332_2_En_2_Chapter.html#Sec8) 28 [The Inspector View](../Text/A310332_2_En_2_Chapter.html#Sec13) 33 [The Project View](../Text/A310332_2_En_2_Chapter.html#Sec14) 38 [Switch Between One-Column and Two-Columns](../Text/A310332_2_En_2_Chapter.html#Sec15) 39 [Scale Icons](../Text/A310332_2_En_2_Chapter.html#Sec16) 39 [Inspect Assets](../Text/A310332_2_En_2_Chapter.html#Sec17) 39 [Search for Assets](../Text/A310332_2_En_2_Chapter.html#Sec18) 40 [Operate on Assets](../Text/A310332_2_En_2_Chapter.html#Sec19) 42 [The Hierarchy View](../Text/A310332_2_En_2_Chapter.html#Sec20) 42 [Inspect Game Objects](../Text/A310332_2_En_2_Chapter.html#Sec21) 42 [Parent and Child GameObjects](../Text/A310332_2_En_2_Chapter.html#Sec22) 43 [The Scene View](../Text/A310332_2_En_2_Chapter.html#Sec23) 43 [Navigate the Scene](../Text/A310332_2_En_2_Chapter.html#Sec24) 44 [Scene View Options](../Text/A310332_2_En_2_Chapter.html#Sec25) 47 [Scene View Gizmos](../Text/A310332_2_En_2_Chapter.html#Sec26) 47 [The Game View](../Text/A310332_2_En_2_Chapter.html#Sec27) 48 [Maximize on Play](../Text/A310332_2_En_2_Chapter.html#Sec28) 49 [Stats](../Text/A310332_2_En_2_Chapter.html#Sec29) 50 [Game View Gizmos](../Text/A310332_2_En_2_Chapter.html#Sec30) 50 [The Console View](../Text/A310332_2_En_2_Chapter.html#Sec31) 51 [Explore Further](../Text/A310332_2_En_2_Chapter.html#Sec32) 53 [Unity Manual](../Text/A310332_2_En_2_Chapter.html#Sec33) 53 [Tutorials](../Text/A310332_2_En_2_Chapter.html#Sec34) 53 [Version Control](../Text/A310332_2_En_2_Chapter.html#Sec35) 53 [Chapter 3:​ Making a Scene](../Text/A310332_2_En_3_Chapter.html) 55 [Create a New Project](../Text/A310332_2_En_3_Chapter.html#Sec1) 56 [The Main Camera](../Text/A310332_2_En_3_Chapter.html#Sec2) 61 [Multiple Cameras](../Text/A310332_2_En_3_Chapter.html#Sec3) 61 [Anatomy of a Camera](../Text/A310332_2_En_3_Chapter.html#Sec4) 61 [The Transform Component](../Text/A310332_2_En_3_Chapter.html#Sec5) 63 [The Camera Component](../Text/A310332_2_En_3_Chapter.html#Sec6) 63 [FlareLayer Component](../Text/A310332_2_En_3_Chapter.html#Sec15) 66 [GUILayer Component](../Text/A310332_2_En_3_Chapter.html#Sec16) 66 [AudioListener Component](../Text/A310332_2_En_3_Chapter.html#Sec17) 67 [Add a Cube to the Scene](../Text/A310332_2_En_3_Chapter.html#Sec18) 67 [Make the Cube](../Text/A310332_2_En_3_Chapter.html#Sec19) 67 [Frame the Cube](../Text/A310332_2_En_3_Chapter.html#Sec20) 69 [Move the Cube](../Text/A310332_2_En_3_Chapter.html#Sec21) 69 [Anatomy of a Cube](../Text/A310332_2_En_3_Chapter.html#Sec22) 70 [Align With View](../Text/A310332_2_En_3_Chapter.html#Sec27) 72 [Camera Control](../Text/A310332_2_En_3_Chapter.html#Sec28) 73 [Import the Script](../Text/A310332_2_En_3_Chapter.html#Sec29) 73 [Attach the Script](../Text/A310332_2_En_3_Chapter.html#Sec30) 78 [Add a Light](../Text/A310332_2_En_3_Chapter.html#Sec31) 80 [Anatomy of a Light](../Text/A310332_2_En_3_Chapter.html#Sec32) 83 [Adjust the Light](../Text/A310332_2_En_3_Chapter.html#Sec44) 84 [Make a Halo](../Text/A310332_2_En_3_Chapter.html#Sec45) 85 [Textures](../Text/A310332_2_En_3_Chapter.html#Sec46) 87 [Import a Texture](../Text/A310332_2_En_3_Chapter.html#Sec47) 87 [Shop the Asset Store](../Text/A310332_2_En_3_Chapter.html#Sec48) 91 [Import the Texture](../Text/A310332_2_En_3_Chapter.html#Sec49) 94 [Apply the Texture](../Text/A310332_2_En_3_Chapter.html#Sec50) 95 [Explore Further](../Text/A310332_2_En_3_Chapter.html#Sec51) 96 [Unity Manual](../Text/A310332_2_En_3_Chapter.html#Sec52) 96 [Reference Manual](../Text/A310332_2_En_3_Chapter.html#Sec53) 97 [Asset Store](../Text/A310332_2_En_3_Chapter.html#Sec54) 98 [Computer Graphics](../Text/A310332_2_En_3_Chapter.html#Sec55) 98 [Chapter 4:​ Making It Move:​ Scripting the Cube](../Text/A310332_2_En_4_Chapter.html) 99 [Organize the Assets](../Text/A310332_2_En_4_Chapter.html#Sec1) 99 [Create the Script](../Text/A310332_2_En_4_Chapter.html#Sec2) 100 [Name the Script](../Text/A310332_2_En_4_Chapter.html#Sec3) 102 [Anatomy of a Script](../Text/A310332_2_En_4_Chapter.html#Sec4) 103 [Attach the Script](../Text/A310332_2_En_4_Chapter.html#Sec5) 104 [Edit the Script](../Text/A310332_2_En_4_Chapter.html#Sec6) 106 [Understand the Script](../Text/A310332_2_En_4_Chapter.html#Sec7) 108 [Read the Scripting Reference](../Text/A310332_2_En_4_Chapter.html#Sec8) 109 [Run the Script](../Text/A310332_2_En_4_Chapter.html#Sec9) 111 [Debug the Script](../Text/A310332_2_En_4_Chapter.html#Sec10) 112 [Compilation Errors](../Text/A310332_2_En_4_Chapter.html#Sec11) 112 [Runtime Errors](../Text/A310332_2_En_4_Chapter.html#Sec12) 113 [Debug with MonoDevelop](../Text/A310332_2_En_4_Chapter.html#Sec13) 113 [Make It Rotate](../Text/A310332_2_En_4_Chapter.html#Sec14) 119 [Rotate the Transform](../Text/A310332_2_En_4_Chapter.html#Sec15) 119 [Other Ways to Rotate](../Text/A310332_2_En_4_Chapter.html#Sec16) 122 [Rotate in World Space](../Text/A310332_2_En_4_Chapter.html#Sec17) 122 [Children of the Cube](../Text/A310332_2_En_4_Chapter.html#Sec18) 123 [Making Prefabs](../Text/A310332_2_En_4_Chapter.html#Sec19) 123 [Breaking Prefabs](../Text/A310332_2_En_4_Chapter.html#Sec20) 127 [Explore Further](../Text/A310332_2_En_4_Chapter.html#Sec21) 128 [Unity Manual](../Text/A310332_2_En_4_Chapter.html#Sec22) 128 [Scripting Reference](../Text/A310332_2_En_4_Chapter.html#Sec23) 128 [Scripting](../Text/A310332_2_En_4_Chapter.html#Sec25) 129 [Chapter 5:​ Let’s Dance! Animation and Sound](../Text/A310332_2_En_5_Chapter.html) 131 [Import the Skeletons Pack](../Text/A310332_2_En_5_Chapter.html#Sec1) 132 [Add a Skeleton](../Text/A310332_2_En_5_Chapter.html#Sec2) 134 [Hide the Cubes](../Text/A310332_2_En_5_Chapter.html#Sec3) 135 [Orbit the Skeleton](../Text/A310332_2_En_5_Chapter.html#Sec4) 136 [Make the Skeleton Dance](../Text/A310332_2_En_5_Chapter.html#Sec5) 137 [Make the Skeleton Dance Forever](../Text/A310332_2_En_5_Chapter.html#Sec6) 139 [Add a Dance Floor](../Text/A310332_2_En_5_Chapter.html#Sec7) 141 [Add a Shadow](../Text/A310332_2_En_5_Chapter.html#Sec8) 144 [Add Music](../Text/A310332_2_En_5_Chapter.html#Sec9) 149 [Explore Further](../Text/A310332_2_En_5_Chapter.html#Sec10) 153 [Unity Manual](../Text/A310332_2_En_5_Chapter.html#Sec11) 153 [Reference Manual](../Text/A310332_2_En_5_Chapter.html#Sec12) 153 [Asset Store](../Text/A310332_2_En_5_Chapter.html#Sec13) 154 [Computer Graphics](../Text/A310332_2_En_5_Chapter.html#Sec14) 154 [Chapter 6:​ Let’s Roll! Physics and Controls](../Text/A310332_2_En_6_Chapter.html) 155 [Make a New Scene](../Text/A310332_2_En_6_Chapter.html#Sec1) 155 [Delete GameObjects](../Text/A310332_2_En_6_Chapter.html#Sec2) 157 [Adjust the Light](../Text/A310332_2_En_6_Chapter.html#Sec3) 157 [Retile the Floor](../Text/A310332_2_En_6_Chapter.html#Sec4) 158 [Reset the Camera](../Text/A310332_2_En_6_Chapter.html#Sec5) 162 [Make a Ball](../Text/A310332_2_En_6_Chapter.html#Sec6) 163 [Make a Sphere](../Text/A310332_2_En_6_Chapter.html#Sec7) 163 [Make It Fall](../Text/A310332_2_En_6_Chapter.html#Sec8) 166 [Customize the Collision](../Text/A310332_2_En_6_Chapter.html#Sec9) 170 [PhysicMaterials](../Text/A310332_2_En_6_Chapter.html#Sec10) 171 [Standard PhysicMaterials](../Text/A310332_2_En_6_Chapter.html#Sec11) 171 [Anatomy of a PhysicMaterial](../Text/A310332_2_En_6_Chapter.html#Sec12) 172 [Apply the PhysicMaterial](../Text/A310332_2_En_6_Chapter.html#Sec13) 174 [Make a New PhysicMaterial](../Text/A310332_2_En_6_Chapter.html#Sec14) 175 [Make It Roll](../Text/A310332_2_En_6_Chapter.html#Sec15) 177 [Create the Script](../Text/A310332_2_En_6_Chapter.html#Sec16) 177 [Update:​ Gather Input](../Text/A310332_2_En_6_Chapter.html#Sec17) 177 [FixedUpdate:​ Use the Force](../Text/A310332_2_En_6_Chapter.html#Sec18) 179 [Is It Rolling?​](../Text/A310332_2_En_6_Chapter.html#Sec19) 180 [Limit the Speed](../Text/A310332_2_En_6_Chapter.html#Sec20) 183 [The Complete Script](../Text/A310332_2_En_6_Chapter.html#Sec21) 184 [Be the Ball](../Text/A310332_2_En_6_Chapter.html#Sec22) 184 [Explore Further](../Text/A310332_2_En_6_Chapter.html#Sec23) 186 [Unity Manual](../Text/A310332_2_En_6_Chapter.html#Sec24) 187 [Reference Manual](../Text/A310332_2_En_6_Chapter.html#Sec25) 187 [Scripting Reference](../Text/A310332_2_En_6_Chapter.html#Sec26) 187 [On the Web](../Text/A310332_2_En_6_Chapter.html#Sec27) 188 [Chapter 7:​ Let’s Bowl! Advanced Physics](../Text/A310332_2_En_7_Chapter.html) 189 [Lengthen the Lane](../Text/A310332_2_En_7_Chapter.html#Sec1) 189 [Make Some Pins](../Text/A310332_2_En_7_Chapter.html#Sec2) 190 [Place the Pins in the Scene](../Text/A310332_2_En_7_Chapter.html#Sec3) 192 [Use Camera Follow](../Text/A310332_2_En_7_Chapter.html#Sec4) 197 [Keep Playing](../Text/A310332_2_En_7_Chapter.html#Sec5) 198 [Return the Ball](../Text/A310332_2_En_7_Chapter.html#Sec6) 198 [Send a Message](../Text/A310332_2_En_7_Chapter.html#Sec7) 199 [Check for Gutter Ball](../Text/A310332_2_En_7_Chapter.html#Sec8) 200 [The Complete Listing](../Text/A310332_2_En_7_Chapter.html#Sec9) 202 [Bowl for Barrels](../Text/A310332_2_En_7_Chapter.html#Sec10) 203 [Pick a Barrel](../Text/A310332_2_En_7_Chapter.html#Sec11) 203 [Make a Prefab](../Text/A310332_2_En_7_Chapter.html#Sec12) 204 [Use the Prefab](../Text/A310332_2_En_7_Chapter.html#Sec13) 206 [Add a Collider](../Text/A310332_2_En_7_Chapter.html#Sec14) 206 [Add a Compound Collider](../Text/A310332_2_En_7_Chapter.html#Sec15) 208 [Update the Prefab](../Text/A310332_2_En_7_Chapter.html#Sec16) 212 [A Complex Collider (HyperBowl)](../Text/A310332_2_En_7_Chapter.html#Sec17) 214 [Add Sounds](../Text/A310332_2_En_7_Chapter.html#Sec18) 214 [Get Sounds](../Text/A310332_2_En_7_Chapter.html#Sec19) 215 [Add a Rolling Sound](../Text/A310332_2_En_7_Chapter.html#Sec20) 215 [Add a Pin Collision Sound](../Text/A310332_2_En_7_Chapter.html#Sec21) 221 [Explore Further](../Text/A310332_2_En_7_Chapter.html#Sec22) 224 [Scripting Reference](../Text/A310332_2_En_7_Chapter.html#Sec23) 224 [Assets](../Text/A310332_2_En_7_Chapter.html#Sec24) 224 [Chapter 8:​ Let’s Play! Scripting the Game](../Text/A310332_2_En_8_Chapter.html) 225 [The Game Rules](../Text/A310332_2_En_8_Chapter.html#Sec1) 225 [Scoring the Game](../Text/A310332_2_En_8_Chapter.html#Sec2) 226 [The Frame Score](../Text/A310332_2_En_8_Chapter.html#Sec3) 226 [The Player Score](../Text/A310332_2_En_8_Chapter.html#Sec4) 228 [Setting the Score](../Text/A310332_2_En_8_Chapter.html#Sec5) 229 [Getting the Score](../Text/A310332_2_En_8_Chapter.html#Sec6) 232 [The Complete Listing](../Text/A310332_2_En_8_Chapter.html#Sec7) 234 [Creating a FuguBowlPlayer](../Text/A310332_2_En_8_Chapter.html#Sec8) 234 [The Pin Status](../Text/A310332_2_En_8_Chapter.html#Sec9) 235 [The Game Logic](../Text/A310332_2_En_8_Chapter.html#Sec10) 238 [Design the FSM](../Text/A310332_2_En_8_Chapter.html#Sec11) 239 [Tracking the Game](../Text/A310332_2_En_8_Chapter.html#Sec12) 240 [Starting the State Machine](../Text/A310332_2_En_8_Chapter.html#Sec13) 240 [The States](../Text/A310332_2_En_8_Chapter.html#Sec14) 242 [The Complete Listing](../Text/A310332_2_En_8_Chapter.html#Sec26) 250 [Explore Further](../Text/A310332_2_En_8_Chapter.html#Sec27) 251 [Unity Manual](../Text/A310332_2_En_8_Chapter.html#Sec28) 251 [Scripting Reference](../Text/A310332_2_En_8_Chapter.html#Sec29) 251 [On the Web](../Text/A310332_2_En_8_Chapter.html#Sec31) 252 [Chapter 9:​ The Game GUI](../Text/A310332_2_En_9_Chapter.html) 253 [The Scoreboard](../Text/A310332_2_En_9_Chapter.html#Sec1) 253 [Create the Script](../Text/A310332_2_En_9_Chapter.html#Sec2) 253 [Style the GUI](../Text/A310332_2_En_9_Chapter.html#Sec3) 257 [The Pause Menu](../Text/A310332_2_En_9_Chapter.html#Sec4) 259 [Create the Script](../Text/A310332_2_En_9_Chapter.html#Sec5) 260 [Track the Current Menu Page](../Text/A310332_2_En_9_Chapter.html#Sec6) 260 [Pause the Game](../Text/A310332_2_En_9_Chapter.html#Sec7) 261 [Check Time.​DeltaTime](../Text/A310332_2_En_9_Chapter.html#Sec8) 262 [Display the Menu](../Text/A310332_2_En_9_Chapter.html#Sec9) 263 [Automatic Layout](../Text/A310332_2_En_9_Chapter.html#Sec10) 264 [The Main Page](../Text/A310332_2_En_9_Chapter.html#Sec11) 264 [The Credits Page](../Text/A310332_2_En_9_Chapter.html#Sec12) 266 [The Options Page](../Text/A310332_2_En_9_Chapter.html#Sec13) 267 [The Audio Panel](../Text/A310332_2_En_9_Chapter.html#Sec14) 268 [The Graphics Panel](../Text/A310332_2_En_9_Chapter.html#Sec15) 269 [The System Panel](../Text/A310332_2_En_9_Chapter.html#Sec16) 270 [Customize the GUI Color](../Text/A310332_2_En_9_Chapter.html#Sec17) 271 [Customize the Skin](../Text/A310332_2_En_9_Chapter.html#Sec18) 274 [The Complete Script](../Text/A310332_2_En_9_Chapter.html#Sec19) 276 [Explore Further](../Text/A310332_2_En_9_Chapter.html#Sec20) 276 [Unity Manual](../Text/A310332_2_En_9_Chapter.html#Sec21) 277 [Reference Manual](../Text/A310332_2_En_9_Chapter.html#Sec22) 277 [Scripting Reference](../Text/A310332_2_En_9_Chapter.html#Sec23) 277 [Asset Store](../Text/A310332_2_En_9_Chapter.html#Sec24) 277 [Chapter 10:​ Using Unity iOS](../Text/A310332_2_En_10_Chapter.html) 279 [Test with the Unity Remote](../Text/A310332_2_En_10_Chapter.html#Sec1) 282 [Install Xcode](../Text/A310332_2_En_10_Chapter.html#Sec2) 285 [Customize the Player Settings](../Text/A310332_2_En_10_Chapter.html#Sec3) 286 [Resolution and Presentation](../Text/A310332_2_En_10_Chapter.html#Sec4) 289 [Other Settings](../Text/A310332_2_En_10_Chapter.html#Sec5) 289 [Test with the iOS Simulator](../Text/A310332_2_En_10_Chapter.html#Sec6) 292 [Explore Further](../Text/A310332_2_En_10_Chapter.html#Sec7) 298 [Unity Manual](../Text/A310332_2_En_10_Chapter.html#Sec8) 299 [Reference Manual](../Text/A310332_2_En_10_Chapter.html#Sec9) 299 [iOS Developer Library](../Text/A310332_2_En_10_Chapter.html#Sec10) 299 [Chapter 11:​ Building for Real:​ Device Testing and App Submission](../Text/A310332_2_En_11_Chapter.html) 301 [Register as an iOS Developer](../Text/A310332_2_En_11_Chapter.html#Sec1) 302 [Use the Provisioning Portal](../Text/A310332_2_En_11_Chapter.html#Sec2) 302 [Register Test Devices](../Text/A310332_2_En_11_Chapter.html#Sec3) 303 [Register App Identifiers](../Text/A310332_2_En_11_Chapter.html#Sec4) 304 [Use Development Provisioning Profiles](../Text/A310332_2_En_11_Chapter.html#Sec5) 304 [Use Distribution Provisioning Profiles](../Text/A310332_2_En_11_Chapter.html#Sec6) 305 [Use the Xcode Organizer](../Text/A310332_2_En_11_Chapter.html#Sec7) 305 [Build and Run](../Text/A310332_2_En_11_Chapter.html#Sec8) 306 [Prepare App Store Graphics](../Text/A310332_2_En_11_Chapter.html#Sec9) 311 [Create an Icon](../Text/A310332_2_En_11_Chapter.html#Sec10) 311 [Take Screenshots](../Text/A310332_2_En_11_Chapter.html#Sec11) 311 [Add an App on iTunes Connect](../Text/A310332_2_En_11_Chapter.html#Sec12) 312 [Select an App Type](../Text/A310332_2_En_11_Chapter.html#Sec13) 313 [Enter App Information](../Text/A310332_2_En_11_Chapter.html#Sec14) 314 [Set Availability and Price](../Text/A310332_2_En_11_Chapter.html#Sec15) 315 [Set Language and Category](../Text/A310332_2_En_11_Chapter.html#Sec16) 315 [Prepare for Submission](../Text/A310332_2_En_11_Chapter.html#Sec17) 316 [Upload the Icon and Screenshots](../Text/A310332_2_En_11_Chapter.html#Sec18) 317 [General App Information](../Text/A310332_2_En_11_Chapter.html#Sec19) 318 [Submit for Review](../Text/A310332_2_En_11_Chapter.html#Sec21) 321 [Be Prepared for Rejection](../Text/A310332_2_En_11_Chapter.html#Sec22) 322 [Update the App](../Text/A310332_2_En_11_Chapter.html#Sec23) 322 [Track Sales](../Text/A310332_2_En_11_Chapter.html#Sec24) 323 [Issue Promo Codes](../Text/A310332_2_En_11_Chapter.html#Sec25) 323 [Explore Further](../Text/A310332_2_En_11_Chapter.html#Sec26) 325 [Unity Manual](../Text/A310332_2_En_11_Chapter.html#Sec27) 325 [Apple Developer Site](../Text/A310332_2_En_11_Chapter.html#Sec28) 325 [Chapter 12:​ Presentation:​ Screens and Icons](../Text/A310332_2_En_12_Chapter.html) 327 [Bowling for iOS](../Text/A310332_2_En_12_Chapter.html#Sec1) 328 [Scale the GUI](../Text/A310332_2_En_12_Chapter.html#Sec2) 331 [Scale the Scoreboard](../Text/A310332_2_En_12_Chapter.html#Sec3) 331 [Scale the Pause Menu](../Text/A310332_2_En_12_Chapter.html#Sec4) 333 [Set the Icon](../Text/A310332_2_En_12_Chapter.html#Sec5) 335 [Set the Splash Screen](../Text/A310332_2_En_12_Chapter.html#Sec6) 339 [Create a Second Splash Screen](../Text/A310332_2_En_12_Chapter.html#Sec7) 342 [Create the Splash Scene](../Text/A310332_2_En_12_Chapter.html#Sec8) 342 [Create the Splash Screen](../Text/A310332_2_En_12_Chapter.html#Sec9) 344 [Load the Next Scene](../Text/A310332_2_En_12_Chapter.html#Sec10) 347 [Display the Activity Indicator](../Text/A310332_2_En_12_Chapter.html#Sec11) 349 [Script the Activity Indicator](../Text/A310332_2_En_12_Chapter.html#Sec12) 351 [Explore Further](../Text/A310332_2_En_12_Chapter.html#Sec13) 353 [Reference Manual](../Text/A310332_2_En_12_Chapter.html#Sec14) 353 [Scripting Reference](../Text/A310332_2_En_12_Chapter.html#Sec15) 353 [iOS Developer Library](../Text/A310332_2_En_12_Chapter.html#Sec16) 354 [Asset Store](../Text/A310332_2_En_12_Chapter.html#Sec17) 354 [Books](../Text/A310332_2_En_12_Chapter.html#Sec18) 355 [Chapter 13:​ Handling Device Input](../Text/A310332_2_En_13_Chapter.html) 357 [The Touchscreen](../Text/A310332_2_En_13_Chapter.html#Sec1) 357 [Swipe the Ball](../Text/A310332_2_En_13_Chapter.html#Sec2) 357 [Tap the Ball](../Text/A310332_2_En_13_Chapter.html#Sec3) 360 [The Accelerometer](../Text/A310332_2_En_13_Chapter.html#Sec4) 364 [Debug the Accelerometer](../Text/A310332_2_En_13_Chapter.html#Sec5) 364 [Detect Shakes](../Text/A310332_2_En_13_Chapter.html#Sec6) 365 [The Camera](../Text/A310332_2_En_13_Chapter.html#Sec7) 367 [Read the Web Cam](../Text/A310332_2_En_13_Chapter.html#Sec8) 367 [Explore Further](../Text/A310332_2_En_13_Chapter.html#Sec9) 367 [Scripting Reference](../Text/A310332_2_En_13_Chapter.html#Sec10) 368 [iOS Developer Library](../Text/A310332_2_En_13_Chapter.html#Sec11) 368 [Asset Store](../Text/A310332_2_En_13_Chapter.html#Sec12) 369 [Chapter 14:​ Optimization](../Text/A310332_2_En_14_Chapter.html) 373 [Choose Your Target](../Text/A310332_2_En_14_Chapter.html#Sec1) 373 [Frame Rate](../Text/A310332_2_En_14_Chapter.html#Sec2) 373 [Targeting Space](../Text/A310332_2_En_14_Chapter.html#Sec5) 375 [Profile the Game](../Text/A310332_2_En_14_Chapter.html#Sec6) 375 [Game View Stats](../Text/A310332_2_En_14_Chapter.html#Sec7) 375 [The Build Log](../Text/A310332_2_En_14_Chapter.html#Sec8) 376 [Run the Built-in Profiler](../Text/A310332_2_En_14_Chapter.html#Sec9) 378 [Run the Editor Profiler](../Text/A310332_2_En_14_Chapter.html#Sec10) 381 [Manually Connect the Profiler](../Text/A310332_2_En_14_Chapter.html#Sec11) 383 [Add a Frame Rate Display](../Text/A310332_2_En_14_Chapter.html#Sec12) 384 [Optimize Settings](../Text/A310332_2_En_14_Chapter.html#Sec13) 387 [Quality Settings](../Text/A310332_2_En_14_Chapter.html#Sec14) 388 [Physics Manager](../Text/A310332_2_En_14_Chapter.html#Sec15) 389 [Time Manager](../Text/A310332_2_En_14_Chapter.html#Sec16) 390 [Audio Manager](../Text/A310332_2_En_14_Chapter.html#Sec17) 391 [Optimizing GameObjects](../Text/A310332_2_En_14_Chapter.html#Sec24) 394 [Camera](../Text/A310332_2_En_14_Chapter.html#Sec25) 394 [Lights](../Text/A310332_2_En_14_Chapter.html#Sec26) 396 [Pins](../Text/A310332_2_En_14_Chapter.html#Sec27) 397 [Optimize Assets](../Text/A310332_2_En_14_Chapter.html#Sec30) 401 [Textures](../Text/A310332_2_En_14_Chapter.html#Sec31) 401 [Audio](../Text/A310332_2_En_14_Chapter.html#Sec32) 403 [Meshes](../Text/A310332_2_En_14_Chapter.html#Sec33) 406 [Optimize Scripts](../Text/A310332_2_En_14_Chapter.html#Sec34) 407 [Cache GetComponent](../Text/A310332_2_En_14_Chapter.html#Sec35) 407 [UnityGUI](../Text/A310332_2_En_14_Chapter.html#Sec36) 408 [Runtime Static Batching](../Text/A310332_2_En_14_Chapter.html#Sec37) 409 [Share Materials](../Text/A310332_2_En_14_Chapter.html#Sec38) 409 [Minimize Garbage Collection](../Text/A310332_2_En_14_Chapter.html#Sec39) 411 [Optimize Offline](../Text/A310332_2_En_14_Chapter.html#Sec40) 412 [Beast](../Text/A310332_2_En_14_Chapter.html#Sec41) 412 [Umbra](../Text/A310332_2_En_14_Chapter.html#Sec42) 413 [Final Profile](../Text/A310332_2_En_14_Chapter.html#Sec43) 414 [Explore Further](../Text/A310332_2_En_14_Chapter.html#Sec44) 415 [Unity Manual](../Text/A310332_2_En_14_Chapter.html#Sec45) 415 [Reference Manual](../Text/A310332_2_En_14_Chapter.html#Sec46) 415 [Scripting Reference](../Text/A310332_2_En_14_Chapter.html#Sec47) 416 [Asset Store](../Text/A310332_2_En_14_Chapter.html#Sec48) 416 [On the Web](../Text/A310332_2_En_14_Chapter.html#Sec49) 416 [Books](../Text/A310332_2_En_14_Chapter.html#Sec50) 416 [Chapter 15:​ Where to Go from Here?​](../Text/A310332_2_En_15_Chapter.html) 417 [More Scripting](../Text/A310332_2_En_15_Chapter.html#Sec1) 417 [Editor Scripts](../Text/A310332_2_En_15_Chapter.html#Sec2) 417 [C#](../Text/A310332_2_En_15_Chapter.html#Sec3) 419 [Script Execution Order](../Text/A310332_2_En_15_Chapter.html#Sec4) 424 [Plug-ins](../Text/A310332_2_En_15_Chapter.html#Sec5) 427 [Tracking Apps](../Text/A310332_2_En_15_Chapter.html#Sec6) 427 [Promo Codes](../Text/A310332_2_En_15_Chapter.html#Sec7) 429 [More Monetization](../Text/A310332_2_En_15_Chapter.html#Sec8) 430 [In-App Purchasing](../Text/A310332_2_En_15_Chapter.html#Sec9) 430 [Asset Store](../Text/A310332_2_En_15_Chapter.html#Sec10) 430 [Developing for Android](../Text/A310332_2_En_15_Chapter.html#Sec11) 430 [Contract Work](../Text/A310332_2_En_15_Chapter.html#Sec12) 431 [Final Words](../Text/A310332_2_En_15_Chapter.html#Sec13) 431 Index433 About the Author

# 1. Getting Started

Unity is a cross-platform 2D and 3D game development system developed by a company called Unity Technologies (originally named Over the Edge). What does it mean to call Unity cross-platform, exactly? Well, it’s cross-platform in the sense that the Unity Editor, the game creation and editing tool that is the centerpiece of Unity, runs on macOS and Windows. More impressively, Unity is cross-platform in the sense that using the Unity Editor you can build games for macOS, Windows, web browsers (using Flash, Google Native Client, or Unity’s browser plug-in), iOS, Android, and game consoles. And the list keeps growing.

As for 3D, Unity is a 3D game development system in the sense that Unity’s built-in graphics, sound, and physics engines all operate in 3D space, which is perfect for creating 3D games. Unity can also be used to create 2D games; in fact, many successful 2D games have been developed in Unity.

This book describes the latest version of Unity as of this writing, which is Unity 5.6.2, but Unity is a fast-moving target, with new features and user interface changes appearing even in minor releases. This caveat applies to everything, of course, including products, licenses, and prices from Unity, Apple, and third-party vendors.

## Prerequisites

Before the fun part, which includes learning how to use the Unity Editor and build games, you need to download Unity and install it. Although you’ll spend the first several chapters working with step-by-step examples in the Unity Editor and not get into iOS development until later (by the way, iOS originally stood for iPhone Operating System but now includes the iPod touch and iPad), it’s not a bad idea to get started on the iOS development prerequisites, too.

### Prepare Your Mac

For iOS development, you’ll need a Mac running the Lion or Mountain Lion version of macOS (10.9.4 version and Xcode 7.0 or higher). Unity 5 can still run on some older versions of macOS, like Snow Leopard, but Lion and Mountain Lion will need the latest version of Xcode, the software tool required by Apple for iOS development. Typically, the latest or a fairly recent version of Xcode is required to target the latest version of iOS .

### Register as an iOS Developer

It’s worth visiting the Apple developer site to register as an iOS developer as soon as possible since the approval process can take a while, particularly if you’re registering a company. First, you’ll need to register on the site as an Apple developer (free); then, you’ll need to log in and register as an iOS developer ($99 per year). This is required to deploy your apps on test devices and to submit those apps to the App Store.

### Download Xcode

Although you won’t need Xcode until you reach the iOS portion of this book, you can download Xcode now from the Mac App Store (just search for xcode) or from the Apple developer site at [`http://developer.apple.com/`](http://developer.apple.com/) . When you start building Unity iOS apps, I’ll go over the Xcode installation in more detail.

### Download Unity

To obtain Unity, visit the Unity web site at [`http://unity3d.com/`](http://unity3d.com/) and go to the Download page. There you will find a download link for the latest version of Unity (at the moment, Unity 5.6.2) and also a link to the release notes (which are included with the installation). There is even a link to a list of older versions in case you need to roll back to a previous version of Unity for some reason.

Tip

While you’re on the Unity web site, take a look around. Check out the demos, the FAQ, the feature comparisons among the various licenses, and the support links to the documentation, user forum, and other community support sites. You’ll certainly have to come back later, so you might as well figure out where everything is now!

There is only one Unity application, but depending on your needs, you can subscribe to different licensing models (Person, Plus, Pro, or Enterprise).

Unity version numbers are in the form major.minor.patch. So, Unity 5.6.2 is Unity 5.6 with an incremental upgrade to Unity 5.5, with a couple of bug fix updates. Major upgrades, such as Unity 4 to Unity 5, may require changes to the project.

Tip

In general, once a Unity project has been upgraded, it may become incompatible with older versions of Unity. So, it’s a good idea to make a copy of your project before upgrading it, just in case you need to revert to the previous version of Unity.

To start the Unity installation process, click the download link (as of this writing, it’s the Try Personal button). The file is about 1GB in size, so the download might take a while, but you’re on your way!

## Install Unity

The Unity download file is a download installer, which at this time is a file named UnityDownloadAssistant.

### Run the Download Assistant

Double-click the Unity Download Assistant icon to start the Unity installation (Figure [1-1](#A310332_2_En_1_Chapter.html#Fig1)).

![A310332_2_En_1_Fig1_HTML.jpg](Images/A310332_2_En_1_Fig1_HTML.jpg)

Figure 1-1.

The Unity Download Assistant

The installer will proceed through a typical installation sequence (the steps are listed on the left side of the installer window), and at the end, a Unity folder will be placed in your Applications folder.

Tip

If you happen to be upgrading from an older version of Unity, this process will just replace that old version. If you want to keep the previous copy, rename the old folder first. For example, if you’re upgrading from Unity 4.5 to Unity 5, first rename your Unity folder to Unity45 before performing the new installation. Then you can run both versions of Unity.

The Unity installation folder contains the Unity application and several associated files and folders (Figure [1-2](#A310332_2_En_1_Chapter.html#Fig2)).

![A310332_2_En_1_Fig2_HTML.jpg](Images/A310332_2_En_1_Fig2_HTML.jpg)

Figure 1-2.

Unity installation folder

The most important file in the Unity installation folder, and the only one you really need, is the Unity app, which provides the environment used to construct your games. This app is sometimes more specifically termed the Unity Editor, as distinct from the Unity runtime engine or Unity Player, which is incorporated into final builds. But usually when I just say “Unity,” the context is clear (I also refer to Unity Technologies as Unity).

The Documentation folder contains the same User Manual, Component Reference, and Script Reference viewable on the Unity web site (on the Learn tab). Each of these files can be opened in a web browser from the Unity Help menu, and you can always double-click `Documentation.html` directly to see the front page of the documentation.

The Standard Packages folder contains several files with the `.unityPackage` extension. These Unity package files each hold collections of Unity assets and can be imported into Unity (and from Unity, you can export assets into a package file).

The MonoDevelop app is the default script editor for Unity and is a custom version of the open source MonoDevelop editor used for the Mono Project, known as Mono for short. Mono is an open source version of Microsoft’s .NET Framework and forms the basis of the Unity scripting system.

Finally, there’s the Unity Bug Reporter app, which is normally run from the Report a Bug item in the Unity Help menu. However, you can always launch the Unity Bug Reporter directly from the Unity installation folder. That’s pretty helpful if you run into a bug where Unity doesn’t even start up!

Be sure to install the Example Project component, which you will be working on in this book. In Figure [1-3](#A310332_2_En_1_Chapter.html#Fig3), I have also selected the WebGL Build Support component. If you also plan to build games for Windows computers , be sure to install the Windows Build Support component. Now would be a good time to grab a coffee or tea or take a walk; depending on how many options you selected and the speed of your Internet connection, the download may take a while (Figure [1-4](#A310332_2_En_1_Chapter.html#Fig4)).

![A310332_2_En_1_Fig4_HTML.jpg](Images/A310332_2_En_1_Fig4_HTML.jpg)

Figure 1-4.

Downloading and installing message

![A310332_2_En_1_Fig3_HTML.jpg](Images/A310332_2_En_1_Fig3_HTML.jpg)

Figure 1-3.

Unity component selection

### Welcome to Unity!

After installing Unity, the Unity Editor window will appear with a friendly Unity Hello! window appearing on top (Figure [1-5](#A310332_2_En_1_Chapter.html#Fig5)). The Hello! window is where you can sign into your Unity account. If you do not have a Unity account, select the “create one” link. If you are not currently connected to the Internet, you can work offline by clicking the “Work offline” button.

![A310332_2_En_1_Fig5_HTML.jpg](Images/A310332_2_En_1_Fig5_HTML.jpg)

Figure 1-5.

The Unity Hello! window

The Unity Hello! window will appear every time you start up Unity. If you haven’t already created a Unity account, now would be a good time to create one.

After signing in for the first time, you will be presented with the Unity “License management” screen. If you have paid for a licensed version (Plus or Pro) version of Unity, enter your serial number in the dialog box. If you are planning to use the free version of Unity, select the Unity Personal radio button.

![A310332_2_En_1_Figa_HTML.jpg](Images/A310332_2_En_1_Figa_HTML.jpg)

## Manage Unity

Before getting into actual game development with Unity, this is a good time to look at some of the administrative features in the Unity Editor.

### Change Skins (Pro)

The Unity Editor appears in one of two skins , Dark or Light. If you’re using Unity Pro, you have the option of using the Dark skin, and if you’re using the Personal version of Unity, you’ll only see the Light skin (Figure [1-6](#A310332_2_En_1_Chapter.html#Fig6)).

![A310332_2_En_1_Fig6_HTML.jpg](Images/A310332_2_En_1_Fig6_HTML.jpg)

Figure 1-6.

The Unity Editor

For the rest of this book, I use the Light skin for screenshots, but aside from the hue, there is no difference in the user interface. To change this (for Unity Pro), select Preferences in the Unity menu (Figure [1-7](#A310332_2_En_1_Chapter.html#Fig7)).

![A310332_2_En_1_Fig7_HTML.jpg](Images/A310332_2_En_1_Fig7_HTML.jpg)

Figure 1-7.

The Preferences menu item in the Unity Editor

With the Preferences window open, you can change the skin from Dark to Light or Light to Dark. If you’re using Unity Personal, you’re stuck with the Light skin, as shown by the grayed-out Editor Skin option in Figure [1-8](#A310332_2_En_1_Chapter.html#Fig8).

![A310332_2_En_1_Fig8_HTML.jpg](Images/A310332_2_En_1_Fig8_HTML.jpg)

Figure 1-8.

General preferences in the Unity Editor

While you’re in the Preferences window, I recommend making sure Load Previous Project on Startup is deselected. That will ensure Unity brings up the project selection dialog when it starts up, instead of automatically opening the most recently opened project, which can be time-consuming and is not always what you want. In particular, you don’t want to accidentally upgrade a project to a new version of Unity before you’re ready.

A single Unity license can be used on two machines. In its early years, when the Unity Editor ran only on macOS, a Unity license was good for only one machine, but the number was increased to two after Windows support for the Unity Editor was added.

### Report Problems

If you use Unity for a significant period of time, you’ll certainly encounter bugs, real or imagined. That’s not a knock on Unity. The 3D game engines are very complicated, at least internally, and the pace of their development is remarkable. (I started with Unity 1.6 when it ran only on macOS and deployed builds only for Windows and macOS.) Bugs don’t fix themselves, especially when they’re not reported. That’s where the Unity Bug Reporter comes in. As I mentioned when going over the Unity installation files, the Bug Reporter is available in the Unity folder but normally is launched from the Help menu in the Unity Editor (Figure [1-9](#A310332_2_En_1_Chapter.html#Fig9)).

![A310332_2_En_1_Fig9_HTML.jpg](Images/A310332_2_En_1_Fig9_HTML.jpg)

Figure 1-9.

The Report a Bug option in the Help menu Tip

The Bug Reporter, as you can see in the list of files in the Unity installation, is a separate application from the Unity Editor. So if you encounter a bug that prevents the Unity Editor from starting up properly, you can always launch the Bug Reporter directly by double-clicking that file in the Finder.

The resulting Unity Bug Reporter window (Figure [1-10](#A310332_2_En_1_Chapter.html#Fig10)) prompts you to select where the bug occurs (in the Unity Editor or in the Unity Player, i.e., a deployed build), specify the frequency of the bug’s occurrence, and specify an e-mail address to which Unity Technologies will send responses about this bug.

![A310332_2_En_1_Fig10_HTML.jpg](Images/A310332_2_En_1_Fig10_HTML.jpg)

Figure 1-10.

The Unity Bug Reporter window

Below that, in the middle of the window, is the list of attachments. Unity automatically starts off this list with the current project, and you might include supplemental files such as screenshots or log files. You can remove the current project from this list, but normally you should include the project so that Unity support can reproduce the problem.

By the same token, you should fill in the problem description with a detailed enough explanation that Unity support would know how to replicate the undesired behavior and understand why it’s undesired. Basically, you want to avoid responses in the mode of “We can’t reproduce the problem” and “This is not a bug. It’s by design.”

Shortly after submitting a bug report, you should receive an e-mail confirmation from Unity Technologies with a case number and a link to a copy of the report in the Unity bug database, which you can check to see the status of the bug.

### Check for Updates

If you’re lucky, your bug report will result in a fix. If you’re really lucky, that fix will show up in the next Unity update. You can always check whether there’s a new update by selecting the Check for Updates command from the Window menu on the menu bar. The resulting window will display whether an update is available (Figure [1-11](#A310332_2_En_1_Chapter.html#Fig11)).

![A310332_2_En_1_Fig11_HTML.jpg](Images/A310332_2_En_1_Fig11_HTML.jpg)

Figure 1-11.

The Unity Editor update check

Notice the version number displayed in Figure [1-11](#A310332_2_En_1_Chapter.html#Fig11) is Unity 5.6.2f1\. The suffix represents the most granular releases, including emergency hot fixes. Out of some caution, Unity Technologies usually doesn’t make newly released updates immediately available to the Editor update check, so if you’re waiting anxiously for a bug-fix update, you can always check the Unity web site , too.

## Explore Further

My favorite technical books, like Peter van der Linden’s Just Java (a detailed but easygoing introduction to Java for nonprogrammers), provide a nice break at the end of each chapter by reciting an interesting anecdote, a bit of trivia, or some relevant history. Alas, I won’t be doing that in this book. However, I do find simple chapter summaries and recaps dull (I always skip them as a reader), and I feel one of the major challenges facing new Unity developers is that they need to get in the habit of finding Unity information on their own, and they need to know where to find that information, which isn’t always easy!

Therefore, at the end of each chapter, including this one, I’ll direct you to documentation and other resources related to the topics you just learned so you’ll know where to find the definitive and comprehensive information sources and can take things further when you’ve finished this book. I’ll focus on the official Unity manuals and the Unity web site but also mention some third-party resources. So now that you have Unity installed, before putting it to use in this book, take a break and browse the web sites and reference documentation that you will surely be utilizing heavily from now on. It’s always a good idea to figure out where to find what you need before you need it!

### iOS Development Requirements

At the beginning of this chapter, I mentioned it’s a good idea to get the ball rolling on downloading Xcode and registering for the Apple Developer Program.

Requirements for iOS development, including required hardware and details about the Apple Developer Program, are listed on Apple’s developer support page ( [`http://developer.apple.com/support`](http://developer.apple.com/support) ).

You can find information about Xcode requirements and downloading Xcode at [`http://developer.apple.com/xcode`](http://developer.apple.com/xcode) .

### The Unity Web Site

Along with the growth of Unity features and platforms, the Unity web site ( [`http://unity3d.com/`](http://unity3d.com/) ) has grown, too. It’s all worth browsing, but in particular, for general information about Unity, check out the FAQ section on the Unity web site ( [`http://unity3d.com/unity/faq`](http://unity3d.com/unity/faq) ).

Many instructional videos are available on the Unity Video archive ( [`http://video.unity3d.com/`](http://video.unity3d.com/) ), and Unity has recently introduced a Learn tab on its web site for easy access to documentation and tutorials.

### Unity Manuals and References

The top section of the Unity Help menu (Figure [1-12](#A310332_2_En_1_Chapter.html#Fig12)) lists the official Unity documentation consisting of three documents: the Unity Manual, the Reference Manual, and the Scripting Reference. Although the Unity Manual doesn’t have much information on the installation and licensing procedure discussed in this chapter, the manual otherwise provides good general instruction and conceptual explanations on using Unity. The Reference Manual and Scripting Reference will become increasingly important as you create and script scenes throughout this book.

![A310332_2_En_1_Fig12_HTML.jpg](Images/A310332_2_En_1_Fig12_HTML.jpg)

Figure 1-12.

The Help menu

The Unity Help menu items bring up the locally installed copies of those manuals, but they are also available on the Learn tab of the Unity web site.

### The Unity Community

The first three items in the middle of the Unity Help menu are links to the official Unity community sites. The forum ( [`http://forum.unity3d.com`](http://forum.unity3d.com) ) is where users can bring up just about anything related to Unity (the forum does have moderators, though).

The Unity Answers site ( [`http://answers.unity3d.com/`](http://answers.unity3d.com/) ) follows the format of Stack Exchange and provides some quality control over questions and answers. The Unity Feedback site ( [`http://feedback.unity3d.com`](http://feedback.unity3d.com) ) allows users to post feature requests and vote on feature requests, whether they’re their own or posted by someone else.

Tip

Although the bug type selector offers Feature Request as a type of bug, Unity encourages everyone to submit feature requests to its Feedback site.

Welcome to the Unity community !

# 2. A Unity Tour

Now that you have Unity installed, it’s time to get acquainted with the Unity Editor. The Editor (I’ll use the terms Unity, Unity Editor, and Editor interchangeably, as long as the meaning is clear) is where you create, test, and build a game for one or more target platforms. The Editor operates on a single Unity project containing assets included in a game.

This chapter will introduce the Unity Editor using the Climber Game from the Unity Asset Store. I won’t delve into the inner workings of this game here, but it’s a convenient ready-made project you can use to get acquainted with Unity, even to the point of building the game as a macOS app to get a feel for the build process. Hopefully, this will get you comfortable with Unity’s workflow, user interface and various file types before learning how to create a new project from scratch in the next chapter.

Tip

Filename extensions are hidden by default in macOS, but anyone performing any kind of development should be looking at complete filenames. To ensure extensions are displayed, go to the Advanced tab in the Finder Preferences window and check “Show all filename extensions.”

## Introducing The Climber Game

To install the Climber Game from the Unity Store, you will need to access the Unity Asset Store. The Unity Asset Store can be accessed a number of ways. In the main window of the Unity screen, there is an Asset Store tab, this will bring up the Asset Store Window. This window can also be access by using the option button and 9 (⌘+9). To download resources from the asset store, you will need to register with Unity to create Unity ID. When you have created a Unity ID, in the Unity Asset Store window, there is a search bar. Type in the search bar Climber and this will bring up a list of files that meet this search criteria. The default window settings in Unity, will show the Unity Store window minimized, to view the Asset Store in Full Screen mode, there is a drop-down menu on the top-right side of the screen. Select this and click with the left mouse button. This will show the screen options, Reload, Maximize, Close Tab, and Add Tab (needs a Figure). Select the maximize option (click the left mouse button). At the top of the screen, there are several filter options. Below the filter options, there the assets that meet your search criteria. Double click the Climber, and this will load the screen for this asset (needs a Figure). Select the import button and this Asset will be imported into Unity.

![A310332_2_En_2_Fig1_HTML.jpg](Images/A310332_2_En_2_Fig1_HTML.jpg)

Figure 2-1.

Unity Project Folder with the Climber Game in the scene folder

### Open the Climber Project

Unity will automatically open the Climber project when imported. If for some reason Unity doesn’t immediately open Climber Game , or if you want to return to it later (as we will in Chapters [10](../Text/A310332_2_En_10_Chapter.html) and [11](../Text/A310332_2_En_11_Chapter.html) where we will use Climber Game to get acquainted with Unity iOS), select the Open Project item in the File menu on the Unity menu bar (Figure [2-2](#A310332_2_En_2_Chapter.html#Fig2)).

![A310332_2_En_2_Fig2_HTML.jpg](Images/A310332_2_En_2_Fig2_HTML.jpg)

Figure 2-2.

Open Project menu item

The Project Wizard will appear (the same Project Wizard referred to by the Always Show Project Wizard item in the Preferences window). Select the Climber Game project from the list of recent projects in the Project Wizard (the same one that will show up every time you start Unity if you left the box checked) or click the Open button (Figure [2-3](#A310332_2_En_2_Chapter.html#Fig3)).

![A310332_2_En_2_Fig3_HTML.jpg](Images/A310332_2_En_2_Fig3_HTML.jpg)

Figure 2-3.

Selecting Climber Game from the Open Project menu

### Open the Climber Game Scene

With the Climber Game project open, the Unity Editor should now display the main scene of the project, named New project. The title bar of the window should list the scene file, New Unity Project, followed by the project name and the target platform (Figure [2-4](#A310332_2_En_2_Chapter.html#Fig4)).

The scene name is displayed along the upper border of the Editor window, and the scene contents are reflected in the Hierarchy View, Scene View, and Game View. I’ll explain views in a bit and spend a good portion of this chapter going over each view in detail.

A scene is the same as a level in a game (in fact, the Unity script function that loads a scene is called Application.LoadLevel). The purpose of the Unity Editor is to construct scenes, and thus it always has one scene open at a time.

If the Editor doesn’t open an existing scene at startup, it will open a newly created scene, the same as if you had selected New Scene from the File menu on the Unity menu bar.

![A310332_2_En_2_Fig4_HTML.jpg](Images/A310332_2_En_2_Fig4_HTML.jpg)

Figure 2-4.

Climber Game project opened with a new scene

If you’re not looking at the Climber Game scene right now, then select the Open Scene command in the File menu (Figure [2-5](#A310332_2_En_2_Chapter.html#Fig5)).

![A310332_2_En_2_Fig5_HTML.jpg](Images/A310332_2_En_2_Fig5_HTML.jpg)

Figure 2-5.

The Open Scene menu item

The resulting file chooser will start off in the top level of the project’s Assets folder, where you can select the Scenes Folder and then select the Main scene file (Figure [2-6](#A310332_2_En_2_Chapter.html#Fig6)). All scene files are displayed with a Unity icon and have the .unity filename extension.

![A310332_2_En_2_Fig6_HTML.jpg](Images/A310332_2_En_2_Fig6_HTML.jpg)

Figure 2-6.

Selecting the Climber Game scene in the Load Scene chooser

### Play Climber

With the desired scene open, click the Play button at the center top of the Editor to start the game. The Game View will appear and display the running game, which can be played with standard keyboard and mouse controls (Figure [2-7](#A310332_2_En_2_Chapter.html#Fig7)).

Use the AWSD (or arrow) keys to move forward, back, left, and right, respectively, and press the Esc key to pause. Clicking the Play button again will stop the game and exit Play mode. The two buttons next to the Play button are used to pause and step through the game.

![A310332_2_En_2_Fig7_HTML.jpg](Images/A310332_2_En_2_Fig7_HTML.jpg)

Figure 2-7.

Play mode in the Editor

### Building a game

During normal development, you would alternate between editing a scene and playing it.

When you’re satisfied how the game plays in the Editor, then you’re ready to build the app for the target platform. You won’t start building for iOS until Chapter [10](../Text/A310332_2_En_10_Chapter.html), but to get a feel for the build process, go ahead and build an macOS app version of Climber right now. Select the Build Settings item from the Unity File menu to bring up the Build Settings window (Figure [2-8](#A310332_2_En_2_Chapter.html#Fig8)).

![A310332_2_En_2_Fig8_HTML.jpg](Images/A310332_2_En_2_Fig8_HTML.jpg)

Figure 2-8.

Bringing up the Build Settings window

The upper portion of the window lists the scenes to include in the build. Just the scene you have open should be checked. Any unchecked or unlisted scenes won’t be included in the build (Figure [2-9](#A310332_2_En_2_Chapter.html#Fig9)).

![A310332_2_En_2_Fig9_HTML.jpg](Images/A310332_2_En_2_Fig9_HTML.jpg)

Figure 2-9.

Build Settings for the Mac platform

The default platform selected in the Platform list on the left is PC, Mac & Linux Standalone, matching the platform listed on the Editor title bar.

Select from the file menu, Build Settings, and select the PC, Mac & Linux Standalone icone.

Now you can click either the Build or Build and Play button in the Build Settings window. Clicking Build will generate an macOS app version of our game. Clicking Build and Play will do the same but also run the game, which just saves the effort of bringing up a Finder window and double-clicking the new app. Unity will prompt you for a file name and location for the app, defaulting to the top level of the project directory, which is fine (Figure [2-10](#A310332_2_En_2_Chapter.html#Fig10)).

![A310332_2_En_2_Fig10_HTML.jpg](Images/A310332_2_En_2_Fig10_HTML.jpg)

Figure 2-10.

Saving an macOS app build

When the build is complete, double-click the generated app to run the game. Or if you selected Build and Run, the game should start automatically. Unity-generated macOS apps start with a resolution-chooser window (Figure [2-11](#A310332_2_En_2_Chapter.html#Fig11)).

![A310332_2_En_2_Fig11_HTML.jpg](Images/A310332_2_En_2_Fig11_HTML.jpg)

Figure 2-11.

Startup dialog for a Unity macOS app

After selecting the desired resolution and clicking Play!, you’ll see a game window with the size you just specified. Now you have Climber running in its own Mac window (Figure [2-12](#A310332_2_En_2_Chapter.html#Fig12)).

![A310332_2_En_2_Fig12_HTML.jpg](Images/A310332_2_En_2_Fig12_HTML.jpg)

Figure 2-12.

Climber as a Mac app

## The Editor Layout

Now that you’ve got a feel for the Unity test-and-build workflow with the Climber project, let’s take a closer look at the layout of the Unity Editor. The main window is divided into areas (I’m used to calling them panes, but I’ll call them areas since that’s the term used in Xcode). Each area holds one or more tabbed views. The default displayed view (factory Settings) for an area is selected by clicking the view’s tab. Views can be added, moved, removed, and resized, and the Editor supports switching among layouts, so a layout essentially is a specific arrangement of views. For example, the default layout of the main window (Figure [2-13](#A310332_2_En_2_Chapter.html#Fig13)) has an area containing a Scene View (Figure [2-14](#A310332_2_En_2_Chapter.html#Fig14)) and a Game View (Figure [2-15](#A310332_2_En_2_Chapter.html#Fig15)).

Note

The Unity documentation is somewhat inconsistent in naming views. It’s obvious from their names that the Game View and Scene View are views. But the Console and Inspector are also views. In this book, I’ll include View in all of their names to make clear they are all views and can be manipulated the same way.

![A310332_2_En_2_Fig13_HTML.jpg](Images/A310332_2_En_2_Fig13_HTML.jpg)

Figure 2-13.

The default layout of the Unity Editor

![A310332_2_En_2_Fig14_HTML.jpg](Images/A310332_2_En_2_Fig14_HTML.jpg)

Figure 2-14.

The Scene View selected in a multitabbed area

![A310332_2_En_2_Fig15_HTML.jpg](Images/A310332_2_En_2_Fig15_HTML.jpg)

Figure 2-15.

The Game View selected in a multitabbed area

### Preset Layouts

The default layout is just one of several preset layouts. Alternate layouts can be selected from the menu in the top right corner of the main window (Figure [2-16](#A310332_2_En_2_Chapter.html#Fig16)). Go ahead and try them out. Figures [2-17](#A310332_2_En_2_Chapter.html#Fig17) through [2-20](#A310332_2_En_2_Chapter.html#Fig20) show the resulting layouts.

![A310332_2_En_2_Fig16_HTML.jpg](Images/A310332_2_En_2_Fig16_HTML.jpg)

Figure 2-16.

The Layout menu

I’ll describe the individual types of views in more detail shortly, but for now note that the 2-by-3 layout (Figure [2-17](#A310332_2_En_2_Chapter.html#Fig17)) is an example of a layout where the Scene View and Game View are in separate areas instead of sharing the same one. The 4-split layout (Figure [2-18](#A310332_2_En_2_Chapter.html#Fig18)) has four instances of the Scene View (reminiscent of tools used for computer-aided design), demonstrating that a layout is not restricted to one of each type of view.

![A310332_2_En_2_Fig20_HTML.jpg](Images/A310332_2_En_2_Fig20_HTML.jpg)

Figure 2-20.

The Wide layout

![A310332_2_En_2_Fig19_HTML.jpg](Images/A310332_2_En_2_Fig19_HTML.jpg)

Figure 2-19.

The Tall layout

![A310332_2_En_2_Fig18_HTML.jpg](Images/A310332_2_En_2_Fig18_HTML.jpg)

Figure 2-18.

The 4-split layout

![A310332_2_En_2_Fig17_HTML.jpg](Images/A310332_2_En_2_Fig17_HTML.jpg)

Figure 2-17.

The 2-by-3 layout

### Custom Layouts

The preset layouts provide a variety of workspaces, but fortunately you’re not restricted to using them exactly as they are. Unity provides the flexibility to completely rearrange the Editor window as you like.

#### Resize Areas

For starters, you may notice while trying out the various preset layouts that some of the areas are too narrow, for example, in the left panel of the Wide layout (Figure [2-20](#A310332_2_En_2_Chapter.html#Fig20)). Fortunately, you can click the border of an area and drag it to resize the area.

#### Move Views

Even cooler, you can move views around. Dragging the tab of a view into another tab region will move the view there. And dragging the tab into a “docking” area will create a new area. For example, start with the Default layout, drag the Inspector tab to the right of the Hierarchy tab. Now the Inspector View shares the same area as the Hierarchy View. The result should look like Figure [2-21](#A310332_2_En_2_Chapter.html#Fig21).

![A310332_2_En_2_Fig21_HTML.jpg](Images/A310332_2_En_2_Fig21_HTML.jpg)

Figure 2-21.

Workspace customized with views moved

#### Detach Views

You can even drag a view outside the Editor window so that it resides in its own “floating” window, which can be treated just like any other area. Drag the Scene tab outside the Editor so it resides in a floating window, and then drag the Game tab into its tab region. The result should look like Figure [2-22](#A310332_2_En_2_Chapter.html#Fig22). Likewise, dragging a tab into a docking region of the floating window will add another area to the window.

Tip

I like to detach the Game View into a floating window, since I normally don’t need to see it while I’m working in the Editor until I click Play, and this allows me to maximize the Game View to fill up to the entire screen.

Floating windows are often covered up by other windows, so the Windows menu on the menu bar has menu items for making each view visible (Figure [2-22](#A310332_2_En_2_Chapter.html#Fig22)). Notice there is a keyboard shortcut for each, and there is also a Layouts submenu that is identical to the layout menu inside the Editor.

![A310332_2_En_2_Fig22_HTML.jpg](Images/A310332_2_En_2_Fig22_HTML.jpg)

Figure 2-22.

List of views in the Windows menu

#### Add and Remove Views

You can also add and remove views in each area using the menu at the top right corner of the area (Figure [2-23](#A310332_2_En_2_Chapter.html#Fig23)). The Close Tab item removes the currently displayed view. The Add Tab item provides a list of new views for you to choose from.

![A310332_2_En_2_Fig23_HTML.jpg](Images/A310332_2_En_2_Fig23_HTML.jpg)

Figure 2-23.

Layouts available

You may want to have different layouts for different target platforms, or different layouts for development vs. play testing, or even different layouts for different games. For example, I have a custom layout specifically for HyperBowl that preserves the Game View in a suitable portrait aspect ratio. It would be a hassle to manually reconfigure the Editor every time you start up Unity. Fortunately, you can name and save layouts by selecting the Save Layout option in the layout menu, which will prompt you for the new layout name (Figure [2-24](#A310332_2_En_2_Chapter.html#Fig24)).

![A310332_2_En_2_Fig24_HTML.jpg](Images/A310332_2_En_2_Fig24_HTML.jpg)

Figure 2-24.

Prompt for new layout

After saving, the new layout will be listed in the layout menu and also in the list of layouts available for deletion if you select Delete Layout (Figure [2-25](#A310332_2_En_2_Chapter.html#Fig25)).

![A310332_2_En_2_Fig25_HTML.jpg](Images/A310332_2_En_2_Fig25_HTML.jpg)

Figure 2-25.

Deletion menu for layouts

If you’ve messed up or deleted the original layouts, you can select the Restore Factory Settings option in the area menu (Figure [2-26](#A310332_2_En_2_Chapter.html#Fig26)). This will also delete any custom layouts.

![A310332_2_En_2_Fig26_HTML.jpg](Images/A310332_2_En_2_Fig26_HTML.jpg)

Figure 2-26.

Restore original layout settings

If you change a layout and haven’t saved the changes, you can always discard them by just reselecting that layout in the layout menu.

## The Inspector View

The best view to describe in detail first is the Inspector View, since its function is to display information about objects selected in other views. It’s really more than an inspector, since it can typically be used to modify the selected item.

The Inspector View is also used to display and adjust the various settings that can be brought up in the Edit menu. For example, you might notice that the Assets folder in the Climber Project has a lot of files with a .meta extension , as shown in Figure [2-27](#A310332_2_En_2_Chapter.html#Fig27). In fact, there is one of these files for each asset file. Unity tracks the assets in a project using these meta files, which are in text format to facilitate use with version control systems like Perforce and Subversion (or newer distributed version control systems like Git and Mercurial).

![A310332_2_En_2_Fig27_HTML.jpg](Images/A310332_2_En_2_Fig27_HTML.jpg)

Figure 2-27.

The meta files in the Climber project

But if you aren’t using a version control system you can turn off version control compatibility in the Editor Settings and get rid of those unsightly files. Bring up the Editor Settings by going to the Edit menu and selecting Editor Settings from the Settings submenu (Figure [2-28](#A310332_2_En_2_Chapter.html#Fig28)).

![A310332_2_En_2_Fig28_HTML.jpg](Images/A310332_2_En_2_Fig28_HTML.jpg)

Figure 2-28.

Bringing up the Editor Settings

Now the Inspector View displays the Editor Settings . If the project currently has meta files, then the Version Control Mode is set to Meta Files (and if you’re using the Asset Server, this option is set to Asset Server). To remove the meta files, set the Version Control Mode to Disabled (Figure [2-29](#A310332_2_En_2_Chapter.html#Fig29)).

![A310332_2_En_2_Fig29_HTML.jpg](Images/A310332_2_En_2_Fig29_HTML.jpg)

Figure 2-29.

Editor Settings in the Inspector View

With the Version Control Mode set to Disabled , Unity will remove the meta files (Figure [2-30](#A310332_2_En_2_Chapter.html#Fig30)). The asset tracking is now handled within binary files inside the Library folder of the project.

Note

Unity users who are using meta files for version control support also have the option of setting Asset Serialization Mode to Force Text. In that mode, Unity scene files are saved in a text-only YAML (YAML Ain’t Markup Language) format.

![A310332_2_En_2_Fig30_HTML.jpg](Images/A310332_2_En_2_Fig30_HTML.jpg)

Figure 2-30.

The Climber project folder minus the meta files

Normally, the Inspector View displays the properties of the most recently selected object (when you bring up the Editor Settings, you really selected it). But sometimes you don’t want the Inspector View to change while you’re selecting other objects. In that case, you can pin the Inspector View to an object by selecting the Lock option in the menu at the top right of the view (Figure [2-31](#A310332_2_En_2_Chapter.html#Fig31)).

![A310332_2_En_2_Fig31_HTML.jpg](Images/A310332_2_En_2_Fig31_HTML.jpg)

Figure 2-31.

Locking the Inspector View

## The Project View

While the Inspector View can be thought of as the lowest-level View in the Editor, since it displays the properties of just a single object, the Project View, can be considered the highest-level view (Figure [2-32](#A310332_2_En_2_Chapter.html#Fig32)). The Project View displays all of the assets available for your game, ranging from individual models, textures and scripts to the scene files which incorporate those assets. All of the project assets are files residing in the Assets folder of your project (so I actually think of the Project View as the Assets View).

![A310332_2_En_2_Fig32_HTML.jpg](Images/A310332_2_En_2_Fig32_HTML.jpg)

Figure 2-32.

Top level of the Project view

### Switch Between One-Column and Two-Columns

Before Unity 5, the Project View had only a one-column display. That option is still available in the menu for the Project View (click the little three-line icon at the top right of the view), so you can now switch between one and two columns.

Notice how the Project View Climber project (Figure [2-32](#A310332_2_En_2_Chapter.html#Fig32)) resembles how the project’s Assets folder looks in the Finder (see Figure [2-30](#A310332_2_En_2_Chapter.html#Fig30)). It actually looks more like a Windows file view, where you navigate the folder hierarchy in the panel on the left and view the contents of the selected folder in the right panel.

### Scale Icons

The slider on the bottom scales the view in the right panel—a larger scale is nice for textures and smaller is better for items like scripts that don’t have interesting icons. This is a good reason to partition assets by asset type (i.e., put all textures in a Textures folders, scripts in a Script folder, and so on). Chances are, a single-scale slider setting won’t be good for a mixture of asset types.

### Inspect Assets

Selecting an asset on the right will display the properties of that asset in the Inspector View. For example, if you select a sound sample, the Inspector View displays information about the sound format, some of which you can change, like the compression, and it even lets you play the audio in the Editor (Figure [2-33](#A310332_2_En_2_Chapter.html#Fig33)). I’ll explain the sound properties in a later chapter, but for now feel free to select various types of assets in the Project View and see what shows up in the Inspector View.

![A310332_2_En_2_Fig33_HTML.jpg](Images/A310332_2_En_2_Fig33_HTML.jpg)

Figure 2-33.

Inspecting a selected asset in the Project View

### Search for Assets

In a large and complex project, it’s difficult to manually search for a particular asset. Fortunately, just as in the Finder, there is a search box that can be used to filter the results showing in the right panel of the Project view. In Figure [2-34](#A310332_2_En_2_Chapter.html#Fig34), the Project View displays the result of searching for assets with “add” in their names.

![A310332_2_En_2_Fig34_HTML.jpg](Images/A310332_2_En_2_Fig34_HTML.jpg)

Figure 2-34.

Searching for assets with “add” in the name

The right panel displays the search results for everything under Assets (i.e., all of our assets). The search can be narrowed further by selecting one of the subfolders in the left panel. For example, if you know you’re looking for a texture, and you’ve arranged your assets into subfolders by the type of asset, you can select the Scripts folder to search (Figure [2-35](#A310332_2_En_2_Chapter.html#Fig35)).

![A310332_2_En_2_Fig35_HTML.jpg](Images/A310332_2_En_2_Fig35_HTML.jpg)

Figure 2-35.

Searching assets in a folder

Notice just below the search, there is a tab with the name of the folder that was selected. You can still click the Assets tab to the left to see the search results for all your assets, both locally and on the Unity Asset Store, which we’ll make copious use of in this book.

You can also filter your search by asset type, using the menu immediately to the right of the search box. Instead of just searching in the Scripts folder, you could have selected Script as the asset type of interest (Figure [2-36](#A310332_2_En_2_Chapter.html#Fig36)). Notice how that resulted in s:Texture being added to the search box. The t: prefix indicates the search should be filtered by the following asset type. You could have just typed that in without using the menu.

![A310332_2_En_2_Fig36_HTML.jpg](Images/A310332_2_En_2_Fig36_HTML.jpg)

Figure 2-36.

Search filtered by asset type

The button to right of the asset type menu is for filtering by label (you can assign a label to each asset in the Inspector View), which is also pretty handy for searching the Asset Store. And the rightmost button, the star, will save the current search in the Favorites section of the left panel.

### Operate on Assets

Assets in the Project View can be manipulated very much like their corresponding files in the Finder.

Double-clicking an asset will attempt to open a suitable program to view or edit the asset. This is equivalent to right-clicking the asset and selecting Open. Double-clicking a scene file will open the scene in this Unity Editor window, just as if you had selected Open Scene in the File menu.

You can also rename, duplicate and delete, and drag files in and out of a folder just as you can in the Finder. Some of the operations are available in the Unity Edit menu and in a pop-up menu when you right-click on an asset. You’ll get some practice with that in the next few chapters.

Likewise, in the next chapter you will work on adding assets to a project. That involves importing a file or importing a Unity package, using the Assets menu on the menu bar or just dragging files into the Assets folder of the project using the Finder.

## The Hierarchy View

Every game engine has a top-level object called a game object or entity to represent anything that has a position, potential behavior, and a name to identify it. Unity game objects are instances of the class GameObject.

Note

In general, when we refer to a type of Unity object, we’ll use its class name to be precise and make clear how that object would be referenced in a script.

The Hierarchy View is another representation of the current scene. While the Scene View is a 3D representation of the scene that you can work in as you would with a content creation tool, and the Game View shows the scene as it looks when playing the game, the Hierarchy View lists all the GameObjects in the scene in an easily navigable tree structure.

### Inspect Game Objects

When you click a GameObject in the Hierarchy View, it becomes the current Editor selection and its components are displayed in the Editor. Every GameObject has a Transform Component, which specifies its position, rotation, and scale, relative to its parent in the hierarchy (if you’re familiar with the math of 3D graphics, the Transform is essentially the transformation matrix of the object). Some components provide a function for the game object (e.g., a light is a GameObject with a Light Component attached). Other components reference assets such as meshes, textures, and scripts. Figure [2-37](#A310332_2_En_2_Chapter.html#Fig37) shows the components of the Player GameObject (in the Hierarchy view, the entire Player tree of GameObjects is displayed in blue because it’s linked to a prefab, a special type of asset that is used to clone a GameObject or group of GameObjects).

![A310332_2_En_2_Fig37_HTML.jpg](Images/A310332_2_En_2_Fig37_HTML.jpg)

Figure 2-37.

Hierarchy View and Inspector View

### Parent and Child GameObjects

Notice that many of the GameObjects are arranged in a hierarchy, hence the name of this view (in computer graphics, this is commonly known as a scene graph). Parenting makes sense for game objects that are conceptually grouped together. For example, when you want to move a car, you want the wheels to automatically move along with the car. So the wheels should be specified as children of the car, offset from the center of the car. When the wheels turn, they turn relative to the movement of the car. Parenting also allows us to activate or deactivate whole groups of game objects at one time.

## The Scene View

Whereas the Hierarchy View allows us to create, inspect, and modify the GameObjects in the current scene, it doesn’t give us a way to visualize the scene. That’s where the Scene View comes in. The Scene View is similar to the interfaces of 3D modeling applications. It lets you examine and modify the scene from any 3D vantage point and gives you an idea how the final product will look.

### Navigate the Scene

If you’re not familiar with working in 3D space, it’s a straightforward extension from working in 2D. Instead of just working in a space with x and y axes and (x,y) coordinates, in 3D space, you have an additional z axis and (x,y,z) coordinates. The x and z axes define the ground plane and y is pointing up (you can think of y as height).

Note

Some 3D applications and game engines use the z axis for height and the x and y axes for the ground plane, so when importing assets you might have to adjust (rotate) them.

The viewpoint in 3D space is usually called the camera. Clicking the x, y, and z arrows of the multicolored Scene Gizmo in the upper right corner is a quick way of flipping the camera so that it faces along the respective axis. For example, clicking the y arrow gives you a top-down view of the scene (Figure [2-38](#A310332_2_En_2_Chapter.html#Fig38)), and the text under the Scene Gizmo says “Top.”

The camera here is not the same as the Camera GameObject in the scene that is used during the game, so you don’t have to worry about messing up the game while you’re looking around in the Scene View.

![A310332_2_En_2_Fig38_HTML.jpg](Images/A310332_2_En_2_Fig38_HTML.jpg)

Figure 2-38.

A side view in the Scene View

Clicking the box in the center of the Scene Gizmo toggles the camera projection between perspective, which renders objects smaller as they recede in the distance, and orthographic, which renders everything at their original size whether they are close or far. Perspective is more realistic and what you normally use in games, but orthographic is often more convenient when designing (hence its ubiquity in computer-aided design applications). The little graphic preceding the text under the Scene Gizmo indicates the current projection.

You can zoom in and out using the mouse scroll wheel or by selecting the Hand tool in the upper right toolbar of the Editor window and click-dragging the mouse while holding the Control key down. When the Hand tool is selected, you can also move the camera by click-dragging the view, and you can rotate (orbit) the camera by dragging the mouse while holding the Option (or Alt) key down, so you’re not restricted to just the axis camera angles, like in Figure [2-39](#A310332_2_En_2_Chapter.html#Fig39).

![A310332_2_En_2_Fig39_HTML.jpg](Images/A310332_2_En_2_Fig39_HTML.jpg)

Figure 2-39.

A tilted perspective in the Scene View

Notice that when you’re looking from an arbitrary angle, the text under the Scene Gizmo says Persp or Iso, depending on whether you’re using perspective or orthographic projection (Iso is short for isometric, which is the tilted orthographic view common in games like Starcraft and Farmville).

The other buttons on the toolbar activate modes for moving, rotating, and scaling GameObjects. There’s no reason to change the Climber scene, so those modes will be explained in more detail when you start creating new projects.

Tip

If you accidentally make a change to the scene, you can select Undo from the Edit menu. If you made a lot of changes you don’t want to keep, you can just decline to save this scene when you switch to another scene or exit Unity. In the meantime, note that you can still move the camera while in those modes, using alternate keyboard and mouse combinations. Table [2-1](#A310332_2_En_2_Chapter.html#Tab1) lists all the possible options.

Table 2-1.

Available Scene View Camera Controls

| Action | Hand tool | 1-button mouse or trackpad | 2-button mouse | 3-button mouse |
| --- | --- | --- | --- | --- |
| Move | Click-drag | Hold Alt-Command and click-drag | Hold Alt-Control and click-drag | Hold Alt and middle click-drag |
| Orbit | Hold Alt and click-drag | Hold Alt and click-drag | Hold Alt and click-drag | Hold Alt and click-drag |
| Zoom | Hold Control and click-drag | Hold Control and click-drag or two-finger swipe | Hold Alt and right-click-drag | Hold Alt and right-click drag or scroll wheel |

There are a couple of other handy keyboard-based scene navigation features. Pressing the Arrow keys will move the camera forward, back, left, and right along the x–z plane (the ground plane). And holding the right mouse button down allows navigation of the scene as in a first-person game. The AWSD keys move left, forward, right, and back, respectively, and moving the mouse controls where the camera (viewpoint) looks .

When you want to look at a particular GameObject in the Scene View, sometimes the quickest way to do that is to select the GameObject in the Hierarchy view, then use the Frame Selected menu item in the Edit menu (note the handy shortcut key F). In Figure [2-40](#A310332_2_En_2_Chapter.html#Fig40), I clicked on the x axis of the Scene Gizmo to get a horizontal view, then selected the Player GameObject in the Hierarchy View, and pressed the F key (shortcut for Frame Selected in the Edit menu) to zoom in on and center the player in the Scene View.

You can also select a GameObject directly in the Scene View, but you have to exit the Hand tool first. Just as selecting a GameObject in the Hierarchy View will result in that selection displaying in the Scene View and Inspector View, selecting a GameObject in the Scene View will likewise display that selection in the Inspector View and display it as the selected GameObject back in the Hierarchy view. In Figure [2-40](#A310332_2_En_2_Chapter.html#Fig40), after I invoke Frame Selected on the Player, I clicked the Move tool (the button directly right of the Hand tool button in the top right corner of the Editor window) and then clicked a GameObject near the Player in the Scene View. The Hierarchy View automatically updates to show that GameObject is selected, and the GameObject is also displayed in the Inspector View.

![A310332_2_En_2_Fig40_HTML.jpg](Images/A310332_2_En_2_Fig40_HTML.jpg)

Figure 2-40.

Selecting a GameObject in the Scene View

### Scene View Options

The buttons lining the top of the Scene View provide display options to assist in your game development. Each button configures a view mode.

The leftmost button sets the Draw mode. Normally, this mode is set to Textured, but if you want to see all the polygons, you can set it to Wireframe. Because Climber is a 2d Game, the Wireframe view will not show much relevant information (Figure [2-41](#A310332_2_En_2_Chapter.html#Fig41)).

![A310332_2_En_2_Fig41_HTML.jpg](Images/A310332_2_En_2_Fig41_HTML.jpg)

Figure 2-41.

Wireframe display in the Scene view

The next button sets the Render Paths, which controls whether the scene is colored normally or for diagnostics.

The three buttons to the right of the Render Paths mode button are simple toggle buttons. They each pop up some mouse-over documentation (otherwise known as tooltips) when you let the mouse hover over them.

The first of those controls the Scene Lighting mode. This toggles between using a default lighting scheme in the Scene View or the actual lights you’ve placed in the game.

The middle button toggles the Game Overlay mode, whether the sky, lens flare, and fog effects are visible.

And finally, there is the Audition Mode, which toggles sound on and off.

### Scene View Gizmos

The Gizmos button on the right activates displays of diagnostic graphics associated with the Components. The Scene View in Figure [2-42](#A310332_2_En_2_Chapter.html#Fig42) shows several gizmos. By clicking the Gizmos button and checking the list of available gizmos, you can see those icons represent a Camera, a couple of AudioSources, and a few Lights.

![A310332_2_En_2_Fig42_HTML.jpg](Images/A310332_2_En_2_Fig42_HTML.jpg)

Figure 2-42.

Gizmos in the Scene View

You can select and deselect the various check boxes in the Gizmos window to focus on the objects you’re interested in. The check box at the top left toggles between a 3D display of the gizmos or just 2D icons. The adjacent slider controls the scale of the gizmos (so a quick way to hide all gizmos is to drag the scale slider all the way to the left).

## The Game View

Now let’s go back to the Game View, which you encountered when playing Climber in the Editor. Like the Hierarchy View and Scene View, the Game View depicts the current scene, but not for editing purposes. Instead, the Game View is intended for playing and debugging the game.

The Game View appears automatically when you click the Play button at the top of the Unity Editor window. If there isn’t an existing Game View when you click Play, a new one is created. If the Game view is visible while the Editor is not in Play mode, it shows the game in its initial state (i.e., from the vantage of the initial Camera position).

The Game View shows how the game will look and function when you actually deploy it, but there may be discrepancies from how it will look and behave on the final build target. One possible difference is the size and aspect ratio of the Game View. This can be changed using the menu at the top left of the view. Figure [2-43](#A310332_2_En_2_Chapter.html#Fig43) shows what happens when you switch from the Free Aspect ratio , which adjusts to the dimensions of the view, to a 5:4 aspect ratio, which results in the scaling down the game display so that it fits within the area and maintains the chosen aspect ratio.

![A310332_2_En_2_Fig43_HTML.jpg](Images/A310332_2_En_2_Fig43_HTML.jpg)

Figure 2-43.

The Game view

### Maximize on Play

Clicking the Maximize on Play button will result in the Game view expanding to fill the entire Editor window when it is in the Play mode (Figure [2-44](#A310332_2_En_2_Chapter.html#Fig44)). If the view is detached from the Editor window, the button has no effect.

![A310332_2_En_2_Fig44_HTML.jpg](Images/A310332_2_En_2_Fig44_HTML.jpg)

Figure 2-44.

Game view with Maximize on Play

### Stats

The Stats button displays statistics about the scene (Figure [2-45](#A310332_2_En_2_Chapter.html#Fig45)) that update as the game runs.

![A310332_2_En_2_Fig45_HTML.jpg](Images/A310332_2_En_2_Fig45_HTML.jpg)

Figure 2-45.

Game view with Stats

### Game View Gizmos

The Gizmos button activates displays of diagnostic graphics associated with the Components. The Game View in Figure [2-46](#A310332_2_En_2_Chapter.html#Fig46) shows two icons that are gizmos for an Audio Source. The list to the right of the Gizmos button allows you to select which gizmos you want displayed.

![A310332_2_En_2_Fig46_HTML.jpg](Images/A310332_2_En_2_Fig46_HTML.jpg)

Figure 2-46.

Game View with Gizmos

Both the Game View and Scene View are both depictions of the current scene. A Unity project consists of one or more scenes, and the Unity Editor has one scene open at a time. Think of the project as a game and the scenes as levels (in fact, some Unity script functions that operate on scenes use “level” in their names). Unity GameObjects are made interesting by attaching Components, each of which provides some specific information or behavior. That’s where the Inspector View comes in. If you select a game object in the Hierarchy View or Scene View, the Inspector View will display its attached components.

## The Console View

The remaining view in all the preset layouts, the Console View, is easy to ignore but it’s pretty useful (Figure [2-47](#A310332_2_En_2_Chapter.html#Fig47)).

![A310332_2_En_2_Fig47_HTML.jpg](Images/A310332_2_En_2_Fig47_HTML.jpg)

Figure 2-47.

The Console view

Informational, warning and error messages appear in the Console View. Errors are in red, warnings in yellow, and informational messages in white. Selecting a message from the list displays it with more detail in the lower area. Also, the single-line area at the bottom of the Unity Editor displays the most recent Console message, so you can always see that a message has been logged even if the Console view is not visible.

Tip

Warning messages are easy to ignore, but you ignore them at your peril. They are there for a reason and usually indicate something has to be resolved. And if you let warnings accumulate, it’s difficult to notice when a really important warning shows up.

The Console can get cluttered pretty quickly. You can manage that clutter with the leftmost three buttons on top of the Console View. The Clear toggle button removes all the messages. The Collapse toggle button combines similar messages. The Clear on Play toggle will remove all messages each time the Editor enters Play mode.

The Error Pause button will cause the Editor to halt on an error message, specifically when a script calls a Log.LogError.

While operating in the Editor, log messages end up in the Editor log, while messages generated from a Unity-built executable are directed to the Player log. Selecting Open Player Log or Open Editor Log from the view menu (click the little icon at the top right of the Console View) will bring up those logs, either in a text file or in the Console app (Figure [2-48](#A310332_2_En_2_Chapter.html#Fig48)).

![A310332_2_En_2_Fig48_HTML.jpg](Images/A310332_2_En_2_Fig48_HTML.jpg)

Figure 2-48.

The Unity logs in the Mac Console app

## Explore Further

We’ve come to the end of this Unity tour and our play time with Climber demo. Beginning with the next chapter, you’ll be creating Unity projects from scratch to learn the game engine features. But this won’t be the last you’ll see of Climber, as we’ll return to that project in Chapters [10](../Text/A310332_2_En_10_Chapter.html) and [11](../Text/A310332_2_En_11_Chapter.html), repeating this process to introduce Unity iOS development. Until then, starting with the next chapter, you’ll be creating Unity projects from scratch and exploring general and mostly cross-platform Unity game engine features.

This is the first chapter that really starts using Unity. You haven’t yet started building your own scene (that will begin in Chapter [3](../Text/A310332_2_En_3_Chapter.html)), but you’ve utilized the sample Climber project installed with Unity to get familiar with the Unity Editor. From this point on, there are plenty of official Unity resources that expand on the topics I will be covering.

### Unity Manual

As you can see, there’s a lot of Unity user interface, and we’ve hardly covered it all. This is a good time to get serious about reading the Unity Manual, either from within the Unity Editor (the Welcome screen or the Help menu) or on the Unity web site ( [`http://unity3d.com/`](http://unity3d.com/) ) under the Learn tab in the “Documentation” section. The web version is pretty handy when you want to look something up or just read about Unity without having a Unity Editor running nearby.

Most of what was covered in this chapter matches topics in the Unity Basics section of the Unity Manual, particular the sections on “Learning the Interface,” “Customizing Your Workspace,” “Publishing Builds,” and “Unity Hotkeys” (although I think a better reference is just to check the keyboard shortcuts listed in the menus).

I did jump ahead into the Advanced section of the Unity Manual and touch on Unity’s support for version control. That’s covered more in depth with the Unity Manual’s page on “Using External Version Control with Unity.”

### Tutorials

Besides the “Documentation” section, the Learn tab on the Unity web site also includes a “Tutorials” section that features an extensive set of Beginning Editor videos. As the name implies, these videos provide an introduction to the Unity Editor, and in fact the set of videos cover much of what was discussed in this chapter, including descriptions of the most important views (the Game View, Scene View, Hierarchy View, Inspector View and Project View) and even the process of publishing a build.

### Version Control

Although I only discussed version control briefly, in the context of explaining how to remove meta files, that topic is worth a little more discussion, since a VCS is so important to software development (which you’ll realize the first time you lose your project or can’t remember what change you made that broke your game!). If you already have a favorite VCS, you may want to use it with Unity, and if you haven’t been using one, then you may want to consider it if only to keep old versions of your project around in case you need to roll back, with the ability to check differences between versions,

Among version control systems, Perforce is a popular commercial tool used in game studios, and Subversion (svn) has a long history as an open source option. These days, distributed version control systems like Git and Mercurial are trending. I use Mercurial on Bitbucket ( [`http://bitbucket.com/`](http://bitbucket.com/) ) for my internal projects and post public projects on GitHub, including the projects for this book ( [`www.apress.com/9781484231739`](http://www.apress.com/9781484231739) ).

To say Unity VCS support is product agnostic is really another way of saying Unity doesn’t have any particular version control system integrated into the Unity Editor. The meta files, and YAML scene files for Unity Pro users, simply provide better compatibility with text-oriented version control systems that are commonly used for source code. You still have to run the VCS operations yourself outside of Unity. You can find out more about YAML, by the way, on [`http://yaml.org/`](http://yaml.org/)

I find it convenient to use the Mac GitHub app provided on the GitHub web site and similarly Sourcetree for BitBucket, also available on that web site.

And as we mentioned while explaining the options for Version Control Mode in the Editor Preferences, Unity also offers a VCS designed specifically for Unity called the Unity Asset Server. It requires the purchase of a Unity Team License.

# 3. Making a Scene

Playing Climber was fun, but this book is about making games in Unity, not playing games in Unity! Finally, it’s time to get started in game development. It’s a long journey starting from nothing and ending up with a playable game, and this is the beginning. In this chapter, you’ll start with an empty scene in the Unity Editor and populate it with the basic building blocks of a 3D environment: models, textures, lights, a sky, and a camera.

One of the challenges facing a game developer, especially one on a limited budget, is how to obtain these building blocks. The Unity Editor provides some primitive models, and any image can be used as a texture (in the tradition of the Internet, I’ll provide you a picture of a cat as a sample texture). And, as mentioned in Chapter [1](../Text/A310332_2_En_1_Chapter.html), the Unity installation includes a set of Standard Assets that can be imported into your project at any time. All of that can only take you so far, though.

Fortunately, the folks at Unity Technologies recognized this need and created the Asset Store, an online marketplace of Unity-ready assets with its storefront integrated directly into the Unity Editor. It turns out that a lot of these assets are free, so we’ll take advantage of that fact and incorporate some of these free assets into the Unity project created in this chapter.

Each chapter in this book (with the exception of Chapters [10](../Text/A310332_2_En_10_Chapter.html) and [11](../Text/A310332_2_En_11_Chapter.html) where the Trash Cat project stages a brief comeback) builds upon the project from the previous chapter. So by the end of this book, the simple static scene created in this chapter will have evolved into an optimized bowling game with sound and physics, running on iOS with leaderboards, achievements and ads. The project created in this chapter, along with the projects for each consecutive chapter, is available on [`www.apress.com/9781484231739`](http://www.apress.com/9781484231739) but minus any assets from the Asset Store or Standard Packages.

Let’s get started!

## Create a New Project

If you still have the Unity Editor running from the previous chapter, create a new Unity project using the New Project item in the File menu (Figure [3-1](#A310332_2_En_3_Chapter.html#Fig1)).

![A310332_2_En_3_Fig1_HTML.jpg](Images/A310332_2_En_3_Fig1_HTML.jpg)

Figure 3-1.

The New Project menu item

The resulting Project Wizard (Figure [3-2](#A310332_2_En_3_Chapter.html#Fig2)) is the same as the one we used when selecting Open Project in the previous chapter, but now it has the Create New Project tab selected. If you’re starting up a fresh Unity session and are presented with the Project Wizard, you can just click this tab to create a new project instead of opening an existing one.

![A310332_2_En_3_Fig2_HTML.jpg](Images/A310332_2_En_3_Fig2_HTML.jpg)

Figure 3-2.

Creating a new project with the Project Wizard

The Project Wizard prompts for the file name and location of the new project, defaulting to your home folder and the name New Unity Project (if the folder already exists, a number is appended, e.g., New Unity Project 1, or if that also exists then New Unity Project 2, and so forth). The ellipse in the Location box brings up a file chooser so you can browse for a directory location instead of typing it in.

You can name the project as you please or just use the default for now. The Unity project itself doesn’t care about its name, so you can rename the project folder in the Finder later (just make sure you exit Unity or switch to another project first, to avoid wreaking havoc with your current session).

Tip

If you have a project that you want to keep around for a while, it’s a good idea to give it a meaningful name. More than once, I’ve discovered an old New Unity Project folder on my Mac and wondered if it was something important.

The Add Asset packages menu item when selected will list Asset Packages that we can immediately import into our new project You don’t need to select any right now, as you can always import what you need later.

The Unity Editor will now appear, showing a new and empty project (Figure [3-3](#A310332_2_En_3_Chapter.html#Fig3)). The Project View displays no assets (you can confirm in the Finder that the Assets folder in your project contains no files), and the current scene is an untitled and unsaved scene consisting only of a GameObject named Main Camera.

![A310332_2_En_3_Fig3_HTML.jpg](Images/A310332_2_En_3_Fig3_HTML.jpg)

Figure 3-3.

How a new Unity project looks in the Editor

The first thing you should do is save and name this scene. The Save Scene option is found in the File menu (Figure [3-4](#A310332_2_En_3_Chapter.html#Fig4)) and has the same Command+S shortcut that is conventional with application save operations (you could think of scenes edited and managed by Unity as analogous to documents edited and managed by a word processing application).

![A310332_2_En_3_Fig4_HTML.jpg](Images/A310332_2_En_3_Fig4_HTML.jpg)

Figure 3-4.

The Save Scene menu item

The resulting chooser will provide the project’s Assets folder as the default location, which is fine; the scene is an asset and part of the project, so the scene file has to go in the Assets folder. A default name is not provided, so you need to provide one. Let’s call it cube, since this scene will just showcase a cube. The new scene will show up in the Project View, with a Unity icon (Figure [3-5](#A310332_2_En_3_Chapter.html#Fig5)).

![A310332_2_En_3_Fig5_HTML.jpg](Images/A310332_2_En_3_Fig5_HTML.jpg)

Figure 3-5.

The new scene in the Project View

When you exit Unity or switch scenes or projects, Unity will prompt you to save the scene if there are unsaved changes (sometimes those changes are innocuous user interface changes, so don’t be alarmed). But it’s a good practice to save frequently, in case there’s a crash.

The File menu also has a Save Scene As command that is similar to the Save As command of other applications. It lets you save the scene under a new name at any time, effectively making a copy of the scene.

Tip

Instead of using the Save Scene As command, it’s often simpler to select the scene file in the Project View and either rename (press Return) or duplicate (Command+D) the scene in the Project View.

You might be wondering about the difference between Save Scene and Save Project in the File menu. Save Project saves any changes to the project but not the current scene, while Save Scene also saves any changes in the current scene, i.e., changes to the GameObjects in the scene or changes to per-scene settings (RenderSettings). Save Scene is what you’ll want to use most of the time. Save Project is appropriate when you’ve made some changes to project settings or assets that you want to save but have scene changes you’re not ready to save or maybe even have a new scene open that you don’t want to save at all.

## The Main Camera

The Hierarchy View of the new scene lists just one GameObject, named Main Camera . Every new scene starts with that Camera GameObject, which acts as our eyes in the scene when we play the game. The Main Camera is special in that it’s easily accessible by scripts (it’s referenced by the variable Camera.main).

### Multiple Cameras

The fact that the camera’s name is Main Camera might lead you to believe that multiple Cameras can exist in a scene, and you would be correct. It might seem strange to have multiple simultaneous viewpoints, but multiple Cameras make it possible to have a split-screen display or a window within the screen. Those usages aren’t actually supported in Unity for iOS (see the section “Viewport” below), but multiple Cameras are still useful for situations like rendering the game world with the Main Camera and rendering a user interface with another, effectively overlaying one set of GameObjects over another.

### Anatomy of a Camera

Let’s select the Main Camera so we can see in the Inspector view what a Camera is made of (Figure [3-6](#A310332_2_En_3_Chapter.html#Fig6)).

![A310332_2_En_3_Fig6_HTML.jpg](Images/A310332_2_En_3_Fig6_HTML.jpg)

Figure 3-6.

Inspector View of the Main Camera

Starting from the top, you can see the name Main Camera. The check box to the left specifies whether this GameObject is active or inactive. If you uncheck the check box, the Camera is deactivated, indicated by a grayed-out appearance in the Hierarchy View, and you won’t see anything in the Game View. You can check the check box labeled Static on the right if you know this GameObject will never move. Marking GameObjects as static can facilitate optimization, although that rarely applies to Cameras.

Below the name, you can see this GameObject has MainCamera as its tag, which is really what designates this GameObject as a Main Camera. The name is just for display purposes, so you could edit the name here in the Inspector View without any repercussions.

The Layer menu for this GameObject is on the right. While names are used for description and tags for identification, layers are used to group GameObjects. You can think of these layers sort of like Photoshop layers. This Layer menu should not be confused with the Layers menu at the top right of the Editor window (they are unfortunately close to each other in the Default layout). The Layers menu at the top right controls which GameObjects are visible in the Scene View (it really should be located in the Scene View).

### The Transform Component

Like every other GameObject, the Main Camera has a Transform Component that specifies the position, rotation, and scale of the GameObject. Scale is meaningless for Cameras, but the position and rotation allow you to place and point the Camera.

Click the little question mark icon on the top right of the Component. That will bring up the Reference Manual documentation for the Transform Component in a web browser window.

Tip

Every time you encounter a new Component, the first thing you should do is check the documentation.

The Reference Manual page for each Component describes the properties of the Component and explains the usage of the Component. Each Reference Manual page also features a link to the corresponding Script Reference page for the Component, detailing how to access the Component from within a script.

### The Camera Component

The Camera Component is what makes this GameObject a Camera. To be perfectly precise, Main Camera is a GameObject that has a Component which specifically is a subclass of Component called Camera. But it’s convenient to say Main Camera is a Camera (since that is its function), and say Camera Component to make clear that we’re talking about the Component.

Again, clicking on the question mark icon of this Component will bring up the corresponding Reference manual page, listing the Camera Component properties. But I’ll go through them briefly here, in top-down order as they’re displayed in the Inspector View.

#### Clear Flags

The Clear Flags property specifies how the Camera initializes the color and depth buffers at the beginning of each rendering pass. The color buffer contains the pixels the Camera renders, and the depth buffer holds the distance from the Camera of each pixel (this is how it determines if a newly drawn pixel should replace the previous pixel at that location).

Solid Color indicates the color buffer will be cleared to the specified color, essentially using it as a background color, and the depth buffer will be cleared.

Skybox also clears the depth buffer and renders the specified Skybox textures in the background (defaulting to Solid Color if no Skybox is supplied).

Depth Only leaves the color buffer alone and only clears the depth buffer. This is useful when adding a Camera that renders after the Main Camera and over its results, e.g., to render a user interface or HUD (heads-up display) over the rendering of the game world. In that case, you don’t want the second Camera to clear the screen before rendering.

#### Culling Mask

This is where the usefulness of Layers becomes apparent. The Culling Mask specifies which Layers are rendered by the Camera. In other words, a GameObject will only be visible to the Camera if its Layer is included in the Camera’s Culling Mask.

The default, Everything, corresponds to all Layers, but, for example, if you had an HUD Camera, you might define an HUD Layer for all your HUD GameObjects and set the Culling Mask for the HUD Camera to only render the HUD Layer. And then you would set the Culling Mask of the Main Camera to include everything except the HUD Layer.

#### Projection

By default, the Camera is set to use a perspective projection, which as I mentioned in describing the Scene View in the previous chapter, means objects are smaller as they recede from the Camera (the effect is called foreshortening). In this mode, the Camera has a viewing volume resembling a four-sided pyramid (not counting the base of the pyramid) emanating from the Camera position in the view direction. This viewing volume is called a frustum and is defined by a Field of View (FOV) , near and far plane.

The Field of View is analogous to the FOV of a real-world camera. The FOV is the vertical viewing angle, in degrees. The horizontal viewing angle is implicitly defined by the combination of the FOV and the aspect ratio of the Camera. The near and far planes specify how far the screen is in front of the camera and how far away the base of the frustum is. Nothing outside the frustum is visible to the Camera.

To get a better idea of how the frustum looks, take a look at the Scene View. Figure [3-7](#A310332_2_En_3_Chapter.html#Fig7) shows how the frustum of the Main Camera looks when you press the y-axis arrow on the Scene Gizmo, changing the vantage to a top-down view. Press the F key (shortcut for the Frame Selected command in the Edit menu) to center the Main Camera in the Scene view, then zoom out until you can see the entire frustum. You should also uncheck the 3D Gizmos check box in the Gizmos menu so that the Camera icon doesn’t shrink to nothingness when you zoom out.

The Scene View also displays a Camera Preview in the lower right corner. This is what the Camera would render right now, which is also what the Game View would display right now (you can take a look at the Game View right now to confirm). There’s nothing visible in this scene yet, but this will be a handy feature later.

![A310332_2_En_3_Fig7_HTML.jpg](Images/A310332_2_En_3_Fig7_HTML.jpg)

Figure 3-7.

The Camera frustum in the Scene View

Note that the frustum outline and the Camera Preview only display when the Camera is selected and the Camera Component in the Inspector view is open (every Component in the Inspector view can be opened or closed by clicking the little triangle in the upper left). This is generally true of any Component that has an extra graphic display in the Scene View. Opening and closing the Component in the Inspector View will toggle that display.

The alternative to perspective is orthographic, which has no foreshortening and thus is appropriate for 2D games, isometric games user interfaces, and HUDs. The only Camera property specific to orthographic mode is the orthographic projection size, which is the half-height of the orthographic view in world units .

#### Viewport

The viewport of (0,0,1,1) indicates the Camera covers the whole screen. The numbers are normalized screen coordinates, meaning the coordinates across the width and height of the screen range from 0 to 1\. For a split screen or a window within the screen, you would have multiple Cameras, each with different viewports, (0,0,1,.5) covers half of the screen. However, Unity iOS doesn’t support viewports that don’t cover the full screen.

#### Depth

Depth is another property useful when there are multiple Cameras in the scene. During each rendering update, the Cameras will perform their rendering passes in the order of their depth values, lowest to highest. So if you had a second Camera for viewing an HUD, for example, you might specify that the Main Camera has a depth of 0 and the HUD Camera a depth of 1 to ensure the game world renders first and the game HUD renders on top of that.

#### Rendering Path

The rendering path determines how the scene is rendered. Each Camera can have its own rendering or default to the one specified in the Player Settings. Vertex Lit is the simplest but fastest path, Forward Rendering supports more advanced graphics, and Deferred is the fanciest but slowest and not available in Unity iOS.

#### Target Texture

If a texture is assigned to this property, the camera renders to that texture instead of the screen. The texture can then be placed in the scene, for example, as a television display or the rear-view mirror in a car.

#### HDR

This property enables High Dynamic Range (HDR) , which handles pixels with RGB components outside the normal 0-to-1 range and facilitates rendering of extremely high-contrast scenes. HDR is not available in Unity iOS.

### FlareLayer Component

When a Camera GameObject is created from the GameObject menu, both a Camera Component and a FlareLayer Component are automatically attached. The FlareLayer Component allows the Camera to render Light Flares (described later in this chapter).

The FlayerLayer Component is a little unusual in that it’s not exposed to scripting, so it does have a Reference Manual page, but no Script Reference page. However, a FlareLayer Component can still be accessed from a GameObject using its class name, i.e., by calling GetComponent(“FlareLayer”).

### GUILayer Component

A GUILayer Component is also automatically attached to a Camera GameObject. This component allows the Camera to render GUIText and GUITextures, which are 2D elements often used for user interfaces.

Note

GUILayer, GUIText and GUITexture (and GUIElement, which is the parent class of GUIText and GUITexture) have nothing to do with UnityGUI, which is a newer built-in GUI system.

You may have noticed that the Inspector View displays GUILayer as one word and Flare Layer as two words. The Editor attempts to present class names in a more English-like fashion by introducing spaces in the names where an upper-case letter follows a lower-case one. That works fine for FlareLayer but not GUILayer. In any case, this book will stick to the original class name, since that is what will be referenced in scripts.

### AudioListener Component

The AudioListener Component is only automatically attached to the Main Camera, since there can only be one active AudioListener at a time. Whereas the Camera Component is analogous to your eyes, responsible for rendering everything visible to it on your computer screen, the AudioListener Component is responsible for routing all the audible AudioSources in the scene to the computer speakers.

## Add a Cube to the Scene

You’ll already have noticed the Camera Preview and Game view display nothing, and the same happens in the Game view when you click the Play button. Of course, that’s because you don’t have anything in the scene besides the Camera. Let’s rectify that with the time-honored tradition of creating a cube.

### Make the Cube

Under the Create Other submenu of the GameObject menu, Unity provides a number of GameObjects bundled with Components. Among these is a Camera GameObject, if we wanted to add another one. There is also a Cube GameObject along with a number of other primitive shapes (Figure [3-8](#A310332_2_En_3_Chapter.html#Fig8)).

![A310332_2_En_3_Fig8_HTML.jpg](Images/A310332_2_En_3_Fig8_HTML.jpg)

Figure 3-8.

Creating a Cube from the GameObject menu

Select the Cube item from the GameObject menu. That will create a Cube GameObject and add it to the current scene. The Cube will appear in the Hierarchy View (Figure [3-9](#A310332_2_En_3_Chapter.html#Fig9)) as the second GameObject of the scene.

![A310332_2_En_3_Fig9_HTML.jpg](Images/A310332_2_En_3_Fig9_HTML.jpg)

Figure 3-9.

New cube GameObject in the Hierarchy View

### Frame the Cube

Although the Cube is in the scene, it may not be immediately visible in the Scene View. To ensure the Cube can be seen in the Scene View, select the Cube in the Hierarchy View and press the F key (shortcut for the Frame Selected command in the Edit menu), which will center the Cube in the Scene View (Figure [3-10](#A310332_2_En_3_Chapter.html#Fig10)).

![A310332_2_En_3_Fig10_HTML.jpg](Images/A310332_2_En_3_Fig10_HTML.jpg)

Figure 3-10.

The Cube in the Scene View

### Move the Cube

Remember, the Frame Selected command moves the Scene View camera, not the GameObject you’re looking at. The Cube may have been created at an arbitrary position, so set its position to the world origin, (0,0,0) in the Inspector View, by typing those coordinates into the x,y,z Position fields of the cube’s Transform (Figure [3-11](#A310332_2_En_3_Chapter.html#Fig11)). Don’t worry if you make a mistake. You can always Undo and Redo changes like this with the Undo and Redo commands in the Edit menu. In this case, you can Undo the Position change and then Redo it.

![A310332_2_En_3_Fig11_HTML.jpg](Images/A310332_2_En_3_Fig11_HTML.jpg)

Figure 3-11.

The Inspector View of a cube

### Anatomy of a Cube

While you have the Cube displayed in the Inspector View, take a look at what it’s made of. Or, in other words, see what Components of the Cube make it a Cube.

#### Transform Component

Of course, as with every GameObject, there’s a Transform Component, which provides the position, rotation, and scale of the Cube.

#### MeshFilter Component

The MeshFilter Component references the 3D model, known in Unity as a Mesh, which consists of triangles and per-vertex information such as texture coordinates. When not referencing a built-in Mesh like the Cube, this property is assigned a Mesh from the project assets.

#### MeshRenderer Component

Hand-in-hand with the MeshFilter is the MeshRenderer Component, which determines how the mesh is rendered and is largely dictated by the Material (or list of Materials, if the Mesh is split into submeshes). You can think of a Material like the fabric on a sofa—the Mesh provides the shape and the Material wraps around the Mesh to provide the surface appearance.

The MeshRenderer also controls whether the Mesh receives shadows or casts shadows (I’ll introduce shadows later in this chapter).

#### BoxCollider Component

The MeshFiler and MeshRenderer Components together only make the Cube visible. The BoxCollider Component provides a physical surface that is used for collisions in the Unity physics system. Since there’s nothing that can collide with the Cube in this scene, you don’t need this Component. There’s no harm in leaving it there, but you could also disable the Component by unchecking its check box. Or you could remove the Component entirely from the GameObject by right-clicking it and selecting Remove Component from the resulting menu (Figure [3-12](#A310332_2_En_3_Chapter.html#Fig12)).

![A310332_2_En_3_Fig12_HTML.jpg](Images/A310332_2_En_3_Fig12_HTML.jpg)

Figure 3-12.

Removing a Component

### Align With View

Since you moved the Cube, you can press the F key again to center it in the Scene View. But that doesn’t affect the Main Camera, as you can see from the Camera Preview in the Scene View and if you check the Game View.

Fortunately, there’s a convenient way to sync the Main Camera with the Scene View camera. Select the Main Camera, and then under the GameObject menu, invoke Align With View (Figure [3-13](#A310332_2_En_3_Chapter.html#Fig13)). Now the Main Camera has the same position and rotation as the Scene camera, and you can see in the Camera Preview that the Scene View and Game View are identical.

![A310332_2_En_3_Fig13_HTML.jpg](Images/A310332_2_En_3_Fig13_HTML.jpg)

Figure 3-13.

The Align With View command

## Camera Control

Now if you click the Play button, you can see the Cube, but there’s not much going on. The Cube just sits there, and there is no interactivity. The first thing I like to do with a scene that has just one object like this is add a Camera orbit script so I can inspect the object from all angles. Conveniently, Unity comes with several Camera control scripts in the Standard Assets, including a MouseOrbit script .

### Import the Script

To import that script, go to the Assets menu and select the Import Package submenu. This is the same list of Assets packages available when we created a new project (Figure [3-14](#A310332_2_En_3_Chapter.html#Fig14)).

![A310332_2_En_3_Fig14_HTML.jpg](Images/A310332_2_En_3_Fig14_HTML.jpg)

Figure 3-14.

Importing scripts from Standard Assets

From this list, selecting Utility will present a window displaying all the scripts in the Scripts package (Figure [3-15](#A310332_2_En_3_Chapter.html#Fig15)). The scripts ending in the .js extension are written in JavaScript and the ones ending in the .cs extension are written in C#. You can import just the MouseOrbit script or all of the scripts. It’s easy enough to delete unwanted assets later.

![A310332_2_En_3_Fig15_HTML.jpg](Images/A310332_2_En_3_Fig15_HTML.jpg)

Figure 3-15.

The available Standard Assets scripts

Now the scripts and their containing folders are displayed in the Project View (Figure [3-16](#A310332_2_En_3_Chapter.html#Fig16)). The Project View doesn’t list the .cs suffixes of the file names, but you can see from the icons that this is a C# script, and selecting a script will display the full name on the line at the bottom of the Project View.

![A310332_2_En_3_Fig16_HTML.jpg](Images/A310332_2_En_3_Fig16_HTML.jpg)

Figure 3-16.

Standard Assets scripts after import

If you select the C# script in the Project View, the code in that script shows up in the Inspector View (Figure [3-17](#A310332_2_En_3_Chapter.html#Fig17)).

![A310332_2_En_3_Fig17_HTML.jpg](Images/A310332_2_En_3_Fig17_HTML.jpg)

Figure 3-17.

The Inspector View of the SimpleMouseRotator script

### Attach the Script

Drag the SimpleMouseRotator script onto the Main Camera GameObject in the Hierarchy View. That attaches the script onto the GameObject, so now if you select the Main Camera, the SimpleMouseRotator script will show up as one of its Components (Figure [3-18](#A310332_2_En_3_Chapter.html#Fig18)).

![A310332_2_En_3_Fig18_HTML.jpg](Images/A310332_2_En_3_Fig18_HTML.jpg)

Figure 3-18.

The SimpleMouseRotator script attached to the Main Camera

The first property of the SimpleMouseRotator t Component is a reference to the SimpleMouseRotator script (notice how Unity pretty-prints the Component type as Mouse Rotator, but don’t be fooled, it’s SimpleMouseRotator!). You could actually change the script referenced by that property by dragging another one from the Project view into that field or by clicking the little circle on its right, which would pop up a list of available scripts to choose from (which right now are all the Standard Assets script you just imported).

The script itself determines the properties that follow. Notice that each of those properties in the SimpleMouseRotator Component corresponds to a public variable declared at the top of the SimpleMouseRotator (each line beginning with var is a public variable, while those starting with private var are private).

Tip

Selecting the Debug option in the Inspector view menu (top right corner) will display private variables as properties, too, which can be useful for debugging. Try it now and you’ll see the private variables x and y show up. The Debug option also adds diagnostic properties of built-in Components.

The first property defined by the script is Target, which is the GameObject the Camera will orbit around. It defaults to nothing (or in the code, null), so if you click Play, there is nothing for the Camera to orbit around. To rectify that, drag the Cube from the Hierarchy View into the Target field (Figure [3-19](#A310332_2_En_3_Chapter.html#Fig19)).

![A310332_2_En_3_Fig19_HTML.jpg](Images/A310332_2_En_3_Fig19_HTML.jpg)

Figure 3-19.

Assigning the Target property of the SimpleMouseRotator script

Now when you click the Play button, the Camera orbits the Cube as you move the mouse. While in Play mode, you can adjust the other properties until you find values you like. The Rotation Range property is the rotation range of the camera. Changing it to 3 dramatically limits Camera movement (Figure [3-20](#A310332_2_En_3_Chapter.html#Fig20)). The Rotation Speed property controls how fast the Camera orbits in relation to the mouse movement, and the Damping Time property sets amount of camera damping (limit the amount of camera shake).

When you exit Play mode, the properties revert to the values they had before you entered Play mode. At that point it’s necessary to enter the desired values again. If you end up with property values that you don’t like, you can always start over by clicking the icon on the top right of the Component and selecting Reset to revert back to the default property values.

![A310332_2_En_3_Fig20_HTML.jpg](Images/A310332_2_En_3_Fig20_HTML.jpg)

Figure 3-20.

Testing the SimpleMouseRotator script

## Add a Light

You’ve probably been thinking the Cube looks awfully dark. Well, let’s add a Light! Under the Create Other submenu of the GameObject menu, several types of Lights are listed (Figure [3-21](#A310332_2_En_3_Chapter.html#Fig21)). Let’s choose Point Light .

![A310332_2_En_3_Fig21_HTML.jpg](Images/A310332_2_En_3_Fig21_HTML.jpg)

Figure 3-21.

Creating a Point Light

The Point Light now appears in the Hierarchy View and the Scene View (Figure [3-22](#A310332_2_En_3_Chapter.html#Fig22)), and you can see that its distinguishing component is a Light Component, with its type set to Point. A Point Light only lights objects within its radius. So you should adjust its position or radius so that the cube will be lit. Here you can set its position to 5,5,5 (remember, the cube is at 0,0,0) and set the radius to 10\.

![A310332_2_En_3_Fig22_HTML.jpg](Images/A310332_2_En_3_Fig22_HTML.jpg)

Figure 3-22.

The Inspector View of a Point Light

### Anatomy of a Light

In the same way that a GameObject becomes a Camera when it has a Camera Component attached, a GameObject behaves as a Light when it has a Light Component attached. Like the Camera Component, the Light Component has several properties, which we’ll go over here in top-down order as shown in the Inspector View.

#### Type

The Type of a Light specifies whether it is a Point Light, Directional Light, Spot Light, or Area Light. Since this GameObject was created as a Point Light, it’s Type was automatically set to Point. In contrast to a Directional Light, which acts as a light source infinitely far away (it’s sometimes called an infinite light), a Point Light has a position (and is thus often known as a positional light) and radiates in every direction. A Spot Light also has a position, but radiates in a certain direction, like a cone. An Area Light is only used in generating light maps (lighting that is precalculated and “baked” into textures).

#### Range

The Range property for Point Lights specifies the radius of the Light. Only objects within the radius are affected by the Light.

#### Color

The Color property is the color emitted by the Light. Clicking the little color rectangle brings up a color chooser to assign this property.

#### Intensity

The Light Intensity ranges from 0 to 1, where 0 is unlit and 1 is maximum brightness.

#### Shadow Type

The Shadow Type property of a Light specifies whether it the Light projects shadows, and if so, whether they are Hard Shadows or Soft Shadows.

#### Cookie

Directional and Spot Lights can project a cookie texture onto a surface, typical to simulate a shadow.

#### Culling Mask

The Culling Mask property specifies the layers that the Light will affect. Very much like how the Culling Mask of the Camera Component dictates what objects are visible to the Camera, the Culling Mask of the Light dictates what objects are lit by a Light.

#### Flare

The Flare property, when assigned a Flare asset, produces a lens flare effect emanating from this Light.

#### Draw Halo

The Draw Halo property produces a halo effect around the position of the Light. When this property is enabled, an additional property for the halo color is made available.

#### Render Mode

The Render Mode of a Light specifies whether the Light is Important, Not Important, or Auto, which means its importance is determined automatically, based on the Light’s brightness and the current Quality Settings. A Light’s importance determines whether it is applied as a pixel light or not. Pixel lights are required for effects like shadows and cookies.

#### Lightmapping

The Lightmapping property determines whether this Light is used for dynamic lighting, generating lightmaps, or both. You won’t be using lightmaps for these examples (they take a long time to generate), so RealtimeOnly or Auto will be fine for these Lights.

### Adjust the Light

As with Cameras, when you have the Point Light selected and its Light Component open in the Inspector, more information about the Light is depicted in the Scene View. For a Point Light, the radius is displayed, so you can see what objects are affected by the Light (Figure [3-23](#A310332_2_En_3_Chapter.html#Fig23)). In order for the Cube to be affected by the Point Light, it must be within the Light’s radius. But remember, a Point Light radiates outward from its position, so you can’t have the Point Light at the same position as the Cube, because then the Light would be radiating from within the Cube and wouldn’t illuminate the Cube’s outer surface.

You can adjust the Light properties solely in the Inspector View, but this try using the Scene View. Click the Move button in the button bar on the top left of the Editor (it’s the button with arrows directly right of the Hand tool). Now with the Point Light selected, the Scene View displays arrows centered at the Light that correspond to the x, y, and z axes of the Light. Click-dragging on any of those arrows will move the Light along the corresponding axis .

![A310332_2_En_3_Fig23_HTML.jpg](Images/A310332_2_En_3_Fig23_HTML.jpg)

Figure 3-23.

The Scene View of a Point Light

The circles around the Light delineate the range of the Light. You can adjust the range by click-dragging the little yellow rectangles just inside the circles. Anything within the range is lit by the Light, and anything outside the range won’t be affected by the Light.

Warning

An object in exactly the same position as a Point Light won’t be affected by that Light.

Go ahead and move the Light and adjust its radius as necessary to make sure the Cube is lit. Now when you click the Play button, the Cube is lit, and the lighting varies as you orbit the Camera around the Cube.

### Make a Halo

Right now the Point Light itself is invisible when you’re in Play mode. The Light’s presence is only evident through the GameObjects that it illuminates. However, you can see where the Light is coming from if you enable its halo. Enable the Draw Halo property in the Inspector View, and while you’re at it, click the Color property to change the Light’s color (Figure [3-24](#A310332_2_En_3_Chapter.html#Fig24)).

![A310332_2_En_3_Fig24_HTML.jpg](Images/A310332_2_En_3_Fig24_HTML.jpg)

Figure 3-24.

Selecting a halo color

This affects not only the color of the Light that is reflected off objects, but it’s also the color used to draw the halo. You can see the halo in the Scene View (Figure [3-25](#A310332_2_En_3_Chapter.html#Fig25)) and when you click Play (Figure [3-26](#A310332_2_En_3_Chapter.html#Fig26)).

![A310332_2_En_3_Fig26_HTML.jpg](Images/A310332_2_En_3_Fig26_HTML.jpg)

Figure 3-26.

The Game View of a Point Light with a halo

![A310332_2_En_3_Fig25_HTML.jpg](Images/A310332_2_En_3_Fig25_HTML.jpg)

Figure 3-25.

The Scene View of a Point Light with a halo

## Textures

### Import a Texture

Just about any image file (.jpeg, .png, .tiff, .psd) on your Mac can be imported as a texture by invoking the Import Asset command in the Assets menu (Figure [3-27](#A310332_2_En_3_Chapter.html#Fig27)) and selecting the image file in the resulting chooser.

![A310332_2_En_3_Fig27_HTML.jpg](Images/A310332_2_En_3_Fig27_HTML.jpg)

Figure 3-27.

Importing a new asset

For this example, I’ve chosen a cat picture from my Photos directory, where it was stored by iPhoto. Feel free to use this file by downloading it from [`www.apress.com/9781484231739`](http://www.apress.com/9781484231739) (it’s in this chapter’s project, under the Textures folder). But if you have any image on your Mac in one of the supported file formats, that will be fine. After importing the image file, the resulting texture will appear in the Project View (Figure [3-28](#A310332_2_En_3_Chapter.html#Fig28)).

![A310332_2_En_3_Fig28_HTML.jpg](Images/A310332_2_En_3_Fig28_HTML.jpg)

Figure 3-28.

The Project View of a newly imported texture

Select the texture and examine its import settings in the Inspector View (Figure [3-29](#A310332_2_En_3_Chapter.html#Fig29)). The default Texture Type, Texture, is the preset type that is generally suitable for textures applied to models. If there are unapplied Import Settings, the Apply button is enabled and should be clicked (or Unity will prompt you to when it tries to use the texture).

![A310332_2_En_3_Fig29_HTML.jpg](Images/A310332_2_En_3_Fig29_HTML.jpg)

Figure 3-29.

The Inspector View of an imported Texture

Notice how the Preview panel at the bottom summarizes the imported size and format of the Texture. The original image file has not been changed in any way, so importing the file effectively creates a duplicate of the original that is appropriate for our target platform. You never have to worry about messing up the original asset. In fact, you can always start over from it by right-clicking it in the Project View and selecting Reimport.

Note

We’ve been using the term texture generically and without capitalization. There actually is a Texture class, that is the parent class of all types of textures, but the actual class name of the texture asset created by importing an image file is Texture2D (the other subclasses of Texture include Texture3D, MovieTexture and RenderTexture).

To apply the texture to the Cube , drag the texture onto the Cube in the Hierarchy View. This automatically creates a Material using the texture and applies that Material to the Cube, replacing the previous Material that was there. (Figure [3-30](#A310332_2_En_3_Chapter.html#Fig30)).

![A310332_2_En_3_Fig30_HTML.jpg](Images/A310332_2_En_3_Fig30_HTML.jpg)

Figure 3-30.

A texture applied to the Cube

In the Project View, you can now see the newly created Material is placed in a Materials folder located by the texture. Now if you click Play again, the Cube looks cool! (Figure [3-31](#A310332_2_En_3_Chapter.html#Fig31)).

![A310332_2_En_3_Fig31_HTML.jpg](Images/A310332_2_En_3_Fig31_HTML.jpg)

Figure 3-31.

The Game View of the textured Cube

### Shop the Asset Store

Unity doesn’t have many built-in textures in Standard Assets (although there are many textures used in the various packages). However, the Unity Asset Store, a marketplace of Unity-ready assets, has several free texture packs. Conveniently, the Asset Store is integrated into the Unity Editor. Under the Window menu (Figure [3-32](#A310332_2_En_3_Chapter.html#Fig32)), select the Asset Store item.

![A310332_2_En_3_Fig32_HTML.jpg](Images/A310332_2_En_3_Fig32_HTML.jpg)

Figure 3-32.

Selecting the Asset Store from the Window menu

This will bring up the Asset Store window and display the Asset Store front page (Figure [3-33](#A310332_2_En_3_Chapter.html#Fig33)), which lists the latest releases (both paid and free), categories, and featured items.

![A310332_2_En_3_Fig33_HTML.jpg](Images/A310332_2_En_3_Fig33_HTML.jpg)

Figure 3-33.

The Unity Asset Store

Let’s look for some free textures. Click the Textures & Materials category in the upper-right section of the page, and then select Free in the Maximum Price on the left (Figure [3-34](#A310332_2_En_3_Chapter.html#Fig34)).

![A310332_2_En_3_Fig34_HTML.jpg](Images/A310332_2_En_3_Fig34_HTML.jpg)

Figure 3-34.

Free textures on the Asset Store

There are plenty of good candidates here, but if you scroll down, you should be able find to my favorite free texture library, the Free ArtskillZ Texture Pack. Click it to see the full product page (Figure [3-35](#A310332_2_En_3_Chapter.html#Fig35)). It includes the product description, screenshots, list of the individual files, and reviews.

![A310332_2_En_3_Fig35_HTML.jpg](Images/A310332_2_En_3_Fig35_HTML.jpg)

Figure 3-35.

The ArtskillZ Texture Pack on the Asset Store

### Import the Texture

Click the Download button, and you’ll see the same type of Import window that you saw when importing the built-in Unity assets. Go ahead and import everything.

Now you’ll find all those textures in your Project View, separated into three folders categorizing them by genre (Figure [3-36](#A310332_2_En_3_Chapter.html#Fig36)).

![A310332_2_En_3_Fig36_HTML.jpg](Images/A310332_2_En_3_Fig36_HTML.jpg)

Figure 3-36.

The Project View of the ArtskillZ Textures

The textures displayed with blue icons are normal maps and are listed as such in their Texture Type fields. A normal map gives the illusion of a nonflat surface (sometimes the term bump map is used, although it’s not technically exactly the same).

### Apply the Texture

For starters, we’ll try the ScifiPanel01 Texture. Drag it to the Cube and Unity automatically creates a new Material named after the texture and applies that Material to the Cube. But we want to use the normal map that accompanies that texture, so use the Shader selector of the Material in the Inspector view to change the shader to Legacy Shaders Bumped Specular. This shader accepts lists a second texture labelled Normalmap and also has a slider to control the Shininess property (it should be noted that the slider moves left to approach 1 and right to approach 0).Drag the normal map texture, ScifiPanel01_n, into it the Normalmap field (Figure [3-37](#A310332_2_En_3_Chapter.html#Fig37)).

![A310332_2_En_3_Fig37_HTML.jpg](Images/A310332_2_En_3_Fig37_HTML.jpg)

Figure 3-37.

Scifi Texture applied to the Cube

Click Play, and you’ll now see the textured Cube (Figure [3-38](#A310332_2_En_3_Chapter.html#Fig38)).

![A310332_2_En_3_Fig38_HTML.jpg](Images/A310332_2_En_3_Fig38_HTML.jpg)

Figure 3-38.

The Game View of the Cube with a specular bump map texture

You could have also searched for free textures using the search field in the Project View. I’ll use that option in the next chapters as we make use of more Asset Store packages.

## Explore Further

This chapter may have seemed like it covered a lot of material (no pun intended) just to create a simple static scene, but a lot of basic Unity concepts were explored along the way, particularly the relationship between Components and GameObjects. As a result, now you have a scene with a bump-mapped and textured Cube, and is illuminated by a Point Light that’s sporting a Flare. The scene was populated with assets imported from local files, the Unity Standard Assets, and the Unity Asset Store. And the scene isn’t completely static due to the SimpleMouseRotator script attached to the Main Camera. Scripting will play a dominant role in this book, starting with the next chapter as we move beyond a static scene.

Before moving on to that, take a break and go over the official Unity documentation on the features used so far, if only to get familiar with where to find this documentation for future reference.

### Unity Manual

The Unity Basics section of the Unity Manual was mostly relevant to the previous chapter, describing the user interface, but that section does have a “Creating Scenes” page that More relevant is the “Building a Scene” section, which describes the relationship among GameObjects, Components, and scripts, how to use the Inspector View to edit Component properties, and how to navigate the Scene View and move GameObjects, all of which was covered in this chapter. Lights and Cameras are also explained. One feature listed in this section that won’t be used in this book is the Terrain Engine (it is available in Unity iOS but slow). Terrain is a significant and impressive Unity feature, though, so it’s worth a read the “Asset Import and Creation” section describes the asset types used in this chapter: Meshes, Materials, textures and scripts. The process of importing assets and the Asset Store are also explained.

We haven’t arrived at a point where we have gameplay, but the “Creating GamePlay” section has a page on “Transforms,” which is we saw is fundamental to understand even just for placing static GameObjects.

### Reference Manual

Getting more in depth than the Unity Manual, the Reference Manual describes each Component in detail, explaining its properties and documenting the use of the Component. The Reference Manual also documents the asset types and gives an explanation of GameObject.

As a rule, you should read the Reference Manual documentation for every Component and asset type you use. Remember, there is a Help icon on each Component in the Inspector view that will bring up the appropriate Reference Manual page when you click it. Like the Unity Manual, the Reference Manual is available not just from the Unity Help menu but also on the Unity web site under the Learn tab (which can be reached directly at [`http://docs.unity3d.com/`](http://docs.unity3d.com/) ).The “Settings Manager” section of the Reference Manual includes a page on the Render Settings, which is where the Skybox in this chapter was assigned. The “Transform Components” section lists just one Component, naturally, the Transform Component, which is attached to very GameObject.

The “Mesh Components” section is more interesting, as it describes both the MeshFilter and MeshRenderer Components necessary for any GameObject with Mesh, such as the Cube created in this chapter. The “Rendering Components” section is more bountiful, yet, featuring the Camera Component and its associated GUILayer Component and FlareLayer. The Light Component is also documented here. Although assets aren’t really Components, the Reference Manual has a section titled “Asset Components,” which lists the various asset classes. In this chapter, Flare, Material, Mesh and Texture2D were incorporated into the scene (remember that Skybox is really a Material).

The most fun reading is in the section titled “Built-In Shader Guide,” which describes in detail (and with pictures) all of the built-in shaders, ranging from the simple and fast to very fancy. The Cube in this chapter started out with the default Diffuse shader and was replaced with a deluxe Specular Bump Map shader. As mentioned in this chapter, a shader is really a program, so if you can’t find a built-in shader that suits your needs, you can write your own by following the examples and instructions in the “Shader Reference” section.

### Asset Store

This chapter begins our book-long practice of using free assets from the Unity Asset Store. I recommend browsing the Asset Store regularly to check the latest releases. Besides the free textures, models, and scripts, there are plenty of reasonably priced packages that can save you a lot of time and work. The Asset Store can also be viewed with a regular web browser at [`http://assetstore.unity3d.com/`](http://assetstore.unity3d.com/) (but without the ability to purchase or download any assets).

### Computer Graphics

Computer graphics is a huge and intricate area of study. This chapter worked with 3D models, textures, bump maps, light flares, skyboxes, and we’re just getting started. So it’s well worth reading up on basic (and advanced) computer graphics, such as the popular and comprehensive text Real-Time Rendering, by Tomas Akenine-Möller, Eric Haines, and Naty Hoffman. Mark Haigh-Hutchinson’s Real-Time Cameras is an entire book devoted to the subject of virtual Cameras and their control schemes.

I won’t be covering the process of creating assets that can be imported into Unity, but Luke Ahearn has two books that may be generally helpful: 3D Game Textures: Create Professional Game Art Using Photoshop and 3D Game Environments: Create Professional 3D Game Worlds. And you can tell from the title of Wes McDermott’s Creating 3D Game Art for the iPhone with Unity that it’s an appropriate complementary text for this book.

# 4. Making It Move: Scripting the Cube

Now that you know how to construct a static 3D scene by placing GameObjects, it’s time to make the scene dynamic, and more interesting, by adding movement to GameObjects while the game is played. After all, a 3D graphics scene is nice, but an animated 3D graphics scene is even better!

But applying animation data isn’t the only way to move a GameObject. Making a GameObject physical will cause it to move in response to collisions and forces, including gravity . A script can also move a GameObject by changing the position and/or rotation values in its Transform component. In fact, any method for moving a GameObject, whether it’s animation, physics, or a script, ultimately changes the Transform component, which always represents the position, rotation, and scale of the GameObject.

This chapter will focus on moving GameObjects by scripting, demonstrating how to rotate the formerly static cube created in the previous chapter, and even introducing tween-style animations. In the process, I’ll present basic concepts and tools (editing and debugging) of the Unity scripting system.

The scripts you’ll be writing are available at [`www.apress.com/9781484231739`](http://www.apress.com/9781484231739) in the project for this chapter, but I recommend creating them from scratch. You have the best chance of understanding how the code works (and doesn’t work) when you type it in yourself.

## Organize the Assets

It’s easier to view and browse assets in the Project view when they’re partitioned by type . So, before you start adding scripts, this is a good time to get organized and create some folders for the scripts and textures in the project. Launch the Unity Editor, if you don’t already have it open from the previous chapter, and make sure you have the project from the previous chapter loaded and the cube scene open. Then click the Create button in the top left of the Project view (Figure [4-1](#A310332_2_En_4_Chapter.html#Fig1)) to bring up a menu of new assets that you can add to the project (alternatively, you can right-click in the Project view and select the Create submenu). Select the Folder item in the menu, and a folder titled New Folder will appear in the Project view. Rename the folder to Textures and drag any textures into it that you imported in the previous chapter (like my cat photo). Although you haven’t created any scripts yet, go ahead and create a Scripts folder, too.

![A310332_2_En_4_Fig1_HTML.jpg](Images/A310332_2_En_4_Fig1_HTML.jpg)

Figure 4-1.

Creating a Textures folder in the Project view

## Create the Script

Select the newly added Scripts folder and then, also from the Create menu of the Project view, select JavaScript (Figure [4-2](#A310332_2_En_4_Chapter.html#Fig2)). A script called New Behaviour (U.S. readers, you’ll just have to get used to the British spelling) will appear in the Scripts folder. If you accidentally created the new script file outside the Scripts folder, just drag the script into the folder .

![A310332_2_En_4_Fig2_HTML.jpg](Images/A310332_2_En_4_Fig2_HTML.jpg)

Figure 4-2.

Creating a JavaScript Note

Although the Create menu in the Project view lists “Javascript,” with just the first letter capitalized, I’ll pretend it says “JavaScript,” which is the official capitalization and is what is used in the Unity documentation (who knows, a Unity update may fix the menu spelling at any time). Also, this book will use the phrase “a JavaScript” to refer to a script, instead of the more correct but clumsier to say “a JavaScript script.”

Since this new file is a JavaScript, it has a `.js` extension. The Project view does not display file name extensions, but the language of the script is evident from its icon (Figure [4-3](#A310332_2_En_4_Chapter.html#Fig3)). And if the script is selected, its full name is shown in the line at the bottom of the Project view.

![A310332_2_En_4_Fig3_HTML.jpg](Images/A310332_2_En_4_Fig3_HTML.jpg)

Figure 4-3.

The Project view of the new script

### Name the Script

New Behaviour is not a very meaningful name for a script, so the first order of business is to give it a new name . An obvious choice is Rotate, since the eventual purpose of the script is to rotate the cube. But with overly general names, you can run into a conflict (Unity will report an error) if there are two scripts with the same name, even if they reside in different folders. This is because each script defines a new class (a subclass of MonoBehaviour, to be specific), and you can’t have two classes with the same name.

One approach is to name each script with a standard prefix (much like how Objective-C classes start with NS). This is a common technique adopted by third-party Unity user interface packages. If everyone names their button class Button, they can’t coexist!

Note

C# scripts have the option of partitioning classes among namespaces to avoid name clashes. This book uses JavaScript nearly all the way through, but examples of C# scripts and namespaces are provided in Chapter [17](https://doi.org/10.1007/978-1-4842-3174-6_17).

Sometimes I use the game name as a script name prefix, if the script is specific to the game (for example, in HyperBowl, I have a lot of scripts that start with Hyper). However, I usually prefix my script names with Fugu, corresponding to my game brand, Fugu Games. This book will stick with that convention, so go ahead and name the new script FuguRotate (Figure [4-3](#A310332_2_En_4_Chapter.html#Fig3)). Of course, in general, you’re free to choose your own script name convention .

### Anatomy of a Script

If you select the FuguRotate script in the Project view , the Inspector view displays the source code, or at least as much as will fit in the Inspector view display (Figure [4-4](#A310332_2_En_4_Chapter.html#Fig4)).

![A310332_2_En_4_Fig4_HTML.jpg](Images/A310332_2_En_4_Fig4_HTML.jpg)

Figure 4-4.

Inspector view of the new script

As the Inspector view shows, when Unity creates a new script , it includes three items (Listing [4-1](#A310332_2_En_4_Chapter.html#Par15)).

```
#pragma strict
function Start () {
}
function Update () {
}
Listing 4-1.
Contents of a New Script
```

The top line of the script, `#pragma strict`, instructs the script compiler that all variables have type declarations, or at least have types that are easily inferred by the compiler. This is not a requirement for Unity desktop builds, but it is required for Unity iOS, so you might as well make a habit of it.

Note

All the complete script listings in this book start with `#pragma strict`. Any code that doesn’t include that line is only an excerpt from a script.

The two functions in the script are callbacks , which are called by the Unity game engine at well-defined moments in the game. The Start function is called when this script is first enabled. A component is not completely enabled unless the GameObject it’s attached to is also active. So if a GameObject is initially active in a scene and it has an enabled script attached, then the Start function of that script is called when the scene starts playing. Otherwise, the Start function is not called until the script is enabled (by setting the enabled variable of the script component) and the GameObject is made active (by calling the SetActive function of the GameObject).

In contrast to the Start function, which is called at most once in the lifetime of a script component, the Update function is called every frame (i.e., once before each time Unity renders the current scene). Like the Start function, Update is called only when the script is (completely) enabled, and the first call to Update happens after the one and only call to Start. Thus, the Start function is useful for performing any initialization needed by the Update function.

### Attach the Script

Like other assets in the Project view, a script has no effect until you add it to the scene, specifically, as a component of a GameObject. Ultimately, you want the FuguRotate script to rotate the cube, so drag it from the Project view onto the cube GameObject in the Hierarchy view. The Inspector view now shows the script is attached to the cube as a component (Figure [4-5](#A310332_2_En_4_Chapter.html#Fig5)).

![A310332_2_En_4_Fig5_HTML.jpg](Images/A310332_2_En_4_Fig5_HTML.jpg)

Figure 4-5.

FuguRotate script attached to the cube

Unity often offers two, and sometimes three, ways to perform the same operation. For example, you could also have attached the FuguRotate script to the cube by clicking the Add Component button in the Inspector view of the cube and then selecting the FuguRotate script from the resulting menu. Or you could have dragged the FuguRotate script into the area below the Add Component button (but that’s difficult if there isn’t much space displayed below the button).

### Edit the Script

Now it’s time to fill out the script. Select the FuguRotate script in the Project view, and then in the Inspector view click the Open button to bring up the script editor . Double-clicking the script in the Project view, or selecting Open from the right-click menu on the script, will work, too. The default script editor for Unity is a customized version of MonoDevelop, a code editor and debugger tailored for Mono, the open source framework underlying Unity’s scripting system (Figure [4-6](#A310332_2_En_4_Chapter.html#Fig6)).

![A310332_2_En_4_Fig6_HTML.jpg](Images/A310332_2_En_4_Fig6_HTML.jpg)

Figure 4-6.

The MonoDevelop editor

If you prefer to use another script editor, you can change the default by bringing up the Unity Preferences window, selecting the External Tools tab, and then browsing for the application of your choice (Figure [4-7](#A310332_2_En_4_Chapter.html#Fig7)).

![A310332_2_En_4_Fig7_HTML.jpg](Images/A310332_2_En_4_Fig7_HTML.jpg)

Figure 4-7.

Choosing a different script editor

The FuguRotate script as it stands won’t do anything noticeable since the callback functions are empty, having no code between their brackets. Let’s start off with some tracing code in the Start and Update functions to demonstrate when those callbacks are invoked (Listing [4-2](#A310332_2_En_4_Chapter.html#Par25)).

```
function Start () {
Debug.Log("Start called on GameObject "+gameObject.name);
}
function Update () {
Debug.Log("Update called at time "+Time.time);
}
Listing 4-2.
FuguRotate.js with Calls to Debug.Log
```

One cool feature of Unity’s MonoDevelop is its code autocompletion. For example, Figure [4-8](#A310332_2_En_4_Chapter.html#Fig8) shows that as you type Time.t, MonoDevelop pops up a list of functions and variables that are members of the Time class and looks for one that starts with t.

![A310332_2_En_4_Fig8_HTML.jpg](Images/A310332_2_En_4_Fig8_HTML.jpg)

Figure 4-8.

Autocompletion in MonoDevelop

### Understand the Script

Now that the FuguRotate script is doing something, let’s try to understand what that is. Both Start and Update call the function Debug.Log to print messages to the Console view. Log is a static function defined in the Debug class, meaning you can call that function by specifying its class name before the function name, instead of an object (functions defined in classes are also called methods ).

Note

Static functions and variables are also called class functions and variables since they are associated with a class instead of instances of that class.

The variable gameObject references the GameObject this component (the script) is attached to (in this case, the cube), and every GameObject also has a name variable that references the name of this GameObject (in this case, Cube). The + operator can be used to concatenate two strings (it’s not just for addition anymore), so the Start function will print “Start called on GameObject” followed by the name of the GameObject.

Similarly, the Update function calls Debug.Log, concatenating “Update called at time” with the value of the static variable Time.time, which holds the time elapsed (in seconds) since the game started (in the case of running from the Unity Editor, when you click the Play button). Time.time is of type float (a floating-point number, which can represent noninteger values), but the + operator will convert the number to a string before appending it.

### Read the Scripting Reference

Besides autocompletion, another handy feature of MonoDevelop is the ability to bring up the Scripting Reference documentation for any Unity class, function, or variable. Clicking Debug at the beginning of a Debug.Log call in the script and pressing Command+’ will bring up the Scripting Reference documentation for the Debug class in a browser window. Clicking Log will bring up the specific documentation for the Debug.Log function .

Tip

Every time you see a Unity class, function, or variable you’re not familiar with, the first thing you should do is read the Script Reference documentation on it.

Figure [4-9](#A310332_2_En_4_Chapter.html#Fig9) shows the Unity Help menu.

![A310332_2_En_4_Fig9_HTML.jpg](Images/A310332_2_En_4_Fig9_HTML.jpg)

Figure 4-9.

Bringing up the Scripting Reference from the Help menu

The Scripting Reference contains documentation for all Unity classes plus their functions and variables. I find the fastest way to look up an arbitrary Unity class, function, or variable in the Scripting Reference is to type it in the search box. The search function in MonoDevelop enables you to search for the function you are looking for help on (Figure [4-10](#A310332_2_En_4_Chapter.html#Fig10)).

![A310332_2_En_4_Fig10_HTML.jpg](Images/A310332_2_En_4_Fig10_HTML.jpg)

Figure 4-10.

The Scripting Reference

However, clicking Classes on the left will give you the list of classes (Figure [4-11](#A310332_2_En_4_Chapter.html#Fig11)). Some classes, such as Debug and Time, are static classes, meaning they contain only static functions and variables (there are no Unity functions that don’t reside in a class), and there’s no reason to subclass them.

![A310332_2_En_4_Fig11_HTML.jpg](Images/A310332_2_En_4_Fig11_HTML.jpg)

Figure 4-11.

List of classes

Most classes, however, act as types of objects. The cube in the scene is an instance of the class GameObject. And GameObject is a subclass of Object, meaning it inherits all the documented variables and functions of Object. Conceptually, and in object-oriented parlance, the cube is a GameObject and thus also is an Object. Contrast this with the relationship between GameObjects and components. The cube is a GameObject, and it has a MeshFilter (which has a Mesh).

Many components, including lights and cameras, are subclasses of Behaviour, which is a component that can be enabled or disabled (as evidenced by the check boxes in the Inspector view). Some are not, though, like Transform, which is a direct subclass (no intervening classes) of a component and cannot be disabled (no check box in the Inspector view).

Each script is actually a subclass of MonoBehaviour , which is a subclass of Behaviour (so you can enable and disable scripts). Thus, the FuguRotate script defines a subclass of MonoBehaviour called FuguRotate. This class declaration is implicit in JavaScript, although you could make it explicit, as shown in Listing [4-3](#A310332_2_En_4_Chapter.html#Par39).

```
#pragma strict
class FuguRotate extends MonoBehaviour {
function Start () {
var object:GameObject = null;
Debug.Log("Start called on GameObject "+object.name);
}
function Update () {
Debug.Log("Update called at time "+Time.time);
}
}
Listing 4-3.
Version of FuguRotate.js with Explicit Class Declaration
```

### Run the Script

Proper programming is a lot like the scientific method you learned in school. You should have a general theory on how things work, hypothesize about what your code should be doing, and then run an experiment to validate that hypothesis. In other words, it’s time to run your code and see whether it does what you expect. When you click Play, the messages emitted by Debug.Log will show up in the Console view (Figure [4-12](#A310332_2_En_4_Chapter.html#Fig12)).

![A310332_2_En_4_Fig12_HTML.jpg](Images/A310332_2_En_4_Fig12_HTML.jpg)

Figure 4-12.

Tracing the Start and update callbacks

If the code doesn’t behave as you expect, it is time to revise your theory on what it’s doing. This may be a small consolation, but debugging your code is a learning experience!

## Debug the Script

Of course, your code will not be perfect as soon as you type it in. On the contrary, usually it will require several iterations of debugging. There are basically two types of errors: compilation errors that appear even before you try to run the game and runtime errors that occur while the game is playing.

### Compilation Errors

Every time a script is saved, it’s automatically compiled (converted from the source code to the code format that actually runs). Errors in the script that prevent successful compilation will appear in red at the bottom of the Unity Editor and in the Console view .

Double-clicking the error message at the bottom of the Editor will bring up the corresponding message in the Console view, and double-clicking the message in the Console view will bring up the script editor with the cursor located at the offending line of code. Even without that convenience, the error message will list the file name and line number of the offending code so you can find it manually.

For example, if you had typed Time.tim instead of Time.time in the Update function, an error message would have appeared as soon as you tried to save the script (Figure [4-13](#A310332_2_En_4_Chapter.html#Fig13)). Conveniently, Unity will often do a decent job of suggesting what you may have intended to type (although generally computers aren’t good at “do what I mean, not what I say”).

![A310332_2_En_4_Fig13_HTML.jpg](Images/A310332_2_En_4_Fig13_HTML.jpg)

Figure 4-13.

A script compilation error

### Runtime Errors

Like compilation errors, runtime errors also show up in the Console view. To demonstrate, replace the reference to gameObject in your Start function with a reference to a local variable named object . A local variable is declared within a function declaration and therefore is accessible only within the scope of the function. In this case, object is initialized as null, meaning it’s not referring to any actual GameObject and is never assigned any GameObject. When you click Play, an error results when the script tries to reference the name of the GameObject (Figure [4-14](#A310332_2_En_4_Chapter.html#Fig14)).

![A310332_2_En_4_Fig14_HTML.jpg](Images/A310332_2_En_4_Fig14_HTML.jpg)

Figure 4-14.

A script runtime error

### Debug with MonoDevelop

I still perform most of my debugging by calling Debug.Log and examining error messages in the Console view. But modern programmers are accustomed to more sophisticated debugging tools. It turns out that MonoDevelop also provides a full-featured debugger that’s been customized to work with Unity.

If MonoDevelop doesn’t automatically open when you edit the script from within the Unity Editor, you can open it from the MonoDevelop File menu, or you can double-click the solution file in the Finder (the highlighted file in Figure [4-15](#A310332_2_En_4_Chapter.html#Fig15)).

![A310332_2_En_4_Fig15_HTML.jpg](Images/A310332_2_En_4_Fig15_HTML.jpg)

Figure 4-15.

MonoDevelop project files

With the MonoDevelop solution loaded, enable debugging by selecting Attach to Process from the MonoDevelop Run menu (Figure [4-16](#A310332_2_En_4_Chapter.html#Fig16)).

![A310332_2_En_4_Fig16_HTML.jpg](Images/A310332_2_En_4_Fig16_HTML.jpg)

Figure 4-16.

The MonoDevelop Attach to Process command

Then choose Unity Editor in the resulting process list (Figure [4-17](#A310332_2_En_4_Chapter.html#Fig17)).

![A310332_2_En_4_Fig17_HTML.jpg](Images/A310332_2_En_4_Fig17_HTML.jpg)

Figure 4-17.

Attaching MonoDevelop to the Unity Editor

Click Play again, and this time MonoDevelop will show not only the line of code where the error occurred but also the specific type of error (NullReferenceException) and associated information such as the stack trace, which is useful for determining what chain of function calls resulted in the error (Figure [4-18](#A310332_2_En_4_Chapter.html#Fig18)).

![A310332_2_En_4_Fig18_HTML.jpg](Images/A310332_2_En_4_Fig18_HTML.jpg)

Figure 4-18.

MonoDevelop error details with the Unity Editor attached

While in debugging mode, the Unity Editor may be unresponsive, so when you’re finished with a debugging run, select Detach from the Run menu in MonoDevelop to detach the Unity Editor process (Figure [4-19](#A310332_2_En_4_Chapter.html#Fig19)). The Detach command is also available as a button on the MonoDevelop toolbar.

![A310332_2_En_4_Fig19_HTML.jpg](Images/A310332_2_En_4_Fig19_HTML.jpg)

Figure 4-19.

Detaching the Unity Editor process from MonoDevelop

Let’s fix the null reference problem by changing the initial value of the variable object from null to the script’s GameObject (Listing [4-4](#A310332_2_En_4_Chapter.html#Par55)).

```
function Start () {
var object:GameObject = this.gameObject;
Debug.Log("Start called on GameObject "+object.name);
}
Listing 4-4.
Start Function in FuguRotate.js with Null Reference Fix
```

The reference to this.gameObject is equivalent to just gameObject. The variable this always refers to the current object, which is this script component (in some other programming languages, self is used in the same way). Sometimes I like to explicitly prefix variables like gameObject with this. to make it clear that I’m referring to an instance variable, which is a variable defined in the class that is not local to a function and not static, so each instance of the class has its own copy.

Now the script shouldn’t break in the Start function (you can click Play in the Unity Editor to confirm). But you can still make execution stop on any line in MonoDevelop by adding a breakpoint. Click to the left of the line in the Update function, and a breakpoint indicator will appear by the line (Figure [4-20](#A310332_2_En_4_Chapter.html#Fig20)).

![A310332_2_En_4_Fig20_HTML.jpg](Images/A310332_2_En_4_Fig20_HTML.jpg)

Figure 4-20.

Adding a breakpoint in MonoDevelop

Now when you invoke Attach to Process, connect to the Unity Editor, and click Play in the Unity Editor, execution will stop in your Update function just as if there were an error there (Figure [4-21](#A310332_2_En_4_Chapter.html#Fig21)). When execution is halted in MonoDevelop while in debugging mode, you can examine the stack trace and inspect the runtime environment in other ways. For example, Figure [4-21](#A310332_2_En_4_Chapter.html#Fig21) shows the result of typing gameObject in the Watch panel and then halting at a breakpoint in the FuguRotate Update callback. The current value of gameObject is the cube GameObject, which can now have its member variables examined.

![A310332_2_En_4_Fig21_HTML.jpg](Images/A310332_2_En_4_Fig21_HTML.jpg)

Figure 4-21.

Execution halted at a breakpoint

Once you’re finished examining the runtime state at the breakpoint, you have an option in the Run menu to continue until the next breakpoint or error. The Run menu also has the commands Step Over (continue until the next line of code in this function), Step Into (continue until the first line of code in any function called by this line of code), or Step Out (continue until you’ve exited this function) so you can step through your script one line at a time. All of these commands have keyboard shortcuts shown in the Run menu and are also available as buttons on the toolbar.

## Make It Rotate

Now that you’re familiar with scripts and how to attach, edit, and debug them, you’re ready to make this script do what you ultimately want—rotate the cube .

### Rotate the Transform

Moving a GameObject at runtime requires changing its Transform component. Rotating the GameObject specifically requires changing the Rotation value in its Transform component. Replacing the contents of the FuguRotate script with the code in Listing [4-5](#A310332_2_En_4_Chapter.html#Par62) will do the trick.

```
#pragma strict
var speed:float = 10.0; // controls how fast we rotate
function Start () {
var object:GameObject = this.gameObject;
Debug.Log("Start called on GameObject "+object.name);
}
// rotate around the object's y axis
function Update () {
//Debug.Log("Update called at time "+Time.time);
transform.Rotate(Vector3.up*speed*Time.deltaTime);
}
Listing 4-5.
FuguRotate.js Script with Rotation Code in Update
```

Let’s go through the new code. First, any line starting with // is a comment and not executed as code. This is a convenient way to deactivate a line of code without deleting it from the file. Comments can also be added within /* and */, which is useful for multiline comments.

Note

Some will say code should be written well enough to be self-explanatory, but as a basic rule of thumb, if the intent of the code is not obvious, add a comment. I’ve wasted plenty of time trying to remember why I wrote some code the way I did. The speed variable controls how fast the object rotates, and since it’s declared as a public instance variable, it’s available as an adjustable property in the Inspector view.

You could have just typed var speed=10.0 instead of var speed:float=10.0\. The compiler can infer that speed must be of type float since it’s initialized with a floating-point number (this is known as type inference ), but it’s better to be as clear as you can, not just for the Unity compiler but also for any human reading the code, including yourself! The variable speed is used in the Update function, which calls the Rotate function defined in the Transform class. Rotate takes a Vector3 as an argument and interprets its x,y,z values as Euler angles (rotation, in degrees, around the GameObject’s x-, y-, and z-axes). A vector represents a direction and magnitude, but it’s common in game engines to use the vector data structure to represent Euler angles.

Note

The movie Despicable Me has as good a definition of a vector as any: “I go by Vector. It’s a mathematical term, represented by an arrow with both direction and magnitude. Vector! That’s me, because I commit crimes with both direction and magnitude. Oh yeah!”

Vector3.up is a conveniently defined Vector3 with values (0,1,0), so the Update function is rotating around the y-axis by speed * Time.deltaTime degrees. Time.deltaTime is the elapsed time since the last frame, in seconds, so, effectively, you’re rotating by speed degrees per second. In Update callbacks, you almost always want to multiply any continuous change by Time.deltaTime so that your game behavior does not fluctuate with differences in frame rate. If the multiplication by Time.deltaTime was omitted, the FuguRotate script would rotate objects twice as slow on any machine that’s running two times slower.

As intended, the variable speed shows up in the Inspector view , and you can edit it (Figure [4-22](#A310332_2_En_4_Chapter.html#Fig22)).

![A310332_2_En_4_Fig22_HTML.jpg](Images/A310332_2_En_4_Fig22_HTML.jpg)

Figure 4-22.

The Inspector view of the FuguRotate script with adjustable speed

If you click Play, the cube now spins at a moderate rate, and continuous change in the Transform component’s y Rotation value will be displayed in the Inspector view. You can edit the Speed value of the FuguRotate script in the Inspector to slow it down or speed it up.

### Other Ways to Rotate

Transform.Rotate is a good example of why you should read the Scripting Reference for every function you encounter. It turns out Transform.Rotate is an overloaded function, meaning it has variations with different parameters .

Note

The term parameter is used when describing the function declaration, and argument refers to the same value when its passed at runtime, but it’s a subtle distinction. Parameter and argument can usually be used interchangeably without confusion.

The documentation for Transform.Rotate lists several combinations of arguments that it can take. Instead of accepting a Vector3 to rotate around and an angle (in degrees), Transform.Rotate can take the x-, y-, and z-axis rotations separately. Listing [4-6](#A310332_2_En_4_Chapter.html#Par73) shows an alternative version of the Update function that just passes in the x, y, and z rotations.

```
function Update () {
transform.Rotate(0,speed*Time.deltaTime,0);
}
Listing 4-6.
Version of Transform.Rotate That Takes Rotation in x, y, and z Angles
```

Or the x, y, and z rotations can be packaged into a Vector3, as shown in Listing [4-7](#A310332_2_En_4_Chapter.html#Par75).

```
function Update () {
transform.Rotate(Vector3(0,speed*Time.deltaTime,0));
}
Listing 4-7.
Version of Transform.Rotate That Takes Rotation in a Vector3
```

Although a vector has a precise mathematical definition, Unity follows a common practice among 3D application programming interfaces of reusing its vector data structure to represent anything that has x, y, and z values. For example, if you read the Script Reference page on Transform (and you should), you’ll see the position, rotation, and scale of Transform are all Vector3 values.

### Rotate in World Space

Each of the Transform.Rotate variations also has an optional parameter, which defaults to Space.Self. This specifies that the rotation takes place around the transform’s (and thus the GameObject’s) local axes, which correspond to the axis handles you see in the Scene view when the GameObject is selected. If you specify Space.World, as shown in Listing [4-9](#A310332_2_En_4_Chapter.html#Par78), the rotation takes place around the world axes (the x-, y-, z-axes centered at 0,0,0).

```
function Update () {
transform.Rotate(Vector3.up,speed*Time.deltaTime,Space.World);
}
Listing 4-9.
Rotating Around the World Axes
```

## Children of the Cube

A scene with just one cube is not very interesting, so let’s make it slightly more interesting by adding more cubes. You could repeatedly create new cubes in the same manner as the first one. Or you could save some time by duplicating the existing cube (select the cube and then invoke the Duplicate command on the Edit menu or use the Command+D keyboard shortcut). But let’s take this opportunity to learn about prefabs .

### Making Prefabs

A prefab is a special type of asset created by cloning a GameObject. The prefab can then be used to create identical copies of that GameObject. In that sense, Unity prefabs are like prefabricated housing but better. If you make a change to one instance of a prefab, you can have that change automatically propagate to all other instances of the prefab .

First, in keeping with our asset organization, create a new folder in the Project view named Prefabs. Then, with the Prefabs folder selected, click Prefab on the Create menu in the Project view to create an empty prefab in that folder. You can then fill out the prefab by dragging the cube from the Hierarchy view onto the empty prefab. Alternatively, instead of starting with an empty prefab, you can just drag the cube directly into the Prefabs folder, and a prefab will automatically be created and named after the original GameObject (Figure [4-23](#A310332_2_En_4_Chapter.html#Fig23)).

![A310332_2_En_4_Fig23_HTML.jpg](Images/A310332_2_En_4_Fig23_HTML.jpg)

Figure 4-23.

The Project view of the Cube prefab

You can now drag the prefab into the Hierarchy view every time you want to create a new cube that looks just like the original cube GameObject . But instead of having multiple independent cubes in the scene, let’s add some cubes as children of the existing cube. Drag the prefab directly onto the cube in the Hierarchy view twice, and you’ll now have two new cubes underneath Cube. In the Inspector view (Figure [4-24](#A310332_2_En_4_Chapter.html#Fig24)), you can see the new cubes are identical to the first cube, featuring the same components and component properties. Let’s name the new cubes child1 and child2 (by the way, this is a good time to try the Lock feature of the Inspector view so you can inspect two GameObjects at the same time).

![A310332_2_En_4_Fig24_HTML.jpg](Images/A310332_2_En_4_Fig24_HTML.jpg)

Figure 4-24.

Editing the child Cube

As the children of cube , the positions of child1 and child2 displayed in the Inspector view are relative to the position of their parent, Cube. This means if a child cube has position (0,0,0), it is in the exact same position as its parent. So, let’s set the positions of child1 and child2 to (2,0,0) and (-2,0,0). The two child cubes now are spaced out from their parent cube like satellite cubes (Figure [4-25](#A310332_2_En_4_Chapter.html#Fig25)).

![A310332_2_En_4_Fig25_HTML.jpg](Images/A310332_2_En_4_Fig25_HTML.jpg)

Figure 4-25.

Scene view of the cube and its child Cubes

Before you click Play, change the FuguRotate script to the simple transform.Rotate call (Listing [4-9](#A310332_2_En_4_Chapter.html#Par85)). Now when you click Play, the main cube rotates as before, and the child cubes follow around like the spokes in a wheel. The child cubes also spin around their own axes since they’re running their own copies of the FuguRotate script (all three cubes would rotate around the same world origin if you had supplied Space.World in the Transform.Rotate call).

```
#pragma strict
var speed:float = 10.0; // controls how fast we rotate
function Start () {
var object:GameObject = this.gameObject;
Debug.Log("Start called on GameObject "+object.name);
// rotate around the object's y axis
function Update () {
//Debug.Log("Update called at time "+Time.time);
transform.Rotate(Vector3.up*speed*Time.deltaTime);
}
Listing 4-9.
FuguRotate.js Reverted to Rotation in Update
```

Change the FuguRotate speed property for child2 from 10 to 50, click Play, and you will see that cube spins faster than the others. You can easily make the same change in child1, but imagine if you had 50 cubes to change. That would be tedious! This is where the power of prefabs comes in. Select child2 and then invoke Apply Changes To Prefab in the GameObject menu (Figure [4-26](#A310332_2_En_4_Chapter.html#Fig26)).

![A310332_2_En_4_Fig26_HTML.jpg](Images/A310332_2_En_4_Fig26_HTML.jpg)

Figure 4-26.

Applying changes to a prefab

Now child1 is identical to child2 again (except for the name and position, which Unity reasonably assumes you don’t want the same in every instance of the prefab). When you click Play, the child cubes are now spinning at the same faster speed.

### Breaking Prefabs

But the main cube is also spinning faster because of the updated rotation speed. If that isn’t what you intended, you can change the cube’s speed back to 10, and then to ensure that you don’t propagate changes in the child cubes to the main cube, you can select the main cube and invoke Break Prefab Instance in the GameObject menu (Figure [4-27](#A310332_2_En_4_Chapter.html#Fig27)).

![A310332_2_En_4_Fig27_HTML.jpg](Images/A310332_2_En_4_Fig27_HTML.jpg)

Figure 4-27.

Breaking a prefab instance

Now the cube no longer has a relation to the prefab, and any change to the child cubes will not be propagated to the cube.

## Explore Further

The scene has evolved from pretty and static to pretty and dynamic, merely with the addition of simple scripted movement. You’ll jazz up the scene some more in the next chapter with animation and sound, but the major milestone you’ve reached at this point really is learning how to create, edit, and debug scripts. From now until the end of the book, you’ll be adding scripts, so get used to it!

### Unity Manual

The “Building Scenes” section of the Unity Manual has two pages relevant to the work in this chapter—a description of prefabs in the “Prefabs” section and a good explanation in the “Component-Script Relationship” section.

The one new type of asset introduced in this chapter (besides prefabs) is the script. The “Using Scripts” page in the “Asset Import and Creation” section introduces the basic concepts covered in the rotation script—creating a script, attaching it to a GameObject, printing to the Console view (using the print function instead of Debug.Log as you did), declaring a variable, and even applying a rotation in the Update function.

It’s worth mentioning the “Transforms” page again since your rotation script is in the business of modifying Transform components. That page also describes the parent–child GameObject relationship, which technically is among transforms, but since there’s a one-to-one relationship between GameObjects and transforms, it’s less confusing to think of the linkage among GameObjects, as displayed in the Hierarchy view.

We dipped into one advanced topic in this chapter—debugging. The “Debugging” section describes the Console view, the MonoDevelop debugger, and where to find log files on your file system.

### Scripting Reference

I’ve mentioned two of the three main pieces of official Unity documentation so far—the Unity Manual and the Reference Manual. The third piece is the Scripting Reference . This chapter presented your first foray into scripting, so everything in the “Scripting Overview” section of the Scripting Reference is worth reading at this point. The “Runtime Classes” list illuminates the inheritance relationship among the Unity classes. After that, I recommend making frequent use of the search box on that page anytime you see anything in a script you don’t recognize (or even if you do recognize it, if you haven’t read its documentation).

### Scripting

Although you’re working only with JavaScript in this book, there’s enough JavaScript and C# out in the Unity world that you should get comfortable with both. The book Head First C# by Andrew Stellman and Jennifer Greene is a great visual step-by-step introduction to C#.

And since C# was created by Microsoft as part of its .NET Framework, the official C# documentation and other resources can be found by searching for C# on the Microsoft Developer Network (MSDN) at [`http://msdn.microsoft.com/`](http://msdn.microsoft.com/) .

While you’re on MSDN, search also for .NET documentation, as the Unity scripting engine is implemented with Mono, which is an open source version of .NET. The official Mono web site is at [`http://mono-project.org/`](http://mono-project.org/) .

Put two programmers together in a room, and if there’s anything they’ll fight about, it’s coding conventions. My rule of thumb is to go along with the convention of the official language and framework that I’m coding in. It’s a dull topic with fun names.

For example, Unity uses a combination of camel case and Pascal case in its capitalization rules (or camelCase and PascalCase, if you apply the conventions to themselves). You can look up camel case on Wikipedia.

Bracket placement is also a common source of contention. The convention I use here (and used by Unity, at least when creating the template for new scripts) is called Egyptian style, according to Jeff Atwood (of Stack Exchange fame) on his popular blog Coding Horror ( [`www.codinghorror.com/blog/2012/07/new-programming-jargon.html`](http://www.codinghorror.com/blog/2012/07/new-programming-jargon.html) ). The article also terms the practice of applying a standard prefix to class names smurf naming.

# 5. Let’s Dance! Animation and Sound

Of the three ways to move GameObjects in Unity, scripted control of the Transform component was covered in the previous chapter, and physics will play a significant role in the bowling game example initiated in the next chapter. Scripted movement is ubiquitous. For example, even in the upcoming bowling game, which relies heavily on physics to control and simulate the bowling ball and its collision with the floor and bowling pins, the camera-following behavior is implemented by a script that modifies its Transform component (much like the script used in the cube scene). Not only that, every moving GameObject is reset between rolls and frames by restoring the values in its Transform component.

Before proceeding to that bowling game, you’ll spend a little more time in this chapter building on the cube scene constructed so far, enhancing it with a dancing character animation to get a feel for Unity’s animation support. And it wouldn’t be a dance demo without music, so the scene will incorporate looping sound. It will still be a nonphysical and only mildly interactive scene, but ideally it will provide a pretty and entertaining way to introduce Unity’s animation and audio support before delving into a lot of physics (a big topic, spanning two chapters) and even more scripting (a bigger topic, spanning the rest of this book).

Quality animations, particularly character animations, are not easy to create if you’re not a professional. The same goes for music. Fortunately, there are plenty of music and character animation packs on the Unity Asset Store, many of them free, including the Skeletons Pack and General Music Set used in this chapter. Those assets are not included in the corresponding project on [`www.apress.com/9781484231739`](http://www.apress.com/9781484231739) , but that project can be used as a starting point if you’re just jumping in at this point and don’t have the cube scene constructed from the previous chapter.

## Import the Skeletons Pack

Search the Asset Store from within the Asset Store window . Type skeleton in the search field and select Free, and several skeleton asset packs show up in the search results (Figure [5-1](#A310332_2_En_5_Chapter.html#Fig1)).

![A310332_2_En_5_Fig1_HTML.jpg](Images/A310332_2_En_5_Fig1_HTML.jpg)

Figure 5-1.

The Project view search results for the Skeletons Pack

Select the skeleton asset and confirm in the Inspector view that the package is indeed the Skeletons Pack (Figure [5-2](#A310332_2_En_5_Chapter.html#Fig2)).

![A310332_2_En_5_Fig2_HTML.jpg](Images/A310332_2_En_5_Fig2_HTML.jpg)

Figure 5-2.

The Import Unity Package view of the Skeletons Pack

The installation will add a SkeletonData folder in the Project view (Figure [5-3](#A310332_2_En_5_Chapter.html#Fig3)) and some prefabs in the prefabs folder.

![A310332_2_En_5_Fig3_HTML.jpg](Images/A310332_2_En_5_Fig3_HTML.jpg)

Figure 5-3.

The Project view of the Skeletons Pack prefabs

## Add a Skeleton

The four prefabs installed by the Skeletons Pack all have the same skeleton but sport different gear, such as a sword and shield . However, it turns out the prefabs with armed skeletons look like they’re chopping their own limbs off when dancing, so let’s just stick with the skeletonNormal prefab, which has just the bare-bones (ha!) skeleton. Add an instance of the skeletonNormal prefab to the scene by dragging that prefab into the Hierarchy view (Figure [5-4](#A310332_2_En_5_Chapter.html#Fig4)). The skeletonNormal GameObject has an Animation component that references the animation data of the skeleton. skeletonNormal also has two child GameObjects. One, named Bip01, is actually a hierarchy of GameObjects that represents the bones of the skeleton. As the skeleton moves, so does the arm, and any hand movement is relative to the arm, and so on. The skeleton hierarchy is reminiscent of the “Dem Bones” song—“The thigh bone’s connected to the hip bone….”

![A310332_2_En_5_Fig4_HTML.jpg](Images/A310332_2_En_5_Fig4_HTML.jpg)

Figure 5-4.

Hierarchy view with the skeletonNormal asset

The other child GameObject, skeleton, is a GameObject with a SkinnedMeshRenderer component, like the MeshRenderer component that is a subclass of Renderer. However, SkinnedMeshRenderer renders the mesh that wraps around the skeleton and follows its joints as they move. The mesh essentially forms the skin around a skeleton, so the process of mapping such a mesh to a skeleton is often called skinning .

The skeletonNormal prefab also contains a couple of particle effects, implemented by GameObjects with ParticleEmitter components. Particle effects consist of many tiny animated primitives and are great for creating flames, explosions, smoke, or any kind of sparkly effects .

Note

The particle effects included in the Skeletons Pack work with Unity’s Legacy particle system, not the newer Shuriken particle system introduced in Unity 3.5\. The Legacy system uses the ParticleEmitter component, while Shuriken uses the ParticleSystem component.

## Hide the Cubes

This scene is all about the dancing skeleton, so you can dispense with the cubes that were featured in the previous two chapters. Instead of deleting the cubes from the scene (by selecting them and invoking Delete from the Edit menu or by using the Command+Delete shortcut), they can be made invisible, out of sight and out of mind, by deactivating them. Select the parent cube in the Hierarchy view and deselect the top-left check box in the Inspector view, and the entire tree of cubes in the Hierarchy view is now grayed out to indicate they are inactive (Figure [5-5](#A310332_2_En_5_Chapter.html#Fig5)).

![A310332_2_En_5_Fig5_HTML.jpg](Images/A310332_2_En_5_Fig5_HTML.jpg)

Figure 5-5.

Scene with the skeleton active and the cubes deactivated

All the child cubes are effectively inactive because the parent’s active status overrides the children’s. In other words, a GameObject is really active only if it’s marked as active and its parent is marked as active and its parent’s parent is marked as active, and so on. Any inactive GameObject has its components effectively disabled. Its Renderer no longer renders, its Collider no longer collides, and any attached scripts do not have their callbacks called, with the exception of OnEnable and OnDisable.

## Orbit the Skeleton

When you inserted the skeleton into Hierarchy view, Unity placed this asset at the origin (0,0,0). However, you may recall that you placed the cubes, the camera, and the lights away from this point. So, you need to move the skeleton closer to the camera. You could move the skeleton with the Transform tool, or you could simply type in the position in the Inspector. The Transform values (or coordinates) for Position are (0,0,-10) for X, Y, and Z, respectively (Figure [5-6](#A310332_2_En_5_Chapter.html#Fig6)).  

![A310332_2_En_5_Fig6_HTML.jpg](Images/A310332_2_En_5_Fig6_HTML.jpg)

Figure 5-6.

The Transform values for the position of the skeleton

In Chapter [2](../Text/A310332_2_En_2_Chapter.html), you created the FuguRotate script to rotate the object around its x-axis. In Chapter [2](../Text/A310332_2_En_2_Chapter.html), you put this script on the Cube GameObject. You can apply this script to any game object. In Project view, select the FuguRotate script from the Scripts folder and drag this to the camera in the Hierarchy view.

Now when you click Play, the Main Camera orbits the skeleton as you move the mouse (Figure [5-7](#A310332_2_En_5_Chapter.html#Fig7)). As a bonus, since the skeleton prefab includes a particle effect, the skeleton appears to be standing amid a sparkly haze.

![A310332_2_En_5_Fig7_HTML.jpg](Images/A310332_2_En_5_Fig7_HTML.jpg)

Figure 5-7.

The Game view of an idle skeleton

## Make the Skeleton Dance

Right now the skeleton just shuffles around a little instead of dancing. If you select the skeletonNormal asset and examine it in the Inspector view, you can see the GameObject has an Animation component with a field referencing an “idle” or “Run” AnimationClip (Figure [5-8](#A310332_2_En_5_Chapter.html#Fig8)).

![A310332_2_En_5_Fig8_HTML.jpg](Images/A310332_2_En_5_Fig8_HTML.jpg)

Figure 5-8.

Changing the skeleton’s position and animation

Notice also that the Play Automatically check box is selected, which is why the animation runs as soon as you click Play. This option would be deselected if the animation is intended to be initiated by a script at some time during the game.

To replace the idle (or run) AnimationClip with a dancing AnimationClip asset, click the circle to the right of “idle” to bring up the AnimationClip selection list (Figure [5-9](#A310332_2_En_5_Chapter.html#Fig9)). Select the AnimationClip asset named “dance.”

![A310332_2_En_5_Fig9_HTML.jpg](Images/A310332_2_En_5_Fig9_HTML.jpg)

Figure 5-9.

An AnimationClip selection list

Now when you click Play, the skeleton dances ! (See Figure [5-10](#A310332_2_En_5_Chapter.html#Fig10).)

![A310332_2_En_5_Fig10_HTML.jpg](Images/A310332_2_En_5_Fig10_HTML.jpg)

Figure 5-10.

The dancing skeleton

## Make the Skeleton Dance Forever

If you let the skeleton dance for a while, it eventually stops when the AnimationClip asset ends. But the AnimationClip asset can be set to loop. In the Inspector view of skeletonNormal, click the reference to the “dance” AnimationClip asset in the Animation component. The Project view will show the selected AnimationClip asset and associated skeleton mesh along with the skeleton’s other AnimationClip assets (Figure [5-11](#A310332_2_En_5_Chapter.html#Fig11)).

![A310332_2_En_5_Fig11_HTML.jpg](Images/A310332_2_En_5_Fig11_HTML.jpg)

Figure 5-11.

Looping the animation

The Inspector view displays a single stretch of animation data that’s split into the various AnimationClip assets. Select the “dance” clip in that list, and below, the Wrap Mode will display as Default. Change Wrap Mode to Loop and click the Apply button (just below the Wrap Mode field).

Now when you click Play, the skeleton will dance and dance and dance.

Note

This skeleton uses Unity’s Legacy animation system. You can verify this by clicking the Rig button shown in Figure [5-11](#A310332_2_En_5_Chapter.html#Fig11) and see that the Legacy check box is selected. Unity 4 introduced a new animation system called Mecanim, which, among other features, allows you to use animation with different skeletons (or avatars, as they call them).

## Add a Dance Floor

The skeleton looks kind of strange dancing in the sky, so let’s give it a floor to dance on. You could make another cube and scale it, but there’s a more suitable primitive GameObject that you can use—a plane. Like the cube, you can make a plane from the Create submenu of the GameObject menu on the menu bar. But this time, try the Create button on the top left of the Hierarchy view (Figure [5-12](#A310332_2_En_5_Chapter.html#Fig12)).

![A310332_2_En_5_Fig12_HTML.jpg](Images/A310332_2_En_5_Fig12_HTML.jpg)

Figure 5-12.

Creating a plane using the Create menu in the Hierarchy view

Note that all the items in the Create menu are available in the GameObject menu on the menu bar, but not all the items that can be created from the GameObject menu are available in the Hierarchy view’s Create menu.

Now a plane is listed in the Hierarchy view and displayed in the Scene view (Figure [5-13](#A310332_2_En_5_Chapter.html#Fig13)). A plane is like a flat cube—it has a top and bottom but no sides and no height. If you change its x and z scale in its Transform component, the Plane will stretch out, but changing the y scale (height) makes no difference.

![A310332_2_En_5_Fig13_HTML.jpg](Images/A310332_2_En_5_Fig13_HTML.jpg)

Figure 5-13.

The Scene view of the new plane

Remember, the skeleton position was set to (0,0,-10), and the skeleton’s origin is at the bottom, coinciding with the skeleton's feet. So, select the new plane that’s now shown in the Hierarchy view, and then, in the Inspector view, set the plane’s position also to (0,0,-10) so that the floor is placed at the skeleton’s feet. Specifically, the y positions (height) of the feet and floor have to match up.

That’s a pretty bland-looking floor; it could benefit from a material with a texture. The ArtskillZ Free Texture Pack is already imported and used for the cube, so you might just as well use it here for the floor. With the cubes, you just dragged a texture onto the cube in the Hierarchy view and then modified the resulting material in the Inspector view.

This time around, go straight to the Inspector view of the plane (Figure [5-14](#A310332_2_En_5_Chapter.html#Fig14)) and change its shader to Bumped Specular. Then drag a main texture and its associated bump (normal map) texture from the ArtskillZ Free Texture Pack (in the figure, I’ve chosen the Floor03 and Floor03_n textures from the Fantasy folder of the pack).

![A310332_2_En_5_Fig14_HTML.jpg](Images/A310332_2_En_5_Fig14_HTML.jpg)

Figure 5-14.

Editing the plane position and material

Now when you click Play, the skeleton is dancing on a floor! (See Figure [5-15](#A310332_2_En_5_Chapter.html#Fig15).)

![A310332_2_En_5_Fig15_HTML.jpg](Images/A310332_2_En_5_Fig15_HTML.jpg)

Figure 5-15.

The skeleton dancing on a textured plane

## Add a Shadow

One of the Light component properties discussed in Chapter [3](../Text/A310332_2_En_3_Chapter.html) is Shadow Type. Now that the scene has a floor that shadows can be cast on, let’s try this feature.

Select a point light (either in the Hierarchy view or in the Scene view), and in the Inspector view (Figure [5-16](#A310332_2_En_5_Chapter.html#Fig16)), change Shadow Type from None to Soft Shadows. This provides shadows with a nice, soft, fuzzy border (the penumbra of the shadow), which looks better than the sharp borders of Hard Shadows.

![A310332_2_En_5_Fig16_HTML.jpg](Images/A310332_2_En_5_Fig16_HTML.jpg)

Figure 5-16.

A point light converted to a directional light with soft shadows

Now you should change the type from Point Light to Directional Light. Point lights can cast shadows, but only hard shadows and only when rendering in Deferred Mode, which isn’t available in Unity iOS. Actually, soft shadows aren’t available in Unity iOS at all, but they look so good, let’s try them out here.

Whereas a point light radiates light in all directions from a position , a directional light is treated as if it were infinitely far away, and thus its position doesn’t matter, but its rotation does. So in the Transform component, you can use 0,0,0 for the position (which doesn’t really matter for a directional light, but it looks cleaner than having an arbitrary position) and 25,0,0 for the rotation, indicating the light is rotated 25 degrees around the x-axis. In other words, the light is looking down 25 degrees from horizontal.

Instead of entering a rotation, you could manually rotate the light in the Scene view (Figure [5-17](#A310332_2_En_5_Chapter.html#Fig17)) by selecting the light, clicking the Rotate tool in the button bar on the top left of the Unity Editor (third button from the left), and then dragging the circle representing the x-axis rotation on the light.

![A310332_2_En_5_Fig17_HTML.jpg](Images/A310332_2_En_5_Fig17_HTML.jpg)

Figure 5-17.

Rotating a directional light in the Scene view

There’s no reason to use the Move tool (second button from the left on the button bar) since the position of a directional light doesn’t matter, but it can help visualize what the light is illuminating if you move it up and away so that it’s obviously pointing at the skeleton.

Tip

You can aim a directional or spot light (or anything that can be aimed) in the same way you aimed the Main Camera at the cube in Chapter [3](../Text/A310332_2_En_3_Chapter.html). In this case, you would select the skeleton, press the F key to Frame Selected, click the leftmost button on the top-left toolbar to enter the Hand tool mode in the Scene view, and then in the Scene view Alt+click-drag until the Scene view camera is pointing the way you want the light to point. Then select the point light and invoke Align to view from the GameObject menu.

If you click Play at this point, you should see a shadow, but one with room for improvement, even with Strength set to 1\. Unity uses a technique called shadow maps for generating shadows, where the shadow quality is dependent on the resolution of the shadow maps. The default setting for the shadow map resolution in the Light component is Use Quality Settings, which you can override in the Inspector view. But let’s go to the Quality Settings window instead by selecting Project Settings ➤ Quality from the Edit menu (Figure [5-18](#A310332_2_En_5_Chapter.html#Fig18)).

![A310332_2_En_5_Fig18_HTML.jpg](Images/A310332_2_En_5_Fig18_HTML.jpg)

Figure 5-18.

Selecting Quality Settings from the Edit menu

The Quality Settings manager appears in the Inspector view (Figure [5-19](#A310332_2_En_5_Chapter.html#Fig19)), displaying a list of quality settings and the selected quality setting for each build platform. The default quality setting for desktop platforms is Good. Select that line in the platform settings table so the individual settings for Good are displayed. The Shadows settings, in particular, control the shadow quality. So, change Shadow Resolution to Very High Resolution and also lower Shadow Distance to 10 since shorter shadow distances lead to better shadow resolution (but too short and the shadow won’t render).

![A310332_2_En_5_Fig19_HTML.jpg](Images/A310332_2_En_5_Fig19_HTML.jpg)

Figure 5-19.

Adjusted quality settings for shadows

Now when you click Play, you have a dancing shadow that should look pretty good! (See Figure [5-20](#A310332_2_En_5_Chapter.html#Fig20).)

![A310332_2_En_5_Fig20_HTML.jpg](Images/A310332_2_En_5_Fig20_HTML.jpg)

Figure 5-20.

The Game view of the skeleton with a shadow

## Add Music

No dance demo would be complete without music. Fortunately, there’s plenty of free music available in the Asset Store. One music package I’ve used frequently is Gianmarco Leone’s General Music Set. If you enter general music in the Project view search field, all of the audio clips in that package show up in the results (Figure [5-21](#A310332_2_En_5_Chapter.html#Fig21)). Click any one of them and then import the package from the Inspector view.

![A310332_2_En_5_Fig21_HTML.jpg](Images/A310332_2_En_5_Fig21_HTML.jpg)

Figure 5-21.

Searching for the General Music Set in the Asset Store

After the audio clips install in the Project view (Figure [5-22](#A310332_2_En_5_Chapter.html#Fig22)), select the audio clip named Tomorrow from Music by Joey. However, you can decide for yourself which track to install. In the Inspector view (Figure [5-23](#A310332_2_En_5_Chapter.html#Fig23)), click the Play button at the bottom, above the stereo waveform display, to listen to the audio clip.

![A310332_2_En_5_Fig23_HTML.jpg](Images/A310332_2_En_5_Fig23_HTML.jpg)

Figure 5-23.

The Inspector view of an audio clip

![A310332_2_En_5_Fig22_HTML.jpg](Images/A310332_2_En_5_Fig22_HTML.jpg)

Figure 5-22.

The Project view of the Music Files music set

Now you’re ready to add the audio clip to the scene. Simply drag the audio clip from the Project view into the Hierarchy view, and a same-named GameObject will automatically be created (Figure [5-24](#A310332_2_En_5_Chapter.html#Fig24)).

![A310332_2_En_5_Fig24_HTML.jpg](Images/A310332_2_En_5_Fig24_HTML.jpg)

Figure 5-24.

Audio in the scene

Select the Tomorrow GameObject, and you will see in the Inspector view that it has an AudioSource component. Among its properties is a reference to Tomorrow and a Play On Awake check box. That specifies the sound will start playing as soon as the scene starts playing. You should select the Loop check box too, so the song will keep playing over and over, just like the dancing animation. As this is background music, set the Spatial Blend setting to 2D.

Now when you click Play, you have a real music dance party!

## Explore Further

Thus ends our dalliance with dancing skeletons . For the rest of this book, you’ll be concentrating on developing a bowling game with mostly physics-based motion. But you should already feel a sense of accomplishment, having spent the past three chapters evolving a simple static cube scene into a musical dancing skeleton scene, complete with fancy graphics features such as particle effects and dynamic shadows. Fancy features tend to be expensive features in terms of performance, so use them judiciously!

### Unity Manual

The “Asset Import and Creation” section covers the two new types of assets introduced in this chapter: audio and animation . The “Audio Files” page lists the audio formats recognized by Unity and describes the AudioClip asset that results from importing an audio file. In earlier chapters, you just dipped your toes into the “Creating Gameplay” section of the Unity Manual, but now you’ve entered the deep end, adding content described in the pages “Particles,” “Legacy Animation System,” and “Sound.” Also, the Mecanim animation system is the future of Unity animation, so it’s worthwhile to read up on that in the “Mecanim Animation System” section.

Using dynamic shadows is a big topic. The “Advanced” section of the Unity Manual has an extensive treatment in the section “Shadows in Unity,” which covers “Directional Shadows,” “Troubleshooting Shadows,” and “Shadow Size Computation.”

### Reference Manual

The “Animation” section of the Reference Manual has the “Animation Component” and “Animation Clip” pages covering those assets used by the Legacy animation system. This section also includes several pages on components used in the new Mecanim animation system.

The “Audio” section lists the audio components needed to add music to the dancing scene constructed in this chapter; see the sections “AudioListener” (normally automatically attached to the Main Camera) and “AudioSource” (attached to the sound source). In addition, the section “Audio Filter Components” lists components (a Pro feature) that can add effects like echo, for example.

The “Effects” section has a page on the new Shuriken particle system and multiple pages on the Legacy particle system.

The Reference Manual also documents the various settings managers, including a “Quality Settings” page, which you used to adjust your shadow quality, but it also affects other visual quality elements, such as lighting and particles.

### Asset Store

The Asset Store not only offers a lot of music in the Audio category, much of it free, but also provides scripts for playing music. If you want to play more than one song in your dance scene, you can write a script that plays a fixed or random sequence of songs, using the Unity functions defined in the AudioSource class (to play and stop music) and AudioListener class (to control the volume). But it’s easier to just download, for example, the Free Jukebox script from the Asset Store (or Jukebox Pro if you’re willing to part with a few dollars).

### Computer Graphics

I mentioned the book Real-Time Rendering at the end of Chapter [3](../Text/A310332_2_En_3_Chapter.html), and it merits a recommendation again for the computer graphics topics you encountered in this chapter: animation, particle effects, and dynamic shadows. There are many books just on character animation, including those on the content-creation side for professional animators, such as George Maestri’s Digital Character Animation.

# 6. Let’s Roll! Physics and Controls

At last, you’re ready to begin the main Unity project for this book, a bowling game in the style of HyperBowl, an arcade/attraction game I worked on more than ten years ago and ported to Unity just a few years ago. HyperBowl has a unique be-the-ball gameplay where the player constantly rolls the ball around a 3D environment to reach the pins. The bowling game constructed in this book will be much simpler but feature the same style of control.

Even a simple bowling game like this one is a significant project. It will incorporate physics, input handling, camera control, collision sounds, game rules, a score display, and a start/pause menu. And that’s even before performing any adaptation for iOS. This chapter just focuses on getting a ball to roll around and requires some physics setup but only a single script for the ball control. The script is available in the project for this chapter on [`www.apress.com/9781484231739`](http://www.apress.com/9781484231739) . But it’s a fairly simple script and developed incrementally throughout this chapter, so it’s best to follow along, implementing it from scratch.

## Make a New Scene

The dance scene in the previous chapter was really a modification of the original cube scene you started with in Chapter [3](../Text/A310332_2_En_3_Chapter.html), so you didn’t have create the entire scene from scratch again. The light and floor would be useful in the bowling game, so by the same token, you could continue modifying the cube scene and deactivate or remove the skeleton and music before adding content for the bowling game. But there’s no rule saying you can have only one scene in a project, so let’s leave the cube/dance scene intact and just make a copy of it to use as the beginnings of the bowling scene.

From the previous chapter, you should have the Unity Editor still open to the dance (cube) scene. The quickest way to start working with a copy of this scene is to select “Save Scene as” in the File menu (Figure [6-1](#A310332_2_En_6_Chapter.html#Fig1)).

![A310332_2_En_6_Fig1_HTML.jpg](Images/A310332_2_En_6_Fig1_HTML.jpg)

Figure 6-1.

Saving the scene as a new scene

This will be a bowling scene, so let’s name it Bowl (Figure [6-2](#A310332_2_En_6_Chapter.html#Fig2)).

![A310332_2_En_6_Fig2_HTML.jpg](Images/A310332_2_En_6_Fig2_HTML.jpg)

Figure 6-2.

The Bowl scene saved at the top level of Assets Note

To be consistent with the asset organization system, scenes would go into a Scenes folder, but if I have just one or a few scenes, I often just leave them saved at the top level of the Assets folder.

Instead of using “Save Scene as,” you could have duplicated the scene by selecting it in the Project view and invoking Duplicate from the Edit menu (or the keyboard shortcut Command+D), then renamed the new scene, and finally switched to it by double-clicking the scene file.

### Delete GameObjects

Now you can clean out anything from the scene that’s not needed for bowling without fear of mangling the dance scene . So, instead of deactivating the skeleton, just delete it by selecting the skeleton GameObject in the Hierarchy view and using the Delete command from the Edit menu (or the Command+Delete keyboard shortcut). Also, delete the cube hierarchy and Caribbean Isle GameObject (Figure [6-3](#A310332_2_En_6_Chapter.html#Fig3)), as cubes and music won’t be necessary for bowling, either.

![A310332_2_En_6_Fig3_HTML.jpg](Images/A310332_2_En_6_Fig3_HTML.jpg)

Figure 6-3.

Deleting unneeded GameObjects from the bowling scene

### Adjust the Light

Once again, a little housekeeping is in order before starting work on the new scene . Let’s start with the color of the light. Beginning with white lights until all the assets are in the scene allows you to see everything in the full original colors. So, let’s change the light color to white by clicking in its Color field and selecting white in the color chooser (Figure [6-4](#A310332_2_En_6_Chapter.html#Fig4)).

![A310332_2_En_6_Fig4_HTML.jpg](Images/A310332_2_En_6_Fig4_HTML.jpg)

Figure 6-4.

Set the light color to white

Also, since the point light was actually changed to a directional light in the previous chapter to support soft shadows , the name Point Light is misleading. This is a good time to change its name to just Light, which will keep your options open if you want to change its type again later.

### Retile the Floor

The shiny patterned floor from the dance scene doesn’t look much like a bowling lane, so let’s change its appearance . This is an opportunity to try Unity’s support for procedural materials. Procedural materials use textures generated from algorithms instead of image files. This allows a lot of variation in the materials by tweaking the texture-generation parameters and also potentially offers space savings since the procedural materials don’t require loading image files as textures.

Let’s try a procedural material for the floor. Search for substance in the Asset Store window (the Project view searches among individual assets, so in this case it won’t bring up any Asset Store results). A number of free substance packages from Mikołaj Spychał will appear in the Asset Store window (Figure [6-5](#A310332_2_En_6_Chapter.html#Fig5)).

![A310332_2_En_6_Fig5_HTML.jpg](Images/A310332_2_En_6_Fig5_HTML.jpg)

Figure 6-5.

The Asset Store results for substance

The package you want is Parquets – Substances. Download and import that package, and a Substances archive (a file with the `.sbsar` extension) will show up in the Project view in a Parquet01 folder (Figure [6-6](#A310332_2_En_6_Chapter.html#Fig6)).

![A310332_2_En_6_Fig6_HTML.jpg](Images/A310332_2_En_6_Fig6_HTML.jpg)

Figure 6-6.

The Project view of the Substances package

The Substances folder contains a ProceduralMaterial asset (ProceduralMaterial is a subclass of Material) and a supporting texture and script. Clicking the right arrow of the Parquet01 icon will change it to a left arrow and expand to show the procedural material of WoodenParquet followed by its associated textures (Figure [6-7](#A310332_2_En_6_Chapter.html#Fig7)).

![A310332_2_En_6_Fig7_HTML.jpg](Images/A310332_2_En_6_Fig7_HTML.jpg)

Figure 6-7.

An expanded view of the WoodenParquet from the Substances archive

The WoodenParquet procedural material looks like an okay candidate for a bowling lane floor (mostly because it is for free), so drag the WoodenParquet procedural material (not the archive) to the plane in the Hierarchy view. You can see in the Scene view that the plane now has a wooden parquet floor (Figure [6-8](#A310332_2_En_6_Chapter.html#Fig8)).

![A310332_2_En_6_Fig8_HTML.jpg](Images/A310332_2_En_6_Fig8_HTML.jpg)

Figure 6-8.

The plane with the WoodenParquet procedural material

The tiles look overly large, but you can adjust how the textures (there’s a main texture and a normal map texture) stretch across the plane by editing the UV scale fields of the textures. Select the plane in the Hierarchy view, and in the Inspector view set the x and y Tiling values (in the Procedural Properties section) of both textures to 5 instead of 1 (Figure [6-9](#A310332_2_En_6_Chapter.html#Fig9)).

![A310332_2_En_6_Fig9_HTML.jpg](Images/A310332_2_En_6_Fig9_HTML.jpg)

Figure 6-9.

The Inspector view of the plane with a procedural material

Now the textures are tiled five times, where before you just had one (Figure [6-10](#A310332_2_En_6_Chapter.html#Fig10)). In the Inspector view, notice that you have a number of procedural properties you can play with to vary the appearance of the generated textures.

![A310332_2_En_6_Fig10_HTML.jpg](Images/A310332_2_En_6_Fig10_HTML.jpg)

Figure 6-10.

The plane with its Tiling values set to 5

And as with the light, while you have the Inspector view up, this is a good time to change the name in the top text field and rename the plane to Floor to make its function more obvious.

### Reset the Camera

The script attached to the Main Camera isn’t suitable for the bowling game, so you can disable that script. Better yet, remove the script entirely from the Main Camera by selecting the Main Camera in the Hierarchy view; then in the Inspector view, right-click the script component and select Remove Component (Figure [6-11](#A310332_2_En_6_Chapter.html#Fig11)).

![A310332_2_En_6_Fig11_HTML.jpg](Images/A310332_2_En_6_Fig11_HTML.jpg)

Figure 6-11.

Removing the script from the Main Camera

While you’re at it, set the Main Camera position to (6,1,-10) and its rotation to (0,-90,0). Now you should be looking at a nice floor and skyline and not much else (Figure [6-12](#A310332_2_En_6_Chapter.html#Fig12)).

![A310332_2_En_6_Fig12_HTML.jpg](Images/A310332_2_En_6_Fig12_HTML.jpg)

Figure 6-12.

The Game view with a fixed Main Camera

## Make a Ball

Now the scene has a light, a flare, and a floor, as well as a Main Camera with a fixed viewpoint. It’s time to add the bowling ball .

### Make a Sphere

Two primitive GameObjects have been used so far in this book: Plane and Cube . For a ball, there’s another primitive that’s perfect: Sphere. Like Plane and Cube, Sphere can be selected from the Create submenu of the GameObject menu on the menu bar, but a sphere can also be instantiated using the Create button on the top left of the Hierarchy view (Figure [6-13](#A310332_2_En_6_Chapter.html#Fig13)).

![A310332_2_En_6_Fig13_HTML.jpg](Images/A310332_2_En_6_Fig13_HTML.jpg)

Figure 6-13.

Creating a ball

After selecting Sphere from the Create menu, a new GameObject named Sphere should be listed in the Hierarchy view. Select the GameObject named Sphere in the Hierarchy view so it can be examined in the Inspector view (Figure [6-14](#A310332_2_En_6_Chapter.html#Fig14)).

![A310332_2_En_6_Fig14_HTML.jpg](Images/A310332_2_En_6_Fig14_HTML.jpg)

Figure 6-14.

The Inspector view of the ball

Like the Cube and Plane primitives, the GameObject named Sphere has a MeshFilter component and a MeshRenderer component, plus a Collider component that is of the same shape as the primitive (in this case, a SphereCollider ). First, in the spirit of meticulously naming GameObjects, change the name of the sphere to Ball so it’s clear that this GameObject is the bowling ball. Then set the ball’s position to (0,1,5), which places the center of the ball 1 above the floor. Since the radius of the ball is 0.5 meters (the Sphere Collider’s Radius setting shown in Figure [6-14](#A310332_2_En_6_Chapter.html#Fig14) is a good hint), this gives some clearance between the ball and floor. And when you click Play, the ball will hang in the air above the floor (Figure [6-15](#A310332_2_En_6_Chapter.html#Fig15)).

![A310332_2_En_6_Fig15_HTML.jpg](Images/A310332_2_En_6_Fig15_HTML.jpg)

Figure 6-15.

The ball in the air

This is as expected, of course. Just like everything else created so far—the floor, the cubes, and even the dancing character—the ball will go or stay where it’s told.

### Make It Fall

For the ball to fall in response to gravity and respond to other forces, the ball must be made physical. A GameObject is made physical by adding a Rigidbody component .

Note

Most game physics is known as rigid body simulation , which is pretty much what it sounds like—simulating how hard objects with nonchanging shapes react to forces and collision. A bowling ball is perfect for rigid body simulation. A blob of tapioca pudding, on the other hand, is not.

To add a Rigidbody component to the ball, select the ball in the Hierarchy view; then in the Component menu on the menu bar, choose the Physics menu item and then select Rigidbody (Figure [6-16](#A310332_2_En_6_Chapter.html#Fig16)). You could also click the Add Component button on the bottom of the Inspector view to add a Rigidbody component .

![A310332_2_En_6_Fig16_HTML.jpg](Images/A310332_2_En_6_Fig16_HTML.jpg)

Figure 6-16.

Adding a Rigidbody component to the ball

The Inspector view now shows a Rigidbody component attached to the ball (Figure [6-17](#A310332_2_En_6_Chapter.html#Fig17)).

![A310332_2_En_6_Fig17_HTML.jpg](Images/A310332_2_En_6_Fig17_HTML.jpg)

Figure 6-17.

The Inspector view of the ball with a Rigidbody component

Most of the Rigidbody properties can be used with their default values, but a mass of 1 kilogram is a bit light for a bowling ball. Let’s set it to 5, as 5 kg is in the ballpark for a bowling ball weight.

The minimal values for Drag (air resistance) and Angular Drag (the drag experienced from rotating) are fine. There’s no reason to increase the drag unless, say, you’re bowling underwater.

The Use Gravity property specifies that the ball will respond to gravitational force. If the check box is not selected, then the ball will behave as if it were in a zero-G environment (bowling in space!).

The Is Kinematic property is used for GameObjects that move and collide but don’t respond to forces (e.g., an elevator platform). Any GameObject with a Collider component that is going to move should also have a Rigidbody component attached, and if the movement is not from physics but from scripting or animation, then it should be a kinematic Rigidbody.

The Interpolation property allows smoothing of the GameObject’s movement (at a computational cost).

Likewise, the Collision Detection property provides improved collision detection, in particular for fast-moving GameObjects (again, at a computational cost).

The Constraints property can limit how the GameObject can move in response to forces. For example, a GameObject can be limited to movement along one plane or even just one direction.

With the Rigidbody component properties appropriately set, click Play again. Now the ball drops, and even better, it lands on the floor! (See Figure [6-18](#A310332_2_En_6_Chapter.html#Fig18).)

![A310332_2_En_6_Fig18_HTML.jpg](Images/A310332_2_En_6_Fig18_HTML.jpg)

Figure 6-18.

The Game view of the ball landing on the floor

The default gravitational force, by the way, is standard Earth gravity, approximately 9.8 m/s². This can be customized in the PhysicsManager (Figure [6-19](#A310332_2_En_6_Chapter.html#Fig19)), available under the Project Settings submenu of the Edit menu.

![A310332_2_En_6_Fig19_HTML.jpg](Images/A310332_2_En_6_Fig19_HTML.jpg)

Figure 6-19.

The PhysicsManager

The Gravity property of the PhysicsManager is a vector (specifically, a Vector3 if you access the PhysicsManager from a script), with the only nonzero value in the y direction (and negative, so the force is downward). Thus, not only could you change the gravitational force to, say, that of the moon, you could even change its direction! If you change the y value from -9.81 to 9.81, the ball will fall upward, and if you change the gravitational vector from (0,-9.81,0) to (1,0,0), the ball will fall sideways at 1 m/s². And if you want weightlessness, a gravitational force of (0,0,0) will do the trick.

## Customize the Collision

A Collider component provides a GameObject with a collision shape. If you examine the ball and floor in the Inspector view, you’ll see each has a Collider component that was automatically attached and has a shape matching its mesh. The floor has a MeshCollider component, which always matches the GameObject’s mesh, and the ball has a SphereCollider component, which always has a spherical shape. When you select the ball or floor, the Scene view highlights not only the mesh of the GameObject but also the shape of its Collider component. You can toggle the display of Collider gizmos using the Gizmos menu in the Scene view.

There is almost a one-to-one correspondence among primitive GameObjects and primitive colliders (by “primitive” I mean it’s built in to Unity and also that the shape is simple, which is a good thing for performance). There are exceptions, though. For example, there is a primitive Cylinder GameObject that you can create from the GameObject menu but no available Collider in the shape of a cylinder. Instead, if you create a Cylinder, it automatically uses a CapsuleCollider.

The MeshCollider is a special case. Automatically following the shape of the associated mesh sounds good, but it’s only a good idea for static (i.e., nonmoving) GameObjects. Anything that is moving or might move should have a primitive collider or an aggregation of primitive colliders (more about that in the next chapter). A MeshCollider also has to have its Convex property (check box) enabled if it’s to collide with other MeshColliders.

### PhysicMaterials

Collider components dictate at what point two GameObjects will collide, but what actually happens when the collision is detected? Does one GameObject bounce off the other, and if so, by how much? Will the ball slide or roll along the floor or just stick to it? That’s where the Material property of a Collider component comes in. The property name is a little bit misleading, as the material of a Collider component isn’t the same material used with MeshRenderer components to determine the appearance of a mesh surface. Instead, a Collider component uses a PhysicMaterial to determine the collision properties of the collision surface. The spelling is a little odd, but I think of a PhysicMaterial as a physics material or physical material.

Note

Unlike ProceduralMaterial, the PhysicMaterial class is not a subclass of Material.

The Material property for Collider components defaults to the Default Material property in the PhysicsManager (Figure [6-19](#A310332_2_En_6_Chapter.html#Fig19)), which is None. To customize its collision behavior, a Collider component has to have a PhysicMaterial assigned.

### Standard PhysicMaterials

You can create a new PhysicMaterial from scratch from the Project view’s Create menu, but Unity provides some free PhysicMaterials in the Standard Assets package on the Unity Store. If you have not already downloaded and imported the Standard Assets package from the Unity Store, now would be a good time to do this.

![A310332_2_En_6_Fig20_HTML.jpg](Images/A310332_2_En_6_Fig20_HTML.jpg)

Figure 6-20.

Downloading the Standard Assets package from the Asset Store

Once it’s downloaded, you can see several PhysicsMaterials in the Assets ➤ Standard Assets ➤ PhysicsMaterials folder .

![A310332_2_En_6_Fig21_HTML.jpg](Images/A310332_2_En_6_Fig21_HTML.jpg)

Figure 6-21.

The PhysicsMaterials assets in the Standard Assets package

### Anatomy of a PhysicMaterial

Select among the various PhysicMaterials and compare their values in the Inspector view. For example, check out the differences between the Bouncy and Ice PhysicMaterials (Figure [6-22](#A310332_2_En_6_Chapter.html#Fig22)).

![A310332_2_En_6_Fig22_HTML.jpg](Images/A310332_2_En_6_Fig22_HTML.jpg)

Figure 6-22.

Comparison of Bouncy and Ice PhysicMaterials

Notice how Ice has particularly low friction values . Dynamic Friction is the friction that occurs while one object is sliding over another. Static Friction is the friction of an object resting on the other. The range for friction is 0 to 1, where 0 is frictionless and 1 means no sliding at all. Ice also has a Bounciness value of 0, which makes sense, since ice doesn’t bounce. And the Bouncy PhysicMaterial has a maximum Bounciness value of 1.

What happens when a collision occurs between GameObjects with two different PhysicMaterials? Their Friction and Bounciness values are combined according to the Friction Combine and Bounce Combine values, respectively. The Friction Combine value for Bouncy indicates that when it collides or slides along another PhysicMaterial, the applied friction is the average of the friction values from the two materials. And the Bounce Combine value for Bouncy specifies that the maximum bounciness from the two PhysicMaterials will be used (and since Bouncy already has the maximum bounciness value of 1, that will always be the result).

You might be wondering what happens if the two PhysicMaterials have different combine values, as do Bouncy and Ice. Which one will be used? Well, as of this writing, it’s not actually documented officially, but the precedence order, from low to high, appears to be Average, Multiply, Minimum, and Maximum. So if Bouncy and Ice collide, it will result in Minimum friction, which is the Ice friction at 0.1, and Maximum bounciness, which is the Bouncy bounciness of 1\. That is what you would expect!

The last three properties shown in the Inspector view of the PhysicMaterials allow for anisotropic friction, meaning different friction values in different directions. If the Friction Direction property is filled in with a nonzero vector, then the secondary static and dynamic friction takes effect in that direction. For the wood-tiled floor, you might specify a secondary friction direction that runs along the grain of the wood and lower static and dynamic friction values along that direction.

### Apply the PhysicMaterial

You can drag any of these standard PhysicMaterials into the Material fields of the Floor and Ball Collider components or click the little circle on the right of the Collider component’s Material field to select from a pop-up (Figure [6-23](#A310332_2_En_6_Chapter.html#Fig23)). Or you can drag the PhysicMaterial from the Project view onto the GameObject in the Inspector view, and the PhysicMaterial will show up in the right place (i.e., the Material field of the Collider component ).

![A310332_2_En_6_Fig23_HTML.jpg](Images/A310332_2_En_6_Fig23_HTML.jpg)

Figure 6-23.

Adding a Bouncy PhysicMaterial to the ball

Go ahead and try some different PhysicMaterials on the floor and ball. Choose the Bouncy PhysicMaterial for the ball and metal for the floor. Then click Play and watch the ball bounce and bounce and bounce; then switch the ball to Ice, and watch the ball land with a thud.

Instead of swapping different PhysicMaterials in and out of a Collider component, you could adjust the PhysicMaterials themselves. For example, you could select the Bouncy PhysicMaterial in the Project view and then in the Inspector view edit the PhysicMaterial properties. But since those PhysicMaterials are in a Standard Assets package, they could get replaced if you ever reimport the package (either accidentally or deliberately if there is an upgrade). Also, if a PhysicMaterial is used by more than one GameObject, changing its properties would affect all of those GameObjects.

The clean and safe solution here is to create a new PhysicMaterial for every GameObject that needs one. Conceptually, you would expect a unique PhysicMaterial anytime you also need a unique material. To put it another way, a unique surface would have its own material and PhysicMaterial. For instance, the ball and the floor should have their own PhysicMaterials, while all of the bowling pins (that you’ll create in the next chapter) should share the same PhysicMaterial.

### Make a New PhysicMaterial

Before creating new PhysicMaterials , for organizational purposes, you should create a folder to contain them. Select the top-level Assets folder in the left panel of the Project view. Then using the Create menu in the Project view, create a new folder and name it Physics (it’s shorter and less oddly spelled than PhysicMaterials).

You can make a new PhysicMaterial using the Create menu in the Project view and then fill in all the properties of the new PhysicMaterial in the Inspector view. But you can save yourself some work by copying a PhysicMaterial from Standard Assets, preferably one that is already close to what you want. Given that the floor has a wood material, it’s convenient to start with the Wood PhysicMaterial in Standard Assets. Make a copy of the Wood PhysicMaterial by selecting it in the Project view and invoking Duplicate from the Edit menu (or Command+D for the keyboard shortcut).

Drag the copy of the Wood PhysicMaterial to your new Physics folder in the Project view and rename it Floor, since you’ll apply it to your GameObject named Floor. Then duplicate the Floor PhysicMaterial and name the new one Ball (Figure [6-24](#A310332_2_En_6_Chapter.html#Fig24)) since you’ll apply that one to your GameObject named Ball.

![A310332_2_En_6_Fig24_HTML.jpg](Images/A310332_2_En_6_Fig24_HTML.jpg)

Figure 6-24.

Custom PhysicMaterials

Now there’s a PhysicMaterial for the floor and one for the ball. To use these PhysicMaterials on their intended GameObjects, drag the Floor PhysicMaterial onto the Floor GameObject in the Hierarchy view and drag the Ball PhysicMaterial onto the Ball GameObject. (You may want to check the two GameObjects in the Inspector view to verify the PhysicMaterials are showing up in the Collider component’s Material fields.) Now you’re ready to start tweaking the PhysicMaterial properties.

As you would expect, the Wood PhysicMaterial already has values reasonable for a wood floor, so you can leave it alone. But you want to tweak the Ball Physic Material, so select the Ball PhysicMaterial and set its values as shown in Figure [6-25](#A310332_2_En_6_Chapter.html#Fig25).

![A310332_2_En_6_Fig25_HTML.jpg](Images/A310332_2_En_6_Fig25_HTML.jpg)

Figure 6-25.

Adjusted values for the Ball PhysicMaterial

The ball shouldn’t bounce on the floor (or off the bowling pins when you add them later), so set the Bounciness value to 0 and Bounce Combine to Minimum, which will ensure that the ball never bounces at all no matter what it collides with. Then set the Dynamic and Static Friction values to 1 and Friction Combine to Maximum, so the ball always has maximum friction whether it’s at rest or rolling. The maximum friction values ensure the ball rolls instead of slides.

Now when you click Play, the ball should just drop on the floor and come to rest without bouncing.

## Make It Roll

To get the ball rolling, you’ll need to add some controls to the game (for it to be even a game, in fact). The original arcade version of HyperBowl was controlled by a real bowling ball that acted as a large trackball . Spinning that ball would impart a spin on the ball in the game (resulting in comical body movement while trying to bowl up the hills of San Francisco).

Of course, building an air-supported bowling ball peripheral is outside the scope of this book, but the PC version of HyperBowl provided a mouse-based version of this control, in which pushing the mouse would impart a spin on the ball in that direction. This control is fairly straightforward to implement with just a single script.

### Create the Script

Select the Scripts folder in the Project view, create a new JavaScript script using the Create button on the top left of the Project view, and name the new script FuguForce since you’ll be using it to apply a force to the ball. (I’m tempted to call it FuguRoll for the sushi connotation, but I don’t want the name to imply that you’re applying a torque, a rotational rather than a linear force.) Then drag the FuguForce script onto the ball in the Hierarchy view so you’ll be ready to test it as soon as you add some code to the script.

### Update: Gather Input

Attempting to calculate rotations and translations (changes in position) to simulate rolling the ball would be complicated, involving collision detection and responses with the floor and pins and taking in account gravity and friction and bouncing…all the calculations that the physics engine already does. It would be a waste not to leave that aspect up to the Unity physics system.

And because the ball already has a Rigidbody component, it is already a physical object subject to forces, including gravity, and responding to collisions, including the floor. So, all that’s needed to roll the ball is a push. How much of a push depends on the input, which, in this design, is the movement of the mouse. Note the original, non-Unity version of HyperBowl applied a torque (rotational force) to the bowling ball, effectively spinning it. Unity does have a script function to apply torque to a Rigidbody component, called Rigidbody.AddTorque, but I’ve found that it works better in Unity to roll the ball by pushing it with a linear force using Rigidbody.AddForce.

The mouse movement, and input in general, can be checked every frame, so the input-gathering code and computation of the corresponding push should go in the Update callback of the script, as shown in Listing [6-1](#A310332_2_En_6_Chapter.html#Par70). Copy that listing into the FuguForce script.

```
#pragma strict
var mousepowerx:float = 1.0;
var mousepowery:float = 1.0;
private var forcex:float=0.0;
private var forcey:float=0.0;
function Update() {
forcex = mousepowerx*Input.GetAxis("Mouse X")/Time.deltaTime;
forcey = mousepowery*Input.GetAxis("Mouse Y")/Time.deltaTime;
}
Listing 6-1.
FixedUpdate Callback in the BallController Script
```

The script begins with the variables mousepowerx and mousepowery used to scale force applied the ball. mousepowerx affects the force resulting from moving the mouse left or right, and mousepowery affects the force resulting from moving the mouse forward and backward. The variables are public, so they can be adjusted in the Inspector view.

The final calculated force is stored in the private variables forcex and forcey, also corresponding to left-right and forward-backward mouse movement, respectively.

Tip

It’s a good idea to always specify an initial value for variables in their declarations. That makes the meaning of the code clearer and avoids errors because of wrong assumptions about the initial values (C and C++ programmers especially know to be wary of mystery bugs from uninitialized variables).

The Update callback is where forcex and forcey are calculated from mousepowerx and mousepowery and the mouse movement. Input in Unity is queried using the Input class. For example, the mouse position can be checked by examining the static variable Input.mousePosition, so the mouse movement could be ascertained by saving the mouse position in each Update call and comparing the current position with the saved position from the previous frame.

But the higher-level Input.GetAxis function does that work already. Input.Getaxis returns a value from -1 to 1 for left-right or forward-backward movement from a mouse, joystick, or keyboard, depending on the argument passed to the function.

In the Update callback, Input.GetAxis(“Mouse X”) is called to obtain the left-right mouse movement, and Input.GetAxis(“Mouse Y”) is called for the front-back movement that occurred over the previous frame. The results are assigned to the variables forcex and forcey, respectively, after taking into account how much time elapsed during the frame (our old friend Time.deltaTime) and multiplying by scaling factors mousepowerx and mousepowery.

With this code in the FuguForce script, you can see the public mousepower variables show up in the Inspector view of the ball (Figure [6-26](#A310332_2_En_6_Chapter.html#Fig26)).

![A310332_2_En_6_Fig26_HTML.jpg](Images/A310332_2_En_6_Fig26_HTML.jpg)

Figure 6-26.

The ball with the FuguForce.js script

When you click Play, you still don’t have any control over the ball, since you haven’t added the actual ball-pushing code yet. However, if you select the Debug option in the Inspector view menu, the private forcex and forcey variables will display, and you can see them change as you move the mouse.

### FixedUpdate: Use the Force

Functions that affect physics, including Rigidbody.AddForce, should be invoked in the FixedUpdate callback . Unlike the Update callback, which is called once per frame and can take any amount of time, the FixedUpdate callback takes place after every fixed time step has elapsed. For good results, physics simulations need to run at a fixed interval, typically with more frequency than Update. Varying intervals between physics computations can result in varying behavior and long lags between physics updates. In Unity, this time step is set in the TimeManager (Figure [6-27](#A310332_2_En_6_Chapter.html#Fig27)), which is available in the Project Settings submenu of the Edit menu.

![A310332_2_En_6_Fig27_HTML.jpg](Images/A310332_2_En_6_Fig27_HTML.jpg)

Figure 6-27.

The TimeManager

The FixedUpdate callback is called at these physics update intervals (I think of FixedUpdate really as PhysicsUpdate and of Fixed Timestep as PhysicsTimestep), so this is where any change to the physics simulation should take place, especially any action on a Rigidbody.

Since the force for pushing the ball is already calculated in the Update callback, the FixedUpdate callback just needs to apply those values in a call to Rigidbody.AddForce on the ball’s Rigidbody component (Listing [6-2](#A310332_2_En_6_Chapter.html#Par82)).

```
function FixedUpdate() {
rigidbody.AddForce(forcex,0,forcey);
}
Listing 6-2.
FixedUpdate Callback in FuguForce.js
```

The Rigidbody function AddForce is called on the variable rigidbody, which is a Component variable that always references the Rigidbody component or this GameObject. The GameObject class also has a rigidbody variable, so a reference to gameObject.rigidbody would be equivalent.

The three values passed to RigidBody.AddForce are the x, y, and z values of the force, which is a vector since it has direction and magnitude. The direction of the force is in world space, and from the viewpoint of the Main Camera in this game, x is left-right and z is forward-backward. So, forcex is passed in for the x argument and forcey for the z argument. The controls don’t push the ball up or down, just forward and sideways, so 0 is passed in as the y argument.

Note

Be sure to check the Script Reference page on RigidBody.AddForce. It is an overloaded function with a variation that takes the force as a Vector3 instead of three separate numbers. Also, both variations take an optional argument that can specify the value applied to the Rigidbody is a quantity besides force—either acceleration, an impulse, or an instantaneous change in velocity.

Now when you click Play and move your mouse, the ball rolls in that direction. Try changing the mousepowerx and mousepowery values in the Inspector view to get the roll responsiveness you want.

### Is It Rolling?

You might notice that if you move the mouse while the ball is falling, you can actually push the ball while it’s still in the air, which doesn’t seem right. Moving the mouse should roll the ball only when it’s on a surface.

Fortunately, Unity has callback functions, each prefixed by OnCollision, that are called when a GameObject collides with another GameObject and when the contact ceases. Each of the callbacks takes one argument, a Collision object that contains information about the collision, such as a reference to the other GameObject that was involved.

The FuguForce script could check if the colliding GameObject is the floor by checking whether its name is Floor (using the GameObject name variable). But in general it’s a better practice and more efficient to find and identify GameObjects by their tags (using the GameObject tag variable). If you select the GameObject named Floor and examine it in the Inspector view, you can see in the upper left it has the default tag Untagged. To change the GameObject’s tag to Floor, the Floor tag must first be created by selecting Add Tag from the Tag menu of the GameObject (Figure [6-28](#A310332_2_En_6_Chapter.html#Fig28)).

![A310332_2_En_6_Fig28_HTML.jpg](Images/A310332_2_En_6_Fig28_HTML.jpg)

Figure 6-28.

Adding a new tag

This brings up the TagManager in the Inspector view. The TagManager manages tags and layers. Tags are used to identify GameObjects, so you can create practically an unlimited number of them. Layers, on the other hand, are used to label groups of GameObjects, and at most 32 layers can be defined since they are implemented as bits in a 32-bit number. This makes it convenient to specify combinations of layers, for example, in the Culling Mask properties of cameras and lights.

To create a new tag named Floor, just type Floor into the first empty tag field in the TagManager (Figure [6-29](#A310332_2_En_6_Chapter.html#Fig29)). Then select the GameObject named Floor in the Hierarchy view again, click the Tag menu in the Inspector view again, and the new Floor tag is available for selection (this might not be visible until you restart Unity ).

![A310332_2_En_6_Fig29_HTML.jpg](Images/A310332_2_En_6_Fig29_HTML.jpg)

Figure 6-29.

The floor with the Floor tag added

Now the FuguForce script can be augmented with collision callbacks to see whether the ball is on the floor (Listing [6-3](#A310332_2_En_6_Chapter.html#Par93)).

```
private var isRolling:boolean=false;
private var floorTag:String = "Floor";
function Update() {
forcex = mousepowerx*Input.GetAxis("Mouse X")/Time.deltaTime;
forcey = mousepowery*Input.GetAxis("Mouse Y")/Time.deltaTime;
}
function FixedUpdate() {
if (isRolling && GetComponent.().velocity.sqrMagnitude().AddForce(forcex,0,forcey);
}
}
function OnCollisionEnter(collider:Collision) {
if (collider.gameObject.tag == floorTag) {
isRolling = true;
}
}
function OnCollisionStay(collider:Collision) {
if (collider.gameObject.tag == floorTag) {
isRolling = true;
}
}
function OnCollisionExit(collider:Collision) {
if (collider.gameObject.tag == floorTag) {
isRolling = false;
}
}
Listing 6-3.
Collision Callbacks in FuguForce.js
```

Two private variables are added. The first variable, isRolling, is true when the ball is on the floor. The second variable, floorTag, is the tag assigned to the floor.

Tip

Defining a variable for the tag avoids having to spell out the tag in multiple locations (and avoids hard-to-debug spelling errors), and if the name of the tag is changed later, only the variable needs to be updated. Each of the three collision callbacks checks if the GameObject the ball collided against is the floor by checking the tag of the tag of the colliding GameObject. If the GameObject is not the floor, then the collision callback doesn’t do anything.

The OnCollisionEnter callback is called by Unity when the ball collides with another GameObject, so that function sets isRolling to true. Conversely, OnCollisionExit is called by Unity when contact ceases between the ball and a GameObject, and in that case isRolling is set to false.

In the meantime, OnCollisionStay is called while the ball stays in contact with a GameObject, so it also ensures that isRolling stays true. Now the FixedUpdate callback can check whether isRolling is true, indicating that the ball is on the floor, before applying a push to the ball (Listing [6-4](#A310332_2_En_6_Chapter.html#Par98)).

```
function FixedUpdate() {
if (isRolling && GetComponent.().velocity.sqrMagnitude().AddForce(forcex,0,forcey);
}
}
Listing 6-4.
Add a Rolling Check in FixedUpdate of FuguForce.js
```

### Limit the Speed

Finally, as a bit of polish, the ball control script can check the ball’s speed before pushing the ball, avoiding cases where the ball ends up rolling much faster than you intended because of some freak combination of existing velocity, frame rate, and input values (Listing [6-5](#A310332_2_En_6_Chapter.html#Par100)).

```
var maxVelocitySquared:float=400.0;
Listing 6-5.
Add a Speed Check in FixedUpdate of FuguForce.js
```

This is a pretty simple change, requiring only the addition of a variable called maxVelocitySquared to hold your maximum velocity value, which is actually the square of the maximum velocity value.

### The Complete Script

Listing [6-6](#A310332_2_En_6_Chapter.html#Par103) presents the complete listing for the ball controller script, `FuguForce.js`, put together so far. This is the version in the Chapter [6](../Text/A310332_2_En_6_Chapter.html) project at [`www.apress.com/9781484231739`](http://www.apress.com/9781484231739) .

```
#pragma strict
var mousepowerx:float = 1.0;
var mousepowery:float = 1.0;
var maxVelocitySquared:float=400.0;
private var forcey:float=0;
private var forcex:float=0;
private var isRolling:boolean=false;
private var floorTag:String = "Floor";
function Update() {
forcex = mousepowerx*Input.GetAxis("Mouse X")/Time.deltaTime;
forcey = mousepowery*Input.GetAxis("Mouse Y")/Time.deltaTime;
}
function FixedUpdate() {
if (isRolling && GetComponent.().velocity.sqrMagnitude().AddForce(forcex,0,forcey);
}
}
function OnCollisionEnter(collider:Collision) {
if (collider.gameObject.tag == floorTag) {
isRolling = true;
}
}
Listing 6-6.
Complete Listing of FuguForce.js
```

## Be the Ball

The Ball control works pretty well at this point, but the Main Camera just sits there while the ball rolls off into the distance. This is a glaring deficiency, as any decent 3D game has some kind of camera movement. In HyperBowl, the Main Camera follows the ball as you roll but always faces the same direction (toward the pins). It turns out Unity has a camera-follow script in Standard Assets. The Scripts package was already imported from Standard Assets in Chapter [3](../Text/A310332_2_En_3_Chapter.html) to obtain the MouseOrbit script. The script is also located in the Utility folder of the Standard Assets package and is called SmoothFollow (Figure [6-30](#A310332_2_En_6_Chapter.html#Fig30)).

![A310332_2_En_6_Fig30_HTML.jpg](Images/A310332_2_En_6_Fig30_HTML.jpg)

Figure 6-30.

The Utility scripts in Standard Assets

You will note that this is a C# script. C# is a common language for creating video games. Drag the SmoothFollow script from the Project view onto the Main Camera in the Hierarchy view. Then select the Main Camera in the Hierarchy view so you can edit the SmoothFollow script properties in the Inspector view (Figure [6-31](#A310332_2_En_6_Chapter.html#Fig31)).

![A310332_2_En_6_Fig31_HTML.jpg](Images/A310332_2_En_6_Fig31_HTML.jpg)

Figure 6-31.

The Inspector view of the Main Camera with the SmoothFollow script

The Main Camera should follow the ball, so drag the GameObject named Ball from the Hierarchy view into the SmoothFollow script’s Target field in the Inspector view .

As for the other SmoothFollow properties, set Distance to 2, which will leave the Main Camera trailing 2 m behind the Ball, and set Height to 1, so the camera stays 1 m above the ball instead of following right behind it. A Height Damping setting of 2 allows the Main Camera to spring up and down a bit while following the ball’s height, and a Rotation Damping setting of 0 ensures the Main Camera will always stay behind the ball and not swing around to the left or right.

Now click Play, and as you roll around, you’re rolling and following!

## Explore Further

Now we have the beginnings of real gameplay. It’s not a complete bowling game yet, but it has physics (a ball rolling on a floor), interactive control (pushing the ball), and camera movement (following the ball). Each of those features is a rich topic in game development, but in particular you’ll be digging deeper into physics in the next chapter with the addition bowling pins and collision-based sound effects. Then the game will look a lot more like a bowling game!

### Unity Manual

In the “Asset Import and Creation” section, the “Procedural Materials” page describes the Substance archive format a little bit and provides an overview of the tools used to create and analyze procedural materials.

In the “Creating Gameplay” section, the “Input” page describes the Input class and its GetAxis function used in the BallController script implemented in this chapter and lists the joystick and keyboard values passed to that function. The lower portion of that page describes input functions and variables available for iOS, so you can read that for a preview of this book’s chapter on iOS input.

This chapter has been mostly about physics, so the “Physics” page is the most important to read. It lists all the collider types, including the Sphere Collider, Box Collider, and Mesh Collider used in this chapter. The page describes the differences between rigid bodies (the ball) and static colliders (the floor) and how physic materials and their properties affect collision behavior.

### Reference Manual

In the “Asset Components” section, the “Procedural Material Assets” page explains what you see in the Project view and Inspector view for procedural materials.

The “Physics Components” section lists all the available colliders in detail, along with the available joints (including hinges and springs), constant force, and cloth components (cloth is not supported in Unity iOS).

### Scripting Reference

The Unity Scripting Reference is available on the Internet ( [`https://docs.unity3d.com/ScriptReference/index.html`](https://docs.unity3d.com/ScriptReference/index.html) ) and provides valuable information. The Unity Scripting Reference is also available directly from MonoDevelop. When you enter any of the Unity classes or functions, you can use the command key and the single quote key (⌘ +’) to bring up contextually relevant information. For example, after typing in the Input class, using the ⌘+’ key combination will open a page in your default web browser and show you relevant information about Input from the Unity Scripting Reference (Figure [6-32](#A310332_2_En_6_Chapter.html#Fig32)).

![A310332_2_En_6_Fig32_HTML.jpg](Images/A310332_2_En_6_Fig32_HTML.jpg)

Figure 6-32.

The Speed variable added to the ball

### On the Web

The physics implementation in Unity is based on an older version (2.x) of the PhysX engine, originally developed by NovodeX, acquired by Ageia and bundled with a hardware physics accelerator, and now a product of nVidia. The latest version (3.x) is available on the nVidia Developer Zone ( [`https://developer.nvidia.com/physx`](https://developer.nvidia.com/physx) `)`. The PhysX software development kit is worth a look just to get a better idea what physics engine features underlie the Unity physics components and script functions.

# 7. Let’s Bowl! Advanced Physics

At this point, you have the glimmerings of a bowling game, or at least a scene in the Unity Editor where the player can roll the ball around on the floor. But the game won’t look like a bowling game until it at least has bowling pins. That will be remedied in this chapter and involve more Unity physics, including collisions between Rigidbody components (among the ball and pins) and compound colliders (to accommodate the shape of a bowling pin). On top of that, you’ll add collision-based sound effects so the game will sound like a bowling game.

This additional functionality will require the construction of several new scripts, which are available in the Unity project for this chapter. You can access it, and all other source code for this book, via the Download Source Code button located at [`www.apress.com/9781484231739`](http://www.apress.com/9781484231739) . However, the barrel model used for the bowling pins and the audio for pin collisions and the ball-rolling sound come from the Asset Store and not included in the online project (but they are free).

## Lengthen the Lane

Before adding bowling pins to the bowl scene, which should be open in the Unity Editor as you left it in the previous chapter, you need some more room to roll. Select the floor in the Hierarchy view, and in the Inspector view (Figure [7-1](#A310332_2_En_7_Chapter.html#Fig1)) set Scale to 10 for X, Y, and Z.

![A310332_2_En_7_Fig1_HTML.jpg](Images/A310332_2_En_7_Fig1_HTML.jpg)

Figure 7-1.

The larger Floor GameObject, scaled up by a factor of 10 Tip

For performance reasons, it’s best to avoid changing the scale in the Transform component (for imported models, change the scale in the Import Settings dialog instead). If you have to change the scale, change it uniformly along all three axes.

The floor is now ten times larger (except in height since a plane has zero height). But the textures on the floor are also stretched, resulting in some really wide planks. To compensate, you should also adjust the tiling for the floor’s Main and Normalmap textures by a factor of ten, which results in a new Tiling factor of 50 in each direction. Now the wooden planks look as they originally did.

## Make Some Pins

Now that there’s room to roll, it’s time to add the pins. In lieu of a bowling pin model, you can make a simplistic version of a bowling pin with the Capsule primitive.

Select Capsule from the Create menu of the Hierarchy view (Figure [7-2](#A310332_2_En_7_Chapter.html#Fig2)). Capsule, like the other primitive models, is also available in the GameObject menu on the menu bar.

![A310332_2_En_7_Fig2_HTML.jpg](Images/A310332_2_En_7_Fig2_HTML.jpg)

Figure 7-2.

Creating a capsule

Name the resulting GameObject Pin. To create ten pins, you could duplicate the pin nine times, but in the same way the cube was cloned in the cube scene, it’s better to make a prefab first. This way, any changes you make to a single pin can be applied to all ten pins.

So, drag the pin into the Prefabs folder in the Project view to create the prefab (Figure [7-3](#A310332_2_En_7_Chapter.html#Fig3)).

![A310332_2_En_7_Fig3_HTML.jpg](Images/A310332_2_En_7_Fig3_HTML.jpg)

Figure 7-3.

Pin prefab

Creating a prefab allows you to make one copy of a game asset and apply it as many times as needed in the game. When you make changes to the prefab, these changes are applied to all the assets in the game.

## Place the Pins in the Scene

Now you are ready to place the pins in the scene. From the project folder, drag one the prefabs of the pin to the scene. You will need the pin to be in line with the ball. So, place it on the same x-axis as the ball (100,1,0) (Figure [7-4](#A310332_2_En_7_Chapter.html#Fig4)).

![A310332_2_En_7_Fig4_HTML.jpg](Images/A310332_2_En_7_Fig4_HTML.jpg)

Figure 7-4.

Initial pin Transform settings

Now, add nine more pins to form a typical bowling pin formation (a triangle).

![A310332_2_En_7_Fig5_HTML.jpg](Images/A310332_2_En_7_Fig5_HTML.jpg)

Figure 7-5.

Bowling with pins

If you test this now, you will find that as you bowl into the pins, the ball just bounces off them since the pins have Collider components but are static GameObjects. Like the ball, each pin needs to have a Rigidbody component to react to forces. A Rigidbody component can be added to the Pin prefab from the Component menu, but this time, let’s try it another way. Select the Pin prefab in the Project view, and then in the Inspector view click the Add Component button. In the resulting pop-up, under Physics, select Rigidbody (or you can find that selection by typing rigidbody in the search field of the pop-up). Whichever way you choose, there should now be a Rigidbody component on the Pin prefab (Figure [7-6](#A310332_2_En_7_Chapter.html#Fig6)).

![A310332_2_En_7_Fig6_HTML.jpg](Images/A310332_2_En_7_Fig6_HTML.jpg)

Figure 7-6.

Pin prefab with a Rigidbody component

Like the Floor and Ball Collider components, the Pin prefab should have its own PhysicMaterial. A quick way to create a suitable PhysicMaterial is to copy the one used for the ball, so select the Ball PhysicMaterial in your Physics folder in the Project view, duplicate it using the Edit menu or Command+D, and name the new PhysicMaterial Pin (Figure [7-7](#A310332_2_En_7_Chapter.html#Fig7)).

![A310332_2_En_7_Fig7_HTML.jpg](Images/A310332_2_En_7_Fig7_HTML.jpg)

Figure 7-7.

PhysicMaterial for the Pin prefab

Since the bowling pins should roll just as well as the ball, leave the Dynamic Friction and Static Friction values at 1 (to avoid slippage while rolling), and leave the Friction Combine value at Maximum. Unlike the ball, however, the bowling pins should be fairly bouncy, so set Bounciness to 0.5 and set the Bounce Combine value to Average (taking in account the bounciness of whatever it’s bouncing against). Finally, it’s time to assign the Pin PhysicMaterial to the Pin prefab. Select the Pin prefab in the Project view; then in the Inspector view, click the circle to the right of the PhysicMaterial field of the Collider component and select the Pin PhysicMaterial from the pop-up chooser (Figure [7-8](#A310332_2_En_7_Chapter.html#Fig8)).

![A310332_2_En_7_Fig8_HTML.jpg](Images/A310332_2_En_7_Fig8_HTML.jpg)

Figure 7-8.

Assigning the Pin PhysicMaterial to the Pin prefab

Now when you click Play and roll the ball into the pins, they get knocked around, as shown in Figure [7-9](#A310332_2_En_7_Chapter.html#Fig9), bouncing and rolling!

![A310332_2_En_7_Fig9_HTML.jpg](Images/A310332_2_En_7_Fig9_HTML.jpg)

Figure 7-9.

Collision with pins that all have Rigidbody components

## Use Camera Follow

You will note that the view of the impact of the ball hitting the pins may be in the distance, and the impact may not be very apparent to the player. So, what you need to do is get the camera to follow the bowling ball. To do this, you will need to create another script. In the Script folder, create a new C# script and name it CameraFollow with the contents of Listing [7-1](#A310332_2_En_7_Chapter.html#Par18).

```
using UnityEngine;
using System.Collections;
public class CameraController : MonoBehaviour {
public GameObject player;
private Vector3 offset;
void Start ()
{
offset = transform.position - player.transform.position;
}
Listing 7-1.
CameraFollow Script
```

You may need to adjust the starting position of the camera so that the starting position is just behind and above the ball.

## Keep Playing

It’s rather tiresome to have to stop the game and click Play again every time you want to test a roll. And when the ball rolls off the floor, you’re stuck watching the ball fall indefinitely. So, let’s have the ball and pins reset if the ball rolls off the edge of the floor.

### Return the Ball

First, you need a script that will restore a GameObject to its initial position and rotation (can’t forget rotation—if a pin is knocked down, you want to reset it to its initial standing orientation). Create a new JavaScript, call it FuguReset, and add the contents of Listing [7-2](#A310332_2_En_7_Chapter.html#Par22).

```
// restore GameObject to its original position and rotation
#pragma strict
private var startPos:Vector3;
private var startRot:Vector3;
function Awake() {
// save the initial position and rotation of this GameObject
startPos = transform.localPosition;
startRot = transform.localEulerAngles;
}
function ResetPosition() {
// set back to initial position
transform.localPosition = startPos;
transform.localEulerAngles = startRot;
// make sure we stop all physics movement
if (GetComponent.() != null) {
GetComponent.().velocity = Vector3.zero;
GetComponent.().angularVelocity = Vector3.zero;
}
}
Listing 7-2.
FuguReset.js Script Restores a GameObject’s Position
```

The Awake function saves the GameObject’s position and rotation in a couple of variables, and the function ResetPosition restores the GameObject to those settings. ResetPosition also checks whether the GameObject has a Rigidbody component. If so, then the function stops it from moving or rotating. The FuguReset script should be attached to every GameObject that will need to reset its position (and possibly rotation) when you restart the game. That list includes the ball, the pins, and the Main Camera. Start by dragging the FuguReset script onto the Main Camera and Ball GameObjects in the Hierarchy view. Then select the Pin prefab in the Project view, and in the Inspector view click the Add Component button to add the FuguReset script (Figure [7-10](#A310332_2_En_7_Chapter.html#Fig10)).

![A310332_2_En_7_Fig10_HTML.jpg](Images/A310332_2_En_7_Fig10_HTML.jpg)

Figure 7-10.

Attaching the FuguReset script to the Pin prefab

The Ball, Pin, and Main Camera GameObjects now record their original positions and rotations when the game starts, and you have a ResetPosition function ready to call that will restore them to those original positions and rotations.

### Send a Message

Normally, calling a function in a script attached to one GameObject from a script attached to another GameObject is a little bit tricky. It involves calling GetComponent on a GameObject to retrieve the script and then referencing that script to call the function.

But in simple cases (e.g., when you don’t care about the return value of the function), Unity allows you to call functions by sending messages to GameObjects using either the GameObject SendMessage function or GameObject BroadcastMessage function. A GameObject receiving a message, which consists of the name of the function to call, will pass that message on to any script that might have that function.

Let’s add some functions to your game controller script that will send ResetPosition messages (Listing [7-3](#A310332_2_En_7_Chapter.html#Par28)).

```
var ball:GameObject; // the bowling ball
Function ResetBall() {
ball.SendMessage("ResetPosition");
}
function ResetPins() {
for (var pin:GameObject in pins) {
pin.BroadcastMessage("ResetPosition");
}
}
function ResetCamera() {
Camera.main.SendMessage("ResetPosition");
}
function ResetEverything() {
ResetBall();
ResetPins();
ResetCamera();
}
Listing 7-3.
Functions to Send ResetPosition Messages in FuguBowl.js
```

The additional code begins with a public variable that will refer to the ball. The script already has a pins array that references all of the bowling pins, and the Main Camera can always be referenced through the static variable Camera.main, so now the script can access every GameObject that it needs to reset. The functions ResetCamera and ResetBall call SendMessage to send the ResetPosition message to their respective target GameObjects. Every ResetPosition function defined in scripts attached to those GameObjects will be called. Specifically, since the Main Camera and Ball GameObjects have the FuguReset script attached, the ResetPosition function in that script will respond to the message. The ResetPins function is a little bit different in that it calls BroadcastMessage to send the ResetPosition message . BroadcastMessage behaves the same as SendMessage except that the message is also propagated to any child GameObjects of the original recipient GameObject. This will be useful later in this chapter when you swap in some pins that have their Rigidbody components attached to child GameObjects.

### Check for Gutter Ball

When the ball rolls off the edge of the floor, it’s essentially a gutter ball, since there’s no hope of reaching the pins at this point. So, a reset of the game is definitely in order when this happens. The gutter ball can be implemented by checking in every frame whether the y position of the ball has dropped below a certain level. That can be implemented by adding the public variable and Update callback in Listing [7-4](#A310332_2_En_7_Chapter.html#Par31) to the FuguBowl script.

```
var sunkHeight:float = -10.0; // fall below this y position and we have a gutterball
function Update() {
if (ball.transform.position.y<sunkHeight) {
ResetEverything();
}
}
Listing 7-4.
Update Callback in FuguBowl.js to Test for Gutter Ball
```

The variable sunkHeight specifies what y position the ball has to fall below to reset everything. The default value of sunkHeight is some distance below zero, so the players has some time to see the ball fall before it resets. The Update callback checks every frame whether or not the ball’s y position is below sunkHeight. If it is, ResetEverything is called, which sends out all the ResetPosition messages to the Ball, Main Camera, and Pin GameObjects. Before you click Play to test this, you need to assign the ball public variable in the script by dragging the Ball GameObject from the Hierarchy view into the Ball field of the FuguBowl script (Figure [7-11](#A310332_2_En_7_Chapter.html#Fig11)).

![A310332_2_En_7_Fig11_HTML.jpg](Images/A310332_2_En_7_Fig11_HTML.jpg)

Figure 7-11.

Game controller properties in FuguBowl.js to support resetting the ball

Now when the ball rolls off the floor, the Ball, Main Camera, and Pin GameObjects should pop right back into their beginning positions. Continuous gameplay!

### The Complete Listing

Listing [7-5](#A310332_2_En_7_Chapter.html#Par35) gives the complete game controller script.

```
// bowling game controller
#pragma strict
var pin:GameObject; // pin prefab to instantiate
var pinPos:Vector3 = Vector3(0,1,20); // position to place rack of pins
var pinDistance = 1.5; // initial distance between pins
var pinRows = 4; // number of pin rows
var ball:GameObject; // the bowling ball
var sunkHeight:float = -10.0; // fall below this y position and we have a gutterball
private var pins:Array;
function Awake () {
CreatePins();
}
function CreatePins() {
pins = new Array();
var offset = Vector3.zero;
for (var row=0; row<pinRows; ++row) {
offset.z+=pinDistance;
offset.x=-pinDistance*row/2;
for (var n=0; n<=row; ++n) {
pins.push(Instantiate(pin, pinPos+offset, Quaternion.identity));
offset.x+=pinDistance;
}
}
}
function Update() {
if (ball.transform.position.y<sunkHeight) {
ResetEverything();
}
}
function ResetBall() {
ball.SendMessage("ResetPosition");
}
function ResetPins() {
for (var pin:GameObject in pins) {
pin.BroadcastMessage("ResetPosition");
}
}
function ResetCamera() {
Camera.main.SendMessage("ResetPosition");
}
function ResetEverything() {
ResetBall();
ResetPins();
ResetCamera();
}
Listing 7-5.
Complete Listing for FuguBowl.js
```

## Bowl for Barrels

Capsules make for simplistic bowling pins, both in appearance and in collision shape. A real bowling pin model would be much better, but unfortunately, searching for free bowling pins on the Asset Store turns up nothing (although that could change at any time if a vendor decides to fill the void). However, there are a lot of other models on the Asset Store that, if you’re flexible, could act as bowling pin substitutes. It turns out there are several packages of barrel models, so you’ll go with barrels as pins for your backup plan.

### Pick a Barrel

A search for barrel on the Asset Store reveals several free packages of barrel models (Figure [7-12](#A310332_2_En_7_Chapter.html#Fig12)).

![A310332_2_En_7_Fig12_HTML.jpg](Images/A310332_2_En_7_Fig12_HTML.jpg)

Figure 7-12.

Free barrels on the Asset Store

Any of the free packages will work fine, but the Barrel package from Game-Ready, the one displaying two rusty metal barrels in its Asset Store icon, already has a prefab set up, so let’s download that package. From the Asset Store description and from the Project view after you import the package, you can see the Barrel package is nicely organized, with a folder named Prefabs that contains the Barrel prefabs and the Barrel models (Figure [7-13](#A310332_2_En_7_Chapter.html#Fig13)).

![A310332_2_En_7_Fig13_HTML.jpg](Images/A310332_2_En_7_Fig13_HTML.jpg)

Figure 7-13.

The Project view of the Barrel package

### Make a Prefab

You need a Barrel prefab to replace the simple Pin prefab, but you should avoid modifying the original Barrel prefab (another import of the Barrel package would clobber your changes). To make your own copy of the Barrel prefab, select the prefab, press Command+D (or Duplicate from the Edit menu), and drag the copy of the prefab into the Prefabs folder. Then rename it to BarrelPin since it’s a Barrel and you’re using it as a bowling pin (Figure [7-14](#A310332_2_En_7_Chapter.html#Fig14)).

![A310332_2_En_7_Fig14_HTML.jpg](Images/A310332_2_En_7_Fig14_HTML.jpg)

Figure 7-14.

Copy of the Barrel prefab

Select the BarrelPin prefab so that it is displayed in the Inspector view. If you used the same asset package, a MeshFilter component and a MeshRenderer component are present. But, it needs a Collider component and a Rigidbody component. The prefab also needs a FuguReset script attached to handle the ResetPosition messages sent by the game controller. For now, let’s add a FuguReset script. Now add a Rigidbody component. In the Rigidbody component, set Mass to 2 (kg). That’s pretty light for a metal barrel, but the game won’t be fun if the ball can’t knock down the barrels-as-pins without too much trouble. In games, fun is better than reality!

You can add the FuguReset script to the prefab by dragging the script into the Inspector view or by using the AddComponent button (Figure [7-15](#A310332_2_En_7_Chapter.html#Fig15)).

![A310332_2_En_7_Fig15_HTML.jpg](Images/A310332_2_En_7_Fig15_HTML.jpg)

Figure 7-15.

Adding the FuguReset script to the BarrelPin prefab

### Use the Prefab

To replace the simple capsule-shaped Pin prefab with the BarrelPin prefab, select the FuguBowl GameObject in the Hierarchy view so that its components display in the Inspector view and then drag the BarrelPin prefab into the Pin field of the FuguBowl script, replacing the Pin prefab you started with (Figure [7-16](#A310332_2_En_7_Chapter.html#Fig16)).

![A310332_2_En_7_Fig16_HTML.jpg](Images/A310332_2_En_7_Fig16_HTML.jpg)

Figure 7-16.

Using the BarrelPin prefab as the bowling pin

Now when you click Play, ten barrels will appear instead of ten capsules. But they will immediately fall through the floor because the pins don’t have Collider components yet!

### Add a Collider

You can’t avoid it any longer. It’s time to take care of the BarrelPin collision. So that you can visualize the BarrelPin and its collision shape in the Scene view, temporarily place the BarrelPin prefab in the scene by dragging the prefab into the Hierarchy view. Once there, press the F key to invoke the Frame Selected command, which will center the barrel in the Scene view. Also, change the viewing options (top-left pull-down menu in the Scene view) from Textured to Wireframe, so the mesh and Collider component’s shape can be seen more easily. Unfortunately, the Component menu doesn’t list any primitive colliders that are barrel-shaped (a CylinderCollider would be perfect, but it doesn’t exist), and although a MeshCollider would conform to the shape of the barrel, MeshColliders are not appropriate for physical GameObjects because of performance issues. The closest matching shape in the Component menu is CapsuleCollider . So, let’s start with a CapsuleCollider shape on your Barrel GameObject and see how well it fits.

Although Unity tries to size a Collider component to fit the GameObject’s mesh when the Collider component is added, in this case the fit isn’t very good largely because, as you can see from the rotation in the Transform component, this GameObject is rotated 90 degrees around its x-axis. Without that rotation, the barrel is lying on its side, but the rotation also affects the orientation of the Collier component. To adjust for the rotation, switch the axis of the CapsuleCollider shape to the z-axis. After that, setting the radius to 2 and the height to 6 will produce a much closer match between CapsuleCollider and the barrel (Figure [7-17](#A310332_2_En_7_Chapter.html#Fig17)).

![A310332_2_En_7_Fig17_HTML.jpg](Images/A310332_2_En_7_Fig17_HTML.jpg)

Figure 7-17.

Adding a CapsuleCollider shape to the barrel

The capsule matches the barrel shape pretty well around the side but not on the top and bottom, which should have flat surfaces instead of just the endpoints the capsule. For flat surfaces, BoxColliders are the obvious choice. So, this is a situation where you want to combine multiple Collider components into a compound collider.

### Add a Compound Collider

Unity allows more than one Collider component on a GameObject, but they have to be different types of colliders. For example, two BoxCollider components cannot be attached to the same GameObject. However, an arbitrary set of primitive Collider components can be combined into a compound collider by attaching each of the Collider components to its own GameObject and making all of those GameObjects children of the Rigidbody’s GameObject. This is the approach you’ll take in combining a CapsuleCollider and a BoxCollider for the barrel. To hold those two Collider components, create two child GameObjects of the barrel in the Hierarchy view and name them box and capsule for their respective shapes (Figure [7-18](#A310332_2_En_7_Chapter.html#Fig18)). To create a child GameObject, select GameObject from the menu and then Create Empty Child Object.

![A310332_2_En_7_Fig18_HTML.jpg](Images/A310332_2_En_7_Fig18_HTML.jpg)

Figure 7-18.

Child GameObjects for a compound collider

Rather than create a new CapsuleCollider from scratch, let’s take advantage of the CapsuleCollider that’s already oriented and sized properly on the Barrel GameObject by copying that component. Select the Barrel GameObject, and in the Inspector view, right-click the CapsuleCollider and select Copy Component (Figure [7-19](#A310332_2_En_7_Chapter.html#Fig19)).

![A310332_2_En_7_Fig19_HTML.jpg](Images/A310332_2_En_7_Fig19_HTML.jpg)

Figure 7-19.

Copying a component

Then select the capsule GameObject again, right-click its Transform component in the Inspector view, and in the pop-up menu select Paste Component As New (Figure [7-20](#A310332_2_En_7_Chapter.html#Fig20)). The capsule GameObject now has a CapsuleCollider just like the one created for the Barrel GameObject, so you don’t need the original one anymore. Select the Barrel GameObject, right-click the CapsuleCollider you just copied, and select RemoveComponent in the pop-up menu to remove the CapsuleCollider. There should be just one CapsuleCollider in the Barrel hierarchy now.

![A310332_2_En_7_Fig20_HTML.jpg](Images/A310332_2_En_7_Fig20_HTML.jpg)

Figure 7-20.

Pasting a component

Let’s turn your attention to the BoxCollider. You could add it directly to the Box GameObject using the Component menu, but if you add the BoxCollider to the Barrel GameObject instead, then Unity will do the work for you in sizing the BoxCollider to the mesh. So, let’s repeat the process you went through with the CapsuleCollider, only this time with a BoxCollider. With the Barrel GameObject selected, add a BoxCollider using the Component menu or the AddComponent button. In the Scene view, you can see the BoxCollider encompasses the barrel (Figure [7-21](#A310332_2_En_7_Chapter.html#Fig21)).

![A310332_2_En_7_Fig21_HTML.jpg](Images/A310332_2_En_7_Fig21_HTML.jpg)

Figure 7-21.

A BoxCollider automatically sized around the barrel

It’s fine that the BoxCollider extends from the top to bottom of the mesh since it’s desirable to have two flat collision surfaces on both ends. But it’s not so desirable to have the BoxCollider extending outside the curved portion of the barrel. Decrease the width of the BoxCollider by lowering its x and y values in the Inspector view (remember, the barrel is rotated) to 2.5\. From the top-down vantage of the Scene view (click the y-axis of the Scene view gizmo), you can see the BoxCollider now fits inside the barrel (Figure [7-22](#A310332_2_En_7_Chapter.html#Fig22)).

![A310332_2_En_7_Fig22_HTML.jpg](Images/A310332_2_En_7_Fig22_HTML.jpg)

Figure 7-22.

Top-down view of a BoxCollider fitting within the barrel

Now that you have the BoxCollider sized the way you want it, repeat the process for the CapsuleCollider—right-click the BoxCollider in the Inspector view, select Copy Component, select the Box GameObject, right-click its Transform component in the Inspector view, and select Paste Component As New. Don’t forget to go back to the BoxCollider on the barrel, right-click it, and select Remove Component. With the barrel selected, you should see both Collider components displayed in the Scene view, providing a better approximation of the barrel shape than either of the Collider components individually (Figure [7-23](#A310332_2_En_7_Chapter.html#Fig23)).

![A310332_2_En_7_Fig23_HTML.jpg](Images/A310332_2_En_7_Fig23_HTML.jpg)

Figure 7-23.

Both colliders in a compound collider on display

### Update the Prefab

Finally, now that the barrel is ready to go, select Apply Changes To Prefab in the GameObject menu (Figure [7-24](#A310332_2_En_7_Chapter.html#Fig24)).

![A310332_2_En_7_Fig24_HTML.jpg](Images/A310332_2_En_7_Fig24_HTML.jpg)

Figure 7-24.

Applying changes to the BarrelPin prefab Tip

It’s a good idea to perform a Save Project or Save Scene (which implicitly performs a Save Project) operation to make sure the prefab changes are really saved. Project changes are saved when Unity exits normally, but if it exits abnormally, all bets are off.

With the prefab updated, the Barrel GameObject is no longer needed in the scene and can be removed (Command+Delete or choose Delete from the Edit menu). Now when you click Play, the barrels no longer fall through the floor, and as shown in Figure [7-25](#A310332_2_En_7_Chapter.html#Fig25), they tumble when you roll into them!

![A310332_2_En_7_Fig25_HTML.jpg](Images/A310332_2_En_7_Fig25_HTML.jpg)

Figure 7-25.

Bowling into barrels

### A Complex Collider (HyperBowl)

The compound collider created for the BarrelPin prefab is still fairly simple, consisting of just two primitive Collider components. Compound colliders can be much more complex, depending on the shape they’re approximating. As an example of a more realistic bowling pin, take a look at HyperBowl (Figure [7-26](#A310332_2_En_7_Chapter.html#Fig26)).

![A310332_2_En_7_Fig26_HTML.jpg](Images/A310332_2_En_7_Fig26_HTML.jpg)

Figure 7-26.

Compound collider for HyperBowl bowling pin

The compound collider for a bowling pin in HyperBowl consists of six primitive colliders: a BoxCollider to provide a flat surface at the bottom, a CapsuleCollider for the neck of the pin, and four SphereColliders of various sizes to fill out the body and top of the pin.

## Add Sounds

Your bowling game is starting to look good, but it’s awfully quiet! You could add background music or ambient sound like the looping music in the dance scene. But a bowling game really should have collision-based sounds, such as a rolling sound for the ball and collision sounds for each pin.

### Get Sounds

First, you need to find some sound files. Once again, let’s visit the Asset Store and search for free SFX (Figure [7-27](#A310332_2_En_7_Chapter.html#Fig27)).

![A310332_2_En_7_Fig27_HTML.jpg](Images/A310332_2_En_7_Fig27_HTML.jpg)

Figure 7-27.

Asset Store search results for free SFX

The Free SFX Package from Bleep Blop Audio lists a good variety of audio clips, so let’s download that package. The files show up in a Project view folder named Assets inside your Assets folder (submitting packages to the Asset Store is a little tricky, so that file organization was probably unintentional).

### Add a Rolling Sound

Feel free to play the various sounds by selecting them and clicking the Play button in the Inspector view. But for now you’ll use the Sci-Fi_Ambiences sound for the ball-rolling sound. It’s not a great ball-rolling sound, but it’s the only audio clip in this package that’s suitable for looping (Figure [7-28](#A310332_2_En_7_Chapter.html#Fig28)).

![A310332_2_En_7_Fig28_HTML.jpg](Images/A310332_2_En_7_Fig28_HTML.jpg)

Figure 7-28.

The audio clip used for the ball-rolling sound

Drag the audio clip to the Ball GameObject in the Hierarchy view and select the ball. The Inspector view should display a new AudioSource component that was automatically created to reference the audio clip (Figure [7-29](#A310332_2_En_7_Chapter.html#Fig29)).

![A310332_2_En_7_Fig29_HTML.jpg](Images/A310332_2_En_7_Fig29_HTML.jpg)

Figure 7-29.

The Inspector view of the ball with an audio source

The rolling sound will be controlled by a script and not played automatically, so make sure the Play On Awake check box is deselected. But when the rolling sound is played, it should continue playing until told to stop, so the Loop check box should be selected.

The AudioClip component is a 3D sound, as indicated in the AudioSource component below the AudioClip name. That means the sound will attenuate with the distance between the AudioSource component and AudioListener component (which is attached to the Main Camera). The attenuation is specified by the graph shown at the bottom of the AudioSource component. You can adjust the attenuation by selecting among the Volume Rolloff options and also specifying the Min Distance value, which specifies at what distance the attenuation begins. If the AudioListener component is less than the Min Distance value from the AudioSource component, then the sound is not attenuated at all. Or you can directly manipulate the attenuation curve by dragging the handle along the curve.

Tip

If you’re having trouble hearing an audio source, try starting with a high Min Distance value to be certain you’re hearing it at full volume and then adjust the Min Distance value and attenuation curve to your liking. The script to play the rolling sound will be attached to the ball. Create a new JavaScript, place it in the Scripts folder, and name it FuguBallSound. Then add the contents of Listing [7-6](#A310332_2_En_7_Chapter.html#Par66) to the script.

```
#pragma strict
var minSpeed:float = 1.0; // actually the square of the minSpeed
private var sqrMinSpeed:float = 1.0;
private var floorTag = "Floor";
function Awake() {
sqrMinSpeed = minSpeed * minSpeed;
}
function OnCollisionStay(collider:Collision) {
if (collider.gameObject.tag == floorTag) {
if (GetComponent.().velocity.sqrMagnitude>sqrMinSpeed) {
if (!GetComponent.().isPlaying) {
GetComponent.().Play();
}
} else {
if (GetComponent.().isPlaying) {
GetComponent.().Stop();
}
}
}
}
function OnCollisionExit(collider:Collision) {
if (collider.gameObject.tag == floorTag) {
if (GetComponent.().isPlaying) {
GetComponent.().Stop();
}
}
}
Listing 7-6.
FuguBallSound.js Script for Ball-Rolling Sound
```

There is some similarity with the FuguForce script, since both scripts keep track of when the ball is rolling using collision callbacks and checking the tag of the colliding GameObject to see whether it’s the floor. FuguBallSound uses just two of the OnCollision callbacks : OnCollisionStay and OnCollisionExit. OnCollisionEnter isn’t implemented because it would be redundant with OnCollisionStay (the FuguForce script didn’t really need OnCollisionEnter either). Both of the script's collision callbacks reference the audio variable of this component, which is equivalent to the audio variable of this component’s GameObject and references the attached AudioSource.

When the ball is on the floor, OnCollisionStay checks whether the ball is moving faster than a minimum speed, which is specified in the public variable minSpeed so that it can be adjusted in the Inspector view. The script actually compares the square of the ball speed against the square of minSpeed to avoid the computational expense of square root calculations. If the ball is on the floor and moving fast enough and if the ball’s AudioSource is not already playing its audio clip, then the script starts playing the audio clip. If the ball’s speed drops far enough, the rolling sound will stop.

The job of OnCollisionExit is easier, as it just needs to check whether audio is playing and, if it is, then stop playing the audio. In other words, if the ball loses contact with the floor because it bounced or rolled off, then the rolling sound stops.

Drag the script onto the ball in the Hierarchy view (Figure [7-30](#A310332_2_En_7_Chapter.html#Fig30)) and click Play to try it. As the ball rolls around, the rolling sound should become audible, and the sound should cease when the ball comes to a rest.

![A310332_2_En_7_Fig30_HTML.jpg](Images/A310332_2_En_7_Fig30_HTML.jpg)

Figure 7-30.

The FuguBallSound script attached to the Ball GameObject

### Add a Pin Collision Sound

Now you’re ready for the pin sound. In place of a real bowling pin sound, you’ll make do with the Coin_Pick_Up_03 sound from the Free SFX Pack (Figure [7-31](#A310332_2_En_7_Chapter.html#Fig31)).

![A310332_2_En_7_Fig31_HTML.jpg](Images/A310332_2_En_7_Fig31_HTML.jpg)

Figure 7-31.

The audio clip used for pin collision sounds

Select the Pin prefab in the Project view and add an AudioSource component using the Add Component button at the bottom of the Inspector view. Then click to the right of the AudioClip field of the AudioSource component to select Coin_Pick_Up_03 AudioClip. Once again, deselect the Play On Awake check box or you’ll hear that sound play whenever the game starts. Similar to how the ball-rolling sound is played by a script attached to the ball, the Pin collision sounds will be played by a script attached to each pin. Create a new JavaScript, place it in the Scripts folder, and name it FuguPinSound . Add the code in Listing [7-7](#A310332_2_En_7_Chapter.html#Par73) to the script.

```
#pragma strict
var minSpeed = 0.01;
function OnCollisionEnter(collider:Collision) {
if (collider.relativeVelocity.sqrMagnitude > minSpeed) {
if (collider.gameObject.tag != "Pin") {
GetComponent.().Play(); // hit anything besides another pin, play the sound
} else {
// otherwise pin with lower ID gets to play
if (gameObject.GetInstanceID() ().Play();
}
}
}
Listing 7-7.
FuguPinSound.js script for Pin Collision Sounds
```

This script is different from FuguBallSound in that it uses the OnCollisionEnter callback and not the OnCollisionStay or OnCollisionExit callback. Again, there is a minSpeed variable that is compared with the squared magnitude of a velocity. But this time the velocity is the relativeVelocity variable of the collision since both the pin and whatever it’s colliding with (the ball or another pin) may both be moving.

Here it is assumed that each pin has a tag named Pin, so you can test whether the pin is colliding with another pin. If the pin is not colliding with another pin, then it must be getting hit by the ball or is falling on the floor, in which case the script plays the collision sound. If the pin is getting hit by another pin, then a decision has to be made about which pin gets to play the collision sound. Otherwise, they’ll both play the sound at the same time. A simple trick is used to arbitrate. Each object in Unity has a unique ID number that can be retrieved by the object function GetInstanceID. The rule used in the script is that the pin with the lower ID number wins and gets to play the collision sound .

To attach the FuguPinSound script to the BarrelPin prefab, select the prefab in the Project view and use the Add Component button in the Inspector view to select the script. And while you have the BarrelPin prefab in the Inspector view, set its tag to Pin, as the FuguPinSound script expects. In the same manner that the Floor tag was created and assigned to the Floor GameObject, select Add Tag in the Tag menu, add a tag named Pin in the TagManager (make sure you’re creating a new tag, not a new layer), and select the Pin prefab again so that you can use the Tag menu to choose the new Pin tag. This, by the way, is an example of how a tag can be used to identify a group of elements rather than uniquely naming one, like you did with the floor.

The Barrel GameObject in your BarrelPin prefab should now look like Figure [7-32](#A310332_2_En_7_Chapter.html#Fig32) in the Inspector view.

![A310332_2_En_7_Fig32_HTML.jpg](Images/A310332_2_En_7_Fig32_HTML.jpg)

Figure 7-32.

AudioSource and FuguPinSound script attached to the BarrelPin prefab

Now when you click Play and bowl into the barrels, pleasant coin sounds chime in as the barrels bounce around!

## Explore Further

The bowling game is starting to look like a bowling game at this point (whereas at the end of the previous chapter, you had at best what could be called a rolling game ). But although the player can roll the ball around and knock down bowling pins, you still don’t have the rules of the game. Stay tuned for that in the next chapter, which will get much heavier into scripting. In fact, this chapter is sort of a turning point, trending to more and more scripting and less introduction of new components. So from now on, you should be spending most of your time in the Scripting Reference of the Unity documentation.

### Scripting Reference

The Instantiate function in the Object class was introduced to create the bowling pins at runtime. This is the function used to spawn GameObjects, typically from prefabs. In other games, you might use Instantiate to spawn anything from pickup items to non-player characters (NPCs). The function is described in the “Scripting Overview” section, but its page in “Runtime Classes” has more detailed information.

One new callback, Awake, was introduced as an alternative to the Start callback. The MonoBehaviour collision callbacks OnCollisionEnter, OnCollisionStay, and OnCollisionExit were introduced in the previous chapter but used again for the rolling and collision sounds. The Collision class for information on collisions, relative velocity, the GameObject that was collided against. Other data such as the actual point of contact are also available.

The page for the Rigidbody component is worth reading in its entirety. Its variables correspond largely to the properties available in the Inspector view, and besides the AddForce function you used to push the ball, there are many related functions: AddRelativeForce, Add Torque, AddRelativeTorque, AddExplosionForce, and AddForceAtPosition.

The Transform class was used again, this time checking Transform.position to determine whether the ball had rolled off the floor. Since Quaternions were mentioned, take a look at the rotation and localRotation variables in Transform and compare them to the eulerAngles and localEulerAngles variables. From the GameObject class, the SendMessage and BroadcastMessage functions were used to invoke the ResetPosition functions in other GameObjects. There’s also a SendMessageUpward function that works like BroadcastMessage, except the message is sent up the GameObject’s hierarchy instead of down. The message functions are also defined in the Component class.

AudioSource functions were used to play and stop audio clips. Other functions are useful if you want to refine your sound code. For example, the HyperBowl rolling sound code changes the volume of the sound (using the AudioSource.volume variable) according to the ball’s velocity, which not only provides a nicer rolling sound but also a softer cutoff of the sound when stopping.

### Assets

You saw the Asset Store has a bountiful selection of free barrel models and sound libraries. And there’s much more if you don’t restrict yourself to free assets.

Although the Asset Store doesn’t yet have bowling pin models, it’s not hard to find some on 3D model marketplaces such as [`http://Turbosquid.com/`](http://turbosquid.com/) . Free audio, including bowling sounds , is available on the Creative Common–licensed [`http://freesound.org/`](http://freesound.org/) .

# 8. Let’s Play! Scripting the Game

Your bowling game that has been taking shape over the past few chapters is starting to actually look like a bowling game ! It has a bowling ball, bowling pins (or rather, the barrels added in the previous chapter), game controls, and game physics. But it’s still more a toy than a game since it lacks game rules and scoring. That will be remedied in this chapter with a healthy dose of scripting. Most of that will take place in the game controller script, `FuguBowlPlayer.js`, which will be filled out with the complete logic for a bowling game laid out as states in a finite state machine (FSM) . The scoring rules are complicated, so those will be encapsulated in a script named `FuguBowlPlayer.js`.

The good news is that, for once, there are no added assets in this chapter besides the FuguBowlPlayer script. The bad news is that there’s a lot of new code to type in, particularly in the game controller script. Although it may be tempting to just copy the online version of the FuguBowl script from the project for this chapter at [`www.apress.com/9781484231739`](http://www.apress.com/9781484231739) , it’ll be easier to get the hang of implementing game logic for future projects if you put it together from scratch, piece by piece (or in the case of an FSM, state by state). The same goes for the new FuguBowlPlayer script.

## The Game Rules

Let’s quickly go over the rules of bowling. A game consists of ten frames. In each frame, you have up to two balls to knock down all ten pins. If you knock down all ten with the first ball, it’s called a strike, and you advance to the next frame. If you knock down all ten pins with two balls, then it’s called a spare. In the tenth frame, if you get a spare or strike, then you’re awarded a bonus third (and final) ball. The final game score is the sum of the scores from each frame. The score for a frame is the number of pins knocked down in that frame, except when it’s a spare or strike. If it’s a spare, it’s the number of pins knocked down (ten), plus the number of pins knocked down with the next ball. If it’s a strike, it’s also ten but added to the number of pins knocked down from the next two balls. So, a “perfect” game, 12 consecutive strikes , results in a score of 300 (I leave that calculation as an exercise for you).

## Scoring the Game

The FuguBowl script already defines a class that represents your game, but it makes sense to encapsulate the score code in a player class since conceptually there is a player (and potentially more than one, although not in this particular game) and the score is associated with the player.

Moreover, the scoring rules for bowling are not simple. Placing the code for the bowling score calculation in a separate script not only keeps the game controller script smaller and more readable but also allows reuse of the score code for other bowling games.

To begin, create a new JavaScript, name it FuguBowlPlayer, and place it in the Scripts folder (Figure [8-1](#A310332_2_En_8_Chapter.html#Fig1)).

![A310332_2_En_8_Fig1_HTML.jpg](Images/A310332_2_En_8_Fig1_HTML.jpg)

Figure 8-1.

Creating the FuguBowlPlayer script

### The Frame Score

In the FuguBowlPlayer script, before implementing the FuguBowlPlayer class, let’s start with a supporting class called FuguBowlScore that represents the score for a single bowling frame (Listing [8-1](#A310332_2_En_8_Chapter.html#Par8)). It’s small enough and not a MonoBehavior in its own right (no Start or Update callbacks, for example), so it’s fine to just add it to the FuguBowlPlayer script.

```
class FuguBowlScore {
var ball1:int; // pins down for ball 1
var ball2:int; // pins down for ball 2
var ball3:int; // pins down for ball 3
var total:int; // total score for this frame (may include future rolls)
function Clear() {
ball1 = -1;
ball2 = -1;
ball3 = -1;
total = -1;
}
function IsSpare():boolean {
// doesn't handle spare on ball3
return !IsStrike() &&
(ball1 + ball2 == 10);
}
function IsStrike():boolean {
return ball1 == 10;
}
}
Listing 8-1.
The FuguBowlScore Class in FuguBowlPlayer.js
```

The FuguBowlScore class is simple and doesn’t inherit from any other class (it could well be defined as a struct instead of a class , except JavaScript doesn’t support defining new structs). The class includes instance variables that represent the score for the first ball rolled, the second ball, and (only relevant in the tenth frame) the bonus ball. FuguBowlScore also has a variable to hold the total score for the frame, which can depend on the result of future rolls if the current frame is a spare or strike.

Each of the variables needs a way to indicate that its score hasn’t been calculated. The scores for ball1, ball2, and ball3 aren’t available until that ball has been rolled. The total score isn’t available until the bowling for this frame is complete, and even then, if the result has been a spare or strike, the score still cannot be calculated until the next ball or two have been rolled.

Leaving the score values as zero won’t work since zero is a valid score when the player doesn’t knock down any pins. But there’s no way a negative score can be rolled, so -1 will work fine as an indicator that a score has not been calculated yet. The Clear function in FuguBowlScore initializes this frame by setting all the variables to this number.

Besides the Clear function , the FuguBowlScore class has two other member functions. IsStrike returns true if there has been a strike rolled in this frame. That can be tested easily by seeing whether the number of pins knocked down with the first ball is ten. The IsSpare function is only slightly more complex; it returns true if the sum of the pins knocked down by the first and second balls is ten, but only if there isn’t a strike in this frame (in other words, if you’re not adding ten pins from the first ball and zero pins from the second ball).

Notice the :boolean that is added to the two function declarations. That makes it clear that each of these functions returns a Boolean value, although the compiler can infer the return type from the code.

### The Player Score

With the FuguBowlScore class defined, now you’re ready to add the FuguBowlPlayer class, which will aggregate the frame scores for the total game score (Listing [8-2](#A310332_2_En_8_Chapter.html#Par15)). As with FuguBowlScore , the FuguBowlPlayer class is declared explicitly, even though it has the same name as the script file, and it doesn’t inherit from MonoBehaviour (otherwise the class declaration would include “extends MonoBehaviour”). Besides not having any Unity callback functions, this means the FuguBowlPlayer class is not a subclass of Component and therefore the script can’t be attached to a GameObject. In other words, the FuguBowlPlayer script acts as a code library containing classes used by other scripts.

```
class FuguBowlPlayer {
var scores:FuguBowlScore[]; // all 10 frames of the game
// constructor
function FuguBowlPlayer() {
scores = new FuguBowlScore[10];
for (var i:int=0; i<scores.length; ++i) {
scores[i] = new FuguBowlScore();
}
ClearScore();
}
// reset the score for all frames
function ClearScore() {
for (var score:FuguBowlScore in scores) {
score.Clear();
}
}
Listing 8-2.
The Beginnings of the FuguBowlPlayer Class in FuguBowlPlayer.js
```

The FuguBowlPlayer class naturally contains information about the player, and, for example, could have a String variable for the player’s name. However, for the purposes of this book, you’ll confine yourself to tracking the player’s bowling score. Therefore, the class has just one instance variable, scores, which is a built-in array of FuguBowlScore objects. Each FuguBowlScore element in the array represents the score for one frame of the bowling game.

Note

Built-in arrays are accessed with indices just like instances of the Array class (used for the pins variable in `FuguBowl.js`). Built-in arrays have much faster access than instances of Array and can be edited in the Inspector view when declared as public variables. However, built-in arrays, unlike instances of Array, are not resizable at runtime.

The function FuguBowlPlayer within the class definition has the same name as the class, which means it’s a constructor , a special function called when an instance of this class is created. An explicitly defined constructor isn’t always necessary (FuguBowlScore doesn’t have one), and it is even prohibited within MonoBehaviour. But a constructor is warranted in this case because each newly created FuguBowlPlayer needs to have its scores array also created. So, the FuguBowlPlayer constructor creates a built-in array of ten entries, declared to be of type FuguBowlScore, and fills the array with newly created FuguBowlScore instances.

The constructor also calls a function ClearScore , which in turn calls Clear on each of the FuguBowlScore objects, ensuring they all start out indicating no score has been registered. The FuguBowlPlayer class also has IsSpare and IsStrike functions that call the same-named functions on the specified FuguBowlScore object, indexed in the scores array by the argument passed in. Since array indices begin at zero, the ten frames of a game are represented by the indices 0 to 9.

Note

In a sense, the FuguBowlPlayer class acts as a wrapper around the FuguBowlScore class so that other scripts need only know about the FuguBowlPlayer class. As an exercise in polish, you could provide more abstraction by having IsSpare and IsStrike accept frame indices in the range 1 to 10 and then subtract 1 before using them as array indices.

### Setting the Score

As the game progresses, the score has to be updated after each roll of the ball. Let’s plan on adding functions named SetBall1Score, SetBall2Score, and SetBall3Score (for the tenth frame) to the FuguBowlPlayer class. Like the IsSpare and IsStrike functions in that class, the additional functions will take a frame index as an argument.

The score for each roll of the ball potentially updates the total score for a previous frame if it had a strike or spare. So, first you’ll need functions to perform those updates in the FuguBowlPlayer class, named SetSpareScore (Listing [8-3](#A310332_2_En_8_Chapter.html#Par24)) and SetStrikeScore (Listing [8-4](#A310332_2_En_8_Chapter.html#Par26)).

The SetSpare function sets the total score to the sum of the first and second ball scores (which should always be ten), plus the score from the next roll, which is the first ball of the next frame.

```
function SetSpareScore(frame:int) {
var framescore:FuguBowlScore = scores[frame];
// the score is the score of both rolls in this frame and teh first roll of the next
framescore.total = framescore.ball1+framescore.ball2+scores[frame+1].ball1;
}
Listing 8-3.
The SetSpareScore Function in the FuguBowlPlayer Class
```

The SetStrike function is a little more complicated. It sets the total score to the score from the first ball (which should be ten) plus the scores from the next two rolls, which normally consists of the first and second balls from the next frame. But if the next frame is a strike, then the next two rolls are the first ball from the next frame and the first ball from the frame after that.

```
function SetStrikeScore(frame:int) {
var framescore:FuguBowlScore = scores[frame];
framescore.total = framescore.ball1;
// always add the score from first roll of the next frame
framescore.total+=scores[frame+1].ball1;
if (frame < 8 && IsStrike(frame+1)) {
framescore.total+=scores[frame+2].ball1;
} else {
// for the ninth frame (frame 8) add the second ball from the next (final) frame (there always is one)
framescore.total+=scores[frame+1].ball2;
}
}
Listing 8-4.
The SetStrikeScore Function in the FuguBowlPlayer Class
```

The use of SetSpareScore and SetStrikeScore is illustrated in the definition of SetBall1Score (Listing [8-5](#A310332_2_En_8_Chapter.html#Par30)). The first line sets the ball1 variable of the frame with the number of pins knocked down, which is passed in as the second argument of the function. Then, if the current frame is not the first frame, SetBall1Score checks whether a spare was rolled in the previous frame. If that’s the case, then the previous frame is waiting for the results of this roll to calculate the frame’s total score, and SetSpareScore is called on that frame to perform that final calculation.

If the current frame is not the first or second frame, then SetBal1Score checks whether strikes have been rolled in the previous two frames, in which case the first of those frames is waiting for the result of this roll to update its total score, and SetStrikeScore is called for that frame.

Notice SetBall1Score doesn’t set the total score for the current frame. If the current roll is not a strike, then the frame isn’t over yet, and if the current roll is a strike, then two more rolls have to take place before the total for this frame can be updated (via SetStrikeScore, of course!).

```
function SetBall1Score(frame:int,pinsDown:int) {
scores[frame].ball1=pinsDown;
// if previous frame was a spare, set its score
if (frame>0 && IsSpare(frame-1)) {
SetSpareScore(frame-1);
}
// if the previous two frames were strikes, then set the score of the first frame
if (frame>1 && IsStrike(frame-1) && IsStrike(frame-2)) {
SetStrikeScore(frame-2);
}
}
Listing 8-5.
The SetBall1Score Function in the FuguBowlPlayer Class
```

The SetBall2Score function (Listing [8-6](#A310332_2_En_8_Chapter.html#Par33)) is more complicated since it has more cases to handle. As the second argument passed into the function is the number of pins knocked down so far in the frame, the ball2 variable is set to the number of pins down minus whatever was already knocked down for the first ball, except for the special case where the first ball is a strike.

Normally, if the first ball is a strike, the second ball is skipped, and SetBall2Score wouldn’t even be called. But if a strike occurs on the first roll of the tenth frame, then the second ball is awarded, and ball2 is set to the total number of pins knocked down because the pins were reset after the strike in the first roll.

```
function SetBall2Score(frame:int,pinsDown:int) {
var framescore:FuguBowlScore = scores[frame];
if (IsStrike(frame)) { // we must be in the final frame
framescore.ball2=pinsDown;
} else {
framescore.ball2=pinsDown-framescore.ball1;
}
// calculate this frame's total score if it isn't a spare or strike
if (!IsSpare(frame) && !IsStrike(frame)) {
framescore.total= pinsDown;
}
// if previous frame was a strike then set that frame's score
if (frame>0 && IsStrike(frame-1)) {
SetStrikeScore(frame-1);
}
}
Listing 8-6.
SetBall2Score Function in the FuguBowlPlayer Class
```

The total score for this frame is set to the sum of the first and second balls, except in the case of a spare or strike, in which case one or two future rolls, respectively, have to take place before the total can be resolved.

Given that this is the second roll of the frame and a spare only adds the first roll of the next frame to its score, there’s no way this roll can contribute to a spare score from the previous frame. However, if the previous frame had a strike, calling SetStrikeScore, that frame is warranted what that frame since it’s been waiting for the first and second rolls of this frame to complete its total.

The function SetBall3Score (Listing [8-7](#A310332_2_En_8_Chapter.html#Par37)) is a special case because a third roll can occur only in the tenth frame, and even then only if there was a strike with the first or second ball or a spare with the first two balls. When SetBall3Score is called, the function sets the ball3 variable with the number of pins down if ball2 was a strike or completed a spare, which are the two cases where the pins get reset before the third ball. Otherwise, there must have been a strike in the first frame and something other than a strike in the second, so ball3 is set to the number of pins down minus the pins knocked down by the second ball.

```
function SetBall3Score(frame:int,pinsDown:int) {
var framescore:FuguBowlScore = scores[frame];
if (IsStrike(frame) && framescore.ball2 <10) {
framescore.ball3 = pinsDown - framescore.ball2;
} else {
framescore.ball3 = pinsDown; // spare or two strikes
}
framescore.total = framescore.ball1+framescore.ball2+framescore.ball3;
}
Listing 8-7.
The SetBall3Score Function in the FuguBowlPlayer Class
```

### Getting the Score

Although implementation of a user interface will take place in the next chapter, you should make sure the FuguBowlPlayer class supports a score display. A typical bowling scoreboard, in real life or in a computer game, displays the score for each roll in each frame, which matches perfectly with the ball1, ball2, and ball3 variables in the FuguBowlScore class. Furthermore, a bowling scoreboard typically has special markings to indicate a spare or strike in each frame, which the IsSpare and IsStrike functions in the FuguBowlPlayer class can accommodate.

However, while FuguBowlScore has a total variable that represents the total score for the frame, a bowling scoreboard usually shows the game total score for that frame, i.e., the total cumulative score up to and including that frame. For example, Figure [8-2](#A310332_2_En_8_Chapter.html#Fig2) shows how the scoreboard in HyperBowl looks after bowling two frames.

![A310332_2_En_8_Fig2_HTML.jpg](Images/A310332_2_En_8_Fig2_HTML.jpg)

Figure 8-2.

The HyperBowl scoreboard

The first frame displays the scores for the first and second rolls and the resulting total. The second frame displays the score for its first roll and the mark for a spare on the second roll. The game total score displayed in the second frame is the game total score from the first frame plus the additional frame total score from the second frame.

This example provides a clue on how to implement a function that returns the game total score for a frame. Listing [8-8](#A310332_2_En_8_Chapter.html#Par42) shows the code for such a function, GetScore, added to the FuguBowlPlayer class. If the specified frame is the first frame of the game, GetScore returns the frame total score. If the frame total score is -1, meaning not yet available, then GetScore returns -1 to indicate the game total score is not yet available for that frame.

```
function GetScore(frame:int):int {
if (frame==0 || scores[frame].total==-1) {
return scores[frame].total;
} else {
var prev:int = GetScore(frame-1);
if (prev==-1) {
return -1;
} else {
return scores[frame].total+prev;
}
}
}
Listing 8-8.
The GetScore Function in the FuguBowlPlayer Class
```

But if the frame total is available and the frame is not the first frame, then the frame total is added to the game total from the previous frame, unless that game total is not yet available. If the game total is not available from the previous frame, then, again, -1 is returned.

The game total from the previous frame is acquired by calling GetScore on that frame, which in turn will call GetScore on the frame before it, until the first frame is reached. GetScore is an example of a recursive function , which is a function that calls itself. Infinite recursion is avoided because eventually GetScore arrives at its base case, the first frame, which will always return.

### The Complete Listing

The complete listing for the FuguBowlScore script is too unwieldy to show here (it spans four pages), but all of its contents have been shown in this section. The entire script, `FuguBowlScore.js`, is available in the project for this chapter at [`www.apress.com/9781484231739`](http://www.apress.com/9781484231739) .

If you want to replace a script with the version from [`www.apress.com/9781484231739`](http://www.apress.com/9781484231739) , perform the replacement in the Finder (i.e., drag or paste the file into the Scripts folder of this project). Normally, the ability to just drag a file from the Finder into the Project view is really convenient, but in this case Unity will rename the new file to avoid clobbering the original.

Don’t rename the original script yourself if your intent is to replace it since any references in the scene will still refer to that script under the new name. Rather, duplicate the original script (either in the Finder or using Duplicate from the Edit menu in the Unity Editor) if you want to keep a copy.

### Creating a FuguBowlPlayer

The FuguBowlPlayer class is now all set and ready to use. Since the script is not attached to a GameObject (and can’t be, since FuguBowlPlayer is not a MonoBehaviour), a FuguBowlPlayer has to be instantiated at runtime. The natural place to do this is in the FuguBowl script’s Awake callback, which is already instantiating the bowling pins (Listing [8-9](#A310332_2_En_8_Chapter.html#Par49)).

```
static var player:FuguBowlPlayer = null;
function Awake() {
player = new FuguBowlPlayer();
CreatePins();
}
Listing 8-9.
Creating a FuguBowlPlayer in FuguBowl.js
```

The augmented Awake callback creates a new FuguBowlPlayer instance and assigns it to the variable player, which is declared a static variable for easy access from outside the FuguBowl script (looking ahead to the scoreboard implementation in the next chapter).

To support multiple players, the variable player might instead be an array of FuguBowlPlayers (a resizable array instead of a built-in array to accommodate different numbers of players), and there might be some encapsulating functions like GetPlayer and GetNumberOfPlayers. But for the purposes of this book, we’ll keep things simple and focus on a single-player game.

## The Pin Status

The FuguBowl script now has a reference to FuguBowlPlayer in addition to the bowling pins and ball. But to call the FuguBowlPlayer score functions, FuguBowl must pass in the number of pins that have been knocked down. Rather than adding code to FuguBowl to figure out whether a pin is down, it’s cleaner to write that code in a script attached to each pin. Then FuguBowl can just ask each pin if it’s down and doesn’t have to know about the inner workings of the pins.

For this new pin script, create a new JavaScript, name it FuguPinStatus, and place it in the Scripts folder (Figure [8-3](#A310332_2_En_8_Chapter.html#Fig3)).

![A310332_2_En_8_Fig3_HTML.jpg](Images/A310332_2_En_8_Fig3_HTML.jpg)

Figure 8-3.

Create a FuguPinStatus.js script .

Next, add the contents of Listing [8-10](#A310332_2_En_8_Chapter.html#Par55) to the script.

```
#pragma strict
var knockedAngle:float = 45.0;
private var initialAngles:Vector3;
function Start () {
initialAngles = transform.localEulerAngles;
}
function IsKnockedOver() {
return Mathf.Abs(transform.localEulerAngles.x-initialAngles.x)>knockedAngle ||
Mathf.Abs(transform.localEulerAngles.y-initialAngles.y)>knockedAngle ||
Mathf.Abs(transform.localEulerAngles.z-initialAngles.z)>knockedAngle;
}
Listing 8-10.
Listing for FuguPinStatus.js
```

The Awake callback in the script saves the GameObject’s initial rotation in the private variable initialAngles for comparison later. The function IsKnockedOver returns true or false depending on whether the pin’s rotation around any axis has changed by more than the number of degrees specified in the knockedAngle variable.

In the Project view, select the Barrel GameObject in the BarrelPin prefab and use the Add Component button in the Inspector view to attach the FuguPinStatus script (Figure [8-4](#A310332_2_En_8_Chapter.html#Fig4)).

![A310332_2_En_8_Fig4_HTML.jpg](Images/A310332_2_En_8_Fig4_HTML.jpg)

Figure 8-4.

The FuguPinStatus.js script attached to the Barrel pin prefab

Before querying each pin to see whether it’s been knocked over, the FuguBowl script needs to find the pins. Querying each GameObject in the variable pins would work if the Array is filled with the capsule-based Pin prefab, but when the array is full of BarrelPin GameObjects, the Barrel GameObjects that act as the real pins are children of those BarrelPin GameObjects.

Fortunately, in Chapter [7](../Text/A310332_2_En_7_Chapter.html) you tagged all the “real” pins (the GameObjects with Rigidbody and Mesh components that actually move) with the tag Pin. This makes it possible to retrieve an array of all the real pins using the static function GameObject.FindGameObjectsWithTag. That should take place at the end of the CreatePins function in the FuguBowl script, after all the pins have been instantiated (Listing [8-11](#A310332_2_En_8_Chapter.html#Par60)). The tagged pins are assigned to a private variable called pinBodies.

```
private var pinBodies:GameObject[]; // the real physical pins
function CreatePins() {
pins = new Array();
var offset = Vector3.zero;
for (var row=0; row<pinRows; ++row) {
offset.z+=pinDistance;
offset.x=-pinDistance*row/2;
for (var n=0; n<=row; ++n) {
pins.push(Instantiate(pin, pinPos+offset, Quaternion.identity));
offset.x+=pinDistance;
}
}
pinBodies = GameObject.FindGameObjectsWithTag("Pin");
}
Listing 8-11.
Declaring and Setting the pinBodies Variable in FuguBowl.js
```

The number of pins that have been knocked down are counted by the function GetPinsDown (Listing [8-12](#A310332_2_En_8_Chapter.html#Par62)) that loops through the pinObjects array, incrementing a local variable pinsDown for each pin whose IsKnockedOver function returns true. After the loop is complete, the value of the pinsDown counter is returned.

```
function GetPinsDown():int {
var pinsDown:int = 0;
for (var pin:GameObject in pinBodies) {
if (pin.GetComponent(FuguPinStatus).IsKnockedOver()) {
++pinsDown;
}
}
return pinsDown;
}
Listing 8-12.
The GetPinsDown Function in FuguBowl.js
```

Besides counting pins that have been knocked down, you can remove them, like the way real pins get scraped away after getting knocked down in a real bowling alley. The function RemoveDownedPins (Listing [8-13](#A310332_2_En_8_Chapter.html#Par64)) loops through the pinBodies list and calls SetActive(false) on each one that returns true for IsKnockedDown. Calling SetActive(false) is equivalent to deselecting the check box on the top left of a GameObject in the Inspector view. Each pin that is knocked down, while not getting completely removed from the scene, is made invisible and does not collide.

```
function RemoveDownedPins() {
for (var pin:GameObject in pinBodies) {
if (pin.GetComponent(FuguPinStatus).IsKnockedOver()) {
pin.SetActive(false);
}
}
}
Listing 8-13.
The RemovePins Function in FuguBowl.js
```

Of course, deactivated pins need to be reactivated at some point. The existing ResetPins function (Listing [8-14](#A310332_2_En_8_Chapter.html#Par66)) is a good place to do that since ResetPins is called when restoring the pins to their original positions in preparation for bowling a new frame.

```
function ResetPins() {
for (var pin:GameObject in pinBodies) {
pin.SetActive(true);
pin.SendMessage("ResetPosition");
}
}
Listing 8-14.
ResetPins Function in FuguBowl.js
```

In fact, now the loop through the pins array can be replaced with a loop through pinBodies. Within the loop, SetActive(true) is called on each pin in case it was deactivated, and SendMessage can replace BroadcastMessage because each pin in pinBodies is known to have the FuguReset script attached.

## The Game Logic

I highly recommend sketching out a game as a finite state machine before attempting to code the game logic. A finite state machine represents a program as, surprise, a finite set of states. An FSM is in one state at any time and transitions from state to state.

Note

The term state machine often refers to an FSM, but technically there is a whole range of state machines ranging from the FSM, the simplest and least-powerful class of automata, up to the Turing machine, which has infinite memory and a potentially infinite number of states.

Once you have your game visualized as an FSM, you can be confident that you can code it. Conversely, if you can’t visualize your game flow as an FSM, you probably just have a vague idea that you’re not yet ready to implement it!

### Design the FSM

To sketch out the FSM, think of all the states that the game will go through. All FSMs have an initial state, which in this case is the beginning of a new game. The player starts out in the first frame with the first ball, starts rolling the ball, and, when finished rolling, has a strike, a spare, or a regular score. Then the player moves either onto the second ball or back to the first in a new frame, and the process is repeated. If the player is on the second ball, the flow is the same except for the transition back to the first ball of a new frame or possibly the third ball if this is the tenth frame and the player rolled a strike or spare. After all ten frames have been completed, the FSM transitions to the GameOver state, which may cycle back to a new game.

Figure [8-5](#A310332_2_En_8_Chapter.html#Fig5) shows how the state machine just described might look.

![A310332_2_En_8_Fig5_HTML.jpg](Images/A310332_2_En_8_Fig5_HTML.jpg)

Figure 8-5.

A state machine for bowling

It’s not important to get it exactly right the first time. Typically, when you sketch out the flow of a program and then start scripting, you’ll realize you forgot something or there’s a better way, and then you go back and adjust your original design. It’s like what one of my professors said about constructing proofs—it’s a messy process completing one, but then you go back and clean it up and pretend that’s how you planned it in the first place!

Technically, an FSM doesn’t have memory, so a really complete state machine for bowling would have a lot of duplicated states for all three balls and even all ten frames. But a state machine with a few hundred states would be hard to understand, which would largely defeat its purpose. You don’t have to be purists about this, so let’s assume there are variables tracking the current frame that’s being played and which ball the player is rolling.

### Tracking the Game

Listing [8-15](#A310332_2_En_8_Chapter.html#Par76) shows the variables defined in the FuguBowl script to track the current frame and current ball.

```
private var state:String; // current state in the state machin
enum Roll {
Ball1,
Ball2,
Ball3
}
Listing 8-15.
The Variables in FuguBowl.js That Track the Current Frame and Ball
```

The variable frame tracks the current frame with an int, ranging from 0 to 9 instead of 1 to 10 because that number will be used as an array index when it’s passed to the FuguBowlPlayer functions.

The variable roll tracks the current roll of the ball and could also have been declared an int, choosing a convention where the numbers 0 to 2 or 1 to 3 represent the three possible balls in a frame. But it’s cleaner to define an enum (short for enumeration), which essentially allows you to define a new type where the value is one from a list of named values. So, roll is declared to be of type Roll, which is an enum with values Ball1, Ball2, or Ball3.

By the way, at this point you’ve encountered several different categories of types: primitive types like int and boolean, built-in arrays that contain other types, enums like the Roll enum, structs like Vector3, and classes like GameObject, Component, and FuguBowlPlayer. Of these categories, classes and arrays are reference types, meaning different variables can reference the same instance of that type, and modifications to the instance will be visible through every variable referencing that instance.

### Starting the State Machine

Okay, now that you’ve sketched out the state machine , how do you go about coding it? FSMs are useful enough that some game engines, including CryEngine, Unreal, and Second Life, have built-in support for their scripts. Unfortunately, Unity doesn’t, but since this is software, there are a variety of ways to homebrew some FSM support.

One way is to have essentially a big conditional statement inside the Update function that performs different actions depending on which state it’s in (and we’d probably use an enum to define those different states). However, that method tends to result in unwieldy Update functions. In fact, because the Update callback added to the FuguBowl script in Chapter [7](../Text/A310332_2_En_7_Chapter.html) will be obviated by the state machine you’re about to add, you should delete that callback from the FuguBowl script right now.

Another way to create an FSM, which I tried for a while, is to represent a state machine with a GameObject that has several scripts attached, where each script represents a state. Whenever the current state is exited, that script would disable itself and enable the next script or state. This method is appealing, since each script or state has its own Update function and other callbacks. But I find my state machines easier to understand and work with if the entire FSM is contained in a single script.

Ultimately, I’ve settled on implementing state machines with coroutines, which are functions that can suspend themselves for one or more frames at a time by calling yield (the function is yielding control temporarily even though it hasn’t finished executing). You can implement each state as a coroutine that returns when the state is exited. While in the state, the coroutine performs whatever action is supposed to take place during that state, but repeatedly yielding so it doesn’t block the rest of Unity (including the renderer and physics) from running. And before returning, the coroutine specifies the next state it will transition to. Some, but not all, callbacks can be used as coroutines. For example, Awake cannot be used as a coroutine, nor Update, but Start can yield and thus can act as an FSM controller (Listing [8-16](#A310332_2_En_8_Chapter.html#Par84)).

```
private var state:String; // current state in the state machine
enum Roll {
Ball1,
Ball2,
Ball3
}
Listing 8-16.
Start Callback Running State Machine in FuguBowl.js
```

The name of the current state, which is the name of the coroutine implementing that state, is held as a String in a private variable named state. The Start callback loops endlessly, repeatedly calling StartCoroutine with the value of state to invoke the coroutine. Start yields on the call to StartCoroutine so that it waits until the state coroutine has finished executing before starting a coroutine with the next value of state.

There’s no guarantee the coroutines called will yield at all, so Start includes an extra yield in the loop for good measure to make sure at least one yield occurs between states and gives the rest of the game engine an opportunity to work.

As a debugging aid, Start calls Debug.Log before executing each state coroutine (Figure [8-6](#A310332_2_En_8_Chapter.html#Fig6)). This is a convenient way to trace the state machine progress when testing the game.

![A310332_2_En_8_Fig6_HTML.jpg](Images/A310332_2_En_8_Fig6_HTML.jpg)

Figure 8-6.

The Console view of a state machine debug trace

### The States

As a convention, you’ll prefix all the state coroutines with State to indicate those functions are states in a state machine.

#### StateNewGame

The initial state of the bowling state machine is StateNewGame (Listing [8-17](#A310332_2_En_8_Chapter.html#Par90)), which starts a new game by clearing the player’s score and setting the current frame index to 0, specifying the player is currently bowling the first frame of the game.

```
function StateNewGame() {
player.ClearScore();
frame = 0;
state="StateBall1";
}
Listing 8-17.
StateNewGame in FuguBowl.js
```

Before returning (effectively exiting the state), StateNewGame specifies the next state is StateBall1, which is the state where the player begins rolling the first ball of the frame.

#### StateBall1

The first action StateBall1 (Listing [8-18](#A310332_2_En_8_Chapter.html#Par93)) takes is to reset the positions of the pins, the ball, and also the Main Camera. This isn’t really necessary the first time the state is entered (i.e., the beginning of the game), but when the player finishes bowling a frame and transitions back to this state, everything needs to be placed in its starting position and rotation (pins that may be knocked over will have their rotations changed).

```
function StateBall1() {
ResetEverything(); // reset pins, camera and ball
roll = Roll.Ball1;
state="StateRolling";
}
Listing 8-18.
The State to Start Rolling the First Ball in FuguBowl.js
```

After performing the reset, this state sets the roll variable to Roll.Ball1, indicating the player is rolling the first ball of the frame. Finally, this state transitions to StateRolling, the state occupied while the ball is rolling toward the pins .

#### StateBall2

Like StateBall1, StateBall2 (Listing [8-19](#A310332_2_En_8_Chapter.html#Par96)) starts by resetting the Ball and Main Camera.

```
function StateBall2() {
ResetBall();
ResetCamera();
if (GetPinsDown()==10) {
ResetPins();
} else {
RemoveDownedPins();
}
roll = Roll.Ball2;
state="StateRolling";
}
Listing 8-19.
The State to Commence Rolling the Second Ball in FuguBowl.js
```

If the player has reached this point, normally it means the first ball of the frame didn’t result in a strike, and now it’s time to try knocking down the remaining pins, after cleaning up the downed pins from the previous roll. But if all the pins are down, it must be that special case in the tenth frame where a strike then results in two more rolls, and the pins are reset.

Before exiting the state, roll is set to Roll.Ball2, and as with StateBall1, the next state is set to StateRolling .

#### StateBall3

Speaking of the tenth frame , that’s the only case where StateBall3 (Listing [8-20](#A310332_2_En_8_Chapter.html#Par100)) occurs since that’s the only frame in which the player can roll three times.

```
function StateBall3() {
ResetBall();
ResetCamera();
if (GetPinsDown()==10) {
ResetPins();
} else {
RemoveDownedPins();
}
roll = Roll.Ball3;
state="StateRolling";
}
Listing 8-20.
The State to Start Rolling the Third Ball in FuguBowl.js
```

StateBall3 , just like StateBall2, resets the ball, and if you knocked down ten pins from the previous rolls in the frame (either a spare or two strikes), it will reset the pins. Otherwise, the downed pins from the previous roll are cleaned up. Again, the state sets the roll variable to Roll.Ball3 and transitions to StateRolling.

#### StateRolling

StateRolling (Listing [8-21](#A310332_2_En_8_Chapter.html#Par103)) more or less fulfills the Update function created in Chapter [7](../Text/A310332_2_En_7_Chapter.html)  and discarded earlier in this one. The state yields every frame, doing nothing except waiting until the Ball control should cease.

```
function StateRolling() {
while (true) {
// let go of the ball when we reach the pins
if (ball.transform.position.z>pinPos.z) {
state = "StateRolledPast";
return;
}
// gutterball
if (ball.transform.position.y<sunkHeight) {
state = "StateGutterBall";
return;
}
yield;
}
}
}
Listing 8-21.
The State for Rolling the Ball in FuguBowl.js
```

There are two cases where this roll is definitely done. Either the ball rolls off the edge of the floor, which is basically the same situation as a gutter ball in a normal bowling game, or you’ve rolled up to the pins (if you don’t relinquish control, the game is too easy; you can roll back and forth among the pins like a monster truck!).

Detecting a gutter ball is easy. As in the now-removed Update callback, this state checks whether the ball has fallen below the y position specified in the sunkHeight variable. If so, transition to the gutter ball state.The other case is nearly as simple, checking whether the ball’s z position has passed the first pin’s z position. Essentially, the state is checking whether the ball has reached the “pin line” stretching left to right across the floor where that pin is placed. If it has, then transition to StateRolledPast .

You actually didn’t consider having StateGutterBall and StateRolledPast when you sketched out your FSM, but it’s not a big deal. You can now just insert those states in place of the formerly direct transition between StateRolling and StateRollOver.

#### StateRolledPast

After rolling far enough to reach the pins, the StateRolledPast state is entered (Listing [8-22](#A310332_2_En_8_Chapter.html#Par108)). This state “lets go” of the ball by disabling the Ball and Main Camera controls.

```
function StateRolledPast() {
var follow:Behaviour = Camera.main.GetComponent("SmoothFollow");
if (follow != null) {
follow.enabled = false;
}
ball.GetComponent(FuguForce).enabled = false;
yield WaitForSeconds(rolledPastTime);
state = "StateRollOver";
}
Listing 8-22.
The State Entered When Reaching the Pins in FuguBowl.js
```

To disable the Main Camera SmoothFollow script, the state accesses the Main Camera through the static variable main in the Camera class and calls GetComponent on that GameObject, passing in SmoothFollow as the class of the component to be retrieved. That returns the SmoothFollow script that’s attached to the Main Camera. The script is then assigned to a local variable named follow.

The follow variable is declared to be of type Behaviour, which is a direct subclass of Component. A Behaviour can be enabled or disabled by setting its enabled variable. The follow variable could instead have been declared as a more specific class like MonoBehaviour (a subclass of Behaviour and the parent class of any script that can be attached to a GameObject) or even SmoothFollow, the most specific class of the SmoothFollow script because all of those classes inherit the enabled variable from Behaviour. But choosing the most general class that’s suitable provides the most flexibility. For example, if you declared follow to be of type SmoothFollow and then later opted to use a different Camera control script, you would have to adjust the type declaration for follow.

Tip

For type declarations, use the most general class that implements the functions and variables you need.

Another reason not to declare follow to be of type SmoothFollow is that the version of this project available on [`www.apress.com/9781484231739`](http://www.apress.com/9781484231739) doesn’t include any Asset Store or Standard Assets packages, including the SmoothFollow script. Referencing the SmoothFollow class when it’s not available in the project will result in a compiler error.

This is the reason why SmoothFollow is passed to GetComponent as a String so that it’s not used to find a class until GetComponent is called at runtime, at which point GetComponent returns null. Attempting to access the enabled variable on null will result in an error, so the state first checks whether follow is null before attempting to disable it. The FuguForce script attached to the ball is disabled in a similar manner, by calling GetComponent on the ball to retrieve the FuguForce script and then setting its enabled variable to false. But, unlike the Main Camera and the SmoothFollow script, because you’re certain the ball has a FuguForce script attached, there’s no need to perform a null check test before disabling the script.

Furthermore, GetComponent is an overloaded function with one version taking a String argument and the other taking a class directly. So, instead of passing the String "FuguForce" to GetComponent, the class FuguForce is passed directly. This way, a compiler error will result if the class name is misspelled (e.g., GetComponent(FuguFource)).

Passing in a misspelled String name of a class (e.g., GetComponent("FuguFource")) won’t result in an error at compile time, which may at first sound like a good thing, but it’s better to catch problems early. At runtime, that call to GetComponent will return null, which could result in a crash if the code is expecting a non-null result, or just produce aggravating debugging sessions, as you wonder why that script isn’t getting disabled as you expect.

Tip

Let the compiler do your error checking whenever possible.

At some point, the SmoothFollow and FuguForce scripts have to be reenabled. The natural place to do that is in the ResetCamera and ResetBall functions (Listing [8-23](#A310332_2_En_8_Chapter.html#Par118)), as they get called when a roll is over and the Main Camera and Ball GameObjects, respectively, are reset to start another roll.

```
function ResetBall() {
ball.GetComponent(FuguForce).enabled = true;
ball.SendMessage("ResetPosition");
}
function ResetCamera() {
var follow:Behaviour = Camera.main.GetComponent("SmoothFollow");
if (follow != null) {
follow.enabled = true;
}
Camera.main.SendMessage("ResetPosition");
}
Listing 8-23.
ResetBall and ResetCamera Functions in FuguBowl.js
```

In both the ResetBall and ResetCamera functions, the code to enable the script component is the same one used to disable the script, except the enabled variable is set to true.

After disabling both the FuguForce and SmoothFollow scripts , StateRolledPast waits five seconds before transitioning to the next state, StateRollOver, allowing time for the ball to roll into the pins (or past the pins). A while loop that calls yield until the desired amount of time has passed (by checking Time.time) would do the trick, but it’s much more convenient to call yield on the function WaitForSeconds, which takes a number of seconds as an argument. (In StateRolledPast, the number of seconds is hard-coded to 5, but it could easily be a public variable to allow customization.) As a result, the coroutine StateRolledPast will essentially pause (without blocking execution in the rest of Unity) until the given time has elapsed.

#### StateGutterBall

The gutter ball state is a lot simpler than StateRolledPast. It just transitions directly to StateRollOver (Listing [8-24](#A310332_2_En_8_Chapter.html#Par122)).

```
function StateGutterBall() {
state = "StateRollOver";
}
Listing 8-24.
The State for a Gutter Ball in FuguBowl.js
```

Although it seems StateRolling could transition directly to StateRollOver instead of passing through StateGutterBall, the inclusion of StateGutterBall provides some symmetry with StateRolledPast. In a polished bowling game, you would most likely implement some kind of feedback that you rolled a gutter ball. For example, in HyperBowl, animated letters spelling out “Gutter” fly across the screen (Figure [8-7](#A310332_2_En_8_Chapter.html#Fig7)), and this animation takes place in StateGutterBall.

![A310332_2_En_8_Fig7_HTML.jpg](Images/A310332_2_En_8_Fig7_HTML.jpg)

Figure 8-7.

Animated text for a gutter ball in HyperBowl

Those letters are animated with iTween, by the way. You aren’t limited to visual feedback either. In my Fugu Bowl app on the App Store, StateGutterBall plays the sound of kids booing!

#### StateRollOver

When the roll of the ball is deemed complete (the ball has guttered by rolling off the floor or has reached the pin line), the FSM enters StateRollOver (Listing [8-25](#A310332_2_En_8_Chapter.html#Par126)), which wraps up the current roll by figuring out what was scored.

```
function StateRollOver() {
var pinsDown:int = GetPinsDown();
//    Debug.Log("pins down: "+pinsDown);
switch (roll) {
case Roll.Ball1: player.SetBall1Score(frame,pinsDown); break;
case Roll.Ball2: player.SetBall2Score(frame,pinsDown); break;
case Roll.Ball3: player.SetBall3Score(frame,pinsDown); break;
}
if (roll == Roll.Ball1 && player.IsStrike(frame)) {
state = "StateStrike";
return;
}
if (roll == Roll.Ball2 && player.IsSpare(frame)) {
state = "StateSpare";
return;
}
state = "StateKnockedSomeDown";
}
Listing 8-25.
The State for the End of a Roll in FuguBowl.js
```

This state first ascertains how many pins are down by calling the GetPinsDown function. That number is passed to the appropriate FuguBowlPlayer scoring function, based on whether this is the first, second, or third ball, so the score for this roll is recorded. The next state depends on the result of the roll, whether it was a strike (which can happen only on the first ball), a spare (which can happen only on the second ball), or any other result. (StateKnockedSomeDown isn’t a great name, considering you might have knocked down zero pins, but I like it better than StateNotSpareOrStrike.)

#### StateSpare, StateStrike, and StateKnockedSomeDown

Like the gutter ball state, the states for bowling a strike, a spare, and anything else have no actions except for transitioning to the next state (Listing [8-26](#A310332_2_En_8_Chapter.html#Par129)).

```
function StateSpare() {
state="StateNextBall";
}
function StateStrike() {
state = "StateNextBall";
}
function StateKnockedSomeDown() {
state="StateNextBall";
}
Listing 8-26.
StateSpare, StateStrike, and StateKnockedSomeDown in FuguBowl.js
```

All three of these states transition to StateNextBall , so it’s tempting to consider these states redundant and remove them entirely. But for a complete bowling game you might initiate some kind of feedback in each of these states, like congratulatory audio and graphics for bowling a strike. I mentioned that Fugu Bowl on the App Store emits a booing sound during a gutter ball. The same app utters a cheer when a strike is bowled, and this happens in StateStrike. And HyperBowl displays animated “SPARE” and “STRIKE” text in StateSpare and StateStrike, respectively.

#### StateNextBall

StateNextBall is the most complicated state so far (Listing [8-27](#A310332_2_En_8_Chapter.html#Par132)) because most of the bowling rules kick in here. This is where you must decide whether you’re rolling another ball in this frame or advancing to the next frame.

```
function StateNextBall() {
if (frame == 9) { // last frame
switch (roll) {
case Roll.Ball1: // always has a second roll
state = "StateBall2";
break;
case Roll.Ball2: // bonus roll if we got a spare or strike
if (player.IsSpare(frame) || player.IsStrike(frame)) {
state = "StateBall3";
} else {
state = "StateGameOver";
}
break;
case Roll.Ball3:
state = "StateGameOver";
break;
}
// all other frames
} else if (roll == Roll.Ball1 && !player.IsStrike(frame)) {
state = "StateBall2";
} else {
++frame;
state = "StateBall1";
}
}
Listing 8-27.
The State for Transitioning to the Next Ball in FuguBowl.js
```

The tenth frame in particular is complex because you have to check for the end of the game and could potentially have three rolls. So, let’s first look at the bottom portion of the code, which applies to the first nine frames. If you just finished rolling Ball1 and didn’t get a strike, then the next state is StateBall2 . Otherwise, you must have rolled Ball2 or got a strike, so either way, the next state is StateBall1, and you increment your frame index. In the tenth frame, you always roll at least twice, so if you just rolled Ball1, you set the next state to StateBall2\. If you just rolled Ball2 and the result is a spare or strike, then you set the next state to StateBall3; otherwise, you go to the game-over state. And of course if you just rolled Ball3, that’s the last possible roll, and you go to the game-over state.

#### StateGameOver

Finally, you’ve reached the end-of-game state, StateGameOver (Listing [8-28](#A310332_2_En_8_Chapter.html#Par135)).

```
function StateGameOver() {
Debug.Log("Final Score: "+player.GetScore(9));
yield WaitForSeconds(gameOverTime);
state="StateNewGame";
}
Listing 8-28.
The Game Over State in FuguBowl.js
```

In lieu of a fancy user interface (the Unity GUI system will be introduced in the next chapter), let’s take the opportunity to print out the final score in the Console view using the function Debug.Log. The final score is retrieved by calling the FuguBowlPlayer function GetScore with the array index 9, which indicates you want the total score tallied in the tenth frame.

In a complete bowling game, this is the place to put up a “Game Over” message, submit the score to a leader board, and perform any other action appropriate at the end of a game (in HyperBowl, the Application.LoadLevel function is called to switch to a scene displaying the score and present a trophy). For now, let’s just transition back to StateNewGame and start a new game.

### The Complete Listing

The overhauled FuguBowl script is much larger even than the FuguBowlPlayer script, so the entire listing for `FuguBowl.js` isn’t shown here. The complete script is available on [`www.apress.com/9781484231739`](http://www.apress.com/9781484231739) , and all its contents have been presented in this section, piece by piece (or mostly, state by state).

## Explore Further

This chapter was a change of pace, concentrating solely on scripting. Most of that scripting was added to the game controller script, `FuguBowl.js`, in the form of a state machine, but scoring code was incorporated in a new script, `FuguBowlPlayer.js`.

If you’re tired of writing big chunks of code and long for the little script snippets that you started with, you just have to endure writing one more big script in the next chapter, for the user interface. And, as a counterpart for the scoring code added in this chapter, the next one includes a small additional script to display a scoreboard.

### Unity Manual

State machines are useful not only for game control logic but also for anything that can be represented as going through different states (simple examples: a light is on or off, a weapon is locked and loaded, a door is open or closed). State machines are particularly useful for animation (walking, running, throwing), which is why the Mecanim animation system does have its own FSM capability. This is described in the Unity Manual in the “The Mecanim Animation System” section in “Animation State Machines.”

### Scripting Reference

The major scripting technique introduced in this chapter was the use of coroutines, specifically to implement an FSM in the FuguBowl script. The “Scripting Overview” section of the Scripting Reference has a “Coroutines and Yield” page that provides a basic explanation and some examples.

The classes related to coroutines include Coroutine and WaitForSeconds (both of them inherit from YieldInstruction). The one function you used is the StartCoroutine function of MonoBehaviour. The documentation page for each MonoBehavior callback specifies whether the callback can be used as a coroutine (as you saw, Start can, but Awake, Update, and FixedUpdate cannot).

The added code in the FuguBowl script also had frequent cause to access components, so the “Accessing Components” page is relevant. That page lists the Component and GameObject variables that can be used to access commonly attached components and also provides examples on how to call the function GetComponent (either on a component or on a GameObject) to access components, including scripts .

#### Asset Store

Several more sophisticated FSM frameworks are available on the Asset Store . A search for fsm in the Asset Store window brings up several, including a popular visual programming package called Playmaker, developed by Huton Games at [`http://hutongames.com/`](http://hutongames.com/) .

### On the Web

Wikipedia ( [`http://wikipedia.org/`](http://wikipedia.org/) ) has an extensive description of bowling and its scoring rules. Just search for bowling on the site. And if you search for state machine, you’ll find an article that explains probably more than you’ll ever want to know about FSMs!

One of the most well-known virtual worlds, Second Life, has FSM support. Search for state on the Second Life wiki ( [`http://wiki.secondlife.com`](http://wiki.secondlife.com) ) to see how states are defined using the Linden Scripting Language (LSL) .

# 9. The Game GUI

Over the past several chapters, you’ve put together a fairly complete bowling game with 3D graphics, physics, sound, player controls, and automatic camera movement. The game has almost every category of feature expected in a 3D game except for one: a graphical user interface (GUI) . In particular, the bowling game should have a scoreboard, and games usually have a menu that shows up at the beginning of the game and when the game is paused.

Tip

I have had an iOS game rejected by Apple for not having a pause menu, so I recommend including one, if only for that reason.

In this chapter, you’ll implement both a scoreboard and a start/pause menu with Unity’s built-in GUI system, known as UnityGUI. (You can tell how long a Unity feature has been around by the coolness of its name—more recent features have names like Shuriken, Mecanim, and Beast.)

The scoreboard and pause menu scripts are available in the project for this chapter, accessible via the Download Source Code button located at [`www.apress.com/9781484231739`](http://www.apress.com/9781484231739) . To reiterate a point ad nauseam, however, entering the code line by line and function by function provides the best learning experience!

## The Scoreboard

Let’s start off with the scoreboard because it’s a simpler case than the menu. The scoreboard only needs to display, with no interaction required. As described in the previous chapter, a typical bowling scoreboard will show the results for each frame (i.e., the first and second balls, and possibly third, in the tenth frame), as well as the total score up to that frame. You won’t do anything fancy here; you’ll just display the scores with text by drawing the scoreboard as a sequence of labels, with each label displaying the score for that frame as text.

### Create the Script

Let’s get started by creating a GameObject with a scoreboard script attached (this should be a familiar process by now). In the Project view, create a new JavaScript in the Scripts folder and name it FuguBowlScoreboard (Figure [9-1](#A310332_2_En_9_Chapter.html#Fig1)).

![A310332_2_En_9_Fig1_HTML.jpg](Images/A310332_2_En_9_Fig1_HTML.jpg)

Figure 9-1.

Creating the FuguBowlScoreboard.js script

Next, create a new GameObject in the Hierarchy view, name it Scoreboard, and attach the FuguBowlScoreboard script to it.

Now you’re ready to add UnityGUI code to the FuguBowlScoreboard script. If you’ve developed user interfaces with other GUI systems, you’ll probably find UnityGUI a bit unusual. Instead of creating and placing GUI objects that have callbacks responding to events like button clicks, UnityGUI controls are created inside OnGUI callback functions, which are called every frame (actually a few times a frame). To demonstrate quickly how UnityGUI works, place the contents of Listing [9-1](#A310332_2_En_9_Chapter.html#Par9) in the FuguBowlScoreboard script.

```
#pragma strict
function OnGUI() {
GUI.Label(Rect(5,100,200,20),"This is a label");
}
Listing 9-1.
Testing a Simple UnityGUI Label in FuguBowlScoreboard.js
```

All UnityGUI controls are created with calls to static functions in the GUI class (and also GUILayout, but I’ll get to that in the next section). The OnGUI callback of Listing [9-1](#A310332_2_En_9_Chapter.html#Par9) calls GUI.Label to draw a label, passing in a Rect (rectangle) that specifies the label is to be drawn at the x,y coordinates of 5,100 (the coordinate system for UnityGUI has 0,0 at the top left of the screen) and with a width and height of 200 and 20 pixels, respectively. The second argument is the text to display in the label. Click Play, and you should see “This is a label” appear on the screen.

Placing user interface code in a single script callback can be cumbersome if the user interface is complex. But for this simple user interface, the OnGUI callback is convenient, and you can expand on it to form a no-frills text-based bowling scoreboard. Let’s replace the single-label example in FuguBowlScoreboard with the contents of Listing [9-2](#A310332_2_En_9_Chapter.html#Par12).

```
#pragma strict
var style:GUIStyle; // customize the appearance
function OnGUI() {
for (var f:int=0; f<10; f++) {
var score:String="";
var roll1:int = FuguBowl.player.scores[f].ball1;
var roll2:int = FuguBowl.player.scores[f].ball2;
var roll3:int = FuguBowl.player.scores[f].ball3;
switch (roll1) {
case -1: score += " "; break;
case 10: score +="X"; break;
default: score += roll1;
}
score+="/";
if (FuguBowl.player.IsSpare(f)) {
score +="I";
} else {
switch (roll2) {
case -1: score += " "; break;
case 10: score +="X"; break;
default: score += roll2;
}
}
if (f==9) {
score+="/";
if (10==roll2+roll3) {
score +="I";
} else {
switch (roll3) {
case -1: score += " "; break;
case 10: score +="X"; break;
default: score += roll3;
}
}
}
GUI.Label(Rect(f*30+5,5,50,20),score,style);
var total:int=FuguBowl.player.GetScore(f);
if (total != -1) {
GUI.Label(Rect(f*30+5,20,50,20)," "+total,style);
}
}
}
Listing 9-2.
A Bowling Scoreboard
```

The new OnGUI callback loops through all ten frames, querying the FuguBowlPlayer for the FuguBowlScore representing each frame. At the bottom of the loop, one or two calls to GUI.Label are issued. The first GUI.Label displays the score for each ball of the frame, and the second GUI.Label is drawn below the first, displaying the game’s total score for that frame if it is available.

Most of the code in the loop is devoted to figuring out what to display for each ball of the frame. For ball1, if it has not been rolled yet, then the String to display is just a space. If a strike has been rolled, then the letter X is displayed. Otherwise, you can display the numeric score (1 to 9).

Ball2 is similar to Ball1, except it also displays the letter I for a spare. Normally, I would use the slash character (/) for a spare, but that is used here to separate the scores for Ball1, Ball2, and Ball3.

The String for Ball3 is constructed only if the frame is the tenth frame (index 9). Ball3 is the same as Ball2, except it checks for a spare combining Ball2 and Ball3 instead of Ball1 and Ball2.

The frame total score is retrieved just by calling the FuguBowlPlayer function GetScore and is displayed as long as long as the score is available, i.e., not -1\. The Rect for each GUI.Label is offset from the top left of the screen by an increment based on the frame that GUI.Label is displaying (Figure [9-2](#A310332_2_En_9_Chapter.html#Fig2)).

![A310332_2_En_9_Fig2_HTML.jpg](Images/A310332_2_En_9_Fig2_HTML.jpg)

Figure 9-2.

A bowling scoreboard displayed by FuguBowlScoreboard.js

Figure [9-2](#A310332_2_En_9_Chapter.html#Fig2) shows a game on the second ball of the fourth frame. Three pins have been knocked down on the first ball. Strikes have been rolled on the first two frames and a spare on the third (five pins on the first roll and five on the second). The game total for the first frame is 10 for the strike plus the pins knocked down by the next two balls, which is 10 for the second strike and then 5 for the first ball in the third frame, for a total of 25.

The second frame also has a strike, so it’s 10 plus 5 for the first ball of the third frame and 5 for the second ball of that frame, for a total of 20\. Add that to the 25 from the first frame, for a game total of 45.

The third frame has a spare, so it’s 10 again but with the next ball added, which is 3, for a total of 13; add that to the game total from the previous frame, and that’s 58\. So, your scoring code and scoreboard code works!

### Style the GUI

The appearance of a GUI control can be customized with a GUIStyle, which is a collection of properties that affect the display of the control. Conceptually, it’s similar to how Cascading Style Sheets (CSS) are used in formatting web content.

Each GUI function that creates a GUI control is an overloaded function that comes in two flavors: one that uses the default GUIStyle and one that takes a GUIStyle argument. The initial single-label example just used the default GUIStyle, but the scoreboard uses the version of GUI.Label that takes a GUIStyle argument, which provides a way to customize the scoreboard’s appearance.

The GUIStyle passed to the GUI.Label is bound to a public variable named style, which allows customization of the GUIStyle in the Inspector view. For example, since the scoreboard is all text, you can click the Text Color field in the Normal subsection of the style (equivalent to accessing style.onNormal.textColor in the script) to bring up a color chooser and change the color of the scoreboard (Figure [9-3](#A310332_2_En_9_Chapter.html#Fig3)).

![A310332_2_En_9_Fig3_HTML.jpg](Images/A310332_2_En_9_Fig3_HTML.jpg)

Figure 9-3.

GUIStyle options

Among the many other GUIStyle properties, several affect the GUI font, which defaults to Unity’s built-in font, Arial. You can change the font by dragging a Font asset from the Project view into the Font field of the GUIStyle (in the script, that would be the variable style.font). Any font in TrueType, OpenType, or dfont format can be imported into Unity.

Other GUIStyle properties control the font size and style (for fonts imported as dynamic fonts), text alignment, and word wrap.

Rich-text format is particularly interesting because it provides for HTML-like markup in the label text. So, really, although the script is simple, your style customization options are plentiful!

## The Pause Menu

Compared with the scoreboard, a start/pause menu will take considerably more scripting, so, first, a design is in order. Let’s create a main menu and two submenus: one for game options and one to display the game credits. The options menu will have distinct panels for audio, graphics, system, and stats. The logic is not as complicated as for the bowling game controller, but it’s another example where you should first sketch out the design as a state diagram (Figure [9-4](#A310332_2_En_9_Chapter.html#Fig4)).

![A310332_2_En_9_Fig4_HTML.jpg](Images/A310332_2_En_9_Fig4_HTML.jpg)

Figure 9-4.

A state diagram for a pause menu

The diagram shows that the player can go from the game-playing state (or equivalently, the menu-invisible state) to the main pause menu and, from there, to a credits screen or an options menu, or the player can quit the game. The options menu in turn can display an audio, graphics, system, or statistics panel, each of which could be listed as states, but for now, let’s keep it simple by just thinking of each top-level menu screen as a state.

All states except for Quit (which you can think of as the exit state) can transition back to the state that transitioned to them in the first place, which is why each transition arrow is drawn bidirectionally. This means the player can go from the main menu to the options menu and back to the main menu and from there can go back to the normal gameplay mode. But there won’t be any shortcuts going from the options menu straight back to gameplay, for example.

### Create the Script

Let’s start the pause menu implementation by creating a new JavaScript in the Scripts folder of the Project view and naming it FuguPause (Figure [9-5](#A310332_2_En_9_Chapter.html#Fig5)).

![A310332_2_En_9_Fig5_HTML.jpg](Images/A310332_2_En_9_Fig5_HTML.jpg)

Figure 9-5.

Creating the FuguPause.js script

Now, to add the script to the scene, create a new GameObject in the Hierarchy view, name it PauseMenu, and attach the FuguPause script to the PauseMenu GameObject.

### Track the Current Menu Page

The technique used in the FuguBowl script of implementing a state machine with coroutines won’t work with UnityGUI, as all of the UnityGUI functions must be called within an OnGUI callback. But the state diagram is still useful as the basis for an implementation. For starters, you can focus on the states that represent distinct menu screens. Let’s call them pages to distinguish them from the more general term screen. These menu states could be distinguished by strings or integers, but an enumeration fits the bill when you have several related but distinct values. So, let’s define an enum called Page for the menu page states and a variable named currentPage to track which page is currently being displayed (Listing [9-3](#A310332_2_En_9_Chapter.html#Par33)).

```
enum Page {
None,Main,Options,Credits
}
Listing 9-3.
Enumerations for Menu Pages
```

Following the state diagram, the enum lists a state for no menu (the game is playing), the main menu page, the options page, and the credits page. Because the variable currentPage is of type Page, only the values Page.None, Page.Main, Page.Options, and Page.Credits can be assigned to that variable.

### Pause the Game

Making a pause menu involves solving two problems: making a menu and making the game pause. You’ll start with making the game pause. The static variable Time.timeScale specifies how fast the simulated game time progresses and defaults to 1\. Setting Time.timeScale to 0 effectively pauses the game, suspending physics, animation, and anything else dependent on the advance of Time.time. So, let’s make a PauseGame function that sets Time.timeScale to 0 (Listing [9-4](#A310332_2_En_9_Chapter.html#Par36)).

```
private var savedTimeScale:float;
function PauseGame() {
savedTimeScale = Time.timeScale;
Time.timeScale = 0;
AudioListener.pause = true;
currentPage = Page.Main;
}
Listing 9-4.
A Function to Pause the Game in FuguPause.js
```

PauseGame first saves the current Time.timeScale in a variable savedTimeScale so you can restore Time.timeScale when the game is unpaused (you shouldn’t assume Time.timeScale is 1 when the game is paused).

PauseGame also sets AudioListener.pause to true, which suspends any playing sounds (you don’t want to do this if you’re going to play sounds in the menu).

Finally, the currentPage variable is set to Page.Main, indicating the main pause screen should be displayed.

The game is unpaused by reversing all of that (Listing [9-5](#A310332_2_En_9_Chapter.html#Par41)).

```
function UnPauseGame() {
Time.timeScale = savedTimeScale;
AudioListener.pause = false;
currentPage = Page.None;
}
Listing 9-5.
Function That Unpauses the Game in FuguPause.js
```

UnPauseGame restores Time.timeScale to the value you saved in the variables savedTimeScale by PauseGame and sets AudioListener.pause to false to reenable audio.

To see whether the game is paused, you can just check whether Time.timeScale is 0\. Listing [9-6](#A310332_2_En_9_Chapter.html#Par44) shows a little convenience function for that. Notice that it’s a static function, so any script can reference it as FuguPause.IsGamePaused().

```
static function IsGamePaused() {
return Time.timeScale==0;
}
Listing 9-6.
Checking Whether the Game Is Paused in FuguPause.js
```

If the game is to start paused, the Start callback should have a call to PauseGame (Listing [9-7](#A310332_2_En_9_Chapter.html#Par46)). Adding a public boolean startPaused variable and checking it before calling PauseGame makes the initial pause optional.

```
var startPaused:boolean = true;
function Start() {
if (startPaused) {
PauseGame();
}
}
Listing 9-7.
Pausing the Game in the Start Callback of FuguPause.js
```

Since the default value for startPaused is true, if you click Play, the game will immediately pause, showing the ball suspended in the air. And then you’re stuck, since there’s no menu. There’s also no way to unpause and then pause again, so let’s have the Escape key (the key labeled Esc on the top left of most keyboards) toggle the pause state. Input handling is usually performed in the Update callback, and this is no exception (Listing [9-8](#A310332_2_En_9_Chapter.html#Par48)).

```
function Update() {
if (Input.GetKeyDown(KeyCode.Escape))
{
switch (currentPage) {
case Page.None: PauseGame(); break; // if the pause menu is not displayed, then pause
case Page.Main: UnPauseGame(); break; // if the main pause menu is displaying, then unpause
default: currentPage = Page.Main; // any subpage goes back to main page
}
}
}
Listing 9-8.
Handling the Escape Key in the Update Callback of FuguPause.js
```

The first line checks whether the Esc key has been pressed. If so, then it calls PauseGame, unless the pause menu is already up. The function also treats Esc as a back key, switching from a subpage to the main page or from the main page to an unpaused state.

### Check Time.DeltaTime

Setting Time.timeScale to 0 halts the advance of Time.time, so Time.deltaTime is always 0\. This works great for Update functions that multiply values by Time.deltaTime, but dividing by Time.deltaTime when time is frozen results in a division-by-zero error. There is such a case in the Update callback of the FuguForce script, so before proceeding with the pause menu, that has to be taken care of (Listing [9-9](#A310332_2_En_9_Chapter.html#Par51)).

```
function Update() {
forcex = 0;
forcey = 0;
if (Time.deltaTime > 0) {
CalcForce();
}
}
function CalcForce() {
var deltaTime:float = Time.deltaTime;
forcex = mousepowerx*Input.GetAxis("Mouse X")/deltaTime;
forcey = mousepowery*Input.GetAxis("Mouse Y")/deltaTime;
}
Listing 9-9.
Avoiding Divide-by-Zero in FuguForce.js
```

Instead of immediately dividing the rolling force values by Time.deltaTime, now the Update callback checks that Time.deltaTime is not 0 before proceeding with the force calculation, now moved into a CalcForce function. Update always initializes the force values to 0 to ensure that no lingering force is applied to the bowling ball while the game is paused.

### Display the Menu

The actual display the pause menu, as with the scoreboard in the previous section, must take place in an OnGUI callback function. Specifically, OnGUI needs to check whether the game is paused and, if so, then display the current menu page (Listing [9-10](#A310332_2_En_9_Chapter.html#Par54)).

```
function OnGUI () {
if (IsGamePaused()) {
if (skin != null) {
GUI.skin = skin;
} else {
GUI.color = hudColor;
}
switch (currentPage) {
case Page.Main: ShowPauseMenu(); break;
case Page.Options: ShowOptions(); break;
case Page.Credits: ShowCredits(); break;
}
}
}
Listing 9-10.
The OnGUI Callback in FuguPause.js
```

For now, there are placeholders for each of the page display functions, keeping the script in a runnable state (i.e., without compilation errors) as you fill out each of the display functions one at a time. It’s a good idea to place Debug.Log statements inside these stub functions to verify they’re called when expected. For example, if you click Play, you should see “Main Pause” appear in the Console view every time you pause the game.

### Automatic Layout

For the menu buttons that are stacked vertically and centered in the screen, you can take advantage of the GUILayout functions to avoid figuring out all the Rects would otherwise have to be passed for each GUI.Button. Between calls to GUILayout.BeginArea and GUILayout.EndArea, you can make calls to create GUI controls without passing Rects, using the GUILayout versions of the GUI functions. The controls will be automatically placed and sized within the Rect that was passed to GUILayout.BeginArea. Since all the pause menu pages will be displayed the same way, let’s make some convenience functions that wrap around the GUILayout functions (Listing [9-11](#A310332_2_En_9_Chapter.html#Par57)).

```
var menutop:int=25;
function BeginPage(width:int,height:int) {
GUILayout.BeginArea(Rect((Screen.width-width)/2,menutop,width,height));
}
function EndPage() {
// show Back button if not Main page
if (currentPage != Page.Main && GUILayout.Button("Back")) {
currentPage = Page.Main;
}
GUILayout.EndArea();
Listing 9-11.
GUILayout Functions for Positioning Pages in FuguPause.js
```

The BeginPage function calls GUILayout.BeginArea, and the area is specified by the width and height passed as arguments, horizontally centered on the screen and with its top edge distance from the top of the screen specified by the public variable called menutop.

The EndPage function calls GUILayout.EndArea, but before that, if the current pate is not the main page, it displays a Back button. If that button is clicked, then the current page is set to the main page.

## The Main Page

Now you have all of the pieces to get a menu on the screen. Listing [9-12](#A310332_2_En_9_Chapter.html#Par61) shows a fleshed-out ShowPauseMenu function.

```
function ShowPauseMenu() {
BeginPage(150,300);
if (GUILayout.Button ("Play")) {
UnPauseGame();
}
if (GUILayout.Button ("Options")) {
currentPage = Page.Options;
}
if (GUILayout.Button ("Credits")) {
currentPage = Page.Credits;
}
#if !UNITY_WEBPLAYER && !UNITY_EDITOR
if (GUILayout.Button ("Quit")) {
Application.Quit();
}
#endif
EndPage();
}
Listing 9-12.
Function That Displays the Pause Menu in FuguPause.js
```

All of the GUI code between BeginPage and EndPage effectively takes place between GUILayout.BeginArea and GUILayout.EndArea, so you can use the GUILayout versions of the GUI calls to create elements without passing Rects. For example, instead of calling GUI.Button, you could call GUILayout.Button. Aside from the missing Rect parameter, the functions look otherwise identical.

GUILayout.Button is different from GUILayout.Label in that it returns true or false depending on whether the button has been pressed. So, each call to GUILayout.Button takes place inside an “if” test and executes the appropriate statement if the button has indeed been pressed.

Now create a GameObject and name this MainMenu. Add the FuguPause Script component to this GameObject. You can select Add Component from the Inspector or drag the FuguPause script from the Scripts folder in the Project view and drop this on the new MainMenu GameObject you just created.

When you click Play, you’ll now see the main menu, and the Play button in the menu should unpause the screen (Figure [9-6](#A310332_2_En_9_Chapter.html#Fig6)). Application.Quit has no functionality in a Unity web player or in the Editor, so that piece of code is surrounded by a test of the corresponding preprocessor definitions UNITY_WEBPLAYER and UNITY_EDITOR to see if the build target is a web player or if you’re running in the Editor. These definitions are evaluated just before compilation takes place (hence the term preprocessor), so if UNITY_WEBPLAYER is false and UNITY_EDITOR is false, the enclosed code is compiled. Otherwise, it’s as if the code were never there.

![A310332_2_En_9_Fig6_HTML.jpg](Images/A310332_2_En_9_Fig6_HTML.jpg)

Figure 9-6.

The main menu

The Credits and Options buttons won’t do anything yet because the ShowCredits and ShowOptions functions are still empty shells. The Credits page is simpler, so let’s start with that one.

## The Credits Page

Multiple credit entries can be stored in a String array. A variable called credits is defined for that, along with a ShowCredits function to display the credits, in Listing [9-13](#A310332_2_En_9_Chapter.html#Par68).

```
var credits:String[]=[
"A Fugu Games Production",
"Copyright (c) 2017 Technicat, LLC. All Rights Reserved.",
"More information at http://fugugames.com/"] ;
function ShowCredits() {
BeginPage(300,300);
for (var credit in credits) {
GUILayout.Label(credit);
}
EndPage();
}
Listing 9-13.
Function to Display the Credits Page in FuguPause.js
```

Because the variable credits is public, you can edit each of the credit entries and add to the array or remove from it in the Inspector view.

The ShowCredits function loops through the array of credits and displays each one in a UnityGUI label (Figure [9-7](#A310332_2_En_9_Chapter.html#Fig7)). Notice that there is a simple way to loop through the array using in instead of the usual habit of iterating through a sequence of array indices. In this particular case, you don’t need an index for anything else, so let’s go with the simpler method.

![A310332_2_En_9_Fig7_HTML.jpg](Images/A310332_2_En_9_Fig7_HTML.jpg)

Figure 9-7.

The Credits page

Like the main menu, you start with a call to BeginPage and end with EndPage so you’re operating inside a GUILayout area (this time 300 × 300) and can use GUILayout.Label instead of GUI.Label. EndPage ensures that there automatically is a Back button. Remember, the Update callback also treats the Esc key as equivalent to clicking the Back button.

## The Options Page

The Options page is quite a bit more involved than the Credits page, as it features a toolbar implemented with four tabs: Audio, Graphics, Stats, and System. Listing [9-14](#A310332_2_En_9_Chapter.html#Par73) shows the fleshed-out ShowToolbar function, with some supporting variables and functions.

```
private var toolbarInt:int=0;
private var toolbarStrings: String[]= ["Audio","Graphics","System"];
function ShowOptions() {
BeginPage(318,300);
toolbarInt = GUILayout.Toolbar (toolbarInt, toolbarStrings);
switch (toolbarInt) {
case 0: ShowAudio(); break;
case 1: ShowGraphics();  break;
case 2: ShowSystem(); break;
}
EndPage();
}
Listing 9-14.
The Options Page in FuguPause.js
```

Again, everything in the ShowOptions function is enclosed between calls to the BeginPage and EndPage functions. The first line calls GUILayout.Toolbar to create a toolbar—a row of buttons that acts as a tab or radio button—from an array of Strings, where each String is the label of the button. The function also takes an integer that corresponds to a position in that array of strings. That is the button to make currently active on the toolbar. But if you just had

```
GUILayout.Toolbar (toolbarIndex, toolbarStrings);
```

then `toolbarIndex` would never change from its initial value (0), and even if you click another button, the next time this is called (in the next invocation of OnGUI), the active button would be set back to that value.

But GUILayoutToolbar returns an integer that represents the currently active button, so you feed that value back into the variable that you passed in, toolbarIndex.

```
toolbarIndex = GUILayout.Toolbar (toolbarIndex, toolbarStrings);
```

Thus, toolbarIndex is 0 initially, but if you click Graphics, it’ll change to 1, and if you click Controls, it’ll change to 2, and so on. Then there is a switch statement that checks toolbarIndex and calls the display function that matches the selected button. As with the main menu, we start with stub functions and implement them one by one. Go ahead and click around, and you should see the corresponding function names show up in the Console view because that’s all you’ve told it to do!

## The Audio Panel

When the Audio tab is selected, it will display a slider for volume control (Figure [9-8](#A310332_2_En_9_Chapter.html#Fig8)).

![A310332_2_En_9_Fig8_HTML.jpg](Images/A310332_2_En_9_Fig8_HTML.jpg)

Figure 9-8.

The Audio panel in the pause menu

Adding that slider to the ShowAudio function is actually pretty easy; it’s just one line. Well, it consists two lines since a slider with no label is a bit too mysterious (Listing [9-15](#A310332_2_En_9_Chapter.html#Par82)).

```
function ShowAudio() {
GUILayout.Label("Volume");
AudioListener.volume = GUILayout.HorizontalSlider(AudioListener.volume,0.0,1.0);
Listing 9-15.
The Audio Panel in FuguPause.js
```

The first line calls GUILayout.Label, which should be familiar by now. The second line calls GUILayout.HorizontalSlider, which takes as arguments the minimum value represented by the slider, the maximum value, and a value that represents the current setting.

The slider reflects the value of AudioListener.volume, which is the master volume of all sounds in Unity. So, AudioListener.volume is passed as the current value of the slider, and because AudioListener.volume ranges from 0 to 1, those values are passed as the minimum and maximum, respectively. And, to register the slider value, the return value of GUILayout.Slider is assigned back to AudioListener.volume. Otherwise, the value of AudioListener.Volume will never change and the slider won’t budge.

## The Graphics Panel

The Graphics panel will display the some of the same graphics quality information shown by the quality settings of the Unity Editor (Figure [9-9](#A310332_2_En_9_Chapter.html#Fig9)).

![A310332_2_En_9_Fig9_HTML.jpg](Images/A310332_2_En_9_Fig9_HTML.jpg)

Figure 9-9.

Graphics options in the pause menu

The two buttons at the bottom of the panel increase and decrease the quality settings level. This panel doesn’t require any new UnityGUI controls to implement this panel, but it does access the QualitySettings class (Listing [9-16](#A310332_2_En_9_Chapter.html#Par87)).

```
function ShowGraphics() {
GUILayout.Label(QualitySettings.names[QualitySettings.GetQualityLevel()]);
GUILayout.Label("Pixel Light Count: "+QualitySettings.pixelLightCount);
GUILayout.Label("Shadow Cascades: "+QualitySettings.shadowCascades);
GUILayout.Label("Shadow Distance: "+QualitySettings.shadowDistance);
GUILayout.Label("Soft Vegetation: "+QualitySettings.softVegetation);
GUILayout.BeginHorizontal();
if (GUILayout.Button("Decrease")) {
QualitySettings.DecreaseLevel();
}
if (GUILayout.Button("Increase")) {
QualitySettings.IncreaseLevel();
}
GUILayout.EndHorizontal();
}
Listing 9-16.
Function That Displays the Quality Settings
```

The ShowGraphics function is fairly straightforward. It retrieves the current quality settings level, represented as an integer, by calling QualitySettings.GetQualityLevel, and then it uses that integer as an index to the QualitySettings.names String array to retrieve the name of that quality setting. That name and several quality settings are displayed in labels, and two buttons are placed at the bottom: one calling QualitySettings.DecreaseLevel and the other calling QualitySettings.IncreaseLevel.

The two buttons allow you to increase and decrease the quality settings level, within the range of existing levels, and not only will you see the displayed quality settings information change, you might see the graphical quality of the scene change before your eyes, too, since it’s still being rendered every frame, even with the game time pause.

## The System Panel

The System panel displays information about the hardware platform (Figure [9-10](#A310332_2_En_9_Chapter.html#Fig10)).

![A310332_2_En_9_Fig10_HTML.jpg](Images/A310332_2_En_9_Fig10_HTML.jpg)

Figure 9-10.

The System panel in the pause menu

As in the Graphics page, the information is displayed with labels, but whereas the Graphics page accesses the QualitySettings class, the System panel accesses the SystemInfo class (Listing [9-17](#A310332_2_En_9_Chapter.html#Par92)).

```
function ShowSystem() {
GUILayout.Label("Graphics: "+SystemInfo.graphicsDeviceName+" "+
SystemInfo.graphicsMemorySize+"MB\n"+
SystemInfo.graphicsDeviceVersion+"\n"+
SystemInfo.graphicsDeviceVendor);
GUILayout.Label("Shadows: "+ Available(SystemInfo.supportsShadows));
GUILayout.Label("Image Effects: "+Available(SystemInfo.supportsImageEffects));
// GUILayout.Label("Render Textures: "+Available(SystemInfo.supportsRenderTextures));
}
Listing 9-17.
The System Panel in FuguPause.js
```

The SystemInfo class provides information about the graphics hardware and its capabilities, including some identifying information such as the device name and the name of the vendor, its graphics memory size, and whether the hardware supports some of the more advanced features, such as dynamic shadows, rendering to a texture, or image effects (which requires rendering to a texture).

### Customize the GUI Color

You may have noticed the white text in the pause menu is often difficult to read. One solution is to change the static variable GUI.color, which tints the entire GUI with a Color value (Listing [9-18](#A310332_2_En_9_Chapter.html#Par95)).

```
var hudColor:Color = Color.white;
function OnGUI () {
if (IsGamePaused()) {
if (skin != null) {
GUI.skin = skin;
} else {
GUI.color = hudColor;
}
switch (currentPage) {
case Page.Main: ShowPauseMenu(); break;
case Page.Options: ShowOptions(); break;
case Page.Credits: ShowCredits(); break;
}
}
}
Listing 9-18.
GUI Color Customization in FuguPause.js
```

You add a public variable called hudColor to the FuguPause script so a color can be chosen in the Inspector view. All UnityGUI operations, including setting variables like GUI.Color, must take place within an OnGUI callback, so the assignment to GUI.color is placed inside the OnGUI callback before any of the GUI controls are created (but after IsGamePaused is called since there’s no reason to set GUI.Color if there’s no GUI rendering). Note that you can set GUI.color multiple times within OnGUI to change the color before rendering various parts of the GUI.

Now you can select a color in the Inspector view (Figure [9-11](#A310332_2_En_9_Chapter.html#Fig11)) and see the resulting GUI tint (Figure [9-12](#A310332_2_En_9_Chapter.html#Fig12)).

![A310332_2_En_9_Fig12_HTML.jpg](Images/A310332_2_En_9_Fig12_HTML.jpg)

Figure 9-12.

The pause menu with a custom color

![A310332_2_En_9_Fig11_HTML.jpg](Images/A310332_2_En_9_Fig11_HTML.jpg)

Figure 9-11.

Color selection for the pause menu

### Customize the Skin

The default UnityGUI skin is pretty neutral, and it’s hard to read the text, as you can see with the pause menu from the previous section. With the scoreboard, you could change the text color by adjusting the style to GUI.Label, but if you have a lot of GUI elements, that can be a hassle.

That’s where UnityGUI skins come in. Skins are collections of styles assigned to the various GUI elements. Applying a skin is easy—just declare a public variable of type GUISkin and assign that skin to the variable GUI.skin, within the OnGUI callback, similar to assigning a GUI.Color (Listing [9-19](#A310332_2_En_9_Chapter.html#Par100)).

```
var skin:GUISkin;
function OnGUI () {
if (IsGamePaused()) {
if (skin != null) {
GUI.skin = skin;
} else {
GUI.color = hudColor;
}
switch (currentPage) {
case Page.Main: ShowPauseMenu(); break;
case Page.Options: ShowOptions(); break;
case Page.Credits: ShowCredits(); break;
}
}
}
Listing 9-19.
Adding a GUISkin to the Pause Menu
```

You can test this skin support with a really cool-looking free UnityGUI skin from the Asset Store called the Necromancer GUI (Figure [9-13](#A310332_2_En_9_Chapter.html#Fig13)).

![A310332_2_En_9_Fig13_HTML.jpg](Images/A310332_2_En_9_Fig13_HTML.jpg)

Figure 9-13.

The Necromancer GUI on the Asset Store

Download and import the Necromancer GUI and then drag its skin file (named Necromancer GUI) into the Skin property that’s now in the Inspector view (Figure [9-14](#A310332_2_En_9_Chapter.html#Fig14)).

![A310332_2_En_9_Fig14_HTML.jpg](Images/A310332_2_En_9_Fig14_HTML.jpg)

Figure 9-14.

The Necromancer GUI

Then click Play, and you have a much more ornate pause menu. Figure [9-15](#A310332_2_En_9_Chapter.html#Fig15) shows how the pause menu Graphics panel looks with the Necromanger GUI. Look at all the pretty buttons!

![A310332_2_En_9_Fig15_HTML.jpg](Images/A310332_2_En_9_Fig15_HTML.jpg)

Figure 9-15.

The Necromancer GUI skin in action

### The Complete Script

The finalized start/pause menu script is significantly longer than the scoreboard script, so the complete listing is omitted here. All the code has been shown in this chapter, and the entire script `FuguPause.js` is available in the project for this chapter at [`www.apress.com/9781484231739`](http://www.apress.com/9781484231739) .

## Explore Further

Finally, the scoreboard display makes your bowling game a fully functional bowling game, and the addition of a start/pause menu provides some of the polish that players expect!

The addition of the game GUI not only completes the bowling game (in a commercial game project, this might be considered a “first playable” milestone) but also marks the end of the introduction to Unity’s basic features typically used in a 3D game.

Tip

Although I left the GUI development to the end of this phase, which is unfortunately common in game development, it’s best to include GUI design early in the project. Even just sketching out the menus will help clarify the anticipated game modes and options.

For the most part, although you’ve been modifying and testing the game solely in the Editor, these are cross-platform features. Right now, you could build this game as a web player or a Mac or Windows executable, and the game would run the same on all platforms, performance differences aside. But the ultimate goal of this book is to get you into iOS development, so, starting with the next chapter, the remainder of this book will be all about Unity iOS.

### Unity Manual

The Game Interface Elements link in the “Creating Gameplay” section of the Unity Manual leads to the “Unity Scripting Guide” section, which is a tutorial-style sequence of pages going through the UnityGUI system, from creating a single button to adding various other UnityGUI controls such as sliders and radio buttons, using automatic layout versus fixed layout, creating reusable compound controls, and customizing the GUI appearance with styles and skins.

The “Unity Scripting Guide” also explains how you can customize the Unity Editor using UnityGUI. It turns out the Editor interface is actually implemented with UnityGUI (which explains why on occasion you may see an OnGUI error message in the Console view that is not related to any of your code)!

### Reference Manual

I briefly mentioned how additional fonts besides the built-in Unity font can be imported into a Unity project and then assigned to UnityGUI styles and skins. The Reference Manual has a page in its “Asset Components” section describing the Font asset and its important options in more detail.

### Scripting Reference

Naturally, the “Runtime Classes” list in the Scripting Reference includes pages describing the UnityGUI functions you’ve used, starting with the OnGUI callback defined in the MonoBehaviour class.

Most of the UnityGUI functions used in both the scoreboard and pause menu are the static GUI and GUILayout functions used to create the various UnityGUI controls, such as GUI.Button and GUI.Label. It’s worth going through the Scripting Reference pages for each of them so you know what GUI controls are available, along with customization variables like GUI.color and GUI.skin (and there are others, e.g., to customize the background color). GUIStyle and GUISkin are classes too, and you can change their properties any time in a script.

Besides the UnityGUI classes, a few others are used in the pause menu. The static variable Time.timeScale was set to pause and unpause the game; Application.Quit was called to exit the game; and the AudioListener, QualitySettings, and SystemInfo classes were accessed in the Options page.

### Asset Store

The Necromancer GUI looked great in the pause menu, demonstrating how nice UnityGUI can look with a well-crafted GUISkin, but there are many others on the Asset Store. They’re all listed, along with the Necromancer GUI, in the GUI Skins category under Textures and Materials. That category is well populated with skins for UnityGUI and also skins for third-party GUI systems, such as the popular EZGUI from Above and Beyond Software ( [`http://anbsoft.com/`](http://anbsoft.com/) ) and NGUI from Tasharen Entertainment ( [`http://tasharen.com`](http://tasharen.com) ).

Those third-party GUI systems are available in the Scripting category’s well-populated GUI subcategory, which is filled with prescripted GUIs such as minimaps and menus. In fact, a version of the pause menu implemented in this chapter is on the Asset Store under Complete Projects (with the name FuguPause).

The Asset Store also has a decent assortment of fonts listed under the Fonts category of Textures and Materials. But since Unity can import TrueType and OpenType fonts, plenty of free font sites on the Web are available to you, along with moderately priced font libraries (I use the MacXWare Font Library).

# 10. Using Unity iOS

Congratulations! You now have a bowling game incorporating the basic features that typically comprise a 3D game: 3D graphics (of course), physics, sound effects, player control (of the bowling ball), camera movement, and a graphical user interface .

The default built target is listed on the title bar of the Editor window as PC, Mac and Linux Standalone. If you were to perform an macOS build of the bowling project right now, the game would run essentially the same as it does in the Editor. It’s the same for Linux, Windows, and even a web player (although a web player build would require you to switch build targets first).

Targeting iOS is another story. You could change the build target for the bowling game to iOS right now and build it without any compiler errors. But the bowling ball control in `FuguForce.js` is designed for mouse input, so that would have to change. Besides re-implementing the input handling, adapting desktop PC games to mobile devices (a process called porting ) often requires adjustments for the device display and compromises to achieve adequate performance.

Moreover, building an iOS app involves much more in the way of external procedures than the desktop stand-alone and web player targets. Because of Apple’s requirement that all iOS apps be compiled with Xcode, Unity iOS builds an app by first generating an Xcode project, which in turn is compiled by Xcode to create the final app. To actually run the app on a test device and submit the app to the App Store, a rather complicated sequence of procedures is required, along with registration in Apple’s iOS Developer Program. For now, let’s go back to your roots, so to speak, and temporarily return to the Climber project that you started with in this book. Conveniently, Climber is ready to run on iOS without modification. If you have an iOS device, go ahead and download Climber from the App Store. A quick way to find the Climber app is to enter Climber in the App Store search field, and Angry Bots will appear, along with other apps from Unity Technologies (Figure [10-1](#A310332_2_En_10_Chapter.html#Fig1)).

![A310332_2_En_10_Fig1_HTML.jpg](Images/A310332_2_En_10_Fig1_HTML.jpg)

Figure 10-1.

Example apps from the App Store

Besides Climber, you should also search and download the Unity Remote app, which acts as a remote control for testing a Unity iOS game in the Editor. While you’re at it, you should download and try all the example apps from Unity Technologies. Many of them have not been updated in a while, but they do provide an idea of what kinds of games can be created with Unity.

Before you can run this game, you will need to make one small change. Select File ➤ Build Settings from the main menu. In the dialog box you will see that the game is currently set to PC, Mac, & Linux Standalone (Figure [10-2](#A310332_2_En_10_Chapter.html#Fig2)). You need to change the target platform to iOS. Select the iOS icon option in the Platform menu and then select the Switch Platform button (Figure [10-3](#A310332_2_En_10_Chapter.html#Fig3)).

![A310332_2_En_10_Fig3_HTML.jpg](Images/A310332_2_En_10_Fig3_HTML.jpg)

Figure 10-3.

The Build setting menu set to iOS

![A310332_2_En_10_Fig2_HTML.jpg](Images/A310332_2_En_10_Fig2_HTML.jpg)

Figure 10-2.

The Build setting menu set to PC, Mac & Linux Standalone

Now you can select build and run this game on an iOS device.

## Test with the Unity Remote

Unity has provided a very cool solution for in-editor testing of Unity iOS projects in the form of the Unity Remote app, which runs on an iOS device and relays touchscreen and accelerometer data from that device over Wi-Fi to the Unity Editor. If you don’t have an iOS device, you can skip this section and still get some testing done using the iOS simulator, described in the next section.

Unity Remote can be found on the App Store in the same way as any app. Enter Unity Remote in the App Store search box (Figure [10-4](#A310332_2_En_10_Chapter.html#Fig4)).

![A310332_2_En_10_Fig4_HTML.jpg](Images/A310332_2_En_10_Fig4_HTML.jpg)

Figure 10-4.

The Unity Remote app on the App Store

Once Unity Remote is installed on your iOS device, make sure the device is connected to your Mac that’s running Unity. Now open the Unity Remote app on your iOS device and then select the GameScene from The Climber ➤ Scenes folder (Figure [10-5](#A310332_2_En_10_Chapter.html#Fig5)).

![A310332_2_En_10_Fig5_HTML.jpg](Images/A310332_2_En_10_Fig5_HTML.jpg)

Figure 10-5.

The Climber GameScene

Now you need to let Unity know that you want to connect to the Unity Remote app on your device. Then click the Play button in Unity. The game will load in the Unity game screen, and when you press Play, the game should also play on your device. From the main menu, select Edit ➤ Project Settings ➤ Editor (Figure [10-6](#A310332_2_En_10_Chapter.html#Fig6)).

![A310332_2_En_10_Fig6_HTML.jpg](Images/A310332_2_En_10_Fig6_HTML.jpg)

Figure 10-6.

Setting the Unity Editor

In the Inspector, select the iOS device that you have installed the Unity Remote App on.

When the Unity Editor is in Play mode, the graphics in the Game view will also appear in Unity Remote, and you can play the game on your device. It will may a bit coarse, because in Unity you have not scaled the game to the device settings. You can improve the performance by minimizing the resolution of the Game view in the Unity Editor (Figure [10-7](#A310332_2_En_10_Chapter.html#Fig7)).

![A310332_2_En_10_Fig7_HTML.jpg](Images/A310332_2_En_10_Fig7_HTML.jpg)

Figure 10-7.

Minimized device resolution in the Game view

Regardless of the graphics quality, you are now able to see what the Climber game looks like on an iOS device, although you’re really still playing in the Editor and just using the device as a controller. Because you have not set up touchscreen input in the Climber game, you will need to use the arrow and space keys on the Mac. Once this is configured, the touchscreen input is sent back to the Unity Editor via the USB cable and is received by the game running in Play mode, so you still have all the debugging features of the Unity Editor at your disposal during the test run. It’s the best of both worlds!

## Install Xcode

At some point you have to leave the comforts of the Unity Editor and really start building for iOS. All iOS apps have to be built through Xcode, Apple’s official iOS development tool (and also its official macOS development tool), which is why a Unity iOS build first generates an Xcode project, which in turn is built by Xcode to create the app. Xcode is available free from the Apple Developer site but conveniently also available on the Mac App Store. Search for xcode on the Mac App Store and click the Free button to download (Figure [10-8](#A310332_2_En_10_Chapter.html#Fig8)).

![A310332_2_En_10_Fig8_HTML.jpg](Images/A310332_2_En_10_Fig8_HTML.jpg)

Figure 10-8.

Xcode on the Mac App Store

After installation, you should see the Xcode application in your Applications folder. Now you’re ready to start performing iOS builds from Unity!

## Customize the Player Settings

Unity iOS builds invariably require customization of the player settings in the Unity Editor (the Unity Player, as opposed the Unity Editor, is the deployed version of the Unity engine). Select Player Settings under the Edit menu, or click the Player Settings button in the Build window, and the player settings will show up in the Inspector view (Figure [10-9](#A310332_2_En_10_Chapter.html#Fig9)).

![A310332_2_En_10_Fig9_HTML.jpg](Images/A310332_2_En_10_Fig9_HTML.jpg)

Figure 10-9.

Selecting the player settings

The player settings enable you to set the settings for PC, Mac & Linux Standalone (the first icon), iOS (the second icon), Android (the third icon), and WebGL (the fourth icon).

Because you’re interested only in the iOS player settings now, click the iOS Settings tab that somewhat resembles an iPhone to see the settings for iOS (Figure [10-10](#A310332_2_En_10_Chapter.html#Fig10)).

![A310332_2_En_10_Fig10_HTML.jpg](Images/A310332_2_En_10_Fig10_HTML.jpg)

Figure 10-10.

Resolution and Presentation in the player settings for iOS

The player settings are plentiful, so they are organized into several sections: Resolution and Presentation, Icon, Splash Image, Debugging and crash reporting, and the catchall Other Settings. Chapter [12](../Text/A310332_2_En_12_Chapter.html) is devoted to presentation issues, including the Icon and Splash Image settings, so for now, you’ll focus on the Resolution and Presentation and Other Settings.

### Resolution and Presentation

Most of the Resolution and Presentation settings have reasonable defaults (Figure [10-8](#A310332_2_En_10_Chapter.html#Fig8)), but I recommend using Auto Rotation for Default Orientation since Apple requires iPad apps to autorotate, meaning the display is rotated automatically to stay upright. The other Default Orientation choices are Landscape Left, Landscape Right, Portrait, or Portrait Upside Down.

If Auto Rotation is selected, then the Allowed Orientations for Auto Rotation are displayed. Climber is intended only to run in Portrait mode, so Climber will flip between Portrait and Portrait UpsideDown as the device is turned around, but never to a Landscape mode.

The Use Animated Autorotation option specifies whether the display visibly rotates or just switches instantly. I always opt for the animated rotation because it looks cool. The status bar options specify whether the iOS status bar is visible when the app launches and, if visible, in what style.

The Show Loading Indicator controls whether the iOS activity indicator, the little spinning graphic at the center of the screen, appears when the app launches. This setting is also covered among presentation options in Chapter [12](../Text/A310332_2_En_12_Chapter.html).

### Other Settings

It so happens that many important settings are collected under the generically titled Other Settings (Figure [10-11](#A310332_2_En_10_Chapter.html#Fig11)). The static and dynamic batching options under the Rendering settings are really optimization options, affecting whether and when meshes are automatically combined for performance reasons. That will be discussed among optimization techniques in Chapter [16](https://doi.org/10.1007/978-1-4842-3174-6_16).

![A310332_2_En_10_Fig11_HTML.jpg](Images/A310332_2_En_10_Fig11_HTML.jpg)

Figure 10-11.

Other settings in the player settings for iOS

There are two settings in the Identification section: Bundle Identifier and Version. They are not important for running in the iOS simulator, but when it comes time for app submission, these settings must match the corresponding information for the app specified in iTunesConnect (covered as part of the app submission process in the next chapter).

The bundle ID is a unique identifier for your app, typically expressed like a URL in reverse. For example, all of my apps have bundle IDs of the form com.technicat.<appname>, so I would change com.unity.climber to com.technicat.climber.

The bundle version is the same app version number displayed for the app on the App Store. It’s usually formatted as two numbers, <major>.<minor>, or sometimes three, <major>.<minor>.<revision>. This number has to be incremented every time you submit an update for your app on the App Store.

Under Configuration, most of the settings have reasonable defaults, but you do need to decide whether your app will run on iPhones, iPads, or both by selecting the target device. Most 3D apps generally run well on a variety of screen aspect ratios, at least better than 2D apps, but iPhones and iPod touches have narrower screens than iPads. Sometimes, for business reasons, you might want to release an app only for the iPad or only for the iPhone.

The SDK version has two options: Device SDK and Simulator SDK. When building for a test device or an app submission, covered in the next chapter, the default value of Device SDK is the correct choice, but in this chapter, Simulator SDK should be selected to run our game in the iOS simulator.

Note

SDK Version is an odd property name to distinguish between simulator and hardware builds, but it is a holdover from older versions of Unity iOS that required specifying the iOS SDK that was installed with Xcode.

The Target iOS Version setting specifies the minimum iOS version the app will run on. Leaving it on the default and minimum value, iOS 7.0, maximizes the number of compatible devices and thus potential customers. However, targeting any iOS too old may result in a Unity warning of a potential App Store rejection.

Scripting Define Symbols allows you to define your own preprocessor definitions, just like the UNITY_EDITOR and UNITY_WEBPLAYER definitions referenced in the FuguPause script in the previous chapter. For example, if you had some code that calls some functions available only in Unity Pro, then you might wrap the code with #if UNITY_PRO … #endif and then add UNITY_PRO to the Scripting Define Symbols field in projects that are running with Unity Pro.

Multiple definitions can be added using semicolons as a separator. If you also had a USE_ADS preprocessor definition to control whether ads are displayed, you might have USE_ADS;UNITY_PRO in the Scripting Define Symbols field.

Some of the Optimization settings have nothing to do with optimization. AOT Compilation Options refers to the ahead-of-time compilation that takes place during a Unity iOS build, as opposed to just-in-time (JIT) compilation . This why `#pragma strict` is required in Unity iOS scripts. It gives the AOT compiler enough information to do its job.

Finally, Stripping Level, Script Call Optimization , and Optimize Mesh Data really are optimization settings, which will be covered among other optimization techniques in Chapter [16](https://doi.org/10.1007/978-1-4842-3174-6_16). But, briefly, Stripping Level is a Unity iOS Pro option that removes unused code (or if you’re unlucky, used code, which is why it’s optional), Script Call Optimization can speed up scripts by eliminating exception handling (exceptions, essentially, are errors or “exceptional” conditions that can be caught by calling code), and Optimize Mesh Data removes unused mesh data (e.g., normals are not necessary when an unlit material is used).

As you can see, there are many options in the player settings that affect the appearance and performance of Unity iOS builds. After changing the player settings, it’s a good idea to save those changes by invoking Save Project or Save Scene (which implicitly performs a Save Project, too) from the File menu. Although many if not most of the player settings will be important at some point, the only change you have to make to get Angry Bots running on the iOS simulator is setting Device SDK to Simulator SDK.

## Test with the iOS Simulator

Now that you have Xcode installed and have Device SDK set to Simulator SDK, you’re ready to build and run Climber on the iOS Simulator. The Build and Run command in the File menu and the Build and Run button in the Build Settings window will perform the Build phase, generating an Xcode project, and then perform the Run phase, invoking a build-and-run within the Xcode project. Normally, that is pretty convenient, but when working with the iOS simulator, you have more control over the iOS simulator when performing the Xcode build-and-run manually from Xcode. So, let’s bring up the Build Settings window again (from the File menu) and click the Build button.

Tip

Before performing a build, check in the Build Settings window that you have the correct set of scenes included.

Unity will bring up a Save As window prompting you for a location and file name of the soon-to-be-built Xcode project (Figure [10-12](#A310332_2_En_10_Chapter.html#Fig12)).

![A310332_2_En_10_Fig12_HTML.jpg](Images/A310332_2_En_10_Fig12_HTML.jpg)

Figure 10-12.

Build prompt for location and name of the Xcode project

It usually defaults to the top level of the Unity project folder. Let’s call the Xcode project Climber_iOSv1.0\. You have added the version number so that you can keep track of any changes as needed.

Caution

Be careful not to choose a build destination inside the Assets folder or Unity will try to import an entire Xcode project as project assets.

If this isn’t the first time you built this project at this location, Unity will detect that an Xcode project with that name already exists and will ask if you want to replace the project completely or append the project changes (Figure [10-13](#A310332_2_En_10_Chapter.html#Fig13)).

![A310332_2_En_10_Fig13_HTML.jpg](Images/A310332_2_En_10_Fig13_HTML.jpg)

Figure 10-13.

Build Append or Replace dialog box

Appending is faster, but sometimes you need a fresh Xcode project. For example, if you change the bundle ID in the player settings, and definitely if you’ve upgraded either Unity or Xcode, you should build a new Xcode project.

Tip

Build and Run from the File menu is not quite the same as the Build and Run button in the Build Settings. The command in the File menu assumes you want to append the build folder, if it already exists, and doesn’t ask if you want to append or replace it. I prefer to stick with performing builds from the Build Settings window, so I always know what’s going on. I’ve had cases where I copied a project and invoking Build and Run from the File menu still built to the original location!

In any case, after clicking Build, a progress bar will display during the build (Figure [10-14](#A310332_2_En_10_Chapter.html#Fig14)). As with asset reimporting, this can take a while for a big project, and the Editor will be unresponsive during the build.

![A310332_2_En_10_Fig14_HTML.jpg](Images/A310332_2_En_10_Fig14_HTML.jpg)

Figure 10-14.

The progress indicator during a build

When the build is complete, an Xcode project will appear in the location you specified (Figure [10-15](#A310332_2_En_10_Chapter.html#Fig15)).

![A310332_2_En_10_Fig15_HTML.jpg](Images/A310332_2_En_10_Fig15_HTML.jpg)

Figure 10-15.

An Xcode project folder generated by a Unity iOS build

The Xcode project is named Unity-iPhone.xcodeproj and is located in the Xcode project folder. Double-click that file to open the project in Xcode (Figure [10-16](#A310332_2_En_10_Chapter.html#Fig16)).

![A310332_2_En_10_Fig16_HTML.jpg](Images/A310332_2_En_10_Fig16_HTML.jpg)

Figure 10-16.

Xcode launched with a Unity iOS project

Notice how iPhone 7 Plus is displayed at the top left of the Xcode window. That is a menu for selecting the Xcode scheme, which is a collection of build rules in Xcode. The other available schemes are the current iPad and iPhone simulators (Figure [10-17](#A310332_2_En_10_Chapter.html#Fig17)).

![A310332_2_En_10_Fig17_HTML.jpg](Images/A310332_2_En_10_Fig17_HTML.jpg)

Figure 10-17.

Selecting the iPad and iPhone simulators

Performing a Build and Run from Unity would have launched the iOS simulator automatically without first giving you a chance to choose between the iPad and iPhone simulators. For now, let’s stay with the iPad simulator and click the Run button at the top left of the Xcode window. The Run button is really a Build and Run button, as it compiles the project if necessary before attempting to run it. If successful, an iOS simulator window matching the screen size of an iPad will appear, running Climber (Figure [10-18](#A310332_2_En_10_Chapter.html#Fig18)).

![A310332_2_En_10_Fig18_HTML.jpg](Images/A310332_2_En_10_Fig18_HTML.jpg)

Figure 10-18.

Climber running in the iOS simulator

The iOS simulator will interpret mouse input as touchscreen events, so you can click the screen to play. In addition, the Hardware menu for the iOS simulator simulates other device events (Figure [10-19](#A310332_2_En_10_Chapter.html#Fig19)).

![A310332_2_En_10_Fig19_HTML.jpg](Images/A310332_2_En_10_Fig19_HTML.jpg)

Figure 10-19.

iOS simulator options

For example, the Rotate Left and Rotate Right commands simulate rotating the device orientation (try it and see the autorotation in action), and invoking Home is the same as pressing the device Home button on a device, which will suspend or exit the app and display the Home screen of the device. The app icon should be there and can be clicked (simulated tap) to restart the app. The Hardware menu also allows you to select different devices and iOS versions to simulate.

Besides testing, the iOS simulator is useful for taking screenshots via the Save Screen Shot command in the Tools menu (Figure [10-20](#A310332_2_En_10_Chapter.html#Fig20)). Invoking that command immediately saves the screenshot to the desktop. The App Store requires screenshots for the devices your app supports, so if you need an iPad screenshot, for example, and you don’t have an iPad, the iOS simulator is a convenient tool for getting that screenshot.

![A310332_2_En_10_Fig20_HTML.jpg](Images/A310332_2_En_10_Fig20_HTML.jpg)

Figure 10-20.

The Save Screen Shot command in the iOS simulator

The iOS simulator window doesn’t have an operable close button, but the simulator can be exited by the Quit iOS Simulator command in the iOS simulator menu or its keyboard shortcut Command+Q (Figure [10-21](#A310332_2_En_10_Chapter.html#Fig21)).

![A310332_2_En_10_Fig21_HTML.jpg](Images/A310332_2_En_10_Fig21_HTML.jpg)

Figure 10-21.

Exiting the iOS simulator

The iOS simulator is fairly complete at simulating iOS features, including iAd and Game Center. So if you don’t have an iOS device available for testing, you can still get through most of the rest of this book just using the iOS simulator.

## Explore Further

You might be experiencing déjà vu because you started with the Climber demo as your first project and now you’ve returned. The difference, of course, is now you’re building the project for iOS. You’ve taken important steps, changing the build target to iOS, testing in the Unity Editor with Unity Remote, and then building with Xcode to run in the iOS simulator. The next step, building to run on a real iOS device, will take place in the next chapter, and then you’ll return to your regular programming, the bowling game. By then, ideally, you’ll be comfortable with the build process and can concentrate on making the game an iOS game!

### Unity Manual

Because the discussion has returned to Climber, it’s fitting that the first relevant section in the Unity Manual is the “Unity Basics” section, specifically the “Publishing Builds” page.

The Unity Manual also includes several sections specifically about Unity iOS, although you may have to click the iPhone icon at the top of the manual to make that content visible. The section “Getting Started with iOS Development” is somewhat out of date as of this writing, but its “Unity Remote” page is still useful to explain how to set up and operate that app.

More recent documentation on Unity iOS is listed in the “Advanced” section of the Unity Manual, including a “Structure of a Unity Xcode Project” page. It’s a good idea to become familiar with the files in the generated Xcode project, but you won’t be messing with the Xcode files generated by Unity, except to enable the built-in profiler when you get to Chapter [16](https://doi.org/10.1007/978-1-4842-3174-6_16).

### Reference Manual

Most of the work in getting Angry Bots to build consisted of filling in appropriate values in the player settings. This chapter provided a cursory overview of the iOS-specific settings, and only the Default Orientation and Device SDK settings are important for running in the iOS simulator, but the “Player Settings” page in the Reference Manual details each of the individual cross-platform and iOS-specific player settings, along with the settings for other platforms.

### iOS Developer Library

Download links and documentation for Xcode are available on the Xcode page at the Apple Developer site ( [`http://developer.apple.com/xcode`](http://developer.apple.com/xcode) ). One of the great things about using Unity for iOS development is that you can avoid needing to know much about Xcode and Objective-C. But still, learning your way around Xcode and understanding Objective-C code can only help.

In addition to the Xcode documentation you can find under its Help menu, there are links to developer documentation from the Xcode web site ( [`http://developer.apple.com/xcode`](http://developer.apple.com/xcode) ). There are many other articles listed under the Xcode topic, and many are also available in the Xcode Help menu, including the Xcode User Guide and Xcode Basics Help.

The iOS Developer Library lists “Xcode” in the topics, and most of the relevant “IDE” section of the Xcode topic can be found in the iOS simulator’s User Guide. The guide is extensive, featuring multiple chapters that expand on the introduction given in this book, explaining how to launch the simulator from Xcode, how to operate the simulator, and how to invoke simulated device input.

Unity does a lot to shield you from working in Xcode, and in this chapter, you literally just had to click the Run button in Xcode to run the iOS simulator. So, it’s a good idea to read up on the tool in preparation for the next chapter, where you’ll learn how to build and run your app for testing and build it for submission to the App Store.

# 11. Building for Real: Device Testing and App Submission

Running an iOS version of the Climber game turned out to be amazingly easy. It was just a matter of switching the build target to iOS and then immediately playing the game in the Editor using the Unity Remote app as the input device (or, when running the game in the iOS simulator, performing a build with the target SDK set to Simulator SDK in the player settings).

That might seem too easy to be the end of it, and it is. The next step is the painful part: building the Unity project so that it installs and runs on real iOS hardware. The Unity part of this procedure is easy; it’s nearly identical to building a project to run on the iOS simulator. But installing an app on a test device requires that the app be built with a mobile provisioning profile that is also installed in Xcode and on the test device. Before that, the profile has to be created in Apple’s Provisioning Portal using a development certificate associated with the test device and the app’s bundle ID. So, before that, the test device and bundle ID have to be registered on the Provisioning Portal, and the development certificate has to be generated after creating a certificate request.

That’s just for testing. A similar process is required when building apps for submission to the App Store, but with a separate distribution provisioning profile and corresponding certificate. Furthermore, submitting an app to the App Store also requires adding the app to iTunes Connect, a separate web site for managing apps on the App Store.

If dealing with provisioning profiles sounds like a hassle, you may be tempted to skip this chapter and return later. In fact, the iOS adaptation of the bowling game in the next couple of chapters can be tested with the Unity Remote (for the input code in Chapter [13](../Text/A310332_2_En_13_Chapter.html)) and iOS simulator (the icon, splash screen, and screen size set in Chapter [12](../Text/A310332_2_En_12_Chapter.html)), as you did with the Climber game in the previous chapter. Plus, access to the Provisioning Portal and iTunes Connect (and the associated nonpublic documentation) requires membership in the iOS Developer Program, so if you haven’t acquired that yet, then you’ll have to wait anyway.

But you will need an iOS Developer Program membership to set up Game Center leaderboards and achievements (which I will cover in Chapter [14](../Text/A310332_2_En_14_Chapter.html)) and an iAd banner and interstitial ads (which I will cover in Chapter [15](../Text/A310332_2_En_15_Chapter.html)); further, the performance measurement (which I cover in Chapter [16](https://doi.org/10.1007/978-1-4842-3174-6_16)) won’t be very interesting without it being able to run on real hardware. So, at the least, it’s best to get the iOS Developer Program registration process started as soon as possible!

## Register as an iOS Developer

As you might expect, the Apple developer web site ( [`http://developer.apple.com/`](http://developer.apple.com/) ) is the place to find most of the official Apple developer resources (iTunes Connect is an exception), including all of the Apple developer programs—the Mac Developer Program, the Safari Developer Program, and the one you’re interested in, the iOS Developer Program. You can register in the iOS Developer Program as an individual or a company. Registering as an individual is fairly straightforward, as long as you have a credit card and are prepared to pay the $99 yearly fee. Signing up as a company requires the same, but it also requires an actual legal company entity and supporting information about the company, including a Data Universal Numbering System (DUNS) number, which is a unique identifier assigned to a company by Dun & Bradstreet. Both individual and company accounts have to be approved by Apple, but approval for company accounts can take longer than individual accounts (the approval for my company, Technicat, LLC, took three months).

The only additional feature for company accounts is the ability for the account administrator to add team members, so in this book I’ll assume you’re working with an individual account or, equivalently, as the administrator of a company account.

Once you’ve been approved as an iOS developer, you’ll have login access to the Apple developer site and also iTunes Connect ( [`http://itunesconnect.apple.com/`](http://itunesconnect.apple.com/) ) using your Apple ID. ITunes Connect is the site where you manage all your apps that are on or being prepared for the App Store. But before working in iTunes Connect, you’ll be logging in to the Apple developer site and working in the Provisioning Portal to create development and distribution provisioning profiles, which are required for test and distribution builds, respectively.

## Use the Provisioning Portal

Assuming you have an iOS Developer Program membership (if not, you can skip this chapter and return later), log in to [`http://developer.apple.com/`](http://developer.apple.com/) and enter the Provisioning Portal .

Note

The Apple developer site has several areas that you can log in to—the Member Center, iOS Dev Center, and Developer Forums—and you can reach the Provisioning Portal from either the Member Center or the iOS Dev Center (at this time, the organization is a bit confusing).

Detailed documentation is available on the site once you log in (the Provisioning Portal is one area where Apple doesn’t have much in the way of public screen-to-screen documentation ), but I’ll briefly outline the steps that are involved and the information you need to provide.

### Register Test Devices

A development provisioning profile is valid only for a specified set of test devices , so the first thing to do is register in the Provisioning Portal the iOS devices you want to use for testing. You will need to supply the unique device identifier (UDID) for each device. The UDID of a device can be found with iTunes or Xcode. In iTunes, when an iOS device is connected to your Mac, the UDID can be found by clicking the serial number of the device, which switches the serial number display to a UDID display. But that display cannot be copied and pasted, so a better option is to obtain the number from Xcode.

When an iOS device is connected to your Mac, the UDID will show up in the Xcode Devices window. To bring up the Devices window in Xcode, launch Xcode if it’s not running already and then select Devices from the Window menu (Figure [11-1](#A310332_2_En_11_Chapter.html#Fig1)).

![A310332_2_En_11_Fig1_HTML.jpg](Images/A310332_2_En_11_Fig1_HTML.jpg)

Figure 11-1.

Selecting Devices from the Xcode Window menu

Once the Organizer window is up, all connected iOS devices should be listed in the left panel. Select the device you want to register for testing, and the device information, including the UDID (listed as the identifier), will be displayed (Figure [11-2](#A310332_2_En_11_Chapter.html#Fig2)).

![A310332_2_En_11_Fig2_HTML.jpg](Images/A310332_2_En_11_Fig2_HTML.jpg)

Figure 11-2.

Obtaining a device ID from the Xcode Organizer

You can then copy and paste the UDID into the Provisioning Portal to register your test device .

### Register App Identifiers

Once you’ve registered your test devices , you need to register app identifiers because each provisioning profile is valid for only a single app ID. An app ID corresponds to a bundle ID (the same one specified in the Unity player settings for your app) or matches many bundle IDs when it ends with an asterisk (then it’s called a wildcard app ID). For example, I registered the app ID com.technicat.* that I can use in a single provisioning profile for all my apps, such as com.technicat.fugubowl and com.technicat.hyperbowl.

Tip

Create a wildcard app ID that matches all or most of your bundle IDs so you don’t have to create a new app ID for every bundle ID.

### Use Development Provisioning Profiles

To create a development provisioning profile , you need to generate a development certificate that is associated with the profile. The has instructions on how to generate and upload a certificate request, which will in turn result in a new development certificate. Then you can create a development provisioning profile associated with that certificate.

Actually, once you have generated a development certificate, the Provisioning Portal will automatically generate a Team provisioning profile for you that uses the asterisk (*) as an app ID and thus matches any of your bundle IDs, which is a real convenience. But you can also generate any number of other development provisioning profiles with the same certificate and the app ID you specify.

### Use Distribution Provisioning Profiles

Creating a distribution provisioning profile is similar to creating a development provisioning profile. You’ll need to create a distribution certificate and then a distribution provisioning profile associated with that certificate .

Tip

You can save a little time by reusing the same certificate request file to create both the development and distribution certificates.

## Use the Xcode Organizer

When your development provisioning profiles are available, you can download them into Xcode . Bring up the Xcode Organizer window (from the Xcode Window menu), select Provisioning Profiles in the sidebar, and click the Refresh button on the lower right of the profiles list. After prompting you for your Apple developer login, the Organizer will retrieve and display all the provisioning profiles you’ve created in the Provisioning Portal.

Figure [11-3](#A310332_2_En_11_Chapter.html#Fig3) shows all four of my provisioning profiles. Notice all the app IDs are prefixed with a number. That’s generated by the Provisioning Portal and called the bundle seed ID. For our purposes here, you can ignore it.

![A310332_2_En_11_Fig3_HTML.jpg](Images/A310332_2_En_11_Fig3_HTML.jpg)

Figure 11-3.

Provisioning profiles listed in the Xcode Organizer

The first provisioning profile , named La Petite Baguette Distribution , is a distribution provisioning profile that only matches an app called La Petite Baguette (a restaurant app). Notice that the app ID is fully specified, exactly matching the bundle ID for the La Petite Baguette app. Notice also that the provisioning profile has expired.

The second profile , iOS Team Provisioning Profile , is a development provisioning profile automatically created by the Provisioning Portal. It matches any app’s bundle ID.

The third profile , named techdev , is a development provisioning profile I created that will work with any of my apps, as it will match any bundle ID starting with com.technicat.

The fourth profile is my sole distribution provisioning profile . It also matches any app that has a bundle ID starting with com.technicat.

Notice the expiration date. Certificates, and thus their associated provisioning profiles, expire after a year, at which point you’ll have to replace the expired profile with a new profile created with a new certificate.

You can add a provisioning profile (Figure [11-4](#A310332_2_En_11_Chapter.html#Fig4)) within the Xcode Organizer by clicking the + button on the lower left.

![A310332_2_En_11_Fig4_HTML.jpg](Images/A310332_2_En_11_Fig4_HTML.jpg)

Figure 11-4.

Adding a provisioning profile

## Build and Run

Now you’re ready to start testing your device . The process is the same as building and running for the iOS simulator, except first you will need to go to the Unity player settings and set Target SDK to Device SDK instead of Simulator SDK (Figure [11-5](#A310332_2_En_11_Chapter.html#Fig5)).

![A310332_2_En_11_Fig5_HTML.jpg](Images/A310332_2_En_11_Fig5_HTML.jpg)

Figure 11-5.

The player settings for a device build

Before starting a build, because you’re building for real hardware, you should make sure the Target iOS Version setting is compatible with your test device (or vice versa), meaning the Target iOS Version setting should be at least as old as whatever version of iOS your test device is running. But really, it’s a deployment decision, too. Supporting older versions of iOS increases the potential number of customers but may increase your support burden and prevent the use of features available only in later versions of iOS. It turns out there is data on iOS hardware and versions currently running Unity-based apps, collected by Unity, at [`http://stats.unity3d.com/`](http://stats.unity3d.com/) . Any Unity app you create will contribute to this data, as long as the Submit HW Statistics option is selected in the player settings. You can perform a Build and Run operation, which is equivalent to performing a build, manually opening the generated Xcode project by double-clicking the project file (Figure [11-6](#A310332_2_En_11_Chapter.html#Fig6)), and then clicking the Run button in Xcode.

![A310332_2_En_11_Fig6_HTML.jpg](Images/A310332_2_En_11_Fig6_HTML.jpg)

Figure 11-6.

An Xcode project generated by a Unity iOS build

Either way, instead of building and running in the iOS simulator, Xcode will build, install, and run on the connected test device.

Tip

It’s best to disable screen lock completely while using a device for testing. Initiating a run from Xcode will fail if the connected device has the screen locked.

As with the iOS simulator, any Unity debug text will show up in the Xcode debug area (Figure [11-7](#A310332_2_En_11_Chapter.html#Fig7)) instead of the Console view as in the Unity Editor.

![A310332_2_En_11_Fig7_HTML.jpg](Images/A310332_2_En_11_Fig7_HTML.jpg)

Figure 11-7.

Xcode running an app on a test device Note

The Xcode equivalent of a view in the Unity Editor is called an area. Unlike views, Xcode areas are spelled in lowercase (e.g., the navigation area, the editor area, and the debug area).

You can also make use of the MonoDevelop debugger, although there’s more setup involved to debug an iOS app than a game running in the Unity Editor. To enable the app for remote debugging with MonoDevelop, select the Development Build option in the Build Settings window (Figure [11-8](#A310332_2_En_11_Chapter.html#Fig8)). After the Development Build option is enabled, the Script Debugging option becomes available, and you should select that, too.

![A310332_2_En_11_Fig8_HTML.jpg](Images/A310332_2_En_11_Fig8_HTML.jpg)

Figure 11-8.

Creating a development build with script debugging enabled

Remote debugging has problems interacting with Xcode, including the inability to resume after breakpoints, so first perform a Build and Run operation to get the app installed on the test device. Click Stop in Xcode to halt the test run and then manually restart the app on the device (i.e., tap the app’s icon just like you would to start the app normally). Finally, in MonoDevelop, perform an Attach to Process operation, only instead of attaching to the Unity Editor, attach it to the test device (Figure [11-9](#A310332_2_En_11_Chapter.html#Fig9)).

![A310332_2_En_11_Fig9_HTML.jpg](Images/A310332_2_En_11_Fig9_HTML.jpg)

Figure 11-9.

Attaching the MonoDevelop debugger to the iOS player

After you’re satisfied with the device testing, it’s time to submit your app to the App Store. This doesn’t require any change within Unity; you only need to rebuild the app within Xcode using a distribution provisioning profile instead of a development provisioning profile. But first, you need to create an App Store entry for the app in iTunes Connect. And before you create the iTunes Connect entry, you should have some graphics ready for the App Store.

## Prepare App Store Graphics

You’ll need two kinds of graphics for the App Store: an app icon and a set of screenshots for all the devices that you’re targeting. iTunes Connect has a tendency to log out after a period of idleness and lose all changes, so you don’t want to be messing around with creating an icon and taking screenshots while logged in there.

### Create an Icon

iTunes Connect will ask for a 1024 × 1024 version of your app icon, either in `.png` or `.jpeg` format, that will accompany the App Store description of your app. If you planned ahead when creating the app icon, you’re all set. Otherwise, you’ll have to create it now before proceeding to iTunes Connect.

Ideally, you’d have a professional icon designer create the icon, but if you’re low on both funds and art skills, you can do what I do in a pinch—grab a large screenshot from the Unity Editor game view and scale it to 1024 × 1024\. It’s better to scale from large to small. Scaling up a smaller existing icon usually doesn’t create good results and is discouraged by Apple, although it probably happens a lot because until fairly recently apps were submitted with a 512 × 512 icon uploaded.

Caution

Apple may reject an app if the App Store icon does not match any of app’s icons that are displayed on a device. So if you create an entirely new 1024 × 1024 for the App Store, you should set that as the default icon in the Unity player settings.

### Take Screenshots

iTunes Connect will also ask you for screenshots in three sizes: matching the iPhone 4 (640 × 960 or 960 × 640), iPhone 5 (640 × 1136 or 1136 × 640), and iPad (768 × 1024 or 1024 × 768 or double that for the new iPad). That’s assuming you selected iPhone + iPad as the supported hardware in the Unity player settings. If you selected only iPhone, then you don’t need to supply the iPad screenshot, and if you selected only iPad, then you only need to supply the iPad screenshot.

There are three fairly convenient ways to take screenshots: using the iOS simulator, from a device using the Xcode Organizer window, and on the device itself by pressing both the Home and Power buttons at the same time.

Taking screenshots with the iOS simulator is easy. Use the Screenshot command in the Edit menu, and the resulting screenshot is saved on the desktop. It may be inconvenient to use the iOS simulator if your game does not play well with the limited iOS simulator input. But if you don’t have an iPhone or an iPad, this is one way to get screenshots.

Taking Xcode screenshots is almost as simple. With a device attached to your Mac, select the Screenshots panel of the device as it appears in the Xcode Organizer window.

Then click the New Screenshot button on the lower right when you see a screen you like, as you play your game on the test device. The screenshot will appear on the desktop for easy access.

My favorite way to take a screenshot is directly on the device by pressing the Home and Power buttons simultaneously. The resulting screenshot is saved to Photos on the device, which can later be synced to iPhoto on the Mac and accessible in the Finder from there. This method allows me to take screenshots at my leisure, and anywhere I want, like on the sofa, instead of being tethered to a Mac. And it’s easier to quickly take a screenshot at a propitious moment than trying to grab a mouse and quickly click the New Screenshot button in the Xcode Organizer.

## Add an App on iTunes Connect

Now that you’ve got your icon and screenshots ready, you can start adding your app information to iTunes Connect . Log in to [`http://itunesconnect.apple.com/`](http://itunesconnect.apple.com/) and you’ll see the main menu, as shown in Figure [11-10](#A310332_2_En_11_Chapter.html#Fig10).

![A310332_2_En_11_Fig10_HTML.jpg](Images/A310332_2_En_11_Fig10_HTML.jpg)

Figure 11-10.

The main menu in iTunes Connect

Click Manage Your Applications to get started. Now you’ll see a list of apps that you’ve already added and an Add New App button. Figure [11-11](#A310332_2_En_11_Chapter.html#Fig11) shows this page in my iTunes Connect account, displaying some of my apps, each with a version number and a dot that is color coded according to the app’s status .

![A310332_2_En_11_Fig11_HTML.jpg](Images/A310332_2_En_11_Fig11_HTML.jpg)

Figure 11-11.

Add or manage apps in iTunes Connect

A green dot means the app is live on the App Store, and red means it has been rejected. Yellow has all kinds of meanings, but often means the app has not been uploaded yet. An app that is already live and has an update submitted or in progress will list the status of both. If this is your first app, of course, no apps will be listed.

### Select an App Type

When you click Add New App, the next page (Figure [11-12](#A310332_2_En_11_Chapter.html#Fig12)) asks you to select an app type, either an iOS App for the App Store or a macOS app for the Mac App Store .

![A310332_2_En_11_Fig12_HTML.jpg](Images/A310332_2_En_11_Fig12_HTML.jpg)

Figure 11-12.

Selecting the new app type in iTunes Connect

Clicking New App will bring up a series of web pages, each with a form to fill out, and you can advance to the next page or form by clicking the Continue button.

### Enter App Information

After selecting iOS as the app type, you’ll be presented with a page for entering the app name and bundle ID . These should match the product name and bundle ID specified in the player settings of the Unity Editor. The list of bundle IDs is taken from the list of app IDs you added to the provisioning portal. If you added a wildcard app ID, you can select that and then the suffix of the specific bundle ID for the app you’re adding.

For example, in Figure [11-13](#A310332_2_En_11_Chapter.html#Fig13), I have the info for your modified Angry Bots project, renamed Fugu Bots. The wildcard ID com.technicat.* and suffix FuguBot matches the bundle ID com.technicat.FuguBot I have in the Unity player settings.

![A310332_2_En_11_Fig13_HTML.jpg](Images/A310332_2_En_11_Fig13_HTML.jpg)

Figure 11-13.

Specifying the app name and bundle ID on iTunes Connect

The default language indicates the primary language you’ll be using for all the assets and descriptions you’re submitting with this app.

The SKU Number setting can be anything you want, but it has to be unique among your apps and should be readily identifiable with your app if you see it in reports.

### Set Availability and Price

Upon continuing from the App Information page, iTunes Connect will ask you to specify the price of the app and when you would like the app to become available on the App Store (Figure [11-14](#A310332_2_En_11_Chapter.html#Fig14)).

![A310332_2_En_11_Fig14_HTML.jpg](Images/A310332_2_En_11_Fig14_HTML.jpg)

Figure 11-14.

Setting the app availability and price on iTunes Connect

The availability is contingent on when Apple approves the app, so if you specify an availability date one week from the day the app is submitted and the approval takes two weeks, then it will be made available on the approval date. If you want the app released as soon as possible, just use the default date, which is the day you’re entering this information.

The prices range from free to $.99 to $1.99 to $2.99 and so on. There’s no need to worry too much about charging the right price; it can be changed anytime later.

### Set Language and Category

The first section is the language and category information (Figure [11-15](#A310332_2_En_11_Chapter.html#Fig15)).

![A310332_2_En_11_Fig15_HTML.jpg](Images/A310332_2_En_11_Fig15_HTML.jpg)

Figure 11-15.

The app version, category, and copyright information on iTunes Connect

Select the appropriate category and subcategories. You might as well also select a secondary category to increase the exposure of your app.

### Prepare for Submission

The Prepare for Submission section is the most important for marketing (Figure [11-16](#A310332_2_En_11_Chapter.html#Fig16)).

![A310332_2_En_11_Fig16_HTML.jpg](Images/A310332_2_En_11_Fig16_HTML.jpg)

Figure 11-16.

The product description screen for the app in iTunes Connect

This section includes the description of your app as it will appear on the App Store and keywords that help your app show up in searches on the App Store. You also need to include a support URL, and it is advantageous to link to the Marketing URL.

### Upload the Icon and Screenshots

This is where you upload the icon and screenshots you prepared earlier (Figure [11-17](#A310332_2_En_11_Chapter.html#Fig17)). The icons fit Apple’s requirements to resemble the app icons that are built into the app or the app may get rejected by Apple .

![A310332_2_En_11_Fig17_HTML.jpg](Images/A310332_2_En_11_Fig17_HTML.jpg)

Figure 11-17.

Uploading the app icon and screenshots in iTunes Connect

iTunes Connect will not allow you to proceed without uploading the icon and at least one screenshot, although you really need to upload a screenshot for each screen type (aspect ratio) that the app will run with. If a Unity iOS app is built for the iPhone and iPad, then you need to upload screenshots for two categories, basically corresponding to the iPhone and iPad.

Caution

iTunes Connect might time out pretty quickly. If it times out before you complete this page, the entire app description disappears, and you’ll have to start over. That’s why we prepared the icon and screenshots beforehand.

### General App Information

Complete the General App Information. In this section, input the copyright information and fill in the rating information (Figure [11-18](#A310332_2_En_11_Chapter.html#Fig18)) and the contact information for the developer (Figure [11-19](#A310332_2_En_11_Chapter.html#Fig19)).

![A310332_2_En_11_Fig19_HTML.jpg](Images/A310332_2_En_11_Fig19_HTML.jpg)

Figure 11-19.

Completing the General App Information section

![A310332_2_En_11_Fig18_HTML.jpg](Images/A310332_2_En_11_Fig18_HTML.jpg)

Figure 11-18.

Setting the app content rating in iTunes Connect

The Rating section is pretty straightforward (Figure [11-18](#A310332_2_En_11_Chapter.html#Fig18)). Fill it out; it’s all mandatory. In the additional information section, there is a link to view in the App Store. This information can be updated after you have submitted the game for review. You will come back to the later. You still need to add more information about your game before you submit to Apple for review.

If the ratings don’t match the content, at least in Apple’s eyes, the app may be rejected. For example, I tried submitting a version of Angry Bots and had the app rejected because the game has shooting, albeit against irate automata, and according to Apple, that qualifies as realistic violence that should be marked as Frequent/Intense.

#### Uploading the Build to the iTunes Connect

There are two ways to upload the build to iTunes Connect. The build can be uploaded directly from Xcode or Application Loader. You will use Xcode here. In Xcode, with the build ready, select Generic iOS Device as the target (Figure [11-20](#A310332_2_En_11_Chapter.html#Fig20)).

![A310332_2_En_11_Fig20_HTML.jpg](Images/A310332_2_En_11_Fig20_HTML.jpg)

Figure 11-20.

Setting the Generic iOS Device setting

Now you set the product as an archive. To do this, select Product ➤ Archive from the main menu bar (Figure [11-21](#A310332_2_En_11_Chapter.html#Fig21)).

![A310332_2_En_11_Fig21_HTML.jpg](Images/A310332_2_En_11_Fig21_HTML.jpg)

Figure 11-21.

Selecting Archive from the Product menu

When Xcode has finished compiling the archive, you will see the Archives screen, which includes a button for uploading to the App Store (Figure [11-22](#A310332_2_En_11_Chapter.html#Fig22)). After clicking this button, Xcode will attempt to upload the build to your iTunes Connect account. If you have set the identifier settings correctly and are correctly signed into your iTunes Connect account in Xcode, this file will be successfully uploaded.

![A310332_2_En_11_Fig22_HTML.jpg](Images/A310332_2_En_11_Fig22_HTML.jpg)

Figure 11-22.

Uploading the build

### Submit for Review

After filling out the required and any optional information , you are now ready to submit for review (Figure [11-23](#A310332_2_En_11_Chapter.html#Fig23)).

![A310332_2_En_11_Fig23_HTML.jpg](Images/A310332_2_En_11_Fig23_HTML.jpg)

Figure 11-23.

The app in Prepare for Submission status

After you click Submit for Review, you may need to complete some questions about export compliance and, if needed, upload encryption authorization documents.

![A310332_2_En_11_Fig24_HTML.jpg](Images/A310332_2_En_11_Fig24_HTML.jpg)

Figure 11-24.

An archived distribution build in the Xcode Organizer window

After you have submitted the app (Figure [11-24](#A310332_2_En_11_Chapter.html#Fig24)), it’s a matter of waiting for Apple to approve or reject it.

Tip

It’s a good idea to log back into iTunes Connect after your submission and verify the app status is Waiting for Review. Even if the app binary is valid, the review may be held up because of a missing screenshot, for example.

You’ll eventually receive an e-mail notifying you that the app is available on the App Store, that it’s been rejected, or that Apple needs more time to review it. This response usually arrives within a couple of weeks after submission, but I’ve had apps accepted within 24 hours or rejected after two months. When the review period is extended, Apple will typically send an e-mail stating it needs more time to review your app.

### Be Prepared for Rejection

If the app is rejected, then the e-mail will tell you to return to your app in iTunes Connect and click the Resolution Center button to view and optionally respond to the rejection reasons, which may include technical issues, crash bugs, or violations of Apple’s App Store review guidelines (which you can read on the Apple Developer site after becoming a registered iOS developer).

If you elect to revise your app, you will have to click Reject Binary and Ready for Upload again before uploading. The Resolution Center also allows you to file an appeal to the App Review Board if you feel the rejection is unreasonable.

If accepted, the app will appear shortly on the App Store if you selected the automatic release option in iTunes Connect. Otherwise, the app is placed in Pending Developer Release status until you log in to iTunes Connect, click View Details on the app, and click the Release this Version button.

### Update the App

After an app has been approved, you can always return to the iTunes Connect page for the app and click View Details to edit the app description, and the modified text will show up on the App Store within a few hours. The iTunes Connect page will also display an Add New Version button. Clicking this will lead you through a condensed version of the original submission process to submit an update of the app. You can change nearly all the details except the bundle ID, but the only required change is an increment of the bundle version .

Tip

When updating apps, remember to increment the bundle version both in iTunes Connect and in the Unity player settings.

### Track Sales

After the first version of an app appears on the App Store, you can track its downloads and revenue (if it’s a paid app) by logging on to iTunes Connect and clicking the Payments and Financial Reports link . The resulting page displays a time-based graph of sales, a breakdown of sales per country, and the option to generate reports (Figure [11-25](#A310332_2_En_11_Chapter.html#Fig25)).

![A310332_2_En_11_Fig25_HTML.jpg](Images/A310332_2_En_11_Fig25_HTML.jpg)

Figure 11-25.

The Payment and Financial Reports page in iTunes Connect

### Issue Promo Codes

For each version of an app, Apple provides 100 promo codes that can be used to download the app free on the App Store (the promo codes are available even for free apps, although there’s not much point in using them in that case). To obtain the promo codes for an app, click View Details for that version of the app in iTunes Connect and then click the Promo Codes button on the top right (Figure [11-26](#A310332_2_En_11_Chapter.html#Fig26)).

![A310332_2_En_11_Fig26_HTML.jpg](Images/A310332_2_En_11_Fig26_HTML.jpg)

Figure 11-26.

Clicking View Details on a live app in iTunes Connect

```
XY9LJRKMN7JM
TANFXAHR9EM9
XP6TMAFFHFLT
TXJ44ELTHETL
F9W3WEK7JL4Y
Listing 11-1.
A Promo Code File with Five Promo Codes
```

Once downloaded, the codes are valid for 28 days, even if the app is updated in the meantime.

Tip

Since the expiration date of the codes is based on their download dates, you can conserve the codes by downloading them from iTunes Connect only as needed, instead of downloading all 50 at the same time.

The codes no longer allow users to write reviews for the app on the App Store, but the codes are ideal for sending to sites that host their own reviews.

## Explore Further

Testing Unity iOS apps in the Unity Editor and in the iOS simulator is convenient, but there’s no substitute for the real thing! The good news is that building for real hardware doesn’t change much on the Unity side (really, just setting the SDK to Device SDK in the player settings). The bad news is that you have to go through the entire Apple developer registration, go through the Provisioning Portal setup for your test devices and provisioning profiles, and go through the iTunes Connect setup for all your apps. In a sense, this is the chapter where you became an iOS developer!

Now that you have that out of the way, you can leave the Angry Bots project again and return to your bowling game. You’ll spend the remainder of this book adapting the game for iOS. However, you haven’t seen the last of iTunes Connect. You will use iTunes Connect later when you incorporate Game Center (Chapter [14](../Text/A310332_2_En_14_Chapter.html)) and iAd (Chapter [15](../Text/A310332_2_En_15_Chapter.html)).

### Unity Manual

Since the simulator and device builds for Unity iOS involve nearly identical steps, the Unity Manual sections mentioned in the previous chapter also apply to this chapter. In addition, the “Getting Started in iOS Development” section of the Unity Manual has a page that briefly lists the steps required by Apple for submitting an app to the App Store, and the “Debugging” page in the “Advanced” section explains how to attach a MonoDevelop debugging session to a Unity iOS app running on a device.

### Apple Developer Site

The road to all Apple developer information begins at the Apple developer site ( [`http://developer.apple.com/`](http://developer.apple.com/) ), so that should be your first stop. From there, you can find descriptions of the iOS Developer Program (and other Apple developer programs) and commence the registration process. Even before the registration is complete, you can explore the iOS Dev Center, which includes the iOS Developer Library, which in turn contains the iTunes Connect Developer Guide (listed under Languages and Utilities).

Tip

The documentation in the iOS Developer Library is also available in the Xcode Organizer. As you might gather from all the work that takes place in the Organizer (provisioning profiles, screenshots, app submissions), you might be spending most of your Xcode time there!

Once your registration is approved, you can access the iTunes Connect site and the Provisioning Portal on the Apple developer site to complete the mobile provisioning and app submission steps covered in this chapter.

# 12. Presentation: Screens and Icons

Now that your Climber break is over and the basics of building and running a Unity iOS project have been covered, it’s time to resume your work on the bowling game and get it working on iOS!

From this point on, I will generally assume you’re testing by building and running on a device, but in many cases you can test in the Editor with the Unity Remote or test in the iOS simulator.

Tip

It’s a good idea to always do some testing in the Unity Editor to catch errors that might not show up when testing on a device. And if you run into a weird problem on a device, go back to the Editor and see whether you can debug it there.

This chapter focuses on making the game look right on an iOS device. This includes making sure the game shows up in the correct orientation and all the game elements show up at an appropriate resolution on the screen, which is a precursor to making the game playable (it’s hard to start playing when you can’t read or tap the Play button).

Furthermore, besides providing required attributes such as the app name and icon, you’ll add some polish by including a splash screen and the familiar iOS activity indicator to indicate the game is loading.

Some of these presentation details might seem to be cosmetic and easy enough to implement that they could be deferred until the end of a project (and typically they are), but easy work could and should be done early. Tasks that can be accomplished quickly help you get into a development groove. And when you’re working crunch-time hours trying to finish your game, the last thing you want to see is unfinished work you could have gotten out of the way early.

Besides, packaging is important. In fact, when I’m creating desktop software, I like to start with the installer because that is the first customer experience. For iOS apps, the customer first sees the app icon and name and then the splash screen while launching the app. This will form the customer’s first impression of whether the app is polished or amateurish. And you’re the first customer of your app. You’ll feel better about your project and have a clearer idea of what you want your app to be if it’s packaged like a finished product from the beginning. And you never know when you’ll need to demo it!

The corresponding Unity project is available online at [`www.apress.com/9781484231739`](http://www.apress.com/9781484231739) , and, as usual, I recommend typing in the scripts rather than copying them from the online project. But there are some textures in the project files that will be used for the icon and splash screen, so, from the online project for Chapter [12](../Text/A310332_2_En_12_Chapter.html), copy the additional files in the Textures folder to the Textures folder of your bowling project (remember, you can drag the files into the Project view). You should have three Fugu Games splash textures of different resolutions, one splash screen displaying a book cover (of this book), and an icon texture (Figure [12-1](#A310332_2_En_12_Chapter.html#Fig1)).

![A310332_2_En_12_Fig1_HTML.jpg](Images/A310332_2_En_12_Fig1_HTML.jpg)

Figure 12-1.

Textures for this chapter copied from [`www.apress.com/9781484231739`](http://www.apress.com/9781484231739)

## Bowling for iOS

In the Unity Editor, bring up the Project Wizard (Open Project in the File menu) and select the bowling project as you last left it in Chapter [9](../Text/A310332_2_En_9_Chapter.html), before you switched to Climber. You should now be familiar with the process of switching build targets.

Bring up the Build Settings window (select Build Settings in the File menu), select iOS as the build target, and click the Switch Platform button.

If you have the Necromancer GUI installed from Chapter [9](../Text/A310332_2_En_9_Chapter.html), some script errors will appear in the Console view after switching the build target to iOS, because a test script in that package wasn’t written with `#pragma strict` and is missing some type declarations. This can be resolved quickly by just removing `GUITestScript.js` from the Project view or wrapping `#if !UNITY_IPHONE` around all its contents (bonus points for going in and fixing the code by appending :int to the function parameters). Removing the script will disable the Necromancer GUI test scene, but otherwise the GUISkin is still usable in your pause menu. All of the changes introduced by this chapter will work with or without the Necromancer GUI (but the screenshots show the Necromancer GUI because it’s prettier).

Once the asset reimport is complete, go to the Edit menu, and under Settings, select Player to bring up the player settings in the Inspector view. In the Cross-Platform Settings area, fill in the Company Name and Product Name (the app name) fields. Select the iOS tab to display the iOS player settings and fill in the bundle ID of the app (Figure [12-2](#A310332_2_En_12_Chapter.html#Fig2)).

![A310332_2_En_12_Fig2_HTML.jpg](Images/A310332_2_En_12_Fig2_HTML.jpg)

Figure 12-2.

FuguBowl player settings

Both the original and iOS versions of HyperBowl are portrait-mode games (and it turns out that portrait mode is best for the swipe-to-roll control that will be implemented in the next chapter), so let’s restrict this game to portrait.

Therefore, set the Default Orientation property in the player settings to Portrait (Figure [12-3](#A310332_2_En_12_Chapter.html#Fig3)).

![A310332_2_En_12_Fig3_HTML.jpg](Images/A310332_2_En_12_Fig3_HTML.jpg)

Figure 12-3.

Portrait orientation in the player settings

## Scale the GUI

You now have enough set up to build and run, and the initial screen of your game looks fine if you run it on a low-resolution iOS device that has a 320 × 480 pixel screen. But on any of the newer devices, all of which have at least double the screen resolution, the pause menu looks really small, to the point where it’s difficult to read or even tap an individual button.

To scale the GUI, you first need to figure out the scaling factor you want to use, which is the current screen width divided by the screen width you were originally targeting. The GUI looks alright on the iPhone 3GS, at 320 × 480 pixels, so let’s say the base screen width you’re targeting is 320\. So, if you’re actually running on an iPhone 5, for example, at 1136 × 640 pixels, you’ll have to scale up the GUI.

The next step is to apply the scaling factor. This can be done in UnityGUI by setting the static variable GUI.matrix, which is a transformation matrix . In computer graphics, a transformation matrix encompasses and applies a translation (change of position), rotation, and scale.

In fact, the local position, rotation, and scale of a Transform component corresponds to a 4 × 4 transformation matrix for that GameObject. And the world position, rotation, and scale are calculated by combining (multiplying) all the transformation matrices of a GameObject’s parent GameObjects. In the good ol’ days, you had to include this linear algebra code when programming in computer graphics. Now you can let the game engine hide this from you, but you’re getting a little taste of it here.

### Scale the Scoreboard

Let’s start with the scoreboard , because that’s a little bit simpler, by making the modifications shown in Listing [12-1](#A310332_2_En_12_Chapter.html#Par21) to the FuguBowlScoreboard script. The addition is a variable named baseScreenWidth that specifies the base screen width, defaulting to the 320-pixel width of the iPhone 3GS. The variable is public, so you have the option of adjusting it in the Inspector view.

Then, at the beginning of the OnGUI function, add two lines of code to construct the scaling matrix and assign it to GUI.matrix. (Putting the matrix at the beginning of OnGUI means it takes effect before any GUI rendering takes place.)

```
#pragma strict
var style:GUIStyle; // customize the appearance
var baseScreenWidth:float = 320.0; // for iOS, the screen width we think we're rendering on
function OnGUI() {
#if UNITY_IPHONE
var guiScale:float = Screen.width/baseScreenWidth;
GUI.matrix = Matrix4x4.TRS (Vector3.zero, Quaternion.identity, Vector3(guiScale, guiScale, 1));
#endif
for (var f:int=0; f<10; f++) {
var score:String="";
var roll1:int = FuguBowl.player.scores[f].ball1;
var roll2:int = FuguBowl.player.scores[f].ball2;
var roll3:int = FuguBowl.player.scores[f].ball3;
switch (roll1) {
case -1: score += " "; break;
case 10: score +="X"; break;
default: score += roll1;
}
score+="/";
if (FuguBowl.player.IsSpare(f)) {
score +="I";
} else {
switch (roll2) {
case -1: score += " "; break;
case 10: score +="X"; break;
default: score += roll2;
}
}
if (f==9) {
score+="/";
if (10==roll2+roll3) {
score +="I";
} else {
switch (roll3) {
case -1: score += " "; break;
case 10: score +="X"; break;
default: score += roll3;
}
}
}
GUI.Label(Rect(f*30+5,5,50,20),score,style);
var total:int=FuguBowl.player.GetScore(f);
if (total != -1) {
GUI.Label(Rect(f*30+5,20,50,20)," "+total,style);
}
}
}
Listing 12-1.
Scaling Added to the FuguBowlScoreboard.js Script
```

The new code is intended only for iOS (although it would work on any platform), so new lines of code are wrapped between `#if UNITY_IPHONE` and `#endif`. UNITY_IPHONE is a built-in preprocessor definition built that is defined only if the build target is iOS (you can also use UNITY_IOS). The end result is that the additional code only turns into executing code for iOS and essentially disappears if you’re building to any other target (you don’t do that for baseScreenWidth because you don’t want to lose its Inspector view setting if the build target is changed).

Of the two lines of real code, the first calculates how much you need to scale the GUI, based on Screen.width , which is the current screen width, and baseScreenWidth, which is the screen width the GUI is coded to render on. The result is assigned to a local variable guiScale.

You might have wondered why we declared baseScreenWidth as a float instead of an int. Well, this is why: Screen.width is an int, and if you divide an int by an int, then the compiler assumes you want the result as an int, which works out okay if, for example, you’re dividing 640 (iPhone 4) by 320 and end up with 2\. But if you’re running on an iPad 2, then you would be dividing 768 by 320, and the result would be rounded down to 2, which is wrong in this case. But as long as one number in the operation is a float, then the compiler will treat the whole thing as a floating-point computation.

Caution

Watch out for operations where you’re supplying ints as inputs and expecting float results. It’s a common source of errors.

The result is passed to a call to Matrix4x4.TRS, which constructs a 4x4 matrix with a translation, rotation, and scale. The translation is Vector3.zero (x, y, and z are 0), Quaternion.identity (the identity Quaternion represents no rotation), and a Vector3 representing the scale. The scale factor based on the screen size is applied in the x and y directions, and the z-axis is left unscaled since that’s the direction pointing into the screen.

### Scale the Pause Menu

Scaling the pause menu is almost the same process. Let’s add the same baseScreenWidth public variable and the same code to set GUI.matrix in the beginning of the OnGUI function in the FuguPause script (Listing [12-2](#A310332_2_En_12_Chapter.html#Par28)).

```
var baseScreenWidth:float = 320.0; // target screen width on iOS
function OnGUI () {
if (IsGamePaused()) {
#if UNITY_IPHONE
var guiScale:float = screenWidth/baseScreenWidth;
GUI.matrix = Matrix4x4.TRS (Vector3.zero, Quaternion.identity, Vector3(guiScale, guiScale, 1));
#endif
if (skin != null) {
GUI.skin = skin;
} else {
GUI.color = hudColor;
}
switch (currentPage) {
case Page.Main: ShowPauseMenu(); break;
case Page.Options: ShowOptions(); break;
case Page.Credits: ShowCredits(); break;
}
}
}
Listing 12-2.
Scaling the Pause Menu in FuguPause.js
```

However, one more alteration is required. The BeginPage function centers the GUI using Screen.width, so you need to use baseScreenWidth instead. Listing [12-3](#A310332_2_En_12_Chapter.html#Par30) shows this code change.

```
function BeginPage(width:int,height:int) {
#if !UNITY_IPHONE
GUILayout.BeginArea(Rect((Screen.width-width)/2,menutop,width,height));
#else
GUILayout.BeginArea(Rect((baseScreenWidth-width)/2,menutop,width,height));
#endif
}
Listing 12-3.
Modified BeginPage Function to Work with Scaled GUI
```

Notice how you used `#if !UNITY_IPHONE`. The exclamation mark means not, so the first line of real code exists for any built target except iOS, and the other line is just for iOS. You could have flipped the order of the two lines of code and started with `#if UNITY_IPHONE`.

While you’re at it, you should remove the Quit button from the pause menu, as Apple rejects any app with a quit option. The Quit button in the ShowPauseMenu function of the FuguPause script is already excluded from Unity web player builds by checking the UNITY_WEBPLAYER preprocessor definition, so you can just add a check for UNITY_IPHONE (Listing [12-4](#A310332_2_En_12_Chapter.html#Par33)).

```
function ShowPauseMenu() {
BeginPage(150,300);
if (GUILayout.Button ("Play")) {
UnPauseGame();
}
if (GUILayout.Button ("Options")) {
currentPage = Page.Options;
}
if (GUILayout.Button ("Credits")) {
currentPage = Page.Credits;
}
#if !UNITY_IPHONE && !UNITY_WEBPLAYER
if (GUILayout.Button ("Quit")) {
Application.Quit();
}
#endif
EndPage();
}
Listing 12-4.
Excluded Quit Button from the iOS Build of FuguPause.js
```

The Quit button is included in the build only if it sees `#if !UNITY_IPHONE && !UNITY_WEBPLAYER`, which is true when the build target is not iOS and not a web player. When you build the game now, you should see the pause menu running on a device (or the iOS simulator) scaled up with the screen resolution and missing the Quit button, like the iPhone 4 screenshot in Figure [12-4](#A310332_2_En_12_Chapter.html#Fig4).

![A310332_2_En_12_Fig4_HTML.jpg](Images/A310332_2_En_12_Fig4_HTML.jpg)

Figure 12-4.

The scaled pause menu

## Set the Icon

By default, the icon for your app will be the Unity logo. Figure [12-6](#A310332_2_En_12_Chapter.html#Fig6) shows the icons for several demo apps published by Unity Technologies on the App Store.

If we were to build it right now the gloss that is automatically applied by Apple since you didn’t select Prerendered Icon in the iOS player settings.

To customize the icon, you can drag any texture into the Default Icon field of the player settings. Let’s use the Icon1024 texture in the Textures folder, copied from the project for this chapter at [`www.apress.com/9781484231739`](http://www.apress.com/9781484231739) . I often indicate the original texture size in the file name (a 1024 × 1024 texture in this case), which saves a lot of time in assigning icon and splash screen textures.

Since you’re using the texture as an icon, you should adjust the import settings appropriately.

Select the Icon1024 texture in the Project view , and then in the Inspector view, set Texture Type to GUI instead of Texture (Figure [12-5](#A310332_2_En_12_Chapter.html#Fig5)). This will prevent Unity from applying settings that are suitable for textures with 3D models but not for GUI display. For example, textures used with models should have dimensions that are powers of 2, such as 256 × 256 or 512 × 512 because that’s what graphics hardware likes to operates with, and the textures are stretched around models anyway. But, generally, GUI textures should stay at their original resolution since they’re usually sized according to their intended display (which is usually not at a power of 2 resolution). You can switch Texture Type to Advanced to compare the underlying settings of the Texture and GUI preset texture types (i.e., select Texture, then switch to Advanced, and then select GUI and switch to Advanced). Within Advanced, you can override any of the preset values for more control over the import settings.

![A310332_2_En_12_Fig5_HTML.jpg](Images/A310332_2_En_12_Fig5_HTML.jpg)

Figure 12-5.

The import settings for the icon texture Tip

If you’re going to use a texture for two different purposes, duplicate it so you can apply different import settings for each.

Below the Texture Type area is where the texture maximum size and the compression level are specified. Unity will generate a warning at build time if you have used a compressed texture as an icon, pointing out correctly that compressed textures may not look good as icons. Select the Default tab so these settings apply to all platforms unless overridden, set Max Size as 1024 to avoid scaling the texture, and set Format to TrueColor for full-color resolution and no compression. Click Apply to reimport the texture with the adjusted settings, and the preview at the bottom will reflect the new format, size, and memory usage.

Now drag the icon texture into the Default Icon field of the player settings in the Cross Platform area. Examine the Icon foldout of the iOS settings (Figure [12-6](#A310332_2_En_12_Chapter.html#Fig6)). As long as the Override for iPhone check box is not selected, automatically scaled versions of the default icon will appear for all the other icon sizes (Figure [12-6](#A310332_2_En_12_Chapter.html#Fig6)).

![A310332_2_En_12_Fig6_HTML.jpg](Images/A310332_2_En_12_Fig6_HTML.jpg)

Figure 12-6.

The default icon scaled to different icon sizes in the player settings

The various icon sizes correspond to the screen resolutions of the various iOS devices:

*   57 × 57: Any iPhone or iPod touch preceding the fourth generation (the iPhone 3GS or the third-generation iPod touch)
*   114 × 114: Fourth generation and later iPod touch and iPhone
*   72 × 72: The original iPad, iPad 2, and iPad Mini
*   144 × 144: The “new” iPad (or as I like to call it, the iPad 3)

Ideally, there would be an icon tailored for each size, in which case you would select the Override check box and then drag the various versions of your icon to each appropriate box. Any box that you leave unfilled with use the Default Icon file. But Unity does a good job of scaling textures down (it is far preferable to scale down than up), so I often reuse the same 1024 × 1024 texture that I upload for the iTunes Connect app description.

Tip

If you don’t use the same icon file for iTunes Connect and the player settings, make sure the various icons at least resemble each other. I’ve had an app rejected because its icon in iTunes Connect did not resemble the app icon as displayed on a device.

I recommend you always select the Prerendered Icon option to avoid the gloss that otherwise would be automatically added by Apple.

Besides the optional shine, the portion of the build executed from Xcode also automatically rounds the corners of the icon and adds a drop shadow. So, don’t do that yourself! Just keep it square with sharp corners and no special borders.

## Set the Splash Screen

All iOS apps display a static “splash” screen on startup. Like the default app icon, the splash screen in many Unity-built apps shows the Unity logo (Figure [12-7](#A310332_2_En_12_Chapter.html#Fig7)).

![A310332_2_En_12_Fig7_HTML.jpg](Images/A310332_2_En_12_Fig7_HTML.jpg)

Figure 12-7.

The default Unity splash screen

Unless you have the Plus or Pro version of Unity, it is not possible to change the Unity splash screen. However, if you use the Personal edition, you can add a splash image. The Splash Image foldout is located beneath the Icon foldout in the iOS settings, and as with the icon selection, it has slots for multiple resolutions appropriate for the various iOS screens and also for both portrait and landscape orientations (Figure [12-8](#A310332_2_En_12_Chapter.html#Fig8)):

![A310332_2_En_12_Fig8_HTML.jpg](Images/A310332_2_En_12_Fig8_HTML.jpg)

Figure 12-8.

Splash screens for different screen resolutions and orientations

*   Mobile splash screen: 320 × 480 for any iPod touch or iPhone preceding the fourth generation (i.e., the iPhone 4)
*   iPhone 3.5"/Retina: 640 × 960 for the Retina display of the iPhone 4, iPhone 4S, and fourth-generation iPod touches
*   iPhone 4"/Retina: 640 × 1136 for the iPhone 5 and fifth-generation iPod touch
*   iPhone 4.7"/ Retina: 750 × 1334 for the iPhone 6
*   iPhone 5.5"/Retina: 1242 × 2208 for the iPhone 6 Plus and newer
*   iPhone 5.5" Landscape /Retina: 2208 × 1242 for the iPhone 6 Plus and newer
*   iPad Portrait: 768 × 1024 for the original iPad and the iPad 2
*   iPad Landscape: 1024 × 768 for the original iPad and the iPad 2
*   iPad Portrait/Retina: 1536 × 2048 for the Retina display of the new iPad (the iPad 3 and newer)
*   iPad Landscape/Retina: 2048 × 1536 for the Retina display of the new iPad (the iPad 3 and newer)

There is no single default splash screen that Unity will automatically resize for all the splash resolutions, so you have to populate the splash screen boxes for the resolutions and orientations that your game will use. Since only the Portrait orientation is selected in the Resolution and Presentation settings, only the portrait splash screens need to be assigned. Like icons, the splash screens can be any textures, and Unity will scale them as needed, but you’ll get the best results with textures that are already the target size. You’ll want to use something appropriate for your company and game.

Note

Apple recommends that the splash screen resemble an actual screen of your game, but it’s typical to just present a company logo. Personally, I think it’s confusing to have a screen that acts like an unresponsive game screen.

Drag each splash screen texture from the Textures folder into the matching Splash screen box in the player settings (Figure [12-8](#A310332_2_En_12_Chapter.html#Fig8)), meaning Fugu320×480 into Mobile Splash Screen, Fugu640×960 into iPhone 3.5"/Retina, and Fugu1536×2048 into High Res. iPad Portrait. The iPhone 4"/Retina item needs a texture, so drag the closest match, Fugu640×9 into it. And Fugu1536×2048 can be used for iPad Portrait.

## Create a Second Splash Screen

Although you can’t change the splash screen in Unity iOS Basic, you can make a splash screen that appears right after the built-in one. You just need to make a scene that displays the splash texture on the screen for a few seconds and then loads the first game scene. This can also be useful with the Plus and Pro versions of Unity if you want to have more than one splash screen. For example, in HyperBowl I have some HyperBowl artwork displayed by the built-in splash screen and then my Fugu Games logo in the second splash screen.

### Create the Splash Scene

Invoke the New Scene command from the File menu to create a new empty scene. Save it as Splash. Bring up the Build Settings window and click Add Open Scenes to add the new scene to your build. It will appear at the bottom of the list of scenes, so drag it up to the top. Its scene index should now display as 0, meaning it’s the first scene loaded (Figure [12-9](#A310332_2_En_12_Chapter.html#Fig9)).

![A310332_2_En_12_Fig9_HTML.jpg](Images/A310332_2_En_12_Fig9_HTML.jpg)

Figure 12-9.

The Build Settings with a splash scene added

### Create the Splash Screen

The easiest way to display a full-screen texture is with a UI element (or user interaction). In the splash scene, go to the GameObject menu, select UI, and choose Raw Image (Figure [12-10](#A310332_2_En_12_Chapter.html#Fig10)).

![A310332_2_En_12_Fig10_HTML.jpg](Images/A310332_2_En_12_Fig10_HTML.jpg)

Figure 12-10.

Creating a UI image

Rename the resulting GameObject to Screen and examine it in the Inspector view. The newly created image needs to be centered on the screen (Figure [12-11](#A310332_2_En_12_Chapter.html#Fig11)). Unlike uses normalized screen coordinates: x and y range from 0 to 1, where 0,0 is the top-left corner of the screen and 1,1 is the bottom right, so setting x and y to 0.5 specifies that the texture is centered on the screen.

![A310332_2_En_12_Fig11_HTML.jpg](Images/A310332_2_En_12_Fig11_HTML.jpg)

Figure 12-11.

A default GUITexture

You have positioned your image at 0,0,0 and made it 400 × 400\. Drag a texture from the Textures folder in the Project view into the Texture field of the RawImage component. To make the texture stretch across the entire screen, change the scale to 1,1,1 (Figure [12-12](#A310332_2_En_12_Chapter.html#Fig12)).

![A310332_2_En_12_Fig12_HTML.jpg](Images/A310332_2_En_12_Fig12_HTML.jpg)

Figure 12-12.

A full-screen UI

Now if you run the game, you’ll see the splash texture on your screen. When running on a device or the iOS simulator, the splash texture will appear after the built-in splash screen (Figure [12-13](#A310332_2_En_12_Chapter.html#Fig13)).

![A310332_2_En_12_Fig13_HTML.jpg](Images/A310332_2_En_12_Fig13_HTML.jpg)

Figure 12-13.

The secondary splash screen

### Load the Next Scene

As a splash screen, this scene should stay up for just a few seconds and then begin to load the game scene. Like any real game level, this scene could use a main logic script, so create a new JavaScript script, name it FuguSplash, and add the contents of Listing [12-5](#A310332_2_En_12_Chapter.html#Par74).

```
#pragma strict
var waitTime:float=2; // in seconds
var level:String; // scene name to load
function Start() {
yield WaitForSeconds(waitTime);
Application.LoadLevel(level);
}
Listing 12-5.
Wait-and-Load Code in FuguSplash.js
```

There are two public variables that can be customized in the Inspector view: the time (in seconds) to wait before loading the next scene and the name of the scene to load. The Start function is really simple; it waits for the built-in coroutine WaitForSeconds to finish, passing in the specified wait time, and then it calls Application.LoadLevel to load the level you specified. In the process, the current scene will be unloaded so the splash screen will disappear.

Let’s add the script to the splash scene. Create an empty GameObject, name it Splash, and attach the FuguSplash script to it. Then in the Inspector view, enter the name of the bowling scene in the Level field (Figure [12-14](#A310332_2_En_12_Chapter.html#Fig14)). Now if you run the game, either in the Editor as in an iOS build, the splash screen will appear for a couple of seconds, and then the bowling scene will appear.

![A310332_2_En_12_Fig14_HTML.jpg](Images/A310332_2_En_12_Fig14_HTML.jpg)

Figure 12-14.

The FuguSplash script added to the splash scene

Just a few words about WaitForSeconds; it is convenient, but you could have implemented your own version. Listing [12-6](#A310332_2_En_12_Chapter.html#Par78) shows a hypothetical implementation.

```
function WaitForSeconds(waitTime:float) {
var startTime:float = Time.time;
while (waitTime > Time.time-startTime) {
yield;
}
}
Listing 12-6.
Implementation of WaitForSeconds
```

Note that the specified wait time is game time, not real-world time, so WaitForSeconds isn’t useful, for example, while the game is paused and game time is stopped. But if you need to wait in real seconds, you can use the hypothetical implementation of WaitForSeconds and replace Time.time with Time.realtimeSinceStartup, which returns the time in real-world seconds since the game was started (Listing [12-7](#A310332_2_En_12_Chapter.html#Par80)).

```
function WaitForSecondsRealtime(waitTime:float) {
var startTime:float = Time.realtimeSinceStartup;
while (waitTime > Time.realtimeSinceStartup-startTime) {
yield;
}
}
Listing 12-7.
A Real-Time Version of WaitForSeconds
```

## Display the Activity Indicator

When something is taking a while, it’s nice to have some kind of loading indicator. For the initial scene load, there’s a convenient way to display the iOS spinner that you often see in apps as an activity indicator. You can enable this by choosing one of the activity indicator styles for Show Loading Indicator at the bottom of the Resolution and Presentation foldout in the player settings (Figure [12-15](#A310332_2_En_12_Chapter.html#Fig15)). Given the splash screen is providing a mostly white background, Gray is the best style (the other styles are white).

![A310332_2_En_12_Fig15_HTML.jpg](Images/A310332_2_En_12_Fig15_HTML.jpg)

Figure 12-15.

Player settings with Gray selected for the Show Loading Indicator

Now when you run the game in an iOS build, you’ll see the Gray activity indicator spinning on the built-in splash screen.

## Script the Activity Indicator

It would be nice to use the activity indicator elsewhere, especially when loading other levels. Fortunately, Unity has a scripting interface for the activity indicator, in the form of static functions in the Handheld class. You just need a script that calls those functions to start and stop the activity indicator. Create a new JavaScript called FuguSpinner and add the contents of Listing [12-8](#A310332_2_En_12_Chapter.html#Par84) to the script.

```
#pragma strict
#if UNITY_IPHONE
function Start() {
DontDestroyOnLoad(this.gameObject);
Handheld.SetActivityIndicatorStyle(iOSActivityIndicatorStyle.WhiteLarge);
Handheld.StartActivityIndicator();
}
function OnLevelWasLoaded() {
Handheld.StopActivityIndicator();
Destroy(gameObject);
}
#endif
Listing 12-8.
Starting and Stopping an Activity Indicator in FuguSpinner.js
```

In the Start callback, the call to Handheld.SetActivityIndicatorStyle specifies the appearance of the activity indicator. The available options, represented by the iOSActivityIndicatorStyle enum, match the Loading Indicator options available in the player settings. You’ll choose iOSActivityIndicatorStyle.WhiteLarge since you know the secondary splash screen has a mostly black background.

The call to Handheld.StartActivityIndicator makes the activity indicator visible in the center of the screen. The Unity Script Reference documentation for Handheld.StartActivityIndicator says that the activity indicator will be activated after the current frame, so you need to yield at least a frame before calling Application.LoadLevel. Otherwise, the indicator won’t appear until after the new level is loaded, which defeats the purpose of activating the indicator in the first place. You don’t have to worry about that here because you know the FuguSplash script is yielding for some number of seconds before loading the next scene.

However, the activity indicator keeps going even after the new scene is loaded by FuguSplash. To stop the activity indicator after the scene has finished loading, this script has to survive the scene load, which is why DontDestroyOnLoad is called at the beginning of the Start function, passing it the GameObject this script is attached to. This ensures the GameObject, and thus this script, survives when the next scene is loaded. Because the GameObject survives the scene load, you can be assured the MonoBehaviour callback OnLevelWasLoaded is called after the new scene has finished loading. This is a perfect place to turn off the activity indicator with a call to Handheld.StopActivityIndicator. At this point, the script and the GameObject it’s attached to aren’t needed anymore, so Destroy is called on the GameObject.

Tip

It’s a good practice to destroy objects when you don’t need them anymore. Besides freeing up space, you want to avoid the worst case where you loop back into the scene where the object was originally created and then you end up with two versions of the same object!

Note that DontDestroyOnLoad and Destroy are both static functions in the Object class. Just as you called Instantiate in Chapter [7](../Text/A310332_2_En_7_Chapter.html) instead of calling Object.Destroy, for example (or UnityEngine.Object.Destroy to avoid the name clash with System.Object class in .NET), here you’re implicitly calling this Destroy, which works because this is an Object.

Attach the FuguSpinner script to a new GameObject in the scene, called Spinner. Now when you perform a Build and Run operation on a device, the activity indicator appears in the center of the splash image and disappears after the next scene has loaded (Figure [12-16](#A310332_2_En_12_Chapter.html#Fig16)).

![A310332_2_En_12_Fig16_HTML.jpg](Images/A310332_2_En_12_Fig16_HTML.jpg)

Figure 12-16.

Secondary splash screen with an activity indicator

## Explore Further

Now your bowling game for iOS looks like a real app! It has an icon, a splash screen (or two), and an activity indicator spinning away on the splash screen. Also, it runs in portrait orientation, and the graphics scale to fit the screen including the UnityGUI scoreboard and pause menu. The menu is even automatically functional in iOS, with its buttons responding to taps on the screen. You can’t quite say that about the actual gameplay, but you’ll implement touchscreen-based game controls in the next chapter.

### Reference Manual

Once again, you’ve spent some time in the player settings (a recurring theme now that you’re making iOS builds), so the Reference Manual documentation on the player settings is worth a review .

You used just one new component in this chapter, a GUITexture for your splash screen image. GUITexture and its parent class GUIElement have no relation to UnityGUI (in fact, GUITexture and its sibling GUIText were the closest things to user interface elements before UnityGUI arrived).

This chapter was devoted largely to icon and splash screen images, which should all be imported using the GUI preset import settings, which are documented for Texture2D in the “Asset Components” section.

### Scripting Reference

The one new UnityGUI feature introduced in this chapter was the matrix variable in the GUI class. At the time of this writing, the Scripting Reference page for that variable is nearly blank, but the GUIUtility class has some better-documented functions, including ScaleAroundPivot, which can be used as an alternative to setting GUI.matrix (in fact, it is a helper function that sets GUI.matrix internally).

You used only the Matrix4x4.TRS constructor, but the Scripting Reference documentation on Matrix4x4 is not bad, explaining how matrices are used in Unity and listing many class functions that will be useful if you ever have to start messing around with matrices.

The yield instruction WaitForSeconds was already introduced in Chapter [8](../Text/A310332_2_En_8_Chapter.html) for the game-over state, and it turns out to be convenient for displaying a splash screen a certain number of seconds. You took this opportunity to explore hypothetical implementations of WaitForSeconds and compare the static Time variables Time.time and Time.realTimeSinceStartup. It’s an important distinction when the pause menu is up and game time is suspended.

You called the static Application function LoadLevel to transition from the splash scene to the bowling scene, but you also could have called an asynchronous version of the function, Application.LoadLevelAsync. As an asynchronous function, it doesn’t suspend Unity while it’s loading a new scene, so, really, you could have anything happen in the splash scene while waiting for the new scene to finish loading.

As demonstrated with your use of the activity indicator functions in the Handheld class, you can make Objects survive across scene loads by calling DontDestroyOnLoad on them, and you use Destroy on them when they’re not needed anymore. These are both static functions in the Object class, which is always worth reading up on since it’s the parent class of everything that can be in a scene.

To display and hide the activity indicator, you called the StartActivityIndicator and StopActivityIndicator functions in the Handheld class. See the documentation on the activity indicator functions about the necessity to yield before performing a scene load. Use the iOSActivityIndicatorStyle enumeration for a list of the available activity indicator styles.

The Handheld class is worth a looking over, as it contains all the scriptable Unity mobile features (except those specific to iOS). For example, the Handheld class has a static PlayFullScreenMovie function that can play a movie from local storage or can stream it from a web site, which is another possibility for a cool-looking splash scene. The Scripting Reference page for that function has details on the supported video formats and how to control the movie player (based, at the time of this writing, on the native iOS movie player, MPMoviePlayerController).

### iOS Developer Library

As you explore Unity iOS features, more and more of the documentation in the iOS Developer Library is relevant. Remember, that documentation is available both on the Apple developer site ( [`http://developer.apple.com/`](http://developer.apple.com/) ) and from the Xcode Organizer window.

One document worth reading in its entirely, found in the “User Experience” topic of the iOS Developer Library, is Apple’s iOS Human Interface Guidelines. The original guidelines dating back to the original Mac were the gold standard in user interface best practices. Since then, the guidelines have been split into Mac and iOS versions, but you should read the iOS version if only to avoid app rejections. Related to this chapter, the “Custom Icon and Image Creation Guidelines” section lists requirements and recommendations for creating app icons and splash screens (known as launch images in Apple’s terminology).

Many Unity classes that are mobile-specific or iOS-specific will have an Objective-C counterpart. For example, the activity indicator controlled by Unity’s Handheld class is accessed in Objective-C via the UIActivityIndicatorview class, also found in the “User Experience” topic in the “Windows and Views” section.

### Asset Store

The Smart Scene Changer from Ciitt, available free on the Unity Asset Store, provides more sophisticated introductory screen behavior than what was described in this chapter, including fading the screen in and out and using multiple screens. I use the Transitions Manager ( [`https://www.assetstore.unity3d.com/en/#!/content/80061`](https://www.assetstore.unity3d.com/en/#!/content/80061) ) in all my apps.

### Books

Josh Clark’s Tapworthy: Designing Great Apps is the book that convinced me to eschew automatic icon gloss and always select Prerendered Icon in the Unity player settings. It’s also a good treatment of app design in general.

There are plenty of textbooks on linear algebra (and even a hefty “matrix” article on [`http://wikipedia.org/`](http://wikipedia.org/) ), but any computer graphics book will have a primer on the matrix math. For example, Real-time Rendering ( [`http://realtimerendering.com/`](http://realtimerendering.com/) ) has an appendix called “Some Linear Algebra.”

# 13. Handling Device Input

Although your bowling game is up and running on iOS , it’s not quite in a playable state because you don’t have any working player controls. That brings me to the issue of input handling, which is perhaps the most obvious difference between desktop and mobile games.

Instead of reading the mouse or keyboard, an iOS game has to use one of its device sensors, typically the touchscreen (tapping and swiping) or accelerometer (shaking and tilting). You’ll opt for touchscreen input to control the bowling ball in this chapter, but you’ll do some shake detection with the accelerometer and play with the device camera a little bit, too.

The project for this chapter on [`www.apress.com/9781484231739`](http://www.apress.com/9781484231739) has the script changes introduced in this chapter, and no other assets are added. This is true for the remainder of this book. It’s all scripting from here on out!

## The Touchscreen

When you build for iOS, Unity works with the touchscreen in iOS, so if you build and run your bowling game on a device right now, or test it with the Unity Remote in the Editor, you can operate the initial menu by tapping the buttons. When you tap the Play button on the pause menu, the bowling ball drops. So far, so good.

But what about the bowling ball control? Input.GetAxis does return values when swiping on the screen, but the results are unlikely to be exactly what you want (in older versions of Unity iOS, Input.GetAxis was not functional at all). In any case, you need to first define how the control will work in the iOS version of the bowling game.

### Swipe the Ball

For Fugu Bowl, you’ll adopt the touchscreen control that I use for HyperBowl. The player pushes the ball in a certain direction by swiping along the screen in that direction. Swiping up on the screen will push the ball forward, swiping down will push the ball back, swiping left will roll it left, and swiping right will roll it right.

The swipe control scheme is similar enough to the mouse controls that you can work within the framework of the existing FuguForce script. Bring up that script and add some new public variables, swipepowerx and swipepowery, so you can adjust the swipe power, just like you did with the mouse control (Listing [13-1](#A310332_2_En_13_Chapter.html#Par8)).

```
var swipepowerx:float = 0.1;
var swipepowery:float = 0.1;
Listing 13-1.
Adjustment Variables for Swipe Control in FuguForce.js
```

You added new variables instead of reusing mousepowerx and mousepowery so that you can switch between the stand-alone and iOS build targets without clobbering the power adjustment values for each platform. The properties for both will always show up in the Inspector view (Figure [13-1](#A310332_2_En_13_Chapter.html#Fig1)).

![A310332_2_En_13_Fig1_HTML.jpg](Images/A310332_2_En_13_Fig1_HTML.jpg)

Figure 13-1.

Control adjustments for both desktop and iOS in FuguForce.js

The big change is your CalcForce function, which gets called by your Update callback once per frame. On iOS, instead of calling the static function Input.GetAxis to see how much the mouse has been moved, CalcForce calls the static function Input.GetTouch to check for swipes. The UNITY_IPHONE preprocessor definition makes sure the new code exists only on iOS and the old code exists on any other platform (Listing [13-2](#A310332_2_En_13_Chapter.html#Par11)).

```
function CalcForce() {
var deltatime:float = Time.deltaTime;
#if UNITY_IPHONE
if (Input.touchCount > 0) {
// Get movement of the finger since last frame
var touch:Touch = Input.GetTouch(0);
if (touch.phase == TouchPhase.Moved) {
var touchPositionDelta:Vector2 = touch.deltaPosition;
forcey = swipepowery*touchPositionDelta.y/deltatime;
forcex = swipepowerx*touchPositionDelta.x/deltatime;
}
}
#else
forcey = mousepowery*Input.GetAxis("Mouse Y")/deltatime;
forcex = mousepowerx*Input.GetAxis("Mouse X")/deltatime;
#endif
}
Listing 13-2.
Detecting Swipes in FuguForce.js
```

Like Input.GetAxis, Input.GetTouch returns information registered since the previous frame, so you can call it in CalcForce, which gets called from Update and thus once each frame.

Input.GetTouch returns a struct of type Touch, which describes the touch event; whether the finger was pressed, released, or moved; and if moved, by how many pixels.

You’re now dealing with multitouch screens , so Input.GetTouch has one parameter, an integer indicating which of the latest Input.Touch events to return. The number of touch events is available in the static variable Input.touchCount, so you can process all touch events in a loop like this:

```
for (var i:int=0; i < Input.touchCount; ++i) {
var touch:Touch = Input.GetTouch(i);
// do your stuff here
}
```

To keep things simple here, you’re interested in only one finger, so just check whether there’s one or more Touch events available, and if so, just check the first one.

```
if (Input.touchCount > 0) {
var touch:Touch = Input.GetTouch(0);
```

Touch events can signify different finger actions: a press, release, or drag. This can be checked by inspecting the phase property of the Touch object, which is a TouchPhase enumeration. You’re interested only in Touch events that signify a finger has dragged along the screen, so check whether the touch phase is TouchPhase.Moved.

```
if (touch.phase == TouchPhase.Moved) {
```

If it is, then calculate the force used to push the bowling ball. It’s similar to the mouse control, but instead of multiplying by Input.GetAxis, multiply by the deltaPosition property of the Touch event, which is the number of pixels the finger has dragged across.

```
var touchPositionDelta:Vector2 = touch.deltaPosition;
forcey = powery*touchPositionDelta.y/deltatime;
forcex = powerx*touchPositionDelta.x/deltatime;
```

Notice the deltaPosition property is a Vector2, which is just like a Vector3, but with only x and y properties and no z value.

Now if you click Play in the Editor and test with the Unity Remote or if you perform a Build and Run operation on a device, the ball will roll in the direction you swipe.

The complete FuguBowlForce script with the touchscreen additions is available in the project for this chapter on [`http://learnuninty4.com/`](http://learnuninty4.com/) .

### Tap the Ball

Although your bowling game doesn’t care where on the screen the swiping takes place, many games require the ability to detect whether a specific GameObject has been touched. On desktop and web platforms, mouse events over GameObjects with Collider components can be detected with callbacks such as OnMouseDown, OnMouseUp, and OnMouseOver. For example, the OnMouseDown callback in a script is invoked at the beginning of a mouse click that takes place over the script’s GameObject or, rather, the GameObject’s Collider component.

Note

The OnMouse callbacks also work on GameObjects with GUIText and GUITexture components. This was the closest thing to a built-in GUI system in Unity before UnityGUI was introduced.

To demonstrate how the OnMouse callbacks work, create a new JavaScript named FuguDebugOnMouse and attach it to the Ball GameObject (Figure [13-2](#A310332_2_En_13_Chapter.html#Fig2)).

![A310332_2_En_13_Fig2_HTML.jpg](Images/A310332_2_En_13_Fig2_HTML.jpg)

Figure 13-2.

Ball with the FuguDebugOnMouse script attached

Then add the contents of Listing [13-3](#A310332_2_En_13_Chapter.html#Par29) to the script.

```
#pragma strict
#if !UNITY_IPHONE
function OnMouseDown () {
#else
function OnTap () {
#endif
Debug.Log("GameObject "+ gameObject.name + " was touched");
}
Listing 13-3.
Detecting a Mouse Click in FuguDebugMouse.js
```

The script contains an OnMouseDown callback that logs a message about the GameObject getting touched. When you run the game in the Editor and click the ball, the debug message appears in the Console view (Figure [13-3](#A310332_2_En_13_Chapter.html#Fig3)).

![A310332_2_En_13_Fig3_HTML.jpg](Images/A310332_2_En_13_Fig3_HTML.jpg)

Figure 13-3.

Debug message demonstrating the OnMouseDown callback

Unity iOS invokes the OnMouse callbacks in response to touch events, except in the cases where there’s no obvious mapping. For example, what’s the touch equivalent of OnMouseOver, which is called when the mouse hovers over a GameObject? But it makes sense to call OnMouseDown when a GameObject is tapped (and OnMouseUp when the finger stops touching the screen). And this is what happens, as you can see if you run the game again in the Editor with the Unity Remote or if you perform a Build and Run operation on a device. In either case, tapping the ball will produce the same debug message you saw when clicking it.

Although it’s convenient that the OnMouse callbacks work for touches, at least to this extent, it’s useful to know how to implement your own tap-object detection. It’s a straightforward combination of checking for a tap and then performing a raycast from the screen position into the 3D world along the camera direction. Raycasting is the process of finding the first object that a ray intersects (as you may recall from high-school math, a ray has a starting position and a direction, like an arrow).

To demonstrate, let’s create a new JavaScript named FuguTap, attach it to the Main Camera (Figure [13-4](#A310332_2_En_13_Chapter.html#Fig4)), and add the contents of Listing [13-4](#A310332_2_En_13_Chapter.html#Par34) to the script.

![A310332_2_En_13_Fig4_HTML.jpg](Images/A310332_2_En_13_Fig4_HTML.jpg)

Figure 13-4.

Main Camera with the FuguOnTap script attached

```
#pragma strict
#if UNITY_IPHONE
function Update () {
for (var i = 0; i < Input.touchCount; ++i) {
if (Input.GetTouch(i).phase == TouchPhase.Began) {
var ray:Ray = camera.ScreenPointToRay (Input.GetTouch(i).position);
var hit:RaycastHit;
if (Physics.Raycast (ray,hit,camera.farClipPlane,camera.cullingMask)) {
hit.collider.SendMessage("OnTap",SendMessageOptions.DontRequireReceiver);
}
}
}
}
#endif
Listing 13-4.
A Script Mimicking OnMouseDown Events
```

The entire script consists of an Update callback that loops through all the touch events registered in the past frame. But in this case, only the beginning of a tap is of interest, so only touches that are in the phase TouchPhase.Began are considered. The screen coordinates of each such touch is extracted and passed to the camera function ScreenPointToRay to form a ray that originates at the screen position and projects along the camera direction into the 3D world.

The ray is passed in as the first argument to the static function Physics.Raycast to see whether the ray intersects a Collider component of any GameObject. Information about the first intersection, if any, is returned in the RayCastHit struct that is passed in as the second argument. The third argument is the raycast distance, and the fourth argument is the set of layers that will be tested for intersection. You restrict the possible raycast results by supplying the camera’s far plane distance as the raycast distance and the camera’s culling mask (set of visible layers) as the set of layers to test for intersection.

If the call to Physics.Raycast returns true, meaning there’s been an intersection, then you retrieve the intersecting Collider component and call SendMessage on it to invoke OnTap callbacks in attached scripts (calling SendMessage on a component is equivalent to calling SendMessage on the component’s GameObject).

Notice that the call to SendMessage includes an optional second argument, SendMessageOptions.DontRequireReceiver. Without that argument, Unity will report an error if the message is sent to a GameObject that doesn’t have any matching functions, or, in this case, any OnTap functions.

The entire Update callback is wrapped in a UNITY_IPHONE if def, because it’s only intended for iOS touchscreen input. Likewise, you can now use UNIT_IPHONE to rename the OnMouseDown callback to OnTap so it will be called by the FuguOnTap script (Listing [13-5](#A310332_2_En_13_Chapter.html#Par40)).

```
#pragma strict
if !UNITY_IPHONE
function OnMouseDown () {
#else
function OnTap () {
#endif
Debug.Log("GameObject "+ gameObject.name + " was touched");
}
Listing 13-5.
FuguDebugOnMouse Script with an Alternate Callback Name for iOS
```

Now if you run the game again and tap the ball, you’ll still see the debug message, but it will result from the OnTap messages sent from FuguOnTap.

## The Accelerometer

Every iOS device has an accelerometer that measures acceleration in three different directions. The three values can be examined in the static variable Input.accelerometer, which is a Vector3.

The x value corresponds to acceleration that runs left to right along the face of the device. The y value represents the acceleration running from top to bottom along the face of the device, and the z value is the acceleration from the front to the back of the device. Starting with Unity 4, the accelerometer values are adjusted for the device orientation, so if you’re holding the device in landscape mode, the accelerometer x values still run from left to right.

### Debug the Accelerometer

A time-honored way to figure out how code works is to print stuff out. Let’s create a new JavaScript called FuguDebugAccel and attach it to a new GameObject named DebugAccel in the bowling scene. Add the Update function shown in Listing [13-6](#A310332_2_En_13_Chapter.html#Par45) to the script.

```
function Update () {
Debug.Log("accel x: "+Input.acceleration.x+" y: "+Input.acceleration.y+" z: "+Input.acceleration.z);
}
Listing 13-6.
Code to Print Out Accelerometer Values
```

The Update function takes the x, y, and z accelerometer values and concatenates them into a somewhat readable String. Debug.Log sends that String to the Console view if you’re testing in the Unity Editor (with the Unity Remote) or to the Xcode debug area if you’re running in the iOS simulator or on a device.

Try tilting the device around to see how the values change. For example, the Debug.Log output shown in Figure [13-5](#A310332_2_En_13_Chapter.html#Fig5) results from placing the device flat on a table. The x and y values are nearly 0, and the z value is close to -1\. If the device is flipped upside down, it would be close to 1.

![A310332_2_En_13_Fig5_HTML.jpg](Images/A310332_2_En_13_Fig5_HTML.jpg)

Figure 13-5.

Debug.Log output of accelerometer values

The acceleration values are in units of gravity. At rest, each accelerometer value ranges from -1 to 1, where 1 is full earth gravity (9.8 meters per second squared). Now try shaking the device. You should see the numbers spike up and down.

### Detect Shakes

An issue with your pause menu on iOS is that you can’t pause with the Esc key since there’s no Esc key on your devices (although Android devices typically have a Back button that behaves like an Esc key).

It’s straightforward to add a pause button to the screen in the FuguPause script by just adding a GUI.Button call inside the OnGUI function.

```
if (GUI.Button(Rect(0,0,100,50), "Pause") { PauseGame(); }
```

However, I don’t like to have buttons on the screen during gameplay, especially on really small screens like the iPhone. So, let’s take advantage of the alternate input that iOS devices can provide and pause by shaking the device.

From your previous exercise in printing out accelerometer values, you saw that shaking the device causes at least one of the x, y, or z values of Input.acceleration to spike up or down. So, you can check for a shake by checking whether any of the values are much larger than 1 or much less than -1, for example. But you don’t have to be exact, so you can just take the square of the magnitude (length) of the vector.

```
if (Input.acceleration.sqrMagnitude>shakeThreshold)
```

Getting the sqrMagnitude of the Vector3 is faster than getting the magnitude since the magnitude of a vector is the square root of x² + y² + z², and this way you avoid the square root operation, which is relatively expensive (remember learning to calculate square roots by hand in high school?).

So, let’s start by adding a public variable for the shake threshold to the FuguPause script.

```
var shakeThreshold:float = 5;
```

Now you can tweak that property in the Inspector view (Figure [13-6](#A310332_2_En_13_Chapter.html#Fig6)) and make it more or less sensitive to shakes.

![A310332_2_En_13_Fig6_HTML.jpg](Images/A310332_2_En_13_Fig6_HTML.jpg)

Figure 13-6.

Adjusting the shake-to-pause threshold in the Inspector view

Next, add the check of Input.acceleration as a UNITY_IPHONE replacement for the check of the Esc key (Listing [13-7](#A310332_2_En_13_Chapter.html#Par60)).

```
var shakeThreshold:float = 5;
function Update() {
#if UNITY_IPHONE
if (Input.acceleration.sqrMagnitude>shakeThreshold)
#else
if (Input.GetKeyDown(KeyCode.escape))
#endif
{
switch (currentPage) {
case Page.None: PauseGame(); break; // if the pause menu is not displayed, then pause
case Page.Main: UnPauseGame(); break; // if the main pause menu is displaying, then unpause
default: currentPage = Page.Main; // any subpage goes back to main page
}
}
}
Listing 13-7.
Adding Shake to Pause in the FuguPause.js
```

That’s all you need. Now when you test with the Unity Remote or on a device, shaking the device will pause the game! The augmented FuguPause script (just two lines of new code!) is in the project for this chapter on [`www.apress.com/9781484231739`](http://www.apress.com/9781484231739) .

## The Camera

Unity 5.x provides built-in support for iOS cameras. Unity also provides cross-platform support for reading webcam video into a texture.

### Read the Web Cam

Unity has a special kind of texture (like Texture2D, a subclass of Texture) called WebCamTexture that displays video from an attached hardware camera. It works on multiple platforms, including iOS. Let’s give it a try. Create a JavaScript called FuguWebCam and add the Start function given in Listing [13-8](#A310332_2_En_13_Chapter.html#Par64). The Start function creates a WebCamTexture, assigns the texture to the material of the GameObject you’ve attached the script to, and then calls Play to start updating the texture from the camera video.

```
#pragma strict
function Start () {
var webcamTexture:WebCamTexture = WebCamTexture();
GetComponent.().material.mainTexture = webcamTexture;
webcamTexture.Play();
}
Listing 13-8.
Playing a WebCamTexture in FuguWebCam.js
```

Now attach the script to the Ball GameObject. If you click Play in the Editor, you’ll see video from the Mac camera appear on the ball. And if you perform a Build and Run operation on a device, you’ll see the same thing, only using the device camera.

## Explore Further

All in all, it didn’t take much work (compared to the packaging effort in the previous chapter) to implement touchscreen input for the bowling game, although in practice you’ll spend a lot of time testing and adjusting controls for a game to get it just right. In any case, with the shake-to-pause feature, the functional port of the bowling game is complete. In other words, the iOS version game is now playable!

And despite that there isn’t a real use for the device camera in this game, you did activate it using Unity’s WebCamTexture class to render a video texture on the bowling ball. Gimmicky, but game development is very much about learning! Especially in iOS, there’s an opportunity to play around with a lot of different control schemes and input sensors.

### Scripting Reference

Almost all the Unity features introduced in this chapter are members of the Input class. In fact, the Scripting Reference documentation on Input includes an overview of the iOS input features.

After reading the overview of the Input class, you should read the pages for each of the functions used in this chapter, in particular Input.GetTouch (and the associated Touch struct), which has code samples showing how to loop through all the Touch events in each frame, and Input.acceleration, which has code samples showing how to check acceleration values in every frame.

One thing to keep in mind is that the accelerometer can generate multiple values per frame. For most cases, it should be sufficient to just sample Input.accelerometer once per frame, but if finer-grained sampling is required, you can access the variables Input.accelerationEventCount and Input.accelerationEvents to obtain all acceleration events.

The Input class provides access to other iOS sensors. The static variables Input.gyro, Input.compass, and Input.location return data from the gyroscope, compass, and location services, respectively. In addition to the pages documenting those variables, you should check out the pages on the classes of those variables: the Gyroscope, Compass, and LocationServices classes. The documentation is sparse, but you can find code samples in the individual class functions. The static variable Input.compensateSensors controls whether the accelerometer, gyroscope, and compass data are adjusted for the screen orientation.

Besides testing object selection, raycasting is used a lot in games and graphics, so you should familiarize yourself with the page on Physics.Raycast, which has several code examples. For example, in HyperBowl, I use raycasting to keep the Main Camera from dipping below the ground and to set the initial positions of GameObjects to rest just on top of the ground. In both cases, I raycast down from the GameObject position toward the ground and check the resulting distance. Finally, I covered the use of the WebCamTexture class. The page on WebCamTexture has a lot of stuff, but click the links to its Play, Stop, and Pause functions (the functions you’d most commonly use) to find code samples on each of those pages. Also, check out the link to the WebCamTexture constructor. That page lists a variety of constructors that allow you to customize the texture size for different resolutions .

### iOS Developer Library

It’s a good idea to read the iOS Reference Library in addition to, or even before, reading the Unity Script Reference, so you’ll know what iOS capabilities are available to be exposed through Unity and how Unity classes and functions map to their iOS counterparts.

You can view the iOS Reference Library (no login required) at [`http://developer.apple.com/library`](http://developer.apple.com/library) or from the Xcode Organizer; from there select the Guides tab and browse the guides, in particular the following:

*   The guide “Event Handling Guide” provides an overview of touch events, which relate to Unity’s Input.GetTouch and the Touch class, and motion events, which relate to Input.acceleration and the other Input accelerometer variables.
*   The guide “Camera Programming Topics for iOS” describes the UIImagePickerController class used by the Prime31 Etcetera plug-ins.
*   The guide “AV Foundation Programming Guide” describes the video capture functions most likely used for the WebCamTexture class in Unity.

### Asset Store

The Unity Input class gives you access to basic touch information, but it doesn’t provide access to the iOS gesture recognizers, which detect higher-level “gestures,” such as swiping and pinching. The Asset Store comes to the rescue again, offering third-party packages that provide high-level gesture detection. I use the FingerGestures package, which has a nice callback system for detecting and handling various gestures.

For example, in HyperBowl I wanted the pause menu to come up when the player pinches the screen (two fingers on the screen coming together). With the Finger Gestures package, it just involves adding a callback for the gesture in the pause menu script, as shown here:

```
function OnGesturePinchEnd(pos1:Vector2,pos2:Vector2) {
PauseGame();
}
```

Also necessary is a line in the Start or Awake function that adds the callback to the FingerGestures callback list for that gesture, as shown here:

```
FingerGestures.OnPinchEnd += OnGesturePinchEnd;
```

Unity doesn’t provide script access to everything available in iOS , and that’s where plug-ins come in. The Unity plug-in system allows you to add new script functions that access “native” code. Generally speaking, if you can code something in C, C++, or Objective-C, you can make a Unity plug-in for it. For example, in the Unity Asset Store, you’ll find there are plug-ins built around mobile ad SDKs and plug-ins for accessing iOS features.

Plug-ins are installed in the Assets/Plugins folder, which, as I mentioned before, is a good place to store scripts that have to be loaded before any other scripts. They also often require manual integration steps in Xcode, like adding entries to the `Info.plist` file or installing additional code libraries (preventing a Build and Run operation from Unity—you have to select Build, modify the Xcode project, and then select Run from Xcode). And then there’s always the risk that multiple plug-ins installed in the same Unity project will have conflicts.

That’s why I like to use the plug-ins provided by Prime31 Studios at [`http://prime31.com/unity`](http://prime31.com/unity) . They provide a large variety of plug-ins that can coexist in the same Unity project, and all Xcode modifications are performed by a Unity postprocessor script, so the Build and Run operation from Unity will still work and no messing around in Xcode is required.

For example, the Prime31 Etcetera plug-in, available on the Asset Store, has a bunch of assorted features, including access to the iOS Photos gallery and the device camera with one function call, like this:

```
EtceteraBinding.promptForPhoto(1.0);
```

Figure [13-7](#A310332_2_En_13_Chapter.html#Fig7) shows the resulting prompt in my Fugu Maze app (where I use the photo chooser to allow players to customize the maze wall texture). If you want to try it, download Fugu Maze Free from the App Store or one of the free HyperBowl lanes (I use the photo chooser to customize the bowling ball texture).

![A310332_2_En_13_Fig7_HTML.jpg](Images/A310332_2_En_13_Fig7_HTML.jpg)

Figure 13-7.

The Prime31 Etcetera plug-in photo chooser on an iPad

Another Prime31 plug-in that makes available additional device input is the iCade plug-in (available only on the Prime31 web site). The iCade is a retro-style arcade cabinet that provides joystick input to iOS devices through a Bluetooth connection. The iCade essentially pretends it’s a Bluetooth keyboard, so any code using the iCade plug-in is similar to the code for keyboard controls. If you added code for iCade controls to the CalcForce function of your bowling ball code, it would look something like the snippet in Listing [13-9](#A310332_2_En_13_Chapter.html#Par90) (this sample is actually taken from HyperBowl).

```
var yinput:float = 0;
if (iCadeBinding.state.JoystickUp) yinput = 1;
if (iCadeBinding.state.JoystickDown) yinput = -1;
var xinput:float = 0;
if (iCadeBinding.state.JoystickLeft) xinput = 1;
if (iCadeBinding.state.JoystickRight) xinput = -1;
var deltatime:float = Time.deltaTime;
forcey += iCadePowery*yinput/deltatime;
forcex += iCadePowerx*xinput/deltatime;
Listing 13-9.
iCade Additions to CalcForce Using the Prime31 iCade Plug-In
```

Just as if you’re checking whether four different keys were pressed, you’re checking whether the joystick was moved in any of four directions. The joysticks aren’t any more sensitive than that.

# 14. Optimization

Consistent with Donald Knuth’s much-ballyhooed quote that “premature optimization is the root of all evil,” I’ve left the topic of optimization until nearly the end of this book. In this chapter, you’ll optimize your bowling app and go over the precursors to that process, establishing the target performance and measuring the current performance. The project for this chapter on [`www.apress.com/9781484231739`](http://www.apress.com/9781484231739) contains the optimized scripts and optimization helper scripts developed in this chapter.

## Choose Your Target

There’s a saying, “Fast, good, or cheap. Pick two.” That’s generally the case with optimization. Among quality, space, and speed, you can often at best achieve two of the three, at the expense of the third.

### Frame Rate

Before you start optimizing, you should establish your target frame rate. iOS devices refresh their screens 60 times per second, but by default, Unity iOS builds attempt to achieve 30 frames per second (fps). Targeting a frame rate at half the device refresh rate is less demanding on the device battery, and 30 fps is a lot easier to achieve than 60 fps. Your bowling game is simple, so you should be able to hit 60 fps if you’re willing to make some compromises.

It might seem obvious to always target the highest possible frame rate, but if a game switches frequently between 30 fps and 60 fps, the discontinuity may be more distracting than just steadily running at 30 fps.

Nevertheless, it makes sense to start with a target of 60 fps before profiling so you can see your peak performance and then lower it later if you need to do so.

#### Create the Script

There’s no way to set the target frame rate in the Unity Editor, but fortunately, you can adjust the target frame rate in a script by setting the static variable Application.targetFrameRate. So, let’s create a new JavaScript script, place it in the Scripts folder, and name it FuguFrameRate (Figure [14-1](#A310332_2_En_14_Chapter.html#Fig1)).

![A310332_2_En_14_Fig1_HTML.jpg](Images/A310332_2_En_14_Fig1_HTML.jpg)

Figure 14-1.

Adding a script in the Scripts folder

This will be a really short script, with one public variable and a Start callback with just one line of code (Listing [14-1](#A310332_2_En_14_Chapter.html#Par8)).

```
#pragma strict
public var frameRate:int = 60;
function Start() {
Application.targetFrameRate = frameRate;
}
Listing 14-1.
Complete Script for FuguFrameRate.js
```

The script sets the target frame rate by changing the value of the static variable Application.targetFrameRate to the value of frameRate, which is a public variable so it can be adjusted in the Inspector view. Since iOS devices all have a screen refresh rate of 60 fps, that’s the best frame rate you can achieve and thus a reasonable default value for frameRate.

#### Attach the Script

To utilize the FuguFrameRate script, it has to be added to a scene so that its Start callback is executed when the game starts. So, let’s create an empty GameObject in the bowling scene, name it FrameRate, and drag the FuguFrameRate script onto it (Figure [14-2](#A310332_2_En_14_Chapter.html#Fig2)).

![A310332_2_En_14_Fig2_HTML.jpg](Images/A310332_2_En_14_Fig2_HTML.jpg)

Figure 14-2.

Frame rate script included in the bowling scene

This script was added to your bowling scene, but you could just as well have added it to your splash scene instead since Application.targetFrameRate only needs to be set once and it stays at that value even when a new scene is loaded. Your current splash scene doesn’t really do anything, so running at Unity’s default 30 fps is fine.

However, if the splash scene were to display a cool animation, you might want to set it at 60 fps, and in that case, you would also add the frame rate script to that scene. In fact, if the splash scene looked best at 60 fps and the bowling scene ran more smoothly at 30 fps (rather than jittering between 30 fps and 60 fps), you would add the frame rate script to both scenes, setting the target frame rate to 60 fps in the splash scene and to 30 fps in the bowling scene .

### Targeting Space

Besides the frame rate, you should also set a goal for the app size. You have to compete for room on iOS devices with songs, photos, videos, and all the other apps that users install. A smaller footprint makes the decision easier for a user to keep your app.

A smaller app also downloads in less time and loads faster (both the app load and level loads). Especially important, the App Store limits downloads over cellular connections to apps less than 50MB in size. You don’t want to miss out on impulse buys, so 50MB is a good app size target. This constraint is much easier to meet than the App Store’s original 20MB limit, but any app with significant content can still easily exceed that size (HyperBowl , with six lanes, is about 40MB).

## Profile the Game

Before you start optimizing, you need to know what to optimize. That’s where profiling, obtaining performance information about the game, comes in. You have options including viewing statistics displayed in the Editor, profiling the app itself, and even writing some of your own performance measurement code.

### Game View Stats

In the Editor, you have immediate access to performance-related information about the scene using the Stats overlay in the Game view (Figure [14-3](#A310332_2_En_14_Chapter.html#Fig3)).

![A310332_2_En_14_Fig3_HTML.jpg](Images/A310332_2_En_14_Fig3_HTML.jpg)

Figure 14-3.

The Game view statistics overlay

When you click Play, you’ll see these statistics update while the game is in progress. This information is convenient, but it’s no substitute for profiling the actual app.

### The Build Log

Minimizing your app size is not as important as it used to be when exceeding 20MB meant that an app could only be downloaded onto a device over Wi-Fi, not 3G wireless. Now the limit is a more generous 50MB, but this is still easy to reach if you’ve got a big game, and smaller means faster downloads and more people can fit your app into devices with less free storage (I always fill up my iPads fairly quickly).

You can see a breakdown of assets that went into your app by checking the Editor log after a build, as shown in Listing [14-2](#A310332_2_En_14_Chapter.html#Par20).

```
Textures      5.2 mb         16.9%
Meshes        17.4 kb         0.1%
Animations    0.0 kb          0.0%
Sounds        21.2 mb        69.1%
Shaders       0.0 kb          0.0%
Other Assets  19.1 kb         0.1%
Levels        10.7 kb         0.0%
Scripts       175.2 kb        0.6%
Included DLLs 4.1 mb         13.3%
File headers  16.6 kb         0.1%
Complete size 30.6 mb       100.0%
Used Assets, sorted by uncompressed size:
21.1 mb         69.0% Assets/Assets/Free/Assets/Sci-Fi_Ambiences/Sci-fi_AmbienceLoop1.wav
4.0 mb          13.1% Assets/Textures/LearnUnityCover.jpg
170.8 kb         0.5% Assets/Standard Assets/Skyboxes/Textures/Sunny3/Sunny3_up.tif
170.8 kb         0.5% Assets/Standard Assets/Skyboxes/Textures/Sunny3/Sunny3_right.tif
170.8 kb         0.5% Assets/Standard Assets/Skyboxes/Textures/Sunny3/Sunny3_left.tif
170.8 kb         0.5% Assets/Standard Assets/Skyboxes/Textures/Sunny3/Sunny3_front.tif
170.8 kb         0.5% Assets/Standard Assets/Skyboxes/Textures/Sunny3/Sunny3_back.tif
170.8 kb         0.5% Assets/Standard Assets/Light Flares/Sources/Textures/50mmflare.psd
170.8 kb         0.5% Assets/Barrel/Barrel_D.tga
31.2 kb          0.1% Assets/Assets/Free/Assets/8Bit/Coin_Pick_Up_03.wav
17.4 kb          0.1% Assets/Barrel/Barrel.fbx
17.3 kb          0.1% Assets/Substances_Free/Wood_Planks_01.sbsar
10.8 kb          0.0% Assets/Standard Assets/Skyboxes/Textures/Sunny3/Sunny3_down.tif
1.0 kb          0.0% Assets/Prefabs/BarrelPin.prefab
0.6 kb          0.0% Assets/Standard Assets/Light Flares/50mm Zoom.flare
0.3 kb          0.0% Assets/Standard Assets/Skyboxes/Sunny3 Skybox.mat
Listing 14-2.
Build Log
```

The log lists a breakdown of your app size by type of asset—textures, audio, meshes, scripts—and a list of the individual assets that went into it. Unfortunately, the size contributions are based on uncompressed sizes and not the final compressed asset sizes, but it still give you an idea of which assets are taking the most space.

The log also provides a way to find out which assets in your project are used or not used, via the handy search field in the Console app. For example, you have all your textures for FuguBowl in the Textures folder, so if you want to check which of those textures are used and which ones you can remove from the project, you would select Editor Log in the Console view menu, which should launch the Console app with the Editor log selected (Figure [14-4](#A310332_2_En_14_Chapter.html#Fig4)).

![A310332_2_En_14_Fig4_HTML.jpg](Images/A310332_2_En_14_Fig4_HTML.jpg)

Figure 14-4.

Filtering the build log using the Console app search field

In the search box you would type Textures to filter the display. Then you could compare the resulting list with the Textures folder in the Project view and see whether there’s anything in the Project view that didn’t end up in the build.

### Run the Built-in Profiler

After the Unity iOS build has generated its Xcode project, you have the option of activating the built-in profiler, which reports information similar to the Stats overlay in the Game view. To activate the built-in profiler, also referred to in the Unity documentation as the internal profiler, select the header file `iPhone_Profiler.h` in the Xcode project, listed inside the Classes folder, and change the value of ENABLE_INTERNAL_PROFILER from 0 to 1 (Figure [14-5](#A310332_2_En_14_Chapter.html#Fig5)).

![A310332_2_En_14_Fig5_HTML.jpg](Images/A310332_2_En_14_Fig5_HTML.jpg)

Figure 14-5.

Built-in profiler switch in the Unity Xcode project

Now when you click Play, the app will recompile with the built-in profiler enabled, and the debug area of Xcode will display statistics, as shown in Listing [14-3](#A310332_2_En_14_Chapter.html#Par26), after every 30 frames while the game is running.

```
iPhone Unity internal profiler stats:
cpu-player>    min: 39.8   max: 63.2   avg: 58.0
cpu-ogles-drv> min:  2.1   max:  3.7   avg:  2.4
cpu-present>   min:  0.7   max:  6.6   avg:  1.8
frametime>     min: 47.7   max: 72.2   avg: 64.5
draw-call #>   min:  40    max:  40    avg:  40     | batched:     0
tris #>        min:  5544  max:  5544  avg:  5544   | batched:     0
verts #>       min:  4358  max:  4358  avg:  4358   | batched:     0
player-detail> physx:  0.0 animation:  0.0 culling  0.0 skinning:  0.0 batching:  0.0 render: 58.0 fixed-update-count: 0 .. 0
mono-scripts>  update:  0.2   fixedUpdate:  0.0 coroutines:  0.1
mono-memory>   used heap: 405504 allocated heap: 524288  max number of collections: 0 collection total duration:  0.0
Listing 14-3.
Built-in Profiler Output on a Fourth-Generation iPod Touch
```

The first number to look at in the profile is the frametime, which is the total amount of time taken by a frame, in milliseconds. If you’re running at 60 fps, you should see an average of around 16.7 ms for the frametime. If the frametime is above 34, then you’re not even getting 30 fps. The line after the frametime is pretty important, too, as it tends to have a big impact on the frametime. Draw calls are distinct drawing operations. Generally, each rendered mesh incurs a draw call, and lighting and shadows on top of that can add more draw calls.

The player-detail line breaks down the time listed in the cpu-player line at the top. The player-detail time is apportioned among physx (physics), animation, culling (determining objects that don’t need to be rendered), skinning (updating the skin position on animated characters), batching (combining meshes that have the same materials so they are rendered with a single draw call), rendering, and the fixed update count.

The profile results in Listing [14-3](#A310332_2_En_14_Chapter.html#Par26), taken on a fourth-generation iPod touch, show a frametime not even close to 30 fps, much less 16 fps, and the player-detail is dominated by the render time. One thing that is known to have a big performance impact is dynamic shadows, so let’s disable shadows in your Light component (Figure [14-6](#A310332_2_En_14_Chapter.html#Fig6)).

![A310332_2_En_14_Fig6_HTML.jpg](Images/A310332_2_En_14_Fig6_HTML.jpg)

Figure 14-6.

Disabling shadows from the bowling scene light

Then perform a Build and Run operation again to start a new profile session (Listing [14-4](#A310332_2_En_14_Chapter.html#Par31)).

```
iPhone Unity internal profiler stats:
cpu-player>    min:  5.0   max:  7.6   avg:  5.7
cpu-ogles-drv> min:  3.1   max:  7.0   avg:  3.7
cpu-present>   min: 14.4   max: 42.9   avg: 28.1
cpu-waits-gpu> min: 14.4   max: 42.9   avg: 28.1
msaa-resolve> min:  0.0   max:  0.0   avg:  0.0
frametime>     min: 28.5   max: 55.5   avg: 39.5
draw-call #>   min:  29    max:  29    avg:  29     | batched:    10
tris #>        min:  4584  max:  4584  avg:  4584   | batched:  3420
verts #>       min:  3712  max:  3712  avg:  3712   | batched:  2750
player-detail> physx:  0.0 animation:  0.0 culling  0.0 skinning:  0.0 batching:  1.9 render:  3.1 fixed-update-count: 0 .. 0
mono-scripts>  update:  0.2   fixedUpdate:  0.0 coroutines:  0.1
mono-memory>   used heap: 401408 allocated heap: 524288  max number of collections: 0 collection total duration:  0.0
Listing 14-4.
Built-in Profiler Output After Removing Shadows
```

If you’re not running Unity iOS Pro or you turned off shadows earlier for performance reasons, then you’re already here. Anyway, the results are dramatic. Turning off shadows significantly reduces draw calls, and the frametime and render time are lower, although the frame rate is still below 30 fps.

### Run the Editor Profiler

The built-in profiler gives you a general idea of the bottlenecks in your game’s performance—whether it’s draw calls, physics computation, or script execution, for example. But it’s still a bit of a guessing game figuring out exactly what’s taking up the frametime. The Profiler can record the performance of a game run in the Editor or profile remotely, capturing performance data from a test device. To profile your bowling game running on a test device, you need to select the Development Build option in the Build Settings, which will enable the Autoconnect Profiler option (Figure [14-7](#A310332_2_En_14_Chapter.html#Fig7)).

![A310332_2_En_14_Fig7_HTML.jpg](Images/A310332_2_En_14_Fig7_HTML.jpg)

Figure 14-7.

Building with Autoconnect Profiler enabled

Perform a Build and Run operation, and the Profiler window should appear automatically, running a profiling session of the game running on the device (Figure [14-8](#A310332_2_En_14_Chapter.html#Fig8)).

![A310332_2_En_14_Fig8_HTML.jpg](Images/A310332_2_En_14_Fig8_HTML.jpg)

Figure 14-8.

The Profiler window

The topmost bar displays the computer processing unit (CPU) usage. This particular profile indicates you’re hovering around 30 fps. The area below shows a breakdown of the CPU usage by function calls (Figure [14-9](#A310332_2_En_14_Chapter.html#Fig9)).

![A310332_2_En_14_Fig9_HTML.jpg](Images/A310332_2_En_14_Fig9_HTML.jpg)

Figure 14-9.

The Profiler breakdown of CPU usage

You can expand the items to reveal the nested function calls. Figure [14-9](#A310332_2_En_14_Chapter.html#Fig9) indicates you’re spending a lot of time in your UnityGUI functions, both the scoreboard and the pause menu (this profile is taken with the pause menu up).

### Manually Connect the Profiler

If for some reason the Profiler doesn’t start automatically, you can open the Profiler from the Window menu (Figure [14-10](#A310332_2_En_14_Chapter.html#Fig10)).

![A310332_2_En_14_Fig10_HTML.jpg](Images/A310332_2_En_14_Fig10_HTML.jpg)

Figure 14-10.

The Window menu item to display the Profiler

The Profiler is actually a view, which is why it’s listed among the other views in the Window menu. But, like the Asset Store, the Profiler takes up so much screen space it is best left by itself in a floating window.

Once the Profiler window is up, it will automatically start recording the game in the Editor (even if the game is not actually running), so click the Record button to stop it. Then select your iOS test device in the Active Profiler window (Figure [14-11](#A310332_2_En_14_Chapter.html#Fig11)) and click the Record button again to start profiling.

![A310332_2_En_14_Fig11_HTML.jpg](Images/A310332_2_En_14_Fig11_HTML.jpg)

Figure 14-11.

Selecting an iOS device as the active profiler

### Add a Frame Rate Display

Enabling the built-in profiler or attaching to the Editor Profiler over the network is inconvenient just to see the frame rate. That’s where an on-screen frame rate display would come in handy. That way you can just toggle it on while you’re on the sofa watching TV and play-testing your game (at least that’s what I do).

For an on-screen frame rate display, you could use a Unity UI Image component like you did with the bowling scoreboard. However, there’s another way to display 2D text on screen, and that’s by using the UI Text component. You can create a GameObject with a UI Text component already attached using the GameObject menu on the menu bar or using the Create menu in the Hierarchy view (Figure [14-12](#A310332_2_En_14_Chapter.html#Fig12)).

![A310332_2_En_14_Fig12_HTML.jpg](Images/A310332_2_En_14_Fig12_HTML.jpg)

Figure 14-12.

Creating a UI Text GameObject

Let’s go ahead and create that UI Text GameObject and name it FPS (Figure [14-13](#A310332_2_En_14_Chapter.html#Fig13)).

![A310332_2_En_14_Fig13_HTML.jpg](Images/A310332_2_En_14_Fig13_HTML.jpg)

Figure 14-13.

The FPS GameObject with a UI Text component

Like the UI Texture you used for your secondary splash screen, UI Text is positioned on the screen in normalized coordinates, where the bottom-left corner is 0,0 and the-top right is 1,1, specified in the Transform x and y values of the GameObject. Set the x and y positions of your frame rate display to -.1 each, placing it near the bottom-left corner, and change the text value to FPS.

Don’t bother to customize the font because the frame rate display is only for development use and you won’t activate it in the released app (although in HyperBowl, the frame rate display can be activated by users so they can report performance issues). The default size value of 0 indicates the font size specified in the font’s import setting is used, but that’s pretty small, so let’s set it to a reasonably large size, 40. All this time, the GUIText is visible in the Game view, so you can see the effect of the size changes (Figure [14-14](#A310332_2_En_14_Chapter.html#Fig14)).

![A310332_2_En_14_Fig14_HTML.jpg](Images/A310332_2_En_14_Fig14_HTML.jpg)

Figure 14-14.

The Game view with a UI Text GameObject

Just as UI Image is a more convenient for displaying static textures than Unity UI, requiring no scripting, UI Text is a more straightforward way to display static text on the screen than Unity UI Image. But for your frame rate display, you do need to change the text in UI Text, and you can do that in a script, setting the text variable in the UI Text instance. Let’s implement your frame rate display script now. Create a new script, place it in the Scripts folder, and name it FuguFPS (Figure [14-15](#A310332_2_En_14_Chapter.html#Fig15)).

![A310332_2_En_14_Fig15_HTML.jpg](Images/A310332_2_En_14_Fig15_HTML.jpg)

Figure 14-15.

Creating a new FuguFPS.js script

Then add the Update function in Listing [14-5](#A310332_2_En_14_Chapter.html#Par48) to the script. The script’s Update callback sets the GUIText text variable, the text that is displayed on the screen, with the calculated frame rate. The calculation is simplistic, based on Time.delta time, which is the most recent frametime. Normally, frames per second would be calculated as 1/Time.deltaTime, but since Time.deltaTime is scaled by Time.timeScale, you can easily take that into account by using Time.timeScale/Time.deltaTime.

The text variable in GUIText is a String variable, so you need to convert the frame value to a String by calling the number’s ToString function (every built-in type has a ToString function). And then append the String “FPS” so you won’t wonder what those two numbers are on the screen!

```
#pragma strict
function Update()
{
if (Time.deltaTime>0) {
var fps:float = Time.timeScale/Time.deltaTime;
guiText.text  = fps.ToString("f0")+"FPS";
}
}
Listing 14-5.
The Complete Frame Rate Display Script FuguFPS.js
```

Now attach the script to the FPS GameObject. When you click Play in the Editor, you should see the frame rate display flicker among different numbers. And when you perform a Build and Run operation to a test device, you should see the frame rate display correspond to the numbers you got in the built-in profiler (Figure [14-16](#A310332_2_En_14_Chapter.html#Fig16)).

![A310332_2_En_14_Fig16_HTML.jpg](Images/A310332_2_En_14_Fig16_HTML.jpg)

Figure 14-16.

A frame rate display running on a test device

Just remember, you should deactivate the FPS GameObject before you release this app!

## Optimize Settings

The proper way to optimize is to methodically go through the bottlenecks starting with the largest, profiling the improvements at each stage until you achieve your target. But for the purposes of getting through a number of optimization procedures in this chapter, you’ll plow through a bunch of them in sequence.

### Quality Settings

Let’s start by tweaking the global settings that relate to your optimization effort. The settings that are most likely to affect performance are the quality settings, which control the visual quality of the game (Figure [14-17](#A310332_2_En_14_Chapter.html#Fig17)).

![A310332_2_En_14_Fig17_HTML.jpg](Images/A310332_2_En_14_Fig17_HTML.jpg)

Figure 14-17.

Optimizing the quality settings

You’re going for speed, so set the current iOS quality to Fastest and then adjust the settings from there. Zero pixel lights is fine for the lighting setup. If you had a spotlight, then the lack of a pixel light would be obvious.

You don’t want to force all textures to half-resolution, though. Relying on the Texture Import settings to limit the resolution on a case-by-case basis gives you more flexibility. So, let’s set Texture Quality to Full Res.

Similarly, you’ll want to change the setting for Anisotropic Textures from none to per-texture. Anisotropic Textures are textures that compensate for being viewed at an angle, which is appropriate for a texture used for a road in a racing game, for example. Like texture resolution, anisotropy can be adjusted for a texture in its import settings.

Multisample Anti Aliasing (full-screen smoothing) is an expensive operation, as you would expect a full-screen per-pixel operation would be.

You don’t have any particle systems in this game, so the Soft Particles setting doesn’t matter, but it’s another pixel-dominated operation (these types of features are often called fill-rate limited).

Shadows are disabled, so all the shadow parameters below it are irrelevant, but if shadows were enabled, the two key properties are Shadow Resolution and Shadow Distance. Both properties affect the shadow’s resolution; increasing the Shadow Resolution naturally allows for a more sharply defined shadow at the expense of more memory consumed for a larger shadow map, and much like the improved depth buffer precision of a shortened camera frustum, a shorter Shadow Distance value reduces the need for a large shadow map texture. Reducing the Shadow Resolution value saves space while introducing more jaggy shadows, while reducing the Shadow Distance value potentially reduces the number of objects involved in the shadows.

### Physics Manager

Rigidbody components go to sleep when they’re at rest to avoid unnecessary physics computation. “At rest” means the Rigidbody’s motion has stopped moving and rotating. You can specify the threshold for Sleep Velocity and Sleep Angular Velocity in the Physics Manager (Figure [14-18](#A310332_2_En_14_Chapter.html#Fig18)), available under the Settings portion of the Edit menu.

![A310332_2_En_14_Fig18_HTML.jpg](Images/A310332_2_En_14_Fig18_HTML.jpg)

Figure 14-18.

Optimizing the PhysicsManager

At the bottom of the Physics Manager, you can also specify which layers of GameObjects can collide with one another. This doesn’t make a difference with your bowling game because all of your colliding objects are in the Default layer. But to demonstrate this point, let’s modify the collision table to only allow GameObjects in the Default layer to collide with other GameObjects in the Default layer.

### Time Manager

Because the physics updates take place at fixed intervals, you can reduce physics computation time by increasing the fixed timestep in the Time Manager (Figure [14-19](#A310332_2_En_14_Chapter.html#Fig19)). Ideally, the fixed timestep should be set as high as possible without degrading the physics simulation. For now, you’ll increase the fixed timestep from 0.02 to 0.03.

![A310332_2_En_14_Fig19_HTML.jpg](Images/A310332_2_En_14_Fig19_HTML.jpg)

Figure 14-19.

Optimizing the Time Manager

The property immediately below Fixed Timestep is also related to physics updates: Maximum Allowed Timestep. This value caps how much time can be spent on physics updates in each frame. This prevents the runaway situation where the fixed updates take up a lot of time and increase the frametime, thus including more fixed updates in the next frame and so on. Lowering the Maximum Allowed Timestep value minimizes the Fixed Update impact on the frametime but risks degrading the physics simulation.

### Audio Manager

There is also an optimization trade-off in how the audio is played. In the Audio Manager (Figure [14-20](#A310332_2_En_14_Chapter.html#Fig20)), you can specify the audio latency, which is how much delay is allowed before the sound is played.

![A310332_2_En_14_Fig20_HTML.jpg](Images/A310332_2_En_14_Fig20_HTML.jpg)

Figure 14-20.

Optimizing the Audio Manager

For games where responsive sound is important (e.g., the collision sounds in your bowling game), selecting “Best latency” (i.e., the lowest latency) is appropriate.

The Other Settings section is filled with optimization options; they’re not just in the Optimization portion (Figure [14-21](#A310332_2_En_14_Chapter.html#Fig21)).

![A310332_2_En_14_Fig21_HTML.jpg](Images/A310332_2_En_14_Fig21_HTML.jpg)

Figure 14-21.

Optimizing other settings in player settings

#### Static Batching

As I pointed out in the first profiling session, draw calls tend to have a big impact on performance. Static batching reduces draw calls by combining meshes at build time that can be rendered together in the same draw call. For meshes to be combined, their GameObjects must be marked as static in the Inspector view, and they must share the same material or set of materials.

Static batching is a flexible system; it remembers the distinct identity of each GameObject, so a GameObject with a statically batched mesh can still be deactivated at will and is still subject to culling.

Your bowling game has only one static mesh, the floor, so enabling static batching won’t make any difference here. In general, static batching provides a good performance boost but at the expense of taking up more memory because it essentially copies each batched mesh .

#### Dynamic Batching

The next option, dynamic batching, takes place at runtime. Like static batching, it requires meshes to share materials before they can be combined, but dynamic batching can handle moving objects because it checks every frame if it needs to rebatch objects. For example, the bowling pins in your game can be batched because they all obviously have the same material. They were instantiated from the same prefab, after all. However, the bowling ball cannot be batched with any of the pins or the bowling surface.

Batching does take some time, which is why it shows up in the built-in profiler, but, like static batching, it generally is a big win for performance.

#### Accelerometer Frequency

Accelerometer Frequency is set to 60 samples per second by default. You can save some processing time by lowering that frequency to 15, which is sufficient for your shake detection code. If you don’t use the accelerometer at all in an app, set it to 0.

#### Stripping Level

Three cumulative levels are available: String Assemblies, Strip Bytecode, and “Use micro mscorlib.” Let’s choose the latter, which is the most aggressive optimization and includes the other two. The micro mscorlib is a custom, slimmed-down version of the core Mono library, which may be incompatible with additional .NET libraries you might import, but that’s not an issue for this game.

#### Script Call Optimization

The Script Call Optimization level determines how calls to native code are made. The default setting, Slow and Safe, incurs overhead, so let’s set it to Fast with No Exceptions. As the label signifies, this setting is faster but will fail if the native code throws an exception (essentially, an error that is expected to be handled by calling code).

#### Optimize Mesh Data

The Optimize Mesh Data option removes at build time any unnecessary per-vertex mesh information. If a mesh is not using a bump-map shader, then the tangents can be removed, and if the mesh is using an unlit shader, then normals can be removed. This saves space and facilitates batching since batching copies all of that mesh data. Of course, if you’re going to change the shader of a mesh at runtime via a script to a shader that requires more per-vertex information than the original shader, you shouldn’t use this option.

## Optimizing GameObjects

After adjusting the quality settings, Physics Manager, and Time Manager, you’re ready to tweak individual GameObjects.

### Camera

Cameras are a focal point (no pun intended) of optimization because they dictate what is rendered and how it is rendered. The camera frustum automatically provides one source of optimization. Any GameObject that resides entirely outside the frustum is not visible to the camera, so no attempt is made to render the GameObject. This optimization is called frustum culling and also benefits animation and particle systems. The system has no need to play an animation or simulate particles when there’s no one to see it.

Tip

The fastest way to render a GameObject is to not render it all.

Therefore, adjusting the camera’s frustum so it includes fewer objects in a scene is one source of optimization. You can narrow the frustum by lowering its field of view, and you can shorten the frustum by decreasing the far distance value, bringing the far plane closer to the camera (Figure [14-22](#A310332_2_En_14_Chapter.html#Fig22)).

![A310332_2_En_14_Fig22_HTML.jpg](Images/A310332_2_En_14_Fig22_HTML.jpg)

Figure 14-22.

Optimizing the Main Camera

For example, the default value of 1000 for the far plane value in the Main Camera is overkill, considering the bowling floor is much smaller. Setting the far value to 100 still leaves everything visible (Unity skyboxes are conveniently visible independent of the frustum).

Minimizing the far plane value has the additional benefit of storing a smaller range of values in the depth buffer when rendering. This avoids the problem of z-fighting, where an object that is barely behind another seems to poke through the one in front because of the limited numerical precision in the depth buffer.

Another way to cull GameObjects from rendering by a camera is to specify that it renders only GameObjects in a certain set of layers, listed in its culling mask. This is really a matter of correctness—you want to make sure the camera is rendering only what it’s supposed to be rendering and not, say, HUD elements that are already rendered by a separate HUD camera. But it does affect performance. I once had a bug where the Main Camera was unintentionally rendering GUI elements, but it was far enough in the distance that I didn’t notice it.

Each camera also has an eventMask variable that is similar to its culling mask (or, equivalently, its cullingMask variable), but instead of specifying what layers are rendered by the camera, the eventMask variable determines what layers receive onMouse events from the camera. Minimizing the layers present in eventMask can reduce the overhead of onMouse processing, but as it’s not visible in the Inspector view, that variable has to be set from a script.

The last camera optimization to make is to specify the render path. There are two render paths available on iOS: Vertex Lit and Forward Rendering (deferred rendering is not available for mobile platforms). Forward Rendering is more flexible and powerful than Vertex Lit. For example, the Vertex Lit path won’t display bump-mapping. But in this chapter, you’re aggressively going for a high frame rate, so let’s set the render path to Vertex Lit.

### Lights

You already disabled shadows on your light, which is a big performance improvement because it reduces draw calls. And lighting is already influenced by the quality settings, which specify no pixel lights and disabled shadows (Figure [14-23](#A310332_2_En_14_Chapter.html#Fig23)).

![A310332_2_En_14_Fig23_HTML.jpg](Images/A310332_2_En_14_Fig23_HTML.jpg)

Figure 14-23.

Optimizing the light

But you can perform some additional adjustments on the light similar to your camera tweaks. Like the camera, lights have a specified range, which you can minimize to reduce the number of lit GameObjects. Lights also have a culling mask, which, as in the case of the Main Camera, can be set to the Default layer, but with judicious assignment of layers you can reduce unnecessarily lit GameObjects.

### Pins

In pursuit of speed, you’ve chosen the simplest lighting and the simplest render path. You can also simplify the shaders you’re using for each mesh. The bowling pins comprise most of the objects in your scene, so let’s modify the BarrelPin prefab first (Figure [14-24](#A310332_2_En_14_Chapter.html#Fig24)).

![A310332_2_En_14_Fig24_HTML.jpg](Images/A310332_2_En_14_Fig24_HTML.jpg)

Figure 14-24.

Optimizing the BarrelPin prefab

In the Project view, select the Barrel GameObject in the prefab and click the Shader selector in the Inspector view. There are many simpler shaders than Bumped Specular; anything with less than two materials is likely to be faster, and Vertex Lit will be more efficient than any of the pixel shaders. There is even a set of shaders specifically implemented for mobile devices, including Mobile/Vertex Lit. But there’s one that is even better for your situation, a Mobile/Vertex Lit shader implemented specifically for use only with directional lights. So, let’s choose that one, Mobile/Vertex Lit (Only Directional Light).

#### Floor

The Floor GameObject has a few component properties that can be tweaked for optimization (Figure [14-25](#A310332_2_En_14_Chapter.html#Fig25)). Starting with the MeshRenderer component, you have the Cast Shadows and Receive Shadows options enabled. Although you have shadows disabled in the light and in the quality settings, you should pay attention to the per-object settings just in case you enable shadows in the future. If shadows were enabled, you would want shadows cast on the floor, so Receive Shadows should be enabled. But because you don’t expect the floor to cast a shadow (at least not on anything you can see), let’s disable the Cast Shadows option. For performance, the fewer shadow casters, the better.

![A310332_2_En_14_Fig25_HTML.jpg](Images/A310332_2_En_14_Fig25_HTML.jpg)

Figure 14-25.

Optimizing the floor

Turning to physics, there is another opportunity for optimization. Although you have primitive colliders for the bowling pins and bowling ball, your floor has a MeshCollider component. Because the floor is a flat and square surface, you could use a BoxCollider instead. Replacing it is a simple process: when you add a BoxCollider component to the floor, either using the Add Component button in the Inspector view or using the Component menu on the menu bar, Unity asks if you want to replace the existing MeshCollider (say yes), and the new BoxCollider component will automatically be sized to fit the floor mesh.

As with the bowling pins, you could also change the shader from the fairly complex Bumped Specular shader to Mobile/VertexLit (Only Directional Lights), as you did with your bowling pin prefab. But the floor is a special case because it’s flat. The lighting across its surface is uniform. So, you can use the Unlit Texture shader and avoid any lighting calculations at all.

#### Ball

The last GameObject you will optimize is the ball. It’s already using a SphereCollider component, so the one thing you’ll change is the shader. Like the bowling pins, the ball is going to move, so let’s use a lit shader, in particular the same Mobile/VertexLit (Only Directional Light) shader that you selected for the pins. However, the ball is currently using the default diffuse material that was automatically assigned when you created the ball, and you can’t change the shader on that material. So, you’ll need to assign a different material to the ball.

You can create a new material in the same way you created the other types of assets. In the Project view, bring up the Create menu and select Material (Figure [14-26](#A310332_2_En_14_Chapter.html#Fig26)).

![A310332_2_En_14_Fig26_HTML.jpg](Images/A310332_2_En_14_Fig26_HTML.jpg)

Figure 14-26.

Creating a new material

Place the new material in the Materials folder and name it Ball. Select it (Figure [14-27](#A310332_2_En_14_Chapter.html#Fig27)), and in the Inspector view you can adjust its shader, selecting Mobile/VertexLit (Directional Light).

![A310332_2_En_14_Fig27_HTML.jpg](Images/A310332_2_En_14_Fig27_HTML.jpg)

Figure 14-27.

Setting the shader for a new material

Then drag the Ball material onto the Ball GameObject, and you’re all set (Figure [14-28](#A310332_2_En_14_Chapter.html#Fig28)).

![A310332_2_En_14_Fig28_HTML.jpg](Images/A310332_2_En_14_Fig28_HTML.jpg)

Figure 14-28.

Optimizing the ball

Both of the Mobile/VertexLit shaders will display a white color if no texture is supplied. If you want the ball to have a color other than white, you would use the standard Vertex Lit shader, which allows you to specify a Main Color and Specular (shininess) Color .

## Optimize Assets

It’s easy to overlook the fact that a lot of optimization can take place when the asset is imported, and you have control over this in the import settings for each asset.

### Textures

Your textures are already imported with reasonable default settings, but let’s look at a couple of textures so you can understand those settings. In the Project view, let’s look in the Textures folder and compare the cat texture you used for the example scene in Chapter [3](../Text/A310332_2_En_3_Chapter.html) (Figure [14-29](#A310332_2_En_14_Chapter.html#Fig29)) and the texture you used for the secondary splash screen in Chapter [12](../Text/A310332_2_En_12_Chapter.html) (Figure [14-31](#A310332_2_En_14_Chapter.html#Fig31)). The cat texture used the import settings corresponding to the Texture preset, while the splash texture used the import settings of the GUI preset. In both cases, switching from the preset to Advanced reveals the specific settings of the preset.

![A310332_2_En_14_Fig29_HTML.jpg](Images/A310332_2_En_14_Fig29_HTML.jpg)

Figure 14-29.

Import settings for a texture

The filter type determines how a pixel from the texture (or texel) is calculated for rendering purposes. Bilinear filtering averages the surrounding texels from the closest smaller mipmap, while trilinear filtering combines the bilinear filtering results from the closest larger mipmap and the closest smaller mipmap. Point sampling is the simplest and fastest filter, while trilinear filtering is the most expensive. Bilinear filtering is the middle ground and a reasonable default.

Mipmapping is usually appropriate, despite the increased memory usage. Besides improving the texture display, mipmapping allows the graphics processor to work with a smaller version of the texture, which can improve performance. For a texture used in a menu you’ll typically view the texture at just one size, and ideally at the texture’s original size, so mipmapping is unnecessary. That’s certainly true for the splash screen, especially when it isn’t even loaded into a scene.

Tip

For a texture with sharp details (e.g., one used as a street sign), it may be better to use point sampling or even disable mipmapping entirely to avoid blurring the texture.

The anisotropic (directionally dependent) filtering level compensates for the case where a texture is tilted with respect to the camera, such as the road surface in a racing game. In those cases, filtering uniformly across a texture in both directions may not look right. For regular textures in a scene, the anisotropic level should be chosen on a case-by-case basis. For UI textures, which are viewed face on from the camera, no anisotropic filtering is required.

Normally, textures are imported while taking into account gamma space, the nonlinear color space of screens. In general, this isn’t necessary for UI textures, so in those cases, the bypass sRGB option is selected. If it doesn’t look quite right, try both options.

For mipmapping , textures need to be square and have dimensions that are a power of two (POT), because mipmaps are generated at successively halved dimensions. For example, a 256 × 256 texture would have mipmaps at 128 × 128, 64 × 64, and so on. Even a nonmipmapped texture benefits from POT dimensions because that’s what the graphics hardware ultimately uses. Most textures designed for games have POT dimensions, but if they don’t, the texture’s import settings can automatically size the texture to the nearest POT. For GUI textures, you usually don’t want this. Your splash screen has None set for its POT scaling, and the resulting preview displays its original dimensions and NPOT. If you try to use it as a regular texture in the scene, Unity will issue a warning in the Console view that you’re using an NPOT texture in a non-GUI way and will incur a performance penalty.

The max texture size and compression level of the texture are listed in the preset settings and are specified independently of the presets. For regular textures, the default Max Size value of 1024 is reasonable (2048 × 2048 is the maximum supported by all devices), but it should be chosen on a case-by-case basis. For example, if a large texture has just one color, you might as well make it a 2 × 2 texture. And if a texture is used only on a mesh that is small or far away on-screen, then there’s no reason to make it a big texture. For the splash screen, you set Max Size to 2048 so you don’t scale the texture to be smaller than the iPad Retina screen.

Tip

Setting the max scale to the maximum allowable and the compression level to TrueColor allows you to see the original format of the texture in the preview area of the import settings.

For a regular texture, the default compression level, 4-bit PVRTC (Power VR Texture Compression), is reasonable. A texture with less detail may work with the more aggressive 2-bit PVRTC. As with mipmapping, texture compression reduces the memory taken by texture mapping, and the graphics processor support for PVRTC provides an additional performance advantage. And as with mipmapping, particularly detailed textures may be degraded too much by compression, in which case leaving the texture at uncompressed 16-bit or uncompressed TrueColor (24-bit) may be better. For GUI textures, you might not want to compress the texture for that reason, or in the case of the splash screen that you assign in the player settings, you should leave it alone in its native format.

### Audio

You adjusted the DSP Buffer Size value in the Audio Manager for more responsive collision sounds, lowering the latency at some processing expense. As with textures, you can also adjust the import settings for AudioClips. Analogous to having two general categories of texture usage, for 3D and GUI, you also have 2D and 3D sounds; 3D sounds are associated with GameObjects, or at least a position in the 3D world, while 2D sounds are used for ambient audio or music (like the song you added to your dance scene in Chapter [5](../Text/A310332_2_En_5_Chapter.html)).

Let’s compare a couple of your bowling sounds: the pin collision sound (Figure [14-30](#A310332_2_En_14_Chapter.html#Fig30)) and the ball-rolling sound (Figure [14-31](#A310332_2_En_14_Chapter.html#Fig31)). They’re both sounds that emanate from a position in the world and even move with respect to the Main Camera (or rather, more precisely, they move with respect to the AudioListener component attached to the Main Camera). Thus, they’re both marked as 3D sounds in their import settings. 3D sounds should be mono, not stereo. Fortunately, the original audio files for both of these sounds are mono, so you don’t have to select “Force to mono.”

![A310332_2_En_14_Fig31_HTML.jpg](Images/A310332_2_En_14_Fig31_HTML.jpg)

Figure 14-31.

Optimizing the import settings for the ball-rolling sound

![A310332_2_En_14_Fig30_HTML.jpg](Images/A310332_2_En_14_Fig30_HTML.jpg)

Figure 14-30.

Optimizing the import settings for the pin collision sound

The collision sound is fairly small, so let’s leave it uncompressed to avoid the processing involved in uncompressing the sound and to maximize the quality of the playback. The rolling sound is a larger sound file, so let’s compress it and leave it compressed in memory. Because this is the only compressed sound and iOS has hardware support for decoding one compressed sound at any time, you’re not incurring any software decoding overhead. However, if you also had ambient sound or music playing in the scene, chances are that would be a much larger sound file and you would want to compress that instead.

Tip

Import uncompressed sounds to allow flexibility in deciding which sounds to compress in the Editor. There’s no reason to import compressed audio unless you’ve found a compression tool that gives you better quality than Unity’s compression.

With compression selected, the gapless looping option becomes available. That forces the compression to process the end of the audio clip so it can loop seamlessly, which is what you want, since you’re playing this sound in a loop.

For compressed audio, there is one other load option besides “Uncompress on load” and “Compressed in memory” called “Stream from disc.” There is some additional overhead in streaming from disc, but that’s a good option for a scene that plays multiple songs in succession, in which case it would be prohibitive to keep all of those songs in memory.

### Meshes

Most of the mesh-related import settings (Figure [14-32](#A310332_2_En_14_Chapter.html#Fig32)) that affect optimization have an impact principally on memory usage.

![A310332_2_En_14_Fig32_HTML.jpg](Images/A310332_2_En_14_Fig32_HTML.jpg)

Figure 14-32.

Optimizing the import settings for a mesh

The first is Mesh Compression, which reduces the amount of space and memory used for a mesh by reducing the numerical precision (i.e., number of bits used to represent the vertices, normals, texture coordinates, and tangents). This can also reduce the visual quality, so it’s best to experiment and find the maximum compression level that doesn’t degrade the appearance. The available levels are low, medium, high, and none. The default is none.

Listed below Mesh Compression is Read/Write Enabled. This is by default on, creating a copy of the mesh that can be modified. But if you don’t have any scripts that read or write mesh variables, such as vertices and normals, and you usually don’t need any, then you can leave this option off.

Optimize Mesh, not to be confused with the Mesh Optimization option in the player settings, offers some performance improvement by optimizing the arrangement of the mesh’s triangle list.

Leave Generate Colliders off if you’re not going to use a Mesh collider. In fact, it’s not a bad idea to leave this option off as a rule, because you can always add a Mesh collider manually if you need one, but it’s often preferable to add one or more primitive colliders instead.

If you know the shader for this mesh will not require normals or tangents, you can set the Normals and Tangents options, respectively, to none. But if you have Mesh Optimization selected in the Player Settings, unused vertex data will be removed at build time anyway.

## Optimize Scripts

The usual tips for optimizing code apply to scripting in Unity. For example, you’ve already made a point of calling the magnitudeSquared function of Vector3 instead of magnitude to avoid its internal square root operation. But I’ll describe some of the less obvious ways to improve script performance or use scripts to improve performance in the following sections.

### Cache GetComponent

In general, it’s a good idea to assign a value to a variable if you’re going to calculate that value more than once, whether it’s a math expression, the result of a function call, or even accessing the value of an instance or static variable, which might in fact be internally performing a function call.

This is the case with the component shortcut variables you’ve been accessing in your scripts like transform, audio, and rigidbody, each of which results in a call to GetComponent that searches among all the components attached to the GameObject for a component with the matching type.

So, any time you’re going to reference a component’s shortcut variable more than once in a script, whether it’s listed more than once in the script or referenced repeatedly in callback like Update (especially if the shortcut is referenced in a loop with many iterations), you should assign the component to a variable early on. Listing [14-6](#A310332_2_En_14_Chapter.html#Par123) shows how to apply this optimization to your FuguReset script.

```
#pragma strict
private var startPos:Vector3;
private var startRot:Vector3;
// for performance
private var trans:Transform = null;
private var body:Rigidbody = null;
function Awake() {
// cache the Transform reference
trans = transform;
body = rigidbody;
// save the initial position and rotation of this GameObject
startPos = trans.localPosition;
startRot = trans.localEulerAngles;
}
function ResetPosition() {
// set back to initial position
trans.localPosition = startPos;
trans.localEulerAngles = startRot;
// make sure we stop all physics movement
if (body != null) {
body.velocity = Vector3.zero;
body.angularVelocity = Vector3.zero;
}
}
Listing 14-6.
Optimized FuguReset.js Script
```

The original version of `FuguReset.js` referenced two component shortcut variables: transform and rigidbody. So, you add two private variables, named trans and body, to reference those components and initialize trans and body in the Awake callback.

This cleanup will be applicable to many of your scripts because you commonly reference transform, rigidbody, and audio. I won’t list all of the revised code, but the updated scripts are available in the project files for this chapter on [`www.apress.com/9781484231739`](http://www.apress.com/9781484231739) .

### UnityGUI

The detailed profile results indicate that UnityGUI takes up a significant portion of your frametime. One source of slowdown in UnityGUI is the overhead of using GUILayout to automatically place GUI elements. You don’t use GUILayout in the scoreboard, so you can add an Awake callback that sets useGUILayout to false in the FuguBowlScoreboard script (Listing [14-7](#A310332_2_En_14_Chapter.html#Par127)).

```
function Awake() {
useGUILayout = false;
}
Listing 14-7.
Setting useGUILayout to False in FuguBowlScoreboard.js
```

The pause menu still uses GUILayout , but because the menu is only up when the game is paused and won’t affect gameplay, its performance is less of a concern.

### Runtime Static Batching

Dynamic batching can combine similar meshes that weren’t candidates for static batching at build time because they’re moving or happened to be created during the game. But dynamic batching has to constantly update the batching, which is why the batching time is listed in the profile results. This rebatching seems like a waste if the batched objects are moving together or if they are not moving but were instantiated at runtime. Also, since batching takes memory, there is an upper limit on the amount of dynamic batching that will occur (the Unity documentation currently says 30,000 vertices, but that is subject to change).

Fortunately, there is a class called StaticBatchingUtility that allows you to perform static batching at runtime by calling a Combine function. Listing [14-8](#A310332_2_En_14_Chapter.html#Par131) shows a really simple script that just calls StaticBatchingUtility.Combine on the script’s GameObject.

```
#pragma strict
function Start() {
StaticBatchingUtility.Combine(gameObject);
}
Listing 14-8.
Script for Statically Batching an Object Hierarchy
```

Although simple, the script is fully functional. For example, in the rotating cube scene, if you dragged this script onto the main rotating cube that has two child cubes (and disabled the individual rotation scripts of the child cubes), all three cubes would be statically batched together when the scene starts playing. The batched GameObjects don’t have to be in a hierarchy together. StaticBatchingUtility.Combine is an overloaded function, and with another version it takes two arguments: an array of GameObjects to batch together and a GameObject designated as the parent.

### Share Materials

For batching to take place, whether statically, dynamically, or through a call to StaticBatchingUtility.Combine, the meshes to be batched must share the same material or set of materials. But for dynamic batching in particular, you need to be careful that you don’t break that sharing if you modify the material.

For example, the script in Listing [14-9](#A310332_2_En_14_Chapter.html#Par135) animates the texture of a material by constantly changing its texture offsets. This provides a scrolling texture effect; in HyperBowl, this script is used to animate textures for scrolling neon signs, drifting clouds in the sky, and flowing water. However, when the script modifies the material by calling its SetTextureOffset function, a new material is created if the original material happens to be shared.

```
#pragma strict
var speed:Vector2;
var materialIndex:int=0;
private var offset:Vector2;
private var material:Material;
function Start() {
offset=new Vector2(0,0);
material = renderer.materials[materialIndex];
}
function Update () {
var dtime:float = Time.deltaTime;
offset.x=(offset.x+speed.x*dtime)%1.0f;
offset.y=(offset.y+speed.y*dtime)%1.0f;
material.SetTextureOffset("_MainTex",offset);
}
Listing 14-9.
Script That Animates the Texture Coordinate Offsets of a Material
```

This makes sense, of course. If you create two GameObjects with the same material and decide you want to alter the appearance of one by modifying its material, you don’t necessarily want to make the same change to the other GameObject.

However, if you’re going to apply the same animation to all the GameObjects that are sharing the material, then there’s no reason to stop sharing the material. So, you can modify the script to access the sharedMaterials variable of the MeshRenderer instead of the materials variable. Accessing sharedMaterials (or sharedMaterial if you assume only one material on the GameObject) means you really do want to change the shared material and avoids creating a new one, thus preserving eligibility for batching. To handle both shared and nonshared material cases, let’s add a public variable that allows you to specify whether the animation breaks material sharing, and let’s use that value to determine whether to modify renderer.materials or renderer.sharedMaterials (Listing [14-10](#A310332_2_En_14_Chapter.html#Par138)).

```
#pragma strict
var speed:Vector2;
var materialIndex:int=0;
var shared:boolean = true;
private var offset:Vector2;
private var material:Material;
function Start() {
offset=new Vector2(0,0);
if (shared) {
material = renderer.sharedMaterials[materialIndex];
} else {
material = renderer.materials[materialIndex];
}
}
function Update () {
var dtime:float = Time.deltaTime;
offset.x=(offset.x+speed.x*dtime)%1.0f;
offset.y=(offset.y+speed.y*dtime)%1.0f;
material.SetTextureOffset("_MainTex",offset);
}
Listing 14-10.
Texture Animation Script with Option to Share Material
```

An additional, although less significant, optimization is available when using this script to animate a shared material. It would be redundant for every GameObject with the same material to run the same texture animation script; just one instance of the script is sufficient to drive the animation for everyone.

### Minimize Garbage Collection

Garbage collections can introduce noticeable pauses into a game. The most effective way to minimize those pauses is to minimize the necessity for garbage collection, and that can be accomplished by minimizing the number of objects that are eventually reclaimed by the collector. One technique is to keep objects in a pool so they can be reused, instead of letting them get garbage collected. You can also explicitly invoke the collector by calling System.GC.Collect. For example, you can add that call to the Awake callback in your bowling game controller to make sure a garbage collection takes place before the game starts, which will immediately clean up any unused objects left over from the previous scene (Listing [14-11](#A310332_2_En_14_Chapter.html#Par141)).

```
function Awake() {
player = new FuguBowlPlayer();
CreatePins();
System.GC.Collect();
}
Listing 14-11.
Adding a Call to the Garbage Collector in FuguBowl.js
```

## Optimize Offline

All of the changes you’ve made so far are quick optimizations that you can immediately try with a test run and profile to see their effect. Unity also integrates some commercial scene preprocessing tools that can greatly improve performance: Beast and Umbra. These won’t be used in this chapter because they can take quite a while to set up and run, and frankly, they won’t do much for your simple scene. But I’ll describe them briefly in the following sections.

### Beast

It’s difficult to add high-quality, real-time lighting without a heavy performance penalty. Each additional pixel light generates more draw calls, shadows are expensive, and the resulting visual quality is not as good as on the desktop much less a computer-generated scene that has been rendered offline. Beast solves both problems by generating lightmaps, which is precalculated lighting that is “baked” into textures. Of course, nothing is free—besides the setup and baking time, the lightmapped scene ends up with additional large textures that take up more memory, increase the app size, and lengthen the scene-loading time.

To generate lightmaps for the current scene, you would first have to mark all the nonmoving GameObjects, including lights, as static, because you can only precalculate lighting for fixed lights and surfaces. A lightmap is layered as a second texture over the mesh. Then you would bring up the Lighting window from the Window menu on the Editor menu bar (Figure [14-33](#A310332_2_En_14_Chapter.html#Fig33)).

![A310332_2_En_14_Fig33_HTML.jpg](Images/A310332_2_En_14_Fig33_HTML.jpg)

Figure 14-33.

Lighting window

In that window, you can adjust the lightmapping properties for each object in the Object pane and then switch to the Bake pane to set the lightmapping properties and initiate the lightmapping.

Note that dual lightmaps are unavailable on Unity iOS, because that feature requires deferred lighting, so having single lightmaps is the only option. Dynamic and static lighting won’t mix as well, especially with shadows.

### Umbra

I mentioned how each camera performs frustum culling, ignoring any GameObject that lies outside the camera’s view volume. Those not familiar with real-time computer graphics might assume that an obscured object (i.e., blocked from view by another) is also culled, but that’s not the case. All objects within the frustum are rendered, and the depth buffer is used to determine on a pixel-by-pixel basis which object is in front of another. Determining whether an object is positioned outside the frustum is a relatively straightforward calculation, but determining which objects are occluded requires some preprocessing. That’s where Unity’s integrating of Umbra as an occlusion culling tool comes in.

Adding occlusion culling to the scene is similar to the process of lightmapping the scene. You would mark nonmoving objects as static and then bring up the Occlusion Culling window from the Window menu on the Unity Editor menu bar (Figure [14-34](#A310332_2_En_14_Chapter.html#Fig34)).

![A310332_2_En_14_Fig34_HTML.jpg](Images/A310332_2_En_14_Fig34_HTML.jpg)

Figure 14-34.

Occlusion Culling window

From there, you can adjust per-object occlusion culling settings in the Object tab and then on the Bake tab specify the occlusion culling settings and then bake the occlusion culling data.

## Final Profile

Now that you’ve completed an optimization pass through your project, it’s time to profile the game again (Listing [14-12](#A310332_2_En_14_Chapter.html#Par151)), although if you want to be methodical, you would have to run a profile after each change to see its effect.

```
iPhone Unity internal profiler stats:
cpu-player>    min:  5.0   max:  9.7   avg:  6.1
cpu-ogles-drv> min:  3.4   max:  7.4   avg:  4.2
cpu-present>   min:  0.9   max: 10.5   avg:  4.2
cpu-waits-gpu> min:  0.9   max: 10.5   avg:  4.2
msaa-resolve> min:  0.0   max:  0.0   avg:  0.0
frametime>     min: 13.5   max: 22.1   avg: 16.6
draw-call #>   min:  30    max:  30    avg:  30     | batched:    10
tris #>        min:  4592  max:  4592  avg:  4592   | batched:  3420
verts #>       min:  3728  max:  3728  avg:  3728   | batched:  2750
player-detail> physx:  0.0 animation:  0.0 culling  0.0 skinning:  0.0 batching:  2.2 render:  3.4 fixed-update-count: 0 .. 0
mono-scripts>  update:  0.1   fixedUpdate:  0.0 coroutines:  0.0
mono-memory>   used heap: 389120 allocated heap: 524288  max number of collections: 0 collection total duration:  0.0
Listing 14-12.
Built-in Profile Results After Optimizations
```

The profile lists an average frametime of 16.6 ms, which is just about 60 fps. Target achieved! This profile was run on a fourth-generation iPod touch (equivalent to an iPhone 4), so depending on your device, your mileage may vary.

The player-detail line indicates the frametime is dominated by batching and rendering, so the graphics are the remaining bottleneck, and any more time spent on script optimization would not be time well spent. And if you decided that 30 fps isn’t so bad, then you’re in a good situation to add more content and features.

## Explore Further

I began this chapter by citing the perils of optimizing too early, at the risk of complicating things before you even got something working. But you should keep your performance target in mind from the beginning of development. Start simple (the Unity documentation recommends rendering no more than 40,000 vertices on the iPhone 3GS), get something working, optimize enough to meet your performance target, continue adding content and functionality, and stop and optimize every time the game’s performance falls below your target. And remember to profile and optimize the bottlenecks. It will do you no good to optimize something that’s taking 10 percent of your frametime and leave alone something else that’s taking 50 percent.

### Unity Manual

In the Unity Manual, the section “Getting Started with iOS Development” has several pages called “Optimizing Performance in iOS” covering the player settings you adjusted to optimize builds, describing the built-in profiler, and showing how to optimize the build size.

The “Advanced” section documents the Profiler available with Unity Pro and also the offline optimization features mentioned: lightmapping and occlusion culling. The “Advanced” section includes many other pages related to optimization: “Optimizing Graphics Performance,” “Reducing File Size,” “Automatic Memory Management” (garbage collection), and “Shadows” (in particular, “Shadow Performance”). The “Asset Bundles” and “Loading Resources” pages explain how assets can be downloaded and brought into a scene on demand, which is a technique that can be used to manage the installed app size.

### Reference Manual

The Reference Manual describes all the components and assets you optimized. Among those components, you tweaked the camera frustum and visibility mask, adjusted the culling mask and distance for the light in addition to disabling its shadow, minimized the number of shadow casters and receivers per MeshFilter, simplified the shaders for each MeshRenderer, and replaced a MeshCollider with a Box collider. You looked at the import settings for the various asset types referenced by the components: Texture2D, AudioClip, Mesh, and Animation. The Reference Manual also documents the settings managers you customized, including Quality Settings, Physics Manager, Time Manager, and Audio Manager. You also used the GUIText component, sibling of the GUITexture you use for a splash screen, for your frame rate display.

### Scripting Reference

The “Scripting Overview” section of the Scripting Reference has a “Performance Optimization” page with some tips on how to write faster scripts. The one new function you learned in this chapter is the static function Combine in the StaticBatchingUtility class (Combine is the only function in that class), which you called to batch a hierarchy of GameObjects at runtime. The one new variable you learned in this chapter is the useGUILayout variable in the MonoBehaviour class, which when set to true provides a performance increase by telling the UnityGUI system that the current OnGUI callback will not be performing any automatic layout using the GUILayout functions.

The settings managers all have corresponding classes so that their settings can be queried and set with scripts. Because all of the settings managers except Render Settings are active for the entire game and not per scene, script access allows you to adjust settings for each scene. For example, you might define a quality level for each scene and add a script to each scene that loads the appropriate quality level. The classes corresponding to the settings managers discussed in this chapter include Quality Settings, Audio Settings, Physics, and Time.

You also used the Time class, evaluating its timeScale and deltaTime variables and setting the text variable of a GUIText for your rudimentary frame rate display.

### Asset Store

The Asset Store lists a few profiling systems (search for profiler) and some object pooling systems (search for pool), such as PoolManager, Smart Pool, and Pooling Manager.

### On the Web

The Unity wiki at [`http://wiki.unity3d.com/`](http://wiki.unity3d.com/) has some performance tips under its “Tips, Tricks and Tools” section and several performance-related scripts. In particular, the “FramesPerSecond” page lists several frame-rate display scripts more sophisticated than the one you created in this chapter (for example, they calculate the frame rate from a series of frames for a steadier display).

The Umbra occlusion culling system is developed by Umbra Software at [`http://umbrasoftware.com/`](http://umbrasoftware.com/) . The Beast lightmapping system is an Autodesk product with a product page at [`http://gameware.autodesk.com/beast`](http://gameware.autodesk.com/beast) .

### Books

I’ve mentioned Real-Time Rendering ( [`http://realtimerendering.com`](http://realtimerendering.com) ) a couple of times already, but it’s really, really relevant to the graphics optimizations in this chapter. Just the treatment on mipmapping is a good read.

# 15. Where to Go from Here?

You’ve now completed this introduction to Unity and Unity iOS development, including downloading and installing Unity, developing a simple bowling game in the Unity Editor , getting it to run on iOS, and finally optimizing it to run with decent performance. Since not every Unity feature or game development topic was covered (this book would start to resemble a multivolume encyclopedia set!), I’ll wrap up with a chapter’s worth of “Explore Further.”

## More Scripting

I just scratched the surface with Unity scripting , not only in the variety of scripting languages offered and the extensive set of Unity classes but also what is scripted.

### Editor Scripts

If you feel there’s something missing in the Unity Editor , there’s a good chance you can add the feature by writing Editor scripts. These are scripts also written in JavaScript, C#, or Boo, but they’re located in the Editor folder in the Project view. Editor scripts have access to the Editor GUI, project settings, and project assets via a set of Editor classes, documented in the Scripting Reference under “Editor Classes.”

As an example, let’s create a menu on the Unity Editor menu bar with commands for activating and deactivating an entire hierarchy of GameObjects. In other words, the commands call SetActive on every GameObject in the hierarchy and are equivalent to clicking the check box of each GameObject in the Inspector view.

Before Unity 4, each GameObject just had one active state represented by the active check box in the Inspector view and the GameObject variable active. But starting with Unity 4, that check box in the Inspector view represents the local active state of the GameObject, settable in a script by calling SetActive and read from the GameObject variable activeSelf. The global or “really” active state depends on all its parents also being locally active. Thus, ensuring all GameObjects in a hierarchy are locally active allows you to deactivate the entire group by toggling the active state of the root GameObject. Clicking all those check boxes by hand can be laborious and error-prone, so a single command to perform that operation would be a boon, especially for developers upgrading projects to Unity 4.

The process of creating an Editor script is the same as creating a script intended for use in a scene, except the Editor script has to be located in the Editor folder. So, let’s create a new JavaScript in the Project view, name it FuguEditor, and place it in the Editor folder (Figure [15-1](#A310332_2_En_15_Chapter.html#Fig1)).

![A310332_2_En_15_Fig1_HTML.jpg](Images/A310332_2_En_15_Fig1_HTML.jpg)

Figure 15-1.

Creating a new Editor script

Now add the code in Listing [15-1](#A310332_2_En_15_Chapter.html#Par8) to the script.

```
#pragma strict
@MenuItem ("FuguGames/ActivateRecursively")
static function ActivateRecursively() {
if (Selection.activeGameObject !=null) {
SetActiveRecursively(Selection.activeGameObject,true);
}
}
@MenuItem ("FuguGames/DeactivateRecursively")
static function DeactivateRecursively() {
if (Selection.activeGameObject !=null) {
SetActiveRecursively(Selection.activeGameObject,false);
}
}
static function SetActiveRecursively(obj:GameObject,val:boolean) {
obj.SetActive(val);
for (var i:int=0; i<obj.transform.GetChildCount(); ++i) {
SetActiveRecursively(obj.transform.GetChild(i).gameObject,val);
}
}
@MenuItem ("FuguGames/ActivateRecursively", true)
@MenuItem ("FuguGames/DeactivateRecursively", true)
static function ValidateGameObject() {
return (Selection.activeGameObject !=null);
}
Listing 15-1.
Complete Listing for FuguEditor.js
```

The line @MenuItem ("FuguGames/ActivateRecursively") adds an ActivateRecursively item to the FuguGames menu on the Editor menu bar, creating the menu if it hadn’t already been created. The function following the @MenuItem line is invoked when that menu item is selected. ActivateRecursively checks whether a GameObject has been selected in the Editor, and, if so, then it passes that GameObject to the function SetActiveRecursively along with the value true. SetActiveRecursively in turn calls SetActive with the passed value true call for each GameObject in the hierarchy.

After that, a similar block of code adds a DeactivateRecursively item to the menu, differing only in name and in passing false instead of true to SetActiveRecursively.

Although you can check whether an object is selected before calling SetActiveRecursively, it’s better user interface design to deactivate a menu item if it’s not going to have any effect. You can add this by listing the MenuItem attributes again, but this time with an additional true argument, which indicates the next function is a static function that returns true only if the menu item should be active. Both ActivateRecursively and DeactivateRecursively use ValidateGameObject, which just checks whether a GameObject is selected in the Editor, to decide whether the menu item should be available or grayed out.

Once this script has been compiled, the new menu should appear, as shown in Figure [15-2](#A310332_2_En_15_Chapter.html#Fig2) (there may be a delay).

![A310332_2_En_15_Fig2_HTML.jpg](Images/A310332_2_En_15_Fig2_HTML.jpg)

Figure 15-2.

A menu added to the menu bar using an Editor script

This example has minimal user interface, just some menu items, but Editor scripts can create new windows and user interfaces using UnityGUI functions (and the Editor is actually implemented in UnityGUI, which is why occasional Editor errors show up as UnityGUI errors in the Console view). Editor scripts can also be used for batch processing, postprocessing assets after import, or preprocessing scenes before a build. I have simple Editor scripts that remove build files, show GameObject statistics, and even call the macOS say command so I can talk to myself.

### C#

I’ve focused on using Unity’s JavaScript in this book because, well, I had to pick one. JavaScript is more succinct than C#, at least for small scripts. Just compare the empty JavaScript (Listing [15-2](#A310332_2_En_15_Chapter.html#Par15)) and C# script (Listing [15-3](#A310332_2_En_15_Chapter.html#Par16)) generated by the Create menu in the Project view.

```
#pragma strict
function Start () {
}
function Update () {
}
Listing 15-2.
Newly Created JavaScript Script
```

```
using UnityEngine;
using System.Collections;
public class NewBehaviourScript : MonoBehaviour {
// Use this for initialization
void Start () {
}
// Update is called once per frame
void Update () {
}
}
Listing 15-3.
Newly Created C# Script
```

In the C# script, the class declaration is explicit, and you have to ensure that the class name matches the script name. At the top of the C# script, two namespaces that are commonly used in Unity scripts, UnityEngine and System.Collections, are explicitly imported. In JavaScript, they are implicitly imported.

A lot of JavaScript code will run in C# with little modification, but there are some syntactic differences. For example, variable declarations begin with <type> <name> instead of var <name>:<type>. Functions are defined the same way, starting with the return type and name instead of starting with function. There’s no reason to add `#pragma strict` to a C# file because C# is already strictly typed.

For small scripts, JavaScript is more convenient, but for large-scale Unity development, C# is arguably the more practical choice, given that it’s a better-documented language with industrial-strength testing because it’s the most popular Mono and .NET language. Furthermore, many Unity extension packages are written in C# (the Prime31 plug-ins, EZGUI, and NGUI among them), and mixing JavaScript and C# in Unity is inconvenient; if you have JavaScript code referencing C# scripts, those scripts must be placed in the Plugins or Standard Assets folders so they load earlier.

Another reason to use C# is the addition of namespace support for C# in Unity 5\. This feature allows you to dispense with prefixing class names to avoid conflicts. For example, Listing [15-4](#A310332_2_En_15_Chapter.html#Par21) shows a C# version of the FuguFrameRate script, where the class is defined within a namespace declaration.

```
using UnityEngine;
using System.Collections;
namespace Fugu {
sealed public class FrameRate : MonoBehaviour {
public int frameRate = 60;
void Awake () {
Application.targetFrameRate = frameRate;
}
}
} // end namespace
Listing 15-4.
C# Version of the FuguFrameRate Script
```

Because the class is inside the namespace Fugu, you can safely call the class just FrameRate and name the script FrameRate to match. If you had to refer to this class from another script, you would have to use the fully qualified name Fugu.FrameRate unless you added a using Fugu; statement at the top of the script. C# allows you to optionally prepend the class definition with sealed, which specifies that this class is not intended to be subclassed. A compiler error will result if you try to define a subclass of FrameRate. Java programmers will recognize this is the same as the Java final declaration.

In C#, coroutines must explicitly return type IEnumerator (which is in the System.Collections namespace and why it’s convenient to always import that namespace). Although Unity automatically adds a Start callback that returns void in newly created C# scripts, that Start callback can be turned into a coroutine by changing the return type from void to IEnumerator.

StartCoroutine can be called from any callback, so this technique has the advantage of providing a uniform way to invoke coroutines and makes it unnecessary to use callbacks directly as coroutines (and also makes it unnecessary to remember which callbacks can be used as coroutines). This is not a bad practice to apply in JavaScript, too.

You also can’t just call yield within a coroutine as you did in JavaScript. In C#, you have to call yield return null to equivalently yield for a frame.

Another difference is the syntax of anonymous functions. In our FuguGameCenter script, I passed unnamed success or failure functions in the form of function(success:boolean) {…}, but in C#, anonymous functions are known as lambda functions (dating back to terminology from the Lisp language, which used (lambda (success) …)) and is specified in the form (bool success) => {…} Listing [15-5](#A310332_2_En_15_Chapter.html#Par27) shows a C# version of the FuguGameCenter script.

```
using UnityEngine;
using System.Collections;
using UnityEngine.SocialPlatforms.GameCenter;
namespace Fugu {
sealed public class GameCenter : MonoBehaviour {
public bool showAchievementBanners = true;
// Use this for initialization
void Start () {
#if UNITY_IPHONE
Social.localUser.Authenticate ( (bool success) => {
if (success && showAchievementBanners) {
GameCenterPlatform.ShowDefaultAchievementCompletionBanner(showAchievementBanners);
Debug.Log ("Authenticated "+Social.localUser.userName);
}
else {
Debug.Log ("Failed to authenticate "+Social.localUser.userName);
}
}
);
#endif
}
static public void Achievement(string name,double score) {
#if UNITY_IPHONE
if (Social.localUser.authenticated) {
Social.ReportProgress(name,score, (bool success) => {
if (success) {
Debug.Log("Achievement "+name+" reported successfully");
}  else {
Debug.Log("Failed to report achievement "+name);
}
} );
}
#endif
}
static public void Score(string name,long score) {
#if UNITY_IPHONE
if (Social.localUser.authenticated) {
Social.ReportScore (score, name, (bool success) => {
if (success) {
Debug.Log("Posted "+score+" on leaderboard "+name);
}  else {
Debug.Log("Failed to post "+score+" on leaderboard "+name);
}
} );
}
#endif
}
}
}
Listing 15-5.
C# Version of FuguGameCenter
```

Finally, what you might miss about JavaScript the most is the ability to easily modify the Vector3 values in a transform. Listing [15-6](#A310332_2_En_15_Chapter.html#Par29) shows a simple line of JavaScript that modifies just the x component of a transform’s position.

```
function Start () {
transform.position.x=0;
}
Listing 15-6.
Modifying a Vector3 of a Transform in JavaScript
```

That line of code will generate a compiler error in C# because Vector3 is a struct, not a class, and thus a value type, not a reference type. The fact that it works in JavaScript is a convenience (albeit a misleading one, because it does make Vector3 look like a class, not a struct).

Because Vector3 is a struct, you don’t have to worry about the garbage collector having to clean up a bunch of unused Vector3 instances since, well, there are no instances. But it does mean in C# you have to create a new Vector3 if you want to modify the transform’s position, as shown in Listing [15-7](#A310332_2_En_15_Chapter.html#Par32)

```
void Start () {
transform.position = new Vector3(0,transform.position.y,transform.position.z);
}
Listing 15-7.
Valid Way to Modify a Transform in C#
```

Notice how in C# you have to specify new before the Vector3 constructor, whereas in JavaScript that is optional. That is a minor convenience of JavaScript (these types of conveniences are generally known as syntactic sugar ), but C# has the advantage of allowing you to define your own structs, in a manner similar to how classes are defined. In JavaScript, you can use structs, but there’s no way to define new ones. Andrew Stellman and Jennifer Greene’s Head First C# (O’Reilly) is a good introduction to C#, but experienced programmers can get a quick start in C# with the C# in a Nutshell series (also published by O’Reilly). Java programmers in particular will find C# familiar (despite the name, C# has no relation to C or C++, unless you count distant ancestors).

### Script Execution Order

Before leaving the topic of scripting, there’s one more issue worth mentioning: specifying the order of execution among scripts. If you look in the SmoothFollow script used to control the Main Camera in the bowling game, you’ll see the Camera position is updated not in an Update callback but in a LateUpdate callback. LateUpdate is called in every frame just like Update, but all LateUpdate calls in a frame take place after all Update calls have completed. SmoothFollow calculates the camera position in LateUpdate, so it takes into account any position change that has taken place in its target’s Update callback.

Having both Update and LateUpdate callbacks is a common technique in game engines, but it can go only so far. What if you want a GameObject to update after another GameObject’s LateUpdate? Sorry, there’s no LateLateUpdate callback! But Unity does have a more general solution, the Script Execution Order settings, which can be brought up from the Project Settings submenu of the Edit menu (Figure [15-3](#A310332_2_En_15_Chapter.html#Fig3)).

![A310332_2_En_15_Fig3_HTML.jpg](Images/A310332_2_En_15_Fig3_HTML.jpg)

Figure 15-3.

Bringing up the Script Execution Order Settings

The Script Execution Order settings can also be brought up by clicking the Execution Order button displayed in the Inspector view when a script is selected. Either way, the Script Execution Order settings show up in the Inspector view (Figure [15-4](#A310332_2_En_15_Chapter.html#Fig4)); you’ll see a list initially containing just one item, Default Time.

![A310332_2_En_15_Fig4_HTML.jpg](Images/A310332_2_En_15_Fig4_HTML.jpg)

Figure 15-4.

The Script Execution Order settings in the Inspector view

To add some script that would execute after FuguForce, you would click the plus (+) sign at the lower right of the Script Execution Order settings and select the FuguForce script from the resulting menu. By default, the FuguForce script would appear in the Script Execution Order settings below the Default Time setting, meaning FuguBowl will run after all other scripts.

Then you might decide that the game controller script, FuguBowl, should run after every other script (since it’s constantly checking the bowling position, for example) except for SmoothFollow. So again, you would click the + button to add the FuguBowl script and then drag FuguBowl, using the handle on the left, above SmoothFollow but below Default Time, resulting in a list, as shown in Figure [15-5](#A310332_2_En_15_Chapter.html#Fig5). Anything dragged above Default Time would execute before all the other scripts.

![A310332_2_En_15_Fig5_HTML.jpg](Images/A310332_2_En_15_Fig5_HTML.jpg)

Figure 15-5.

Specifying the execution order of FuguBowl.js and SmoothFollow.js

Clicking the minus (-) button on the right of a script will remove the script from the Script Execution Order settings.

### Plug-ins

Much mention has been made of third-party plug-ins, some available on the Asset Store and many from Prime31 Studios. But if you happen to be conversant in C, C++, or Objective-C, you can write your own plug-ins, either to access more device functionality or to make use of libraries written in C, C+, or Objective-C. Besides including the native library, plug-ins require additional Unity wrapper scripts. The process is documented on the “Plugins” page in the “Advanced” section of the Unity Manual.

## Tracking Apps

Although iTunes Connect provides download and sales figures (and the graph is a lot easier to read than the `.csv` report, which once was the only option), there are several third-party products that track app sales and other data like app rankings and reviews.

App Annie ( [`http://appannie.com/`](http://appannie.com/) ) and AppFigures ( [`http://appfigures.com/`](http://appfigures.com/) ) are two of the more popular web-based products that automatically perform daily downloads of app data from iTunes Connect (after you provide your iTunes Connect login information during account setup). They both have nice graph presentations of the sales data and send daily e-mail summaries of the app statistics. App Annie is currently free (technically in beta), while AppFigures charges a fee.

Figure [15-6](#A310332_2_En_15_Chapter.html#Fig6) shows an App Annie graph of downloads over a month for Fugu Bowl. The number of downloads that are updates are overlaid over the downloads that are sales. There in the update line corresponding to releases of updates for FuguBowl.

![A310332_2_En_15_Fig6_HTML.jpg](Images/A310332_2_En_15_Fig6_HTML.jpg)

Figure 15-6.

The App Annie sales graph

AppViz ( [`http://appviz.com/`](http://appviz.com/) ) is an macOS application that performs the same function but downloads the data onto your Mac and features a variety of graphs, including sales by geography (Figure [15-7](#A310332_2_En_15_Chapter.html#Fig7)). AppViz is not free, but it doesn’t cost much, and I think it’s well worth it.

![A310332_2_En_15_Fig7_HTML.jpg](Images/A310332_2_En_15_Fig7_HTML.jpg)

Figure 15-7.

The AppViz geographic view

There’s no reason to restrict yourself to just one of these products. I use AppViz to ensure I have all of my app sales data available locally, and I like its variety of graphics and the App Store review downloads. And I use AppAnnie for its automated e-mail reports of daily sales figures and rankings display (downloading of all that ranking data in AppViz takes a long time), and App Annie has support for some Android app stores. Also, it’s free!

Tip

From AppViz, you can export all the data you’ve downloaded for it and then import that data into another product like App Annie. Since Apple only makes a year’s worth of app sales data available on iTunes Connect, this is a way to ensure you can always import the entire sales history of an app into a sales tracking product.

## Promo Codes

I have described how to download promo codes after an app is approved on the App Store, but I didn’t explain what to do with them. There are plenty of options. Nearly every app review site accepts promo codes with review requests. Touch Arcade ( [`http://toucharcade.com/`](http://toucharcade.com/) ) is one of those sites, but it also has a very active forum where developers issue promo codes. And AppGiveaway ( [`http://appgiveaway.com/`](http://appgiveaway.com/) ) runs promotions where it hands out promo codes over a set period of time. This is a lot more convenient than handing them out yourself, although you should do that, too. Keeping an inventory of promo codes is also a good reason to crank out app updates, since you get a fresh batch of 50 codes with each update.

## More Monetization

Given the yearly Apple developer fee and Unity license costs, chances are you want to at least make that money back.

### In-App Purchasing

Apple offers one other revenue channel for iOS besides app sales: an in-app purchasing (IAP) system called Storekit. Unity iOS doesn’t have a script interface for IAP, but Prime31 Studios includes a Storekit plug-in among its offerings. The Unity Manual has some tips for how you might download and activate additional content purchased with IAP, and the iTunes Connect documentation explains how to set up in-app purchases.

### Asset Store

I use dozens of free and paid Asset Store packages in my apps, and you will surely find the Asset Store useful for your own projects. But the Asset Store is also another place to self-publish products and generate revenue (Unity takes a 30 percent cut, which is the same as what Apple does on the App Store). And you can release packages free on the Asset Store for recognition or as a contribution to the community (I’ve certainly taken advantage of that generosity for the examples in this book).

Conveniently, Asset Store submissions are made from within the Unity Editor, using the Asset Store tools downloaded from, aptly, the Asset Store. You can submit any collection of assets from a project—audio, textures, models, scripts, plug-ins, or even the entire project itself.

Tip

Use preprocessor directives to ensure there are no compiler errors when switching build targets, and add a custom UNITY_PRO preprocessor directive if you have Unity Pro features so that Unity Basic users won’t have problems.

Much of the work in an Asset Store submission, as with an App Store submission, involves creating screenshots, icons, app descriptions, and store artwork. The instructions for becoming a seller on the Asset Store are available at [`http://unity3d.com/asset-store`](http://unity3d.com/asset-store) .

### Developing for Android

Almost everything developed for Unity iOS in this book will work fine with Unity Android. Unsurprisingly, anything in the iPhone class is not available on Android either (e.g., the variable iPhone.generation, which we used to check the device type), but everything in the Input class that works for iOS also works the same on Android, including the touchscreen and accelerometer functions.

Prime31 Studio and other vendors offer Unity Android plug-ins to provide more access to device features and mobile services, such as the AdMob mobile ad service.

The build and submission process is different for Android, too. Instead of Xcode, Android requires installation of the Android SDK. You can still invoke a Build and Run operation from the Unity Editor, but instead of building an Xcode project for the app, Unity Android directly builds the executable app (an APK file). And unlike iOS development, Android development can be targeted to a number of app stores. Google Play in particular has no approval process, so it’s a good avenue to self-publish quickly and a useful fallback channel for apps rejected by Apple.

### Contract Work

While working on that next hit app, independent developers often keep the bills paid by taking contract work. Prospective clients can be found in the Paid Work topic of the Unity forum and on [`http://unity3dwork.com/`](http://unity3dwork.com/) and even general contractor web sites like [`http://odesk.com/`](http://odesk.com/) . Some marketplaces specialize in mobile app development, such as [`http://apptank.com/`](http://apptank.com/) and [`http://theymakeapps.com/`](http://theymakeapps.com/) .

If you’re looking for full-time game development work, whether it involves Unity or not, experience developing games with Unity, not to mention a portfolio of self-published games, can only help.

## Final Words

Now I’ve come to the end, and ideally you’re ready and excited to start making your own Unity iOS apps. If there’s one lesson that I hope you can take from this book, it’s that you can start with something simple and keep building on it until it turns into something interesting. Don’t be one of those people who want to start with a massively multiplayer online (MMO) game as a first project! Remember to keep learning and participate in the Unity community, both contributing and asking for help on the Unity forum and on the Unity Answers site, and don’t forget to check the Unity wiki. It’s fine to promote your work to the community (there is a Showcase topic in the Unity forum) and ask others to spread the word, but remember to return the favor! As an unintended result of working on my own projects with Unity iOS, I’ve met a population of developers with whom I can commiserate and celebrate and trade valuable tips. I won’t try to list them all, but you can find them on my Twitter follow list at `@fugugames`. Twitter, by the way, is another great way to interact with other Unity developers, on a more personal level. I’ve communicated with many customers over Twitter, also, and have had some success with setting up Facebook product pages for my games (in particular, HyperBowl). Although app customers can be legendarily snarky, I’ve found most of them are supportive and sympathetic of us independent developers and are enthused to be part of the process if you let them. In fact, I’ve benefited from a lot of free QA and some localization (translation) help, not to mention a lot feedback. So, set up those Twitter feeds and Facebook fan pages and start talking!

Above all, have fun. Any other reward is a bonus!

Index A Advanced physics barrels, bowl on asset store BarrelPin prefab box BoxCollider CapsuleCollider collider component copy compound collider GameObjects, child HyperBowl pasting a component picking prefab project view update, prefab lane lengthening pins assign prefab bowling with capsule creation collision prefab rigidbody, bowl play BroadcastMessage FuguReset script gutter ball listings messaging ResetPosition resettable resources of assets scripting references sounds addition AudioClip AudioSource get sound OnCollision callbacks pin collision rolling sound Animation AnimationClip loop AudioClips Gianmarco Leone’s general music set inspector view of music in the scene dance floor hierarchy view plane position scene view dancing skeleton asset store AudioClip computer graphics reference utility hiding cubes orbit shadow directional light maps point light QualitySettings rotate, directional light skeleton, game view soft shadows skeleton dance Skeletons Pack dance floor particle effects Project View search results skinning sword and shield Apple developer site AudioListener B Bowling ball ball control Collider component MeshCollider PhysicMaterial SeePhysicMaterials HyperBowl ball controller script FixedUpdate callback FuguForce script OnCollisionEnter callback Script creation Speed Check TagManager update callback Rigidbody component Collision Detection property Constraints property Gravity property Inspector View Interpolation property Is Kinematic property PhysicsManager SmoothFollow script sphere MeshFilter component C Climber Game Build Settings Mac platform OS X app Console View EditorLayout SeeUnity Editor Game View SeeGame View Hierarchy View SeeHierarchy View Inspector View SeeInspector View play mode project menu Project View SeeProject View resources manual tutorials version controls scene Scene View SeeScene View selection of, project wizard Console View C# scripts class declaration FuguFrameRate FuguGameCenter new script valid modification vector transformation Cube GameObject Align with view BoxCollider component creation framing MeshFilter component MeshRenderer component moving Transform component D, E Data Universal Numbering System (DUNS) Device input accelerometer debug inspector view, shake-to-pause log output, log print out values shakes detection camera asset store GameObject iCade additions iOS developer library Prime31 Etcetera plug-in photo scripting reference WebCam touch screen adjustment variables ball swipe ball tapping control adjustments detecting swipe FuguDebugOnMouse FuguOnTap input OnMouseDown raycasting TouchPhase.Began F Field of View (FOV) Finite state machine (FSM) FlareLayer FuguBowl clear function inheritance player score struct definition FuguBowlPlayer, creation of G GameObjects, move by scripting SeeScripts Game scripting asset store FungBowlPlayer logics of console view, state machine finite state machine design initialize state machine listings state machine, bowling states SeeState coroutines tracking yield function pin status awake callback barrel pin FuguPinStatus GetPinsDown function pinBodies variable RemovePins function ResetPins function references rules score clear function ClearScore function constructor frame FungBowlPlayer FungBowlScore GetScore function HyperBowl scoreboard listings MonoBehavior player’s score recursive function SetBallScore SetSpareScore SetStrike function setting stores on web utility manual Game View Free Aspect ratio Gizmos maximize on play stats Graphical user interface (GUI), game asset store audio panel credits page graphics panel main page display of menu options page pause menu current page display, menu divide-by-zero enumeration of escape key GUILayout functions layout, automatic OnGUI callback PauseGame function script creation state diagram Time.deltaTime unpause reference manual scoreboard bowling FuguBowlScoreboard GUIStyle scripting styling UnityGUI code scripting reference system panel color customization GUISkin, pause menu Necromancer GUI in pause menu scripts selection, color skin customization utility manual GUILayer H Hierarchy View inspect GameObject parent GameObjects High dynamic range (HDR) I Inspector View editor settings locking .meta extension files iOS Developer Program App Store graphics app icon screenshots build and run iTunes Connect SeeiTunes Connect provisioning portal app identifiers development provisioning profile device testing distribution provisioning profile documentation registration Xcode organizer iOS Team Provisioning Profile La Petite Baguette Distribution techdev techdist iOS Team Provisioning Profile iTunes Connect add/manage apps app information app type availability and price icon and screenshots, upload promo codes Rating section rejection process sales tracking updates upload preparation version, category and copyright information J, K Just-in-time (JIT) compiler L Lambda functions La Petite Baguette Distribution Linden Scripting Language (LSL) M, N Main Camera anatomy of AudioListener Component components Clear Flags property Culling Mask property depth HDR perspective projection rendering path texture viewport FlareLayer component GUILayer component multiple Transform component MouseOrbit script Camera GameObject import package O Optimization assets audio collision sound compression, mesh import settings meshes mipmapping textures asset store GameObjects ball BarrelPin prefab camera floor frustum culling lights main camera pins shader setting offline beast Lightmapping window occlusion culling umbra (Pro) profile Autoconnect Profiler build log built in profiler console app search folder CPU usage of profiler display of frame rate editor profiler (Pro) fourth generation iPod Touch FPS GameObject frametime FuguFPS game view stats GUIText GameObject GUITexture manual connection profiler shadows disabling reference manual scripting reference scripts cache GetComponent garbage collection minimizing runtime static batching (Pro) share materials System.GC.Collect texture animation UnityGUI settings of accelerometer frequency audio manager dynamic batching mesh data multi sample antialiasing physics manager quality script call optimize static batching time manager target selection frame rate script attachment script creation space targeting unity manual on web P, Q PhysicMaterials adjusted values anatomy of Bouncy vs. Ice creation into Material fields Project View Standard Assets Physics and controls bowling ball SeeBowling ball bowling scene asset organization system delete GameObjects floor retile Inspector View light adjustment Main Camera point light Save Scene as Substance archive wood planks ProceduralMaterial PhysX software development kit Point Light GameObject adjustment Color property cookie texture creation Culling Mask property Draw Halo property Flare property Halo property Inspector View intensity Lightmapping Range property Render Mode Shadow Type property Type setting Project View assets search Inspector View operations on asset scale icons switching (one and two column) top level of R Raycasting Rigid body simulation S Scene View camera controls camera view GameObject selection Gizmos navigation of options tilted perspective top-down view Screens and icons activity indicator display PlayerSettings GUI scale BeginPage function pause menu quit button scoreboard ScreenWidth transformation matrix iOS bowling FuguBowl player setting orientation, autorotate iOS developer library reference manual script, activity indicator secondary screen start and stop scripting reference setting of import, textures Prerendered icon Project view sizing in player splash screen (pro) ApplicationLoadLevel build settings creation default utility FuguSplash script GUITexture loading scene orientations scene creation screen resolutions secondary screen wait and load WaitForSeconds, implementing textures Scripts debugging compilation errors MonoDevelop runtime errors folder creation anatomy of callbacks execution FuguRotate script Inspector View methods MonoDevelop editor naming Scripting Reference organize assets prefabs Apply Changes To Prefab Break Prefab Instance child cubes, editing cloning a GameObject Project View rotate the Cube Transform component Transform.Rotate in World Space Scripting References State coroutines HyperBowl, gutter ball ResetCamera and ResetBall functions SmoothFollow script StartGameOver StateBall1 StateBall2 StateBall3 StateGutterBall StateNewGame StateNextBall StateRolledPast StateRolling StateRollOver StateSpare, StateStrike, and StateKnockedSomeDown Syntactic sugar T techdev profile Type inference U, V, W, X, Y, Z Unique device identifier (UDID) Unity Editor default layout game view monetization for Android, develop app store contract work in-app purchase New Project creation, Add Asset preset layout 2-by-3 view menu 4-split view tall view wide view promo codes scene view scriptings of C# editor scripts execution order JavaScript script listing, FuguEditor menu addition plugins SetActive SetActiveRecursively settings, execution order tracking App Annie sales graph AppViz workspace customization add view detach view move views remove view resize areas Unity iOS Angry Bots, iOS version apps on app store game iOS developer library reference manual utility manual player settings additional settings customization of just-in-time compiler optimization techniques resolution and presentation selecting porting test, iOS simulator Angry Bots append or replace build prompt, location and name exit iOS project with Xcode options progress indicator save screen shot command selection, iPad or iPhone Xcode project folder test, remotely device resolution, minimize remote app Xcode installation Unity project Asset Store Cube SeeCube GameObject MouseOrbitscript SeeMouseOrbit script New Project creation menu item new scene Project Wizard Save Scene option PointLight SeePoint Light GameObject Reference Manual textures Asset Store Cube Game View Import Asset command Import window Inspector View Project View unity manual Unity system community for game development iOS developer, register as Mac preparation page download versions Xcode, downloading installation of bug report documentation folder execution of installer paid activation, license welcome screen iOS development requirements management bug reporting Indie (light) preferences in editor Pro (dark) reporter window skin change updates, check for manuals references web site Unity tour Angry Bots, application load level Climber Game SeeClimber Game