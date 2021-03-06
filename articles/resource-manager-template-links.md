<properties
   pageTitle="リソースをリンクするためのリソース マネージャー テンプレート | Microsoft Azure"
   description="関連するリソース間のリンクをテンプレートを使ってデプロイするためのリソース マネージャー スキーマを示します。"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/05/2016"
   ms.author="tomfitz"/>

# リソース リンク テンプレート スキーマ

2 つのリソース間のリンクを作成します。リンクは、ソース リソースと呼ばれるリソースに適用されます。リンクの 2 番目のリソースは、ターゲット リソースと呼ばれます。

## スキーマの形式

リンクを作成するには、テンプレートのリソース セクションに次のスキーマを追加します。
    
    {
        "type": enum,
        "apiVersion": "2015-01-01",
        "name": string,
        "dependsOn": [ array values ],
        "properties":
        {
            "targetId": string,
            "notes": string
        }
    }



## 値

次の表では、スキーマに設定する必要がある値について説明します。

| 名前 | 値 |
| ---- | ---- |
| type | 列挙型<br />必須<br />**{namespace}/{type}/providers/links**<br /><br />作成するリソースの種類。{namespace} と {type} の値は、ソース リソースのプロバイダー名前空間とリソース タイプです。 |
| apiVersion | 列挙型<br />必須<br />**2015-01-01**<br /><br />リソースの作成に使用する API バージョン。 |  
| name | 文字列<br />必須<br />**{resouce}/Microsoft.Resources/{linkname}**<br />最大 64 文字。<、>、%、&、?、制御文字を含めることはできません。<br /><br />ソース リソースの名前とリンクの名前の両方を指定する値。 | 
| dependsOn | 配列<br />省略可能<br />リソース名またはリソースの一意識別子のコンマ区切りリスト。<br /><br />このリンクが依存するリソースのコレクション。リンクしているリソースを同じテンプレートでデプロイする場合は、それらのリソースが最初にデプロイされるように、リソース名をこの要素に含めます。| 
| properties | オブジェクト<br />必須<br />[プロパティ オブジェクト](#properties)<br /><br />リンク先のリソースを識別し、リンクに関するメモが記述されたオブジェクト。 | 

<a id="properties" />
### プロパティ オブジェクト

| 名前 | 値 |
| ------- | ---- |
| targetId | 文字列<br />必須<br />**{resource id}**<br /><br />リンク先のターゲット リソースの識別子。 | | notes | 文字列<br />省略可能<br />最大 512 文字<br /><br />ロックの説明。 |


## リンク リソースの使用方法

リソースの依存関係がデプロイ後も続く場合、2 つのリソース間のリンクを適用します。たとえば、あるアプリが別のリソース グループのデータベースに接続する場合があります。そのアプリからデータベースへのリンクを作成することで、その依存関係を定義できます。リンクにより、2 つのリソース間の関係を記録できます。後で、自分や組織の他の人間があるリソースのリンクを問い合わせれば、そのリソースが他のリソースと連動するしくみを理解できます。

リンクされるリソースは、すべて同じサブスクリプションに属する必要があります。各リソースは、そのリソース以外の 50 のリソースにリンクできます。リンクされたリソースを削除または移動する場合、リンクの所有者は、残りのリンクをクリーンアップする必要があります。

REST 経由でリンクを使用する方法については、「[リンク済みリソース](https://msdn.microsoft.com/library/azure/mt238499.aspx)」を参照してください。

次の Azure PowerShell コマンドを使用すると、サブスクリプションのすべてのリンクが表示されます。他のパラメーターを指定し、結果を絞り込むことができます。

    Get-AzureRmResource -ResourceType Microsoft.Resources/links -isCollection -ResourceGroupName <YourResourceGroupName>

## 例

次の例では、Web アプリに読み取り専用のロックが適用されます。

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "hostingPlanName": {
                "type": "string"
            }
        },
        "variables": {
            "siteName": "[concat('site',uniqueString(resourceGroup().id))]"
        },
        "resources": [
            {
                "apiVersion": "2015-08-01",
                "type": "Microsoft.Web/serverfarms",
                "name": "[parameters('hostingPlanName')]",
                "location": "[resourceGroup().location]",
                "sku": {
                    "tier": "Free",
                    "name": "f1",
                    "capacity": 0
                },
                "properties": {
                    "numberOfWorkers": 1
                }
            },
            {
                "apiVersion": "2015-08-01",
                "name": "[variables('siteName')]",
                "type": "Microsoft.Web/sites",
                "location": "[resourceGroup().location]",
                "dependsOn": [ "[parameters('hostingPlanName')]" ],
                "properties": {
                    "serverFarmId": "[parameters('hostingPlanName')]"
                }
            },
            {
                "type": "Microsoft.Web/sites/providers/links",
                "apiVersion": "2015-01-01",
                "name": "[concat(variables('siteName'),'/Microsoft.Resources/SiteToStorage')]",
                "dependsOn": [ "[variables('siteName')]" ],
                "properties": {
                    "targetId": "[resourceId('Microsoft.Storage/storageAccounts','storagecontoso')]",
                    "notes": "This web site uses the storage account to store user information."
                }
    	    }
        ],
        "outputs": {}
    }

## クイック スタート テンプレート

次のクイック スタート テンプレートでは、リンクを使ってリソースがデプロイされます。

- [アラートによってキューに項目を追加するロジック アプリ](https://azure.microsoft.com/documentation/templates/201-alert-to-queue-with-logic-app)
- [アラートによって Slack にメッセージを投稿するロジック アプリ](https://azure.microsoft.com/documentation/templates/201-alert-to-slack-with-logic-app)
- [既存のゲートウェイを使用する API アプリのプロビジョニング](https://azure.microsoft.com/documentation/templates/201-api-app-gateway-existing)
- [新しいゲートウェイを使用する API アプリのプロビジョニング](https://azure.microsoft.com/documentation/templates/201-api-app-gateway-new)
- [テンプレートを使用したロジック アプリと API アプリの作成](https://azure.microsoft.com/documentation/templates/201-logic-app-api-app-create)
- [アラートが発生したときにテキスト メッセージを送信するロジック アプリ](https://azure.microsoft.com/documentation/templates/201-alert-to-text-message-with-logic-app)


## 次のステップ

- テンプレートの構造の詳細については、「[Azure リソース マネージャーのテンプレートの作成](resource-group-authoring-templates.md)」を参照してください。

<!---HONumber=AcomDC_0406_2016-->