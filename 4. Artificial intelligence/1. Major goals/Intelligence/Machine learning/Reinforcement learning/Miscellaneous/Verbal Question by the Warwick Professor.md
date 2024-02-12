## 1. POMDP

### 1.1 Definition
- $S$  is a set of states,
- $A$  is a set of actions,
- $T$  is a set of conditional transition probabilities between states,
- $R:S \times A\to \mathbb{R}$  is the reward function.
- $\Omega$  is a set of observations,
- $O$  is a set of conditional observation probabilities, and
- $\gamma \in \left [ 0, 1 \right )$  is the discount factor.

## 2. Problem

### 2.1 Definition
The coffee robot looks for a target person in a specific area.
![playster-hq-plan](../../../../../../Attachments/4.%20Artificial%20intelligence/1.%20Major%20goals/Intelligence/Machine%20learning/Reinforcement%20learning/Miscellaneous/Verbal%20Question%20by%20the%20Warwick%20Professor/IMG-20240212100315968.jpg)

### 2.2 Model-based

#### 2.2.1 Assumptions
- The search area is finite.
- Search time is limited.
- The robot's sensors can cover a certain range of space, and this range can traverse the search area. Therefore, the entire search area can be divided into multiple sub-areas, and the robot's sensors can cover an entire sub-area at any time.
- Each sub-area has different sizes, and the time and distance for the robot to reach the next sub-area are related to the complexity of the crowd density scene.
- When the robot switches sub-areas, the sensor can cover the area channel to avoid the target passing through the blind spot.

#### 2.2.2 Model Settings
##### 2.2.2.1 State Modeling
According to the above assumptions, the problem model can be abstracted into discrete state-spaces. The following matrices can be viewed as states and used as beliefs too.
###### Sub-area Distance Matrix
Among them, the fixed (but learnable) information includes a sub-area distance matrix, as shown below. In this simplified matrix, the distance of connected sub-areas is 1, and the distance of disconnected sub-areas is infinite.
![500](../../../../../../Attachments/4.%20Artificial%20intelligence/1.%20Major%20goals/Intelligence/Machine%20learning/Reinforcement%20learning/Miscellaneous/Verbal%20Question%20by%20the%20Warwick%20Professor/IMG-20240212100315992.png)
###### Relation Matrix
The relations between people can be achieved through early supervised learning, or it can be added to the state for iteration.

###### Staff Distribution Matrix
The state information changes over time includes staff distribution matrix and digital system information (timetable).
![400](../../../../../../Attachments/4.%20Artificial%20intelligence/1.%20Major%20goals/Intelligence/Machine%20learning/Reinforcement%20learning/Miscellaneous/Verbal%20Question%20by%20the%20Warwick%20Professor/IMG-20240212100316015.png)
Suppose there are $n$ sub-areas and $m$ staffs. The shape of staff distribution matrix is $n\times m$.

##### 2.2.2.2 Action Modeling
The action of the robot can be abstracted as going from the current sub-area to the next adjacent sub-area.
![500](../../../../../../Attachments/4.%20Artificial%20intelligence/1.%20Major%20goals/Intelligence/Machine%20learning/Reinforcement%20learning/Miscellaneous/Verbal%20Question%20by%20the%20Warwick%20Professor/IMG-20240212100316033.png)

##### 2.2.2.3 Reward Modeling
###### Reward for stable operation
$$
R_{operation}=\alpha t_{save}-\beta t_{danger}d-\gamma t
$$
$t_{save}$ is the time the machine is running normally.
$t_{danger}$ is the time when the machine encounters dangers (collision).
$d \in D$ is the type of the danger.
$\gamma t$ represents the loss of time elapsed before the target is found.

###### Rewards for reaching goals
$$
R_{target}=
\begin{cases}
 1 & \text{ if } p>p'  \\
 0 & \text{ if } p<p' 
\end{cases}
$$

#### 2.2.3 Methods
##### 2.2.3.1 Belief MDP
$b$ is a belief over the states.
$a$ is action.
$o\in \Omega$ is observing with probability $O(o,s',a)=P_{r}(o \mid s',a)$.
Transition probabilities is $T(s,a,s')=P_{r}(s' \mid s,a)$.
Rewards is $R(s,a)$.
The update of $b$ is: 
$$
b'=\tau (o,a,b)
$$
When going to real state $s'$, the new belief is:
$$
\begin{equation}
\begin{split}
b'(s')
& = P_{r}(s' \mid o,a,b) =\frac{P_{r}(s',o,a,b)}{P_{r}(o,a,b)} \\
& = \frac{P_{r}(o \mid s',a,b)P_{r}(s' \mid a,b)}{P_{r}(o \mid a,b)} \\
& = \frac{P_{r}(o \mid s',a) {\textstyle \sum_{s}^{}}P_{r}(s' \mid a, b, s) P_{r}(s \mid a,b)}{P_{r}(o \mid a,b)} \\
& = \frac{O(o,s',a) {\textstyle \sum_{s}^{}} T(s,a,s') b(s)}{P_{r}(o \mid a,b)} \\
\end{split}
\end{equation}
$$
which is proved by Astrom [^1].

##### 2.2.3.2 Policy-based
The policy function is $\pi (b_{t})$.
In this problem, the strategy of the robot is to go to the most probable position of the target. Therefore, the optimization objective can be simplified to mention the accuracy of the belief. 

##### 2.2.3.3 Value-based
The reward of time $t$ is $R(s_{t},\pi (b_{t}))$.
The **state-value function** is:
$$
V^{\pi}(b_{0})=\sum_{t=0}^{\infty } \gamma^{t} r(b_{t},a_{t})=\sum_{t=0}^{\infty } \gamma^{t} E(R(s_{t},a_{t})\mid b_{0},\pi)
$$
The optimal policy:
$$
\pi^{*}=argmax_{\pi}V\pi(b_{0})
$$


### 2.3 Model-free

$$
\pi_{\theta} \left ( a_{t} | s_{t}, z \right ) = \mathbb{P} \left [ a_{t} | s_{t}, z \right ] 
$$





[^1]: Åström, K. J. (1965). Optimal control of Markov processes with incomplete state information. _Journal of mathematical analysis and applications_, _10_(1), 174-205.