# `extract_bisicles_data`

A Python program for extracting data from
[BISICLES](http://davis.lbl.gov/Manuals/BISICLES-DOCS/) output HDF5 files, and
writing to NetCDF.

## Requirements

The program should work with Python2 or Python3 (it has been tested with Python
2.6, 2.7, various versions of Python3 and all seems well), and has the
following third party requirements:

  * h5py
  * netcdf4
  * amrfile

The amrfile Python code is provided with BISICLES, and requires a compiled
library. More information:

[http://davis.lbl.gov/Manuals/BISICLES-DOCS/libamrfile.html](http://davis.lbl.gov/Manuals/BISICLES-DOCS/libamrfile.html)

## Usage And Options

To see the available program options and default values, use the `-h` option:

```
extract_bisicles_data -h
```

### Input Data Directory

The input data directory can be specified with the `-i` or `--indir` option,
and is a required argument. This should point to the directory which contains
the BISICLES output files, for example:

```
extract_bisicles_data -i /path/to/bisicles/data
```

### Input File Pattern

The input filename pattern to match can be specified with the `-p` or
`--inpattern` option. The default value is `*.hdf5`, and the Python `glob`
library uses this pattern to match the input files which should be read.

### Query Data

Query mode can be specified with the `-q` or `--query` option. When this is
specified, no extraction will take place and all other options (except the
option which specifies the input data directory and input file pattern) are ignored:

```
extract_bisicles_data -i /path/to/bisicles/data -p 'plot*.hdf5' -q
```

This will read through the input files, and print out information regarding the
files which have been found, and the levels, variables and time steps which
they contain.

### Output Directory

The directory in which the output NetCDF files will be created can be specified
with the `-d` or `--outdir` option. The default is to write the output files to
the current working directory.

### Output File Names

A separate NetCDF output file is created for each level that is extracted from
the input data. By default, output files will be name `out_level0.nc`,
`out_level1.nc`, ... The prefix of the output files (i.e. the `out`) can be
specified with the `-o` or `--outpattern` option. For example:

```
extract_bisicles_data -i /path/to/bisicles/data -o data
```

This would create output files named `data_level0.nc`, `data_level1.nc`, ...

### NetCDF Compression

NetCDF compression is enabled by default, but can be disabled with the `-z` or
`--nocompress` option.

The compression level defaults to 3, and can be set to a value from 1 to 9,
using the `-x` or `--nccomplevel` option.

### Levels To Extract

The levels which should be extracted from the data can be specified with the
`-l` or `--levels` option. By default all levels are extracted.

### Time Steps To Extract

The time steps which should be extracted from the data can be specified with the
`-t` or `--times` option. By default all time steps are extracted.

When specifying the requested levels or time steps, ranges can be specified in
the following ways:

  * `1-10` : 1 to 10 inclusive
  * `1:10` : 1 to 10 inclusive
  * `1:2:10` : every second value from 1 to 10 (1, 3, 5, 7, 9)

These can be combined with other options. For example:

```
-t 1,3,5,10-15,20:10:50,100
```

This would extract the time steps:

```
1, 3, 5, 10, 11, 12, 13, 14, 15, 20, 30, 40, 50, 100
```

### Variables To Extract

The variables which should be extracted from the data can be specified with the
`-v` or `--variables` option. By default, all variables are extracted.

Multiple variables can be specifed, separated by a comma:

```
extract_bisicles_data -i /path/to/bisicles/data -v thickness,Z_surface
```

### Number Of Processes To Run Simultaneously

The code uses the Python `multiprocessing` library to run multiple processes at
once. The main bottleneck in the program is writing the output file, as only
one process can write to a file at a time. As such, running many more processes
than the number of output files which will be created (which will equal the
number of levels to be extracted) may not provide a huge improvement in
performance. The number of processes to run can be specified with the `-n` or
`--numprocs` option. The default value is 4.
