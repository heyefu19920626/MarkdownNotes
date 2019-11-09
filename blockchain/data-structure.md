
#### Merkle Tree

- Hash
    + Hash是一个把任意长度的数据映射成固定长度数据的函数
- Hash List(Hash List可以看作一种特殊的Merkle Tree，即树高为2的多叉Merkle Tree)
- Merkle Tree
    + MT是一种树，大多数是二叉树，也可以多叉树，无论是几叉树，它都具有树结构的所有特点
    + Merkle Tree的叶子节点的value是数据集合的单元数据或者单元数据HASH
    + 非叶子节点的value是根据它下面所有的叶子节点值，然后按照Hash算法计算而得出的