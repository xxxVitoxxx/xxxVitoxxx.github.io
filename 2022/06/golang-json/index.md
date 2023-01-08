# 用 golang 處理 json 資料

Go 的標準函式庫可以很容易的對 JSON 資料進行編、解碼的工作。
<!--more-->

## json 資料轉換

### struct

在處理 `struct` 與 `json` 資料轉換時，只要 `struct` 內元素的名稱字首是大寫，就可以轉換成 `json`  

#### struct 轉換成 json

`json` 的 `key` 會跟 `struct` 元素名稱一樣  

```go
type Person struct {
    Name string
    Age  int 
}

func main() {
    p := Person{
        Name: "vito",
        Age:  11,
    }

    b, err := json.Marshal(p)
    if err != nil {
        fmt.Println("err: ", err)
    }
    fmt.Println(string(b))
    // output: {"Name":"vito","Age":11}
}
```

#### json 轉換成 struct

沒有加 `struct tag` 的情況下不管大小寫，只要字母是一樣即可將 `json` 轉換成 `struct`  

```go
type Person struct {
    Personname string
    Age        int
}

func main() {
    j := []byte(`{
        "personName":"vito",
        "age":11
    }`)

    p := Person{}
    if err := json.Unmarshal(j, &p); err != nil {
        fmt.Println("err: ", err)
    }
    fmt.Println(p)
    // output: {vito 11}
}
```  

### map

#### map 轉換成 json

```go
func main() {
    m := map[string]interface{}{
        "name": "vito",
        "age":  11,
    }
    b, err := json.Marshal(m)
    if err != nil {
        fmt.Println("err: ", err)
    }
    fmt.Println(string(b))
    // output: {"age":11,"name":"vito"}
}
```  

#### json 轉換成 map

```go
func main() {
    j := []byte(`{
        "name":"vito",
        "age":11
    }`)

    var m map[string]interface{}
    if err := json.Unmarshal(j, &m); err != nil {
        fmt.Println("err: ", err)
    }
    fmt.Println(m)
    // output: map[age:11 name:vito]
}
```  

## struct tag

可以看到上面 `struct` 轉成 `json` 的範例，轉換後的 `key` 都是大寫，如果今天你想要 `key` 是小寫的該怎麼辦呢？golang 提供了 `struct tag` 來實現  

```go
type Person struct {
    Name string `json:"name"`
    Age  int    `json:"age"`
}

func main() {
    p := Person{
        Name: "vito",
        Age:  11,
    }

    b, err := json.Marshal(p)
    if err != nil {
        fmt.Println("err: ", err)
    }
    fmt.Println(string(b))
    // output: {"name":"vito","age":11}
}
```  

也可以透過 `json tag` 定義 `key` 值  

```go
type Person struct {
    Name string `json:"person_name"`
    Age  int    `json:"person_age"`
}

func main() {
    p := Person{
        Name: "vito",
        Age:  11,
    }

    b, err := json.Marshal(p)
    if err != nil {
        fmt.Println("err: ", err)
    }
    fmt.Println(string(b))
    // output: {"person_name":"vito","person_age":11}
}
```

### omitempty

#### 忽略空值

當結構體元素是默認值時，想要忽略它，可以使用 `omitempty`  

```go
type Person struct {
    Name    string  `json:"name,omitempty"`
    Age     int     `json:"age,omitempty"`
    Address string  `json:"address,omitempty"`
}

func main() {
    p := Person{
        Name: "vito",
        Age:  11,
    }

    b, err := json.Marshal(p)
    if err != nil {
        fmt.Println("err: ", err)
    }
    fmt.Println(string(b))
    // {"name":"vito","age":11}
    // 當 Address 為默認值 "" 時，會忽略該元素
}
```  

#### 忽略 struct

當想忽略 `Person` 中的 `School` 時，則 `School` 必須是 `pointer`  

```go
type Person struct {
    Name    string  `json:"name,omitempty"`
    Age     int     `json:"age,omitempty"`
    Address string  `json:"address,omitempty"`
    // 若 School 不是 pointer ，就算加 omitempty 也無法忽略它
    School  *School `json:"school,omitempty"`
}

type School struct {
    Name    string `json:"name"`
    Address string `json:"address"`
}

func main() {
    p := Person{
        Name: "vito",
        Age:  11,
    }
    b, err := json.Marshal(p)
    if err != nil {
        fmt.Println("err: ", err)
    }
    fmt.Println(string(b))
    // {"name":"vito","age":11}
}
```

### string

#### 轉換成 json 時，將型態轉為字串

```go
type Person struct {
    Name string `json:"name"`
    Age  int    `json:"age,string"`
}

func main() {
    p := Person{
        Name: "vito",
        Age:  11,
    }
    b, err := json.Marshal(p)
    if err != nil {
        fmt.Println("err: ", err)
    }
    fmt.Println(string(b))
    // age 的值被轉為文字型態
    // output: {"name":"vito","age":"11"}
}
```

#### json 轉換 struct

將 `json` 格式的 `age` 的值（type: string） 轉換成 `struct` 時，原先 `age` 的型態會被轉成 `int`  

```go
type Person struct {
    Name string `json:"name"`
    Age  int    `json:"age,string"`
}

func main() {
    // 原始的 json 資料， age 的值是文字型態的 11
    j := []byte(`{
        "name":"vito",
        "age":"11"
    }`)

    var p Person
    if err := json.Unmarshal(j, &p); err != nil {
        fmt.Println("err: ", err)
    }
    fmt.Println(p)
    // output: {vito 11}
    fmt.Printf("%T\n", p.Age)
    // 轉換成 struct 後，原先文字型態的 11 變成 int 型態的 11
    // output: int
}
```  

當要轉換成 `struct` 時，如果型態有錯誤，會回傳錯誤訊息，但還是會成功轉換成 `struct` ，只是型態有問題的元素的值會是默認值，而不是你的資料  

```go
type Person struct {
    Name string `json:"name"`
    Age  int    `json:"age,string"`
}

func main() {
    j := []byte(`{
        "name":"vito",
        "age":"11.1"
    }`)

    var p Person
    if err := json.Unmarshal(j, &p); err != nil {
        // 預計 json age 的值會變轉成 int ，但 json age 的值轉換後是 float ，所以出現錯誤
        fmt.Println("err: ", err)
        // err:  json: cannot unmarshal number 11.1 into Go struct field Person.age of type int
    }
    fmt.Println(p)
    // output: {vito 0}
}
```  

### -

不管是否有資料想忽略元素可以用 `-`  

```go
type Person struct {
    Name string `json:"name"`
    Age  int    `json:"-"`
}

func main() {
    p := Person{
        Name: "vito",
        Age:  11,
    }

    b, err := json.Marshal(p)
    if err != nil {
        fmt.Println("err: ", err)
    }
    fmt.Println(string(b))
    // output: {"name":"vito"}
}
```  

若只是想讓 `json` 的 `key` 是 `-` 時，只需要再加上 `,`  

```go
type Person struct {
    Name string `json:"name"`
    Age  int    `json:"-,"`
}

func main() {
    p := Person{
        Name: "vito",
        Age:  11,
    }

    b, err := json.Marshal(p)
    if err != nil {
        fmt.Println("err: ", err)
    }
    fmt.Println(string(b))
    // output: {"name":"vito","-":11}
}
```  

