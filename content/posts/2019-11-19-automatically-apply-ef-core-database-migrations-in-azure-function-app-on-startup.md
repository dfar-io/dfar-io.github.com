---
title: Automatically Apply EF Core Database Migrations in Azure Function App on Startup
author: dfar

date: 2019-11-19T18:04:49+00:00
url: /automatically-apply-ef-core-database-migrations-in-azure-function-app-on-startup/
categories:
  - Uncategorized

---
To apply database migrations to an Azure Function app on startup, you&#8217;ll need to have the following set up first:

  * <a rel="noreferrer noopener" aria-label="Entity Framework in Azure Functions (opens in a new tab)" href="https://dev.to/azure/using-entity-framework-with-azure-functions-50aa" target="_blank">Entity Framework in Azure Functions</a>

Once the above is set up, you can set up the application to apply all migrations on startup, making for a link between the running application and the database it is dependent on. This is especially useful for local development &#8211; as you won&#8217;t run into cases where a local schema is outdated.

Add the following code to the `Startup.cs` file:

<pre class="wp-block-code"><code>...

&#91;assembly: FunctionsStartup(typeof(YOUR_NAMESPACE.Startup))]

namespace YOUR_NAMESPACE
{
  class Startup : FunctionsStartup
  {
    public override void Configure(IFunctionsHostBuilder builder)
    {
      ConfigureDatabase(builder);
      ...
    }

    private void ConfigureDatabase(IFunctionsHostBuilder builder)
    {
      string SqlConnection =
                    Environment.GetEnvironmentVariable(
                      Constants.ConnectionStringKey);

      if (SqlConnection == null)
      {
        throw new ArgumentNullException(
          $"Environment variable {Constants.ConnectionStringKey} not set.");
      }

      builder.Services.AddDbContext&lt;Context>(
        options => options.UseSqlServer(SqlConnection));

      var optionsBuilder = new DbContextOptionsBuilder&lt;Context>();
      optionsBuilder.UseSqlServer(SqlConnection);

      using (var context = new Context(optionsBuilder.Options))
      {
        try
        {
          context.Database.Migrate();
        }
        catch (Exception e)
        {
          throw new Exception(
            $"Error when migrating database: {e.Message}");
        }
      }
    }
  }
}</code></pre>