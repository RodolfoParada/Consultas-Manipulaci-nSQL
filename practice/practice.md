USE ecommerce;

-- Insertar datos de prueba
INSERT INTO usuarios (nombre, apellido, email, fecha_registro) VALUES
('María', 'González', 'maria@example.com', '2024-01-15'),
('Carlos', 'Rodríguez', 'carlos@example.com', '2024-02-20'),
('Ana', 'Martínez', 'ana@example.com', '2024-03-10'),
('Pedro', 'Sánchez', 'pedro@example.com', '2024-01-05');

INSERT INTO categorias (nombre, descripcion) VALUES
('Electrónica', 'Productos electrónicos y gadgets'),
('Ropa', 'Ropa y accesorios'),
('Hogar', 'Artículos para el hogar'),
('Deportes', 'Equipamiento deportivo');

INSERT INTO productos (nombre, descripcion, precio, stock, categoria_id, activo) VALUES
('Laptop Gaming', 'Laptop potente para gaming', 1299.99, 5, 1, true),
('Mouse Óptico', 'Mouse ergonómico inalámbrico', 29.99, 25, 1, true),
('Teclado Mecánico', 'Teclado RGB con switches cherry', 89.99, 12, 1, true),
('Camiseta Deportiva', 'Camiseta transpirable para running', 24.99, 50, 4, true),
('Zapatillas Running', 'Zapatillas ligeras para correr', 79.99, 20, 4, true),
('Sartén Antiadherente', 'Sartén de 24cm antiadherente', 34.99, 15, 3, true);

INSERT INTO pedidos (usuario_id, fecha_pedido, estado, total) VALUES
(1, '2024-06-01', 'completado', 1329.98),
(2, '2024-06-02', 'pendiente', 114.98),
(3, '2024-06-03', 'enviado', 79.99);

INSERT INTO detalle_pedidos (pedido_id, producto_id, cantidad, precio_unitario) VALUES
(1, 1, 1, 1299.99),  -- Laptop
(1, 2, 1, 29.99),    -- Mouse
(2, 2, 1, 29.99),    -- Mouse
(2, 3, 1, 89.99),    -- Teclado
(3, 5, 1, 79.99);    -- Zapatillas

-- CONSULTAS BÁSICAS

-- 1. Listar todos los productos con su categoría
SELECT p.nombre, p.precio, p.stock, c.nombre AS categoria
FROM productos p
INNER JOIN categorias c ON p.categoria_id = c.id
ORDER BY p.nombre;

-- 2. Productos con stock bajo (< 10 unidades)
SELECT nombre, stock
FROM productos
WHERE stock < 10
ORDER BY stock ASC;

-- 3. Usuarios registrados en el último mes
SELECT nombre, apellido, fecha_registro
FROM usuarios
WHERE fecha_registro >= DATE_SUB(CURRENT_DATE, INTERVAL 1 MONTH)
ORDER BY fecha_registro DESC;

-- 4. Pedidos con total mayor a 100€
SELECT p.id, u.nombre, u.apellido, p.total, p.fecha_pedido
FROM pedidos p
INNER JOIN usuarios u ON p.usuario_id = u.id
WHERE p.total > 100
ORDER BY p.total DESC;

-- CONSULTAS AVANZADAS

-- 5. Top 5 productos más vendidos
SELECT p.nombre, SUM(dp.cantidad) AS total_vendido
FROM productos p
INNER JOIN detalle_pedidos dp ON p.id = dp.producto_id
GROUP BY p.id, p.nombre
ORDER BY total_vendido DESC
LIMIT 5;

-- 6. Ingresos por categoría
SELECT c.nombre AS categoria,
       COUNT(dp.producto_id) AS productos_vendidos,
       SUM(dp.cantidad * dp.precio_unitario) AS ingresos_totales
FROM categorias c
INNER JOIN productos p ON c.id = p.categoria_id
INNER JOIN detalle_pedidos dp ON p.id = dp.producto_id
GROUP BY c.id, c.nombre
ORDER BY ingresos_totales DESC;

-- 7. Clientes con más pedidos
SELECT u.nombre, u.apellido,
       COUNT(p.id) AS total_pedidos,
       SUM(p.total) AS total_gastado
FROM usuarios u
LEFT JOIN pedidos p ON u.id = p.usuario_id
GROUP BY u.id, u.nombre, u.apellido
ORDER BY total_pedidos DESC, total_gastado DESC;

-- 8. Productos nunca vendidos
SELECT p.nombre, c.nombre AS categoria
FROM productos p
INNER JOIN categorias c ON p.categoria_id = c.id
LEFT JOIN detalle_pedidos dp ON p.id = dp.producto_id
WHERE dp.id IS NULL
ORDER BY p.nombre;

-- 9. Estado de pedidos con detalles
SELECT p.id AS pedido_id,
       CONCAT(u.nombre, ' ', u.apellido) AS cliente,
       p.fecha_pedido,
       p.estado,
       p.total,
       GROUP_CONCAT(CONCAT(pr.nombre, ' (', dp.cantidad, ')') SEPARATOR ', ') AS productos
FROM pedidos p
INNER JOIN usuarios u ON p.usuario_id = u.id
INNER JOIN detalle_pedidos dp ON p.id = dp.pedido_id
INNER JOIN productos pr ON dp.producto_id = pr.id
GROUP BY p.id, u.nombre, u.apellido, p.fecha_pedido, p.estado, p.total
ORDER BY p.fecha_pedido DESC;

-- 10. Reporte mensual de ventas
SELECT
  DATE_FORMAT(fecha_pedido, '%Y-%m') AS mes,
  COUNT(*) AS pedidos_totales,
  COUNT(DISTINCT usuario_id) AS clientes_unicos,
  SUM(total) AS ingresos_totales,
  AVG(total) AS ticket_promedio
FROM pedidos
WHERE fecha_pedido >= '2024-01-01'
GROUP BY DATE_FORMAT(fecha_pedido, '%Y-%m')
ORDER BY mes DESC;

-- OPERACIONES DE MANIPULACIÓN

-- 11. Actualizar stock después de venta
UPDATE productos
SET stock = stock - 2
WHERE id = 1;  -- Laptop Gaming

-- 12. Aplicar descuento a productos de una categoría
UPDATE productos
SET precio = precio * 0.9  -- 10% descuento
WHERE categoria_id = 4;    -- Deportes

-- 13. Marcar productos inactivos con stock 0
UPDATE productos
SET activo = false
WHERE stock = 0;

-- 14. Insertar nuevo pedido
INSERT INTO pedidos (usuario_id, fecha_pedido, estado, total)
VALUES (1, CURRENT_DATE, 'pendiente', 0.00);

SET @nuevo_pedido_id = LAST_INSERT_ID();

-- Insertar detalles del pedido
INSERT INTO detalle_pedidos (pedido_id, producto_id, cantidad, precio_unitario)
VALUES
(@nuevo_pedido_id, 2, 1, 29.99),   -- Mouse
(@nuevo_pedido_id, 6, 1, 34.99);   -- Sartén

-- Actualizar total del pedido
UPDATE pedidos
SET total = (
  SELECT SUM(cantidad * precio_unitario)
  FROM detalle_pedidos
  WHERE pedido_id = @nuevo_pedido_id
)
WHERE id = @nuevo_pedido_id;

-- 15. Limpiar pedidos muy antiguos (ejemplo)
DELETE FROM detalle_pedidos
WHERE pedido_id IN (
  SELECT id FROM pedidos
  WHERE fecha_pedido < '2020-01-01'
);

DELETE FROM pedidos
WHERE fecha_pedido < '2020-01-01';