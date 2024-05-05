Consonants-only Base 32
===================================

This brief document specifies a consonants-only alphabet for base 32 encoding.

Overview
-----------------------------------

The proposed alphabet aims to prevent the occurrence of words in base 32 encoded strings, reducing the risk of inadvertently generating negative or offensive language.

Additionally, the absence of digits in the alphabet eliminates the risk of parsing the encoded strings as numbers.

Some use cases can be:

* Embed word-safe IDs into a URL slug, e.g. `www.example.com/this-is-a-slug-xhPhSqz`;
* Represent 64-bit IDs as strings in languages such as Javascript, which hold 53-bit integers in memory;
* Have an alternative string format for UUIDs, which are disliked by some people for its length and dashes.

Description
-----------------------------------

The proposed alphabet is a list of sixteen uppercase and lowercase letters, resulting in a _case-sensitive_ list of 32 symbols. It is made up of a series of letters from the [Latin script](https://en.wikipedia.org/wiki/Latin_script), except the letters `A`, `E`, `I`, `O`, `U`, `J`, `V`, `W`, `Y`, and `L`. The excluded letters are vowels or those historically evolved from vowels. Additionally, the letter `L` is excluded due to its lowercase resemblance to the letter `I`.

This alphabet can be used to both encode integers and byte arrays. The integers should be encoded by the [regular algorithm](https://stackoverflow.com/questions/2267362/how-to-convert-an-integer-to-a-string-in-any-base) which uses remainder operation. The byte arrays should be encoded following the [RFC 4648](https://datatracker.ietf.org/doc/html/rfc4648) recipe, except for the alphabet.

We do not recommend using splitting characters such as dashes. We also do not recommend using padding such as equals signs. In both cases, it must be expressed in documentation when necessary for a particular application.

The following table maps each symbol to its respective value.

| Value | Symbol | Value | Symbol | Value | Symbol | Value | Symbol |
| :---: | :----: | :---: | :----: | :---: | :----: | :---: | :----: |
| 0 | B | 8 | N | 16 | b | 24 | n |
| 1 | C | 9 | P | 17 | c | 25 | p |
| 2 | D | 10 | Q | 18 | d | 26 | q |
| 3 | F | 11 | R | 19 | f | 27 | r |
| 4 | G | 12 | S | 20 | g | 28 | s |
| 5 | H | 13 | T | 21 | h | 29 | t |
| 6 | K | 14 | X | 22 | k | 30 | x |
| 7 | M | 15 | Z | 23 | m | 31 | z |

The following [ABNF](https://en.wikipedia.org/wiki/Augmented_Backus%E2%80%93Naur_form) provides the formal definition of the alphabet.

```abnf
consonants-only-alphabet   = upper-case-consonants lower-case-consonants
upper-case-consonants      = %s"BCDFGHKMNPQRSTXZ"
lower-case-consonants      = %s"bcdfghkmnpqrstxz"
```

How to encode integers
-----------------------------------

The following table is a list of integers encoded to our consonants-only base 32. The algorithm utilized is the based on the remainder operation (%).

| Positive integers    | Base 32 strings |
|:--------------------:|:---------------:|
| `0x000000000000`     | `B`             |
| `0x000000000ac9`     | `DkP`           |
| `0x000000b1cd82`     | `RFfSD`         |
| `0x00057e088d9b`     | `hzBcFSr`       |
| `0x7fffffffffff`     | `Fzzzzzzzzz`    |

The following Python code was used to generate the table.

```python
#!/usr/bin/python3
alphabet = "BCDFGHKMNPQRSTXZbcdfghkmnpqrstxz"
def encode(number, alphabet):
    output = ""
    base = len(alphabet)
    while number:
        output += alphabet[number % base]
        number //= base
    return output[::-1] or alphabet[0]

print(encode(0x000000000000, alphabet));
print(encode(0x000000000ac9, alphabet));
print(encode(0x000000b1cd82, alphabet));
print(encode(0x00057e088d9b, alphabet));
print(encode(0x7fffffffffff, alphabet));
```

How to encode byte arrays
-----------------------------------

The following table shows a list of UUIDs encoded to our consonants-only base 32. The algorithm utilized is described in the RFC 4648.

| UUIDs version 4                        | Base 32 strings              |
|:--------------------------------------:|:----------------------------:|
| `00000000-0000-0000-0000-000000000000` | `BBBBBBBBBBBBBBBBBBBBBBBBBB` |
| `222e11dd-91d1-400d-994c-dadd71d28015` | `GNmCFmScqHBBrKQSrRXmFggBDg` |
| `c460fffc-c456-4df7-a28e-ebaffcf78e3a` | `ncbZzzKGQpKzZNgXtXmzptsXMN` |
| `75a847d7-0c6f-4119-a096-0e69536f925d` | `XkgGZhnSTtBcfNGkCpghKrsdRg` |
| `ffffffff-ffff-ffff-ffff-ffffffffffff` | `zzzzzzzzzzzzzzzzzzzzzzzzzs` |

The following Python code was used to generate the table.

```python
#!/usr/bin/python3
from uuid import UUID;
from base64 import b32encode;
alphabet = "BCDFGHKMNPQRSTXZbcdfghkmnpqrstxz"
def encode(value, alphabet): 
    standard = "ABCDEFGHIJKLMNOPQRSTUVWXYZ234567"
    translator = str.maketrans(standard, alphabet)
    return b32encode(value.bytes).decode("ascii").translate(translator).replace("=", "")

print(encode(UUID("00000000-0000-0000-0000-000000000000"), alphabet))
print(encode(UUID("222e11dd-91d1-400d-994c-dadd71d28015"), alphabet))
print(encode(UUID("c460fffc-c456-4df7-a28e-ebaffcf78e3a"), alphabet))
print(encode(UUID("75a847d7-0c6f-4119-a096-0e69536f925d"), alphabet))
print(encode(UUID("ffffffff-ffff-ffff-ffff-ffffffffffff"), alphabet))
```

License
-----------------------------------

This document is available under the [CC0 license](https://en.wikipedia.org/wiki/Creative_Commons_license).

