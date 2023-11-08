# Base De Datos Jardineria

1. Obtén un listado con el nombre de cada cliente y el nombre y apellido de su representante de ventas.

```sql
SELECT c.nombre_cliente, CONCAT(e.nombre, ' ', e.apellido1) FROM cliente c, empleado e
WHERE c.codigo_empleado_rep_ventas = e.codigo_empleado;
```
2. Muestra el nombre de los clientes que hayan realizado pagos junto con el nombre de sus representantes de ventas.

```sql
SELECT DISTINCT c.nombre_cliente, CONCAT(e.nombre, ' ', e.apellido1) as Representante FROM cliente c, empleado e, pago p
WHERE c.codigo_empleado_rep_ventas = e.codigo_empleado
AND p.codigo_cliente = c.codigo_cliente;
```

3. Muestra el nombre de los clientes que no hayan realizado pagos junto con el nombre de sus representantes de ventas.

```sql
SELECT c.nombre_cliente, CONCAT(e.nombre, ' ', e.apellido1) as Representante FROM cliente c
JOIN empleado e ON c.codigo_empleado_rep_ventas = e.codigo_empleado
LEFT JOIN pago p ON c.codigo_cliente = p.codigo_cliente
WHERE p.codigo_cliente IS NULL;
```

4. Devuelve el nombre de los clientes que han hecho pagos y el nombre de sus representantes junto con la ciudad de la oficina a la que pertenece el representante.

```sql
SELECT DISTINCT c.nombre_cliente, CONCAT(e.nombre, ' ', e.apellido1) as Representante, ofi.ciudad FROM cliente c
JOIN pago p ON c.codigo_cliente = p.codigo_cliente
JOIN empleado e ON c.codigo_empleado_rep_ventas = e.codigo_empleado
JOIN oficina ofi ON e.codigo_oficina = ofi.codigo_oficina;
```

5. Devuelve el nombre de los clientes que no hayan hecho pagos y el nombre de sus representantes junto con la ciudad de la oficina a la que pertenece el representante.

```sql
SELECT c.nombre_cliente, CONCAT(e.nombre, ' ', e.apellido1) as Representante, ofi.ciudad FROM cliente c
JOIN empleado e ON c.codigo_empleado_rep_ventas = e.codigo_empleado
JOIN oficina ofi ON e.codigo_oficina = ofi.codigo_oficina
LEFT JOIN pago p ON c.codigo_cliente = p.codigo_cliente
WHERE p.codigo_cliente IS NULL;
```

6. Lista la dirección de las oficinas que tengan clientes en Fuenlabrada.
   
```sql
SELECT DISTINCT ofi.linea_direccion1 FROM cliente c
JOIN empleado e ON c.codigo_empleado_rep_ventas = e.codigo_empleado
JOIN oficina ofi ON e.codigo_oficina = ofi.codigo_oficina
WHERE c.ciudad = 'Fuenlabrada';
```

7. Devuelve el nombre de los clientes y el nombre de sus representantes junto con la ciudad de la oficina a la que pertenece el representante.

```sql
SELECT DISTINCT ROW_NUMBER() OVER (ORDER BY c.nombre_cliente) AS num_registro, c.nombre_cliente, CONCAT(e.nombre, ' ', e.apellido1) as Representante, ofi.ciudad FROM cliente c
JOIN empleado e ON c.codigo_empleado_rep_ventas = e.codigo_empleado
JOIN oficina ofi ON e.codigo_oficina = ofi.codigo_oficina;
```

8. Devuelve un listado con el nombre de los empleados junto con el nombre de sus jefes.

```sql
SELECT DISTINCT ROW_NUMBER() OVER (ORDER BY emp.nombre) AS num_registro, CONCAT(emp.nombre, ' ', emp.apellido1) AS 'Nombre Empleado', CONCAT(j1.nombre, ' ', j1.apellido1) AS 'Nombre Jefe' FROM empleado emp
LEFT JOIN empleado j1 ON emp.codigo_jefe = j1.codigo_empleado ORDER BY emp.nombre;
```

9.  Devuelve un listado que muestre el nombre de cada empleados, el nombre de su jefe y el nombre del jefe de sus jefe.

```sql
SELECT DISTINCT ROW_NUMBER() OVER (ORDER BY emp.nombre) AS num_registro, 
CONCAT(emp.nombre, ' ', emp.apellido1) AS 'Nombre Empleado', 
CONCAT(j1.nombre, ' ', j1.apellido1) AS 'Nombre Jefe', 
CONCAT(j2.nombre, ' ', j2.apellido1) AS 'Nombre Jefe de Jefe' 
FROM empleado emp
LEFT JOIN empleado j1 ON emp.codigo_jefe = j1.codigo_empleado 
LEFT JOIN empleado j2 ON j1.codigo_jefe = j2.codigo_empleado
ORDER BY emp.nombre;
```

10. Devuelve el nombre de los clientes a los que no se les ha entregado a tiempo un pedido.

```sql
SELECT DISTINCT c.nombre_cliente FROM cliente c
INNER JOIN pedido p ON c.codigo_cliente = p.codigo_cliente
WHERE p.fecha_entrega > p.fecha_esperada;
```

11. Devuelve un listado de las diferentes gamas de producto que ha comprado cada cliente.

```sql
SELECT ped.codigo_cliente, GROUP_CONCAT(DISTINCT p.gama SEPARATOR ', ') AS Gama FROM producto p
JOIN detalle_pedido dp ON p.codigo_producto = dp.codigo_producto
JOIN pedido ped ON dp.codigo_pedido = ped.codigo_pedido
JOIN pago pg ON pg.codigo_cliente = ped.codigo_cliente
GROUP BY ped.codigo_cliente
ORDER BY ped.codigo_cliente;
```