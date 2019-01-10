---
title: 选择自然语言处理技术
description: ''
author: zoinerTejada
ms.date: 02/12/2018
ms.openlocfilehash: 699e01bc9905d02fc8ec1113039087189f6e8caf
ms.sourcegitcommit: 1f4cdb08fe73b1956e164ad692f792f9f635b409
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2019
ms.locfileid: "54114107"
---
# <a name="choosing-a-natural-language-processing-technology-in-azure"></a><span data-ttu-id="1773a-102">在 Azure 中选择自然语言处理技术</span><span class="sxs-lookup"><span data-stu-id="1773a-102">Choosing a natural language processing technology in Azure</span></span>

<span data-ttu-id="1773a-103">自由格式文本处理针对包含文本段落的文档执行，通常是为了支持搜索，但也用于执行其他自然语言处理 (NLP) 任务，如情绪分析、主题检测、语言检测、关键短语提取和文档分类。</span><span class="sxs-lookup"><span data-stu-id="1773a-103">Free-form text processing is performed against documents containing paragraphs of text, typically for the purpose of supporting search, but is also used to perform other natural language processing (NLP) tasks such as sentiment analysis, topic detection, language detection, key phrase extraction, and document categorization.</span></span> <span data-ttu-id="1773a-104">本文重点介绍支持 NLP 任务的技术选项。</span><span class="sxs-lookup"><span data-stu-id="1773a-104">This article focuses on the technology choices that act in support of the NLP tasks.</span></span>

<!-- markdownlint-disable MD026 -->

## <a name="what-are-your-options-when-choosing-an-nlp-service"></a><span data-ttu-id="1773a-105">选择 NLP 服务时有哪些选项？</span><span class="sxs-lookup"><span data-stu-id="1773a-105">What are your options when choosing an NLP service?</span></span>

<!-- markdownlint-enable MD026 -->

<span data-ttu-id="1773a-106">在 Azure 中，以下服务提供自然语言处理 (NLP) 功能：</span><span class="sxs-lookup"><span data-stu-id="1773a-106">In Azure, the following services provide natural language processing (NLP) capabilities:</span></span>

- [<span data-ttu-id="1773a-107">Azure HDInsight with Spark and Spark MLlib</span><span class="sxs-lookup"><span data-stu-id="1773a-107">Azure HDInsight with Spark and Spark MLlib</span></span>](/azure/hdinsight/spark/apache-spark-overview)
- [<span data-ttu-id="1773a-108">Azure Databricks</span><span class="sxs-lookup"><span data-stu-id="1773a-108">Azure Databricks</span></span>](/azure/azure-databricks/what-is-azure-databricks)
- [<span data-ttu-id="1773a-109">Microsoft 认知服务</span><span class="sxs-lookup"><span data-stu-id="1773a-109">Microsoft Cognitive Services</span></span>](/azure/cognitive-services/welcome)

## <a name="key-selection-criteria"></a><span data-ttu-id="1773a-110">关键选择条件</span><span class="sxs-lookup"><span data-stu-id="1773a-110">Key selection criteria</span></span>

<span data-ttu-id="1773a-111">若要缩小选择范围，请先回答以下问题：</span><span class="sxs-lookup"><span data-stu-id="1773a-111">To narrow the choices, start by answering these questions:</span></span>

- <span data-ttu-id="1773a-112">是否要使用预构建的模型？</span><span class="sxs-lookup"><span data-stu-id="1773a-112">Do you want to use prebuilt models?</span></span> <span data-ttu-id="1773a-113">如果是，请考虑使用 Microsoft 认知服务提供的 API。</span><span class="sxs-lookup"><span data-stu-id="1773a-113">If yes, consider using the APIs offered by Microsoft Cognitive Services.</span></span>

- <span data-ttu-id="1773a-114">是否需要针对大型文本数据语料库训练自定义模型？</span><span class="sxs-lookup"><span data-stu-id="1773a-114">Do you need to train custom models against a large corpus of text data?</span></span> <span data-ttu-id="1773a-115">如果是，请考虑使用 Azure HDInsight with Spark MLlib and Spark NLP。</span><span class="sxs-lookup"><span data-stu-id="1773a-115">If yes, consider using Azure HDInsight with Spark MLlib and Spark NLP.</span></span>

- <span data-ttu-id="1773a-116">是否需要低级别 NLP 功能，如词汇切分、词干分解、词形还原和词频/逆文档频率 (TF/IDF)？</span><span class="sxs-lookup"><span data-stu-id="1773a-116">Do you need low-level NLP capabilities like tokenization, stemming, lemmatization, and term frequency/inverse document frequency (TF/IDF)?</span></span> <span data-ttu-id="1773a-117">如果是，请考虑使用 Azure HDInsight with Spark MLlib and Spark NLP。</span><span class="sxs-lookup"><span data-stu-id="1773a-117">If yes, consider using Azure HDInsight with Spark MLlib and Spark NLP.</span></span>

- <span data-ttu-id="1773a-118">是否需要简单的高级别 NLP 功能，如实体和意图识别、主题检测、拼写检查或情绪分析？</span><span class="sxs-lookup"><span data-stu-id="1773a-118">Do you need simple, high-level NLP capabilities like entity and intent identification, topic detection, spell check, or sentiment analysis?</span></span> <span data-ttu-id="1773a-119">如果是，请考虑使用 Microsoft 认知服务提供的 API。</span><span class="sxs-lookup"><span data-stu-id="1773a-119">If yes, consider using the APIs offered by Microsoft Cognitive Services.</span></span>

## <a name="capability-matrix"></a><span data-ttu-id="1773a-120">功能矩阵</span><span class="sxs-lookup"><span data-stu-id="1773a-120">Capability matrix</span></span>

<span data-ttu-id="1773a-121">以下各表汇总了功能上的关键差异。</span><span class="sxs-lookup"><span data-stu-id="1773a-121">The following tables summarize the key differences in capabilities.</span></span>

### <a name="general-capabilities"></a><span data-ttu-id="1773a-122">常规功能</span><span class="sxs-lookup"><span data-stu-id="1773a-122">General capabilities</span></span>

| | <span data-ttu-id="1773a-123">Azure HDInsight</span><span class="sxs-lookup"><span data-stu-id="1773a-123">Azure HDInsight</span></span> | <span data-ttu-id="1773a-124">Microsoft 认知服务</span><span class="sxs-lookup"><span data-stu-id="1773a-124">Microsoft Cognitive Services</span></span> |
| --- | --- | --- |
| <span data-ttu-id="1773a-125">提供预先训练的模型作为服务</span><span class="sxs-lookup"><span data-stu-id="1773a-125">Provides pretrained models as a service</span></span> | <span data-ttu-id="1773a-126">否</span><span class="sxs-lookup"><span data-stu-id="1773a-126">No</span></span> | <span data-ttu-id="1773a-127">是</span><span class="sxs-lookup"><span data-stu-id="1773a-127">Yes</span></span> |
| <span data-ttu-id="1773a-128">REST API</span><span class="sxs-lookup"><span data-stu-id="1773a-128">REST API</span></span> | <span data-ttu-id="1773a-129">是</span><span class="sxs-lookup"><span data-stu-id="1773a-129">Yes</span></span> | <span data-ttu-id="1773a-130">是</span><span class="sxs-lookup"><span data-stu-id="1773a-130">Yes</span></span> |
| <span data-ttu-id="1773a-131">可编程性</span><span class="sxs-lookup"><span data-stu-id="1773a-131">Programmability</span></span> | <span data-ttu-id="1773a-132">Python、Scala、Java</span><span class="sxs-lookup"><span data-stu-id="1773a-132">Python, Scala, Java</span></span> | <span data-ttu-id="1773a-133">C#、Java、Node.js、Python、PHP、Ruby</span><span class="sxs-lookup"><span data-stu-id="1773a-133">C#, Java, Node.js, Python, PHP, Ruby</span></span> |
| <span data-ttu-id="1773a-134">支持大数据集和大型文档的处理</span><span class="sxs-lookup"><span data-stu-id="1773a-134">Support processing of big data sets and large documents</span></span> | <span data-ttu-id="1773a-135">是</span><span class="sxs-lookup"><span data-stu-id="1773a-135">Yes</span></span> | <span data-ttu-id="1773a-136">否</span><span class="sxs-lookup"><span data-stu-id="1773a-136">No</span></span> |

### <a name="low-level-natural-language-processing-capabilities"></a><span data-ttu-id="1773a-137">低级别的自然语言处理功能</span><span class="sxs-lookup"><span data-stu-id="1773a-137">Low-level natural language processing capabilities</span></span>

| | <span data-ttu-id="1773a-138">Azure HDInsight</span><span class="sxs-lookup"><span data-stu-id="1773a-138">Azure HDInsight</span></span> | <span data-ttu-id="1773a-139">Microsoft 认知服务</span><span class="sxs-lookup"><span data-stu-id="1773a-139">Microsoft Cognitive Services</span></span> |  
| --- | --- | --- |
| <span data-ttu-id="1773a-140">分词器</span><span class="sxs-lookup"><span data-stu-id="1773a-140">Tokenizer</span></span> | <span data-ttu-id="1773a-141">是 (Spark NLP)</span><span class="sxs-lookup"><span data-stu-id="1773a-141">Yes (Spark NLP)</span></span> | <span data-ttu-id="1773a-142">是（语言分析 API）</span><span class="sxs-lookup"><span data-stu-id="1773a-142">Yes (Linguistic Analysis API)</span></span> |
| <span data-ttu-id="1773a-143">词干分析器</span><span class="sxs-lookup"><span data-stu-id="1773a-143">Stemmer</span></span> | <span data-ttu-id="1773a-144">是 (Spark NLP)</span><span class="sxs-lookup"><span data-stu-id="1773a-144">Yes (Spark NLP)</span></span> | <span data-ttu-id="1773a-145">否</span><span class="sxs-lookup"><span data-stu-id="1773a-145">No</span></span> |
| <span data-ttu-id="1773a-146">词形还原工具</span><span class="sxs-lookup"><span data-stu-id="1773a-146">Lemmatizer</span></span> | <span data-ttu-id="1773a-147">是 (Spark NLP)</span><span class="sxs-lookup"><span data-stu-id="1773a-147">Yes (Spark NLP)</span></span> | <span data-ttu-id="1773a-148">否</span><span class="sxs-lookup"><span data-stu-id="1773a-148">No</span></span> |
| <span data-ttu-id="1773a-149">词性标记</span><span class="sxs-lookup"><span data-stu-id="1773a-149">Part of speech tagging</span></span> | <span data-ttu-id="1773a-150">是 (Spark NLP)</span><span class="sxs-lookup"><span data-stu-id="1773a-150">Yes (Spark NLP)</span></span> | <span data-ttu-id="1773a-151">是（语言分析 API）</span><span class="sxs-lookup"><span data-stu-id="1773a-151">Yes (Linguistic Analysis API)</span></span> |
| <span data-ttu-id="1773a-152">词频/逆向文档频率 (TF/IDF)</span><span class="sxs-lookup"><span data-stu-id="1773a-152">Term frequency/inverse-document frequency (TF/IDF)</span></span> | <span data-ttu-id="1773a-153">是 (Spark MLlib)</span><span class="sxs-lookup"><span data-stu-id="1773a-153">Yes (Spark MLlib)</span></span> | <span data-ttu-id="1773a-154">否</span><span class="sxs-lookup"><span data-stu-id="1773a-154">No</span></span> |
| <span data-ttu-id="1773a-155">字符串相似性&mdash;编辑距离计算</span><span class="sxs-lookup"><span data-stu-id="1773a-155">String similarity&mdash;edit distance calculation</span></span> | <span data-ttu-id="1773a-156">是 (Spark MLlib)</span><span class="sxs-lookup"><span data-stu-id="1773a-156">Yes (Spark MLlib)</span></span> | <span data-ttu-id="1773a-157">否</span><span class="sxs-lookup"><span data-stu-id="1773a-157">No</span></span> |
| <span data-ttu-id="1773a-158">N 元语法计算</span><span class="sxs-lookup"><span data-stu-id="1773a-158">N-gram calculation</span></span> | <span data-ttu-id="1773a-159">是 (Spark MLlib)</span><span class="sxs-lookup"><span data-stu-id="1773a-159">Yes (Spark MLlib)</span></span> | <span data-ttu-id="1773a-160">否</span><span class="sxs-lookup"><span data-stu-id="1773a-160">No</span></span> |
| <span data-ttu-id="1773a-161">停止词删除</span><span class="sxs-lookup"><span data-stu-id="1773a-161">Stop word removal</span></span> | <span data-ttu-id="1773a-162">是 (Spark MLlib)</span><span class="sxs-lookup"><span data-stu-id="1773a-162">Yes (Spark MLlib)</span></span> | <span data-ttu-id="1773a-163">否</span><span class="sxs-lookup"><span data-stu-id="1773a-163">No</span></span> |

### <a name="high-level-natural-language-processing-capabilities"></a><span data-ttu-id="1773a-164">高级别的自然语言处理功能</span><span class="sxs-lookup"><span data-stu-id="1773a-164">High-level natural language processing capabilities</span></span>

| | <span data-ttu-id="1773a-165">Azure HDInsight</span><span class="sxs-lookup"><span data-stu-id="1773a-165">Azure HDInsight</span></span> | <span data-ttu-id="1773a-166">Microsoft 认知服务</span><span class="sxs-lookup"><span data-stu-id="1773a-166">Microsoft Cognitive Services</span></span> |
| --- | --- | --- |
| <span data-ttu-id="1773a-167">实体/意图识别和提取</span><span class="sxs-lookup"><span data-stu-id="1773a-167">Entity/intent identification & extraction</span></span> | <span data-ttu-id="1773a-168">否</span><span class="sxs-lookup"><span data-stu-id="1773a-168">No</span></span> | <span data-ttu-id="1773a-169">是（语言理解智能服务 (LUIS) API）</span><span class="sxs-lookup"><span data-stu-id="1773a-169">Yes (Language Understanding Intelligent Service (LUIS) API)</span></span> |
| <span data-ttu-id="1773a-170">主题检测</span><span class="sxs-lookup"><span data-stu-id="1773a-170">Topic detection</span></span> | <span data-ttu-id="1773a-171">是 (Spark NLP)</span><span class="sxs-lookup"><span data-stu-id="1773a-171">Yes (Spark NLP)</span></span> | <span data-ttu-id="1773a-172">是（文本分析 API）</span><span class="sxs-lookup"><span data-stu-id="1773a-172">Yes (Text Analytics API)</span></span> |
| <span data-ttu-id="1773a-173">拼写检查</span><span class="sxs-lookup"><span data-stu-id="1773a-173">Spell checking</span></span> | <span data-ttu-id="1773a-174">是 (Spark NLP)</span><span class="sxs-lookup"><span data-stu-id="1773a-174">Yes (Spark NLP)</span></span> | <span data-ttu-id="1773a-175">是（必应拼写检查 API）</span><span class="sxs-lookup"><span data-stu-id="1773a-175">Yes (Bing Spell Check API)</span></span> |
| <span data-ttu-id="1773a-176">情绪分析</span><span class="sxs-lookup"><span data-stu-id="1773a-176">Sentiment analysis</span></span> | <span data-ttu-id="1773a-177">是 (Spark NLP)</span><span class="sxs-lookup"><span data-stu-id="1773a-177">Yes (Spark NLP)</span></span> | <span data-ttu-id="1773a-178">是（文本分析 API）</span><span class="sxs-lookup"><span data-stu-id="1773a-178">Yes (Text Analytics API)</span></span> |
| <span data-ttu-id="1773a-179">语言检测</span><span class="sxs-lookup"><span data-stu-id="1773a-179">Language detection</span></span> | <span data-ttu-id="1773a-180">否</span><span class="sxs-lookup"><span data-stu-id="1773a-180">No</span></span> | <span data-ttu-id="1773a-181">是（文本分析 API）</span><span class="sxs-lookup"><span data-stu-id="1773a-181">Yes (Text Analytics API)</span></span> |
| <span data-ttu-id="1773a-182">支持除英语以外的多种语言</span><span class="sxs-lookup"><span data-stu-id="1773a-182">Supports multiple languages besides English</span></span> | <span data-ttu-id="1773a-183">否</span><span class="sxs-lookup"><span data-stu-id="1773a-183">No</span></span> | <span data-ttu-id="1773a-184">是（因 API 而异）</span><span class="sxs-lookup"><span data-stu-id="1773a-184">Yes (varies by API)</span></span> |

## <a name="see-also"></a><span data-ttu-id="1773a-185">另请参阅</span><span class="sxs-lookup"><span data-stu-id="1773a-185">See also</span></span>

[<span data-ttu-id="1773a-186">自然语言处理</span><span class="sxs-lookup"><span data-stu-id="1773a-186">Natural language processing</span></span>](../scenarios/natural-language-processing.md)
