**Sabias que...** 

En el ámbito del unit testing hay errores que solemos cometer con frencuencia,
sobre todo cuando recien arrancamos a escribir nuestros propios tests. 
Estos errores se pueden denominar como **antipatrones**.
Vamos a analizar algunos de ellos para tenerlos en cuenta y escribir tests mas legibles,
mantenibles y confiables.

**Setup sermon**

Cuando la fase de arrange de nuestro test se torna larga, sea porque esta utilizando mocks o creando objetos complejos, corremos el riesgo de que se pierda la intención del test entre tantos detalles secundarios.

**Ejemplo**
```java
public class TicketServiceTest {
    
    private Map<String, String> datos;

    public void generar_conOfertaElegidaInvalida_lanzaExcepcion() {
        datos.put("nombre", "placeholder");
        datos.put("apellido", "placeholder");
        datos.put("mail", "valid@mail.com");
        datos.put("tipoDeDocumento", "OTROS");
        datos.put("numeroDeDocumento", "39356743");
        datos.put("fechaDeNacimiento", "01/02/2005");
        datos.put("genero", "M");
        datos.put("tipoDeTramite", "CAMBIO_MOVIL");
        datos.put("numeroDeLinea", "11");
        datos.put("categoriaDelPlan", "Progresar");
        datos.put("tipoDeTramite", "ALTA_FIJA");
        datos.put("ofertaElegida", " ");

        assertThatThrownBy(() -> ticketService.generar(datos))
                .isInstanceOf(IllegalArgumentException.class)
                .hasMessage("La oferta elegida no es valida.");
    }
}
```
**El problema**

La mayoría del código en este método está ahi para generar un objeto con datos válidos, y es facil perder de vista cual es la linea relevante para el test que estamos creando.

**¿Cómo podemos mejorarlo?**

Podemos recurrir a un método privado con nombre descriptivo para tener un objeto con datos válidos y dejar en el test unicamente el campo relevante:

```java
public class TicketServiceTest {
    
    private Map<String, String> datos;

    public void generar_conOfertaElegidaInvalida_lanzaExcepcion() {
        
        completarDatosValidos();
        datos.put("ofertaElegida", " ");

        assertThatThrownBy(() -> ticketService.generar(datos))
                .isInstanceOf(IllegalArgumentException.class)
                .hasMessage("La oferta elegida no es valida.");
    }

    private Map<String, String> completarDatosValidos() {
        datos.put("nombre", "placeholder");
        datos.put("apellido", "placeholder");
        datos.put("mail", "valid@mail.com");
        datos.put("tipoDeDocumento", "OTROS");
        datos.put("numeroDeDocumento", "39356743");
        datos.put("fechaDeNacimiento", "01/02/2005");
        datos.put("genero", "M");
        datos.put("tipoDeTramite", "CAMBIO_MOVIL");
        datos.put("numeroDeLinea", "11");
        datos.put("categoriaDelPlan", "Progresar");
        datos.put("tipoDeTramite", "ALTA_FIJA");
        datos.put("ofertaElegida", "RETENCION");
    }
}
```

Observemos el cambio en la lectura de nuestro test:

Sabemos que luego del llamado a *completarDatosValidos* el resto de las validaciones no fallará y en la linea siguiente completamos el dato que corresponde a nuestro escenario de pruebas, expresando la intención del test de manera mas clara.

Y para los que conozcan patrones de diseño otra gran opción en este caso es el uso del patron de diseño [Builder](https://somospnt.com/blog/46-builders-la-solucion-definitiva-a-los-datos-de-prueba-en-los-test)

Siempre que terminamos de escribir el test debemos preguntarnos *¿Se entiende clara y obviamente de qué se trata este test?* Si dudamos al responder, es probable que aún nos quede trabajo por hacer.
