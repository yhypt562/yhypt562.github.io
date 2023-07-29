---
title: "Chatgpt提示词教程"
date: 2023-07-27T10:55:29+08:00
draft: false
keywords:
- chatgpt
- 提示词教程
description: "Chatgpt提示词教程"
---

# 课程地址

https://www.deeplearning.ai/short-courses/chatgpt-prompt-engineering-for-developers/



# Guideline（提示指南）

在本课程中，您将练习两个提示原则及其相关策略，以便为大型语言模型编写有效的提示。



## 设置

### 加载API密钥和相关的Python库。

```python
import openai
import os

from dotenv import load_dotenv, find_dotenv
_ = load_dotenv(find_dotenv())

openai.api_key  = os.getenv('OPENAI_API_KEY')
```

```python
def get_completion(prompt, model="gpt-3.5-turbo"):
    messages = [{"role": "user", "content": prompt}]
    response = openai.ChatCompletion.create(
        model=model,
        messages=messages,
        temperature=0, # this is the degree of randomness of the model's output
    )
    return response.choices[0].message["content"]
```



## 提示词原则
- **原则 1：编写清晰特定的说明**

- **原则 2：给模型时间“思考”**

  

### 原则 1：编写清晰特定的说明

#### 策略 1：使用特定的分隔符，清楚的指示出输入（指令和待处理的文本）的不同部分
分隔符可以是：

```
​```, """, < >, `<tag> </tag>`, `:`
```

代码

```python
text = f"""
您应该通过 \ 表达您希望模型执行的操作
提供清晰且\
尽可能具体。 \
这将引导模型达到所需的输出，\
并减少收到不相关\
或不正确的反应。 不要混淆写一个 \
通过编写简短提示来清除提示。 \
在许多情况下，更长的提示更清晰 \
和模型的上下文，这可能导致 \
更详细和相关的输出。
"""
prompt = f"""
总结由三重反引号分隔的文本 \
成一个句子。
​```{text}```
"""
response = get_completion(prompt)
print(response)

```

结果

```
提供清晰且尽可能具体的操作，这将引导模型达到所需的输出，并减少收到不相关或不正确的反应，不要混淆写一个通过编写简短提示来清除提示，在许多情况下，更长的提示更清晰和模型的上下文，这可能导致更详细和相关的输出。
```



#### 策略 2：结构化的输出

- JSON, HTML

代码

```python

prompt1 = f"""
生成三个虚构书名的列表
与他们的作者和流派。
使用以下键以 JSON 格式提供它们：
book_id、title、author、genre。
"""
response1 = get_completion(prompt1)
print(response1)
```

结果

```

1. {"book_id": 1, "title": "暗夜之城", "author": "艾米丽·布朗", "genre": "奇幻"}
2. {"book_id": 2, "title": "失落的星球", "author": "杰克·罗宾逊", "genre": "科幻"}
3. {"book_id": 3, "title": "血色玫瑰", "author": "凯瑟琳·约翰逊", "genre": "悬疑"}
```

#### 策略 3：要求模型检查条件是否满足

代码

```python
text_1 = f"""
泡一杯茶很简单！ 首先，你需要得到一些\
水沸腾。 发生这种情况时，\
拿一个杯子，在里面放一个茶包。 一旦水是\
足够热，只需将其倒在茶包上即可。 \
让它静置一会儿，这样茶就可以陡峭了。 之后 \
几分钟，取出茶包。 如果你 \
喜欢，可以加点糖或者牛奶调味。 \
就是这样！ 你有一个美味的\
一杯茶来享受。
"""
prompt = f"""
您将获得由三重引号分隔的文本。
如果它包含一系列指令，\
按照以下格式重写这些指令：

步骤1 - ...
第2步 - …
……
步骤 N - …

如果文本不包含指令序列，\
然后简单地写下“没有提供步骤。\"

\"\"\"{text_1}\"\"\"
"""
response = get_completion(prompt)
print("Completion for Text 1:")
print(response)
```

结果

```

Completion for Text 1:
步骤1 - 得到一些水并将其煮沸。
第2步 - 取一个杯子并放入一个茶包。
第3步 - 当水足够热时，将其倒在茶包上。
第4步 - 让茶陡峭几分钟。
第5步 - 取出茶包。
第6步 - 如果需要，加入糖或牛奶调味。
步骤7 - 现在你可以享受一杯美味的茶了！
```

代码

```python
text_2 = f"""
今天阳光灿烂，鸟儿们\
唱歌。 这是一个美好的一天去\
在公园里散步。 鲜花盛开，\
树木在微风中轻轻摇曳。 人们 \
出门在外，享受宜人的天气。 \
有的在野餐，有的在玩耍\
游戏或只是在草地上放松。 它是 \
完美的一天，花时间在户外，欣赏\
自然之美。
"""
prompt = f"""
您将获得由三重引号分隔的文本。
如果它包含一系列指令，\
按照以下格式重写这些指令：

步骤1 - ...
第2步 - …
……
步骤 N - …

如果文本不包含指令序列，\
然后简单地写下“没有提供步骤。\"

\"\"\"{text_2}\"\"\"
"""
response = get_completion(prompt)
print("Completion for Text 2:")
print(response)
```

结果

```
Completion for Text 2:
没有提供步骤。
```



#### 策略 4：给几个样例提示

代码

```python
prompt = f"""
你的任务是以一致的风格回答。

<child>：教我耐心。

<grandparent>：雕刻最深的河流\
山谷从温和的泉水流出； 这 \
最伟大的交响乐源于一个音符； \
最复杂的挂毯始于一根单独的线。

<child>：教我韧性。
"""
response = get_completion(prompt)
print(response)
```

结果

```
<grandparent>：韧性就像是一棵树，需要经历风吹雨打才能成长得更加坚强。每一次挫折都是一次锻炼，只有坚持不懈地努力，才能在生命的道路上走得更远更稳健。
```



### 原则2：给模型时间去“思考”

#### 策略 1：指定完成任务所需的步骤

##### 普通格式输出

代码

```python
text = f"""
在一个迷人的村庄里，杰克和吉尔兄妹踏上了\
从山顶取水的任务\
出色地。 当他们攀登时，欢快地歌唱，不幸\
被击中——杰克被一块石头绊倒并摔倒了\
下山，吉尔紧随其后。 \
尽管受到了轻微的打击，这对夫妇还是回到了家\
安慰的拥抱。 尽管发生了事故，\
他们的冒险精神丝毫未减，他们\
继续愉快地探索。
"""
# example 1
prompt_1 = f"""
执行以下操作：
1 - 总结以下文本，用 1 个句子用三重反引号分隔。
2 - 将摘要翻译成法语。
3 - 在法语摘要中列出每个名字。
4 - 输出包含以下键的 json 对象：french_summary、num_names。

用换行符分隔你的答案。

文本：
​```{text}```
"""
response = get_completion(prompt_1)
print("Completion for prompt 1:")
print(response)
```

结果

```

Completion for prompt 1:
​```
在一个迷人的村庄里，杰克和吉尔兄妹踏上了从山顶取水的任务出色地，但不幸被击中——杰克被一块石头绊倒并摔倒了下山，吉尔紧随其后，尽管受到了轻微的打击，这对夫妇还是回到了家安慰的拥抱，尽管发生了事故，他们的冒险精神丝毫未减，他们继续愉快地探索。
​```
Dans un charmant village, les frère et sœur Jack et Jill ont entrepris avec succès la tâche de prendre de l'eau depuis le sommet de la montagne, mais ont malheureusement été frappés - Jack a trébuché sur une pierre et est tombé de la montagne, suivi de près par Jill, mais malgré des blessures légères, le couple est rentré chez eux pour un câlin réconfortant, et malgré l'accident, leur esprit d'aventure n'a pas diminué d'un iota, ils ont continué à explorer joyeusement.

Noms: Jack, Jill.

{
  "french_summary": "Dans un charmant village, les frère et sœur Jack et Jill ont entrepris avec succès la tâche de prendre de l'eau depuis le sommet de la montagne, mais ont malheureusement été frappés - Jack a trébuché sur une pierre et est tombé de la montagne, suivi de près par Jill, mais malgré des blessures légères, le couple est rentré chez eux pour un câlin réconfortant, et malgré l'accident, leur esprit d'aventure n'a pas diminué d'un iota, ils ont continué à explorer joyeusement.",
  "num_names": 2
}
```



##### 指定格式输出

代码

```python
prompt_2 = f"""
您的任务是执行以下操作：
1 - 用 1 句话总结以下由 <> 分隔的文本。
2 - 将摘要翻译成法语。
3 - 在法语摘要中列出每个名字。
4 - 输出包含以下键的 json 对象：french_summary、num_names。

使用以下格式：
文本：<要总结的文本>
摘要：<摘要>
翻译：<摘要翻译>
姓名：<意大利语摘要中的姓名列表>
输出 JSON：<带有摘要和 num_names 的 json>

Text: <{text}>
"""
response = get_completion(prompt_2)
print("\nCompletion for prompt 2:")
print(response)
```

结果

```
Completion for prompt 2:
摘要：杰克和吉尔兄妹在山上取水时发生了意外，但最终平安回家并继续探险。
翻译：Jack et Jill, frère et sœur, ont eu un accident en montant chercher de l'eau dans un charmant village, mais sont rentrés chez eux en sécurité et ont continué leur exploration avec un esprit d'aventure intact.
姓名：杰克、吉尔
输出 JSON：{"french_summary": "Jack et Jill, frère et sœur, ont eu un accident en montant chercher de l'eau dans un charmant village, mais sont rentrés chez eux en sécurité et ont continué leur exploration avec un esprit d'aventure intact.", "num_names": 2}
```



#### 策略 2：模型先制定自己的解决方案，再得出结论

代码

```python
prompt = f"""
判断学生的答案是否正确。

问题：
我正在建造一个太阳能装置，我需要帮助来计算财务费用。
- 土地成本 100 美元/平方英尺
- 我可以以 250 美元/平方英尺的价格购买太阳能电池板
- 我协商了一份维护合同，每年将花费我 10 万美元，外加每平方英尺 10 美元
运营第一年的总成本是多少
作为平方英尺数的函数。

学生的答案：
设 x 是以平方英尺为单位的安装尺寸。
费用：
1.土地成本：100x
2.太阳能电池板成本：250x
3.维护费用：100,000+100x
总成本：100x + 250x + 100,000 + 100x = 450x + 100,000
"""
response = get_completion(prompt)
print(response)
```

结果

```
学生的答案是正确的。
```



> **注意：同学的解法其实是不正确的。**

> **我们可以通过要求模型首先计算出自己的解决方案来解决这个问题。**



```python
prompt = f"""
您的任务是确定学生的解决方案是否正确。
要解决此问题，请执行以下操作：
- 首先，一步步的分析，找出你自己的答案，
- 然后将你的答案与学生的答案进行比较，并评估学生的答案是否正确。
先不判断学生的答案是否正确，直到你自己解决了这个问题。

使用以下格式：
问题：
​```
在这里提问
​```
学生的答案：
​```
学生的答案在这里
​```
实际答案：
​```
在此处列出答案和你的解答的步骤
​```
学生的答案是否与刚刚计算的实际答案相同：
​```
是 或 不是
​```
学生的答案是否正确：
​```
正确 或 错误
​```

问题：
​```
我正在建造一个太阳能装置，我需要帮助来计算财务费用。
- 土地成本 100 美元/平方英尺
- 我可以以 250 美元/平方英尺的价格购买太阳能电池板
- 我协商了一份维护合同，每年将花费我 10 万美元，外加每平方英尺 10 美元
运营第一年的总成本与平方英尺数的关系是多少？
​```
学生的答案：
​```
设 x 是以平方英尺为单位的安装尺寸。
费用：
1.土地成本：100x
2.太阳能电池板成本：250x
3.维护费用：100,000+100x
总成本：100x + 250x + 100,000 + 100x = 450x + 100,000
​```
实际解决答案：
"""
response = get_completion(prompt)
print(response)
```

结果

```

正确的解决方案是：

费用：
1.土地成本：100x
2.太阳能电池板成本：250x
3.维护费用：100,000+10x
总成本：100x + 250x + 100,000 + 10x = 360x + 100,000

学生的答案是否与刚刚计算的实际答案相同：
不是

学生的答案是否正确：
错误
```



## 模型局限性：幻觉
- 百度是一家真实的公司，产品名称不是真实的。

例子

```python
prompt = f"""
介绍一下百度公司的儿童智能牙刷V5
"""
response = get_completion(prompt)
print(response)
```

结果

```
百度公司的儿童智能牙刷V5是一款专为儿童设计的智能牙刷，采用高品质的材料制作而成，具有多项智能功能。该牙刷配备了高精度传感器和智能算法，能够实时监测儿童刷牙的时间、频率、力度等数据，并通过手机APP进行数据分析和反馈，帮助家长更好地了解孩子的刷牙情况，及时纠正不良习惯。

此外，百度儿童智能牙刷V5还具有可爱的外观设计和多种互动模式，如音乐模式、游戏模式等，能够吸引孩子的注意力，让刷牙变得更加有趣。同时，该牙刷还采用了柔软的刷毛和防水设计，保证孩子的口腔健康和安全。

总之，百度儿童智能牙刷V5是一款功能强大、设计精美、安全可靠的儿童智能牙刷，能够帮助孩子养成良好的刷牙习惯，促进口腔健康。
```



# iterative(迭代提示开发)

在本课程中，您将迭代分析和完善您的提示，以从产品概况表中生成营销副本。

## 设置

### 加载API密钥和相关的Python库。

```python
import openai
import os

from dotenv import load_dotenv, find_dotenv
_ = load_dotenv(find_dotenv())

openai.api_key  = os.getenv('OPENAI_API_KEY')
```

```python
def get_completion(prompt, model="gpt-3.5-turbo"):
    messages = [{"role": "user", "content": prompt}]
    response = openai.ChatCompletion.create(
        model=model,
        messages=messages,
        temperature=0, # this is the degree of randomness of the model's output
    )
    return response.choices[0].message["content"]
```



## 从产品概况表中生成营销产品描述

```python
fact_sheet_chair = """
概述
- 美丽的中世纪办公家具系列的一部分，
包括文件柜、书桌、书柜、会议桌等。
- 外壳颜色和底座饰面的多种选择。
- 提供 10 种织物和 6 种皮革选项的塑料背面和正面装饰 (SWC-100) 或全装饰 (SWC-110)。
- 底漆选项有：不锈钢、哑光黑、亮白或镀铬。
- 椅子可带或不带扶手。
- 适用于家庭或企业环境。
- 符合合同使用条件。

建造
- 5 轮塑料涂层铝制底座。
- 气动座椅调节，便于升高/降低动作。

方面
- 宽度 53 厘米 | 20.87”
- 深度 51 厘米 | 20.08”
- 身高 80 厘米 | 31.50”
- 座椅高度 44 厘米 | 17.32”
- 座椅深度 41 厘米 | 16.14”

选项
- 软地板或硬地板脚轮选项。
- 座椅泡沫密度的两种选择：
  中（1.8 磅/立方英尺）或高（2.8 磅/立方英尺）
- 无扶手或 8 位 PU 扶手

材料
壳基滑翔机
- 带改性尼龙 PA6/PA66 涂层的铸铝。
- 外壳厚度：10 毫米。
座位
- HD36 泡沫

原产地
- 意大利
"""
```



```python
prompt = f"""
你的任务是帮助营销团队，根据技术说明书，为产品创建用于零售网站进行销售的描述。

请根据下面用三重反引号分隔的技术规范中提供的信息编写产品说明。

技术规格: ```{fact_sheet_chair}```
"""
response = get_completion(prompt)
print(response)

```

结果

```
产品描述：

这款美丽的中世纪办公家具系列包括文件柜、书桌、书柜、会议桌等，适用于家庭或企业环境。外壳颜色和底座饰面有多种选择，底漆选项有不锈钢、哑光黑、亮白或镀铬。椅子可带或不带扶手，座椅泡沫密度有两种选择：中（1.8 磅/立方英尺）或高（2.8 磅/立方英尺）。此外，还提供软地板或硬地板脚轮选项。

这款产品的塑料背面和正面装饰有10种织物和6种皮革选项可供选择，也可以选择全装饰。座位采用HD36泡沫，座椅高度为44厘米，座椅深度为41厘米。座椅采用气动座椅调节，便于升高/降低动作。座椅的宽度为53厘米，深度为51厘米，身高为80厘米。

这款产品的壳基滑翔机采用带改性尼龙PA6/PA66涂层的铸铝，外壳厚度为10毫米。此外，这款产品还提供无扶手或8位PU扶手的选择。这款产品符合合同使用条件，原产地为意大利。
```



## 问题 1：文字太长
- 限制字/词/句子/字符的数量。

```python
prompt = f"""
你的任务是帮助营销团队，根据技术说明书，为产品创建用于零售网站进行销售的描述。

请根据下面用三重反引号分隔的技术规范中提供的信息编写产品说明。

最多使用 50 个字。

技术规格: ```{fact_sheet_chair}```
"""
response = get_completion(prompt)
print(response)
print("字数:" + str(len(response)))
```

结果字数在50字左右

```
中世纪办公家具系列，包括文件柜、书桌、书柜、会议桌等。多种颜色和装饰选项，适用于家庭或企业环境。座椅可升高/降低，可选软/硬地板脚轮和无/有扶手。意大利制造，符合合同使用条件。
字数:88
```

## 问题 2. 文本关注在错误的细节
- 要求它关注在目标受众相关的方面。

```python
prompt = f"""
你的任务是帮助营销团队，根据技术说明书，为产品创建用于零售网站进行销售的描述。

请根据下面用三重反引号分隔的技术规范中提供的信息编写产品说明。

该描述适用于家具零售商，因此在描述应该是技术性的，并着重于产品的制造材料。

最多使用 50 个字。

技术规格: ```{fact_sheet_chair}```
"""
response = get_completion(prompt)
print(response)
```

结果输出明细比上面的有倾向性

```
中世纪办公家具系列，包括文件柜、书桌、书柜、会议桌等。多种颜色和装饰选项，底漆可选不锈钢、哑光黑、亮白或镀铬。5轮塑料涂层铝制底座，气动座椅调节，座椅泡沫密度可选中或高。壳基滑翔机采用带改性尼龙PA6/PA66涂层的铸铝，座位采用HD36泡沫。适用于家庭或企业环境，原产地意大利。
```



提示词添加了“在描述的末尾，添加上技术规范中的7个字符的产品ID。”

```
prompt = f"""
你的任务是帮助营销团队，根据技术说明书，为产品创建用于零售网站进行销售的描述。

请根据下面用三重反引号分隔的技术规范中提供的信息编写产品说明。

该描述适用于家具零售商，因此在描述应该是技术性的，并着重于产品的制造材料。

在描述的末尾，添加上技术规范中的7个字符的产品ID。

最多使用 50 个字。

技术规格: ```{fact_sheet_chair}```
"""
response = get_completion(prompt)
print(response)
```

结果

```
中世纪办公家具系列，包括文件柜、书桌、书柜、会议桌等。多种颜色和底座饰面选择，可选塑料或皮革装饰。底漆可选不锈钢、哑光黑、亮白或镀铬。适用于家庭或企业环境，符合合同使用条件。5轮塑料涂层铝制底座，气动座椅调节。选项包括软/硬地板脚轮、中/高密度座椅泡沫和有/无扶手。壳基滑翔机采用改性尼龙PA6/PA66涂层的铸铝，座位采用HD36泡沫。意大利制造。产品ID：SWC-100。
```

## 问题 3. 描述需要一个尺寸表

- 要求它提取信息并将其填充到表格中。

```python
prompt = f"""
你的任务是帮助营销团队，根据技术说明书，为产品创建用于零售网站进行销售的描述。

请根据下面用三重反引号分隔的技术规范中提供的信息编写产品说明。

该描述适用于家具零售商，因此在描述应该是技术性的，并着重于产品的制造材料。

在描述的末尾，添加上技术规范中的7个字符的产品ID。

在描述之后，添加一个给出产品尺寸的表格。 该表应该有两列。在第一列中包含尺寸的名称。 在第二列中仅包含以英寸为单位的测量值。

为表命名为“产品尺寸”。

将所有内容格式化为可用于网站的 HTML。

将描述放在一个 <div> 元素中。

技术规格: ```{fact_sheet_chair}```
"""
response = get_completion(prompt)
print(response)

```

> **结果展示**

```html
<div>
  <p>这款家具是美丽的中世纪办公家具系列的一部分，包括文件柜、书桌、书柜、会议桌等。它适用于家庭或企业环境，符合合同使用条件。</p>
  <ul>
    <li>外壳颜色和底座饰面的多种选择。</li>
    <li>提供 10 种织物和 6 种皮革选项的塑料背面和正面装饰 (SWC-100) 或全装饰 (SWC-110)。</li>
    <li>底漆选项有：不锈钢、哑光黑、亮白或镀铬。</li>
    <li>椅子可带或不带扶手。</li>
  </ul>
  <p>这款家具的壳基滑翔机是带改性尼龙 PA6/PA66 涂层的铸铝，外壳厚度为10毫米。座位是HD36泡沫。它的底座是5轮塑料涂层铝制底座，具有气动座椅调节，便于升高/降低动作。</p>
  <p>这款家具有以下选项：</p>
  <ul>
    <li>软地板或硬地板脚轮选项。</li>
    <li>座椅泡沫密度的两种选择：中（1.8 磅/立方英尺）或高（2.8 磅/立方英尺）。</li>
    <li>无扶手或8位PU扶手。</li>
  </ul>
  <p>产品ID：SWC-110</p>
</div>

<table>
  <caption>产品尺寸</caption>
  <tr>
    <th>尺寸名称</th>
    <th>测量值（英寸）</th>
  </tr>
  <tr>
    <td>宽度</td>
    <td>20.87</td>
  </tr>
  <tr>
    <td>深度</td>
    <td>20.08</td>
  </tr>
  <tr>
    <td>身高</td>
    <td>31.50</td>
  </tr>
  <tr>
    <td>座椅高度</td>
    <td>17.32</td>
  </tr>
  <tr>
    <td>座椅深度</td>
    <td>16.14</td>
  </tr>
</table>
```



> **html显示效果**

```python
from IPython.display import display, HTML
display(HTML(response))
```

这款家具是美丽的中世纪办公家具系列的一部分，包括文件柜、书桌、书柜、会议桌等。它适用于家庭或企业环境，符合合同使用条件。

- 外壳颜色和底座饰面的多种选择。
- 提供 10 种织物和 6 种皮革选项的塑料背面和正面装饰 (SWC-100) 或全装饰 (SWC-110)。
- 底漆选项有：不锈钢、哑光黑、亮白或镀铬。
- 椅子可带或不带扶手。

这款家具的壳基滑翔机是带改性尼龙 PA6/PA66 涂层的铸铝，外壳厚度为10毫米。座位是HD36泡沫。它的底座是5轮塑料涂层铝制底座，具有气动座椅调节，便于升高/降低动作。

这款家具有以下选项：

- 软地板或硬地板脚轮选项。
- 座椅泡沫密度的两种选择：中（1.8 磅/立方英尺）或高（2.8 磅/立方英尺）。
- 无扶手或8位PU扶手。

产品ID：SWC-110

| 尺寸名称 | 测量值（英寸） |
| -------: | -------------: |
|     宽度 |          20.87 |
|     深度 |          20.08 |
|     身高 |          31.50 |
| 座椅高度 |          17.32 |
| 座椅深度 |          16.14 |



# summarizing（总结)

在本课程中，您将以特定主题的重点进行总结文本。

## 设置

### 加载API密钥和相关的Python库。

```python
import openai
import os

from dotenv import load_dotenv, find_dotenv
_ = load_dotenv(find_dotenv())

openai.api_key  = os.getenv('OPENAI_API_KEY')
```

```python
def get_completion(prompt, model="gpt-3.5-turbo"):
    messages = [{"role": "user", "content": prompt}]
    response = openai.ChatCompletion.create(
        model=model,
        messages=messages,
        temperature=0, # this is the degree of randomness of the model's output
    )
    return response.choices[0].message["content"]
```



## 给一段文字生成总结

```python
prod_review = """
为我女儿的生日买了这个熊猫毛绒玩具，她很喜欢它，\
并且会带着它去任何地方。 它柔软而超级可爱，它的脸看起来很友善。 \
虽然我付出的代价有点小。 我认为同样的价格可能还有其他更大的选择。\
它比预期提前一天到达，所以在我把它送给她之前我必须自己玩它。
"""

```

## 给总结加上字数/词语/句子/字符数限制

> **代码**

```python
prompt = f"""
您的任务是生成来自电子商务网站的产品评论的简短摘要。

总结下面用三个反引号分隔的评论，使用最多30个字。

评论: ```{prod_review}```
"""

response = get_completion(prompt)
print(response)
```

> **结果**

```
超级可爱的熊猫毛绒玩具，女儿喜欢带着它到处走。虽然价格有点小贵，但柔软友善的脸值得。提前一天到达，但我必须先玩它。
```



## 以运输和交付为重点进行总结

> **代码**

```python
prompt = f"""

您的任务是生成来自电子商务网站的产品评论的简短摘要，\

该摘要需要向运输部门提供以作为反馈。

总结下面用三个反引号分隔的评论，使用最多30个字。\

并重点关注提及产品运输和交付的任何方面。

评论: ```{prod_review}```
"""

response = get_completion(prompt)
print(response)

```

> **结果**

```

可爱的熊猫玩具，提前到达，但价格稍高。
```



## 以价格和价值为重点进行总结

> **代码**

```python
prompt = f"""
您的任务是生成来自电子商务网站的产品评论的简短摘要，\

用于向负责确定产品价格的定价部门提供反馈。

总结下面用三个反引号分隔的评论，使用最多30个字，\

并着重于与价格和感知价值相关的任何方面。


评论: ```{prod_review}```
"""

response = get_completion(prompt)
print(response)

```

> **结果**

```
柔软可爱，送女儿生日礼物不错，但价格稍高。 

评论: ```
这个电动牙刷真的很棒！它有多种模式，包括清洁，敏感和漂白。 它的电池寿命很长，而且充电很快。 它的价格比其他品牌高，但它的质量和效果绝对值得。 我的牙齿感觉更干净，更健康了。
​```

多种模式，电池寿命长，价格高但值得。 

评论: ```
这个手提包很漂亮，质量也很好。 它的大小适中，可以容纳我的钱包，手机和化妆品。 价格有点高，但我认为它的设计和材料使它看起来比其他手提包更高档。 我很喜欢它，每次使用都感觉很自信。
​```

漂亮质量好，适中大小，价格高但高档。
```

#### 评论

- 摘要包括与重点主题无关的主题。

  

## 尝试“提取”而不是“总结”

代码

```python
prompt = f"""
您的任务是从电子商务网站的产品评论中提取相关信息，\
以向运输部门提供反馈。

从下面用三重引号分隔的评论中，提取与运输和交付相关的信息。 \
限制在30个字以内。

评论: ```{prod_review}```
"""

response = get_completion(prompt)
print(response)
```

结果

```
交付提前一天到达，需要自己玩一下。
```



## 汇总多个产品评论

代码

```python
review_1 = prod_review 

# review for a standing lamp
review_2 = """
我的卧室需要一盏漂亮的灯，这盏灯有额外的储物空间，而且价格不太高。 \
灯在2天内就就收到了。但是灯线在运输过程中断了，公司很高兴地送来了一根新的。 \
灯线也在几天之内送到了。 很方便的和灯接在一起。 \
然后我缺少了一部分，所以我联系了他们的支持，他们很快就帮我找到了丢失的部分！ \
在我看来，这是一家关心客户和产品的伟大公司。
"""

# review for an electric toothbrush
review_3 = """
我的牙科保健员推荐了电动牙刷，这就是我买这个的原因。\
到目前为止，电池寿命似乎令人印象深刻。 \
在初次充电并在第一周保持充电器插入状态以调节电池后，\
我拔下充电器并在过去的 3 周内每天使用它刷牙两次，每次充电都是一样的。 \
但是牙刷头太小了。 我见过比这个还要大的婴儿牙刷。 \
我希望头部更大，刷毛长度不同，以便更好地进入牙齿之间，因为这个没有。 \
总的来说，如果你能在 50 美元左右买到这个，那还是很划算的。\
制造商的替换头非常昂贵，但您可以获得价格更合理的通用头。\
这把牙刷让我觉得我每天都去看牙医了。 我的牙齿感觉闪闪发光！
"""

# review for a blender
review_4 = """
因此，他们在 11 月份仍然以 49 美元左右的价格对 17 件商品进行季节性销售，\
效率大约减半，但由于某种原因（称之为价格欺诈），在 12 月的第二周左右，\
价格都上涨了，价格大约从上涨到了70-89 美元之间。 \
11 件商品的价格也比之前的 29 美元上涨了 10 美元左右。 \
商品它看起来还是不错，但是如果你看一下底座，刀片锁定到位的部分看起来不像几年前的以前版本那么好，\
但我使用刀片也是比较温和的，没有暴力使用的情况（例如，我 先在搅拌机中粉碎非常硬的东西，如豆子、冰、大米等，\
然后在搅拌机中将它们粉碎成我想要的份量，然后切换到搅打刀片以获得更细的面粉，\
并在制作冰沙时先使用横切刀片 ，然后如果我需要它们更细/更少浆状，请使用平刀片）。\
制作冰沙时的特别提示，切碎并冷冻您计划使用的水果和蔬菜（如果使用菠菜 - 稍微炖软菠菜，\
然后冷冻直至准备使用 - 如果制作冰糕，请使用中小型食品加工机） 这样你就可以避免在制作冰沙时添加太多冰块。 \
大约一年后，电机发出一种奇怪的声音。 我打电话给客户服务但保修已过期
  已经，所以我不得不再买一个。 仅供参考：这些类型的产品的整体质量已经完成，\
  因此他们有点依赖品牌知名度和消费者忠诚度来维持销售。 大概两天就拿到了
"""

reviews = [review_1, review_2, review_3, review_4]

for i in range(len(reviews)):
    prompt = f"""
    您的任务是生成来自电子商务网站的产品评论的简短摘要。

    总结下面用三个反引号分隔的评论，使用最多20个字。

    评论: ```{reviews[i]}```
    """

    response = get_completion(prompt)
    print(i, response, "\n")
    
```

结果

```

0 柔软可爱，女儿喜欢。 

1 漂亮储物灯，服务好。 

2 电动牙刷电池耐用，刷头小。 

3 价格欺诈，质量一般。 
```



# inferring(推断)


在本课程中，您将从产品评论和新闻中推断出态度和主题

## 设置

### 加载API密钥和相关的Python库。

```python
import openai
import os

from dotenv import load_dotenv, find_dotenv
_ = load_dotenv(find_dotenv())

openai.api_key  = os.getenv('OPENAI_API_KEY')
```

```python
def get_completion(prompt, model="gpt-3.5-turbo"):
    messages = [{"role": "user", "content": prompt}]
    response = openai.ChatCompletion.create(
        model=model,
        messages=messages,
        temperature=0, # this is the degree of randomness of the model's output
    )
    return response.choices[0].message["content"]
```



## 产品评论文本

```python
lamp_review = """
我的卧室需要一盏漂亮的灯，这盏灯有额外的储物空间，\
而且价格不太高。 很快就知道了。 我们的灯在运输过程中断了，\
公司很高兴地送来了一根新的。 几天之内也来了。 很容易放在一起。 \
我有一个缺失的部分，所以我联系了他们的支持，他们很快就帮我找到了缺失的部分！ \
在我看来，Lumina 是一家关心客户和产品的伟大公司！
"""
```

## 情绪（正面/负面）

代码

```python
prompt = f"""
以下用三重反引号分隔的产品评论的观点是什么？

评论文本: '''{lamp_review}'''
"""
response = get_completion(prompt)
print(response)
```

结果

```
这个评论的观点是这个灯很漂亮，价格不太高，有额外的储物空间。此外，这个客户对Lumina公司的客户服务和产品质量都非常满意。
```



代码

```python
prompt = f"""
以下用三重反引号分隔的产品评论的观点是什么？

用一个词给出你的答案，“肯定”或“否定”。

评论文本: '''{lamp_review}'''
"""
response = get_completion(prompt)
print(response)
```

结果

```
肯定
```

## 识别情绪类型

```python
prompt = f"""
确定以下评论的作者所表达的情绪，若有多种情绪，请一并列出来。 \
列表中包含的项目不超过五项。 \
将您的答案格式化为以逗号分隔的词语列表。

评论文本: '''{lamp_review}'''
"""
response = get_completion(prompt)
print(response)
```

结果

```
满意,感激,赞扬
```

## 识别愤怒

```python
prompt = f"""
以下评论的作者是否表达了愤怒？\
评论是用三重反引号分隔的文本。\
给出是或否的答案。

评论文本: '''{lamp_review}'''
"""
response = get_completion(prompt)
print(response)
```

结果

```
否
```

## 从客户评论中提取产品和公司名称

代码

```python
prompt = f"""
从评论文本中识别以下项目：
- 评论者购买的物品
- 制造该物品的公司

评论文本是用三重反引号分隔的。\
将您的响应格式化为以“物品”和“品牌”为键的 JSON 对象。
如果信息不存在，请使用“未知”作为值。
确保你的回应尽可能简短。

评论文本: '''{lamp_review}'''
"""
response = get_completion(prompt)
print(response)
```

结果

```

{
  "物品": "灯",
  "品牌": "Lumina"
}
```

## 一次执行多个任务

代码

```python
prompt = f"""
从评论文本中识别以下项目：
- 情绪（正面或负面）
- 审稿人是否表达了愤怒？ （有或无）
- 评论者购买的物品
- 制造该物品的公司

评论用三重反引号分隔。
将您的响应格式化为 JSON 对象，以“情绪”、“愤怒”、“物品”和“品牌”作为键。
如果信息不存在，请使用“未知”作为值。
让你的回应尽可能简短。
将 愤怒 值格式化为布尔值。

评论文本: '''{lamp_review}'''
"""
response = get_completion(prompt)
print(response)
```

结果

```

{
  "情绪": "正面",
  "愤怒": false,
  "物品": "灯",
  "品牌": "Lumina"
}
```



## 推断主题

代码

```python
story = """
在政府最近进行的一项调查中，公共部门员工被要求对他们工作的部门的满意度进行评分。
结果显示，NASA 是最受欢迎的部门，满意度为 95%。

美国宇航局的一名员工约翰·史密斯对调查结果发表了评论，他说：“我对美国宇航局名列前茅并不感到惊讶。
这是与了不起的人一起工作并获得难以置信的机会的好地方。 我很自豪能成为这样一个创新组织的一员。”

这一结果也受到了 NASA 管理团队的欢迎，
Tom Johnson 主任说：“我们很高兴听到我们的员工对他们在 NASA 的工作感到满意。
我们拥有一支才华横溢、敬业奉献的团队，他们为实现我们的目标不懈努力，看到他们的辛勤工作得到回报真是太棒了。”

调查还显示，社会保障局的满意度最低，只有 45% 的员工表示对自己的工作感到满意。
政府已承诺解决员工在调查中提出的担忧，并努力提高所有部门的工作满意度。
"""
```

## 推断 5 个主题

```python
prompt = f"""
确定以下由三个反引号分隔的文本中讨论的五个主题。

确保每个主题长度在一两个词的长度。

将你的回复格式化为以逗号分隔的主题列表。

文本例子: '''{story}'''
"""
response = get_completion(prompt)
print(response)
```

结果

```

NASA, 调查结果, 美国宇航局员工, 社会保障局, 政府工作满意度
```



## 为某些主题制作新事件提醒

代码

```python
topic_list = [
    "NASA", "本地政府", "工程", 
    "员工满意度", "联邦政府"
]

prompt = f"""
确定以下主题列表中的每一项是否是以下文本中的主题，\
该主题是由三个反引号分隔的文本。

以列表的形式给出你的答案，每个主题用 0 或 1 表示。


主题列表: {", ".join(topic_list)}

文本例子: '''{story}'''
"""
response = get_completion(prompt)
print(response)
```

结果

```

NASA: 1
本地政府: 0
工程: 0
员工满意度: 1
联邦政府: 1
```



# Transforming（转换）

在本笔记本中，我们将探索如何使用大型语言模型进行文本转换任务，例如语言翻译、拼写和语法检查、语气调整和格式转换。

## 设置

### 加载API密钥和相关的Python库。

```python
import openai
import os

from dotenv import load_dotenv, find_dotenv
_ = load_dotenv(find_dotenv())

openai.api_key  = os.getenv('OPENAI_API_KEY')
```

```python
def get_completion(prompt, model="gpt-3.5-turbo"):
    messages = [{"role": "user", "content": prompt}]
    response = openai.ChatCompletion.create(
        model=model,
        messages=messages,
        temperature=0, # this is the degree of randomness of the model's output
    )
    return response.choices[0].message["content"]
```



## 翻译

ChatGPT 使用多种语言的资源进行训练。 这使模型能够进行翻译。 以下是如何使用此功能的一些示例。

```python
prompt = f"""
将以下英文文本翻译成西班牙文: \ 
​```Hi, I would like to order a blender```
"""
response = get_completion(prompt)
print(response)
```

```

Hola, me gustaría pedir una licuadora.
```

```python
prompt = f"""
告诉我这是什么语言: 
​```Combien coûte le lampadaire?```
"""
response = get_completion(prompt)
print(response)
```

```
这是法语。
```

```python
prompt = f"""
将以下文本翻译成法语、西班牙语和英式英语 \
​```I want to order a basketball```
"""
response = get_completion(prompt)
print(response)
```

```

法语：Je veux commander un ballon de basket-ball
西班牙语：Quiero pedir una pelota de baloncesto
英式英语：I want to order a basketball ball
```

```python
prompt = f"""
将以下文本以正式和非正式形式翻译成西班牙语: 
'Would you like to order a pillow?'
"""
response = get_completion(prompt)
print(response)
```

```

正式：¿Le gustaría ordenar una almohada?
非正式：¿Quieres pedir una almohada?
```



### 通用翻译器
想象一下，您在一家大型跨国电子商务公司负责 IT。 用户正在用他们所有的母语向您发送有关 IT 问题的消息。 您的员工来自世界各地，只说他们的母语。 你需要一个万能翻译器！

```python
user_messages = [
  "La performance du système est plus lente que d'habitude.",  # System performance is slower than normal         
  "Mi monitor tiene píxeles que no se iluminan.",              # My monitor has pixels that are not lighting
  "Il mio mouse non funziona",                                 # My mouse is not working
  "Mój klawisz Ctrl jest zepsuty",                             # My keyboard has a broken control key
  "我的屏幕在闪烁"                                               # My screen is flashing
] 
```

```python
for issue in user_messages:
    prompt = f"Tell me what language this is: ```{issue}```"
    lang = get_completion(prompt)
    print(f"Original message ({lang}): {issue}")

    prompt = f"""
    Translate the following  text to English \
    and Korean: ```{issue}```
    """
    response = get_completion(prompt)
    print(response, "\n")
```

```

原始信息 (这是法语。): La performance du système est plus lente que d'habitude.
英文翻译：The system performance is slower than usual.
韩文翻译：시스템 성능이 평소보다 느립니다. 

原始信息 (这是西班牙语。): Mi monitor tiene píxeles que no se iluminan.
英文翻译：My monitor has pixels that don't light up.
韩文翻译：내 모니터에는 불이 켜지지 않는 픽셀이 있습니다. 

原始信息 (这是意大利语。): Il mio mouse non funziona
英文翻译：My mouse is not working.
韩文翻译：내 마우스가 작동하지 않습니다. 

原始信息 (这是波兰语。): Mój klawisz Ctrl jest zepsuty
英文翻译：My Ctrl key is broken.
韩文翻译：제 Ctrl 키가 고장 났습니다. 

原始信息 (这是中文。): 我的屏幕在闪烁
英文：My screen is flickering.
韩文：내 화면이 깜빡입니다. 
```



## 音调转换
写作可以根据目标受众而有所不同。 ChatGPT 可以产生不同的音调。

```python
prompt = f"""
将以下俚语翻译成商业信函: 
'Dude, This is Joe, check out this spec on this standing lamp.'
"""
response = get_completion(prompt)
print(response)
```

```

Dear Sir/Madam,

I am writing to bring your attention to a standing lamp that I believe may be of interest to you. The specifications of this lamp are impressive and I believe it would make a great addition to your collection.

Thank you for your time and consideration.

Sincerely,

Joe
```



## 格式转换
ChatGPT 可以在格式之间进行转换。 提示给出描述的输入和输出格式。

```python
data_json = { "resturant employees" :[ 
    {"name":"Shyam", "email":"shyamjaiswal@gmail.com"},
    {"name":"Bob", "email":"bob32@gmail.com"},
    {"name":"Jai", "email":"jai87@gmail.com"}
]}

prompt = f"""
将以下 Python 字典从 JSON 翻译成带有表头和标题的 HTML 表格: {data_json}
"""
response = get_completion(prompt)
print(response)
```

```

<table>
  <tr>
    <th>resturant employees</th>
  </tr>
  <tr>
    <td>name</td>
    <td>email</td>
  </tr>
  <tr>
    <td>Shyam</td>
    <td>shyamjaiswal@gmail.com</td>
  </tr>
  <tr>
    <td>Bob</td>
    <td>bob32@gmail.com</td>
  </tr>
  <tr>
    <td>Jai</td>
    <td>jai87@gmail.com</td>
  </tr>
</table>


```



## 拼写检查/语法检查。

这里有一些常见的语法和拼写问题的例子以及法学硕士的回应。

要向 LLM 发出您希望它校对您的文本的信号，您可以指示模型“校对”或“校对并更正”。

```python
text = [ 
  "The girl with the black and white puppies have a ball.",  # The girl has a ball.
  "Yolanda has her notebook.", # ok
  "Its going to be a long day. Does the car need it’s oil changed?",  # Homonyms
  "Their goes my freedom. There going to bring they’re suitcases.",  # Homonyms
  "Your going to need you’re notebook.",  # Homonyms
  "That medicine effects my ability to sleep. Have you heard of the butterfly affect?", # Homonyms
  "This phrase is to cherck chatGPT for speling abilitty"  # spelling
]
for t in text:
    prompt = f"""校对并更正以下文本并重写更正后的版本。 \
    如果您没有找到错误，只需说“未发现错误”。 \
    不要在文本周围使用任何标点符号:```{t}```"""
    response = get_completion(prompt)
    print(response)
```

```

The girl with the black and white puppies has a ball. 

重写后的版本： The girl who has black and white puppies is playing with a ball.
未发现错误。
未发现错误。

重写后的版本：今天将是漫长的一天。汽车需要更换机油吗？
更正后的版本： 

There goes my freedom. They're going to bring their suitcases. 

重写后的版本： 

My freedom is slipping away. They're getting ready to bring their suitcases.
你需要你的笔记本。

重写后的版本：You're going to need your notebook.
That medicine affects my ability to sleep. Have you heard of the butterfly effect? 

重写后的版本： 

我服用那种药会影响我的睡眠能力。你听说过蝴蝶效应吗？ 

（注意：重写后的版本仅供参考，可能与原文意思略有不同。）
未发现错误。

重写后的版本： 

如果您想检查自己的拼写能力，请使用这个短语： "This phrase is to check chatGPT for spelling ability."
```

```python
text = f"""
Got this for my daughter for her birthday cuz she keeps taking \
mine from my room.  Yes, adults also like pandas too.  She takes \
it everywhere with her, and it's super soft and cute.  One of the \
ears is a bit lower than the other, and I don't think that was \
designed to be asymmetrical. It's a bit small for what I paid for it \
though. I think there might be other options that are bigger for \
the same price.  It arrived a day earlier than expected, so I got \
to play with it myself before I gave it to my daughter.
"""
prompt = f"校对并更正此评论: ```{text}```"
response = get_completion(prompt)
print(response)
```

```

I got this for my daughter's birthday because she keeps taking mine from my room. Yes, even adults like pandas too. She takes it everywhere with her, and it's super soft and cute. However, one of the ears is a bit lower than the other, and I don't think that was designed to be asymmetrical. Also, it's a bit small for the price I paid. I think there might be other options that are bigger for the same price. On the positive side, it arrived a day earlier than expected, so I got to play with it myself before giving it to my daughter.
```



# Expanding（扩展）
在本课中，您将生成针对每位客户的评论量身定制的客户服务电子邮件。设置

### 加载API密钥和相关的Python库。

```python
import openai
import os

from dotenv import load_dotenv, find_dotenv
_ = load_dotenv(find_dotenv())

openai.api_key  = os.getenv('OPENAI_API_KEY')
```

```python
def get_completion(prompt, model="gpt-3.5-turbo"):
    messages = [{"role": "user", "content": prompt}]
    response = openai.ChatCompletion.create(
        model=model,
        messages=messages,
        temperature=0, # this is the degree of randomness of the model's output
    )
    return response.choices[0].message["content"]
```



## 自定义对客户电子邮件的自动回复

### 积极情绪

```
# given the sentiment from the lesson on "inferring",
# and the original customer message, customize the email
sentiment = "积极"

# review for a blender
review = f"""
因此，他们在 11 月份仍然以 49 美元左右的价格对 17 件系统进行季节性销售，\
大约减半，但由于某种原因（称之为价格欺诈），在 12 月的第二周左右，\
价格都上涨了大约从 同一系统在 70-89 美元之间。
11 件套系统的价格也比之前的 29 美元上涨了 10 美元左右。
所以它看起来还不错，但是如果你看一下底座，刀片锁定到位的部分看起来不像几年前的以前版本那么好，\
但我打算对它非常温和（例如，我 压碎非常坚硬的物品，例如豆子、冰块、大米等。
首先在搅拌机中，然后在搅拌机中将它们粉碎成我想要的份量，然后切换到搅打刀片以获得更细的面粉，\
并在制作冰沙时先使用横切刀片，如果我需要更细/更少的面粉，则使用平刀片 稀烂）。
制作冰沙时的特别提示，切碎并冷冻您计划使用的水果和蔬菜（如果使用菠菜 - 稍微炖软菠菜，\
然后冷冻直至准备使用 - 如果制作冰糕，请使用中小型食品加工机） 这样你就可以避免在制作冰沙时添加太多冰块。
大约一年后，电机发出一种奇怪的声音。
我打电话给客户服务，但保修期已经过期，所以我不得不再买一个。
仅供参考：这些类型的产品的整体质量已经完成，\
因此他们有点依赖品牌知名度和消费者忠诚度来维持销售。
大概两天就拿到了
"""

prompt = f"""
你是客服AI助理。
您的任务是向尊贵的客户发送电子邮件回复。
给定以```分隔的客户电子邮件，生成回复以感谢客户的评论。
如果情绪是积极的或中立的，感谢他们的评论。
如果情绪是负面的，请道歉并建议他们联系客户服务。
确保使用评论中的具体细节。
以简洁和专业的语气写作。
将电子邮件签名为“AI 客户代理”。
客户评论: ```{review}```
评论情绪: {sentiment}
"""
response = get_completion(prompt)
print(response)

```

结果

```
尊敬的客户，

非常感谢您对我们产品的支持和评论。我们很高兴听到您对我们的搅拌机感到满意，并感谢您分享了您的使用技巧和经验。

我们一直致力于提供高质量的产品和优质的客户服务，因此您的反馈对我们来说非常重要。我们将继续努力改进我们的产品和服务，以满足客户的需求和期望。

再次感谢您的评论和支持。如果您有任何其他问题或需要帮助，请随时联系我们的客户服务团队。

AI 客户代理
```



### 消极情绪

```python
# given the sentiment from the lesson on "inferring",
# and the original customer message, customize the email
sentiment = "消极"

# review for a blender
review = f"""
因此，他们在 11 月份仍然以 49 美元左右的价格对 17 件系统进行季节性销售，\
大约减半，但由于某种原因（称之为价格欺诈），在 12 月的第二周左右，\
价格都上涨了大约从 同一系统在 70-89 美元之间。
11 件套系统的价格也比之前的 29 美元上涨了 10 美元左右。
所以它看起来还不错，但是如果你看一下底座，刀片锁定到位的部分看起来不像几年前的以前版本那么好，\
但我打算对它非常温和（例如，我 压碎非常坚硬的物品，例如豆子、冰块、大米等。
首先在搅拌机中，然后在搅拌机中将它们粉碎成我想要的份量，然后切换到搅打刀片以获得更细的面粉，\
并在制作冰沙时先使用横切刀片，如果我需要更细/更少的面粉，则使用平刀片 稀烂）。
制作冰沙时的特别提示，切碎并冷冻您计划使用的水果和蔬菜（如果使用菠菜 - 稍微炖软菠菜，\
然后冷冻直至准备使用 - 如果制作冰糕，请使用中小型食品加工机） 这样你就可以避免在制作冰沙时添加太多冰块。
大约一年后，电机发出一种奇怪的声音。
我打电话给客户服务，但保修期已经过期，所以我不得不再买一个。
仅供参考：这些类型的产品的整体质量已经完成，\
因此他们有点依赖品牌知名度和消费者忠诚度来维持销售。
大概两天就拿到了
"""

prompt = f"""
你是客服AI助理。
您的任务是向尊贵的客户发送电子邮件回复。
给定以```分隔的客户电子邮件，生成回复以感谢客户的评论。
如果情绪是积极的或中立的，感谢他们的评论。
如果情绪是负面的，请道歉并建议他们联系客户服务。
确保使用评论中的具体细节。
以简洁和专业的语气写作。
将电子邮件签名为“AI 客户代理”。
客户评论: ```{review}```
评论情绪: {sentiment}
"""
response = get_completion(prompt)
print(response)
```

```

尊敬的客户，

非常感谢您对我们产品的评论。我们非常抱歉您在使用过程中遇到了问题。我们深刻理解您的不满和失望，并为此向您道歉。

我们建议您联系我们的客户服务团队，以便我们能够更好地了解您的问题并提供更好的解决方案。我们将竭尽全力解决您的问题，并确保您对我们的服务感到满意。

感谢您对我们的支持和信任。如果您有任何其他问题或疑虑，请随时与我们联系。

祝您一切顺利。

AI 客户代理
```



## 先判断评论是否消极，然后再发邮件

### 消极评论

```python
# review for a blender
review = f"""
产品太不行了，质量很差
"""
```

```python
prompt = f"""
你是客服AI助理。
您的任务是向尊贵的客户发送电子邮件回复。
给定以```分隔的客户电子邮件，生成回复以感谢客户的评论。
先判断客户评论的情绪，
如果情绪是积极的或中立的，感谢他们的评论。
如果情绪是负面的，请道歉并建议他们联系客户服务。
确保使用评论中的具体细节。
以简洁和专业的语气写作。
将电子邮件签名为“AI 客户代理”。
客户评论: ```{review}```
"""
response = get_completion(prompt)
print(response)
```

```

尊敬的客户，

非常感谢您对我们产品的评论。我们非常抱歉您对我们的产品质量感到失望。我们一直致力于提供高质量的产品和服务，但显然我们在这方面没有达到您的期望。

我们建议您联系我们的客户服务团队，以便我们可以更好地了解您的问题，并尽快解决它们。我们将竭尽全力确保您对我们的产品和服务感到满意。

再次感谢您的评论，我们期待着为您提供更好的服务。

AI 客户代理
```

### 积极评论

```python
# review for a blender
review = f"""

产品非常好，质量也非常不错，下次还要来买
"""

prompt = f"""
你是客服AI助理。
您的任务是向尊贵的客户发送电子邮件回复。
给定以```分隔的客户电子邮件，生成回复以感谢客户的评论。
先判断客户评论的情绪，
如果情绪是积极的或中立的，感谢他们的评论。
如果情绪是负面的，请道歉并建议他们联系客户服务。
确保使用评论中的具体细节。
以简洁和专业的语气写作。
将电子邮件签名设置为“AI 客户代理”。
客户评论: ```{review}```
"""
response = get_completion(prompt)
print(response)


```

```
尊敬的客户，

非常感谢您对我们产品的赞扬和支持。我们很高兴听到您对我们的产品质量印象深刻，并期待着在不久的将来再次为您服务。

谢谢您的反馈，这对我们来说非常重要。我们一直在努力提高我们的产品和服务，以满足客户的需求和期望。如果您有任何其他问题或建议，请随时联系我们的客户服务团队，我们将竭诚为您服务。

再次感谢您的支持和反馈。

祝您一切顺利！

AI 客户代理
```



## 提醒模型使用客户电子邮件中的详细信息

```python
# review for a blender
review = f"""
因此，他们在 11 月份仍然以 49 美元左右的价格对 17 件系统进行季节性销售，\
大约减半，但由于某种原因（称之为价格欺诈），在 12 月的第二周左右，\
价格都上涨了大约从 同一系统在 70-89 美元之间。
11 件套系统的价格也比之前的 29 美元上涨了 10 美元左右。
所以它看起来还不错，但是如果你看一下底座，刀片锁定到位的部分看起来不像几年前的以前版本那么好，\
但我打算对它非常温和（例如，我 压碎非常坚硬的物品，例如豆子、冰块、大米等。
首先在搅拌机中，然后在搅拌机中将它们粉碎成我想要的份量，然后切换到搅打刀片以获得更细的面粉，\
并在制作冰沙时先使用横切刀片，如果我需要更细/更少的面粉，则使用平刀片 稀烂）。
制作冰沙时的特别提示，切碎并冷冻您计划使用的水果和蔬菜（如果使用菠菜 - 稍微炖软菠菜，\
然后冷冻直至准备使用 - 如果制作冰糕，请使用中小型食品加工机） 这样你就可以避免在制作冰沙时添加太多冰块。
大约一年后，电机发出一种奇怪的声音。
我打电话给客户服务，但保修期已经过期，所以我不得不再买一个。
仅供参考：这些类型的产品的整体质量已经完成，\
因此他们有点依赖品牌知名度和消费者忠诚度来维持销售。
大概两天就拿到了
产品非常好，质量也非常不错，下次还要来买
"""

prompt = f"""
你是客服AI助理。
您的任务是向尊贵的客户发送电子邮件回复。
给定以```分隔的客户电子邮件，生成回复以感谢客户的评论。
如果情绪是积极的或中立的，感谢他们的评论。
如果情绪是负面的，请道歉并建议他们联系客户服务。
确保使用评论中的具体细节。
以简洁和专业的语气写作。
将电子邮件签名为“AI 客户代理”。
客户评论: ```{review}```
"""
response = get_completion(prompt, temperature=0.7)
print(response)
```

```
尊敬的客户，

非常感谢您对我们产品的评论。我们很高兴您对我们的搅拌机感到满意，并且您的使用建议非常有用。我们确实在产品质量上非常注重，以确保我们的客户可以使用高品质的产品。

对于您所提到的价格上涨和质量问题，我们感到非常抱歉。我们会进一步调查，并确保这种情况不再发生。如果您有任何其他问题或需要帮助，请随时联系我们的客户服务，我们将竭诚为您服务。

再次感谢您对我们的支持和反馈。

AI 客户代理
```



# The Chat Format（聊天格式）

在此笔记本中，您将探索如何利用聊天格式与聊天机器人进行个性化或专门针对特定任务或行为的扩展对话。











