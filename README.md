## Fasttext Introduction

 [Fasttext](https://github.com/facebookresearch/fastText/) is a library for text representation and classification by facebookresearch. It implements text classification and word embedding learning.
 

## FastText4j

  A java version implementation of fasttext in kotlin. Features:

 * 100% in java
 * Compatible with original C++ model

   It can read all trained models from fasttext directly
 * Compatible with the original product quantizer compression model
 * Provides APIs for training(with equivalent performance)
 * Support for customing storage formats
 * Support reading model files in mmap

The scale of official model for chinese wiki is about 2.8G, which needs, at least, 4G for jvm in general. Moreover, it takes longer time for loading model files than C++ version.

But it could be much optimised through mmap with limited RAM and time(around 3 seconds).

## Installing

### Gradle
```
compile 'com.mayabot:fastText4j:1.2.2'

```

### Maven
```xml
<dependency>
  <groupId>com.mayabot</groupId>
  <artifactId>fastText4j</artifactId>
  <version>1.2.2</version>
</dependency>
```

## Example use cases

### 1.Word embedding model
```java
File file = new File("data/fasttext/data.text");

FastText fastText = FastText.train(file, ModelName.sg);

fastText.saveModel("data/fasttext/model.bin");
```
data.txt is the file of data, stored in utf-8. Before training, it is necessary to do word spliting to get training set. By default, it will use 3-6 char ngram. Additionally, cow algorithm is implemented as well. For more parameter tuning if needed, please provide TrainArgs object.

### 2.Classification model
```java
File file = new File("data/fasttext/data.txt");

FastText fastText = FastText.train(file, ModelName.sup);

fastText.saveModel("data/fasttext/model.bin");
```
data.txt is also encoded in utf-8 with one sample each line. And it needs to do word spliting beforehand as well. There is a string starting with ```__label__``` in each line，representing the classifying target, such as ```__label__正面```. Each sample could have  multiple label. Through the attribute 'label' in TrainArgs, you can customise the head. 

Invoke predict method to classify after the model trained.



### 3.Saving official model in java
```java
FastText fastText = FastText.loadFasttextBinModel("data/fasttext/wiki.zh.bin");
fastText.saveModel("data/fasttext/wiki.model");
```

### 4.Predict
```java
//predict the result of a word
FastText fastText = FastText.loadCModel("data/fasttext/wiki.zh.bin");
List<FloatStringPair> predict = fastText.predict(Arrays.asList("fastText在预测标签时使用了非线性激活函数".split(" ")), 5);
```

### 5.Nearest Neighbor Search
```java
FastText fastText = FastText.loadCModel("data/fasttext/wiki.zh.bin");

List<FloatStringPair> predict = fastText.nearestNeighbor("中国",5);
```

### 6.Analogies
By giving three words A, B and C, return the nearest words in terms of semantic distance and their similarity list, under the condition of (A - B + C).
```java
FastText fastText = FastText.loadCModel("data/fasttext/wiki.zh.bin");

List<FloatStringPair> predict = fastText.analogies("国王","皇后","男",5);
```

## Api
```java

 /**
 * classify the label by sup model
 */
 List<FloatStringPair> predict(Iterable<String> tokens, k: Int)

 /**
 * nearest neighbor search
 * @param word 
 * @param k k most similar words
 */
 List<FloatStringPair> nearestNeighbor(String word, k: Int)

/**
 * Analogies search
 * Query triplet (A - B + C)?
 */
 List<FloatStringPair> analogies(String A,String B,String C, k: Int)

 /**
 * The vector of a certain word
 */
 Vector getWordVector(String word)

 /**
 * The vector of a certain phrase
 */
 Vector getSentenceVector(Iterable<String> tokens)

 /**
 * Save the vector in text format
 */
 saveVectors(String fileName)

 /**
 * Save the model in binary format
 */
 saveModel(String file)

 /**
 * Model Training
 * @param File trainFile
 * @param model_name
 *  sg skipgram Use skipgram algorithm
 *  cow cbow Use cbow algorithm
 *  sup supervised Text classification
 * @param args Parameters 
 **/
 FastText FastText.train(File trainFile, ModelName model_name, TrainArgs args)

 /**
 * Load the model saved by saveModel method
 * @param file 
 * @param mmap Load by mmap model to accelerate and save RAM
 */
 Fasttext.loadModel(String file,boolean mmap)


 /**
 * Load model generated by C++ version(support bin & ftz).
 */
 Fasttext.loadFasttextBinModel(String binFile)
```


## Parameters of TrainArgs
The parameters is consistant with the C++ version :
```
The following arguments for the dictionary are optional:
  -minCount           minimal number of word occurences [1]
  -minCountLabel      minimal number of label occurences [0]
  -wordNgrams         max length of word ngram [1]
  -bucket             number of buckets [2000000]
  -minn               min length of char ngram [0]
  -maxn               max length of char ngram [0]
  -t                  sampling threshold [0.0001]
  -label              labels prefix [__label__]

The following arguments for training are optional:
  -lr                 learning rate [0.1]
  -lrUpdateRate       change the rate of updates for the learning rate [100]
  -dim                size of word vectors [100]
  -ws                 size of the context window [5]
  -epoch              number of epochs [5]
  -neg                number of negatives sampled [5]
  -loss               loss function {ns, hs, softmax} [softmax]
  -thread             number of threads [12]
  -pretrainedVectors  pretrained word vectors for supervised learning []
  -saveOutput         whether output params should be saved [0]

The following arguments for quantization are optional:
  -cutoff             number of words and ngrams to retain [0]
  -retrain            finetune embeddings if a cutoff is applied [0]
  -qnorm              quantizing the norm separately [0]
  -qout               quantizing the classifier [0]
  -dsub               size of each sub-vector [2]
```

## Resource
### Official pre-trained model
Recent state-of-the-art [English word vectors](https://fasttext.cc/docs/en/english-vectors.html).<br/>
Word vectors for [157 languages trained on Wikipedia and Crawl](https://github.com/facebookresearch/fastText/blob/master/docs/crawl-vectors.md).<br/>
Models for [language identification](https://fasttext.cc/docs/en/language-identification.html#content) and [various supervised tasks](https://fasttext.cc/docs/en/supervised-models.html#content).

## References

Please cite [1](#enriching-word-vectors-with-subword-information) if using this code for learning word representations or [2](#bag-of-tricks-for-efficient-text-classification) if using for text classification.

### Enriching Word Vectors with Subword Information

[1] P. Bojanowski\*, E. Grave\*, A. Joulin, T. Mikolov, [*Enriching Word Vectors with Subword Information*](https://arxiv.org/abs/1607.04606)

```
@article{bojanowski2017enriching,
  title={Enriching Word Vectors with Subword Information},
  author={Bojanowski, Piotr and Grave, Edouard and Joulin, Armand and Mikolov, Tomas},
  journal={Transactions of the Association for Computational Linguistics},
  volume={5},
  year={2017},
  issn={2307-387X},
  pages={135--146}
}
```

### Bag of Tricks for Efficient Text Classification

[2] A. Joulin, E. Grave, P. Bojanowski, T. Mikolov, [*Bag of Tricks for Efficient Text Classification*](https://arxiv.org/abs/1607.01759)

```
@InProceedings{joulin2017bag,
  title={Bag of Tricks for Efficient Text Classification},
  author={Joulin, Armand and Grave, Edouard and Bojanowski, Piotr and Mikolov, Tomas},
  booktitle={Proceedings of the 15th Conference of the European Chapter of the Association for Computational Linguistics: Volume 2, Short Papers},
  month={April},
  year={2017},
  publisher={Association for Computational Linguistics},
  pages={427--431},
}
```

### FastText.zip: Compressing text classification models

[3] A. Joulin, E. Grave, P. Bojanowski, M. Douze, H. Jégou, T. Mikolov, [*FastText.zip: Compressing text classification models*](https://arxiv.org/abs/1612.03651)

```
@article{joulin2016fasttext,
  title={FastText.zip: Compressing text classification models},
  author={Joulin, Armand and Grave, Edouard and Bojanowski, Piotr and Douze, Matthijs and J{\'e}gou, H{\'e}rve and Mikolov, Tomas},
  journal={arXiv preprint arXiv:1612.03651},
  year={2016}
}
```

(\* These authors contributed equally.)

## License

fastText is BSD-licensed. [facebook provide an additional patent grant.](https://github.com/facebookresearch/fastText/blob/master/PATENTS)
