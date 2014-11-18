<properties urlDisplayName="Use SSH" pageTitle="Использование SSH для подключения к виртуальным машинам Linux в Azure" metaKeywords="Azure SSH keys Linux, Linux vm SSH" description="Узнайте, как создавать и использовать ключи SSH с виртуальной машиной Linux в Azure." metaCanonical="" services="virtual-machines" documentationCenter="" title="Использование SSH с Linux в Azure" authors="timlt" solutions="" manager="timlt" editor="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-linux" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="timlt" />

# Использование SSH с Linux в Azure

В этом разделе описываются шаги по созданию ключей SSH, совместимых с Azure.

Текущая версия портала управления Azure принимает только открытые ключи SSH, инкапсулированные в сертификате X509. Для создания и использования ключей SSH с Azure выполните описанные ниже действия.

## Создание совместимых ключей Windows Azure в Linux

1.  При необходимости установите служебную программу `openssl`:

    **CentOS или Oracle Linux**

        # sudo yum install openssl

    **Ubuntu**

        # sudo apt-get install openssl

    **SLES и openSUSE**

        # sudo zypper install openssl

2.  Используйте `openssl` для создания сертификата X509 с парой ключей 2048-разрядных ключей RSA. Ответьте на несколько вопросов, предлагаемых программой `openssl` (или оставьте ответы пустыми). Содержимое этих полей не используется платформой.

        # openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem

3.  Измените разрешения для закрытого ключа, чтобы защитить его.

        # chmod 600 myPrivateKey.key

4.  При создании виртуальной машины Linux загрузите `myCert.pem`. В процессе подготовки открытый ключ будет автоматически установлен в этом сертификате в файл `authorized_keys` для указанного пользователя в виртуальной машине.

5.  Если вы собираетесь обойтись без портала управления и использовать API напрямую, преобразуйте `myCert.pem` в `myCert.cer` (сертификат X509 с кодировкой DER) с помощью следующей команды:

        # openssl  x509 -outform der -in myCert.pem -out myCert.cer

## Создание ключа из существующего ключа, совместимого с OpenSSH

Предыдущий пример описывает, как создать новый ключ для использования в Windows Azure. В некоторых случаях у вас уже могут быть открытый и закрытый ключи, совместимые с OpenSSH, и вы захотите использовать те же ключи в Windows Azure.

Закрытые ключи OpenSSH могут считываться напрямую программой `openssl`. Приведенная ниже команда получает существующий закрытый ключ SSH (id\_rsa в представленном ниже примере) и создает открытый ключ `.pem`, который необходим для Azure.

    # openssl req -x509 -key ~/.ssh/id_rsa -nodes -days 365 -newkey rsa:2048 -out myCert.pem

Файл **MyCert.pem** представляет собой открытый ключ, который затем будет использоваться для подготовки виртуальной машины Linux в Windows Azure. Во время подготовки файл `.pem` будет преобразован в совместимый с `openssh` открытый ключ и размещен в `~/.ssh/authorized_keys`.

## Подключение к виртуальной машине Windows Azure с Linux

1.  В некоторых случаях конечная точка SSH для виртуальной машины Linux может быть настроена для другого порта, а не для порта 22 по умолчанию. Правильный номер порта можно узнать в панели мониторинга виртуальной машины на портале управления (в разделе "Сведения о SSH").

2.  Подключитесь к виртуальной машине Linux с помощью `ssh`. Будет предложено принять отпечаток пальца открытого ключа узла при первом входе.

        # ssh -i  myPrivateKey.key -p <port> username@servicename.cloudapp.net

3.  (Дополнительно) Можно скопировать `myPrivateKey.key` в `~/.ssh/id_rsa` таким образом, чтобы клиент OpenSSH смог автоматически получать это без использования параметра `-i`.

## OpenSSL на Windows

### Использование msysgit

1.  Загрузите и установите msysgit из следующего расположения: <http://msysgit.github.com/>
2.  Запустите `msys` из установленного каталога (например: c:\\msysgit\\msys.exe)
3.  Перейдите в каталог `bin`, введя команду `cd bin`

### Использование GitHub для Windows

1.  Загрузите и установите GitHub для Windows из следующего расположения: <http://windows.github.com/>
2.  Запустите оболочку Git из меню "Пуск" \> "Все программы" GitHub, Inc

### Использование cygwin

1.  Загрузите и установите Cygwin из следующего расположения: <http://cygwin.com/>
2.  Проверьте установку пакета OpenSSL и всех его зависимостей.
3.  Запустите `cygwin`

## Создание закрытого ключа в Windows

1.  Выполните одну инструкцию из представленного выше набора, чтобы иметь возможность запустить `openssl.exe`.
2.  Введите следующую команду:

        # openssl.exe req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem

3.  Экран должен выглядеть следующим образом:

    ![linuxwelcomegit][linuxwelcomegit]

4.  Ответьте на задаваемые вопросы.
5.  Должны быть созданы два файла: `myPrivateKey.key` и `myCert.pem`.
6.  Если вы собираетесь обойтись без портала управления и использовать API напрямую, преобразуйте `myCert.pem` в `myCert.cer` (сертификат X509 с кодировкой DER) с помощью следующей команды:

        # openssl.exe  x509 -outform der -in myCert.pem -out myCert.cer

## Создание PPK для Putty

1.  Загрузите и установите Puttygen из следующего расположения: <http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html>

2.  Puttygen, возможно, не удастся прочитать закрытый ключ, созданный ранее (`myPrivateKey.key`). Выполните следующую команду для его преобразования в закрытый ключ RSA, который распознается Puttygen:

        # openssl rsa -in ./myPrivateKey.key -out myPrivateKey_rsa
        # chmod 600 ./myPrivateKey_rsa

    Вышеуказанная команда должна создать новый закрытый ключ с именем myPrivateKey\_rsa.

3.  Запустите `puttygen.exe`

4.  Выберите в меню пункты Файл \> Загрузить закрытый ключ

5.  Найдите закрытый ключ с именем `myPrivateKey_rsa`. Требуется изменить фильтр файлов для отображения на **Все файлы (\*.\*)**

6.  Щелкните **Открыть**. Появится запрос, который должен выглядеть следующим образом:

    ![linuxgoodforeignkey][linuxgoodforeignkey]

7.  Нажмите кнопку **ОК**.

8.  Щелкните **Сохранить закрытый ключ**, который выделен на следующем снимке экрана:

    ![linuxputtyprivatekey][linuxputtyprivatekey]

9.  Сохраните файл как PPK.

## Использование Putty для подключения к компьютеру Linux

1.  Загрузите и установите putty из следующего расположения: <http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html>
2.  Запустите putty.exe
3.  Заполните имя узла, используя IP с портала управления.

    ![linuxputtyconfig][linuxputtyconfig]

4.  Перед выбором **Открыть** щелкните вкладку Подключение \> SSH \> Проверка подлинности, чтобы указать ключ. Ниже см. снимок экрана поля для заполнения.

    ![linuxputtyprivatekey][1]

5.  Щелкните **Открыть**, чтобы подключится к виртуальной машине.

  [linuxwelcomegit]: ./media/virtual-machines-linux-use-ssh-key/linuxwelcomegit.png
  [linuxgoodforeignkey]: ./media/virtual-machines-linux-use-ssh-key/linuxgoodforeignkey.png
  [linuxputtyprivatekey]: ./media/virtual-machines-linux-use-ssh-key/linuxputtygenprivatekey.png
  [linuxputtyconfig]: ./media/virtual-machines-linux-use-ssh-key/linuxputtyconfig.png
  [1]: ./media/virtual-machines-linux-use-ssh-key/linuxputtyprivatekey.png
