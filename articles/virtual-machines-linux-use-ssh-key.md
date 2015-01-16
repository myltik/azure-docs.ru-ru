<properties urlDisplayName="Use SSH" pageTitle="Использование SSH для подключения к виртуальным машинам Linux в Azure" metaKeywords="Azure SSH keys Linux, Linux vm SSH" description="Узнайте, как создавать и использовать ключи SSH с виртуальной машиной Linux в Azure." metaCanonical="" services="virtual-machines" documentationCenter="" title="How to Use SSH with Linux on Azure" authors="szarkos" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="10/15/2014" ms.author="szarkos" />

#Использование SSH с Linux в Azure

Текущая версия портала управления Azure принимает только открытые ключи SSH, инкапсулированные в сертификате X509. Для создания и использования ключей SSH с Azure выполните описанные ниже действия.

## Создание совместимых ключей Microsoft Azure в Linux

1. При необходимости установите служебную программу openssl:

	**CentOS или Oracle Linux**

		# sudo yum install openssl

	**Ubuntu**

		# sudo apt-get install openssl

	**SLES и openSUSE**

		# sudo zypper install openssl


2. Использование `openssl` для создания сертификата X509 с парой ключей RSA 2048 бит. Ответьте на несколько вопросов, предлагаемых программой openssl (или оставьте поле для ответа пустым). Содержимое этих полей не используется платформой.

		# openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem

3.	Измените разрешения для закрытого ключа, чтобы защитить его.

		# chmod 600 myPrivateKey.key

4.	При создании виртуальной машины Linux загрузите `myCert.pem`. В процессе подготовки открытый ключ будет автоматически установлен в этом сертификате в файл `authorized_keys` для указанного пользователя в виртуальной машине.

5.	Если вы собираетесь использовать API напрямую, а не использовать портал управления, преобразуйте `myCert.pem` в `myCert.cer` (сертификат X509 с декодировкой DER) с помощью следующей команды:

		# openssl x509 -outform der -in myCert.pem -out myCert.cer


## Создание ключа из существующего ключа, совместимого с OpenSSH
Предыдущий пример описывает, как создать новый ключ для использования в Windows Azure. В некоторых случаях у вас уже могут быть открытый и закрытый ключи, совместимые с OpenSSH, и вы захотите использовать те же ключи в Windows Azure.

Закрытые ключи OpenSSH могут считываться напрямую программой openssl. Приведенная ниже команда получает существующий закрытый ключ SSH (id_rsa в представленном ниже примере) и создает открытый ключ (PEM-файл), который необходим для Azure.

	# openssl req -x509 -key ~/.ssh/id_rsa -nodes -days 365 -newkey rsa:2048 -out myCert.pem

Файл **myCert.pem** представляет собой открытый ключ, который затем будет использоваться для подготовки виртуальной машины Linux в Microsoft Azure. Во время подготовки файл .pem будет преобразован в совместимый открытый ключ openssh и помещен в ~/.ssh/authorized_keys.


## Подключение к виртуальной машине Windows Azure с Linux

1. В некоторых случаях конечная точка SSH для виртуальной машины Linux может быть настроена не для порта по умолчанию (22), а для другого порта. Вы можете найти номер соответствующего порта на панели мониторинга для виртуальной машины с портала управления (в "Сведения об SSH").

2.	Подключитесь к виртуальной машине Linux с помощью `ssh`. Будет предложено принять отпечаток пальца открытого ключа узла при первом входе.

		# ssh -i  myPrivateKey.key -p <port> username@servicename.cloudapp.net

3.	(Необязательно) Вы можете скопировать myPrivateKey.key в ~/.ssh/id_rsa таким образом, чтобы клиент OpenSSH смог автоматически получать этот ключ без использования параметра -i.

## Получение OpenSSL в Windows
### Использование msysgi

1.	Загрузите и установите msysgit из следующего расположения: [http://msysgit.github.com/](http://msysgit.github.com/)
2.	Запустите `msys` из установленных каталога (пример: c:\msysgit\msys.exe)
3.	Измените каталог `bin`, введя `cd bin`

###Использование GitHub для Windows

1.	Загрузите и установите GitHub для Windows из следующего расположения: [http://windows.github.com/](http://windows.github.com/)
2.	Запустите оболочку Git из меню "Пуск" > "Все программы" GitHub, Inc

###Использование cygwin

1.	Загрузите и установите Cygwin из следующего расположения: [http://cygwin.com/](http://cygwin.com/)
2.	Проверьте установку пакета OpenSSL и всех его зависимостей.
3.	Запустите `cygwin`

## Создание закрытого ключа в Windows

1.	Выполните одну инструкцию из набора выше, чтобы иметь возможность выполнить `openssl.exe`
2.	Введите следующую команду:

		# openssl.exe req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem

3.	Экран должен выглядеть следующим образом:

	![linuxwelcomegit](./media/virtual-machines-linux-use-ssh-key/linuxwelcomegit.png)

4.	Ответьте на задаваемые вопросы.
5.	Должны быть созданы два файла: myPrivateKey.key и myCert.pem.
6.	Если вы собираетесь использовать API напрямую, а не использовать портал управления, преобразуйте `myCert.pem` в `myCert.cer` (сертификат X509 с декодировкой DER) с помощью следующей команды:

		# openssl.exe  x509 -outform der -in myCert.pem -out myCert.cer

## Создание PPK для Putty

1. Загрузите и установите Puttygen из следующего расположения: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)

2. Puttygen, возможно, не удастся прочитать закрытый ключ, созданный ранее (myPrivateKey.key). Выполните следующую команду для его преобразования в закрытый ключ RSA, который распознается Puttygen:

		# openssl rsa -in ./myPrivateKey.key -out myPrivateKey_rsa
		# chmod 600 ./myPrivateKey_rsa

	Вышеуказанная команда должна создать новый закрытый ключ с именем myPrivateKey_rsa.

3. Запустите `puttygen.exe`

4. Выберите в меню пункты Файл > Загрузить закрытый ключ

5. Найдите закрытый ключ с именем myPrivateKey_rsa. Вам потребуется изменить фильтр отображения файлов на **Все файлы (\*.\*)**

6. Щелкните **Открыть**. Появится запрос, который должен выглядеть следующим образом:

	![linuxgoodforeignkey](./media/virtual-machines-linux-use-ssh-key/linuxgoodforeignkey.png)

7. Нажмите кнопку **OК**

8. Нажмите кнопку **Сохранить закрытый ключ**, которая выделена на следующем снимке экрана:

	![linuxputtyprivatekey](./media/virtual-machines-linux-use-ssh-key/linuxputtygenprivatekey.png)

9. Сохраните файл как PPK.


## Использование Putty для подключения к компьютеру Linux

1.	Загрузите и установите putty из следующего расположения: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
2.	Запустите putty.exe
3.	Заполните имя узла, используя IP с портала управления.

	![linuxputtyconfig](./media/virtual-machines-linux-use-ssh-key/linuxputtyconfig.png)

4.	Прежде чем щелкнуть **Открыть**, щелкните "Подключение" > SSH > вкладка "Аутентификация", чтобы указать ключ. Ниже см. снимок экрана поля для заполнения.

	![linuxputtyprivatekey](./media/virtual-machines-linux-use-ssh-key/linuxputtyprivatekey.png)

5.	Щелкните **Открыть**, чтобы подключиться к виртуальной машине.

<!--HONumber=35.1-->
