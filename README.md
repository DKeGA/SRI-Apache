# proyectoApache

## Creación del docker-compose

Primero crearemos el fichero docker-compose.yml y luego vamos a configurarlo para que tenga todos los servicioos, el formato sera el siguiente:

        version: '3.9'
        
        services:
        
          asir-apache:
          
            image: php:7.4-apache
            
            networks:
            
              bind9_subnet:
              
                ipv4_address: 10.1.0.225
                
            container_name: asir-apache
            
            ports:
            
            - '80:80'
            
            - '8000:8000'
            
            - '443:443'
            
            volumes:
            
            - ./html:/var/www/html
            
            - ./confApache:/etc/apache2

          asir_cliente:
          
              container_name: asir_cliente2
              
              image: alpine
              
              networks:
              
                bind9_subnet:
                
                  ipv4_address: 10.1.0.224
                  
              stdin_open: true 
              
              tty: true       
              
              dns:
              
                  - 10.1.0.222

          bind9:
          
            container_name: asir_bind
            
            image: internetsystemsconsortium/bind9:9.16
            
            networks:
            
              bind9_subnet:
              
                ipv4_address: 10.1.0.222
                
            volumes:
            
               - ~/Escritorio/sri/proyectoApache/config:/etc/bind
               
               - ~/Escritorio/sri/proyectoApache/zonas:/var/lib/bind

          firefox:
          
            container_name: firefox
            
            image: jlesage/firefox
            
            ports:
            
            -  '5800:5800'
            
            volumes:
            
            - ./firefox:/config:rw
            
            dns:
            
            - 10.1.0.222
            
            networks:
            
              bind9_subnet:
              
                ipv4_address: 10.1.0.221
                

          wireshark:
          
            image: lscr.io/linuxserver/wireshark:latest
            
            container_name: wireshark
            
            cap_add:
            
              - NET_ADMIN
              
            security_opt: 
            
              - seccomp:unconfined
              
            environment:
            
              - PUID=1000
              
              - PGID=1000
              
              - TZ=Europe/London
              
            volumes:
            
              - ./wireshark:/config
              
            ports:
            
              - '3000:3000'
              
            restart: unless-stopped


        networks:
        
            bind9_subnet:
            
                external: true
                
                
Una vez creado el fichero ya podremos usar el comando "docker-compose up" y nuestro contenedor estara listo para usarse.

## Crear los sitios 

Para crear los sitios, lo primero sera crear un directorio html, dentro de este habra otros dos directorios y dentro de cada uno abra un documento html especificando en que sitio estamos.

## Crear sitios virtuales

Para crear los sitios virtuales tendremos que ir a los archivos de configuración del apache y modificaremos varios ficheros:

* ports.conf 

Aquí vamos a fijar los puertos que vamos a escuchar

* sites-available

Dentro de este directorio encontraremos varios ficheros, en ellos establecemos el puerto que se va escuchar y vamos a mapear el DocumentRoot con la ruta del sitio al que va a apuntar ese puerto, luego copiaremos estos parametros que modificamos y los pegaremos en el directorio "sites-enabled".

## Seervidor DNS

Tambien tendremos que integrar un servidor DNS tal  y como hicimos en la practica de DNS_Linux para que pueda resolver los nombres de fabulas maravillosas y fabulas oscuras

## Integrar firefox

Para integrar firefox hemos añadido previamente el servicio en el docker-compose.yml, como especificamos el puerto 5800, para poder conectarnos tendremos que abrir el navegador y poner "localhost:5800"

## Añadir wireshark

Para añadir wireshark previamente lo hemos añadido en el docker-compose.yml, para usarlo tendremos que acceder a "localhost:3000"
