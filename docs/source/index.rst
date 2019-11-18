A python library for reading data from Statistics Canada
========================================================

This library implements most of the functions defined by the Statistics Canada
`Web Data Service <https://www.statcan.gc.ca/eng/developers/wds>`_.
It also has a number of helper functions that make it easy to read Statistics
Canada tables or vectors into pandas dataframes.

Installation
============
The easiest way to install the package is with conda:
::

    conda install -c ian.e.preston stats_can

The code is also available on
`github <https://github.com/ianepreston/stats_can>`_,
I haven't got it on pypi or anything yet.


Quickstart
==========
To download the following `Sample Dataset
<https://www150.statcan.gc.ca/t1/tbl1/en/tv.action?pid=1310080502>`_ to a
pandas dataframe:

::


    from stats_can.sc import table_to_df

    # Copy the table id from the associated StatsCan page
    table_id = "10-10-0110-01"

    # Create a h5file in current working directory
    df = table_to_df(table_id)

    # Save to an existing h5file
    existing_folder = "my_data_folder/"
    existing_filename = "sample_data.h5
    df = table_to_df(table_id, path=existing_folder, file=existing_filename)



To download the `Sample Dataset
<https://www150.statcan.gc.ca/t1/tbl1/en/cv.action?pid=1010011001>`_
from vectors:

::

    from stats_can.sc import vectors_to_df

    # Copy the vectors from the associated customize table page
    vectors = ["v36883", "v36885", "v36938"]

    # Get by number of datapoints
    num_datapoints = 100
    vector_df = sc.vectors_to_df(vectors, periods=num_datapoints)

    # Get by datespan
    import datetime as dt
    end_time = dt.date.today()
    start_time = end_time - dt.timedelta(days=7)
    vector_df = sc.vectors_to_df(vectors,
                                 start_release_date=start_time,
                                 end_release_date=end_time)


::

    from stats_can.sc import vectors_to_df_local

    # Save to an existing h5file
    vector_df = sc.vectors_to_df_local(vectors,
                                       periods=100,
                                       path=existing_folder,
                                       file=existing_filename)




Key functions
=============

To load a table into a pandas dataframe, use table_to_df. Given a valid
Statistics Canada table name (it will do some basic parsing to handle dashes)
the function will return a dataframe, either from an already downloaded file on
disk, or after downloading the necessary table. By default it will load an hdf5
file and look for tables there. You can set the h5file parameter to None to load
directly from zipped csv files, but the h5 version is significantly faster.

.. autofunction:: stats_can.sc.table_to_df


There are two ways to load a list of vector numbers into a datetime indexed
dataframe, vectors_to_df and vectors_to_df_local. The former uses the web data
service to load each data point directly from Statistics Canada, while the
latter loads them from locally saved tables (again, downloading any required
tables that haven't already been downloaded). They both return basically the
same data but there are likely performance differences, depending on how many
tables your vectors are sourced from, your internet connection speed, etc.

.. autofunction:: stats_can.sc.vectors_to_df

.. autofunction:: stats_can.sc.vectors_to_df_local

To update locally stored tables with the latest data, use update_tables.
Again, there is an option to update zipped csv files, but hdf5 is the default
This will be slower to run initially as it converts the tables into dataframes
before storing them in hdf5, but subsequent loading of the tables is faster.

.. autofunction:: stats_can.sc.update_tables


For managing already downloaded tables there is list_downloaded_tables and
delete_tables. Neither are particularly necessary if you're only working with
zipped CSV files, since it's pretty easy to just look in a folder and see
what's there and delete it if necessary, but it makes managing an hdf5 file
easier, which is the preferred way of storing tables.

.. autofunction:: stats_can.sc.list_downloaded_tables

.. autofunction:: stats_can.sc.delete_tables


Contributing
============

Contributions are welcome. I'll outline the method I use to set up a dev environment below,
but a great enhancement for this project would be to extend that to other possible approaches.
Using conda, a dev environment can bet set up as follows:
::
        git clone <your fork of stats_can>
        cd stats_can
        conda env create -f sc_dev.yml
        conda activate sc_dev
        python setup.py develop

From there you should be able to make any changes to the source code, run tests and build
docs. I use travis CI for building and testing. I'm actually not sure if it will work for
people who aren't me the way I've got it set up, since the last step is to push a passing
build to Anaconda cloud.

This is pretty meta but I would love some contributions around making this project easier
to contribute to. If you have ideas for that please feel free to create an issue or submit a PR.


Indices and tables
==================

* :ref:`genindex`
* :ref:`modindex`
