---
title:      utf8编码实现
categories:
 - 编程
---

讲到utf8，就要说一下unicode，unicode是一个标准，几乎包含了世界上所有的字符，每一个字符在unicode中都有一个对应的码点，用U+表示，如汉字林的码点就是U+6797，数字是16进制表示。在将unicode的码点保存到计算的时候，又有了不同的实现方法如utf8，utf16，utf32等等。这里主要说一下utf8，utf8是一种变长编码格式，一个字符可能使用到1~6个字节，主要好处就是节省存储空间，utf8编码的首字节会有使用的字节长度信息，参考下表：

| Bytes | Bits | Hex Min  | Hex Max  | Byte Sequence in Binary |
| ----- | ---- | -------- | -------- | ----------------------- |
|    1  |   7  | 00000000 | 0000007f | 0vvvvvvv |
|    2  |   11 | 00000080 | 000007FF | 110vvvvv 10vvvvvv |
|    3  |   16 | 00000800 | 0000FFFF | 1110vvvv 10vvvvvv 10vvvvvv |
|    4  |   21 | 00010000 | 001FFFFF | 11110vvv 10vvvvvv 10vvvvvv 10vvvvvv |
|    5  |   26 | 00200000 | 03FFFFFF | 111110vv 10vvvvvv 10vvvvvv 10vvvvvv 10vvvvvv |
|    6  |   31 | 04000000 | 7FFFFFFF | 1111110v 10vvvvvv 10vvvvvv 10vvvvvv 10vvvvvv 10vvvvvv |

第一列表示编码所需字节数，第二列为能表示的 Unicode 的最大二进制位数，第三列和第四列为能表示的 Unicode 范围，最后一列表示编码后的字节布局。把编码中字母v表示的部分连接起来就是对应的 Uniocde 编码。

这里以汉字「林」的 Unicode 编码是 U+6797，对应二进制为 0110011110010111，总共有 15 位。因为两字节最多表示11位，三字节最多表示16位，所以使用三字节编码。对应二进制拆成（从低位到高位）三部分，分别是 0110, 011110, 010111，再拼上编码前缀得到 11100110, 10011110, 10010111，对应十六进制为 0xE6, 0x9E, 0x97，这就是汉字「林」的UTF-8编码。

## 编码实现

在讲实现的时候，先说一下基础数据结构：

```c++
struct Tab {
  /* cmask和cval确定字符个数,如字符长度是3，则cmask是11110000,
   * cval是11100000, cmask & head == cval，其中head是utf8编码
   * 的首字节
     */
  int cmask_;
  int cval_;
  // 在utf8编码中，除去首子集，后续都是10xxxxxx的格式，shift表示
  // utf8编码中x的格式，它是6的倍数
  int shift_;
  // 对应unicode编码最大值
  long maxValue_;
  // 对应unicode编码最小值
  long minValue_;
};

static Tab tab[] =
{
  0x80, 0x00, 0 * 6, 0x7F,       0,          /* 1 byte sequence */
  0xE0, 0xC0, 1 * 6, 0x7FF,      0x80,       /* 2 byte sequence */
  0xF0, 0xE0, 2 * 6, 0xFFFF,     0x800,      /* 3 byte sequence */
  0xF8, 0xF0, 3 * 6, 0x1FFFFF,   0x10000,    /* 4 byte sequence */
  0xFC, 0xF8, 4 * 6, 0x3FFFFFF,  0x200000,   /* 5 byte sequence */
  0xFE, 0xFC, 5 * 6, 0x7FFFFFFF, 0x4000000,  /* 6 byte sequence */
  0,                                         /* end of table */
};
```

### unicode转utf8实现

实现参考下面代码：

```c++
/**
 * ecnode a unicode char to utf8 code
 * @param[in] unicode_code save a unicode code
 * @param[out] utf8_code point to a memory save the utf8 result
 * @return length is the length of utf8_code
 * @return ERROR if error happened
 */
int encodeToUTF8(char *utf8_code, const wchar_t unicode_code) {
  long source_code;
  int tab_shift;
  int byte_cnt;
  Tab *t;

  if (NULL == utf8_code) {
    return ERROR;
  }

  source_code = unicode_code;
  byte_cnt = 0;
  // 从小到大遍历
  for (t = tab; t->cmask_; t++) {
    // 记录字节数
    byte_cnt++;
    // 找到source_code所在范围，开始处理unicode编码到
    // utf8的转换
    if (source_code <= t->maxValue_) {
      cout << "come here " << byte_cnt << endl;
      // 出去最高字节，总共需要位移次数
      tab_shift = t->shift_;
      // 获取首字节内容并按照utf8格式编码，首字节填充编码
      // 长度信息，编码长度来自t->cval_
      *utf8_code = t->cval_ | (source_code >> tab_shift);
      //处理剩下字节，保存在数据的后续位置
      while (tab_shift > 0) {
        tab_shift -= 6;
        // 指向下一个字节
        utf8_code++;
        // 获取低六位内容并填充上10开头，0x80就是10000000
        *utf8_code = 0x80 | ((source_code >> tab_shift) & 0x3F);
      }

      return byte_cnt;
    }
  }
  return ERROR;
}
```

### utf8转unicode实现

实现参考下面代码:

```c++
/**
 * give a utf8 code, decode it and return a unicode code
 *
 * @param[in] utf8_code is an array of char, it saves the utf8 code
 * @param[in] utf8_length is the length of the utf8_code array
 * @param[out] unicode_code save the result, it's a uniocde code
 * @return ERROR if some thing unexpected happened
 * @return result length(byte) if success
 */
int decodeFromUTF8(wchar_t &unicode_code, const char *utf8_code, const size_t utf8_length) {
  long result;
  int first_byte;
  int other_byte;
  int byte_cnt;
  Tab *t;
  if (NULL == utf8_code || utf8_length < 1) {
    return ERROR;
  }

  byte_cnt = 0;

  first_byte = *utf8_code & 0xff;
  // result保存unicode结果，此时result保存了第一个字节的信息
  result = first_byte;

  // 从utf8的低字节开始遍历
  for (t = tab; t->cmask_; t++) {
    byte_cnt++;

    /**
     * 假设utf8是三个字节汉字，在byte_cnt为1和2不会进入if分支，
     * 而是进行移位6位保存低字节内容，在byte_cnt为3时进入，在
     * for语句外面，result已经保存过高位信息
     */
    if ((first_byte & t->cmask_) == t->cval_) {
      result &= t->maxValue_;

      if (result < t->minValue_) {
        return ERROR;
      }

      unicode_code = result;
      return byte_cnt;
    }
    if (byte_cnt >= utf8_length) {
      return ERROR;
    }

    // 处理下一个字节
    utf8_code++;
    /** 0x80 = 10000000，
     * UTF-8 编码从第二个字节开始高两位都是10，
     * 这一步是为了把最高位的 1 去掉 */
    other_byte = (*utf8_code ^ 0x80) & 0xFF;

    /* 0xc0 = 1100000，
     * 这一步检查次高位是否为1，如果是1，则为非法UTF-8 序列*/
    if (other_byte & 0xC0) {
      return ERROR;
    }

    /* other_byte只有低6位有效，根据utf8规则，
     * result左移6位，将other_byte的低6位填进来*/
    result = (result << 6) | other_byte;
  }

  return ERROR;
}
```

代码中都有详细的注释，结合表格中内容，很容易就可以看懂了。
