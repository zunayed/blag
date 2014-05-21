Title: Knicks Stats →
Date: 2014-05-20
Tags: Javascript, D3.js, data, visualization
Category: code
Slug: knicks-stats
Author: Zunayed Morsalin
Summary: Scraping nba.com and making a cloropeth of knicks failure 

## Live demo - http://knicks-stats.dtostillwell.com/
-<img width="960" src="/images/knicks_stats.png"  title="Knicks" />


The nba stats [website](http://www.nba.com/knicks/stats/team) is an excelent place to get all kinds of info about a team. However the is no easy way to get that data yourself without some web scrapping. I found some apis but they all cost hundreds of dollars a month :(

So I wrote a quick and dirty python script to get the data I needed. The BeatifulSoup library is a great tool for working with the DOM. 

```python
def parse_html_data(location):
    soup = BeautifulSoup(open(location))
    data = {}
    all_tr = soup.find_all("tr")

    for item in all_tr:
        game_id = item.find_all("td", {'class': "col-Game_ID_SORT"})
        fg_pct = item.find_all("td", {'class': "col-FG_PCT"})
        fg3_pct = item.find_all("td", {'class': "col-FG3_PCT"})

        a = [d.get_text() for d in game_id]
        b = [d.get_text() for d in fg_pct]
        c = [d.get_text() for d in fg3_pct]

        if len(a) > 0:
            team = re.sub('[@, vs , .]', '', a[0][19:])

            if team in data:
                data[team]['fg %'].append(float(b[0].replace("%", "")))
                data[team]['3pt %'].append(float(c[0].replace("%", "")))
            else:
                data[team] = {
                    'fg %': [float(b[0].replace("%", ""))],
                    '3pt %': [float(c[0].replace("%", ""))]
                }

    return data

```

I had to clean up the data and associate the teams to states for the country map. Once I had the data I used D3.js to help me draw geoJSON map data along with the scalar values need to color each state. 

```javascript
/*global d3: false  */
"use strict";

//data
var knicks_data

var dataSets = {
    "fg %": {
        "name": 'fg %',
        "color": "Greens",
        "minDomain": 30,
        "maxDomain": 50
    },
    "3pt %": {
        "name": '3pt %',
        "color": "Greens",
        "minDomain": 15,
        "maxDomain": 50

    }
};

var current = dataSets['fg %']
var states_data


//set up map
var w = screen.width;
var h = screen.height;

//map type & center point 
var projection = d3.geo.mercator()
    .center([-101.381836, 42.587699])
    .scale(1000);

var path = d3.geo.path().projection(projection);

//set up a qX-9 number to associate with colorbrew.css styles
var setColor = d3.scale.quantize()
    .domain([current.minDomain, current.maxDomain])
    .range(d3.range(1,9).map(function(i) { return "q" + (i) + "-9"; }));


//initialize map
var svg = d3.select("#d3_map")
    .append("svg")
    .attr("width", w)
    .attr("height", h);


// create a container for counties
var counties = svg.append("g")
    .attr("id", "counties")
    .attr("class", current.color);


//reading geoJSON & CSV files
d3.json("static/data/us_states_small_size.json", function(states){
    states_data = states
    createMap(states_data);
});

//reading data file
d3.json("static/data/data.json", function(data){
    knicks_data = data
    // counties.selectAll("path")
    //   .attr("class", stateColor);
});


//map number of complaint to color intensity
var stateColor = function(state) {
    if(state in knicks_data){
        console.log(knicks_data['Texas'][current.name])
        return setColor(knicks_data[state][current.name]);
    }else{
        //no data
        return "q1-9";
    }
};

//draw world map
var createMap = function (states) {
    counties.append("g")
        .selectAll("path")
        .data(states.features)
        .enter()
        .append("path")
        // .style("fill", "#43a2ca")
        .attr("state", function(d) { return d.properties['NAME'] } )
        .attr("class", function(d) { return stateColor(d.properties['NAME']) } )
        .attr("stroke", "#fff")
        .on("mouseover", mouseover)
        .on("mouseout", mouseout)
        .attr("d", path);
};


//D3 hoverbox info
var mouseover = function() {
    d3.select(this).style("stroke-width", "4px");
    var state_name = d3.select(this).attr("state");

    if(state_name in knicks_data){
        var fg = (knicks_data[state_name][current.name]).toFixed(2);
    }else{
        //no data
        var fg = 'N/A'
    }

    d3.select("#infoBox").html("State: " + state_name + ' ' + 'FG %: ' + fg);
};


var mouseout = function() {
    d3.select(this).style("stroke-width", "");
    d3.select(this).style("stroke", "#fff");
};

//monitor dropdown menu to change map data
d3.select("#dataSelector").on("change", function() {
    current = dataSets[this.value]

    setColor.domain([current.minDomain, current.maxDomain]);
    createMap(states_data);

    counties.attr("class", current.color);
});

```
