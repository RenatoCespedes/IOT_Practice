# Practica de Internet de las Cosas
En este proyecto se instalará y configurará lo propuesto en el Capítulo 7 *Building the Critical Components* del libro **Build Your Own IoT Platform**.
## Requerimientos
- Docker

## Instalación

Los servicios a utilizarse se configurarón en un archivo [docker](docker-compose.yml), donde:

| SERVICIO| VERSIÓN|
| ----- | ---- |
| node-red | latest|
| mosquitto| 1.6|
| mysql| latest|
| phpmyadmin| latest|

Luego se ejecuta el siguiente comando para la instalación y el levantamiento de los servicios:
```bash
docker-compose up
```

## Importación de la Base de datos
Se realiza la importación del [archivo](base_de_datos/tSeriesDB.sql), el cual contiene la base de datos. Pero antes se explicara su creación, lo primero es la creación de la base de datos en phpmyadmin. para ello se ingresa al enlace http://localhost:8080/phpmyadmin/, seguido se hace click en la pestaña de **Bases de datos**. Lo último es colocarle el nombre de **tSeriesDB**, seleccionar la codificación de **utf8_general_ci** y click en **Crear**. Después se procede a crear la tabla que tendra el nombre de thingData, la cual es usada en este caso y tendra una cierta cantidad de columnas.

En el caso de la importación de la base de datos se procede a hacer lo siguientes pasos:
1. Selecciona la base de datos **tSeriesDB** .
2. Luego click en **Importar** se encuentra en la pestaña superior.
3. Busca el archivo [tSeriesDB.sql](base_de_datos/tSeriesDB.sql).
4. Por último, se hace click en el botón "Continuar" encontrado en el pie de página.

En el siguiente [enlace](https://help.wnpower.com/hc/es/articles/360043459551-Importar-tu-base-de-datos-MySQL-desde-un-archivo-SQL-en-phpMyAdmin) se encuentra este proceso con imagenes de referencia.

Además para acceder a la base de datos de series de tiempo, se crea un usuario especial y se le asigno una contraseña.

## Instalación de nodos requeridos en Node-RED

La instalación predeterminada de Node-RED no tiene un nodo para acceder a MySQL. Por lo tanto, se agrega este nodo usando el administrador de paleta.

1. Abré la interfaz **Node-RED** en el navegador.
2. Abré la **User Settings**.
3. Selecciona la opción **Manage Palette** en el menú que aparece. 
4. Selecciona el apartado **Install** dentro de **Manage Palette** y se escribe **MySQL** para enumerar los nodos disponibles. 
5. Selecciona e instala *node-red-node-MySQL*.

## Importación de Flujos
Los flujos se encuentran en  formato JSON, en la carpeta [flujos_red_node](flujos_red_node).
Para la importación solo se hace click en el icono de opciones(esquina superior derecha), luego en **import**. Seguido se agrega el archivo json a importar y se hace selección de nuevo diagrama. Este proceso se repite para los demas archivos json.

## Testing
* Para la prueba de [1_MQTT_Publish_Capability.json](flujos_red_node/1_MQTT_Publish_Capability.json), se puede observar los datos de salida en el debug del red node.

* Para la prueba de [2_REST_API_Message_Publisher.json](flujos_red_node/2_REST_API_Message_Publisher.json) se usa la siguiente estructura de comando:

  ```bash
  curl -X POST "localhost:1880/pub/'nombre_del_topico'/'nombre_del_metodo_payload'" -i
  ```

  El cual retorna un mensaje de la siguiente forma:
  ```{"success":true,"message":"published myTopic/myPayload"} ```

* Para la prueba de [3_Database_Listener.json](flujos_red_node/3_Database_Listener.json) se hace uso del siguiente comando de mosquitto:

  ```bash
    mosquitto_pub -h localhost -t topicoRedNode -m "Hola Mundo"
  ```
  Insertando el mensaje junto con el tópico en la base de datos.

* Para las prueba de [4_REST_API_Message_Retriever.json](flujos_red_node/4_REST_API_Message_Retriever.json) se usa los siguiente comando:

    1. Se usa para la extracción del ultimo elemento insertado en la base de datos dependiendo del tópico solicitado.

    ```bash
      curl -X GET "localhost:1880/get/myTopic" -i   #extrae el ultimo elemento insertado del tópico mytopic
    ```

    Oteniendo ```[{"id":8,"topic":"myTopic","payload":"myPayload","timestamp":"1543717154.899"}]```

    2. Se usa para la extracción de 1 o mas elementos insertados en la base de datos dependiendo del tópico solicitado.

    ```bash
      curl -X GET "https://www.in24hrs.xyz:1880/get/myTopic/last/3"  #extrae los 3 ultimos elementos insertados del tópico mytopic
    ```
    Obteniendo ```[{"id":8,"topic":"myTopic","payload":"myPayload", "timestamp":"1543717154.899"}, {"id":7,"topic":"myTopic","payload":"myPayload",
    "timestamp":"1543716966.189"}, {"id":6,"topic":"myTopic","payload":"myPayload", "timestamp":"1543717132.192"}] ```

## Integrantes

- Céspedes Fuentes, Renato
- Hermoza Loayza, Miguel
- Torres Rodríguez, Jaime
- Vicente Castro, Renzo

