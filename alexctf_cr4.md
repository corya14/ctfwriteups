# [CR4: Poor RSA](https://ctf.oddcoder.com/challenges#CR4: Poor RSA)
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

[Stack Overflow](https://stackoverflow.com/questions/20464561/fermat-factorisation-with-python)

[rsatool.py](https://github.com/ius/rsatool)

## Approach 1: Brute Forcing
Wouldn't hurt to try while we figure out other solutions. Unfortunately the modulus was enormous.
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
Maybe since Fady chose his own prime numbers, he made a mistake and found a non prime? Perhaps if we try to factor the modulus we will find a smaller factor! **Nope.**

### Sqrt down
Maybe Fady chose two prime numbers really close to the square root of the modulus! If we do trial division from the square root down perhaps we will find _p_ and _q_. **Nope.**

## Approach 2: Fermat Factorization
Perhaps the prime numbers chosen are vulnerable to Fermat factorization. I found a [post](https://stackoverflow.com/questions/20464561/fermat-factorisation-with-python) on Stack Overflow with a python Fermat factorization function:
```python
def isqrt(n):
  x = n
  y = (x + n // x) // 2
  while y < x:
    x = y
    y = (x + n // x) // 2
  return x

def fermat(n, verbose=True):
    a = isqrt(n) # int(ceil(n**0.5))
    b2 = a*a - n
    b = isqrt(n) # int(b2**0.5)
    count = 0
    while b*b != b2:
        if verbose:
            print('Trying: a=%s b2=%s b=%s' % (a, b2, b))
        a = a + 1
        b2 = a*a - n
        b = isqrt(b2) # int(b2**0.5)
        count += 1
    p=a+b
    q=a-b
    assert n == p * q
    print('a=',a)
    print('b=',b)
    print('p=',p)
    print('q=',q)
    print('pq=',p*q)
    return p, q

if __name__ == '__main__':
	mod = '52a99e249ee7cf3c0cbf963a009661772bc9cdf6e1e3fbfc6e44a07a5e0f894457a9f81c3ae132ac5683d35b28ba5c324243'
	intmod = int(mod,16)
	fermat(intmod,False)
```

Perhaps if we leave that running for a little bit, it will find the factors! **Nope.**

## Approach 3: Smarter Brute Forcing
I tried finding lists of prime numbers and performing trial division of the modulus. After several gigabytes of text files... **Nope.**
```python
#crypto4_smart.py
import os

if __name__ == '__main__':
	pub_key = 'ME0wDQYJKoZIhvcNAQEBBQADPAAwOQIyUqmeJJ7nzzwMv5Y6AJZhdyvJzfbh4/v8bkSgel4PiURXqfgcOuEyrFaD01soulwyQkMCAwEAAQ=='
	modulus = '52a99e249ee7cf3c0cbf963a009661772bc9cdf6e1e3fbfc6e44a07a5e0f894457a9f81c3ae132ac5683d35b28ba5c324243'
	int_modulus = int(modulus,16)

	found = False
	factor = -1

	print(int_modulus)
	directory = 'C:/Users/corya_000/Downloads/primes/txt/'
	for filename in os.listdir(directory):
		print('Checking primes file ' + filename + ' for factors')
		f = open(directory + filename,'r')
		for line in f:
			splitline = line.split(' ')
			for item in splitline:
				if not item == '':
					try:
						prime = int(item)
						if (int_modulus % prime == 0):
							print('FOUND ' + str(item))
							found = true
							factor = prime
							break
					except(ValueError):
						pass
		print('NOPE')
	print('found?:' + str(found))
	print(factor)
```

## Approach 4: Desperation
I can't believe I didn't try this first, but I google'd parts of the problem. Maybe Fady chose a popular prime number? Who knows! Well anyways after a search of the hex modulus I found a [pastebin](http://pastebin.com/NmX7d271) posted by a mysterious _balbla_ with p and q:
```
#!/usr/bin/python2
import gmpy2

p =  863653476616376575308866344984576466644942572246900013156919

q =  965445304326998194798282228842484732438457170595999523426901
e =  65537
c =  
t = (p-1)*(q-1)

n = 52a99e249ee7cf3c0cbf963a009661772bc9cdf6e1e3fbfc6e44a07a5e0f894457a9f81c3ae132ac5683d35b28ba5c324243
n = p*q

# returns d such that e * d == 1 modulo t, or 0 if no such y exists.
d = gmpy2.invert(e,t)

# Decryption
m = pow(c,d,n)
print "Solved ! m = %d" % m
```

I gave p and q to the SIT team slack and Chris ran [rsatool.py](https://github.com/ius/rsatool) and used openssl rsautl to obtain the flag:
```
./rsatool.py -f PEM -o key.pem -p863653476616376575308866344984576466644942572246900013156919 -q965445304326998194798282228842484732438457170595999523426901
cat flag.b64 | base64 -d > flag.bytes
openssl rsautl -in flag.bytes -out /dev/tty -inkey key.pem -decrypt
```
