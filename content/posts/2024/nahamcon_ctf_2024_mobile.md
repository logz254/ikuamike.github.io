+++
title = "NahamCon CTF 2024 WriteUp - Mobile"
date = "2024-05-28T00:02:45+03:00"
author = ""
authorTwitter = "" #do not include @
tags = ["CTF", "Android", "jadx-gui", "Logcat"]
keywords = ["", ""]
description = ""
showFullContent = false
readingTime = false
hideComments = false
color = "" #color from the theme settings
toc = true
cover = "/img/nahamcon_ctf_2024/logo.jpeg"
images = ["/img/nahamcon_ctf_2024/logo.jpeg"]
+++

## Introduction
Following my previous writeup that I did for Misc challenges in the NahamCon CTF, I decided to play the challenges in the Mobile category. This is post CTF as they left the challenges up for a few days after the competition was over. 

I tried my best to not look at released writeups as I worked on these challenges as a way to learn something new or even just practice some of what I already know.

## 1. Kitty Kitty Bang Bang

This challenge is rated **Easy**.

{{< image src="/img/nahamcon_ctf_2024/kittykittybangbang_2.png" alt="" position="center" style="border-radius: 8px;" >}}

We are provided with the [apk](/files/nahamcon_ctf_2024/com.nahamcon2024.kittykittybangbang.apk) file for the challenge. I'll start by loading it into [jadx-gui](https://github.com/skylot/jadx/releases) decompiler. Looking at the Android Manifest we see app info such as the package name, activity etc.

{{< image src="/img/nahamcon_ctf_2024/kittykittybangbang_1.png" alt="" position="center" style="border-radius: 8px;" >}}

Navigating to the MainActivity and looking at the decompiled code, we note that the application logs several information including the flag. 

{{< image src="/img/nahamcon_ctf_2024/kittykittybangbang_3.png" alt="" position="center" style="border-radius: 8px;" >}}

With this knowledge, we can install the app in an emulator, I'll use genymotion and look at the logs.
When installed, the app only displays a picture of a cat.

{{< image src="/img/nahamcon_ctf_2024/kittykittybangbang_4.png" alt="" position="center" style="border-radius: 8px;" >}}

To view the logs from the app, we first get the pid of the running app.

```sh
adb shell ps | grep kitty
```

Once we have the pid, we can supply it to logcat.

```sh
adb logcat -v color --pid=2333
```

{{< image src="/img/nahamcon_ctf_2024/kittykittybangbang_5.png" alt="" position="center" style="border-radius: 8px;" >}}

With the logs streaming in, we tap on the picture of the cat.

{{< image src="/img/nahamcon_ctf_2024/kittykittybangbang_7.png" alt="" position="center" style="border-radius: 8px;" >}}

We then get the flag in the logs.

{{< image src="/img/nahamcon_ctf_2024/kittykittybangbang_6.png" alt="" position="center" style="border-radius: 8px;" >}}


## 2. Guitar

This challenge is rated **Easy**.

{{< image src="/img/nahamcon_ctf_2024/Guitar_1.png" alt="" position="center" style="border-radius: 8px;" >}}


We are provided with the [apk](/files/nahamcon_ctf_2024/com.nahamcon2024.guitar.apk) which I'll load into jadx.

Looking through the Android Manifest and decompiled code, I eventually found the flag in the strings.xml file in base64 encoded format.

{{< image src="/img/nahamcon_ctf_2024/Guitar_2.png" alt="" position="center" style="border-radius: 8px;" >}}

flag:

{{< image src="/img/nahamcon_ctf_2024/Guitar_3.png" alt="" position="center" style="border-radius: 8px;" >}}



## 3. Buggy Jumper 1

This challenge is rated **Easy**.

{{< image src="/img/nahamcon_ctf_2024/buggy_jumper_1.png" alt="" position="center" style="border-radius: 8px;" >}}

We are provided with the [apk](/files/nahamcon_ctf_2024/com.nahamcon2024.buggyjumper.apk) file for the challenge.

Starting off, I loaded the app into [jadx-gui](https://github.com/skylot/jadx/releases) and started with checking out the Android Manifest.

{{< image src="/img/nahamcon_ctf_2024/buggy_jumper_2.png" alt="" position="center" style="border-radius: 8px;" >}}

In the manifest, we see the package name, activity, service etc. We also see a reference to godot engine.

Looking at the listed activity and other parts of the application on jadx-gui didn't reveal much about the functionality of the app.

I then decided to load the app on genymotion emulator to have a feel of how it looks when it runs. It seems like a simple game.

{{< image src="/img/nahamcon_ctf_2024/buggy_jumper_3.png" alt="" position="center" style="border-radius: 8px;" >}}

When we play, we have to click on the screen severally to make the bug jump, once the bug falls we get our score and points.

{{< image src="/img/nahamcon_ctf_2024/buggy_jumper_4.png" alt="" position="center" style="border-radius: 8px;" >}}

On the shop interface, we can purchase the 'drip'. All we need is 7331,7331 points but all we managed was 2 from the short gameplay.

{{< image src="/img/nahamcon_ctf_2024/buggy_jumper_5.png" alt="" position="center" style="border-radius: 8px;" >}}

Back on the emulator, I got an adb shell and checked what local files I could find that could have been created by the app.

{{< image src="/img/nahamcon_ctf_2024/buggy_jumper_6.png" alt="" position="center" style="border-radius: 8px;" >}}

There's not much, but seeing libgodot here probably means that it's important to the running of the app. Back on jadx-gui, looking at the resources/assets we have the libgodot in lib folder and also in the assets/scripts folder we have flag.gdc. This is possibly the flag but it's not readable here.

{{< image src="/img/nahamcon_ctf_2024/buggy_jumper_7.png" alt="" position="center" style="border-radius: 8px;" >}}

After some research, gdc file format is godot engine compiled script. Godot engine is a game engine. I found that we can decompile the compiled script using Godot reverse engineering tools - [gdsdecomp](https://github.com/bruvzg/gdsdecomp).

With the tool downloaded, we can provide the apk directly to perform the decompilation.

```sh
./gdre_tools.x86_64 --recover=../com.nahamcon2024.buggyjumper.apk
```

{{< image src="/img/nahamcon_ctf_2024/buggy_jumper_8.png" alt="" position="center" style="border-radius: 8px;" >}}

This will create a new folder with the all the gdc files in the app decompiled. In the scripts folder, we get the flag now in plain text. 

{{< image src="/img/nahamcon_ctf_2024/buggy_jumper_9.png" alt="" position="center" style="border-radius: 8px;" >}}


## 4. Buggy Jumper 2

This challenge is rated **Medium**.

{{< image src="/img/nahamcon_ctf_2024/buggy_jumper_10.png" alt="" position="center" style="border-radius: 8px;" >}}

This is a follow up of the previous challenge and we should use the same app. From the description, the focus is in the shop.

In the shop.gd file, we see the code for the shop.

{{< image src="/img/nahamcon_ctf_2024/buggy_jumper_11.png" alt="" position="center" style="border-radius: 8px;" >}}

To buy the drip, a web request is sent to a url with an auth token and the value of points the response from this /verify endpoint will give the flag. The url is provided in the global.gd file.

{{< image src="/img/nahamcon_ctf_2024/buggy_jumper_12.png" alt="" position="center" style="border-radius: 8px;" >}}

There's more code in the global.gd file. After reading the code, I understand the sequence needed to get the flag. 

Below is the summary:

- Register via a POST request to the /register endpoint to get an auth token. The string **Godot** is required to be in the user agent.
- Save points via a POST request to the /save endpoint. We need to set the points to be >=73317331 and in json format.
- Make a request to /verify providing points >=73317331 to get the flag.

{{< image src="/img/nahamcon_ctf_2024/buggy_jumper_13.png" alt="" position="center" style="border-radius: 8px;" >}}

An alternative option to get the flag, is by letting the app make the request for us. We can manipulate the score value as this is stored in a local file **saved_value.dat**. We have seen this referenced in the code and in the local files on the emulator.

```sh
echo 73317331 > ./data_mirror/data_ce/null/0/com.nahamcon2024.buggyjump/files/saved_value.dat
```
Once we restart the app, we can get the flag.

{{< image src="/img/nahamcon_ctf_2024/buggy_jumper_14.png" alt="" position="center" style="border-radius: 8px;" >}}

## Conclusion

The mobile app challenges were quite nice to go over, thankful to the NahamCon CTF team that they left the challenges accessible post event.

There are 2 challenges that I didn't manage to solve. I realized the app was written in flutter and it seems there's a unique step by step for testing apps written in flutter. I'll be digging deeper to learn how to work with apps made in flutter.

Here is a link to another writeup that covers the remaining 2 challenges:

[https://hackmd.io/@avila-pwn-notes/r183kzlEA](https://hackmd.io/@avila-pwn-notes/r183kzlEA)


