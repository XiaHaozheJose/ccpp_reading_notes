[TOC]



## 1. 直接可参考资料

https://www.zsythink.net/archives/2862



## 1. 常用 过滤器

```yaml
tasks:
  - debug:
      #将字符串转换成纯大写
      msg: "{{ testvar | upper }}"
  - debug:
      #将字符串转换成纯小写
      msg: "{{ testvar | lower }}"
  - debug:
      #将字符串变成首字母大写,之后所有字母纯小写
      msg: "{{ testvar | capitalize }}"
  - debug:
      #将字符串反转
      msg: "{{ testvar | reverse }}"
  - debug:
      #返回字符串的第一个字符
      msg: "{{ testvar | first }}"
  - debug:
      #返回字符串的最后一个字符
      msg: "{{ testvar | last }}"
  - debug:
      #将字符串开头和结尾的空格去除
      msg: "{{ testvar1 | trim }}"
  - debug:
      #将字符串放在中间，并且设置字符串的长度为30，字符串两边用空格补齐30位长
      msg: "{{ testvar1 | center(width=30) }}"
  - debug:
      #返回字符串长度,length与count等效,可以写为count
      msg: "{{ testvar2 | length }}"
  - debug:
      #将字符串转换成列表，每个字符作为一个元素
      msg: "{{ testvar3 | list }}"
  - debug:
      #将字符串转换成列表，每个字符作为一个元素，并且随机打乱顺序
      #shuffle的字面意思为洗牌
      msg: "{{ testvar3 | shuffle }}"
  - debug:
      #将字符串转换成列表，每个字符作为一个元素，并且随机打乱顺序
      #在随机打乱顺序时，将ansible_date_time.epoch的值设置为随机种子
      #也可以使用其他值作为随机种子，ansible_date_time.epoch是facts信息
      msg: "{{ testvar3 | shuffle(seed=(ansible_date_time.epoch)) }}"
```