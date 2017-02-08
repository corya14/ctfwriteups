# [CR2: Many Time Secrets](https://ctf.oddcoder.com/challenges#CR2: Many time secrets)
>This time Fady learned from his old mistake and decided to use onetime pad as his encryption technique, but he never knew why people call it one time pad!

I learned about one-time pads (OTP) in my intro to cyber security class. However, the hint provided made it clear that the cipher was repeating the OTP.

We were given a secret message to decode:
```
0529242a631234122d2b36697f13272c207f2021283a6b0c7908
2f28202a302029142c653f3c7f2a2636273e3f2d653e25217908
322921780c3a235b3c2c3f207f372e21733a3a2b37263b313012
2f6c363b2b312b1e64651b6537222e37377f2020242b6b2c2d5d
283f652c2b31661426292b653a292c372a2f20212a316b283c09
29232178373c270f682c216532263b2d3632353c2c3c2a293504
613c37373531285b3c2a72273a67212a277f373a243c20203d5d
243a202a633d205b3c2d3765342236653a2c7423202f3f652a18
2239373d6f740a1e3c651f207f2c212a247f3d2e65262430791c
263e203d63232f0f20653f207f332065262c3168313722367918
2f2f372133202f142665212637222220733e383f2426386b
```

## Creds
Spencer (What's yo github bruhh?)

## Initial Approach
My initial approach was to xor the cipher with itself at an offset. At the right offset, this would cancel out the key and provide two different plaintexts of the message XOR'd with eachother. With this result, XORing common words may end up cancelling out one of the parts of the message and leave us with plaintext. I began creating some python functions to aid with this strategy.
```python
def hex2bin(txt):
	hextxt = ""
	txt = txt.replace('0x','')
	for i in range(0,len(txt)):
		hextxt = hextxt + hexc2bin(txt[i])
	return hextxt
def hexc2bin(c):
	c = c.replace('0x','')
	if c == '0':
		return '0000'
	elif c =='1':
		return '0001'
	elif c =='2':
		return '0010'
	elif c == '3':
		return '0011'
	elif c == '4':
		return '0100'
	elif c == '5':
		return '0101'
	elif c == '6':
		return '0110'
	elif c == '7':
		return '0111'
	elif c == '8':
		return '1000'
	elif c == '9':
		return '1001'
	elif c == 'a':
		return '1010'
	elif c == 'b':
		return '1011'
	elif c == 'c':
		return '1100'
	elif c == 'd':
		return '1101'
	elif c == 'e':
		return '1110'
	elif c == 'f':
		return '1111'
	else:
		print "BADCHAR"
		print c

def ascii2hex(c):
	return hex(int(ord(c))).replace('0x','')

def hex2ascii(c):
	return str(bytearray.fromhex(c.replace('0x','')).decode())

def hexor(a,b):
	ans = ''
	a_bin = hexc2bin(a.replace('0x',''))
	b_bin = hexc2bin(b.replace('0x',''))
	for i in range(0,4):
		ans = ans + str(int(a_bin[i]) ^ int(b_bin[i]))
	return hex(int(ans,2)).replace('0x','')

def asciixor(a,b):
	ans = ''
	a_hex = ascii2hex(a).replace('0x','')
	b_hex = ascii2hex(b).replace('0x','')
	ans = ans + hexor(a_hex[0],b_hex[0])
	ans = ans + hexor(a_hex[1],b_hex[1])
	return hex2ascii(ans)
```

## Lucky Guess
As I was developing the python functions, another SIT member found a site ([XOR Cracker](https://wiremask.eu/tools/xor-cracker/)) that guessed the most probable key length as 13

| Key Length | Probability |
| --- | --- |
| 2 | 15.7% |
| 6 | 14.7% |
| **13** | **15.7%** |
| 15 | 9.5% |
| 18 | 9.0% |
| 20 | 8.3% |
| 23 | 7.6% |
| 26 | 9.7% |
| 29 | 5.3% |
| 34 | 4.6% |

Also of note, the site guessed keys similar to `_{KEE_`. Since flags are in the format `ALEXCTF{A-Za-z0-9_*}`, we decided to guess the key as the flag.

### First Guess
Due to the estimated key length of 13, we guessed `ALEXCTF{xxxx}` as the key. XORing the plaintext with this key repeated yielded the following result text:
>```
Dear FriUSN◄☻Rkix<tgSB‼t☺understooT↔GD☻kjs}kk▲F]Y☺used One DTGX☻vbd+ynmL^CIHon scheme∟↔c↔Jcbro<tf_S‼TU is the o^QS↔Gh`rrltgQI‼PDthod that►TY↔OgwhnqazWDRQMy proven DR
_G&mo<c|_DXXE ever if DUO↔Icz bo e[WG↔Recure, LeD↔gX☻mmo|<ih▲^\H☺agree witX↔GX☻rl ~oe.JOZ☺encryptio^↔Y^Jcne+}ly_^@‼
```

**Bingo!** No need to continue with the initial approach. At this point, we could use the partially decoded words to figure out the rest of the key.

### Key Length Actually 26
`Dear Fri` is 8 correct characters. Since the correct characters repeat every 26 characters, we knew the key was 26 characters long.

### Solving
Using `ALEXCTF{xxxxxxxxxxxxxxxxx}` as a base, a SIT team member **Spencer** pointed out that we could XOR the desired result letter (e.g. `e` in `Friends`) with the original cipher text character to determine the key character. I made the python function in his honor:
```python
def spencer(a,b):
	# a: Desired ptxt letter
	# b: Incorrect ptxt letter
	return asciixor(asciixor(b,'x'),a)
```
