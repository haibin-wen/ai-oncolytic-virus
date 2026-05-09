# ai-oncolytic-virus

## 项目目标

设计一款 **AI 生成 / AI 设计的溶瘤病毒**，以 HSV-1 改造为起点。

借鉴 Arc Institute 在生成式合成噬菌体上的工作（King et al., 2025），把"基因组语言模型 + 设计约束 + 湿实验筛选"这套范式从噬菌体迁移到溶瘤病毒。

## 启发来源

King et al. (2025, bioRxiv)，*Generative design of novel bacteriophages with genome language models*：

- 用 Evo 1 / Evo 2 基因组语言模型 + 监督微调，针对宿主 *E. coli* C 设计 ΦX174 类似的噬菌体基因组。
- 从约 300 个 AI 生成的基因组里筛出 16 个**有活性的真实噬菌体**。
- 部分生成体在裂解动力学上**优于天然 ΦX174**。
- Cryo-EM 显示其中一个使用了**演化上极远的 DNA 包装蛋白**——AI 探索到了天然演化没走过的设计空间。
- 多个生成噬菌体组成 cocktail，在 3 株 ΦX174-耐药的 *E. coli* 上快速逆转耐药。

这是第一次有人在**完整基因组尺度**上做生成式生物设计。

参考：
- 论文：https://www.biorxiv.org/content/10.1101/2025.09.12.675911v1
- Arc Institute 新闻：https://arcinstitute.org/news/hie-king-first-synthetic-phage

## 为什么是溶瘤病毒

溶瘤病毒（oncolytic virus, OV）已有临床先例（T-VEC 等），但目前的改造仍然是**理性设计 + 经验试错**——删除 ICP34.5、插入 GM-CSF、置换启动子，几十年没大变。

如果把 King et al. 的范式搬过来，有可能在**基因组尺度上联合优化**：
- 哪些非必需基因可以删除？删多少？
- 启动子工程，调强组织特异性表达。
- payload 选择和组合优化（免疫调节因子、激酶、检查点抑制剂等）。
- 衣壳工程修改 tropism，提高肿瘤选择性。

这些问题原本是分开做的小修小补，基因组语言模型有可能把它们作为联合优化问题处理。

## 设计目标（基于 HSV-1）

设计一款基于 HSV-1 改造的溶瘤病毒，要求：

- 在肿瘤细胞中高效复制和裂解。
- 在正常细胞（特别是神经元）中弱毒 / 无毒。
- 携带可调的免疫激活 payload。
- 演化稳定性：抗肿瘤异质性、抗逃逸。

## 关键差异（相对于噬菌体工作）

噬菌体的整体范式可以借鉴，但搬到 HSV-1 有几个关键挑战：

1. **基因组规模差距巨大**：ΦX174 ~5.4 kb / 11 个基因；HSV-1 ~152 kb / ~80 个基因。基因组语言模型在更长基因组上的生成能力还需要验证。
2. **真核病毒生物学复杂**：剪接、潜伏-激活、免疫逃逸机制远比噬菌体丰富。
3. **宿主是癌细胞**：tropism 不是"感染哪种菌株"，而是"区分肿瘤 vs 正常组织"——这是个细胞状态问题，不是基因组序列匹配问题。
4. **筛选通量低**：噬菌体可以靠平板筛上百个候选；HSV-1 重组、细胞实验、动物模型每一轮都更贵。
5. **安全性约束严格**：要做临床转化，安全性不能是事后补丁。

## 资源

- **湿实验能力**：项目发起方有溶瘤病毒改造经验，HSV-1 的工程化和功能验证可在自己实验室完成。
- **AI 思路参考**：Arc Institute 的 phage 工作（King et al., 2025）；Evo 1 / Evo 2 已开源。

## 不在范围内（这一阶段）

- 具体模型选型（直接用 Evo 2？fine-tune？还是训练 HSV 专属模型？）。
- 训练数据 pipeline。
- 实验筛选漏斗设计。

这份文档只描述要做什么，求解路径暂不展开。

---

## 论文拆解

把 HSV-1 OV 的设计视为大分子（基因组）设计问题，与晶体类大分子设计同构，自然落到 1 篇方法 + 1 篇应用。

### 方法论文（一篇）

**GFlowNet-style net for HSV-1 OV genome design**

- edit-MDP：state = 当前基因组改造序列；action = 一次基因组编辑（payload 插入 / 启动子替换 / 非必需基因删除等）。
- Reward：肿瘤选择性代理 + payload 表达约束 + 安全约束（神经毒性 / 复制控制）+ 多样性保持。
- 与 Evo 类 genome language model 混合作为先验，处理 ~152 kb 长基因组。

**接的研究线**：GFlowNet for biological sequences (Jain et al. ICML 2022)；Evo 1 (Nguyen, Poli, Durrant 2024) / Evo 2 (Brixi 2025)；HyenaDNA (Nguyen 2023) / Caduceus (Schiff 2024)；RFdiffusion 风格的约束条件生成；ProteinMPNN；T-VEC / ICP34.5 删除与启动子工程那条 OV 改造历史。

### 应用 / 验证（一篇）

由方法论文产生的 HSV-1 OV 候选，下选后做体外细胞系组 + 免疫健全鼠模型，作为应用论文（Nat Biotech / Cell 类候选）。

**接的研究线**：Andtbacka et al. 2015 T-VEC trial；HSV 临床前模型（Friedman / Markert 系列）。
