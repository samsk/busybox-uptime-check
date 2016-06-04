# busybox-uptime-check

Simple and resilient busybox based system monitoring, intended for use with various remote uptime check services.

# Usage

	- Configure nginx to serve selected directory (see *contrib/nginx*)
	- Configure *up-check* to be started from cron (see *contrib/cron*)
	- Add/Modify scripts in *up-check.d* to your needs
	- Configure your remote uptime check service to periodically fetch your url and also look for **ALL OK** string

#Features

	- Stupid simple (KISS principle)
	- Pure (busybox) shell
	- Low resources usage during system check and webcheck
	- Resilient to almost any failure (hopefully)
	- No extra running deamons
	- No extra dependencies

# How it works

*up-check* produces for **every hour** new file in output directory, with date, OK/FAIL status for every check script from *up-check.d*
along with optional error message in case of failure. Final **ALL OK** will be added if every check returned OK.

# Possible failures and their handling

The one i've thinked off:

	- Some check fails => no 'ALL OK' in check http response => *immediate remote ALERT*
	- Web server not working => *immediate remote ALERT*
	- Cron service not working => *delayed remote ALERT within next 60 minutes*
	- Disk full => *immediate or delayed remote ALERT*
	- Disk read-only => *delayed remote ALERT*

# Why using busybox

Busybox has been chosen because it provides simple and powerfull shell scripting capatibilities,
while maintaining low resource usage compared to traditional shell (i.e. bash).

# How to write check script

	- If check fails, script should exit with *failure* (i.e. via exit 1) and it should also produce simple error message
	- Otherwise no message should be produced and script should exit with 0 (SUCCESS)

However, this is only guideline - you can script your checks as you want/need.

