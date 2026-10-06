# 📚 Study Lab — Sistemes i Xarxes (ASIX)

[![Institut TIC de Barcelona](https://img.shields.io/badge/Institut_TIC_de_Barcelona-ASIX_%2F_ASIR-lightgrey?style=flat)](https://agora.xtec.cat/itb/)
[![Ubuntu](https://img.shields.io/badge/Linux-Ubuntu_Server_24.04-E95420?style=flat&logo=ubuntu&logoColor=white)](https://ubuntu.com/)
[![Focus](https://img.shields.io/badge/Focus-NOC_%2F_Sysadmin_%2F_Networking-blue?style=flat)]()

Repositorio de prácticas de laboratorio, configuraciones de infraestructura y documentación técnica correspondiente al ciclo formativo de grado superior en **Administració de Sistemes Informàtics en Xarxa (ASIX)** en el **Institut TIC de Barcelona**.

---

## 🗂️ Índice de Módulos y Laboratorios

### 🌐 M06 — Sistemes Operatius en Xarxa (SXI)

* **[Pràctica 0: Servidor DHCP Bàsic](practiques/practica_0/MANUAL_PRACTICA_DHCP.md)**
  * Configuración inicial de `isc-dhcp-server` en subred `192.168.1.0/24`.
  * Asignación de rangos dinámicos, exclusión de IPs de infraestructura y reserva básica `pc1`.

* **[Pràctica 1: Desplegament de Servidor DHCP Autoritatiu i Aïllament L2](practiques/practica_1/README.md)**  ⭐ *(Destacado con evidencias)*
  * Servidor autoritativo con prevención de denegación de servicio (`deny declines; deny bootp;`).
  * Asignación de IP estática con Netplan y amarre de interfaces del demonio.
  * Jerarquía de directivas y sobreescritura de parámetros por Host (Isabel y Fernando).
  * **Documentación complementaria:**
    * 📋 [Guia Pràctica de Comandes (Català)](practiques/practica_1/GUIA_PRACTICA_P1_DHCP.md)
    * 🧠 [Manual Teórico de Ingeniería: De Cimiento a Carretera (P0 + P1)](practiques/practica_1/MANUAL_TEORICO_DHCP_P0_P1.md)
    * ❓ [Preguntes Teòriques de la Pràctica](practiques/practica_1/PREGUNTES_TEORIA_DHCP.md)

---

## 🛠️ Entorno de Laboratorio

* **Hipervisor:** IsardVDI (KVM/QEMU)
* **Sistemas Operativos:** Ubuntu Server 24.04 LTS (CLI)
* **Gestión de Red:** Netplan (`systemd-networkd`) & ISC DHCP (`dhcpd`)
* **Herramientas de diagnóstico:** `ss`, `journalctl`, `iproute2`, `dhclient`, `resolvectl`

---
*Mantenido por **Bo Hao Zhang**.*
