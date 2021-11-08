## 基于TaCL-BERT的中文命名实体识别及中文分词
**Paper**: TaCL: Improve BERT Pre-training with Token-aware Contrastive Learning

**Authors**: Yixuan Su, Fangyu Liu, Zaiqiao Meng, Lei Shu, Ehsan Shareghi, and Nigel Collier

论文主Github repo: [https://github.com/yxuansu/TaCL](https://github.com/yxuansu/TaCL)
### 环境配置
```yaml
python version == 3.8
pip install -r requirements.txt
```
### 模型结构
Chinese TaCL BERT + CRF

### Huggingface模型:

|Model Name|Model Address|
|:-------------:|:-------------:|
|Chinese (**cambridgeltl/tacl-bert-base-chinese**)|[link](https://huggingface.co/cambridgeltl/tacl-bert-base-chinese)|

### 使用范例:
```python
import torch
# initialize model
from transformers import AutoModel, AutoTokenizer
model_name = 'cambridgeltl/tacl-bert-base-chinese'
model = AutoModel.from_pretrained(model_name)
tokenizer = AutoTokenizer.from_pretrained(model_name)
# create input ids
text = "中文TaCL-BERT模型真强大！"
text = "[CLS] " + text + " [SEP]"
tokenized_token_list = tokenizer.tokenize(text)
input_ids = torch.LongTensor(tokenizer.convert_tokens_to_ids(tokenized_token_list)).view(1, -1)
# compute hidden states
representation = model(input_ids).last_hidden_state # [1, seqlen, embed_dim]
```


## 实验
### 一、实验数据集
1. 命名实体识别: (1) MSRA (2) OntoNotes (3) Resume (4) Weibo
2. 中文分词: (1) PKU (2) CityU (3) AS

### 二、下载数据集
```yaml
chmod +x ./download_benchmark_data.sh
./download_benchmark_data.sh
```

### 三、下载训练好的模型
```yaml
chmod +x ./download_checkpoints.sh
./download_checkpoints.sh
```

### 四、使用训练好的模型进行inference
```yaml
cd ./sh_folder/inference/
chmod +x ./inference_{}.sh
./inference_{}.sh
```
对于不同的数据集{}的取值为['msra', 'ontonotes', 'weibo', 'resume', 'pku', 'cityu', 'as']，相关参数的含义为:

```yaml
--saved_ckpt_path: 训练好的模型位置
--train_path: 训练集数据路径
--dev_path: 验证集数据路径
--test_path: 测试集数据路径
--label_path: 数据标签路径
--batch_size: inference时的batch size
```

### 五、测试集模型结果
使用提供的模型进行inference后，可以得到如下结果。
|     Dataset | Precision       |Recall|F1|
| :-------------: |:-------------:|:-----:|:-----:|
|MSRA|95.41|95.47|95.44|
|OntoNotes|81.88|82.98|82.42|
|Resume|96.48|96.42|96.45|
|Weibo|68.40|70.73|69.54|
|PKU|97.04|96.46|96.75|
|CityU|98.16|98.19|98.18|
|AS|96.51|96.99|96.75|

### 六、从头训练一个模型
```yaml
cd ./sh_folder/train/
chmod +x ./{}.sh
./{}.sh
```
对于不同的数据集{}的取值为['msra', 'ontonotes', 'weibo', 'resume', 'pku', 'cityu', 'as']，相关参数的含义为:

```yaml
--model_name: 中文TaCL BERT的模型名称(cambridgeltl/tacl-bert-base-chinese)
--train_path: 训练集数据路径
--dev_path: 验证集数据路径
--test_path: 测试集数据路径
--label_path: 数据标签路径
--learning_rate: 学习率
--number_of_gpu: 可使用的GPU数量
--number_of_runs: 重复试验次数
--save_path_prefix: 模型存储路径
```

**[Note 1]** 我们没有对模型进行任何调参，2e-5只是默认值。通过调整学习率也许可以获得更好的结果。

**[Note 2]** 实际的batch size等于gradient_accumulation_steps x number_of_gpu x batch_size_per_gpu。我们推荐将其设置为128。

**Inference**: 使用在./sh_folder/inference/路径中的sh进行inference。将--saved_ckpt_path设置为自己训练重新训练好的模型的路径。





