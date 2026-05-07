# Assignment 3 - Complete Documentation

**Student Name**: [Your Full Name]  
**Student ID**: [Your ID]  
**Date Submitted**: [Submission Date]

---

## 🎥 VIDEO DEMONSTRATION LINK (REQUIRED)

> **⚠️ IMPORTANT: This section is REQUIRED for grading!**
> 
> Upload your 3-5 minute video to your **PERSONAL Gmail Google Drive** (NOT university email).
> Set sharing to "Anyone with the link can view".
> Test the link in incognito/private mode before submitting.

**Video Link**: [Paste your personal Gmail Google Drive link here]

**Video filename**: `[YourStudentID]_Assignment3_Synchronization.mp4`

**Verification**:
- [ ] Link is accessible (tested in incognito mode)
- [ ] Video is 3-5 minutes long
- [ ] Video shows code walkthrough and commits
- [ ] Video has clear audio
- [ ] Uploaded to PERSONAL Gmail (not @std.psau.edu.sa)

---

## Part 1: Development Log (1 mark)

Document your development process with **minimum 3 entries** showing progression:

### Entry 1 - May 6, 2026, 6:30 PM

What I implemented: 

I reviewed the original scheduler simulation code and identified the shared resources that needed synchronization. These included contextSwitchCount, completedProcessCount, totalWaitingTime, and executionLog.

Challenges encountered: 

The main challenge was understanding which parts of the code could cause race conditions when multiple threads access them.

How I solved it: 

I marked the shared counters and the shared ArrayList as critical sections that need protection.

Testing approach: 

I checked where each shared variable was updated in the code and planned which synchronization mechanism should protect it.

**Time spent: 30 min


### Entry 2 - May 6, 2026, 7:00 PM

What I implemented: 

I added ReentrantLock objects to protect the shared counter variables. I used contextSwitchLock for contextSwitchCount, completedProcessLock for completedProcessCount, and waitingTimeLock for totalWaitingTime.

Challenges encountered: 

I had to decide whether to use one lock for all counters or separate locks for each counter.

How I solved it: 

I used separate locks because the counters are independent from each other. This provides better concurrency than using one lock for all counters.

Testing approach: 

I checked that each counter update was placed inside a lock() and unlock() block using try-finally.

Time spent: 

40 minutes

---

### Entry 3 - May 7, 2026, 7:45 PM

What I implemented: 

I added a ReentrantLock called logLock to protect the executionLog ArrayList. I modified the logExecution method so that adding messages to the log happens inside a protected critical section.

Challenges encountered: 

The challenge was remembering that ArrayList is not thread-safe and can cause problems if multiple threads modify it at the same time.

How I solved it: 

I used logLock.lock() before adding a message to executionLog and logLock.unlock() inside a finally block.

Testing approach: 

I ran the program and verified that the program completed without ConcurrentModificationException or log-related errors.

Time spent: 

35 minutes
---

### Entry 4 - [Date, Time]
**What I implemented**: 

**Challenges encountered**: 

**How I solved it**: 

**Testing approach**: 

**Time spent**: 

---

### Entry 5 - [Date, Time]
**What I implemented**: 

**Challenges encountered**: 

**How I solved it**: 

**Testing approach**: 

**Time spent**: 

---

## Part 2: Technical Questions (1 mark)

### Question 1: Race Conditions
**Q**: Identify and explain TWO race conditions in the original code. For each:
- What shared resource is affected?
- Why is concurrent access a problem?
- What incorrect behavior could occur?

**Your Answer**:

Two race conditions in the original code were related to the shared counters and the execution log. The first race condition affects the shared counter variables contextSwitchCount, completedProcessCount, and totalWaitingTime. Concurrent access is a problem because multiple process threads may read and update the same variable at the same time, so one update could overwrite another update. For example, if two threads execute contextSwitchCount++ at the same time, both may read the same old value and only one increment will be saved. The second race condition affects executionLog, which is an ArrayList. Since ArrayList is not thread-safe, multiple threads adding log messages at the same time could cause missing log entries or inconsistent data.
### Question 2: Locks vs Semaphores
**Q**: Explain the difference between ReentrantLock and Semaphore. Where did you use each in your code and why?
ReentrantLock is used for mutual exclusion, meaning it protects a critical section so only one thread can access a shared resource at a time. Semaphore is used to control how many threads can access a limited resource by using permits. In my code, I used ReentrantLock to protect the shared counters: contextSwitchCount, completedProcessCount, and totalWaitingTime. I also used another ReentrantLock called logLock to protect the shared executionLog list. I used Semaphore for CPU access by creating cpuSemaphore = new Semaphore(1). Since the semaphore has only one permit, it works like a binary semaphore and allows only one process to enter the CPU execution section at a time.

---

### Question 3: Deadlock Prevention
**Q**: What is deadlock? Explain TWO prevention techniques and what you did to prevent deadlocks in your code.

**Your Answer**:

Deadlock happens when two or more threads wait for each other forever, so none of them can continue execution. One prevention technique is to always release locks and semaphores inside a finally block. In my code, every ReentrantLock is unlocked inside finally, such as contextSwitchLock.unlock() and logLock.unlock(). I also release the CPU semaphore inside a finally block using SharedResources.cpuSemaphore.release(), so the CPU permit is returned even if an error happens. Another prevention technique is to avoid holding multiple locks at the same time when it is not necessary. My code uses small critical sections and separate methods for each shared resource, which reduces the chance of deadlock.

---

### Question 4: Lock Granularity Design Decision 
**Q**: For Task 1 (protecting the three counters), explain your lock design choice:
- Did you use ONE lock for all three counters (coarse-grained) OR separate locks for each counter (fine-grained)?
- Explain WHY you made this choice
- What are the trade-offs between the two approaches?
- Given that the three counters are independent, which approach provides better concurrency and why?

**Your Answer**:
contextSwitchLock for contextSwitchCount, completedProcessLock for completedProcessCount, and waitingTimeLock for totalWaitingTime. I chose this design because the three counters are independent and do not depend on each other. The advantage of fine-grained locking is that it allows better concurrency because different threads can update different counters without blocking each other unnecessarily. The disadvantage is that it adds more locks, so the code is slightly more complex. A coarse-grained approach would use one lock for all three counters, which is simpler but can reduce performance because every counter update would wait for the same lock. Since the counters are independent, fine-grained locking provides better concurrency and is the better choice for this program.

---

## Part 3: Synchronization Analysis (1 mark)

### Critical Section #1: Counter Variables

**Which variables**: 
contextSwitchCount, completedProcessCount, and totalWaitingTime
**Why they need protection**: 

These variables are shared between process threads. If multiple threads update them at the same time, race conditions can happen. For example, two threads may read the same value before either one writes the updated value, which can cause a lost update and incorrect final statistics.
**Synchronization mechanism used**: 
ReentrantLock.
**Code snippet**:
```java
public static final ReentrantLock contextSwitchLock = new ReentrantLock();
public static final ReentrantLock completedProcessLock = new ReentrantLock();
public static final ReentrantLock waitingTimeLock = new ReentrantLock();

public static int contextSwitchCount = 0;
public static int completedProcessCount = 0;
public static long totalWaitingTime = 0;

public static void incrementContextSwitch() {
    contextSwitchLock.lock();
    try {
        contextSwitchCount++;
    } finally {
        contextSwitchLock.unlock();
    }
}

public static void incrementCompletedProcess() {
    completedProcessLock.lock();
    try {
        completedProcessCount++;
    } finally {
        completedProcessLock.unlock();
    }
}

public static void addWaitingTime(long time) {
    waitingTimeLock.lock();
    try {
        totalWaitingTime += time;
    } finally {
        waitingTimeLock.unlock();
    }
}

**Justification**: 

---

### Critical Section #2: Execution Log

**What resource**: 

**Why it needs protection**: 

**Synchronization mechanism used**: 

**Code snippet**:
```java
// Paste your implementation here
```

**Justification**: 

I used separate locks for the three counters because they are independent from each other. This prevents race conditions while still allowing better concurrency than using one lock for all counters. The finally block guarantees that each lock is released after the update.
---

### Critical Section #3: CPU Semaphore

**Purpose of semaphore**: 
The semaphore controls access to the simulated CPU execution section.
**Number of permits and why**: 
The semaphore has one permit:
new Semaphore(1)

execuThis means only one process can enter the CPU section at a time. This matches the idea of a single CPU resource in the simulation.

tionLog is an ArrayList, and ArrayList is not thread-safe. If multiple process threads try to add log messages at the same time, the list could become inconsistent or lose some log entries.
**Where implemented**: 
The semaphore is declared in SharedResources and used in the run() method and the runToCompletion() method.
**Code snippet**:
```java
// Paste your implementation here
```public static final Semaphore cpuSemaphore = new Semaphore(1);
public void runToCompletion() {
    try {
        SharedResources.cpuSemaphore.acquire();
        try {
            // last process runs until completion

            Thread.sleep(remainingTime);
            remainingTime = 0;
            completionTime = System.currentTimeMillis();

            long waitingTime = (completionTime - creationTime) - burstTime;
            SharedResources.addWaitingTime(waitingTime);
            SharedResources.incrementCompletedProcess();

        } finally {
            SharedResources.cpuSemaphore.release();
        }
    } catch (InterruptedException e) {
        System.out.println(Colors.RED + "  ✗ " + name + " was interrupted." + Colors.RESET);
    }
}

**Effect on program behavior**: 

---

## Part 4: Testing and Verification (2 marks)

### Test 1: Consistency Check

**What I tested**: Running program multiple times to verify consistent results

**Testing procedure**: 
```bash
mvn clean compile exec:java
mvn clean compile exec:java
mvn clean compile exec:java
mvn clean compile exec:java
mvn clean compile exec:java
```

**Results**: 
(Show that running multiple times produces consistent, correct results)

I ran the program multiple times using the same student ID, 445052140. The program consistently generated 16 processes with a time quantum of 4000ms. In the final output, all processes completed successfully.
**Why synchronization is necessary**: 
(Explain what race conditions COULD occur without synchronization, even if you didn't observe them. Explain which shared resources need protection and why.)

**Conclusion**: 

The program ran successfully and produced correct final statistics. The completed process count matched the total number of processes, and the program finished without synchronization errors. This shows that the ReentrantLock objects and the Semaphore helped protect shared resources and control CPU access correctly.
---

### Test 2: Exception Testing

**What I tested**: Checking whether the program runs without thread-related exceptions, especially errors related to the shared `executionLog`.

**Testing procedure**: 
```bash
mvn clean compile exec:java
mvn clean compile exec:java
mvn clean compile exec:java
mvn clean compile exec:java
mvn clean compile exec:java


What this proves:
This proves that the shared executionLog was protected correctly using logLock. It also shows that the locks and semaphore were released correctly using finally blocks, so the program did not get stuck or crash during execution. 

---
Test 3: Correctness Verification
What I tested: Verifying correct final values, including completed processes, context switches, waiting time, and average waiting time.

Expected values:
The total completed processes should equal the number of generated processes. Since the output showed that the program generated 16 processes, the expected completed process count is 16. The context switch count should increase each time a process gets CPU time. The waiting time and average waiting time should be calculated and printed at the end.

Analysis:
The actual completed process count is 16, which matches the number of generated processes. This means all processes finished successfully. The total context switches value is 37, which shows that many processes needed more than one CPU quantum because their burst time was larger than the time quantum. The waiting time and average waiting time were printed correctly in the final statistics, which confirms that totalWaitingTime was updated through the synchronized method.
---

### Test 4: Different Scenarios
Scenario tested: Running the program with multiple processes generated from student ID 445052140.

Purpose:
The purpose was to test the scheduler with different burst times, different priorities, and multiple rounds of CPU execution. This checks whether the synchronization still works when processes have different execution lengths.

Results:
The program generated 16 processes with a time quantum of 4000ms. The processes had different burst times and priorities. Some processes finished in one quantum, while others yielded the CPU and were added back to the ready queue. At the end, all 16 processes completed successfully.

What I learned:
I learned that synchronization is important when multiple threads share counters, logs, and CPU access. The ReentrantLock objects protected the shared statistics and execution log. The Semaphore controlled CPU access and allowed only one process to execute in the CPU section at a time. This made the simulation safer and prevented race conditions.

---

## Part 5: Reflection and Learning

### What I learned about synchronization:

I learned that synchronization is important when multiple threads access the same shared resources. Without synchronization, race conditions can happen and the final values may become incorrect. I learned that `ReentrantLock` can be used to protect critical sections such as shared counters and shared lists. I also learned that `Semaphore` can be used to control access to a limited resource, such as the simulated CPU in my program. One important concept I learned is that locks and semaphores should be released inside a `finally` block to prevent deadlocks. I also learned the difference between fine-grained locking and coarse-grained locking. Fine-grained locking gives better concurrency when the shared resources are independent. This assignment helped me understand how thread safety is used in real programs.

---

### Real-world applications:

**Example 1**: Banking systems need synchronization when multiple transactions update the same account balance at the same time. Without synchronization, two withdrawals or deposits could happen incorrectly and the final balance may be wrong.

**Example 2**: Online booking systems need synchronization when many users try to reserve the same seat, hotel room, or appointment slot. Synchronization prevents two users from booking the same resource at the same time.

---

### How I would explain synchronization to others:

Synchronization is like using a key for a shared room. If many people want to enter the room and change something inside, only one person should enter at a time so nothing gets mixed up. In Java, a lock works like that key. A thread locks the resource, finishes its work, and then unlocks it so another thread can use it. A semaphore is similar, but it controls how many threads are allowed to enter at once. In my program, the CPU semaphore has only one permit, so only one process can use the CPU section at a time.

## Part 6: GitHub Repository Information

**Repository URL**: 

**Number of commits**: 
17
**Commit messages**: 
1. update mu  studant ID

2. task3: add semaphore to control concurrent cpu
3. Task2: protect logExecution method
4. task1: Add ReentrantLock


---

## Summary

**Total time spent on assignment**:  Approximately 5 hours.
**Key takeaways**:
 1. I learned how to protect shared variables using `ReentrantLock` to prevent race conditions.
 2. I learned how to use `Semaphore` to control access to a limited resource such as the CPU.
 3. I learned that using `try-finally` is important to make sure locks and semaphores are always released.
**Most challenging aspect**:  The most challenging aspect was understanding where the critical sections were and deciding which shared resources needed synchronization.
 It was also challenging to fix errors related to the main class, package name, and running the project in Maven/NetBeans.
**What I'm most proud of**:  I am most proud of completing the synchronization tasks successfully and getting the program to run until all processes completed. The final output showed that all 16 processes finished, and the synchronization statistics were printed correctly.
---

**End of Documentation**
