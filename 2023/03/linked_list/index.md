# Linked List

Linked List 是一種線性數據結構，由多個 Node 組成，每個 Node 中包含的 Point 會指向下一個 Node ，藉此將多個 Node 連結起來，適合用來處理動態數據。  
其他資料結構如 Tree 和 Graph 都是基於 Linked List 組成的，所以瞭解 Node 與 Linked List 之間的關係及應用，這有助於你未來在學習 Tree 與 Graphs 時會更加輕鬆。  
<!--more-->

##  What is a Linked List?

![Linked List](https://imgur.com/tc4BAy7.png)

- Linked List 是一種線性數據結構，它可以動態的存儲數據元素  
- Node 代表數據元素及指針，其中指針會鏈接下一個 Node ，以此形成一個鏈狀結構  
- 同個 Linked List 中的 Node 資料的型態可以不同  
- 最後一個 Node 的指針會指向 `NULL`  
- 不會浪費內存空間  

## Basic Operations

Linked List 具有以下操作。  

- Insertion **−** Adds an element at the beginning of the list  
- Deletion **−** Deletes an element at the beginning of the list  
- Insert Last **−** Adds an element at the end of the list  
- Delete Last **−** Deletes an element from the end of the list  
- Insert After **−** Adds an element after an item of the list  
- Delete **−** Deletes an element from the list using the key  
- Display forward **−** Displays the complete list in a forward manner  
- Display backward **−** Displays the complete list in a backward manner  

## Linked List and Array Difference

Linked List 和 Array 都屬於線性資料結構，但兩者的使用時機與操作的時間複雜度稍微不太一樣，在選擇要使用哪種 list 前可以依使用情境或會頻繁使用的操作去做取捨。  

### Array

會分配一段連續記憶體位置來儲存，一般來說 Array 的長度是固定的且資料型態都要相同，以 go 來說，當你宣告 Array 時，同時也需宣告 Array 的長度，所以不管有無使用都會分配記憶體空間給它。  

```go=1
arr := [3]int{}
```  

可以透過 index 直接取得特定資料，查詢速度較快。  

#### When to Use

- 需要快速取得資料時  
- 不需要頻繁新增和刪除資料時  
- 資料的數量不會有太大的變更  

### Linked List

將多個 Node 有順序性的關聯起來，不需要連續的記憶體空間，資料數量可以是動態的，因此不需要預留空間，不會有 resize 問題。  

#### When to Use

- 無法預期資料數量時  
- 需要頻繁新增和刪除資料時  
- 不需要頻繁查詢資料時  

## Types of Linked List in Data Structure

Linked List 可以分為以下四種類型。  

### Singly Linked List

單鏈表是最常見的類型，每個節點都包含數據跟指向下一個 Node 的指針位置（通常會用 next 表示）。    

[程式碼範例](https://github.com/xxxVitoxxx/data-structure-and-algorithm/blob/main/data-structure/linked_list/singly_linked_list/linked_list.go)  

![Singly Linked List](https://imgur.com/3evtkGy.png)  

### Doubly Linked List

雙向鏈表中的 Node 會有存儲兩個指針的位置，第一個會儲存上一個 Node 位置（prev），第二個會儲存下一個 Node 位置，因此你可以從任一個 Node 存取上一個 Node 及下一個 Node。    

[程式碼範例](https://github.com/xxxVitoxxx/data-structure-and-algorithm/blob/main/data-structure/linked_list/doubly_linked_list/linked_list.go)  

![Doubly Linked List](https://imgur.com/BJxxqCi.png)  

### Circular Singly  Linked List

循環鏈表與單鏈表很相似，唯一不同的是，最後一個節點的 next 會指向第一個節點，以此實現循環。  

[程式碼範例](https://github.com/xxxVitoxxx/data-structure-and-algorithm/blob/main/data-structure/linked_list/circular_singly_linked_list/linked_list.go)  

![Singly Circular Linked List](https://imgur.com/iuGQMuU.png)  

### Circular Doubly Linked List

雙向循環鏈表與雙項鏈表相似，不同的地方在於最後一個節點的 next 會指向第一個節點，而第一個節點的 prev 會指向最後一個節點，以此達到雙向循環。  

[程式碼範例](https://github.com/xxxVitoxxx/data-structure-and-algorithm/blob/main/data-structure/linked_list/circular_doubly_linked_list/linked_list.go)  

![Doubly Circular Linked List](https://imgur.com/gPyZOfq.png)  

## Reference

- [Linked List in A Data Structure: All You Need to Know](https://www.simplilearn.com/tutorials/data-structure-tutorial/linked-list-in-data-structure)  
