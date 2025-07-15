##  **if**
```
x=int(input("Enter a number: "))
if x > 0:
    print("Positive")       
    print(len(str(x)))
#elif是elssif语句的缩写
elif x < 0:
    print("Negative")
    print(len(str(abs(x))))
else:
    print("Zero")
    print(len(str(x)))
```
## **len(s)**
返回字符串长度
## **for**
```
word=['cat', 'dog', 'fish', 'bird', 'lizard', 'hamster', 'rabbit', 'turtle']
for animal in word:
    print(animal,len(animal))
```
for迭代多项集的场景
```
copy()): 创建字典的一个浅拷贝（副本），这样你可以在遍历副本时安全地修改原字典（比如删除元素），不会影响遍历过程。
item(): 返回字典中所有键值对的视图，可以用 for user, status in ... 这样的方式遍历。

.copy().items() 遍历字典的所有键值对，但遍历的是副本，不会因为删除原字典中的元素而报错。
```

```
# 创建示例多项集
users = {'Hans': 'active', 'Éléonore': 'inactive', '景太郎': 'active'}

# 策略：迭代一个副本
for user, status in users.copy().items():
    if status == 'inactive':
        del users[user]
print(users)

# 策略：创建一个新多项集
active_users = {}
for user, status in users.items():
    if status == 'active':
        active_users[user] = status
print(active_users)
```


