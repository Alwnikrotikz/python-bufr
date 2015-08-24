# Introduction #

Just a few details on where this project is heading and what is currently being worked on.

2010-04-14

Just a few notes...

In just a few days I plan to upload a database table structure and backend for translating the BUFR variable names into netcdf names. The database is needed to streamline the conversion of BUFR files into netCDF files. Together with the database I plan to upload a generic bufr-to-NetCDF conversion script and some tools for importing new BUFR file variable names into the database for easy variable name translation.

Notes on implementation. The database interface is made using sql-alchemy and the table design makes it possible to extend the direct BUFR file conversion to any file format wanted, and not just NetCDF. The database is instrumented with a generic dump utility which makes it possible to dump a xml representation of the specific instrument variable mappings. The xml file can be edited and reloaded into the database. The database xml import methods uses both the primary key and an alternative key to try to insert the xml content into the database.

The database structure is listed below

![http://python-bufr.googlecode.com/svn/trunk/doc/images/python-bufrtranslationdatabase.png](http://python-bufr.googlecode.com/svn/trunk/doc/images/python-bufrtranslationdatabase.png)

Further into the near future (a month or so) I plan to upload a small XML-RPC server and client interface. In this way the BUFR decoding and variable translation tables can be centralized.