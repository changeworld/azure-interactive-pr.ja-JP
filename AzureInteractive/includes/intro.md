---
title: インクルード ファイル
description: インクルード ファイル
services: functions
author: ggailey777
manager: jeconnoc
ms.service: multiple
ms.topic: include
ms.date: 06/21/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 0f86f2698a3a0c1e20272c335b63faf03b4b92d6
ms.sourcegitcommit: 81587470a181e314242c7a97cd0f91c82d4fe232
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2018
ms.locfileid: "47460026"
---
このチュートリアルでは、HTML ベースのユーザー インターフェイスを表示するシンプルな Web アプリケーションをデプロイします。 サーバーレス バックエンドにより、アプリケーションで画像をアップロードし、その画像について説明するキャプションを自動的に取得できます。

![Web アプリの実行](media/functions-first-serverless-web-app/0-app-screenshot-finished.png)

次の図は、アプリケーションによって使用されている Azure サービスを示しています。

1. Blob Storage は静的 Web コンテンツ (HTML、CSS、JS) を提供し、画像が格納されます。
2. Azure Functions によって、画像のアップロード、サイズ変更、およびメタデータ ストレージが管理されます。
3. Cosmos DB には、画像のメタデータが格納されます。
4. Logic Apps により、Computer Vision API から画像のキャプションが取得されます。
5. Azure Active Directory はユーザー認証を管理します。

![ソリューションのアーキテクチャ図](media/functions-first-serverless-web-app/0-architecture.jpg)

このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
> * 静的 Web サイトとアップロードされた画像がホストされるように Azure Blob Storage を構成します。
> * Azure Functions を使用して、画像を Azure Blob Storage にアップロードします。
> * Azure Functions を使用して、画像のサイズを変更します。
> * 画像のメタデータを Azure Cosmos DB に格納します。
> * Cognitive Services Vision API を使用して、画像のキャプションを自動生成します。
> * Azure Active Directory を使用して、ユーザーを認証することにより、Web アプリをセキュリティで保護します。
