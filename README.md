# Multi-Resolution Neural Network Reliability Analysis Toolbox

## Introduction

Neural network accelerators are increasingly used because of their competitive advantages in performance and energy efficiency. Their reliability is critical to these applications, and must be comprehensively evaluated and verified to ensure application safety.

There are different requirements for the accuracy of reliability analysis at different stages of design. It will be very time-consuming to rely only on the very accurate reliability analysis at the bottom level, and may get biased results due to the limited experimental settings. Simulation at a higher level can provide reliability analysis more quickly and provide more extensive conclusions, but it will bring more inaccuracies because the details of the underlying hardware architecture are not considered. 

Therefore, we provide multi-level fault analysis tools or methods for different scenarios, including **FPGA-based reliability emulation**<sup>[1][2]</sup>, **operation level fault injection**<sup>[3]</sup>, **neuron level fault injection**<sup>[4]</sup> and **statistical model based reliability analysis**<sup>[5]</sup>. We also made **application-oriented fault analysis** for autonomous driving scenarios<sup>[6]</sup>.

## Compare

### FPGA-based reliability emulation system

Hardware level fault injection provides the most accurate fault simulation result, which can be used when accurate fault analysis is required under specific hardware scenarios. The speed is limited by the real hardware execution.

### Operation-level fault injection

Operation-level fault provides relative accurate hardware error mapping to basic value calculation, that is, each data of the multiplication-addition calculation process. It can be used when cost of hardware level simulation is unacceptable. Compare with neuron level fault injection, it is more accurate and can distinguish different operator implementations (e.g normal convolution, winograd convolution and FFT convolution). This level fault simulation usually takes several times to tens of times the normal forward propagation time.

### Neuron-level fault injection

Neuron-level error injection assumes that the impact of hardware errors is reflected in neuron values, including weights and activation values, which can be simulated through the neural network training framework (e.g. PyTorch, Tensorflow). In order to obtain a selective fault-tolerant protection design strategies, error injection on neurons can be further divided into several levels:

- Bit level: Distinguish the error of each binary bit.
- Per neuron: Distinguish the error of neuron value.
- Channel-wise or kernel-wise: Distinguish the error impact between convolution channels or kernels.
- Layer-wise or block-wise: Distinguish layers or blocks.

For simulation speed, optimized neuron-level fault injection framework is similar to the normal forward propagation, with a small amount of overhead. However, it is still unrealistic to simulate the difference on millions of fine-graind neurons, so common error injection frameworks focus on coarse-grained analysis. [PyTorchFI](https://github.com/pytorchfi/pytorchfi), [TensorFI](https://github.com/DependableSystemsLab/TensorFI), [Ares](https://github.com/alugupta/ares), etc. are all fault injection tools work on this level. 

Our neuron-level fault injection tool is based on PyTorch. Compare with previous work, our tool can be simply and arbitrarily used as bit-, neuron-, channal- and layerwise- grained fault injector, configure different bit-flip mode and error mode, and can easily insert internal observation hook into network. This is beneficial to different experimental needs.

### Statistical model based analysis

The base idea of statistical model based analysis is observe the statistical trend when errors occur and propagate on large number of neurons. Although its accuracy is not as good as the error injection experiment, it can quickly get conclusions under different configurations, such as the reliability of the model under different injection rates, quantization methods and protection strategies. This can also provide more insights for fault tolerance analysis, and covering factors that have not been considered by the error injection method.

### Application-oriented fault analysis

Application oriented fault analysis for specific scenarios, conducting end-to-end analysis. An application oriented fault analysis may be slow or rely on specific simulators, but it can provide a more comprehensive supplementary analysis of the impact of errors on the entire system.

For example, the autopilot system includes not only neural networks, but also multiple processing stages, which may mask or transmit errors. We conducted deep learning acclerator in loop error injection simulation for autopilot system based on CARLA, indicating that fault injection may cause autopilot task failure.

### Comparison table

|Method|Feature|Speed|
|-|-|-|
|FPGA-based reliability emulation|Simulate on hardware, accurate on specific hardware|Slow, depend on real hardware|
|Operation-level fault injection|Fault inject on operation calculation|Several times slower than forward propagation|
|Neuron-level fault injection|Fault inject on neuron values|Fast as forward propagation on single fault inject|
|Statistical model based analysis|Based on statistical trend|Order of magnitude less or even no fault injection experiments|

## Reference

[1] [Dawen Xu, Ziyang Zhu, Cheng Liu, Ying Wang, Huawei Li, Lei Zhang, Kwang-Ting Cheng, "Persistent Fault Analysis of Neural Networks on FPGA-based Acceleration System", The 31th IEEE International Conference on Application-specific Systems, Architectures and Processors (ASAP), July, 2020.](https://ieeexplore.ieee.org/document/9153244)

[2] [Dawen Xu, Ziyang Zhu, Cheng Liu, Ying Wang, Lei Zhang, Huaguo Liang, Huawei Li, Kwang-Ting Cheng, "Reliability Evaluation and Analysis of FPGA-based Neural Network Acceleration System", IEEE Transactions on Very Large Scale Integration (VLSI) Systems, 2021.](https://ieeexplore.ieee.org/document/9316989)

[3] [Xinghua Xue, Haitong Huang, Cheng Liu, Ying Wang, Tao Luo, Lei Zhang, "Winograd Convolution: A Perspective from Fault Tolerance", In proceedings of ACM/IEEE Design Automation Conference (DAC), 2022.](https://dl.acm.org/doi/10.1145/3489517.3530531)

[4] [Haitong Huang, Cheng Liu, Xinghua Xue, Ying Wang, Huawei Li, Xiaowei Li, "MRFI: An Open Source Multi-Resolution Fault Injection Framework for Neural Network Processing", arXiv preprint arXiv:2306.11758 (2023).](https://arxiv.org/abs/2306.11758)

[5] [Haitong Huang, Xinghua Xue, Cheng Liu, Ying Wang, Tao Luo, Long Cheng, Huawei Li, and Xiaowei Li, "Statistical Modeling of Soft Error Influence on Neural Networks." IEEE Transactions on Computer-Aided Design of Integrated Circuits and Systems, doi: 10.1109/TCAD.2023.3266405.](https://ieeexplore.ieee.org/document/10098868)

[6] [Haitong Huang, Cheng Liu, "Deep Learning Accelerator in Loop Reliability Evaluation for Autonomous Driving", arXiv preprint arXiv:2306.11759 (2023).](https://arxiv.org/abs/2306.11759)