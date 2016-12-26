---
layout: post
comments: true
categories: Artificial-Intelligence

img: /images/mdvrp.png
abstract: "Applied Genetic Algorithm to solve Multiple Depot Vehicle Routing Problem, which is an extended problem of Travelling Salesman Problem."
tags: artificial-intelligence operations-research TSP multiple-depot-vehicle-route-problem c++ matlab
---

> I partook as one of top 60 students (700 totally) in the Undergraduate Research Program founded by our college. Under my mentor's guidance, I got involved in a cooperative project with electronic power company aiming at devising an efficient and low-cost scheduling proposal to maintain electricity metering devices. I found that Multi-depot Vehicle Routing Problem fits our requirement well. Thus, I conducted research on this topic.

## Introduction

A company may have several depots from which it can serve its customers. If the customers are clustered around depots, then the distribution problem should be modeled as a set of independent VRPs. However, if the customers and the depots are intermingled then a Multi-Depot Vehicle Routing Problem should be solved.

A MDVRP requires the assignment of customers to depots. A fleet of vehicles is based at each depot. Each vehicle originate from one depot, service the customers assigned to that depot, and return to the same depot.

The objective of the problem is to service all customers while minimizing the number of vehicles and travel distance.

We can find below a formal description for the MDVRP:

* **Objective**

The objective is to minimize the vehicle fleet and the sum of travel time, and the total demand of commodities must be served from several depots.

* **Feasibility**

A solution is feasible if each route satisfies the standard VRP constraints and begins and ends at the same depot.

* **Formulation**

The VRP problem is extended to the case wherein we have multiple depots, so we will note the vertex set like ![formula1](./formula1.gif), where ![formula2](./formula2.gif) are the vertex representing the depots. Now, a route ![formula3](./formula3.gif) is defined by ![formula4](./formula4.gif), with ![formula5](./formula5.gif). The cost of a route is calculated like in the case of the standard VRP.

## Implementation

> Having thumbed through substantial papers on VRP, I found that neither the traditional exact solution nor the ordinary heuristic algorithm could satisfy our requirements. Thus, I shifted my focus to metaheuristic like [Tabu Search](https://en.wikipedia.org/wiki/Tabu_search), [Genetic Algorithm](https://en.wikipedia.org/wiki/Genetic_algorithm), [Ant Colony Optimization](https://en.wikipedia.org/wiki/Ant_colony_optimization_algorithms), [Neural Network](https://en.wikipedia.org/wiki/Artificial_neural_network), and so forth. Finally, I proposed a hybrid Genetic Algorithm.

#### Genetic Algorithm

In the field of artificial intelligence, a genetic algorithm (GA) is a search heuristic that mimics the process of natural selection. This heuristic (also sometimes called a metaheuristic) is routinely used to generate useful solutions to optimization and search problems. Genetic algorithms belong to the larger class of evolutionary algorithms (EA), which generate solutions to optimization problems using techniques inspired by natural evolution, such as inheritance, mutation, selection, and crossover.

To get familiar with this algorithm, I calculate out the maximum value of ![formula6](./formula6.gif) based on Genetic Algorithm, where ![formula7](./formula7.gif). The program shown as below runs well and can get the result of `98` at most of time.

{% highlight cpp %}
//this program uses SGA to calculate the maximum value of f(x) = x1^2 + x2^2
#include <iostream>
#include <ctime>
#include <cstdlib>
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <vector>
#define LENGTH 3
using namespace std;

class chromosome
{
public:
	int x1,x2;
	int value;
	double fitness;
	void assign(int a,int b)
	{
		x1 = a;
		x2 = b;
		cal_value();
	}
	void cal_value()
	{
		value = x1 << LENGTH;
		value |= x2;
	}
	void cal_fitness()
	{
		fitness = x1*x1 + x2*x2;
	}
};
double total_fitness = 0;
vector<chromosome> population[2];
vector<chromosome> temp;
int cop_counter = 0;
int selector = 0;
int and1 = ((1 << LENGTH) - 1) << LENGTH;
int and2 = (1 << LENGTH) - 1;
int variation_percent = 2;

int random(int end)
{
	return rand() % (end + 1);
}

void select_population()
{
	int counter = 0;
	int new_sel = (selector + 1) % 2;
	population[new_sel].clear();
	while (counter <= 3)
	{
		int pos = population[selector].size() - 1;
		int r = random(total_fitness);
		int end = population[selector][0].fitness;
		for (int i = 1;i < (int)population[selector].size();i++)
			if (r <= end)
			{
				pos = i - 1;
				break;
			}
			else
				end += population[selector][i].fitness;
		population[new_sel].push_back(population[selector][pos]);
		counter++;
	}
}

void cop(chromosome &temp1, chromosome &temp2)
{
	chromosome new1, new2;
	int pos = random(LENGTH * 2 - 2);//0..LENGTH * 2 - 2 (end position)
	int v1 = temp1.value;
	int v2 = temp2.value;
	int length = LENGTH * 2;
	int num1 = ((1 << pos) - 1) << (length - pos);
	int num2 = ((1 << (length - pos)) - 1);
	int part11 = v1 & num1;
	int part12 = v1 & num2;
	int part21 = v2 & num1;
	int part22 = v2 & num2;
	int new_v1 = part11 | part22;
	int new_v2 = part21 | part12;
	new1.assign((new_v1 & and1) >> LENGTH, new_v1 & and2);
	new2.assign((new_v2 & and1) >> LENGTH, new_v2 & and2);
	temp.push_back(new1);
	temp.push_back(new2);
}

void copulation()
{
	chromosome temp1,temp2;
	int remainder = 4;
	int r1,r2;
	int new_sel = (selector + 1) % 2;

	cop_counter = 0;
	while (remainder)
	{
		r1 = random(remainder-1);
		temp1 = population[new_sel][r1];
		population[new_sel].erase(population[new_sel].begin() + r1);
		remainder--;
		r2 = random(remainder-1);
		temp2 = population[new_sel][r2];
		population[new_sel].erase(population[new_sel].begin() + r2);
		remainder--;
		cop(temp1,temp2);
	}
	population[new_sel] = temp;
	temp.clear();
}

void variation()
{
	int new_sel = (selector + 1) % 2;
	for (int i = 0;i < (int)population[new_sel].size();i++)
	{
		int rand = random(99) + 1;
		if (rand <= 2)
		{
			int pos = random(LENGTH * 2 - 1) + 1;
			int num = 1 << (LENGTH * 2 - pos);
			population[new_sel][i].value ^= num;
			population[new_sel][i].x1 = (population[new_sel][i].value & and1) >> LENGTH;
			population[new_sel][i].x2 = population[new_sel][i].value & and2;
		}
	}
}

void update_fitness()
{
	total_fitness = 0;
	for (int i = 0;i < (int)population[selector].size();i++)
	{
		population[selector][i].cal_fitness();
		total_fitness += population[selector][i].fitness;
	}
}

bool cmp(chromosome a,chromosome b)
{
	return a.fitness < b.fitness;
}

//initial population: 1 6, 2 4, 3 5, 4 1
void init()
{
	srand((unsigned)time(NULL));
	chromosome chr;
	chr.assign(1,6);
	population[0].push_back(chr);
	chr.assign(2,4);
	population[0].push_back(chr);
	chr.assign(3,5);
	population[0].push_back(chr);
	chr.assign(4,1);
	population[0].push_back(chr);
}

//debug
/*int b[100];
char str[100];
int bcounter=0;
void tobinary(int x)
{
	memset(b,0,sizeof(b));
	bcounter=0;
	while (x>0)
	{
		b[bcounter++]=x%2;
		x/=2;
	}
	int i;
	for (i = 0;i < 6;i++)
		str[i]=b[5-i]+'0';
	str[i]='\0';
}*/

int main()
{
	int counter = 0;
	init();
	int maxn = 0;
	int maxvalue;
	while (counter <= 1000)
	{
		update_fitness();
		sort(population[selector].begin(),population[selector].end(),cmp);
		if (population[selector][population[selector].size() - 1].fitness > maxn)
		{
			maxn = population[selector][population[selector].size() - 1].fitness;
			maxvalue = population[selector][population[selector].size() - 1].value;
		}
		select_population();
		copulation();
		variation();
		selector = (selector + 1) % 2;
		counter++;
	}
	printf("x1:%d x2:%d\n",(maxvalue&and1)>>LENGTH,maxvalue&and2);
}

{% endhighlight %}

#### Hybrid Genetic Algorithm

However, simple genetic algorithm always converges to the local optimum too early and can not achieve the best result. Thus, I introduced Local Search into the genetic algorithm and proposed a new way of encoding to improve the solution. After adjusting the parameters in the algorithm for many times, the algorithm fits well to the dataset provided by the company.

* **Genetic Encoding**

I adopted a new encoding method to represent for each solution. A good encoding method has high efficiency in the calculation of fitness.

We represent the solution as following sequence : `(0 depotY0 customerX0 customerX1 … depotY1 customerXk … depotY2 … depotYm-1 …)`. Thus, the visiting order is determined and some illegal orders will be excluded. We can calculate the fitness for each individual by Dynamic Programming based on the visiting order.

* **Calculation of fitness**

The calculation for the fitness of each individual is a complex process. I adopted a partition method, whose basic idea is based on [Dynamic Programming](https://en.wikipedia.org/wiki/Dynamic_programming), to achieve the optimal arrangement of the vehicles referring to this paper-[Prins C. A simple and effective evolutionary algorithm for the vehicle routing problem[J]. Computers & Operations Research, 2004, 31(12): 1985-2002.](http://www.sciencedirect.com/science/article/pii/S0305054803001588). I determine the visiting order of all customers at first and then arrange vehicles for them.

* **Crossover**

I adopted the classic OX crossover. The procedure is shown as below. `P1` and `P2` represent for the parents while `C1` and `C2` represent for the children after crossover. `i` and `j` are the pointer to the spliting position.

![ox](./ox.png)

* **Population Selection**

Roulette is one of the classical method. However, by experiments, I found that this method will create same individuals after several generations and it shrinks the scope of feasible solution. Thus, I abandon roulette and apply a new method. I sort the individuals according to their fitness and choose the individuals in the front part to crossover with each other. The offspring produced by those individuals will replace the individuals in the lower half. I found that this method performs well in the experiment.

* **Local Search**

I apply several methods to conduct neighborhood search, such as 2-opt, exchanging 2 nodes and removing part of the sequence then insering it into another position. These methods are applied to escape the local optimum. In order to balance the quality and efficiency, these operators should be considered carefully since more of them there are, the lower efficiency our algorithm has.

* **Program Framework**

{% highlight cpp%}
while (generation <= maxGeneration)
	update_fitness();
	sort(population);
	preserve_best_solution();
	crossover();
	mutation_localSearch ();
	generation++;
endwhile
{% endhighlight %}

#### Experiments

I compare my results with the international standard dataset provided by [http://www.bernabe.dorronsoro.es/vrp/](http://www.bernabe.dorronsoro.es/vrp/). Experiments show that my algorithm performs well and even release new optimum. The following figures are the results of two of the international dataset.

![fig1](./fig1.png) ![fig2](./fig2.png)