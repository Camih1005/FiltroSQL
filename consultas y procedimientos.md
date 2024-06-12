## CONSULTAS

 - 1.Obtener la lista de todos los productos con sus precios

select nombre,precio from Productos;

/* 2.Encontrar todos los pedidos realizados por un usuario
específico, por ejemplo, Juan Perez*/

select us.id,p.fecha,p.total from Pedidos as p
  join Usuarios as us on p.id_usuario = us.id 
    where us.nombre = "Juan Perez";

/*
3.Listar los detalles de todos los pedidos, incluyendo el
nombre del producto, cantidad y precio unitario
*/

select dp.id_pedido,p.nombre,dp.cantidad,dp.precio_unitario 
  from DetallesPedidos as dp
     join Productos as p on p.id = dp.id_producto;

/*
4.Calcular el total gastado por cada usuario en todos sus
pedidos
*/

select distinct us.nombre,pd.total as TotalGastado
  from DetallesPedidos as dp
	join Pedidos as pd on pd.id = dp.id_pedido
      join Usuarios as us on us.id = pd.id_usuario;

/*
5.Encontrar los productos más caros (precio mayor a $500)
*/

select nombre,precio from Productos as p
	where precio > 500;

/*
6.Listar los pedidos realizados en una fecha específica, por
ejemplo, 2024-03-10
*/
select * from Pedidos as pd
	where date(fecha) = "2024-03-10";

/*
7.Obtener el número total de pedidos realizados por cada
usuario
*/

select us.nombre,count(*) as NumerosPedidos from Pedidos as pd
	join Usuarios as us on us.id = pd.id_usuario
		group by us.nombre;
		
/*
8.Encontrar el nombre del producto más vendido (mayor
cantidad total vendida)
*/
select p.nombre,max(dp.cantidad) as CantidadTotal from DetallesPedidos as dp
	join Productos as p on dp.id_producto = p.id
		group by p.nombre
			having max(dp.cantidad) =  (select max(cantidad) from DetallesPedidos);

/*
9.Listar todos los usuarios que han realizado al menos un
pedido
*/

select us.nombre,us.correo_electronico from Pedidos as p
	join Usuarios as us on p.id_usuario = us.id
		where p.id is not null;


/*
10.Obtener los detalles de un pedido específico, incluyendo los
productos y cantidades, por ejemplo, pedido con id 1
*/

select pd.id,us.nombre,p.nombre,dp.cantidad,p.precio from Pedidos as pd
	join DetallesPedidos as dp on dp.id_pedido = pd.id
		join Usuarios as us on us.id = pd.id_usuario
			join Productos as p on p.id = dp.id_producto
				where pd.id = 1;


/*
SUBCONSULTAS
*/
/*
1.Encontrar el nombre del usuario que ha gastado más en total
*/
select nombre from(
select nombre,sum(cXp) as suma from
(select us.nombre,(dp.cantidad * dp.precio_unitario)as cXp from Pedidos as pd
	join DetallesPedidos as dp on dp.id_pedido = pd.id
		join Usuarios as us on us.id = pd.id_usuario
			) as sub 	
				group by nombre
					order by suma desc limit 1) as alias;			
			


/*
2.Listar los productos que han sido pedidos al menos una vez
*/

select p.nombre from DetallesPedidos as dp	
	right join (select * from Productos) as p on p.id = dp.id_producto 
		where dp.id_pedido is not null;

/*
3.Obtener los detalles del pedido con el total más alto
*/

select distinct pd.id,pd.id_usuario,pd.fecha,pd.total from DetallesPedidos as dp
	join Pedidos as pd on pd.id = dp.id_pedido
		where pd.total = (select max(total) from Pedidos);

/*
4.Listar los usuarios que han realizado más de un pedido.
*/
select us.nombre from DetallesPedidos as dp	
	right join (select * from Productos) as p on p.id = dp.id_producto 
		join Pedidos as pd on pd.id =  dp.id_producto
			join Usuarios as us on us.id = pd.id_usuario
		where dp.id_pedido is not null;
/*
5.Obtener los detalles del pedido con el total más alto
*/

select p.nombre,dp.precio_unitario as precio from Productos as p	
	right join DetallesPedidos as dp on p.id = dp.id_producto
		where precio_unitario = (select max(precio_unitario) from DetallesPedidos);



/*
PROCEDURESS
*/

/*
1. Crear un procedimiento almacenado para agregar un nuevo
producto
Enunciado: Crea un procedimiento almacenado llamado AgregarProducto que reciba como
parámetros el nombre, descripción y precio de un nuevo producto y lo inserte en la tabla
Productos .
*/
delimiter $$
drop procedure if exists AgregarProducto;
create procedure AgregarProducto 
(
in nombre1 varchar(100),
in descripcion1 text,
in precio1 decimal(10,2)
)
begin
declare mensaje varchar(50);
	insert into Productos(nombre,descripcion,precio) values(nombre1,descripcion1,precio1);
	
set mensaje = "producto agregado";
select mensaje;
    end $$
	
delimiter ;  

call AgregarProducto("Samsung A53 5G","Super celular",160000);


/*
2.Crear un procedimiento almacenado para obtener los
detalles de un pedido
Enunciado: Crea un procedimiento almacenado llamado ObtenerDetallesPedido que reciba
como parámetro el ID del pedido y devuelva los detalles del pedido, incluyendo el nombre del
producto, cantidad y precio unitario.
*/
delimiter $$
drop procedure if exists ObtenerDetallesPedido;
create procedure ObtenerDetallesPedido
(
in idPedido int
)
  begin
	select p.nombre,dp.cantidad,dp.precio_unitario from DetallesPedidos as dp
		join Pedidos as pd on pd.id = dp.id_pedido
			join Productos as p on p.id = dp.id_producto
        where pd.id = idPedido;
        
        
  end $$
delimiter ;	

call ObtenerDetallesPedido(1);


/*
Crear un procedimiento almacenado para actualizar el
precio de un producto
Enunciado: Crea un procedimiento almacenado llamado ActualizarPrecioProducto que reciba
como parámetros el ID del producto y el nuevo precio, y actualice el precio del producto en la
tabla Productos .
*/
delimiter $$
drop procedure if exists ActualizarPrecioProducto;
create procedure ActualizarPrecioProducto(
in idProducto int,
in newPrecio decimal(10,2)
)
begin	
declare mensaje varchar(50);

	update Productos 
    set precio = newPrecio
    where id = idProducto;
    
    select * from Productos
    where id = idProducto;


set mensaje = "producto actualizado";
select mensaje;
end $$
delimiter ;

call ActualizarPrecioProducto(3,125);


/*
Crear un procedimiento almacenado para eliminar un
producto
Enunciado: Crea un procedimiento almacenado llamado EliminarProducto que reciba como
parámetro el ID del producto y lo elimine de la tabla Productos .
*/
delimiter //
drop procedure if exists EliminarProducto;
create procedure EliminarProducto(
in idProducto int
)
begin
declare mensaje varchar(50) ;

delete from DetallesPedidos where id_producto = idProducto;
delete from Productos where id = idProducto;

set mensaje = "producto eliminado";
select mensaje;
end //

delimiter ;

call EliminarProducto(1);


/*
5.Crear un procedimiento almacenado para obtener el total
gastado por un usuario
Enunciado: Crea un procedimiento almacenado llamado TotalGastadoPorUsuario que reciba
como parámetro el ID del usuario y devuelva el total gastado por ese usuario en todos sus
pedidos.
*/

delimiter //
drop procedure if exists TotalGastadoPorUsuario;
create procedure TotalGastadoPorUsuario(
in id1 int
)
begin
select distinct us.nombre,pd.total as TotalGastado
  from DetallesPedidos as dp
	join Pedidos as pd on pd.id = dp.id_pedido
      join Usuarios as us on us.id = pd.id_usuario
      where us.id = id1;
      end //
delimiter ;

call TotalGastadoPorUsuario(2);
