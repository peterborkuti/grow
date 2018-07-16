<<TableOfContents>>

== What is a UI issue ==

A UI issue is strongly related to the user interface. Meaning that it can be fixed by changing some css, JavaScript or html (~=jsp ftl, vm) code.

== How to decide if it is a UI issue or not ==

That's not easy at all. Basically, big percent of all the issues cause anomalies on the UI. For example,  portal displays bad data, displays badly formatted data, displays data on the wrong place, for the wrong user, etc. But this does not mean, that all these issues are related to UI code. It can be, but not necessarily.

So let's see, the type of UI issues

== Types of UI issues ==
=== Easy ===

Meaning, that it is easy to decide if it is a UI issue.

==== Browser issue ====

The issue can be reproduced only with a specific browser or a specific version of a browser.

Like the "IE issues" which can be reproduced only with IE. You can be sure, that these type of issues are UI related, but if not, nobody will arguing about this later, so let UI guys to check them.

==== JavaScript error issues ====

There is a JavaScript error in the browser's console.

It is not necessary, that the customer reported the error message, probably customer claimed for that something does not work on the UI (a button cannot be clicked, for example).

You can get the error message when you enable the browser's console and reproducing the issue.

JavaScript code should work without errors (every case of user interaction should be covered by the code).

For a properly working JavaScript program, the JavaScript files should be loaded by the browser (you can check it in the Network panel of the browser), but if all of them loaded in the good order, JavaScript should work.

There are really only a few cases when it turnes out, that a JavaScript error message was caused by some backend (~= not UI) issue, but let UI guys to decide it. These are ususally hard-to-fix issues (or ridiculously easy to fix but hard to decide where to fix issues) so nobody will laugh at you if you "put it into the wrong box".

==== CSS related issues ====

Something appears ugly or colored wrongly, or it is somehow does not look like normally. If only the "design" breaks and the data is OK. Or the data is OK, but it is displayed on the wrong place of a page.

==== CKEditor issues ====

Everybody hates CKEditor and usually UI guys deal with CKEditor (or other editors in the portal) so let them decide the good componenet for these issues.

If you wanted to be very kind, you can check the issue with different editor or with the "vanilla" ckeditor in the portal, or on the ckeditor's website.

If you can reproduce the issue without Liferay (only with CKEditor) it is a 100% UI issue.

Subtypes of these issues:

* Editor buttons are wrong
*Some of the Editor buttons are not displayed.\\We customized CKEditor, so the bug is probably in our code, but anyway, it is a UI issue.
* Switching from one mode of the editor to another, content changes (It can be planned, but because lacking of proper documentation we should check the code to decide it)
* When Saving a content something lost (Yes, this can be backend or middleware related, but majority of the cases is caused by the editor or the conversation process of the editor)

=== Hard ===

Meaning, that it is hard to decide if it is a UI issue or not.

Unfortunately, all the other types are hard to decide, but I can give you some clues.

==== The wrong data displayed on the UI ====

Check, if the wrong data comes from the backend or not. If the backend sends the wrong data check

if the database contains the wrong data

If ~*not~*, it is a ~*backend issue~*. You may find the correct component based on the table where this data is stored.

If ~*yes~*, check if the UI sends the wrong data when saving.

It is easier than you think: You should check the traveling data from the browser to the portal. If the traveling data is wrong, it is definitely a UI issue. If not, it is ~*not~* a UI issue. You can not miss a lot if you set the component according to the storing table.

== How to reproduce a UI issue ==

If the steps of the customer does not work (I mean, the issue can not be reproduced)

Ask the customer 

* the browser name and version
* portal-ext.properties
* custom theme
* portlets on the page where the issue reproducible at customer's side
* a video about the issue with opened browser console
* check if minifying is on~/off
* check if javascript fastload is on~/off
* double check if the browser is IE and *not* in compatibility mode
* check your tmp dirs and the browser's 'cache (it should not be used by the browser)
* check if customer caches are emptied and their browser cache is not used

== Skills you need for checking a UI issue ==
* using different browsers
* setting to on~/off browser's caching
* switching the browser into developer mode
* using a javascript~/css debug friendly portal-ext.properties
* checking the browser's console

First version was written  for Emma Liu to cover her interesting questions and to preserve it for the future by Péter Borkuti

