模块文件
config_data.py	全局配置中心，定义向量库、文本分割、模型、会话等所有可配置参数（如 chunk 大小、模型名）；
knowledge_base.py	知识库核心服务，实现文本 MD5 校验、文本分割、向量入库逻辑；
vector_stores.py	向量库封装，提供检索器（Retriever），对接 Chroma 完成相似性检索；
file_history_store.py	会话历史存储，基于文件系统实现聊天记录的增删改查，兼容 LangChain 消息模型；
rag.py	RAG 核心链路封装，组合 “检索 - 构造 Prompt - 调用大模型 - 解析输出” 全流程，支持会话历史集成；
app_file_uploader.py	Streamlit 前端页面，提供文件上传界面，调用知识库服务完成入库；
app_qa.py	Streamlit 前端页面，提供聊天交互界面，调用 RAG 服务完成智能问答；

具体文件内容

app_file_uploader与knowledge_base 负责离线文件转向量入库

vector_stores 负责查找输入文本的相似性句子的top-k

rag 负责将“输入”、“历史信息”、“相似度高的外部信息”结合传入提示词中，并将提示词入链完成询问

file_history_store 会话历史存储

config_data 配置文件

app_qa steamlit的前端界面

777




