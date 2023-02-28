---
title: Terraform Introducción
date: 2023-02-27 12:00:00 +0800
categories: [Terraform, Introducción]
tags: [Terraform]
math: true
mermaid: true
---

## ¿Qué es Terraform?

Terraform es una herramienta de infraestructura como código `(IaC)` que permite a los equipos de desarrollo y operaciones definir, crear y administrar de manera eficiente la infraestructura de la nube en un formato declarativo.

Con Terraform, los usuarios pueden describir la infraestructura que necesitan en un archivo de configuración, que luego se utiliza para crear y mantener de manera automatizada y reproducible los recursos de la infraestructura en varios proveedores de la nube, como Amazon Web Services `(AWS)`, Microsoft Azure, Google Cloud Platform `(GCP)`, entre otros.

La ventaja de Terraform es que permite la gestión de la infraestructura como código, lo que significa que se puede versionar, probar, revisar y mantener la infraestructura como se hace con el código de una aplicación, lo que facilita la colaboración y la automatización. Además, Terraform se integra con otros servicios y herramientas de gestión de la nube, lo que hace que sea fácilmente escalable y adaptable a diferentes necesidades y entornos de trabajo.

## ¿Qué es pycharm?

PyCharm es un entorno de desarrollo integrado (IDE) diseñado específicamente para el lenguaje de programación Python. Es una herramienta de software creada por la compañía JetBrains y cuenta con una amplia variedad de características que facilitan la escritura, depuración y mantenimiento de código Python.

Algunas de las características más notables de PyCharm incluyen la capacidad de autocompletar código, depurar errores de forma efectiva, refactorizar código, integración con herramientas de control de versiones como Git y SVN, así como la capacidad de trabajar con diferentes frameworks de Python, incluyendo Django, Flask, Pyramid y web2py.

PyCharm también tiene una amplia variedad de plugins y complementos que se pueden agregar para extender su funcionalidad, y está disponible en versiones gratuitas y de pago, con esta última incluyendo características adicionales para proyectos más grandes y complejos.

## Instalación de Terraform

Para la instalación de Terraform podemos ir a la documentación oficial y seguirla : [terraform.io](https://www.terraform.io/). En mi caso les dejare en este POST los pasos a seguir para intalar Terraform en linux.
Algunas de las características más notables de PyCharm incluyen la capacidad de autocompletar código, depurar errores de forma efectiva, refactorizar código, integración con herramientas de control de versiones como Git y SVN, así como la capacidad de trabajar con diferentes frameworks de Python, incluyendo Django, Flask, Pyramid y web2py.

### Ubuntu/Debian

```bash
wget -O- https://apt.releases.hashicorp.com/gpg | gpg --dearmor | sudo tee /usr/share/keyrings/hashicorp-archive-keyring.gpg
```
```bash
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
```
```bash
sudo apt update && sudo apt install terraform
```

### CentOS/RedHat

```bash
sudo yum install -y yum-utils
```
```bash
sudo yum-config-manager --add-repo https://rpm.releases.hashicorp.com/RHEL/hashicorp.repo
```
```bash
sudo yum -y install terraform
```

Después de su instalación de Terraform podemos comprobar su versión.

```bash
terraform -v
```


## Instalación de pycharm

Descarga el archivo de instalación de PyCharm desde la página web oficial de JetBrains [jetbrains.com](https://www.jetbrains.com/pycharm/download/). Asegúrase de seleccionar la versión adecuada para tu distribución de Linux.

Descomprima el archivo de instalación usando el siguiente comando:

```bash
tar xfz pycharm-<version>.tar.gz
```

Luego, navegue hasta el directorio "bin" dentro del directorio descomprimido:

```bash
cd pycharm-<version>/bin
```

Modifique los permisos de la carpeta para que pueda ejecutarlos con su usuario

```bash
sudo chown usuario:grupo_usuario pycharm-community-2022.3.2/ -R
```

Ejecute el archivo "pycharm.sh" usando el siguiente comando:

```bash
./pycharm.sh
```

Si ha seguido todos los pasos al ejecutar el Script se encontrará con una pantalla parecida a esta:

![pycharm_panel](https://user-images.githubusercontent.com/67869168/221543333-6a9f7d2a-077a-4f97-8680-9fd108f6cce0.png)

Por último solo nos quedará agregar el Plugin de Terraform, para ello nos dirigiremos a la sección de Plugins y Buscaremos Terraform:

![pycharm_plugin_terraform](https://user-images.githubusercontent.com/67869168/221543631-a05132c1-aa09-405a-99ca-2ae2e3ce73a4.png)
