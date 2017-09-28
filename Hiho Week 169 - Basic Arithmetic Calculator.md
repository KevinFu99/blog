# Hiho Week 169 \- Basic Arithmetic Calculator

就是手写一个支持优先级的计算器

用到了栈(因为我们需要经常提取最后几项) 每次压入运算符的时候 把前面所有优先级高于或者等于当前运算符的都算完

代码如下:

```c++
#include <cstdio>
#include <cstdlib>
#include <cstring>
#include <cmath>
#include <algorithm>

template <typename T>
struct Stack{
    const static int MAXSIZE = 105;
    T data[MAXSIZE]; int head;

    Stack(){
        head=0;
    }
    inline bool hasElement() const{return head;}
    inline T top() const{return data[head-1];}
    inline void push(const T &v){data[head++]=v;}
    inline T pop(){return data[--head];}
};
Stack<int> num;
Stack<char> op;

int prior[300]; //priority of operator
char str[105]; int pos; //offset

inline void doOperation(){
    int rhs = num.pop();
    int &lhs = num.data[num.head-1];
    switch (op.pop()) {
        case '+':
            lhs+=rhs;
            break;
        case '-':
            lhs-=rhs;
            break;
        case '*':
            lhs*=rhs;
            break;
        case '/':
            lhs/=rhs;
            break;
    }
}

inline bool read(){ //0 for operator, 1 for number
    bool ret = isdigit(str[pos]);
    if(ret){
        int ans=str[pos]-'0';
        while(isdigit(str[++pos])) ans=ans*10+str[pos]-'0';
        num.push(ans);
    }else op.push(str[pos++]);
    return ret;
}

int main(){
    int len;
    pos=0;

    //initialize priority
    prior['+']=prior['-']=1;
    prior['*']=prior['/']=2;
    prior['(']=prior[')']=-1; //avoid being calculated

    scanf("%s",str);
    len=strlen(str);

    while(pos<len){
        if(!read()){ //reads an operator
            if(prior[op.top()]>0){ //not "()"
                char tmp=op.pop();
                while(op.hasElement() && prior[op.top()] >= prior[tmp]) doOperation();
                op.push(tmp);
            }else if(op.top() == ')'){
                op.pop(); //pop )
                while(op.top()!='(') doOperation();
                op.pop(); //pop (
            } //do nothing for (
        }//do nothing for numbers
    }

    while(op.hasElement()) doOperation();
    printf("%d\n",num.top());

    return 0;
}
```

2017年9月28日22:16