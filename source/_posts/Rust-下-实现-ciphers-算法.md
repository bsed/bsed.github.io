---
title: "Rust 下 实现 ciphers 算法"
categories: [ "算法" ]
tags: [ "algorithms","rust" ]
draft: false
slug: "implementation-of-ciphers-algorithm-under-rust"
date: "2019-07-19 11:06:00"
---

密码学，一种凯撒密码，也称为凯撒密码，移位密码，凯撒代码或凯撒变换，是最简单和最广为人知的加密技术之一。
它是一种替换密码，其中明文中的每个字母都被字母表中的一些固定数量的位置的字母替换。例如，左移3，D将被A替换，E将变为B，依此类推。
该方法以Julius Caesar的名字命名，他在私人通信中使用了它。
由Caesar密码执行的加密步骤通常作为更复杂的方案的一部分，例如Vigenère密码，并且仍然在ROT13系统中具有现代应用。与所有单字母替换密码一样，Caesar密码很容易破解，在现代实践中基本上没有通信安全性。
![rust_ciphers.svg][1]

<!--more-->


Source: [Wikipedia](https://en.wikipedia.org/wiki/Caesar_cipher)
Vigenère (Not implemented yet)
The Vigenère cipher is a method of encrypting alphabetic text by using a series of interwoven Caesar ciphers based on the letters of a keyword. It is a form of polyalphabetic substitution.
The Vigenère cipher has been reinvented many times. The method was originally described by Giovan Battista Bellaso in his 1553 book La cifra del. Sig. Giovan Battista Bellaso; however, the scheme was later misattributed to Blaise de Vigenère in the 19th century, and is now widely known as the "Vigenère cipher".
Though the cipher is easy to understand and implement, for three centuries it resisted all attempts to break it; this earned it the description le chiffre indéchiffrable(French for 'the indecipherable cipher'). Many people have tried to implement encryption schemes that are essentially Vigenère ciphers. Friedrich Kasiski was the first to publish a general method of deciphering a Vigenère cipher in 1863.

Source: [Wikipedia](https://en.wikipedia.org/wiki/Vigen%C3%A8re_cipher)
Transposition (Not implemented yet)
In cryptography, a transposition cipher is a method of encryption by which the positions held by units of plaintext (which are commonly characters or groups of characters) are shifted according to a regular system, so that the ciphertext constitutes a permutation of the plaintext. That is, the order of the units is changed (the plaintext is reordered).
Mathematically a bijective function is used on the characters' positions to encrypt and an inverse function to decrypt.

Source: [Wikipedia](https://en.wikipedia.org/wiki/Transposition_cipher)

代码(*caesar.rs*):
```rust
//! Caesar Cipher
//! Based on cipher_crypt::caesar
//!
//! ＃算法
//! 
//! 通过移旋转每个ASCII字符。 最基本的例子是ROT 13，它将'a'旋转到
//! 'N'。 此实现不会旋转unicode字符。

/// Caesar cipher 通过shift旋转密文并返回一个拥有的String.
pub fn caesar(cipher: &str, shift: u8) -> String {
  cipher
    .chars()
    .map(|c| {
      if c.is_ascii_alphabetic() {
        let first = if c.is_ascii_lowercase() {
          b'a'
        } else {
          b'A'
        };
        
        (fisrst + (c as u8 + shift - first) % 26) as char
      } else {
        c
      }
    })
    .collect()
}

#[cfg(test)]
mod tests {
  use supper::*;
  #[test]
  fn empty() {
        assert_eq!(caesar("", 13), "");
  }

  #[test]
  fn caesar_rot_13() {
    assert_eq!(caesar("rust", 13), "ehfg");
  }
  #[test]
  fn caesar_unicode() {
      assert_eq!(caesar("attack at dawn 攻", 5), "fyyfhp fy ifbs 攻");
  }
}
```


  [1]: https://imgs.gnux.cn/usr/uploads/2019/07/586006452.svg