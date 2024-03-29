# Azure Database Migration Project
![Azure](https://img.shields.io/badge/azure-%230072C6.svg?style=for-the-badge&logo=microsoftazure&logoColor=white) ![MicrosoftSQLServer](https://img.shields.io/badge/Microsoft%20SQL%20Server-CC2927?style=for-the-badge&logo=microsoft%20sql%20server&logoColor=white) ![GitHub](https://img.shields.io/badge/github-%23121011.svg?style=for-the-badge&logo=github&logoColor=white) ![Windows 11](https://img.shields.io/badge/Windows%2011-%230079d5.svg?style=for-the-badge&logo=Windows%2011&logoColor=white)

## Project Description
This project encompasses the migration of an on-premises SQL Server database to Azure SQL Database. It involves setting up a production environment, migrating schema and data to Azure, implementing data backup and restore strategies, conducting disaster recovery simulations, configuring geo-replication and failover, and integrating Microsoft Entra Directory. The project aims to leverage Azure's cloud capabilities for enhanced data management, security, and scalability.

## Table of Contents
- [Project Description](#project-description)
- [Production Environment Setup](#production-environment-setup)
- [Migrate to Azure SQL Database](#migrate-to-azure-sql-database)
- [Data Backup and Restore](#data-backup-and-restore)
- [Disaster Recovery Simulation](#disaster-recovery-simulation)
- [Geo-replication and Failover](#geo-replication-and-failover)
- [Microsoft Entra Directory Integration](#microsoft-entra-directory-integration)
- [Project Accomplishments](#project-accomplishments)


## Production Environment Setup
In this section, we outline the initial setup of our production environment. This involves acquiring a new virtual machine, detailing its specifications, and installing essential software including Microsoft SQL Server and SQL Server Management Studio (SMSS). Additionally, we discuss the process of restoring the AdventureWorks2022 database, setting the stage for further migration activities.

<details>
  <summary>Open Logbook</summary>

#### Virtual Machine (VM) Setup

**VM Acquisition**: Obtained a Virtual Machine named `production-vm`.

**Specs**: 
- **OS**: Windows 11 Pro 2h22
- **Size**: Standard_B2ms

**Software Installation**:
  - Installed Microsoft SQL Server.
  - Installed SQL Server Management Studio (SMSS).

**Database Restoration**:
  - Restored AdventureWorks2022 database from a backup file.

*Figure 2.1: Object explorer displaying the AdventureWorks2022 database*
![Object explorer displaying the AdventureWorks2022 database](assets/2.1-OE-after-backup.png)

</details>

## Migrate to Azure SQL Database
Here, we delve into the core process of migrating our database to Azure SQL Database. We start by creating a new database in Azure and configuring the necessary networking settings. This is followed by establishing our initial connection using Visual Studio Code and proceeding with the migration. Key steps include installing Azure Data Studio, connecting to both local and Azure databases, and successfully migrating the schema and data to Azure.
<details>
  <summary>Open Logbook</summary>

### Azure SQL Database Server and Database setup

**Azure Database Creation**: 
  - Created database `aw-database` on `aw-production-server.database.windows.net`.
**Networking Setup**: 
  - Configured firewall rule in Azure (aw-production-server > Networking) to allow local machine connection.
**Initial Connection**:
  - Connected to the Azure database using Visual Studio Code.

*Figure 3.1: Visual Studio Code displaying connection to Azure SQL Database*
![Visual Studio Code displaying connection to Azure SQL Database](assets/3.1-VSC-SQLconnection.png)

**Tool Installation and Connection**:
  - Downloaded and installed Azure Data Studio.
  - Established connections to both local and Azure databases.
  
### Migration to SQL Database

**Schema Migration**:
  - Migrated schema from the local SQL database to Azure using SQL Server Schema Compare in Azure Data Studio.

*Figure 3.2: Showing successful schema migration*
![Azure SQL Database schema migration](assets/3.2-Azure-schema-migration.png)

**Data Migration**:
  - Set up a Database Migration service in Azure and registered an Integration Runtime.
  - Migrated data to Azure database using Azure SQL Migration in Azure Data Studio. Migration confirmed successful.


*Figure 3.3: Showing successful data migration*
![Azure SQL Database data migration](assets/3.3-Azure-data-migration.png)


*Figure 3.4: Query made on Azure SQL database confirming migration*
![Azure SQL Database data migration query result](assets/3.4-Azure-data-migration-query.png)

The database has been migrated to Azure!
</details>

## Data Backup and Restore
This section focuses on the crucial tasks of data backup and restoration. Initially, we create a full backup of our production database to ensure data safety. Then, we set up an Azure storage account for backup file storage and upload the backup file to it. The section also covers the creation of a development VM for testing, the restoration of the AdventureWorks database on this VM, and the configuration of automated backups using SQL Server Agent and Maintenance Plans in SMSS.

<details>
  <summary>Open Logbook</summary>

### Backup Creation

**Backup Creation**: Generated full backup of `production-vm` database.

*Figure 4.1: On-premise database backup*  
![Successful backup of on-premise database](assets/4.1-successful-backup.png)

**Azure Storage Setup**: Configured `awproductionstorage` for backup storage.

**Backup Upload**: Uploaded backup file to `onpremisebackup` container.

*Figure 4.2: `onpremisebackup` storage container contents*  
![Blob storage of storage container](assets/4.2-onpremicebackup.png)

### Development VM Creation and Setup

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

### SSMS Automated Backup

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

## Disaster Recovery Simulation

In this section, we will conduct a disaster recovery simulation to test our preparedness for data loss or corruption in the Azure SQL database. The process involves intentionally creating scenarios that mimic real-world data disasters, such as deleting critical data and inducing data corruption. This simulation will not only test our ability to recover from such incidents but also evaluate the effectiveness of our backup and restore strategies under pressure.

<details>
  <summary>Open Logbook</summary>

### Simulating data loss and corruption

To simulate data loss, I plan to delete all entries in the `Production.ProductInventory` table and the `Purchasing.Vendor` table. To mimic data corruption, I will modify the `Purchasing.Vendor` table by setting all `AccountNumbers` to NULL. This will be done by executing the following queries:


```
DELETE FROM Production.ProductInventory;;
```

```
UPDATE Purchasing.Vendor
SET Name = 'ABCDEF';
```
*Figure 5.1: Purchasing.vendor table before (left) and after (right) data corruption*
![Purchasing.vendor table before (left) and after (right) data corruption](assets/5.1-purchasingvendor.png)

*Figure 5.2: Production.ProductInventory table before (left) and after (right) data loss*
![Production.ProductInventory table before (left) and after (right) data loss](assets/5.2-productInventory.png)

### Restoring Database
To restore the database I went onto the Azure portal, and into the `aw-database` and select Restore.

From here I select the restore point to midnight the same day, before the deletion and corruption occurred and set the databases name to `aw-database-restored`.

To then check these I ran a `SELECT * FROM table` query.

*Figure 5.3: Production.ProductInventory table after restoration*
![Production.ProductInventory table after restoration](assets/5.3-productInventory.png)

*Figure 5.4: Purchasing.vendor table after restoration*
![Purchasing.vendor table after restoration](assets/5.4-purchasingvendor.png)

</details>

## Geo-replication and Failover

In this section, we focus on enhancing the resilience and data protection of our production database through geo-replication. By configuring a synchronized copy of the Azure SQL database in a secondary region, we ensure continuous availability and disaster recovery capabilities. The process involves setting up a geo-replica in a different location and testing failover mechanisms to ensure seamless transition between primary and secondary servers in case of an outage or disaster.

<details>
  <summary>Open Logbook</summary>

### Configuring Geo-replication

I started by navigating to the `aw-database` in the Azure portal and selecting `Replicas` under Data Management. Here, I created a replica within the `aw-production-backup` SQL Database Server located in the East US region.

*Figure 6.1: New SQL Database Server in East US location*  
![New SQL Database Server within East US location](assets/6.1-new-database-server.png)

I then completed the form to establish the geo-replica of the original `aw-database`.

*Figure 6.2: New SQL Database in East US location*  
![New SQL Database within East US location created](assets/6.2-geo-replica.png)

Next, I set up the failover configuration between the primary server `aw-production-server (US South)` and the secondary server `aw-production-backup (East US)`.

*Figure 6.3: Failover group configuration*  
![Failover group](assets/6.3-failover.png)

To test the setup, I initiated a Forced Failover, causing the primary and secondary servers to switch roles.

*Figure 6.4: Successful forced failover*  
![Failover group after forced failover](assets/6.4-forced-failover.png)

Finally, I conducted the forced failover once again to revert the servers back to their original configuration.

</details>

## Microsoft Entra Directory Integration

In this section, we will integrate Microsoft Entra Directory with our Azure SQL Database setup. This process includes configuring Microsoft Entra ID for Azure SQL Database, setting up administrative and read-only users, and testing their permissions. The goal is to demonstrate how Microsoft Entra Directory can be used to manage user access and permissions in a secure and efficient manner.

<details>
  <summary>Open Logbook</summary>

### Configure Microsoft Entra ID for Azure SQL Database

#### Setting up Admin user

I began by navigating to the `aw-production-server` in the Azure Portal. Under the settings, I accessed `Microsoft Entra ID` to assign an Entra user as the admin for the SQL Server.

*Figure 7.1: Microsoft Entra Admin setup*  
![Entra ID Admin](assets/7.1-entra-admin.png)

After setting up the admin, I tested the login to the Azure database using the Microsoft Entra ID, which was successful.

#### Setting up DB Reader User

Next, I added a new user named `aw-DB-Datareader` in the Microsoft Entra ID Service through the Azure Portal.

*Figure 7.2: AW-DB-Reader user in Azure Portal*  
![Showing the AW-DB-Reader user](assets/7.2-db-reader.png)

I then used Azure Data Studio to execute the following SQL commands to create the user and assign them to the db_datareader role:

```
CREATE USER [aw-DB-Datareader@aicoreusers.onmicrosoft.com] FROM EXTERNAL PROVIDER;
ALTER ROLE db_datareader ADD MEMBER [aw-DB-Datareader@aicoreusers.onmicrosoft.com];
```

#### Testing the DB Reader User

To test the newly created user, I executed a `SELECT` query to confirm that the user can read data:

```
SELECT * FROM Person.Address;
```

*Figure 7.3: Database serving read-only user with data*  
![Shows the database serving read-only user with data](assets/7.3-read-db.png)

Finally, I attempted a `DELETE` operation to ensure the user is restricted to read-only access:

```
DELETE TOP (1)
FROM Person.Address;
```

*Figure 7.4: Database restricting read-only user from altering data*  
![Shows the database not allowing read-only user to alter database](assets/7.4-read-only.png)

</details>

## Project Accomplishments

We have successfully completed a comprehensive migration of our database to Azure SQL, ensuring enhanced data security, availability, and scalability. Key achievements include setting up a robust production environment, seamless migration of data and schema to Azure, establishing reliable backup and restoration processes, simulating and overcoming disaster recovery scenarios, configuring geo-replication for data redundancy, and integrating Microsoft Entra Directory for improved access control. This project marks a significant milestone in leveraging cloud technology for advanced database management.