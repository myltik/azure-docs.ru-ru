## <a name="install-wordpress"></a>Установка WordPress

Чтобы проверить работу стека, установите пример приложения. Например, выполните приведенные ниже шаги, чтобы установить платформу [WordPress](https://wordpress.org/) с открытым кодом для создания веб-сайтов и блогов. Для других рабочих нагрузок потребуется установка платформ [Drupal](http://www.drupal.org) и [Moodle](https://moodle.org/). 

Эта программа установки WordPress предназначена для подтверждения концепции. Дополнительные сведения об установке в рабочей среде и параметры см. в [документации WordPress](https://codex.wordpress.org/Main_Page). 



### <a name="install-the-wordpress-package"></a>Установка пакета WordPress

Выполните следующую команду:

```bash
sudo apt install wordpress
```

### <a name="configure-wordpress"></a>Настройка WordPress

Настройте WordPress, чтобы использовать MySQL и PHP. Чтобы открыть необходимый текстовый редактор и создать файл `/etc/wordpress/config-localhost.php`, выполните следующую команду:

```bash
sudo sensible-editor /etc/wordpress/config-localhost.php
```
Скопируйте следующие строки в файл, заменив пароль базы данных на *свой_пароль*. Другие значения оставьте без изменений. Затем сохраните файл.

```php
<?php
define('DB_NAME', 'wordpress');
define('DB_USER', 'wordpress');
define('DB_PASSWORD', 'yourPassword');
define('DB_HOST', 'localhost');
define('WP_CONTENT_DIR', '/usr/share/wordpress/wp-content');
?>
```

В рабочей папке создайте текстовый файл `wordpress.sql`, чтобы настроить базу данных WordPress. 

```bash
sudo sensible-editor wordpress.sql
```

Добавьте следующие команды, заменив пароль базы данных на *свой_пароль*. Другие значения оставьте без изменений. Затем сохраните файл.

```sql
CREATE DATABASE wordpress;
GRANT SELECT,INSERT,UPDATE,DELETE,CREATE,DROP,ALTER
ON wordpress.*
TO wordpress@localhost
IDENTIFIED BY 'yourPassword';
FLUSH PRIVILEGES;
```


Выполните следующую команду для создания базы данных.

```bash
cat wordpress.sql | sudo mysql --defaults-extra-file=/etc/mysql/debian.cnf
```

После выполнения команды удалите файл `wordpress.sql`.

Переместите файлы установки WordPress в корневой каталог документов на веб-сервере.

```bash
sudo ln -s /usr/share/wordpress /var/www/html/wordpress

sudo mv /etc/wordpress/config-localhost.php /etc/wordpress/config-default.php
```

Теперь можно завершить настройку WordPress и выполнить публикацию на платформе. Откройте браузер и перейдите на страницу `http://yourPublicIPAddress/wordpress`. Замените общедоступный IP-адрес своей виртуальной машины. Она должна выглядеть, как показано ниже.

![Страница установки WordPress](./media/virtual-machines-linux-tutorial-wordpress/wordpressstartpage.png)