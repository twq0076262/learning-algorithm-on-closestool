# 算法 11：堆——神奇的优先队列（上）

堆是什么？是一种特殊的完全二叉树，就像下面这棵树一样。

![picture11.1](images/11.1.png)

有没有发现这棵二叉树有一个特点，就是所有父结点都比子结点要小（注意：圆圈里面的数是值，圆圈上面的数是这个结点的编号，此规定仅适用于本节）。符合这样特点的完全二叉树我们称为最小堆。反之，如果所有父结点都比子结点要大，这样的完全二叉树称为最大堆。那这一特性究竟有什么用呢？
        
假如有 14 个数分别是 99、5、36、7、22、17、46、12、2、19、25、28、1 和 92。请找出这 14 个数中最小的数，请问怎么办呢？最简单的方法就是将这 14 个数从头到尾依次扫一遍，用一个循环就可以解决。这种方法的时间复杂度是 O(14)也就是 O(N)。

```

    for(i=1;i<=14;i++)
    {
        if(a[ i]<min)min=a[ i];
    }

```

现在我们需要删除其中最小的数，并增加一个新数 23，再次求这 14 个数中最小的一个数。请问该怎么办呢？只能重新扫描所有的数，才能找到新的最小的数，这个时间复杂度也是 O(N)。假如现在有 14 次这样的操作（删除最小的数后并添加一个新数）。那么整个时间复杂度就是 O(142)即 O(N2)。那有没有更好的方法呢？堆这个特殊的结构恰好能够很好地解决这个问题。
        
首先我们先把这个 14 个数按照最小堆的要求（就是所有父结点都比子结点要小）放入一棵完全二叉树，就像下面这棵树一样。

![picture11.2](images/11.2.png)

很显然最小的数就在堆顶，假设存储这个堆的数组叫做h的话，最小数就是 h[ 1]。接下来，我们将堆顶的数删除，并将新增加的数 23 放到堆顶。显然加了新数后已经不符合最小堆的特性，我们需要将新增加的数调整到合适的位置。那如何调整呢？

![picture11.3](images/11.3.png)

向下调整！我们需要将这个数与它的两个儿子 2 和 5 比较，并选择较小一个与它交换，交换之后如下。

![picture11.4](images/11.4.png)

我们发现此时还是不符合最小堆的特性，因此还需要继续向下调整。于是继续将 23 与它的两个儿子 12 和7比较，并选择较小一个交换，交换之后如下。

![picture11.5](images/11.5.png)

到此，还是不符合最小堆的特性，仍需要继续向下调整直到符合最小堆的特性为止。

![picture11.6](images/11.6.png)

我们发现现在已经符合最小堆的特性了。综上所述，当新增加一个数被放置到堆顶时，如果此时不符合最小堆的特性，则将需要将这个数向下调整，直到找到合适的位置为止，使其重新符合最小堆的特性。

![picture11.7](images/11.7.png)

向下调整的代码如下。

```

    void siftdown(int i) //传入一个需要向下调整的结点编号i，这里传入1，即从堆的顶点开始向下调整 
    {
	    int t,flag=0;//flag用来标记是否需要继续向下调整 
	    //当i结点有儿子的时候（其实是至少有左儿子的情况下）并且有需要继续调整的时候循环窒执行
	    while( i*2<=n && flag==0 )
	    {        
	        //首先判断他和他左儿子的关系，并用t记录值较小的结点编号 
	        if( h[ i] > h[ i*2] )
	            t=i*2;
	        else
	            t=i; 
	        //如果他有右儿子的情况下，再对右儿子进行讨论 
	        if(i*2+1 <= n)
	        {
	            //如果右儿子的值更小，更新较小的结点编号  
	            if(h[ t] > h[ i*2+1])
	                t=i*2+1;
	        }
	        //如果发现最小的结点编号不是自己，说明子结点中有比父结点更小的  
	        if(t!=i)
	        {
	            swap(t,i);//交换它们，注意swap函数需要自己来写
	            i=t;//更新i为刚才与它交换的儿子结点的编号，便于接下来继续向下调整 
	        }
	        else
	            flag=1;//则否说明当前的父结点已经比两个子结点都要小了，不需要在进行调整了 
	    }
    }

```

我们刚才在对 23 进行调整的时候，竟然只进行了 3 次比较，就重新恢复了最小堆的特性。现在最小的数依然在堆顶为 2。之前那种从头到尾扫描的方法需要 14 次比较，现在只需要 3 次就够了。现在每次删除最小的数并新增一个数，并求当前最小数的时间复杂度是 O(3)，这恰好是 O(log214)即 O(log2N)简写为 O(logN)。假如现在有 1 亿个数（即 N=1 亿），进行 1 亿次删除最小数并新增一个数的操作，使用原来扫描的方法计算机需要运行大约 1 亿的平方次，而现在只需要 1 亿*log1 亿次，即 27 亿次。假设计算机每秒钟可以运行 10 亿次，那原来则需要一千万秒大约 115 天！而现在只要 2.7 秒。是不是很神奇，再次感受到算法的伟大了吧。
        
说到这里，如果只是想新增一个值，而不是删除最小值又该如何操作呢？即如何在原有的堆上直接插入一个新元素呢？只需要直接将新元素插入到末尾，再根据情况判断新元素是否需要上移，直到满足堆的特性为止。如果堆的大小为 N（即有 N 个元素），那么插入一个新元素所需要的时间也是 O(logN)。例如我们现在要新增一个数 3。

![picture11.8](images/11.8.png)

先将 3 与它的父结点 25 比较，发现比父结点小，为了维护最小堆的特性，需要与父结点的值进行交换。交换之后发现还是要比它此时的父结点 5 小，因此需要再次与父结点交换。至此又重新满足了最小堆的特性。向上调整完毕后如下。

![picture11.9](images/11.9.png)

向上调整的代码如下。

```

    void siftup(int i) //传入一个需要向上调整的结点编号i
    {
	    int flag=0; //用来标记是否需要继续向上调整
	    if(i==1)  return; //如果是堆顶，就返回，不需要调整了    
	    //不在堆顶 并且 当前结点i的值比父结点小的时候继续向上调整 
	    while(i!=1 && flag==0)
	    {
	        //判断是否比父结点的小 
	        if(h[ i]<h[ i/2])
	            swap(i,i/2);//交换他和他爸爸的位置 
	        else
	            flag=1;//表示已经不需要调整了，当前结点的值比父结点的值要大 
	        i=i/2; //这句话很重要，更新编号i为它父结点的编号，从而便于下一次继续向上调整 
	    }
    }

```



