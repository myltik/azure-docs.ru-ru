---
title: Настройка пользовательского интерфейса решения для удаленного мониторинга в Azure | Документация Майкрософт
description: Статья содержит сведения о способе доступа к исходному коду пользовательского интерфейса акселератора решений для удаленного мониторинга, а также о некоторых настройках.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/17/2018
ms.topic: conceptual
ms.openlocfilehash: 6e791051fb82197a770bff05f636159c638e3b9a
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34627857"
---
# <a name="customize-the-remote-monitoring-solution-accelerator"></a>Настройка акселератора решений для удаленного мониторинга

Статья содержит сведения о способе доступа к исходному коду и настройке пользовательского интерфейса акселератора решений для удаленного мониторинга. Содержание статьи:

## <a name="prepare-a-local-development-environment-for-the-ui"></a>Подготовка локальной среды разработки для пользовательского интерфейса

Код пользовательского интерфейса акселератора решений для удаленного мониторинга реализуется с помощью платформы React.js. Исходный код можно найти в репозитории GitHub [azure-iot-pcs-remote-monitoring-webui](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui).

Чтобы внести изменения в пользовательский интерфейс, его копию можно запустить локально. Локальная копия подключается к развернутому экземпляру решения для выполнения действий, например для извлечения данных телеметрии.

Ниже описан процесс настройки локальной среды для развертывания пользовательского интерфейса.

1. Разверните **базовый** экземпляр акселератора решений с помощью CLI **PCS**. Запишите имя развертывания и учетные данные, указанные для виртуальной машины. Дополнительные сведения см. в статье [Развертывание предварительно настроенного решения для удаленного мониторинга с помощью интерфейса командной строки](iot-accelerators-remote-monitoring-deploy-cli.md).

1. С помощью портала Azure или [командной строки Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) включите доступ по протоколу SSH к виртуальной машине, на которой размещаются микрослужбы в вашем решении. Например: 

    ```sh
    az network nsg rule update --name SSH --nsg-name {your solution name}-nsg --resource-group {your solution name} --access Allow
    ```

1. Используя портал Azure или [командную строку Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest), найдите имя и общедоступный IP-адрес виртуальной машины. Например: 

    ```sh
    az resource list --resource-group {your solution name} -o table
    az vm list-ip-addresses --name {your vm name from previous command} --resource-group {your solution name} -o table
    ```

1. Подключитесь к виртуальной машине по протоколу SSH, используя IP-адрес из предыдущего шага и учетные данные, указанные при запуске **PCS** для развертывания решения.

1. Чтобы разрешить локальному пользовательскому интерфейсу установить подключение, выполните следующие команды в оболочке Bash на виртуальной машине.

    ```sh
    cd /app
    sudo ./start.sh --unsafe
    ```

1. После завершения команды и запуска веб-сайта можно отключиться от виртуальной машины.

1. В локальной копии репозитория [azure-iot-pcs-remote-monitoring-webui](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) измените **.ENV**-файл, добавив URL-адрес развернутого решения.

    ```config
    NODE_PATH = src/
    REACT_APP_BASE_SERVICE_URL=https://{your solution name}.azurewebsites.net/
    ```

1. В командной строке локальной копии папки `azure-iot-pcs-remote-monitoring-webui` выполните следующие команды, чтобы установить необходимые библиотеки и запустить пользовательский интерфейс локально.

    ```cmd/sh
    npm install
    npm start
    ```

1. Предыдущая команда запускает пользовательский интерфейс локально по адресу http://localhost:3000/dashboard. Вы можете изменить код во время работы сайта.Он будет обновляться динамически.

## <a name="customize-the-layout"></a>Настройка макета

Каждая страница в решении для удаленного мониторинга состоит из набора элементов управления, которые называются *панелями* в исходном коде. Например, страница **Панель мониторинга** состоит из пяти панелей: "Обзор", "Карта", "Сигналы", "Телеметрия" и "Ключевые показатели эффективности". Исходный код, определяющий каждую страницу и ее панели, находится в репозитории GitHub [pcs-remote-monitoring-webui](https://github.com/Azure/pcs-remote-monitoring-webui). Например, код, определяющий страницу **Панель мониторинга**, ее макет и панели на странице, расположен в папке [src/components/pages/dashboard](https://github.com/Azure/pcs-remote-monitoring-webui/tree/master/src/components/pages/dashboard).

Так как панели управляют собственным макетом и размером, вы можете без труда изменить макет страницы. Например, следующие изменения в элементе **PageContent** в файле `src/components/pages/dashboard/dashboard.js` заменяют положение панелей карты и телеметрии и изменяют относительную ширину панелей карты и ключевых показателей эффективности.

```nodejs
<PageContent className="dashboard-container" key="page-content">
  <Grid>
    <Cell className="col-1 devices-overview-cell">
      <OverviewPanel
        openWarningCount={openWarningCount}
        openCriticalCount={openCriticalCount}
        onlineDeviceCount={onlineDeviceCount}
        offlineDeviceCount={offlineDeviceCount}
        isPending={kpisIsPending || devicesIsPending}
        error={devicesError || kpisError}
        t={t} />
    </Cell>
    <Cell className="col-5">
      <TelemetryPanel
        telemetry={telemetry}
        isPending={telemetryIsPending}
        error={telemetryError}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <CustAlarmsPanel
        alarms={currentActiveAlarmsWithName}
        isPending={kpisIsPending || rulesIsPending}
        error={rulesError || kpisError}
        t={t} />
    </Cell>
    <Cell className="col-4">
    <PanelErrorBoundary msg={t('dashboard.panels.map.runtimeError')}>
        <MapPanel
          azureMapsKey={azureMapsKey}
          devices={devices}
          devicesInAlarm={devicesInAlarm}
          mapKeyIsPending={azureMapsKeyIsPending}
          isPending={devicesIsPending || kpisIsPending}
          error={azureMapsKeyError || devicesError || kpisError}
          t={t} />
      </PanelErrorBoundary>
    </Cell>
    <Cell className="col-6">
      <KpisPanel
        topAlarms={topAlarmsWithName}
        alarmsPerDeviceId={alarmsPerDeviceType}
        criticalAlarmsChange={criticalAlarmsChange}
        warningAlarmsChange={warningAlarmsChange}
        isPending={kpisIsPending || rulesIsPending || devicesIsPending}
        error={devicesError || rulesError || kpisError}
        colors={chartColorObjects}
        t={t} />
    </Cell>
  </Grid>
</PageContent>
```

![Изменение макета панели](./media/iot-accelerators-remote-monitoring-customize/layout.png)

> [!NOTE]
> Карта не настроена в локальном развертывании.

Вы также можете добавить несколько экземпляров одной панели или нескольких версий, если [дублируете и настроите панель](#duplicate-and-customize-an-existing-control). В следующем примере показано, как добавить два экземпляра панели телеметрии, изменив файл `src/components/pages/dashboard/dashboard.js`.

```nodejs
<PageContent className="dashboard-container" key="page-content">
  <Grid>
    <Cell className="col-1 devices-overview-cell">
      <OverviewPanel
        openWarningCount={openWarningCount}
        openCriticalCount={openCriticalCount}
        onlineDeviceCount={onlineDeviceCount}
        offlineDeviceCount={offlineDeviceCount}
        isPending={kpisIsPending || devicesIsPending}
        error={devicesError || kpisError}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <TelemetryPanel
        telemetry={telemetry}
        isPending={telemetryIsPending}
        error={telemetryError}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <TelemetryPanel
        telemetry={telemetry}
        isPending={telemetryIsPending}
        error={telemetryError}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    <Cell className="col-2">
      <CustAlarmsPanel
        alarms={currentActiveAlarmsWithName}
        isPending={kpisIsPending || rulesIsPending}
        error={rulesError || kpisError}
        t={t} />
    </Cell>
    <Cell className="col-4">
    <PanelErrorBoundary msg={t('dashboard.panels.map.runtimeError')}>
        <MapPanel
          azureMapsKey={azureMapsKey}
          devices={devices}
          devicesInAlarm={devicesInAlarm}
          mapKeyIsPending={azureMapsKeyIsPending}
          isPending={devicesIsPending || kpisIsPending}
          error={azureMapsKeyError || devicesError || kpisError}
          t={t} />
      </PanelErrorBoundary>
    </Cell>
    <Cell className="col-6">
      <KpisPanel
        topAlarms={topAlarmsWithName}
        alarmsPerDeviceId={alarmsPerDeviceType}
        criticalAlarmsChange={criticalAlarmsChange}
        warningAlarmsChange={warningAlarmsChange}
        isPending={kpisIsPending || rulesIsPending || devicesIsPending}
        error={devicesError || rulesError || kpisError}
        colors={chartColorObjects}
        t={t} />
    </Cell>
  </Grid>
</PageContent>
```

Затем вы можете просмотреть разные данные телеметрии на каждой панели.

![Несколько панелей данных телеметрии](./media/iot-accelerators-remote-monitoring-customize/multiple-telemetry.png)

> [!NOTE]
> Карта не настроена в локальном развертывании.

## <a name="duplicate-and-customize-an-existing-control"></a>Дублирование и настройка имеющихся элементов управления

Ниже показано, как использовать панель **сигналов**, чтобы продемонстрировать, как дублировать имеющуюся панель, изменить ее и использовать измененную версию.

1. В локальной копии репозитория создайте копию папки **alarms** в папке `src/components/pages/dashboard/panels`. Присвойте новой копии имя **cust_alarms**.

1. В файле **alarmsPanel.js** папки **cust_alarms** измените имя класса на **CustAlarmsPanel**:

    ```nodejs
    export class CustAlarmsPanel extends Component {
    ```

1. Добавьте следующую строку в файл `src/components/pages/dashboard/panels/index.js` :

    ```nodejs
    export * from './cust_alarms';
    ```

1. Замените `AlarmsPanel` на `CustAlarmsPanel` в файле `src/components/pages/dashboard/dashboard.js`:

    ```nodejs
    import {
      OverviewPanel,
      CustAlarmsPanel,
      TelemetryPanel,
      KpisPanel,
      MapPanel,
      transformTelemetryResponse,
      chartColors
    } from './panels';

    ...

    <Cell className="col-3">
      <CustAlarmsPanel
        alarms={currentActiveAlarmsWithName}
        isPending={kpisIsPending || rulesIsPending}
        error={rulesError || kpisError}
        t={t} />
    </Cell>
    ```

Вы заменили исходную панель **сигналов** копией **CustAlarms**. Эта копия аналогична исходной. Теперь можно изменить копию. Например, чтобы изменить порядок столбцов в панели **сигналов**, сделайте следующее:

1. Откройте файл `src/components/pages/dashboard/panels/cust_alarms/alarmsPanel.js` .

1. Измените определения столбцов, как показано в следующем фрагменте кода:

    ```nodejs
    this.columnDefs = [
      rulesColumnDefs.severity,
      {
        headerName: 'rules.grid.count',
        field: 'count'
      },
      {
        ...rulesColumnDefs.ruleName,
        minWidth: 200
      },
      rulesColumnDefs.explore
    ];
    ```

На следующем рисунке показана новая версия панели **сигналов**:

![Обновленная панель сигналов](./media/iot-accelerators-remote-monitoring-customize/reorder-columns.png)

## <a name="customize-the-telemetry-chart"></a>Настройка диаграммы телеметрии

Диаграмма телеметрии на странице **Панель мониторинга** определяется файлами в папке `src/components/pages/dashboard/panels/telemtry`. Пользовательский интерфейс извлекает данные телеметрии из серверной части решения в файле `src/services/telemetryService.js`. Ниже показано, как изменить период времени, отображаемый на диаграмме телеметрии, с 15 минут до 5:

1. В файле `src/services/telemetryService.js` найдите функцию **getTelemetryByDeviceIdP15M**. Скопируйте функцию и измените ее копию следующим образом:

    ```nodejs
    static getTelemetryByDeviceIdP5M(devices = []) {
      return TelemetryService.getTelemetryByMessages({
        from: 'NOW-PT5M',
        to: 'NOW',
        order: 'desc',
        devices
      });
    }
    ```

1. Чтобы использовать эту функцию для заполнения диаграммы телеметрии, откройте файл `src/components/pages/dashboard/dashboard.js`. Найдите строку, которая инициализирует поток телеметрии, и измените ее следующим образом:

    ```node.js
    const getTelemetryStream = ({ deviceIds = [] }) => TelemetryService.getTelemetryByDeviceIdP5M(deviceIds)
    ```

Диаграмма телеметрии отображает данные телеметрии за 5 минут:

![Диаграмма телеметрии с данными за один день](./media/iot-accelerators-remote-monitoring-customize/telemetry-period.png)

## <a name="add-a-new-kpi"></a>Добавление нового ключевого показателя эффективности

Страница **Панель мониторинга** отображает ключевые показатели эффективности на панели **ключевых показателей эффективности системы**. Эти показатели вычисляются в файле `src/components/pages/dashboard/dashboard.js`. Они преобразовываются для просмотра с помощью файла `src/components/pages/dashboard/panels/kpis/kpisPanel.js`. Ниже показано, как рассчитать и преобразовать для просмотра новое значение ключевого показателя эффективности на странице **Панель мониторинга**. Ниже приведен пример добавления нового процентного изменения в ключевом показателе эффективности аварийного предупреждения.

1. Откройте файл `src/components/pages/dashboard/dashboard.js` . Измените объект **initialState**, чтобы он включал свойство **warningAlarmsChange**, как показано ниже.

    ```nodejs
    const initialState = {
      ...

      // Kpis data
      currentActiveAlarms: [],
      topAlarms: [],
      alarmsPerDeviceId: {},
      criticalAlarmsChange: 0,
      warningAlarmsChange: 0,
      kpisIsPending: true,
      kpisError: null,

      ...
    };
    ```

1. Измените объект **currentAlarmsStats**, чтобы он включал свойство **totalWarningCount**:

    ```nodejs
    return {
      openWarningCount: (acc.openWarningCount || 0) + (isWarning && isOpen ? 1 : 0),
      openCriticalCount: (acc.openCriticalCount || 0) + (isCritical && isOpen ? 1 : 0),
      totalWarningCount: (acc.totalWarningCount || 0) + (isWarning ? 1 : 0),
      totalCriticalCount: (acc.totalCriticalCount || 0) + (isCritical ? 1 : 0),
      alarmsPerDeviceId: updatedAlarmsPerDeviceId
    };
    ```

1. Вычислите новый ключевой показатель эффективности. Найти вычисления по количеству критических предупреждений. Дублируйте код и измените копию следующим образом:

    ```nodejs
    // ================== Warning Alarms Count - START
    const currentWarningAlarms = currentAlarmsStats.totalWarningCount;
    const previousWarningAlarms = previousAlarms.reduce(
      (cnt, { severity }) => severity === 'warning' ? cnt + 1 : cnt,
      0
    );
    const warningAlarmsChange = ((currentWarningAlarms - previousWarningAlarms) / currentWarningAlarms * 100).toFixed(2);
    // ================== Warning Alarms Count - END
    ```

1. Добавьте ключевой показатель эффективности **warningAlarmsChange** в поток данных КПЭ.

    ```nodejs
    return ({
      kpisIsPending: false,

      // Kpis data
      currentActiveAlarms,
      topAlarms,
      criticalAlarmsChange,
      warningAlarmsChange,
      alarmsPerDeviceId: currentAlarmsStats.alarmsPerDeviceId,

      ...
    });

1. Include the new **warningAlarmsChange** KPI in the state data used to render the UI:

    ```nodejs
    const {
      ...

      currentActiveAlarms,
      topAlarms,
      alarmsPerDeviceId,
      criticalAlarmsChange,
      warningAlarmsChange,
      kpisIsPending,
      kpisError,

      ...
    } = this.state;
    ```

1. Обновите данные, передаваемые на панель ключевых показателей эффективности:

    ```node.js
    <KpisPanel
      topAlarms={topAlarmsWithName}
      alarmsPerDeviceId={alarmsPerDeviceType}
      criticalAlarmsChange={criticalAlarmsChange}
      warningAlarmsChange={warningAlarmsChange}
      isPending={kpisIsPending || rulesIsPending || devicesIsPending}
      error={devicesError || rulesError || kpisError}
      colors={chartColorObjects}
      t={t} />
    ```

Вы внесли нужные изменения в файл `src/components/pages/dashboard/dashboard.js`. Ниже описаны изменения, внесенные в файл `src/components/pages/dashboard/panels/kpis/kpisPanel.js`, для отображения нового ключевого показателя эффективности.

1. Измените следующую строку кода для получения нового значения ключевого показателя эффективности следующим образом:

    ```nodejs
    const { t, isPending, criticalAlarmsChange, warningAlarmsChange, error } = this.props;
    ```

1. Измените разметку для отображения нового значения ключевого показателя эффективности следующим образом:

    ```nodejs
    <div className="kpi-cell">
      <div className="kpi-header">{t('dashboard.panels.kpis.criticalAlarms')}</div>
      <div className="critical-alarms">
        {
          criticalAlarmsChange !== 0 &&
            <div className="kpi-percentage-container">
              <div className="kpi-value">{ criticalAlarmsChange }</div>
              <div className="kpi-percentage-sign">%</div>
            </div>
        }
      </div>
      <div className="kpi-header">{t('Warning alarms')}</div>
      <div className="critical-alarms">
        {
          warningAlarmsChange !== 0 &&
            <div className="kpi-percentage-container">
              <div className="kpi-value">{ warningAlarmsChange }</div>
              <div className="kpi-percentage-sign">%</div>
            </div>
        }
      </div>
    </div>
    ```

На странице **Панель мониторинга** теперь отображается новое значение ключевого показателя эффективности.

![Ключевой показатель эффективности предупреждения](./media/iot-accelerators-remote-monitoring-customize/new-kpi.png)

## <a name="customize-the-map"></a>Настройка карты

Дополнительные сведения о компонентах карты в решении см. на странице GitHub о [настройке карты](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map).

<!--
### Connect an external visualization tool

See the [Connect an external visualization tool](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to connect an external visualization tool.

-->

## <a name="other-customization-options"></a>Другие возможности настройки

Для дополнительного изменения уровня представления и визуализации в решении для удаленного мониторинга можно изменить код. Соответствующие репозитории GitHub:

* [Микрослужба конфигурации для решений Интернета вещей Azure (.NET)](https://github.com/Azure/pcs-ui-config-dotnet/)
* [Микрослужба конфигурации для решений Интернета вещей Azure (Java)](https://github.com/Azure/pcs-ui-config-java/)
* [Веб-интерфейс для удаленного мониторинга Интернета вещей Azure PCS](https://github.com/Azure/pcs-remote-monitoring-webui)

## <a name="next-steps"></a>Дополнительная информация

Из статьи вы узнали о ресурсах, которые могут помочь вам при настройке пользовательского веб-интерфейса в акселераторе решений для удаленного мониторинга.

Более подробные сведения об акселераторе решений для удаленного мониторинга см. в статье [Архитектура предварительно настроенного решения удаленного мониторинга](iot-accelerators-remote-monitoring-sample-walkthrough.md).

Дополнительные сведения о настройке решения для удаленного мониторинга см. в статье [Настройка и повторное развертывание микрослужбы](iot-accelerators-microservices-example.md).
<!-- Next tutorials in the sequence -->