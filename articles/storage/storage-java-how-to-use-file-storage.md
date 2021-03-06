---
title: "如何通过 Java 使用文件存储 | Microsoft Docs"
description: "了解如何使用 Azure 文件服务上传、下载、列出和删除文件。 用 Java 编写的示例。"
services: storage
documentationcenter: java
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 3bfbfa7f-d378-4fb4-8df3-e0b6fcea5b27
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 12/08/2016
ms.author: robinsh
ms.translationtype: Human Translation
ms.sourcegitcommit: 33630644e2b3b6565d009276145ecf220802cc63
ms.openlocfilehash: 49b35ff1b82f5384b105d99ce95773648a11f6f4
ms.contentlocale: zh-cn
ms.lasthandoff: 07/06/2017


---
# <a name="how-to-use-file-storage-from-java"></a>如何通过 Java 使用文件存储
[!INCLUDE [storage-selector-file-include](../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-check-out-samples-java](../../includes/storage-check-out-samples-java.md)]

## <a name="overview"></a>概述
本指南将介绍如何针对 Microsoft Azure 文件存储服务执行基本的操作。 通过以 Java 编写的示例，学习如何创建共享和目录，以及如何上传、列出和删除文件。 如果你不熟悉 Microsoft Azure 的文件存储服务，则若要了解这些示例，你需要学习后续部分的概念。

[!INCLUDE [storage-file-concepts-include](../../includes/storage-file-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-java-application"></a>创建 Java 应用程序
若要生成示例，你需要 Java 开发工具包 (JDK) 和 [Azure Storage SDK for Java][]。 此外，你应该已经创建了一个 Azure 存储帐户。

## <a name="setup-your-application-to-use-file-storage"></a>设置你的应用程序以使用文件存储
若要使用 Azure 存储 API，请将下列语句添加到要通过其来访问存储服务的 Java 文件的顶部：

```java
// Include the following imports to use blob APIs.
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.file.*;
```

## <a name="setup-an-azure-storage-connection-string"></a>设置 Azure 存储连接字符串
若要使用文件存储，你需要连接到你的 Azure 存储帐户。 第一步是配置连接字符串，我们将使用该字符串连接到你的存储帐户。 为此，我们需要定义一个静态变量。

```java
// Configure the connection-string with your values
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account_name;" +
    "AccountKey=your_storage_account_key";
```

> [!NOTE]
> 将 your_storage_account_name 和 your_storage_account_key 替换为你的存储帐户的实际值。
> 
> 

## <a name="connecting-to-an-azure-storage-account"></a>连接到 Azure 存储帐户
若要连接到你的存储帐户，则需要使用 **CloudStorageAccount** 对象，以便将连接字符串传递到 **parse** 方法。

```java
// Use the CloudStorageAccount object to connect to your storage account
try {
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);
} catch (InvalidKeyException invalidKey) {
    // Handle the exception
}
```

**CloudStorageAccount.parse** 会引发 InvalidKeyException，因此需将其置于 try/catch 块内。

## <a name="how-to-create-a-share"></a>如何：创建共享
文件存储中的所有文件和目录都位于名为 **Share** 的容器内。 你的存储帐户可以拥有无数的共享，只要你的帐户容量允许。 若要获得共享及其内容的访问权限，你需要使用文件存储客户端。

```java
// Create the file storage client.
CloudFileClient fileClient = storageAccount.createCloudFileClient();
```

使用文件存储客户端以后，你就可以获得对共享的引用。

```java
// Get a reference to the file share
CloudFileShare share = fileClient.getShareReference("sampleshare");
```

实际创建共享时，请使用 CloudFileShare 对象的 **createIfNotExists**方法。

```java
if (share.createIfNotExists()) {
    System.out.println("New share created");
}
```

而在目前，**share** 保留对名为 **sampleshare** 的共享的引用。

## <a name="how-to-upload-a-file"></a>如何：上传文件
Azure 文件存储共享至少包含文件所在的根目录。 在本部分，你将学习如何将文件从本地存储上传到共享所在的根目录。

上传文件的第一步是获取对文件所在的目录的引用。 为此，需要调用共享对象的 **getRootDirectoryReference** 方法。

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();
```

现在，你已经有了共享所在的根目录的引用，因此可以使用以下代码来上传文件。

```java
        // Define the path to a local file.
        final String filePath = "C:\\temp\\Readme.txt";
    
        CloudFile cloudFile = rootDir.getFileReference("Readme.txt");
        cloudFile.uploadFromFile(filePath);
```

## <a name="how-to-create-a-directory"></a>如何：创建目录
你也可以将文件置于子目录中，不必将其全部置于根目录中，以便对存储进行有效的组织。 Azure 文件存储服务可以创建任意数目的目录，只要你的帐户允许。 以下代码将在根目录下创建名为 **sampledir** 的子目录。

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

//Get a reference to the sampledir directory
CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

if (sampleDir.createIfNotExists()) {
    System.out.println("sampledir created");
} else {
    System.out.println("sampledir already exists");
}
```

## <a name="how-to-list-files-and-directories-in-a-share"></a>如何：列出共享中的文件和目录
可以轻松获取共享中文件和目录的列表，只需针对 CloudFileDirectory 引用调用 **listFilesAndDirectories** 即可。 该方法将返回你可以对其进行循环访问的 ListFileItem 对象的列表。 例如，下面的代码将列出根目录中的文件和目录。

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

for ( ListFileItem fileItem : rootDir.listFilesAndDirectories() ) {
    System.out.println(fileItem.getUri());
}
```

## <a name="how-to-download-a-file"></a>如何：下载文件
对于文件存储，另一项需要更频繁执行的操作是下载文件。 在下面的示例中，代码会下载 SampleFile.txt 并显示其内容。

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

//Get a reference to the directory that contains the file
CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

//Get a reference to the file you want to download
CloudFile file = sampleDir.getFileReference("SampleFile.txt");

//Write the contents of the file to the console.
System.out.println(file.downloadText());
```

## <a name="how-to-delete-a-file"></a>如何：删除文件
另一项常见的文件存储操作是删除文件。 下面的代码会删除名为 SampleFile.txt 的文件，该文件存储在名为 **sampledir** 的目录中。

```java
// Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

// Get a reference to the directory where the file to be deleted is in
CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");

String filename = "SampleFile.txt"
CloudFile file;

file = containerDir.getFileReference(filename)
if ( file.deleteIfExists() ) {
    System.out.println(filename + " was deleted");
}
```

## <a name="how-to-delete-a-directory"></a>如何：删除目录
删除目录相当简单，但需注意的是，你不能删除仍然包含有文件或其他目录的目录。

```java
// Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

// Get a reference to the directory you want to delete
CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");

// Delete the directory
if ( containerDir.deleteIfExists() ) {
    System.out.println("Directory deleted");
}
```

## <a name="how-to-delete-a-share"></a>如何：删除共享
删除共享时，可针对 CloudFileShare 对象调用 **deleteIfExists** 方法。 以下是具有此类功能的示例代码。

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    // Create the file client.
   CloudFileClient fileClient = storageAccount.createCloudFileClient();

   // Get a reference to the file share
   CloudFileShare share = fileClient.getShareReference("sampleshare");

   if (share.deleteIfExists()) {
       System.out.println("sampleshare deleted");
   }
} catch (Exception e) {
    e.printStackTrace();
}
```

## <a name="next-steps"></a>后续步骤
如果你还想更多地了解其他 Azure 存储 API，请点击以下链接。

* [Java 开发中心](http://azure.microsoft.com/develop/java/)
* [用于 Java 的 Azure 存储 SDK](https://github.com/azure/azure-storage-java)
* [用于 Android 的 Azure 存储 SDK](https://github.com/azure/azure-storage-android)
* [Azure 存储客户端 SDK 参考](http://dl.windowsazure.com/storage/javadoc/)
* [Azure 存储服务 REST API](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Azure 存储团队博客](http://blogs.msdn.com/b/windowsazurestorage/)
* [使用 AzCopy 命令行实用程序传输数据](storage-use-azcopy.md)


