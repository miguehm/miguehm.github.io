---
title: Rust Cheat Sheet
author: Miguel Hernández Moreno
pubDatetime: 2024-12-21T15:54:38Z
slug: rust-journey
featured: false
draft: false
tags:
  - dev
  - rust
description: Apuntes sobre mis aprendizajes de Rust
---

# Tipos de Dato

## Tipos Escalares

- **`i8`**: Entero con signo de 8 bits.
- **`i16`**: Entero con signo de 16 bits.
- **`i32`**: Entero con signo de 32 bits.
- **`i64`**: Entero con signo de 64 bits.
- **`i128`**: Entero con signo de 128 bits.
- **`isize`**: Entero con signo cuyo tamaño depende de la arquitectura del sistema (32 o 64 bits).

- **`u8`**: Entero sin signo de 8 bits.
- **`u16`**: Entero sin signo de 16 bits.
- **`u32`**: Entero sin signo de 32 bits.
- **`u64`**: Entero sin signo de 64 bits.
- **`u128`**: Entero sin signo de 128 bits.
- **`usize`**: Entero sin signo cuyo tamaño depende de la arquitectura del sistema (32 o 64 bits).

- **`f32`**: Número de punto flotante de 32 bits.
- **`f64`**: Número de punto flotante de 64 bits (por defecto en Rust).

- **`char`**: Representa un solo carácter Unicode. Se almacena como un valor de 4 bytes.

- **`bool`**: Representa un valor booleano, que puede ser `true` o `false`.

## Tipos Compuestos

- Tuplas: Colecciones de valores de diferentes tipos. Ejemplo: `(i32, f64, char)`.

  ```rust
  let tup: (i32, f64, char) = (500, 6.4, 'a');
  ```

- Arrays: Colecciones de valores del mismo tipo con un tamaño fijo. Ejemplo: `[i32; 5]`, que representa un array de 5 enteros.

  ```rust
  let arr: [i32; 5] = [1, 2, 3, 4, 5];
  ```

## Tipos Dinámicos

- `String`: Una cadena de texto de tamaño dinámico, construida sobre un vector de bytes. Se puede modificar.

  ```rust
  let mut s = String::from("Hola");
  ```

- `Vec<T>`: Un vector, que es una colección de tamaño dinámico. Los elementos pueden ser de cualquier tipo `T`.

  ```rust
  let v: Vec<i32> = vec![1, 2, 3];
  ```

- `Box<T>`: Boxed value, permite almacenar un valor en el heap, facilitando la gestión de datos de tamaño variable o recursivo.

  ```rust
  let b = Box::new(5);
  ```

- `Rc<T>`: Un contador de referencias que permite múltiples propietarios de un valor. Útil para compartir datos sin transferir ownership.

  ```rust
  use std::rc::Rc;
  let a = Rc::new(5);
  ```

- `Arc<T>`: Similar a `Rc<T>`, pero atómico, lo que permite el uso seguro en entornos concurrentes.

  ```rust
  use std::sync::Arc;
  let b = Arc::new(5);
  ```

## Otros Tipos

- Referencias: Se utilizan para tomar prestado un valor sin tomar ownership. Hay dos tipos:

  - `&T`: Referencia inmutable.
  - `&mut T`: Referencia mutable.

  ```rust
  let x = 5;
  let r = &x; // referencia inmutable
  let mut y = 10;
  let r_mut = &mut y; // referencia mutable
  ```

- Funciones: Pueden considerarse tipos de datos, ya que las funciones también son tratadas como valores en Rust.

  ```rust
  fn add(x: i32, y: i32) -> i32 {
      x + y
  }
  ```

## Tipo de Dato Especiales

### Unit Type

No almacenan ningun dato, indican la ausencia de un valor significativo `()`.

- Literal, se expresa como `()`.
- Tamaño de `0`, no ocupa espacio en memoria.
- Equivalente a `void`, una función en rust sin valor de retorno devuelve un `()` implicitamente.

```rust
fn main(){
  assert_eq!(do_nothing(), ());
}

fn do_nothing() {
  println!("I will return a ()");
}
```

### Never Type `!`

Tipo especial, indica que una función o expresión nunca regresa.

- La función no tiene un valor de retorno válido.
- Las funciones terminan ejecutandose indefinidamente o detiene la ejecución (como con un `panic!`). Útil cuando para que un error inesperado no se propague en flujos mas amplios.

```rust
fn infinite_loop() -> ! {
  loop {
    println!("This loop never ends");
  }
}
```

> Loop infinito, no detiene la ejecución.

```rust
fn divergent_function() -> ! {
  panic!("This function never return!");
}
```

> Se llama la macro `panic` y el programa cierra completamente (aunque se puede establecer que unicamente se cierre el hilo).

Estas sentencias también invocan un `panic`.

```rust
fn never_return() -> ! {
  unimplemented!();
}

fn never_return() -> ! {
  todo!();
}
```

> Bloques sin implementar

### Option

Enum para representar un valor que puede o no estar presente.

```rust
enum Option<T> {
    Some(T),  // Variante que indica que hay un valor presente
    None,     // No contiene ningún valor
}
```

- Seguridad de gestión de valores, obliga a manejar el caso cuando un valor no esté presente.
- Integrado con patrones de coincidencia, permite el manejo claro y conciso de estados (`match`).
- Metodos comunes:
  - `is_some()`, true si es `Some`.
  - `is_none()`, true si es `None`.
  - `unwrap()`, Extrae el valor de `Some`, si es `None` provoca un `panic` en tiempo de ejecución.
  - `map()`, Aplica una función a `Some`, si existe.
  - `and_then()`, Encadena oparaciones que devuelven otras opciones.

```rust
fn main() {
    let maybe_value: Option<u8> = Some(5);
    assert_eq!(maybe_value.is_some(), true);

    let maybe_value: Option<u8> = None;
    assert_eq!(maybe_value.is_none(), true);

    let maybe_value: Option<u8> = Some(6);
    assert_eq!(maybe_value.unwrap(), 6);

    let maybe_value: Option<u8> = Some(2);
    assert_eq!(maybe_value.map(|v| v * 2), Some(4));
}
```

# Conceptos de Rust

## Binding

Para usar una variable, debe estar **ligada** a un valor (inicializada).

```rust
let x: i32 = 5;
let y: i32; // declaración sin utilizar solo mostrara un warning
```

> Las declaraciones sin utilizar suelen llevar `_` al principio. En este caso sería `_y`.

## Mutability

Todas las variables son inmutables por defecto.

```rust
let mut x: i32 = 5;
x = 8;
```

## Scope

Región o area del programa donde las variables y bindings tiene validez. Definen la duración (lifetime) de los los valores y bindings.

- Inicio y fin claros, se delimitan con `{}`.
- Bindings locales, variables dentro de un scope son válidas únicamente dentro de ese scope.
- Cuando un scope termina, rust libera automaticamente todos los recursos asociados a las variables (RAII).

```rust
fn main(){
  let x: i32 = 5;

  {
    let y: i32 = 8;
    println!('The value of x is {} and y is {}', x, y);
  }

  // ERROR: en este scope `y` ya no existe.
  println!('The value of x is {} and y is {}', x, y);
}
```

## Shadowing

Permite declarar una nueva variable con el mismo nombre de una ya existente en el mismo scope o en un scope anidado. El sombreado no modifica la original, crea un nuevo binding con el mismo nombre.

- Nuevo binding, al sombrear una variable se crea un nuevo binding con ese nombre.
- Tipo y mutabilidad, permite cambiar el tipo y mutabilidad de una variable sin errores.
- Inmutable por defecto, mantiene la inmutabilidad en transformaciones intermedias.

```rust
fn main(){
  let x = 5; // x = 5

  let x = x + 1; // x = 6

  {
    let x = x * 2; // x = 12
  }

  let x = x + 1; // x = 7
}
```

> El shadowing funciona cuando una variable sin binding no tiene un tipo explicito.

```rust
//let x; // debe tener declarado un tipo explicito como u8
let x: u8;

let x = 5;
```

## Destructuring Assignments

Extrae multiples valores de una estructura (tuplas, arrays, structs, enums), y asignarlos a otras variables. Utiliza patrones para desestructurar y acceder directamente a sus partes.

> Por defecto, rust aplica un `move` a los valores.

- Nombres de variables

```rust
let tuple = (1, 2, 3);
let (a, b, c) = tuple;
```

- Comodines

```rust
let array = [1, 2, 3, 4];
let [first, second, _, last] = array // ignora el tercer elemento
```

- Operador `..`

```rust
let array = [1, 2, 3, 4, 5];
let [.., last] = array; // Ignora todos menos el último
let [first, ..] = array; // Toma el primero e ignora el ultimo
let [first, second, .., last] = array; // Ignora únicamente el tercero y cuarto
```

### Uso de `ref`

Es una palabra clase, se usa unicamente en la desestructuración de patrones para crear una referencia en lugar de mover el valor, transforma el valor desestructurado en una referencia.

```rust
let tuple = (String::from("Hello"), 5);

let (ref text, num) = tuple;

println!("{} {}", text, num);

println!("{} {}", tuple.0, tuple.1);
```

> `tuple(0)` no fue movido en la desestructuración.
> El uso de `&` respecto a `ref` en patrones tiene un proposito de coincidencia, no de creación.
> `ref`: palabra clave, a la izquierda de `=`, usado en patrones.
> `&`: operador, a la derecha de `=`, creación de referencias para punteros.

```rust
let letter = 'a';
let r1 = &a;
let ref r2 = a; // actua como patron

assert_eq!(get_addr(r1), get_addr(r2));
```

## Statements and Expressions

Una expresión tiene un valor que puede formar parte de una asignación.

```
let y = {
  let num: u8 = 5;
  let square: u8 = num * num;

  square * num
};
```

> El bloque devuelve el valor de la última expresión sin punto y coma (implicit return).

Una sentencia no devuelve un valor útil.

```rust
let z = {
  2 * 2;
};
```

> El bloque retorna un valor de unidad `()`.

# Gestión de Memoria

## Ownership

- Cada valor en rust tiene una unica variable como propietario, esta variable es responsable de la memoria del valor.
- Cuando el propietario sale del ámbito el valor se libera automáticamente.
- Se puede transferir la propiedad de un valor utilizando **move**.

### Move Semantics

Cuando se asigna un valor predefinido a otra variable, la propiedad del valor _se mueve_ a la nueva variable.

```rust
let s1 = String::from("hola");
let s2 = s1; // el valor se transfiere a `s2`

//println!("{}", s1); // Provocará un error, s1 ya no es valido
println!("{}", s2);
```

> `s1` deja de ser valido, previniendo el uso de accidental de memoria que ya fue liberada.

### References

Para compartir valores sin transferir la propiedad se utilizan las referencias, estas permiten acceder al valor sin tomar posesión de el `&`.

```rust
let s1 = String::from("hola");
let len = calcular_largo(&s1);

println!("El largo de {} es {}", s1, len);
```

### Mutability

Por defecto, las referencias son inmutables, a menos que se declaren como mutables.

```rust
let mut num = 6;
let r = &mut num;

*r = 7;

println!("{}", num);
```

> `*r` accede al valor, similar a los punteros en c.

```rust
let mut s = String::from("hola");
let r = &mut s; // r es una referencia mutable a s

r.push_str(", mundo."); // Usamos r para modificar s

println!("{}", s); // Imprime: hola, mundo.
```

- La referencia `r` no necesita ser mutable, ya que solo se utiliza para modificar el valor de `s`, no para cambiar a qué objeto apunta.
- El método `push_str` desreferencia `r` automáticamente.

### Tipos que obedecen a las reglas de ownership.

Tipos que manejan recursos como `String`, `Vec`, y tuplas o estructuras que contienen campos que no son `Copy`.

1. Tipos que Manejan Recursos:

   - `String`
   - `Vec<T>`
   - `Box<T>`
   - `Rc<T>` (contadores de referencia)
   - `Arc<T>` (contadores de referencia atómicos)

2. Estructuras Personalizadas:

   - Si una estructura o tipo personalizado contiene cualquier campo que no implemente `Copy`, entonces esa estructura tampoco implementará `Copy` a menos que implementes el trait `Copy` explícitamente.

3. Cualquier Tipo que Contenga un Puntero/Título:
   - Tipos que contienen punteros a recursos dinámicos, como `&mut T` (referencias mutables), no implementan `Copy`.

```rust
let s1 = String::from("Hola"); // `String` no implementa Copy
let s2 = s1; // Esto mueve el valor de s1 a s2, s1 ya no es válido

let v = vec![1, 2, 3]; // `Vec<i32>` no implementa Copy
let v2 = v; // Esto mueve el valor de v a v2, v ya no es válido
```

> Los tipos que no implementen `Copy` pueden usar `.clone()` para hacer una copia profunda y asignarla a otra variable sin necesidad de transferir la propiedad

```rust
let t1 = String::from("Hello, world!");
let t2 = t1.clone();

println!("t1: {}", t1);
println!("t2: {}", t2);
```

### Excepciones

Tipos primitivos, tuplas y arrays compuestos solo de tipos que son `Copy`, y referencias a tipos que implementan `Copy`. En esos casos el valor hace una copia al ser asignado en otra variable, por lo que su propiedad no se transfiere.

1. Tipos Primitivos:

   - `i8`, `i16`, `i32`, `i64`, `i128`, `isize` (enteros con signo)
   - `u8`, `u16`, `u32`, `u64`, `u128`, `usize` (enteros sin signo)
   - `f32`, `f64` (números de punto flotante)
   - `bool` (booleanos)
   - `char` (caracteres)

2. Tuplas y Arrays:

   - Las tuplas y arrays que consisten únicamente en tipos que implementan `Copy` también implementan `Copy`.
   - Ejemplo: `(i32, f64)`, `[i32; 5]` (arrays de enteros de 32 bits de longitud 5).

3. Referencias:
   - Las referencias a tipos que implementan `Copy` son también copiables. Ejemplo: `&i32`, `&str`, etc. Aunque estas referencias **no son copias del valor apuntado**, la referencia en sí se puede copiar.

```rust
let x: i32 = 5; // i32 implementa Copy
let y = x; // se copia el valor de x en y

let arr = [1, 2, 3]; // Array de enteros que implementa Copy
let arr_copy = arr; // se copia el array
```

### Partial Move

Asociado a estructuras que contienen múltiples campos o elementos como `struct` o `enum`. Si uno de esos campos no implementa `Copy` y es movido a otra variable, los demás campos pueden seguir existiendo y ser accesibles en la variable original.

```rust
struct MyStruct {
    text: String,
    num: i32
}

fn main() {
    let my_data = MyStruct {
        text: String::from("Hello, World!"),
        num: 42
    };

    let _text_moved = my_data.text;

    // println!("{}", my_data.text); // Error: value borrowed here after move
    println!("{}", my_data.num); // Works fine
}
```

#### Consideraciones

Cuando se mueve un campo mediante `self`, el compilador marca todo el `self` como movido, por lo tanto no se pueden acceder a los datos de la misma instancia. Rust no permite el movimiento parcial de una parte del campo si después se necesita el campo completo para otras operaciones.

```rust
trait Person {
    fn name(&self) -> String;
}

// Person is a supertrait of Student.
// Implementing Student requires you to also impl Person.
trait Student: Person {
    fn university(&self) -> String;
}

trait Programmer {
    fn fav_language(&self) -> String;
}

// CompSciStudent (computer science student) is a subtrait of both Programmer
// and Student. Implementing CompSciStudent requires you to impl both supertraits.
trait CompSciStudent: Programmer + Student {
    fn git_username(&self) -> String;
}

fn comp_sci_student_greeting(student: &dyn CompSciStudent) -> String {
    format!(
        "My name is {} and I attend {}. My favorite language is {}. My Git username is {}",
        student.name(),
        student.university(),
        student.fav_language(),
        student.git_username()
    )
}

struct CSStudent {
    name: String,
    university: String,
    fav_language: String,
    git_username: String,
}

// Es necesario `clone` para que las demás
// impl funcionen correctamente
impl Person for CSStudent {
    fn name(&self) -> String {
        self.name.clone()
    }
}

impl Student for CSStudent {
    fn university(&self) -> String {
        self.university.clone()
    }
}

impl Programmer for CSStudent {
    fn fav_language(&self) -> String {
        self.fav_language.clone()
    }
}

impl CompSciStudent for CSStudent {
    fn git_username(&self) -> String {
        self.git_username.clone()
    }
}

fn main() {
    let student = CSStudent {
        name: "Sunfei".to_string(),
        university: "XXX".to_string(),
        fav_language: "Rust".to_string(),
        git_username: "sunface".to_string(),
    };

    println!("{}", comp_sci_student_greeting(&student));
}
```

## Borrowing

Permite que partes del programa accedan a datos sin tomar la propiedad de esos datos, ayuda a prevenir errores como los accesos a memoria inválida o condiciones de carrera en entornos concurrentes, motivando a trabajar con referencias en lugar de mover valores además de garantizar la seguridad de la memoria.

1. Puedes tener múltiples referencias inmutables a un dato al mismo tiempo, prestamo inmutable.
2. Solo puedes tener una única referencia mutable activa. Prestamo mutable.
3. No puedes tener referencias inmutables y mutables al mismo tiempo.

### Préstamo Inmutable

Se pueden tener múltiples préstamos inmutables a un solo valor al mismo tiempo.

```rust
fn main() {
    let s = String::from("Hola");

    let r1 = &s; // Préstamo inmutable
    let r2 = &s; // Otro préstamo inmutable

    println!("{} y {}", r1, r2); // Ambos préstamos pueden ser usados
}
```

### Préstamo Mutable

Se puede tener un préstamo mutable, pero no puedes tener otra referencia o prestamo mutable o inmutable activo al mismo tiempo, asegura que no haya otros accesos al valor cuando se está modificando.

```rust
fn main() {
    let mut s = String::from("Hola");

    let r_mut = &mut s; // Préstamo mutable

    r_mut.push_str(", mundo."); // Modificamos el valor a través de la referencia mutable

    println!("{}", r_mut); // Imprimimos el valor modificado

    // println!("{}", s); // Esto causaría un error, ya que s no puede ser usado mientras r_mut esté en uso
}
```

> `r_mut` debe salir del scope o dejar de estar activo para volver a utilizar `s` o crear otras referencias mutables e inmutables.

### Tiempo de Vida

Determinan cuanto tiempo pueded durar un valor en memoria. Evita errores a referencias dangling (referencias a areas de memoria que han sido liberadas). Cada variable, referencia o dato tiene un tiempo de vida que comienza cuando es creado y termina cuando deja de ser accesible. Esto garantiza que:

- Las referencias no viven más que los datos a los que hacen referencia.
- No existan referencias dangling.

```rust
let mut s = String::from("Hola");
let r1 = &mut s;
r1.push_str(", mundo");
let r2 = &mut s;
r2.push_str(" y a todos!");

println!("{}", r2);
```

> Es un código válido ya que r1 deja de ser utilizado o accesible, por lo tanto el tiempo de vida de r1 terminó antes de que r2 se creara.

# Compound Types

## Array

Colección de elementos del mismo tipo, tamaño conocido en tiempo de compilación

```rust
let arr: [u8; 3] = [1, 2, 3]; // Tipo y tamaño definidos

let arr0 = [1, 2, 3]; // El compilador puede inferir el tamaño y el tipo
let arr1: [_, 3] = ['a', 'b', 'c'];

let arr2: [i8; 100] = [1; 100]; // inicializar varios valores a la vez

let arr3 = [1, 2];
let n1 = arr2[0]; //acceso directo a los datos
let n1 = arr2.get(3).unwrap() // acceso seguro
```

> `get()` devuelve un `Option<T>` que puede contener un `(Some)` o estar vacío `(None)`.
> Es una forma segura de gestionar posibles errores.
> `arr2.get(3).unwrap()` producirá un `panic` ya que `unwrap()` recibió un `None`.

```rust
let arr = [1, 2, 3];
let n1 = arr[0];

let n2 = arr.get(4).unwrap(); // acceso seguro
let n3 = arr[4]; // acceso inseguro
```

## Slice

Es una porción de un array o de una colección, un slice no tiene un tamaño fijo y puede trabajar con un array o vector en tiempo de ejecución.

- Referencia a una parte de una colección, borrowing.
- Es dinámico y su tipo se determina en tiempo de compilación.

```rust
let arr: [i8; 4] = [1, 2, 3, 4];
let my_slice: &[i8] = &arr[0..=2];
```

> Un slice reference o simplemente slice, no es un array, es un objeto two-word. La primera word contiene la dirección a la que apunta y la segunda el tamaño del slice.
> El tamaño de las word es `usize` y depende de la arquitectura del procesador (64 bits para x86_64).

Un `String` necesita que la primera letra sea un `&str` (Deref cohersion)

## Tuple

Colección de valores de diferentes tipos, tamaño fijo.

```rust
let tupla = (500, 6.4, "Hola");

println!("{}", tupla.0); //acceso a los datos

let (x, y, z) = tupla; // Desestructuración de la tupla
```

Tambien pueden ser usadas como argumento y retorno de una función.

```rust
fn main() {
    let result = operacion((2,3));
    println!("{:?}", result);
}

fn operacion(num: (u8, u8)) -> (u8, u8) {
    (num.0+num.1, num.0*num.1)
}
```

## Struct

Agrupa varios valores bajo un mismo nombre.

### Estructura de Tipo Concreto

```rust
struct Persona {
    nombre: String,
    edad: u32,
}

fn main() {
    let persona1 = Persona {
        nombre: String::from("Alice"),
        edad: 30,
    };
    let nombre = persona1.nombre;
    let edad = persona1.edad;
}
```

### Estructura de Tipo Tuple

Los valores no tienen semántica, se accede a los datos mediante índices.

```rust
#[derive(Debug)]
struct A(u8, f64, i8);

fn main(){
    let mut example: A = A(8, 1.1, -1);
    println!("{:?}", example);
}
```

### Estructura de Tipo Genérica

Puede contener cualquier tipo `T`.

```rust
#[derive(Debug)]
struct C<T>(T);

fn main(){
    let my_gen: C<char> = C('a');
    let my_gen = C(1.1);
    let my_gen = C("hola");

    println!("{:?}", my_gen);
}
```

Con semantica y dos tipos genericos `T` y `U`.

```rust
#[derive(Debug)]
struct Point<T, U>{
    x: T,
    y: U
}

fn main(){
    let p1: Point<u8, String> = Point {
        x: 8,
        y: "Hola".to_string()
    };

    println!("{:?}", p1);
}
```

### Métodos

```rust
struct Persona {
    name: String,
    age: u8
}

impl Persona {
    fn saludar(&self) {
        println!("Hola, mi nombre es {} y tengo {} años.", self.name, self.age);
    }
}

fn main(){
    let persona1 = Persona {
        name: String::from("Alice"),
        age: 30,
    };
    persona1.saludar();
    // implicitamente se realiza lo siguiente:
    // Persona::saludar(&persona1);
}
```

> `&self` es _azucar sintáctico_ de `self: &Self`.

```rust
impl Contador {
    // Método que toma una referencia inmutable al objeto
    fn aumentar(self: &Self) {...}

    // Método que consume el objeto
    fn obtener_contador(self: Self) {...}

    // Método que toma una referencia mutable al objeto
    fn incrementar(self: &mut Self) {...}
}
```

### Funciones asociadas

Similar al anterior (métodos), pero ligado a la estructura, no a las instancias creadas con la estructura.
Generalmente se utilizan como constructores.

```rust
struct MyNumber;

impl MyNumber {
    fn create(value: i32) -> MyNumber {
        // Aquí simplemente retornamos una nueva instancia de MyNumber
        MyNumber
    }
}

fn main(){
    // Usamos el operador :: para acceder a un método asociado
    let number = MyNumber::create(42);
}
```

> Análogo a un objeto en POO pero sin características como herencia.

### Diferencias entre Métodos y Funciones Asociadas

```rust
struct Persona {
    name: String,
    age: u8
}

impl Persona {
    // método
    fn saludar(&self) { // Uso de `self`, referencia a la instancia.
        println!("Hola, mi nombre es {} y tengo {} años.", self.name, self.age);
    }

    // función asociada
    fn create_dummy(){ // Asociada a la estructura.
        Persona{name: "Bot".to_string(), age: 99}
    }
}
```

### Alias `Self`

`Self` es un alias que se refiere a la estructura que se está trabajando dentro del bloque `impl`. En el ejemplo anterior:

```rust
struct MyNumber;

impl MyNumber {
    fn create(value: i32) -> Self {
        // Aquí simplemente retornamos una nueva instancia de MyNumber
        Self
    }
}

fn main(){
    // Usamos el operador :: para acceder a un método asociado
    let number = MyNumber::create(42);
}
```

### Multiples bloques `impl`

```rust
struct MyNumber;

impl MyNumber {
    fn create(value: i32) -> Self {
        // Aquí simplemente retornamos una nueva instancia de MyNumber
        Self
    }
}

impl MyNumber {
    fn show(&self) {
        println!("I am existing")
    }
}

fn main(){
    // Usamos el operador :: para acceder a un método asociado
    let number = MyNumber::create(42);
}
```

> La principal ventaja de usar el alias `Self` es, si en el futuro se cambia el nombre de la estructura, las implementaciones seguirán funcionanda.

### Implementaciones Genéricas `impl`

```rust
#[derive(Debug)]
struct MyData<T>{
    value: T
}

impl<T> MyData<T> {
    fn show(&self) -> &T {
        &self.value
    }
}

fn main(){
    let d1 = MyData{value: 8_u8};
    let d2 = MyData{value: "hello".to_string()};

    println!("{}", d1.show());
    println!("{}", d2.show());
}
```

> `impl<T>` indica que se trabaja con un tipo genérico en la implementación, `MyData<T>` indica el tipo genérico `T` a almacenar en la estructura.

### Estructuras anidadas

```rust
struct Direccion {
    ciudad: String,
    pais: String,
}

struct Persona {
    nombre: String,
    edad: u32,
    direccion: Direccion,
}
```

### Field Init Shorthand Syntax

Para reducir repeticiones, al pasar unicamente el nombre de las variables, rust infiere que el parametro tiene el mismo nombre.

```rust
struct Persona {
    nombre: String,
    edad: u8
}

fn main(){
    let nombre = String::from("Marco");
    let edad = 22;

    let p2 = Persona {
        nombre,
        edad
    }
}
```

### Struct Update Syntax

Permite inicializar un struct utilizando los valores de otro struct, modificando solo algunos de sus campos.

```rust
struct User {
    username: String,
    email: String,
}

fn main(){
    let user1 = User {
        username: String::from("alice"),
        email: String::from("alice@example.com"),
    };

    let user2 = User {
        username: String::from("marco"),
        ..user1
    };
}
```

> Considerar que los valores pasados de `user1` a `user2` han sido movidos.

## Enum

Un tipo de una o varias variantes posibles. Cada variante puede contener datos adicionales.

- Seguridad de tipo, solo se puede usar la variante definida por el enum.

```rust
enum Message {
    Quit,
    Move { x: i32, y: i32 },
    Write(String),
    Code(u8),
}

fn process_message(msg: Message) {
    match msg {
        Message::Quit => println!("Quitting!"),
        Message::Move { x, y } => println!("Moving to x: {}, y: {}", x, y),
        Message::Write(text) => println!("Writing message: {}", text),
        Message::Code(i) => println!("Message code {}", i),
    }
}

fn main() {
    let msg1 = Message::Quit;
    let msg2 = Message::Move { x: 10, y: 20 };
    let msg3 = Message::Write(String::from("Hello, world!"));
    let msg4 = Message::Code(4);

    process_message(msg1);
    process_message(msg2);
    process_message(msg3);
    process_message(msg4);
}
```

### Bloques `impl` en `enum`

```rust
enum Checking {
    foo,
    bar
}

impl Checking {
    fn show(&self) {
        println!("I am a enum");
    }
}

fn main(){
    let v1 = Checking::foo;
    let v2 = Checking::bar;

    v1.show();
}
```

### Discriminantes

Las variantes tienen asociadas un valor que las identifica (compatibilidad con los enums de C).

```````rust
enum Message {
    Info,
    Error = 1,
    Warning = 5,
    Comment, // 6
}

fn main(){
    println!("{}", Message::Warning as u8); // 5
}
``````rust
enum Message {
    Quit,
    Move { x: i32, y: i32 },
    Write(String),
}

fn process_message(msg: Message) {
    match msg {
        Message::Quit => println!("Quitting!"),
        Message::Move { x, y } => println!("Moving to x: {}, y: {}", x, y),
        Message::Write(text) => println!("Writing message: {}", text),
    }
}

fn main() {
    let msg1 = Message::Quit;
    let msg2 = Message::Move { x: 10, y: 20 };
    let msg3 = Message::Write(String::from("Hello, world!"));

    process_message(msg1);
    process_message(msg2);
    process_message(msg3);
}
```````

### Consideraciones con `enum`

Las operaciones binarias no pueden aplicarse a `enum`. Deben emplearse `match` o `matches!`.

```rust
enum Checking {
    foo,
    bar
}

fn main(){
    let v1 = Checking::foo;
    let v2 = Checking::bar;

    if v1 == v2 { // error!
        println!("Success");
    }
}
```

# Control de Flujo

## if/else

```rust
let n: u8 = 5;

if n < 5 {
    println!("Menor a 5");
} else if {
    println!("Igual a 5");
} else {
    println!("Mayor a 5");
}
```

## Loop

Un ciclo infinito, se pueden usar `continue`/`break` para romper el ciclo, tambien se pueden emplear `tags` para especificar el loop a manipular.

```rust
let mut n = 0;
'tag1: loop {
    'tag2: loop {
        println!("{}", n);
        if n > 3 {
            break 'tag1;
        }
        n += 1;
    }
}
```

# Pattern Match

## if let

Para coincidencia de patrones, usada regularmente con enums, Option y Result.

```rust
fn get_age() -> Option<u8> {
    Some(22)
}

fn main(){
    let age = if let Some(age) = get_age() {
        println!("Your age is {}", age);
        age
    } else {
        panic!("Invalid age");
    };
}
```

## Match

Expresión basada en patrones, similar a switch, obliga al programador a cubrir todos los casos posibles.

- Exaustividad, rust exige que se cubran todos los posibles casos, de lo contrario se obtendra un error en tiempo de compilación.
- Patrones complejos, match maneja patrones como rangos, desestructuración y más.
- Condiciones adicionales `if` a los patrones.

```rust
let x = 2;
let result = match x {
  1 => "valor x = 1",
  2 => "valor x = 2",
  _ => "puede ser otro valor"
}; // retorna el string a result

println!("{}", result);
```

> Si no se cubren todos los casos, crea un error.

```rust
let number: i32 = 0;

match number {
  n if n == 1 => println!("Numero uno"),
  n if n == 2 => println!("Numero dos"),
  3 | 4 => println!("Tres o cuatro"),
  _ => println!("Otro numero")
};
```

> `n` funciona gracias al shadowing

> Match con condiciones adicionales.

## Matches!

Una manera mas concisa para verificar el tipo o estado de una variable.

```rust
let alphabet = ['a', 'b', 'c', 'G', '7'];

for caracter in alphabet {
    assert!(matches!(caracter, 'a'..='z' | 'A'..='Z' | '0'..='9'));
}
```

> El uso de `|` respecto al operador logico OR `||` es para combinar patrones, no para evaluar expresiones boleanas.

## Coincidencia con `@`

Crea una variable que coincide con en el patrón.

- Hace explícito la captura del valor dentro del patrón.
- Asegura la captura del valor que coincide con el patrón.

```rust
let val = Some(5);

match val {
    Some(num @ 1..=10) => println!("{:?}", num + 1),
    _ => println!("")
}
```

A pesar de que este ejemplo hace lo mismo, recordar que `unwrap` puede provocar un `panic!` en tiempo de ejecución si el valor es `none`.

```rust
let val = Some(5);

match val {
    Some(1..=10) => println!("{:?}", val.unwrap() + 1),
    _ => println!("")
}
```

## Consideraciones con `match`

El match, además de evaluar patrones, crea bindings nuevos basados en los patrones coincidentes.

```rust
let mut word: String = String::from("hello");

match word {
    val => val.push_str(", world"),
}

println!("{}", word);
```

> Este codigo emitirá un error debido a que `val` mueve el valor dentro de scope donde coincidió el patrón.

# Generics

## Funciones

El tipo de la función será determinado cuando se llama a la función. Incluye uno o mas **parametros de tipo** `<...>`.

```rust
fn imprimir<T>(valor: T) {
    println!("{:?}", valor);
}
```

Comparación de funciones con parámetros especificos y genéricos.

```rust
struct A; // Estructura unidad
struct B(A); // Estructura de tipo tuple `A`
struct C<T>(T); // Estructura generica

fn regular(_s: B) {}

fn generic_specific(_s: C<A>) {}

fn generic_specific_i32(_s: C<i32>) {}

fn generic<T>(_s: C<T>) {}

fn main(){
    regular(B(A));
    generic_specific(C(A));
    generic_specific_i32(C(-1));
    generic(C('a')); // Especificación implicita
    generic(C(1.1));
    generic::<u8>(C(3)); // Especificación explicita
}
```

### Funciones Genéricas con Restricciones

Define un parametro de tipo `T` con la restricción de tener implementado el trait `Add`. `Output = T` establece que la suma de los dos elementos tipo `T` también devolverán un tipo `T`.

```rust
fn sumar<T: std::ops::Add<Output = T>>(a: T, b: T) -> T {
    a + b
}
```

## Const Generics

Permite definir parametros que son constantes en tiempo de compilación, pueden ser usados en la definición de tipos y estructuras genericas.

```rust
// arreglo de tipo `T` con un tamaño constante `N`
struct Array<T, const N: usize> {
    data: [T; N],
}

impl<T, const N: usize> Array<T, N> {
    fn new(data: [T; N]) -> Self {
        Array { data }
    }

    fn get(&self, index: usize) -> &T {
        &self.data[index]
    }
}

fn main() {
    // array de 3 elementos i32
    let arr: Array<i32, 3> = Array::new([1, 2, 3]);

    println!("Elemento en la posición 0: {}", arr.get(0));
}
```

## Traits

Similar a una _interfaz_, define métodos que los tipos se comprometen a implementar.

```rust
// abstraction
trait Vehicle {
    // default method
    fn start(&mut self) {
        println!("It should turn on/off the vehicle");
    }
    fn model(&self) -> &str;
    fn show_info(&self);
}

// specification
struct Car {
    is_on: bool,
    model_name: String,
}

impl Vehicle for Car {
    // default trait methods can be overridden
    fn start(&mut self) {
        self.is_on = !self.is_on;
        println!("The car is {}", if self.is_on { "ON" } else { "OFF" });
    }

    fn model(&self) -> &str {
        &self.model_name
    }

    fn show_info(&self) {
        let is_on: &bool = &self.is_on;
        let is_on_message: &str = if *is_on { "ON" } else { "OFF" };
        println!("Model: {}\nCar is: {}", self.model_name, is_on_message);
    }
}

fn main() {
    let mut c1: Car = Car {
        model_name: "Honda Civic".to_string(),
        is_on: false,
    };

    c1.start();
    c1.start();

    println!("c1\n{}", c1.model());

    c1.show_info();
}
```

### Derive

El atributo `#[derive]` Permite generar implementaciones de ciertos `trait` automaticamente para estructuras y enumeraciones.

```rust
#[derive(Debug, Clone, PartialEq)]
struct Persona {
    nombre: String,
    edad: u32,
}
```

- `Debug`: permite que la estructura sea formateada con el formato de depuración.
- `Clone`: permite crear copias de la estructura.
- `PartialEq`: permite comparar dos instancias de `Persona` por igualdad.
- `PartialOrd` y `Ord`: Permiten comparaciones de orden entre instancias, útil para ordenación.
- ...

> No todos los traits pueden sel derivados.

### Operator

Muchos operadores pueden ser sobrecargados. Los operadores son _azucar sintáctico_ para llamadas a métodos.

```rust
use std::ops;

// Implement fn multiply to make the code work.
// As mentioned above, `+` needs `T` to implement `std::ops::Add` Trait.
// So, what about `*`?  You can find the answer here: https://doc.rust-lang.org/core/ops/
fn multiply<T: std::ops::Mul<Output = T>>(a: T, b: T) -> T {
    a * b
}

fn main() {
    assert_eq!(6, multiply(2u8, 3u8));
    assert_eq!(5.0, multiply(1.0, 5.0));

    println!("Success!");
}
```

> `T: std::ops::Mul<Output = T>` es la especificación de la restricción para el tipo genérico `T`.

Implementando métodos `Add` y `Sub` para estructuras.

```rust
// Fix the errors, DON'T modify the code in `main`.
use std::ops;

struct Foo;
struct Bar;

#[derive(Debug, PartialEq)]
struct FooBar;

#[derive(Debug, PartialEq)]
struct BarFoo;

// The `std::ops::Add` trait is used to specify the functionality of `+`.
// Here, we make `Add<Bar>` - the trait for addition with a RHS of type `Bar`.
// The following block implements the operation: Foo + Bar = FooBar
impl ops::Add<Bar> for Foo {
    type Output = FooBar;

    fn add(self, _rhs: Bar) -> FooBar {
        FooBar
    }
}

impl ops::Sub<Bar> for Foo {
    type Output = BarFoo;

    fn sub(self, _rhs: Bar) -> BarFoo {
        BarFoo
    }
}

fn main() {
    // DON'T modify the code below.
    // You need to derive some trait for FooBar to make it comparable.
    assert_eq!(Foo + Bar, FooBar);
    assert_eq!(Foo - Bar, BarFoo);

    println!("Success!");
}
```

### Trait as a function parameter

En lugar de un tipo concreto para un parámetro, podemos usar `trait` como palabra clave junto al nombre del trait, asi decimos que el parametro acepta cualquier tipo que implemente el trait especificado.

```rust
trait Summary {
    fn summarize(&self) -> String;
}

#[derive(Debug)]
struct Post {
    title: String,
    author: String,
    content: String,
}

impl Summary for Post {
    fn summarize(&self) -> String {
        format!("The author of post {} is {}", self.title, self.author)
    }
}

// Referencia de `a` que acepta cualquier tipo
// que implemente el trait `Summary`
fn summary(a: &impl Summary) {
    a.summarize();
}

fn main() {
    let post = Post {
        title: "Popular Rust".to_string(),
        author: "Sunface".to_string(),
        content: "Rust is awesome!".to_string(),
    };

    summary(&post);

    println!("{:?}", post);
}
```

### Retornando tipos que implementan Trait

Se puede usar la sintaxis `impl Trait` en la especificación de retorno para devolver un tipo que implemente un trait, **siempre y cuando** se retorne un único tipo.
Si se desea retornar multiples tipos que implementan un trait, se debe retornar un `Trait Object`.

```rust
struct Sheep {
    name: String
}

struct Cow {
    name: String
}

trait Animal {
    fn noise(&self) -> String;
    fn get_name(&self) -> &str;
}

impl Animal for Sheep {
    fn noise(&self) -> String {
        "baaaaah!".to_string()
    }

    fn get_name(&self) -> &str  {
        &self.name
    }
}

impl Animal for Cow {
    fn noise(&self) -> String {
        "moooooo!".to_string()
    }

    fn get_name(&self) -> &str  {
        &self.name
    }
}

// Se retorna un unico tipo
fn random_sheep(random_number: f64) -> impl Animal {
    if random_number < 0.5 {
        Sheep {
            name: "Fulanito".to_string()
        }
    } else {
        Sheep {
            name: "Menganito".to_string()
        }
    }
}

// Se retornan multiples tipos
fn random_animal(random_number: f64) -> Box<dyn Animal> {
    if random_number < 0.5 {
        Box::new(Sheep {name: "Pedro".to_string()})
    } else {
        Box::new(Cow {name: "Joel".to_string()})
    }
}

fn main() {
    let random_number = 0.634;
    let sheep = random_sheep(random_number);
    println!("You've randomly chosen {}, and it says {}", sheep.get_name(), sheep.noise());

    let animal = random_animal(random_number);
    println!("You've randomly chosen {}, and it says {}", animal.get_name(), animal.noise());
}
```

> `dyn` significa dinámico, se utiliza para crear un **trait object**, estos permiten almacenar o interactuar con tipos que implementan un trait pero que su implementación especifica se desconoce en tiempo de compilación.
> En rust, las llamadas a funciones se resuelven en tiempo de compilación (dispatch estático), pero `dyn` habilita un dispath dinámico donde la decisión sobre que método de trait ejecutar se toma en tiempo de ejecución.
> Los objetos `dyn Trait` no tienen un tamaño conocido en tiempo de compilación, por eso deben usarse a traves de un puntero `Box`, `Rc` o una referencia `&dyn Trait`.

### Trait Bound

Son restricciones para especificar que un tipo debe implementar un trait en particular, `T: trait`.

```rust
fn main() {
    assert_eq!(sum(1, 2), 3);
}

// Implement `fn sum` with trait bound in two ways.
fn sum<T: std::ops::Add<Output = T>>(x: T, y: T) -> T {
    x + y
}
```

Usando `where`.

```rust
fn main() {
    assert_eq!(sum(1, 2), 3);
}

fn sum<T>(x: T, y: T) -> T
where
    T: std::ops::Add<Output = T>,
{
    x + y
}
```

Trabajando con funciones anonimas (closure).

```rust
struct Ejemplo<T: Fn(u32) -> u32> {
    operacion: T,
    valor: Option<u32>,
}
```

`T: Fn(u32) -> u32` significa que `T` debe implementar el trait `Fn`, es decir, debe ser una función anónima que toma un `u32` como entrada y retorna un `u32`. Esto permite trabajar con closures que cumplan con la firma requerida sin importar su implementación.

Uso en `impl`.

```rust
impl<T: Fn(u32) -> u32> Ejemplo<T> {
    fn new(operacion: T) -> Ejemplo<T> {
        Ejemplo {
            operacion,
            valor: None,
        }
    }

    fn obtener_valor(&mut self, argumento: u32) -> u32 {
        match self.valor {
            Some(v) => v,
            None => {
                let resultado = (self.operacion)(argumento); // Llamada al closure
                self.valor = Some(resultado);
                resultado
            }
        }
    }
}
```

Permite inicializar la estructura con un closure que cumpla el trait `Fn(u32) -> u32`.

Llamada al closure: `(self.operacion)(argumento)`

### Variantes del trait `Fn`

- Fn: Closure que puede ser llamada sin modificar su estado interno. Es inmutable. `T: Fn(u32) -> u32`
- FnMut: Closure que puede modificar su estado interno. `T: FnMut(u32) -> u32`
- FnOnce: Closure que puede ser llamada una única vez, consumiendo su estado interno. `T: FnOnce(u32) -> u32`

## Retornando traits con `dyn`

Rust necesita conocer cuanto espacio necesita el retorno de una función. Por lo tanto las funciones deben:

- Retornar un tipo concreto con `impl Trait`.
- Retornar un trait object con `dyn`.

### Consecuencias de usar `dyn`

Al trabajar con un objeto de tipo trait `Box<dyn Trait>` implica que solo se pueden acceder a los metodos definidos en el trait `Trait`.

```rust

trait Bird {
    fn quack(&self) -> String;
}

struct Duck;
impl Duck {
    fn swim(&self) {
        println!("Look, the duck is swimming")
    }
}
struct Swan;
impl Swan {
    fn fly(&self) {
        println!("Look, the duck.. oh sorry, the swan is flying")
    }
}

impl Bird for Duck {
    fn quack(&self) -> String{
        "duck duck".to_string()
    }
}

impl Bird for Swan {
    fn quack(&self) -> String{
        "swan swan".to_string()
    }
}

fn main() {
    // FILL in the blank.
    let duck = Duck;
    duck.swim();

    let bird = hatch_a_bird(2);
    // This bird has forgotten how to swim, so below line will cause an error.
    // bird.swim();
    // But it can quak.
    assert_eq!(bird.quack(), "duck duck");

    let bird = hatch_a_bird(1);
    // This bird has forgotten how to fly, so below line will cause an error.
    // bird.fly();
    // But it can quak too.
    assert_eq!(bird.quack(), "swan swan");

    println!("Success!");
}

// IMPLEMENT this function.
fn hatch_a_bird(n: u8) -> Box<dyn Bird> {
    match n {
        1 => Box::new(Swan{}),
        2 => Box::new(Duck{}),
        _ => todo!()
    }
}
```

> Métodos como `swim` y `fly` no están disponibles porque no se definen en el trait `Bird`.

### Static dispatch and Dinamic dispatch

```rust
trait Foo {
    fn method(&self) -> String;
}

impl Foo for u8 {
    fn method(&self) -> String { format!("u8: {}", *self) }
}

impl Foo for String {
    fn method(&self) -> String { format!("string: {}", *self) }
}

// Usando generics
// - Toma un parametro `a` que puede ser cualquier tipo que
//   implemente el trait `Foo`.
// El compilador puede resolver que implementación `method`
// llamar en tiempo de compilación.
fn static_dispatch<T: Foo>(a: T) {
    a.method();
}

// Usando referencia a un trait object
// - `&dyn Foo` es un puntero a cualquier tipo que implementa
//    el trait `Foo` pero el tamaño exacto del tipo no se
//    conoce en tiempo de compilación.
// `dyn` indica que es un Trait Object, el compilador debe resolver
//  en tiempo de ejecución que método usar.
fn dynamic_dispatch(a: &dyn Foo){
    a.method();
}

fn main() {
    let x = 5u8;
    let y = "Hello".to_string();

    static_dispatch(x);
    dynamic_dispatch(&y);

    println!("Success!");
}
```

### Object Safe

Solo se pueden crear trait objects si son _object-safe trait_, estos cumplen lo siguiente:

- El tipo de retorno no es `Self`.
- No tiene parámetros de tipo genérico.

```rust
trait MyTrait {
    fn f(&self) -> Box<dyn MyTrait>;
}

impl MyTrait for u32 {
    fn f(&self) -> Box<dyn MyTrait> { Box::new(42) }
}

impl MyTrait for String {
    fn f(&self) -> Box<dyn MyTrait> { Box::new(self.clone()) }
}

fn my_function(x: Box<dyn MyTrait>) -> Box<dyn MyTrait> {
    x.f()
}

fn main() {
    my_function(Box::new(13_u32));
    my_function(Box::new(String::from("abc")));

    println!("Success!");
}
```

## Advance Trait

### Associaded Types

Definen tipos asociados en el contexto de un trait.

- Actua como un placeholder que será especificado por el tipo que implemente ese trait.
- Simplifica la sintaxis.

```rust
trait Operation {
    type Input;
    type Output;

    fn pow(&self, n: Self::Input) -> Self::Output;
}

struct Number(i32);

impl Operation for Number {
    type Input = u32;
    type Output = i32;

    fn pow(&self, n: Self::Input) -> Self::Output {
        self.0.pow(n)
    }
}

fn main() {
    let a: i32 = 3;
    let b: u32 = 3;
    let my_number: Number = Number(a);

    println!("{} pow to {} is {}", a , b, my_number.pow(b));
}
```

### Default Generic Type Parameters

Cuando se define un trait, estructura o enumeracion con un parametro generico, se puede especificar un tipo predeterminado.

```rust
#[derive(Debug)]
struct Point<T = i32> {
    x: T,
    y: T,
}

fn main() {
    let p1 = Point { x: 1, y: 2 };
    println!("{:?}", p1);
}
```

```rust
use std::ops::Sub;

#[derive(Debug)]
struct Point<T = i32> {
    x: T,
    y: T,
}

impl<T> Sub for Point<T>
where
    T: Sub<Output = T>,
{
    type Output = Self;

    fn sub(self, rhs: Self) -> Self::Output {
        Point {
            x: self.x - rhs.x,
            y: self.y - rhs.y,
        }
    }
}

fn main() {
    let p1 = Point { x: 1, y: 2 };
    let p2 = Point { x: 3, y: 5 };

    println!("{:?}", p1);

    let result = p1 - p2;
    println!("{:?}", result);
}
```

### Fully Qualified Syntax

Nada en rust previene que dos traits tengan un método con el mismo nombre, tampoco que se implementen ambos traits a un solo tipo.

Cuando se llaman dos métodos con el mismo nombre se deben emplear la sintaxis completamente cualificada.

```rust
trait UsernameWidget {
    // Get the selected username out of this widget
    fn get(&self) -> String;
}

trait AgeWidget {
    // Get the selected age out of this widget
    fn get(&self) -> u8;
}

// A form with both a UsernameWidget and an AgeWidget.
struct Form {
    username: String,
    age: u8,
}

impl UsernameWidget for Form {
    fn get(&self) -> String {
        self.username.clone()
    }
}

impl AgeWidget for Form {
    fn get(&self) -> u8 {
        self.age
    }
}

fn main() {
    let form = Form{
        username: "rustacean".to_owned(),
        age: 28,
    };

    // If you uncomment this line, you'll get an error saying
    // "multiple `get` found". Because, after all, there are multiple methods
    // named `get`.
    // println!("{}", form.get());

    let username = UsernameWidget::get(&form);
    assert_eq!("rustacean".to_owned(), username);
    let age = AgeWidget::get(&form); // You can also use `<Form as AgeWidget>::get`
    assert_eq!(28, age);

    println!("Success!");
}
```

### Supertraits

Permite relaciones jerarquicas entre traits. Un supertrait es un trait que debe ser implementado antes de que lo implemente otro trait que lo utilice como base.

```rust
trait A {
    fn do_a(&self);
}

trait B: A {
    fn do_b(&self);
}

trait C: A + B { // debe definir A y B
    fn do_c(&self);
}

struct MyStruct;

impl A for MyStruct {
    fn do_a(&self) {}
}

impl B for MyStruct {
    fn do_b(&self) {}
}

impl C for MyStruct {
    fn do_c(&self) {}
}

fn main() {
    let s1 = MyStruct;

    s1.do_a();
    s1.do_b();
    s1.do_c();
}
```

### Orphan Rules

Se puede implementar un trait para un tipo si al menos uno de ellos (el trait o el tipo) pertenecen a tu crate.

- Eres propietario del tipo en concreto.
- Eres propietario del crate.

Ejemplo valido

```rust
pub struct MyType;

pub trait MyTrait {
    fn do_something(&self);
}

impl MyTrait for MyType {
    fn do_something(&self) {
        println!("Doing something!");
    }
}
```

> Puedes implementar el trait porque el tipo pertenece a tu crate.

Ejemplo no valido

```rust
use std::fmt;

impl fmt::Display for Vec<u32> {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        write!(f, "[{} elements]", self.len())
    }
}
```

> `Display` ni `Vec` pertenecen al crate actual

Soluciones

- Newtype Pattern, Envolver el tipo externo e implementar el trait en ese tipo.

```rust
use std::fmt;

struct MyVec(Vec<u32>);

impl fmt::Display for MyVec {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        write!(f, "[{} elements]", self.0.len())
    }
}
```

- Extender el trait en mi crate, para tipos externos.

- Crear tus propios crates personalizados.

## Collection Types

- Dinámico, colección que puede crecer y reducir su tamaño durante la ejecución del programa.
- Almacenamiento Contiguo, los elementos se almacenan en memoria contiguo, permite un acceso rápido usando índices.

```rust
let mut vec: Vec<i32> = Vec::new();
let mut vec = vec![1, 2, 3];

vec.push(4); // [1, 2, 3, 4]

vec.pop(); // [1, 2, 3]

vec.remove(1); // [1, 3]

let val = vec[0]; // 1
let val = vec.get(0); // Some(1)
let val = vec.get(5); // None, previene panic

for i in &vec {
    // Vec no implementa `Copy`
    println!("{}", i);
}
```

### Propiedades

- Tipo de datos homogéneo, todos los elementos de un vector deben ser del mismo tipo.
- Capacidad, un vector tiene una capacidad definida, si se excede, la memoria se realoja automaticamente.
- Tamaño, la cantidad de elementos almacenados `len()`.
- Reasignación, rust automáticamente realoja la memoria, podria ser una operación costosa.

Se puede solventar la propiedad de almacenar un solo tipo usando enums o structs:

```rust
#[derive(Debug, PartialEq)]
enum IpAddr {
    V4(String),
    V6(String),
}
fn main() {
    let v : Vec<IpAddr>= vec![
        IpAddr::V4("127.0.0.1".to_string()),
        IpAddr::V6("::1".to_string())
    ];

    // Comparing two enums need to derive the PartialEq trait
    assert_eq!(v[0], IpAddr::V4("127.0.0.1".to_string()));
    assert_eq!(v[1], IpAddr::V6("::1".to_string()));

    println!("Success!");
}
```

```rust
trait IpAddr {
    fn display(&self);
}

struct V4(String);
impl IpAddr for V4 {
    fn display(&self) {
        println!("ipv4: {:?}",self.0)
    }
}
struct V6(String);
impl IpAddr for V6 {
    fn display(&self) {
        println!("ipv6: {:?}",self.0)
    }
}

fn main() {
    let v: Vec<Box<dyn IpAddr>> = vec![
        Box::new(V4("127.0.0.1".to_string())),
        Box::new(V6("::1".to_string())),
    ];

    for ip in v {
        ip.display();
    }
}
```

### HashMap

Permite almacenar y acceder a valores mediante clave-valor.

- Pueden crecer como los `Vec` pero tambien pueden encogerse cuando les sobra espacio.
- Al pasar las claves-valor a los HashMap, tambien aplican las reglas de Ownership y Borrowing.

```rust
use std::collections::HashMap;

fn main() {
    let mut my_hm: HashMap<String, i32> = HashMap::new();

    my_hm = HashMap::from([("Marco".to_string(), 22), ("Juan".to_string(), 23)]);

    my_hm.insert("Carlos".to_string(), 24);

    let n1 = my_hm.get("Juan"); // -> Option

    let n1 = my_hm["Juana"]; // -> Value, posible panic

    my_hm.remove("Juan");

    for (key, value) in &my_hm {
        println!("{}: {}", key, value);
    }

    println!("{:?}", my_hm.contains_key("Juan"));

    // Inserta una clave-valor solo si la clave no existe
    my_hm.entry("Frank".to_string()).or_insert(25);

    // No cambia el valor, Marco ya existe en el HashMap
    my_hm.entry("Marco".to_string()).or_insert(18);
}
```

#### Restricciones

Cualquier tipo que implemente los traits `eq` y `hash` puede ser una clave en un hashmap.

- bool
- int, uint
- String, &str

> `f32` y `f64` no implementan `hash` porque la precisión del punto flotante provocarian muchos errores en la generación de claves hash.

```rust
use std::collections::HashMap;

#[derive(Hash, Eq, PartialEq, Debug)]
struct Viking {
    name: String,
    country: String,
}

impl Viking {
    /// Creates a new Viking.
    fn new(name: &str, country: &str) -> Viking {
        Viking {
            name: name.to_string(),
            country: country.to_string(),
        }
    }
}

fn main() {
    // Use a HashMap to store the vikings' health points.
    let vikings = HashMap::from([
        (Viking::new("Einar", "Norway"), 25),
        (Viking::new("Olaf", "Denmark"), 24),
        (Viking::new("Harald", "Iceland"), 12),
    ]);

    for (viking, health) in &vikings {
        println!("{:?} has {} hp", viking, health);
    }
}
```

> `PartialEq` implementa la capacidad de comparación, `Eq` indica que es una comparación total y definitiva, sin posibilidad de devolver valores desconocidos o incompletos
