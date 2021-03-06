# csw-to-geoblacklight
Pull ISO 19139 metadata from a CSW and populate a GeoBlacklight Solr index, and export to opengeometadata

## Requirements
Ought to work with Python 2.7+ and 3+

## Installation
1. Download/clone this repository.
2. Install required Python modules using `pip install -r requirements.txt`

## Configuration
1. Make a copy of config.py.sample.
2. Rename it to config.py.
3. Fill it out.

## Usage
Fire up the ol' command line, my friend.  

```
usage: csw-to-geoblacklight.py [-h] [-r] [-pi PROVENANCE_INSTITUTION]
                               [-c COLLECTION] [-md]
                               [-csv | -j | -x | -ogm TO_OPENGEOMETADATA]
                               (-aj ADD_JSON | -cat BY_CATEGORY | -p PATH_TO_CSV | -i INSTITUTION | -s SINGLE_RECORD_UUID | -v SINGLE_VIRTUAL_CSW | -d DELETE_RECORDS_INSTITUTION)

optional arguments:
  -h, --help            show this help message and exit
  -r, --recursive       If input involves a folder, recurse into subfolders.
  -pi PROVENANCE_INSTITUTION, --provenance-institution PROVENANCE_INSTITUTION
                        The institution to assign dct_provenance_s to. If
                        provided, this will speed things up. But make sure
                        it's applicable to _all_ records involved. Valid
                        values are one of the following : iowa, illinois,
                        mich, minn, msu, psu, purdue, umd, wisc
  -c COLLECTION, --collection COLLECTION
                        The collection name (dc_collection) to use for these
                        records. Added as XSL param
  -md, --metadata-link  If set, will add a link to the ISO19139 metadata to
                        the GeoBlacklight JSON in the form of an
                        OpenGeometadata URL. Specifically, the url will look
                        something like: https://opengeometadata.github.io/{rep
                        o}/07/22/47/a0c6fb4d9a9a9b67e578f2ac50/iso19139.xml.
  -csv, --to-csv        Output to CSV.
  -j, --to-json         Outputs GeoBlacklight JSON files.
  -x, --to-xml          Outputs ISO19139 XML files.
  -ogm TO_OPENGEOMETADATA, --to-opengeometadata TO_OPENGEOMETADATA
                        Outputs ISO19139 XMLs and GeoBlacklight JSON files to
                        a folder name specified.
  -aj ADD_JSON, --add-json ADD_JSON
                        Indicate path to folder with GeoBlacklight JSON files
                        that will be uploaded.
  -cat BY_CATEGORY, --by-category BY_CATEGORY
                        Indicate GeoNetwork Category to pull records from.
  -p PATH_TO_CSV, --path-to-csv PATH_TO_CSV
                        Indicate path to the csv containing the record uuids
                        to update.
  -i INSTITUTION, --institution INSTITUTION
                        The institution to harvest records for. Valid values
                        are one of the following : iowa, illinois, mich, minn,
                        msu, psu, purdue, umd, wisc
  -s SINGLE_RECORD_UUID, --single-record-uuid SINGLE_RECORD_UUID
                        A single uuid to update
  -v SINGLE_VIRTUAL_CSW, --single-virtual-csw SINGLE_VIRTUAL_CSW
                        A virtual csw to harvest records from. Provide the
                        text that follows 'csw-'
  -d DELETE_RECORDS_INSTITUTION, --delete-records-institution DELETE_RECORDS_INSTITUTION
                        Delete records for an instution. Valid values are one
                        of the following : iowa, illinois, mich, minn, msu,
                        psu, purdue, umd, wisc
```

## Examples

Say you wanted to request the records that match the GeoNetwork category "Minnesota Geospatial Commons" and output the transformed records as individual JSON (each record gets a folder named for its uuid and the output file is called `geoblacklight.json`):
```
./csw-to-geoblacklight.py -cat "Minnesota Geospatial Commons" -j
```

Or to write the ISO1939 XMLs to files (each record gets a folder named for its uuid and the output file is called `iso19139.xml`):
```
./csw-to-geoblacklight.py -cat "Minnesota Geospatial Commons" -x
```

Or to a CSV (helpful for checking for problems) called `gblout.csv`:
```
./csw-to-geoblacklight.py -cat "Minnesota Geospatial Commons" -csv
```

Or to both XML and JSON, along with a `layers.json` file for [OpenGeometadata](https://github.com/OpenGeoMetadata), include `-ogm` and the name of the folder to use as the base for output (it will be created if it doesn't exist):
```
./csw-to-geoblacklight.py -cat "Minnesota Geospatial Commons" -ogm minn-geo-commons
```

Since we know that the institution for all of the Minn. Geo. Commons is Minnesota, you can provide the `provenance-institution` using `-pi`, which saves time:
```
./csw-to-geoblacklight.py -cat "Minnesota Geospatial Commons" -j -pi minn
```

To publish the same category to Solr, under the collection name "MN Geospatial Commons":
```
./csw-to-geoblacklight.py -cat "Minnesota Geospatial Commons" -c "MN Geospatial Commons" -pi minn
```

To update a single record (useful when making minor fixes for individual records), you need to provide is the uuid:
```
./csw-to-geoblacklight.py -s ff0f7dcb-3a4a-42ac-b106-5d92bd48bf14
```

Don't forget the Collection if it belongs to one already:
```
./csw-to-geoblacklight.py -s ff0f7dcb-3a4a-42ac-b106-5d92bd48bf14 -c "MN Geospatial Commons"
```

To add a folder full of GeoBlacklight JSON files to Solr:
```
./csw-to-geoblacklight.py -aj ./path/to/jsons
```

To add a folder full of folders that contain GeoBlacklight JSON files to Solr, add the recursive flag `-r`:
```
./csw-to-geoblacklight.py -aj ./base/path -r
```
