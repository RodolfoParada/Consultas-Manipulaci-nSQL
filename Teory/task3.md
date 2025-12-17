Task 3: JOIN - Combinar Tablas (8 minutos)
JOIN permite combinar datos de múltiples tablas relacionadas.

INNER JOIN - Intersección
-- INNER JOIN básico
SELECT p.nombre, p.precio, c.nombre AS categoria
FROM productos p
INNER JOIN categorias c ON p.categoria_id = c.id;

-- Sintaxis alternativa
SELECT p.nombre, p.precio, c.nombre AS categoria
FROM productos p
JOIN categorias c ON p.categoria_id = c.id;

-- JOIN múltiple
SELECT p.nombre, u.nombre AS autor, c.nombre AS categoria
FROM posts p
INNER JOIN usuarios u ON p.autor_id = u.id
INNER JOIN categorias c ON p.categoria_id = c.id;

-- JOIN con condiciones adicionales
SELECT p.nombre, p.precio, ped.cantidad
FROM productos p
INNER JOIN detalle_pedidos ped ON p.id = ped.producto_id
WHERE ped.pedido_id = 123;
LEFT JOIN - Todos de la Izquierda
-- LEFT JOIN básico
SELECT u.nombre, u.email, COUNT(p.id) AS total_posts
FROM usuarios u
LEFT JOIN posts p ON u.id = p.autor_id
GROUP BY u.id, u.nombre, u.email;

-- Usuarios sin posts
SELECT u.nombre
FROM usuarios u
LEFT JOIN posts p ON u.id = p.autor_id
WHERE p.id IS NULL;

-- LEFT JOIN múltiple
SELECT p.nombre AS producto, c.nombre AS categoria, pr.nombre AS proveedor
FROM productos p
LEFT JOIN categorias c ON p.categoria_id = c.id
LEFT JOIN proveedores pr ON p.proveedor_id = pr.id;
RIGHT JOIN - Todos de la Derecha
-- RIGHT JOIN (menos común, pero útil)
SELECT c.nombre AS categoria, COUNT(p.id) AS total_productos
FROM productos p
RIGHT JOIN categorias c ON p.categoria_id = c.id
GROUP BY c.id, c.nombre;

-- Categorías sin productos
SELECT c.nombre
FROM productos p
RIGHT JOIN categorias c ON p.categoria_id = c.id
WHERE p.id IS NULL;
FULL OUTER JOIN - Todos de Ambas
-- FULL OUTER JOIN (no soportado en MySQL, se simula)
SELECT u.nombre AS usuario, p.titulo AS post
FROM usuarios u
LEFT JOIN posts p ON u.id = p.autor_id

UNION

SELECT u.nombre AS usuario, p.titulo AS post
FROM usuarios u
RIGHT JOIN posts p ON u.id = p.autor_id;
SELF JOIN - Unión con Sí Misma
-- Empleados y sus jefes
SELECT e.nombre AS empleado, j.nombre AS jefe
FROM empleados e
LEFT JOIN empleados j ON e.jefe_id = j.id;

-- Productos relacionados
SELECT p1.nombre AS producto, p2.nombre AS relacionado
FROM productos_relacionados pr
JOIN productos p1 ON pr.producto_id = p1.id
JOIN productos p2 ON pr.relacionado_id = p2.id;