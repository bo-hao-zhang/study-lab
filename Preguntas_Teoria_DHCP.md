# 🧠 Banco de Estudio: 33 Preguntas y Respuestas de Teoría DHCP

> **Instrucciones de estudio:** Las respuestas están ocultas bajo pestañas desplegables (`<details>`). Intenta responder mentalmente o por escrito a cada pregunta antes de desplegar la solución para comprobar tu nivel de asimilación.

---

## Bloque 1: Fundamentos y Conceptos Básicos (Preguntas 1 a 5)

### 1. ¿Qué significan las siglas DHCP y cuál es su objetivo principal en una red?
<details>
<summary><b>Mostrar respuesta</b></summary>

* **Significado:** *Dynamic Host Configuration Protocol* (Protocolo de Configuración Dinámica de Host).
* **Objetivo:** Automatizar y centralizar la asignación y gestión de parámetros de configuración de red TCP/IP (dirección IP, máscara, gateway, DNS, etc.) a los clientes, eliminando la necesidad de configuración manual equipo por equipo.
</details>

---

### 2. ¿En qué capa del modelo OSI/TCP-IP opera DHCP y qué protocolo/puertos de transporte utiliza?
<details>
<summary><b>Mostrar respuesta</b></summary>

* **Capa:** Capa de **Aplicación**.
* **Protocolo de transporte:** **UDP** (*User Datagram Protocol*).
* **Puertos:**
  * **UDP 67:** Servidor DHCP (recibe peticiones).
  * **UDP 68:** Cliente DHCP (recibe ofertas y confirmaciones).
</details>

---

### 3. ¿El servidor DHCP debe estar obligatoriamente en la misma red local que los clientes? Justifica tu respuesta.
<details>
<summary><b>Mostrar respuesta</b></summary>

**No.** Aunque las peticiones iniciales del cliente son mensajes de difusión (*broadcast*) que los routers no propagan por defecto, un servidor DHCP puede estar en otra subred o red remota si se configura un agente de retransmisión (*DHCP Relay Agent* o `ip helper-address`), el cual convierte el broadcast del cliente en un unicast dirigido al servidor DHCP remoto.
</details>

---

### 4. ¿Qué concepto define la relación temporal entre un cliente DHCP y su dirección IP asignada?
<details>
<summary><b>Mostrar respuesta</b></summary>

El concepto de **concesión o alquiler (*lease*)**. El cliente no es "propietario" de la dirección IP, sino que la tiene "alquilada" durante un periodo determinado acordado con el servidor.
</details>

---

### 5. ¿Cómo sabe un cliente DHCP que una respuesta que viaja por la red es específicamente para él si todavía no tiene una dirección IP asignada?
<details>
<summary><b>Mostrar respuesta</b></summary>

Por medio de su **dirección física o dirección MAC**. El cliente incluye su MAC en la cabecera de la trama Ethernet y en el payload del mensaje DHCP (campo `chaddr` - *client hardware address*), lo que le permite identificar qué respuestas del servidor van dirigidas a su interfaz física.
</details>

---

## Bloque 2: Parámetros de Configuración (Preguntas 6 a 8)

### 6. ¿Cuáles son los parámetros de red básicos y obligatorios que asigna un servidor DHCP?
<details>
<summary><b>Mostrar respuesta</b></summary>

1. **Dirección IP del cliente.**
2. **Máscara de subred (*subnet mask*).**
3. **Tiempos de concesión (*lease time*, *renewal time* y *rebinding time*).**
</details>

---

### 7. ¿Cuáles son los parámetros opcionales más habituales que puede proporcionar DHCP?
<details>
<summary><b>Mostrar respuesta</b></summary>

1. **Puerta de enlace predeterminada (*Default Gateway* / `option routers`).**
2. **Servidores de nombres de dominio (*DNS Servers* / `option domain-name-servers`).**
3. **Nombre o sufijo de dominio DNS (`option domain-name`).**
</details>

---

### 8. ¿Puede un cliente comunicarse en su red local si el servidor DHCP solo le entrega IP y máscara pero no Gateway ni DNS?
<details>
<summary><b>Mostrar respuesta</b></summary>

**Sí.** Con la IP y la máscara de subred tiene todo lo necesario para comunicarse dentro de su propio dominio de difusión local (misma subred). Sin embargo, no podrá comunicarse con otras subredes/Internet (falta Gateway) ni resolver nombres mediante URLs tipo `google.com` (falta DNS).
</details>

---

## Bloque 3: Tipos de Asignación y Comparativas (Preguntas 9 a 13)

### 9. ¿En qué consiste la asignación manual o estática en DHCP (reserva)?
<details>
<summary><b>Mostrar respuesta</b></summary>

Consiste en vincular una dirección IP concreta a una **dirección MAC física fija**. Cada vez que ese dispositivo específico solicita red, el servidor siempre le entrega exactamente la misma IP.
</details>

---

### 10. ¿Cuándo es aconsejable utilizar asignación estática/reserva por MAC?
<details>
<summary><b>Mostrar respuesta</b></summary>

Se aconseja para dispositivos que ofrecen servicios y cuya IP no debería variar:
* Servidores internos e impresoras de red.
* Dispositivos de gestión de red (routers, switches, firewalls).
* Control de seguridad para evitar que dispositivos no autorizados obtengan configuración de red.
</details>

---

### 11. ¿En qué se diferencian la asignación automática y la asignación dinámica?
<details>
<summary><b>Mostrar respuesta</b></summary>

* **Asignación Automática:** Asigna una dirección IP de forma **permanente** la primera vez que el cliente la solicita. La IP se mantiene vinculada indefinidamente y no caduca, hasta que el cliente la libere explícitamente.
* **Asignación Dinámica:** Asigna una dirección IP de forma **temporal (*lease*)**. Si el cliente no renueva la concesión o se desconecta, la IP se recicla y vuelve al pool para ser usada por otro host.
</details>

---

### 12. ¿Qué es una asignación híbrida?
<details>
<summary><b>Mostrar respuesta</b></summary>

Es la combinación en una misma red de asignaciones manuales/estáticas (para servidores, impresoras o puestos fijos) junto con un rango de asignación dinámica (para puestos de usuario, portátiles o dispositivos móviles).
</details>

---

### 13. Menciona 3 ventajas de usar DHCP (automática/dinámica) frente a la configuración manual estática equipo por equipo.
<details>
<summary><b>Mostrar respuesta</b></summary>

1. **Ahorro de tiempo y esfuerzo administrativo:** Se configuran los parámetros una sola vez de forma centralizada en el servidor.
2. **Prevención de errores y duplicidades:** Evita conflictos de IP duplicadas y erratas humanas en máscaras o puertas de enlace.
3. **Facilita la movilidad:** Los dispositivos portátiles pueden cambiar de subred o edificio y configurarse de forma transparente y automática.
</details>

---

## Bloque 4: El Proceso DORA y Handshake (Preguntas 14 a 19)

### 14. ¿Qué representan las siglas del proceso DORA?
<details>
<summary><b>Mostrar respuesta</b></summary>

* **D:** `DHCPDISCOVER`
* **O:** `DHCPOFFER`
* **R:** `DHCPREQUEST`
* **A:** `DHCPACK`
</details>

---

### 15. ¿Qué dirección IP de origen y destino tiene el paquete `DHCPDISCOVER`?
<details>
<summary><b>Mostrar respuesta</b></summary>

* **IP de origen:** `0.0.0.0` (porque el cliente aún no posee ninguna dirección IP).
* **IP de destino:** `255.255.255.255` (Broadcast limitado a la red local).
</details>

---

### 16. Si hay dos servidores DHCP activos en la misma subred, ¿cuántos mensajes `DHCPOFFER` recibirá el cliente?
<details>
<summary><b>Mostrar respuesta</b></summary>

Recibirá **dos mensajes `DHCPOFFER`** (uno de cada servidor). Cada servidor propondrá una dirección IP disponible de su respectivo pool.
</details>

---

### 17. Ante múltiples ofertas (`DHCPOFFER`), ¿cuál suele escoger el cliente por defecto?
<details>
<summary><b>Mostrar respuesta</b></summary>

Por lo general, el cliente acepta **la primera oferta válida que llega a su interfaz de red**.
</details>

---

### 18. ¿Por qué el mensaje `DHCPREQUEST` se envía como Broadcast si el cliente ya sabe a qué servidor elegirá?
<details>
<summary><b>Mostrar respuesta</b></summary>

Porque al enviarlo por difusión (*broadcast*):
1. Notifica formalmente al **servidor elegido** de que acepta su oferta.
2. Notifica simultáneamente a los **demás servidores no elegidos** para que cancelen sus ofertas y devuelvan esas direcciones IP a sus pools de disponibles.
</details>

---

### 19. ¿Qué contiene el mensaje `DHCPACK` enviado por el servidor?
<details>
<summary><b>Mostrar respuesta</b></summary>

Es la confirmación final de la concesión. Contiene la dirección IP concedida, la máscara de subred, el tiempo de concesión (*lease time*), la IP del servidor DHCP y todas las opciones configuradas (puerta de enlace, DNS, etc.).
</details>

---

## Bloque 5: Tipos de Mensajes DHCP (Preguntas 20 a 24)

### 20. ¿Cuándo y por qué envía un servidor DHCP el mensaje `DHCPNAK` (o `DHCPNACK`)?
<details>
<summary><b>Mostrar respuesta</b></summary>

Envía un `DHCPNAK` (*Negative Acknowledge*) cuando:
* El cliente solicita una IP que ya no es válida o que pertenece a otra subred.
* La concesión que intenta renovar ya ha expirado o ha sido asignada a otro equipo.
* Al recibir el `DHCPNAK`, el cliente debe reiniciar inmediatamente el proceso desde cero con un `DHCPDISCOVER`.
</details>

---

### 21. ¿Qué es y cuándo se envía un mensaje `DHCPDECLINE`?
<details>
<summary><b>Mostrar respuesta</b></summary>

Lo envía el **cliente** al servidor cuando detecta (habitualmente mediante una comprobación ARP) que la dirección IP que el servidor le acaba de conceder en el `DHCPACK` **ya está en uso por otra máquina en la red**.
</details>

---

### 22. ¿Qué función cumple el mensaje `DHCPRELEASE` y quién lo envía?
<details>
<summary><b>Mostrar respuesta</b></summary>

Lo envía el **cliente** al servidor para renunciar voluntariamente a su dirección IP antes de que expire el tiempo de alquiler (por ejemplo, al apagar el equipo correctamente o desconectar la interfaz con `ipconfig /release` o `dhclient -r`).
</details>

---

### 23. ¿Para qué se utiliza el mensaje `DHCPINFORM`?
<details>
<summary><b>Mostrar respuesta</b></summary>

Lo envía un cliente que **ya tiene una dirección IP asignada manualmente o estática**, pero necesita consultar al servidor DHCP para obtener únicamente parámetros adicionales de configuración local (por ejemplo, servidores DNS, rutas estáticas o servidores proxy).
</details>

---

### 24. Resume en una frase la función de cada uno de los 8 mensajes DHCP.
<details>
<summary><b>Mostrar respuesta</b></summary>

1. **`DHCPDISCOVER`:** El cliente busca servidores DHCP en la red.
2. **`DHCPOFFER`:** El servidor propone una configuración e IP al cliente.
3. **`DHCPREQUEST`:** El cliente acepta la oferta o solicita renovar su IP.
4. **`DHCPACK`:** El servidor confirma la asignación y envía los parámetros definitivos.
5. **`DHCPNAK`:** El servidor rechaza la petición del cliente.
6. **`DHCPDECLINE`:** El cliente rechaza la IP por colisión detectada.
7. **`DHCPRELEASE`:** El cliente libera y devuelve la IP al servidor.
8. **`DHCPINFORM`:** El cliente solicita opciones extra teniendo ya una IP previa.
</details>

---

## Bloque 6: Tiempos de Concesión y Renovación (Preguntas 25 a 27)

### 25. ¿Qué es el tiempo de renovación ($T_1$ o *Renewal Time*) y cómo se comporta el cliente cuando se alcanza?
<details>
<summary><b>Mostrar respuesta</b></summary>

* Ocurre por defecto al **50% del tiempo total de concesión (*lease time*)**.
* El cliente envía un `DHCPREQUEST` por **Unicast** dirigido directamente al servidor que le otorgó la IP para solicitar la extensión del contrato sin interrumpir la conexión.
</details>

---

### 26. ¿Qué es el tiempo de reconexión ($T_2$ o *Rebinding Time*) y en qué se diferencia de $T_1$?
<details>
<summary><b>Mostrar respuesta</b></summary>

* Ocurre por defecto al **87.5% (7/8) del tiempo total de concesión**.
* Se produce si el servidor original no contestó en $T_1$ (servidor caído o apagado).
* **Diferencia clave:** En $T_2$ el cliente ya no envía unicast al servidor original, sino que envía un `DHCPREQUEST` por **Broadcast** a cualquier servidor DHCP activo en la red para intentar renovar la concesión antes de que expire.
</details>

---

### 27. ¿Qué ocurre exactamente si expira el 100% del *lease time* sin haber recibido respuesta de renovación?
<details>
<summary><b>Mostrar respuesta</b></summary>

El cliente **pierde el derecho a usar la dirección IP**, debe desconfigurar la interfaz de inmediato (cortando las comunicaciones de red) y reiniciar el proceso completo desde el principio enviando un `DHCPDISCOVER`.
</details>

---

## Bloque 7: Detección de Conflictos y Seguridad (Preguntas 28 a 29)

### 28. ¿Qué protocolo utiliza el cliente tras recibir el `DHCPACK` para garantizar que no haya direcciones duplicadas?
<details>
<summary><b>Mostrar respuesta</b></summary>

Utiliza el protocolo **ARP** (*Address Resolution Protocol*), concretamente un **Gratuitous ARP / ARP Probe**. Emite una pregunta en broadcast preguntando quién tiene la IP recién asignada; si nadie responde, la asume como válida. Si alguien responde, envía un `DHCPDECLINE`.
</details>

---

### 29. En redes con alta rotación de clientes (por ejemplo, una cafetería o estación de tren), ¿cómo conviene configurar el tiempo de concesión (*lease time*) y por qué?
<details>
<summary><b>Mostrar respuesta</b></summary>

Conviene configurarlo con un **tiempo bajo (por ejemplo, entre 10 y 30 minutos)**.  
**Motivo:** Evita el agotamiento del pool de direcciones IP. Si se usara un tiempo largo (como 24 horas), los clientes que solo se conectan un momento retendrían las IPs durante todo el día, impidiendo que nuevos usuarios obtengan conexión.
</details>

---

## Bloque 8: Configuración en Linux (`dhcpd.conf`) (Preguntas 30 a 33)

### 30. ¿Para qué sirve la directiva `authoritative;` en el archivo `dhcpd.conf`?
<details>
<summary><b>Mostrar respuesta</b></summary>

Indica que el servidor DHCP es la **autoridad oficial y legítima** para esa red. Si un cliente solicita una configuración errónea o desfasada (por ejemplo, tras conectarse a otra red), el servidor tiene potestad para enviar un `DHCPNAK` de inmediato, forzando al cliente a olvidar la IP incorrecta y solicitar una nueva válida.
</details>

---

### 31. ¿Qué directiva define el rango de direcciones IP disponibles para asignación dinámica y dentro de qué bloque se define?
<details>
<summary><b>Mostrar respuesta</b></summary>

* **Directiva:** `range <IP_inicio> <IP_fin>;` (por ejemplo: `range 192.168.1.50 192.168.1.100;`).
* **Bloque:** Debe declararse dentro de una declaración de subred:
  ```nginx
  subnet 192.168.1.0 netmask 255.255.255.0 {
      range 192.168.1.50 192.168.1.100;
  }
  ```
</details>

---

### 32. ¿Cuál es la sintaxis exacta para configurar una reserva estática por MAC para un equipo llamado `srv-web` con IP `192.168.1.10` y MAC `00:11:22:33:44:55`?
<details>
<summary><b>Mostrar respuesta</b></summary>

```nginx
host srv-web {
    hardware ethernet 00:11:22:33:44:55;
    fixed-address 192.168.1.10;
}
```
</details>

---

### 33. ¿Qué diferencia hay entre las directivas `default-lease-time` y `max-lease-time`?
<details>
<summary><b>Mostrar respuesta</b></summary>

* **`default-lease-time <segundos>;`:** Es la duración del alquiler asignada por defecto a un cliente que no especifica cuánto tiempo solicita en su petición.
* **`max-lease-time <segundos>;`:** Es el límite máximo absoluto de tiempo de concesión que el servidor permitirá, incluso si un cliente solicita expresamente una duración mayor en su mensaje DHCP.
</details>
