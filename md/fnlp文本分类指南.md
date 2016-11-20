## fnlp文本分类指南

FNLP主要是为中文自然语言处理而开发的工具包，也包含为实现这些任务的机器学习算法和数据集。

工具包说明和地址：https://github.com/FudanNLP/fnlp/blob/master/README.md

按以上说明完成fnlp项目导入后，可以看到和文本分类的相关的demo类在fnlp-demo下的org.fnlp.demo.nlp.tc包中，以下主要介绍该包中的TextClassificationSimple类

### 数据准备

首先,我们需要准备有类别标记的训练数据。为了简单起见,我们收集一些新闻的 标题,然后根据这些标题进行分类。这些数据放在 example-data/text-classification 目录下。每个文件表示一类,文件中每一行为一篇新闻的标题。具体见以下程序行，规定了训练数据的路径和模型存放路径

```java
// 训练数据路径
private static String trainDataPath = "../example-data/text-classification/";
//模型文件
private static String modelFile = "../example-data/text-classification/model.gz";
```

### 程序解读

```java
TextClassifier tc = new TextClassifier();
```

tc对象的主要功能：

1. 载入之前保存的模型文件
2. 读入文本数据进行模型训练
3. 利用模型文件对文本进行分类

<span id="jump">功能2</span>的具体实现见如下代码：

```java
public void train(Reader reader, String modelFile) throws Exception {
		//分类模型
		//建立字典管理器
		AlphabetFactory af = AlphabetFactory.buildFactory();

		//使用n元特征
		Pipe ngrampp = new NGram(new int[] {1,2});
		//将字符特征转换成字典索引
		Pipe indexpp = new StringArray2IndexArray(af);
		//将目标值对应的索引号作为类别
		Pipe targetpp = new Target2Label(af.DefaultLabelAlphabet());		

		//建立pipe组合
		SeriesPipes pp = new SeriesPipes(new Pipe[]{ngrampp,targetpp,indexpp});
		SeriesPipes  pp2 = new SeriesPipes(new Pipe[]{prePipe, ngrampp,targetpp,indexpp});
		InstanceSet instset = new InstanceSet(pp2,af);
		//读入数据，并进行数据处理
		instset.loadThruStagePipes(reader);

		//建立分类器		
		OnlineTrainer trainer = new OnlineTrainer(af,100);
		trainer.c = 0.01f;
		pclassifier = trainer.train(instset);
		pp.removeTargetPipe();
		pclassifier.setPipe(pp);
		af.setStopIncrement(true);

		//将分类器保存到模型文件
		pclassifier.saveTo(modelFile);	
	}
```

​	要理解这段程序，先需理解其中涉及到的三个文本处理基本类：AlphabetFactory，Pipe，Instance和一个训练器类OnlineTrainer:

##### 字典管理器类－AlphabetFactory类

​	Fnlp中字典管理器使用org.fnlp.ml.types.alphabet.AlphabetFactory常量类，该字典管理器类的对象维护一个映射表<String, IAlphabet>，其中默认创建特征字典和标签字典。

```java
public static final String DefalutFeatureName = "FEATURES";
public static final String DefalutLabelName = "LABELS";
private Map<String, IAlphabet> maps = null;
//构造特征词典和标签词典
public IFeatureAlphabet buildFeatureAlphabet(String name)	{
}
public LabelAlphabet buildLabelAlphabet(String name)	{
}
```

##### 样本表示类－Instance

​	FNLP 中样本用 org.fnlp.ml.types.Instance 类(简称 Instance 类)描述。每一样 本都是 Instance 类的实例化对象。Instance 类有两个主要的成员变量 data 和 target, 它们分别表示样本的属性 x 和类别 y

```java
//表示单个样本 (x,y)。x,y 分别对应 data,target. 
public class Instance {
// 样本属性,相当于 x 
protected Object data; 
// 标签或类别,相当于 y 
protected Object target;
}
```

​	样本集合用 org.fnlp.ml.types.InstanceSet 类(简称 InstanceSet 类)描述。样本集合定义为 InstanceSet 类的实例化对象。

```java
// 样本集合
public class InstanceSet extends ArrayList <Instance > {
// 本样本集合默认的数据类型转换管道 
	private Pipe pipes = null;
// 本样本集合对应的特征和标签索引字典管理器
	private AlphabetFactory factory = null;
//批量读取训练数据 
	public void loadThruPipes(Reader reader); }
```

##### 特征抽取－Pipe

​	FNLP中特征抽取通过管道来进行,定义为org.fnlp.nlp.pipe.Pipe 类(简称Pipe 类),该类是一个抽象类。

```java
// 特征转换管道,将数据从字符串转换为基于向量的特征表示 
public abstract class Pipe{
// 基本的数据类型转换处理操作,继承类需重新定义实现
public abstract void addThruPipe(Instance inst); 
}
```

​	所有的特征转换器都继承 Pipe 类,通过重新定义 addThruPipe 方法来实现不同的数据类型转换。

​	一个特殊的继承类是 org.fnlp.nlp.pipe.SeriesPipes 类(简称 SeriesPipes 类),该 类是一个或多个 Pipe 对象的串行组合。

##### 训练器类－OnlineTrainer

​	训练器类训练样本参数来获取分类器，并且在开发集或者测试集上评估性能。

```java
//参数训练方法，返回线性分类器
public Linear train(InstanceSet trainset, InstanceSet devset)
//在开发集上评估性能
public void evaluate(InstanceSet devset)
```

了解这四个基本类后，就能理解[功能2](#jump)的具体实现代码了：

1. 首先建立一个字典管理器，字典管理器将自动创建标签字典和特征字典，
2. 建立三个特征抽取类，功能分别是Ngram（抽取语句中的n元特征），StringArray2IndexArray（将字符特征转化成字典索引），Target2Label（将目标值对应的索引号作为类别）
3. 建立Pipe组合｛pp和pp2｝pp2和pp的值是相同，都是｛Ngram,StringArray2IndexArray,Target2Label｝的组合
4. 建立初始化的样本集合InstanceSet，传入特征抽取组合｛pp2｝和字典管理器｛af｝
5. 读入数据｛reader｝，进行数据处理，处理完之后，所得InstanceSet的每个Instance样式为｛Feature: (xx,xx,xx)，Label{xx}｝
6. 建立trainer，传入参数字典管理器｛af｝和迭代次数｛100｝，更改训练器步长c为｛0.1｝
7. trainer调用train函数，利用感知机的原理训练一个分类器｛pclassifier｝，将管道组合｛pp｝去除与label有关的管道并传给pclassifier，（这个pclassifier是训练完的，之后通常处理只有feature没有label的数据）随后将字典af设置为不再增长，最后将分类器保存到模型文件。

在完成训练器训练后，即可使用该分类器进行文本分类（使用classify方法）

```java
String str = "苹果将投建私人餐厅防止员工谈话被窃听";
String label = (String) tc.classify(str).getLabel(0);
System.out.println("类别："+ label);
```

