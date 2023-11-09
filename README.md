# Base De Datos Jardineria

# Consultas Multitabla (Composicion Interna)

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

# Consultas Multitabla (Composicion Externa)

1. Devuelve un listado que muestre solamente los clientes que no han pagado.

```sql
SELECT c.nombre_cliente, c.telefono, c.ciudad FROM cliente c
LEFT JOIN pago p ON c.codigo_cliente = p.codigo_cliente
WHERE p.codigo_cliente IS NUll;
```

2. Devuelve un listado que muestre solamente los clientes que no han realizado ningun pedido.

```sql
SELECT c.nombre_cliente FROM cliente c
LEFT JOIN pedido p ON c.codigo_cliente = p.codigo_cliente
WHERE p.codigo_cliente IS NUll;
```

3. Devuelve un listado que muestre solamente los clientes que no han realizado ningun pago y los que no han realizado ningun pedido.

```sql
SELECT DISTINCT c.nombre_cliente FROM cliente c
LEFT JOIN pago pg ON c.codigo_cliente = pg.codigo_cliente
LEFT JOIN pedido pd ON c.codigo_cliente = pd.codigo_cliente
WHERE pg.codigo_cliente IS NULL 
OR pd.codigo_cliente IS NULL; 
```

4. Devuelve un listado que muestre solamente los empleados que no tienen oficina asociada.

```sql
SELECT e.codigo_oficina, e.nombre FROM empleado e
LEFT JOIN oficina ofi ON e.codigo_oficina = ofi.codigo_oficina
WHERE e.codigo_oficina IS NULL;
```

5. Devuelve un listado que muestre solamente los empleados que no tienen cliente asociado.

```sql
SELECT e.nombre, c.nombre_cliente FROM empleado e
LEFT JOIN cliente c ON e.codigo_empleado = c.codigo_empleado_rep_ventas
WHERE c.codigo_empleado_rep_ventas IS NULL;
```

6. Devuelve un listado que muestre solamente los empleados que no tienen cliente asociado, junto con los datos de la oficina en la que trabaja.

```sql
SELECT e.nombre as Nombre_Empleado, CONCAT(ofi.ciudad, '/', ofi.pais, '/', ofi.codigo_postal, '/', ofi.telefono) as Datos_Oficina FROM empleado e
JOIN oficina ofi ON e.codigo_oficina = ofi.codigo_oficina
LEFT JOIN cliente c ON e.codigo_empleado = c.codigo_empleado_rep_ventas
WHERE c.codigo_empleado_rep_ventas IS NULL;
```

7. Devuelve un listado que muestre los empleados que no tienen oficina asociada y los que no tienen un cliente asociado.

```sql
SELECT e.codigo_empleado, e.nombre, e.codigo_oficina, c.codigo_empleado_rep_ventas FROM empleado e
LEFT JOIN oficina ofi ON e.codigo_oficina = ofi.codigo_oficina
LEFT JOIN cliente c ON e.codigo_empleado = c.codigo_empleado_rep_ventas
WHERE e.codigo_oficina IS NULL OR c.codigo_empleado_rep_ventas IS NULL;
```

8. Devuelve un listado de los productos que nunca han aparecido en un pedido.

```sql
SELECT DISTINCT pd.codigo_producto, pd.nombre FROM producto pd
LEFT JOIN detalle_pedido dp ON pd.codigo_producto = dp.codigo_producto
WHERE dp.codigo_producto IS NULL;
```

9. Devuelve un listado de los productos que nunca han aparecido en un pedido. El resultado debe mostrar, nombre, descripcion y la imagen del producto.

```sql
SELECT DISTINCT pd.codigo_producto, pd.nombre, g.descripcion_texto, g.imagen FROM producto pd
JOIN gama_producto g ON g.gama = pd.gama
LEFT JOIN detalle_pedido dp ON pd.codigo_producto = dp.codigo_producto
WHERE dp.codigo_producto IS NULL;
```

10. Devuelve las oficinas donde no trabajan ninguno de los empleados que hayan sido los representantes de ventas de algún cliente que haya realizado la compra de algún producto de la gama Frutales.

```sql
SELECT DISTINCT em.* FROM oficina ofi
LEFT JOIN empleado em ON em.codigo_oficina = ofi.codigo_oficina
JOIN cliente c ON c.codigo_empleado_rep_ventas = em.codigo_empleado
JOIN pedido ped ON ped.codigo_cliente = c.codigo_cliente
JOIN detalle_pedido dep ON dep.codigo_pedido = ped.codigo_pedido
JOIN producto prod ON dep.codigo_producto = prod.codigo_producto
WHERE LOWER(prod.gama) != 'frutales';
```

11. Devuelve un listado con los clientes que han realizado algún pedido pero no han realizado ningún pago.

```sql
SELECT DISTINCT c.nombre_cliente, pg.codigo_cliente, pd.codigo_cliente FROM cliente c
LEFT JOIN pago pg ON c.codigo_cliente = pg.codigo_cliente
LEFT JOIN pedido pd ON c.codigo_cliente = pd.codigo_cliente
WHERE pg.codigo_cliente IS NULL AND pd.codigo_cliente IS NOT NULL;
```

12. Devuelve un listado con los datos de los empleados que no tienen clientes asociados y el nombre de su jefe asociado.

```sql
SELECT e.nombre as EMPLEADO, jefe.nombre as JEFE, c.nombre_cliente FROM empleado e
LEFT JOIN empleado jefe ON e.codigo_jefe = jefe.codigo_empleado
LEFT JOIN cliente c ON e.codigo_empleado = c.codigo_empleado_rep_ventas
WHERE c.codigo_empleado_rep_ventas IS NULL;
```

# Consultas Tablas individuales.

1. Devuelve un listado con el códiggo de oficina y la ciudad donde hay oficinas.

```sql
SELECT codigo_oficina, ciudad FROM oficina ofi;
```

2. Devuelve un listado con la ciudad, y el telefono de las oficinas de España.

```sql
SELECT ciudad, telefono FROM oficina ofi WHERE LOWER(pais) = 'españa';
```

3. Devuelve un listado con el nombre, apellidos y email de los empleados cuyo jefe tiene un codigo de jefe igual a 7.

```sql
SELECT nombre, apellido1, apellido2 FROM empleado WHERE codigo_jefe = 7;
```

4. Devuelve el nombre del puesto, nombre, apellido y email del jefe de la empresa.

```sql
SELECT nombre, apellido1, apellido2 FROM empleado WHERE codigo_jefe = 7;
```

5. Devuelve un listado con el nombre, apellidos y puesto de aquellos empleados que no sean representantes de ventas.

```sql
SELECT nombre, apellido1, apellido2, puesto FROM empleado WHERE LOWER(puesto) NOT LIKE 'representante ventas';
```

6. Devuelve un listado con el nombre de todos los clientes españoles.

```sql
SELECT nombre_cliente FROM cliente WHERE LOWER(pais) = 'spain';
```

7. Devulve un listado con los distintos estados por los que puede pasar un pedido.

```sql
SELECT estado FROM pedido GROUP BY estado;
```

8. Devuelve un listado con el código de cliente de aquellos clientes que realizaron algun pago en 2008. Tenga en cuenta que debera eliminar aquellos codigos de cliente que aparezcan repetidos. Resuelva la consulta:

- Utilizando la funcion `YEAR` de MYSQL.
  
```sql
SELECT DISTINCT codigo_cliente FROM pago WHERE YEAR(fecha_pago) = 2008;
```

- Utilizando la funcion `DATE_FORMAT` de MYSQL.

```sql
SELECT DISTINCT codigo_cliente FROM pago WHERE DATE_FORMAT(fecha_pago, '%Y') = 2008;
```

- Sin utilizar ninguna de las funciones anteriores.

```sql
SELECT DISTINCT codigo_cliente FROM pago WHERE fecha_pago BETWEEN '2008-01-01' AND '2008-12-31';
```

9. Devuelve un listado con el codigo de pedido, codigo de cliente, fecha esperada y fecha de entrega de los pedidos que no han sido entregados a tiempo.

```sql
SELECT codigo_pedido, codigo_cliente, fecha_esperada, fecha_entrega FROM pedido WHERE fecha_entrega > fecha_esperada;
```

10.  Devuelve un listado con el codigo de pedido, codigo de cliente, fecha esperada y fecha de entrega de los pedidos cuya fecha de entrega ha sido al menos dos dias antes de la fecha esperada.

- Utilizando la funcion `DATE_ADD` de MYSQL.

```sql
SELECT codigo_pedido, codigo_cliente, fecha_esperada, fecha_entrega FROM pedido
WHERE fecha_entrega <= DATE_ADD(fecha_esperada, INTERVAL -2 DAY);
```

- Utilizando la funcion `DATEDIFF` de MYSQL.

```sql
SELECT codigo_pedido, codigo_cliente, fecha_esperada, fecha_entrega FROM pedido
WHERE DATEDIFF(fecha_entrega, fecha_esperada) <= -2;
```

- Seria posible resolver esta consulta utilizando el operador, suma o resta?

```sql
SELECT codigo_pedido, codigo_cliente, fecha_esperada, fecha_entrega, (fecha_esperada - INTERVAL 2 DAY) as Diferencia FROM pedido 
WHERE fecha_entrega <= (fecha_esperada - INTERVAL 2 DAY);

-- ALTERNATIVA 
SELECT codigo_pedido, codigo_cliente, fecha_esperada, fecha_entrega, (fecha_entrega - fecha_esperada) FROM pedido WHERE (fecha_entrega - fecha_esperada) <= -2;
```

11. Devuelve un listado de todos los pedidos que fueron rechazados en 2009.

```sql
SELECT codigo_pedido, codigo_cliente, fecha_esperada, fecha_entrega, (fecha_entrega - fecha_esperada) FROM pedido WHERE (fecha_entrega - fecha_esperada) <= -2;
```

12. Devuelve un listado de todos los pedidos que han sido entregados en el mes de enero de cualquier año.

```sql
SELECT * FROM pedido WHERE LOWER(estado) = 'entregado' AND MONTH(fecha_entrega) = 1;
```

13. Devuelve un listado con todos los pagos que se realizaron en el año 2009 mediante Paypal. Ordene el resultado de mayor a menor.

```sql
SELECT * FROM pago 
WHERE LOWER(forma_pago) = 'paypal' AND YEAR(fecha_pago) = 2009
ORDER BY total;
```

14. Devuelve un listado con todas las formas de pago que aparecen en la tabla pago. Tenga en cuenta que no deben aparecer formas de pago repetidas.

```sql
SELECT forma_pago FROM pago GROUP BY forma_pago;
```

15. Devuelve un listado con todos los productos que pertenecen a la gama `Ornamentales` y que tienen mas de `100` unidades en stock. El listado debera estar ordenado por su precio de venta, mostrando en primer lugar los de mayor precio.

```sql
SELECT * FROM producto 
WHERE LOWER(gama) = 'ornamentales' AND cantidad_en_stock > 100
ORDER BY precio_venta DESC;
```

16. Devuelve un listado con todos los clientes que sean de la ciudad de `Madrid` y cuyo representante de ventas tenga el codigo de empleado 11 o 30.
    
```sql
SELECT * FROM cliente WHERE LOWER(ciudad) = 'madrid' AND codigo_empleado_rep_ventas IN (11, 30);
```