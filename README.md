# Minimizing the size of standard docker images

This is a utility to minimize the size of official docker images, so that it only contains the 
bare necessities.

Why is this useful?
1. It minimizes the size of your docker images, which speeds up load times
2. It minimizes the attack surface, if you get in the container, there is nothing there..

## Example
The following example strips the nginx installation from the default NGiNX docker image,

```
create-stripped-image -i nginx -t stripped-nginx  \
	       -x 80 \
	       -p nginx  \
	       -f /etc/passwd \
	       -f /etc/group \
	       -f '/lib/*/libnss*' \
	       -f /bin/ls \
	       -f /bin/cat \
	       -f /bin/sh \
	       -f /bin/mkdir \
	       -f /bin/ps \
	       -f /var/run \
	       -f /var/log/nginx \
	       -f /var/cache/nginx
```
Aside from the nginx package, I have added the files /etc/passwd, /etc/group and /lib/*/libnss* shared libraries 
are necessary for getpwnam() to work correctly.

The directories /var/run, /var/log/nginx and /var/cache/nginx are required for NGiNX to operate.

In addition, I added the /bin/sh and a few handy utilities, just to be able to snoop around a little bit..


The stripped image has now shrunk to an incredible 5.4% of the original 132.8 Mb to just 7.3Mb and is still fully operational!

```
$ docker images | grep nginx
stripped-nginx                     latest              d61912afaf16        21 seconds ago      7.297 MB
nginx                              1                   319d2015d149        12 days ago         132.8 MB
nginx                              1.9                 319d2015d149        12 days ago         132.8 MB
nginx                              1.9.2               319d2015d149        12 days ago         132.8 MB
```

Just run the nginx container as you normally would!

```
docker run --name nginx -P -d --entrypoint /usr/sbin/nginx stripped-nginx  -g "daemon off;"
docker run --link nginx:stripped cargonauts/toolbox-networking curl -s -D - http://stripped
```


