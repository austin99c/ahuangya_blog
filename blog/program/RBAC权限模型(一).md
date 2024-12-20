---
slug: RBAC-MODEL-ONE
title: RBAC权限模型(一)
date: 2024-12-11
authors: loner
tags: [java,rbac]
keywords: [java,rbac]
image: https://gitee.com/huang_bai_hui/article_img/raw/master/202412112345479.webp
sticky: 10
---

<!-- truncate -->
近日有一个设计RBAC鉴权的需求，所以做需求之前想深入了解一下什么RBAC模型。

## 一、什么是RBAC模型？

RBAC(Role-Based Access Control)：基于角色的访问控制，它是一种权限管理模型。核心思想是通过**角色**来分配和管理用户的权限，而不是直接将权限分配给用户。

当使用RBAC时，通过分析系统用户的实际情况，基于共同的职责和需求，授予他们不同角色。可以授予给用户一个或多个角色，每个角色具有一个或多个权限，这种**用户-角色**、**角色-权限**之间的关系，让我们可以不用单独管理单个用户，用户从授予的角色里面集成所需的权限。

## 二、RBAC的主要优势

1. 简化管理：通过角色而不是用户来管理权限，减少了管理复杂度。当用户的职责发生变化时，只需要更改用户的角色，而不需要重新分配权限。
2. 细粒度控制：可以根据角色定义非常细粒度的访问控制策略。
3. 可扩展性：易于扩展和维护，因为新用户只需要分配到现有的角色，新权限只需添加到相应的角色中。
4. 最小权限原则：用户只能执行分配给其他角色的操作，符合最小权限原则。
5. RBAC更加灵活高效：与**直接给用户分配权限(ACL)**相比，RBAC更加灵活高效。因为它允许权限根据角色进行集中管理，从而减少了权限的复杂性和重复性。如果**直接给用户分配权限(ACL)**，没有角色这层关系，扩展性就弱了许多，但这种方式仅适合用户数量、角色类型少的平台。

## 三、RBAC模型分类

RBAC的模型分为：RBAC0、RBAC1、RBAC2、RBAC3四种。

### 3.1 RBAC0

RBAC0是其他3个模型的基础。大部分系统基于RBAC0就可以满足设计需求。

比如我们开发中最为熟悉的**GitLab**，每个账号都会被赋予一个角色，通常为：开发者(Developer)、维护者(Maintainer)、所有者(Owner)。

比如所有者角色拥有创建仓库、删除仓库、部署项目等所有权限；维护者角色拥有同意合并请求、推送受保护的分支等部分权限；开发者角色则拥有提交代码的权限。

![202412112349956](https://austin-blog.oss-cn-beijing.aliyuncs.com/blog_image/202412112349956.png)

### 3.2 RBAC1

RBAC1模型对于RBAC0来讲，引入了子角色，角色可以被继承，即子角色可以继承父角色的所有权限。角色之间存在上下级关系，如：运营经理---> 运营主管 ---> 运营组长 ---> 运营人员 ---> 运营实习生，上级拥有下级的所有权限，也可以额外拥有其他权限。通过给角色分级，高级别的角色可继承低级别角色的权限，一定程序上简化了权限管理工作。

### 3.3 RBAC2

- 基数约束：限制一个角色可以分配给多少个用户，或一个用户可以拥有多少个角色。
- 先决条件角色：用户想要成为某一个角色，必须先成为这个角色的下一级角色。如：想要拥有运营经理角色，必须要拥有运营主管的角色。

> 静态职责分离(Static Constraints) SSD 

- 静态角色互斥：互斥角色是指权限互相制约的两个角色。比如：一个用户不能同时被指派出纳员和审计员两个角色。

> 动态职责分离(Dynamic Constraints) DSD

- 动态角色互斥：允许每个用户具有多个角色，但是在运行时只能激活其中的某些角色，比如BOSS直聘中，在登录时，会询问使用招聘者还是应聘者，同时只能选择一种身份进行操作。

### 3.4 RBAC3

RBAC3是RBAC模型的进一步扩展，它结合了RBAC1和RBAC2的特性，既引入了角色间的继承关系，又引入了角色限制关系。

- 继承自RBAC2，角色可以有层次结构，允许角色继承其他角色的权限，简化了权限管理。
- 角色约束
  - 静态约束：包括静态分离职责(SSD)和静态角色互斥，限制在角色分配阶段的角色组合。
  - 动态约束：包括动态分离职责(DSD)和动态角色互斥，确保在会话期间角色激活的约束。
- 角色激活限制：在RBAC3中，用户可能被分配了多个角色，但在某个会话中只能激活其中的一部分，遵循角色互斥和分离职责的约束。
- 审计和监控：RBAC3强调对角色分配、角色激活、权限使用等进行详细的审计和监控，以确保合规性和安全性。



## 四、什么是权限？

权限是资源的集合，在系统中权限具体表现在：**页面权限**、**操作权限**和**数据权限**。

- 页面权限：如**超级管理员**会具备系统管理模块的所有页面权限，如：用户列表、角色列表、菜单列表等页面权限。
- 操作权限：系统中一些操作按钮，比如新增用户、冻结用户等按钮，这些按钮根据配置的权限判断是否展示。
- 数据权限：不同的用户看到的数据是不同的，通过数据权限控制列表数据的展示。



第一章先初步了解，后续几个章节再详细补充一下内容。如：RBAC数据库表设计、功能模块设计、数据权限如何做等内容。