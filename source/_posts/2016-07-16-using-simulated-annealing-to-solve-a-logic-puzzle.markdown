---
layout: post
title: "Using Simulated Annealing To Solve A Logic Puzzle"
date: 2016-07-16 02:10
comments: true
categories: [Artificial Intelligence, Search Methods, Logic Puzzles]
---

The other day, I was watching Ted Ed's collection of YouTube videos on riddles and came across this interesting logic puzzle described as ["Einstein's Riddle"](https://www.youtube.com/watch?v=1rDVz_Fb6HQ). Einstein probably didn't make up the riddle, but the problem itself is kind of interesting for a few reasons. You can either watch the video or keep reading for a retelling of the problem below.

<!-- more -->

---

### The Riddle

The world's rarest fish has been stolen from the city aquarium, and the police have followed the scent to a street of 5 identical looking houses. The police can only search one house without the thief getting away, and so we have to find out which house contains the fish.

We have the following information:

1. Each house's owner is of a different nationality.  
   The choices are Dane, Brit, Swede, Norwegian, and German.
2. The interior walls of each house are coloured differently.  
   The choices are yellow, red, white, green, and blue.
3. Each house contains a different animal.  
   The choices are horse, cat, bird, fish, and dog.
4. The owner of each house drinks a different beverage.  
   The choices are water, tea, milk, coffee, and root beer.
5. The owner of each house smokes a different kind of cigar.  
   The choices are Pall Mall, Prince, Blue Master, Dunhill, and Blends.

Furthermore, we have the following 15 clues:

1. The Brit lives in the house with red walls.
2. The Swede has a dog.
3. The Dane drinks tea.
4. The house with green walls is directly to the left of the house with white walls.
5. The owner of the house with green walls drinks coffee.
6. The person who smokes Pall Mall cigars owns a bird.
7. The owner of the house with yellow walls smokes Dunhill.
8. The man living in the center house drinks milk.
9. The Norwegian lives in the first house.
10. The man who smokes blends lives next to the cat owner.
11. The horse's owner lives next to the man who smokes Dunhill.
12. The man who smokes Blue Master drinks root beer.
13. The German smokes Prince.
14. The Norwegian lives next to the house with blue walls.
15. The man who smokes Blends lives next to the man who drinks water.

Using nothing but this information, it is possible to figure out who has the fish. It may be helpful to construct a table and eliminate possibilities using the above clues.

Don't read ahead yet if you want to figure this out on your own first.

---

I remember having seen this problem several years ago, and I solved it on paper using the traditional logical solution shown in the video using a lot of process of elimination. Solving the problem using this method would probably require me to use a pen and paper and spending a lot of time 

When I first learned about the problem years ago, I didn't have much programming knowledge. But now, I wondered if I could use my programming knowledge to solve the problem a different way. At first I wondered if it could be brute forced, just trying every possible arrangement. 
