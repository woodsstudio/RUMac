# R U a Mac?
### A project designed to make hackintoshing so much easier, instead of hours of Proper Tree configs, grabbing Kexts, and painfull work.

> [!WARNING]
> Woods Studio Labs hasn't completed this repo, so information may be missing or incorrect\
> Please do NOT follow this GitHub repo until we have made more effort on this\
> We thank you for your patience\
> Visit the [Woods Studio Labs Page](https://labs.woodsstudio.dev) or this repos website (https://projectmac.woodsstudio.dev) for more.
## Notices and Warnings:
This project is mainly designed for Microsoft Windows based devices. We are currently working on a Linux version. (We wont make a macOS build, cause we can't figure out why you'd want to mac'ify your mac. Use [Dortania's OpenCore Guide](https://dortania.github.io/OpenCore-Install-Guide/) if you want to update macOS => macOS.)\
**macOS 12 is very recommended to start, you can update later via [OpenCore Legacy Patcher](https://github.com/dortania/OpenCore-Legacy-Patcher/releases/download/2.5.1/OpenCore-Patcher.pkg)**

A USB 3.0 Drive (preferably) with a minimum of 16GB (Offline Installer)\
A SATA HDD / SSD, minimum 128GB
> [!CAUTION]
> You must backup any information of the HDD/SSD, as you will not be able to recover it, due to it will be formatted and turned from a NTFS system to APFS

A Machine with 4GB Minimum and an Intel Ivy Bridge (3rd Gen) CPU Set\
Windows 10 21H2 and above\
An Active Internet Connection (PCIe Card / Internal Chip!! USB Dongles don't function, like the Realtek 802.11ac NiC, even with Kexts)
>[!Note]
> Realtek USB Dongles will have added support soon, but MediaTek NiCs won't. macOS 13+ doesnt support USB Dongles without some serious hackery

**Hardware and Knowledge:**
You MUST know you hardware in order to properly grab required information once the tool makes your USB

=============
## What we did:
We used a Lenovo ThinkCenter M83 SFF [View the Spec Sheet](https://psref.lenovo.com/syspool/Sys/PDF/ThinkCentre/ThinkCentre_M83_Tower/ThinkCentre_M83_Tower_Spec.PDF)\
It has an Intel(R) Core(TM) i5-4430 CPU @ 3.00GHz, with 8GB DDR3 on a 250GB Samsung EVO SATA SSD.
>[!WARNING]
> We used macOS 12 because it was semi-easy to setup. We now recommend macOS 11 Big Sur for similar / slower hardware, although use older macOS / OSX versions for hardware differences. Use WS Simplify to get the best macOS build for your hardware.
  
## Getting Started
Make sure you have the following:

macOS 12 .raw file: [Download](https://download2389.mediafire.com/aue40muhj0ngDGAHfDb3RUTRoKna8-WV4FEM9iUZIxeS0Dsp8q-74iULZAU5x9ZGujguCu3idLjSoAfJ8KYPlcf0FUOqqj0Icg23m_EgZGvzL4TEKmv2VyM4nuMjIZJPkoWAGpyQMAfXt_R01ttLiNHirAoEIdYS7kF_oFkdLNe8Sw/w0luvledaw970vo/Olarila+Monterey+12.7.6.raw)\
Woods Studio Simplify (remake): [Download](https://archive.org/download/woodsstudiosearcher/com.woodsstudio.searcher.7z)\
OpenCore Release Packages: [Download](https://release-assets.githubusercontent.com/github-production-release-asset/179354282/7b316dce-9b6f-4eca-a4d7-d42d6af4ad99?sp=r&sv=2018-11-09&sr=b&spr=https&se=2026-10-08T02%3A44%3A49Z&rscd=attachment%3B+filename%3DOpenCore-1.0.8-RELEASE.zip&rsct=application%2Foctet-stream&skoid=96c2d410-5711-43a1-aedd-ab1947aa7ab0&sktid=398a6654-997b-47e9-b12b-9515b896b4de&skt=2026-10-08T01%3A44%3A29Z&ske=2026-10-08T02%3A44%3A49Z&sks=b&skv=2018-11-09&sig=7dX7vLMLmb9SZ7FqiLsaFOuUmKmxUk%2Fe59WWSYS%2FVgI%3D&jwt=eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmVsZWFzZS1hc3NldHMuZ2l0aHVidXNlcmNvbnRlbnQuY29tIiwia2V5Ijoia2V5MSIsImV4cCI6MTc5MTQyNzI3OCwibmJmIjoxNzkxNDI1NDc4LCJwYXRoIjoicmVsZWFzZWFzc2V0cHJvZHVjdGlvbi5ibG9iLmNvcmUud2luZG93cy5uZXQifQ.VgpklwPXiBf9nyVERsv4jQFAMStszcO2RMy7ofUP4Oc&response-content-disposition=attachment%3B%20filename%3DOpenCore-1.0.8-RELEASE.zip&response-content-type=application%2Foctet-stream)

>[!NOTE]
>Please visit the Labs Github page (www.projectmac.woodsstudio.dev) for updates until this repo is finished
