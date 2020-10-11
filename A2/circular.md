### Assignment 2: Synchronisation - CSSE7610

<br>

###### Student Name: Ming Zhang

###### Student ID: 45006115

<br>

***

#### (a)

Compares to the original producer-consumer problem, this producer-consumer problem uses a circular buffer and split semaphores. 

**Mutual Exclusion:**

To prove the mutual exclusion, we need to prove that **A**: the producer and the consumer will not execute the same place at the same time (which is p3 and q2 won't be executed at the same time when (in modulo N) = (out modulo N)), which means that consumer will not take a product from an empty place and the producer will not put the product into a place where is already full.

<u>At the start</u> $\rightarrow$ in = 0, out = 0

* If producer goes first, it will successfully execute from p1 to p5, when p3 runs in = 0 and it will set to be 1 in p4 and keep the value in p5. The first step of the process q (q1) will not execute until p5 runs. Thus, when q2 executes, out = 0 and in = 1, thus, **A** holds at this time.
* If the consumer goes first, it will be stuck at q1, since that notEmpty.v = 0. Then, similar to the first condition, q1 need to wait until p5, thus, when q2 executes, out = 0 and in = 1, thus, **A** holds at this time.

<u>After that</u> $\rightarrow$ in = 1, out = 1. The only condition that conflict to **A** is the consumer runs first and succeed. However, similar to the condition at the start, if the consumer goes first, it will be stuck at q1. From this, we can know that the conditions of the next few steps are similar. Thus, we can see that the consumer must execute after the producer.  

The producer can run multiple times before the buffer is full, but the consumer can only execute after the producer runs.

<u>When the buffer is full</u>, the producer has run a circle and meet the consumer again. Assumes that they meet at index 0 (here the index number will not have an effect on the result), at that time, (in modulo N) = 0 and notFull.v = 0 (the producer has run the whole buffer thus the notFull.v minus 1 for N times, and the consumer doesn't increase the notFull.v). Thus, the producer will be stuck at p2 until consumer calls signal(notFull). In this condition, the **A** still holds. Thus, the algorithm holds mutual exclusion.

**Deadlock Freedom:**

The condition that can produce deadlock is that the producer and the consumer are both stuck at wait (p2 and q1).

If the producer stuck at p2, it must have executed p5(at the start of the process, nutFull.v = N, p1 will not be stuck). If the p5 executed, the q will not be stuck at q1 (if q1 has stuck, the process status will be changed to ready, if q1 is not stuck at the time, it also will not be stuck). Once the producer can run, it will call signal(notEmpty), then the q2 will not be stuck. Thus, this algorithm is deadlock-free.

**Starvation Freedom:**

Once the producer succeeds to call wait(notFull), it can run through the whole process, then, the consumer will have chance to executes q1. Once the consumer runs q1, it can run through the whole process and finally call signal(notFull) which will make the producer has a chance to execute. Thus, the algorithm is starvation free.

#### (b)

<table>
    <tr>
    	<th colspan="3">Producer-consumer(deque)</th>
    </tr>
    <tr>
    	<td colspan="3">
            dataType array [0..N-1] buffer<br>
            integer in, out ← 0<br>
            semaphore notEmpty ← (0, ∅)<br>
            semaphore notFull ← (N , ∅)<br>
            binary semaphore S ← (1, ∅)
        </td>
    </tr>
    <tr>
    	<td>p</td>
        <td>q</td>
        <td>r</td>
    </tr>
    <tr>
    	<td>
            dataType d<br>
        	loop forever<br>
            p1:  d ← produce<br>
            p2:  wait(S)<br>
            p3:  wait(notFull)<br>
            p4:  buffer[in] ← d<br>
            p5:  in ← (in+1) modulo N<br>
            p6:  signal(notEmpty)<br>
            p7:  signal(S)
        </td>
        <td>
            dataType d<br>
        	loop forever<br>
            q1:  wait(notEmpty)<br>
            q2:  d ← buffer[out]<br>
            q3:  out ← (out+1) modulo N<br>
            q4:  signal(notFull)<br>
            q5:  consume(d)
        </td>
        <td>
            dataType d<br>
        	loop forever<br>
            r1:  wait(S)<br>
            r2:  wait(notEmpty)<br>
            r3:  in ← (in - 1 + N) modulo N<br>
            r4:  d ← buffer[in]<br>
            r5:  signal(notFull)<br>
            r6:  signal(S)<br>
            r7:  consume(d)
        </td>
    </tr>
</table>

To implement the algorithm, I add a binary semaphore S to make sure that p and r will not run together. The index of r is based on the index of p, if they can run at the same time, they might interfere each other which might produce the wrong index or make there are empty cell between the product's cells. There will not be problems between q and r, or between p and q. The condition of p and q is similar to the previous algorithm. The semaphore notEmpty can make sure q and r will not interfere with each other.

