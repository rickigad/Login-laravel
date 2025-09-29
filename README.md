# Login-laravel
# README - Laboratorio: Implementación de Login en Laravel

> **Proyecto:** Laboratorio 1 — Primer acercamiento a Laravel (MVC)

---

## Índice

1. Descripción
2. Requisitos previos
3. Instalación y flujo de comandos utilizados
4. Estructura del proyecto y breve explicación MVC
5. Base de datos (.env, migraciones, respaldo)
6. Resultado (captura de pantalla)
7. Dificultades encontradas y soluciones aplicadas
8. Referencias / Fuentes consultadas
9. Fecha de ejecución
10. Footer (datos del desarrollador)

---

## 1. Descripción

Este repositorio documenta el proceso de configuración e implementación del módulo de **Login** en Laravel como parte del laboratorio de la unidad `Modelo–Vista–Controlador (MVC)`. Aquí se describen los pasos seguidos, los comandos ejecutados, la configuración de la base de datos, los problemas encontrados y las soluciones aplicadas. El objetivo es entregar un README claro que sirva de referencia para futuras prácticas.

---

## 2. Requisitos previos

**Hardware / OS**

* Sistema operativo: Windows / Linux / macOS (indicar cuál usaste)

**Software / Herramientas**

* PHP 8.0 o superior
* Composer (última versión estable)
* Laravel (Laravel installer o `composer create-project`)
* Servidor local: XAMPP, WampServer, Laragon o equivalente
* Servidor web: Apache o Nginx
* Base de datos: MySQL o MariaDB en ejecución
* Node.js y npm (si se compila assets front-end)
* Editor de código: Visual Studio Code (recomendado)

> **Nota:** Si no se utilizaron algunos componentes (por ejemplo `npm`), indícalo claramente en el README del repositorio.

---

## 3. Instalación y flujo de comandos utilizados

A continuación el flujo de comandos utilizado para preparar el proyecto y activar el scaffolding de autenticación (ejemplo con `laravel/ui` y con alternativas):

```bash
# 1. Crear proyecto (si aplica)
composer create-project laravel/laravel nombre-proyecto
# o usando el instalador de Laravel
laravel new nombre-proyecto

cd nombre-proyecto

# 2. Instalar dependencias PHP (si clonaste el repo)
composer install

# 3. Copiar .env y generar key
cp .env.example .env       # en Windows: copy .env.example .env
php artisan key:generate

# 4. Editar .env -> configurar DB
# DB_DATABASE=nombre_db
# DB_USERNAME=root
# DB_PASSWORD=

# 5. Ejecutar migraciones
php artisan migrate

# 6. Instalar scaffolding de autenticación (ejemplo con laravel/ui)
composer require laravel/ui
php artisan ui bootstrap --auth
npm install && npm run dev          # o: composer run dev (según composer.json)

# 7. Levantar servidor de desarrollo
php artisan serve
# o, si usas entornos con Apache/XAMPP, usar la carpeta htdocs/www

# 8. Opcional: limpiar cache de config
php artisan config:clear
php artisan config:cache
```

**Comandos útiles**

* `composer install` — instala dependencias exactas del composer.lock.
* `composer update` — actualiza dependencias según composer.json.
* `php artisan migrate` — aplica migraciones pendientes.
* `php artisan migrate:rollback` — revierte la última migración.
* `php artisan key:generate` — genera APP_KEY en `.env`.

---

## 4. Estructura del proyecto y explicación breve de MVC

* **app/Models/**: Contiene los Modelos (representación de tablas/entidades). Ej. `User.php`.
* **app/Http/Controllers/**: Controladores que reciben peticiones, ejecutan lógica y retornan vistas o respuestas. Ej. `Auth` controllers generados por el scaffold.
* **routes/web.php**: Define las rutas web que apuntan a controladores o closures y las vistas.
* **resources/views/**: Vistas (`Blade` templates) responsables de la presentación (HTML/CSS/JS).
* **database/migrations/**: Archivos PHP que describen la estructura de las tablas.

**Resumen MVC**

* *Model*: Gestiona datos y reglas de negocio (ORM Eloquent).
* *View*: Plantillas Blade que muestran la interfaz.
* *Controller*: Conecta modelos y vistas, procesa peticiones y valida datos.

---

## 5. Base de datos

### Archivo `.env` (fragmento relevante)

```dotenv
APP_NAME=Laravel
APP_ENV=local
APP_KEY=base64:XXXXXXXXXXXX
APP_DEBUG=true
APP_URL=http://localhost:8000

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=nombre_lab
DB_USERNAME=root
DB_PASSWORD=
```

### Migraciones

* Ejecutadas con: `php artisan migrate`
* Si aparece error de longitud de cadena en MySQL (índice), usar en `AppServiceProvider`:

```php
use Illuminate\Support\Facades\Schema;

public function boot()
{
    Schema::defaultStringLength(191);
}
```

### Respaldo (backup) de la base de datos

* Genera un respaldo y colócalo en el repositorio dentro de `database/backups/`.

Ejemplo de comando para exportar (MySQL):

```bash
mysqldump -u root -p nombre_lab > database/backups/backup_nombre_lab.sql
```

Ejemplo de restauración:

```bash
mysql -u root -p nombre_lab < database/backups/backup_nombre_lab.sql
```

> **Archivos incluidos** (sugeridos en el repo):
>
> * `database/backups/backup_nombre_lab.sql` (backup de la BD usada en el laboratorio)

---

## 6. Resultado (captura de pantalla)

Incluye en el repositorio una imagen con el resultado visible (por ejemplo `docs/screenshot_result.png`). Puedes insertar la imagen en este README con Markdown:

```markdown
![Resultado del laboratorio](docs/screenshot_result.png)
```

> **Instrucciones:** toma una captura de la página de Login/Registro funcionando y súbela a `docs/` o `screenshots/` en el repositorio.

---

## 7. Dificultades y soluciones (ejemplos)

1. **Error: "The application encryption key has not been specified."**

   * *Causa:* APP_KEY vacío en `.env`.
   * *Solución:* `php artisan key:generate` y luego `php artisan config:clear`.

2. **Errores de migración por longitud de cadenas / índices en MySQL**

   * *Causa:* Versión de MySQL / collations que limitan longitudes.
   * *Solución:* `Schema::defaultStringLength(191);` en `AppServiceProvider@boot`.

3. **`Script dev not defined in composer.json`**

   * *Causa:* El `composer.json` no tiene script `dev`.
   * *Solución:* Usar `npm run dev` o agregar en `composer.json`:

```json
"scripts": {
  "dev": "vite",
  "build": "vite build"
}
```

4. **Problemas con `npm install` o `node_modules`**

   * *Solución:* Borrar `node_modules/` y `package-lock.json` y ejecutar `npm install` de nuevo. Verificar versión de Node recomendada.

5. **Error 500 en servidor**

   * *Diagnóstico:* Revisar `storage/logs/laravel.log` y verificar `.env` y `APP_KEY`.

---

## 8. Referencias / Fuentes consultadas

> Se recomienda citar al menos tres fuentes. A continuación ejemplos recomendados (agrega más si consultaste otros recursos):

* [https://laravel.com/docs](https://laravel.com/docs)
* [https://laracasts.com/discuss](https://laracasts.com/discuss)
* [https://stackoverflow.com/questions/tagged/laravel](https://stackoverflow.com/questions/tagged/laravel)
* (Agregar enlaces a tutoriales o guías específicas que hayas consultado)

---

## 9. Fecha de ejecución del laboratorio

**Fecha de ejecución:** 28 de septiembre de 2025

> (Coloca la fecha exacta en que realizaste la práctica si fue diferente.)

---

## 10. Footer (datos del desarrollador)

> Este laboratorio ha sido desarrollado por el estudiante de la Universidad Tecnológica de Panamá:

* **Nombre:** `TU_NOMBRE_AQUI`
* **Correo:** `tu_correo@ejemplo.com`
* **Curso:** Ingeniería Web — (indicar sección / código)
* **Instructor del Laboratorio:** Ing. Irina Fong

---

### Notas finales

* Acomoda las rutas de imágenes y del respaldo de la base de datos en el repositorio antes de enlazarlas en el README.
* Verifica que `composer.lock`, `package-lock.json` y los respaldos no contengan información sensible antes de subirlos al repositorio público.

---

**Si deseas, puedo**:

* Personalizar el README con tu nombre, correo, captura de pantalla y el backup de la BD (si me proporcionas los archivos),
* Generar el archivo `backup_nombre_lab.sql` (si me das snapshot/DB export),
* Ajustar el README a otro formato (por ejemplo incluir badges, iconos de tecnologías o plantilla visual).

¡Listo! Revisa el contenido y dime si quieres que lo adapte con tus datos reales (nombre, correo, capturas y backup).
