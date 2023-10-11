z---
title: "Copy Azure SQL Database to GCP Cloud SQL"
date: 2023-10-11T18:43:08Z
draft: false
---

## Pre-reqs

* Azure Account with both:
  * Azure SQL and Storage Account
  * Azure SQL needs to allow Azure to access resource.
* GCP Account with:
  * Cloud SQL (needs contained database authentication flag)
* SSMS
* SQL Server Developer Edition (match version used in Cloud SQL)
  * Needs `sp_configure` for contained database authentication.

## Process

1. (Linux) In Azure, scale up the DB to max DTUs to speed export process.
2. Export .bacpac to Azure Storage Account.
3. After export completion, scale DB back down to previous DTUs.
4. (Windows) Download .bacpac using Azure Storage Explorer.
5. Connect to local SQL via SSMS.
6. Import Data-tier Application
7. Create .bak (Database -> Tasks -> Backup)
8. Upload .bak to storage account. (Program Files/Microsoft SQL Server/**)
9. Delete .bacpac with Azure Storage Explorer
10. (Linux) Create SAS via Portal
11. Create and run STS job to copy file.
12. Import .bak from GCS bucket to GCP Cloud SQL.

## Troubleshooting

* Low Disk Space - use WinDirStat, [clear out WInSxS folder](https://helpdeskgeek.com/windows-11/what-is-the-winsxs-folder-why-is-it-huge-and-how-to-cleanup/)
