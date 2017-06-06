---
layout: post
title: "building a basic lightswitch application - page 3"
date: 2011-05-31 23:12:39 +0200
comments: false
author: Nico Vermeir
sitemap: false
permalink: /building a basic lightswitch application - page 3.html
redirect_from:
 - /blog/page/Building-a-basic-Lightswitch-application-Page-3.aspx.html
 - /blog/page/building-a-basic-lightswitch-application-page-3.aspx.html
---
<p>&nbsp;</p>
<p class="MsoNormal" style="margin: 0cm 0cm 10pt;"><span style="font-size: small;"><span style="font-family: Calibri;">Notice that the list only shows the firstname of the hero, I don&rsquo;t really like that, I want the hero name to be shown there, let&rsquo;s fix it.</span></span></p>
<p>&nbsp;</p>
<p class="MsoNormal" style="margin: 0cm 0cm 10pt;"><span style="font-size: small;"><span style="font-family: Calibri;">When the application is running in debug mode you will see a &ldquo;Design screen&rdquo; button in the upper right corner, when you click it, the application will pause and go in design mode, here we will change the list lay-out, but first a small theory lesson.</span></span></p>
<p>&nbsp;</p>
<p class="MsoNormal" style="margin: 0cm 0cm 10pt;"><span style="font-size: small;"><span style="font-family: Calibri;">Lightswitch applications are build in Silverlight, a Silverlight UI is build using XAML and the power of XAML lies partly in nesting controls and that is exactly what we have here. In the List and Details screen we have a Grid with 2 columns, the left column contains a list and the list contains a summary item. The summary item is the firstname that we saw when running the application, we will switch it to a column lay-out.</span></span></p>
<p style="text-align: center;"><br /><a href="http://i54.tinypic.com/2d9rz1l.jpg" target="_blank"><img src="http://i54.tinypic.com/2d9rz1l.jpg" alt="initial screen" width="205" height="307" /></a><br />(click to enlarge)</p>
<p style="text-align: left;">&nbsp;</p>
<p class="MsoNormal" style="margin: 0cm 0cm 10pt;"><span style="font-size: small;"><span style="font-family: Calibri;">So when we&rsquo;re in the design mode, click on the small arrow of the summary item and select Columns layout. Notice that now all fields are shown, so delete all unnecessary columns and click the save button in the upper right corner.</span></span></p>
<p style="text-align: center;"><br /><a href="http://i51.tinypic.com/2lw7m84.jpg" target="_blank"><img src="http://i51.tinypic.com/2lw7m84.jpg" alt="initial screen" width="205" height="307" /></a><br />(click to enlarge)</p>
<p style="text-align: left;">&nbsp;</p>
<p class="MsoNormal" style="margin: 0cm 0cm 10pt;"><span style="font-size: small;"><span style="font-family: Calibri;">Behold the result:</span></span></p>
<p style="text-align: center;"><br /><a href="http://i56.tinypic.com/55ghfm.jpg" target="_blank"><img src="http://i56.tinypic.com/55ghfm.jpg" alt="initial screen" width="545" height="307" /></a><br />(click to enlarge)</p>
<p style="text-align: left;">&nbsp;</p>
<p class="MsoNormal" style="margin: 0cm 0cm 10pt;"><span style="font-size: small;"><span style="font-family: Calibri;">All right, sit back take a deep breath and relax. Let&rsquo;s look back at what we&rsquo;ve done in this short time, we&rsquo;ve created a database with relationships, build a data driven application, added a UI that allows us to view, edit and add data.<br /> All this, without writing any piece of code and that right there is the power of Microsoft Lightswitch.</span></span></p>
<p style="text-align: left;">&nbsp;</p>
<p class="MsoNormal" style="margin: 0cm 0cm 10pt;"><span style="font-size: small;"><span style="font-family: Calibri;">What we&rsquo;ve done here is barely scratching the surface of Lightswitch. You can dive into the code and extend it, write your own screen templates, add calculated fields, add data validation and so on. The possibilities are huge. Will this ever be used to create business size applications? I don&rsquo;t now, future will tell, but what I can tell you is that it will never replace a competent developer, applications the size of those that I deal with daily can never be designed through a graphical environment like Lightswitch. So have no fear fellow developers world-wide, our services will always be needed </span><span style="font-family: Wingdings; mso-ascii-font-family: Calibri; mso-ascii-theme-font: minor-latin; mso-hansi-font-family: Calibri; mso-hansi-theme-font: minor-latin; mso-char-type: symbol; mso-symbol-font-family: Wingdings;"><span style="mso-char-type: symbol; mso-symbol-font-family: Wingdings;">J</span></span><span style="font-family: Calibri;">.</span></span></p>
<p style="text-align: left;">&nbsp;</p>
<p class="MsoNormal" style="margin: 0cm 0cm 10pt;"><span style="font-size: small;"><span style="font-family: Calibri;">I hope you enjoyed this very basic explanation of Microsoft Lightswitch, please note that this is still beta software and as such does not represent the final product. As a final exercise, please try to add the city logic to the application yourself so our heroes can have cities to defend. You can find the source code, including the city logic, attached to this article.</span></span></p>
<p style="text-align: left;">&nbsp;</p>
<p class="MsoNormal" style="margin: 0cm 0cm 10pt;"><span style="font-size: small;"><span style="font-family: Calibri;">If you have any questions, please leave a comment! </span></span></p>
<p class="MsoNormal" style="margin: 0cm 0cm 10pt;"><span style="font-size: small;"><span style="font-family: Calibri;"><a href="http://cid-5a345bb15e973473.office.live.com/browse.aspx/Demos" target="_blank">Download source code</a></span></span></p>
<p style="text-align: center;"><span style="font-family: Times New Roman; font-size: small;"><a href="http://www.spikie.be/blog/page/Building-a-basic-Lightswitch-application-Page-2.aspx">&nbsp;&lt;-- Previous Page</a></span></p>
{% include imported_disclaimer.html %}
