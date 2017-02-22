---
title: "Эффективное использование сред DevOps для веб-приложений | Документация Майкрософт"
description: "Узнайте, как использовать слоты развертывания для настройки нескольких сред разработки приложения и управления ими"
services: app-service\web
documentationcenter: 
author: sunbuild
manager: yochayk
editor: 
ms.assetid: 16a594dc-61f5-4984-b5ca-9d5abc39fb1e
ms.service: app-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 10/24/2016
ms.author: sumuth
translationtype: Human Translation
ms.sourcegitcommit: 385eb87ec32f5f605b28cc8c76b1c89c7e90bfec
ms.openlocfilehash: 5284022ea473db893800b0f64b5bf4f811d994aa


---
# <a name="use-devops-environments-effectively-for-your-web-apps"></a>Эффективное использование сред разработки и операций для веб-приложений
В этой статье показано, как настроить развертывание разных версий веб-приложений в разных средах, например в среде разработки, промежуточной среде, среде для проверки качества и рабочей среде, а также управлять ими. Каждая версия приложения может рассматриваться как среда разработки для конкретной цели в рамках процесса развертывания. Например, разработчики могут использовать среду для проверки качества для тестирования перед переносом изменений в рабочую среду.
Работа с несколькими средами разработки может вызывать затруднения, так как необходимо отслеживать код, управлять ресурсами (вычислительные ресурсы, веб-приложения, базы данных, кэш и т. д.) и развертывать код в этих средах.

## <a name="set-up-a-non-production-environment-stage-dev-qa"></a>Настройка непроизводственной среды (для подготовки, разработки, контроля качества)
После того как вы создадите и настроите рабочую версию веб-приложения, необходимо создать непроизводственную среду. Для использования слотов развертывания убедитесь, что вы работаете в рамках плана службы приложений Azure "Стандартный" или "Премиум". Слоты развертывания являются динамическими веб-приложениями с собственными именами узлов. Содержимое веб-приложений и элементы конфигурации можно переключать между двумя слотами развертывания (включая рабочий). Развертывание приложения в области развертывания дает следующие преимущества:

- В промежуточном слоте развертывания можно проверить изменения в веб-приложении до того, как они будут отправлены в рабочий слот.
- Развертывание веб-приложения в промежуточном слоте и последующее перемещение в производственный позволяет подготовить все экземпляры слота до того, как они будут перемещены в производственную среду. Это сокращает время простоя при развертывании веб-приложения. Перенаправление трафика не вызывает затруднений, а запросы не теряются из-за операций переключения. Чтобы автоматизировать весь этот рабочий процесс, настройте функцию [автоматического переноса](web-sites-staged-publishing.md#configure-auto-swap), когда проверка перед переносом не требуется.
- После переноса в слоте промежуточного развертывания окажется предыдущее производственное веб-приложение. Если изменения в рабочем слоте не соответствуют вашим ожиданиям, вы можете мгновенно выполнить откат к последнему удачному веб-приложению.

Сведения о настройке промежуточного слота развертывания см. в статье [Настройка промежуточных сред для веб-приложений в службе приложений Azure](web-sites-staged-publishing.md). Каждая среда должна включать собственный набор ресурсов. Например, если веб-приложение использует базу данных, рабочая и промежуточная версии веб-приложения должны использовать разные базы данных. Чтобы настроить промежуточную среду развертывания, добавьте для нее такие ресурсы, как база данных, хранилище и кэш.

## <a name="examples-of-using-multiple-development-environments"></a>Примеры использования нескольких сред разработки
Все проекты разработки необходимо вести в рамках системы управления исходным кодом как минимум с двумя средами (средой разработки и рабочей). Но при использовании систем управления контентом, платформ приложений и т. п. можно столкнуться с ситуацией, когда приложение не поддерживает по умолчанию тот или иной сценарий. Это справедливо для некоторых популярных платформ, рассмотренных в следующих разделах. При работе с CMS и платформами возникает множество вопросов:

- Как разделить содержимое между средами?
- Какие файлы можно менять, не опасаясь, что изменение потребует обновления версии платформы?
- Как управлять конфигурацией каждой среды?
- Как управлять обновлением модулей, подключаемых модулей или базовой платформы?

В проекте есть много способов настройки сред. Ниже приведены примеры, соответствующие рассматриваемым типам приложений.

### <a name="wordpress"></a>WordPress
Из этого раздела вы узнаете, как настроить рабочий процесс развертывания, используя слоты для WordPress. WordPress, как и большинство решений для управления контентом, по умолчанию не поддерживает работу с несколькими средами без настройки. Веб-приложения службы приложений Azure предоставляют несколько функций, облегчающих хранение параметров конфигурации вне кода.

1. Прежде чем создавать промежуточный слот развертывания, настройте поддержку нескольких сред кодом приложения. Для этого добавьте в начало файла `wp-config.php` в веб-приложении в локальной среде разработки следующий код. Это позволит приложению использовать конфигурацию в зависимости от выбранной среды.

    ```
    // Support multiple environments
    // set the config file based on current environment
    if (strpos($_SERVER['HTTP_HOST'],'localhost') !== false) {
    // local development
     $config_file = 'config/wp-config.local.php';
    }
    elseif ((strpos(getenv('WP_ENV'),'stage') !== false) || (strpos(getenv('WP_ENV'),'prod' )!== false ))
    //single file for all azure development environments
     $config_file = 'config/wp-config.azure.php';
    }
    $path = dirname(__FILE__). '/';
    if (file_exists($path. $config_file)) {
    // include the config file if it exists, otherwise WP is going to fail
    require_once $path. $config_file;
    ```

2. Создайте в корне веб-приложения папку `config` и добавьте в нее файлы `wp-config.azure.php` и `wp-config.local.php` (для среды Azure и локальной среды).

3. Скопируйте в файл `wp-config.local.php` следующий код:

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
    define('AUTH_KEY', 'put your unique phrase here');
    define('SECURE_AUTH_KEY','put your unique phrase here');
    define('LOGGED_IN_KEY','put your unique phrase here');
    define('NONCE_KEY', 'put your unique phrase here');
    define('AUTH_SALT', 'put your unique phrase here');
    define('SECURE_AUTH_SALT', 'put your unique phrase here');
    define('LOGGED_IN_SALT', 'put your unique phrase here');
    define('NONCE_SALT', 'put your unique phrase here');

    /**
     * WordPress Database Table prefix.
     *
     * You can have multiple installations in one database if you give each a unique
     * prefix. Only numbers, letters, and underscores please!
     */
    $table_prefix = 'wp_';
    ```

    Настройка ключей безопасности, продемонстрированная в предыдущем коде, помогает предотвратить взлом веб-приложения, поэтому используйте уникальные значения. Если вы хотите создать строку для ключей безопасности (пары "ключ-значение"), указанных в коде, [перейдите к автоматическому генератору](https://api.wordpress.org/secret-key/1.1/salt).

4. Скопируйте в файл `wp-config.azure.php`следующий код:

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
    define('AUTH_KEY',getenv('DB_AUTH_KEY'));
    define('SECURE_AUTH_KEY', getenv('DB_SECURE_AUTH_KEY'));
    define('LOGGED_IN_KEY', getenv('DB_LOGGED_IN_KEY'));
    define('NONCE_KEY', getenv('DB_NONCE_KEY'));
    define('AUTH_SALT', getenv('DB_AUTH_SALT'));
    define('SECURE_AUTH_SALT', getenv('DB_SECURE_AUTH_SALT'));
    define('LOGGED_IN_SALT',  getenv('DB_LOGGED_IN_SALT'));
    define('NONCE_SALT',  getenv('DB_NONCE_SALT'));

    /**
    * WordPress Database Table prefix.
    *
    * You can have multiple installations in one database if you give each a unique
    * prefix. Only numbers, letters, and underscores please!
    */
    $table_prefix = getenv('DB_PREFIX');
```

#### <a name="use-relative-paths"></a>Использование относительных путей
В завершение следует настроить в приложении WordPress относительные пути. WordPress хранит сведения об URL-адресах в базе данных. Использование абсолютных путей усложняет перемещение из одной среды в другую, так как базу данных необходимо обновлять при каждом перемещении из локальной среды в промежуточную и из промежуточной в рабочую. Чтобы снизить вероятность возникновения проблем, вызванных обновлением базы данных при каждом переносе ресурсов из одной среды в другую, используйте [подключаемый модуль для создания путей относительно корневого элемента](https://wordpress.org/plugins/root-relative-urls/), который можно установить с помощью панели мониторинга администратора WordPress.

Добавьте следующие записи в файл `wp-config.php` перед комментарием `That's all, stop editing!`:

```

  define('WP_HOME', 'http://'. filter_input(INPUT_SERVER, 'HTTP_HOST', FILTER_SANITIZE_STRING));
    define('WP_SITEURL', 'http://'. filter_input(INPUT_SERVER, 'HTTP_HOST', FILTER_SANITIZE_STRING));
    define('WP_CONTENT_URL', '/wp-content');
    define('DOMAIN_CURRENT_SITE', filter_input(INPUT_SERVER, 'HTTP_HOST', FILTER_SANITIZE_STRING));
```

Активируйте подключаемый модуль, используя меню `Plugins` на панели мониторинга администратора WordPress. Сохраните постоянную ссылку на приложение WordPress.

#### <a name="the-final-wp-configphp-file"></a>Итоговая версия файла `wp-config.php`
Никакие основные обновления WordPress не будут оказывать влияния на файлы `wp-config.php`, `wp-config.azure.php` и `wp-config.local.php`. Ниже приведен окончательный вариант файла `wp-config.php`:

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
elseif ((strpos(getenv('WP_ENV'),'stage') !== false) ||(strpos(getenv('WP_ENV'),'prod' )!== false )){
  $config_file = 'config/wp-config.azure.php';
}


$path = dirname(__FILE__). '/';
if (file_exists($path. $config_file)) {
  // include the config file if it exists, otherwise WP is going to fail
  require_once $path. $config_file;
}

/** Database Charset to use in creating database tables. */
define('DB_CHARSET', 'utf8');

/** The Database Collate type. Don't change this if in doubt. */
define('DB_COLLATE', '');


/* That's all, stop editing! Happy blogging. */

define('WP_HOME', 'http://'. $_SERVER['HTTP_HOST']);
define('WP_SITEURL', 'http://'. $_SERVER['HTTP_HOST']);
define('WP_CONTENT_URL', '/wp-content');
define('DOMAIN_CURRENT_SITE', $_SERVER['HTTP_HOST']);

/** Absolute path to the WordPress directory. */
if ( !defined('ABSPATH') )
    define('ABSPATH', dirname(__FILE__). '/');

/** Sets up WordPress vars and included files. */
require_once(ABSPATH. 'wp-settings.php');
```

#### <a name="set-up-a-staging-environment"></a>Настройка промежуточной среды
1. Если у вас уже есть веб-приложение WordPress, развернутое в подписке Azure, войдите на [портал Azure](http://portal.azure.com) и перейдите к веб-приложению WordPress. Если у вас нет веб-приложения WordPress, его можно создать в Azure Marketplace. Дополнительные сведения см. в статье [Создание веб-приложения WordPress в службе приложений Azure](web-sites-php-web-site-gallery.md).
Чтобы создать слот развертывания с именем *stage*, выберите **Параметры** > **Слоты развертывания** > **Добавить**. Слот развертывания — это другое веб-приложение, которое использует те же ресурсы, что и основное веб-приложение, созданное ранее.

    ![Создание промежуточного слота развертывания](./media/app-service-web-staged-publishing-realworld-scenarios/1setupstage.png)

2. Добавьте другую базу данных MySQL, например `wordpress-stage-db`, в группу ресурсов `wordpressapp-group`.

    ![Добавление базы данных MySQL в группу ресурсов](./media/app-service-web-staged-publishing-realworld-scenarios/2addmysql.png)

3. Обновите строки подключения для промежуточного слота развертывания, указав в них новую базу данных `wordpress-stage-db`. Рабочее веб-приложение `wordpressprodapp` и промежуточное веб-приложение `wordpressprodapp-stage` должны указывать на разные базы данных.

#### <a name="configure-environment-specific-app-settings"></a>Настройка параметров приложения с учетом конкретной среды
Разработчики могут хранить пары "ключ-значение" в Azure в разделе **Параметры приложения** сведений о конфигурации веб-приложения. В среде выполнения веб-приложения автоматически получают эти значения и делают их доступными коду, выполняемому в веб-приложении. Это дает положительный побочный эффект с точки зрения безопасности, так как конфиденциальная информация, например строки подключения к базам данных и пароли, никогда не отображается в виде открытого текста в таких файлах, как `wp-config.php`.

Процесс, описанный в следующих разделах, объединяет изменения как файлов, так и базы данных для приложения WordPress:

* обновление версии WordPress;
* добавление, изменение или обновление подключаемых модулей;
* добавление, изменение или обновление тем.

Настройка параметров приложения:

* сведения о базе данных;
* включение и выключение ведения журнала для WordPress.
* параметры безопасности WordPress.

![Параметры приложения для веб-приложения WordPress](./media/app-service-web-staged-publishing-realworld-scenarios/3configure.png)

Убедитесь, что вы добавили следующие параметры приложения для рабочего веб-приложения и промежуточного слота. Обратите внимание, что рабочая и промежуточная версии веб-приложения должны использовать разные базы данных.

1. Снимите флажок **Параметр слота** для всех параметров, кроме WP_ENV. Этот процесс отвечает за смену конфигурации вашего веб-приложения, а также содержимого файлов и базы данных. Если флажок **Параметр слота** установлен, параметры веб-приложения и строки подключения *не* будут перемещаться из одной среды в другую во время **переключения**, поэтому любые изменения базы данных не приведут к сбою рабочего веб-приложения.

2. Разверните веб-приложение в локальной среде разработки, чтобы затем переместить его и базу данных в промежуточную среду с помощью WebMatrix или других средств, таких как FTP, Git или PhpMyAdmin.

    ![Диалоговое окно публикации веб-приложения WordPress в WebMatrix](./media/app-service-web-staged-publishing-realworld-scenarios/4wmpublish.png)

3. Перейдите к веб-приложению в промежуточной среде и протестируйте его. Рассмотрим сценарий, в котором нужно обновить тему веб-приложения. Промежуточная версия выглядит так:

    ![Просмотр промежуточных веб-приложений до переключения слотов](./media/app-service-web-staged-publishing-realworld-scenarios/5wpstage.png)

4. Если ничего менять не требуется, щелкните **Переключить** в параметрах промежуточного веб-приложения, чтобы переместить его содержимое в рабочую среду. В нашем примере каждая операция **переключения** приводит к переключению веб-приложения и базы данных между средами.

    ![Просмотр изменений при переключении для WordPress](./media/app-service-web-staged-publishing-realworld-scenarios/6swaps1.png)

    > [!NOTE]
    > Если в вашем сценарии требуется только передача файлов (без обновления базы данных), то перед выполнением операции **переключения** установите флажок **Параметр слота** для всех *параметров приложения* и *параметров строки подключения*, связанных с базой данных, в колонке **параметров веб-приложения** на портале Azure. В этом случае параметры DB_NAME, DB_HOST, DB_PASSWORD, DB_USER и строка подключения по умолчанию не должны отображаться при предварительном просмотре изменений перед **переключением**. После **переключения** в веб-приложении WordPress будут только обновленные файлы.
    >
    >

    Перед выполнением операции **переключения** рабочее приложение WordPress выглядит так:
    ![Рабочее веб-приложение перед переключением слотов](./media/app-service-web-staged-publishing-realworld-scenarios/7bfswap.png)

    После операции **переключения** тема в рабочем веб-приложении будет обновлена.

    ![Рабочее веб-приложение после переключения слотов](./media/app-service-web-staged-publishing-realworld-scenarios/8afswap.png)

5. Если вам необходимо выполнить **откат**, перейдите к параметрам рабочего веб-приложения и нажмите кнопку **Переключить**, чтобы переключить веб-приложение и базу данных из рабочего слота на промежуточный. Имейте в виду, что если в операцию **переключения** включены изменения базы данных, то при следующем повторном развертывании промежуточного веб-приложения потребуется развернуть изменения базы данных в текущей базе данных для промежуточного веб-приложения. Это может быть как предыдущая рабочая, так и промежуточная база данных.

#### <a name="summary"></a>Сводка
Ниже приведен общий процесс для любого приложения с базой данных:

1. Установка приложения в локальной среде.
2. Выбор конфигурации в зависимости от конкретной среды (локальное приложение и веб-приложение Azure).
3. Настройка промежуточной и рабочей сред для веб-приложений.
4. Синхронизация содержимого рабочей среды (файлы, код и база данных) с локальной и промежуточной средами (если ваше рабочее приложение уже выполняется в Azure).
5. Разработка приложения в локальной среде.
6. Перевод рабочего веб-приложения в режим обслуживания или блокировки для синхронизации содержимого базы данных в рабочей среде с промежуточной средой и средой разработки.
7. Развертывание в промежуточной среде и тестирование.
8. Развертывание в рабочей среде.
9. Повтор шагов 4–6.

### <a name="umbraco"></a>Umbraco
Из этого раздела вы узнаете, как использовать пользовательские модули в системе CMS Umbraco для развертывания в нескольких средах разработки и операций. В этом примере представлен другой подход к управлению развертыванием в нескольких средах разработки.

[Umbraco CMS](http://umbraco.com/) — это популярное среди разработчиков CMS-решение на основе .NET, предоставляющее модуль [Courier2](http://umbraco.com/products/more-add-ons/courier-2) для переноса содержимого из среды разработки в промежуточную, а затем и в рабочую среду. Вы можете легко создать локальную среду разработки для веб-приложения Umbraco CMS с помощью Visual Studio или WebMatrix.

- [Создание веб-приложения Umbraco с помощью Visual Studio](https://our.umbraco.org/documentation/Installation/install-umbraco-with-nuget)
- [Создание веб-приложения Umbraco с помощью WebMatrix](http://umbraco.tv/videos/umbraco-v7/implementor/fundamentals/installation/creating-umbraco-site-from-webmatrix-web-gallery/)

Не забывайте удалять папку `install` приложения и никогда не переносите ее в промежуточную или рабочую среду. В этом руководстве используется WebMatrix.

#### <a name="set-up-a-staging-environment"></a>Настройка промежуточной среды
1. Предположим, у вас уже есть готовое веб-приложение Umbraco CMS. Создайте слот развертывания для веб-приложения Umbraco CMS в соответствии с приведенными выше инструкциями. Если приложения нет, вы можете создать его в магазине.
2. Обновите строки подключения для промежуточного слота развертывания, указав в них новую базу данных **umbraco-stage-db**. Ваше рабочее веб-приложение (umbraositecms-1) и промежуточное веб-приложение (umbracositecms-1-stage) *должны* указывать на разные базы данных.

    ![Обновление строки подключения для промежуточного веб-приложения с новой промежуточной базой данных](./media/app-service-web-staged-publishing-realworld-scenarios/9umbconnstr.png)

3. Щелкните **Get Publish settings** (Получить параметры публикации) для **промежуточного** слота развертывания. Будет загружен файл параметров публикации, в котором хранятся сведения, необходимые Visual Studio или WebMatrix для публикации приложения из локальной среды разработки в веб-приложение Azure.

    ![Получение параметров публикации для промежуточного веб-приложения](./media/app-service-web-staged-publishing-realworld-scenarios/10getpsetting.png)
4. Откройте веб-приложение в локальной среде разработки WebMatrix или Visual Studio. В этом руководстве используется WebMatrix. Сначала нам необходимо импортировать файл параметров публикации для промежуточного веб-приложения.

    ![Импорт параметров публикации в Umbraco с помощью Web Matrix](./media/app-service-web-staged-publishing-realworld-scenarios/11import.png)

5. Просмотрите изменения в диалоговом окне и разверните локальное веб-приложение в веб-приложение Azure *umbracositecms-1-stage*. При развертывании файлов непосредственно в промежуточное веб-приложение исключаются файлы в папке `~/app_data/TEMP/`, так как они будут повторно созданы при первом запуске промежуточного веб-приложения. Следует также пропустить файл `~/app_data/umbraco.config`, так как он тоже будет создан заново.

    ![Просмотр изменений публикации в WebMatrix](./media/app-service-web-staged-publishing-realworld-scenarios/12umbpublish.png)

6. После успешной публикации локального веб-приложения Umbraco в промежуточное веб-приложение перейдите к промежуточному веб-приложению и выполните несколько тестов, чтобы исключить возможные проблемы.

#### <a name="set-up-the-courier2-deployment-module"></a>Настройка модуля развертывания Courier2
Используя модуль [Courier2](http://umbraco.com/products/more-add-ons/courier-2), вы можете щелчком правой кнопки мыши передавать содержимое, таблицы стилей и модули разработки из промежуточного веб-приложения в рабочее. Это снижает риск возникновения сбоев рабочего веб-приложения при развертывании обновлений.
Приобретите лицензию на Courier2 для домена `*.azurewebsites.net` и пользовательского домена (например, http://abc.com). После этого поместите скачанный файл лицензии (LIC-файл) в папку `bin`.

![Перенос файла лицензии в папку bin](./media/app-service-web-staged-publishing-realworld-scenarios/13droplic.png)

1. [Скачайте пакет Courier2](https://our.umbraco.org/projects/umbraco-pro/umbraco-courier-2/). Войдите в промежуточное веб-приложение, например http://umbracocms-site-stage.azurewebsites.net/umbraco, и в меню **Разработчик** щелкните **Пакеты** > **Install local package** (Установить локальный пакет).

    ![Установщик пакета Umbraco](./media/app-service-web-staged-publishing-realworld-scenarios/14umbpkg.png)

2. Передайте пакет Сourier2 с помощью установщика.

    ![Загрузка пакета модуля Courier](./media/app-service-web-staged-publishing-realworld-scenarios/15umbloadpkg.png)

3. Для настройки пакета необходимо обновить файл courier.config в папке **Config** вашего веб-приложения.

    ```xml
    <!-- Repository connection settings -->
     <!-- For each site, a custom repository must be configured, so Courier knows how to connect and authenticate-->
     <repositories>
        <!-- If a custom Umbraco Membership provider is used, specify login & password + set the passwordEncoding to clear: -->
        <repository name="production web app" alias="stage" type="CourierWebserviceRepositoryProvider" visible="true">
          <url>http://umbracositecms-1.azurewebsites.net</url>
          <user>0</user>
          <!--<login>user@email.com</login> -->
          <!-- <password>user_password</password>-->
          <!-- <passwordEncoding>Clear</passwordEncoding>-->
          </repository>
     </repositories>
     ```

4. В `<repositories>` введите URL-адрес рабочего сайта и сведения о пользователе.
    Если используется поставщик членства Umbraco по умолчанию, следует добавить соответствующий идентификатор для пользователя-администратора в разделе &lt;user&gt;.
    Если используется собственный поставщик членства Umbraco, укажите `<login>`, `<password>`, с которыми модуль Courier2 должен подключаться к рабочему сайту.
    Дополнительные сведения см. в [документации по модулю Courier2](http://umbraco.com/help-and-support/customer-area/courier-2-support-and-download/developer-documentation).

5. Аналогичным образом установите модуль Courier2 на рабочий сайт и настройте его на размещенное веб-приложение в соответствующем файле courier.config, как показано здесь.

    ```xml
     <!-- Repository connection settings -->
     <!-- For each site, a custom repository must be configured, so Courier knows how to connect and authenticate-->
     <repositories>
        <!-- If a custom Umbraco Membership provider is used, specify login & password + set the passwordEncoding to clear: -->
        <repository name="Stage web app" alias="stage" type="CourierWebserviceRepositoryProvider" visible="true">
          <url>http://umbracositecms-1-stage.azurewebsites.net</url>
          <user>0</user>
          </repository>
     </repositories>
    ```

6. Откройте вкладку **Courier2** в панели мониторинга веб-приложений Umbraco CMS и щелкните **Расположения**. Вы увидите имя репозитория, используемое в `courier.config`. Сделайте это в веб-приложениях в рабочей и промежуточной среде.

    ![Просмотр целевого репозитория веб-приложения](./media/app-service-web-staged-publishing-realworld-scenarios/16courierloc.png)

7. Для развертывания содержимого промежуточного сайта в рабочей среде перейдите в раздел **Содержимое** и выберите существующую страницу или создайте ее. Выберем существующую страницу в разделе "Мое веб-приложение" с заголовком **Приступая к работе — новый**, после чего нажмем кнопку **Сохранить и опубликовать**.

    ![Изменение заголовка страницы и публикация](./media/app-service-web-staged-publishing-realworld-scenarios/17changepg.png)

8. Щелкните измененную страницу правой кнопкой мыши, чтобы просмотреть все параметры. Щелкните **Courier**, чтобы открыть диалоговое окно **Развертывание**. Щелкните **Развернуть**, чтобы инициировать развертывание.

    ![Диалоговое окно развертывания модуля Courier](./media/app-service-web-staged-publishing-realworld-scenarios/18dialog1.png)

9. Просмотрите изменения и нажмите кнопку **Продолжить**.

    ![Просмотр изменений в диалоговом окне развертывания модуля Courier](./media/app-service-web-staged-publishing-realworld-scenarios/19dialog2.png)

    Информация о результатах развертывания отображается в журнале развертывания.

     ![Просмотр журналов развертывания из модуля Courier](./media/app-service-web-staged-publishing-realworld-scenarios/20successdlg.png)

10. Перейдите к рабочему веб-приложению, чтобы проверить, отражены ли изменения.

     ![Переход к рабочему веб-приложению](./media/app-service-web-staged-publishing-realworld-scenarios/21umbpg.png)

Дополнительные сведения об использовании Courier см. в документации.

#### <a name="how-to-upgrade-the-umbraco-cms-version"></a>Обновление Umbraco CMS
Пакеты Courier невозможно использовать при обновлении с одной версии CMS Umbraco до другой. При обновлении версии Umbraco CMS необходимо проверить совместимость с пользовательскими модулями, партнерскими модулями и основными библиотеками Umbraco. Ниже приведены рекомендации.

* Всегда создавайте резервную копию веб-приложения и базы данных перед обновлением. Для веб-приложений Azure можно настроить автоматическую архивацию компонентов веб-сайтов с помощью функции архивации. При необходимости веб-сайт можно восстановить с помощью функции восстановления. Дополнительные сведения см. в статьях [Архивация приложения в Azure](web-sites-backup.md) и [Восстановление приложения в Azure](web-sites-restore.md).
* Проверьте партнерские пакеты на совместимость с версией, до которой выполняется обновление. На странице скачивания пакета проверьте совместимость проекта с версией Umbraco CMS.

Дополнительные сведения о локальном обновлении веб-приложения см. в [общих рекомендациях по обновлению](https://our.umbraco.org/documentation/getting-started/setup/upgrading/general).

После обновления локального сайта опубликуйте изменения в промежуточное веб-приложение. Протестируйте приложение. Если ничего менять не требуется, нажмите кнопку **Переключить**, чтобы переключить промежуточный сайт в рабочее веб-приложение. При выполнении операции **переключения** можно просмотреть изменения, которые произойдут в конфигурации веб-приложения. В рамках **переключения** происходит переключение веб-приложений и баз данных. После завершения **переключения** рабочее веб-приложение будет указывать на базу данных umbraco-stage-db, а промежуточное — на базу данных umbraco-prod-db.

![Предварительный просмотр перед операцией перемещения при развертывании Umbraco CMS](./media/app-service-web-staged-publishing-realworld-scenarios/22umbswap.png)

Ниже представлены преимущества переключения веб-приложения и базы данных:

* При возникновении проблем с приложением это позволяет выполнить откат к предыдущей версии веб-приложения с помощью другой операции **переключения**.
* Для обновления необходимо выполнить развертывание файлов и базы данных из промежуточного веб-приложения в рабочие веб-приложение и базу данных. Развертывание файлов и базы данных связано с большим количеством рисков. Используя функцию **переключения** между слотами, мы сокращаем время простоя при обновлении и снижаем риск сбоев, которые могут произойти при развертывании изменений.
* Вы можете выполнить **тестирование A/B** с помощью функции [тестирование в рабочей среде](https://azure.microsoft.com/documentation/videos/introduction-to-azure-websites-testing-in-production-with-galin-iliev/).

Этот пример демонстрирует гибкость платформы: вы можете создавать пользовательские модули, аналогичные модулю Umbraco Courier, для управления развертыванием в разных средах.

## <a name="references"></a>Ссылки
[Гибкая разработка программного обеспечения с помощью службы приложений Azure.](app-service-agile-software-development.md)

[Настройка промежуточных сред для веб-приложений в службе приложений Azure](web-sites-staged-publishing.md)

[Блокирование веб-доступа к непроизводственным областям развертывания](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)



<!--HONumber=Dec16_HO3-->


