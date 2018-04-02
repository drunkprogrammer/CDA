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
#### 20-问题描述####
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


##Linked List##
###[Remove Nth Node From End of List]###
####19-问题描述####
Given a linked list, remove the nth node from the end of list and return its head.

For example,

   Given linked list: 1->2->3->4->5, and n = 2.

   After removing the second node from the end, the linked list becomes 1->2->3->5.
Note:
Given n will always be valid.
Try to do this in one pass.

####思路总结####
将从链表末尾数的第N个的数据移除。其实这就是对链表某个指定元素的操作问题。
官方定义的每个节点的结构体如下（Definition for singly-linked list）：

      struct ListNode {
	      int val;
	      ListNode *next;
          ListNode(int x) : val(x), next(NULL) {}
	  };
	 
 ListNode(int x) : val(x), next(NULL) {}  这个是初始化它的值和它的下一个指针的函数。

指向结构体的指针： 

                 struct ListNode *p;
                 struct ListNod l1;
                 p=&l1;//p指向l1
 
指向结构体某个值的方式：

                p->val;//第一种
				(*p).val;//第二种

解决代码：

	/**
	 * Definition for singly-linked list.
	 * struct ListNode {
	 *     int val;
	 *     ListNode *next;
	 *     ListNode(int x) : val(x), next(NULL) {}
	 * };
	 */
	class Solution
	{
	public:
	    ListNode* removeNthFromEnd(ListNode* head, int n)
	    {
	        ListNode *begin=head, **temp=&head;
	        for(int i=1;i<n;i++) begin=begin->next;
	        while(begin->next!=NULL)
	        {
	            temp=&((*temp)->next);
	            begin=begin->next;
	        }
	        *temp=(*temp)->next;
	        return head;
	    }
	};


##Map##
###[Subdomain Visit Count]###
####811-问题描述####
A website domain like "discuss.leetcode.com" consists of various subdomains. At the top level, we have "com", at the next level, we have "leetcode.com", and at the lowest level, "discuss.leetcode.com". When we visit a domain like "discuss.leetcode.com", we will also visit the parent domains "leetcode.com" and "com" implicitly.

Now, call a "count-paired domain" to be a count (representing the number of visits this domain received), followed by a space, followed by the address. An example of a count-paired domain might be "9001 discuss.leetcode.com".

We are given a list cpdomains of count-paired domains. We would like a list of count-paired domains, (in the same format as the input, and in any order), that explicitly counts the number of visits to each subdomain.

#####Example 1:#####

Input: 
["9001 discuss.leetcode.com"]

Output: 
["9001 discuss.leetcode.com", "9001 leetcode.com", "9001 com"]

Explanation: 
We only have one website domain: "discuss.leetcode.com". As discussed above, the subdomain "leetcode.com" and "com" will also be visited. So they will all be visited 9001 times.

#####Example 2:#####

Input: 
["900 google.mail.com", "50 yahoo.com", "1 intel.mail.com", "5 wiki.org"]

Output: 
["901 mail.com","50 yahoo.com","900 google.mail.com","5 wiki.org","5 org","1 intel.mail.com","951 com"]

Explanation: 
We will visit "google.mail.com" 900 times, "yahoo.com" 50 times, "intel.mail.com" once and "wiki.org" 5 times. For the subdomains, we will visit "mail.com" 900 + 1 = 901 times, "com" 900 + 50 + 1 = 951 times, and "org" 5 times.

#####Notes:#####

The length of cpdomains will not exceed 100. 

The length of each domain name will not exceed 100.

Each address will have either 1 or 2 "." characters.

The input count in any count-paired domain will not exceed 10000.

####思路总结####
计算各个子域名出现的次数.通过题目中给的测试用例，我们可以知道：

"900 google.mail.com" 表示 google.mail.com被访问了900次，mail.com也同样被访问了900次，com也被访问了500次。

如果有相同被访问的域名，则把次数相加。

所以我们可以用map来保存被访问的域名和被访问的次数，map<key,value>。map属于关联性容器，它和multimap的区别在于，map中的键(key)不能重复，可以用索引和at方法查找值，但是multimap中的键可以重复。

我们需要先将字符串中的数字提取出来，可以先用find方法，找到空格，再截取字符串，最后用atoi方法将字符串转换成int型。但是还有一种更简单的方法，直接计算，可以看看代码实现部分。之后我们将域名的子域名也存入map,如果遇到相同的，则加上之前的值。

####代码实现####
	#include<list>
	#include<algorithm>
	#include<string>
	class Solution {
	public:
	    vector<string> subdomainVisits(vector<string>& cpdomains) {
	        map<string,int> domains;
	        string subdomain;
	        for(auto domain:cpdomains)
	        {
	            int num=0;
	            for(int i=0;i<domain.size();i++)
	            {
	                if(domain[i]==' ')
	                {
	                    subdomain=domain.substr(i+1);
	                    break;
	                }
	                else
	                {
	                    num=num*10+(domain[i]-'0');//字符串中的数字
	                }
	            }
	            domains[subdomain]+=num;
	            for(int j=0;j<subdomain.length();j++)
	            {
	                if(subdomain[j]=='.')
	                domains[subdomain.substr(j+1)]+=num;
	            }
	        }
	        vector<string> subdomains;
	        for(auto subdomain:domains)
	        subdomains.push_back(to_string(subdomain.second)+" "+subdomain.first); 
	        return subdomains;
	    } 
	};
