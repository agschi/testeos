<properties
   pageTitle="將 ASP.NET 容器部署到遠端 Docker 主機 | Microsoft Azure"
   description="了解如何使用 Visual Studio Tools for Docker，將 ASP.NET 5 Web 應用程式發佈到 Azure Docker 主機電腦上執行的 Docker 容器"   
   services="visual-studio-online"
   documentationCenter=".net"
   authors="allclark"
   manager="douge"
   editor=""/>

<tags
   ms.service="visual-studio-online"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="06/08/2016"
   ms.author="allclark"/>

# 將 ASP.NET 容器部署到遠端 Docker 主機

## 概觀
Docker 是輕量級容器引擎，與虛擬機器在某些方面類似，您可以用它來裝載應用程式和服務。Visual Studio 在 Ubuntu、CoreOS 和 Windows 上支援 Docker。本教學課程將逐步引導您使用 [Visual Studio 2015 Tools for Docker](http://aka.ms/DockerToolsForVS) 擴充，將 ASP.NET 5 應用程式發佈至 Azure 上的 Docker 主機。

## 1\.必要條件
需要下列項目，才能完成本教學課程：

- 按照[如何使用 Docker 電腦搭配 Azure](./virtual-machines/virtual-machines-linux-docker-machine.md) 中所述，建立 Azure Docker 主機 VM
- 安裝 [Visual Studio 2015](https://www.visualstudio.com/zh-TW/downloads/download-visual-studio-vs.aspx)
- [Visual Studio 2015 RC Tools for Docker - 預覽](http://aka.ms/DockerToolsForVS)

## 2\.建立 ASP.NET 5 Web 應用程式
下列步驟將逐步引導您建立將在本教學課程中使用的基本 ASP.NET 5 應用程式。

[AZURE.INCLUDE [create-aspnet5-app](../includes/create-aspnet5-app.md)]

## 3\.新增 Docker 支援

[AZURE.INCLUDE [create-aspnet5-app](../includes/vs-azure-tools-docker-add-docker-support.md)]

## 4\.指向遠端 Docker 主機

1.  在 [Visual Studio 方案總管] 中，找出 [屬性] 資料夾並將其展開。
1.  開啟 [Docker.props] 檔案。

    ![開啟 Docker.props 檔案。][0]

1.  將 [DockerMachineName] 的值變更為遠端 Docker 主機的名稱。如果您不知道遠端 Docker 主機的名稱，請在 Windows PowerShell 提示字元中執行 ```docker-machine ls```。對所需的主機使用 [名稱] 資料行底下所列的值。

    ![變更 Docker 機器名稱][1]

1.  重新啟動 Visual Studio。

## 5\.設定 Azure Docker 主機端點
將您的應用程式從 Visual Studio 部署至 Azure 之前，請將端點 80 新增到 Docker 主機虛擬機器，讓您稍後可從瀏覽器檢視應用程式。此操作可以透過 [Azure 傳統入口網站](http://go.microsoft.com/fwlink/?LinkID=213885)或透過 Windows PowerShell 完成︰

- **使用 [Azure 傳統入口網站](http://go.microsoft.com/fwlink/?LinkID=213885)來設定 Azure Docker 主機端點**

    1.  瀏覽到 [Azure 傳統入口網站](http://go.microsoft.com/fwlink/?LinkID=213885)。 
    
    1.  選取 [虛擬機器]。
    
    1.  選取您的 Docker 主機虛擬機器。
    
    1.  選取 [端點] 索引標籤。
    
    1.  選取 [新增]\(在頁面底部)。
    
    1.  遵循指示來公開連接埠 80，此連接埠依預設由部署指令碼使用。

- **使用 Windows PowerShell 來設定 Azure Docker 主機端點**

    1. 開啟 Windows PowerShell
    1. 在 Windows PowerShell 提示中輸入下列命令 (變更角括號中的值以符合您的環境)︰  

        ```PowerShell
        C:\PS>Get-AzureVM -ServiceName "<your_cloud_service_name>" -Name "<your_vm_name>" | Add-AzureEndpoint -Name "<endpoint_name>" -Protocol "tcp" -PublicPort 80 -LocalPort 80 | Update-AzureVM
        ```

## 6\.建置並執行應用程式
當部署至遠端主機時，用於 [編輯及重新整理] 開發的磁碟區對應功能將無法運作。因此，您將需要在建置應用程式時使用 [版本組態]，以避免使用磁碟區對應組態。請遵循下列步驟來執行您的應用程式。

1.  從 Visual Studio 工具列中，選取 [版本組態]

1.  將啟動目標變更為 [Docker]。

1.  選取 [Docker] 圖示，以建置並執行應用程式。

![啟動應用程式][2]

您應該會看到類似下面的結果。

![檢視您的應用程式][3]

[0]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/docker-props-in-solution-explorer.png
[1]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/change-docker-machine-name.png
[2]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/launch-application.png
[3]: ./media/vs-azure-tools-docker-hosting-web-apps-in-docker/view-application.png

<!---HONumber=AcomDC_0615_2016-->