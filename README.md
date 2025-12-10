# 🚀 Debian + n8n en Máquina Virtual

![n8n](https://img.shields.io/badge/n8n-%E2%98%85-orange) ![Debian](https://img.shields.io/badge/Debian-Bookworm-blue) ![Estado](https://img.shields.io/badge/Estado-Borrador-yellow) ![Taller](https://img.shields.io/badge/Proyecto-Taller%20SO-green)

Configuración y despliegue de un servidor de automatización (n8n) sobre Debian en entorno virtualizado. Proyecto para el Taller de Sistemas Operativos.

---

## 📌 Índice
- [Descripción](#descripción)
- [Estado del proyecto](#estado-del-proyecto)
- [Requisitos previos](#requisitos-previos)
- [Estructura del repositorio](#estructura-del-repositorio)
- [Inicio rápido (Docker)](#inicio-rápido-docker)
- [Instalación nativa (Opcional)](#instalación-nativa-opcional)
- [Configuración recomendada para producción](#configuración-recomendada-para-producción)
- [Backup y restauración](#backup-y-restauración)
- [Seguridad](#seguridad)
- [Troubleshooting rápido](#troubleshooting-rápido)
- [Contribuir](#contribuir)
- [Licencia y créditos](#licencia-y-créditos)

---

## 📝 Descripción
Este repositorio contiene recursos, instrucciones y ejemplos para desplegar n8n sobre una máquina virtual con Debian. Está pensado para prácticas de laboratorio y despliegues ligeros reproducibles.

> Nota: Ajusta versiones y rutas según tu entorno.

---

## 📊 Estado del proyecto
- Estado: Borrador / En desarrollo
- Última actualización: 2025-12-10
- Uso previsto: enseñanza y despliegue en VM

---

## ⚙️ Requisitos previos
- Host con virtualización (VirtualBox, VMware, Proxmox, Hyper-V, etc.)
- Conexión a Internet
- Imagen de Debian (recomendado: Debian 12 "Bookworm")
- Usuario con sudo o root
- (Recomendado) Docker y Docker Compose

---

## 📁 Estructura esperada del repositorio
- docs/ — documentación adicional
- scripts/ — scripts de provisión (bash / ansible)
- vm/ — Vagrantfile / plantillas
- docker/docker-compose.yml — ejemplo de despliegue con Docker Compose
- README.md — este archivo
---

## 🚀 Inicio rápido (Docker) — Método recomendado

1. Crea y accede a tu VM Debian.
2. Actualiza sistema:
```bash
sudo apt update && sudo apt upgrade -y
```
3. Instala Docker y Docker Compose (según documentación oficial).
4. Crea un directorio para n8n y añade `docker-compose.yml`.
5. Inicia:
```bash
docker compose up -d
```

Ejemplo mínimo `docker-compose.yml` (ajusta según necesidades):
```yaml
version: "3.8"
services:
  n8n:
    image: n8nio/n8n:latest
    restart: unless-stopped
    ports:
      - "5678:5678"
    environment:
      N8N_HOST: "TU_DOMINIO_O_IP"
      N8N_PORT: "5678"
      WEBHOOK_URL: "https://tu-dominio.tld/"
      GENERIC_TIMEZONE: "Europe/Madrid"
      # Para producción, usar DB externa:
      # DB_TYPE: "postgresdb"
      # DB_POSTGRESDB_HOST: "postgres"
      # DB_POSTGRESDB_PORT: "5432"
      # DB_POSTGRESDB_DATABASE: "n8n"
      # DB_POSTGRESDB_USER: "n8n"
      # DB_POSTGRESDB_PASSWORD: "changeme"
    volumes:
      - ./n8n-data:/home/node/.n8n
```

Atajos útiles:
- Ver logs: `docker compose logs -f`
- Reiniciar: `docker compose restart`

---


## ✅ Configuración recomendada para producción
- Base de datos: Postgres (no SQLite).
- Reverse proxy: Nginx o Traefik con TLS (Let's Encrypt).
- Autenticación: habilitar BASIC/JWT o SSO.
- Firewall: configurar UFW/iptables para limitar puertos.
- Backups automáticos de datos y base de datos.
- Monitorización y alertas (logs, healthchecks).

Checklist rápida:
- [ ] DB externa (Postgres)
- [ ] Proxy TLS
- [ ] Backups automatizados
- [ ] Usuarios/roles y autentificación
- [ ] Monitorización básica

---

## 💾 Backup y restauración
Backup de directorio docker:
```bash
tar czf n8n-backup-$(date +%F).tar.gz ./n8n-data
# y dump de Postgres si aplica:
pg_dump -U n8n -h postgres n8n > n8n-db-$(date +%F).sql
```

Restauración:
1. Detén contenedores / servicio.
2. Restaura archivos y/o base de datos.
3. Arranca servicios.

---

## 🔒 Seguridad
- No exponas la interfaz sin autenticación.
- Usa TLS siempre en producción.
- No guardes secretos en Git. Usa .env o gestores de secretos.
- Mantén el sistema y contenedores actualizados.
- Considera fail2ban y reglas restrictivas de firewall.

---

## ⚠️ Troubleshooting rápido
- Puerto 5678 ocupado: `ss -tuln | grep 5678`
- Permisos en volúmenes Docker: `chown -R 1000:1000 ./n8n-data` (user interno del contenedor)
- Conexión a Postgres: prueba desde la VM con `psql` o `nc -zv host port`

---

## 🤝 Contribuir
1. Fork → Branch feature/tu-cambio → PR
2. Describe cambios y añade pruebas/documentación
3. Revisa checklist de PR

---

## 📜 Licencia y créditos
- Licencia: MIT
- Referencias:
  - n8n: https://n8n.io
  - Docker: https://docs.docker.com
  - Debian: https://www.debian.org

---
