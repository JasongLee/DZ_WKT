#DZ_WKT
PL/SQL code for the conversion of [OGC Well Known Text] (http://www.opengeospatial.org/standards/sfa) and [Extended WKT] (http://postgis.refractions.net/documentation/manual-1.3SVN/ch04.html#id2726317) to and from the [Oracle Spatial] (http://www.oracle.com/us/products/database/options/spatial/overview/index.html) MDSYS.SDO_GEOMETRY geometry type.
For the most up-to-date documentation see the auto-build  [dz_wkt_deploy.pdf](https://github.com/pauldzy/DZ_WKT/blob/master/dz_wkt_deploy.pdf).

This library is provided for testing and feedback.  It may be installed and executed from any schema.  The deployment script creates three packages:

1. DZ\_WKT\_MAIN
2. DZ\_WKT\_TEST
3. DZ\_WKT\_UTIL

The test package is not needed for normal usage but always a good idea to inspect and keep around.

Some simple usage that mirrors the capability of the SDO_UTIL WKT utilities:
```sql
SELECT dz_wkt_main.sdo2wkt(
    MDSYS.SDO_GEOMETRY(2001,8265,SDO_POINT_TYPE(-100,200,NULL),NULL,NULL)
) 
FROM 
dual;

>> POINT ( -100 200)
```
```sql
SELECT dz_wkt_main.wkt2sdo(
   'POINT(-100 200)'
) 
FROM 
dual;

> MDSYS.SDO_GEOMETRY(2001,NULL,MDSYS.SDO_POINT_TYPE(-100,200,NULL),NULL,NULL)
```
or some more complicated items you can't do with the included Oracle utilities
```sql
SELECT dz_wkt_main.wkt2sdo(
   'SRID=4269;POINT M(-100 75 30)'
) 
FROM 
dual;

>> MDSYS.SDO_GEOMETRY(
       3301
      ,4269
      ,NULL
      ,MDSYS.SDO_ELEM_INFO_ARRAY(1,1,1)
      ,MDSYS.SDO_ORDINATE_ARRAY(-100,75,30)
   )
```
```sql
SELECT 
dz_wkt_main.wkt2sdo(
   'SRID=3857;LINESTRING Z(100045 1175 3000, 100050 1180 3010)'
) 
FROM 
dual;

>> MDSYS.SDO_GEOMETRY(
       3002
      ,3857
      ,NULL
      ,MDSYS.SDO_ELEM_INFO_ARRAY(1,2,1)
      ,MDSYS.SDO_ORDINATE_ARRAY(100045,1175,3000,100050,1180,3010)
   )
```
```sql
SELECT 
dz_wkt_main.sdo2wkt(
    p_input => MDSYS.SDO_GEOMETRY(
        3002
       ,3857
       ,NULL
       ,MDSYS.SDO_ELEM_INFO_ARRAY(1,2,1)
       ,MDSYS.SDO_ORDINATE_ARRAY(100045,1175,3000,100050,1180,3010)
    )
   ,p_add_ewkt_srid => 'TRUE'
) FROM dual;

> SRID=3857;LINESTRING Z(100045 1175 3000,100050 1180 3010)
```
```sql
SELECT dz_wkt_main.sdo2wkt(
    p_input => MDSYS.SDO_GEOMETRY(
        2002
       ,4269
       ,NULL
       ,MDSYS.SDO_ELEM_INFO_ARRAY(1,2,1)
       ,MDSYS.SDO_ORDINATE_ARRAY(
           -75.123456789012345678901234,40.123456789012345678901234
          ,-76.123456789012345678901234,41.123456789012345678901234
        )
    )
   ,p_prune_number => 8
) FROM dual;

> LINESTRING(-75.12345678 40.12345678,-76.12345678 41.12345678)
```
##Installation
Simply execute the deployment script into the schema of your choice.  Then execute the code using either the same or a different schema.  All procedures and functions are publically executable and utilize AUTHID CURRENT_USER for permissions handling.

##Collaboration
Forks and pulls are **most** welcome.  The deployment script and deployment documentation files in the repository root are generated by my [build system](https://github.com/pauldzy/Speculative_PLSQL_CI) which obviously you do not have.  You can just ignore those files and when I merge your pull my system will autogenerate updated files for GitHub.

##Oracle Licensing Disclaimer
Oracle places the burden of matching functionality usage with server licensing entirely upon the user.  In the realm of Oracle Spatial, some features are "[spatial](http://download.oracle.com/otndocs/products/spatial/pdf/12c/oraspatitalandgraph_12_fo.pdf)" (and thus a separate purchased "option" beyond enterprise) and some are "[locator](http://download.oracle.com/otndocs/products/spatial/pdf/12c/oraspatialfeatures_12c_fo_locator.pdf)" (bundled with standard and enterprise).  This differentiation is ever changing.  Thus the definition for 11g is not exactly the same as the definition for 12c.  If you are seeking to utilize my code **without** a full Spatial option license, I do provide a good faith estimate of the licensing required and when coding I am conscious of keeping repository functionality to the simplest licensing level when possible.  However - as all such things go - the final burden of determining if functionality in a given repository matches your server licensing is entirely placed upon the user.  You should **always** fully inspect the code and its usage of Oracle functionality in light of your licensing.  Any reliance you place on my estimation is therefore strictly at your own risk.

In my estimation functionality in the DZ_WKT repository should match Oracle Locator licensing for 10g, 11g and 12c.
