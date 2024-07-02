# SAP Hello World

A basic `NodeJS` RESTful API that persists data in a PostgreSQL DB, can deploy and run in SAP Cloud Platform Cloud Foundry Environment.

## Prerequisites

- You [signed up](https://account.hanatrial.ondemand.com/register) for a [trial account](https://account.hanatrial.ondemand.com/) in SAP Cloud Platform, or you have a productive account.
- Once you got your account, you performed the steps described in [Getting Started with Cloud Foundry](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/en-US/b8ee7894fe0b4df5b78f61dd1ac178ee.html).
- You have basic knowledge about [NodeJS: Development](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/en-US/3a7a0bece0d044eca59495965d8a0237.html), for example, [Create a NodeJS Application](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/en-US/772b45ce6c46492b908d4c985add932a.html) and [SAP NPM Registry](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/en-US/fe672690385a4541b45622a9088f4503.html). Please make sure that you bookmark these pages: Later [SAP NodeJS Packages](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/en-US/92f1bce8f72946c180d198e21f74a68c.html) and [Secure NodeJS Applications](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/en-US/3a8e4372f8e74d05b4ed03a484865e08.html) are also relevant.
- For more information about working with NPM registry, see this [blog](https://blogs.sap.com/2017/05/16/sap-npm-registry-launched-making-the-lives-of-node.js-developers-easier/).

## Step 1: Configure NPM on your machine to ensure all subsequent NPM calls work.

Execute the following command:

```
npm config set @sap:registry https://registry.npmjs.org
```

## Step 2: Authentication

To log on, access your endpoint with the following command:

```
$ cf api https://api.cf.us10-001.hana.ondemand.com
Setting API endpoint to https://api.cf.us10-001.hana.ondemand.com...
OK

API endpoint:   https://api.cf.us10-001.hana.ondemand.com
API version:    3.166.0

Not logged in. Use 'cf login' or 'cf login --sso' to log in.
```

Depending upon the landscape your account was created in, to log on use the following command:

```
$ cf login --sso
API endpoint: https://api.cf.us10-001.hana.ondemand.com

Temporary Authentication Code ( Get one at https://login.cf.us10-001.hana.ondemand.com/passcode ):
Authenticating...
OK

Targeted org 5c66f386trial_org.

Targeted space Spikes.

API endpoint:   https://api.cf.us10-001.hana.ondemand.com
API version:    3.166.0
user:           terrence.miao@paradise.net
org:            5c66f386trial_org
space:          Spikes
```

If you have access to more than 1 org or space, execute the following command:

```
$ cf target -o ORG -s SPACE
```

## Step 3: Run the service locally.

- Clone this repo to your machine.  
- In the folder you cloned into, execute the `npm install` command.
- To start the server, execute the `node server.js` command.
- To get all users or the details of one user, browse `http://<ip>:8088/users` or `http://<ip>:8088/users/2`.
- To add another entry to the list of users using the POST operation use, for example, the `Postman` extension of Chrome. (PUT and DELETE are not yet implemented). To test these operations, import the file `SAP-CP-CF_Hello_World.postman_collection.json` from this repository into `Postman`.  

## Step 4: Create the PostgreSQL service instance.

Perform the following command:

```
$ cf marketplace -e postgresql-db
Getting service plan information for service offering postgresql-db in org 5c66f386trial_org / space Spikes as terrence.miao@paradise.net...

broker: sm-backing-services-broker-postgresql-db-cf-us10-001-51b42369-07f7-443f-a1c3-ae6a75d005bb
   plan    description                         free or paid   costs
   trial   Trial PostgreSQL service offering   free

$ cf create-service postgresql-db trial sapcpcfhw-db -c '{
        "memory": 2,
        "storage": 50,
        "engine_version": "14",
        "multi_az": true
}'
Creating service instance sapcpcfhw-db in org 5c66f386trial_org / space Spikes as terrence.miao@paradise.net...

Create in progress. Use 'cf services' or 'cf service sapcpcfhw-db' to check operation status.
OK

$ cf service sapcpcfhw-db
Showing info of service sapcpcfhw-db in org 5c66f386trial_org / space Spikes as terrence.miao@paradise.net...

name:            sapcpcfhw-db
guid:            316b4422-6a37-421e-8aae-91e3cac25df7
type:            managed
broker:          sm-backing-services-broker-postgresql-db-cf-us10-001-51b42369-07f7-443f-a1c3-ae6a75d005bb
offering:        postgresql-db
plan:            trial
tags:
offering tags:   relational, database
description:     PostgreSQL service on SAP BTP
documentation:
dashboard url:

Showing status of last operation:
   status:    create succeeded
   message:   Operation create_instance for instance 316b4422-6a37-421e-8aae-91e3cac25df7 completed successfully
   started:   2024-06-28T12:52:43Z
   updated:   2024-06-28T12:52:43Z

Showing bound apps:
   There are no bound apps for this service instance.

Showing sharing info:
   This service instance is not currently being shared.

Showing upgrade status:
   Upgrades are not supported by this broker.

$ cf bind-service sapcpcfhw sapcpcfhw-db
Binding service instance sapcpcfhw-db to app sapcpcfhw in org 5c66f386trial_org / space Spikes as terrence.miao@paradise.net...
OK

TIP: Use 'cf restage sapcpcfhw' to ensure your env variable changes take effect

$ cf restage sapcpcfhw
This action will cause app downtime.

Restaging app sapcpcfhw in org 5c66f386trial_org / space Spikes as terrence.miao@paradise.net...

Staging app and tracing logs...
   Downloading nodejs_buildpack...
   Downloaded nodejs_buildpack (273.3M)
   Cell c839c7b1-088e-41af-b9b7-66256aacd457 creating container for instance 7068521b-4318-4f76-b8a5-ec03ac4c4ae4
   Security group rules were updated
   Cell c839c7b1-088e-41af-b9b7-66256aacd457 successfully created container for instance 7068521b-4318-4f76-b8a5-ec03ac4c4ae4
   Downloading app package...
   Downloading build artifacts cache...
   Downloaded build artifacts cache (225B)
   Downloaded app package (678.2K)
   -----> Nodejs Buildpack version 1.8.24
   -----> Bootstrapping python
   -----> Installing python 3.11.9
   Copy [/tmp/buildpacks/56091fcb779a24a9/dependencies/21946bb90387e2dbfa40b0d6ca474060/python_3.11.9_linux_x64_cflinuxfs4_dea1f79d.tgz]
   -----> Installing binaries
   engines.node (package.json): 18.x.x
   engines.npm (package.json): unspecified (use default)
   -----> Installing node 18.20.2
   Copy [/tmp/buildpacks/56091fcb779a24a9/dependencies/106b0f6b3b0f03ec74a01833f9959529/node_18.20.2_linux_x64_cflinuxfs4_bfaf6e93.tgz]
   Using default npm version: 10.5.0
   -----> Installing yarn 1.22.19
   Copy [/tmp/buildpacks/56091fcb779a24a9/dependencies/0f06052381cbcef1593bd279ca3d9458/yarn_1.22.19_linux_noarch_any-stack_32d0e82e.tgz]
   Installed yarn 1.22.19
   -----> Creating runtime environment
   PRO TIP: Warning: package-lock.json not found. The buildpack may reach out to the internet to download module updates, even if they are vendored.
   Visit https://docs.cloudfoundry.org/buildpacks/node/index.html#offline_environments
   PRO TIP: Warning: package-lock.json not found. The buildpack may reach out to the internet to download module updates, even if they are vendored.
   Visit https://docs.cloudfoundry.org/buildpacks/node/index.html#offline_environments
   NODE_ENV=production
   NODE_HOME=/tmp/contents1579284933/deps/0/node
   NODE_MODULES_CACHE=true
   NODE_VERBOSE=false
   NPM_CONFIG_LOGLEVEL=error
   NPM_CONFIG_PRODUCTION=true
   -----> Building dependencies
   Prebuild detected (node_modules already exists)
   Rebuilding any native modules
   rebuilt dependencies successfully
   Installing any new modules (package.json)
   up to date in 381ms
   Contrast Security no credentials found. Will not write environment files.
   Exit status 0
   Uploading droplet, build artifacts cache...
   Uploading droplet...
   Uploading build artifacts cache...
   Uploaded build artifacts cache (226B)
   Uploaded droplet (45.2M)
   Uploading complete
   Cell c839c7b1-088e-41af-b9b7-66256aacd457 stopping instance 7068521b-4318-4f76-b8a5-ec03ac4c4ae4
   Cell c839c7b1-088e-41af-b9b7-66256aacd457 destroying container for instance 7068521b-4318-4f76-b8a5-ec03ac4c4ae4

Restarting app sapcpcfhw in org 5c66f386trial_org / space Spikes as terrence.miao@paradise.net...

Stopping app...

Waiting for app to start...

Instances starting...

name:                sapcpcfhw
requested state:     started
isolation segment:   trial
routes:              sapcpcfhw-friendly-giraffe-hs.cfapps.us10-001.hana.ondemand.com
last uploaded:       Fri 28 Jun 23:55:17 AEST 2024
stack:               cflinuxfs4
buildpacks:
isolation segment:   trial
	name               version   detect output   buildpack name
	nodejs_buildpack   1.8.24    nodejs          nodejs

type:           web
sidecars:
instances:      1/1
memory usage:   128M
     state     since                  cpu    memory          disk           logging             details
#0   running   2024-06-28T13:55:32Z   0.0%   51.9M of 128M   166.6M of 1G   0B/s of unlimited
```

For more information about this command, see the [documentation](https://docs.cloudfoundry.org/devguide/services/managing-services.html).

## Step 5: Push to Cloud and run the service.

To deploy the application to SAP Cloud Platform Cloud Foundry Environment, execute the following command:

```
$ cf push --random-route
Pushing app sapcpcfhw to org 5c66f386trial_org / space Spikes as terrence.miao@paradise.net...
Applying manifest file /Users/terrence/Projects/SAP/cloud-cf-helloworld-nodejs/manifest.yml...

Updating with these attributes...
  ---
  applications:
  - name: sapcpcfhw
    path: /Users/terrence/Projects/SAP/cloud-cf-helloworld-nodejs
    memory: 128M
+   random-route: true
+   buildpack: nodejs_buildpack
Manifest applied
Packaging files to upload...
Uploading files...
 637.12 KiB / 637.12 KiB [===============================================] 100.00% 2s

Waiting for API to complete processing files...

Staging app and tracing logs...
   Downloading nodejs_buildpack...
   Downloaded nodejs_buildpack
   Cell 0e9d359d-cbb1-42e2-ae1d-5cfbdc4910b6 creating container for instance 9c8a15c3-b604-42da-8473-b09e56156130
   Security group rules were updated
   Cell 0e9d359d-cbb1-42e2-ae1d-5cfbdc4910b6 successfully created container for instance 9c8a15c3-b604-42da-8473-b09e56156130
   Downloading build artifacts cache...
   Downloading app package...
   Downloaded build artifacts cache (224B)
   Downloaded app package (678.2K)
   -----> Nodejs Buildpack version 1.8.24
   -----> Bootstrapping python
   -----> Installing python 3.11.9
   Copy [/tmp/buildpacks/56091fcb779a24a9/dependencies/21946bb90387e2dbfa40b0d6ca474060/python_3.11.9_linux_x64_cflinuxfs4_dea1f79d.tgz]
   -----> Installing binaries
   engines.node (package.json): 18.x.x
   engines.npm (package.json): unspecified (use default)
   -----> Installing node 18.20.2
   Copy [/tmp/buildpacks/56091fcb779a24a9/dependencies/106b0f6b3b0f03ec74a01833f9959529/node_18.20.2_linux_x64_cflinuxfs4_bfaf6e93.tgz]
   Using default npm version: 10.5.0
   -----> Installing yarn 1.22.19
   Copy [/tmp/buildpacks/56091fcb779a24a9/dependencies/0f06052381cbcef1593bd279ca3d9458/yarn_1.22.19_linux_noarch_any-stack_32d0e82e.tgz]
   Installed yarn 1.22.19
   -----> Creating runtime environment
   PRO TIP: Warning: package-lock.json not found. The buildpack may reach out to the internet to download module updates, even if they are vendored.
   Visit https://docs.cloudfoundry.org/buildpacks/node/index.html#offline_environments
   PRO TIP: Warning: package-lock.json not found. The buildpack may reach out to the internet to download module updates, even if they are vendored.
   Visit https://docs.cloudfoundry.org/buildpacks/node/index.html#offline_environments
   NODE_ENV=production
   NODE_HOME=/tmp/contents3803092767/deps/0/node
   NODE_MODULES_CACHE=true
   NODE_VERBOSE=false
   NPM_CONFIG_LOGLEVEL=error
   NPM_CONFIG_PRODUCTION=true
   -----> Building dependencies
   Prebuild detected (node_modules already exists)
   Rebuilding any native modules
   rebuilt dependencies successfully
   Installing any new modules (package.json)
   up to date in 362ms
   Contrast Security no credentials found. Will not write environment files.
   Exit status 0
   Uploading droplet, build artifacts cache...
   Uploading build artifacts cache...
   Uploading droplet...
   Uploaded build artifacts cache (225B)

Waiting for app sapcpcfhw to start...

Instances starting...

name:                sapcpcfhw
requested state:     started
isolation segment:   trial
routes:              sapcpcfhw-friendly-giraffe-hs.cfapps.us10-001.hana.ondemand.com
last uploaded:       Fri 28 Jun 22:05:32 AEST 2024
stack:               cflinuxfs4
buildpacks:
isolation segment:   trial
	name               version   detect output   buildpack name
	nodejs_buildpack   1.8.24    nodejs          nodejs

type:            web
sidecars:
instances:       1/1
memory usage:    128M
start command:   npm start
     state     since                  cpu    memory          disk           logging             details
#0   running   2024-06-28T12:05:46Z   0.9%   42.5M of 128M   166.6M of 1G   0B/s of unlimited
```

```
$ cf logs sapcpcfhw --recent
```

![GitLab Pipeline](GitLab%20Pipeline.png "GitLab Pipeline")

![GitLab Variables](GitLab%20Variables.png "GitLab Variables")

For more information on these commands, see [Getting Started with Cloud Foundry](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/en-US/b8ee7894fe0b4df5b78f61dd1ac178ee.html) and [Deploy an Application](http://docs.cloudfoundry.org/devguide/deploy-apps/deploy-app.html).

Check the output of this command, and write down the URL created for the application. As a result you should be able to browse `https://<URL for your app>/users`. If you want to use the `Postman` collection above, please adjust the URL for the requests in the Cloud folder to the allocated `<URL for your app>`.

References
----------

- Cloud Foundry - Hello World Sample using NodeJS, _https://github.com/SAP-samples/cloud-cf-helloworld-nodejs_
