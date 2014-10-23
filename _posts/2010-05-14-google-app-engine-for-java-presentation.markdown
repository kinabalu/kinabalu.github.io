---
layout: post
status: publish
published: true
title: Google App Engine for Java presentation
author:
  display_name: kinabalu
  login: kinabalu
  email: andrew@mysticcoders.com
  url: http://www.mysticcoders.com
author_login: kinabalu
author_email: andrew@mysticcoders.com
author_url: http://www.mysticcoders.com
wordpress_id: 1413
wordpress_url: http://www.mysticcoders.com/?p=1413
date: '2010-05-14 20:45:25 +0000'
date_gmt: '2010-05-15 03:45:25 +0000'
categories:
- Mystic
tags: []
comments: []
---
I gave a talk last night at the <a href="http://ocjug.org" target="_blank">Orange County Java User's Group (OCJUG)</a> on Google App Engine development with the Java platform.  This was the same one we gave at the <a href="http://scandevconf.se" target="_blank">Scandinavian Developer Conference</a> in March.  The whole talk stemmed from a good friend <a href="http://www.eugeneciurana.com" target="_blank">Eugene Ciurana</a> being unable to attend, so I adapted his slides and did the talk.  

One of the aspects I added was a demo showing how to deploy a <a href="http://wicket.apache.org" target="_blank">Apache Wicket</a> based web application to the dev server and then ultimately up to Google App Engine's servers.  In addition, the demo rather than show off the easy-to-use Eclipse plugin opts for using the <a href="http://code.google.com/appengine/downloads.html" target="_blank">App Engine SDK</a> along with Maven to build.  And we also use <a href="http://code.google.com/p/simpleds/" target="_blank">simpleds</a> for persistence in the demo just to show an alternative to the JDO / JPA offerings that Google provides on top of Datastore.

With that, here is a snippet of the Maven pom.xml to grab all the required dependencies:

<pre lang="xml" colla="+">
                <dependency><br />
                    <groupId>org.extrema-sistemas</groupId><br />
                    <artifactId>simpleds</artifactId><br />
                    <version>0.9</version><br />
                </dependency>

                <dependency><br />
                    <groupId>com.google.appengine</groupId><br />
                    <artifactId>appengine-api-1.0-sdk</artifactId><br />
                    <version>1.3.1</version><br />
                </dependency>

                <dependency><br />
                    <groupId>asm</groupId><br />
                    <artifactId>asm</artifactId><br />
                    <version>3.1</version><br />
                </dependency>

                <dependency><br />
                    <groupId>org.apache.geronimo.specs</groupId><br />
                    <artifactId>geronimo-jta_1.1_spec</artifactId><br />
                    <version>1.1.1</version><br />
                </dependency>

                <dependency><br />
                    <groupId>javax.jdo</groupId><br />
                    <artifactId>jdo2-api</artifactId><br />
                    <version>2.3-eb</version><br />
                </dependency>

                <dependency><br />
                    <groupId>org.apache.geronimo.specs</groupId><br />
                    <artifactId>geronimo-jpa_3.0_spec</artifactId><br />
                    <version>1.1.1</version><br />
                </dependency>

                <dependency><br />
                    <groupId>org.datanucleus</groupId><br />
                    <artifactId>datanucleus-jpa</artifactId><br />
                    <version>1.1.5</version><br />
                </dependency>

                <dependency><br />
                    <groupId>org.datanucleus</groupId><br />
                    <artifactId>datanucleus-enhancer</artifactId><br />
                    <version>1.1.4</version><br />
                </dependency>

                <dependency><br />
                    <groupId>org.datanucleus</groupId><br />
                    <artifactId>datanucleus-core</artifactId><br />
                    <version>1.1.5</version><br />
                </dependency>

                <dependency><br />
                    <groupId>com.google.appengine.orm</groupId><br />
                    <artifactId>datanucleus-appengine</artifactId><br />
                    <version>1.0.5</version><br />
                </dependency><br />
</pre>

Your mileage certainly may vary on versions, but as of this writing, slamming that into your pom.xml should get you cracking with simpleds for Google App Engine.

And in case you're interested, here's the presentation from last night and March's talk at Scandev.

<a href="http://www.mysticcoders.com/wp-content/uploads/2010/05/Google-App-Engine-Java-HOWTO.pdf"><img src="http://www.mysticcoders.com/wp-includes/images/crystal/document.png" border="0" /></a>&nbsp;-&nbsp;<a href="http://www.mysticcoders.com/wp-content/uploads/2010/05/Google-App-Engine-Java-HOWTO.pdf">Google App Engine for Java</a>

Enjoy!

