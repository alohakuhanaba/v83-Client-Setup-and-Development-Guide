# v83 Client Setup and Development Guide for modern HD monitors

<!-- markdown-toc start - Don't edit this section. Run M-x markdown-toc-refresh-toc -->
**Table of Contents**


- [Core setup](#Core-setup)
- [End user safety](#End-user-safety)
- [Server related things that might help you](#Server-related-things-that-might-help-you)
- [What you need to further develop the client patch](#What-you-need-to-further-develop-the-client-patch)
- [How to further develop the client patch](#How-to-further-develop-the-client-patch)
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

       
- 3. test the changes using the appropriate memory editting functions at the bottom of client.cpp; if doing code caves make sure to fill out the relevant information in the testing code cave in codecaves.h
- 4. compile the dll, replace old one, run the game, and test the changes; if the changes are correct, migrate the information from the line(s) used for testing into their own uniquely named lines of codes
- note: these instructions may not be easily understood at first, and that's normal. you need a degree of understanding of what is going on before you can do it. so here are some tutorials that helped me along the way:
## License

https://github.com/444Ro666/v83-Client-Setup-and-Development-Guide/blob/main/LICENSE
The don't sue me if anything in this guide gives you a virus or exposes additional vulnerabilities in your system license.
