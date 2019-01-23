---
title: 关系数据
description: ''
author: zoinerTejada
ms.date: 02/12/2018
ms.topic: guide
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.openlocfilehash: d68bddcb75e5c8f786a7739e85de2645a2c3d641
ms.sourcegitcommit: 1b50810208354577b00e89e5c031b774b02736e2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2019
ms.locfileid: "54484919"
---
# <a name="traditional-relational-database-solutions"></a><span data-ttu-id="448d3-102">传统关系数据库解决方案</span><span class="sxs-lookup"><span data-stu-id="448d3-102">Traditional relational database solutions</span></span>

<span data-ttu-id="448d3-103">关系数据是使用关系模型建模的数据。</span><span class="sxs-lookup"><span data-stu-id="448d3-103">Relational data is data modeled using the relational model.</span></span> <span data-ttu-id="448d3-104">在此模型中，数据表示为元组。</span><span class="sxs-lookup"><span data-stu-id="448d3-104">In this model, data is expressed as tuples.</span></span> <span data-ttu-id="448d3-105">*元组*是属性/值对的集合。</span><span class="sxs-lookup"><span data-stu-id="448d3-105">A *tuple* is a set of attribute/value pairs.</span></span> <span data-ttu-id="448d3-106">例如，元组可能为 (itemid = 5, orderid = 1, item = "Chair", amount = 200.00)。</span><span class="sxs-lookup"><span data-stu-id="448d3-106">For example, a tuple might be (itemid = 5, orderid = 1, item = "Chair", amount = 200.00).</span></span> <span data-ttu-id="448d3-107">具有相同属性的元组集称为“关系”。</span><span class="sxs-lookup"><span data-stu-id="448d3-107">A set of tuples that all share the same attributes is called a *relation*.</span></span>

<span data-ttu-id="448d3-108">关系自然地表示为表，其中每个元组公开为表中的一行。</span><span class="sxs-lookup"><span data-stu-id="448d3-108">Relations are naturally represented as tables, where each tuple is exposed as a row in the table.</span></span> <span data-ttu-id="448d3-109">但是，与元组不同，行具有显式的顺序。</span><span class="sxs-lookup"><span data-stu-id="448d3-109">However, rows have an explicit ordering, unlike tuples.</span></span> <span data-ttu-id="448d3-110">数据库架构定义每个表的列（标题）。</span><span class="sxs-lookup"><span data-stu-id="448d3-110">The database schema defines the columns (headings) of each table.</span></span> <span data-ttu-id="448d3-111">每个列都定义有一个名称和一个数据类型，该名称和数据类型应用于在表的所有行的该列中存储的所有值。</span><span class="sxs-lookup"><span data-stu-id="448d3-111">Each column is defined with a name and a data type for all values stored in that column across all rows in the table.</span></span>

![一个示例，其中显示了使用关系数据库的数据](../images/example-relational.png)

<span data-ttu-id="448d3-113">使用关系模型来组织数据的数据存储称为关系数据库。</span><span class="sxs-lookup"><span data-stu-id="448d3-113">A data store that organizes data using the relational model is referred to as a relational database.</span></span> <span data-ttu-id="448d3-114">主键唯一地标识表中的行。</span><span class="sxs-lookup"><span data-stu-id="448d3-114">Primary keys uniquely identify rows within a table.</span></span> <span data-ttu-id="448d3-115">外键字段在一个表中用来通过引用另一个表的主键来引用另一个表中的行。</span><span class="sxs-lookup"><span data-stu-id="448d3-115">Foreign key fields are used in one table to refer to a row in another table by referencing the primary key of the other table.</span></span> <span data-ttu-id="448d3-116">外键用来维护引用完整性，确保被引用的行在进行引用的行依赖它们时不会被更改或删除。</span><span class="sxs-lookup"><span data-stu-id="448d3-116">Foreign keys are used to maintain referential integrity, ensuring that the referenced rows are not altered or deleted while the referencing row depends on them.</span></span>

![一个示例，其中显示了使用关系数据库的数据](../images/example-relational2.png)

<span data-ttu-id="448d3-118">关系数据库支持有助于确保数据完整性的各种约束：</span><span class="sxs-lookup"><span data-stu-id="448d3-118">Relational databases support various types of constraints that help to ensure data integrity:</span></span>

- <span data-ttu-id="448d3-119">唯一约束确保某个列中的所有值都是唯一的。</span><span class="sxs-lookup"><span data-stu-id="448d3-119">Unique constraints ensure that all values in a column are unique.</span></span>

- <span data-ttu-id="448d3-120">外键约束在两个表中的数据之间强制实施链接。</span><span class="sxs-lookup"><span data-stu-id="448d3-120">Foreign key constraints enforce a link between the data in two tables.</span></span> <span data-ttu-id="448d3-121">外键引用另一个表中的主键或另一个唯一键。</span><span class="sxs-lookup"><span data-stu-id="448d3-121">A foreign key references the primary key or another unique key from another table.</span></span> <span data-ttu-id="448d3-122">外键约束强制实施引用完整性，不允许执行会导致无效外键值的更改。</span><span class="sxs-lookup"><span data-stu-id="448d3-122">A foreign key constraint enforces referential integrity, disallowing changes that cause invalid foreign key values.</span></span>

- <span data-ttu-id="448d3-123">检查约束也称为实体完整性约束，它们将可以在单个列中或在关系中存储的值限制为同一行中其他列中的值。</span><span class="sxs-lookup"><span data-stu-id="448d3-123">Check constraints, also known as entity integrity constraints, limit the values that can be stored within a single column, or in relationship to values in other columns of the same row.</span></span>

<span data-ttu-id="448d3-124">大多数关系数据库使用结构化查询语言 (SQL) 语言，这使得可以使用声明性方法进行查询。</span><span class="sxs-lookup"><span data-stu-id="448d3-124">Most relational databases use the Structured Query Language (SQL) language that enables a declarative approach to querying.</span></span> <span data-ttu-id="448d3-125">查询描述所需的结果，但不描述执行查询的步骤。</span><span class="sxs-lookup"><span data-stu-id="448d3-125">The query describes the desired result, but not the steps to execute the query.</span></span> <span data-ttu-id="448d3-126">然后，引擎决定用来执行查询的最佳方式。</span><span class="sxs-lookup"><span data-stu-id="448d3-126">The engine then decides the best way to execute the query.</span></span> <span data-ttu-id="448d3-127">这不同于过程性方法，在过程性方法中，查询程序显式指定处理步骤。</span><span class="sxs-lookup"><span data-stu-id="448d3-127">This differs from a procedural approach, where the query program specifies the processing steps explicitly.</span></span> <span data-ttu-id="448d3-128">但是，关系数据库能够以存储过程或函数的形式存储可执行代码例程，因此可以混合使用声明性和过程性方法。</span><span class="sxs-lookup"><span data-stu-id="448d3-128">However, relational databases can store executable code routines in the form of stored procedures and functions, which enables a mixture of declarative and procedural approaches.</span></span>

<span data-ttu-id="448d3-129">为提高查询性能，关系数据库使用“索引”。</span><span class="sxs-lookup"><span data-stu-id="448d3-129">To improve query performance, relational databases use *indexes*.</span></span> <span data-ttu-id="448d3-130">主索引由主键使用，它们将数据顺序定义为与在磁盘上的顺序相同。</span><span class="sxs-lookup"><span data-stu-id="448d3-130">Primary indexes, which are used by the primary key, define the order of the data as it sits on disk.</span></span> <span data-ttu-id="448d3-131">辅助索引提供了替换的字段组合，因此，无需对磁盘上的全部数据重新排序便可高效地查询所需的行。</span><span class="sxs-lookup"><span data-stu-id="448d3-131">Secondary indexes provide an alternative combination of fields, so the desired rows can be queried efficiently, without having to re-sort the entire data on disk.</span></span>

<span data-ttu-id="448d3-132">因为关系数据库强制实施引用完整性，所以，缩放关系数据库可能变得很困难。</span><span class="sxs-lookup"><span data-stu-id="448d3-132">Because relational databases enforce referential integrity, scaling a relational database can become challenging.</span></span> <span data-ttu-id="448d3-133">这是因为任何查询或插入操作都可能会涉及任意数量的表。</span><span class="sxs-lookup"><span data-stu-id="448d3-133">That's because any query or insert operation might touch any number of tables.</span></span> <span data-ttu-id="448d3-134">可以通过对数据进行“分片”来横向扩展关系数据库，但这需要仔细设计架构。</span><span class="sxs-lookup"><span data-stu-id="448d3-134">You can scale out a relational database by *sharding* the data, but this requires careful design of the schema.</span></span> <span data-ttu-id="448d3-135">有关详细信息，请参阅 [分片模式](../../patterns/sharding.md)。</span><span class="sxs-lookup"><span data-stu-id="448d3-135">For more information, see the [Sharding pattern](../../patterns/sharding.md).</span></span>

<span data-ttu-id="448d3-136">如果数据是非关系型的或者具有不适合关系数据库的要求，请考虑使用[非关系或 NoSQL](../big-data/non-relational-data.md) 数据存储。</span><span class="sxs-lookup"><span data-stu-id="448d3-136">If data is non-relational or has requirements that are not suited to a relational database, consider a [Non-relational or NoSQL](../big-data/non-relational-data.md) data store.</span></span>
