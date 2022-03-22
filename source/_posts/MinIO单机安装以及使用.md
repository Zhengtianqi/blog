title: MinIO单机安装以及使用
author: ztq
tags:

  - 分布式存储
categories:
  - 数据库
date: 2022-03-22 21:55:00

---

一、简介	

​		MinIO 是在 GNU Affero 通用公共许可证 v3.0 下发布的高性能对象存储。它与 Amazon S3 云存储服务 API 兼容。使用 MinIO 为机器学习、分析和应用程序数据工作负载构建高性能基础架构。

相关文档：

官方文档：https://docs.min.io/docs/minio-quickstart-guide.html

官方文档（中文）：http://docs.minio.org.cn/docs/master/minio-monitoring-guide

官方首页（中文）http://www.minio.org.cn/



二、单机版安装

1、搜索镜像

```java
docker search minio
```

2、拉取镜像

```java
docker pull minio/minio
```

3、启动与安装镜像

```java
docker run \
  -p 9000:9000 \
  -p 9001:9001 \
  -d --restart=always \
  --name minio1 \
  -v /home/environment/minio/data:/data \
  -v /home/environment/minio/config:/root/.minio \
  --privileged=true \
  minio/minio server /data --console-address ":9001"
```

-it 表示运行参数
-p 表示暴露端口
-d 表示后台运行
-v 卷挂载（容器到主机的映射，避免存容器中丢失数据）

9001是管理页，默认账号密码均为minioadmin，也可以设置密码  9000

```java
-e "MINIO_ACCESS_KEY=账号" \
-e "MINIO_SECRET_KEY=密码" \
```

4、查看运行镜像

```java
docker ps
```



三、JAVA API demo

1、引入依赖

```java
<dependency>
	<groupId>com.squareup.okhttp3</groupId>
	<artifactId>okhttp</artifactId>
	<version>4.8.1</version>
</dependency>
	
<dependency>
	<groupId>io.minio</groupId>
	<artifactId>minio</artifactId>
	<version>8.3.0</version>
	<exclusions>
		<exclusion>
			<groupId>com.squareup.okhttp3</groupId>
			<artifactId>okhttp</artifactId>
        </exclusion>
	</exclusions>
</dependency>
```

minio强依赖于okhttp3，低版本okhttp3会报错：https://blog.csdn.net/u014698745/article/details/122025869

若剔除所有的okhttp3还是不能运行，则将minio版本降低。

2、MinIO客户端

```java
import io.minio.*;
import io.minio.http.Method;
import io.minio.messages.Bucket;
import io.minio.messages.Item;
import org.apache.commons.lang3.StringUtils;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.stereotype.Component;

import java.io.File;
import java.io.FileInputStream;
import java.io.InputStream;
import java.net.URLEncoder;
import java.util.ArrayList;
import java.util.List;
import java.util.concurrent.TimeUnit;

public class S3Utils {
    private static final Logger logger = LoggerFactory.getLogger(S3Utils.class);

    private static MinioClient s3Client;

    static {
        try {
            s3Client = MinioClient.builder()
                    .endpoint(SdkConstant.endpoint)
                    .credentials(SdkConstant.s3AccessKey, SdkConstant.s3AccessSecret)
                    .build();
        } catch (Exception e) {
            logger.error("初始化s3Client时出错!", e);
        }
        if (null == s3Client) {
            logger.error("创建s3Client时出错!");
        }
    }

    /**
     * 创建一个桶
     */
    public static void createBucket(String bucket) throws Exception {
        boolean found = s3Client.bucketExists(BucketExistsArgs.builder().bucket(bucket).build());
        if (!found) {
            s3Client.makeBucket(MakeBucketArgs.builder().bucket(bucket).build());
        }
    }

    /**
     * 上传一个文件
     */
    public static void uploadFile(InputStream stream, String bucket, String objectName) throws Exception {
        s3Client.putObject(PutObjectArgs.builder().bucket(bucket).object(objectName)
                .stream(stream, -1, 10485760).build());
    }

    /**
     * 上传一个文件,并返回文件url
     */
    public static String uploadFileReturnUrl(InputStream stream, String bucket, String objectName) throws Exception {
        s3Client.putObject(PutObjectArgs.builder().bucket(bucket).object(objectName)
                .stream(stream, -1, 10485760).build());
        String url = s3Client.getPresignedObjectUrl(
                GetPresignedObjectUrlArgs.builder()
                        .method(Method.GET)
                        .bucket(bucket)
                        .object(objectName)
                        .expiry(7, TimeUnit.DAYS)
                        .build());
        return StringUtils.split(url, "?")[0];
    }

    /**
     * 列出所有的桶
     */
    public static List<String> listBuckets() throws Exception {
        List<Bucket> list = s3Client.listBuckets();
        List<String> names = new ArrayList<>();
        list.forEach(b -> {
            names.add(b.name());
        });
        return names;
    }

    /**
     * 列出一个桶中的所有文件和目录
     */
    public static List<S3FileInfo> listFiles(String bucket) throws Exception {
        Iterable<Result<Item>> results = s3Client.listObjects(
                ListObjectsArgs.builder().bucket(bucket).recursive(true).build());

        List<S3FileInfo> infos = new ArrayList<>();
        results.forEach(r -> {
            S3FileInfo info = new S3FileInfo();
            try {
                Item item = r.get();
                info.setFileName(item.objectName());
                info.setDirectory(item.isDir());
                infos.add(info);
            } catch (Exception e) {
                e.printStackTrace();
            }
        });
        return infos;
    }

    /**
     * 下载一个文件
     */
    public static InputStream download(String bucket, String objectName) throws Exception {
        InputStream stream = s3Client.getObject(
                GetObjectArgs.builder().bucket(bucket).object(objectName).build());
        return stream;
    }

    /**
     * 删除一个桶
     */
    public static void deleteBucket(String bucket) throws Exception {
        s3Client.removeBucket(RemoveBucketArgs.builder().bucket(bucket).build());
    }

    /**
     * 删除一个对象
     */
    public static void deleteObject(String bucket, String objectName) throws Exception {
        s3Client.removeObject(RemoveObjectArgs.builder().bucket(bucket).object(objectName).build());
    }


    public static void main(String[] args) throws Exception {
        S3Utils.createBucket(SdkConstant.bucketName);
        File file = new File("C:\\Users\\27049\\Desktop\\minio docker.txt");
        InputStream inputStream = new FileInputStream(file);
        System.out.println(S3Utils.uploadFileReturnUrl(inputStream, SdkConstant.bucketName, "minio docker.txt"));
    }
}

```

其中：S3FileInfo中只有两个字段，fileName和directory

创建连接的参数为：

SdkConstant.s3AccessKey 用户名

SdkConstant.s3AccessSecret 密码

SdkConstant.endpoint MinIO所在地址（ip:9000）

存储位置：指定的data目录/桶名/...文件夹名.../文件名



getPresignedObjectUrl()返回的url最大支持7天，若想永久使用，则配置

https://blog.csdn.net/instanceof_zjl/article/details/109601131?utm_medium=distribute.pc_aggpage_search_result.none-task-blog-2~aggregatepage~first_rank_ecpm_v1~rank_v31_ecpm-2-109601131.pc_agg_new_rank&utm_term=java+%E8%AE%BE%E7%BD%AE+minio%E6%B0%B8%E4%B9%85%E8%AE%BF%E9%97%AE%E9%93%BE%E6%8E%A5&spm=1000.2123.3001.4430

![image-20220322221741703](/img/image-20220322221741703.png)
