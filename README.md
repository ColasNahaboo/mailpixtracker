# Mail Pix Tracker

This is a simple bash script to track emails via an invisible pixel, for my personal use.

- Just two shell scripts: `mailpixtracker` here, and  [cgibashopts](https://github.com/ColasNahaboo/cgibashopts)
- Only needs any web server able to execute [CGI](https://en.wikipedia.org/wiki/Common_Gateway_Interface) scripts
- It generates for you a simple HTML line to include in your emails

Note that the tracking is not foolproof. Most mailers nowadays prompt the user before loading images.

## Usage
- Go to the script URL on your web server.<br>E.g: `https://my.server.org/cgi-bin/mailpixtracker`
- Create a new tracker. You will need one separate tracker (with a unique ID number) for each email, to track each email in a separate log. You can optionally add a meaningful name.
- Mailpixtracker will then show you the log page for the tracker. You can edit its name and various notes at any time.
- When composing your email, copy/paste into it the yellow highlighted HTML line at the top of the log page, via your installed browser extension or email software.
- Note that the log will begin to fill up while composing your mail once the tracker is installed. The first lines in the log - yours - are always highlighted in grey, to differentiate them from recipients.
- Once the email is sent, each time people will read it, and have allowed their mailers to display images, the mailtracker log page will add an entry.
- Log entries are colored with a different color for each client IP address, for ease of reading.
- New log entries added since last time the log was viewed have a yellow rectangle at their left side.
- The Notes field is a convenient way to store details about the sent email for this tracker ID, as well as remembering which timestamps correspond to specific events. Use it!

## Example

The log page for the tracker of ID #17 and its access logs:

![](screenshot1.png)

## Installation

- Just copy the bash script `mailpixtracker` into any cgi-enabled directory on your web server, and also the [cgibashopts](https://github.com/ColasNahaboo/cgibashopts) script
- You can rename the "mailpixtracker" file as you want, e.g to "a-name-hard-to-guess". Since there are no access control, choosing a hard to guess name will act as a protection, or you can protect the access via directives of your web server, but its more complex (see "Access Control" below). <br>E.g. `https://my.server.org/cgi-bin/a-name-hard-to-guess`
- If using a webmail, I recommend installing a browser extension to allow easy inclusion of the generated tracker HTML code in your emails. For GMail, you can use the HTMail extension for [Firefox](https://addons.mozilla.org/en-US/firefox/addon/htmail/) or [Chrome](https://chromewebstore.google.com/detail/htmail-insert-html-into-g/omojcahabhafmagldeheegggbakefhlh?hl=en).
- Requirements: 
  - any web server able to execute [CGI](https://en.wikipedia.org/wiki/Common_Gateway_Interface) scripts
  - linux, or some linux-compatible environement (e.g. cygwin on windows)
  - bash at least version 4 (see $BASH_VERSION). V4 was released in 2010.
  - Some common linux utilities: [tac](https://man7.org/linux/man-pages/man1/tac.1.html), [base64](https://man7.org/linux/man-pages/man1/base64.1.html), [cut](https://man7.org/linux/man-pages/man1/cut.1.html)

## Configuration

In the same directory where you put the mailtracker script, you can add a config file with the same name but with .conf appended. E.g: "my-mpt.conf"
If you do not want a non-cgi script in this dir, you can set the name of the config file to load as the environment variable `MAILPIXTRACKER_CONF` in your web server configuration.

This script will be interpreted as a bash script, so you can redefine global variables:
- `TZ` timezone used for dates in logs. Default: the server timezone.
- `dir` where to store logs and cached data. Default is ".", th directory where the script is run.
- `dateformat` the format of the linux command "date" to format the timestamp. The default is '%Y-%m-%d.%Hh%M,%S', which gives times like 2025-11-18.13h41,38 for a better readability, but you can use the true ISO format by setting `dateformat='%Y-%m-%dT%H:%M:%S'`<br>Warning: formats must ensure their alphabetical order obey the chronological order: YYYY-MM-DD is OK, but not MM/DD/YYYY.
- `style` optional HTML code included in header. Default is a minimal style. You can embed CSS code or link an external stylesheet, e.g:
  - `style='<style>h1 {font-family:arial;}</style>'`
  - `style='<link rel="stylesheet" href="..." />'`
- `header` optional HTML header code included at the start of the html body. Default is empty.
- `footer` optional HTML footer code included at the end of the html body.<br>Default is `<hr><a href='https://github.com/ColasNahaboo/mailpixtracker'>`

Example:

```
TZ=CET
dir=/var/tmp/mailpixtracker
dateformat='%Y-%m-%dT%H:%M:%S'
style='<style>html {font-family: verdana,arial,geneva,helvetica,sans-serif;}</style>'
```

Note that by default, mailpixtracker will create some files and a directory (all starting by the same name as the script itself) in the installation directory. You can change this via the `dir` config variable.

If you want to implement access control to the script in your server, be aware that access to the `pix/NN` sub-urlshould always be enabled for all.<br>
E.g. access to `https://my.server.org/cgi-bin/mailpixtracker` may be restricted, but all accesses to `https://my.server.org/cgi-bin/mailpixtracker/pix/NN` should be allowed.

### Access Control
If you want to allow other users to use your mailpixtracker installation, I recommend generating a unique name for the script for them to use by creating a symbolic link to your mailpixtracker file, so you can remove their access simply by removing the symbolic link and associated files. `pwgen` is a handy utility to generate unique names.<br>
E.g: `cd /my-server-cgi-directory; ln -s a-name-hard-to-guess ciefahnaiteiQu6a`<br>
Thus you can tell them to use `https://my.server.org/cgi-bin/ciefahnaiteiQu6a`<br>
And to remove their access: `rm /my-server-cgi-directory/ciefahnaiteiQu6a*`

## License: GPL V3 (c) 2025 Colas Nahaboo

[GitHub Source repository](https://github.com/ColasNahaboo/mailpixtracker)

## Technical details

Files auto-created in `dir` (default is the same cgi-bin directory where mailpixtracker has been installed):

- `mailpixtracker-1pix.png` caches the transparent 1-pixel image used as a tracker in emails
- `mailpixtracker-log/NN` the log (one entry per line) of access to the tracker for id `NN`, tab-separated values of timestamp,ip,hostname,useragent
- `mailpixtracker-log/NN.meta` optionally, the metadata for the log of id `NN`, as key-space-values format one per line, with possible keys:
  - `name` the user-friendly name of the tracker, html-escaped.
  - `note` notes for this tracker, html-escaped and newlines escaped as tabs for a multi-line otes text to fit on a single metadata line
  
If you want to remove the logs for a tracker `NN`, just remove `mailpixtracker-log/NN` and `mailpixtracker-log/NN.meta` on your server.

## History

- v2.2.2 2025-12-07 IP adress index number shown in left column of logs
- v2.2.1 2025-11-27 optional global mpt.conf for all names, env var MAILPIXTRACKER_GLOBALCONF
- v2.2.0 2025-11-25 new log entries signaled.
- v2.1.1 2025-11-25 UI display now "#NN" instead of "for NN"
- v2.1.0 2025-11-25 smaller htmail icon, can edit name and note metadata
- v2.0.0 2025-11-24
  - log lines format change: tab-separated values
  - v2 only logs in the v2 format, but can read the v1 format.
  - metadata for the log files
  - tabular output of the log lines for better readability
  - no more mailpixtracker-n file
  - now requires cgibashopts
  - on tracker creation, a name can be given to the tracker
- v1.0.4 2025-11-21 log lines have one color per distinct IP. First one is always grey.
- v1.0.3 2025-11-21 $MAILPIXELTRACKER_CONF env var to define config file location. Default footer identifying the script.
- v1.0.2 2025-11-16 autocreate `$dir` if needed.
- v1.0.0 2025-11-16 Initial released version.
