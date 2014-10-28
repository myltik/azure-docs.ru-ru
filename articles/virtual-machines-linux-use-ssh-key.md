<properties linkid="article" urlDisplayName="Use SSH" pageTitle="Use SSH to connect to Linux virtual machines in Azure" metaKeywords="Azure SSH keys Linux, Linux vm SSH" description="Learn how to generate and use SSH keys with a Linux virtual machine on Azure." metaCanonical="" services="virtual-machines" documentationCenter="" title="How to Use SSH with Linux on Azure" authors="" solutions="" manager="" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author></tags>

# Использование SSH с Linux в Azure

В этом разделе описываются шаги по созданию ключей SSH, совместимых с Azure.

Текущая версия портала управления Azure принимает только открытые ключи SSH, инкапсулированные в сертификате X509. Для создания и использования ключей SSH с Azure выполните описанные ниже действия.

## Создание совместимых ключей Windows Azure в Linux

1.  При необходимости установите служебную программу `openssl`:

    **CentOS или Oracle Linux**

        `sudo yum install openssl`

    **Ubuntu**

        `sudo apt-get install openssl`

    **SLES и openSUSE**

        `sudo zypper install openssl`

2.  Используйте `openssl` для создания сертификата X509 с парой ключей 2048-разрядных ключей RSA. Ответьте на несколько вопросов, предлагаемых программой `openssl` (или оставьте ответы пустыми). Содержимое этих полей не используется платформой.

            openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem

3.  Измените разрешения для закрытого ключа, чтобы защитить его.

            chmod 600 myPrivateKey.key

4.  При создании виртуальной машины Linux загрузите `myCert.pem`. В процессе подготовки открытый ключ будет автоматически установлен в этом сертификате в файл `authorized_keys` для указанного пользователя в виртуальной машине.

5.  Если вы собираетесь обойтись без портала управления и использовать API напрямую, преобразуйте `myCert.pem` в `myCert.cer` (сертификат X509 с кодировкой DER) с помощью следующей команды:

            openssl  x509 -outform der -in myCert.pem -out myCert.cer

## Создание ключа из существующего ключа, совместимого с OpenSSH

Предыдущий пример описывает, как создать новый ключ для использования в Windows Azure. В некоторых случаях пользователи могут уже иметь открытый и закрытый ключи, совместимые с OpenSSH, и пожелать использовать те же ключи в Windows Azure.

Закрытые ключи OpenSSH могут считываться напрямую программой `openssl`. Приведенная ниже команда получает существующий закрытый ключ SSH (id\_rsa в представленном ниже примере) и создает открытый ключ `.pem`, который необходим для Azure.

    # openssl req -x509 -key ~/.ssh/id_rsa -nodes -days 365 -newkey rsa:2048 -out myCert.pem

Файл **MyCert.pem** представляет собой открытый ключ, который затем будет использоваться для подготовки виртуальной машины Linux в Windows Azure. Во время подготовки файл `.pem` будет преобразован в совместимый с `openssh` открытый ключ и размещен в `~/.ssh/authorized_keys`.

## Подключение к виртуальной машине Windows Azure с Linux

Каждая виртуальная машина Linux готовится с SSH в конкретном порте, который может отличаться от используемого стандартного порта, так что

1.  Найдите порт, который будет использоваться для подключения к виртуальной машине Linux с портала управления.
2.  Подключитесь к виртуальной машине Linux с помощью `ssh`. Будет предложено принять отпечаток пальца открытого ключа узла при первом входе.

        ssh -i  myPrivateKey.key -p <port> username@servicename.cloudapp.net

3.  (Дополнительно) Можно скопировать `myPrivateKey.key` в `~/.ssh/id_rsa` таким образом, чтобы клиент openssh смог автоматически получать это без использования параметра `-i`.

## OpenSSL на Windows

### Использование msysgit

1.  Загрузите и установите msysgit из следующего расположения: [][0]<http://msysgit.github.com/></a>
2.  Запустите `msys` из установленного каталога (например: c:\\msysgit\\msys.exe)
3.  Перейдите в каталог `bin`, введя команду `cd bin`

### Использование GitHub для Windows

1.  Загрузите и установите GitHub для Windows из следующего расположения: [][1]<http://windows.github.com/></a>
2.  Запустите оболочку Git из меню "Пуск" \> "Все программы" GitHub, Inc

### Использование cygwin

1.  Загрузите и установите Cygwin из следующего расположения: [][2]<http://cygwin.com/></a>
2.  Проверьте установку пакета OpenSSL и всех его зависимостей.
3.  Запустите `cygwin`

## Создание закрытого ключа в Windows

1.  Выполните одну инструкцию из представленного выше набора, чтобы иметь возможность запустить `openssl.exe`.
2.  Введите следующую команду:

        openssl.exe req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem

3.  Экран должен выглядеть следующим образом:

    ![linuxwelcomegit][linuxwelcomegit]

4.  Ответьте на задаваемые вопросы.
5.  Должны быть созданы два файла: `myPrivateKey.key` и `myCert.pem`.
6.  Если вы собираетесь обойтись без портала управления и использовать API напрямую, преобразуйте `myCert.pem` в `myCert.cer` (сертификат X509 с кодировкой DER) с помощью следующей команды:

        openssl.exe  x509 -outform der -in myCert.pem -out myCert.cer

## Создание PPK для Putty

1.  Загрузите и установите puttygen из следующего расположения: [][3]<http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html></a>
2.  Запустите `puttygen.exe`
3.  Выберите в меню пункты Файл \> Загрузить закрытый ключ
4.  Найдите закрытый ключ с именем `myPrivateKey.key`. Придется изменить фильтр для отображения всех файлов на \*\*All Files (\*.\*)\*\*.
5.  Щелкните **Открыть**. Появится запрос, который должен выглядеть следующим образом:

    ![linuxgoodforeignkey][linuxgoodforeignkey]

6.  Нажмите кнопку **ОК**.
7.  Щелкните **Сохранить закрытый ключ**, который выделен на следующем снимке экрана:

    ![linuxputtyprivatekey][linuxputtyprivatekey]

8.  Сохраните файл как PPK.

## Использование Putty для подключения к компьютеру Linux

1.  Загрузите и установите putty из следующего расположения: [][3]<http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html></a>
2.  Запустите putty.exe
3.  Заполните имя узла, используя IP с портала управления.

    ![linuxputtyconfig][linuxputtyconfig]

4.  Перед выбором **Открыть** щелкните вкладку Подключение \> SSH \> Проверка подлинности, чтобы указать ключ. Ниже см. снимок экрана поля для заполнения.

    ![linuxputtyprivatekey][4]

5.  Щелкните **Открыть**, чтобы подключится к виртуальной машине.

  [0]: http://msysgit.github.com/
  [1]: http://windows.github.com/
  [2]: http://cygwin.com/
  [linuxwelcomegit]: ./media/virtual-machines-linux-use-ssh-key/linuxwelcomegit.png
  [3]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
  [linuxgoodforeignkey]: ./media/virtual-machines-linux-use-ssh-key/linuxgoodforeignkey.png
  [linuxputtyprivatekey]: ./media/virtual-machines-linux-use-ssh-key/linuxputtygenprivatekey.png
  [linuxputtyconfig]: ./media/virtual-machines-linux-use-ssh-key/linuxputtyconfig.png
  [4]: ./media/virtual-machines-linux-use-ssh-key/linuxputtyprivatekey.png
