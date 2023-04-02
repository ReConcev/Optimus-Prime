# Optimus-Prime
> Optimal Discovery of Prime Numbers through Symmetric MultiProcessing (SMP)

Primes are of great interest in cryptography and other sciences, but are an NP-Complete or NP-Hard problem, for which "brute force" algorithms have been employed.  In short, the way you determine the next prime is by testing division for previously discovered primes, and as more primes are discovered, the number of tests increase.

It is beyond the scope of this paper to explore the concept of a recursion relationship for a prime number series (such as Gaussian or Spiral Primes), although I have personally spent countless hours spellbound.

It is the scope of this paper to outline a more efficient brute force algorithm, one that is "embarassingly parallelized".  The project will include a performance conTest against some "state-of-the-art" algorithm as yet to be identified, and an algorithm that will be outlined in this paper.

### Divide and Conquer

Prime numbers can be conceptualized in many ways, and every good algorithm is based on good architecture, and hopefully a real-world metaphor to help explain it.

I think of prime numbers as intervals originating from zero.  I think of intervals as wavelengths of lights, or sine waves.  A prime can then be thought of as new "color" -- a new waveform of a prime wavelength that crosses the central axis at sequential intervals.

Example:
* 2,4,6,8...  (the color 2)
* 3,6,9,12... (the color 3)

Now imagine we turn on the first 4 waveforms -- 2,3,5,7.  If we then sequentially began to check which numbers of a sequential number line were "lit" (were divisible by a prime) we would see 8, 9, 10, 12... We would not see 11 lit.  No prior primes cross this number, and we can then identify it as a prime, light up another color (color 11 -- newest prime waveform identified), and keep the search moving.

This breaks the process down into:
1. Create a shared data structure to represent the sequential number line
2. Identify primes in a bounded range (unlit).
> Notes:
> * A prime+1 is never prime (can be lit)
> * A prime+2 is a prime candidate (pun intended)
3. Create waveform emitters to expand the bounded range
> Notes:
> * A prime emitter can be thought of in terms of a base number system.  The base 10 system we all use increments every 10th number, using 0 through 9 for the non-interval increments.  We may be able to optimize math if we align an emitter to the base number system of the prime instance.

**Note: it should be obvious that as we spawn waveform emitters, we will become "highly parallelized".  What may be less obvious is the opportunities for parallelism when identifying lit/unlit numbers within a bounded range.**

Runtime optimization is a balance between how fast waveform emitters propagate light (think speed of light being speed at which intervals are marked), and how fast we search a bounded range using parallized elimination of lit numbers.
