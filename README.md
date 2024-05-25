# ElasticSearch

[Course Link](https://www.udemy.com/course/elasticsearch-masterclass)

[[toc]]

## What is ElasticSearch?

- **ElasticSearch** is an open-source search engine.
- It is designed for searching, analyzing, and visualizing large volumes of data quickly and in near real-time.
- ElasticSearch is built on top of Apache Lucene, a powerful text search engine library.
- It is part of the Elastic Stack, which also includes tools like Logstash for data processing and Kibana for data visualization.

### Key Features

1. **Core Principles**:
   - Understanding the core principles of ElasticSearch and Apache Lucene.
   - It provides distributed, RESTful search and analytics capabilities.
2. **Search Capabilities**:
   - In-depth understanding of searching in ElasticSearch.
   - It supports complex queries and full-text search.
3. **Data Processing**:
   - Processing data with the help of Logstash from numerous sources to several destinations.
   - It can handle structured and unstructured data.
4. **Performance**:
   - The secret behind ElasticSearch's fast performance and how it works under the hood.
   - It is designed to scale horizontally, meaning it can handle large amounts of data by adding more nodes to the cluster.
5. **Real-time Analytics**:
   - Perform real-time analytics on huge datasets.
   - Visualize data in ElasticSearch using Kibana.

### Learning Resources

- **Courses**: There are comprehensive courses available, such as the "Elasticsearch Masterclass" on Udemy, which covers:
  - Core principles and functionalities.
  - Setting up the environment.
  - Diving into cluster management.
  - In-depth search functionalities.
  - Aggregation and statistical analysis.
  - Processing events with Logstash.

### Practical Applications

- ElasticSearch is widely used for log and event data analysis, full-text search, security intelligence, business analytics, and more.

## Installation

- Download and install ElasticSearch from the official website: [ElasticSearch Downloads](https://www.elastic.co/downloads/elasticsearch)
- Download and install Kibana for data visualization: [Kibana Downloads](https://www.elastic.co/downloads/kibana)
- Download and install Logstash for data processing: [Logstash Downloads](https://www.elastic.co/downloads/logstash)
- `sudo mv ~/Downloads/kibana-8.13.4 /usr/local/`
- `sudo mv ~/Downloads/logstash-8.13.4 /usr/local/`
- `sudo mv ~/Downloads/elasticsearch-8.13.4 /usr/local/`

Now you will need to change the `$JAVA_HOME` inside kibana and elasticsearch to the correct path. Inside of `/usr/local/kibana-8.13.4/bin` you will need to change the `kibana` file to point to where ever your java home is. Because I am using sdkman I have it set to `/home/username/.sdkman/candidates/java/current`. That file should look like this:

> 🛑 Before you run this below and fuck your install up please make note that I use [sdkman](https://sdkman.io/). Whenever I give directions like this I really do try and remind the reader that is is based on my individual setup. It makes me think of that part in Dr. Strange where he opens the Eye of Agamotto for the first time and then gets screamed at by Wong and Mordo because:


https://github.com/polyglotdev/elasticsearch-masterclass/assets/28935096/29dd581f-2d28-4b3b-9ed0-0d24732ea829


```bash
#!/bin/sh
export JAVA_HOME=/Users/domhallan/.sdkman/candidates/java/current

SCRIPT=$0

# SCRIPT may be an arbitrarily deep series of symlinks. Loop until we have the concrete path.
while [ -h "$SCRIPT" ] ; do
  ls=$(ls -ld "$SCRIPT")
  # Drop everything prior to ->
  link=$(expr "$ls" : '.*-> \(.*\)$')
  if expr "$link" : '/.*' > /dev/null; then
    SCRIPT="$link"
  else
    SCRIPT=$(dirname "$SCRIPT")/"$link"
  fi
done

DIR="$(dirname "${SCRIPT}")/.."
CONFIG_DIR=${KBN_PATH_CONF:-"$DIR/config"}
NODE="${DIR}/node/bin/node"
test -x "$NODE"
if [ ! -x "$NODE" ]; then
  echo "unable to find usable node.js executable."
  exit 1
fi

if [ -f "${CONFIG_DIR}/node.options" ]; then
  KBN_NODE_OPTS="$(grep -v ^# < ${CONFIG_DIR}/node.options | xargs)"
fi

NODE_OPTIONS="--no-warnings --max-http-header-size=65536 $KBN_NODE_OPTS $NODE_OPTIONS" NODE_ENV=production exec "${NODE}" "${DIR}/src/cli/dist" "${@}"
```

And then inside of `/usr/local/elasticsearch-8.13.4/bin` you will need to change the `elasticsearch-env` file to point to where ever your java home is. Because I am using sdkman I have it set to `/home/username/.sdkman/candidates/java/current`. That file should look like this:

```bash
#!/bin/bash

set -e -o pipefail

CDPATH=""

SCRIPT="$0"

# SCRIPT might be an arbitrarily deep series of symbolic links; loop until we
# have the concrete path
while [ -h "$SCRIPT" ] ; do
  ls=`ls -ld "$SCRIPT"`
  # Drop everything prior to ->
  link=`expr "$ls" : '.*-> \(.*\)$'`
  if expr "$link" : '/.*' > /dev/null; then
    SCRIPT="$link"
  else
    SCRIPT=`dirname "$SCRIPT"`/"$link"
  fi
done

# determine Elasticsearch home; to do this, we strip from the path until we find
# bin, and then strip bin (there is an assumption here that there is no nested
# directory under bin also named bin)
ES_HOME=`dirname "$SCRIPT"`

# now make ES_HOME absolute
ES_HOME=`cd "$ES_HOME"; pwd`

while [ "`basename "$ES_HOME"`" != "bin" ]; do
  ES_HOME=`dirname "$ES_HOME"`
done
ES_HOME=`dirname "$ES_HOME"`

# Set JAVA_HOME to sdkman path
export JAVA_HOME=/Users/domhallan/.sdkman/candidates/java/current

# now set the path to java
if [ ! -z "$ES_JAVA_HOME" ]; then
  JAVA="$ES_JAVA_HOME/bin/java"
  JAVA_TYPE="ES_JAVA_HOME"

  if [ ! -x "$JAVA" ]; then
    echo "could not find java in $JAVA_TYPE at $JAVA" >&2
    exit 1
  fi

  # check the user supplied jdk version
  "$JAVA" -cp "$ES_HOME/lib/java-version-checker/*" org.elasticsearch.tools.java_version_checker.JavaVersionChecker
else
  # use JAVA_HOME
  JAVA="$JAVA_HOME/bin/java"
  JAVA_TYPE="JAVA_HOME"
fi

# do not let JAVA_TOOL_OPTIONS slip in (as the JVM does by default)
if [ ! -z "$JAVA_TOOL_OPTIONS" ]; then
  echo "warning: ignoring JAVA_TOOL_OPTIONS=$JAVA_TOOL_OPTIONS"
  unset JAVA_TOOL_OPTIONS
fi

# warn that we are not observing the value of JAVA_HOME
if [ ! -z "$JAVA_HOME" ]; then
  echo "warning: ignoring JAVA_HOME=$JAVA_HOME; using $JAVA_TYPE" >&2
fi

# JAVA_OPTS is not a built-in JVM mechanism but some people think it is so we
# warn them that we are not observing the value of $JAVA_OPTS
if [ ! -z "$JAVA_OPTS" ]; then
  echo -n "warning: ignoring JAVA_OPTS=$JAVA_OPTS; "
  echo "pass JVM parameters via ES_JAVA_OPTS"
fi

export HOSTNAME=$HOSTNAME

if [ -z "$ES_PATH_CONF" ]; then ES_PATH_CONF="$ES_HOME"/config; fi

if [ -z "$ES_PATH_CONF" ]; then
  echo "ES_PATH_CONF must be set to the configuration path"
  exit 1
fi

# now make ES_PATH_CONF absolute
ES_PATH_CONF=`cd "$ES_PATH_CONF"; pwd`

ES_DISTRIBUTION_TYPE=tar

cd "$ES_HOME"
```

