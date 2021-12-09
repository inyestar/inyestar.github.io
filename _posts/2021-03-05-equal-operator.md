---
layout: post
title: Equal Operator
tags: [Javascript]
---

### Double Equals (Loose Equality)
- 피연산자의 타입이 다를 경우 피연산자의 타입을 변환한다.
- 타입이 같으면 === 연산자를 사용한다.

### Process
1. 피연산자가 같은 타입인지 확인하고 같으면 === 비교를 호출한다.
2. null과 undefined를 비교하고 있는지 확인하고 그럴경우 true를 리턴한다.
3. string과 number를 비교하고 있는지 확인하고 그럴경우 \
string 값을 number[[ToNumber]]로 변환한다.\
변환된 피연산자들로 == 비교를 호출하여 첫 번째 단계부터 다시 진행한다.

    > 입력 값에 따른 [[ToNumber]] 결과
    > - undefined → NaN
    > - null → 0
    > - number → number
    > - boolean → 0 혹은 1
    > - string → number, NaN, 0

4. boolean과 다른 타입을 비교하고 있는지 확인하고 그럴경우 boolean을 number로 변환한다.\
  변환된 피연산자들로 첫 번째 단계부터 다시 진행한다.

    > undefined, null, 0, "", NaN은 [[ToBoolean]] 후 false로 변환되고 이 외에 나머지는 true로 변환된다.

5. object를 number, string, symbol과 비교하고 있는지 확인하고 그럴경우 \
object를 primitive[[ToPrimitive]]로 변환한다.\
마찬가지로 변환된 피연산자들로 == 비교를 호출하여 처음부터 다시 시작한다.

    - [[ToPrimitive]]의 preferredType이 number(default)일 경우,  
    1. valueOf() 함수를 호출하여 리턴값이 primitive일 경우 그대로 리턴
    2. 그렇지 않으면 toString() 함수를 호출하여 마찬가지로 primitive 이면 리턴
    3. 그렇지 않으면 TypeError 던짐

    - ToPrimitive의 preferredType이 string일 경우, 위 1번과 2번 단계가 바뀐다.
    1. toString() 함수를 호출하여 리턴값이 primitive일 경우 그대로 리턴
    2. valueOf() 함수를 호출하여 리턴값이 primitive일 경우 그대로 리턴
    3. 그렇지 않으면 TypeError 던짐

    > [] = [] 비교 시 피연산자는 모두 object이므로 ===를 호출한다. 각 피연산자는 다른 주소를 저장하고 있기 때문에 false를 리턴한다.

6. 위 과정에 모두 부합하지 않을 경우 false를 리턴한다.

### Example
#### Case 1) [] == 0
1. 타입이 다른가? 다르므로 다음 단계로 간다.
2. null과 undefined를 비교하고 있는가? 아니므로 다음 단계로 간다.
3. number과 string을 비교하고 있는가? 아니므로 다음 단계로 간다.
4. boolean과 다른 타입을 비교하고 있는가? 아니므로 다음 단계로 간다.
5. object를 number/string/symbol과 비교하고 있는가?
  맞으므로 array를 primitive로 변환하여 나온 값으로 == 비교를 다시 시작한다.
    {% highlight js %}
    [].valueOf()
    // []
    [].toString()
    // ""
    {% endhighlight %}
6. "" == 0으로 비교를 다시 시작한다.
7. 1번과 2번을 거친 후 3번에 해당하므로 string을 number로 변환한다.\
빈 문자열은 0으로 변환되므로 0 == 0 으로 비교를 다시 시작한다.
8. 1번으로 돌아가 피연산자의 타입이 같으므로 0 === 0을 호출한다.

### References
- [https://www.codementor.io/javascript/tutorial/double-equals-and-coercion-in-javascript](https://www.codementor.io/javascript/tutorial/double-equals-and-coercion-in-javascript)
