# Optimal Prime Discovery Algorithm
> Copyright 2023.  All rights reserved.

### Motivation for Prime Discovery
1. Curiosity (aka obsession)
2. [RSA Encryption](https://theconversation.com/why-do-we-need-to-know-about-prime-numbers-with-millions-of-digits-89878)

### Given
1. The [Prime Number Theorem](https://en.wikipedia.org/wiki/Prime_number_theorem) is correct.
2. Factorization Testing is the only discovery method given the ++random distribution of primes++ (NP-Hard or "Brute force").
3. There are an infinite number if primes.
4. For prime n, there is a bounded range of prime candidates from n+1 to n^2^, in which primes may be completely determined using the set of all primes up to and including n
5. The bounded range of prime candidates will contain at least one additional prime (because primes are infinite).

### Theory
1. Although NP-Hard by nature, there exists a parallelized, multiprocessing algorithm (aka SMP) to prime discover that can be proven as theorhetically optimal, given the current nature of NUMA computer technology, and Rust "correctness".
2. Rust is performant by design.  We talk a lot about memory safety, but speak little about memory being freed immediately when no longer required.  This is the heart of NUMA correctness, or correctness in any processing architecture that I can conceive of.  Therefore, Rust achieves the theorhetically "leanest" use of memory resources.
3. Rust achieves "fearless concurrency".  The interior mutability pattern throws a mutex lock (blocks read threads) just long enough (aka theorhetical minimal) to complete the write by one owner thread, and then returns it to immutable guarantees and multithreaded reads.  This allows the fastest interhost immutable sharing (aka localhost, node in cluster) ++[SMP](https://en.wikipedia.org/wiki/Symmetric_multiprocessing) through multithreading++ model, preferably  within a [GPGPU](https://en.wikipedia.org/wiki/General-purpose_computing_on_graphics_processing_units) infrastructure, are similar SMP architecture.

### Algorithmic Specification
1. For each new prime discovered through testing (aka largest prime starting with 2), *denoted **(++n++)***.
    > Preload every prime already discovered to "jumpstart" the search for the next largest prime.  It helps that primes get scarcer, with a growth defined in the Prime Theorem.
2.  a new "chunk" or "universe" of tests may be spawned testing all previous discovered primes in parallel against an ordered binary array indexed by candidates (aka VecDeque -- "a double-ended queue implemented with a growable ring buffer") within the bounded range of ***++n+1++*** through ***++n^2^++*** in a "complete" fashion.  ***++No other prime determination can be performed until the primes within this range are discovered, AND there must be at least one new prime within this range.++***  When discovered, recursion occurs for the next bounded range.
3. The testing of each candidate within a bounded range is dependent only upon the immutable primes array bounding the universe of computation, and more importantly is independent of all other candidates within said range (meaning that each candidate can be tested for factorization in parallel).
4. The number of integer candidates in a universe of tests (size of test range) increases quadraticly (squares), while the length of the primes array monotonicly decreases.  Thus, the rate of growth of the immutable array of primes will continually decrease, while the ability for parallel, multithreaded testing of candidates increases continuously (quadratically).  This is why parallel processing is so effective.
5. A number is prime only if it is not "non-prime".  (That is the NP-Hard part of the algorithm, and there is no escapaing it)  Said another way, a mutable boolean array of the range of candidates can be marked by parallelized prime "runners" marking "non-primes" (***scatter***), and once all prime runners within the bounded range complete marking, all unmarked members would then be primes (***gather***).  After identification of primes, this part of boolean array can be freed.  A double-ended queue implemented with a growable ring buffer (aka VecDeque) is an optimal collection type.
6. Prime runners can be spawned in parallel for each prime in the array to mark independent subranges of a range for that prime, in accordance with the frequency of "non-primes" it produces.  Nothing prevents multiple runners for a given prime (splitting up the candidates in the bounded range), as long as marking is partitioned with respect the the range, so each runner can also be multithreaded.
7. Candidates can be constrained to a type such as "Unsigned Odd Integer", effectively cutting the boolean array sizes in half, and eliminating the ***++n=2++*** prime runner(s).
8. Nothing prevents further constraint of type for other higher frequency primes to reduce size of boolean array.  Such an approach will offset resource requirements for quadratically increasing ranges.  Data types of "Unsigned non-factor 2,3,5,7,11,... Integers" are ripe candidates to trade cpu (modulus operations) for memory (size of boolean arrays).  Said another way, if even numbers can be elimated beforehand, so can ever 3,5,and 7 number (in theory).
9. Storage of large integers can be optimized using Unsigned VarInt encoding.(MSB bit of each byte used to designate another LSbyte will follow.) This produces minimum storage requirements for Unsigned Integers or "just enough bytes".  This could be further refined to "just enough bits" in theory.

### Implementation
#### Seeking a Sponsor...
