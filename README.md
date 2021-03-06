# busybox-uptime-check

Simple and resilient busybox based system monitoring, intended for use with various remote uptime check services.

# Usage

 - Configure nginx to serve selected directory (see *contrib/nginx*)
 - Configure *up-check* to be started from cron (see *contrib/cron*)
 - Configure variables in *up-check.conf" file residing in same directory as the script itself (always use EXPORT)
 - Add/Modify scripts in *up-check.d* to your needs
 - Configure your remote uptime check service to periodically fetch your url (*https://DOMAIN/DIR/check*) and also look for **ALL OK** string

#Features

 - Stupid simple (KISS principle)
 - Pure (busybox) shell
 - Low resources usage during system check and webcheck
 - Resilient to almost any failure (hopefully)
 - No extra running deamons
 - No extra dependencies

# How it works

**up-check** script produces for **every hour** new file in output directory, with date, OK/FAIL status for every check script from *up-check.d*
along with optional error message in case of failure. Final **ALL OK** will be added if every check returned OK.
Webserver is configured to serve time based **current** file (if it can find one).

# Possible failures and their handling

The ones i've thinked of:

 - Some check fails => no 'ALL OK' in check http response => *immediate remote ALERT*
 - Web server not working => connection error => *immediate remote ALERT*
 - Cron service not working => no new file produced => *delayed remote ALERT within next 60 minutes*
 - Disk full => no new file produced => *immediate or delayed remote ALERT*
 - Disk goes sudenly read-only => no new file produced => *delayed remote ALERT*

# Why using busybox

Busybox has been chosen, because it provides simple and powerfull shell scripting capatibilities, with great flexibility,
while maintaining low resource usage compared to traditional shell (i.e. bash).

# How to write check script

 - Check script verifies whatever conditions it should check
 - If check fails, script should exit with *failure* (i.e. via exit 1) and it should also produce simple descriptive error message
 - Otherwise no message should be produced and script should exit with 0 (SUCCESS)

However, this is only guideline - you can script your checks as you want/need.

