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
Next was just a matter of reversing the hashes to get the flag. I Googled a few sites but fell victim to an odd inversely proportional relationship between the site's MD5 database and their SEO... No matter, I'll just brute force it! My friend, Max, wrote a hash brute forcing program in GO that was embarassingly faster than my python script. Fortunately he has a social life and a hot girlfriend so he had to leave before he could obtain the flag before me (:P).

The python script checked all combos of `A-Za-z0-9*_{}` and took about an hour (66^5 iterations run on my tiny Lenovo Yoga). However, about 10 minutes before it finished, a SIT member found a site with all the hashes (http://www.md5online.org/).
