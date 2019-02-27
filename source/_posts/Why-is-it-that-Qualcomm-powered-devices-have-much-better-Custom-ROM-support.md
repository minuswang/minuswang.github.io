---
title: Why is it that Qualcomm powered devices have much better Custom ROM support?
date: 2019-02-27 15:05:36
tags:
---


Jeremy O'Donoghue, Principal Engineer at Qualcomm (2002-present)
Answered May 23, 2017 · Author has 88 answers and 85.2k answer views

You need two things on a device if you want to have good support for Custom ROMs:

A boot loader that can be easily, safely and reliably unlocked;
A complete and reliable kernel for your device.
The first is mainly in the hands of the device manufacturer. The Android boot loader, at least as supported on Qualcomm devices, can be very strongly locked down to prevent custom code from being loaded, but this is an option that can be enabled or disabled by the phone OEM.

The second is in the hands of the vendor(s) of the silicon in the device. I cannot speak for other silicon vendors, but there are two factor that make Qualcomm devices particularly good in this respect.

Qualcomm is principled about publishing the complete source code for its kernels - it complies fully with GPL requirements to make all kernel source code available, and publishes many of the other changes it makes to the base Android system.
Most OEMs who use Qualcomm parts also base their phones closely on the Qualcomm reference design, which means that items such as touch and display controllers (which Qualcomm does not make) have open source drivers in the kernel.
The result of all this is that you can compile an Android build for a Qualcomm device based on code which has been published and tested by Qualcomm - and Qualcomm does a huge amount of testing - this makes a great and stable base for a custom ROM.

Some manufacturers use binary drivers or do not publish complete kernel sources - it will inevitably be far more difficult to develop custom ROMs for such devices.

I always like to give one warning to custom ROM users though: your phone carries a huge amount of your personal data including most of your passwords, your contacts and your e-mail. Using a custom ROM for which you did not carefully check the source code and compile yourself means that you are 100% trusting the creator of the ROM not to steal or abuse your personal data.


Raja Manickam, Knows a lot about android flashing, custom ROMs, kernels etc
Updated Oct 22, 2017 · Author has 142 answers and 227k answer views

As we all know, any manufacturer which produces an Android smartphone/device should release the kernel source code as part of the GPL agreements. Generally there are companies which do this quickly, those who stall and eventually release, and those who keep their money grabbing hands locked tightly together.

Mediatek are the money grabbing type and to date have not released source code for their current chipsets which include the quad-core MT6582, MT6589(T), octacore MT6592 etc. What is worrying is that these are the processors found in many of the Chinese phones we report on on a daily basis!

Why do we need Mediatek Source Code?

One of the major reasons we need Source Code for Mediatek phones is to fix the problems which Mediatek have ignored. Security issues, problems with Bluetooth, and GPS (as we have all learned) could be easily fixed by a community of developers! Patches can then be created and distributed to everyone so we an all enjoy a secure, up to date Mediatek experience, not to mention possible 3rd party roms like OMNI ROM, Cyanogenmod and others could be ported.

Why don’t Mediatek release Source Code?

Mediatek will happily release the source code for their processors but at a price (which is against the GPL policies Android is distributed under). You can contact Mediatek now and pay for a license for your chip, but how many thousands of $ would they ask we don’t know, and the fact is it should be free.

Mediatek don’t want to offer source code as it forces customers to upgrade their phones to the next version with the latest processor. For example if we take a look at a quad-core MT6589T phone with 2GB RAM we can see it is most likely running Android 4.2, the phone is more than capable of 4.4 (and higher) but the chances of an official update are slim! Customers wanting the latest Android OS are forced to buy a newer phone!

So, you could have got an idea of what's wrong with mediatek phones.

The exact opposite is Qualcomm and so such phones have a a ton of custom ROM.


Sainath Reddy, Android Developer
Answered May 22, 2017 · Author has 262 answers and 655.8k answer views

Qualcomm releases the kernel sources and the drivers that are needed to run the device. These are really required for the developer community to build better ROMs and to tweak and extract the full potential of the chipset.

The Manufacturer using the Qualcomm chipset has to finally release these source files and the drivers, with all the modification they have done. Without these modifications known, the developer community might not be able to support the device properly.

This is the main reason for having better and more ROMS for Qualcomm chipsets compared to say MediaTek.


https://www.quora.com/Why-is-it-that-Qualcomm-powered-devices-have-much-better-Custom-ROM-support