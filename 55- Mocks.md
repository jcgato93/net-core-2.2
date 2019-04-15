# Mocks

- Un mock es un objeto el cual intenta suplantar una 
dependencia de una clase.

- Los mocks son importantes porque nos ayudan a enfocar
nuestras pruebas en una clase específica sin tener la 
molestia de lidiar con dependencias.

- Utilizando mocks , podemos especificar el tipo de
resultado que debe retorna el metodo de la clase que se 
utiliza como mock.

- Para este se utiliza la libreria Moq.

## Implementacion 

- Primero se debe descargar el paquete Moq.


``` csharp
namespace ProyectoPruebas
{
    [TestClass]
    public class TransferenciasTestsConMocks
    {
        [TestMethod]
        public void TransferenciaInvalidaArrojaException()
        {
            // Preparación
            Exception expectedException = null;
            Cuenta origen = new Cuenta() { Fondos = 0 };
            Cuenta destino = new Cuenta() { Fondos = 0 };
            decimal montoATransferir = 5m;
            var mock = new Mock<IServicioValidacionesDeTransferencias>();
            string mensajeDeError = "mensaje de error";
            mock.Setup(x => x.RealizarValidaciones(origen, destino, montoATransferir)).Returns(mensajeDeError);
            var servicio = new ServicioDeTransferencias(mock.Object);

            // Prueba
            try
            {
                servicio.TransferirEntreCuentas(origen, destino, montoATransferir);
                Assert.Fail("Un error debió ser arrojado");
            }
            catch (Exception ex)
            {
                expectedException = ex;
            }

            // Verificación
            Assert.IsTrue(expectedException is ApplicationException);
            Assert.AreEqual(mensajeDeError, expectedException.Message);
        }

        [TestMethod]
        public void TransferenciaValidaEditaLosFondosDeLasCuentas()
        {
            // Preparación
            Cuenta origen = new Cuenta() { Fondos = 10 };
            Cuenta destino = new Cuenta() { Fondos = 5 };
            decimal montoATransferir = 7m;
            var mock = new Mock<IServicioValidacionesDeTransferencias>();

            mock.Setup(x => x.RealizarValidaciones(origen, destino, montoATransferir)).Returns(string.Empty);

            var servicio = new ServicioDeTransferencias(mock.Object);

            // Prueba
            servicio.TransferirEntreCuentas(origen, destino, montoATransferir);

            // Verificación
            Assert.AreEqual(3, origen.Fondos);
            Assert.AreEqual(12, destino.Fondos);
        }
    }
}

```