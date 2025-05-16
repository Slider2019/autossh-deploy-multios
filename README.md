# 🔐 Proyecto: Automatización SSH y Despliegue Remoto Multiplataforma

![Bash](https://img.shields.io/badge/-Bash-4EAA25?logo=gnu-bash&logoColor=white)![SSH](https://img.shields.io/badge/-SSH-000000?logo=ssh)![Vagrant](https://img.shields.io/badge/-Vagrant-1563FF?logo=vagrant&logoColor=white)


## 📚 Descripción del Proyecto

Este proyecto consiste en la **configuración de un entorno automatizado** con bash scripting para ejecutar comandos de forma remota desde una máquina central (llamada `Script Box`) hacia servidores remotos (`web01`, `web02`, `web03`) utilizando **SSH**. Se configuran usuarios, permisos, conectividad y autenticación basada en contraseña y clave pública para facilitar tareas de administración remota y automatización en entornos tipo DevOps.

---

## 🗂️ Índice

<!-- Tabla de contenidos -->
<details close>
  <summary>Tabla de Contenidos</summary>
  <ol>
    <li><a href="#⚙️objetivo-del-proyecto">⚙️ Objetivo del proyecto</a></li>
    <li><a href="#🧱-topología-del-laboratorio">🧱 Topología del laboratorio</a></li>
    <li><a href="#📦-configuración-inicial-con-vagrant">📦 Configuración inicial con Vagrant</a></li>
    <li>
      <a href="#🔧-preparación-de-los-servidores">🔧 Preparación de los servidores</a>
      <ul>
        <li><a href="#🧾-asignar-nombre-de-host">🧾 Asignar nombre de host</a></li>
        <li><a href="#🗂️-editar-etchosts">🗂️ Editar /etc/hosts</a></li>
        <li><a href="#🔁-verificar-conectividad">🔁 Verificar conectividad</a></li>
        <li><a href="#🧑‍💻-crear-usuario-devops">🧑‍💻 Crear usuario devops</a></li>
        <li><a href="#🔑-permisos-sudo-sin-contraseña">🔑 Permisos sudo sin contraseña</a></li>
      </ul>
    </li>
    <li><a href="#⚠️-problemas-con-ubuntu-web03">⚠️ Problemas con Ubuntu (Web03)</a></li>
    <li><a href="#🚀-ejecución-remota-desde-script-box">🚀 Ejecución remota desde Script Box</a></li>
    <li><a href="#🔐-autenticación-ssh-con-clave-pública">🔐 Autenticación SSH con clave pública</a></li>
    <li><a href="#🧠-conceptos-clave-sobre-claves-ssh">🧠 Conceptos clave sobre claves SSH</a></li>
    <li><a href="#🧪-preparando-automatización-remota">🧪 Preparando automatización remota</a></li>
    <li><a href="#🧪-prueba-local-del-script">🧪 Prueba local del script</a></li>
    <li><a href="#🧠-automatización-con-scp-y-ssh">🧠 Automatización con <code>scp</code> y <code>ssh</code></a></li>
    <li><a href="#🛠️--pasos-técnicos-explicados">🛠️ Pasos Técnicos Explicados</a></li>
    <li><a href="#📘-lecciones-aprendidas">📘 Lecciones aprendidas</a></li>
  </ol>
</details>
<br>


---

## ⚙️ Objetivo del proyecto

Configurar una solución que permita **administrar múltiples servidores remotos** desde una única máquina controladora usando `SSH` y automatizando todo el proceso con Bash Script, primero mediante contraseña y luego usando **clave pública**. Esta práctica es esencial en entornos de automatización y DevOps.
<p align="right">(<a href="#🗂️-índice">Volver al inicio</a>)</p>

---

## 🧱 Topología del laboratorio

- 🖥️ **Script Box**: Máquina de control central.
- 🖥️ **web01 & web02**: Servidores CentOS.
- 🖥️ **web03**: Servidor Ubuntu *(opcional según recursos)*.
<p align="right">(<a href="#🗂️-índice">Volver al inicio</a>)</p>

---

## 📦 Configuración inicial con Vagrant

```bash
vagrant up
```

### IPs asignadas en el `Vagrantfile`:

Se añaden 3 máquinas virtuales en el Vagrantfile:
```text
web01 → 10.0.13.13 / 10.13.10.14
web02 → 10.0.13.14 / 10.13.10.15
web03 → 10.0.13.15 / 10.13.10.16 (Ubuntu)
```
<p align="right">(<a href="#🗂️-índice">Volver al inicio</a>)</p>

----------

## 🔧 Preparación de los servidores

### 🧾 Asignar nombre de host

Entrar a cada máquina y modificar `/etc/hostname` con:

```bash
echo "web01" > /etc/hostname
```

_Repetir para web02 y web03._

----------

### 🗂️ Editar /etc/hosts 

Desde Script Box:

```bash
sudo vim /etc/hosts
```

Añadir:

```text
10.0.13.13   web01
10.0.13.14   web02
10.0.13.15   web03
```

----------

### 🔁 Verificar conectividad

```bash
ping -c 2 web01
ping -c 2 web02
ping -c 2 web03
```

----------

### 🧑‍💻 Crear usuario devops

Crear usuario devops en cada servidor

```bash
sudo useradd devops
sudo passwd devops
```

----------

### 🔑 Permisos sudo sin contraseña

Editar el archivo sudoers:
```bash
sudo visudo
```

Añadir al final:

```text
devops ALL=(ALL) NOPASSWD:ALL
```
<p align="right">(<a href="#🗂️-índice">Volver al inicio</a>)</p>

----------

## ⚠️ Problemas con Ubuntu (Web03)

Ubuntu no permite inicio por contraseña por defecto. Solución:

1.  Editar SSH config:
    
    ```bash
    sudo vim /etc/ssh/sshd_config
    ```
    
2.  Cambiar:
    
    ```text
    PasswordAuthentication yes
    ```
    
3.  Reiniciar SSH:
    
    ```bash
    sudo systemctl restart ssh
    ```
    <p align="right">(<a href="#🗂️-índice">Volver al inicio</a>)</p>
    

----------

## 🚀 Ejecución remota desde Script Box

Con contraseña:

```bash
ssh devops@web01 uptime
```

> 👉 Esto conecta brevemente, ejecuta el comando, y vuelve a Script Box
> sin mantener la sesión abierta.
<p align="right">(<a href="#🗂️-índice">Volver al inicio</a>)</p>

----------

## 🔐 Autenticación SSH con clave pública

1.  Generar claves en Script Box:
    
    ```bash
    ssh-keygen
    ```
    
2.  Copiar clave pública a cada servidor:
    
    ```bash
    ssh-copy-id devops@web01
    ssh-copy-id devops@web02
    ssh-copy-id devops@web03
    ```
    
3.  Verificar acceso:
    
    ```bash
    ssh devops@web01 uptime
    ```
    

💡 _Ya no pedirá contraseña._
<p align="right">(<a href="#🗂️-índice">Volver al inicio</a>)</p>

----------

## 🧠 Conceptos clave sobre claves SSH

-   La **clave privada** es larga y debe mantenerse segura.
-   La **clave pública** es más corta y se distribuye.
-   🧩 Ambas se generan en pareja y funcionan como cerradura y llave.
-   Por defecto, SSH usa `~/.ssh/id_rsa` cuando haces `ssh`, lo cual equivale a:
    
    ```bash
    ssh -i ~/.ssh/id_rsa user@host
    ```
    

📌 Si la llave calza con la cerradura del servidor, ¡acceso garantizado sin contraseña!
<p align="right">(<a href="#🗂️-índice">Volver al inicio</a>)</p>

----------

## 🧪 Preparando automatización remota

### 🛠️ Crear script multiOS (websetup_multios.sh)

1. Crear el script:
    
    ```bash
	  websetup_multios.sh
    ```
    
2.  Agregar lógica para detectar OS:
    
    ```bash
    #!/bin/bash/
    
    yum --help &>/dev/null
    if [ $? -eq 0 ]; then
      echo "   Ejecutar configuración en CentOS"
      PKG="httpd"
      SVC="httpd"
      # comandos específicos CentOS
      sudo yum install $PKG -y
      sudo systemctl start $SVC
    else
      echo "   Ejecutar configuración en Ubuntu"
      PKG="apache2"
      SVC="apache2"
      # comandos específicos Ubuntu
      sudo apt update
      sudo apt install $PKG -y
      sudo systemctl start $SVC
    fi
    ```
    

✅ Gracias al uso de variables (`$PKG`, `$SVC`), podemos reutilizar comandos.
<p align="right">(<a href="#🗂️-índice">Volver al inicio</a>)</p>

----------

## 🧪 Prueba local del script

```bash
bash websetup_multios.sh
```

✔️ En la máquina local (CentOS), el script ejecutará el bloque para CentOS.
<p align="right">(<a href="#🗂️-índice">Volver al inicio</a>)</p>

## 🧠 Automatización con `scp` y `ssh`

### 🔍 **Objetivos**

Automatizar el despliegue y ejecución de scripts en múltiples máquinas remotas Linux (Web01, Web02, Web03) usando:

-   `scp` (secure copy) para transferir archivos
-   `ssh` para ejecutar comandos remotamente

----------

## 🛠️ **Pasos Técnicos Explicados**

### 📁 1. **Transferencia de Archivos con `scp`**

```bash
scp testfile.txt devops@<ip_del_host>:/tmp/
```

-   ✅ `scp` utiliza el mismo protocolo que `ssh`, por lo que si tienes claves configuradas, no pedirá contraseña.
-   ❌ Si intentas copiar al directorio `/root`, verás un error de “Permiso denegado” si no usas `sudo` o no eres `root`.

### 🔁 2. **Estructura del Script de Despliegue Remoto**

#### 📄 Nombre del archivo: `web_deploy.sh`

```bash
#!/bin/bash

USR=devops

for host in $(cat remhosts); 
do
  echo "🔗 Conectando a $host..."
  echo "📤 Enviando script a $host..."

  scp websetup_multios.sh $USR@$host:/tmp/

  echo "🚀 Ejecutando script en $host..."
  ssh $USR@$host "sudo bash /tmp/websetup_multios.sh"

  echo "🧹 Eliminando script temporal de $host..."
  ssh $USR@$host "rm -f /tmp/websetup_multios.sh"

  echo "✅ Completado en $host"
  echo "------------------------------------"
done
```

📌 Este script:

-   Lee una lista de hosts desde el archivo `remhosts`
-   Copia el script `websetup_multios.sh` al directorio `/tmp/` de cada host
-   Lo ejecuta con `sudo` usando `ssh`
-   Luego limpia archivos temporales tras la ejecución
<p align="right">(<a href="#🗂️-índice">Volver al inicio</a>)</p>

----------

### 🔑 **Importancia del uso de `sudo`**

-   Como el script realiza tareas administrativas (instalaciones, servicios), se necesita ejecutar como `root`.
-   Puedes usar `sudo` directamente en el comando o dentro del script.

----------

### 🔎 **Validación y Verificación**

Una vez ejecutado el script:

-   Se accede a los servidores por IP desde el navegador para validar que el despliegue fue exitoso.
-   Todos los servidores (Web01, Web02, Web03) respondieron correctamente, incluso con diferentes sistemas operativos (`yum` vs `apt`).

----------

## 📘 Lecciones aprendidas

Durante este proyecto, pude reforzar y aplicar varios conceptos fundamentales de administración y automatización de sistemas:

-   ✅ Uso práctico de **Vagrant** para crear entornos reproducibles.
    
-   ✅ Profundización en **SSH**, tanto con autenticación por contraseña como con clave pública.
    
-   ✅ Gestión de usuarios, permisos `sudo` y edición de configuraciones críticas del sistema.
    
-   ✅ Comprensión de diferencias entre sistemas operativos (CentOS vs Ubuntu).
    
-   ✅ Ejecución remota de comandos, base para herramientas como Ansible o scripts Bash automatizados.
    
-   ✅ Introducción a la lógica condicional para manejar tareas en múltiples hosts.
    

🧠 **Este proyecto fue una base perfecta para entender la automatización en ambientes DevOps. Me dio confianza para avanzar hacia herramientas más avanzadas como Ansible, CI/CD y orquestación.**
<p align="right">(<a href="#🗂️-índice">Volver al inicio</a>)</p>

----------
> 💬 💡 _"Este tipo de scripting representa la base de muchas herramientas de automatización como Ansible. Aprender a hacerlo a mano es crucial para entender lo que ocurre bajo el capó."_

----------

📌 _Repositorio creado como parte de mi formación DevOps. Listo para escalar hacia soluciones más robustas con herramientas como Ansible, Terraform y CI/CD._

----------

🚀 **Si, has llegado hasta aquí, ¡Gracias por leer!. Si te interesa ver el código o probarlo, clona el repo y comienza tu propia automatización y si tienes alguna consulta o duda, enviame un mensaje privado por linkedin**
<p align="right">(<a href="#🗂️-índice">Volver al inicio</a>)</p>
<br>
<br>

## 📬 Contacto

Enlace a Linkedin
[![LinkedIn](https://img.shields.io/badge/-LinkedIn-0077B5?logo=linkedin)](https://www.linkedin.com/in/diegorojasv/)
