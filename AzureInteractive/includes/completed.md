---
title: インクルード ファイル
description: インクルード ファイル
services: functions
author: tdykstra
manager: jeconnoc
ms.service: multiple
ms.topic: include
ms.date: 06/25/2018
ms.author: tdykstra
ms.custom: include file
ms.openlocfilehash: 1c4aaf7afd9fbc54dd34c2cca13a8b74e947c16a
ms.sourcegitcommit: e721422a57e6deb95245135fd9f4f5677c344d93
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/26/2018
ms.locfileid: "40079478"
---
<span data-ttu-id="7c065-103">Azure サービスを使用して、多彩な機能を備えたサーバーレス アプリケーションを作成できました。</span><span class="sxs-lookup"><span data-stu-id="7c065-103">You've successfully created a full-featured, serverless application using Azure services.</span></span>

## <a name="clean-up-resources"></a><span data-ttu-id="7c065-104">リソースのクリーンアップ</span><span class="sxs-lookup"><span data-stu-id="7c065-104">Clean up resources</span></span>

<span data-ttu-id="7c065-105">このアプリケーションの作業が完了したら、次のコマンドを使用して、このチュートリアルで作成したすべてのリソースを削除できます。</span><span class="sxs-lookup"><span data-stu-id="7c065-105">When you are done working with this application, you can use the following command to delete all resources created during the tutorial:</span></span>

```azurecli
az group delete --name first-serverless-app
```

<span data-ttu-id="7c065-106">確認を求められたら「`y`」と入力します。</span><span class="sxs-lookup"><span data-stu-id="7c065-106">Type `y` when prompted.</span></span>  

## <a name="next-steps"></a><span data-ttu-id="7c065-107">次の手順</span><span class="sxs-lookup"><span data-stu-id="7c065-107">Next steps</span></span>

<span data-ttu-id="7c065-108">このチュートリアルで学習した内容は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="7c065-108">In this tutorial, you learned how to:</span></span>
> [!div class="checklist"]
> * <span data-ttu-id="7c065-109">静的な Web サイトとアップロードされた画像がホストされるように Azure Blob Storage を構成する。</span><span class="sxs-lookup"><span data-stu-id="7c065-109">Configure Azure Blob storage to host a static website and uploaded images.</span></span>
> * <span data-ttu-id="7c065-110">Azure Functions を使用して、画像を Azure Blob Storage にアップロードする。</span><span class="sxs-lookup"><span data-stu-id="7c065-110">Upload images to Azure Blob storage using Azure Functions.</span></span>
> * <span data-ttu-id="7c065-111">Azure Functions を使用して、画像のサイズを変更する。</span><span class="sxs-lookup"><span data-stu-id="7c065-111">Resize images using Azure Functions.</span></span>
> * <span data-ttu-id="7c065-112">画像のメタデータを Azure Cosmos DB に格納する。</span><span class="sxs-lookup"><span data-stu-id="7c065-112">Store image metadata in Azure Cosmos DB.</span></span>
> * <span data-ttu-id="7c065-113">Cognitive Services Vision API を使用して、画像のキャプションを自動生成する。</span><span class="sxs-lookup"><span data-stu-id="7c065-113">Use Cognitive Services Vision API to auto-generate image captions.</span></span>
> * <span data-ttu-id="7c065-114">Azure Active Directory を使用してユーザーを認証することにより、Web アプリをセキュリティで保護する。</span><span class="sxs-lookup"><span data-stu-id="7c065-114">Use Azure Active Directory to secure the web app by authenticating users.</span></span>

<span data-ttu-id="7c065-115">さらに多くのサービスを関数に接続する方法については、Logic Apps のチュートリアルに進んでください。</span><span class="sxs-lookup"><span data-stu-id="7c065-115">To learn how to connect even more services to Functions, continue to the Logic Apps tutorial.</span></span> 

> [!div class="nextstepaction"]
> [<span data-ttu-id="7c065-116">Functions と Logic Apps</span><span class="sxs-lookup"><span data-stu-id="7c065-116">Functions with Logic Apps</span></span>](https://docs.microsoft.com/azure/azure-functions/functions-twitter-email)