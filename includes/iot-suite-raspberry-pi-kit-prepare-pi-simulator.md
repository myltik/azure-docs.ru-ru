## <a name="prepare-your-raspberry-pi"></a>Подготовка устройства Raspberry Pi

### <a name="install-raspbian"></a>Установка Raspbian

Если вы впервые используете устройство Raspberry Pi, необходимо установить операционную систему Raspbian с помощью NOOBS на карте SD, входящей в состав набора. В [руководстве по программному обеспечению Raspberry Pi][lnk-install-raspbian] содержатся сведения об установке операционной системы на устройстве Raspberry Pi. В этом руководстве предполагается, что операционная система Raspbian уже установлена на Raspberry Pi.

> [!NOTE]
> На картах SD, входящих в [начальный набор Microsoft Azure IoT для Raspberry Pi 3][lnk-starter-kits], уже установлены NOOBS. Вы можете установить Raspberry Pi из этой карты, а также другую операционную систему Raspbian.

Чтобы завершить настройку оборудования, сделайте следующее:

- Подключите Raspberry Pi к источнику питания, входящему в состав набора.
- Подключите Raspberry Pi к сети с помощью кабеля Ethernet, входящего в состав набора. Кроме того, для Raspberry Pi можно настроить [беспроводное подключение][lnk-pi-wireless].

Установка оборудования для Raspberry Pi завершена.

### <a name="sign-in-and-access-the-terminal"></a>Вход и доступ к терминалу

Существует два варианта получения доступа к среде терминала на Raspberry Pi.

- Если клавиатура и монитор подключены к Raspberry Pi, вы можете использовать графический пользовательский интерфейс Raspbian, чтобы получить доступ к окну терминала.

- Получите доступ к командной строке на устройстве Raspberry Pi с настольного компьютера, используя SSH.

#### <a name="use-a-terminal-window-in-the-gui"></a>Использование окна терминала в графическом пользовательском интерфейсе

По умолчанию в качестве учетных данных для Raspbian используется имя пользователя **pi** и пароль **raspberry**. На панели задач в графическом пользовательском интерфейсе можно запустить служебную программу **Terminal** с помощью значка в виде монитора.

#### <a name="sign-in-with-ssh"></a>Вход с помощью SSH

Используйте SSH, чтобы получить доступ к Raspberry Pi с помощью командной строки. Сведения о настройке SSH на устройстве Raspberry Pi и о подключении из [Windows][lnk-ssh-windows], [Linux и Mac OS][lnk-ssh-linux] см. в руководстве по [SSH (SECURE SHELL)][lnk-pi-ssh].

Войдите, используя имя пользователя **pi** и пароль **raspberry**.

#### <a name="optional-share-a-folder-on-your-raspberry-pi"></a>Совместный доступ к папке на устройстве Raspberry Pi (необязательно)

При необходимости вы можете предоставить общий доступ к папке на устройстве Raspberry Pi для среды рабочего стола. Предоставив общий доступ к папке, вы сможете использовать предпочитаемый классический текстовый редактор (например, [Visual Studio Code](https://code.visualstudio.com/) или [Sublime Text](http://www.sublimetext.com/)), чтобы редактировать файлы на устройстве Raspberry Pi вместо использования `nano` или `vi`.

Чтобы предоставить общий доступ к папке в Windows, необходимо настроить сервер Samba на устройстве Raspberry Pi. Вы также можете использовать встроенный сервер [SFTP](https://www.raspberrypi.org/documentation/remote-access/) с клиентом SFTP на настольном компьютере.

[lnk-install-raspbian]: https://www.raspberrypi.org/learning/software-guide/quickstart/
[lnk-pi-wireless]: https://www.raspberrypi.org/documentation/configuration/wireless/README.md
[lnk-pi-ssh]: https://www.raspberrypi.org/documentation/remote-access/ssh/README.md
[lnk-ssh-windows]: https://www.raspberrypi.org/documentation/remote-access/ssh/windows.md
[lnk-ssh-linux]: https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md
[lnk-starter-kits]: https://azure.microsoft.com/develop/iot/starter-kits/