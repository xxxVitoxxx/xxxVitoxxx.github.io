# Floyd Cycle Detection Algorithm

Floyd Cycle Detection Algorithm 或 Hare-Tortoise Algorithm 是一種指針演算法。它使用兩個 pointer，以不同的速度移動，速度較快的稱為 fast ，速度較慢的稱為 slow ，所以又稱為 Fast-Slow Pointers 。
<!--more-->

## Introduction

兩個 pointer 分別為 fast 和 slow 沿著 Linked List 遍歷，fast 移動的速度會比 slow 快。一般情況下， slow 每次會往前一步，而 fast 往前的步數會是 slow 的兩倍。

關鍵思想是 fast 和 slow 會從同一個位置以不同速度遍歷 Linked List ，如果存在 cycle ，兩者必然會在 cycle 中的某個點相遇。就像有兩位跑者在跑道上，雖然起跑點是一樣的，但由於某一方速度較快，且如果跑道是圓的情況下，那速度較快的跑者勢必會在某個點遇到跑得慢的跑者。另一方面如果跑道是直的，那跑的快的跑者會優先跑到終點，且過程中不會與速度較慢的跑者相遇。

{{< image width="100%" src="https://imgur.com/EojcfDc.png" >}}

{{< image width="100%" src="https://imgur.com/nKJfVgR.png"  >}}

{{< image width="100%" src="https://imgur.com/nxk6n0B.png"  >}}

{{< image width="100%" src="https://imgur.com/SMrGymc.png"  >}}

{{< image width="100%" src="https://imgur.com/FuEpzFx.png"  >}}

## Use case

使用 Fast-Slow Pointers 可以解決以下幾種問題：
- 判斷 Linked List 有沒有 cycle
- 找到 Linked List 的中心點
- 計算 cycle 的長度
- 找到 cycle 的起始點

### 判斷 Linked List 有沒有 cycle

如果 Linked List 有 cycle 的情況下 fast 與 slow 一定會相遇，而沒有 cycle 的情況下兩者不會相遇。

```go
type ListNode struct {
    Val int
    Next *ListNode
}

func hasCycle(head *ListNode) bool {
    fast, slow := head, head
    for fast != nil && fast.Next != nil {
        fast = fast.Next.Next
        slow = slow.Next
        if fast == slow {
            return true
        }
    }
    return false
}
```

### 找到 Linked List 的中心點

因為 fast 移動的距離是 slow 的兩倍，所以當 fast 到終點時 slow 會在 Linked List 的中心點。

```go
func findCenterPointer(head *ListNode) *ListNode {
    fast, slow := head, head
    for fast != nil && fast.Next != nil {
        fast = fast.Next.Next
        slow = slow.Next
    }
    return slow
}
```

### Cycle 的長度

當 fast 與 slow 相遇時，那相遇的點一定會在 cycle 中，這時 slow 繼續前走並計算走的步數。當 slow 再次與 fast 相遇時，走的步數就會是 cycle 的長度。 

```go
func cycleLength(head *ListNode) int {
    fast, slow := head, head
    for fast != nil && fast.Next != nil {
        fast = fast.Next.Next
        slow = slow.Next
        if fast == slow {
            var l int
            for slow != nil {
                l++
                slow = slow.Next
                if slow == fast {
                    return l
                }
            }
        }
    }
    return 0
}
```

### 找到 cycle 的起始點

假設有一個 cycle 的 Linked List ，由 head 到 cycle 起始點的長度為 X ，而 cycle 的長度為 Y ，可以得知整個 Linked List 的長度為 X + Y 。

在已知 cycle 長度的情況下， 假設 pointer 1 和 pointer 2 都在 head 上，先讓 pointer 1 移動 Y 步。我們觀察一下 pointer 1 和 pointer 2 的位置， pointer 1 已經走了 Y 步，如果再走 X 步剛好遍歷過整個 Linked List 回到 cycle 的起始點，而 pointer 2 還在 head 上，所以走 X 步就會到 cycle 的起始點。

我們已經知道 pointer 1 和 pointer 2 雙方只要走 X 步就可以到 cycle 的起始點並相遇，換句話說，只要讓 pointer 1 和 pointer 2 以等速移動，那雙方相遇的點就是 cycle 的起始點。

整理一下步驟：  
step1: 利用 Fast-Slow Pointers 找出在 cycle 相遇的點  
step2: 計算 cycle 的長度為 L  
step3: 一個 pointer 先從 head 往前走 L 步，另一個 pointer 在 head ，接著兩個 pointer 等速移動直到相遇  

```go
func detectCycle(head *ListNode) *ListNode {
    fast, slow := head, head
    for fast != nil && fast.Next != nil {
        fast = fast.Next.Next
        slow = slow.Next
        if fast == slow {
            l := cycleLen(slow)
            pnt1, pnt2 := head, head
            for l > 0 {
                pnt1 = pnt1.Next
                l--
            }

            for pnt1 != pnt2 {
                pnt1 = pnt1.Next
                pnt2 = pnt2.Next
            }
            return pnt1
        }
    }
    return nil
}

func cycleLen(node *ListNode) int {
    var l int
    curry := node
    for curry != nil {
        l++
        curry = curry.Next
        if curry == node {
            return l
        }
    }
    return 0
}
```  

前面的方法在找到相遇的點後還需要額外執行 for 迴圈來尋找 cycle 長度。接下來我們用另一種與前面類似的方法但不需要尋找 cycle 長度。  

{{< image width="100%" src="https://imgur.com/gxICZFN.png" >}}  

在有 cycle 的情況下，假設 A 點為 Linked List 的 head ， B 點為 cycle 的起始點， C 點為 fast pointer 和 slow pointer 相遇的點。 X 為 A 點到 B 點的距離， Y 為 B 點到 C 點的距離， Z 為 C 點到 B 點的距離。  

會發現當 slow pointer 走了 X + Y ， fast pointer 走了 X + Y + n * (Y + Z) *( n 為循環的次數)*，在 fast pointer 移動的速度是 slow pointer 的 2 倍時可以得到 2 * (X + Y) = X + Y + n * (Y + Z) ，簡化後為 X + Y = n * (Y + Z)。因為我們想知道 B 點的位置，所以我們要得到 X 的距離，求 X 的方程式為 X = (n - 1) * Y + n * Z 。  

現在我們知道如何得到 X 的長度了，假設 n = 1 ，會得到 X = Z ，這代表如果我在 A 點和 C 點各放一個 pointer ，雙方以相同的速度前進，雙方會在 B 點相遇，也就是 cycle 的起始點。  

整理一下步驟：  
step1: 利用 Fast-Slow Pointers 找出在 cycle 相遇的點  
step2: 將 fast 指回 head 的位置  
step3: fast 和 slow 等速往前走直到相遇  

```go
func detectCycle(head *ListNode) *ListNode {
    fast, slow := head, head
    for fast != nil && fast.Next != nil {
        fast = fast.Next.Next
        slow = slow.Next
        if fast == slow {
            fast = head
            for fast != slow {
                fast = fast.Next
                slow = slow.Next
            }
            return slow
        }
    }
    return nil
}
```  

## Related topics in leetcode

- [leetcode 141](https://github.com/xxxVitoxxx/leetcode/blob/main/141.linked_list_cycle/main.go)
- [leetcode 142](https://github.com/xxxVitoxxx/leetcode/blob/main/142.linked_list_cycle_II/main.go)
- [leetcode 143](https://github.com/xxxVitoxxx/leetcode/blob/main/143.recorder_list/main.go)
- [leetcode 202](https://github.com/xxxVitoxxx/leetcode/blob/main/202.happy_number/main.go)
- [leetcode 234](https://github.com/xxxVitoxxx/leetcode/blob/main/234.palindrome_linked_list/main.go)
- [leetcode 457](https://github.com/xxxVitoxxx/leetcode/blob/main/457.circular_array_loop/main.go)
- [leetcode 876](https://github.com/xxxVitoxxx/leetcode/blob/main/876.middel_of_the_linked_list/main.go)
## Reference

- [Fast and Slow Pointers: Introduction](https://www.educative.io/courses/grokking-coding-interview-patterns-python/fast-and-slow-pointers-introduction)
- [快慢指針（Fast-slow Pointers）](https://hackmd.io/@Hsins/fast-slow-pointers)
- [探索 Floyd Cycle Detection Algorithm](https://medium.com/@orionssl/%E6%8E%A2%E7%B4%A2-floyd-cycle-detection-algorithm-934cdd05beb9)
- [leetcode算法汇总 （三）快慢指针](https://zhuanlan.zhihu.com/p/72886883)

