# Fundamentos de Redes — Investigación Guiada
 
> **Curso:** Full Stack Java Developer — Generation Chile  
> **Actividad:** Fundamentos de Redes  
> **Nombre:** Renato Campos
 
---

## 1. ¿Qué es cada dispositivo?
 
| Concepto | Definición simple | Nivel técnico (breve) | Ejemplo real |
|----------|------------------|-----------------------|--------------|
| **Router** | Dispositivo que conecta redes diferentes entre sí y decide por dónde enviar los datos hacia su destino | Opera en **capa 3 (Red)** del modelo OSI. Usa **direcciones IP** y tablas de enrutamiento para determinar la mejor ruta para cada paquete entre redes distintas | El router WiFi de tu casa que conecta tu red local a Internet, o los routers de un ISP que conectan ciudades |
| **Switch** | Dispositivo que conecta varios equipos dentro de una misma red y envía los datos únicamente al dispositivo correcto | Opera en **capa 2 (Enlace de datos)**. Usa **direcciones MAC** y una tabla CAM para reenviar tramas solo al puerto donde está el destinatario | Un switch en el rack de un data center que interconecta 24 servidores en la misma red local |
| **Hub** | Dispositivo que conecta equipos en red pero reenvía todo lo que recibe a todos los puertos sin distinción | Opera en **capa 1 (Física)**. No tiene inteligencia: simplemente replica la señal eléctrica por todos sus puertos (**broadcast**) | Dispositivo usado en redes pequeñas de los años 90, hoy prácticamente reemplazado por completo por switches |
 
---

## 2. ¿Cómo funciona realmente?
 
### ¿Qué hace el router con los paquetes de datos?
 
Cuando un paquete llega al router, este lee la **dirección IP de destino** en el encabezado del paquete, luego consulta su **tabla de enrutamiento** para determinar por cuál interfaz debe reenviarlo. Si el destino está en otra red, el router envía el paquete al siguiente salto (next hop) hasta que finalmente llega a la red del destinatario. Cada salto entre routers se llama un **hop**.
 
Por ejemplo, cuando haces `fetch("https://api.miapp.com")`, ese paquete puede pasar por 10-15 routers distintos antes de llegar al servidor donde está alojada la API.
 
### ¿Cómo decide un switch a dónde enviar la información?
 
El switch mantiene una **tabla MAC** (también llamada tabla CAM) que asocia cada **dirección MAC** con un puerto físico específico. Cuando recibe una trama (frame):
 
1. Lee la **MAC de destino** en el encabezado de la trama.
2. Busca esa MAC en su tabla.
3. Si la encuentra, reenvía la trama **solo** por el puerto correspondiente.
4. Si no la conoce, hace **flooding**: envía la trama por todos los puertos excepto el de origen, una sola vez, para aprender dónde está ese dispositivo.
 
Esto hace que la comunicación sea **eficiente y segura** comparada con un hub.
 
### ¿Por qué el hub es considerado obsoleto?
 
El hub es obsoleto por tres razones principales:
 
- **Colisiones:** Al enviar todo a todos los puertos, los datos colisionan cuando dos dispositivos transmiten al mismo tiempo, degradando el rendimiento.
- **Desperdicio de ancho de banda:** Todo el tráfico se replica innecesariamente a dispositivos que no son el destinatario.
- **Inseguridad:** Cualquier equipo conectado al hub puede capturar (sniffear) el tráfico de los demás, ya que todos los paquetes llegan a todos.
 
El switch resolvió todos estos problemas y por eso lo reemplazó completamente.
 
### Conceptos clave mencionados
 
- **IP (Internet Protocol):** Dirección lógica que identifica un dispositivo en una red. Es lo que usa el router para enrutar paquetes. Ejemplo: `192.168.1.10` o `200.14.236.1`.
- **MAC (Media Access Control):** Dirección física grabada en la tarjeta de red de cada dispositivo. Es lo que usa el switch para dirigir tramas. Ejemplo: `AA:BB:CC:DD:EE:FF`.
- **Broadcast:** Envío de datos a todos los dispositivos de una red. El hub siempre opera en broadcast; el switch lo hace solo cuando no conoce la MAC de destino.
 
---
## 3. Conexión directa con desarrollo (CLAVE)
 
### ¿Qué rol cumple el router cuando haces una petición a una API?
 
Cuando tu frontend ejecuta:
 
```javascript
fetch("https://api.miapp.com/users")
```
 
Ocurre lo siguiente:
 
1. El **DNS** resuelve `api.miapp.com` a una dirección IP (ej: `104.26.10.50`).
2. Tu computador envía el paquete al **router de tu red local** (tu gateway).
3. Ese router examina la IP de destino y lo reenvía al **router de tu ISP**.
4. El paquete pasa por **múltiples routers** en Internet (cada uno decidiendo el mejor camino).
5. Finalmente llega al **router del data center** donde está alojado tu backend.
6. El router del data center entrega el paquete al servidor correcto.
 
**Sin routers, tu `fetch()` nunca saldría de tu red local.**
 
### ¿Por qué un switch es importante en un backend o data center?
 
En un data center, el switch es el que conecta internamente:
 
- Tu **servidor de aplicación** (donde corre tu backend en Java/Node/Python).
- Tu **base de datos** (PostgreSQL, MySQL, MongoDB).
- Tu **servidor de caché** (Redis, Memcached).
- Tu **load balancer**.
- Tu **servidor de archivos** o storage.
 
Todos estos servicios se comunican entre sí a través de switches. Si un switch falla o se satura, la comunicación interna se corta, aunque cada servidor individual esté funcionando perfectamente.
 
### ¿Qué problemas de red podrían afectar tu app aunque tu código esté correcto?
 
| Problema de red | Efecto en tu aplicación |
|----------------|------------------------|
| **Alta latencia** entre servicios | Timeouts en requests a la API o a la base de datos |
| **Pérdida de paquetes** | Respuestas incompletas, retransmisiones TCP, lentitud |
| **DNS mal configurado** | El dominio `api.miapp.com` no resuelve a ninguna IP |
| **Tabla de rutas incorrecta** en el router | Los paquetes no llegan al servidor destino |
| **Switch saturado** en el data center | Cuello de botella en la comunicación entre backend y DB |
| **Firewall bloqueando puertos** | La conexión se rechaza aunque el servidor esté escuchando |
 
**Como developer, tu código puede estar 100% correcto y aún así la aplicación falla si la red tiene problemas.**
 

---

 
## 4. Caso práctico real
 
### Escenario
 
> "Tu aplicación está en producción, pero los usuarios no pueden acceder."
 
### ¿Podría ser problema de router? ¿Por qué?
 
**Sí.** Si el router del data center tiene una ruta mal configurada, los paquetes de los usuarios nunca llegan al servidor. También puede ser un problema del ISP (un fallo en el enrutamiento BGP puede dejar tu servidor inaccesible desde ciertas regiones).
 
**Cómo diagnosticarlo:**
```bash
traceroute api.miapp.com
```
Si el traceroute muestra que los paquetes se pierden en un salto intermedio (timeouts con `* * *`), el problema es de enrutamiento.
 
### ¿Podría ser problema de switch? ¿Por qué?
 
**Sí.** Si el switch al que está conectado tu servidor falla, el servidor queda **aislado de la red interna** del data center. Está encendido y ejecutando código, pero nadie puede comunicarse con él.
 
**Cómo diagnosticarlo:**
```bash
# Desde otro servidor en la misma red del data center:
ping 192.168.1.50   # IP interna de tu servidor
```
Si no responde al ping desde la misma red local, probablemente es un problema del switch o del cable de red.
 
### ¿Cómo distinguir si es problema de red o de código?
 
| Paso | Comando | Si funciona... | Si falla... |
|------|---------|---------------|-------------|
| 1. Verificar conectividad básica | `ping api.miapp.com` | La red funciona, seguir al paso 2 | Problema de **red/DNS** |
| 2. Verificar ruta de red | `traceroute api.miapp.com` | Los paquetes llegan, seguir al paso 3 | Problema de **enrutamiento** |
| 3. Verificar que el servicio responde | `curl -I https://api.miapp.com` | El servidor web responde, seguir al paso 4 | Problema de **servidor/firewall** |
| 4. Verificar respuesta de la app | `curl https://api.miapp.com/health` | Todo OK, revisar frontend | Problema de **código/aplicación** |
| 5. Revisar logs | `docker logs mi-app` | Buscar errores específicos | El error está en los logs |
 
**Regla general:** Si `ping` y `curl` funcionan pero la app no, es código. Si `ping` falla, es red.
 