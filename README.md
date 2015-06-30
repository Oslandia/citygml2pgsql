A CityGML to SQL converter
==========================

citygml2pgsql converts buildings found in a CityGML file to PolyhedralSurface insert statements aimed at PosgresSQL/PostGIS.

Usage
======

```
citygml2pgsql -l mycity.xml
```

Will list the LOD-qualified geometry types found for buildings, several LODs can be present.

```
itygml2pgsql mycity.xml LOD SRID GEOMETRY_COLUMN TABLE_NAME
```

Will generate on stdout a serie of insert statements of PolyhedralSurface in column GEOMETRY_COLUMN of table TABLE_NAME, one surface per building. The table must exist prior to insertion.

The LOD (in [0,1,2,3]) is mandatory and must match one of the LODs displayed with the ```-l``` option.

The SRID must match the one of the GEOMETRY_COLUMN defined in postgis.

Credits
=======

This plugin has been developed by Oslandia ( http://www.oslandia.com ).

First release was funded by European Union (FEDER related to the e-PLU project) and by Oslandia.

License
=======

This work is free software and licenced under the GNU GPL version 2 or any later version.
See LICENSE file.


