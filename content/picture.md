+++
title = 'Pixel Perfect Ploy'
date = 2024-02-05T12:23:59+05:30
draft = false
description="Masking Executables as Image Files"
image="/images/reality.png"
imageBig="/images/reality.png"
categories=["general","digtital-forensics","malware-evasion"]
authors=["Nirjhar Barma"]
avatar="/images/nir.png"
+++

# Hiding exe with Image extension
What we always see is not what it is- A file could look innocent but it could hide the most dangerous malware. Windows hides file extensions by default, therefore there is an infinite possible ways to send a malware unnoticed, two way of doing it is hiding/attaching it with an image-

_This blog is only for educational purpose and in no way intended for malicious purpose._

## Method 1
We can start by playing with victims mind by sending some inappropriate/attractive picture which will attract them and open the file. So the first step is to find a picture, I will use this

![](/images/exthide/exthide1.png)

Once we have done it, we will open a new .txt document-

![](/images/exthide/exthide2.png)

We will then rename it to **filename.ps1**(.ps1 is a powershell extension) -

![](/images/exthide/exthide3.png)

The next step is to edit the file, for me is the “_seeyou.ps1_”. Code given below:

![](/images/exthide/exthide4.png)

After saving the code, we will use a software to merge the executable and image but before that we have to get an [ico-file](https://www.freeconvert.com/ico-converter) of the image.

Finally it should look like this-
![](/images/exthide/exthide5.png)

Download the software from here- [github](https://github.com/MScholtes/Win-PS2EXE) and run it

![](/images/exthide/exthide6.png)

In the source file paste the .ps1 file path along with file name, in the icon file paste the .ico file path along with file name and tick/check the suppress output and suppress error output to hide any message. Once followed, press the compile button which will give another file.

![](/images/exthide/exthide7.png)

A file is created-

![](/images/exthide/exthide8.png)

Now we will rename the file to “seeyou.jpg.exe” since windows extensions are turned off by default it will be impossible to notice unless the user has enabled it, generally people won’t notice but if you hover above the picture you will find the file version(pic representation given above).

![](/images/exthide/exthide9.png)

Now there are some things to notice, firstly we need to obfuscate the powershell code so that it can bypass anti-virus, we can add some more advanced tech like heap spraying or shellcode injection or persistant backdoor, since this is just a walkthrough on how to craft such file we will not be exploring the advance parts.




## Method 2

We will kick things off with a malware/trojan/logger and picture, don’t have a malware? Get it from [vx-underground](https://vx-underground.org/)

Now we will bind the .exe and image file together into one using winRAR sfx archive option-.

Firstly we will select both the .exe and image file and select add to winRAR archive,

Then select _create sfx archive._

![](/images/exthide/exthide10.png)

 Then move to advanced tab and click on SFX options, then click on _setup_ tab and write the image file and .exe file name there

![](/images/exthide/exthide11.png)

Then go to Modes tab and select _unpack to temporary folder_ and _hide all_ option

![](/images/exthide/exthide12.png)

Then move to update tab, check the _extract and update files_ and _overwrite all files_-

![](/images/exthide/exthide13.png)


Finally go to _Text and icon_ tab, select the .ico file of the image and upload there. Once completed, you can click on ok and close the window.

![](/images/exthide/exthide14.png)

Aaand we have created a file but it has the file extension .exe, by default windows hides all file extensions but we don’t want to take any risks therefore we will use a very uncommon method known as **_Unitrix_** method. We will use the .scr(screen saver) extension to not make it suspicious, so it becomes _peppa.jpg.scr_ now we will use the unitrix exploit, what it will do is override the words from right to left, which will be very hard to notice.

So we will choose to rename and write peppagpj.scr and put the cursor between peppa|gpj.scr and right click which will open a dialog box and choose Insert Unicode control character and then choose RLO(Right to Left Override)

![](/images/exthide/exthide15.png)

The result will look like this when compared with the first output-

![](/images/exthide/exthide16.1.png)
![](/images/exthide/exthide16.2.png)

Yaay we have now successfully created an image embedded with a malware/trojan/spyware and disguised it.

## How to prevent it
Like that we have learned how to craft an image with an embedded exe. But how to keep ourselves safe from such attacks? Firstly we have to be very cautious of what we download and open, we should enable some folder features which could come handy-

![](/images/exthide/exthide17.png)

Or we can select the file and do _alt+enter_ key to view its properties and determine what it is. When unsure of its contents use a sandbox.

Author: Nirjhar


