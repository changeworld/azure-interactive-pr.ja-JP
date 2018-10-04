---
title: インクルード ファイル
description: インクルード ファイル
services: functions
author: ggailey777
manager: jeconnoc
ms.service: multiple
ms.topic: include
ms.date: 06/25/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: a66fcc3a406c79fcf9881ddaaaf8330f5b373043
ms.sourcegitcommit: 81587470a181e314242c7a97cd0f91c82d4fe232
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2018
ms.locfileid: "47460001"
---
Azure サービスを使用して、多彩な機能を備えたサーバーレス アプリケーションを作成できました。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このアプリケーションの作業が完了したら、次のコマンドを使用して、このチュートリアルで作成したすべてのリソースを削除できます。

```azurecli
az group delete --name first-serverless-app
```

確認を求められたら `y` と入力します。  

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。
> [!div class="checklist"]
> * 静的な Web サイトとアップロードされた画像がホストされるように Azure Blob Storage を構成する。
> * Azure Functions を使用して、画像を Azure Blob Storage にアップロードします。
> * Azure Functions を使用して、画像のサイズを変更します。
> * 画像のメタデータを Azure Cosmos DB に格納します。
> * Cognitive Services Vision API を使用して、画像のキャプションを自動生成します。
> * Azure Active Directory を使用してユーザーを認証することにより、Web アプリをセキュリティで保護する。

さらに多くのサービスを関数に接続する方法については、Logic Apps のチュートリアルに進んでください。 

> [!div class="nextstepaction"]
> [Functions と Logic Apps](https://docs.microsoft.com/azure/azure-functions/functions-twitter-email)
