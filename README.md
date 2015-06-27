# Minimizing the size of standard docker images

This script creates a new minimal docker image which contains only selected packages and files from a source
Docker image.

Why is this useful?
1. It minimizes the size of your docker images, which speeds up load times
2. It hardens the image, as all other packages are removed from the image


## Example
The following example strips the nginx installation from the default NGiNX docker image,

```
        create-stripped-image -i nginx -t stripped-nginx  \
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
			-f /var/log/nginx 
```

docker run -d stripped-ngnix  /usr/sbin/nginx -g "deamon: off;"
