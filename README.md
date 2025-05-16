# 🔐 Proyecto: Automatización SSH y Despliegue Remoto Multiplataforma

Automatización de autenticación SSH y despliegue de configuraciones en múltiples servidores Linux (CentOS y Ubuntu) utilizando scripts en Bash, `scp` y `ssh`, como base para la gestión de infraestructura remota. Este proyecto sienta las bases para herramientas más avanzadas como Ansible, demostrando comprensión de conceptos clave y habilidades de automatización DevOps.

---

## 🧭 Índice

1. [🗝️ Autenticación SSH con clave pública](#🗝️-autenticación-ssh-con-clave-pública)
2. [🧠 Conceptos clave sobre claves SSH](#🧠-conceptos-clave-sobre-claves-ssh)
3. [🧪 Preparando automatización remota](#🧪-preparando-automatización-remota)
4. [🔁 Ejecutar comandos remotos con bucle `for`](#🔁-ejecutar-comandos-remotos-con-bucle-for)
5. [⚙️ Instalación remota de software (YUM vs APT)](#⚙️-instalación-remota-de-software-yum-vs-apt)
6. [🛠️ Creando script multios (`websetup_multios.sh`)](#🛠️-creando-script-multios)
7. [🧪 Prueba local del script](#🧪-prueba-local-del-script)
8. [📦 Despliegue remoto automatizado (`web_deploy.sh`)](#📦-despliegue-remoto-automatizado)
9. [🎓 Lecciones aprendidas](#🎓-lecciones-aprendidas)

---

## 🗝️ Autenticación SSH con clave pública

### 🔐 Problema

Cada vez que ejecutamos `ssh`, se solicita la contraseña. Esto interrumpe procesos automatizados.

### ✅ Solución

Implementar **autenticación basada en clave pública**, más segura y automatizable.

### 📌 Pasos

1. **Generar claves:**

```bash
ssh-keygen
```

Pulsa `Enter` para aceptar el nombre por defecto y omitir passphrase.

2.  **Archivos generados:**
    

-   🔑 `~/.ssh/id_rsa`: clave **privada**
    
-   🏷️ `~/.ssh/id_rsa.pub`: clave **pública**
    

3.  **Copiar clave pública a servidores:**
    

```bash
ssh-copy-id devops@web01
ssh-copy-id devops@web02
ssh-copy-id devops@web03
```

4.  **Verificar acceso sin contraseña:**
    

```bash
ssh devops@web01
```

----------

## 🧠 Conceptos clave sobre claves SSH

-   🔒 La **clave privada** debe permanecer protegida.
    
-   📤 La **clave pública** puede distribuirse libremente.
    
-   🔄 Ambas se generan juntas y funcionan como cerradura y llave.
    
-   Por defecto, SSH usa:
    

```bash
ssh -i ~/.ssh/id_rsa user@host
```

----------

## 🧪 Preparando automatización remota

### 🎯 Objetivo

Configurar múltiples servidores remotos desde una máquina central mediante Bash scripting.

### 🗂️ Organización

1.  Crear carpeta de trabajo:
    

```bash
mkdir remote_websetup
cd remote_websetup
```

2.  Crear archivo `hosts`:
    

```bash
web01
web02
web03
```

----------

## 🔁 Ejecutar comandos remotos con bucle `for`

```bash
for host in $(cat hosts); do
  echo $host
done
```

👨‍💻 Comandos útiles dentro del bucle:

```bash
ssh devops@$host hostname
ssh devops@$host free -m
```

----------

## ⚙️ Instalación remota de software (YUM vs APT)

Problema:

```bash
ssh devops@$host sudo yum install git -y
```

❌ Falla en `web03` (Ubuntu). Necesitamos distinguir el SO.

----------

## 🛠️ Creando script multiOS

1.  Copiar script base:
    

```bash
cp 3_vars_websetup.sh websetup_multios.sh
```

2.  Añadir lógica de detección de OS:
    

```bash
yum --help &>/dev/null
if [ $? -eq 0 ]; then
  echo "   Ejecutar configuración en CentOS"
  PKG="httpd"
  SVC="httpd"
  sudo yum install $PKG -y
  sudo systemctl start $SVC
else
  echo "   Ejecutar configuración en Ubuntu"
  PKG="apache2"
  SVC="apache2"
  sudo apt update
  sudo apt install $PKG -y
  sudo systemctl start $SVC
fi
```

✅ Uso de variables reutilizables: `$PKG`, `$SVC`

----------

## 🧪 Prueba local del script

```bash
bash websetup_multios.sh
```

✔️ Ejecuta la instalación dependiendo del sistema operativo local.

----------

## 📦 Despliegue remoto automatizado

### 🔍 Objetivo

Automatizar la transferencia y ejecución de scripts en múltiples servidores remotos.

### 📁 Transferencia de archivos con `scp`

```bash
scp testfile.txt devops@<ip>: /tmp/
```

✅ Usa claves SSH → no requiere contraseña  
❌ No copiar a `/root` sin permisos

### 📄 Script de despliegue: `web_deploy.sh`

```bash
#!/bin/bash

USR=devops

for host in $(cat remhosts); do
  echo "🔗 Conectando a $host..."
  echo "📤 Enviando script a $host..."
  scp multios_websetup.sh $USR@$host:/tmp/

  echo "🚀 Ejecutando script en $host..."
  ssh $USR@$host "sudo bash /tmp/multios_websetup.sh"

  echo "🧹 Eliminando script temporal de $host..."
  ssh $USR@$host "rm -f /tmp/multios_websetup.sh"

  echo "✅ Completado en $host"
  echo "------------------------------------"
done
```

📌 Pasos del script:

-   Lee servidores desde `remhosts`
    
-   Copia y ejecuta el script remotamente
    
-   Limpia archivos temporales tras la ejecución
    

### 🔑 Uso de `sudo`

El script realiza tareas administrativas (instalaciones, servicios).  
Por eso, requiere permisos elevados.

----------

## 🎓 Lecciones aprendidas

🔧 Este proyecto fue una experiencia completa de scripting y automatización remota. Aprendí a:

-   Configurar autenticación SSH sin contraseña
    
-   Automatizar configuraciones con bucles Bash
    
-   Detectar sistemas operativos en scripts
    
-   Reutilizar código con variables bien estructuradas
    
-   Transferir y ejecutar scripts con `scp` y `ssh`
    
-   Validar resultados en entornos heterogéneos (Ubuntu y CentOS)
    
-   Sentar bases sólidas para herramientas de configuración modernas como **Ansible**
    

> 💬 💡 _"Este tipo de scripting representa la base de muchas herramientas de automatización como Ansible. Aprender a hacerlo a mano es crucial para entender lo que ocurre bajo el capó."_

----------

📌 _Repositorio creado como parte de mi formación DevOps. Listo para escalar hacia soluciones más robustas con herramientas como Ansible, Terraform y CI/CD._

----------

🚀 **Si, has llegado hasta aquí, ¡Gracias por leer!. Si te interesa ver el código o probarlo, clona el repo y comienza tu propia automatización.**
