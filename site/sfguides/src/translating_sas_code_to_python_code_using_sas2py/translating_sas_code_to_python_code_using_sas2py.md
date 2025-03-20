author: Inspark Analytics
id: translating_sas_code_to_python_code_using_sas2py
summary: This guide teaches you how to translate SAS source code to Python source code using SAS2PY via Snowflake
categories: Getting-Started, Partner-Integration
environments: web
status: Published 
feedback link: https://github.com/Snowflake-Labs/sfguides/issues
tags: Getting Started, SAS, Python

# Translating SAS code to Python code using SAS2PY

## Overview

In this Snowflake Quickstart, we will be introducing you to the basics of operating Snowflake in conjunction with SAS-Translator. 

SAS-Translator is a built-in translation tool in Snowflake that allows users to translate SAS source code into Python, where data can be migrated to the Snowflake database in tables, the newly generated Python code can then run against the migrated data. SAS-Translator’s goal is to shift away from the general usage of SAS into Snowflake, a more modern standpoint in data analytics.

### What you will build

A pipeline for migrating SAS source code and data files to Snowflake tables via SAS-Translator’s migration worksheet, executing SAS-to-Python translations via SAS-Translator’s translation worksheet, and then applying the newly generated Python code against the migrated data via Snowflake Notebooks.

### What you will learn

We will be going through the following which will get you familiarized with…

- Connecting to applications, such as SAS-Translator, using Snowflake’s Partner Connect (tentative, unsure if this is the exact way)
- Creating Snowflake warehouses, databases, tables, worksheets, notebooks, views, etc.
- Data migration onto Snowflake using SAS-Translator
- Translating SAS source code to Python source code using SAS-Translator
- Executing worksheets and notebooks

### Prerequisites

- A Snowflake Account (?)

## Getting Started

To get started, we must first login into Snowflake and get familiar with it’s interface, then connect to our app, SAS-Translator, through the Partner connect

[NOTES]
- Involves Logging into snowflake and familiarizing with the basic homescreen layout (?)
- how to connect with our app, SAS2PY

### Familiarizing with Snowflake

this includes login, and basic homescreen interface

### Partner Connecting to SAS2PY

this includes using the Partner Connect, 

## Snowflake Environment Setup

Now that you are more familiar with the layout of Snowflake and are connected to SAS-Translator, we can start setting up the Snowflake Environment. This will be the backbone of how Snowflake operates and how you connect to the Snowflake servers and databases. We will be going over how to create warehouses, databases, schemas, and as well as uploading files to Snowflake.

> aside negative
>
> **Disclaimer** <br> As we go through this step, we will be organizing/formatting these entities in a specific way and also using specific naming conventions. As long as the references are correct, you are freely able to customize it, but for the sake of this quickstart, please follow our format.

[Notes]
- Involves creating warehouse, database, schema, and snowflake stages, and uploading files

### Creating a Warehouse

The Warehouse are essential Snowflake servers that are used computation operations. This encapsulates all the data processing, query running, and any data loading task that takes place. The size of the computation can be scaled up or down depending on the workload itself.

used to run on the snowflake servers

> aside negative
>
> **Disclaimer** <br> Operating/running warehouses run on a credit system. This roughly translates from the size of the warehouses in comparison to runtime (usually charged per hour), which means larger warehouses will charge more than smaller warehouses over the same runtime. Warehouses that are turned on have an inactivity timer (which is customizable but fact check this), shutting down the warehouse due to inactivity and saving credits. Credits are a unit of measurement to quantify the consumption of compute resources in a warehouse, which you buy with tangible money. When making a free tutorial snowflake account, you are given some free credits (fact check this please) as a freebie to test out the warehouses. To learn more about credits, please direct yourself to the about credits page (reference properly here).

#### Steps

Under the main screen (or Home), click Create Warehouse
[INSERT IMAGE HERE]

After clicking the Create Warehouse button, there will be a pop-up window for customizing. For this quickstart, please set the following parameters…
- Warehouse Name: WH1
- Type: Standard
- Size: X-Small
[INSERT IMAGE HERE]

> aside negative
>
> **Disclaimer** <br> Warehouses uses credits, which in turn are bought with real money. Stated in the bottom left, we can see that 1 credit is used per total hour of usage this specific warehouse uses. Larger warehouses in Size, the more credits per hour. If you wish to learn more about the credit consumption, you can click on the “1 credit/hour” in the bottom left, and as of writing this, the legal documents for credit consumption will pop up in a new tab.

After clicking Create Warehouse, a pop-up window should appear stating you’ve successfully created a warehouse, you can now press Done to proceed.

### Creating a Database

The Database is essentially a container for organizing and storing data. This where all the data will be held.

#### Steps

On the left side of the screen, click Data, which should default to Databases (if not please select Databases). On the top right there will be blue button that says +Databases.
- After clicking create, you will be prompted to give a name (and an optional comment), please name it “DB1” and create the database. DB1 should then appear on the list of databases you have access to.
[INSERT IMAGE HERE]

just a database, holds everything

### Creating Schemas

The Schemas are used in data warehousing, generally used for the organization of hierarchal files. For this quickstart, this is where the source code and data files will be placed.

#### Steps

Select the Database DB1, to which you the dropdown of DB1 will display INFORMATION_SCHEMA and PUBLIC, as well as showcase the database details in the middle of the screen. To create a Schema, press the top right button that says +Schema, to which a pop-up window will appear.
- After clicking create, please name it “SM1”, to which after creating it, it will show up under DB1 alongside INFORMATION_SCHEMA and PUBLIC in the drop down of DB1.
[INSERT IMAGE HERE]

used by the warehouse 

### Creating Snowflake Stages

Snow Stages are essentially where data files is stored for both onloading and offloading data. For this quickstart, we will be creating two stages, one to hold the SAS source code (ST_Code), and one to hold the data file (ST_Data) (can be done with one stage, but we’ve seperated them into two for visibility and clarity).

#### Steps

Select Schema SM1 under Database DB1 (after creating a Stage, the Stage will be selected not Schema, so please be sure to reselect Schema when creating the second Stage), to which yet another dropdown will appear underneath SM1 stating there are no objects are found. To create Stages here, click on the top right button that says Create, to which a dropdown will appear. Hovering over Stage will show a few options, but we want to select Snowflake Managed.
- Under the Stage section, you can see two sections, Snowflake Managed being Internally Managed while Amazon S3, Microsoft Azure, and Google Cloud Platform being Externally Managed. The difference between the two is just where you prefer your files stored in, and with this quickstart, we’ve provided data files used to use Internally.
[INSERT IMAGE HERE]

When selecting Snowflake Managed Stage, the pop-up window for Create Stage will appear. We want to create two stages, one named ST_Code and ST_Data, but the following other parameters will be the same…
- Schema : DB1.SM1
- Directory Table should be enabled
- Client-side encryption should be selected
[INSERT IMAGE HERE]

After creating both stages, your database hierarchy should look like this…
[INSERT IMAGE HERE]

### How to Upload Files

The final step to this environment setup is to upload the files that will be used for this quickstart. Here we provided a simple SAS source file (to be put into ST_Code) as well as a data file (to be put into ST_Data) that it manipulates.

#### Steps

Select either Stage ST_Code or ST_Data and click the top right button that says +Files. A pop-up window should appear, which will allow you to upload your files. Please upload the appropriate file to the selected Stage (ST_Code for the SAS source code, and ST_Data for the data file)
- If the Database and Schema are is not DB1.SM1 by default, please change it accordingly, and then upload the respective file
[INSERT IMAGE HERE]

After uploading the files, both Stages should look like this...
[INSERT IMAGE HERE]

## Preparing Migration of SAS Data for Snowflake

[Notes] Subject to change based on what the data migration worksheet looks like

With the Snowflake environment setup complete, we can finally start with utilizing SAS-Translator. The first step to utilizing SAS-Translator is to upload all data files onto Snowflake (done in the environment setup) then migrating from their original file type into Snowflake Tables. Since this is Snowflakes primary way of data storage, migrating to their customs makes the workflow more seamless. The way we will migrate data will be through the migration tool provided by SAS-Translator, which is essentially just a Worksheet script.

### Steps

The first step is to navigate to the Migration Job; On the left sidebar under Projects and Worksheets, you will find a worksheet named Migration Job (subject to change), upon clicking it you will have opened the worksheets interface.
[INSERT IMAGE HERE]

Once inside the Migration Job, there are some key things to keep in mind. For this quickstart, we’ve pre-set most of the required information, if the naming conventions of the setup were followed, but we will go over what would need to be changed for given other naming conventions…
- To note, even if the schema or warehouse used are different (see top right uses a different warehouse, and to the left of Settings right above the code defaults to PUBLIC schema), the beginning of the script will adjust accordingly.

#### Data Migration Job

The screenshot used will use square brackets as well as generic names to describe what it should be replaced with, please remove the square brackets when inputting the correct information

[INSERT IMAGE HERE]

- [role_name] = name of current role
- [warehouse_name] = WH1
- [database_name] = DB1
- [schema_name] = SM1
- [stage_data_name] = ST_Data
- [data_name_file] = hotel (subject to change)
- [desired_table_name] = hotel (subject to change)

After filling in the information, press the RIGHT side of the blue button on the top right (the arrow down/drop down icon), and then select Run All. After it’s finished executing, you will have successfully migrated the data into a Snowflake Table. To check, you can click the icon below the worksheets icon (on the left side bar, the icon below the blue highlighted icon) and go to Databases. The tables will be saved under DB1 → PUBLIC → Tables.
[INSERT IMAGE HERE]

> aside positive
>
> **Notes** <br> Migration can be done in bulk (instead of one at a time), the last 4 uncommented lines can be copied and edited to any number of files you wish to be migrated as long as the references are correct.

[Notes]
- How to run the Migration job, which converts data (.sas7bdat files), to Snowflake Tables

## Translating SAS to Python

[Notes] Subject to change based on what the translation worksheet looks like

Now that all files are in Snowflake Tables, we can move onto the Translation Job, the main portion of SAS-Translator. This reads in SAS source code and outputs it into Python (one of the three languages supported by Snowflake), which can be run on a worksheet (more on that later). After the Translation Job is complete, the Python code will be shown under the App.

#### Translation Job

Since from the previous step, you were working with the Migration Worksheet, we can immediately open the Translation Worksheet listed on the left column. Similar to the Migration Job, we have left key variable names to familiarize yourself with so that you are able to run the Translation Job on any SAS source code file.

[INSERT IMAGE HERE]

- [role_name] = name of current role
- [warehouse_name] = WH1
- [database_name] = DB1
- [schema_name] = SM1
- [stage_code_name] = ST_Code
- [sas_file_name] = hotel (subject to change)

After filling in the information, press the RIGHT side of the blue button on the top right (the arrow down/drop down icon), and then select Run All. After it’s finished executing, you will have successfully translated the selected SAS code into Python.

[Notes]
- How to run the Translation job, which just converts the SAS source code into a new file that's written in Python
- Then show's how to run the newly created Python code in a worksheet

## Executing Python Code with Notebooks

After the Translation Job, the new Python translated code will be found under the App. This conversion is one-to-one from SAS to Python, so any changes made here on and afterwards could invalidate the integrity of the translation.

### Steps

#### Locating the Python Code

Navigate your back back to the home page so clearly see the tabs on the left side bar, and click on Data Products, and then under it we want to go to Apps. You should see the SAS-Translator App as one of the installed Apps, and clicking on it will bring you to the dashboard. (fact check that it should be here after)
[INSERT IMAGE HERE]

If a warehouse is not selected located on the top right side, pick the warehouse WH1 that we made, and select the Output option in the App Views dropdown box on the left side.
[INSERT IMAGE HERE]

The code shown here  is the newly generated SAS to Python source code. To compile and execute this newly created Python code, we must navigate ourselves to the Notebooks page, so you will need to copy the entire python code via the button or selecting all the text and copying it before going back to the home page via the back arrow on the top left.
[INSERT IMAGE HERE]

#### Running Code with Notebooks

After locating the Python Code, we can now execute code with the newly migrated data all on Snowflake. We will be using Notebooks, which is able to query and execute code from the tables.

After backing out of the App dashboard, we can go to the Projects tab and select Notebooks underneath it. We will need a fresh notebook, and by clicking the top right blue button +Create (not the dropdown arrow), we can create a Notebook specifically for this SAS source code.
- The name here doesn’t matter too much since this is the last step, for visibility and clarity sake the most idealist naming convention is to name it the same as the SAS source code because it has the same functions just in Python.
- Select Database DB1 with the schema PUBLIC (should be public), as well as choosing the Warehouse WH1.
[INSERT IMAGE HERE]

When you first open a newly created Notebook, there will be three example code cells describing about the chance of snow between friday to sunday (as of writing this quickstart). Although the first cell is somewhat similar, we want to delete all of these and start fresh.

To delete the code cells, when hovering over a code cell, there will be triple downward dots on the top right side, select delete for all three examples.
[INSERT IMAGE HERE]

At the bottom middle of the screen (moving upwards since you have been deleting cells), you will see three options, Python, SQL, and Markdown. Please create two Python cells, one will be used for the code itself, and the other is the driver code.
[INSERT IMAGE HERE]

> aside positive
>
> **Python Cell** <br> In this cell, please paste the Python code provided previously in the translated output. The Python code is the first cell because it will be generating and executing all the given transformations onto the tables firsthand.
> [INSERT IMAGE HERE]

> aside positive
>
> **Driver Cell** <br> In this cell, we have given you code to make this the Driver Cell. The main function of the driver cell is to display the table, and to trigger an evaluation of the table, which executes all previous transformations.
> [INSERT IMAGE HERE]

When both cells have the correct code and are ordered in their respective places, click Run All on the top right.
[INSERT IMAGE HERE] i guess

After both cells have finished executing, we can see in the Driver Cell a table displaying the current table, post transformations, and the conclusion to this tutorial.
[INSERT IMAGE HERE]

## Conclusion and Resources

### Conclusion

Throughout this quickstart, we were able to successfully set up the environment for general Snowflake usage (and learned how to for other potential uses). Utilizing SAS-Translator, we migrated all data files into Snowflake Tables, as well as shifting away from SAS into Python, which we then ran the newly translated code in Notebooks, resulting in the altercation of the corresponding Snowflake Table.

### What You Learned

- The Snowflake Interface
- How to use the Partner Connect
- Creating Snowflake objects such as warehouses, databases, schemas, snowflake stages, etc.
- Migrating SAS source code and data files into Snowflake Tables
- Executing the translation job of SAS-Translator to translate SAS source code into Python
- Utilizing Notebooks and running Python code to reflect changes onto Snowflake Tables

### Resources

- Link to Snowflake documents (?)
- Link to Inspark Analytics SAS-Translator confluence (?)