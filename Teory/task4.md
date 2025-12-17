Task 4: Manipulación de Datos (6 minutos)
INSERT, UPDATE, DELETE para modificar datos en la base de datos.

INSERT - Insertar Datos
-- INSERT básico
INSERT INTO usuarios (nombre, email) VALUES ('Juan Pérez', 'juan@example.com');

-- INSERT múltiple
INSERT INTO productos (nombre, precio, stock) VALUES
('Laptop', 1200.00, 5),
('Mouse', 25.50, 20),
('Teclado', 75.00, 15);

-- INSERT con SELECT
INSERT INTO productos_categorias (producto_id, categoria_id)
SELECT p.id, c.id
FROM productos p, categorias c
WHERE p.nombre LIKE '%Gaming%' AND c.nombre = 'Electrónica';

-- INSERT con DEFAULT
INSERT INTO pedidos (usuario_id, total) VALUES (123, 150.00);
-- fecha se establece automáticamente con DEFAULT CURRENT_TIMESTAMP
UPDATE - Actualizar Datos
-- UPDATE básico
UPDATE productos SET precio = precio * 1.10 WHERE categoria = 'Electrónica';

-- UPDATE múltiple columnas
UPDATE usuarios
SET nombre = 'Juan Carlos Pérez', activo = true
WHERE id = 123;

-- UPDATE con subconsulta
UPDATE productos
SET stock = stock - (
  SELECT SUM(cantidad) FROM detalle_pedidos
  WHERE producto_id = productos.id
)
WHERE id = 456;

-- UPDATE condicional
UPDATE productos
SET descuento = CASE
  WHEN stock > 100 THEN 0.20
  WHEN stock > 50 THEN 0.10
  ELSE 0.05
END
WHERE precio > 100;
DELETE - Eliminar Datos
-- DELETE básico
DELETE FROM productos WHERE stock = 0;

-- DELETE con JOIN (MySQL)
DELETE p FROM productos p
LEFT JOIN detalle_pedidos dp ON p.id = dp.producto_id
WHERE dp.id IS NULL;  -- Productos no vendidos

-- DELETE con subconsulta
DELETE FROM usuarios
WHERE id NOT IN (
  SELECT DISTINCT usuario_id FROM pedidos
);

-- TRUNCATE (eliminar todos los registros)
TRUNCATE TABLE logs_auditoria;
-- Más rápido que DELETE, pero no se puede deshacer