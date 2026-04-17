Fase 1 — Un sol node WEB

En esta fase inicial se ha creado un contenedor con Apache (httpd) que sirve una página web estática.
He ha montado un volumen local (./web) para almacenar el contenido web, permitiendo modificar los archivos sin reconstruir el contenedor.
Se ha expuesto el puerto 8080 para acceder al servicio desde el navegador.

Captura de la web funcionando.
<img width="810" height="866" alt="imagen" src="https://github.com/user-attachments/assets/71f9291e-f2c1-469c-8fc8-76a2332561b9" />


Fase 2 — Segon node web

Se ha añadido un segundo contenedor Apache con la misma configuración que el primero. 
Aqui he creado el entorno para el balanceo de carga, teniendo múltiples servidores backend.
Para poder diferenciar qué servidor responde a cada petición, se ha modificado el contenido web.

Ambos servidores funcionan correctamente y responden peticiones.
<img width="820" height="142" alt="imagen" src="https://github.com/user-attachments/assets/cf4a6fdc-d14e-42e3-afce-56a86ca1253c" />
<img width="806" height="222" alt="imagen" src="https://github.com/user-attachments/assets/e11e38b3-2fa9-4b62-87e0-8e850e8733d0" />
<img width="524" height="161" alt="imagen" src="https://github.com/user-attachments/assets/76f750f6-5a3e-4110-a636-0cd514e44522" />

Fase 3 — Volum compartit.

Se ha configurado un volumen compartido entre ambos contenedores Apache:
./web:/usr/local/apache2/htdocs/
Esto permite que ambos servidores sirvan exactamente el mismo contenido web, garantizando consistencia.
Resultado:
Cualquier cambio en el contenido se refleja automáticamente en ambos servidores.

Evidencia: ambos nodos muestran el mismo contenido.


⚙️ Fase 4 — Proxy inverso y balanceo de carga

Se ha añadido un contenedor Nginx como punto de entrada único al sistema.

Se ha configurado como proxy inverso utilizando:

upstream para definir los servidores backend
proxy_pass para redirigir las peticiones

El balanceo de carga se realiza mediante el algoritmo Round Robin (por defecto en Nginx).

✔️ Resultado:
Las peticiones se distribuyen entre los dos servidores Apache.

📸 Evidencia:

Varias peticiones mostrando respuestas de distintos servidores
Uso de curl o navegador para comprobar alternancia
⚙️ Fase 5 — Memoria caché en Nginx

Se ha implementado caché en el proxy inverso mediante:

proxy_cache_path
proxy_cache
add_header X-Cache

Esto permite almacenar respuestas y mejorar el rendimiento.

Se ha verificado el funcionamiento mediante las cabeceras HTTP:

MISS: la respuesta no estaba en caché
HIT: la respuesta se sirve desde caché

✔️ Resultado:
El sistema de caché funciona correctamente.

📸 Evidencia:

Respuesta con X-Cache: MISS
Posterior respuesta con X-Cache: HIT
1. WEB FUNCIONANDO 
<img width="810" height="866" alt="imagen" src="https://github.com/user-attachments/assets/71f9291e-f2c1-469c-8fc8-76a2332561b9" />
2. BALANCEO
<img width="806" height="222" alt="imagen" src="https://github.com/user-attachments/assets/e11e38b3-2fa9-4b62-87e0-8e850e8733d0" />
<img width="524" height="161" alt="imagen" src="https://github.com/user-attachments/assets/76f750f6-5a3e-4110-a636-0cd514e44522" />
3.CACHE
<img width="550" height="352" alt="imagen" src="https://github.com/user-attachments/assets/755c8d3c-ab23-4e62-b24b-9666a30b8e8a" />
