# Grupo-9

Integrantes:

Nicolás Leonel Moscova Di Miro
Igor Boschetti

Grafico Topológico:

flowchart TD
    %% Estilos
    classDef host fill:#e1f5fe,stroke:#039be5,stroke-width:2px;
    classDef vm fill:#f3e5f5,stroke:#8e24aa,stroke-width:2px;
    classDef service fill:#e8f5e9,stroke:#43a047,stroke-width:1px;
    classDef storage fill:#fff3e0,stroke:#fb8c00,stroke-width:1px;
    classDef network fill:#fce4ec,stroke:#d81b60,stroke-width:1px;

    %% Nodos principales
    Host["💻 Máquina Física (Host)\n(Cliente Web / Cliente SSH)"]:::host
    
    subgraph Red["Configuración de Red Local (Bridge / Adaptador Puente)"]
        IP_Est["IP Estática: En rango de la red física\nNetmask: Definida\nGateway: Definido"]:::network
    end

    subgraph VM["Servidor Debian (Máquina Virtual)"]
        direction TB
        Hostname["Hostname: TPServer"]:::vm
        
        subgraph Servicios["Servicios y Aplicaciones"]
            SSH["🔐 SSH Server\n(Acceso root con clave pública/privada)"]:::service
            Web["🌐 Apache + PHP (>= 7.3)\n(Sirviendo index.php y logo.png)"]:::service
            DB["🗄️ MariaDB\n(Base de datos: db.sql)"]:::service
        end

        subgraph Almacenamiento["Almacenamiento (Discos y Particiones)"]
            SDA[(" Disco Base (/dev/sda)\nSistema Operativo")]:::storage
            SDC[(" Disco Adicional (/dev/sdc - 10 GB)\nTipo 83 (Linux)")]:::storage
            
            PartWWW["📁 /www_dir (3 GB)\n(Montaje automático)\n(Apunta a Apache)"]:::storage
            PartBKP["📁 /backup_dir (6 GB)\n(Montaje automático)\n(Almacena los .tar.gz)"]:::storage
        end
        
        subgraph Scripts["Automatización (Crontab)"]
            Cron["⚙️ /opt/scripts/backup_full.sh\nDiario 00:00 -> /var/logs\nL,M,V 23:00 -> /www_dir"]:::service
        end
    end

    %% Conexiones
    Host <-->|Peticiones HTTP/SSH| Red
    Red <-->|eth0 / enp0s3| Hostname
    Hostname --- Servicios
    Hostname --- Almacenamiento
    Hostname --- Scripts
    
    SDC --> PartWWW
    SDC --> PartBKP
    
    Web -.-|Lee archivos web desde| PartWWW
    Cron -.-|Guarda backups en| PartBKP
