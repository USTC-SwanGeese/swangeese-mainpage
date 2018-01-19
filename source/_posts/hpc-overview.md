---
title: 高性能计算简介
date: 2018-01-06 16:43:07
tags:
    - Overall
categories:
    - Tutorials
---

总地来说，高性能计算（High Performance Computing，简称 HPC）的主要工作是利用大量资源，以远高于常规的性能去执行单一而任务量巨大的计算任务，从而在可以接受的时间内将其完成。这些计算任务有时是科学计算任务，例如，超星系团以上规模的天体模拟、地球气象模拟、地震模拟、宏基因组测序等等；也有时是计算机领域的某些分支需求大量的计算量，例如某些加密算法或 hash 算法的 brute force 或近 brute force 的破译或碰撞。而为了达成这样的任务目标，所使用的方法也在随时代变化。

从早期的向量机（[Vector Machine](https://en.wikipedia.org/wiki/Vector_processor#Supercomputers)）到现代的基于微处理器芯片的分布式并行集群，高性能计算的处理方式已经从单一的处理器发展向了基于高速专用网络的并行计算模式。现在，我们使用大量的独立机器（称为节点，与PC机的结构相似，由CPU、内存、外存和主板芯片构成，并可以选配加速卡），使用专用的高速互联网络将它们链接，通过各个节点之间的数据交换来协作，从而完成计算任务。于是，高性能计算集群的构建主要需要考虑两方面问题：

1. 节点间通信、协作的方式，包括硬件支持（高速互连网络如何布置）和编程模型等。

    对于这一部分，商业上常用的解决方案是Infiniband网络。以Mellanox Infiniband产品为例，最新的ConnextX-6已经可以达到单口200Gb/s的带宽，比日常使用的千兆以太网**快200倍**。而与之相应地，编程模型上，底层我们通过RDMA来使得网络传输无需占用CPU时间，上层通过MPI接口来简化编程，这里不多展开。

1. 节点本身性能的提高，包括CPU和加速卡（通常是[GPGPU](https://en.wikipedia.org/wiki/General-purpose_computing_on_graphics_processing_units)，有时也有[Intel MIC](https://en.wikipedia.org/wiki/Xeon_Phi)或[FPGA](https://en.wikipedia.org/wiki/Field-programmable_gate_array)）的芯片设计、主存（也就是常说的内存）和外部存储器（常为机械硬盘或固态硬盘）的性能提升。

    对于这一部分，我们分以下几部分来说：

    * CPU。
    
        高性能计算集群通常在单节点上使用2片以上的CPU，每片CPU有8~32个不等的核心，开启超线程时每个核心支持两个线程。主频一般是可调节的，通常在接近1.2GHz到3.5GHz的范围内。例如SC17 SCC中，我们使用的集群每个节点配备了两片Intel Xeon Platinum 8176，基频2.1GHz，最低可降至1.2GHz，最高睿频3.8GHz，28核56线程。单个节点上CPU的并行编程方式通常用OpenMP，有时也用Intel TBB。

    * GPGPU。
        
        GPGPU是General Purpose Graphics Processing Unit的缩写，而Graphics Processing Unit（GPU）就是通常所说的显卡。主要生产商是NVIDIA；NVIDIA有三个主要系列，其中Tesla即是GPGPU，用于通用计算。当前最新的Tesla产品是[Tesla V100](https://www.nvidia.com/en-us/data-center/tesla-v100/)，性能强劲，在SC17 SCC上我们的集群每个节点配备了四块，共12块。NVIDIA GPU的编程方式是CUDA。

    * 主存。
    
        高性能集群主存的容量和PC差异较大，现在通常在64GB到1TB不等。SC17 SCC中我们每个节点使用了256GB主存。而主存的其它技术参数则和PC上使用的相似，当前使用最多的也是DDR4，只是具备了[ECC](https://en.wikipedia.org/wiki/ECC_memory)纠错功能。在稳定运行时间要求较短时（例如我们的比赛当中），可以考虑关闭ECC，可能能够略微提高性能。

    * 外部存储器。
        
        一般有SSD和基于RAID的机械硬盘阵列两种思路，后者容量大但搭建和维护较为麻烦，而前者较为方便、随机访问快。
