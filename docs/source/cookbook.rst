Recipes
=========

.. WARNING::

    The pyexcel DOES NOT consider Fonts, Styles and Charts at all. In the resulting excel files, fonts, styles and charts will not be transferred.

These recipes give a one-stop utility functions for known use cases. Similar functionality can be achieved using other application interfaces.

Update one column of a data file
---------------------------------

Suppose you have one data file as the following:

example.xls

======== ======== ========
Column 1 Column 2 Column 3
======== ======== ========
1        4        7
2        5        8
3        6        9
======== ======== ========

.. testcode::
   :hide:

   >>> import pyexcel
   >>> # please make sure pyexcel-xls is pip-installed
   >>> data = [
   ...      ["Column 1", "Column 2", "Column 3"],
   ...      [1, 4, 7],
   ...      [2, 5, 8],
   ...      [3, 6, 9]
   ...  ]
   >>> s = pyexcel.Sheet(data)
   >>> s.save_as("example.xls")


And you want to update ``Column 2`` with these data: [11, 12, 13]

Here is the code::

   >>> from pyexcel.cookbook import update_columns
   >>> custom_column = {"Column 2":[11, 12, 13]}
   >>> update_columns("example.xls", custom_column, "output.xls")

Your output.xls will have these data:

======== ======== ========
Column 1 Column 2 Column 3
======== ======== ========
1        11       7
2        12       8
3        13       9
======== ======== ========

.. testcode::
   :hide:

   >>> import os
   >>> os.unlink("example.xls")
   >>> os.unlink("output.xls")

Update one row of a data file
---------------------------------

Suppose you have the same data file:

example.xls

===== = = =
Row 1 1 2 3
Row 2 4 5 6
Row 3 7 8 9
===== = = =

.. testcode::
   :hide:

   >>> import pyexcel
   >>> data = [
   ...      ["Row 1", 1, 2, 3],
   ...      ["Row 2", 4, 5, 6],
   ...      ["Row 3", 7, 8, 9]
   ... ]
   >>> s = pyexcel.Sheet(data)
   >>> s.save_as("example.xls")

And you want to update the second row with these data: [7, 4, 1]

Here is the code::

   >>> from pyexcel.cookbook import update_rows
   >>> custom_row = {"Row 1":[11, 12, 13]}
   >>> update_rows("example.xls", custom_row, "output.xls")

Your output.xls will have these data:

======== ======== ========
Column 1 Column 2 Column 3
======== ======== ========
7        4        1
2        5        8
3        6        9
======== ======== ========

.. testcode::
   :hide:

   >>> os.unlink("example.xls")
   >>> os.unlink("output.xls")

Merge two files into one
-------------------------

Suppose you want to merge the following two data files:

example.csv

======== ======== ========
Column 1 Column 2 Column 3
======== ======== ========
1        4        7
2        5        8
3        6        9
======== ======== ========

example.xls

======== ========
Column 4 Column 5
======== ========
10       12      
11       13      
======== ========

.. testcode::
   :hide:

   >>> data = [
   ...      ["Column 1", "Column 2", "Column 3"],
   ...      [1, 2, 3],
   ...      [4, 5, 6],
   ...      [7, 8, 9]
   ...  ]
   >>> s = pyexcel.Sheet(data)
   >>> s.save_as("example.csv")
   >>> data = [
   ...      ["Column 4", "Column 5"],
   ...      [10, 12],
   ...      [11, 13]
   ...  ]
   >>> s = pyexcel.Sheet(data)
   >>> s.save_as("example.xls")


The following code will merge the tow into one file, say "output.xls"::

   >>> from pyexcel.cookbook import merge_two_files
   >>> merge_two_files("example.csv", "example.xls", "output.xls")

The output.xls would have the following data:

======== ======== ======== ======== ========
Column 1 Column 2 Column 3 Column 4 Column 5
======== ======== ======== ======== ========
1        4        7        10       12      
2        5        8        11       13      
3        6        9
======== ======== ======== ======== ========

.. testcode::
   :hide:

   >>> os.unlink("example.csv")
   >>> os.unlink("example.xls")
   >>> os.unlink("output.xls")

Select candidate columns of two files and form a new one
--------------------------------------------------------

Suppose you have these two files:

example.ods

======== ======== ======== ======== ========
Column 1 Column 2 Column 3 Column 4 Column 5
======== ======== ======== ======== ========
1        4        7        10       13      
2        5        8        11       14      
3        6        9        12       15
======== ======== ======== ======== ========

example.xls

======== ======== ======== ======== =========
Column 6 Column 7 Column 8 Column 9 Column 10
======== ======== ======== ======== =========
16       17       18       19       20
======== ======== ======== ======== =========

   >>> data = [
   ...      ["Column 1", "Column 2", "Column 3", "Column 4", "Column 5"],
   ...      [1, 4, 7, 10, 13],
   ...      [2, 5, 8, 11, 14],
   ...      [3, 6, 9, 12, 15]
   ...  ]
   >>> s = pyexcel.Sheet(data)
   >>> s.save_as("example.csv")
   >>> data = [
   ...      ["Column 6", "Column 7", "Column 8", "Column 9", "Column 10"],
   ...      [16, 17, 18, 19, 20]
   ...  ]
   >>> s = pyexcel.Sheet(data)
   >>> s.save_as("example.xls")


And you want to filter out column 2 and 4 from example.ods,  filter out column 6 and 7 and merge them:

======== ======== ======== ======== ======== =========
Column 1 Column 3 Column 5 Column 8 Column 9 Column 10
======== ======== ======== ======== ======== =========
1        7        13       18       19       20      
2        8        14                                    
3        9        15                           
======== ======== ======== ======== ======== =========

The following code will do the job::

   >>> from pyexcel.cookbook import merge_two_readers
   >>> sheet1 = pyexcel.get_sheet(file_name="example.csv", name_columns_by_row=0)
   >>> sheet2 = pyexcel.get_sheet(file_name="example.xls", name_columns_by_row=0)
   >>> del sheet1.column[1, 3, 5]
   >>> del sheet2.column[0, 1]
   >>> merge_two_readers(sheet1, sheet2, "output.xls")

.. testcode::
   :hide:

   >>> sheet3 = pyexcel.get_sheet(file_name="output.xls", name_columns_by_row=0)
   >>> [str(name) for name in sheet3.colnames]
   ['Column 1', 'Column 3', 'Column 5', 'Column 8', 'Column 9', 'Column 10']
   >>> sheet3.column["Column 8"]
   [18, '', '']
   >>> os.unlink("example.csv")
   >>> os.unlink("example.xls")
   >>> os.unlink("output.xls")

Merge two files into a book where each file become a sheet
----------------------------------------------------------

Suppose you want to merge the following two data files:

example.csv

======== ======== ========
Column 1 Column 2 Column 3
======== ======== ========
1        4        7
2        5        8
3        6        9
======== ======== ========

example.xls

======== ========
Column 4 Column 5
======== ========
10       12      
11       13      
======== ========

   >>> data = [
   ...      ["Column 1", "Column 2", "Column 3"],
   ...      [1, 2, 3],
   ...      [4, 5, 6],
   ...      [7, 8, 9]
   ...  ]
   >>> s = pyexcel.Sheet(data)
   >>> s.save_as("example.csv")
   >>> data = [
   ...      ["Column 4", "Column 5"],
   ...      [10, 12],
   ...      [11, 13]
   ...  ]
   >>> s = pyexcel.Sheet(data)
   >>> s.save_as("example.xls")

The following code will merge the tow into one file, say "output.xls"::

   >>> from pyexcel.cookbook import merge_all_to_a_book
   >>> merge_all_to_a_book(["example.csv", "example.xls"], "output.xls")

The output.xls would have the following data:

`example.csv` as sheet name and inside the sheet, you have:

======== ======== ======== 
Column 1 Column 2 Column 3 
======== ======== ======== 
1        4        7        
2        5        8        
3        6        9
======== ======== ========


`example.ods` as sheet name and inside the sheet, you have:

======== ========
Column 4 Column 5
======== ========
10       12      
11       13      
                 
======== ========

.. testcode::
   :hide:

   >>> book = pyexcel.get_book(file_name="output.xls")
   >>> [str(name) for name in book.sheet_names()]
   ['example.csv', 'example.xls']
   >>> os.unlink("example.csv")
   >>> os.unlink("example.xls")
   >>> os.unlink("output.xls")
