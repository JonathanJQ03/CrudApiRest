# Aplicación Full Stack: Gestión de Productos y Categorías

Este proyecto consiste en una aplicación web full stack con funcionalidades CRUD para gestionar productos y categorías. La arquitectura se divide en tres capas:

- **Base de datos**: MySQL con dos tablas relacionadas.
- **Backend**: Dos servicios independientes desarrollados con Spring Boot que exponen APIs REST.
- **Frontend**: Aplicación desarrollada con Angular que consume ambas APIs para permitir la gestión desde una interfaz gráfica.

---

## Estructura del Proyecto

```
fullstack-crud/
├── backend/
│   ├── product-service/         # API REST de productos (puerto 8081)
│   └── category-service/        # API REST de categorías (puerto 8082)
├── frontend/                    # Aplicación Angular (puerto 4200)
├── mysql/
│   └── init.sql                 # Script de creación de tablas
├── docker-compose.yml           # Orquestación de servicios
└── README.md
```

---

## Modelo de Datos

### Tabla `categories`

| Campo        | Tipo         | Descripción                     |
|--------------|--------------|---------------------------------|
| `id`         | INT (PK)     | Identificador único             |
| `name`       | VARCHAR(100) | Nombre de la categoría          |
| `description`| TEXT         | Descripción de la categoría     |

### Tabla `products`

| Campo        | Tipo         | Descripción                             |
|--------------|--------------|-----------------------------------------|
| `id`         | INT (PK)     | Identificador único                     |
| `name`       | VARCHAR(100) | Nombre del producto                     |
| `price`      | DECIMAL      | Precio del producto                     |
| `description`| TEXT         | Descripción del producto                |
| `category_id`| INT (FK)     | Relación con `categories(id)`           |

---

## Backend - Servicios REST

### Product Service (http://localhost:8081/api/products)

| Método | Endpoint             | Descripción                         |
|--------|----------------------|-------------------------------------|
| GET    | `/api/products`      | Listar todos los productos          |
| GET    | `/api/products/{id}` | Obtener un producto por ID          |
| POST   | `/api/products`      | Crear un nuevo producto             |
| PUT    | `/api/products/{id}` | Actualizar un producto existente    |
| DELETE | `/api/products/{id}` | Eliminar un producto                |

### Category Service (http://localhost:8082/api/categories)

| Método | Endpoint               | Descripción                           |
|--------|------------------------|---------------------------------------|
| GET    | `/api/categories`      | Listar todas las categorías           |
| GET    | `/api/categories/{id}` | Obtener una categoría por ID          |
| POST   | `/api/categories`      | Crear una nueva categoría             |
| PUT    | `/api/categories/{id}` | Actualizar una categoría existente    |
| DELETE | `/api/categories/{id}` | Eliminar una categoría                |

---

## Frontend

- Framework: Angular
- Consumo de ambas APIs REST mediante servicios HTTP
- Funcionalidades principales:
  - Listar, crear, editar y eliminar productos
  - Listar, crear, editar y eliminar categorías
  - Relacionar productos con categorías existentes

---

## Archivos esenciales

### docker-compose.yml

```yaml
version: '3.8'

services:
  mysql:
    image: mysql
    container_name: mi-mysql-jaguaco
    environment:
      MYSQL_ROOT_PASSWORD: admin123
      MYSQL_DATABASE: test
    ports:
      - "3307:3306"
    volumes:
      - mysql-data:/var/lib/mysql

  backend-products:
    image: img-products:v1.1
    container_name: c-app-products-v1.0-jaguaco
    ports:
      - "8081:8003"
    environment:
      SPRING_DATASOURCE_URL: jdbc:mysql://mysql:3306/test
      SPRING_DATASOURCE_USERNAME: root
      SPRING_DATASOURCE_PASSWORD: admin123
    depends_on:
      - mysql

  backend-category:
    image: img-category:v4
    container_name: c-app-category-jaguaco
    ports:
      - "8082:8008"
    environment:
      SPRING_DATASOURCE_URL: jdbc:mysql://mysql:3306/test
      SPRING_DATASOURCE_USERNAME: root
      SPRING_DATASOURCE_PASSWORD: admin123
    depends_on:
      - mysql

  frontend:
    image: crud-angular-img:v1.0
    container_name: c-app-angular-jaguaco
    ports:
      - "4200:80"
    depends_on:
      - backend-products
      - backend-category

volumes:
  mysql-data:

```

### mysql/init.sql

```sql
CREATE TABLE IF NOT EXISTS categories (
  id INT AUTO_INCREMENT PRIMARY KEY,
  name VARCHAR(100) NOT NULL,
  description TEXT
);

CREATE TABLE IF NOT EXISTS products (
  id INT AUTO_INCREMENT PRIMARY KEY,
  name VARCHAR(100) NOT NULL,
  price DECIMAL(10,2) NOT NULL,
  description TEXT,
  category_id INT,
  FOREIGN KEY (category_id) REFERENCES categories(id) ON DELETE SET NULL
);
```

---


## Instrucciones para levantar el proyecto

1. **Clonar el repositorio**


2. **Acceder a la carpeta raíz del proyecto**

   ```bash
   cd proyecto-docker
   ```

3. **Levantar los servicios con Docker Compose**

   Asegúrate de tener Docker Desktop en ejecución.

   ```bash
   docker compose up 
   ```

4. **Verificar en Docker Desktop**

   - Asegúrate de que los siguientes contenedores estén levantados:
     - `mysql-crud` (base de datos)
     - `product-service`
     - `category-service`

5. **Levantar el frontend (Angular)**

   En una nueva terminal:

   ```bash
   cd frontend
   npm install
   ng serve
   ```

   Esto iniciará el frontend en `http://localhost:4200`.

6. **Acceder a la aplicación**

   Abre tu navegador y navega a:

   ```
   http://localhost:4201
   ```

   Desde allí podrás interactuar con la aplicación y realizar operaciones CRUD tanto de productos como de categorías.

---

## Licencia

Este proyecto se distribuye bajo la licencia MIT.
