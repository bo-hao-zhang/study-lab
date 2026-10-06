# 📚 Study Lab — Sistemes i Xarxes (ASIX)

[![Institut TIC de Barcelona](https://img.shields.io/badge/Institut_TIC_de_Barcelona-ASIX_%2F_ASIR-lightgrey?style=flat)](https://agora.xtec.cat/itb/)
[![Ubuntu](https://img.shields.io/badge/Linux-Ubuntu_Server_24.04-E95420?style=flat&logo=ubuntu&logoColor=white)](https://ubuntu.com/)
[![Focus](https://img.shields.io/badge/Focus-NOC_%2F_Sysadmin_%2F_Networking-blue?style=flat)]()

Repositorio centralizado de laboratorios prácticos, configuraciones de infraestructura, evidencias y guías de estudio del ciclo formativo de grado superior en **Administració de Sistemes Informàtics en Xarxa (ASIX)** en el **Institut TIC de Barcelona**.

---

## 🗂️ Módulos y Laboratorios

### 🌐 M06 — Sistemes Operatius en Xarxa (SXI)

#### 🔹 [📡 ISC-DHCP Server Suite (Laboratorio Completo)](dhcp-server-lab/README.md)
Despliegue integral de un servidor DHCP autoritativo en entorno virtualizado aislado (L2) con **IsardVDI**, configuración de pools dinámicos, políticas de seguridad anti-DoS, resolución de nombres y reservas fijas por dirección física (MAC).

* 📄 **Writeup Principal & Evidencias:** [dhcp-server-lab/README.md](dhcp-server-lab/README.md) *(con capturas reales del ciclo DORA, sockets y base de datos de leases)*.
* ⚙️ **Configuraciones listas:** [dhcp-server-lab/configs/](dhcp-server-lab/configs/) (`dhcpd.conf`, `iface-enp3s0.yaml`, `isc-dhcp-server`).
* 📚 **Documentación técnica:**
  * 📋 [Guia Pràctica de Comandes (Català)](dhcp-server-lab/docs/GUIA_PRACTICA_P1.md) — Para ejecución y entrega de clase.
  * 🧠 [Manual Teórico de Ingeniería: De Cimiento a Carretera (P0 + P1)](dhcp-server-lab/docs/MANUAL_TEORICO_INGENIERIA.md) — Fundamentos desde KVM y kernel hasta RFC 2131.
  * ❓ [Preguntas y Respuestas Teóricas](dhcp-server-lab/docs/PREGUNTAS_EXAMEN_DHCP.md) — 10 preguntas obligatorias de examen.
  * 📑 [Laboratorio Previo (Práctica 0)](dhcp-server-lab/docs/P0_MANUAL_PRACTICA_DHCP.md) — Setup elemental y reservas iniciales.
* 📂 **Material original de clase:** [dhcp-server-lab/classroom_materials/](dhcp-server-lab/classroom_materials/) *(PDFs y enunciados)*.

---

## 🛠️ Entorno y Stack Tecnológico

* **Hipervisor & Virtualización:** IsardVDI (KVM / QEMU / Linux Bridges)
* **Sistemas Operativos:** Ubuntu Server 24.04 LTS (CLI)
* **Gestión de Red L3/L4:** Netplan (`systemd-networkd`), ISC-DHCP (`dhcpd`)
* **Herramientas de diagnóstico & auditoría:** `iproute2`, `ss`, `journalctl`, `dhclient`, `resolvectl`

---
*Mantenido por **Bo Hao Zhang**.*
