---
title: "How to Survive a Laptop Fire"
date: 2021-05-01T18:54:31+08:00
draft: false
---

How would you go about saving your work if your laptop burned in a fire?

![](/images/fire-burning-laptop-flame.jpg)

I've developed a cloud-centric workflow that allows me to work more or less seamlessly across multiple projects, computers, and operating systems over the last few years. This post discusses the principles, software, and services that I use to make that happen.

When I am advocating about working in this manner, I frequently ask people, "How bad would it be for your work if your laptop burned in a fire?" &mdash; Alot of the responses I get tend to focus on personal data - ‘Well I do backup on Dropbox!’, ‘I use Google Drive’, etc. But I argue personal data loss is really just part of the pain you'd experience. What about your Applications? Software libraries? Just how hard would it be to recover a working environment that would allow you to carry on?

It is very much important to be able to resume anywhere you left off and to be able to treat your personal devices as just a "shell" for your working environment. After all, devices are tools that help you get things done, not slow things down.

## Survival skills

### Set up a cross-platform workflow

Although many people talk about the fact that optimization for specific operating systems is better for usability, using cross-platform apps or setting up a non OS-dependent ecosystem is essential so that it is convenient later on. Using cross-platform apps also allows you to easily switch from one platform to another without even needing to re-learn specific paradigms and how things work differently on different platforms.

These days, it is easy to get a cross-platform workflow ecosystem going. Developing cross-platform applications are much easier with the rise of [Electron](https://www.electronjs.org/) and other cross-platform GUI libraries/frameworks.

For instance, instead of just sticking to programs like Microsoft Word or Visual Studio, you can try other alternatives, like [Visual Studio Code](https://code.visualstudio.com) or [IntelliJ IDEA](https://www.jetbrains.com/idea/). Using cross-platform programs has other advantages too, which brings me to my second tip:

### Use free software when possible

Apart from the obvious benefit of not costing anything, free software has the added advantage that you’ll always be able to access it. It’s pretty common to get access to proprietary stuff through one’s workplace, but I don’t enjoy the idea that I could lose access to a tool I depend on in any circumstance.

Another great benefit of free software is others can freely reuse and reproduce your work if you release it publicly, which allows you to modify programs to fit your needs.

### Use cloud tools

The only local software I absolutely need is Chromium or Chrome. This gives me quite a lot of freedom in terms of devices I can do work from. This is because: * Bitwarden manages all my passwords * I use Gmail's web interface for school/personal email * I use Markdown for documents and sync them via a Git repository hosted on Gitea at home * I store all my photos in Nextcloud * I stream my music from Spotify * I store all my data and files in Nextcloud or Syncthing * I host all my projects on GitHub * I have my blog on Github, allowing me to use Git to synchronize files even when developing on Windows, Mac and Linux.

This is important as it allows me to work seamlessly across devices without having to worry about data loss or things of those sorts. It's just something that you can slowly integrate into your workflow over time without having to think about anything. You can even have ease of mind by hosting your own "cloud" tools and apps. Things like Gitea, Gitlab Community, Nextcloud, ownCloud, Resilio Sync, Syncthing, Bitwarden, etc. can be leveraged by self-hosting them in your home. This means that your data is only owned by you, and you wouldn't have to depend on a third party to manage it. As these tools are open source, privacy isn't a big issue too, because people are contributing to the codebase of these projects every single day and are patching vulnerabilities as soon as they are found.

### Keep your local filesystem disposable

What do I mean by "disposable"? It means that your files on your local drive are just temporary versions of the ones on the "cloud". Everything on my local drive is disposable. Keeping the system disposable is closely linked with keeping it simple. Simplicity is also ensured by only using software that can be installed using a package manager. This means zero manual configuration for most of my applications. There are a handful of helpful bash scripts hosted on GitHub that I clone to ~/bin. I have configuration for my desktop setup and my text editor hosted on GitHub as well. The configuration files for them are known as "dotfiles", and they can be easily stored in a Git repo and version-controlled with GitHub as the storage which hosts the dotfiles. I track my dotfiles with a small little tool called `yadm`, and it has been just seamless with my workflow. All it does is it creates a bare git repo in your home folder, and uses git to track changes in those files. It is basically just a thin wrapper around git. This is the preferred way because you wouldn't want to just have your home folder as your git repo because it would conflict with having project files in other subfolders.

So getting a new system ready to be productive is a case of installing some software using the package manager and cloning one single GitHub repo. I have a script set up in the repo that automatically sets up my system on Linux, which is just awesome.

### Use Github for all projects

GitHub is a place to host version controlled projects. It’s not the only choice, other popular choices are GitLab and BitBucket. The main reason to choose GitHub is that everyone else is there, so it’s where collaboration happens.

I use ‘project’ pretty loosely. Things I keep there inlcude: * Data analysis documents * Software projects * Slides for talks (when using markdown) * Learning/Study repositories of notes and examples.

There’s this kind of ritual I go through now when I decide to do some new work where I create and name the GitHub repo. It’s become a habit to the point where not doing this creates mild anxiety.

One point I should make: I commit my work frequently in small increments. In a regular work day I’ll push to Github a couple of times. This is important for the ‘version control’ aspect, which although not strictly required for laptop fire survival, is pretty damn handy.

### Keep data in the cloud

This one is self explanatory&mdash;keeping your data in the cloud will ensure that your data is not lost when you switch devices, etc. I have a self-hosted Nextcloud instance in my home that contains literally **everything**.

For files that I need synced across devices as soon as they are updated, I use Syncthing, which is a cross-platform, decentralized file synchronization program.

## Associated Pros and Cons

### + Work from anything anywhere

Occasionally when stuck without a computer I have been able to get critical tasks done with makeshift workstations. I can make quick edits on the go or fix typos in my documents no matter where I am, even without an internet connection. I also use [GitJournal](https://gitjournal.io/) which allows me to sync my notes repository on my phone, allowing me to take quick notes on the go and pen down ideas as soon as I discover them.

### + Collaborate easily

Having everything online has been amazing for opening up collaboration avenues. I can always immediately provide someone with a link to anything I’ve produced. Sometimes down to a specific line of code that implements the subject of discussion. Since I work in public GitHub repositories as much as possible, I occasionally get interest from interesting people I would never have encountered otherwise.

### - Dependent on internet access

A big glaring flaw with this setup is that while I’m immune to fire, my kryptonite is now lack of internet access. This issue can be solved however, as git-based synchronization programs work offline and you can continue jotting down your ideas on the go even without an internet connection, and sync them whenever you can. With a setup like this, I'd strongly recommend using self-hosted services or GitHub so that you don't need to depend on an online editor just to edit your files.

### - Security risk

The other thing that may be giving some of you hives is the security risk of having your life’s work online. I take this pretty seriously. I have Bitwarden generate strong random-string passwords and store them and I use Two-Factor Authentication (2FA) for my Google, Lastpass, and GitHub accounts. Everything is self-hosted, so as long as you don't stupidly give away your credentials, it will be just fine.

## Overall

Clearly I find on the balance, doing things this way is worthwile. It meshes nicely with other values I hold relating to openess and reproducibilty. Having a simple system like this also frees me from regular worry.

Thanks for reading this post! If there are any tips or hacks you have for making this system better, feel free to drop a comment below or hit me up at <https://raphtlw.rocks/contact>.

> Header Image Credit: https://line.17qq.com/articles/hobojddhz.html
