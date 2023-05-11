【每日prompt】
什么是langchain 的 indexs(检索器)？
检索器用来解析数据并构建索引，更方便的检索数据，切割的同时避免破坏数据的完整性。
大文本数据的切割，官方提供markdown, txt, python，latex等格式支持。
常用的文件数据，官方提供pdf，HTML，Markdown等格式支持。
常用的三方平台，官方提供B站，youtube，Notion，Twitter等支持。
解析数据并构建索引后，可以使用三方的向量数据库存储，官方提供了Chroma，Deep Lake，FAISS，Pinecone等支持。
甚至官方直接实现了一些索引器，SelfQueryRetriever，CohereRerank等。

下面是openai的ChatGPT Retriever Plugin演示，用自然语言索引数据，并输出目标格式。
