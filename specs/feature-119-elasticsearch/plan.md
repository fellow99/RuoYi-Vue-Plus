# feature-119-elasticsearch 技术方案 (plan.md)

> 对应规格：spec.md | 模块：feature-119-elasticsearch | 最后更新：2026-08-10

## 1. 技术上下文
- Easy-Es 3.0.2, Elasticsearch Client 7.17.28, 默认关闭(easy-es.enable=false)

## 2. 模块结构
- ruoyi-common-elasticsearch: EasyEsConfiguration + ActuatorEnvironmentPostProcessor
- Demo: EsCrudController (/es): GET select, GET search, POST insert, PUT update, DELETE delete/{id}
- Demo entity: Document (easy-es mapper)

## 3. 文件清单

| 文件 | 用途 |
|------|------|
| ruoyi-common-elasticsearch/.../EasyEsConfiguration.java | ES 配置 |
| ruoyi-modules/ruoyi-demo/.../EsCrudController.java | Demo CRUD |
| ruoyi-modules/ruoyi-demo/.../esmapper/DocumentMapper.java | Easy-Es Mapper |
