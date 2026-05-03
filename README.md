The final project for the advanced database management class
consisted of developing a useful backbone of an ecommerce website.

The first few phases relied on creating the Entity Relationship Diagram,
the data dictionary. Later phases relied on creation of the OLTP
tables based on the ERD, including proper constraints and rules; additionally,
the tables were populated with many sample records for sanity checks and
for verification that the system works as intended. 

The work here only contains my contribution as an attachment to a final
group project report. The project is intended to be concise, but for
readers interested in delving deeper into the project, the basic and 
non complex backbone for an ETL process is included in the script.sql file.

The last phase was worked by another teammate, and it includes the analytical 
part based on the OLAP tables. A Star schema was used as the basis for my work, which contains one fact table obtained from the source table
order_items and the surrouding context being date (which is not obtained
from OLTP tables), customers, products, categories, supplier, etc.

For future considerations and for a deeper analysis of a large business, a galaxy schema might be more a more logical option. The extract phase consisted
of extracting the data from the OLTP tables and placing it into staging tables. The creation of the staging tables is done once only, as well as
the creation of the dimension tables, unless moving to a more complex
and production ready system. The transform phase consisted of applying rules
to transform, sanitize, validate data, say to capitalize names, lowercase
certain attribute entries, remove leading and trailing whitespaces, and
checking that no invalid entries are inside. For instance, a negative price
should never enter our OLAP tables. The load phase consisted of loading
the transformed data from the staging tables into the fact and dimension
tables. Proper joins were applied in an attempt to reduce many joins during
the querying for the analytical queries.

The last phase was done by another teammate, and it relied on creating
analytical queries and data cubes. While typically full projects are uploaded
to github, rather than only an individual contribution, this repository is
meant as an external link to the project report that must be concise. Hence,
this is why the repository looks very minimal. It is only intended as an
individual contribution to a larger project.
