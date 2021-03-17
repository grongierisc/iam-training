# 1. InterSystems API Manager Training

This repository contains the materials, examples, excercices to learn the basic concepts of IAM.

- [1. InterSystems API Manager Training](#1-intersystems-api-manager-training)
- [2. Introduction](#2-introduction)
  - [2.1. What is IAM ?](#21-what-is-iam-)
  - [2.2. What is an API Managment ?](#22-what-is-an-api-managment-)
  - [2.3. IAM Portal](#23-iam-portal)
  - [2.4. Flow of this training](#24-flow-of-this-training)
- [3. Installation](#3-installation)
  - [3.1. What do you need to install?](#31-what-do-you-need-to-install)
  - [3.2. How IAM work's with IRIS](#32-how-iam-works-with-iris)
  - [3.3. Setup](#33-setup)
  - [3.4. Install IAM](#34-install-iam)
    - [3.4.1. Iris Image](#341-iris-image)
    - [3.4.2. IAM Image](#342-iam-image)
    - [3.4.3. Update the docker file](#343-update-the-docker-file)
    - [3.4.4. Update the docker-compose](#344-update-the-docker-compose)
    - [3.4.5. Option :](#345-option-)
    - [3.4.6. Test it !](#346-test-it-)
- [4. First Service/Route](#4-first-serviceroute)
  - [4.1. Create a service](#41-create-a-service)
  - [4.2. Create a route](#42-create-a-route)
  - [4.3. Test it !](#43-test-it-)
- [5. Second, Go futher with plugin](#5-second-go-futher-with-plugin)
  - [5.1. Add a plugin to the service](#51-add-a-plugin-to-the-service)
  - [5.2. Test it !](#52-test-it-)
- [6. Third, add our own authentication](#6-third-add-our-own-authentication)
  - [6.1. Add consumers](#61-add-consumers)
  - [6.2. Add Basic auth plugin](#62-add-basic-auth-plugin)
  - [6.3. Add ACL Plugin](#63-add-acl-plugin)
  - [6.4. Configure USER with ACL and credentials](#64-configure-user-with-acl-and-credentials)
  - [6.5. Test it !](#65-test-it-)
- [7. Exercice, Rate-Limiting](#7-exercice-rate-limiting)
  - [7.1. Correction](#71-correction)
- [Dev Portal](#dev-portal)
  - [Overview](#overview)
  - [Enable it !](#enable-it-)
  - [Add your first spec](#add-your-first-spec)

# 2. Introduction

## 2.1. What is IAM ? 

IAM stand for InterSystems API Manager, it's based on **Kong Entreprise Edition**.

This mean you have acces on top of Kong OpenSource edition to :
* Manager Protal
* Developer Portal 
* Advance plugin
  * Oauth2
  * Caching
  * ...

![alt](https://raw.githubusercontent.com/grongierisc/iam-training/training/misc/img/KongEEvsOSS.png)

## 2.2. What is an API Managment ?

API management is the process of creating and publishing web application programming interfaces (APIs), enforcing their usage policies, controlling access, nurturing the subscriber community, collecting and analyzing usage statistics, and reporting on performance. API Management components provide mechanisms and tools to support developer and subscriber community.

![alt](https://raw.githubusercontent.com/grongierisc/iam-training/training/misc/img/Api_Managment.png)

## 2.3. IAM Portal

Kong and IAM are design as API first, this mean, every thing done in Kong/IAM can be done by rest calls or the manager portal.

During this training all example / exercice will present both this way :

<table>
<tr>
<th> IAM Portal </th>
<th> Rest API </th>
</tr>
<tr>
<td>

![alt](https://raw.githubusercontent.com/grongierisc/iam-training/training/misc/img/default_kong.png)

</td>
<td>

![alt](https://raw.githubusercontent.com/grongierisc/iam-training/training/misc/img/rest_call.png)

</td>
</tr>
</table>

## 2.4. Flow of this training

The aim of this training is to use IAM as a proxy of an IRIS rest API.

Definition of this rest API can be found here : 

```url
http://localhost:52773/swagger-ui/index.html#/
```
or here
```url
https://github.com/grongierisc/iam-training/blob/training/misc/spec.yml
```

Start this training with the main branch.

At the end of the training you should have the same result as the training branch.
# 3. Installation
## 3.1. What do you need to install? 
* [Git](https://git-scm.com/downloads) 
* [Docker](https://www.docker.com/products/docker-desktop) (if you are using Windows, make sure you set your Docker installation to use "Linux containers").
* [Docker Compose](https://docs.docker.com/compose/install/)
* [Visual Studio Code](https://code.visualstudio.com/download) + [InterSystems ObjectScript VSCode Extension](https://marketplace.visualstudio.com/items?itemName=daimor.vscode-objectscript)
* InterSystems IRIS IAM enabled license file.
* IAM Docker image

## 3.2. How IAM work's with IRIS

At Kong/IAM start, the container check for the Kong/IAM license with a curl call.

The endpoint of this call is a rest API on the IRIS container.

FYI : Kong license is embedded in IRIS one.

![alt](https://raw.githubusercontent.com/grongierisc/iam-training/training/misc/img/IAM_IRIS.png)
## 3.3. Setup

Git clone this repository.

```sh
git clone https://github.com/grongierisc/iam-training
```

Run the initial rest API :

```sh
docker-compose up
```

Test it :

```url
http://localhost:52773/swagger-ui/index.html#/
```

Login/Password :
SuperUser/SYS

## 3.4. Install IAM

### 3.4.1. Iris Image

First you need to swith for the community edition to an licensed one.

To do so, you need to setup your access to InterSystems Container Registry to download IRIS limited access images.

Have a look at this [Introducing InterSystems Container Registry](https://community.intersystems.com/post/introducing-intersystems-container-registry) on [Developer Community](https://community.intersystems.com).

* Log-in into https://containers.intersystems.com/ using your WRC credentials and get a *token*.
* Set up docker login in your computer:
```bash
docker login -u="user" -p="token" containers.intersystems.com
```
* Get InterSystems IRIS image:
```bash
docker pull containers.intersystems.com/intersystems/irishealth:2020.4.0.524.0
```

### 3.4.2. IAM Image
In [WRC Software Distribution](https://wrc.intersystems.com/wrc/coDistribution.csp):
* Components > Download *IAM-1.5.0.9-4.tar.gz* file, unzip & untar and then load the image:
```bash
docker load -i iam_image.tar
```

### 3.4.3. Update the docker file

Change IRIS community edition to a licensed one.
* containers.intersystems.com/intersystems/irishealth:2020.4.0.524.0
* add iris.key in key folder

Edit the dockerfile to add on top of it this part
```dockerfile
ARG IMAGE=containers.intersystems.com/intersystems/irishealth:2020.4.0.524.0
# Frist stage
FROM $IMAGE as iris-iam
COPY key/iris.key /usr/irissys/mgr/iris.key
COPY iris-iam.script /tmp/iris-iam.script
RUN iris start IRIS \
&& iris session IRIS < /tmp/iris-iam.script \
&& iris stop IRIS quietly

# Second stage
FROM iris-iam
```

This part will create a multi-stage dockerfile.

* the first stage is to enable IRIS to serve IAM license.
* the second stage is for the REST API build

Create a new file iris-iam.script to build a new IRIS Image to enable IAM endpoint and user.

```objectscript
zn "%SYS"
write "Create web application ...",!
set webName = "/api/iam"
set webProperties("Enabled") = 1
set status = ##class(Security.Applications).Modify(webName, .webProperties)
write:'status $system.Status.DisplayError(status)
write "Web application "_webName_" was updated!",!

set userProperties("Enabled") = 1
set userName = "IAM"
Do ##class(Security.Users).Modify(userName,.userProperties)
write "User "_userName_" was updated!",!
halt
```

### 3.4.4. Update the docker-compose

Update the docker-compose file to :

* db
  * postgres database for IAM
* iam-migration
  * bootstrap the database
* iam
  * actual IAM instance
* a volume for data persistent 

Add this part to the end of the docker-compose file.

```yml
  iam-migrations:
    image: intersystems/iam:1.5.0.9-4
    command: kong migrations bootstrap up
    depends_on:
      - db
    environment:
      KONG_DATABASE: postgres
      KONG_PG_DATABASE: ${KONG_PG_DATABASE:-iam}
      KONG_PG_HOST: db
      KONG_PG_PASSWORD: ${KONG_PG_PASSWORD:-iam}
      KONG_PG_USER: ${KONG_PG_USER:-iam}
      KONG_CASSANDRA_CONTACT_POINTS: db
      KONG_PLUGINS: bundled,jwt-crafter
      ISC_IRIS_URL: IAM:${IRIS_PASSWORD}@iris:52773/api/iam/license
    restart: on-failure
    links:
      - db:db
  iam:
    image: intersystems/iam:1.5.0.9-4
    depends_on:
      - db
    environment:
      KONG_ADMIN_ACCESS_LOG: /dev/stdout
      KONG_ADMIN_ERROR_LOG: /dev/stderr
      KONG_ADMIN_LISTEN: '0.0.0.0:8001'
      KONG_ANONYMOUS_REPORTS: 'off'
      KONG_CASSANDRA_CONTACT_POINTS: db
      KONG_DATABASE: postgres
      KONG_PG_DATABASE: ${KONG_PG_DATABASE:-iam}
      KONG_PG_HOST: db
      KONG_PG_PASSWORD: ${KONG_PG_PASSWORD:-iam}
      KONG_PG_USER: ${KONG_PG_USER:-iam}
      KONG_PROXY_ACCESS_LOG: /dev/stdout
      KONG_PROXY_ERROR_LOG: /dev/stderr
      KONG_PORTAL: 'on'
      KONG_PORTAL_GUI_PROTOCOL: http
      KONG_PORTAL_GUI_HOST: '127.0.0.1:8003'
      KONG_ADMIN_GUI_URL: http://localhost:8002
      KONG_PLUGINS: bundled
      ISC_IRIS_URL: IAM:${IRIS_PASSWORD}@iris:52773/api/iam/license
    volumes: 
      - ./iam:/iam
    links:
      - db:db
    ports:
      - target: 8000
        published: 8000
        protocol: tcp
      - target: 8001
        published: 8001
        protocol: tcp
      - target: 8002
        published: 8002
        protocol: tcp
      - target: 8003
        published: 8003
        protocol: tcp
      - target: 8004
        published: 8004
        protocol: tcp
      - target: 8443
        published: 8443
        protocol: tcp
      - target: 8444
        published: 8444
        protocol: tcp
      - target: 8445
        published: 8445
        protocol: tcp
    restart: on-failure
  db:
    image: postgres:9.6
    environment:
      POSTGRES_DB: ${KONG_PG_DATABASE:-iam}
      POSTGRES_PASSWORD: ${KONG_PG_PASSWORD:-iam}
      POSTGRES_USER: ${KONG_PG_USER:-iam}
    volumes:
      - 'pgdata:/var/lib/postgresql/data'
    healthcheck:
      test: ["CMD", "pg_isready", "-U", "${KONG_PG_USER:-iam}"]
      interval: 30s
      timeout: 30s
      retries: 3
    restart: on-failure
    stdin_open: true
    tty: true
volumes:
  pgdata:
```

Add the .env file in root folder :

```env
IRIS_PASSWORD=SYS
```

### 3.4.5. Option :

For ease of use (and may be security), you can use the .env file in the IRIS dockerfile.

To do so, edit the docker-compose with this in the iris service part :

```yml
    build: 
      context: .
      dockerfile: dockerfile
      args: 
        - IRIS_PASSWORD=${IRIS_PASSWORD}
```

And the dockerfile (second or first stage of the build):

```dockerfile
ARG IRIS_PASSWORD
RUN echo "${IRIS_PASSWORD}" > /tmp/password.txt && /usr/irissys/dev/Container/changePassword.sh /tmp/password.txt
```

### 3.4.6. Test it !

```sh
docker-compose -f "docker-compose.yml" up -d --build
```

# 4. First Service/Route

Remember how Kong/IAM works ?

![alt](https://raw.githubusercontent.com/grongierisc/iam-training/training/misc/img/KongEEvsOSS.png)

Here, we will build :
* a service
  * for our crud API
* a route
  * to acccess this service

## 4.1. Create a service

<table>
<tr>
<th> IAM Portal </th>
<th> Rest API </th>
</tr>
<tr>
<td>

![alt](https://raw.githubusercontent.com/grongierisc/iam-training/training/misc/img/create_service.png)

</td>
<td>

```sh
# Create service

curl -i -X POST \
--url http://localhost:8001/services/ \
--data 'name=crud' \
--data 'url=http://iris:52773/crud/'
```

</td>
</tr>
</table>

What do we see here, to create a service we simply need it's url.

## 4.2. Create a route

<table>
<tr>
<th> IAM Portal </th>
<th> Rest API </th>
</tr>
<tr>
<td>

![alt](https://raw.githubusercontent.com/grongierisc/iam-training/training/misc/img/create_route.png)

</td>
<td>

```sh
# Create route

curl -i -X POST \
--url http://localhost:8001/services/crud/routes \
--data 'name=crud-route' \
--data 'paths=/persons/*' \
--data 'strip_path=false'
```

</td>
</tr>
</table>

What do we see here, to create a route we need :
* it's service name
* a path where RegEx is allowed 

## 4.3. Test it !

<table>
<tr>
<th> Original API </th>
<th> Proxy API </th>
</tr>
<tr>
<td>

```sh
# Legacy


curl –i --location --request GET 'http://localhost:52773/crud/persons/all' \
--header 'Authorization: Basic U3VwZXJVc2VyOlNZUw=='

```

</td>
<td>

```sh
# KONG


curl –i --location --request GET 'http://localhost:8000/persons/all' \
--header 'Authorization: Basic U3VwZXJVc2VyOlNZUw=='

```

</td>
</tr>
</table>

What do we see here :
* Nothing new on legacy side.
* On kong side :
  * We change the port
  * The path correspond to the route
  * We still need to authenticate

# 5. Second, Go futher with plugin

To go futher, we will try to auto-authenticate Kong to the IRIS endpoint.

To do so, we will use and plugin, resquest-transformer.

![alt](https://raw.githubusercontent.com/grongierisc/iam-training/training/misc/img/auto_authenticate.png)

## 5.1. Add a plugin to the service

<table>
<tr>
<th> IAM Portal </th>
<th> Rest API </th>
</tr>
<tr>
<td>

![alt](https://raw.githubusercontent.com/grongierisc/iam-training/training/misc/img/service_plugin.png)

</td>
<td>

```sh
# Create plugin
curl -i -X POST \
--url http://localhost:8001/services/crud/plugins \
--data 'name=request-transformer' \
--data 'config.add.headers=Authorization:Basic U3VwZXJVc2VyOlNZUw==' \
--data 'config.replace.headers=Authorization:Basic U3VwZXJVc2VyOlNZUw=='
```

</td>
</tr>
</table>

## 5.2. Test it !

<table>
<tr>
<th> Original API </th>
<th> Proxy API </th>
</tr>
<tr>
<td>

```sh
# Legacy


curl –i --location --request GET 'http://localhost:52773/crud/persons/all' 


```

</td>
<td>

```sh
# KONG


curl –i --location --request GET 'http://localhost:8000/persons/all' 


```

</td>
</tr>
</table>

What do we see here :
* Error 401 on lecay
* We reatch the data without authentification


# 6. Third, add our own authentication

What we want to achived here is to add our own authentication without any distuption of the orignial API.

![alt](https://raw.githubusercontent.com/grongierisc/iam-training/training/misc/img/custom_auth.png)

## 6.1. Add consumers

<table>
<tr>
<th> IAM Portal </th>
<th> Rest API </th>
</tr>
<tr>
<td>

![alt](https://raw.githubusercontent.com/grongierisc/iam-training/training/misc/img/consumer_anonymous.png)

</td>
<td>

```sh
# Add consumer anonymous
curl -i -X POST \
--url http://localhost:8001/consumers/ \
--data "username=anonymous" \
--data "custom_id=anonymous"
```

</td>
</tr>
<tr>
<td>

![alt](https://raw.githubusercontent.com/grongierisc/iam-training/training/misc/img/consumer_user.png)

</td>
<td>

```sh
# Add consumer user
curl -i -X POST \
--url http://localhost:8001/consumers/ \
--data "username=user" \
--data "custom_id=user"
```

</td>
</tr>
</table>

## 6.2. Add Basic auth plugin

<table>
<tr>
<th> IAM Portal </th>
<th> Rest API </th>
</tr>
<tr>
<td>

![alt](https://raw.githubusercontent.com/grongierisc/iam-training/training/misc/img/basic_auth.png)

</td>
<td>

```sh
# Enable basic auth for service
curl -i -X POST http://localhost:8001/routes/crud-route/plugins \
--data "name=basic-auth" \
--data "config.anonymous=5cc8dee4-066d-492e-b2f8-bd77eb0a4c86" \
--data "config.hide_credentials=false"
```

</td>
</tr>
</table>

Where :
* config.anonymous = uuid of anonymous consumer

## 6.3. Add ACL Plugin

<table>
<tr>
<th> IAM Portal </th>
<th> Rest API </th>
</tr>
<tr>
<td>

![alt](https://raw.githubusercontent.com/grongierisc/iam-training/training/misc/img/acl.png)

</td>
<td>

```sh
# Enable ACL

curl -i -X POST http://localhost:8001/routes/crud-route/plugins \
--data "name=acl" \
--data "config.whitelist=user"
```

</td>
</tr>
</table>

## 6.4. Configure USER with ACL and credentials


<table>
<tr>
<th> IAM Portal </th>
<th> Rest API </th>
</tr>
<tr>
<td>

![alt](https://raw.githubusercontent.com/grongierisc/iam-training/training/misc/img/user_config.png)

</td>
<td>

```sh
# Add consumer group
curl -i -X POST \
--url http://localhost:8001/consumers/user/acls \
--data "group=user"
# Add consumer credentials
curl -i -X POST http://localhost:8001/consumers/user/basic-auth \
--data "username=user" \
--data "password=user"
```

</td>
</tr>
</table>

## 6.5. Test it !

<table>
<tr>
<th> Original API </th>
<th> Proxy API </th>
</tr>
<tr>
<td>

```sh
# Legacy


curl –i --location --request GET 'http://localhost:52773/crud/persons/all' \
--header 'Authorization:Basic dXNlcjp1c2Vy'


```

</td>
<td>

```sh
# KONG

curl –i --location --request GET 'http://localhost:8000/persons/all' \
--header 'Authorization:Basic dXNlcjp1c2Vy'


```

</td>
</tr>
</table>

# 7. Exercice, Rate-Limiting

1. Enable Unauthenticated user
2. Limite rate by 2 calls per minutes to Unauthenticated user

## 7.1. Correction

1. Enable Unauthenticated user

<table>
<tr>
<th> IAM Portal </th>
<th> Rest API </th>
</tr>
<tr>
<td>

![alt](https://raw.githubusercontent.com/grongierisc/iam-training/training/misc/img/acl_anonymous.png)

</td>
<td>

```sh
# Add consumer group

curl -i -X POST \
--url http://localhost:8001/consumers/anonymous/acls \
--data "group=user"
```

</td>
</tr>
</table>

2. Limite rate by 2 calls per minutes to Unauthenticated user

<table>
<tr>
<th> IAM Portal </th>
<th> Rest API </th>
</tr>
<tr>
<td>

![alt](https://raw.githubusercontent.com/grongierisc/iam-training/training/misc/img/rate_limite_user.png)

</td>
<td>

```sh
# Add rate limite consumer
curl -i -X POST \
--url http://localhost:8001/consumers/anonymous/plugins \
--data "name=rate-limiting" \
--data "config.limit_by=consumer" \
--data "config.minute=2"
```

</td>
</tr>
</table>

# Dev Portal

## Overview

The Kong Developer Portal provides :
* a single source of truth for all developers
* intuitive content management for documentation
* streamlined developer onboarding
* role-based access control (RBAC)

![alt](https://raw.githubusercontent.com/grongierisc/iam-training/training/misc/img/dev_portal.png)

## Enable it !

<table>
<tr>
<th> IAM Portal </th>
<th> Rest API </th>
</tr>
<tr>
<td>

![alt](https://raw.githubusercontent.com/grongierisc/iam-training/training/misc/img/dev_portal_enable.png)

</td>
<td>

```sh
curl -X PATCH http://localhost:8001/workspaces/default --data "config.portal=true"
```

</td>
</tr>
</table>

## Add your first spec

<table>
<tr>
<th> IAM Portal </th>
<th> Rest API </th>
</tr>
<tr>
<td>
<tr>
![alt](https://raw.githubusercontent.com/grongierisc/iam-training/training/misc/img/portal_editor.png)
</tr>
<tr>
![alt](https://raw.githubusercontent.com/grongierisc/iam-training/training/misc/img/portal_editor_ui.png)
</tr>
</td>
<td>

```sh
curl -X POST http://localhost:8001/default/files -F "path=specs/iam-training.yml" -F "contents=@misc/spec.yml"
```

</td>
</tr>
</table>
