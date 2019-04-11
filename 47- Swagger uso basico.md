# Swagger uso basico 

## Personalizar la informacion de la API

- En la clase Startup.cs

```csharp
public void ConfigureServices(IServiceCollection services)
{

    services.AddSwaggerGen(config =>
    {
        config.SwaggerDoc("v1",new Info {
            Title="Mi Web API", 
            Version = "v1",
            Description = "Esta es una descripcion del Web API",
            TermsOfService = "www.terminos.com"
            License = new License()
            {
                Name = "MIT",
                Url = "http://bfy.tw/4ngh"
            },
            Contact = new Contact()
            {
                Name = "Juan ..."
                Email = "test@gmail.com",
                Url = "www.juan.me"
            }
            });
    });
}
```

## Comentarios sobre los EndPoints

- Click derecho sobre el proyecto , y luego sobre la opcion de <strong>Edit nombreProject.csproj</strong>, y se coloca lo siguiente 

``` xml
<Project Sdk = "">
  ....

    <PropertyGroup>
        <GenerateDocumentationFile>true</GenerateDocumentationFile>
        <NoWarn>$(NoWarn);1591</NoWarn>
    </PropertyGroup>


</Project> 

``` 

- Luego en la clase Startup.cs 

```csharp
public void ConfigureServices(IServiceCollection services)
{

    services.AddSwaggerGen(config =>
    {
        config.SwaggerDoc("v1",new Info {
            Title="Mi Web API", 
            Version = "v1",
            Description = "Esta es una descripcion del Web API",
            TermsOfService = "www.terminos.com"
            License = new License()
            {
                Name = "MIT",
                Url = "http://bfy.tw/4ngh"
            },
            Contact = new Contact()
            {
                Name = "Juan ..."
                Email = "test@gmail.com",
                Url = "www.juan.me"
            }
            });

            var xmlFile = $"{Assembly.GetExecutingAssembly().GetName().Name}.xml";
            var xmlPath = Path.Combine(AppContext.BaseDirectory, xmlFile);
            config.IncludeXmlComments(xmlPath);
    });
}
```


- Comentar un EndPoint

```csharp
/// <summary>
/// Borra un elemento específico
/// <summary>
/// <param name="id">Id del elemento a borrar</param>
[HttpDelete("{id}",Name = "BorrarAutor")]
public async Task<ActionResult<Autor>> Delete(int id)
{
    ...
}
```


## Especificar que retorna un EndPoint

- Se puede describir que retorna un EndPoint de forma explicita
utilizando <strong>ProducesResponseType</strong>

```csharp
/// <summary>
/// Borra un elemento específico
/// <summary>
/// <param name="id">Id del elemento a borrar</param>
[HttpDelete("{id}",Name = "BorrarAutor")]
[ProducesResponseType(404)]
[ProducesResponseType(typof(AutorDto),200)]
public async Task<ActionResult<Autor>> Delete(int id)
{
    ...
}
```

- Tambien existen reglas de convencion para no tener que colocar
esto en cada uno de los EndPoints. Para esto , se pega el siguiente codigo sobre el namespace de la clase Startup.cs

```csharp
[assembly: ApiConventionType(typeof(DefaultApiConventions))]

namespace NombreProyecto{
    .....
}
```