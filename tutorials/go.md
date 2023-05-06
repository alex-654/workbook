### variables
```go
var num int = 11  // long
num := 22 // short

var (
  a string // ""
  b bool // false
  c int // 0
)
```

### function
```go
// public function
func Multiply(x int, y int) int {
    return x * y
}

// private function
func divide(x int, y int) int {
    return x / y
}

// multiple return
func divide(x, y int) (int, error) {
    if y == 0 {
        return 0, errors.New("cannot divide on zero")
    }

    return x / y, nil
}
```

### base  scalar types
int, int64, float64


### convert int to string
```go
s := strconv.Itoa(-42) // "-42"
```

### loop 
```go

for i := 0; i < 10; i++ { //base
    nums = append(nums, i)
}

for i < 10 { //while
    nums = append(nums, i)
    i++
}

for {
    if i == 10 {
        break
    }

    nums = append(nums, i)
    i++
}

for i, name := range names {
    fmt.Println("Hello ", name, " at index ", i)
}
```

### switch
```go
switch x {
    default: // default always evaluates last
        fmt.Println("default case")
    case 10:
        fmt.Println("case 10")
}

switch { 
    default:
        fmt.Println("default case")
    case x == 10:
        fmt.Println("equal 10 case")
        fallthrough
    case x <= 10:
        fmt.Println("less or equal 10 case")
}
// output
// equal 10 case
// less or equal 10 case
```

### strings
```go
"hello " + "world" // "hello world"

username := "Ivan"
greetings := fmt.Sprintf("hello, %s", username) // "hello, Ivan"

len("go") // 2
len("го") // 4 return bytes
```

### sort example
```go
sort.Slice(nums, func(i, j int) bool {
    return nums[i] < nums[j]
})
```
### slice
```go
s := []int{}
s := []int{1, 2} 
```

### map
```go
// empty map long
var m map[int]string

// empty map short
m := map[int]string{}

// recommended way of map creation
m := make(map[int]string, 10)

// create map with values
m := map[int]string{1: "hello", 2: "world"}
```