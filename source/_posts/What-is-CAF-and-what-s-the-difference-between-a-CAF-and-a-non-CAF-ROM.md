---
title: 'What is CAF, and what''s the difference between a CAF and a non-CAF ROM?'
date: 2019-02-27 15:03:55
tags:
---

You might have come across ROMs that mention the tag [CAF] in their name, or description, or somewhere, while others have the LOS and AOSP tag. Take, for example, the very popular Sultan’s CAF ROMs.
The question that then arises is- What exactly is CAF? And what’s different in CAF ROMs, compared to non-CAF, normal ROMs?

This is somewhat of a mildly complex topic, and no-one in the community has written a detailed post on this either, so, let’s get this from the beginning, shall we?

CAF stands for the CodeAurora Forum. It is like the name says, an online forum (just like XDA, except, the forum is more for OS developers than flash maniacs) where Qualcomm releases source codes and patches for their CPUs. Code Aurora is a kind of an initiative in which brands can release the open-source codes for SoCs(CPUs) for future software development. Just keep this in your mind, for now, we’ll need this later.

Whenever Google releases a new Android version, they put their source codes on the AOSP remote (click to view), for the manufacturers and developers to customize, tweak, or modify things and release ROMs for phones. These codes have a clean and organized history for every new Android “tag”, a revision of Android. For example, Android-8.1.0_r14 means that it is the 14th revision of stable Android 8.1. These codes (released by Google) are limited and tested to work on a small number of devices (often referred to as AOSP supported devices). Any bugs found in these codes are handled and fixed directly by the AOSP team at Google. The custom ROMs built on top of these non-modified codes have AOSP as the base and are labeled with the AOSP tag.

LineageOS (abbreviated as LOS, formerly CyanogenMod) developers then take these codes and edit and modify these codes to make them work on many more devices. They then release these modified codes on their GitHub page (click to view). LOS is a community-driven project, and there is no monetary aid or pressure for high-quality and thorough testing. Still, bugs and errors are fixed quickly by the community. Now because the LOS source codes can run on more devices than the AOSP ones, we have much more ROMs built on top of LOS than AOSP, and, you guessed it, the ROMs built using LOS as the base have the LOS tag, naturally, so on and so forth.

All this aside, chipset makers like Qualcomm and MediaTek also want to implement their own hardware-specific features into Android so that the OS becomes more optimized for their respective CPUs. So, they take the AOSP source codes from Google, alter it according to their CPU, add or remove features, and then pass the modified codes to the OEMs (phone manufacturers) for the OS development. AOSP is licensed in such a way, that chipset manufacturers can modify and pass the codes to the OEMs without releasing it to the public. As a result of this, the SoC source/modified codes stay only with the SoC manufacturers (like Qualcomm) & the OEMs (like Xiaomi), and it depends on their will on whether or not to release it to the public.
Unlike others (Mediatek, Kirin, etc.,) though, Qualcomm adds their modifications, does the altering as per the SoC, and then releases it to the public (read: developers) on CAF (if you have a memory that doesn’t suck, you remember what CAF is). The ROMs made on top of Qualcomm’s modified source codes are labeled with the CAF tag.

Now let’s talk about what CAF brings to the table.
Qualcomm makes their CPUs (duh!), so it’s pretty safe to say that nobody knows Qualcomm CPUs better than Qualcomm. In the CAF, they add a lot of stuff that can take advantage of their CPU prowess, like better audio processing, various CPU clock tweaks, camera patches, and libs, and much more. Developers know how big of a role CAF plays in development. It is a major reason why Snapdragon devices get much more ROMs than, say, MediaTek ones. If you feel your phone’s software is much more optimized and fluid in CAF ROMs, it isn’t a coincidence. CAF ROMs, if available, are therefore generally considered to be more optimized for Qualcomm’s Snapdragon SoCs than LOS and AOSP ROMs.

Now that you know all this, you’re welcome.

https://customroms.net/faq/caf-vs-aosp-los-roms/