# 对象存储功能规格 (spec.md)

> 模块：feature-103-oss | 状态：已实现 | 最后更新：2026-08-10

## 1. 模块概述

### 1.1 目的
基于 AWS S3 协议提供统一的分布式对象存储能力，支持 MinIO、RustFS 及所有 S3 兼容的云存储服务（阿里云 OSS、腾讯云 COS、七牛云等），实现文件上传、下载、删除、预签名 URL 生成，并通过后台配置动态切换存储服务商。

### 1.2 范围
- ✅ S3 协议统一抽象（OssClient 接口）
- ✅ MinIO 分布式文件存储
- ✅ RustFS 分布式文件存储
- ✅ 所有 S3 兼容云存储（阿里云/腾讯云/七牛云/华为云 OBS 等）
- ✅ 文件上传（支持 Path/File/InputStream/byte[]/ReadableByteChannel 多种数据源）
- ✅ 文件下载（支持 Path/File/OutputStream/WritableByteChannel 多种目标）
- ✅ 文件删除
- ✅ 预签名 URL 生成（上传/下载）
- ✅ 异步上传/下载
- ✅ 多存储配置动态切换（OssFactory 工厂模式）
- ✅ 访问策略控制（私有/公有读写/公有只读）
- ✅ OSS 对象管理（SysOssController，分页查询/上传/下载/删除）
- ✅ OSS 配置管理（SysOssConfigController，CRUD + 状态切换）

## 2. 用户故事
- 作为**管理员**，我可以配置多个对象存储服务商并随时切换
- 作为**用户**，我可以上传文件并获得可访问的文件 URL
- 作为**开发者**，我可以通过 OssFactory.instance() 获取统一的存储客户端

## 3. 功能需求
- FR-103-001: 系统 MUST 支持 S3 协议兼容的对象存储服务
- FR-103-002: 系统 MUST 支持文件上传（MultipartFile）并返回访问 URL
- FR-103-003: 系统 MUST 支持文件下载（通过 OSS ID）
- FR-103-004: 系统 MUST 支持文件删除（物理删除 OSS 对象）
- FR-103-005: 系统 MUST 支持多套 OSS 配置的 CRUD 和动态切换
- FR-103-006: 系统 SHOULD 支持预签名 URL 生成
- FR-103-007: 系统 SHOULD 支持异步上传/下载

## 4. 关键实体

| 实体 | 说明 |
|------|------|
| SysOss | OSS 对象记录 (sys_oss) |
| SysOssConfig | OSS 配置 (sys_oss_config) |
| SysOssExt | OSS 上传扩展参数 |
| OssClientConfig | 客户端运行时配置 |
| OssProperties | YAML 配置属性映射 |

## 5. 依赖
- AWS S3 SDK (software.amazon.awssdk:s3, s3-transfer-manager)
- Redisson（配置缓存）
- ruoyi-common-redis（CacheUtils/RedisUtils）
- ruoyi-system（SysOssController, SysOssConfigController）
