# Fundamentos de Redes — Investigación Guiada
 
> **Curso:** Full Stack Java Developer — Generation Chile  
> **Actividad:** Fundamentos de Redes  
> **Autor de la actividad:** Renato Campos
 
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