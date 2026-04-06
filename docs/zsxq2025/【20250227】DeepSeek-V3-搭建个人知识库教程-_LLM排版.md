
# DeepSeek V3 搭建个人知识库教程

## 前置准备
注册一个deepseek账号并获取密钥（api key），后面anythingLLM会使用这个密钥

## 接入deepseek
1. 打开deepseek官网：https://www.deepseek.com/
2. 点击右上角api 开放平台，登录或者注册一个新账号
3. 注册自己的账号，新注册的账号会赠送 10元免费额度（约500w tokens）
4. 点击 apikeys，创建一个新的密钥，并复制
5. 最后获取到的密钥类似：sk-xxxxxxxxxxxxx

## 下载并安装anythingLLM
浏览器打开anythingLLM官网，选择对应的系统下载，下载完成后直接安装即可
anythingLLM官方下载地址：https://anythingllm.com/desktop
AnythingLLM是一款功能强大且高度可定制的企业级应用程序，允许用户将任何文档、资源或内容转化为上下文，以便与大型语言模型（LLM）进行交互，本地部署所有数据和会话均存储在本机上。

## 点击进入配置页面
选择 deepseek 作为 AI 提供商
在LLM首选项，LLM提供商选择deepseek，输入前面获取到的deepseek密钥，模型选择【deepseek-chat】
模型说明（Chat Model Selection）：
- deepseek-chat：即deepseek-V3模型，响应快，价格相对便宜
- deepseek-reasoner：即deepseek-R1 推理模型，思维比较缜密，思考时间长，效果最好，费用较高

## 创建自己的工作区
返回首页，在左上角点击新工作区，建立一个自己的工作区，输入工作区名称

## 导入文档
点击导入文档，进入文档管理界面，拖拽文档/电子书/会议纪要/论文等所有碎片化内容
选择已经导入的文档，点击导入工作区并保存

## 开始聊天
至此，所有准备工作已经完成，返回页面，点击【NewThread】试一试吧

## 注意事项
分享资料仅供个人学习，请及时删除，切勿商用传播