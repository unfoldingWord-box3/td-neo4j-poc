# Load the data

Copy the data to your neo4j import directory, like so:

    cp td_data.csv /usr/local/Cellar/neo4j/3.5.3/libexec/import/td_data.csv

Run the following in neo4j:

    USING PERIODIC COMMIT
    LOAD CSV WITH HEADERS FROM "file:///td_data.csv" AS line
    CREATE (:Language {langID: line.id, name: line.name, code: line.code});

# Create some stuff :)

    CREATE CONSTRAINT ON (n:langID) ASSERT n.langID IS UNIQUE
    CREATE CONSTRAINT ON (n:code) ASSERT n.code IS UNIQUE
    CREATE INDEX ON :Language(langID);
    CREATE INDEX ON :Language(code);

# Create Relationships

    USING PERIODIC COMMIT
    LOAD CSV WITH HEADERS FROM "file:///td_data.csv" AS row
    MATCH (language:Language {langID:row.id})
    MATCH (gl:Language {langID:row.gateway_language_id})
    MERGE (language)-[:HAS_GL]->(gl);

# Sample relationship data

    MATCH p=()-[r:HAS_GL]->() RETURN p LIMIT 25
