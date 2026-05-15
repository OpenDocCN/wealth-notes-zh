
# Deepseek+R1+本地部署完全手册

## 简介
Deepseek R1 是支持复杂推理、多模态处理、技术文档生成的通用大语言模型。本手册为技术团队提供完整的本地部署指南，涵盖硬件配置、国产芯片适配、量化方案、云端替代方案及完整671B MoE模型的Ollama部署方法。

## 核心提示
个人用户：不建议部署32B及以上模型，硬件成本极高且运维复杂。
企业用户：需专业团队支持，部署前需评估ROI（投资回报率）。

## 本地部署核心配置要求

### 1. 模型参数与硬件对应表

| 模型参数 | Windows 配置要求 | Mac 配置要求 | 适⽤场景 |
|----------|------------------|--------------|----------|
| - RAM: 4GB | - 内存: 8GB | 简单文本生成、基础代码补全 | 1.5B |
| - GPU: 集成显卡/现代CPU（M1/M2/M3） | - 存储: 5GB | 中等复杂度问答、代码调试 | 7B Pro/M3 |
| - RAM: 8-10GB | - 存储: 8GB | 复杂推理、技术文档生成 | 14B Max |
| - GPU: RTX 3090（24GB VRAM） | - 存储: 20GB | 科研计算、大规模数据处理 | 32B+ |
|  |  | 企业级部署（需多卡并联） | 暂不支持 |

## 算力需求分析

| 模型 | 最低算力需求 |
|------|--------------|
| DeepSeek-R1 (671B) | FP8 ≥890GB (2*XE9680（16*H20 GPU）) |
| DeepSeek-R1-Distill-70B | BF16 ≥180GB (4*L20 或 2*H20 GPU) |

## 国产芯片与硬件适配方案

### 1. 国内生态合作伙伴动态

| 企业 | 适配内容 |
|------|----------|
| 华为昇腾 | 昇腾910B原生支持R1全系列，提供端到端推理优化（等效A100 FP16） |
| 腾讯方案 | 沐曦MXN系列支持70B模型BF16推理，显存利用率提升30%（等效RTX 3090） |
| 海光DCU | 适配V3/R1模型，性能对标NVIDIA A100（BF16） |

### 2. 国产硬件推荐配置

| 模型参数 | 推荐方案 | 适⽤场景 |
|----------|----------|----------|
| 1.5B | 太初T100加速卡 | 个人开发者原型验证 |
| 14B | 昆仑芯K200集群 | 企业级复杂任务推理 |
| 32B | 壁彻算力平台+昇腾910B集群 | 科研计算与多模态处理 |

## 云端部署替代方案

### 1. 国内云服务商推荐

| 平台 | 核心优势 | 适⽤场景 |
|------|----------|----------|
| 硅基流动 | 官方推荐API，低延迟，支持多模态模型 | 企业级高并发推理 |
| 腾讯云 | 一键部署+限时免费体验，支持VPC私有化 | 中小规模模型快速上线 |
| PPIO派欧云 | 价格仅为OpenAI 1/20，注册赠5000万tokens | 低成本尝鲜与测试 |

## 完整671B MoE模型部署（Ollama+Unsloth）

### 1. 量化方案与模型选择

| 量化版本 | 最低内存+显存需 | 适⽤场景 |
|----------|----------------|----------|
| DeepSeek-R1-UD-IQ1_M | 158 GB ≥200 GB | 消费级硬件（如Mac Studio） |
| DeepSeek-R1-Q4_K_M | 404 GB ≥500 GB | 高性能服务器/云GPU |

### 2. 硬件配置建议

| 硬件类型 | 推荐配置 | 性能表现（短文本生成） |
|----------|----------|--------------------------|
| 消费级设备 | Mac Studio（192GB统一内存） | 10+ token/秒 |
| 高性能服务器 | 4×RTX 4090（96GB显存+384GB内存） | 7-8 token/秒（混合推理） |

### 3. 部署步骤（Linux示例）

1. 安装依赖工具：
   ```bash
   /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
   brew install llama.cpp
   ```

2. 下载并合并模型分⽚：
   ```bash
   llama-gguf-split --merge DeepSeek-R1-UD-IQ1_M-00001-of-00004.gguf DeepSeek-R1-UD-IQ1_S.gguf
   ```

3. 安装Ollama：
   ```bash
   curl -fsSL https://ollama.com/install.sh | sh
   ```

4. 创建Modelfile：
   ```plaintext
   FROM /path/to/DeepSeek-R1-UD-IQ1_M.gguf
   PARAMETER num_gpu 28 # 每块RTX 4090加载7层（共4卡）
   PARAMETER num_ctx 2048
   PARAMETER temperature 0.6
   TEMPLATE "<｜end▁of▁thinking｜>{{ .Prompt }}<｜end▁of▁thinking｜>"
   ```

5. 运行模型：
   ```bash
   ollama create DeepSeek-R1-UD-IQ1_M -f DeepSeekQ1_Modelfile
   ollama run DeepSeek-R1-UD-IQ1_M --verbose
   ```

### 4. 性能调优与测试

- GPU利用率低：升级高带宽内存（如DDR5 5600+）。
- 扩展交换空间：
  ```bash
  sudo fallocate -l 100G /swapfile
  sudo chmod 600 /swapfile
  sudo mkswap /swapfile
  sudo swapon /swapfile
  ```

## 注意事项与风险提示

### 1. 成本警示
- 70B模型：需3张以上80G显存显卡（如RTX A6000），单卡用户不可行。
- 671B模型：需8xH100集群，仅限超算中心部署。

### 2. 替代方案
个人用户推荐使用云端API（如硅基流动），免运维且合规。

### 3. 国产硬件兼容性
需使用定制版框架（如昇腾CANN、沐曦MXMLLM）。

## 附录：技术支持与资源

- 华为昇腾：昇腾云服务
- 沐曦GPU：免费API体验
- 李锡涵博客：完整部署教程

## 结语
Deepseek R1 的本地化部署需极高的硬件投入与技术门槛，个人用户务必谨慎，企业用户应充分评估需求与成本。通过国产化适配与云端服务，可显著降低风险并提升效率。技术无极限，理性规划方能降本增效！

手册更新与反馈：如有补充或修正，请联系文档作者，接入细节请阅读详细文档硅基流动社区。

## 全球企业个人渠道附表

1. 秘塔搜索：https://metaso.cn
2. 360纳米AI搜索：https://www.n.cn/
3. 硅基流动：https://cloud.siliconflow.cn/i/OBklluwO
4. 字节跳动火山引擎：https://console.volcengine.com/ark/region:ark+cn-beijing/experience
5. 百度云千帆：https://console.bce.baidu.com/qianfan/modelcenter/model/buildIn/list
6. 英伟达NIM：https://build.nvidia.com/deepseek-ai/deepseek-r1
7. Groq：https://groq.com/
8. Fireworks：https://fireworks.ai/models/fireworks/deepseek-r1
9. Chutes：https://chutes.ai/app/chute/
10. Github：https://github.com/marketplace/models/azureml-deepseek/DeepSeek-R1/playground
11. POE：https://poe.com/DeepSeek-R1
12. Cursor：https://cursor.sh/
13. Monica：https://monica.im/invitation?c=ACZ7WJJ9
14. Lambda：https://lambdalabs.com/
15. Cerebras：https://cerebras.ai
16. Perplexity：https://www.perplexity.ai
17. 阿里云百炼：https://api.together.ai/playground/chat/deepseek-ai/DeepSeek-R1

## 芯片企业支持附图
## 云厂商智算企业支持附图

注：厂商支持图表版权归智东西。