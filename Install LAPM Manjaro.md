---


---

<h1 id="instalar-lamp-en-manjaro">Instalar LAMP en Manjaro</h1>
<ol>
<li>Actualizar la base de datos de paquetes de nuestro sistema</li>
</ol>
<pre><code>  sudo pacman -Syu
</code></pre>
<h2 id="apache">Apache</h2>
<ol start="2">
<li>Instalar el apache</li>
</ol>
<pre><code>  sudo pacman -S apache
</code></pre>
<ol start="3">
<li>Reiniciar servicio httpd</li>
</ol>
<pre><code>  sudo systemctl restart httpd
</code></pre>
<ul>
<li>Podemos verificar que Apache está corriendo con el comando:<pre><code>sudo systemctl status httpd
</code></pre>
Ejemplo Output:<pre><code>httpd.service - Apache Web Server
Loaded: loaded (/usr/lib/systemd/system/httpd.service; disabled; vendor
preset: disabled)
Active: active (running) since Tue 2016-02-16 13:00:18 IST; 7s ago
Main PID: 1067 (httpd)
Tasks: 82 (limit: 512)
CGroup: /system.slice/httpd.service
	├─1067 /usr/bin/httpd -k start -DFOREGROUND
	├─1070 /usr/bin/httpd -k start -DFOREGROUND
	├─1071 /usr/bin/httpd -k start -DFOREGROUND
	└─1072 /usr/bin/httpd -k start -DFOREGROUND
Feb 16 13:00:18 server systemd[1]: `Started Apache Web Server.`
Feb 16 13:00:18 server httpd[1067]: AH00558: httpd: Could not reliably
dete...ge
</code></pre>
</li>
</ul>
<ol start="5">
<li>Test Apache</li>
</ol>
<ul>
<li>
<p>Crear un archivo de prueba</p>
<pre><code>sudo nano /srv/http/index.html
</code></pre>
</li>
<li>
<p>con el siguiente contenido</p>
<pre><code>&lt;!DOCTYPE html&gt;
&lt;html&gt;
&lt;head&gt;
  &lt;meta charset="UTF-8" /&gt;
  &lt;meta name="viewport" content="width=device-width, initial-scale=1" /&gt;
  &lt;meta http-equiv="X-UA-Compatible" content="ie=edge" /&gt;
  &lt;title&gt;Bienvenido&lt;/title&gt;
&lt;/head&gt;
&lt;body&gt;
  &lt;h2&gt;Página de Test Web Server &lt;/h2&gt;
&lt;/body&gt;
&lt;/html&gt;
</code></pre>
</li>
<li>
<p>Abrir <code>http://localhost</code> or <code>http://IP-address</code>.<br>
En el navegadro deberá aparecer la frase “Página de Test  Web Server”</p>
</li>
</ul>
<h1 id="mysql">Mysql</h1>
<ol start="7">
<li>Instalar el gestor de base de datos</li>
</ol>
<pre><code>  sudo pacman -S mysql 
</code></pre>
<blockquote>
<p>MariaDB ahora es oficialmente la implementación predeterminada de MySQL en Arch Linux desde 2013. Por lo tanto, se le preguntará si desea instalar el servidor MariaDB o Percona, simplemente presione enter, la selección predeterminada será MariaDB se instalará en su S.O Manjaro (Arch Linux).</p>
</blockquote>
<ol start="5">
<li>Inicializar el directorio de datos  antes de iniciar el servicio.</li>
</ol>
<pre><code>  sudo mysql_install_db --user=mysql --basedir=/usr --datadir=/var/lib/mysql
</code></pre>
<ol start="6">
<li>Habilitar e iniciar el servicio</li>
</ol>
<pre><code>  sudo systemctl enable mysqld
  sudo systemctl start mysqld
</code></pre>
<ul>
<li>Podemos verificar que MySql esta corriento con:<pre><code>sudo systemctl status mysqld
</code></pre>
Ejemplo Output:<pre><code>mysqld.service - MariaDB database server
Loaded: loaded (/usr/lib/systemd/system/mysqld.service; disabled;
vendor preset: disabled)
Active: active (running) since Tue 2016-02-16 13:19:50 IST; 44s ago
Process: 1406 ExecStartPost=/usr/bin/mysqld-post (code=exited,
status=0/SUCCESS)
Main PID: 1405 (mysqld)
Tasks: 26 (limit: 512)
</code></pre>
</li>
</ul>
<ol start="9">
<li>Configuracion de contraseña para el usuario root</li>
</ol>
<pre><code>  sudo mysql_secure_installation
</code></pre>
<h1 id="php">PHP</h1>
<ol start="10">
<li>Instalar  PHP</li>
</ol>
<pre><code>  sudo pacman -S php php-apache 
</code></pre>
<ol start="11">
<li>Configurar Apache para que funcione en conjunto con PHP.</li>
</ol>
<pre><code>  sudo nano /etc/httpd/conf/httpd.conf
</code></pre>
<ul>
<li>
<p>Comentamos o remplazamos la linea</p>
<pre><code>  LoadModule mpm_event_module modules/mod_mpm_event.so 
</code></pre>
<p>con la siguiente:</p>
<pre><code>  LoadModule mpm_prefork_module modules/mod_mpm_prefork.so
</code></pre>
</li>
<li>
<p>Posteriormente, al final del mismo archivo, agregamos el siguiente bloque si usas php 5 ó si tienes php 7:</p>
<pre><code># Use for PHP 5.x:
LoadModule php5_module modules/libphp5.so
AddHandler php5-script php
Include conf/extra/php5_module.conf

# Use for PHP 7.x:
LoadModule php7_module modules/libphp7.so
AddHandler php7-script php
Include conf/extra/php7_module.conf
</code></pre>
</li>
<li>
<p>En este mismo archivo también se puede agregar esto para  para cargar los index.php si se encuentran en el directorio (Omitir está parte jeje)</p>
<pre><code>&lt;IfModule dir_module&gt;
  &lt;IfModule php7_module&gt;
  	DirectoryIndex index.php index.html
  	&lt;FilesMatch "\.php$"&gt;
  		SetHandler application/x-httpd-php
  	&lt;/FilesMatch&gt;
  	&lt;FilesMatch "\.phps$"&gt;
  		SetHandler application/x-httpd-php-source
  	&lt;/FilesMatch&gt;
  &lt;/IfModule&gt;
&lt;/IfModule&gt;
</code></pre>
</li>
</ul>
<ol start="12">
<li>Test PHP</li>
</ol>
<ul>
<li>Creamos el archivo test.php<pre><code>sudo nano /srv/http/test.php
</code></pre>
Con el siguiente contenido<pre><code>    &lt;?php 
      phpinfo();
</code></pre>
</li>
<li>Guardamos el archivo y reiniciamos el httpd<pre><code>  sudo systemctl restart httpd
</code></pre>
</li>
<li>Abrimos <code>http://localhost/test.php</code> o <code>http://ip-address/test.php</code> y verificamos que se encuentre la información de PHP</li>
</ul>
<h1 id="phpmyadmin">PhpMyAdmin</h1>
<ol start="13">
<li>
<p>Instalar PhpMyAdmin</p>
<pre><code>sudo pacman -S phpmyadmin
</code></pre>
</li>
<li>
<p>Editar el archivo <code>php.ini</code></p>
<pre><code>sudo nano /etc/php/php.ini
</code></pre>
<p>verificando que las siguientes lineas esten SIN comentar</p>
<pre><code>[...]
extension=bz2
extension=mysqli
[...]
</code></pre>
<p>Guardar y cerrar el archivo</p>
</li>
<li>
<p>Crear archivo de configuración para phpMyAdmin,</p>
<pre><code>sudo nano /etc/httpd/conf/extra/phpmyadmin.conf
</code></pre>
<p>Agregar las siguientes lineas:</p>
<pre><code>Alias /phpmyadmin "/usr/share/webapps/phpMyAdmin"
&lt;Directory "/usr/share/webapps/phpMyAdmin"&gt;
DirectoryIndex index.php
AllowOverride All
Options FollowSymlinks
Require all granted
&lt;/Directory&gt;
</code></pre>
</li>
<li>
<p>Editar el archivo de configuración de Apache</p>
<pre><code>sudo nano /etc/httpd/conf/httpd.conf
</code></pre>
<p>Agregar la siguiente linea al final del archivo</p>
<pre><code>Include conf/extra/phpmyadmin.conf
</code></pre>
<p>Guardamos el archivo y reiniciamos httpd</p>
<pre><code>sudo systemctl restart httpd
</code></pre>
</li>
<li>
<p>Test phpMyAdmin<br>
Abrimos <code>http://localhost/phpmyadmin</code> o `http://IP-Address/phpmyadmin</p>
</li>
</ol>
<h2 id="y-listo-pa--pintura...">Y listo pa  pintura…</h2>
<p>Fuente:</p>
<p>[1] Install Apache, MariaDB, PHP (LAMP) 2016 <a href="https://forum.manjaro.org/t/install-apache-mariadb-php-lamp-2016/1243">https://forum.manjaro.org/t/install-apache-mariadb-php-lamp-2016/1243</a><br>
[2] Pasos para instalar LAMP en Manjaro <a href="https://gist.github.com/pokisin/a294d2993c50c43a579bb09cef66d98d">https://gist.github.com/pokisin/a294d2993c50c43a579bb09cef66d98d</a></p>
<blockquote>
<p>Written with <a href="https://stackedit.io/">StackEdit</a>.</p>
</blockquote>

