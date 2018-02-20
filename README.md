# Project setup (.NET Core 2.0)

Replace all instances of ??? with your namespace.

## Packages

Make sure all versions in your .csproj file == 2.0.0

    dotnet add package Microsoft.Extensions.Configuration.Json
    dotnet add package Npgsql.EntityFrameworkCore.PostgreSQL
    dotnet add package Microsoft.EntityFrameworkCore.Tools
    dotnet add package Microsoft.EntityFrameworkCore.Tools.DotNet

Also add this to your .csproj

    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.EntityFrameworkCore.Tools.DotNet" Version="2.0.0" />
    </ItemGroup> 

## appsettings.json

Make sure the connection string is correct.

    {
        "DBInfo":
        {
            "Name": "PostGresConnect",
            "ConnectionString": "server=localhost;userId=postgres;password=postgres;port=5432;database=???;"
        }
    }

## DatabaseOptions.cs

    namespace ???
    {
        public class DatabaseOptions
        {
            public string Name { get; set; }
            public string ConnectionString { get; set; }
        }
    }

## models/DatabaseContext.cs

    using Microsoft.EntityFrameworkCore;
    
    namespace ???.Models
    {
        public class DatabaseContext : DbContext
        {
            public DatabaseContext(DbContextOptions<DatabaseContext> options) : base(options) { }
        }
    } 

## startup.cs

Might require "dotnet clean" and a restart of VSCode

	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Threading.Tasks;
	using Microsoft.AspNetCore.Builder;
	using Microsoft.AspNetCore.Hosting;
	using Microsoft.AspNetCore.Http;
	using Microsoft.Extensions.DependencyInjection;
	using Microsoft.Extensions.Configuration;
	using Microsoft.EntityFrameworkCore;

	using ???.Models;

	namespace ???
	{
	    public class Startup
	    {
	        public IConfiguration Configuration { get; private set; }

	        public void ConfigureServices(IServiceCollection services)
	        {
	            services.AddDbContext<DatabaseContext>(options => options.UseNpgsql(Configuration["DBInfo:ConnectionString"]));

	            services.AddSession();
	            services.AddMvc();
	        }

	        public void Configure(IApplicationBuilder app, IHostingEnvironment env)
	        {
	            if (env.IsDevelopment())
	            {
	                app.UseDeveloperExceptionPage();
	            }

	            var builder = new ConfigurationBuilder()
	            .SetBasePath(env.ContentRootPath)
	            .AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
	            .AddEnvironmentVariables();
	            Configuration = builder.Build();

	            app.UseSession();
	            app.UseMvc();
	        }
	    }
	}

## Migrations

	dotnet ef migrations add xxxxxxx
	dotnet ef database update