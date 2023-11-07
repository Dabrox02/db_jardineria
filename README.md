# Base De Datos Jardineria

1. Obtén un listado con el nombre de cada cliente y el nombre y apellido de su representante de ventas.

```sql
SELECT c.nombre_cliente, CONCAT(e.nombre, ' ', e.apellido1) FROM cliente c, empleado e
WHERE c.codigo_empleado_rep_ventas = e.codigo_empleado;
```
