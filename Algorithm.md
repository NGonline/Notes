[TOC]

## 递增二维数组的查找(剑指P38)
二维数组中,每一行都从左到右递增,每一列都从上到下递增.完成一个函数,输入这样的二维数组和一个整数,判断数组中是否含有该整数

从右上角数字开始，若大于则剔除该列，小于则剔除改行，不断缩小
```
bool Find(int* matrix, int rows, int columns, int number)
{
	bool found = false;
	if(matrix!=NULL && rows>0 && columns>0){
		int row = 0;
		int column = columns-1;
		while(row<rows and column>=0){
			if(matrix[row*columns+column] == number){
				found = true;
				break;
			}
			else if(matrix[row*columns+column] > number)
				--column;
			else
				++row;
		}
	}
	return found;
}
```
测试用例：包含查找数字，不包含查找数字，特殊输入（空指针）


## 替换字符串中的空格(剑指P44)
函数,将字符串中的每个空格替换成”%20”

替换后字符串变长,必须创建新的空间.注意凡是数组和指针都要考虑NULL的情况,赋值时从后往前简化操作
```
void ReplaceBlank(char string[],int length)	// length为最大容量
{
	if(NULL==string || length<=0)
		return;
	int originalLength = 0;
	int numberOfBlank = 0;
	int i = 0;
	while(string[i] != ‘\0’){
		++originalLength;
		if(string[i] == ‘ ’)
			++numberOfBlank;
		++i;
	}
	int newLength = originalLength+numberOfBlank*2;
	if(newLength > length)
		return;
	int indexOfOriginal = originalLength;
	int indexOfNew = newLength;
	while(indexOfOriginal>=0 && indexOfNew>indexOfOriginal){
		if(string[indexOfOriginal] == ‘ ’){
			string[indexOfNew--] = ‘0’;
			string[indexOfNew--] = ‘2’;
			string[indexOfNew--] = ‘%’;
		}
		else
			string[indexOfNew--] = string[indexOfOriginal];
		--indexOfOriginal;
	}
}
```
测试用例：包含空格（字符串前中后），没有空格，特殊输入（NULL、空字符串、只有一个空格、只有若干空格）


## 已排序数组插入另一个(剑指P48)
两个已排序数组A1和A2,内存在A1的末尾有足够的空余空间容纳A2.请实现一个函数，把A2中的所有数字插入到A1中且所有数字排序

从后往前复制较大的数字避免重复移动多次


## 从未到头打印链表(剑指P51)
从尾到头打印链表，结点定义如下:
```
struct ListNode
{
	int m_nKey;
	ListNode* m_pNext;
}
```
栈实现：
```
void PrintListReversingly(ListNode* pHead)
{
	std::stack<ListNode*> nodes;
	ListNode* pNode = pHead;
	while(pNode != NULL){
		nodes.push(pNode);
		pNode = pNode->m_pNext;
	}
	while(!nodes.empty()){
		pNode = nodes.top();
		printf(“%d\t”, pNode->m_nValue);
		nodes.pop();
	}
}
```
递归实现，可能导致栈溢出：
```
void PrintListReversingly(ListNode* pHead)
{
	if(pHead != NULL){
		PrintListReversingly(pHead->m_pNext);
		printf(“%d\t”, pHead->m_nValue);
	}
}
```
测试用例：功能测试（多个结点，一个结点），特殊输入（头结点指针为NULL）
 
前序和中序重建二叉树(剑指P55)
从无重复数字的前序和中序遍历中重建二叉树，结点定义如下：
struct BinaryTreeNode
{
	int m_nValue;
	BinaryTreeNode *m_pLeft,*m_pRight;
}
 
前序遍历的第一个结点一定是根
BinaryTreeNode* Construct(int* preorder,int* inorder,int length)
{
	if(NULL==preorder || NULL==inorder || length<=0)
		return NULL;
	return ConstructCore(preorder,preorder+length-1,inorder,inorder+length-1);
}

BinaryTreeNode* ConstructCore(int* startPreorder,int *endPreorder,
int* startInorder,int* endInorder)
{
	int rootValue = startPreorder[0];
	BinaryTreeNode* root = new BinaryTreeNode();
	root->m_nValue = rootValue;
	root->m_pLeft = root->m_pRight = NULL;
	if(startPreorder == endPreorder){
		if(startInorder==endInorder && 
			*startPreorder==*startInorder)
			return root;
		else
			throw std::exception(“Invalid input.”);
	}
	int *rootInorder = startInorder;
	while(rootInorder<=endInorder && *rootInorder!=rootValue)
		++rootInorder;
	if(rootInorder==endInorder && *rootInorder!=rootValue)
		throw std::exception(“Invalid input.”);
	int leftLength = rootInorder-startInorder;
	int *leftPreorderEnd = startPreorder+leftLength;
	if(leftLength > 0)
		root->m_pLeft = ConstructCore(startPreorder+1,
leftPreorderEnd,startInorder,rootInorder-1);
	if(leftLength < endPreorder-startPreorder)
		root->m_pRight = ConstructCore(leftPreorderEnd+1,
endPreorder,rootInorder+1,endInorder);
	return root;
}
测试用例：普通二叉树（完全、非完全），特殊二叉树（都只有一个儿子、只有一个结点），特殊输入（根节点为NULL，输入的前序和中序遍历不匹配）
 
两个栈实现队列(剑指P59)
两个栈实现队列，队列声明如下：
template <typename T> class CQueue
{
public:
	CQueue(void);
	~CQueue(void);
	void appendTail(const T& node);
	T deleteHead();
private:
	stack<T> stack1,stack2;
}
 
当stack2中为队列的前几个元素，只有当其空时才需要将stack1的加入其中
template<typename T> void CQueue<T>::appendTail(const T& element)
{
	stack1.push(element);
}
template<typename T> T CQueue<T>::deleteHead()
{
	if(stack2.size() <= 0){
		while(stack1.size() > 0){
			T& data = stack1.top();
			stack1.pop();
			stack2.push(data);
		}
	}
	if(stack2.size() == 0)
		throw exception(“queue is empty”);
	T head = stack2.top();
	stack2.pop();
	return head;
}
测试用例：往空队列里添加删除、往非空队列里添加删除、连续删除直到队列为空
 
随机partition
快排的随机partition
 
int Partition(int data[],int length,int start,int end)
{
	if(NULL==data || length<=0 || start<0 || end>=length)
		throw new std::exception(“Invalid Parameters”);
	int index = RandomInRange(start,end);
	Swap(&data[index],&data[end]);
	int small = start-1;
	for(index=start;index<end;++index){
		if(data[index] < data[end]){
			++small;
			if(small != index)
				Swap(&data[index],&data[small]);
		}
	}
	Swap(&data[++small],&data[end]);
	return small;
}
 
循环后的排序数列找最值(剑指P66)
输入为一个递增排序数组循环左移后的结果，要求返回其最小元素
 
二分查找，第一个元素肯定大于等于最后一个元素，注意有相同元素（即头尾相等）的情况
int Min(int* numbers, int length)
{
	if(NULL==numbers || length<=0)
		throw std::exception("Invalid parameters");
	int index1 = 0;
	int index2 = length-1;
	int indexMid = index1;
	while(numbers[index1] >= numbers[index2]){
		if(index2-index1 == 1){
			indexMid = index2;
			break;
		}
		indexMid = (index1+index2)/2;
		// 若三个数字都相等则只能顺序查找
		if(numbers[indexMid]==numbers[index1] && 
numbers[index1]==numbers[index2])
			return MinInOrder(numbers,index1,index2);
		if(numbers[indexMid] >= numbers[index1])
			index1 = indexMid;
		else if(numbers[indexMid] <= numbers[index2])
			index2 = indexMid;
	}
	return numbers[indexMid];
}
int MinInOrder(int* numbers,int index1,int index2)
{
	int result = numbers[index1];
	for(int i=index1+1; i<=index2; ++i)
		if(result > numbers[i])
			resutl = numbers[i];
	return result;
}
测试用例：功能测试（有重复、无重复），边界测试（无移位、只包含一个数字），特殊输入（NULL指针）
 
走楼梯(剑指P73)
一次可以向上走一级或者两级，问走一个n级的台阶共有多少种不同的走法
 
斐波那契数列。递归树种重复极多，效率很低
long long Fibonacci(unsigned int n)
{
	if(n <= 0)
		return 0;
	if(n == 1)
		return 1;
	return Fibonacci(n-1)+Fibonacci(n-2);
}
循环实现，O(n)
long long Fibonacci(unsigned n)
{
	int result[2] = {0,1};
	if(n < 2)
		return result[n];
	long long fibNMinusOne = 1;
	long long fibNMinusTwo = 0;
	long long fibN = 0;
	for(unsigned int i=2; i<=n; ++i){
		fibN = fibNMinusOne+fibNMinusTwo;
		fibNMinusTwo = fibNMinusOne;
		fibNMinusOne = fibN;
	}
	return fibN;
}
O(logN)的解法： [fib(N) fib(N-1); fib(N-1) fib(N-2)] = [1 1; 1 0]^(n-1)
 
2x1小方块的覆盖(剑指P76)
用8个2x1的小矩形无重叠地覆盖一个2x8的大矩阵共有多少种方法
 
斐波那契数列。同上
 
二进制表示1的个数(剑指P78)
输入一个整数，返回其二进制表示中1的个数
 
注意位移比除法效率高得多，注意负数时，若用右移解决会死循环
常规解法：
int NumberOfOne(int n)
{
	int count = 0;
	unsigned int flag = 1;
	while(flag){
		if(n & flag)
			count++;
		flag = flag<<1;
	}
	return count;
}
高端解法：
int NumberOfOne(int n)
{
	int count = 0;
	while(n){
		count++;
		n = (n-1)&n;
	}
	return count;
}
 
判断是否为2的幂(剑指P82)
一条语句判断整数是否是2的幂
 
同上高端解法
 
两个二进制数几位不同(剑指P82)
输入两个整数m和n，计算二进制表示有几位不同
 
异或后计算1的个数，同上上
 
打印1到n(剑指P94)
输入数字n，打印1到最大的n位十进制数，即10^n-1
 
注意陷阱，是个大数问题，应该用字符串表示。注意递归简化代码，以及终止的判别效率，以及打印符合阅读习惯
void PrintOneToMaxOfNDigits(int n)
{
	if(n <= 0)
		return;
	char *number = new char[n+1];
	memset(number, ‘0’,n);
	number[n] = ‘\0’;
	while(!Increment(number));
		PrintNumber(number);
	delete []number;
}
bool Increment(char* number)
{
	bool isOverflow = false;
	int nTakeOver = 0;
	int nLength = strlen(number);
	int nSum;
	for(int i=nLength-1; i>=0; i--){
		nSum = number[i]-‘0’+nTakeOver;
		nTakeOver = 0;
		if(i == nLength-1)
			nSum++;
		if(nSum >= 10){
			if(0 == i)
				isOverflow = true;
			else{
				nSum -= 10;
				nTakeOver = 1;
				number[i] = ‘0’+nSum;
}
}
else{
		number[i] = ‘0’+nSum;
		break;
}
}
return isOverflow;
}
void PrintNumber(char* number)
{
	bool isBeginning = true;
	int nLength = strlen(number);
	for(int i=0; i<nLength; ++i){
		if（isBeginning && number[i]!= ‘0’）
			isBeginning = false;
		if(!isBeginning)
			printf(“%c”,number[i]);
}
}
 
删除链表结点(剑指P99)
给定单链表的头指针和一个结点指针，要求O(1)时间删除该结点,结点定义如下
struct ListNode
{
	int m_nValue;
	ListNode* m_pNext;
}
 
得到上一个结点是困难的，通过复制后一个结点达到O(1)，注意指针删除后置NULL
void DeleteNode(ListNode** pListHead, ListNode* pToBeDeleted)
{
	if(NULL==pListHead || NULL==pToBeDeleted)
		return;
	ListNode* pNext = pToBeDeleted->m_pNext;
	if(pNext != NULL){
		pToBeDeleted->m_nValue = pNext->m_nValue;
		pToBeDeleted->m_pNext = pNext->m_pNext;
		delete pNext;
		pNext = NULL;
	}
	else if(*pListHead == pToBeDeleted){
		delete pToBeDeleted;
		pToBeDeleted = NULL;
		*pListHead = NULL;
	}
	else{	// 是尾结点的话，删除后前一个结点的next没有置NULL
		ListNode* pNode = *pListHead;
		while(pNode->m_pNext != pToBeDeleted)
			pNode = pNode->m_pNext;
		pNode->m_pNext = NULL;
		delete pToBeDeleted;
		pToBeDeleted = NULL;
	}
}
测试用例：功能测试（多结点删除中间、多结点删除尾结点、单结点、空链表），特殊输入（各种输入NULL）
 
奇数移到偶数前(剑指P102)
调整整数数组顺序，使得奇数位于偶数前面
 
考虑可扩展性
void Reorder(int *pData, unsigned int length, bool (*func)(int))
{
	if(NULL==pData || 0==length)
		return;
	int *pBegin = pData;
	int *pEnd = pData+length-1;
	while(pBegin < pEnd){
		while(pBegin<pEnd && !func(*pBegin))
			pBegin++;
		while(pBegin<pEnd && func(*pEnd))
			pEnd--;
		if(pBegin < pEnd)
			Swap(pBegin,pEnd);
	}
}
bool isEven(int n){ return (n&1)==0; }
void ReorderOddEven(int *pData, unsigned int length)
{
	Reorder(pData, length, isEven);
}
测试用例：奇偶交替，奇数在前，偶数在前，指针为NULL，数组只包含一个数字
 
输出链表倒数第k个(剑指P107)
输出链表的倒数第k个结点，结点定义同17
 
注意鲁棒性（NULL，长度不够，无符号数），只遍历一次
ListNode* FindKthToTail(ListNode* pListHead, unsigned int k)
{
	if(NULL==pListHead || 0==k)
		return NULL;
	ListNode* pAhead = pListHead;
	ListNode* pBehind = NULL;
	for(unsigned int i=0; i<k-1; ++i){
		if(pAhead->m_pNext != NULL
			pAhead = pAhead->m_pNext;
		else
			return NULL;
	}
	pBehind = pListHead;
	while(pAhead->m_pNext != NULL){
		pAhead = pAhead->m_pNext;
		pBehind = pBehind->m_pNext;
	}
	return pBehind;
}
测试用例：功能测试（k在中间、头结点、尾结点），特殊输入（NULL，小于k个，k=0）
 
求链表中点(剑指P111)
求链表中点，判断单链表是否为环
 
同上
 
链表反向(剑指P112)
反转链表，结点定义同17
 
ListNode* ReverseList(ListNode* pHead)
{
	ListNode* pReverseHead = NULL;
	ListNode* pNode = pHead;
	ListNode* pPrev = NULL;
	while(pNode != NULL){
		ListNode* pNext = pNode->m_pNext;
		if(pNode != NULL)
			pReversedHead = pNode;
		pNode->m_pNext = pPrev;
		pPrev = pNode;
		pNode = pNext;
	}
	return pReverseHead;
}
测试用例：空链表，只有一个结点，有多个结点
 
合并排序链表(剑指P114)
合并两个排序的链表，结点定义同17
 
ListNode* Merge(ListNode* pHead1, ListNode* pHead2)
{
	if(NULL == pHead1)
		return pHead2;
	else if(NULL == pHead2)
		return pHead1;
	ListNode* pMergedHead = NULL;
	if(pHead1->m_nValue < pHead2->m_nValue){
		pMergedHead = pHead1;
		pMergedHead->m_pNext = Merge(pHead1->m_pNext,pHead2);
	}
	else{
		pMergedHead = pHead2;
		pMergedHead->m_pNext = Merge(pHead1,pHead2->m_pNext);
	}
	return pMergedHead;
}
 
判断二叉树是否为子结构(剑指P117)
输入两棵二叉树A和B，判断B是否为A的子结构。结点定义如5
 
bool HasSubtree(BinaryTreeNode* pRoot1, BinaryTreeNode* pRoot2)
{
	bool result = false;
	if(pRoot1!=NULL && pRoot2!=NULL){
		if(pRoot1->m_nValue == pRoot2->m_nValue)
			result = IsTree2OnTopOfTree1(pRoot1,pRoot2);
		if(!result)
			result = HasSubtree(pRoot1->m_pLeft,pRoot2);
		if(!result)
			result = HasSubtree(pRoot1->m_pRight,pRoot2);
	}
	return result;
}
bool IsTree2OnTopOfTree1(BinaryTreeNode* pRoot1,
BinaryTreeNode* pRoot2)
{
	if(NULL == pRoot2)
		return true;
	if(NULL == pRoot1)
		return false;
	if(pRoot1->m_nValue != pRoot2->m_nValue)
		return false;
	return IsTree2OnTopOfTree1(pRoot1->m_pLeft,pRoot2->m_pLeft) && 
		   IsTree2OnTopOfTree1(pRoot1->m_pRight,pRoot2->m_pRight);
}
 
顺时针打印矩阵(剑指P128)
输入一个矩阵，从左上角开始顺时针从外到里打印每一个数字
 
void PrintMatrixClockwise(int **numbers, int columns, int rows)
{
	if(NULL==numbers || columns<=0 || rows<=0)
		return;
	int start = 0;
	while(columns>2*start && rows>2*start){	// 起点总是(start,start)
		PrintMatrixInCircle(numbers,columns,rows,start);
		++start;
	}
}
void PrintMatrixInCircle(int **numbers, int columns, int rows, int start)
{
	int endX = columns-start-1;
	int endY = rows-start-1;
	int i,number;
	for(i=start; i<=endX; ++i)	// 左到右
		printNumber(numbers[start][i]);
	if(start < endY)	// 上到下
		for(i=start+1; i<=endY; ++i)
			printNumber(numbers[i][endX]);
	if(start<endX && start<endY)	//右到左
		for(i=endX-1; i>=start; --i)
			printNumber(numbers[endY][i]);
	if(start<endX && start<endY-1)	// 从下到上
		for(i=endY-1; i>=start+1; --i)
			printNumber(numbers[i][start]);
}
 
能输出最小值的栈(剑指P132)
定义栈的数据结构，使min,push,pop的时间复杂度都是O(1)
 
仅用一个变量记录是不行的，因为弹出之后就无法更新了，需要一个辅助栈m_min记录每个位置的最小值
template <typename T>
void StackWithMin<T>::push(const T& value)
{
	m_data.push(value);
	if(m_min.size()==0 || value<m_min.top())
		m_min.push(value);
	else
		m_min.push(m_min.top());
}
template <typename T>
void StackWithMin<T>::pop()
{
	assert(m_data.size()>0 && m_min.size()>0);
	m_data.pop();
	m_min.pop();
}
template <typename T>
const T& StackWithMin<T>::min() const	// 返回为右值
{
	assert(m_data.size()>0 && m_min.size()>0);
	return m_min.top();
}
 
判断是否为弹栈序列(剑指P134)
输入两个整数序列，第一个序列为栈的压入顺序，判断第二个是否可能为第一个的弹出序列
 
bool IsPopOrder(const int* pPush, const int* pPop, int nLength)
{
	bool bPossible = false;
	const int *pNextPush = pPush;
	const int *pNextPop = pPop;
	std::stack<int> stackData;
	if(pPush!=NULL && pPop!=NULL && nLength>0){
		while(pNextPop-pPop < nLength){
			while(stackData.empty() || stackData.top() != *pNextPop){
				if(pNextPush-pPush == nLength)
					break;
				stackData.push(*pNextPush);
				pNextPush++;
			}
			if(stackData.top() != *pNextPop)
				break;
			stackData.pop();
			pNextPop++;
		}
		if(stackData.empty() && pNextPop-pPop==nLength)
			bPossoble = true;
	}
	return bPossible;
}
 
按行遍历二叉树(剑指P137)
一行一行依次输出二叉树结点
 
void PrintFromTopToBottom(BinaryTreeNode* pTreeRoot)
{
	if(NULL == pTreeRoot)
		return;
	std::queue<BinaryTreeNode*> nodeQueue;
	nodeQueue.push_back(pTreeRoot);
	while(nodeQueue.size() > 0){
		BinaryTreeNode *pNode = dequeTreeNode.front();
		nodeQueue.pop_front();
		printf("%d ",pNode->m_nValue);
		if(pNode->m_pLeft)
			nodeQueue.push_back(pNode->m_pLeft);
		if(pNode->m_pRight)
			nodeQueue.push_back(pNode->m_pRight;
	}
}
 
判断是否为后序遍历(剑指P140)
判断一个整数数组是否为某个二叉搜索树的后序遍历结果
 
bool VerifyBST(int seq[], int length)
{
	if(NULL==seq || length<=0)
		return false;
	int root = seq[length-1];
	int i = 0;
	for(; i<length-1; ++i)
		if(seq[i] > root)
			break;
	int j = i;
	for(; j<length-1; ++j)
		if(seq[j] < root)
			return false;
	bool left = true;
	if(i > 0)
		left = VerifyBST(seq,i);
	bool right = true;
	if(i < length-1)
		right = VerifyBST(seq+i,length-i-1);
	return (left&&right);
}
测试用例：功能测试（完全二叉树、只有左或右结点的二叉树、只有一个结点的二叉树、无对应的二叉树），特殊输入（NULL）
 
二叉树中和为某数的路径(剑指P143)
输入一颗二叉树和一个整数，打印所有和为该整数的路径（到叶节点的才算）
 
需要记录路径，注意回溯时对路径的还原
void FindPath(BinaryTreeNode* pRoot, int expectedSum)
{
	if(NULL == pRoot)
		return;
	std::vector<int> path;
	int currentSum = 0;
	FindPath(pRoot,expectedSum,currentSum,path);
}
void FindPath(BinaryTreeNode* pRoot, int expectedSum, std::vector<int>& path,)
{
	path.push_back(pRoot->m_nValue);
	bool isLeaf = (pRoot->m_pLeft==NULL && pRoot->m_pRight==NULL);
	if(expectedSum==pRoot->m_nValue && isLeaf){
		printf("A path is found: ");
		std::vector<int>::iterator iter = path.begin();
		for(; iter!=path.end(); ++iter)
			printf("%d\t",*iter);
		printf("\n");
	}
	if(pRoot->m_pLeft != NULL)
		FindPath(pRoot->m_pLeft, expectedSum-pRoot->m_nValue, path);
	if(pRoot->m_pRight != NULL)
		FindPath(pRoot->m_pRight, expectedSum-pRoot->m_nValue, path);
	path.pop_back();
}
 
复杂链表的复制(剑指P147)
实现如下复杂链表的复制，m_pSibling指向链表中的任意结点或者NULL
struct ComplexListNode
{
	int m_nValue;
	ComplexListNode *m_pNext,*m_pSibling;
}
 
直观的方法是后加sibling，平方时间或者平方空间。此处用分步的方法，将新链表结点建在输入对应结点之后，嵌在输入之中，时间O(n)且无额外空间
ComplexListNode* Clone(ComplexLIstNode* pHead)
{
	ClonedNodes(pHead);
	ConnectSiblingNodes(pHead);
	return ReconnectNodes(pHead);
}
void CloneNodes(ComplexListNode* pHead)
{
	ComplexListNode* pNode = pHead;
	while(pNode != NULL)
	{
		ComplexListNode* pCloned = new ComplexListNOde();
		pCloned->m_nValue = pNode->m_nValue;
		pCloned->m_pNext = pNode->m_pNext;
		pCloned->m_pSibling = NULL;
		pNode->m_pNext = pCloned;
		pNode = pCloned->m_pNext;
	}
}
void ConnectSiblingNodes(ComplexListNode* pHead)
{
	ComplexListNode* pNode = pHead;
	while(pNode != NULL){
		ComplexListNode* pCloned = pNode->m_pNext;
		if(pNode->m_pSibling != NULL)
			pCloned->m_pSibling = pNode->m_pSibling->m_pNext;
		pNode = pCloned->m_pNext;
	}
}
ComplexLIstNode* ReconnectNodes(ComplexListNode* pHead)
{
	ComplexListNode* pNode = pHead;
	ComplexListNode* pClonedHead = NULL;
	ComplexListNode* pClonedNode = NULL;
	if(pNode != NULL){
		pClonedHead = pClonedNode = pNode->m_pNext;
		pNode->m_pNext = pClonedNode->m_pNext;
		pNode = pNode->m_pNext;
	}
	while(pNode != NULL){
		pClonedNode->m_pNext = pNode->m_pNext;
		pClonedNode = pCLonedNode->m_pNext;
		pNode->m_pNext = pClonedNode->m_pNext;
		pNode = pNode->m_pNext;
	}
	return pClonedHead;
}
测试用例：功能测试（sibling指向自身，两个sibling形成环，只有一个结点。。。），特殊输入
 
二叉搜索树变链表(剑指P151)
将二叉搜索树转换为排序的双向链表，要求不能创建任何新的结点，只能调整已有结点的指针
 
用中序遍历。前序的话，函数必须要返回头和尾，中序只需要尾就可以了
BinaryTreeNode* Convert(BinaryTreeNode* pRootOfTree)
{
	BinaryTreeNode *pLastNodeInList = NULL;
	ConvertNode(pRootOfTree, &pLastNodeInList);
	BinaryTreeNode *pHeadOfList = pLastNodeInList;
	while(pHeadOfList!=NULL && pHeadOfList->m_pLeft!=NULL)	// 回到头结点
		pHeadOfLIst = pHeadOfList->m_pLeft;
	return pHeadOfList;
}
void ConvertNode(BinaryTreeNode* pNode, BinaryTreeNode** pLastNodeInList)
{
	if(NULL == pNode)
		return;
	BinaryTreeNode *pCurrent = pNode;
	if(pCurrent->m_pLeft != NULL)
		ConvertNode(pCurrent->m_pLeft,pLastNodeInList);
	pCurrent->m_pLeft = *pLastNodeInList;
	if(*pLastNodeInList != NULL)
		(*pLastNodeInList)->m_pRight = pCurrent;
	*pLastNodeInList = pCurrent;
	if(pCurrent->m_pRight != NULL)
		ConvertNode(pCurrent->m_pRight,pLastNodeInList);
}
 
全排列(剑指P154)
输入一个字符串，打印该字符串中字符的所有排列
 
void Permutation(char* pStr)
{
	if(NULL == pStr)
		return;
	Permutation(pStr,pStr);
}
void Permutation(char* pStr, char* pBegin)
{
	if(*pBegin == '\0')
		printf("&s\n",pStr);
	else
		for(char* pCh=pBegin; *pCh!='\0'; ++pCh){
			Swap(pCh,pBegin);
			Permutation(pStr,pBegin+1);
			Swap(pCh,pBegin);
		}
}
 
全组合(剑指P156)
如上题，但是打印所有组合
 
也是分解为小问题，分为一个和剩余，分两种情况（取不取那一个）进行递归
 
找频度超过一半的数(剑指P163)
数组中有一个数字出现的次数超过了长度的一半，请找出这个数字
 
中位数一定为所求,所以可以基于partition，时间O(n)
int MoreThanHalfNum(int* numbers, int length)
{
	if(NULL==numbers || length<=0)
		throw std::exception("Invalid input!");
	int middle = length>>1;
	int start = 0;
	int end = length-1;
	int index = Partition(numbers, length, start, end);
	while(index != middle){
		if(index > middle){
			end = index-1;
			index = Partition(numbers, length, start, end);
		}
		else{
			start = index+1;
			index = Partition(numbers, length, start, end);
		]
	}
	return numbers[middle];
}
若不可以修改输入数组，用一个值-次数对来记录，若遇到一个等于值得则次数加1，否则减1，到零时更换值。因为结果的次数比其他的加起来还多，所以最终的值即为结果
int MoreThanHalfNum(int* numbers, int length)
{
	if(NULL==numbers || length<=0)
		throw std::exception("Invalid input!");
	int result = numbers[0];
	int times = 1;
	for(int i=1; i<length; ++i){
		if(0 == times){
			result = numbers[i];
			times = 1;
		}
		else if(numbers[i] == result)
			times++;
		else
			times--;
	}
	return result;
}
 
最小k个数(剑指P167)
输入n个整数，找出其中最小的k个数
 
允许修改输入数组时，可用partition的方法，O(n)
void GetLeatNumbers(int* input, int n, int* output, int k)
{
	if(NULL==input || NULL==output || k>n || k<=0)
		return;
	int start = 0;
	int end = n-1;
	int index = Partition(input,n,start,k);
	while(index != k-1){
		if(index > k-1){
			end = index-1;
			index = Partition(input,n,start,end);
		}
		else{
			start = index+1;
			index = Partition(input,n,start,end);
		}
	}
	for(int i=0; i<k; ++i)
		output[i] = input[i];
}
用树存当前结果可以不修改原始数组，最大堆的实现较麻烦，如果可以的话，用红黑树(multiset)。时间O(nlogk)，且对大数据而言，可以避免一次读入内存。
typedef multiset<int,greater<int>> intSet;
typedef multiset<int,greater<int>>::iterator setIterator;
void GetLeastNumbers(const vector<int>& data, intSet& leastNumbers, int k)
{
	leastNumbers.clear();
	if(k<1 || data.size()<k)
		return;
	vector<int>::const_iterator iter = data.begin();
	for(; iter!=data.end(); ++iter){
		if(leastNumbers.size()<k)
			leastNumbers.insert(*iter);
		else
			setIterator iterGreatest = leastNumbers.begin();
			if(*iter < *leastNumbers.begin()){
				leastNumbers.erase(iterGreatest);
				leastNumbers.insert(*iter);
			}
	}
}
 
连续子数组的最大和
输入一个整数数组，求所有子数组的和的最大值
 
bool g_InvalidInput = false;
int FindGreatestSumOfSubarray(int *pData, int nLength)
{
	if(NULL==pData || nLength<=0){
		g_InvalidInput = true;
		return 0;
	}
	g_InvalidInput = false;
	int nCurrentSum = 0;
	int nGreatestSum = 0x80000000;
	for(int i=0; i<nLenght; ++i){
		if(nCurrentSum <= 0)
			nCurrentSum = pData[i];
		else
			nCurrentSum += pData[i];;
		if(nCurrentSum > nGreatestSum)
			nGreatestSum = nCurrentSum;
	}
	return nGreatestSum;
}
 
1出现的次数(剑指P174)
输入整数n，求1到n的十进制表示中1出现的次数
 
直观的O(NlogN)方法
int NumberOf1Between1AndN(unsigned int n){
	int number = 0;
	for(unsigned int i=1; i<=n; ++i)
		number += NumberOf1(i);
	return number;
}
int NumberOf1(unsigned int n){
	int number = 0;
	while(n){
		if(n%10 == 1)
			number++;
		n = n/10;
	}
	return number;
}
每次去掉最高位进行递归，O(logN)。转化为字符串是解决大数问题的直观方法
int NumberOf1Between1AndN(int n){
	if(n <= 0)
		return 0;
	char strN[50];
	sprintf(strN, "%d", n);
	return NumberOf1(strN);
}
int NumberOf1(const char* strN){
	if(!strN || *strN<'0' || *strN>'9' || *str=='\0')
		return 0;
	int first = *strN-'0';
	unsigned int length = static_cast<unsigned int>(strlen(strN));
	if(length==1 && first==0)
		return 0;
	if(length==1 && first>0)
		return 1;
	int numFirstDigit = 0;	// 第一位为1的数目
	if(first == 1)
		numFirstDigit = PowerBase10(length-1);
	else if(first == 1)
		numFirstDigit = atoi(strN+1)+1;
	// 除上下两种情况外的数目（一位为1，剩下0-9排列）
	int numOtherDigits = first*(length-1)*PowerBase10(length-2);
	int numRecursive = NumberOf1(strN+1);	// 去掉第一位的结果
	return numFirstDigit+numOtherDigits+numRecursive;
}
int PowerBase10(unsigned int n){
	int result = 1;
	for(unsigned int i=0; i<n; ++i)
		result *= 10;
	return result;
}
测试用例：功能测试（5,10,55,99等），边界值测试（0,1等），性能测试（较大数）
 
拼接整数的最小值(剑指P177)
输入正整数数组，将所有数字（不一定只有一位）拼接成一个数，打印最小值。如{3,32,321}结果为321323
 
大数问题用字符串。因为字符串的长度相同，所以只要用字符串的大小比较即可。效率同快排O(NlogN)
const int g_MaxNumberLength = 10;
char* g_StrCombine1 = new char[g_MaxNumberLength*2+1];
char* g_StrCombine2 = new char[g_MaxNumberLength*2+1];
void PrintMinNumber(int* numbers, int length)
{
	if(NULL==numbers || length<=0)
		return;
	char** strNumbers = (char**) (new int[length]);
	for(int i=0; i<length; ++i){
		strNumber[i] = new char[g_MaxNumberLength+1];
		sprintf(strNumbers[i],"&d",numbers[i]);
	}
	qsort(strNumbers,length,sizeof(char*),compare);
	for(int i=0; i<length; ++i)
		printf("%s",strNumbers[i]);
	printf("\n");
	for(int i=0; i<length; ++i)
		delete []strNumbers[i];
	delete []strNumbers;
}
int compare(const void* strNumber1, const void* strNumber2)
{
	strcpy(g_strCombine1, *(const char**) strNumber1);
	strcat(g_strCombine1, *(const char**) strNumber2);
	strcpy(g_strCombine2, *(const char**) strNumber2);
	strcat(g_strCombine2, *(const char**) strNumber1);
	return strcmp(g_StrCombine1,g_StrCombine2);
}
可用反证法证明
 
丑数
将只包含因子2、3和5的数称为丑数。求按从小到大顺序排列的第1500个丑数
 
用数组保存已找到的丑数，避免在非丑数上浪费时间，用空间换时间。数组中丑数按大小排列
int GetUglyNumber(int index)
{
	if(index <= 0)
		return 0;
	int *pUglyNumbers = new int[index];
	pUglyNumbers[0] = 1;
	int nextUglyIndex = 1;
	int *pMultiply2 = pUglyNumbers;
	int *pMultiply3 = pUglyNumbers;
	int *pMultiply5 = pUglyNumbers;
	while(nextUglyIndex < index){
		pUglyNumbers[nextUglyIndex] = Min(*pMultiply*2, *pMultiply3*3, *pMultiply5*5);
		while(*pMultiply2*2 <= pUglyNumbers[nextUglyIndex]
			++pMultiply2;
		while(*pMultiply3*3 <= pUglyNumbers[nextUglyIndex]
			++pMultiply3;
		while(*pMultiply5*5 <= pUglyNumbers[nextUglyIndex]
			++pMultiply5;
		++nextUglyIndex;
	}
	int ugly = pUglyNumbers[nextUglyIndex-1];
	delete []pUglyNumbers;
	return ugly;
}
 
第一个只出现一次的字符(剑指P186)
在字符串中找出第一个只出现一次的字符
 
char FirstNotRepeatingChar(chat* pString)
{
	if(NULL == pString)
		return '\0';
	const int tableSize = 256;
	unsigned int hashTable[tableSize];
	for(unsigned int i=0; i<tableSize; ++i)
		hashTable[i] = 0;
	char* pHashKey = pString;
	while(*pHashKey != '\0')
		hashTable[*pHashKey++]++;
	pHashKey = pString;
	while(*pHashKey != '\0'){
		if(hashTable[*pHashKey] == 1)
			return *pHashKey;
		pHashKey++;
	}
	return '\0';
}
 
删除某些字符(剑指P189)
输入两个字符串，从第一个字符串中删除第二个字符串中出现过的所有字符
 
同上,哈希
 
删除重复字符(剑指P189)
删除字符串中所有重复出现的字符
 
同上上，哈希
 
逆序对
输入一个数组，求数组中逆序对的总数
 
基于归并排序
int InversePairs(int *data, int length)
{
	if(NULL==data || length<=0)
		return 0;
	int *copy = new int[length];
	for(int i=0; i<length; ++i}
		copy[i] = data[i];
	int count = InversePairsCore(data,copy,0,length-1);
	delete []copy;
	return count;
}
int InversePairsCore(int* data, int* copy, int start, int end)
{
	if(start == end){
		copy[start] = data[start];
		return 0;
	}
	int length = (end-start)/2;
	int left = InversePairsCore(copy,data,start,start+length);
	int right = InversePairsCore(copy,data,start+length+1,end);
	int i = start+length;
	int j = end;
	int indexCopy = end;
	int count = 0;
	while(i>=start && j>=start+length+1){
		if{data[i] > data[j]){
			copy[indexCopy--] = data[i--];
			count += j-start-length;
		}
		else
			copy[indexCopy--] = data[j--];
	}
	for(; i>=start; --i)
		copy[indexCopy--] = data[i];
	for(; j>=start+length+1; --j)
		copy[indexCopy--] = data[j];
	return left+right+count;
}
 
两个链表的公共结点
求两个链表的第一个公共结点
 
有公共结点的话,一旦出现公共结点,之后的所有结点都相同.只遍历一遍的话需要辅助栈,所以应当先遍历一遍得到二者的长度差,再进行第二遍遍历,复杂度仍是线性
ListNode* FindFirstCommonNode(ListNode *pHead1, ListNode *pHead2)
{
	unsigned int nLength1 = GetListLength(pHead1);
	unsigned int nLength2 = GetListLength(pHead2);
	int nLengthDif = nLength1-nLength2;
	ListNode* pListHeadLong = pHead1;
	ListNode* pListHeadShort = pHead2;
	if(nLength2 > nLength1){
		pListHeadLong = pHead2;
		pListHeadShort = pHead1;
		nLengthDif = nLength2-nLength1;
	}
	for(int i=0; i<nLengthDif; ++i)
		pListHeadLong = pListHeadLong->m_pNext;
	while(pListHeadLong!=NULL && pListHeadShort!=NULL && pListHeadLong!=pListHeadShort){
		pListHeadLong = pListHeadLong->m_pNext;
		pListHeadShort = pListHeadShort->m_pNext;
	}
	ListNode* pFirstCommonNode = pListHeadLong;
	return pFirstCommonNode;
}
测试用例：有公共结点、无公共结点、公共结点在末尾、公共结点为头结点、输入NULL
 
统计排序数组中的频度(剑指P204)
统计一个数字在排序数组中出现的次数
 
已排序则用二分查找
int GetFirstK(int* data, int k, int start, int end)
{
	if(start > end)
		return -1;
	int middleIndex = (start+end)/2;
	int middleData = data[middleIndex];
	if(middleData == k){
		if((middleIndex>0 && data[middleIndex-1]!=k) || middleIndex==0)
			return middleIndex;
		else
			end = middleIndex-1;
	}
	else if(middleData > k)
		end = middleIndex-1;
	else
		start = middleIndex+1;
	return GetFirstK(data,k,start,end);
}
int GetLastK(int* data, int k, int start, int end)
{
	if(start > end)
		return -1;
	int middleIndex = (start+end)/2;
	int middleData = data[middleIndex];
	if(middleData == k){
		if((middleIndex<end-start && data[middleIndex-1]!=k) || middleIndex==end-start)
			return middleIndex;
		else
			end = middleIndex+1;
	}
	else if(middleData < k)
		end = middleIndex+1;
	else
		start = middleIndex-1;
	return GetLastK(data,k,start,end);
}
int GetNumberOfK(int* data, int length, int k)
{
	int number = 0;
	if(data!=NULL && length>0){
		int first = GetFirstK(data,k,0,length-1);
		int last = GetFirstK(data,k,0,length-1);
		if(first>-1 && last>-1)
			number = last-first+1;
	}
	return number;
}
测试用例：功能测试（包含查找数字，没有查找数字，出现一次或多次），边界值测试（查找最大、最小值，数组只有一个数字），特殊输入（NULL）。
 
二叉树的深度
求二叉树的深度
 
int TreeDepth(BinaryTreeNode* pRoot)
{
	if(NULL == pRoot)
		return 0;
	int nLeft = TreeDepth(pRoot->m_pLeft);
	int nRight = TreeDepth(pRoot->m_pRight);
	return (nLeft>nRight) ? (nLeft+1) : (nRight+1);
}
 
判断是否为平衡二叉树(剑指P209)
输入一颗二叉树的根结点，判断该树是不是平衡二叉树（任意结点左右子树的深度差不超过1）
 
遍历多次：
bool IsBalanced(BinaryTreeNode* pRoot)
{
	if(NULL == pRoot)
		return true;
	int left = TreeDepth(pRoot->m_pLeft);
	int right = TreeDepth(pRoot->m_pRight);
	int diff = left-right;
	if(diff>1 || diff<-1)
		return false;
	return IsBalanced(pRoot->m_pLeft) && IsBalanced(pRoot->m_pRight);
}
遍历一次：
bool IsBalanced(BinaryTreeNode* pRoot, int* pDepth)
{
	if(NULL == pRoot){
		*pDepth = 0;
		return true;
	}
	int left, right;
	if(IsBalanced(pRoot->m_pLeft,&left) && IsBalanced(pRoot->m_pRight,&right)){
		int diff = left-right;
		if(diff<=1 && diff>=-1){
			*pDepth = 1+(left>right ? left : right);
			return true;
		}
	}
	return false;
}
bool IsBalanced(BinaryTreeNode* pRoot)
{
	int depth = 0;
	return IsBalanced(pRoot,&depth);
}
 
频度超过一半的查找(剑指P211)
一个整数数组里除了两个数字外，其他数字都出现了两次，找出两个只出现一次的数字
 
全部异或，结果即为这两个数的异或。为1的位可以用来将数组分成两部分，每一部分各含一个结果
void FindNumsAppearOnce(int data[], int length, int* num1, int* num2)
{
	if(NULL==data || length<2)
		return;
	int resultExclusiveOR = 0;
	for(int i=0; i<length; ++i)
		resultExclusiveOR ^= data[i];
	unsigned int indexOf1 = FindFirstBitIs1(resultExclusiveOR);
	*num1 = *num2 = 0;
	for(int j=0; j<length; ++j){
		if(IsBit1(data[j], indexOf1))
			*num1 ^= data[j];
		else
			*num2 ^= data[j];
	}
}
unsigned int FindFirstBitIs1(int num)
{
	int indexBit = 0;
	while(num&1==0 && indexBit<8*sizeof(int)){
		num = num>>1;
		++indexBit;
	}
	return indexBit;
}
bool IsBit1(int num, unsigned int indexBit)
{
	num = num>>indexBit;
	return (num&1);
}
 
和为定值的连续正数序列(剑指P216)
输入一个正数s，打印所有和为s的连续正数序列
 
void FindContinuousSequence(int sum)
{
	if(sum < 3)
		return;
	int small = 1;
	int big = 2;
	int middle = (1+sum)/2;
	int curSum = small+big;
	while(small < middle){
		if(curSum == sum)
			PrintContinousSequence(small,big);
		while(curSum>sum && small<middle){
			curSum -= small;
			small++;
			if(curSum == sum)
				PrintContinuousSequence(small,big);
		}
		big++;
		curSum += big;
	}
}
 
翻转单词
翻转句子中单词的顺序，但单词内字符的顺序不变（标点符号当成字母处理，只有空格为分隔符）
 
void Reverse(char *pBegin, char *pEnd)
{
	if(NULL==pBegin || NULL==pEnd)
		return;
	while(pBegin < pEnd){
		char temp = *pBegin;
		*pBegin = *pEnd;
		*pEnd = temp;
		pBegin++;
		pEnd--;
	}
}
char* ReverseSentence(char *pData)
{
	if(NULL == pData)
		return NULL;
	char *pBegin = pData;
	char *pEnd = pData;
	while(*pEnd != '\0')
		pEnd++;
	pEnd--;
	Reverse(pBegin,pEnd);
	pBegin = pEnd = pData;
	while(*pBegin != '\0'){
		if(*pBegin == ' '){
			pBegin++;
			pEnd++;
		}
		else if(*pEnd==' ' || *pEnd=='\0'){
			Reverse(pBegin,pEnd-1);
			pBegin = pEnd;
		}
		else
			pEnd++;
	}
	return pData;
}
 
字符串的左旋转
定义一个函数实现字符串的左旋转操作
 
用翻转减少时间。字符串相关代码需要注意NULL引起的崩溃以及内存越界
char* LeftRotateString(char* pStr, int n)
{
	if(pStr != NULL){
		int nLength = static_cast<int>(strlen(pStr));
		if(nLength>0 && n>0 && n<nLength){
			char* pFirstStart = pStr;
			char* pFirstEnd = pStr+n-1;
			char* pSecondStart = pStr+n;
			char* pSecondEnd = pStr+nLength-1;
			Reverse(pFirstStart,pFirstEnd);
			Reverse(pSecondStart,pSecondEnd);
			Reverse(pFirstStart,pSecondEnd);
		}
	}
	return pStr;
}
 
打印n个色子的概率(剑指P223)
n个骰子扔在地上，所有骰子朝上一面的点数之和为s。输入n，打印s的所有可能值的出现概率
 
避免将常数硬编码,用循环代替递归提高效率
int g_maxValue = 6;
void PrintProbability(int number)
{
	if(number < 1)
		return;
	int* pProbabilities[2];
	pProbabilities[0] = new int[g_maxValue*number+1];
	pProbabilities[1] = new int[g_maxValue*number+1];
	for(int i=0; i<g_maxValue*number+1; ++i){
		pProbabilities[0][i] = 0;
		pProbabilities[1][i] = 0;
	}
	int flag = 0;	// 用两个数组互相递归，记录当前数组
	for(int i=1; i<=g_maxValue; ++i)
		pProbabilities[flag][i] = 1;
	for(int k=2; k<=number; ++k){
		for(int i=0; i<k; ++i)
			pProbabilities[1-flag][i] = 0;
		for(int i=k; i<=g_maxValue*k; ++i){
			pProbabilities[1-flag][i] = 0;
			for(int j=1; j<=i && j<=g_maxValue; ++j)
				pProbabilities[1-flag][i] += 
											pProbabilities[flag][i-j];
		}
		flag = 1-flag;
	}
	double total = pow((double)g_maxValue,number);
	for(int i=number; i<=g_maxValue*number; ++i){
		double ratio = (double)pProbabilities[flag][i]/total;
		printf("%d: %e\n",i,ratio);
	}
	delete []pProbabilities[0];
	delete []pProbabilities[1];
}
 
约瑟夫环
0到n-1排成一圈，从0开始每次删除第m个数字，求最后剩下的数字。e.g. 01234则依次删除20415
 
用环形链表模拟,复杂度O(mn):
int LastRemaining(unsigned int n, unsigned int m)
{
	if(n<1 || m<1)
		return -1;
	unsigned int i=0;
	list<int> numbers;
	for(i=0; i<n; ++i)
		numbers.push_back(i);
	list<int>::iterator current = numbers.begin();
	while(numbers.size() > 1){
		for(int i=1; i<m; ++i){
			current++;
			if(current == numbers.end())
				current = numbers.begin();
		}
		list<int>::iterator next = ++current;
		if(next == numbers.end())
			next = numbers.begin();
		--current;
		numbers.erase(current);
		current = next;
	}
	return *current;
}
找递归关系
int LastRemaining(unsigned int n, unsigned int m)
{
	if(n<1 || m<1)
		return -1;
	int last = 0;
	for(int i=2; i<=n; ++i)
		last = (last+m)%i;
	return last;
}
 
不用加减乘除求和(剑指P237)
写一个函数，不用加减乘除求两个整数的和
 
int Add(int num1, int num2)
{
	int sum, carry;
	do{
		sum = num1^num2; // 异或
		carry = (num1&num2)<<1;
		num1 = sum;
		num2 = carry;
	}while(num2 != 0);
	return num1;
}
不使用新变量交换两个变量的值也可用异或完成
 
StrToInt(剑指P245)
实现函数StrToInt将字符串变为整数
 
atoi是用一个全局变量来记录出错的。注意一下情况：正数、负数、最大的正负数、NULL、空字符串、有+-、有其他字符、只有+-、溢出
enum Status {kValid=0, kInvalid};
int g_nStatus = kValid;
int StrToInt(const char* str)
{
	g_nStatus = kInvalid;
	long long num = 0;
	if(str!=NULL && *str!='\0'){
		bool minus = false;
		if(*str == '+')
			str++;
		else if(*str == '-'){
			str++;
			minus = true;
		}
		if(*str != '\0')
			num = StrToIntCore(str,minus);
	}
	return (int)num;
}
long long StrToIntCore(const char* digit, bool minus)
{
	long long num = 0;
	while(*digit != '\0'){
		if(*digit>='0' && *digit<='9'){
			int flag = minus ? -1 : 1;
			num = num*10+flag*(*digit-'0');
			if((!minus && num>0x7FFFFFFF) || (minus && num<(signed int)0x80000000)){
				num = 0;
				break;
			}
			digit++;
		}
		else{
			num = 0;
			break;
		}
	}
	if(*digit == '\0')
		g_nStatus = kValid;
	return num;
}
 
最低公共祖先
树的最低公共祖先
 
如果是二叉搜索树的话，可用大小关系搜索
如果有指向父结点的指针，可以转化为求两个链表的第一个公共结点
如果是最一般的情况：用两个链表的最后公共结点来解决
bool GetNodePath(TreeNode* pRoot, TreeNode* pNode, list<TreeNode*>& path)
{
	if(pRoot == pNode)
		return true;
	path.push_back(pRoot);
	bool found = false;
	vector<TreeNode*>::iterator i = pRoot->m_vChildren.begin();
	while(!found && i<pRoot->m_vChildren.end())
		found = GetNodePath(*i++,pNode,path);
	if(!found)
		path.pop_back();
	return found;
}
TreeNode* GetLastCommonNode(const list<TreeNode*>& path1, const list<TreeNode*>& path2)
{
	list<TreeNode*>::const_iterator iterator1 = path1.begin();
	list<TreeNode*>::const_iterator iterator2 = path2.begin();
	TreeNode* pLast = NULL;
	while(itertor1!=path1.end && iterator2!=path2.end()){
		if(*iterator1 == *iterator2)
			pLast = *iterator1;
		++iterator1;
		++iterator2;
	}
	return pLast;
}
TreeNode* GetLastCommonParent(TreeNode* pRoot, TreeNode* pNode1, TreeNode* pNode2)
{
	if(pRoot==NULL || pNode1==NULL || pNode2==NULL)
		return NULL:
	list<TreeNode*> path1;
	GetNodePath(pRoot,pNode1,path1);
	list<TreeNode*> path2;
	GetNodePath(pRoot,pNode2,path2);
	return GetLastCommonNode(path1,path2);
}
 
让CPU占用率曲线听你指挥（BoP）
让CPU占用率固定在50%，以及输出正弦函数
 
睡眠时间10毫秒比较接近windows的调度时间片，太小会造成线程的频繁唤醒和挂起，增加了内核时间的不稳定性
```
#include “Windows.h”
while(true){
	for(int i=0; i<9600000; i++)
		;
	Sleep(10);
}
GetTickCount()记录系统启动至今所经历的时间的毫秒值，输出正弦曲线：
const double SPLIT = 0.01;
const int COUNT = 200;	// 表示分辨率，跟SPLIT相乘为2
const double PI = 3.14159265;
const int INTERVAL = 300;	// 正弦函数的幅度
int _tmain(int argc, _TCHAR* argv[])
{
	DWORD busySpan[COUNT];
	DWord idleSpan[COUNT];
	int half = INTERVAL/2;
	double radian = 0.0;
	for(int i=0; i<COUNT; i++){
		busySpan[i] = (DWORD)(half+(sin(PI*radian)*half));
		idleSpan[i] = INTERVAL-busySpan[i];
		radian += SPLIT;
	}
	DWORD startTime = 0;
	int j = 0;
	while(true){
		j = j%COUNT;
		startTime = GetTickCount();
		while(GetTickCount()-startTime <= busySpan[j])
			;
		Sleep(idleSpan[j]);
		j++;
	}
	return 0;
}
```

中国象棋将帅问题（BoP）
输出棋盘上将帅所有合法的位置，只能用一个变量
 
两个位置各有9种，需要将一个变量拆成两部分表示。从左到右从上到下1-9编号的话，其实就是除3后的余数不相同就可以了
BYTE i = 81;
while(i--){
	if(i/9%3 == i%9%3)
		continue;
	printf("A = %d, B = %d\n" i/9+1, i%9+1);
}
或者效率更高的写法：
struct{
	unsigned char a:4;
	unsigned char b:4;
} i;
for(i.a=1; i.a<=9; ++i.a)
	for(i.b=1; i.b<=9; ++i.b)
		if(i.a%3 != i.b%3)
			printf("A = %d, B = %d",i.a,i.b);
 
饮料供应（BoP）
假设所有饮料总共的容量为V，每种饮料每瓶的容量为Vi，满意度为Hi，最大数量为Ci。问满意度和的最大值为多少
 
其实可以贪心，此处用动态规划，opt[V,0]为所求。意义为从第T到n-1种饮料中，总量为V的最大满意度
int Cal(int V, int T)
{
	opt[0][T] = 0;
	for(int i=1; i<=V; i++)	// 边界条件
		op[i][T] = -INF;
	for(int j=T-1; j>=0; j--)	// 考虑到第几种饮料
		for(int i=0; i<=V; i++){	// 剩余多少容量
			opt[i][j] = -INF;
			for(int k=0; k<=C[j]; k++){
				if(i < k*V[j])
					break;
				int x = opt[i-k*V[j]][j+1];
				if(x != -INF){
					x += H[j]*k;
					if(x > opt[i][j])
						opt[i][j] = x;
				}
			}
		}
}
备忘录法动态规划用记忆化搜索，若子问题尚未求解才进行计算
int [V+1][T+1] opt;	// 备忘录，初始化为-1表示尚未求解
int Cal(int V, int type)
{
	if(type == T){
		if(V == 0)
			return 0;
		else
			return -INF;
	}
	if(V < 0)
		return -INF;
	else if(V == 0)
		return 0;
	else if(opt[V][type] != -1)
		return opt[V][type];
	int ret = -INF;
	for(int i=0; i<=C[type]; i++){
		int temp = Cal(V-i*C[type], type+1);
		if(temp != -INF){
			temp += H[type]*i;
			if(temp > ret)
				ret = temp;
		}
	}
	return opt[V][type] = ret;
}
 
光影切割问题（BoP）
二维坐标上有若干三线不共点且不与x轴垂直的直线，求x坐标[A,B]区间的空间被直线分成几个部分
 
块数为1+直线数+交点数，实际就是计算交点数量。将直线A上的交点即B上的作为两个序列，逆序对的数量就是交点的数量
 
小飞的电梯调度算法（BoP）
电梯从一楼出发，nPerson[]记录到每一层的人数，求到哪一层停止，所有人走到各自目的要走的层数和最少
 
int nPerson[];
int nMinFloor, nTargetFloor;	// 最少的层数和以及目标层
int N1, N2, N3;	// i层以下、i层、i层以上的数量
nTargetFloor = 1;
nMinFloor = 0;
for（N1=0,N2=nPerson[1],N3=0,i=2; i<=N; i++){
	N3 += nPerson[i];
	nMinFloor += nPerson[i]*(i-1);
}
for(i=2; i<=N; i++){
	if(N1+N2 < N3){
		nTargetFloor = i;
		nMinFloor += N1+N2-N3;
		N1 += N2;
		N2 = nPerson[i];
		N3 -= nPerson[i];
	}
	else
		break;
}
 
高效地安排见面会（BoP）
已知有n位学生，分别对m场招聘会感兴趣，如何安排招聘会的时间使用时最少而所有学生时间都不冲突
假设有N个面试，起止时间分别为B[i]和E[i]，问至少需要多少个面试点
 
将招聘会作为结点，有同一个学生感兴趣的招聘会之间连线，则转化为最小着色问题（无有效算法）
按开始时间排序后，可以贪心地着色：
int nMaxColors=0,i,j,k;
for(i=0; i<N; i++){
	for(k=0; k<nMaxColors; k++)
		isForbidden[k] = false;
	for(j=0; j<i; j++)
		if(Overlap(b[j],e[j],b[i],e[i]))
			isForbidden[color[j]] = true;
	for(k=0; k<nMaxColors; k++)
		if(!isForbidden[k])
			break;
	if(k < nMaxColors)
		color[i] = k;
	else
		color[i] = nMaxColors++;
}
return nMaxColors;
或者算冲突最多的时刻
int nColorUsing=0,MaxColor=0;
for(int i=0; i<2*N; i++){
	if(TimePoints[i].type == "Begin"){
		nColorUsing++;
		if(nColorUsing > MaxColor)
			MaxColor = nColorUsing;
	}
	else
		nColorUsing--;
}

 
亲戚
或许你并不知道，你的某个朋友是你的亲戚。他可能是你的曾祖父的外公的女婿的外甥的表姐的孙子。如果能得到完整的家谱，判断两个人是否亲戚应该是可行的，但如果两个人的最近公共祖先与他们相隔好几代，使得家谱十分庞大，那么检验亲戚关系实非人力所能及.在这种情况下，最好的帮手就是计算机。
为了将问题简化，你将得到一些亲戚关系的信息，如同Marry和Tom是亲戚，Tom和B en是亲戚，等等。从这些信息中，你可以推出Marry和Ben是亲戚。请写一个程序，对于我们的关心的亲戚关系的提问，以最快的速度给出答案。
参考输入输出格式 输入由两部分组成。
第一部分以N，M开始。N为问题涉及的人的个数(1 ≤ N ≤ 20000)。这些人的编号为1,2,3,…,N。下面有M行(1 ≤ M ≤ 1000000)，每行有两个数ai, bi，表示已知ai和bi是亲戚.
第二部分以Q开始。以下Q行有Q个询问(1 ≤ Q ≤ 1 000 000)，每行为ci, di，表示询问ci和di是否为亲戚。
对于每个询问ci, di，若ci和di为亲戚，则输出Yes，否则输出No。
样例输入与输出
输入relation.in
10 7
2 4
5 7
1 3
8 9
1 2
5 6
2 3
3
3 4
7 10
8 9
输出relation.out
Yes
No
Yes 
如果这道题目不用并查集，而只用链表或数组来存储集合，那么效率很低，肯定超时。
int N,M,Q;
int pre[20000],rank[20000];
void makeset(int x)
{
	pre[x] = -1;
	rank[x] = 0;
}
int find(int x)
{
	int r = x;
	while(pre[r] != -1)
		r = pre[r];
	while(x != r){
		int q = pre[x];
		pre[x] = r;
		x = q;
	}
	return r;	  
}	
void unionone(int a,int b)
{
	int t1 = find(a);
	int t2 = find(b);
	if(rank[t1] > rank[t2])
		pre[t2] = t1;
	else
		pre[t1] = t2;
	if(rank[t1] == rank[t2])
		rank[t2]++;	 
}	   
int main()
{
	int i,a,b,c,d;
	while(cin>>N>>M){
		for(i=1; i<=N; i++)
			makeset(i);
		for(i=1; i<=M; i++){
			cin>>a>>b;
			if(find(a)!=find(b))
				unionone(a,b);
		}
		cin>>Q; 
		for(i=1; i<=Q; i++){
			cin>>c>>d;
			if(find(c) == find(d))
				cout<<"YES"<<endl;
			else
				cout<<"NO"<<endl; 
		}		   
	}   
	return 0;
} 
Divide Two Integers (Leetcode)
Divide two integers without using multiplication, division and mod operator.
 
取反的时候千万注意负数的范围是比正数大的
class Solution {
public:
    int divide(int dividend, int divisor) {
        // Start typing your C/C++ solution below
        // DO NOT write int main() function
        assert(divisor!=0);
        bool possitive = (dividend>0&&divisor>0) || (dividend<0&&divisor<0);
        long long dividendL = dividend;
        long long divisorL = divisor;
        if(dividendL < 0)
            dividendL = -dividendL;
        if(divisorL < 0)
            divisorL = -divisorL;
        long long result = 0;
        int shift = 31;
        while(dividendL>0 && shift>=0){
            if(dividendL >= divisorL<<shift){
                dividendL -= divisorL<<shift;
                result += 1<<shift;
            }
            shift--;
        }
        return possitive? result : -result;
    }
}; 
Implementing strStr() (Leetcode)
Implement strStr().
Returns a pointer to the first occurrence of needle in haystack, or null if needle is not part of haystack.
 
KMP的实现，各种边界条件
class Solution {
public:
    char *strStr(char *haystack, char *needle) {
        // Start typing your C/C++ solution below
        // DO NOT write int main() function
        if(haystack==NULL || needle==NULL)
            return NULL;
        int hLen = strlen(haystack);
        int nLen = strlen(needle);
        if(nLen == 0)
            return haystack;
        int *f = new int[nLen];
        f[0] = -1;
        int i,u;
        for(i=1,u=-1; i<nLen; i++){
            while(u>=0 && needle[i]!=needle[u+1])
                u = f[u];
            if(needle[i] == needle[u+1])
                f[i] = ++u;
            else
                f[i] = u = -1;
        }
        i = u = 0;
        while(i<hLen && u<nLen){
            if(haystack[i] == needle[u]){
                i++;
                u++;
            }
            else if(u==0)
                i++;
            else
                u = f[u-1]+1;
        }
    	delete[] f;
        if(u < nLen)
            return NULL;
        else
            return haystack+i-u;
    }
}; 
Remove Element (Leetcode)
Given an array and a value, remove all instances of that value in place and return the new length.
The order of elements can be changed. It doesn't matter what you leave beyond the new length.
 
class Solution {
public:
    int removeElement(int A[], int n, int elem) {
        // Start typing your C/C++ solution below
        // DO NOT write int main() function
        if(n < 1)
            return 0;
        int result = 0;
        for(int i=0; i<n; i++)
            if(A[i] != elem)
                A[result++] = A[i];
        return result;
    }
}; 
矩阵相乘
C=AB，均为n*n
 
考虑cache-friendly，应当为一下的一种
for(k=0; k<n; k++)
	for(i=0; i<n; i++){
		r = A[i][k];
		for(j=0; j<n; j++)
			C[i][j] += r*B[k][j];
	}
	
for(i=0; i<n; i++)
	for(k=0; k<n; k++){
		r = A[i][k];
		for(j=0; j<n; j++)
			C[i][j] += r*B[k][j];
	} 
非递归前序遍历
 
stack<BinaryNode<Type>*> s;
BinaryNode<Type> *root = this;
while(root!=NULL || !s.empty()){
	if(root != NULL){
		cout<<root->data;
		s.push(root);
		root = root->left;
	}
	else{
		root = s.top();
		s.pop();
		root = root->right;
	}
} 
非递归中序遍历
 
stack<BinaryNode<Type>*> s;
BinaryNode<Type> *root = this;
while(root!=NULL || !s.empty()){
	while(root != NULL){
		s.push(root);
		root = root->left;
	}
	if(!s.empty()){
		root = s.top();
		s.pop();
		cout<<root->data;
		root = root->right;
	}
} 
非递归后序遍历
 
必须要用标志位
stack<BinaryNode<Type>*> s;
BinaryNode<Type> *root = this;
if(root != NULL)
	s.push(root);
while(!s.empty()){
	BinaryNode<Type>* node = s.top();
	s.pop();
	if(node->mark)
		cout<<node->data;
	else{
		node->mark = true;
		s.push(node);
		if(node->right != NULL){
			node->right->mark = false;
			s.push(node->right);
		}
		if(node->left != NULL){
			node->left->mark = false;
			s.push(node->left);
		}
	}
} 
二分查找
 
假设序列从1开始
int low=1,high=N,mid;
while(low <= high){
	mid = low+(high-low)/2;
	if(A[mid] == key)
		return mid;
	else if(key < A[mid])
		high = mid-1;
	else
		low = mid+1;
}
return 0; 
二叉树去除结点
 
int BST<Type>::RemoveMin(BSTNode<Type> *&T){
	if(T == NULL)
		return 0;
	else if(T->left != NULL)
		return RemoveMin(T->left);
	BSTNode<Type> *p;
	p = T;
	T = T->right;
	delete p;
	return 1;
}
int BST<Type>::Remove(const Type &x,BSTNode<Type> *&T){
	BSTNode<Type> *p;
	if(T == NULL)
		return 0;
	else if(x < T->data)
		return Remove(x,T->left);
	else if(x > T->data)
		return Remove(x,T->right);
	else if(T->left!=NULL && T->right!=NULL){
		p = FindMin(T->right);	// 找直接后继
		T->data = p->data;
		return RemoveMin(T->right);
	}
	p = T;	// 处理至少一个儿子为空的情况
	T = (T->left==NULL)? T->right:T->left;
	delete p;
	return 1;
}
 
有向图DFS
 
void Graph<VType,EType>::DFS_Traveling(const VType &start){
	Stack<Edge<VType,EType>*> s(NumEdge);
	Vertex<VType,EType> *pVertex,*destVertex;
	Edge<VType,EType> *pEdge;
	pVertex = head;
	while(pVertex){
		pVertex->tag = 0;
		pVertex = pVertex->next;
	}
	pVertex = GetVertexPos(start);
	if(!p){
		cerr<<"Start vertex is Error!"<<endl;
		return;
	}
	bool finished = false;
	while(pVertex){
		if(pVertex->tag == 0){
			pVertex->tag = 1;
			cout<<pVertex->data<<endl;
			if(pVertex->adj)
				s.Push(pVertex->adj);
			while(!s.IsEmpty()){
				pEdge = s.Pop();
				if(pEdge->link)
					s.Push(pEdge->link);
				destVertex = pEdge->dest;
				if(destVertex->tag == 0){
					destVertex->tag = 1;
					cout<<destVertex->data<<endl;
					if(destVertex->adj)
						s.Push(destVertex->adj);
				}
			}
		}
		pVertex = pVertex->next;
		if(!pVertex && !finished){	// 因为起始点不一定是点链表的第一个
			pVertex = head;
			finished = true;
		}
	}
	return;
}
 
有向图BFS
 
void Graph<VType,EType>::BFS_Traveling(const VertexType &start){
	Queue<Vertex<VType,EType>*> q(NumVertex);
	Vertex<VType,EType> *pVertex,*temp,*destVertex;
	Edge<VType,EType> *pEdge;
	pVertex = head;
	while(pVertex){
		pVertex->tag = 0;
		pVertex = pVertex->next;
	}
	pVertex = GetVertexPos(start);
	if(!pVertex){
		cerr<<"Start vertex is Error!"<<endl;
		return;
	}
	bool finished = false;
	while(pVertex){
		if(pVertex->tag == 0){
			pVertex->tag = 1;
			cout<<pVertex->data<<endl
			q.EnQueue(p);
			while(!q.IsEmpty()){
				temp = q.DeQueue();
				pEdge = temp->adj;
				while(pEdge){
					destVertex = pEdge->dest;
					if(destVertex ->tag == 0){
						destVertex ->tag = 1;
						cout<< destVertex->data<<endl;
						q.EnQueue(destVertex);
					}
					pEdge = pEdge->link;
				}
			}
		}
		pVertex = pVertex->next;
		if(!pVertex && !finished){
			pVertex = head;
			finished = true;
		}
	}
	return;
}
 
Dijkstra算法
邻接矩阵，有向图
 
void Graph<VType,EType>::Dijkstra(VType start){
	EType *dist = new EType[NumVertex];	// 最短路径
	int *prev = new int[NumVertex];	// 源点到顶点的最短路径上前一结点
	int *u = new int[NumVertex];
	int pmin;	// dist中最小值下标
	int i,j;
	int idxStart;
	for(idxStart=0;idxStart<NumVertex;idxStart++)
		if(NodeList[idxStart] == start)
			break;
	if(idxStart >= NumVertex){
		cerr<<"This is error!"<<endl;
		return;
	}
	for(i=0;i<NumVertex;i++){
		dist[i] = AdjMatrix[idxStart*NumVertex+i];
		prev[i] = idxStart;
		u[i] = 0;
	}
	prev[idxStart] = -1;
	u[idxStart] = 1;
	for(i=0;i<NumVertex-1;i++){
		for(j=0;j<NumVertex;j++)
			if(!u[j])
				break;
		pmin = j;
		for(j++;j<NumVertex;j++)
			if(!u[j] && dist[j]<dist[pmin])
				pmin = j;
		u[pmin] = 1;
		for(j=0;j<NumVertex;j++)
			if(!u[j] && dist[pmin]+AdjMatrix[pmin*NumVertex+j]<dist[j]){
				dist[j] = dist[pmin]+AdjMatrix[pmin*NumVertex+j];
				prev[j] = pmin;
			}
	}
	for(j=0;j<NumVertex;j++){
		cout<<j<<" : "<<dist[j]<<endl;
		cout<<"All vertices are : ";
		i = j;
		while(i!=idxStart){
			cout<<i<<"<-";
			i = prev[i];
		}
		cout<<idxStart<<"."<<endl;
	}
	return;
}
 
拓扑排序
邻接表，有向图
 
void Topological_Order(Graph<VType,EType> &graph){
	Vertex<VType,EType> *pVertex,*destVertex;
	Edge<VType,EType> *pEdge;
	Stack<Vertex<VType,EType>*> s(graph.NumVertex);
	pVertex = graph.head;
	while(pVertex){
		pVertex->tag = 0;
		pVertex = pVertex->next;
	}
	pVertex = graph.head;
	while(pVertex){	// 计算入度
		pEdge = pVertex->adj;
		while(pEdge){
			destVertex = pEdge->dest;
			destVertex->tag++;
			pEdge = pEdge->link;
		}
		pVertex = pVertex->next;
	}
	pVertex = graph.head;
	while(pVertex){	// 入度为0进栈
		if(pVertex->tag == 0)
			s.Push(pVertex);
		pVertex = pVertex->next;
	}
	for(int i=0;i<graph.NumVertex;i++){
		if(s.IsEmpty()){
			cerr<<"There is a cycle in the graph."<<endl;
			return;
		}
		else{
			pVertex = s.Pop();
			cout<<"Element"<<pVertex->data<<" is in position"<<i+1<<endl;
			pEdge = pVertex->adj;
			while(pEdge){
				destVertex = pEdge->dest;
				destVertex->tag--;
				if(destVertex->tag == 0)
					s.Push(destVertex);
				pEdge = pEdge->link;
			}
		}
	}
	return;
}
 
堆排序
 
// 建立最大化堆
void SiftDown(Type heap[],int CurrentSize,int j){
	int MaxSon;
	heap[0] = heap[j];
	for(; j*2<=CurrentSize; j=MaxSon){
		MaxSon = 2*j;
		if(MaxSon!=CurrentSize && heap[MaxSon+1]>heap[MaxSon])
			MaxSon++;
		if(heap[MaxSon] > heap[0])
			heap[j] = heap[MaxSon];
		else break;
	}
	heap[j] = heap[0];
}
void BuildHeap(Type heap[],int j){
	for(int j=CurrentSize/2; j>0; j--)
		SiftDown(j);
}

// 最大化堆插入
void Insert(Type heap[],int CurrentSize,int MaxSize,const Type &x){
	Exception(++CurrentSize>MaxSize,"Out of boundary!");
	int j = CurrentSize;
	heap[0] = x;
	for(; x>heap[j/2]; j/=2)
		heap[j] = heap[j/2];
	heap[j] = x;
}

// 最大化堆删除
void DeleteMax(Type heap[],int CurrentSize,Type &x){
	x = heap[1];
	heap[1] = heap[CurrentSize--];
	SiftDown(heap,CurrentSize,1);
}

// 堆排序
void MaxHeapSort(Type seq[],int n){
	BuildHeap(seq,n);
	for(int k=n; k>1; k--){
		Swap(seq[1],seq[k]);
		SiftDown(seq,k-1,1);
	}
}
 
快速排序
 
void QuickSort(Type seq[],int low,int high){
	if(low+INSERT_NUM > high)
		InsertSort(&seq[low],high-low+1);
	else{
		int k,j,mid=(low+high)/2;
		if(seq[mid] < seq[low])	// 找三者的中值
			Swap(seq[mid],seq[low]);
		if(seq[high] < seq[low])
			Swap(seq[high],seq[low]);
		if(seq[high] < seq[mid])
			Swap(seq[mid],seq[high]);
		Type milestone=seq[mid];
		Swap(seq[mid],seq[high]);
		k = low;	// 第二个的起点
		j = low;	// 未处理的第一个
		while(j<high){
			if(seq[j]<milestone){
				Swap(seq[k],seq[j]);
				k++;
			}
			j++;
		}
		Swap(seq[high],seq[k]);
		QuickSort(seq,low,k-1);
		QuickSort(seq,k+1,high);
	}
}
QuickSort(Type seq[],int n){
	QuickSort(seq,1,n);
}
 
多级图问题
k级
 
const int MAXNUM = 99999;
void multistage_graph(Graph<VType,EType> &graph,const int k){
	int *D,*path; 
	D = new int[graph.NumVertex]; 	// 记录最短路径中下一个点的编号
	D[graph.NumVertex-1] = graph.NumVertex-1;
	path = new int[k]; 	// 记录源点到汇点的序号
	int j,m;
	EType *cost;
	cost = new EType[graph.NumVertex];
	for(j=0;j<graph.NumVertex;j++)
		cost[j] = MAXNUM;
	cost[graph.NumVertex-1] = 0;	// 汇点到自身的路径长度
	Vertex<VType,EType> *pVertex;
	Edge<VType,EType> *pEdge;
	EType weight;
	pVertex = graph.head;
	j = graph.NumVertex-1;
	while(pVertex){	// tag为顶点编号
		pVertex->tag = j--;
		pVertex = pVertex->next;
	}
	pVertex = graph.head;
	while(pVertex){
		pEdge = pVertex->adj;
		while(pEdge){
			weight = pEdge->cost;
			m = pEdge->dest->tag;
			if(weight+cost[m] < cost[pVertex->tag]){
				cost[pVertex->tag] = weight+cost[m];
				D[pVertex->tag] = m;
			}
			pEdge = pEdge->link;
		}
		pVertex = pVertex->next;
	}
	path[0] = 0;
	path[k-1] = graph.NumVertex-1;
	for(j=1;j<=k-2;j++)
		path[j] = D[path[j-1]];
	for(j=0;j<=k-1;j++)
		cout<<path[j]<<endl;
	cout<<"Min Cost: "<<cost[0]<<endl;
}
 
背包问题
假设已按照单价排序
struct WVM{
	float cweight;	//  当前重量
	float cval;	// 当前价值
	int cnum;	// 当前考察的数量
};
class knap{
	friend void knapsack(knap<WType,PType> &);
public:
	knap(const WType total,const int n,PType &v,WType w1[],PType v1[]);
	~knap() {delete []w;delete []v;}
private:
	PType f(Wtype remnant,int i);	// 剩余重量的价值上届
	WType TOT;	// 重量限制
	int num;	// 物品数量
	WType *weight;
	PType *value;
	PType maxval;
};

 
knap<WType,PType>:: knap(const WType total,const int n,PType &v,WType w1[],PType v1[]){
	TOT = total;
	num = n;
	maxval  = v;
	weight = new WType[n+1];
	value = new PType[n+1];
	for(int j=1;j<=n;j++){
		weight[j] = w1[j];
		value[j] = v1[j];
	}
}
PType knap<WType,PType>::f(WType t,int i){
	PType amount=0;
	i++;
	while(i<=n && w[i]<=t){
		t -= weight[i];
		amount += value[i];
		i++;
	}
	if(i <= n)
		amount += t*value[i]/weight[i];
	return amount;
}
void knapsack(knap<WType,PType> &ks){
	ks.maxval = 0;
	stack<WVM> s;
	WVM x;
	x.cweight = 0;
	x.cval = 0;
	x.cnum = 1;	// 右儿子
	s.push(x);
	while(s.empty() == false){
		x = s.top();
		if(x.cnum == ks.num){
			if(x.cval > ks.maxval)
				ks.maxval = x.cval;
			s.pop();
		}
		else if(x.cval+ks.f(ks.TOT-x.cweight,x.cnum) > ks.maxval){
			x.cnum ++;
			s.pop();	// 父结点
			s.push(x);	// 右儿子
			if(x.cweight+ks.weight[x.cnum] <= ks.TOT){
				x.cweight += ks.weight[x.cnum];
				x.cval += ks.value[x.cnum];
				s.push(x);	// 左儿子
			}
		}
		else
			s.pop();	// 剪枝
	}
	cout<<ks.mval<<endl;
}
 
单元最短路径
分支定界
class MinNode{
	friend class Graph<VType,EType>;
public:
	MinNode(int n,EType length1){VexNum=n;length=length1;}
	bool operator >(const MinNode<VType,EType> &p) const{
		return this->length > p.length;
	}
private:
	int VexNum;	// 源点序号
	EType length;
}

 
const int MAXNUM = 99999;
void Graph<VType,EType>::shortestpath(VType start){
	int j,k,source;
	EType *distance = new EType[NumVertex];
	int *p = new int[NumVertex];	// 到该点最短路径的前一个结点
	vector<MinNode<VType,EType>> H;
	for(source=0;source<NumVertex;source++)
		if(NodeList[source] == start)
			break;
	if(source >= NumVertex){
		cerr<<"This is error!"<<endl;
		return;
	}
	MinNode<VType,EType> E(source,0);
	for(k=0;k<NumVertex;k++){
		distance[k] = MAXNUM;
		p[k] = source;
	}
	distance[source] = 0;
	p[source] = -1;
	vector<MinNode<VType,EType>>::iterator q;
	MinNode<VType,EType> N;
	while(1){
		for(j=0;j<NumVertex;j++)
			if((AdjMatrix[E.VexNum*NumVertex+j]<MAXNUM) && 
					(E.length+AdjMatrix[E.VexNum*NumVertex+j]<distance[j])){
				distance[j] = E.length+AdjMatrix[E.VexNum*NumVertex+j];
				p[j] = E.VexNum;
				N = MinNode<VType,EType>(j,distance[j]);
				H.push_back(N);
				push_heap(H.begin(),H.end(),greater<MinNode<VType,EType>>());
			}
		if(H.empty())
			break;
		else{
			pop_heap(H.begin(),H.end(),greater<MinNode<VType,EType>>());
			q = H.end()-1;
			E = *q;
			H.pop_back();
		}
	}
	for(k=0;k<NumVertex;k++){
		cout<<k<<"-"<<distance[k]<<endl;
		j=k;
		cout<<":";
		while(j != source){
			cout<<j<<"<-";
			j = p[j];
		}
		cout<<source<<"."<<endl;
	}
}
 
Next Permutation (Leetcode)
Implement next permutation, which rearranges numbers into the lexicographically next greater permutation of numbers.
If such arrangement is not possible, it must rearrange it as the lowest possible order (ie, sorted in ascending order).
The replacement must be in-place, do not allocate extra memory.
Here are some examples. Inputs are in the left-hand column and its corresponding outputs are in the right-hand column.
1,2,3 → 1,3,2
3,2,1 → 1,2,3
1,1,5 → 1,5,1 
class Solution {
public:
    void nextPermutation(vector<int> &num) {
        // IMPORTANT: Please reset any member data you declared, as
        // the same Solution instance will be reused for each test case.
        vector<int>::reverse_iterator front = num.rbegin();
        vector<int>::reverse_iterator changePoint = front+1;
        while(changePoint!=num.rend() && 
				*changePoint>=*(changePoint-1))
            ++changePoint;
        if(changePoint != num.rend()){
            vector<int>::reverse_iterator larger = front;
            while(*larger <= *changePoint)
                ++larger;
            swap(*changePoint,*larger);
        }
        reverse(front,changePoint);
    }
}; 
Combination Sum (Leetcode)
Given a set of candidate numbers (C) and a target number (T), find all unique combinations in C where the candidate numbers sums to T.
The same repeated number may be chosen from C unlimited number of times.
Note:
All numbers (including target) will be positive integers.
Elements in a combination (a1, a2, � , ak) must be in non-descending order. (ie, a1 ? a2 ? � ? ak).
The solution set must not contain duplicate combinations.
For example, given candidate set 2,3,6,7 and target 7, 
A solution set is: 
[7] 
[2, 2, 3] 
 

class Solution {
private:
    void choose(vector<int>::iterator start,vector<int>::iterator finish,int target,vector<vector<int> >& result,vector<int>& solution)
    {
        if(start == finish)
            return;
        if(target == 0){
            result.push_back(solution);
            return;
        }
        if(*start <= target){
            solution.push_back(*start);
            choose(start,finish,target-*start,result,solution);
            solution.pop_back();
        }
        choose(start+1,finish,target,result,solution);
    }
public:
    vector<vector<int> > combinationSum(vector<int> &candidates, int target) {
        sort(candidates.begin(),candidates.end());
        vector<vector<int> > result;
		if(candidates.size() == 0)
			return result;
        vector<int> solution;
        for(vector<int>::iterator iter=candidates.begin(); iter!=candidates.end()&&*iter<=target; ++iter){
            solution.clear();
            solution.push_back(*iter);
            choose(iter,candidates.end(),target-*iter,result,solution);
        }
        return result;
    }
}; 
Combination Sum II (Leetcode)
Given a collection of candidate numbers (C) and a target number (T), find all unique combinations in C where the candidate numbers sums to T.
Each number in C may only be used once in the combination.
Note:
All numbers (including target) will be positive integers.
Elements in a combination (a1, a2, � , ak) must be in non-descending order. (ie, a1 ? a2 ? � ? ak).
The solution set must not contain duplicate combinations.
For example, given candidate set 10,1,2,7,6,1,5 and target 8, 
A solution set is: 
[1, 7] 
[1, 2, 5] 
[2, 6] 
[1, 1, 6] 
 
注意不可重复的情况下，迭代器增量的问题
class Solution {
private:
    void choose(vector<int>::iterator start,vector<int>::iterator stop,int target,
                vector<vector<int> >& res,vector<int>& sol)
    {
        if(target == 0){
            res.push_back(sol);
            return;
        }
        if(start == stop)
            return;
        int cur = *start;
        if(cur <= target){
            sol.push_back(cur);
            choose(start+1,stop,target-cur,res,sol);
            sol.pop_back();
        }
        while(start!=stop && *start==cur)
            start++;
        choose(start,stop,target,res,sol);
    }
public:
    vector<vector<int> > combinationSum2(vector<int> &num, int target) {
        // Start typing your C/C++ solution below
        // DO NOT write int main() function
        sort(num.begin(),num.end());
        vector<vector<int> > res;
        if(num.size() == 0)
            return res;
        vector<int> sol;
        vector<int>::iterator iter=num.begin();
        while(iter != num.end()){
            int cur = *iter;
            if(cur > target)
                break;
            sol.clear();
            sol.push_back(cur);
            choose(iter+1,num.end(),target-cur,res,sol);
            while(iter!=num.end() && *iter==cur)
                iter++;
        }
        return res;
    }
}; 
Rotate Image (Leetcode)
You are given an n x n 2D matrix representing an image.
Rotate the image by 90 degrees (clockwise).
Follow up:
Could you do this in-place? 
注意有中线的情况(n为奇数)，中线部分不要转两次
class Solution {
public:
    void rotate(vector<vector<int> > &matrix) {
        // Start typing your C/C++ solution below
        // DO NOT write int main() function
        size_t N = matrix.size();
        for(size_t i=0; i<(N+1)/2; i++)
            for(size_t j=0;j<N/2; j++){
                int cur = matrix[i][j];
                matrix[i][j] = matrix[N-1-j][i];
                matrix[N-1-j][i] = matrix[N-1-i][N-1-j];
                matrix[N-1-i][N-1-j] = matrix[j][N-1-i];
                matrix[j][N-1-i] = cur;
            }
    }
}; 
Pow(x, n) (Leetcode)
Implement pow(x, n). 
n小于0时若作为-n递归，则涉及INT_MIN无法变负数的问题
class Solution {
public:
    double pow(double x, int n) {
        // Start typing your C/C++ solution below
        // DO NOT write int main() function
        if(n == 0)
            return 1;
        double square = pow(x,n/2);
        double result = square*square;
        if(n&0x1){
            if(n > 0)
                result *= x;
            else
                result /= x;
        }
        return result;
    }
}; 
N-Queens (Leetcode)
Given an integer n, return all distinct solutions to the n-queens puzzle.
Each solution contains a distinct board configuration of the n-queens' placement, where 'Q' and '.' both indicate a queen and an empty space respectively.
For example,
There exist two distinct solutions to the 4-queens puzzle:
[
 [".Q..",  // Solution 1
  "...Q",
  "Q...",
  "..Q."],

 ["..Q.",  // Solution 2
  "Q...",
  "...Q",
  ".Q.."]
]

 
class Solution {
private:
    vector<int> chessboard;
    int N; 
    void solve(int row,vector<vector<string> >& result)
    {
        if(row >= N){
            vector<string> sol;
            string temp;
            for(int i=0; i<N; i++){
                temp = string(N,'.');
                temp[chessboard[i]] = 'Q';
                sol.push_back(temp);
            }
            result.push_back(sol);
            return;
        }
        for(int i=0; i<N; i++){
            int valid = true;
            for(int j=0; j<row; j++)
                if(chessboard[j]==i || abs(j-row)==abs(chessboard[j]-i)){
                    valid = false;
                    break;
                }
            if(valid){
                chessboard[row] = i;
                solve(row+1,result);
            }
        }
    }
};
public:
    vector<vector<string> > solveNQueens(int n) {
        // Start typing your C/C++ solution below
        // DO NOT write int main() function
        chessboard.clear();
        chessboard = vector<int>(n,-1);
        this->N = n;
        vector<vector<string> > result;
        solve(0,result);
        return result;
    }
 
Rotate List (Leetcode)
Given a list, rotate the list to the right by k places, where k is non-negative.
For example:
Given 1->2->3->4->5->NULL and k = 2,
return 4->5->1->2->3->NULL.
 
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode *rotateRight(ListNode *head, int k) {
        // Start typing your C/C++ solution below
        // DO NOT write int main() function
        if(head == NULL)
            return head;
        int length=0;
        for(ListNode* ptr=head; ptr!=NULL; ptr=ptr->next)
            length++;
        k = k%length;
        ListNode* front = head;
        for(int i=0; i<k; i++)
            front = front->next;
        ListNode* back = head;
        while(front->next != NULL){
            front = front->next;
            back = back->next;
        }
        front->next = head;	// k为0的情况也被包含了
        head = back->next;
        back->next = NULL;
        return head;
    }
}; 
Pascal’s Triangle (Leetcode)
Given numRows, generate the first numRows of Pascal's triangle.
For example, given numRows = 5,
Return
[
     [1],
    [1,1],
   [1,2,1],
  [1,3,3,1],
 [1,4,6,4,1]
]
 
class Solution {
public:
    vector<vector<int> > generate(int numRows) {
        // Start typing your C/C++ solution below
        // DO NOT write int main() function
        vector<vector<int> > result;
        if(numRows < 1)
            return result;
        vector<int> row;
        result.push_back(vector<int>(1,1));
        for(int i=1; i<numRows; i++){
            row.clear();
            row.push_back(1);
            for(int j=0; j<result[i-1].size()-1; j++)
                row.push_back(result[i-1][j]+result[i-1][j+1]);
            row.push_back(1);
            result.push_back(row);
        }
        return result;
    }
}; 
Pascal’s Triangle II (Leetcode)
Given an index k, return the kth row of the Pascal's triangle.
For example, given k = 3,
Return [1,3,3,1].
Note:
Could you optimize your algorithm to use only O(k) extra space?
 
class Solution {
public:
    vector<int> getRow(int rowIndex) {
        // Start typing your C/C++ solution below
        // DO NOT write int main() function
        vector<int> result;
        if(rowIndex < 0)
            return result;
        result.push_back(1);
        for(int i=1; i<=rowIndex; i++){
            int temp=0;
            for(int j=0; j<result.size(); j++){
                int temp2 = temp;
                temp = result[j];
                result[j] += temp2;
            }
            result.push_back(1);
        }
        return result;
    }
}; 
Triangle (Leetcode)
Given a triangle, find the minimum path sum from top to bottom. Each step you may move to adjacent numbers on the row below.
For example, given the following triangle
[
     [2],
    [3,4],
   [6,5,7],
  [4,1,8,3]
]
 
min()在algorithm中定义：
class Solution {
public:
    int minimumTotal(vector<vector<int> > &triangle) {
        // Start typing your C/C++ solution below
        // DO NOT write int main() function
        int numRow = triangle.size();
        if(numRow < 1)
            return 0;
        vector<int> length = triangle[numRow-1];
        for(int i=numRow-2; i>=0; i--)
            for(int j=0; j<=i; j++)
                length[j] = 
							triangle[i][j]+min(length[j],length[j+1]);
        return length[0];
    }
}; 
Jump Game (Leetcode)
Given an array of non-negative integers, you are initially positioned at the first index of the array.
Each element in the array represents your maximum jump length at that position.
Determine if you are able to reach the last index.
For example:
A = [2,3,1,1,4], return true.
A = [3,2,1,0,4], return false.
 
当心[0]的情况
class Solution {
public:
    bool canJump(int A[], int n) {
        // Start typing your C/C++ solution below
        // DO NOT write int main() function
        if(A==NULL || n<1)
            return false;
        int range=0;
        for(int i=0; i<=range; i++){
            if(i+A[i] > range)
                range = i+A[i];
            if(range >= n-1)
                return true;
        }
        return false;
    }
};
 
Permutations (Leetcode)
Given a collection of numbers, return all possible permutations.
For example,
[1,2,3] have the following permutations:
[1,2,3], [1,3,2], [2,1,3], [2,3,1], [3,1,2], and [3,2,1].
 
class Solution {
private:
    void permutation(vector<int>& num,int idx,vector<vector<int> >& result)
    {
        if(idx >= num.size()){
            result.push_back(num);
            return;
        }
        for(int i=idx; i<num.size(); i++){
            int temp = num[idx];
            num[idx] = num[i];
            num[i] = temp;
            permutation(num,idx+1,result);
            num[i] = num[idx];
            num[idx] = temp;
        }
    }
public:
    vector<vector<int> > permute(vector<int> &num) {
        // Start typing your C/C++ solution below
        // DO NOT write int main() function
        vector<vector<int> > result;
        int length = num.size();
        if(length < 1)
            return result;
        permutation(num,0,result);
        return result;
    }
};
 
Permutations II (Leetcode)
Given a collection of numbers that might contain duplicates, return all possible unique permutations.
For example,
[1,1,2] have the following unique permutations:
[1,1,2], [1,2,1], and [2,1,1].
 
map的用法
class Solution {
private:
void permutation(vector<int>& seq,int count,
map<int,int>& left,vector<vector<int> >& result)
    {
        if(count < 1){
            result.push_back(seq);
            return;
        }
        for(map<int,int>::iterator iter=left.begin(); 
											iter!=left.end(); ++iter){
            if(iter->second > 0){
                iter->second--;
                seq.push_back(iter->first);
                permutation(seq,count-1,left,result);
                seq.pop_back();
                iter->second++;
            }
        }
    }
public:
    vector<vector<int> > permuteUnique(vector<int> &num) {
        // Start typing your C/C++ solution below
        // DO NOT write int main() function
        vector<vector<int> > result;
        if(num.size() < 1)
            return result;
        map<int,int> left;
        for(vector<int>::iterator iter=num.begin(); 
iter<num.end(); ++iter){
            if(left.count(*iter))
                left[*iter]++;
            else
                left[*iter] = 1;
        }
        vector<int> seq;
        permutation(seq,num.size(),left,result);
        return result;
    }
};
 
Best Time to Buy and Sell Stock (Leetcode)
Say you have an array for which the ith element is the price of a given stock on day i.
If you were only permitted to complete at most one transaction (ie, buy one and sell one share of the stock), design an algorithm to find the maximum profit.
 
注意不是找最大最小值，最小值必须在最大值前面
class Solution {
public:
    int maxProfit(vector<int> &prices) {
        // Start typing your C/C++ solution below
        // DO NOT write int main() function
        if(prices.size() < 1)
            return 0;
        int curMin = INT_MAX;
        int maxPro = 0;
        for(vector<int>::iterator iter=prices.begin(); iter!=prices.end(); ++iter){
            if(*iter < curMin)
                curMin = *iter;
            if(maxPro < *iter-curMin)
                maxPro = *iter-curMin;
        }
        return maxPro;
    }
};
 
Best Time to Buy and Sell Stock II (Leetcode)
Say you have an array for which the ith element is the price of a given stock on day i.
Design an algorithm to find the maximum profit. You may complete as many transactions as you like (ie, buy one and sell one share of the stock multiple times). However, you may not engage in multiple transactions at the same time (ie, you must sell the stock before you buy again).
 
class Solution {
public:
    int maxProfit(vector<int> &prices) {
        // Start typing your C/C++ solution below
        // DO NOT write int main() function
        if(prices.size() < 2)
            return 0;
        int result = 0;
        for(vector<int>::iterator iter=prices.begin()+1; 
											iter!=prices.end(); ++iter)
            if(*iter > *(iter-1))
                result += (*iter-*(iter-1));
        return result;
    }
};
 
Best Time to Buy and Sell Stock III (Leetcode)
Say you have an array for which the ith element is the price of a given stock on day i.
Design an algorithm to find the maximum profit. You may complete at most two transactions.
Note:
You may not engage in multiple transactions at the same time (ie, you must sell the stock before you buy again).
 
class Solution {
public:
    int maxProfit(vector<int> &prices) {
        // Start typing your C/C++ solution below
        // DO NOT write int main() function
        if(prices.size() < 2)
            return 0;
        vector<int> formerProfit;   // 在该天或之前卖出的最大利润
        int maxP = 0;
        int curMin = INT_MAX;
        for(int i=0; i<prices.size(); i++){
            if(prices[i] < curMin)
                curMin = prices[i];
            if(prices[i]-curMin > maxP)
                maxP = prices[i]-curMin;
            formerProfit.push_back(maxP);
        }
        int result = 0;
        int curMax = INT_MIN;
		// 加上该天或之后买进的最大利润
        for(int i=prices.size()-1; i>=0; i--){
            if(prices[i] > curMax)
                curMax = prices[i];
            if(curMax-prices[i]+formerProfit[i] > result)
                result = curMax-prices[i]+formerProfit[i];
        }
        return result;
    }
};
 
Unique Paths (Leetcode)
A robot is located at the top-left corner of a m x n grid (marked 'Start' in the diagram below).
The robot can only move either down or right at any point in time. The robot is trying to reach the bottom-right corner of the grid (marked 'Finish' in the diagram below).
How many possible unique paths are there?
空间O(min(m,n))，用dp（组合的话容易溢出）
 
class Solution {
public:
    int uniquePaths(int m, int n) {
        // Start typing your C/C++ solution below
        // DO NOT write int main() function
        if(m > n){
            m ^= n;
            n ^= m;
            m ^= n;
        }
        vector<int> path(m,1);
        for(int i=1; i<n; i++)
            for(int j=1; j<m; j++)
                path[j] += path[j-1];
        return path.back();
    }
};
 
Unique Paths II (Leetcode)
Follow up for "Unique Paths":
Now consider if some obstacles are added to the grids. How many unique paths would there be?
An obstacle and empty space is marked as 1 and 0 respectively in the grid.
For example,
There is one obstacle in the middle of a 3x3 grid as illustrated below.
[
  [0,0,0],
  [0,1,0],
  [0,0,0]
]
The total number of unique paths is 2.
Note: m and n will be at most 100.
 
注意第一行和第一列，有1了就堵住了
class Solution {
public:
    int uniquePathsWithObstacles(vector<vector<int> > &obstacleGrid) {
        // Start typing your C/C++ solution below
        // DO NOT write int main() function
        int m = obstacleGrid.size();
        if(m < 1)
            return 0;
        int n = obstacleGrid[0].size();
        if(n < 1)
            return 0;
        vector<int> path(n,0);
        for(int i=0; obstacleGrid[0][i]==0; i++)
            path[i] = 1;
        for(int i=1; i<m; i++){
            if(obstacleGrid[i][0] == 1)
                path[0] = 0;
            for(int j=1; j<n; j++){
                if(obstacleGrid[i][j] == 1)
                    path[j] = 0;
                else
                    path[j] += path[j-1];
            }
        }
        return path.back();
    }
};
 
Search in Rotated Sorted Array (Leetcode)
Suppose a sorted array is rotated at some pivot unknown to you beforehand.
(i.e., 0 1 2 4 5 6 7 might become 4 5 6 7 0 1 2).
You are given a target value to search. If found in the array return its index, otherwise return -1.
You may assume no duplicate exists in the array.
 
逻辑复杂得一逼
class Solution {
public:
    int search(int A[], int n, int target) {
        // Start typing your C/C++ solution below
        // DO NOT write int main() function
        if(A == NULL)
            return -1;
        int start=0,stop=n-1;
        while(start <= stop){
            int mid = start+(stop-start)/2;
            if(A[mid] == target)
                return mid;
            else if(A[mid] > target){
                if(A[start] < A[stop])
                    stop = mid -1;
                else if(A[mid]>=A[start] && target<=A[stop])
                    start = mid+1;
                else
                    stop = mid-1;
            }
            else{
                if(A[start] < A[stop])
                    start = mid+1;
                else if(target>=A[start] && A[mid]<=A[stop])
                    stop = mid-1;
                else
                    start = mid+1;
            }
        }
        return -1;
    }
};
最外层if转化为A[mid]与A[start]的比较可以更简化一些	
 
找出现三次的数
数组A中，除了某一个数字x之外，其他数字都出现了三次，而x出现了一次。请给出最快的方法，找到x。
 
这一类的题目，即使简单的异或不能解决，也可以从二进制位、位操作方面去考虑，总之这样的大方向是不会错的。
题目中，如果数组中的元素都是三个三个出现的，那么从二进制表示的角度，每个位上的1加起来，应该可以整除3。如果有一个数x只出现一次，会是什么情况呢？
如果某个特定位上的1加起来，可以被3整除，说明对应x的那位是0，因为如果是1，不可能被3整除
如果某个特定位上的1加起来，不可以被3整除，说明对应x的那位是1
根据上面的描述，我们可以开辟一个大小为32的数组，第0个元素表示，A中所有元素的二进制表示的最低位的和，依次类推。最后，再转换为十进制数即可。这里要说明的是，用一个大小为32的整数数组表示，同样空间是O(1)的。
 
Search for a Range (Leetcode)
Given a sorted array of integers, find the starting and ending position of a given target value.
Your algorithm's runtime complexity must be in the order of O(log n).
If the target is not found in the array, return [-1, -1].
For example,
Given [5, 7, 7, 8, 8, 10] and target value 8,
return [3, 4].
 
class Solution {
private:
    int searchStart(int A[], int start, int stop, int target)
    {
        while(start <= stop){
            int mid = start+(stop-start)/2;
            if(A[mid]==target && (mid==start ||A[mid-1]<target))
                return mid;
            else if(A[mid] < target)
                start = mid+1;
            else
                stop = mid-1;
        }
        return -1;
    }
    int searchStop(int A[], int start, int stop, int target)
    {
        while(start <= stop){
            int mid = start+(stop-start)/2;
            if(A[mid]==target && (mid==stop || A[mid+1]>target))
                return mid;
            else if(A[mid] > target)
                stop = mid-1;
            else
                start = mid+1;
        }
        return -1;
    }
public:
    vector<int> searchRange(int A[], int n, int target) {
        // Start typing your C/C++ solution below
        // DO NOT write int main() function
        vector<int> result;
        if(A==NULL || n<1)
            return result;
        result.push_back(searchStart(A,0,n-1,target));
        result.push_back(searchStop(A,0,n-1,target));
        return result;
    }
};
 
Search Insert Position (Leetcode)
Given a sorted array and a target value, return the index if the target is found. If not, return the index where it would be if it were inserted in order.
You may assume no duplicates in the array.
Here are few examples.
[1,3,5,6], 5 → 2
[1,3,5,6], 2 → 1
[1,3,5,6], 7 → 4
[1,3,5,6], 0 → 0
 
class Solution {
public:
    int searchInsert(int A[], int n, int target) {
        // Start typing your C/C++ solution below
        // DO NOT write int main() function
        if(A==NULL || n<0)
            return -1;
        int start = 0;
        int stop = n-1;
        int mid = 0;
        while(start <= stop){
            mid = start+(stop-start)/2;
            if(A[mid] == target)
                return mid;
            else if(A[mid] > target)
                stop = mid-1;
            else
                start = mid+1;
        }
        return start;
    }
};
 
Length of Last Word (Leetcode)
Given a string s consists of upper/lower-case alphabets and empty space characters ' ', return the length of last word in the string.
If the last word does not exist, return 0.
Note: A word is defined as a character sequence consists of non-space characters only.
For example, 
Given s = "Hello World",
return 5.
 
注意末尾为连续空格的情况
class Solution {
public:
    int lengthOfLastWord(const char *s) {
        // Start typing your C/C++ solution below
        // DO NOT write int main() function
        if(s == NULL)
            return 0;
        int length = 0;
        const char* ptr=s;
        while(*ptr != '\0'){
            if(ptr!=s && *(ptr-1)==' ' && *ptr!=' ')
                length = 0;
            if(*ptr != ' ')
                length++;
            ptr++;
        }
        return length;
    }
};
 
Search a 2D Matrix
Write an efficient algorithm that searches for a value in an m x n matrix. This matrix has the following properties:
Integers in each row are sorted from left to right.
The first integer of each row is greater than the last integer of the previous row.
For example,
Consider the following matrix:
[
  [1,   3,  5,  7],
  [10, 11, 16, 20],
  [23, 30, 34, 50]
]
Given target = 3, return true.
 
class Solution {
public:
    bool searchMatrix(vector<vector<int> > &matrix, int target) {
        // Start typing your C/C++ solution below
        // DO NOT write int main() function
        int m = matrix.size();
        if(m < 1)
            return false;
        int n = matrix[0].size();
        if(n < 1)
            return false;
        for(int i=0; i<m; i++)
            for(int j=n-1; j>=0&&matrix[i][j]>=target; j--)
                if(matrix[i][j] == target)
                    return true;
        return false;
    }
};
 
Climbing Stairs (Leetcode)
You are climbing a stair case. It takes n steps to reach to the top.
Each time you can either climb 1 or 2 steps. In how many distinct ways can you climb to the top?
 
class Solution {
public:
    int climbStairs(int n) {
        // Start typing your C/C++ solution below
        // DO NOT write int main() function
        int fib[2];
        fib[0] = 1;
        fib[1] = 1;
        if(n < 2)
            return fib[n];
        int idx=1;
        for(int i=2; i<=n; i++){
            idx = 1-idx;
            fib[idx] += fib[1-idx];
        }
        return fib[idx];
    }
};
 
Sort Colors (Leetcode)
Given an array with n objects colored red, white or blue, sort them so that objects of the same color are adjacent, with the colors in the order red, white and blue.
Here, we will use the integers 0, 1, and 2 to represent the color red, white, and blue respectively.
Note:
You are not suppose to use the library's sort function for this problem.
Follow up:
A rather straight forward solution is a two-pass algorithm using counting sort.
First, iterate the array counting number of 0's, 1's, and 2's, then overwrite array with total number of 0's, then 1's and followed by 2's.
Could you come up with an one-pass algorithm using only constant space?
 
class Solution {
public:
    void sortColors(int A[], int n) {
        // Start typing your C/C++ solution below
        // DO NOT write int main() function
        if(A==NULL || n<1)
            return;
        int* end0 = A;
        int* end1 = A+n-1;
        int* ptr1 = A;
        while(ptr1 <= end1){
            if(*ptr1 == 0)
                iter_swap(ptr1++,end0++);	// 因为ptr1指的肯定是1所以++
            else if(*ptr1 == 2)
                iter_swap(ptr1,end1--);
            else
                ptr1++;
        }
    }
};
 
Minimum Window Substring (Leetcode)
Given a string S and a string T, find the minimum window in S which will contain all the characters in T in complexity O(n).
For example,
S = "ADOBECODEBANC"
T = "ABC"
Minimum window is "BANC".
Note:
If there is no such window in S that covers all characters in T, return the emtpy string "".
If there are multiple such windows, you are guaranteed that there will always be only one unique minimum window in S.
 
start后移到哪里需要斟酌
class Solution {
public:
    string minWindow(string S, string T) {
        // Start typing your C/C++ solution below
        // DO NOT write int main() function
        vector<int> dict(256,-1);
        int numC=0;
        for(int i=0; i<T.size(); i++){
            if(dict[T[i]] > 0)
                dict[T[i]]++;
            else{
                dict[T[i]] = 1;
                numC++;
            }
        }
        int start=0;
        int stop=0;
        int chLeft=numC;
        vector<int> contain(256,0);
        int minStart = -1;
        int minLength = INT_MAX;
        while(true){
            while(stop<S.length() && chLeft){
                char curr = S[stop];
                if(dict[curr] > 0){
                    contain[curr]++;
                    if(contain[curr] == dict[curr]) // >=的话会重复计算
                        chLeft--;
                }
                stop++;
            }
			if(chLeft)
				break;
            while(start<stop && !chLeft){
                if(stop-start < minLength){
                    minStart = start;
                    minLength = stop-start;
                }
                char curr = S[start];
                if(dict[curr] > 0){
                    contain[curr]--;
                    if(contain[curr] == dict[curr]-1)
                        chLeft++;
                }
                start++;
            }
            if(stop == S.length())
                break;
        }
		if(minStart >= 0)
			return S.substr(minStart,minLength);
		else return string("");
    }
};
 
Combinations (Leetcode)
Given two integers n and k, return all possible combinations of k numbers out of 1 ... n.
For example,
If n = 4 and k = 2, a solution is:
[
  [2,4],
  [3,4],
  [2,3],
  [1,2],
  [1,3],
  [1,4],
]
 
注意组合是不能重复的
class Solution {
private:
    vector<vector<int> > result;
    vector<int> temp;
    void combineHelper(int low,int high,int leftN)
    {
        if(leftN == 0){
            result.push_back(temp);
            return;
        }
        for(int i=low; i+leftN-1<=high; i++){
            temp.push_back(i);
            combineHelper(i+1,high,leftN-1);
            temp.pop_back();
        }
    }
public:
    vector<vector<int> > combine(int n, int k) {
        // Start typing your C/C++ solution below
        // DO NOT write int main() function
        result.clear();
        if(k<1 || k>n)
            return result;
        temp.clear();
        combineHelper(1,n,k);
        return result;
    }
};
 
Subsets (Leetcode)
Given a set of distinct integers, S, return all possible subsets.
Note:
Elements in a subset must be in non-descending order.
The solution set must not contain duplicate subsets.
For example,
If S = [1,2,3], a solution is:
[
  [3],
  [1],
  [2],
  [1,2,3],
  [1,3],
  [2,3],
  [1,2],
  []
]
 
递归写法
class Solution {
private:
    vector<vector<int> > result;
    vector<int> temp;
    void subsetsHelper(int start, vector<int> &S) {
        result.push_back(temp);
        for(int i=start; i<S.size(); i++){
            temp.push_back(S[i]);
            subsetsHelper(i+1,S);
            temp.pop_back();
        }
    }
public:
    vector<vector<int> > subsets(vector<int> &S) {
        // Start typing your C/C++ solution below
        // DO NOT write int main() function
        result.clear();
        temp.clear();
        sort(S.begin(),S.end());
        subsetsHelper(0,S);
        return result;
    }
};
循环写法：
vector<vector<int> > subsets(vector<int> &S) {
    sort(S.begin(), S.end());
    vector<vector<int> > v(1);
    for(int i = 0; i < S.size(); ++i) {
        int j = v.size();
        while(j-- > 0) {
            v.push_back(v[j]);
            v.back().push_back(S[i]);
        }
    }
    return v;
}
 
冲突兼容
输入很多名字对，每一对都表示两个有冲突的人。是否可以将这些人分成两组，每组中的人都没有冲突？ 
可以作为染色问题。这里用一对set保存无冲突的一组，新输入的名字存在的话可能需要合并
inline int another(const int& i) { return (i/2)*4+1-i; }
bool isConflict(vector<pair<string,string> > nameList)
	int pairNum = nameList.size();
	string temp1,temp2;
	vector<set<string>> inconflict;
	set<string> tempSet1,tempSet2;
	bool result = true;
	for(int p=0; p<pairNum; p++){
		temp1 = nameList[i].first;
		temp2 = nameList[i].second;
		tempSet1.clear();
		tempSet2.clear();
		int contain1=-1,contain2=-1;
		for(int i=0; i<inconflict.size(); i++){
			if(inconflict[i].count(temp1))
				contain1 = i;
			if(inconflict[i].count(temp2))
				contain2 = i;
		}
		if(contain1<0 && contain2<0){
			tempSet1.insert(temp1);
			inconflict.push_back(tempSet1);
			tempSet2.insert(temp2);
			inconflict.push_back(tempSet2);
		}
		else if(contain1>=0 && contain2<0)
			inconflict[another(contain1)].insert(temp2);
		else if(contain1<0 && contain2>=0)
			inconflict[another(contain2)].insert(temp1);
		else if(contain1 == contain2)
			return false;
		else if(contain1 != another(contain2)){
			inconflict[contain1].insert(
				inconflict[another(contain2)].begin(),
				inconflict[another(contain2)].end());
			inconflict[another(contain1)].insert(
				inconflict[contain2].begin(),
				inconflict[contain2].end());
			inconflict.erase(
				inconflict.begin()+
					min(contain2,another(contain2)),
				inconflict.begin()+
					max(contain2,another(contain2))+1);
		}
	}
	return true;
} 
LCA of BST
二叉搜索树的最低公共父节点 
Node *LCA(Node *root, Node *p, Node *q) {
	if (!root || !p || !q) return NULL;
	if (max(p->data, q->data) < root->data)
		return LCA(root->left, p, q);
	else if (min(p->data, q->data) > root->data)
		return LCA(root->right, p, q);
	else
		return root;
}
 
LCA with Links to Parents
 
int getHeight(Node *p) {
	int height = 0;
	while (p) {
		height++;
		p = p->parent;
	}
	return height;
}
// As root->parent is NULL, we don't need to pass root in.
Node *LCA(Node *p, Node *q) {
	int h1 = getHeight(p);
	int h2 = getHeight(q);
	// swap both nodes in case p is deeper than q.
	if (h1 > h2) {
		swap(h1, h2);
		swap(p, q);
	}
	// invariant: h1 <= h2.
	int dh = h2 - h1;
	for (int h = 0; h < dh; h++)
		q = q->parent;
	while (p && q) {
		if (p == q) return p;
		p = p->parent;
		q = q->parent;
	}
	return NULL;	// p and q are not in the same tree
}
 
Word Search (Leetcode)
Given a 2D board and a word, find if the word exists in the grid.
The word can be constructed from letters of sequentially adjacent cell, where "adjacent" cells are those horizontally or vertically neighboring. The same letter cell may not be used more than once.
For example,
Given board =
[
  ["ABCE"],
  ["SFCS"],
  ["ADEE"]
]
word = "ABCCED", -> returns true,
word = "SEE", -> returns true,
word = "ABCB", -> returns false.
 
各种条件判断放在递归函数的头上可以简化代码
class Solution {
private:
    vector<vector<bool> > used;
    int nRow,nCol;
    bool existHelper(vector<vector<char> > &board,int row,int col,string &word,int idx){
        if(idx == word.length())    // 注意这个if和下一个的先后关系
            return true;
        if(row<0 || col<0 || row>=nRow || col>=nCol || used[row][col] || board[row][col]!=word[idx])
            return false;
        used[row][col] = true;
        if(existHelper(board,row-1,col,word,idx+1))
            return true;
        if(existHelper(board,row+1,col,word,idx+1))
            return true;
        if(existHelper(board,row,col-1,word,idx+1))
            return true;
        if(existHelper(board,row,col+1,word,idx+1))
            return true;
        used[row][col] = false;
        return false;
    }
public:
    bool exist(vector<vector<char> > &board, string word) {
        // Start typing your C/C++ solution below
        // DO NOT write int main() function
        if(word.length() < 1)
            return true;
        nRow = board.size();
        if(nRow < 1)
            return false;
        nCol = board[0].size();
        if(nCol < 1)
            return false;
        used = vector<vector<bool> >(nRow,vector<bool>(nCol,false));
        for(int i=0; i<nRow; i++)
            for(int j=0; j<nCol; j++)
                if(existHelper(board,i,j,word,0))
                    return true;
        return false;
    }
};
 
Remove Duplicates from Sorted Array II (Leetcode)
Follow up for "Remove Duplicates":
What if duplicates are allowed at most twice?
For example,
Given sorted array A = [1,1,1,2,2,3],
Your function should return length = 5, and A is now [1,1,2,2,3].
 
一定要先测试，确定应该比较哪个。如1，1，1，2，2，3
class Solution {
public:
    int removeDuplicates(int A[], int n) {
        // Start typing your C/C++ solution below
        // DO NOT write int main() function
        if(n < 3)
            return n;
        int result = 2;
        for(int i=2; i<n; i++){
            if(A[i]!=A[result-1] || A[i]!=A[result-2])
                A[result++] = A[i];
        }
        return result;
    }
};
 
Remove Duplicates from Sorted List (Leetcode)
Given a sorted linked list, delete all duplicates such that each element appear only once.
For example,
Given 1->1->2, return 1->2.
Given 1->1->2->3->3, return 1->2->3. 
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode *deleteDuplicates(ListNode *head) {
        // Note: The Solution object is instantiated only once and is reused by each test case.
        if(head == NULL)
            return head;
        ListNode *curr = head;
        ListNode *next = curr->next;
        while(next != NULL){
            if(next->val == curr->val){
                next = next->next;
                delete curr->next;
                curr->next = next;
            }
            else{
                next = next->next;
                curr = curr->next;
            }
        }
        curr->next = NULL;
        return head;
    }
}; 
Remove Duplicates from Sorted List II (Leetcode)
Given a sorted linked list, delete all nodes that have duplicate numbers, leaving only distinct numbers from the original list.
For example,
Given 1->2->3->3->4->4->5, return 1->2->5.
Given 1->1->1->2->3, return 2->3.
 
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode *deleteDuplicates(ListNode *head) {
        // Note: The Solution object is instantiated only once and is reused by each test case.
        bool dup = false;
        ListNode dummy(0);
        dummy.next = head;
        ListNode *curr = &dummy;
        while(head != NULL){
            ListNode* front = head->next;
            while(front!=NULL && front->val==head->val){
                dup = true;
                front = front->next;
                delete head->next;
                head->next = front;
            }
            if(dup){
                head = head->next;
                delete curr->next;
                curr->next = head;
                dup = false;
            }
            else{	// 这里必须有else，否则消除之后head可能已经是NULL
                curr = head;
                head = head->next;
            }
        }
        return dummy.next;
    }
}; 
Largest Rectangle in Histogram (Leetcode)
Given n non-negative integers representing the histogram's bar height where the width of each bar is 1, find the area of largest rectangle in the histogram.
 
Above is a histogram where width of each bar is 1, given height = [2,1,5,6,2,3].
 
The largest rectangle is shown in the shaded area, which has area = 10 unit.
For example,
Given height = [2,1,5,6,2,3],
return 10.
 
递增序列表示了左边界,检查到右边界则可以开始计算
class Solution {
public:
    int largestRectangleArea(vector<int> &height) {
        // Start typing your C/C++ solution below
        // DO NOT write int main() function
        stack<pair<int,int>> st;
        vector<int> h = height;
        st.push(pair<int,int>(-1,-1));
        h.push_back(0);
        int maxArea = 0;
        int i = 0;
        while(i < h.size()){
            if(h[i] > st.top().second){
                st.push(pair<int,int>(i,h[i]));
                i++;
            }
            else{
                pair<int,int> cur = st.top();
                st.pop();
                int area = (i-st.top().first-1)*cur.second;
                if(area > maxArea)
                    maxArea = area;
            }
        }
        return maxArea;
    }
};
 
Spiral Matrix (Leetcode)
Given a matrix of m x n elements (m rows, n columns), return all elements of the matrix in spiral order.
For example,
Given the following matrix:
[
 [ 1, 2, 3 ],
 [ 4, 5, 6 ],
 [ 7, 8, 9 ]
]
You should return [1,2,3,6,9,8,7,4,5].
 
右向左和下往上不一定有
class Solution {
public:
    vector<int> spiralOrder(vector<vector<int> > &matrix) {
        // Start typing your C/C++ solution below
        // DO NOT write int main() function
        int M = matrix.size();
        vector<int> result;
        if(M < 1)
            return result;
        int N = matrix[0].size();
        for(int start=0; start<(min(M,N)+1)/2; start++){
            for(int i=start; i<N-start; i++)
                result.push_back(matrix[start][i]);
            for(int i=start+1; i<M-start; i++)
                result.push_back(matrix[i][N-start-1]);
            if(start*2+1 != M)
                for(int i=N-start-2; i>=start; i--)
                    result.push_back(matrix[M-start-1][i]);
            if(start*2+1 != N)
                for(int i=M-start-2; i>start; i--)
                    result.push_back(matrix[i][start]);
        }
        return result;
    }
};
 
Spiral Matrix II (Leetcode)
Given an integer n, generate a square matrix filled with elements from 1 to n2 in spiral order.
For example,
Given n = 3,
You should return the following matrix:
[
 [ 1, 2, 3 ],
 [ 8, 9, 4 ],
 [ 7, 6, 5 ]
]
 
class Solution {
public:
    vector<vector<int> > generateMatrix(int n) {
        // Start typing your C/C++ solution below
        // DO NOT write int main() function
        vector<vector<int> > result = 
							vector<vector<int> >(n,vector<int>(n,0));
        int num = 0;
        for(int start=0; start<(n+1)/2; start++){
            for(int i=start; i<n-start; i++)
                result[start][i] = ++num;
            for(int i=start+1; i<n-start; i++)
                result[i][n-start-1] = ++num;
            if(start*2+1 != n){
                for(int i=n-start-2; i>=start; i--)
                    result[n-start-1][i] = ++num;
                for(int i=n-start-2; i>start; i--)
                    result[i][start] = ++num;
            }
        }
        return result;
    }
};
 
Two Sum (Leetcode)
Given an array of integers, find two numbers such that they add up to a specific target number.
The function twoSum should return indices of the two numbers such that they add up to the target, where index1 must be less than index2. Please note that your returned answers (both index1 and index2) are not zero-based.
You may assume that each input would have exactly one solution.
Input: numbers={2, 7, 11, 15}, target=9
Output: index1=1, index2=2
 
class Solution {
public:
    vector<int> twoSum(vector<int> &numbers, int target) {
        // Start typing your C/C++ solution below
        // DO NOT write int main() function
        vector<pair<int,int> > temp;
        for(size_t i=0; i<numbers.size(); i++)
            temp.push_back(pair<int,size_t>(numbers[i],i+1));
        sort(temp.begin(),temp.end());
        vector<int> result;
        int i=0,j=temp.size()-1;
        while(i < j){
            int sum = temp[i].first+temp[j].first;
            if(sum == target){
                result.push_back(temp[i].second);
                result.push_back(temp[j].second);
                break;
            }
            else if(sum < target)
                i++;
            else
                j--;
        }
        sort(result.begin(),result.end());
        return result;
    }
};
 
Median of Two Sorted Array (Leetcode)
There are two sorted arrays A and B of size m and n respectively. Find the median of the two sorted arrays. The overall run time complexity should be O(log (m+n)).
 
class Solution {
public:
    double findMedianSortedArrays(int A[], int m, int B[], int n) {
        // Start typing your C/C++ solution below
        // DO NOT write int main() function
        if(m > n)
            return findMedianSortedArrays(B,n,A,m);
        if(m < 3){
            int temp[6];
            int i=0;
            for(int j=0; j<m; j++)
                temp[i++] = A[j];
            int midB = (n-1)/2;
            if(n%2)
                for(int j=max(0,midB-1); j<min(n,midB+2); j++)
                    temp[i++] = B[j];
            else
                for(int j=max(0,midB-1); j<min(n,midB+3); j++)
                    temp[i++] = B[j];
            sort(temp,temp+i);
			int mid = (i-1)/2;
            if(i%2)
                return temp[mid];
            else
                return static_cast<double>(temp[mid]+temp[mid+1])/2;
        }
        int midA = (m-1)/2;
        if(A[midA] <= B[(n-1)/2])
            return findMedianSortedArrays(A+midA,m-midA,B,n-midA);
        else
            return findMedianSortedArrays(A,m-midA,B+midA,n-midA);
    }
};
 
Surrounded Regions (Leetcode)
Given a 2D board containing 'X' and 'O', capture all regions surrounded by 'X'.
A region is captured by flipping all 'O's into 'X's in that surrounded region .
For example,
X X X X
X O O X
X X O X
X O X X
After running your function, the board should be:
X X X X
X X X X
X X X X
X O X X
 
class Solution {
private:
    int nRow,nCol;
    bool flipFromEdge(int row,int col,vector<vector<char>> &board){
        if(row<0 || col<0 || row>=nRow || col>=nCol)
            return false;
        if(board[row][col] == 'X')
            return false;
        if(board[row][col] == 'r')
            return true;
        board[row][col] = 'r';
        bool result = false;
        if(flipFromEdge(row-1,col,board))
            result = true;
        if(flipFromEdge(row+1,col,board))
            result = true;
        if(flipFromEdge(row,col-1,board))
            result = true;
        if(flipFromEdge(row,col+1,board))
            result = true;
        return result;
    }
public:
    void solve(vector<vector<char>> &board) {
        // Start typing your C/C++ solution below
        // DO NOT write int main() function
        nRow = board.size();
        if(nRow < 1)
            return;
        nCol = board[0].size();
        if(nCol < 1)
            return;
        for(int i=0; i<nRow; i++){
            flipFromEdge(i,0,board);
            flipFromEdge(i,nCol-1,board);
        }
        for(int i=0; i<nCol; i++){
            flipFromEdge(0,i,board);
            flipFromEdge(nRow-1,i,board);
        }
        for(int i=0; i<nRow; i++)
            for(int j=0; j<nCol; j++)
                if(board[i][j] == 'O')
                    board[i][j] = 'X';
                else if(board[i][j] == 'r')
                    board[i][j] = 'O';
    }
};
 
Populating Next Right Pointers in Each Node (Leetcode)
Given a binary tree
    struct TreeLinkNode {
      TreeLinkNode *left;
      TreeLinkNode *right;
      TreeLinkNode *next;
    }
Populate each next pointer to point to its next right node. If there is no next right node, the next pointer should be set to NULL.
Initially, all next pointers are set to NULL.
Note:
You may only use constant extra space.
You may assume that it is a perfect binary tree (ie, all leaves are at the same level, and every parent has two children).
For example,
Given the following perfect binary tree,
         1
       /  \
      2    3
     / \  / \
    4  5  6  7
After calling your function, the tree should look like:
         1 -> NULL
       /  \
      2 -> 3 -> NULL
     / \  / \
    4->5->6->7 -> NULL
 
/**
 * Definition for binary tree with next pointer.
 * struct TreeLinkNode {
 *  int val;
 *  TreeLinkNode *left, *right, *next;
 *  TreeLinkNode(int x) : val(x), left(NULL), right(NULL), next(NULL) {}
 * };
 */
class Solution {
public:
    void connect(TreeLinkNode *root) {
        // Start typing your C/C++ solution below
        // DO NOT write int main() function
        if(root == NULL)
            return;
        root->next = NULL;
        TreeLinkNode *head = root;
        while(head->left != NULL){
            TreeLinkNode *cur = head;
            while(cur->next != NULL){
                cur->left->next = cur->right;
                cur->right->next = cur->next->left;
                cur = cur->next;
            }
            cur->left->next = cur->right;
            cur->right->next = NULL;
            head = head->left;
        }
    }
};
 
Populating Next Right Pointers in Each Node II (Leetcode)
Follow up for problem "Populating Next Right Pointers in Each Node".
What if the given tree could be any binary tree? Would your previous solution still work?
Note:
You may only use constant extra space.
For example,
Given the following binary tree,
         1
       /  \
      2    3
     / \    \
    4   5    7
After calling your function, the tree should look like:
         1 -> NULL
       /  \
      2 -> 3 -> NULL
     / \    \
    4-> 5 -> 7 -> NULL
 
复杂题一定要注意分步以及初始、结束条件
/**
 * Definition for binary tree with next pointer.
 * struct TreeLinkNode {
 *  int val;
 *  TreeLinkNode *left, *right, *next;
 *  TreeLinkNode(int x) : val(x), left(NULL), right(NULL), next(NULL) {}
 * };
 */
class Solution {
public:
    void connect(TreeLinkNode *root) {
        // Note: The Solution object is instantiated only once and is reused by each test case.void connect(TreeLinkNode * n) {
        while (root != NULL) {
            TreeLinkNode *lowerFirst = NULL;
            TreeLinkNode *currPrev = NULL;
            for (; root!=NULL; root=root->next){
                if(lowerFirst == NULL)
                    lowerFirst = (root->left!=NULL ? 
												root->left:root->right);
                if(root->left){
                    if(currPrev != NULL)
                        currPrev->next = root->left;
                    currPrev = root->left;
                }
                if(root->right){
                    if(currPrev != NULL)
                        currPrev->next = root->right;
                    currPrev = root->right;
                }
            }
            root = lowerFirst;
        }
    }
};
 
Flatten Binary Tree to Linked List (Leetcode)
Given a binary tree, flatten it to a linked list in-place.
For example,
Given
         1
        / \
       2   5
      / \   \
     3   4   6
The flattened tree should look like:

   1
    \
     2
      \
       3
        \
         4
          \
           5
            \
             6
 
注意指针传参要是引用
class Solution {
private:
    void flattenHelper(TreeNode *root,TreeNode *&head,TreeNode *&tail){
        head = NULL;
        tail = NULL;
        if(root == NULL)
            return;
        TreeNode *head1 = NULL;
		TreeNode *head2 = NULL;
		TreeNode *tail1 = NULL;
		TreeNode *tail2 = NULL;
        flattenHelper(root->left,head1,tail1);
        flattenHelper(root->right,head2,tail2);
		root->left = NULL;
        if(head1 != NULL)
            root->right = head1;
        else
            root->right = head2;
        if(tail1 != NULL)
            tail1->right = head2;
        head = root;
        if(tail1==NULL && tail2==NULL)
            tail = root;
        else if(tail2==NULL)
            tail = tail1;
        else
            tail = tail2;
    }
public:
    void flatten(TreeNode *root) {
        // Start typing your C/C++ solution below
        // DO NOT write int main() function
        TreeNode *head = NULL;
		TreeNode *tail = NULL;
        flattenHelper(root,head,tail);
    }
};
高端：
class Solution {
public:
	void flatten(TreeNode *root) {
		if (!root) return;
		TreeNode* left = root->left;
		TreeNode* right = root->right;
		if (left) {
			root->right = left;
			root->left = NULL;
			TreeNode* rightmost = left;
			while(rightmost->right) {rightmost = rightmost->right;}
			rightmost->right = right;
		}
		flatten(root->right); 
	}
};
 
Distinct Subsequences (Leetcode)
Given a string S and a string T, count the number of distinct subsequences of T in S.
A subsequence of a string is a new string which is formed from the original string by deleting some (can be none) of the characters without disturbing the relative positions of the remaining characters. (ie, "ACE" is a subsequence of "ABCDE" while "AEC" is not).
Here is an example:
S = "rabbbit", T = "rabbit"
Return 3.
 
递归（超时）：
class Solution {
private:
    string first,second;
    int count;
    void numDistinctHelper(int idx1,int idx2){
        if(idx2 == second.size()){
            count++;
            return;
        }
        if(idx1 == first.size())
            return;
        if(first[idx1] == second[idx2])
            numDistinctHelper(idx1+1,idx2+1);
        numDistinctHelper(idx1+1,idx2);
    }
public:
    int numDistinct(string S, string T) {
        // Start typing your C/C++ solution below
        // DO NOT write int main() function
        first = S;
        second = T;
        count = 0;
        numDistinctHelper(0,0);
        return count;
    }
};
DP：
class Solution {
public:
    int numDistinct(string S, string T) {
        vector<int> f(T.size()+1);
        f[T.size()] = 1;
        for (int i = S.size()-1; i >= 0; --i)
            for (int j = 0; j < T.size(); ++j)
                f[j] += (S[i]==T[j])*f[j+1];
        return f[0];
    }
};
 
Minimum Depth of Binary Tree (Leetcode)
Given a binary tree, find its minimum depth.
The minimum depth is the number of nodes along the shortest path from the root node down to the nearest leaf node.
 
只有一个儿子为NULL的不是叶子
/**
 * Definition for binary tree
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    int minDepth(TreeNode *root) {
        // Start typing your C/C++ solution below
        // DO NOT write int main() function
        queue<pair<TreeNode*,int> > q;
        if(root == NULL)
            return 0;
        q.push(pair<TreeNode*,int>(root,1));
        while(true){
            pair<TreeNode*,int> temp = q.front();
            q.pop();
            if(temp.first->left==NULL && temp.first->right==NULL)
                return temp.second;
            if(temp.first->left != NULL)
                q.push(pair<TreeNode*,int>(temp.first->left,temp.second+1));
            if(temp.first->right != NULL)
                q.push(pair<TreeNode*,int>(temp.first->right,temp.second+1));
        }
        return 0;
    }
};
 
Balanced Binary Tree (Leetcode)
Given a binary tree, determine if it is height-balanced.
For this problem, a height-balanced binary tree is defined as a binary tree in which the depth of the two subtrees of every node never differ by more than 1.
 
/**
 * Definition for binary tree
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
private:
    bool isBalanced(TreeNode *root,int &depth){
        if(root == NULL){
            depth = -1;
            return true;
        }
        int depthL=-1,depthR=-1;
        bool balancedL = isBalanced(root->left,depthL);
        bool balancedR = isBalanced(root->right,depthR);
        depth = max(depthL,depthR)+1;
        return balancedL&&balancedR&&abs(depthL-depthR)<=1;
    }
public:
    bool isBalanced(TreeNode *root) {
        // Start typing your C/C++ solution below
        // DO NOT write int main() function
        int depth;
        return isBalanced(root,depth);
    }
};
 
Partition List (Leetcode)
Given a linked list and a value x, partition it such that all nodes less than x come before nodes greater than or equal to x.
You should preserve the original relative order of the nodes in each of the two partitions.
For example,
Given 1->4->3->2->5->2 and x = 3,
return 1->2->2->4->3->5.
 
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode *partition(ListNode *head, int x) {
        // Start typing your C/C++ solution below
        // DO NOT write int main() function
        ListNode dummy(0);
        dummy.next = head;
        ListNode *lessPre = &dummy;
        ListNode *cur = head;
        ListNode dummy1(0);
        ListNode *largerPre = &dummy1;
        while(cur != NULL){
            if(cur->val < x){
                lessPre->next = cur;
                lessPre = cur;
            }
            else{
                largerPre->next = cur;
                largerPre = cur;
            }
            cur = cur->next;
        }
        lessPre->next = dummy1.next;
        largerPre->next = NULL;
        return dummy.next;
    }
};
 
Maximum Depth of Binary Tree (Leetcode)
Given a binary tree, find its maximum depth.
The maximum depth is the number of nodes along the longest path from the root node down to the farthest leaf node.
 
/**
 * Definition for binary tree
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    int maxDepth(TreeNode *root) {
        // Start typing your C/C++ solution below
        // DO NOT write int main() function
        if(root == NULL)
            return 0;
        int depthLeft = maxDepth(root->left);
        int depthRight = maxDepth(root->right);
        return max(depthLeft,depthRight)+1;
    }
};
 
Validate Binary Search Tree (Leetcode)
Given a binary tree, determine if it is a valid binary search tree (BST).
Assume a BST is defined as follows:
The left subtree of a node contains only nodes with keys less than the node's key.
The right subtree of a node contains only nodes with keys greater than the node's key.
Both the left and right subtrees must also be binary search trees.
confused what "{1,#,2,3}" means? > read more on how binary tree is serialized on OJ.

OJ's Binary Tree Serialization:
The serialization of a binary tree follows a level order traversal, where '#' signifies a path terminator where no node exists below.
Here's an example:
   1
  / \
 2   3
    /
   4
    \
     5
The above binary tree is serialized as "{1,2,3,#,#,4,#,#,5}".
 
注意INT_MAX和INT_MIN的情况
/**
 * Definition for binary tree
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
private:
    bool isValidBST(TreeNode *root,int lBound,int uBound) {
        if(root == NULL)
            return true;
        bool isMid = root->val<=uBound && root->val>=lBound;
        if(root->val == INT_MIN)
            return root->left==NULL && 
					isValidBST(root->right,INT_MIN+1,uBound);
        if(root->val == INT_MAX)
            return root->right==NULL && 
					isValidBST(root->left,lBound,INT_MAX-1);
        return isMid && isValidBST(root->left,lBound,root->val-1)
                && isValidBST(root->right,root->val+1,uBound);
    }
public:
    bool isValidBST(TreeNode *root) {
        // Start typing your C/C++ solution below
        // DO NOT write int main() function
        return isValidBST(root,INT_MIN,INT_MAX);
    }
};
 
Restore IP Address (Leetcode)
Given a string containing only digits, restore it by returning all possible valid IP address combinations.
For example:
Given "25525511135",
return ["255.255.11.135", "255.255.111.35"]. (Order does not matter)
 
注意0开头
class Solution {
private:
    vector<string> partition;
    void helper(string &s,int idx,vector<string> &result){
        int len = s.size();
        if(idx > len)
            return;
        if(partition.size() == 4){
            if(idx == len){	// 这个和外层若交换的话，会降低效率（很长的序列）
                string temp;
                for(int i=0; i<4; i++)
                    temp = temp+partition[i]+".";
                temp.pop_back();
                result.push_back(temp);
            }
            return;
        }
        partition.push_back(string(""));
        for(int i=1; i<4; i++){
            if((i==1||s[idx]!='0') && 
									atoi(s.substr(idx,i).c_str())<256){
                partition.back() = s.substr(idx,i);
                helper(s,idx+i,result);
            }
        }
        partition.pop_back();
    }
public:
    vector<string> restoreIpAddresses(string s) {
        // Start typing your C/C++ solution below
        // DO NOT write int main() function
        vector<string> result;
        helper(s,0,result);
        return result;
    }
};
 
字符串中去除b和ac
删除字符串中的“b”和“ac”，需要满足如下的条件：
字符串只能遍历一次
不能够使用额外的空间
例如：
acbac   ==>  ""
aaac    ==>  aa
ababac  ==>  aa
bbbbd   ==>  d
进一步思考：如何处理aaccac呢，需要做哪些改变呢？
 
void Filter(char str[]){
	if(str == NULL)
		return;
	char *front = str;
	char *back = str;
	while(*front != '\0'){
		if(*front == 'b')
			front++;
		else if(*front=='c' && back!=str && *(back-1)=='a'){
			back--;
			front++;
		}
		else
			*back++ = *front++;
	}
	*back = '\0';
}
 
锯齿状数组(微策略)
给定一个N个整数元素的数组，元素分别为A1, A2, A3....AN, 将数组变为A1 < A2 > A3 < A4.....的锯齿状数组
 
可以O(n)，用nth Element(就是类似快排的Partition)找到中位数O(n)，然后把大于中位数的放在奇数下标，小于的放在偶数下标就可以了，也是O(n)
 
First Missing Possitive (Leetcode)
Given an unsorted integer array, find the first missing positive integer.
For example,
Given [1,2,0] return 3,
and [3,4,-1,1] return 2.
Your algorithm should run in O(n) time and uses constant space.
 
class Solution {
public:
    int firstMissingPositive(int A[], int n) {
        // Start typing your C/C++ solution below
        // DO NOT write int main() function
        if(A==NULL || n<0)	// 不能n<1
            return 0;
        int i = 0;
        while(i < n){
            if(A[i]<1 || A[i]==i+1 || A[i]>n || A[i]==A[A[i]-1])
                i++;	// 注意最后一个条件，没有的话会死循环
            else
                swap(A[i],A[A[i]-1]);
        }
        for(i=0; i<n; i++)
            if(A[i] != i+1)
                break;
        return i+1;
    }
};
 
去掉C/C++程序代码中的注释
 
（1）在读取到的一行中查找“//”，如果找到，则把“//”及其后的部分扔掉。
（2）在读取到的一行中查找“/*”，记录位置pos1，然后再在这行中查找“*/”，如果找到，也记录位置pos2，扔掉它们与其中的内容，以pos2开始，继续查找“/*”；如果在当前行中没有找到，则去掉当前行中“/*”及其后的内容，读取新的一行，查找“*/”，如没有。则去掉读取到的这一行，再读一行，查找“*/”，如找到，记录位置pos2，去掉这一行的0到pos2之间的字符。
（3）进行步骤1、步骤2，直到程序结束。
编程时要考虑的特殊情况i：
“”中的“//”“/*”
‘’中的“//”“/*”
“//”与“/*”的嵌套关系，比如///* 、/*  //*/
 
Levenshtein Distance
输入两个字符串求列文斯坦距离
 
int ldistance(const string source,const string target)
{
	int n = source.length();
	int m = target.length();
	if(m == 0)
		return n;
	if(n == 0)
		return m;
	vector<vector<int> > matrix(n+1,vector<int>(0,m+1));
	for(int i=1; i<=n; i++){
		const char si = source[i-1];
		for(int j=1; j<=m; j++){
			const char dj = target[j-1];
			int cost;
			if(si == dj)
				cost=0;
			else
				cost=1;
			const int above = matrix[i-1][j]+1;
			const int left = matrix[i][j-1]+1;
			const int diag = matrix[i-1][j-1]+cost;
			matrix[i][j] = min(above,min(left,diag));
		}
	}
	return matrix[n][m];
}
 
Palindrome Partitioning (Leetcode)
Given a string s, partition s such that every substring of the partition is a palindrome.
Return all possible palindrome partitioning of s.
For example, given s = "aab",
Return
  [
    ["aa","b"],
    ["a","a","b"]
  ]
 
DP计算回文子串，然后回溯
class Solution {
private:
    vector<vector<bool> > isPalindrome;
    void partition(string &s,int start,vector<vector<string> > &result,vector<string> &part) {
        if(start >= s.size()){
            result.push_back(part);
            return;
        }
        for(int i=0; i<s.size()-start; i++){
            if(isPalindrome[start][start+i]){
                part.push_back(s.substr(start,i+1));
                partition(s,start+i+1,result,part);
                part.pop_back();
            }
        }
    }
public:
    vector<vector<string>> partition(string s) {
        // Start typing your C/C++ solution below
        // DO NOT write int main() function
        vector<vector<string> > result;
        if(s.size() < 1)
            return result;
        vector<string> part;
        isPalindrome = vector<vector<bool> >(s.size(),vector<bool>(s.size(),false));
        for(int i=0; i<s.size(); i++){
            isPalindrome[i][i] = true;
            for(int j=1; i+j<s.size(); j++){
                if(s[i-j] != s[i+j])
                    break;
                isPalindrome[i-j][i+j] = true;
            }
        }
        for(int i=0; i<s.size()-1; i++){
            for(int j=0; i+1+j<s.size(); j++){
                if(s[i-j] != s[i+1+j])
                    break;
                isPalindrome[i-j][i+1+j] = true;
            }
        }
        partition(s,0,result,part);
        return result;
    }
};
 
Palindrome Partitioning II (Leetcode)
Given a string s, partition s such that every substring of the partition is a palindrome.
Return the minimum cuts needed for a palindrome partitioning of s.
For example, given s = "aab",
Return 1 since the palindrome partitioning ["aa","b"] could be produced using 1 cut.
 
后半部分的dp注意多加一个点，否则剩下的正好是一个回文的话还要分类讨论
class Solution {
public:
    int minCut(string s) {
        // Start typing your C/C++ solution below
        // DO NOT write int main() function
        if(s.size() < 1)
            return 0;
        vector<int> dp(s.size()+1);
        vector<vector<bool> > isPalindrome(s.size(),vector<bool>(s.size(),false));
        for(int i=0; i<s.size(); i++){
            isPalindrome[i][i] = true;
            for(int j=1; i+j<s.size(); j++){
                if(s[i-j] != s[i+j])
                    break;
                isPalindrome[i-j][i+j] = true;
            }
        }
        for(int i=0; i<s.size()-1; i++){
            for(int j=0; i+1+j<s.size(); j++){
                if(s[i-j] != s[i+1+j])
                    break;
                isPalindrome[i-j][i+1+j] = true;
            }
        }
        dp[s.size()] = 0;
        for(int i=s.size()-1; i>=0; i--){
            dp[i] = dp[i+1]+1;
            for(int j=i; j<s.size(); j++)
                if(isPalindrome[i][j] && 1+dp[j+1]<dp[i])
                    dp[i] = 1+dp[j+1];
        }
        return dp[0]-1;
    }
};
 
Binary Tree Zigzag Level Order Traversal (Leetcode)
Given a binary tree, return the zigzag level order traversal of its nodes' values. (ie, from left to right, then right to left for the next level and alternate between).
For example:
Given binary tree {3,9,20,#,#,15,7},
    3
   / \
  9  20
    /  \
   15   7
return its zigzag level order traversal as:
[
  [3],
  [20,9],
  [15,7]
]
 
/**
 * Definition for binary tree
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    vector<vector<int> > zigzagLevelOrder(TreeNode *root) {
        // Start typing your C/C++ solution below
        // DO NOT write int main() function
        vector<vector<int> > result;
        vector<int> temp;
        vector<TreeNode*> q[2];
        int curr = 0;
        if(root == NULL)
            return result;
        q[curr].push_back(root);
        while(q[curr].size() > 0){
            for(int i=0; i<q[curr].size(); i++){
                temp.push_back(q[curr][i]->val);
                if(q[curr][i]->left != NULL)
                    q[1-curr].push_back(q[curr][i]->left);
                if(q[curr][i]->right != NULL)
                    q[1-curr].push_back(q[curr][i]->right);
            }
            if(curr == 1)
                reverse(temp.begin(),temp.end());
            result.push_back(temp);
            temp.clear();
            q[curr].clear();
            curr = 1-curr;
        }
        return result;
    }
};
 
Clone Graph (Leetcode)
Clone an undirected graph. Each node in the graph contains a label and a list of its neighbors.
/**
 * Definition for undirected graph.
 * struct UndirectedGraphNode {
 *     int label;
 *     vector<UndirectedGraphNode *> neighbors;
 *     UndirectedGraphNode(int x) : label(x) {};
 * };
 */
 
注意要记录点是否已被遍历到
class Solution {
    typedef UndirectedGraphNode Node;
public:
    UndirectedGraphNode *cloneGraph(UndirectedGraphNode *node) {
        if(node == NULL)
            return NULL;
        Node* result = new Node(node->label);
        queue<Node*> q;
        map<Node*,Node*> m;
        q.push(node);
        m[node] = result;
        while(!q.empty()){
            Node* cur = q.front();
            q.pop();
            for(int i=0; i<cur->neighbors.size(); i++){
                Node* nb = cur->neighbors[i];
                if(m.count(nb) > 0)
                    m[cur]->neighbors.push_back(m[nb]);
                else{
                    m[cur]->neighbors.push_back(new Node(nb->label));
                    m[nb] = m[cur]->neighbors.back();
                    q.push(nb);
                }
            }
        }
        return result;
    }
};
 
Valid Palindrome (Leetcode)
Given a string, determine if it is a palindrome, considering only alphanumeric characters and ignoring cases.
For example,
"A man, a plan, a canal: Panama" is a palindrome.
"race a car" is not a palindrome.
Note:
Have you consider that the string might be empty? This is a good question to ask during an interview.
For the purpose of this problem, we define empty string as valid palindrome.
 
注意只考虑字母和数字，要用cctype
class Solution {
public:
    bool isPalindrome(string s) {
        // Start typing your C/C++ solution below
        // DO NOT write int main() function
        int start = 0;
        int stop = s.size()-1;
        while(start < stop){
            if(!isalnum(s[start]))
                start++;
            else if(!isalnum(s[stop]))
                stop--;
            else{
                if(tolower(s[start]) == tolower(s[stop])){
                    start++;
                    stop--;
                }
                else
                    return false;
            }
        }
        return true;
    }
};
 
Longest Substring Without Repeating Characters (Leetcode)
Given a string, find the length of the longest substring without repeating characters. For example, the longest substring without repeating letters for "abcabcbb" is "abc", which the length is 3. For "bbbbb" the longest substring is "b", with the length of 1.
 
记录上一次出现的位置更加高效
class Solution {
public:
    int lengthOfLongestSubstring(string s) {
        // Start typing your C/C++ solution below
        // DO NOT write int main() function
        int last[256];
        for(int i=0; i<256; ++i)
            last[i] = -1;
        int result = 0;
        int beforeStart = -1;
        for(int i=0; i<s.length(); ++i){
            if(last[s[i]] >  beforeStart)
                 beforeStart = last[s[i]];
            else if(i- beforeStart > result)
                result = i- beforeStart;
            last[s[i]] = i;
        }
        return result;
    }
};
 
需要发行的纸币
输入从小到大排列的纸币面额，取值【1，10^6】，输出一个整数，表示应该发行的纸币面额，这个整数是已经发行的所有纸币面额都无法表示的最小整数。（已经发行的每个纸币面额最多只能使用一次）
输入	输出
1 2 3 9 100	7
1 2 4 9 100	8
1 2 4 7 100	15

 
int minCash(vector<int> cash)
{
	int max = 0;
	for(int i=0; i<cash.size(); i++){
		if(max < cash[i]-1)
			break;
		max += cash[i];
	}
	return max+1;
}
 
Plus One (Leetcode)
Given a number represented as an array of digits, plus one to the number.
 
注意负数，以及只有一个0的情况
class Solution {
public:
    vector<int> plusOne(vector<int> &digits) {
        // Start typing your C/C++ solution below
        // DO NOT write int main() function
        if(digits[0] >= 0){
            int carry = 1;
            for(int i=digits.size()-1; i>=0; i--){
                digits[i] += carry;
                if(digits[i] > 9){
                    digits[i] -= 10;
                    carry = 1;
                }
                else
                    carry = 0;
            }
            if(carry == 1)
                digits.insert(digits.begin(),1);
        }
        else{
            digits[0] = -digits[0];
            int borrow = 1;
            for(int i=digits.size()-1; i>=0; i--){
                digits[i] -= borrow;
                if(digits[i] < 0){
                    digits[i] += 10;
                    borrow = 1;
                }
                else
                    borrow = 0;
            }
            if(borrow == 1)
                digits.insert(digits.begin(),1);
            digits[0] = -digits[0];
        }
        return digits;
    }
};
 
Gas Station (Leetcode)
There are N gas stations along a circular route, where the amount of gas at station i is gas[i].
You have a car with an unlimited gas tank and it costs cost[i] of gas to travel from station i to its next station (i+1). You begin the journey with an empty tank at one of the gas stations.
Return the starting gas station's index if you can travel around the circuit once, otherwise return -1.
Note:
The solution is guaranteed to be unique.
 
注意边界情况（只有一个点）和判断没有解的跳出条件
class Solution {
public:
    int canCompleteCircuit(vector<int> &gas, vector<int> &cost) {
        // Note: The Solution object is instantiated only once and is reused by each test case.
        vector<int> diff;
        if(gas.size() < 1)
            return -1;
        for(size_t i=0; i<gas.size(); i++)
            diff.push_back(gas[i]-cost[i]);
        long long curr = 0;
        int start = 0;
        int reach = 0;
        while(true){
            if(curr < 0){
                if(reach <= start)
                    return -1;
                curr = 0;
                start = reach;
            }
            curr += diff[reach++];
            if(reach == diff.size())
                reach = 0;
            if(reach==start && curr>=0)
                return start;
        };
    }
};
 
Text Justification (Leetcode)
Given an array of words and a length L, format the text such that each line has exactly L characters and is fully (left and right) justified.
You should pack your words in a greedy approach; that is, pack as many words as you can in each line. Pad extra spaces ' ' when necessary so that each line has exactly Lcharacters.
Extra spaces between words should be distributed as evenly as possible. If the number of spaces on a line do not divide evenly between words, the empty slots on the left will be assigned more spaces than the slots on the right.
For the last line of text, it should be left justified and no extra space is inserted between words.
For example,
words: ["This", "is", "an", "example", "of", "text", "justification."]
L: 16.
Return the formatted lines as:
[
   "This    is    an",
   "example  of text",
   "justification.  "
]
Note: Each word is guaranteed not to exceed L in length.
click to show corner cases.
Corner Cases:
A line other than the last line might contain only one word. What should you do in this case?
In this case, that line should be left-justified.
 
class Solution {
public:
    vector<string> fullJustify(vector<string> &words, int L) {
        // Start typing your C/C++ solution below
        // DO NOT write int main() function
        string line;
        vector<string> result;
        int wIdx = 0;
        while(wIdx < words.size()){
            line.clear();
            assert(words[wIdx].length() <= L);
            int len = words[wIdx].length();
            line = words[wIdx++];
            int i = wIdx;
            while(i<words.size() && len+words[i].length()+1<=L)
                len += words[i++].length()+1;
            int remain = L-len;
            if(i == words.size()){
                for(; wIdx<i; wIdx++){
                    line += " ";
                    line += words[wIdx];
                }
            }
            else{
                for(; wIdx<i; wIdx++){
                    int numBlank = remain/(i-wIdx)+1;
                    if(remain%(i-wIdx))
                        numBlank++;
                    remain -= numBlank-1;
                    line += string(numBlank,' ');
                    line += words[wIdx];
                }
            }
            if(remain > 0)
                line += string(remain,' ');
            result.push_back(line);
        }
        return result;
    }
};
 
Decode Ways (Leetcode)
A message containing letters from A-Z is being encoded to numbers using the following mapping:
'A' -> 1
'B' -> 2
...
'Z' -> 26
Given an encoded message containing digits, determine the total number of ways to decode it.
For example,
Given encoded message "12", it could be decoded as "AB" (1 2) or "L" (12).
The number of ways decoding "12" is 2.
 
递归超时。空字符串返回0
class Solution {
public:
    int numDecodings(string s) {
        // Start typing your C/C++ solution below
        // DO NOT write int main() function
        if(s.length() < 1)
            return 0;
        vector<int> dp(s.length()+1);
        dp[s.length()] = 1;
        for(int i=s.length()-1; i>=0; i--){
            if(s[i] == '0')
                dp[i] = 0;
            else{
                dp[i] = dp[i+1];
                if(i<s.length()-1 && atoi(s.substr(i,2).c_str())<27)
                    dp[i] += dp[i+2];
            }
        }
        return dp[0];
    }
};
 
Candy (Leetcode)
There are N children standing in a line. Each child is assigned a rating value.
You are giving candies to these children subjected to the following requirements:
Each child must have at least one candy.
Children with a higher rating get more candies than their neighbors.
What is the minimum candies you must give?
 
class Solution {
public:
    int candy(vector<int> &ratings) {
        // Note: The Solution object is instantiated only once and is reused by each test case.
        if(ratings.size() < 1)
            return 0;
        vector<int> can(1,1);
        for(int i=1; i<ratings.size(); i++){
            if(ratings[i] > ratings[i-1])
                can.push_back(can.back()+1);
            else
                can.push_back(1);
        }
        for(int i=ratings.size()-2; i>=0; i--){
            if(ratings[i]>ratings[i+1] && can[i+1]+1>can[i])
                can[i] = can[i+1]+1;
        }
        int result = 0;
        for(int i=0; i<can.size(); i++)
            result += can[i];
        return result;
    }
};
 
Single Number II (Leetcode)
Given an array of integers, every element appears three times except for one. Find that single one.
Note:
Your algorithm should have a linear runtime complexity. Could you implement it without using extra memory?
 
class Solution {
public:
    int singleNumber(int A[], int n) {
        // Note: The Solution object is instantiated only once and is reused by each test case.
        int lower = 0;
        int higher = 0;
        for(int i=0; i<n; i++){
            higher ^= lower&A[i];
            lower ^= A[i];
            int count3 = higher&lower;
            higher &= ~count3;
            lower &= ~count3;
        }
        return lower;
    }
};
 
Copy List with Random Pointer (Leetcode)
A linked list is given such that each node contains an additional random pointer which could point to any node in the list or null.
Return a deep copy of the list.
 
原链表重建和新链表设置random不能同时，否则random到前面的origin点就找不到对应的新点了
/**
 * Definition for singly-linked list with a random pointer.
 * struct RandomListNode {
 *     int label;
 *     RandomListNode *next, *random;
 *     RandomListNode(int x) : label(x), next(NULL), random(NULL) {}
 * };
 */
class Solution {
public:
    RandomListNode *copyRandomList(RandomListNode *head) {
        // Note: The Solution object is instantiated only once and is reused by each test case.
        if(head == NULL)
            return NULL;
        vector<RandomListNode* > origin;
        RandomListNode* ptr = head;
        while(ptr != NULL){
            origin.push_back(ptr);
            RandomListNode* temp = ptr;
            ptr = ptr->next;
            temp->next = new RandomListNode(temp->label);
        }
        ptr = origin[0]->next;
        for(size_t i=0; i<origin.size(); i++){
            if(origin[i]->random == NULL)
                origin[i]->next->random = NULL;
            else
                origin[i]->next->random = origin[i]->random->next;
        }
        for(size_t i=0; i<origin.size(); i++){
            if(i == origin.size()-1){
                origin[i]->next->next = NULL;
                origin[i]->next = NULL;
            }
            else{
                origin[i]->next->next = origin[i+1]->next;
                origin[i]->next = origin[i+1];
            }
        }
        return ptr;
    }
};
 
Word Break (Leetcode)
Given a string s and a dictionary of words dict, determine if s can be segmented into a space-separated sequence of one or more dictionary words.
For example, given
s = "leetcode",
dict = ["leet", "code"].
Return true because "leetcode" can be segmented as "leet code".
 
非递归（超时）：
class Solution {
private:
    bool Helper(string &s,int start,unordered_set<string> &dict,vector<int> &len){
        if(start == s.length())
            return true;
        if(start > s.length())
            return false;
        for(vector<int>::iterator iter=len.begin(); iter!=len.end(); ++iter)
            if(dict.count(s.substr(start,*iter)) > 0)
                if(Helper(s,start+*iter,dict,len))
                    return true;
        return false;
    }
public:
    bool wordBreak(string s, unordered_set<string> &dict) {
        // Note: The Solution object is instantiated only once and is reused by each test case.
        set<int> len;
        unordered_set<string>::iterator iter;
        for(iter=dict.begin(); iter!=dict.end(); iter++)
            len.insert(iter->length());
        vector<int> length(len.begin(),len.end());
        sort(length.begin(),length.end(),greater<int>());
        return Helper(s,0,dict,length);
    }
};
动态规划，当心字典为空：
class Solution {
public:
    bool wordBreak(string s, unordered_set<string> &dict) {
        // Note: The Solution object is instantiated only once and is reused by each test case.
        if(dict.size() < 1)
            return false;
        set<int> len;
        for(unordered_set<string>::iterator iter=dict.begin(); iter!=dict.end(); iter++)
            len.insert(iter->length());
        vector<int> length(len.begin(),len.end());
        sort(length.begin(),length.end());
        vector<bool> dp(s.length()+1,false);
        dp[0] = true;
        for(int i=0; i<s.length(); i++)
            if(!dp[i+1])
                for(int l=0; l<length.size()&&length[l]<=i+1; l++)
                    if(dp[i+1-length[l]] && 
					dict.count(s.substr(i-length[l]+1,length[l]))>0){
                        dp[i+1] = true;
                        break;
                    }
        return dp.back();
    }
};
 
Word Break II (Leetcode)
Given a string s and a dictionary of words dict, add spaces in s to construct a sentence where each word is a valid dictionary word.
Return all such possible sentences.
For example, given
s = "catsanddog",
dict = ["cat", "cats", "and", "sand", "dog"].
A solution is ["cats and dog", "cat sand dog"].
 
class Solution {
private:
    vector<string> result;
    string temp;
    vector<int> length;
    void Helper(string &s,int start,unordered_set<string> &dict){
        if(start == s.length()){
			result.push_back(temp.substr(0,temp.length()-1));
            return;
        }
        for(int i=0; i<length.size()&&length[i]<=s.length()-start; i++){
            string part = s.substr(start,length[i]);
            if(dict.count(part) > 0){
                temp += part+" ";
                Helper(s,start+length[i],dict);
                temp.erase(temp.end()-length[i]-1,temp.end());
            }
        }
    }
public:
    vector<string> wordBreak(string s, unordered_set<string> &dict) {
        // Note: The Solution object is instantiated only once and is reused by each test case.
        result.clear();
        if(dict.size() < 1)
            return result;
        set<int> len;
        unordered_set<string>::iterator iter;
        for(iter=dict.begin(); iter!=dict.end(); iter++)
            len.insert(iter->length());
        length = vector<int>(len.begin(),len.end());
        sort(length.begin(),length.end());
        temp.clear();
        Helper(s,0,dict);
        return result;
    }
};
 
Sum Root to Leaf Numbers (Leetcode)
Given a binary tree containing digits from 0-9 only, each root-to-leaf path could represent a number.
An example is the root-to-leaf path 1->2->3 which represents the number 123.
Find the total sum of all root-to-leaf numbers.
For example,
    1
   / \
  2   3
The root-to-leaf path 1->2 represents the number 12.
The root-to-leaf path 1->3 represents the number 13.
Return the sum = 12 + 13 = 25.
 
注意叶子的定义；到NULL才加的话，叶子节点会重复计算
/**
 * Definition for binary tree
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
    int result;
    void Helper(TreeNode* root,int curSum){
        if(root == NULL)
            return;
        if(root->left==NULL && root->right==NULL){
            result += curSum*10+root->val;
            return;
        }
        if(root->left != NULL)
            Helper(root->left,curSum*10+root->val);
        if(root->right != NULL)
            Helper(root->right,curSum*10+root->val);
    }
public:
    int sumNumbers(TreeNode *root) {
        // Note: The Solution object is instantiated only once and is reused by each test case.
        result = 0;
        Helper(root,0);
        return result;
    }
};
 
Interleaving String (Leetcode) 
Given s1, s2, s3, find whether s3 is formed by the interleaving of s1 and s2.
For example,
Given:
s1 = "aabcc",
s2 = "dbbca",
When s3 = "aadbbcbcac", return true.
When s3 = "aadbbbaccc", return false.
 
计算dp的时候s3序号容易搞错
class Solution {
public:
    bool isInterleave(string s1, string s2, string s3) {
        // Start typing your C/C++ solution below
        // DO NOT write int main() function
        if(s1.size()+s2.size() != s3.size())
            return false;
        vector<bool> dp(s1.size()+1,false);
        dp.back() = true;
        for(int i=s1.size()-1; i>=0; i--)
            dp[i] = dp[i+1]&&(s1[i]==s3[i+s3.size()-s1.size()]);
        for(int i=s2.size()-1; i>=0; i--){
            dp.back() = dp.back()&&
								(s2[i]==s3[i+s3.size()-s2.size()]);
            for(int j=s1.size()-1; j>=0; j--){
                if(dp[j] && 
					s2[i]==s3[s3.size()+i-s2.size()+j-s1.size()])
                    dp[j] = true;
                else if(dp[j+1] && 
					s1[j]==s3[s3.size()+i-s2.size()+j-s1.size()])
                    dp[j] = true;
                else
                    dp[j] = false;
            }
        }
        return dp[0];
    }
};
 
Add Two Numbers (Leetcode) 
You are given two linked lists representing two non-negative numbers. The digits are stored in reverse order and each of their nodes contain a single digit. Add the two numbers and return it as a linked list.
Input: (2 -> 4 -> 3) + (5 -> 6 -> 4)
Output: 7 -> 0 -> 8
 
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode *addTwoNumbers(ListNode *l1, ListNode *l2) {
        // Start typing your C/C++ solution below
        // DO NOT write int main() function
        int carry = 0;
        ListNode dummy(0);
        ListNode* result = &dummy;
        while(l1!=NULL || l2!=NULL){
            int val = carry;
            if(l1 != NULL){
                val += l1->val;
                l1 = l1->next;
            }
            if(l2 != NULL){
                val += l2->val;
                l2 = l2->next;
            }
            if(val > 9){
                carry = 1;
                val -= 10;
            }
            else
                carry = 0;
            result->next = new ListNode(val);
            result = result->next;
        }
        if(carry == 1)
            result->next = new ListNode(1);
        return dummy.next;
    }
};
 
Longest Palindromic Substring (Leetcode)
Given a string S, find the longest palindromic substring in S. You may assume that the maximum length of S is 1000, and there exists one unique longest palindromic substring.
 
注意判断i >= rightmost的情况
class Solution {
public:
    string longestPalindrome(string s) {
        // Note: The Solution object is instantiated only once and is reused by each test case.
        if(s.size() < 1)
            return string();
        string expanded("$*");
        for(int i=0; i<s.size(); i++){
            expanded.push_back(s[i]);
            expanded.push_back('*');
        }
        expanded.push_back('%');
        vector<int> len(expanded.size(),0);
        int rightmost = 1;
        int rightmostMid = 1;
        int maxLen = 0;
        int maxMid = 0;
        for(int i=1; i<len.size()-1; i++){
            if(i < rightmost)
                len[i] = len[2*rightmostMid-i];
            else	// 容易漏
                len[i] = 0;
            if(i+len[i] >= rightmost){
                int j = i;
                while(expanded[j] == expanded[2*i-j])
                    j++;
                len[i] = j-1;
                rightmost = j-1;
                rightmostMid = i;
                if(j-i-1 > maxLen){
                    maxLen = j-i-1;
                    maxMid = i;
                }
            }
        }
        return s.substr((maxMid-maxLen+1)/2-1,maxLen);
    }
};
 
ZigZag Conversion (Leetcode) 
The string "PAYPALISHIRING" is written in a zigzag pattern on a given number of rows like this: (you may want to display this pattern in a fixed font for better legibility)
P   A   H   N
A P L S I I G
Y   I   R
And then read line by line: "PAHNAPLSIIGYIR"
Write the code that will take a string and make this conversion given a number of rows:
string convert(string text, int nRows);
convert("PAYPALISHIRING", 3) should return "PAHNAPLSIIGYIR".
 
特殊情况比较多
class Solution {
public:
    string convert(string s, int nRows) {
        // Note: The Solution object is instantiated only once and is reused by each test case.    
        string result;
        if(nRows < 1)
            return result;
        if(nRows == 1)
            return s;
        for(int i=0; i<nRows; i++){
			int last = -1;
            for(int idx=0; idx<s.size(); idx+=2*(nRows-1)){
				int j = idx+i;
                if(j<s.size() && j!=last){
                    result.push_back(s[j]);
					last = j;
				}
				j = idx+2*(nRows-1)-i;
                if(j<s.size() && j!=last){
                    result.push_back(s[j]);
					last = j;
				}
            }
        }
        return result;
    }
};
 
最大子段和
给定一个整型数组a，求出最大连续子段之和，如果和为负数，则按0计算，比如1， 2， -5， 6， 8则输出6 + 8 = 14
 
注意全负数的情况
class Solution {
public:
    int maxSubArray(int A[], int n) {
        // Start typing your C/C++ solution below
        // DO NOT write int main() function
        int result = INT_MIN;
        int sum = A[0];
		for(int i=1; i<n; i++)
			sum<0 ? sum=A[i] : sum+=A[i];
            if(sum > result)
                result = sum;
        }
        return result;
    }
}; 
最大子段积
给定一个整型数组a，求出最大连续子段的乘积，比如 1， 2， -8， 12， 7则输出12 * 7 = 84
 
int MaxProduct(int *a, int n)
{
    int maxProduct = 1; // max positive product at current position
    int minProduct = 1; // min negative product at current position
    int r = 1; // result, max multiplication totally
    for (int i=0; i<n; i++){
        if(a[i] > 0){
            maxProduct *= a[i];
            minProduct = min(minProduct*a[i], 1);
        }
        else if(a[i] == 0){
            maxProduct = 1;
            minProduct = 1;
        }
        else{ // a[i] < 0
            int temp = maxProduct;
            maxProduct = max(minProduct*a[i], 1);
            minProduct = temp*a[i];
        }
        r = max(r,maxProduct);
    }
    return r;
}
 
Same Tree (Leetcode) 
Given two binary trees, write a function to check if they are equal or not.
Two binary trees are considered equal if they are structurally identical and the nodes have the same value.
 
/**
 * Definition for binary tree
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    bool isSameTree(TreeNode *p, TreeNode *q) {
        // Note: The Solution object is instantiated only once and is reused by each test case.
        if(p==NULL && q==NULL)
            return true;
        if(p==NULL || q==NULL)
            return false;
        return p->val==q->val && isSameTree(p->left,q->left) && isSameTree(p->right,q->right);
    }
};
 
Anagrams (Leetcode)
Given an array of strings, return all groups of strings that are anagrams.
Note: All inputs will be in lower-case.
 
注意容器的使用
class Solution {
    typedef pair<string,string> p;
public:
    vector<string> anagrams(vector<string> &strs) {
        // Note: The Solution object is instantiated only once and is reused by each test case.
        multimap<string,string> m;
        vector<string> result;
        for(vector<string>::iterator iter=strs.begin(); 
												iter!=strs.end(); ++iter){
            string sorted = *iter;
            sort(sorted.begin(),sorted.end());
            m.insert(p(sorted,*iter));
        }
        for(multimap<string,string>::iterator iter=m.begin(); 
												iter!=m.end(); ++iter){
            if(m.count(iter->first) > 1)
                result.push_back(iter->second);
        }
        return result;
    }
};
 
Binary Tree Maximum Path Sum (Leetcode) 
Given a binary tree, find the maximum path sum.
The path may start and end at any node in the tree.
For example:
Given the below binary tree,
       1
      / \
     2   3
Return 6.
 
注意是任意点到任意点，且有负数
/**
 * Definition for binary tree
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
private:
    int result;
    int Helper(TreeNode *root){
        if(root == NULL)
            return 0;
        int left = max(0,Helper(root->left));
        int right = max(0,Helper(root->right));
        if(left+right+root->val > result)
            result = left+right+root->val;
        return root->val+max(left,right);
    }
public:
    int maxPathSum(TreeNode *root) {
        // Note: The Solution object is instantiated only once and is reused by each test case.
        result = INT_MIN;
        Helper(root);
        return result;
    }
};
 
Symmetric Tree (Leetcode) 
Given a binary tree, check whether it is a mirror of itself (ie, symmetric around its center).
For example, this binary tree is symmetric:
    1
   / \
  2   2
 / \ / \
3  4 4  3
But the following is not:
    1
   / \
  2   2
   \   \
   3    3
Note:
Bonus points if you could solve it both recursively and iteratively.
 
/**
 * Definition for binary tree
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
递归：
class Solution {
private:
    bool isSymmetric(TreeNode *r1,TreeNode *r2){
        if(r1==NULL && r2==NULL)
            return true;
        if(r1==NULL || r2==NULL)
            return false;
        if(r1->val != r2->val)
            return false;
        return isSymmetric(r1->left,r2->right) && isSymmetric(r1->right,r2->left);
    }
public:
    bool isSymmetric(TreeNode *root) {
        // Note: The Solution object is instantiated only once and is reused by each test case.
        if(root == NULL)
            return true;
        return isSymmetric(root->left,root->right);
    }
};
非递归：
class Solution {
public:
    typedef pair<TreeNode*,TreeNode*> p;
    bool isSymmetric(TreeNode *root) {
        // Note: The Solution object is instantiated only once and is reused by each test case.
        if(root == NULL)
            return true;
        queue<p> q;
        q.push(p(root->left,root->right));
        while(!q.empty()){
            p temp = q.front();
            q.pop();
            if(temp.first==NULL && temp.second==NULL)
                continue;
            if((temp.first==NULL||temp.second==NULL) || temp.first->val!=temp.second->val)
                return false;
            q.push(p(temp.first->left,temp.second->right));
            q.push(p(temp.first->right,temp.second->left));
        }
        return true;
    }
};
 
Recover Binary Search Tree (Leetcode) 
Two elements of a binary search tree (BST) are swapped by mistake.
Recover the tree without changing its structure.
Note:
A solution using O(n) space is pretty straight forward. Could you devise a constant space solution?
confused what "{1,#,2,3}" means? > read more on how binary tree is serialized on OJ.

OJ's Binary Tree Serialization:
The serialization of a binary tree follows a level order traversal, where '#' signifies a path terminator where no node exists below.
Here's an example:
   1
  / \
 2   3
    /
   4
    \
     5
The above binary tree is serialized as "{1,2,3,#,#,4,#,#,5}".
 
/**
 * Definition for binary tree
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
private:
    vector<TreeNode*> array;
    void inorder(TreeNode *root){
        if(root == NULL)
            return;
        inorder(root->left);
        array.push_back(root);
        inorder(root->right);
    }
public:
    void recoverTree(TreeNode *root) {
        // Start typing your C/C++ solution below
        // DO NOT write int main() function
        array.clear();
        inorder(root);
        int first = 0;
        while(array[first]->val <= array[first+1]->val)
            first++;
        int second = array.size()-1;
        while(array[second]->val >= array[second-1]->val)
            second--;
        swap(array[first]->val,array[second]->val);
    }
};
 
去除/**/内的注释
 
注意不匹配情况，嵌套情况，/**/情况
char buf[5050];
int main() {
	printf("Case #1:\n");
	int cnt = 0;
	string ans;
	while(gets(buf)){
		int l = strlen(buf);
		for(int i=0; i<l; ++i){
			if(cnt == 0){
				if(buf[i]=='/' && i+1<l && buf[i+1]=='*'){
					++cnt;
					++i;
					continue;
				}
			}else{
				if (buf[i]=='*' && i+1<l && buf[i+1]=='/'){
					--cnt;
					++i;
					continue;
				}
				if (buf[i]=='/' && i+1<l && buf[i+1]=='*'){
					++cnt;
					++i;
					continue;
				}
			}
			if(cnt == 0)
				ans += buf[i];
		}
		if(cnt == 0)
			ans += '\n';
	}
	cout<<ans;
	return 0;
}
 
最大公约数
 
int gcd(int v1, int v2){
	while(v2){
		int temp = v2;
		v2 = v1%v2;
		v1 = temp;
	}
	return v1;
}
 
Path Sum (Leetcode)
Given a binary tree and a sum, determine if the tree has a root-to-leaf path such that adding up all the values along the path equals the given sum.
For example:
Given the below binary tree and sum = 22,
              5
             / \
            4   8
           /   / \
          11  13  4
         /  \      \
        7    2      1
return true, as there exist a root-to-leaf path 5->4->11->2 which sum is 22.
 
/**
 * Definition for binary tree
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    bool hasPathSum(TreeNode *root, int sum) {
        // Note: The Solution object is instantiated only once and is reused by each test case.
        if(root == NULL)
            return false;
        if(root->left==NULL && root->right==NULL && root->val==sum)
            return true;
        return hasPathSum(root->left,sum-root->val)||hasPathSum(root->right,sum-root->val);
    }
};
 
Path Sum II (Leetcode) 
Given a binary tree and a sum, find all root-to-leaf paths where each path's sum equals the given sum.
For example:
Given the below binary tree and sum = 22,
              5
             / \
            4   8
           /   / \
          11  13  4
         /  \    / \
        7    2  5   1
return
[
   [5,4,11,2],
   [5,8,4,5]
]
 
注意是到leaf结束
/**
 * Definition for binary tree
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
private:
    vector<int> path;
    vector<vector<int> > result;
    void Helper(TreeNode *root,int sum){
        if(root->left==NULL && root->right==NULL){
            if(sum == root->val){
                path.push_back(root->val);
                result.push_back(path);
                path.pop_back();
            }
            return;
        }
        path.push_back(root->val);
        if(root->left != NULL)
            Helper(root->left,sum-root->val);
        if(root->right != NULL)
            Helper(root->right,sum-root->val);
        path.pop_back();
    }
public:
    vector<vector<int> > pathSum(TreeNode *root, int sum) {
        // Note: The Solution object is instantiated only once and is reused by each test case.
        path.clear();
        result.clear();
        if(root == NULL)
            return result;
        Helper(root,sum);
        return result;
    }
};
 
Unique Binary Search Trees （Leetcode）
Given n, how many structurally unique BST's (binary search trees) that store values 1...n?
For example,
Given n = 3, there are a total of 5 unique BST's.
   1         3     3      2      1
    \       /     /      / \      \
     3     2     1      1   3      2
    /     /       \                 \
   2     1         2                 3
 
class Solution {
public:
    int numTrees(int n) {
        // Note: The Solution object is instantiated only once and is reused by each test case.
        vector<int> dp(n+1,0);
        dp[0] = 1;
        for(int i=1; i<=n; i++)
            for(int j=0; j<i; j++)
                dp[i] += dp[j]*dp[i-j-1];
        return dp.back();
    }
};
 
Merge Sorted Array (Leetcode)
Given two sorted integer arrays A and B, merge B into A as one sorted array.
Note:
You may assume that A has enough space to hold additional elements from B. The number of elements initialized in A and B are m and n respectively.
 
class Solution {
public:
    void merge(int A[], int m, int B[], int n) {
        // Note: The Solution object is instantiated only once and is reused by each test case.
        int *tail = A+m+n-1;
        int *tailA = A+m-1;
        int *tailB = B+n-1;
        while(tail >= A){
            if(tailB<B || (tailA>=A && *tailA>*tailB))	// 注意
                *tail-- = *tailA--;
            else
                *tail-- = *tailB--;
        }
    }
};
 
Maximal Rectangle (Leetcode)
Given a 2D binary matrix filled with 0's and 1's, find the largest rectangle containing all ones and return its area.
 
每次pop都计算该点左右的边界所构成的矩形
class Solution {
public:
    int maximalRectangle(vector<vector<char> > &matrix) {
        // Note: The Solution object is instantiated only once and is reused by each test case.
        if(matrix.size()==0 || matrix[0].size()==0)
            return 0;
        int result = 0;
        vector<int> line(matrix[0].size()+1,0);
        for(int i=0; i<matrix.size(); i++){
            stack<int> st;
            for(int j=0; j<line.size()-1; j++){
                if(matrix[i][j] == '0')
                    line[j] = 0;
                else
                    line[j]++;
            }
            int idx = 0;
            while(idx <= line.size()){
                if(st.empty() || line[idx]>line[st.top()])
                    st.push(idx++);
                else{
                    int height = line[st.top()];
                    st.pop();
                    int area = height*idx;
                    if(!st.empty())	// 容易漏
                        area = height*(idx-st.top()-1);
                    if(area > result)
                        result = area;
                }
            }
        }
        return result;
    }
};
 
Sqrt(x) (Leetcode) 
Implement int sqrt(int x).
Compute and return the square root of x.
 
class Solution {
public:
    int sqrt(int x) {
        // Note: The Solution object is instantiated only once and is reused by each test case.
        if(x < 0)
            return -1;
        int result = 0;
        long long temp = INT_MAX/2+1;	// int的话会溢出
        while(temp > 0){
            if((result+temp)*(result+temp) <= x)
                result += temp;
            temp = temp>>1;
        }
        return result;
    }
};
 
String to Integer (atoi) (Leetcode)
Implement atoi to convert a string to an integer.
Hint: Carefully consider all possible input cases. If you want a challenge, please do not see below and ask yourself what are the possible input cases.
Notes: It is intended for this problem to be specified vaguely (ie, no given input specs). You are responsible to gather all the input requirements up front.
Requirements for atoi:
The function first discards as many whitespace characters as necessary until the first non-whitespace character is found. Then, starting from this character, takes an optional initial plus or minus sign followed by as many numerical digits as possible, and interprets them as a numerical value.
The string can contain additional characters after those that form the integral number, which are ignored and have no effect on the behavior of this function.
If the first sequence of non-whitespace characters in str is not a valid integral number, or if no such sequence exists because either str is empty or it contains only whitespace characters, no conversion is performed.
If no valid conversion could be performed, a zero value is returned. If the correct value is out of the range of representable values, INT_MAX (2147483647) or INT_MIN (-2147483648) is returned.
 
class Solution {
public:
    int atoi(const char *str) {
        // Note: The Solution object is instantiated only once and is reused by each test case.
        long long result = 0;
        bool isPositive = true;
        while(*str==' ' || *str=='\t')
            str++;
        if(*str == '-'){
            str++;
            isPositive = false;
        }
        else if(*str == '+')
            str++;
        while(isdigit(*str)){
            result = 10*result+*str-'0';
            if(isPositive && result>INT_MAX)
                return INT_MAX;
            if(!isPositive && -result<INT_MIN)
                return INT_MIN;
            str++;
        }
        return isPositive ? result:-result;
    }
};
 
Word Ladder (Leetcode) 
Given two words (start and end), and a dictionary, find the length of shortest transformation sequence from start to end, such that:
Only one letter can be changed at a time
Each intermediate word must exist in the dictionary
For example,
Given:
start = "hit"
end = "cog"
dict = ["hot","dot","dog","lot","log"]
As one shortest transformation is "hit" -> "hot" -> "dot" -> "dog" -> "cog",
return its length 5.
Note:
Return 0 if there is no such transformation sequence.
All words have the same length.
All words contain only lowercase alphabetic characters.
 
class Solution {
public:
    int ladderLength(string start, string end, unordered_set<string> &dict) {
        // Note: The Solution object is instantiated only once and is reused by each test case.
        unordered_set<string> added;
        queue<pair<string,int>> q;
        if(dict.count(start)==0 || dict.count(end)==0)
            return 0;
        q.push(pair<string,int>(start,1));
        added.insert(start);
        while(!q.empty()){
            string curr = q.front().first;
            int step = q.front().second;
            q.pop();
            for(int i=0; i<curr.length(); i++){
                char temp = curr[i];	// 注意不要漏
                for(char c='a'; c<='z'; c++){
                    curr[i] = c;
                    if(curr == end)
                        return step+1;
                    if(dict.count(curr)>0 && added.count(curr)==0){
                        q.push(pair<string,int>(curr,step+1));
                        added.insert(curr);
                    }
                }
                curr[i] = temp;
            }
        }
        return 0;
    }
};
 
Length of Last Word (Leetcode) 
Given a string s consists of upper/lower-case alphabets and empty space characters ' ', return the length of last word in the string.
If the last word does not exist, return 0.
Note: A word is defined as a character sequence consists of non-space characters only.
For example, 
Given s = "Hello World",
return 5.
 
当心末尾有空格
class Solution {
public:
    int lengthOfLastWord(const char *s) {
        // Note: The Solution object is instantiated only once and is reused by each test case.
        if(s == NULL)
            return 0;
        int length = 0;
        while(*s != '\0'){
            if(isalnum(*s))
                length++;
            else if(isalnum(*(s+1)))
                length = 0;
            s++;
        }
        return length;
    }
};
 
Permutation Sequence (Leetcode)
The set [1,2,3,…,n] contains a total of n! unique permutations.
By listing and labeling all of the permutations in order,
We get the following sequence (ie, for n = 3):
"123"
"132"
"213"
"231"
"312"
"321"
Given n and k, return the kth permutation sequence.
Note: Given n will be between 1 and 9 inclusive.
 
class Solution {
public:
    string getPermutation(int n, int k) {
        // IMPORTANT: Please reset any member data you declared, as
        // the same Solution instance will be reused for each test case.
        string result;
        vector<char> num;
        for(int i=0; i<n; i++)
            num.push_back('0'+i+1);
        vector<int> factorial(n,1);
        for(int i=2; i<n; i++)
            factorial[i] = i*factorial[i-1];
        if(n<1 || k<1 || k>factorial[n-1]*n)
            return result;
        k--;	// 容易漏
        for(int i=n-1; i>=0; i--){
            int number = k/factorial[i];
            k -= number*factorial[i];
			// list的话只能++和--
            vector<char>::iterator iter = num.begin()+number;
            result.push_back(*iter);
            num.erase(iter);
        }
        return result;
    }
}; 
Edit Distance (Leetcode)
Given two words word1 and word2, find the minimum number of steps required to convert word1 to word2. (each operation is counted as 1 step.)
You have the following 3 operations permitted on a word:
a) Insert a character
b) Delete a character
c) Replace a character
 
class Solution {
public:
    int minDistance(string word1, string word2) {
        // Note: The Solution object is instantiated only once and is reused by each test case.
        int len1 = word1.length();
        int len2 = word2.length();
        vector<vector<int> > dis(len1+1,vector<int>(len2+1,0));
        for(int i=0; i<=len1; i++)
            dis[i][0] = i;
        for(int i=0; i<=len2; i++)
            dis[0][i] = i;
        for(int i=1; i<=len1; i++)
            for(int j=1; j<=len2; j++){
                int add = min(dis[i-1][j],dis[i][j-1])+1;
                if(word1[i-1] == word2[j-1])
                    dis[i][j] = min(add,dis[i-1][j-1]);
                else
                    dis[i][j] = min(add,dis[i-1][j-1]+1);
            }
        return dis[len1][len2];
    }
};
 
Merge Intervals (Leetcode)
Given a collection of intervals, merge all overlapping intervals.
For example,
Given [1,3],[2,6],[8,10],[15,18],
return [1,6],[8,10],[15,18].
 
/**
 * Definition for an interval.
 * struct Interval {
 *     int start;
 *     int end;
 *     Interval() : start(0), end(0) {}
 *     Interval(int s, int e) : start(s), end(e) {}
 * };
 */
bool compare(const Interval &i1, const Interval &i2){
    if(i1.start == i2.start)
        return i1.end<i2.end;
    return i1.start<i2.start;
}
class Solution {
public:
    vector<Interval> merge(vector<Interval> &intervals) {
        // IMPORTANT: Please reset any member data you declared, as
        // the same Solution instance will be reused for each test case.
        sort(intervals.begin(),intervals.end(),compare);
        vector<Interval> result;
        if(intervals.size() < 1)
            return result;
        Interval curr = intervals[0];
        vector<Interval>::iterator iter = intervals.begin();
        for(; iter!=intervals.end(); ++iter){
            if(curr.end < iter->start){
                result.push_back(curr);
                curr = *iter;
            }
            else
                curr.end = max(curr.end,iter->end);
        }
        result.push_back(curr);
        return result;
    }
};
 
Multiply Strings (Leetcode) 
Given two numbers represented as strings, return multiplication of the numbers as a string.
Note: The numbers can be arbitrarily large and are non-negative.
 
注意最后为0，头上有若干0的情况
class Solution {
public:
    string multiply(string num1, string num2) {
        // IMPORTANT: Please reset any member data you declared, as
        // the same Solution instance will be reused for each test case.
        reverse(num1.begin(),num1.end());
        reverse(num2.begin(),num2.end());
        string result;
        for(int i=0; i<num2.size(); i++){
            int carry = 0;
            int val2 = num2[i]-'0';
            for(int j=0; j<num1.size(); j++){
                int val1 = num1[j]-'0';
                int val = val1*val2+carry;
                carry = val/10;
                val -= carry*10;
                if(i+j >= result.size())
                    result.push_back(val+'0');
                else
                    result[i+j] += val;
                if(result[i+j] > '9'){
                    result[i+j] -= 10;
                    carry++;
                }
            }
            while(carry > 0){
				result.push_back(carry%10+'0');
				carry /= 10;
			}
        }
        reverse(result.begin(),result.end());
		string::iterator start = result.begin();
		while(*start=='0' && start<result.end()-1)
			++start;
        return string(start,result.end());
    }
};
 
Regular Expression Matching (Leetcode) 
Implement regular expression matching with support for '.' and '*'.
'.' Matches any single character.
'*' Matches zero or more of the preceding element.

The matching should cover the entire input string (not partial).

The function prototype should be:
bool isMatch(const char *s, const char *p)

Some examples:
isMatch("aa","a") → false
isMatch("aa","aa") → true
isMatch("aaa","aa") → false
isMatch("aa", "a*") → true
isMatch("aa", ".*") → true
isMatch("ab", ".*") → true
isMatch("aab", "c*a*b") → true
 
class Solution {
public:
    bool isMatch(const char *s, const char *p) {
        // Start typing your C/C++ solution below
        // DO NOT write int main() function
        if(s==NULL || p==NULL)
            return false;
        if(*p=='\0')
            return *s=='\0';
        if(*(p+1) != '*'){
            if(*s == '\0')
                return false;
            else if(*s==*p || *p=='.')
                return isMatch(s+1,p+1);
            else
                return false;
        }
        else{
            if(*p == '*')
                return false;
            while(*s==*p || (*p=='.' && *s!='\0'))
                if(isMatch(s++,p+2))
                    return true;
            return isMatch(s,p+2);
        }
    }
};
 
Reverse Linked List II (Leetcode) 
Reverse a linked list from position m to n. Do it in-place and in one-pass.
For example:
Given 1->2->3->4->5->NULL, m = 2 and n = 4,
return 1->4->3->2->5->NULL.
Note:
Given m, n satisfy the following condition:
1 ≤ m ≤ n ≤ length of list.
 
当心m=1的情况
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode *reverseBetween(ListNode *head, int m, int n) {
        // IMPORTANT: Please reset any member data you declared, as
        // the same Solution instance will be reused for each test case.
        if(head == NULL)
            return NULL;
        ListNode dummy(0);
        dummy.next = head;
        ListNode* reverseHead = &dummy;
        for(int i=1; i<m; i++)
            reverseHead = reverseHead->next;
        ListNode* pre = reverseHead;
        ListNode* cur = reverseHead->next;
        for(int i=m; i<=n; i++){
            ListNode* next = cur->next;
            cur->next = pre;
            pre = cur;
            cur = next;
        }
        reverseHead->next->next = cur;
        reverseHead->next = pre;
        return dummy.next;
    }
};
 
Jump Game II (Leetcode) 
Given an array of non-negative integers, you are initially positioned at the first index of the array.
Each element in the array represents your maximum jump length at that position.
Your goal is to reach the last index in the minimum number of jumps.
For example:
Given array A = [2,3,1,1,4]
The minimum number of jumps to reach the last index is 2. (Jump 1 step from index 0 to 1, then 3 steps to the last index.)
 
O(n)的方法
class Solution {
public:
    int jump(int A[], int n) {
        // IMPORTANT: Please reset any member data you declared, as
        // the same Solution instance will be reused for each test case.
        int range = 0;
        vector<int> step(n,INT_MAX);
        step[0] = 0;
        for(int i=0; i<n; i++){
            for(int j=min(i+A[i],n-1); j>i&&step[i]+1<step[j]; j--)
                step[j] = step[i]+1;
        }
        return step.back();
    }
};
 
Trapping Rain Water (Leetcode) 
Given n non-negative integers representing an elevation map where the width of each bar is 1, compute how much water it is able to trap after raining.
For example, 
Given [0,1,0,2,1,0,1,3,2,1,2,1], return 6.
 
 
class Solution {
public:
    int trap(int A[], int n) {
        // IMPORTANT: Please reset any member data you declared, as
        // the same Solution instance will be reused for each test case.
        int i = 0;
        int j = n-1;
        int result = 0;
        int minHeight = 0;
        while(i < j){
            if(A[i] > A[j]){
                if(minHeight < A[j]){
                    result += (A[j]-minHeight)*(j-i-1)-minHeight;
                    minHeight = A[j];
                }
                else
                    result -= A[j];
                j--;
            }
            else{
                if(minHeight < A[i]){
                    result += (A[i]-minHeight)*(j-i-1)-minHeight;
                    minHeight = A[i];
                }
                else
                    result -= A[i];
                i++;
            }
        }
        return result;
    }
};
 
LRU Cache (Leetcode)
Design and implement a data structure for Least Recently Used (LRU) cache. It should support the following operations: get and set.
get(key) - Get the value (will always be positive) of the key if the key exists in the cache, otherwise return -1.
set(key, value) - Set or insert the value if the key is not already present. When the cache reached its capacity, it should invalidate the least recently used item before inserting a new item.
 
class LRUCache{
    int cap,size;
    struct Node{
        int key,value;
        Node *next,*pre;
        Node():key(-1),value(-1),next(NULL),pre(NULL){};
        Node(int k,int v):key(k),value(v),next(NULL),pre(NULL){};
    };
    Node start;
    void MoveNext(Node* ahead){
        Node* cur = ahead->next;
        ahead->next = cur->next;
		if(ahead->next != NULL)	// 注意
	        ahead->next->pre = ahead;
        cur->next = start.next;
        if(start.next != NULL) 	// 注意
            start.next->pre = cur;
        cur->pre = &start;
        start.next = cur;
    }
public:
    LRUCache(int capacity) {
        cap = capacity;
        size = 0;
        start = Node(-1,-1);
    } 
    int get(int key) {
        Node* ahead = &start;
        for(int i=0; i<size; i++){
            if(ahead->next->key == key){
                MoveNext(ahead);
                return start.next->value;
            }
            ahead = ahead->next;
        }
        return -1;
    } 
    void set(int key, int value) {
        Node* ahead = &start;
        while(ahead->next != NULL){
            if(ahead->next->key == key){
                MoveNext(ahead);
                start.next->value = value;
                return;
            }
			ahead = ahead->next;
        }
        if(size < cap){
            ahead->next = new Node(key,value);
			ahead->next->pre = ahead;
            MoveNext(ahead);
            size++;
        }
        else{
            ahead->key = key;
            ahead->value = value;
            ahead = ahead->pre;
            MoveNext(ahead);
        }
    } 
    ~LRUCache(){
        Node* cur = start.next;
        while(cur != NULL){
            Node* temp = cur->next;
            delete cur;
            cur = temp;
        }
    }
};
 
Longest Valid Parentheses (Leetcode)
Given a string containing just the characters '(' and ')', find the length of the longest valid (well-formed) parentheses substring.
For "(()", the longest valid parentheses substring is "()", which has length = 2.
Another example is ")()())", where the longest valid parentheses substring is "()()", which has length = 4.
 
class Solution {
public:
    int longestValidParentheses(string s) {
        // IMPORTANT: Please reset any member data you declared, as
        // the same Solution instance will be reused for each test case.
        stack<int> st;
        int start = -1;
        int length = 0;
        for(int i=0; i<s.length(); i++){
            if(s[i] == '(')
                st.push(i);
            else if(!st.empty()){
                st.pop();
                if(!st.empty())	// 注意这个讨论
                    length = max(length,i-st.top());
                else
                    length = max(length,i-start);
            }
            else
                start = i;
        }
        return length;
    }
};
 
Merge k Sorted Lists (Leetcode)
Merge k sorted linked lists and return it as one sorted list. Analyze and describe its complexity.
 
注意heap默认为用小于构造最大堆
class Comp{
public:
    bool operator()(const ListNode* l1,const ListNode* l2){
        if(l2 == NULL)
            return false;
        if(l1 == NULL)
            return true;
        return l1->val > l2->val;
    }
};
class Solution {
public:
    ListNode *mergeKLists(vector<ListNode *> &lists) {
        // IMPORTANT: Please reset any member data you declared, as
        // the same Solution instance will be reused for each test case.
        ListNode dummy(0);
        ListNode* curr = &dummy;
        make_heap(lists.begin(),lists.end(),Comp());
        if(lists.size() < 1)
            return NULL;
        while(lists.front() != NULL){
            curr->next = lists.front();
            curr = curr->next;
			pop_heap(lists.begin(),lists.end(),Comp());
            lists.back() = curr->next;
            push_heap(lists.begin(),lists.end(),Comp());
        }
        return dummy.next;
    }
};
 
Sort List (Leetcode)
Sort a linked list in O(n log n) time using constant space complexity.
 
class Solution {
    ListNode* merge(ListNode* h1,ListNode* h2, ListNode* end, ListNode* &temp){	// 注意要返回尾结点
        ListNode d(0);
        ListNode* cur = &d;
        ListNode *l1(h1),*l2(h2);
        while(l1!=h2 || l2!=end){
            if(l1!=h2 && (l2==end || l1->val<=l2->val)){
                cur->next = l1;
                l1 = l1->next;
                cur = cur->next;
            }
            else{
                cur->next = l2;
                l2 = l2->next;
                cur = cur->next;
            }
        }
		temp = cur;
        cur->next = end;
        return d.next;
    }
public:
    ListNode *sortList(ListNode *head) {
        // IMPORTANT: Please reset any member data you declared, as
        // the same Solution instance will be reused for each test case.
        int length = 0;
        ListNode* cur = head;
        while(cur != NULL){
            cur = cur->next;
            length++;
        }
        ListNode dummy(0);
        dummy.next = head;
        for(int step=1; step<=length; step*=2){
            cur = &dummy;
            while(cur != NULL){
                ListNode* h1 = cur->next;
                ListNode* h2 = h1;
                for(int i=0; i<step&&h2!=NULL; i++)
                    h2 = h2->next;
                if(h2 == NULL)
                    break;
                ListNode* end = h2;
                for(int i=0; i<step&&end!=NULL; i++)
                    end = end->next;
				ListNode* temp;
				cur->next = merge(h1,h2,end,temp);
                cur = temp;
            }
        }
        return dummy.next;
    }
};

## Bit Count
- Precompute_16bit:
```
// static char bits_in_16bits [0x1u << 16] ;      
int bitcount (unsigned int n) {
   // works only for 32-bit ints
   return bits_in_16bits [n & 0xffffu]
        + bits_in_16bits [(n >> 16) & 0xffffu] ;
}
```
- Parallel Count
```
#define TWO(c)     (0x1u << (c))
#define MASK(c)    (((unsigned int)(-1)) / (TWO(TWO(c)) + 1u))
#define COUNT(x,c) ((x) & MASK(c)) + (((x) >> (TWO(c))) & MASK(c))

int bitcount (unsigned int n) {
    n = COUNT(n, 0) ;
    n = COUNT(n, 1) ;
    n = COUNT(n, 2) ;
    n = COUNT(n, 3) ;
    n = COUNT(n, 4) ;
    /* n = COUNT(n, 5) ;    for 64-bit integers */
    return n ;
}
```
- Nifty Parallel Count
```
#define MASK_01010101 (((unsigned int)(-1))/3)
#define MASK_00110011 (((unsigned int)(-1))/5)
#define MASK_00001111 (((unsigned int)(-1))/17)

int bitcount (unsigned int n) {
    n = (n & MASK_01010101) + ((n >> 1) & MASK_01010101) ;  // 00->00, 01->01, 10->01, 11->10 
    n = (n & MASK_00110011) + ((n >> 2) & MASK_00110011) ; 
    n = (n & MASK_00001111) + ((n >> 4) & MASK_00001111) ; 
    return n % 255 ;
}
```
- MIT HAKMEM (n^k mod (n-1) = 1)
```
int bitcount(unsigned int n) {
    /* works for 32-bit numbers only    */
    /* fix last line for 64-bit numbers */
    register unsigned int tmp;
    // 011111111111 is 01 001001 001001 ...
    // 033333333333 is 11 011011 011011 ...
    // 030707070707 is 11 000111 000111 ...
    tmp = n - ((n >> 1) & 033333333333)
            - ((n >> 2) & 011111111111);
    return ((tmp + (tmp >> 3)) & 030707070707) % 63;
}
```
- JDK
```
/**
 * Returns the number of one-bits in the two's complement binary
 * representation of the specified <tt>int</tt> value.  This function is
 * sometimes referred to as the <i>population count</i>.
 *
 * @return the number of one-bits in the two's complement binary
 *     representation of the specified <tt>int</tt> value.
 * @since 1.5
 */
public static int bitCount(int i) {
    i = i - ((i >>> 1) & 0x55555555);   // 00->00, 01->01, 10->01, 11->10 
    i = (i & 0x33333333) + ((i >>> 2) & 0x33333333);
    i = (i + (i >>> 4)) & 0x0f0f0f0f;
    i = i + (i >>> 8);
    i = i + (i >>> 16);
    return i & 0x3f;
}
```