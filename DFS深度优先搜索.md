## DFS深度优先搜索

#### 框架

```
解决一个回溯问题，实际上就是一个决策树的遍历过程。你只需要思考 3 个问题：
1、路径：也就是已经做出的选择。
2、选择列表：也就是你当前可以做的选择。
3、结束条件：也就是到达决策树底层，无法再做选择的条件。
```

```
result = []
def backtrack(路径, 选择列表):
    if 满足结束条件:
        result.add(路径)
        return
    
    for 选择 in 选择列表:
    	# 做选择
    	将该选择从选择列表移除
    	路径.add(选择)
    	backtrack(路径, 选择列表)
    	# 撤销选择
    	路径.remove(选择)
    	将该选择再加入选择列表
```

#### 1.全排列

```
给定一个 没有重复 数字的序列，返回其所有可能的全排列。
示例:
输入: [1,2,3]
输出:
[
  [1,2,3],
  [1,3,2],
  [2,1,3],
  [2,3,1],
  [3,1,2],
  [3,2,1]
```

<img src="D:\学习\java\leetcode\pic\dfs\1.png" style="zoom:75%;" />

```java
	List<List<Integer>> permute(int[] nums) {
		LinkedList<Integer> track = new LinkedList<>();
		List<List<Integer>> res = new LinkedList<>();
        int[] visited = new int[nums.length];
		backtrack(nums, track, res, visited);
		return res;
	}

	void backtrack(int[] nums, LinkedList<Integer> track, List<List<Integer>> res, int[] visited) {
		if (track.size() == nums.length) {
			res.add(new LinkedList<>(track));
			return;
		}

		for (int i = 0; i < nums.length; i++) {
			if (visited[i]==1)
				continue;
			track.add(nums[i]);
            visited[i] = 1;
			backtrack(nums, track, res, visited);
			track.removeLast();
            visited[i] = 0;
		}
	}
```

#### 2.[全排列 II](https://leetcode-cn.com/problems/permutations-ii/)

```
给定一个可包含重复数字的序列，返回所有不重复的全排列。
示例:
输入: [1,1,2]
输出:
[
  [1,1,2],
  [1,2,1],
  [2,1,1]
]
```

关键：

1.排序！！

2.剪枝去重

<img src="D:\学习\java\leetcode\pic\dfs\2.png" style="zoom:75%;" />

```java
	List<List<Integer>> permute(int[] nums) {
		LinkedList<Integer> track = new LinkedList<>();
		List<List<Integer>> res = new LinkedList<>();
        int[] visited = new int[nums.length];
        Arrays.sort(nums);
		backtrack(nums, track, res, visited);
		return res;
	}

	void backtrack(int[] nums, LinkedList<Integer> track, List<List<Integer>> res, int[] visited) {
		if (track.size() == nums.length) {
			res.add(new LinkedList<>(track));
			return;
		}

		for (int i = 0; i < nums.length; i++) {
			if (visited[i]==1)
				continue;
			track.add(nums[i]);
            visited[i] = 1;
			backtrack(nums, track, res, visited);
			track.removeLast();
            visited[i] = 0;
		}
	}
```

#### 3.[N皇后](https://leetcode-cn.com/problems/n-queens/)

```
n 皇后问题研究的是如何将 n 个皇后放置在 n×n 的棋盘上，并且使皇后彼此之间不能相互攻击。
给定一个整数 n，返回所有不同的 n 皇后问题的解决方案。
每一种解法包含一个明确的 n 皇后问题的棋子放置方案，该方案中 'Q' 和 '.' 分别代表了皇后和空位。
示例:
输入: 4
输出: [
 [".Q..",  // 解法 1
  "...Q",
  "Q...",
  "..Q."],

 ["..Q.",  // 解法 2
  "Q...",
  "...Q",
  ".Q.."]
]
解释: 4 皇后问题存在两个不同的解法。
```

解法一：判断(i,j)是否可以放置，直接简单的遍历第j列是否放置，左上对角线和右上对角线是否放置

```java
	List<List<String>> res=new ArrayList<>();
    public List<List<String>> solveNQueens(int n) {
        //棋盘,默认为0表示空，1表示皇后
        int[][] board=new int[n][n];
        //row当前填写得的行号
        dfs(n,0,board);
        return res;
    }

    //深度优先遍历
    private void dfs(int n, int row, int[][] board) {
        //0~n-1都填写完毕
        if (row==n){
            res.add(track(board,n));
            return;
        }
        for (int col = 0; col < n; col++) {
            if (isUsable(board,row,col)){
                board[row][col]=1;
                //填写下一行
                dfs(n,row+1,board);
                board[row][col]=0;
            }
        }
    }

    //board[row][col]是否可用
    private boolean isUsable(int[][] board, int row, int col) {
        //检查列上有无皇后
        for (int i = 0; i < row; i++) {
            if (board[i][col]==1) return false;
        }
        //检查左上至右下对角线有无皇后
        for (int i = col-1; i >= 0; i--) {
            if (i+row-col<0) break;
            if (board[i+row-col][i]==1) return false;
        }
        //检查右上至左下对角线有无皇后
        for (int i = col+1; i < board.length; i++) {
            if (row+col-i<0) break;
            if (board[row+col-i][i]==1) return false;
        }
        return true;
    }

    //将int类型棋盘转换成输出格式
    private List<String> track(int[][] board, int n) {
        List<String> list=new ArrayList<>();
        for (int i = 0; i < n; i++) {
            StringBuilder temp=new StringBuilder();
            for (int j = 0; j < n; j++) {
                if (board[i][j]==0)temp.append('.');
                else temp.append('Q');
            }
            list.add(temp.toString());
        }
        return list;
    }
```

解法二：撇是副对角线，row+col相等，捺是副对角线，row-col相等（-n+1~n-1），主副对角线的个数都为2*n-1。

```java
	private List<List<String>> ans = new ArrayList<>();

    public List<List<String>> solveNQueens(int n) {
        char[][] curr = new char[n][n];
        boolean[] cols = new boolean[n];
        boolean[] pie = new boolean[2 * n - 1];
        boolean[] na = new boolean[2 * n - 1];
        for (int i = 0; i < n; i ++) {
            for (int j = 0; j < n; j ++) {
                curr[i][j] = '.';
            }
        }
        dfs(n, curr, 0, cols, pie, na);
        return ans;
    }

    public void dfs(int n, char[][] curr, int row, boolean[] cols, boolean[] pie, boolean[] na) {
        if (row == n) {
            List<String> state = new ArrayList<>();
            for (int i = 0; i < n; i ++) {
                state.add(String.valueOf(curr[i]));
            }
            ans.add(state);
            return;
        }

        for (int col = 0; col < n; col ++) {
            if (curr[row][col] == 'Q' || cols[col] || pie[row + col] || na[row - col + n - 1])
                continue;
            curr[row][col] = 'Q';
            cols[col] = true;
            pie[row + col] = true;
            na[row - col + n - 1] = true;
            dfs(n, curr, row + 1, cols, pie, na);
            curr[row][col] = '.';
            cols[col] = false;
            pie[row + col] = false;
            na[row - col + n - 1] = false;
        }
    }
```

#### 4.[组合](https://leetcode-cn.com/problems/combinations/)

```
给定两个整数 n 和 k，返回 1 ... n 中所有可能的 k 个数的组合。
示例:
输入: n = 4, k = 2
输出:
[
  [2,4],
  [3,4],
  [2,3],
  [1,2],
  [1,3],
  [1,4],
]
```

关键：剪枝！！！绿色是需要剪枝的部分

![](D:\学习\java\leetcode\pic\dfs\3.png)

```java
	List<List<Integer>> res = new ArrayList<>();
    public List<List<Integer>> combine(int n, int k) {
        if(n < 1 || k < 1 || n < k) return res;
        List<Integer> path = new ArrayList<>();
        dfs(n, k, 1, path);
        return res;
    }

    public void dfs(int n, int k, int start, List<Integer> path){
        if(path.size() == k){
            res.add(new ArrayList<>(path));
            return;
        }
        for(int i = start; i <= n - (k - path.size()) + 1; i++){
            path.add(i);
            dfs(n, k, i + 1, path);
            path.remove(path.size() - 1);
        }
    }
```

#### 5.[复原IP地址](https://leetcode-cn.com/problems/restore-ip-addresses/)

```
给定一个只包含数字的字符串，复原它并返回所有可能的 IP 地址格式。
有效的 IP 地址正好由四个整数（每个整数位于 0 到 255 之间组成），整数之间用 '.' 分隔。
示例:
输入: "25525511135"
输出: ["255.255.11.135", "255.255.111.35"]
```

思路：

约束条件，剪枝：

1. 子ip数为四个，ip地址的长度在4和12之间。
2. 0<=subIp<=255且不能是“0x”,"0xx"的形式。
3. 每个子树最多三个分支，因为子ip长度最大为3嘛。
4. 做出选择后，要保证剩下的字符串也满足要求哦。

<img src="D:\学习\java\leetcode\pic\dfs\4.png" style="zoom:67%;" />

```java
	public List<String> restoreIpAddresses(String s) {
        int len = s.length();
        List<String> res = new ArrayList<String>();
        if(s==null || len<4 || len > 12) return res;
        Deque<String> path = new ArrayDeque<>();
        dfs(s,0,path,res);
        return res;
    }

    public boolean isValid(String sub,int count,int rest){
        int subIP =  Integer.valueOf(sub);
        if(sub.charAt(0)=='0'&&sub.length()>1) return false;
        if(subIP>=0&&subIP<=255&&(4-count)<=rest&&rest<=(4-count)*3) return true;
        else return false;
    }

    public void dfs(String s,int start, Deque<String> path,List<String> res){
        if(path.size()==4 && start==s.length()){
            res.add(String.join(".", path));
            return;
        }
        for(int i = start+1; i <= start+3 && i <= s.length() ; i++){
            String sub = s.substring(start,i);
            if(isValid(sub,path.size()+1,s.length()-i)){
                //做选择
                path.addLast(sub);
                dfs(s,i,path,res);
                //撤销选择
                path.removeLast();
            }
        }
    }
```

**补充知识：java中的Stack已被淘汰，用双端队列模拟栈。**

#### 6.[子集 II](https://leetcode-cn.com/problems/subsets-ii/)

```java
给定一个可能包含重复元素的整数数组 nums，返回该数组所有可能的子集（幂集）。
说明：解集不能包含重复的子集。
示例:
输入: [1,2,2]
输出:
[
  [2],
  [1],
  [1,2,2],
  [2,2],
  [1,2],
  []
]
```

```java
	//注意res的声明，new LinkedList<>()，<>里面不要写类型！！！！！！！
	List<List<Integer>> res = new LinkedList<>();
    public List<List<Integer>> subsetsWithDup(int[] nums) {
        if(nums==null || nums.length==0) return res;
        //一定要sort
        Arrays.sort(nums);
        LinkedList<Integer> path = new LinkedList<Integer>();
        dfs(nums,0,path);
        return res;
    }

    public void dfs(int[] nums, int start, LinkedList<Integer> path){
        res.add(new LinkedList<>(path));
        for(int i=start;i<nums.length;i++){
            if(i>start&&nums[i]==nums[i-1]) continue;
            //做选择
            path.addLast(nums[i]);
            dfs(nums,i+1,path);
            path.removeLast();
        }
    }
```

