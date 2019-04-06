# Habilitando CORS por atributo

- Habilitar la opcion en la clase Startup.cs en el metodo 
ConfigureServices

```csharp
public void ConfigureServices(IserviceCollection services)
{
    services.AddCors(options =>
    {
        options.AddPolicy("PermitirApiRquest",
        builder => builder.WithOrigins("www.apirequest.com").AllowAnyMethod().AllowAnyHeader());
    });
}
```

- Configuracion en el metodo 

```csharp
public void Configure(IApplicationBuilder app,IHostingEnviroment env){
    // otro codigo

    app.UseHttpsRedirection();
    app.UseAuthentication();  
    app.UseCors() // <----- configuracion aqui
    app.UseMvc();
}
```

- Configuracion en un Controlador especifico de la siguiente manera

```csharp
[Route("api/[controller]")]
[ApiController]
[EnableCors("PermitirApirequest")] // <--- colocar el nombre del policy configurado en ConfigureServices de la clase Startup.cs
public class ValuesController : ControllerBase
{
    // ......
}
```
