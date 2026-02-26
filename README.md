# Entorno de desarrollo

## Procesadores de Lenguajes

- Este repositorio contiene todo el trabajo desarrollado en la **práctica 04** de la asignatura **Procesadores de Lenguajes** del grado de Ingeniería Informática que se cursa en la Universidad de La Laguna. 
- A su vez, este informe documenta el trabajo realizado para la práctica.
- Autor - _Cristhian Cruz Delgado_
- Fecha - _19 Feb 2026_

### 1. Configurar proyecto

El desarrollo de la práctica ha sido trabajado en la máquina virtual **PL2526-30**.

Se recomienda trabajar desde tu máquina local conectándote de forma remota a la máquina virtual mediante entornos de desarrollo como VSCode.

Una vez en el entorno de trabajo, compila y prueba que los test funcionen correctamente.

Para compilar y probar test ejecute:

- $ npx jison src/grammar.jison -o src/parser.js
- $ npm test

```
drwxrwxr-x 217 usuario usuario  12288 feb 25 09:58 node_modules
-rw-rw-r--   1 usuario usuario   1258 feb 25 09:55 package.json
-rw-rw-r--   1 usuario usuario 166986 feb 25 09:58 package-lock.json
-rw-rw-r--   1 usuario usuario    971 feb 25 09:55 README.md
drwxrwxr-x   2 usuario usuario   4096 feb 25 09:59 src
  -rw-rw-r-- 1 usuario usuario   899 feb 25 09:55 grammar.jison
  -rwxrwxr-x 1 usuario usuario   282 feb 25 09:55 index.js
  -rw-rw-r-- 1 usuario usuario 21374 feb 25 09:59 parser.js
drwxrwxr-x   2 usuario usuario   4096 feb 25 09:55 __tests__
  -rw-rw-r-- 1 usuario usuario 4275 feb 25 09:55 parser.test.js
```

### 2. Análisis de una gramática independiente del contexto en un fichero `.jison`

Sea un fichero `grammar.jison` donde se define:

```
%lex
%%
\s+ { /* skip whitespace */; }
[0-9]+ { return 'NUMBER'; }
"**" { return 'OP'; }
[-+*/] { return 'OP'; }
<<EOF>> { return 'EOF'; }
. { return 'INVALID'; }
/lex
```

`\s+` ignora los espacios en blanco sin generar un token, mientras que devolver un token crea un elemento significativo para el parser.

Los tokens generados por _123**45+@_, será NUMBER: 123, OP: **, NUMBER: 45, OP: +, INVALID: @ y EOF.

`**` debe aparecer antes porque si [-+*/] estuviera primero, al encontrar ** coincidiría con el primer * como un token OP y luego el segundo * como otro OP, en lugar de reconocer ** como un solo token.

`EOF` se devuelve cuando se alcanza el final del archivo de entrada <<<EOF>EOF>>, indicando que no hay más caracteres que procesar.

La regla `.` captura cualquier carácter no reconocido por reglas anteriores y devuelve INVALID, permitiendo detectar errores léxicos en la entrada.

### 3. Añadir nuevas cadenas que ha de reconocer el lenguaje que representa la gramática independiente del contexto

La expresión regular que representa las nuevas cadenas que ha de reconocer la nueva gramática es la siguiente:

> \s+|\/\/.*

Estas son cadenas que especifican comentarios de una única línea.

### 4. Modificación de una expresión regular que ya reconoce el lenguaje que representa la gramática independiente del contexto

La expresión regular original que reconoce número enteros es la siguiente:

> [0-9]+

La nueva expresión regular que reconoce números enteros y decimales es la siguiente:

> [0-9]+(\.[0-9]+)?([eE][+-]?[0-9]+)?

### 5. Añadir pruebas para el proyecto

Pruebas añadidas:

```JavaScript
describe('Single-line comments', () => {
  test('should ignore comment after expression', () => {
    expect(parse("3 + 5 // comment")).toBe(8);
  });

  test('should ignore full line comment', () => {
    expect(parse("7 // only comment")).toBe(7);
  });

  test('should ignore comment with symbols', () => {
    expect(parse("10 - 3 // + * / **")).toBe(7);
  });
});

describe('Floating point numbers', () => {
  test('should parse simple decimal numbers', () => {
    expect(parse("3.5")).toBe(3.5);
    expect(parse("0.1")).toBe(0.1);
    expect(parse("10.0")).toBe(10.0);
  });

  test('should handle decimal addition', () => {
    expect(parse("1.5 + 2.5")).toBe(4);
  });

  test('should handle decimal subtraction', () => {
    expect(parse("5.5 - 2.2")).toBeCloseTo(3.3);
  });

  test('should handle decimal multiplication', () => {
    expect(parse("2.5 * 4")).toBe(10);
  });

  test('should handle decimal division', () => {
    expect(parse("7.5 / 2.5")).toBe(3);
  });

  test('should handle mixed integer and decimal operations', () => {
    expect(parse("3 + 2.5")).toBe(5.5);
    expect(parse("10 - 0.5")).toBe(9.5);
  });

  test('should handle small decimal values', () => {
    expect(parse("0.0001 + 0.0002")).toBeCloseTo(0.0003);
  });

  test('should handle scientific notation', () => {
    expect(parse("1e+3")).toBe(1000);
    expect(parse("2e+2 + 1")).toBe(201);
  });

  test('should handle negative exponent scientific notation', () => {
    expect(parse("1e-3")).toBeCloseTo(0.001);
  });
});
```

# Conclusiones del informe

En este informe se han cumplido con esta lista de objetivos

1. Configurar proyecto
2. Análisis de una gramática independiente del contexto en un fichero `.jison`
3. Añadir nuevas cadenas que ha de reconocer el lenguaje
4. Modificación de una expresión regular que ya reconoce el lenguaje
5. Añadir pruebas para el proyecto
