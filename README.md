Jupiter
=======

A small file io library for the LÖVE framework. Allows the storage of multi-dimensional table values using the filesystem.

Jupiter can store tables to the *n*th dimension. It currently handles keys and values of the following types:

+ Numbers
+ Strings
+ Booleans (true is written as a string, nil is ignored)
+ Tables

Jupiter will **not** store functions or other userdata that returns a memory address when using tostring().

Usage
-----

We can load Jupiter using a require:`jupiter = require "jupiter"`

Now we save save and load files in the following fashion:

    data = {_fileName = "example.txt", "Save this string!"}
    success = jupiter.save(data)
    newData = jupiter.load(data)

Note that we have specified the table property *_fileName* in our table declaration. **This property is mandatory** and tells Jupiter where to save the file. Obviously, *_fileName* will not be written to the file and needs only to be set for the initial write of *data*; Jupiter handles *_fileName* automatically for tables it has loaded.

The resultant file *example.txt* will contain the stored key and value of the table. Jupiter uses lua pairs to generate human readable (and editable!) files:

> ####example.txt
> 1=Save this string!

If you plan on editing files saved by Jupiter be mindful that any space characters in a key or value are kept:
> ####example.txt
> 1=Value<br />
> 2 =Value

would result in

    table[1] = "Value"
    table['2 '] = "Value"

Advanced Usage
--------------

Jupiter also has a couple of other useful features. For instance, it can load the most recently modified file from a single directory:
    
    jupiter = require "jupiter"
    mostRecentFile = jupiter.load()
    
This operation can only be performed on a single directory, specifiable by setting `jupiter.saveDir = "save"`

Not that the default directory is *save*. This operation is useful for creating a 'Continue' option or other such feature.

To demonstrate Jupiter's handling of nested tables we can do the following:

    jupiter = require "jupiter"
    data = {
        _fileName = "example.txt", 
        1, 
        "someval", 
        doNotWrite = nil, 
        {
            1,
            this = 2, 
            that = "3",
            the = {
                other = "bartbes has no soul"
            }
        }
    }
    
    success = jupiter.save(data)

    if success then
        newData = jupiter.load("example.txt")
    end

Output:

> ####example.txt<br />
1=1<br />
2=someval<br />
3.1=1<br />
3.this=2<br />
3.that=3<br />
3.the.other=bartbes has no soul


