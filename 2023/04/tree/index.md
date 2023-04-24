# Tree

因為他跟倒過來的樹很相似，所以被稱為 Tree，日常中，許多應用都與他脫離不了關係，像是組織架構圖、資料儲存、 sitemap 等等...  

若熟悉 Linked List 將會更容易理解 Tree ，想了解 Linke List 可以參考[文章](https://xxxvitoxxx.github.io/2023/03/linked_list/)。  
<!--more-->

## Introduction

{{< image src="https://imgur.com/pJN1Paw.png" alt="Introduction" caption="圖片來源 https://www.geeksforgeeks.org/introduction-to-tree-data-structure-and-algorithm-tutorials/" linked="true" >}}  

Tree 是一種非線性資料結構，由 node 集合組成的階層式結構，且 Node 之間會透過 Edge 連接，表明兩個節點之間的關係。  

## Basic Terminologies in Tree Data Structure

### Node

是 Tree 基本組成的一部分，它是一種結構，包含本身資料及 Child Node 的 Pointer 。  

### Edge

是 Tree 的另一個基本組成，它連接兩個 Node 表示兩個 Node 之間的關係，每個 Node 可能會有數條 Outgoing Edge ，除了 Root Node 之外，每個 Node 一定會有一條 Incoming Edge 。  

### Root

在 Tree 中， Root 是第一個 Node ，它是 Tree 的 Initial Node ，如上圖 A 為 Root 。  

### Parent

A 通過 Outgoing Edge 連接到 B 和 C ，所以 A 為 B 和 C 的 Parent 。  

### Child

A 通過 Outgoing Edge 連接到 B 和 C ，所以 B 和 C 為 A 的 Child 。  

### Siblings

擁有相同 Parent 的 Node 稱為 Siblings ，如上圖 D 、 E 稱為 Siblings 。  

### Ancestor of a Node

從 Root 透過 Edge 到該 Node 中的任何 Node 都稱為該 Node 的祖先，如上圖， D 的祖先有 B 和 A 。  

### Descendant

該 Node 透過 Outgoing 到 Leaf Node 中的所有 Node 稱為該 Node 的 Descendant ，如上圖。 I 、 M 、 N 為 E 的後裔。  

### Neighbor of a Node

該 Node 的 Parent 與 Child 都稱為該 Node 的鄰居，如上圖， D 的鄰居有 B 和 H 。  

### Leaf

沒有任何 Child 的 Node ，也被稱為 External Node ，如上圖 K 、 L 、 M 、 N 、 O 、 P 為 Leaf 。  

### Subtree

Node 與其 Descendant 組成的 Tree 。  

### Level

從 Root 到 Node 的 Edge 數稱為 Level ，如上圖 I 的 Level 為 3 。  

## Properties of a Tree

### Number of Edges

Node 到 Node 間只有一條 Edge ，如果 Tree 有 n 個 Node ，那它就有 n-1 條 Edge 。  

### Depth of a Node

Root 到 Node 的 Edge 數，如 B 的 Degree 為 1 ， I 的 Degree 為 3 。  

### Height of a Tree

Root 到 Leaf 的最大 Edge ，如上圖，樹高為 4 。  

### Height of a Node

Node 到 Leaf 的最大 Edge ，如上圖 C 的 Height 為 3 。  

### Degree of a Node

Node 擁有的 Child Node 數量稱為 Node 的 Degree ，如上圖 A 有 B 、 C 兩個 Child Node ，所以 A 的 Degree 為 2 ;而 L 為 Leaf Node 所以 L 的 Degree 為 0 。  

### Degree of a Tree

樹中 Node 最大的 Degree，如上圖 A 、 B 、 H 、 I 、 C 、 J 的 Degree 都是 2 ，為該樹 Node 最大的 Degree ，所以該樹的 Degree 為 2 。  

## Types of Tree in Data Structure

Tree 有非常多種類型，以下會簡單介紹幾種 Tree 的特性。  

### General Tree

{{< image src="https://imgur.com/48sTcWd.png" alt="General Tree" caption="圖片來源 https://www.simplilearn.com/tutorials/data-structure-tutorial/trees-in-data-structure" linked="true">}}  
一般樹是一種對層次結構沒有限制的樹。  

#### Properties

- Node 間的資料是無序的  
- 可以有多個 Child ，因此， Root 就像其他 Subtree 的超集  

### Binary Tree

{{< image src="https://imgur.com/MzUSr9j.png" alt="Binary Tree" caption="圖片來源 https://www.simplilearn.com/tutorials/data-structure-tutorial/trees-in-data-structure" linked="true" >}}  
二元樹  

#### Properites

- 每個 Node 只能有 0~2 個 Child ，分別稱為 Left Node 和 Right Node  

#### Types of Binary Tree

二元樹依 Child Mode 可分為三種類型。  

##### Full Binary Tree

每個 Node 都有 0~2 個 Child 。  

##### Complete Binary Tree

除了 Leaf 之外，每個 Node 都有 0~2 個 Child ，但 Leaf 必須在同一個 Level 。  

##### Perfect Binary Tree

除了 Leaf 之外，每個 Node 都有 2 個 Child ，且所有 Leaf 都在同一個 Level 。  

### Binary Search Tree

{{< image src="https://imgur.com/7O4UQlV.png" alt="Binary Search Tree" caption="圖片來源 https://www.simplilearn.com/tutorials/data-structure-tutorial/trees-in-data-structure" linked="true" >}}

二元搜尋樹（BST）也是二元樹的一種，它擁有二元樹的所有屬性，但基於某些額外特性，使得二元搜尋樹來的相對高效。  

#### Properties

- 遵循二元樹的所有特性  
- 所有 Node 的資料不會重複  
- Right Node 必定大於 Parent 且 Parent 必定大於 Left Node  

#### Advantages of BST

1. 與二元樹相比較為高效，因為執行各種操作的時間複雜度較低  
2. 由於 Node 的資料是有序的，所以搜尋變得更簡單  
3. 因為有序的特性，要查詢某範圍資料的 Node 時使得較為簡單  

#### Disadvantages of BST

{{< image src="https://imgur.com/dnm9ybJ.png" alt="disadvantages of BST" caption="圖片來源 https://runestone.academy/ns/books/published/pythonds/Trees/SearchTreeAnalysis.html" linked="true" >}}  

為了解決過度傾斜的問題，而有了自平衡二元搜尋樹。  

### Self-Balanced Binary Search Tree

自平衡二元搜尋樹可以避免二元搜尋樹出現過度傾斜的狀況。  

#### Properties

- 假設 m 是 Left Subtree 的高度， n 是 Right Subtree 的高度，其 m-n 需等於 1 、 0 或是 -1  
- 一但 m-n 相差超過 1 ，它們會自我平衡，使左右子樹高度不會相差大於 1  

#### Types of Self-Balanced Binary Search Tree

屬於自平衡二元搜尋樹還有以下幾種類型。  

- AVL Tree  
- Red Black Tree  
- B Tree  
- B+ Tree  
- Splay Tree  
- Priority Search Tree  

## Reference

- [7.3. Vocabulary and Definitions](https://runestone.academy/ns/books/published/pythonds/Trees/VocabularyandDefinitions.html)  
- [Data Structure and Algorithms - Tree](https://www.tutorialspoint.com/data_structures_algorithms/tree_data_structure.htm)  
- [Data structures: Introduction to Trees - youtube](https://www.youtube.com/watch?v=qH6yxkw0u78)  
- [An Introduction to Tree in Data Structure](https://www.simplilearn.com/tutorials/data-structure-tutorial/trees-in-data-structure)  
