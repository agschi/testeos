<properties 
   pageTitle="Uso de SDK de .NET del Almacén de Data Lake para desarrollar aplicaciones | Azure" 
   description="Uso de SDK de .NET del Almacén de Azure Data Lake para desarrollar aplicaciones" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="01/04/2016"
   ms.author="nitinme"/>

# Introducción al Almacén de Azure Data Lake mediante SDK de .NET

> [AZURE.SELECTOR]
- [Using Portal](data-lake-store-get-started-portal.md)
- [Using PowerShell](data-lake-store-get-started-powershell.md)
- [Using .NET SDK](data-lake-store-get-started-net-sdk.md)
- [Using Azure CLI](data-lake-store-get-started-cli.md)
- [Using Node.js](data-lake-store-manage-use-nodejs.md)

Aprenda a utilizar el SDK de .NET del Almacén de Azure Data Lake para crear una cuenta de Azure Data Lake y realizar operaciones básicas como crear carpetas, cargar y descargar archivos de datos, eliminar la cuenta, etc. Para obtener más información sobre Data Lake, consulte [Almacén de Azure Data Lake](data-lake-store-overview.md).

## Requisitos previos

* Visual Studio 2013 o 2015 Las instrucciones siguientes usan Visual Studio 2015.
* **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Habilite su suscripción de Azure** para la versión de vista previa pública del Almacén de Data Lake. Consulte las [instrucciones](data-lake-store-get-started-portal.md#signup).

## Creación de una aplicación .NET

1. Abra Visual Studio y cree una aplicación de consola.

2. En el menú **Archivo**, haga clic en **Nuevo** y, después, en **Proyecto**.

3. En **Nuevo proyecto**, escriba o seleccione los siguientes valores:

	| Propiedad | Valor |
	|----------|-----------------------------|
	| Categoría | Plantillas/Visual C#/Windows |
	| Plantilla | Aplicación de consola |
	| Nombre | CreateADLApplication |

4. Haga clic en **Aceptar** para crear el proyecto.

5. Agregue los paquetes de Nuget al proyecto.

	1. Haga clic con el botón derecho en el Explorador de soluciones y haga clic en **Administrar paquetes de NuGet**.
	2. En la pestaña **Administrador de paquetes de Nuget**, asegúrese de que **Origen del paquete** está establecido en **nuget.org** y que la casilla **Incluir versión preliminar** está activada.
	3. Busque e instale los siguientes paquetes del Almacén de Data Lake:
	
		* Microsoft.Azure.Management.DataLake.Store
		* Microsoft.Azure.Management.DataLake.StoreFileSystem
		* Microsoft.Azure.Management.DataLake.StoreUploader

		![Agregue un origen de Nuget](./media/data-lake-store-get-started-net-sdk/ADL.Install.Nuget.Package.png "Crear una nueva cuenta de Azure Data Lake")

	4. También debe instalar el paquete **Microsoft.Azure.Common.Authentication**. Esto también es un paquete en versión preliminar y es necesario para la autenticación con el Almacén de Azure Data Lake.

		![Agregue un origen de Nuget](./media/data-lake-store-get-started-net-sdk/adl.install.azure.auth.png "Creación de una nueva cuenta de Azure Data Lake")

	4. Cierre el **Administrador de paquetes Nuget**.

7. Abra el archivo **Program.cs** y sustituya el bloque de código existente por el siguiente. Además, se proporcionan los valores para los parámetros en el fragmento de código.

	Este código recorre el proceso de creación de un Almacén de Data Lake, de creación de carpetas en el almacén, de la carga de archivos, de la descarga de archivos y finalmente de la eliminación de la cuenta. Si busca datos de ejemplo para cargar, puede obtener la carpeta **Ambulance Data** en el [repositorio Git de Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData).
	
		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Security;
		using System.IO;
		
		using Microsoft.Azure;
		using Microsoft.Azure.Common.Authentication;
		using Microsoft.Azure.Common.Authentication.Models;
		using Microsoft.Azure.Management.DataLake.Store;
		using Microsoft.Azure.Management.DataLake.Store.Models;
		using Microsoft.Azure.Management.DataLake.StoreFileSystem;
		using Microsoft.Azure.Management.DataLake.StoreFileSystem.Models;
		using Microsoft.Azure.Management.DataLake.StoreUploader;
		using Microsoft.Azure.Common.Authentication.Factories;
		
		
		namespace CreateADLApplication
		{
		    class CreateADLApplication
		    {
		        private static DataLakeStoreManagementClient _dataLakeStoreClient;
		        private static DataLakeStoreFileSystemManagementClient _dataLakeStoreFileSystemClient;
		        private const string ResourceGroupName = "<resource_grp_name>"; //THIS SHOULD ALREADY EXIST
		
		        static void Main(string[] args)
		        {
		            var subscriptionId = new Guid("<subscription_ID>");
		            var _credentials = GetAccessToken();
		            string dataLakeAccountName = "<data_lake_store_name>";
		            string location = "East US 2";
		
		            _credentials = GetCloudCredentials(_credentials, subscriptionId);
		            _dataLakeStoreClient = new DataLakeStoreManagementClient(_credentials);
		            _dataLakeStoreFileSystemClient = new DataLakeStoreFileSystemManagementClient(_credentials);
		
		            var parameters = new DataLakeStoreAccountCreateOrUpdateParameters();
		            parameters.DataLakeStoreAccount = new DataLakeStoreAccount
		            {
		                Name = dataLakeAccountName,
		                Location = location
		            };
		
		            // Create a Data Lake Store account
		            Console.WriteLine("Creating an Azure Data Lake Store account ...");
		            _dataLakeStoreClient.DataLakeStoreAccount.Create(ResourceGroupName, parameters);
		
		            Console.WriteLine("Account created. Press ENTER to continue...");
		            Console.ReadLine();
		
		            // Create a directory called MYTEMPDIR in the store
		            Console.WriteLine("Creating a directory under the Azure Data Lake Store account");
		            CreateDir(_dataLakeStoreFileSystemClient, "/mytempdir", dataLakeAccountName, "777");
		            Console.WriteLine("Directory created. Press ENTER to continue...");
		            Console.ReadLine();
		
		            // Upload a file under MYTEMPDIR
		            Console.WriteLine("Uploading a file to the Azure Data Lake Store account");
		            bool force = false; //Set this to true if you want to overwrite existing data
		            UploadFile(_dataLakeStoreFileSystemClient, dataLakeAccountName, "C:\\users\\nitinme\\desktop\\vehicle1_09142014.csv", "/mytempdir/vehicle1_09142014.csv", force);
		            Console.WriteLine("File uploaded. Press ENTER to continue...");
		            Console.ReadLine();
		
		            // List the files in the Data Lake Store
		            Console.WriteLine("Listing all files in the Azure Data Lake Store account");
		            var fileList = ListItems(_dataLakeStoreFileSystemClient, dataLakeAccountName, "/mytempdir");
		            var fileMenuItems = fileList.Select(a => String.Format("{0,15} {1}", a.Type, a.PathSuffix)).ToList();
		            foreach (var filename in fileMenuItems)
		            {
		                Console.WriteLine(filename);
		
		            }
		            Console.WriteLine("Files listed. Press ENTER to continue...");
		            Console.ReadLine();
		
		            // Download the files from the Data Lake Store
		            Console.WriteLine("Downloading files from an Azure Data Lake Store account");
		            DownloadFile(_dataLakeStoreFileSystemClient, dataLakeAccountName, "/mytempdir/vehicle1_09142014.csv", "C:\\users\\nitinme\\desktop\\vehicle1_09142014_copy.csv", force);
		            Console.WriteLine("Files downloaded. Press ENTER to continue...");
		            Console.ReadLine();
		
		            // Delete the Data Lake Store account
		            Console.WriteLine("Azure Data Lake Store account will be deleted. Press ENTER to continue...");
		            _dataLakeStoreClient.DataLakeStoreAccount.Delete(ResourceGroupName, dataLakeAccountName);
		            Console.ReadLine();
		            Console.WriteLine("Account deleted. Press ENTER to exit...");
		            Console.ReadLine();
		        }
		
		        public static SubscriptionCloudCredentials GetAccessToken(string username = null, SecureString password = null)
		        {
		            var authFactory = new AuthenticationFactory();
		
		            var account = new AzureAccount { Type = AzureAccount.AccountType.User };
		
		            if (username != null && password != null)
		                account.Id = username;
		
		            var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
		            return new TokenCloudCredentials(authFactory.Authenticate(account, env, AuthenticationFactory.CommonAdTenant, password, ShowDialog.Auto).AccessToken);
		        }
		
		        public static SubscriptionCloudCredentials GetCloudCredentials(SubscriptionCloudCredentials creds, Guid subId)
		        {
		            return new TokenCloudCredentials(subId.ToString(), ((TokenCloudCredentials)creds).Token);
		        }
		
		        public static bool CreateDir(DataLakeStoreFileSystemManagementClient dataLakeStoreFileSystemClient, string dlAccountName, string path, string permission)
		        {
		            dataLakeStoreFileSystemClient.FileSystem.Mkdirs(path, dlAccountName, permission);
		            return true;
		        }
		
		        public static bool UploadFile(DataLakeStoreFileSystemManagementClient dataLakeStoreFileSystemClient, string dlAccountName, string srcPath, string destPath, bool force = false)
		        {
		            var parameters = new UploadParameters(srcPath, destPath, dlAccountName, isOverwrite: true);
		            var frontend = new DataLakeStoreFrontEndAdapter(dlAccountName, dataLakeStoreFileSystemClient);
		            var uploader = new DataLakeStoreUploader(parameters, frontend);
		            uploader.Execute();
		            return true;
		        }
		        
		        public static bool AppendToFile(DataLakeStoreFileSystemManagementClient dataLakeStoreFileSystemClient, string dlAccountName, string path, string content)
		        {
		            var stream = new MemoryStream(Encoding.UTF8.GetBytes(content));
		            
		            dataLakeStoreFileSystemClient.FileSystem.DirectAppend(filePath, accountName, stream);
		            return true;
		        }
		
		        public static void DownloadFile(DataLakeStoreFileSystemManagementClient dataLakeFileSystemClient,
		        string dataLakeAccountName, string srcPath, string destPath, bool force)
		        {
		            var beginOpenResponse = dataLakeFileSystemClient.FileSystem.BeginOpen(srcPath, dataLakeAccountName,
		                new FileOpenParameters());
		            var openResponse = dataLakeFileSystemClient.FileSystem.Open(beginOpenResponse.Location);
		            if (force || !File.Exists(destPath))
		                File.WriteAllBytes(destPath, openResponse.FileContents);
		        }
		
		        public static List<FileStatusProperties> ListItems(DataLakeStoreFileSystemManagementClient dataLakeFileSystemClient, string dataLakeAccountName, string path)
		        {
		            var response = dataLakeFileSystemClient.FileSystem.ListFileStatus(path, dataLakeAccountName, new DataLakeStoreFileSystemListParameters());
		            return response.FileStatuses.FileStatus.ToList();
		        }
		    }
		}


8. Compile y ejecute la aplicación. Siga las indicaciones para ejecutar y completar la aplicación.

## Otras formas de crear una cuenta de Almacén de Data Lake

- [Introducción al Almacén de Data Lake mediante el Portal](data-lake-store-get-started-portal.md)
- [Introducción al Almacén de Azure Data Lake mediante PowerShell](data-lake-store-get-started-powershell.md)
- [Introducción al Almacén de Data Lake mediante la CLI de Azure](data-lake-store-get-started-cli.md)


## Pasos siguientes

- [Protección de los datos en el Almacén de Data Lake](data-lake-store-secure-data.md)
- [Uso de Análisis de Azure Data Lake con el Almacén de Data Lake](data-lake-analytics-get-started-portal.md)
- [Uso de HDInsight de Azure con el Almacén de Data Lake](data-lake-store-hdinsight-hadoop-use-portal.md)

<!---HONumber=AcomDC_0128_2016-->