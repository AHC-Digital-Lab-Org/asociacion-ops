# 🏗️ Infraestructura Core: Asociacion-Ops

Este repositorio es el **motor de operaciones** de la organización. Centraliza la lógica de integración y despliegue continuo (CI/CD) para garantizar que todos los proyectos de la asociación mantengan los mismos estándares de calidad, seguridad y eficiencia.

## 🌟 1. Visión General del Ecosistema

Nuestra infraestructura utiliza un modelo de **Arquitectura Centralizada** (Reusable Workflows) para escalar de forma gratuita y segura:

* **GitHub Organizations:** Centro de mando para permisos, equipos y **Secretos Globales**.
* **asociacion-ops (Este repo):** Define los "planos" técnicos y la lógica de despliegue reutilizable.
* **Repo-Template:** El molde inicial para nuevos proyectos, conectado automáticamente a este "cerebro".
* **Netlify:** Hosting que recibe los despliegues "empujados" desde GitHub Actions. Al usar este método, podemos desplegar repositorios de la organización y observar las funcionalidades en tiempo real.

## 🔐 2. Sistema de Permisos y Colaboración

Fomentamos una cultura de **Inner Source**: cualquier miembro puede proponer mejoras en cualquier proyecto, pero la integridad de la rama principal está protegida.

### Roles en la Organización:

| Rol | Alcance | Responsabilidades |
| :--- | :--- | :--- |
| **DevOps (Admin)** | Global | Provisión de repositorios, gestión de secretos de organización y mantenimiento de este repo. |
| **Maintainer** | Repositorio | Dueño del proyecto. Es el único que puede **aprobar y fusionar (merge)** Pull Requests. |
| **Writer (Equipo Devs)** | Todos los Repos | Miembros del equipo `Desarrolladores-Asociacion`. Pueden crear ramas y proponer cambios en cualquier proyecto. |

### Flujo de Trabajo y Protección de Ramas:
1.  **Colaboración Abierta:** Todos los desarrolladores tienen permiso de *Writer* en todos los repositorios. Pueden crear ramas (`feature/mejora`) y subir commits.
2.  **Pull Request Obligatorio:** La rama `main` está protegida. Nadie puede hacer push directo. Todo cambio debe venir de un PR.
3.  **Checks de CI/CD:** Antes de permitir el merge, el sistema ejecuta automáticamente el Linter y el Build. Si fallan, el merge queda bloqueado.
4.  **Aprobación Humana:** Aunque los tests pasen, se requiere obligatoriamente la aprobación (Review) de un **Maintainer** para poder integrar el código.

## 📂 3. Arquitectura Técnica y Secretos

### Gestión de Secretos
* **`NETLIFY_AUTH_TOKEN` (Secreto de Org):** Almacenado en los ajustes de la Organización. Permite que GitHub hable con Netlify en nombre de la asociación.
* **`NETLIFY_SITE_ID` (Variable de Repo):** Se configura en cada repositorio individual. Indica a qué sitio web de Netlify debe enviarse el código.

### El Corazón: `netlify-reusable.yml`
Ubicado en `.github/workflows/`, este archivo contiene los pasos de ejecución:
1.  **Instalación:** Configura Node.js y descarga las dependencias.
2.  **Calidad:** Ejecuta el Linter para asegurar que el código sigue las reglas de la asociación.
3.  **Build:** Compila el proyecto.
4.  **Deploy:** Usa `npx netlify-cli deploy --prod` para enviar el resultado a producción.

## 🛠️ Protocolo de Provisión (Solo DevOps)

Cada vez que se inicie un proyecto nuevo, el DevOps debe:

1.  **Crear el Repo:** Usar el botón "Use this template" del `repo-template`. (Recuerda seleccionar la organizacion a la hora de crearlo)
2.  **Aprovisionar Netlify:** Crear el sitio manualmente en Netlify:
3.  * Ir a `Import from Github` seleccionamos la organizacion y el nuevo repositorio `deploy`
    * Una vez desplegado: `Project configuration > Project information > PROJECT ID "SITE ID"`
    * Copiamos esta `SITE ID` en la variable `NETLIFY_SITE_ID` del repositorio
4.  **Configurar Variables:** En el nuevo repo de GitHub, añadir la variable `NETLIFY_SITE_ID`.
5.  **Asignar Responsable:** Añadir al desarrollador jefe como *Maintainer* del repositorio.
6.  **Activar Protección de Rama:**
7.  * Ir a `Settings > Branches > Add rule`.
    * Pattern: `main`.
    * Bypass: `Mantain {...} For pull request only`.
    * Activar: `Require a pull request before merging` (1 approval).
    * Activar: `Require status checks to pass` (Seleccionar el job de CI/CD = 🚀 CI/CD Pipeline).

---

> **Nota de Mantenimiento:** Cualquier cambio realizado en este repositorio (`asociacion-ops`) se propagará automáticamente a todos los proyectos que utilicen el flujo de trabajo
