# dv2gen-example
An example how to build a data warehouse using dv2gen-maven-plugin

## Motivation
dv2gen-example is a Maven project using the dv2gen-maven-plugin to 
set up a DataVault 2.0 Data Warehouse.

It can be used as a blueprint how to use dv2gen-maven-plugin.

## SetUp
Imagine you work at ExampleCom and you want to analyze the data of the sales department.

From sales you get a daily CSV file - listing all the sales orders of that day.

Your first challenge is to load the sales orders into your data warehouse.

## Sales Data
In directory ``src/test/resources/ExampleCom-Data/sales/`` the orders from the sales department of ExampleCom 
from 2025-04-07 until 2025-04-12 are given.

At the moment this is the only input for our data warehouse.

The CSV files from the sales department are placed into a file import directory on the server running the data warehouse.
The location of the directory is defined in src/main/xml/RawVault.xml. 
The XPath is: ``concat(/system/target[@name = "dev"]/@import_dir, '/', /db_sources/db_source[name = "sales"]/file_import/@import_subdir)``

## Data Warehouse Configuration
In directory ``src/main/xml`` the data warehouse configuration in given in two XML files: ``RawVault.xml`` and ``BusinessVault.xml``.

This configuration is used by the dv2gen-maven-plugin to generate the needed artifacts for the data warehouse
in ``target/classes/DataVault``. 

Some additional files are generated in ``target/classes/templates``. These files must be manually checked and copied 
to ``target/classes/DataVault`` and to the user's home directory. 

In the templates directory the following files are placed: 
* ``target/classes/templates/DataVault/dbt_project.yml`` - the dbt project configuration, add individual configurations
and copy to ``target/classes/DataVault/dbt_project.yml`` 
* ``target/classes/templates/DataVault/sql_scripts/DatabaseGrants/`` - Scripts to initialize the DWH database

You can add files in ``src/main/resources/DataVault`` to overwrite the generated artifacts mentioned earlier or to add your own scripts and models to the DataVault.

## Build Process
The Maven build (with `mvn clean install`) puts all required results into target/classes/DataVault.

Before the build some placeholders have to be replaced with actual values:
in src/main/xml/RawVault.xml:
   * the location of the datavault_dir, import_dir and log_dir in the corresponding Attributes in /raw_vault/system/target

## Data Load Processes
All batch scripts are written in Powershell so that they can be used in different operating system environments. 

To load the data into the DWH some batch scripts are provided:
* 0102_import.ps1 - to import the data from the file import CSV files of a data source to the source tables in the DWH database
* 0201_psa.ps1 - to load the data from the source tables into the PSA tables for one load_date
* 0301_load_full_refresh.ps1 - to load the data into the DWH tables for one load_date ignoring the actual DWH data (for a full refresh of the DWH)
* 0302_load.ps1 - to load the data into the DWH tables for one load_date
* set_dwh_env.ps1 - to set ``$project_dir`` and ``$home_dir`` and to define ``$target`` and ``$db_user`` according to 
``$project_dir/dbt_project.yml`` and ``$home_dir/.dbt/profiles.yml`` 

To access the Postgres DB the Powershell module DbModule.psm1 is used.
To run the dbt commands the Powershell module dbtModule.psm1 is used.
To access the YAML config files YamlModule.psm1 and json_from_yaml.py are used.

This is an example how to load the PSA tables and the DWH tables for the test data:
```
# Powershell script to load the test data into the DWH

. /Users/christophoberle/IdeaProjects/dv2gen-example/target/classes/DataVault/batch/set_dwh_env.ps1

Set-Location $project_dir/batch

$load_date = "2025-04-07"
./0102_import.ps1 -db_user $db_user -target $target -db_source sales -load_date $load_date
./0201_psa.ps1 -target $target -load_date $load_date
./0301_load_full_refresh.ps1 -target $target -load_date $load_date

$load_date = "2025-04-08"
./0102_import.ps1 -db_user $db_user -target $target -db_source sales -load_date $load_date
./0201_psa.ps1 -target $target -load_date $load_date
./0302_load.ps1 -target $target -load_date $load_date

$load_date = "2025-04-09"
./0102_import.ps1 -db_user $db_user -target $target -db_source sales -load_date $load_date
./0201_psa.ps1 -target $target -load_date $load_date
./0302_load.ps1 -target $target -load_date $load_date

$load_date = "2025-04-10"
./0102_import.ps1 -db_user $db_user -target $target -db_source sales -load_date $load_date
./0201_psa.ps1 -target $target -load_date $load_date
./0302_load.ps1 -target $target -load_date $load_date

$load_date = "2025-04-11"
./0102_import.ps1 -db_user $db_user -target $target -db_source sales -load_date $load_date
./0201_psa.ps1 -target $target -load_date $load_date
./0302_load.ps1 -target $target -load_date $load_date

$load_date = "2025-04-12"
./0102_import.ps1 -db_user $db_user -target $target -db_source sales -load_date $load_date
./0201_psa.ps1 -target $target -load_date $load_date
./0302_load.ps1 -target $target -load_date $load_date
```

## ToDos
* Describe the set-up of the database
* Describe how to set up a Docker container
* Describe how to set up a CI/CD pipeline

## Questions?
Contact me



 
