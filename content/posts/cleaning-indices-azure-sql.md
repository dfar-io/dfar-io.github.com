---
title: "Cleaning Indices in Azure SQL"
date: 2022-06-20T13:29:01Z
draft: true
---

If you're finding issues with performance in Azure SQL, you may want to check for index fragmentation and rebuild the indices to improve performance. This can also be done on a regular schedule.

## Considerations

First, determine whether you need to reorganize or rebuild:

1. Reorganize - organizes the index, leaves the DB online, can be resumed. Generallly preferred.
2. Rebuild - rebuilds the index fully, can be quicker, runs as an offline operation (locks the DB).

## Initial Cleanup

The general workflow for rebuilding indices

To rebuild all indexes for a table based on a fragmentation threshold:

``` sql
-- Provide list of fragmented indices

SELECT
    OBJECT_NAME(ps.object_id) AS TableName,
    i.name AS IndexName,
    ips.avg_fragmentation_in_percent
FROM
    sys.dm_db_partition_stats ps
INNER JOIN
    sys.indexes i
ON ps.object_id = i.object_id
AND ps.index_id = i.index_id
CROSS APPLY sys.dm_db_index_physical_stats(DB_ID(), ps.object_id, ps.index_id, null, 'LIMITED') ips
WHERE ips.avg_fragmentation_in_percent > 0 -- change this number to change threshold for running reorganize/rebuild
ORDER BY ips.avg_fragmentation_in_percent DESC
```

Then to reorganize the index (to rebuild, just replace `REORGANIZE` with `REBUILD`):

``` sql
-- Reorganize
ALTER INDEX <INDEX_NAME> ON dbo.<TABLE_NAME> REORGANIZE;
```

You may want to scale the database up temporarily while cleaning indices, as it can take quite a while.

## Automatic

1. Create Elastic Job Agent in Azure.
    1. Can be done with Terraform: https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/mssql_job_agent