---
layout: post
title: "Building a basic Lightswitch application - Page 2"
date: 2011-05-31 22:55:45 +0200
comments: false
author: Nico Vermeir
sitemap: false
permalink: /building-a-basic-lightswitch-application-page-2.html
redirect_from:
 - /blog/page/Building-a-basic-Lightswitch-application-Page-2.aspx.html
 - /blog/page/building-a-basic-lightswitch-application-page-2.aspx.html
---
<p><span style="font-family: Times New Roman; font-size: small;"> </span><span style="font-size: small;"><span style="font-family: Calibri;">When you&rsquo;re in the Owner design screen, click &ldquo;Add relationship&rdquo;, with this function you can create foreign keys in a table. Change the settings so they match the screenshot. </span></span></p>
<p style="text-align: center;"><span style="font-family: Times New Roman; font-size: small;"> </span><br /> <a href="http://i56.tinypic.com/314sc9c.jpg" target="_blank"><img src="http://i56.tinypic.com/314sc9c.jpg" alt="initial screen" width="429" height="285" /></a><br />(click to enlarge)</p>
<p style="text-align: left;"><span style="font-family: Times New Roman; font-size: small;"> </span></p>
<p class="MsoNormal" style="margin: 0cm 0cm 10pt;"><span style="font-size: small;"><span style="font-family: Calibri;">This screen really makes relationships easy as it explains really well what you&rsquo;re relationship will do. Once you add it you will notice that a new column is created in the 2 tables. Next up is the Hero &ndash; City relation, open up the City design screen from the Solution explorer, click add new relationship and follow the screenshot below.</span></span></p>
<p style="text-align: center;"><span style="font-family: Times New Roman; font-size: small;"> </span><br /><a href="http://i53.tinypic.com/2mqvt05.jpg" target="_blank"><img src="http://i53.tinypic.com/2mqvt05.jpg" alt="initial screen" width="429" height="285" /></a><br />(click to enlarge)</p>
<p style="text-align: left;"><span style="font-family: Times New Roman; font-size: small;"> </span></p>
<p class="MsoNormal" style="margin: 0cm 0cm 10pt;"><span style="font-size: small;"><span style="font-family: Calibri;">So, that&rsquo;s quite enough for data, let&rsquo;s move on to creating the screens!</span></span></p>
<p style="text-align: left;"><span style="font-family: Times New Roman; font-size: small;"> </span></p>
<p class="MsoNormal" style="margin: 0cm 0cm 10pt;"><span style="font-size: small;"><span style="font-family: Calibri;">In the Solution Explorer, right click the Screens folder and click Add Screen. In this screen you can select a template and the data that will be shown in the screen. Select the List and Detail screen and the Hero table for data.</span></span></p>
<p style="text-align: center;"><span style="font-family: Times New Roman; font-size: small;"> </span><br /><a href="http://i55.tinypic.com/hukdcm.jpg" target="_blank"><img src="http://i55.tinypic.com/hukdcm.jpg" alt="initial screen" width="504" height="307" /></a><br />(click to enlarge)</p>
<p style="text-align: left;"><span style="font-family: Times New Roman; font-size: small;"> </span></p>
<p class="MsoNormal" style="margin: 0cm 0cm 10pt;"><span style="font-size: small;"><span style="font-family: Calibri;">At this point we have a runnable application, when you run it you will see a clean Silverlight interface that allows you to add superheroes and view an entire list. The only problem here is that a superhero requires an owner but we don&rsquo;t have the option to add owners yet, so let&rsquo;s fix this small problem and start building our superhero list!</span></span></p>
<p style="text-align: left;"><span style="font-family: Times New Roman; font-size: small;"> </span></p>
<p class="MsoNormal" style="margin: 0cm 0cm 10pt;"><span style="font-size: small;"><span style="font-family: Calibri;">Add a screen with a &ldquo;New Data Screen&rdquo; template and choose the Owner table like in the screenshot below.</span></span></p>
<p style="text-align: center;"><span style="font-family: Times New Roman; font-size: small;"> </span><br /><a href="http://i54.tinypic.com/2dl3ne1.jpg" target="_blank"><img src="http://i54.tinypic.com/2dl3ne1.jpg" alt="initial screen" width="504" height="307" /></a><br />(click to enlarge)</p>
<p style="text-align: left;"><span style="font-family: Times New Roman; font-size: small;"> </span></p>
<p class="MsoNormal" style="margin: 0cm 0cm 10pt;"><span style="mso-no-proof: yes;"><span style="font-size: small;"><span style="font-family: Calibri;">Now when we execute the application there&rsquo;s a new option on the left side for adding an owner, let&rsquo;s add one now. <span style="line-height: 115%; font-family: &quot;Calibri&quot;,&quot;sans-serif&quot;; font-size: 11pt; mso-ascii-theme-font: minor-latin; mso-fareast-font-family: Calibri; mso-fareast-theme-font: minor-latin; mso-hansi-theme-font: minor-latin; mso-bidi-font-family: &quot;Times New Roman&quot;; mso-bidi-theme-font: minor-bidi; mso-no-proof: yes; mso-ansi-language: EN-US; mso-fareast-language: EN-US; mso-bidi-language: AR-SA;">Just enter a name, I used DC Comics and Marvel Comics, and click the save button. </span></span></span></span></p>
<p style="text-align: center;"><span style="font-family: Times New Roman; font-size: small;"> </span><br /><a href="http://i55.tinypic.com/2ibkuc2.jpg" target="_blank"><img src="http://i55.tinypic.com/2ibkuc2.jpg" alt="initial screen" width="545" height="307" /></a><br />(click to enlarge)</p>
<p style="text-align: left;"><span style="font-family: Times New Roman; font-size: small;"> </span></p>
<p class="MsoNormal" style="margin: 0cm 0cm 10pt;"><span style="mso-no-proof: yes;"><span style="font-size: small;"><span style="font-family: Calibri;">Now when we go back to the hero list and click on the New button, the selection list will be filled with the owners we entered a minute ago.</span></span></span></p>
<p style="text-align: center;"><span style="font-family: Times New Roman; font-size: small;"> </span><br /><a href="http://i53.tinypic.com/2zqajad.jpg" target="_blank"><img src="http://i53.tinypic.com/2zqajad.jpg" alt="initial screen" width="545" height="307" /></a><br />(click to enlarge)</p>
<p style="text-align: left;"><span style="font-family: Times New Roman; font-size: small;"> </span></p>
<p class="MsoNormal" style="margin: 0cm 0cm 10pt;"><span style="font-size: small;"><span style="font-family: Calibri;">I entered the details of Superman here, as soon as the OK button is clicked the new record will be shown in the list and the details boxes will be filled as well.</span></span></p>
<p style="text-align: center;"><span style="font-family: Times New Roman; font-size: small;"> </span><br /><a href="http://i54.tinypic.com/2uyskzk.jpg" target="_blank"><img src="http://i54.tinypic.com/2uyskzk.jpg" alt="initial screen" width="545" height="307" /></a><br />(click to enlarge)</p>
<p style="text-align: center;">&nbsp;<a href="http://www.spikie.be/blog/page/Building-a-basic-Lightswitch-application.aspx">&lt;-- Previous Page</a>&nbsp; -&nbsp; <a href="http://www.spikie.be/blog/page/Building-a-basic-Lightswitch-application-Page-3.aspx">Next Page --&gt;</a></p>
{% include imported_disclaimer.html %}
