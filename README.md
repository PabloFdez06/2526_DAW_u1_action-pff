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

---

## Documentación del Workflow de Generación Automática

### Introducción

En este proyecto he implementado un sistema de generación automática de documentación mediante GitHub Actions. El objetivo principal es que cada vez que realizo cambios en el código y los subo al repositorio, la documentación se genere automáticamente sin necesidad de ejecutar comandos manualmente.

### Estructura del Workflow

He creado un archivo de workflow en la ruta `.github/workflows/ci.yml`. Este archivo contiene todas las instrucciones que GitHub Actions ejecutará de forma automática. El workflow se llama "Generar Documentación Java (robusto)" y está configurado para ejecutarse en tres situaciones diferentes:

1. Cuando hago un push a las ramas main o master
2. Cuando se crea un pull request
3. Cuando lo ejecuto manualmente desde la interfaz de GitHub

### Configuración de Permisos

Lo primero que he configurado en el workflow son los permisos necesarios. He establecido permisos de escritura sobre el contenido del repositorio para que el workflow pueda hacer commits automáticos con la documentación generada:

```yaml
permissions:
  contents: write
```

### Pasos del Proceso de Generación

He dividido el proceso en varios pasos que se ejecutan secuencialmente en un entorno Ubuntu:

#### 1. Clonación del Repositorio

El primer paso consiste en clonar el repositorio completo en el servidor de GitHub Actions. He utilizado la acción `actions/checkout@v4` con la opción `persist-credentials: true` para mantener las credenciales durante todo el proceso:

```yaml
- name: Clonar repositorio
  uses: actions/checkout@v4
  with:
    persist-credentials: true
```

#### 2. Instalación de Java

Como mi proyecto es Java y necesito Javadoc para generar la documentación, he configurado la instalación de Java 17 utilizando la distribución Temurin. Este paso asegura que el entorno tenga todas las herramientas necesarias de Java:

```yaml
- name: Instalar Java
  uses: actions/setup-java@v4
  with:
    java-version: '17'
    distribution: 'temurin'
```

#### 3. Instalación de Pandoc y LaTeX

Para poder convertir la documentación HTML a PDF, he incluido un paso que instala Pandoc junto con las librerías necesarias de LaTeX. He usado `apt-get` para instalar los paquetes:

```yaml
- name: Instalar Pandoc y LaTeX
  run: |
    sudo apt-get update
    sudo apt-get install -y pandoc texlive-latex-recommended texlive-latex-extra texlive-fonts-recommended
```

#### 4. Generación de Documentación HTML

He configurado un paso para generar la documentación HTML usando Javadoc. Este paso crea el directorio `docs/html` y ejecuta el comando `javadoc` sobre el código fuente. He añadido validación para detectar si hay errores durante la generación:

```yaml
- name: Generar documentación HTML con Javadoc
  run: |
    echo " Generando Javadoc..."
    mkdir -p docs/html
    if javadoc -d docs/html -sourcepath src/main/java -subpackages org.example; then
      echo " Javadoc generado correctamente."
    else
      echo " Error generando Javadoc."
      exit 1
    fi
```

El parámetro `-d docs/html` indica el directorio de salida, `-sourcepath src/main/java` señala dónde está el código fuente, y `-subpackages org.example` indica que debe documentar todos los subpaquetes dentro de org.example.

#### 5. Generación de Documentación PDF

Después de generar el HTML, he configurado un paso adicional que convierte la página principal de la documentación HTML a formato PDF. Esto lo consigo usando Pandoc:

```yaml
- name: Generar documentación en PDF
  run: |
    echo " Generando PDF desde HTML..."
    mkdir -p docs/pdf
    if pandoc -s docs/html/index.html -o docs/pdf/documentacion.pdf; then
      echo " PDF generado correctamente."
    else
      echo " Error generando PDF."
      exit 1
    fi
```

La opción `-s` le indica a Pandoc que genere un documento independiente completo, y `-o` especifica el archivo de salida.

#### 6. Subida de Artefactos

He incluido un paso que sube toda la documentación generada como artefacto de GitHub Actions. Esto permite descargar la documentación directamente desde la interfaz de GitHub sin necesidad de hacer commit:

```yaml
- name: Subir documentación como artefacto
  uses: actions/upload-artifact@v4
  with:
    name: documentacion
    path: docs/
```

#### 7. Commit Automático

Finalmente, he configurado un commit automático que guarda la documentación generada directamente en el repositorio. He utilizado la acción `stefanzweifel/git-auto-commit-action@v5` que detecta cambios en el directorio `docs/` y los confirma automáticamente:

```yaml
- name: Commit automático de documentación
  uses: stefanzweifel/git-auto-commit-action@v5
  with:
    commit_message: "update: documentación generada automáticamente"
    branch: ${{ github.ref_name }}
    file_pattern: "docs/**"
```

### Funcionamiento en Conjunto

Cuando hago un push al repositorio, GitHub Actions se activa automáticamente. El workflow clona el código, instala todas las herramientas necesarias, genera la documentación en HTML usando Javadoc, la convierte a PDF con Pandoc, y finalmente hace un commit automático con los archivos generados. Todo este proceso ocurre en los servidores de GitHub sin intervención manual.

### Ventajas de Este Sistema

La principal ventaja de este sistema es que me aseguro de que la documentación está siempre actualizada con el código. No tengo que acordarme de ejecutar comandos manualmente ni de subir archivos de documentación. Además, puedo revisar el historial de cambios de la documentación igual que reviso el historial del código.

### Validación y Detección de Errores

He incluido validaciones en cada paso crítico del proceso. Si Javadoc falla al generar la documentación, el workflow se detiene y muestra un mensaje de error. Lo mismo ocurre con la generación del PDF. Esto me permite detectar rápidamente si hay problemas en los comentarios del código o en la configuración.
