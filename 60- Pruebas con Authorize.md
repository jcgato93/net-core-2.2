# Pruebas de integracion a controladores con Authorize

<p>
Cuando los controladores tiene un filtro de autenticacion 
tambien aplica para las proyectos de pruebas.
</p>

- Para saltar esos filtros de autorizacion podemos crear una configuracion
de <strong>AllowAnonymous</strong>

``` csharp
 [TestInitialize]
public void Initialize()
{
    _factory = new WebApplicationFactory<Startup>();
}

public WebApplicationFactory<Startup> ConstruirWebHostBuilderConfigurado()
{
    return _factory.WithWebHostBuilder(builder =>
    {
        builder.ConfigureTestServices(services =>
        {
            services.AddMvc(options =>
            {
                // Sobre escribir el servicio de filtrado de authorize 
                // Permitiendo una peticion anonima
                options.Filters.Add(new AllowAnonymousFilter());               
            });

            services.AddScoped<IRepositorioAutores, RepositorioAutoresMock>();
        });
    });
}
```        


## Implementacion con Claims

<p>
Aunque con <strong>AllowAnonymous</strong> se resuelve las peticiones sin 
autenticacion, a veces necesitas obtener los claims que vienen en el sistema 
de <strong>Authorize</strong>. Para manejar esta posible situacion seguimos 
los siguiente pasos
</p>

- Crear la carpeta <strong>Filters</strong> dentro del proyecto Test

- Dentro de la carpeta Filters crear la siguiente clase implementando 
la interfaz <strong>IAsyncActionFilter</strong>

``` csharp
public class FakeUserFilter : IAsyncActionFilter
{
    public async Task OnActionExecutionAsync(ActionExecutingContext context, ActionExecutionDelegate next)
    {
        context.HttpContext.User = new ClaimsPrincipal(new ClaimsIdentity(new List<Claim>
    {
        new Claim(ClaimTypes.NameIdentifier, "12345678-1234-1234-1234-123456789012"),
        new Claim(ClaimTypes.Name, "TestUser"),
        new Claim(ClaimTypes.Email, "test.user@example.com"), // agrega tantos claims como necesites
    }));

        await next();
    }

}
```


- Agregar el filtro a la configuracion
``` csharp
 [TestInitialize]
public void Initialize()
{
    _factory = new WebApplicationFactory<Startup>();
}

public WebApplicationFactory<Startup> ConstruirWebHostBuilderConfigurado()
{
    return _factory.WithWebHostBuilder(builder =>
    {
        builder.ConfigureTestServices(services =>
        {
            services.AddMvc(options =>
            {
                // Agregar filtro con los claims
                options.Filters.Add(new FakeUserFilter());              
            });

            services.AddScoped<IRepositorioAutores, RepositorioAutoresMock>();
        });
    });
}
