# [RE4: UnVM Me](https://ctf.oddcoder.com/challenges#RE4: unVM me)
>If I tell you what version of python I used .. where is the fun in that?

We were given a .pyc file to reverse engineer. Obtaining the code was easy, but they threw in MD5 hashes to add an extra challenge.

## Creds
Max ([@mfresonke](https://github.com/mfresonke))

[Mysterie/uncompyle2](https://github.com/Mysterie/uncompyle2)

[md5online.org](http://www.md5online.org/)


## Uncompyle
This was a very wonderful python decompiler. The produced result was pristine python code!
```python
# 2017.02.04 23:50:09 EST
# Embedded file name: unvm_me.py
import md5
md5s = [174282896860968005525213562254350376167L,
 137092044126081477479435678296496849608L,
 126300127609096051658061491018211963916L,
 314989972419727999226545215739316729360L,
 256525866025901597224592941642385934114L,
 115141138810151571209618282728408211053L,
 8705973470942652577929336993839061582L,
 256697681645515528548061291580728800189L,
 39818552652170274340851144295913091599L,
 65313561977812018046200997898904313350L,
 230909080238053318105407334248228870753L,
 196125799557195268866757688147870815374L,
 74874145132345503095307276614727915885L]
print 'Can you turn me back to python ? ...'
flag = raw_input('well as you wish.. what is the flag: ')
if len(flag) > 69:
    print 'nice try'
    exit()
if len(flag) % 5 != 0:
    print 'nice try'
    exit()
for i in range(0, len(flag), 5):
    s = flag[i:i + 5]
    if int('0x' + md5.new(s).hexdigest(), 16) != md5s[i / 5]:
        print 'nice try'
        exit()

print 'Congratz now you have the flag'
# okay decompyling unvm_me.pyc 
# decompiled 1 files: 1 okay, 0 failed, 0 verify failed
# 2017.02.04 23:50:09 EST
```

The decompiled python code reveals the flag as 13 MD5 hashes of 5 characters.

## MD5 is Broken
Next was just a matter of reversing the hashes to get the flag. 
```
831daa3c843ba8b087c895f0ed305ce7
6722f7a07246c6af20662b855846c2c8
5f04850fec81a27ab5fc98befa4eb40c
ecf8dcac7503e63a6a3667c5fb94f610
c0fd15ae2c3931bc1e140523ae934722
569f606fd6da5d612f10cfb95c0bde6d
068cb5a1cf54c078bf0e7e89584c1a4e
c11e2cd82d1f9fbd7e4d6ee9581ff3bd
1df4c637d625313720f45706a48ff20f
3122ef3a001aaecdb8dd9d843c029e06
adb778a0f729293e7e0b19b96a4c5a61
938c747c6a051b3e163eb802a325148e
38543c5e820dd9403b57beff6020596d
```
I Googled a few sites but fell victim to an odd inversely proportional relationship between the site's MD5 database and their SEO... No matter, I'll just brute force it! My friend, Max, wrote a hash brute forcing program in GO that was embarassingly faster than my python script. Unfortunately he had to leave before he could obtain the flag.

The python script checked all combos of `A-Za-z0-9*_{}` and took about an hour (66^5 iterations run on my tiny Lenovo Yoga). However, about 10 minutes before it finished, a SIT member found a site with all the hashes (http://www.md5online.org/).

![md5online.org results](https://i.imgur.com/3PsXy7P.png)

`ALEXCTF{dv5d4s2vj8nk43s8d8l6m1n5l67ds9v41n52nv37j481h3d28n4b6v3k}`
