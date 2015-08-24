# Introduction #

This page describes setting up ubuntu repositories on lucid and installing the python-bufr modules. Currently only lucid is supported but for both x86 and amd\_64


# Details #

### installation ###

Add the following line to `/etc/apt/sources.list`

```
# python bufr repository 
deb http://python-bufr.googlecode.com/svn/apt lucid main
```

Update index and install
```
sudo apt-get update
sudo apt-get install python-bufr-transform emos
```

Notice the above lines will install all packages into '/opt' if you just want the python-bufr module, use
```
sudo apt-get update
sudo apt-get install python-bufr emos
```

This will install the bufr module and a libemos package. If you have your own bufr tables you can obmit installing the emos package

**Please note that there is a bug in the libemos package supplied with ubuntu-lucid so that it can't be used in a shared module. Hence the python-bufr depends on the emos package which is included in the repository.**


### Configuration ###

This is only necessary if you want to convert BUFR file to NetCDF.

In order to translate from BUFR to NetCDF you need a the variable translation table. This table is stored in a database so you'll need a database backend (in this case sqlite).

```
sudo apt-get install python-sqlite sqlite3
```

For a general example see InstallationAndUsageNewVersion

FIXME... more documentation

### Usage ###

Basic usage of the python-bufr module. First set env variables

```
export PYTHONPATH=/opt/lib/python2.6/site-packages/
export BUFR_TABLES=/opt/lib/emos/bufrtables/
```

Then start up your python prompt
```
Python 2.6.5 (r265:79063, Apr 16 2010, 13:57:41) 
[GCC 4.4.3] on linux2
Type "help", "copyright", "credits" or "license" for more information.
>>> import bufr
>>> bfr = bufr.BUFRFile('file.bufr')
```

You can read one bufr section at a time using
```
>>> data = bfr.read()
```

Or collect data using the iterator interface
```
>>> all_data = []
>> for data in bfr:
...   all_data.append(data)

```


### Dependencies ###

If you are curious the dependency graph is show below

![http://python-bufr.googlecode.com/svn/trunk/doc/images/dep.png](http://python-bufr.googlecode.com/svn/trunk/doc/images/dep.png)

