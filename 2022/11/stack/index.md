# Stack

<!--more-->

stack 是一種線性的資料結構，其新增資料或刪除資料都在同一端，這意味著，若你想刪除第一筆資料，就得先從最後一筆資料開始依序刪除。  

![info](https://imgur.com/lTGIbl5.png)  

遵循後進先出（LIFO, Last-in-First-out）或先進後出（FILO, First-in-Last-out）原則，像疊盤子，假設一次只能拿一個盤子，若你想疊盤子，就只能疊在最上面;若想拿到最下面的盤子，就得先從最上面的盤子開始一個一個拿走。  

因 stack 特性，需實作以下幾種基本操作：  

- Push: 新增資料到 stack 的頂端（top）  
- Pop: 移除 stack 中頂端（top）的資料  
- Peek: 取得頂端（top）的資料  

並實作以下幾種操作輔助：  

- IsEmpty: 檢查 stack 是否為空  
- IsFull: 檢查 stack 是否被填滿（當 stack 是有長度限制才需要實作）  
- Size: stack 大小  
- Print: 印出 stack 資料  

**Push operation**

這是在 stack 實現 Push 操作的圖形說明，它展示了當資料被新增到 stack 時， stack 如何由左至右增長。  

![push](https://imgur.com/ocbmdFs.png)  

**Pop operation**  

這是在 stack 實現 Pop 操作的圖形說明，它展示了當資料從 stack 被移除時， stack 如何由左至右減少。  

![pop](https://imgur.com/u54q5tI.png)  

## Implement of the stack in golang

可以用 [slice](./slice.go) 與 [linked list](./linked_list.go) 實作 stack，這邊用 slice 示範如何實作。  

### slice based stack

#### Declaring stack

```go
type StackSlice struct {
    top      int
    capacity int
    stack    []int
}

func NewStackSlice(capacity int) *StackSlice {
    return &StackSlice{
        top:      -1,
        capacity: capacity,
        stack:    make([]int, 0, capacity),
    }
}
```  

stack 結構有以下三種屬性：  

1. **top**: stack 中最後一筆資料的位置（當沒有資料時; top 為 -1）  
2. **capacity**: stack 的大小  
3. **stack**: int 屬性的 slice  

#### Implementing a IsEmpty operation

檢查 stack 是否有資料。  

```go
func (s *StackSlice) IsEmpty() bool {
    return s.top == -1
}
```  

#### Implementing a IsFull operation

檢查 stack 是否還有空間可以存放資料。  

```go
func (s *StackSlice) IsFull() bool {
    return s.top+1 == s.capacity
}
```  

#### Implementing a Size operation

回傳 stack 大小。  

```go
func (s *StackSlice) Size() int {
    return s.top + 1
}
```  

#### Implementing a Peek operation

回傳 stack 最後一筆資料。  

```go
func (s *StackSlice) Peek() (int, error) {
    if s.IsEmpty() {
        return 0, errors.New("stack underflow")
    }
    return s.stack[s.top], nil
}
```  

#### Implementing a Print operation

印出 stack 中所有資料。  

```go
func (s *StackSlice) Print() {
    for i := 0; i <= s.top; i++ {
        fmt.Printf("%d  ", s.stack[i])
    }
    fmt.Println()
}
```  

#### Implementing a Push operation

新增資料到 stack

```go
func (s *StackSlice) Push(data int) error {
    if s.IsFull() {
        return errors.New("stack overflow")
    }

    s.top++
    s.stack[s.top] = data
    return nil
}
```  

Push 實現的方法如下所述：  

1. 第 2 行： 先檢查 stack 是否還有空間可以存放資料。  
2. 第 6 行： 調整 top 位置  
3. 第 7 行： 將資料指定到 top 位置  

#### Implementing a Pop operation

從 stack 移除資料  

```go
func (s *StackSlice) Pop() error {
    if s.IsEmpty() {
        return errors.New("stack underflow")
    }

    s.stack = s.stack[:s.top]
    s.top--
    return nil
}
```  

Pop 實現的方法如下所述：  

1. 第 2 行： 檢查 stack 是否有資料  
2. 第 6 行： 從 slice 移除 top 的資料  
3. 第 7 行： 調整 top 位置  

## application of stack in data structure

這邊舉例幾個 stack 的應用  

- Parentheses Checking  
- Expression Evaluation and Conversion  
- Backtracking  
- String Reversal  
- Towers of Hanoi  

### 1. Parentheses Checking  

檢查 `()` 、 `[]` 、 `{}` 括號在是否有效，同時檢查左右括號是否是平衡的。  

例如 `[(){}]` 是平衡的，而 `{[}]` 是不平衡的。  

### 2. Expression Evaluation and Conversion  

表達式的評估（計算）與轉換，表達式有以下三種型態：  

**Prefix Expression（前輟表達式）**  
又稱波蘭表達式，其特點是沒有括號且運算符號在數字前面。  

- X  
- +XY  
- *+XY-XY  

**Infix Expression（中輟表達式）**  
一般人們可以識別、計算的運算式。  

- X  
- X+Y  
- (X+Y)*(X-Y)  

**Postfix Expression（後輟表達式）**  
又稱逆波蘭表達式，其特點是沒有括號且運算符號在數字後面。  

- X  
- XY+  
- XY+XY-*  

#### Evaluation

表達式的計算是基於運算符（operator）的優先級（precedence）和結合性（associativity），當表達式有多個運算符時，會依據運算符的優先級和結合性做評估，優先級高的運算符先求值，優先級低的最後求值。  

```
30 + 8 * 3 / 2
```  

在上面的中輟表達式中，`*` 和 `/` 的優先級相同，而 `+` 的優先級最低，根據運算符號的優先級，會先計算 `*` 和 `/` ，再做 `+` ，由於 `*` 和 `/` 有相同優先級，根據 [golang operator associativity](https://www.tutorialspoint.com/go/go_operators_precedence.htm) 計算順序會先執行 `*` ，再執行 `/` ，最後才執行 `+` ，因此上面的表達式的求值順序如下：  

```
8 * 3   ---> 24
24 / 2  ---> 12
30 + 12 ---> 42
```  

#### Conversion

由於需要額外處理優先級，因此計算機很難評估中輟表達式，中輟表達式是人類編寫和識別的方式，通常也是程式的輸入方式，因此通常會將中輟主換成前輟或後輟。  

### 3. Backtracking

Backtracking 是一種窮舉的搜尋法，會找尋所有可能的答案，可分為兩個概念；  

- enumerate： 列出可能的答案在測試答案是否正確  
- pruning： 在找尋答案的過程中，只要遇到不符合的條件就回朔到上一步，不在繼續往下尋找  

#### 8 queens problem

如何在 8 x 8 的西洋棋盤上放 8 個皇后的棋子，且這 8 個皇后不會互相攻擊（也就是每個皇后的橫向、縱向、斜對角都沒有出現其他皇后）的所有解法。  

#### Sudoku

數獨，是一種數學邏輯遊戲，你必須用 1 到 9 去填滿 9 x 9 的格子，且各數字的橫向、縱向皆不會出現重複的數字。  

### 4. String Reversal

利用 stack 先進後出的特性，反轉字串。  

### 5. Towers of Hanoi

河內塔是根據傳說形成的數學問題，有三根桿子分別是 A、B、C ，有 N 個圓盤疊在桿子 A 上，且這 N 個圓盤的尺寸由下到上依序變小，需在遵循以下條件的同時將所有圓盤移至桿子 C :  

- 同一時間只能移動一個圓盤  
- 小圓盤必須在大圓盤上  

## Reference

[Data Structure and Algorithms - Stack](https://www.tutorialspoint.com/data_structures_algorithms/stack_algorithm.htm)  
[Stack: Intro(簡介)](http://alrightchiu.github.io/SecondRound/stack-introjian-jie.html)  
[Implementing Stacks in Data Structures](https://www.simplilearn.com/tutorials/data-structure-tutorial/stacks-in-data-structures)  
[Slice Based Stack Implementation in Golang](https://rksurwase.medium.com/slice-based-stack-implementation-in-golang-8140603a1dc2)  
