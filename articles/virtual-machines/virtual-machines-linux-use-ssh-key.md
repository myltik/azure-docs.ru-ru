<properties 
	pageTitle="Использование SSH для подключения к виртуальным машинам Linux в Azure" 
	description="Узнайте, как создавать и использовать ключи SSH с виртуальной машиной Linux в Azure." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="szarkos" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/15/2015" 
	ms.author="szark"/>

#Использование SSH с Linux в Azure

Текущая версия портала управления Azure принимает только открытые ключи SSH, инкапсулированные в сертификате X509. Для создания и использования ключей SSH с Azure выполните описанные ниже действия.

## Создание совместимых ключей Azure в Linux ##

1. При необходимости установите служебную программу `openssl`:

	**CentOS или Oracle Linux**

		# sudo yum install openssl

	**Ubuntu**

		# sudo apt-get install openssl

	**SLES и openSUSE**

		# sudo zypper install openssl


2. Используйте `openssl` для создания сертификата X509 с парой 2048-разрядных ключей RSA. Ответьте на несколько вопросов, предлагаемых программой `openssl` (или оставьте ответы пустыми). Содержимое этих полей не используется платформой.

		# openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem

3.	Измените разрешения для закрытого ключа, чтобы защитить его.

		# chmod 600 myPrivateKey.key

4.	При создании виртуальной машины Linux загрузите `myCert.pem`. В процессе подготовки открытый ключ в этом сертификате будет автоматически установлен в файл `authorized_keys` для указанного пользователя в виртуальной машине.

5.	Если вы собираетесь обойтись без портала управления и использовать API напрямую, преобразуйте `myCert.pem` в `myCert.cer` (сертификат X509 с кодировкой DER) с помощью следующей команды:

		# openssl  x509 -outform der -in myCert.pem -out myCert.cer


## Создание ключа из существующего ключа, совместимого с OpenSSH
В предыдущем примере описано, как создать новый ключ для использования в Azure. В некоторых случаях у вас уже могут быть открытый и закрытый ключи, совместимые с OpenSSH, и вы захотите использовать те же ключи в Azure.

Закрытые ключи OpenSSH могут считываться напрямую служебной программой `openssl`. Приведенная ниже команда получает существующий закрытый ключ SSH (id\_rsa в представленном ниже примере) и создает открытый ключ `.pem`, который необходим для Azure:

	# openssl req -x509 -key ~/.ssh/id_rsa -nodes -days 365 -newkey rsa:2048 -out myCert.pem

Файл **myCert.pem** представляет собой открытый ключ, который затем будет использоваться для подготовки виртуальной машины Linux в Azure. Во время подготовки файл `.pem` будет преобразован в совместимый с `openssh` открытый ключ и помещен в `~/.ssh/authorized_keys`.


## Подключение к виртуальной машине Azure из Linux

1. В некоторых случаях конечная точка SSH для виртуальной машины Linux может быть настроена не для порта по умолчанию (22), а для другого порта. Вы можете найти номер соответствующего порта на панели мониторинга для виртуальной машины с портала управления (в «Сведения об SSH»).

2.	Подключитесь к виртуальной машине Linux с помощью `ssh`. Будет предложено принять отпечаток пальца открытого ключа узла при первом входе.

		# ssh -i  myPrivateKey.key -p <port> username@servicename.cloudapp.net

3.	(Дополнительно) Вы можете скопировать `myPrivateKey.key` в `~/.ssh/id_rsa`, чтобы клиент OpenSSH мог автоматически получать его без использования параметра `-i`.

## OpenSSL на Windows ##

Существует несколько служебных программ, которые включают `openssl` для Windows. Ниже приведено несколько примеров

### Использование Msysgit ###

1.	Загрузите и установите msysgit из следующего расположения:[http://msysgit.github.com/](http://msysgit.github.com/)
2.	Запустите `msys` из каталога установки (например, c:\\msysgit\\msys.exe)
3.	Перейдите в каталог `bin`, введя `cd bin`


### Использование GitHub для Windows ###

1.	Загрузите и установите GitHub для Windows из следующего расположения: [http://windows.github.com/](http://windows.github.com/)
2.	Запустите оболочку Git из меню "Пуск" > "Все программы" GitHub, Inc

	**Примечание.** При выполнении указанных команд `openssl` вы можете столкнуться со следующей ошибкой:

		Unable to load config info from /usr/local/ssl/openssl.cnf

	Наиболее простым решением этой проблемы будет задать переменную среды `OPENSSL_CONF`. Процесс задания этой переменной будет зависеть от настроенной вами в Github оболочки:

	**Powershell:**

		$Env:OPENSSL_CONF="$Env:GITHUB_GIT\ssl\openssl.cnf"

	**CMD:**

		set OPENSSL_CONF=%GITHUB_GIT%\ssl\openssl.cnf

	**Git Bash:**

		export OPENSSL_CONF=$GITHUB_GIT/ssl/openssl.cnf


###Использование Cygwin###

1.	Загрузите и установите Cygwin из следующего расположения: [http://cygwin.com/](http://cygwin.com/)
2.	Проверьте установку пакета OpenSSL и всех его зависимостей.
3.	Запустите `cygwin`


## Создание закрытого ключа в Windows ##

1.	Выполните один из представленных выше наборов инструкций, чтобы иметь возможность запустить `openssl.exe`.
2.	Введите следующую команду:

		# openssl.exe req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem

3.	Экран должен выглядеть следующим образом:

	![linuxwelcomegit](./media/virtual-machines-linux-use-ssh-key/linuxwelcomegit.png)

4.	Ответьте на задаваемые вопросы.
5.	Должны быть созданы два файла: `myPrivateKey.key` и `myCert.pem`.
6.	Если вы собираетесь обойтись без портала управления и использовать API напрямую, преобразуйте `myCert.pem` в `myCert.cer` (сертификат X509 с кодировкой DER) с помощью следующей команды:

		# openssl.exe  x509 -outform der -in myCert.pem -out myCert.cer

## Создание PPK для Putty ##

1. Загрузите и установите Puttygen из следующего расположения: [http://www.chiark.greenend.org.uk/\~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)

2. Puttygen, возможно, не удастся прочитать закрытый ключ, созданный ранее (`myPrivateKey.key`). Выполните следующую команду для его преобразования в закрытый ключ RSA, который распознается Puttygen:

		# openssl rsa -in ./myPrivateKey.key -out myPrivateKey_rsa
		# chmod 600 ./myPrivateKey_rsa

	Вышеуказанная команда должна создать новый закрытый ключ с именем myPrivateKey\_rsa.

3. Запустите `puttygen.exe`

4. Выберите меню: Файл > Загрузить закрытый ключ

5. Найдите закрытый ключ с именем `myPrivateKey_rsa`. Требуется изменить фильтр файлов для отображения на **Все файлы (*.*)**

6. Щелкните **Открыть**. Появится запрос, который должен выглядеть следующим образом:

	![linuxgoodforeignkey](./media/virtual-machines-linux-use-ssh-key/linuxgoodforeignkey.png)

7. Нажмите кнопку **ОК**.

8. Щелкните **Сохранить закрытый ключ**, который выделен на следующем снимке экрана:

	![linuxputtyprivatekey](./media/virtual-machines-linux-use-ssh-key/linuxputtygenprivatekey.png)

9. Сохраните файл как PPK.


## Использование Putty для подключения к компьютеру Linux ##

1.	Загрузите и установите putty из следующего расположения: [http://www.chiark.greenend.org.uk/\~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
2.	Запустите putty.exe
3.	Заполните имя узла, используя IP с портала управления.

	![linuxputtyconfig](./media/virtual-machines-linux-use-ssh-key/linuxputtyconfig.png)

4.	Перед выбором **Открыть** щелкните вкладку Подключение > SSH > Проверка подлинности, чтобы указать ключ. Ниже см. снимок экрана поля для заполнения.

	![linuxputtyprivatekey](./media/virtual-machines-linux-use-ssh-key/linuxputtyprivatekey.png)

5.	Щелкните **Открыть**, чтобы подключится к виртуальной машине.
 

<!---HONumber=August15_HO6-->