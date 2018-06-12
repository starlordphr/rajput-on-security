---

layout: post
title: Meltdown and Spectre
tags: [Cyber Threat Watch]

---

Meltdown and Spectre were disclosed to the public on January 25, 2018 and shocked the cyber security research community. Both Meltdown and Spectre are microarchitectural attacks that exploit out-of-order execution, branch prediction in order to leak kernel or process space secret respectively.

Before we go into the details of this attack, lets brush up on some basics.

## Background

Modern processors use various speedup techniques for executing instructions at a much faster rate than before. For instance Intel i3, i5 and i7 series all have 14 stage Instruction Level Pipeline (ILP) which can be used to execute 14 independent micro-operations at once. Intel has also experimented with longer 20-24 ILPs in Nehlam, but decreased it moving further. On the other hand it also uses technologies such as Speculative / Out-of-order Execution, where instructions independent from the current instructions that are predicted to be used in the future are executed in parallel with the current instruction. This reduces the amount of delay the processor has to go through for obtaining intermediate results in the instructions that follow.

![Metldown](/assets/img/posts/CyberThreatWatch/Metldown.png)

### Out-of-order execution

On an Intel architecture, instruction is fetched from the memory and decoded to micro-operations. The *Reorder Buffer* is responsible for temporary register allocation, register renaming and retiring. These micro-operations are forwarded to the Unified Reservation Station that queues the micro-operations and sends it to the Execution Units.

The branch prediction technology in the processor makes an educated guess of the instructions that might be executed next. If the branch prediction module chooses an instruction for execution, it is executed and used immediately when needed by the upcoming instruction. In case the branch prediction module makes an incorrect guess, all the registers are cleared but the result still resides in the cache.

### Address Spaces

Address space in a process can be roughly divided into the process address space and the kernel address space. The protected code of the process resides in the kernal address space and this differentiation on modern processors is realized by a supervisor bit. Whenever an exception is generated, the running process sets the supervisor bit and moves into the kernal address space and unsets it when it comes out of it.

### Cache

Modern processors have three levels of caches: L1, L2 and L3 cache. L1 cache is the fastest and the smallest of the caches whereas L3 is the slowest and the largest of them. There have been proposed side channel attacks for caches which can be used to infer information stored in the cache for instance, Evict+Time, Prime+Probe and Flush+Reload. These side channel inference attacks have been ignored from the threat models of these processors, but maybe it's time to reconsider.

---
## Meltdown

Meltdown breaks the most fundamental isolation between user applications and the operating system. This attack allows a program to access the memory, and thus also the secrets, of other programs and the operating system \[[1]\].

Meltdown consists of two main components:
1. **Executing Transient Instruction:** An instruction executed out-of-order, leaving measurable side effects is a transient instruction and such instructions are executed by the processor all the time because CPU continously runs ahead of the instruction currently being executed. As stated earlier if this instruction is not actually executed, the results from this transient instruction are still fetched and stored in cache.

What if this instruction was fetching some value from the kernel address space? This might lead the processor to cache sensitive information from the kernel address space of a process. Take this simple code for example:

```
1. ; rcx = kernel address
2. ; rbx = probe array
3. retry:
4. mov al, byte [rcx]
5. shl rax, 0xc
6. jz retry
7. mov rbx, qword [rbx + rax]
```

Register *rcx* stores a kernel space address; so when instruction 4 is executed an exception is raised, but its possible that instruction 7 is already executed as a result of speculative execution and the content from the address pointed by *rbx+rax* is already present in the cache.

But, moving into the kernel address space will raise an exception because general processes are not allowed to access kernel address space. Meltdown uses a very smart approach for bypassing this problem where the main Meltdown process forks a child process that actually runs the transient instruction and receives the exception. Moreover, since we do not care about the child process after the exception, it can be killed.

2. **Building a covert channel:** Second step is to extract this information from the cache using already discussed cache side channel attacks. The main Meltdown process can be used to implement the Flush+Reload \[[2]\] attack for obtaining sensitive kernel space information from the cache. (We will talk about cache side channel attacks in a future post.)

Meltdown can achieve average reading rates of 503 KB/s with an error rate as low as 0.02%.

### Solutions
Meltdown is an attack that can be implemented in software to exploit the vulnerability in the processor architecture. The only real way to fix this is by changing the processor architecture and we are a long way from it.

Intel issued a patch on January 3, 2018 but this instead caused many computers to reboot frequently. It mainly affected their fourth-generation Haswell and fifth-generation Broadwell chips. Microsoft \[[3]\] then issued an emergency security update on 29 January, 2018 for disabling Intel's fix. So, yes we don't have an official fix for it.

But, operating systems like Windows and Ubuntu have implemented variants of KAISER \[[4]\] which randomizes the start address of kernel address space at every boot. This is not the permanent fix, but it will be helpful till we find a better solution.

A theoretical fix would be to disable out-of-order execution completely from the operating system level, provided there is a way to do so. But, I don't think this will be done because this would indulge a performance hit. Intel states that the current kaby lake models will show less than 5% performance loss, but Intel's 6th generation Core i7 6700K released in mid-2015 losses around 10% performance, with responsiveness dropping 31% on Windows 10 \[[5]\].

---
## Spectre

Spectre breaks the isolation between different applications. It allows an attacker to trick error-free programs, which follow best practices, into leaking their secrets.

At a high level, Spectre attacks trick the processor into speculatively executing instruction sequences that should not have executed during correct program execution. Once the processor realises that the branch predicted is incorrect, the results are discarded. Such instructions are called as transient instructions. By carefully choosing which transient instruction are speculatively executed, information can be leaked \[[6]\].

We will discuss about the branch prediction approach towards performing Spectre attack.

### Branch Prediction

The Branch Target Buffer (BTB) keeps a mapping between recently executed branch and the destination addresses. Processor can use the BTB to predict future code addresses even before the execution of the branch.

For conditional branches, the processor maintains a record of recent branch outcomes and uses this to predict the branch to be taken.

### Attack Overview
Spectre attack consists of three phases:
1. **Setup Phase:** Adversary mistrains the processor so that it can be later exploited for erroneous speculative prediction. Moreover, the adversary can also prepare the side channel for extracting victim's information by performing flush+reload.
2. **Execution Phase:** The processor speculatively executes instructions that transfer confidential information from the victim's context to microarchitectural side channel. The attacker uses this to obtain sensitive information from the same process.
3. **Final Phase:** Sensitive data is recovered using flush+reload cache side channel attack. This consists of timing how long reads take from memory addresses in the cache to precisely monitor the sensitive information in cache lines.

### Using Conditional Branch Misprediction

The code fragment mentioned below begins with a bounds check on x which is essential for security.
```
if (x < array1_size)
    y = array2[array1[x] * 256];
```
Unfortunately, during speculative execution, the conditional branch for the bounds check can follow an incorrect path. For example the adversary causes the code to run such that:
* the value of x is maliciously chosen such that array1[x] resolves to a secret byte k somewhere in the victim's memory.
* the sensitive information obtained as a result of transient instruction is removed but it is cached.
* previous operations with valid values of x for leading the branch predictor to assume *if* will likely be true.

---
## Conclusion
Both Meltdown and Spectre are attacks due to vulnerability in the processor architecture and they are independent of operating system. The main difference between them both is that while Meltdown exploits vulnerability in out-of-order execution, Spectre tricks the branch prediction module by mistraining it. Moreover, Meltdown can be used to access kernel address space whereas, Spectre is designed to leak secrets from the victim process.

---
## References
\[1\] Lipp M., Schwarz M., Gruss D., Prescher T., Haas W., Mangard S., Kocher P., Genkin D., Yarom Y., and Hamburg M., "Meltdown".<br />
\[2\] Yarom. Y, and Falkner K., "FLUSH+RELOAD: a High Resolution, Low Noise, L3 Cache Side-Channel Attack".<br />
\[3\] ZDNet, "[Microsoft: Emergency Patch](http://www.zdnet.com/article/windows-emergency-patch-microsofts-new-update-kills-off-intels-spectre-fix/)". \[Accessed: 14 April, 2018\]<br />
\[4\] Gruss D., Lipp M., Schwarz M., Fellner R., Maurice C., and Mangard S., "KASLR is Dead: Long Live KASLR".<br />
\[5\] Tech Crunch, "[Meltdown Performance Hit](https://techcrunch.com/2018/01/11/intel-details-performance-hit-for-meltdown-fix-on-affected-processors/)". \[Accessed: 14 April, 2018\]<br />
\[6\] Kocher P., Genkin D., Gruss D., Haas W., Hamburg M., Lipp M., Mangard S., Prescher T., Schwarz M., and Yarom Y., "Spectre Attacks: Exploiting Speculative Execution".<br />

[1]: https://meltdownattack.com/meltdown.pdf "Meltdown"
[2]: https://eprint.iacr.org/2013/448.pdf "Flush+Reload"
[3]: http://www.zdnet.com/article/windows-emergency-patch-microsofts-new-update-kills-off-intels-spectre-fix/ "Microsoft: Emergency Patch"
[4]: https://gruss.cc/files/kaiser.pdf "KAISER"
[5]: https://techcrunch.com/2018/01/11/intel-details-performance-hit-for-meltdown-fix-on-affected-processors/ "Meltdown Performance Hit"
[6]: https://spectreattack.com/spectre.pdf "Spectre"

---
