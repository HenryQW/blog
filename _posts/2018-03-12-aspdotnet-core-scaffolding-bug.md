---
key: 20180312
modify_date: 2018-03-12
tags: [C#, MVC, English]
title: ASP.NET Core Scaffolding Bug for separated projects
---

A quick fix for ASP.NET Core Scaffolding Bug for separated DbContext & Model.

<!--more-->

---

Project structure:
{% highlight raw%}
├── Solution
│   └── BL
│   │   ├── Database
│   │   └── DbContext
│   └──Core
│   │   └── Models
│   │       ├──ApplicationInfo
│   │       ├── VersionInfo
│   │       └── ApplicationInfo
│   └── WebApp
│   │   └── Controllers
│   │       └── ApplicationInfoesController
│ …
{% endhighlight %}

When `DbContext` and `Models` are placed in separated projects from the main `WebApplication`, like the one above, Scaffolding will prompt the following error:

![Scaffolding error](/assets/images/2018-03-12/error.png)

This issue is being addressed and [the patch](https://github.com/aspnet/Scaffolding/pull/676){:target="_blank"} is releasing soon (hopefully) in Scaffolding v2.0.3.

Meanwhile, a quick fix is to supply full qualifier name into `DbSet`:
{% highlight C# %}
// This will not work
public DbSet<VersionInfo> VersionInfos { get; set; }

// Full model qualifiers must be used in order for Scaffolding to succeed
public DbSet<Core.Models.DbModels.ApplicationInfo> ApplicationInfos { get; set; }
{% endhighlight %}
