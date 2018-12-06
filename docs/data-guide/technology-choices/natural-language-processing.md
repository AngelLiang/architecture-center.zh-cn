---
title: 选择自然语言处理技术
description: ''
author: zoinerTejada
ms.date: 02/12/2018
ms.openlocfilehash: b1cb019164285d16b6e9d34eae220801785adab9
ms.sourcegitcommit: e7e0e0282fa93f0063da3b57128ade395a9c1ef9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2018
ms.locfileid: "52902300"
---
# <a name="choosing-a-natural-language-processing-technology-in-azure"></a><span data-ttu-id="0a73c-102">在 Azure 中选择自然语言处理技术</span><span class="sxs-lookup"><span data-stu-id="0a73c-102">Choosing a natural language processing technology in Azure</span></span>

<span data-ttu-id="0a73c-103">自由格式文本处理针对包含文本段落的文档执行，通常是为了支持搜索，但也用于执行其他自然语言处理 (NLP) 任务，如情绪分析、主题检测、语言检测、关键短语提取和文档分类。</span><span class="sxs-lookup"><span data-stu-id="0a73c-103">Free-form text processing is performed against documents containing paragraphs of text, typically for the purpose of supporting search, but is also used to perform other natural language processing (NLP) tasks such as sentiment analysis, topic detection, language detection, key phrase extraction, and document categorization.</span></span> <span data-ttu-id="0a73c-104">本文重点介绍支持 NLP 任务的技术选项。</span><span class="sxs-lookup"><span data-stu-id="0a73c-104">This article focuses on the technology choices that act in support of the NLP tasks.</span></span>

## <a name="what-are-your-options-when-choosing-an-nlp-service"></a><span data-ttu-id="0a73c-105">选择 NLP 服务时有哪些选项？</span><span class="sxs-lookup"><span data-stu-id="0a73c-105">What are your options when choosing an NLP service?</span></span>

<span data-ttu-id="0a73c-106">在 Azure 中，以下服务提供自然语言处理 (NLP) 功能：</span><span class="sxs-lookup"><span data-stu-id="0a73c-106">In Azure, the following services provide natural language processing (NLP) capabilities:</span></span>

- [<span data-ttu-id="0a73c-107">Azure HDInsight with Spark and Spark MLlib</span><span class="sxs-lookup"><span data-stu-id="0a73c-107">Azure HDInsight with Spark and Spark MLlib</span></span>](/azure/hdinsight/spark/apache-spark-overview)
- [<span data-ttu-id="0a73c-108">Azure Databricks</span><span class="sxs-lookup"><span data-stu-id="0a73c-108">Azure Databricks</span></span>](/azure/azure-databricks/what-is-azure-databricks)
- [<span data-ttu-id="0a73c-109">Microsoft 认知服务</span><span class="sxs-lookup"><span data-stu-id="0a73c-109">Microsoft Cognitive Services</span></span>](/azure/cognitive-services/welcome)

## <a name="key-selection-criteria"></a><span data-ttu-id="0a73c-110">关键选择条件</span><span class="sxs-lookup"><span data-stu-id="0a73c-110">Key selection criteria</span></span>

<span data-ttu-id="0a73c-111">若要缩小选择范围，请先回答以下问题：</span><span class="sxs-lookup"><span data-stu-id="0a73c-111">To narrow the choices, start by answering these questions:</span></span>

- <span data-ttu-id="0a73c-112">是否要使用预构建的模型？</span><span class="sxs-lookup"><span data-stu-id="0a73c-112">Do you want to use prebuilt models?</span></span> <span data-ttu-id="0a73c-113">如果是，请考虑使用 Microsoft 认知服务提供的 API。</span><span class="sxs-lookup"><span data-stu-id="0a73c-113">If yes, consider using the APIs offered by Microsoft Cognitive Services.</span></span>

- <span data-ttu-id="0a73c-114">是否需要针对大型文本数据语料库训练自定义模型？</span><span class="sxs-lookup"><span data-stu-id="0a73c-114">Do you need to train custom models against a large corpus of text data?</span></span> <span data-ttu-id="0a73c-115">如果是，请考虑使用 Azure HDInsight with Spark MLlib and Spark NLP。</span><span class="sxs-lookup"><span data-stu-id="0a73c-115">If yes, consider using Azure HDInsight with Spark MLlib and Spark NLP.</span></span>

- <span data-ttu-id="0a73c-116">是否需要低级别 NLP 功能，如词汇切分、词干分解、词形还原和词频/逆文档频率 (TF/IDF)？</span><span class="sxs-lookup"><span data-stu-id="0a73c-116">Do you need low-level NLP capabilities like tokenization, stemming, lemmatization, and term frequency/inverse document frequency (TF/IDF)?</span></span> <span data-ttu-id="0a73c-117">如果是，请考虑使用 Azure HDInsight with Spark MLlib and Spark NLP。</span><span class="sxs-lookup"><span data-stu-id="0a73c-117">If yes, consider using Azure HDInsight with Spark MLlib and Spark NLP.</span></span>

- <span data-ttu-id="0a73c-118">是否需要简单的高级别 NLP 功能，如实体和意图识别、主题检测、拼写检查或情绪分析？</span><span class="sxs-lookup"><span data-stu-id="0a73c-118">Do you need simple, high-level NLP capabilities like entity and intent identification, topic detection, spell check, or sentiment analysis?</span></span> <span data-ttu-id="0a73c-119">如果是，请考虑使用 Microsoft 认知服务提供的 API。</span><span class="sxs-lookup"><span data-stu-id="0a73c-119">If yes, consider using the APIs offered by Microsoft Cognitive Services.</span></span>

## <a name="capability-matrix"></a><span data-ttu-id="0a73c-120">功能矩阵</span><span class="sxs-lookup"><span data-stu-id="0a73c-120">Capability matrix</span></span>

<span data-ttu-id="0a73c-121">以下各表汇总了功能上的关键差异。</span><span class="sxs-lookup"><span data-stu-id="0a73c-121">The following tables summarize the key differences in capabilities.</span></span>  

### <a name="general-capabilities"></a><span data-ttu-id="0a73c-122">常规功能</span><span class="sxs-lookup"><span data-stu-id="0a73c-122">General capabilities</span></span>

| | <span data-ttu-id="0a73c-123">Azure HDInsight</span><span class="sxs-lookup"><span data-stu-id="0a73c-123">Azure HDInsight</span></span> | <span data-ttu-id="0a73c-124">Microsoft 认知服务</span><span class="sxs-lookup"><span data-stu-id="0a73c-124">Microsoft Cognitive Services</span></span> |
| --- | --- | --- |
| <span data-ttu-id="0a73c-125">提供预先训练的模型作为服务</span><span class="sxs-lookup"><span data-stu-id="0a73c-125">Provides pretrained models as a service</span></span> | <span data-ttu-id="0a73c-126">否</span><span class="sxs-lookup"><span data-stu-id="0a73c-126">No</span></span> | <span data-ttu-id="0a73c-127">是</span><span class="sxs-lookup"><span data-stu-id="0a73c-127">Yes</span></span> |
| <span data-ttu-id="0a73c-128">REST API</span><span class="sxs-lookup"><span data-stu-id="0a73c-128">REST API</span></span> | <span data-ttu-id="0a73c-129">是</span><span class="sxs-lookup"><span data-stu-id="0a73c-129">Yes</span></span> | <span data-ttu-id="0a73c-130">是</span><span class="sxs-lookup"><span data-stu-id="0a73c-130">Yes</span></span> |
| <span data-ttu-id="0a73c-131">可编程性</span><span class="sxs-lookup"><span data-stu-id="0a73c-131">Programmability</span></span> | <span data-ttu-id="0a73c-132">Python、Scala、Java</span><span class="sxs-lookup"><span data-stu-id="0a73c-132">Python, Scala, Java</span></span> | <span data-ttu-id="0a73c-133">C#、Java、Node.js、Python、PHP、Ruby</span><span class="sxs-lookup"><span data-stu-id="0a73c-133">C#, Java, Node.js, Python, PHP, Ruby</span></span> |
| <span data-ttu-id="0a73c-134">支持大数据集和大型文档的处理</span><span class="sxs-lookup"><span data-stu-id="0a73c-134">Support processing of big data sets and large documents</span></span> | <span data-ttu-id="0a73c-135">是</span><span class="sxs-lookup"><span data-stu-id="0a73c-135">Yes</span></span> | <span data-ttu-id="0a73c-136">否</span><span class="sxs-lookup"><span data-stu-id="0a73c-136">No</span></span> |

### <a name="low-level-natural-language-processing-capabilities"></a><span data-ttu-id="0a73c-137">低级别的自然语言处理功能</span><span class="sxs-lookup"><span data-stu-id="0a73c-137">Low-level natural language processing capabilities</span></span>

| | <span data-ttu-id="0a73c-138">Azure HDInsight</span><span class="sxs-lookup"><span data-stu-id="0a73c-138">Azure HDInsight</span></span> | <span data-ttu-id="0a73c-139">Microsoft 认知服务</span><span class="sxs-lookup"><span data-stu-id="0a73c-139">Microsoft Cognitive Services</span></span> |  
| --- | --- | --- | 
| <span data-ttu-id="0a73c-140">分词器</span><span class="sxs-lookup"><span data-stu-id="0a73c-140">Tokenizer</span></span> | <span data-ttu-id="0a73c-141">是 (Spark NLP)</span><span class="sxs-lookup"><span data-stu-id="0a73c-141">Yes (Spark NLP)</span></span> | <span data-ttu-id="0a73c-142">是（语言分析 API）</span><span class="sxs-lookup"><span data-stu-id="0a73c-142">Yes (Linguistic Analysis API)</span></span> |
| <span data-ttu-id="0a73c-143">词干分析器</span><span class="sxs-lookup"><span data-stu-id="0a73c-143">Stemmer</span></span> | <span data-ttu-id="0a73c-144">是 (Spark NLP)</span><span class="sxs-lookup"><span data-stu-id="0a73c-144">Yes (Spark NLP)</span></span> | <span data-ttu-id="0a73c-145">否</span><span class="sxs-lookup"><span data-stu-id="0a73c-145">No</span></span> |
| <span data-ttu-id="0a73c-146">词形还原工具</span><span class="sxs-lookup"><span data-stu-id="0a73c-146">Lemmatizer</span></span> | <span data-ttu-id="0a73c-147">是 (Spark NLP)</span><span class="sxs-lookup"><span data-stu-id="0a73c-147">Yes (Spark NLP)</span></span> | <span data-ttu-id="0a73c-148">否</span><span class="sxs-lookup"><span data-stu-id="0a73c-148">No</span></span> |
| <span data-ttu-id="0a73c-149">词性标记</span><span class="sxs-lookup"><span data-stu-id="0a73c-149">Part of speech tagging</span></span> | <span data-ttu-id="0a73c-150">是 (Spark NLP)</span><span class="sxs-lookup"><span data-stu-id="0a73c-150">Yes (Spark NLP)</span></span> | <span data-ttu-id="0a73c-151">是（语言分析 API）</span><span class="sxs-lookup"><span data-stu-id="0a73c-151">Yes (Linguistic Analysis API)</span></span> |
| <span data-ttu-id="0a73c-152">词频/逆向文档频率 (TF/IDF)</span><span class="sxs-lookup"><span data-stu-id="0a73c-152">Term frequency/inverse-document frequency (TF/IDF)</span></span> | <span data-ttu-id="0a73c-153">是 (Spark MLlib)</span><span class="sxs-lookup"><span data-stu-id="0a73c-153">Yes (Spark MLlib)</span></span> | <span data-ttu-id="0a73c-154">否</span><span class="sxs-lookup"><span data-stu-id="0a73c-154">No</span></span> |
| <span data-ttu-id="0a73c-155">字符串相似性&mdash;编辑距离计算</span><span class="sxs-lookup"><span data-stu-id="0a73c-155">String similarity&mdash;edit distance calculation</span></span> | <span data-ttu-id="0a73c-156">是 (Spark MLlib)</span><span class="sxs-lookup"><span data-stu-id="0a73c-156">Yes (Spark MLlib)</span></span> | <span data-ttu-id="0a73c-157">否</span><span class="sxs-lookup"><span data-stu-id="0a73c-157">No</span></span> |
| <span data-ttu-id="0a73c-158">N 元语法计算</span><span class="sxs-lookup"><span data-stu-id="0a73c-158">N-gram calculation</span></span> | <span data-ttu-id="0a73c-159">是 (Spark MLlib)</span><span class="sxs-lookup"><span data-stu-id="0a73c-159">Yes (Spark MLlib)</span></span> | <span data-ttu-id="0a73c-160">否</span><span class="sxs-lookup"><span data-stu-id="0a73c-160">No</span></span> |
| <span data-ttu-id="0a73c-161">停止词删除</span><span class="sxs-lookup"><span data-stu-id="0a73c-161">Stop word removal</span></span> | <span data-ttu-id="0a73c-162">是 (Spark MLlib)</span><span class="sxs-lookup"><span data-stu-id="0a73c-162">Yes (Spark MLlib)</span></span> | <span data-ttu-id="0a73c-163">否</span><span class="sxs-lookup"><span data-stu-id="0a73c-163">No</span></span> |

### <a name="high-level-natural-language-processing-capabilities"></a><span data-ttu-id="0a73c-164">高级别的自然语言处理功能</span><span class="sxs-lookup"><span data-stu-id="0a73c-164">High-level natural language processing capabilities</span></span>

| | <span data-ttu-id="0a73c-165">Azure HDInsight</span><span class="sxs-lookup"><span data-stu-id="0a73c-165">Azure HDInsight</span></span> | <span data-ttu-id="0a73c-166">Microsoft 认知服务</span><span class="sxs-lookup"><span data-stu-id="0a73c-166">Microsoft Cognitive Services</span></span> |
| --- | --- | --- | 
| <span data-ttu-id="0a73c-167">实体/意图识别和提取</span><span class="sxs-lookup"><span data-stu-id="0a73c-167">Entity/intent identification & extraction</span></span> | <span data-ttu-id="0a73c-168">否</span><span class="sxs-lookup"><span data-stu-id="0a73c-168">No</span></span> | <span data-ttu-id="0a73c-169">是（语言理解智能服务 (LUIS) API）</span><span class="sxs-lookup"><span data-stu-id="0a73c-169">Yes (Language Understanding Intelligent Service (LUIS) API)</span></span> |    
| <span data-ttu-id="0a73c-170">主题检测</span><span class="sxs-lookup"><span data-stu-id="0a73c-170">Topic detection</span></span> | <span data-ttu-id="0a73c-171">是 (Spark NLP)</span><span class="sxs-lookup"><span data-stu-id="0a73c-171">Yes (Spark NLP)</span></span> | <span data-ttu-id="0a73c-172">是（文本分析 API）</span><span class="sxs-lookup"><span data-stu-id="0a73c-172">Yes (Text Analytics API)</span></span> |
| <span data-ttu-id="0a73c-173">拼写检查</span><span class="sxs-lookup"><span data-stu-id="0a73c-173">Spell checking</span></span> | <span data-ttu-id="0a73c-174">是 (Spark NLP)</span><span class="sxs-lookup"><span data-stu-id="0a73c-174">Yes (Spark NLP)</span></span> | <span data-ttu-id="0a73c-175">是（必应拼写检查 API）</span><span class="sxs-lookup"><span data-stu-id="0a73c-175">Yes (Bing Spell Check API)</span></span> |
| <span data-ttu-id="0a73c-176">情绪分析</span><span class="sxs-lookup"><span data-stu-id="0a73c-176">Sentiment analysis</span></span> | <span data-ttu-id="0a73c-177">是 (Spark NLP)</span><span class="sxs-lookup"><span data-stu-id="0a73c-177">Yes (Spark NLP)</span></span> | <span data-ttu-id="0a73c-178">是（文本分析 API）</span><span class="sxs-lookup"><span data-stu-id="0a73c-178">Yes (Text Analytics API)</span></span> |
| <span data-ttu-id="0a73c-179">语言检测</span><span class="sxs-lookup"><span data-stu-id="0a73c-179">Language detection</span></span> | <span data-ttu-id="0a73c-180">否</span><span class="sxs-lookup"><span data-stu-id="0a73c-180">No</span></span> | <span data-ttu-id="0a73c-181">是（文本分析 API）</span><span class="sxs-lookup"><span data-stu-id="0a73c-181">Yes (Text Analytics API)</span></span> |
| <span data-ttu-id="0a73c-182">支持除英语以外的多种语言</span><span class="sxs-lookup"><span data-stu-id="0a73c-182">Supports multiple languages besides English</span></span> | <span data-ttu-id="0a73c-183">否</span><span class="sxs-lookup"><span data-stu-id="0a73c-183">No</span></span> | <span data-ttu-id="0a73c-184">是（因 API 而异）</span><span class="sxs-lookup"><span data-stu-id="0a73c-184">Yes (varies by API)</span></span> |

## <a name="see-also"></a><span data-ttu-id="0a73c-185">另请参阅</span><span class="sxs-lookup"><span data-stu-id="0a73c-185">See also</span></span>

[<span data-ttu-id="0a73c-186">自然语言处理</span><span class="sxs-lookup"><span data-stu-id="0a73c-186">Natural language processing</span></span>](../scenarios/natural-language-processing.md)
