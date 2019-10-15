---
title: 简单实用的python CLICK
date: 2019/8/29 21:37
tags: 
---

click是一个将python脚本转为命令行使用的包.
类似于 Argparse  tf.FLAGS  


使用非常简单, import之后, 使用```@click.command()```以装饰器的形式附在想要使用的函数上
然后使用```@click.option()```添加配置信息, 最常用的就是```@click.option('--count', default=1, help='Number of greetings.')```这种形式

```python
import click

@click.command()
@click.option('--count', default=1, help='Number of greetings.')
@click.option('--name', prompt='Your name',
              help='The person to greet.')
def hello(count, name):
    """Simple program that greets NAME for a total of COUNT times."""
    for x in range(count):
        click.echo('Hello %s!' % name)

if __name__ == '__main__':
    hello()
```
