---
date: '2017-02-14 19:15 +0100'
published: true
title: 'Automatic Deployment with Git to a VPS '
category:
  - Development
---
[Create a repo](/2017/git-server/) on your VPS.

Git repositories have a folder called 'hooks'. This folder contains some sample files for possible actions that you can hook and perform custom actions set by you.

Git documentation define three possible server hooks: 'pre-receive', 'post-receive' and 'update'. 

'Pre-receive' is executed as soon as the server receives a 'push', 'update' is similar but it executes once for each branch, and 'post-receive' is executed when a 'push' is completely finished and it's the one we are interested in.

 In your repository: 
 
 ```
 cd hooks
 ```

create the file 'post-receive':

```
cat > post-receive
```

When you execute this command, you will have a blank line indicating that everything you type will be saved to this file:

```
#!/bin/sh
GIT_WORK_TREE=/home/path/to/your/www
export GIT_WORK_TREE
git checkout -f
```

set the proper permissions using:

```
chmod +x post-receive
```

---

In order for this to work with changes to your server you need to run your server with Node-Supervisor.

```
sudo npm install supervisor -g
```

```
supervisor /home/path/to/your/www/server.js
```
