# Deploy to Kubernetes in Google Cloud: Challenge Lab

<div class="lab-preamble__details subtitle-headline-1"><span>1 hour 30 minutes</span> <span>9 Credits</span>

<div class="lab__rating">[](/focuses/10457/reviews?parent=catalog)<a data-target="#lab-review-modal" data-toggle="modal">Rate Lab</a></div>

</div>

</div>

<div class="js-markdown-instructions markdown-lab-instructions" id="markdown-lab-instructions">

## GSP318

![Google Cloud Self-Paced Labs](https://cdn.qwiklabs.com/GMOHykaqmlTHiqEeQXTySaMXYPHeIvaqa2qHEzw6Occ%3D)

## Overview

You must complete a series of tasks within the allocated time period. Instead of following step-by-step instructions, you'll be given a scenario and a set of tasks - you figure out how to complete it on your own! An automated scoring system (shown on this page) will provide feedback on whether you have completed your tasks correctly.

To score 100% you must complete all tasks within the time period!

When you take a Challenge Lab, you will not be taught Google Cloud concepts. To build the solution to the challenge presented, use skills learned from the labs in the quest this challenge lab is part of. You will be expected to extend your learned skills; you will be expected to change default values, but new concepts will not be introduced.

This lab is only recommended for students who have completed the labs in the [Kubernetes in Google Cloud](https://google.qwiklabs.com/quests/29) Quest.

<ql-warningbox>Please make sure you review the labs in the Kubernetes in Google Cloud Quest before starting this lab!</ql-warningbox>

Topics tested:

*   Creating Docker images on a host.
*   Running Docker containers on a host.
*   Storing Docker images in the Google Container Repository (GCR).
*   Deploying GCR images on Kubernetes.
*   Pushing updates onto Kubernetes.
*   Automating deployments to Kubernetes using Jenkins.

Are you up for the challenge?

### Setup

#### Before you click the Start Lab button

Read these instructions. Labs are timed and you cannot pause them. The timer, which starts when you click **Start Lab**, shows how long Google Cloud resources will be made available to you.

This Qwiklabs hands-on lab lets you do the lab activities yourself in a real cloud environment, not in a simulation or demo environment. It does so by giving you new, temporary credentials that you use to sign in and access Google Cloud for the duration of the lab.

#### What you need

To complete this lab, you need:

*   Access to a standard internet browser (Chrome browser recommended).
*   Time to complete the lab.

**Note:** If you already have your own personal Google Cloud account or project, do not use it for this lab.

**Note:** If you are using a Pixelbook, open an Incognito window to run this lab.

## Challenge scenario

You have just completed training on containers and their creation and management and now you need to demonstrate to the Jooli Inc. development team your new skills. You have to help with some of their initial work on a new project around an application environment utilizing Kubernetes. Some of the work was already done for you, but other parts require your expert skills.

You are expected to create container images, store the images in a repository, and configure a Jenkins CI/CD pipeline to automate the build for the product. Your know that Kurt, your supervisor, will ask you to complete these tasks:

*   Create a Docker image and store the Dockerfile.
*   Test the created Docker image.
*   Push the Docker image into the Container Repository.
*   Use the image to create and expose a deployment in Kubernetes
*   Update the image and push a change to the deployment.
*   Create a pipeline in Jenkins to deploy a new version of your image when the source code changes.

Some Jooli Inc. standards you should follow:

*   Create all resources in the `us-east1` region and `us-east1-b` zone, unless otherwise directed.

*   Use the project VPCs.

*   Naming is normally _team-resource_, e.g. an instance could be named **kraken-webserver1**.

*   Allocate cost effective resource sizes. Projects are monitored and excessive resource use will result in the containing project's termination (and possibly yours), so beware. This is the guidance the monitoring team is willing to share: unless directed, use `n1-standard-1`.

### Your challenge

As soon as you sit down at your desk and open your new laptop you receive the following request to complete these tasks. Good luck!

<ql-warningbox>Do not wait for the lab to provision! You can work through tasks 1-3 before you need the provisioning to be finished. Just ensure the workstation exists before starting Task 1.</ql-warningbox>

## Task 1: Create a Docker image and store the Dockerfile

Open Cloud Shell and run `source <(gsutil cat gs://cloud-training/gsp318/marking/setup_marking.sh)`. This command will install marking scripts you can use to help check your progress.

Use Cloud Shell to clone the `valkyrie-app` source code repository (it is in your project).

The app source code is in `valkyrie-app/source`. Create `valkyrie-app/Dockerfile` and add the configuration below.

    FROM golang:1.10
    WORKDIR /go/src/app
    COPY source .
    RUN go install -v
    ENTRYPOINT ["app","-single=true","-port=8080"]

Use `valkyrie-app/Dockerfile` to create a Docker image called **valkyrie-app** with the tag **v0.0.1**

Once you have created the Docker image, and before clicking **Check my progress**, run `step1.sh` to perform the local check of your work. After you get a successful response from the local marking you can check your progress.

Click _Check my progress_ to verify the objective. <ql-activity-tracking step="1">Create a Docker image and store the Dockerfile</ql-activity-tracking>

<ql-warningbox>If you don't get a green check mark, click on the **Score** fly-out on the top right and click **Run Step** on the relevant step. A hint pop up opens to give you advice.</ql-warningbox>

## Task 2: Test the created Docker image

Launch a container using the image **valkyrie-app:v0.0.1**. You need to map the host’s port 8080 to port 8080 on the container. Add `&` to the end of the command to cause the container to run in the background.

When your container is running you will see the page by **Web Preview**.

Once you have your container running, and before clicking **Check my progress**, run `step2.sh` to perform the local check of your work. After you get a successful response from the local marking you can check your progress.

Click _Check my progress_ to verify the objective. <ql-activity-tracking step="2">Test the created Docker image</ql-activity-tracking>

<ql-warningbox>If you don't get a green check mark, click on the **Score** fly-out on the top right and click **Run Step** on the relevant step. A hint pop up opens to give you advice.</ql-warningbox>

## Task 3: Push the Docker image in the Container Repository

Push the Docker image **valkyrie-app:v0.0.1** into the Container Registry.

Make sure you re-tag the container to **gcr.io/YOUR_PROJECT/valkyrie-app:v0.0.1**.

Click _Check my progress_ to verify the objective. <ql-activity-tracking step="3">Push the Docker image in the Google Container Repository</ql-activity-tracking>

<ql-warningbox>If you don't get a green check mark, click on the **Score** fly-out on the top right and click **Run Step** on the relevant step. A hint pop up opens to give you advice.</ql-warningbox>

## Task 4: Create and expose a deployment in Kubernetes

Kurt created the `deployment.yaml` and `service.yaml` to deploy your new container image to a Kubernetes cluster (called valkyrie-dev). The two files are in `valkyrie-app/k8s`.

Remember you need to get the Kubernetes credentials before you deploy the image onto the Kubernetes cluster.

Before you create the deployments make sure you check the `deployment.yaml` and `service.yaml` files. Kurt thinks they need some values set (he thinks he left some placeholder values).

You can check the load balancer once it’s available.

Click _Check my progress_ to verify the objective. <ql-activity-tracking step="4">Create and expose a deployment in Kubernetes</ql-activity-tracking>

<ql-warningbox>If you don't get a green check mark, click on the **Score** fly-out on the top right and click **Run Step** on the relevant step. A hint pop up opens to give you advice.</ql-warningbox>

## Task 5: Update the deployment with a new version of valkyrie-app

Before deploying the new code, increase the replicas from 1 to 3 to ensure you don't cause an outage.

Click _Check my progress_ to verify the objective. <ql-activity-tracking step="5">Increase the replicas from 1 to 3</ql-activity-tracking>

<ql-warningbox>If you don't get a green check mark, click on the **Score** fly-out on the top right and click **Run Step** on the relevant step. A hint pop up opens to give you advice.</ql-warningbox>

Kurt made changes to the source code (he put the changes in a branch called **kurt-dev**). You need to merge **kurt-dev** into **master** (you should use `git merge origin/kurt-dev`).

Build the new code as version v0.0.2 of valkyrie-app, push the updated image to the Container Repository, and then redeploy to the valkyrie-dev cluster. You will know you have the new v0.0.2 version because the titles for the cards will be green.

Click _Check my progress_ to verify the objective. <ql-activity-tracking step="6">Update the deployment with a new version of valkyrie-app</ql-activity-tracking>

<ql-warningbox>If you don't get a green check mark, click on the **Score** fly-out on the top right and click **Run Step** on the relevant step. A hint pop up opens to give you advice.</ql-warningbox>

## Task 6: Create a pipeline in Jenkins to deploy your app

This process of building the container and pushing to the container repository can be automated using Jenkins. There is a Jenkins deployment in your `valkyrie-dev` cluster - connect to Jenkins and configure a job to build when you push a change to the source code.

Remember with Jenkins:

*   Get the password with `printf $(kubectl get secret cd-jenkins -o jsonpath="{.data.jenkins-admin-password}" | base64 --decode);echo`.

*   Connect to the Jenkins console using the commands below (but make sure you don't have a running container `docker ps`; if you do, kill it):

    export POD_NAME=$(kubectl get pods --namespace default -l "app.kubernetes.io/component=jenkins-master" -l "app.kubernetes.io/instance=cd" -o jsonpath="{.items[0].metadata.name}")
    kubectl port-forward $POD_NAME 8080:8080 >> /dev/null &

*   Setup your credentials to use **Google Service Account from metadata**.
*   Create a pipeline job that points to your `*/master` branch on your source code.

Make two changes to your files before you commit and build:

*   Edit `valkyrie-app/Jenkinsfile` and change YOUR_PROJECT to your actual project id.
*   Edit `valkyrie-app/source/html.go` and change the two occurrences of green to orange.

Use `git` to:

*   Add all the changes then commit those changes to the master branch.
*   Push the changes back to the repository.

When you are ready, manually trigger a build (the initial build will take some time, so just monitor the process). The build will replace the running containers with containers with different tags; you will see orange colored headings.

Click _Check my progress_ to verify the objective. <ql-activity-tracking step="7">Create a pipeline in Jenkins to deploy your app</ql-activity-tracking>

<ql-warningbox>If you don't get a green check mark, click on the **Score** fly-out on the top right and click **Run Step** on the relevant step. A hint pop up opens to give you advice.</ql-warningbox>

## Congratulations!

![Deploy_to_Kubernetes_Skill_badge_WBG.png](https://cdn.qwiklabs.com/BPq1bJ9GVpeQP4Uzw6fYChiEFfVH4XGGQXpO6X9Tqms%3D)

### Finish Your Quest

This self-paced lab is part of the Qwiklabs [Kubernetes in Google Cloud](https://google.qwiklabs.com/quests/29) Quest. A Quest is a series of related labs that form a learning path. Completing this Quest earns you the badge above, to recognize your achievement. You can make your badge public and link to them in your online resume or social media account. [Enroll in this Quest](https://google.qwiklabs.com/quests/29/enroll) and get immediate completion credit if you've taken this lab. [See other available Qwiklabs Quests](https://google.qwiklabs.com/catalog).

This skill badge quest is part of Google Cloud’s [Hybrid and Multi-Cloud Cloud Architect](https://cloud.google.com/training/kubernetes-anthos-hybridcloud) learning path. Continue your learning journey by enrolling in the [Secure Workloads in Google Kubernetes Engine](https://google.qwiklabs.com/quests/142) skill badge quest.

### Take your next lab

This lab is also part of a series of labs called Challenge Labs. These labs are designed to test your Google Cloud knowledge and skill. Search for "Challenge Lab" in the [lab catalog](https://google.qwiklabs.com/catalog) and challenge yourself!

### Google Cloud Training & Certification

...helps you make the most of Google Cloud technologies. [Our classes](https://cloud.google.com/training/courses) include technical skills and best practices to help you get up to speed quickly and continue your learning journey. We offer fundamental to advanced level training, with on-demand, live, and virtual options to suit your busy schedule. [Certifications](https://cloud.google.com/certification/) help you validate and prove your skill and expertise in Google Cloud technologies.

##### Manual Last Updated December 29, 2020

##### Lab Last Tested October 9, 2020

Copyright 2020 Google LLC All rights reserved. Google and the Google logo are trademarks of Google LLC. All other company and product names may be trademarks of the respective companies with which they are associated.

</div>

</div>

<div class="hidden js-end-lab-button-container lab-content__end-lab-button"><ql-lab-control-button class="js-end-lab-button" running=""></ql-lab-control-button></div>

<div class="lab-content__renderable-instructions">

<div class="lab-content__recommendation">

<section class="upcoming-cards">

## Continue questing

<div class="content-card-grid">

<div class="card-content-wrapper js-content-card" data-id="2524" data-level="Expert" data-name="Deploy to Kubernetes in Google Cloud: Challenge Lab" data-type="Lab">[

<div class="card__body">

<div class="overline card--content__type">Hands-On Lab</div>

### Deploy to Kubernetes in Google Cloud: Challenge Lab

This challenge lab tests your skills and knowledge from the labs in the Kubernetes in Google Cloud quest. You should be familiar with the content of the labs before attempting this lab.

</div>

<div class="card__footer">

<div class="card__footer__left"><span>Expert</span></div>

</div>

](/focuses/10457?parent=catalog)</div>

</div>

</section>

</div>

</div>

</ql-drawer-content><ql-drawer end="" id="outline-drawer" open="" slot="drawer" width="320">

<div class="js-lab-content-outline lab-content__outline">[GSP318](#step1)[Overview](#step2)[Challenge scenario](#step3)[Task 1: Create a Docker image and store the Dockerfile](#step4)[Task 2: Test the created Docker image](#step5)[Task 3: Push the Docker image in the Container Repository](#step6)[Task 4: Create and expose a deployment in Kubernetes](#step7)[Task 5: Update the deployment with a new version of valkyrie-app](#step8)[Task 6: Create a pipeline in Jenkins to deploy your app](#step9)[Congratulations!](#step10)</div>

</ql-drawer></ql-drawer-container></ql-drawer-content></ql-drawer-container>

<div class="lab-introduction js-lab-introduction is-hidden">

<div class="lab-introduction__inner">

# Welcome to Your First Lab!

<ql-icon-button class="js-skip-button">close</ql-icon-button>

<div class="lab-introduction__video"><iframe allow="autoplay; encrypted-media" allowfullscreen="" frameborder="0" id="lab-introduction" src="https://www.youtube.com/embed/yF7EDXKTmoQ?enablejsapi=1&amp;rel=0&amp;showinfo=0"></iframe></div>

<a class="js-skip-button button button--outline">Skip this video</a></div>

</div>

</div>

</main>

<div class="modal fade" id="lab-details-modal">

<div class="modal-container">

<div class="mdl-shadow--24dp modal-content">

<div class="modal-body">

This challenge lab tests your skills and knowledge from the labs in the Kubernetes in Google Cloud quest. You should be familiar with the content of the labs before attempting this lab.

This lab is included in these quests: [Kubernetes in Google Cloud](/quests/29), [Deploy to Kubernetes in Google Cloud](/quests/116). If you complete this lab you'll receive credit for it when you enroll in one of these quests.

**Duration:** 9m setup · 90m access · 90m completion

<span>**Levels:** expert</span>

**Permalink:** [https://google.qwiklabs.com/catalog_lab/2524](https://google.qwiklabs.com/catalog_lab/2524)

</div>

<div class="modal-actions"><a class="button button--text" data-dismiss="modal">Got It</a></div>

</div>

</div>

<iframe class="l-ie-iframe-fix"></iframe></div>

<div class="modal fade" id="lab-review-modal">

<div class="modal-container">

<div class="mdl-shadow--24dp modal-content">

<form class="simple_form js-lab-review-form" id="new_lab_review" action="/lab_reviews" accept-charset="UTF-8" data-remote="true" method="post"><input name="utf8" type="hidden" value="✓">

<div class="modal-body">

How satisfied are you with this lab?*

<div class="l-mtm">

<div class="control-group hidden lab_review_user_id">

<div class="controls"><input class="hidden" type="hidden" value="4061609" name="lab_review[user_id]" id="lab_review_user_id"></div>

</div>

<div class="control-group hidden lab_review_classroom_id">

<div class="controls"><input class="hidden" type="hidden" name="lab_review[classroom_id]" id="lab_review_classroom_id"></div>

</div>

<div class="control-group hidden lab_review_lab_id">

<div class="controls"><input class="hidden" type="hidden" value="2524" name="lab_review[lab_id]" id="lab_review_lab_id"></div>

</div>

<div class="control-group hidden lab_review_focus_id">

<div class="controls"><input class="hidden" type="hidden" name="lab_review[focus_id]" id="lab_review_focus_id"></div>

</div>

<div class="control-group hidden lab_review_rating">

<div class="controls"><input class="hidden js-rating-input" type="hidden" name="lab_review[rating]" id="lab_review_rating"></div>

</div>

<div class="control-group text optional lab_review_comment"><label class="text optional control-label" for="lab_review_comment">Comment</label>

<div class="controls"><textarea class="text optional" name="lab_review[comment]" id="lab_review_comment"></textarea></div>

</div>

</div>

</div>

<div class="modal-actions"><a class="button button--text" data-dismiss="modal">Cancel</a> <input type="submit" name="commit" value="Submit" disabled="disabled" id="submit" data-disabled="false" class="button" data-disable-with="Submit"></div>

</form>

</div>

</div>

<iframe class="l-ie-iframe-fix"></iframe></div>

<div class="modal fade" id="lab-access-modal">

<div class="modal-container">

<div class="mdl-shadow--24dp modal-content"><a class="lab-access-modal-close" data-analytics-action="dismissed_lab_payment_modal" data-dismiss="modal">_close_</a>

<form class="js-lab-access-form" action="/lab_onetime_coupons/activate?parent=catalog" accept-charset="UTF-8" data-remote="true" method="post"><input name="utf8" type="hidden" value="✓">

<div class="modal-body">

<div class="lab-access-modal"><input type="hidden" name="id" id="id" value="10457"> <input type="hidden" name="user_id" id="user_id" value="4061609"> <input type="hidden" name="launch_with_credits" id="launch_with_credits" value="0" class="js-launch-with-credits-input"> <input type="hidden" name="launch_with_subs" id="launch_with_subs" value="0" class="js-launch-with-subscription-input">

<div class="lab-access-modal__method">

This lab costs 9 Credits.

You have a valid subscription package. Would you like to charge this lab to your subscription?

<a class="button js-launch-with-subscription-button js-lab-access-modal-button" data-analytics-action="clicked_launch_with_subscription_button">Use Subscription</a></div>

<div class="lab-access-modal__method">

Enter Lab Access Code:

<div class="lab-access-modal__code js-access-code"><input type="text" name="uuid_1" id="uuid_1" value="" maxlength="4" placeholder="1234" class="js-access-code-input"> <input type="text" name="uuid_2" id="uuid_2" value="" maxlength="4" placeholder="1234" class="js-access-code-input"> <input type="text" name="uuid_3" id="uuid_3" value="" maxlength="4" placeholder="1234" class="js-access-code-input"> <input type="text" name="uuid_4" id="uuid_4" value="" maxlength="4" placeholder="1234" class="js-access-code-input"></div>

<a class="button js-launch-with-access-code-button js-lab-access-modal-button" data-analytics-action="clicked_launch_with_access_code_button">Launch with Access Code</a></div>

</div>

</div>

</form>

</div>

</div>

<iframe class="l-ie-iframe-fix"></iframe></div>

<script>$( function() { ql.initMaterialInputs(); initChosen(); initSearch(); initTabs(); ql.list.init(); ql.favoriting.init(); ql.header.myAccount.init(); initTooltips(); ql.autocomplete.init(); ql.modals.init(); ql.toggleButtons.init(); ql.analytics.init(); ql.favoriting.init(); ql.labControlPanel.addRecaptchaErrorHandler(); initLabContent(); ql.labOutline.links.init(); initLabReviewModal(); initLabAccessModal(); ql.labAssessment.init(); ql.labIntroduction.init( true ); ql.labData.init(); initLabTranslations( {"are_you_sure":"All done? If you end this lab, you will lose all your work. You may not be able to restart the lab if there is a quota limit. Are you sure you want to end this lab?\n","in_progress":"*In Progress*","ending":"*Ending*","starting":"*Starting, please wait*","end_concurrent_labs":"Sorry, you can only run one lab at a time. To start this lab, please confirm that you want all of your existing labs to end.\n","copied":"Copied","no_resource":"Error retrieving resource.","no_support":"No Support","mac_press":"Press ⌘-C to copy","thanks_review":"Thanks for reviewing this lab.","windows_press":"Press Ctrl-C to copy","days":"days"} ); ql.labRun.init(); ql.chat.init(); ql.initHeader(); ql.navigation.init(); ql.navPanel.init(); ql.navigation.init(); });</script> <style>.mdl-layout__container { position: static }</style>