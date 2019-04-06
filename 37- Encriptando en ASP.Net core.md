# Encriptando con ASP.Net Core

<p>
Net core ofrece el servicio de proteccion de datos
</p>

- IDataProtection que se encargara de encriptar y 
desencriptar los datos

- IDataProtectionProvider se encargara de crear el 
IDataProtector

## Configuración

- En el metodo <strong>ConfigureServices</strong> de la clase <strong>Startup.cs</strong>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDataProtection();
}
```

- Utilizar encriptacion. Esta puede ser desde cualquier
clase inyectando  <strong>IDataProtectionProvider</strong>


```csharp
public class ValuesController : ControllerBase
{
    private readOnly IDataProtector _protector;

    public ValuesController(IDataProtectionProvider protectionProvider)
    {
        _protector = protectionProvider.CreateProtector("valor_unico_y_quizas_secreto");
    }


    public ActionResult<string> Get(int id)
    {
        string textoPlano = "Juan castillo";

        string textoCifrado = _protector.Protect(textoPlano);

        string textoDesencriptado = __protector.Unprotect(textoCifrado);
    }
}

```


# Limitar tiempo de encriptación

<p>
Podemos especificar un tiempo de encriptacion vigente.
luego de que ese tiempo expira , no prodra ser desencriptado. Esto es muy util cuando se esta trabajando
con Tokens
</p>

```csharp
public class ValuesController : ControllerBase
{
    private readOnly IDataProtector _protector;

    public ValuesController(IDataProtectionProvider protectionProvider)
    {
        _protector = protectionProvider.CreateProtector("valor_unico_y_quizas_secreto");
    }


    public ActionResult<string> Get(int id)
    {
        var protectorLimitadoPorTiempo = _protector.ToTimeLimitDataProtector();

        string textoPlano = "Juan castillo";

        string textoCifrado = protectorLimitadoPorTiempo.Protect(textoPlano, TimeSpan.fromSeconds(5)); // Proteger por 5 seg        

        string textoDesencriptado = protectorLimitadoPorTiempo.Unprotect(textoCifrado);

        return Ok(new { textoPlano, textoCrifrado, textoDesencriptado});
    }
}

```