---
title: 基于springboot实现邮件发送
date: 2017-07-28  
tags: [mail, springboot]
---

我相信大家项目中难免会遇到发送邮件的业务。下面我就以重置密码，并将随机生成的新密码发送至邮箱的功能为例做一个简要代码小记，以便今后的学习参考。

#### 代码实现
application.properties
```bash
# mail
spring.mail.host=smtp.126.com
spring.mail.username=XXX@126.com
spring.mail.password=XXX
spring.mail.properties.mail.smtp.auth=true
```
<!--more-->

ForgetPwdDTO.java
```bash
/**
 * 忘记密码DTO
 * Created by ynh on 2017/1/22.
 */
@Data
@EqualsAndHashCode(callSuper = false)
public class ForgetPwdDTO {

	/**
	 * 邮箱地址
	 */
	private String email;
}
```

Result.java

```bash
import lombok.Data;
import lombok.ToString;

import java.io.*;

/**
 * 接口返回结果基类
 *
 * Created by ynh on 2017/1/22.
 */
@Data
@ToString(callSuper = true)
public class Result<T extends Serializable> implements Serializable{

	/**
	 * 错误信息
	 */
	private Error error;

	/**
	 * 描述
	 */
	private String desc;

	/**
	 * 业务处理返回实际对象
	 */
	private T response;

	public Result() {
	}

	public Result(T response) {
		this.response = response;
	}

}
```

发送邮件方法

```bash

    @Value("${spring.mail.username}")
    private String from;
    
    /**
     * 忘记密码
     *
     * @param dto 请求参数类
     * @return 失败信息
     */
    public Result forgetPwd(ForgetPwdDTO dto) {

        if (StringUtils.isEmpty(dto.getEmail())) {
            return assembleFailResult(Error.SERVICE_ERROE, "找回密码邮箱为空");
        }

        SysUser user = sysUserMapper.findByEmail(dto.getEmail());

        if (user == null) {

            return assembleFailResult(Error.SERVICE_ERROE, "用户不存在");
        }

        //产生6位随机数
        Integer newPwd = (int) ((Math.random() * 9 + 1) * 100000);

        //数据库密码重置
        user.setPassword(Md5Util.changeToMd5(newPwd.toString()));
        sysUserMapper.updateByPrimaryKeySelective(user);

        //邮件通知
        SimpleMailMessage message = new SimpleMailMessage();
        message.setFrom(from);//发送者.
        message.setTo(dto.getEmail());//接收者.
        message.setSubject("测评系统[重置密码]");//邮件主题.
        message.setText("您好，您的新密码为" + newPwd + ",为了保障您的账户安全，请尽快修改密码！");//邮件内容.
        mailSender.send(message);//发送邮件
        return assembleSuccessResult(null);
    }
```

### 邮箱中的配置
由于主机不支持mail函数，网站要使用邮件发送功能就需要调用外部的smtp，同时邮箱自身也要开启smtp/pop功能才可以正常调用。
以网易的126邮箱为例：
<img src="http://oo8ieb5e5.bkt.clouddn.com/image/vps/stmp.png" />
将图中的POP3/SMTP服务开启即可

