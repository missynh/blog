---
title: 我所理解的RPC框架
date: 2018-03-06  
tags: RPC
---
RPC（Remote Procedure Call）指远程过程调用，也就是说，两台服务器A，B，服务器A想要调用服务器B上提供的函数/方法，由于不在一个进程中，或不在一个服务器上，
不能直接调用，那么这时就需要通过网络来调用和传达调用的数据。

这时或许有人会提出跟我一样的疑问，REST同样可以调用到另一台机器的接口，为什么还要用RPC呢？

1.简化我们的代码量

如果说我们需要使用REST接口的方式调用另外一台服务器上的接口，你将需要写下面这一坨代码。

<!-- more -->

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

rpc提供了很多服务治理，他注重于消费者、提供者自主的去向注册机注册服务，而REST风格的方式借助于nginx做负载均衡，还需要自己手动的去配置路由。
消费者需要哪种服务，不需要关注哪台服务器去给他提供了服务。





