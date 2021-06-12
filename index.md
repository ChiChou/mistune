---
layout: default
title: "Mistune Bug"
image: /assets/thumbnail.png
description: "Mistune is a full chain exploit targeting iOS 14 on iPhone 11, successfully demostrated at TianfuCup 2020. It consists of two bugs that were introduced by iOS 3 and iOS 6 respectively."
---

# What's Mistune?

Mistune is a full chain exploit targeting iOS 14 on iPhone 11, successfully demostrated by [@codecolorist](https://twitter.com/codecolorist) at TianfuCup 2020. It consists of two bugs that were introduced by iOS 3 and iOS 6 respectively.

Full chain remote code execution means that by clicking on a malicious link (wherever it comes, such as mail, direct messages and AirDrop), the attacker may be able to access your Contacts, Camera, Payments and load further kernel exploit payload to gain full control of the phone.

Although the exploit is delievered via traditional web browser 1-click vector, the bugs are unique compared to known WebKit exploits.

## Is it a jailbreak?

Nope. I'm a noob and I know nothing about kernel exploitation. It's just userspace exploits.

## Were the bugs patched?

**Yes**.

After direct disclosure to the vendor, [CVE-2021-1748](https://support.apple.com/en-us/HT212146) was patched by iOS 14.4 and [CVE-2021-1864](https://support.apple.com/en-us/HT212317) was gone after iOS 14.5. Those bugs were probably never been exploited in-the-wild, but you should always keep your phone updated to avoid potential attacks.

> iTunes Store
>
> Available for: iPhone 6s and later, iPad Pro (all models), iPad Air 2 and later, iPad 5th generation and later, iPad mini 4 and later, and iPod touch (7th generation)
>
> Impact: Processing a maliciously crafted URL may lead to arbitrary javascript code execution
>
> Description: A validation issue was addressed with improved input sanitization.
>
> CVE-2021-1748: CodeColorist working with Ant Security Light-Year Labs

> iTunes Store
>
> Available for: iPhone 6s and later, iPad Pro (all models), iPad Air 2 and later, iPad 5th generation and later, iPad mini 4 and later, and iPod touch (7th generation)
>
> Impact: An attacker with JavaScript execution may be able to execute arbitrary code
>
> Description: A use after free issue was addressed with improved memory management.
>
> CVE-2021-1864: CodeColorist of Ant-Financial LightYear Labs

## Everyone is born unique. What makes it special?

Usually an exploit starts from WebKit, loads shellcode and try to exploit other bugs to escalate the privilege. Both of the bugs of Mistune were triggered and exploited in Javascript, but they were targeting a preinstalled app, iTunes Store, instead of MobileSafari.

Mistune could directly escape browser sandbox before native code execution (CVE-2021-1748). In fact, it's capable of reading sensitive information and launching arbitrary app (including our beloved Calculator) with zero memory corruption, making it invincible to modern memory safety and control-flow integrity mitigations.

A second memory safety issue (CVE-2021-1864) was used to gain full shellcode execution. Interestingly, it was not possible to trigger this without the first sandbox bug, while you can always influently replace this part with any working WebKit exploit.

iOS remained standing at various pwn competitions after Pointer Authentication Code (PAC) was shipped by A12 chips. TianfuCup 2020 was the very first successful event that has this category pwned.

## What exact mitigations have been bypassed?

### Pointer Authentication Code (PAC)

This is the most remarkable protection for the challenge. With hardware-assisted control-flow integrity, it was hard for most known browser exploit primitives to work.

### Sandbox

MobileSafari has Javascript running in a very limited containerized process. The exploit uses a client-site XSS to easily switch to a loosy context where Just-in-time is still avaliable.

### Transparency Consent and Control (TCC)

Contacts, MediaLibrary and Camera are protected by TCC framework. Third party apps and web sites need user's approvement before accessing them. This exploit got shellcode executed in a very special context where those accesses are explicitly granted.

### Hardened JIT

Hardened Just-in-Time compiler leverages special system registers to implement `W^X` policy to JITed code, to protect it from being altered by arbitrary memory read and write. The exploit had arbitrary shellcode successfully executed.

### Other WebKit mitigations

As the exploit was not really targetting WebKit itself, protections like Gigacage, PACCage, StructureID Randomization are not the main concerns.

### Objective-C Runtime

Objective-C has introduced several little-known protections, including runtime obfuscation and randomization. The exploit involves Objective-C runtime features and has bypassed some of them at the time of the competition.

I'm so glad to see some new improvement by 14.5 effectively stops many of them after the report.
