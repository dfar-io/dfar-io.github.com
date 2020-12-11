---
title: Git Repo Best Practices
author: dfar

date: 2019-04-21T17:37:42+00:00
excerpt: |
  
  <![CDATA[]]>
url: /git-repo-best-practices/
categories:
  - Uncategorized

---
When creating a repository for a project, here are a few best practices to keep everything in good working state. <h2>Forcing Pull Request Merges to master</h2> When creating a repo, it&#8217;s a good idea to keep the master branch deployable and clean at all times. A good way to do this is to not allow direct commits to the branch. In turn, if you have a CI/CD process in place, you can have more confidence that checking things into master can result in a deployment. In a system using trunk-based deployment, you may decide to automatically deploy all changes from master into production. You can do this in Github with the following steps: Go to the repo, and go to &#8216;Settings&#8217;. Click on &#8216;Branches&#8217;. Create the rule: <a href="http://40.76.37.251/wp-content/uploads/2019/04/2019-04-21-13\_32\_26-New-branch-protection-rule.png"><img class="alignnone wp-image-360" src="http://40.76.37.251/wp-content/uploads/2019/04/2019-04-21-13\_32\_26-New-branch-protection-rule.png" alt="" width="617" height="556" /></a> Once this is done, if you try to commit directly to master, you&#8217;ll see the following: <a href="http://40.76.37.251/wp-content/uploads/2019/04/2019-04-21-13\_33\_25-.png"><img class="alignnone wp-image-361" src="http://40.76.37.251/wp-content/uploads/2019/04/2019-04-21-13\_33\_25-.png" alt="" width="675" height="216" /></a>