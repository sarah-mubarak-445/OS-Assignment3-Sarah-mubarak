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

**Why they need protection**: 

**Synchronization mechanism used**: 

**Code snippet**:
```java
// Paste your implementation here
```

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

---

### Critical Section #3: CPU Semaphore

**Purpose of semaphore**: 

**Number of permits and why**: 

**Where implemented**: 

**Code snippet**:
```java
// Paste your implementation here
```

**Effect on program behavior**: 

---

## Part 4: Testing and Verification (2 marks)

### Test 1: Consistency Check
**What I tested**: Running program multiple times to verify consistent results

**Testing procedure**: 
```bash
# Commands used (run the program at least 5 times)
```

**Results**: 
(Show that running multiple times produces consistent, correct results)

**Why synchronization is necessary**: 
(Explain what race conditions COULD occur without synchronization, even if you didn't observe them. Explain which shared resources need protection and why.)

**Conclusion**: 

---

### Test 2: Exception Testing
**What I tested**: Checking for ConcurrentModificationException

**Testing procedure**: 

**Results**: 

**What this proves**: 

---

### Test 3: Correctness Verification
**What I tested**: Verifying correct final values (total burst time, context switches, etc.)

**Expected values**: 

**Actual values**: 

**Analysis**: 

---

### Test 4: Different Scenarios
**Scenario tested**: [e.g., different time quantum, more processes, etc.]

**Purpose**: 

**Results**: 

**What I learned**: 

---

## Part 5: Reflection and Learning

### What I learned about synchronization:

[6-8 sentences about key concepts, challenges, insights]

---

### Real-world applications:

Give TWO examples where synchronization is critical:

**Example 1**: 

**Example 2**: 

---

### How I would explain synchronization to others:

[Explain to someone who just finished Assignment 1 - use simple terms and analogies]

---

## Part 6: GitHub Repository Information

**Repository URL**: 

**Number of commits**: 

**Commit messages**: 
1. 
2. 
3. 
4. 

---

## Summary

**Total time spent on assignment**: 

**Key takeaways**: 
1. 
2. 
3. 

**Most challenging aspect**: 

**What I'm most proud of**: 

---

**End of Documentation**
