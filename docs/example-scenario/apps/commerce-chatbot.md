---
title: 用于酒店预订的对话式 Azure 聊天机器人
description: 经验证的解决方案，所生成的对话式聊天机器人适合使用 Azure 机器人服务、认知服务和 LUIS，Azure SQL 数据库以及 Application Insights 的商业应用程序。
author: iainfoulds
ms.date: 07/05/2018
ms.openlocfilehash: 85bdc3194961bbbd8d89db34e5c56e4baa8d8599
ms.sourcegitcommit: 5d99b195388b7cabba383c49a81390ac48f86e8a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/06/2018
ms.locfileid: "37891268"
---
# <a name="conversational-azure-chatbot-for-hotel-reservations"></a><span data-ttu-id="27f9d-103">用于酒店预订的对话式 Azure 聊天机器人</span><span class="sxs-lookup"><span data-stu-id="27f9d-103">Conversational Azure chatbot for hotel reservations</span></span>

<span data-ttu-id="27f9d-104">本示例方案适用于需将对话式聊天机器人集成到应用程序中的企业。</span><span class="sxs-lookup"><span data-stu-id="27f9d-104">This example scenario is applicable to businesses that need integrate a conversational chatbot into applications.</span></span> <span data-ttu-id="27f9d-105">在本解决方案中，将对连锁酒店使用一个 C# 聊天机器人，以便客户通过 Web 或移动应用程序查看房间可用情况并进行预订。</span><span class="sxs-lookup"><span data-stu-id="27f9d-105">In this solution, a C# chatbot is used for a hotel chain that allows customers to check availability and book accommodation through a web or mobile application.</span></span>

<span data-ttu-id="27f9d-106">示例场景包括：允许客户查看酒店房间可用情况并订房、允许客户查看餐馆外卖菜单并下单，或者允许客户搜索并订购照片。</span><span class="sxs-lookup"><span data-stu-id="27f9d-106">Example scenarios include providing a way for customers to view hotel availability and book rooms, review a restaurant take-out menu and place a food order, or search for and order prints of photographs.</span></span> <span data-ttu-id="27f9d-107">传统上，企业需雇佣并培训客户服务代理来响应此类客户请求，而客户则需等待代理为其提供帮助。</span><span class="sxs-lookup"><span data-stu-id="27f9d-107">Traditionally, businesses would need to hire and train customer service agents to respond to these customer requests, and customers would have to wait until a representative is available to provide assistance.</span></span>

<span data-ttu-id="27f9d-108">有了机器人服务和语言理解或语音 API 服务等 Azure 服务，公司就可以使用自动化的可缩放机器人帮助客户并处理订单或预订。</span><span class="sxs-lookup"><span data-stu-id="27f9d-108">By using Azure services such as the Bot Service and Language Understanding or Speech API services, companies can assist customers and process orders or reservations with automated, scalable bots.</span></span>

## <a name="potential-use-cases"></a><span data-ttu-id="27f9d-109">可能的用例</span><span class="sxs-lookup"><span data-stu-id="27f9d-109">Potential use cases</span></span>

<span data-ttu-id="27f9d-110">以下用例可以考虑本解决方案：</span><span class="sxs-lookup"><span data-stu-id="27f9d-110">Consider this solution for the following use cases:</span></span>

* <span data-ttu-id="27f9d-111">查看餐馆外卖菜单并下单</span><span class="sxs-lookup"><span data-stu-id="27f9d-111">View restaurant take-out menu and order food</span></span>
* <span data-ttu-id="27f9d-112">查看酒店房间可用情况并订房</span><span class="sxs-lookup"><span data-stu-id="27f9d-112">Check hotel availability and reserve a room</span></span>
* <span data-ttu-id="27f9d-113">搜索并订购提供的照片</span><span class="sxs-lookup"><span data-stu-id="27f9d-113">Search available photos and order prints</span></span>

## <a name="architecture"></a><span data-ttu-id="27f9d-114">体系结构</span><span class="sxs-lookup"><span data-stu-id="27f9d-114">Architecture</span></span>

![从体系结构的角度概要说明对话式聊天机器人中涉及的 Azure 组件][architecture]

<span data-ttu-id="27f9d-116">本解决方案涉及的对话式聊天机器人充当酒店接待。</span><span class="sxs-lookup"><span data-stu-id="27f9d-116">This solution covers a conversational bot that functions as a concierge for a hotel.</span></span> <span data-ttu-id="27f9d-117">数据流经解决方案的情形如下所示：</span><span class="sxs-lookup"><span data-stu-id="27f9d-117">The data flows through the solution as follows:</span></span>

1. <span data-ttu-id="27f9d-118">客户通过移动或 Web 应用访问此聊天机器人。</span><span class="sxs-lookup"><span data-stu-id="27f9d-118">The customer accesses the chatbot with a mobile or web app.</span></span>
2. <span data-ttu-id="27f9d-119">用户通过 Azure Active Directory B2C（企业对客户）进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="27f9d-119">Using Azure Active Directory B2C (Business 2 Customer), the user is authenticated.</span></span>
3. <span data-ttu-id="27f9d-120">用户与机器人服务交互即可了解酒店房间可用情况。</span><span class="sxs-lookup"><span data-stu-id="27f9d-120">Interacting with the Bot Service, the user requests information about hotel availability.</span></span>
4. <span data-ttu-id="27f9d-121">认知服务通过处理自然语言请求来理解客户通信。</span><span class="sxs-lookup"><span data-stu-id="27f9d-121">Cognitive Services processes the natural language request to understand the customer communication.</span></span>
5. <span data-ttu-id="27f9d-122">用户对结果满意之后，机器人会在 SQL 数据库中添加或更新客户预订项目。</span><span class="sxs-lookup"><span data-stu-id="27f9d-122">After the user is happy with the results, the bot adds or updates the customer’s reservation in a SQL Database.</span></span>
6. <span data-ttu-id="27f9d-123">在此过程中，Application Insights 会一直收集运行时遥测数据，以便 DevOps 团队改进机器人性能和使用效果。</span><span class="sxs-lookup"><span data-stu-id="27f9d-123">Application Insights gathers runtime telemetry throughout the process to help the DevOps team with bot performance and usage.</span></span>

### <a name="components"></a><span data-ttu-id="27f9d-124">组件</span><span class="sxs-lookup"><span data-stu-id="27f9d-124">Components</span></span>

* <span data-ttu-id="27f9d-125">[Azure Active Directory][aad-docs] 是 Microsoft 提供的多租户、基于云的目录和标识管理服务。</span><span class="sxs-lookup"><span data-stu-id="27f9d-125">[Azure Active Directory][aad-docs] is Microsoft’s multi-tenant cloud-based directory and identity management service.</span></span> <span data-ttu-id="27f9d-126">Azure AD 支持使用 B2C 连接器，目的是确定使用外部 ID（例如 Google、Facebook 或 Microsoft 帐户）的用户。</span><span class="sxs-lookup"><span data-stu-id="27f9d-126">Azure AD supports a B2C connector allowing you to identify individuals using external IDs such as Google, Facebook, or a Microsoft Account.</span></span>
* <span data-ttu-id="27f9d-127">[应用服务][appservice-docs]允许你采用所选编程语言生成和托管 Web 应用程序，无需管理基础结构。</span><span class="sxs-lookup"><span data-stu-id="27f9d-127">[App Service][appservice-docs] enables you to build and host web applications in the programming language of your choice without managing infrastructure.</span></span>
* <span data-ttu-id="27f9d-128">[机器人服务][botservice-docs]提供的工具可用于生成、测试、部署和管理智能机器人。</span><span class="sxs-lookup"><span data-stu-id="27f9d-128">[Bot Service][botservice-docs] provides tools to build, test, deploy, and manage intelligent bots.</span></span>
* <span data-ttu-id="27f9d-129">[认知服务][cognitive-docs]允许你使用智能算法，以自然的沟通方式来观察、倾听、表述、了解和解释用户需求。</span><span class="sxs-lookup"><span data-stu-id="27f9d-129">[Cognitive Services][cognitive-docs] lets you use intelligent algorithms to see, hear, speak, understand and interpret your user needs through natural methods of communication.</span></span>
* <span data-ttu-id="27f9d-130">[SQL 数据库][sqldatabase-docs]是一种完全托管的云关系数据库服务，可以与 SQL Server 引擎兼容。</span><span class="sxs-lookup"><span data-stu-id="27f9d-130">[SQL Database][sqldatabase-docs] is a fully managed relational cloud database service that provides SQL Server engine compatibility.</span></span>
* <span data-ttu-id="27f9d-131">[Application Insights][appinsights-docs] 是可扩展的应用程序性能管理 (APM) 服务，用于监视应用程序（例如聊天机器人）的性能。</span><span class="sxs-lookup"><span data-stu-id="27f9d-131">[Application Insights][appinsights-docs] is an extensible Application Performance Management (APM) service that lets you monitor the performance of applications, such as your chatbot.</span></span>

### <a name="alternatives"></a><span data-ttu-id="27f9d-132">备选项</span><span class="sxs-lookup"><span data-stu-id="27f9d-132">Alternatives</span></span>

* <span data-ttu-id="27f9d-133">[Microsoft 语音 API][speech-api] 可以用来更改客户与机器人的交互方式。</span><span class="sxs-lookup"><span data-stu-id="27f9d-133">[Microsoft Speech API][speech-api] can be used to change how customers interface with your bot.</span></span>
* <span data-ttu-id="27f9d-134">[QnA Maker][qna-maker] 可以用来以半结构化内容（例如常见问题解答）的形式为机器人快速添加知识。</span><span class="sxs-lookup"><span data-stu-id="27f9d-134">[QnA Maker][qna-maker] can be used as to quickly add knowledge to your bot from semi-structured content like an FAQ.</span></span>
* <span data-ttu-id="27f9d-135">[文本翻译 API][translator] 是一项可以考虑使用的服务，可以轻松地向机器人添加多语音支持。</span><span class="sxs-lookup"><span data-stu-id="27f9d-135">[Translator Text][translator] is a service that you might consider to easily add multi-lingual support to your bot.</span></span>

## <a name="considerations"></a><span data-ttu-id="27f9d-136">注意事项</span><span class="sxs-lookup"><span data-stu-id="27f9d-136">Considerations</span></span>

### <a name="availability"></a><span data-ttu-id="27f9d-137">可用性</span><span class="sxs-lookup"><span data-stu-id="27f9d-137">Availability</span></span>

<span data-ttu-id="27f9d-138">本解决方案使用 Azure SQL 数据库来存储客户预订项目。</span><span class="sxs-lookup"><span data-stu-id="27f9d-138">This solution uses Azure SQL Database for storing customer reservations.</span></span> <span data-ttu-id="27f9d-139">SQL 数据库包括区域冗余数据库、故障转移组和异地复制。</span><span class="sxs-lookup"><span data-stu-id="27f9d-139">SQL Database includes zone redundant databases, failover groups, and geo-replication.</span></span> <span data-ttu-id="27f9d-140">有关详细信息，请参阅 [Azure SQL 数据库可用性功能][sqlavailability-docs]。</span><span class="sxs-lookup"><span data-stu-id="27f9d-140">For more information, see [Azure SQL Database availability capabilities][sqlavailability-docs].</span></span>

<span data-ttu-id="27f9d-141">若要了解其他可伸缩性主题，请参阅 Azure 体系结构中心的[可用性核对清单][availability]。</span><span class="sxs-lookup"><span data-stu-id="27f9d-141">For other scalability topics, see the [availability checklist][availability] in the Azure Architecture Center.</span></span>

### <a name="scalability"></a><span data-ttu-id="27f9d-142">可伸缩性</span><span class="sxs-lookup"><span data-stu-id="27f9d-142">Scalability</span></span>

<span data-ttu-id="27f9d-143">本解决方案使用 Azure 应用服务。</span><span class="sxs-lookup"><span data-stu-id="27f9d-143">This solution uses Azure App Service.</span></span> <span data-ttu-id="27f9d-144">可以通过应用服务自动调整运行机器人的实例数。</span><span class="sxs-lookup"><span data-stu-id="27f9d-144">With App Service, you can automatically scale the number of instances that run your bot.</span></span> <span data-ttu-id="27f9d-145">可以通过此功能时刻了解客户对 Web 应用程序和聊天机器人的需求情况。</span><span class="sxs-lookup"><span data-stu-id="27f9d-145">This functionality lets you keep up with customer demand for your web application and chatbot.</span></span> <span data-ttu-id="27f9d-146">有关自动缩放的详细信息，请参阅体系结构中心的[自动缩放最佳做法][autoscaling]。</span><span class="sxs-lookup"><span data-stu-id="27f9d-146">For more information on autoscale, see [Autoscaling best practices][autoscaling] in the architecture center.</span></span>

<span data-ttu-id="27f9d-147">若要了解其他可伸缩性主题，请参阅 Azure 体系结构中心的[可伸缩性核对清单][scalability]。</span><span class="sxs-lookup"><span data-stu-id="27f9d-147">For other scalability topics, see the [scalability checklist][scalability] in the Azure Architecture Center.</span></span>

### <a name="security"></a><span data-ttu-id="27f9d-148">“安全”</span><span class="sxs-lookup"><span data-stu-id="27f9d-148">Security</span></span>

<span data-ttu-id="27f9d-149">本解决方案使用 Azure Active Directory B2C（企业对客户）进行用户身份验证。</span><span class="sxs-lookup"><span data-stu-id="27f9d-149">This solution uses Azure Active Directory B2C (Business 2 Consumer) to authenticate users.</span></span> <span data-ttu-id="27f9d-150">使用 AAD B2C 时，聊天机器人不存储任何敏感的客户帐户信息或凭据。</span><span class="sxs-lookup"><span data-stu-id="27f9d-150">With AAD B2C, your chatbot doesn't store any sensitive customer account information or credentials.</span></span> <span data-ttu-id="27f9d-151">有关详细信息，请参阅 [Azure Active Directory B2C 概述][aadb2c-docs]。</span><span class="sxs-lookup"><span data-stu-id="27f9d-151">For more information, see [Azure Active Directory B2C overview][aadb2c-docs].</span></span>

<span data-ttu-id="27f9d-152">存储在 Azure SQL 数据库中的信息使用透明数据加密 (TDE) 进行静态加密。</span><span class="sxs-lookup"><span data-stu-id="27f9d-152">Information stored in Azure SQL Database is encrypted at rest with transparent data encryption (TDE).</span></span> <span data-ttu-id="27f9d-153">SQL 数据库还提供 Always Encrypted，用于在查询和处理过程中加密数据。</span><span class="sxs-lookup"><span data-stu-id="27f9d-153">SQL Database also offers Always Encrypted which encrypts data during querying and processing.</span></span> <span data-ttu-id="27f9d-154">有关 SQL 数据库安全性的详细信息，请参阅 [Azure SQL 数据库安全性和符合性][sqlsecurity-docs]。</span><span class="sxs-lookup"><span data-stu-id="27f9d-154">For more information on SQL Database security, see [Azure SQL Database security and compliance][sqlsecurity-docs].</span></span>

<span data-ttu-id="27f9d-155">若需安全解决方案的通用设计指南，请参阅 [Azure 安全性文档][security]。</span><span class="sxs-lookup"><span data-stu-id="27f9d-155">For general guidance on designing secure solutions, see the [Azure Security Documentation][security].</span></span>

### <a name="resiliency"></a><span data-ttu-id="27f9d-156">复原</span><span class="sxs-lookup"><span data-stu-id="27f9d-156">Resiliency</span></span>

<span data-ttu-id="27f9d-157">本解决方案使用 Azure SQL 数据库来存储客户预订项目。</span><span class="sxs-lookup"><span data-stu-id="27f9d-157">This solution uses Azure SQL Database for storing customer reservations.</span></span> <span data-ttu-id="27f9d-158">SQL 数据库包括区域冗余数据库、故障转移组、异地复制和自动备份。</span><span class="sxs-lookup"><span data-stu-id="27f9d-158">SQL Database includes zone redundant databases, failover groups, geo-replication, and automatic backups.</span></span> <span data-ttu-id="27f9d-159">有了这些功能，应用程序就可以在进行维护或出现服务中断的情况下继续运行。</span><span class="sxs-lookup"><span data-stu-id="27f9d-159">These features allow your application to continue running in the event of a maintenance event or outage.</span></span> <span data-ttu-id="27f9d-160">有关详细信息，请参阅 [Azure SQL 数据库可用性功能][sqlavailability-docs]。</span><span class="sxs-lookup"><span data-stu-id="27f9d-160">For more information, see [Azure SQL Database availability capabilities][sqlavailability-docs].</span></span>

<span data-ttu-id="27f9d-161">本解决方案使用 Application Insights 来监视应用程序的运行状况。</span><span class="sxs-lookup"><span data-stu-id="27f9d-161">To monitor the health of your application, this solution uses Application Insights.</span></span> <span data-ttu-id="27f9d-162">可以使用 Application Insights 生成警报并响应那些会影响客户体验和聊天机器人可用性的性能问题。</span><span class="sxs-lookup"><span data-stu-id="27f9d-162">With Application Insights, you can generate alerts and respond to performance issues that would impact the customer experience and availability of the chatbot.</span></span> <span data-ttu-id="27f9d-163">有关详细信息，请参阅[什么是 Application Insights？][appinsights-docs]</span><span class="sxs-lookup"><span data-stu-id="27f9d-163">For more information, see [What is Application Insights?][appinsights-docs]</span></span>

<span data-ttu-id="27f9d-164">若需可复原解决方案的通用设计指南，请参阅[设计适用于 Azure 的可复原应用程序][resiliency]。</span><span class="sxs-lookup"><span data-stu-id="27f9d-164">For general guidance on designing resilient solutions, see [Designing resilient applications for Azure][resiliency].</span></span>

## <a name="deploy-the-solution"></a><span data-ttu-id="27f9d-165">部署解决方案</span><span class="sxs-lookup"><span data-stu-id="27f9d-165">Deploy the solution</span></span>

<span data-ttu-id="27f9d-166">本解决方案分为三个组件，方便你了解自己最关注的领域：</span><span class="sxs-lookup"><span data-stu-id="27f9d-166">This solution is divided into three components for you to explore areas that you are most focused on:</span></span>

* <span data-ttu-id="27f9d-167">[基础结构组件](#deploy-infrastructure-components)。</span><span class="sxs-lookup"><span data-stu-id="27f9d-167">[Infrastructure components](#deploy-infrastructure-components).</span></span> <span data-ttu-id="27f9d-168">使用 Azure 资源管理器模板来部署应用服务、Web 应用、Application Insights、存储帐户以及 SQL Server 和数据库的核心基础结构组件。</span><span class="sxs-lookup"><span data-stu-id="27f9d-168">Use an Azure Resource Manger template to deploy the core infrastructure components of an App Service, Web App, Application Insights, Storage account, and SQL Server and database.</span></span>
* <span data-ttu-id="27f9d-169">[Web 应用聊天机器人](#deploy-web-app-chatbot)。</span><span class="sxs-lookup"><span data-stu-id="27f9d-169">[Web App Chatbot](#deploy-web-app-chatbot).</span></span> <span data-ttu-id="27f9d-170">使用 Azure CLI 将机器人与机器人服务、语言理解和智能服务 (LUIS) 应用部署在一起。</span><span class="sxs-lookup"><span data-stu-id="27f9d-170">Use the Azure CLI to deploy a bot with the Bot Service and Language Understanding and Intelligent Services (LUIS) app.</span></span>
* <span data-ttu-id="27f9d-171">[C# 聊天机器人应用程序示例](#deploy-chatbot-c-application-code)。</span><span class="sxs-lookup"><span data-stu-id="27f9d-171">[Sample C# chatbot application](#deploy-chatbot-c-application-code).</span></span> <span data-ttu-id="27f9d-172">使用 Visual Studio 复查示例性的酒店预订 C# 应用程序代码并将其部署到 Azure 中的机器人。</span><span class="sxs-lookup"><span data-stu-id="27f9d-172">Use Visual Studio to review the sample hotel reservation C# application code and deploy to a bot in Azure.</span></span>

<span data-ttu-id="27f9d-173">**先决条件：**</span><span class="sxs-lookup"><span data-stu-id="27f9d-173">**Prerequisites.**</span></span> <span data-ttu-id="27f9d-174">必须已经有 Azure 帐户。</span><span class="sxs-lookup"><span data-stu-id="27f9d-174">You must have an existing Azure account.</span></span> <span data-ttu-id="27f9d-175">如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。</span><span class="sxs-lookup"><span data-stu-id="27f9d-175">If you don't have an Azure subscription, create a [free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) before you begin.</span></span>

### <a name="deploy-infrastructure-components"></a><span data-ttu-id="27f9d-176">部署基础结构组件</span><span class="sxs-lookup"><span data-stu-id="27f9d-176">Deploy infrastructure components</span></span>

<span data-ttu-id="27f9d-177">若要通过 Azure 资源管理器模板部署基础结构组件，请执行以下步骤。</span><span class="sxs-lookup"><span data-stu-id="27f9d-177">To deploy the infrastructure components with an Azure Resource Manager template, perform the following steps.</span></span>

1. <span data-ttu-id="27f9d-178">单击“部署到 Azure”按钮：</span><span class="sxs-lookup"><span data-stu-id="27f9d-178">Click the **Deploy to Azure** button:</span></span><br><a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmspnp%2Fsolution-architectures%2Fmaster%2Fapps%2Fcommerce-chatbot.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"/></a>
2. <span data-ttu-id="27f9d-179">等待模板部署在 Azure 门户中打开，然后完成以下步骤：</span><span class="sxs-lookup"><span data-stu-id="27f9d-179">Wait for the template deployment to open in the Azure portal, then complete the following steps:</span></span>
   * <span data-ttu-id="27f9d-180">选择“新建”资源组，然后在文本框中提供一个名称，例如 *myCommerceChatBotInfrastructure*。</span><span class="sxs-lookup"><span data-stu-id="27f9d-180">Choose to **Create new** resource group, then provide a name such as *myCommerceChatBotInfrastructure* in the text box.</span></span>
   * <span data-ttu-id="27f9d-181">从“位置”下拉框中选择区域。</span><span class="sxs-lookup"><span data-stu-id="27f9d-181">Select a region from the **Location** drop-down box.</span></span>
   * <span data-ttu-id="27f9d-182">提供用于 SQL Server 管理员帐户的用户名和安全密码。</span><span class="sxs-lookup"><span data-stu-id="27f9d-182">Provide a username and secure password for the SQL Server administrator account.</span></span>
   * <span data-ttu-id="27f9d-183">查看条款和条件，然后勾选“我同意上述条款和条件”。</span><span class="sxs-lookup"><span data-stu-id="27f9d-183">Review the terms and conditions, then check **I agree to the terms and conditions stated above**.</span></span>
   * <span data-ttu-id="27f9d-184">选择“购买”按钮。</span><span class="sxs-lookup"><span data-stu-id="27f9d-184">Select the **Purchase** button.</span></span>

<span data-ttu-id="27f9d-185">需要几分钟才能完成部署。</span><span class="sxs-lookup"><span data-stu-id="27f9d-185">It takes a few minutes for the deployment to complete.</span></span>

### <a name="deploy-web-app-chatbot"></a><span data-ttu-id="27f9d-186">部署 Web 应用聊天机器人</span><span class="sxs-lookup"><span data-stu-id="27f9d-186">Deploy Web App chatbot</span></span>

<span data-ttu-id="27f9d-187">若要创建此聊天机器人，请使用 Azure CLI。</span><span class="sxs-lookup"><span data-stu-id="27f9d-187">To create the chatbot, use the Azure CLI.</span></span> <span data-ttu-id="27f9d-188">以下示例先安装机器人服务的 CLI 扩展，接着创建资源组，然后部署使用 Application Insights 的机器人。</span><span class="sxs-lookup"><span data-stu-id="27f9d-188">The following example installs the CLI extension for Bot Service, creates a resource group, then deploys a bot that uses Application Insights.</span></span> <span data-ttu-id="27f9d-189">系统提示时，请验证 Microsoft 帐户，并允许机器人自行注册到机器人服务以及语言理解和智能服务 (LUIS) 应用。</span><span class="sxs-lookup"><span data-stu-id="27f9d-189">When prompted, authenticate your Microsoft account and allow the bot to register itself with the Bot Service and Language Understanding and Intelligent Services (LUIS) app.</span></span>

```azurecli-interactive
# Install the Azure CLI extension for the Bot Service
az extension add --name botservice --yes

# Create a resource group
az group create --name myCommerceChatbot --location eastus

# Create a Web App Chatbot that uses Application Insights
az bot create \
    --resource-group myCommerceChatbot \
    --name commerceChatbot \
    --location eastus \
    --kind webapp \
    --sku S1 \
    --insights eastus
```

### <a name="deploy-chatbot-c-application-code"></a><span data-ttu-id="27f9d-190">部署聊天机器人 C# 应用程序代码</span><span class="sxs-lookup"><span data-stu-id="27f9d-190">Deploy chatbot C# application code</span></span>

<span data-ttu-id="27f9d-191">GitHub 上提供了一个示例 C# 应用程序：</span><span class="sxs-lookup"><span data-stu-id="27f9d-191">A sample C# application is available on GitHub:</span></span> 

* [<span data-ttu-id="27f9d-192">商业机器人 C# 示例</span><span class="sxs-lookup"><span data-stu-id="27f9d-192">Commerce Bot C# sample</span></span>](https://github.com/Microsoft/AzureBotServices-scenarios/tree/master/CSharp/Commerce/src)

<span data-ttu-id="27f9d-193">示例应用程序包括 Azure Active Directory 身份验证组件，并集成了认知服务的语言理解和智能服务 (LUIS) 组件。</span><span class="sxs-lookup"><span data-stu-id="27f9d-193">The sample application includes the Azure Active Directory authentication components and integration with the Language Understanding and Intelligent Services (LUIS) component of Cognitive Services.</span></span> <span data-ttu-id="27f9d-194">此应用程序要求使用 Visual Studio 来生成和部署解决方案。</span><span class="sxs-lookup"><span data-stu-id="27f9d-194">The application requires Visual Studio to build and deploy the solution.</span></span> <span data-ttu-id="27f9d-195">有关如何配置 AAD B2C 和 LUIS 应用的其他信息，可参阅 GitHub 存储库文档。</span><span class="sxs-lookup"><span data-stu-id="27f9d-195">Additional information on configuring AAD B2C and the LUIS app can be found in the GitHub repo documentation.</span></span>

## <a name="pricing"></a><span data-ttu-id="27f9d-196">定价</span><span class="sxs-lookup"><span data-stu-id="27f9d-196">Pricing</span></span>

<span data-ttu-id="27f9d-197">为了方便用户了解运行本解决方案的成本，我们已在成本计算器中预配置了所有服务。</span><span class="sxs-lookup"><span data-stu-id="27f9d-197">To explore the cost of running this solution, all of the services are pre-configured in the cost calculator.</span></span> <span data-ttu-id="27f9d-198">若要了解自己的特定用例的定价变化情况，请按预期的流量更改相应的变量。</span><span class="sxs-lookup"><span data-stu-id="27f9d-198">To see how the pricing would change for your particular use case, change the appropriate variables to match your expected traffic.</span></span>

<span data-ttu-id="27f9d-199">我们已根据你预期的聊天机器人需要处理的消息数提供了三个示例性的成本配置文件：</span><span class="sxs-lookup"><span data-stu-id="27f9d-199">We have provided three sample cost profiles based on the amount of messages you expect your chatbot to process:</span></span>

* <span data-ttu-id="27f9d-200">[小][small-pricing]：对应于每月处理的消息数 < 10,000 的情况。</span><span class="sxs-lookup"><span data-stu-id="27f9d-200">[Small][small-pricing]: this correlates to processing < 10,000 messages per month.</span></span>
* <span data-ttu-id="27f9d-201">[中][medium-pricing]：对应于每月处理的消息数 < 500,000 的情况。</span><span class="sxs-lookup"><span data-stu-id="27f9d-201">[Medium][medium-pricing]: this correlates to processing < 500,000 messages per month.</span></span>
* <span data-ttu-id="27f9d-202">[大][large-pricing]：对应于每月处理的消息数 < 1 千万的情况。</span><span class="sxs-lookup"><span data-stu-id="27f9d-202">[Large][large-pricing]: this correlates to processing < 10 million messages per month.</span></span>

## <a name="related-resources"></a><span data-ttu-id="27f9d-203">相关资源</span><span class="sxs-lookup"><span data-stu-id="27f9d-203">Related Resources</span></span>

<span data-ttu-id="27f9d-204">如需一系列介绍如何利用 Azure 机器人服务的引导式教程，请参阅文档的[教程节点][botservice-docs]。</span><span class="sxs-lookup"><span data-stu-id="27f9d-204">For a set of guided tutorials on leveraging the Azure Bot Service, see the [tutorial node][botservice-docs] of the documentation.</span></span>

<!-- links -->
[aadb2c-docs]: /azure/active-directory-b2c/active-directory-b2c-overview
[aad-docs]: /azure/active-directory/
[appinsights-docs]: /azure/application-insights/app-insights-overview
[appservice-docs]: /azure/app-service/
[architecture]: ./media/commerce-chatbot/architecture-commerce-chatbot.png
[autoscaling]: ../../best-practices/auto-scaling.md
[availability]: ../../checklist/availability.md
[botservice-docs]: /azure/bot-service/
[cognitive-docs]: /azure/cognitive-services/
[resiliency]: ../../resiliency/index.md
[resource-groups]: /azure/azure-resource-manager/resource-group-overview
[security]: /azure/security/
[scalability]: ../../checklist/scalability.md
[sqlavailability-docs]: /azure/sql-database/sql-database-technical-overview#availability-capabilities
[sqldatabase-docs]: /azure/sql-database/
[sqlsecurity-docs]: /azure/sql-database/sql-database-technical-overview#advanced-security-and-compliance
[qna-maker]: /azure/cognitive-services/QnAMaker/Overview/overview
[speech-api]: /azure/cognitive-services/speech/home
[translator]: /azure/cognitive-services/translator/translator-info-overview

[small-pricing]: https://azure.com/e/dce05b6184904c50b38e1a8654f726b6
[medium-pricing]: https://azure.com/e/304d17106afc480dbc414f9726078a03
[large-pricing]: https://azure.com/e/8319dd5e5e3d4f118f9029e32a80e887