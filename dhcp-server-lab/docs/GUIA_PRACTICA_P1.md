# Guia Pràctica Pas a Pas: Pràctica 1 DHCP (isc-dhcp-server)
**Institut TIC de Barcelona — M06 Sistemes Operatius en Xarxa**

Aquesta guia conté exclusivament les comandes, fitxers i comprovacions necessàries per executar i completar la Pràctica 1 al laboratori d'IsardVDI, utilitzant una màquina **Ubuntu Server** com a servidor DHCP i una màquina **Ubuntu Server / Debian CLI** com a client.

---

## 📌 Topologia del Laboratori a IsardVDI

* **Servidor (Ubuntu Server 24.04):**
  * `enp1s0`: Xarxa `Default` (SSH / Internet). **NO TOCAR MAI**.
  * `enp2s0`: Xarxa de gestió d'Isard.
  * `enp3s0`: Xarxa privada `Personal1`. IP estàtica: **`192.168.1.2/24`**.
* **Client (Ubuntu Server / Debian CLI):**
  * Interfície connectada a la xarxa `Personal1` (ex: `enp2s0` o `enp3s0`).
  * Configuració: Sol·licitud dinàmica per DHCP.

---

## 🚀 Pas a Pas al Servidor

### Pas 1: Fixar la IP estàtica a `enp3s0` (Netplan)
1. Comprova si el fitxer ja existeix o crea'l de nou:
   ```bash
   sudo nano /etc/netplan/iface-enp3s0.yaml
   ```
2. Assegura't que conté exactament aquestes línies (respectant els 2 espais de sagnat, sense tabuladors):
   ```yaml
   network:
     version: 2
     renderer: networkd
     ethernets:
       enp3s0:
         dhcp4: no
         addresses:
           - 192.168.1.2/24
   ```
3. Aplica la configuració i verifica que la IP està assignada:
   ```bash
   sudo netplan apply
   ip -br a show enp3s0
   ```
   *(Hauràs de veure `enp3s0 UP 192.168.1.2/24`).*

---

### Pas 2: Instal·lació de `isc-dhcp-server`
Actualitza el repositori i instal·la el paquet del servei:
```bash
sudo apt update
sudo apt install isc-dhcp-server -y
```
*(Si en finalitzar la instal·lació mostra un avís en vermell indicant que el servei ha fallat en iniciar, és completament normal perquè encara no té interfície ni configuració).*

---

### Pas 3: Indicar la interfície d'escolta (`enp3s0`)
Edita el fitxer de variables del servei:
```bash
sudo nano /etc/default/isc-dhcp-server
```
Vés al final del fitxer i assigna la interfície `enp3s0` a `INTERFACESv4`:
```bash
INTERFACESv4="enp3s0"
INTERFACESv6=""
```
*(Guarda amb `Ctrl+O`, `Enter` i surt amb `Ctrl+X`).*

---

### Pas 4: Còpia de seguretat del fitxer original
Fes una còpia de seguretat neta abans de modificar la configuració:
```bash
sudo cp /etc/dhcp/dhcpd.conf /etc/dhcp/dhcpd.conf.original
```

---

### Pas 5: Configuració de `/etc/dhcp/dhcpd.conf`
Obre el fitxer principal per editar-lo:
```bash
sudo nano /etc/dhcp/dhcpd.conf
```

Substitueix el contingut pel següent bloc corregit i estructurat:

```text
# ==========================================
# PARÀMETRES GLOBALS
# ==========================================
authoritative;
one-lease-per-client on;
default-lease-time 600;
max-lease-time 7200;
option domain-name "aula53.asir";
option domain-name-servers 192.168.1.10, 192.168.1.11;

# ==========================================
# PARÀMETRES DE SEGURETAT
# ==========================================
ddns-update-style none;
deny declines;
deny bootp;

# ==========================================
# DEFINICIÓ DE SUBXARXA I RANG DINÀMIC
# ==========================================
subnet 192.168.1.0 netmask 255.255.255.0 {
    range 192.168.1.100 192.168.1.200;
    option broadcast-address 192.168.1.255;
    option routers 192.168.1.1;
    option subnet-mask 255.255.255.0;
}

# ==========================================
# RESERVES PER ADREÇA FÍSICA (MAC)
# ==========================================
host Isabel {
    hardware ethernet 00:00:45:12:EE:F4;
    fixed-address 192.168.1.21;
    default-lease-time -1;
    max-lease-time -1;
    option host-name "Isabel";
}

host Fernando {
    hardware ethernet 00:00:45:13:1E:44;
    fixed-address 192.168.1.22;
    option domain-name-servers 192.168.1.20;
}
```
*(Guarda amb `Ctrl+O`, `Enter` i surt amb `Ctrl+X`).*

---

### Pas 6: Validació de sintaxi i arrencada del servei
1. Comprova que no hi ha cap error de sintaxi:
   ```bash
   sudo dhcpd -t
   ```
   *(Si no retorna errors i surt net, la sintaxi és correcta).*
2. Reinicia el servei i verifica el seu estat:
   ```bash
   sudo systemctl restart isc-dhcp-server
   sudo systemctl status isc-dhcp-server
   ```
   Ha de mostrar `active (running)` en color verd.
3. Comprova que el servidor està escoltant al socket UDP 67:
   ```bash
   sudo ss -unlp | grep dhcpd
   ```

---

## 💻 Pas a Pas al Client (Ubuntu Server / Debian CLI)

Connecta't a la màquina client a través d'IsardVDI (consola o SSH):

### 1. Identificar la interfície de xarxa a Personal1
Executa per llistar les interfícies:
```bash
ip -br link
```
Identifica quina és la interfície connectada a la xarxa `Personal1` (per exemple, `enp2s0`).

### 2. Sol·licitar IP al servidor DHCP
1. Allibera qualsevol IP prèvia que tingués la interfície:
   ```bash
   sudo dhclient -r enp2s0
   ```
2. Sol·licita una nova IP mostrant el procés detallat en pantalla:
   ```bash
   sudo dhclient -v enp2s0
   ```
   *(Hauràs de veure la negociació `DHCPDISCOVER`, `DHCPOFFER`, `DHCPREQUEST` i `DHCPACK` amb la IP atorgada dins del rang `192.168.1.100 - 192.168.1.200`).*

### 3. Verificar els paràmetres atorgats al Client
* **Comprovar IP i màscara:**
  ```bash
  ip a show enp2s0
  ```
* **Comprovar la porta d'enllaç per defecte (`192.168.1.1`):**
  ```bash
  ip route show
  ```
* **Comprovar els servidors DNS (`192.168.1.10` i `192.168.1.11`) i el domini (`aula53.asir`):**
  ```bash
  resolvectl status enp2s0
  ```

---

## 🔍 Comprovacions Finals i Logs al Servidor

* **Veure les concessions actives registrades:**
  ```bash
  cat /var/lib/dhcp/dhcpd.leases
  ```
* **Monitoritzar peticions DHCP en temps real:**
  ```bash
  sudo journalctl -u isc-dhcp-server -f
  ```

---

## 📋 Preguntes Teòriques de la Pràctica (Respostes d'Examen)

1. **Quin ús té l'option host-name?**  
   Assigna directament el nom de màquina (*hostname*) al sistema operatiu del client.
2. **Quina porta d'enllaç s'assignarà al host 192.168.1.101? I quins servidors DNS?**  
   * **Porta d'enllaç:** `192.168.1.1` (definida a `option routers` de la subxarxa).  
   * **Servidors DNS:** `192.168.1.10` i `192.168.1.11` (heretats de la configuració global).
3. **Si un host amb MAC 00:11:33:44:AA:FF sol·licita una IP amb temps de concessió de 4 hores. Què passarà? Quin nom de domini tindrà aquest equip? Quin servidor DNS utilitzarà?**  
   * **Temps:** Rebrà només **2 hores (7.200 s)**, limitat pel paràmetre global `max-lease-time 7200`.  
   * **Domini:** `aula53.asir`.  
   * **DNS:** `192.168.1.10` i `192.168.1.11`.
4. **Si un host qualsevol que no és ni “Isabel” ni “Fernando” demana una concessió per quant de temps se li donarà?**  
   Rebrà **600 segons (10 minuts)** (`default-lease-time`), o el temps sol·licitat si no supera el límit màxim de 7.200 segons.
5. **Quin temps de concessió es donarà al host Isabel?**  
   Temps **il·limitat / permanent** (`-1` desactiva la caducitat del lease).
6. **Quin servidor DNS utilitzarà “Fernando”?**  
   Únicament el **`192.168.1.20`**, ja que la directiva del seu bloc `host` preval sobre la global.
7. **Quin servidor DNS utilitzarà “Isabel”?**  
   Els servidors DNS globals **`192.168.1.10` i `192.168.1.11`**, en no tenir DNS específics al seu bloc de reserva.
8. **Què passarà si “Fernando” demana una IP durant 5 hores?**  
   Rebrà una concessió limitada a un màxim de **2 hores (7.200 s)**, heretada de `max-lease-time 7200;`.
9. **En cas que la màquina on tenim instal·lat el servidor DHCP tingui diverses targetes de xarxa. Com diem al servidor que ha d'assignar adreces només per una?**  
   Definint la interfície concreta a la variable `INTERFACESv4` dins de `/etc/default/isc-dhcp-server` (ex: `INTERFACESv4="enp3s0"`).
10. **Què vol dir el paràmetre Authoritative en un servidor DHCP?**  
    Indica que el servidor és l'autoritat oficial de la subxarxa. Si rep una sol·licitud d'un client amb una IP incorrecta d'un altre segment, li envia un paquet `DHCPNAK` forçant-lo a descartar-la i sol·licitar una IP nova vàlida.
