---
layout: post
status: publish
published: true
title: Apache Wicket Clustering With Multiple Options
author:
  display_name: kinabalu
  login: kinabalu
  email: andrew@mysticcoders.com
  url: http://www.mysticcoders.com
author_login: kinabalu
author_email: andrew@mysticcoders.com
author_url: http://www.mysticcoders.com
excerpt: |
  We've been seeing more and more implementations of <a href="http:&#47;&#47;wicketbyexample.com&#47;api&#47;wicket&#47;1.4.5&#47;org&#47;apache&#47;wicket&#47;protocol&#47;http&#47;SecondLevelCacheSessionStore.IPageStore.html" target="_blank">IPageStore<&#47;a> out in the wild for <a href="http:&#47;&#47;wicket.apache.org&#47;" target="_blank">Apache Wicket<&#47;a>.  The interface basically decides how Wicket will store the <a href="http:&#47;&#47;cwiki.apache.org&#47;WICKET&#47;page-maps.html" target="_blank">Pagemap<&#47;a> for your application.  The default that ships with Wicket uses <a href="http:&#47;&#47;wicketbyexample.com&#47;api&#47;wicket&#47;1.4.6&#47;org&#47;apache&#47;wicket&#47;protocol&#47;http&#47;pagestore&#47;DiskPageStore.html" target="_blank">DiskPageStore<&#47;a> which is an implementation that stores the serialized pages grouped in a single file per pagemap. After reading a wonderful blog post on <a href="http:&#47;&#47;letsgetdugg.com" target="_blank">Letsgetdugg<&#47;a> a few days ago: <a href="http:&#47;&#47;letsgetdugg.com&#47;2010&#47;02&#47;07&#47;clustering-wicket-for-fun-and-profit&#47;" target="_blank">Clustering Wicket for fun and profit!<&#47;a>, I decided to take a look at writing an implementation using <a href="http:&#47;&#47;hazelcast.com" target="_blank">Hazelcast<&#47;a> - an "open source clustering and highly scalable data distribution platform".
wordpress_id: 1726
wordpress_url: http://wicketbyexample.com/?p=215
date: '2010-02-10 22:23:35 +0000'
date_gmt: '2010-02-11 05:23:35 +0000'
categories:
- Apache Wicket
tags: []
comments:
- id: 3509
  author: victori
  author_email: victori@fabulously40.com
  author_url: http://letsgetdugg.com/
  date: '2010-02-13 14:22:10 +0000'
  date_gmt: '2010-02-13 21:22:10 +0000'
  content: There is a bit more to it. The reason my pagestore is so simple is the
    fact I offload the complexity to memcached. Memcached prematurely expires older
    caches automatically when it needs room for more space. Last I checked I don't
    know of any Java caching implementation that expires caches prematurely like memcached.
- id: 3510
  author: kinabalu
  author_email: andrew@mysticcoders.com
  author_url: http://www.mysticcoders.com
  date: '2010-02-13 15:29:28 +0000'
  date_gmt: '2010-02-13 22:29:28 +0000'
  content: I believe Hazelcast will handle that for you automatically, you can set
    up different caching structures in your Hazelcast configuration so that older
    caches get expired, and also set a max so once it reaches a certain point if you
    haven't got a persistence mechanism, it'll drop the oldest.
- id: 3511
  author: phil
  author_email: pvdyck@operamail.com
  author_url: ''
  date: '2010-02-24 04:17:34 +0000'
  date_gmt: '2010-02-24 11:17:34 +0000'
  content: |-
    FYI, I think you should add ":" to the sessionId test in unbind :
      public void unbind(final String sessionId) {
            IMap map = client.getMap(mapName);
            for (String key : map.keySet()) {
                if (key.startsWith(sessionId+":")) {
                    map.remove(key);
                }
            }
        }

    And maybe you should define a constant named DELIMITER... and hope that it never happens elsewhere...
- id: 3512
  author: Martin Grotzke
  author_email: martin.grotzke@javakaffee.de
  author_url: http://www.javakaffee.de/blog/
  date: '2010-02-27 17:32:57 +0000'
  date_gmt: '2010-02-28 00:32:57 +0000'
  content: |-
    I created the memcached-session-manager ([1]) which is a session manager for tomcat that replicates sessions to memcached nodes for session failover.

    There are different serialization strategies available ([2]), besides default java serialization I wrote an xml based serialization (using javolution) which has the advantage, that it supports different class versions (useful for a new deployment of your application).

    Right now I'm implementing a feature so that sessions are only replicated if they are modified so that readonly requests don't cause a session replication.

    I created the memcached-session-manager for a wicket application that needs to be highly scalable, and I created a very simple wicket app for performance comparison of the different serialization strategies. I still need to extend this benchmark app (and add it to the github repo), but there's already a wiki page with a rough overview over the current results - with an older version of msm without the replicate-only-modified-sessions-feature ([3]).

    Cheers,
    Martin


    [1] http:&#47;&#47;code.google.com&#47;p&#47;memcached-session-manager&#47;
    [2] http:&#47;&#47;code.google.com&#47;p&#47;memcached-session-manager&#47;wiki&#47;SerializationStrategies
    [3] http:&#47;&#47;code.google.com&#47;p&#47;memcached-session-manager&#47;wiki&#47;Performance
- id: 3513
  author: Ondra
  author_email: ondra@dynawest.cz
  author_url: http://www.pohlidame.cz/
  date: '2010-05-06 20:21:12 +0000'
  date_gmt: '2010-05-07 03:21:12 +0000'
  content: |-
    Does that really work? I don't see any code for this [1]:

    """Note that the versionNumber and ajaxVersionNumber parameters may be -1.
        * If ajaxVersionNumber is -1 and versionNumber is specified, the page store must return the page with highest ajax version.
        * If both versionNumber and ajaxVersioNumber are -1, the pagestore must return last touched (saved) page version with given id. """

    Here is my untested implementation with Infinispan (JBoss Cache):
    http:&#47;&#47;ondra.zizka.cz&#47;stranky&#47;programovani&#47;java&#47;web&#47;wicket&#47;wicket-pagestore-jbosscache-infinispan-integration.texy
- id: 3514
  author: Ondra
  author_email: ondra@dynawest.cz
  author_url: http://www.pohlidame.cz/
  date: '2010-05-06 20:21:44 +0000'
  date_gmt: '2010-05-07 03:21:44 +0000'
  content: "[1] http:&#47;&#47;wicketbyexample.com&#47;api&#47;wicket&#47;1.4.5&#47;org&#47;apache&#47;wicket&#47;protocol&#47;http&#47;SecondLevelCacheSessionStore.IPageStore.html#storePage%28java.lang.String,%20org.apache.wicket.Page%29"
- id: 3515
  author: Dmitry
  author_email: nskmda@mailinator.com
  author_url: ''
  date: '2010-05-26 13:19:36 +0000'
  date_gmt: '2010-05-26 20:19:36 +0000'
  content: |-
    Hey, what a great post.

    But I still have a question. I tried to apply the code "as is" to my Wicket app.
    I started 2 instances of web server behind the haproxy to see what will happen.
    But I get 'Page Expired' exception (especially on an Ajax request) when a request hits second instance.
    The problem (traced down) is in this line:
    IPageMap pageMap = session.pageMapForName(requestParameters.getPageMapName(), false);

    The PageMap returned from the session is null.
    Is extra configuration needed for your clustering solution to work? The

    Session.(IPageMap)getAttribute(attributeForPageMapName(pageMapName));

    simply returns null for the second web server instance.

    Any suggestions would be really appreciated.
- id: 3516
  author: SM
  author_email: heysiddu@gmail.com
  author_url: ''
  date: '2011-10-31 10:01:58 +0000'
  date_gmt: '2011-10-31 17:01:58 +0000'
  content: |-
    Can someone provide an example where I can use two Session Stores, one a local store that would cache all pages and second a clustered store that would cache only current page.

    If JVM running local store goes down and request is routed to another JVM within the cluster, application&acirc;&euro;&trade;s newSession() implementation should check clustered store and return current page.
- id: 3517
  author: S
  author_email: heymantrala@gmail.com
  author_url: ''
  date: '2011-11-01 15:03:51 +0000'
  date_gmt: '2011-11-01 22:03:51 +0000'
  content: |-
    New to Wicket so a basic question. Is there a first level ie; L1 cache maintained by Wicket?
    If you are storing only one page per entry in SecondLevelCache, aren't you losing some of session data and hence resulting in not so good user experience? Or, is there a first level cache that will still save the whole page map?
---
<p>We've been seeing more and more implementations of <a href="http:&#47;&#47;wicketbyexample.com&#47;api&#47;wicket&#47;1.4.5&#47;org&#47;apache&#47;wicket&#47;protocol&#47;http&#47;SecondLevelCacheSessionStore.IPageStore.html" target="_blank">IPageStore<&#47;a> out in the wild for <a href="http:&#47;&#47;wicket.apache.org&#47;" target="_blank">Apache Wicket<&#47;a>.  The interface basically decides how Wicket will store the <a href="http:&#47;&#47;cwiki.apache.org&#47;WICKET&#47;page-maps.html" target="_blank">Pagemap<&#47;a> for your application.  The default that ships with Wicket uses <a href="http:&#47;&#47;wicketbyexample.com&#47;api&#47;wicket&#47;1.4.6&#47;org&#47;apache&#47;wicket&#47;protocol&#47;http&#47;pagestore&#47;DiskPageStore.html" target="_blank">DiskPageStore<&#47;a> which is an implementation that stores the serialized pages grouped in a single file per pagemap. After reading a wonderful blog post on <a href="http:&#47;&#47;letsgetdugg.com" target="_blank">Letsgetdugg<&#47;a> a few days ago: <a href="http:&#47;&#47;letsgetdugg.com&#47;2010&#47;02&#47;07&#47;clustering-wicket-for-fun-and-profit&#47;" target="_blank">Clustering Wicket for fun and profit!<&#47;a>, I decided to take a look at writing an implementation using <a href="http:&#47;&#47;hazelcast.com" target="_blank">Hazelcast<&#47;a> - an "open source clustering and highly scalable data distribution platform".<br />
<a id="more"></a><a id="more-1726"></a><br />
The implementation below borrows heavily from <a href="http:&#47;&#47;fabulously40.com&#47;fabulously&#47;victori" target="_blank">Victor<&#47;a>.  It basically creates a HazelcastInstance in the constructor and then overrides all the methods necessary from <a href="http:&#47;&#47;wicketbyexample.com&#47;api&#47;wicket&#47;1.4.5&#47;org&#47;apache&#47;wicket&#47;protocol&#47;http&#47;pagestore&#47;AbstractPageStore.html" target="_blank">AbstractPageStore<&#47;a>.  Here's some quick code to put in your app's Application implementation that will use this new IPageStore:</p>
<pre lang="java" colla="+">
    @Override<br />
    protected ISessionStore newSessionStore() {<br />
        return new SecondLevelCacheSessionStore(this,<br />
                new HazelcastPageStore("default"));<br />
    }<br />
<&#47;pre></p>
<p>And here's the code for HazelcastPageStore:</p>
<pre lang="java" colla="+">
public class HazelcastPageStore extends AbstractPageStore<br />
                                      implements SecondLevelCacheSessionStore.IClusteredPageStore {</p>
<p>    private Logger logger = LoggerFactory.getLogger(HazelcastPageStore.class);</p>
<p>    private String mapName;<br />
    private HazelcastInstance client;</p>
<p>    public HazelcastPageStore(String mapName) { this(mapName, null); }</p>
<p>    public HazelcastPageStore(String mapName, Config config) {<br />
        this.mapName = mapName;<br />
        client = Hazelcast.newHazelcastInstance(config);<br />
    }</p>
<p>    public String getKey(final String sessId, final String pageMapName, final int pageId, final int pageVersion) {<br />
        return getKey(sessId, pageMapName, pageId, pageVersion, -1);<br />
    }</p>
<p>    public String getKey(final String sessId, final String pageMapName, final int pageId, final int pageVersion, final int ajaxVersion) {<br />
        String key = sessId + ":" + pageMapName + ":" + pageId + ":" + pageVersion + ":" + ajaxVersion;<br />
        if (logger.isDebugEnabled()) {<br />
            logger.debug("GetKey: " + key);<br />
        }<br />
        return key;<br />
    }</p>
<p>    public String storeKey(final String sessionId, final Page page) {<br />
        String key = sessionId + ":" + page.getPageMapName() + ":" + page.getId() + ":" + page.getCurrentVersionNumber() + ":" + (page.getAjaxVersionNumber() - 1);<br />
        if (logger.isDebugEnabled()) {<br />
            logger.debug("StoreKey: " + key);<br />
        }<br />
        return key;<br />
    }</p>
<p>    public boolean containsPage(final String sessionId, final String pageMapName, final int pageId, final int pageVersion) {<br />
        return client.getMap(mapName).containsKey(getKey(sessionId, pageMapName, pageId, pageVersion));<br />
    }</p>
<p>    public void destroy() {<br />
        client.shutdown();<br />
    }</p>
<p>    public <t> Page getPage(final String sessionId, final String pagemap, final int id, final int versionNumber, final int ajaxVersionNumber) {<br />
        IMap<string, Page> map = client.getMap(mapName);</p>
<p>        return map.get(getKey(sessionId, pagemap, id, versionNumber, ajaxVersionNumber));<br />
    }</p>
<p>    public void pageAccessed(final String sessionId, final Page page) {<br />
    }</p>
<p>    public void removePage(final String sessionId, final String pagemap, final int id) {<br />
        String key = getKey(sessionId, pagemap, id, 0);<br />
        key = key.substring(0, key.lastIndexOf(":"));</p>
<p>        IMap<string, Page> map = Hazelcast.getMap(mapName);<br />
        for (String k : map.keySet()) {<br />
            if (k.startsWith(key)) {<br />
                map.remove(k);<br />
            }<br />
        }<br />
    }</p>
<p>    public void storePage(final String sessionId, final Page page) {<br />
        IMap<string, Page> map = client.getMap(mapName);<br />
        map.put(storeKey(sessionId, page), page);<br />
    }</p>
<p>    public void unbind(final String sessionId) {<br />
        IMap<string, Page> map = client.getMap(mapName);<br />
        for (String key : map.keySet()) {<br />
            if (key.startsWith(sessionId)) {<br />
                map.remove(key);<br />
            }<br />
        }<br />
    }<br />
}<br />
<&#47;pre></p>
<p>Several other IPageStore implementations available:</p>
<ul>
<li><a href="http:&#47;&#47;letsgetdugg.com&#47;2010&#47;02&#47;07&#47;clustering-wicket-for-fun-and-profit&#47;" target="_blank">Letsgetdugg - memcached implementation<&#47;a><&#47;li>
<li><a href="http:&#47;&#47;www.mail-archive.com&#47;users@wicket.apache.org&#47;msg46421.html" target="_blank">Google App Engine memcached implementation<&#47;a><&#47;li><br />
<&#47;ul></p>
<p>Let us know if you find any others out in the wild so we can add them here.</p>