**Sabias que...** 

En el ámbito del unit testing hay errores que solemos cometer con frencuencia, sobre todo cuando recien arrancamos a escribir nuestros propios tests. Estos errores se pueden denominar como **antipatrones**. Vamos a analizar algunos de ellos para tenerlos en cuenta y escribir tests mas legibles, mantenibles y confiables.

**Falsas promesas**

Este antipatrón trata de un test que hace mucho menos de lo dice hacer... o no hace nada directamente.

Hay 3 escenarios comunes de este antipatrón:
-   El test no *hace* nada
-   El test no *verifica* nada
-   El test no es tan exhaustivo como su nombre indica

**Ejemplo de un test que no hace nada**

```java
@Test
public void filteringObjects() throws Exception {
    //Array array = new Array("joe", "jane", "john");
    //Array filtered = array.filter(new Predicate<String>() {
    //  public boolean evaluate(String candidate) {
    //      return candidate.length() == 4;
    //  }
    //});
    //assertEquals(new Array("jane", "john"), filtered);
}
```
**El problema**

Si este test es parte de una clase que tiene muchos tests y corremos todos, nuestro IDE lo marcará como exitoso, despues de todo el test no falló y al ver el informe podemos suponer erroneamente que la funcionalidad está implementada de foma correcta.
Aún si lo encontramos, ¿qué deberiamos hacer? Alguien escribió el test por una razón, y también lo comentó, pero no tenemos idea del porque.

**¿Cómo podemos mejorarlo?**

En este caso lo que debemos evitar es commitear un test comentado en primer lugar, si el código ya no es necesario debemos *borrarlo*. Dejarlo comentado soló creará confusión en el futuro. Además en caso de necesitar consultar el código viejo podemos recurrir a nuestra herramienta de control de versiones (git, svn).


**Ejemplo de un test que no verifica nada**

```java
@Test
public void cloneRetainsEssentialDetails() throws Exception {
    Document model = ModelFactory.createDocument();
    model.schedule(new Transaction("Test", date("2010-01-20"), 1));
    model = model.clone();
    Transaction tx = model.getScheduledTransactions().get(0);
}
```
**El problema**
A simple vista el test parece correcto, tiene un nombre descriptivo y es corto, pero prestando atención notamos que no hay ningun assert.
Este es un ejemplo un test que nunca falla, y de la misma manera que en la sección anterior solo genera confusión.

**¿Cómo podemos mejorarlo?**

La solución a este escenario es simple: asegurarnos de siempre tener un assert. Una técnica que nos puede ayudar es la de escribir el assert primero y luego completar lo restante con ese assert en mente.

**Ejemplo de un test que hace menos de lo que sugiere**

```java
@Test
public void zipBetweenTwoArraysProducesAHash() throws Exception {
    Array keys = new Array("a", "b", "c");
    Array values = new Array(1, 2, 3);
    Array zipped = keys.zip(values);

    assertThat(zipped.flatten()).isNotNull()
}
```
**El problema**

El nombre del test indica que verificará que al hacer un zip entre 2 arrays se produce un Hash. Se instancian los arrays y se produce la operación zip, pero el test verifica que *flatten* no sea nulo, lo cual está muy lejos de garantizar que haya un Hash, si flatten retornam, por ejemplo `""` el test también pasará

**¿Cómo podemos mejorarlo?**

Escribir primero el assert también nos ayuda a ver más rápido un posible conflicto entre la aserción y el nombre del test. Otra alternativa es utilizar un nombre como placeholder y escribir el nombre una vez que tengamos completo el test, de esta manera nos será mas facil describir lo que realmente estamos testeando.