+++
date = 2019-12-16
title = "Password Security"
description = "Learn about various considerations regarding password security, one of the most important digital highlights of the last decade."
+++

## Users

### Why does it matter?

Information security, including passwords and identities, has become one of the most important digital highlights of the
last decade.
With [billions of people affected by data breaches every year](https://www.usatoday.com/story/money/2018/12/28/data-breaches-2018-billions-hit-growing-number-cyberattacks/2413411002/)
, there's a greater need to introduce strong information security systems. If you think you've been part of a breach, or
you want to check and see, you can use [Have I Been Pwned](https://haveibeenpwned.com/) to see if your email has been
involved in any public breaches. Remember that there's a possibility that a company experienced a breach and did not
report it to anyone.

### How do I protect myself?

The first place to start with any personal security check-up is to gather a list of all the different websites, apps, or
programs that require you to have login credentials. Optionally, once you know where your information is being stored,
you can sort the list from the most-important items such as banks or government logins to less important items such as
your favorite meme site. You will want to ensure that your critical logins are secure before getting to the others.

Once you think you have a good idea of all your different authentication methods, I recommend using a password manager
such as [Bitwarden](https://bitwarden.com/). Using a password manager allows you to automatically save your logins,
create randomized passwords, and transfer passwords across devices. However, you'll need to memorize your "master
password" that allows you to open the password manager. It's important to make this something hard to guess since it
would allow anyone who has it to access every password you've stored in there.

Once you've stored your passwords, make sure you continually check-up on your account and make sure you aren't following
bad password practices. Krebs on Security has a [great blog post](https://krebsonsecurity.com/password-dos-and-donts/)
on password recommendations. Any time that a data breach happens, make sure you check to see if you were included and if
you need to reset any account passwords.

## Developers

### What are the basic requirements?

When developing any password-protected application, there are a few basic rules that anyone should follow even if they
do not follow any official guidelines such as NIST. The foremost practice is to require users to use passwords that are
8 characters or long and cannot easily be guessed. This sounds extremely simple, but it requires quite a few different
strategies. First, the application should check the potential passwords against a dictionary of insecure passwords
such `password`, `1234abc`, or `application_name`.

Next, the application should offer guidance on the strength of passwords being entered during enrollment. Further, NIST
officially recommends **not** implementing any composition rules that make passwords hard to remember (e.g. passwords
with letters, numbers, and special characters) and instead encouraging the use of long phrases which can include spaces.
It should be noted that to be able to keep spaces within passwords, all unicode characters should be supported and
passwords should not be truncated.

### What does NIST recommend?

The National Institute of Standards and Technology ([NIST](https://www.nist.gov)) in the US Department of Commerce
regularly publishes information around information security and digital identity guidelines. Recently, NIST
published [Special Publication 800-63B](https://pages.nist.gov/800-63-3/sp800-63b.html): Digital Identity Guidelines and
Authentication and Lifecycle Management.

> A Memorized Secret authenticator - commonly referred to as a password or, if numeric, a PIN - is a secret value intended to be chosen and memorized by the user. Memorized secrets need to be of sufficient complexity and secrecy that it would be impractical for an attacker to guess or otherwise discover the correct secret value. A memorized secret is something you know.
>
> \- NIST Special Publication 800-63B

NIST offers a lot of guidance on passwords, but I'm going to highlight just a few of the important factors:

- Require passwords to be a minimum of 8 characters (6 characters if randomly generated and be generated using an
  approved random bit generator).
- Compare potential passwords against a list that contains values known to be commonly-used, expected, or compromised.
- Offer guidance on password strength, such as a strength meter.
- Implement a rate-limiting mechanism to limit the number of failed authentication attempts for each user account.
- Do not require composition rules for passwords and do not require passwords to be changed periodically (unless
  compromised).
- Allow pasting of user identification and passwords to facilitate the use of password managers.
- Allow users to view the password as it is being entered.
- Use secure forms of communication and storage, including salting and hashing passwords using a one-way key derivation
  function.

NIST offers further guidance on other devices that require specific security policies, querying for passwords, and more.
All the information discussed so far comes
from [NIST Special Publication 800-63B](https://pages.nist.gov/800-63-3/sp800-63b.html) but NIST offers a lot of
information on digital identities, enrollment, identity proofing, authentication, lifecycle management, federation, and
assertions in the total [NIST SP 800-63: Digital Identity Guidelines](https://pages.nist.gov/800-63-3/).


