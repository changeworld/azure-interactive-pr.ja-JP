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
このチュートリアルでは、HTML ベースのユーザー インターフェイスを提供するシンプルな Web アプリケーションをデプロイします。サーバーレス バックエンドにより、アプリケーションで画像をアップロードし、その画像について説明するキャプションを自動的に生成できます。

![Web アプリの実行](media/functions-first-serverless-web-app/0-app-screenshot-finished.png)

次の図は、アプリケーションで使用されている Azure サービスを示しています。

1. Blob Storage は静的 Web コンテンツ (HTML、CSS、JS) を配信し、画像を格納します。
2. Azure Functions は、画像のアップロード、サイズ変更、およびメタデータ ストレージを管理します。
3. Cosmos DB は、画像のメタデータを格納します。
4. Logic Apps は Computer Vision API から画像のキャプションを取得します。
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
