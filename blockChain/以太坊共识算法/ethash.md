# ethash

## 1. 原理

其基本原理可以用一个公式表示：

`RAND(h, nonce) <= M / d`

这里h表示区块头的哈希，nonce 表示一个自增的变量，RAND 表示经过一系列算法生成一个类似随机数的函数。
M 表示一个极大的数，d 则是当前区块的难度值 header.diffculty

### 1.1 DAG

我们知道 POW 算法依赖一个 nonce 值输入算法来得到一个低于困难度阈值的哈希值，Eth 引入了 DAG 来提供一个大的，瞬态，依赖于块头哈希和 Nonce 随机生成的固定资源的子集来参与最终哈希值的计算。

**DAG 资源大约占用 1GB 大小的内存。 其文件存储路径为**:

- Mac/Linux：$(HOME)/.ethash/full-R<REVISION>-<SEEDHASH>

- Windows: $(HOME)/Appdata/Local/Ethash/full-R<REVISION>-<SEEDHASH>

**其文件目录命名的意义为**：

- <REVISION>：一个十进制整数，当前的修订版本

- <SEEDHASH> ：16个小写的十六进制数字，指定了当前epoch(在以太坊中每30000个区块会生成一个DAG，这个DAG被称为epoch，大约5.2d，125h)种子哈希的前8个字节

**DAG文件的内部格式**:

1. little-endian小端模式存储(每个unint32以little-endian格式编码)

2. headerBytes：8-byte magic number(0xfee1deadbaddcafe)

3. DAG是一个uint32s类型的二维数组，维度是N * 16，N是一个幻数(从16777186开始)

4. DAG的行应按顺序写入文件，行之间没有分隔符

ethash依赖于DAG实现POW，从mining的wiki中得知：DAG需要很长时间才能生成。如果客户端仅按需生成它，可能会在找到新纪元的第一个块之前看到每个纪元转换的漫长等待。一般会预先计算DAG来避免在每个epoch过渡时发生过长的等待时间。geth执行自动的DAG生成，每次维持两个DAG来保障epoch过渡流畅。

### 1.2 Dagger-Hashimoto

==ethash设计的初衷旨在实现以下目标==:

1. IO饱和度:算法应消耗几乎所有可用的内存访问带宽(有效的抵抗ASIC)

2. GPU友好:尽可能地使用GPU进行挖矿。

3. 轻客户端可验证性:轻客户端应该能够在0.01秒内验证一轮挖掘的有效性，Python或Javascript中验证不到0.1秒，最多1 MB内存（但指数增加）

4. 轻客户端减速:使用轻客户端运行算法的过程应该比使用完整客户端的过程慢得多，以至于轻客户端算法不是经济上可行的实现挖掘实现的途径，包括通过专用硬件

5. 轻客户端快速启动:轻客户端应该能够完全运行并能够在40秒内在Javascript中验证块

==ethash的大概流程是这样的==：

1. 先根据block number以及block header计算出一个种子值seed

2. 使用seed产生一个16MB的伪随机数集cache

3. 根据cache生成一个1GB的数据集DAG(可以根据cache快速定位DAG中指定位置的元素，所以一般轻客户端保存cache，完整客户端保存DAG)

4. 从DAG中随机选择元素对其进行hash，然后判断哈希值是否小于给定值

5. cache和DAG每个周期( 30000 个块)更新一次。DAG从1GB开始随着时间线性增长，现在好像达到20多GB了

**参考**：

- [以太坊源码研读0x09 ethash和clique共识引擎](https://www.jianshu.com/p/c7f9f92dbb29)

- [以太坊Ethash算法源码分析](https://blog.csdn.net/TurkeyCock/article/details/81364008)