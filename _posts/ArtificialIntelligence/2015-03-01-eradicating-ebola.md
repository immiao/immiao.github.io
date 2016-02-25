---
layout: post
comments: true
categories: Artificial-Intelligence
---

> This is a problem in [The Mathematical Contest in Modeling, 2015](http://www.comap.com/undergraduate/contests/mcm/contests/2015/problems/). Our solution earns us the Honorable Mention.

Click [here](./paper.pdf) to see full pages of our paper.

## Description

The world medical association has announced that their new medication could stop Ebola and cure patients whose disease is not advanced. Build a realistic, sensible, and useful model that considers not only the spread of the disease, the quantity of the medicine needed, possible feasible delivery systems (sending the medicine to where it is needed), (geographical) locations of delivery, speed of manufacturing of the vaccine or drug, but also any other critical factors your team considers necessary as part of the model to optimize the eradication of Ebola, or at least its current strain.

## Our Solution

> To optimize the eradication of Ebola, we consider about how to site the drug delivery center and how to make a good schedule scheme to deliver the drugs to epidemic areas. Obviously, a good location of the drug delivery center and a proper schedule scheme to deliver the drugs are important factors to improve the efficiency of the delivery system and meet the need of the epidemic areas.

* [**SIR Model**](https://en.wikipedia.org/wiki/Epidemic_model)

The first model we established is SIR model which is a common model to consider the transmission of infectious disease. The first model we established is SIR model which is a common model to consider the transmission of infectious disease.  Applying SIR model is convenient for us to predict the seriousness of the epidemic in the future in order to choose an optimal delivery route in a modified MDVRP (multiple depot vehicle routing problem) model which is a model based on VRP but modified to adjust the problem of optimizing the eradication of Ebola. Later we will talk about this modified MDVRP model in detail.

<br/>
![sirformula](./sirformula.png)

* **Weighted K-Means Algorithm**

The second model is to explore a proper site for the drug delivery center considering different factors which obviously affect the eradication of Ebola such as the seriousness of the epidemic area gained from SIR model, the distance to the epidemic area and so on. We generalized a modified K-Means algorithm to select several centers considering not only the distance from the center to the epidemic area but also some important factors mentioned above.

*basic K-Means:*
<br/>
![kmeans](./kmeans.png)

*Weighted K-Means:*
<br/>
![wkmeans](./weighted-kmeans.png)

* **Modified MDVRP Model**

The third model is to achieve an optimal scheduling scheme for vehicles in drug delivery centers. In order to optimize the scheduling problem, we established a modified MDVRP model mentioned above which is a natural extension of VRP (vehicle routing problem). VRP and MDVRP have been proven to be NP-hard problems. Hence, they are hard to be solved using traditional mathematical methods when the scale of the problem grows large. In this paper, we proposed a hybrid genetic algorithm to optimize the MDVRP while considering not only the distance to the epidemic area (though the disaster is serious, it is still important to consider the cost of distance because more time is needed when the medicine is delivered to a remote area and it will also cause the increase of some other areas because of the late arriving of the medicine) but also the seriousness of the epidemic area predicted by SIR(that's to say, in spite of the long distance, if the seriousness of a certain epidemic area is at a high level, it may be considered at a high priority).

*basic MDVRP:*
<br/>
![mdvrp](./mdvrp1.png)

*MDVRP with banned list:*
<br/>
![mdvrp](./mdvrp2.png)
![mdvrp](./mdvrp3.png)

## Experiment

The first picture shows the tendency of the epidemic without using our system.

![nosir](./nosir.png)

The second picture shows the tendency of the epidemic with our system.

![sir](./sir.png)

Ebola will be eradicated more qucikly with our effective system.