## <a name="view-the-telemetry"></a>Просмотр телеметрии

Теперь устройство Raspberry Pi может отправлять данные телеметрии в удаленное решение мониторинга. Эти данные можно просмотреть на панели мониторинга решения. Вы также можете использовать эту панель, чтобы отправлять сообщения в Raspberry Pi.

- Перейдите к панели мониторинга решения.
- Выберите устройство в раскрывающемся списке **Устройство для просмотра**.
- Данные телеметрии из Raspberry Pi отобразятся на панели мониторинга.

![Отображение данных телеметрии из Raspberry Pi][img-telemetry-display]

## <a name="act-on-the-device"></a>Выполнение действий на устройстве

Используйте панель мониторинга решения для вызова методов на устройстве Raspberry Pi. Когда устройство Raspberry Pi подключается к решению для удаленного мониторинга, оно отправляет сведения о поддерживаемых методах.

- На панели мониторинга решения щелкните **Устройства**, чтобы перейти на страницу **устройств**. Выберите Raspberry Pi в **списке устройств**. Затем выберите **Методы**:

    ![Список устройств на панели мониторинга][img-list-devices]

- На странице **Вызвать метод** в раскрывающемся списке **Метод** выберите **LightBlink**.

- Щелкните **InvokeMethod**. Симулятор выводит сообщение на консоли Raspberry Pi. Приложение на устройстве Raspberry Pi отправляет подтверждение обратно на панель мониторинга решения.

    ![Просмотр журнала методов][img-method-history]

- Вы можете включить и отключить индикатор, используя метод **ChangeLightStatus** с параметром **LightStatusValue** со значением **1** (для включения) или **0** (для выключения).

> [!WARNING]
> Если не завершить выполнение решения удаленного мониторинга в учетной записи Azure, вам будет выставлен счет. Дополнительные сведения о сокращении затрат во время выполнения решения для удаленного мониторинга см. в статье [Configuring Azure IoT Suite preconfigured solutions for demo purposes][lnk-demo-config] (Настройка предварительно настроенных решений Azure IoT Suite для демонстрационных целей). Удалите предварительно настроенное решение из учетной записи Azure, когда завершите его использование.


[img-telemetry-display]: media/iot-suite-raspberry-pi-kit-view-telemetry-simulator/telemetry.png
[img-list-devices]: media/iot-suite-raspberry-pi-kit-view-telemetry-simulator/listdevices.png
[img-method-history]: media/iot-suite-raspberry-pi-kit-view-telemetry-simulator/methodhistory.png

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md