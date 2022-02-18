# dmarc-analyse

Tool to analyse DMARC reports in IMAP an folder and optionally send every day or week a report via email

## Installation

```bash
$ cd /root
$ git clone https://github.com/wooni005/dmarc_analyse.git
$ cd dmarc-analyse
$ chmod +x dmarc-analyse.*
```

Install the required Python3 modules:

```bash
$ pip3 install IMAPClient email
```

## Configuration

First copy the 2 example files into work files:

```bash
$ cd dmarc-analyse
$ cp config.py.example config.py
$ cp dmarc-analyse.sh.example dmarc-analyse.sh
```

Fill in your IMAP mailserver settings and the corresponding credentials to read the IMAP folder

```bash
$ nano config.py
IMAP_HOST = 'imap.yourhost.com' # Or Gmail: 'imap.gmail.com'
IMAP_PORT = 993
USERNAME = 'your@email'
PASSWORD = 'yourPassword'
MAILBOX_FOLDER = 'VPS/DMARC'
```

And change the email address in the dmarc-analyse.sh script:

```bash
$ nano dmarc-analyse.sh
...
TO_EMAIL_ADDRESS="your@email"
...
```

## Test the script

If you've some DMARC reports in your VPS/DMARC folder, you can execute the script.

```
$ ./dmarc-analyse.py
```

The Python script only processes the unread reports. To read all reports, use the --all option

```
$ ./dmarc-analyse.py --all
```

## Cronjob

The DMARC report are using a 24h period from 1:00 to 0:59.
Send the dmarc_analyse report every day after 1:00.
In the next crontab it's executed at 1:03.

```bash
$ nano /etc/crontab
...
3  1    * * *   root /root/dmarc-analyse/dmarc-analyse.sh 2>&1
```

## Email report

This is an example of a report:

```
Analyzing DMARC reports in the IMAP folder 'VPS/DMARC' (user: your@email)

Report: google.com     for   yourdomain.com (Period 2022-02-10 01:00:00 - 2022-02-11 00:59:59)
    - record 1: OK!  11 email(s) checked send from: vps.yourdomain.com       (IP: 12.34.56.78)
    - record 2: OK!   1 email(s) checked send from: vps.yourdomain.com       (IP: 12.34.56.78)
 Total of 12 email(s) checked and all OK!


DMARC analyse summary:
 - Total DMARC report mails processed: 1
 - Nr of messages checked: 12
 - Nr of messages passed:  12
 - Nr of messages failed:   0
```

## DMARC rollout strategy

Background info and rollout strategy:
https://support.google.com/a/answer/10032473?hl=en&ref_topic=2759254
