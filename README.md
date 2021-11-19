# Mutt dotfiles

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

## Contents

* `./neomutt.desktop` - a desktop file for opening mutt via the GUI inside the Kitty terminal. Stick it in /usr/share/applications
* `bin/import_calendar_invite` - imports a calendar invite into google calendar with gcalcli and then calls mutt-ical to respond to the invite
* `bin/ldap_owa_query` - allows contact searching via LDAP (used for coroprate office365/outlook accounts)
* `bin/mailsync` -  a great script that syncs your mail, originally written by Luke Smith
* `bin/mutt-ical` - used to respond to email calendar invites
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

## Setup

* Install any dependencies you need based on your use cases from the above list
* Move the stuff in .config into your existing .config directoy inside your home folder
* Move the stuff you need from the bin folder into /usr/local/bin (don't forget to `chmod +x` them)
* Go through each config file and customize it - there are comments/instructions in each file
* Enable any systemd services you need (or use something like cron)
