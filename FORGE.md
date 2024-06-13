## Laravel Forge Deployment Script

```shell
# Change to the project directory
cd $FORGE_SITE_PATH

# Turn on maintenance mode
php artisan down || true

# Pull the latest changes from the git repository
git reset --hard
git clean -df
git pull origin $FORGE_SITE_BRANCH

DIR="vendor"
if [ -d "$DIR" ]; then
$FORGE_COMPOSER update --no-interaction --prefer-dist --optimize-autoloader
else
$FORGE_COMPOSER install --no-interaction --prefer-dist --optimize-autoloader --no-dev
fi

( flock -w 10 9 || exit 1
echo 'Restarting FPM...'; sudo -S service $FORGE_PHP_FPM reload ) 9>/tmp/fpmlock

#if [ -f artisan ]; then
  #$FORGE_PHP artisan migrate --force
  $FORGE_PHP artisan cache:clear
  $FORGE_PHP artisan route:clear
  #$FORGE_PHP artisan config:cache
  #$FORGE_PHP artisan view:cache
  $FORGE_PHP artisan up
#fi
```
