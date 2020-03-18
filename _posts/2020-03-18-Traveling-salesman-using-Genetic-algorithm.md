---
published: true
---
---
published: true
---
# A Genetic algorithm to solve Traveling salesman problem 

Hi Everyone,
Most of you might heard about Traveling salesman problem in mathematics.

For those who don't know What TSP is,

**Problem description:** There are N-cities say C1,C2,C3 and so on.
distance between two cities is given as C12=d1,C13=d2,C34=f2.... and so on.
There is a salesman who want to do buisness in all cities. For that he has to travel in all cities for once and only once. Task is to find the path which has lowest total distance.

There is lot of classical solution availaible for this problem. But here I want to try a diffrent aproach i.e Genetic algorithm.

**Genetic Algorithm** is an algorithm inspired from natural selection to find the optimum solution from a pool of many solutions. Basically it's a searching algorithm.

There are following steps involved in a genetic algorithm:

- create  random population of possible solutions
- find the fitness of each soltuion based on task or goal
- arrange all solutions according to their fitness
- cross-over two fit solutions together to produce new generation
- Mutate one gene randomly with random probability
- repeate until we found desired solution

The GA i will use will work just like above algorithm

First I will map cities with number. For ex if we have 5 cities i named them 0,1,2,3,4

Now any route which include all cities is a valid solution. e.g  \[0,3,4,1,2] is a valid solution.

I will start with only ten random generated routes.

Next I will find the fitness of each route and arrange them in increasing order. As we want to minimize the total length ... fitness = reciprocal of total distance. i.e low distance means high fitness.

Next I will choose five top pairs for crossover and generate next generation 
I will repeat this, with mutation, to produce 300000 generation and see if the 300000th generation has solution in it or not

I will use python and have Three files.

city.py
```python
import math
class City:
    def __init__(self,x,y):
        self.x=x;
        self.y=y;

    def getDistance(self,x2,y2):
        dist=math.sqrt((self.x-x2)**2 + (self.y-y2)**2)
        return dist
```

geneticAlgo.py
```python
import random
import time
import city
def generateRandomPop(cityNum,popSize):
    pops=[]
    random.seed(time.time())
    while len(pops)!=popSize:
            cpop=list(range(cityNum))
            random.shuffle(cpop)
            pops.append(cpop)
    return pops

def fitnessCalc(gnome,area):
    totalDistance=0
    # print(gnome)
    for i in range(0,len(gnome)-1):
        # print(area[gnome[i]].x,area[gnome[i+1]].x)
        dis=area[gnome[i]].getDistance(area[gnome[i+1]].x,area[gnome[i+1]].y)
        totalDistance=totalDistance+dis
    #print(totalDistance)
    return 1/totalDistance

def crossover(gnome1,gnome2):
    mid=len(gnome1)/2
    repeatcheck=[0]*len(gnome1)
    child1=[]
    child2=[]
    # print(gnome1,gnome2)
    for i in range(0,len(gnome1)):
        # print(i)
        if i <= mid:
            child1.append(gnome1[i])
            repeatcheck[gnome1[i]]=1
        else:
            if repeatcheck[gnome2[i]] !=1:
                child1.append(gnome2[i])
                repeatcheck[gnome2[i]]=1
    # print(repeatcheck)
    n=0
    for k in repeatcheck:
        if k==0:
            child1.append(n)
        n=n+1
    repeatcheck=[0]*len(gnome2)
    for i in range(0,len(gnome2)):
        if i <= mid:
            child2.append(gnome2[i])
            repeatcheck[gnome2[i]]=1
        else:
            if repeatcheck[gnome1[i]] !=1:
                child2.append(gnome1[i])
                repeatcheck[gnome1[i]]=1
    n=0
    for k in repeatcheck:
        if k==0:
            child2.append(n)
        n=n+1
    # print(child1,child2)
    return child1,child2

def generateNewPop(crossoverPop,mutationRate,mutationCount):
    newpop={}
    mutation=False
    newpop[0],newpop[1]=crossover(crossoverPop[0],crossoverPop[1])
    newpop[2],newpop[3]=crossover(crossoverPop[0],crossoverPop[2])
    newpop[4],newpop[5]=crossover(crossoverPop[1],crossoverPop[2])
    newpop[6],newpop[7]=crossover(crossoverPop[2],crossoverPop[3])
    newpop[8],newpop[9]=crossover(crossoverPop[3],crossoverPop[4])
    random.seed(time.time())
    # print(mutationRate*float(mutationCount))

    if mutationRate*float(mutationCount)==float(1):
        # print("doing mutation")
        i=random.randint(0,9)
        j=random.randint(0,len(crossoverPop[0])-1)
        k=random.randint(0,len(crossoverPop[0])-1)
        newpop[i][j],newpop[i][k]=newpop[i][k],newpop[i][j]
        mutation=True
    return newpop,mutation

```

main.py
```python
import city
import random
import geneticAlgo


city1=city.City(10,20)
city2=city.City(12,8)
city3=city.City(20,15)
city4=city.City(-20,12)
city5=city.City(-20,-20)
area=[city1,city2,city3,city4,city5]
n=len(area)
mutationCount=0

currentPop=geneticAlgo.generateRandomPop(5,10)
currentPopFitness={}
for i in range(0,len(currentPop)):
    currentPopFitness[i]=geneticAlgo.fitnessCalc(currentPop[i],area)
print(currentPopFitness)
sortedPop={k: v for k, v in sorted(currentPopFitness.items(), key=lambda item: item[1])}
crossoverPop={}
for i ,j in zip(sortedPop, range(0,5)):
    crossoverPop[j]=currentPop[i]

newPopulation,mutationFlag=geneticAlgo.generateNewPop(crossoverPop,0.005,mutationCount)
print(newPopulation)
if mutationFlag:
    mutationCount=0
newpop=[]
for i in newPopulation:
    newpop.append(newPopulation[i])
currentPop=newpop
for kk in range(300000):
    mutationCount=mutationCount+1
    # print(kk)
    currentPopFitness={}
    for i in range(0,len(currentPop)):
        currentPopFitness[i]=geneticAlgo.fitnessCalc(currentPop[i],area)
    # print(currentPopFitness)
    sortedPop={k: v for k, v in sorted(currentPopFitness.items(), key=lambda item: item[1])}
    crossoverPop={}
    for i ,j in zip(sortedPop, range(0,10)):
        if j >= 5:
            crossoverPop[j-5]=currentPop[i]

    newPopulation,mutationFlag=geneticAlgo.generateNewPop(crossoverPop,0.005,mutationCount)
    # print(newPopulation)
    if mutationFlag:
        mutationCount=0
    newpop=[]
    for i in newPopulation:
        newpop.append(newPopulation[i])
    currentPop=newpop
print(currentPop)
currentPopFitness={}
for i in range(0,len(currentPop)):
    currentPopFitness[i]=geneticAlgo.fitnessCalc(currentPop[i],area)
print(currentPopFitness)
sortedPop={k: v for k, v in sorted(currentPopFitness.items(), key=lambda item: item[1])}
print(sortedPop)
```

city ordinates i used is 
- A: 10,20
- B: 12,8
- C: 20,15
- D: -20,12
- E: -20,20

running main.py gives following results

![rs.png]({{site.baseurl}}/_posts/rs.png)



I ran this two times... and as you can see , first one gives solution \[4,3,0,2,1] and second time it gave solution \[1,2,0,3,4] which is same and correct.

Isn't it amazing.... we write an algorithm which find correct solution starting from random solution.
