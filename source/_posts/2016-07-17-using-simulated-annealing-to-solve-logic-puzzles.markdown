---
layout: post
title: "Using Simulated Annealing To Solve Logic Puzzles"
date: 2016-07-17 02:43
comments: true
categories: [Artificial Intelligence, Search Methods, Logic Puzzles]
---

The other day, I was watching Ted Ed's collection of YouTube videos on riddles and came across this interesting logic puzzle described as ["Einstein's Riddle"](https://www.youtube.com/watch?v=1rDVz_Fb6HQ). Einstein probably didn't make up the riddle, but the problem itself is kind of interesting for a few reasons. You can either watch the video or keep reading for a retelling of the problem below.

<!-- more -->

---

### The Riddle

> The world's rarest fish has been stolen from the city aquarium, and the police have followed the scent to a street of 5 identical looking houses all in a row. The police can only search one house without the thief getting away, and so we have to find out which house contains the fish.

{% img center caption /images/simulated_annealing/houses.png 'Houses in a row' 'The 5 houses as shown in the video' %}

We have the following information:

1. Each house's owner is of a different nationality,<br> either Dane, Brit, Swede, Norwegian, or German.
2. The interior walls of each house are coloured differently,<br> either yellow, red, white, green, or blue.
3. Each house contains a different animal,<br> either horse, cat, bird, fish, or dog.
4. The owner of each house drinks a different beverage,<br> either water, tea, milk, coffee, or root beer.
5. The owner of each house smokes a different kind of cigar,<br> either Pall Mall, Prince, Blue Master, Dunhill, or Blends.

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

Using nothing but this information, it is possible to figure out who has the fish.

Don't read ahead yet if you want to figure this out on your own first.

---

I remember having seen this problem several years ago, and I solved it on paper using the traditional logical solution shown in the video using a lot of process of elimination. Solving the problem using this method is probably how it was meant to be done, and I'd recommend trying it first to compare the different methods.

When I first learned about the problem years ago, I didn't have much programming knowledge. But now, I wondered if I could use my programming knowledge to solve the problem a different way. At first I wondered if it could be brute forced, just trying every possible arrangement.

How many arrangements are there? Well for the first house, there are 5 "attributes" to pick (nationality, wall colour, animal, beverage, and cigar), and each has 5 options. This gives 5<sup>5</sup> possibilities for the first house. Then for the second house, there are still 5 attributes to pick, but only 4 options for each. This gives 4<sup>5</sup> possibilities for the second house, 3<sup>5</sup> for the third, 2<sup>5</sup> for the fourth and 1<sup>5</sup> for the fifth. Multiplying these together gives 24 883 200 000 or almost 25 billion possibilities. If we could check 100 000 possibilities every second, it would take 69 hours to check all the possibilities.

There are many ways we could speed up the process, like using some of the clues to significantly reduce the size of the search space. We could also turn the clues into logic expressions in code and use them to perform a similar process of elimination technique to simulate how it would be done on paper. I thought to use a technique I learned in my cooperative and adaptive algorithms class called simulated annealing. Simulated annealing can be used to solve problems like this, where there's a large search space and we are trying to find a global optimum. In this case, the global optimum is the arrangement in which all 15 of the clues are satisfied.

### Simulated Annealing

The idea behind [simulated annealing](https://en.wikipedia.org/wiki/Simulated_annealing) is fairly simple.

We start with an initial state (or "solution"), where a state is one of the almost 25 billion possibilities described above. That is, for each house and for each of its attributes, one of the five choices for the attribute is picked.

We also need to define a cost function which, when given a state, tells us how "good" the state is. Here, a natural cost function would be "the number of clues that are NOT satisfied". For example, if out of the 15 clues, 12 are satisfied, but 3 are not satisfied, our cost function would give us 3 for the corresponding state. We seek to minimize the cost of our state. If the cost of a state is 0, that means all the clues are satisfied!

So now we have the concept of a state and a cost function. Now what if the initial state we picked doesn't have a cost of zero? Then we want to reduce it, right? But how? We have to pick a new state to replace our initial one and evaluate its cost and hope its cost is better (lower). But if we just pick another totally random state, that's basically just doing the brute-force method described earlier.

Instead, we can try to take advantage of what we have and change it little by little. We can do that by defining what's called a "neighbour state". Here, a neighbour state is simply a state which can be reached by swapping an attribute choice between two houses. For example, if our current state has the German in the second house and the Brit in the fourth house, a neighbour of the current state would be the state in which everything is the same except that the German is in the fourth house and the Brit is in the second house. For a given state, there are <sub>5</sub>C<sub>2</sub> = 10 choices for the two houses to swap, and 5 attributes to choose from, giving a total of 50 neighbouring states for any state. The hope is that by simply performing a swap, the cost will probably not drastically increase.

Now that we have the concept of a neighbour state, we can simply choose a random neighbour and "move" to it; that is, change our current state to the neighbour state. But we probably don't want to do that if it has a worse cost right? If our current state has cost 3, then we probably don't want to move to a state of cost 7. However, we will not always be able to move to a state of lower cost. For this problem, it turns out we'll often get stuck in a local minimum, a state in which all neighbouring states have cost greater than or equal to the cost of the current state.

{% img center caption /images/simulated_annealing/local_minimum.png 'Local minimum' 'We want to find the global minimum, not the local minimum' %}

We won't be able to find the global minimum just by choosing neighbours with lower cost all the time, because eventually there won't be any. We can escape this trap either by moving to another random state, or instead, we can sometimes accept worse solutions. The hope here is that by following this worse solution, we can eventually get to the global minimum.

Simulated annealing performs the latter using what's called an acceptance probability. The acceptance probability is used to determine whether we want to move to a neighbouring state or not. There are a few basic properties of the acceptance probability.

If the neighbouring state has lesser or equal cost, then we will always move to it.

If it has greater cost, we will only move to it with a certain probability. Otherwise we'll stay where we are and choose another neighbour. We define the cost delta denoted by Δc, which is simply the current cost subtracted from the neighbour cost, and a parameter t, which stands for temperature, and influences how likely we are to accept the neighbouring state.

{% img center caption /images/simulated_annealing/acceptance_probability.png Acceptance Probability %}

Looking at the exponential function, the greater the cost delta, the lower the power, and thus the lower the acceptance probability is. If the neighbouring cost is much higher than the current cost, we are not likely to move to it.

t affects how often we pick worse solutions. The greater the value of t, the higher the acceptance probability is. t is a parameter we choose which typically starts high and steadily decreases every so often, so we are less likely to accept worse solutions as time goes on (where we are hopefully close to finding the global minimum).

Note that since Δc and t are both positive, the exponential function's value is in the range (0, 1). We can use a random number in this range to choose, based on the acceptance probability, whether we should move to the new state or not. If the random number is less than the acceptance probability's value, we should move to the new state.

#### Turning It Into Code

We now have everything necessary to apply this technique to our problem. To summarize:

1. Pick an initial solution and compute its cost
2. Pick a random neighbour of the current solution
3. Compute its cost, the cost delta, and the acceptance probability
4. Move to the neighbour if a random number in (0, 1) is less than the acceptance probability
5. Repeat steps 2-4 until the current solution's cost is 0. Decrease the temperature `t` every so often.

We'll write the code in python because it's great for stuff like this. First, we need to setup the initial state.

``` python
    nationalities = [ 'dane',      'brit',   'swede',       'norwegian', 'german'    ]
    colours       = [ 'yellow',    'red',    'white',       'green',     'blue'      ]
    animals       = [ 'horse',     'cat',    'bird',        'fish',      'dog'       ]
    beverages     = [ 'water',     'tea',    'milk',        'coffee',    'root beer' ]
    cigars        = [ 'pall mall', 'prince', 'blue master', 'dunhill',   'blends'    ]

    attributes = [nationalities, colours, animals, beverages, cigars]

    NUM_HOUSES = 5
    initial = []

    for i in xrange(NUM_HOUSES):
        initial.append([attr[i] for attr in attributes])
```

For each attribute, the <i>i<sup>th</sup></i> house will take on the <i>i<sup>th</sup></i> choice for the attribute. Thus, the initial state can be represented by a list of 5 houses, each of which is a list of the attributes it takes on.

```python
    [
        [ 'dane',      'yellow', 'horse', 'water',     'pall mall'   ], # House 1
        [ 'brit',      'red',    'cat',   'tea',       'prince'      ], # House 2
        [ 'swede',     'white',  'bird',  'milk',      'blue master' ], # House 3
        [ 'norwegian', 'green',  'fish',  'coffee',    'dunhill'     ], # House 4
        [ 'german',    'blue',   'dog',   'root beer', 'blends'      ], # House 5
    ]
```

For convenience, we'll also define the following constants:

```python
    NAT = 0 # Nationality index
    COL = 1 # Colour index
    ANI = 2 # Animal index
    BEV = 3 # Beverage index
    CIG = 4 # Cigar index
```

Each index in the list for a house corresponds to a specific attribute as shown above.

Now we're ready to define our simulated annealing procedure.

```python
    import math
    import random

    def sa(initial):
        # Define initial values
        current = initial
        current_cost = cost_of_state(current)
        temp = 1.0 # initial value of t used in acceptance probability
        num_iterations = 0

        while current_cost > 0: # keep going until we find the global minimum
            num_iterations += 1

            neighbour = get_random_neighbour(current)
            neighbour_cost = cost_of_state(neighbour)

            cost_delta = neighbour_cost - current_cost

            # If the neighbouring state is at least as good as the current state
            # move to it, otherwise check the acceptance probability and only
            # move if the random number is less than it
            if cost_delta <= 0 or random.random() < math.exp(-cost_delta/temp):
                current, current_cost = neighbour, neighbour_cost

            # Decrease the temperature by 0.05 every 500 iterations until it's at 0.20
            if num_iterations % 500 == 0 and temp > 0.20:
                temp -= 0.05

        # We found the solution!
        # Return it and the number of iterations it took to get there
        return current, num_iterations
```

There's two functions we haven't defined yet that are used above. These are `get_random_neighbour` and `cost_of_state`.

```python
    def get_random_neighbour(state):
        neighbour = [house[:] for house in state] # Deep copy

        i, j = random.sample(xrange(5), 2)
        attr_idx = random.randint(0, 4)

        neighbour[i][attr_idx], neighbour[j][attr_idx] =
            neighbour[j][attr_idx], neighbour[i][attr_idx]

        return neighbour
```

We first deepcopy the current state since we don't want to mutate the current state when determining the neighbour state. We then pick two houses to swap (`i` and `j`) and an `attr_index` (one of NAT, COL, ANI, BEV, and CIG, the constants we defined earlier). Finally, for the two houses picked, we swap their attribute choices for the corresponding attribute and return the neighbour state.

We now need to define the `cost_of_state` function, which when given a state returns its cost. As mentioned earlier, it will be the number of clues that are not satisfied by the state.

```python
    def cost_of_state(state):
        cost = 15
        for i, h in enumerate(state):
            cost -= sum([
                h[NAT] == 'brit' and h[COL] == 'red',
                h[NAT] == 'swede' and h[ANI] == 'dog',
                h[NAT] == 'dane' and h[BEV] == 'tea',
                i < 4 and h[COL] == 'green' and state[i+1][COL] == 'white',
                h[COL] == 'green' and h[BEV] == 'coffee',
                h[CIG] == 'pall mall' and h[ANI] == 'bird',
                h[COL] == 'yellow' and h[CIG] == 'dunhill',
                i == 2 and h[BEV] == 'milk',
                i == 0 and h[NAT] == 'norwegian',
                h[CIG] == 'blends' and ((i > 0 and state[i-1][ANI] == 'cat')
                                     or (i < 4 and state[i+1][ANI] == 'cat')),
                h[ANI] == 'horse' and ((i > 0 and state[i-1][CIG] == 'dunhill')
                                     or (i < 4 and state[i+1][CIG] == 'dunhill')),
                h[CIG] == 'blue master' and h[BEV] == 'root beer',
                h[NAT] == 'german' and h[CIG] == 'prince',
                h[NAT] == 'norwegian' and ((i > 0 and state[i-1][COL] == 'blue')
                                        or (i < 4 and state[i+1][COL] == 'blue')),
                h[CIG] == 'blends' and ((i > 0 and state[i-1][BEV] == 'water')
                                     or (i < 4 and state[i+1][BEV] == 'water')),
            ])

        return cost
```

The above 15 boolean expressions correspond to the 15 clues in the order they were presented at the beginning of this post.  For each house, we check how many clues are satisfied and subtract this total from the current cost. After doing this for each house we have our cost for the state.

Finally we have everything we need to run the simulated annealing technique.

```python
    random.seed(100)

    solution, iterations = sa(initial)

    for house in solution:
        print house

    print 'Number of iterations:', iterations
```

We use a seed value of 100 for the random number generator so we can produce the same results over and over. The output of the above is:

```python
    ['norwegian', 'yellow', 'cat', 'water', 'dunhill']
    ['dane', 'blue', 'horse', 'tea', 'blends']
    ['brit', 'red', 'bird', 'milk', 'pall mall']
    ['german', 'green', 'fish', 'coffee', 'prince']
    ['swede', 'white', 'dog', 'root beer', 'blue master']
    Number of iterations: 9870
```

We found the solution! In 9870 iterations of simulated annealing, we found that the German has the fish in the fourth house. We only had to look at about 0.00004% of the possibilities to find the solution. If you'd like to review the code in full, it can be found [here](https://gist.github.com/agnanachandran/1cdb49e8360d4c1ac8bea877c252aed3).

Although taking the time to code this may have taken longer than to solve the problem by hand, this technique can be applied to many other problems, most notably, the [Travelling Salesman Problem](https://en.wikipedia.org/wiki/Travelling_salesman_problem) in which we would swap cities instead. The only things we would need to change are the state representation, the neighbouring state selection, and the cost function. The technique itself is generally applicable to all sorts of problems.

### Final Notes

It's worth noting that simulated annealing has many tunable parameters (initial temperature, temperature reduction function, stopping conditions, acceptance probability function). If these are changed, the number of iterations taken to find the solution can vary drastically. In my tests, I saw as many as a million iterations and as few as 200 iterations to converge to the solution. Choosing the parameters wisely is part of the art of making simulated annealing performant.

Logic problems can be often be solved in a variety of ways. Doing it this way allows us to do very little thinking with regards to the clues and how they all relate to each other. We let the computer do the work for us.

The applications of techniques like this are of course not limited to logic puzzles like this. Simulated annealing in particular can be used for circuit board placement, physics simulations, and structural optimization. Artificial intelligence techniques in general have wide-reaching applications and implications. Learning about different techniques is both interesting and valuable, if only for solving fun logic puzzles like this one.
