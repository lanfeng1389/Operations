# minio迁移到oss

```javascript
1、安装mc
wget https://dl.min.io/client/mc/release/linux-amd64/mc
chmod +x mc
sudo mv mc /usr/local/bin/

```

**获取阿里专有云 OSS 的 S3 兼容信息**（联系管理员）：

*   **Endpoint**：如 `http://oss-cn-hzjbp-a.example.com`（通常是内网地址）
    
*   **AccessKey ID / Secret**（具备 `PutObject`, `GetObject` 权限）
    
*   **Bucket 名称**（目标桶需已创建）
    

**确认 MinIO 集群可访问**：

*   MinIO 地址：如 `http://minio-cluster.local:9000`
    
*   AccessKey / Secret
    

```javascript
mc alias set minio-local \
  http://minio-cluster.local:9000 \
  YOUR_MINIO_ACCESS_KEY \
  YOUR_MINIO_SECRET_KEY \
  --api S3v4
```
```javascript
mc alias set alioss \
  http://oss-cn-hzjbp-a.example.com \
  YOUR_ALIYUN_ACCESS_KEY \
  YOUR_ALIYUN_SECRET_KEY \
  --api S3v4 \
  --path=on
```
```javascript
1、全量同步
mc mirror minio-local/source-bucket alioss/dest-bucket

2、额外网络测试
mc admin info minio-local
# 应返回集群状态，而非 timeout
curl -I http://oss-cn-hzjbp-a.example.com
# 正常应返回 HTTP 403（Forbidden）——说明网络通，只是没权限
```