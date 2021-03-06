# Without install script

Install playSMS without using install script.

Please note that before following below steps you need to cover all steps required in [Requirements setup on Ubuntu](Requirements-setup-on-Ubuntu.md).

Please also note that the current official release is **playSMS version 1.3.1** and we will use that.

1.  Extract playSMS package and go there (For example in /usr/local/src)

    ```
    sudo tar -zxf playsms-1.3.1.tar.gz -C /usr/local/src
    ls -l /usr/local/src/
    cd /usr/local/src/playsms-1.3.1/
    ```

2.  Run getcomposer.sh

    ```
    sudo ./getcomposer.sh
    ```

    You may see the following warning, that can safely be ignored:
    ```
    Warning: Ambiguous class resolution, "PEAR_ErrorStack" was found in both "/usr/local/src/playsms-1.3.1/web/lib/composer/vendor/pear/pear/PEAR/ErrorStack.php" and "/usr/local/src/playsms-1.3.1/web/lib/composer/vendor/pear/pear/PEAR/ErrorStack5.php", the first will be used.
    ```

3.  Create playSMS web root, log, lib and set ownership to user www-data or web server user

    Assumed that your web root is `/var/www/html` and your web server user is `www-data`

    ```
    sudo mkdir -p /var/www/html/playsms /var/log/playsms /var/lib/playsms
    sudo chown -R www-data /var/www/html/playsms /var/log/playsms /var/lib/playsms
    ```

    Please note that there are Linux distributions using `apache` as web server user instead of `www-data`

    Also note that there are Linux distributions set `/var/www` as web root instead of `/var/www/html`

4.  Copy files and directories inside `web` directory to playSMS web root and set ownership to web server user

    ```
    sudo cp -R web/* /var/www/html/playsms
    sudo chown -R www-data /var/www/html/playsms
    ```

5.  Setup database (import database)

    ```
    mysqladmin -u root -p create playsms
    mysql -u root -p playsms < db/playsms.sql
    ```

6.  Copy config-dist.php to config.php and then edit config.php

    ```
    sudo cp /var/www/html/playsms/config-dist.php /var/www/html/playsms/config.php
    sudo nano /var/www/html/playsms/config.php
    ```

    Please read and fill all fields with correct values

7.  Enter daemon/linux directory, copy files and folder inside

    ```
    sudo cp daemon/linux/etc/playsmsd.conf /etc/playsmsd.conf
    sudo cp daemon/linux/bin/playsmsd /usr/local/bin/playsmsd
    ```

8.  Just to make sure every paths are correct, please edit /etc/playsmsd.conf

    ```
    nano /etc/playsmsd.conf
    ```

    Make sure that `PLAYSMS_PATH` is pointing to a correct playSMS installation path (in this example to `/var/www/html/playsms`)

    Also Make sure that `PLAYSMS_BIN` is pointing to a correct playSMS daemon scripts path (in this example to `/usr/local/bin`)

9.  Start playsmsd now from Linux console, no need to reboot

    ```
    sudo playsmsd start
    ```

10. Configure `rc.local` to get playsmsd started on boot

    Look for `rc.local` on `/etc`, `/etc/init.d` or `/etc/rc.d/init.d`

    When you found it edit that `rc.local` and put:

    `/usr/local/bin/playsmsd start`

    on the bottom of the file (before exit if theres an exit command).

    This way playsmsd will start automatically on boot.

Note:

* After successful installation, please run command `ps ax` and see if playsmsd is running

  ```
  ps ax | grep playsms
  4069 pts/12  S    0:00 /usr/bin/php -q /usr/local/bin/playsmsd /etc/playsmsd.conf schedule
  4071 pts/12  S    0:00 /usr/bin/php -q /usr/local/bin/playsmsd /etc/playsmsd.conf dlrssmsd
  4073 pts/12  S    0:00 /usr/bin/php -q /usr/local/bin/playsmsd /etc/playsmsd.conf recvsmsd
  4075 pts/12  S    0:00 /usr/bin/php -q /usr/local/bin/playsmsd /etc/playsmsd.conf sendsmsd
  ```

* Run several checks

  ```
  sudo playsmsd status
  sudo playsmsd check
  ```

* Stop here and review your installation steps when playsmsd is not running
* Consider to ask question in playSMS forum when you encountered a problem
* If all seems to be correctly installed you may try to login from web browser

  ```
  URL                    : http://[your web server IP]/playsms/
  Default admin username : admin
  Default admin password : admin
  ```
