---
version: 1.1.0
title: Strings
redirect_from:
  - /lessons/basics/strings/
---

Strings, Char Lists, Graphemes และ Codepoints.

{% include toc.html %}

## Strings

String ใน Elixir นั้นคือลำดับของ byte ดูใน code ตัวอย่าง

```elixir
iex> string = <<104,101,108,108,111>>
"hello"
iex> string <> <<0>>
<<104, 101, 108, 108, 111, 0>>
```

จาก code ตัวอย่างทำการต่อ string ด้วย byte ที่มีค่า `0` แล้ว IEx แสดงข้อมูลของ string เป็น binary
เนื่องจาก <<0>> ไม่ใช่ข้อมูลชนิด string แต่เป็น byte ด้วยวิธีนี้ทำให้แสดงข้อมูลแต่ละ byte ของ string ออกมา

>NOTE: การใช้สัญลักษณ์ <<>> เพื่อบอกให้ compiler รู้ว่าค่าที่อยู่ข้างในมีชนิดเป็น byte

## Charlists

การทำงานภายในของ Elixir นั้นข้อมูลชนิด string เป็นลำดับของ byte แทนที่จะเป็น array ของ character
ดังนั้น Elixir จึงมีข้อมูลชนิด char list (character list)
โดยที่ข้อมูล charlist นั้นจะครอบด้วย single quote ส่วน string นั้นจะครอบด้วย double quote

สิ่งที่ charlist แตกต่างจาก string มีอะไรอีก
ค่าข้อมูลแต่ละตัวใน charlist จะเป็น UNICODE ของตัวอักขระ ส่วน string จะเป็น UTF-8

ดังตัวอย่าง

```elixir
iex(5)> 'hełło'
[104, 101, 322, 322, 111]
iex(6)> "hełło" <> <<0>>
<<104, 101, 197, 130, 197, 130, 111, 0>>
```

`322` คือค่า Unicode ของ `ł`
ส่วน UTF-8 จะมี 2  byte คือ `197` และ `130`

โดยปกติการเขียนโปรแกรมใน Elixir จะใช้ string
เนื่องจากถ้าใช้งาน charlist จำเป็นต้องใช้ module จากภาษา Erlang ด้วย

ดูข้อมูลเพิ่มเติมจาก [`Getting Started Guide`](http://elixir-lang.org/getting-started/binaries-strings-and-char-lists.html).

## Graphemes and Codepoints

Codepoints are just simple Unicode characters which are represented by one or more bytes, depending on the UTF-8 encoding. Characters outside of the US ASCII character set will always encode as more than one byte. For example, Latin characters with a tilde or accents (`á, ñ, è`) are typically encoded as two bytes. Characters from Asian languages are often encoded as three or four bytes. Graphemes consist of multiple codepoints that are rendered as a single character.

The String module already provides two functions to obtain them, `graphemes/1` and `codepoints/1`. Let's look at an example:

```elixir
iex> string = "\u0061\u0301"
"á"

iex> String.codepoints string
["a", "́"]

iex> String.graphemes string
["á"]
```

## String Functions

มาดู function ที่สำคัญและมีประโยชน์มาก ๆ ของ String module ซึ่งสามารถรายละเอียดของ function ทั้งหมดได้ที่ [`String`](https://hexdocs.pm/elixir/String.html)

### `length/1`

ทำการส่งค่าจำนวนของ Graphemes ใน string ที่กำหนดกลับมา

```elixir
iex> String.length "Hello"
5
```

### `replace/3`

ทำการส่งค่าใหม่ชนิด string โดยข้อมูลถูกแทนที่ด้วยค่าใหม่ตามรูปแบบที่ต้องการกลับมาให้

```elixir
iex> String.replace("Hello", "e", "a")
"Hallo"
```

### `duplicate/2`

ทำการส่งค่าใหม่ชนิด string โดยข้อมูลจะซ้ำกันตามจำนวนที่กำหนดกลับมาให้

```elixir
iex> String.duplicate("Oh my ", 3)
"Oh my Oh my Oh my "
```

### `split/2`

ทำการส่งค่า list ของ string โดยแยกตามรูปแบบที่กำหนกกลับมาก

```elixir
iex> String.split("Hello World", " ")
["Hello", "World"]
```

## แบบฝึกหัด

Let's walk through a simple exercises to demonstrate we are ready to go with Strings!

### Anagrams

A and B are considered anagrams if there's a way to rearrange A or B making them equal. For example:

+ A = super
+ B = perus

If we re-arrange the characters on String A, we can get the string B, and vice versa.

So, how could we check if two strings are Anagrams in Elixir?  The easiest solution is to just sort the graphemes of each string alphabetically and then check if they both lists are equal. Let's try that:

```elixir
defmodule Anagram do
  def anagrams?(a, b) when is_binary(a) and is_binary(b) do
    sort_string(a) == sort_string(b)
  end

  def sort_string(string) do
    string
    |> String.downcase
    |> String.graphemes
    |> Enum.sort
  end
end
```

Let's first give a watch to `anagrams?/2`. We are checking whether the parameters we are receiving are binaries or not. That's the way we check if a parameter is a String in Elixir.

After that, we are calling a function that orders the string alphabetically. It first converts the string to lowercase and then uses `String.graphemes/1` to get a list of the graphemes in the string. Finally, it pipes that list into `Enum.sort/1`. Pretty straight, right?

Let's check the output on iex:

```elixir
iex> Anagram.anagrams?("Hello", "ohell")
true

iex> Anagram.anagrams?("María", "íMara")
true

iex> Anagram.anagrams?(3, 5)
** (FunctionClauseError) no function clause matching in Anagram.anagrams?/2
    iex:2: Anagram.anagrams?(3, 5)
```

As you can see, the last call to `anagrams?` caused a FunctionClauseError. This error is telling us that there is no function in our module that meets the pattern of receiving two non-binary arguments, and that's exactly what we want, to just receive two strings, and nothing more.
