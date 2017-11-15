+++
date = "2012-12-07"
title = "Migrating from SourceSafe to Mercurial"
description = "Describes how to migrate source controlled projects from Microsoft SourceSafe to Mercurial, with or without revision history."
categories = ["TechNote"]
tags = ["SourceSafe", "Mercurial", "Migration"]
thumbnail = "images/theme/thumbnail-04.jpg"
+++

In this post, I will describe two methods to migrate source controlled projects from Microsoft SourceSafe to Mercurial. The first method involves only migration of the latest code version; and the second method the migration of both objects and history.

<!--more-->

## Migration without History

This method can be used in a couple of cases. The first case is when you do not need change history from SourceSafe and the second case is when the Migration with History procedure (described later on in this document) logs errors with the conversion. The fallback for accessing history is to maintain the SourceSafe repositories in a read-only capacity.

### Procedure

1.  Connect to the SourceSafe database
2.  Verify all files in your project are checked in
3.  Get the latest version of your project
    a. Check the *Recursive and Build tree (override working folders)* options
4.  In a separate working folder, create a new Mercurial repository
5.  Add an *.hgignore* file to the new repository *(see Additional References at end)*
6.  Create a *src* folder within your repository
7.  Copy the contents of the SourceSafe latest version folder to your *src* folder
8.  Execute the Mercurial *add* command
9.  Execute the Mercurial *commit* commant
10. Zip up the contents of the SourceSafe folder for archive purposes
11. Rename the project within SourceSafe to something like “.mercurial”

> The last two steps are optional. Zipping up the contents of the SourceSafe folder gives you the ability to have an archive of the latest source files before conversion, in case you lose access to the original SourceSafe installation. Renaming the project within SourceSafe gives a visual identifier that it has been migrated.

## Migration with History

The Mercurial website lists two methods of migrating from SourceSafe. The first method uses an indirect approach by first migrating to Subversion and then migrating to Mercurial. The second method uses a direct conversion from SourceSafe to Mercurial. The indirect method uses a custom library to access the SourceSafe repository, bypassing the native command line tools. The direct method uses a custom Perl script to wrap the SourceSafe command line tools. 

> The SourceSafe repository format was never officially documented so using the indirect method and its own reverse engineering routines was considered too risky; therefore the direct method was chosen.

### Tools

In order to execute the direct conversion procedure, you will need, besides SourceSafe, an installation of the Perl programming language and the custom conversion script. Links to both of those tools are listed below. The ActiveState community edition will work fine for this purpose.

- [ActiveState Perl](http://www.activestate.com/activeperl)
- [Conversion Script (i.e. vss2hg.pl)](http://edmundv.home.xs4all.nl/sw/vss2hg.pl)

The conversion script has the typical usage document inside it. One change that I had to make in order to run this script successfully, in my case, is documented below.

    Old line: $cmd = $SSCMD . " Get -GTM -W -I-Y -GL\"$localdir\" -V$version \"$file\" 2>&1";
    New Line: $cmd = $SSCMD . " Get -GTM -W -I-Y -GL\".\\$localdir\" -V$version \"$file\" 2>&1";

> The exact reason I made the change was never recorded, so I recommend you try the original script before making any changes.

## Additional References

- [Selenic Wiki - SourceSafe Conversion](http://mercurial.selenic.com/wiki/SourceSafeConversion)
- [StackOverflow - SourceSafe to Mercurial Conversion](http://stackoverflow.com/questions/961878/moving-from-visual-sourcesafe-to-mercurial)
- [StackOverflow - Ignore File for Visual Studio 2013](https://stackoverflow.com/questions/22011316/mercurial-hgignore-for-visual-studio-2013-projects)
- [StackOverflow - Ignore File for Visual Studio 2008](https://stackoverflow.com/questions/34784/mercurial-hgignore-for-visual-studio-2008-projects)