<properties
	pageTitle="Управление устройствами с помощью пакета SDK для шлюза | Microsoft Azure"
	description="Пошаговое руководство по пакету SDK для шлюза центра IoT Azure, в котором описывается, как реализовать управление устройствами при использовании пакета SDK для шлюза"
	services="iot-hub"
	documentationCenter=""
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/12/2016"
     ms.author="dobett"/>


# Пакет SDK для шлюза IoT (бета-версия): управление устройствами с помощью пакета SDK для шлюза

В этом руководстве показано, как использовать возможности [управления устройствами][lnk-device-management] центра IoT с [пакетом SDK для шлюза центра IoT Azure][lnk-gateway-sdk]. Здесь для размещения шлюза с виртуальными устройствами, которые отправляют данные телеметрии в центр IoT, используется вычислительный модуль Intel Edison. Возможности управления устройствами в центре IoT позволяют удаленно обновлять встроенное ПО платы Edison.

Темы, рассматриваемые в этом руководстве:

- **Архитектура** — важные сведения об архитектуре в примере управления устройством.

- **Сборка и запуск**: шаги, необходимые для сборки и запуска примера.

## Архитектура

В этом руководстве вы подготовите плату Intel Edison к работе в качестве шлюза устройства IoT, подключенного к центру IoT. Плату можно настроить таким образом, чтобы ею можно было управлять в центре IoT. На следующей схеме показаны основные элементы решения, создаваемого в этом руководстве:

![Архитектура шлюза и управления устройствами][img-architecture]

### Устройства

В этом решении есть три устройства IoT, подключенные к центру IoT:

- Плата Edison — это устройство с именем **GW-device**. В руководстве для обновления встроенного ПО этого физического устройства используются возможности управления устройствами центра IoT.

- Два виртуальных устройства (**GW-ble1-demo** и **GW-ble2-demo**). Эти устройства моделируют устройства Bluetooth с низким энергопотреблением, которые подключаются к центру IoT через шлюз и отправляют данные телеметрии (значения температуры) в центр.

### Программное обеспечение для шлюза

Программное обеспечение для шлюза запускается как служба на плате Edison. Телеметрию (значения температуры) создают два виртуальных устройства. Модуль сопоставления сопоставляет эти виртуальные устройства с устройствами, зарегистрированными в центре IoT, а модуль HTTP обрабатывает данные, отправляемые с конечной точки центра IoT. Подробные сведения об этом сценарии представлены в статье [Пакет SDK для шлюза IoT: отправка сообщений с устройства в облако через виртуальное устройство][lnk-gateway-scenario].

### Клиент для управления устройствами

[Клиент для управления устройствами][lnk-device-management] выполняется как служба на плате Edison. Это позволяет запускать удаленные задания на плате Edison, например [обновление встроенного ПО][lnk-dm-jobs], перезагрузка и обновление конфигурации, а также запрашивать свойства устройств. В этом руководстве клиент для управления устройствами получает и обрабатывает запрос на обновление встроенного ПО на плате Edison.

### Центр IoT

[Центр IoT Azure][lnk-iot-hub] — это полностью управляемая служба, которая обеспечивает надежный и защищенный двунаправленный обмен данными между миллионами устройств IoT и серверной частью решения. В этом руководстве центр IoT выполняет следующие действия:

- обеспечивает подключение трех устройств к облаку;
- получает от шлюза данные телеметрии, созданные виртуальными устройствами;
- обеспечивает отправку запроса на обновление встроенного ПО на плату Edison;
- отслеживает ход выполнения обновления встроенного ПО.

### Пример пользовательского интерфейса управления устройствами

[Пример пользовательского интерфейса управления устройствами][lnk-dm-sample-ui] — это пример веб-приложения, которое позволяет использовать возможности управления устройствами центра IoT в интерактивном веб-интерфейсе. Например, его можно использовать для запроса устройств, подключенных к центру IoT, и отправки заданий обновления встроенного ПО на устройства. В этом руководстве с помощью примера пользовательского интерфейса вы отправите задание обновления встроенного ПО на плату Edison и будете отслеживать ход его выполнения.

## Выполнение сборки и запуск

Для выполнения этого примера необходимо создать пользовательский образ платы Edison, включающий в себя программное обеспечение шлюза центра IoT и клиент для управления устройствами.

Перед началом выполнения описанных ниже действий убедитесь, что плата Edison может подключаться к беспроводной сети. Чтобы настроить плату Edison, ее необходимо подключить к главному компьютеру. Позже с его помощью можно переключиться с платы Edison на созданный пользовательский образ. Intel предоставляет руководства по началу работы для следующих операционных систем:

- [Начало работы с макетной платой Intel Edison на 64-разрядной версии Windows][lnk-setup-win64].
- [Начало работы с макетной платой Intel Edison на 32-разрядной версии Windows][lnk-setup-win32].
- [Начало работы с платой Intel® Edison в ОС Linux][lnk-setup-linux].

Чтобы настроить плату Edison и ознакомиться с ней, выполните все шаги, описанные в этих статьях по началу работы, кроме следующих:

- Переключение на последнюю версию встроенного ПО. Вы обновите встроенное ПО в рамках этого руководства, поэтому сейчас этот шаг не нужно выполнять.
- Последний шаг ("Выбор IDE"), который не требуется для данного руководства.

Настроив плату Edison и установив необходимые драйверы на главном компьютере, убедитесь, что к плате Edison можно подключиться с использованием последовательного терминала. На странице [Setting up a serial terminal][lnk-serial-connection] \(Настройка последовательного терминала) на веб-сайте Intel содержатся ссылки на статьи с указаниями по настройке для операционных систем сервера виртуальных машин, например Windows и Linux.

Кроме того, необходимо выполнить следующие задачи:

- [Создайте центр IoT][lnk-create-hub] в подписке Azure. Для работы с этим руководством требуется имя центра. Если у вас еще нет подписки Azure, получите [бесплатную учетную запись][lnk-free-trial].
- Добавьте в центр IoT три устройства (**GW-ble1-demo**, **GW-ble2-demo** и **GW-device**) и запишите их идентификаторы и ключи устройства. Для добавления этих устройств в центр IoT, созданный при выполнении предыдущего шага, и получения их ключей можно использовать [обозреватель устройств или средство iothub-explorer][lnk-explorer-tools]. Двое из этих устройств (**GW-ble1-demo** и **GW-ble2-demo**) будут использоваться в качестве виртуальных устройств BLE, подключенных к шлюзу, а одно из них (**GW-device**) будет использоваться для идентификации устройства шлюза Edison в качестве клиента для управления устройствами, которым можно управлять из центра IoT.

### Подготовка среды сборки и обеспечение возможности создания пользовательского образа

Чтобы создать пользовательский образ для платы Edison, нужна среда Linux. В этих шагах предполагается, что вы используете Ubuntu 14.04. На момент написания статьи рекомендуется использовать эту платформу. В главном разделе должно быть не менее 40 ГБ свободного места.

> [AZURE.NOTE] На компьютере с 4 ядрами ЦП выполнение скрипта, который создает пользовательский образ, может занять до 6 часов. Скрипт можно выполнить быстрее на более мощном компьютере с большим количеством ядер ЦП.

Для выполнения шагов, описанных в этом разделе, мы ознакомились со следующими статьями и документами: [Intel Edison Board Support Package][lnk-inteledison-bsp] \(Пакет поддержки платы Intel Edison), [Manually Building Yocto Images for the Intel Edison Board from Source][lnk-hackgnar] \(Создание образов Yocto для платы Intel Edison из источника) и [Creating a Custom Linux Kernel for the Edison (release 2.1)][lnk-shawnhymel] \(Создание пользовательского ядра Linux для Edison (выпуск 2.1)).

1. Войдите в компьютер Ubuntu 14.04 и выполните следующую команду в домашней папке, чтобы скачать исходный пакет Edison:
    
    ```
    curl -O http://downloadmirror.intel.com/25028/eng/edison-src-ww25.5-15.tgz
    ```

2. Распакуйте исходный пакет, чтобы создать папку **edison-src** в домашней папке, с помощью следующей команды и перейдите к новой папке **edison-src**:
    
    ```
    tar xfvz edison-src-ww25.5-15.tgz
    cd edison-src/
    ```

3. Установите пакеты необходимых компонентов:
    
    ```
    sudo apt-get -y install build-essential git diffstat gawk chrpath texinfo libtool gcc-multilib libsdl1.2-dev u-boot-tools
    ```

4. Создайте в папке **edison-src** два каталога:
    
    ```
    mkdir bitbake_download_dir
    mkdir bitbake_sstate_dir 
    ```

5. Запустите скрипт, указанный ниже, чтобы скачать среду сборки. При наличии более чем 4 ядер ЦП задайте для аргументов скрипта **--parallel\_make** и **--bb\_number\_thread** значение, соответствующее числу доступных ядер:
    
    ```
    ./meta-intel-edison/setup.sh --dl_dir=bitbake_download_dir  --sstate_dir=bitbake_sstate_dir 
    ```

6. Измените расположение репозитория Git Paho. В файле **~/edison-src/out/linux64/poky/meta-intel-iot-middleware/recipes-connectivity/paho-mqtt/paho-mqtt\_3.1.bb** замените URL-адрес `git://git.eclipse.org/gitroot/paho/org.eclipse.paho.mqtt.c.git/` на `git://github.com/eclipse/paho.mqtt.c.git`.

7. Инициализируйте среду сборки с помощью следующих команд:
    
    ```
    cd out/linux64/
    source poky/oe-init-build-env
    ```

8. Создайте пользовательский образ с помощью команды ниже. Первое выполнение этой команды в компьютере с 4 ядрами ЦП может занять до 6 часов. Последующие сборки после добавления собственных настроек будут выполняться значительно быстрее:
    
    ```
    bitbake edison-image
    ```

9. Завершите сборку, выполнив следующие команды:
    
    ```
    cd ~/edison-src/
    ./meta-intel-edison/utils/flash/postBuild.sh ./out/linux64/build/
    ```

Файлы, требуемые для переключения на плату Edison, теперь находятся в папке **~/edison-src/out/linux64/build/toFlash/**.

### Добавление пакета SDK для шлюза в пользовательский образ

В этом разделе описан процесс добавления пакета SDK для шлюза в пользовательский образ, чтобы при запуске плата Edison выполняла функции шлюза IoT. В этом руководстве шлюз содержит модуль, который моделирует два устройства Bluetooth с низким энергопотреблением (BLE), создающие данные телеметрии (значения температуры) для шлюза. Эти данные передаются в центр IoT.

Выполните следующие действия на том же компьютере Ubuntu 14.04, который использовался для сборки образа Edison в предыдущем разделе.

1. Клонируйте пакет SDK для шлюза в домашнюю папку:
    
    ```
    cd ~
    git clone https://github.com/Azure/azure-iot-gateway-sdk.git --recursive
    ```

2. Настройте шлюз для подключения к центру IoT и моделирования двух устройств. В файле **~/azure-iot-gateway-sdk/samples/simulated\_device\_cloud\_upload/src/simulated\_device\_cloud\_upload\_intel\_edison.json** замените заполнители **IoTHubName**, **IoTHubSuffix**, **deviceID** и **deviceKey** значениями, записанными при настройке центра IoT. Используйте созданные ранее устройства **GW-ble1-demo** и **GW-ble2-demo**.

3. Создайте папку для хранения новой инструкции:
    
    ```
    mkdir ~/edison-src/meta-intel-edison/meta-intel-edison-distro/recipes-support/azure-iot-field-gateway-sdk
    ```

4. Скопируйте файл инструкции **azure-iot-field-gateway-sdk.bb** из папки **~/azure-iot-gateway-sdk/samples/simulated\_device\_cloud\_upload/src/** в созданную папку **~/edison-src/meta-intel-edison/meta-intel-edison-distro/recipes-support/azure-iot-field-gateway-sdk/**. Замените в файле два экземпляра `<<userName>>` на имя текущего пользователя.

5. Добавьте в файл **~/edison-src/meta-intel-edison/meta-intel-edison-distro/recipes-core/images/edison-image.bb** запись для новой инструкции. В конце файла добавьте следующую строку:
    
    ```
    IMAGE_INSTALL += "azure-iot-field-gateway-sdk"
    ```

6. Теперь можно проверить изменения, запустив команду **bitbake**, чтобы создать инструкцию:
    
    ```
    cd ~/edison-src/out/linux64/
    source poky/oe-init-build-env
    bitbake azure-iot-field-gateway-sdk
    ```

### Добавление клиента для управления устройствами центра IoT Azure в пользовательский образ

В этом разделе описан процесс добавления клиента для управления устройствами центра IoT в пользовательский образ. В результате обеспечивается возможность управления устройством шлюза Edison в центре IoT. В этом руководстве клиент для управления устройством содержит пример кода, который позволит обновить встроенное ПО на устройстве шлюза Edison.

Выполните следующие действия на том же компьютере Ubuntu 14.04, который использовался для добавления шлюза в образ Edison в предыдущем разделе.

1. Клонируйте ветвь репозитория **dmpreview** пакета SDK центра IoT в домашнюю папку:
    
    ```
    cd ~
    git clone https://github.com/Azure/azure-iot-sdks -b dmpreview --recursive
    ```

2. Создайте следующие папки для хранения новой инструкции:
    
    ```
    mkdir ~/edison-src/meta-intel-edison/meta-intel-edison-distro/recipes-support/iotdm-edison-sample
    mkdir ~/edison-src/meta-intel-edison/meta-intel-edison-distro/recipes-support/iotdm-edison-sample/files
    ```

3. Скопируйте файл **iotdm эдисон sample.bb** из папки **~/azure-iot-sdks/c/iotdm\_client/samples/iotdm\_edison\_sample/bitbake/** в папку **~/edison-src/meta-intel-edison/meta-intel-edison-distro/recipes-support/iotdm-edison-sample**.

4. Отредактируйте файл **~/edison-src/meta-intel-edison/meta-intel-edison-distro/recipes-support/iotdm-edison-sample/iotdm-edison-sample.bb** и замените `-Duse_http:BOOL=OFF` на `-Duse_http:BOOL=ON`.

5. Скопируйте файл **iotdm\_edison\_sample.service** из папки **~/azure-iot-sdks/c/iotdm\_client/samples/iotdm\_edison\_sample/bitbake/** в папку **~/edison-src/meta-intel-edison/meta-intel-edison-distro/recipes-support/iotdm-edison-sample/files**.

6. Добавьте в файл **~/edison-src/meta-intel-edison/meta-intel-edison-distro/recipes-core/images/edison-image.bb** запись для новой инструкции. В конце файла добавьте следующую строку:
    
    ```
    IMAGE_INSTALL += "iotdm-edison-sample"
    ```

7. Так как пакет SDK для шлюза и клиент для управления устройствами совместно используют некоторые библиотеки, необходимо изменить файл **~/edison-src/out/linux64/poky/meta/classes/sstate.bbclass**. В конце этого файла добавьте строки ниже. Обязательно замените `<your user>` на имя текущего пользователя:
    
    ```
    SSTATE_DUPWHITELIST += "/home/<your user>/edison-src/out/linux64/build/tmp/sysroots/edison/usr/lib/libaziotsharedutil.a"
    SSTATE_DUPWHITELIST += "/home/<your user>/edison-src/out/linux64/build/tmp/sysroots/edison/usr/include/azureiot"
    ```

8. Настройте автоматический запуск Wi-Fi на плате Edison, добавив в конце файла **~/edison-src/meta-intel-edison/meta-intel-edison-distro/recipes-connectivity/wpa\_supplicant/wpa-supplicant/wpa\_supplicant.conf-sane** приведенные ниже строки. Обязательно замените `<your wifi ssid>` и `<your wifi password>` на правильные значения для своей сети Wi-Fi.
    
    ```
    network={
      ssid="<your wifi ssid>"
      key_mgmt=WPA-PSK
      pairwise=CCMP TKIP
      group=CCMP TKIP WEP104 WEP40
      eap=TTLS PEAP TLS
      psk="<your wifi password>"
    }
    ```

9. Теперь можно создать образ для платы Edison, содержащий пакет SDK для шлюза и клиент для управления устройствами. Команда **bitbake** будет выполняться гораздо быстрее, чем раньше, так как теперь требуется только создать инструкцию и добавить ее в образ.
    
    ```
    cd ~/edison-src/out/linux64/
    source poky/oe-init-build-env
    bitbake edison-image
    ```

10. Завершите сборку, выполнив следующие команды:
  
    ```
    cd ~/edison-src/
    ./meta-intel-edison/utils/flash/postBuild.sh ./out/linux64/build/
    ```

Файлы, требуемые для переключения на плату Edison, теперь находятся в папке **~/edison-src/out/linux64/build/toFlash/**.

### Переключение с платы Intel Edison на пользовательский образ

Теперь можно переключиться с платы Edison на пользовательский образ, содержащий клиент для управления устройствами и программное обеспечение шлюза центра IoT.

Скопируйте файлы из папки **toFlash** на компьютере Ubuntu, использованном для создания пользовательского образа, на компьютер, который подключается к плате Edison с помощью USB-кабеля.

При использовании компьютера Windows для подключения к плате Edison через USB-кабель следует запустить сценарий **flashall.bat**, чтобы переключиться с платы Edison. При использовании компьютера Linux для подключения к плате Edison через USB-кабель следует запустить сценарий **flashall.bat**, чтобы переключиться с платы Edison.

По завершении переключения подключитесь к плате Edison с главного компьютера, используя [подключение через последовательный порт][lnk-serial-connection], и войдите в качестве **привилегированного пользователя**. Убедитесь, что плата Edison подключена к сети Wi-Fi.

### Запуск примера

Клиент для управления устройствами необходимо настроить на плате Edison для подключения к центру IoT в качестве устройства **GW-device**. В текстовом редакторе (например, **vi** или **nano**) создайте файл с именем **.cs** в папке /home/root на плате Edison. Этот файл должен содержать только строку подключения к устройству **GW-device**. Если вы не записали эту строку подключения ранее, ее можно получить из реестра устройств центра IoT с помощью [обозревателя устройств или инструмента iothub-explorer][lnk-explorer-tools].

Создав **CS**-файл, перезагрузите плату Edison, используя следующую команду:

```
reboot -h now
```

При этом убедитесь, что при запуске служба для управления устройствами и служба шлюза будут иметь состояние **ОК**:

```
[  OK  ] Started Daemon to receive the wpa_supplicant event.
         Starting PulseAudio Sound System...
[  OK  ] Started WPA supplicant service.
[  OK  ] Started Azure IoT DM as a service..
[  OK  ] Started Azure Iot Gateway as a service..
         Stopping Daemon to receive the wpa_supplicant event...
[  OK  ] Stopped Daemon to receive the wpa_supplicant event.

```

Чтобы устранить неполадки службы шлюза IoT, просмотрите записи в файле **/deviceCloudUploadGatewaylog.log** платы Edison. Для просмотра всех выходных данных службы также можно использовать следующую команду:

```
systemctl status simulated_device_cloud_upload.service
```

Чтобы устранить неполадки службы управления устройствами IoT, используйте следующую команду для просмотра выходных данных:

```
systemctl status iotdm_edison_sample.service
```

### Запуск обновления встроенного ПО

При обновлении встроенного ПО на плате Edison, запрошенном службой управления устройствами IoT, обычно скачивается ZIP-файл со встроенным ПО по указанному URL-адресу. Чтобы упростить этот учебник, скопируйте ZIP-файл вручную на плату Edison и используйте при запросе на обновление URL-адрес **file://**, а не **http://**.

Опять же, для упрощения работы с руководством при обновлении встроенного ПО повторно используется тот же образ встроенного ПО, а не совершенно новый образ. Вы сможете увидеть использование этого образа на плате Edison.

Создайте ZIP-файл с именем **edison.zip**, содержащий все файлы и вложенные папки из папки **toFlash** на компьютере Ubuntu, использованном для создания пользовательского образа. Убедитесь, что файлы из папки **toFlash** находятся в корне ZIP-файла. Скопируйте файл **edison.zip** в папку /home/root на плате Edison с помощью такого средства, как SCP (или PSCP при использовании Putty).

Чтобы отправить задание обновления встроенного ПО и отслеживать ход его выполнения, используйте [пример пользовательского интерфейса управления устройствами][lnk-dm-sample-ui] Node.js. Его можно запустить на компьютере с ОС Windows или Linux. Для этого потребуется [Node.js][lnk-nodejs] 6.1.0 или более поздней версии. Чтобы получить, создать и выполнить пример пользовательского интерфейса управления устройствами на настольном компьютере, сделайте следующее:

1. Откройте **окно командой строки**.

2. Убедитесь, что установлена платформа Node.js 6.1.0 или более поздней версии, введя `node --version`.

3. Клонируйте репозиторий GitHub пользовательского интерфейса управления устройствами центра IoT Azure, выполнив следующую команду:

    ```
    git clone https://github.com/Azure/azure-iot-device-management.git
    ```
    
4. Чтобы получить зависимые пакеты, в корневой папке, куда клонирована копия репозитория пользовательского интерфейса управления устройствами центра IoT Azure, выполните следующую команду:

    ```
    npm install
    ```

5. После завершения команды npm install выполните следующую команду, чтобы создать код.

    ```
    npm run build
    ```

6. Используйте текстовый редактор, чтобы открыть файл user-config.json в корне клонированной папки. Замените текст &lt;YOUR CONNECTION STRING HERE&gt; на строку подключения центра IoT. Ее можно найти на [портале][lnk-azure-portal] Azure.

7. Чтобы запустить пользовательское приложение управления устройствами, в командной строке выполните следующую команду:

    ```
    npm run start
    ```

8. Когда в командной строке появится сообщение "Services have started" (Службы запущены), откройте веб-браузер и перейдите к приложению управления устройствами по URL-адресу <http://127.0.0.1:3003>, чтобы просмотреть виртуальные устройства.

    ![Пользовательский интерфейс управления устройствами][img-dm-ui]

9. Выберите устройство **GW-device**, в раскрывающемся списке **Device Jobs** (Задания устройства) выберите пункт **Обновление микропрограммы** и щелкните **Запустить**.

10. В поле **Package URI** (URI пакета) введите **file:///home/root/edison.zip**, чтобы использовать файл образа, скопированный ранее на плату Edison. Щелкните **Отправить**, нажмите кнопку **Да** и выберите ссылку **Журнал заданий**, чтобы увидеть выполнение новых родительских и дочерних заданий:

    ![Ссылка "Журнал заданий"][img-history-link]

11. Через несколько минут в последовательном терминале, подключенном к плате Edison, отобразится перезагрузка платы Edison и внесение изменений во встроенное ПО:

    ```
    reading ota_update.scr
    14747 bytes read in 17 ms (846.7 KiB/s)
    ## Executing script at 00100000
    === OTA update script ===
    Validating Ota package
    part find mmc 0 label:update u_part_num;
    ota drive is mmc 0:9

    Validating edison_ifwi-dbg-00-dfu.bin hash for boot0 and boot1 partitions

    fatload mmc 0:9 0x6400000 edison_ifwi-dbg-00-dfu.bin;
    reading edison_ifwi-dbg-00-dfu.bin
    ...
    ```

12. После завершения перезагрузки платы Edison обновите страницу в примере пользовательского интерфейса управления устройствами. Состояние двух заданий обновления встроенного ПО изменится на **Выполнено**:

    ![Состояние задания "Выполнено"][img-job-status]

Вы завершили работу с руководством, в котором показано, как использовать программное обеспечение для шлюза центра IoT и клиент для управления устройствами на плате Intel Edison. В рамках этого руководства вы сделали следующее:

- Создали пользовательский образ Intel Edison, который включает в себя клиент для управления устройствами и шлюз для программного обеспечения центра IoT, который запускается при каждом запуске платы Edison.
- Настроили плату Edison и клиент для управления устройствами для подключения к центру IoT.
- Запустили задание управления устройствами из примера пользовательского интерфейса, которое перезагружает плату Edison и применяет новый образ встроенного ПО.

## Дальнейшие действия

Дополнительные сведения об управлении устройствами с помощью центра IoT, а также пример пользовательского интерфейса см. в статье [Общие сведения об управлении устройствами с помощью центра IoT Azure (предварительная версия)][lnk-device-management].

Если вы хотите подробнее изучить возможности пакета SDK для шлюза и поэкспериментировать с примерами кода, см. статью [Пакет SDK для шлюза IoT Azure][lnk-gateway-sdk].

Для дальнейшего изучения возможностей центра IoT см. следующие статьи:

- [Разработка решения][lnk-design]
- [Руководство разработчика по центру Azure IoT (IoT — Интернет вещей)][lnk-devguide]
- [Обзор управления устройствами центра IoT с помощью примера пользовательского интерфейса][lnk-dmui]
- [Управление центрами IoT через портал Azure][lnk-portal]



<!-- Links and images -->

[img-dm-ui]: media/iot-hub-gateway-sdk-device-management/image1.png
[img-history-link]: media/iot-hub-gateway-sdk-device-management/image2.png
[img-job-status]: media/iot-hub-gateway-sdk-device-management/image3.png
[img-architecture]: media/iot-hub-gateway-sdk-device-management/architecture.png

[lnk-iot-hub]: iot-hub-what-is-iot-hub.md
[lnk-device-management]: iot-hub-device-management-overview.md
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/
[lnk-setup-win64]: https://software.intel.com/get-started-edison-windows
[lnk-setup-win32]: https://software.intel.com/get-started-edison-windows-32
[lnk-setup-osx]: https://software.intel.com/get-started-edison-osx
[lnk-setup-linux]: https://software.intel.com/get-started-edison-linux
[lnk-serial-connection]: https://software.intel.com/setting-up-serial-terminal-intel-edison-board
[lnk-inteledison-bsp]: http://www.intel.com/content/dam/support/us/en/documents/edison/sb/edisonbsp_ug_331188007.pdf
[lnk-hackgnar]: http://www.hackgnar.com/2016/01/manually-building-yocto-images-for.html
[lnk-shawnhymel]: http://shawnhymel.com/724/creating-a-custom-linux-kernel-for-the-edison-yocto-2-1/
[lnk-create-hub]: iot-hub-manage-through-portal.md
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-explorer-tools]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/manage_iot_hub.md
[lnk-nodejs]: https://nodejs.org/
[lnk-azure-portal]: https://portal.azure.com
[lnk-dm-sample-ui]: iot-hub-device-management-ui-sample.md
[lnk-gateway-physical]: iot-hub-gateway-sdk-physical-device.md
[lnk-gateway-scenario]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-dm-jobs]: iot-hub-device-management-device-jobs.md

[lnk-design]: iot-hub-guidance.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-dmui]: iot-hub-device-management-ui-sample.md
[lnk-portal]: iot-hub-manage-through-portal.md

<!---HONumber=AcomDC_0914_2016-->