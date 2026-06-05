---
layout: single
title: "Gitlab-CI Setup & .gitlab-ci.yml Examples"
date: 2018-07-04 14:07:30.000000000 +09:00
type: post
header:
  teaser: "https://docs.gitlab.com/ee/ci/img/cicd_pipeline_infograph.png"
  image: "https://docs.gitlab.com/ee/ci/img/cicd_pipeline_infograph.png"
categories:
- IT
tags: [Git, Gitlab, Gitlab-CI, gitlab-runner, CI, CD]
---

## Adopting CI/CD
 I already covered [how to install Gitlab](https://lovemewithoutall.github.io/it/start-docker/) before. This time, I adopted a CI/CD tool. In projects I worked on in the past, we mainly used [jenkins], but this time I decided to give [Gitlab-CI] a try. Given that most members of our project are Git beginners, choosing [Gitlab-CI]—which integrates tightly with [Gitlab]—seemed advantageous for spreading Git adoption. For a comparison of [Gitlab-CI] and [jenkins], I referred to the links below. Of course, [jenkins] and [Gitlab-CI] aren't really tools you can compare on the same footing in the first place.

### [Gitlab-CI] versus [jenkins]
 * [The Gitlab-CI I built is the best](https://about.gitlab.com/comparison/gitlab-vs-jenkins.html)
 * [Jenkins is good, but I see the potential of Gitlab](https://www.inovex.de/blog/modern-cicd-with-jenkins-2-and-gitlab-ci-comparison/)

## [Gitlab-CI] Setup
[This post by All-Round Player](http://allroundplaying.tistory.com/21) explains it very well. You just have to follow along. It's so good I could kiss the author. The [official Gitlab-CI docs] are thorough too. But you do have to configure what task the **gitlab-runner** will perform. This has to be written in .gitlab-ci.yml.

## .gitlab-ci.yml Setup
Now let's write a **.gitlab-ci.yml** so that the **gitlab-runner** performs the task I want. This isn't very hard either. Each line under **script:** is one command in the executor you entered at step 7 of **gitlab-runner register**, where it asks **Please enter the executor**. For example, if you select **shell** as the executor and configure **.gitlab-ci.yml** with the script below,

```bash
script:
    - echo hello
    - dir
```

you get a result like the following.

```yml
hello # result of echo hello
c:\blah\blah\blah # result of dir
```

## Example: Simple Deployment
Now let's build a **.gitlab-ci.yml** that performs the simple task of copying & pasting the source code. **only** describes which git branch's changes it reacts to.

For example,

```yml
    only:
    - master
```
runs only when there is a commit or change on the master branch.

After *register*ing a **gitlab-runner** on the deployment target server, let's run the **.gitlab-ci.yml** example below.
When a push comes into the master branch or the develop branch, it runs the respective script below.
On a Windows environment, I used shell as the executor and made use of the xcopy command.

```yml
# job that runs when a push comes into the master branch
deploy-to-production-server:
    only:
    - master
    script:
    - xcopy . D:\production_server\where_code_goes\ /h /k /y /e /r /d

# job that runs when a push comes into the develop branch
deploy-to-develop-server:
    only:
    - develop
    script:
    - xcopy . D:\develop_server\where_code_goes\ /h /k /y /e /r /d
```

It's crude, but it works fine for simple deployment. Anyway, It works!
But things get a bit more complicated when you've set up separate develop and production servers.
Also, when there are many services in operation, it's hard to *register* a **gitlab-runner** for each service.

## Example: Remote Deployment Using Shared Runners

To solve the problems of the simple deployment example above, you can deploy by mounting the disk of a remote server with **Shared Runners**.
The case where you register one **gitlab-runner** per project, as in the simple deployment example, is called **Specific Runners**.
The difference between **Shared Runners** and **Specific Runners** is explained well in the [official docs](https://docs.gitlab.com/ce/ci/runners/README.html#shared-vs-specific-runners).
Unless it's a project that runs particularly complex scripts and needs special management, it's more convenient to use **Shared Runners**.
For how to configure **Shared Runners**, refer to [here](https://docs.gitlab.com/ce/ci/runners/README.html#registering-a-shared-runner).
The **.gitlab-ci.yml** below runs with shell as the executor on a Windows environment.

```yml
deploy to production:
    stage: deploy # if you don't set this separately, it defaults to test
    environment:  # setting it separately per server makes rolling back easier later
      name: production server
    only:
    - master
    script:
    - echo 'deploy to production server'
    - 'net use o: \\server_IP\source_code_path server_login_PW /user:server_login_ID' # map the path of the remote server to deploy to as the o drive
    - 'xcopy . o:\ /h /k /y /e /r /d' # deploy
    after_script: # this after_script block always runs even if an error occurs while running script
    - 'net use /delete o:' # disconnect the remote drive

deploy to develop:
    stage: deploy
    only:
    - develop 
    - branches # sometimes you need to urgently push a branch and deploy it to the develop server
    except:
    - master # definitely separate it from the master branch. this is a block added for the example, so it's fine to remove it
    environment:
      name: develop server
    script:
    - echo 'deploy to develop server'
    - 'net use o: \\server_IP\source_code_path server_login_PW /user:server_login_ID'
    - 'xcopy . o:\ /h /k /y /e /r /d'
    after_script:
    - 'net use /delete o:'

```

## Example: Applying stages and cache

In the example above, a single **job** was run for a single deployment.
In this example, I added **stage** and **cache**.
1. If you register **stages**, you can run multiple **jobs** sequentially.
1. If you configure **cache**, you can use the cache created by a previously run **job**. So if you use it at a point like **npm install**, where you have to download libraries and such from the internet every time, you can cut down the deployment time. For details, refer to the [official docs](https://docs.gitlab.com/ee/ci/caching/index.html#).

```yml
# the jobs run in the order deploy -> foreign_survey_build
# the reason deploy runs first is that npm install and build take a lot of time
stages:
  - deploy
  - foreign_survey_build

# run after_script after every job finishes
after_script:
  - 'net use /delete o:'

# every job's name must be unique
deploy-to-dev:
  stage: deploy # this is a job that runs in the deploy stage
  environment:
    name: development server
  only:
    - develop
    - branches
  except:
    - master
  script:
    - echo 'deploy to develop server'
    - 'net use o: \\server_IP\source_code_path server_login_PW /user:server_login_ID'
    - 'xcopy . o:\ /h /k /y /e /r /d /exclude:.\gitlab-ci-copy-exclude-list.txt'

deploy-to-prod:
  stage: deploy # there are 2 jobs with stage: deploy, and in this case the two jobs run in parallel
  environment:
    name: production server
  only:
  - master    
  script:
    - echo 'deploy to production server'
    - 'net use o: \\server_IP\source_code_path server_login_PW /user:server_login_ID'
    - 'xcopy . o:\ /h /k /y /e /r /d /exclude:.\gitlab-ci-copy-exclude-list.txt'

foreign-survey-build-to-dev:
  stage: foreign_survey_build
  only:
    - develop
    - branches
  except:
    - master
  environment:
    name: development server
  script:
    - echo 'foreign_survey is being builded to develop server'    
    - cd foreign_survey\client # only some modules use npm, so I structured it this way
    - call npm install # if there is a cache and package.json hasn't changed, it uses the cached files (stored compressed).
    - call npm run build
    - 'net use o: \\server_IP\source_code_path server_login_PW /user:server_login_ID'
    - 'xcopy dist\. o:\foreign_survey\client\dist\ /h /k /y /e /r'
  cache:
    key: ${CI_COMMIT_REF_SLUG} # uses the cache created on this branch (master)
    paths:
    - foreign_survey/client/node_modules/ # defines the directory to cache

foreign-survey-build-to-prod:
  stage: foreign_survey_build
  environment:
    name: production server
  only:
  - master    
  script:
    - echo 'foreign_survey is being builded to production server'
    - cd foreign_survey\client
    - call npm install
    - call npm run build
    - 'net use o: \\server_IP\source_code_path server_login_PW /user:server_login_ID'
    - 'xcopy dist\. o:\foreign_survey\client\dist\ /h /k /y /e /r'
  cache: # cache checks for its existence while running the job, and caches once more after the script finishes running.
    key: ${CI_COMMIT_REF_SLUG}
    paths:
    - foreign_survey/client/node_modules/
```

In the example above, the cache is stored based on the branch. But when there are multiple sub Node projects inside a single parent project and each builds at its own stage, storing the cache based on branch alone is useless. If you configure it as below, you can store the cache per stage & branch.

```yml
# ...
cache:
    key: "$CI_JOB_STAGE-$CI_COMMIT_REF_SLUG"
# ...
```

For details on how to share cache within the same branch, please refer to [here](https://docs.gitlab.com/ee/ci/caching/#sharing-caches-across-the-same-branch).

I sincerely hope you'll be freed from deployment hell!

The end!

[Gitlab-CI]: https://about.gitlab.com/features/gitlab-ci-cd/
[official Gitlab-CI docs]: https://docs.gitlab.com/ee/ci/README.html
[jenkins]: https://jenkins.io/
[Gitlab]: <https://gitlab.com>
