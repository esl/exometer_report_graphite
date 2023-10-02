# exometer_report_graphite

Copyright (c) 2014 Basho Technologies, Inc.  All Rights Reserved.

__Version:__ Feb 1 2015 23:02:37

__Authors:__ Ulf Wiger ([`ulf.wiger@feuerlabs.com`](mailto:ulf.wiger@feuerlabs.com)), Magnus Feuer ([`magnus.feuer@feuerlabs.com`](mailto:magnus.feuer@feuerlabs.com)).

The graphite reporter uses the TCP/IP protocol to forward
subscribed-to metrics and data points to a graphite server, such as
the one provided by [`http://hostedgraphite.com`](http://hostedgraphite.com). When the graphite
reporter receives a metric-datapoint value (subscribed to through
`exometer_report:subscriber()`), the reporter will immediately
forward the key-value pair to the graphite server.

#### <a name="Configuring_graphite_reporter">Configuring graphite reporter</a> ####

Below is an example of a graphite reporter application environment, with
its correct location in the hierarchy:

```erlang

{exometer_core, [
    {report, [
        {reporters, [
            {exometer_report_graphite, [
                {connect_timeout, 5000},
                {prefix, "web_stats"},
                {env_prefix, "HOSTNAME"},
                {host, "carbon.hostedgraphite.com"},
                {port, 2003},
                {api_key, "267d121c-8387-459a-9326-000000000000"}
            ]}
        ]}
    ]}
]}
```

The following attributes are available for configuration:

+ `connect_timeout` (milliseconds - default: 5000)<br />Specifies how long the graphite reporter plugin shall wait for a TCP
connection to complete before timing out. A timed out connection will
not be reconnected automatically. (To be fixed.)

+ `prefix` (string - default: "")<br />Specifies an optional prefix to prepend all metric names with before
they are sent to the graphite server.

+ `env_prefix` (string - default: "")<br />Specifies an environmental variable name from which an additional prefix
will be taken. When not set no addional prefix is added.
In case both `prefix` and `env_prefix` are defined it will be placed __before__ `prefix` and separated with a dot.

+ `host` (string - default: "carbon.hostedgraphite.com")<br />Specifies the name (or IP address) of the graphite server to report to.

+ `port` (integer - default: 2003)<br />Specifies the TCP port on the given graphite server to connect to.

+ `api_key` (string - default: n/a)<br />Specifies the api key to use when reporting to a hosted graphite server.

Full metric naming depending on the configuration is as following:

`api_key` | `env_prefix` | `prefix` | Full metric name
--------- | ------------ | -------- | ----------------
&cross;   | &cross;      | &cross;  | `Metric`
&cross;   | &cross;      | &check;  | `Prefix.Metric`
&cross;   | &check;      | &cross;  | `EnvPrefix.Metric`
&cross;   | &check;      | &check;  | `EnvPrefix.Prefix.Metric`
&check;   | &cross;      | &cross;  | `ApiKey.Metric`
&check;   | &cross;      | &check;  | `ApiKey.Prefix.Metric`
&check;   | &check;      | &cross;  | `ApiKey.EnvPrefix.Metric`
&check;   | &check;      | &check;  | `ApiKey.EnvPrefix.Prefix.Metric`
