CREATE DATABASE IF NOT EXISTS biblioteca;
USE biblioteca;

CREATE TABLE autores (
    id_autor INT AUTO_INCREMENT PRIMARY KEY,
    nombre VARCHAR(100) NOT NULL
);

CREATE TABLE libros (
    id_libro INT AUTO_INCREMENT PRIMARY KEY,
    titulo VARCHAR(200) NOT NULL,
    id_autor INT NOT NULL,
    isbn VARCHAR(20) UNIQUE,
    total_ejemplares INT NOT NULL,
    ejemplares_disponibles INT NOT NULL,
    FOREIGN KEY (id_autor) REFERENCES autores(id_autor)
);


CREATE TABLE usuarios (
    id_usuario INT AUTO_INCREMENT PRIMARY KEY,
    nombre VARCHAR(100) NOT NULL,
    email VARCHAR(100) UNIQUE
);


CREATE TABLE prestamos (
    id_prestamo INT AUTO_INCREMENT PRIMARY KEY,
    id_usuario INT NOT NULL,
    id_libro INT NOT NULL,
    fecha_prestamo DATE NOT NULL,
    fecha_devolucion DATE,
    fecha_devolucion_real DATE,
    FOREIGN KEY (id_usuario) REFERENCES usuarios(id_usuario),
    FOREIGN KEY (id_libro) REFERENCES libros(id_libro)
);


INSERT INTO autores (nombre) VALUES
('Gabriel García Márquez'),
('J.K. Rowling'),
('George Orwell'),
('Isabel Allende'),
('Julio Cortázar');

INSERT INTO libros (titulo, id_autor, isbn, total_ejemplares, ejemplares_disponibles) VALUES
('Cien años de soledad', 1, '9788439720479', 5, 3),
('El amor en los tiempos del cólera', 1, '9780307474728', 4, 2),
('Harry Potter y la piedra filosofal', 2, '9788478884452', 6, 4),
('Harry Potter y la cámara secreta', 2, '9788478884957', 5, 5),
('1984', 3, '9780451524935', 3, 1),
('Rebelión en la granja', 3, '9780451526342', 4, 2),
('La casa de los espíritus', 4, '9780553383805', 5, 3),
('Rayuela', 5, '9788432225070', 4, 2);

INSERT INTO usuarios (nombre, email) VALUES
('Juan Pérez', 'juan.perez@email.com'),
('María González', 'maria.gonzalez@email.com'),
('Pedro Ramírez', 'pedro.ramirez@email.com'),
('Ana Torres', 'ana.torres@email.com');

INSERT INTO prestamos 
(id_usuario, id_libro, fecha_prestamo, fecha_devolucion, fecha_devolucion_real) 
VALUES
(1, 1, '2025-11-01', '2025-11-10', NULL),
(2, 3, '2025-11-05', '2025-11-15', NULL),
(3, 5, '2025-10-01', '2025-10-10', '2025-10-10'),
(4, 6, '2025-10-03', '2025-10-12', '2025-10-11'),
(1, 2, '2025-09-01', '2025-09-10', '2025-09-15'),
(2, 7, '2025-09-05', '2025-09-12', '2025-09-14');

-- Consultas--

-- Busca libros por autor o titulo
SELECT l.titulo, a.nombre AS autor
FROM libros l
JOIN autores a ON l.id_autor = a.id_autor
WHERE l.titulo LIKE '%harry%'
   OR a.nombre LIKE '%rowling%';

-- prestamos activos de un usuario
SELECT u.nombre, l.titulo, p.fecha_prestamo, p.fecha_devolucion
FROM prestamos p
JOIN usuarios u ON p.id_usuario = u.id_usuario
JOIN libros l ON p.id_libro = l.id_libro
WHERE p.fecha_devolucion_real IS NULL
  AND u.id_usuario = 1;

-- encontrar libros disponibles
SELECT titulo, ejemplares_disponibles
FROM libros
WHERE ejemplares_disponibles > 0;

-- calcular multas por retraso

SELECT 
    u.nombre,
    l.titulo,
    DATEDIFF(p.fecha_devolucion_real, p.fecha_devolucion) AS dias_retraso,
    DATEDIFF(p.fecha_devolucion_real, p.fecha_devolucion) * 500 AS multa
FROM prestamos p
JOIN usuarios u ON p.id_usuario = u.id_usuario
JOIN libros l ON p.id_libro = l.id_libro
WHERE p.fecha_devolucion_real > p.fecha_devolucion;

-- Reporte de uso mensual
SELECT 
    YEAR(fecha_prestamo) AS anio,
    MONTH(fecha_prestamo) AS mes,
    COUNT(*) AS total_prestamos
FROM prestamos
GROUP BY anio, mes
ORDER BY anio, mes;
