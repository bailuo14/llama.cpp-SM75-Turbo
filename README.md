🚀 llama.cpp-SM75-Turbo

为 NVIDIA Turing (SM75) 架构优化

市面上的 `llama.cpp` 通用版为了兼容从 Pascal 到 Hopper 的所有显卡，代码中包含了大量冗余逻辑
本项目**大刀阔斧地做了减法**，仅保留针对 **Turing 架构 (RTX 2060/2070/2080/Ti/Super)** 的核心计算内核

2080ti22G运行Qwen3.8-27B-GSQ-ROC-IQ3_XXS稳定30 tk/s+
架构专编：-DCMAKE_CUDA_ARCHITECTURES=75-real
只编译 sm_75（Turing），输出纯 SASS 机器码，无 PTX JIT 兜底

砍掉其他所有架构（sm_50 ~ sm_90）的 kernel 代码
DLL 从 545.8 MB → 32.1 MB，加载更快、显存占用更低

Turing MMA tensor core 路径：启用 mma m16n8k8 fp16/bf16 矩阵指令——Turing 原生张量核心指令，GEMM 走专用硬件而非通用 SIMT 路径

FlashAttention：sm_75 FA kernel 全量化组合编译（f16/q4_0/q8_0/bf16），长上下文 attention 更快、显存更省

CUDA Graphs：ON——kernel 启动开销打包成图，短序列生成效率更高

理论上只要是图灵（SM75）架构的显卡都能吃到加速，其他显卡也因此跑不了，需要的话请移步llama.cpp仓库

注意：速度受 CPU 内存带宽、后台程序占用影响较大,所以不一定能达到我的速度

