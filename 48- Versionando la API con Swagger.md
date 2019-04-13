# Versionando la API con Swagger

- Para especificar esto en Swagger podemos utilizar una convención

- Lo primero es separar los controladores por carpetas de version Ej :  V1 , V2



- Se debe hacer es nombrar los namespace de los controladores

``` csharp
namespace NombreProyecto.Controllers.V1 // <-- V1
{
    //...
}
```

- Configurar otro documento de Swagger en la clase Startup.cs
en el metodo ConfigureServices
``` csharp
public void ConfigureServices(IServiceCollection services){
        //...


         services.AddSwaggerGen(config =>
        {
       config.SwaggerDoc("v1", new Info {
                    // ...
                }

        config.SwaggerDoc("v2", new Info { Title = "Mi Web API", Version = "v2" });
        });

            
}
```


- Configuracion en el metodo Configure
``` csharp
   public void Configure(IApplicationBuilder app, IHostingEnvironment env)
        {
            app.UseSwagger();

            app.UseSwaggerUI(config =>
            {
                config.SwaggerEndpoint("/swagger/v1/swagger.json", "Mi API V1");
                config.SwaggerEndpoint("/swagger/v2/swagger.json", "Mi API V2");
            });

            if (env.IsDevelopment())
            {
                app.UseDeveloperExceptionPage();
            }
            else
            {
                // The default HSTS value is 30 days. You may want to change this for production scenarios, see https://aka.ms/aspnetcore-hsts.
                app.UseHsts();
            }

            app.UseHttpsRedirection();
            app.UseMvc();
        }
```

- Crear clase de convencion de redireccion segun el namespace,
esta puede ser dentro del mismo archivo Startup.cs despues del metodo configure
``` csharp
public class ApiExplorerGroupPerVersionConvention : IControllerModelConvention
{
            public void Apply(ControllerModel controller)
            {
                // Ejemplo: "Controllers.V1"
                var controllerNamespace = controller.ControllerType.Namespace;
                var apiVersion = controllerNamespace.Split('.').Last().ToLower();
                controller.ApiExplorer.GroupName = apiVersion;
            }
}
```


- Aplicar la convencion en el metodo ConfigureServices
en el services.AddMvc

``` csharp
public void ConfigureServices(IServiceCollection services){
        //...
services.AddMvc(config =>
            {
                config.Conventions.Add(new ApiExplorerGroupPerVersionConvention());
            }).SetCompatibilityVersion(CompatibilityVersion.Version_2_2);                

    //...
}                
```


- Por ultimo los controladores deben de llevar la siguiente convencion 
de nombres

``` csharp
 [ApiController]
    [Route("api/v1/[controller]")]   
    public class AutoresController: ControllerBase
    {

```        