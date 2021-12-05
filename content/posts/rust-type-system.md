---
title: "Rust Type System"
date: 2021-12-05T12:30:53-08:00
draft: false
---
I've been fucking around and avoiding my finals by getting caught up on the first 5 days of [the advent of code](https://adventofcode.com).
Because I hate my brain, I have decided that it would be fun to learn how to write functional code in Rust, specifically for Day 3 Part 2.

Functional Rust is terrifying.

Look at this function header I just wrote:

```
fn get_diagnostic_value<F>(bit_criteria: F, bit_index: usize, mut diagnostic_values: Vec<Vec<bool>>, bit_counts: [(i32, i32); 12]) 
    -> Vec<bool> 
    where F: Fn([(i32, i32); 12]) -> Box<dyn Fn(&Vec<bool>) -> bool> {
```

Why do I do this to myself. S M H.

I should start seriously blogging sometime.
