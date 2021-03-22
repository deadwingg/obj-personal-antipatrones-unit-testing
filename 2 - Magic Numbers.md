**Sabias que...** 

En el ámbito del unit testing hay errores que solemos cometer con frencuencia,
sobre todo cuando recien arrancamos a escribir nuestros propios tests. 
Estos errores se pueden denominar como **antipatrones**.
Vamos a analizar algunos de ellos para tenerlos en cuenta y escribir tests mas legibles,
mantenibles y confiables.

**Números mágicos**

Los números magicos son literales que aparecen en nuestro código sin el contexto necesario para entender su significado y que dificultan entender que sucede realmente.

**Ejemplo**
```java
public class BowlingGameTest {
    @Test
    public void perfectGame() throws Exception {
        roll(10, 12); // magic
        assertThat(game.score(), is(equalTo(300))); // numbers
    }
}
```
**El problema**

¿Por qué roll de 10 y 12 nos da un puntaje de 300? ¿Qué representan estos argumentos de roll?
Quizá esta información sea obvia al momento de escribir el test, pero ¿qué pasa si lo lee otra persona?, o incluso nosotros mismos luego de uno meses...


**¿Cómo podemos mejorarlo?**

Lo más común es hacer uso de constantes con nombres decriptivos para darle contexto a las cifras, por ejemplo:

```java
public class BowlingGameTest {
    private static final int TEN_PINS = 10;
    private static final int TWELVE_TIMES = 12;
    @Test
    public void perfectGame() throws Exception {
        roll(TEN_PINS, TWELVE_TIMES);
        assertThat(game.score()).isEqualTo(300);
    }
}
```

De esta manera es mas claro de donde sale el 300, tirar los 10 pinos 12 veces seguidas nos da el puntaje perfecto en el bowling(300).

Algo que puede ocasionar este enfoque es que tengamos que crear muchas constantes si tenemos multiples escenarios.
Otra alternativa a los magic numbers es crear métodos privados que nos ayuden a dar contexto y sirvan para multiples valores:

```java
public class BowlingGameTest {
    @Test
    public void perfectGame() throws Exception {
        roll(pins(10), times(12));
        assertThat(game.score(), is(equalTo(300)));
    }
    private int pins(int n) { return n; }
    private int times(int n) { return n; }
}
```

Evitando los magic numbers podemos lograr que nuestros tests sean mas claros!
