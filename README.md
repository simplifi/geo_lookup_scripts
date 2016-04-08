# Geo Lookup Scripts

## CSV to Geo Yaml



#### Usage
```
Converts a csv file with the following columns:

  required: name, address, city, state, zip
  optional: longitude, latitude

outputs the following YAML format:

   ---
   - :name: 'BALLWEG IMPLEMENT CO., INC.: W7246 HWY 68'
     :address: W7246 HWY 68, WAUPUN, WI 53963
     :results:
     - :address: Waupun, WI 53963, USA
       :location_type: APPROXIMATE
       :latitude: 43.6333219
       :longitude: -88.72955189999999

preference of addresses: ROOFTOP, RANGE_INTERPOLATED, GEOMETRIC_CENTER, APPROXIMATE
  -s, --source=<filename/uri>    Source yaml file
  -o, --output=<s>               Output file location (default: )
  -v, --version                  Print version and exit
  -h, --help                     Show this message
```

If the longitude and latitude are not supplied, those values are looked up via the geocoder api.  When doing the lookups, if more than 1 result is returned, all results are included in the format.  Fence Builder looks for the most exact match and chooses that one when building the json output.

## Fence Builder

Converts a geo_yaml file to a geo-json file

#### Usage

```
usage: fence_builder --source filename.yml --output output.json

converts a geo-json yaml file to a geo-json json file

# preference of addresses: ROOFTOP, RANGE_INTERPOLATED, GEOMETRIC_CENTER, APPROXIMATE
  -s, --source=<filename/uri>    Source yaml file
  -o, --output=<s>               Output file location, default is STDOUT (default: -)
  -f, --fence-type=<s>           Which type of geo fence to create, Target or Conversion (default: Target)
  -e, --fence-size=<f>           Size in miles for the bounding box (default: 0.0189394)
  -v, --version                  Print version and exit
  -h, --help                     Show this message
```

default output is to stdout, but can be specified via command line options

## Advanced

These two scripts can be used together like this

```
cat ~/Downloads/north-east-fences.csv| ./csv_to_geo_yaml | ./fence_builder --fence-type Target > north-east-fences.json
```

