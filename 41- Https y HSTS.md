# HTTPS y HSTS

- TLS (Transport Layer Security) es un protocolo estandar
para establecer una conexion entre un servidor web y un 
cliente.

- El middleware <strong>UseHttpsRedirection</strong> sirve
para redirigir peticiones HTTP  a HTTPS

- El middleware <strong>UseHsts</strong> sirve para enviar
a nuestros clientes la cabecera Strict-Transport-Security
(HSTS)

- Lo ideal es solo permitir conexiones HTTPS.