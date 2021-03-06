## Introduction

This file will describe the xml of the vpd template file format used to
describe the contents of a VPD image

## High Level Description

The template VPD format is XML, used to describe the contents of a binary VPD image.  The template file is fed into createVpd.py.  It in turn interprets that XML, error checks it and creates a binary VPD image.

The template consists of 3 levels
* `<vpd>` - information about the overall VPD image
* `<record>` - information about a record in `<vpd>`
* `<keyword>` - information about a keyword contained in a `<record>`

The XML Hierarchy looks like the following:
``` xml
<vpd>
  <record>
    <keyword>
    </keyword>
    <keyword>
    </keyword>
  </record>
  <record>
    <keyword>
    ..
  </record>
  <record>
  ..
</vpd>
```

## `<vpd>`
This section covers the required tags in the <vpd> section of the template description
 
The top level `<vpd>` tag is required in all template files, whether it is a top level manifest description of a full image or simply a record level description.  The tag requirements in the <vpd> section depend upon usage.

### Tags included in the `<vpd>` section
`<name></name>`  
Used to define the output name of the files created by the tool.  This information is not stuck into the binary VPD image.

If the special name of FILENAME is given, then the name of the top level manifest file will be used for the name on any output files.

Can be used only once in the top level description  
Cannot be included in single record definitions  

`<size></size>`  
Used to define the maximum size of the binary image.  If the create image will exceede this size, an error is generated by the tool.

Can be used only once in the top level description  
Cannot be included in single record definitions  

`<VD></VD>`  
The hex value to be stuck in for the VD keyword in the VHDR

Can be used only once in the top level description  
Cannot be included in single record definitions  

``` xml
<record>
  ..
</record>
```
Defines the creation of a record within VPD.  Please see the section on the `<record>` tag for more information on valid fields and syntax

When creating a top level VPD template, at least 1 `<record>` must be defined.
When creating a record only template description, only 1 `<record>` tag can be used.  It is not allowed to include different records in a record only template.

A sample `<vpd>` section would look like this:
``` xml
<vpd>
  <name>simple</name>
  <size>16kb</size>
  <VD>01</VD>
  <record ..>
</vpd>
```

## `<record>`
The record tag is used to describe the information that will go into a VPD record.  You can specify this information 3 different ways.
 * By defining the keywords to go in the record using the `<keyword>` tag
 * By pointing to a different file with the `<rtvpdfile>` tag.  That file contains the record definition that uses the `<keyword>` tag
 * By pointing to a fully created binary representation of the record using the `<rbinfile>` tag
One 1 of these 3 tags can be given at a time and the creation program checks to make sure that is the case.

``` xml
<record name=”NAME”>
</record>
```
The name attribute is required in a ```<record>``` and must be 4 characters long. 

### Tags included in the `<record>`  
`<rdesc></rdesc>`
A text description of the contents/purpose of a record.  Only 1 `<rdesc>` is allowed per record

``` xml
<keyword>
  ..
</keyword>
```
Defines a keyword within a record.  Please see the `<keyword>` section of the document for further descriptions.

`<rtvpdfile></rtvpdfile>`
Contains the name or path and name to a file describing the record

`<rbinfile></rbinfile>`
Contains the name or path and name to the binary version of a record.  It is assumed that this file is correctly formatted and only minimally checked to make sure the record names match.

A sample `<record>` section would look like this:

``` xml
<record name=”NAME”>
  <rdesc> The NAME record</rdesc>
  <keyword..>
</record>
```

The inclusion of a record tvpd file would look like this:
``` xml
<record name=”NAME”>
  <rtvdfile>record-NAME.tvpd</rtvpdfile>
</record>
```

The inclusion of a record binary file would look like this:
``` xml
<record name=”NAME”>
  <rbinfile>record-NAME.bin</rbinfile>
</record>
```

## `<keyword>`
The `<keyword>` tag is used to describe the contents of a keyword within a record.

``` xml
<keyword NAME=”NM”>
  ..
</keyword>
```
The name attribute is required and only 2 characters long

### Tags included in the `<keyword>`

`<ktvpdfile></ktvpdfile>`
Contains the name or path and name to a file describing the keyword
This tag is mutually exclusive from the `<kwdesc>`, `<kwformat>`, `<kwlen>` and `<kwdata>` below

`<kwdesc></kwdesc>`
A description of the contents of the keyword.  Only 1 tag allowed per keyword.

`<kwformat></kwformat>`
The  format of the data in the `<kwdata>` tag.  It can be three different values
 * hex
 * ascii
 * bin
hex and ascii data are both specified within the `<kwdata>` tag.  When using the bin type, the `<kwdata>` tag is a reference to a binary file that contains just data for the keyword

`<kwlen></kwlen>`
The length of the keyword.  If the data given is shorter than the keyword, the data will be right padded with zeros.  If the data is longer than the `<kwlen>`, then an error is generated.

`<kwdata></kwdata>`
The data to go into the keyword.  It is checked to make sure it matches the format specified.  For example, that hex data has only valid hex characters.

Sample keyword sections would look like this:

For hex data:
``` xml
<keyword name="NM">
  <kwdesc>The name keyword</kwdesc>
  <kwformat>hex</kwformat>
  <kwlen>4</kwlen>
  <kwdata>01AEF78DB</kwdata>
</keyword>
```

For ascii data:
``` xml
<keyword name="NM">
  <kwdesc>The name keyword</kwdesc>
  <kwformat>ascii</kwformat>
  <kwlen>4</kwlen>
  <kwdata>NAME</kwdata>
</keyword>
```

For bin data:
``` xml
<keyword name="NM">
  <kwdesc>The name keyword</kwdesc>
  <kwformat>bin</kwformat>
  <kwlen>4</kwlen>
  <kwdata>name.bin</kwdata>
</keyword>
```

The inclusion of a keyword ktvpdfile would look like this:
``` xml
<keyword name=”NM”>
  <ktvpdfile>vini-nm.xml</ktvpdfile>
</keyword>
```

## Examples
Please see the examples dir in this repo for complete representations multiple types of template files