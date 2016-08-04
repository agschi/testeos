<properties
    pageTitle="Site Recovery (VMware to Azure)/Reprotect VM after failover"
    description="Site Recovery (VMware -> Azure)/Znovuzapnutí ochrany virtuálního počítače po navrácení služeb po obnovení"
    service="microsoft.recoveryservices"
    resource="vaults"
    authors="aashu"
    displayOrder=""
    selfHelpType="generic"
    supportTopicIds="32536447"
    resourceTags=""
    productPesIds="15207"
    cloudEnvironments="public"
/>


# Site Recovery (VMware To Azure)/Znovuzapnutí ochrany virtuálního počítače po navrácení služeb po obnovení

Běžné problémy během navrácení služeb po obnovení nebo znovuzapnutí ochrany

## **Doporučené kroky**

* Účet vCenter použitý pro zjišťování by měl mít správná oprávnění pro navrácení služeb po obnovení. <br>
[Zde zobrazte seznam oprávnění.](https://aka.ms/asrsupfailbackperm)

* Licence pro vCenter by neměla být bezplatnou licencí – musí se jednat o zkušební verzi nebo být licencované. <br>
V uzlu hostitele ESXi na serveru vCenter přejděte na kartu Konfigurace -> Licencované funkce. Rozhraní API vSphere by mělo být uvedené ve funkcích produktu. Virtuální počítač, který převzal služby při selhání, by měl být ve správné síti.

* Virtuální počítač by měl být spuštěný a nacházet se v síti, aby mohl komunikovat zpátky s konfiguračním serverem a procesním serverem. <br>
Zkontrolujte, že virtuální počítač je v síti Azure připojené přes ExpressRoute nebo VPN.

* Úložiště dat, která obsahují disky virtuálního počítače, by měla být připojená k hostiteli ESXi hlavního cíle. <br>
Ujistěte se, že úložiště dat je připojené pro čtení i zápis a je typu VMFS. Jiné typy úložiště dat nejsou aktuálně podporované.

* Pokud jednotka pro uchovávání dat není v nabídce pro výběr viditelná, přidejte k hlavnímu cíli novou jednotku.<br>
    1. Svazek by neměl být používán k žádnému jinému účelu (cíl replikace atd.).
    2. Svazek by neměl být v režimu zámku.
    3. Svazek by neměl být svazkem mezipaměti. (Na tomto svazku by neměla existovat instalace MT. Svazek vlastní instalace PS+MT není jako svazek pro uchovávání dat způsobilý. Zde nainstalovaný svazek PS+MT je svazkem mezipaměti pro MT. )
    4. Typ systému souborů svazku by neměl být FAT ani FAT32.
    5. Kapacita svazku by neměla být nulová. e. Výchozí svazek pro uchovávání dat pro Windows je svazek R.
    6. Výchozí svazek pro uchovávání dat pro Linux je /mnt/retention.

* [Zde jsou uvedené i některé běžné problémy.](https://aka.ms/asrsupfailbackcommonissues)

## **Doporučené dokumenty**
Celková [dokumentace navrácení služeb po obnovení je zde](https://aka.ms/asrsupv2afailback).


<!--HONumber=Jul16_HO4-->
