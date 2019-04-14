# Fluentd logs to multiple Datadog instances

This will forward your Fluentd logs to multiple Datadog instances.
The most common use case is if you are a user of Datadog's multi-org structure, and
you are wanting to have your logs sent to two (or more) organizations.

## Pre-requirements
### Install Fluentd on your application server(s)

See. [Fluentd installation instructions](https://docs.fluentd.org/v0.12/categories/installation)

### Install Datadog's output plugin

To add the plugin to fluentd agent, use the following command:

    /usr/sbin/td-agent-gem install fluent-plugin-datadog

### Restart fluentd agent
    service td-agent restart

## Get your Datadog API key
See. [Datadog – API keys](https://app.datadoghq.com/account/settings#api)

If you are running on Datadog EU, see. [Datadog EU – API keys](https://app.datadoghq.eu/account/settings#api)

## Configure log input for your application

Edit `/etc/td-agent/td-agent.conf`, make sure that **path** points to your application log file.

```xml 
<source>
  @type tail
  path /tmp/myapp.log
  pos_file /var/log/td-agent/myapp.pos
  format none
  tag myapplication
  tag datadog.org1
  tag datadog.org2
</source>
```

## Configure log output to Datadog organisation(s)
In this example, we'll forward your logs to two Datadog instances, and we'll expect that the logs that you are sending are apache logs, set dd_source to something else if that doesn't apply to your use case. If you are running on **Datadog EU**, see the last example.

Edit `/etc/td-agent/td-agent.conf` to read the following:

```xml 
<match datadog.org1.**>
    @type datadog
    @id myteam
    api_key <api key here>

    # Optional
    include_tag_key true
    tag_key 'tag'
    dd_source 'apache'
    dd_tags 'app:myapp,team:myteam'
</match>
```


```xml 
<match datadog.org2.**>
    @type datadog
    @id myteam
    api_key <api key here>
    host tcp-intake.logs.datadoghq.eu
    ssl_port 443

    # Optional
    include_tag_key true
    tag_key 'tag'
    dd_source 'apache'
    dd_tags 'app:myapp,team:myteam'
</match>
```

**Note** If you are running on Datadog EU, set host and ssl_port to these values instead:

```xml 
<match datadog.org2.**>
    @type datadog
    @id myteam
    api_key <api key here>
    host tcp-intake.logs.datadoghq.eu
    ssl_port 443

    # Optional
    include_tag_key true
    tag_key 'tag'
    dd_source 'apache'
    dd_tags 'app:myapp,team:myteam'
</match>
```