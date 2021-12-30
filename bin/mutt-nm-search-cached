#!/bin/bash
# usage: mutt-nm-search-cached ACCOUNT_ALIAS

notmuch --config ~/.config/notmuch/notmuchrc search --output=messages "$(cat ~/.cache/mutt_terms) path:$1/**" | head -n 1000 | perl -le '@a=<>;chomp@a;s/\id:// for@a;$,="|";print@a'
