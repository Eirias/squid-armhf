# Squid Docker container
Squid in a docker container.  This container is based on Alpine to keep the image size as small as possible.  Out of the box squid is configured to proxy/cache HTTP/S traffic.  If you just need a simple proxy then you only need to add a user who is allowed to use the proxy, but most real world situations will require some more modifications to the out of the box configuration.

## Add a user 
//If necessary run ```sudo apt-get install apache2-utils``` afterwards you can use
```
htpasswd -c /conf/passwords FIRST_USR #create file
htpasswd /conf/passwords SECOND_USER  #append users
```

## Running the container
To run squid with the out of the box confiruation 

```
docker run -d -p 3128:3128 sk278/squid-armhf
```


### Custom squid configuraiton

```
docker run \
-p 3121:3128 \
-v /my/custom/squid.conf:/etc/squid/squid.conf:ro,Z \
-d sk278/squid-armhf
```

### Caching to disk
The default configruation specifies no disk cache and thus is in-memory only.  If you want to allow disk caching you must pass in a custom configuration file that specifies the cache.  It is recommened you also pass in a volume for persistance.

```
docker run  \
-p 3128:3128 \
-v /my/custom/squid.conf:/etc/squid/squid.conf:ro,Z \
-v /data/cache:/var/cache/squid:Z \
-d sk278/squid-armhf
```


## Logging
The default configruation currently logs inside the container to `/var/log/squid/`.  The default configuration is setup to only log failed attempts.  Successfull attempts are *not* logged with the default config. The following log files are configured by default: `/var/log/access.log`, `/var/log/cache.log`.  The goal is to redirect the access and cache log to stdout and stderr [Issue 5](https://github.com/scbunn/docker-squid/issues/5).

## Default Configuration
The default configuration supports basic HTTP/S.  There are no access controls in place to prevent unwanted hosts from using this proxy.  Any host that is able to connect will be accepted.  The default config will proxy any request to port `80` or port `443`.
