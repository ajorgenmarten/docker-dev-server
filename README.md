# Configuración del servidor de desarrollo paso a paso

## Instalación de servidor DNS (CoreDNS)

### Objetivo: Crear un servidor DNS local que resuelva nombres como *.dev.local en tu red, apuntando a la IP de tu máquina host

### 📝 Requisitos

1. Docker instalado
2. Carpeta `volumes/dns` creada con:
    - Fichero `Corfile`: Configuración de CoreDNS
    - Fichero `dev.local.db`: Zona DNS para el dominio

### 📂 Estrucutra de archivos

```txt
./volumes/dns/
├── Corefile          # Configuración principal de CoreDNS
└── dev.local.db      # Definición de registros DNS para dev.local
```

### 🔨 Configuración de Corefile

```txt
.:53 {
    forward . 8.8.8.8 8.8.4.4
    log
    errors
}

dev.local:53 {
    file /etc/coredns/dev.local.db
    log
    errors
}
```
- `file /etc/coredns/dev.local.db`: Ruta dentro del contenedor donde se montará tu archivo de zona.
- `log` y `errors`: Habilita logging para diagnóstico.

### 🔨 Configuración de dev.local.db (Zona DNS)

```txt
$TTL 60
@ IN SOA dev.local. admin.dev.local. (
    2025080801 ; serial
    3600       ; Refresh
    1800       ; Retry
    1209600    ; Expire
    60         ; Minimum TTL
)
IN NS ns1.dev.local.
ns1 IN A <IP-DEL-HOST>
pgadmin IN A <IP-DEL-HOST>
portainer IN A <IP-DEL-HOST>
files IN A <IP-DEL-HOST>
```

**Importante**:

- Reemplaza <IP-DEL-HOST> con la IP local de tu máquina (ej: 192.168.1.100).
    - Cómo obtenerla:
        - Windows: ipconfig (Busca IPv4 en la interfaz de red).
        - Linux/macOS: ifconfig o ip a (Busca inet en eth0 o wlan0).

- El Serial debe incrementarse cada vez que modifiques el archivo (ej: 2023082502).