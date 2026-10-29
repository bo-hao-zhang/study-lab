# 🌐 Guia Oficial Pas a Pas: Pràctica 4 - Kea DHCP4 (Fase 2: VLANs i DHCP Relay)

> **Mòdul:** M06 / MP07 – Sistemes Operatius en Xarxa / Serveis de Xarxa (ASIX 2n)  
> **Unitat:** Sessió 2 - VLANs i DHCP Relay  
> **Font Oficial:** [Pràctica_4__Exercicis_Lab_Kea_DHCP4__Fase_2_.pdf](file:///c:/Users/bohao/Desktop/sxi/clase/practicas/practica4/Pràctica_4__Exercicis_Lab_Kea_DHCP4__Fase_2_.pdf)  
> **Entorn:** IsardVDI (Ubuntu 22.04 LTS / `~/kea-labs-main`)  
> **Alumne:** Bo Hao Zhang  

---

## 🧭 1. Context: Què estem fent en aquest laboratori?

A la vida real, els servidors no estan connectats físicament a totes les VLANs dels usuaris.
1. Tenim **1 sol servidor Kea DHCP** a una xarxa interna de servidors anomenada **Backend** (`10.50.1.0/24`).
2. Tenim **3 VLANs d'usuaris** aïllades:
   - **VLAN 10:** `10.50.10.0/24`
   - **VLAN 20:** `10.50.20.0/24`
   - **VLAN 30:** `10.50.30.0/24`
3. Com que els clients fan broadcast (`255.255.255.255`) i el broadcast no surt de la seva VLAN, necessitem un intermediari: el **DHCP Relay**.
4. El Relay escolta el broadcast a la VLAN per la targeta `eth2`, l'empaqueta i l'envia per unicast a Kea per la targeta `eth1` (Backend). Al paquet hi posa el camp **`giaddr`** (la IP del relay a la VLAN, ex: `10.50.10.1`), perquè Kea sàpiga quina subxarxa ha d'assignar.

---

## 📸 Checklist Oficial de Captures d'Evidència

| # | Tasca / Apartat | Què ha de mostrar la captura a l'informe |
| :-: | :--- | :--- |
| 📸 **0** | **Canvi de Hostname** | Execució de `hostnamectl` mostrant `pc-bohao`. |
| 📸 **1** | **Desplegament Topologia** | Taula de Containerlab o `sudo docker ps` amb tots els nodes actius (*Up / healthy*). |
| 📸 **2** | **Tasca 1: Mapa d'IPs** | Execució de les comandes de verificació d'IPs de KEA i els 3 RELAYS. |
| 📸 **3** | **Tasca 2: Tràfic tcpdump** | El tràfic interceptat mostrant el Discover original i el Discover reenviat amb `giaddr`. |
| 📸 **4** | **Tasca 2: Autocorrecció** | Comprovació de la IP obtinguda pel client VLAN 10 i el registre a `kea-leases4.csv`. |
| 📸 **5** | **Repte: Diagnòstic Relay** | Sortida de `ping 10.50.1.99` fallant i la regla de `iptables -t nat -L OUTPUT`. |
| 📸 **6** | **Repte: Resolució Relay** | Client VLAN 20 obtenint correctament la IP `10.50.20.X` un cop esborrada la regla. |

---

## 🛠️ 2. Execució Pas a Pas de la Pràctica

### Pas 0: Canvi de nom de màquina i Desplegament

1. **Modificar el hostname de la teva màquina Isard:**
   ```bash
   sudo hostnamectl set-hostname pc-bohao
   exec bash
   ```
   > 📸 **CAPTURA 0:** Mostra el prompt amb `isard@pc-bohao:~$`.

2. **Entrar al directori del projecte:**
   ```bash
   cd ~/kea-labs-main
   ```

3. **Desplegar la fase 2:**
   ```bash
   sudo ./scripts/deploy.sh vlans
   ```
   *(Aquesta és la comanda exacta del PDF. També serveix `./scripts/deploy.sh 2`).*

   > 📸 **CAPTURA 1:** Mostra la taula final de Containerlab amb tots els contenidors en estat `running`.

---

### Tasca 1: Mapa de Xarxa (Obtenció d'IPs)

Executa les 4 comandes oficials per veure les adreces IP de la teva infraestructura:

```bash
echo "=== KEA ===" && sudo docker exec clab-kea-lab-vlans-kea ip -4 addr show eth1 | grep inet
echo "=== RELAY VLAN10 ===" && sudo docker exec clab-kea-lab-vlans-relay-vlan10 ip -4 addr | grep inet
echo "=== RELAY VLAN20 ===" && sudo docker exec clab-kea-lab-vlans-relay-vlan20 ip -4 addr | grep inet
echo "=== RELAY VLAN30 ===" && sudo docker exec clab-kea-lab-vlans-relay-vlan30 ip -4 addr | grep inet
```

> 📸 **CAPTURA 2:** Fes captura de la sortida d'aquestes comandes a la terminal.

#### 📋 Taula Completa (Ja emplenada al teu Google Doc):

| Component | IP Backend (`eth1`) | IP VLAN (`eth2`) | Funció |
| :--- | :--- | :--- | :--- |
| **`kea`** | `10.50.1.10/24` | `-` | Servidor central DHCP4: rep les peticions dels relays, gestiona els pools de les 3 VLANs i concedeix les adreces IP. |
| **`relay-vlan10`** | `10.50.1.11/24` | `10.50.10.1/24` | DHCP Relay i Gateway VLAN 10: reenvia els broadcasts dels clients cap al servidor Kea. |
| **`relay-vlan20`** | `10.50.1.21/24` | `10.50.20.1/24` | DHCP Relay i Gateway VLAN 20: reenvia els broadcasts dels clients cap al servidor Kea. |
| **`relay-vlan30`** | `10.50.1.31/24` | `10.50.30.1/24` | DHCP Relay i Gateway VLAN 30: reenvia els broadcasts dels clients cap al servidor Kea. |

---

### Tasca 2: Flux de Paquets (tcpdump)

1. **Terminal 1: Posar a escoltar el tcpdump al relay de la VLAN 10:**
   ```bash
   sudo docker exec clab-kea-lab-vlans-relay-vlan10 sh -c "apk add tcpdump >/dev/null 2>&1; tcpdump -i any -n port 67 or port 68" &
   ```

2. **Terminal 2: Demanar IP des del client de la VLAN 10:**
   ```bash
   sudo docker exec clab-kea-lab-vlans-client-vlan10 udhcpc -i eth1 -n -q -v
   ```

   > 📸 **CAPTURA 3:** Fes captura del tràfic interceptat per `tcpdump`.

#### Valors identificats a la captura:
- **DISCOVER original (VLAN 10):**
  - Origen: `0.0.0.0:68`
  - Destí: `255.255.255.255:67`
- **DISCOVER reenviat pel Relay (Backend):**
  - Origen: `10.50.1.11:67`
  - Destí: `10.50.1.10:67`
- **Valor de `giaddr`:**
  - `10.50.10.1` (IP del relay a la interfície `eth2` de la VLAN 10).

3. **Autocorrecció:**
   Executa el bloc d'autocorrecció:
   ```bash
   echo "=== AUTOCORRECCIÓ ==="
   sudo docker exec clab-kea-lab-vlans-client-vlan10 ip -4 addr show eth1 | grep "10.50.10"
   sudo docker exec clab-kea-lab-vlans-kea cat /var/lib/kea/kea-leases4.csv | grep "10.50.10"
   ```
   > 📸 **CAPTURA 4:** Mostra que el client té IP del rang `10.50.10.100-200` i que apareix al fitxer CSV de Kea.

---

### 3. Repte: Troubleshooting de Relay (VLAN 20)

#### 1. Simular l'error:
```bash
sudo docker exec clab-kea-lab-vlans-relay-vlan20 sh -c "apk add iptables >/dev/null 2>&1; iptables -t nat -A OUTPUT -p udp -d 10.50.1.10 --dport 67 -j DNAT --to-destination 10.50.1.99"
```

#### 2. Executar les comandes de diagnòstic:
```bash
# T2.3.1: Provar de demanar IP (fallarà, no rep resposta)
sudo docker exec clab-kea-lab-vlans-client-vlan20 udhcpc -i eth1 -n -q -v

# T2.3.3: Verificar connectivitat
sudo docker exec clab-kea-lab-vlans-relay-vlan20 ping -c 2 10.50.1.10
sudo docker exec clab-kea-lab-vlans-relay-vlan20 ping -c 2 10.50.1.99

# T2.3.4: Mirar la regla NAT errònia
sudo docker exec clab-kea-lab-vlans-relay-vlan20 iptables -t nat -L OUTPUT -n -v
```
> 📸 **CAPTURA 5:** Mostra el ping a `10.50.1.99` fallant i la regla de DNAT que desvia el tràfic a aquesta IP.

#### 3. Respostes a les preguntes del repte:
1. **Quin és el problema?**  
   Hi ha una regla de DNAT a la taula `nat` d'`iptables` que redirigeix tot el tràfic DHCP (UDP port 67) destinat a Kea (`10.50.1.10`) cap a la IP inexistent `10.50.1.99`.
2. **Per què els clients no obtenen IP?**  
   Perquè els paquets DHCP del relay mai arriben al servidor Kea; es perden pel camí en anar destinats a una IP que no té cap host assignat.
3. **Proposa la solució:**  
   Eliminar la regla de DNAT de la cadena OUTPUT del relay.

#### 4. Resoldre l'error i verificar:
```bash
# Eliminar la regla errònia
sudo docker exec clab-kea-lab-vlans-relay-vlan20 iptables -t nat -D OUTPUT -p udp -d 10.50.1.10 --dport 67 -j DNAT --to-destination 10.50.1.99
sleep 5

# Demanar IP de nou des del client VLAN 20
sudo docker exec clab-kea-lab-vlans-client-vlan20 udhcpc -i eth1 -f -v

# Verificar la IP obtinguda
sudo docker exec clab-kea-lab-vlans-client-vlan20 ip -4 addr show eth1 | grep "10.50.20"
```
> 📸 **CAPTURA 6:** Mostra que ara el Client 2 sí obté una IP del segment `10.50.20.X`.

---

### 4. Repte Obert: Afegir una Nova VLAN (VLAN 40)

Requisits:
- Xarxa: `10.50.40.0/24`
- Pool: `10.50.40.100 - 10.50.40.200`
- Gateway: `10.50.40.1`
- IP relay backend: `10.50.1.23`

#### Pas 1: Afegir la subxarxa a `fase2-vlans/configs/kea/kea-dhcp4.conf`
Obre el fitxer:
```bash
nano fase2-vlans/configs/kea/kea-dhcp4.conf
```
Afegeix dins de la llista `"subnet4"`:
```json
{
    "id": 4,
    "subnet": "10.50.40.0/24",
    "pools": [ { "pool": "10.50.40.100 - 10.50.40.200" } ],
    "option-data": [
        { "name": "routers", "data": "10.50.40.1" },
        { "name": "domain-name-servers", "data": "8.8.8.8, 1.1.1.1" }
    ]
}
```

#### Pas 2: Afegir el bridge, relay i client a `fase2-vlans/topology.clab.yml`
Obre el fitxer:
```bash
nano fase2-vlans/topology.clab.yml
```
1. Sota de `nodes:` afegeix:
   ```yaml
       br-vlan40:
         kind: bridge

       relay-vlan40:
         kind: linux
         image: kea-relay:latest
         exec:
           - ip addr add 10.50.1.23/24 dev eth1
           - ip addr add 10.50.40.1/24 dev eth2

       client-vlan40:
         kind: linux
         image: nicolaka/netshoot:latest
   ```
2. Sota de `links:` afegeix:
   ```yaml
       - endpoints: ["relay-vlan40:eth1", "br-backend:eth4"]
       - endpoints: ["relay-vlan40:eth2", "br-vlan40:eth1"]
       - endpoints: ["client-vlan40:eth1", "br-vlan40:eth2"]
   ```

#### Pas 3: Redesplegar i verificar:
```bash
./scripts/destroy.sh vlans
sudo ./scripts/deploy.sh vlans

# Afegir ruta estàtica a Kea cap a la nova VLAN 40 via el relay 10.50.1.23:
sudo docker exec clab-kea-lab-vlans-kea ip route add 10.50.40.0/24 via 10.50.1.23

# Demanar IP des del nou client
sudo docker exec clab-kea-lab-vlans-client-vlan40 udhcpc -i eth1 -n -q -v
sudo docker exec clab-kea-lab-vlans-client-vlan40 ip -4 addr show eth1
```
*(Rebrà una IP del rang `10.50.40.X`).*

---

### Pas Final: Neteja
En acabar la sessió de laboratori:
```bash
./scripts/destroy.sh vlans
```
