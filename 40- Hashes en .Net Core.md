# Hashes en .Net Core

- Crear una clase <strong>HashResult</strong>

```csharp
public class HashResult
{
    public string Hash { get; set; }
    public byte[] Salt { get; set; }
}
```

- Crear la clase <strong>HashService</strong>

```csharp
public class HashService
{
    // sal aleatorio
    public HashResult Hash(string input)
    {
        // Genera una sal aleatorio
        byte[] salt = new byte[128 / 8];
        using(var rng = RandomNumberGenerator.Create())
        {
            rng.GetBytes(salt);
        }

        return Hash(input, salt);
    }


    // sal existente
    public HashResult Hash(string input, byte[] salt)
    {
        // deriva una subllave de 256 bits (usa HMACSHAI con 10.000 iteraciones)
        string hashed = Convert.ToBase64String(KeyDerivation.Pbkdf2(
            password: inupt,
            salt: salt,
            prf: KeyDerivationPrf.HMACSHA1,
            iterationCount: 10000,
            numBytesRequestd: 256 / 8
        ));

        return new HashResult()
        {
            Hash = hashed,
            Salt = salt
        };
    }
}
```


- Registrar Servicio en la clase Startup.cs

```csharp

public void ConfigureServices(IServiceCollection services)
{
    services.AddScoped<Hashservices>();
}
```


- Inyectar servicio en una clase

```csharp
public class ValuesController : ControllerBase
{

    private readonly HashService _hashService;

    public ValuesController(HashService hashService)
    {
        _hashService = hashService;
    }


    [HttpGet("hash")]
    public ActionResult GetHash()
    {
        string textoPlano = "Camilo";
        
        // Aleatorio
        var hashResult1 = _hashService.Hash(textoPlano).Hash;

        // Usando una salt
        var hashResult2 = _hashService.Hash(textoPlano,"sal").Hash;

        // Usando una salt
        var hashResult3 = _hashService.Hash(textoPlano,"sal").Hash;

        return Ok(new {textoPlano, hashResult1, hashResult2, hashResult3});
    }

}
```

