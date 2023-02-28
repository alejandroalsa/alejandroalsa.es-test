---
title: Terraform Providers
date: 2023-02-27 12:00:00 +0800
categories: [Terraform, Providers]
tags: [Terraform]
math: true
mermaid: true
---

## ¿Qué son los Providers en Terraform?

En Terraform, los `providers` son plugins que permiten a Terraform interactuar con diferentes APIs y servicios de proveedores de nube, como AWS, Google Cloud Platform, Microsoft Azure, entre otros.

Un `provider` de Terraform define los recursos que se pueden crear, modificar y eliminar utilizando Terraform, y proporciona la lógica necesaria para interactuar con el API correspondiente. Por ejemplo, el proveedor de AWS de Terraform proporciona recursos para crear instancias EC2, grupos de seguridad, balanceadores de carga, entre otros.

Los `providers` de Terraform se descargan e instalan automáticamente en el equipo donde se ejecuta Terraform cuando se especifican en un archivo de configuración de Terraform. Estos archivos de configuración también indican a Terraform cómo interactuar con los recursos del proveedor utilizando la API del proveedor.

Algunos proveedores populares de Terraform incluyen AWS, Azure, Google Cloud Platform, Kubernetes, Docker, entre otros. Puede consultar la lista completa en la web de [terraform.io](https://registry.terraform.io/browse/providers)

## AWS Provider

Ahora les mostrare los pasos que necesitan seguir para realizar la integracion del Provider de AWS en Terraform.

Como es logico lo primero que tendremos que hacer es registrarnos en AWS, para ello nos dirigiremos a la siguiente que y completaremos el registro y los pasos necesarios para crearno una cuenta en [AWS](https://portal.aws.amazon.com/billing/signup#/start/email).

## AWS IAM-V2

Una vez completado el registro no dirigiremos a la zona de usuario en nuestra consola de AWS y anadiremos un usuario, como se muestra en la imagen:

![aws_panel_usuarios](https://user-images.githubusercontent.com/67869168/221550923-a7453efe-b5b0-437f-b41b-ed94f4b08ebe.png)
![aws_panel_usuarios](https://user-images.githubusercontent.com/67869168/221551373-c973be63-b07e-4dc7-aa87-c53a331c315a.png)

Por último en la sección de permisos tendremos que adjuntar de forma directa algunas políticas, como la de acceso de administrador, esta nos proporciona acceso completo a los servicios y recursos de AWS.

![aws_panel_usuarios_permisos](https://user-images.githubusercontent.com/67869168/221552441-42331333-8771-490a-932a-61f2fc72bf7e.png)

Para poder empezar a trabajar con este usuario IAM que acabamos de crear necesitamos agregarle una clave de acceso, para ello en la seccion de usuarios, seleccionamos el usuario y nos movemos a la pestana de Credenciales de Seguridad, alli crearemos la clave de acceso:

![aws_panel_usuarios_clave_acceso](https://user-images.githubusercontent.com/67869168/221553483-5d9c578b-97c3-4fb7-9805-753af38fac06.png)

En este caso para interactual con el usuario y poder utilizarlo utilizaremos el CLI de AWS que mas abajo muestro como instalarlo:

![aws_panel_usuarios_cli](https://user-images.githubusercontent.com/67869168/221553944-3d6ebd44-f276-4e1d-8005-5962f69e905e.png)

Si lo desemos le anadiremos una descripcion a la clave de acceso y despues finalizaremos el proceso. Al finalizar AWS nos mostrara la Clave de Acceso y la Clave de Aceso Secreta:

![Añadir un aws_panel_usuarios_clave_acceso](https://user-images.githubusercontent.com/67869168/221554561-0072020f-6df4-4673-964a-a803ca3c5512.png)

## AWS CLI Instalacion

AWS CLI (Command Line Interface) es una herramienta de línea de comando que permite a los desarrolladores y administradores de sistemas interactuar con los servicios de Amazon Web Services (AWS) mediante la ejecución de comandos en la terminal.

AWS CLI ofrece una interfaz de línea de comando unificada para interactuar con diversos servicios de AWS, lo que permite a los usuarios crear y administrar recursos de AWS, así como automatizar tareas comunes de administración de sistemas y aplicaciones en la nube de AWS.

Con AWS CLI, los usuarios pueden interactuar con los servicios de AWS utilizando comandos simples, lo que les permite integrar fácilmente los servicios de AWS en scripts, programas y herramientas de terceros.

**Requisitos de instalación y actualización**

*   Debe poder extraer o "descomprimir" el paquete descargado. Si su sistema operativo no tiene el comando incorporado unzip , use un equivalente.
*   La CLI de AWS utiliza `glibc`, `groffy less`. Estos se incluyen de forma predeterminada en la mayoría de las principales distribuciones de Linux.
*   Admitimos AWS CLI en versiones de 64 bits de distribuciones recientes de CentOS, Fedora, Ubuntu, Amazon Linux 1, Amazon Linux 2 y Linux ARM.
*   Debido a que AWS no mantiene repositorios de terceros, no podemos garantizar que contengan la versión más reciente de la CLI de AWS.

**Instalar o actualizar la CLI de AWS**

Para actualizar su instalación actual de AWS CLI, descargue un nuevo instalador cada vez que actualice para sobrescribir las versiones anteriores. Siga estos pasos desde la línea de comandos para instalar la CLI de AWS en Linux.

Proporcionamos los pasos en un grupo fácil de copiar y pegar en función de si usa Linux de 64 bits o Linux ARM. Consulte las descripciones de cada línea en los pasos siguientes.

```bash
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
```
```bash
unzip awscliv2.zip
```
```bash
sudo ./aws/install
```

> **NOTA**
Para actualizar su instalación actual de AWS CLI, agregue su enlace simbólico existente y la información del instalador para construir el installcomando con el --update parámetro.
```bash
sudo ./aws/install --bin-dir /usr/local/bin --install-dir /usr/local/aws-cli --update
```
{: .prompt-info }

Después podemos comprobar la versión instalada ejecutando `aws --version`

## Implementation de la IAM-V2 en AWS CLI

Al realizar la instalación del AWS CLI, se nos habrá creado una `.aws` en `/home/$USER/.aws/`{: .filepath}., dentro de esta nos encontraremos con dos archivos: `config` y `credentials`.
El archivo que tendremos que modificar será el de `credentials`:

```bash
sudo nano /home/$USER/.aws/credentials
```

Nos encontraremos con algo parecido a esto donde en `aws_access_key_id` añadiremos la `ID` de la clave y en `aws_secret_access_key` la clave de acceso secreta: 

```yml
[alejandroalsa]
aws_access_key_id=***************
aws_secret_access_key=***************
```

Para comprobar que todos los proceso y pasos son correctos, ejecutaremos el siguiente comando para listar las instancias `EC2` que tengamos:

```bash
aws ec2 describe-instances --profile alejandroalsa
```

Si las claves son correctas y no tenemos ninguna instancia creada, nos devolverá:

```
{
    "Reservations": []
}
```

En el caso de que alguna de las claves no sean las correctas o cualquier otro tipo de error devolverá:

```
An error occurred (AuthFailure) when calling the DescribeInstances operation: AWS was not able to validate the provided access credentials
```

También es importante comprobar la región donde esté configurado nuestro CLI y las EC2 creadas.

En el caso de que queremos editar la región del CLI tendremos que editar el archivo `config`:

```bash
sudo nano /home/$USER/.aws/config
```

En él tendremos que especificar la región:

```yml
[alejandroalsa]
region = eu-west-3
```

Para ver la región de nuestro AWS, solo tendremos que consultarlo en el panel.

![aws_panel_region](https://user-images.githubusercontent.com/67869168/221561705-5acb1afa-ff01-4f97-bb63-8d4cd9264c8e.png)
