# database-integration
Integrating Multiple SQL Databases Using PL/SQL and Dynamic SQL

In this project, my team built a SQL script to integrate four different SQL databases. The databases contain information about products/orders in store inventories. The program takes a global query and splits it up into subqueries mapped to the local databases using a metadata table, then executes each subquery. It prints out the global query, the subqueries, the tables for each subquery result, and a merged table with all the subquery results.

The following stored procedures are used:
createOutputTables - creates temporary tables for partial and merged output
showRows - prints tables to console
createSubqueries - checks if token has is a keyword or has a local mapping and appends token/mapping to subquery string
getNextTokenAndDelim - gets next token and delimiter (punctuation) in subquery string
outputLocalMappings - retrieves local mapping from metadata table
checkNull - checks if local mapping is null and casts to varchar if so (to avoid zero-length column error)
checkNum - casts numerical datatyppes to varchar so that columns can be merged using UNION

First, the tables for each local database are created and data (such as the titles of books/CDs in the store inventory, or customer contact information) is inserted. Next the keys table, which contains various SQL keywords, is created. Then the metadata table is created - it contains mappings for all of the local databases. For example, each local database has a table for customer information containing a column for the customer name. However, this column has different names in the various databases. The metadata table contains the global column name 'Customer_Name', which maps to 'CNAME' in Database 1, 'NAME_C' in Database 2, 'NAME' in Database 3, and so on. In an anonymous PL/SQL block, the global input queries are created and the stored producedure createOutputTables is called. This procedure uses the various other procedures to output subqueries and temporary tables containing the results of the subqueries from the local databases.


Sample Input and Output:

Displaying CDs where artist = Michael Jackson:

Global Query Input: SELECT (PRODUCT2_ID) AS Prod_ID,(PRODUCT2_NAME) AS
Prod_Name,(PRODUCT2_ARTIST) AS Prod_Artist FROM PRODUCT2_TB
WHERE(PRODUCT2_ARTIST)='Michael Jackson'

Subquery Output (each subquery corresponds with a different local database):

Subquery 1: SELECT (to_char(MID)) AS PROD_ID,(MTITLE) AS PROD_NAME,(MARTIST) AS
PROD_ARTIST FROM TB_MUSICCD WHERE(MARTIST)='MICHAEL JACKSON'
Subquery 2: SELECT (to_char(CD_ID)) AS PROD_ID,(TITLE) AS PROD_NAME,(ARTIST) AS
PROD_ARTIST FROM MUSIC_CDS WHERE(ARTIST)='MICHAEL JACKSON'
Subquery 3: SELECT (to_char(MUSIC_CDS_S.ID)) AS PROD_ID,(TITLE) AS
PROD_NAME,(ARTIST) AS PROD_ARTIST FROM MUSIC_CDS_S
WHERE(ARTIST)='MICHAEL JACKSON'
Subquery 4: SELECT (CAST(NULL AS varchar(10))) AS PROD_ID,(CAST(NULL AS
varchar(10))) AS PROD_NAME,(CAST(NULL AS varchar(10))) AS PROD_ARTIST FROM
DUAL WHERE(CAST(NULL AS varchar(10)))='MICHAEL JACKSON'

Table Output (from local databases):
Note: If a table shows no data, it means the table does not contain
the specified product.

TABLE 1:
PROD_ID || PROD_NAME || PROD_ARTIST ||
6          THRILLER     MICHAEL JACKSON

TABLE 2:
PROD_ID || PROD_NAME || PROD_ARTIST ||

TABLE 3: 
PROD_ID || PROD_NAME || PROD_ARTIST ||
1       THE ESSENTIAL MJ   MICHAEL JACKSON
2       BAD                MICHAEL JACKSON

TABLE 4: 
PROD_ID || PROD_NAME || PROD_ARTIST ||

MERGED RESULTS:
PROD_ID || PROD_NAME || PROD_ARTIST ||
1       THE ESSENTIAL MJ   MICHAEL JACKSON
2       BAD                MICHAEL JACKSON
6       THRILLER           MICHAEL JACKSON





