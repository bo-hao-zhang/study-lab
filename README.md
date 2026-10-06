# 📚 Study Lab — Sistemes i Xarxes (ASIX)

[![Institut TIC de Barcelona](https://img.shields.io/badge/Institut_TIC_de_Barcelona-ASIX_%2F_ASIR-lightgrey?style=flat)](https://agora.xtec.cat/itb/)
[![Ubuntu](https://img.shields.io/badge/Linux-Ubuntu_Server_24.04-E95420?style=flat&logo=ubuntu&logoColor=white)](https://ubuntu.com/)
[![Focus](https://img.shields.io/badge/Focus-NOC_%2F_Sysadmin_%2F_Networking-blue?style=flat)]()

Repositorio de laboratorios prácticos de infraestructura, redes y sistemas correspondientes a **2º de ASIX** en el **Institut TIC de Barcelona**.

---

## 🗂️ Laboratorios de Infraestructura

### 🌐 [📡 Despliegue de Servidor DHCP Autoritativo (`isc-dhcp-server`)](dhcp-server-lab/)
Despliegue y verificación en entorno virtualizado aislado (L2) con **IsardVDI**:
* **¿Qué es?:** Servidor DHCP autoritativo en Ubuntu Server 24.04 con pools dinámicos y reservas por hardware (MAC).
* **¿Por qué?:** Aislamiento estricto de Capa 2 para evitar problemas de *Rogue DHCP* en la red física, políticas de lease y protección anti-DoS (`deny declines; deny bootp;`).
* **¿Cómo?:** Netplan estático en servidor, amarre de sockets en `/etc/default/isc-dhcp-server` y reglas en `/etc/dhcp/dhcpd.conf`.
* **Resultados:** Trazabilidad completa del ciclo DORA en cliente CLI, inspección de sockets UDP 67 y validación de concesiones en disco.

👉 **[Ver Informe Completo con Evidencias en `dhcp-server-lab/`](dhcp-server-lab/)**

---

*Mantenido por **Bo Hao Zhang**.*
