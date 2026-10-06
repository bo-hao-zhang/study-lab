# Pràctica 1: Serveis DHCP — Preguntes Teòriques
**Institut TIC de Barcelona — M06 Sistemes Operatius en Xarxa**

### 1. Quin ús té l'option host-name?
Assigna directament el nom de màquina (*hostname*) que ha d'adoptar el client al seu sistema operatiu.

### 2. Quina porta d'enllaç s'assignarà al host 192.168.1.101? I quins servidors DNS?
* **Porta d'enllaç:** `192.168.1.1` (definida a `option routers` de la subxarxa).
* **Servidors DNS:** `192.168.1.10` i `192.168.1.11` (heretats de la configuració global).

### 3. Si un host amb MAC 00:11:33:44:AA:FF sol·licita una IP amb temps de concessió de 4 hores. Què passarà? Quin nom de domini tindrà aquest equip? Quin servidor DNS utilitzarà?
* **Temps de concessió:** Rebrà només **2 hores (7.200 s)**, ja que el servidor té fitxat el límit màxim amb `max-lease-time 7200`.
* **Nom de domini:** `aula53.asir` (paràmetre global).
* **Servidors DNS:** `192.168.1.10` i `192.168.1.11` (paràmetres globals).

### 4. Si un host qualsevol que no és ni “Isabel” ni “Fernando” demana una concessió per quant de temps se li donarà?
Se li donarà el temps per defecte: **600 segons (10 minuts)** (`default-lease-time`). Si el client en sol·licita un de concret a la petició, se li atorgarà el que demana sempre que no superi el topall de 7.200 segons (`max-lease-time`).

### 5. Quin temps de concessió es donarà al host Isabel?
Temps **il·limitat / permanent**, ja que té definit `default-lease-time -1;` i `max-lease-time -1;` (el valor `-1` desactiva la caducitat de la concessió).

### 6. Quin servidor DNS utilitzarà “Fernando”?
Únicament el servidor DNS **`192.168.1.20`**, ja que la directiva del seu bloc `host` preval sobre la configuració global.

### 7. Quin servidor DNS utilitzarà “Isabel”?
Els servidors DNS globals **`192.168.1.10` i `192.168.1.11`**, en no tenir cap DNS definit dins del seu bloc de reserva.

### 8. Què passarà si “Fernando” demana una IP durant 5 hores?
Rebrà la concessió limitada a un màxim de **2 hores (7.200 s)**, ja que no té límit propi al seu bloc i hereta el paràmetre global `max-lease-time 7200;`.

### 9. En cas que la màquina on tenim instal·lat el servidor DHCP tingui diverses targetes de xarxa. Com diem al servidor que ha d'assignar adreces només per una?
Definint la interfície concreta a la directiva `INTERFACESv4` dins del fitxer `/etc/default/isc-dhcp-server` (per exemple: `INTERFACESv4="enp3s0"`).

### 10. Què vol dir el paràmetre Authoritative en un servidor DHCP?
Indica que el servidor és l'autoritat oficial de la subxarxa. Si un client sol·licita o renova una IP incorrecta d'un altre segment, el servidor li envia un paquet `DHCPNAK` per obligar-lo a rebutjar-la i començar de nou la sol·licitud d'una IP vàlida.
