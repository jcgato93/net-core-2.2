Existe la opcion de que estas se creen escogiendo
la opcion de Individual User Account cuando se 
esta creando el proyecto 


De no haberse creado con anterioridad 
seguir los siguientes pasos:

1- Crear carpeta models
y dentro de esta la clase "ApplicationUser"
que herede de "IdentityUser"

```csharp
    public class ApplicationUser : IdentityUser 
    {
        ...
    }
```

2- Crear carpeta "Context" y crear la clase 
"ApplicationDbContext"  y que esta herede de 
"IdentityDbContext"  y se le pasa el "ApplicationUser"
```csharp
    public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            :base(options)
        {

        }

        // aqui se colocan los DbSet<...> de las otras tablas
        // de que iran en la base de datos
    }
```
3- En la clase Startup.cs en el metodo ConfitureServices()
```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        //Configurar ApplicationDbContext
        services.AddDbContext<ApplicationDbContext>(options => options.UseSqlServer(Configuration.GetConnectionString("defaultConnection")));

        // Configuracion del identity
        services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultTokenProviders();

        services.AddMvc().SerCompatibilityVersion(CompatibilityVersion.Version_2_2);
    }
```

4- actualizar la base de datos desde la terminal

    // en visual studio 
    PM> Add-Migration  nombre_migracion

    PM> update-database


    // en visual studio code 
    dotnet ef migrations add nombre_migracion

    dotnet ef database update