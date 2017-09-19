Организациям требуются крупномасштабные вычисления. Рабочие нагрузки крупномасштабных вычислений включают техническое проектирование и анализ, вычисление финансовых рисков, отрисовку образов, комплексное моделирование, моделирование методом Монте-Карло и другие задачи. 

Благодаря Azure можно запускать высокопроизводительные вычисления (HPC) и пакетные рабочие нагрузки в кластерах виртуальных машин Azure с помощью любых служб инфраструктуры Azure или управляемых служб. Azure позволяет легко масштабировать вычислительные ресурсы на тысячах виртуальных машин или ядер, а затем уменьшать их масштаб, если требуется меньше ресурсов. 

Решения Azure HPC эффективно выполняют рабочие нагрузки Windows и Linux с ресурсоемкими вычислениями: от параллельных пакетных заданий до традиционного моделирования HPC. Ведущие приложения HPC могут выполняться на виртуальных машинах Azure, позволяя использовать специализированные размеры и образы виртуальных машин, предназначенные для задач с большим объемом вычислений. 


## <a name="solution-architectures"></a>Архитектуры решений

Примеры архитектуры решений с большим объемом вычислений в Azure:

* Выполнение приложений HPC на виртуальных машинах или в [масштабируемых наборах виртуальных машин](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) с использованием любого диспетчера кластера или сетки Windows либо Linux. См. [дополнительные сведения](https://azure.microsoft.com/en-us/solutions/architecture/hpc-cluster/)
* Создание гибридных решений, расширяющих локальный кластер HPC для передачи пиковых нагрузок на виртуальные машины Azure. См. [дополнительные сведения](https://azure.microsoft.com/en-us/solutions/architecture/hpc-on-prem-burst/).
* Использование управляемых и масштабируемых [пакетных](https://azure.microsoft.com/services/batch/) служб Azure для запуска рабочих нагрузок с ресурсоемкими вычислениями без управления базовой инфраструктурой. См. [дополнительные сведения](https://azure.microsoft.com/en-us/solutions/architecture/hpc-big-compute-saas/).

## <a name="hpc-cluster-and-grid-solutions"></a>Решения кластера HPC и сетки
Развертывайте или расширяйте знакомые средства управления рабочей нагрузкой HPC на виртуальных машинах Azure и выполняйте рабочие нагрузки с ресурсоемкими вычислениями. Вы можете использовать средства с открытым исходным кодом, например [диспетчер рабочей нагрузки Slurm](https://slurm.schedmd.com/), диспетчеры кластера или сетки корпорации Майкрософт или других издателей.

### <a name="linux-and-oss-cluster-solutions"></a>Кластерные решения Linux и OSS
Используйте шаблоны Azure Resource Manager для развертывания решений кластера Linux HPC на виртуальных машинах или в масштабируемых наборах виртуальных машин, включая:

* [Slurm](https://azure.microsoft.com/documentation/templates/slurm/)
* [Torque](https://azure.microsoft.com/documentation/templates/torque-cluster/)
* [PBS Professional](https://github.com/xpillons/azure-hpc/tree/master/Compute-Grid-Infra)

См. также коллекцию [шаблонов 5-click](https://github.com/tanewill/5clickTemplates).

### <a name="grid-managers"></a>Диспетчеры сетки
Корпорация Майкрософт сотрудничает с поставщиками коммерческих диспетчеров сеток, чтобы их решения стали доступными на виртуальных машинах Azure. Примеры приведены ниже.

* [IBM Spectrum Symphony и Symphony LSF](https://azure.microsoft.com/blog/ibm-and-microsoft-azure-support-spectrum-symphony-and-spectrum-lsf/)
* [TIBCO DataSynapse GridServer](https://azure.microsoft.com/blog/tibco-datasynapse-comes-to-the-azure-marketplace/) 
* [Bright Cluster Manager](http://www.brightcomputing.com/technology-partners/microsoft)
* [Univa Grid Engine](http://www.univa.com/products/grid-engine)

### <a name="microsoft-hpc-pack"></a>Пакет Microsoft HPC
[Пакет HPC](https://technet.microsoft.com/library/cc514029(v=ws.11).aspx) — это бесплатное решение Майкрософт для высокопроизводительных вычислений, созданное на основе технологий Microsoft Azure и Windows Server. Пакет HPC поддерживает широкий диапазон рабочих нагрузок для выполнения на виртуальных машинах [Windows](../articles/virtual-machines/windows/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) и [Linux](../articles/virtual-machines/linux/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Ниже перечислено, что вы можете узнать.

* [Создание кластера HPC Pack 2016 в Azure](../articles/virtual-machines/windows/hpcpack-2016-cluster.md)
* [Используйте преимущества Azure с помощью HPC Pack](https://technet.microsoft.com/library/gg481749(v=ws.11).aspx)




## <a name="azure-batch"></a>Пакетная служба Azure
[Пакетная служба](../articles/batch/batch-technical-overview.md) — это служба платформы, которая позволяет эффективно работать с приложениями для крупномасштабных параллельных и высокопроизводительных вычислений (HPC) в облаке. Пакетная служба Azure планирует запуск ресурсоемких вычислительных задач в управляемом пуле виртуальных машин и автоматически масштабирует вычислительные ресурсы, учитывая требования заданий. 

Используйте пакеты SDK для пакетной службы и средства для интеграции приложений HPC или рабочих нагрузок контейнера с помощью Azure, промежуточного хранения данных в Azure и создания конвейеров выполнения заданий. Кроме того, можно использовать излишки емкости (низкоприоритетной) виртуальной машины Azure по [сниженной цене](https://azure.microsoft.com/pricing/details/batch/), что значительно снижает стоимость выполнения некоторых рабочих нагрузок в пакетной службе.

Ниже перечислено, что вы можете узнать.

* [Сведения о начале разработки с помощью пакетной службы](../articles/batch/batch-dotnet-get-started.md)
* [Примеры использования кода пакетной службы](https://github.com/Azure/azure-batch-samples)
* [Сведения об использовании низкоприоритетных виртуальных машин в пакетной службе (предварительная версия)](../articles/batch/batch-low-pri-vms.md)
* [Сведения о запуске контейнерных рабочих нагрузок HPC с помощью Batch Shipyard](https://github.com/Azure/batch-shipyard)
* [Сведения об использовании языка R с пакетной службой](https://github.com/Azure/doAzureParallel)

## <a name="hpc-and-gpu-vm-sizes"></a>Размеры виртуальной машины графического процессора и HPC
Azure предлагает различные размеры для виртуальных машин [Linux](../articles/virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) и [Windows](../articles/virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), в том числе размеры для рабочих нагрузок с ресурсоемкими вычислениями. Например, виртуальные машины H16r и H16mr могут подключаться к сети RDMA серверной части с высокой пропускной способностью. Эта облачная сеть может улучшить производительность тесно связанных параллельных приложений, выполняемых под управлением [Microsoft MPI](https://msdn.microsoft.com/library/bb524831.aspx) или Intel MPI. Виртуальные машины серии N оснащены графическими процессорами NVIDIA и предназначены для приложений с ресурсоемкими вычислениями или графикой, в том числе для обучения искусственного интеллекта (AI) и визуализации. 

Подробнее.

* Размеры виртуальных машин [Linux](../articles/virtual-machines/linux/sizes-hpc.md) и [Windows](../articles/virtual-machines/windows/sizes-hpc.md) для высокопроизводительных вычислений 
* Размеры виртуальных машин [Linux](../articles/virtual-machines/linux/sizes-gpu.md) и [Windows](../articles/virtual-machines/windows/sizes-gpu.md) с графическими процессорами. 

Ниже перечислено, что вы можете узнать.

* [Настройка кластера Linux RDMA для выполнения приложений MPI](../articles/virtual-machines/linux/classic/rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Настройка кластера RDMA в Windows с помощью пакета HPC для запуска приложений MPI](../articles/virtual-machines/windows/classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Использование экземпляров с поддержкой RDMA или графического процессора (GPU) в пулах пакетной службы](../articles/batch/batch-pool-compute-intensive-sizes.md)

## <a name="hpc-images"></a>Образы HPC

Образы виртуальных машин Windows и Linux, предназначенных для HPC, можно найти в [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/). Примеры приведены ниже.

* [HPC RogueWave на основе CentOS](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/RogueWave.CentOSbased73HPC?tab=Overview)
* [SUSE Linux Enterprise Server для HPC](https://azure.microsoft.com/marketplace/partners/suse/suselinuxenterpriseserver12optimizedforhighperformancecompute/)
* [TIBCO Grid Server 6.2.0 Engine](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/tibco-software.gridserverlinuxengine?tab=Overview)
* [Виртуальные машины для анализа и обработки данных для Windows и Linux](../articles/machine-learning/machine-learning-data-science-virtual-machine-overview.md)


 
## <a name="hpc-applications"></a>Приложения HPC

Запустите пользовательские или коммерческие приложения HPC в Azure. Некоторые приложения могут эффективно масштабироваться с помощью дополнительных виртуальных машин или вычислительных ядер. В следующих разделах приведены примеры.

> [!IMPORTANT]
> Проконсультируйтесь с поставщиками всех коммерческих приложений насчет лицензирования или иных ограничений на запуск приложений в облаке. Не все поставщики предлагают лицензирование с оплатой по мере использования. Для вашего решения может потребоваться сервер лицензий в облаке или локальный сервер лицензий.

### <a name="engineering-applications"></a>Проектирование приложений


* [Altair RADIOSS](https://azure.microsoft.com/blog/availability-of-altair-radioss-rdma-on-microsoft-azure/)
* [ANSYS CFD](https://azure.microsoft.com/blog/ansys-cfd-and-microsoft-azure-perform-the-best-hpc-scalability-in-the-cloud/)
* [MATLAB Distributed Computing Server](../articles/virtual-machines/windows/matlab-mdcs-cluster.md)
* [StarCCM+](https://blogs.msdn.microsoft.com/azurecat/2017/07/07/run-star-ccm-in-an-azure-hpc-cluster/)
* [OpenFOAM](https://simulation.azure.com/casestudies/Team-182-ABB-UC-Final.pdf)



### <a name="graphics-and-rendering"></a>Графика и отрисовка

* [Autodesk Maya, 3ds Max и Arnold](../articles/batch/batch-rendering-service.md) в пакетной службе Azure (предварительная версия)

### <a name="ai-and-deep-learning"></a>Искусственный интеллект и глубокое обучение

* [Microsoft Cognitive Toolkit](https://docs.microsoft.com/cognitive-toolkit/cntk-on-azure)
* [Набор средств глубокого обучения для виртуальной машины обработки и анализа данных](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-ads.dsvm-deep-learning)
* [Инструкции Batch Shipyard для глубокого обучения](https://github.com/Azure/batch-shipyard/tree/master/recipes#deeplearning)

## <a name="solution-partners"></a>Решения партнеров

Партнеры, разрабатывающие или создающие пакетные приложения и приложения HPC в Azure, перечислены ниже.

* [Cycle Computing](https://cyclecomputing.com/) (компания уже [объединена с корпорацией Майкрософт](https://blogs.microsoft.com/blog/2017/08/15/microsoft-acquires-cycle-computing-accelerate-big-computing-cloud/))
* [Rescale](https://www.rescale.com/azure/)
* [UberCloud](https://www.theubercloud.com/)

## <a name="hpc-storage"></a>Хранилище HPC

Масштабные рабочие нагрузки пакетной службы и HPC требуют ресурсов для хранения данных и доступа, которые превышают возможности традиционных файловых систем в облаке. Вы можете использовать параллельные решения файловых систем в Azure, например [Lustre](http://lustre.org/) и [BeeGFS](http://www.beegfs.com/content/).

Подробнее.

* [Параллельные файловые системы для хранилища HPC в Azure](https://blogs.msdn.microsoft.com/azurecat/2017/03/17/parallel-file-systems-for-hpc-storage-on-azure/)


## <a name="related-azure-services"></a>Связанные службы Azure

Виртуальные машины Azure, масштабируемые наборы виртуальных машин и связанные вычислительные службы являются основой для большинства решений Azure HPC. Но вы можете использовать для своего решения другие связанные службы Azure. Ниже приведен неполный список этих служб.

### <a name="storage"></a>Хранилище

* [Хранилище BLOB-объектов, таблиц и очередей](../articles/storage/storage-introduction.md)
* [Хранилище файлов](../articles/storage/storage-files-introduction.md)

### <a name="data-and-analytics"></a>Данные и аналитика
* [HDInsight](../articles/hdinsight/hdinsight-hadoop-introduction.md) для кластеров Hadoop в Azure
* [Фабрика данных](../articles/data-factory/data-factory-introduction.md)
* [Data Lake Store](../articles/data-lake-store/data-lake-store-overview.md)
* [Машинное обучение](../articles/machine-learning/machine-learning-what-is-machine-learning.md)
* [База данных SQL](../articles/sql-database/sql-database-technical-overview.md)

### <a name="networking"></a>Сеть
* [Виртуальная сеть](../articles/virtual-network/virtual-networks-overview.md)
* [ExpressRoute](../articles/expressroute/expressroute-introduction.md)

### <a name="containers"></a>Контейнеры
* [Служба контейнеров](../articles/container-service/dcos-swarm/container-service-intro.md)
* [Реестр контейнеров](../articles/container-registry/container-registry-intro.md)



## <a name="customer-stories"></a>Истории клиентов

Ниже приведен список клиентов, которые выполнили свои бизнес-задачи с помощью решений Azure HPC.

* [ANEO](https://customers.microsoft.com/story/it-provider-finds-highly-scalable-cloud-based-hpc-redu) 
* [AXA Global P&C](https://customers.microsoft.com/story/axa-global-p-and-c)
* [Axioma](https://customers.microsoft.com/story/axioma-delivers-fintechs-first-born-in-the-cloud-multi-asset-class-enterprise-risk-solution)
* [d3View](https://customers.microsoft.com/story/big-data-solution-provider-adopts-new-cloud-gains-thou)
* [Hymans Robertson](https://customers.microsoft.com/story/hymans-robertson)
* [MetLife](https://enterprise.microsoft.com/en-us/customer-story/industries/insurance/metlife/)
* [Microsoft Research](https://customers.microsoft.com/doclink/fast-lmm-and-windows-azure-put-genetics-research-on-fa)
* [Milliman](https://customers.microsoft.com/story/actuarial-firm-works-to-transform-insurance-industry-w)
* [Mitsubishi UFJ Securities International](https://customers.microsoft.com/story/powering-risk-compute-grids-in-the-cloud)
* [Schlumberger](http://azure.microsoft.com/blog/big-compute-for-large-engineering-simulations)
* [Towers Watson](https://customers.microsoft.com/story/insurance-tech-provider-delivers-disruptive-solutions)


## <a name="next-steps"></a>Дальнейшие действия
* Дополнительные сведения о решениях больших вычислений для [моделирования проектирования](https://simulation.azure.com/), [отрисовки](https://simulation.azure.com/) и [банковских рынков и рынков капитала](https://finance.azure.com/).
* Последние объявления см. в [блоге группы Microsoft HPC и пакетной службы](http://blogs.technet.com/b/windowshpc/), а также в [блоге Azure](https://azure.microsoft.com/blog/tag/hpc/).


