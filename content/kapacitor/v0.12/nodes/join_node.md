---
title: JoinNode
note: Auto generated by tickdoc

menu:
  kapacitor_012:
    name: Join
    identifier: join_node
    weight: 90
    parent: nodes
---

Joins the data from any number of nodes. 
As each data point is received from a parent node it is paired 
with the next data points from the other parent nodes with a 
matching timestamp. Each parent node contributes at most one point 
to each joined point. A tolerance can be supplied to join points 
that do not have perfectly aligned timestamps. 
Any points that fall within the tolerance are joined on the timestamp. 
If multiple points fall within the same tolerance window than they are joined in the order 
they arrive. 

Aliases are used to prefix all fields from the respective nodes. 

The join can be an inner or outer join, see the [JoinNode.Fill](/kapacitor/v0.12/nodes/join_node/#fill) property. 

Example: 


```javascript
    var errors = stream
        |from()
            .measurement('errors')
    var requests = stream
        |from()
            .measurement('requests')
    // Join the errors and requests streams
    errors
        |join(requests)
            // Provide prefix names for the fields of the data points.
            .as('errors', 'requests')
            // points that are within 1 second are considered the same time.
            .tolerance(1s)
            // fill missing values with 0, implies outer join.
            .fill(0.0)
            // name the resulting stream
            .streamName('error_rate')
        // Both the "value" fields from each parent have been prefixed
        // with the respective names 'errors' and 'requests'.
        |eval(lambda: "errors.value" / "requests.value")
           .as('rate')
        ...
```

In the above example the `errors` and `requests` streams are joined 
and then transformed to calculate a combined field. 


Index
-----

### Properties

-	[As](/kapacitor/v0.12/nodes/join_node/#as)
-	[Fill](/kapacitor/v0.12/nodes/join_node/#fill)
-	[On](/kapacitor/v0.12/nodes/join_node/#on)
-	[StreamName](/kapacitor/v0.12/nodes/join_node/#streamname)
-	[Tolerance](/kapacitor/v0.12/nodes/join_node/#tolerance)

### Chaining Methods

-	[Alert](/kapacitor/v0.12/nodes/join_node/#alert)
-	[Bottom](/kapacitor/v0.12/nodes/join_node/#bottom)
-	[Count](/kapacitor/v0.12/nodes/join_node/#count)
-	[Deadman](/kapacitor/v0.12/nodes/join_node/#deadman)
-	[Derivative](/kapacitor/v0.12/nodes/join_node/#derivative)
-	[Distinct](/kapacitor/v0.12/nodes/join_node/#distinct)
-	[Eval](/kapacitor/v0.12/nodes/join_node/#eval)
-	[First](/kapacitor/v0.12/nodes/join_node/#first)
-	[GroupBy](/kapacitor/v0.12/nodes/join_node/#groupby)
-	[HttpOut](/kapacitor/v0.12/nodes/join_node/#httpout)
-	[InfluxDBOut](/kapacitor/v0.12/nodes/join_node/#influxdbout)
-	[Join](/kapacitor/v0.12/nodes/join_node/#join)
-	[Last](/kapacitor/v0.12/nodes/join_node/#last)
-	[Log](/kapacitor/v0.12/nodes/join_node/#log)
-	[Max](/kapacitor/v0.12/nodes/join_node/#max)
-	[Mean](/kapacitor/v0.12/nodes/join_node/#mean)
-	[Median](/kapacitor/v0.12/nodes/join_node/#median)
-	[Min](/kapacitor/v0.12/nodes/join_node/#min)
-	[Percentile](/kapacitor/v0.12/nodes/join_node/#percentile)
-	[Sample](/kapacitor/v0.12/nodes/join_node/#sample)
-	[Shift](/kapacitor/v0.12/nodes/join_node/#shift)
-	[Spread](/kapacitor/v0.12/nodes/join_node/#spread)
-	[Stats](/kapacitor/v0.12/nodes/join_node/#stats)
-	[Stddev](/kapacitor/v0.12/nodes/join_node/#stddev)
-	[Sum](/kapacitor/v0.12/nodes/join_node/#sum)
-	[Top](/kapacitor/v0.12/nodes/join_node/#top)
-	[Union](/kapacitor/v0.12/nodes/join_node/#union)
-	[Where](/kapacitor/v0.12/nodes/join_node/#where)
-	[Window](/kapacitor/v0.12/nodes/join_node/#window)

Properties
----------

Property methods modify state on the calling node.
They do not add another node to the pipeline, and always return a reference to the calling node.
Property methods are marked using the `.` operator.


### As

Prefix names for all fields from the respective nodes. 
Each field from the parent nodes will be prefixed with the provided name and a &#39;.&#39;. 
See the example above. 

The names cannot have a dot &#39;.&#39; character. 



```javascript
node.as(names ...string)
```


### Fill

Fill the data. 
The fill option implies the type of join: inner or full outer 
Options are: 

- none - (default) skip rows where a point is missing, inner join. 
- null - fill missing points with null, full outer join. 
- Any numerical value - fill fields with given value, full outer join. 


```javascript
node.fill(value interface{})
```


### On

Join on specfic dimensions. 
For example given two measurements: 

1. building_power -- tagged by building, value is the total power consumed by the building. 
2. floor_power -- tagged by building and floor, values is the total power consumed by the floor. 

You want to calculate the percentage of the total building power consumed by each floor. 

Example: 


```javascript
    var buidling = stream
        |from()
            .measurement('building_power')
            .groupBy('building')
    var floor = stream
        |from()
            .measurement('floor_power')
            .groupBy('building', 'floor')
    building
        |join(floor)
            .as('building', 'floor')
            .on('building')
        |eval(lambda: "floor.value" / "building.value")
            ... // Values here are grouped by 'building' and 'floor'
```



```javascript
node.on(dims ...string)
```


### StreamName

The name of this new joined data stream. 
If empty the name of the left parent is used. 


```javascript
node.streamName(value string)
```


### Tolerance

The maximum duration of time that two incoming points 
can be apart and still be considered to be equal in time. 
The joined data point&#39;s time will be rounded to the nearest 
multiple of the tolerance duration. 


```javascript
node.tolerance(value time.Duration)
```


Chaining Methods
----------------

Chaining methods create a new node in the pipeline as a child of the calling node.
They do not modify the calling node.
Chaining methods are marked using the `|` operator.


### Alert

Create an alert node, which can trigger alerts. 


```javascript
node|alert()
```

Returns: [AlertNode](/kapacitor/v0.12/nodes/alert_node/)


### Bottom

Select the bottom `num` points for `field` and sort by any extra tags or fields. 


```javascript
node|bottom(num int64, field string, fieldsAndTags ...string)
```

Returns: [InfluxQLNode](/kapacitor/v0.12/nodes/influx_q_l_node/)


### Count

Count the number of points. 


```javascript
node|count(field string)
```

Returns: [InfluxQLNode](/kapacitor/v0.12/nodes/influx_q_l_node/)


### Deadman

Helper function for creating an alert on low throughput, aka deadman&#39;s switch. 

- Threshold -- trigger alert if throughput drops below threshold in points/interval. 
- Interval -- how often to check the throughput. 
- Expressions -- optional list of expressions to also evaluate. Useful for time of day alerting. 

Example: 


```javascript
    var data = stream
        |from()...
    // Trigger critical alert if the throughput drops below 100 points per 10s and checked every 10s.
    data
        |deadman(100.0, 10s)
    //Do normal processing of data
    data...
```

The above is equivalent to this 
Example: 


```javascript
    var data = stream
        |from()...
    // Trigger critical alert if the throughput drops below 100 points per 10s and checked every 10s.
    data
        |stats(10s)
        |derivative('collected')
            .unit(10s)
            .nonNegative()
        |alert()
            .id('node \'stream0\' in task \'{{ .TaskName }}\'')
            .message('{{ .ID }} is {{ if eq .Level "OK" }}alive{{ else }}dead{{ end }}: {{ index .Fields "collected" | printf "%0.3f" }} points/10s.')
            .crit(lamdba: "collected" <= 100.0)
    //Do normal processing of data
    data...
```

The `id` and `message` alert properties can be configured globally via the &#39;deadman&#39; configuration section. 

Since the [AlertNode](/kapacitor/v0.12/nodes/alert_node/) is the last piece it can be further modified as normal. 
Example: 


```javascript
    var data = stream
        |from()...
    // Trigger critical alert if the throughput drops below 100 points per 1s and checked every 10s.
    data
        |deadman(100.0, 10s)
            .slack()
            .channel('#dead_tasks')
    //Do normal processing of data
    data...
```

You can specify additional lambda expressions to further constrain when the deadman&#39;s switch is triggered. 
Example: 


```javascript
    var data = stream
        |from()...
    // Trigger critical alert if the throughput drops below 100 points per 10s and checked every 10s.
    // Only trigger the alert if the time of day is between 8am-5pm.
    data
        |deadman(100.0, 10s, lambda: hour("time") >= 8 AND hour("time") <= 17)
    //Do normal processing of data
    data...
```



```javascript
node|deadman(threshold float64, interval time.Duration, expr ...tick.Node)
```

Returns: [AlertNode](/kapacitor/v0.12/nodes/alert_node/)


### Derivative

Create a new node that computes the derivative of adjacent points. 


```javascript
node|derivative(field string)
```

Returns: [DerivativeNode](/kapacitor/v0.12/nodes/derivative_node/)


### Distinct

Produce batch of only the distinct points. 


```javascript
node|distinct(field string)
```

Returns: [InfluxQLNode](/kapacitor/v0.12/nodes/influx_q_l_node/)


### Eval

Create an eval node that will evaluate the given transformation function to each data point. 
A list of expressions may be provided and will be evaluated in the order they are given 
and results of previous expressions are made available to later expressions. 


```javascript
node|eval(expressions ...tick.Node)
```

Returns: [EvalNode](/kapacitor/v0.12/nodes/eval_node/)


### First

Select the first point. 


```javascript
node|first(field string)
```

Returns: [InfluxQLNode](/kapacitor/v0.12/nodes/influx_q_l_node/)


### GroupBy

Group the data by a set of tags. 

Can pass literal * to group by all dimensions. 
Example: 


```javascript
    |groupBy(*)
```



```javascript
node|groupBy(tag ...interface{})
```

Returns: [GroupByNode](/kapacitor/v0.12/nodes/group_by_node/)


### HttpOut

Create an http output node that caches the most recent data it has received. 
The cached data is available at the given endpoint. 
The endpoint is the relative path from the API endpoint of the running task. 
For example if the task endpoint is at &#34;/api/v1/task/&lt;task_name&gt;&#34; and endpoint is 
&#34;top10&#34;, then the data can be requested from &#34;/api/v1/task/&lt;task_name&gt;/top10&#34;. 


```javascript
node|httpOut(endpoint string)
```

Returns: [HTTPOutNode](/kapacitor/v0.12/nodes/http_out_node/)


### InfluxDBOut

Create an influxdb output node that will store the incoming data into InfluxDB. 


```javascript
node|influxDBOut()
```

Returns: [InfluxDBOutNode](/kapacitor/v0.12/nodes/influx_d_b_out_node/)


### Join

Join this node with other nodes. The data is joined on timestamp. 


```javascript
node|join(others ...Node)
```

Returns: [JoinNode](/kapacitor/v0.12/nodes/join_node/)


### Last

Select the last point. 


```javascript
node|last(field string)
```

Returns: [InfluxQLNode](/kapacitor/v0.12/nodes/influx_q_l_node/)


### Log

Create a node that logs all data it receives. 


```javascript
node|log()
```

Returns: [LogNode](/kapacitor/v0.12/nodes/log_node/)


### Max

Select the maximum point. 


```javascript
node|max(field string)
```

Returns: [InfluxQLNode](/kapacitor/v0.12/nodes/influx_q_l_node/)


### Mean

Compute the mean of the data. 


```javascript
node|mean(field string)
```

Returns: [InfluxQLNode](/kapacitor/v0.12/nodes/influx_q_l_node/)


### Median

Compute the median of the data. Note, this method is not a selector, 
if you want the median point use .percentile(field, 50.0). 


```javascript
node|median(field string)
```

Returns: [InfluxQLNode](/kapacitor/v0.12/nodes/influx_q_l_node/)


### Min

Select the minimum point. 


```javascript
node|min(field string)
```

Returns: [InfluxQLNode](/kapacitor/v0.12/nodes/influx_q_l_node/)


### Percentile

Select a point at the given percentile. This is a selector function, no interpolation between points is performed. 


```javascript
node|percentile(field string, percentile float64)
```

Returns: [InfluxQLNode](/kapacitor/v0.12/nodes/influx_q_l_node/)


### Sample

Create a new node that samples the incoming points or batches. 

One point will be emitted every count or duration specified. 


```javascript
node|sample(rate interface{})
```

Returns: [SampleNode](/kapacitor/v0.12/nodes/sample_node/)


### Shift

Create a new node that shifts the incoming points or batches in time. 


```javascript
node|shift(shift time.Duration)
```

Returns: [ShiftNode](/kapacitor/v0.12/nodes/shift_node/)


### Spread

Compute the difference between min and max points. 


```javascript
node|spread(field string)
```

Returns: [InfluxQLNode](/kapacitor/v0.12/nodes/influx_q_l_node/)


### Stats

Create a new stream of data that contains the internal statistics of the node. 
The interval represents how often to emit the statistics based on real time. 
This means the interval time is independent of the times of the data points the source node is receiving. 


```javascript
node|stats(interval time.Duration)
```

Returns: [StatsNode](/kapacitor/v0.12/nodes/stats_node/)


### Stddev

Compute the standard deviation. 


```javascript
node|stddev(field string)
```

Returns: [InfluxQLNode](/kapacitor/v0.12/nodes/influx_q_l_node/)


### Sum

Compute the sum of all values. 


```javascript
node|sum(field string)
```

Returns: [InfluxQLNode](/kapacitor/v0.12/nodes/influx_q_l_node/)


### Top

Select the top `num` points for `field` and sort by any extra tags or fields. 


```javascript
node|top(num int64, field string, fieldsAndTags ...string)
```

Returns: [InfluxQLNode](/kapacitor/v0.12/nodes/influx_q_l_node/)


### Union

Perform the union of this node and all other given nodes. 


```javascript
node|union(node ...Node)
```

Returns: [UnionNode](/kapacitor/v0.12/nodes/union_node/)


### Where

Create a new node that filters the data stream by a given expression. 


```javascript
node|where(expression tick.Node)
```

Returns: [WhereNode](/kapacitor/v0.12/nodes/where_node/)


### Window

Create a new node that windows the stream by time. 

NOTE: Window can only be applied to stream edges. 


```javascript
node|window()
```

Returns: [WindowNode](/kapacitor/v0.12/nodes/window_node/)
