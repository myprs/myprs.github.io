---
layout: post
title:  "Showing Birthdays from Owncloud Address Book in Mozilla Thunderbird Calendar (Lightning Extension)"
date:   2016-01-06 11:00:0 +0100
categories: Mozilla Thunderbird, Owncloud, Lightning, Thunderbird extensions
excerpt_separator: <!--more-->
---

Yesterday I had to set up [Mozilla Thunderbird][MozillaThunderbird], because I was setting up my new laptop, using [Ubuntu 10.15 (Wili )][UbuntuWili]. Besides the usual trade of setting up email accounts, I had to integrate my owncloud calendars and address book. Nothing really fancy but when reviewing the extensions I did notice that the birthdays from the owncloud address book do not show up in the thunderbird calendar. All the solutions provided by thunderbird extensions did not look appealing to me, but google turned up some description of a not so well documented owncloud feature. One more good thing about this feature is that it is not limited to thunderbird, but it's universal.<!--more-->

First for a short reference, I will list up the plugins used:

 * Calendar: [Thunderbird Lightning extension][Lightning]. (Download from the extensions manager within thunderbird. Does handle the CalDav synchronisation also.)
 * Address Book: 
   * For syncing the address book I do use the [Inverse SOGo Connector][SOGoFrontendDownloads]. (Download from their website and install according to [this recipe][ThunderbirdInstallExtensionFile].)
   * I also do use the [Contact Tabs extension][ContactTabsExtension].  (Maybe only a German language version? Download from the extensions manager within thunderbird.)

Now for the real deal: all extensions I did look at (not many and I did not install one of them) did not look appealing to me as they seemed to create these calendar entries on the fly. I do doubt that they do track changes in the address book properly and do clutter your calendar. Anyways some seemed to be dated and people were complaining in the comments (but where don't they anyways). Also I did get the impression that these extensions might be focused or limited to local calendars. Anyways while googling around a [nice article][BlogMehlOwncloudBirthdayCalendar] in [Max's weblog][BlogMehl] did point out that owncloud brings all the good stuff out of the box. You just have to find it:

There is a CalDav calendar accessible, which contains all the birthdays from your address book. Just import 

``` http(s)://YOUR-OC-URL/remote.php/caldav/calendars/YOUR-USER/contact_birthdays``` 

into you thunderbird as you did with your regular calendars. I do recommend do mark it as read only. Thank you [Max][BlogMehl] for pointing out.

Needless to say, that you can integrate this calendar into any Software that is able to integrate CalDav calendars. So this is the universal solution. Nicely done, ownclod!


[MozillaThunderbird]: https://www.mozilla.org/de/thunderbird/
[UbuntuWili]: https://wiki.ubuntu.com/WilyWerewolf/ReleaseNotes
[owncloud]: https://https://owncloud.org/
[Lightning]: https://addons.mozilla.org/de/thunderbird/addon/lightning/
[ThunderbirdInstallExtensionFile]: http://kb.mozillazine.org/Extensions_%28Thunderbird%29
[ContactTabsExtension]: https://addons.mozilla.org/de/thunderbird/addon/contact-tabs/
[SOGoFrontendDownloads]: http://www.sogo.nu/downloads/frontends.html
[BlogMehl]: https://blog.mehl.mx
[BlogMehlOwncloudBirthdayCalendar]: https://blog.mehl.mx/2014/birthday-calendar-with-owncloud-via-caldav/

