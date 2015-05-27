
1. Обязательно остановите мобильную службу, если она запущена в IIS Express. Щелкните правой кнопкой мыши значок IIS Express на панели задач и выберите пункт **Остановить** для мобильной службы.

    ![](./media/mobile-services-how-to-configure-iis-express/iis-express-tray-stop-site.png)


2. В окне командной строки выполните команду **ipconfig**, чтобы найти допустимый локальный IP-адрес для рабочей станции.

    ![](./media/mobile-services-how-to-configure-iis-express/ipconfig.png)


3. В Visual Studio откройте файл applicationhost.config для IIS Express. Этот файл расположен в следующем подкаталоге каталога профиля пользователя.

        C:\Users<your profile name>\Documents\IISExpress\config\applicationhost.config

4. Настройте IIS Express, чтобы разрешить запросы удаленного подключения к службе. Для этого в файле applicationhost.config найдите для своей мобильной службы элемент site и добавьте новый элемент `binding` для порта, используя указанный выше IP-адрес. После этого сохраните файл applicationhost.config.

    Обновленный элемент site должен выглядеть следующим образом:

        <site name="todolist_Service(1)" id="2">
            <application path="/" applicationPool="Clr4IntegratedAppPool">
                <virtualDirectory path="/" physicalPath="C:\Archive\GetStartedDataWP8\C#\todolist_Service" />
            </application>
            <bindings>
                <binding protocol="http" bindingInformation="*:58203:localhost" />
                <binding protocol="http" bindingInformation="*:58203:192.168.137.72" />
            </bindings>
        </site>

5. Откройте консоль брандмауэра Windows и создайте новое правило для порта, чтобы разрешить подключения к нему. Дополнительные сведения о создании нового правила для порта в брандмауэре Windows см. в разделе [Добавление нового правила для порта в брандмауэре Windows].

    >[AZURE.NOTE]Если тестовый компьютер присоединен к домену, исключения брандмауэра могут регулироваться политикой домена. В этом случае необходимо связаться со своим администратором домена, чтобы получить исключение для порта на вашем компьютере.

    Теперь все должно быть готово для проверки размещения мобильной службы в IIS Express.

    >[AZURE.NOTE]После завершения локального тестирования службы вам следует удалить созданное правило брандмауэра Windows.


<!-- URLs. -->
[Добавление нового правила для порта в брандмауэре Windows]: http://go.microsoft.com/fwlink/?LinkId=392240

<!--HONumber=54-->