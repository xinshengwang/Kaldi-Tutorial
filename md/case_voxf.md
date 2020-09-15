### 准备工作目录
创建自己的工作目录。在这里，以 ./Voxf作为本例的根目录：
```
mkdir Voxf 
cd Voxf
```
接下来我们要在自己的工作目录中构建一个与[熟悉kaldi](熟悉kaldi.md)中介绍过的相似的目录结构。

**建立软连接**
为了能够在工作目录下方便的访问kaldi安装目录下的相关脚本，我们需要在工作目录下建立一些软连接，从而可以通过这些软连接直接访问相关的文件目录。在此，需要连接的目录包括```/kaldi/egs/voxforge/s5``` 下的 ```local```, ```steps```, 和 ```utils```:

```
ln -s /scratch/xinshengwang/software/kaldi/egs/voxforge/s5/local local
ln -s /scratch/xinshengwang/software/kaldi/egs/voxforge/s5/steps steps
ln -s /scratch/xinshengwang/software/kaldi/egs/voxforge/s5/utils utils
ln -s /scratch/xinshengwang/software/kaldi/egs/voxforge/s5/conf conf
```

```
cp /scratch/xinshengwang/software/kaldi/egs/voxforge/s5/getdata.sh ./
```

除此之外，kaldi的脚本基本都依赖kaldi的安装环境，因此在各脚本的中都会通过 ```source ./path.sh``` 的方式将kaldi的安装路径添加到环境中. ```path.sh```位于```/s5```文件下，在此，我们将```/kaldi/egs/voxforge/s5/```下的```path.sh```文件拷贝到我们的工作目录：
```
cp /kaldi/egs/voxforge/s5/getdata.sh .YOUR_PAHT/Voxf
```
打开刚拷贝过来的 ``path.sh`` 文件，把第一行 ```DATA_ROOT``` 更改为安装kaldi的绝对路径：
```
export KALDI_ROOT=YOUR_PATH/kaldi
```
另外，我们还可以从path.sh文件中看到关于数据存储的提示，默认存储的位置位于 ```kalid/egs/voxforge/s5/voxforge```.我们可以对DATA_ROOT进行定义，以更改数据的存储位置。在这里，我们希望数据存储在工作目录下的 ```voxforge```文件下，因此定义 ```DATA_ROOT=voxforge```,具体如下：

```
...
# export DATA_ROOT="$KALDI_ROOT/egs/voxforge/s5/voxforge"    # e.g. something like /media/secondary/voxforge
DATA_ROOT=voxforge
if [ -z $DATA_ROOT ]; then
...
```

### 数据准备

**数据下载**
kaldi 提供了下载数据的脚本。 voxforge 数据的下载脚本位于 /kaldi/egs/voxforge/s5/getdata.sh. 这里我们将该下载脚本拷贝到我们的工作目录中:
```
cp /kaldi/egs/voxforge/s5/getdata.sh .YOUR_PAHT/Voxf
```
在上一步中我们已经在 ```path.sh``` 中设置了数据保存的位置，在此可以直接运行 ```getdata.sh```. 数据会自动保存至工作目录下的```voxforge```文件下. 下载结束后，该文件下会有两个自文件夹, 其中 ```tgz```是下载的源文件, ```extracted```中存放的是解压后的文件。

**数据简介**
VoxForge 数据包含来自于1235个不同speaker的95628个```.wav```文件，其采样率为16kHz. 在上一步得到的```extracted```的文件夹下的每一个子文件夹的名字都包含了一个speark的ID信息，每个文件夹内包含两个子文件夹：
* wav: 包含```.wav```文件
* etc: 包含了每一个```.wav```文件的相关信息。在这里，我们只考虑其中的 ```prompts-original```，该文件中一行由一个```.wav```文件的文件名和对应的文本转译组成。

**数据处理**
我们需要在工作目录下建立```data/train```和```data/test```两个目录分别存放用于训练和测试的数据（此处不必手动创建, 在接下来给出的数据处理代码中将自动创建这些目录）. 按照kaldi的要求, ```data/train```和```data/test```下应当包含一下文件：
* text
    - 存储了数据的转译文件，每一行的格式： 
        - ```utt_id WORD1 WORD2 WORD3 WORD4 ...```
    - 示例：
        - ```Aaron-20080318-kdl_b0019 HIS SLIM HANDS GRIPPED THE EDGES OF THE TABLE```
    - 这里我们采用```文件夹名_wav名```的形式作为utt_id
* wav.scp
    - 存储了每一个音频文件的目录，每一行的格式： 
        - ```file_id path/file```
    - 实例：
       - ```Aaron-20080318-kdl_b0019 /mnt/data/VF_Main_16kHz/Aaron-20080318-kdl/wav/b0019.wav```
    - 这里我们同样采用```文件夹名_wav名```的形式作为file_id.
* utt2spk
    - 存储了每一条音频文件对应的speaker ID, 每一行格式：
        - ``` utt_id speark_id```
    - 实例：
        - ```Aaron-20080318-kdl_b0019 Aaron```
* spk2utt
    - utt2spk的一个反向指向文件，每一行格式：
        - ```speark_id utt_id```
    - 实例
        - ```Aaron Aaron-20080318-kdl_b0019```
* full_vocab
    - 训练数据中所有文本的词汇表，只在```data/train```需要。

以上这个文件可通过如下python代码实现. 在工作目录下创建一个```data_prepare.py```文件，将下边的代码拷贝至该文件中保存，运行 ```python data_prepare.py```生成上述文件。
```python
import os
import pandas as pd
import random
from tqdm import tqdm
import re

def select_spk(num_spk):
    spk = []
    for i in range(num_spk):
        rand_spk = random.choice(all_spk)
        while rand_spk in selected_spk:
            rand_spk = random.choice(all_spk)
        spk.append(rand_spk)
        selected_spk.append(rand_spk)
    return spk


def get_df(select_spk,wave_root):
    spk_id = []
    utt_id = []
    text = []
    wav_path = []
    no_wav = 0
    no_prompt = 0
    for folder in tqdm(os.listdir(wave_root)):
        speaker = folder[:folder.find('-')]
        if speaker not in select_spk:
            continue
        waves = []
        if 'wav' not in os.listdir(os.path.join(wave_root, folder)):
            no_wav += 1 
            continue
        if prompt_file not in os.listdir(os.path.join(wave_root, folder, 'etc/')):
            no_prompt += 1
            continue
        
        prompts = [line.rstrip('\n') for line in open(os.path.join(wave_root, folder, 'etc/', prompt_file))]
        for prompt in prompts:
            if prompt[:prompt.find(' ')] == '':
                continue
            start_idx = prompt.find('/') + 1 if '/' in prompt[:prompt.find(' ')] else 0
            waves.append(prompt[start_idx:prompt.find(' ')])
            clean_text = prompt[prompt.find(' '):].strip().upper()
            clean_text = re.sub(r"[^\w\d's]+",' ',clean_text)
            text.append(clean_text)

        for wav in waves:
            wav_path = os.path.join(wave_root, folder, 'wav/', wav + '.wav')
            if not os.path.isfile(wav_path):
                print(wav_path)
            # wav_path.append(os.path.join(docker_path, local_path))
            utt_id.append(folder + '_' + wav)
            spk_id.append(speaker)
    return pd.DataFrame({'spk_id': spk_id, 'utt_id': utt_id, 'text': text, 'wav_path': wav_path})


def data_prep(df, mode):
    os.makedirs(os.path.join('data', mode), exist_ok=True)
    with open(os.path.join('data', mode, 'text'), 'a') as file:
        for _, row in df.iterrows():
            content = row['utt_id'] + ' ' + row['text'] + '\n'
            file.write(content)
    with open(os.path.join('data', mode, 'wav.scp'), 'a') as file:
        for _, row in df.iterrows():
            content = row['utt_id'] + ' ' + row['wav_path'] + '\n'
            file.write(content)
    with open(os.path.join('data', mode, 'utt2spk'), 'a') as file:
        for _, row in df.iterrows():
            content = row['utt_id'] + ' ' + row['spk_id'] + '\n'
            file.write(content)
    with open(os.path.join('data', mode, 'puretext.txt'), 'a') as file:
        for _, row in df.iterrows():
            content = row['text'] + '\n'
            file.write(content)

if __name__ == '__main__':
    all_spk = {}; anonymous = 0
    # 更改为你自己的目录
    wav_root = "/tudelft.net/staff-bulk/ewi/insy/SpeechLab/xinsheng/kaldi_learning/Voxf/voxforge/extracted"
    for folder in tqdm(os.listdir(wav_root)):
        spk = folder[:folder.find('-')]
        if spk == 'anonymous' or spk == 'jaiger': anonymous +=  1
        if spk not in all_spk:
            all_spk[spk] = 1
        else:
            all_spk[spk] += 1

    all_spk = list(all_spk.keys())
    selected_spk = []

    train_spk = select_spk(100)
    test_spk = select_spk(20)
    len(set(train_spk).intersection(test_spk)) == 0

    prompt_file = 'prompts-original'

    train_df = get_df(train_spk,wav_root)
    train_df = train_df.sort_values(by=['spk_id', 'utt_id'])
    train_df = train_df.reset_index(drop=True)
    train_df.head()

    test_df = get_df(test_spk,wav_root)
    test_df = test_df.sort_values(by=['spk_id', 'utt_id'])
    test_df = test_df.reset_index(drop=True)
    test_df.head()

    data_prep(train_df, 'train')
    data_prep(test_df, 'test')
```
为了保证生成的文件以C/C++兼容的模式存储，运行如下指令对生成的文件进行处理：
```
utils/fix_data_dir.sh data/train/
utils/fix_data_dir.sh data/test/
```
以上准备工作完成后, ```/data```下的目录结构应如下所示：
```
data
├───train
│   ├───text
│   ├───utt2spk
│   ├───spk2utt
│   |───wav.scp
|   └───full_vocab  *only for train directory
└───test
    ├───text
    ├───utt2spk
    ├───spk2utt
    └───wav.scp
```
