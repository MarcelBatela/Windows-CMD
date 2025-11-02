
![[canal.jpeg]]

# Résultats d’un audit local


Prérequis:
Pour bien mener ce test in faut installer l'outils `upnpc` . Pour mon je l'ai tester qu'avec Kali linux donc en utilisant le gestionnaire apt. 
Commande: `sudo apt install miniupnpc`  

#### SCANNING

Le résultat `nmap`  le deux (2) ports ouverts, voir l'image ci-dessous. Le port:
- 8099 tcp, permet à CANALBOX de manager le modem WIFI.
- 49152 tcp, la présence du Protocole UPNP 

**NB**: Dans le but d'avoir la page d'administration du modem, je me suis concentré sur le port  `49152` .

```bash
Nmap scan report for dsldevice.lan (192.168.1.254)
Host is up, received arp-response (0.0018s latency).
Scanned at 2025-11-02 17:01:14 CET for 63s
Not shown: 994 closed tcp ports (reset)
PORT      STATE    SERVICE REASON         VERSION
22/tcp    filtered ssh     no-response
23/tcp    filtered telnet  no-response
80/tcp    filtered http    no-response
443/tcp   filtered https   no-response
8099/tcp  open     http    syn-ack ttl 64 Web-Based Enterprise Management CIM serverOpenPegasus WBEM httpd
|_http-title: Site doesn't have a title.
| http-methods: 
|_  Supported Methods: HEAD POST OPTIONS
49152/tcp open     upnp    syn-ack ttl 64 Portable SDK for UPnP devices 1.6.20 (Linux 3.18.21; UPnP 1.0)
MAC Address: 78:4F:24:9A:11:30 (Taicang T&W Electronics)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel, cpe:/o:linux:linux_kernel:3.18.21

```

En utilisant l'outils `upnpc` j'ai pu facilement enumerer :
- La version du upnp du modem.
- L'adresse **CGNAT** du modem
- Tous les services disponibles
- Code ou Numéro  **ALCLXXXX**
- Et autres.

Le **CGNAT** (pour **Carrier-Grade Network Address Translation**) — aussi appelé **NAT de niveau opérateur** — est une technique utilisée par les **fournisseurs d’accès à Internet (FAI)** pour **partager une même adresse IP publique entre plusieurs clients**.

👉 Le **CGNAT** permet donc à un opérateur de faire ceci :

- Chaque abonné reçoit une **adresse IP privée** (comme dans un réseau local).
- Plusieurs abonnés partagent **la même IP publique**.
- Le routeur du FAI traduit les connexions de chacun grâce à des **numéros de port**.


*Pour savoir comment utiliser l'outils:*

```bash
upnpc -h
upnpc: miniupnpc library test client, version 2.3.3.
 (c) 2005-2025 Thomas Bernard.
More information at https://miniupnp.tuxfamily.org/ or http://miniupnp.free.fr/

Usage:
  upnpc [options] -a ip port external_port protocol [duration] [remote host]
    Add port mapping
  upnpc [options] -r port1 [external_port1] protocol1 [port2 [external_port2] protocol2] [...]
    Add multiple port mappings to the current host
  upnpc [options] -d external_port protocol [remote host]
    Delete port redirection
  upnpc [options] -f external_port1 protocol1 [external_port2 protocol2] [...]
    Delete multiple port redirections
  upnpc [options] -s
    Get Connection status
  upnpc [options] -l
    List redirections
  upnpc [options] -L
    List redirections (using GetListOfPortMappings (for IGD:2 only)
  upnpc [options] -n ip port external_port protocol [duration] [remote host]
    Add (any) port mapping allowing IGD to use alternative external_port (for IGD:2 only)
  upnpc [options] -N external_port_start external_port_end protocol [manage]
    Delete range of port mappings (for IGD:2 only)
  upnpc [options] -A remote_ip remote_port internal_ip internal_port protocol lease_time
    Add Pinhole (for IGD:2 only)
  upnpc [options] -U uniqueID new_lease_time
    Update Pinhole (for IGD:2 only)
  upnpc [options] -C uniqueID
    Check if Pinhole is Working (for IGD:2 only)
  upnpc [options] -K uniqueID
    Get Number of packets going through the rule (for IGD:2 only)
  upnpc [options] -D uniqueID
    Delete Pinhole (for IGD:2 only)
  upnpc [options] -S
    Get Firewall status (for IGD:2 only)
  upnpc [options] -G remote_ip remote_port internal_ip internal_port protocol
    Get Outbound Pinhole Timeout (for IGD:2 only)
  upnpc [options] -P
    Get Presentation URL

Notes:
  protocol is UDP or TCP.
  Use "" for any remote_host and 0 for any remote_port.
  @ can be used in option -a, -n, -A and -G to represent local LAN address.

Options:
  -e description : set description for port mapping.
  -6 : use IPv6 instead of IPv4.
  -u URL : bypass discovery process by providing the XML root description URL.
  -m address/interface : provide IPv4 address or interface name (IPv4 or IPv6) to use for sending SSDP multicast packets.
  -z localport : SSDP packets local (source) port (1024-65535).
  -p path : use this path for MiniSSDPd socket.
  -t ttl : set multicast TTL. Default value is 2.
  -i : ignore errors and try to use also disconnected IGD or non-IGD device.

```

# ip CGNAT ==> 100.XXX.XXX.XXX

```bash
upnpc -l
upnpc: miniupnpc library test client, version 2.3.3.
 (c) 2005-2025 Thomas Bernard.
More information at https://miniupnp.tuxfamily.org/ or http://miniupnp.free.fr/

List of UPNP devices found on the network :
 desc: http://192.168.1.254:49152/gatedesc.xml
 st: urn:schemas-upnp-org:device:InternetGatewayDevice:1

Found an IGD with a reserved IP address (100.xxx.xxx.xxx) : http://192.168.1.254:49152/upnp/control/WANIPConn1
Local LAN ip address : 192.168.1.88
Connection Type : IP_Routed
Status : Connected, uptime=3508s, LastConnectionError : ERROR_NONE
  Time started : Sun Nov  2 16:17:32 2025
MaxBitRateDown : 512000 bps (512 Kbps)   MaxBitRateUp 512000 bps (512 Kbps)
ExternalIPAddress = 100.64.83.126
 i protocol exPort->inAddr:inPort description remoteHost leaseTime
 ```

Liste complètes des services disponibles.

```bash
upnp-listdevices 
searching all UPnP devices
  1: urn:schemas-upnp-org:service:LANHostConfigManagement:1
     http://192.168.1.254:49152/gatedesc.xml
     uuid:75802409-bccb-40e7-8e6c-fa095ecce13a::urn:schemas-upnp-org:service:LANHostConfigManagement:1
  2: urn:schemas-upnp-org:device:LANDevice:1         
     http://192.168.1.254:49152/gatedesc.xml
     uuid:75802409-bccb-40e7-8e6c-fa095ecce13a::urn:schemas-upnp-org:device:LANDevice:1
  3: urn:schemas-upnp-org:service:WANIPv6FirewallControl:1
     http://192.168.1.254:49152/gatedesc.xml
     uuid:75802409-bccb-40e7-8e6c-fa095ecce13a::urn:schemas-upnp-org:service:WANIPv6FirewallControl:1
  4: urn:schemas-upnp-org:service:WANPPPConnection:1 
     http://192.168.1.254:49152/gatedesc.xml
     uuid:75802409-bccb-40e7-8e6c-fa095ecce13a::urn:schemas-upnp-org:service:WANPPPConnection:1
  5: urn:schemas-upnp-org:service:WANIPConnection:2  
     http://192.168.1.254:49152/gatedesc.xml
     uuid:75802409-bccb-40e7-8e6c-fa095ecce13a::urn:schemas-upnp-org:service:WANIPConnection:2
  6: urn:schemas-upnp-org:device:WANConnectionDevice:2
     http://192.168.1.254:49152/gatedesc.xml
     uuid:75802409-bccb-40e7-8e6c-fa095ecce13a::urn:schemas-upnp-org:device:WANConnectionDevice:2
  7: uuid:75802409-bccb-40e7-8e6c-fa095ecce13a       
     http://192.168.1.254:49152/gatedesc.xml
     uuid:75802409-bccb-40e7-8e6c-fa095ecce13a
  8: urn:schemas-upnp-org:service:WANCommonInterfaceConfig:1
     http://192.168.1.254:49152/gatedesc.xml
     uuid:75802409-bccb-40e7-8e6c-fa095ecce13f::urn:schemas-upnp-org:service:WANCommonInterfaceConfig:1
  9: urn:schemas-upnp-org:device:WANDevice:2         
     http://192.168.1.254:49152/gatedesc.xml
     uuid:75802409-bccb-40e7-8e6c-fa095ecce13f::urn:schemas-upnp-org:device:WANDevice:2
 10: uuid:75802409-bccb-40e7-8e6c-fa095ecce13f       
     http://192.168.1.254:49152/gatedesc.xml
     uuid:75802409-bccb-40e7-8e6c-fa095ecce13f
 11: urn:schemas-dummy-com:service:Dummy:1           
     http://192.168.1.254:49152/gatedesc.xml
     uuid:a87452f5-d9fa-471f-bb29-7e7f7b02f5a8::urn:schemas-dummy-com:service:Dummy:1
 12: urn:schemas-upnp-org:device:InternetGatewayDevice:2
     http://192.168.1.254:49152/gatedesc.xml
     uuid:a87452f5-d9fa-471f-bb29-7e7f7b02f5a8::urn:schemas-upnp-org:device:InternetGatewayDevice:2
 13: uuid:a87452f5-d9fa-471f-bb29-7e7f7b02f5a8       
     http://192.168.1.254:49152/gatedesc.xml
     uuid:a87452f5-d9fa-471f-bb29-7e7f7b02f5a8
 14: upnp:rootdevice                                 
     http://192.168.1.254:49152/gatedesc.xml
     uuid:a87452f5-d9fa-471f-bb29-7e7f7b02f5a8::upnp:rootdevice

http://192.168.1.254:49152/gatedesc.xml :
 1: urn:schemas-upnp-org:service:LANHostConfigManagement:1
    uuid:75802409-bccb-40e7-8e6c-fa095ecce13a::urn:schemas-upnp-org:service:LANHostConfigManagement:1
 2: urn:schemas-upnp-org:device:LANDevice:1
    uuid:75802409-bccb-40e7-8e6c-fa095ecce13a::urn:schemas-upnp-org:device:LANDevice:1
 3: urn:schemas-upnp-org:service:WANIPv6FirewallControl:1
    uuid:75802409-bccb-40e7-8e6c-fa095ecce13a::urn:schemas-upnp-org:service:WANIPv6FirewallControl:1
 4: urn:schemas-upnp-org:service:WANPPPConnection:1
    uuid:75802409-bccb-40e7-8e6c-fa095ecce13a::urn:schemas-upnp-org:service:WANPPPConnection:1
 5: urn:schemas-upnp-org:service:WANIPConnection:2
    uuid:75802409-bccb-40e7-8e6c-fa095ecce13a::urn:schemas-upnp-org:service:WANIPConnection:2
 6: urn:schemas-upnp-org:device:WANConnectionDevice:2
    uuid:75802409-bccb-40e7-8e6c-fa095ecce13a::urn:schemas-upnp-org:device:WANConnectionDevice:2
 7: uuid:75802409-bccb-40e7-8e6c-fa095ecce13a
    uuid:75802409-bccb-40e7-8e6c-fa095ecce13a
 8: urn:schemas-upnp-org:service:WANCommonInterfaceConfig:1
    uuid:75802409-bccb-40e7-8e6c-fa095ecce13f::urn:schemas-upnp-org:service:WANCommonInterfaceConfig:1
 9: urn:schemas-upnp-org:device:WANDevice:2
    uuid:75802409-bccb-40e7-8e6c-fa095ecce13f::urn:schemas-upnp-org:device:WANDevice:2
10: uuid:75802409-bccb-40e7-8e6c-fa095ecce13f
    uuid:75802409-bccb-40e7-8e6c-fa095ecce13f
11: urn:schemas-dummy-com:service:Dummy:1
    uuid:a87452f5-d9fa-471f-bb29-7e7f7b02f5a8::urn:schemas-dummy-com:service:Dummy:1
12: urn:schemas-upnp-org:device:InternetGatewayDevice:2
    uuid:a87452f5-d9fa-471f-bb29-7e7f7b02f5a8::urn:schemas-upnp-org:device:InternetGatewayDevice:2
13: uuid:a87452f5-d9fa-471f-bb29-7e7f7b02f5a8
    uuid:a87452f5-d9fa-471f-bb29-7e7f7b02f5a8
14: upnp:rootdevice
    uuid:a87452f5-d9fa-471f-bb29-7e7f7b02f5a8::upnp:rootdevice
```

#### Tableau explicatif des services disponibles:

| #   | Service / Device                                              | Signification                                                          | Risque potentiel                                                                                                                                                                                                              |
| --- | ------------------------------------------------------------- | ---------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1   | **`urn:schemas-upnp-org:service:LANHostConfigManagement:1`**  | Permet de configurer les paramètres LAN (DHCP, IP locales, etc.).      | Si mal protégé, quelqu’un sur le réseau local pourrait modifier ta configuration DHCP (délivrer de fausses IP, etc.).                                                                                                         |
| 2   | **`urn:schemas-upnp-org:device:LANDevice:1`**                 | Représente l’ensemble du réseau local vu par la box.                   | Purement descriptif. Faible risque en soi.                                                                                                                                                                                    |
| 3   | **`urn:schemas-upnp-org:service:WANIPv6FirewallControl:1`**   | Contrôle du pare-feu IPv6 côté WAN (Internet).                         | Si accessible depuis le LAN, un malware local pourrait désactiver ton pare-feu IPv6.                                                                                                                                          |
| 4   | **`urn:schemas-upnp-org:service:WANPPPConnection:1`**         | Gère les connexions PPPoE (ADSL/Fibre).                                | Très sensible : pourrait être utilisé pour couper la connexion ou récupérer des infos WAN.                                                                                                                                    |
| 5   | **`urn:schemas-upnp-org:service:WANIPConnection:2`**          | Sert notamment à ouvrir ou rediriger des ports via UPnP.               | C’est **le service typique d’ouverture automatique de ports** (très utilisé par les jeux, torrents, etc.). Si une appli malveillante sur ton PC l’utilise, elle peut ouvrir des ports vers l’extérieur sans ton consentement. |
| 6   | **`urn:schemas-upnp-org:device:WANConnectionDevice:2`**       | Appareil virtuel représentant la connexion WAN.                        | Faible risque direct. Sert de conteneur logique.                                                                                                                                                                              |
| 8   | **`urn:schemas-upnp-org:service:WANCommonInterfaceConfig:1`** | Donne des infos générales sur la connexion (débit, statut, etc.).      | Mineur, mais divulgue des infos utiles à un attaquant local.                                                                                                                                                                  |
| 9   | **`urn:schemas-upnp-org:device:WANDevice:2`**                 | Représente le module de connexion Internet.                            | Faible risque direct.                                                                                                                                                                                                         |
| 11  | **`urn:schemas-dummy-com:service:Dummy:1`**                   | Service factice, probablement pour compatibilité ou debug.             | Aucun danger connu.                                                                                                                                                                                                           |
| 12  | **`urn:schemas-upnp-org:device:InternetGatewayDevice:2`**     | C’est le “routeur Internet” vu par UPnP, la racine logique du système. | Si exposé sur le WAN (ce qui ne devrait **jamais** arriver), c’est une faille critique.                                                                                                                                       |
| 14  | **`upnp:rootdevice`**                                         | L’entrée principale du périphérique UPnP.                              | Structurelle, pas dangereuse en soi.                                                                                                                                                                                          |
## En résumé : les plus sensibles

#### Les services les plus dangereux sont :

- `WANIPConnection`
- `WANPPPConnection`
- `WANIPv6FirewallControl`

#### Car ils permettent :

- d’ouvrir ou rediriger des ports (exposition Internet involontaire),
- de modifier le pare-feu,
- voire de couper la connexion WAN

## Pour les liens exploitables


```bash
curl -Iv  http://192.168.1.254:49152/gatedesc.xml
*   Trying 192.168.1.254:49152...
* Connected to 192.168.1.254 (192.168.1.254) port 49152
* using HTTP/1.x
> HEAD /gatedesc.xml HTTP/1.1
> Host: 192.168.1.254:49152
> User-Agent: curl/8.15.0
> Accept: */*
> 
* Request completely sent off
< HTTP/1.1 200 OK
HTTP/1.1 200 OK
< CONTENT-LENGTH: 5318
CONTENT-LENGTH: 5318
< CONTENT-TYPE: text/xml;charset="utf-8"
CONTENT-TYPE: text/xml;charset="utf-8"
< DATE: Thu, 01 Jan 1970 14:45:29 GMT
DATE: Thu, 01 Jan 1970 14:45:29 GMT
< LAST-MODIFIED: Thu, 01 Jan 1970 00:01:29 GMT
LAST-MODIFIED: Thu, 01 Jan 1970 00:01:29 GMT
< SERVER: Linux/3.18.21, UPnP/1.0, Portable SDK for UPnP devices/1.6.20
SERVER: Linux/3.18.21, UPnP/1.0, Portable SDK for UPnP devices/1.6.20
< CONNECTION: close
CONNECTION: close
< 
```



Obtension du code  **ALCLxxx**  en utilisant curl

```bash
curl -iv http://192.168.1.254:49152/gatedesc.xml         
*   Trying 192.168.1.254:49152...
* Connected to 192.168.1.254 (192.168.1.254) port 49152
* using HTTP/1.x
> GET /gatedesc.xml HTTP/1.1
> Host: 192.168.1.254:49152
> User-Agent: curl/8.15.0
> Accept: */*
> 
* Request completely sent off
< HTTP/1.1 200 OK
HTTP/1.1 200 OK
< CONTENT-LENGTH: 5318
CONTENT-LENGTH: 5318
< CONTENT-TYPE: text/xml;charset="utf-8"
CONTENT-TYPE: text/xml;charset="utf-8"
< DATE: Thu, 01 Jan 1970 14:29:01 GMT
DATE: Thu, 01 Jan 1970 14:29:01 GMT
< LAST-MODIFIED: Thu, 01 Jan 1970 00:01:29 GMT
LAST-MODIFIED: Thu, 01 Jan 1970 00:01:29 GMT
< SERVER: Linux/3.18.21, UPnP/1.0, Portable SDK for UPnP devices/1.6.20
SERVER: Linux/3.18.21, UPnP/1.0, Portable SDK for UPnP devices/1.6.20
< CONNECTION: close
CONNECTION: close
< 

<?xml version="1.0"?>
<root xmlns="urn:schemas-upnp-org:device-1-0" configId="7">
  <specVersion>
    <major>1</major>
    <minor>0</minor>
  </specVersion>
  <device>
    <deviceType>urn:schemas-upnp-org:device:InternetGatewayDevice:2</deviceType>
    <friendlyName>Internet Home Gateway Device</friendlyName>
    <manufacturer>Nokia</manufacturer>
    <manufacturerURL>http://www.nokia.com</manufacturerURL>
    <modelDescription>Optical-fiber Broadband Router</modelDescription>
    <modelName>IGD Version 2.00</modelName>
    <modelNumber>2.00</modelNumber>
    <modelURL>http://www.nokia.com</modelURL>
    <serialNumber>**ALCLXXXXXX<**/serialNumber>
    <UDN>uuid:a87452f5-d9fa-471f-bb29-7e7f7b02f5a8</UDN>
    <UPC></UPC>
    <iconList>
      <icon>
        <mimetype>image/png</mimetype>
        <width>16</width>
        <height>16</height>
        <depth>8</depth>
        <url>/ligd.png</url>
      </icon>
    </iconList>
    <serviceList>
      <service>
        <serviceType>urn:schemas-dummy-com:service:Dummy:1</serviceType>
        <serviceId>urn:dummy-com:serviceId:dummy1</serviceId>
        <SCPDURL>/dummy.xml</SCPDURL>
        <controlURL>/dummy</controlURL>
        <eventSubURL></eventSubURL>
      </service>
    </serviceList>
    <deviceList>
      <device>
        <deviceType>urn:schemas-upnp-org:device:WANDevice:2</deviceType>
        <friendlyName>WANDevice</friendlyName>
        <manufacturer>Nokia</manufacturer>
        <manufacturerURL>http://www.nokia.com</manufacturerURL>
        <modelDescription>WAN Device on Linux IGD</modelDescription>
        <modelName>Linux IGD</modelName>
        <modelNumber>1.00</modelNumber>
        <modelURL>http://www.nokia.com</modelURL>
        <serialNumber>1.00</serialNumber>
        <UDN>uuid:75802409-bccb-40e7-8e6c-fa095ecce13f</UDN>
        <UPC>123456789012</UPC>
        <serviceList>
          <service>
            <serviceType>urn:schemas-upnp-org:service:WANCommonInterfaceConfig:1</serviceType>
            <serviceId>urn:upnp-org:serviceId:WANCommonIFC1</serviceId>
            <SCPDURL>/gateicfgSCPD.xml</SCPDURL>
            <controlURL>/upnp/control/WANCommonIFC1</controlURL>
            <eventSubURL>/upnp/event/WANCommonIFC1</eventSubURL>
          </service>
        </serviceList>
        <deviceList>
          <device>
            <deviceType>urn:schemas-upnp-org:device:WANConnectionDevice:2</deviceType>
            <friendlyName>WANConnectionDevice</friendlyName>
            <manufacturer>Nokia</manufacturer>
            <manufacturerURL>http://www.nokia.com</manufacturerURL>
            <modelDescription>WanConnectionDevice on Linux IGD</modelDescription>
            <modelName>Linux IGD</modelName>
            <modelNumber>1.0</modelNumber>
            <modelURL>http://www.nokia.com</modelURL>
            <serialNumber>1.0</serialNumber>
            <UDN>uuid:75802409-bccb-40e7-8e6c-fa095ecce13a</UDN>
            <UPC>123456789012</UPC>
            <serviceList>
              <service>
                <serviceType>urn:schemas-upnp-org:service:WANIPConnection:2</serviceType>
                <serviceId>urn:upnp-org:serviceId:WANIPConn1</serviceId>
                <SCPDURL>/gateconnSCPD.xml</SCPDURL>
                <controlURL>/upnp/control/WANIPConn1</controlURL>
                <eventSubURL>/upnp/event/WANIPConn1</eventSubURL>
              </service>
              <service>
                <serviceType>urn:schemas-upnp-org:service:WANPPPConnection:1</serviceType>
                <serviceId>urn:upnp-org:serviceId:WANPPPConn1</serviceId>
                <SCPDURL>/gateconnPPPSCPD.xml</SCPDURL>
                <controlURL>/upnp/control/WANPPPConn1</controlURL>
                <eventSubURL>/upnp/event/WANPPPConn1</eventSubURL>
              </service>
              <service>
                <serviceType>urn:schemas-upnp-org:service:WANIPv6FirewallControl:1</serviceType>
                <serviceId>urn:upnp-org:serviceId:WANIPv6FwCtrl1</serviceId>
                <SCPDURL>/wanipv6fwctrlSCPD.xml</SCPDURL>
                <controlURL>/upnp/control/WANIPv6FwCtrl1</controlURL>
                <eventSubURL>/upnp/event/WANIPv6FwCtrl1</eventSubURL>
              </service>
            </serviceList>
          </device>
        </deviceList>
      </device>
      <device>
        <deviceType>urn:schemas-upnp-org:device:LANDevice:1</deviceType>
        <friendlyName>LANDevice</friendlyName>
        <manufacturer>Nokia</manufacturer>
        <manufacturerURL>http://www.nokia.com</manufacturerURL>
        <modelDescription>LAN Device on Linux IGD</modelDescription>
        <modelName>Linux IGD</modelName>
        <modelNumber>1.0</modelNumber>
        <modelURL>http://www.nokia.com</modelURL>
        <serialNumber>1.0</serialNumber>
        <UDN>uuid:75802409-bccb-40e7-8e6c-fa095ecce13a</UDN>
        <UPC></UPC>
        <serviceList>
          <service>
            <serviceType>urn:schemas-upnp-org:service:LANHostConfigManagement:1</serviceType>
            <serviceId>urn:upnp-org:serviceId:LANHostConfig1</serviceId>
            <SCPDURL>/lanhostconfigSCPD.xml</SCPDURL>
            <controlURL>/upnp/control/LANHostConfig1</controlURL>
            <eventSubURL></eventSubURL>
          </service>
        </serviceList>
      </device>
    </deviceList>
     <presentationURL>http://192.168.1.254</presentationURL>
  </device>
</root>
* shutting down connection #0
```

On peut constater que le début de la repose xml renvoyer par le router nous avons le code **ALCLXXX** 

```bash
<device>
    <deviceType>urn:schemas-upnp-org:device:InternetGatewayDevice:2</deviceType>
    <friendlyName>Internet Home Gateway Device</friendlyName>
    <manufacturer>Nokia</manufacturer>
    <manufacturerURL>http://www.nokia.com</manufacturerURL>
    <modelDescription>Optical-fiber Broadband Router</modelDescription>
    <modelName>IGD Version 2.00</modelName>
    <modelNumber>2.00</modelNumber>
    <modelURL>http://www.nokia.com</modelURL>
    <serialNumber>**ALCLbXXXXXX<**/serialNumber>
    <UDN>uuid:a87452f5-d9fa-471f-bb29-7e7f7b02f5a8</UDN>
    <UPC></UPC>
    
```

# Exploitation
Maintenat que j'ai le code, il faut le tester sur la pager de connexion.  Il faut taper le lien ci-dessous: 

```bash
https://my.canalbox.africa/login
```

![[login.png]]

Ici je fournis le code ALCL obtenue pendant l'exploitation

![[log1.png]]

Je suis maintenant dans le panel d'administration du modem.

![[Capture d’écran_2025-11-02_16-24-11.png]]

![[Capture d’écran_2025-11-02_16-24-25.png]]

# Types d’attaques que j'ai realiser:

> _Remarque : ceci décrit les vecteurs, pas de commandes ni d’exploits détaillés._


1. **Usurpation/empoisonnement DHCPv4 (fake DHCP)**
    
    - **Principe** : un serveur DHCP malveillant fournit de fausses informations réseau (gateway, DNS, routes).
    - **Impact** : redirection du trafic, captation DNS, coupure d’accès, interception de connexions non chiffrées.
    - **Indicateurs** : clients obtiennent une gateway/DNS différente de la config attendue ; changement d’adresse IP inattendu.
    - **Mitigation** : authentification ou filtrage DHCP, isolation des ports, DHCP snooping (sur équipements managés).
        
2. **Abus des Router Advertisements (IPv6 SLAAC / radvd)**
    
    - **Principe** : en injectant RA ou en manipulant les préfixes, un attaquant peut pousser des routes/IPv6 aux clients.
    - **Impact** : redirection de trafic IPv6, interception / man-in-the-middle, déni de service IPv6.
    - **Indicateurs** : apparition de préfixes IPv6 inhabituels, clients configurés avec des adresses/routes inattendues.
        
3. **Poisoning / spoofing DNS (Bind9 ou DNS malveillant)**
    
    - **Principe** : fournir des réponses DNS falsifiées aux clients (ou contrôler le forwarder DNS).
    - **Impact** : redirection vers sites de phishing, interception d’identifiants, altération de services.
    - **Indicateurs** : résolution DNS qui renvoie des IP différentes de la baseline, utilisateurs redirigés vers domaines suspects.
        
4. **Captive portal / redirection web (phishing / credential capture)**
    
    - **Principe** : forcer ou rediriger les utilisateurs vers une page d’authentification/consentement frauduleuse.
    - **Impact** : récolte d’identifiants, installation de malware, social engineering.
    - **Indicateurs** : redirections HTTP/S inhabituelles, certificats SSL non valides sur pages d’authentification.
        # NB:
        *Pour mon cas j'ai utiliser un dns  volide, vpcs dans ZOMRO, et autres*
        
1. **Abus UPnP / API d’administration**
    
    - **Principe** : utiliser UPnP ou services d’administration exposés pour ouvrir des ports, modifier le NAT/pare-feu, ou récupérer des infos.
    - **Impact** : exposition de services internes sur Internet, persistance d’accès, attaque depuis l’extérieur.
    - **Indicateurs** : règles NAT créées sans consentement, services accessibles depuis WAN.
    
2. **Manipulation du WAN / PPP settings**
    
    - **Principe** : modification des paramètres de connexion (coupure, reroutage, récupération d’informations opérateur).
    - **Impact** : interruption du service, changement de routage, exposition d’informations sensibles.
    - **Indicateurs** : coupures récurrentes, changements de statut WAN.
        
3. **Man-in-the-Middle général (MITM) via rerouting**
    
    - **Principe** : combiner les vecteurs (DHCP, RA, DNS) pour intercepter trafic.
    - **Impact** : lecture/modification du trafic non chiffré, sessions détournées.
        # NB:
        Proxy inversé NGINX*
        
4. **Exfiltration et pivotement**
    
    - **Principe** : après accès, récolter données internes puis exfiltrer via tunnels ou serveurs contrôlés.
    - **Impact** : fuite d’informations, compromission d’autres systèmes.
    - **Indicateurs** : connexions réseau sortantes vers IP/domaine inhabituels, transferts volumineux en heures creuses.


## **Limites** : 
Certains vecteurs n’ont pas été testés dans le présent engagement, notamment les requêtes SOAP et les requêtes XML mal formées destinées à évaluer la résistance des modems Canalbox. Ces tests demanderaient une autorisation explicite et un périmètre dédié en raison du risque de perturbation du service. Ils pourront être inclus dans un volet d’évaluation ultérieur sur demande.

### Pour toute question relative aux résultats ou à la méthodologie, vous pouvez me contacter directement — je répondrai avec plaisir.

### Tel +242 04 445 6102
### contact@atte-thm.com

