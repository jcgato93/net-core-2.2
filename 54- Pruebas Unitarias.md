# Pruebas Unitarias

- Una prueba unitaria se encarga de probar una unidad de trabajo.

- Prueban una pequeña y aislada parte del software.

- Tiene la particularidad de ser bastante rapidas.

## Crear prueba unitaria

``` csharp
namespace ProyectoPruebas
{
    [TestClass]
    public class TransferenciasTestsSinMocks
    {
        [TestMethod]
        public void TransferenciaEntreCuentasConFondosInsuficientesArrojaUnError()
        {
            // Preparaci�n
            Exception expectedException = null;
            Cuenta origen = new Cuenta() { Fondos = 0 };
            Cuenta destino = new Cuenta() { Fondos = 0 };
            decimal montoATransferir = 5m;
            var servicio = new ServicioDeTransferenciasSinMocks();

            // Prueba
            try
            {
                servicio.TransferirEntreCuentas(origen, destino, montoATransferir);
                Assert.Fail("Un error debi� ser arrojado");
            }
            catch (Exception ex)
            {
                expectedException = ex;
            }

            // Verificaci�n
            Assert.IsTrue(expectedException is ApplicationException);
            Assert.AreEqual("La cuenta origen no tiene fondos suficientes para realizar la operaci�n", expectedException.Message);
        }

        [TestMethod]
        public void TransferenciaEntreCuentasEditaLosFondos()
        {
            // Preparaci�n
            Cuenta origen = new Cuenta() { Fondos = 10 };
            Cuenta destino = new Cuenta() { Fondos = 5 };
            decimal montoATransferir = 7m;
            var servicio = new ServicioDeTransferenciasSinMocks();

            // Prueba
            servicio.TransferirEntreCuentas(origen, destino, montoATransferir);

            // Verificaci�n
            Assert.AreEqual(3, origen.Fondos);
            Assert.AreEqual(12, destino.Fondos);
        }
    }
}
```