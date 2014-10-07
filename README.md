nexus-npm-registry-docker-image
===============================

This repository is for the build of the Nexus NPM Registry 2.10.0 that supports NPM private registry. The description of this release is documented at https://issues.sonatype.org/browse/NEXUS-5852.

Installing
==========

```
$ sudo docker run --name npm-registry-server -d -p 80:8081 -v /app/npm-volume/:/opt/sonatype-work marcellodesales/nexus-npm-registry
```

This assumes the following:

* You will be binding the port 80 to the internal port 8081.
* The host volume to save the registry's data is at /app/npm-volume

Setting up 
==========

Here are the flavors of NPM registry you need to setup:

* Public Registry Proxies: http://books.sonatype.com/nexus-book/2.10/reference/npm-proxying-registries.html
* Private Registry: http://books.sonatype.com/nexus-book/2.10/reference/npm-private-registries.html
* Grouping Registries: http://books.sonatype.com/nexus-book/2.10/reference/npm-grouping-registries.html

Client Configuration
==========

The client configuration is described at http://books.sonatype.com/nexus-book/2.10/reference/npm-configuring.html

Here's what I have so far using the default "admin:admin123" user:

```
$ cat ~/.npmrc 
_auth = YWRtaW46YWRtaW4xMjM=
init.author.name = Marcello de Sales
init.author.email = Marcello_deSales@intuit.com
init.author.url = http://about.me/marcellodesales
email = Marcello_deSales@intuit.com
registry = http://npm.corp.intuit.net/nexus/content/groups/npm-all/
```

Your private module MUST define the npm-internal URL as the "publishConfig" value on its package.json. Make sure it contains the correct entry:

```  
  "publishConfig": {
    "registry": "http://npm.corp.intuit.net/nexus/content/repositories/npm-internal/"
  }
```

Deploying
=========

The deployment process is described at http://books.sonatype.com/nexus-book/2.10/reference/npm-deploying-packages.html. 

One note is that the documentation is incorrectly showing the "admin" credentials. As discussed at https://issues.sonatype.org/browse/NEXUS-5852, the correct should be using the following command:

```
$ echo -n "admin:admin123" | openssl base64
YWRtaW46YWRtaW4xMjM=
```

Also, try using the "npm loging" after you configured the npm registry using https://www.npmjs.org/doc/misc/npm-config.html#registry or updating your ~/.npmrc with the entry...

Behind Proxy
=======

The server requires outbound HTTP connection to the registries such as nodejs and nodejitsu. If you happen to be behind an http proxy, set it up using the http proxy settings as described at http://stackoverflow.com/a/12427692/433814.

TODO
========

* This version runs as "root". It needs to be under the user "nexus".
