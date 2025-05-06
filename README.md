# dv2gen-example
An example how to build a data warehouse using dv2gen-maven-plugin

## Motivation
dv2gen-example is a Maven project using the dv2gen-maven-plugin to 
set up a DataVault 2.0 Data Warehouse.

It can be used as a blueprint how to use dv2gen-maven-plugin.

## SetUp
Imagine you work at ExampleCom and you want to analyze the data of the sales department.

From sales you get a daily file listing all the sales orders of that day.

Your first challenge is to load the sales orders into your data warehouse.

## Sales Data
In directory src/test/resources/ExampleCom-Data/sales/ the orders from the sales department of ExampleCom 
from 2025-04-07 until 2025-04-12 are given.

At the moment this is the only input for our data warehouse.

## Data Warehouse Configuration
In directory src/main/xml the data warehouse configuration in given in two XML files: RawVault.xml and BusinessVault.xml.

This configuration is used by the dv2gen-maven-plugin to generate the needed artifacts for the data warehouse
in target/classes/DataVault.

Additional configurations are 
* src/main/resources/DataVault/dbt_project.yml - the dbt project configuration
* src/main/resources/DatVault/packages.yml - the dbt packages used
* src/main/resources/DataVault/sql_scripts/DatabaseGrants/ - Scripts to initialize the DWH database
* src/main/resources/DataVault/batch - batch scripts to run the DWH processes

These files are copied into target/classes/DataVault.


 