class Solution {
public:
    string convert(string s, int numRows) {
        if(numRows<2 || s.size()<=numRows) return s;
        string res(s.size(),0);
        char *p=&res[0];
        for(int i=0, j,k, step=(numRows-1)*2;i<numRows;++i) {
            if(i==0 || i==numRows-1) 
                for(j=i;j<s.size();j+=step) *p++=s[j];
            else
                for(j=i, k=i<<1;j<s.size();k=step-k,j+=k) *p++=s[j];
        }
        return res;
    }
};