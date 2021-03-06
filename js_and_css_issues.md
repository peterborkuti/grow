= Approaching JavaScript and CSS Issues =
== Description ==

This article describes 

* the role of JavaScript and CSS in general
* gives a picture about the goal of JavaScript and CSS in Liferay Portal
* shows the important layers
* explains some concepts
* gives some tips
* guides a support engineer how to start solving a JavaScript issue

== Related documents ==

[[https://in.liferay.com/web/support/wiki/-/wiki/Support+Guides/Approaching+JavaScript+API+Issues|Approaching JavaScript API Issues]]

[[https://in.liferay.com/web/global.engineering/wiki/-/wiki/Core+Development+Main/How+to+build+an+Alloy+fix+pack|How to build Alloy fixpacks for older portal versions]]

== Roles and Goals of CSS and JavaScript ==
=== CSS ===

CSS means **C**ascading **S**tyle **S**heets

* defines how to display HTML elements
* can mark elements in HTML in order to deal with them with JavaScript
* can sign elements in HTML to let JavaScript know about the element "state"

[[http://www.w3schools.com/css/|W3C CSS Tutorial]]

[[https://developer.mozilla.org/en-US/docs/Web/Guide/CSS|MDN CSS developer guide]]

=== JavaScript ===
* programming language based on EcmaScript
* embedded in browsers so it runs on client-side
* can interact with users
* can interact with the server asynchronously
* can control the browser
* modify web pages (content, cssHTML elements and CSS)

[[http://www.w3schools.com/js/|W3C JavaScript Tutorial]]

[[https://developer.mozilla.org/en/docs/Web/JavaScript|MDN > Web technology for developers > JavaScript]]

== How it works ==

Browsers build a model in memory about the displayed document, which called **DOM** (**D**ocument **O**bject **M**odel). Programmers can modify this DOM with JavaScript via a **standardized API**. The name of the standard is **EcmaScript** and JavaScript is one of its dialect. JavaScript programs stored as text files and **delivered as source code**. Browsers have **JavaScript engines** which implement the standard and interpret~/compile JavaScript source code.

[[http://www.ecmascript.org/|ECMAScript org]]

[[http://en.wikipedia.org/wiki/List_of_ECMAScript_engines|ECMAScript engines]]

== Frameworks ==

In old days (before EcmaScript standardization) browsers wanted to attract users with new JavaScript features, which caused big incompatibilities. When the first EcmaScript standard came out, it was hard-to read, so JavaScript engine makers interpreted it differently and~/or interpreted just a subset of it and provided additional features, which also caused incompatibilities. Moreover, the API of DOM is a hardly understandable, far from easy-to-use creature, so programming browsers in JavaScript was not a lot of fun.

JavaScript frameworks emerged to hide incompatibility among browsers and to offer an easy-to-use interface to DOM.

After some (many) years the incompatibility between browsers nearly disappeared (but unfortunately people use old browsers also), so frameworks started to offer new spectacular features, new GUI elements, etc.

[[http://en.wikipedia.org/wiki/History_of_the_web_browser|History of WEB browsers]]

[[http://en.wikipedia.org/wiki/Browser_wars|Browser wars]]

== Layers in Liferay Portal ==
* portlet
* portal
* AlloyUI
* YUI
* third-party

=== Where you can find js/css files ===
==== portlet ====
* js and css directories of portlet
* jsp~/jspf files of portlet

In **portlet layer** there are two places where you can find js~/css. Standalone files, which are in js and css directories and embedded fragments in jsp~/jspf files.

==== portal ====
* html~/js~/liferay, html~/css
* vm, ftl, jsp, jspf files
* taglib files in html~/taglib

Liferay Portal has js fragments many places. Liferay has an own JavaScript framework usually on top of AlloyUI, which is in html~/js~/liferay. You can find js fragment also in vm and ftl files (it could be very tricky to find them, if you forget this).JSP tags also can contain JavaScript, so you have to think also for taglib files, just search after the text //<aui:script//

==== AlloyUI ====
*{{{html/js/aui/aui-*}}}

AlloyUI is a JavaScript framework. We could say, that it is **the** JavaScript framework for Liferay. An excerpt from its [[http://alloyui.com/about/||About AlloyUI]] document: //It's mantained by highly qualified engineers of Liferay, it's built on top of YUI3 (an awesome project made by Yahoo!) and uses Bootstrap (another cool project made by Twitter).//

Moreover it is integrated into Liferay, so it has taglibs and you do not have to install anything to use it in portlets.

[[https://www.liferay.com/documentation/liferay-portal/6.2/development/-/ai/simple-alloyui-example-liferay-portal-6-2-dev-guide-en|Liferay Portal 6.2 Developer's Guide]]

==== YUI ====
* html~/js~/aui

AlloyUI is built on top of YUI which is a huge JavaScript framework and it is packed together with AlloyUI. Every piece of directory whose name does not start with //aui-//, is part of YUI.

[[http://yuilibrary.com/|YUI Library]]

==== third party ====
* html~/js~/editor
* html~/js~/misc

We have many third-party editors and use CKEditor in many places. Moreover, we customized it a lot. Be extremely careful when modifying CKEditor sources, and check portal-web~\build.xmlfile, especially the //<replace...>// parts. We customize CKEditor with replacing parts of original sources.

[[http://ckeditor.com/|CKEditor]]

[[HowToSolveCKEditorIssues|How to solve CKEditor issues]]

== Setup ==
=== minification ===

[[http://en.wikipedia.org/wiki/Minification_(programming)|Wikipedia about minification]]

As I mentioned earlier, JavaScript is delivered in source code as normal text file. It is very verbose (think of long variable names, white spaces), so wastes bandwidth. Programmers try to push down the size of delivered files. In order to achieve this minifier tools were created. A minified JavaScript file is a normal text file, but every unnecessary character is eliminated from it. For example, white spaces, new lines. Variable names substituted only one~/two character length, etc.

While it is good on production, it nearly prevents debugging. So JavaScript files stored on disk in more formats. One is normal (as the programmer typed in, readable, easy to debug)and one in minified version. We also use a "debugging" version of files. (It is usually the same as the normal js file, so I am not sure, why we use it, probably it is a convention, YUI uses this) Check the //html/js/aui// directories and you can see every js files in three versions, the file names ended with **-debug** and **-min**.

Liferay Portal has a built-in minifier filter~/aggregate filter which can minify and aggregate files on-the-fly.

See //liferay-web.xml//, //AggregateFilter.java// or //MinifierFilter.java// on older portal versions.

When debugging, switch off minification in portal-ext.properties:

{{{
minifier.enabled=false
}}}
=== barebone.jsp and everything.jsp ===

JavaScript files are usually small and there are many of them. When a browser wants to download all of them, every single file needs a new http connection. This is slow and uses unnecessary server resources, so concatenates every JavaScript file into one, which is a good idea and a common practice. Unfortunately it has one big drawback. When a page uses only some of the javascript files but we concatenate all js files into one, unnecessary js files will travel through the net.

Liferay has a clever feature for this, barebone.jsp and everything.jsp.

There are two lists of JavaScript files in portal.properties which are concatenated into one:

{{{
    javascript.bundle.ids=\
        javascript.barebone.files,\
        javascript.everything.files
}}}

javascript.barebone.files and javascript.everything.files.

See liferay-web.xml AggregateFilter.java or MinifierFilter.java on older portal versions.

When debugging, it must be switched off :

{{{
javascript.fast.load=false
}}}

For css use the

{{{
theme.css.fast.load=false
theme.images.fast.load=false
}}}

For more info see the portal.properties file.

==== Tip - minified CKEditor ====

CKEditor JavaScript files are minified and hardly can be unminified. Here are two tips, which ~*did not~* work for me:

----

Info From [[https://in.liferay.com/web/iliyan.peychev/so/profile|Iliyan Peychev]]

{{{
Download/clone CKEditor from our GitHub repository and open "dev/builder/build.sh”

Scroll to bottom, there is a line, something like:
java -jar ckbuilder/$CKBUILDER_VERSION/ckbuilder.jar …..

Replace it with:
java -jar ckbuilder/$CKBUILDER_VERSION/ckbuilder.jar --build --leave-css-unminified --leave-js-unminified ../../ release --version="4.1.1 DEV" --build-config build-config.js --overwrite "$@“

Note these two options:
“--leave-css-unminified” and "--leave-js-unminified"

and then run build.sh and deploy the result to Portal.
}}}

However, it works for newer versions, in CKEditor 3.6.6.1 and there is no ckbuilder just a packager, which has no parameters like '--leave-js-unminified'.

----

Info From [[https://in.liferay.com/web/gergely.mathe/so/profile|Gergely Máthé]]

{{{
The ckeditor documentation advises to use ckeditor_source.js instead of ckeditor.js in html\js\editor\ckeditor.jsp if we want to debug an unminified version,
but when I tried I got console errors and the editor was not rendered at all. On trunk, ckeditor_source.js doesn't even exist.
}}}
----

At the end I used the pretty-format feature of the browser (Chrome and IE can show minified js files or you can use any of the on-line JavaScript beautifiers).

[[http://jsbeautifier.org/|Online JavaScript beautifier]]

{{chrome-pretty-print.gif}}

== Build ==

Third party codes are in portal-web~\third-party. When building the portal, zips in that directory are unzipped and changed. For more info see the build.xml in portal-web directory.

You do not have to build all of the portal. Step into the portal-web directory and you can use specific ant tasks for building parts of the portal-web, for example

* ant build-alloy
* ant build-ckeditor

Use //ant -p// for listing tasks.

==== Tip - in-place editing ====

build and redeploy is a time-consuming task. Usually I edit JavaScript~/jsp file in tomcat's directory. However emptying cache in portal (Server Administration) and in browser after in-place editing is a good practice.

Chrome has this feature in a hidden place:

{{chrome-cache.gif}}

== Tools ==

When debugging JavaScript, our browser is the most important tool. I used Firebug for FireFox, Chrome Developer Tools and IE developer tools. Now I mainly use Chrome Developer Toolswhen an issue is not browser dependent and IE Developer Tools for IE issues.

[[https://developer.chrome.com/devtools/docs/javascript-debugging|Debugging JavaScript with Chrome, Google own doc]]

[[http://www.codeproject.com/Articles/273129/Beginner-Guide-to-Page-and-Script-Debugging-with-C|Very good, detailed guide for Chrome dev tools]]

[[http://msdn.microsoft.com/en-us/library/gg699336(v=vs.85).aspx|Using the F12 Developer Tools to Debug JavaScript Errors]]

===== Tip - IE Document modes =====

IE has a fantastic feature, the "Document mode". (F12 than CTRL-8) IE can be set to behave like older versions of IE and programmers at Microsoft done their work very well. It is not 100%, but if it works, it can spare hours for you.

//Note: Testing (or using with Liferay)  IE in Document (aka Compatibility) mode is generally discouraged, so try it first with the appropriate IE version. But if it works, it is a real time saving practice.//

//Note: While Document mode is discouraged, we have a portal-property for this: browser.compatibility.ie.versions~=IE~=edge. A real paradox, isn't it? Anyway it is very useful about this property when a customer has issues with IE11 and CKEditor.//

{{ie-document-mode.gif}}

===== Tip - Utilu collection =====

Installing different versions from browsers is a challenging task. Fortunately others did this job, see the Utilu collection of browsers. I advise you to create a Windows Virtual Machine and install the utilu collections or ask for a VM which contains it preinstalled.

[[http://utilu.com/|Utilu browser collections]]

It contains IE versions up to IE8 so you will need a separate VM for IE9, if you need it.

== Breakpoints and logging ==

If you know the Source in Liferay, you can put //debugger;// command into the JavaScript source, and Chrome will  be happy to stop on that. If you know the Source in Browser, you can place breakpoint with developer tools. Breakpoints can be "placed" to DOM modifications (attribute changing, tree modifications, etc) and user interactions (Mouse clicks, key presses, etc) also.

You can also use //console.log("message")//, but beware, IE can use console.log() only when the console is open! For outputting variables, console.log("variable:" + myVar) is ok, but console("variable:", myVar) if far more better.//alert()// also can be used if it fits to your needs.

== How to find the JavaScript file ==

If it is a standalone JavaScript file, you can find it easily:

* aui files are in html~\js~\aui directory
* portlet's js files are in the portlet's js directory

If it is not a standalone js file, probably it is part of the portlet's jsp files and~/or it is come from portal layer or theme.

See the picture below about the layers:

* red: standalone files for external portlet
* green: standalone files for internal portlet
* blue: aggregated JavaScript code

{{javascript-layers.jpg}}

Try to find some text in the browser's javascript source view which can identify the jsp file. For example, when I debugged the Search portlet, I found a 'var REGEX_DATE' literal useful.So I did a search after this text in contents of the portlet's jps files, and it was found only in one place, in modified.jsp.

If you are not so lucky as I was, try to search the string literal in all of the portal's files. I usually use this file name pattern with Eclipse:

{{{
*.f*, *.java, *.js*, *.v*
}}}

The {{{*.f*}}} is for ftl files, {{{*.js*}}} is all for jsp, jspf and JavaScripts and {{{*.v*}}} is for velocity macros.

== solving an issue step by step ==
=== Description ===

Search portlet Search button disabled when selecting date range in IE8

[[https://issues.liferay.com/browse/LPS-47324|LPS-47324]]

=== Reproducing the issue ===
* Build Liferay Portal master
* Install and set-up Liferay Portal for JavaScript debugging
* Run with IE11. In this step, we hope, that issue will be detected in IE8 document mode. If not, we are facing to install a Virtual Machine with Windows and Utilu IE collection.
* Add Search portlet
* Use IE and switch it to Document mode 8
* Search for "test"
* Click on "Custom range ..."
* Choose two valid dates ("To" date must be greater than "From" date)

{{repro-steps.gif}}

* Check if search button is clickable.
* Check if search button is clickable when using Chrome or IE11 in Edge Document mode.

=== Start to find the root cause ===

Something enables the button in Chrome, but not in IE8. Probably the flow of program should be the same, so in IE8 the flow starts, but breaks somewhere. What could start the flow? The user interaction. After entering a date, a program flow starts end ends in enabling the button.

We can catch the flow in both ends. Chrome has a nice feature, DOM breakpoints. We can "place" breakpoints not only for code lines, but events, like modifying DOM or user actions, like mouse click.

{{chrome-dom-breakpoint.gif}}

Now click on the html with the right mouse button and click on "Break on ... -> Attribute modification".

Click into the //To date// field and click on a new date. Chrome will break on a line. Now you have the call stack, so you can traverse on it up. Check where it can be stopped by IE8. Put a break point in IE11 to the startig point and follow the program flow and find the place, where it decides not to enable the button.

== Writer ==

Péter Borkuti

