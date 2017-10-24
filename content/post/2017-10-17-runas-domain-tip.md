+++
date = "2017-10-24"
title = "RunAs Tip for Long NetBIOS Names"
description = "Describes an error that might occur with long host NetBIOS names."
categories = ["Technote"]
tags = ["Windows", "RunAs"]
thumbnail = "images/theme/thumbnail-09.jpg"
+++

This post will discuss a recent problem I encountered when trying to use the RunAs command to elevate myself as a local user account.

<!--more-->

Today I logged on to a computer running Windows Server 2008 R2 to investigate why some scheduled tasks appeared to be hung. These scheduled tasks run under a local account that is a member of the *administrators* group.

As part of my investigation process, I wanted to run the scheduled task command as the intended local user so I needed the *RunAs* command.

> For the purposes of this explanation, I am going to refer to the other host as *SERVER-WITH-NAME* and the local account as *account*. A key point to note is that the host's name is *16* characters.

I first opened an *administrator* command prompt and tried the following:

    C:\>runas /user:SERVER-WITH-NAME\account "cmd"
    Enter the password for SERVER-WITH-NAME\account:
    Attempting to start cmd as user "SERVER-WITH-NAME\account" ...
    RUNAS ERROR: Unable to run - cmd
    1326: Logon failure: unknown user name or bad password.

So my first thought was that somehow the password had been changed on the local account. I reset the password and tried again with the same results.

Next I verified the *domain* of the account, which in this case is the name of the local computer.

    C:\>hostname
    SERVER-WITH-NAME

I did some further research and came across this article in Microsoft's Support site titled [Naming conventions in Active Directory for computers, domains, sites, and OUs](https://support.microsoft.com/en-us/help/909264/naming-conventions-in-active-directory-for-computers-domains-sites-and) which states:

- Minimum name length: 1 character 
- Maximum name length: 15 characters

> Note The 16th character is reserved to identify the functionality that is installed on the registered network device. 

With that little bit of information, I modified my command to only use the first **15** characters of the computer's name. This time I met with success.

    C:\>runas /user:SERVER-WITH-NAM\account "cmd"
    Enter the password for SERVER-WITH-NAM\account:
    Attempting to start cmd as user "SERVER-WITH-NAM\account" ...
    C:\>

So in summary, if you encounter the *unknown user name or bad password* error when using the *RunAs* command; first verify the account's name and reset its password, if necessary. If that doesn't resolve the issue, then don't forget to check the name of the computer that you are using to reference the local account. If it is longer than 15 characters you will need to shorten it.

