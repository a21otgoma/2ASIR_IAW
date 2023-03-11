# PROYECTO FINAL IMPLANTACIÓN DE APLICACIONES WEB
- Manuela Otero Gómez
- [AnyCompanyCrafting](https://http://54.167.196.180/)
  

## 1- Objetivos

Se pretende desplegar toda la infraestructura lógica necesaria para llevar a cabo la implantación de un sitio web dinámico en la nube de AWS utilizando las herramientas proporcionadas por la capa gratuíta de aprendizaje y haciendo uso de 
- Apache2
- PHP
- MySQL

Estas herramientas software se implantarán sobre los servicios de AWS

- EC2
- RDS
- Cloud9
- VPCs personalizadas

El contexto narrativo que se ofrece para el proyecto fue proporcionado por la profesora y era el siguiente:
> AnyCompany Crafting te ha contratado para que les ayudes a migrar su sitio web y mercado en línea a la nube.
La tienda venderá su artesanía en línea y permitirá que los clientes creen sus propios escaparates para vender su artesanía en el sitio web. 

## 2- Desarrollo

Los primeros pasos del proyecto son similares a muchas de las prácticas que hemos ido realizando durante el curso:
- Desplegamos una instancia EC2 con un grupo de seguridad propio y reglas de entrada para SSH y HTTP (la de SSh es solo durante la administración)
- Desplegamos una instancia RDS para alojar la base de datos
- Aprovisionamos la EC2 con todos los servicios que vamos a necesitar (Apache, PHP, MySQL...)
- Preparamos un entorno seguro: dos grupos de seguridad, uno con acceso desde el exterior por HTTP en el que estará la EC2 y otro para la RDS al que solo se le abrirá el puerto 3306 y con acceso solo desde el grupo de seguridad de la EC2.
- Creamos nuestra base de datos
- Creamos los archivos [.html](./Proyecto%20final/index.html) y [.php](./Proyecto%20final/php/) para permitir que el sitio web pueda leer y escribir de la base de datos. 

### 2.1- Estructura de la base de datos
  El diseño de la base de datos, como se ha comentado, es conciso pero funcional. De momento consta de dos tablas, una tabla Vendedores en la que se almacenan los datos de todos los responsables de los escaparates (incluido el AnyCompanyCrafting, que a efectos de este enunciado le consideramos un cliente) y una tabla Productos en la que se recogerán todos los productos de cada uno de los vendedores.
  Estas tablas estarán relacionadas mediante una clave foránea en la tabla productos que referencia al identificador del vendedor. 
  
  Esta estructura permite en futuras ampliaciones del proyecto crear tablas de clientes que compran productos, la tabla intermedia que se generaría con la orden de compra etc.

  También se podrían crear triggers para la base de datos que ofreciesen descuentos si el cliente compra un número mínimo de productos. Vistas para que los dueños de los escaparates controlasen sus productos sin que alterasen la base de datos...

### 2.2- Diseño Web
En cuanto al diseño web, aun con la ayuda de bootstrap, podría mejorarse. De momento es funcional con la siguiente estructura:
- Un index.html con la página de bienvenida de AnyCompanyCrafting en el que puedes darte de alta como vendedor a través de un formulario. Cuando completas ese formulario, te aparece la lista de todos los vendedores que ya están registrados.
- Una página cliente1.html en la que un vendedor ya registrado puede empezar a dar de alta sus productos. Cuando acabas de enviar cada uno de ellos, te devuelve una lista de todos los productos de ese cliente. **IMPORTANTE: PARA EMPEZAR A CREAR PRODUCTOS HAY QUE ENTRAR COMO UN CLIENTE YA EXISTENTE** por lo que se recomienda poner un identificador (DNI) fácil de recordar para facilitar la tarea de comprobación de funcionamiento

### 2.3- Securización
La seguridad de nuestro sitio web se raliza en dos frentes principalmente: 
- Uso de los grupos de seguridad creando, como se ha descrito anteriormente, dos grupos uno que tendrá acceso desde el exterior y otro excluivo para la base de datos
- Uso de VPCs propias. Hemos creado una VPC (siguiendo el asistente de AWS) que alojará cuatro subredes diferentes. Estas subredes también se han creado siguiendo el asistente de Amazon. De las cuatro subredes creadas, dos serán púbícas y dos privadas. La idea es que las máquinas escalables de las EC2 con conexión al exterior se coloquen en las subredes públicas (que además están en dos zonas de disponibilidad distintas) y las dos privadas para las RDS. 

###  2.4- Escalabilidad
El planteamiento inicial no incluye más seguridad que la proporcionada por AWS y la estructura de sus grupos de seguridad.También hemos añadido unas Redes Virtuales Privadas  (VPC) propias que hemos subdividido en dos zonas públicas y dos privadas con unas IPs elegidas por nosotros siguiendo la guía proporcionada en la MasterClass. La máquina con el servicio Web la hemos colocado en una pública y la RDS con la base de datos en una privada. Además, están en distintas zonas de disponibilidad para mejorar la tolerancia a fallos. 
Como hemos visto a lo largo del curso en esta y otras asigaturas, esta primera aproximación a la resolución del problema planteado es básca y podría escalarse en el futuro:
- Mejora de la seguridad
  - Se podría añadir un proxy inverso que balancee la carga y haga traducción de red (NAT) para que se enmascare la ip de la máquina que aloja el servicio
  - Se podrían añadir restricciones a la base de datos para que solo pudiesen escribir algunos usuarios, en algunas columnas.
  - Como futura SysAdmin soy consciente de que mi código PHP es muy probable que contenga grandes agujeros de seguridad que en un entorno real pasaría pocas pruebas. 
- Mejora del rendimiento
  - Este despliegue está habilitado para la escalabilidad horizontal, solo habría que añadir tantas instancias como fuesen necesarias para asegurar la Alta Disponibilidad
  - Como futura SysAdmin soy consciente de que mi código PHP es muy probable que esté poco depurado para optimizar el rendimiento
- Mejora de la base de datos
  - La base de datos desplegada es básica pero funcional. También permitiría añadir más tablas con las relaciones pertinentes y convertir ese sitio web del proyecto en un market place como los que todos estamos acostumbrados a utilizar. De hecho, a través de php se podría implementar no solo el registro, sino el acceso autentificado de cada cliente a su escaparate. 

## 3- Presupuesto
Con fines educativos, hemos estado usando el LearningLab que proporciona Amazon Web Service y sus servicios de la capa gratuíta. Los precios generales se pueden consultar [aquí](https://aws.amazon.com/es/pricing/?aws-products-pricing.sort-by=item.additionalFields.productNameLowercase&aws-products-pricing.sort-order=asc&awsf.Free%20Tier%20Type=*all&awsf.tech-category=*all). Presento aquí un resumen del consumo de la capa gratuíta según Amazon:

En un caso real, habría que analizar con el cliente los requiermientos para cubrir sus necesidades y crear los presupuestos con las herramientas de la web anterior. A esto habría que sumar nuestros servicios como profesionales que dependerán de si somos trabajadores de una empresa que proporciona estos servicios con sus cuotas o si somos nosotros los propietarios de esa empresa como socios o autónomos

## 4- Obstáculos
El problema que se plantea con el enunciado citado arriba permite múltiples aproximaciones y soluciones, yo solo he encarado una de ellas.
El principal inconveniente que he afrontado es el manejo tan básico que como Administradora de Sistemas en los programas propios del desarrollo Web como PHP o HTML. Aunque herramientas como bootstrap facilitan la tarea, el resultado final queda muy lejos de algo profesional. Sin embargo, sí que es perfectamente funcional; la base de datos está bien construida y permite ampliaciones, las máquinas están bien aprovisionadas y securizadas, los servicios funcionan perfectamente.

## 5- Conclusiones
Amazon Web Service proporciona un entorno de trabajo independiente de las características de hardware de los equipos de los alumnos a la hora de desplegar máquinas. Además proporciona la seguridad y la alta disponibilidad de un gigante como Amazon respaldando las granjas de servidores. 
Trabajar en la nube, una vez que te haces a los conceptos, no es muy diferente de hacerlo en máquinas locales. Al final son entornos Linux en los que yo me siento cómoda y me muevo con seguridad. 
Para el nivel que tenemos, el despliegue aquí montado nos permite acercarnos al mundo real y ver cómo se trabajaría en un sitio que manejen su propio sitio web o que tengan o pretendan contratar los servicios de la nube de Amazon. 