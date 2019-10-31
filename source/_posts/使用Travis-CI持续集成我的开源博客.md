---
title: 使用Travis CI持续集成我的开源博客
date: 2019-10-28 17:22:35
tags: [CI]
---

## 学习笔记 : 使用 Travis CI 持续集成我的 Hexo 开源博客


### Travis CI for beginner
> The following content from : https://docs.travis-ci.com/user/for-beginners/

#### What Is Continuous Integration (CI)?
Continuous Integration is the practice of merging in small code changes
frequently - rather than merging in a large change at the end of a development
cycle. The goal is to build healthier software by developing and testing in smaller
increments. This is where Travis CI comes in.

As a continuous integration platform, Travis CI supports your development
process by automatically building and testing code changes, providing immediate
feedback on the success of the change. Travis CI can also automate other parts
of your development process by managing deployments and notifications.  


#### CI Builds and Automation: Building, Testing, Deploying
When you run a build, Travis CI clones your GitHub repository into a brand-new
virtual environment, and carries out a series of tasks to build and test your
code. If one or more of those tasks fail, the build is considered
[*broken*](#breaking-the-build). If none of the tasks fail, the build is
considered [*passed*](#breaking-the-build) and Travis CI can deploy your code
to a web server or application host.

CI builds can also automate other parts of your delivery workflow. This means
you can have jobs depend on each other with [Build Stages](/user/build-stages/),
set up [notifications](/user/notifications/), prepare
[deployments](/user/deployment/) after builds and many other tasks.


#### Builds, Jobs, Stages and Phases
In the Travis CI documentation, some common words have specific meanings:

* *phase* - the [sequential steps](/user/job-lifecycle/)
  of a job. For example, the `install` phase, comes before the `script` phase,
  which comes before the optional `deploy` phase.
* *job* - an automated process that clones your repository into a virtual
  environment and then carries out a series of *phases* such as compiling your
  code, running tests, etc. A job fails, if the return code of the `script` *phase*
  is non-zero.
* *build* - a group of *jobs*. For example, a build might have two *jobs*, each
  of which tests a project with a different version of a programming language.
  A *build* finishes when all of its jobs are finished.
* *stage* - a group of *jobs* that run in parallel as part of a sequential build
  process composed of multiple [stages](/user/build-stages/).


#### Breaking the Build
The build is considered *broken*, when one or more of its jobs complete with a
state that is not *passed*:

 * *errored* - a command in the `before_install`, `install`, or `before_script`
   phase returned a non-zero exit code. The job stops immediately.
 * *failed* - a command in the `script` phase returned a non-zero exit code. The
   job continues to run until it completes.
 * *canceled* - a user cancels the job before it completes.

Our [Common Builds Problems](/user/common-build-problems/) page is a good place
to start troubleshooting why your build is broken.


#### Infrastructure and Environment Notes
Travis CI offers a few different infrastructure environments, so you can select
the setup that suits your project best:

* *Ubuntu Linux* - these Linux Ubuntu environments run inside full virtual machines, provide plenty of computational resources, and support the use of `sudo`, `setuid`, and `setgid`. Check out more information on the [Ubuntu Linux Build Environment](/user/reference/linux/).
* *macOS* - uses one of several versions of the macOS operating system. This environment is useful for building projects that require the macOS software, such as projects written in Swift. It is not a requirement to use the macOS environment, if you develop on a macOS machine. Here you can find more details on the [macOS Build Environment](/user/reference/osx/).
* *Windows* - currently Windows Server version 1803 is supported. If you want to know more about it, see the [Windows Build Environment](/user/reference/windows/).

More details on our build environments are available in our [CI Environment](/user/ci-environment/) documentation.


> 🚀 Now that you've read the basics, head over to our [Tutorial](/user/tutorial/) for details on setting up your first build!  Travis CI User Documentaiton : https://docs.travis-ci.com/ and the open source address of docs: https://github.com/travis-ci/docs-travis-ci-com



### Continuous Ingration with my Hexo blog
⏳ *时间如梭~ 自己现在都已经大三了. 大二时我才开始拥抱 GitHub，并使用 Hexo 加 Github 搭建了一个属于自己的静态博客网站，继而从此养成了写编程学习笔记的好习惯. 此项目包含了我大一到大三所有的编程学习笔记 : 真心希望自己的这些日常学习笔记，心得，及项目能够帮助一些同学提高学习效率！往后无论是读研，还是踏入社会后，我都会将她视为伴我成长的伙伴，一直维护着她，这一切的一切都是为了能够写出更加优美的代码哟~*

![ ](使用Travis-CI持续集成我的开源博客\Travis-CI-MyBlogWebsite-1.PNG)

![ ](使用Travis-CI持续集成我的开源博客\Travis-CI-MyBlogWebsite-2.PNG)



### reference
* [Travis CI 持续集成 GitHub 个人博客](https://www.jianshu.com/p/9d056d5ba78e)
* [持续集成服务 Travis CI 教程](http://www.ruanyifeng.com/blog/2017/12/travis_ci_tutorial.html)
* [使用 Travis CI 自动部署 Hexo 博客](https://www.bilibili.com/read/cv2884489)
