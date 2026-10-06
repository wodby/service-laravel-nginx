# Nginx for Laravel on Wodby

What this service adds to the PHP Nginx service it is based on.

## Preset

The service sets `NGINX_VHOST_PRESET` to `laravel`, the image's rule set for Laravel. Its template is declared as a config file of this service and can be overridden there. It behaves like the PHP preset, with one addition: the asset routes of Livewire and Flux (`/livewire/livewire.js`, `/livewire/preview-file`, `/livewire-<hash>/…`, `/flux/flux.js`, `/flux/flux.css` and their minified forms) are passed to Laravel when no such file exists and are sent without the long cache lifetime of static files. Publishing those assets is not required for them to load.

## Document root and build

- The `docroot` setting (variable `DOCROOT_SUBDIR`) defaults to `public`. It is a setting of this service, not shared with the PHP service.
- Only that subdirectory of the PHP service's source is copied into this image, at the same path as in the PHP image. The application code, `vendor` and `.env` are not in the web server image.
- Assets must be in `public` when the image is built: front-end build output goes there in the build pipeline.

## Files

- The storage volume of the linked PHP service is mounted here at `/mnt/files`.
- On start, the service's init action (`WODBY2_SERVICE_INIT_ACTION`, here `init`) makes `storage/app/public` and `public/storage` links to `/mnt/files/public`. Files the application stores on its public disk are then served by Nginx directly from the volume under `/storage/`. `php artisan storage:link` is not needed.
- The link cannot be made when `public/storage` in the image holds anything other than a `.gitignore`.

## Check the result

- `nginx -T` shows the preset and document root in effect.
- `ls -l public/storage` inside this service shows the link to the volume.
