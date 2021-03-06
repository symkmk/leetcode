## 107_(二叉树的层次遍历 II)Binary Tree Level Order Traversal II
## 1 问题描述、输入输出与样例
### 1.1 问题描述
给定一个二叉树，返回其节点值自底向上的层次遍历。 （即按从叶子节点所在层到根节点所在的层，逐层从左向右遍历）。
### 1.2 输入与输出
输入：
* TreeNode* root：二叉树的根节点指针

输出：
* vector<vector<int>>：自底向上的层次遍历的结果，是一个二维矢量的形式
### 1.3 样例
#### 1.3.1 样例1
输入: 二叉树: [3,9,20,null,null,15,7]
	
        3
	   / \
	  9  20
	    /  \
	   15   7

输出:

	[
	  [15,7],
	  [9,20],
	  [3]
	]

## 2 思路描述与代码    
### 2.1 思路描述（队列BFS+堆栈）
主要利用思路就是在用队列做BFS的同时将数据记录到堆栈中，然后再利用堆栈实现自底向上的层次遍历。重点在于:
1. 在入队列的时候右节点优先进队，便于堆栈处理数据
2. 记录堆栈中需要记录当前层的所有节点数据与节点个数

比如输入[3,9,20,null,null,15,7]<br>
首先队列BFS并记录到堆栈中，其过程主要如下：<br>
* 将根节点3入队;<br>
* 队列不空，当前层节点数据有1个，数据是3，入队的元素有[20, 9]，并记录当前层数据3和节点个数1到堆栈中，此时 q = [20, 9], s = [3, 1];<br>
* 队列不空，当前层节点数据有2个，数据是[20, 9]，入队的元素有[7, 15]，并记录当前层数据[20, 9]和节点个数2到堆栈中，此时 q = [7, 15], s = [3, 1, 20, 9, 2];<br>
* 队列不空，当前层节点数据有2个，数据是[7, 15]，无入队元素，并记录当前层数据[7, 15]和节点个数2到堆栈中，此时 q 为空, s = [3, 1, 20, 9, 2, 7, 15, 2];<br>
* 队列空，开始从堆栈中处理遍历的数据;<br>

等到队列空后，开始处理堆栈里面的数据，其过程主要如下：<br>
* 堆栈不空，获取栈顶元素2并出栈，2为当前层的节点数据，然后取出堆栈中2个元素[15, 7],此时s = [3, 1, 20, 9, 2], 遍历结果为[[15, 7]];<br>
* 堆栈不空，获取栈顶元素2并出栈，2为当前层的节点数据，然后取出堆栈中2个元素[9, 20],此时s = [3, 1], 遍历结果为[[15, 7],[9, 20]];<br>
* 堆栈不空，获取栈顶元素1并出栈，1为当前层的节点数据，然后取出堆栈中1个元素[2],此时堆栈为空，遍历结果为[[15, 7],[9, 20], [3]];<br>
* 堆栈为空，返回遍历结果[[15, 7],[9, 20], [3]];
### 2.2 代码
```cpp
vector<vector<int>> levelOrderBottom(TreeNode* root) {
    vector<vector<int>> res;
    stack<int> s;
    //如果输入为空
    if (root == NULL) {
        return res;
    }
    //1. 队列做BFS,右子树优先进队
    queue<TreeNode*> q;
    q.push(root);
    while( !q.empty() ){
        //获取当前层的数目
        int len = q.size();
        vector<int> level;
        TreeNode* tmp;
        for( int i = 0; i < len; i++ ){
            //取一节点放入堆栈中
            tmp = q.front();
            q.pop();
            s.push(tmp->val);
            //当前层所有的节点如果有孩子节点则压入队列
            //右子树优先入队
            if(tmp->right){
                q.push(tmp->right);
            }
            if(tmp->left){
                q.push(tmp->left);
            }
            }
            //记录节点的个数
            s.push(len);
        }
        
        //2. 遍历堆栈记录数据
        while( !s.empty() ){
            vector<int> level_traverse;
            int len = s.top();
            s.pop();
            for( int i = 0; i < len; i++ ){
                level_traverse.push_back(s.top());
                s.pop();
            }
            res.push_back(level_traverse);
        } 
        return res;
    }
```
## 3 思考与拓展
### 3.1 思考
本题的做法很多，我觉得基于队列+堆栈的做法的时间复杂度是最优的。
#### 3.1.1 其他方法 
#### 3.1.1.1 队列BFS+反转
1. 首先按照[102 二叉树的层次遍历](https://leetcode-cn.com/problems/binary-tree-level-order-traversal/)获取遍历结果res；
2. 反转 res；<br>

值得注意的是反转需要花费一定的时间。
#### 3.1.2 复杂度分析
方法|空间复杂度|时间复杂度
--- | --- | ---
队列BFS+堆栈|O(2^h),h是二叉树的高度|O(n)
队列BFS+反转|O(2^h),h是二叉树的高度|O(n),个人觉得这种方法时间上稍弱于上者
#### 3.1.3 难点分析
1. 基于队列BFS+堆栈的方法对入队顺序有要求
2. 基于队列BFS+堆栈的方法需要记录当前层的节点数据到堆栈中
### 3.2 拓展
如果让你做二叉树自底向上的锯齿形层次遍历呢？
