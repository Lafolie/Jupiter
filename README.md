Jupiter
=======

A small file io library for the [LÖVE framework][1]. Allows the storage of multi-dimensional table values using the filesystem.

Jupiter can store tables to the __n__th dimension. It currently handles keys and values of the following types:

+ Numbers
+ Strings
+ Booleans (true is written as a string, nil is ignored)
+ Tables

Jupiter will **not** store functions or other userdata that returns a memory address when using tostring().

Usage
-----

We can load Jupiter using a require:`jupiter = require "jupiter"`

Now we can save and load files in the following fashion:

    data = {_fileName = "example.txt", "Save this string!"}
    success = jupiter.save(data)
    newData = jupiter.load("filename.extension")

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

Note that the default directory is *save*. This operation is useful for creating a 'Continue' option or other such feature.

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

Although the above lua code might seem rather convoluted, it demonstrates how Jupiter can handle even the messiest of table structures. Jupiter uses lua pairs in its formatting routines and as such it can cope with both sequencial and non-sequential table structures.

Nested tables are stored in a logical format similar to that of lua syntax:

> table.index.subIndex.nIndex=value

Save operations also return *true* when successful: `success = jupiter.save(data)`

Non Sequiturs and Caveats
-------------------------

The following table assignments are not compatible with Jupiter's functions:
    
    --reference loop
    a = {}
    a[1] = a
    --binary loop
    a = {}
    b = {a}
    a[1] = b

It should be noted, however, that these kinds of assignments are likely to be rare in data structures designed for persistence. Nevertheless it is documented here for clarification.

Jupiter uses the pattern `"^(..-)=(.+)$"` to find variable assignments. If your line does not include an equals "=" character it will be omitted from the loaded table. Similarly, nil values are omitted and will not be written back to a file unless it's value is changed.

The *saveDir* directory is created on the first `jupiter.load` call if it does not already exist.

Since Jupiter uses the [love.filesystem][2] module files are **always** stored in the [user save directory][3]. Files are also loaded from here, although if they are not found LÖVE will look inside the boot directory (which contains your main.lua).

[1]: http://love2d.org/ "LÖVE"
[2]: http://love2d.org/wiki/love%2efilesystem "love.filesystem documentation"
[3]: http://love2d.org/wiki/love%2filesystem%2getSaveDirectory "love.filesystem.getSaveDirectory documentation"