# [CR2: Many Time Secrets](https://ctf.oddcoder.com/challenges#CR2: Many time secrets)
I learned about one-time pads (OTP) in my intro to cyber security class. However, the hint provided made it clear that the cipher was repeating the OTP.

## Approach 1
My initial approach was to xor the cipher with itself at an offset. At the right offset, this would cancel out the key and provide two plaintexts xor'd with eachother. I began creating some python functions to aid with this strategy.
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

## Approach 2
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

### Key Length Actually 26
`Dear Fri` is 8 correct characters. Since the correct characters repeat every 26 characters, we knew the key was 26 characters long.

### Solving
Using `ALEXCTF{xxxxxxxxxxxxxxxxx}` as a base, a SIT team member **Spencer** pointed out that we could XOR the desired result letter (e.g. `e` in `Friends`) with the original cipher text character to determine the key character. I made the python function in his honor:
```python
def spencer(a,b):
	return asciixor(asciixor(b,'x'),a)
```
