---
layout: post
title: "[Git] Tutorial 2 - Building github blog in 15 minutes"
date: 2018-07-14 18:00:00
author: Dojin Kim
categories: Git
tags: github git blog jekyll
cover:  "/assets/imgs/github/gitpage.png"
---

This is Tutorial 2: creating a github blog. **Those who do have same result with original style skip to Step 2 ** Those who have different result read from step 1 :)


**We have Problem in configuration **

<h2>Step 1: Go to _config.yml</h2>


1) Check that your branch is set to **master**<br>
<img src="{{ site.baseurl }}/assets/imgs/github/master.png"/>



2) Click edit button which is in top right corner(icon is pencil)<br>
3) Press Ctrl+F(Widnows), Command+F(Mac) and type "base" to find baseurl.<br>
4) Delete data of base_url.
- You can add other information such as description, title, tagline, and others but it is not necessary.<br>
<img src="{{ site.baseurl }}/assets/imgs/github/base.png"/>


5) GO down and click commit changes.
- Write what you committed or else automatically Update _config.yml will be written.
- Get used to writing what you committed, so that you won't forget what was changed.


<img src="{{ site.baseurl }}/assets/imgs/github/commit.png"/>
<br><br>

<h2>Step 2: Use Markdown Editor to make a post</h2>

I will show how to upload posts using markdown editor. I recommend **Typora** for Windows users & **Haroopad** for Mac users. Since I use Mac I will use Haroopad.<br>

1) Go to Haroopad website.<br>
2) Click Users<br>
<img src="{{ site.baseurl }}/assets/imgs/github/users.png"/>



3) Scroll down and click icon that represents your OS
<img src="{{ site.baseurl }}/assets/imgs/github/os.png"/>


4) If you install you will see following screen.
- Click a blue button that is on bottom right.It will show how you can add hyperlinks, make fonts bigger, emphasize and etc.
- For example) Writing ******Bing****** will make the word bold like this - **Bing**
<img src="{{ site.baseurl }}/assets/imgs/github/hpad.png"/>


**Congratulations!! You passed Tutorial 2!!** Now you will learn how to write your post in Tutorial 3. Thank you for following all instructions