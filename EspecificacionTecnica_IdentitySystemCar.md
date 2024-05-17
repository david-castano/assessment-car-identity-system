# <span style="color:DodgerBlue">Especificación Técnica del Servicio</span>: IdentitySystemCar_v1.0
 
## <span style="color:DodgerBlue">1. Control de Cambios del Documento</span>

| Fecha | Motivo | Realizado por |
|-|:-:|:-:|
|*2024/05/12*|*Creación del Documento*|*David Julian Castaño Ramirez*|
| |  |  |

## <span style="color:DodgerBlue">2. Documentos de Referencia</span>

| Nro | Nombre | Descripción |
|:-:|-|-|
| 1 |**Contratos** (Especificación técnica del api)| [vehicles-api](contratos/vehiculos-api/vehicles-api/vehicles-api.raml)<br> [users-api](contratos/users-api/users-api/users-api.raml)<br> [trips-api](contratos/trips-api/trips-api/trips-api.raml)<br> [alerts-api](contratos/alerts-api/alerts-api/alerts-api.raml)<br> [stadistics-api](contratos/stadistics-api/stadistics-api/stadistics-api.raml)|
| 2 |[**Diagrama de Componentes**](carIdentitySystem/Diagrama%20de%20componentes.drawio.png)|Diagrama que muestra todos los componentes que interactúan en la solución|
||||

## <span style="color:DodgerBlue">3. Protocolos de seguridad en el desarrollo</span>

- **HTTPS**: Protocolo de seguridad de la comunicación HTTP via web.
- **MFA**: Multiple factor autentication. Autenticación en multiples factores.
- **KMS**: Cifrado en transito y en reposo de los datos a través del flujo.
- **Cognito y lambda authorizer**: Control de acceso basado en roles.
- **Protección contra ataques**: WAF Wall application firewall. Corta fuego contra consumos inusuales.


## <span style="color:DodgerBlue">4. Monitoreo y trazabilidad</span>

| AWS Event bridge | AWS Cloud watch | AWS RedShift | 
|:-:|:-:|:-:|
| Nos permite tener una arquitectura basada en eventos donde es nuestra solución serán notificados los eventos relevantes al sistema Integrado de monitoreo que posee CCS|Aquí podremos ver las alertas personalizadas para ser rastreadas en el entorno de AWS |BigData generada por la operación en linea de los sensores de control, mostrandonos KPIs de negocio, metricas, recorridos y demás datos importantes para la toma de decisiones|
||||||

- Anotaciones importantes:
> Al realizar una solicitud a alguno de los servicios, podremos tener los logs de las transacciones a través de cloudWatch.

> En nuestra arquitectura basada en eventos podremos ver los mensajes entre componentes a través del event bridge el cual también tiene el stack de cloudWatch.

> A nivel de negocio y captura en "tiempo real" de las ubicación de un viaje, podremos visualizarlas a nivel de AWS RedShift y Quicksight.

## <span style="color:DodgerBlue">5. Diagrama de Componentes</span>

En esta sección se  muestran  los componentes detallado que hacen parte de la realización del servicio y la ubicación de estos componentes  en los entornos de ejecución:

![Diagrama de Componentes](Identity%20system%20car/Diagrama%20de%20componentes.drawio.png)

| Componente | Función |
|-|-|
|*react.js and react native*|*se suguiere estas tecnologias por su capacidad reactiva a nivel de aplicación frontend, sin embargo posiblemente los servicios sean consumidos directamente por el sensor control a través de un hardware microTic, y los software integrados consumirían desde sus arquitecturas*|
|*Route 53*|*Elemento encargado en redireccionar las peticiones según la salud y el balanceo de cargas hacia otra zona de disponibilidad*|
|*us-east-2*|*Zona de disponibilidad alternativa donde se encuentran desplegados componentes serverless para no afectación de facturación doble y donde se recibirán peticiones en caso de desborde o anulación de la zona ppal (us-east-1)*|
|*API Gateway*|*Administración de end-points con propiedades de trazabilidad.*|
|*WAF*|*Wall application Firewall, es un firewall de aplicación que nos permitirá tener control contra posibles ataques de denegación de acceso y solicitudes irregulares*|
|*Lambda function Authorizer*|*Servicio serverless para proveer una capa de seguridad y tokenización de accesos*|
|*Cognito*|*Base de datos orientada al resguardo de secretos y accesos del ecosistema planteado.*|
|*Secret manager y AWS KMS*|*Encripción y desencripción de mensajes a lo largo del flujo, para lograr una seguridad en transito y reposo*|
|*Lambda*|*Mediante estas funciones lambda recibiremos los mensajes y realizaremos la obtención o registro de los datos del sistema*|
|*Rol AutoScaling*|*Configuración lambda para escalado vertical de la aplicación por uso concurrente*|
|*DynamoDB*|*Base de datos NoSQL donde tendremos almacenados los datos capturados por los sensores o los actores del sistema*|
|*RedShift*|*Base de datos donde podremos analizar las metricas, KPIs y demás medidas del negocio, altamente escalable y rapido*|
|*Quicksight*|*Panel de visualización para toma de decisiones y análisis de comportamientos*|
|*CloudWatch*|*Componente transversal que nos permitirá tener visualización de logs de los movimientos del sistema y generación de alertas operativas que consideremos necesarias para el monitoreo del sistema*|
|*CloudTrail*|*Componente transversal que nos permitirá el certeza de quien hizo que. Registrando las llamadas a cada componente*|
|*Event bridge*|*Administración de eventos para desencadenar acciones.*|
|*Sistema de monitoreo (externo)*|*Sistema desde donde CSS monitorea sus grupos de carros*|
|*Maps*|*Sistema de mapas donde podremos por medio de cordenadas, obtener información precisa de desplazamientos*|
|*BD Clientes*|*Base de datos de clientes de la compañía*|
|||



**DETALLE:**

[1. El sistema de monitoreo debe realizar la inscripción inicial del vehiculo y usuario. Desde este primer paso podrán también posteriormente ser consumidos los demás servicios.]

[2. El servicio Route 53 se encargará de evaluar la salúd y balanceo de cargas para elegir una zona pertinente según la concurrencia o disponibilidad]

[3. El apigateway nos proveerá control y administración de las solicitudes y ademas podremos utilizar las ventajas del WAF como firewall a solicitudes irregulares y encripción del mensaje en transito via KMS y secret manager]

[4. Por medio de la lambda autorizadora y cognito administraremos el acceso a las diferentes APIs con tokenización]

[5. El Event Bridge nos ayudará a tener unas acciones basadas en eventos para varios integraciones como lo son las lambdas de interfaz para el negocio, el sistema de monitoreo, maps, la BD de clientes, DynamoDB y RedShift]

[6. Procedemos al guardado de la API de entidad en la colección DynamoDB]

[7. Desde RedShift se configurarán las reglas de negocio, metricas, estadisticas y análisis de datos en general para la toma de decisiones y posterior visualización en AWS QuickSight]

[8. Por medio del token obtenido podremos consumir las API de negocio.]



## <span style="color:DodgerBlue">6. Decisiones de Realización</span>


En esta sección se muestran componentes implementados en unidades de despliegue dependiendo de la plataforma.

| Decisión | Descripción |
|-|-|
| Tema | *Nube* |
| Descripción del Problema | *Es necesario tener escalabilidad, resiliencia a fallos, seguridad, alta disponibilidad* |
| Alternativas |  *Data center OnPremise*  |
| Decisión | *Utilización de AWS services* |
| Implicaciones | *Ninguna* |

| Decisión | Descripción |
|-|-|
| Tema | *Resiliencia a fallos* |
| Descripción del Problema | *En caso de una falla en la disponibilidad de una zona AWS* |
| Alternativas |  *Rol autoScaling, Route 53*  |
| Decisión | *Route 53 y duplicación de recursos en zona de disponibilidad alternativa* |
| Implicaciones | *En caso de una falta de disponibilidad de la zona AWS, Route 53 evaluará la salúd de la conectividad y disponibilidad y hasta balanceo de cargas para redirigir el trafico hacia us-east2*|

| Decisión | Descripción |
|-|-|
| Tema | *Diseño* |
| Descripción del Problema | *Al ser un reporte de estado de ubicación o intención de destino se pensó en el patrón de observer, pero también cabe la posibilidad de publicador* |
| Alternativas |  *Observer*  |
| Decisión | *Publicador* |
| Implicaciones | *Se toma la decisión de utilizar el patrón de publish, sin embargo al recibir el mensaje se trata con un bus de eventos para desencadenar el flujo de software* |

| Decisión | Descripción |
|-|-|
| Tema | *Almacenamiento* |
| Descripción del Problema | *Definir tipo de almacenamiento* |
| Alternativas |  *Estructurado o no estructurado*  |
| Decisión | *NoEstructurado* |
| Implicaciones | *Se manejará una BD NoSQL DynamoDB, sin embargo internamente maneja referencias a ids de las colecciones de entidad del sistema. Se optó por este tipo de almacenamiento por la rapidez en consultas que brinda.* |

| Decisión | Descripción |
|-|-|
| Tema | *Autenticación y seguridad* |
| Descripción del Problema | *Definir sistema de autenticación y seguridad de los mensajes* |
| Alternativas |  *JWT, OAuth 2.0, Lambda Authorizer y cognito*  |
| Decisión | *Lambda authorizer y cognito* |
| Implicaciones | *A los consumidores se les entregará un usuario y password el cual generará una autenticación de doble factor la cual se podrá validar desde cognito.Además el mensaje apenas llegue al Event Bridge se encriptará según un secreto administrado por secret manager para asegurar la seguridad del mensaje en transito*|

| Decisión | Descripción |
|-|-|
| Tema | *Arquitectura* |
| Descripción del Problema | *Definir sistema consumo* |
| Alternativas |  *Consumo directo de lambdas, Utilización de Event Bridge como iPaaS*  |
| Decisión | *Event Bridge* |
| Implicaciones | *Este componente se comportará como el router central de la arquitectura donde se direccionarán los flujos necesarios basados en eventos para transportar la información*|

| Decisión | Descripción |
|-|-|
| Tema | *Análisis de datos* |
| Descripción del Problema | *Análisis de datos y visualizaciones para toma de decisiones* |
| Alternativas |  *Topologia estrella en DynamoDB y visualización en QuickSight, Transporte de datos a RedShift*  |
| Decisión | *RedShift y QuickSight* |
| Implicaciones | *Este componente se comportará como el router central de la arquitectura donde se direccionarán los flujos necesarios basados en eventos para transportar la información*|

## <span style="color:DodgerBlue">7. Consideraciones de seguridad</span>

> El cliente contará con un nombre de usuario y clave para autenticarse, con esta autenticación se generará un secreto de segundo factor de autenticación.

> Los mensajes de extremo a extremo serán cubiertos. En primera instancia por el protocolo HTTPS donde se expone el endpoint y después del apigateway por un secreto y encripción administrado por secret manager y AWS KMS.

> Algunos de los servicios exigen el envío del id de entidad, lo cual asegurará la integridad de la información a pesar del tipo de BD No relacional, lo cual deberá ser controlado por la lambda de interfaz.

## <span style="color:DodgerBlue">8. Recomendaciones de implementación </span>

> Recomendamos empezar una prueba piloto con un lote pequeño de vehiculos, para establecer las metricas que puedan hacer sentido a la toma de decisiones [PMV - Proyecto Mínimo Viable](https://www.pragma.co/es/blog/que-es-el-minimo-producto-viable-y-como-definirlo) como también para probar en producción el apagado de una zona de disponibilidad completa y corroborar el correcto funcionamiento del route 53 y su direccionamiento hacia la nueva zona, como el ajuste de reglas cloudWatch para el monitoreo necesario.

## <span style="color:DodgerBlue">8. Despliegue de la solución </span>

> Se deberá contar con un pipeline de release en azure devops que sea auto ejecutado posterior a un pull request en pares para verificación del código y este pipeline deberá contener pruebas de integración a las API de entidad.





