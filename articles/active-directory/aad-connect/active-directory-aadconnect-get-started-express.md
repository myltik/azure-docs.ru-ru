<properties
	pageTitle="Azure AD Connect: начало работы с использованием стандартных параметров | Microsoft Azure"
	description="Узнайте, как скачать, установить и запустить мастер установки Azure AD Connect."
	services="active-directory"
	documentationCenter=""
	authors="andkjell"
	manager="femila"
	editor="curtand"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="09/13/2016"
	ms.author="billmath;andkjell"/>

# Приступая к работе с Azure AD Connect с использованием стандартных параметров
**Стандартные параметры** Azure AD Connect применяются в том случае, если вы используете для проверки подлинности топологию с одним лесом и [синхронизацию паролей](../active-directory-aadconnectsync-implement-password-synchronization.md). **Стандартные параметры** заданы по умолчанию и используются в большинстве часто развертываемых сценариев. Выполнив всего несколько простых действий, можно расширить локальный каталог в облако.

Прежде чем приступать к установке Azure AD Connect, [скачайте Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771) и выполните предварительные шаги, перечисленные в разделе [Azure AD Connect: оборудование и необходимые условия](../active-directory-aadconnect-prerequisites.md).

Если стандартные параметры не соответствуют топологии, сведения о других сценариях см. в разделе [Дополнительная документация](#related-documentation).

## Экспресс-установка Azure AD Connect
Чтобы ознакомиться с этими действиями на практике, см. раздел [видео](#videos).

1. Войдите с правами локального администратора на сервер, на котором требуется установить Azure AD Connect. Это должен быть сервер, который нужно сделать сервером синхронизации.
2. Перейдите к файлу **AzureADConnect.msi** и дважды щелкните его.
3. На экране приветствия установите флажок, подтверждающий ваше согласие с условиями лицензионного соглашения, и нажмите кнопку **Продолжить**.
4. На экране «Стандартные параметры» щелкните **Использовать стандартные параметры**. ![Приветствие мастера установки Azure AD Connect.](./media/active-directory-aadconnect-get-started-express/express.png)
5. На экране "Подключение к Azure AD" введите имя пользователя и пароль глобального администратора Azure AD. Нажмите кнопку **Далее**. ![Подключение к Azure AD](./media/active-directory-aadconnect-get-started-express/connectaad.png) Если вы получаете сообщение об ошибке и испытываете проблемы с подключением, см. статью [Устранение неполадок подключения в Azure AD Connect](../active-directory-aadconnect-troubleshoot-connectivity.md).
6. На экране "Подключение к AD DS" введите имя пользователя и пароль учетной записи администратора предприятия. Вы можете указать имя домена в формате NetBios, либо ввести полное доменное имя, т. е. FABRIKAM\\administrator или fabrikam.com\\administrator. Нажмите кнопку **Далее**. ![Подключение к AD DS](./media/active-directory-aadconnect-get-started-express/connectad.png)
7. Страница [**Настройка входа в Azure AD**](../active-directory-aadconnect-user-signin.md#azure-ad-sign-in-configuration) будет отображаться, только если вы не завершили [проверку доменов](../active-directory-add-domain.md) при выполнении [предварительных требований](../active-directory-aadconnect-prerequisites.md). ![Непроверенные домены](./media/active-directory-aadconnect-get-started-express/unverifieddomain.png) Если появилась эта страница, просмотрите каждый домен с пометкой **Не добавлено** или **Не проверено**. Убедитесь, что используемые домены прошли проверку в Azure AD. Проверив домены, щелкните значок обновления.
8. На экране "Все готово к настройке" нажмите кнопку **Установить**.
	- При необходимости на странице "Готово к настройке" можно снять флажок **Start the synchronization process as soon as configuration completes** (Запустить синхронизацию сразу после завершения настройки). Снимите этот флажок, если собираетесь настроить дополнительные параметры, например [фильтрацию](../active-directory-aadconnectsync-configure-filtering.md). Если отменить выбор этого параметра, мастер настроит синхронизацию, но оставит планировщик отключенным. Он не запустится, пока вы не включите его вручную, [повторно запустив мастер установки](../active-directory-aadconnectsync-installation-wizard.md).
	- Если Exchange находится в локальной службе Active Directory, вы также можете включить функцию [**гибридного развертывания Exchange**](https://technet.microsoft.com/library/jj200581.aspx). Этот флажок нужно установить, если почтовые ящики Exchange будут одновременно использоваться в облаке и локальной среде. ![Все готово к настройке Azure AD Connect](./media/active-directory-aadconnect-get-started-express/readytoconfigure.png)
9. По завершении установки щелкните **Выход**.
10. После завершения установки выполните выход и снова войдите, прежде чем начинать использовать диспетчер службы синхронизации или редактор правил синхронизации.

## Видеоролики

Рекомендуем посмотреть видео по экспресс-установке:

>[AZURE.VIDEO azure-active-directory-connect-express-settings]

## Дальнейшие действия
После установки Azure AD Connect можно [проверить установку и назначить лицензии](../active-directory-aadconnect-whats-next.md).

Дополнительные сведения об этих функциях, которые были включены в процессе установки, см. в следующих статьях: [Azure AD Connect: автоматическое обновление](../active-directory-aadconnect-feature-automatic-upgrade.md), [Синхронизация Azure AD Connect: предотвращение случайного удаления](../active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) и [Использование Azure AD Connect Health для синхронизации](../active-directory-aadconnect-health-sync.md).

Дополнительные сведения см. в статье [Синхронизация Azure AD Connect: планировщик](../active-directory-aadconnectsync-feature-scheduler.md).

Узнайте больше об [интеграции локальных удостоверений с Azure Active Directory](../active-directory-aadconnect.md).

## Дополнительная документация

Раздел |  
--------- | ---------
Обзор Azure AD Connect | [Интеграция локальных удостоверений с Azure Active Directory](../active-directory-aadconnect.md)
Установка с помощью настроенных параметров | [Выборочная установка Azure AD Connect](active-directory-aadconnect-get-started-custom.md)
Обновление из DirSync | [Обновление из средства синхронизации Azure AD (DirSync)](active-directory-aadconnect-dirsync-upgrade-get-started.md)
Учетные записи, используемые для установки | [Дополнительная информация об учетных записях и разрешениях Azure AD Connect](active-directory-aadconnect-accounts-permissions.md)

<!---HONumber=AcomDC_1005_2016-->