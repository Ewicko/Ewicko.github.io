---
title: "The Ideal Time for a Solve"
date: 2026-06-06
description: "How much compute is enough?"
draft: false
math: false
---

### Context
How much compute should you use to get a design solution? How much development time is enough for making a solve faster?

### Meat
Obviously faster is better when it comes to getting a solution to a problem. But in hardware, when you are generating an irl design, there may be a max speed that satisfies the 80/20 of solver development/resources. What do I mean? Well, basically, theres a finite amount of resources one can use to solve things, both compute (calculations per second, with more per second costing more money) and solver development (think Abe Lincoln's "Give me six hours to chop down a tree and I will spend the first four sharpening the axe."). These are the knobs you can turn but this begs the question: how do you know when to give up? With hardware, I argue the design lifecycle largely determines this. The time it takes to test the hardware produced must be taken into consideration and the time it takes to ruminate on the hardware must also be taken into account. You can keep testing countless hardware designs for the wrong problem, but a serendipitous thought in the shower can help you choose the correct problem to design for in the first place. This varies per task of course. Also, one thing to note, is that the development of the solver (to make it use less compute) follows an s curve categorically. Ie working on "incompressible fluids sim X" will have its own 80/20 point where the only way to make it meaningfully faster with reasonable resources would be to just add compute. 
So this is probably the formula: take the EV of the development, compare that cost per design to the cost of say 10 fast designs (cost of development is amortized over time), then compare number of designs you get to test (ie compare cost and time/design). Then, take the formula for the "it will probably take this long to iterate on this design" (testing and ruminating): 2t + s = iteration_time, where the first half is the time to test (t is the time you think it will take), and the second half is for rumination, conveniently the number of showers needed. The number of showers needed can be found by c/10, taking c the cost or MSRP of the thing being designed and dividing by 10. If you take 2 showers a day, you might need a faster solver. If you are like most engineers and only shower before seeing your girlfriend, you might be able to get away with a slower solver.
