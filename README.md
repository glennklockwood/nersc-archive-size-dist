# HPSS Object Size Distribution Dataset

This dataset is a list of the size of every object stored in NERSC's user-facing
tape archive system ("archive").  This system contains data dating back over
forty years.

The dataset files are named `hpss_file_sizes_20201016-*.log.gz` and are just
text files containing one file size per line, sharded to be roughly 128 MiB
in size each uncompressed.  They can be recombined into a single text file
trivially using `gunzip -c` and concatenating the results.

## Provenance

1. The raw dataset was generated on October 16, 2020 by Kirill Lozinskiy.  He
   dumped the `BFATTR_DATA_LEN` column from the HPSS metadata database into a
   file called `hpss_file_sizes_20201016.log` which is stored at NERSC in the
   archive system under `/home/g/glock/hpss_file_sizes_20201016.log`.
2. The `file-sizes_hpss-vs-lustre-vs-gpfs.ipynb` notebook uses Dask to shard
   this file at 32 MiB boundaries, strip whitespace and bogus lines (such as
   the DB2 headers outputs), and save the sharded dataset as a series of
   newline-delimited numbers corresponding to individual object sizes.  These
   sharded datasets are named `hpss_file_sizes_20201016-*.log.gz`.
3. The same notebook as #2 also generates `hpss_file_size_hist_20201016.csv`
   which contains a histogram of the above datsets.  Bins denote the start of
   the bin and are inclusive, so the "1024" bin contains all objects between
   1024 and 2047 bytes in size.

The initial DB2 dump did not appear to contain any zero-sized objects; whether
this reflects reality (as a result of NERSC's HPSS configuration) or the DB2
query criteria is unknown.

There are also histogram CSV files called:

- `cscratch_20190115_sizebytype_hist.csv`
- `tlproject2_20181109_sizebytype_hist.csv`

These contain file size distributions for the Cori scratch (Lustre) file system
and the project2 (GPFS) file system at NERSC measured on Jan 15, 2019 and Nov 9,
2018, respectively.  The former was released as part of the [Lustre Design
Analysis dataset](https://doi.org/10.5281/zenodo.3261815) and the latter has 
not been published.  The nomenclature denoting bin starts and ends for these
two files is _different_ from the bins in `hpss_file_size_hist_20201016.csv`
and are included here just for convenience and should not be cited.  The 
`file-sizes_hpss-vs-lustre-vs-gpfs.ipynb` notebook demonstrates how to convert
these histograms into a common binning scheme to facilitate direct comparison.
