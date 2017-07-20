---
title: "Настройка SSL-соединений для безопасного подключения к базе данных Azure для MySQL | Документация Майкрософт"
description: "Инструкции по настройке базы данных Azure для MySQL и связанных приложений для правильного использования SSL-соединений."
services: mysql
author: JasonMAnderson
ms.author: janders
editor: jasonwhowell
manager: jhubbard
ms.service: mysql-database
ms.topic: article
ms.date: 06/01/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 3716c7699732ad31970778fdfa116f8aee3da70b
ms.openlocfilehash: 1303e6cf6f6fdd10a7310d770127828276d5056e
ms.contentlocale: ru-ru
ms.lasthandoff: 06/30/2017

---
# <a name="configure-ssl-connectivity-in-your-application-to-securely-connect-to-azure-database-for-mysql"></a>Настройка SSL-подключений в приложении для безопасного подключения к базе данных Azure для MySQL
База данных Azure для MySQL поддерживает подключение сервера базы данных Azure для MySQL к клиентским приложениям с помощью протокола SSL (Secure Sockets Layer). Применение SSL-соединений между сервером базы данных и клиентскими приложениями обеспечивает защиту от атак "злоумышленник в середине" за счет шифрования потока данных между сервером и приложением.

По умолчанию в службе базы данных должно быть настроено обязательное использование SSL-соединений при подключении к серверу базы данных Azure для MySQL.  Рекомендуется не отключать параметр SSL без необходимости.

## <a name="enforcing-ssl-connections"></a>Применение SSL-соединений
При подготовке нового сервера базы данных Azure для MySQL с помощью портала Azure или интерфейса командной строки применение SSL-соединений включается по умолчанию.

Аналогичным образом предопределенные строки подключения в разделе "Строки подключения" в настройках сервера на портале Azure содержат необходимые параметры для распространенных языков, что позволяет требовать применения SSL. Значение параметра SSL зависит от соединителя. Например, может использоваться "ssl=true", "sslmode=require", "sslmode=required" или другой вариант.

## <a name="configure-enforcement-of-ssl"></a>Настройка применения SSL
Можно включить или отключить применение протокола SSL. Для повышения безопасности Microsoft Azure рекомендует всегда включать параметр "Enforce SSL connection" (Применять SSL-соединение).

### <a name="using-azure-portal"></a>Использование портала Azure
С помощью портала Azure перейдите к серверу базы данных Azure для MySQL и щелкните **Безопасность подключения**. Воспользуйтесь переключателем для включения или отключения параметра **Enforce SSL connection** (Применять SSL-соединение). Нажмите кнопку **Сохранить**. Для повышения безопасности корпорация Майкрософт рекомендует всегда включать параметр **Enforce SSL connection** (Применять SSL-соединение).
![enable-ssl](./media/howto-configure-ssl/enable-ssl.png)

Чтобы проверить правильность настройки, на странице **Обзор** просмотрите индикатор **SSL enforce status** (Состояние применения SSL).

### <a name="using-azure-cli"></a>Использование Azure CLI
Вы также можете включить или отключить параметр **ssl-enforcement** с помощью Azure CLI, используя значения Enabled и Disabled соответственно.
```azurecli-interactive
az mysql server update --resource-group myresource --name mysqlserver4demo --ssl-enforcement Enabled
```
## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>Проверка поддержки SSL-соединений в приложении или платформе
Многие распространенные приложения, использующие MySQL для служб базы данных, такие как Wordpress, Drupal и Magento, по умолчанию не включают SSL при установке. Включите в этих приложениях применение SSL-соединений после установки или с помощью команд интерфейса командной строки, соответствующих конкретному приложению. Если на сервере MySQL принудительно применяются SSL-соединения, когда связанное приложение не настроено должным образом, то при его подключении к серверу базы данных может произойти сбой. Сведения о включении SSL-соединений можно найти в документации приложения.

## <a name="applications-that-require-a-local-certificate-for-ssl-connectivity"></a>Приложения, которым требуется локальный сертификат для SSL-соединений
В некоторых случаях для безопасного подключения приложениям требуется локальный PEM-файл сертификата, созданный из CER-файла сертификата центра сертификации.  Ниже описывается, как получить этот CER-файл, создать локальный PEM-файл и привязать его к приложению.

### <a name="download-the-certificate-file-from-the-certificate-authority-ca"></a>Скачивание файла сертификата из центра сертификации
Сертификат, необходимый для подключения к серверу базы данных Azure для MySQL по протоколу SSL, находится [здесь](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt).  Скачайте файл сертификата на локальный диск (в этом руководстве мы используем папку **c:\ssl**).

### <a name="download-and-install-openssl-on-your-pc"></a>Скачивание и установка OpenSSL на компьютер
Чтобы создать локальный **PEM**-файл, необходимый для безопасного подключения приложения к серверу базы данных, необходимо установить OpenSSL на своем компьютере.  

В следующих разделах описаны подходы, которые можно использовать. В зависимости от выбранной операционной системы можно использовать компьютер под управлением Linux или Windows. Вам нужно будет применить только один из методов.

### <a name="linux-users---download-and-install-openssl-using-a-linux-pc"></a>Пользователи Linux. Скачивание и установка OpenSSL с помощью компьютера Linux
Библиотеки OpenSSL предоставляются в виде исходного кода непосредственно от [OpenSSL Software Foundation](http://www.openssl.org).  Приведенные ниже инструкции помогут вам установить OpenSSL на компьютер под управлением Linux.  В этом руководстве показаны команды для Ubuntu 12.04 и более поздней версии.

Откройте сеанс терминала и установите OpenSSL.
```bash
wget http://www.openssl.org/source/openssl-1.1.0e.tar.gz
```  
Извлеките файлы из скачанного пакета.
```bash
tar -xvzf openssl-1.1.0e.tar.gz
```
Укажите каталог, в который были извлечены файлы.  По умолчанию это должен быть каталог, показанный ниже.

```bash
cd openssl-1.1.0e
```
Настройте OpenSSL, выполнив приведенную ниже команду. Если вы хотите, чтобы файлы хранились в папке, отличной от /usr/local/openssl, то укажите соответствующее значение в команде.

```bash
./config --prefix=/usr/local/openssl --openssldir=/usr/local/openssl
```
Теперь, когда библиотека OpenSSL настроена должным образом, необходимо скомпилировать ее для преобразования сертификата.  Для выполнения компиляции выполните следующую команду.

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
В случае успешного выполнения отобразится следующее сообщение.
```bash
OpenSSL 1.1.0e 7 Apr 2014
```

### <a name="windows-pc-users---download-and-install-openssl-using-a-windows-pc"></a>Пользователи компьютеров Windows. Скачивание и установка OpenSSL с помощью компьютера Windows
Чтобы создать локальный **PEM**-файл, необходимый для безопасного подключения приложения к серверу базы данных, необходимо установить OpenSSL на своем компьютере.

Библиотеки OpenSSL предоставляются в виде исходного кода непосредственно от [OpenSSL Software Foundation](http://www.openssl.org). Приведенные ниже инструкции помогут вам установить OpenSSL на компьютер под управлением Linux.

Установить OpenSSL на компьютере Windows можно следующими способами:

1. **(Рекомендуется.)** При использовании встроенных функций Bash для Windows в Windows 10 и более поздних версиях OpenSSL устанавливается по умолчанию.  Инструкции по включению функций Bash для Windows в Windows 10 можно найти [здесь](https://msdn.microsoft.com/commandline/wsl/install_guide).

2. Можно скачать приложение для Win32 или Win64, предоставляемое сообществом. Хотя OpenSSL Software Foundation не предоставляет и не одобряет какие-либо определенные установщики Windows, [здесь](https://wiki.openssl.org/index.php/Binaries) приводится список доступных установщиков.

### <a name="convert-your-cer-certificate-to-a-local-pem"></a>Преобразование CER-файла сертификата в локальный PEM-файл
Скачанный файл из корневого центра сертификации имеет формат **CRT**. Используйте OpenSSL для преобразования этого файла сертификата в **PEM**-файл.  Для этого нужно запустить программу командной строки openssl.exe и выполнить следующую команду.

```dos
OpenSSL>x509 -inform DER -in BaltimoreCyberTrustRoot.crt -out MyServerCACert.pem
```
Теперь, когда вы успешно создали файл сертификата (MyServerCACert.pem), можно безопасно подключиться к серверу базы данных по протоколу SSL.

В следующих примерах показано, как подключиться к серверу MySQL с помощью интерфейса командной строки MySQL и MySQL Workbench, используя файл **MyServerCACert.pem**.

### <a name="connecting-to-server-using-the-mysql-cli-over-ssl"></a>Подключение к серверу с помощью интерфейса командной строки MySQL по протоколу SSL
С помощью интерфейса командной строки MySQL выполните следующую команду.

```dos
mysql.exe -h mysqlserver4demo.mysql.database.azure.com -uUsername@mysqlserver4demo -pYourPassword --ssl-ca=c:\ssl\MyServerCACert.pem
```
Выполните команду mysql **status**, чтобы проверить наличие подключения к серверу MySQL с помощью протокола SSL.

```dos
mysql> status
--------------
mysql.exe  Ver 14.14 Distrib 5.7.18, for Win64 (x86_64)

Connection id:          65535
Current database:
Current user:           jason@66.235.55.141
SSL:                    Cipher in use is AES256-SHA
Using delimiter:        ;
Server version:         5.6.26.0 MySQL Community Server (GPL)
Protocol version:       10
Connection:             janderserver.mysql.sqltest-eg1.mscds.com via TCP/IP
Server characterset:    latin1
Db     characterset:    latin1
Client characterset:    cp850
Conn.  characterset:    cp850
TCP port:               3306
Uptime:                 1 day 19 hours 9 min 43 sec

Threads: 4  Questions: 26082  Slow queries: 0  Opens: 112  Flush tables: 1  Open tables: 105  Queries per second avg: 0.167
--------------
```

> [!NOTE]
> Сейчас при подключении mysql.exe к службе с использованием параметра --ssl-mode=VERIFY_IDENTITY может возникать известная проблема, из-за которой происходит сбой подключения со следующей ошибкой: _ERROR 2026 (HY000): SSL connection error: SSL certificate validation failure_ Downgrade to "--ssl-mode=VERIFY_CA" or lesser [SSL modes](https://dev.mysql.com/doc/refman/5.7/en/secure-connection-options.html#option_general_ssl-mode) (ОШИБКА 2026 (HY000): ошибка SSL-соединения: ошибка проверки сертификата SSL. Перейдите к параметру --ssl-mode=VERIFY_CA или к второстепенным режимам SSL). Если вам необходимо использовать параметр --ssl-mode=VERIFY_IDENTITY, то используйте имя регионального сервера, пока эта проблема не будет устранена. Проверьте связь с сервером для определения имени регионального сервера, например `westeurope1-a.control.database.windows.net`, а затем используйте это имя при подключении. Мы планируем устранить это ограничение в будущем.

### <a name="connecting-to-server-using-the-mysql-workbench-over-ssl"></a>Подключение к серверу с помощью MySQL Workbench по протоколу SSL
Настройте MySQL Workbench, чтобы безопасно подключаться по протоколу SSL. В MySQL Workbench в диалоговом окне "Setup New Connection" (Настройка нового подключения) перейдите на вкладку **SSL**. Введите расположение файла **MyServerCACert.pem** в поле **SSL CA File:** (Файл SSL-сертификата центра сертификации).
![Сохранение настроенного элемента](./media/howto-configure-ssl/mysql-workbench-ssl.png)

## <a name="next-steps"></a>Дальнейшие действия
Сведения о вариантах подключения приложений см. в статье [Библиотеки подключений для базы данных Azure для MySQL](concepts-connection-libraries.md).

