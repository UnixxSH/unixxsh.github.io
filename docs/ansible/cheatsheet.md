---
layout: default
title: Cheatsheet
parent: Ansible
nav_order: 1
---
# Cheatsheet

___

## Dynamic variables
### example1
{% raw %}
```
'{{ lookup('vars', 'somevar_' ~ other_var) }}'
```
### example2
```
'{{ regex_search('/.+?(?=/' ~ sid ~ ')') }}'
```
{% endraw %}

___

## Get file names from find module
{% raw %}
```
- ansible.builtin.find:
    paths: "/my/path"
  register: my_list

- ansible.builtin.set_fact:
    myvar: "{{ my_list['files'] | map(attribute='path') | map('basename') | list }}"
```
{% endraw %}

___

## Block jinja2 trim
At beginning of template
```
#jinja2: trim_blocks:False
```
```
#jinja2: lstrip_blocks: "True"
```

___

## Template in K8S manifest
```
kind: ConfigMap
apiVersion: v1
metadata:
  name: myname
data:
  sync.yaml: |
    {% for l in lookup('template', 'mytemplate.j2').split('\n') %}
    {{ l }}
    {% endfor %}
```

___

## Force first line indent in jinja2 template
```
{% raw %}
{% filter indent(width=2, first=True) %}
code
{% endfilter %}
{% endraw %}
```
