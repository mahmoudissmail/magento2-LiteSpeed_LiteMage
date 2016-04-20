# magento2-LiteSpeed_LiteMage

## Description

The LiteMage Cache module provides an improved caching solution alternative to the PageCache module and Varnish Cache. The module will replace the Varnish option to the cache selector in the administrator interface for easy switching. 

For most use cases, LiteMage Cache can improve your stores' performance right out of the box. LiteMage Cache also reduces the complexity of your stack; only the LiteMage Extension and LiteSpeed Web Server are required\*. There is no need for an NGINX reverse proxy nor a Varnish Cache instance because the server can handle HTTPS and HTTP/2 requests and cache the pages all in one application.

LiteMage Cache operates by taking information from Magento and instructing the LiteSpeed server on how to cache the page. Once the server knows how to cache it, future requests to the same page will be served directly from the server. Requests will never hit the Magento backend until a change occurs.

\* For clustered setups, LiteSpeed Load Balancer is needed.

## Prerequisites
LiteSpeed Web Server Enterprise Edition with Magento 2 set up and working.

### PHP

Magento 2 requires PHP versions 7.0.2+, 5.6.x, or 5.5.x. In the command line, type `php -v` to get the php version used by the command line interface. This version may be different than the one used by the server.
If the version number does not match the one used within the server, there may be some compatibility issues.

Run the command `which php` to get the directory of the php binary. Then, change the current binary name to something different as a backup (e.g. `mv php php-orig`). Lastly, symbolic link or copy the binary installed in the LSWS directory to the directory returned by the `which` command. If the which command doesn't return anything, we recommend using `/usr/local/bin/` or `/usr/bin/`.

Example using PHP 7 with `which` returning `/usr/bin/php`:
```
mv /usr/bin/php /usr/bin/php-orig
ln -s /usr/local/lsws/lsphp70/bin/php /usr/bin/php
```
Run `php -v` afterwards. If the steps were done correctly, the version number should match the server's php version.

## Installation

The following steps assume that the Prerequisites mentioned above are met.

1. Access a terminal as the Magento owner and cd to the Magento 2 root directory.
  * Log in as user1
  * su user1
  * or Log in as root, su user1

   Then cd into the Magento 2 root directory.
2. Set the store to developer mode:

    ```
    php bin/magento deploy:mode:set developer
    ```
3. Download the zip package file from this repository.
4. Unzip the source package. The unzipped directory should be named magento2-LiteSpeed_LiteMage-master.
5. In the Magento 2 root directory, run the following command to create the needed directories:
    ```
    mkdir -p app/code/LiteSpeed/Litemage
    ```
6. Move the contents from the GitHub directory to the newly created directory:
    ```
    mv /path/to/magento2-LiteSpeed_LiteMage-master/* app/code/LiteSpeed/Litemage/
    ```
7. Confirm that the contents' owner is consistent with the other magento store files.
8. Enable LiteMage 2 in magento:
    ```
    php bin/magento module:enable LiteSpeed_Litemage
    ```
9. Upgrade the Magento setup:
    ```
    php bin/magento setup:upgrade
    ```
10. Recompile using the multi-tenant compiler. The single-tenant compiler has some issues. [link](http://devdocs.magento.com/guides/v2.0/config-guide/cli/config-cli-subcommands-compiler.html#config-cli-subcommands-single)
    ```
    php bin/magento setup:di:compile-multi-tenant
    ```
11. Once finished, delete `var/di/relations.ser` to work around a Magento bug. [link](https://github.com/magento/magento2/issues/4070)
    ```
    rm var/di/relations.ser
    ```
12. If desired, switch back to production mode. The previous step may need to be repeated after the mode switch.

### Enable LiteMage after installation:

1. In the Magento 2 root directory's .htaccess file, add the following line:
    ```
    LiteMage on
    ```
2. Log into the Magento admin page.
3. In Store -> Configuration -> Advanced -> System, make sure LiteMage is enabled and the Full Page Cache setting has LiteMage selected.
4. In System -> Cache Management, refresh configurations and page cache.
5. Visit and refresh a page that should be cache enabled. Look for the LiteMage related response headers.

   Example:
    ```
    X-LiteSpeed-Cache: litemage,hit
    ```

## Configuration

No further changes to your Magento 2 configurations should be necessary as LiteMage honors the same cacheable settings as varnish in the layout xml files.

## Todo
* Add Cache Warm up
* Add Customizable Configurations
* Add Unit Tests

