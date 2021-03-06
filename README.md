![haproxy](https://csphere.cn/assets/a12cc8b7-0f3e-4716-aa6a-dc6901c099e6)

## 如何使用该镜像

最强大的负载均衡 - HAProxy, 用来保障服务平稳运行

### 启动
```console
$ image=index.csphere.cn/microimages/haproxy
$ docker run --name myhaproxy -d \
  -v haproxy.json:/etc/haproxy/haproxy.json \
  --cap-add=NET_ADMIN $image
```

`haproxy.json` 配置文件，用于配置哪些应用服务的容器加入到haproxy里面：
```json
{
  "admin_password": "{{.ADMIN_PASSWORD}}",
  "lb_instances": [
    {
      "app":"c",
      "service":"codesync",
      "port":21,
      "domain":"domain1.net",
      "path":"/path1/a/b"
    },{
      "app":"spring",
      "service":"greenhouse",
      "port":8080,
      "domain":"www.domain2.com"
    }
  ]
}
```
字段解释：

- `app` csphere里的应用名，每次部署一个项目时的唯一名称
- `service` 是每个应用中哪个服务放到`haproxy`负载均衡后面
- `port` 该服务监听的端口
- `domain` 类似：`www.app.com`
- `path` 类似: `/login`
- `admin_password`, 通过 `http://haproxy-ip/stats` 可以访问负载均衡统计

注意，同一个domain的不同path，可以映射到不同的应用和服务去，不同domain的相同path，可以映射到相同的应用和服务。这里的`path`稍作变化，可以灵活应用到灰度发布中去，且可以针对不同的项目做出定制化，比如根据`uid`的范围。

当服务的容器不断发生变化时，haproxy可以实时更新后端并自动reload，保证服务的平滑运行。在实际测试100万请求过程中不断增加容器后端，没有一个请求失败。

## 授权和法律

该镜像由希云制造，未经允许，任何第三方企业和个人，不得重新分发。违者必究。

## 支持和反馈

该镜像由希云为企业客户提供技术支持和保障，任何问题都可以直接反馈到: `docker@csphere.cn`

