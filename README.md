# FloorplanToBlender3d

原仓库地址：https://github.com/grebtsew/FloorplanToBlender3d/

本人使用 Windows11 和 Docker Desktop 部署

> 说明
> 1. 本人只运行了server模式，其他模式未测试
> 2. 当前文档内容与 docker-compose.server.yml 相互关联
> 3. 通过formdata方式传递图片依旧不好使，我也懒得改，如有问题，请自行解决

## 我的改动

1. 不再使用预构建的Docker镜像，而是以当前prod分支代码为基础构建镜像
2. 优化了 docker-compose.server.yml 文件，增加了一些配置，优化了因国内外网络差异导致依赖找不到、作者原代码报错等各种奇怪问题
3. 修改server_config.ini配置文件，使 RestApi 可以被跨域访问
4. 优化object接口相关函数，下载时会自动填充文件名及后缀

## 部署方式

1. 确保本地的Docker支持docker-compose，并且docker镜像地址可以正常使用

    本人使用的镜像地址是：https://docker.xuanyuan.me 配置方式如下 
    ```json
    {
        ...
        "registry-mirrors": [
            "https://docker.xuanyuan.me"
        ]
    }
    ```
2. 进入到当前项目根目录下，执行以下命令
   ```bash
   docker-compose -f docker-compose.server.yml up
   ```

3. 若Docker Desktop里查看两个容器都正常启动，则部署完成

## API 端点和使用方法
服务器提供了三种类型的请求处理：GET、POST 和 PUT。以下是主要的 API 端点

### 1. 查看 API 文档
您可以通过访问 Swagger UI 界面来查看完整的 API 文档：

```http request
http://localhost:8001
```

### 2. 基本工作流程
将平面图转换为 3D 模型的基本工作流程是：
1. 创建一个 ID（POST /create）
2. 上传图片（PUT /create）
3. 转换图片为 3D 模型（POST /transform）
4. 查看是否转换完成（GET /processes）
5. 下载 3D 模型（GET /object）

或者，可以一次性完成上传和转换（PUT /createandtransform）

### 3. 主要 API 功能

> 注：有些API需要提供file参数，file参数不可直接拼接在url后面，需要使用`multipart/form-data`格式，详情查阅swagger文档

#### GET 请求
- `http://localhost:8000/?func=info` - 获取服务器信息
- `http://localhost:8000/?func=all` - 获取所有文件
- `http://localhost:8000/?func=images` - 获取所有图片
- `http://localhost:8000/?func=objects` - 获取所有对象
- `http://localhost:8000/?func=processes` - 获取所有进程
- `http://localhost:8000/?func=image?id=<id>` - 获取特定 ID 的图片
- `http://localhost:8000/?func=object?id=<id>&oformat=<format>` - 获取特定 ID 的对象
- `http://localhost:8000/?func=configfiles` - 获取所有配置文件
- `http://localhost:8000/?func=stackingfiles` - 获取所有堆叠文件
#### POST 请求
- `http://localhost:8000/?func=create` - 创建新的 ID 和哈希值
- `http://localhost:8000/?func=remove?id=<id>` - 删除指定 ID 关联的所有文件
- `http://localhost:8000/?func=transform?func=transform&id=<id>&oformat=<format>` - 将图片转换为 3D 对象
#### PUT 请求
- `http://localhost:8000/?func=create?id=<id>&hash=<hash>&iformat=<format>` - 上传图片
- `http://localhost:8000/?func=createandtransform?id=<id>&hash=<hash>&iformat=<format>&oformat=<format>` - 上传图片并开始转换过程



