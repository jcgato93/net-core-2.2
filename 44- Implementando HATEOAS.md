# Implementando HATEOAS


- Crear la clase <strong>Enlace</strong>
Models/Enlace

```csharp
public class Enlace
{
    // Link
    public string Href { get; private set; }

    // Descripcion
    public string Rel { get; private set; }

    // Metodo de acceso (GET, POST ,PUT ,DELETE)
    public string Metodo { get; private set; }

    public Enlace(string href, string rel, string metodo)
    {
        Href = href;
        Rel = rel;
        Metodo = metodo;
    }
}
```

- Crear la clase <strong>Recurso</strong>
Models/Recurso

```csharp
public class Recurso
{
    public List<Enlace> Enlaces { get; set; } = new List<Enlace>();
}
```


- Crear controlador llamado <strong>RootController</strong>

```csharp
[ApiController]
[Route("api")]
public class RootController : ControllerBase
{
    private readonly IUrlHelper _urlHelper;

    public RootController(IUrlHelper urlHelper)
    {
        // nos ayuda a generar los enlaces 
        // del Web Api de forma dinamica
        this._urlHelper = urlHelper;
    }

    [HttpGet(Name = "GetRoot")]
    public ActionResult<IEnumerable<Enlace>> Get()
    {
        List<Enlace> enlaces = new List<Enlace>();

        // Aquí colocamos los links

        return enlaces;
    }
}
```


- Registrar servicio en la clase Startup.cs en el 
metodo ConfigureServices

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<IActionContextAccesor, ActionContextAccesor>()
    .AddScoped<IUrlHelper>(x => x
    .GetRequiredService<IUrlHelperFactory>()
    .GetUrlHelper(x.GetRequiredService<IActionContextAccessor>()
    .ActionContext));
}
```


- Nombrar todos los metodos de los controladores 
para que puedan ser listados

```csharp
[HttpGet(Name="nombre del metodo")]
public ActionResult Get(){...}
```


- Agregar en el controlador RootController el listado 
de los enlaces previamente nombrados

```csharp
[ApiController]
[Route("api")]
public class RootController : ControllerBase
{
    private readonly IUrlHelper _urlHelper;

    public RootController(IUrlHelper urlHelper)
    {
        // nos ayuda a generar los enlaces 
        // del Web Api de forma dinamica
        this._urlHelper = urlHelper;
    }

    [HttpGet(Name = "GetRoot")]
    public ActionResult<IEnumerable<Enlace>> Get()
    {
        List<Enlace> enlaces = new List<Enlace>();

        // Aquí colocamos los links
        enlaces.Add(new Enlace(href: _urlHelper.Link("GetRoot",new {}),rel: "self", metodo:"GET"));

        enlaces.Add(new Enlace(href: _urlHelper.Link("ObtenerAutores",new {}),rel: "autores", metodo:"POST"));

        return enlaces;
    }
}
```

ya con esto si se va a la url de localhost/api aparecera el listado


# Listar recursos de una clase

<p>
Para ver un ejemplo de esto
</p>

- vamos a la clase AutorDTO y heredamos de recurso

- En el controlador de Autores

```csharp
public class AutoresController : ControllerBase
{
    private readonly IUrlHelper urlHelper;

    public AutoresController(IUrlHelper urlHelper)
    {
        this.urlHelper = urlHelper;
    }

    public async Task<ActionResult<AutorDTO>> Get(int id)
    {
        var autor = await context.Autores.FirstOrDefaultAsync(x => x.Id == id);

        if(autor == null)
        {
            return NotFound();
        }

        var autorDTO  = mapper.Map<AutorDTO>(autor);

        GenerarEnlaces(autorDto);

        reutnr autorDTO;
    }

    private void GenerarEnlaces(AutorDTO autor)
    {
            autor.Enlaces.Add(new Enlace(urlHelper.Link("ObtenerAutor", new { id = autor.Id }), rel: "self", metodo: "GET"));
            autor.Enlaces.Add(new Enlace(urlHelper.Link("ActualizarAutor", new { id = autor.Id }), rel: "update-author", metodo: "PUT"));
            autor.Enlaces.Add(new Enlace(urlHelper.Link("BorrarAutor", new { id = autor.Id }), rel: "delete-author", metodo: "DELETE"));
    }

}
```