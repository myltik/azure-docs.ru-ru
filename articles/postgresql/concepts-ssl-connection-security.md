---
title: Настройка SSL-соединения в базе данных Azure для PostgreSQL
description: Инструкции и сведения о настройке базы данных Azure для PostgreSQL и связанных приложений для правильного использования SSL-соединений.
services: postgresql
author: JasonMAnderson
ms.author: janders
editor: jasonwhowell
manager: kfile
ms.service: postgresql
ms.custom: ''
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: f3efb99ddb47f167a0d9cbef064890e817a18841
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32186823"
---
# <a name="configure-ssl-connectivity-in-azure-database-for-postgresql"></a>Настройка SSL-соединения в базе данных Azure для PostgreSQL
База данных Azure для PostgreSQL предпочитает подключать клиентские приложения к службе PostgreSQL с помощью SSL (Secure Sockets Layer). Применение SSL-соединений между сервером базы данных и клиентскими приложениями обеспечивает защиту от атак "злоумышленник в середине" за счет шифрования потока данных между сервером и приложением.

По умолчанию в службе базы данных PostgreSQL настроено обязательное использование SSL-соединения. При необходимости можно отключить обязательное использование SSL при подключении к службе базы данных, если клиентское приложение не поддерживает SSL-соединения. 

## <a name="enforcing-ssl-connections"></a>Применение SSL-соединений
Для всех серверов базы данных Azure для PostgreSQL, подготовленных с помощью портала Azure и интерфейса командной строки, применение SSL-соединений включается по умолчанию. 

Аналогичным образом предопределенные строки подключения в разделе "Строки подключения" в настройках сервера на портале Azure содержат необходимые параметры для распространенных языков, что позволяет подключиться к серверу базы данных с помощью SSL. Значение параметра SSL зависит от соединителя. Например, может использоваться "ssl=true", "sslmode=require", "sslmode=required" или другой вариант.

## <a name="configure-enforcement-of-ssl"></a>Настройка применения SSL
При необходимости применение SSL-соединений можно отключить. Для повышения безопасности Microsoft Azure рекомендует всегда включать параметр **Enforce SSL connection** (Применять SSL-соединение).

### <a name="using-the-azure-portal"></a>Использование портала Azure
Войдите в сервер базы данных Azure для PostgreSQL и щелкните **Безопасность подключения**. Воспользуйтесь переключателем для включения или отключения параметра **Enforce SSL connection** (Применять SSL-соединение). Нажмите кнопку **Сохранить**. 

![Безопасность подключения: отключить применение SSL](./media/concepts-ssl-connection-security/1-disable-ssl.png)

Чтобы проверить правильность настройки, на странице **Обзор** просмотрите индикатор **SSL enforce status** (Состояние применения SSL).

### <a name="using-azure-cli"></a>Использование Azure CLI
Вы также можете включить или отключить параметр **ssl-enforcement** с помощью Azure CLI, используя значения `Enabled` и `Disabled` соответственно.

```azurecli
az postgres server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Enabled
```

## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>Проверка поддержки SSL-соединений в приложении или платформе
Многие распространенные платформы приложений, использующие PostgreSQL для служб базы данных, такие как Drupal и Django, по умолчанию не включают SSL при установке. Включить применение SSL-соединений необходимо после установки с помощью команд интерфейса командной строки, соответствующих конкретному приложению. Если на сервере PostgreSQL применяются SSL-соединения, а связанное приложение не настроено должным образом, то при подключении приложения к серверу базы данных может произойти сбой. Сведения о включении SSL-соединений можно найти в документации приложения.


## <a name="applications-that-require-certificate-verification-for-ssl-connectivity"></a>Приложения, требующие проверки сертификата для SSL-соединений
В некоторых случаях для безопасного подключения приложениям требуется локальный файл сертификата, созданный из файла сертификата (CER-файла) доверенного центра сертификации. Ниже описывается, как получить этот CER-файл, декодировать сертификат и привязать его к приложению.

### <a name="download-the-certificate-file-from-the-certificate-authority-ca"></a>Скачивание файла сертификата из центра сертификации 
Сертификат, необходимый для подключения к серверу базы данных Azure для PostgreSQL по протоколу SSL, находится [здесь](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt). Скачайте файл сертификата и сохраните его локально.

### <a name="download-and-install-openssl-on-your-machine"></a>Скачивание и установка OpenSSL на компьютер 
Чтобы декодировать файл сертификата, необходимый для безопасного подключения приложения к серверу базы данных, установите на своем локальном компьютере OpenSSL.

#### <a name="for-linux-os-x-or-unix"></a>Для Linux, OS X или Unix
Библиотеки OpenSSL предоставляются в виде исходного кода непосредственно от [OpenSSL Software Foundation](http://www.openssl.org). Приведенные ниже инструкции помогут вам установить OpenSSL на компьютер под управлением Linux. В этой статье используются команды, которые гарантированно поддерживают работу с Ubuntu 12.04 и более поздних версий.

Откройте сеанс терминала и скачайте OpenSSL.
```bash
wget http://www.openssl.org/source/openssl-1.1.0e.tar.gz
``` 
Извлеките файлы из скачанного пакета.
```bash
tar -xvzf openssl-1.1.0e.tar.gz
```
Укажите каталог, в который были извлечены файлы. По умолчанию это должен быть каталог, показанный ниже.

```bash
cd openssl-1.1.0e
```
Настройте OpenSSL, выполнив приведенную ниже команду. Если вы хотите, чтобы файлы хранились в папке, отличной от /usr/local/openssl, то укажите соответствующее значение.

```bash
./config --prefix=/usr/local/openssl --openssldir=/usr/local/openssl
```
Теперь, когда библиотека OpenSSL настроена должным образом, необходимо скомпилировать ее для преобразования сертификата. Для выполнения компиляции выполните следующую команду.

```bash
make
```
После завершения компиляции можно установить OpenSSL как исполняемый файл, выполнив следующую команду.
```bash
make install
```
Чтобы убедиться, что компонент OpenSSL успешно установлен в системе, выполните приведенную ниже команду. Убедитесь, чтобы отображаются такие же выходные данные.

```bash
/usr/local/openssl/bin/openssl version
```
В случае успешного выполнения отобразится следующее сообщение:
```bash
OpenSSL 1.1.0e 7 Apr 2014
```

#### <a name="for-windows"></a>Для Windows
Установить OpenSSL на компьютере Windows можно следующими способами:
1. **(Рекомендуется.)** При использовании встроенных функций Bash для Windows в Windows 10 и более поздних версиях OpenSSL устанавливается по умолчанию. Инструкции по включению функций Bash для Windows в Windows 10 можно найти [здесь](https://msdn.microsoft.com/commandline/wsl/install_guide).
2. Можно скачать приложение для Win32 или Win64, предоставляемое сообществом. Хотя OpenSSL Software Foundation не предоставляет и не одобряет какие-либо определенные установщики Windows, [здесь](https://wiki.openssl.org/index.php/Binaries) приводится список доступных установщиков.

### <a name="decode-your-certificate-file"></a>Декодируйте файл сертификата
Файл из корневого центра сертификации скачивается в зашифрованном виде. Чтобы декодировать файл сертификата, воспользуйтесь OpenSSL. Для этого выполните следующую команду OpenSSL:

```dos
openssl x509 -inform DER -in BaltimoreCyberTrustRoot.crt -text -out root.crt
```

### <a name="connecting-to-azure-database-for-postgresql-with-ssl-certificate-authentication"></a>Подключение к базе данных Azure для PostgreSQL с использованием проверки подлинности на основе SSL-сертификата
Теперь, когда вы успешно декодировали сертификат, можно безопасно подключиться к серверу базы данных по протоколу SSL. Чтобы выполнить проверку сертификата сервера, необходимо поместить сертификат в файл ~/.postgresql/root.crt в основном каталоге пользователя. (В Microsoft Windows этот файл имеет имя %APPDATA%\postgresql\root.crt). Ниже приводятся инструкции по подключению к базе данных Azure для PostgreSQL.

#### <a name="using-psql-command-line-utility"></a>Использование служебной программы командной строки psql
В следующем примере показано, как подключиться к серверу PostgreSQL с помощью служебной программы командной строки psql, используя созданный файл `root.crt` и параметр `sslmode=verify-ca` или `sslmode=verify-full`.

С помощью интерфейса командной строки PostgreSQL выполните следующую команду:
```bash
psql "sslmode=verify-ca sslrootcert=root.crt host=mydemoserver.postgres.database.azure.com dbname=postgres user=mylogin@mydemoserver"
```
В случае успешного выполнения отобразится следующий результат:
```bash
Password for user mylogin@mydemoserver:
psql (9.6.2)
WARNING: Console code page (437) differs from Windows code page (1252)
     8-bit characters might not work correctly. See psql reference
     page "Notes for Windows users" for details.
SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-SHA384, bits: 256, compression: off)
Type "help" for help.

postgres=>
```

#### <a name="using-pgadmin-gui-tool"></a>Использование инструмента графического пользовательского интерфейса pgAdmin
Чтобы pgAdmin 4 мог безопасно подключаться по протоколу SSL, необходимо настроить параметр `SSL mode = Verify-CA` или `SSL mode = Verify-Full`, как показано ниже.

![Снимок экрана pgAdmin: настройка параметра "SSL mode" (Режим SSL) на вкладке "Connection" (Подключение)](./media/concepts-ssl-connection-security/2-pgadmin-ssl.png)

## <a name="next-steps"></a>Дополнительная информация
Сведения о вариантах подключения приложений см. в статье [Библиотеки подключений для базы данных Azure для PostgreSQL](concepts-connection-libraries.md).
