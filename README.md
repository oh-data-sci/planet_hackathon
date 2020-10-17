planet.com hackathon
===
# introduction
written during a hackathon sponsored by [planet.com](https://planet.com), with temporary access to their data. we attempted to gauge number and amount of water wells from satellite imagery. 

we had at the start a couple of [geospatial information files](https://www.dropbox.com/sh/ekhbwwdyx70fhs8/AAA_ZF1XXkV7vPTvgv5dX8Fma?dl=0) giving:

- polygon delimiting an area of interest (pabal village in central india).
- locations of 257 wells within that polygon (collected at the ground from the edge of the well)
- final [presentation] (https://docs.google.com/presentation/d/1k5Zsr0MKK5Es7j35ajb5oxdabc-76uTZZiz0ez46AGc/edit?usp=sharing)

we tried to:

- fetch multiband satellite imagery via the planet.com api:
	- limit the data volume and optimise download speeds by using clipping
	- only images within the area of interest 
	- then reduce the area of interest to cut down data volumes and processing times 
	- only images with clear skies
	- ~~only one image per 2 weeks~~
	- collect from october 2019 (when wells should be full) through may 2020 (the driest period of the year)
- compute, for the images the pixel ndwi 
	+ ndwi: a "water index" based on ratio of difference between near infrared and green channel and their sum, 
	+ ndwi: like ndvi, but substitute red and green: (green - ir)/(green+ir)


- find a sensible threshold ndwi value
- filter spots with a high ndwi, 
- and count their area for each image
- track the water area over time. 
- mark the date when >50% of wells are dry.

# files included
data files are excluded from this repository. 

- `first_exploration.ipynb` reads and parses locations from the included `kml` files.
- `download_aoi_from_geojson.ipynb` reads the a geojson file containing the area of interest polygon and converts to query to planet.com api.
-  `rasterio_testing.ipynb` playing with pixel values to compute ndwi.

# lessons learned

- `fastkml` is not a good way to read and extract data from `kml` files, use `elementree` and treat like `xml` instead.
- the planet.com's process for fetching data via their api is:
	- send a search request: one for each chosen band, for the given window, for the given
	- collect search responses
	- for each successful response:
	    + get asset url for a given frame
	    + get the url for the desired asset type, e.g. url for `asset['analytic']` 
	    + activate desired asset via url
	    + await activation to complete
	    + get location url from asset
	    + run the download asset function.
- clipping the data is nontrivial. the clipping requires the `order` endpoint which is out of scope of this hackathon (we were not given access).

