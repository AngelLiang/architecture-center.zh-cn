---
title: 在 Azure 上构建微服务
description: 在 Azure 上设计、构建和运行微服务体系结构
ms.date: 03/07/2019
layout: LandingPage
ms.topic: landing-page
---

# <a name="building-microservices-on-azure"></a>在 Azure 上构建微服务

<!-- markdownlint-disable MD033 -->

<img src="../_images/microservices.svg" style="float:left; margin-top:8px; margin-right:8px; max-width: 80px; max-height: 80px;"/>

微服务是一种流行的体系结构类型，用于构建可复原、高度可缩放、可独立部署且能快速演变的应用程序。 但是，成功的微服务体系结构需要利用不同的方法来设计和生成应用程序。

<ul  class="panelContent cardsZ">
<li style="display: flex; flex-direction: column;">
    <a href="./introduction.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardText">
                        <h3>什么是微服务？</h3>
                        <p>微服务与其他体系结构的区别在哪里？应该何时使用微服务？</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<li style="display: flex; flex-direction: column;">
    <a href="../guide/architecture-styles/microservices.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardText">
                        <h3>微服务体系结构样式</h3>
                        <p>微服务体系结构样式综合概述</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
</ul>

## <a name="examples-of-microservices-architectures"></a>微服务体系结构示例

<ul  class="panelContent cardsZ">
<li style="display: flex; flex-direction: column;">
    <a href="../example-scenario/infrastructure/service-fabric-microservices.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardText">
                        <h3>使用 Service Fabric 分解整体应用程序</h3>
                        <p>一种迭代方法，用于将 ASP.NET 网站分解成微服务。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<li style="display: flex; flex-direction: column;">
    <a href="../example-scenario/data/ecommerce-order-processing.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardText">
                        <h3>Azure 上的可缩放订单处理</h3>
                        <p>一种订单处理方式，使用通过微服务实现的函数编程模型。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
</ul>

## <a name="build-a-microservices-application"></a>构建微服务应用程序

<ul  class="panelContent cardsZ">
<li style="display: flex; flex-direction: column;">
    <a href="./model/domain-analysis.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardText">
                        <h3>使用域分析为微服务建模</h3>
                        <p>为了避免设计微服务时会出现的某些常见错误，请使用域分析来定义微服务边界。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<li style="display: flex; flex-direction: column;">
    <a href="../reference-architectures/microservices/aks.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardText">
                        <h3>Azure Kubernetes 服务 (AKS) 的参考体系结构</h3>
                        <p>此参考体系结构展示的基本 AKS 配置可用作大多数部署的起点。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<li style="display: flex; flex-direction: column;">
    <a href="../reference-architectures/microservices/service-fabric.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardText">
                        <h3>Azure Service Fabric 的参考体系结构</h3>
                        <p>此参考体系结构展示的推荐配置可用作大多数部署的起点。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<li style="display: flex; flex-direction: column;">
    <a href="./design/index.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardText">
                        <h3>设计微服务体系结构</h3>
                        <p>这些文章深入探讨了如何根据一个使用 Azure Kubernetes 服务 (AKS) 的参考实现来生成微服务应用程序。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<li style="display: flex; flex-direction: column;">
    <a href="./design/patterns.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardText">
                        <h3>设计模式</h3>
                        <p>一组适用于微服务的设计模式。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
</ul>

## <a name="operate-microservices-in-production"></a>在生产环境中运行微服务

<ul  class="panelContent cardsZ">
<li style="display: flex; flex-direction: column;">
    <a href="./logging-monitoring.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardText">
                        <h3>日志记录和监视</h3>
                        <p>微服务体系结构的分布式特性使得日志记录和监视变得特别重要。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
<li style="display: flex; flex-direction: column;">
    <a href="./ci-cd.md" style="display: flex; flex-direction: column; flex: 1 0 auto;">
        <div class="cardSize" style="flex: 1 0 auto; display: flex;">
            <div class="cardPadding" style="display: flex;">
                <div class="card">
                    <div class="cardText">
                        <h3>持续集成和部署</h3>
                        <p>要在微服务上获得成功，必须实施持续集成和持续交付 (CI/CD)。</p>
                    </div>
                </div>
            </div>
        </div>
    </a>
</li>
</ul>
