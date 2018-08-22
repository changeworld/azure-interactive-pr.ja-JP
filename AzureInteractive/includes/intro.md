---
title: インクルード ファイル
description: インクルード ファイル
services: functions
author: tdykstra
manager: jeconnoc
ms.service: multiple
ms.topic: include
ms.date: 06/21/2018
ms.author: tdykstra
ms.custom: include file
ms.openlocfilehash: d651fd3d03f2678d625e60f9ab1e9f59e623964f
ms.sourcegitcommit: e721422a57e6deb95245135fd9f4f5677c344d93
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/26/2018
ms.locfileid: "40079459"
---
<span data-ttu-id="b0ec0-103">このチュートリアルでは、HTML ベースのユーザー インターフェイスを表示するシンプルな Web アプリケーションをデプロイします。</span><span class="sxs-lookup"><span data-stu-id="b0ec0-103">In this tutorial, you deploy a simple web application that presents an HTML-based user interface.</span></span> <span data-ttu-id="b0ec0-104">サーバーレス バックエンドにより、アプリケーションで画像をアップロードし、その画像について説明するキャプションを自動的に取得できます。</span><span class="sxs-lookup"><span data-stu-id="b0ec0-104">A serverless backend enables the application to upload images and automatically get captions describing them.</span></span>

![Web アプリの実行](media/functions-first-serverless-web-app/0-app-screenshot-finished.png)

<span data-ttu-id="b0ec0-106">次の図は、アプリケーションによって使用されている Azure サービスを示しています。</span><span class="sxs-lookup"><span data-stu-id="b0ec0-106">The following diagram shows the Azure services used by the application:</span></span>

1. <span data-ttu-id="b0ec0-107">Blob Storage は静的 Web コンテンツ (HTML、CSS、JS) を提供し、画像が格納されます。</span><span class="sxs-lookup"><span data-stu-id="b0ec0-107">Blob Storage serves static web content (HTML, CSS, JS) and stores images.</span></span>
2. <span data-ttu-id="b0ec0-108">Azure Functions によって、画像のアップロード、サイズ変更、およびメタデータ ストレージが管理されます。</span><span class="sxs-lookup"><span data-stu-id="b0ec0-108">Azure Functions manages image uploads, resizing, and metadata storage.</span></span>
3. <span data-ttu-id="b0ec0-109">Cosmos DB には、画像のメタデータが格納されます。</span><span class="sxs-lookup"><span data-stu-id="b0ec0-109">Cosmos DB stores image metadata.</span></span>
4. <span data-ttu-id="b0ec0-110">Logic Apps により、Computer Vision API から画像のキャプションが取得されます。</span><span class="sxs-lookup"><span data-stu-id="b0ec0-110">Logic Apps gets image captions from Computer Vision API.</span></span>
5. <span data-ttu-id="b0ec0-111">Azure Active Directory によりユーザー認証が管理されます。</span><span class="sxs-lookup"><span data-stu-id="b0ec0-111">Azure Active Directory manages user authentication.</span></span>

![ソリューションのアーキテクチャ図](media/functions-first-serverless-web-app/0-architecture.jpg)

<span data-ttu-id="b0ec0-113">このチュートリアルで学習する内容は次のとおりです。</span><span class="sxs-lookup"><span data-stu-id="b0ec0-113">In this tutorial, you learn how to:</span></span>
> [!div class="checklist"]
> * <span data-ttu-id="b0ec0-114">静的な Web サイトとアップロードされた画像がホストされるように Azure Blob Storage を構成します。</span><span class="sxs-lookup"><span data-stu-id="b0ec0-114">Configure Azure Blob storage to host a static website and uploaded images.</span></span>
> * <span data-ttu-id="b0ec0-115">Azure Functions を使用して、画像を Azure Blob Storage にアップロードします。</span><span class="sxs-lookup"><span data-stu-id="b0ec0-115">Upload images to Azure Blob storage using Azure Functions.</span></span>
> * <span data-ttu-id="b0ec0-116">Azure Functions を使用して、画像のサイズを変更します。</span><span class="sxs-lookup"><span data-stu-id="b0ec0-116">Resize images using Azure Functions.</span></span>
> * <span data-ttu-id="b0ec0-117">画像のメタデータを Azure Cosmos DB に格納します。</span><span class="sxs-lookup"><span data-stu-id="b0ec0-117">Store image metadata in Azure Cosmos DB.</span></span>
> * <span data-ttu-id="b0ec0-118">Cognitive Services Vision API を使用して、画像のキャプションを自動生成します。</span><span class="sxs-lookup"><span data-stu-id="b0ec0-118">Use Cognitive Services Vision API to auto-generate image captions.</span></span>
> * <span data-ttu-id="b0ec0-119">Azure Active Directory を使用して、ユーザーを認証することにより、Web アプリをセキュリティで保護します。</span><span class="sxs-lookup"><span data-stu-id="b0ec0-119">Use Azure Active Directory to secure the web app by authenticating users.</span></span>