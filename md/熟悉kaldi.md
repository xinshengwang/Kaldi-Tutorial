这一部分简单的熟悉下Kaldi的目录结构。Kaldi的根目录下包含如下几个目录： egs, src, tools, misc, 和 windows。其中，egs 和 src 是两个后续会用到的目录。

**egs** 表示 “examples”, 包含了大多数主流语音数据集的training recipes， 这些数据包括 Wall Street Journal Corpus (wsj), TIMIT (timit), Resource Management (rm) 等. 在每一个目录下会有几个不同的版本 (s3, s4, s5 等)， 数字越高表示版本越新，也是推荐使用的版本。

**src** 表示 “source” 或者 “source code”, 包含了用于训练的大多源代码。

在每一个 training recipe 目录下都包含一个基本一致的目录结构，下图所示的是 Wall Street Journal Corpus (egs/wsj/s5) 的目录结构

<div align=center><img src="https://github.com/xinshengwang/Kaldi-Tutorial/raw/master/md/Image/familar_kaldi/directorystructure.png" width="300"/>
