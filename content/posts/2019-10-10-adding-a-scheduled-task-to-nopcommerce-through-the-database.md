---
title: Adding a Scheduled Task to NOPCommerce through the database
author: dfar

date: 2019-10-10T14:04:31+00:00
url: /adding-a-scheduled-task-to-nopcommerce-through-the-database/
categories:
  - Uncategorized

---
When working in NOPCommerce, you may want to create a scheduled task without having to go through plugin install and uninstall.  


First, you&#8217;ll have to have a task set up in ideally a plugin.

You can do so by adding the following to the `ScheduleTask` table in the NOPCommerce database:

<pre class="wp-block-code"><code>INSERT INTO [dbo].[ScheduleTask]
           ([Name]
           ,[Seconds]
           ,[Type]
           ,[Enabled]
           ,[StopOnError]
           ,[LeasedByMachineName]
           ,[LeasedUntilUtc]
           ,[LastStartUtc]
           ,[LastEndUtc]
           ,[LastSuccessUtc])
     VALUES
           (
           'NAME_OF_TASK'
           SCHEDULE_IN_SECONDS
           'NAMESPACE.ClassName, NAMESPACE'
           IS_ENABLED, -- 1-yes, 0-no
           SHOULD_STOP_ON_ERROR, -- 1-yes, 0-no
           NULL,
           NULL,
           NULL,
           NULL,
           NULL
           )
GO
</code></pre>

After that&#8217;s done, you should be able to immediately run the task.