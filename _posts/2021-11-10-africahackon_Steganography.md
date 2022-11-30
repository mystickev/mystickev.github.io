---
layout: default
tags:
- Africahackon
- steganography
- writeup
---

[back /](/)

# CHALLENGE: PIXEL VALUE DIFFERENCING

![date](https://img.shields.io/badge/date-10.11.2021-brightgreen.svg)  
![solved in time of CTF](https://img.shields.io/badge/solved-in%20time%20of%20CTF-brightgreen.svg)  
![warmup category](https://img.shields.io/badge/category-Steg-lightgrey.svg)
![score](https://img.shields.io/badge/score-75-blue.svg)

## Description

How often do you read steganography research papers?, the spectogram looks nice.what does it hide (75pts)

## Attached files

- [spectro.zip](/assets/images\/favicons/spectro.zip)

## Summary

This challenge was about Pixel value differencing where LSB substitution and PVD are both applied. In PVD process, non-overlapping 3x3 pixel blocks or a combination of 3x3 and 2x2 blocks are used in a raster pattern(side to side) from top to bottom
Extraction is done using a generated log file during embedding, that contains data locations in the steg Image

```
ah{redacted}
```

## Detailed solution

Doing a bit of research on PVD Steg, considering the files provided in the challenge(embed.log), i found one tool that reveals hidden message using a given log file.The [repo](https://github.com/TonyJosi97/pvd_steganography) shows a python implementation of pixel-value-differencing. We can tell that we are in the right place by reading the instructions which shows that the tool uses embed.log to map data points of the concealed message
so:
git clone <repo>
cd <repo>
open the pvdextract.py in a code editor and replace the logfile name with ours(embedlog.log). Run the script with the command

  Python3 PVDExtract.py protest.png cipher

- protest.png - image with concealed message
- cipher = output_file
This will spit the flag
  
![image info](/assets/images/spec.png)

## Takeaways
  
From the paper **The traditional steganography methods with Pixel Value Differencing (PVD) usually only deal with the PVD in the horizontal direction but ignore the vertical one. In this paper, the cover image is divided into 2×2 nonoverlapping blocks, then the modulus function method is used in horizontal direction and the simple PVD method is used in vertical direction for steganography.**

# CHALLENGE: DIGITAL INK STEGANOGRAPHY(DIIT)

![date](https://img.shields.io/badge/date-10.11.2021-brightgreen.svg)  
![solved in time of CTF](https://img.shields.io/badge/solved-in%20time%20of%20CTF-brightgreen.svg)  
![warmup category](https://img.shields.io/badge/category-Steg-lightgrey.svg)
![score](https://img.shields.io/badge/score-75-blue.svg)

## Description

i heard you likes memes XD » hint=John Cena :)

## Attached files

- ![chall1.png](/assets/images/chall1.png)

## Summary

This challenge was about digital Invisible ink..which is a steg method used to conceal a message. The idea behind it is replacing some information with a concealed message through a cover object. This makes any digital stream a viable carrier.

## Flag

```
ah{redacted}
```

## Detailed solution

Doing a bit of research on digital ink steg, led me to one java tool. The DIIT project can be found at <http://diit.sourceforge.net/> Download the tool and load the image.I used the battle steg algorithm which successfully decrypted the concealed image to a text file

![image info](/assets/images/diit.png)

## Takeaways

With the rich playground of digital streams and formats, more uncommon steg methods are always being invented. Its important to keep track by searching for new tricks

[back /](/)
