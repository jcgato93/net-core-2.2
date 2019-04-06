# Llaves de proteccion de datos

<p>
Los algoritmos de encriptación más comunes utilizan una llave. Y los procesos dados por ASP.NET Core no son la excepción. Hasta ahora nos hemos mantenido solamente hablando del string de propósito, sin embargo, es importante notar que el framework va a manejar las llaves por ti, a menos que decidas sobrescribir ciertas configuraciones por defecto. Desde el tiempo de vida de una llave, hasta donde guardar las llaves, son de las configuraciones que podríamos personalizar si lo deseamos.

Por ejemplo, si tu Web API lo colocas en un App Service de Azure, entonces las llaves se van a colocar, por defecto, en la carpeta %HOME%\ASP.NET\DataProtection-Keys. Una consideración especial para tomar en cuenta con esto, es que los distintos slots de tu app van a tener llaves diferentes. Esto puede traer ciertas situaciones inesperadas, como que lo que encriptes en un slot no puedas desencriptarlo en otro slot. Esto puede ser problemático en determinadas ocasiones, sobre todo si acostumbras a manejar tus slots de manera casi transparente para el usuario. La solución para esta problemática es colocar las llaves en un lugar externo, como en Azure Blob Storage.

Del mismo modo, si tienes la aplicación instalada en IIS, las llaves serán persistidas en el registro HKLM.

Es decir, por defecto, el framework verifica en donde se encuentra la aplicación y trata de buscar la mejor manera de guardar las llaves. Es importante que entiendas lo que el framework va a hacer con las llaves, en caso de que necesites sobrescribir el comportamiento por defecto para que se ajuste a tus necesidades.
</p>