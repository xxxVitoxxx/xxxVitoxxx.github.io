# Binary Tree

二元樹是一種樹狀的資料結構，本文章將會介紹六種不同的二元樹及如何實作。  
<!--more-->

## Introduction

{{< image src="https://imgur.com/XuTFaW3.png" alt="Binary Tree" >}}

二元樹是一種分層式的資料結構，由 Node 組成且每個 Node 最多只能有 2 個 Child ，分別稱為 Left Node 和 Right Node 。  

Node 由以下三點組成：
- data element
- pointer of left node
- pointer of right node

## Types of Binary Tree

### Full Binary Tree

{{< image src="https://imgur.com/ICOMNtY.png" alt="Full Binary Tree" caption="圖片來源 https://www.programiz.com/dsa/binary-tree" linked="true" >}}  

每個 Node  都有 0 或 2 個 Child 時，稱為 Full Binary Tree 。  

### Complete Binary Tree

{{< image src="https://imgur.com/tlW6lAc.png" alt="Complete Binary Tree" caption="圖片來源 https://www.programiz.com/dsa/binary-tree" linked="true" >}}  

Complete Binary Tree 跟 Full Binary Tree 很相似，但有以下幾點不同。  

1. 除了最後一層之外，所有層都必須被完整填充
2. 最後一層（Leaf Layer）從左邊開始填充  
3. 最後一個 Leaf（`6`） 可能會沒有 Right Sibling  

### Perfect Binary Tree

{{< image src="https://imgur.com/lJZISHn.png" alt="Perfect Binary Tree" caption="圖片來源 https://www.programiz.com/dsa/binary-tree" linked="true" >}}  

所有 Leaf 都在同一層，且除了 Leaf 之外的 Node 都有 2 個 Child 。  

### Degenerate or Pathological Tree

{{< image src="https://imgur.com/dTbGDT3.png" alt="Degenerate or Pathological Tree" caption="圖片來源 https://www.programiz.com/dsa/binary-tree" linked="true" >}}  

稱為退化樹或病態樹，除了 Leaf 之外，每個 Node 都有一個 Child ，不是 Left Node 就是 Right Node 。  

### Skewed Binary Tree

{{< image src="https://imgur.com/dg2zAUg.png" alt="Skewed Binary Tree" caption="圖片來源 https://www.programiz.com/dsa/binary-tree" linked="true" >}}  

也是退化樹/病態樹的一種，其 Child 全部都在左側或右側。依照向左或向右傾斜又可分為 `Left-Skewed Binary Tree` 和 `Right-Skewed Binary Tree` 。  

### Balanced Binary Tree

{{< image src="https://imgur.com/VtBrse1.png" alt="Balanced Binary Tree" caption="圖片來源 https://www.programiz.com/dsa/binary-tree" linked="true" >}}  

每個 Node 的 Left Subtree 和 Right Subtree 高度只相差 1 或 0 。  

## Implement Bainary Tree

用 Linked List 實作 Binary Tree ，[完整程式碼](https://github.com/xxxVitoxxx/data-structure-and-algorithm/blob/main/data-structure/tree/binary_tree/binary_tree.go)。  

### Declare Structure

```go
type Node struct {
	value int
	left, right *Node
}

type BinaryTree struct {
	root *Node
}

func createNode(value int) *Node {
	return &Node{value: value}
}

func newBinaryTree(value int) *BinaryTree {
	return &BinaryTree{
		root: &Node{value: value},
	}
}
```  

第 1 ~ 4 行：宣告 Node 有三個屬性，分別是資料跟 Left Node 和 Right Node 。  
第 6 ~ 8 行：宣告名稱為 Binary Tree 的 Linked List 。  
第 10 ~ 12 行：建立 Node 的方法。  
第 14 ~ 18 行：建立 Linked List 的方法。  

###  Implement Count the Number of Node in a Tree

```go
func (root *Node) GetNodeCount() int {
	if root == nil {
		return 0
	}

	return root.left.GetNodeCount() + root.right.GetNodeCount() + 1
}
```  

使用 recursion 實作計算該樹共有多少 Node 。  

### Implement Count Degree of a Tree

```go
func (root *Node) GetTreeDegree() int {
	if root != nil {
		var maxDegree int
		queue := append([]*Node{}, root)
		for len(queue) != 0 {
			element := queue[0]
			queue = queue[1:]
		
			var degree int
			if element.left != nil {
				degree += 1
				queue = append(queue, element.left)
			}

			if element.right != nil {
			    degree += 1
			    queue = append(queue, element.right)
			}

			if degree > maxDegree {
			    maxDegree = degree
			}
		}
		return maxDegree
	}
	return 0
}
```

實作計算樹的 Degree 。  

### Implement Search

```go
func (root *Node) Search(value int) (*Node, error) {
	if root != nil {
		queue := append([]*Node{}, root)
		for len(queue) != 0 {
			element := queue[0]
			queue = queue[1:]
			if element.value == value {
				return element, nil
			}

			if element.left != nil {
				queue = append(queue, element.left)
			}
			
			if element.right != nil {
				queue = append(queue, element.right)
			}
		}
	}

	return &Node{}, errors.New("the value not exist in node of a tree")
}
```

實作搜尋資料是否存在樹中的 Node 。  

### Implement Preorder Traversal

```go
func (root *Node) Preorder() {
	if root != nil {
		fmt.Printf("-> %v ", root.value)
		root.left.Preorder()
		root.right.Preorder()
	}
}
```

使用 recursion 實作前序遍歷。  
前序遍歷的輸出順序為： `Root` -> `Left Node` -> `Right Node` 。  

### Implement Inorder Traversal

```go
func (root *Node) Inorder() {
	if root != nil {
		root.left.Inorder()
		fmt.Printf("-> %v ", root.value)
		root.right.Inorder()
	}
}
```

使用 recursion 實作中序遍歷。  
中序遍歷的輸出順序為： `Left Node` -> `Root` -> `Right Node` 。  

### Implement Postorder Traversal

```go
func (root *Node) Postorder() {
	if root != nil {
		root.left.Postorder()
		root.right.Postorder()
		fmt.Printf("-> %v ", root.value)
	}
}
```

使用 recursion 實作後序遍歷。  
後序遍歷的輸出順序為： `Left Node` -> `Right Node` -> `Root` 。  

### Implement Layer-Order Traversal

```go
func (root *Node) LayerOrder() {
	if root == nil {
		return
	}

	var queue []*Node
	queue = append(queue, root)
	for len(queue) != 0 {
		node := queue[0]
		queue = queue[1:]
		fmt.Printf("-> %v ", node.value)
		if node.left != nil {
			queue = append(queue, node.left)
		}
		
		if node.right != nil {
			queue = append(queue, node.right)
		}
	}
}
```
  
使用 queue 實作階層式遍歷。  
階層式遍歷的輸出順序為先輸出 `Root` 再依序 `Left Node` -> `Right Node` 輸出。  
