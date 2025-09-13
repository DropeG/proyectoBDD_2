# Informe E1:

## 1. Entidades:

#### Entidades Fuertes:

- Persona
- InstituciónSalud
- Consulta
- ArancelDC
- Medicamentos
- PlanIsapre
- PrestaciónFonasa
- BonificaciónPorGrupo

#### Entidades Debiles:

- Receta: Depende de Consulta y Persona
- Orden: Depende de Consulta y Persona

## 2. Llaves:

#### **Llaves primarias**

- **Persona**: llave primaria es **run** porque el run es unico en Chile y permite identificar a cada persona.
- **Paciente, Trabajador, Medico y Administrativo**: hereda **run** de Persona.
- **Consulta**: llave primaria es **id_consulta**, cada consulta tiene que tener un identificador único que la distinga.
- **Orden**: llave primaria es **id_orden + id_consulta** (entidad débil), una orden la identificamos dentro de la consulta que la genero, el id_orden solo no basta porque se puede repetir en distintas consultas.
- **Receta**: llave primaria es **id_receta + id_consulta** (entidad débil, una receta depende de la consulta. El id_receta es un numero de linea dentro de la consulta, por lo que necesitamos el id_consulta para identificarla globalmente.
- **Medicamento**: llave primaria es **cod_generico**, cada medicamento tiene un código único en el sistema.
- **PrestaciónFonasa**: llave primaria es **cod_fonasa + cod_adicional**, en FONASA, la clave de prestación esta compuesta por el codigo principal y el adicional, que juntos identifican de manera única la prestación.
- **ArancelDC**: llave primaria es **cod_dc**, cada prestación en el arancel interno de la institución tiene un código único.
- **BonificaciónPorGrupo**: llave primaria es **grupo_fonasa**, cada grupo de FONASA (A, B, C, D) tiene un porcentaje de bonificación único.
- **InstituciónSalud**: llave primaria es **cod_ministerial**, el código ministerial es único.
- **PlanIsapre**: llave primaria es **cod_ministerial**

#### **Llaves parciales**:

- **Orden**: La llave parcial es **id_orden**, ya que sólo distingue órdenes dentro de una misma consulta.
- **Receta**: La llave parcial es **id_receta**, solo distingue recetas dentro de una consulta.

#### **Utilidad**:

- Garantizan **unicidad** de cada entidad en el sistema.
- Facilitan la **integridad referencial**, asegurando que recetas y órdenes no existan sin la consulta que las origina.
- Las llaves parciales son esenciales para modelar entidades que no tienen autonomía y cuya identificación depende de otra Entidad.

## 3. Cardinalidades

- **Consulta – genera – Orden**: Cardinalidad: **1:N**, porque de una consulta médica pueden derivarse múltiples órdenes de exámenes o procedimientos. Sin embargo cada orden pertenece a una sola consulta.
- **Consulta – genera – Receta**: Cardinalidad: **1:N**, una misma consulta puede generar varias recetas, pero cada receta esta asociada a una única consulta.
- **Receta – incluye – Medicamento**: Cardinalidad: **N:M**, una receta puede incluir varos medicamentos, y un mismo medicamento puede estar presente en distintas recetas. 
- **Orden – incluye – PrestacionFonasa**: Cardinalidad:**N:M**, una orden puede incluir múltiples prestaciones médicas, y una misma prestación puede aparecer en muchas órdenes.
- **PrestacionFonasa – pertenece a – ArancelDC**: Cardinalidad: **N:1**, cada prestación FONASA está descrita en un único arancel institucional, pero un arancel puede contener muchas prestaciones.
- **Paciente – recibe – Consulta**: Cardinalidad: **1:N**, un paciente puede recibir muchas consultas a lo largo del tiempo, pero cada consulta corresponde a un único paciente
-  **Médico – atiende – Consulta**: Cardinalidad: **1:N**, un médico atiende múltiples consultas, pero cada consulta la atiende un único médico.
- **PlanIsapre – pertenece – InstituciónSalud**: Cardinalidad: **N:1**, cada plan está asociado a una sola institución de salud, pero una institución puede tener varios planes. 

## 4. Justificación Jerarquias:

#### **Justificación**:

Se establecio que una **Persona** puede ser varios roles:
-   **Paciente**, cuando es quien recibe las atenciones médicas.
        
-   **Trabajador**, cuando participa como personal de la institución de salud. Este a su vez se especializa en:
        
-   **Médico**, con atributos propios como la profesión.
            
-   **Administrativo**, encargado de funciones no médicas.

Esta jerarquía permite **reutilizar atributos comunes** (RUN, nombre, apellidos, dirección, email, teléfono) en la superentidad **Persona**, evitando duplicación de información y manteniendo consistencia.
    
-   **Ventajas del uso**:
    
    -   Modela de manera clara que todos los pacientes y trabajadores son personas, pero con roles distintos.
            
    -   Facilita la extensibilidad futura (si se agregara, por ejemplo, un rol de “Enfermera”).
        
No se emplearon jerarquías en entidades como **PrestaciónFonasa** o **Receta**, porque no presentan subtipos conceptuales relevantes. En esos casos bastan atributos o relaciones adicionales.

## 5. Esquema Relacional Normalizado en BCNF:

-   **PERSONA**( **run**: VARCHAR(12), nombres: VARCHAR(80), apellidos: VARCHAR(80), direccion: VARCHAR(120), email: VARCHAR(120), telefono: VARCHAR(20) )
    
-   **PACIENTE**( **run**: VARCHAR(12) FK→PERSONA )
    
-   **TRABAJADOR**( **run**: VARCHAR(12) FK→PERSONA )
    
-   **MEDICO**( **run**: VARCHAR(12) FK→TRABAJADOR, profesion: VARCHAR(80) )
    
-   **ADMINISTRATIVO**( **run**: VARCHAR(12) FK→TRABAJADOR )

-   **CONSULTA**( **id_consulta**: INT, run_paciente: VARCHAR(12) FK→PACIENTE, run_medico: VARCHAR(12) FK→MEDICO, fecha: DATE, firma_doctor: VARCHAR(80), diagnostico: TEXT )
    
-   **RECETA**( **id_consulta**: INT FK→CONSULTA, **id_receta**: INT, fecha: DATE, diagnostico: TEXT, firma_doctor: VARCHAR(80), es_psicotropica: BOOLEAN, cod_auth: VARCHAR(80) )
    
-   **ORDEN**( **id_consulta**: INT FK→CONSULTA, **id_orden**: INT, fecha: DATE, diagnostico: TEXT, firma_doctor: VARCHAR(80) )
-   **MEDICAMENTO**( **cod_generico**: VARCHAR(20), nombre_generico: VARCHAR(80), tipo: VARCHAR(40) )
    
-   **RECETA_MEDICAMENTO**( **id_consulta**: INT, **id_receta**: INT, **cod_generico**: VARCHAR(20) FK→MEDICAMENTO, cantidad: INT CHECK(cantidad > 0), PK compuesta )
-   **ARANCEL_DC**( **cod_dc**: VARCHAR(20), nombre_dc: VARCHAR(80), valor_dc: INT, tipo: VARCHAR(40) )
    
-   **PRESTACION_FONASA**( **cod_fonasa**: VARCHAR(20), **cod_adicional**: VARCHAR(10), nombre: VARCHAR(120), valor_fonasa: INT, grupo: VARCHAR(10), cod_dc: VARCHAR(20) FK→ARANCEL_DC )
-   **BONIFICACION_GRUPO**( **grupo_fonasa**: CHAR(1), porcentaje: FLOAT )
-   **INSTITUCION_SALUD**( **cod_ministerial**: VARCHAR(20), nombre: VARCHAR(80), tipo: VARCHAR(40), enlace: VARCHAR(200), rut: VARCHAR(12) )
    
-   **PLAN_ISAPRE**( **cod_plan**: VARCHAR(20), cod_ministerial: VARCHAR(20) FK→INSTITUCION_SALUD )

## 6. Relaciones que se transforman en tablas:

 -  **Consulta – Orden**: se añade la clave primaria de **Consulta** como clave foránea en la tabla **Orden**.
 -  **Orden - PrestaciónFonasa**: una **Orden** puede incluir varios Exámenes o Procedimientos y un mismo examen puede estar presente en distintas órdenes
 - **Paciente - Consulta**: un paciente puede tener muchas **Consultas**, cada **Consulta** corresponde a un único paciente. Clave foránea de **Paciente** en **Consulta**.

## 7. Llaves foráneas y surrogate:

#### Llaves foráneas:

-   **PACIENTE(run)** → FK a **PERSONA(run)**
    
-   **TRABAJADOR(run)** → FK a **PERSONA(run)**
    
-   **MEDICO(run)** → FK a **TRABAJADOR(run)**
    
-   **ADMINISTRATIVO(run)** → FK a **TRABAJADOR(run)**
    
-   **CONSULTA(run_paciente)** → FK a **PACIENTE(run)**
    
-   **CONSULTA(run_medico)** → FK a **MEDICO(run)**
    
-   **RECETA(id_consulta)** → FK a **CONSULTA(id_consulta)**
    
-   **ORDEN(id_consulta)** → FK a **CONSULTA(id_consulta)**
    
-   **PRESTACION_FONASA(cod_dc)** → FK a **ARANCEL_DC(cod_dc)**
    
-   **PLAN_ISAPRE(cod_ministerial_inst)** → FK a **INSTITUCION_SALUD(cod_ministerial)**

#### Surrogate Key:

-   **CONSULTA(id_consulta):** se introduce un identificador artificial (`id_consulta` autoincremental o UUID) para distinguir de manera única cada consulta.
    
-   **ORDEN(id_consulta, id_orden):** se usa `id_orden` como un número de línea dentro de la consulta; en este caso actúa como **llave parcial**, pero su combinación con `id_consulta` conforma la PK.
    
-   **RECETA(id_consulta, id_receta):** igual que la orden, `id_receta` funciona como identificador local dentro de la consulta.

## 8. Fidelidad, Redundancia, Anomalías y Simplicidad:

El diseño resuelve los principales criterios. Garantiza **Fidelidad** al modelo E/R, ya que todas las entidades, relaciones y atributos del dominio fueron representados de buena manera en el esquema sin perder información. En segundo lugar, evita la **Redundancia**, dado que cada hecho del mundo real se almacena en una única tabla y no se repiten atributos innecesarios, por ejemplo los datos de la persona se registran una sola vez en **Persona**, lo anterior contribuye a prevenir **Anomalias** de inserción, actualización y eliminación, ya que todos los atributos dependen exclusivamente de la clave primaria de su relación. Se logro también la **Simplicidad**, se opto por un diseño claro y normalizado, en el que cada tabla tiene un propósito bien definido. Y por ultimo hay una buena elección de llaves primarias, se privilegia el uso de llaves naturales cuando existen, y se recurre a las surrogates keys en algunos casos.


![Diagrama del modelo E/R](./crazyFrog.svg)


    
        
        

        

    


    

