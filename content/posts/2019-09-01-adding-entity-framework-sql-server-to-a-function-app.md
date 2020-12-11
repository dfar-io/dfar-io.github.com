---
title: Adding Entity Framework/SQL Server to a Function App
author: dfar

date: 2019-09-01T20:14:49+00:00
url: /adding-entity-framework-sql-server-to-a-function-app/
categories:
  - Uncategorized

---
When working with function apps, you may want to connect SQL Server to allow for using an API with data persistence.

## Setting up Project to Use Entity Framework

Add the following to your `.csproj` file, in the section with `PackageReference` entries:

<pre class="wp-block-code"><code>&lt;ItemGroup>
    ...
    &lt;PackageReference Include="Microsoft.Azure.Functions.Extensions" Version="1.0.0" />
    &lt;PackageReference Include="Microsoft.EntityFrameworkCore.SqlServer" Version="2.2.3" />
    &lt;PackageReference Include="Microsoft.EntityFrameworkCore.Design" Version="2.2.3" />
    &lt;PackageReference Include="Microsoft.EntityFrameworkCore.Tools" Version="2.2.3" />
&lt;/ItemGroup></code></pre>

And then add the following to allow for copying the project DLL (supports builds in both Windows and Linux):

<pre class="wp-block-code"><code>...
  &lt;Target Name="PostBuild" AfterTargets="PostBuildEvent">
    &lt;Exec Command="copy /Y &quot;$(TargetDir)bin\$(ProjectName).dll&quot; &quot;$(TargetDir)$(ProjectName).dll&quot;" Condition=" '$(OS)' == 'Windows_NT'" />
    &lt;Exec Command="cp &quot;$(TargetDir)bin\$(ProjectName).dll&quot; &quot;$(TargetDir)$(ProjectName).dll&quot;" Condition=" '$(OS)' != 'Windows_NT'" />
  &lt;/Target>
&lt;/Project></code></pre>

For setting this up, I&#8217;d recommend having at least one Model ready for use. In our case, let&#8217;s use a simple example with `Entry.cs`:

<pre class="wp-block-code"><code>[Key]
public int EntryId { get; set; }

public DateTime Date { get; set; }

public string Content { get; set; }</code></pre>

Now create the `Context.cs` and `ContextFactory.cs` files in the Models folder:

<pre class="wp-block-code"><code>    
using Microsoft.EntityFrameworkCore;

namespace YOUR_NAMESPACE
{
  public class Context : DbContext
  {
    public Context(DbContextOptions&lt;UsawStatsContext> options)
      : base(options)
    { }

    public DbSet&lt;Entry> MeetResults { get; set; }
  }
}</code></pre>

<pre class="wp-block-code"><code>using System;
using Microsoft.EntityFrameworkCore;
using Microsoft.EntityFrameworkCore.Design;

namespace YOUR_NAMESPACE
{
  public class ContextFactory : IDesignTimeDbContextFactory&lt;Context>
  {
    public Context CreateDbContext(string&#91;] args)
    {
      string SqlConnection =
        Environment.GetEnvironmentVariable("CONN_STR");

      if (SqlConnection == null)
      {
        throw new ArgumentNullException(
          $"Environment variable CONN_STR not set.");
      }

      var optionsBuilder = new DbContextOptionsBuilder&lt;Context>();
      optionsBuilder.UseSqlServer(SqlConnection);

      return new Context(optionsBuilder.Options);
    }
  }
}</code></pre>

Now create a `Startup.cs` file in root:

<pre class="wp-block-code"><code>using System;
using YOUR_NAMESPACE.Models;
using Microsoft.Azure.Functions.Extensions.DependencyInjection;
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.DependencyInjection;

&#91;assembly: FunctionsStartup(typeof(YOUR_NAMESPACE.Startup))]

namespace YOUR_NAMESPACE
{
  class Startup : FunctionsStartup
  {
    ConfigureDatabase(builder);
  }

  private void ConfigureDatabase(IFunctionsHostBuilder builder)
    {
      string SqlConnection =
                    Environment.GetEnvironmentVariable(
                      "CONN_STR");

      if (SqlConnection == null)
      {
        throw new ArgumentNullException(
          $"Environment variable "CONN_STR" not set.");
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
}</code></pre>

## Setting up Database

Finally, you&#8217;ll need to set up a database for use. Create the database and use the connection string &#8211; let&#8217;s use SQL Server locally and set this in the Azure Function project&#8217;s `local.settings.json`:

<pre class="wp-block-code"><code>"Values": {
  ...
  "CONN_STR": "Data Source=localhost\\SQLEXPRESS;Initial Catalog=DB_NAME;Integrated Security=SSPI;"
},</code></pre>

Once this is set, setting up migrations is the next step. Set the environment variable on your PC:

<pre class="wp-block-code"><code>$env:CONN_STR="CONN_STRING"
dotnet ef migrations add InitialCreate</code></pre>

With this step done, start the application and check to see if your database was updated correctly, and that you can see the tables desired.