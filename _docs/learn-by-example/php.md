---
title: "Create a Docker image for Php"
description: "Using Codefresh pipelines"
group: learn-by-example
toc: true
---

Codefresh can work with Php projects using any of the popular frameworks (Laravel, Symphony, CakePHp etc.)

## The example php project

You can see the example project at [https://github.com/codefresh-contrib/php-composer-sample-app](https://github.com/codefresh-contrib/php-composer-sample-app). The repository contains a simple Php project that uses [composer](https://getcomposer.org/) as a package manager.

The dockerfile uses [multi-stage builds](https://docs.docker.com/develop/develop-images/multistage-build/) to minimize the size of the docker image.

`Dockerfile`
{% highlight docker %}
{% raw %}
FROM composer/composer:php7 as vendor

WORKDIR /tmp/

COPY composer.json composer.json
COPY composer.lock composer.lock

RUN composer install \
    --ignore-platform-reqs \
    --no-interaction \
    --no-plugins \
    --no-scripts \
    --prefer-dist

FROM php:7.2-apache-stretch

COPY . /var/www/html
COPY --from=vendor /tmp/vendor/ /var/www/html/vendor/
{% endraw %}
{% endhighlight %}


## Create a Docker image for Php project

An [example pipeline](https://github.com/codefresh-contrib/php-composer-sample-app/blob/master/codefresh.yml) is also offered in the git repository.
It contains just two [steps]({{site.baseurl}}/docs/codefresh-yaml/steps/). A [clone step]({{site.baseurl}}/docs/codefresh-yaml/steps/git-clone/) to fetch the code and a [build step]({{site.baseurl}}/docs/codefresh-yaml/steps/build/) to create a Docker image.

 `codefresh.yml`
{% highlight yaml %}
{% raw %}
version: '1.0'
steps:
  main_clone:
    title: Cloning main repository...
    type: git-clone
    repo: 'codefresh-contrib/php-composer-sample-app'
    revision: master
    git: github
  MyAppDockerImage:
    title: Building Docker Image
    type: build
    image_name: my-php-image
    working_directory: ./
    tag: master
    dockerfile: Dockerfile
{% endraw %}
{% endhighlight %}

Once you run this pipeline Codefresh will create a Docker image for the Php application:

{% include image.html 
lightbox="true" 
file="/images/learn-by-example/php/php-cicd-pipeline.png" 
url="/images/learn-by-example/php/php-cicd-pipeline.png" 
alt="Creating a docker image for php" 
caption="Creating a docker image for php" 
max-width="80%" 
%}

Notice that all dependencies are downloaded when the dockerfile is created.




## Launch Docker images

Codefresh can also launch Docker images (using Docker swarm behind the scenes). With each Codefresh account you get access to a limited number of Docker environments that can host any Docker image or Docker compose file.

First find your images in the [internal Codefresh registry]({{site.baseurl}}/docs/docker-registries/codefresh-registry/).

{% include image.html 
lightbox="true" 
file="/images/learn-by-example/php/launch-docker-image.png" 
url="/images/learn-by-example/php/launch-docker-image.png" 
alt="Launching a Docker image" 
caption="Launching a Docker image" 
max-width="80%" 
%}

Click on the launch button and a new pipeline will run for deployment:

{% include image.html 
lightbox="true" 
file="/images/learn-by-example/php/test-environment-url.png" 
url="/images/learn-by-example/php/test-environment-url.png" 
alt="Getting the environment url" 
caption="Getting the environment url" 
max-width="80%" 
%}

Notice that the pipeline logs show the dynamic URL of the application. Simply visit it with your browser 
and you will see the result.

{% include image.html 
lightbox="true" 
file="/images/learn-by-example/php/test-environment.png" 
url="/images/learn-by-example/php/test-environment.png" 
alt="Application preview" 
caption="Application preview" 
max-width="80%" 
%}

Notice that these environments are only for testing and previewing your application as it is developed. They are **NOT** for production purposes.



## What to read next

* [Codefresh YAML]({{site.baseurl}}/docs/codefresh-yaml/what-is-the-codefresh-yaml/)
* [Pipeline steps]({{site.baseurl}}/docs/codefresh-yaml/steps/)
* [Creating pipelines]({{site.baseurl}}/docs/configure-ci-cd-pipeline/pipelines/)
* [How pipelines work]({{site.baseurl}}/docs/configure-ci-cd-pipeline/introduction-to-codefresh-pipelines/)

