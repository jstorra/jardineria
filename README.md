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

    ```SQL
    SELECT DISTINCT o.codigo_oficina FROM oficina o
    JOIN empleado e ON o.codigo_oficina = e.codigo_oficina
    JOIN cliente c ON e.codigo_empleado = c.codigo_empleado_rep_ventas
    JOIN pago p ON c.codigo_cliente = p.codigo_cliente
    JOIN pedido pe ON c.codigo_cliente = pe.codigo_cliente
    JOIN detalle_pedido dp ON pe.codigo_pedido = dp.codigo_pedido
    JOIN producto pro ON dp.codigo_producto = pro.codigo_producto
    WHERE pro.gama != 'Frutales';
    ```

11. Devuelve un listado con los clientes que han realizado algún pedido pero no han realizado ningún pago.

    ```SQL
    SELECT DISTINCT c.* FROM cliente c
    LEFT JOIN pedido pe ON c.codigo_cliente = pe.codigo_cliente
    LEFT JOIN pago p ON c.codigo_cliente = p.codigo_cliente
    WHERE pe.codigo_cliente IS NOT NULL AND p.codigo_cliente IS NULL;
    ```

12. Devuelve un listado con los datos de los empleados que no tienen clientes asociados y el nombre de su jefe asociado.

    ```SQL
    SELECT DISTINCT e.*, jefe.nombre AS jefe FROM empleado e
    LEFT JOIN cliente c ON e.codigo_empleado = c.codigo_empleado_rep_ventas
    JOIN empleado jefe ON e.codigo_jefe = jefe.codigo_empleado
    WHERE c.codigo_empleado_rep_ventas IS NULL;
    ```

# 1.4.7 Consultas

1. Devuelve un listado con el código de oficina y la ciudad donde hay oficinas.

    ```SQL
    SELECT codigo_oficina, ciudad FROM oficina;
    ```

2. Devuelve un listado con la ciudad y el teléfono de las oficinas de España.

    ```SQL
    SELECT ciudad, telefono FROM oficina WHERE pais = 'España';
    ```

3. Devuelve un listado con el nombre, apellidos y email de los empleados cuyo jefe tiene un código de jefe igual a 7.

    ```SQL
    SELECT nombre, apellido1, apellido2, email FROM empleado WHERE codigo_jefe = 7;
    ```

4. Devuelve el nombre del puesto, nombre, apellidos y email del jefe de la empresa.

    ```SQL
    SELECT nombre, apellido1, apellido2, email, puesto FROM empleado WHERE codigo_jefe IS NULL;
    ```

5. Devuelve un listado con el nombre, apellidos y puesto de aquellos empleados que no sean representantes de ventas.

    ```SQL
    SELECT nombre, apellido1, apellido2, email, puesto FROM empleado
    WHERE puesto != 'Representante Ventas';
    ```

6. Devuelve un listado con el nombre de los todos los clientes españoles.

    ```SQL
    SELECT DISTINCT nombre_cliente, pais FROM cliente WHERE pais = 'Spain';
    ```

7. Devuelve un listado con los distintos estados por los que puede pasar un pedido.

    ```SQL
    SELECT DISTINCT estado FROM pedido;
    ```

8. Devuelve un listado con el código de cliente de aquellos clientes que realizaron algún pago en 2008. Tenga en cuenta que deberá eliminar aquellos códigos de cliente que aparezcan repetidos. Resuelva la consulta:

    - Utilizando la función `Year` de MySQL.

    ```SQL
    SELECT DISTINCT codigo_cliente FROM pago WHERE YEAR(fecha_pago) = 2008;
    ```

    - Utilizando la función `DATE FORMAT` de MySQL.

    ```SQL
    SELECT DISTINCT codigo_cliente FROM pago WHERE DATE_FORMAT(fecha_pago, '%Y') = 2008;
    ```

    - Sin utilizar ninguna de las funciones anteriores.

    ```SQL
    SELECT DISTINCT codigo_cliente FROM pago
    WHERE fecha_pago BETWEEN '2008-01-01' AND '2008-12-31';
    ```

9. Devuelve un listado con el código de pedido, código de cliente, fecha esperada y fecha de entrega de los pedidos que no han sido entregados a tiempo.

    ```SQL
    SELECT codigo_pedido, codigo_cliente, fecha_esperada, fecha_entrega FROM pedido
    WHERE fecha_entrega > fecha_esperada OR fecha_entrega IS NULL
    ORDER BY fecha_entrega IS NULL;
    ```

10. Devuelve un listado con el código de pedido, código de cliente, fecha esperada y fecha de entrega de los pedidos cuya fecha de entrega ha sido al menos dos días antes de la fecha esperada.

    - Utilizando la función `ADDDATE` de MySQL.

    ```SQL
    SELECT codigo_pedido, codigo_cliente, fecha_esperada, fecha_entrega FROM pedido
    WHERE fecha_esperada >= ADDDATE(fecha_entrega, INTERVAL 2 DAY);
    ```

    - Utilizando la función `DATEDIFF` de MySQL.

    ```SQL
    SELECT codigo_pedido, codigo_cliente, fecha_esperada, fecha_entrega FROM pedido
    WHERE DATEDIFF(fecha_esperada, fecha_entrega) >= 2;
    ```

    - ¿Sería posible resolver esta consulta utilizando el operador de suma `+` o resta `-`?

    ```SQL
    SELECT codigo_pedido, codigo_cliente, fecha_esperada, fecha_entrega FROM pedido 
    WHERE fecha_entrega <= (fecha_esperada - INTERVAL 2 DAY);
    ```

11. Devuelve un listado de todos los pedidos que fueron **rechazados** en `2009`.

    ```SQL
    SELECT * FROM pedido WHERE YEAR(fecha_pedido) = 2009 AND estado = 'Rechazado';
    ```

12. Devuelve un listado de todos los pedidos que han sido **entregados** en el mes de enero de cualquier año.

    ```SQL
    SELECT * FROM pedido WHERE MONTH(fecha_entrega) = 01 AND estado = 'Entregado';
    ```

13. Devuelve un listado con todos los pagos que se realizaron en el año `2008` mediante `Paypal`. Ordene el resultado de mayor a menor.

    ```SQL
    SELECT * FROM pago WHERE forma_pago = 'PayPal' AND YEAR(fecha_pago) = 2008
    ORDER BY total DESC;
    ```

14. Devuelve un listado con todas las formas de pago que aparecen en la tabla `pago` . Tenga en cuenta que no deben aparecer formas de pago repetidas.

    ```SQL
    SELECT DISTINCT forma_pago FROM pago;
    ```

15. Devuelve un listado con todos los productos que pertenecen a la gama `Ornamentales` y que tienen más de `100` unidades en stock. El listado deberá estar ordenado por su precio de venta, mostrando en primer lugar los de mayor precio.

    ```SQL
    SELECT * FROM producto WHERE gama = 'Ornamentales' AND cantidad_en_Stock > 100
    ORDER BY precio_venta DESC;
    ```

16. Devuelve un listado con todos los clientes que sean de la ciudad de `Madrid` y cuyo representante de ventas tenga el código de empleado `11` o `30`.

    ```SQL
    SELECT * FROM cliente WHERE ciudad = 'Madrid'
    AND (codigo_empleado_rep_ventas = 11 OR codigo_empleado_rep_ventas = 30);
    ```

# 1.4.8 Subconsultas

## 1.4.8.1 Con operadores básicos de comparación

1. Devuelve el nombre del cliente con mayor límite de crédito.

    ```SQL
    SELECT nombre_cliente FROM cliente WHERE limite_credito = (
        SELECT MAX(limite_credito) FROM cliente);
    ```

2. Devuelve el nombre del producto que tenga el precio de venta más caro.

    ```SQL
    SELECT nombre FROM producto WHERE precio_venta = (SELECT MAX(precio_venta) FROM producto);
    ```

3. Devuelve el nombre del producto del que se han vendido más unidades. (Tenga en cuenta que tendrá que calcular cuál es el número total de unidades que se han vendido de cada producto a partir de los datos de la tabla `detalle_pedido`)

    ```SQL
    SELECT nombre FROM producto WHERE codigo_producto = (
        SELECT codigo_producto FROM detalle_pedido
        GROUP BY codigo_producto
        ORDER BY sum(cantidad) DESC LIMIT 1);
    ```

4. Los clientes cuyo límite de crédito sea mayor que los pagos que haya realizado. (Sin utilizar `INNER JOIN`).

    ```SQL
    SELECT * FROM cliente c WHERE c.limite_credito > (
        SELECT sum(total) FROM pago p
        WHERE c.codigo_cliente = p.codigo_cliente);
    ```

5. Devuelve el producto que más unidades tiene en stock.

    ```SQL
    SELECT * FROM producto WHERE cantidad_en_stock = (
        SELECT MAX(cantidad_en_stock) FROM producto);
    ```

6. Devuelve el producto que menos unidades tiene en stock.

    ```SQL
    SELECT * FROM producto WHERE cantidad_en_stock = (
        SELECT MIN(cantidad_en_stock) FROM producto);
    ```

7. Devuelve el nombre, los apellidos y el email de los empleados que están a cargo de **Alberto Soria**.

    ```SQL
    SELECT nombre, apellido1, apellido2, email FROM empleado WHERE codigo_jefe = (
        SELECT codigo_empleado FROM empleado
        WHERE LOWER(CONCAT(nombre,' ', apellido1)) = 'alberto soria');
    ```

## 1.4.8.2 Subconsultas con ALL y ANY

1. Devuelve el nombre del cliente con mayor límite de crédito.

    ```SQL
    SELECT c.nombre_cliente, c.limite_credito FROM cliente c
    WHERE c.limite_credito >= ALL (
        SELECT limite_credito
        FROM cliente
    );
    ```

2. Devuelve el nombre del producto que tenga el precio de venta más caro.

    ```SQL
    SELECT p.nombre FROM producto p WHERE p.precio_venta >= ALL (
        SELECT precio_venta
        FROM producto
    );
    ```

3. Devuelve el producto que menos unidades tiene en stock.

    ```SQL
    SELECT * FROM producto p WHERE p.cantidad_en_stock <= ALL (
        SELECT cantidad_en_stock
        FROM producto
    );
    ```

## 1.4.8.3 Subconsultas con IN y NOT IN

1. Devuelve el nombre, apellido1 y cargo de los empleados que no representen a ningún cliente.

    ```SQL
    SELECT nombre, apellido1, puesto FROM empleado
    WHERE codigo_empleado IN (
        SELECT codigo_empleado FROM empleado e
        LEFT JOIN cliente c ON e.codigo_empleado = c.codigo_empleado_rep_ventas
        WHERE c.codigo_empleado_rep_ventas IS NULL);
    ```

2. Devuelve un listado que muestre solamente los clientes que no han realizado ningún pago.

    ```SQL
    SELECT * FROM cliente WHERE codigo_cliente IN (
        SELECT c.codigo_cliente FROM cliente c
        LEFT JOIN pago p ON c.codigo_cliente = p.codigo_cliente
        WHERE p.codigo_cliente IS NULL);
    ```

3. Devuelve un listado que muestre solamente los clientes que sí han realizado algún pago.

    ```SQL
    SELECT * FROM cliente WHERE codigo_cliente NOT IN (
        SELECT c.codigo_cliente FROM cliente c
        LEFT JOIN pago p ON c.codigo_cliente = p.codigo_cliente
        WHERE p.codigo_cliente IS NULL);
    ```

4. Devuelve un listado de los productos que nunca han aparecido en un pedido.
    
    ```SQL
    SELECT * FROM producto WHERE codigo_producto IN (
        SELECT p.codigo_producto FROM producto p
        LEFT JOIN detalle_pedido dp ON p.codigo_producto = dp.codigo_producto
        WHERE dp.codigo_producto IS NULL);
    ```

5. Devuelve el nombre, apellidos, puesto y teléfono de la oficina de aquellos empleados que no sean representante de ventas de ningún cliente.

    ```SQL
    SELECT nombre, apellido1, puesto, (
        SELECT o.telefono FROM empleado e
        JOIN oficina o ON e.codigo_oficina = o.codigo_oficina
        WHERE e.codigo_empleado = empleado.codigo_empleado) AS telefono_oficina
    FROM empleado WHERE codigo_empleado IN (
        SELECT codigo_empleado FROM empleado e
        LEFT JOIN cliente c ON e.codigo_empleado = c.codigo_empleado_rep_ventas
        WHERE c.codigo_empleado_rep_ventas IS NULL);
    ```

6. Devuelve las oficinas donde **no trabajan** ninguno de los empleados que hayan sido los representantes de ventas de algún cliente que haya realizado la compra de algún producto de la gama `Frutales`.

    ```SQL
    SELECT * FROM oficina WHERE codigo_oficina IN (
        SELECT DISTINCT o.codigo_oficina FROM oficina o
        JOIN empleado e ON o.codigo_oficina = e.codigo_oficina
        JOIN cliente c ON e.codigo_empleado = c.codigo_empleado_rep_ventas
        JOIN pedido p ON c.codigo_cliente = p.codigo_cliente
        JOIN detalle_pedido dp ON p.codigo_pedido = dp.codigo_pedido
        JOIN producto pro ON dp.codigo_producto = pro.codigo_producto
        WHERE pro.gama != 'Frutales');
    ```

7. Devuelve un listado con los clientes que han realizado algún pedido pero no han realizado ningún pago.

    ```SQL
    SELECT * FROM cliente WHERE codigo_cliente IN (
        SELECT DISTINCT c.codigo_cliente FROM cliente c
        LEFT JOIN pedido pe ON c.codigo_cliente = pe.codigo_cliente
        LEFT JOIN pago p ON c.codigo_cliente = p.codigo_cliente
        WHERE pe.codigo_cliente IS NOT NULL AND p.codigo_cliente IS NULL);
    ```

## 1.4.8.4 Subconsultas con EXISTS y NOT EXISTS

1. Devuelve un listado que muestre solamente los clientes que no han realizado ningún pago.

    ```SQL
    SELECT * FROM cliente c WHERE NOT EXISTS (
        SELECT codigo_cliente FROM pago p WHERE p.codigo_cliente = c.codigo_cliente);
    ```

2. Devuelve un listado que muestre solamente los clientes que sí han realizado algún pago.

    ```SQL
    SELECT * FROM cliente c WHERE EXISTS (
        SELECT codigo_cliente FROM pago p WHERE p.codigo_cliente = c.codigo_cliente);
    ```

3. Devuelve un listado de los productos que nunca han aparecido en un pedido.

    ```SQL
    SELECT * FROM producto pd WHERE NOT EXISTS (
        SELECT * FROM detalle_pedido dp WHERE dp.codigo_producto = pd.codigo_producto);
    ```

4. Devuelve un listado de los productos que han aparecido en un pedido alguna vez.

    ```SQL
    SELECT * FROM producto pd WHERE EXISTS (
        SELECT * FROM detalle_pedido dp WHERE dp.codigo_producto = pd.codigo_producto);
    ```

## VIDEO: 5 TIPS GROUP BY

1. Devuelve el nombre, gama y la cantidad de productos que existen de cada gama.

    ```SQL
    SELECT p.nombre, p.gama, COUNT(*) Total FROM producto p
    JOIN gama_producto g ON p.gama = g.gama
    GROUP BY p.nombre, p.gama;
    ```

2. Devuelve el nombre, gama y la cantidad de productos que existen de cada gama mientras la cantidad sea mayor a **3**.

    ```SQL
    SELECT p.nombre, p.gama, COUNT(*) Total
    FROM producto p
    JOIN gama_producto g ON p.gama = g.gama
    GROUP BY p.nombre, p.gama
    HAVING COUNT(*) > 3;
    ```

3. Devuelve la cantidad de productos que existen por la primera letra del nombre del producto.

    ```SQL
    SELECT SUBSTRING(p.nombre, 1, 1) productoLetra, COUNT(*) Total
    FROM producto p
    GROUP BY SUBSTRING(p.nombre, 1, 1);
    ```

4. Devuelve un listado con el nombre de todos los productos y ademas todos los precios.

    ```SQL
    SELECT DISTINCT CONCAT('Producto: ',nombre) ProductosYPrecios FROM producto
    UNION ALL
    SELECT DISTINCT CONCAT('Precio: $',precio_venta) FROM producto;
    ```

5. Devuelve un listado con el nombre de todos los productos, todos los precios y la cantidad que existe de cada uno.

    ```SQL
    SELECT ProductosYPrecios, COUNT(*) Total
    FROM
        (SELECT CONCAT('Producto: ',nombre) ProductosYPrecios FROM producto
        UNION ALL
        SELECT CONCAT('Precio: $',precio_venta) FROM producto) as newTable
    GROUP BY ProductosYPrecios;
    ```

## VIDEO: 5 TIPS WHERE

1. Devuelve un listado de las gamas que no han aparecen en ningun producto.

    ```SQL
    SELECT g.* FROM gama_producto g
    WHERE g.gama NOT IN (
        SELECT p.gama FROM producto p
        WHERE g.gama = p.gama
    );
    ```

2. Devuelve un listado de las gamas que aparecen en algun producto.

    ```SQL
    SELECT * FROM gama_producto g
    WHERE (
        SELECT COUNT(*)
        FROM producto p
        WHERE g.gama = p.gama
    ) > 0;
    ```

3. Devuelve un listado con los productos que comiencen por la letra **A** ó **C**.

    ```SQL
    SELECT * FROM producto
    WHERE nombre RLIKE '^[AC]' ORDER BY nombre;
    ```

4. Devuelve un listado de las gamas que aparecen en algun producto y que el nombre del producto finaliza con la letra **M**.

    ```SQL
    SELECT g.* FROM gama_producto g
    WHERE g.gama IN (
        SELECT p.gama FROM producto p
        WHERE p.nombre LIKE '%M'
    );
    ```

5. Devuelve un listado de los productos que su nombre comienza con la letra **H**.

    ```SQL
    SELECT * FROM producto
    WHERE SUBSTRING(nombre, 1, 1) = 'H';
    ```

## VIDEO: 5 TIPS UPDATE

1. Actualiza los registros de la tabla `pagos` en su campo **total** y agregale **$1**.

    ```SQL
    UPDATE pago SET total = total + 1;
    ```

2. Actualiza las formas de pago de la tabla `pagos` a su estado por defecto.

    ```SQL
    UPDATE pago SET forma_pago = DEFAULT;
    ```

3. Actualiza los registros de gama en la tabla `pagos` para que se muestre la gama y la descripción correspondiente de esa gama.

    ```SQL
    UPDATE producto p SET gama = (
        SELECT CONCAT(p.gama,' ',g.descripcion_texto)
        FROM gama_producto g
        WHERE p.gama = g.gama
    );
    ```

4. Actualiza el nombre de los productos a `Actualizado` sí su gama contiene letras **O**.

    ```SQL
    UPDATE producto p SET nombre = 'Actualizado'
    WHERE p.gama IN (
        SELECT g.gama FROM gama_producto
        WHERE g.gama LIKE '%o%'
    );
    ```

5. Actualiza la tabla `productos` para que los clientes tambien sean los proveedores.

    ```SQL
    UPDATE producto p
    JOIN detalle_pedido dp ON p.codigo_producto = dp.codigo_producto
    JOIN pedido pe ON dp.codigo_pedido = pe.codigo_pedido
    JOIN cliente c ON pe.codigo_cliente = c.codigo_cliente
    SET p.proveedor = c.nombre_cliente;
    ```

## VIDEO: 5 TIPS SELECT

1. 

    ```SQL

    ```

2. 

    ```SQL

    ```

3. 

    ```SQL

    ```

4. 

    ```SQL

    ```

5. 

    ```SQL

    ```

## Modelo Fisico

![](modelo-fisico.png)

## Uso del Proyecto

Clona este repositorio en tu maquina local:

```BASH
git clone https://github.com/jstorra/jardineria.git
```

---

<p align="center">Developed by <a href="https://github.com/jstorra">@jstorra</a></p>