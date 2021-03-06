---
title: Quantum Hackathon
---

--sep--
---
title: Introduction
---

# QuantX - Quantum Hackathon

## Introduction

You are about to solve the katas **GroversAlgorithm** and **SolveSATWithGrover**.

If you prefer, you can run these katas locally with a jupyter notebook. If you choose this option, you need to have installed miniconda (or Anaconda). See *Preparation* for detailed instructions on the installation of such a local environment.

Otherwise it is possible to run these katas entirely online by leveraging Binder. No installation needed in this case. Please favor the local installation for a faster execution and to avoid connexion issues.


## Prerequisites

For this Hackathon, you will need:

- A browser
- An internet connexion
- And that's it !

Basic knowledge of [logic gates](https://en.wikipedia.org/wiki/Logic_gate) can be useful.

--sep--
---
title: Objectives of the Hackathon
---

## Objectif of the Hackathon

This Hackathon **doesn't assume any prior knowledge on Quantum Computing**. First, you will rediscover one of the most fundamental quantum algorithms : Grover's algorithm. Grover's algorithm has been generalised in many different directions : quantum walks, quantum singular value transformation .. It's a great foundation to build upon in order to understand quantum algorithmss.

Second, you will use Grover's quantum algorithm to **solve a concrete problem** : the satisfiability problem (SAT). SAT solvers are used in many different domains, for example circuit design and automatic theorem proving.

--sep--
---
title: Preparation
---

## Preparation

### First option : local environment

* If you haven't already installed an Anaconda distribution, you can install miniconda here : <a href="https://docs.conda.io/en/latest/miniconda.html" target="_blank">https://docs.conda.io/en/latest/miniconda.html</a>.

* Open a miniconda (or Anaconda) prompt.

* Create and activate a new conda environment named ```qsharp-env``` with the required packages (including Jupyter Notebook and IQ#) by running the following commands:

```bash
conda create -n qsharp-env -c quantum-engineering qsharp notebook

conda activate qsharp-env
```

* To populate your local package cache with all required QDK components, run the following command from the same terminal :

```bash
python -c "import qsharp"
```

You now have the IQ# kernel for Jupyter, which provides the core functionality for compiling and running Q# operations from Q# Jupyter Notebooks.

* <a href="https://github.com/microsoft/QuantumKatas/blob/main/GroversAlgorithm/GroversAlgorithm.ipynb" target="_blank">Download the kata GroversAlgorithm</a> 

* Unzip the file and navigate to the unzipped folder in your Conda terminal.

Décompressez le fichier et naviguez jusqu'au dossier dans votre terminal Conda.

* Run the jupyter notebook :

```bash
jupyter notebook GroversAlgorithm.ipynb
```

### That's it !
Follow the instructions of the jupyter notebook. To validate a step, click on ```step forward``` on the left or use the key combination ```Ctrl + Enter```.
Your code output will appear below.

Once you have completed the GroversAlgorithm kata, you can follow-up with the SolveSATWithGrover kata:

<a href="https://github.com/microsoft/QuantumKatas/blob/main/SolveSATWithGrover/SolveSATWithGrover.ipynb" target="_blank">Download the kata SolveSATWithGrover</a> 


### Alternative : run the katas online

Go to the <a href="https://github.com/microsoft/QuantumKatas/tree/main/GroversAlgorithm" target="_blank">Githhub repo</a> of GroversAlgorithm kata.

Then click on the link : *run the GroversAlgorihtm kata as a Jupyter Notebook*.

Once you have completed the GroversAlgorithm kata, you can follow-up with the <a href="https://github.com/microsoft/QuantumKatas/tree/main/SolveSATWithGrover" target="_blank">SolveSATWithGrover</a> kata.

Then click on the link : *run the SolveSATWithGrover kata as a Jupyter Notebook*.


--sep--
---
title: Kata GroversAlgorithm - tips
---

# GroversAlgorithm : tips

Here you will find tips and links to help you solve this kata.

## Taks 1.1

In Q#, an operation is a procedure that can process quantum memory (qubits). The type of each argument is given after the symbol `:` that follows the name of each variable. Here the variable `queryRegister` is hence a qubit array and the variable `target` is a qubit. The last given type (here: `Unit`) is the type of the object returned by the operation. Here `Unit` means that the operation returns nothing. It is similar to `void` in other languages. Therefore this operation doesn't make use of the key-word `return`. `is Adj` means that it is possible to automatically generate the 'adjoint' operation (i.e. the inverse operation). Let's not worry about it for now.

An operation that returns noting (whise output type is `Unit`) is not useless since quantum memory processing happens in place : it is a side-effect of the operation. Here for instance, the operation `Oracle_AllOnes` modifies the state (i.e. the internal value) of the qubit `target`.

You will need the [quantum gate **X**](https://en.wikipedia.org/wiki/Quantum_logic_gate#Pauli-X_gate), the quantum equivalent of a **NOT** gate. You will also need the [**Controlled** functor](https://docs.microsoft.com/en-us/azure/quantum/user-guide/language/expressions/functorapplication#controlled-functor).

Controlling a quantum operation **U** by an array of control qubits ```[controlQubits]``` amounts to applying **U** to ```targetQubit``` if and only if every qubit of the control array are in the state |1>. It is somewhat similar to a classical ```if```. It follows the following syntax :


```csharp
Controlled U ([controlQubits], targetQubit);
```

```[controlledQubits]``` is an array of qubits. ```targetQubit```  is a qubit.


## Task 1.2

The **Controlled** functor expresses the condition that all the contol qubits are in the state |1>. There is no Q# native syntax to express an other condition, such as some control qubits are in the state |0> and others are in the state |1>. It is therefore necessary to reduce to the all |1> case by modifying the value of the control qubits before (and after!) applying the **Controlled** operation.

Look here for documentation about [```for``` loops](https://docs.microsoft.com/en-us/azure/quantum/user-guide/language/statements/iterations?oicd=WTMCID) and classical [```if``` conditions](https://docs.microsoft.com/en-us/azure/quantum/user-guide/language/statements/conditionalbranching).

*Bonus:* For a nice and concise code, I recommend the [**within {..} apply {..}**](https://docs.microsoft.com/en-us/quantum/user-guide/using-qsharp/control-flow#conjugations) syntax.

## Tasks 1.4

This procedure doesn't process qubits. Therefore it is expressed in Q# by a function and not an operation. This function transforms the operation `markingOracle` into another operation that we may call `phaseOracle`. The [**return**](https://docs.microsoft.com/en-us/azure/quantum/user-guide/language/statements/returnsandtermination?view=qsharp-preview) syntax is used.

An intermediate step can be to write an operation `OracleConverterWithQubitRegister`
first. `OracleConverterWithQubitRegister` takes two arguments : the oracle `markingOracle` and a qubit array `register`. It applies the `phaseOracle` to `register`. An additional qubit is necessary in the body of `OracleConverterWithQubitRegister`. To declare and use quantum memory (qubits), the keyword is [**use**](https://docs.microsoft.com/en-us/azure/quantum/user-guide/language/statements/quantummemorymanagement?view=qsharp-preview). You will also need the [Hadamard quantum gate (**H**)](https://en.wikipedia.org/wiki/Quantum_logic_gate#Hadamard_(H)_gate) and the [**X** quantum gate](https://en.wikipedia.org/wiki/Quantum_logic_gate#Pauli-X_gate). To apply a gate **X** or **H** to `qubit`, use this syntax in Q#:
```csharp
X(qubit);
H(qubit);
```

To write the function `OracleConverter`, it is possible in Q# to define [partial operations](https://docs.microsoft.com/en-us/azure/quantum/user-guide/language/expressions/partialapplication?view=qsharp-preview).

## Task 2.1

The [quantum gate **H**](https://en.wikipedia.org/wiki/Quantum_logic_gate#Hadamard_(H)_gate) yields superposiion states from the computational basis states |0> and |1>.


## Task 2.2

Depending on the targeted solution, this syntax can be useful:
- the [**Controlled** functor](https://docs.microsoft.com/en-us/azure/quantum/user-guide/language/expressions/functorapplication#controlled-functor).
- the [**Z** quantum gate](https://en.wikipedia.org/wiki/Quantum_logic_gate#Pauli-Z_(%7F'%22%60UNIQ--postMath-00000028-QINU%60%22'%7F)_gate).
- Functions [**Most**](https://docs.microsoft.com/en-us/qsharp/api/qsharp/microsoft.quantum.arrays.most) and [**Tail**](https://docs.microsoft.com/en-us/qsharp/api/qsharp/microsoft.quantum.arrays.tail). Don't forget to open the Microsoft.Quantum.Arrays namespace to use these functions.

## Task 2.3

Applying a Hadamard transform consists in applying a Hadamard gate (**H**) to every qubit.

## Task 3.1

This [Wikipedia article](https://en.wikipedia.org/wiki/Grover%27s_algorithm) describes Grover's algorihtm.

## Task 3.2

- The function **Message** follows this syntax:
```csharp
Message ("this is the message I want to print");
```

- The Q# operation **M()** measures a qubit:
```csharp
let myResult = M(qubit);
```

`myResult` has type `Result`. Objects of type `Result` have two possible values : `Zero` and `One`. 
See [here](https://docs.microsoft.com/en-us/qsharp/api/qsharp/microsoft.quantum.intrinsic.m?view=qsharp-preview) for the syntax of a quantum measurement in Q#.
The function [MultiM](https://docs.microsoft.com/en-us/qsharp/api/qsharp/microsoft.quantum.measurement.multim?view=qsharp-preview) in the namespace Microsoft.Quantum.Measurement makes it possible to directly measure an array of qubits.


--sep--
---
title: SolveSATWithGrover - tips
---

# Kata SolveSATWithGrover : tips

Here you will find tips and links to help you solve this kata.

## Task 1.1

It is common to apply the functor **Controlled** to the **X** quantum gate. When the control is on only 1 or 2 qubits, here is a more concise syntax:
- control on 1 qubit:
  
```csharp
CNOT(controlQubit, targetQubit);
```
is strictly equivalent to: 
```csharp
Controlled X ([controlQubit], targetQubit);
```
- control on 2 qubits:
  
```csharp
CCNOT(FirstControlQubit, SecondControlQubit, targetQubit);
```
is strictly equivalent to:
```csharp
Controlled X ([FirstControlQubit, SecondControlQubit], targetQubit);
```

## Task 1.2

Don't forget the syntax [**within {..} apply {..}**](https://docs.microsoft.com/en-us/quantum/user-guide/using-qsharp/control-flow#conjugations) for nice looking Q# code 😊.

## Task 1.4

- Selecting a subarray of qubits :

To define the subarray of the array `myArray` whose indices are between `start` and `stop`, use the following syntax:

```csharp
let myArray = [10, 11, 12, 13, 14, 15];
let start = 1;
let stop = 3;
let mySubarray = myArray[start .. stop]; //  mySubarray = [11, 12, 13].
```

For more information see the paragraph [Array slices](https://docs.microsoft.com/en-us/quantum/user-guide/language/expressions#array-expressions).

- The function **Length** returns the length of an array:
  
```csharp
let N = Length(myArray);
```

## Task 1.5

### Mutables variables

Variables are declared as :

```csharp
let myImmutableVariable = 2;
```
are immutables.

For mutable variables, we use the keywords `mutable` for declaration and `set` for modification :

```csharp
mutable myMutableVariable = 4 ;
set myMutableVariable = 3;
```
You can learn more about mutable variables [here](https://docs.microsoft.com/en-us/azure/quantum/user-guide/language/statements/variabledeclarationsandreassignments).

### Mutable array 

To declare a mutable array, you can use this syntax :
```csharp
mutable myArray = new Int[0];
```

The above line of code declares an array xith length 0 (i.e. empty). To add a value to this array, use this syntax :
```csharp
myArray += [myNewValue];
```

### Copy & Update

To modify a value of an array, use this syntax :
```csharp
myArray w/= myIndex <- myNewValue;
```
Thevalue at position `myIndex` of `myArray` becomes `myNewValue`. 

You can look up documentation on Copy & Update Expressions [here](https://docs.microsoft.com/en-us/azure/quantum/user-guide/language/expressions/copyandupdateexpressions).


## Task 3.2

After processing qubits, you have to bring them back to the state |0> before releasing them. To achieve this, you can use the function ```Reset``` :
```csharp
Reset(qubit);
```
For an array of qubits, you can use the function ```ResetAll``` :
```csharp
ResetAll(qubitArray);
```

--sep--
---
title: Conclusion
---

# Conclusion

Congratulations, you have made it!

You can go deeper on Quanutm Computing by solving [the other katas](https://github.com/microsoft/QuantumKatas/tree/master).
