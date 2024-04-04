# Finite Automata

## Acceptors and Transducers

An acceptor is a transition system with:
* (Input-)labelled transition
* a start/initial state
* a set of final states

An transducer is a transition system with:
* (input & output-) labelled transition
* a start/initial state

## Deterministic Finite Automata

A deterministic finite automaton (DFA) is a total, finite state acceptor

DFAs represent "computation with finite memory"

DFAs are simple, easy to work with and show up all over the place.

## Deterministic Finite Automata - Formal

Formally, a deterministic finite automaton (DFA) is a tuple of $Q, \sum, \delta, q_0, F$ where
* $Q$ is a finite set of states
* $\sum$ is the input alphabet
* $\delta: Q \times X \rightarrow Q$ is the transition function
* $q_0 \in Q$ is the start state
* $F \subseteq Q$ is the set of final/accepting states

### Language of a DFA

A DFA accepts a sequence of symbols from $\sum$ - i.e. the elements of $\sum^*$

Informally: A word defines a run in the DFA and the word is accepted if the run ends in a final state

Steps:
1. Start in a state $q_0$
2. Take the first symbol of $w$
3. Repeat the following until there are no symbols left:
    * Based on the current state and current input symbol, transition to the appriorate state determined by $\delta$
    * Move to the next symbol in $w$

For a DFA $A = (Q, \sum, \delta, q_0, F)$, the language of $A$, $L(A)$, is the set of words from $\sum^*$ which are accepted by $A$.

A language $L \subseteq \sum^*$ is regular if there is some DFA $A$ such that $L = L(A)$

### Language of a DFA: Formally
Given a DFA $A = (Q, \sum, \delta, q_0, F)$ we define $L_A : Q \rightarrow \sum^*$ inductively as follows: 
* If $g \in F$ then $\lambda \in L_A(q)$
* If $q \overset{a}{\rightarrow} q'$ and $w \in L_A(q')$ then $aw \in L_A(q)$

We then define
$$L(A) = L_A(q_0)$$

## Non-deterministic Finite Automata

### Non-deterministic Finite Automata - Definition
A non-deterministic finite automaton (NFA) is a non-deterministic, finite state acceptor

More general than DFAs: A DFA is an NFA

Formaly, a non-deterministic finite automaton (NFA) is a tuple $(Q, \sum, \delta, q_0, F)$ where
* Q is a finite set of states
* $\sum* is the input alphabet
* $\delta \subseteq Q \times (\sum \times \{ \epsilon \}) \times Q$ is the transition relation
* $q_0 \in Q$ is the start state
* $F \subseteq Q$ is the set of final/accepting states

### Language of an NFA
An NFA accepts a sequence of symbols from $\sum$ - i.e. elements of $\sum^*$

Informally: A word defines several runs in the NFA and the word is accepted if at least one run ends in a final state

1. Runs can end prematurely (these do not count)
2. An NFA will always "choose wisely"

For an NFA $A = (Q, \sum, \delta, q_0, F)$, the language of $A$, $L(A)$, is the set of words from $\sum^*$ which are accepted by $A$.

### Language of a NFA: Formally
Given a NFA $A = (Q, \sum, \delta, q_0, F)$ we define $L_A : Q \rightarrow \sum^*$ inductively as follows: 
* If $g \in F$ then $\lambda \in L_A(q)$
* If $q \overset{a}{\rightarrow} q'$ and $w \in L_A(q')$ then $aw \in L_A(q)$
* If $q \overset{\epsilon}{\rightarrow} q'$ and $w \in L_A(q)$ then $w \in L_A(q)$
We then define
$$L(A) = L_A(q_0)$$

### NFAs vs DFAs
Clearly for any DFA $A$ there is an NFA $B$ such that $L(A) = L(B)$

Theorem:

* For any NFA $B$ there is a DFA $A$ such that $L(A) = L(B)$
* For any NFA with n states, there exists a DFA with at most $2^m$ states that accepts the same language
* There exist NFAs with $n$ states such that the smallest DFA that accepts the same language has at least $2^n$ states

## Regular Languages

### Non-regular languages
There are languages which are not regular.

This can be proven using a "simple" counting argument: there are uncountably many languages and only countably many DFAs.

### Complementation
Theorem:

If $L$ is a regular language, then $L^c = \sum^* - L$ is a regular language

Proof:
* Let $A = (Q, \sum, \delta, q_0, F)$ be a DFA such that $L(A) = L$
* Consider $A' = (Q, \sum, \delta, q_0, Q - F)$
* For any word $w \in \sum^*$ the corresponding run in $A$ is unique, so:
    * If $w \in L(A)$ then $w \notin L(A')$, and
    * IF $w \notin L(A)$ then $w \in L(A')$,
* Therefore $L(A') = \sum^* - L(A) = L^c$

### Union
Theorem:

If $L_1$ and $L_2$ are both regular languages, then $L_1 \cup L_2$ is a regular language

Proof:

* Let $L(B_1) = L_1$ and $L(B_2) = L_2$
* Construct a new NFA $B$ by having a new start state with $\epsilon$-transitions to the start states of $B_1$ and $B_2$
* Now consider $w \in B_1 \cup B_2$
    * If $w \in B_1$ then there is a run in $B_1$ and hence in $B$, which ends in a final state
    * If $w \in B_2$ then there is a run in $B_2$ and hence in $B$, which ends in a final state
    * In either case $w \in L(B)$

### Intersection
Theorem:

If $L_1$ and $L_2$ are both regular languages, then $L_1 \cap L_2$ is a regular language.

Proof:
$$L_1 \cap L_2 = {({L_1}^c \cup {L_2}^c)}^c$$

### Concatenation
Theorem:

If $L_1$ and $L_2$ are both regular languages, then $L_1 \cdot L_2$ is a regular language.

Proof:
* Let $B_1$ and $B_2$ be NFAs such that $L(B_1) = L_1$ and $L(B_2) = L_2$
* Construct an NFA $B$ which has an $\epsilon$-transition from the final states of $B_1$ to the start state of $B_2$. Let the start state of $B$ be the start state of $B_1$ and the final states of $B$ be the final states of $B_2$
* Now, any word that is in $L_1 \cdot L_2$ can be written as $vw$ where $v \in L_1$ and $w \in L_2$. Since $v$ has an accepting run in $L_1$ and $w$ has an accepting run in $L_2$, then $vw$ must have an accepting run in $L_1 \cdot L_2$

### Kleene Star
Theorem:

If $L$ is a regular language, then $L^*$ is regular

Proof:
* Let $B$ be a NFA such that $L(B) = L$
* Construct a NFA $B'$ such that
    * create a new start state which is accepting
    * adding a $\epsilon$-transition from the new start state to the old start state of $B$
    * adding $\epsilon$-transitions from the final states of $B$ to the new start state
* Rest is essentially the proof for concatentation

### Regular Operations
Concatenation, union and Kleene star are collectively known as regular operations.


## Regular Expression

### Definition and applications
Regular expressions are a way of describing "finite automation" patterns:
* Second last letter is 'b'
* Every odd letter is 'b'

It has many applications in Computer Science
* Lexical analysis in compiler construction
* Search facilities provided by text editors and databases; utilities such as grep and awk
* Pattern matching on strings

### Inductive definition
Given a finite set $\sum$, a regular expression over $\sum$ is defined recursively as follows
* $\empty$ is a regular expression
* $\epsilon$ is a regular expression
* $a$ is a regular expression for all $a \in \sum$
* If $E_1$ and $E_2$ are regular expressions then $E_1 E_2$ is also an regular expression
* If $E_1$ and $E_2$ are regular expressions then $E_1 + E_2$ is also an regular expression
* If $E$ is a regular expression then $E^*$ is a regular expression

Parentheses are used to disambiguate regexps, though * binds tighter than concatenation, which binds tighter than +.

### Language of a Regular Expression
Formally given a regular expression, $E$, over $\sum$, we define $L(E) \subseteq \sum^*$ recursively as follows:
* If $E = \empty$ then $L(E) = \empty$
* If $E = \epsilon$ then $L(E) = {\lambda}$
* If $E = a$ where $a \in \sum$ then $L(E) = {a}$
* If $E = E_1 E_2$, then $L(E) = $L(E_1) \cdot L(E_2)$
* If $E = E_1 + E_2$, then $L(E) = $L(E_1) \cup L(E_2)$
* If $E = {E_1}^*$ then $L(E) = {(L(E_1))}^*$


### Regular Expression vs NFAs
Theorem:
* For any regular expression $E$, $L(E)$ is a regular language$
* For any regular language $L$, there is a regular expression $E$ such that $L = L(E)$