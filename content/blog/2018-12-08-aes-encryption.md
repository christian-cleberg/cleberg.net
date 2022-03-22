+++
date = 2018-12-08
title = "AES Encryption"
description = "Learn more about AES encryption, an advanced encryption technique established by the National Institute of Standards and Technology."
+++

## Basic AES

In case this is your first time hearing about this, AES is known as the **Advanced Encryption Standard**. This
encryption was established by the National Institute of Standards and Technology, sub-selected from the Rijndael family
of ciphers (128, 192, and 256 bits) in 2001. Furthering its popularity and status, the US government chose AES as their
default encryption method for top-secret data, removing the previous standard which had been in place since 1977.

AES has proven to be an extremely safe encryption method, with 7-round and 8-round attacks making no material
improvements since the release of this encryption standard almost two decades ago.

> Though many papers have been published on the cryptanalysis of AES, the fastest single-key attacks on round-reduced AES variants [20, 33] so far are only slightly more powerful than those proposed 10 years ago [23,24].
> 
> \- [Bogdonav, et al.](http://research.microsoft.com/en-us/projects/cryptanalysis/aesbc.pdf)

## How Secure is AES?

In theory, AES-256 is uncrackable due to the massive number of combinations that can be produced. AES-128 is no longer
recommended if you're looking for real security to protect important data. A
semi-short [comic strip](http://www.moserware.com/2009/09/stick-figure-guide-to-advanced.html) from Moserware quickly
explains AES for the general public to understand. Basically AES encrypts the data by obscuring the relationship between
the data and the encrypted data. Additionally, this method spreads the message out. Lastly, the key produced by AES is
the secret to decrypting it. Someone may know the method of AES, but without the key, they are powerless.

To obscure and spread the data out, AES creates a substitution-permutation network. Wikipedia has a simple example of an
SP
network [in this image](https://upload.wikimedia.org/wikipedia/commons/thumb/c/cd/SubstitutionPermutationNetwork2.png/468px-SubstitutionPermutationNetwork2.png)
. This network sends the data through a set of S boxes (using the unique key) to substitute the bits with another block
of bits. Then, a P box will permutate, or rearrange, the bits. This is done over and over, with the key being derived
from the last round. For AES, the key size specifies the number of transformation rounds: 10, 12, and 14 rounds for
128-bit, 192-bit, and 256-bit keys, respectively.

## The Process

1. KeyExpansion: Using [Rijndael's key schedule](https://en.m.wikipedia.org/wiki/Advanced_Encryption_Standard), the keys
   are dynamically generated.
2. AddRoundKey: Each byte of the data is combined with this key using bitwise xor.
3. SubBytes: This is followed by the substitution of each byte of data.
4. ShiftRows: Then, the final three rows are shifted a certain number of steps, dictated by the cipher.
5. MixColumns: After the rows have been shifted, the columns are mixed and combined.

After that happens - steps 2 through 5 repeat for the number of rounds specified by the key. However, the final round
excludes the MixColumns step. As you can see, this is a fairly complex process. One must have a solid understanding of
general mathematic principles to fully understand how the sequence works (and to even attempt to find a weakness).
According to research done by Bogdanov et al., it would take billions of years to brute force a 126-bit key with current
hardware. Additionally, this brute force attack would require storing 2\^88 bits of data! However, there are a few
different attacks that have been used to show vulnerabilities with the use of this technology. Side-channel attacks use
inadvertent leaks of data from the hardware or software, which can allow attackers to obtain the key or run programs on
a user's hardware.

Warning: This is not something you should run out and try to implement in your Hello World app after only a few hours of
research. While AES is extremely efficient in what it does, it takes a lot of time and patience to understand. If you're
looking for something which currently implements AES, check
out [the Legion of the Bouncy Castle](https://www.bouncycastle.org/documentation.html). This Java package can help users
learn more about how to implement cryptography.

## Why Does Encryption Matter?

Why is encryption so important? A popular news story recently features the Australian parliament, where lawmakers are
going to let
officials [force companies build backdoors into their software](https://arstechnica.com/tech-policy/2018/12/australia-passes-new-law-to-thwart-strong-encryption/)
. Most notably, messaging apps are expected to be ordered to do this. Now, we know that encrypted messages are encoded
and decoded using private keys, which are theoretically only known by the sender and receiver. How would a backdoor
allow the government to decrypt these messages when they issue a warrant? From what the law provides, *no one quite
knows how the government will be able to do this*.

Let's ignore the technical difficulties of creating backdoors into code that's been developed to be secure for a minute
here. How would this be possible on a day-to-day level? For most companies, the government would need to notify a
developer that they have a warrant and need the developer to implant a backdoor, so they can spy or do whatever they
want to do. Even if the developer does know how to do that, she would need to submit the code for review. Any reasonable
senior developer would see these code changes and would need to know why these were being made. More realistically, it
would probably be obvious that this code has malicious intent. Additionally, almost all code changes require change
tickets to be filed with the company before code changes can occur. Here's another stop on the paper trail that would
let the company know it's being hacked by its own government.

Supposedly, these warrants would ensure that the person who is being tapped to install the backdoor can not tell anyone
and will not face jail time for implementing this backdoor. What happens when your company finds out? You won't go to
jail, and you can't tell them why you did it. The only options are that they say "Hey, that's totally fine. Hack away."
or they fire you immediately. I'm going to let you decide which is more likely.

Apple, one of the biggest companies pushing for stronger encryption worldwide, has been known to outright refuse
governmental requests. With this new
law, [Apple may pull out the Australian market completely](https://tendaily.com.au/amp/news/australia/a181206zli/if-encryption-laws-go-through-australia-may-lose-apple-20181206)
. Hopefully, this law sees future amendments or complete reversal before people start losing their jobs or blackhats
take advantage of these forced exploits.

**Read More:**
[Federal Information Processing Standards Publication 197 [.pdf]](http://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.197.pdf)
