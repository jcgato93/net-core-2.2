# Introduccion a Swagger

- La idea de Swagger es que nos permite generar una documentacion interactiva de nuesto Web API.

- Swagger lo que hace es leer nuestro código fuente, y a
partir del mismo generar una página web la cual los
usuarios podrán utilizar para consultar los distintos
endpoints de nuestro Web API.

- Los usuarios pueden leer comentarios acerca de nuestros 
endpoints.


# Implementar Swagger

- Primero debemos instalar el paquede de Swagger

<p>
- Esto puede hacerce desde la terminal con el siguiente
comando en visual studio:

> Install-Package Swashbuckle.AspNetCore


- utilizando el dotnet CLI 

> dotnet add package Swashbuckle.AspNetCore
</p>


- Configurar Swagger. Para esto vamos a la clase Startup
en el metodo ConfigureServices

```csharp
public void ConfigureServices(IServiceCollection services)
{

    services.AddSwaggerGen(config =>
    {
        config.SwaggerDoc("v1",new Info {Title="Mi Web API", Version = "v1"});
    });
}
```

- Configurar middlewares en el metodo Configure

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSwagger();

            app.UseSwaggerUI(config =>
            {
                config.SwaggerEndpoint("/swagger/v1/swagger.json", "Mi API V1");
                
            });
}
```
