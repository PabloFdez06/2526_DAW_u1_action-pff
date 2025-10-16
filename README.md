# Generación Automática de Documentación Java
Este proyecto implementa un flujo automatizado usando GitHub Actions para generar documentación Java en formatos HTML y PDF empleando Javadoc y Pandoc.

## a) Herramientas y comandos usados
Las principales herramientas son:

- Javadoc: genera documentación HTML desde los comentarios en el código.

 ```
javadoc -d docs/html -sourcepath src/main/java -subpackages org.example
Pandoc + LaTeX: convierte la salida HTML a PDF.
```

```
pandoc -s docs/html/index.html -o docs/pdf/documentacion.pdf
```

Estas herramientas se ejecutan automáticamente a través del flujo de trabajo definido en .github/workflows/ci.yml.

## b) Ejemplo de código documentado
Ejemplo de código Java usando el estilo JavaDoc:

java
```
package org.example;

/**
 * Clase que representa un saludo.
 */
public class Saludo {

    /**
     * Devuelve un mensaje de saludo personalizado.
     *
     * @param nombre Nombre de la persona a saludar.
     * @return Mensaje de saludo.
     */
    public String saludar(String nombre) {
        return "Hola, " + nombre + "!";
    }
}

```

## c) Formatos generados y ubicaciones

| Formato | Ruta | Descripción |
|----------|------|-------------|
| HTML | docs/html/index.html | Documentación navegable generada por Javadoc |
| PDF | docs/pdf/documentacion.pdf | Documento exportado a formato PDF generado con Pandoc |


## d) Workflow y eventos
El flujo de trabajo “Generar Documentación Java (robusto)” se ejecuta cuando:

Se realiza un push a las ramas main o master.

Se crea un pull request.

Se ejecuta manualmente mediante workflow_dispatch.

### Pasos del trabajo (build-docs):
- Clonar el repositorio.

- Instalar Java 17.

- Instalar Pandoc + LaTeX.

- Generar Javadoc en HTML.

- Convertir a PDF.

- Subir artefactos de documentación.

- Realizar commit automático con los archivos generados.

## e) Mensajes de commit recomendados
Usa el modo imperativo, claro y descriptivo. Ejemplos:


- update: generar documentación automáticamente
- fix: corregir etiquetas @param inexistentes
- add: plantilla base de JavaDoc
- refactor: reorganizar estructura de paquetes

## f) Evidencia de configuración SSH en GitHub

### Pasos para autenticar con SSH:

#### Generar clave SSH
```
ssh-keygen -t ed25519 -C "tu_email@ejemplo.com"
```

#### Copiar la clave pública
```
cat ~/.ssh/id_ed25519.pub
```

#### Añadir la clave pública en GitHub → Configuración > SSH and GPG keys

#### Verificar conexión
```
ssh -T git@github.com
```

Salida esperada:

```
Hi <usuario>! You've successfully authenticated, but GitHub does not provide shell access.
```

## g) Reproducir la generación de documentación
Clona y ejecuta los comandos manualmente:


```
git clone git@github.com:<usuario>/<repositorio>.git
cd <repositorio>
```

### Generar documentación localmente
```
javadoc -d docs/html -sourcepath src/main/java -subpackages org.example
pandoc -s docs/html/index.html -o docs/pdf/documentacion.pdf
```
