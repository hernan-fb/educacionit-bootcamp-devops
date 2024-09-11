Clase 17


PaaS (Platform as a Service)

Heroku: Plataforma de desarrollo y despliegue de aplicaciones web.
Google App Engine: Plataforma para desarrollar y hospedar aplicaciones web y móviles escalables.
Microsoft Azure App Service: Plataforma para crear, implementar y escalar aplicaciones web y móviles.
AWS Elastic Beanstalk: Servicio de despliegue, escalamiento y administración de aplicaciones.
Salesforce Force.com: Plataforma de desarrollo de aplicaciones empresariales basada en la nube.



el 26 de septiembre va a ver una evento en Argentina que se llama NERDEARLA que se hace de forma presencial y virtual


https://nerdear.la/es/



Tambien esta https://ekoparty.org/



Un webhook es un método de comunicación entre aplicaciones que permite que una aplicación envíe datos a otra en tiempo real, tan pronto como ocurra un evento específico. En lugar de que una aplicación tenga que consultar regularmente (polling) a otra para obtener actualizaciones, el webhook permite que la aplicación que recibe datos se "suscriba" a eventos de la aplicación que envía los datos.

Características de los webhooks:
HTTP POST: Cuando ocurre el evento en la aplicación emisora, se envía una solicitud HTTP POST a una URL predefinida en la aplicación receptora, junto con información sobre el evento.

Eventos específicos: Los webhooks suelen ser configurados para reaccionar a eventos específicos, como la creación de un nuevo usuario, un pago, un cambio de estado, etc.

Asincrónico: Permiten comunicaciones asíncronas, lo que significa que el receptor no tiene que esperar a que la emisora le envíe los datos. Esto puede mejorar la eficiencia y la capacidad de respuesta de las aplicaciones.

Configuraciones: Usualmente, los desarrolladores configurarán los webhooks en la aplicación que envía datos, definiendo qué eventos desecharán y a qué URL se enviarán los datos.

Ejemplo de uso:
Imagina un sistema de gestión de proyectos donde, al ser actualizado el estado de una tarea, se envía un webhook a una aplicación de chat (como Slack). Este webhook podría contener datos sobre la tarea actualizada, y Slack puede usar esa información para enviar un mensaje en un canal específico, informando a los miembros del equipo sobre el cambio.

En resumen, los webhooks son una forma eficiente y en tiempo real de comunicar eventos entre aplicaciones, eliminando la necesidad de verificar activamente cambios.






