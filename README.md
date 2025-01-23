# codepratice-day9
代码随想录第9天

栈的基础知识。今天的内容也是补的。
括号匹配题需要背下来。 
有效的括号这题是旷视面试遇到过的。

[用栈实现队列](https://leetcode.cn/problems/implement-queue-using-stacks/description/)
用栈实现队列，即用两个栈，一个作为输入，一个作为输出，输出空的时候，导入输入栈的数据。
pop时，先将数据导入stout，再将stout的顶部元素弹出，就是队列头。

```CPP
class MyQueue {
public:
    stack<int> stIn;
    stack<int> stOut;

    MyQueue() {
        
    }
    
    void push(int x) {
        stIn.push(x);    
    }
    
    int pop() {
        //当stout为空的时候，再从stIn中导入
        if(stOut.empty())
        {
            //从stin导入数据直到stin为空
            while(!stIn.empty())
            {
                stOut.push(stIn.top());
                stIn.pop();
            }
        }
        int result=stOut.top();
        stOut.pop();
        return result;
    }
    
    //get the front element
    int peek() {
        int res=this->pop();//直接使用已有的pop函数
        stOut.push(res);//因为pop已经弹出了res,所以还需要加回去
        return res;
    }
    
    bool empty() {
        return stIn.empty()&&stOut.empty();
    }
};

/**
 * Your MyQueue object will be instantiated and called as such:
 * MyQueue* obj = new MyQueue();
 * obj->push(x);
 * int param_2 = obj->pop();
 * int param_3 = obj->peek();
 * bool param_4 = obj->empty();
 */

```

[用队列实现栈](https://leetcode.cn/problems/implement-stack-using-queues/submissions/)
1.使用两个队列，在pop和top时，少弹出一个元素，这个元素就是栈顶元素。将que2的元素变成que1的，然后再赋值回去，就完成弹出栈顶元素
2.也可以用一个队列实现。将模拟栈弹出时，将队列头元素（除最后一个元素）重新加入队尾，再弹出元素就是栈的顺序。

两个队列实现的方式：
```CPP
class MyStack {
public:
    queue<int> que1;
    queue<int> que2;
    MyStack() {
        
    }
    
    void push(int x) {
        que1.push(x);
    }
    
    int pop() {
        int size=que1.size();
        size--;
        
        //将que1导入que2,但留下最后一个元素，是栈顶元素
        while(size--)
        {
            que2.push(que1.front());
            que1.pop();
        }
        int result=que1.front();
        que1.pop();
        que1=que2;
        while(!que2.empty())
        {
            que2.pop();
        }
        return result;
    }
    
    int top() {
        int size=que1.size();
        size--;
        while(size--)
        {
            que2.push(que1.front());
            que1.pop();
        }    
        
        int result=que1.front();
        que2.push(que1.front());
        que1.pop();

        que1=que2;
        while(!que2.empty())
        {
            que2.pop();
        }
        return result;
    }
    
    bool empty() {
        return que1.empty();
    }
};

/**
 * Your MyStack object will be instantiated and called as such:
 * MyStack* obj = new MyStack();
 * obj->push(x);
 * int param_2 = obj->pop();
 * int param_3 = obj->top();
 * bool param_4 = obj->empty();
 */
```

一个队列实现的方式：
```CPP
class MyStack {
public:
    queue<int> que;

    MyStack() {
        
    }
    
    void push(int x) {
        que.push(x);    
    }
    
    int pop() {
        int size=que.size();
        size--;
        while(size--)//将队列头部的元素（除了最后一个）重新添加到队列尾部
        {
            que.push(que.front());
            que.pop();
        }
        int result=que.front();//此时队列头是原本的最后一个元素，弹出就是栈的顺序
        que.pop();
        return result;
    }
    
    int top() {
        int size=que.size();
        size--;
        while(size--)
        {
            //将队列头部的元素（除了最后一个)重新加到队列尾部
            que.push(que.front());
            que.pop();
        }        
        int result=que.front();
        que.push(que.front());//加入一个元素，因为top()不需要移除元素，只是返回一个元素，队列尾部（作为栈的弹出
        que.pop();
        return result;
    }
    
    bool empty() {
        return que.empty();
    }
};

/**
 * Your MyStack object will be instantiated and called as such:
 * MyStack* obj = new MyStack();
 * obj->push(x);
 * int param_2 = obj->pop();
 * int param_3 = obj->top();
 * bool param_4 = obj->empty();
 */
```

[用栈实现队列](https://leetcode.cn/problems/implement-queue-using-stacks/description/)
在写代码之前分析好有哪几种不匹配的情况。
1.字符串中左方向的括号多余
2.括号没有多余，但是括号的类型没有匹配上
3.字符串中右方向的括号多余

```CPP
class Solution {
public:
    bool isValid(string s) {
        if(s.size()%2!=0) return false;//长度为奇数的话，一定不符合
        stack<char> st;
        for(int i=0;i<s.size();++i)
        {
            if(s[i]=='(') st.push(')');
            else if(s[i]=='{') st.push('}');
            else if(s[i]=='[') st.push(']');
            else if(st.empty()||st.top()!=s[i]) return false;
            else st.pop();//在st.top()已经等于s[i]，弹出一个匹配元素
        }
        return st.empty();//已经遍历完字符串，但是栈不为空，说明不是正确匹配的括号。否则就刚好匹配成功。
    }
};

```
多看多练

[删除字符串中的所有相邻重复项](https://leetcode.cn/problems/remove-all-adjacent-duplicates-in-string/description/)
重复删除两个相邻且相同的字母。
匹配问题->栈

将字符串中字符，如果遍历的与栈顶不同，就压入，如果相同就弹出这个元素，最后将栈的元素给到答案，反转后为答案。
也可以拿字符串直接作为栈。
```CPP
class Solution {
public:
    string removeDuplicates(string S) {
        stack<char> st;
        for(char s:S)
        {
            if(st.empty()||s!=st.top())
            {
                st.push(s);
            }
            else
            {
                st.pop();
            }
        }
        string result="";
        while(!st.empty())
        {
            result+=st.top();
            st.pop();
        }
        reverse(result.begin(), result.end());
        return result;
    }
};
```

```CPP
class Solution {
public:
    string removeDuplicates(string s) {
        string result;
        for(char x:s)
        {
            if(result.empty()||result.back()!=x)
            {
                result.push_back(x);
            }
            else
            {
                result.pop_back();
            }
        }
        return result;
    }
};

```
递归的实现就是：每一次递归调用都会把函数的局部变量、参数值和返回地址等压入调用栈中。
