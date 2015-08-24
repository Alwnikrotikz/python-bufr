# This project has moved to github: http://github.com/mraspaud/python-bufr !!! #

# Introduction #
Generic BUFR file reader written in python. File support is similar to the ECMWF BUFR library.

Only reading supported for now.

Depends on
  * ECMWF BUFR library and tables
  * python numpy

This module is part of the [Pytroll project](http://www.pytroll.org).

The python-bufr module is now available on pypi. See link below for install instructions:
[Documentation](http://python-bufr.googlecode.com/svn/trunk/doc/build/html/index.html)

## Comments and Suggestions ##
Feel free to make suggestions for updates or new features in the issues tab. Don't be afraid that your might end up under the Committers tab to the right ... Because chances are that you will :-)


NOW !! what to do with all that BUFR data ... well why not resample it !!! see
http://code.google.com/p/pyresample/

## Roadmap ##

  * Generic BUFR to NetCDF conversion based on SQL-database variable translation tables (**Done**)
  * XML-RPC server/client interface