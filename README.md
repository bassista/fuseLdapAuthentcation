# Jboss Fuse Ldap authentication lab
 > **IMPORTANT:** Before launching the following script you need to build the fuse image yourself, see [# Build the Fuse image](#build-the-fuse-image)

`sh fuseLdap.sh`

This is a simple script that run for you 3 docker images:
- OpenLdap with preloaded users/groups data: valdar/ldapfuseusers:1.0.0
  - address: `localhost:389` admin dn: `cn=admin,dc=example,dc=org` admin password: `admin`
- PhpLdapAdmin (just to have a convenient way to visualize/modifiy ldap contents): osixia/phpldapadmin:0.6.9
  - address: `https://localhost` admin dn: `cn=admin,dc=example,dc=org` admin password: `admin`
- Jbosse fuse (**you need to build this image yourself, see [# Build the Fuse image](#build-the-fuse-image)**)

After that it creates a fabric and update the configuration to authenticate using the openldap server. In this way you will be able to log in in to karaf console or hawtio using credentials stored in openldap:
- user: `fuseldap` password: `fuseldap` group: `admin`
- user: `notfuseldap` password: `notfuseldap` group: `none`

## Interacting with the Fuse container
When the script finish you should be able to check fuse container's local ports with:
```
$ docker ps
CONTAINER ID        IMAGE                        COMMAND                CREATED             STATUS              PORTS                                                                                                                                                  NAMES
9e996ab8e080        fuse6.3:latest               "/bin/sh -c 'service   About an hour ago   Up About an hour    0.0.0.0:49153->44444/tcp, 0.0.0.0:49154->61616/tcp, 0.0.0.0:49155->8101/tcp, 0.0.0.0:49156->8181/tcp, 0.0.0.0:49157->1099/tcp, 0.0.0.0:49158->22/tcp   root
398aa9b12fc8        osixia/phpldapadmin:0.5.0    "/sbin/my_init"        About an hour ago   Up About an hour    80/tcp, 0.0.0.0:443->443/tcp                                                                                                                           phpldapadmin
38b8e0885dbf        valdar/ldapfuseusers:1.0.0   "/sbin/my_init"        About an hour ago   Up About an hour    0.0.0.0:389->389/tcp                                                                                                                                   openldap
```
in this example the hawtio console would be at `http://localhost:49156`, activeMQ at `localhost:49154`, karaf console at `localhost:49155` and ssh into the container at `localhost:49158`.

Should now be possible to login in to _hawtio_ or the _karaf console_ using the users `fuseldap` with password `fuseldap` (coming from ldap) and the default user `admin` password `admin` (coming from the ZookeeperLoginMoule); should not be possible to login with user `notfuseldap` and password `notfuseldap` (although this one is present in ldap, dose not belong to the group admin so is lacking the permissions to login to fuse).

## Build the Fuse image
Before launching the script you need to build fuse6.3 image yourself by downloading JBoss Fuse distribution from

http://www.jboss.org/products/fuse

The build process will extract in the Docker image all the zip files it will find in your working folder. If it finds more than a file it will put all of them inside the  Docker it's going to be created. Most of the time you will want to have just a single zip file.

    # download docker file
	wget https://raw.github.com/paoloantinori/dockerfiles/master/centos/fuse/fuse/Dockerfile

    # check if base image has been updated
	docker pull pantinor/fuse

    # build your docker fuse image. you are expected to have either a copy of jboss-fuse-karaf-6.3.x.redhat-y.zip or a link to that file in the current folder.
    docker build --rm -t fuse6.3 .

more infoan be found at https://github.com/paoloantinori/dockerfiles/tree/master/centos/fuse

# Instructions for Fuse 6.2.1

Instructions and code for this same lab targeting Fuse 6.2.1 has been moved to his own branch: https://github.com/valdar/fuseLdapAuthentcation/tree/fuse-6.2.1
