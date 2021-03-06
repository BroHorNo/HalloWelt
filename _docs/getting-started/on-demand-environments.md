---
title: "On-demand environments"
description: "Code collaboration with Codefresh"
group: getting-started
toc: true

---

In this tutorial we will see how Codefresh can be used by many developers who work on multiple features and how to create separate demo environments for each feature. You will learn how to:

* Launch On-Demand test environments for Docker containers
* Use pull requests for feature-based development
* Make sure that Codefresh builds automatically whenever a commit happens

Using Pull Requests for feature development is one of the tenets of a sound Continuous Integration process.
Codefresh can be instructed to build pull requests in an automatic manner (or any other source code branch) making
collaboration among teams much more flexible.

Codefresh also has the unique capability of launching Docker images in temporary test environments. These test environments
are ephemeral and are not intended to be used as QA (let alone production) environments. They are perfect
for quick demos. Use them
if you want to quickly share a feature with a colleague or a customer.

## Launching a Docker image using Codefresh

Docker images play a central role in Codefresh. The [basic CI tutorial]({{site.baseurl}}/docs/getting-started/create-a-basic-pipeline/) describes how you can easily create a Docker image from your source code.

In this section we will take this one step further and actually launch the result Docker image.
Codefresh has the unique capability of launching a docker image (using [Docker Swarm](https://docs.docker.com/engine/swarm/) behind the scenes) on the same hosted environment that Codefresh itself runs.

{% include 
image.html 
lightbox="true" 
file="/images/getting-started/quick-start-test-pr/demo-stage.jpg" 
url="/images/getting-started/quick-start-test-pr/demo-stage.jpg" 
max-width="80%" 
%}

This means that with zero effort from your side you can quickly inspect the status of your application using the Codefresh infrastructure. 






To start a Docker image as a demo environment, locate it in the *Images* section and click the *launch* button.

{% include 
image.html 
lightbox="true" 
file="/images/getting-started/quick-start-test-pr/launch.png" 
url="/images/getting-started/quick-start-test-pr/launch.png" 
alt="Launching a Docker image" 
caption="Launching a Docker image (click image to enlarge)" 
max-width="80%" 
%}


Our sample application is self-contained (it consists of only a single Docker image) so choose *standalone* for the popup menu. Codefresh can also launch demo environments for applications that consist of multiple images (e.g. a service image and a database image). This capability happens with Codefresh *compositions* which are described in detail in section [On-Demand Environments]({{site.baseurl}}/docs/on-demand-test-environment/create-composition/).

Codefresh automatically knows which port should be exposed in the test environment
(i.e. which port of the Docker container should be made available for external connections). The sample application we are using here is exposing its web interface at port 5000 (but a random port will actually be assigned for external connections).

Once your application is launched, Codefresh will present the run log. You will see the same messages that would appear if you executed the `docker run` command locally. 

{% include 
image.html 
lightbox="true" 
file="/images/getting-started/quick-start-test-pr/launch-url.png" 
url="/images/getting-started/quick-start-test-pr/launch-url.png" 
alt="Start logs" 
caption="Start logs (click image to enlarge)" 
max-width="60%" 
%}

### Accessing the test environment

Once launch is complete, Codefresh will print a dynamic URL that contains the deployed environment. Now you have a demo environment created just for you! You can send this link with an email to a colleague to ask for feedback or to a customer to show progress.



{% include 
image.html 
lightbox="true" 
file="/images/getting-started/quick-start-test-pr/demo-environment.png" 
url="/images/getting-started/quick-start-test-pr/demo-environment.png" 
alt="Demo environment" 
caption="Demo environment (click image to enlarge)" 
max-width="60%" 
%}


The number of concurrent test environments that you can have depends on your Codefresh account. Remember that you should never treat
these on demand environments as production ones. They were never designed that way.

If the environment is not functioning correctly for your own application, make sure that the port exposed by Codefresh in the *Launch settings* is the one that is actually used in your application as an HTTP endpoint. 

To find your existing on-demand environments, click *Docker Swarm* `->` *Environments* on the left part of the screen. You will get a list of your active environments. You can see details such as:

* Which branch is this environment from
* Which Git commit represents this environment
* What is the URL endpoint created by Codefresh

{% include 
image.html 
lightbox="true" 
file="/images/getting-started/quick-start-test-pr/env-details.png" 
url="/images/getting-started/quick-start-test-pr/env-details.png" 
alt="Details for an environment" 
caption="Details for an environment (click image to enlarge)" 
max-width="70%" 
%}

On the right side, you can find a list of buttons that allow you to visit the environment directly, share the link on Slack and most importantly stop the environment, so that it doesn't count against your account. It is a good practice to launch environments only when you need them and clean them up once you are done with them.

## Collaborating with Pull requests

Continuous Integration (CI) is based on two pillar stones:

1. The merging of all features into a shared mainline (this is the *integration* part)
2. The capability to auto-build all commits and have a deployable artifact at any given point in time (this is the *continuous* part)


{% include 
image.html 
lightbox="true" 
file="/images/getting-started/quick-start-test-pr/ci-with-merges.png" 
url="/images/getting-started/quick-start-test-pr/ci-with-merges.png" 
alt="CI with merges" 
caption="Continuous Integration (click image to enlarge)" 
max-width="80%" 
%}

Your Git provider (e.g. GitHub) is the one responsible for the Integration part. Codefresh takes care of the Continuous part in a very natural way. It uses webhooks to respond to changes that happen in source control (either in the form of new commits or when branches are created)

### Auto-build of branches and pull requests

By default Codefresh will connect to your Git provider and do the following:

1. Auto-build every new commit that happens in master or any other branch
2. Auto-build every new branch when it is created

You can change the default behavior so that it matches your own workflow using extra [GIT Triggers]({{site.baseurl}}/docs/configure-ci-cd-pipeline/triggers/git-triggers/).

You don't have to do anything special to setup this communication between Codefresh and your Git provider. It was setup automatically for you when you connected your Codefresh account to your Git provider.

Codefresh also creates for you a default GIT trigger the first time you create a project.


{% include 
image.html 
lightbox="true" 
file="/images/pipeline/triggers/default-git-trigger.png" 
url="/images/pipeline/triggers/default-git-trigger.png" 
alt="Default GIT trigger" 
caption="Default GIT trigger" 
max-width="50%" 
%}


If you create a new branch in your repository Codefresh will automatically build it (and also store the resulting Docker image). The build will clearly define its source branch:

{% include 
image.html 
lightbox="true" 
file="/images/getting-started/quick-start-test-pr/auto-build-feature.png" 
url="/images/getting-started/quick-start-test-pr/auto-build-feature.png" 
alt="Building branches" 
caption="Building branches (click image to enlarge)" 
max-width="50%" 
%}

All branches are shown under the respective tab. You can build any branch on its own and you can also start a demo environment for any branch as well.

{% include 
image.html 
lightbox="true" 
file="/images/getting-started/quick-start-test-pr/view-branches.png" 
url="/images/getting-started/quick-start-test-pr/view-branches.png" 
alt="Viewing branches" 
caption="Viewing branches (click image to enlarge)" 
max-width="50%" 
%}




Codefresh also has a dedicated view for Pull Requests. When you open a Pull Request, Codefresh will show it under the respective tab. All facilities we described so far for the main branch are available for Pull requests as well. You can 
create demo environments for Pull Requests in the same manner. Previewing the changes of a specific feature has never been easier.

{% include 
image.html 
lightbox="true" 
file="/images/getting-started/quick-start-test-pr/view-prs.png" 
url="/images/getting-started/quick-start-test-pr/view-prs.png" 
alt="View Pull Requests" 
caption="View Pull Requests (click image to enlarge)" 
max-width="50%" 
%}


When you commit to a Pull Request, not only Codefresh will auto-build it, but you will also see the build request in the GitHub UI as well:

{% include 
image.html 
lightbox="true" 
file="/images/getting-started/quick-start-test-pr/auto-build-pr.png" 
url="/images/getting-started/quick-start-test-pr/auto-build-pr.png" 
alt="Pull Request Status" 
caption="Pull Request Status (click image to enlarge)" 
max-width="50%" 
%}

### Different build options for branches and pull requests

So far, we have used the same build options for all branches and pull requests. Codefresh allows you to define more than one groups of settings. The build settings can then be individually fine-tuned with regards to:

* The build steps (e.g. unit tests and/or integration tests)
* The branch names they affect
* Whether they run automatically or manually


{% include 
image.html 
lightbox="true" 
file="/images/pipeline/triggers/pipeline-examples.png" 
url="/images/pipeline/triggers/pipeline-examples.png" 
alt="Multiple pipelines" 
caption="Multiple pipelines (click image to enlarge)" 
max-width="90%" 
%}

With these basic building blocks, it is possible to create any development process that matches your organization.
Here are two extreme examples.

Early Start-up company:

* A main pipeline that runs automatically and deploys to production from *master* branch.
* A testing pipeline that runs automatically and runs unit tests whenever a Pull request is opened.


Fortune 500 company with complex approval process:

* A main pipeline that runs manually from *master* and executes both unit and integration tests. It deploys to production.
* A testing pipeline that runs manually for *staging* and runs integration tests. It deploys to staging environment.
* A code review pipeline that runs automatically from branches named *feature-x* and runs code quality and static analysis tools.


## What to read next

* [Deploy to Kubernetes]({{ site.baseurl }}/docs/getting-started/deployment-to-kubernetes-quick-start-guide/)
* [Introduction to Pipelines]({{ site.baseurl }}/docs/configure-ci-cd-pipeline/introduction-to-codefresh-pipelines/)
* [Internal Docker Registry]({{ site.baseurl }}/docs/docker-registries/codefresh-registry/)



