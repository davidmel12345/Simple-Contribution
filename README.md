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
order_items and the surrouding context being customers, products, categories, supplier, date (this last one not being obtained from OLTP tables), etc.

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
attachment to a final group project report, and it is intended to show the
individual code contribution to a larger project.

Lastly, a careful observer might have realized that while the dimensional 
model diagram has explicit foreign keys in the fact table that reference
the primary keys (surrogate keys) of the dimension tables, these are not
constraints in the sql scripts. Is this strictly wrong? It depends on
the context. For OLTP tables, we always want to enforce foreign key
constraints that reference the primary key of the corresponding tables.
This is to enforce data integrity. Now, in the OLAP and data warehousing
world, things change a little. It is not that we do not care about
data integrity in the OLAP tables; rather, it is that a well designed
ETL process is the one that guarantees integrity. The ETL process
is the integrity enforcement. Now is it good practice? Why would
someone avoid adding foreign key constraints for the OLAP fact table, all
of which would reference the corresponding primary key (surrogate key)
of the dimension tables? The reason for this is that in OLAP, we are typically
working with many more records than when for OLTP. In OLAP, we are typically
dealing with thousands, millions, or even billions of rows, which 
ultimately means that if we enforce the constraints, the process will be
significantly slowed down. This is why a well designed ETL process
is responsible for data integrity. That said, some engineers still add
the constraints yet disable them when the processes are run (because
otherwise performance is slowed down); something like that can be done
in the following way:

ALTER TABLE fact_sales
ADD CONSTRAINT fk_fact_customer
FOREIGN KEY (customer_key)
REFERENCES dim_customer(customer_key)
ENABLE NOVALIDATE;

Note that in the above piece of code, we would still be adding the foreign key
constraint on fact_sales.customer_key, and it would reference dim_customer.customer_key. However, ENABLE NOVALIDATE allows the system not to check
that because for OLAP this can slow down operations; remember we are dealing
with billions of records at times. Why would someone ever add foreign keys
if they are not even going to be checked then? Well, it appears in user
constraints, bi tools can see it, and it documents the relationship. So 
it acts as metadata, and the optimizer can use the metadata. In summary,
business intelligence tools can automatically detect the relationship, and
you can get peformance benefit without the cost. It basically allows for
query planning. That said, it will still not validate the constraint.
However, as I said before, a well designed ETL process is responsible
for the data integrity part, and you can add the constraints with ENABLE
NOVALIDATE for metadata and documentation, but that option will not
validate it because it is too much overhead.

Slowly changing dimension type 2 attributes such as record_hash are 
added so that we can avoid processing records that have had no updates.
Essentially, if there was an update to a customer, then there will be
a change in the overall hash of the record, so a mismatch 
between hash values would be detected. Additionally, other slowly changing dimensions type 2 attributes such as valid_from, valid_to, and is_current are added because we do not want to override records in OLAP tables but rather track history and to analyze based on changes. Overwriting changes would lose history but would lead to less storage, and this would be a slowly changing dimensions type 1 implementation. I chose slowly changing dimensions type 2 attributes for the dimension tables given the context and the business.
For an ecommerce website, it is a good idea to keep track of history of
changes and to not override them in the OLAP tables because overriding loses
track of those changes and that history. This may lead to more storage used, but it can lead to more powerful conclusions about the behaviors of our customers.