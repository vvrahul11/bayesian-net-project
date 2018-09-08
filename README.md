[![Build Status](https://travis-ci.org/lamba92/bayesian-net-project.svg?branch=master)](https://travis-ci.org/lamba92/bayesian-net-project) [![](https://jitpack.io/v/Lamba92/bayesian-net-project.svg)](https://jitpack.io/#Lamba92/bayesian-net-project)

# Bayesian Networks Project

The project consists in two exercises:

   **1.** **VE (Variable Elimination)** algorithm extension and its implementation on `Static Bayesian Networks`,
 
   **2.** **Rollup Filtering inference** algorithm and its implementation on `Dynamic Bayesian Networks (DBN)`.
      
# Preliminaries

## Static Bayesian Networks

Bayesian network is a directed acyclic graph(DAG) that is an efficient and compact representation for a set of conditional independence assumptions about distributions. The directed graph tries to represent the random variables as nodes in a graph.
These nodes represent the random variables and the edges represent the direct influence of one variable of one another.
In general each random variable is associated with a Conditional Probability Distribution also called as a CPD that specifies the distribution over the values of the random variable associated with its parents.The CPD encodes the distribution of the variables and help in precisely determining the output of the variable.


### Variable Elimination algorithm

`Variable elimination (VE)` is a simple and general exact inference algorithm in probabilistic graphical models, such as Bayesian networks and Markov random fields. It can be used for inference of `Maximum A Posteriori (MAP)` state or estimation of conditional or marginal distributions over a subset of variables. The algorithm has exponential time complexity, but could be efficient in practice for the low-treewidth graphs, if the proper elimination order is used (which is a NP-hard problem).  
To find a variable's proper elimination order we'll use some heuristics.

### Heuristics

La struttura dati utilizza per trovare un ordine di eliminazione prende il nome di Moral Graph.
In graph theory, a moral graph is used to find the equivalent undirected form of a directed acyclic graph. The moralized counterpart of a directed acyclic graph is formed by adding edges between all pairs of nodes that have a common child, and then making all edges in the graph undirected.

Definiamo una funzione di valutazione la quale utilizza una delle seguenti euristiche:

 - **Min-neighbors**: The cost of a vertex is the number of neighbors it has in the current graph.
 
 - **Min-weight**: The cost of a vertex is the product of weights — domain cardinality — of its neighbors.
 
 - **Min-ﬁll**: The cost of a vertex is the number of edges that need to be added to the graph due to its elimination.
 
 - **Weighted-min-ﬁll**: The cost of a vertex is the sum of weights of the edges that need to be added to the graph due to its elimination, where a weight of an edge is the product of weights of its constituent vertices.
 
Tuttavia è dimostrato che nessuna di queste funzioni sia migliore di un'altra in quanto la loro bontà è strettamente dipendente dalla topologia della rete su cui l'algoritmo stesso viene applicato.

Moral graph e funzione di valutazione vengono utilizzati da un algoritmo greedy l'ordinamento appropriato con cui effettuare l'algortimo VE.

L'algoritmo greedy viene riportato nella seguente immagine: IMG.
 

## Dynamic Bayesian Networks

`Dynamic Bayesian Networks (DBN`'s) are static Bayesian networks that are modeled over an arrangement of time-series. 
In a Dynamic Bayesian Network, each time slice is conditionally dependent on the previous one. The probabilities among the original distribution determine the probabilities in the successive time series.

Per costruire una DBN occorre:
**1.** Distribuzione a priori sulle variabili di stato P(Xo)
**2.** il modello di transizione P(Xt+1 | Xt)
**3.** il modello sensore P(Et | Xt)

### Rollup Filtering algorithm

É possibile costruire una rete dinamica completa con un numero non limitato di intervalli, a partire dai parametri specificati precedentemente, copiando il primo intervallo.
La tecnica che ci consente di effettuare quest'operazione prende il nome di Unrolling.

Tuttavia, quest'ultima soffre di un'occupazione eccessiva di memoria poiché tutti gli slice temporali (anche possibilmente infiniti), prodotti sino al tempo *t*, vengono mantenuti in memoria.

Per risolvere questo problema viene utilizzata la tecnica del Rollup Filtering. Con essa è possibile concentrarsi solamente su due slice alla volta; l'intervallo successivo verrà creato utilizzando l'algortimo di VE sull'ultimo intervallo prodotto.

Per effettuare l'inferenza occorre anche possedere una sequenza di osservazioni.

# Project Description
   
## 1. VE (Variable Elimination) algorithm extension

L'esercizio si suddivide in quattro steps:
 - Rimuovere le variabili irrilevanti,
 - Implementare euristiche di ordinamento delle variabili,
 - Permettere inferenze `MPE (Most Probable Explanation)` e `MAP (Maximum a Posteriori Probability)`,
 - Analisi risultati empirici.

## 2. Rollup Filtering algorithm implementation

L'esercizio si suddivide in tre steps:
 - Adattare l'algoritmo `Variable Elimination` per BN statiche e modificarlo in modo che effettui `Rollup Filtering` su una `Dynamic Bayes Net` considerando due slice alla volta,
 - Fornire all'algortimo una sequenza di osservazioni, eventualmente più di una per ogni istante di tempo,
 - Analisi risultati empirici.


# Project management

The project has been divided into three main parts:
- `utils` folder, which contains:

  - `MoralGraph.kt`, which implements a Moral Graph and all the necessary methods needed,

  - `BIFToBayesNet.kt`, which implements a BIF file parser used to instatiate a BayesNet object from a BIF file (Boolean Domain only),
  
  - `Utils.kt` and `WrongDistributionException` files.
  
- `CustomDynamicaBayesNet.kt` , which implents l'algoritmo di Rollup Filtering con due slice alla volta. Per far avanzare la rete allo stato successivo t+1, dove t è lo stato temporale attuale, occorre eseguire il metodo `forward`

- `Inferences.kt` che istanzia un oggetto il quale contiene l'estensione dell'algoritmo di VE richiesta dal progetto e che espone i metodi

  - `order`, che a partire da una `BayesianNetwork` restituisce un ordine appropriato con cui applicare l'algortimo di VE,
  
  - `calculateVariables` che, date le variabili di query, le evidenze e la Rete Bayesiana, rimuove da quest'ultima le **variabili irrilevanti**, ossia every variable that is not an ancestor of a query variable or evidence variable.

There is also a `resource` folder, which contains all the BIF format hard-coded nets.


# VE extension implementation

## Utilities

They are contained within the utils folder. They are:



# Getting Started

An easy to use library that allows to build and evolve a Dynamic Bayesian Network which random variables have a boolean domain. It has been built using the library [aima-java](https://github.com/aimacode/aima-java). 

## Installing

Add the [JitPack.io](http://jitpack.io) repository to the project `build.grade`:
```
repositories {
    maven { url 'https://jitpack.io' }
}
```

Then import the latest version in the `build.gradle` of the modules you need:

```
dependencies {
    implementation 'com.github.Lamba92:bayesian-net-project:{latest_version}'
    implementation 'com.googlecode.aima-java:aima-core:3.0.0'
}
```

If using Gradle Kotlin DSL:
```
repositories {
    maven(url = "https://jitpack.io")
}
...
dependencies {
    implementation("com.github.Lamba92", "bayesian-net-project", "{latest_version}")
    implementation("com.googlecode.aima-java", "aima-core", "3.0.0")
}
```
If you are using Maven, switch to Gradle, it's 2018.

## Usage

### Static Bayes Network

Create a `???` using a newly generated dynamic network or use an example from the factories of this library and aima's:

### Dynamic Bayes Network

Create a `CustomDynamicBayesianNet` using a newly generated dynamic network or use an example from the factories of this library and aima's:
```
import it.unito.bayesian.net.CustomDynamicBayesianNet
import it.unito.bayesian.net.Inferences.*

...

val inference = getCustomEliminationAsk(minWeightHeuristicFunction())
val customNet = getComplexDynamicNetworkExample()

customNet.forward() // Moves the network one step forward
```

`CustomDynamicBayesianNet` constructor needs a network and a `BayesianInference` to proceed forward in time.

Check out the [KDocs](https://jitpack.io/com/github/lamba92/bayesian-net-project/0.6/javadoc/bayesian-net-project/) for details. 

For a usage example have a look [at some tests here](https://github.com/lamba92/bayesian-net-project/tree/master/src/main/kotlin/it/unito/bayesian/net/main).

## Authors

* **Cesare Pio Iurlaro** - [CesareIurlaro](https://github.com/CesareIurlaro)
* **Giuseppe Gabbia**  - [beppe95](https://github.com/beppe95)
* **Lamberto Basti**  - [lamba92](https://github.com/lamba92)
