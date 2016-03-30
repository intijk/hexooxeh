---
title: hexxoo
date: 2016-03-28 18:07:10
tags:
---
Installation:

	npm install hexo-cli -g
	npm install hexo-deployer-git --save	

Init:
	
	hexo init blog


First-time deploy:

	Append to _config.yml	

	-----------
	deploy:
  	  type: heroku
  	  repo: git@github.com:gitname/gitname.github.io.git
	-----------

	$ hexo generate; hexo deploy

Write:
	
	hexo new [layout] <title>

Publish:
	
	hexo generate; hexo deploy

Double repos for multi-devices posting:

	github:
	create hexo-src repo

	pc1
	--------------
	pc1-blog $ git init
	pc1-blog $ git add remote origin git@github.com:gitname/hexo-src.git
	pc1-blog $ git add .gitigore    #Yes, hexo provide such a one	
	pc1-blog $ git commit 
	pc1-blog $ git add . 
	pc1-blog $ git commit 
	pc1-blog $ git push origin master
	--------------
	
	pc2
	--------------
	$ hexo init pc2-blog
	$ git pull hexo-src
	$ cp -r hexo-src pc2-blog
	pc2-blog $ hexo generate; hexo deploy 

<font size="10">☯</font>
