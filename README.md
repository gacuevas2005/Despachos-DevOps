# 📦 Microservicio de Gestión de Despachos (InnovaTech Chile)

Este módulo forma parte del ecosistema backend distribuido de **InnovaTech Chile**, encargado exclusivamente de procesar, actualizar, auditar y persistir la lógica de negocio asociada al ciclo de vida de los despachos logísticos. Desarrollado bajo una arquitectura desacoplada y apátrida (*stateless*), el servicio se encuentra optimizado para su despliegue en contenedores Docker y su orquestación en la nube (AWS).

## 🚀 Ficha Técnica y Tecnologías
* **Framework Principal:** Spring Boot 3.4.4 (Spring Web, Validation)
* **Capa de Datos:** Spring Data JPA + Hibernate ORM
* **Motor Base de Datos:** MariaDB / MySQL 8.0+
* **Lenguaje:** Java 17 (OpenJDK)
* **Gestor de Dependencias:** Maven 3+
* **Documentación Viva:** OpenAPI 3 / Springdoc Swagger UI v2.7.0

---

## 🛠️ Requisitos Previos (Local)
Antes de levantar el servicio, asegúrate de contar con:
1.  **Java Development Kit (JDK) 17** configurado en tus variables de entorno (`JAVA_HOME`).
2.  **Apache Maven** instalado (u operar mediante el wrapper `./mvnw`).
3.  Una instancia activa de **MySQL o MariaDB** (Local o vía Docker).

---

## ⚙️ Variables de Entorno Requeridas
El archivo `application.properties` está parametrizado para evitar fugas de credenciales en el código fuente. Debes proveer las siguientes variables en tu entorno de desarrollo o en tu orquestador Docker:

| Variable | Descripción | Ejemplo Local |
| :--- | :--- | :--- |
| `DB_ENDPOINT` | Dirección IP o Host del motor de Base de Datos | `localhost` |
| `DB_PORT` | Puerto de escucha del motor SQL | `3306` |
| `DB_NAME` | Nombre de la base de datos del negocio | `innovatech_db` |
| `DB_USERNAME` | Usuario con privilegios de lectura/escritura | `root` |
| `DB_PASSWORD` | Contraseña segura de acceso | `tu_password_seguro` |

---

## ⚡ Instrucciones de Ejecución

### Opción A: Compilación y Ejecución Tradicional (Maven)
1. Clona el repositorio y muévete a la raíz de este microservicio.
2. Inyecta las variables de entorno en tu terminal y empaqueta el artefacto `.jar`:
   ```bash
   mvn clean package -DskipTests
Ejecuta el binario compilado:

Bash
java -jar target/Springboot-API-REST-DESPACHO-0.0.1-SNAPSHOT.jar
Opción B: Construcción de Imagen Docker (Modo Producción)
El pipeline de CI/CD automatiza este paso utilizando la configuración nativa de Docker. Para replicarlo localmente:

Bash
docker build -t innovatech-backend-despacho:latest .
📑 Documentación Interactiva de la API (Swagger)
Una vez que el servicio se encuentra arriba en el puerto 8091, puedes acceder a la interfaz gráfica interactiva de Swagger para validar los endpoints y sus contratos de datos en tiempo real:

URL de Swagger UI: http://localhost:8091/swagger-ui.html

🛣️ Catálogo de Endpoints (API Reference)
Todos los endpoints base están montados sobre la ruta raíz: /api/v1/despachos

1. Obtener todos los despachos
   Método: GET

Ruta: /api/v1/despachos

Respuesta Exitosa: 200 OK (Retorna un arreglo JSON).

2. Obtener un despacho por ID
   Método: GET

Ruta: /api/v1/despachos/{idDespacho}

Parámetro: idDespacho (Long)

Respuestas: 200 OK / 404 Not Found

3. Crear un nuevo despacho (Ideal para pruebas en Postman)
   Método: POST

Ruta: /api/v1/despachos

Cabecera Obligatoria: Content-Type: application/json

Cuerpo de la Petición (Payload):

JSON
{
"fechaDespacho": "2026-05-17",
"patenteCamion": "CAMION-AWS-LIVE",
"intento": 1,
"idCompra": 105,
"direccionCompra": "Av. Melipilla 456, Demo AWS",
"valorCompra": 7500,
"despachado": true
}
Respuesta Exitosa: 201 Created (Incluye cabecera Location apuntando al recurso creado).

4. Actualizar un despacho existente
   Método: PUT

Ruta: /api/v1/despachos/{idDespacho}

Cabecera Obligatoria: Content-Type: application/json

Respuesta Exitosa: 200 OK (Retorna la entidad con las modificaciones validadas).

5. Eliminar un despacho por ID
   Método: DELETE

Ruta: /api/v1/despachos/{idDespacho}

Respuesta Exitosa: 204 No Content (Confirmación de eliminación física/lógica).