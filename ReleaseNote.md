# Version 0.2 beta #

Brief notes on the new version.

Please see issues tab for list of open bugs.

This version consists of multiple python packages with the purpose to ease the decoding of BUFR files and the translation of BUFR files into NetCDF files.

The interface in the new version is changed to avoid conflicts with the pybufr project. The python package now installs under bufr and not pybufr.

Some functionality has been moved from the c extension to pure python. This means that the text output from the c module is now the raw BUFR strings that cannot directly be used as NetCDF variable names. This should make the BUFR decoding faster. Functionality to create valid NetCDF names are moved to the python part of the module.

Some packages for making generic BUFR to NetCDF conversions have been added. The packages contain a database model/interface for handling the BUFR to NetCDF variable name conversion and a output generating bufr2netcdf package and script.

For installation please see InstallationAndUsageNewVersion