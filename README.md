# Mail Pix Tracker

This is a simple bash script to track emails via an invisible pixel, for my personal use.

- Just two shell scripts: `mailpixtracker` here, and  [cgibashopts](https://github.com/ColasNahaboo/cgibashopts)
- Only need any web server able to execute [CGI](https://en.wikipedia.org/wiki/Common_Gateway_Interface) scripts
- It displays some html code to include in your emails. For GMail, you can use the HTMail extension for [Firefox](https://addons.mozilla.org/en-US/firefox/addon/htmail/) or [Chrome](https://chromewebstore.google.com/detail/htmail-insert-html-into-g/omojcahabhafmagldeheegggbakefhlh?hl=en).

Note that the tracking is not foolproof. Most mailers nowadays prompt the user before loading images.

## Installation

- Just copy mailpixtracker into any cgi-enabled directory on your web server,
  and the [cgibashopts](https://github.com/ColasNahaboo/cgibashopts) script
- You can rename mailpixtracker as you want, let's say "my-mpt". Since there are no access control, choosing a hard to guess name will act as a protection, or you can protect the access via directives of your web server.
- Requirements: 
  - bash at least version 4 (see $BASH_VERSION). V4 was released in 2010.
  - [tac](https://man7.org/linux/man-pages/man1/tac.1.html)
  - [base64](https://man7.org/linux/man-pages/man1/base64.1.html)
  - [cut](https://man7.org/linux/man-pages/man1/cut.1.html)

## Configuration

In the same directory where you put the mailtracker script, you can add a config file with the same name but with .conf appended. E.g: "my-mpt.conf"
If you do not want a non-cgi script in this dir, you can set the name of the config file to load as the environment variable `MAILPIXTRACKER_CONF` in your web server configuration.

This script will be interpreted as a bash script, so you can redefine global variables:
- `TZ` timezone used for dates in logs. Default: the server timezone.
- `dir` where to store logs and cached data. Default is ".", th directory where the script is run.
- `dateformat` the format of the linux command "date" to format the timestamp. The default is '%Y-%m-%d.%Hh%M,%S', which gives times like 2025-11-18.13h41,38 for a better readability, but you can use the true ISO format by setting `dateformat='%Y-%m-%dT%H:%M:%S'`
- `style` optional HTML code included in header. Default is a minimal style. You can embed CSS code or link an external stylesheet, e.g:
  - `style='<style>h1 {font-family:arial;}</style>'`
  - `style='<link rel="stylesheet" href="..." />'`
- `header` optional HTML header code included at the start of the html body. Default is empty.
- `footer` optional HTML footer code included at the end of the html body. Default is `<hr><a href='https://github.com/ColasNahaboo/mailpixtracker'>`

Example:

```
TZ=CET
dir=/var/tmp/mailpixtracker
dateformat='%Y-%m-%dT%H:%M:%S'
style='<style>html {font-family: verdana,arial,geneva,helvetica,sans-serif;}</style>'
```

Note that by default, mailpixtracker will create some files and a directory (all starting by the same name as the script itself) in the installation directory. You can change this via the `dir` config variable.

## Example

The page for the tracker of ID 14 and its access logs:

![](screenshot1.png)

## License: GPL V3 (c) 2025 Colas Nahaboo

[Source repository](https://github.com/ColasNahaboo/mailpixtracker)

## Technical details

Files auto-created in `dir` (default is the same cgi-bin directory where mailpixtracker has been installed):

- `mailpixtracker-1pix.png` caches the transparent 1-pixel image used as a tracker in emails
- `mailpixtracker-log/NN` the log (one entry per line) of access to the tracker for id `NN`, tab-separated values of timestamp,ip,hostname,useragent
- `mailpixtracker-log/NN.meta` optionally, the metadata for the log of id `NN`, as key-space-values format one per line, with possible keys:
  - `name` the user-friendly name of the tracker, html-escaped.
  - `note` notes for this tracker, html-escaped and newlines escaped as tabs for a multi-line otes text to fit on a single metadata line

## History

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
