Please be aware it's a script which helps you to automate the sub tasks of the backport process. **First step** is to make sure whether the fix to be backported meets with the rules described on **[[https://grow.liferay.com/excellence/Fix+Pack+Backporting+Guidelines|Backporting Guidelines]]** page.

----

I'd like to have a cherry-pick-tool which can find the files on ee-6.2.x.

== Trials and ideas ==

I found this on the net:

git config merge.renameLimit 999999\\

But it did not help.

I also had an idea:

~* Creating a diff file from master

~* changing the paths in the diff according to ee-6.2.x

~* applying the diff on ee-6.2.x

To this, we need a list of changed files, which can be created the help of git (--follow parameter)

== Some steps to help ==

Let's say that you have to backport LPS-65583 from ee-7.0.x to ee-6.2.x.

Get the hashes of the commits:

{{{
git log --reverse --pretty=format:'%h' --grep LPS-65583 > commits.txt
}}}

Get the file names for the commits:

{{{
for i in `cat commits.txt`; do  git show --pretty="" --name-only $i; done|sort|uniq
}}}

The sort|uniq at the end will delete all the repeated file names.

Now we can check how the file names changed:

{{{
for i in `cat filenames.txt`; do git log --pretty="" --name-only --follow -- $i; echo "*******"; done
}}}

or without file-name repeats:

{{{
 for i in `cat filenames.txt`; do git log --pretty="" --name-only --follow -- $i; echo "*******"; done|awk -v l="XXX" '{ if ($0 != l) { print $0; l=$0};}'
}}}

Unfortunately, these commands follow the file up to the time starting the git repo, but the search can be limited like this:

{{{
for i in `cat filenames.txt`; do git log --pretty="" --name-only --follow  HEAD...ee-6.2.x -- $i; echo "*******"; done
}}}

or without file-name repeats:

{{{
 for i in `cat filenames.txt`; do git log --pretty="" --name-only --follow  HEAD...ee-6.2.x -- $i; echo "*******"; done|awk -v l="XXX" '{ if ($0 != l) { print $0; l=$0};}'
}}}

I am not sure, if it is OK because after creating the branch ee-7.0.x the ee-6.2.x would grow independently, but I hope, it works.

Now you know what is the name of the files in ee-6.2.x.

If you have many files, you may want to automate the file name changes:

If you followed the examples above, you had a filenames.txt file with the file names in ee-7.0.x.

Let's use sed to change the file names. Sed understand this command: 's@a@b@' and it will change "a" to "b".

Append to the end of every row in filenames.txt the file name in ee-6.2.x separated by "@":

{{{
awk '{print $0 "@";}' filenames.txt > filenames2.txt
}}}

Now edit filenames2.txt and append the old file names.

Run this script to prepare filenames2.txt for later processing:

{{{
awk '{print "s@" $0 "@g"}' filenames2.txt > filename3.txt
}}}

[[https://github.com/peterborkuti/backport-script/blob/master/backport-script.sh|Here is the whole script]][[Now you are well prepared, start the backport:| (you can watch it on GitHub)]]

*copy the backport-script.sh into the root of your repo
*edit the LPS, FROMBRANCH and TOBRANCH variables in the script
*run the script : .~/backport-script.sh

it will fetch~/update the needed branches, find commits, etc and it will write to the console the git commands for applying the patches.

You only need to copy-paste the commands and push enter.

Pull requests: [[https://github.com/peterborkuti/backport-script]]

