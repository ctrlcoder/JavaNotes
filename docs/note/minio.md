## minio是什么？

是一个开源的对象存储服务，兼容 Amazon S3 云端存储服务，同时也支持本地磁盘、NAS、NFS、公有云存储、分布式云存储，非常适合用于存储非结构化数据，比如：图片、视频、日志、备份、容器镜像等。
对象大小最大能支持到 5TB。是一个非常轻量的服务,可以很简单的和其他应用的结合。

## 快速入门示例－文件上传
需要有存储服务的三个参数才能连接到该服务。

| 参数       | 说明                                         |
| ---------- | -------------------------------------------- |
| Endpoint   | 对象存储服务的URL                            |
| Access Key | Access key就像用户ID，可以唯一标识你的账户。 |
| Secret Key | Secret key是你账户的密码。                   |

```java
public class FileUploader {
  public static void main(String[] args) throws NoSuchAlgorithmException, IOException, InvalidKeyException, XmlPullParserException {
    try {
      // 使用MinIO服务的URL，端口，Access key和Secret key创建一个MinioClient对象
      MinioClient minioClient = new MinioClient("https://play.min.io", "Q3AM3UQ867SPQQA43P2F", "zuf+tfteSlswRu7BJ86wekitnifILbZam1KYY3TG");

      // 检查存储桶是否已经存在
      boolean isExist = minioClient.bucketExists("asiatrip");

      if(isExist) {
        System.out.println("Bucket already exists.");
      } else {
        // 创建一个名为asiatrip的存储桶，用于存储照片的zip文件。
        minioClient.makeBucket("asiatrip");
      }

      // 使用putObject上传一个文件到存储桶中。
      minioClient.putObject("asiatrip","asiaphotos.zip", "/home/user/Photos/asiaphotos.zip");

      System.out.println("/home/user/Photos/asiaphotos.zip is successfully uploaded as asiaphotos.zip to `asiatrip` bucket.");

    } catch(MinioException e) {
      System.out.println("Error occurred: " + e);
    }
  }
}
```
## API文档

### API文档: 操作存储桶

*   [makeBucket](https://docs.min.io/docs/java-client-api-reference#makeBucket)
*   [listBuckets](https://docs.min.io/docs/java-client-api-reference#listBuckets)
*   [bucketExists](https://docs.min.io/docs/java-client-api-reference#bucketExists)
*   [removeBucket](https://docs.min.io/docs/java-client-api-reference#removeBucket)
*   [listObjects](https://docs.min.io/docs/java-client-api-reference#listObjects)
*   [listIncompleteUploads](https://docs.min.io/docs/java-client-api-reference#listIncompleteUploads)

### API文档: 操作文件对象

*   [getObject](https://docs.min.io/docs/java-client-api-reference#getObject)
*   [putObject](https://docs.min.io/docs/java-client-api-reference#putObject)
*   [copyObject](https://docs.min.io/docs/java-client-api-reference#copyObject)
*   [statObject](https://docs.min.io/docs/java-client-api-reference#statObject)
*   [removeObject](https://docs.min.io/docs/java-client-api-reference#removeObject)
*   [removeIncompleteUpload](https://docs.min.io/docs/java-client-api-reference#removeIncompleteUpload)

### API文档: Presigned操作

*   [presignedGetObject](https://docs.min.io/docs/java-client-api-reference#presignedGetObject)
*   [presignedPutObject](https://docs.min.io/docs/java-client-api-reference#presignedPutObject)
*   [presignedPostPolicy](https://docs.min.io/docs/java-client-api-reference#presignedPostPolicy)

### API文档: 操作存储桶策略

*   [getBucketPolicy](https://docs.min.io/docs/java-client-api-reference#getBucketPolicy)
*   [setBucketPolicy](https://docs.min.io/docs/java-client-api-reference#setBucketPolicy)