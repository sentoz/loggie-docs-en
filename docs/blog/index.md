
<style>
  .md-sidebar--secondary:not([hidden]) {
    visibility: hidden;
  }
</style>

# Blog

## Quickly Build a Scalable Cloud-Native Logging Architecture with Loggie

<aside style="font-size: .7rem" markdown>
<span>:octicons-person-16: [__ethfoo__] &nbsp;</span>
<span>
:octicons-calendar-24: 2022-10-26 &nbsp;
:octicons-clock-24: 10 min read 
</span>
</aside>

Abstract：

- Introduction and philosophy of Loggie
- Cases of using Loggie to collect logs in Kubernetes
- Using Loggie to build log collection architectures of different scales
  
  [:octicons-arrow-right-24: __Continue reading__](https://github.com/loggie-io/asserts/blob/main/ppt/%E4%BD%BF%E7%94%A8Loggie%E5%BF%AB%E9%80%9F%E6%9E%84%E5%BB%BA%E5%8F%AF%E6%89%A9%E5%B1%95%E6%97%A5%E5%BF%97%E7%B3%BB%E7%BB%9F%E6%9E%B6%E6%9E%84.pdf)
  [__ethfoo__]: https://github.com/ethfoo
  [使用Loggie快速构建可扩展的云原生日志架构]: (https://github.com/loggie-io/asserts/blob/main/ppt/%E4%BD%BF%E7%94%A8Loggie%E5%BF%AB%E9%80%9F%E6%9E%84%E5%BB%BA%E5%8F%AF%E6%89%A9%E5%B1%95%E6%97%A5%E5%BF%97%E7%B3%BB%E7%BB%9F%E6%9E%B6%E6%9E%84.pdf)

---

## Practice of building NetEase Shufan cloud-native log platform

<aside style="font-size: .7rem" markdown>
<span>:octicons-person-16: [__ethfoo__] &nbsp;</span>
<span>
:octicons-calendar-24: 2022-03-11 &nbsp;
:octicons-clock-24: 8 min read 
</span>
</aside>

NetEase has been exploring and practicing cloud native applications since 2015. As an important part of observability, the log collection platform has also undergone the evolution from traditional host to container, in order to support the large-scale cloud native transformation of various business departments within enterprise group.
This article will describe the problems we encountered, solutions we used for evolution and transformation, and experiences and best practices we have precipitated in this process.

Abstract：

- Operator-based log collection
- Dilemmas and challenges in large-scale scenarios
- The present and future of open-sourced Loggie
  
  [:octicons-arrow-right-24: __Continue reading__](https://mp.weixin.qq.com/s/rtnBplSAdOzQmtYAtXrAqA)
  [__ethfoo__]: https://github.com/ethfoo
  [网易数帆云原生日志平台架构实践]: (https://mp.weixin.qq.com/s/rtnBplSAdOzQmtYAtXrAqA)

