---
layout: post
title: "sql server ce is a life savior"
date: 2011-05-16 09:53:00 +0200
comments: true
published: true
categories: ["post"]
tags: [".Net", "Web development"]
alias: ["/blog/post/2011/05/16/SQL-server-CE-is-a-life-savior.aspx", "/blog/post/2011/05/16/sql-server-ce-is-a-life-savior.aspx"]
author: Nico Vermeir
redirect_from:
 - /blog/post/2011/05/16/SQL-server-CE-is-a-life-savior.aspx
 - /blog/post/2011/05/16/sql-server-ce-is-a-life-savior.aspx
---
<p><span style="text-decoration: underline;"><strong>Update</strong></span></p>
<p>So the conversion to MVC3 is about done, I've uploaded the first testversion to <a href="http://www.huizedegraef.be/test">http://www.huizedegraef.be/test</a> and so far it's working fine.<br />The next step is to get the admin panel up and running, stay tuned!</p>
<p><span style="text-decoration: underline;"><strong>Original</strong></span></p>
<p>So the conversion of the Huize De Graef project from asp.net webforms to MVC3 is going really well.<br />A first testversion ran fine on my local test server using the trust configuration of the webhost (thank you SoHosted for providing the file) but unfortunatly once uploaded Entity Framework wouldn't connect to the MySQL DB, always giving security errors.</p>
<p>After spending 2 days researching and mailing with the SoHosted support team still no solution. At Techdays 2011 I heard something about SQL Server compact edition, a Microsoft SQL solution that didn't require an installation. A quick test indeed proved that MVC3 apps using EF and SQL CE work perfectly on the webhost.</p>
<p>So currently I'm converting the MySQL to SQL CE, I'm hoping to have it online by the end of the week.</p>
{% include imported_disclaimer.html %}
