---
layout: post
title: "So-called virtual environments in Julia"
date: 2020-08-15 22:52:01
categories: regular Julia
tags: regular Julia
image: /assets/article_images/2022-08-15-julia_environment/night-track.JPG
image2: /assets/article_images/2022-08-15-julia_environment/night-track-mobile.JPG
---

I have been using **Julia** since its 0.4 version, and this is the moment that I haver never had to use virtual environments (from **Python** point of view). However, few days ago running some demo codes I realised that if I wanted to follow the tutorial, I would need to create a virtual environment. With that in mind, in this post I am going to explain how to create a virtual environment and give some tricks at the end.

First I open **Julia** in a recently created folder (`env`). Thereafter, one press `]` to get access to the RELP interface of Julia's package manager. You will see in blue that the **Julia** version is 1.7 and that indeed the **pkg** is loaded.

![Open Julia in the folder env][pic1]

For commodity, leave the RELP interface by pressing the key `delete`. This should send you back the default interface. Use the package manager, then activate the virtual environment in the current folder, and check the status environment.
{% highlight julia %}
using Pkg;
Pkg.activate(".")
Pkg.status()
{% endhighlight %}

![Use Pkg and activate the environment][pic2]

You might remember that the folder where I opened **Julia** is called `env`. This is also going to be the name of the created environment. Of course, because this is a recently created environment, this does not contain any package yet. However, this is a prove that indeed the current environment is new and will not affect our default **Julia** installation. Let's install the package `"Example"` and check for what files have been created in the current directory `readdir()`.
{% highlight julia %}
Pkg.add("Example") ;
readdir()
{% endhighlight %}

![Add example and check current directory][pic3]

After `"Example"` has been added, two folders were created in the current directory, `Manifest.toml and Project.toml`. Basically those two files tell julia what packages can be used once the environment in the current folder is activated. Let's exit from **Julia** and open it again. If one does that and press `]` it will find that the environment is the default one, _i.e._ it shows that the used **Julia** version is 1.7. To show my point, activate the environment and check its status without leaving the `pkg` 's RELP.
{% highlight julia %}
activate .
status
{% endhighlight %}

![Pkg RELP activate and status][pic4]

Notice that the name of the environment passed from **@v1.7** to **env**

## Tricks

As shown in the last picture the environment is not loaded till once explicitly does it. Thus, if one wants to load the environment in the current directory just type: `julia --project=.`.

Hope this helps you.

[pic1]: /assets/article_images/2022-08-15-julia_environment/1.png "Default env"
[pic2]: /assets/article_images/2022-08-15-julia_environment/2.png "Activate env"
[pic3]: /assets/article_images/2022-08-15-julia_environment/3.png "Add package and check"
[pic4]: /assets/article_images/2022-08-15-julia_environment/4.png "Open env and check created env"
