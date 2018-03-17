#  CDA #
I abbreviate C++ Data structures Algorithms to CDA.

This project includes the Data Structures, Algorithms example coding such as list, stack, queue，quick sort and more....

By coding on the Leetcode judgement, I start thinking about what is the meaning of programming, what is time complexity and how to estimate it.

Keep it up. Go bears!!!


Summarizing how to solve problem is very important. Because the note will help you understand the problem you have solved. It is a pity that I realize it now.

Ok,let's beginning.


### [Number of Matching Subsequences]  ###
#### 792-问题描述: ####
Given string S and a dictionary of words words, find the number of words[i] that is a subsequence of S.

Example :
Input: 
S = "abcde"
words = ["a", "bb", "acd", "ace"]
Output: 3
Explanation: There are three words in words that are a subsequence of S: "a", "acd", "ace".
Note:

All words in words and S will only consists of lowercase letters.
The length of S will be in the range of [1, 50000].
The length of words will be in the range of [1, 5000].
The length of words[i] will be in the range of [1, 50].

#### 思路总结 ####

从给出的子字符串中计算出匹配于S的个数。题目中要求的是subsequences而不是substring,所以子字符串的字符不必连续。并且既然是subsequences,那么子字符串的字符也必然是按照顺序来的，在S中前后顺序必然也是子字符串的前后顺序。

题目中S的长度在1-50000，子字符串的个数也在1-5000，每个字符串的字符数在1-50个。

那么第一种思路来了：
 依次对每子个字符串的所有字符进行遍历，从S中查找是否有相同的字符，查找到的那个字符就不能再匹配子字符串的其他字符了。这种思路就是暴力破解法。O(S.length*words.length+sum(words[i].length)),复杂度在25000万以上，以现代计算机的运算速度来看，运算次数控制在百万和千万以下，大多程序都可以在几秒内处理完毕。Online Judgement系统(leetcode)给出的最大运算时间是2s,如果超过2s,系统就会报出Time Limit Exceeded(超时)的错误.第一种方法明显超时。

根据前面的思路进行改进，如果每一次比对子字符串都需要遍历一遍S，那么我可以不可以想另外一种思路，整个过程都只比对一次S呢？第二种思路如下：

从S开始遍历，如果子字符串的第一个字符和当前比对S的字符相等，那么就将子字符串的第一个字符擦去，S的字符往后移一个，接下来就按照前面的这种方式比对，直到S的最后一个字符，最后再查找words里面有多少个空字符串，就是有多少个子序列匹配于S。

这种思路的代码简单易读，而且复杂度变为O(S.length+sum(words[i].length))。



	class Solution {
	public:
	    int numMatchingSubseq(string S, vector<string>& words) {
	        int num=0;
	        for(int i=0;i<S.size();i++){
	           for(int j=0;j<words.size();j++)
	           if(S[i]==words[j][0]) {
	             words[j].erase(0,1); 
	             if(words[j].size()==0) num++;
	           };
	       }
	        return num;
	   }
	};       
	        

words[j].erase(0,1)就是去掉words[j]的第一个字符，0表示开始的下标,1表示去掉的个数。

这道题总共有46个测试用例，在执行第37个测试用例的时候给挂了，S的长度是4万左右，我们这种方法是每个字符串都遍历了一遍，并且S遍历时候也是一个字符一个字符比对，那么还有什么方法可以再简单点呢？
 
接下来就是第三种思路了，字符串是由26个字母构成的吧，那么我们用vector<vector<int>>类型的容器保存S中出现的字符的下标，运用二分查找判断字符串中的字符是否在容器中出现。
	
	class Solution {
	public:
	    int numMatchingSubseq(string S, vector<string>& words) {
	        int num=0;
	        vector<vector<int>> alpha(26);
	        for(int i=0;i<S.size();i++)
	            alpha[S[i]-'a'].push_back(i);
	        for(int j=0;j<words.size();j++)
	        {
	            int x=-1;
	            bool result=true;
	            for(char c:words[j])
	            {
	                auto it=upper_bound(alpha[c-'a'].begin(),alpha[c-'a'].end(),x);
	                if(it==alpha[c-'a'].end()) result=false;
	                else x=*it;
	            }
	            if(result) num++;
	        }
	        return num;    
	    }
	};


auto是c++11中的新特性，定义为自动推断变量的类型。
upper_bound()是二分查找函数，返回第一个比x大的值。
lower_bound()返回的是一个不小于x的距离x最短值。

这个问题就这样解决了，更优质的思路还待续......




## Stack ##

###[Valid Parentheses]###
#### 20- 问题描述####
Given a string containing just the characters '(', ')', '{', '}', '[' and ']', determine if the input string is valid.

The brackets must close in the correct order, "()" and "()[]{}" are all valid but "(]" and "([)]" are not.

#### 思路总结 ####
判断字符串是不是有效的括号。将字符串的字符依次放入栈中，如果字符和栈顶字符恰好是一对括号，则可以让栈的栈顶元素出栈，否则将该字符入栈。当字符串的所有字符判断完毕，再去检查栈中是否还留有元素。如果还有，则字符串不是有效的括号。反之，如果栈为空，则字符串是有效的括号。

栈（Stack）是一个容器适配器（Container adaptor）类型，被特别设计用来运行于LIFO（Last-in first-out）场景，在该场景中，只能从容器末尾添加（Insert）或提取（Extract）元素。stack 提供了一系列成员函数用于操作它的元素，只能从容器“后面”压进（Push）元素或从容器“后面”提取（Pop）元素。容器中的“后面”位置也被称为“栈顶”。
用来实现栈的底层容器必须满足顺序容器的所有必要条件。满足上述条件的标准容器有 std::vector、std::deque 及 std::list，如果未特别指定 stack 的底层容器，标准容器 std::deque 将被使用。

用stack实现的代码：

	#include<stack>
	class Solution {
	public:
	    bool isValid(string s) {
	        if(s.length()==0) return false;
	        stack<char> stack1;
	        for(int i=0;i<s.length();i++)
	        {
	            if(i>0){
	                if(!stack1.empty() && stack1.top()=='[' && s[i]==']') stack1.pop();
	                else if(!stack1.empty() && stack1.top()=='(' && s[i]==')') stack1.pop();
	                else if(!stack1.empty() && stack1.top()=='{' && s[i]=='}') stack1.pop();
	                else stack1.push(s[i]);
	            }
	            else stack1.push(s[i]);
	        }
	        if(stack1.size()!=0) return false;
	        return true;
	    }
	};


这里有个地方需要注意：如果没有加上stack1.empty()的判断条件，那么程序将会报出Runtime Error（运行错误）。原因是用"[]{}()"这个用例测试的时候，当i=2时，栈为空，那么stack.top()这句代码将会被编译器优化，实际上调用的是deque的back(),而调用这句的时候，指针需要向下移一个位置，这样stack.top()就成了未定义的行为，由此报错。


然而用vector实现的时候，则不必考虑是否为空的情况，实现代码如下：

	class Solution {
	public:
	    bool isValid(string s) {
	        if(s.length()==0) return false;
	        vector<char> stack1;
	        for(int i=0;i<s.length();i++)
	        {
	            if(i>0){
	                if(stack1.back()=='[' && s[i]==']') stack1.pop_back();
	                else if(stack1.back()=='(' && s[i]==')') stack1.pop_back();
	                else if(stack1.back()=='{' && s[i]=='}') stack1.pop_back();
	                else stack1.push_back(s[i]);
	            }
	            else
	            stack1.push_back(s[i]);
	        }
	        if(stack1.size()!=0) return false;
	        return true;
	    }
	};

毕竟vector也是用来实现栈的底层容器......