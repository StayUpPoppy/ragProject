AI RAG 智能问答系统
基于检索增强生成（RAG）技术构建的轻量级智能问答系统，支持本地知识库上传、向量检索、多轮上下文对话，依托通义千问大模型实现基于知识库的精准问答，有效降低大模型 “幻觉” 问题。
项目简介
本项目以 LangChain 为核心框架，结合 Chroma 轻量级向量数据库、通义千问大模型与 Streamlit 可视化界面，实现 “知识库构建 - 向量检索 - 智能问答 - 会话留存” 全流程闭环。用户可上传 TXT 格式知识库文件，系统自动完成文本分块、向量入库；问答环节基于知识库检索结果生成回答，并保留会话历史，支持多轮上下文交互。
核心功能
知识库管理：支持 TXT 文件上传，通过 MD5 校验避免重复入库，长文本自动分块后转为向量存储至 Chroma；
精准检索：基于文本嵌入模型实现相似性检索，从知识库中匹配与问题最相关的文本片段；
智能问答：结合检索结果、会话历史构造 Prompt，调用通义千问生成专业回答，流式输出提升交互体验；
会话留存：基于本地文件系统持久化存储会话历史，支持多轮上下文对话；
可视化界面：通过 Streamlit 快速构建 Web 界面，无需前端开发，开箱即用。


快速开始
1. 环境准备
Python 3.8+
安装依赖：
bash
运行
pip install langchain langchain-chroma langchain-community streamlit dashscope chromadb
配置通义千问 API Key：
在系统环境变量中添加 DASHSCOPE_API_KEY，值为你的通义千问 API 密钥（可从阿里云百炼平台获取）。
2. 项目结构
plaintext
├── app_file_uploader.py    # 知识库上传页面（Streamlit）
├── app_qa.py               # 智能问答页面（Streamlit）
├── knowledge_base.py       # 知识库核心服务（上传、分块、入库）
├── vector_stores.py        # 向量库封装（检索器实现）
├── file_history_store.py   # 会话历史存储（本地文件）
├── rag.py                  # RAG 核心链路（检索+问答）
├── config_data.py          # 全局配置（向量库、模型、分块规则等）
├── chroma_db/              # Chroma 向量库持久化目录（自动生成）
├── chat_history/           # 会话历史存储目录（自动生成）
└── md5.text                # MD5 记录文件（自动生成，去重用）
3. 运行项目
步骤 1：启动知识库上传页面
bash
运行
streamlit run app_file_uploader.py
页面访问：浏览器打开 http://localhost:8501
操作：上传 TXT 格式的知识库文件，系统自动完成文本分块、向量入库（重复内容会跳过）。
步骤 2：启动智能问答页面
bash
运行
streamlit run app_qa.py
页面访问：浏览器打开 http://localhost:8502
操作：输入问题，AI 基于知识库内容生成回答，支持多轮对话。
核心配置（config_data.py）
可根据需求调整以下核心参数：
python
运行
# MD5 记录文件路径
md5_path = "./md5.text"

# Chroma 向量库配置
collection_name = "rag"
persist_directory = "./chroma_db"

# 文本分块配置
chunk_size = 1000          # 每个文本块字符数
chunk_overlap = 100        # 文本块重叠字符数
separators = ["\n\n", "\n", ".", "!", "?", "。", "！", "？", " ", ""]  # 分割符
max_split_char_number = 1000  # 文本分割阈值（小于该值不分割）

# 检索配置
similar_threshold = 1      # 检索返回最相似的Top-K数量

# 模型配置
embedding_model_name = "text-embedding-v4"  # 嵌入模型
chat_model_name = "qwen3-max"               # 大模型

# 会话配置
session_config = {
    "configurable": {
        "session_id": "user_001",  # 固定会话ID（可扩展为动态生成）
    }
}
核心流程
1. 知识库构建流程
plaintext
用户上传TXT文件 → 读取文本内容 → MD5校验（去重）→ 长文本分块 → 文本块转向量 → 向量+元数据存入Chroma → 记录MD5
2. 智能问答流程
plaintext
用户输入问题 → 向量检索（Chroma）→ 拼接「问题+会话历史+检索结果」构造Prompt → 调用通义千问生成回答 → 流式输出 → 保存会话历史
项目特点
轻量化：基于本地文件系统和 Chroma 向量库，无需云数据库，部署成本低；
防重复：MD5 校验机制避免相同内容多次入库，节省存储空间；
会话感知：保留聊天历史，支持多轮上下文对话，提升问答连贯性；
高可配：所有核心参数集中配置，文本分块、模型、检索规则可灵活调整；
低幻觉：严格基于知识库检索结果回答，大幅降低大模型幻觉问题。
待优化方向
文件类型扩展：支持 PDF、Word、Markdown 等多格式文件上传；
检索优化：增加关键词 + 向量混合检索、调整检索 Top-K 数提升精准度；
会话管理：支持多会话切换、删除、重命名，替代固定会话 ID；
多模型适配：支持 OpenAI、智谱等多厂商大模型，配置化切换；
性能优化：大文件入库异步处理，避免页面卡顿；
权限控制：增加用户认证，区分不同用户的知识库和会话；
日志监控：增加操作日志、检索命中率统计，便于问题排查。
注意事项
确保通义千问 API Key 有效，且拥有足够的调用额度；
知识库文件建议为 UTF-8 编码的 TXT 文件，避免乱码；
文本分块参数（chunk_size、chunk_overlap）需根据知识库内容调整，平衡检索精准度和效率；
会话历史存储在本地 chat_history 目录，如需迁移可直接拷贝该目录。
