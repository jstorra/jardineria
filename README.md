# 1.4.5 Consultas multitabla (Composición interna)

Resuelva todas las consultas utilizando la sintaxis de `SQL1` y `SQL2`. Las consultas con sintaxis de `SQL2` se deben resolver con `INNER JOIN` y `NATURAL JOIN`.

1. Obtén un listado con el nombre de cada cliente y el nombre y apellido de su representante de ventas.

    ```SQL
    SELECT c.nombre_cliente as Nombre_Cliente,
    CONCAT(e.nombre,' ',e.apellido1,' ',e.apellido2)
    AS Nombre_Representante_Ventas
    FROM cliente c
    JOIN empleado e ON c.codigo_empleado_rep_ventas = e.codigo_empleado;
    ```

2. Muestra el nombre de los clientes que hayan realizado pagos junto con el nombre de sus representantes de ventas.

    ```SQL
    SELECT DISTINCT c.nombre_cliente, e.nombre AS nombre_representante FROM cliente c
    JOIN pago p ON c.codigo_cliente = p.codigo_cliente
    JOIN empleado e ON c.codigo_empleado_rep_ventas = e.codigo_empleado;
    ```

3. Muestra el nombre de los clientes que no hayan realizado pagos junto con el nombre de sus representantes de ventas.

    ```SQL
    SELECT DISTINCT c.nombre_cliente, e.nombre AS nombre_representante FROM cliente c
    LEFT JOIN pago p ON c.codigo_cliente = p.codigo_cliente
    JOIN empleado e ON c.codigo_empleado_rep_ventas = e.codigo_empleado
    WHERE p.codigo_cliente IS NULL;
    ```

4. Devuelve el nombre de los clientes que han hecho pagos y el nombre de sus representantes junto con la ciudad de la oficina a la que pertenece el representante.

    ```SQL
    SELECT DISTINCT c.nombre_cliente, e.nombre AS nombre_representante, o.ciudad FROM cliente c
    JOIN pago p ON c.codigo_cliente = p.codigo_cliente
    JOIN empleado e ON c.codigo_empleado_rep_ventas = e.codigo_empleado
    JOIN oficina o ON e.codigo_oficina = o.codigo_oficina;
    ```

5. Devuelve el nombre de los clientes que no hayan hecho pagos y el nombre de sus representantes junto con la ciudad de la oficina a la que pertenece el representante.

    ```SQL
    SELECT DISTINCT c.nombre_cliente, e.nombre AS nombre_representante, o.ciudad FROM cliente c
    LEFT JOIN pago p ON c.codigo_cliente = p.codigo_cliente
    JOIN empleado e ON c.codigo_empleado_rep_ventas = e.codigo_empleado
    JOIN oficina o ON e.codigo_oficina = o.codigo_oficina
    WHERE p.codigo_cliente IS NULL;
    ```

6. Lista la dirección de las oficinas que tengan clientes en `Fuenlabrada`.

    ```SQL
    SELECT DISTINCT IF(o.linea_direccion2 = '', o.linea_direccion1,
    CONCAT(o.linea_direccion1,' - ',o.linea_direccion2)) AS direccion_oficina
    FROM cliente c
    JOIN empleado e ON c.codigo_empleado_rep_ventas = e.codigo_empleado
    JOIN oficina o ON e.codigo_oficina = o.codigo_oficina
    WHERE c.ciudad = 'Fuenlabrada';
    ```

7. Devuelve el nombre de los clientes y el nombre de sus representantes junto con la ciudad de la oficina a la que pertenece el representante.

    ```SQL
    SELECT DISTINCT c.nombre_cliente, e.nombre AS nombre_representante, o.ciudad FROM cliente c
    JOIN empleado e ON c.codigo_empleado_rep_ventas = e.codigo_empleado
    JOIN oficina o ON e.codigo_oficina = o.codigo_oficina;
    ```

8. Devuelve un listado con el nombre de los empleados junto con el nombre de sus jefes.

    ```SQL
    SELECT e.nombre AS nombre_empleado, e2.nombre AS nombre_jefe FROM empleado e
    LEFT JOIN empleado e2 ON e.codigo_jefe = e2.codigo_empleado;
    ```

9. Devuelve un listado que muestre el nombre de cada empleado, el nombre de su jefe y el nombre del jefe de sus jefe.

    ```SQL
    SELECT e.nombre AS nombre_empleado, e2.nombre AS nombre_jefe, e3.nombre AS nombre_jefe_mayor
    FROM empleado e
    LEFT JOIN empleado e2 ON e.codigo_jefe = e2.codigo_empleado
    LEFT JOIN empleado e3 ON e2.codigo_jefe = e3.codigo_empleado;
    ```

10. Devuelve el nombre de los clientes a los que no se les ha entregado a tiempo un pedido.

    ```SQL
    SELECT DISTINCT nombre_cliente FROM cliente c
    JOIN pedido p ON c.codigo_cliente = p.codigo_cliente
    WHERE fecha_entrega > fecha_esperada OR fecha_entrega IS NULL;
    ```

11. Devuelve un listado de las diferentes gamas de producto que ha comprado cada cliente.

    ```SQL
    SELECT c.codigo_cliente, c.nombre_cliente, GROUP_CONCAT(DISTINCT pro.gama SEPARATOR ', ')
    AS gamas_producto 
    FROM cliente c
    JOIN pedido pe ON pe.codigo_cliente = c.codigo_cliente
    JOIN detalle_pedido dp ON dp.codigo_pedido = pe.codigo_pedido
    JOIN pago pag ON pag.codigo_cliente = c.codigo_cliente
    JOIN producto pro ON dp.codigo_producto = pro.codigo_producto
    GROUP BY c.codigo_cliente
    ORDER BY c.codigo_cliente;
    ```

# 1.4.6 Consultas multitabla (Composición externa)

Resuelva todas las consultas utilizando las cláusulas LEFT JOIN, RIGHT JOIN, NATURAL LEFT JOIN y NATURAL RIGHT D0IN 

1. Devuelve un listado que muestre solamente los clientes que no han realizado ningún pago.

    ```SQL
    SELECT * FROM cliente c
    LEFT JOIN pago p ON c.codigo_cliente = p.codigo_cliente
    WHERE p.codigo_cliente IS NULL; 
    ```

2. Devuelve un listado que muestre solamente los clientes que no han realizado ningún pedido.

    ```SQL
    SELECT * FROM cliente c
    LEFT JOIN pedido p ON c.codigo_cliente = p.codigo_cliente
    WHERE p.codigo_cliente IS NULL;
    ```

3. Devuelve un listado que muestre los clientes que no han realizado ningún pago y los que no han realizado ningún pedido.

    ```SQL
    SELECT DISTINCT c.nombre_cliente FROM cliente c
    LEFT JOIN pago p ON c.codigo_cliente = p.codigo_cliente
    LEFT JOIN pedido pe ON c.codigo_cliente = pe.codigo_cliente
    WHERE p.codigo_cliente IS NULL OR pe.codigo_cliente IS NULL
    ORDER BY c.nombre_cliente;
    ```

4. Devuelve un listado que muestre solamente los empleados que no tienen una oficina asociada.

    ```SQL
    SELECT * FROM empleado e
    LEFT JOIN oficina o ON e.codigo_oficina = o.codigo_oficina
    WHERE o.codigo_oficina IS NULL;
    ```

5. Devuelve un listado que muestre solamente los empleados que no tienen un cliente asociado.

    ```SQL
    SELECT e.nombre FROM empleado e
    LEFT JOIN cliente c ON e.codigo_empleado = c.codigo_empleado_rep_ventas
    WHERE codigo_empleado_rep_ventas IS NULL
    ORDER BY e.nombre;
    ```

6. Devuelve un listado que muestre solamente los empleados que no tienen un cliente asociado junto con los datos de la oficina donde trabajan.

    ```SQL
    SELECT e.*, o.* FROM empleado e
    LEFT JOIN cliente c ON e.codigo_empleado = c.codigo_empleado_rep_ventas
    JOIN oficina o ON e.codigo_oficina = o.codigo_oficina
    WHERE codigo_empleado_rep_ventas IS NULL
    ORDER BY e.codigo_empleado;
    ```

7. Devuelve un listado que muestre los empleados que no tienen una oficina asociada y los que no tienen un cliente asociado

    ```SQL
    SELECT e.* FROM empleado e
    LEFT JOIN oficina o ON e.codigo_oficina = o.codigo_oficina
    LEFT JOIN cliente c ON e.codigo_empleado = c.codigo_empleado_rep_ventas
    WHERE o.codigo_oficina IS NULL OR c.codigo_empleado_rep_ventas IS NULL;
    ```

8. Devuelve un listado de los productos que nunca han aparecido en un pedido.

    ```SQL
    SELECT p.codigo_producto, p.nombre FROM producto p
    LEFT JOIN detalle_pedido dp ON p.codigo_producto = dp.codigo_producto
    WHERE dp.codigo_producto IS NULL
    ORDER BY p.nombre;
    ```

9. Devuelve un listado de los productos que nunca han aparecido en un pedido. El resultado debe mostrar el nombre, la descripción y la imagen del producto

    ```SQL
    SELECT p.codigo_producto, p.nombre, p.descripcion, g.imagen FROM producto p
    LEFT JOIN detalle_pedido dp ON p.codigo_producto = dp.codigo_producto
    JOIN gama_producto g ON p.gama = g.gama
    WHERE dp.codigo_producto IS NULL
    ORDER BY p.nombre;
    ```

10. Devuelve las oficinas donde no trabajan ninguno de los empleados que hayan sido los representantes de ventas de algún cliente que haya realizado la compra de algún producto de la gama Frutales.

11. Devuelve un listado con los clientes que han realizado algún pedido pero no han realizado ningún pago.

12. Devuelve un listado con los datos de los empleados que no tienen clientes asociados y el nombre de su jefe asociado.

## Modelo Fisico

![](modelo-fisico.png)

## Uso del Proyecto

Clona este repositorio en tu maquina local:

```BASH
git clone https://github.com/jstorra/jardineria.git
```

---

<p align="center">Developed by <a href="https://github.com/jstorra">@jstorra</a></p>