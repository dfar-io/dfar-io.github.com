---
title: Setting up SQL Server in Arch Linux
author: dfar

date: 2020-04-21T14:49:05+00:00
url: /setting-up-sql-server-in-arch-linux/
categories:
  - Technology

---
First, install the AUR packages provided for installation:

<pre class="wp-block-code"><code>yay -S mssql-server msodbcsql mssql-tools</code></pre>

Once installed, perform the configuration:

<pre class="wp-block-code"><code>sudo /opt/mssql/bin/mssql-conf setup</code></pre>

Once configured, there are a few ways to access the database:

  * Using the **sqlcmd** command line tool.
  * Using the <a rel="noreferrer noopener" href="https://docs.microsoft.com/en-us/sql/visual-studio-code/sql-server-develop-use-vscode?view=sql-server-ver15" target="_blank">VSCode mssql extension</a>.
  * Using <a href="https://docs.microsoft.com/en-us/sql/azure-data-studio/download-azure-data-studio?view=sql-server-ver15" target="_blank" rel="noreferrer noopener">Azure Data Studio</a>.