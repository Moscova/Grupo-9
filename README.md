# Grupo-9

Integrantes:

Nicolás Leonel Moscova Di Miro
Igor Boschetti

Grafico Topológico:

graph TD
    %% Definición de la Máquina Física y Conexión
    Host[🖥️ Máquina Física / Host] <-->|Red Local / Mismo Rango IP| Net[🌐 Interfaz de Red VM\nIP Estática, Netmask, Gateway]

    %% Contenedor Principal: Máquina Virtual
    subgraph VM [🐧 Máquina Virtual: Debian GNU/Linux (Hostname: TPServer)]
        direction TB
        Net
        
        %% Servicios de Red
        subgraph Servicios [⚙️ Servicios]
            SSH[🔐 SSH Server\nAcceso root por Clave Pública/Privada]
            Web[🌐 Apache + PHP >= 7.3\nSirviendo index.php y logo.png]
            DB[🗄️ MariaDB\nCarga de db.sql]
        end

        %% Almacenamiento y Discos
        subgraph Almacenamiento [💾 Almacenamiento]
            Disk1[(Disco 1\nOS Base)]
            Disk2[(Disco 2\n10 GB Adicional)]
            
            Part1[/Partición 1 tipo 83\n3 GB - Montaje: /www_dir/]
            Part2[/Partición 2 tipo 83\n6 GB - Montaje: /backup_dir/]
            
            Disk2 --> Part1
            Disk2 --> Part2
        end

        %% Tareas Automatizadas
        subgraph Automatizacion [🕒 Tareas Automáticas / Cron]
            BKP[📜 /opt/scripts/backup_full.sh]
            Cron((Cron Jobs))
            
            Cron -->|Diario 00:00| Log[/var/log/]
            Cron -->|L-M-V 23:00| Part1
            
            Log -.-> BKP
            Part1 -.-> BKP
            BKP -->|Guarda archivos .tar.gz| Part2
        end
        
        %% Conexiones internas
        Net --- Servicios
        Web -.->|Apunta a| Part1
    end
