# Ejercicio Práctico: Servidor FTP con FileZilla y Cliente FTP en Java

Este documento guía a través de los pasos para configurar un servidor FTP utilizando FileZilla Server y desarrollar un cliente FTP en Java para interactuar con el servidor.

## Parte 1: Configuración del Servidor FTP con FileZilla Server

### Paso 1: Instalación de FileZilla Server

1. Descarga FileZilla Server desde el sitio oficial: [FileZilla - The free FTP solution](https://filezilla-project.org/)
2. Ejecuta el instalador y sigue las instrucciones para completar la instalación en tu máquina.

### Paso 2: Configuración del Servidor FTP

1. Abre FileZilla Server Interface y conecta al servidor local.
2. Crea un usuario administrador:
   - Ve a `Edit` > `Users`.
   - Pulsa `Add` y escribe un nombre de usuario.
   - En la pestaña `Password`, establece una contraseña para el usuario.

### Paso 3: Estructura de Carpetas y Usuarios

1. **Estructura de Carpetas:**
   - Crea una estructura de carpetas en tu sistema para representar las asignaturas del segundo año de DAM.
   - Coloca algunos archivos PDF en cada carpeta para simular los diferentes temas de las asignaturas.

2. **Creación de Usuarios:**
   - En FileZilla Server, ve a `Edit` > `Users` para crear nuevos usuarios.
   - Para cada usuario, establece permisos adecuados en la sección `Shared folders` agregando las carpetas correspondientes y ajustando los derechos de acceso.

## Parte 2: Desarrollo del Cliente FTP en Java

### Paso 1: Preparación del Entorno

1. Asegúrate de tener instalado Java JDK y un IDE como Eclipse o IntelliJ IDEA.
2. Añade la biblioteca Apache Commons Net a tu proyecto. Esta biblioteca facilita la implementación de funcionalidades FTP en Java.

### Paso 2: Desarrollo del Cliente FTP

Crea una nueva clase `ClienteFTP` en Java y sigue los pasos siguientes para implementar las funcionalidades requeridas.

```java
import org.apache.commons.net.ftp.FTPClient;
import org.apache.commons.net.ftp.FTPFile;
import org.apache.commons.net.ftp.FTPReply;
import java.io.FileOutputStream;
import java.io.OutputStream;
import java.util.Scanner;

public class ClienteFTP {

    private FTPClient ftp;
    private Scanner scanner;

    public ClienteFTP() {
        ftp = new FTPClient();
        scanner = new Scanner(System.in);
    }

    public void conectarYLoguear() {
        try {
            System.out.println("Conectando al servidor FTP...");
            ftp.connect("direccion_servidor_ftp");
            int respuesta = ftp.getReplyCode();
            if (!FTPReply.isPositiveCompletion(respuesta)) {
                System.out.println("Conexión fallida: " + respuesta);
                return;
            }
            System.out.print("Nombre de usuario: ");
            String usuario = scanner.nextLine();
            System.out.print("Contraseña: ");
            String password = scanner.nextLine();
            if (!ftp.login(usuario, password)) {
                System.out.println("Login fallido!");
                ftp.disconnect();
                return;
            }
            System.out.println("Login exitoso!");
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    public void explorarYDescargar() {
        String comando = "";
        try {
            while (!comando.equalsIgnoreCase("exit")) {
                System.out.print("Comando (ls, cd <directorio>, get <archivo>, exit): ");
                comando = scanner.nextLine();
                String[] partesComando = comando.split(" ");
                switch (partesComando[0]) {
                    case "ls":
                        listarDirectorioActual();
                        break;
                    case "cd":
                        if (partesComando.length > 1) cambiarDirectorio(partesComando[1]);
                        else System.out.println("Falta el nombre del directorio.");
                        break;
                    case "get":
                        if (partesComando.length > 1) descargarArchivo(partesComando[1]);
                        else System.out.println("Falta el nombre del archivo.");
                        break;
                }
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            cerrarConexion();
        }
    }

    private void listarDirectorioActual() throws Exception {
        FTPFile[] archivos = ftp.listFiles();
        for (FTPFile archivo : archivos) {
            System.out.println(archivo.getName());
        }
    }

    private void cambiarDirectorio(String directorio) throws Exception {
        if (!ftp.changeWorkingDirectory(directorio)) {
            System.out.println("Cambio de directorio fallido.");
        } else {
            System.out.println("Directorio cambiado a " + ftp.printWorkingDirectory());
        }
    }

    private void descargarArchivo(String nombreArchivo) throws Exception {
        try (OutputStream outputStream = new FileOutputStream(nombreArchivo)) {
            if (ftp.retrieveFile(nombreArchivo, outputStream)) {
                System.out.println("Archivo descargado: " + nombreArchivo);
            } else {
                System.out.println("Descarga fallida.");
            }
        }
    }

    private void cerrarConexion() {
        try {
            if (ftp.isConnected()) {
                ftp.logout();
                ftp.disconnect();
                System.out.println("Conexión cerrada.");
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    public static void main(String[] args) {
        ClienteFTP cliente = new ClienteFTP();
        cliente.conectarYLoguear();
        cliente.explorarYDescargar();
    }
}

## Evaluación y Observaciones

- **Servidor FTP:** La configuración debe incluir una estructura de carpetas adecuada para las asignaturas de DAM y usuarios con acceso.
- **Cliente FTP en Java:** Debe permitir la navegación por carpetas y la descarga de archivos, manejando correctamente errores y excepciones.

### Documentación y Buenas Prácticas:

- Asegúrate de que tu código esté bien comentado y siga las buenas prácticas de programación.
- Proporciona documentación clara sobre cómo ejecutar tu servidor FTP y tu cliente FTP en Java.
