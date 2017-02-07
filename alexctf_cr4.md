# [CR2: Poor RSA](https://ctf.oddcoder.com/challenges#CR4: Poor RSA)
> This time Fady decided to go for modern cryptography implementations, He is fascinated with choosing his own prime numbers, so he picked up RSA once more. Yet he was unlucky again!

We were given an RSA public key:
```
-----BEGIN PUBLIC KEY-----
ME0wDQYJKoZIhvcNAQEBBQADPAAwOQIyUqmeJJ7nzzwMv5Y6AJZhdyvJzfbh4/v8bkSgel4PiURXqfgcOuEyrFaD01soulwyQkMCAwEAAQ==
-----END PUBLIC KEY-----
```
And a base 64 encoded and RSA encrypted flag:

`Ni45iH4UnXSttNuf0Oy80+G5J7tm8sBJuDNN7qfTIdEKJow4siF2cpSbP/qIWDjSi+w=`


## Creds
Chris

[balbla](http://pastebin.com/NmX7d271)

## Approach 1: Brute Forcing
Wouldn't hurt to try right while we figure out other solutions. Unfortunately the modulus was enormous.
```
Public-Key: (399 bit)
Modulus:
    52:a9:9e:24:9e:e7:cf:3c:0c:bf:96:3a:00:96:61:
    77:2b:c9:cd:f6:e1:e3:fb:fc:6e:44:a0:7a:5e:0f:
    89:44:57:a9:f8:1c:3a:e1:32:ac:56:83:d3:5b:28:
    ba:5c:32:42:43
Exponent: 65537 (0x10001)
```
That's a modulus of

`52a99e249ee7cf3c0cbf963a009661772bc9cdf6e1e3fbfc6e44a07a5e0f894457a9f81c3ae132ac5683d35b28ba5c324243`

or even better, in base 10:

`833810193564967701912362955539789451139872863794534923259743419423089229206473091408403560311191545764221310666338878019`

**We ain't brute forcing that lol**

### Bottom up
Maybe since Fady chose his own prime numbers, he didn't actually choose a prime number? Perhaps if we try to factor the modulus we will find a smaller factor! Nope.

### Sqrt down
