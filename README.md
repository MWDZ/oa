# Meta OA

## Resource 1
Refer [Link](https://www.1point3acres.com/bbs/thread-875779-1-1.html)

### Question 1
Your task is to implement a simulation of a `change directory` command. This command changes the current working directory to the specified one.
The initial working directory is root i.e. `/` . You are given a list of `cd` commands `commands`.

There are multiple options for command arguments.
- `cd /` - changes the working directory to the root directory.
- `cd .` - stays in the current directory.
- `cd ..` - moves the working directory one level up. In the root directory, cd.. does nothing.
- `cd < subdirectory>` - moves to the specified subdirectory within the current working directory.

All specified directories exist. Return the absolute path from the root to the working directory after executing all ed commands in the given order. / should be used as separators.

#### Example:
- For 
  ```
    commands = [
      "cd users",
      "ed codesignal",
      "cd ..",
      "cd admin",
    ]
  ```
  the output should be solution (commands) = `"/users/admin"` .

#### Solution:
```python

def solution(commands):
  # 初始化路径栈
  path_stack = []
  
  # 遍历每个命令
  for command in commands:
      if command == "cd /":
          # 切换到根目录，清空栈
          path_stack = []
      elif command == "cd .":
          # 当前目录，不做任何操作
          continue
      elif command == "cd ..":
          # 返回上一级目录，弹出栈顶元素
          if path_stack:
              path_stack.pop()
      else:
          # cd <subdirectory>，提取子目录并压入栈
          subdirectory = command.split()[1]
          path_stack.append(subdirectory)
  
  # 构造最终的路径
  return "/" + "/".join(path_stack)

# 示例测试
commands = [
    "cd users",
    "cd codesignal",
    "cd ..",
    "cd admin",
]
print(solution(commands))  # 输出应为 "/users/admin"
```

### Question 2
Given a rectangular `matrix` and an integer `frameSize`, consider the outer frames of all `frameSize × frameSize` contiguous square submatrices of `matrix`.

Your task is the following:
- Calculate the sum of all numbers located on the frame of each frameSize × frameSize submatrix.
- Determine the maximum of all these sums.
- Find all the distinct numbers that appear in at least one of the frames of frameSize x framesize submatrices with a sum equal to the maximum. Each integer from these square frames should be calculated exactly once. Return the sum of these distinct numbers.

Note: A `frameSize x frameSize` square frame contains `max (1, 4 * (frameSize - 1))` cells. See example for better understanding.
#### Example

- For
  ```
  matrix = [[9, 7, 8, 9, 2],
            [6, 9, 9, 6, 1],
            [4, 10, 1, 3, 10],
            [18, 2, 3, 9, 3],
            [4, 6, 8, 5, 21]]
  ```
  and `frameSize = 3`, the output should be `solution(maxtrix, frameSize) = 92`

#### Solution
```python
def solution(matrix, frameSize):
    rows = len(matrix)
    cols = len(matrix[0])
    
    # 创建前缀和矩阵
    prefixSum = [[0] * (cols + 1) for _ in range(rows + 1)]
    
    # 计算前缀和
    for i in range(1, rows + 1):
        for j in range(1, cols + 1):
            prefixSum[i][j] = matrix[i-1][j-1] + prefixSum[i-1][j] + prefixSum[i][j-1] - prefixSum[i-1][j-1]
    
    max_sum = float('-inf')  # 用于存储最大框架和
    frame_elements = set()   # 用于存储最大框架和的框中的独特元素

    # 使用前缀和计算子矩阵的外框和
    def get_frame_sum_and_elements(i, j):
        elements = set()
        frame_sum = 0

        # 计算上边框和下边框的和以及对应的元素
        for col in range(j, j + frameSize):
            # 上边框
            frame_sum += matrix[i][col]
            elements.add(matrix[i][col])
            # 下边框
            frame_sum += matrix[i + frameSize - 1][col]
            elements.add(matrix[i + frameSize - 1][col])
        
        # 计算左边框和右边框的和（去掉上下边界的重叠部分）
        for row in range(i + 1, i + frameSize - 1):
            # 左边框
            frame_sum += matrix[row][j]
            elements.add(matrix[row][j])
            # 右边框
            frame_sum += matrix[row][j + frameSize - 1]
            elements.add(matrix[row][j + frameSize - 1])

        return frame_sum, elements
    
    # 遍历所有可能的 frameSize × frameSize 的子矩阵
    for i in range(rows - frameSize + 1):
        for j in range(cols - frameSize + 1):
            frame_sum, elements = get_frame_sum_and_elements(i, j)
            
            if frame_sum > max_sum:
                max_sum = frame_sum
                frame_elements = elements
            elif frame_sum == max_sum:
                frame_elements.update(elements)
    
    # 返回去重后的元素和
    return sum(frame_elements)

# 示例测试
matrix = [
    [9, 7, 8, 9, 2],
    [6, 9, 9, 6, 1],
    [4, 10, 1, 3, 10],
    [18, 2, 3, 9, 3],
    [4, 6, 8, 5, 21]
]
frameSize = 3
print(solution(matrix, frameSize))  # 输出应为 92
```

### Question 3
You are given `n`, an integer representing the length of a binary string `a`, which is all `'O'`s in the beginning.

You are also given `operations`, an array of strings, each representing an operation of one of these two types:

- `"L"` - find the smallest index `i` , for which `a[i] = '0'` , and set `a[i] = '1'`. If there is no such index, do nothing.

- `"C{ind}"` - set `a[ind] = '0'`. This operation does not depend on the previous value of `a[ind]`. It is guaranteed that `ind` is a valid 0-based index of `a` (ie: ind < n ).

Given `n` and `operations`, your task is to return `a`, the binary string after all operations have been applied.



#### Example
- For n = 10 and operations = ["L", "I", "CO", "I", "C3"], the output should be solution(n, operations) = "1100000000" .

#### Solution
```python
def solution(n, operations):
    # 初始化一个全为 '0' 的字符串列表
    a = ['0'] * n
    
    # 遍历每一个操作
    for op in operations:
        if op == "L":
            # 查找第一个 '0' 的位置并设置为 '1'
            for i in range(n):
                if a[i] == '0':
                    a[i] = '1'
                    break
        elif op.startswith("C"):
            # 提取操作中的索引并将其设置为 '0'
            index = int(op[1:])  # 提取 "C{ind}" 中的索引值
            a[index] = '0'
    
    # 返回最终的字符串
    return ''.join(a)

# 示例测试
n = 10
operations = ["L", "L", "C0", "L", "C3"]
print(solution(n, operations))  # 输出应为 "1100000000"

```


### Question 4
Imagine a robot walking along a number line in which each whole number represents a coordinate. The robot starts at coordinate `0` and walks according to the given `commands` - each command prompts the robot to either move one coordinate to the left (L) or one coordinate to the right (R).

After the robot has successfully executed all `commands`, to optimize future walks, your task is to figure out which direction the robot has stopped on relative to its starting coordinate of `0`:

- If the robot stops on a negative coordinate, return `"L"`;
- If the robot stops on `0`, return an empty string;
- If the robot stops on a positive coordinate, return `"R"` .

#### Example
- For `commands = "RLLRLL"`, the output should be `solution (commands) = "L"`.
  - After the first two commands, the robot will return to the initial coordinate `0`.
  - After the third and fourth commands, the robot will also return to the initial coordinate `0`.
  - After the last two commands, the robot will stop on coordinate `-2`.
  - `2 < 0` so `"L"` should be returned.

- For `commands = "LLRLLLRRRR"`, the output should be `solution(commands) = ""`
#### Solution
```python
def solution(commands):
    # 初始化机器人位置为 0
    position = 0
    
    # 遍历每个指令
    for command in commands:
        if command == "L":
            position -= 1
        elif command == "R":
            position += 1
    
    # 判断最终的位置
    if position < 0:
        return "L"
    elif position > 0:
        return "R"
    else:
        return ""

# 示例测试
commands = "RLLRLL"
print(solution(commands))  # 输出应为 "L"

commands = "LLRLLLRRRR"
print(solution(commands))  # 输出应为 ""

```

## Resource 2
Refer [link](https://www.1point3acres.com/bbs/thread-1043993-1-1.html)

### Question 5
Given an array of strings `schedule` that represents the schedule of bus departure times and a string time that represents the current time, find out how many minutes ago the last bus left. If the first bus for the day has yet to
leave, return `-1`.

Time is represented as a string in the form of `HH:MM` (in the 24-hour format). Bus departure times are sorted in chronological order.

Please assume that if a bus is scheduled to leave at the current time, it hasn't left yet.

Note: You are not expected to provide the most optimal solution, but a solution with time complexity not worse than `O(schedule.length * MINUTES_IN_DAY)` will fit within the execution time limit.

#### Example
- For `schedule = ["12:30", "14:00", "19:55"]` and time = `"14:30"`, the output should be `solution(schedule, time) = 30`.

  The current time is `"14:30"`. The last bus left at `"14:00"`, so the answer is `30` minutes ago.

- For `schedule = ["00:00", "14:00", "19:55"]` and time = `"00:00"`, the output should be `solution(schedule, time) = -1`.
    
  No buses left before `"00:00"` (the bus scheduled to leave at `"00:00"` hasn't left yet), so the answer is `-1`.

- For `schedule = ["12:30", "14:00", "19:55"]` and time = `"14:00"`, the output should be `solution(schedule, time) = 90`.

  The current time is `"14:00"` . The last bus left at `"12:30"` (the bus scheduled to leave at `"14:00"` hasn't left yet), so the answer is `90` minutes age.

#### Solution
```python
def time_to_minutes(time: str) -> int:
    hours, minutes = map(int, time.split(":"))
    return hours * 60 + minutes

def solution(schedule, time):
    current_time = time_to_minutes(time)
    last_departure_time = -1
    
    # 遍历所有的出发时间，找到最晚的出发时间且早于当前时间
    for departure in schedule:
        departure_time = time_to_minutes(departure)
        if departure_time < current_time:
            last_departure_time = departure_time
        else:
            break  # 因为时间是有序的，所以可以提前结束遍历
    
    # 如果没有找到早于当前时间的公交，返回 -1
    if last_departure_time == -1:
        return -1
    
    # 计算与当前时间的分钟差
    return current_time - last_departure_time


# 示例测试
schedule = ["12:30", "14:00", "19:55"]
time = "14:30"
print(solution(schedule, time)) # 30

schedule = ["00:00", "14:00", "19:55"]
time = "00:00"
print(solution(schedule, time)) # -1

schedule = ["12:30", "14:00", "19:55"]
time = "14:00"
print(solution(schedule, time)) # 90
```

### Question 6
You are given an array of integers `numbers` and a positive integer `k`. Your task is to count the number of contiguous subarrays within `numbers` that contains at least k pairs of elements with duplicate values.

More formally, count the number of contiguous subarrays
`numbers[i..j] (i ≤ j )` for which there are `2 * k` elements (with pairwise distinct indices `i ≤ i_1, j_1, i_2, j_2, ..., i_k, j_k ≤ j`)  with each pair having the
same value - `numbers[i_1] = numbers[j_1], numbers[i_2] = numbers [j_2], ..., numbers[j_k] = numbers [j_k]`.

#### Example
- For `numbers = [0, 1, 0, 1, 0]` and `k = 2`, the output should be `solution(numbers, k) = 3`.

  There are `3` subarrays that satisfy the criteria of containing at least `k = 2` pairs of duplicate values:
  
  - `numbers[0 .. 3] = [0, 1, 0, 1]` with `numbers[0] = 0 = numbers[2]` and `numbers[1] = 1 = numbers[3]`
  - `numbers[1 .. 4] = [1, 0, 1, 0]`with `numbers[1] = 1 = numbers[3]` and `numbers[2] = 0 = numbers[4]`
  - `numbers[0..4] = [0, 1, 0, 1, 0]` with `numbers[0] = 0 = numbers [2]`, `numbers[1] = 1 = numbers[3]`, and `numbers[2] = 0 = numbers[4]`

  In each of these subarrays, there is at least one pair of `0`s and one pair of `1`s.

- For `numbers = [2, 2, 2, 2, 2, 2]` and `k = 3`, the output should be `solution(numbers, k) = 1`.

#### Solution
```python
#无解法
```

### Question 7
You are given a `matrix` of characters and an array of distinct strings `words`. Your task is to implement a simplified string search by determining the number of times in which strings from `words` can be found in the matrix under the following constraints:

- A string is found when it can be formed by combining characters along some path in the matrix.
- A path may start at any cell, and initially must go from left to right or from top to bottom.
- All paths are allowed to change direction once to go in the opposite direction (i.e., from left -> right to right -> left, or from top -> bottom to bottom -> top).

Review the examples below for details.

Note: You are not expected to provide the most optimal solution, but a solution with time complexity not worse than `O(matrix.length * matrix[0].length * words.length * max(words[i]. length))` will fit within the execution time limit.

#### Example
- For
  ```
    matrix = [["a", "b", "a", "c"],
              ["x", "a", "c", "d"],
              ["y", "r", "d", "s"]]
  ```
  and `words = ["ac", "cat", "car", "bar", "acdc", "abacaba"]`, the output should be `solution(matrix, words) = 7`.

  - `ac` has `3`, 
    
    Path: 
    [[0, 2], [0, 3]]
    [[0, 2], [1, 2]]
    [[1, 1], [1, 2]]

  - `cat` has `0`,
    No 't' in matrix
  - `car` has `0`,
    No 'r' in matrix
  - `bar` has `1`,
    Path: [[0, 1], [1, 1], [2, 1]]
  - `acdc` has `2`,
    Path: 
    [[0, 2], [1, 2], [2, 2], [1, 2]] (change direction)
    [[1, 1], [1, 2], [1, 3], [1, 2]] (change direction)
  - `abacaba` has `1`,
    Path:
    [[0, 0], [0, 1], [0, 2], [0, 3], [0, 2]] (change direction)

  In all, there are 7 paths in total.

  #### Solution
  ```python
  def simplify_string(word):
    n = len(word)
    min_i = n - 1
    # 判断一个字符串是否是回文
    def is_palindrome(s):
        return s == s[::-1]
    for i in range(n-2, -1, -1):  # 从右往左检查
        if is_palindrome(word[i:]):
            min_i = i  
    prefix = word[:min_i]
    pali = word[min_i:]
    pali_n = len(pali)
    return prefix + pali[:pali_n // 2 + 1]

  def solution(matrix, words):
      rows, cols = len(matrix), len(matrix[0])
      
      def search_horizontal(row, col, word):
          # Search left to right, then switch to right to left
          word_len = len(word)
          if col + word_len <= cols:
              # Search from left to right
              if all(matrix[row][col + i] == word[i] for i in range(word_len)):
                  return 1
          if col - word_len + 1 >= 0:
              # Search from right to left
              if all(matrix[row][col - i] == word[i] for i in range(word_len)):
                  return 1
          return 0

      def search_vertical(row, col, word):
          # Search top to bottom, then switch to bottom to top
          word_len = len(word)
          if row + word_len <= rows:
              # Search from top to bottom
              if all(matrix[row + i][col] == word[i] for i in range(word_len)):
                  return 1
          if row - word_len + 1 >= 0:
              # Search from bottom to top
              if all(matrix[row - i][col] == word[i] for i in range(word_len)):
                  return 1
          return 0
      
      def find_word(word):
          count = 0
          for row in range(rows):
              for col in range(cols):
                  if matrix[row][col] == word[0]:
                      count += search_horizontal(row, col, word)
                      count += search_vertical(row, col, word)
          return count
      
      total_count = 0
      for word in words:
          total_count += find_word(simplify_string(word))
      
      return total_count

  matrix = [["a", "b", "a", "c"],
            ["x", "a", "c", "d"],
            ["y", "r", "d", "s"]]
  words = ["ac", "cat", "car", "bar", "acdc", "abacaba"]


  print(solution(matrix, words))
  ```


## Resource 3 - Latest
Refer [link](https://www.xiaohongshu.com/explore/66f843b3000000001b023ac2?xsec_token=ABa8tlWUvZE5F0BMvqPehj3z5DX2sj-m-D4Qtesk-Adfs=&xsec_source=pc_search&source=web_explore_feed)

### Question 8
Given a range of numbers `[left, right]`, both ends inclusive, where `100 ≤ left ≤  right ≤ 999`, count the number of three-digit numbers having all three digits pairwise distinct (meaning that no two digits are the same). It is guaranteed that all numbers within a range have exactly three digits.

Note: You are not expected to provide the most optimal solution, but a solution with time complexity not worse than O(right^2) will fit within the execution time limit.

#### Example
- For `left = 876` and `right = 890`, the output should be `solution(left, right) = 3`.
  
  The numbers `876`, `879`, `890` are the only numbers in the range that have all three digits pairwise distinct.

#### Input/Output
- [execution time limit] 3 seconds (java)
- [memory limit] 1 GB
- [input] integer left
  An integer representing the lower bound of the given number range.
  Guaranteed constraints: `100 ≤ left ≤ right ≤ 999`.

  ```python
  def is_distinct(n):
      # 判断三位数的百位、十位、个位是否各不相同
      digits = list(str(n))
      return len(digits) == len(set(digits))

  def solution(left, right):
      count = 0
      # 遍历区间 [left, right] 中的数字
      for num in range(left, right + 1):
          # 只处理三位数，且百位、十位和个位数字不同
          if is_distinct(num):
              count += 1
      return count
  print(solution(876, 890))  # 输出: 3
  ```

### Question 9
Given an array of integers numbers and an array `pattern` representing a comparison `pattern`, find how many subarrays of `numbers` match the given pattern.
`pattern` can only contain the following integers:

- `pattern[i] = 1`, represents that the number corresponding to this element of the pattern is greater than the previous one.
- `pattern[i] = 0`, represents that the number corresponding to this element of the pattern is equal to the previous one.
- `pattern[i] = -1`, represents that the number corresponding to this element of the pattern is less than the previous one.

  It is guaranteed that the `numbers.length > pattern.length`.

Note: You are not expected to provide the most optimal solution, but a solution with
time complexity not worse than `O(numbers.length x pattern.length)` will fit within the execution time limit.

#### Example
- For numbers = [4, 1, 3, 4, 4, 5, 5, 1] and pattern = [1, 0, -1], the output should be `solution(numbers, pattern) = 1`.

  Explanation:

  Let's check all possible subarrays of length `3`. Note, that the subarray `[4, 1, 3]`, starting with `numbers[0] = 4` does not need to be checked, as there is
  nothing to compare the first element with.
  - Subarray `[1, 3, 4]` doesn't satisfy the pattern. pattern[0] = 1 means ther the first element of the subarray should be greater than the

#### Solution
```python
def solution(numbers, pattern):
    count = 0
    n = len(numbers)
    m = len(pattern)
    
    # 遍历每一个子数组
    for i in range(n - m):
        match = True
        
        # 检查子数组是否符合pattern
        for j in range(m):
            if pattern[j] == 1 and numbers[i + j + 1] <= numbers[i + j]:
                match = False
                break
            if pattern[j] == 0 and numbers[i + j + 1] != numbers[i + j]:
                match = False
                break
            if pattern[j] == -1 and numbers[i + j + 1] >= numbers[i + j]:
                match = False
                break
        
        # 如果符合条件，计数加1
        if match:
            count += 1
    
    return count

numbers = [4, 1, 3, 4, 4, 5, 5, 1]
pattern = [1, 0, -1]

print(solution(numbers, pattern))
```

### Question 10
Imagine you are playing a gravity-based puzzle game that involves clearing obstacles to allow an irregularly-shaped figure to fall to the bottom.

You are given a rectangular matrix `board` representing the game board, which only contains the following types of cells:

- `'-'` represents an empty cell
- `'#'` represents an obstacle
- `'*'` represents part of the figure

It is guaranteed that the figure consists of one piece, where all parts are connected by the sides.

Your task is to simulate how the figure should fall, and find the minimum number of obstacles that should be removed to let the figure finally touch the bottom of the board with at least one of its cells.

Note: You are not expected to provide the most optimal solution, but a solution with time complexity not worse than `O(board.length * board[0].length)` will fit within the execution time limit.

#### Example
- For
  ```
  board = [['*', '*', '*'],
           ['#', '*', '*'],
           ['-', '-', '-'],
           ['-', '#', '#']]
  
  ```
  the output should be `solution(board) = 2`.