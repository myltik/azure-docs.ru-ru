<properties 
	pageTitle="Учебник. Настройка защиты между локальными сайтами VMWare" 
	description="InMage в Azure Site Recovery обрабатывает репликацию, отработку отказа и восстановление между локальными узлами VMWare." 
	services="site-recovery" 
	documentationCenter="" 
	authors="raynew" 
	manager="jwhit" 
	editor="tysonn"/>

<tags 
	ms.service="site-recovery" 
	ms.workload="backup-recovery" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/18/2015" 
	ms.author="raynew"/>

# Учебник. Настройка защиты между локальными сайтами VMWare


<h2><a id="overview" name="overview" href="#overview"></a>Обзор</h2>

<p>InMage в Azure Site Recovery обеспечивает репликацию между локальными узлами VMWare в режиме реального времени. InMage включается в подписки на службу Azure Site Recovery.</p>




<h2><a id="before" name="before" href="#before"></a>Предварительные требования</h2>
<div class="dev-callout"> 

<UL>
<LI><b>Учетная запись Azure</b>. Вам потребуется учетная запись Azure. Если у вас ее нет, см. раздел <a href="http://aka.ms/try-azure">Бесплатная пробная версия Azure</a>. Информацию по ценам на подписки см. в разделе <a href="http://go.microsoft.com/fwlink/?LinkId=378268">Цены на диспетчер Azure Site Recovery</a>.</LI>
</UL>


<h2><a id="tutorial" name="tutorial" href="#tutorial"></a>Пошаговые указания</h2>
<a name="vault"></a> <h3>Шаг 1. Создание хранилища и скачивание InMage</h3>

1. Выполните вход на [портал управления](https://manage.windowsazure.com).


2. Разверните элементы <b>Службы данных</b>, <b>Службы восстановления</b> и щелкните <b>Хранилище Site Recovery</b>.


3. Щелкните <b>Создать</b>, а затем - <b>Быстрое создание</b>.
	
4. В поле <b>Имя</b> введите понятное имя для идентификации хранилища.

5. В поле <b>Область</b> выберите географический регион для хранилища архивации. Чтобы проверить поддерживаемые регионы, см. географическую доступность в разделе <a href="http://go.microsoft.com/fwlink/?LinkId=389880">Информация о ценах на Azure Site Recovery</a>.

6. Щелкните элемент <b>Создать хранилище</b>. 

	![New Vault](./media/hyper-v-recovery-manager-configure-vault/SRSAN_HvVault.png)

<P>Проверьте строку состояния, чтобы убедиться, что хранилище успешно создано. Это хранилище будет указано с состоянием <b>Активно</b> на главной странице служб восстановления.</P>

<a name="upload"></a> <h3>Шаг 2. Настройка хранилища</h3>


1. На странице <b>Службы восстановления</b> щелкните хранилище, чтобы открыть страницу быстрого запуска. Страницу быстрого запуска можно открыть и в любой другой момент, щелкнув этот значок.

	![Quick Start Icon](./media/hyper-v-recovery-manager-configure-vault/SRSAN_QuickStartIcon.png)

2. В раскрывающемся списке выберите **Между двумя локальными узлами VMWare**.
3. Скачайте InMage Scout.
	
	![VMWare](./media/hyper-v-recovery-manager-configure-vault/SRVMWare_SelectScenario.png)

4. Настройте репликацию между двумя сайтами VMWare с использованием документации InMage, которая автоматически скачивается с продуктом.


<h2><a id="next" name="next" href="#next"></a>Дальнейшие действия</h2>
<UL>

<LI>При наличии вопросов обращайтесь на <a href="http://go.microsoft.com/fwlink/?LinkId=313628">форум по службам восстановления Azure</a>.</LI> 
</UL>

<!--HONumber=49-->