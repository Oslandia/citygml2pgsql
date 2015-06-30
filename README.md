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
citygml2pgsql mycity.xml LOD SRID GEOMETRY_COLUMN TABLE_NAME
```

Will generate on stdout a serie of insert statements of PolyhedralSurface in column GEOMETRY_COLUMN of table TABLE_NAME, one surface per building. The table must exist prior to insertion.

The LOD (in [0,1,2,3]) is mandatory and must match one of the LODs displayed with the ```-l``` option.

The SRID must match the one of the GEOMETRY_COLUMN defined in postgis.

Tutorial
========

Get the script:
```
git clone citygml2pgsql
cd citygml2pgsql
```

Get some CityGML files:
```
wget http://www.citygml.org/fileadmin/count.php?f=fileadmin%2Fcitygml%2Fdocs%2FBerlin_Alexanderplatz_v1.0.0.zip
wget http://www.citygml.org/fileadmin/count.php?f=fileadmin%2Fcitygml%2Fdocs%2FKreuz_Leverkusen_2008-03-05.zip
wget https://download.data.grandlyon.com/files/grandlyon/localisation/bati3d/LYON_1ER_2012.zip
unzip *Berlin_Alexanderplatz*.zip
unzip *Kreuz_Leverkusen*.zip
unzip LYON_1ER_2012.zip
```

Have a look at what we have:
```
./citygml2pgsql.py -l LYON_1ER_2012/LYON_1ER_BATI_REMARQUABLE_2012.gml Berlin_Alexanderplatz_v1.0.0.xml Kreuz_Leverkusen_2008-03-05/080305SIG3D_Building_Levkreuz.xml
```

will give us:
```
In LYON_1ER_2012/LYON_1ER_BATI_REMARQUABLE_2012.gml
    found reference to EPSG:3946
    found geometries lod2MultiSurface
In Berlin_Alexanderplatz_v1.0.0.xml
    found reference to urn:ogc:def:crs,crs:EPSG:6.12:3068,crs:EPSG:6.12:5783
    found geometries lod2Solid
In Kreuz_Leverkusen_2008-03-05/080305SIG3D_Building_Levkreuz.xml
    found reference to EPSG:31466
    found geometries lod1Solid
```

Now we have the information we need to create our tables in a test database:
```
createdb test_citygml
psql test_citygml -c "CREATE EXTENSION postgis"
psql test_citygml -c "CREATE TABLE lyon(gid SERIAL PRIMARY KEY, geom GEOMETRY('POLYHEDRALSURFACEZ', 3946))"
psql test_citygml -c "CREATE TABLE levkreuz(gid SERIAL PRIMARY KEY, geom GEOMETRY('POLYHEDRALSURFACEZ', 31466))"
psql test_citygml -c "CREATE TABLE berlin(gid SERIAL PRIMARY KEY, geom GEOMETRY('POLYHEDRALSURFACEZ', 3068))"
```

And we can populate our tables:
```
./citygml2pgsql.py LYON_1ER_2012/LYON_1ER_BATI_REMARQUABLE_2012.gml 2 3946 geom lyon | psql test_citygml
./citygml2pgsql.py Kreuz_Leverkusen_2008-03-05/080305SIG3D_Building_Levkreuz.xml 1 31466 geom levkreuz | psql test_citygml
./citygml2pgsql.py Berlin_Alexanderplatz_v1.0.0.xml 2 3068 geom berlin | psql test_citygml

```

That's it!


Credits
=======

This plugin has been developed by Oslandia ( http://www.oslandia.com ).

First release was funded by European Union (FEDER related to the e-PLU project) and by Oslandia.

License
=======

This work is free software and licenced under the MIT licence.
See LICENSE file.


