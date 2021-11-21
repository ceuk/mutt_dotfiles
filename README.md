# Mutt dotfiles

<p float="left">
  <img src="https://user-images.githubusercontent.com/1681236/142619631-abd51f53-fea9-449d-a91e-52a07c1a24f4.png" width="49.5%" />
  <img src="https://user-images.githubusercontent.com/1681236/142620913-22247cb9-66bd-48e3-8387-e42650637d35.png" width="49.5%" /> 
</p>

Robust Mutt configs with examples for the following account types:

* Generic IMAP/SMTP
* Google (Gmail/Gsuite etc) via IMAP/SMTP
* Microsoft Office365 via IMAP/SMTP
* Microsoft Office365 using DavMail (useful if app passwords or direct IMAP/SMTP access is disabled)

## Some Features

* Query and save contacts via abook, goobook or ldapsearch (e.g. for Office365 accounts, query only)
* Good calendar invite handling: render calendar invites inline and respond to them with `A` when viewing the calendar invite attachment
* Nice handling of inline text/html rendering (use `C-l` to open up all links the email - great for quickly finding unsubscribe links)
* Secure configs (no hardcoded passwords etc)
* Fast global search of all mail in every account or all mail in a single account via NotMuch
* Everything looks really nice :)
* Includes all peripheral configs for isync, msmtp, notmuch etc 

## Setup

* Install any dependencies you need based on your use cases from the below list
* Move the stuff in .config into your existing .config directoy inside your home folder
* Move the stuff you need from the bin folder into /usr/local/bin (don't forget to `chmod +x` them)
* Go through each config file and customize it - there are comments/instructions in each file
* Enable any systemd services you need (or use something like cron)

## Dependencies

* NeoMutt (latest version, all compile options enabled)
* [glow](https://github.com/charmbracelet/glow) (nicer plain text email rendering)
* Lynx (render HTML emails, W3m works well too)
* Notmuch (for email search)
* Isync (for syncing emails via IMAP)
* MSMTP (for sending emails via SMTP)
* Perl (for rendering calendar invites - you might need to install some perl deps via CPAN, can't remember)
* python-vobject (for interacting with calendar invites)
* python-icalendar (for interacting with calendar invites)
* python-pytz (for interacting with calendar invites)
* python-tzlocal (for interacting with calendar invites)
* DavMail (if you want to use Office365/Outlook mail and the company locks down IMAP access)
* openldap (for using ldapsearch if you want to look up contacts on office365 etc - required DavMail)
* abook (basic contact management)
* goobook (if you want to use google contacts instead)
* gcalcli (if you want to import events into a google calendar)
* urlscan (to quickly show/open links within an email)
* pandoc (if you want to compose HTML emails in markdown)

## Contents

* `./neomutt.desktop` - a desktop file for opening mutt via the GUI inside the Kitty terminal. Stick it in /usr/share/applications
* `bin/import_calendar_invite` - imports a calendar invite into google calendar with gcalcli and then calls mutt-ical to respond to the invite
* `bin/ldap_owa_query` - allows contact searching via LDAP (used for coroprate office365/outlook accounts)
* `bin/mailsync` -  a great script that syncs your mail, originally written by Luke Smith
* `bin/mutt-ical` - used to respond to email calendar invites
* `bin/mutt-trim` - clean up emails when quoting reply
* `bin/mutt-vcalendar-filter` - used to render email calendar invites in Mutt
* `bin/mutt-viewical` - used to render email calendar invites in Mutt
* `.config/isync/mbsyncrc` - configuration file for isync (used to sync your emails). There are examples of many different account types to use as a starting point
* `.config/msmtp/config` - configuration file for msmtp (used to send emails). There are examples of many different account types to use as a starting point
* `.config/mutt/accounts/*` - individual config files for each email account you wish to use in Mutt. There are examples of many different account types to use as a starting point
* `.config/mutt/muttrc` - the main Mutt config - use this to customize various settings/macros/bindings etc
* `.config/mutt/styles.muttrc` - my custom styling for Mutt - you shouldn't need to tweak this much/at all
* `.config/mutt/dracula.muttrc`  - the dracula color scheme - remove or replace if you want different colors
* `.config/mutt/mailcap` - mailcap file for detrening how to render different types of emails/attachments - customize as needed (there's already quite a lot of handy stuff in there)
* `.config/notmuch/notmuchrc` - config file for notmuch (used for searching emails) - you will need to change some values in here such as your name/email
* `.config/systemd/user/calendar-remind*` - systemd timer/service files for repeatedly calling `gcalcli remind` (to generate notifications 15 mins before an event starts). Enable with `systemctl --user enable calendar-remind.timer` or delete if you don't need.
* `.config/systemd/user/gcalcli*` - systemd timer/service files for repeatedly calling `gcalsync` (to sync gcalcli with google calendar). Enable with `systemctl --user enable gcalcli.timer` or delete if you don't need.
* `.config/systemd/user/mailsync*` - systemd timer/service files for repeatedly calling `mailsync` (to sync your emails). Enable with `systemctl --user enable mailsync.timer`

## Key Bindings

All default keys are unbound to remove random, unwanted bindings and commands. Therefore all bindings are explicit.

Press `?` inside Mutt to see available keybinds for your current context.

### General

* `q` exit/close/back
* `<escape>` abort command
* `?` help
* `/` search
* `j` scroll down 
* `k` scroll up
* `gg` first item/top
* `G` last item/bottom
* `<return>` open/select/confirm
* `<tab>` trigger auto-complete
* `n` (when searching) search next
* `Shift-n` or `p` (when searching) search previous
* `Ctrl-u` scroll half a page upwards
* `Ctrl-d` scroll half a page downwards
* `Ctrl-n` next unread message
* `Ctrl-p` previous unread message
* `zz` center screen on current selection (similar to Vim)
* `zt` center screeen at top of current selection (similar to Vim)
* `zb` center scren at bottom of current selection (similar to Vim)

### Index-specific

* `<left>` or `<right>` or `h`  toggle thread open/collapse 
* `g` `i` go to inbox
* `g` `s` go to sent items
* `g` `d` go to drafts
* `g` `a` go to archive
* `g` `Shift-s` go to spam
* `f` change folder
* `Ctrl-b` toggle sidebar
* `Ctrl-j` sidebar next item
* `Ctrl-k` sidebar previous item
* `Ctrl-o` open current sidebar item
* `<space>` flag current email (toggle)
* `t` tag current email (toggle)
* `T` tag current thread (toggle)
* `Shift-m` `i` move email(s) to inbox (works on tagged emails)
* `Shift-m` `s` move email(s) to sent items (works on tagged emails)
* `Shift-m` `d` move email(s) to drafts (works on tagged emails)
* `Shift-m` `a` move email(s) to archive (works on tagged emails)
* `Shift-m` `Shift-S` move email(s) to spam (works on tagged emails)
* `Shift-c` `i` copy email(s) to inbox (works on tagged emails)
* `Shift-c` `s` copy email(s) to sent items (works on tagged emails)
* `Shift-c` `d` copy email(s) to drafts (works on tagged emails)
* `Shift-c` `a` copy email(s) to archive (works on tagged emails)
* `Shift-c` `Shift-S` copy email(s) to spam (works on tagged emails)
* `d` mark current email(s) for deletion (works on tagged emails)
* `u` undelete email(s) (works on tagged emails)
* `Shift-d` quick delete current email(s) (works on tagged emails)
* `Shift-a` quick archive current email(s) (works on tagged emails)
* `l` add/edit label
* `\` filter (limit) current mailbox (see [filters cheat sheet](https://github.com/fredrikhl/cheatsheets/blob/master/mutt-limit.md))
* `x` clear current filter/limit
* `Ctrl-i` only show flagged messages (press `x` to clear)
* `Shift-l` filter by label
* `+`  link current thread with a tagged thread
* `c` compose new mail
* `Ctrl-r` recall draft (postponed) message
* `r` reply to message
* `Shift-r` reply all
* `Shift-f` forward message
* `v` view email attachments
* `|` pipe current email to shell command
* `Ctrl-a` mark all messages as read
* `$` sync mailbox to local filesystem
* `o` remote sync current account (send/receive from IMAP)

### Pager-specific (email view)

* `Shift-j`  next email
* `Shift-k`  previous email
* `r` reply to message
* `Shift-r` reply all
* `Shift-f` forward message
* `v` view email attachments
* `|` pipe current email to shell command
* `Ctrl-l` call urlscan to show all links in the current email
* `Shift-a` (when viewing a calendar invite) respond to invite

### Attachment List

* `s` save current attachemnt to ~/Downloads

### Compose-specific (the confirmation screen before you send an email)

* `y` send 
* `a` attach file
* `p` postpone message (save to drafts)
* `e` edit message
* `t` edit "to" field (recipient)
* `f` edit "from" field (sender)
* `s` edit subject
* `c` edit "CC" field
* `b` edit "BCC" field

