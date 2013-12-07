---
layout: post
title: "set up git-p4 in windows"
date:   2013-12-04 19:43:09
comments : true
categories: git
---

  Recently ,our company is thinking of swithing the SCM tool and leverage GIT,why?
as git is hot ,distributed, easy to recover and proficient to branch and merge.
But the challenge is that ,we can not quicky switch to that as for CI(continous integration) ,rely on
the P4(our current SCM)

#Precondition
Install [mysisgit 1.8.3](https://code.google.com/p/msysgit/downloads/detail?name=Git-1.8.3-preview20130601.exe&can=2&q=) Note:do not install the 1.8.4 as it is still not stable right now
once ths installation is finished ,add GIT_HOME into you system path then run below in command line 

{% highlight ruby %}
git --version
git version 1.8.3.msysgit.0
{% endhighlight %}

Install [python 2.7](http://www.python.org/ftp/python/2.7.3/python-2.7.3.msi) ,git-p4 is a python script ,we need to install the python runtime env for this.
Note : do not use the latest 3.x version as latest python version change some syntax ,which can make the script running failed.
After the installation is success ,add python to the system path then run below in command line

{% highlight ruby %}
python --version
Python 2.7.3
{% endhighlight %}

Install [p4 command line tool](http://www.perforce.com/product/components/perforce-commandline-client) .This is used to allow git talk to P4
Note:if you already install P4 visual client ,then I dont need to install P4 command line tool as they are all packed together,just double check the P4 command
line tool is on the system path.
{% highlight ruby %}
p4
Perforce -- the Fast Software Configuration Management System.
{% endhighlight %}

Download [git-p4.py](https://github.com/git/git/blob/master/git-p4.py) into you Git installation directory ,for example ,if you install git under C:\Git,then put his under C:\Git\bin
after this step is down ,try below in command line
{% highlight ruby %}
git-p4.py
usage: C:/Git/bin/git-p4.py <command> [options]
valid commands: clone, rollback, debug, commit, rebase, branches, sync, submit
Try C:/Git/bin/git-p4.py <command> --help for command specific help.
Perforce -- the Fast Software Configuration Management System.
{% endhighlight %}

#How to use that.
Git will use P4 client command tool to communicate with Perforce ,to ensure this works ,we should add a NEW P4 Client which is specifically used by Git ,not your normal P4 client
For example ,you are using "jicui_L-SHC-00436301" as your perforce woking client,this client point to C:\dev as the root directory.To avoid any conflicting with Git and your normal 
dev work.Git require a clean and not conflicts working client ,say you have to create a new one for Git ,like "git-p4" and point to the root node different with your working client.
How to create a new P4 client? It's your matter to search google ,BUT make sure that ,the root node for that new created client should different with the Git working directory.

to ensure your can login to the new p4 client ,you should set up below on your system environment virables.
{% highlight ruby %}
p4CLIENT:{your new client name}
p4PORT;{p4 server url:1666}
{% endhighlight %}

After that ,use below to login into P4
{% highlight ruby %}
p4 login
{% endhighlight %}
It success ,it promopts to you for password input.

Untill then ,you are good to go for have your first git-p4 experiment.
>Step1:clone P4 reposiotry to Git directory
git-p4.py clone //{your peforce path} .For example if you want to clone your branch which on P4 is under path //depot/project/foo,then this is the path you need to
put in the {your perforce path}.

>Step2:modify any files under your git and commit to local repository ,for example as below
{% highlight ruby %}
change some file
git add .
git commit -a 
git-p4.py rebase //recommand to do so
git-py.py submit
{% endhighlight %}

Basically p4 will open an system default editor to view the change list and change summary,thus git-p4 will try to open you system default editor
if you open your git by MinGW or CygWin ,it will open vi as your system default editor ,to switch to another editor your prefer ,please run below.

{% highlight ruby %}
git config set core.editor="vim"
{% endhighlight %}

add vim to you system path

{% highlight ruby %}
echo $PATH

{% endhighlight %}

Till then, you are successfully set up the git-p4 env and in case you want to get more help on that go to [link](http://git-scm.com/docs/git-p4)
#Architecture overview   

<img src="/assets/git-p4.jpg" height="200px" width="300px" alt="AO"/>


