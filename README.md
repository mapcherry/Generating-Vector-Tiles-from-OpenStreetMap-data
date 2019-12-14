# Generating VectorTiles from OpenStreetMap data workshop

## Prerequisites
* Docker
* Git
* Git bash (for windows)
* make (  [download for windows](http://gnuwin32.sourceforge.net/packages/make.htm) ) 



## Clone OpenMapTiles repository

```
git clone git@github.com:openmaptiles/openmaptiles.git
cd openmaptiles
```

## Code generating from the layer definitions ( ./build/mapping.yaml; ./build/tileset.sql )
```make```

## Download PBF file from Geofabrik
* PBFs can be downloaded from [GeoFabrik](http://download.geofabrik.de/)

## Clip area you want to import using osmconvert 

Clipping based on Longitude and Latitude

```osmconvert file.pbf -b=23.292333,46.626013,23.933521,46.956346 -o=clipped.pbf```

Clipping based on a Polygon

```osmconvert file.pbf -B=area.poly -o=clipped.pbf```


## Copy PBF to import inside openmaptiles/data

```sh
mkdir data
cp /path/to/file.pbf data/file.pbf
```

## Start PostgreSQL
```docker-compose up -d postgres```

## Import water, natural earth, lake lines and borders
```sh
docker-compose run import-water
docker-compose run import-natural-earth
docker-compose run import-lakelines
docker-compose run import-osmborder
```

## Import pbf files from ./data into PostgreSQL
```docker-compose run import-osm```


## Start SQL postprocessing:  ./build/tileset.sql -> PostgreSQL "
```make clean && make && make import-sql```

## Generate vector tiles for a defined bounding box
```docker-compose run -e BBOX="23.292333,46.626013,23.933521,46.956346" generate-vectortiles```

## Start the server serve the tiles
 * run this in CMD under Windows
```docker run --rm -it -v /c/users/path/to/openmaptiles/data:/data -p 8080:80 klokantech/tileserver-gl```


