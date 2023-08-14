+++
author = "Javad Hamidi"
date = "2021-03-14"
description = ""
tags = ["Crypto"]
title = "Various Vernacular - UTCTF 2021"

+++
We're given the encrypted flag `wmysau{foeim_Tfusoli}` along with some additional encrypted text to help us 'Hkgxologflutleiaymt xgf Azutgkrftmtf ltmntf ERW wfr ELW wfmtk Rkweq'.

Some familiarity with common cyphers, along with the hint 'This is a substitution cypher', give us a pretty good direction to pursue so we decided to use [this online tool](https://www.boxentriq.com/code-breaking/cryptogram "Substitution Cipher Solver Tool") for brute-forcing the solution.

However, trying brute-forcing the text rendered nothing more decipherable than the initial text. Instead, we look to the challenge title, where we find that the word vernacular refers to local languages or dialects. Perhaps the cypher text is in a different language? The hint 'The plaintext may not necessarily be in English' confirms this.

Some experimentation with different languages and google translates produces the string in German 'provisionsgeschmüte von mageordneten setzen cdu und csu unter druck'. In English, 'disregarding commissions from the ordered people put cdu and csu under pressure.' Looks like we found a valid substitution! Trying (using all English characters) on the encrypted flag, with attention to capitalisation, gave us `utflag{nicht_English}`.