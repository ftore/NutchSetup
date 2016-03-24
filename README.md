# Nutch + HBase + Solr Setup

## Required applications
<a href="http://www.apache.org/dyn/closer.lua/nutch/2.3.1/apache-nutch-2.3.1-src.tar.gz">Nutch 2.3.1</a><br />
<a href="https://archive.apache.org/dist/hbase/hbase-0.98.8/hbase-0.98.8-hadoop2-bin.tar.gz">HBase 0.98.8-hadoop2</a><br />
<a href="http://mirror.apache-kr.org/lucene/solr/4.10.4/solr-4.10.4.tgz">Solr 4.10.4</a><br />

## Install Nutch
Download Nutch from <a href="http://www.apache.org/dyn/closer.lua/nutch/2.3.1/apache-nutch-2.3.1-src.tar.gz">here</a>.

```
$ wget http://www.apache.org/dyn/closer.lua/nutch/2.3.1/apache-nutch-2.3.1-src.tar.gz
$ tar -xzf apache-nutch-2.3.1-src.tar.gz
$ sudo mv apache-nutch-2.3.1 /opt/nutch-2.3.1
```
Specify Gora backend and other configuration in $NUTCH_HOME/conf/nutch-size.xml.

```
  <property>
    <name>storage.data.store.class</name>
    <value>org.apache.gora.hbase.store.HBaseStore</value>
    <description>Default class for storing data</description>
  </property>
  <property>
    <name>http.agent.name</name>
    <value>My Nutch Spider</value>
  </property>
  <property>
    <name>plugin.includes</name>
    <value>protocol-httpclient|urlfilter-regex|index-(basic|more)|query-(basic|site|url|lang)|indexer-solr|nutch-extensionpoints|protocol-httpclient|urlfilter-regex|parse-(text|html|msexcel|msword|mspowerpoint|pdf)|summary-basic|scoring-opic|urlnormalizer-(pass|regex|basic)protocol-http|urlfilter-regex|parse-(html|tika|metatags)|index-(basic|anchor|more|metadata)</value>
  </property>

```

Ensure the HBase gora-hbase dependency is available in $NUTCH_HOME/ivy/ivy.xml

```
<!-- Uncomment this to use HBase as Gora backend. -->
<dependency org="org.apache.gora" name="gora-hbase" rev="0.6.1" conf="*->default" />
<dependency org="org.apache.hbase" name="hbase-common" rev="0.98.8-hadoop2" conf="*->default" />
```

Ensure that HBaseStore is set as the default datastore in $NUTCH_HOME/conf/gora.properties.

```
gora.datastore.default=org.apache.gora.hbase.store.HBaseStore
```

Compile Nutch:
```
$ cd $NUTCH_HOME/
$ ant runtime
```

## Install HBase

You may also have to update your /etc/hosts file. Make sure that the hosts file contains the loop back address, which is 127.0.0.1. (In Ubuntu it is 127.0.1.1) 

```
$ sudo vi /etc/hosts

127.0.0.1       localhost.localdomain localhost akmal-vm
::1             ip6-localhost ip6-loopback
fe80::1%lo0     ip6-localhost ip6-loopback
```

Download HBase.
```
$ wget https://archive.apache.org/dist/hbase/hbase-0.98.8/hbase-0.98.8-hadoop2-bin.tar.gz
$ tar -xzf hbase-0.98.8-hadoop2-bin.tar.gz
$ sudo mv hbase-0.98.8-hadoop2 /opt/hbase-0.98.8
$ cd /opt/hbase-0.98.8/conf
$ vi hbase-site.xml

    <property>
        <name>hbase.rootdir</name>
        <value>file:///home/akmal/hbase</value>
    </property>
    <property>
        <name>hbase.zookeeper.property.dataDir</name>
        <value>/home/akmal/zookeeper</value>
    </property>

```
Start HBase.

```
$ cd $HBASE_HOME/bin/start-hbase.sh
```

## Install Solr
Download Solr.

```
$ wget http://mirror.apache-kr.org/lucene/solr/4.10.4/solr-4.10.4.tgz
$ tar -xzf solr-4.10.4.tgz
$ sudo mv solr-4.10.4 /opt/solr-4.10.4

```
Do not run Solr yet.

## Integrate Solr with Nutch

Backup the original Solr example schema.xml:
```
$ mv ${APACHE_SOLR_HOME}/example/solr/collection1/conf/schema.xml ${APACHE_SOLR_HOME}/example/solr/collection1/conf/schema.xml.org
```

Copy the Nutch specific schema.xml to replace it:
```
cp ${NUTCH_RUNTIME_HOME}/conf/schema.xml ${APACHE_SOLR_HOME}/example/solr/collection1/conf/
```

Open the Nutch schema.xml file for editing:
```
vi ${APACHE_SOLR_HOME}/example/solr/collection1/conf/schema.xml
```
Comment out the following lines (53-54) in the file by changing this:
```
   <filter class="solr.EnglishPorterFilterFactory" protected="protwords.txt"/>
```
to this
```
<!--   <filter class="solr.EnglishPorterFilterFactory" protected="protwords.txt"/> -->
```
Add the following line right after the line <field name="id" ... /> (probably at line 69-70)
```
<field name="_version_" type="long" indexed="true" stored="true"/>
```
If you want to see the raw HTML indexed by Solr, change the content field definition (line 80) to:
```
<field name="content" type="text" stored="true" indexed="true"/>
```

Start Solr:
```
$ cd $SOLR_HOME/example 
$ sudo -E java -jar start.jar
```

## Crawl your first website

Create a URL seed list in $NUTCH_HOME/runtime/local:
```
$ mkdir -p urls
$ cd urls
$ sudo vi seed.txt

http://nutch.apache.org
```

Edit the file $NUTCH_HOME/runtime/local/conf/regex-urlfilter.txt and replace
```
# accept anything else
#+.
+^http://([a-z0-9]*\.)*nutch.apache.org/
```
Start crawling:
```
$ cd $NUTH_HOME/runtime/local
$ sudo -E ./bin/crawl urls TestCrawl http://localhost:8983/solr 2
```

Now you can start searching in <a href="http://localhost:8983/solr/#/collection1/query">http://localhost:8983/solr/#/collection1/query</a>

##References
<a href="http://wiki.apache.org/nutch/NutchTutorial">http://wiki.apache.org/nutch/NutchTutorial</a><br />
<a href="http://wiki.apache.org/nutch/Nutch2Tutorial">http://wiki.apache.org/nutch/Nutch2Tutorial</a><br />
<a href="http://hbase.apache.org/book/quickstart.html">http://hbase.apache.org/book/quickstart.html</a><br />

