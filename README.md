planet.com hackathon
===
# introduction
written during a hackathon sponsored by [planet.com](https://planet.com), with temporary access to their data. we attempted to gauge number and amount of water wells from satellite imagery. 

we had at the start a couple of [geospatial information files](https://www.dropbox.com/sh/ekhbwwdyx70fhs8/AAA_ZF1XXkV7vPTvgv5dX8Fma?dl=0) giving:

- polygon delimiting an area of interest (pabal village in central india).
- locations of 257 wells within that polygon (collected at the ground from the edge of the well)

we tried to:

- fetch multband satellite imagery via the planet.com api:
	- limit the data volume and optimise download speeds by using clipping
	- only images within the area of interest
	- only images with clear skies
	- only one image per 2 weeks
	- should cover the area of interest
	- should range from october (when wells should be full) through may (the driest period)
- compute, for the images the pixel ndwi (a "water index" based on ratio of near infrared and green channel)
- find a sensible threshold value, filter spots with a high ndwi, and count their area.

# files included
data files are excluded from this repository. 

- `first_exploration.ipynb` reads and parses locations from the included `kml` files.
- `download_aoi_from_geojson.ipynb` reads the a geojson file containing the area of interest polygon and converts to query to planet.com api.
- 
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
