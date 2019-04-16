[TOC]



## 1. playbook 是一个 yaml 文件

- playbook 编写时，必须遵守 **yaml** 语法规则
- 比如 `xxx: ` 中的 `:` 后面，**必须** 要有 **空格**


## 2. hosts 根节点

### 1. 指定【被控机】

```yaml
- hosts: 主机1, 主机2, 主机3
```

### 2. 指定【被控机】所属【组】

```yaml
- hosts: 组名
```

```yaml
- hosts: 组1, 组2
```


## 3. remote_user 子节点

```yaml
- hosts: 组1, 组2
  remote_user: 登录被控机的用户名
```


## 4. tasks 子节点

指定要执行的【ansible 模块】语句

```yaml
- hosts: 组1, 组2
  remote_user: 登录被控机的用户名
  tasks: 
    - name: 要执行的【ansible 模块】语句的【描述信息】
      ansible 模块名: 
        模块所需参数1: 参数值
        模块所需参数2: 参数值
```


## 5. 一个 playbook 中，可包含多个【hosts 根节点】

```yaml
- hosts: 主机
  remote_user: 登录被控机的用户名
  tasks: 
    - name: 要执行的【ansible 模块】语句的【描述信息】
      ansible 模块名: 
        模块所需参数1: 参数值
        模块所需参数2: 参数值

- hosts: 组1
  remote_user: 登录被控机的用户名
  tasks: 
    - name: 要执行的【ansible 模块】语句的【描述信息】
      ansible 模块名: 
        模块所需参数1: 参数值
        模块所需参数2: 参数值

- hosts: 组2
  remote_user: 登录被控机的用户名
  tasks: 
    - name: 要执行的【ansible 模块】语句的【描述信息】
      ansible 模块名: 
        模块所需参数1: 参数值
        模块所需参数2: 参数值

- hosts: 组1, 组2
  remote_user: 登录被控机的用户名
  tasks: 
    - name: 要执行的【ansible 模块】语句的【描述信息】
      ansible 模块名: 
        模块所需参数1: 参数值
        模块所需参数2: 参数值
```


## 6. 检查 playbook【语法】是否正确

```
ansible-playbook --syntax-check [playbook yaml 文件路径]
```


## 7. 检查 playbook【运行】是否正确

```
ansible-playbook --check [playbook yaml 文件路径]
```

注意: 并不能完全模拟真实的运行效果，只是一个大概估计。

