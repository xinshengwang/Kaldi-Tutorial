## Install

- <https://deepakbaby.github.io/post/kaldi-mkl/>

- issues and solutions:
  provided by Siyuan Feng and [deepakbaby](https://deepakbaby.github.io/post/kaldi-mkl/)

  **安装到/home/nfs/$your_username这个文件夹下就可以, 我后来发现scratch里的文件会被管理员定期清理 **

  ```
  Memo in installing kaldi with MKL support without root access
  
  https://deepakbaby.github.io/post/kaldi-mkl/
  
  # to have rwx permissions
  mkdir /scratch/$your_dir 
  
  # to use other people's pre-installed kaldi/tools/ dir,
  I do:
  
  $ ./configure --shared --mkl-root=/tudelft.net/staff-bul
  k/ewi/insy/SpeechLab/intel/mkl --fst-root=/tudelft.net/staff-bulk/ewi/insy/SpeechLab/kaldi/kaldi/tools/openfst --atlas-root=/tudelft.net/staff-bulk/ewi/insy/SpeechLab/kaldi/kaldi/tools/ATLAS_headers --clapack-root=/tudelft.net/staff-bulk/ewi/insy/SpeechLab/kaldi/kaldi/tools/CLAPACK
  
  Problem happend. 
  Then I go to /scratch and mkdir to :
  /scratch/siyuanfeng/
  
  Looks like I have rwx access and can 
  
  
  ./configure --shared --mkl-root=/tudelft.net/staff-bulk/ewi/insy/SpeechLab/intel/mkl
  
  WARNING: CUDA will not be used! If you have already installed cuda drivers
           and CUDA toolkit, try using the --cudatk-dir= option. A GPU and CUDA
           are required to run neural net experiments in a realistic time.
  
  Instead use:
  
  ./configure --shared --mkl-root=/tudelft.net/staff-bulk/ewi/insy/SpeechLab/intel/mkl --cudatk-dir=/opt/insy/cuda/10.0
  
  Then I succeeded.
  
  
  I hope to install Kaldi/src on xxx/SpeechLab/, the problem is kaldi/tools/ cannot be installed due to access rights.
  My solution is to link kaldi/tools/ in /scratch/siyuanfeng/software/kaldi/tools. I.e., to copy /scratch/siyuanfeng/software/kaldi/src/kaldi.mk to xxx/SpeechLab/siyuanfeng/software/kaldi/src/. 
  After that, skip ./configure --shared (as kaldi.mk already exists)
  Just make depend & make.
  
  Lingyun Gao and Justin's kaldi installation is CPU-only installation.
  
  ```

## Blogs for Kaldi

- http://fancyerii.github.io/2019/05/21/kaldi-doc/
- 官方文档: https://kaldi-asr.org/
- <https://www.eleanorchodroff.com/tutorial/kaldi/index.html>

- http://jrmeyer.github.io/asr/2019/08/17/Kaldi-cheatsheet.html

- <https://groups.google.com/forum/#!forum/kaldi-help>


