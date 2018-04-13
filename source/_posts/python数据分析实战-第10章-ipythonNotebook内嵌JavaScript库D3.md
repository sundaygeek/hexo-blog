---
title: 'python数据分析实战-第10章-ipythonNotebook内嵌JavaScript库D3 '
date: 2018-04-12 18:33:58
tags:
- pyhton
- javascript-D3
- jinja2
- 数据分析
categories:
 - 数据分析
 - python
---


第10章　IPython Notebook内嵌JavaScript库D3　　252
10.1　开放的人口数据源　　252
10.2　JavaScript库D3　　255
10.3　绘制簇状条状图　　259
10.4　地区分布图　　262
10.5　2014年美国人口地区分布图　　266
10.6　小结　　270

```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
%matplotlib inline

# from urllib.request import urlopen
# pop2014 = pd.read_csv(
# urlopen('http://www.census.gov/popest/data/counties/totals/2014/files/CO-EST2014-alldata.csv'),
# encoding='latin-1',
# dtype={'STATE': 'str', 'COUNTY': 'str'}
# )

pop2014 = pd.read_csv('CO-EST2014-alldata.csv',encoding='latin-1',dtype={'STATE': 'str', 'COUNTY': 'str'})
print(pop2014.shape)
print(pop2014.columns)
```

    (3193, 84)
    Index(['SUMLEV', 'REGION', 'DIVISION', 'STATE', 'COUNTY', 'STNAME', 'CTYNAME',
           'CENSUS2010POP', 'ESTIMATESBASE2010', 'POPESTIMATE2010',
           'POPESTIMATE2011', 'POPESTIMATE2012', 'POPESTIMATE2013',
           'POPESTIMATE2014', 'NPOPCHG_2010', 'NPOPCHG_2011', 'NPOPCHG_2012',
           'NPOPCHG_2013', 'NPOPCHG_2014', 'BIRTHS2010', 'BIRTHS2011',
           'BIRTHS2012', 'BIRTHS2013', 'BIRTHS2014', 'DEATHS2010', 'DEATHS2011',
           'DEATHS2012', 'DEATHS2013', 'DEATHS2014', 'NATURALINC2010',
           'NATURALINC2011', 'NATURALINC2012', 'NATURALINC2013', 'NATURALINC2014',
           'INTERNATIONALMIG2010', 'INTERNATIONALMIG2011', 'INTERNATIONALMIG2012',
           'INTERNATIONALMIG2013', 'INTERNATIONALMIG2014', 'DOMESTICMIG2010',
           'DOMESTICMIG2011', 'DOMESTICMIG2012', 'DOMESTICMIG2013',
           'DOMESTICMIG2014', 'NETMIG2010', 'NETMIG2011', 'NETMIG2012',
           'NETMIG2013', 'NETMIG2014', 'RESIDUAL2010', 'RESIDUAL2011',
           'RESIDUAL2012', 'RESIDUAL2013', 'RESIDUAL2014', 'GQESTIMATESBASE2010',
           'GQESTIMATES2010', 'GQESTIMATES2011', 'GQESTIMATES2012',
           'GQESTIMATES2013', 'GQESTIMATES2014', 'RBIRTH2011', 'RBIRTH2012',
           'RBIRTH2013', 'RBIRTH2014', 'RDEATH2011', 'RDEATH2012', 'RDEATH2013',
           'RDEATH2014', 'RNATURALINC2011', 'RNATURALINC2012', 'RNATURALINC2013',
           'RNATURALINC2014', 'RINTERNATIONALMIG2011', 'RINTERNATIONALMIG2012',
           'RINTERNATIONALMIG2013', 'RINTERNATIONALMIG2014', 'RDOMESTICMIG2011',
           'RDOMESTICMIG2012', 'RDOMESTICMIG2013', 'RDOMESTICMIG2014',
           'RNETMIG2011', 'RNETMIG2012', 'RNETMIG2013', 'RNETMIG2014'],
          dtype='object')
    


```python
pop2014_by_state = pop2014[pop2014.SUMLEV == 40]
pop2014_by_state.shape
```




    (51, 84)




```python
states = pop2014_by_state[['STNAME','POPESTIMATE2011', 'POPESTIMATE2012', 'POPESTIMATE2013',
'POPESTIMATE2014']]
```


```python
temp = states.sort_values(['POPESTIMATE2014'], ascending=False)[:5]
print(temp)
```

              STNAME  POPESTIMATE2011  POPESTIMATE2012  POPESTIMATE2013  \
    190   California         37701901         38062780         38431393   
    2566       Texas         25657477         26094422         26505637   
    329      Florida         19107900         19355257         19600311   
    1860    New York         19521745         19607140         19695680   
    608     Illinois         12858725         12873763         12890552   
    
          POPESTIMATE2014  
    190          38802500  
    2566         26956958  
    329          19893297  
    1860         19746227  
    608          12880580  
    


```javascript
%%javascript
require.config({
paths: {
d3: '//cdnjs.cloudflare.com/ajax/libs/d3/3.5.5/d3.min'
}
});
```


    <IPython.core.display.Javascript object>



```python
from IPython.core.display import display, Javascript, HTML
display(HTML("""
<style>
.bar {
fill: steelblue;
}
.bar:hover{
fill: brown;
}
.axis {
font: 10px sans-serif;
}
.axis path,
.axis line {
fill: none;
stroke: #000;
}
.x.axis path {
display: none;
}
</style>
<div id="chart_d3" />
"""))
```



<style>
.bar {
fill: steelblue;
}
.bar:hover{
fill: brown;
}
.axis {
font: 10px sans-serif;
}
.axis path,
.axis line {
fill: none;
stroke: #000;
}
.x.axis path {
display: none;
}
</style>
<div id="chart_d3" />




```python
import jinja2
myTemplate = jinja2.Template("""
require(["d3"], function(d3){
var data = []
{% for row in data %}
data.push({ 'state': '{{ row[1] }}', 'population': {{ row[5] }} });
{% endfor %}
d3.select("#chart_d3 svg").remove()
var margin = {top: 20, right: 20, bottom: 30, left: 40},
width = 800 - margin.left - margin.right,
height = 400 - margin.top - margin.bottom;
var x = d3.scale.ordinal()
.rangeRoundBands([0, width], .25);
var y = d3.scale.linear()
.range([height, 0]);
var xAxis = d3.svg.axis()
.scale(x)
.orient("bottom");
var yAxis = d3.svg.axis()
.scale(y)
.orient("left")
.ticks(10)
.tickFormat(d3.format('.1s'));
var svg = d3.select("#chart_d3").append("svg")
.attr("width", width + margin.left + margin.right)
.attr("height", height + margin.top + margin.bottom)
.append("g")
.attr("transform", "translate(" + margin.left + "," + margin.top + ")");
x.domain(data.map(function(d) { return d.state; }));
y.domain([0, d3.max(data, function(d) { return d.population; })]);
svg.append("g")
.attr("class", "x axis")
.attr("transform", "translate(0," + height + ")")
.call(xAxis);
svg.append("g")
.attr("class", "y axis")
.call(yAxis)
.append("text")
.attr("transform", "rotate(-90)")
.attr("y", 6)
.attr("dy", ".71em")
.style("text-anchor", "end")
.text("Population");
svg.selectAll(".bar")
.data(data)
.enter().append("rect")
.attr("class", "bar")
.attr("x", function(d) { return x(d.state); })
.attr("width", x.rangeBand())
.attr("y", function(d) { return y(d.population); })
.attr("height", function(d) { return height - y(d.population); });
});
""");
```


```python
display(Javascript(myTemplate.render(
data=states.sort_values(['POPESTIMATE2012'], ascending=False)[:10].itertuples()
)))
```


    <IPython.core.display.Javascript object>



```python
display(HTML("""
<style>
.bar2011 {
fill: steelblue;
}
.bar2012 {
fill: red;
}
.bar2013 {
fill: yellow;
}
.bar2014 {
fill: green;
}
.axis {
font: 10px sans-serif;
}
.axis path,
.axis line {
fill: none;
stroke: #000;
}
.x.axis path {
display: none;
}
</style>
<div id="chart_d3" />
"""))
```



<style>
.bar2011 {
fill: steelblue;
}
.bar2012 {
fill: red;
}
.bar2013 {
fill: yellow;
}
.bar2014 {
fill: green;
}
.axis {
font: 10px sans-serif;
}
.axis path,
.axis line {
fill: none;
stroke: #000;
}
.x.axis path {
display: none;
}
</style>
<div id="chart_d3" />




```python
import jinja2
myTemplate = jinja2.Template("""
require(["d3"], function(d3){
var data = []
var data2 = []
var data3 = []
var data4 = []
{% for row in data %}
data.push({ 'state': '{{ row[1] }}', 'population': {{ row[2] }} });
data2.push({ 'state': '{{ row[1] }}', 'population': {{ row[3] }} });
data3.push({ 'state': '{{ row[1] }}', 'population': {{ row[4] }} });
data4.push({ 'state': '{{ row[1] }}', 'population': {{ row[5] }} });
{% endfor %}
d3.select("#chart_d3 svg").remove()
var margin = {top: 20, right: 20, bottom: 30, left: 40},
width = 800 - margin.left - margin.right,
height = 400 - margin.top - margin.bottom;
var x = d3.scale.ordinal()
.rangeRoundBands([0, width], .25);
var y = d3.scale.linear()
.range([height, 0]);
var xAxis = d3.svg.axis()
.scale(x)
.orient("bottom");
var yAxis = d3.svg.axis()
.scale(y)
.orient("left")
.ticks(10)
.tickFormat(d3.format('.1s'));
var svg = d3.select("#chart_d3").append("svg")
.attr("width", width + margin.left + margin.right)
.attr("height", height + margin.top + margin.bottom)
.append("g")
.attr("transform", "translate(" + margin.left + "," + margin.top + ")");
x.domain(data.map(function(d) { return d.state; }));
y.domain([0, d3.max(data, function(d) { return d.population; })]);
svg.append("g")
.attr("class", "x axis")
.attr("transform", "translate(0," + height + ")")
.call(xAxis);
svg.append("g")
.attr("class", "y axis")
.call(yAxis)
.append("text")
.attr("transform", "rotate(-90)")
.attr("y", 6)
.attr("dy", ".71em")
.style("text-anchor", "end")
.text("Population");
svg.selectAll(".bar2011")
.data(data)
.enter().append("rect")
.attr("class", "bar2011")
.attr("x", function(d) { return x(d.state); })
.attr("width", x.rangeBand()/4)
.attr("y", function(d) { return y(d.population); })
.attr("height", function(d) { return height - y(d.population); });
svg.selectAll(".bar2012")
.data(data2)
.enter().append("rect")
.attr("class", "bar2012")
.attr("x", function(d) { return (x(d.state)+x.rangeBand()/4); })
.attr("width", x.rangeBand()/4)
.attr("y", function(d) { return y(d.population); })
.attr("height", function(d) { return height - y(d.population); });
svg.selectAll(".bar2013")
.data(data3)
.enter().append("rect")
.attr("class", "bar2013")
.attr("x", function(d) { return (x(d.state)+2*x.rangeBand()/4); })
.attr("width", x.rangeBand()/4)
.attr("y", function(d) { return y(d.population); })
.attr("height", function(d) { return height - y(d.population); });
svg.selectAll(".bar2014")
.data(data4)
.enter().append("rect")
.attr("class", "bar2014")
.attr("x", function(d) { return (x(d.state)+3*x.rangeBand()/4); })
.attr("width", x.rangeBand()/4)
.attr("y", function(d) { return y(d.population); })
.attr("height", function(d) { return height - y(d.population); });
});
""");
```


```python
display(Javascript(myTemplate.render(
data=states.sort_values(['POPESTIMATE2014'], ascending=False)[:5].itertuples()
)))
```


    <IPython.core.display.Javascript object>



```javascript
%%javascript
require.config({
paths: {
d3: '//cdnjs.cloudflare.com/ajax/libs/d3/3.5.5/d3.min',
queue: '//cdnjs.cloudflare.com/ajax/libs/queue-async/1.0.7/queue.min',
topojson: '//cdnjs.cloudflare.com/ajax/libs/topojson/1.6.19/topojson.min'
}
});
```


    <IPython.core.display.Javascript object>



```python
from IPython.core.display import display, Javascript, HTML
display(HTML("""
<style>
.counties {
fill: none;
}
.states {
fill: none;
stroke: #fff;
stroke-linejoin: round;
}
.q0-9 { fill:rgb(247,251,255); }
.q1-9 { fill:rgb(222,235,247); }
.q2-9 { fill:rgb(198,219,239); }
.q3-9 { fill:rgb(158,202,225); }
.q4-9 { fill:rgb(107,174,214); }
.q5-9 { fill:rgb(66,146,198); }
.q6-9 { fill:rgb(33,113,181); }
.q7-9 { fill:rgb(8,81,156); }
.q8-9 { fill:rgb(8,48,107); }
</style>
<div id="choropleth" />
"""))
```



<style>
.counties {
fill: none;
}
.states {
fill: none;
stroke: #fff;
stroke-linejoin: round;
}
.q0-9 { fill:rgb(247,251,255); }
.q1-9 { fill:rgb(222,235,247); }
.q2-9 { fill:rgb(198,219,239); }
.q3-9 { fill:rgb(158,202,225); }
.q4-9 { fill:rgb(107,174,214); }
.q5-9 { fill:rgb(66,146,198); }
.q6-9 { fill:rgb(33,113,181); }
.q7-9 { fill:rgb(8,81,156); }
.q8-9 { fill:rgb(8,48,107); }
</style>
<div id="choropleth" />




```python
import jinja2
choropleth = jinja2.Template("""
require(["d3","queue","topojson"], function(d3,queue,topojson){
// var data = []
// {% for row in data %}
// data.push({ 'state': '{{ row[1] }}', 'population': {{ row[2] }} });
// {% endfor %}
d3.select("#choropleth svg").remove()
var width = 960,
height = 600;
var rateById = d3.map();
ar quantize = d3.scale.quantize()
.domain([0, .15])
.range(d3.range(9).map(function(i) { return "q" + i + "-9"; }));
var projection = d3.geo.albersUsa()
.scale(1280)
.translate([width / 2, height / 2]);
var path = d3.geo.path()
.projection(projection);
//row to modify
var svg = d3.select("#choropleth").append("svg")
.attr("width", width)
.attr("height", height);
queue()
.defer(d3.json, "us.json")
.defer(d3.tsv, "unemployment.tsv", function(d) { rateById.set(d.id, +d.rate); })
.await(ready);
function ready(error, us) {
if (error) throw error;
svg.append("g")
.attr("class", "counties")
.selectAll("path")
.data(topojson.feature(us, us.objects.counties).features)
.enter().append("path")
.attr("class", function(d) { return quantize(rateById.get(d.id)); })
.attr("d", path);
svg.append("path")
.datum(topojson.mesh(us, us.objects.states, function(a, b) { return a !== b; }))
.attr("class", "states")
.attr("d", path);
}
});
""");
```


```python
display(Javascript(choropleth.render()))
```


    <IPython.core.display.Javascript object>



```python
pop2014_by_county = pop2014[pop2014.SUMLEV == 50]
pop2014_by_county.shape
```




    (3142, 84)




```python
# USJSONnames = pd.read_table(urlopen('http://bl.ocks.org/mbostock/raw/4090846/us-countynames.tsv'))
USJSONnames = pd.read_table('world-country-names.tsv')
USJSONnames.shape
```




    (252, 2)


