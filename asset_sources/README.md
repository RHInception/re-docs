# Component Diagram Updates

If you update the component diagram you should re-export the PNG image
at 700px wide (to fit in read-the-docs w/out scaling).

After you do that you should re-add the XMP metadata to the image to
preserve copyright.

**NOTE** ``exiv2`` expects the XMP metadata file to have the same
base name as the image it is being inserted into. Therefore, the png
**must** be called ``ComponentDiagram.png`` to match the metadata file
``ComponentDiagram.xmp``:

**NOTE**: ``images/ComponentDiagram.png`` is just a symlink to
  ``asset_sources/ComponentDiagram.png``

# Requirements

* **exiv2** - https://admin.fedoraproject.org/pkgdb/package/exiv2/

# Examples

## Example - Add Metadata

Embed the metadata file into the exported PNG:

    $ exiv2 -v -iX ComponentDiagram.png


## Example - View Metadata

View the embedded metadata in the exported PNG:

    exiv2 -v -px ComponentDiagram.png

Output should look similar to this:

```
File 1/1: ComponentDiagram.png
Xmp.xmpRights.Marked                         XmpText     4  True
Xmp.xmpRights.WebStatement                   XmpText    48  http://release-engine.readthedocs.org/en/latest/
Xmp.xmpRights.UsageTerms                     LangAlt     3  lang="x-default" This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">Creative Commons Attribution-ShareAlike 4.0 International License</a>., lang="en" This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">Creative Commons Attribution-ShareAlike 4.0 International License</a>., lang="en_us" This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">Creative Commons Attribution-ShareAlike 4.0 International License</a>.
Xmp.dc.title                                 LangAlt     2  lang="x-default" Release Engine - Component Interactions, lang="en_us" Release Engine - Component Interactions
Xmp.cc.license                               XmpText    46  http://creativecommons.org/licenses/by-sa/4.0/
Xmp.cc.attributionName                       XmpText    27  Tim Bielawa - Red Hat, Inc.
```
