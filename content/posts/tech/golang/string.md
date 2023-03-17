# string

## what is

1. a read-only slice of bytes;
   1. a strings can hold arbitrary byte sequence, even they are not always valid UFT-8 byte sequence
   for:
   `const sample = "\xbd\xb2\x3d\xbc\x20\xe2\x8c\x98"`
   will print mess;
   `��=� ⌘`

   2. A string literal, absent byte-level escapes, always holds valid UTF-8 sequences.

 > java use uft-16
 >
1.

## internal implement

```go
type StringHeader struct {
        Data uintptr
        Len  int
}
```

## back quote vs  double quote

1. back quote:
    will not escape characters

2. double quote: will escape characters

### escape

> an  character is a character that invokes an alternative interpretation on subsequent characters in a character sequence

Value Description
\a Alert or bell
\b Backspace
\\ Backslash
\t Horizontal tab
\n Line feed or newline
\f Form feed
\r Carriage return
\v Vertical tab
\' Single quote (only in rune literals)
\" Double quote (only in string literals)
