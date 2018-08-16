---
id: 1
title: 'Test First Post &#8211; Code Snippet'
date: 2017-02-03T08:38:50+00:00
author: Brett
layout: post
guid: http://millerb.co.uk/?p=1
permalink: /hello-world/
avada_post_views_count:
  - "4"
categories:
  - Uncategorized
---
My first post with the Syntax Highlighter working.

<pre class="prettyprint lang-powershell" data-start-line="1" data-visibility="visible" data-highlight="" data-caption="">#Add variables for old and new groups
#$OldGroup = "app-PowerProject-Users-3"
#$NewGroup = "app-SCCM SD-AstaPowerproject13.0.01x86"

#Manually enter old and new group names
$OldGroup = Read-Host "Enter Group to Transfer From"
$NewGroup = Read-Host "Enter Group to Transfer To"

#get all users of the $OldGroup & add these to a variable
$users = Get-ADGroupMember -Identity $OldGroup

#Measure how many users are going to transfer to $NewGroup
$Count = ($users).Count
Write-Output ""
Write-Host "$Count Objects will be transferred`n" -ForegroundColor Red

foreach ($user in $users) {
    Add-ADGroupMember -Identity $NewGroup -Member $user -Verbose
    }

Write-Host ""
Write-Host "Completed Successfully - Review any errors above"</pre>

Inline code with the blog post.

This should result in at least some content to place on my blog.

&nbsp;