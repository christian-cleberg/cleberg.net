+++
date = 2022-06-22
title = "Daily Plaintext Poetry via Email"
description = "A quick tutorial showing how to use Python and the PoetryDB API to email daily poetry."
draft = false
+++

## Source Code

I don't want to bury the lede here, so if you'd like to see the full source 
code I use to email myself plaintext poems daily, visit the repository: 
[dailypoem](https://git.cleberg.net/cgit.cgi/dailypoem.git/).

## My Daily Dose of Poetry

Most of my programming projects are small, random projects that are made 
strictly to fix some small problem I have or enhance my quality of life.

In this case, I was looking for a simply and easy way to get a daily dose of 
literature or poetry to read in the mornings.

However, I don't want to sign up for a random mailing list on just any website. 
I also don't want to have to work to find the reading content each morning, as I 
know I would simply give up and stop reading daily.

Thus, I found a way to deliver poetry to myself in plain-text format, on a daily 
basis, and scheduled to deliver automatically.

## Prerequisites

This solution uses Python and email, so the following process requires the 
following to be installed:

1. An SMTP server, which can be as easy as installing `mailutils` if you're on 
a Debian-based distro.
2. Python (& pip!)
3. The following Python packages: `email`, `smtplib`, `json`, and `requests`

## Breaking Down the Logic

I want to break down the logic for this program, as it's quite simple and 
informational.

### Required Packages

This program starts with a simple import of the required packages, so I wanted 
to explain why each package is used:

```py
from email.mime.text import MIMEText # Required for translating MIMEText
import smtplib # Required to process the SMTP mail delivery
import json # Required to parse the poetry API results
import requests # Required to send out a request to the API
```

### Sending the API Request

Next, we need to actually send the API request. In my case, I'm calling a random 
poem from the entire API. If you want, you can call specific poems or authors 
from this API.

```py
json_data = requests.get('https://poetrydb.org/random').json()
```

This gives us the following result in JSON:

```json
[
  {
    "title": "Sonnet XXII: With Fools and Children",
    "author": "Michael Drayton",
    "lines": [
      "To Folly",
      "",
      "With fools and children, good discretion bears;",
      "Then, honest people, bear with Love and me,",
      "Nor older yet, nor wiser made by years,",
      "Amongst the rest of fools and children be;",
      "Love, still a baby, plays with gauds and toys,",
      "And, like a wanton, sports with every feather,",
      "And idiots still are running after boys,",
      "Then fools and children fitt'st to go together.",
      "He still as young as when he first was born,",
      "No wiser I than when as young as he;",
      "You that behold us, laugh us not to scorn;",
      "Give Nature thanks you are not such as we.",
      "Yet fools and children sometimes tell in play",
      "Some, wise in show, more fools indeed than they."
    ],
    "linecount": "15"
  }
]
```

### Parsing the API Results

In order to parse this into a readable format, we need to use the `json` package 
and extract the fields we want. In the example below, I am grabbing every field 
presented by the API.

For the actual poem content, we need to loop over each line in the `lines` 
variable since each line is a separate string by default.

> You *could* also extract the title or author and make another call out to the 
> API to avoid having to build the plaintext poem with a loop, but it just 
> doesn't make sense to me to send multiple requests when we can create a simple 
> loop on our local machine to work with the data we already have.
>
> For 
> [example](https://poetrydb.org/title/Sonnet%20XXII:%20With%20Fools%20and%20Children/lines.text), 
> look at the raw data response of this link to see the poem's lines returned 
> in plaintext.

```py
title = json_data[0]['title']
author = json_data[0]['author']
line_count = json_data[0]['linecount']
lines = ''
for line in json_data[0]['lines']:
  lines = lines + line + "\n"
```

### Composing the Email

Now that I have all the data I need, I just need to compose it into a message 
and prepare the message metadata.

For my daily email, I want to see the title of the poem first, followed by the 
author, then a blank line, and finally the full poem. This code snippet combines 
that data and packages it into a MIMEText container, ready to be emailed.

```py
msg_body = title + "\n" + author + "\n\n" + lines
msg = MIMEText(msg_body)
```

Before we send the email, we need to prepare the metadata (subject, from, to, 
etc.):

```py
sender_email = 'example@server.local'
recipient_emails = ['user@example.com']
msg['Subject'] = 'Your Daily Poem (' + line_count + ' lines)'
msg['From'] = sender_email
msg['To'] = recipient_email
```

### Sending the Email

Now that I have everything ready to be emailed, the last step is to simply 
connect to an SMTP server and send the email out to the recipients. In my case, 
I installed `mailutils` on Ubuntu and let my SMTP server be `localhost`.

```py
smtp_server = 'localhost'
s = smtplib.SMTP(smtp_server)
s.sendmail(sender_email, recipient_emails, msg.as_string())
s.quit()
```

## The Result!

Instead of including a screenshot, I've copied the contents of the email that 
was delivered to my inbox below since I set this process up in plaintext format.

```text
Date: Wed, 22 Jun 2022 14:37:19 +0000 (UTC)
From: REDACTED
To: REDACTED
Subject: Your Daily Poem (36 lines)
MIME-Version: 1.0
Content-Transfer-Encoding: 8bit
Content-Type: text/plain; charset=utf-8

Sonnet XXII: With Fools and Children
Michael Drayton

With fools and children, good discretion bears;
Then, honest people, bear with Love and me,
Nor older yet, nor wiser made by years,
Amongst the rest of fools and children be;
Love, still a baby, plays with gauds and toys,
And, like a wanton, sports with every feather,
And idiots still are running after boys,
Then fools and children fitt'st to go together.
He still as young as when he first was born,
No wiser I than when as young as he;
You that behold us, laugh us not to scorn;
Give Nature thanks you are not such as we.
Yet fools and children sometimes tell in play
Some, wise in show, more fools indeed than they.
```

## Scheduling the Daily Email

Last, but not least, is scheduling this Python script with `crontab`. To 
schedule a script to run daily, you can add it to the `crontab` file. To do 
this, open `crontab` in editing mode:

```bash
crontab -e
```

In the file, simply paste the following snippet at the bottom of the file and 
ensure that the file path is correctly pointing to wherever you saved your 
Python script:

```config
0 8 * * * python3 /home/<your_user>/dailypoem/main.py
```

We have now set-up the script and scheduled it to run daily at 08:00!
