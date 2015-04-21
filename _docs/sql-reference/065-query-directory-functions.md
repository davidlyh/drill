---
title: "Query Directory Functions"
parent: "SQL Reference"
---
You can use the following query directory functions when [querying multiple files or directories]({{site.baseurl}}/docs/querying-directories):

* MAXDIR
* MINDIR
* IMAXDIR
* IMINDIR

The query directory functions restrict a query to one of a number of subdirectories. For example, suppose you had time-series data in subdirectories named 2015, 2014, and 2013. You could use the MAXDIR function to get the latest data and MINDIR to get the earliest data.

You use MAXDIR and MINDIR when your directory names are case-sensitive, and IMAXDIR and IMINDIR when your directory names are case-insensitive.

The query directory functions are recommended instead of the MAX or MIN aggregate functions to prevent Drill from scanning all data in directories.

## Query Directory Function Syntax

The following syntax shows how to construct a SELECT statement that using the MAXDIR function:

    SELECT * FROM <plugin>.<workspace>.`<filename>` 
    WHERE dir*n* = MAXDIR('<plugin>.<workspace>', '<filename>');

Enclose both arguments to the query directory function in single-quotation marks, not backticks. The first argument to the function is the plugin and workspace names in dot notation, and the second argument is the directory name. The dir variable, `dir0`, `dir1`, and so on, refer to
subdirectories in your workspace path, as explained in section, ["Querying Directories"]({{site.baseurl}}/docs/querying-directories). 

## Query Directory Function Example 

This example creates a top-level directory called `querylogs` in the `/tmp` directory. Using the `/tmp` directory is convenient for example purposes because `/tmp` is predefined as a workspace in the default `dfs` storage plugin. In the `querylogs` directory, you create three subdirectories:

* 2015
* 2014
* 2013

In each subdirectory, you create a CSV file that contains arbitrary log data. Issue MAXDIR and MINDIR queries to exercise the query directory functions:

    SELECT * FROM dfs.tmp.`querylogs` WHERE dir0 = MAXDIR('dfs.tmp','querylogs');
    +------------+------------+
    |  columns   |    dir0    |
    +------------+------------+
    | ["2015","some arbitrary data"] | 2015       |
    +------------+------------+
    1 row selected (0.112 seconds)

    SELECT * FROM dfs.tmp.`querylogs` WHERE dir0 = MINDIR('dfs.tmp','querylogs');
    +------------+------------+
    |  columns   |    dir0    |
    +------------+------------+
    | ["2013","even more data"] | 2013       |
    +------------+------------+
    1 row selected (0.119 seconds)

In this example, using any variable other than dir0 does not return results because the subdirectory level nesting is only one level deep.

