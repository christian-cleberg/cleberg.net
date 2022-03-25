+++
date = 2022-02-11
title = "Most Corporate Security is Trash"
description = "Spending time working in corporate security or compliance can give you a cynical view of data protection."
draft = false
+++

## It's Hard to Explain How Bad Most Corporate Security Is

It's true. If you were to look at the security of information inside a corporation, you would be astounded at how easily something could go massively wrong.

My career entails looking through information systems (security, privacy, reliability, etc.) for various clients every day, so I tend to get a good picture of standard security failures and control gaps. While some companies or departments are great, most are not. Processes are messy and convoluted, applications are moved to the cloud and assumed to be safe (_"AWS is responsible for all that security stuff!"_ lol no), lynch-pin employees exist without trained backups, data has no quality standards, etc. You get the picture.

> Side story: We once tested a badge system and found that the RFID scanner would allow basically any badge into a sensitive room as long as they were an employee. It was logged and when we brought it up, they reviewed the log to confirm all people who accessed the room were appropriate. Still, it was ridiculous that the doors were set to let any badge in.

For example, when Target experienced their famous 2013 data breach, they were notified numerous times by their detection systems about malware installations. Why didn't they act upon these notifications? Who knows, but it wouldn't surprise me if alerts weren't reviewed at all (_"our IDS is too sensitive, so we just ignore most alerts"_) or someone just saw that the software was from one of their official HVAC vendors so they assumed it was safe.

Also, Target's systems allowed the attackers to move from the initial, low-risk areas to much more sensitive areas of the network, suggesting a lack of basic network segregation (or if segregation did exist, perhaps duplicated credentials across network segments/servers existed).

The reasons for a general lack of security are numerous, but generally occur because companies follow a compliance-driven security approach. This means they will develop and implement security measures if the law requires them or if it will protect against loss of money/reputation/etc.

It's comedic (maybe "sad" is a better word?) how much a company will pay to protect it's intellectual property (IP) - and then they'll copy-and-paste a terrible solution to comply with user data protection laws, since it's the lowest-cost option.

## Thoughts In Relation to Consumer Software

All things considered, I have a very hard time trusting consumer products that do not make their source code available for review - I don't necessarily care what the license is as long as I can audit it. If I can't do that, I have very little trust left.

I have worked in various information security and audit firms, so I have seen that things can be going wrong in a company or with a software product - yet it will still pass "without issue".

Why? Well, just because issues were noted doesn't necessarily mean that a control failed - a control can have issues but still meet a firm's level of tolerable failure. However, tolerable failure rates are judgemental in nature. This is why I strongly prefer open-source software products and do my best to review them in-depth whenever I can (and you should too!).

Auditing software before you use it can take a bit of time and will involve research about unfamiliar topics or software languages/methods, but it's definitely worth it if you're a delusional paranoid techie like me.
