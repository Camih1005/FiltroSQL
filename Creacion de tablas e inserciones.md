drop database if exists techHaven;
create database techHaven;

use techHaven;

create table Productos(
id int primary key auto_increment,
nombre varchar(100) not null,
precio decimal(10,2) not null,
descripcion text
);

create table Usuarios(
id int primary key auto_increment,
nombre varchar(100) not null,
correo_electronico varchar(100),
fecha_registro date
);

create table Pedidos(
id int primary key auto_increment,
id_usuario int,
fecha date,
total decimal(10,2)
);

create table DetallesPedidos(
id_pedido int,
id_producto int,
cantidad int,
precio_unitario decimal(10,2),
foreign key(id_pedido)references Pedidos(id),
foreign key(id_producto)references Productos(id)
);

show tables;


INSERT INTO Productos (nombre, precio, descripcion) VALUES
('iPhone 13', 799.99, 'Apple iPhone 13 con pantalla de 6.1 pulgadas y cámara dual.'),
('Samsung Galaxy S21', 699.99, 'Samsung Galaxy S21 con pantalla de 6.2 pulgadas y cámara triple.'),
('Sony WH-1000XM4', 349.99, 'Auriculares inalámbricos Sony con cancelación de ruido.'),
('MacBook Pro', 1299.99, 'Apple MacBook Pro con pantalla Retina de 13 pulgadas.'),
('Dell XPS 13', 999.99, 'Portátil Dell XPS 13 con pantalla de 13.3 pulgadas y procesador Intel 17.'),
('GoPro HERO9', 399.99, 'Cámara de acción GoPro HERO9 con resolución 5K.'),
('Amazon Echo Dot', 49.99, 'Altavoz inteligente Amazon Echo Dot con Alexa.'),
('Kindle Paperwhite', 129.99, 'Amazon Kindle Paperwhite con pantalla de 6 pulgadas y luz ajustable.'),
('Apple Watch Series 7', 399.99, 'Apple Watch Series 7 con GPS y pantalla Retina siempre activa.'),
('Bose QuietComfort 35 II', 299.99, 'Auriculares inalámbricos Bose con cancelación de ruido.'),
('Nintendo Switch', 299.99, 'Consola de videojuegos Nintendo Switch con controles Joy-Con.'),
('Fitbit Charge 5', 179.95, 'Monitor de actividad física Fitbit Charge 5 con GPS y seguimiento del sueño.');

INSERT INTO Usuarios (nombre, correo_electronico, fecha_registro) VALUES
('Juan Perez', 'juan.perez@example.com', '2024-01-01'),
('Maria Lopez', 'maria.lopez@example.com', '2024-01-05'),
('Carlos Mendoza', 'carlos.mendoza@example.com', '2024-02-10'),
('Ana González', 'ana.gonzalez@example.com', '2024-02-20'),
('Luis Torres', 'luis.torres@example.com', '2024-03-05'),
('Laura Rivera', 'laura.rivera@example.com', '2024-03-15');

INSERT INTO Pedidos (id_usuario, fecha, total) VALUES
(1, '2024-02-25', 1049.98),
(2, '2024-03-10', 1349.98),
(3, '2024-03-20', 1249.99),
(4, '2024-03-25', 449.98),
(5, '2024-04-05', 699.99),
(6, '2024-04-10', 399.99);

INSERT INTO DetallesPedidos (id_pedido, id_producto, cantidad, precio_unitario) VALUES
(1, 1, 1, 799.99),
(1, 7, 5, 49.99),
(2, 4, 1, 1299.99),
(2, 8, 1, 129.99),
(3, 2, 1, 699.99),
(3, 9, 1, 399.99),
(4, 5, 1, 999.99),
(4, 10, 1, 299.99),
(5, 11, 1, 299.99),
(5, 3, 1, 349.99),
(6, 6, 1, 399.99);


