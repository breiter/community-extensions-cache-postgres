
# Community.Microsoft.Extensions.Caching.PostgreSQL

## Description

This implemantation uses PostgreSQL as distributed cache.

## Getting Started
1. Install the package into your project
```
dotnet add package Community.Microsoft.Extensions.Caching.PostgreSql
```
2. Add the following line to the `Startup`  `Configure` method.
```c#
services.AddDistributedPostgreSqlCache(setup => 
{
	setup.ConnectionString = configuration["ConnectionString"];
	setup.SchemaName = configuration["SchemaName"];
	setup.TableName = configuration["TableName"];
	setup.DisableRemoveExpired = configuration["DisableRemoveExpired"];
    // Optional - DisableRemoveExpired default is FALSE
	setup.CreateInfrastructure = configuration["CreateInfrastructure"];
	// CreateInfrastructure is optional, default is TRUE
	// This means que every time starts the application the 
	// creation of table and database functions will be verified.
})
```
### `DisableRemoveExpired = True` use case:
When you have 2 or more instances/microservices/processes and you just to leave one of them removing expired items. 
* Note 1: This is not mandatory, see if it fits in you cenario.
* Note 2: Even if you set to `True` and the expired items will not be auto-removed, when you call `GetItem` those expired items are filtred out. In that case you are responsable to manually remove the expired key or update it

3. Then pull from DI like any other service

```c#
    private readonly IDistributedCache _cache;

    public WeatherForecastController(IDistributedCache cache)
    {
        _cache = cache;
    }

```
## What it does to my database:

Creates a table and six functions, see scripts folder for more details.
```
[schemaName].datediff
[schemaName].deletecacheitemformat
[schemaName].deleteexpiredcacheitemsformat
[schemaName].getcacheitemformat
[schemaName].setcache
[schemaName].updatecacheitemformat

```

## Runing the console sample
You will need a local postgresql server with this configuration:
1. Server listening to port 5432 at localhost
1. The password of your `postgres` account, if not attached already to your user.
1. Clone this repo.
1. Run the following commands inside `PostgreSqlCacheSample`:
```shell
dotnet restore
prepare-database.cmd -create
dotnet run
```
![S](sample_project.gif)

Then you can delete the database with:
```
prepare-database.cmd -erase
```
## Change Log
1. v3.0.1 - `DisableRemoveExpired` configuration added; If `TRUE` the cache instance won`t delete expired items.
1. v3.0
   1. [BREAKING CHANGE] - Direct instantiation not preferred
   2. Single thread loop remover
1. v2.0.x - Update everything to net5.0, more detailed sample project.
1. v1.0.8 - Update to latest dependencies -


# License
* MIT
### This is a fork from [repo](https://github.com/wullemsb/Extensions.Caching.PostgreSQL)
