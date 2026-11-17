# Laravel-Stellar

A Laravel 10 website on the "Stellar" HTML template with a self-written, reusable news module (local Composer package) managed through the Orchid admin panel.

## Context

A learning project by Oleksandr Karabudka. The main work was done in August and September 2023; in July 2024 the layouts, templates, menu and environment settings were reworked. The focus was on packaging a feature as a standalone Laravel module (its own namespace, service provider, migrations, config, translations, views and admin screens) and plugging it into an application and into Orchid.

## Features

- Public site based on the "Stellar" template by HTML5 UP, rendered through Blade layouts.
- News list on the home page (`<x-akrbdk-news-itemsList />` component), a news detail page at `/news/{alias}` with a list of other recommended items.
- News items with category, active flag, sort order, publish date, active-from/active-to period, preview and body text, preview and main image, locale and alias.
- Orchid admin screens for news categories and news items: lists with filters, edit forms (Quill editor, image upload, date pickers, category relation), and per-category item lists.
- Admin menu entries generated dynamically for every news category, protected by the `package.news.access` permission.
- Factories and a category seeder for demo data.

## Tech stack

- PHP 8.1+, Laravel 10
- Orchid Platform 14
- Local Composer package `akrbdk/laravel-module-news` (path repository `packages/*`)
- SQLite (default)
- Vite; static template assets (CSS, Sass, jQuery) in `public/assets`
- Docker Compose (nginx 1.20, PHP-FPM 8.2), Makefile
- Orchestra Testbench (dev dependency for package testing)

## Architecture

- The news feature lives in `packages/news` and is required by the root `composer.json` through a path repository, so it can be developed in place and extracted later.
- Layered design inside the package: Eloquent models, repositories behind interfaces (`Repositories/Contracts`), a `NewsService` for business logic, and Blade view components with DTOs. Interfaces are bound to implementations in `NewsServiceProvider::$bindings`.
- Repositories encapsulate visibility rules (active flag, active-from/active-to window, active category), so components and screens do not repeat them.
- `NewsServiceProvider` loads routes, migrations, views, translations and config and makes them publishable; `NewsOrchidServiceProvider` registers the admin menu and permission.
- Package config (`packages/news/config/config.php`) controls the number of items, similar items and the date format.

## Getting started

Requires Docker, Docker Compose and make.

```bash
sh docker/conf/ssl/generate.sh       # create the local self-signed TLS certificate
cp .env.example .env
touch database/database.sqlite       # default SQLite database file
make install-backend                 # starts containers, composer install, storage:link, key:generate
```

Then, inside the `php` container (`make php` opens a shell):

```bash
php artisan migrate
php artisan orchid:admin <name> <email> <password>
php artisan db:seed --class=\\Akrbdk\\News\\Database\\Seeders\\CategorySeeder
```

The site runs on http://localhost:90 and the admin panel on http://localhost:90/admin (`HTTP_PORT=90` in `.env.example`). `make down` stops the containers, `make clear-cache` clears Laravel caches.

## Testing

Only the default Laravel example tests are included; the news package has no tests yet.

```bash
docker compose exec php php artisan test
```

## Status

Finished learning project (2023, reworked in 2024), not maintained.

## License

MIT, see [LICENSE](LICENSE). The "Stellar" template by HTML5 UP is licensed under CCA 3.0 (see the header of `public/assets/css/main.css`).
