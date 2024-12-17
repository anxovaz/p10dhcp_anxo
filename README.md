# DHCP - Anxo Vázquez
## 1. dhcpd.conf
Creo el archivo con el siguiente contenido:
```shell
# Archivo: /dhcpd.conf
# Configuración básica del servidor DHCP

# Definir la subred y la máscara de subred
subnet 172.16.0.0 netmask 255.255.0.0 {
  # Rango de direcciones IP a asignar
  range 172.16.0.10 172.16.0.50;

  # Dirección del router (puerta de enlace)
  option routers 172.16.0.1;

  # Dirección de la broadcast
  option broadcast-address 172.16.255.255;

  # Servidores DNS que se proporcionarán a los clientes
  option domain-name-servers 8.8.8.8, 8.8.4.4;

  # Tiempo de concesión de las IPs (en segundos)
  default-lease-time 600;
  max-lease-time 7200;
}

# Configuración adicional para asignación de IP fija (si es necesario)
# Puedes usar estas líneas para asignar una IP estática a un dispositivo basado en su MAC address.
# host ubuntu-client {
#   hardware ethernet 00:11:22:33:44:55;
#   fixed-address 172.16.0.60;
# }

```

## 2. isc-dhcp-server
Creo el archivo con:
```shell
# Archivo: /etc/default/isc-dhcp-server

# El servicio DHCP se ejecutará en estas interfaces
INTERFACESv4="eth0"

# IPv6:
# INTERFACESv6="eth0" 
```

## 3. docker-compose.yml
Declaro el servidor, el cliente y la red

```shell
services:
  dhcp-server:
    image: networkboot/dhcpd
    container_name: serverdhcp
    networks:
      dhcp__network:
        ipv4_address: 172.16.0.2
    volumes:
      - ./dhcp/dhcpd.conf:/etc/dhcp/dhcpd.conf
      - ./dhcp/isc-dhcp-server:/etc/Default/isc-dchp-server
    ports:
      - "67:67/udp"  # DHCP
  ubuntu-client:
    image: ubuntu:latest
    container_name: clientedhcp
    networks:
      - dhcp__network
    command: sleep infinity    


networks:
  dhcp__network:
    driver: bridge
    ipam:
      config:
        - subnet: 172.16.0.0/16
          gateway: 172.16.0.1
```

## 4. Pruebas
Entro en el cliente y ejecuto "ip a" para ver su dirección ip:
```shell
ip a
```


