# 如何使用 Firebolt 实现超过 1 万亿行数据的亚秒级分析 —— Similarweb 的案例分享

## 关于 Similarweb

想象一下，一个可以分析整个互联网使用情况的工具，相当于面向全互联网的 Google Analytics，这就是 **Similarweb**。

![Delivering interactive analytics over 100s of TBs](https://player.vimeo.com/video/647222413)

Similarweb 是一家大数据公司，收集海量的网络相关数据，帮助市场营销人员、品牌、销售团队等分析用户与网站的交互行为。例如：

- 哪些关键词搜索为您的网站带来了流量；
- 用户访问的具体子页面；
- 用户是否也访问了竞争对手的网站；
- 用户的地理位置及使用的移动操作系统；
- 用户点击的是自然流量还是付费广告。

打开 Similarweb 的网站，首先映入眼帘的是一个搜索框，您可以直接输入任何网站进行研究分析：

![Similarweb 搜索界面](https://cdn.prod.website-files.com/5f9964ab7f4720029f59e197/6248d49c6ae486479d67db26_yDbUboQdNG2mSOIQE3qry32zDrYHY5-nFhvIrzg1-fJLBnvh5h_km6WpUD6YCPqMrfy044ObcqXbCXDkI76MDkLImqa81p-33smGZiPGSEDN_jQs1rneppVZjatwdmNJyHqrdnoS.png)

通过这个平台，用户可以轻松切片、分析，并获得深刻的洞察。例如，以下是对 **godaddy.com** 的行为概览：

![godaddy.com 分析概览](https://cdn.prod.website-files.com/5f9964ab7f4720029f59e197/6248d49bbdb29ba15175b6d9_oBwueE0YzP7USzXpSTvxr5wviAmQLfIYU8hhW0-v4VpE_2neo7OgVWqGKOF7gymd_W60OJbZeUQ8scl58Dth0o3CTzQmg5znAI-bSVlX6CyKJHtFaSRWT3hNf8_LZfhEz2C8z7fV.png)

用户还可以更深入地分析数据，甚至比较多个网站的数据，如 **godaddy.com** 和 **wix.com** 的对比分析：

![网站对比分析](https://cdn.prod.website-files.com/5f9964ab7f4720029f59e197/6248d49b44720b2d9a07ae7c_d578RXrYzhs9vcxPW3umEkgaZZLIxcIsQcvoy3mhCjBbMlRD7naurQgPnlC4hTbeqOSPy7Y6AyZtY5vN4PnoPUSFncq8Vq9En4LjW0380xkKFUZXRm9fWYxt6z1i0mlavHXakRJb.png)

### 数据量与技术堆栈

Similarweb 每天新增 5TB 的数据，目前生产环境下的数据集大约有 1PB，所有内容均存储在 AWS 上，主要使用以下技术栈：

- **数据湖处理**：Spark 和 Databricks；
- **任务编排**：Airflow；
- **查询服务**：DynamoDB 或 Hive（Firebolt 引入前）。

数据管道主要分为以下四个步骤：

1. **数据采集**：从公共数据、合作伙伴数据和直接测量的数据源中收集数据；
2. **数据清洗与合成**：清洗和处理数据以去除隐私或脏数据；
3. **数据建模**：通过机器学习技术，预测互联网行为；
4. **数据交付**：将数据包装成用户可以直接分析和操作的体验。

---

## 🌟 特别推荐：外贸工具团购网

**外贸工具团购网**为外贸中小型卖家和独立站主提供了一个便捷的工具平台。精选工具包括：

- **SEMrush**：竞品分析
- **Ecomhunt**：爆款选品
- **AdSpy**：广告投放策略
- **SimilarWeb**：流量分析

**外贸神器会员共享账号**，助力外贸增长！点击了解更多：[https://bit.ly/waimao518](https://bit.ly/waimao518)

---

## ‘Segment Analysis’ 用例

假设您是 FootLocker 的市场营销人员，希望了解 **FootLocker.com** 与 **Amazon.com** 的表现对比。显然，这并不是简单的“一对一”对比，因为亚马逊销售的商品远超鞋类。为此，Similarweb 提供了对“大型网站特定细分领域”的分析功能。例如，比较 FootLocker.com 与亚马逊上的“鞋类搜索”流量。

这一功能在分析上极具挑战性：

- **数据量巨大**：每天需要处理数百 GB 的数据；
- **用户动态输入**：分析必须处理用户输入的动态组合，可能的组合数量指数级增长，难以预处理；
- **分析速度要求高**：用户需要快速的响应。

**使用 Firebolt** 后，这些挑战得到了极大缓解。

---

## Firebolt 的选择与优势

在工具的选择中，Similarweb 对比了 **BigQuery**、**Firebolt** 和 **Athena**，最终选择了 Firebolt。以下是测试结果：

![性能对比](https://cdn.prod.website-files.com/5f9964ab7f4720029f59e197/6764b780047162a513377c4c_62b8dab4f33a8c7b40ae2bea_Similarweb_case_study_benchmark_3way_tiny.png)

Firebolt 之所以脱颖而出，主要由于：

1. **卓越性能**：支持原始数据的动态查询，无需预处理；
2. **存储与计算分离**：通过工作负载隔离，实现一致快速的查询体验；
3. **成本效益高**：提供更低的总体拥有成本（TCO）。

通过与 Airflow 和 Firebolt 的 REST API 集成，Similarweb 在几周内完成了生产环境的自动化部署，并基于 Firebolt 提供了更多功能支持。

以下为 Similarweb 使用 Firebolt 的具体案例：用户在 UI 中查询 **Amazon PS5** 相关数据，背后实际动态扫描了数 TB 数据，而页面加载时间仅需约 1 秒：

![PS5 数据查询](https://cdn.prod.website-files.com/5f9964ab7f4720029f59e197/6248d49c2e2fd969c8172d52_TrkVwtDPpQTrO5TJ__W21jtrqHZI1_GqrGxDzz1TpEOlz-CWKB4WP2shjcYSA-te-q_6DbLYPqdCGop337rM4hRBt2XAdqT8HdXVL5gfDjVvt1h5MdcEil7b3SclPvQ9WFo27Ya3.png)

---

## 总结

通过 Firebolt，Similarweb 成功解决了大数据处理中的速度和成本问题，同时提供了卓越的用户体验。这一案例表明，正确选择底层技术对于数据驱动的产品至关重要。
