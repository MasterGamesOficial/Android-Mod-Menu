**This template is NOT for newbies/noobs, it is for experience modders and programmers only. You will be expected to read, learn and even google. If you don't have the knowledge, this tutorial will be too hard for you**

# Quick links
- [Pre-requisites](#pre-requisites)
- [What will you need?](#what-will-you-need)
- [download/clone](#downloadclone)
- [Install NDK](#install-ndk)
- [Open the project](#open-the-project)
- [Files to work with and making changes](#files-to-work-with-and-making-changes)
- [Implementing the menu to the target game](#implementing-the-menu-to-the-target-game)
- [Loading lib without mod menu](#loading-lib-without-mod-menu)
- [FAQ](#faq)
- [Reporting issues](#reporting-issues)
- [Useful links](#useful-links)
- [Credits/Acknowledgements](#creditsacknowledgements)

# Introduction
Simple floating mod menu with sounds for il2cpp and other native android games, KittyMemory, MSHook, and And64InlineHook included. This template is optimized for modders who want the faster way to implement the menu in the game without hassle. Assets are stored as base64 in java/smali and does not need to be stored under assets folder.

It comes with string and offset obfuscation without using any external tool and without modifying the compiler. We use AY Obfuscator

Support Android 4.2.x way up to Android R preview. Support ARMv7, x86 and ARM64 architecture. However x86 is deprecated for Unity games so x86 is not our priority

Mod menu is based on Octowolve/Escanor and Van's template. 

Preview:

![](https://i.imgur.com/CoI38ER.gif)

# Pre-requisites:
- Basic knowledge of C++, Java, dalvik opcodes (smali), ARM and ARM64 assembly. x86 is optional
- Be able to patch hex and hook
- Understanding how Android development works

# What will you need?
- Android Studio 4 and up: https://developer.android.com/studio
- Git if you want to clone a project though Android Studio (Optional): https://git-scm.com/download
- [Apktool.jar](https://ibotpeaches.github.io/Apktool/) or any 3rd party tools: [APK Easy Tool](https://forum.xda-developers.com/android/software-hacking/tool-apk-easy-tool-v1-02-windows-gui-t3333960), [Jasi Toolkit](https://jaspreetsingh.store/jasi-toolkit/), or [INZ APKTool](https://forum.gsmhosting.com/vbb/f831/inz-apktool-2-0-windows-gui-apk-tool-2722815/)
- Any text editor: [Notepad++](https://notepad-plus-plus.org/downloads/), [Subline](https://www.sublimetext.com/) or [Visual Studio Code](https://code.visualstudio.com/)
- Any png compression to compress your png file: https://compresspng.com/
- Any base64 encoding to encode your file: https://www.base64encode.org/
- Any audio converters to convert your sound files to .ogg (Optional): [XMedia Recode](https://www.xmedia-recode.de/en/download.php)
- This template of course

# Download/Clone
Click on the button that says Code, and click Download ZIP

![](https://i.imgur.com/EZnbd10.png)

Or clone through Android Studio itself (Requires Git to be installed on your machine and be configured in Android Studio)

Click on "Get from Version Control"

![](https://i.imgur.com/y8haBYN.png)

Input the git url and Clone

![](https://i.imgur.com/z8KkW8S.png)

# Install NDK
At the bottom-right corner, click on Configure and SDK Manager

![](https://i.imgur.com/xBP1bCE.png)

Select **Android SDK**, check **NDK (Side by side)** and click OK. It will download and install

![](https://i.imgur.com/FcAd2Px.png)

# Open the project 
Once you've downloaded all the necessary files, extract the template project to the folder without any spaces. If any folder has spaces, it will cause problem

On Android Studio on the welcome screen, choose **"Open an existing Android Studio project"**

Navigate to the extracted project and open it

![](https://i.imgur.com/3etm4qX.png)

It will index and Gradle will sync the project fir the first time. Please wait for a while, it will take around 5 minutes depending your computer performance

If you encounter an error 

```NDK not configured. Download it with SDK manager. Preferred NDK version is 'xx.x.xxxxxx'```

Click File and Project Structure

![](https://i.imgur.com/EWhLe7M.png)

Select default NDK version

![](https://i.imgur.com/YfmIvoN.png)

After it's done, you can start working!

# Files to work with and making changes

On the left side, you see your project view. Default view is Android

![](https://i.imgur.com/YT71Y6B.png)

**`LoadLib.java`**

To call toast if you load lib without mod menu

**`MainActivity.java`**

Starts the main activity. No need to use if you implement the menu in the APK file

**`Preferences.java`**

Saving the menu feature preferences and calling changes via JNI

**`FloatingModMenuService.java`**

The codes of floating mod menu. You don't need to change much unless you want to redesign it. The codes are explained in the comments (//...)

- GradientDrawable

A code for setting corner and stroke/inner border. Works for any View Components
```
GradientDrawable gradientdrawable = new GradientDrawable();
gradientdrawable.setCornerRadius(20); //Set corner
gradientdrawable.setColor(Color.parseColor("#1C2A35")); //Set background color
gradientdrawable.setStroke(1, Color.parseColor("#32cb00")); //Set border
```

Set the gradient drawable to the component

```
[name of your view component].setBackground(gradientdrawable);
```

- Resizing menu box

This is the code that set the size of the box. You can change it manually example (500, 500).

```mExpanded.setLayoutParams(new LinearLayout.LayoutParams(MATCH_PARENT, WRAP_CONTENT));```

Note: You must implement DPI to auto size due to many types of phone with different DPIs and resolutions

**`StaticActivity.java`**

To initialize by game activity's OnCreate
Checks if device running Android 6.0 or above and if have overlay permission checked. Sounds being written to the cache directory.
Start() will be called when implementing the menu to the game. We will explain later

- writeToFile:
Decode base64 and write to file to a target directory

**`Menu/Sounds.h`**

Basically the menu sounds, that have been converted to .ogg using XMedia Recode and encoded to base64. They are automatically decoded and stored into /data/data/(package name)/cache upon startup (See StaticActivity). Remember, we want to avoid storing files under assets as possible

**`Menu/Menu.h`**

This is menu related
- Title: Big text

- Heading: Little text

- Icon: Compressed image that is encoded to base64

- IconWebViewData: Use icon in Web view with GIF animation support. URL requires internet permission `android.permission.INTERNET`

```From internet: (Requires android.permission.INTERNET)
return env->NewStringUTF("https://i.imgur.com/SujJ85j.gif"); 

From assets folder: (Requires android.permission.INTERNET)
return env->NewStringUTF("file:///android_asset/example.gif"); 

Base64 html:
return env->NewStringUTF("data:image/png;base64, <encoded base64 here>");

Nothing:
return NULL
```

- Toast: To get text from c++ in order to show toast in java

- getFeatureList: Here you add the mod features

**`Toast.h`**

Your toast

**`Main.cpp`**

In this file, you will mostly do implementation with your codes for modding

- Changes: Get changes of toggles, seekbars, spinner and buttons to do modding. Features MUST be count from 0

Usage:

```
Toggle_[feature name]
SeekBar_[feature name]_[min value]_[max value]
Spinner_[feature name]_[Items e.g. item1_item2_item3]
Button_[feature name]
Button_OnOff_[feature name]
InputValue_[feature name]
```

Examples:

```Toggle_God mode
Spinner_Weapons_AK47_9mm_Knife
Button_OnOff_God mode
```

Do not forget to count your features from 0 and remember them

- hack_thread: Here you add your code for hacking with KittyMemory or Hooking. We will not teach, you must have learned it already

- JNI_OnLoad: Initialize when the library loads

KittyMemory usage:
```MemoryPatch::createWithHex([Lib Name], [offset], "[hex. With or without spaces]");
[Struct].get_CurrBytes().Modify();
[Struct].get_CurrBytes().Restore();

[Struct].get_TargetAddress();
[Struct].get_PatchSize();
[Struct].get_CurrBytes().c_str();
```

Example: https://github.com/MJx0/KittyMemory/blob/master/Android/test/src/main.cpp

Hook usage:
ARM64:
```A64HookFunction((void *) getAbsoluteAddress([Lib Name], [offset]), (void *) [function], (void **) &[old function]);```

ARMv7/x86:
```MSHookFunction((void *) getAbsoluteAddress([Lib Name], [offset]), (void *) [function], (void **) &[old function]);```

**`Android.mk`**

The make file for the c++ compiler. In that file, you can change the lib name on the `LOCAL_MODULE` line
When you change the lib name, change also on `System.loadLibrary("")` under OnCreate method on FloatingModMenuService.java
Both must have same name

**`proguard-rules.pro`**

Java obfuscation is enabled by default with an exception to `public static void Start`

Add `-dontobfuscate` to disable obfuscation

**C++ string obfuscation**

We use AY Obfuscator but the usage has changed to `OBFUSCATE("string here")` and `OBFUSCATE_KEY("string here", 'single letter here')`

**Encoding your files into base64**

You can pretty much use any tools for base64 encoding.

We use a simple website https://www.base64encode.org/

Scroll down till you see `Encode files into Base64 format`. Click or tap on the box to select a file

Click on `ENCODE` button and click on `CLICK OR TAP HERE` to download your encoded file. Now you can paste it in your code

# Testing

If you have your device with adb enabled, connected your PC or your emulator with adb enabled. Android Studio will detect and you can click Play to run your app onto your device/emulator

Most emulators will have adb enabled by default

![](https://i.imgur.com/ZegjeM8.png)

To use adb, you must enable USB debugging in the device system settings, under Developer options.

On Android 4.2 and higher, the Developer options screen is hidden by default. To make it visible, go to **Settings** > **About phone** and tap Build number seven times. Return to the previous screen to find Developer options at the bottom.

On some devices, the Developer options screen might be located or named differently.

# Implementing the menu to the target game

**KNOW YOUR GAME'S MAIN ACTIVITY**

Now we are looking for main activity, there are 2 ways to do

1. Decompile the game's APK file. Open `androidmanifest.xml` and search after `<action android:name="android.intent.action.MAIN"/>`.

Example the game's main activity was `com.unity3d.player.UnityPlayerActivity`

![](https://i.imgur.com/FfOtc1K.png)

Be sure to enable Word wrap so it is easier to read

![](https://i.imgur.com/7DzU8d0.png)

2. APK Easy Tool since it can read out location of main activity without decompiling APK

![](https://i.imgur.com/JQdPjyZ.png)

Note it somewhere so you can easly remember it

**CALLING YOUR MOD MENU ACTIVITY**

Decompile the game APK

There are 2 ways to call your mod menu activity. Choose one of them you like to try. Don't know? just choose METHOD 1

###### METHOD 1

This simple way, we will call to `StaticActivity.java`. `MainActivity.java` will never be used

Locate to the game's path of main activity and open the **smali** file. If the game have multi dexes, it may be located in smali_classes2.. please check all

Example if it was `com.unity3d.player.UnityPlayerActivity` then the path would be `(decompiled game)/com/unity3d/player/UnityPlayerActivity.smali`

Open the main acitivity's smali file, search for OnCreate method and paste this code inside (change the package name if you had changed it)
```
invoke-static {p0}, Luk/lgl/modmenu/StaticActivity;->Start(Landroid/content/Context;)V
```
 
![](https://i.imgur.com/7CxTCl8.png)

Save the file

###### METHOD 2

Warning: This method is not recommended. Using this method may cause problems with activity. Only use it if the first method really fails, or if you really want to use `MainActivity.java` for a reason

On Android Studio, put the game's main activity to `public String GameActivity`

![](https://i.imgur.com/jdacwvH.png)

On `androidmanifest.xml`, near the end of application tag, add your new main activity above `</application>`. `uk.lgl.modmenu.MainActivity` is your main activity

```xml
<activity android:configChanges="keyboardHidden|orientation|screenSize" android:name="uk.lgl.modmenu.MainActivity">
     <intent-filter>
         <action android:name="android.intent.action.MAIN"/>
         <category android:name="android.intent.category.LAUNCHER"/>
     </intent-filter>
</activity>
```

![](https://i.imgur.com/33lMPhc.png)

Save the file

Do NOT use both methods in the same game

**BUILDING YOUR PROJECT AND COPYING FILES**

Build the project to the APK file.
**Build** -> **Build Bundle(s)/APK(s)** -> **Build APK(s)**

If no errors occured, you did everything right and build will succeded. You will be notified that it build successfully

![](https://i.imgur.com/WpSKV1L.png)

Click on **locate** to show you the location of **build.apk**. It is stored at `(your-project)\app\build\outputs\apk\ app-debug.apk`

![](https://i.imgur.com/wBTPSLi.png)

Decompile your **app-debug.apk**.

Copy your mod menu from decompiled app-debug.apk smali to the game's smali folder. Example ours is uk.lgl.modmenu, we copy the `uk` folder from **app-debug** `(app-debug\smali\uk)` to the game's decompiled directory `(game name)\smali`

![](https://i.imgur.com/aO6eEab.png)
 
Very important for multi dex games. Let's say if main activity is located in **smali_classes2**, we would put my mod menu in **smali_classes2**

Copy the library file (.so) from **app-debug.apk** to the target game. Make sure to copy .so to the correct architecture
armeabi-v7a is armeabi-v7a, arm64-v8a is arm64-v8a, and so on.

PUTTING THE .SO file ON A WRONG ARCHITECTURE WILL RESULT A CRASH
 
![](https://i.imgur.com/oZq1Wq7.png)

**CHANGING FILES**

Open the game's `androidmanifest.xml`
Add the permission besides other permissions
```
<uses-permission android:name="android.permission.SYSTEM_ALERT_WINDOW"/>
```

![](https://i.imgur.com/XOxLU91.png)

Add the service above the end of application tag (change the package name of your menu if you had changed it)
```
<service android:name="uk.lgl.modmenu.FloatingModMenuService"
        android:enabled="true"
        android:exported="false"/>
```

![](https://i.imgur.com/rw0hawa.png)

Save the **AndroidManifest.xml** file
 
**COMPILING GAME APK**
 
Now compile and sign the apk
If compile fail, read the log and look up on Google

If the mod menu appears and the hack are working, congratz!

If you face any problem, be sure to check the logcat, and if it was native related, write the log such as `LOGD("whatever");` in your cpp codes, recompile and capture the logcat. See what part of your code faced the problem. Logcat may also tell you if hooking fails (lib crash)

# Loading lib without mod menu

Just call the LoadLib in the OnCreate method
```
    invoke-static {p0}, Luk/lgl/loadlib/LoadLib;->Start(Landroid/content/Context;)V
```

And uncomment the isToastCalled check in hack_thread function

Make sure to delete `modmenu` folder from the smali to avoid reaching the method limit of the smali classes (limit is 65535)

# FAQ
### I have a problem decompiling and compiling APK file

Check if apk is not protected. If not, search for the related issues on Google or on Apktool Github page: https://github.com/iBotPeaches/Apktool/issues

### I'm getting an error `ERROR: executing external native build for ndkBuild Android.mk. Affected Modules: app`

Install the NDK first and make sure your path does NOT contain spaces

### More than one file was found with OS independent path (.so file)

If you have `jniLibs` folder on `\app\src\main`, delete it.

![](https://i.imgur.com/Y5Ze1XH.png)

### I'm getting an error `Unsigned short value out of range: 65536` if I compile

The method index can't fit into an unsigned 16-bit value, means you have too many methods in the smali due to the limit 65535. Place your code on other classes, such as smali_classes2 instead. This work for Android 5 (Lollipop) and above only.

### I'm getting strange issues on Android Studio or Gradle

There are millions of reason why you are getting an error on Android Studio. Bacisally, search on Google for the answer. If you can't find a solution on Google, try invalidate caches. Click **File** -> **Invalidate Caches/Restart**. Let it load. In the critical cases, you may need to reinstall Android Studio

See troubleshooting: https://developer.android.com/studio/troubleshoot

See known issues: https://developer.android.com/studio/known-issues

### How to add colored text on JNI toast?
It is not implemented yet, and we don't have enough knowledge in JNI porting to do this

But it is deprecated in API level 30/Android 11, means custom toast will not work, so we will not implement it

See: https://developer.android.com/reference/android/widget/Toast#getView()

### How can I protect my dex and/or lib?

There are only chinese based tools we heard so far but they are super slow and we never manage to use them. We highly suggest to not use them, because we don't know if they contain malwares, spywares, you know, rumours about chinese spying. Protecting APK may end up including additional spywares and may result getting flagged by some anti-virus, so use it are your own risk. Do not be offended, i'm just warning

I will not mention their service names. Please don't ask me for it.

But there is no need to protect dex since there are nothing important in java/smali codes. All the important codes such as offsets are in the lib file and they are protected enough

### How to get older version of the template?

Go to the commit page https://github.com/LGLTeam/Android-Mod-Menu/commits/master

### Can I compile this project on Android using AIDE or other apps?

Likely yes and no, but we don't support compiling project on Android device. Please do not ask for help

### How can I соntact you?
You can соntact me via Tеlеgram @ThеᒪGᒪ or Disсоrd ᒪGᒪ#1066

Newbies who do not understand anything should NOT соntact me. You will be blocked if you ask/beg me to teach/spoonfeed. Don't assume i'm a teacher, i'm NOT a teacher :P

Speak english only please

### Can you help me mod (name of game) or can you do mod service?

Noob, we are not spoonfeeding. Don't соntact if you don't know how to mod games.
                              
Instead, try to find a couple of tutorials to learn and mod the game yourself. It's a lot easier than you think. If you can't, search on the internet and you should find a couple of forums such as Platinmods where you can ask your questions regarding game modding.

### Do you have project of someones mod menu including game codes for example MITO Team mod?

No, because they used this template and they created their own mod with it, we don't support nor work with them. Ask the right owner who have them, example if mod is created by MITO Team, ask MITO Team, NOT me. I, we LGL Team are the wrong persons to ask.

# Reporting issues

You can report it here https://github.com/LGLTeam/Android-Mod-Menu/issues

Please give link to the APK and provide logcat from Android Studio as possible

Best way is to соntact me privately. See above   

# Useful links

* https://piin.dev/basic-hooking-tutorial-t19.html

* https://iosgods.com/topic/65529-instance-variables-and-function-pointers/

* https://guidedhacking.com/threads/android-function-pointers-hooking-template-tutorial.14771/

* http://www.cydiasubstrate.com/api/c/MSHookFunction/

* https://www.cprogramming.com/tutorial/function-pointers.html

# Credits/Acknowledgements
Thanks to the following individuals whose code helped me develop this mod menu

* Octowolve/Escanor - Mod menu: https://github.com/z3r0Sec/Substrate-Template-With-Mod-Menu and Hooking: https://github.com/z3r0Sec/Substrate-Hooking-Example
 
* VanHoevenTR - Mod menu - https://github.com/LGLTeam/VanHoevenTR_Android_Mod_Menu

* MrIkso - First mod menu template https://github.com/MrIkso/FloatingModMenu

* MJx0 A.K.A Ruit - https://github.com/MJx0/KittyMemory

* Rprop - https://github.com/Rprop/And64InlineHook

* Google - Android UI sounds

* Material.io - https://material.io/design/sound/sound-resources.html#

* Platinmod modders for suggestions and ideas, and the solution of fixing compilation errors :)