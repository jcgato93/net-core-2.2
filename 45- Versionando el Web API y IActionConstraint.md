# Versionando el Web API y IActionConstraint

Se puede presentar la ocasion en la que necesitemos
hacer cambios a nuestra API que puede que sean dificiles
o imposibles de implementar por parte de los clientes que
consumen las API's, por lo que una manera de resolver 
esto es versionando la API para que pueda usarse de 
forma simultanea.

- El cliente indica qué versión del Web API quiere utilizar.

- El cliente puede indicar esto por URL, query string,
cabecera HTTP,etc.

## Definicion de endpoints como los siguientes

- api/v1/autores
- api/v2/autores

## Organizar versiones de la API

<p>
Dentro de la carpeta <strong>Controllers<strong> podrian
organizarce otras dos carpeta
- V1
- V2
</p>


## Alterando la cabecera de la petición con IActionConstraint

- IActionConstraint nos permite indicarle a ASP.Net Core
que action queremos que se ejecuta en determinadas circunstancias

- Los metodos mas comunes donde se ve el uso de 
IActionConstraint es el los atributos de los 
metodos de los controladores como GET,POST,PUT,DELETE



# Implentar logica de los controladores de forma personalizada

- En la carpeta Helpers crear una clase <strong>HttpHeaderIsPresentAtribute</strong>

```csharp
public class HttpHeaderIsPresentAttribute
{
    private string _header;
    private string _value;

    public HttpHeaderIsPresentAttribute(string header,string value)
    {
        _header = header;
        _value = value;
    }

    public int Order => 0;

    public bool Accept(ActionConstraintContext context)
    {
        var headers = constext.RouteContext.HttpContext.Request.Headers;

        if(!headers.ContainsKey(_header))
        {
            return false;
        }

        return string.Equals(headers[_header], _value, StringComparison.OrdinalIgnoreCase);
    }
}
```


- Implementacion en el controlador

<p>
Así , cuando en la cabecera se envie
"x-version" se podra evaluar a que controlador 
esta apuntando.
</p>

```csharp
[ApiController]
[Route("api/[controller]")]
[HttpHeaderIsPresent("x-version","1")] // <-- version 1
public class AutoresController : ControllerBase
{
    // ... 
}


[ApiController]
[Route("api/[controller]")]
[HttpHeaderIsPresent("x-version","2")] // <-- version 2
public class AutoresController : ControllerBase
{
    // ... 
}
```
