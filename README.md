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

## float_neg
```csharp
unsigned float_neg(unsigned uf) 
{
  unsigned result;
  unsigned temp;
  result = uf ^ 0x80000000; 
  temp = uf << 1;
  if(temp > 0xff000000)
    return uf;
  return result;
}
```

实现思路：根据IEEE我们易知浮点数用最高位表示符号位，因此我们只需要把最高位取反即可。同时根据题目要求，当参数为NaN时，直接返回参数本身即可。这里采用将uf左移1位避免符号位影响，然后与0xff000000进行比较，若大于则说明原uf23-30位均为1且0-22位不全为0，即为NaN，若小于等于则返回与0x80000000异或，将最高位取反，其他位不变的结果。

## logicalNeg
```csharp
int logicalNeg(int x) 
{
  return ((x | ~x + 1) >> 31) + 1;
}
```

实现思路：本题突破点在于对于任何一个非零数x，（x| ~x + 1）的最高位必然为1，将其右移31位后必然得到-1，再加1则得到0。而当x为0时，（x| ~x + 1）的最高位为0，右移31位后仍为0，再加1则得到1。满足逻辑非的要求。

## bitMask
```csharp
int bitMask(int highbit, int lowbit) 
{
  int base = ~0;
  int i = 2;
  return base + (i << highbit) >> lowbit << lowbit;
}
```

实现思路：首先构建一个各位全为1，值为-1的底。接下来的思路则为将该数highbit+1位至31位和0至lowbit-1位置0。为了减少操作符数量，设i为1，只需将向左移highbit位再与base相加，则可将highbit+1位至31位置0，再向右移lowbit位，向左移lowbit位则可将0至lowbit-1位置0。

## isGreater
```csharp
int isGreater(int x, int y) 
{
  int temp = x + ~y;
  int diff = x ^ y;
  int over = temp ^ x;
  int result = temp ^ (diff & over);
  return !(result >> 31);
}
```

实现思路：首先构建temp为x + ~y，diff用以保存x和y是否为异号，over用以表示temp和x是否异号，diff和over结合共同用于表示是否x + ~y溢出。若未溢出，则x - y应为正数，即x + ~y的符号位应为0，若溢出，则应取x为正，y为负，即temp符号位为1的情况，所以采用temp与（diff ^ over)异或，可满足在这两种情况下符号位为0，其余情况下为1。右移31位后取非则满足题意要求。

## logicalShift
```csharp
int logicalShift(int x, int n) 
{
  int temp = x >> n;
  int i = 1 << 31;
  int mask = i >> n << 1;
  mask = ~mask;
  int result = temp & mask;
  return result;
}
```

实现思路：只需保证x在右移后高n位为0即可。通过常数1左移31位再右移n位再左移1位再取反，我们则得到高n位位0，其余位为1的一个二进制数，将该数与x右移n位后的数按位取交则可满足题意。

## satMul2
```csharp
int satMul2(int x) 
{
  int result = x << 1; 
  int over = (x ^ result) >> 31; 
  int sign = result >> 31; 
  int tmin = 1 << 31;
  int t = tmin ^ sign;
  result = (result & ~over) + (over & t); 
  return result;
}
```

实现思路：x乘2即为x左移1位，但我们需要考虑溢出，及溢出后保留为最大或最小值。构建over表示是否溢出，sign表示左移后的符号，借助常数1构建tmin，若sign全0，则t为tmin，若sign全1则t为tmax，恰好与溢出前所表示数的正负一致。最后result根据是否溢出进行修正即可。

## subOK
```csharp
int subOK(int x, int y) 
{
  int diff = (x ^ y);
  int sub = x + (~y + 1);
  int over = (x ^ sub);
  return !( (diff & over) >> 31);
}
```

实现思路：diff用于储存是否异号，sub为相减后的结果，over表示相减后是否与x异号。若x和y异号，且x-y后变号，则表示溢出，右移31位后取非返回0，否则返回1。

## trueThreeFourths
```csharp
int trueThreeFourths(int x)
{
  int bias = 0x3;
  int sign = x >> 31;
  int remain = x & bias;
  bias = sign & bias;
  int result = x >> 2;
  result = result + (result << 1);
  remain = remain + (remain << 1)
  return result + ((remain + bias) >> 2);
}
```

实现思路：首先借助bias将x右移的过程中舍去的位数存到remain中。该题同样要求向0舍入，借助sign对bias进行修正，若x为正数则bias修正为0，若为负数则仍为0x3。为了避免溢出，则先将x向右移2位实现除以4，然后实现乘3。而后对remain进行乘3/4的操作，注意在除以4之前加上已经修正过的偏置项即可。最后将result和舍入处理过的remain相加即可。

## isPower2
```csharp
int isPower2(int x) 
{
  int sign = ~(x >> 31);
  int temp = sign & x;
  int judge = !temp;
  int result = temp + sign;
  result = result & temp;
  return !result ^ judge;
}
```

实现思路：首先明确负数和0都不可能是2的幂次，因此通过函数前两行将非正数全部置为0，再通过judge在后续排除为0的情况。此时只考虑x为正的情况，sign值为-1，通过将result与result-1进行交来判断temp中是否只有一个1，若只有一个1则result为0，否则为非0。将result取非后再和judge，此时只有当x为正数，且所有位中只含有一个1时才返回1，满足题意要求。

## float_i2f
```csharp
unsigned float_i2f(int x) 
{
  if(!x)
  {
    return 0;
  }
  unsigned sign = x & 0x80000000;
  unsigned i = 0;
  unsigned absx = sign ? (~x + 1) : x;
  unsigned temp = absx;
  while (temp > 1)
  {
    temp = temp >> 1;
    i++;
  }
  unsigned E = i;
  unsigned e = E + 127;
  unsigned frac = absx << 32 - i;
  unsigned round = frac & 0x1ff;
  frac = frac >> 9;
  int bias = 0;
  if(round > 0x100)
  {
    bias = 1;
  }
  else if(round == 0x100 & frac)
  {
    bias = 1;
  }
  unsigned result = (sign | (e << 23) | frac) + bias;
  return result;
}
```

实现思路：若x为0直接返回0。若x为非0，则继续进行以下操作。利用sign保存x的符号位，而后将x的绝对值保存在temp中，便于后续运算。接下来的while循环实际上则是一个移动小数点的过程，直至temp小于1，此时我们得到的i即为浮点数表示公式中的E，将E + 127偏移后置为e。将absx左移32-i位后剩下的则为小数部分，用round保留小数部分的低9位，再将frac右移9位。再进行两种情况的判断，第一种是round大于0x100，则进位，bias置1，第二种是round刚好等于0x100，向偶数进位，即此时frac最低一位为1，bias置1。最后的结果则将符号位，指数位和小数位并到一起再加上bias即可。

## howManyBits
```csharp
int howManyBits(int x) 
{
  int sign = x >> 31;
  int temp = sign ^ x;
  int bit16 = (!!(temp >> 16)) << 4;
  temp = temp >> bit16;
  int bit8 = (!!(temp >> 8)) << 3;
  temp = temp >> bit8;
  int bit4 = (!!(temp >> 4)) << 2;
  temp = temp >> bit4;
  int bit2 = (!!(temp >> 2)) << 1;
  temp = temp >> bit2;
  int bit1 = (!!(temp >> 1));
  temp = temp >> bit1;
  int bit0 = temp;
  int result = bit16 + bit8 + bit4 + bit2 + bit1 + bit0 + 1;
  return result;
}
```

实现思路：该题主要实现思路就是将x取绝对值后找到最高位的1，这个找1的过程采用二分查找，首先对高16位进行查找，如有1则将temp右移更新，再找8位，4位以此类推直至找到最终最高位1的位置，之所以最后采用加bit0 = temp而不直接置为1是为了排除0这一特殊情况，最后result将各个bits相加起来，再加上符号位的一位即可。

## float_half
```csharp
unsigned float_half(unsigned uf) 
{
  unsigned NaN = 0x7f800000;
  unsigned mask1 = 0x7fffffff;
  unsigned e = uf & NaN;
  unsigned sign = uf & 0x80000000;
  unsigned round = (uf & 3) == 3;
  unsigned uf_1 = (uf & mask1) >> 1;
  unsigned temp = uf_1 + round;
  unsigned rs_1 = sign | temp;
  if(e == NaN)return uf;
  if(e > 0x00800000)
  {
    return (uf & 0x807fffff) | (e - 0x00800000);
  } 
  return rs_1;
}
```

实验思路：首先取出uf的指数位，符号位以及舍入位，这里之所以&3，是因为完成向偶数取整。uf_1为除去符号位后的uf再除2，temp为uf_1加上舍入项。rs_1为temp加上符号位之后的项。如果e == NaN直接返回uf，如果e大于0x00800000，即为正常情况，返回指数位-1后的结果。剩下的一种情况则为e为0x80000000，此时e不动，小数位右移加舍入项，即为rs_1。至此各种情况均已讨论完毕。
