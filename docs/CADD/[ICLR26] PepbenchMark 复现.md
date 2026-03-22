# [ICLR26] PepbenchMark 复现

## 背景

当前，有4种主要的模型类型用于肽类分子建模：

| 模型类型 | 数据处理方式 | 类比 |
|---------|-----------------|------|
| Fingerprint (分子指纹) | 将分子转化为一串由 0 和 1 组成的结构特征代码。 | 像通过条形码识别商品。 |
| GNN (图神经网络) | 将分子视为由原子（点）和化学键（线）构成的网格图。 | 像通过三维地图理解城市结构。 |
| PLM (蛋白质语言模型) | 将氨基酸序列视为一种"语言"，学习序列中的语义。 | 像 ChatGPT 阅读文章一样阅读蛋白质。 |
| SMILES-based (文本模型) | 处理代表化学结构的字符串（如 CC(=O)OC...）。 | 像处理一串特定的密码字符。 |

但肽机器学习的数据源分散在不同的数据库和出版物中，迫使研究人员自行整理数据集；没有标准化的肽数据预处理流程；不同研究中的评估指标也存在显著差异。

## 在本地创建项目和环境

```pwsh
git clone https://github.com/ZGCI-AI4S-Pep/PepBenchmark.git
cd PepBenchmark

conda env create -f environment.yaml
conda activate pepbenchmark
```

由于该项目并没有发布到官方的 PyPI (pip) 仓库中，而是托管在 GitHub 上，我们需要手动安装pepbenchmark：

```pwsh
cd PepBenchmark
conda activate pepbenchmark
pip install -e .
```

**“把当前文件夹作为一个‘开发版’库安装到虚拟环境中。”**安装路径会被锁定在 **Conda 环境的 site-packages 目录**中。

## 加载数据集(PepBenchData)

