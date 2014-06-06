<properties linkid="article" urlDisplayName="Использование SSH" pageTitle="Использование SSH для подключения к виртуальным машинам Linux в Azure" metaKeywords="ключи SSH Azure, виртуальная машина Linux, SSH" description="Узнайте, как создать и использовать ключи SSH на виртуальных машинах Linux в Azure." metaCanonical="" services="virtual-machines" documentationCenter="" title="Использование SSH с Linux в Azure" authors="" solutions="" manager="" editor="" />





#Использование SSH с Linux в Azure

В этом разделе описываются шаги по созданию ключей SSH, совместимых с Azure.

Текущая версия портала управления Azure принимает только открытые ключи SSH, инкапсулированные в сертификате X509. Для создания и использования ключей SSH с Azure выполните описанные ниже действия.

## Создание совместимых ключей Windows Azure в Linux ##

1. Установите служебную программу `openssl`, если необходимо.

	**CentOS или Oracle Linux**

		`sudo yum install openssl`

	**Ubuntu**

		`sudo apt-get install openssl`

	**SLES и openSUSE**

		`sudo zypper install openssl`


2. Используйте программу `openssl`, чтобы создать сертификат X509 с парой 2048-разрядных ключей RSA. Ответьте на несколько вопросов, предлагаемых программой `openssl` (или оставьте ответы пустыми). Содержимое этих полей не используется платформой.

			openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem

3.	Измените разрешения для закрытого ключа, чтобы защитить его.

			chmod 600 myPrivateKey.key
4.	При создании виртуальной машины Linux отправьте `myCert.pem`. В процессе подготовки в этом сертификате будет автоматически установлен открытый ключ в файле `authorized_keys` для указанного пользователя на виртуальной машине.

5.	Если вы собираетесь обойтись без портала управления и использовать API напрямую, преобразуйте `myCert.pem` в `myCert.cer` (сертификат X509 с кодировкой DER) с помощью следующей команды:

			openssl  x509 -outform der -in myCert.pem -out myCert.cer


## Создание ключа из существующего ключа, совместимого с OpenSSH
Предыдущий пример описывает, как создать новый ключ для использования в Windows Azure. В некоторых случаях пользователи могут уже иметь открытый и закрытый ключи, совместимые с OpenSSH, и пожелать использовать те же ключи в Windows Azure.

Закрытые ключи OpenSSH могут считываться напрямую программой `openssl`. Приведенная ниже команда получает существующий закрытый ключ SSH (id_rsa в представленном ниже примере) и создает открытый ключ `.pem`, который необходим для Windows Azure.

	# openssl req -x509 -key ~/.ssh/id_rsa -nodes -days 365 -newkey rsa:2048 -out myCert.pem

Файл **MyCert.pem** представляет собой открытый ключ, который затем будет использоваться для подготовки виртуальной машины Linux в Windows Azure. Во время подготовки файл `.pem` будет преобразован в совместимый открытый ключ `openssh` и помещен в `~/.ssh/authorized_keys`.


## Подключение к виртуальной машине Windows Azure из Linux
Каждая виртуальная машина Linux готовится с SSH в конкретном порте, который может отличаться от используемого стандартного порта, так что 

1.	Найдите порт, который будет использоваться для подключения к виртуальной машине Linux с портала управления.
2.	Подключитесь к виртуальной машине Linux с помощью `ssh`. Будет предложено принять отпечаток пальца открытого ключа узла при первом входе.

		ssh -i  myPrivateKey.key -p <port> username@servicename.cloudapp.net
3.	(Дополнительно) Вы можете скопировать `myPrivateKey.key` в `~/.ssh/id_rsa`, чтобы клиент openssh мог автоматически получить этот ключ без использования параметра `-i`.

## OpenSSL на Windows ##
### Использование msysgit ###

1.	Загрузите и установите msysgit из следующего расположения: [http://msysgit.github.com/](http://msysgit.github.com/)
2.	Запустите `msys` из установленного каталога (например, c:\msysgit\msys.exe)
3.	Перейдите в каталог `bin`, введя команду `cd bin`

###Использование GitHub для Windows###

1.	Загрузите и установите GitHub для Windows из следующего расположения: [http://windows.github.com/](http://windows.github.com/)
2.	Запустите оболочку Git из меню "Пуск" > "Все программы" GitHub, Inc

###Использование cygwin###

1.	Загрузите и установите Cygwin из следующего расположения: [http://cygwin.com/](http://cygwin.com/)
2.	Проверьте установку пакета OpenSSL и всех его зависимостей.
3.	Запустите `cygwin`

## Создание закрытого ключа в Windows ##

1.	Выполните одну инструкцию из представленного выше набора, чтобы иметь возможность выполнить `openssl.exe`
2.	Введите следующую команду:

		openssl.exe req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem

3.	Экран должен выглядеть следующим образом:

	![linuxwelcomegit](./media/linux-use-ssh-key/linuxwelcomegit.png)

4.	Ответьте на задаваемые вопросы.
5.	Будут созданы два файла: `myPrivateKey.key` и `myCert.pem`.
6.	Если вы собираетесь обойтись без портала управления и использовать API напрямую, преобразуйте `myCert.pem` в `myCert.cer` (сертификат X509 с кодировкой DER) с помощью следующей команды:

		openssl.exe  x509 -outform der -in myCert.pem -out myCert.cer

## Создание PPK для Putty ##

1.	Загрузите и установите puttygen из следующего расположения: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
2.	Запустите `puttygen.exe`
3.	Выберите меню: Файл > Загрузить закрытый ключ
4.	Найдите закрытый ключ с именем `myPrivateKey.key`. Требуется изменить фильтр файлов для отображения на **Все файлы (\*.*)**
5.	Щелкните **Открыть**. Появится запрос, который должен выглядеть следующим образом:

	![linuxgoodforeignkey](./media/linux-use-ssh-key/linuxgoodforeignkey.png)

6.	Нажмите кнопку **ОК**.
7.	Щелкните **Сохранить закрытый ключ**, который выделен на следующем снимке экрана:

	![linuxputtyprivatekey](./media/linux-use-ssh-key/linuxputtygenprivatekey.png)

8.	Сохраните файл как PPK.

## Использование Putty для подключения к компьютеру Linux ##

1.	Загрузите и установите putty из следующего расположения: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
2.	Запустите putty.exe
3.	Заполните имя узла, используя IP с портала управления.

	![linuxputtyconfig](./media/linux-use-ssh-key/linuxputtyconfig.png)

4.	Перед выбором **Открыть** щелкните вкладку Подключение > SSH > Проверка подлинности, чтобы указать ключ. Ниже см. снимок экрана поля для заполнения.

	![linuxputtyprivatekey](./media/linux-use-ssh-key/linuxputtyprivatekey.png)

5.	Щелкните **Открыть**, чтобы подключится к виртуальной машине.

