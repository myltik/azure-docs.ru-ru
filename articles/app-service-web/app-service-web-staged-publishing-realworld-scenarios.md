<properties
   pageTitle="Эффективное использование сред разработки и операций для веб-приложений"
   description="Узнайте, как использовать слоты развертывания для настройки нескольких сред разработки приложения и управления ими"
   services="app-service\web"
   documentationCenter=""
   authors="sunbuild"
   manager="yochayk"
   editor=""/>

<tags
   ms.service="app-service"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="web"
   ms.date="09/24/2015"
   ms.author="sumuth"/>

# Эффективное использование сред разработки и операций для веб-приложений

В этой статье показано, как настроить развертывание разных версий веб-приложений, например в среде разработки, промежуточной среде, среде для проверки качества и рабочей среде, и управлять ими. Каждая версия приложения может рассматриваться как среда разработки для конкретной цели в рамках процесса развертывания. Например, среда для проверки качества может использоваться разработчиками для тестирования перед переносом изменений в рабочую среду. Настройка нескольких сред разработки может вызывать затруднения, поскольку необходимо отслеживать ресурсы (вычислительные ресурсы, веб-приложения, базы данных, кэш и т. д.) в этих средах и управлять перемещением содержимого из одной среды в другую.

## Настройка непроизводственной среды (для подготовки, разработки, контроля качества)
После того как вы создадите и настроите рабочую версию веб-приложения, необходимо создать непроизводственную среду. Для использования слотов развертывания убедитесь, что вы работаете в рамках плана обслуживания **Standard** или **Premium**. Слоты развертывания фактически являются динамическими веб-приложениями со своими собственными именами узлов. Содержимое веб-приложений и элементы конфигурации можно перемещать между двумя слотами (включая производственный). Развертывание приложения в области развертывания дает следующие преимущества:

1. В промежуточном слоте развертывания можно проверить изменения в веб-приложении до того, как они будут отправлены в рабочий слот.
2. Развертывание веб-приложения в промежуточном слоте и последующее перемещение в производственный позволяет подготовить все экземпляры слота до того, как они будут перемещены в производственную среду. Это сокращает время простоя при развертывании веб-приложения. Перенаправление трафика не вызывает затруднений, а запросы не теряются в результате операций подкачки. Весь этот рабочий процесс можно автоматизировать, настроив функцию [автоматического переноса](web-sites-staged-publishing/#configure-auto-swap-for-your-web-app), когда проверка перед переносом не требуется.
3. После переноса в слоте промежуточного развертывания окажется предыдущее рабочее веб-приложение. Если изменения в рабочем слоте не соответствуют вашим ожиданиям, вы можете мгновенно выполнить откат к последнему удачному веб-приложению.

Сведения о настройке промежуточного слота развертывания см. в статье [Настройка промежуточных сред для веб-приложений в службе приложений Azure](web-sites-staged-publishing). Каждая среда должна включать собственный набор ресурсов. Например, рабочая и промежуточная версии веб-приложения должны использовать разные базы данных. Чтобы настроить промежуточную среду, добавьте для нее такие ресурсы, как база данных, хранилище и кэш.

## Примеры использования нескольких сред разработки

Все проекты разработки необходимо вести в рамках системы управления исходным кодом как минимум с двумя средами (средой разработки и рабочей). Однако при использовании систем управления контентом, платформ приложений и пр. можно столкнуться с ситуацией, когда приложение не поддерживает по умолчанию тот или иной сценарий. Это справедливо для некоторых популярных платформ, рассмотренных ниже. При работе с CMS и платформами возникает множество вопросов.

1. Как разделить содержимое между средами?
2. Какие файлы можно менять, не опасаясь, что изменение потребует обновления версии платформы?
3. Как управлять конфигурацией каждой среды?
4. Как управлять обновлением модулей или базовой платформы?

Есть много способов настройки сред в проекте. Ниже приведен лишь один пример, соответствующий рассматриваемому типу приложения.

### WordPress
Из этого раздела вы узнаете, как настроить рабочий процесс развертывания, используя слоты для WordPress. WordPress, как и большинство решений для управления контентом, по умолчанию не поддерживает работу с несколькими средами. Веб-приложения службы приложений предоставляют несколько функций, облегчающих хранение параметров конфигурации вне кода.

Прежде чем создавать промежуточный слот развертывания, настройте поддержку нескольких сред кодом приложения. Для этого добавьте в начало файла `wp-config.php` в веб-приложении в локальной среде разработки следующий код. Это позволит приложению использовать конфигурацию в зависимости от выбранной среды.

```
// Support multiple environments
// set the config file based on current environment
if (strpos($_SERVER['HTTP_HOST'],'localhost') !== false) {
    // local development
    $config_file = 'config/wp-config.local.php';
}
elseif  ((strpos(getenv('WP_ENV'),'stage') !== false) ||  (strpos(getenv('WP_ENV'),'prod' )!== false )){
      //single file for all azure development environments
      $config_file = 'config/wp-config.azure.php';
}
$path = dirname(__FILE__) . '/';
if (file_exists($path . $config_file)) {
// include the config file if it exists, otherwise WP is going to fail
require_once $path . $config_file;
```

Создайте в корне веб-приложения папку `config` и добавьте в нее два файла: `wp-config.azure.php` и `wp-config.local.php` (для среды Azure и локальной среды).

Скопируйте в файл `wp-config.local.php` следующий код:

```
<?php
// MySQL settings
/** The name of the database for WordPress */

define('DB_NAME', 'yourdatabasename');

/** MySQL database username */
define('DB_USER', 'yourdbuser');

/** MySQL database password */
define('DB_PASSWORD', 'yourpassword');

/** MySQL hostname */
define('DB_HOST', 'localhost');
/**
 * For developers: WordPress debugging mode.
 * * Change this to true to enable the display of notices during development.
 * It is strongly recommended that plugin and theme developers use WP_DEBUG
 * in their development environments.
 */
define('WP_DEBUG', true);

//Security key settings
define('AUTH_KEY',         'put your unique phrase here');
define('SECURE_AUTH_KEY',  'put your unique phrase here');
define('LOGGED_IN_KEY',    'put your unique phrase here');
define('NONCE_KEY',        'put your unique phrase here');
define('AUTH_SALT',        'put your unique phrase here');
define('SECURE_AUTH_SALT', 'put your unique phrase here');
define('LOGGED_IN_SALT',   'put your unique phrase here');
define('NONCE_SALT',       'put your unique phrase here');

/**
 * WordPress Database Table prefix.
 *
 * You can have multiple installations in one database if you give each a unique
 * prefix. Only numbers, letters, and underscores please!
 */
$table_prefix  = 'wp_';
```

Такая настройка ключей безопасности помогает предотвратить хакерские атаки на веб-приложение, поэтому используйте уникальные значения. Если вы хотите создать ключи безопасности (пары «ключ-значение»), перейдите к автоматическому генератору по этой [ссылке](https://api.wordpress.org/secret-key/1.1/salt).

Скопируйте в файл `wp-config.azure.php` следующий код:


```
<?php
// MySQL settings
/** The name of the database for WordPress */

define('DB_NAME', getenv('DB_NAME'));

/** MySQL database username */
define('DB_USER', getenv('DB_USER'));

/** MySQL database password */
define('DB_PASSWORD', getenv('DB_PASSWORD'));

/** MySQL hostname */
define('DB_HOST', getenv('DB_HOST'));

/**
* For developers: WordPress debugging mode.
*
* Change this to true to enable the display of notices during development.
* It is strongly recommended that plugin and theme developers use WP_DEBUG
* in their development environments.
* Turn on debug logging to investigate issues without displaying to end user. For WP_DEBUG_LOG to
* do anything, WP_DEBUG must be enabled (true). WP_DEBUG_DISPLAY should be used in conjunction
* with WP_DEBUG_LOG so that errors are not displayed on the page */

*/
define('WP_DEBUG', getenv('WP_DEBUG'));
define('WP_DEBUG_LOG', getenv('TURN_ON_DEBUG_LOG'));
define('WP_DEBUG_DISPLAY',false);

//Security key settings
/** If you need to generate the string for security keys mentioned above, you can go the automatic generator to create new keys/values: https://api.wordpress.org/secret-key/1.1/salt **/
define('AUTH_KEY' ,getenv('DB_AUTH_KEY'));
define('SECURE_AUTH_KEY',  getenv('DB_SECURE_AUTH_KEY'));
define('LOGGED_IN_KEY', getenv('DB_LOGGED_IN_KEY'));
define('NONCE_KEY', getenv('DB_NONCE_KEY'));
define('AUTH_SALT',  getenv('DB_AUTH_SALT'));
define('SECURE_AUTH_SALT', getenv('DB_SECURE_AUTH_SALT'));
define('LOGGED_IN_SALT',   getenv('DB_LOGGED_IN_SALT'));
define('NONCE_SALT',   getenv('DB_NONCE_SALT'));

/**
* WordPress Database Table prefix.
*
* You can have multiple installations in one database if you give each a unique
* prefix. Only numbers, letters, and underscores please!
*/
$table_prefix  = getenv('DB_PREFIX');
```

#### Использование относительных путей
В завершение следует разрешить приложению WordPress использовать относительные пути. WordPress хранит сведения об URL-адресах в базе данных. Использование абсолютных путей усложняет перемещение из одной среды в другую, так как базу данных необходимо обновлять при каждом перемещении из локальной среды в промежуточную и из промежуточной в рабочую. Чтобы снизить вероятность возникновения проблем, вызванных обновлением базы данных при каждом переносе ресурсов из одной среды в другую, используйте [подключаемый модуль для создания путей относительно корневого элемента](https://wordpress.org/plugins/root-relative-urls/), который можно установить с помощью панели администратора WordPress или загрузить вручную [здесь](https://downloads.wordpress.org/plugin/root-relative-urls.zip).

Добавьте следующие записи в файл `wp-config.php` перед комментарием `That's all, stop editing!`:

```
    define('WP_HOME', 'http://' . $_SERVER ['HTTP_HOST']);
    define('WP_SITEURL', 'http://' . $_SERVER ['HTTP_HOST']);
    define('WP_CONTENT_URL', '/wp-content');
    define('DOMAIN_CURRENT_SITE', $_SERVER['HTTP_HOST']);
```
Активируйте подключаемый модуль, используя меню `Plugins` в панели администратора WordPress. Сохраните постоянную ссылку на приложение WordPress.

#### Итоговая версия файла `wp-config.php`
Никакие основные обновления WordPress не будут оказывать влияния на файлы `wp-config.php`, `wp-config.azure.php` и `wp-config.local.php`. В результате файл `wp-config.php` будет выглядеть так:

```
<?php
/**
 * The base configurations of the WordPress.
 *
 * This file has the following configurations: MySQL settings, Table Prefix,
 * Secret Keys, and ABSPATH. You can find more information by visiting
 *
 * Codex page. You can get the MySQL settings from your web host.
 *
 * This file is used by the wp-config.php creation script during the
 * installation. You don't have to use the web web app, you can just copy this file
 * to "wp-config.php" and fill in the values.
 *
 * @package WordPress
 */

// Support multiple environments
// set the config file based on current environment
if (strpos($_SERVER['HTTP_HOST'],'localhost') !== false) { // local development
    $config_file = 'config/wp-config.local.php';
}
elseif  ((strpos(getenv('WP_ENV'),'stage') !== false) ||  (strpos(getenv('WP_ENV'),'prod' )!== false )){
    $config_file = 'config/wp-config.azure.php';
}


$path = dirname(__FILE__) . '/';
if (file_exists($path . $config_file)) {
    // include the config file if it exists, otherwise WP is going to fail
    require_once $path . $config_file;
}

/** Database Charset to use in creating database tables. */
define('DB_CHARSET', 'utf8');

/** The Database Collate type. Don't change this if in doubt. */
define('DB_COLLATE', '');


/* That's all, stop editing! Happy blogging. */

define('WP_HOME', 'http://' . $_SERVER['HTTP_HOST']);
define('WP_SITEURL', 'http://' . $_SERVER['HTTP_HOST']);
define('WP_CONTENT_URL', '/wp-content');
define('DOMAIN_CURRENT_SITE', $_SERVER['HTTP_HOST']);

/** Absolute path to the WordPress directory. */
if ( !defined('ABSPATH') )
	define('ABSPATH', dirname(__FILE__) . '/');

/** Sets up WordPress vars and included files. */
require_once(ABSPATH . 'wp-settings.php');
```

#### Настройка промежуточной среды
Предположим, что у вас уже есть веб-приложение WordPress, развернутое в Azure. Выполните вход на [портал предварительной версии Azure](http://portal.azure.com) и перейдите к веб-приложению WordPress. Вы также можете создать веб-приложение в магазине. Для получения дополнительных сведений щелкните [здесь](web-sites-php-web-site-gallery). Выберите «Параметры» -> «Слоты развертывания» -> «Добавить», чтобы создать слот развертывания с именем stage. Слот развертывания — это веб-приложение, которое использует те же ресурсы, что и основное веб-приложение, созданное выше.

![Создание промежуточного слота развертывания](./media/app-service-web-staged-publishing-realworld-scenarios/1setupstage.png)

Добавьте другую базу данных MySQL, например `wordpress-stage-db`, в группу ресурсов `wordpressapp-group`.

 ![Добавление базы данных MySQL в группу ресурсов](./media/app-service-web-staged-publishing-realworld-scenarios/2addmysql.png)

Обновите строки подключения для промежуточного слота развертывания, указав в них вновь созданную базу данных `wordpress-stage-db`. Обратите внимание, что рабочая версия веб-приложения `wordpressapp-group` и промежуточная версия `wordpressprodapp-stage` должны использовать разные базы данных.

#### Настройка параметров приложения с учетом конкретной среды
Разработчики могут хранить пары «ключ-значение» в Azure в разделе App Settings (параметры приложения) сведений о конфигурации веб-приложения. Во время выполнения веб-приложения службы приложений автоматически получают эти значения и делают их доступными коду, выполняемому в веб-приложении. Это дает положительный побочный эффект с точки зрения безопасности, так как конфиденциальная информация, например строки подключения к базам данных и пароли, никогда не отображаются в виде открытого текста в таких файлах, как `wp-config.php`.

Описанный ниже процесс объединяет изменения как файлов, так и базы данных для приложения WordPress:

- обновление версии WordPress;
- добавление, изменение или обновление подключаемых модулей;
- добавление, изменение или обновление тем.

Настройка параметров приложения:

- сведения о базе данных;
- включение и выключение ведения журнала для WordPress;
- параметры безопасности WordPress.

![Параметры приложения для веб-приложения WordPress](./media/app-service-web-staged-publishing-realworld-scenarios/3configure.png)

Убедитесь, что вы добавили следующие параметры приложения для рабочего веб-приложения и промежуточного слота. Обратите внимание, что рабочая и промежуточная версии веб-приложения должны использовать разные базы данных. Снимите флажок **Настройка слота** для всех параметров, кроме WP\_ENV. Он отвечает за смену конфигурации вашего веб-приложения, а также содержимого файлов и базы данных. Если флажок **Настройка слота** **установлен**, параметры веб-приложения и строки подключения не будут перемещаться из одной среды в другую при выполнении операции перемещения, поэтому любые изменения базы данных приведут к сбою рабочего веб-приложения.

Разверните веб-приложение в локальной среде разработки, чтобы затем переместить его и базу данных в промежуточную среду с помощью WebMatrix или других инструментов, таких как FTP, Git или PhpMyAdmin.

![Диалоговое окно публикации веб-приложения WordPress в WebMatrix](./media/app-service-web-staged-publishing-realworld-scenarios/4wmpublish.png)

Перейдите к веб-приложению в промежуточной среде и протестируйте его. Рассмотрим сценарий, в котором нужно обновить тему веб-приложения. Промежуточная версия выглядит так:

![Просмотр промежуточных веб-приложений до переключения слотов](./media/app-service-web-staged-publishing-realworld-scenarios/5wpstage.png)


 Если ничего менять не требуется, нажмите кнопку **Переместить** в параметрах промежуточного веб-приложения, чтобы переместить его содержимое в рабочую среду. В нашем примере каждая **операция перемещения** приводит к перемещению веб-приложения и базы данных между средами.

![Просмотр изменений при переключении для WordPress](./media/app-service-web-staged-publishing-realworld-scenarios/6swaps1.png)

 >[AZURE.NOTE]> Если в вашем сценарии требуется только передача файлов (без обновления базы данных), то перед выполнением операции перемещения **установите** флажок **Настройка слота** для всех *параметров приложения* и *параметров строки подключения*, связанных с базой данных, в колонке параметров веб-приложения на портале предварительной версии Azure. В этом случае параметры DB\_NAME, DB\_HOST, DB\_PASSWORD, DB\_USER и строка подключения по умолчанию не должны отображаться при предварительном просмотре изменений перед выполнением операции **перемещения**. При выполнении операции **перемещения** в веб-приложении WordPress будут обновлены **только** файлы.

Перед выполнением операции перемещения рабочее приложение WordPress выглядит так: ![Рабочее веб-приложение перед переключением слотов](./media/app-service-web-staged-publishing-realworld-scenarios/7bfswap.png)

После операции перемещения тема в рабочем веб-приложении будет обновлена.

![Рабочее веб-приложение после переключения слотов](./media/app-service-web-staged-publishing-realworld-scenarios/8afswap.png)

Если вам необходимо выполнить **откат**, перейдите к параметрам рабочего веб-приложения и щелкните **Переместить**, чтобы переместить веб-приложение и базу данных из рабочего слота в промежуточный. Имейте в виду: если в **операцию перемещения** включены изменения базы данных, то при повторном развертывании промежуточного веб-приложения потребуется развернуть изменения базы данных в текущую базу данных, на которую указывает промежуточное веб-приложение. Это может быть как предыдущая рабочая, так и промежуточная база данных.

#### Сводка
Обобщенно процесс для приложения с базой данных выглядит следующим образом.

1. Установка приложения в локальной среде.
2. Выбор конфигурации в зависимости от конкретной среды (локальное приложение и веб-приложение Azure).
3. Настройка промежуточной и рабочей сред в веб-приложениях службы приложений.
4. Синхронизация содержимого рабочей среды (файлы/код и база данных) с локальной и промежуточной средами (если ваше рабочее приложение уже выполняется в Azure).
5. Разработка приложения в локальной среде.
6. Перевод рабочего веб-приложения в режим обслуживания или блокировки для синхронизации содержимого базы данных в рабочей среде с промежуточной средой и средой разработки.
7. Развертывание в промежуточной среде и тестирование.
8. Развертывание в рабочей среде.
9. Повтор шагов 4–6.

### Umbraco
Из этого раздела вы узнаете, как использовать пользовательские модули в системе Umbraco CMS для развертывания в нескольких средах разработки и операций. В приведенном примере представлен другой подход к управлению развертыванием в нескольких средах разработки.

[Umbraco CMS](http://umbraco.com/) — популярное среди разработчиков CMS-решение на основе .NET, предоставляющее модуль [Courier2](http://umbraco.com/products/more-add-ons/courier-2) для переноса содержимого из среды разработки в промежуточную, а затем и в рабочую среду. Вы можете легко создать локальную среду разработки для веб-приложения Umbraco CMS с помощью Visual Studio или WebMatrix.

1. Чтобы создать веб-приложение Umbraco с помощью Visual Studio, щелкните [здесь](https://our.umbraco.org/documentation/Installation/install-umbraco-with-nuget).
2. Чтобы создать веб-приложение Umbraco с помощью WebMatrix, щелкните [здесь](http://umbraco.com/help-and-support/video-tutorials/getting-started/working-with-webmatrix).

Не забывайте удалять папку `install` приложения и никогда не переносите ее в промежуточную или рабочую среду. В этом учебнике мы будем использовать WebMatrix.

#### Настройка промежуточной среды
Предположим, у вас уже есть готовое веб-приложение Umbraco CMS. Создайте слот развертывания для веб-приложения Umbraco CMS в соответствии с приведенными выше инструкциями. Если приложения нет, вы можете создать его в магазине.

Обновите строки подключения для промежуточного слота развертывания, указав в них вновь созданную базу данных **umbraco-stage-db**. Ваше рабочее веб-приложение (umbraositecms-1) и промежуточное веб-приложение (umbracositecms-1-stage) **ДОЛЖНЫ** указывать на разные базы данных.

![Обновление строки подключения для промежуточного веб-приложения с новой промежуточной базой данных](./media/app-service-web-staged-publishing-realworld-scenarios/9umbconnstr.png)

Щелкните **Получить параметры публикации** для **промежуточного** слота развертывания. Будет загружен файл параметров публикации, в котором хранятся сведения, необходимые Visual Studio или WebMatrix для публикации приложения из локальной среды разработки в веб-приложение Azure.

 ![Получение параметров публикации для промежуточного веб-приложения](./media/app-service-web-staged-publishing-realworld-scenarios/10getpsetting.png)

- Откройте веб-приложение в локальной среде разработки **WebMatrix** или **Visual Studio**. В этом учебнике я использую WebMatrix, и сначала нам необходимо импортировать файл параметров публикации для промежуточного веб-приложения.

![Импорт параметров публикации в Umbraco с помощью Web Matrix](./media/app-service-web-staged-publishing-realworld-scenarios/11import.png)

- Просмотрите изменения в диалоговом окне и разверните локальное веб-приложение в веб-приложение Azure *umbracositecms-1-stage*. При развертывании файлов непосредственно в промежуточное веб-приложение исключаются все файлы в папке `~/app_data/TEMP/`, так как они будут повторно сгенерированы при первом запуске промежуточного веб-приложения. Также следует пропустить файл `~/app_data/umbraco.config`, так как он тоже будет создан заново.

![Просмотр изменений публикации в WebMatrix](./media/app-service-web-staged-publishing-realworld-scenarios/12umbpublish.png)

- После успешной публикации локального веб-приложения Umbraco в промежуточное веб-приложение перейдите к промежуточному веб-приложению и выполните несколько тестов, чтобы исключить возможные проблемы.

#### Настройка модуля развертывания Courier2
Используя модуль [Courier2](http://umbraco.com/products/more-add-ons/courier-2), вы можете щелчком правой кнопкой мыши передавать содержимое, таблицы стилей, модули разработки и многое другое из промежуточного веб-приложения в рабочее. Это значительно упрощает развертывание и снижает риск возникновения сбоев рабочего веб-приложения при развертывании обновлений. Приобретите лицензию на Courier2 для домена `*.azurewebsites.net` и пользовательского домена (например, http://abc.com). После этого поместите загруженный файл лицензии (файл LIC) в папку `bin`.

![Перенос файла лицензии в папку bin](./media/app-service-web-staged-publishing-realworld-scenarios/13droplic.png)

Загрузите пакет Courier2 [здесь](https://our.umbraco.org/projects/umbraco-pro/umbraco-courier-2/). Войдите в промежуточное веб-приложение, например http://umbracocms-site-stage.azurewebsites.net/umbraco, и в меню **Разработчик** выберите пункт **Пакеты**. Выберите команду **Установить** для локального пакета.

![Установщик пакета Umbraco](./media/app-service-web-staged-publishing-realworld-scenarios/14umbpkg.png)

Загрузите пакет Сourier2 с помощью установщика.

![Загрузка пакета модуля Courier](./media/app-service-web-staged-publishing-realworld-scenarios/15umbloadpkg.png)

Для настройки необходимо обновить файл courier.config в папке **Config** вашего веб-приложения.

```xml
<!-- Repository connection settings -->
  <!-- For each site, a custom repository must be configured, so Courier knows how to connect and authenticate-->
  <repositories>
        <!-- If a custom Umbraco Membership provider is used, specify login & password + set the passwordEncoding to clear:  -->
        <repository name="production web app" alias="stage" type="CourierWebserviceRepositoryProvider" visible="true">
            <url>http://umbracositecms-1.azurewebsites.net</url>
            <user>0</user>
            <!--<login>user@email.com</login> -->
            <!-- <password>user_password</password>-->
           <!-- <passwordEncoding>Clear</passwordEncoding>-->
           </repository>
  </repositories>
 ```

Under `<repositories>`, enter the production site URL and user information. If you are using default Umbraco Membership provider, then add the ID for the Administration user in <user> section . If you are using a custom Umbraco membership provider, use `<login>`,`<password>` to Courier2 module know how to connect to the production site. For more details, review the [documentation](http://umbraco.com/help-and-support/customer-area/courier-2-support-and-download/developer-documentation) for Courier module.

Similarly, install Courier module on your production site and configure it point to stage web app in its respective courier.config file as shown here

```xml
  <!-- Repository connection settings -->
  <!-- For each site, a custom repository must be configured, so Courier knows how to connect and authenticate-->
  <repositories> <!-- If a custom Umbraco Membership provider is used, specify login & password + set the passwordEncoding to clear:  --> <repository name="Stage web app" alias="stage" type="CourierWebserviceRepositoryProvider" visible="true"> <url>http://umbracositecms-1-stage.azurewebsites.net</url> <user>0</user> </repository> </repositories> ```

Откройте вкладку Courier2 в панели мониторинга веб-приложений Umbraco CMS и выберите расположения. Вы увидите имя репозитория, указанное в файле `courier.config`. Выполните это действие в рабочем и промежуточном веб-приложениях.

![Просмотр целевого репозитория веб-приложения](./media/app-service-web-staged-publishing-realworld-scenarios/16courierloc.png)

Теперь развернем часть содержимого промежуточного сайта в рабочей среде. Перейдите в раздел «Содержимое» и выберите существующую или создайте новую страницу. Выберем существующую страницу в разделе "Мое веб-приложение", заголовок которой изменен на **Приступая к работе — новый**, после чего нажмем кнопку **Сохранить и опубликовать**.

![Изменение заголовка страницы и публикация](./media/app-service-web-staged-publishing-realworld-scenarios/17changepg.png)

Выделите и *щелкните правой кнопкой мыши* измененную страницу, чтобы просмотреть все параметры. Щелкните **Courier**, чтобы открыть диалоговое окно развертывания. Щелкните **Развернуть**, чтобы инициировать развертывание.

![Диалоговое окно развертывания модуля Courier](./media/app-service-web-staged-publishing-realworld-scenarios/18dialog1.png)

Просмотрите изменения и нажмите кнопку «Продолжить».

![Просмотр изменений в диалоговом окне развертывания модуля Courier](./media/app-service-web-staged-publishing-realworld-scenarios/19dialog2.png)

Информация о результатах развертывания отображается в журнале развертывания.

 ![Просмотр журналов развертывания из модуля Courier](./media/app-service-web-staged-publishing-realworld-scenarios/20successdlg.png)

Перейдите к рабочему веб-приложению, чтобы проверить, отражены ли изменения.

 ![Переход к рабочему веб-приложению](./media/app-service-web-staged-publishing-realworld-scenarios/21umbpg.png)

Дополнительные сведения об использовании Courier см. в документации.

#### Обновление Umbraco CMS

Пакеты Courier невозможно развернуть при обновлении с одной версии Umbraco CMS до другой. При обновлении Umbraco CMS до новой версии необходимо проверить совместимость с пользовательскими модулями, модулями сторонних поставщиков и основными библиотеками Umbraco. Рекомендации:

1. Всегда создавайте резервную копию веб-приложения и базы данных перед выполнением обновления. Для веб-приложений Azure можно настроить автоматическое резервное копирование компонентов веб-сайтов с помощью функции архивации. При необходимости веб-сайт можно восстановить с помощью функции восстановления. Дополнительные сведения см. в статьях [Резервное копирование веб-приложения](web-sites-backup) и [Восстановление веб-приложения](web-sites-restore).

2. Проверьте используемые пакеты сторонних поставщиков на совместимость с версией, до которой выполняется обновление. На странице загрузки пакета проверьте совместимость проекта с версией Umbraco CMS.

Дополнительные сведения о локальном обновлении веб-приложения см. [здесь](https://our.umbraco.org/documentation/getting-started/setup/upgrading/general).

После обновления локального сайта опубликуйте изменения в промежуточное веб-приложение. Протестируйте приложение. Если все в порядке, нажмите кнопку **Переместить**, чтобы **перенести** промежуточный сайт в рабочее веб-приложение. При выполнении операции **перемещения** можно просмотреть изменения, которые произойдут в конфигурации веб-приложения. В рамках данной операции **перемещения** происходит переключение веб-приложений и баз данных. Это означает, что после ее завершения рабочее веб-приложение будет указывать на базу данных umbraco-stage-db, а промежуточное — на базу данных umbraco-prod-db.

![Предварительный просмотр перед операцией перемещения при развертывании Umbraco CMS](./media/app-service-web-staged-publishing-realworld-scenarios/22umbswap.png)

Преимущества переключения веб-приложения и базы данных. 1. При возникновении проблем позволяет выполнить откат к предыдущей версии веб-приложения с помощью другой операции **перемещения**. 2. Для обновления необходимо выполнить развертывание файлов и базы данных из промежуточного веб-приложения в рабочие веб-приложение и базу данных. Развертывание файлов и базы данных связано с большим количеством рисков. Используя функцию **перемещения** между слотами, мы сокращаем время простоя при обновлении и снижаем риск сбоев, которые могут произойти при развертывании изменений. 3. Позволяет выполнять **A/B-тестирование** с помощью функции [Тестирование в рабочей среде](http://azure.microsoft.com/documentation/videos/introduction-to-azure-websites-testing-in-production-with-galin-iliev/).

Этот пример демонстрирует гибкость платформы: вы можете создавать пользовательские модули, аналогичные модулю Umbraco Courier, для управления развертыванием в разных средах.

## Ссылки
[Гибкая разработка программного обеспечения с помощью службы приложений Azure.](app-service-agile-software-development)

[Настройка промежуточных сред для веб-приложений в службе приложений Azure](web-sites-staged-publishing)

[Блокирование веб-доступа к непроизводственным областям развертывания](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)

<!---HONumber=Nov15_HO2-->