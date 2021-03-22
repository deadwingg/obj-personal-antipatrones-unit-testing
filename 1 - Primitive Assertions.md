**Sabias que...** 

En el ámbito del unit testing hay errores que solemos cometer con frencuencia,
sobre todo cuando recien arrancamos a escribir nuestros propios tests. 
Estos errores se pueden denominar como **antipatrones**.
Vamos a analizar algunos de ellos para tenerlos en cuenta y escribir tests mas legibles,
mantenibles y confiables.

**Aserciones primitivas**
Nuestras aserciones no tienen el nivel suficiente de abstracción y 
hacen que nuestro test sea dificil de entender.

**Ejemplo**
```java
@Test
public void outputHasLineNumbers() {
    String content = "1st match on #1\nand\n2nd match on #3";
    String out = grep.grep("match", "test.txt", content);
    assertTrue(out.indexOf("test.txt:1 1st match") != -1);
    assertTrue(out.indexOf("test.txt:3 2nd match") != -1);
}
```
**El problema**

El propósito de las aserciones en un test es expresar algo que asumimos o nuestra intención, 
de manera que estemos describiendo como funciona(rá) nuestro código.

¿Qué estamos comprobando en este test?

Estamos verificando que la string de salida contenga el nombre de archivo y el número de linea en el que fue encontrado,
utilizando el método indexOf de la clase String y esperando que el resultado sea distinto de '-1'. 
Seguir este test es mas dificil de lo que debería porque la aserción está expresada sin el nivel de abstracción
necesario.

**¿Cómo podemos mejorarlo?**

Tenemos a nuestra disposición varias alternativas para poder expresar mejor
esta aserción, haciendo uso de librerias como [Hamcrest](http://hamcrest.org/) 
o [AssertJ](https://joel-costigliola.github.io/assertj/).

Vamos con un ejemplo utilizando AssertJ, que nos brinda el método contains:
```java
@Test
public void outputHasLineNumbers() {
    String content = "1st match on #1\nand\n2nd match on #3";
    String out = grep.grep("match", "test.txt", content);
    assertThat(out).contains("test.txt:1 1st match");
    assertThat(out).contains("test.txt:3 2nd match");
}
```

Comparando el primer test con el segundo podemos ver que en este último es evidente lo que 
queremos verificar, logrando tener un test más legible y por lo tanto más mantenible.
