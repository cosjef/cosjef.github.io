---
layout: post
title: "unnecessary third party SSL"
date: 2013-11-07 21:56
comments: true
categories: webpagetest
---


A common website performance mistake is to use SSL where its not needed. When building my first (this) Octopress site, I added Twitter and Github plugins to the right sidebar. This drop-in functionality gave me immediate benefit with almost zero work.  
  
I thought this was fantastic until I ran the site through [webpagetest][1]. While it loaded in a fast 1.9 seconds, I noticed a number of yellow lock indicators in the waterfall chart in several requests. These individual requests were being made over SSL. But why would you need SSL for an anonymous user on a website composed of static objects? 

Below is a screen capture from the webpagetest results for "repeat view" - a browser hitting the site with a primed cache. Note the purple sections that represent SSL negotiation duration and the sheer number of lock icons:
![][image-1]

Note the domain names in use for these requests:  
- Github's API  
- Twitters "twimg" domain
 
While these are important resources, they are simply API calls, and have no need to be encrypted. Lets clean them up inside Octopress to simply use http calls.
  
Open the /source/\_includes/asides/twitter.html file, and find the HTTPS calls:
 \<section\>
	 <section>
	     <h1>Twitter</h1>
	
	<a class="twitter-timeline"  href="https://twitter.com/cosjef"  data-widget-id="398072795696869376">Tweets by @cosjef</a>
	    <script>!function(d,s,id){var js,fjs=d.getElementsByTagName(s)[0],p=/^http:/.test(d.location)?'http':'https';if(!d.getElementById(id)){js=d.createElement(s);js.id=id;js.src=p+"://platform.twitter.com/widgets.js";fjs.parentNode.insertBefore(js,fjs);}}(document,"script","twitter-wjs");</script>
	</section>

Anywhere you see an https reference, change it to http instead. Now open the /source/_includes/asides/github.html file, and perform the same https-to-http edits:  

	{% if site.github_user %}
	<section>
	  <h1>GitHub Repos</h1>
	  <ul id="gh_repos">
	    <li class="loading">Status updating...</li>
	  </ul>
	  {% if site.github_show_profile_link %}
	  <a href="https://github.com/{{site.github_user}}">@{{site.github_user}}</a> on GitHub
	  {% endif %}
	  <script type="text/javascript">
	    $(document).ready(function(){
	        if (!window.jXHR){
	            var jxhr = document.createElement('script');
	            jxhr.type = 'text/javascript';
	            jxhr.src = '{{ root_url}}/javascripts/libs/jXHR.js';
	            var s = document.getElementsByTagName('script')[0];
	            s.parentNode.insertBefore(jxhr, s);
	        }
	
	        github.showRepos({
	            user: '{{site.github_user}}',
	            count: {{site.github_repo_count}},
	            skip_forks: {{site.github_skip_forks}},
	            target: '#gh_repos'
	        });
	    });
	  </script>
	  <script src="{{ root_url }}/javascripts/github.js" type="text/javascript"> </script>
	</section>
	{% endif %}
	

[1]:	http://www.webpagetest.org/result/131107_38_615/

[image-1]:	/images/2013-11-07_22-20-51.png