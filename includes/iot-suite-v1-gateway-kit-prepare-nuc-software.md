## <a name="build-iot-edge"></a>Создание Edge Интернета вещей

В этом руководстве используются пользовательские модули Edge Интернета вещей для связи с предварительно настроенным решением для удаленного мониторинга. Поэтому модули Edge Интернета вещей нужно создать на основе исходного пользовательского кода. В следующих разделах описывается, как установить Edge Интернета вещей и создать пользовательский модуль Edge Интернета вещей.

### <a name="install-iot-edge"></a>Установка Edge Интернета вещей

Далее представлены сведения о том, как установить предварительно скомпилированное программное обеспечение Edge Интернета вещей на Intel NUC:

1. Настройте необходимые репозитории Smart Package, выполнив в Intel NUC следующие команды:

    ```bash
    smart channel --add IoT_Cloud type=rpm-md name="IoT_Cloud" baseurl=http://iotdk.intel.com/repos/iot-cloud/wrlinux7/rcpl13/ -y
    smart channel --add WR_Repo type=rpm-md baseurl=https://distro.windriver.com/release/idp-3-xt/public_feeds/WR-IDP-3-XT-Intel-Baytrail-public-repo/RCPL13/corei7_64/
    ```

    Введите `y`, получив от команды запрос **Include this channel?** (Включить этот канал?).

1. Обновите Smart Package Manager, выполнив следующую команду:

    ```bash
    smart update
    ```

1. Установите пакет Edge Интернета вещей Azure, выполнив следующую команду:

    ```bash
    smart config --set rpm-check-signatures=false
    smart install packagegroup-cloud-azure -y
    ```

1. Проверьте установку, запустив пример Hello World. Этот пример записывает сообщение Hello World в файл log.txt каждые пять секунд. Запустите пример Hello World, выполнив следующие команды:

    ```bash
    cd /usr/share/azureiotgatewaysdk/samples/hello_world/
    ./hello_world hello_world.json
    ```

    При остановке примера игнорируйте все сообщения **Недопустимый аргумент**.

    Вы можете просмотреть содержимое файла журнала, выполнив следующую команду:

    ```bash
    cat log.txt | more
    ```

### <a name="troubleshooting"></a>Устранение неполадок

Перезагрузите Intel NUC, если отображается сообщение об ошибке No package provides util-linux-dev (Нет пакета, предоставляющего util-linux-dev).
