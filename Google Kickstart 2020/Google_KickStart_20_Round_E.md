# Google Kickstart 2020 Round E

## [Problem 2: High Buildings](https://codingcompetitions.withgoogle.com/kickstart/round/000000000019ff47/00000000003bef73)

### The problem:

> In an unspecified country, Google has an office campus consisting of **N** office buildings in a line, numbered from 1 to N from left to right. When represented in meters, the height of each building is an integer between 1 to **N**, inclusive.

> Andre and Sule are two Google employees working in this campus. On their lunch break, they wanted to see the skyline of the campus they are working in. Therefore, Andre went to the leftmost point of the campus (to the left of building 1), looking towards the rightmost point of the campus (to the right of building **N**). Similarly, Sule went to the rightmost point of the campus, looking towards the leftmost point of the campus.

> To Andre, a building **x** is visible if and only if there is no building to the left of building **x** that is strictly higher than building **x**. Similarly, to Sule, a building **x** is visible if and only if there is no building to the right of building x that is strictly higher than building **x**.

> Andre learned that there are A buildings that are visible to him, while Sule learned that there are **B** buildings that are visible to him. After they regrouped and exchanged information, they also learned that there are **C** buildings that are visible to both of them.

> They are wondering about the height of each building. They are giving you the value of **N**, **A**, **B**, and **C** for your information. As their friend, you would like to construct a possible height for each building such that the information learned on the previous paragraph is correct, or indicate that there is no possible height construction that matches the information learned (thus at least one of them must have been mistaken).

> **Output**
For each test case, output one line containing Case #x: y, where x is the test case number (starting from 1) and y is IMPOSSIBLE if there is no possible height for each building according to the above information, or N space-separated integers otherwise. The i-th integer in y must be the height of the i-th building (in meters) between 1 to **N**.

> **Limits**
Number of test cases will be **T**
Time limit: 20 seconds per test set.
Memory limit: 1GB.
1 ≤ T ≤ 100.
1 ≤ C ≤ N.
C ≤ A ≤ N.
C ≤ B ≤ N.

> Test Set 1
1 ≤ N ≤ 5.

> Test Set 2
1 ≤ N ≤ 100.




### Understanding the problem:

From reading the problem we see that we are given 3 variables
- **A**: Number of buildings Andre can see from left to right
- **B**: Number of buildings Sule can see from right to left
- **C**: Number of buildings both Andre and Sule can see
- **N**: The total number of buildings

An important note from the problem of how do Andre and Sule count the buildings is that, the building will be count if there are not buildings with a greater height than it from Andre's (left to right) or Sule (right to left line of sight).


#### Example 1 (Building counting):

```txt
                    +--+
                    |  |
                    +--+
          +--+ +--+ +--+ +--+      +--+
          |  | |  | |  | |  |      |  |
          +--+ +--+ +--+ +--+      +--+
     +--+ +--+ +--+ +--+ +--+ +--+ +--+ +--+
     |  | |  | |  | |  | |  | |  | |  | |  |
A    +--+ +--+ +--+ +--+ +--+ +--+ +--+ +--+    S
      1    2    3    4    5    6    7    8
```

Andre can see buildings 1, 2, 3, 4. Notice that building 3 is not obscured by building 2, as they are the same height, it remains visible. All other buildings to the right are not visible due to 4 being taller than them.

Sule can see buildings, 8, 7, 5, 4. Building 6 is smaller than building 7 which will make it not visible. All other buildings to the left are not visible due to 4 being taller than them.

Andre and Sule can both see building 4.

We will have that:

**A = 4** (1, 2, 3, 4)
**B = 4** (8, 7, 5, 4)
**C = 1** (4)

#### Example 2 (Building counting):

```txt     
     +--+           +--+
     |  |           |  |
     +--+           +--+
     +--+ +--+ +--+ +--+ +--+      +--+
     |  | |  | |  | |  | |  |      |  |
     +--+ +--+ +--+ +--+ +--+      +--+
     +--+ +--+ +--+ +--+ +--+ +--+ +--+ +--+
     |  | |  | |  | |  | |  | |  | |  | |  |
A    +--+ +--+ +--+ +--+ +--+ +--+ +--+ +--+    S
      1    2    3    4    5    6    7    8
```

In this example  Andre can only see 2 buildings, 1 and 4 and Sule can see 8, 7, 5, 4, 1. Both of them can see building 1, 4.

We will have that:

**A = 4** (1)
**B = 4** (8, 7, 5, 4, 1)
**C = 1** (1, 4)

### What is asked from us?

We are asked, that if we were given, **N**, **A**, **B** and **C**, could we approximate the heights of the building so that the values given to us (in N, A, B, C) would max correctly the heights of the building we estimated.

An important note is that we are not asked to calculate the real heights of the builds nor are we requested to have the correct order of the heights of the buildings. We are only asked to provide a possible scenario of buildings and their heights that when **N**, **A**, **B** and **C** are calculated they will result in the same values.



### How to approach the problem?



#### Solution #1

We are not giving much information on the buildings, we only have 4 variables that are our answer and we need to recreate a question (the series of building heights) from them that will generate the same answer. If you have done genetic programming you know that in those cases, you know the answer before hand, what you don't know is the path (in our case the question/building heights) to the answer. Genetic programming will start from a randomize set and try and generate a series of answers evolving through time until it reaches the question that provides that answer. We could approach the problem from this angle, we could either program a Genetic Algorithm that will try and generate the answer or start guessing from a random point, using Depth First Search(DFS) until we reach the answer. 

At this point Genetic programming seems like a good way to go, and most likely some people will immediately discard using DFS. In my experience normally [Occam's razor](https://en.wikipedia.org/wiki/Occam%27s_razor) will most of the time succeed, unless there is some constraint that we can see which would remove that solution from our possible solution set. 

Let's start with the simple approach DFS for solving *Test Set #1*. Using DFS for *Test Set#2* most likely won't work because of the number of possible combinations is a huge number tan can reach to $100^{100}$. In *Test Set #1* we have that the maximum possible combinations is $5^{5}=3125$ which is a reasonable number in which we can test out our DFS Algorithm. 

One important topic to mention when doing any large search problem where we can hit already explored solutions, is memorization. This is just a dictionary containing an unique ID of the solution and it's answer. In our case we use the a string representing the heights of the buildings as the ID and we recorded if we have explored this solution previously and return that answer. In our case if we have seen it it means it did not work as if it did, we would have already return an successful answer.

We start just by having a structure to hold the information of our problem:

```c++
struct ProblemInfo
{
    uint32_t a = 0;
    uint32_t b = 0;
    uint32_t c = 0;
    uint32_t n = 0;
};
```

We now create a function that will check the heights of the building and compare them to our Problem info, if **A**, **B** and **C** matches we found the answer:

```c++
bool CheckABC(const std::vector<uint32_t>& buildings, const ProblemInfo& info)
{
    uint32_t visible_a = 0;
    uint32_t maxH_a = buildings[0];
    std::unordered_set<int32_t> buildings_a;

    int32_t size = static_cast<int32_t>(buildings.size());
    for (int32_t i = 0; i < size; ++i)
    {
        if (buildings[i] >= maxH_a)
        {
            maxH_a = std::max(buildings[i], maxH_a);
            ++visible_a;
            buildings_a.insert(i);
        }
    }

    uint32_t visible_b = 0;
    uint32_t maxH_b = buildings[size - 1];
    uint32_t num_intersect = 0;

    for (int32_t i = size - 1; i >= 0; --i)
    {
        if (buildings[i] >= maxH_b)
        {
            maxH_b = std::max(buildings[i], maxH_b);
            ++visible_b;
            if (buildings_a.count(i) != 0)
            {
                num_intersect++;
            }
        }
    }

    return (info.a == visible_a && info.b == visible_b && info.c == num_intersect);
}
```

Next we create our entry function which will call our recursive function for DFS. Here we will insatiate the memorization dictionary, the initial building configuration and the a variable where to hold the answer for the building heights. Notice that the *answer* variable is an empty array, if at the end we still have an empty array that means that the configuration given in the problem is not possible.

```c++
std::vector<uint32_t> GetHeightBuildings(const ProblemInfo& info)
{
    std::unordered_map<std::string, bool> memo;
    std::vector<uint32_t> buildings(info.n, 1);
    std::vector<uint32_t> answer;

    GetHeightBuildingsDFS(buildings, info, memo, answer);

    return answer;
}
```

And finally our DFS Function to generate the possible configurations and check them until we find a possible answer. The first check this algorithm does, is for the memorization, we can exit early if we have seen this configuration of buildings before as we already know how it will behave. If we haven't seen it than we continue modify the configuration:

```c++
bool GetHeightBuildingsDFS(const std::vector<uint32_t>& buildings, const ProblemInfo& info, std::unordered_map<std::string, bool>& memo, std::vector<uint32_t>& answer)
{
    auto getID = [buildings]() -> std::string {
        std::string id = "";
        for (size_t i = 0; i < buildings.size(); ++i)
        {
            id += std::to_string(buildings[i]);
        }

        return id;
    };

    std::string id = getID();
    if (memo.count(id) != 0)
    {
        return memo[id];
    }

    bool check = CheckABC(buildings, info);
    memo[id] = check;
    if (check)
    {
        answer = buildings;
        return true;
    }

    for (size_t i = 0; i < buildings.size(); ++i)
    {
        if (buildings[i]+1 <= buildings.size())
        {
            std::vector<uint32_t> buildings_new_up = buildings;
            buildings_new_up[i]++;
            if (GetHeightBuildingsDFS(buildings_new_up, info, memo, answer))
            {
                return true;
            }
        }

        if (buildings[i]-1 > 0)
        {
            std::vector<uint32_t> buildings_new_down = buildings;
            buildings_new_down[i]--;
            if (GetHeightBuildingsDFS(buildings_new_down, info, memo, answer))
            {
                return true;
            }
        }
    }

    return false;
}
```

##### Time and Space complexity

###### Time Complexity

We have that we are doing DFS which is: $O(b^{d})$, where $b$ is the number of branches (in our case $N$ branches, different value for the height of each building) and $d$ is the depth ($N$ also in our case, as we can start at 1 and increase to $N$ for each building height), so our complexity is $O(N^{N})$. 

###### Space Complexity

We have space complexity of $O(N)$, as for each time we go to a branch we reserve space for a new set of building configurations.

##### Results from Google Submission

When we run this against the test examples we get the correct answers:

```txt
3

4 1 3 1
**Case #1: 4 1 2 1**

4 4 4 3
**Case #2: IMPOSSIBLE**

5 3 3 2
**Case #3: 1 2 1 2 1**
```

We can see that in *Case #1*, Andre can only see 1 building #1 that has a height of 4, as the rest are obscure by that building. Sule can see 3 buildings (#1, #3, #4) as #2 is obscured by #3, and the both see only 1 same building (#1). 

*Case #2* is impossible, because they are only 4 buildings, Andre and Sule can see all of them but we are  told that the only 3 are the same which Andre and Sule see. In this case the result is *impossible*.

*Case #3*, we have 5 buildings, Andre can see 3 buildings, #1, #2, #4 (#3 and #5 are obscured by #2 ). Sule can see also 3 buildings, #5, #4, #2 (#3 and #1 are obscured by #4). The buildings which they both can see are #2 and #4. 

The Google Submission platform accepts our answer and it works for *Test Sase 1*, but it gives us a *runtime error* on case #2. This is cause by a stack overflow, which can be easily seen if you increase the number of buildings to 40. So this solution will work for a very small number of buildings but it won't work as soon as we try to increase to 7 buildings as we will start running into 2 problem, it takes a long time or as we saw, we have a stack overflow.



##### Takeaway

- If we would have gone directly with Genetic programming, we would have wasted a lot of time implementing the algorithm and would have run into the problem that it will also take a long time, more than the 20 seconds we were given as a limit for Test Set #2. For most of these problems as soon as the code starts getting big and complex, that is a **red flag**, the solution that is being programmed is likely not correct.
- If we think about a recursive algorithm we must take into account the limits set in the problem. We can see from the very beginning that for Test Set 2 we have: $1 \leq N \leq 100$.  Which means generating possible answers will most likely not work as the solution space is huge ($N^{N}$ possible combinations).
- Always think about Occam's razor.



#### Solution #2

We need to think of a proper way to solve the problem. A big mistake is to jump directly to the code and start trying to solve the problem there. For this problem that will not work, one may think about the *Solution #1* approach but as seen it will not be efficient enough.

We need to write down different test cases, think about how we solve them, what do we find in common that can be use to generate an algorithm. We start doing these test we can think of and examine them and find out how they compare and what can we find out that will help us solve them.

##### Part #1

We begin by looking at examples and seeing how the variables that we are given behave. 

Let's take *example #1: 4 1 3 2* :
- We have that $N=4$, $A=1$, $B=3$, $C=2$
- We check how $A$, $B$, $C$ correlate to $N$
  - We know we have 4 buildings ($N$)
  - Andre can see only 1 building ($A$)
  - Sule can see 3 buildings ($B$)
  - They both can see 1 building in common ($C$)
  - We have that $A + B = TotalBuildingsSeen$
  - If we were to substract $C$ to all the building seen we should in theory get a number that is equal or less than $N$: $TotalBuildingsSeen- C \le N$
  - If we were to go over that, than something must be wrong, because how can we see more buildings than what there actually are. We could see less because some may be hidden, but we can't see more.
  - Let's for now assume that $A + B - C \le N$ should always hold true

Now we take *example #2: 4 4 4 3*. We know that for this one we don't have a possible solution, lets check our assumption from before:
- $A + B - C \le N$
  - $4 + 4 - 3 \le 4$
  - $8 - 3 \le 4$
  - $5 \le 4$ this turns out to be **false** as expected

Now for the third example: *5 3 3 2*:
- $A + B - C \le N$
  - $3 + 3 - 2 \le 5$
  - $6 - 2 \le 5$
  - $4 \le 5$ this turns out to be **true** as expected

So far this pattern seems to hold.  Let's come up with some other examples to check it out. *If you asked, wait, what if $C=0$? We don't worry about this because one of the constraints is that $C$ is always between 1 and $N$ and less or equal to $A$ and $B$. Remember to keep the constraints in mind always.*

- 6 3 3 2 (has an answer):
  - $3 + 3 - 2 \le 6$
  - $4 \le 6$, is *true*

- 5 5 4 1 (not possible)
  - $5 + 4 - 1 \le 5$
  - $8 \le 5$, is *false*

- 6 3 3 1 (has an answer)
  - $3 + 3 - 1 \le 6$
  - $5 \le 6$, is *true*

- 6 3 3 3 (has an answer)
  - $3 + 3 - 3 \le 6$
  - $3 \le 6$, is *true*

- 6 3 4 1 (has an answer)
  - $3 + 4 - 1 \le 6$
  - $6 \le 6$, is *true*

- 20 10 15 6 (has an answer)
  - $10 + 15 - 6 \le 20$
  - $19 \le 20$, is *true*

- 1 1 1 1 (has an answer)
  - $1 + 1 - 1 \le 1$
  - $1 \le 1$, is *true*

- 3 1 1 1 (this one seems tricky)
  - $1 + 1 - 1 \le 1$
  - $1 \le 3$, mmm but how?
    - If they both see the same build one of them must be looking 2 buildings at least. 
  - What if we have: 5 1 1 1, we will see the same issue, and we can see it also for any problem of $N$ size
  - Seem that $A$ and $B$ can't be equal to 1 at the same time, the only possible way is when $N = 1$
  - So we revise our assumption and say $A + B - C \le N$ hold true when $N\ge2$ and either $A \ge 2$ or $B \ge 2$ but $A$ and $B$ can both only be $1$ when $N = 1$
  - We can also add that, if we ever get a $A + B - C = 1$ when $N \ge 2$ we have a question that does not have a answer. 
  - If you look at the formula the only way to get a $1$ with our constraints, is that both $A$ and $B$ are $1$.
    - Remember $C$ always needs to be $C \le A$ and $C \le B$, which means that $C$ will always take smaller value, i.e. $C \le \min{(A, B)}$
    - So we can say that $A + B - \min{(A,B)} == \max{(A,B)}$
    - Which shows that to get a $1$ for that equation, we need both $A$ and $B$ to be $1$.


We now know that we can detect when we have a valid or invalid problem: We have that $A + B - C \le N$ always except when $N = 1$.

For $N = 1$ is a very simple solution, as we just return 1 building with any height. For our exception we have a quick solution.



##### Part #2

Next lets try to grab a low hanging fruit, what if both Andre and Sule can see all the buildings, this means that $A = N$, $B = N$, $C = N$, which translates to $A + B - C = N$, in this case we have a fast and easy return value, fill the heights with the same number and return.



##### Part #3

Our next low hanging fruit would be, what if they only share 1 building in common, so $C = 1$.

Let's do some examples



1. $N=6$, $A=3$, $B=3$, $C=1$

Possible Buildings:
```txt     
                    +--+
                    |  |
                    +--+
          +--+      +--+ +--+
          |  |      |  | |  |
          +--+      +--+ +--+
     +--+ +--+ +--+ +--+ +--+ +--+
     |  | |  | |  | |  | |  | |  |
A    +--+ +--+ +--+ +--+ +--+ +--+   S
      1    2    3    4    5    6  
```
- We know that $A = 3$ and $B = 3$
- We see that from Andre' side he must have 1 obstructed building
- The obstructed building could have been in Sule's side and Andre won't had any builds obstructed. Either configuration is valid.
- We need to hide 1 building from both of them
- Check our variables, $A + B - C \le N$
  - $3 + 3 - 1 \le 6$
  - $6 - 1 \le 6$
  - $5 \le 6$
- We have a $5$ and which if we will substract from $N$ will equal to the number of hidden buildings $N - (A + B - C) = H$, $H$ for hidden buildings
-  Let's see other examples



2. $N=6$, $A=3$, $B=2$, $C=1$

Possible Buildings:
```txt
                    +--+
                    |  |
                    +--+
          +--+      +--+      +--+
          |  |      |  |      |  |
          +--+      +--+      +--+
     +--+ +--+ +--+ +--+ +--+ +--+
     |  | |  | |  | |  | |  | |  |
A    +--+ +--+ +--+ +--+ +--+ +--+   S
      1    2    3    4    5    6  
```
- In this example we can also see that if we were to move hidden building #3 and exhange it with building #4, our configuration will remain valid.
- So it seems that the hidden buildings should not matter if they are on the left or on the right.
- Using the formula above:  $N - (A + B - C) = H$
  - $6 - (3 + 2 - 1) = H$
  - $6 - (5 - 1) = H$
  - $6 - 4 = H$
  - $2 = H$
-  We get $H = 2$ the number of hidden buildings we need.
- Lets try another one.



3. $N=5$, $A=3$, $B=3$, $C=1$

Possible Buildings:
```txt
               +--+
               |  |
               +--+
          +--+ +--+ +--+
          |  | |  | |  |
          +--+ +--+ +--+     
     +--+ +--+ +--+ +--+ +--+
     |  | |  | |  | |  | |  |
A    +--+ +--+ +--+ +--+ +--+   S
      1    2    3    4    5  
```
- In this example we have no hidden buildings- 
- Using the formula above:  $N - (A + B - C) = H$
  - $5 - (3 + 3 - 1) = H$
  - $5 - (6 - 1) = H$
  - $5 - 5 = H$
  - $0 = H$
- We get $H = 0$ the number of hidden buildings we need.
- It seems the formula is holding to what we thought.
- Another one.



4. $N=7$, $A=1$, $B=2$, $C=1$

Possible Buildings:
```txt
                                  +--+
                                  |  |
                                  +--+
    +--+                          +--+
    |  |                          |  |
    +--+                          +--+
    +--+ +--+ +--+ +--+ +--+ +--+ +--+
    |  | |  | |  | |  | |  | |  | |  |
A   +--+ +--+ +--+ +--+ +--+ +--+ +--+  S
     1    2    3    4    5    6    7  
```
- All the 5 hidden buildings are in the middle
- Using the formula above:  $N - (A + B - C) = H$
  - $7 - (2 + 1 - 1) = H$
  - $7 - (3 - 1) = H$
  - $7 - 2 = H$
  - $5 = H$
- We get $H = 5$ the number of hidden buildings we need and it is what we expected.


5. $N=2$, $A=1$, $B=2$, $C=1$

Possible Buildings:
```txt
     +--+     
     |  |     
     +--+     
     +--+ +--+
     |  | |  |
A    +--+ +--+  S
      1    2  
```
- No hidden buildings
- If we have $N=2$ is expected not to have hidden buildings
- Using the formula above:  $N - (A + B - C) = H$
  - $2 - (1 + 2 - 1) = H$
  - $2 - (3 - 1) = H$
  - $2 - 2 = H$
  - $0 = H$
- Seems to work what if all buildings in $N=2$ can be seen


6. $N=2$, $A=2$, $B=2$, $C=2$

Possible Buildings:
```txt
     +--+ +--+    
     |  | |  |    
     +--+ +--+    
     +--+ +--+
     |  | |  |
A    +--+ +--+  S
      1    2  
```
- No hidden buildings
- Again, if we have $N=2$ is expected not to have hidden buildings
- Using the formula above:  $N - (A + B - C) = H$
  - $2 - (2 + 2 - 2) = H$
  - $2 - (4 - 2) = H$
  - $2 - 2 = H$
  - $0 = H$
- Seems to work what if all buildings in $N=2$ can be seen


We can conclude the following for $C=1$
- The hidden building positions don't matter as long as there are on either side and not seen by Andre or Sule
- Using the formula $N - (A + B - C) = H$ we can get the number of hidden buildings
- If the positions of the hidden buildings don't matter (as long as they are hidden), we could:
  - Set all the buildings for Andre first
  - Next add the hidden buildings, which can be inserted after the first building from Andre. That way we hide them between the first building Andre see's and any building that is seen by Sule or the second building that Andre can see.
  - Next the Common buildings
  - Finally the ones for Sule.

In theory if we are given the following: $N=5$, $A=2$, $B=3$, $C=1$:
- We get our $H$. $N - (A + B - C) = H$
  - $H = 5 - (2 + 3 - 1)$
  - $H = 5 - (5 - 1)$
  - $H = 5 - 4$
  - $H = 1$
- Our height, according to the expected output, has to be less or equal to N. For any problem that is not $N \ge 3$ we can select the heighs easily:
  - 1 for Hidden Buildings
  - 2 for Buildings seen by Andre or Sule
  - 3 for Common seen buildings.
- If we have a problem with $N = 2$ we know we have no hidden buildings so:
  - 1 for Buildings seen by Andre or Sule
  - 2 for Common seen buildings.
  - Also there are only 3 combinations that $N=2$ can have:
    - Both buildings are seen by Andre a Sule, $Buildings=(2, 2)$
    - Andre can see 2 buildings and Sule 1, $Buildings=(1, 2)$
    - Sule can see 2 buildings and Andre 1, $Buildings=(2, 1)$
    - This could be just done with simple if statements and returning predefined building heights.
- If we see a problem with $N = 1$ we just need to return $1$ as we only have 1 building and 1 would be the only choice for the height.
- For all others, set $A-C$ buildings at the beginning of the vector at height $X$:
  - One important aspect we can see here is the question: *what if $A-C$ is equal to 0.*
  - This means we are setting the hidden buildings in Andre's sight and we don't want that.
  - If $A-C == 0$ we could move this step to after setting the common buildings, so they are hidden between the common and Sule's buildings. 
```txt
    +--+                     
    |  |                     
    +--+                     
    +--+  
    |  |  
A   +--+                      S
     1    2    3    4    5   
```
- Next we insert our $C$ common buildings, 1 in this case, at height $Y$:
```txt
         +--+               
         |  |               
         +--+           
    +--+ +--+                     
    |  | |  |               
    +--+ +--+               
    +--+ +--+
    |  | |  | 
A   +--+ +--+                S
     1    2    3    4    5   
```
- Next insert $B-C$ buildings at height $X$:
```txt
         +--+               
         |  |               
         +--+           
    +--+ +--+ +--+ +--+                     
    |  | |  | |  | |  |             
    +--+ +--+ +--+ +--+             
    +--+ +--+ +--+ +--+
    |  | |  | |  | |  |
A   +--+ +--+ +--+ +--+      S
     1    2    3    4    5   
```
- Add the hidden buildings between building #1 and #2
```txt
              +--+
              |  |
              +--+
    +--+      +--+ +--+ +--+
    |  |      |  | |  | |  |
    +--+      +--+ +--+ +--+
    +--+ +--+ +--+ +--+ +--+
    |  | |  | |  | |  | |  | 
A   +--+ +--+ +--+ +--+ +--+  S
     1    2    3    4    5   
```
- And we have a correct solution. Andre can see 2 buildings, Sule can see 3 and the see in common 1.



##### Part #4

Now we come to the last part what if $C \ge 2$. In this case hidden buildings can be in between the common seen buildings or maybe before them. Let's do what we have done and check some examples.


1. $N=6$, $A=3$, $B=4$, $C=2$

Possible Buildings:
```txt     
          +--+      +--+
          |  |      |  |
          +--+      +--+
          +--+      +--+ +--+
          |  |      |  | |  |
          +--+      +--+ +--+
     +--+ +--+ +--+ +--+ +--+ +--+
     |  | |  | |  | |  | |  | |  |
A    +--+ +--+ +--+ +--+ +--+ +--+   S
      1    2    3    4    5    6  
```
- Taking a look at the formula we saw in the last section $N - (A + B - C) = H$, it seems to calculate also correctly the number of hidden buildings
  - $N - (A + B - C) = H$
  - $6 - (3 + 4 - 2) = H$
  - $6 - (7 - 2) = H$
  - $6 - 5 = H$
  - $1 = H$
- If building #1 is moved to height 2 we could exchange building #2 and #3 and still have a valid configuration.
- From this example if we were to apply the algorithm from last part to solve it, it seems it will work.
- Let's see another example if we could do this:



2. $N=5$, $A=4$, $B=3$, $C=2$

Possible Buildings:
```txt
               +--+ +--+
               |  | |  |
               +--+ +--+
          +--+ +--+ +--+ +--+
          |  | |  | |  | |  |
          +--+ +--+ +--+ +--+
     +--+ +--+ +--+ +--+ +--+
     |  | |  | |  | |  | |  |
A    +--+ +--+ +--+ +--+ +--+   S
      1    2    3    4    5  
```
- We have no hidden buildings and it is we can check the formula $N - (A + B - C) = H$:
  - $N - (A + B - C) = H$
  - $5 - (4 + 3 - 2) = H$
  - $5 - (7 - 2) = H$
  - $5 - 5 = H$
  - $0 = H$
- Solving this seems similar from last part algorithm



2. $N=7$, $A=4$, $B=4$, $C=3$

Possible Buildings:
```txt
          +--+      +--+      +--+
          |  |      |  |      |  |
          +--+      +--+      +--+
          +--+ +--+ +--+ +--+ +--+ +--+
          |  | |  | |  | |  | |  | |  |
          +--+ +--+ +--+ +--+ +--+ +--+
     +--+ +--+ +--+ +--+ +--+ +--+ +--+
     |  | |  | |  | |  | |  | |  | |  |
A    +--+ +--+ +--+ +--+ +--+ +--+ +--+  S
      1    2    3    4    5    6    7  
```
- We have 2 hidden buildings and it is we can check the formula $N - (A + B - C) = H$:
  - $N - (A + B - C) = H$
  - $7 - (4 + 4 - 3) = H$
  - $7 - (8 - 3) = H$
  - $7 - 5 = H$
  - $2 = H$
- If we were to solve this one with the algorithm from last part:
  - Add $A-C$ buildings at height 2
  - Add $C$ buildings at height 3  
  - Add $B-C$ buildings at height 2
  - Add the $H$ hidden buildings between position 1 and 2
- We would solve it:
```txt
                    +--+ +--+ +--+
                    |  | |  | |  |
                    +--+ +--+ +--+
     +--+           +--+ +--+ +--+ +--+
     |  |           |  | |  | |  | |  |
     +--+           +--+ +--+ +--+ +--+
     +--+ +--+ +--+ +--+ +--+ +--+ +--+
     |  | |  | |  | |  | |  | |  | |  |
A    +--+ +--+ +--+ +--+ +--+ +--+ +--+  S
      1    2    3    4    5    6    7  
```



##### Time to Code

Now that we have examine the problem by doing examples and we've gain a better understanding we saw that it can be easily solvable and with much easier to code than Genetic Algorithm or DFS.

Let's continue to use the structure that contains the problem's information

```c++
struct ProblemInfo
{
    int32_t n = 0;
    int32_t a = 0;
    int32_t b = 0;
    int32_t c = 0;
};
```



We add our main function. We take into account the following:
- The formula to know if a problem is solvable or not $A + B - C \le N$ and $A + B - C \ne 1 \; \forall \; N \ge 2$ 
- If we have $N = 1$ just return $\{1\}$ as the building height.
- If we have $N = 2$ predefined the returns as we saw before.
- For the rest $N \gt 2$ we do the algorithm we saw in the last parts.

```c++
std::vector<int32_t> GetHighBuildings(const ProblemInfo& info)
{
    // A + B - C <= N and A + B - C != 1 for N >= 2
    int32_t formulaRes = info.a + info.b - info.c;
    bool notSolvable = formulaRes > info.n || (formulaRes == 1 && info.n >= 2);
    if (notSolvable)
    {
        return {};
    }

    //Easy, just 1 building
    if (info.n == 1)
    {
        return {1};
    }

    //Return prefedined building heights
    if (info.n == 2)
    {
        if (info.c == 2)
        {
            return { 2, 2 };
        }
        else if (info.a == 2)
        {
            return { 1, 2 };
        }
        else if (info.b == 2)
        {
            return { 2, 1 };
        }
        else
        {
            return {};
        }
    }


    std::vector<int32_t> answer;
    answer.reserve(info.n);

    //Push Buildings A
    for (int32_t i = 0; i < info.a - info.c; i++)
    {
        answer.push_back(2);
    }

    //Push Common Buildings
    for (int32_t i = 0; i < info.c; i++)
    {
        answer.push_back(3);
    }

    //Push Buildings B
    for (int32_t i = 0; i < info.b - info.c; i++)
    {
        answer.push_back(2);
    }

    //Push Hidden Buildings
    int32_t hiddenBuildings = info.n - formulaRes;
    if (hiddenBuildings > 0)
    {
        answer.insert(answer.begin() + 1, hiddenBuildings, 1);
    }

    return answer;
}
```



##### Time and Space complexity

###### Time Complexity

We can easily see that we have a linear time complexity of $O(N)$. If we check we see we only add items an $N$ number of times: $(A-C) + (B-C) + C + (N-(A+B-C)) = N$

###### Space Complexity

We have space complexity of $O(N)$, as we create a vector for N building heights to hold our answer.

##### Results from Google Submission

This code passes both test sets from Google.

##### Takeaway

- Always do examples by hand first and look at how those example behave and how can you figure out the algorithm.
- Code last because it is actually the easy part.
- Think of edge cases and how would the examples behave.
- As soon as something starts getting complicated from the code side  (for these types of problems), examine if you might be heading down the wrong path.