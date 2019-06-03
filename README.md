# FireFirma
Firmar electrónicamente con un sólo fichero ejecutable. Sin dependencias.

Descarga la última version desde:

https://github.com/alfem/FireFirma/releases


## Componentes 
![Diagrama de Componentes](https://raw.githubusercontent.com/alfem/FireFirma/master/Componentes-FireFirma.png)


Para poder realizar firmas electrónicas en páginas web usando la aplicación del Ministerio de Hacienda "AutoFirma", hay que poner de acuerdo un buen puñado de componentes:

### El navegador web:
AutoFirma funciona con Chrome y Firefox. Yo me quedo con este último porque lo conozco mejor y puedo indagar en sus tripas. Cada versión nueva trae 'sorpresas' y es normal que si firmas con una versión, con la siguiente ya no. Casi siempre es porque el método de almacenar certificados cambia o porque se vuelve más exigente/quisquilloso con la seguridad. Pero este navegador... firma.

### AutoFirma:
La aplicación desarrollada por el Ministerio de Hacienda para evitar los applets en Java y poder firmar documentos de forma independiente, como un programa más de tu escritorio. El código es un poco 'meh', pero al menos es software libre, está publicado en GitHub y se puede comprobar como funciona. Ojo con la versión de Java que requiere. Si tu sistema operativo lleva de serie una versión de Java demasiado moderna, es muy posible que AutoFirma no funcione. La última versión de AutoFirma (1.6.5) va bien con Java 11. Si tienes la anterior, necesitarás Java 8.

### Manejador de protocolo afirma:
Cuando una web quiere que firmes algo, te manda una url que empieza por 'afirma://'. Como esta url no es estándar, el navegador no sabrá que hacer con ella. Es necesario que tengamos instalado un pequeño fichero que indica la relación entre estas urls y el programa AutoFirma. Normalmente estará en /usr/share/applications/afirma.desktop y contendrá una línea que dice algo como MimeType=x-scheme-handler/afirma;
Puedes comprobar que esta relación funciona tecleando en el navegador una dirección afirma://meloinvento. Debería aparecer una ventana ofreciéndo abrir AutoFirma. 

### Almacén de Certificados de AutoFirma:
Una vez que el navegador ha reconocido la url y ha llamado al programa AutoFirma, éste se queda calladito trabajando 'en background' e intenta abrir un canal seguro para comunicarse con el navegador. Este canal seguro depende que de ambas partes confíen en la otra y para eso usan un certificado nuevecito que se genera durante la instalación de AutoFirma. El certificado debe estar disponibile para la aplicación Autofirma, que lo buscará en /usr/lib/AutoFirma/autofirma.pfx, y para Firefox, que lo buscará en su propio almacén de Certificados. Si no coinciden, porque tenemos más de uno, porque la instalación falló o la hemos hecho varias veces, o vete tú a saber, no podremos firmar desde la web. El navegador desistirá al cabo de unos segundos y posiblemente mostrará algún error.

### Almacén de Autoridades de Certificación:
Firefox puede usar los certificados almacenados en varios sitios: en su propio almacén (que viene de serie), en el del sistema operativo, en el del usuario, en una tarjeta criptográfica, en un pendrive... etc. Lo importante es que debe encontrar el certificado de AutoFirma o no podrá comunicarse con la aplicación. El instalador oficial de AutoFirma coloca este certificado en el directorio del usuario. Suele funcionar, pero si se crea un nuevo usuario o un nuevo perfil después de haber hecho ésta instalación, estos no podrán firmar nada. Parece por tanto preferible instalar el certificado de Autofirma en el propio sistema operativo, de forma que esté disponible para todos los usuarios y perfiles, actuales y futuros. Para eso hay que 'engañar' un poco a Firefox cambiando la librería que usa para acceder a sus Autoridades de Certificación (libnssckbi.so) por otra que usa las Autoridades del sistema (p11-kit-trust.so). 

### Almacén de Certificados del Usuario:
Por último necesitaremos el Certificado de la persona que va a firmar, y que se carga desde Firefox con el Menú->Preferencias->Privacidad y Seguridad. Abajo a la derecha hay un botón "Ver Certificados" que abre una nueva ventana. Eliges la solapa "Sus Certificados" e importas tu certificado personal. 



