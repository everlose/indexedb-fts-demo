## IndexeDB 全文搜索探索 demo

使用方式

1. 本地打开 index.html
2. 输入页面中的文本框，插入若干条消息。
3. 在输入查询关键字查询结果。

## 原理

插入：

1. index.html，documents 表 term 字段存入前使用 FullText.tokenize(msg.text) 去解析 text
2. fulltext.js 的 tokenize => new Intl.Segmenter 去构建分词规则
3. segment.js 对于中文使用单字分词方法，对于英文按空格分词
4. 最后请观察 documents 表的 terms 是一个数组，数组里存放每一个中文字符。

查询

1. index.html 里 FullText.search(index, keyword, ...)
2. 按关键词 keyword 分词，并按分词的数量打开索引游标 index.openKeyCursor(term)
3. 比对并累加游标前进，找出所有游标都匹配的节点id（docId）

## 问题

1. 数据量越大，再往上插入若干条就越慢（indexdb 或者说 leveldb 的老问题）
2. 随着查询的内容多，分词多，打开的索引游标就多。查询的耗时会非线形增加。直到不可接受。
