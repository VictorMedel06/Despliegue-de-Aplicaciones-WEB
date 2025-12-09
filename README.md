# Despliegue-de-Aplicaciones-WEB

# 📘 Práctica Servidor Web – Día 1  
**Configuración inicial de Apache y dominios internos**

Guía paso a paso de la configuración base del servidor web para la práctica del 1º trimestre. En este día dejaremos funcionando Apache, los dominios internos y la estructura de directorios necesaria para continuar con WordPress, Python y Nginx.

---

## 🧩 Entorno y requisitos

- Ubuntu Server/Desktop (22.04 recomendado)  
- Usuario con privilegios sudo  
- Navegador para validar acceso a los dominios internos  
- Archivo `/etc/hosts` modificado para resolver los dominios localmente  

Estructura del repositorio recomendada:

```
README.md
images/      ← aquí irán las capturas (FOTO)
docs/        ← opcional
```

---

# 0) Preparación inicial

Actualiza el sistema:

```bash
sudo apt update && sudo apt -y upgrade
```![Uploading image.png…]()


Instala herramientas básicas:

```bash
sudo apt -y install curl wget unzip git
```

(FOTO)

---

# 1) Configurar dominios locales (archivo `/etc/hosts`)

Edita el archivo:

```bash
sudo nano /etc/hosts
```

Añade al final:

```
127.0.0.1    centro.intranet
127.0.0.1    departamentos.centro.intranet
127.0.0.1    servidor2.centro.intranet
```

(FOTO)

### ✔ Comprobación de resolución DNS local

```bash
ping -c 1 centro.intranet
ping -c 1 departamentos.centro.intranet
ping -c 1 servidor2.centro.intranet
```

(FOTO)

---

# 2) Instalar Apache y crear estructura de sitios

Instala Apache:

```bash
sudo apt -y install apache2 apache2-utils
```

(FOTO)

### Crear los DocumentRoot para cada dominio

```bash
sudo mkdir -p /var/www/centro.intranet
sudo mkdir -p /var/www/departamentos.centro.intranet
sudo mkdir -p /var/www/servidor2.centro.intranet
```

Dar permisos:

```bash
sudo chown -R www-data:www-data /var/www/centro.intranet /var/www/departamentos.centro.intranet /var/www/servidor2.centro.intranet
sudo chmod -R 755 /var/www
```

(FOTO)

### Verificar estado del servicio Apache

```bash
sudo systemctl status apache2 --no-pager
```

(FOTO)

### Crear páginas temporales de prueba

```bash
echo "<h1>centro.intranet funciona</h1>" > /var/www/centro.intranet/index.html
echo "<h1>departamentos.centro.intranet funciona</h1>" > /var/www/departamentos.centro.intranet/index.html
echo "<h1>servidor2.centro.intranet funciona</h1>" > /var/www/servidor2.centro.intranet/index.html
```

(FOTO)

---

# 3) Activar soporte PHP y MySQL

Instala PHP:

```bash
sudo apt -y install libapache2-mod-php php php-mysql php-cli php-curl php-xml php-gd
```

Instala MySQL:

```bash
sudo apt -y install mysql-server
```

Ejecuta la configuración segura:

```bash
sudo mysql_secure_installation
```

(FOTO)

Reinicia Apache:

```bash
sudo systemctl restart apache2
```

---

# 4) Configurar VirtualHosts en Apache

---

## 4.1 VirtualHost para `centro.intranet`

```bash
sudo nano /etc/apache2/sites-available/centro.intranet.conf
```

Contenido:

```apache
<VirtualHost *:80>
    ServerName centro.intranet
    DocumentRoot /var/www/centro.intranet

    <Directory /var/www/centro.intranet>
        AllowOverride All
        Options Indexes FollowSymLinks
        Require all granted
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/centro_error.log
    CustomLog ${APACHE_LOG_DIR}/centro_access.log combined
</VirtualHost>
```

(FOTO)

---

## 4.2 VirtualHost para `departamentos.centro.intranet`

```bash
sudo nano /etc/apache2/sites-available/departamentos.centro.intranet.conf
```

Contenido:

```apache
<VirtualHost *:80>
    ServerName departamentos.centro.intranet
    DocumentRoot /var/www/departamentos.centro.intranet

    <Directory /var/www/departamentos.centro.intranet>
        AllowOverride All
        Require all granted
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/departamentos_error.log
    CustomLog ${APACHE_LOG_DIR}/departamentos_access.log combined
</VirtualHost>
```

(FOTO)

---

## 4.3 VirtualHost para `servidor2.centro.intranet`

```bash
sudo nano /etc/apache2/sites-available/servidor2.centro.intranet.conf
```

Contenido:

```apache
<VirtualHost *:80>
    ServerName servidor2.centro.intranet
    DocumentRoot /var/www/servidor2.centro.intranet

    <Directory /var/www/servidor2.centro.intranet>
        AllowOverride All
        Require all granted
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/server2_error.log
    CustomLog ${APACHE_LOG_DIR}/server2_access.log combined
</VirtualHost>
```

(FOTO)

---

## Activar sitios y reiniciar Apache

```bash
sudo a2ensite centro.intranet.conf
sudo a2ensite departamentos.centro.intranet.conf
sudo a2ensite servidor2.centro.intranet.conf
sudo systemctl reload apache2
```

Validar sintaxis:

```bash
sudo apache2ctl configtest
```

Debe mostrar:

```
Syntax OK
```

(FOTO)

---

# 5) Comprobación final en navegador

### ✔ http://centro.intranet  
(FOTO)

### ✔ http://departamentos.centro.intranet  
(FOTO)

### ✔ http://servidor2.centro.intranet  
(FOTO)

---

# ✅ Conclusión del Día 1

En este punto ya está configurada la base del proyecto:

- Apache instalado y funcionando  
- Tres dominios internos resueltos  
- VirtualHosts activos  
- Estructura lista para WordPress, Python y Nginx  
- Sitios accesibles desde navegador  

El siguiente paso será instalar WordPress, configurar WSGI para Python y habilitar la autenticación y AWStats.
