---
title: Desarrollo de una plantilla basica en Terraform
date: 2023-02-27 12:00:00 +0800
categories: [Terraform, Plantilla Basica]
tags: [Terraform]
math: true
mermaid: true
---

Para comenzar a usar Terraform vamos a desarrollar una estructura basica de lo que podria ser una red de VPS compleja.

Para ello lo primero que realizaremos sera crearno una carpeta con le nombre que nosotros queramos:

```bash
mkdir /home/$USER/PycharmProjects/vps-alejandroalsa
```

El nombre de igual, ustedes pueden pornerle el que quieran.

Antes de poder empezar a escribir el codigo es necesario incializar la palntilla para que terraform cree los archivos necesarios, para ello ejecutaremos el siguiente comando:

```bash
terraform init
```

Esto nos dejara con la siguiente estructura de directorios:

```
├── vps-alejandroalsa
│   ├── main.tf
│   ├── .terraform
│   │   └── providers
│   │       └── registry.terraform.io
│   │           └── hashicorp
│   │               └── aws
│   │                   └── 4.56.0
│   │                       └── linux_amd64
│   │                           └── terraform-provider-aws_v4.56.0_x5
│   └── .terraform.lock.hcl
```

Los archivos ocultos nos dan igual, solo nos tenemos que centrar por ahora en el `main.tf`.

## Estructura Basica del **main.tf**

```tf
terraform {
  required_version = ">= 1.3.9"
}

provider "aws" {
  region = "eu-west-3"
  allowed_account_ids = ["tu_id_cuenta"]
  profile = "alejandroalsa"
}

resource "aws_vpc" "vpc" {
  cidr_block           = "10.10.10.0/24"
  enable_dns_hostnames = true
  enable_dns_support   = true
  tags = {
    Name = "vpc-alejandroalsa"
  }
}
```
{: file='vps-alejandroalsa/main.tf'}

```tf
terraform {
  required_version = ">= 1.3.9"
}
```
{: file='vps-alejandroalsa/main.tf'}

En esta linea especificamos que la version no puede ser mayo o igual que la `1.3.9`

```tf
provider "aws" {
  region = "eu-west-3"
  allowed_account_ids = ["tu_id_cuenta"]
  profile = "alejandroalsa"
}
```
{: file='vps-alejandroalsa/main.tf'}

Definimos la proveedor aws, donde la regios es **Paris**, con un ID de cuenta igual a **X** ([Ver mi ID de AWS](https://us-east-1.console.aws.amazon.com/billing/home#/account)) y un perfil predefinido con el nombre **alejandroalsa**, recuerde que este es el prefil que definiamos en el AWS CLI.

```tf
resource "aws_vpc" "vpc" {
  cidr_block           = "10.10.10.0/24"
  enable_dns_hostnames = true
  enable_dns_support   = true
  tags = {
    Name = "vpc-alejandroalsa"
  }
}
```
{: file='vps-alejandroalsa/main.tf'}

Definimos el recurso **aws_vpc** con el nombre **vpc**. Una VPC o Amazon Virtual Private Cloud es un servicio de computación en la nube comercial que brinda a los usuarios una nube privada virtual, al "provisionar una sección lógicamente aislada de la nube de Amazon Web Services". Despues definimos la direccion de red, en mi caso **10.10.10.0/24**, donde habilitamos los nombres de host DNS y los soportes DNS, por ultimo le asignamos un nombre.

## Ejecucion de una archivo terraform

Cuando ya tenemos todo el codigo de terraform creado, el siguiente paso como es logico, sera su ejecución, para ello antes de lanzarlo podemos hacer una buena practica como la revision de este mismo con el comando:

```bash
terraform plan
```

Cuando se ejecuta este comando, Terraform analiza los archivos de configuración de la infraestructura y calcula qué cambios deben realizarse para que la infraestructura refleje la configuración deseada.

En lugar de realizar los cambios de inmediato, Terraform muestra un plan de lo que se realizará y solicita confirmación antes de aplicar los cambios.

Si todas las acciones que nos ha definido `terraform paln` son las correctas lanzaremos el archivo de terraform con el comando:

```bash
terraform init
```

![aws_vpc](https://user-images.githubusercontent.com/67869168/221626527-9877b065-2cad-45bf-9a78-55f20cadc11c.png)

## Ficheros tfstate

El archivo de estado de terraform, por defecto, se llama `terraform.tfstate` y se encuentra en el mismo directorio donde se ejecuta Terraform. Se crea después de ejecutar `terraform apply` . El contenido real de este archivo es una asignación en formato `JSON` de los recursos definidos en la configuración y los que existen en su infraestructura.

Si nosotros realizáramos alguna modificación en nuestro `.tf`, terraform creara un nuevo fichero `terraform.tfstate` y otro fichero nuevo llamado `terraform.tfstate.backup` que guardara la configuración anterior a la última ejecución, esto lo hace por seguridad, por si en algún momento queremos restablecer los valores a unos anteriores.

## Variables

Las variables en Terraform se utilizan para definir valores que se utilizan en el código de infraestructura. Estas variables pueden ser utilizadas para definir diferentes aspectos de la infraestructura, como el número de instancias de un recurso, la dirección IP, el nombre de un recurso, etc.

Las variables son útiles para hacer que la configuración de Terraform sea más flexible y adaptable a diferentes entornos, ya que pueden ser utilizadas para definir diferentes valores en función de la situación. Por ejemplo, se pueden utilizar variables para definir diferentes configuraciones en función del entorno (por ejemplo, desarrollo, pruebas, producción).

Además, las variables también son útiles para evitar la duplicación de código y reducir el tiempo de desarrollo. Al utilizar variables, se pueden definir valores una vez y reutilizarlos en diferentes partes del código, lo que ahorra tiempo y reduce la posibilidad de errores.

Para comenzar a utiliar variables crearemos el archivo `variables.tf`, donde en este definiremos los siguientes datos:

```tf
variable "region" {
  type = string
  default = "eu-west-3"
}

variable "id_cuenta" {
  type = string
  default = "613801167036"
}

variable "perfil_cli" {
  type = string
  default = "alejandroalsa"
}

variable "dir_ip_01" {
  type = string
  default = "10.10.0.0/16"
}
```
{: file='vps-alejandroalsa/variables.tf'}

Como puede apreciar los valores son los mismos la unica diferencia esta en la direccion IP y en el uso de variables, veamos ahora como llamamos a las variables en el `main.tf`

```tf
terraform {
  required_version = ">= 1.3.9"
}

provider "aws" {
  region = var.region
  allowed_account_ids = [var.id_cuenta]
  profile = var.perfil_cli
}

resource "aws_vpc" "vpc" {
  cidr_block           = var.dir_ip_01
  enable_dns_hostnames = false
  enable_dns_support   = false
  tags                 = {
    Name = "vpc-alejandroalsa-02"
  }
}
```
{: file='vps-alejandroalsa/main.tf'}

Para poder llamar a las variables en Terraform vastara con definir una variable en un archivo y llamarla con la instruccion `var.nombre_variable`.

## Creacion de una instancia EC2 + Outputs

Una instancia EC2 es un tipo de recurso en la nube de Amazon Web Services (AWS) que permite a los usuarios crear y ejecutar servidores virtuales en la nube. Una instancia EC2 es una unidad de cómputo en la nube que se ejecuta en una máquina física alojada en uno de los centros de datos de AWS.

Las instancias EC2 son altamente personalizables y escalables, lo que significa que los usuarios pueden seleccionar diferentes tipos de instancias que se ajusten a sus necesidades, como el tamaño de la memoria, la capacidad de procesamiento, el almacenamiento y la conectividad de red. Además, los usuarios pueden ejecutar diferentes sistemas operativos en las instancias EC2, como Amazon Linux, Windows Server y Ubuntu.

Los Outputs en Terraform son una característica que permite obtener información de la infraestructura creada después de la ejecución del código de Terraform. Los Outputs se definen en el archivo de configuración de Terraform y se utilizan para obtener información útil sobre los recursos creados.

Los Outputs en Terraform son similares a las variables, pero a diferencia de las variables, los Outputs no se utilizan para definir valores utilizados en la infraestructura, sino que se utilizan para obtener información de la infraestructura creada. Los Outputs pueden ser valores simples como una dirección IP, una URL o una cadena de texto, o pueden ser estructuras de datos más complejas como listas, mapas, etc.

El codigo de variables que utilizaremos para crear una instancia EC2 sera el siguiente:

```tf
variable "id_ami" {
  description = "Identificador de la AMI"
  type        = string
  default     = "ami-00874d747dde814fa"
}

variable "tipo_instancia" {
  description = "Tipo de instancia"
  type        = string
  default     = "t2.micro"
}

variable "nombre_clave" {
  description = "Nombre de la clave pública"
  type        = string
  default     = "nombre_clave"
}

variable "nombre_instancia_ec2" {
  description = "Nombre de la instancia"
  type        = string
  default     = "Server 01"
}
```
{: file='vps-alejandroalsa/variables.tf'}

Por aqui les dejo una lista los tipos de [Instancias](https://instances.vantage.sh/?selected=c6in.large,r6gd.large) y [amis](https://cloud-images.ubuntu.com/locator/ec2/) de AWS.

Despues en el `main.tf` solo tendremos que definir las variables en un bloque:

```tf
resource "aws_instance" "Server_01" {
  ami             = var.id_ami
  instance_type   = var.tipo_instancia
  key_name        = var.nombre_clave
  tags = {
    Name = var.nombre_instancia_ec2
  }
}
```
{: file='vps-alejandroalsa/main.tf'}

*   **ami**: Este es el ID de la imagen AMI de la instancia que se utilizará para lanzar la instancia. El valor de este atributo se obtiene de la variable `id_ami`.
*   **instance_type**: Este es el tipo de instancia que se utilizará para la instancia EC2. El valor de este atributo se obtiene de la variable `tipo_instancia`.
*   **key_name**: Este es el nombre de la clave de AWS que se utilizará para acceder a la instancia. El valor de este atributo se obtiene de la variable `nombre_clave`.
*   **tags**: Este es un mapa de etiquetas que se aplicarán a la instancia. En este caso, se especifica una etiqueta "Name" con el valor de la variable `nombre_instancia_ec2`.

Para comenzar a utiliar outputs crearemos el archivo `output.tf`, donde en este definiremos los siguientes datos:

```tf
output "server-ip" {
  value = aws_instance.Server_01.public_ip
}
```
{: file='vps-alejandroalsa/output.tf'}

En este caso estamos definiendo que al final de la ejecucion del archivo muestre la direccion IP publica de la instancia EC2. 

Aquie les dejor una enlace a la web de Terraform para ver todas las opciones de outputs posibles: [Outputs](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/instance#attributes-reference)

Tambiem podemos ejecutar el comando `terraform output` para ver los outputs definidos:

```bash
terraform output
```

## Data Sources

Los Data Sources en Terraform son una forma de obtener información de recursos existentes en una infraestructura de nube y utilizarla en la configuración de Terraform. A diferencia de los recursos, que se utilizan para crear, modificar o eliminar objetos en una infraestructura de nube, los Data Sources se utilizan para consultar y recuperar información de los recursos existentes.

Los Data Sources son útiles en situaciones en las que es necesario acceder a información existente en una infraestructura de nube, como por ejemplo:

*   Obtener la información de configuración de un grupo de seguridad.
*   Obtener información sobre una instancia de base de datos RDS que ya existe.
*   Consultar el estado de una instancia EC2 ya creada.

Para utilizar un Data Source en Terraform, primero se define el Data Source y se especifica la información necesaria para identificar el recurso que se va a consultar. Luego, se utiliza la información devuelta por el Data Source en la configuración de otros recursos o módulos de Terraform.

Por ejemplo, supongamos que se tiene una instancia EC2 que ya existe en una cuenta de AWS, y se necesita obtener su dirección IP pública para utilizarla en la configuración de otros recursos. Para hacerlo, se podría utilizar el siguiente Data Source:

```tf
data "aws_instance" "instancias_disponibles" {
  instance_id = "i-0123456789abcdef0"
}

output "server-ip" {
  value = data.aws_instance.instancias_disponibles.public_ip
}
```
{: file='vps-alejandroalsa/output.tf'}

Aquie les dejor una enlace a la web de Terraform para ver todas las opciones de Data Sources posibles: [Data Sources](https://developer.hashicorp.com/terraform/language/data-sources)

## Ejecuccion de Scripts

Cuando nosotros instalamos cualquier tipo de S.O siempre hay una seria de pasos que hay que realizar para poner en marcha el Servidor, por ejemplo la actualización y instalación de paquetes, servicios, configuraciones, etc.

En Terraform nos dan la opcion de ejecutar estos comandos o scripts al finalizar la instalacion de las instancias, por ejemplo definamos un Script para la actualización y instalación de paquetes y comandso o software basico para un Ubuntu Server.

Lo primero que tendremos que hacer es la creacion de la carpeta `scripts`, en el directorio:

```bash
mkdir /home/$USER/PycharmProjects/vps-alejandroalsa/scripts
```

Despues en el crearemos un Script donde definiremos las instruccione que necesitemos, en mi caso, creare un Script para la configuracion basica de un Ubuntu Server:

```bash
touch script.sh
```

```bash
#!/bin/bash

# Actualizar los repositorios
sudo apt update && sudo apt upgrade -y

# Instalar paquetes básicos
sudo apt-get install openssh-server zip unzip tree net-tools iftop git sshpass -y
```
{: file='vps-alejandroalsa/scripts/script.sh'}

Despues en nuestro `main.tf` solo tendremos que anadir la instruccion para implementar y ejecutar el Script:

```tf
resource "aws_instance" "Server_01" {
  ami             = var.id_ami
  instance_type   = var.tipo_instancia
  key_name        = var.nombre_clave
  
  user_data = file("scripts/script.sh")
  
  tags = {
    Name = var.nombre_instancia_ec2
  }
}
```
{: file='vps-alejandroalsa/main.tf'}
