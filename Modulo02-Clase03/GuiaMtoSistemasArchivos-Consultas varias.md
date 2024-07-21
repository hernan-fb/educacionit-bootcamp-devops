#### Ver el tipo de sistema de archivos: 
* file -sL /dev/sda1

#### Mostrar inodos libres: 
* df -i

#### Mostrar espacio: 
* df -h

#### Mostrar listado de particiones, espacio ocupado y punto de montaje: 
* df -h | grep '^/dev' | tr -s ' ' | cut -d' ' -f1,2,6

#### Espacio total ocupado por /etc: 
* du -sch /etc

#### Espacio total ocupado por distintos directorios: 
* du -sch /*

#### Traer los 10 archivos más grandes en el directorio actual: 
* du -sm * | sort -nr | head