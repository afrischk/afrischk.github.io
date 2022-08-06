---
layout: post
title: Cryptopals Challenges: Base Set 1.1-1.6 (XOR)
published: true
---

I thought it was time to delve deeper into the field of cryptography and C.
Thats why I challenged myself solving the famous Cryptopals challenges using
the C language. Here is what I have so far...

### Craxor

While solving the cryptopals challenges I thought it is probably a good idea to
implement a commandline application which I can reuse and extend later. So the
idea for Craxor was born. Craxor can crack xor encrypted data by first guessing
the key length used, second uses the most probable key to crack the encryption.
Currently only english language is supported, because the scoring function is
only capable of scoring english text properly.

On top of this I wrote the unit tests with the help of unity. These tests
reflect the xor related subtasks from the base set 1.

You can find the repository [here](https://github.com/afrischk/cryptography).
