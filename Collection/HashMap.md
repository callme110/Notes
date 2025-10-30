## HashMap 底层机制和扩容原理

HashMap数据结构由数组、链表和红黑树组成，拉链法解决哈希冲突，红黑树解决链表过长问题

![image-20251030215648848](%7Bphoto%7D/image-20251030215648848.png)

~~~
public class HashMap<K,v> extends AbstractMap<K,v> 
	implements Map<K,v>, Cloneable, Serializable{
	/**
     * 默认容量大小，16
     */
    	static final int DEFAULT_INITIAL_CAPACITY = 1 << 4;
    /**
     * 负载系数，容量超过负载系数的时候会触发扩容，16*0.0.75=12个
     */
    	static final float DEFAULT_LOAD_FACTOR = 0.75f;
    /**
     * 容量最大值，2的30次方
     */
    	static final int MAXIMUM_CAPACITY = 1 << 30;
    /**
     * 数组的默认值，空数组
     */
    	static final Entry<?, ?>[] EMPTY_TABLE = {};
    	transient Entry<K, V>[] table = (Entry<K, V>[]) EMPTY_TABLE;

    /**
     * 链表的节点
     */
    	static class Entry<K, V> implements Map.Entry<K, V> {
            final K key;
            V value;
            Entry<K, V> next;
            int hash;
    	}

    /**
     * 红黑树的节点
     */
        static final class TreeNode<K, V> extends LinkedHashMap.Entry<K, V> {
            TreeNode<K, V> parent;
            TreeNode<K, V> left;
            TreeNode<K, V> right;
            TreeNode<K, V> prev;
            boolean red;

            TreeNode(int hash, K key, V val, Node<K, V> next) {
                super(hash, key, val, next);
            }
        }
    }
}
~~~

#### 红黑树

自平衡二叉查找树。

* 节点是红色或者黑色
* 根是黑色
* 所有叶子都是黑色
* 每个红色节点必须有两个黑色的子节点（所有路径中不能有两个连接的红节点）
* 任意节点到叶子节点的路径都包含相同数目的黑色节点

最长路径==（红色节点的数目\==黑色节点数目）\== 2*黑色节点

红黑树插入的节点是红色，来不断调整树

**情况一**：插入根节点

插入的根节点由红色变为黑色

**情况二：** N的父节点是黑色

**情况三：**

N 的父节点是红色（节点 P 为红色，其父节点必然为黑色），叔叔节点 U 也是红色。由于 P 和 N 均为红色，所有性质4被打破，此时需要进行调整。

这种情况下，先将 P 和 U 的颜色染成黑色，再将 G 的颜色染成红色。此时经过 G 的路径上的黑色节点数量不变，性质5仍然满足。但需要注意的是 G 被染成红色后，可能会和它的父节点形成连续的红色节点，此时需要递归向上调整。

![image-20251030222403958](%7Bphoto%7D/image-20251030222403958.png)