---
title: "How to Contribute to a Github Repository"
date: 2021-05-01T19:25:25+08:00
draft: false
---

A simple guide to contributing on Github

![](https://cdn-images-1.medium.com/max/800/0*wAfE99LmmAafMhTN)

> When you play the game of clones, you merge or you reset --hard.

Git it?

Anyway, if you want to contribute to a project and make it better, your help is always appreciated. Contributing to a project is also a good way to learn more about social coding on Github, new technologies and their ecosystems and how to make constructive, helpful bug reports, feature requests and the noblest of all contributions: a good, clean pull request.

## Making good and clean pull requests

1.  Fork the repository to your personal Github. Forking is basically an easy way to make a duplicate of the repository to your own account. You need to do this so that you don't modify the *original* repository.
2.  Now that you have forked the repository to your account, go to the repository in your account, then click on the clone button and hit the *copy to clipboard* icon.
3.  Now, open a terminal on your local machine and clone the forked repository by doing: `git clone <copied url>`
4.  For example: `git clone <https://github.com/raphtlw/Checkra1nBot.git>`
5.  Create a branch in git inside your local machine: `git checkout -b <branch-name>`. You can name this anything you want. Just make sure there are no spaces.
6.  Now you can fix bugs or do whatever you need to do in order to improve the code in the project. Follow the code style of the project, including indentation. If the project has tests run them! Write or adapt tests as needed. Add or change the documentation as needed. Remember to make sure the changes are applied only to the branch you created!
7.  If exists, open `Contributors.md` in a text editor and add your name to it. Don’t add it at the beginning or the end of the file, add it anywhere in the middle of it. Now, save the file.
8.  Commit those changes by doing `git commit -m "Added <your-name> to contributors list` “-m” tells git that you are committing the changes with a message to tell people what the commit is about.
9.  Push those changes to the forked repository on Github via: `git push origin <your-branch-name>` replacing “<your-branch-name> with the name of the branch you created earlier.

## Submit your changes for review

If you go to your repository on GitHub, you’ll see a `Compare & pull request` button. Click on that button.

![](https://cdn-images-1.medium.com/max/800/1*0U8QYzrfyDD3CI7cW8dUrw.png)

Now, submit the pull request.

![](https://cdn-images-1.medium.com/max/800/1*Mx4jLqDLswJ2icyX49UTQw.png)

Congrats! You have successfully contributed to a Github repository. Now you’ll just have to wait for the repository owner/admins to accept your pull request.

Thanks for reading this guide!

> Header image by [Yancy Min](https://unsplash.com/@yancymin) on [Unsplash](https://unsplash.com)
