# Azure Database Migration project
![Azure](https://img.shields.io/badge/azure-%230072C6.svg?style=for-the-badge&logo=microsoftazure&logoColor=white)![MicrosoftSQLServer](https://img.shields.io/badge/Microsoft%20SQL%20Server-CC2927?style=for-the-badge&logo=microsoft%20sql%20server&logoColor=white)![GitHub](https://img.shields.io/badge/github-%23121011.svg?style=for-the-badge&logo=github&logoColor=white)![Windows 11](https://img.shields.io/badge/Windows%2011-%230079d5.svg?style=for-the-badge&logo=Windows%2011&logoColor=white)

TODO: Project description


## Table of contents


## Production Environment Setup
In this section, we outline the initial setup of our production environment. This involves acquiring a new virtual machine, detailing its specifications, and installing essential software including Microsoft SQL Server and SQL Server Management Studio (SMSS). Additionally, we discuss the process of restoring the AdventureWorks2022 database, setting the stage for further migration activities.

<details>
  <summary>Open Logbook</summary>
  <br/><br/>

**VM Acquisition**: Obtained a Virtual Machine named `production-vm`.

**Specs**: 
- **OS**: Windows 11 Pro 2h22
- **Size**: Standard_B2ms

**Software Installation**:
  - Installed Microsoft SQL Server.
  - Installed SQL Server Management Studio (SMSS).

**Database Restoration**:
  - Restored AdventureWorks2022 database from a backup file.

![Object explorer displaying the AdventureWorks2022 database](assets/2.1-OE-after-backup.png)
*Figure 2.1: Object explorer displaying the AdventureWorks2022 database*

</details>

## Migrate to Azure SQL Database
Here, we delve into the core process of migrating our database to Azure SQL Database. We start by creating a new database in Azure and configuring the necessary networking settings. This is followed by establishing our initial connection using Visual Studio Code and proceeding with the migration. Key steps include installing Azure Data Studio, connecting to both local and Azure databases, and successfully migrating the schema and data to Azure.
<details>
  <summary>Open Logbook</summary>
  <br/><br/>

**Azure Database Creation**: 
  - Created database `aw-database` on `aw-production-server.database.windows.net`.
**Networking Setup**: 
  - Configured firewall rule in Azure (aw-production-server > Networking) to allow local machine connection.
**Initial Connection**:
  - Connected to the Azure database using Visual Studio Code.

![Visual Studio Code displaying connection to Azure SQL Database](assets/3.1-VSC-SQLconnection.png)
*Figure 3.1: Visual Studio Code displaying connection to Azure SQL Database*

**Tool Installation and Connection**:
  - Downloaded and installed Azure Data Studio.
  - Established connections to both local and Azure databases.
  
**Schema Migration**:
  - Migrated schema from the local SQL database to Azure using SQL Server Schema Compare in Azure Data Studio.

![Azure SQL Database schema migration](assets/3.2-Azure-schema-migration.png)
*Figure 3.2: Showing successful schema migration*

**Data Migration**:
  - Set up a Database Migration service in Azure and registered an Integration Runtime.
  - Migrated data to Azure database using Azure SQL Migration in Azure Data Studio. Migration confirmed successful.


![Azure SQL Database data migration](assets/3.3-Azure-data-migration.png)
*Figure 3.3: Showing successful data migration*


![Azure SQL Database data migration query result](assets/3.4-Azure-data-migration-query.png)
*Figure 3.4: Query made on Azure SQL database confirming migration*

The database has been migrated to Azure!
</details>

## Data Backup and Restore
This section focuses on the crucial tasks of data backup and restoration. Initially, we create a full backup of our production database to ensure data safety. Then, we set up an Azure storage account for backup file storage and upload the backup file to it. The section also covers the creation of a development VM for testing, the restoration of the AdventureWorks database on this VM, and the configuration of automated backups using SQL Server Agent and Maintenance Plans in SMSS.

<details>
  <summary>Open Logbook</summary>
  <br/><br/>

**Backup Creation**: Generated full backup of `production-vm` database.

*Figure 4.1: On-premise database backup*  
![Successful backup of on-premise database](assets/4.1-successful-backup.png)

**Azure Storage Setup**: Configured `awproductionstorage` for backup storage.

**Backup Upload**: Uploaded backup file to `onpremisebackup` container.

*Figure 4.2: `onpremisebackup` storage container contents*  
![Blob storage of storage container](assets/4.2-onpremicebackup.png)

**Development VM Setup**: Created `development-vm` for testing.

**Specs**: 
- **OS**: Windows 11 Pro 2h22
- **Size**: Standard_B2ms

**Software Installation**:
  - Installed Microsoft SQL Server.
  - Installed SQL Server Management Studio (SMSS).

**Software Installation**: Installed SQL Server and SMSS.

**Database Restoration**: Restored AdventureWorks database on `development-vm`.

*Figure 4.3: Restored database on development VM*  
![Backup of database on development vm](assets/4.3-dev-backup.png)

**Automated Backup Configuration**:
  1. Activated SQL Server Agent.
  2. Created SQL Server Credentials for Azure storage.
  3. Verified credentials in SMSS.
  
*Figure 4.4: Azure Storage credentials in SMSS*  
![Showing Azure Storage credentials](assets/4.4-creds.png)

  4. Configured `Scheduled Backup` via Maintenance Plan Wizard.
  5. Set backup schedule: Weekly on Saturdays at 12:00 AM.
  6. Directed backups to Azure storage container.

*Figure 4.5: Backup destination configuration*  
![Showing database backup destination](assets/4.5-backup-destination.png)

  7. Validated backup by manual execution and Azure storage check.

*Figure 4.6: Backup validation*  
![Showing both storage container with backup (left) and the successful execution of maintenance plan (right)](assets/4.6-scheduled-backup-working.png)

</details>