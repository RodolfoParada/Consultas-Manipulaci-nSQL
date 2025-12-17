Task 1: Consultas SELECT Básicas (8 minutos)
SELECT es la instrucción fundamental para recuperar datos de una base de datos.

SELECT Básico
-- Sintaxis básica
SELECT columnas FROM tabla;

-- Seleccionar todas las columnas
SELECT * FROM usuarios;

-- Seleccionar columnas específicas
SELECT nombre, email FROM usuarios;

-- Seleccionar con alias de columna
SELECT nombre AS nombre_completo, email AS correo FROM usuarios;

-- Seleccionar con alias de tabla
SELECT u.nombre, u.email FROM usuarios u;

-- Seleccionar valores literales
SELECT 'Hola Mundo' AS saludo, 42 AS numero;
WHERE - Filtrado de Datos
-- Filtrado básico
SELECT * FROM productos WHERE precio > 100;
SELECT * FROM usuarios WHERE activo = true;
SELECT * FROM pedidos WHERE fecha > '2024-01-01';

-- Operadores de comparación
SELECT * FROM productos WHERE precio BETWEEN 50 AND 200;
SELECT * FROM usuarios WHERE edad IN (18, 25, 30, 35);
SELECT * FROM productos WHERE nombre LIKE '%laptop%';

-- Operadores lógicos
SELECT * FROM productos
WHERE precio > 100 AND categoria = 'Electrónica';

SELECT * FROM usuarios
WHERE (edad >= 18 AND edad <= 65) OR vip = true;

SELECT * FROM productos
WHERE NOT (precio < 50 OR stock = 0);
Operadores LIKE y Patrones
-- LIKE con comodines
SELECT * FROM productos WHERE nombre LIKE 'Laptop%';        -- Empieza con "Laptop"
SELECT * FROM productos WHERE nombre LIKE '%Gaming%';       -- Contiene "Gaming"
SELECT * FROM productos WHERE nombre LIKE '%Pro';           -- Termina con "Pro"
SELECT * FROM usuarios WHERE email LIKE '%@gmail.com';      -- Emails de Gmail
SELECT * FROM productos WHERE codigo LIKE 'ABC___';         -- 3 caracteres después de ABC

-- NOT LIKE
SELECT * FROM productos WHERE nombre NOT LIKE '%Genérico%';

-- Uso avanzado
SELECT * FROM usuarios
WHERE nombre LIKE 'J%' OR nombre LIKE 'M%'  -- Nombres que empiezan con J o M
ORDER BY nombre;