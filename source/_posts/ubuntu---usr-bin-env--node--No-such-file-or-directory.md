---
title: "ubuntu  /usr/bin/env: node: No such file or directory"
categories: [ "JS" ]
tags: [ "nodejs","ubuntu" ]
draft: false
slug: "usrbinenv-node-no-such-file-or-directory-ubuntu"
date: "2014-10-25 12:34:00"
---

I have installed nodejs using:

    apt-get install nodejs

Then i have installed npm using:

    apt-get install npm

And then i have installed forever using:

    npm install forever -g

Now i go to my project /var/www/myproject

and attempt to run forever start server.js


<!--more-->


then i get the following message:

/usr/bin/env: node: No such file or directory

Can anyone tell me whats going on?


down vote
accepted
	

You need to symlink the nodejs executable to node

    sudo ln -s "$(which nodejs)" /usr/bin/node

The reason for this is that when you do "apt-get install node", it installs an unrelated package, so they had to choose a different name so it wouldn't conflict

[http://stackoverflow.com/questions/30281057/node-forever-usr-bin-env-node-no-such-file-or-director](http://stackoverflow.com/questions/30281057/node-forever-usr-bin-env-node-no-such-file-or-director)
[https://www.digitalocean.com/community/questions/ubuntu-repo-nodejs-require-symlink-to-node-to-use-forever](https://www.digitalocean.com/community/questions/ubuntu-repo-nodejs-require-symlink-to-node-to-use-forever)