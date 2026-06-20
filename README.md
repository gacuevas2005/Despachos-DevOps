# 📦 Microservicio de Gestión de Despachos (Grupo Cordillera / InnovaTech Chile)

Este módulo forma parte del ecosistema backend distribuido de InnovaTech Chile, encargado exclusivamente de procesar, actualizar, auditar y persistir la lógica de negocio asociada al ciclo de vida de los despachos logísticos. 

Desarrollado bajo una arquitectura desacoplada y apátrida (stateless), el servicio ha sido evolucionado para su despliegue nativo en la nube, operando dentro de contenedores orquestados en **Amazon EKS (Kubernetes)** y automatizado mediante integración y despliegue continuo (CI/CD).

## 🚀 Ficha Técnica y Tecnologías

*   **Framework Principal:** Spring Boot 3.4.4 (Spring Web, Validation)
*   **Capa de Datos:** Spring Data JPA + Hibernate ORM
*   **Motor Base de Datos Cloud:** Amazon RDS (MySQL/MariaDB)
*   **Lenguaje:** Java 17 (OpenJDK)
*   **Gestor de Dependencias:** Maven 3+
*   **Orquestación y Escalabilidad:** Amazon EKS & Horizontal Pod Autoscaler (HPA)
*   **Registro de Contenedores:** Amazon ECR
*   **CI/CD:** GitHub Actions
*   **Documentación Viva:** OpenAPI 3 / Springdoc Swagger UI v2.7.0

---

## ☁️ Novedades Arquitectónicas (Evaluación 3)

Para esta fase del proyecto, el microservicio ha sido migrado a una arquitectura Cloud-Native de nivel empresarial, implementando las siguientes mejoras:

*   **Orquestación con Amazon EKS:** El servicio ya no se ejecuta en contenedores aislados, sino dentro de un clúster de Kubernetes, garantizando alta disponibilidad.
*   **Autoescalado Dinámico (HPA):** Se configuró el *Horizontal Pod Autoscaler*, permitiendo que el microservicio replique sus pods automáticamente en función del consumo de CPU para soportar picos de tráfico.
*   **Pipeline CI/CD Automatizado:** A través de GitHub Actions, cada *push* a la rama principal detona un flujo que compila el proyecto, construye la imagen Docker, la sube de forma segura a **Amazon ECR** (reemplazando a Docker Hub) y actualiza los manifiestos en el clúster (Zero Downtime Deployment).
*   **Seguridad y Secrets:** Las credenciales de la base de datos RDS nunca se exponen en el código fuente. Se inyectan en tiempo de ejecución utilizando **Kubernetes Secrets**.
*   **Proxy Inverso y Redes Privadas:** Este backend opera de forma segura en la red interna del clúster (Puerto 8091). No está expuesto directamente a internet; todo el tráfico entrante es gestionado y enrutado a través del servidor Nginx configurado en el Frontend, eliminando los errores de CORS.

---

## ⚙️ Variables de Entorno y Seguridad

El archivo `application.properties` está parametrizado. En el entorno de producción (AWS), estas variables son aprovisionadas por Kubernetes. Para entornos locales, debes proveerlas manualmente:

| Variable | Descripción | Ejemplo |
| :--- | :--- | :--- |
| `DB_ENDPOINT` | Dirección IP o Host del motor de Base de Datos (URL de RDS) | `innovatech-db.cxxx.us-east-1.rds.amazonaws.com` |
| `DB_PORT` | Puerto de escucha del motor SQL | `3306` |
| `DB_NAME` | Nombre de la base de datos del negocio | `innovatech_db` |
| `DB_USERNAME` | Usuario con privilegios de lectura/escritura | `admin` |
| `DB_PASSWORD` | Contraseña segura de acceso | `********` |

---

## ⚡ Instrucciones de Ejecución (Local)

### Opción A: Compilación y Ejecución Tradicional (Maven)
1. Clona el repositorio y muévete a la raíz de este microservicio.
2. Inyecta las variables de entorno en tu terminal y empaqueta el artefacto `.jar`:
```bash
   mvn clean package -DskipTests
Ejecuta el binario compilado:

Bash
   java -jar target/Springboot-API-REST-DESPACHO-0.0.1-SNAPSHOT.jar
Opción B: Pruebas con Docker
Para construir la imagen simulando el proceso del pipeline antes de subirla a ECR:

Bash
docker build -t innovatech-backend-despacho:latest .
docker run -p 8091:8091 --env-file .env innovatech-backend-despacho:latest
📑 Documentación Interactiva de la API (Swagger)
Una vez que el servicio se encuentra arriba en el puerto 8091 (ya sea localmente o mediante kubectl port-forward hacia el clúster EKS), puedes acceder a la interfaz gráfica interactiva de Swagger:

URL de Swagger UI: http://localhost:8091/swagger-ui.html

🛣️ Catálogo de Endpoints (API Reference)
Todos los endpoints base están montados sobre la ruta raíz: /api/v1/despachos

Obtener todos los despachos
Método: GET

Ruta: /api/v1/despachos

Respuesta Exitosa: 200 OK (Retorna un arreglo JSON).

Obtener un despacho por ID
Método: GET

Ruta: /api/v1/despachos/{idDespacho}

Parámetro: idDespacho (Long)

Respuestas: 200 OK / 404 Not Found

Crear un nuevo despacho
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
    ```
*   **Respuesta Exitosa:** `201 Created` (Incluye cabecera `Location` apuntando al recurso creado).

### Actualizar un despacho existente
*   **Método:** `PUT`
*   **Ruta:** `/api/v1/despachos/{idDespacho}`
*   **Cabecera Obligatoria:** `Content-Type: application/json`
*   **Respuesta Exitosa:** `200 OK` (Retorna la entidad con las modificaciones validadas).

### Eliminar un despacho por ID
*   **Método:** `DELETE`
*   **Ruta:** `/api/v1/despachos/{idDespacho}`
*   **Respuesta Exitosa:** `204 No Content` (Confirmación de eliminación física/lógica).
