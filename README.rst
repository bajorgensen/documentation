dotnetcore-city-info
====================

A simple demo application that refreshes information about cities and shows the
results on a web page. This demonstrates severeal features of OpenShift
Container Platform.

.. contents:: **Contents;**

**Screenshot**

.. image:: img/screenshot.png

How to deploy this on OpenShift
===============================

In OpenShift Container Platform, you need to select **.NET Core Builder Images**
from the service catalog. If you are running OpenShift Origin, you can import 
this image builder into your service catalog by running `oc create -f ...` 
against these files; https://github.com/openshift/openshift-ansible/tree/master/roles/openshift_examples/files/examples/v3.7/image-streams

.. image:: img/dotnetcorebuilder.png

This should spawn the deployment wizard, click "Next >";

.. image:: img/dotnetDeploymentWizard1.png

Complete the Wizard with the following details;

* **Add to Project:** Create Project
* **Project Name:** city-info
* **Project Display Name:** City Info
* **Project Description:** ...
* **Version:** 2.0 - The screenshot *above* shows the OpenShift builder is version 1.0, however, this builder has the option to use a compiler which is different for .NET Core 1.0 or .NET Core 2.0 projects. The version you are selecting in the screenshot *below* is the **compiler version**, and this is a .NET Core 2.0 project. Therefore it is very important to select version 2 builder. If you use select version 1 here by accident then it will expect a .NET Core 1.0 project.json files and similar that have been deprecated and the build would fail!
* **Application Name:** city-info
* **Git Repository:** https://github.com/jamesread/dotnetcore-city-info.git

.. image:: img/dotnetDeploymentWizard2.png

Once the project has been created, it should see a build start in the project overview;

.. image:: img/appOverview.png

You are now ready to try out one or more of the demo scenarios!
   
Demo Scenarios
==============

Deploying a feature branch for multiimgiate testing or A/B deployments
----

* **Time to demo:** 5 minutes
* **Why is this cool?:** Feature branches are commonly used by developers to develop new features that have not yet been integrated into the main application. Product Managers might typically want to deploy a feature branch to see how users accept this feature, to test it works as expected, and gradually introduce it as users respond positively. 

* Use the .NET Core Builder Image to deploy (the master branch) as normal. This
must be built with a v2.0 builder.

.. image:: img/dotnetBuilder.png

* Add to the project again, with a .NET Core Builder Image. This time, use an
**advanced options** to and specify the **feature_weather** branch.

.. image:: img/advOptions.png

.. image:: img/gitReference.png

* Dot *not* deploy a new route.

.. image:: img/noRoute.png

* You should have something that looks like this;

.. image:: img/overview.png

* Now go find the route, and edit it. You want to "Split traffic across
multiple services" and select the new feature branch.

.. image:: img/splitRoute.png

Once saved, it should look something like this;

.. image:: img/splitRouteOverview.png

* Now go view the application. 

Press Ctrl + F5 to refresh the page. Half of the time, you'll get weather
icons, half of the time, you'll not get weather icons. Make sure Cookies are
disabled to disable session persistance. 

.. image:: img/withWeather.png

.. image:: img/withoutWeather.png

Using envionment imgiables for Feature Toggles
----

* **Time to demo:** 5 minutes
* **Why is this cool?:** Showchasing how a modern application development principle, "feature toggles", are simply and easily implemented with containers.

This C# dotnet application will check for the environment imgiable
SHOW_POPULATION as a means for enabling this feature in responses to requests
for city information. You should set this in the DeploymentConfig, and watch OpenShift deploy a new version automatically.

.. image:: img/editDcEnv.png

Wait a moment while the application redeploys (necessary for environment imgiables to take effect). 

You should see your city information pop up now with population numbers;

.. image:: img/addPopulation.png


Configuring all pods to connect to a database
----

* **Time to demo:** 5 minutes
* **Why is this cool?:** Showcases how microservice workloads absolutely don't have to be stateless only, and how configuration is commonly acheived with containers. 

======================= ================ ======================================
  Environment Vaiable     Example          Description
======================= ================ ======================================
DATABASE_USERNAME       `SA`             Your MsSQL Database Username
DATABASE_PASSWORD       `toomanysecrets` Your MsSQL Database Password
======================= ================ ======================================

The database schema can be found in `/img/databaseSchema.sql` in this Git repo.

The database name and the single table name are both hard-coded to `city-info`. 

Scaling a deployment
----

* **Time to demo:** 3
* **Why is this cool?:** To show how scaling is so super easy!

You can scale the city-info project at any time, just using the up/down scale buttons. This will not impact existing connected users negatively.

.. image:: img/scalePods.png

When you view the dashboard, you will see the city-info is provided with a pod hostname. When you scale the application however, you will probably see get updates from a single pod.

This is because OpenShift sets a browser cookie, which sticks you onto one pod by default. You can easily overcome this, block the OpenShift cookie in your browser like so;

.. image:: img/blockCookies1.png
.. image:: img/blockCookies2.png

Blue/Green deployments 
----

TODO :) 
