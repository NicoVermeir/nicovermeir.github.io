---
layout: post
title: "building a basic lightswitch application"
date: 2011-05-31 22:23:35 +0200
comments: false
author: Nico Vermeir
sitemap: false
permalink: /building a basic lightswitch application.html
redirect_from:
 - /blog/page/Building-a-basic-Lightswitch-application.aspx.html
 - /blog/page/building-a-basic-lightswitch-application.aspx.html
---
<p>&nbsp;</p>
<p class="MsoNormal" style="margin: 0cm 0cm 10pt;"><span style="font-size: small;"><span style="font-family: Calibri;">Microsoft LightSwitch is an IDE that can build business applications without writing any code. The screens are generated from the data that is used. In this small article I will walk you through creating a simple application.</span></span></p>
<p>&nbsp;</p>
<p class="MsoNormal" style="margin: 0cm 0cm 10pt;"><span style="font-size: small;"><span style="font-family: Calibri;">First some information about LightSwitch. LS builds 3-tier applications, the interface is build using Microsoft Silverlight in combination with WCF RIA services and Entity Framework, the default datastore for LS is SQL server express. LS also supports data from SQL Azure, MS Sharepoint and SQL server. As for language, support for Visual Basic and C# is build in. LS is currently in its second beta version, a full release is scheduled for end of 2011.</span></span></p>
<p>&nbsp;</p>
<p class="MsoNormal" style="margin: 0cm 0cm 10pt;"><span style="font-size: small;"><span style="font-family: Calibri;">I&rsquo;m going to create an application that lists superheroes, their owner, powers and city of operations. I will not use an existing database, so LS will use a newly created SQL server express DB.<br /> My development machine is running Windows 7 professional 64-bit with Visual Studio 2010 enterprise and sql server express 2008 R2.</span></span></p>
<p>&nbsp;</p>
<p class="MsoNormal" style="margin: 0cm 0cm 10pt;"><span style="font-size: small;"><span style="font-family: Calibri;">Naturally we kick this off by starting a new project in VS2010 so go to File &gt; New &gt; Project, select Lightswitch as projecttype and select a C# Lightswitch project. Give it a name and click OK.</span></span></p>
<p style="text-align: center;"><br /> <a href="http://i56.tinypic.com/122ddsh.jpg" target="_blank"><img style="margin-right: auto; margin-left: auto; display: block;" src="http://i56.tinypic.com/122ddsh.jpg" alt="project selection" width="545" height="307" /></a>(Click to enlarge)</p>
<p class="MsoNormal" style="margin: 0cm 0cm 10pt;"><span style="font-size: small;"><span style="font-family: Calibri;">After the project finishes loading we find ourselves in the very minimalistic interface that is Microsoft Lightswitch, we only have 2 options on the start screen, Create new table or Attach to external data source. These options speak for themselves, create new table creates a new sql server express database for this project, attach to external data source allows you to connect to a sql database, a sharepoint<span style="mso-spacerun: yes;">&nbsp; </span>database or a sql azure database. We will create a new database for our superhero collection, so select Create new table.</span></span></p>
<p style="text-align: center;"><br /><a href="http://i52.tinypic.com/zyhrhz.jpg" target="_blank"><img style="margin-right: auto; margin-left: auto; display: block;" src="http://i52.tinypic.com/zyhrhz.jpg" alt="initial screen" width="545" height="307" /></a>(click to enlarge)</p>
<p style="text-align: left;">&nbsp;</p>
<p class="MsoNormal" style="margin: 0cm 0cm 10pt;"><span style="font-size: small;"><span style="font-family: Calibri;">This opens up a graphical table design, start by naming the table and add columns like in the screenshot. In the properties of the ID column deselect the Display by default checkbox, this makes sure that the ID field isn&rsquo;t shown in the application.</span></span></p>
<p style="text-align: center;"><br /><a href="http://i56.tinypic.com/m7tlpj.jpg" target="_blank"><img src="http://i56.tinypic.com/m7tlpj.jpg" alt="initial screen" width="545" height="307" /></a><br /> (click to enlarge)</p>
<p>&nbsp;</p>
<p class="MsoNormal" style="margin: 0cm 0cm 10pt; text-align: left;"><span style="font-size: small;"><span style="font-family: Calibri;">Create 2 more tables, City and Owner, make them like the screenshots below.</span></span></p>
<p style="text-align: center;"><br /><a href="http://i53.tinypic.com/2daydxd.jpg" target="_blank"><img style="margin-right: auto; margin-left: auto; display: block;" src="http://i53.tinypic.com/2daydxd.jpg" alt="initial screen" width="545" height="307" /></a>(click to enlarge)</p>
<p style="text-align: center;"><a href="http://i56.tinypic.com/2ccrygg.jpg" target="_blank"><img style="margin-right: auto; margin-left: auto; display: block;" src="http://i56.tinypic.com/2ccrygg.jpg" alt="initial screen" width="545" height="307" /></a>(click to enlarge)</p>
<p style="text-align: center;"><a href="http://www.spikie.be/blog/page/Building-a-basic-Lightswitch-application-Page-2.aspx">Next Page --&gt;</a></p>
{% include imported_disclaimer.html %}
