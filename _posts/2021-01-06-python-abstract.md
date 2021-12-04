---
toc: true
layout: post
description: section6
categories: [python]
title: Python Abstract
---

Python Coding dojang

---

## 6.1. Abstract Class

```
from abc import *

class MyBookBase(metaclass=ABCMeta):
    
    @abstractmethod
    def display(self):
        pass

    @abstractmethod
    def get_amount(self):
        print("How many books sold?")

class MyBook(MyBookBase):
    cnt = 0
    def __init__(self, title, author, price):
        MyBook.cnt += 1
        self.title = title
        self.author = author
        self.price = price

    def display(self):
        print("Title: {}".format(self.title))
        print("Author: {}".format(self.author))
        print("Price: {}".format(self.price))

    def get_amount(self):
        super().get_amount()
        print(self.cnt)

new_novel1=MyBook('Human acts','Hangang',12000)
new_novel1.display()
> Title: Human acts
  Author: Hangang
  Price: 12000
new_novel1.get_amount()
> How many books sold?
> 1
new_novel2=MyBook('Vegitarian','Hangang',14000)
new_novel2.display()
> Title: Vegitarian
  Author: Hangang
  Price: 14000
new_novel2.get_amount()
> How many books sold?
> 2
```
