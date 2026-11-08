+++
title = "readable-hash 0.11: numbers you can read back"
date = 2026-11-08
draft = true
+++

[readable-hash](https://github.com/renatgalimov/readable-hash-rs) turns hashes into something you can read. Until now it only went one way: `hello` becomes `thatised`, and there’s no getting `hello` back.

0.11 adds a second mode that goes both ways. Give it any 64-bit number and it prints a short phrase of made-up words. Give it the phrase and you get the number back, exactly.

```rust
use readable_hash::{decode_u64, encode_u64};

fn main() {
    let phrase = encode_u64(42);
    println!("{phrase}");
    // drumlot forfelizo dakwey

    assert_eq!(decode_u64(&phrase), Some(42));
}
```

## What it looks like

```text
1                      kols mesiledan bouitacap
2                      flybulio formilii bromia
3                      laykdah scoolky pulrula
1000                   mamaphie gimtifur shroit
1001                   boswen bixcii motabaroke
18446744073709551615   voywear picawbug denslon
```

A phrase is never longer than 24 characters, spaces included, and most are exactly 24. It’s usually three words, sometimes four.

Neighbouring numbers don’t look like neighbours. 1000 and 1001 have nothing in common, because the number gets scrambled before it’s spelled out. The scramble can be undone, so nothing is lost. It isn’t a secret either: anyone with the crate can undo it.

## What you can count on

* Every 64-bit number has exactly one phrase, and every phrase has exactly one number.
* `decode_u64` returns `None` for anything `encode_u64` couldn’t have produced.
* The format is frozen. A phrase you store today decodes to the same number in every later version.
* Everything runs locally. The word model is compiled into the crate, so there’s no lookup table to keep and no service to call.

There’s no checksum. A typo can land on another valid phrase and give you a different number. If that matters, check the number on your side.

## What it isn’t

It isn’t encryption. The phrase is the number, written differently. Don’t put anything in it you wouldn’t put in a URL.

It isn’t English. The words are glued together from pieces of English words, so most of them are pronounceable, but not all of them are pretty. You will meet `braxxcue`. Phrases that spell something offensive are filtered out.

## How it fits

Sixty-four bits in 24 characters needs a trick. Word lengths aren’t fixed, so where the spaces fall carries bits too. I wrote that part up separately: [The bits are in the spaces](@/posts/bits-in-the-spaces.md).

## Getting it

```sh
cargo add readable-hash@0.11
```

`english_word_hash` is still there and gives the same words it always did.
