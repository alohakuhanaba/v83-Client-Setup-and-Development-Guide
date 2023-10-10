# v83 Client Setup and Development Guide for HD monitors

<!-- markdown-toc start - Don't edit this section. Run M-x markdown-toc-refresh-toc -->
**Table of Contents**

- [Core setup](#Core-setup)

- [License](#license)

<!-- markdown-toc end -->

### Core setup
This setup guide is for an open source v83 client patch designed to work with the most developed open source v83 server (currently cosmic)  

This guide assumes you followed the server setup guide for your respective v83 server source up until the point you need the localhost/client. Instead of using the provided localhost, download the following:  

- compiled ezorsia v2 dll from https://github.com/444Ro666/v83-Client-Setup-and-Development-Guide/releases  follow the instructions in the readme to install it (source is also released for transparency and development)
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




## License

https://github.com/444Ro666/v83-Client-Setup-and-Development-Guide/blob/main/LICENSE
The don't sue me if anything in this guide gives you a virus or exposes additional vulnerabilities in your system license.
