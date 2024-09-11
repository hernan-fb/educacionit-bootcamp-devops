Clase 19


Vamos a ver Servicios Core de AWS: EC2, S3, S3 Glacier, EBS, EFS.

- EC2. Elastic Search 2.

- EBS es un disco. Elástico.

- EFS similar a EBS.

- S3 Glacier,
almacenamiento de archivos a largo plazo, es más económica porque se usa para hacer un backup de algo que no voy a utilizar de forma recurrente. S3 es para acceder a algo constantemente por ej un sitio web.

Sale mucho más barato un Glacier que uno normal.

AWS configure me pide ciertos pasos donde configuro cómo conectarme con el servidor de AWS.

AWS Access Key ID: Usuario
AWS Secret Access Key: Contraseña
Default region name: test
Default Output format: json

Cuando lo vuelvo a correr, ya tengo configurado esto.


Dentro del home tengo una carpeta 

.aws, donde voy a tener:

- config
- credentials


cat config

[default]
region = test
output = json



default es un profile.
No es lo más conveniente tenerlo el archivo en texto plano, porque cualquiera con acceso a mi máquina podría ver las credenciales.

Otra forma encripta esta información del profile con contraseña.

cat credentials:

[default]
aws_access_key_id = test
aws_secret_access_key = test


hay algo que da acceso provisorio donde provee usuario contraseña y token, el cual vence. Por ejemplo en entorno desarrollo vence en 8h y productivo vence en menos tiempo.


CloudFront, el usuario no puede acceder al bucket sino que está el CLoudFront en el medio. Esto se hace a través de un ASL, puede ser otro bucket, un EC2

AWS CloudFront 

Se denomina CDN (Content Delivery Network), se usa para entrega de contenido. Me va a dar este servicio una url, cuando pongo este scio pongo como un caché que sirve el contenido que indique.


Users <-> CloudFront <-> S3 (un bucket, su contenido, no la url)

Podría por ejemplo configurar que el unico con permisos para acceder al bucket sea el cloudfront.

Se utiliza para distintos servicios. El sentido es siempre el mismo, puedo servir distintos tipos de servicios.

Es una manera de cachear el contenido en una región cerca de donde estoy emplazado queriendo acceder a esos servicios.

Para hacer esto de cachear se usan los POP (Point Of Presence).

La ventaja de un cloudfront es básicamente que el contenido es más rápido y que puedo ponerle https al bucket, sino no podría usar https.

Hay distintos orígienes que se pueden utilizar para servir el contenido de un CloudFront: Un loadbalancer, EC2, S3, etc.


Ejercicio clase 19 (ver archivo en carpeta Clase19)

- construir comando:
aws s3 cp hernan.fb.txt s3:/educacionit-bucket-01/

chmod +x upload_to_s3.sh

./upload_to_s3.sh

-------------

Ahora vamos a poner parámetros:


aws s3 cp $2 $4


al ingresar ./upload_to_s3.sh -algo hernan.fb.txt s3://educacionit-bucket-01/

--------------

si agrego:

status=$?

if [ $status -eq 0 ]; then
	echo "archivo subido con exito"
else
	echo "Error al subir el archivo"
fi

--------------

aws s3 cp $2 $4 > /dev/null

de esta manera la salida de la ejecución no se manda al std output.

--------------
