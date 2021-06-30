---

title: "[Python] 객체복사,shallow copy,deep copy"
excerpt: "shallow copy와 deep copy 차이점 알아보기"

categories: [Basic]

tag: [python, shallow copy, deep copy, 객체복사, copy]

toc: true

toc_sticky: true

date: 2021-03-28

last_modified_at: 2021-03-28

---

# [Python] 객체복사,shallow copy,deep copy

a라는 변수가 있을때

b라는 변수에 a에있는 내용을 복사해두고

b를 변경해서 쓰다가 원본 a와 비교하고 싶었다...



그래서 b =a 담고 했지만 b를 변경했더니 a도 같이 변경해버리는 문제가 발생했다!

문제의 원인과 해결방법은 아래에서





## 1. 객체 복사

****

위에서 말했던 케이스이다.

b = a라는 변수를 담았기에 a가 바라보는 객체를 b도 동일하게 바라본다.

그러므로 둘중에 하나를 변환시켜도 동일하게 변환된다.



```
a = [1,2,3,[4,5,6]]
b= a   # 객체 복제
print(b) # [1,2,3,[4,5,6]]
b[1] = 100  # b의 1번자리를 3으로 변경
print(b) # [1, 100, 3, [4, 5, 6]]
print(a) # [1, 100, 3, [4, 5, 6]] 
b[3].append(7)
print(b) # [1, 100, 3, [4, 5, 6, 7]]
print(a) # [1, 100, 3, [4, 5, 6, 7]]
```





단순 객체복제는 같은 객체의 주소를 가리킨다.

<img src="https://github.com/cano721/cano721.github.io/blob/master/_posts/md-images/image-1616918346544.png?raw=true">

## 2. shallow Copy(얕은 복사)

****

```
import copy
a = [1,2,3,[4,5,6]]
b= copy.copy(a)
print(b) # [1, 2, 3, [4, 5, 6]]
b[1] = 100  # b의 1번자리를 3으로 변경
print(b) # [1, 100, 3, [4, 5, 6]]
print(a) # [1, 2, 3, [4, 5, 6]]
b[3].append(7)
print(b) # [1, 100, 3, [4, 5, 6, 7]]
print(a) # [1, 2, 3, [4, 5, 6, 7]]
```



shallow copy는 새로운 객체(변수)를 만들고 원본에 값을 복제하는 것이 아닌

원본을 참조하는 형식이다.



그렇기에 불변의 요소는 가져오고 가변의 요소는 같은 주소를 가리킨다.

<img src="https://github.com/cano721/cano721.github.io/blob/master/_posts/md-images/image%20(1).png?raw=true">

## 3. deep Copy(얕은 복사)

****
```
import copy
a = [1,2,3,[4,5,6]]
b= copy.deepcopy(a)
print(b) # [1, 2, 3, [4, 5, 6]]
b[1] = 100  # b의 1번자리를 3으로 변경
print(b) # [1, 100, 3, [4, 5, 6]]
print(a) # [1, 2, 3, [4, 5, 6]]
b[3].append(7)
print(b) # [1, 100, 3, [4, 5, 6, 7]]
print(a) # [1, 2, 3, [4, 5, 6]]
```



deep copy는 새로운 객체(변수)를 만들고 내용도 새로 생성하여 담는다.

그로인해 b는 a와 무관한 객체가 된다.

<img src="https://github.com/cano721/cano721.github.io/blob/master/_posts/md-images/image%20(2).png?raw=true">