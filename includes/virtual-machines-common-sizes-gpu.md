
GPU optimized VM sizes are specialized virtual machines targeted for heavy graphic rendering and video editing. They are available with single or multiple NVIDIA GPUs. This article provides information about the number and type of GPUs, vCPUs, data disks, and NICs as well as storage throughput and network bandwidth for each size in this grouping. 

* **NC, NCv2, and ND** sizes are optimized for compute-intensive and network-intensive applications and algorithms, including CUDA- and OpenCL-based applications and simulations, AI, and Deep Learning. 
* **NV** sizes are optimized and designed for remote visualization, streaming, gaming, encoding, and VDI scenarios utilizing frameworks such as OpenGL and DirectX.  


## NC instances

The NC instances are powered by NVIDIA’s Tesla K80 card. Users can crunch through data faster by leveraging CUDA for energy exploration applications, crash simulations, ray traced rendering, deep learning and more. The Tesla K80 delivers 4992 CUDA cores with a dual-GPU design, up to 2.91 Teraflops of double-precision and up to 8.93 Teraflops of single-precision performance.

| Size | vCPU | Memory: GiB | Temp storage (SSD) GiB | GPU | Maximum data disks |
| --- | --- | --- | --- | --- | --- |
| Standard_NC6 |6 |56 | 380 | 1 | 24 |
| Standard_NC12 |12 |112 | 680 | 2 | 48 |
| Standard_NC24 |24 |224 | 1440 | 4 | 64 |
| Standard_NC24r* |24 |224 | 1440 | 4 | 64 |

1 GPU = one-half K80 card.

*RDMA capable

## NCv2 instances

NCv2 instance are the next generation of the popular NC-series machines, powered by NVIDIA Tesla P100 GPUs based on the Pascal architecture. These GPUs can provide more than 2x the computational performance of the current NC-series. Customers can take advantage of these updated GPUs for traditional HPC workloads such as reservoir modeling, DNA sequencing, protein analysis, Monte Carlo simulations, and others. Like the NC-series, the NCv2-series offers a configuration that provides a low latency, high-throughput network interface optimized for tightly coupled parallel computing workloads.

| Size | vCPU | Memory: GiB | Temp storage (SSD) GiB | GPU | Maximum data disks |
| --- | --- | --- | --- | --- | --- |
| Standard_NC6_v2 |6 |112 | 336 | 1 | ? |
| Standard_NC12_v2 |12 |224 | 672 | 2 | ? |
| Standard_NC24_v2 |24 |448 | 1344 | 4 | ? |
| Standard_NC24r_v2* |24 |1448 | 1344 | 4 | ? |

1 GPU = one-half P100 card.

*RDMA capable

## ND instances

The ND-series virtual machines are a new addition to the GPU family specifically designed for AI and Deep Learning workloads. They offer excellent performance for training and inference. ND instances are powered by NVIDIA Tesla P40 GPUs based on the Pascal architecture. These instances provide excellent performance for single-precision floating point operations, for AI workloads utilizing Microsoft Cognitive Toolkit, TensorFlow, Caffe, and other frameworks. The ND-series also offers a much larger GPU memory size (24 GB), enabling to fit much larger neural net models. Like the NC-series, the ND-series offers a configuration with a secondary low-latency, high-throughput network through RDMA, and InfiniBand connectivity so you can run large-scale training jobs spanning many GPUs.


| Size | vCPU | Memory: GiB | Temp storage (SSD) GiB | GPU | Maximum data disks |
| --- | --- | --- | --- | --- | --- |
| Standard_ND6 |6 |112 | 336 | 1 | ? |
| Standard_ND12 |12 |224 | 672 | 2 | ? |
| Standard_ND24 |24 |448 | 1344 | 4 | ? |
| Standard_ND24r* |24 |1448 | 1344 | 4 | ? |

1 GPU = one-half P40 card.

*RDMA capable

## NV instances



The NV instances are powered by NVIDIA’s Tesla M60 GPU card and NVIDIA GRID for desktop accelerated applications and virtual desktops where customers are able to visualize their data or simulations. Users are able to visualize their graphics intensive workflows on the NV instances to get superior graphics capability and additionally run single precision workloads such as encoding and rendering. The Tesla M60 delivers 4096 CUDA cores in a dual-GPU design with up to 36 streams of 1080p H.264. 

| Size | vCPU | Memory: GiB | Temp storage (SSD) GiB | GPU | Maximum data disks |
| --- | --- | --- | --- | --- | --- |
| Standard_NV6 |6 |56 |380 | 1 | 24 |
| Standard_NV12 |12 |112 |680 | 2 | 48 |
| Standard_NV24 |24 |224 |1440 | 4 | 64 |

1 GPU = one-half M60 card.


