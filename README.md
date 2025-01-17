[![MIT Licensed](https://img.shields.io/badge/license-MIT-green.svg)](https://github.com/robrua/easy-bert/blob/master/LICENSE.txt)
[![PyPI](https://img.shields.io/pypi/v/easybert.svg)](https://pypi.org/project/easybert/)
[![Maven Central](https://img.shields.io/maven-central/v/com.robrua.nlp/easy-bert.svg)](https://search.maven.org/search?q=g:com.robrua.nlp%20a:easy-bert)
[![JavaDocs](https://javadoc.io/badge/com.robrua.nlp/easy-bert.svg)](https://javadoc.io/doc/com.robrua.nlp/easy-bert)
[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.2651822.svg)](https://doi.org/10.5281/zenodo.2651822)
[![FOSSA Status](https://app.fossa.io/api/projects/git%2Bgithub.com%2Frobrua%2Feasy-bert.svg?type=shield)](https://app.fossa.io/projects/git%2Bgithub.com%2Frobrua%2Feasy-bert?ref=badge_shield)

# easy-bert
easy-bert is a dead simple API for using Google's high quality [BERT](https://github.com/google-research/bert) language model in Python and Java.

Currently, easy-bert is focused on getting embeddings from pre-trained BERT models in both Python and Java. Support for fine-tuning and pre-training in Python will be added in the future, as well as support for using easy-bert for other tasks besides getting embeddings.

## Python

### How To Get It
easy-bert is available on [PyPI](https://pypi.org/project/easybert/). You can install with `pip install easybert` or `pip install git+https://github.com/robrua/easy-bert.git` if you want the very latest.

### Usage
You can use easy-bert with pre-trained BERT models from TensorFlow Hub or from local models in the TensorFlow saved model format.

To create a BERT embedder from a TensowFlow Hub model, simply instantiate a Bert object with the target tf-hub URL:

```python
from easybert import Bert
bert = Bert("https://tfhub.dev/google/bert_multi_cased_L-12_H-768_A-12/1")
```

You can also load a local model in TensorFlow's saved model format using `Bert.load`:

```python
from easybert import Bert
bert = Bert.load("/path/to/your/model/")
```

Once you have a BERT model loaded, you can get sequence embeddings using `bert.embed`:

```python
x = bert.embed("A sequence")
y = bert.embed(["Multiple", "Sequences"])
```

If you want per-token embeddings, you can set `per_token=True`:

```python
x = bert.embed("A sequence", per_token=True)
y = bert.embed(["Multiple", "Sequences"], per_token=True)
```

easy-bert returns BERT embeddings as numpy arrays


Every time you call `bert.embed`, a new TensorFlow session is created and used for the computation. If you're calling `bert.embed` a lot sequentially, you can speed up your code by sharing a TensorFlow session among those calls using a `with` statement:

```python
with bert:
    x = bert.embed("A sequence", per_token=True)
    y = bert.embed(["Multiple", "Sequences"], per_token=True)
```

You can save a BERT model using `bert.save`, then reload it later using `Bert.load`:

```python
bert.save("/path/to/your/model/")
bert = Bert.load("/path/to/your/model/")
```

### CLI
easy-bert also provides a CLI tool to conveniently do one-off embeddings of sequences with BERT. It can also convert a TensorFlow Hub model to a saved model.

Run `bert --help`, `bert embed --help` or `bert download --help` to get details about the CLI tool.

### Docker
easy-bert comes with a [docker build](https://hub.docker.com/r/robrua/easy-bert) that can be used as a base image for applications that rely on bert embeddings or to just run the CLI tool without needing to install an environment.

## Java

### How To Get It
easy-bert is available on [Maven Central](https://search.maven.org/search?q=g:com.robrua.nlp%20a:easy-bert). It is also distributed through the [releases page](https://github.com/robrua/easy-bert/releases).

To add the latest easy-bert release version to your maven project, add the dependency to your `pom.xml` dependencies section:
```xml
<dependencies>
  <dependency>
    <groupId>com.robrua.nlp</groupId>
    <artifactId>easy-bert</artifactId>
    <version>1.0.3</version>
  </dependency>
</dependencies>
```
Or, if you want to get the latest development version, add the [Sonaype Snapshot Repository](https://oss.sonatype.org/content/repositories/snapshots/) to your `pom.xml` as well:
```xml
<dependencies>
  <dependency>
    <groupId>com.robrua.nlp</groupId>
    <artifactId>easy-bert</artifactId>
    <version>1.0.4-SNAPSHOT</version>
  </dependency>
</dependencies>

<repositories>
  <repository>
    <id>snapshots-repo</id>
    <url>https://oss.sonatype.org/content/repositories/snapshots</url>
    <releases>
      <enabled>false</enabled>
    </releases>
    <snapshots>
      <enabled>true</enabled>
    </snapshots>
  </repository>
</repositories>
```

### Usage
You can use easy-bert with pre-trained BERT models generated with easy-bert's Python tools. You can also used pre-generated models on Maven Central.

To load a model from your local filesystem, you can use:

```java
try(Bert bert = Bert.load(new File("/path/to/your/model/"))) {
    // Embed some sequences
}
```

If the model is in your classpath (e.g. if you're pulling it in via Maven), you can use:

```java
try(Bert bert = Bert.load("/resource/path/to/your/model")) {
    // Embed some sequences
}
```

Once you have a BERT model loaded, you can get sequence embeddings using `bert.embedSequence` or `bert.embedSequences`:

```java
float[] embedding = bert.embedSequence("A sequence");
float[][] embeddings = bert.embedSequences("Multiple", "Sequences");
```

If you want per-token embeddings, you can use `bert.embedTokens`:

```java
float[][] embedding = bert.embedTokens("A sequence");
float[][][] embeddings = bert.embedTokens("Multiple", "Sequences");
```

### Pre-Generated Maven Central Models
Various TensorFlow Hub BERT models are available in easy-bert format on [Maven Central](https://search.maven.org/search?q=g:com.robrua.nlp.models). To use one in your project, add the following to your `pom.xml`, substituting one of the Artifact IDs listed below in place of `ARTIFACT-ID` in the `artifactId`:

```xml
<dependencies>
  <dependency>
    <groupId>com.robrua.nlp.models</groupId>
    <artifactId>ARTIFACT-ID</artifactId>
    <version>1.0.0</version>
  </dependency>
</dependencies>
```

Once you've pulled in the dependency, you can load the model using this code. Substitute the appropriate Resource Path from the list below in place of `RESOURCE-PATH` based on the model you added as a dependency:

```java
try(Bert bert = Bert.load("RESOURCE-PATH")) {
    // Embed some sequences
}
```

#### Available Models
| Model | Languages | Layers | Embedding Size | Heads | Parameters | Artifact ID | Resource Path |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [BERT-Base, Uncased](https://tfhub.dev/google/bert_uncased_L-12_H-768_A-12/1) | English | 12 | 768 | 12 | 110M | easy-bert-uncased-L-12-H-768-A-12 [![Maven Central](https://img.shields.io/maven-central/v/com.robrua.nlp.models/easy-bert-uncased-L-12-H-768-A-12.svg)](https://search.maven.org/search?q=g:com.robrua.nlp.models%20a:easy-bert-uncased-L-12-H-768-A-12) | com/robrua/nlp/easy-bert/bert-uncased-L-12-H-768-A-12 |
| [BERT-Base, Cased](https://tfhub.dev/google/bert_cased_L-12_H-768_A-12/1) | English | 12 | 768 | 12 | 110M | easy-bert-cased-L-12-H-768-A-12 [![Maven Central](https://img.shields.io/maven-central/v/com.robrua.nlp.models/easy-bert-cased-L-12-H-768-A-12.svg)](https://search.maven.org/search?q=g:com.robrua.nlp.models%20a:easy-bert-cased-L-12-H-768-A-12) | com/robrua/nlp/easy-bert/bert-cased-L-12-H-768-A-12 |
| [BERT-Base, Multilingual Cased](https://tfhub.dev/google/bert_multi_cased_L-12_H-768_A-12/1) | 104 Languages | 12 | 768 | 12 | 110M | easy-bert-multi-cased-L-12-H-768-A-12 [![Maven Central](https://img.shields.io/maven-central/v/com.robrua.nlp.models/easy-bert-multi-cased-L-12-H-768-A-12.svg)](https://search.maven.org/search?q=g:com.robrua.nlp.models%20a:easy-bert-multi-cased-L-12-H-768-A-12) | com/robrua/nlp/easy-bert/bert-multi-cased-L-12-H-768-A-12 |
| [BERT-Base, Chinese](https://tfhub.dev/google/bert_chinese_L-12_H-768_A-12/1) | Chinese Simplified and Traditional | 12 | 768 | 12 | 110M | easy-bert-chinese-L-12-H-768-A-12 [![Maven Central](https://img.shields.io/maven-central/v/com.robrua.nlp.models/easy-bert-chinese-L-12-H-768-A-12.svg)](https://search.maven.org/search?q=g:com.robrua.nlp.models%20a:easy-bert-chinese-L-12-H-768-A-12) | com/robrua/nlp/easy-bert/bert-chinese-L-12-H-768-A-12 |

### Creating Your Own Models
For now, easy-bert can only use pre-trained TensorFlow Hub BERT models that have been converted using the Python tools. We will be adding support for fine-tuning and pre-training new models easily, but there are no plans to support these on the Java side. You'll need to train in Python, save the model, then load it in Java.

## Bugs
If you find bugs please let us know via a pull request or issue.

## Citing easy-bert
If you used easy-bert for your research, please [cite the project](https://doi.org/10.5281/zenodo.2651822).


## License
[![FOSSA Status](https://app.fossa.io/api/projects/git%2Bgithub.com%2Frobrua%2Feasy-bert.svg?type=large)](https://app.fossa.io/projects/git%2Bgithub.com%2Frobrua%2Feasy-bert?ref=badge_large)