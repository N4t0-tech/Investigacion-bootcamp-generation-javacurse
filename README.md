# Fundamentos de Redes — Investigación Guiada
 
> **Curso:** Full Stack Java Developer — Generation Chile  
> **Actividad:** Fundamentos de Redes  
> **Autor de la actividad:** Guido Pérez
 
---

## 1. ¿Qué es cada dispositivo?
 
| Concepto | Definición simple | Nivel técnico (breve) | Ejemplo real |
|----------|------------------|-----------------------|--------------|
| **Router** | Dispositivo que conecta redes diferentes entre sí y decide por dónde enviar los datos hacia su destino | Opera en **capa 3 (Red)** del modelo OSI. Usa **direcciones IP** y tablas de enrutamiento para determinar la mejor ruta para cada paquete entre redes distintas | El router WiFi de tu casa que conecta tu red local a Internet, o los routers de un ISP que conectan ciudades |
| **Switch** | Dispositivo que conecta varios equipos dentro de una misma red y envía los datos únicamente al dispositivo correcto | Opera en **capa 2 (Enlace de datos)**. Usa **direcciones MAC** y una tabla CAM para reenviar tramas solo al puerto donde está el destinatario | Un switch en el rack de un data center que interconecta 24 servidores en la misma red local |
| **Hub** | Dispositivo que conecta equipos en red pero reenvía todo lo que recibe a todos los puertos sin distinción | Opera en **capa 1 (Física)**. No tiene inteligencia: simplemente replica la señal eléctrica por todos sus puertos (**broadcast**) | Dispositivo usado en redes pequeñas de los años 90, hoy prácticamente reemplazado por completo por switches |
 
---