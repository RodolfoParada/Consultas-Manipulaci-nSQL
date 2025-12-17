-- 1. Crear la base de datos si no existe
CREATE DATABASE IF NOT EXISTS ecommerce_db;
USE ecommerce_db;

-- 2. Crear la tabla USUARIOS
CREATE TABLE Usuarios (
    id_usuario INT AUTO_INCREMENT PRIMARY KEY,
    nombre VARCHAR(100) NOT NULL,
    email VARCHAR(100) NOT NULL UNIQUE,
    contrasena_hash VARCHAR(255) NOT NULL,
    fecha_registro DATETIME NOT NULL
);

-- 3. Crear la tabla CATEGORIAS
CREATE TABLE Categorias (
    id_categoria INT AUTO_INCREMENT PRIMARY KEY,
    nombre VARCHAR(50) NOT NULL UNIQUE,
    descripcion TEXT
);

-- 4. Crear la tabla PRODUCTOS (con FK a Categorias)
CREATE TABLE Productos (
    id_producto INT AUTO_INCREMENT PRIMARY KEY,
    nombre VARCHAR(255) NOT NULL,
    descripcion TEXT,
    precio DECIMAL(10, 2) NOT NULL CHECK (precio >= 0),
    stock INT NOT NULL CHECK (stock >= 0),
    id_categoria INT NOT NULL,
    fecha_creacion DATETIME NOT NULL,
    
    FOREIGN KEY (id_categoria) REFERENCES Categorias(id_categoria)
);

-- 5. Crear la tabla METODOS_PAGO
CREATE TABLE Metodos_Pago (
    id_metodo_pago INT AUTO_INCREMENT PRIMARY KEY,
    nombre VARCHAR(50) NOT NULL UNIQUE
);

-- 6. Crear la tabla DIRECCIONES_ENVIO (con FK a Usuarios)
CREATE TABLE Direcciones_Envio (
    id_direccion INT AUTO_INCREMENT PRIMARY KEY,
    id_usuario INT NOT NULL,
    calle VARCHAR(255) NOT NULL,
    ciudad VARCHAR(100) NOT NULL,
    codigo_postal VARCHAR(20) NOT NULL,
    pais VARCHAR(100) NOT NULL,
    es_principal BOOLEAN NOT NULL DEFAULT FALSE,
    
    FOREIGN KEY (id_usuario) REFERENCES Usuarios(id_usuario)
);

-- 7. Crear la tabla PEDIDOS (con FK a Usuarios, Direcciones_Envio y Metodos_Pago)
CREATE TABLE Pedidos (
    id_pedido INT AUTO_INCREMENT PRIMARY KEY,
    id_usuario INT NOT NULL,
    fecha_pedido DATETIME NOT NULL,
    estado VARCHAR(50) NOT NULL, -- Ej: 'Pendiente', 'Procesando', 'Enviado', 'Entregado'
    total DECIMAL(10, 2) NOT NULL CHECK (total >= 0),
    id_direccion_envio INT NOT NULL,
    id_metodo_pago INT NOT NULL,
    
    FOREIGN KEY (id_usuario) REFERENCES Usuarios(id_usuario),
    FOREIGN KEY (id_direccion_envio) REFERENCES Direcciones_Envio(id_direccion),
    FOREIGN KEY (id_metodo_pago) REFERENCES Metodos_Pago(id_metodo_pago)
);

-- 8. Crear la tabla DETALLES_PEDIDO (Tabla de unión Muchos a Muchos)
CREATE TABLE Detalles_Pedido (
    id_pedido INT NOT NULL,
    id_producto INT NOT NULL,
    cantidad INT NOT NULL CHECK (cantidad > 0),
    precio_unitario DECIMAL(10, 2) NOT NULL CHECK (precio_unitario >= 0),
    
    PRIMARY KEY (id_pedido, id_producto), -- Clave primaria compuesta
    FOREIGN KEY (id_pedido) REFERENCES Pedidos(id_pedido),
    FOREIGN KEY (id_producto) REFERENCES Productos(id_producto)
);

-- 9. Crear la tabla RESENAS_PRODUCTO (con FK a Usuarios y Productos)
CREATE TABLE Resenas_Producto (
    id_resena INT AUTO_INCREMENT PRIMARY KEY,
    id_usuario INT NOT NULL,
    id_producto INT NOT NULL,
    calificacion TINYINT NOT NULL CHECK (calificacion BETWEEN 1 AND 5), -- Calificación de 1 a 5
    comentario TEXT,
    fecha_resena DATETIME NOT NULL,
    
    FOREIGN KEY (id_usuario) REFERENCES Usuarios(id_usuario),
    FOREIGN KEY (id_producto) REFERENCES Productos(id_producto)
);


USE ecommerce_db;

-- Insertar datos de prueba
INSERT INTO Usuarios (nombre, email, contrasena_hash, fecha_registro) VALUES
('María González', 'maria@example.com', 'hash1', '2024-01-15'),
('Carlos Rodríguez', 'carlos@example.com', 'hash2', '2024-02-20'),
('Ana Martínez', 'ana@example.com', 'hash3', '2024-03-10'),
('Pedro Sánchez', 'pedro@example.com', 'hash4', '2024-01-05');


INSERT INTO Categorias (nombre, descripcion) VALUES
('Electronica', 'Productos electronicos y gadgets'),
('Ropa', 'Ropa y accesorios'),
('Hogar', 'Articulos para el hogar'),
('Deportes', 'Equipamiento deportivo');

INSERT INTO Productos (nombre, descripcion, precio, stock, id_categoria, fecha_creacion)
VALUES
('Laptop Gaming', 'Laptop potente para gaming con procesador Intel i7 y 16GB RAM', 1299.99, 5, 1, NOW()),
('Mouse Óptico', 'Mouse ergonómico inalámbrico con sensor de alta precisión', 29.99, 25, 1, NOW()),
('Teclado Mecánico', 'Teclado mecánico RGB con switches Cherry para gaming', 89.99, 12, 1, NOW()),
('Camiseta Deportiva', 'Camiseta transpirable y ligera, ideal para running', 24.99, 50, 4, NOW()),
('Zapatillas Running', 'Zapatillas ligeras con amortiguación para correr largas distancias', 79.99, 20, 4, NOW()),
('Sartén Antiadherente', 'Sartén de 24cm con recubrimiento antiadherente de alta calidad', 34.99, 15, 3, NOW());




INSERT INTO Direcciones_Envio
(id_usuario, calle, ciudad, codigo_postal, pais, es_principal)
VALUES
(1, 'Av. Principal 123', 'Santiago', '8320000', 'Chile', TRUE),
(2, 'Calle Falsa 456', 'Valparaíso', '2340000', 'Chile', TRUE),
(3, 'Pasaje Norte 789', 'Concepción', '4030000', 'Chile', TRUE);


INSERT INTO Direcciones_Envio (id_usuario, calle, ciudad, codigo_postal, pais, es_principal)
VALUES
(1, 'Calle 1', 'Santiago', '8320000', 'Chile', TRUE),
(2, 'Calle 2', 'Valparaíso', '2340000', 'Chile', TRUE),
(3, 'Calle 3', 'Concepción', '4030000', 'Chile', TRUE);



INSERT INTO Metodos_Pago (nombre) VALUES
('Tarjeta de Crédito'),
('Transferencia Bancaria'),
('PayPal');

-- Insertar Pedidos (Asegúrate que los IDs de usuario, dirección y método de pago existan)
INSERT INTO Pedidos (id_usuario, fecha_pedido, estado, total, id_direccion_envio, id_metodo_pago) VALUES
-- Pedido 1
(1, NOW(), 'Procesando', 1434.94, 1, 1),
-- Pedido 2
(2, NOW(), 'Enviado', 124.98, 2, 1),
-- Pedido 3
(3, NOW(), 'Pendiente', 79.99, 3, 2);


-- CONSULTAS BÁSICAS

-- 1. Listar todos los productos con su categoría
SELECT
    p.nombre AS Producto,
    p.precio,
    p.stock,
    c.nombre AS Categoria
FROM
    Productos p
INNER JOIN
    Categorias c ON p.id_categoria = c.id_categoria
ORDER BY
    Producto;

-- 2. Productos con stock bajo (< 10 unidades)
SELECT
    nombre,
    stock
FROM
    Productos
WHERE
    stock < 10
ORDER BY
    stock ASC;

-- 3. Usuarios registrados en el último mes
SELECT
    nombre,
    email,
    fecha_registro
FROM
    Usuarios
WHERE
    fecha_registro >= DATE_SUB(CURDATE(), INTERVAL 1 MONTH)
ORDER BY
    fecha_registro DESC;

-- 4. Pedidos con total mayor a 100€
SELECT
    p.id_pedido,
    u.nombre AS Cliente,
    p.total,
    p.fecha_pedido
FROM
    Pedidos p
INNER JOIN
    Usuarios u ON p.id_usuario = u.id_usuario
WHERE
    p.total > 100
ORDER BY
    p.total DESC;

-- CONSULTAS AVANZADAS

-- 5. Top 5 productos más vendidos
SELECT
    p.nombre AS Producto,
    SUM(dp.cantidad) AS total_vendido
FROM
    Productos p
INNER JOIN
    Detalles_Pedido dp ON p.id_producto = dp.id_producto
GROUP BY
    p.id_producto, p.nombre
ORDER BY
    total_vendido DESC
LIMIT 5;

-- 6. Ingresos por categoría
SELECT
    c.nombre AS Categoria,
    COUNT(DISTINCT p.id_producto) AS productos_vendidos_unicos,
    SUM(dp.cantidad * dp.precio_unitario) AS ingresos_totales
FROM
    Categorias c
INNER JOIN
    Productos p ON c.id_categoria = p.id_categoria
INNER JOIN
    Detalles_Pedido dp ON p.id_producto = dp.id_producto
GROUP BY
    c.id_categoria, c.nombre
ORDER BY
    ingresos_totales DESC;

-- 7. Clientes con más pedidos
SELECT
    u.nombre AS Cliente,
    u.email,
    COUNT(p.id_pedido) AS total_pedidos,
    COALESCE(SUM(p.total), 0) AS total_gastado
FROM
    Usuarios u
LEFT JOIN
    Pedidos p ON u.id_usuario = p.id_usuario
GROUP BY
    u.id_usuario, u.nombre, u.email -- Agrupamos por la clave primaria del usuario y sus detalles
ORDER BY
    total_pedidos DESC, total_gastado DESC;

-- 8. Productos nunca vendidos
SELECT
    p.nombre AS Producto,
    c.nombre AS Categoria
FROM
    Productos p
INNER JOIN
    Categorias c ON p.id_categoria = c.id_categoria
LEFT JOIN
    Detalles_Pedido dp ON p.id_producto = dp.id_producto
WHERE
    dp.id_pedido IS NULL
ORDER BY
    p.nombre;

-- 9. Estado de pedidos con detalles
SELECT
    p.id_pedido,
    u.nombre AS Cliente,
    p.fecha_pedido,
    p.estado,
    p.total,
    GROUP_CONCAT(CONCAT(pr.nombre, ' (', dp.cantidad, ')') SEPARATOR ', ') AS Productos_Comprados
FROM
    Pedidos p
INNER JOIN
    Usuarios u ON p.id_usuario = u.id_usuario
INNER JOIN
    Detalles_Pedido dp ON p.id_pedido = dp.id_pedido
INNER JOIN
    Productos pr ON dp.id_producto = pr.id_producto
GROUP BY
    p.id_pedido, u.nombre, p.fecha_pedido, p.estado, p.total
ORDER BY
    p.fecha_pedido DESC;

-- 10. Reporte mensual de ventas
SELECT
  DATE_FORMAT(fecha_pedido, '%Y-%m') AS mes,
  COUNT(*) AS pedidos_totales,
  COUNT(DISTINCT id_usuario) AS clientes_unicos,
  SUM(total) AS ingresos_totales,
  AVG(total) AS ticket_promedio
FROM
  Pedidos
WHERE
  fecha_pedido >= '2024-01-01'
GROUP BY
  mes
ORDER BY
  mes DESC;

-- OPERACIONES DE MANIPULACIÓN

-- 11. Actualizar stock después de venta
UPDATE Productos
SET stock = stock - 2
WHERE id_producto = 1;  -- Corregido: 'id_producto' en lugar de 'id'

-- 12. Aplicar descuento a productos de una categoría
UPDATE Productos
SET precio = precio * 0.9  -- 10% descuento
WHERE id_categoria = 4;    -- Corregido: 'id_categoria' en lugar de 'categoria_id'

-- 13. Marcar productos inactivos con stock 0
UPDATE Productos
SET activo = FALSE
WHERE stock = 0;

-- 14. Insertar nuevo pedido
INSERT INTO pedidos (usuario_id, fecha_pedido, estado, total)
VALUES (1, CURRENT_DATE, 'pendiente', 0.00);

SET @nuevo_pedido_id = LAST_INSERT_ID();

-- Insertar detalles del pedido
INSERT INTO Pedidos (id_usuario, fecha_pedido, estado, total, id_direccion_envio, id_metodo_pago)
VALUES (1, CURDATE(), 'Pendiente', 0.00, 1, 1); -- Usamos id_usuario 1, dir 1, método 1

-- Guardamos el ID autogenerado del nuevo pedido
SET @nuevo_pedido_id = LAST_INSERT_ID();

-- Paso 2: Insertar detalles del pedido
INSERT INTO Detalles_Pedido (id_pedido, id_producto, cantidad, precio_unitario)
VALUES
(@nuevo_pedido_id, 2, 1, 29.99),   -- Mouse Óptico
(@nuevo_pedido_id, 6, 1, 34.99);   -- Sartén Antiadherente

-- Actualizar total del pedido
UPDATE Pedidos
SET total = (
  SELECT SUM(cantidad * precio_unitario)
  FROM Detalles_Pedido
  WHERE id_pedido = @nuevo_pedido_id
)
WHERE id_pedido = @nuevo_pedido_id; -- Corregido: 'id_pedido' en lugar de 'id'

-- 15. Limpiar pedidos muy antiguos (ejemplo)
DELETE FROM Detalles_Pedido
WHERE id_pedido IN (
  SELECT id_pedido FROM Pedidos -- Corregido: 'id_pedido' en lugar de 'id'
  WHERE fecha_pedido < '2020-01-01'
);

DELETE FROM pedidos
WHERE fecha_pedido < '2020-01-01';