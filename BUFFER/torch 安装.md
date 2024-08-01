---

类型: 笔记
创建日期: 2024-07-31
修改日期: 2024-07-31
---
pip换源之后，可能国内源并没有那么多gpu细分版本的torch，会给你安装默认cpu版本的torch。
因此选择自行下载torch所需的whl文件。
安装时
在你虚拟环境的shell中，切换目录到下载whl文件的目录，此时在下载目录中执行pip install，
pip应该是默认当前目录优先找所需的文件
```
pip install torch-1.7.1+cu101-cp38-cp38-win_amd64.whl
pip install torchvision-0.8.2+cu101-cp38-cp38-win_amd64.whl
pip install torchaudio-0.7.2-cp38-none-win_amd64.whl

```
