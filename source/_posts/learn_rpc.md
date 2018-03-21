---
title: 我所理解的RPC框架
date: 2018-03-06  
tags: RPC
---
RPC（Remote Procedure Call）指远程过程调用，也就是说，两台服务器A，B，服务器A想要调用服务器B上提供的函数/方法，由于不在一个进程中，或不在一个服务器上，
不能直接调用，那么这时就需要通过网络来调用和传达调用的数据。  
<!-- more -->
那么：   
1.通讯：主要是通过在客户端和服务器之间建立TCP连接，远程过程调用的所有交换的数据都在这个连接里传输。连接可以是按需连接，调用结束后就断掉，也可以是长连接，多个远程过程调用共享同一个连接。  
2.寻址问题：服务注册与发现的注册中心  
3.当A服务器上的应用发起远程过程调用时，方法的参数需要通过底层的网络协议如TCP传递到B服务器，由于网络协议是基于二进制的，内存中的参数值要序列化成二进制的形式，也就是序列化或编组，通过寻址和传输将序列化的二进制发送给B服务器。
4.B服务器收到请求后，需要对参数进行反序列化的操作，恢复为内存中的表达方式。然后找到对应的方法(寻址)进行本地调用，然后得到返回值。  
5.返回值还要发送会服务器A上的应用，也要进过序列化的方式发送，服务器A接到后，再反序列化，恢复为内存中的表达方式，交给A服务器上的应用。

#### RPC与REST对比
这时或许有人会提出跟我一样的疑问，REST同样可以调用到另一台机器的接口，为什么还要用RPC呢？

1.简化我们的代码量

如果说我们需要使用REST接口的方式调用另外一台服务器上的接口，你将需要写下面这一坨代码。



```bash
public String load(String url, String query) {

        try {

            //将url转变成URL类对象
            URL restUrl = new URL(url);
            //打开URL连接
            HttpURLConnection conn = (HttpURLConnection) restUrl.openConnection();
            //设置连接参数
            conn.setRequestProperty("Content-Type", "application/json");
            conn.setRequestMethod("POST");
            conn.setDoOutput(true);
            conn.setAllowUserInteraction(false);

            //发送URL请求
            OutputStream outputStream = conn.getOutputStream();

            PrintStream ps = new PrintStream(outputStream);
            ps.print(query);
            ps.close();

            //获取响应
            InputStream inputStream = conn.getInputStream();

            BufferedReader bReader = new BufferedReader(new InputStreamReader(inputStream));
            String line;
            StringBuilder resultStr = new StringBuilder();

            while (null != (line = bReader.readLine())) {

                resultStr.append(line);
            }

            bReader.close();

            return resultStr.toString();

        } catch (MalformedURLException e) {
            e.printStackTrace();
        } catch (ProtocolException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }

        return url;
    }

    public static void main(String []args) {

        try {

            RestUtil restUtil = new RestUtil();

            String jsonStr = "{\"cmd\":\"cn_member/user/isAloneRole\",\"parameters\":{\"mobile\":\"17321204910\"}}";
            String resultString = restUtil.load("http://api.dev/cgi/", jsonStr);

            System.out.print(resultString);
        } catch (Exception e) {

            // TODO: handle exception

            System.out.print(e.getMessage());

        }

    }
```

如果是rpc方式的你只需要一行代码就可以完成。

2.不用维护大量的文档，降低沟通成本

如果说我们采用REST接口的调用方式，若是该REST接口加减了参数，可能会导致你的代码报错，那么你就需要实时去查阅你调用的接口有没有相应的修改，这个代价还是蛮大的。
而dubbo方式的接口参数一目了然，无论你参数的加减都可以很直观的看到，不需要额外维护文档。

3.rpc支持长连接

rpc可以支持短连接，也可以支持长连接。而http协议是无状态的短连接

4.rpc支持多种协议

5.rpc方式提高性能，http带了很多请求头，减少了网络传输过程中的开销

rpc的底层是通过socket进行传输的，他是基于tcp实现的。rpc基于内网调用，REST主要解决的是外网。

6.面向服务，服务治理

rpc提供了很多服务治理，他注重于消费者、提供者自主的去向注册机注册服务；只需要一个服务注册中心，就可以动态的注册和发现服务，是服务的位置透明；
而REST风格的方式借助于nginx做负载均衡，还需要自己手动的去配置路由，当服务越来越多是，服务URL配置管理变得非常困难，F5硬件负载均衡器的单点压力也越来越大。
消费者需要哪种服务，不需要关注哪台服务器去给他提供了服务。

参考地址：https://www.zhihu.com/question/25536695





