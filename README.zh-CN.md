# MOLTO-IT
[![DOI](https://zenodo.org/badge/169291732.svg)](https://zenodo.org/badge/latestdoi/169291732)

[English](README.md) | 简体中文

MOLTO-IT（Multi-Objective Low-Thrust Optimizer for Interplanetary Trajectories）是一个全自动 Matlab 工具，用于低推力、多引力借力轨道的初步设计。软件分两层：外层用遗传算法（NSGA-II）做多目标优化，内层对基于形状的低推力轨道参数化调用 fmincon 做梯度优化。工具包含若干简化假设，例如天体共面、沿轨迹不施加推进约束、低推力段采用形状法参数化。

MOLTO-IT 的目标是尽量减少人工干预。用户只需输入一组参数，包括探测器的出发天体、最终目标、部分硬件特征（运载火箭、质量、推进系统），以及发射日期范围、飞行时间范围和可用作借力的行星列表。软件据此自动计算出高效完成任务的低推力轨道集合，包括借力次数、借力序列与构型。候选轨道按总飞行时间和推进剂消耗量评价并相互比较。

工具易于使用，能在普通桌面计算机上设计多种任务。计算时间从数分钟到数小时不等，取决于搜索空间的大小。

### 目标
MOLTO-IT 旨在提供一个快速、稳健的任务设计环境，使用户能以较低成本对多种任务构型做权衡研究，并开展低精度分析。

### 数学表述
方法学的详细数学描述见论文：

David Morante, Manuel Sanjurjo Rivo, and Manuel Soler. "Multi-Objective Low-Thrust Interplanetary Trajectory Optimization Based on Generalized Logarithmic Spirals", Journal of Guidance, Control, and Dynamics, Vol. 42, No. 3 (2019), pp. 476-490.
https://doi.org/10.2514/1.G003702

## 安装指南
安装只需下载 [MOLTO-IT](https://github.com/uc3m-aerospace/MOLTO-IT/) 并把根目录加入 Matlab 路径。

### 依赖
需要较新版本的 Matlab（R2016a 或更新版本）。使用大部分功能需要 [Matlab Optimization Toolbox](https://es.mathworks.com/help/optim/index.html)。

下载 [Spice Toolkit](https://naif.jpl.nasa.gov/naif/toolkit.html) 的 Matlab 接口，用于行星、彗星和小行星的星历计算。把下载的文件夹及其子文件夹放入 `MOLTO-IT/spice`。

下载对应的星历 [NAIF](https://naif.jpl.nasa.gov/pub/naif/generic_kernels/spk/) .bsp 内核，放入 `MOLTO-IT/spice/kernels`。

注意：运行示例场景所需的星历内核已包含在本发行版中。

修改函数 `MOLTO-IT/spice/load_spice_kernels.m` 以加载新内核。

## 快速使用指南

优化任务时，用户调用主函数 molto_it.m 并提供输入结构体。示例如下：

```matlab
% 飞往木星的借力任务，最多 3 次借力

        input.problem_name  = example;  % 问题名称
        input.problem_type  = 'flyby';  % 任务类型：到达天体的处理方式（flyby/rendezvous）
        input.planet_dep    = 'Earth';  % 出发行星，使用空间命名（例如 3 表示 Earth）
        input.planet_arr    = '5';      % 目标行星，使用空间命名（例如 5 表示 Jupiter）
        input.vinf0_max     =  2;       % 出发行星处的双曲剩余速度（km/s）
        input.planet_fb     = [{'4'},{'3'},{'2'},{'4'},{'3'},{'2'}]; % 可用作借力的行星列表，使用 spice 命名
        input.rfb_min       = 200;      % 最小借力高度（km）
        input.n_fb          = [0,3];    % 可能借力次数的最小/最大值
        input.rev           = [0,0];    % 可能圈数的最小/最大值
        input.ToF           = [50  50  50  50; % 每段转移时间的最小/最大值（天）
            500 500 500 1000];
        input.Isp           = 3000;     % 秒
        input.thrust        = 100;      % mN
        input.nthrusters    = 1;        % 推进器数量
        input.mass          = 1000;     % 千克
        input.power         = 5000;     % W
        input.Initial_Date  = [{'2029 Jan 01 00:00:00'},{'2030 Dec 31 00:00:00'}]; % 最早/最晚发射日期（公历日期）
        input.init_file     = [];       % 初始种群文件名（不提供则随机生成初始种群）
        input.output_file   = [problem_name,'.txt']; % 解种群文件名
        input.plot          = 0;        % 绘图选项（建议 0，选项 1 仍在开发中）
        input.useParallel   = 'yes';    % yes/no，遗传算法是否并行执行
        input.options       = [];       % 开发中
        input.maxGen        = 200;      % 最大代数
        input.popsize       = 200;      % 种群规模

% 运行 MOLTO-IT 算法
        molto_it(input)
```
注意：确保所有文件夹和子文件夹都在 Matlab 路径中。

随后会弹出下面的窗口，可从中观察 Pareto 前沿的演化过程。

![弹出窗口](display.png)
