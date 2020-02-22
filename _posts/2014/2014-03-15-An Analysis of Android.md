---
layout: post  
tags: [Android, Mobile, Malware, Security]
categories: [Research]
permalink: /post/malware-analysis-android-adsms
---

Recently, I am working on Android Malware Spreading project, revealing why so many devices got infected and under what situation they ware infected. Though I did some analysis on PC malware before this, I am still a new player in   the world of Android OS and its applications. I should do more practice.  

This post is a brief analysis about a worm-like android malware. I'd like to pay more attention on the how it spread out and how  malicious behaviors work.

## 1. Summary

Android.AdSMS is an Android SMS Trojan malware, discovered in April 2011, **targeting to China Mobile users**. It spreads out **through sending SMSes containing a certain download link**. It **infects devices by inducing users to click the link, download and install the malious fake patch update**. Particularly, the fake patch file is named as `htc.apk` or `update.apk`, etc.

## 2. General Details

**Note**: These details came from a perspective of malware behaviour characteristics, rather than the common perspective of an malware analyst

### 2.1 Declared Permissions

...

### 2.2 Registered Broadcast Receivers

Receivers declared in `AndroidManifest.xml`:

	android.provider.Telephony.SMS_RECEIVED
	android.intent.action.PHONE_STATE
	android.intent.action.BOOT_COMPLETED
	android.net.conn.CONNECTIVITY_CHANGE
	android.intent.action.WEB_SEARCH
	android.server.checkin.FOTA_READY
	android.server.checkin.FOTA_RESTART
	android.intent.action.PACKAGE_ADDED
	android.intent.action.PACKAGE_REMOVED

The related Receiver class is `SystemEventReceiver`.

*Generally, there two ways to make a broadcast receiver known to the system: one is declare it in the manifest file, with `Receiver` element; the other is to create the receiver dynamically in code and register it with the `Cotext.registerReiver()` method.[Ref. 1 [receiver](http://developer.android.com/guide/topics/manifest/receiver-element.html)]*

### 2.3 Content Observer

A ContentObserver named `SMSObserver` is defined in *com.android.SMSObserver.java*. This `SMSObserver` is registered in `BootService`'s construct method by calling to `addSMSObserver`, and has the capability to monitor the changes of SMS content. Once some changes are made, the member method [`onChange`](http://developer.android.com/reference/android/database/ContentObserver.html#onChange(boolean) will be called automatically.

	public void addSMSObserver() {
        ContentResolver resolver = getContentResolver();
        this.mObserver = new SMSObserver(resolver, new SMSHandler(this), getApplicationContext());
        resolver.registerContentObserver(Uri.parse("content://sms"), true, this.mObserver);
    }

*A registered ContentObserver is able to receive callbacks for changes to the content specified by URI.[Ref 2 [ContentObserver](http://developer.android.com/reference/android/database/ContentObserver.html)]*

### 2.4 Timer——AlarmManager

A Timer service named `TimmerService` was intended to be registered in **`StartService`**'s method `StartAlarm`. 

	 ((AlarmManager) ct.getSystemService("alarm")).setRepeating(1, 0, 86400000, PendingIntent.getService(ct, 0, new Intent(ct, TimmerService.class), 0));

However, from the reversed Java source code, we can find that, this Timer service never worked since the method `StartAlarm`, used for registering the Timer, was never called.

*Aka, the [`AlarmManager`](http://developer.android.com/reference/android/app/AlarmManager.html) is intended for cases where you want to have your application code run at a specific time, even if your application is not currently running. [Ref. 3 [AlarmManager](http://developer.android.com/reference/android/app/AlarmManager.html)]*


### 2.5 Time Intervals/Timeline of Behaviours

* **24h**  The interval for updating or querying the `SMSConfig.XML`*(the config file)*
* **7,500ms** The interval for sending the `pushcmd`
* **15,000ms** The interval for sending messages for `channels`
* **86400000ms(24h)** The interval of a **timer** for start major service--`StartDevice`. 

### 2.6 Device Information Collected

* IMEI;
* TELNUMBER
* PhoneModel;
* SysSDK-->Version.SDK
* RELEASE-->Version.Release

## 3. Control Flow of Main Components

### 3.1 Activity

![Imgur](http://i.imgur.com/axfjhsQ.png)

### 3.2 Receivers

![Imgur](http://i.imgur.com/50EajR5.png)

### 3.3 `StartDevice` service and `AlarmManager`
![Imgur_activity](http://i.imgur.com/x9OvbiT.png)

## 4. Others

#### 4.1 The Message
	""敬中国移动用户，您的手机存在系统安全漏洞，为了提高手机安全级别，请下载更新补丁！http://1oo86.net/  中国移动""

#### 4.2 SMSConfig.XML file

![Imgur](http://i.imgur.com/S8LoQXI.png)


## References


1. Receiver. [http://developer.android.com/guide/topics/manifest/receiver-element.html](http://developer.android.com/guide/topics/manifest/receiver-element.html)

2. ContentObserver. [http://developer.android.com/reference/android/database/ContentObserver.html#onChange(boolean)](http://developer.android.com/reference/android/database/ContentObserver.html)

3. AlarmManager. [http://developer.android.com/reference/android/app/AlarmManager.html](http://developer.android.com/reference/android/app/AlarmManager.html)

4. [http://ae.norton.com/security_response/print_writeup.jsp?docid=2011-051313-4039-99](http://ae.norton.com/security_response/print_writeup.jsp?docid=2011-051313-4039-99)
5. [http://www.hauri.net/security/mobile_view.html?intSeq=1910&strPart=&key=&cpage=1](http://www.hauri.net/security/mobile_view.html?intSeq=1910&strPart=&key=&cpage=1)
6. [http://about-threats.trendmicro.com/Malware.aspx?language=tw&name=ANDROIDOS_ADSMS.A+](http://about-threats.trendmicro.com/Malware.aspx?language=tw&name=ANDROIDOS_ADSMS.A+)
7. [http://blog.sina.com.cn/s/blog_5e96245b0100rndc.html](http://blog.sina.com.cn/s/blog_5e96245b0100rndc.html)

