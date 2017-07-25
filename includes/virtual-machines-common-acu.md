



Мы разработали концепцию вычислительной единицы Azure (ACU), чтобы предоставить способ сравнения производительности ЦП (вычислительной мощности) разных номеров SKU Azure. Это поможет вам легко определить, какие SKU с наибольшей вероятностью удовлетворят ваши требования к производительности.  На данный момент в качестве стандарта единицы ACU выбрана малая ВМ (Standard_A1), равная 100, и все прочие SKU представляют то, насколько быстрее данный SKU может выполнять стандартную нагрузку. 

> [!IMPORTANT]
> Единица ACU используется только для справки.  Фактические результаты для конкретной рабочей нагрузки могут отличаться. 
> 
> 

<br>

| Семейство SKU | ACU/число ядер |
| --- | --- |
| [A0](../articles/virtual-machines/windows/sizes-general.md) |50 |
| [A1–A4](../articles/virtual-machines/windows/sizes-general.md) |100 |
| [A5–A7](../articles/virtual-machines/windows/sizes-general.md) |100 |
| [A1_v2–A8_v2](../articles/virtual-machines/windows/sizes-general.md) |100 |
| [A2m_v2–A8m_v2](../articles/virtual-machines/windows/sizes-general.md) |100 |
| [A8-A11](../articles/virtual-machines/windows/sizes-hpc.md) |225* |
| [D1–D14](../articles/virtual-machines/windows/sizes-general.md) |160 |
| [D1_v2–D15_v2](../articles/virtual-machines/windows/sizes-general.md) |210–250* |
| [DS1–DS14](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) |160 |
| [DS1_v2–DS15_v2](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) |210–250* |
| [D_v3](../articles/virtual-machines/virtual-machines-windows-sizes-general.md) |160–190* ** |
| [Ds_v3](../articles/virtual-machines/virtual-machines-windows-sizes-general.md) |160–190* ** |
| [E_v3](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) |160–190* ** |
| [Es_v3](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) |160–190* ** |
| [F1–F16](../articles/virtual-machines/windows/sizes-compute.md) |210–250* |
| [F1s–F16s](../articles/virtual-machines/windows/sizes-compute.md) |210–250* |
| [G1–G5](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) |180–240* |
| [GS1–GS5](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) |180–240* |
| [H](../articles/virtual-machines/windows/sizes-hpc.md) |290–300* |
| [L4s-L32s](../articles/virtual-machines/windows/sizes-storage.md) |180–240* |
| [M](../articles/virtual-machines/virtual-machines-windows-sizes-memory.md) | 160–180** |

Единицы ACU, помеченные звездочкой, основаны на технологии Intel® Turbo для увеличения частоты ЦП и значительного повышения производительности.  Степень увеличения производительности может различаться в зависимости от размера виртуальной машины, рабочей нагрузки и других рабочих нагрузок, выполняющихся на том же узле.

** С поддержкой технологии Hyper-Threading. 
