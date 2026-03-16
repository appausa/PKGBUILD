# PKGBUILD
Para tener un gestor de paquetes en LFS (Pacman)

# LFS PKGBUILD SCRIPTS
Esta es una colección de scripts PKGBUILD que he creado hasta ahora para LFS. Esto cubre todos los paquetes del capítulo 6 (excepto GRUB, que no uso) más paquetes adicionales de BLFS y otros lugares a medida que los creo.

# NOTAS
Antes de poder utilizar estos scripts PKGBUILD, por supuesto necesitarás descargar, compilar e instalar pacman. La mayoría de las dependencias de pacman tienen instrucciones de compilación en BLFS, excepto fakeroot, pero ese es un paquete sencillo.

Sugiero inicialmente la instalación de pacman y su dependencias en el directorio /tools y entonces seguir el libro adelante de allí. una vez usted acabado configurar tu sistema, paquete y instalar pacman y su dependencias así que lo será disponible de tu lfs construir una vez usted reiniciar eso.

# ADVERTENCIA
Básicamente, cualquiera de los paquetes del libro que hacen referencia al directorio /tools deberá modificarse para poder utilizarse. Algunos otros paquetes también tienen optimizaciones específicas para mi sistema que quizás no desees..

# LICENCIA
La mayor parte del código utilizado en los PKGBUILD se toma del libro LFS, cuyo código está bajo una licencia MIT. El aviso de licencia y derechos de autor se encuentra en el archivo LICENCIA en el directorio de nivel superior.

He aquí una guía para la instalación de Pacman en Linux From Scrach

Instalación de pacman en su cadena de herramientas temporal

Comenzaremos justo antes del apartado 6.7. Encabezados API de Linux-4.9.9 del libro LFS 12.4.

# Dependencias de Pacman

Pacman depende de los siguientes paquetes:

zlib
libarchive
pkg-config
fakeroot, que a su vez depende de libcap

La mayoría de estos no forman parte del libro LFS, así que descargue sus fuentes manualmente:
<pre>
wget https://www.libarchive.org/downloads/libarchive-3.3.2.tar.gz
wget http://turul.canonical.com/pool/main/f/fakeroot/fakeroot_1.22.orig.tar.bz2
wget https://sources.archlinux.org/other/pacman/pacman-5.0.2.tar.gz
</pre>
Compila estos paquetes con el comando siguiente. Al igual que el libro LFS, estos comandos suponen que ha extraído las fuentes relevantes y las ha guardado en el directorio resultante.

# zlib 1.2.11
<pre>
./configure --prefix=/tools

make

make install
</pre>
# libarchive 3.3.2
<pre>
./configure --prefix=/tools --without-xml2 --disable-shared

make

make install
</pre>
# pkg-config 0.29.2
<pre>
./configure --prefix=/tools            \
            --with-internal-glib       \
            --disable-compile-warnings \
            --disable-host-tool        \
            --disable-shared           \
            --docdir=/tools/share/doc/

make

make install
</pre>
# libcap 2.25
<pre>
make

make RAISE_SETFCAP=no lib=lib prefix=/tools install

chmod -v 755 /tools/lib/libcap.so
</pre>
# fakeroot 1.22
<pre>
./configure --prefix=/tools                 \
            --libdir=/tools/lib/libfakeroot \
            --with-ipc=sysv
make
make install
</pre>
# Pacman 5.0.2
<pre>
./configure --prefix=/tools   \
            --disable-doc     \
            --disable-shared  \
            --sysconfdir=/etc \
            --localstatedir=/var

make

make install
</pre>
Esto habrá instalado, entre otros, los archivos de configuración makepkg.conf y pacman.conf en /etc; es posible que desee editarlos. Para makepkg.conf, asegúrese de que CARCH y CHOST sean apropiados, por ejemplo:
<pre>
CARCH="x86_64"
CHOST="x86_64-pc-linux-gnu"
</pre>
Puede configurar su nombre y dirección de correo electrónico como PACKAGER si lo desea.

Configurando para construir paquetes con pacman

Cree un nuevo usuario agregando lo siguiente a /etc/passwd:
<pre>
adalberto:x:1000:999:adalberto:/home/adalberto:/bin/bash
</pre>
Donde 999 es el ID del grupo de usuarios. Verifique /etc/group para obtener el ID correcto o, si los usuarios no están presentes en ese archivo, agregue lo siguiente.
<pre>
users:x:999:
</pre>
Crea el directorio home:
<pre>
mkdir -v /home/adalberto 

chown -Rv adalberto:adalberto /home/adalberto 
</pre>
Salga de su chroot actual, luego ingrese a su nuevo usuario (asegúrese de que 1000, 999 y adalberto estén configurados con los valores adecuados para su sistema):
<pre>
chroot --userspec=1000:999 "$LFS" /tools/bin/env -i \
     HOME=/home/adalberto  \
     TERM="$TERM"          \
     PS1='$? \u:\w \$ '    \
     PATH=/bin:/usr/bin:/sbin:/usr/sbin:/tools/bin:/tools/sbin      \
     /tools/bin/bash --login +h
</pre>
Es posible que desee crear un directorio de compilaciones en su directorio de inicio. Allí, luego creará un directorio para cada paquete que esté creando.

Instalación de pacman con pacman

copia las fuentes de pacman en su directorio build, ~/build/pacman-5.0.2.

Descargar los archivos necesario para construir los (PKGBUILD, makepkg.conf y pacman.conf.x86_64) desde Arch Linux, paquetes repo a construir en el directorio.

Edits los archivos PKGBUILD:

Comentario fuera del grupos y dependencias de líneas. Ojo no utilice los paquetes de Arch Linux, y porque lfs es diferente a Arch Linux, pacman no debe tener dependencias de Arch Linux para intalarlo. Editar la fuente variable a Arch, sólo la fuente de pacman tarball (no el url) y el tres descargar archivos se actual. cambio el configurar llamar en el construir que funcionen:
<pre>
./configure              
    --prefix=/tools      \
    --sysconfdir=/etc    \
    --localstatedir=/var \
    --disable-doc        \
    --disable-shared
</pre>
comentamos que esta fuera de comprobar función; el pruebas no correr sin Pyton.

Now run the following command as your non-root user from the build directory: Ahora corre el siguiente comando como el usuario root para construir el directorio:
<pre>
makepkg --skipchecksums
</pre>
Puedes saltar la seguridad de comprobación porque nosotros no tener instalado OpenSSL aún.

si todo va bien, este debería tener creado un archivo que usted puede ahora instalar lo sigue, como root ahora:
<pre>
pacman -U pacman-5.0.2-2-x86_64.pkg.tar.gz
</pre>
Instalando paquetes del capítulo seis de el libro LFS ahora al continuar con el descansaremos del libro, porque en vez de intalar paquetes manualmente lo aremos con pacman desde el capítulo seis de el Libro LFS.

Todo lo que necesita es que puedas encontrar los archivos PKGBUILD en el directorio packages, pero si quieres leer o crear estos, le sugiero que busque solo referencia de el sitio arch.

Todo lo que necesitas es los archivos PKGBUILD que se encuentran en el directorio de los paquetes, pero si usted desea aprender cómo a crear estos, yo sugerir que usted sólo mire los que tengo aquí ya creado si te encuentras enredado.

# Creación de Paquetes

El proceso general va como este:

Crea um nuevo directorio en el directorio builds.

Copia todo los paquetes y parches que necesites (archivos fuentes, y otros posibles archivos, parches o archivos de configuración).

Escribe las archivos PKGBUILD.

Run makepkg --skipchecksums.
Comprueba el contenido del directorio pkg, que es el que pacman Create, y de esta forma puede verlo.

Instalar el paquete (como root) con
<pre>
pacman -u $ nombre de archivo
</pre>
Al escribir o editar el archivo PKGBUILD pode tener errores. Es importante que usted aga una copia del libro LFS, copie y pegue lo que necesita para el archivo PKGBUILD, pero no será fácil esto porque la configuración de PKGBUILD es un poco difernte del libro.

Si estas con dudas, puedes buscar información en los repo de Arch Linux, en este github.com.

Consejos Directorio fuente Al llamar a cualquiera de las funciones PKGBUILD, makepkg te llevará automáticamente al directorio fuente, que es donde habrá extraído o creado enlaces simbólicos a todos tus archivos fuente.

Consejos Directorio Fuente
Donde entrará los paquetes

Fuente tarball makepkg donde pacman estrae todos los archivos.

Esto es por qué el primero paso de cada función voluntarias usualmente es cd en el directorio creado durante el extracción de la fuente tarball.

Instalando paquetes La instalación de los archivos, no se puede instalar en el sistema raíz, pero en su lugar usted intalara en ${pkgdir}. Para cierto paquetes ponerce en destdir = ${pkgdir} instalar en vez de make install. No todo paquetes usan destdir, sin embargo. Si usted está no seguro cómo aquellos paquetes debería ser construido, usted puede siempre comprobar en Arch Linux los paquetes o el PKGBUILDs de este reporte.

En algunos casos, el libro le dera que mueva un directorio. Mientras trabsja con la creación de los paquetes, Pero pude provocar problemas cuando los intale. Debes crear primero un directorio con permiso -vdm755 $dir_name.

# Post-install Pacman ejecuta las funciones he intala los archivos.

# 6.9. Glibc-2.26 
El libro quiere que crees algunos enlaces simbólicos; lo hice manualmente, ya que sentí que estaría fuera de lugar en el paquete.

La parte tzdata de glibc requiere que zic esté instalado en el sistema, lo que significa que debe instalarse después de glibc con pacman -U. Si bien supongo que se podría llamar directamente al binario zic que reside en los directorios src o pkg, pensé que sería más limpio separar la instalación de tzdata en un paquete aparte, de forma similar a como lo hace Arch Linux.

# 6.10. Adjusting the Toolchain 
I did these steps manually. It didn't Realicé estos pasos manualmente. No me pareció apropiado (ab)utilizar makepkg/pacman para este propósito.

# 6.15. Bc-1.07.1 
El libro indica que se deben crear enlaces simbólicos para libncurses. Lo hice manualmente antes de compilar el paquete.

# 6.20. GCC-7.2.0 
Antes de compilar el paquete, aumente el tamaño de la pila: ulimit -s 32768.

Tuve que usar --force al instalar este paquete, ya que algunas bibliotecas ya existían en el sistema.

# 6.28. Shadow-4.5 
Ejecuté passwd manualmente.

# 6.34. Bash-4.4 Al 
crear el paquete, makepkg me dijo que "El paquete contiene una referencia a $srcdir". Usando <em>grep -R "$(pwd)/src" pkg/</em>, descubrí que Bash instala Makefile.inc en /usr/lib/bash/, que contiene una referencia al directorio de compilación. En una instalación existente de Arch Linux, <em>/usr/lib/bash/Makefile.inc </em>también contenía una referencia a un directorio de compilación (inexistente), así que supongo que esto es inofensivo.

Utilice --force para instalar este paquete, ya que /bin/bash se creó como parte de la versión 6.6. Creación de archivos y enlaces simbólicos esenciales.

Luego volví a aplicar chroot, usando /bin/bash en lugar de /tools/bin/bash.

# 6.40. Perl-5.26.0 
El manual de LFS indica que se cree el archivo /etc/hosts. He optado por hacerlo manualmente, en lugar de que el paquete Perl lo instale. No parece lógico que Perl sea el propietario de este archivo. Como referencia, en Arch Linux, el archivo hosts pertenece al paquete del sistema de archivos, que contiene los archivos base de Arch Linux, por lo que tiene sentido que se cree manualmente en el caso de LFS.

Utilice --force para instalar este paquete, ya que /usr/bin/perl se creó como parte de la versión 6.6. Creación de archivos y enlaces simbólicos esenciales.

# 6.50. Coreutils-8.27 
Por alguna razón, al ejecutar sed in situ (sed -i) en chroot.8, el archivo obtuvo permisos 000. Lo reemplacé con un sed normal, redirigiendo el resultado a un nuevo archivo, y luego usé install para copiar el archivo a su destino.

Utilice --force para instalar este paquete, ya que existen varios archivos (entre otros, cat, dd, echo) en /bin (como enlaces simbólicos a /tools). Estos se añadieron en la versión 6.6, sección «Creación de archivos y enlaces simbólicos esenciales».

# 6.53. Findutils-4.6.0 
Al igual que con coreutils, usar sed en el lugar estableció los permisos a 000, así que usé la misma solución alternativa aquí.

# 6.63. Sysklogd-1.5.1 
El archivo makefile de Sysklogd no admite la especificación de un directorio de destino durante la instalación (make DESTDIR=/ruta). He incluido un parche que añade esta funcionalidad.

Etapa 4: Instalación de pacman en el sistema final. La etapa cuatro comienza inmediatamente después del último paquete del capítulo seis, que actualmente es vim. Antes de continuar con el resto del capítulo, compile e instale pacman y sus dependencias.

Como parte del capítulo seis, ya deberías haber instalado la mayoría de estos paquetes, excepto:

libarchive fakeroot pacman Utilice su instalación temporal de pacman para instalarlos. Recuerde, estamos instalando en el sistema final, no en /tools.

Etapa 5 - Finalización del libro Termine el resto del libro manualmente.

Al reiniciar mi máquina, el sistema LFS no arrancaba correctamente. Resultó que muchos binarios y otros archivos pertenecían a Ben, el usuario con el que compilé los paquetes. No tengo ni idea de por qué sucedió, pero al cambiarles el propietario a root:root, mi sistema pudo arrancar.
