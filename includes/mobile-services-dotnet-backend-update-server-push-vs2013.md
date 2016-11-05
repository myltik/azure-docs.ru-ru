Ниже приведены инструкции по созданию настраиваемого объекта [ApiController](http://go.microsoft.com/fwlink/p/?LinkId=512673), который отправляет push-уведомления в приложение. Аналогичный код можно реализовать в [TableController](http://msdn.microsoft.com/library/azure/dn643359.aspx) или в другом месте в серверных службах.

1. В обозревателе решений Visual Studio щелкните правой кнопкой мыши папку Controllers для проекта мобильной службы, выберите пункт **Добавить**, а затем щелкните **New Scaffolded Item** (Новый элемент скаффолдинга).
   
    Откроется диалоговое окно добавления элемента скаффолдинга.
2. Разверните **Мобильные службы Azure** и выберите элемент **Настраиваемый контроллер мобильных служб Azure**, нажмите кнопку **Добавить**, введите в поле **Имя контроллера** значение `NotifyAllUsersController`, а затем еще раз нажмите кнопку **Добавить**.
   
    ![Диалоговое окно добавления элемента скаффолдинга веб-интерфейса API](./media/mobile-services-dotnet-backend-update-server-push-vs2013/add-custom-api-controller.png)
   
    При этом создается пустой класс контроллера с именем **NotifyAllUsersController**.
3. В новом файле проекта NotifyAllUsersController.cs добавьте следующие операторы **using**:
   
        using Newtonsoft.Json.Linq;
        using System.Threading.Tasks;
4. Добавьте следующий код, реализующий метод POST в контроллере:
   
        public async Task<bool> Post(JObject data)
        {
            try
            {
                // Define the XML paylod for a WNS native toast notification 
                // that contains the value supplied in the POST request.
                string wnsToast = 
                    string.Format("<?xml version="1.0" encoding="utf-8"?>" +
                    "<toast><visual><binding template="ToastText01">" + 
                    "<text id="1">{0}</text></binding></visual></toast>", 
                    data.GetValue("toast").Value<string>());
   
                // Define the WNS native toast with the payload string.
                WindowsPushMessage message = new WindowsPushMessage();
                message.XmlPayload = wnsToast;
   
                // Send the toast notification.
                await Services.Push.SendAsync(message);
                return true;
            }
            catch (Exception e)
            {
                Services.Log.Error(e.ToString());
            }
            return false;
        }
   
   > [!NOTE]
   > Этот метод POST может быть вызван любым клиентом, имеющим ключ приложения, который не является безопасным. Чтобы защитить конечную точку, примените атрибут `[AuthorizeLevel(AuthorizationLevel.User)]` к методу или классу для обязательной аутентификации.
   > 
   > 

<!---HONumber=Oct15_HO3-->