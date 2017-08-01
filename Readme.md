# Docker materials repository

This repository contains public materials for my experiments on Docker. I write a blog about these items so please visit www.re-enter.fr for further information on what these folders contain.

# Disclaimer

The configuration files and code provided here are not production ready and you can reuse them at your own risk to speed up your project kick off. I should not be liable of any issue caused by a missuse of these materials.

#JBOSS AS7 container#

##Build##

To build this container, you need to download the following installers in the **installers** directory:

* JBOSS EAP 6.1.0 installer
* Oracle JDK 7
* mysql JDBC driver (any supported 5 version of your choice)

# Building the container

```bash
docker build -t logimethods/jboss-eap-6.1.0 .
docker build -t logimethods/jboss-eap-6.1.0_config -f Dockerfile_Config .
```

# running the container

## Database

```xml
<xa-datasource-property name="ServerName">database</xa-datasource-property>
<xa-datasource-property name="DatabaseName">ATG_CORE</xa-datasource-property>
<security>
  <user-name>atg</user-name>
  <password>atg</password>
```

```bash
docker run -it --rm --name database -e MYSQL_ROOT_PASSWORD=your-secret-pw -e MYSQL_DATABASE=ATG_CORE -e MYSQL_USER=atg -e MYSQL_PASSWORD=atg mysql:5.6
```

## External Configuration Files

### SLM

```bash
docker run -it --rm -p 8080:8080 -p 9990:9990 -p 9012:9012 -h atg_front_slm --name atg_front_slm -v `pwd`/runtime/atg/config:/srv/jboss/config -v `pwd`/runtime/atg/ear/slm:/opt/jboss-eap-6.1/standalone/deployments -v `pwd`/runtime/atg/log/slm:/var/log/jboss --link database:database  logimethods/jboss-eap-6.1.0 /opt/scripts/jboss.sh start
```

### Other instances

```bash
docker run -it --rm -p 8180:8080 --name atg_front1 -h atg_front1 -v `pwd`/runtime/atg/config:/srv/jboss/config -v `pwd`/runtime/atg/ear/front1:/opt/jboss-eap-6.1/standalone/deployments -v `pwd`/runtime/atg/log/front1:/var/log/jboss --link database:database --link atg_front_slm:atg_front_slm logimethods/jboss-eap-6.1.0 /opt/scripts/jboss.sh start
```

```bash
docker run -it --rm -p 8280:8080 --name atg_front2 -h atg_front2 -v `pwd`/runtime/atg/config:/srv/jboss/config -v `pwd`/runtime/atg/ear/front2:/opt/jboss-eap-6.1/standalone/deployments -v `pwd`/runtime/atg/log/front2:/var/log/jboss --link database:database --link atg_front_slm:atg_front_slm logimethods/jboss-eap-6.1.0 /opt/scripts/jboss.sh start
```

## Embedded Configuration Files

### SLM

```bash
docker run -it --rm -p 8080:8080 -p 9990:9990 -p 9012:9012 --name atg_front_slm logimethods/jboss-eap-6.1.0_config
```

### Other instances

```bash
docker run -it --rm -p 8180:8080 --name atg_front1 logimethods/jboss-eap-6.1.0_config
```

```bash
docker run -it --rm -p 8280:8080 --name atg_front2 logimethods/jboss-eap-6.1.0_config
```