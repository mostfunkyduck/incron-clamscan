# What it does
This will set up incron to run clamscan on every download to /home/jack/Downloads. The script badgers you with notify-send too.

# What it is
a script to put in /usr/local/sbin/incron-clamscan and an incron-table to use with `incrontab -e`

# How to use it

## Dependencies
* notify-send (libnotify, i use the version in brew)
* incron
** this was done so that it would be executed as the current user, avoiding the use of root perms, so that user needs to be added to the incron group `sudo adduser $TheUser incron`
* clamav and clamav-freshclam

## Deploying

* `incron-clamscan` needs to be copied or linked to `/usr/local/sbin/incron-clamscan`
* `incron-clamscan` currently assumes that it will move all contaminated files to `$HOME/toxic`
* `incron-table` needs to be updated so that each directory you want to monitor is mentioned. Deploy as current user with `incrontab incron-table`
* `/var/log/incron-clamscan` needs to exist and be accessible by the current user

# Interesting Choices

* The scan script will ignore things ending in `.part` because this is how the browser marks partial downloads. If you don't do this, then the browser can create tons of partial downloads that will spin up bunches of clamscans and waste a lot of time and resources.
* The incrontab is *unescaping* the file name before it passes it in to the script. In short, this is because incrontab doesn't escape some characters (at least semicolons, i haven't fully explored the boundaries though). To make this minimally annoying, I just do away with the need for escaping.

# How to test

* Drop an EICAR in a monitored directory and observe that it gets uprooted and moved to the quarantine direcotry (currently `$HOME/toxic`

# How to debug

* Check journalctl for incron issues
* Check `/var/log/incron-clamscan/incron-clamscan.log` for script/clamscan issues
* This is very "works on my machine" code, so dependency issues may abound.
* If notify-send is misbehaving, there may be an issue with the DISPLAY variable in the script
