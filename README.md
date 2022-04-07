# Red-Hat-Satellite-Demo
Red Hat Satellite Demo

# Red Hat Satellite Demonstration

Hi everyone! In this document I will try to provide you with guidance on how to

run a simple and quick Red Hat Satellite setup. 

## I will cover the following use cases:

- [Subscription Management](#Subscription-Management)
- [Content Management](#Content-Management)
- [Host Registration](#Host-Registration)
- [Patch Management](#Patch-Management)
- [Red Hat Insight Integration](#Red-Hat-Insight-Integration)  


#### This guide has been tested on Red Hat Satellite 6.9 

## Prerequisites:

- Red Hat Satellite 6.x installed.
- At least 2 VMs of RHEL 7 and RHEL 8 respectively.


## Subscription Management

In order for the Red Hat Satellite Server to manage your environment subscriptions, you will need first to export your subscription manifest from your Red Hat account,then import it to your Red Hat Satellite.

### Manifest export from Red Hat account.

1- Login to https://access.redhat.com 

2- Choose Subscription tab.

3- Choose subscription allocation.

4- Create a new allocation by selecting the SKU that you will need to use it locally on Satellite.

5- Export the manifest in a compressed format and download it to your machine.

### Manifest import to Red Hat Satellite

1- Login as admin to your Red Hat Satellite Server gui. 

2- Move the cursor on Content from the left tab menu.

3- Choose subscription

4- Click on Import a manifest

5- Upload your manifest file and now you should see your Red Hat Subscription on Satellite.

## Content Management


In this section we will cover the following:

- [Red Hat's default repositories management](#Default-repositories)
- [Custom repositories management](#Fedora-EPEL-custom-repository)
- [Sync Plans](#Sync-Plans)
- [Content Views](#Content-views) 


### Default repositories
1- Login as admin to your Red Hat Satellite Server gui. 

2- Move the cursor on Content from the left tab menu.

3- Choose Red Hat repositories.

4- From the available repositories choose the following for this demo:

- Red Hat Enterprise Linux 7 server X86
- Red Hat Enterprise Linux 8 Baseos 
- Red Hat Enterpise linux 8 App Stream
- Red Hat Satellite tools 
- Red Hat Ansible 2.9 

5- Expand the chosen repositories and click on enable.

6- Go back to Content Tab menu.

7- Click on Products.

8- Select All and click on sync now. 

Now the repositories will start to be downloaded from the Red Hat CDN to your Satellite server.These repos contains approx 40000 packages so the download will take time depending on the speed of your internet in my lab it took me 45 min.


### Fedora EPEL custom repository

1- Login as admin to your Red Hat Satellite Server gui. 

2- Move the cursor on Content from the left tab menu.

3- Choose Products

4- Create new Product

5- Insert the following inputs then save:
- name: EPEL-Fedora
- Label: EPEL-Fedora
- Keep the remaining entries empty for now.

6- Click on new repository from repositories tab

7- Insert the following inputs then save:

- Name: Fedora_8
- Label: Fedora_8
- Type: yum
- Upstream URL: https://dl.fedoraproject.org/pub/epel/8/Everything/
- Keep the remaining entries empty for now since this is a public repository

8- Select the Fedora 8 product and click on Sync Now.

The Fedora EPEL repository will start to be downloaded on your Satellite server, this step took 30 min in my lab environment.

### Sync Plans

Instead of manually downloading the repo and the packages using the sync now option, we can create a sync plan which will schedule this download or sync action for your environment.

1- Login as admin to your Red Hat Satellite Server gui. 

2- Move the cursor on Content from the left tab menu.

3- Choose Sync Plans

4- Create new sync plan and insert the following inputs then save:

- Name: sync plan for demo
- Interval: Weekly
- Start time: 18:00 
- Leave the remaining entries as default

5- Select the sub Add tab from Products Tab in "sync Plan for demo"

6- Add the product that you would like to associate with the sync plan and then save.


The selected products and their repos will be updated automatically on a weekly basis.

### Content views 

Now the question is after downloading the repos into your Satellite environment how are you going to show it to the client ? 
- Does all the clients needs to have access to all the repos ?
- Does the clients needs an access to all the content of a certain repo or just the patching part of it for example ?

So we use content views to filter / show the repos to the clients , one of the method to do this filteration is to associate the content view with a certain lifecycle. 


#### Lifecycle creation 

Creation of lifcycle is a method to separate the clients servers from a content association point of view. 

1- Login as admin to your Red Hat Satellite Server gui. 

2- Move the cursor on Content from the left tab menu.

3- Choose Lifecycle environment

4- Click on Create environment path 

5- On the new environment enter the following:

- Name: Dev
- Label : Dev
6- Then click on add new environment
- Name: QA
- Label : QA
- Prior environment : Dev 

7- Repeat step #6 for all the remaining environment.

#### Content view creation

1- Login as admin to your Red Hat Satellite Server gui. 

2- Move the cursor on Content from the left tab menu.

3- Choose Content Views

4- Click on Create new view and enter the following then save:

- Name: Content for Dev
- Label: Content_for_dev
- Tik the box on Solve Dependencies

5- Select the repositories that you would like to show in this content view from the Yum Content tab under "content for dev"
- For example: Fedora 8 , Ansible & Satellite tools 

-  Under file repositories you can perform several filteration , but this will be skipped in this demo.

7- Click on publish new version 

8- Click on promote under action column and choose dev environment. 

Now Fedora 8 repo , Ansible & Satellite tools are part of the content view and the client connected to this view will have access only to these repos.

## Host Registration 

Now it is time to connect the client server with satellite so they can benefit from the subscriptions and content created in the previous steps.

1- Install the pre-built bootstrap RPM to point the client into satellite instead of CDN:
```bash
curl --insecure --output katello-ca-consumer-latest.noarch.rpm https://satellite.example.com/pub/katello-ca-consumer-latest.noarch.rpm
yum localinstall katello-ca-consumer-latest.noarch.rpm 
```

2- Register using subscription-manager using an Activation Key:
```bash
subscription-manager register --org="Hackfest" --activationkey="<Activation Key Name>"
```
3- Enable satellite-tools repository if you want to use client tools such as katello-agent or puppet:
```bash
subscription-manager repos --enable=satellite-tools-$SATELLITE_VERSION-for-rhel-$RHEL_MAJOR_VERSION-x86_64-rpms
```
4-Install client package to report package & errata information:
```bash
yum -y install katello-host-tools
```
Tracer helps administrators identify applications that need to be restarted after a system is patched. To optionally report tracer information:
```bash
yum -y install katello-host-tools-tracer
```
For remote actions via katello-agent:
```bash
yum -y install katello-agent
```
## Patch Management
Patch Management is one of the critical activities that takes place in operating system world. Red Hat Satellite has the ability to manage the patch at scale which help you to secure your system and save your time.

### Individual Host Patch Management 

1- Login as admin to your Red Hat Satellite Server gui. 

2- Move the cursor on host from the left tab menu.

3- Choose Content Hosts

4- As you can see below in the image , all the host has been already scanned towards their respective repositories and the patches are available under the Installable Updates column. 

![image](https://user-images.githubusercontent.com/73826486/162186545-d2724857-b433-4ff4-9259-4ad801fc87e6.png)

5- Now click on security and select the Errata needed to be patched and click on apply.
 
   Also you can click on the ID of the Errata itself and get more description on the patch. 
   
![image](https://user-images.githubusercontent.com/73826486/162188411-6bc3bd93-8780-4acb-a660-2944f8256ba0.png)

### 

## Red Hat Insight Integration


















