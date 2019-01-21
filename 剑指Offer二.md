### 27.二叉搜索树与双向链表

将二叉搜索树转换成一个排序的双链表，利用二叉搜索树的特性，非空二叉树的左子树上的节点的值都比根节点的值小，右子树上的节点的值都比根节点的值大，利用搜索二叉树的中序遍历得到的就是一个排序的序列；双链表不仅要指向它后面的节点还要指向它前面的节点，所以需要一个引用变量preNode，每次访问过的节点要保存在preNode中，这样才能得到一个节点前一个节点，二叉搜索树中节点的属性有left right val;排序双向链表的前一个节点对应left 后一个节点对应right，无新节点生成。

````java
public static TreeNode convert(TreeNode root) {
		if (root == null) {
			return null;
		}
		Stack<TreeNode> stack = new Stack<>();
		TreeNode current = root;
		TreeNode preNode = null;
		boolean isFirst = true;
		while (current != null || !stack.isEmpty()) {
			while (current != null) {
				stack.push(current);
				current = current.left;
			}
			if (!stack.empty()) {
				current = stack.pop();
				if (isFirst) {
					root = current;
					preNode = root;
					isFirst = false;
				} else {
					preNode.right = current;
					current.left = preNode;
					preNode = current;
				}
				current = current.right;
			}
		}
		return root;
	}

	static class TreeNode {
		int val;
		TreeNode left;
		TreeNode right;

		public TreeNode(int val) {
			this.val = val;
		}
	}
````

### 28.字符串排列

从集合依次选出每一个元素，作为排列的第一个元素，然后对剩余的元素进行全排列，如此递归处理；

比如：首先我要打印abc的全排列，就是第一步把a 和bc交换（得到bac,cab），这需要一个for循环，循环里面有一个swap，交换之后就相当于不管第一步了，进入下一步递归，所以跟一个递归函数， 完成递归之后把交换的换回来，变成原来的字串

```
abc 为例子：
1. 固定a, 求后面bc的全排列： abc, acb。 求完后，a 和 b交换； 得到bac,开始第二轮
2. 固定b, 求后面ac的全排列： bac, bca。 求完后，b 和 c交换； 得到cab,开始第三轮
3. 固定c, 求后面ba的全排列： cab, cba
 即递归树： 
　　　　　str:　　 a    　　　　  b 　　　　　　 c
　　　　　　 　　 ab ac     　　ba bc  　　　 ca cb
　   　result: abc acb    　 bac bca　    cab cba
```

````java
public static void recursionArrange(char[] arr, int start, int end) {
		if (end <= 1) {
			return;
		}
		if (start == end) {
			for (int i = 0; i < arr.length; i++) {
				System.out.print(arr[i] + " ");
			}
			System.out.println();
		} else {
			for (int i = start; i <= end; i++) {
				swap(arr, i, start);
				recursionArrange(arr, start + 1, end);
				swap(arr, start, i);
			}
		}
	}
````

###	字符串的组合

输入三个字符 `a、b、c`，则它们的组合有`a` `b` `c` `ab` `ac` `bc` `abc`。当然我们还是可以借鉴全排列的思路，利用问题分解的思路，最终用递归解决。不过这里介绍一种比较巧妙的思路 —— 基于位图。
假设原有元素`n`个，最终的组合结果有`2^n - 1`. 可以使用`2^n - 1`个位，1表示取该元素，0表示不取。 所以`a`表示`001`,取`ab`是011。
`001,010,011,100,101,110,111`。对应输出组合结果为：`a,b,ab,c,ac,bc,abc`。
因此可以循环 `1~2^n-1`(字符串长度)，然后输出对应代表的组合即可。

````java
public static void combine(char[] cs,int begin,int number,Stack<Character> stack) {
		if (number==0) {
			System.out.println(stack.toString());
			return;
		}
		if (begin == cs.length) {
			return;
		}
		stack.push(cs[begin]);
		combine(cs, begin+1, number-1, stack);
		stack.pop();
		combine(cs, begin+1, number, stack);
	}

public static void Combination(char [] s){
        if(s.length == 0){
            return;
        }
        int len = s.length;
        int n = 1<<len;
        //从1循环到2^len-1
        for(int i=0;i<n;i++){
            StringBuffer sb = new StringBuffer();
//查看第一层循环里面的任意一种取值当中的哪一位是1[比如ab,011]， 如果是1，对应的字符就存在，打印当前组合。 
            for(int j=0;j<len;j++){
                if( (i & (1<<j)) != 0) // 对应位上为1，则输出对应的字符
                {
                    sb.append(s[j]);
                }
            }
            System.out.print(sb + " ");
        }   
    }

````

### 29.数组中出现次数超过一半的数字

````java
public static int moreThanHalf(int[] nums) {
		if (nums == null || nums.length == 0) {
			return -1;
		}
		int left = 0;
		int right = nums.length - 1;
		int mid = (left + right) / 2;
		int pivotPos = partition(nums, left, right);
		while (pivotPos != mid) {
			if (pivotPos > mid) {
				pivotPos = partition(nums, left, pivotPos - 1);
			} else {
				pivotPos = partition(nums, pivotPos + 1, right);
			}
		}
		int key = nums[mid];
		if (!isHalfOfNum(nums, key)) {
			return -1;
		}
		return key;
	}

	public static boolean isHalfOfNum(int[] nums, int key) {
		int times = 0;
		for (int i = 0; i < nums.length; i++) {
			if (nums[i] == key) {
				times++;
			}
		}
		if (times * 2 > nums.length) {
			return true;
		} else {
			return false;
		}
	}

	public static int moreHalfNum(int[] nums) {
		if (nums == null || nums.length == 0) {
			return -1;
		}
		int res = nums[0];
		int times = 1;
		for (int i = 1; i < nums.length; i++) {
			if (times == 0) {
				res = nums[i];
				times = 1;
			} else if (res == nums[i]) {
				times++;
			} else {
				times--;
			}
		}
		if (!isHalfOfNum(nums, res)) {
			return -1;
		}
		return res;
	}
````

### 30.最小的K个数

````Java
public static void getLeastKNum(int[] nums, int k) {
		if (nums == null || nums.length == 0 || k <= 0) {
			return;
		}
		int start = 0;
		int end = nums.length - 1;
		int index = partition(nums, start, end);
		while (index != k - 1) {
			if (index > k - 1) {
				index = partition(nums, start, index - 1);
			} else {
				index = partition(nums, index + 1, end);
			}
		}
		for (int i = 0; i < k; i++) {
			System.out.print(nums[i] + " ");
		}
		System.out.println();
	}
````

### 31.连续子数组的最大和

~~~java
public static int findMaxSumOdSubArray(int[] nums) {
		if (nums == null || nums.length == 0) {
			return -1;
		}
		int maxSum = 0;
		int curSum = 0;
		for (int i = 0; i < nums.length; i++) {
			curSum += nums[i];
			if (curSum>maxSum) {
				maxSum = curSum;
			}
			if (curSum<0) {
				curSum = 0;
			}
		}
		return maxSum;
	}

public static int MaxSubSequence3(int[] array){  
    int length=array.length;  
    int[] MaxSum=new int[length];  
    MaxSum[0]=array[0];  
        for(int i=1;i<length;i++){  
            MaxSum[i]=Math.max(MaxSum[i-1]+array[i], array[i]);  
        }  
        //找到MaxSum中的最大值  
        int maxSum=Integer.MIN_VALUE;  
        for(int i=0;i<MaxSum.length;i++){  
            if(MaxSum[i]>maxSum){  
                maxSum=MaxSum[i];  
            }  
        }  
        return maxSum;  
  }  
~~~

### 33.把数组排成最小的数

````java
public static int partitionArr(String[] arr,int left,int right,Comparator<String> comparator) {
		String pivot = arr[left];
		while (left<right) {
			while (left <right && comparator.compare(arr[right], pivot) >=0) {
				right--;
			}
			arr[left] = arr[right];
			while (left <right && comparator.compare(arr[left], pivot) <=0) {
				left++;
			}
			arr[right] = arr[left];
		}
		arr[left] = pivot;
		return left;
	}
	
	public static void quickSort(String[] arr,int left,int right,Comparator<String> comparator) {
		if (left>right) {
			return;
		}
		int pivotPos = partitionArr(arr, left, right, comparator);
		quickSort(arr, left, pivotPos-1, comparator);
		quickSort(arr, pivotPos+1, right, comparator);
	}
	
	public static String printMinNumber(String[] arr) {
		if (arr==null || arr.length==0) {
			return null;
		}
		
		MyComparator comparator = new MyComparator();
		quickSort(arr, 0, arr.length-1, comparator);
		StringBuilder sBuilder = new StringBuilder(); 
		for (String string : arr) {
			sBuilder.append(string);
		}
		return sBuilder.toString();
	}
	
	
	static class MyComparator implements Comparator<String>{

		@Override
		public int compare(String o1, String o2) {
			if (o1 == null || o2 == null) {
                throw new IllegalArgumentException("Arg should not be null");
            }
            String s1 = o1 + o2;
            String s2 = o2 + o1;
            return s1.compareTo(s2);
		}
	}
````

### 34、丑数

````java
public static void getUglyNum(int index) {
		if (index < 0) {
			return;
		}
		int[] arr = new int[index];
		arr[0] = 1;
		int m2 = 0;
		int m3 = 0;
		int m5 = 0;
		for (int i = 1; i < index; i++) {
			int min = min(arr[m2] * 2, arr[m3] * 3, arr[m5] * 5);
			arr[i] = min;
			while (arr[m2] * 2 == arr[i]) {
				m2++;
			}
			while (arr[m3] * 3 == arr[i]) {
				m3++;
			}
			while (arr[m5] * 5 == arr[i]) {
				m5++;
			}
		}
		for (int i = 1; i < arr.length; i++) {
			System.out.println(arr[i-1]);
		}
	}
	public static int min(int m1, int m2, int m3) {
		int min = m1 < m2 ? m1 : m2;
		return min < m3 ? min : m3;
	}
````

### 35.第一个只出现一次的字符

````java
public static Character firstNotRepeatChar(String str) {
		if (str == null || str.length() == 0) {
			return null;
		}
		LinkedHashMap<Character, Integer> map = new LinkedHashMap<>();
		char[] array = str.toCharArray();
		for (int i = 0; i < array.length; i++) {
			if (map.containsKey(array[i])) {
				map.put(array[i], map.get(array[i]) + 1);
			} else {
				map.put(array[i], 1);
			}
		}
		for (char c : array) {
			if (map.get(c) == 1) {
				return c;
			}
		}
		return null;
	}
````

### 36.数组中的逆序对

````java
static int count = 0;

	public static void merge(int[] arr, int left, int mid, int right) {
		int[] nums = new int[right - left + 1];
		int i = left;
		int j = mid + 1;
		int k = 0;
      	//逐个归并
		while (i <= mid && j <= right) {
			if (arr[i] < arr[j]) {
                nums[k++] = arr[i++];
			} else {
				nums[k++] = arr[j++];
				count += mid - i + 1;
			}
		}
		//把左边剩余的归并
		while (i <= mid) {
			nums[k++] = arr[i++];
		}
		//把右边剩余的归并
		while (j <= right) {
			nums[k++] = arr[j++];
		}
		current = 0;
		//把临时数组拷贝到原数组
		while (temp <= right) {
			arr[temp++] = nums[k++];
		}
	}

	public static void mergeSort(int[] nums, int left, int right) {
		int mid = (left + right) / 2;
		if (left < right) {
			mergeSort(nums, left, mid);
			mergeSort(nums, mid + 1, right);
			merge(nums, left, mid, right);
		}
	}
````

### 37.两个链表的第一个公共结点

````java
public static ListNode findFirstCommonNode(ListNode p1, ListNode p2) {
		int len1 = getLength(p1);
		int len2 = getLength(p2);
		if (len1 == 0 || len2 == 0) {
			return null;
		}
		ListNode longNode = null;
		ListNode shortNode = null;
		int dif = 0;
		if (len1 > len2) {
			longNode = p1;
			shortNode = p2;
		} else {
			longNode = p2;
			shortNode = p1;
		}
		dif = Math.abs(len1 - len2);
		for (int i = 0; i <= dif; i++) {
			longNode = longNode.node_next;
		}
		while (longNode != null && shortNode != null) {
			if (longNode.val == shortNode.val) {
				return longNode;
			}
			longNode = longNode.node_next;
			shortNode = shortNode.node_next;
		}
		return longNode;
	}

//------栈---------相同的都弹出，直到最后一个不相同的节点开始，就是公共结点
public static ListNode findFirstCommonNodeWithStack(ListNode p1, ListNode p2) {
		if (p1 == null || p2 == null) {
			return null;
		}
		Stack<ListNode> stack1 = new Stack<>();
		Stack<ListNode> stack2 = new Stack<>();
		while (p1 != null) {
			stack1.push(p1);
			p1 = p1.node_next;
		}
		while (p2 != null) {
			stack2.push(p2);
			p2 = p2.node_next;
		}
		ListNode commonNode = null;
		while (!stack1.isEmpty() && !stack2.isEmpty() && stack1.peek().val == stack2.peek().val) {
			stack2.pop();
			commonNode = stack1.pop();
		}
		return commonNode;
	}
````

### 38.数字在数组中出现的次数O(logn)

````java
public static int getFirstK(int[] nums, int k, int start, int end) {
		if (start > end) {
			return -1;
		}
		int midIndex = (start + end) / 2;
		int midData = nums[midIndex];
		if (midData == k) {
			if ((midIndex > 0 && nums[midIndex - 1] != k) || midIndex == 0) 			{
				return midIndex;
			} else {
				end = midIndex - 1;
			}
		} else if (midData > k) {
			end = midIndex - 1;
		} else {
			start = midIndex + 1;
		}
		return getFirstK(nums, k, start, end);
	}
	
	public static int getLastK(int[] nums, int k, int start, int end) {
		if (start>end) {
			return -1;
		}
		int midIndex = (start + end) / 2;
		int midData = nums[midIndex];
		if (midData == k) {
			if ((midIndex < nums.length-1 && nums[midIndex + 1] != k) || midIndex == nums.length-1) {
				return midIndex;
			} else {
				start = midIndex + 1;
			}
		} else if (midData > k) {
			end = midIndex - 1;
		} else {
			start = midIndex + 1;
		}
		return getLastK(nums,k,start,end);
	}
	
	public static int getNumOfK(int[] nums,int k) {
		int num = 0;
		if (nums!=null && nums.length>0) {
			int first = getFirstK(nums, k, 0, nums.length-1);
			int last = getLastK(nums, k, 0, nums.length-1);
			if (first>-1 && last>-1) {
				num = last-first+1;
			}
		}
		return num;
	}
````

### 二叉树的深度

````java
public static int getTreeDepth(TreeNode root) {
		if (root == null) {
			return 0;
		}
		int left = getTreeDepth(root.left);
		int right = getTreeDepth(root.right);
		return left > right ? left + 1 : right + 1;
	}

public static int getTreeDepth(TreeNode root) {
		if (root == null) {
			return 0;
		}
  		if (root.left == null && root.right==null) {
			return 1;
		}
  		if (root.left == null || root.right==null) {
			return 2;
		}
		LinkedList<TreeNode> queue = new LinkedList<>();
  		queue.add(root);
  		int level = 0;
  		int Width = 0;
         while(!queue.isEmpty()){
			int cur = 0;
          	int last = queue.size();
           	width = Math.max(width,queue.size());
            while(cur < last){
				TreeNode current = queue.remove();
              	cur++;
                if(current.left!=null){
                  queue.add(current.left);
                }
              	if(current.right!=null){
                  queue.add(current.right);
                }
            }
          	level++;
        }
  	return level;
	}
````

### 平衡树

````java
public static boolean isBalance(TreeNode root, int depth) {
		if (root == null) {
			depth = 0;
			return true;
		}
		int left = 0, right = 0;
		if (isBalance(root.left, left) && isBalance(root.right, right)) {
			int dif = Math.abs(left - right);
			if (dif <= 1) {
				depth = 1 + Math.max(left, right);
				return true;
			}
		}
		return false;
	}
````

### 40.数组中只出现一次的数字

````java
public static int[] findNumAppearanceOnce(int[] nums) {
		int[] result = { 0, 0 };
		if (nums == null || nums.length == 0) {
			return result;}
		int xor = 0;
		for (int i : nums) {
			xor ^= i;}
		int indexOf1 = findFirstBit1(xor);
		for (int i : nums) {
			if (isBit1(i, indexOf1)) {
				result[0] ^= i;
			} else {
				result[1] ^= i;
			}
		}
		return result;
	}
	//判断整数在num的二进制表示中从右边数起的indexBit是不是1
	private static boolean isBit1(int num, int indexBit) {
		num >>>= indexBit;
		return (num & 1) == 1;
	}
	private static int findFirstBit1(int xor) {//在整数num中找到最右边是1的位
		int index = 0;
		while ((xor & 1) == 0 && index < 32) {
			xor >>>= 1;
			index++;
		}
		return index;
	}
````

### 41.和为S的两个数

````java
public static void findNumbersWithSum(int[] nums, int sum) {
		if (nums == null || nums.length == 0) {
			return;
		}
		int start = 0;
		int end = nums.length - 1;
		while (start < end) {
			int curSum = nums[start] + nums[end];
			if (curSum < sum) {
				start++;
			} else if (curSum > sum) {
				end--;
			} else {
				System.out.print(nums[start] + " " + nums[end]);
				break;
			}
		}
	}
````

### 和为S的连续正数序列

````java
public static void findContinuousSequence(int sum) {
		if (sum < 3) {
			return;
		}
		int small = 1;
		int big = 2;
    	int mid = (sum+1)/2;
		int curSum = small + big;
		while (small < mid) {
			if (curSum == sum) {
				printContinuousSequence(small, big);
			}
			while (curSum > sum && small < big) {
				curSum -= small;
				small++;
				if (curSum == sum) {
					printContinuousSequence(small, big);
				}
			}
			big++;
			curSum += big;
		}
	}

	public static void printContinuousSequence(int small, int big) {
		for (int i = small; i <= big; i++) {
			System.out.print(i + " ");
		}
		System.out.println();
	}
````

### 42.翻转单词顺序

````java
public static String reverseAll(String dataStr) {
		if (dataStr == null || dataStr.length() == 0) {
			return dataStr;
		}
		char[] dataArr = dataStr.toCharArray();
		int start = 0;
		int end = dataStr.length() - 1;
		reverseStr(dataArr, start, end);
		start = end = 0;
		while (start < dataStr.length()) {
			if (dataArr[start] == ' ') {
				start++;
				end++;
			} else if (end == dataStr.length() || dataArr[end] == ' ') {
				reverseStr(dataArr, start, --end);
				start = ++end;
			} else {
				end++;
			}
		}
		return new String(dataArr);
	}
	public static char[] reverseStr(char[] strArr, int start, int end) {
		if (strArr == null) {
			return null;
		}
		while (start < end) {
			char temp = strArr[start];
			strArr[start++] = strArr[end];
			strArr[end--] = temp;
		}
		return strArr;
	}
````

### 左旋转字符串

````java
public static char[] reverseStr(char[] strArr, int start, int end) {
		if (strArr == null) {
			return null;
		}
		while (start < end) {
			char temp = strArr[start];
			strArr[start++] = strArr[end];
			strArr[end--] = temp;
		}
		return strArr;
	}
	public static char[] leftRotateStr(char[] arr, int n) {
		if (arr == null || arr.length == 0) {  
            return null;  
        }  
        if (n <= 0 || n >= arr.length) {  
            return null;  
        }  
		int firstStart = 0;
		int firstEnd = n - 1;
		int secondStart = n;
		int secondEnd = arr.length - 1;
		reverseStr(arr, firstStart, firstEnd);
		reverseStr(arr, secondStart, secondEnd);
		reverseStr(arr, firstStart, secondEnd);
		return arr;
	}
````

### 44.扑克牌的顺子

````Java
public static Boolean isContinous(int[] arr) {
		if (arr == null || arr.length != 5) {
			return false;
		}
		int countOf0 = (arr[0] == 0 ? 1 : 0);
		int dis = 0;
		for (int i = 1; i < arr.length; i++) {
			if (arr[i] == 0) {
				countOf0++;
			}
			int t = arr[i];
			int j = 0;
			for (j = i - 1; j >= 0; j--) {
				if (t != 0 && t == arr[j]) {
					return false;
				} else {
					if (t > arr[j]) {
						break;
					} else {
						arr[j + 1] = arr[j];
					}
				}
			}
			arr[j + 1] = t;
		}
		for (int i = 0; i < arr.length - 1; i++) {
			if (arr[i] != 0) {
				dis += arr[i + 1] - arr[i] - 1;
			}
		}
		if (dis<=countOf0) {
			return true;
		}else {
			return false;
		}
	}

public boolean isContinuous(int[] numbers) {  
  
        if (numbers == null || numbers.length != 5) {  
  
            return false;  
  
        }  
        // 做三件事  
        // 1.对数组进行排序  
        // 2.统计数组中0的个数  
        // 3.统计排序之后的数组中相邻数字之间的空缺总数  
        // 1.对数组进行排序  
        quickSort(numbers, numbers.length, 0, numbers.length - 1);  
        // 2.统计数组中0的个数  
        int numOf0 = 0;  
        for (int i = 0; i < numbers.length; i++) {  
            if (numbers[i] == 0) {  
                numOf0++;  
            }  
        }  
        // 统计排序数组中两两之间的间隔数  
        // 定义一个指针，从数组的第一个非0元素开始  
        int numOfGap = 0;  
        // 指向前一个元素  
        int small = numOf0;  
        // 指向后一个元素  
        int big = numOf0 + 1;  
        // 在后一个元素不越界的情况下  
        while (big < numbers.length) {  
  
            if (numbers[small] == numbers[big]) {  
                // 一旦相邻两元素相等，肯定不是顺子  
                return false;  
            }  
  
            // 相邻两元素不等，现在肯定是big指向元素大于small指向元素  
            if (numbers[big] - numbers[small] == 1) {  
                // 此时两个元素正好相邻,两个指针后移  
                small++;  
                big++;  
            } else {  
                // 此时两个元素不相邻，中间隔的数>=1个  
                // 获取当前这两个数字之间间隔几个数  
                numOfGap = numbers[big] - numbers[small] - 1;  
                // 判断拥有的0是否能补救  
                if (numOfGap > numOf0) {  
                    // 所需间隔数>0的个数，无法补救,肯定不是顺子  
                    return false;  
                } else {  
                    // 此时numOfGap <= numOf0,当前情况下可以补救，则进行补救，将0的个数减少  
                    // 即当前情况用去numOfGap个0  
                    numOf0 = numOf0 - numOfGap;  
                    small++;  
                    big++;  
                }  
            }  
  
        }  
        return true;  
    }  
````

### 45.圆圈中最后剩下的数字

````java
//每次删除一个数字需要m步，总共n个数字，时间复杂度O(mn)
//用链表模拟圆圈，空间复杂度是O(n)
public static int lastRemaining(int n, int m) {
		if (n < 1 || m < 1) {
			return -1;
		}
		List<Integer> list = new ArrayList<>();
		for (int i = 0; i < n; i++) {
			list.add(i);
		}
		int idx = 0;
		while (list.size() > 1) {
          	//找到要删除的位置
			for (int i = 1; i < m; i++) {
				idx = (idx + 1) % list.size();
			}
			list.remove(idx);
          	//移动到最后一个的时候，将指针指向头部
          	if (idx == list.size()) {
              idx = 0;
          	}
		}
		return list.get(0);
	}
	public static int lastRemaining2(int n, int m) {
		if (n < 1 || m < 1) {
			return -1;
		}
		int last = 0;
		for (int i = 2; i <= n; i++) {
			last = (last + m) % i;
		}
		return last;
	}
````

### 46.求 1+2+3+…+n

````java
public static int sum_solution(int n) {
	int sum = n;
	boolean ans = (n > 0) && ((sum += sum_solution(n - 1)) > 0);
	return sum;
}

static int sum = 0;

public static boolean sum_solution2(int n) {
	sum += n;
	return (n > 0) && sum_solution2(n - 1);
}
````

### 47.不用加减乘除做加法

````java
public static int add(int n1, int n2) {
		int sum = 0;
		int carry = 0;
		do {
			sum = n1 ^ n2;
			carry = (n1 & n2) << 1;
			n1 = sum;
			n2 = carry;
		} while (carry != 0);
		return n1;
}
````

### 49.字符串转成整数

1. 判空
2. 判断符号正负
3. 判断转换后的数字是否溢出
4. 判断字符中是否存在不是数字的数
5. 删除字符串最前面的空格

````java
public static int getStrToInt(String str) {
		char[] arr = str.toCharArray();
		int n = 0;
		if (str.equals("") || str.equals(null)) {// 判断输入是否为空
			return 0;
		}
		int i = 0;
		while (arr[i] == ' ') {// 处理字符串首位的空格
			i++;
		}
		int sign = 1;// 用于判断输入字符串数字的正负，为1表示为正数
		if (arr[i] == '+' || arr[i] == '-') {
			if (arr[i] == '-') {
				sign = -1;
			}
			i++;
		}
		while (i < arr.length && Character.isDigit(arr[i])) {
			int c = arr[i] - '0';
			if (sign > 0 && (n > Integer.MAX_VALUE / 10 || (n == Integer.MAX_VALUE / 10 && c > Integer.MAX_VALUE % 10))) {
				n = Integer.MAX_VALUE;
				break;
			}
			if (sign < 0 && (n + Integer.MIN_VALUE / 10 > 0 || (n + Integer.MIN_VALUE / 10 == 0 && c + Integer.MAX_VALUE % 10 > 0))) {
				n = Integer.MIN_VALUE;
				break;
			}
			n = n * 10 + c;
			i++;
		}
		return sign > 0 ? n : -n;
	}
````

### 56. 链表中环的入口点

````java
public class Solution {
        /**
         * 两个指针，一个指针步长为1，一个步长为2；
         * 先计算两个指针相交的位置点；
         * 然后让一个指针指向头结点，步长都为1，往后走，其相遇点就是入口点
         * 该规律可通过公式推导得出
         */
        public ListNode EntryNodeOfLoop(ListNode pHead){
            ListNode targetNode = new ListNode(3);
            if (pHead == null || pHead.next == null) {
                return null;
            }
            ListNode preNode = pHead.next;
            ListNode postNode = pHead.next.next;
            // 找到相遇点
            while (preNode != postNode) {
                preNode = preNode.next;
                postNode = postNode.next.next;
            }
            // 将其中一个指针指向头结点
            postNode = pHead;
            // 步长都为1，同时往后走，直到两者相遇
            // 相遇点就是入口
            while (preNode != postNode) {
                preNode = preNode.next;
                postNode = postNode.next;
            }
            targetNode = preNode;
            return targetNode;
        }
    }
````

### 删除链表中重复的节点

````java
public static ListNode deleteDuplication(ListNode head) {
		if (head == null || head.node_next == null) {
			return head;
		}
		ListNode current = head.node_next;
		// 如果头节点是重复元素
		if (head.val == current.val) {
			current = current.node_next;
			while (current != null && current.val == head.val) {
				current = current.node_next;
			}
			head = current;
			return deleteDuplication(current);
		} else {
			head.node_next = deleteDuplication(current);
			return head;
		}
	}

	public static ListNode deleteDuplication2(ListNode head) {
		if (head == null || head.node_next == null) {
			return head;
		}
		ListNode preNode = null;
		ListNode node = head;
		while (node != null) {
			ListNode next = node.node_next;
			boolean needDel = false;
			if (next!=null && next.val == node.val) {
				needDel = true;
			}
			if (!needDel) {
				preNode = node;
				node = node.node_next;
			}else {
				int value = node.val;
				ListNode tobeDel = node;
				while (tobeDel !=null && tobeDel.val == value) {
					next = tobeDel.node_next;
					tobeDel = next;
				}
				if (preNode == null) {
					head = next;
				}else {
					preNode.node_next = next;
				}
				node = next;
			}
		}
		return head;
	}
````

### 对称的二叉树

````java
public static boolean isSymmetrical(TreeNode root1,TreeNode root2) {
		if (root1==null && root2==null) {
			return true;
		}
		if (root1==null || root2==null) {
			return false;
		}
		if (root1.val != root1.val) {
			return false;
		}
		return isSymmetrical(root1.left, root2.right) && isSymmetrical(root1.right, root2.left);
	}
````

### 二叉树打印成多行

`````java
public static void levelIterator(TreeNode root) {
		if (root == null) {
			return;
		}
		LinkedList<TreeNode> queue = new LinkedList<>();
		TreeNode curNode = null;
		queue.add(root);
		int inCount = 1;
		int outCount = 0;
		while (!queue.isEmpty()) {
			curNode = queue.remove();
			System.out.print(curNode.val + " ");
			outCount++;
			if (curNode.left != null) {
				queue.add(curNode.left);
			}
			if (curNode.right != null) {
				queue.add(curNode.right);
			}
			if (inCount == outCount) {
				System.out.println();
				inCount = queue.size();
				outCount = 0;
			}
		}
	}
`````

### 按之字形打印二叉树

````java
public static void printTreeNode(TreeNode root) {
		if (root == null) {return;}
		LinkedList<TreeNode> current = new LinkedList<>();
		LinkedList<TreeNode> reverse = new LinkedList<>();
		int flag = 0;
		TreeNode node;
		current.add(root);
		while (current.size()>0) {
			node = current.removeLast();
			System.out.print(node.val+" ");
			if (flag == 0) {
				if (node.left != null) {
					reverse.add(node.left);}
				if (node.right!=null) {
					reverse.add(node.right);}
			}else {
				if (node.right != null) {
					reverse.add(node.right);}
				if (node.left!=null) {
					reverse.add(node.left);}
			}
			if (current.size() == 0) {
				flag = 1-flag;
				LinkedList<TreeNode> temp = current;
				current = reverse;
				reverse = temp;
				System.out.println();
			}
		}
	}
````

### 63.二叉搜索树的第k个节点

````java
TreeNode KthNode(TreeNode pRoot, int k)  
        {  
            if(pRoot == null || k <= 0)  
                return null;  
            TreeNode current = pRoot;  
            TreeNode kNode = null;//用来保存第K个节点  
            int count = 0;  
            LinkedList<TreeNode> stack = new LinkedList<TreeNode>();  
            //中序遍历二叉搜索树  
            while(!stack.isEmpty() || current != null)  
           {  
              while(current!=null)  
             {  
                stack.push(current);  
                current = current.left;  
             }  
              if(!stack.isEmpty())  
              {  
                  current = stack.pop();
                  //每出栈一个节点让count++;对比count和K的值，第k个节点时跳出循环返回节点，  
                  count++;  
                  if(count == k)  
                  {  
                      kNode = current;  
                      break;  
                  }  
                  current = current.right;  
              }  
           }  
            return kNode;  
        } 
````

[股票买卖5个变种](https://segmentfault.com/a/1190000006672807)

我们有一个股票的数组，数组是每时间的钱，我们只能买入一次和卖出一次，求我们的最大收益。

我们知道了一个数组，那么我们可以在低价买入，然后高价卖出，但是需要知道我们的低价需要在高价之前。

我们可以两个变量，一个记录最低价，一个记录我们卖出得到最大钱。

````java
public int maxProfit(int[] prices) {
        //存储最大值的变量,初始化为0
        int ans = 0;
        //判断是否有效价格序列
        if (prices.length == 0) {
            return ans;
        }
        //判断应该在哪一日买入,即最小值
        int bought = prices[0];
        //遍历所有交易日价格
        for (int i = 1; i < prices.length; i++) {
            //判断本日是否能够卖出
            if (prices[i] > bought) {
                //判断如果本日卖出收益是否最大
                if (ans < (prices[i] - bought)) {
                    ans = prices[i] - bought;
                }
            } else {
                //判断本日是否为最低价格
                bought = prices[i];
            }
        }
        return ans;
    }
````

### 二分查找

````java
public static int binSearch(int srcArray[], int key) {  
        int mid;  
        int start = 0;  
        int end = srcArray.length - 1;  
        while (start <= end) {  
            mid = (end - start) / 2 + start;  
            if (key < srcArray[mid]) {  
                end = mid - 1;  
            } else if (key > srcArray[mid]) {  
                start = mid + 1;  
            } else {  
                return mid;  
            }  
        }  
        return -1;  
    }  
````

