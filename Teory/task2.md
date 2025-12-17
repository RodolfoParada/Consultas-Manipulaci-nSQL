Task 2: Consultas SELECT Avanzadas (8 minutos)
Funcionalidades avanzadas de SELECT para consultas complejas.

ORDER BY - Ordenamiento
-- Orden ascendente (por defecto)
SELECT * FROM productos ORDER BY precio;
SELECT nombre, precio FROM productos ORDER BY precio ASC;

-- Orden descendente
SELECT * FROM productos ORDER BY precio DESC;
SELECT nombre, precio FROM productos ORDER BY precio DESC;

-- Orden múltiple
SELECT * FROM productos
ORDER BY categoria ASC, precio DESC;

-- Orden por expresión
SELECT nombre, precio, precio * 1.21 AS precio_con_iva
FROM productos
ORDER BY precio_con_iva DESC;

-- Orden con NULLS
SELECT nombre, precio FROM productos
ORDER BY precio DESC NULLS LAST;  -- MySQL no soporta NULLS FIRST/LAST
LIMIT y OFFSET - Paginación
-- Limitar resultados
SELECT * FROM productos LIMIT 10;
SELECT * FROM usuarios LIMIT 5;

-- Paginación básica
SELECT * FROM productos ORDER BY precio LIMIT 10 OFFSET 0;   -- Primera página
SELECT * FROM productos ORDER BY precio LIMIT 10 OFFSET 10;  -- Segunda página
SELECT * FROM productos ORDER BY precio LIMIT 10 OFFSET 20;  -- Tercera página

-- Paginación avanzada con cálculo
SET @pagina = 2;
SET @limite = 10;
SELECT * FROM productos
ORDER BY precio
LIMIT ? OFFSET (? - 1) * ?;  -- En aplicación se pasan los parámetros
DISTINCT - Valores Únicos
-- Valores únicos de una columna
SELECT DISTINCT categoria FROM productos;
SELECT DISTINCT pais FROM usuarios;

-- DISTINCT en múltiples columnas
SELECT DISTINCT categoria, marca FROM productos;
-- Devuelve combinaciones únicas de categoria + marca

-- COUNT con DISTINCT
SELECT COUNT(DISTINCT categoria) AS total_categorias FROM productos;
SELECT COUNT(DISTINCT pais) AS paises_unicos FROM usuarios;

-- DISTINCT con ORDER BY
SELECT DISTINCT categoria FROM productos ORDER BY categoria;
CASE - Lógica Condicional
-- CASE simple
SELECT nombre, precio,
  CASE
    WHEN precio < 50 THEN 'Económico'
    WHEN precio < 200 THEN 'Medio'
    ELSE 'Premium'
  END AS categoria_precio
FROM productos;

-- CASE con búsqueda
SELECT nombre, stock,
  CASE stock
    WHEN 0 THEN 'Agotado'
    WHEN 1 THEN 'Última unidad'
    WHEN 2 THEN 'Pocas unidades'
    ELSE 'Disponible'
  END AS estado_stock
FROM productos;

-- CASE en ORDER BY
SELECT nombre, categoria, precio
FROM productos
ORDER BY
  CASE categoria
    WHEN 'Electrónica' THEN 1
    WHEN 'Ropa' THEN 2
    WHEN 'Hogar' THEN 3
    ELSE 4
  END ASC,
  precio DESC;