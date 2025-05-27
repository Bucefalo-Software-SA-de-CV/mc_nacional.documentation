# Documentación de Filtrado, Ordenación y Paginación de la API REST

Esta documentación describe cómo utilizar los parámetros de consulta para filtrar, ordenar y paginar los resultados devueltos por la API.

## 1. Filtrado (Parámetro `filter`)

El filtrado se realiza utilizando el parámetro `filter` con una sintaxis anidada que especifica el campo, el operador y el valor.

### 1.1. Operadores Básicos

#### 1.1.1. Igualdad (`eq`)

Busca una coincidencia exacta.

- **URL Ejemplo:**

  ```http
  GET https://myapi.com/api/users?filter[name][eq]=Alice
  ```

- **Equivalente SQL:**

  ```sql
  SELECT * FROM users WHERE name = 'Alice';
  ```

- **Compatibilidad:**
  - Firestore: **Compatible** (operador `==`)
  - BigQuery: **Compatible** (operador `=`)

#### 1.1.2. Mayor que (`gt`)

Busca valores estrictamente mayores que el valor proporcionado.

- **URL Ejemplo:**

  ```http
  GET https://myapi.com/api/products?filter[price][gt]=100
  ```

- **Equivalente SQL:**

  ```sql
  SELECT * FROM products WHERE price > 100;
  ```

- **Compatibilidad:**
  - Firestore: **Compatible** (operador `>`)
  - BigQuery: **Compatible** (operador `>`)

#### 1.1.3. Mayor o igual que (`gte`)

Busca valores mayores o iguales que el valor proporcionado.

- **URL Ejemplo:**

  ```http
  GET https://myapi.com/api/products?filter[price][gte]=100
  ```

- **Equivalente SQL:**

  ```sql
  SELECT * FROM products WHERE price >= 100;
  ```

- **Compatibilidad:**
  - Firestore: **Compatible** (operador `>=`)
  - BigQuery: **Compatible** (operador `>=`)

#### 1.1.4. Menor que (`lt`)

Busca valores estrictamente menores que el valor proporcionado.

- **URL Ejemplo:**

  ```http
  GET https://myapi.com/api/events?filter[attendees][lt]=50
  ```

- **Equivalente SQL:**

  ```sql
  SELECT * FROM events WHERE attendees < 50;
  ```

- **Compatibilidad:**
  - Firestore: **Compatible** (operador `<`)
  - BigQuery: **Compatible** (operador `<`)

#### 1.1.5. Menor o igual que (`lte`)

Busca valores menores o iguales que el valor proporcionado.

- **URL Ejemplo:**

  ```http
  GET https://myapi.com/api/events?filter[attendees][lte]=50
  ```

- **Equivalente SQL:**

  ```sql
  SELECT * FROM events WHERE attendees <= 50;
  ```

- **Compatibilidad:**
  - Firestore: **Compatible** (operador `<=`)
  - BigQuery: **Compatible** (operador `<=`)

#### 1.1.6. Contiene (`contains`)

Busca si un campo de texto contiene una subcadena específica (case-sensitive o insensitive dependiendo de la base de datos subyacente).

- **URL Ejemplo:**

  ```http
  GET https://myapi.com/api/documents?filter[title][contains]=report
  ```

- **Equivalente SQL (Ejemplo):**

  ```sql
  -- Asumiendo case-insensitive LIKE en la mayoría de SQLs
  SELECT * FROM documents WHERE title LIKE '%report%';
  -- En BigQuery, podría ser:
  -- SELECT * FROM documents WHERE CONTAINS_SUBSTR(title, 'report');
  ```

- **Compatibilidad:**
  - Firestore: **No Compatible Nativamente.** Requiere soluciones alternativas (ej: indexación externa con Algolia/Typesense, o almacenar arrays de tokens).
  - BigQuery: **Compatible** (usando `LIKE` o `CONTAINS_SUBSTR`).

#### 1.1.7. Like (`like`)

Similar a `contains`, permite el uso de comodines SQL (`%` para cualquier secuencia, `_` para un solo carácter). El valor debe incluir los comodines.

- **URL Ejemplo:**

  ```http
  GET https://myapi.com/api/users?filter[email][like]=%@example.com
  ```

- **Equivalente SQL:**

  ```sql
  SELECT * FROM users WHERE email LIKE '%@example.com';
  ```

- **Compatibilidad:**
  - Firestore: **No Compatible Nativamente.**
  - BigQuery: **Compatible**.

#### 1.1.8. Comienza con (`startswith`)

Busca si un campo de texto comienza con una cadena específica.

- **URL Ejemplo:**

  ```http
  GET https://myapi.com/api/codes?filter[prefix][startswith]=INV-
  ```

- **Equivalente SQL:**

  ```sql
  SELECT * FROM codes WHERE prefix LIKE 'INV-%';
  -- En BigQuery, también:
  -- SELECT * FROM codes WHERE STARTS_WITH(prefix, 'INV-');
  ```

- **Compatibilidad:**
  - Firestore: **Parcialmente Compatible.** Se puede lograr con consultas de rango (`>= 'INV-'` y `< 'INV.'`), pero es menos directo y puede tener limitaciones.
  - BigQuery: **Compatible** (usando `LIKE` o `STARTS_WITH`).

#### 1.1.9. Termina con (`endswith`)

Busca si un campo de texto termina con una cadena específica.

- **URL Ejemplo:**

  ```http
  GET https://myapi.com/api/files?filter[filename][endswith]=.pdf
  ```

- **Equivalente SQL:**

  ```sql
  SELECT * FROM files WHERE filename LIKE '%.pdf';
  -- En BigQuery, también:
  -- SELECT * FROM files WHERE ENDS_WITH(filename, '.pdf');
  ```

- **Compatibilidad:**
  - Firestore: **No Compatible Nativamente.**
  - BigQuery: **Compatible** (usando `LIKE` o `ENDS_WITH`).

### 1.2. Agrupación Lógica (`and`, `or`, `not`)

Permiten combinar múltiples condiciones. Se requiere un índice numérico (`[0]`, `[1]`, ...) para agrupar condiciones bajo el mismo operador lógico, excepto si solo hay una condición (el índice `[0]` es implícito).

#### 1.2.1. AND Lógico

Todas las condiciones dentro del grupo `and` deben cumplirse. Los filtros simples aplicados al mismo nivel jerárquico se combinan implícitamente con AND.

- **URL Ejemplo (con índice explícito):**

  ```http
  GET https://myapi.com/api/users?filter[and][0][status][eq]=active&filter[and][1][role][eq]=admin
  ```

- **URL Ejemplo (AND implícito):**

  ```http
  GET https://myapi.com/api/users?filter[status][eq]=active&filter[role][eq]=admin
  ```

- **URL Ejemplo (Múltiples operadores en un campo):**

  ```http
  GET https://myapi.com/api/products?filter[price][gte]=50&filter[price][lte]=100
  ```

- **Equivalente SQL (para los 3 ejemplos):**

  ```sql
  SELECT * FROM users WHERE status = 'active' AND role = 'admin';
  -- o
  SELECT * FROM products WHERE price >= 50 AND price <= 100;
  ```

- **Compatibilidad:**
  - Firestore: **Compatible** (las consultas compuestas logran el AND). Requiere índices compuestos.
  - BigQuery: **Compatible**.

#### 1.2.2. OR Lógico

Al menos una de las condiciones dentro del grupo `or` debe cumplirse.

- **URL Ejemplo:**

  ```http
  GET https://myapi.com/api/orders?filter[or][0][status][eq]=pending&filter[or][1][status][eq]=processing
  ```

- **Equivalente SQL:**

  ```sql
  SELECT * FROM orders WHERE status = 'pending' OR status = 'processing';
  ```

- **Compatibilidad:**
  - Firestore: **Limitado.** Solo compatible con el operador `IN` (hasta 30 valores) para múltiples igualdades (`eq`) sobre el _mismo_ campo. El `OR` general entre diferentes campos o diferentes operadores no es directamente soportado y requiere múltiples consultas separadas.
  - BigQuery: **Compatible**.

#### 1.2.3. NOT Lógico

Niega la condición o grupo de condiciones internas.

- **URL Ejemplo (Índice Opcional para una condición):**

  ```http
  GET https://myapi.com/api/users?filter[not][status][eq]=inactive
  ```

- **URL Ejemplo (Con índice explícito):**

  ```http
  GET https://myapi.com/api/users?filter[not][0][status][eq]=inactive
  ```

- **URL Ejemplo (Negando un grupo OR):**

  ```http
  GET https://myapi.com/api/tasks?filter[not][0][or][0][priority][eq]=low&filter[not][0][or][1][priority][eq]=medium
  ```

- **Equivalente SQL:**

  ```sql
  SELECT * FROM users WHERE NOT (status = 'inactive');
  -- o (para el grupo OR negado)
  SELECT * FROM tasks WHERE NOT (priority = 'low' OR priority = 'medium');
  ```

- **Compatibilidad:**
  - Firestore: **Limitado.** Compatible con el operador `!=` (no igual). La negación de grupos complejos (`NOT (A OR B)`) no es soportada directamente.
  - BigQuery: **Compatible**.

#### 1.2.4. Anidación

Los operadores lógicos se pueden anidar.

- **URL Ejemplo:** (Buscar usuarios activos que sean 'admin' O cuyo email termine en '@company.com')

  ```http
  GET https://myapi.com/api/users?filter[and][0][status][eq]=active&filter[and][1][or][0][role][eq]=admin&filter[and][1][or][1][email][endswith]=@company.com
  ```

- **Equivalente SQL:**

  ```sql
  SELECT * FROM users
  WHERE status = 'active'
    AND (role = 'admin' OR email LIKE '%@company.com');
  ```

- **Compatibilidad:**
  - Firestore: **Muy Limitado/No Compatible** debido a las limitaciones con `OR`, `NOT` y `endswith`.
  - BigQuery: **Compatible**.

### 1.3. Operadores para Campos JSON

Estos operadores permiten consultar datos dentro de campos que almacenan objetos o arrays JSON. Son especialmente útiles con bases de datos como BigQuery.

#### 1.3.1. `containskey`

Verifica si una clave específica existe en el objeto JSON.

- **URL Ejemplo:**
  
  ```http
  GET https://myapi.com/api/data?filter[jsonData][containskey]=configKey
  ```

- **Equivalente SQL (BigQuery):**
  
  ```sql
  SELECT * FROM data_table
  WHERE EXISTS (
      SELECT 1 FROM UNNEST(JSON_KEYS(jsonData)) as key WHERE key = 'configKey'
  );
  ```

- **Compatibilidad:**
  - Firestore: No Compatible Nativamente.
  - BigQuery: **Compatible**.

#### 1.3.2. `keystartswith`

Verifica si alguna clave en el objeto JSON comienza con un prefijo.

- **URL Ejemplo:**
  
  ```http
  GET https://myapi.com/api/data?filter[jsonData][keystartswith]=user_
  ```

- **Equivalente SQL (BigQuery):**
  
  ```sql
  SELECT * FROM data_table
  WHERE EXISTS (
      SELECT 1 FROM UNNEST(JSON_KEYS(jsonData)) AS key WHERE key LIKE 'user_%'
  );
  ```

- **Compatibilidad:**
  - Firestore: No Compatible Nativamente.
  - BigQuery: **Compatible**.

#### 1.3.3. `keyendswith`

Verifica si alguna clave en el objeto JSON termina con un sufijo.

- **URL Ejemplo:**
  
  ```http
  GET https://myapi.com/api/data?filter[jsonData][keyendswith]=_id
  ```

- **Equivalente SQL (BigQuery):**
  
  ```sql
  SELECT * FROM data_table
  WHERE EXISTS (
      SELECT 1 FROM UNNEST(JSON_KEYS(jsonData)) AS key WHERE key LIKE '%_id'
  );
  ```

- **Compatibilidad:**
  - Firestore: No Compatible Nativamente.
  - BigQuery: **Compatible**.

#### 1.3.4. `keymatches`

Verifica si alguna clave coincide con un patrón (LIKE).

- **URL Ejemplo:**
  
  ```http
  GET https://myapi.com/api/data?filter[jsonData][keymatches]=%config%
  ```

- **Equivalente SQL (BigQuery):**
  
  ```sql
  SELECT * FROM data_table
  WHERE EXISTS (
      SELECT 1 FROM UNNEST(JSON_KEYS(jsonData)) AS key WHERE key LIKE '%config%'
  );
  ```

- **Compatibilidad:**
  - Firestore: No Compatible Nativamente.
  - BigQuery: **Compatible**.

#### 1.3.5. `containsvalue`

Verifica si algún valor dentro del objeto JSON es igual al valor proporcionado.

- **URL Ejemplo:**
  
  ```http
  GET https://myapi.com/api/data?filter[jsonData][containsvalue]=active
  ```

- **Equivalente SQL (BigQuery):**
  
  ```sql
  SELECT * FROM data_table
  WHERE EXISTS (
      SELECT 1 FROM UNNEST(JSON_KEYS(jsonData)) as key
      WHERE SAFE_CAST(JSON_EXTRACT_SCALAR(jsonData, CONCAT('$.', key)) AS STRING) = 'active'
  );
  ```

- **Compatibilidad:**
  - Firestore: No Compatible Nativamente.
  - BigQuery: **Compatible**.

#### 1.3.6. `valuestartswith`

Verifica si algún valor (tratado como string) comienza con un prefijo.

- **URL Ejemplo:**
  
  ```http
  GET https://myapi.com/api/data?filter[jsonData][valuestartswith]=admin
  ```

- **Equivalente SQL (BigQuery):**
  
  ```sql
  SELECT * FROM data_table
  WHERE EXISTS (
      SELECT 1 FROM UNNEST(JSON_KEYS(jsonData)) AS key
      WHERE SAFE_CAST(JSON_EXTRACT_SCALAR(jsonData, CONCAT('$.', key)) AS STRING) LIKE 'admin%'
  );
  ```

- **Compatibilidad:**
  - Firestore: No Compatible Nativamente.
  - BigQuery: **Compatible**.

#### 1.3.7. `valueendswith`

Verifica si algún valor (tratado como string) termina con un sufijo.

- **URL Ejemplo:**
  
  ```http
  GET https://myapi.com/api/data?filter[jsonData][valueendswith]=.com
  ```

- **Equivalente SQL (BigQuery):**
  
  ```sql
  SELECT * FROM data_table
  WHERE EXISTS (
      SELECT 1 FROM UNNEST(JSON_KEYS(jsonData)) AS key
      WHERE SAFE_CAST(JSON_EXTRACT_SCALAR(jsonData, CONCAT('$.', key)) AS STRING) LIKE '%.com'
  );
  ```

- **Compatibilidad:**
  - Firestore: No Compatible Nativamente.
  - BigQuery: **Compatible**.

#### 1.3.8. `valuematches`

Verifica si algún valor (tratado como string) contiene un patrón (LIKE).

- **URL Ejemplo:**
  
  ```http
  GET https://myapi.com/api/data?filter[jsonData][valuematches]=%error%
  ```

- **Equivalente SQL (BigQuery):**
  
  ```sql
  SELECT * FROM data_table
  WHERE EXISTS (
      SELECT 1 FROM UNNEST(JSON_KEYS(jsonData)) AS key
      WHERE SAFE_CAST(JSON_EXTRACT_SCALAR(jsonData, CONCAT('$.', key)) AS STRING) LIKE '%error%'
  );
  ```

- **Compatibilidad:**
  - Firestore: No Compatible Nativamente.
  - BigQuery: **Compatible**.

#### 1.3.9. `valuegreaterthan`

Verifica si algún valor (tratado como número) es mayor que el valor proporcionado.

- **URL Ejemplo:**
  
  ```http
  GET https://myapi.com/api/data?filter[jsonData][valuegreaterthan]=100
  ```

- **Equivalente SQL (BigQuery):**
  
  ```sql
  SELECT * FROM data_table
  WHERE EXISTS (
      SELECT 1 FROM UNNEST(JSON_KEYS(jsonData)) AS key
      WHERE SAFE_CAST(JSON_EXTRACT_SCALAR(jsonData, CONCAT('$.', key)) AS FLOAT64) > 100
  );
  ```

- **Compatibilidad:**
  - Firestore: No Compatible Nativamente.
  - BigQuery: **Compatible**.

#### 1.3.10. `valuelesserthan`

Verifica si algún valor (tratado como número) es menor que el valor proporcionado.

- **URL Ejemplo:**
  
  ```http
  GET https://myapi.com/api/data?filter[jsonData][valuelesserthan]=50
  ```

- **Equivalente SQL (BigQuery):**
  
  ```sql
  SELECT * FROM data_table
  WHERE EXISTS (
      SELECT 1 FROM UNNEST(JSON_KEYS(jsonData)) AS key
      WHERE SAFE_CAST(JSON_EXTRACT_SCALAR(jsonData, CONCAT('$.', key)) AS FLOAT64) < 50
  );
  ```

- **Compatibilidad:**
  - Firestore: No Compatible Nativamente.
  - BigQuery: **Compatible**.

#### 1.3.11. `valuecontains`

Verifica si algún valor (tratado como string) contiene una subcadena. (Similar a `valuematches` con `LIKE '%...%'`).

- **URL Ejemplo:**
  
  ```http
  GET https://myapi.com/api/data?filter[jsonData][valuecontains]=substring
  ```

- **Equivalente SQL (BigQuery):**
  
  ```sql
  SELECT * FROM data_table
  WHERE EXISTS (
      SELECT 1 FROM UNNEST(JSON_KEYS(jsonData)) AS key
      WHERE SAFE_CAST(JSON_EXTRACT_SCALAR(jsonData, CONCAT('$.', key)) AS STRING) LIKE '%substring%'
  );
  ```

- **Compatibilidad:**
  - Firestore: No Compatible Nativamente.
  - BigQuery: **Compatible**.

#### 1.3.12. `isempty`

Verifica si el objeto JSON está vacío (no tiene claves).

- **URL Ejemplo:**
  
  ```http
  GET https://myapi.com/api/data?filter[jsonData][isempty]=true
  ```

- **Equivalente SQL (BigQuery):**
  
  ```sql
  SELECT * FROM data_table WHERE JSON_LENGTH(jsonData) = 0;
  ```

- **Compatibilidad:**
  - Firestore: No Compatible Nativamente.
  - BigQuery: **Compatible**.

#### 1.3.13. `isnotempty`

Verifica si el objeto JSON no está vacío (tiene al menos una clave).

- **URL Ejemplo:**
  
  ```http
  GET https://myapi.com/api/data?filter[jsonData][isnotempty]=true
  ```

- **Equivalente SQL (BigQuery):**
  
  ```sql
  SELECT * FROM data_table WHERE JSON_LENGTH(jsonData) > 0;
  ```

- **Compatibilidad:**
  - Firestore: No Compatible Nativamente.
  - BigQuery: **Compatible**.

#### 1.3.14. `sizeequals`

Verifica si el objeto JSON tiene exactamente un número específico de claves.

- **URL Ejemplo:**
  
  ```http
  GET https://myapi.com/api/data?filter[jsonData][sizeequals]=3
  ```

- **Equivalente SQL (BigQuery):**
  
  ```sql
  SELECT * FROM data_table WHERE JSON_LENGTH(jsonData) = 3;
  ```

- **Compatibilidad:**
  - Firestore: No Compatible Nativamente.
  - BigQuery: **Compatible**.

#### 1.3.15. `sizegreaterthan`

Verifica si el objeto JSON tiene más de un número específico de claves.

- **URL Ejemplo:**
  
  ```http
  GET https://myapi.com/api/data?filter[jsonData][sizegreaterthan]=2
  ```

- **Equivalente SQL (BigQuery):**
  
  ```sql
  SELECT * FROM data_table WHERE JSON_LENGTH(jsonData) > 2;
  ```

- **Compatibilidad:**
  - Firestore: No Compatible Nativamente.
  - BigQuery: **Compatible**.

#### 1.3.16. `sizelesserthan`

Verifica si el objeto JSON tiene menos de un número específico de claves.

- **URL Ejemplo:**
  
  ```http
  GET https://myapi.com/api/data?filter[jsonData][sizelesserthan]=5
  ```

- **Equivalente SQL (BigQuery):**
  
  ```sql
  SELECT * FROM data_table WHERE JSON_LENGTH(jsonData) < 5;
  ```

- **Compatibilidad:**
  - Firestore: No Compatible Nativamente.
  - BigQuery: **Compatible**.

#### 1.3.17. `keyvalueequals`

Verifica si una clave específica dentro del JSON tiene un valor exacto. El valor del filtro debe ser `key:value`.

- **URL Ejemplo:**
  
  ```http
  GET https://myapi.com/api/data?filter[jsonData][keyvalueequals]=enabled:true
  ```

- **Equivalente SQL (BigQuery):**
  
  ```sql
  -- Asumiendo que el valor del filtro es "enabled:true"
  SELECT * FROM data_table
  WHERE JSON_EXTRACT_SCALAR(jsonData['enabled']) = 'true';
  ```

- **Compatibilidad:**
  - Firestore: No Compatible Nativamente.
  - BigQuery: **Compatible**.

#### 1.3.18. `keyvaluecontains`

Verifica si el valor (string) de una clave específica contiene una subcadena. El valor del filtro debe ser `key:substring`.

- **URL Ejemplo:**
  
  ```http
  GET https://myapi.com/api/data?filter[jsonData][keyvaluecontains]=name:min
  ```

- **Equivalente SQL (BigQuery):**
  
  ```sql
  -- Asumiendo que el valor del filtro es "name:min"
  SELECT * FROM data_table
  WHERE JSON_EXTRACT_SCALAR(jsonData['name']) LIKE '%min%';
  ```

- **Compatibilidad:**
  - Firestore: No Compatible Nativamente.
  - BigQuery: **Compatible**.

#### 1.3.19. `arrayvaluecontains`

Verifica si un valor específico existe dentro de un campo que es un array JSON.

- **URL Ejemplo:**
  
  ```http
  GET https://myapi.com/api/items?filter[jsonField][arrayvaluecontains]=15
  ```

  ```http
  GET https://myapi.com/api/items?filter[jsonField][arrayvaluecontains]=important
  ```

- **Equivalente SQL (BigQuery):**
  
  ```sql
  if (value instanceof Number) {
    EXISTS (
        SELECT 1 FROM UNNEST(JSON_KEYS(jsonField)) as key
        WHERE 15 IN UNNEST(
            ARRAY(
                SELECT CAST(JSON_EXTRACT_SCALAR(jsonField[key]) AS FLOAT64)
                FROM UNNEST(JSON_EXTRACT_ARRAY(jsonField[key])) AS value
            )
        )
    )
  } else { -- value is String or other
    EXISTS (
        SELECT 1 FROM UNNEST(JSON_KEYS(jsonField)) as key
        WHERE EXISTS (
            SELECT 1
            FROM UNNEST(
                ARRAY(
                    SELECT JSON_EXTRACT_SCALAR(value)
                    FROM UNNEST(JSON_EXTRACT_ARRAY(jsonField[key])) AS value
                )
            ) AS element
            WHERE element LIKE '%important%'
        )
    )
  }
  ```

- **Compatibilidad:**
  - Firestore: No compatible.
  - BigQuery: **Compatible**.

### 1.4. Filtrado Anidado (Operador `nested`)

Permite aplicar operadores de filtrado a campos dentro de una estructura JSON anidada, utilizando una ruta de puntos para especificar la ubicación del campo anidado.

- **Sintaxis:** `filter[jsonField][nested][operator][nested.path.key]=value`
- `jsonField`: El nombre del campo en la base de datos que contiene el JSON.
- `nested`: Palabra clave literal que indica un filtro anidado.
- `operator`: Uno de los operadores de filtrado (ej: `eq`, `gt`, `contains`, `startswith`, etc.).
- `nested.path.key`: La ruta separada por puntos hacia el campo dentro del JSON (ej: `user.address.city`).
- `value`: El valor a comparar.

- **URL Ejemplo:** (Buscar datos donde el campo `jsonData`, en la ruta `details.address.city`, contenga 'London')
  
  ```http
  GET https://myapi.com/api/data?filter[jsonData][nested][contains][details.address.city]=London
  ```

- **URL Ejemplo:** (Buscar datos donde `jsonData.user.id` sea igual a '123')
  
  ```http
  GET https://myapi.com/api/data?filter[jsonData][nested][eq][user.id]=123
  ```

- **URL Ejemplo:** (Buscar datos donde `jsonData.metrics.value` sea mayor que 50)
  
  ```http
  GET https://myapi.com/api/data?filter[jsonData][nested][gt][metrics.value]=50
  ```

- **Equivalente SQL (BigQuery):** (El SQL exacto depende del operador y tipo de valor)
  
  ```sql
  -- Para contains:
  SELECT * FROM data_table
  WHERE JSON_EXTRACT_SCALAR(jsonData, '$.details.address.city') LIKE '%London%';

  -- Para eq (asumiendo valor string):
  SELECT * FROM data_table
  WHERE JSON_EXTRACT_SCALAR(jsonData, '$.user.id') = '123';

  -- Para gt (asumiendo valor numérico):
  SELECT * FROM data_table
  WHERE SAFE_CAST(JSON_EXTRACT_SCALAR(jsonData, '$.metrics.value') AS FLOAT64) > 50;
  ```

- **Compatibilidad:**
  - Firestore: No Compatible Nativamente (requiere denormalización o indexación externa).
  - BigQuery: **Compatible** (usando funciones `JSON_EXTRACT_SCALAR` o `JSON_QUERY` con la ruta JSONPath).

## 2. Ordenación (Parámetro `sort`)

Controla el orden de los resultados.

- **Formato:** `sort=[+/-]campo1, [+/-]campo2,...`
  - Usa `-` antes del nombre del campo para orden descendente (ej: `-age`).
  - Usa `+` antes del nombre del campo para orden ascendente (ej: `+name`).
  - Si no se especifica `+` o `-`, se asume orden ascendente por defecto (ej: `name` es igual a `+name`).
- Múltiples campos de ordenación se separan por comas.

- **URL Ejemplo (Ordenar por edad descendente, luego nombre ascendente):**

  ```http
  GET https://myapi.com/api/users?sort=-age,+name
  ```

- **URL Ejemplo (Ordenar por nombre ascendente - prefijo `+` opcional):**

  ```http
  GET https://myapi.com/api/users?sort=name
  ```

  o

  ```http
  GET https://myapi.com/api/users?sort=+name
  ```

- **Equivalente SQL:**

  ```sql
  SELECT * FROM users ORDER BY age DESC, name ASC;
  -- o
  SELECT * FROM users ORDER BY name ASC;
  ```

- **Compatibilidad:**
  - Firestore: **Compatible**, pero puede requerir la creación de índices compuestos específicos, especialmente si se combina con filtros de rango en campos diferentes.
  - BigQuery: **Compatible**.

## 3. Paginación (Parámetros `limit` y `offset`)

Controla cuántos resultados se devuelven y desde qué punto empezar.

- `limit`: Número máximo de registros a devolver.
- `offset`: Número de registros iniciales a saltar.

- **URL Ejemplo (Obtener 10 usuarios, saltando los primeros 20 - página 3):**

  ```http
  GET https://myapi.com/api/users?limit=10&offset=20
  ```

- **Equivalente SQL:**

  ```sql
  -- Sintaxis varía entre SQLs, ejemplos comunes:
  -- MySQL/PostgreSQL:
  SELECT * FROM users LIMIT 10 OFFSET 20;
  -- SQL Server:
  -- SELECT * FROM users ORDER BY some_column OFFSET 20 ROWS FETCH NEXT 10 ROWS ONLY;
  -- Oracle:
  -- SELECT * FROM ( SELECT /*+ FIRST_ROWS(10) */ t.*, ROWNUM rnum FROM ( SELECT * FROM users ORDER BY some_column ) t WHERE ROWNUM <= 30 ) WHERE rnum > 20;
  ```

- **Compatibilidad:**
  - Firestore: **`limit` es Compatible.** **`offset` es No Compatible Nativamente** de forma eficiente. Firestore usa paginación basada en cursores (`startAfter`/`endBefore`) que es más eficiente pero diferente a `offset`. Se puede _simular_ `offset` recuperando `offset + limit` documentos y descartando los primeros `offset` en el cliente/servidor, pero es ineficiente y costoso.
  - BigQuery: **Compatible**.

## 4. Ejemplos de Combinaciones

### Ejemplo 1: Usuarios activos, ordenados por email ascendente, segunda página de 5 resultados

- **URL:**

  ```http
  GET https://myapi.com/api/users?filter[status][eq]=active&sort=email&limit=5&offset=5
  ```

  o

  ```http
  GET https://myapi.com/api/users?filter[status][eq]=active&sort=+email&limit=5&offset=5
  ```

- **Equivalente SQL (Conceptual):**

  ```sql
  SELECT * FROM users
  WHERE status = 'active'
  ORDER BY email ASC
  LIMIT 5 OFFSET 5;
  ```

- **Compatibilidad:** Funciona bien en BigQuery. En Firestore, el `offset` sería problemático/ineficiente.

### Ejemplo 2: Productos con precio entre 50 y 100 O en la categoría 'ofertas', ordenados por precio descendente

- **URL:**

  ```http
  GET https://myapi.com/api/products?filter[or][0][and][0][price][gte]=50&filter[or][0][and][1][price][lte]=100&filter[or][1][category][eq]=ofertas&sort=-price
  ```

- **Equivalente SQL:**

  ```sql
  SELECT * FROM products
  WHERE (price >= 50 AND price <= 100) OR category = 'ofertas'
  ORDER BY price DESC;
  ```

- **Compatibilidad:** Funciona bien en BigQuery. En Firestore, la cláusula `OR` compleja sería difícil/imposible de implementar directamente.

### Ejemplo 3: Documentos que no contienen 'borrador', creados después del 2024-01-01, límite 20 (orden por defecto)

- **URL:** (Sin `sort` explícito)

  ```http
  GET https://myapi.com/api/documents?filter[not][0][title][contains]=borrador&filter[createdAt][gt]=2024-01-01T00:00:00Z&limit=20
  ```

- **Equivalente SQL:**

  ```sql
  SELECT * FROM documents
  WHERE NOT (title LIKE '%borrador%') -- o CONTAINS_SUBSTR en BQ
    AND createdAt > '2024-01-01T00:00:00Z'
  -- ORDER BY ??? (depende del default de la DB o del ORM)
  LIMIT 20;
  ```

- **Compatibilidad:** Funciona bien en BigQuery. En Firestore, el `NOT contains` no es soportado nativamente.

## 5. Notas Generales de Compatibilidad

- **BigQuery:** Ofrece una alta compatibilidad con la mayoría de las operaciones de filtrado (incluyendo `LIKE`, `CONTAINS_SUBSTR`, `STARTS_WITH`, `ENDS_WITH`, `AND`, `OR`, `NOT`), ordenación y paginación (`LIMIT`/`OFFSET`) definidas aquí.
- **Firestore:**
  - **Fortalezas:** Igualdad (`eq`), rangos (`gt`, `gte`, `lt`, `lte`) sobre campos individuales, `AND` implícito (requiere índices), `IN` (para `OR` sobre el mismo campo), `limit`, ordenación básica (puede requerir índices).
  - **Debilidades/Incompatibilidades:** Búsqueda de subcadenas (`contains`, `like`, `endswith`), `startswith` (limitado), `OR` complejo entre diferentes campos/operadores, `NOT` complejo, paginación con `offset`. Estas operaciones a menudo requieren soluciones alternativas como indexación externa (Algolia, Typesense), denormalización de datos o realizar múltiples consultas y combinarlas en la lógica de la aplicación. La paginación debe hacerse preferiblemente con cursores.

Se recomienda diseñar las consultas teniendo en cuenta las capacidades y limitaciones de la base de datos subyacente (Firestore o BigQuery) que se esté utilizando.
