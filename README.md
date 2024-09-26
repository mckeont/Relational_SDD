#Tutorial
https://mckeont.github.io/Relational_SDD/ 


This tutorial is meant to provide an example of how to design a relational spatial database for spatial querying through the use of freely available resources. All you will need is a computer, an internet connection, and some patience.
In this exercise, you will learn how to use point-level data from the US- EPA’s Toxic Release Inventory Program ( TRI ) and join it by spatial location to polygon-level data of census tract boundaries from census.gov.

QGIS and DBeaver can be used to manage the data. LucidCharts can help us easily sketch an Entity-Relationship Diagram. PostgresSQL with the PostGIS extension will be used to write code to process and analyze these data.

The TRI Program contains information about chemical releases from industrial and federal facilities. The data contains information about toxic releases with the following attributes:

Facility name, address, latitude and longitude coordinates, and industry sector codes
Chemical identification and classification information
Quantities of chemicals released on site at the facility
Quantities of chemicals transferred to Publicly Owned Treatment Works (POTWs)
Quantities of chemicals transferred off site to other locations for release/disposal and further waste management
Quantities of chemicals managed through disposal, energy recovery, recycling and treatment; non-production-related waste quantities; production/activity ratio
You can further review documentation about the TRI attributes here:

https://www.epa.gov/toxics-release-inventory-tri-program/tri-basic-data-files-guide

Let’s get to it!
Start by downloading annual .csv data files for Pennsylvania years 1987 to 2019 using the following link:

https://www.epa.gov/toxics-release-inventory-tri-program/tri-basic-data-files-calendar-years-19872018

Once downloaded, open Command Prompt and go to the folder where all the .csv files were downloaded. Make sure all the .csv files are downloaded into the same folder. Once you’re in the correct folder path, use the following code to combine all the .csv files into one named TRI_PA_1987_2018_all_zips.csv

copy *.csv TRI_PA_1987_2018_all_zips.csv
Next you will need to download QGIS, a freely available geographic information system, downloadable here:

https://qgis.org/en/site/forusers/download.html

QGIS will allow us to convert TRI_PA_1987_2018_all_zips.csv into a data format with queryable spatial geometries. This can be done by invoking the following commands:

Add Vector Layer > Add Delimited Text Layer >> import .csv file and designate it as Point Coordinates under the Geometry Definition section.

Once completed, you should now see a visualization of all 143,436 points in this Pennsylvania TRI dataset:
enter image description here
Repeat the above QGIS step with Pennsylvania census tract data. You have the option of importing a Shapefile instead of a .csv file using the following link:

https://catalog.data.gov/dataset/tiger-line-shapefile-2016-state-pennsylvania-current-census-tract-state-based.

A shapefile is a common spatial data format which you may encounter when working with other datasets. If you download the shapefile, you will have a zipped folder containing several files. Keep this folder zipped. In QGIS go to Add Vector Layer > browse for the zipped folder, select it, and import it.

Once completed, you should see the 3,218 census tracts which make up Pennsylvania.

enter image description here

A snippet preview of the PA Census Tract data:
id  |geom                                                                                                                                                                                                                                                           |statefp|countyfp|tractce|geoid      |name   |namelsad            |mtfcc|funcstat|aland    |awater  |intptlat   |intptlon    |
----|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------|--------|-------|-----------|-------|--------------------|-----|--------|---------|--------|-----------|------------|
   1|MULTIPOLYGON (((-74.98425399999999 40.055901999999996, -74.983544 40.056663, -74.982891 40.057351, -74.982569 40.057666999999995, -74.982286 40.057925999999995, -74.981601 40.058493999999996, -74.980903 40.059005, -74.980683 40.059152, -74.98013999999999 |42     |017     |100102 |42017100102|1001.02|Census Tract 1001.02|G5020|S       |  7608630| 2509728|+40.0745595|-074.9405810|
   6|MULTIPOLYGON (((-74.96746 40.131251, -74.96718899999999 40.131606, -74.96663699999999 40.132369, -74.966507 40.13254, -74.966471 40.132587, -74.96624299999999 40.132886, -74.96600099999999 40.133176, -74.965823 40.133415, -74.965548 40.133722999999996, -7|42     |017     |100206 |42017100206|1002.06|Census Tract 1002.06|G5020|S       |  4735309|       0|+40.1347795|-074.9441709|
   2|MULTIPOLYGON (((-74.984893 40.057286999999995, -74.984805 40.057399, -74.984365 40.057736999999996, -74.984129 40.057837, -74.98387 40.057947999999996, -74.98331 40.058187, -74.98301699999999 40.058367, -74.9829 40.058367, -74.982372 40.058682999999995, -|42     |017     |100103 |42017100103|1001.03|Census Tract 1001.03|G5020|S       |  1572548|    9817|+40.0687610|-074.9713318|
   3|MULTIPOLYGON (((-75.233139 40.150928, -75.23261 40.151449, -75.232201 40.151854, -75.230049 40.153973, -75.229694 40.154326, -75.228445 40.155557, -75.22841799999999 40.155584999999995, -75.227924 40.156071999999995, -75.227836 40.15616, -75.22782 40.1561|42     |091     |201411 |42091201411|2014.11|Census Tract 2014.11|G5020|S       |  2409072|   35484|+40.1444849|-075.2140269|
  46|MULTIPOLYGON (((-79.72778 40.41274, -79.72746699999999 40.412867999999996, -79.727279 40.41317, -79.727205 40.413575, -79.727104 40.413636, -79.727079 40.41377, -79.72690899999999 40.413961, -79.72645 40.414232, -79.72531 40.414514, -79.724991 40.414522, |42     |129     |802103 |42129802103|8021.03|Census Tract 8021.03|G5020|S       | 13216699|       0|+40.4111529|-079.6840287|
  47|MULTIPOLYGON (((-79.55927899999999 40.181917, -79.559148 40.182455, -79.559111 40.182573999999995, -79.558984 40.182984, -79.558871 40.183202, -79.558652 40.183623, -79.55854 40.184027, -79.558376 40.184619999999995, -79.558155 40.185418999999996, -79.557|42     |129     |806900 |42129806900|8069   |Census Tract 8069   |G5020|S       | 26895523|   96851|+40.1608151|-079.5207125|
  48|MULTIPOLYGON (((-79.57240999999999 40.239598, -79.572243 40.23965, -79.57195399999999 40.239644999999996, -79.570971 40.23963, -79.570889 40.239613999999996, -79.57022599999999 40.239357, -79.57019799999999 40.239464999999996, -79.57000099999999 40.240206|42     |129     |804501 |42129804501|8045.01|Census Tract 8045.01|G5020|S       | 17597811|       0|+40.2550632|-079.5425163|
   4|MULTIPOLYGON (((-74.964883 40.077273, -74.96455499999999 40.077227, -74.96452599999999 40.077223, -74.964409 40.077207, -74.964231 40.077217999999995, -74.963787 40.077295, -74.963459 40.077411, -74.96273699999999 40.077839, -74.962465 40.078075, -74.9623|42     |017     |100104 |42017100104|1001.04|Census Tract 1001.04|G5020|S       |  1966895|   12199|+40.0918428|-074.9501748|
Next setup DBeaver, a SQL client and database administration tool which we will use to manage the TRI data and create entity-relationship tables. DBeaver is freely available through the following link: dbeaver.io.

Once DBeaver is setup, create a new database connection to PostgresSQL. PostgreSQL is a powerful open source object-relational database system which uses SQL as its coding language. The first thing you’ll want to do is add the PostGIS extension. PostGIS is an open source software program that adds support for geographic objects to the PostgreSQL object-relational database.

To do this, type the following code into DBeaver’s text editor:

create extension postgis;
You can read more about PostGIS functions here:

postgis.net/workshops/postgis-intro/

Great! Next we’ll want to connect our newly created spatial data, stored in QGIS to DBeaver.

In order to do this, a new schema needs to first be created in DBeaver. Do this by right-clicking in the database navigator and choosing create > schema then naming it something relevant. Once created, go back to QGIS and connect to the schema by connecting to DB Manager under the database tab. You should now have your data in DBeaver.

Next, we will have to normalize our data. The data comes with over 110 fields, many of which can be calculated through spatial querying. For this tutorial, the TRI data was normalized into five entity-relationship tables, each containing a primary key for unique entries, as well as foriegn keys to connect to other tables’ primary keys. lucidchart.com can be used to diagram these tables. The list below describes these tables in further detail.

Facility: Contains facility location information, geometries, address, name, and TRI emission identifier.
Company: Contains parent company information, NAICS and SIC information.
Release: Contains emission release quantity, unit of measure, year, industry code, and form type.
Chemical: Contains information about the chemical released such as its classifcation, carcinogen status, metal status, clean air act chemical status, and cas compound identifier.
Type: Contains information about the type of release such as air, water, underground, and surface.
The following image shows and Entity-Relationship-Diagram (ERD) of how the tables are connected. The entity connections use a crows feet convention, which can be further reviewed in lucidchart.com.

enter image description here

Creating Tables in DBeaver
The following SQL script inputs the tables described above into DBeaver. Once created, we are ready to begin asking our spatial queries!

-- set the path and rename the tri table
set search_path to tri_pa_1987_2018, public;
alter table tri_pa_1987_2018."TRI_PA_1987_2018_all_zips"
rename to tri_pa_1987_2018;

select count(*) 
from tri_pa_1987_2018;

--create the company table
drop table if exists company cascade;
create table company (
  company_id serial primary key,
  parent_co_name varchar,
  parent_co_db_num varchar,
  primary_naics varchar,
  naics2 varchar,
  naics3 varchar,
  naics4 varchar,
  naics5 varchar,
  naics6 varchar,
  primary_sic varchar,
  sic2 varchar,
  sic3 varchar,
  sic4 varchar,
  sic5 varchar,
  sic6 varchar
  );
  
insert into company (parent_co_name, 
parent_co_db_num, primary_naics, naics2, naics3, naics4, 
naics5, naics6, primary_sic, sic2, sic3, sic4, sic5, sic6)
select
distinct "112. parent co name" :: varchar, 
"113. parent co db num" :: varchar, 
"23. primary naics" :: varchar, 
"24. naics 2" :: varchar, 
"25. naics 3" :: varchar, 
"26. naics 4" :: varchar, 
"27. naics 5" :: varchar,
"28. naics 6" :: varchar, 
"17. primary sic" :: varchar, 
"18. sic 2" :: varchar,
"19. sic 3" :: varchar,
"20. sic 4" :: varchar, 
"21. sic 5" :: varchar, 
"22. sic 6" :: varchar
from tri_pa_1987_2018;

--create the facility table
drop table if exists facility cascade;
create table facility (
  trifd varchar primary key,
  facility_name varchar,
  federal_facility varchar,
  street_address varchar,
  city varchar,
  county varchar,
  st varchar,
  zip varchar,
  geom geometry,
  company_id int references company (company_id)
  );
  
insert into facility (trifd, facility_name, 
federal_facility, street_address, city, 
county, st, zip, geom, company_id)
select
distinct "2. trifd" :: varchar, 
"4. facility name" :: varchar, 
"14. federal facility" :: varchar,
"5. street address" :: varchar, 
"6. city" :: varchar, 
"7. county" :: varchar, 
"8. st" :: varchar, 
"9. zip" :: varchar, 
geom :: geometry,
( select company_id
  from company as a
   where a.parent_co_db_num = b."113. parent co db num"
   limit 1)
from tri_pa_1987_2018 as b;


--create the chemical table
drop table if exists chemical cascade;
create table chemical (
  chemical varchar primary key,
  srs_id int,
  classification varchar,
  metal_category int,
  metal varchar,
  carcinogen varchar,
  cas_compound_id varchar,
  clean_air_act_chemical varchar
  );
  
insert into chemical (chemical, srs_id, 
classification, metal_category, metal,
carcinogen, cas_compound_id, clean_air_act_chemical )
select
distinct "30. chemical" :: varchar, 
"32. srs id" :: int, 
"34. classification" :: varchar, 
"36. metal category" :: int, 
"35. metal" :: varchar, 
"37. carcinogen" :: varchar, 
"31. cas #/compound id" :: varchar, 
"33. clean air act chemical" :: varchar
from tri_pa_1987_2018;


--create the release table
drop table if exists release cascade;
create table release (
  release_id serial primary key,
  year int,
  quantity int,
  unit_of_measure varchar,
  industry_sector_code varchar,
  industry_sector varchar,
  form_type varchar,
  trifd varchar references facility (trifd),
  chemical varchar references chemical (chemical)
  );
  
insert into release (year, quantity, 
unit_of_measure, industry_sector_code, 
industry_sector, form_type, trifd, chemical)
select
distinct  "1. year" :: int, 
"54. on-site release total" :: int, 
"39. unit of measure" :: varchar, 
"15. industry sector code" :: varchar, 
"16. industry sector" :: varchar, 
"38. form type" :: varchar, 
"2. trifd" :: varchar, 
"30. chemical" :: varchar
from tri_pa_1987_2018;

--create the type table
drop table if exists type cascade;
create table type (
  type_id serial primary key,
  fugitive_air float,
  stack_air float,
  water float,
  other_landfills float,
  rcra_c_landfill float,
  land_treatment float,
  surface_impendmnt float,
  rcra_surface_im float,
  other_surface float,
  other_disposal float,
  underground int,
  underground_cl_l float,
  underground_c_ll_v float,
  release_id serial references release (release_id)
  );
  
insert into type (fugitive_air, stack_air, water, 
other_landfills, rcra_c_landfill, land_treatment, 
surface_impendmnt, rcra_surface_im, other_surface, 
other_disposal, underground, underground_cl_l, 
underground_c_ll_v, release_id )
select
distinct "40. 5.1 - fugitive air" :: float, 
"41. 5.2 - stack air" :: float, 
"42. 5.3 - water" :: float, 
"48. 5.5.1b - other landfills" :: float, 
"47. 5.5.1a - rcra c landfill" :: float, 
"49. 5.5.2 - land treatment" :: float, 
"50. 5.5.3 - surface impndmnt" :: float,
"51. 5.5.3a - rcra surface im" :: float, 
"52. 5.5.3b - other surface i" :: float, 
"53. 5.5.4 - other disposal" :: float,
"43. 5.4 - underground" :: float, 
"44. 5.4.1 - underground cl i" :: float, 
"45. 5.4.2 - underground c ii-v" :: float,
(select release_id
 from release as a 
  where a.trifd = b."2. trifd"
  limit 1)
from tri_pa_1987_2018 as b;
Spatial Queries
You are now ready to ask an endless amount of spatial queries. Work through the following four examples. Once completed, read through the PostGIS documentation and try out new functions to play around with your own queries!
PostGIS Functions for Spatial Relationships: https://web.archive.org/web/20170913113302/http://workshops.boundlessgeo.com/postgis-intro/spatial_relationships.html
Spatial Query 1:
For the year 1990, what chemicals in which census tracts, along with their TRIFD identifier, reported their max emission as greater than 1,000,000?

Use the facility and release tables to join to the Census Tract table. Think about how to select chemical names, TRI emission identifiers ( trifd), corresponding PA census tract, year, and emissions.

You will want to use an aggegrate function to choose the max emissions. Fortunately, we designed these tables to be relational. Join the facility table, which contains information about the facility’s location to the release table which contains information about the emission. This join can be done by the trifd field which is the primary key of the facility table, and the foreign key of the release table.

Next join to the census tract table by using a PostGIS function: ST_Intersects .

join tl_2016_42_tract
  on ST_Intersects(ST_Transform(tl_2016_42_tract.geom, 4326) 
     :: geography, facility.geom)
This allows for a join by spatial location of the TRI points which intersect to each census tract polygon. Oftentimes, you may recieve spatial data with differing projection systems. The ST_SRID(geom) function allows us to check the geographic projection of each layer.

select st_srid(geom)
from tl_2016_42_tract;

st_srid|
-------|
   4269|
select st_srid(geom)
from facility;

st_srid|
-------|
   4326|
In our case, the The PA Census Tracts layer uses the SRID 4269 https://epsg.io/4269 coordinate system. The TRI data uses SRID 4326 https://epsg.io/4326 coordinate system . Since these projection systems differ, the geometry of the census tract layer has to be transformed to match the TRI facilities in order for this query to work, using the ST_Transform function. Try this out, then when you’re ready check your query to the completed code:

select a.trifd, 
  b.chemical, 
  max(b.quantity) as max_emission_in_Census_Tract, 
  b.year, c.namelsad, 
  c.geom as Census_Tract_Geometries, 
  a.geom as TRI_Geometries
from facility as a 
join release as b 
  on a.trifd = b.trifd
join 
 tl_2016_42_tract as c 
 on ST_Intersects(ST_Transform(c.geom, 4326)
 :: geography, a.geom)
where b.year = 1990 
  and b.quantity > 1000000
group by a.trifd, 
  b.chemical, 
  c.namelsad, 
  b.chemical, 
  b.year, 
  c.geom, 
  a.geom;
RESULT:

trifd          |chemical             |max_emission_in_census_tract|year|namelsad            |census_tract_geometries                                                                                                                                                                                                                                        |tri_geometries              |
---------------|---------------------|----------------------------|----|--------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------|
15025SSCLR400ST|AMMONIA              |                     1140000|1990|Census Tract 4927   |MULTIPOLYGON (((-79.90074299999999 40.312036, -79.90044499999999 40.312205, -79.90040499999999 40.312239999999996, -79.900145 40.312464, -79.89992199999999 40.312317, -79.89967899999999 40.312158, -79.89958899999999 40.312098999999996, -79.899351 40.31232|POINT (-79.876911 40.305088)|
15061ZNCCR300FR|ZINC COMPOUNDS       |                     1310750|1990|Census Tract 6055   |MULTIPOLYGON (((-80.39197999999999 40.639486999999995, -80.39195099999999 40.639528, -80.39187799999999 40.639572, -80.391807 40.639595, -80.391533 40.639629, -80.391305 40.639688, -80.39120799999999 40.639713, -80.39100599999999 40.639801999999996, -80.3|POINT (-80.3365 40.67058)   |
15902JHNST545CE|CHROMIUM             |                     1141510|1990|Census Tract 6      |MULTIPOLYGON (((-78.91914299999999 40.295201999999996, -78.918798 40.29609, -78.918695 40.296333, -78.918284 40.297312, -78.91720699999999 40.299655, -78.916718 40.300519, -78.91613199999999 40.301024, -78.915487 40.301612999999996, -78.914953 40.30196199|POINT (-78.91209 40.29904)  |
16323FRNKL600AT|1,1,1-TRICHLOROETHANE|                     1243000|1990|Census Tract 2003   |MULTIPOLYGON (((-79.87272 41.40067, -79.871202 41.406532999999996, -79.870919 41.406821, -79.870499 41.406797999999995, -79.867976 41.406403, -79.86777 41.406501, -79.867346 41.407641, -79.867266 41.407624, -79.867233 41.407713, -79.86740999999999 41.4079|POINT (-79.84519 41.40324)  |
17601RRDNN216GR|TOLUENE              |                     1182000|1990|Census Tract 132.02 |MULTIPOLYGON (((-76.27807299999999 40.04969, -76.277908 40.049648999999995, -76.277908 40.049667, -76.277913 40.04972, -76.277906 40.049807, -76.277844 40.049907, -76.277816 40.049983, -76.277805 40.050013, -76.277784 40.050098, -76.277709 40.050202999999|POINT (-76.26023 40.04903)  |
18016BTHLH501EA|ZINC COMPOUNDS       |                     1070600|1990|Census Tract 113    |MULTIPOLYGON (((-75.378625 40.61529, -75.3781 40.615308999999996, -75.37562 40.615398, -75.375254 40.615485, -75.374757 40.615603, -75.37343899999999 40.615915, -75.372361 40.616171, -75.37182399999999 40.616298, -75.367705 40.616479, -75.36693199999999 4|POINT (-75.344902 40.611803)|
19007MCMPNGREEN|TOLUENE              |                     1900250|1990|Census Tract 1004.03|MULTIPOLYGON (((-74.860519 40.124862, -74.86049299999999 40.125, -74.859884 40.126311, -74.859766 40.126446, -74.85966499999999 40.126796, -74.85952999999999 40.127075999999995, -74.859529 40.127345999999996, -74.85941199999999 40.127435999999996, -74.859|POINT (-74.852234 40.12751) |
19030SSFRLMAILS|MANGANESE COMPOUNDS  |                     7822200|1990|Census Tract 1058.09|MULTIPOLYGON (((-74.859766 40.175941, -74.85810599999999 40.176533, -74.85605699999999 40.177262999999996, -74.854479 40.177827, -74.85049699999999 40.179249, -74.849201 40.179711999999995, -74.84846 40.179978, -74.84827299999999 40.18005, -74.847847 40.1|POINT (-74.850518 40.17182) |
19137LLDSGMARGA|CUMENE               |                     1053700|1990|Census Tract 184    |MULTIPOLYGON (((-75.075687 40.005553, -75.074677 40.006358999999996, -75.073871 40.006979, -75.07369299999999 40.007114, -75.07340099999999 40.007338, -75.07328 40.007436, -75.07286599999999 40.007771, -75.072735 40.007877, -75.07270199999999 40.007903999|POINT (-75.072105 40.003765)|

enter image description here
enter image description here

Spatial Query 2:
List the census tracts which contain chemicals that are classified as a carcinogen and that emitted between 10,000 and 15,000 pounds for the year 2018.

The second spatial query uses the facility, chemical, release, and PA Census Tracts tables. In this query, we’re interested in the names of the chemicals, their emission quantity between 10,000 and 15,000, emission unit of measure, and census tract name, for emissions which are classified as a carcinogen in the year 2018. This query requires identifying all the TRI points which intersect each census tract layer. Again, since these projection systems were different, the geometry of the census tract layer had to be transformed to match the TRI facilities in order for this query to work.

select a.chemical, 
  a.quantity, 
  a.unit_of_measure, 
  d.namelsad, 
  d.geom as Census_Tract_Geometries, 
  c.geom as TRI_Geometries
from release as a 
join chemical as b 
	on a.chemical = b.chemical 
join facility as c 
	on a.trifd = c.trifd 
join
   tl_2016_42_tract as d
   on ST_Intersects(ST_Transform(d.geom, 4326) 
   :: geography, c.geom)
where b.carcinogen ilike 'YES' 
  and a.year = 2018 
  and a.quantity between 10000 and 15000
group by a.chemical, 
  a.quantity, 
  a.unit_of_measure, 
  d.namelsad, 
  d.geom, 
  c.geom;
RESULT:

chemical       |quantity|unit_of_measure|namelsad           |census_tract_geometries                                                                                                                                                                                                                                        |tri_geometries              |
---------------|--------|---------------|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------|
ACETALDEHYDE   |   12909|Pounds         |Census Tract 205.21|MULTIPOLYGON (((-76.87102399999999 39.876773, -76.870905 39.87683, -76.870548 39.877005, -76.87043 39.877063, -76.87030299999999 39.877128, -76.869924 39.877326, -76.869798 39.877392, -76.869754 39.877846999999996, -76.870409 39.879346999999996, -76.87058|POINT (-76.867315 39.87159) |
ACETALDEHYDE   |   13314|Pounds         |Census Tract 9509  |MULTIPOLYGON (((-76.52760099999999 41.769757999999996, -76.527132 41.781484, -76.52694 41.787002, -76.526929 41.787258, -76.52689699999999 41.788018, -76.52689099999999 41.788157999999996, -76.526893 41.788312, -76.526764 41.791167, -76.526709 41.79120599|POINT (-76.42046 41.76242)  |
DICHLOROMETHANE|   13152|Pounds         |Census Tract 3     |MULTIPOLYGON (((-76.107387 40.857152, -76.09964599999999 40.861514, -76.099336 40.861686999999996, -76.098406 40.862204999999996, -76.098097 40.862379, -76.097808 40.862539999999996, -76.09386599999999 40.86479, -76.086626 40.868922999999995, -76.083141 4|POINT (-76.024401 40.834626)|
DICHLOROMETHANE|   13407|Pounds         |Census Tract 807   |MULTIPOLYGON (((-76.718696 40.923052, -76.71787599999999 40.923742999999995, -76.715521 40.925663, -76.713977 40.926699, -76.713561 40.926978999999996, -76.71274199999999 40.927347999999995, -76.70985999999999 40.928571999999996, -76.706893 40.92939399999|POINT (-76.64329 40.96131)  |
STYRENE        |   11517|Pounds         |Census Tract 4610  |MULTIPOLYGON (((-80.16148 40.523803, -80.160349 40.523426, -80.160319 40.523416, -80.160191 40.523379, -80.159765 40.523230999999996, -80.159154 40.523081, -80.157037 40.522563, -80.155788 40.522348, -80.154083 40.522051999999995, -80.15395 40.52202, -80.|POINT (-80.09127 40.49761)  |
STYRENE        |   13176|Pounds         |Census Tract 9603  |MULTIPOLYGON (((-78.69867099999999 40.161449999999995, -78.691099 40.178796999999996, -78.69089 40.179276, -78.690428 40.180336, -78.689911 40.181519, -78.689865 40.181625, -78.689533 40.182386, -78.68782499999999 40.186299, -78.686781 40.188691999999996,|POINT (-78.61066 40.18217)  |
enter image description here
enter image description here

Spatial Query 3:
For all years, identify the chemicals along with their corresponding census tracts that released greater than 4,500,000 pounds into water. Provide your answer in descending order.

The third spatial query uses four tables: type, release, facility, and PA Census Tracts. In this query, we’re interested in the names of the chemicals which are emitted into water at over 4,500,000 pounds. Remember to order the output in descending order and transform the geography to 4326.

select a.water as water_emission_lbs, 
  d.namelsad, 
  b.chemical as chemical_name, 
  c.geom as TRI_Geometries, 
  d.geom as Census_Tract_Geometries
from type as a 
join release as b 
  on a.release_id = b.release_id 
join facility as c 
  on b.trifd = c.trifd
join
   tl_2016_42_tract as d
   on ST_Intersects(ST_Transform(d.geom, 4326) 
   :: geography, c.geom)
where a.water > 4500000
order by d.namelsad, b.chemical, a.water desc;
RESULT:

water_emission_lbs|namelsad          |chemical_name                                      |tri_geometries            |census_tract_geometries                                                                                                                                                                                                                                        |
------------------|------------------|---------------------------------------------------|--------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
         6003084.0|Census Tract 141  |SULFURIC ACID (1994 AND AFTER ACID AEROSOLS" ONLY)"|POINT (-75.2283 40.69789) |MULTIPOLYGON (((-75.233419 40.70308, -75.2333 40.703089999999996, -75.233083 40.703122, -75.231374 40.703329, -75.229438 40.703564, -75.225777 40.704009, -75.223604 40.704263999999995, -75.223439 40.704297, -75.221284 40.7046, -75.221383 40.705152, -75.22|
         5000000.0|Census Tract 60.02|FORMIC ACID                                        |POINT (-75.54086 40.63257)|MULTIPOLYGON (((-75.61489 40.622836, -75.61222099999999 40.623872, -75.61092 40.624362999999995, -75.61022 40.624635, -75.60972 40.624815, -75.606764 40.625946, -75.604953 40.626613, -75.602297 40.627615, -75.601726 40.627829999999996, -75.601531 40.62790|
             3.2E7|Census Tract 9030 |ZINC COMPOUNDS                                     |POINT (-79.94273 40.82926)|MULTIPOLYGON (((-79.97237 40.885918, -79.97213599999999 40.885858999999996, -79.971437 40.885681999999996, -79.971204 40.885622999999995, -79.971155 40.88561, -79.97101099999999 40.885571, -79.970963 40.885559, -79.970928 40.885549, -79.970824 40.885521, |
             3.1E7|Census Tract 9030 |ZINC COMPOUNDS                                     |POINT (-79.94273 40.82926)|MULTIPOLYGON (((-79.97237 40.885918, -79.97213599999999 40.885858999999996, -79.971437 40.885681999999996, -79.971204 40.885622999999995, -79.971155 40.88561, -79.97101099999999 40.885571, -79.970963 40.885559, -79.970928 40.885549, -79.970824 40.885521, |
             2.8E7|Census Tract 9030 |ZINC COMPOUNDS                                     |POINT (-79.94273 40.82926)|MULTIPOLYGON (((-79.97237 40.885918, -79.97213599999999 40.885858999999996, -79.971437 40.885681999999996, -79.971204 40.885622999999995, -79.971155 40.88561, -79.97101099999999 40.885571, -79.970963 40.885559, -79.970928 40.885549, -79.970824 40.885521, |
             2.6E7|Census Tract 9030 |ZINC COMPOUNDS                                     |POINT (-79.94273 40.82926)|MULTIPOLYGON (((-79.97237 40.885918, -79.97213599999999 40.885858999999996, -79.971437 40.885681999999996, -79.971204 40.885622999999995, -79.971155 40.88561, -79.97101099999999 40.885571, -79.970963 40.885559, -79.970928 40.885549, -79.970824 40.885521, |
             1.2E7|Census Tract 9030 |ZINC COMPOUNDS                                     |POINT (-79.94273 40.82926)|MULTIPOLYGON (((-79.97237 40.885918, -79.97213599999999 40.885858999999996, -79.971437 40.885681999999996, -79.971204 40.885622999999995, -79.971155 40.88561, -79.97101099999999 40.885571, -79.970963 40.885559, -79.970928 40.885549, -79.970824 40.885521, |
         9800000.0|Census Tract 9030 |ZINC COMPOUNDS                                     |POINT (-79.94273 40.82926)|MULTIPOLYGON (((-79.97237 40.885918, -79.97213599999999 40.885858999999996, -79.971437 40.885681999999996, -79.971204 40.885622999999995, -79.971155 40.88561, -79.97101099999999 40.885571, -79.970963 40.885559, -79.970928 40.885549, -79.970824 40.885521, |
         8400000.0|Census Tract 9030 |ZINC COMPOUNDS                                     |POINT (-79.94273 40.82926)|MULTIPOLYGON (((-79.97237 40.885918, -79.97213599999999 40.885858999999996, -79.971437 40.885681999999996, -79.971204 40.885622999999995, -79.971155 40.88561, -79.97101099999999 40.885571, -79.970963 40.885559, -79.970928 40.885549, -79.970824 40.885521, |
         7560000.0|Census Tract 9505 |CATECHOL                                           |POINT (-78.67752 41.49085)|MULTIPOLYGON (((-78.69776499999999 41.502702, -78.696818 41.502686, -78.69479 41.502654, -78.689028 41.502576, -78.688915 41.502583, -78.68880399999999 41.502637, -78.68856799999999 41.502759, -78.68838199999999 41.502953, -78.688046 41.503229999999995, -|
enter image description here
enter image description here

Spatial Query 4:
Perhaps you may be interested in knowing which TRI emissions are near a census tract but do not actually fall within their geographic boundaries. Find the top five closest TRI emissions to each census tract, but limit to the top 5 census tracts with the farthest nearest neighbor. Report in meters and in descending order.

This fourth spatial query uses a KNN operator ‘<#>’ to calculate the distance, of the nearest TRI emission to each census tract. This query uses a cross lateral join, which allows rows from the census tract table to assume multiple values from the TRI emissions table. In this join, we can set a limit to identify the closest 5 TRI emissions to each census tract, rather than generate a list of distances of ALL emissions to ALL census tracts, which would have been a time-intensive and massive query. This query was then further refined to limit to the 5 “farthest” nearest neighbors.

select a.trifd, c.namelsad,
  ST_Distance(ST_Transform(c.geom, 4326) 
    :: geography, a.geom) as Distance_in_Meters, 
  c.geom as Census_Tract_Geometries,
  a.geom as TRI_Geometries
from facility as a 
cross join lateral 
  (select b.namelsad, b.geom
    from tl_2016_42_tract as b 
    order by b.geom <#> a.geom limit 5) as c
 order by Distance_in_Meters desc limit 5;  
RESULT:

trifd          |namelsad         |distance_in_meters|census_tract_geometries                                                                                                                                                                                                                                        |tri_geometries              |
---------------|-----------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------|
16923HRBRTGOLDR|Census Tract 9504|    26363.68680118|MULTIPOLYGON (((-78.205353 41.818427, -78.202683 41.818385, -78.19608 41.818284, -78.19467499999999 41.818262, -78.19200599999999 41.818221, -78.191801 41.818217, -78.191186 41.818207, -78.190981 41.818204, -78.190771 41.818200999999995, -78.190141 41.818|POINT (-77.86718 41.98474)  |
16825BRDFRMCDOW|Census Tract 105 |    24821.18701032|MULTIPOLYGON (((-78.282781 40.73641, -78.281764 40.737044, -78.279426 40.738313999999995, -78.279101 40.738519, -78.27499499999999 40.740721, -78.272792 40.741918, -78.271352 40.742701, -78.25218 40.753116, -78.251632 40.753414, -78.24792 40.75543, -78.24|POINT (-78.299167 41.003611)|
15501PBSQC10310|Census Tract 213 |     23488.2263498|MULTIPOLYGON (((-79.05595799999999 39.723422, -79.053146 39.72607, -79.05188299999999 39.727270999999995, -79.050102 39.728967, -79.04955799999999 39.729465999999995, -79.049095 39.729917, -79.049042 39.729952999999995, -79.04901199999999 39.7299889999999|POINT (-79.080029 40.086465)|
16825GLNGRBOX33|Census Tract 105 |    23373.90045074|MULTIPOLYGON (((-78.282781 40.73641, -78.281764 40.737044, -78.279426 40.738313999999995, -78.279101 40.738519, -78.27499499999999 40.740721, -78.272792 40.741918, -78.271352 40.742701, -78.25218 40.753116, -78.251632 40.753414, -78.24792 40.75543, -78.24|POINT (-78.31019 40.98099)  |
16915LHLNC203CH|Census Tract 9602|    22350.41555333|MULTIPOLYGON (((-78.420968 41.478232999999996, -78.42092 41.479676, -78.42088799999999 41.480458999999996, -78.42070199999999 41.485188, -78.420653 41.486402, -78.420625 41.487138, -78.42062399999999 41.487178, -78.420622 41.487227999999995, -78.420591 41|POINT (-78.02423 41.76801)  |
enter image description here
enter image description here

Improving Query Optimization
Fortunately, these datasets are relatively small in the cosmic universe of spatial information. However, you may encounter datasets with hundreds of millions of rows, or you may ask complicated queries which take a lot of computing power to read and write operations. The KNN operator for the fourth query could generate a massive output if no limits were set. To optimize you may want to construct an index or denormalize the data. If you find that the time it takes to execute a query takes a long time ( could be minutes), then you may want to create an index or denormalize.

Index Construction
There are tools available online like SQL Profiler which can help you identify queries which are peforming at suboptimal levels:

https://www.sqlmvp.org/sql-server-profiler-trace/

Either way, an index allows a query to search data quickly by searching part of a table rather than the entire dataset. Try constructing an index using the following format:

create index index_name 
on table_name(column_to_index);
Denormalization
The ERD shown earlier shows how the TRI data have been normalized into five tables. The purpose of this is to reduce redundancy and eliminate deletion and insertion errors. However you may want to minimize invoking several joins to get to a certain attribute. You may have noticed that the geom is only found in the facility table. You may not want to always include the facility table in your queries. However at the moment, you cannot avoid joining to it if you want to access the geom. Go ahead and try adding the GEOM field to the other four TRI tables, then get on out there and create some new queries!

How about exploring the Toxic Release Inventory through Leaflet.js: https://mckeonttemple.github.io/Draft_tri/
I hope you enjoyed this tutorial!
