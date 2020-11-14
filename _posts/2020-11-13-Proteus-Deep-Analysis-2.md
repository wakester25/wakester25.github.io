---
layout: post
title: Deep Analysis 2 - Proteus 
---

![_config.yml]({{ site.baseurl }}/images/proteus_deep_two/deep_analysis_logo.jpg)

So this entry should be relatively short. I think this is going to be my last post on Proteus for now as frankly I am tired of looking at the same malware and feel myself losing motivation. I have some other topics I want to dive into and I feel for my first attempt at analyzing malware I got pretty far! While I didn’t get all the finer details ironed out I am at least happy I was able to get the overall gist of what the malware did, which is a win in my book! 

So to update you where I am at thus far, I pretty much mapped out the main Proteus executable. In doing so I discovered that the malware had the following capabilities:

   * CryptoMining
   * KeyLogging via keyboard hook
   * Account Checking 
   * Create numerous files / directories
   * Accept commands from botnet / download additional files
   * XOR encryption 
   * Fingerprint device
   * Anti debug / sandbox 

## Flow chart of malware execution

<img src="{{ site.baseurl }}/images/proteus_deep_two/flow_chart.PNG">

Each of these functions are started as individual threads. There is a main function that runs an infinite loop around a switch statement that is responsible for starting each process. Writing the malware this way means that even if an exception is caught, the malware won’t exit and will run indefinitely until manually exited. This is actually why we didn’t observe any functionality when performing dynamic analysis. Since the very first case is to try and register the botnet, which has since been taken down, the malware will never make it past that case. Now I could manually go in and patch the program to skip over this function, but since most of the malware was disassembled to source code I didn’t find that necessary. When the program actually registers your device to the botnet it creates a unique string based on a device “fingerprint”. This fingerprint is composed of device data such as cpu and BIOS information. Doing this allows the botnet to differentiate your machine from other machines that are registered. Once this fingerprinting / registration happens the malware simply runs through each of the remaining case statements to kick off each of the capabilities listed above. 

## Main function / case statement and fingerprinting function 

<img src="{{ site.baseurl }}/images/proteus_deep_two/main_function.PNG">

<img src="{{ site.baseurl }}/images/proteus_deep_two/fingerprint.PNG">

One other interesting item that I found within the program was that it used a custom XOR function to store strings. This is so that anyone performing basic static analysis can’t look at the strings stored within the program. However simply stepping through the function manually I was able to deconstruct that the strings stored were simply the parameters needed to register the botnet (URL, user agent, POST commands, ect) and other uniquely identifying commands.

## XOR function and encrypted strings

<img src="{{ site.baseurl }}/images/proteus_deep_two/xor.PNG">

<img src="{{ site.baseurl }}/images/proteus_deep_two/encrypted_strings.PNG">

That is pretty much where I decided to leave things off. There were two remaining DLLs, but they pretty much just had to do with anti-disassembly and SSH. I may dive into these deeper another time but I decided to leave them alone for now. These DLLs can be viewed more as accessories to the main program and I feel confident I hit the major capabilities of the main executable. Now this malware also does have the ability to download additional files, but since the botnet has been shut down there is no way to retrieve these. 

So this is where my journey in tackling my first piece of malware ends. It took me a lot longer than it should have to get to this point but overall I am happy with the progress I was able to make. I was able to take a piece of malware, perform basic static and dynamic analysis, extract IOCs, deobfuscate and even start diving into some deeper analysis. Going forward I now plan to try and tackle a true x86 executable. As this application was written in .NET I was able to get back source code whereas normally I would be left with assembly. I feel performing deep analysis on assembly is a whole different animal and is thus why I am setting this as my next challenge. So I hope you enjoyed this series and as always, happy hunting. 
