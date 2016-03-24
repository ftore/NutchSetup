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

You may also have to update your /etc/hosts file.


## Integrate HBase with Nutch
## Install Solr
## Integrate Solr with Nutch

##References
<a href="http://wiki.apache.org/nutch/NutchTutorial">http://wiki.apache.org/nutch/NutchTutorial</a>
<a href="http://wiki.apache.org/nutch/Nutch2Tutorial">http://wiki.apache.org/nutch/Nutch2Tutorial</a>
<a href="http://hbase.apache.org/book/quickstart.html">http://hbase.apache.org/book/quickstart.html</a>

