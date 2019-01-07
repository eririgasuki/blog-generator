---
title: 实现一个jQuery的API
date: 2019-01-07 20:52:44
tags:
---

```
window.jQuery = function (nodeOrSelector) { //接受一个节点或者CSS选择器来找到目标
    let nodes = {} //初始化一个空的nodes对象，来存放获取到的目标对象
    if (typeof nodeOrSelector === 'string') {  //如果接受到的参数是CSS选择器
        let temp = document.querySelectorAll(nodeOrSelector) //通过DOM的querySelectorAll方法来找到目标对象存放到temp中
        for (let i = 0; i < temp.length; i++) { //遍历这个CSS选择器找到的所有目标
            nodes[i] = temp[i] // 将获取的伪数组的每个值一一存放到nodes对象中
        }
        nodes.length = temp.length //将获取到的伪数组的长度值赋给nodes对象的长度
    } else if (nodeOrSelector instanceof Node) { //如果接受到的参数是一个节点
        nodes = { //直接将改节点存放到nodes对象中
            0: nodeOrSelector, //有且仅有一个的值即节点本身
            length: 1 //对象只存了一个节点故长度为1
        }
    }
    nodes.addClass = function (classes) { //设置一个添加类名的函数addClass，可接受多个类名
        classes.forEach(value => { //多个类名通过数组存放，通过数组的forEach方法遍历类名数组的每一个值。
            for (let i = 0; i < nodes.length; i++) { //遍历获取到的每个节点
                nodes[i].classList.add(value) //将类名数组的每一个值都添加到目标节点的classList中
            }
        })
    }
    nodes.Text = function (text) { //设置一个获取文本或添加文本的函数Text
        if (text === undefined) { //如果用户没有提供参数，默认为获取目标节点中的文本
            var texts = [] //初始化一个空的数组对象，来存放文本
            for (let i = 0; i < node.length; i++) { //遍历获取到的每个节点
                texts.push(node[i].textContent) //通过DOM的textContent方法来获取节点中的文本，并通过数组的push方法将文本存到texts数组中
            }
            return texts //将存放完文本的texts数组返回给用户
        } else { //如果用户提供了参数，默认为将参数设置为目标节点中的文本
            for (let i = 0; i < nodes.length; i++) { //遍历获取到的每个节点
                nodes[i].textContent = text //通过DOM的textContent方法将用户提供的参数设置为节点中的文本。
            }
        }
    }
    return nodes //返回获取到的节点，提供给客户使用。
}
```
