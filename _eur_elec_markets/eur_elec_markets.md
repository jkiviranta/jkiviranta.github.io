---
title: "Portfolio item number 1"
excerpt: "Short description of portfolio item number 1<br/><img src='/images/500x300.png'>"
collection: portfolio
---


# Interactive Map of Europe

To view the interactive map, please open this file in a browser that supports HTML rendering.

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Interactive Map of Europe</title>
    <style>
        #map {
            width: 100%;
            height: 600px;
            border: 1px solid black;
        }
        .country {
            fill: white;
            stroke: black;
            stroke-width: 0.5;
        }
        .country:hover {
            fill: lightblue;
        }
        #info {
            position: absolute;
            top: 10px;
            right: 10px;
            background-color: white;
            border: 1px solid black;
            padding: 10px;
            display: none;
        }
    </style>
</head>
<body>
    <div id="map"></div>
    <div id="info"></div>

    <script src="https://d3js.org/d3.v6.min.js"></script>
    <script>
        const width = 960;
        const height = 600;

        const svg = d3.select("#map")
            .append("svg")
            .attr("width", width)
            .attr("height", height);

        const projection = d3.geoMercator()
            .center([13, 52])
            .scale(500)
            .translate([width / 2, height / 2]);

        const path = d3.geoPath().projection(projection);

        Promise.all([
            d3.json("https://raw.githubusercontent.com/deldersveld/topojson/master/continents/europe.json"),
            d3.json("countries_data.json")
        ]).then(([mapData, countryData]) => {
            svg.selectAll(".country")
                .data(topojson.feature(mapData, mapData.objects.europe).features)
                .enter().append("path")
                .attr("class", "country")
                .attr("d", path)
                .on("mouseover", function(event, d) {
                    const countryName = d.properties.name;
                    const data = countryData[countryName] || {};
                    d3.select(this).attr("fill", "lightblue");
                    d3.select("#info").style("display", "block").html(`
                        <strong>${countryName}</strong><br>
                        Population: ${data.population || "N/A"}<br>
                        Area: ${data.area || "N/A"}
                    `);
                })
                .on("mouseout", function() {
                    d3.select(this).attr("fill", "white");
                    d3.select("#info").style("display", "none");
                });
        });
    </script>
</body>
</html>
