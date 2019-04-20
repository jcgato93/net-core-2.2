# Pruebas de integracion con dependencias


- En la mayoria de los casos los controlares trabajan con dependencias
como <strong>Repositorios</strong> que se conectan a la base de datos.

- Para manejar dichas dependencias podemos utilizar los <strong>Mocks</strong>


## Implementacion

- Crear la Carpeta Mocks dentro del proyecto Test

- Crear la clase que implemente la Interfaz del repositorio,
de este modo podemos crear las respuestas esperadas por la implementacion
de la interfaz

``` csharp
//  ./Mocks/RepositorioAutoresMock

public class RepositorioAutoresMock : IRepositorioAutores
{
        public Autor ObtenerPorId(int id)
        {
            if (id == 0)
            {
                return null;
            }

            return new Autor()
            {
                Id = id,
                Nombre = "Grabiel Garcia"
            };
        }
}
```


- En la clase de Test 

``` csharp
  [TestClass]
    public class AutoresControllerTests
    {
        private WebApplicationFactory<Startup> _factory;

        [TestInitialize]
        public void Initialize()
        {
            _factory = new WebApplicationFactory<Startup>();
        }


        // Configuracion del Web Host
        public WebApplicationFactory<Startup> ConstruirWebHostBuilderConfigurado()
        {
            return _factory.WithWebHostBuilder(builder =>
            {
                builder.ConfigureTestServices(services =>
                {                   
                    // Se configura que clase debe implementar la interfaz
                    // de este modo se sobre escribe el servicio del proyecto testeado
                    services.AddScoped<IRepositorioAutores, RepositorioAutoresMock>();
                });
            });
        }

    
    }
```    

- Crear metodos de prueba a los controladores


``` csharp
  [TestClass]
    public class AutoresControllerTests
    {
        private WebApplicationFactory<Startup> _factory;

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
                    services.AddScoped<IRepositorioAutores, RepositorioAutoresMock>();
                });
            });
        }

        [TestMethod]
        public async Task Get_SiElAutorNoExiste_Retorna404()
        {
            // de este modo la configuracion ya no se maneja atraves de _factory directamente
            var client = ConstruirWebHostBuilderConfigurado().CreateClient();

            var url = "/api/autores/0";
            var response = await client.GetAsync(url);

            // Se espera un 404 NotFound
            Assert.AreEqual(expected: HttpStatusCode.NotFound, actual: (int)response.StatusCode);
        }

        [TestMethod]
        public async Task Get_SiElAutorExiste_EntoncesLoDevuelve()
        {
            var client = ConstruirWebHostBuilderConfigurado().CreateClient();

            var url = "/api/autores/7";
            var response = await client.GetAsync(url);

            if (!response.IsSuccessStatusCode)
            {
                Assert.IsTrue(false, "Codigo de estatus no exitoso: " + response.StatusCode);
            }

            var result = JsonConvert.DeserializeObject<Autor>(await response.Content.ReadAsStringAsync());
            Assert.IsNotNull(result);
            Assert.AreEqual(expected: 7, actual: result.Id);
            Assert.AreEqual(expected: "Gabriel Garcia", actual: result.Nombre);
        }
    }

```