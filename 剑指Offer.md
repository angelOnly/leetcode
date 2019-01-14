###  3.二维数组中的查找

##### 题目

在二维数组中，每一行都按照从左到右递增的顺序排序，每一列都按照从上到下递增的顺序排序，判断二维数组中是否有该整数。

##### 思路

查找整数时会出现重复查找问题，列从最右开始往左边检索，行从上往下边开始检索，当列中的值比目标整数大，则该列中就不存在比目标整数相等的值，列往左移动，当行中的值比目标整数小，行往下移动

java中判断数组为空，要检查三个部分:

一是数组首地址是否为空

二是是否为{}，也就是array.length==0的情况

三是{{}}，这时array.length == 1，但是array[0].length == 0。满足任意一个条件就可以返回false了。

if(array == null || array.length == 0 || (array.length <= 1 && array[0].length == 0))  return false;

````java
//时间复杂度 O(n^2)
public static boolean find(int[][] arr, int target) {
		if (arr == null || arr.length < 1 && arr[0].length < 1) {
			return false;
		}
		int row = 0;
		int col = arr.length - 1;
		while (row >= 0 && row < arr.length - 1 && col >= 0) {
			if (arr[row][col] == target) {
				return true;
			} else if (arr[row][col] > target) {//如果当前的数字比target大，则表示不在该列，则表示在该列的左边
				col--;//列数递减，表示往左移
			} else {//如果当前的数字比target小，则表示不在该行，则表示在该行的下边
				row++;//行数递增，表示往下移
			}
		}
		return false;
}
//二分 时间复杂度 O(nlogn)
public static boolean find2(int[][] arr, int target) {
		if (arr == null || arr.length < 1 && arr[0].length < 1) {
			return false;
		}
		int i = 0;
		while (i < arr.length) {
			if (arr[i][arr[i].length - 1] < target) {
				i++;
			} else {
				int b = 0;
				int t = arr[i].length - 1;
				while (b <= t) {
					int mid = (b + t) / 2;
					if (arr[i][mid] < target) {
						b = mid + 1;
					} else if (arr[i][mid] > target) {
						t = mid - 1;
					} else {
						return true;
					}
				}
				i++;
			}
		}
		return false;
}

public static void main(String[] args) {
		int[][] matrix = { { 1, 2, 8, 9 }, 
                           { 2, 4, 9, 12 }, 
                           { 4, 7, 10, 13 }, 
                           { 6, 8, 11, 15 } };
		System.out.println(find(matrix, 7)); // 要查找的数在数组中
		System.out.println(find(matrix, 5)); // 要查找的数不在数组中
		System.out.println(find(matrix, 1)); // 要查找的数是数组中最小的数字
		System.out.println(find(matrix, 15)); // 要查找的数是数组中最大的数字
		System.out.println(find(matrix, 0)); // 要查找的数比数组中最小的数字还小
		System.out.println(find(matrix, 16)); // 要查找的数比数组中最大的数字还大
		System.out.println(find(null, 16)); // 健壮性测试，输入空指针
	}
````

### 4.替换空格

##### 题目

请实现一个函数，把字符串中的空格替换成"%20"，输入”we are happy.“，输出”we%20are%20happy.“

##### 思路

遍历一次字符串，计算出其空格的个数，可以计算出替换之后的字符串总长度，每替换一个空格，长度就增加2，因此替换后的长度等于原来的长度加上字符串空格的个数乘以2。替换的时候从后往前开始复制和替换，准备两个指针，p1和p2，p1指向原始字符串的末尾，p2指向替换后的字符串末尾。接下来移动p1，逐个把字符复制到p2，直到碰到第一个空为止，此时p1往前移动一格，p2往前插入 "%20"，由于 %20长度是3，因此p2也要往前移动3格。重复以上步骤就可以把空格完全替换。

时间复杂度 O(n)

````JAVA
public static int replaceBlank(char[] chars, int usedLength) {
		if (chars == null || chars.length < usedLength) {
			return -1;
		}
		int blankCount = 0;
		for (int i = 0; i < chars.length; i++) {
			if (chars[i] == ' ') {
				blankCount++;
			}
		}
		if (blankCount == 0) {
			return usedLength;
		}
		int targetLength = blankCount * 2 + usedLength;//计算出替换后的长度
  		//如果转换后的长度大于数组最大长度，返回-1
		if (targetLength > chars.length) {
			return -1;
		}
		int temp = targetLength;
		usedLength--;//从后往前，第一个开始处理的字符
		targetLength--;//替换后的字符放置的位置
  		//字符中有空白字符，一直处理到所有空白字符处理完
		while (targetLength >= 0 && usedLength < targetLength) {
			if (chars[usedLength] == ' ') {
				chars[targetLength--] = '0';
				chars[targetLength--] = '2';
				chars[targetLength--] = '%';
			} else {
				chars[targetLength--] = chars[usedLength];
			}
			usedLength--;
		}
		return temp;
	}

public static void main(String[] args) {
		char[] string = new char[20];
		string[0] = 'w';
		string[1] = 'e';
		string[2] = ' ';
		string[3] = 'a';
		string[4] = 'r';
		string[5] = 'e';
		string[6] = ' ';
		string[7] = 'h';
		string[8] = 'a';
		string[9] = 'p';
		string[10] = 'p';
		string[11] = 'y';
		string[12] = '.';
		int length = replaceBlank(string, 13);//传递的长度是已经使用的长度，而数组的长度要比已使用的长度长，并且超过替换后的长度
		System.out.println(new String(string, 0, length));
	}
````

### 5.单链表

插入：将要插入的节点指向头节点，并将指针往后移动，直到后续节点为null，将新节点插入

删除：只需要将节点往后移即可

````java
public static void main(String[] args) {
		ListNode node = new ListNode(2);
		addNode(node,4);
		addNode(node,5);
		addNode(node,6);
		removeNode(node, 2);
		removeNode(node, 5);
		printNode2(node);
	}
	
	public static Stack<Integer> stack = new Stack<>();
	public static LinkedList<Integer> list = new LinkedList<>();
	//从头到尾打印链表
	public static void printNode2(ListNode head) {
		ListNode cur = head;
		while (cur != null) {
			list.add(cur.val);
			cur = cur.node_next;
		}
		while (!list.isEmpty()) {
			System.out.print(list.pop()+"-->");
		}
	}
	//打印链表(利用栈从尾到头打印链表)
	public static void printNode(ListNode head) {
		ListNode cur = head;
		while (cur != null) {
			stack.push(cur.val);
			cur = cur.node_next;
		}
		while (!stack.isEmpty()) {
			System.out.print(stack.pop()+"-->");
		}
	}
	//删除节点
	public static void removeNode(ListNode head,int val) {
		if (head==null) {
			return;
		}
		if (head.val == val) {
			head = head.node_next;
		}else {
			ListNode curNode = head;
			while (curNode.node_next!=null && curNode.node_next.val != val) 			{
				curNode = curNode.node_next;
			}
			if (curNode.node_next !=null && curNode.node_next.val==val) {
				curNode.node_next = curNode.node_next.node_next;
			}
		}
	}
	//插入节点
	public static void addNode(ListNode head,int val) {
		ListNode newNode = new ListNode(val);
		if (head==null) {
			head = newNode;
		}else {
			ListNode CurNode = head;
			while (CurNode.node_next != null) {
				CurNode = CurNode.node_next;
			}
			CurNode.node_next = newNode;
		}
	}
	//结构体
	static class ListNode{
		int val;
		ListNode node_next;
		public ListNode(int val) {
			super();
			this.val = val;
		}
	}
````

### 6.二叉树的构造

利用中序和后序构造二叉树

````java
public TreeNode buildTree(int[] inorder, int[] postorder) {
        if(postorder.length == 0 || inorder.length == 0) return null;
        Index index = new Index();
        index.index = postorder.length - 1;
        return helper(0,inorder.length - 1,inorder,postorder,index);
    }
    class Index 
    {
	    int index;
    }
    public TreeNode helper(int inStart,int inEnd,int[] inorder, int[] postorder,Index index){
        if(inStart > inEnd) {
            return null;
        }else{
            TreeNode root = new TreeNode(postorder[index.index]);
            index.index--;
            int mid = 0;
            for(int i=inStart;i<=inEnd;i++){
                if(inorder[i] == root.val){
                    mid = i;
                }
            }
            root.right = helper(mid+1,inEnd,inorder, postorder,index);
            root.left = helper(inStart,mid-1,inorder, postorder,index);
            return root;
        }
    }
````

前序和中序构造二叉树

````java
public TreeNode buildTree(int[] preorder, int[] inorder) {
        if(preorder.length == 0 || inorder.length == 0) return null;
        return helper(0,inorder.length - 1,preorder,inorder);
    }
    int preStart = 0;
    public TreeNode helper(int inStart,int inEnd,int[] preorder, int[] inorder){
        if(preStart>preorder.length || inStart>inEnd){
            return null;
        }else{
            TreeNode root = new TreeNode(preorder[preStart]);
            int mid = 0;
            for(int i=inStart;i<=inEnd;i++){
                if(inorder[i] == preorder[preStart]){
                    mid = i;
                }
            }
            preStart++;
            root.left = helper(inStart,mid-1,preorder,inorder);
            root.right = helper(mid+1,inEnd,preorder,inorder);
            return root;
        }
}
````

### 7.两个栈实现队列

````java
public class StackToQueue {

	Stack<Integer> stack1 = new Stack<>();
	Stack<Integer> stack2 = new Stack<>();

	public void addLast(int x) {
		stack1.push(x);
	}
	
	public int removeFirst() {
		if (size()!=0) {//弹出条件：当队列不为空的时候
			if (stack2.isEmpty()) {//当s2为空的时候，将s1中的数字压入s2
				while (!stack1.isEmpty()) {
					stack2.push(stack1.pop());
				}
			}
			return stack2.pop();//只要s2不为空，就弹出
		}else {
			System.out.println("队列已空");
			return -1;
		}
	}

	public int size() {
		return stack1.size() + stack2.size();
	}

	public static void main(String[] args) {
		StackToQueue queue = new StackToQueue();
		 queue.addLast(1);
		 queue.addLast(2);
		 queue.addLast(3);
		 queue.addLast(4);
		 System.out.println(queue.removeFirst());
		 System.out.println(queue.removeFirst());
		 System.out.println(queue.removeFirst());
		 queue.addLast(5);
		 queue.addLast(6);
		 System.out.println(queue.removeFirst());
		 System.out.println(queue.removeFirst());
		 System.out.println(queue.removeFirst());
	}
}
````

### 7.两个队列实现栈

````java
public class QueueToStack {

	LinkedList<Integer> queue1 = new LinkedList<>();
	LinkedList<Integer> queue2 = new LinkedList<>();

	public void push(int x) {
		queue1.addLast(x);
	}

	public int pop() {
		if (size()!=0) {
			if (!queue1.isEmpty()) {
				putN_1ToOther();
				return queue1.removeFirst();
			}else if (!queue2.isEmpty()) {
				putN_1ToOther();
				return queue2.removeFirst();
			}
			return -1;
		}else {
			return -1;
		}
	}

	// 移动n-1个到另一个队列
	public void putN_1ToAnthor() {
		if (!queue1.isEmpty()) {
			while (queue1.size() > 1) {
				queue2.addLast(queue1.removeFirst());
			}
		} else if (!queue2.isEmpty()) {
			while (queue2.size() > 1) {
				queue1.addLast(queue2.removeFirst());
			}
		}
	}

	public int size() {
		return queue1.size() + queue2.size();
	}

	public static void main(String[] args) {
		QueueToStack stack = new QueueToStack();
		stack.push(1);
		stack.push(2);
		stack.push(3);
		stack.push(4);
		System.out.println(stack.pop());
		System.out.println(stack.pop());
		stack.push(5);
		stack.push(6);
		System.out.println(stack.pop());
		System.out.println(stack.pop());
		System.out.println(stack.pop());
		System.out.println(stack.pop());
		System.out.println(stack.pop());
	}
}
````

### 8.旋转数组中最小数

````java
public static int min(int[] nums) {
		int p1 = 0;
		int p2 = nums.length - 1;
		int mid = p1;
		while (nums[p1] >= nums[p2]) {
			if (p2 - p1 == 1) {
				mid = p2;
				break;
			}
			mid = (p1 + p2) / 2;
          	//[1,0,1,1,1],[1,1,1,0,1]特殊处理这种情况
			if (nums[p1] == nums[p2] && nums[p1] == nums[mid]) {
				int temp = nums[p1];
				for (int i = 1; i < nums.length; i++) {
					if (temp > nums[i]) {
						temp = nums[i];
					}
				}
				return temp;
			}
			if (nums[mid] >= nums[p1]) {
				p1 = mid;
			} else if (nums[mid] <= nums[p2]) {
				p2 = mid;
			}
		}
		return nums[mid];
	}
````

### 10.二进制中1的个数

````java
//未考虑负数的情况
public static int numberOf2(int n) {
		int count = 0;
		while (n>0) {
			count += n&1;
			n = n>>1;
		}
		return count;
}
//可以考虑到负数的情况
public static int numberOf2(int n) {
		int count = 0;
  		int flag = 1;
		while (n>0) {
			if(n&flag==0) count++;
			flag = flag<<1;
		}
		return count;
}
//将一个数减去1再和原整数做与运算，会把整数最右边的一个1变成0，整数中有多少个1，就会进行多少次这样的操作
public static int numberOf1(int n) {
		int count = 0;
		while (n>0) {
			++count;
			n = (n-1)&n;
		}
		return count;
}
````

### 11.数值的整数次方

1. 当exponent为负数时，转成正数求解后，求解其倒数
2. 当exponent为0时，直接返回0，当exponent为1时，返回base本身
3. 当base小数为0时，不能直接和0比较，需要重写equals方法，并且base为0考虑其为非法输入。
4. 使用exponent>>1 代替除法，使用位与运算代替 %，位运算效率比乘除法高。

![](http://img.blog.csdn.net/20160904104310985?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

````java
public static double power(double base, int exponent) {
		// 判断底数为0并且指数小于0
		if (equals(base, 0.0)) {
			invalideInput = true;
			return 0.0;
		}
		int absExponent = exponent;
		if (exponent > 0) {
			absExponent = exponent;
		}
		if (exponent < 0) {
			absExponent = -exponent;
		}
		double result = powerWithUnsignExponent(base, absExponent);
		if (exponent < 0) {
			result = 1.0 / result;
		}
		return result;
	}

	public static double powerWithUnsignExponent(double base, int exponent) {
		if (exponent == 0) {
			return 1;
		}
		if (exponent == 1) {
			return base;
		}
		double result = powerWithUnsignExponent(base, exponent >> 1);
		result *= result;
		//a^n = a^(n/2) * a^(n/2) (n为偶数)
		//a^n = a^(n/2) * a^(n/2) * a (n为奇数)
		//这个公式可以将时间复杂度提高到O(logn)，否则直接求指数到时间复杂度为O(n)
		if ((exponent & 0x1) == 1) {//判断exponent是奇数还是偶数
			result *= base;
		}
		return result;
	}

	public static boolean equals(double m, double n) {
		if ((m - n > -0.000001) && (m-n) < 0.000001) {
			return true;
		} else {
			return false;
		}
	}
````

### 12.打印1到最大的n位数

````Java
//字符数组加1
public static boolean isCrement(int[] number) {
		boolean isOverFlow = false;
		int takeOver = 0;
		for (int i = number.length-1; i >=0; i--) {
			int sum = number[i] + takeOver;
			if (i == number.length-1) {
				sum++;
			}
			if (sum>=10) {
				if (i==0) {
					isOverFlow = true; 
				}else {
					takeOver = 1;
					sum -= 10;
					number[i] = sum;
				}
			}else {
				number[i] = sum;
				break;
			}
		}
		return isOverFlow;
	}
	
	public static void printNumber(int[] number) {
		boolean isBeginning = true;
		for (int i = 0; i < number.length; i++) {
			if (isBeginning && number[i] != 0) {
				isBeginning = false;
			}
			if (!isBeginning) {
				System.out.print(number[i]);
			}
		}
		System.out.println();
	}
	
	public static void printMaxOfNDigits(int n) {
		if (n<0) {
			System.out.println("输入出错");
		}
		int[] number = new int[n];
        for(int i=0;i<n;i++){
          number[i] = 0;
        }
		while (!isCrement(number)) {
			printNumber(number);
		}
	}
````

### 13.在O(1)时间内删除节点

时间复杂度

((n-1)*O(1) + O(n))/n = O(1)

只有在尾节点时需要从头节点遍历，时间复杂度为O(n)，其他时候复杂度为O(1)，综合起来时间复杂度还是O(1)

1. 当只有一个节点的时候（头节点也是尾街店），直接删除即可
2. 当有多个节点并是中间节点时，将要删除的节点的后一个节点的值复制到要删除的节点并覆盖它的值，然后把它后面的节点删掉
3. 当是尾节点的时候，需要从头节点开始遍历，并删除该节点

````java
public static ListNode deleteNode(ListNode head, ListNode toDeleteNode) {
		if (head == null || toDeleteNode == null) {
			return head;
		}
  		// 链表中只有一个节点，删除头节点（也是尾节点）
		if (head == toDeleteNode) {
			return head.node_next;
		}
		// 要删除的不是尾节点
  		//将要删除的下一个节点的值复制到要删除的节点上，然后删除它的下一个节点
		if (toDeleteNode.node_next != null) {
			toDeleteNode.val = toDeleteNode.node_next.val;
			toDeleteNode.node_next = toDeleteNode.node_next.node_next;
		} else {// 链表中有多个节点，删除尾节点
			ListNode curNode = head;
			while (curNode.node_next != null) {
				curNode = curNode.node_next;
			}
			curNode = null;
			toDeleteNode = null;
		}
		return head;
	}
````

### 14.调整数组顺序使奇数位于偶数前面

二分让时间复杂度降低，提高扩展性，使用一个函数来规定函数条件成立的标准

````java
public static void order(int[] nums) {
		if (nums == null || nums.length == 0) {
			return;
		}
  		if(nums.length == 1) return;
		int left = 0;
		int right = nums.length - 1;
		while (left < right) {
			while (left < right && !isEven(nums[left])) {
				left++;
			}
			while (left<right && isEven(nums[right])) {
				right--;
			}
			if(isEven(nums[left]) && !isEven(nums[right])) {
				int temp = nums[left];
				nums[left] = nums[right];
				nums[right] = temp;
			}
		}
	}
	//判断一个数是否是偶数
	public static boolean isEven(int n) {
		return (n & 1) == 0;
	}
````

### 15.链表中倒数第k个节点

````java
public static ListNode kthNodeFromEnd(ListNode head,int k) {
		//当头指针为空或者k=0时，不在考虑范围内
		if (head==null || k==0) {
			return null;
		}
		ListNode pNode = head;
		ListNode pBehind = null;
		for (int i = 0; i < k-1; i++) {
			if (pNode.node_next!=null) {
				pNode = pNode.node_next;//先走到k-1到位置上
			}else {
				return null;//当链表长度小于k当时候，会抛空指针
			}
		}
		pBehind = head;
		while (pNode.node_next!=null) {
			pNode = pNode.node_next;//继续走直到走到链表尾部
			pBehind = pBehind.node_next;//后一个指针从头节点开始，当前面一个指针走到尾部，循环结束，该指针刚好走到第k个位置上
		}
		return pBehind;
	}
````

### 16.反转链表

三个指针分别记录当前节点，当前节点的下一个节点和上一个节点，记录上一个和下一个节点未来防止反转之后链表断裂，反转之后的头节点就是为节点。

````Java
public static ListNode reverseNode(ListNode head) {
		if (head==null) {
			return null;
		}
		ListNode pReverseHead = null;//转换后的头节点
		ListNode pNode = head;//当前节点
		ListNode pPrev = null;//当前节点的前一个节点
		while (pNode!=null) {
			ListNode pNext = pNode.node_next;
			if (pNext==null) {
				pReverseHead = pNode;
			}
			pNode.node_next = pPrev;
			pPrev = pNode;
			pNode = pNext;
		}
		return pReverseHead;
	}
````

### 17.合并两个排序的链表

````java
public static ListNode mergeNode(ListNode head1,ListNode head2) {
		if (head1==null) {
			return head2;
		}else if (head2==null) {
			return head1;
		}
		ListNode pMergedHead = null;
		if (head1.val < head2.val) {
			pMergedHead = head1;
			pMergedHead.node_next = mergeNode(head1.node_next, head2);
		}else {
			pMergedHead = head2;
			pMergedHead.node_next = mergeNode(head1, head2.node_next);
		}
		return pMergedHead;
	}
````

### 18.树的子结构

````java
public static boolean sameSubTree(TreeNode node1,TreeNode node2) {
		if (node1==null && node2 == null) {
			return false;
		}
		boolean result = false;
		if (node1.val == node2.val) {
			result = doesTree1HasTree2(node1, node2);
		}
		if (!result) {
			result = sameSubTree(node1.left, node2);
		}
		if (!result) {
			result = sameSubTree(node1.right, node2);
		}
		return result;
	}
	
	public static boolean doesTree1HasTree2(TreeNode node1,TreeNode node2) {
		if (node2==null) {
			return true;
		}
		if (node1 == null) {
			return false;
		}
		if (node1.val != node2.val) {
			return false;
		}
		return doesTree1HasTree2(node1.left, node2.left) && 			doesTree1HasTree2(node1.right, node2.right);
	}
````

###	19.树的镜像

````java
public static void mirrorTreeNode(TreeNode node) {
		if (node == null) {
			return;
		}
		if (node.left== null && node.right == null) {
			return;
		}
		TreeNode temp = node.left;
		node.left = node.right;
		node.right = temp;
		if (node.left!=null) {
			mirrorTreeNode(node.left);
		}
		if (node.right!=null) {
			mirrorTreeNode(node.right);
		}
	}
````

### 20.顺时针打印矩阵

````java
public static void printMatrixInCircle(int[][] nums) {
		if (nums == null || nums.length == 0 || nums[0].length == 0) {
			return;
		}
		int start = 0;
    	//左上角的行坐标和纵坐标总是相等，5*5的矩阵左上角是 2*2，6*6的矩阵左上角是2*2，循环条件是
    	//columes>startX*2  && rows > startY*2
		while (nums.length > start * 2 && nums[0].length > start * 2) {
			printMatrixInCircle(nums, nums.length, nums[0].length, start);
			start++;
		}
	}

	private static void printMatrixInCircle(int[][] nums, int rows, int cols, int start) {
		int endX = cols - 1 - start;
		int endY = rows - 1 - start;
		// 从左往右打印一行，至少有一行
		for (int i = start; i <= endX; ++i) {
			printNumber(nums[start][i]);
		}
		System.out.println();
		// 从上到下打印一列，终止行号大于起始行号
		for (int i = start+1; i <= endY; i++) {
			printNumber(nums[i][endX]);
		}
		System.out.println();
		// 从右到左打印一行，终止行号大于起始行号，终止列号大于起始列号
		if (start < endX && start < endY) {
			for (int i = endX - 1; i >= start; --i) {
				printNumber(nums[endY][i]);
			}
			System.out.println();
		}
		// 从下到上打印一列，终止行号比起始行号至少大2，终止列号大于起始列号
		if (start < endX && start < endY - 1) {
			for (int i = endY - 1; i >= start + 1; --i) {
				printNumber(nums[i][start]);
			}
			System.out.println();
		}
	}

	private static void printNumber(int number) {
		System.out.print(number + " ");
	}
````

### 21.包含min函数的栈

````java
public class MinStack {

	Stack<Integer> date = new Stack<>();
	Stack<Integer> min = new Stack<>();

	public static void main(String[] args) {
		MinStack minStack = new MinStack();
		minStack.push(3);
		minStack.push(4);
		minStack.push(2);
		minStack.push(1);
		minStack.push(0);
		System.out.println(minStack.pop());
		System.out.println(minStack.min());
		System.out.println(minStack.min());
	}

	public void push(int x) {
		date.push(x);
		if (min.size() == 0 || x < min.peek()) {
			min.push(x);
		}else {
			min.push(min.peek());
		}
	}
	
	public int pop() {
		if (date.size()>0 && min.size()>0) {
			min.pop();
			return date.pop();
		}
		return -1;
	}
	
	public int min() {
		if (date.size()>0 && min.size()>0) {
			return min.peek();
		}
		return -1;
	}
}

````

### 22.栈的压入、弹出序列

````java
public static boolean isPopOrder(int[] pushList,int[] popList) {
		if (pushList==null || popList==null || pushList.length==0 || popList.length == 0) {
			return false;
		}
		Stack<Integer> pushStack = new Stack<>();
		int pushIndex = 0;
		int popIndex = 0;
		while (popIndex < popList.length) {
			//入栈元素还未全部入栈的条件下，如果栈为空，或者栈顶元素不与当前处理的不相等，则一直进行入栈操作，直到入栈元素全部入栈或者找到出栈元素相等的元素
			while (pushIndex<pushList.length && (pushStack.isEmpty() || pushStack.peek() != popList[popIndex])) {
				//入栈数组中的元素入栈
				pushStack.push(pushList[pushIndex]);
				//指向下一个要处理的入栈元素
				pushIndex++;
			}
			//如果在上一步的入栈操作中找到了与出栈元素相等的元素
			if (pushStack.peek() == popList[popIndex]) {
				pushStack.pop();//将元素出栈
				popIndex++;//指向下一个要处理的元素
			}else {
				return false;//如果没有找到与出栈元素相等的元素，说明出栈顺序不合法
			}
		}
		return true;
	}
````

### 24.二叉搜索树的后序遍历序列

````java
private static boolean verifySequenceOfBST(List<Integer> sequence, int start, int end) {
		if (start >= end) {
			return true;
		}
		int index = start;
		//从左往右找到第一个不大于根节点都元素的位置
		while (index<end-1 && sequence.get(index) < sequence.get(end)) {
			index++;
		}
		//到此，[start,index-1]的值都比根节点小，[start,index-1]可以看作是根节点都左子树
		//right记录第一个不小于根节点都位置
		int temp = index;
		//找到[index,end-1]都所有元素都大于根节点，[index,end-1]为根节点都右子树
		while (index<end-1 && sequence.get(index) > sequence.get(end)) {
			index++;
		}
		//当index=end-1，说明目前是合法的
		if (index!=end-1) {
			return false;
		}
		return verifySequenceOfBST(sequence, start, temp - 1) && verifySequenceOfBST(sequence, temp, end - 1);
	}
````

### 25.二叉树中和某一值的路径

````java
	public static void findPath(TreeNode node, int expectSum) {
		List<Integer> path = new ArrayList<>();
		if (node != null) {
			findPath(node, path, expectSum, 0);
		}
	}

	public static void findPath(TreeNode node, List<Integer> path, int expectSum, int curSum) {
		if (node == null) {
			return;
		}
		path.add(node.val);
		curSum += node.val;

		if (curSum < expectSum) {
			if (node.left != null) {
				findPath(node.left, path, expectSum, curSum);
			}
			if (node.right != null) {
				findPath(node.right, path, expectSum, curSum);
			}
		} else if (curSum == expectSum) {
			if (node.left == null && node.right == null) {
				for (Integer val : path) {
					System.out.print(val + " ");
				}
			}
		}
		path.remove(path.size() - 1);
		curSum -= node.val;
	}
````

### 26.复杂链表的复制

````java
public static void cloneNode(ListNode head) {
		if (head == null) {
			return;
		}
		ListNode pNode = head;
		ListNode cloneNode = new ListNode();
		cloneNode.val = pNode.val;
		cloneNode.node_next = pNode.node_next;
		pNode.node_next = cloneNode;
		pNode = cloneNode.node_next;
		cloneNode.node_sibling = null;
	}

	public static void connectSiblingNode(ListNode head) {
		if (head == null) {
			return;
		}
		ListNode pNode = head;
		if (pNode != null) {
			ListNode cloneNode = pNode.node_next;
			while (pNode.node_sibling != null) {
				cloneNode.node_sibling = pNode.node_sibling.node_next;
				pNode = cloneNode.node_next;
			}
		}
	}

	public static ListNode reConnectNode(ListNode head) {
		ListNode cloneHead = null;
		ListNode cloneNode = null;
		ListNode pNode = head;
		if (pNode != null) {
			cloneHead = cloneNode = pNode.node_next;
			pNode.node_next = cloneNode.node_next;
			pNode = cloneNode.node_next;
			cloneNode.node_next = pNode.node_next;
			cloneNode = cloneNode.node_next;
		}
		return cloneHead;
	}

	public static ListNode clone(ListNode head) {
		cloneNode(head);
		connectSiblingNode(head);
		return reConnectNode(head);
	}

	static class ListNode {
		int val;
		ListNode node_next;
		ListNode node_sibling;
	}
````

