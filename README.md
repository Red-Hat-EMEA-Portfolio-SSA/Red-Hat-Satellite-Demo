# Red-Hat-Satellite-Demo
Red Hat Satellite Demo

# Red Hat Satellite Demonstration

Hi everyone! In this document I will try to provide you with guidance on how to

run a simple and quick Red Hat Satellite setup. 

## I will cover the following use cases:

- Subscription Management
- Content Management 
- Host Registration
- Patch Management 
- Red Hat Insight Integration  


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

- Red Hat's default repositories management
- Custom repositories management
- Sync Plans
- Content Views


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


### Custom repositories " Fedora Example "

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


















```bash
  npm run deploy
```

