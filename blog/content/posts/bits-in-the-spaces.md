+++
title = "The bits are in the spaces"
date = 2026-11-08
draft = true
+++

[readable-hash](https://github.com/renatgalimov/readable-hash-rs) turns a hash into something you can read: `hello` becomes `thatised`. It only goes one way, like the hash under it. I wanted a version that goes both ways: take a 64-bit number, print three made-up words, and get exactly that number back from them.

The version I had averaged 29 characters, and some phrases ran past 40. That’s long for something you’re supposed to compare at a glance. So the goal got simple: keep all 64 bits and make the phrase as short as possible.

## TL;DR

<figure>
  <img src="/diagrams/space-layouts.svg" alt="Three words built from tokens. Fixed word lengths need 11 tokens for 64 bits. With free word lengths, ten tokens split into three words in six ways; each layout holds 63 bits and the choice of layout adds 2.58 bits, 65.6 in total." style="width:100%;height:auto;border-radius:8px;">
  <figcaption>You pay for the two spaces anyway. Let them carry data.</figcaption>
</figure>

The words are glued together from tokens learned from English: a beginning like `con`, `tra` or `sta`, then a few more pieces, then an ending. Every token is a choice, and every choice stores bits. Pick one of 128 beginnings and you’ve stored 7 bits. Pick one of 64 continuations and you’ve stored 6.

Why not real words? I checked that first. A list of common English words gives you 11 to 13 bits per word, so 64 bits takes five or six of them. Real words read better, but they aren’t denser.

## The wrong question

My first question to the agent was how much variable word length adds. If a word can be 2, 3 or 4 tokens long, that should be worth something.

It came back with about 0.05 bits per word. Almost nothing: there are so many more long words than short ones that letting a word be short barely adds to the pile.

The number was right, but the question was wrong. It holds the number of words fixed and lets the length float. Flip it: fix the length and let the spaces float.

Take two tokens. Fixed as one word, that’s a beginning and a continuation: 128 × 64, 13 bits. Now let the second token decide. It either continues the word (64 options) or starts a new one (128 options). That’s 128 × 192, 14.58 bits, from the same two tokens. Every token after the first is now worth 7.58 bits instead of 6.

## Three words, ten tokens

Now keep three words, but let each one be 1 to 4 tokens long.

Ten tokens in any one layout, say 4 + 3 + 3, give 3 × 7 + 7 × 6 = 63 bits, one short of 64. With fixed lengths you need eleven tokens.

But there are six ways to cut ten tokens into three words of at most four: 4+4+2, 4+2+4, 2+4+4, 4+3+3, 3+4+3 and 3+3+4. The decoder sees the spaces, so it knows which layout it got. Six layouts are another log₂ 6 ≈ 2.58 bits, and 63 + 2.58 = 65.6. Ten tokens are enough.

The missing bit was in the spaces. And they cost nothing extra: three words have two spaces no matter where they go.

(The decoder still has to split each word back into tokens. The token sets are built so a word splits only one way.)

## Shortest first

Which phrase gets which number? Sort all valid phrases by length, spaces included, and give the 2⁶⁴ shortest the numbers 0 to 2⁶⁴ − 1. Ties go by a fixed order.

Going from a number to a phrase and back is just counting. Precompute how many words of each length can follow each token, then walk down the phrase, subtracting as you go. Nothing is random and nothing is looked up. And nothing in the same grammar can be shorter, because a shorter phrase would have taken one of those first 2⁶⁴ places.

The real model is bigger than the toy one: 256 beginnings, up to 64 continuations after each token (only the ones English words actually use), up to 5 tokens per word, and 2 to 4 words per phrase. Over random 64-bit numbers, counting spaces:

| | average | longest |
|---|---:|---:|
| fixed-length words | 28.9 | 40 |
| shortest first | 23.6 | 24 |

The fixed-length maximum is the longest phrase in a sample of 200,000. The shortest-first numbers are exact.

The average sits right under the maximum. Two thirds of all numbers get exactly 24 characters. That’s what sorting does: everything piles up at the last length you need.

A few of them:

```
0xdeadbeefcafebabe   finazo jacinoung joyjugn
0xffffffffffffffff   voywear picawbug denslon
hash of "hello"      hoseulum cerefy dogwordt
```

## What it costs

Shortest first doesn’t care what a token looks like, only how long it is. It happily picks `phth`, `zyzd` and `braxxcue`. I scored the words with a letter-trigram model trained on English text, in bits per letter (lower means more English-like). Common English words score 3.5, the fixed-length words 4.4, and the shortest-first words 5.7. Shorter, and noticeably stranger.

Small numbers get short phrases. 0 is `to to`, 1 is `to of`. That’s what shortest first means, but it shows how big the number is. The fix is to scramble the number before encoding, with something you can undo (the splitmix64 finalizer is a bijection on 64 bits). The examples above are scrambled.

Words still have to end like words. Let any token end a word and phrases get two characters shorter, but 60–69% of the words then end in a fragment that no English word ends with. I kept the endings. For the same reason, one-token words have to be real words (`to`, `of`, `it`, `up`), or you’d see `th` and `sch` standing on their own.

## Next

Length alone is the wrong sort key. The next version will rank phrases by length plus a penalty for unlikely letters, and the weight of that penalty becomes a dial between short and readable. The counting stays the same.

I spent a while trying to make each token carry more. The extra bits were sitting in the gaps between words the whole time.
