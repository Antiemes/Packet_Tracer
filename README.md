# Alap parancsok és kezelés

```code
enable
```

Privilegizált módba lép.

```code
configure terminal
```

vagy röviden

```code
conf t
```

Konfigurációs módba lép.

```code
?
```

Listázza a parancsokat. Lehet kérni konkrét paranccsal kapcsolatos
helpet is. Pl:

```code
show ?
```

```code
<Tab>
```

Kiegészíti a parancsot.

# Jelszavas védelem

```code
enable
conf t
enable password 123
```

Ekkor a jelszó titkosítatlanul tárolódik, amit le is tudunk
kérdezni:

```code
show running-config
```

A jelszó titkosított megadása a következő:

```code
enable secret 123
```

# Egyéb hasznos beállítások

```code
no domain-lookup
no logging synchronous
```

# Soros kapcsolat (sávon kívüli kapcsolat)

Ez csak akkor működik, ha ott vagyok fizikailag.

Kék kábel (console, RS-232)

```code
line console 0
```

Itt tudom konfigurálni a soros vonalat

```code
password 123
login
```

# Sávon belüli kapcsolat

```code
line vty
line vty 0
line vty 0 4
```

```code
password 123
login
```

De kezeljünk felhasználókat.

```code
username admin password 123
line vty 0 15
login local
exit
```

Innen telnettel tudunk belépni. Ha login local van, akkor kér
felhasználónevet kér, simán a login csak jelszót kér.

A jelszavak titkosítása:

```code
service password-encryption
exit
show running-config
```

De a telnet helyett be lehet állítani SSH kapcsolatot is.

Kell IP cím, hosztnév és domain név is.

```code
conf t
ip domain name admin
crypto ?
crypto key generate rsa
line vty 0 15
transport input ssh
login local
```

PC parancssorból

```code
ssh -l username 192.....
```

# Konfigurációk

RAM-ban: Aktív konfiguráció

```code
show running-config
hostname foobar
```

Az aktív konfiguráció másolása startup-configgá

```code
copy running-config startup-config
```

Újraindítás:


```code
reload
```

# Flash törlése

```code
enable
dir flash:
delete cl....
reload
```

Innentől ROMMON módba kerülünk.

TFTK image letöltés:

Egy szervert hozzá kell kapcsolni a routerhez, IP címet beállítani,
TFTP szolgáltatást engedélyezni. Majd a routeren:

```code
tftpdnld
```

Globális változókat kell megadni:

```code
IP_ADDRESS=192.168.1.1
IP_SUBNET_MASK=255.255.255.0
DEFAULT_GATEWAY=192.168.1.1
TFTP_SERVER=192.168.1.100
TFTP_FILE=...
```

```code
tftpdnld
dir flash:
reset
show version
```

# Bejelentkezési üzenet

```code
banner
banner login
banner motd
```

# Interfészek konfigurációja

(1841)

```code
enable
conf t
```

```code
enable
show interfaces
```

Legyen egy PC, aminek az IP címe 192.168.1.10, és tegyünk be egy switchet.

A router IP címe 192.168.1.1 lesz.

```code
conf t
interface FastEthernet 0/0
ip address 192.168.1.1 255.255.255.0
description "Leiras"
no shutdown
```

```code
exit
exit
ping 192.168.1.10
copy run start
```

Soros interface-ek között lehet soros interface-t tenni. Bele kell tenni
a routerbe a HWC-2T modult. A piros villámmal lehet összekötni.

```code
enable
conf t
interface Serial 0/0/0
ip address 10.0.0.2 255.255.255.0
clock rate 64000
description "foo"
no shutdown
```

Az orajelet csak a masiknal tudjuk beallitani.

# Switch konfigurációja

A switch-nek nincs IP címe, így nem tudok pl. telnettel belépni rá.
De van egy VLAN-ja, aminek tudok IP címet adni.

```code
conf t
interface vlan1
ip address 192.168.1.20 255.255.255.0
no shutdown
```

Innentől fogok tudni csatlakozni hozzá távolról.

```code
conf t
line vty 0 15
password foo
login
exit
```

Ha nincs enable jelszó, akkor nem tudok enable-t kiadni.

A switch-nek is be lehet állítani default gateway-t.

```code
ip default gateway 192.168.1.1
```



```code
ip route
```

```code
conf t
ip route ?
ip route 192.168.2.0 255.255.255.0 ?
```

Merre talalhato a halozat? Most meg kell adni, hogy merre talalhato
az a halozat.

* Kovetkezo ugras IP cime.
* Melyik interface-en kell kikuldeni?

```code
ip route 192.168.2.0 255.255.255.0 Serial 0/0/0
exit
show ip route
```

* C: Ezeket kozvetlenul latja
* S: Statikus

Alapértelmezett átjáró: Ha nincs benne a hálózat a route table-ben, akkor
arrafele próbálja meg továbbítani a csomagokat.

```code
conf t
ip route 0.0.0.0 0.0.0.0 Serial0/0/1
exit
show ip route
```

Jele: S\*

Törlés:

```code
no ip route ...
```

# RIP forgalomirányítás

RIP V2 beállítása. A RIP V2 támogatja a változó hosszúságú
alhálózati maszkokat is.

```code
conf t
router rip
version 2
network 192.168.1.0
network 10.0.0.0
```

Fel kell sorolni, hogy melyik hálózatokat hirdessük.
Alhálózati maszkot nem kell megadni.

Innen a show ip route parancs mutatja a felderített hálózatokat.

```code
show ip protocols
```

Megmutatja, hogy milyen protokollokat használ. Illetve egyéb adatokat.

RIP üzenetek mutatása:

```code
debug ip rip
```

Hova ne küldjön frissítő csomagot:

```code
conf t
route rip
passive-interface FastEthernet 0/0
```


