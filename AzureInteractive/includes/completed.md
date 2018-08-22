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
Azure サービスを使用して、多彩な機能を備えたサーバーレス アプリケーションを作成できました。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このアプリケーションの作業が完了したら、次のコマンドを使用して、このチュートリアルで作成したすべてのリソースを削除できます。

```azurecli
az group delete --name first-serverless-app
```

確認を求められたら「`y`」と入力します。  

## <a name="next-steps"></a>次の手順

このチュートリアルで学習した内容は次のとおりです。
> [!div class="checklist"]
> * 静的な Web サイトとアップロードされた画像がホストされるように Azure Blob Storage を構成する。
> * Azure Functions を使用して、画像を Azure Blob Storage にアップロードする。
> * Azure Functions を使用して、画像のサイズを変更する。
> * 画像のメタデータを Azure Cosmos DB に格納する。
> * Cognitive Services Vision API を使用して、画像のキャプションを自動生成する。
> * Azure Active Directory を使用してユーザーを認証することにより、Web アプリをセキュリティで保護する。

さらに多くのサービスを関数に接続する方法については、Logic Apps のチュートリアルに進んでください。 

> [!div class="nextstepaction"]
> [Functions と Logic Apps](https://docs.microsoft.com/azure/azure-functions/functions-twitter-email)