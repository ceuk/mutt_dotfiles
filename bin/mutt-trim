#!/usr/bin/perl
#
# "Beautify" quoted message and make it "ready-to-reply" by Michael Velten

use utf8;

# keep quotes nested up to 3rd level
my $ind_max = 4;

my $name     = '[[:alpha:]]+([\'`-][[:alpha:]]+|[.])*';
my $fullname = '\b(' . $name . '[,]?\s+)*' . $name . '\b';

# Possible reply greetings (regexes) (note that '> ' will be prefixed)
my @greetings = (
    'Dear\s+' . $fullname . '([,.]|\s*!)?',
    '[Hh](ello|i|ey)' . '(\s+' . $fullname . ')?' . '([,.]|\s*!)?',
    'Sehr geehrter?\s+' . $fullname . '([,.]|\s*!)?',
    'Lieber?\s+' . $fullname . '([,.]|\s*!)?',
    'Guten Tag' . '(\s+' . $fullname . ')?' . '([,.]|\s*!)?',
    '[Hh]allo' . '(\s+' . $fullname . ')?' . '([,.]|\s*!)?',
    '[Mm]oin' . '(\s+' . $fullname . ')?' . '([,.]|\s*!)?',
    '[Mm]esdames(,| et) [Mm]essieurs([,.]|\s*!)?',
    'M(adame)\s+' . $fullname . '([,.]|\s*!)?',
    'M(onsieur)\s+' . $fullname . '([,.]|\s*!)?',
    '[Cc]her\s+' . $fullname . '([,.]|\s*!)?',
    '[Cc]h[eè]re\s+' . $fullname . '([,.]|\s*!)?',
    '[Bb]onjour' . '(\s+' . $fullname . ')?' . '([,.]|\s*!)?',
    '[Ss]alut' . '(\s+' . $fullname . ')?' . '([,.]|\s*!)?',
    'Senhor(ita|a)?' . '(\s+' . $fullname . ')?' . '([,.]|\s*!)?',
    'Sra?\.?' . '(\s+' . $fullname . ')?' . '([,.]|\s*!)?',
    'Car([ií]ssim)?[ao]s?' . '(\s+' . $fullname . ')?' . '([,.]|\s*!)?',
    'Prezad([ií]ssim)?[ao]s?' . '(\s+' . $fullname . ')?' . '([,.]|\s*!)?',
    'Estimad([ií]ssim)?[ao]s?' . '(\s+' . $fullname . ')?' . '([,.]|\s*!)?',
    '[Bb]om [Dd]ia' . '(\s+' . $fullname . ')?' . '([,.]|\s*!)?',
    '[Bb]oa ([Tt]arde|[Nn]oite)' . '(\s+' . $fullname . ')?' . '([,.]|\s*!)?',
    '[Oo](i|l[aá])' . '(\s+' . $fullname . ')?' . '([,.]|\s*!)?',
    '[Aa]l[ôo]' . '(\s+' . $fullname . ')?' . '([,.]|\s*!)?',
    '[Hh]ola' . '(\s+' . $fullname . ')?' . '([,.]|\s*!)?',
    'Se[nñ]or(ita|a)?' . '(\s+' . $fullname . ')?' . '([,.]|\s*!)?',
    );

# Possible reply "greetouts" (regexes) (note that '> ' will be prefixed)
my @greetouts = (
    '([Ww]ith )?(([Kk]ind|[Bb]est|[Ww]arm) )?([Rr]egards|[Ww]ishes)([,.]|\s*!)?',
    '[Bb]est([,.]|\s*!)?',
    '[Cc]heers([,.]|\s*!)?',
    '[Mm]it ([Vv]iel|[Bb]est|[Ll]ieb|[Ff]reundlich)en [Gg]r([ü]|ue)([ß]|ss)en([,.]|\s*!)?',
    '(([Vv]iel|[Bb]est|[Ll]ieb|[Ff]reundlich)e )?[Gg]r([ü]|ue)([ß]|ss)e([,.]|\s*!)?',
    '(([[Bb]est|[Ll]ieb|[Ff]reundlich)e[rn] )?[Gg]ru([ß]|ss)([,.]|\s*!)?',
    '[Mm]it (([[Bb]est|[Ll]ieb|[Ff]reundlich)em )?[Gg]ru([ß]|ss)([,.]|\s*!)?',
    '([LV]|MF)G([,.]|\s*!)?',
    '(([Tt]r[eè]s|[Bb]ien) )?([Cc]ordi|[Aa]mic)alement([,.]|\s*!)?',
    '[Aa]miti[eé]s?([,.]|\s*!)?',
    '[Aa]tenciosamente([,.]|\s*!)?',
    '[Aa]tt([,.]|\s*!)?',
    '[Aa]bra[cç]os?([,.]|\s*!)?',
    '[Aa]tentamente([,.]|\s*!)?',
    '[Cc]ordialmente([,.]|\s*!)?',
    );

my $word = '[[:alpha:]]+([\'`-][[:alpha:]]+)*';

# my $saw_greeting = 0;
# my $saw_greetout = 0;
my $saw_own_sig = 0;
my $saw_blank_line = 0;
my $inds_other_sig = 0;
my $quote_header = 0;
my $extra_pref = '';

my (@mail, @purged_mail);

my $msg = shift;
die "Usage: $0 MAIL" unless $msg;
open(MAIL, "+<:encoding(UTF-8)", $msg)  or die "$0: Can't open $msg: $!";
push(@mail, $_) while <MAIL>;   # Read whole mail

# Process whole mail
LINE:
foreach my $line (@mail) {

# Treat non-quoted lines as is
  if ($line !~ /^>/) {
    push(@purged_mail, $line);
    next LINE;
  }

# Keep all lines after my own signature unmodified
  if ($line =~ /^--\s?$/ || $saw_own_sig) {
    $saw_own_sig = 1;
    push(@purged_mail, $line);
    next LINE;
  }

  # $line =~ tr/\xA0/ /;
# tighten "> > " to ">> "
  my ($pref, $suff) = $line =~ /^([>[:space:]]+)(.*)$/;
  $pref =~ s/(>\s*(?!$))/>/g;
# reduce multiple pre- and post-blanks to one post-blank
  $pref =~ s/^\s*(>+)\s*/$1 /;
  $line = $pref . $suff . "\n";

# prepend additional '>' for each Outlook quote header
  if ($line =~ /^>+ [-_=]{3,}\s*$word(\s+$word)*\s*[-_=]{3,}$/) {
    $quote_header = 1;
    next LINE;
  }
# first line after Outlook quote header that does not start with ...:
  if ($quote_header == 1 && $line !~ /^>+ ([-*]\s*)?$word(\s+$word)*\s*:\s+/) {
    $extra_pref = '>' . $extra_pref;
    $quote_header = 0;
  }
  $pref = $extra_pref . $pref;
  $line = $pref . $suff . "\n";

# skip line if number of '>'s is greater than $ind_max
  my $inds = $pref =~ tr/>//;
  next LINE if $inds > $ind_max;

# Remove other signatures
  if ($line =~ /^>+ --\s?$/) {
    $inds_other_sig = $inds;
  }
  if ($inds == $inds_other_sig) {
    next LINE;
  } else {
    $inds_other_sig = 0;
  }

# Remove quoted greeting
  # unless ($saw_greeting) {
    foreach my $greeting (@greetings) {
      if ($line =~ /^>+ $greeting$/) {
        # $saw_greeting = 1;
        next LINE;
      }
    }
  # }

# Remove quoted "greetout"
  # unless ($saw_greetout) {
    foreach my $greetout (@greetouts) {
      if ($line =~ /^>+ $greetout$/) {
        # $saw_greetout = 1;
        next LINE;
      }
    }
  # }

# Remove quoted filler lines
  if ($line =~ /^>+ \s*(-*|_*|=*|\+*|#*|\**)$/) {
    next LINE;
  }

# Save purged line
  push(@purged_mail, $line);
}

# Overwrite original mail with purged mail
truncate(MAIL, 0);
seek(MAIL, 0, 0);
print MAIL @purged_mail;
close(MAIL);
