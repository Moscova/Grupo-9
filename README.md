# Grupo-9

Integrantes:

Nicolás Leonel Moscova Di Miro
Igor Boschetti

Grafico Topológico:

graph TD
    Host[Maquina Fisica Host / Cliente] <-->|Adaptador Puente: Mismo Rango IP| Network[Red: IP Estatica, Netmask, Gateway]
    
    subgraph VM [Maquina Virtual: Debian GNU/Linux - Hostname: TPServer]
        
        subgraph Servicios [1. Servicios Expuestos]
            SSH[SSH Server: Acceso root por claves RSA]
            Apache[Apache + PHP 7.3: index.php y logo.png]
            DB[MariaDB: Base de datos db.sql]
        end
        
        subgraph Almacenamiento [2. Almacenamiento y Discos]
            SDA[(Disco Base: /dev/sda)]
            SDC[(Disco Adicional 10 GB: /dev/sdc)]
            
            Part1[/Particion 1 tipo 83: 3 GB<br>Montaje: /www_dir/]
            Part2[/Particion 2 tipo 83: 6 GB<br>Montaje: /backup_dir/]
            
            SDC --> Part1
            SDC --> Part2
        end
        
        subgraph Tareas [3. Automatizacion de Backups]
            Cron((Crontab))
            Script[Script: /opt/scripts/backup_full.sh]
            
            Cron -->|Todos los dias 00:00| LogDir[/var/logs/]
            Cron -->|L, M, V 23:00| Part1
            
            LogDir -.-> Script
            Part1 -.-> Script
            Script -->|Almacena archivos .tar.gz| Part2
        end
        
        Network --> SSH
        Network --> Apache
        Apache -.->|Apunta DocumentRoot a| Part1
    end
