<properties
   pageTitle="Недостаточный размер папки TEMP по умолчанию для роли | Microsoft Azure"
   description="Роль облачной службы располагает ограниченным объемом места в папке TEMP. Эта статья содержит советы о том, как предотвратить нехватку места."
   services="cloud-services"
   documentationCenter=""
   authors="dalechen"
   manager="felixwu"
   editor=""
   tags="top-support-issue"/>
<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="01/20/2016"
   ms.author="daleche" />

# Недостаточный размер папки TEMP по умолчанию для рабочей роли или веб-роли облачной службы

Максимальный размер временного каталога по умолчанию рабочей роли или веб-роли облачной службы составляет 100 МБ, чего может оказаться недостаточно в определенный момент. В этой статье описано, как можно предотвратить нехватку места для временного каталога.

>[AZURE.NOTE] Это относится только к пакетам Azure для SDK версии с 1.0 до 1.4, использующим веб-роли и рабочие роли.

## Обращение в службу поддержки клиентов Azure

Если на любом этапе изучения этой статьи вам потребуется дополнительная помощь, вы можете обратиться к экспертам по Azure на [форумах MSDN Azure и Stack Overflow](https://azure.microsoft.com/support/forums/).

Кроме того, можно зарегистрировать обращение в службу поддержки Azure. Перейдите на [сайт службы поддержки Azure](https://azure.microsoft.com/support/options/) и щелкните **Поддержка**. Дополнительные сведения об использовании службы поддержки Azure см. в статье [Часто задаваемые вопросы о поддержке Microsoft Azure](https://azure.microsoft.com/support/faq/).


## Почему мне не хватает свободного места?
Стандартные переменные среды Windows TEMP и TMP доступны коду, выполняемому в приложении. Переменные TEMP и TMP указывают на один каталог с максимальным размером в 100 МБ. Данные, хранящиеся в этом каталоге, не сохраняются на протяжении жизненного цикла размещенной службы; если экземпляры роли в размещенной службе перезапускаются, этот каталог очищается.

## Предложение по устранению проблемы
Примените один из следующих альтернативных способов.

- Настройте локальный ресурс хранилища и обращайтесь напрямую к нему, не используя **TEMP** или **TMP**. Для доступа к локальному ресурсу хранилища из кода, запущенного в приложении, вызовите метод [RoleEnvironment.GetLocalResource](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.getlocalresource.aspx). Дополнительные сведения о настройке локальных ресурсов хранилища см. в статье [Настройка локальных ресурсов хранилища](cloud-services-configure-local-storage-resources.md).

- Настройте локальный ресурс хранилища и задайте каталоги TEMP и TMP, чтобы указать путь к этому локальному ресурсу хранилища. Это изменение следует выполнить внутри метода [RoleEntryPoint.OnStart](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx).

В следующем примере кода показано, как изменить целевые каталоги TEMP и TMP из метода OnStart:


```csharp
using System;
using Microsoft.WindowsAzure.ServiceRuntime;

namespace WorkerRole1
{
    public class WorkerRole : RoleEntryPoint
    {
        public override bool OnStart()
        {
            // The local resource declaration must have been added to the
            // service definition file for the role named WorkerRole1:
            //
            // <LocalResources>
            //    <LocalStorage name="CustomTempLocalStore"
            //                  cleanOnRoleRecycle="false"
            //                  sizeInMB="1024" />
            // </LocalResources>

            string customTempLocalResourcePath =
            RoleEnvironment.GetLocalResource("CustomTempLocalStore").RootPath;
            Environment.SetEnvironmentVariable("TMP", customTempLocalResourcePath);
            Environment.SetEnvironmentVariable("TEMP", customTempLocalResourcePath);

            // The rest of your startup code goes here…

            return base.OnStart();
        }
    }
}
```

## Дальнейшие действия

См. другой блог, в котором описывается, [как увеличить размер временной папки ASP.NET веб-ролей Azure](http://blogs.msdn.com/b/kwill/archive/2011/07/18/how-to-increase-the-size-of-the-windows-azure-web-role-asp-net-temporary-folder.aspx).

Просмотрите дополнительные [статьи об устранении неполадок](..\?tag=top-support-issue&service=cloud-services) в облачных службах.

Чтобы узнать, как устранять неполадки ролей облачной службы с помощью данных диагностики компьютеров Azure PaaS, обратитесь [к серии статей в блоге Кевина Уильямсона](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

<!---HONumber=AcomDC_0128_2016-->