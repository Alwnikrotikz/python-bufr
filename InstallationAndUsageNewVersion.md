# Contents #


# Introduction #

This page describes the installation procedure and usage of the python BUFR decoding and translation packages.

# Installation #

## Dependencies ##

The following dependencies are needed for the full set of python packages to work.
  * ECMWF BUFR decoding library
  * python numpy
  * python sqlalchamy (>= version 0.5)
  * python scientic netcdf
  * python development libraries

If you only need the python ECMWF bufr wrapper you only need the following dependencies.
  * ECMWF BUFR decoding library
  * python numpy

## Installation of debian packages ##

Download the tar-ball containing the ubuntu-hardy debian packages from the download page and untar in a new empty directory.

Enter the directory and run
```
sudo apt-get install  python-numpy python-netcdf # installs dependencies
sudo dpkg -i python-bufr*.deb python-xml-marshall*.deb python-sqlalchemy-marshall*.deb 
```

Notice that if you want to use the database translation tables you will need SQLAlchemy version > 0.5. The default hardy package is 0.4 so if you are using hardy you will need to   install a newer version of SQLAlchemy. The SQLAlchemy dependency is not listed in the python-bufr debian packages so you shouldn't get an error when installing the packages if you don't have the standard python-sqlalchemy package installed.

Numpy and NetCDF dependencies are handled by apt.

The python bufr packages will install into the `/opt` directory.

## Installation from source ##

Download the tar-ball containing the source packages from the download page

Make sure that numpy, SQLAlchamy, Scientific.NetCDF and ECMWF's bufr decoding library is installed.

untar the source tar-ball and the seperate python packages inside. Enter the packages in the following sequence and run `python setup.py install` in each package.
  * bufr
  * xml\_marshall
  * sqlalchemy\_marshall
  * metadb
  * transform

When running the `setup.py install` in the bufr package you will be prompted to set environment variables pointing at your ECMWF bufr library directory and bufr tables directory.

The python install scripts will install packages in the normal python path and scripts in `/usr/bin`

## Database API's ##

Notice that in order to use SQLAlchemy you will need to have some database API's installed. A number of backends are possible please refer to SQLAlchemy documentation.

If you are using ubuntu a good test backend is the sqlite database. To install run
```
sudo apt-get install python-sqlite
```

# Usage #

The debian packages install the python packages and scripts in `/opt` hence you will need to set the following variables (in bash)
```
export PATH=$PATH:/opt/bin
export PYTHONPATH=/opt/lib/python2.5/site-packages/
```

If you have an alternative path to your bufr tables you will need to set
```
export BUFR_TABLES=/path/bufrtables/
```
**Remember** the trailing `/`

## Reading and Inspecting BUFR data ##

This assumes the above variables are correctly set.

```
Python 2.5.2 (r252:60911, Jul 22 2009, 15:35:03) 
[GCC 4.2.4 (Ubuntu 4.2.4-1ubuntu3)] on linux2
Type "help", "copyright", "credits" or "license" for more information.
>>> import bufr
>>> bfr = bufr.BUFRFile('NPR_TDIB.SA_D10108_S1823_E2008_B3353637_NS')
```
This will open the BUFR file.
```
>>> n = bfr.next()
                   ECMWF 
 
      BUFR DECODING SOFTWARE VERSION -  7.2 
            1 APRIL  2007. 
 
 
 
 Your path for bufr tables is :
 /home/krl/bufrtest/bufrtables/       
BUFR TABLES TO BE LOADED  B0000000300007004001.TXT,D0000000300007004001.TXT
```
The BUFRFile object is a iterator and supports the 'next()' method. This means that you can use the BUFRFile object in a for-loop `for n in bfr: print n[0]`

```
>>> n = bfr.next()
BUFR TABLES TO BE LOADED  B0000000000074002001.TXT,D0000000000074002001.TXT
>>> len(n)
516
```

The 'n' variable now contains a list of BUFRFileEntry objects. Each object contains a name, unit, index and data attribute. To print the names of all entries use `for i in n: print i.name`

```
>>> n[0]
<_BUFRFile.BUFRFileEntry object at 0xab0ea758>
>>> n[0].name
'SATELLITE IDENTIFIER                                            '
>>> n[0].unit
'CODE TABLE 1007         '
>>> n[0].index
0
>>> n[0].data
array([ 249.,  249.,  249.,  249.,  249.,  249.,  249.,  249.,  249.,
        249.,  249.,  249.,  249.,  249.,  249.,  249.,  249.,  249.,
...snip...
        249.,  249.,  249.,  249.,  249.,  249.,  249.,  249.,  249.,
        249.,  249.,  249.,  249.,  249.,  249.,  249.,  249.,  249.])
>>> n[2].name
'YEAR                                                            '
>>> n[10].name
'LATITUDE (COARSE ACCURACY)                                      '
>>> n[10].data
array([ 47.4 ,  47.5 ,  47.61,  47.71,  47.82,  47.92,  48.02,  48.12,
        48.22,  48.32,  48.42,  48.52,  48.62,  48.71,  48.81,  48.9 ,
...snip...
        45.6 ,  45.49,  45.38,  45.26,  45.15,  45.04,  44.92,  44.81,
        44.7 ,  44.58,  44.47,  44.35])
```

If all entries in a array are identical it is possible to pack then into a single value.

```
>>> bufr.pack_record(n[0])
249
```

To dump all names and data in the above example use
```
bfr_file = bufr.BUFRFile('NPR_TDIB.SA_D10108_S1823_E2008_B3353637_NS')

#iterate though file using 
for record in bfr_file:
    # iterate through all entries for this record
    for entry in record:
        print entry.name
        print entry.unit
        print entry.index
        print entry.data # for satellite data this will be a numpy array
```



## Creating and Using Translation Database ##

This section assumes you have the python sqlite bindings installed.

The 'import\_bfr' script can import BUFR file variables into a new or existing database ... but first usage !!
```
$> import_bfr 
 Not enough arguments 
USAGE:
    import_bfr [-c connection url] [-s start index] <-i instrument> <-g glob> filename 
    
    filename    :   Can either be an xml file or a BUFR file. File type is determined using
                    filename extension. 

    -i          :   instrument, only needed when importing BUFR files, otherwise ignored
    -g          :   glob, only needed when importing BUFR files, otherwise ignored
    -s          :   start index , used when imporing BUFR files
    
```
You always need to define the '-c' option which defines the database url.

Now lets try to import the variable names from a AMSRE BUFR file.
```
$> import_bfr -c 'sqlite:///new_bufr.db' -s 0 -i AMSRE_TDEB -g "NPR_ARBT.*" NPR_ARBT.AQ_D10100_S1139_E1229_B4220606_Ud
                   ECMWF 
 
      BUFR DECODING SOFTWARE VERSION -  7.2 
            1 APRIL  2007. 
 
 
 
 Your path for bufr tables is :
 /home/krl/bufrtest/bufrtables/       
BUFR TABLES TO BE LOADED  B0000000000000011000.TXT,D0000000000000011000.TXT
```
Notice the '-i' option defines the top level name, that we want to store the AMSRE BUFR file variable names under. So from now on the AMSRE bufr variable names are stored under 'AMSRE\_TDEB'.

Now for the BUFR to NetCDF translation using the newly created raw translation table. First inspect the database of instrument types.
```
krl@solsikker:pybufr_test> bufr2netcdf -c 'sqlite:///new_bufr.db' -l 
AMSRE_TDEB	
```
We can see that the 'AMSRE\_TDEB' name exists in our translation database.

Now convert input BUFR file using the translation tables for the `AMSRE_TDEB` instrument type.
```
$> bufr2netcdf -c 'sqlite:///new_bufr.db' AMSRE_TDEB NPR_ARBT.AQ_D10100_S1139_E1229_B4220606_Ud amsre.nc
                   ECMWF 
 
      BUFR DECODING SOFTWARE VERSION -  7.2 
            1 APRIL  2007. 
 
 
 
 Your path for bufr tables is :
 /home/krl/bufrtest/bufrtables/       
BUFR TABLES TO BE LOADED  B0000000000000011000.TXT,D0000000000000011000.TXT

```

Notice that the output NetCDF file is properly large and variable names are not descriptive. To crop out unwanted variables please see section below.

## Editing Translation Database ##

It is possible to either edit the database directly through SQL statements or by exporting the database to XML, edit the XML file and re-import the file.

See example below.

Export instrument translation table to XML file
```
$> export_bfr -c 'sqlite:///new_bufr.db' -i AMSRE_TDEB > amsr_mess.xml
```

An example snippet is shown below

```
$> xmllint --format amsr_mess.xml > amsr_pretty.xml
$> head -30 amsr_pretty.xml 
<?xml version="1.0"?>
<BUFRDesc>
  <int name="id">1</int>
  <unicode name="name">AMSRE_TDEB</unicode>
  <unicode name="description"/>
  <unicode name="fn_regex">NPR_ARBT.*</unicode>
  <unicode name="title"/>
  <unicode name="institution"/>
  <unicode name="source"/>
  <unicode name="history">Converted from BUFR</unicode>
  <unicode name="references"/>
  <unicode name="comments"/>
  <InstrumentedList name="bufr_var">
    <BUFRVar>
      <int name="id">1</int>
      <unicode name="name">SATELLITE IDENTIFIER                                            </unicode>
      <int name="bdesc_id">1</int>
      <int name="seq">0</int>
      <InstrumentedList name="bufr_param">
        <BUFRParam>
          <int name="id">1</int>
          <unicode name="bparam_data">CODE TABLE 1007         </unicode>
          <int name="bufr_var_id">1</int>
          <int name="bufr_param_type_id">2</int>
          <BUFRParamType name="bufr_param_type">
            <int name="id">2</int>
            <int name="bufr_data_type_id">3</int>
            <unicode name="name">bufr_unit</unicode>
            <BUFRDataType name="bufr_data_type">
              <int name="id">3</int>


```

Notice the structure is
  * BUFRDesc (instrument type)
    * BUFRVar (variable e.g. satellite identifier)
      * BUFRParam (Parameter assigned to the specific variable, this is just a data container. It could contain the netcdf name for this variable)
        * BUFRParamType (Determines the parameter type e.g. netcdf\_name)

The easiest way to crop out unwanted variables is to substitute the 'bparam\_data' field of the BUFRParam's with the 'netcdf\_name' BUFRParameterType with a specific tag e.g. 'delete\_me'. Remember only to do it for the BUFRParams below the BUFRVariables you wish to exclude.

Then re-import the XML file
```
import_bfr -c 'sqlite:///new_bufr.db' amsr_pretty.xml
...output...
```

Then connect to the database and run
```
delete from bufr_param where bufr_param_type_id = 9 and bufr_data = 'delete_me'
```

Where '9' is the id of the 'netcdf\_name' type. You can verify this by inspecting the database bufr\_param\_type table.

BUFRVar's missing BUFRParam's of type 'netcdf\_name' are excluded from the BUFR to NetCDF translation.