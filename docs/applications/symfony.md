---
title: Symfony
---

# Symfony

Symfony is the leading PHP framework to create websites and web applications. Built on top of the Symfony Components. 

## Requirements

- Set `Build Pack` to `nixpacks`
- Set `APP_ENV`
- Set `APP_SECRET`
- Set `NIXPACKS_PHP_FALLBACK_PATH` to `/index.php`
- Set `NIXPACKS_PHP_ROOT_DIR` to `/app/public`
- Set `Ports Exposes` to `80`

### Database migrations

If you use Doctrine, you can add the following `Post-deployment script` :

`php bin/console doctrine:migrations:migrate --all-or-nothing --no-interaction`

### Other components

If your application needs a database or Redis, you can simply create them beforehand in the Coolify dashboard.

You will receive the connection strings which you can use in your application and set them as environment variables:

```bash
DATABASE_URL=postgresql://app:!ChangeMe!@127.0.0.1:5432/app?serverVersion=16&charset=utf8

REDIS_HOST=<REDIS_HOST>
REDIS_PASSWORD=null
REDIS_PORT=6379
```

### Trusted proxy

You might need to configure the [trusted proxy](https://symfony.com/doc/current/deployment/proxies.html) :

- Set the environment variable `TRUSTED_PROXIES` with the IP of your server
- Add the following Symfony configuration :

```yaml
# config/packages/framework.yaml

framework:
    trusted_proxies: "%env(TRUSTED_PROXIES)%"
    trusted_headers: ['x-forwarded-for', 'x-forwarded-host', 'x-forwarded-proto', 'x-forwarded-port', 'x-forwarded-prefix']
```