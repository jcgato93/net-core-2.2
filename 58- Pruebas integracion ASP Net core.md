# Pruebas de integracion con asp.net core 2.2

- Primero agregar un nuevo proyecto MSTest Test Project (.NET Core)

- Intalar las dependencias 
    - instalar el paquete Microsoft.AspNetCore.App  (debe
    ser de la misma version del proyecto a testear)

    - instalar Microsoft AspNetCore.Mvc.Testing

- Modificar el archivo .csproj del proyecto test

se debe reemplazar 

``` xml
<Project Sdk="Microsoft.NET.Sdk">
```

por la siguiente linea de codigo
``` xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```


# Implementacion

- Lo primero que debemos hacer es agregar la referecia 
del proyecto a testear.

- utilizar el <strong>WebApplicationFactory </strong> para
poder crear en memoria un servidor web

- Utilizar el <strong>[TestInitialize]</strong> para ejecutar codigo antest de correr las pruebas

``` csharp
namespace WebApiPruebasDeIntegracion
{
    [TestClass]
    public class ValuesControllerTests
    {
        private WebApplicationFactory<Startup> _factory;

        [TestInitialize]
        public void Initialize()
        {
            _factory = new WebApplicationFactory<Startup>();
        }      
    }
}
```

- Crear una prueba del controlador <strong>values</strong>
``` csharp
namespace WebApiPruebasDeIntegracion
{
    [TestClass]
    public class ValuesControllerTests
    {
        private WebApplicationFactory<Startup> _factory;

        [TestInitialize]
        public void Initialize()
        {
            _factory = new WebApplicationFactory<Startup>();
        }

        [TestMethod]
        public async Task Get_DevuelveArregloDeDosElementos()
        {
            // Crear un cliente para hacer peticion Http
            var client = _factory.CreateClient();

            // Especificar la ruta del controlador
            var url = "/api/values";

            // Ejecutar la peticion Http
            var response = await client.GetAsync(url);

            // Verifica si la respuesta tiene algun codigo de Status Http
            // cuando no lo tiene es posible que la razon es que esta mal la ruta de la url
            if (!response.IsSuccessStatusCode)
            {
                Assert.IsTrue(false, "C�digo de estatus no exitoso: " + response.StatusCode);
            }

            /*
            La respuesta del API sera un string de un Json por lo
            que es necesario deserializarlo para convertirlo en un 
            objeto de C#

            JsonConvert.DeserializeObject<string[]>
            el campo dentro de <..> indica a que tipo de objeto debe ser convertido
            */
            var result = JsonConvert.DeserializeObject<string[]>(
                await response.Content.ReadAsStringAsync());

            // Verificacion del resultado                
            Assert.AreEqual(expected: 2, actual: result.Length);
        }
    }
}
```