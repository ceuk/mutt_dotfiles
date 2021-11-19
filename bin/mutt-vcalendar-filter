#!/usr/bin/perl

# vcalendar-filter is a simple filter to give plain text representations of vcards
# Copyright (C) 2008  Martyn Smith
#
# This program is free software: you can redistribute it and/or modify
# it under the terms of the GNU General Public License as published by
# the Free Software Foundation, either version 3 of the License, or
# (at your option) any later version.
#
# This program is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
# GNU General Public License for more details.
#
# You should have received a copy of the GNU General Public License
# along with this program.  If not, see <http://www.gnu.org/licenses/>.

# This script takes a simple VCALENDAR file as input on STDIN and produces a
# human readable text/plain representation of it on STDOUT
#
# It has been designed for use with mutt's auto_view config option, see the
# README file for more details

use strict;
use warnings;

use Data::ICal;
use Text::Autoformat;

my $body = eval { local $/ = undef; <> };
my $calendar = Data::ICal->new(data => $body);

# If parsing failed, try parsing as version 1.0
$calendar = Data::ICal->new(data => $body, vcal10 => 1) unless $calendar;

# If parsing failed, give up :-(
unless ( $calendar ) {
    print "Unable to parse vcalendar: ", $calendar->error_message, "\n";
    print $body;
    exit 1;
}

foreach my $entry ( @{$calendar->{entries}} ) {
    my $properties;

    foreach my $property ( keys %{$entry->properties} ) {
        next unless defined $entry->property($property);
        $properties->{$property} = join(', ', map { $_->decoded_value } @{$entry->property($property)});
        if ( $property eq 'description' ) {
            $properties->{$property} = "$properties->{$property}";

            $properties->{$property} = autoformat $properties->{$property}, {
                all => 1,
                left => 15,
            };
            $properties->{$property} =~ s/^\s*// if defined $properties->{$property};
        }
        elsif ( $property =~ m{ \A dt (?: start | end ) \z }xms ) {
            if ( $properties->{$property} =~ m{ (\d\d\d\d)(\d\d)(\d\d)T(\d\d)(\d\d)(\d\d) }xms ) {
                $properties->{$property} = "$1-$2-$3 $4:$5";
            }
        }
    }

    if ( $entry->ical_entry_type eq 'VTIMEZONE' ) {
        unless ( defined $properties->{tzid} and $properties->{tzid} =~ m{Pacific/Auckland} ) {
            print "Timezone    : ", $properties->{tzid}, "\n";
            print "\n";
        }
    }
    elsif ( $entry->ical_entry_type eq 'VEVENT' ) {
        print '-' x 72, "\n";
        foreach my $key ( qw(summary BR description BR location organizer dtstart dtend) ) {
            if ( $key eq 'BR' ) {
                print "\n";
                next;
            }
            next unless defined $properties->{$key};
            printf "%-12s: %s\n", ucfirst $key, $properties->{$key};
        }
    }
    else {
        print "WARNING: Unknown entry type: ", $entry->ical_entry_type, "\n";
    }
}
