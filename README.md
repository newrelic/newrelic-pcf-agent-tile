# newrelic-pcf-agent-tile
==========================================================
- - -

This repository contains the Pivotal Cloud Foundry Tile that allows you to automatically bind New Relic language agents with your applications in PCF on-prem environment for OpsMgr 1.9.x to 2.2.x.

The latest version of the tile **NewRelic-ServiceBrokerTile-OpsMgr-v1.12.13.pivotal** supports PCF versions 2.2.x. 

<p class="note warning"><strong>WARNING</strong>: Version **1.12.3** of the tile supports upgrades from 1.11.4.</p>

<p class="note warning"><strong>WARNING</strong>: If you are upgrading to tile version 1.12.0 from an older version of the tile, you need to remove the old service instances, service offerings, service broker, and the org for the old tile.</p>

<p class="note warning"><strong>WARNING</strong>: The current tile removes the <code>all_open</code> security group from the tile default security settings.
If you are using a previous versions of the tile, make your PCF environment more secure by
removing the <code>all_open</code> security group from the Application Security Group (ASG) settings. 
The new version of the tile does not open the security, nor does it close the security if it was already open.</p>

<p class="note warning"><strong>NOTE</strong>: In version 1.11.4 bumped up the heap memory due to requirements by the java buildpack.</p>

<p class="note warning"><strong>NOTE</strong>: In version 1.11.3 removed the minor version number from stemcell criteria to allow users to apply stemcell security patches, and fixed discrepancy between the tile version and version number displayed on the tile.</p>



Refer to [New Relic Service Broker for PCF](http://docs.pivotal.io/partners/newrelic/index.html) for details on installation and configuration.


##Prerequisites

*    One or more New Relic accounts/sub-accounts. If you don't have one, feel free to grab one using this link: http://newrelic.com/pivotal.
*    A New Relic valid license key for each account/sub-account. You can obtain the license key for each account from your New Relic account under 'Account Settings'
*    An on-prem/cloud installation of Pivotal Cloud Foundry (PCF) environment with Ops Mgr privileges
*    Proxy host and port details if your PCF environment is behind a firewall

##Installation

*    Download the New Relic Service Broker Tile for PCF for your environment from this repo
*    Login to your PCF Ops Mgr 
*    On the left side of the page click on the button **"Import a Product"**
*    Select the downloaded **.pivotal** tile file (i.e. NewRelic-ServiceBrokerTile-OpsMgr-v1.12.13.pivotal)
*    Allow the file upload to be completed

**Note:** The system doesn't give you any indications during the upload, but you will get a pop-up message at the end, as to whether the upload succeeded or failed.


Once the tile is uploaded to PCF environment:

*    Hover your mouse on **"Newrelic Service Broker v-x.x.x"** on the left navigation in the list of **"Available Products"** that have been uploaded, and select the **"+"** sign button to add the tile
*    Click on New Relic Tile that was just added to the "Installation Dashboard"
*    Under **"Settings"** tab in the **"Assign AZs and Networks"** select the desired network options
*    click the **"Save"** button and select **"New Relic Service Broker"**
*    For every account or sub-account you want to add click the **"Add"** button on the right side
*    Enter **"Plan Name"**, **"Plan Description"**, and your **"New Relic license key"** for each account/sub-account
*    If the plans were created in service broker version 1.12.12 or older, you need to provide the original **"plan guid"**. You can obtain the original plan guids for existing plan from 1.12.12 or older version of the service broker by running the following **"cf"** command at the operating system prompt of your computer:
		cf curl $(cf curl /v2/services?q=label:newrelic | grep "service_plans_url" | awk '{print $2}' | sed 's/[",]//g') | egrep "\"name\":|\"unique_id\":" | sed 's/[\",]//g' | tr -s " " | awk ' {name=$0; getline; printf("\t%-40s %-40s\n",name,$0)}'
*    Once you have entered plans for all desired accounts, and you have added original plan guids as necessary, click the **"Save"** button
*    Go back to **"Installation Dashboard"** (link on top left of the page)
*    Click the big blue **"Apply changes"** button on top right of the page. This will take about some time to finish depending how large of a PCF deployment you have.
*    Once changes are applied, the tile will appear in the **"Marketplace"**


## Use New Relic agent Tile in PCF

*    Login to your PCF console
*    Select your **"Org"** (or create new **"Org"** and **"Space"** as you wish)
*    Go to **"Marketplace"**
*    Select **"New Relic"** Tile and click on **"View Plan Options"**
*    Select the plan from the left that is associated with your desired New Relic account and click on **"Select this plan"** button
*    Fill in the **"Instance Name"**, 
*    Select the **"space"** you'd like to use
*    Select the **"application"** to which to bind the service
*    Click the **"Add"** button


## Bind the New Relic Service to your application in PCF

*    Login to PCF
*    Navigate to your application
*    In the tabs at the bottom select 'Services'
*    Click the '+ Bind a Service' link and bind your New Relic Service to your application
*    If you need to use a proxy, add the following 'Env Variables' to your application:
```
JAVA_OPTS="-Dnewrelic.config.proxy_host=proxy.yourCompany.com -Dnewrelic.config.proxy_port=nnn"
```

## Restage your Application
After Binding the New Relic service, you will need to restage your application.
*    Login to your PCF Instance:
    *    cf api **\<CF_API_ENDPOINT\>** [--skip-ssl-validation]
    *    cf login -u **\<USER\>** -p **\<PASSWORD\>**
    *    **Note:** if you have multiple **"Org"** and/or **"Space"** in your PCF, it will prompt you to select the desired org and space
*    Restage the application you just bound to the service
    *    cf restage **\<APPNAME\>**
*    Login to your New Relic account to check the health of the application


**Note:** If you're using a proxy across all of your applications, you may want to implement a PCF 'Environment Variable Group' for the staging process.
```
$ cf ssevg '{"JAVA_OPTS":"-Dnewrelic.config.proxy_host=proxy.yourCompany.com -Dnewrelic.config.proxy_port=nnn"}'
Setting the contents of the running environment variable group as admin...
OK
```
```
$ cf sevg
Retrieving the contents of the running environment variable group as admin...
OK
Variable Name   Assigned Value
JAVA_OPTS           -Dnewrelic.config.proxy_host=proxy.yourCompany.com -Dnewrelic.config.proxy_port=nnn
```
This will enable you to set the JAVA_OPTS parameters on a more global basis such that all applications would inherit the settings without the need to add application level settings to each application.   You can find more details on 
[Environment Variable Groups](https://docs.pivotal.io/pivotalcf/devguide/deploy-apps/environment-variable.html#evgroups)


