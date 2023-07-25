# Neural Network Reliability Analysis Toolbox

## Introduction

Neural network accelerators are increasingly used because of their competitive advantages in performance and energy efficiency. Their reliability is critical to high-level applications built on top of the accelerators, and must be comprehensively evaluated and verified to ensure application safety. In this case, neural network reliability analysis tools are demanded. While the reliability analysis accuracy and the overhead is always contradictory, we present an neural network reliability analysis toolbox to enable various reliability analysis requirements for the sake of fault-tolerant design at different abstraction levels of neural network applications. Specifically, we provide five different fault analysis tools or methods including **FPGA-based fault emulation[1][2]**, **architecture-level fault simulation[7]**, **operation-level fault simulation [3]**, **neuron-level fault simulation[4]**, and **statistical model based reliability analysis[5]**. We also made **application-level fault simulation** for autonomous driving scenarios[6]. Particularly, the architecture level fault simulation in [7] is developed by Prof.Tan's group and please refer to her [homepage](https://jingweijiatan.github.io/) for details. 

## Compare

### FPGA-based fault emulation

FPGA-based fault emulation developed in [1] essentially have neural network accelerator implemented on FPGAs and injects stuck-at faults into the FPGA bitstream through vendor specific fault injection mechanism. It simulates the hardware faults of FPGAs directly and enables accurate fault simulation whenever it is required. The fault simulation is fast and only limited by the FPGAs.

### Architecture-level fault simulation
The fault simulation in [7] is essentially a microarchitecture-level fault simulation framework and it can be utilized to analyze the reliability of systolic array based CNN accelerators. Specifically, it is built on top of scacle-sim simulator [8].

### Operation-level fault simulation

Operation-level fault simulation proposed in [3] essentially has bit-flip errors injected to output of primitive operations such addition and multiplication in neural network processing. It reflects the influence of bit flip errors of the underlying computing engines, but it is not bound to any specific computing architectures. Compared to architecture-specific fault injection, it is more general and much faster yet compromised with fault simulation accuracy. Moreover, it also has the non-linear functions and different convolution methods such as winograd taken into consideration, which is more accurate than the widely utilized neural-level fault injection tools with little fault simulation speed penalty. 

### Neuron-level fault simulation

Neuron-level fault simulation has random bit flip errors injected to neurons, which is also general and neglects the underlying micro-architecture of the computing engines. This is the most widely used fault injection approach because of the convenient portability and fast fault simulation speed. However, existing neuron-level fault injection tools such as [PyTorchFI](https://github.com/pytorchfi/pytorchfi), [TensorFI](https://github.com/DependableSystemsLab/TensorFI), and [Ares](https://github.com/alugupta/ares) usually only provide coarse-grained reliability analysis such as layer-wise reliability, which is insufficient for in-depth reliability analysis of neural network processing. Worse still, most of the fault injection tools typically have the fault injection mechanism combined with the neural network computing engines i.e. Pytorch, which makes the fault simulation complex across different models and scenarios. To address the above problems, we enable fault reliability analysis at various granularities ranging from bit-level, neuron-level, channal-level, and layer-wise as detailed below. In addition, the error injection mechanism is decoupled with the model processing as much as possible through internal observation hook, which makes the neural network reliability analysis convenient.

- Bit level: Distinguish the error of each binary bit.
- Per neuron: Distinguish the error of neuron value.
- Channel-wise or kernel-wise: Distinguish the error impact between convolution channels or kernels.
- Layer-wise or block-wise: Distinguish layers or blocks.

### Statistical model based reliability analysis

The base idea of statistical model based analysis is to observe the statistical trend when errors occur and propagate on large number of neurons. Although it is not as accurate as fault simulation, the anaylsis under different configurations such as the reliability of the model under different injection rate, quantization methods, and protection strategies can be obtained rapidly. Moreover, the statistical model based analysis is usually more general compared to fault injection based analysis. 

### Application-level fault simulation

Instead of performing the reliability analysis on neural network processing, we also investigate the reliability analysis on high-level applications such that the reliability issues can be estimated from the perspective of applications such as autonomous driving tasks, which can provide a more comprehensive analysis of the impact of hardware errors on the entire autonomous system. This is important, because autonomous driving system usually involves multiple processing stages and some of the stages may hide some hardware errors or some minor computing errors may induce critical failure of the autonomous driving tasks. 

### Comparison table

|Method|Feature|Speed|
|-|-|-|
|FPGA-based reliability emulation|Simulate on hardware, accurate on specific hardware|Slow, depend on specific architecture|
|Architecture-level fault simulation|Fault injection on cycle-accurate accelerator simulator| Slow, depend on specific architecture|
|Operation-level fault injection|Fault injection on primitive operations|Fast, independent with specific architecture|
|Neuron-level fault injection|Fault injection on neurons|Fast, independent with specific architecture|
|Statistical model based analysis|Based on statistical trend|Orders of magnitude faster, independent with specific architecture|

## Reference

[1] [Dawen Xu, Ziyang Zhu, Cheng Liu, Ying Wang, Huawei Li, Lei Zhang, Kwang-Ting Cheng, "Persistent Fault Analysis of Neural Networks on FPGA-based Acceleration System", The 31th IEEE International Conference on Application-specific Systems, Architectures and Processors (ASAP), July, 2020.](https://ieeexplore.ieee.org/document/9153244)

[2] [Dawen Xu, Ziyang Zhu, Cheng Liu, Ying Wang, Lei Zhang, Huaguo Liang, Huawei Li, Kwang-Ting Cheng, "Reliability Evaluation and Analysis of FPGA-based Neural Network Acceleration System", IEEE Transactions on Very Large Scale Integration (VLSI) Systems, 2021.](https://ieeexplore.ieee.org/document/9316989)

[3] [Xinghua Xue, Haitong Huang, Cheng Liu, Ying Wang, Tao Luo, Lei Zhang, "Winograd Convolution: A Perspective from Fault Tolerance", In proceedings of ACM/IEEE Design Automation Conference (DAC), 2022.](https://dl.acm.org/doi/10.1145/3489517.3530531)

[4] [Haitong Huang, Cheng Liu, Xinghua Xue, Ying Wang, Huawei Li, Xiaowei Li, "MRFI: An Open Source Multi-Resolution Fault Injection Framework for Neural Network Processing", arXiv preprint arXiv:2306.11758 (2023).](https://arxiv.org/abs/2306.11758)

[5] [Haitong Huang, Xinghua Xue, Cheng Liu, Ying Wang, Tao Luo, Long Cheng, Huawei Li, and Xiaowei Li, "Statistical Modeling of Soft Error Influence on Neural Networks." IEEE Transactions on Computer-Aided Design of Integrated Circuits and Systems, doi: 10.1109/TCAD.2023.3266405.](https://ieeexplore.ieee.org/document/10098868)

[6] [Haitong Huang, Cheng Liu, "Deep Learning Accelerator in Loop Reliability Evaluation for Autonomous Driving", arXiv preprint arXiv:2306.11759 (2023).](https://arxiv.org/abs/2306.11759)

[7] [Jingweijia Tan, Qixiang Wang, Kaige Yan, Xiaohui Wei, and Xin Fu. "Saca-FI: A microarchitecture-level fault injection framework for reliability analysis of systolic array based CNN accelerator." Future Generation Computer Systems 147 (2023): 251-264.](https://www.sciencedirect.com/science/article/abs/pii/S0167739X2300184X)

[8] [Samajdar, Ananda, Yuhao Zhu, Paul Whatmough, Matthew Mattina, and Tushar Krishna. "Scale-sim: Systolic cnn accelerator simulator." arXiv preprint arXiv:1811.02883 (2018).](https://arxiv.org/abs/1811.02883)
