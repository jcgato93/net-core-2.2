# Pruebas unitarias a un controlador

- Crear proyecto MsTest

- Intalar dependencias 
    - Moq
    - MicrosoftAspNetCore.Mvc.Core (debe coincidir con la version del proyecto a testear)
    
- Agregar la referencia al los proyectos necesarios


## Implementación

``` csharp
namespace WebApiPruebasUnitarias
{
    [TestClass]
    public class AutoresControllerTests
    {
        [TestMethod]
        public void Get_SiElAutorNoExiste_SeNosRetornaUn404()
        {
            // preparación
            var idAutor = 1;       

            // Crear un mock de la instancia del repositorio para manipular 
            // el resultado que debe retornar     
            var mock = new Mock<IRepositorioAutores>();
            
            // default retorna null
            mock.Setup(x => x.ObtenerPorId(idAutor)).Returns(default(Autor));
            var autoresController = new AutoresController(mock.Object);

            // prueba
            var resultado = autoresController.Get(idAutor);

            // Verificación
            Assert.IsInstanceOfType(resultado.Result, typeof(NotFoundResult));
        }

        [TestMethod]
        public void Get_SiElAutorExiste_SeNosRetornaElAutor()
        {
            // Preparación
            var autorMock = new Autor()
            {
                Id = 1,
                Nombre = "Juan"
            };
            var mock = new Mock<IRepositorioAutores>();
            mock.Setup(x => x.ObtenerPorId(autorMock.Id)).Returns(autorMock);
            var autoresController = new AutoresController(mock.Object);

            // Prueba
            var resultado = autoresController.Get(autorMock.Id);

            // Verificación
            Assert.IsNotNull(resultado.Value);
            Assert.AreEqual(resultado.Value.Id, autorMock.Id);
            Assert.AreEqual(resultado.Value.Nombre, autorMock.Nombre);
        }

    }
}
```