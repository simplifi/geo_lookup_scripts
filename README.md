# Geo Lookup Scripts

## Requirements
* ruby 2.1+  

Follow the instructions here for ruby installer
https://github.com/postmodern/ruby-install#readme

Then install Ruby:
```
ruby-install ruby
```
    

* bundler 

``` 
# install bundler
gem install bundler 

# cd into the directory for the scripts:
cd geo_lookup_scripts 

# install all the dependencies using bundler
bundle install
```

## CSV to Geo Yaml

#### Configuration

copy the `config.yml.example` file to `config.yml` and replace the placeholders with your correct authentication information. 

Key signup: [https://developers.google.com/maps/documentation/business/](https://developers.google.com/maps/documentation/business/)

#### Usage
```
Converts a csv file with the following columns:

  required: name, address, city, state, zip
  optional: longitude, latitude, radius, label, fence_type

  radius      - expressed in miles
  label       - will override the default label of "name: address"
  fence_type  - valid values are Target, Conversion or Event, default is Target

outputs the following YAML format:

   ---
   - :name: 'BALLWEG IMPLEMENT CO., INC.: W7246 HWY 68'
     :address: W7246 HWY 68, WAUPUN, WI 53963
     :label: 'My Geofence #1'
     :fency_type: Target
     :results:
     - :address: Waupun, WI 53963, USA
       :location_type: APPROXIMATE
       :latitude: 43.6333219
       :longitude: -88.72955189999999
 
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
  -f, --fence-type=<s>           Which type of geo fence to create, Target, Conversion, or Event (default: Target)
  -e, --fence-size=<f>           Size in miles for the bounding box (default: 0.062)
  -v, --version                  Print version and exit
  -h, --help                     Show this message
```

Default output is to stdout, but can be specified via command line options.  When fence builder encounters an address with more than one matching location, it chooses the most exact match available to output as the fence.  The order of preference is `ROOFTOP, RANGE_INTERPOLATED, GEOMETRIC_CENTER, APPROXIMATE.`  If no locations are assigned, the address will be output to STDERR and no fence will be built for that address.

## Helper methods

`run_for_dir` takes a folder name as it's one argument and will process all the csv files inside

`run_for_single` takes a file name as it's one argument and will process the the csv file

Both helpers will place the file(s) in the output directory and will try to be smart about repeating jobs.  If it finds the destination file in the output, it will skip over that iteration.  Both helper files require the use of gsed on the mac.  gsed can be install via `brew install gsed`.  For more info on homebrew - [http://brew.sh](http://brew.sh)


At their heart, both of these functions replace MSDOS format line endings with unix style, and then remove any unprintable ascii characters.  This will usually strip the input of any UTF-8 special characters which normally result in worse search results.

```
cat ~/Downloads/north-east-fences.csv | LC_ALL=C gsed 's/\\o015/\\n/g' | \
LC_ALL=C gsed 's/[^[:print:]]//g' | ./csv_to_geo_yaml | \
./fence_builder > ./outpus/north-east-fences.json
```

