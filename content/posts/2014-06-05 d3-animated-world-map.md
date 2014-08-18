Title: D3 Animated World Map
Date: 2014-06-16
Tags: D3.js, animation, data, geoJSON
Category: code
Slug: d3-animated-world-map
Author: Zunayed Morsalin
Summary: A  D3 world map and some animation 

<div id="d3_map"></div> 
<script src="http://d3js.org/d3.v3.min.js"></script>

<script>
    
/*global d3: false  */
"use strict";

//set up map
var w = 899;
var h = 500;


//map type & center point 
var projection = d3.geo.mercator()
    .center([-97.109375, 38.664841])
    .scale(500);

var path = d3.geo.path().projection(projection);

//initialize map
var svg = d3.select("#d3_map")
    .append("svg")
    .attr("width", w)
    .attr("height", h);

// seperating out the elements lets us organize the draw order
var country_group = svg.append("g");
var arcGroup = svg.append("g");
var point_group = svg.append("g");


//reading geoJSON & CSV files
d3.json("http://localhost:8000/extras/world_map_resources/countries.json", function(json){
    createMap(json);
});

d3.csv("http://localhost:8000/extras/world_map_resources/cities.csv", function(json){
    createPoints(json);
});

//draw world map
var createMap = function (json) {
    country_group.append("g")
        .selectAll("path")
        .data(json.features)
        .enter()
        .append("path")
        .attr("title", function(d) { return d.id; })
        .style("fill", "#000000")
        .attr("d", path);
};

var createLinks = function (json) { 
    var links = [];
    for(var i=0, len=json.length-1; i<len; i++){
        links.push({
            type: "LineString",
            coordinates: [
                [ json[0].lon, json[0].lat ],
                [ json[i+1].lon, json[i+1].lat ]
                ]
        });
    }
    return links;
};

// place dots on screen based on cities
var createPoints = function(json){

    point_group.selectAll("circle")
        .data(json)
        .enter()
        .append("circle")
        .attr("cx", function(d) { return projection([d.lon, d.lat])[0]; })
        .attr("cy", function(d) { return projection([d.lon, d.lat])[1]; })
        .attr("place", function(d) { return d.place; })
        .attr("r", 3)
        .style("fill", "red")
        // .on("mouseover", mouseover);

    //functions for tweening the path
    var lineTransition = function lineTransition(path) {
        path.transition()
            .duration(3000)
            .attrTween("stroke-dasharray", tweenDash);
    };

    var tweenDash = function tweenDash() {
        var len = this.getTotalLength(),
            interpolate = d3.interpolateString("0," + len, len + "," + len);

        return function(t) { return interpolate(t); };
    };

    //assemble links from office locations
    var links = createLinks(json);

    //append the arcs
    arcGroup.selectAll(".arc")
        .data(links)
        .enter()
        .append("path")
        .attr({ "class": "arc" })
        .attr({ d: path })
        .style({ fill: "none", stroke: "red", "stroke-width": "3px" })
        .call(lineTransition); 
};


//D3 hoverbox info way
// var mouseover = function() {
//     var place = d3.select(this).attr("place");
//     d3.select("#info").html("<b>Contact / " + place + "</b>");
// };
</script>

```javascript
    
/*global d3: false  */
"use strict";

//set up map
var w = 899;
var h = 500;


//map type & center point 
var projection = d3.geo.mercator()
    .center([-97.109375, 38.664841])
    .scale(500);

var path = d3.geo.path().projection(projection);

//initialize map
var svg = d3.select("#d3_map")
    .append("svg")
    .attr("width", w)
    .attr("height", h);

// seperating out the elements lets us organize the draw order
var country_group = svg.append("g");
var arcGroup = svg.append("g");
var point_group = svg.append("g");


//reading geoJSON & CSV files
d3.json("http://localhost:8000/extras/world_map_resources/countries.json", 
    function(json){
        createMap(json);
    }
);

d3.csv("http://localhost:8000/extras/world_map_resources/cities.csv", 
    function(json){
        createPoints(json);
    }
);

//draw world map
var createMap = function (json) {
    country_group.append("g")
        .selectAll("path")
        .data(json.features)
        .enter()
        .append("path")
        .attr("title", function(d) { return d.id; })
        .style("fill", "#43a2ca")
        .attr("d", path);
};

var createLinks = function (json) { 
    var links = [];
    for(var i=0, len=json.length-1; i<len; i++){
        links.push({
            type: "LineString",
            coordinates: [
                [ json[0].lon, json[0].lat ],
                [ json[i+1].lon, json[i+1].lat ]
                ]
        });
    }
    return links;
};

// place dots on screen based on cities
var createPoints = function(json){
    point_group.selectAll("circle")
        .data(json)
        .enter()
        .append("circle")
        .attr("cx", function(d) { return projection([d.lon, d.lat])[0]; })
        .attr("cy", function(d) { return projection([d.lon, d.lat])[1]; })
        .attr("place", function(d) { return d.place; })
        .attr("r", 3)
        .style("fill", "red")
        // .on("mouseover", mouseover);

    //functions for tweening the path
    var lineTransition = function lineTransition(path) {
        path.transition()
            .duration(3000)
            .attrTween("stroke-dasharray", tweenDash);
    };

    var tweenDash = function tweenDash() {
        var len = this.getTotalLength(),
            interpolate = d3.interpolateString("0," + len, len + "," + len);

        return function(t) { return interpolate(t); };
    };

    //assemble links from office locations
    var links = createLinks(json);

    //append the arcs
    arcGroup.selectAll(".arc")
        .data(links)
        .enter()
        .append("path")
        .attr({ "class": "arc" })
        .attr({ d: path })
        .style({ fill: "none", stroke: "red", "stroke-width": "3px" })
        .call(lineTransition); 
};


//D3 hoverbox info way
// var mouseover = function() {
//     var place = d3.select(this).attr("place");
//     d3.select("#info").html("<b>Contact / " + place + "</b>");
// };
```
