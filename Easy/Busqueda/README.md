---
title: "Busqueda"
status: "Finalizado"
---

# Contenido


# Índice de figuras e imágenes


# Índice de tablas


# Busqueda

Busqueda is an Easy Difficulty Linux machine that involves exploiting a command injection vulnerability present in a Python module. By leveraging this vulnerability, we gain user-level access to the machine. To escalate privileges to root, we discover credentials within a Git config file, allowing us to log into a local Gitea service. Additionally, we uncover that a system checkup script can be executed with root privileges by a specific user. By utilizing this script, we enumerate Docker containers that reveal credentials for the administrator user&amp;#039;s Gitea account. Further analysis of the system checkup script&amp;#039;s source code in a Git repository reveals a means to exploit a relative path reference, granting us Remote Code Execution (RCE) with root privileges.


## Details
**IP:**


## Information Gathering

Para saber que puertos se encuentran disponibles, utilizaremos la herramienta de ***Rustscan***, con el siguiente comando:
```bash
rustscan -a [TARGET]
```
>[!INFO] Resultado obtenido
>```bash
>PORT   STATE SERVICE REASON
22/tcp open  ssh     syn-ack
80/tcp open  http    syn-ack
>```


Con esta información podemos utilizar ***curl*** o ***dig*** para obtener más información de la página, yo utilizaré el siguiente comando:
```bash
curl [TARGET] | grep http
```
>[!INFO] Resultado obtenido
>```bash
><p>The document has moved <a href="http://searcher.htb/">here</a>.</p>
>```


Como podemos observar, no podemos ingresar por el simple hecho de que aún no hemos agregado el nombre del dominio al archivo ***/etc/hosts*** podemos utilizar el siguiente comando:
```bash
echo "[TARGET] searcher.htb" | sudo tee -a /etc/hosts
```


Con la ***IP*** apuntando al ***DNS***, podemos volver a utilizar ***curl*** y ver que ha cambiado.
```bash
curl -s -X GET searcher.htb -w "%{http_code}" -o /dev/null
```
>[!INFO] Resultado obtenido
>```bash
>└──╼ $curl -s -X GET searcher.htb -w "%{http_code}" -o /dev/null
>200
>```


Con esto sabemos que ahora podemos acceder a la página web de manera correcta. Podemos ir a la página o hacer una busqueda de rutas/subdominios, yo empezaré por realizar un escaneo de rutas utilizando ***Gobuster***. Utilizaré el siguiente comando:
```bash
gobuster dir -u http://searcher.htb/ -w /usr/share/seclists/Discovery/Web-Content/common.txt -t50 -e js,json,html,php,sh,py,htb
```
>[!INFO] Resultado obtenido
>```bash
>http://searcher.htb/search               (Status: 405) [Size: 153]
http://searcher.htb/server-status        (Status: 403) [Size: 277]
Progress: 4751 / 4751 (100.00%)
>```


Como podemos observar, simplemente salen dos rutas, por lo cual pasaremos a ingresar a la ruta principal en ***http://searcher.htb***.
>[!INFO] Resultado obtenido