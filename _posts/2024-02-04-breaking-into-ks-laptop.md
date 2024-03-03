---
layout: post
title: Breaking into K's Laptop
subtitle: Inspired from cold boot attack
# cover-img: /assets/img/path.jpg
# thumbnail-img: /more/ctf/breaking-into-ks-laptop/thumbnail.jpg
share-img: /assets/img/path.jpg
tags: [system security, cold boot]
author: Hrishikesh Jedhe Deshmukh
---

## Part 1
**My friend 'K' being the pedantic person he is, has encrypted all the partitions of his disk. But he keeps his laptop always ON (last time I checked uptime on his laptop was 16d 2hr 9m 50s). But me being me, somehow managed to procure liquid nitrogen and get the ramdump of his laptop. After this I also took his disk and copied the bits and pieces of his most important encrypted parition. I am sharing both the the images with you. Now it's your responsibility to get me the sekret data that 'K' has in his disk!**

Link to the required files is [here](/more/ctf/breaking-into-ks-laptop/level1-files.tgz)

This part was quite simple, you just needed to use `strings` command on the given ramdump to get the flag. Reason for flag being in ram is that the recent commands that were used are cached and thus stay in memory.

## Part 2
**After K came to know about my exploits he became more pedantic about his security measures. Now he turns off his laptop frequently and clears his filesystem cache as well (sad RAM noises). But still somehow I managed to get data from his RAM along with his disk data. Now task lies on you to find what resides in K's disk and get flag along the way!**

Link to the required files is [here](/more/ctf/breaking-into-ks-laptop/level2-files.tgz)

When a disk is encrypted, AES keys required for decryption are in memory. So you had to retrieve the keys from the ramdump and use them to decrypt the given disk image.

To find the AES keys in the ramdump, you can use a standard tool like [this](https://sourceforge.net/projects/findaes/). Following is the output of the tool:
```
Found AES-256 key schedule at offset 0xed449d0: 
41 ef 36 01 15 f6 8e ba a3 eb 33 d2 cf 04 3c 96 1c 1c 45 bb d4 bc d1 42 8e 8b 64 bb 61 de 1f 66 
Found AES-256 key schedule at offset 0xed44ba0: 
41 ef 36 01 15 f6 8e ba a3 eb 33 d2 cf 04 3c 96 1c 1c 45 bb d4 bc d1 42 8e 8b 64 bb 61 de 1f 66 
Found AES-256 key schedule at offset 0xed45820: 
6c c1 cf 39 aa ef 75 79 7f b7 e8 96 ad 96 8f d4 36 89 d1 4f ea bc 0f e9 e9 0c 63 cd 66 78 8f b0 
Found AES-256 key schedule at offset 0xed459f0: 
6c c1 cf 39 aa ef 75 79 7f b7 e8 96 ad 96 8f d4 36 89 d1 4f ea bc 0f e9 e9 0c 63 cd 66 78 8f b0 
Found AES-256 key schedule at offset 0xed45c80: 
00 01 02 03 04 05 06 07 08 09 0a 0b 0c 0d 0e 0f 10 11 12 13 14 15 16 17 18 19 1a 1b 1c 1d 1e 1f 
Found AES-256 key schedule at offset 0xed46250: 
38 ff 59 99 58 15 49 e1 2a 7d 8e ff f0 86 b8 fb 98 48 fe 84 bf 19 d0 b5 c0 cb c6 e3 e2 aa 07 74 
Found AES-256 key schedule at offset 0xed46420: 
38 ff 59 99 58 15 49 e1 2a 7d 8e ff f0 86 b8 fb 98 48 fe 84 bf 19 d0 b5 c0 cb c6 e3 e2 aa 07 74 
Found AES-256 key schedule at offset 0x189fd410: 
14 da f3 77 0a e4 18 31 e4 83 61 0e a2 ce 7e ec 90 5d 58 95 8f 7d 9b a7 0d 27 80 f8 98 6b 24 99 
Found AES-256 key schedule at offset 0x189ff810: 
9f 20 44 16 85 97 a8 7e 05 8c 94 de 28 c0 8c a8 5b ad 9f b9 df 26 7f d0 02 a9 8f 9f 13 aa 2a 08 
Found AES-256 key schedule at offset 0x1a783060: 
77 84 a2 1b d2 27 08 a5 79 e3 62 b0 c1 a5 c2 2b 79 c1 9e 24 65 bc ac 8c a2 aa 0f b3 0e 57 6c d9 
Found AES-256 key schedule at offset 0x1a783230: 
77 84 a2 1b d2 27 08 a5 79 e3 62 b0 c1 a5 c2 2b 79 c1 9e 24 65 bc ac 8c a2 aa 0f b3 0e 57 6c d9 
Found AES-256 key schedule at offset 0x1a7834c0: 
00 01 02 03 04 05 06 07 08 09 0a 0b 0c 0d 0e 0f 10 11 12 13 14 15 16 17 18 19 1a 1b 1c 1d 1e 1f 
Found AES-256 key schedule at offset 0x1ac14a60: 
2d 55 1a e0 fe 0b b7 03 42 20 1e 8b 2e 9f ef 62 26 4c bb 50 29 c4 db 5b c6 cf e2 4f 21 b6 30 20 
Found AES-256 key schedule at offset 0x1ac14c30: 
2d 55 1a e0 fe 0b b7 03 42 20 1e 8b 2e 9f ef 62 26 4c bb 50 29 c4 db 5b c6 cf e2 4f 21 b6 30 20 
```
But the AES key used for encrypting is 512 byte long. So you need to concatenate two of these 256 byte long keys. Also the two keys that need to be concatenated will be in vicinity of each other, so you do not need to try out all the combinations. The key that will decrypt the disk is obtained by concatening keys at offset `0x189ff410` and `0x189fd810`. Also they need to be concatenated in reverse manner due to addressing being little endian. So the key that we want is `0x9f2044168597a87e058c94de28c08ca85bad9fb9df267fd002a98f9f13aa2a0814daf3770ae41831e483610ea2ce7eec905d58958f7d9ba70d2780f8986b2499`.

Now boot up a virtual machine with provided diskimage as a device. Save the above AES key in `key.txt`. Then run `xxd -r -p key.txt > key.bin`. And then decrypt the disk image using `cryptsetup luksOpen --master-key-file key.bin /dev/sda mydisk`. You can view the files inside the disk image now by mounting it using `mount /mnt /dev/mapper/mydisk`.