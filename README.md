# Azure Database Migration project
![Azure](https://img.shields.io/badge/azure-%230072C6.svg?style=for-the-badge&logo=microsoftazure&logoColor=white)![MicrosoftSQLServer](https://img.shields.io/badge/Microsoft%20SQL%20Server-CC2927?style=for-the-badge&logo=microsoft%20sql%20server&logoColor=white)![GitHub](https://img.shields.io/badge/github-%23121011.svg?style=for-the-badge&logo=github&logoColor=white)![Windows 11](https://img.shields.io/badge/Windows%2011-%230079d5.svg?style=for-the-badge&logo=Windows%2011&logoColor=white)

TODO: Project description


## Table of contents


## Production Environment Setup

**VM Acquisition**: Obtained a Virtual Machine named `adm-prod-vm`.

**Specs**: 
- **OS**: Windows 11 Pro 2h22
- **Size**: Standard_B2ms

**Software Installation**:
  - Installed Microsoft SQL Server.
  - Installed SQL Server Management Studio (SMSS).

**Database Restoration**:
  - Restored AdventureWorks2022 database from a backup file.

![Object explorer displaying the AdventureWorks2022 database](assets/2.1-OE-after-backup.png)

## Migrate to Azure SQL Database

**Azure Database Creation**: 
  - Created database `AdventureWorks2022` on `aw2022.database.windows.net`.
**Networking Setup**: 
  - Configured firewall rule in Azure (aw2022 > Networking) to allow local machine connection.
**Initial Connection**:
  - Connected to the Azure database using Visual Studio Code.

![Visual Studio Code displaying connection to Azure SQL Database](assets/3.1-VSC-SQLconnection.png)

**Tool Installation and Connection**:
  - Downloaded and installed Azure Data Studio.
  - Established connections to both local and Azure databases.
  
**Schema Migration**:
  - Migrated schema from the local SQL database to Azure using SQL Server Schema Compare in Azure Data Studio.

![Azure SQL Database schema migration](assets/3.2-Azure-schema-migration.png)

**Data Migration**:
  - Set up a Database Migration service in Azure and registered an Integration Runtime.
  - Migrated data to Azure database using Azure SQL Migration in Azure Data Studio. Migration confirmed successful.

![Azure SQL Database data migration](assets/3.3-Azure-data-migration.png)
![Azure SQL Database data migration query result](assets/3.4-Azure-data-migration-query.png)

The database has been migrated to Azure!