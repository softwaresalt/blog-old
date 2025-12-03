---
title: "When to Hack a DACPAC"
date: 2023-08-26
layout: single
categories:
  - Technical
tags:
  - SQL
  - Deployment
---

## When to Hack a DACPAC

## Scenario

As a data engineer, you want to migrate a database from an on-premise version of SQL Server to Azure SQL **including all source data**.

## Tool set

1. SQL Server Management Studio (SSMS)
1. Visual Studio (VS) / Azure Data Studio (ADS)
1. msbuild
1. SqlPackage
1. 7-Zip

## Assumptions

You have a Visual Studio / Azure Data Studio compatible SQL database project that includes all database entities and objects, excluding logins, permissions, and other server level objects.  You may already have this from an existing source control system or you may have reverse engineered the project from the database using VS or ADS.

You will extract a DACPAC of the source database with all data using the SqlPackage utility.

## Extraction

To extract the data along with the schema, the following command-line script example will produce  a DACPAC that includes all table data and ignores logins and permissions.

```bat
sqlpackage.exe /a:Extract /of:True /stsc:True /ssn:(local) /sdn:"doe-sso-sqldb-d-eu-ds-002" ^
/tf:"C:\Source\Projects\FLDOE\sql_deployment\doe-sso-sqldb-d-eu-ds-002.dacpac" /p:IgnorePermissions=True /p:ExtractAllTableData=True 
```

## Challenges

To deploy a database project into a target environment, you must first build the project with msbuild and then deploy it using SqlPackage.  VS/ADS can abstract away the the command-line arguments, but you lose some control and flexibility, so you script out the command-line for this to produce the output you want.

Sample Code:

```bat
msbuild "C:\Source\Projects\FLDOE\git\fldoe\apps\sql\migration\fldoesso.azsql.sqlproj" -t:Clean
msbuild "C:\Source\Projects\FLDOE\git\fldoe\apps\sql\migration\fldoesso.azsql.sqlproj" -p:Configuration=debug -clp:Summary
```

This will generate a .dacpac file in the .\bin\Output folder.
The signature for this DACPAC is that of the specific version of SQL Server from which the code was built.  If you open the .sqlproj file, you will see that the database schema provider is not configured for Azure SQL:

```xml
<DSP>Microsoft.Data.Tools.Schema.Sql.Sql150DatabaseSchemaProvider</DSP>
```

You want your database schema provider to be this:

```xml
<DSP>Microsoft.Data.Tools.Schema.Sql.SqlAzureV12DatabaseSchemaProvider</DSP>
```

Without this configuration, you cannot deploy this database project to an Azure SQL instance: your deployment will be rejected.

While switching the database schema provider for the project is straightforward, this would not include the source table data, which would not meet all migration requirements.  Further, the extracted DACPAC is using  an incompatible database schema provider, so deployment of this DACPAC will fail as-is.

## A Solution: Hack the DACPAC

### What is a DACPAC?

Much like most Office documents, a DACPAC is a Zip compressed file containing XML documents and binary data in the case of a DACPAC with table data included.  If you step into a DACPAC file using 7-Zip, you will see two directories and four XML files.

Directories:

- Data (contains folders by table name with schema, each containing 1+ .bcp files of table data in binary format)
- \_rels (contains ".rels" file in XML format indicating all table data files in the Data folder and relationships)

Files:

- DacMetdata.xml (Database name and version.)
- model.xml (A full representation of the database encapsulated in an XML document.)
- Origin.xml (Contains build type infomration about the source version of SQL, counts of each type of entity or object in the database schema, and a checksum of the model.xml file to detect tampering.)
- [Content_Types].xml

### What needs to be done?

We need to implement a version of the DACPAC that contains all data and model information from the originating on-premise SQL Server instance, but incorporates the Azure SQL database schema provider so that it can be successfully deployed to a target Azure SQL instance.

### How to do it

1. Open up the DACPAC file using 7-Zip
2. Right-click on the Origin.xml file and select Edit (this will open the file up in Notepad)
3. Find the ServerVersion node in the XML contents.  It will look something like this:

    ```xml
    <ServerVersion>Microsoft SQL Server 2019 (RTM-GDR) (KB5021125) - 15.0.2101.7 (X64) 
      Jan 23 2023 13:08:05 
      Copyright (C) 2019 Microsoft Corporation
      Developer Edition (64-bit) on Windows 10 Enterprise 10.0 &lt;X64&gt; (Build 22621: ) (Hypervisor)
    </ServerVersion>
    ```

4. Replace that node text with the following:

    ```xml
    <ServerVersion>Microsoft SQL Azure (RTM) - 12.0.2000.8 
      Mar  8 2023 17:58:50 
      Copyright (C) 2022 Microsoft Corporation
    </ServerVersion>
    ```

5. Close the Notepad window and click Save when prompted.  This will save your changes to the Origin.xml file inside the DACPAC.

Your DACPAC is now ready for deployment to an Azure SQL instance!

### What if the Azure SQL server version information changes?

To capture the server version information from your target Azure SQL Instance, extract a bare-bones DACPAC from the target Azure SQL instance, open it up in 7-Zip, and copy the ServerVersion node from the Origin.xml file.
