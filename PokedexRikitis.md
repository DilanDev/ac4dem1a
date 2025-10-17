
# Despliegue de la Aplicación PokeDex en la Nube
El propósito de este informe es detallar el procedimiento para lanzar la aplicación web PokeDex (Angular) en un entorno en la nube, garantizando que esté accesible al público, configurando el dominio adecuadamente, utilizando HTTPS y preparándose para añadir encabezados de seguridad HTTP.
Este informe se adhiere a las recomendaciones de DevOps y a las normas de documentación técnica profesional, tal como se requiere en el caso de estudio.
Se incluyen los pasos llevados a cabo, las configuraciones realizadas, los errores detectados y sus soluciones, además de referencias a capturas de pantalla que respaldan el proceso.
## Tecnologías y servicios utilizados
-   **Azure Virtual Machine (vm-coolify)** 
-   **Coolify (plataforma PaaS autoalojada)**
-   **Hostinger (compra del dominio)**
-   **Cloudflare (DNS y SSL)**
-   **Repositorio GitHub con proyecto Angular**
-   **SecurityHeaders.com (evaluación de seguridad HTTP)**

# 1. Creación de la máquina virtual en Azure (vm-coolify)

Se utilizó Azure como proveedor de nube para alojar la aplicación mediante una máquina virtual.

## 1.1 seleccion de archivo
Se accedió al portal de Azure y se creó un nuevo recurso del tipo **Virtual Machine**.
![EVINDENCIA 1](Screenshots/Captura%20de%20pantalla%202025-10-17%20120039.png)
## 1.2 configuracion basica
-   Nombre de la VM: **vm-coolify**
-   Sistema operativo: Linux (Ubuntu Server)
-   Región: la más cercana para mejor latencia
-   Tamaño: configuración estándar (suficiente para ejecutar Coolify y la aplicación)
![EVINDENCIA 2](Screenshots/Captura%20de%20pantalla%202025-10-17%20121014.png)
![EVINDENCIA 3](Screenshots/Captura%20de%20pantalla%202025-10-17%20121028.png)
## 1.3 autenticacion 
Se configuró el acceso por:
-   Usuario + contraseña o
-   SSH Key (según selección de la captura)
![EVINDENCIA 4](Screenshots/Captura%20de%20pantalla%202025-10-17%20121044.png)
## 1.4 red y puertos
Se validó que el grupo de seguridad de red permitiera el acceso a:
-   Puerto 22 (SSH)
-   Puerto 80 (HTTP)
-   Puerto 443 (HTTPS)
![EVINDENCIA 4](Screenshots/Captura%20de%20pantalla%202025-10-17%20121101.png)
## 1.5 Creacion de la VM
Se revisó el resumen y se creó la VM.  
Una vez desplegada, Azure asignó una **IP pública**, que se utilizó para acceder vía SSH.
![EVINDENCIA 4](Screenshots/Captura%20de%20pantalla%202025-10-17%20121435.png)
# 2. Instalación y configuración inicial de Coolify en la VM

La idea principal era usar  Coolify como herramienta para desplegar la aplicación Angular, evitando asíla tarea de tener que configurar a mano un servidor web o loscontenedores necesarios.

## 2.1. Conexión a la VM

Accedimos por SSH usando la IP pública de la VM.
![EVINDENCIA 4](Screenshots/Captura%20de%20pantalla%202025-10-17%20121724.png)
## 2.2. Actualización del sistema

Se levaron a cabo mejoras esenciales en el servidor
![EVINDENCIA 4](Screenshots/Captura%20de%20pantalla%202025-10-17%20164317.png)

## 2.3. Instalación de Coolify
En esta etapa, surgieron ciertos problemas vinculados a autorizaciones o configuraciones del sistema; concretamente, fallos al reactivar el servidor proxy o al establecer comunicación con el procesointerno.

![EVINDENCIA 4](Screenshots/Captura%20de%20pantalla%202025-10-17%20164434.png)
## 2.4. Acceso al panel de Coolify

Tras la instalación, se ingresó al panel web de Coolify mediante el navegador, empleando la dirección IP pública.
![EVINDENCIA 4](Screenshots/Captura%20de%20pantalla%202025-10-17%20123848.png)

# 3. Compra del dominio en Hostinger

Para acceder a la aplicación a través de una dirección web específica, se optó por adquirir un dominio en Hostinger.
![EVINDENCIA 4](Screenshots/Captura%20de%20pantalla%202025-10-17%20124730.png)
## 3.1. Búsqueda y compra

Buscamos un dominio disponible y se completó el proceso de compra.

## 3.2. Configuración inicial del dominio

Inicialmente, Hostinger gestionaba los **nameservers** del dominio.  
Posteriormente, se cambiarían para utilizar Cloudflare.


# 4. Configuración de Cloudflare como gestor DNS y SSL

Para potenciar la protección, optimizar el funcionamiento y ejercer un dominio superior sobre el DNS y los certificados SSL, se implementó Cloudflare actuando de puente entre el nombre de dominio y la máquina virtual.

## 4.1. Agregar dominio a Cloudflare

Se añadió el dominio comprado en Hostinger a Cloudflare.

## 4.2. Cambio de nameservers en Hostinger

Cloudflare proporcionó dos servidores de nombres (NS1 y NS2).  
Se reemplazaron los nameservers de Hostinger por los de Cloudflare.

## 4.3. Creación de registros DNS en Cloudflare

Se crearon registros tipo A apuntando a la IP pública de la VM:

-   **@** → IP pública de la VM
-   **www** → IP pública de la VM (o CNAME @)

![EVINDENCIA 4](Screenshots/Captura%20de%20pantalla%202025-10-17%20124907.png)

# 5. Despliegue de la aplicación Angular en Coolify

Luego de configurar  el dominio y Cloudflare, procedimos a desplegar la aplicación PokeDex (Angular) utilizando Coolify.

## 5.1. Crear un nuevo proyecto en Coolify

En el panel de Coolify seleccionamos la opción **“Create a new Resource”** y luego **“Static Site”** 
![EVINDENCIA 4](Screenshots/Captura%20de%20pantalla%202025-10-17%20124510.png)
## 5.2. Conexión con el repositorio o archivos

Coolify permite dos opciones para proyectos estáticos:

-   Conectar directamente el repositorio de GitHub.
-   Cargar archivos manualmente o desde URL del build

Nosotros seleccionamos Github

## 5.3. Construcción del proyecto Angular
Como la aplicación está desarrollada en Angular, se debe generar la versión de producción antes del despliegue.

![EVINDENCIA 4](Screenshots/Captura%20de%20pantalla%202025-10-17%20164458.png)

Esto genero la carpeta 

![EVINDENCIA 4](Screenshots/Captura%20de%20pantalla%202025-10-17%20164504.png)

## 5.4. Configuración del dominio dentro de Coolify
En la configuración del proyecto se añadió el dominio personalizado **(pokedexrikitis.site)** Coolify intentó verificar el dominio, pero se presentó el error:

![EVINDENCIA 4](Screenshots/Captura%20de%20pantalla%202025-10-17%20124528.png)
**"Validating DNS failed"**
Esto sucedió porque la propagación DNS aún no se había completado o porque el registro A/CNAME no apuntaba correctamente a la IP pública.

### Solución:
-   Se verificó que Cloudflare estaba activo.
-   Se revisaron los registros DNS.
-   Se esperó unos minutos para la propagación.
-   Se volvió a intentar en Coolify → esta vez funcionó.

## 5.5. Deploy de la aplicación
Coolify ejecutó el deployment automático.  
Sin embargo, al acceder a la URL, la aplicación cargaba, pero:

![EVINDENCIA 4](Screenshots/Captura%20de%20pantalla%202025-10-17%20124630.png)
### Problema 1: Imágenes no cargan (404 Not Found)

En la consola del navegador aparecía:
GET https://midominio.com/pokedex-angular/assets/images/pokemon-green.png 404 (Not Found)
Para **potenciar la protección**, **optimizar el funcionamiento** y **ejercer un dominio superior sobre el** DNS y **los** certificados SSL, se **implementó** Cloudflare **actuando de puente** entre el **nombre de** dominio y la **máquina virtual**.

# 6. Configuración de HTTPS / SSL
Una vez que el dominio ejecutaba correctamente y la aplicación era accesible, se configuró HTTPS para asegurar la conexión.A traves de cloudflare que ofrece certificados SSL automáticos y gratuitos, por lo que se utilizó esta opción.
![EVINDENCIA 4](Screenshots/Captura%20de%20pantalla%202025-10-17%20125153.png)
## 6.1. Cloudflare SSL/TLS
Se ingresó a **Cloudflare → SSL/TLS**.
Seleccionamos el modo de encriptacion por su **flexibilidad, full y full(strict)** encriptacion entre cliente y cloudflare, de extremo a extremo y con/sin certificado valido en el servidor.

Usamos **full(flexible inicialmente hasta probar)**

## 6.2. Redirección HTTP → HTTPS
Para asegurar que todo el tráfico use HTTPS, se activó:
**Always Use HTTPS = ON**
Con esto, cualquier intento de acceder mediante `https://pokedexrikitis.site/` se redirige automáticamente a `https://pokedexrikitis.site/`

## 6.3. Verificación
Se accedió a la URL y el candado de seguridad apareció correctamente,

# 7. Escaneo en securityheaders.com
Después de configurar el dominio y el HTTPS, se evaluó la seguridad de la aplicación utilizando el sitio:
https://securityheaders.com/
![EVINDENCIA 4](Screenshots/Captura%20de%20pantalla%202025-10-17%20172553.png)
## 7.1. Proceso
-   Se ingresó la URL pública de la aplicación (con HTTPS).
-   Se ejecutó el escaneo.  
   
## 7.2. Resultado inicial
La calificación inicial fue **baja (por ejemplo: F, E o D)** debido a que los encabezados de seguridad aún no habían sido aplicados.

Los encabezados faltantes señalados fueron:
-   Content-Security-Policy
-   Strict-Transport-Security
-   X-Content-Type-Options 
-   X-Frame-Options 
-   Referrer-Policy

## 7.3. Próximos pasos
Se comprobo que, para obtener una mejor puntuación antes de la entrega definitiva, era necesario implementar las cabeceras tanto  en Cloudflare **como** en el servidor web configurado **a través** de **Coolify**.

# 8. Problemas encontrados y soluciones aplicadas
Durante el proceso de despliegue se presentaron  múltiples problemas reales, que fueron identificados y resueltos
## 8.1. Propagación DNS lenta
**Problema:** El dominio no resolvía correctamente o señalaba IP antigua.  
**Causa:** Nameservers recién cambiados a Cloudflare.  
**Solución:** Esperar propagación (5-30 min) y limpiar caché DNS.

## 8.2. Error en Coolify: “Validating DNS failed”
**Problema:** Coolify no validaba el dominio.  
**Causa:** Registros DNS mal configurados   
**Solución:** Revisamos registros A/CNAME, eliminamos duplicados, esperaramos propagación y reintentamos
## 8.3. Coolify y el proxy interno
**Problema:** En algunos intentos, Coolify mostraba errores del proxy al crear servicios.  
**Causa:** La configuración inicial podia estar incompleta o ajustes de red internos.  
**Solución:** Reiniciamos Coolify, reiniciamos servicio interno, revisamos logs.
## 8.4. Imágenes Angular 404 (Not Found)
**Problema:** Las imágenes no cargaban, generando errores 404 en `/assets/images/...`.  
**Causa técnica:** Ruta generada en Angular esperaba que la app estuviera en `/pokedex-angular/`, pero el deployment servía desde raíz `/`.  
**Soluciones consideradas:**

-   Revisar `base href` en `index.html`. 
-   Ajustar `deployUrl` o `assets` en `angular.json`.   
-   Mover contenido de `dist/nombre-proyecto` a raíz directamente.
-   Verificar estructura de `assets/` después del build.
## 8.5. HTTPS no funcionaba al inicio
**Problema:** El dominio cargaba en HTTP o mostraba error de certificado.  
**Causa:** Configuración incompleta en Cloudflare (modo SSL/TLS incorrecto).  
**Solución:**

-   Activar “Full” o “Full (Strict)”.
-   Habilitar “Always Use HTTPS”.  
   
## 8.6. Calificación de seguridad baja
**Problema:** securityheaders.com muestra F o D.  
**Causa:** Falta de encabezados de seguridad.  
**Solución pendiente:** Agregar headers en Cloudflare o servidor.
# 9. Conclusión
El despliegue de la aplicación PokeDex en la nube se realizó correctamente utilizando una infraestructura personalizada basada en:
- Máquina virtual en Azure (vm-coolify)  
- Plataforma Coolify para gestionar el despliegue  
-Dominio personalizado adquirido en Hostinger  
- Cloudflare como gestor DNS y SSL  
- HTTPS funcional y dominio activo  
-Aplicación Angular desplegada y accesible públicamente










                                         




  



[def]: ./Screenshots/Captura%20de%20pantalla%202025-10-17%20120039.png