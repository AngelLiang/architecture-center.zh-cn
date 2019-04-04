---
title: Azure 参考体系结构
description: 适用于 Azure 上的常见工作负荷的参考体系结构和实现指南。
layout: LandingPage
ms.topic: landing-page
ms.date: 04/02/2019
ms.openlocfilehash: 7c307c40a710fff7ff07e24570052411b7edd8bf
ms.sourcegitcommit: 1a3cc91530d56731029ea091db1f15d41ac056af
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2019
ms.locfileid: "58887754"
---
<!-- This file is generated! -->
<!-- See the templates in ./build/reference-architectures  -->
<!-- See data in index.json -->

# <a name="azure-reference-architectures"></a>Azure 参考体系结构

我们的参考体系结构是按应用场景安排的。 每个体系结构包括建议的做法，以及有关可伸缩性、可用性、可管理性和安全性的注意事项。 大部分还包括可部署的解决方案或参考实现。

跳转到：[AI](#ai-and-machine-learning) | [大数据](#big-data-solutions) | [IoT](#internet-of-things) | [微服务](#microservices) | [无服务器](#serverless-applications) | [虚拟网络](#virtual-networks) | [VM 工作负荷](#vm-workloads) | [SAP](#sap) | [Active Directory](#extend-on-premises-active-directory-to-azure) | [Web 应用](#web-applications)

<!-- markdownlint-disable MD033 -->

## <a name="ai-and-machine-learning"></a>AI 和机器学习

<ul  class="panelContent cardsF">
<!-- Distributed training of deep learning models -->
<li style="display: flex; flex-direction: column;">
    <a href="./ai/training-deep-learning.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/batch-ai.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>深度学习模型的分布式训练</h3>
                        <p>在已启用 GPU 的 VM 的各群集上运行深度学习模型的分布式训练。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<!-- Batch scoring for deep learning models -->
<li style="display: flex; flex-direction: column;">
    <a href="./ai/batch-scoring-deep-learning.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/batch-ai.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>针对深度学习模型的批处理计分</h3>
                        <p>自动运行将神经风格转换应用到视频的批处理作业。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<!-- Training of Python scikit-learn models -->
<li style="display: flex; flex-direction: column;">
    <a href="./ai/training-python-models.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/python-powered-h.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Python scikit-learn 模型的训练</h3>
                        <p>建议用于优化 scikit-learn Python 模型超参数的做法。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<!-- Batch scoring of Python models -->
<li style="display: flex; flex-direction: column;">
    <a href="./ai/batch-scoring-python.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/python-powered-h.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Python 模型的批量评分</h3>
                        <p>使用 Azure 机器学习，按计划以并行方式对许多 Python 模型进行批量评分。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<!-- Real-time scoring of Python and deep learning models -->
<li style="display: flex; flex-direction: column;">
    <a href="./ai/realtime-scoring-python.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/python-powered-h.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Python 和深度学习模型的实时评分</h3>
                        <p>将 Python 模型部署为 Web 服务，以便使用常规 Python 模型或深度学习模型进行实时预测。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<!-- Batch scoring of R machine learning models -->
<li style="display: flex; flex-direction: column;">
    <a href="./ai/batch-scoring-R-models.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/logo-r.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>R 机器学习模型的批量评分</h3>
                        <p>使用 Azure Batch 执行 R 模型的批量评分。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<!-- Real-time scoring of R machine learning models -->
<li style="display: flex; flex-direction: column;">
    <a href="./ai/realtime-scoring-r.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/logo-r.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>R 机器学习模型的实时评分</h3>
                        <p>在 R 中使用运行于 Azure Kubernetes 服务 (AKS) 中的 Microsoft Machine Learning Server 实现实时预测服务。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<!-- Batch scoring of Spark models on Azure Databricks -->
<li style="display: flex; flex-direction: column;">
    <a href="./ai/batch-scoring-databricks.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/databricks.png" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Azure Databricks 上 Spark 模型的批量评分</h3>
                        <p>构建可缩放的解决方案，以用于使用 Azure Databricks 对 Apache Spark 分类模型进行批量评分。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<!-- Real-time recommendation API -->
<li style="display: flex; flex-direction: column;">
    <a href="./ai/real-time-recommendation.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/machine-learning.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>实时建议 API</h3>
                        <p>使用 Azure Databricks 训练一个建议模型，并使用 Azure 机器学习将其作为 API 部署。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<!-- Enterprise-grade conversational bot -->
<li style="display: flex; flex-direction: column;">
    <a href="./ai/conversational-bot.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/bot-services.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>企业级聊天机器人</h3>
                        <p>如何使用 Azure Bot Framework 生成企业级聊天机器人。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
</ul>

## <a name="big-data-solutions"></a>大数据解决方案

<ul  class="panelContent cardsF">
<!-- Enterprise BI with SQL Data Warehouse -->
<li style="display: flex; flex-direction: column;">
    <a href="./data/enterprise-bi-sqldw.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/data-guide.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>将 Enterprise BI 与 SQL 数据仓库配合使用</h3>
                        <p>用于将数据从本地数据库移到 SQL 数据仓库中的 ELT（提取-加载-转换）管道。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<!-- Automated enterprise BI with Azure Data Factory -->
<li style="display: flex; flex-direction: column;">
    <a href="./data/enterprise-bi-adf.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/data-guide.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>将自动化企业 BI 与 Azure 数据工厂配合使用</h3>
                        <p>自动通过 ELT 管道从本地数据库进行增量加载。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<!-- Stream processing with Azure Databricks -->
<li style="display: flex; flex-direction: column;">
    <a href="./data/stream-processing-databricks.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/databricks.png" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>使用 Azure Databricks 进行流处理</h3>
                        <p>联接两个流中的记录、丰富结果并计算移动平均值的流处理管道。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<!-- Stream processing with Azure Stream Analytics -->
<li style="display: flex; flex-direction: column;">
    <a href="./data/stream-processing-stream-analytics.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/azure-analysis-service.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>使用 Azure 流分析进行流处理</h3>
                        <p>将两个数据流中的记录相关联来计算移动平均值的端到端流处理管道。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
</ul>

## <a name="internet-of-things"></a>物联网

<ul  class="panelContent cardsF">
<!-- Azure IoT reference architecture -->
<li style="display: flex; flex-direction: column;">
    <a href="./iot/index.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="./iot/_images/iot.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Azure IoT 参考体系结构</h3>
                        <p>Azure 上使用 PaaS（平台即服务）组件的 IoT 应用程序的建议体系结构。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
</ul>

## <a name="microservices"></a>微服务

<ul  class="panelContent cardsF">
<!-- Microservices on Azure Kubernetes Service (AKS) -->
<li style="display: flex; flex-direction: column;">
    <a href="./microservices/aks.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/aks.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Azure Kubernetes 服务 (AKS) 上的微服务</h3>
                        <p>建议用于在 AKS 上部署微服务的体系结构。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<!-- Microservices architecture on Azure Service Fabric -->
<li style="display: flex; flex-direction: column;">
    <a href="./microservices/service-fabric.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/sf.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Azure Service Fabric 上的微服务体系结构</h3>
                        <p>建议用于 Service Fabric 上的微服务的体系结构。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
</ul>

## <a name="serverless-applications"></a>无服务器应用程序

<ul  class="panelContent cardsF">
<!-- Serverless web application -->
<li style="display: flex; flex-direction: column;">
    <a href="./serverless/web-app.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/functions.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>无服务器 Web 应用程序</h3>
                        <p>一个无服务器 Web 应用程序，它提供有关 Blob 存储中的静态内容的服务，并使用 Azure Functions 来实现一个 API。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<!-- Event processing using Azure Functions -->
<li style="display: flex; flex-direction: column;">
    <a href="./serverless/event-processing.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/functions.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>使用 Azure Functions 进行事件处理</h3>
                        <p>一个事件驱动的体系结构，它引入数据流并使用 Functions 来处理数据。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
</ul>

## <a name="virtual-networks"></a>虚拟网络

<ul  class="panelContent cardsF">
<!-- Hybrid network using a virtual private network (VPN) -->
<li style="display: flex; flex-direction: column;">
    <a href="./hybrid-networking/vpn.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/vpn.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>使用虚拟专用网络 (VPN) 的混合网络</h3>
                        <p>将本地网络连接到 Azure 虚拟网络。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<!-- Hybrid network using ExpressRoute -->
<li style="display: flex; flex-direction: column;">
    <a href="./hybrid-networking/expressroute.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/expressroute.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>使用 ExpressRoute 的混合网络</h3>
                        <p>通过专用连接将本地网络扩展到 Azure。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<!-- Hybrid network using ExpressRoute with VPN failover -->
<li style="display: flex; flex-direction: column;">
    <a href="./hybrid-networking/expressroute-vpn-failover.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/expressroute.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>使用 ExpressRoute 进行 VPN 故障转移的混合网络</h3>
                        <p>使用 ExpressRoute 并将 VPN 作为故障转移连接以确保高可用性。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<!-- Hub-spoke network topology -->
<li style="display: flex; flex-direction: column;">
    <a href="./hybrid-networking/hub-spoke.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/gateway.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>中心辐射型网络拓扑</h3>
                        <p>创建到本地网络的中心连接点，同时隔离工作负荷。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<!-- Hub-spoke topology with shared services -->
<li style="display: flex; flex-direction: column;">
    <a href="./hybrid-networking/shared-services.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/gateway.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>带共享服务的中心辐射型拓扑</h3>
                        <p>通过包括共享服务（例如 Active Directory）来扩展中心辐射型拓扑。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<!-- DMZ between Azure and on-premises -->
<li style="display: flex; flex-direction: column;">
    <a href="./dmz/secure-vnet-hybrid.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/vnet.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Azure 与本地之间的外围网络</h3>
                        <p>使用网络虚拟设备创建安全的混合网络。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<!-- DMZ between Azure and the Internet -->
<li style="display: flex; flex-direction: column;">
    <a href="./dmz/secure-vnet-dmz.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/vnet.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Azure 与 Internet 之间的外围网络</h3>
                        <p>使用网络虚拟设备创建接受 Internet 流量的安全网络。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<!-- Highly available network virtual appliances -->
<li style="display: flex; flex-direction: column;">
    <a href="./dmz/nva-ha.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/vnet.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>高度可用的网络虚拟设备</h3>
                        <p>在 Azure 中部署一组网络虚拟设备 (NVA) 以实现高可用性。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
</ul>

## <a name="vm-workloads"></a>VM 工作负荷

<ul  class="panelContent cardsF">
<!-- N-tier application with SQL Server -->
<li style="display: flex; flex-direction: column;">
    <a href="./n-tier/n-tier-sql-server.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/windows.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>使用 SQL Server 的 N 层应用程序</h3>
                        <p>为一个使用 Windows 版 SQL Server 的 N 层应用程序配置的虚拟机。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<!-- Multi-region N-tier application -->
<li style="display: flex; flex-direction: column;">
    <a href="./n-tier/multi-region-sql-server.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/windows.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>多区域 N 层应用程序</h3>
                        <p>N 层应用程序位于两个区域以确保高可用性，使用 SQL Server Always On 可用性组。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<!-- N-tier application with Cassandra -->
<li style="display: flex; flex-direction: column;">
    <a href="./n-tier/n-tier-cassandra.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/linux-penguin.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>使用 Cassandra 的 N 层应用程序</h3>
                        <p>为一个使用 Linux 版 Apache Cassandra 的 N 层应用程序配置的虚拟机。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<!-- Jenkins build server -->
<li style="display: flex; flex-direction: column;">
    <a href="./jenkins/index.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/jenkins.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Jenkins 生成服务器</h3>
                        <p>Azure 上的可缩放型企业级 Jenkins 服务器。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<!-- SharePoint Server 2016 farm -->
<li style="display: flex; flex-direction: column;">
    <a href="./sharepoint/index.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/sharepoint.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>SharePoint Server 2016 场</h3>
                        <p>Azure 上的高可用性 SharePoint Server 2016 场，使用 SQL Server Always On 可用性组。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
</ul>

## <a name="sap"></a>SAP

<ul  class="panelContent cardsF">
<!-- SAP NetWeaver -->
<li style="display: flex; flex-direction: column;">
    <a href="./sap/sap-netweaver.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/sap.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>SAP NetWeaver</h3>
                        <p>Windows 上的 SAP NetWeaver，位于支持灾难恢复的高可用性环境中。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<!-- SAP S/4HANA -->
<li style="display: flex; flex-direction: column;">
    <a href="./sap/sap-s4hana.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/sap.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>SAP S/4HANA</h3>
                        <p>Linux 上的 SAP S/4HANA，位于支持灾难恢复的高可用性环境中。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<!-- SAP HANA on Azure Large Instances -->
<li style="display: flex; flex-direction: column;">
    <a href="./sap/hana-large-instances.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/sap.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Azure SAP HANA 大型实例</h3>
                        <p>HANA 大型实例部署在 Azure 区域中的物理服务器上。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
</ul>

## <a name="extend-on-premises-active-directory-to-azure"></a>将本地 Active Directory 扩展到 Azure

<ul  class="panelContent cardsF">
<!-- Integrate with Azure Active Directory -->
<li style="display: flex; flex-direction: column;">
    <a href="./identity/azure-ad.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/azure-active-directory.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>与 Azure Active Directory 集成</h3>
                        <p>将本地 AD 域与 Azure Active Directory 集成。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<!-- Extend an on-premises Active Directory domain to Azure -->
<li style="display: flex; flex-direction: column;">
    <a href="./identity/adds-extend-domain.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/active-directory-vm.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>将本地 Active Directory 域扩展到 Azure</h3>
                        <p>在 Azure 中部署 Active Directory 域服务 (AD DS) 以扩展本地域。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<!-- Create an AD DS forest in Azure -->
<li style="display: flex; flex-direction: column;">
    <a href="./identity/adds-forest.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/active-directory-vm.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>在 Azure 中创建 AD DS 林</h3>
                        <p>在 Azure 中创建受本地 AD 林信任的独立 AD 域。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<!-- Extend Active Directory Federation Services (AD FS) to Azure -->
<li style="display: flex; flex-direction: column;">
    <a href="./identity/adfs.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/active-directory-vm.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>将 Active Directory 联合身份验证服务 (AD FS) 扩展到 Azure</h3>
                        <p>将 AD FS 用于在 Azure 中运行的组件的联合身份验证和授权。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
</ul>

## <a name="web-applications"></a>Web 应用程序

<ul  class="panelContent cardsF">
<!-- Basic web application -->
<li style="display: flex; flex-direction: column;">
    <a href="./app-service-web-app/basic-web-app.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/app-service.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>基本 Web 应用程序</h3>
                        <p>使用 Azure 应用服务和 Azure SQL 数据库的 Web 应用程序。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<!-- Highly scalable web application -->
<li style="display: flex; flex-direction: column;">
    <a href="./app-service-web-app/scalable-web-app.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/app-service.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>高度可缩放的 Web 应用程序</h3>
                        <p>提高 Web 应用程序伸缩性的成熟做法。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<!-- Highly available web application -->
<li style="display: flex; flex-direction: column;">
    <a href="./app-service-web-app/multi-region.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/app-service.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>高度可用的 Web 应用程序</h3>
                        <p>在多个区域运行应用服务 Web 应用以实现高可用性。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<!-- Web application monitoring on Azure -->
<li style="display: flex; flex-direction: column;">
    <a href="./app-service-web-app/app-monitoring.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardImageOuter">
                        <div class="cardImage">
                            <img src="../_images/icons/app-service.svg" height="140px" />
                        </div>
                    </div>
                    <div class="cardText">
                        <h3>Azure 上的 Web 应用程序监视</h3>
                        <p>监视 Azure 应用服务中托管的 Web 应用程序。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
</ul>