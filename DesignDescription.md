Work in progress

# Design Strategy #

The overall design strategy for this project is to provide a series of
interdependent python packages that can ease the decoding and
transformation of BUFR files. The packages should be as confined as
possible and as generic as possible.

# Design Description #

The BUFR transformation can be divided into three functional layers.
  1. An input layer / Toolbox layer
  1. Translation layer
  1. Output generation layer

Overview figure shown below.

![http://python-bufr.googlecode.com/svn/trunk/doc/images/bufr-python-packages.png](http://python-bufr.googlecode.com/svn/trunk/doc/images/bufr-python-packages.png)

## Input Layer / Toolbox Layer ##

The first layer provides toolbox packages which are utilised in the
above layers but these packages can also be used on their own. The
packages in the input layer are:
  * bufr --- python c-wrapper for the ECMWF BUFR decoding library
  * xml\_marshall --- Provides generic xml serialisation and import functions. Provides hooks for advanced python objects.
  * sqlalchemy\_marshall --- Provides generic methods for database xml-serialisation and import. Depends on the xml\_marshall package.

The bufr package can be used to inspect BUFR file variables and extract data. See usages section for
details.

The xml\_marshall package is yet another package providing dumps and loads functionality for python
objects. The package was created to support xml serialisation of complex python objects. During the
xml serialisation the xml\_vars attribute is used to limit the number of serialised attributes to
the minimum needed to create a clone of the object. The object definition must be available during
xml import where the xml\_vars list is used to initiate the newly created object.

The sqlalchemy\_marshall package provides generic functionality to serialise an entire database into an xml
stream. The module depends on the xml\_marshall package and the sqlachemy package.

## Translation Layer ##

The translation layer consists of the metadb package which installs in bufr.metadb . See usage page
for details. The package depends on sqlalchemy and the sql-marshall package and provides variable
translation relations. The main key in the database is the data type, corresponding to a BUFR file.
Each data type has a series of variables assigned to it which corresponds to the variables in the
BUFR file and each of these variables can be assigned one or more parameters. The parameters can be
the NetCDF name og NetCDF long name og the BUFR variable. See figure below for

![http://python-bufr.googlecode.com/svn/trunk/doc/images/python-bufrtranslationdatabase.png](http://python-bufr.googlecode.com/svn/trunk/doc/images/python-bufrtranslationdatabase.png)

Notice that the parameters could also contain information of for instance a hdf5 variable name or any
other attribute that might be needed in the output layer. Adding a extra parameter will no change
the database layout.

### Scripts ###

The metadb package uses the sqlalchemy\_marshall package to import or export translation data. The
import scripts can also be used directly on BUFR files but take care it's a representative file and
the user should check the contents of the database before generating NetCDF or other files.


## Output Generation Layer ##

Currently the output generation layer consists of the bufr2netcdf module (and script). The module
depends on the bufr module, the metadb module and the scientific python netcdf package to convert
the a BUFR file into a netcdf file.

Depending on available python modules it should be a small task to extend the output generation
layer with a module generating a hdf5 file or dumping the BUFR data directly into a database.

