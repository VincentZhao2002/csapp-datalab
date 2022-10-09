# csapp-datalab
## This is my implementation code of datalab, the foundation of computer systems in the Fall semester of 2022 at Renmin University of China

## bitXor
```csharp
int bitXor(int x, int y) 
{
  return (~((~x)&(~y)))&~(x&y);
}
```
实现思路：x&y筛选出都为1的位，~x&~y筛选出全为0的位，取反后相交则将既不全为0也不全为1的位置1

## thirdBits
```csharp
int thirdBits(void) 
{
  int temp = 73;
  temp = (temp << 9)|temp;
  return (temp << 15)|temp;
}
```

实现思路：十进制73转化为二进制表示为1001001，左移9位再与原数取并则得到1001001001001001,再左移15位与temp取并则得到题目所要求的数

## fitsShort
```csharp
int fitsShort(int x) 
{
  x = x >> 15;
  return !(x^(x >> 1));
}
```

实现思路：要保证能用short型变量表示，则第15~31位应均为0或均为1（第15位用于表示符号位）。将x右移15位后，此时应判定x是否为全0或全1，采用x与x向右移1位后异或来实现。若x全0或全1则异或后变为0，取非后变为1。其他情况为0。

## isTmax
```csharp
int isTmax(int x) 
{
  int temp = ~x;
  int judge = !temp;
  int result = temp + temp;
  return !(result|judge);
}
```

实现思路：首先将x取反便于后续操作，定义judge变量排除0这一特殊情况。定义result为两个temp相加，在排除0的情况下，当x为Tmax时，~x为0x80000000，相加后溢出为0。其他情况result都不为0，最后返回处用judge排除0的情况即可。

## fitsBits
```csharp
int fitsBits(int x, int n) 
{
  x = x >> n + 31;
  return !(x ^ x >> 1);
}
```

实现思路：和之前的fitsShort基本一致，判定第n-1至第31位全为0或1即可。

## upperBits
```csharp
int upperBits(int n) 
{
  int temp = !!n;
  return (temp << 31) >> n + 31;
}
```

实现思路：首先定义temp = !!n，当n为0时temp为0，当n为非零时，temp为1，用以排除特殊情况。然后将temp向左移31位，再向右移n + 31位，实际上即为n - 1位，因为此时第31位已经为0或1。右移过后即可保证高n位为1。

## anyOddBit
```csharp
int anyOddBit(int x) 
{
  int temp = 0xAA;
  temp = (temp << 8) | temp;
  temp = (temp << 16) | temp;
    return !!(x & temp);
}
```

实现思路：函数前三行构建一个奇数位全为1的常数。x & temp过后若x奇数位有1则为非零，否则为0，两次取非过后达到非0，即含有奇数位1，返回1，否则返回0。

## byteSwap
```csharp
int byteSwap(int x, int n, int m) 
{
  n =  n << 3;
  m =  m << 3;
  int i = 0xff;
  int temp = ((x >> m) ^ (x >> n)) & i;
  temp = (temp << m) | (temp << n);
  return (x ^ temp);
}
```

实现思路：首先将m和n均向左移3位，实现byte和bit之间的进制转换，定义常量i = 0xff用以排除后续高位影响。函数第四行定义常量保存有变量x第m个字节和第n个字节的信息，然后将该信息分别移至第m个字节和第n个字节，利用经典的异或交换算法完成交换，该算法思想可简单表示为a ^ (a ^ b) = (a ^ a) ^ b = 0 ^ b = b。

## absVal
```csharp
int absVal(int x) 
{
  int sign = x >> 31;
  return (x + sign) ^ sign;
}
```

实现思路：首先定义常量sign，若x为非负数，则sign每位都为0，若x为负，则sign每位都为1，值为-1。若x为非负数其绝对值为本身，(x + 0) ^ 0 = x；若x为负数，则其绝对值为其相反数，即按位取反后加1，也即+（-1）后按位取反。

## divpwr2
```csharp
int divpwr2(int x, int n) 
{
    int sign = (x >> 31);
    int bias = (~(sign << n)) & sign;
    return (x + bias) >> n;
}
```

实现思路：首先明确除以2的n次方，即向右移n位。但对于正数和负数向右移n位实际上均为向下取整，题目要求向0取整，所以我们需要为负数加上偏置项2^n - 1，使其向0取整。当x为非负数时，sign为0，bias为0，正常右移。当x为负数时，sign每位均为1，bias为低n位为1，其余位为0，也即值为2^n - 1。此时对于任意整数x，加上偏置项后向右移n位即可达到除以2^n并向0取整。
