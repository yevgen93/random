# Create a clone of ‘cat /dev/random’

I was tasked with creating a clone of running the Linux command: `cat /dev/random`

My initial research led me to a few interesting places on the interwebs:

# LinuxFromScratch
## http://www.linuxfromscratch.org/hints/downloads/files/entropy.txt

I really enjoyed the explanation of entropy and a true randomness by Robert Connolly:

_"Generating true entropy in a computer is fairly difficult because nothing, outside of quantum physics, is random. The Linux kernel uses keyboard, mouse, network, and disc activities, with a cryptographic algorithm (SHA1), to generate data for the /dev/random device. One of the problems with this is that the input is not constant, so the kernel entropy pool can easily become empty. The /dev/random device is called a "blocking device". This means if the entropy pool is empty applications trying to use /dev/random will have to wait, indefinitely, until something refills the pool. This is both a feature and a nuisance, and can cause a denial of service depending on the application. Another problem with using the keyboard, mouse, network, and disc activity is that on idle, unmanned, and disc-less systems there is very little, or no, input of this kind. It is also theoretically possible for an observer (keyboard or network sniffer) to predict the entropy pool without having root level access. The only real solution to these vulnerabilities is in using a hardware-based random number generator. These hardware devices usually use electrical static as a source of entropy, because there is currently no technology that can reliably predict this. The best hardware random number generators use radioactive decay as an entropy source."_


# Haveged
## https://linux.die.net/man/8/haveged
## https://www.techrepublic.com/article/how-to-add-more-entropy-to-improve-cryptographic-randomness-on-linux/
- I learned that a common problem for headless machines (that can't rely on the mouse/keyboard to generate entropy) is that the entropy runs out. And since `/dev/random` is a blocking device, it will wait indefinitely until the entropy pool is refilled.
- The HAVEGE principle allows generating randomness based on variations in code execution time on a processor.
- In essence, Haveged is a great solution for boosting entropy and improving cryptographic randomness.


# My Pseudo-Solution(s)
## Quantum Random Numbers
- I have to admit that currently I do not have the engineering experience to deliver a true clone of `cat /dev/random_`.
- However, I did find a solid source of entropy; the ANU Quantum Random Numbers Server: **http://qrng.anu.edu.au**. "This website offers true random numbers to anyone on the internet. The random numbers are generated in real-time in our lab by measuring the quantum fluctuations of a vacuum."
- The ideal alternative solution to `cat /dev/random` would be to query their API as a entropy pool and dump the data to STDOUT.
- After doing some more digging, I found a Github project that does just that: **https://github.com/kuberkaul/randomclone**
- I also found a Python tool that interfaces with the same quantum entropy pool and provides the `qrandom` CLI: **https://pypi.org/project/quantumrandom/**

So running the following commands in Terminal would replicate true random entropy being dumped to the screen:
```
sudo pip install quantumrandom
qrandom --binary
```

## CSPRNG
I also learned the difference between (PRNG) Pseudo Random Number Generator and (CSPRNG) Cryptographically Secure Pseudo Random Number Generators. PRNGs are not truly random because they generate a pseudo-random sequence based on the initial seed. Thus, they should be used for statistical modeling, simulation, and to make random data reproducible. CSPRNGs, on the other hand, read random bytes from `/dev/random` and thus work best for security and cryptographic applications where data sensitivity is imperative. This led me to another naive solution: to use Python's os.urandom() module which doesn't rely on manual seeding.
```python
import os
print(os.urandom(100000000))
```
Saving this to rand.py and executing `python rand.py` would dump a large amount of random data (and unfortunately probably hang your Terminal session).
