# v83 Client Setup and Patch Development Guide for modern HD monitors

<!-- markdown-toc start - Don't edit this section. Run M-x markdown-toc-refresh-toc -->
**Table of Contents**


- [Core setup](#Core-setup)
- [End user safety](#End-user-safety)
- [Server related things that might help you](#Server-related-things-that-might-help-you)
- [Disclosure before reading about the development process](#Disclosure-before-reading-about-the-development-process)
- [What you need to further develop the client patch](#What-you-need-to-further-develop-the-client-patch)
- [How to further develop the client patch](#How-to-further-develop-the-client-patch)
- [Tutorials for developing the client patch](#Tutorials-for-developing-the-client-patch)
- [License](#license)

<!-- markdown-toc end -->

### Core Setup
This setup guide is for an open source v83 client patch designed to work with the most developed open source v83 server (currently cosmic)  

This guide assumes you followed the server setup guide for your respective v83 server source up until the point you need the localhost/client. Instead of using the provided localhost, download the following:  

- compiled ezorsia v2 dll from https://github.com/444Ro666/MapleEzorsia-v2/releases follow the instructions in the readme to install it (source is also released for transparency and development)
- hendi's v83 4g client with admin elevation request removed from https://www.mediafire.com/file/by9x35uwd5pv0ol/GMSv83_4GB%2528no_admin_req%2529.rar/file this is the only edit that was made to the binary directly because i could not get it to work from the dll
    - if you would rather do the edit yourself for verification purposes you can get the original release version from https://forum.ragezone.com/threads/localhost-workshop.1202021/ you will also need the to use the hex editor from cosmic files https://drive.google.com/drive/folders/1hgnb92MGL6xqEp9szEMBh0K9pSJcJ6IT and make the following edits https://www.mediafire.com/view/2jdmfszadkzpn49/2023-09-12-100911_1295x227_scrot.png/file#
    - mirror for clean, uneditted 4g client in case it ever becomes unavailable: https://www.mediafire.com/file/5v235m2dfwnqso2/GMSv83_4GB.rar/file
- a modified UI.wz file for the resolution you plan to play the game on and replace the existing UI.wz(remember to rename it as ui.wz exactly afterwards):
  - 1280x720:https://www.mediafire.com/file/ut4pdn2czesxuca/1280UI.wz/file
  - 1366x768:https://www.mediafire.com/file/hnhzc4mopnhqh0m/1366UI.wz/file
  - 1600x900:https://www.mediafire.com/file/di4vdatv3jr7d3h/1600UI.wz/file
  - 1920x1080:https://www.mediafire.com/file/noxd1aekj1eolg0/1920UI.wz/file
  - these UI.wz are modified UI.wz from the cosmic release, and are up to date as of 2023/10/10. in the event that cosmic updates their UI.wz in release, you will have to download these files https://www.mediafire.com/file/na1n5pmwgq3re6u/UIwzedits.rar/file and use a wz editor to update the UI.wz following these instructions:
    - login.img->common->frame->replace with larger resolution frame of appropriate resolution
    - CashShop.img->base->backgrnd/backgrnd1/backgrnd2->replace with updated backgrnd/for explorers, cygnus, and aran
  - you also have to do this if using a different source, custom UI.wz, or if you want to play on a different resolution than one of the 4 listed (i added the black space into the frame using paint 3D)
  - Note: the game is playable using just the patch, but you would have to toggle the setting to CustomLoginFrame=false and it will look slightly uglier on the login screen (and medal will clip through the cash shop preview window)
- download magpie from https://github.com/Blinue/Magpie    open magpie while game is running, click new profile and select your client window, then go to the created profile and toggle on auto scale when in foreground. make sure that window mode is on in the ezorsia config when doing this.
  - magpie is a borderless fullscreener that interacts with directx to re-draw pixels into hi-resolution to match your display resolution. this way you can match the aspect ratio of your screen without the normal fullscreen stretching, keep the classic maple look playing in 1280x720, and avoid the scaled down UI elements that occur when a higher resolution is selected in ezorsia config

### End User Safety
This guide also aims to provide the safest possible setup for the end user so i think we should talk about this now. 

virustotal analysis for hendi's 4g v83 client: https://www.virustotal.com/gui/file/1198fa57ca5a7c489bae43ec13c69681d9cabe0f96762f3dc0357facf2e7d4df
- as you can see 25/71 (more than a third) of AVs detect it, but they are mostly generic/trojan detections which can trigger from modification of binaries as well as lack of official signing (this is still less than the detection ratio of commonly distributed v83 localhosts which range around 40-50/71 detection ratio)
- hendi has also been very transparent in their release, including releasing the source of the tool used to remove old school themida as well as instructions and methods on how to do so as seen from the initial ragezone release
- Therefore, i would say it is most likely safe however, i cannot guarantee the safety of cracked software, so it may be smart to consider virtualizating it
- There are various OS-level virtualization solutions (or even system level virtualization solutions) you can employ. one such solution is sandboxie https://sandboxie-plus.com/ (i won't name which one i use for personal security reasons but the client setup works like a charm within virtualization)

### Server related things that might help you
- when trying to set up MySQL Workbench to attach to the database server make sure to enter the parameters like this (assuming you are using cosmic) https://www.mediafire.com/view/iagchkgjqqvnaoe/connectguideUntitled.png/file (i got stuck on this part, so i recorded it and it might help others)
- if you are using docker like i am, this guide might also help you as it helped me (contains commands you need): https://dingyunxing.github.io/tutorial/2021/10/07/mysql-docker/

## Disclosure before reading about the development process
The following advice about how to use the tools and edit the client is just from what i've learned over the one month that i have joined the scene and have started working with the client. I have a bit of programming experience but had no experience with C++ prior, nor had any experience with asm or reverse engineering. There may very well be better ways to do things and even my own process is in a constant state of change. This is so far, just what i've managed to scrape together, been taught here and there when folks were feeling generous, and what i've found at the time of writing to have successfully worked. I hope it helps, but always be ready to learn more!

### What you need to further develop the client patch
- To compile the dll yourself, you need visual studio 2019 https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=Community&channel=Release&version=VS2022&source=VSLandingPage&cid=3333&passive=false
- you need the source code of the ezorsia v2 dll found at: https://github.com/444Ro666/MapleEzorsia-v2
- you need memory dumps of the client to find addresses in and to inspect the client's mechanisms of operation:
    - angel's v83 IDB release: https://forum.ragezone.com/threads/v83-idb-client-edit-dump.1193418/
    - angel's v95 IDB release: https://discord.com/channels/350831332609359875/1051899319491506298/1125628734347690065
      - if it becomes unavailable you'll have to do your v95 IDB own using original v95 leak from: https://forum.ragezone.com/threads/getting-packet-structures-and-opcodes-with-ida-after-gms-new-update.872876/ or    https://discord.com/channels/350831332609359875/939516633096015932/1147747196951859261
- IDA 7.0 to view the dumped memory: https://rutracker.org/forum/viewtopic.php?t=5459068 (you dont need whole thing, just the version specific to your os. can also get 7.7 in useful links in maple development discord but newer versions of IDA may not be able to read older IDB files)
    - note: i cannot guarantee the safety of cracked software, so it may be smart to consider virtualizating it
        - There are various OS-level virtualization solutions (or even system level virtualization solutions) you can employ. one such solution is sandboxie https://sandboxie-plus.com/ (i won't name which one i use for personal security reasons but the IDA works like a charm within virtualization)
- A debugger to view the memory of an actively running client during testing, to see for example, if your codecaves are going to the places they should be going: https://github.com/x64dbg/x64dbg/releases (use x32dbg version)
- data conversion tools online you should bookmark:
	- dec/hex converter(i use this to verify hex values): https://www.mathsisfun.com/binary-decimal-hexadecimal-converter.html
   	- x86 assembler/disassembler(i use this to convert hex instructions in memory dump to accurate asm commands; commented asm from the dump can't always be used right away in C++ inline):
https://defuse.ca/online-x86-assembler.htm#disassembly
	- IEEE 754 floating point converter (i use this to convert 64 bit values for things like damage cap): https://baseconvert.com/ieee-754-floating-point
- you may need other tools along the way but this is the basics of what you will need

### How to further develop the client patch (the process)
- 1. find a lead to the area of the binary's memory you need to work with to affect the change. To do this you can:
	- find relevant functions using functions area in the fully named v95 IDB
         - you'll need to look for a area with similar functionality in v83 dump later if taking this approach but it can help verify a lot of things and the pseudocode generated in the leaked IDB is often easier to read
	- or use stringpool(enums) to find a related area in memory dump (to find the related string you need to look through wz files)
	- or use an existing address that affects what you're trying to change and looking in the same area

   
- 2. look through the asm in the area you identified in step 1 and use F5 to turn it into pseudocode, can right click in IDA view or pseudocode to synchronize and go to the exact spot in the other one, or look at the bottom left bar that shows which address the cursor in pseudocode is at
	- try to figure out how it works and what you may need to change
	  - knowledge of procedural programming is very helpful here, as the pseudocode goes from top to bottom with v# values often being called and defined out of thin air
	    - can also right click the function name of subroutines and click list cross-references to and from to see what other subroutines work with it; this sometimes works to find related areas but not all the time
	    - can also find more information with xrefs but i don't have much experience doing that
	    - try to find the constructor of the function (for example CUIScreenMsg::CUIScreenMsg(CUIScreenMsg *this), constructors are the one that call itself), a lot of stuff is initialized there
     - you should conclude this part with the address of the start of the asm instructions(s) you want to change, and what you want to change it too. If you're changing a whole section of memory using code caving you also need the return address and the amount of bytes in between the initial address and return address for the NOP count (minimum 5)

       
- 3. test the changes using the appropriate "testing" memory editing functions at the bottom of client.cpp; if doing code caves make sure to fill out the relevant information in the testing code cave in codecaves.h
	- when directly editing memory at an address, you need to make sure the value you enter is appropriate for that part of memory or the game will crash on executing it, pay attention to how many bytes of space you have to work with after the initial offset from the beginning of an asm instruction. a single byte can only be 255 max
	- when code caving you need try to add or modify instructions without affecting the existing instructions taken from the original location of the code cave, nor interferring with variables on the stack, in such a way that the application deems unacceptable.
 		- if it is crashing, try to do it gradually, for example, by copying the original instructions in the code cave completely, without any edits, and getting that to work. then making modifications and trying to get those to work
   - you could also use function replacement to use the client's own functions in your own way, or to modify how the original function works if you don't need to make edits in the middle of a function; but i don't have much experience making this working in practice (see notes in source)
- 4. compile the dll, replace old one, run the game, and test the changes; if the changes are correct, migrate the information from the line(s) used for testing into their own uniquely named lines of codes
- note: these instructions may not be easily understood at first, and that's normal. you need a degree of understanding of what is going on before you can do it. so here are some tutorials that helped me along the way:

### Tutorials for developing the client patch
These are not in any particular order, though i've tried to put the more advanced ones on the bottom. You will likely have to jump back and forth between tutorials and occasionally restudy them to get a handle of what is happening.

- IDA/IDB work and general guides:
	- tutorial on how to use the v95 IDB with the v83 IDB to make edits (you can skip the strings part if using IDBs with existing enums ID'd): https://forum.ragezone.com/threads/getting-packet-structures-and-opcodes-with-ida-after-gms-new-update.872876/
	- another one about IDA and the IDB: https://forum.ragezone.com/threads/how-to-use-ida-in-v83.1107216/
	- another one (with example): https://forum.ragezone.com/threads/removing-a-check-in-the-maplestory-client-v83.1147791/
- C++ tutorial for updating the dll code: https://www.kindsonthegenius.com/cplusplus/c-conditional-statements/
- tutorial for what code caving is: https://www.codeproject.com/Articles/20240/The-Beginners-Guide-to-Codecaves
- asm tutorials:
	- teaches basics of how the stack works, the various registers, as well as instructions: https://www.cs.virginia.edu/~evans/cs216/guides/x86.html
	- teaches instructions with examples: https://www.tutorialspoint.com/assembly_programming/assembly_logical_instructions.htm
	- about movsd instruction: https://www.scs.stanford.edu/05au-cs240c/lab/i386/MOVS.htm
	- another asm tutorial (havent enrolled in this one but it's been recommended before): https://p.ost2.fyi/courses/course-v1:OpenSecurityTraining2+Arch1001_x86-64_Asm+2021_v1/course/
- eric's old tutorial videos that i haven't watched (but they are recommended): https://discord.com/channels/350831332609359875/939516633096015932/939517377136185374 and https://discord.com/channels/350831332609359875/939516633096015932/939525229359816786
- calling conventions for function replacement and asm: https://en.m.wikibooks.org/wiki/X86_Disassembly/Calling_Conventions
- example code for function replacement: https://forum.ragezone.com/threads/custom-string-pool.1148502/ and https://github.com/pokiuwu/AuthHook-v176-StringPool/blob/master/Client176/MapleHook.cpp

## License

https://github.com/444Ro666/v83-Client-Setup-and-Development-Guide/blob/main/LICENSE
The don't sue me if anything in this guide gives you a virus or exposes additional vulnerabilities in your system license.
