# run-code-on-gpu
在 Windows 上建置 PyTorch / Tensorflow 環境的安裝指南

> [!NOTE]  
> 需要預先在系統上安裝 Anaconda 或 Miniconda

## 1. PyTorch
到[PyTorch 官方網站](https://pytorch.org/)，按照需求點選環境後，複製下方出現的指令後在 Terminal/ CMD 內貼上就可以安裝了。

### 問題

#### 為什麼安裝好了，但是 PyTorch 卻無法找到 GPU ？
(我有點不確定這個的解法是什麼，時間有點久了...) 其實 PyTorch 背後有個隱性的限制，就是跟 Python 版本的兼容。如果你裝不起來的話，可能是你的 Python 太新或太舊了，可以嘗試 Python 3.8/3.9 左右的版本後試試看。

<br><br>

## 2. Tensorflow 
中文版的安裝說明因為沒有隨著版本更新修正內容，請參考[英文版安裝說明](https://www.tensorflow.org/install/pip#windows-native)。

### 原生環境 Windows Native
這是對終端機不熟悉的使用者來說最友善的安裝方式。然而根據文件：

> [!CAUTION]
> 警告：TensorFlow 2.10 是最後一個支持在原生 Windows 上使用 GPU 的 TensorFlow 發行版本。從 TensorFlow 2.11 開始，您需要在 WSL2 中安裝 TensorFlow，或者安裝 tensorflow 或 tensorflow-cpu，並且可以選擇嘗試 TensorFlow-DirectML-Plugin。

*我們僅能安裝最新至 2.10 的 TensorFlow 版本。*

#### 1. 安裝 Nvidia GPU 驅動和 Cuda
依序完成
* 在 [NVIDIA Driver Downloads](https://www.nvidia.com/download/index.aspx?lang=en-us) 根據作業系統以及顯卡型號下載並安裝驅動程式
* 在 [CUDA Toolkit Archive](https://developer.nvidia.com/cuda-toolkit-archive) 選擇 **CUDA® Toolkit 11.8** 下載並安裝
* 在 [NVIDIA cuDNN](https://developer.nvidia.com/cudnn) 點選 Download cuDNN Library 並下載安裝 cuDNN SDK
* 在 [Microsoft Visual C++ Redistributable latest supported downloads](https://learn.microsoft.com/en-US/cpp/windows/latest-supported-vc-redist?view=msvc-170) 中下載 Latest Microsoft Visual C++ Redistributable Version 以安裝 Microsoft Visual C++ Redistributable for Visual Studio 2015, 2017 and 2019

#### 2. 建立虛擬環境
我們需要先建立一個 Python 3.9 (或 3.10) 的虛擬環境。
* 在終端機中輸入以下指令來建立一個 `your-env-name` 的 Python 3.9 環境。
```shell
conda create -n your-env-name python=3.9
```
* 接著輸入以下指令來啟用剛剛建立好的環境，並透過 conda 安裝相關的程式庫。
```shell
conda activate yout-env-name
conda install -c conda-forge cudatoolkit=11.2 cudnn=8.1.0
```

[問：Python 3.11 的虛擬環境可不可以用？](#python-311-%E7%9A%84%E8%99%9B%E6%93%AC%E7%92%B0%E5%A2%83%E5%8F%AF%E4%B8%8D%E5%8F%AF%E4%BB%A5%E7%94%A8)

[問：為什麼一定要建立一個虛擬環境，我直接裝在 base 可不可以？](#%E7%82%BA%E4%BB%80%E9%BA%BC%E4%B8%80%E5%AE%9A%E8%A6%81%E5%BB%BA%E7%AB%8B%E4%B8%80%E5%80%8B%E8%99%9B%E6%93%AC%E7%92%B0%E5%A2%83%E6%88%91%E7%9B%B4%E6%8E%A5%E8%A3%9D%E5%9C%A8-base-%E5%8F%AF%E4%B8%8D%E5%8F%AF%E4%BB%A5)

#### 3. 安裝 Tensorflow
在終端機中輸入以下指令安裝 TensorFlow 2.10:
```shell
python -m pip install "tensorflow<2.11"
```

#### 4. 測試 Tensorflow
在 Python 中輸入以下指令
```Python
import tensorflow as tf
print(tf.config.list_physical_devices('GPU'))
```
若印出來資訊不為空，則安裝成功完成！

[問：明明裝好了 Tensorflow 卻在引入時發生 ImportError: No module named tensorflow？](#%E6%98%8E%E6%98%8E%E8%A3%9D%E5%A5%BD%E4%BA%86-tensorflow-%E5%8D%BB%E5%9C%A8%E5%BC%95%E5%85%A5%E6%99%82%E7%99%BC%E7%94%9F-importerror-no-module-named-tensorflow)


### 問題

#### Python 3.11 的虛擬環境可不可以用？
簡單來說，不行。

儘管在官方的說明文件描述了 Python 的支援版本為 3.9-3.11。但事實上，我們要安裝的 TensorFlow 2.10 僅僅支援 Python 3.9 及 3.10。
因此我們如果透過原生 Windows 的安裝方式建置 Tensorflow, 是沒有辦法使用 Python 3.11 的環境的。

#### 為什麼一定要建立一個虛擬環境，我直接裝在 base 可不可以？

在 Anaconda 2023.09 的安裝版本中，base 環境的 Python 版本就已經升級至 3.11。因此我們沒有辦法在這樣的條件下，在 base 直接安裝 Tensorflow。

#### 明明裝好了 Tensorflow 卻在引入時發生 ImportError: No module named tensorflow?

如果你在新環境中打開的 REPL 是 `ipython`，你應該會發現 ipython 中的 Python 版本為 base 中 Python 的版本號。
因為我們沒有在新建立的環境中安裝好 ipython，開啟的 ipython 就是 base 中的 ipython，而我們並未在 base 中安裝 tensorflow，
因此就會得到相對應的 ImportError。

解決方法很簡單，在新建立的環境中安裝 `jupyter` 即可 (其中就有 ipython)
```shell
pip install jupyter
```


