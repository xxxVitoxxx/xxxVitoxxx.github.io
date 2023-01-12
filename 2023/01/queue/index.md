# Queue

queue 是一種兩端開放的線性數據結構，在前端取出元素，在尾端新增元素，類似排隊購票，排在第一位的，可以最先購票。  
<!--more-->

遵循先進先出（FILO, First-in-First-out）的特性，通常被使用在管理多線程和實現優先級排隊系統。  

![queue](https://imgur.com/1mT9Z4p.png)  

## Queue 的基本操作

基本操作：  

- Enqueue: 新增元素到 queue 尾端  
- Dequeue: 移除 queue 前端元素  
- Peek: 取得 queue 前端元素  

輔助操作：

- IsEmpty: 檢查 queue 是否為空  
- IsFull: 檢查 queue 是否被填滿  

## Implement of the stack in golang

queue 可以使用 array 和 linked List 實作，這邊使用 golang 的 slice 實作有容量的 Queue ，完整內容可以參考[這裡](https://github.com/xxxVitoxxx/data-structure-and-algorithm/blob/main/data-structure/queue/slice.go)， 想參考 linked list 實作可以參考[這邊](https://github.com/xxxVitoxxx/data-structure-and-algorithm/blob/main/data-structure/queue/linked_list.go)。  

### Declaring stack

```go
type Queue struct {
    queue []int
    capacity int
}

func NewQueue(cap int) *Queue {
    return &Queue{capacity: cap}
}
```  

Queue 結構有兩個屬性：  

1. queue: int 屬性的 slice  
2. capacity: queue 的大小  

### Implementing a IsEmpty operation

```go
func (q *Queue) IsEmpty() bool {
    return len(q.queue) == 0
}
```  

### Implementing a IsFull operation

```go
func (q *Queue) IsFull() bool {
    return len(q.queue) == q.capacity
}
```  

### Implementing a Enqueue operation

```go
func (q *Queue) Enqueue(element int) error {
    if q.IsFull() {
        return errors.New("queue overflow")
    }

    q.queue = append(q.queue, element)
    return nil
}
```  

### Implementing a Dequeue operation

```go
func (q *Queue) Dequeue() error {
    if q.IsEmpty() {
        return errors.New("queue underflow")
    }

    q.queue = q.queue[1:]
    return nil
}
```  

### Implementing a Peek operation

```go
func (q *Queue) Peek() (int, error) {
    if len(q.queue) == 0 {
        return 0, errors.New("empty queue")
    }
    return q.queue[0], nil
}
```  

## Reference

- [Queue Data Structure](https://www.programiz.com/dsa/queue)  
- [Queue Data Structure: Types, Implementation, Applications
](https://www.naukri.com/learning/articles/queue-data-structure-types-implementation-applications/.png)  
