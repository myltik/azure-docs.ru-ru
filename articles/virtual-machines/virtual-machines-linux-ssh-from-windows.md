<properties 
	pageTitle="Использование SSH в Windows для подключения к виртуальным машинам Linux | Microsoft Azure" 
description="Узнайте, как создавать и использовать ключи SSH на компьютере Windows для подключения к виртуальной машине Linux в Azure." 
	services="virtual-machines-linux" 
	documentationCenter="" 
	authors="squillace" 
	manager="timlt" 
	editor=""
	tags="azure-service-management,azure-resource-manager" />

<tags 
	ms.service="virtual-machines-linux" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/04/2016" 
	ms.author="rasquill"/>

#Использование SSH с Windows в Azure

> [AZURE.SELECTOR]
- [Windows](virtual-machines-linux-ssh-from-windows.md)
- [Linux или Mac](virtual-machines-linux-ssh-from-linux.md)

В этом разделе описывается создание и использование в Windows файлов открытых и закрытых ключей в формате **ssh-rsa** и **.pem**, которые можно использовать для подключения к виртуальным машинам Linux в Azure с помощью команды **ssh**. Если файлы **.pem** уже созданы, можно воспользоваться ими для создания виртуальных машин Linux, к которым можно подключиться с помощью **ssh**. В некоторых других командах протокол **SSH** и файлы ключей используются для безопасного выполнения работы, в частности **scp** или [безопасное копирование](https://en.wikipedia.org/wiki/Secure_copy), позволяющее надежно копировать файлы на компьютеры с поддержкой подключений **SSH** и с них.


## Необходимые SSH и программы создания ключей

**SSH** &#8212; или [secure shell](https://en.wikipedia.org/wiki/Secure_Shell) &#8212; — это протокол зашифрованного подключения, позволяющий безопасно выполнять вход при наличии незащищенных подключений. Это протокол подключения по умолчанию для виртуальных машин Linux, размещенных в Azure, если виртуальные машины Linux не настроены для включения какого-либо другого механизма подключения. Пользователи Windows также могут подключаться к виртуальным машинам Linux в Azure и управлять ими с помощью реализации клиента **ssh**, однако компьютеры Windows, как правило, поставляются без клиента **ssh**-клиента, поэтому его необходимо выбрать.

К числу стандартных клиентов, доступных для установки, относятся следующие.

- [puTTY и puTTYgen]((http://www.chiark.greenend.org.uk/~sgtatham/putty/)
- [MobaXterm](http://mobaxterm.mobatek.net/)
- [Cygwin](https://cygwin.com/)
- [Git для Windows](https://git-for-windows.github.io/), поставляемый вместе со средой и необходимыми инструментами

Если вы в настроении для экспериментов, опробуйте [новый порт набора инструментов **OpenSSH** для Windows](http://blogs.msdn.com/b/powershell/archive/2015/10/19/openssh-for-windows-update.aspx). Помните, однако, что этот код в настоящее время находится на этапе разработки и базу кода необходимо тщательно изучить, прежде чем использовать код в продуктивных системах.

> [AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## Какие файлы ключей необходимо создать?

Базовая конфигурация SSH для Azure включает открытый и закрытый 2048-разрядный ключ **ssh-rsa** (по умолчанию **ssh-keygen** использует файлы **~/.ssh/id\_rsa** и **~/.ssh/id-rsa.pub**), а также файл `.pem`, созданный на основе файла закрытого ключа **id\_rsa** для использования в классической модели развертывания классического портала.

Ниже приведены сценарии развертывания и типы используемых в них файлов.

1. Ключи **ssh-rsa** нужны для любого развертывания с использованием [портала Azure](https://portal.azure.com), независимо от модели развертывания.
2. PEM-файл нужен для создания виртуальных машин с использованием [классического портала](https://manage.windowsazure.com). PEM-файлы также поддерживаются в классических развертываниях с использованием [интерфейса командной строки Azure](../xplat-cli-install.md).

> [AZURE.NOTE] Если вы планируете управлять службой, развернутой с помощью классической модели развертывания, может потребоваться создать **CER-**файл для передачи на портал, хотя это не связано с использованием протокола **ssh** или подключением к виртуальным машинам Linux, о которых идет речь в этой статье. Чтобы создать эти файлы на Linux или Mac, введите

## Получение ssh-keygen и openssl в Windows ##

[В этом разделе](#What-SSH-and-key-creation-programs-do-you-need) перечислено несколько служебных программ, которые включают `ssh-keygen` и `openssl` для Windows. Ниже приведено несколько примеров.

### Использование Msysgit ###

1.	Загрузите и установите msysgit из следующего расположения:[http://msysgit.github.com/](http://msysgit.github.com/)
2.	Запустите `msys` из каталога установки (например, c:\\msysgit\\msys.exe)
3.	Перейдите в каталог `bin`, введя `cd bin`


### Использование GitHub для Windows ###

1.	Загрузите и установите GitHub для Windows из следующего расположения: [http://windows.github.com/](http://windows.github.com/)
2.	Запустите оболочку Git из меню "Пуск" > "Все программы" GitHub, Inc

> [AZURE.NOTE] При выполнении указанных команд `openssl` может возникнуть следующая ошибка.

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

## Создание закрытого ключа##

1.	Выполните один из представленных выше наборов инструкций, чтобы иметь возможность запустить `openssl.exe`.
2.	Введите следующую команду:

		# openssl.exe req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem

3.	Экран должен выглядеть следующим образом:

	![linuxwelcomegit](./media/virtual-machines-linux-ssh-from-linux/linuxwelcomegit.png)

4.	Ответьте на задаваемые вопросы.
5.	Должны быть созданы два файла: `myPrivateKey.key` и `myCert.pem`.
6.	Если вы собираетесь обойтись без портала управления и использовать API напрямую, преобразуйте `myCert.pem` в `myCert.cer` (сертификат X509 с кодировкой DER) с помощью следующей команды:

		# openssl.exe  x509 -outform der -in myCert.pem -out myCert.cer

## Создание PPK для Putty ##

1. Загрузите и установите Puttygen из следующего расположения: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)

2. Puttygen, возможно, не удастся прочитать закрытый ключ, созданный ранее (`myPrivateKey.key`). Выполните следующую команду для его преобразования в закрытый ключ RSA, который распознается Puttygen:

		# openssl rsa -in ./myPrivateKey.key -out myPrivateKey_rsa
		# chmod 600 ./myPrivateKey_rsa

	Вышеуказанная команда должна создать новый закрытый ключ с именем myPrivateKey\_rsa.

3. Запустите `puttygen.exe`

4. Выберите меню: Файл > Загрузить закрытый ключ

5. Найдите закрытый ключ с именем `myPrivateKey_rsa`. Требуется изменить фильтр файлов для отображения на **Все файлы (*.*)**

6. Щелкните **Открыть**. Появится запрос, который должен выглядеть следующим образом:

	![linuxgoodforeignkey](./media/virtual-machines-linux-ssh-from-linux/linuxgoodforeignkey.png)

7. Нажмите кнопку **ОК**.

8. Щелкните **Сохранить закрытый ключ**, который выделен на следующем снимке экрана:

	![linuxputtyprivatekey](./media/virtual-machines-linux-ssh-from-linux/linuxputtygenprivatekey.png)

9. Сохраните файл как PPK.


## Использование Putty для подключения к компьютеру Linux ##

1.	Загрузите и установите putty из следующего расположения: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)
2.	Запустите putty.exe
3.	Заполните имя узла, используя IP с портала управления.

	![linuxputtyconfig](./media/virtual-machines-linux-ssh-from-linux/linuxputtyconfig.png)

4.	Перед выбором **Открыть** щелкните вкладку Подключение > SSH > Проверка подлинности, чтобы указать ключ. Ниже см. снимок экрана поля для заполнения.

	![linuxputtyprivatekey](./media/virtual-machines-linux-ssh-from-linux/linuxputtyprivatekey.png)

5.	Щелкните **Открыть**, чтобы подключится к виртуальной машине.
 

<!---HONumber=AcomDC_0323_2016-->