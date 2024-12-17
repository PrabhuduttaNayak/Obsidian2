[[CSES]]
A number spiral is an infinite grid whose upper-left square has number 1. Here are the first five layers of the spiral:
![[Pasted image 20241217163835.png]]
Your task is to find out the number in row yyy and column xxx.

# Input

The first input line contains an integer ttt: the number of tests.

After this, there are ttt lines, each containing integers yyy and xxx.

# Output

For each test, print the number in row yyy and column xxx.

# Constraints

- 1≤t≤1051 \le t \le 10^51≤t≤105
- 1≤y,x≤1091 \le y,x \le 10^91≤y,x≤109

# Example

Input:

3
2 3
1 1
4 2

Output:

8
1
15

```cpp
        int x,y;
        cin >> x >> y;
        if (x < y)
        {            if (y % 2 == 1)
                {
                    int r = y * y;
                    cout << r - x + 1 << endl;
                }
            else                {
                    int r = (y - 1) * (y - 1) + 1;
                    cout << r + x - 1 << endl;                }        }
        else        {
            if (x % 2 == 0)                {
                    int r = x * x;
                    cout << r - y + 1 << endl;                }
                else                {
                    int r = (x - 1) * (x - 1) + 1;
                    cout << r + y - 1 << endl;                }        }
    }
}
```