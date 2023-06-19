<img src="images/NetMob_white.png" width="35%" height="100%"/> 

# Netmob 2023 Data Challenge

<a href="https://api.mapbox.com/styles/v1/14ndy15/clhrwb13x021g01pggpis9c01.html?title=view&access_token=pk.eyJ1IjoiMTRuZHkxNSIsImEiOiJjbGhydmN6MzAwMTNoM2RvMXdueDlsNDB6In0.weceUml_7pXytBkhTclQCQ&zoomwheel=true&fresh=true#6.81/48.178/2.199/29.9/56" target="_blank">
  <img src="images/YouTube_3D_map.png" width="100%" height="100%"/> 
</a>


<a href="https://api.mapbox.com/styles/v1/14ndy15/clhrwb13x021g01pggpis9c01.html?title=view&access_token=pk.eyJ1IjoiMTRuZHkxNSIsImEiOiJjbGhydmN6MzAwMTNoM2RvMXdueDlsNDB6In0.weceUml_7pXytBkhTclQCQ&zoomwheel=true&fresh=true#6.81/48.178/2.199/29.9/56" target="_blank">Youtube 3D traffic map</a> in 20 France cities. The map is interactive, you can zoom in/out and rotate the map.

## Data access

The access to the data is **restricted to the participants** of the [Netmob 2023 Data Challenge](http://netmob2023challenge.networks.imdea.org/) and to the [terms and conditions](https://netmob2023challenge.networks.imdea.org/terms-and-conditions/).


The NetMob 2023 Data Challenge is a unique opportunity to access data that is typically **very difficult to come by**, and employ it to generate new knowledge, support innovation, prove theories at scale, or validate solutions in real-world settings. Prospective participants shall apply to the challenge by registering at https://netmob2023challenge.networks.imdea.org/. Complete instructions are provided at the same webpage.

## Data description

The dataset is composed by:
* 20 urban areas in France (Paris, Lyon, Marseille, Toulouse, Nice, etc.)
* 68 mobile services (including YouTube, Netflix, Facebook, Instagram, Gmail, etc.)
* 77 days continuous days
* 100 x 100 m2 spatial resolution
* 15 minute temporal resolution
* 400+ billion data points
* 2.3+ TB of data

The full description of the dataset and the methodology can be found in our pre-print available here: [The NetMob23 Dataset: A High-resolution Multi-region Service-level Mobile Data Traffic Cartography](https://arxiv.org/abs/2305.06933).

<img src="images/paper_front.png" width="50%" height="100%"/> 

Please cite our work, when using the dataset:

```bibtex
@misc{netmob23, 
  title={The NetMob23 dataset: A high-resolution multi-region service-level mobile data traffic cartography}, 
  author={Martínez-Durive, Orlando E and Mishra, Sachit and Ziemlicki, Cezary and Rubrichi, Stefania and Smoreda, Zbigniew and Fiore, Marco}, 
  year={2023}, 
  eprint={2305.06933}, 
  archivePrefix={arXiv}, 
  primaryClass={cs.NI} 
}
```

### Spatial dataset

The spatial dataset is composed of 20 France cities.
However, the formal definition in most of the cases is [Metropole](https://en.wikipedia.org/wiki/M%C3%A9tropole), an administrative entity in France, in which several communes cooperate, and which has the right to levy local tax, an établissement public de coopération intercommunale à fiscalité propre.

The list of communnes that compose these cities are given in the [`cities_communes_code.json`](data/cities_communes_code.json) file, while the list of IRIS (smaller administrative units) are given in the [`cities_iris_code.json`](data/cities_iris_code.json) file.
GeoJson files for cities, using communes are in the [`cities_communes`](data/cities_communes) folder, while the ones using IRIS are in the [`cities_iris`](data/cities_iris) folder.


Each one is represented by a geojson file, that contains the grid of the city composed of 100 x 100 m<sup>2</sup> tiles. 
The tile (feature) is represented by a polygon using [WGS84](https://en.wikipedia.org/wiki/World_Geodetic_System). 



<img src="images/cities_density.png" width="60%" height="100%"/> 

```json
{"type": "Feature",
 "geometry": {"type": "Polygon",
  "coordinates": [[
    [4.7662070878542515, 45.55631465259445],
    [4.766246657177647, 45.55721386239888],
    [4.767526651889026, 45.5571860567685],
    [4.767487061740877, 45.55628684742171],
    [4.7662070878542515, 45.55631465259445]
    ]]},
 "properties": {"tile_id": 66}}
 ```

By using the `tile_id` we can also represent the spatial information in a matrix form; where each tile represented a matrix cell. 

$row_{index} = floor(\frac{tile_{id}}{cols}) $

$col_{index} = tile_{id} \mod cols$

The matrix dimension (rows, cols) are given in the `cities_dims` dictionary.

```python
cities_dims = {
    "Bordeaux": (334, 342), 
    "Clermont-Ferrand": (208, 268),
    "Dijon": (195, 234),
    "Grenoble": (409, 251),
    "Lille": (330, 342),
    "Lyon": (426, 287),
    "Mans": (228, 246),
    "Marseille": (211, 210),
    "Metz": (226, 269),
    "Montpellier": (334, 327),
    "Nancy": (151, 165),
    "Nantes": (277, 425),
    "Nice": (150, 214),
    "Orleans": (282, 256),
    "Paris": (409, 346),
    "Rennes": (423, 370),
    "Saint-Etienne": (305, 501),
    "Strasbourg": (296, 258),
    "Toulouse": (280, 347),
    "Tours": (251, 270)
    }
```


The notebook [Regions.ipynb](Regions.ipynb) contains code snippets of how to load the geojson files and plot the regions. For a simple visualization, you can just load the geojson file in the [geojson.io](https://geojson.io/) website.

<!-- <img src="images/tile.png" width="45%" height="100%"/>  -->

### Traffic dataset

The traffic dataset is composed of **209440** plain text files.
Each one for each combination of city, service, day and direction of the traffic. 
A given file, contains the traffic record for all the tiles of the city every 15 minutes.

<img src="images/traffic_records.png" width="80%" height="100%"/> 

Due to daylight saving time in France on **March 31, 2019**, the traffic records for that day have **92-time columns** instead of 96.
These missing timestamps are between 02:00 and 03:00 French local time. Therefore, the first 8-time columns cover the period from 00:00 to 01:45, and the 9-time column corresponds to 03:00.

The notebook [Traffic.ipynb](Traffic.ipynb) contains code snippet on how to load the traffic records from the txt files and plot traffic maps.

<img src="images/city_traffic_maps.png" width="80%" height="100%"/> 

Also, how is possible to aggregate the traffic records over space to obtain traffic time series.

<img src="images/city_time_series.png" width="80%" height="100%"/>