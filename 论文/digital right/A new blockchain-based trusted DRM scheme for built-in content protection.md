# A new blockchain-based trusted DRM scheme for built-in content protection

==本篇文章主要讲解图片的水印技术，提出改进的水印技术。区块链技术就用于存证，确权==

## Abstract

本文基于水印和区块链技术（联盟链）提出了一种艺术品图像数字版权管理机制，具有强鲁棒性和高安全性的特点。

## 1. Introduction

盗版的猖獗为创建人带来了不可估量的损失，尤其在新闻、设计、摄影和电子商务中。然而，图片它本身很难被标识为盗用，需要花费大量时间来走法律程序。

近年来人们对版权的保护意识越来越高，但是主要集中在视频和音乐中。本文针对图片。

介绍数字水印技术，分为水印嵌入和提取。

继续介绍水印技术，分为：robust、fragile和semi-fragile。。。。。。

介绍、对比某种水印技术

继续讲水印。。

区块链用于安全存储水印，为多个水印提供时间戳证明。以此为创建人提供水印证明。

为了解决以上问题，我们基于高鲁棒性提高水印安全性。基于 Arnold，human vision system（HVS），和DCT为 DRM 提供了新的图像水印和区块链方案。

## 2. Preliminaries

讨论 Arnold、HVS 和 DCT 原理

## 3. Method: The proposed DRM misusing detection scheme based on watermark

介绍基于数字水印的 DRM 组件，包括水印嵌入和水印提取

## 4. Results and discussions

方案模拟

## 5. Artwork DRM blockchain platform implementation and its blockchain data management

实现工艺品 DRM 方案，给出区块链数据实例

### 5.1 Artwork DRM blockchain platform implementation

区块链基于 hyperledger fabric 1.0。

### 5.2 Artwork DRM blockchain data management

#### 5.2.1 Artwork raw data management

用户先注册账户，然后上传 artwork，DRMChain 将创建原始 artwork 和它对应的区块链数据

#### 5.2.2 Artwork DRM blockchain data creation

介绍 fabric 网络，初始化，以及 artwork 在区块链中流转的整个过程。

### 5.3 Artwork DRM blockchcian data management

讲解 DRMChain 的优点，其实就是 fabric 的优点。

## 6. Conclusions

