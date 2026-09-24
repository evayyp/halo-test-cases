# 基于 MeterSphere 的综合实验报告

## 1. MeterSphere 安装与登录

### 1.1 下载与安装

在 VMware 中安装 Ubuntu 虚拟机，将 MeterSphere 离线安装包上传至服务器，执行安装脚本：

cd /home/z/下载/metersphere-ce-offline-installer-v3.6.8-lts
/bin/bash install.sh

安装完成后，通过 msctl status 查看服务状态。

![安装截图1](images/install01.png)

![安装截图2](images/install02.png)

### 1.2 登录及问题

安装成功后，浏览器访问 http://192.168.58.128:8081，使用默认账号登录：

- 用户名：admin
- 密码：metersphere

![登录页面](images/login.png)

**遇到的问题：** 部署后页面提示"网络异常"，接口调试返回 404。

**解决方法：** 调整虚拟机网络模式为 NAT，并配置 DNS 为 8.8.8.8，重启 Docker 和 MeterSphere 服务后恢复正常。

![网络异常](images/network-error.png)

---

## 2. MeterSphere 功能测试流程

### 2.1 创建功能测试用例（脑图）

进入【测试跟踪】→【功能用例】，以脑图方式创建登录功能测试用例。

![功能用例脑图](images/functional-mindmap.png)

测试用例列表：

| 用例名称 | 前置条件 | 测试步骤 | 预期结果 |
|----------|----------|----------|----------|
| 验证正确用户名密码能登录成功 | 已注册账号 testuser/123456 | 1.打开登录页 2.输入用户名密码 3.点击登录 | 登录成功，跳转首页 |
| 验证用户名为空时登录失败 | 已注册账号 | 1.打开登录页 2.用户名留空 3.点击登录 | 提示"用户名不能为空" |
| 验证密码错误时登录失败 | 已注册账号 | 1.输入正确用户名 2.输入错误密码 3.点击登录 | 提示"用户名或密码错误" |
| 验证密码输入超长字符串 | 已注册账号 | 1.输入200位密码 2.点击登录 | 提示密码长度超限 |
| 验证连续多次登录失败后账号锁定 | 已注册账号 | 1.连续5次错误登录 2.第6次正确登录 | 提示账号已锁定 |

### 2.2 创建测试用例评审

进入【用例评审】，创建评审任务，关联上述5条用例，评审结果标记为"通过"。

![用例评审](images/review.png)

### 2.3 创建测试计划

进入【测试计划】，创建"登录功能测试计划"，关联5条功能用例。

![测试计划](images/test-plan.png)

### 2.4 缺陷创建

执行测试计划时，发现"密码错误时提示信息不明确"，创建缺陷并关联测试用例。

![缺陷创建](images/bug.png)

### 2.5 测试报告查看

执行完成后查看测试报告，通过率 100%，缺陷统计正常。

![测试报告](images/report.png)

---

## 3. MeterSphere 接口测试实验

### 3.1 登录接口

接口地址：https://demo.halocms.site/apis/api.console.halo.run/v1alpha1/posts

请求方式：GET

认证方式：Basic Auth

- 用户名：demo
- 密码：P@ssw0rd123..

![登录接口](images/api-login.png)

### 3.2 接口用例创建及调试

#### 3.2.1 查看文章列表接口

- 请求方式：GET
- 请求地址：/apis/api.console.halo.run/v1alpha1/posts
- 请求头：Authorization: Basic ZGVtbzpQQHNzdzByZDEyMy4u

![查看文章列表](images/api-post-list.png)

#### 3.2.2 查看分类列表接口

- 请求方式：GET
- 请求地址：/apis/content.halo.run/v1alpha1/categories

![查看分类列表](images/api-category01.png)
![查看分类列表](images/api-category02.png)

#### 3.2.3 查看单篇文章信息接口

- 请求方式：GET
- 请求地址：/apis/content.halo.run/v1alpha1/posts/{name}

![查看单篇文章](images/api-post-detail01.png)
![查看单篇文章](images/api-post-detail02.png)

#### 3.2.4 查看标签列表接口

- 请求方式：GET
- 请求地址：/apis/content.halo.run/v1alpha1/tags

![查看标签列表](images/api-tag.png)

#### 3.2.5 删除回收站文章接口

- 请求方式：DELETE
- 请求地址：/apis/content.halo.run/v1alpha1/posts/{name}

![删除回收站文章](images/api-delete.png)

#### 3.2.6 查看文章内容接口

- 请求方式：GET
- 请求地址：/apis/api.console.halo.run/v1alpha1/posts/{name}/release-content

![查看文章内容](images/api-post-content.png)

#### 3.2.7 发布一篇文章接口

- 请求方式：POST
- 请求地址：/apis/api.console.halo.run/v1alpha1/posts

![发布文章](images/api-publish.png)

---

## 4. 个人总结

本次 MeterSphere 综合实验，我完成了接口测试和功能测试两个核心模块。

实验过程中遇到了不少问题：MeterSphere 部署后网络不通，接口调试一直返回 404，后来通过调整虚拟机网络模式和配置 DNS 解决；Halo 接口的 Basic 认证一开始手动拼接 Authorization 请求头总是失败，后来改用 MeterSphere 内置的 Basic Auth 配置才成功。

接口测试部分，我调试了获取文章列表、查看文章内容、发布文章等多个接口。最大的收获是理解了 {name} 是路径参数变量，需要用真实存在的文章 ID 替换，而不是直接使用花括号语法。

这次实验让我对接口测试、参数提取、场景串联有了直观深入的理解，也锻炼了独立排查问题的能力。

---

## 参考文献

- Halo 文档：https://docs.halo.run/
- 接口文档：https://gitee.com/fit2cloud-edu/MeterSphere/blob/main/附件_1_Halo_接口文档_V2.2.md
