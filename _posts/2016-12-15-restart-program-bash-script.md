---
date: '2016-12-15 15:55 +0100'
published: true
title: Restart Program Bash Script
category:
  - Linux
---

```bash
vim launch.sh
```

```sh
#!/bin/sh

ps auxw | grep icecast2 | grep -v grep > /dev/null

if [ $? != 0 ]
then
        /etc/init.d/icecast2 start > /dev/null
fi
```

```bash
chmod 0755 launch.sh
```

*The cron utility allows us to schedule at what intervals the script should execute.*

```bash
crontab -e
```

*The most often that the script can run in cron is every minute.*

```
* * * * * {path}/launch.sh
```

**Every five minutes would be set up like this:**

```
*/5 * * * * {path}/launch.sh
```
